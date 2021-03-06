---
title: Metod tips för att hantera identitet
titleSuffix: Azure Kubernetes Service
description: Lär dig metod tips för kluster operatörer för hur du hanterar autentisering och auktorisering för kluster i Azure Kubernetes service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: jpalma
author: palma21
ms.openlocfilehash: 0e11f345bfed287be3170df38a909ed24149b754
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "88010267"
---
# <a name="best-practices-for-authentication-and-authorization-in-azure-kubernetes-service-aks"></a>Metod tips för autentisering och auktorisering i Azure Kubernetes service (AKS)

När du distribuerar och underhåller kluster i Azure Kubernetes service (AKS) måste du implementera sätt att hantera åtkomst till resurser och tjänster. Utan de här kontrollerna kan konton ha åtkomst till resurser och tjänster som de inte behöver. Det kan också vara svårt att spåra vilken uppsättning autentiseringsuppgifter som användes för att göra ändringar.

Den här tips artikeln fokuserar på hur en kluster operatör kan hantera åtkomst och identitet för AKS-kluster. I den här artikeln kan du se hur du:

> [!div class="checklist"]
>
> * Autentisera AKS-kluster användare med Azure Active Directory
> * Kontrol lera åtkomst till resurser med hjälp av rollbaserad åtkomst kontroll (RBAC) för Kubernetes
> * Använd Azure RBAC för att styra åtkomsten till AKS-resursen och Kubernetes-API i skala, samt till kubeconfig.
> * Använd en hanterad identitet för att autentisera poddar med andra tjänster

## <a name="use-azure-active-directory"></a>Använda Azure Active Directory

**Vägledning för bästa praxis** – distribuera AKS-kluster med Azure AD-integrering. Med Azure AD centraliseras identitets hanterings komponenten. Ändringar i användar kontots eller gruppens status uppdateras automatiskt i åtkomst till AKS-klustret. Använd roller eller ClusterRoles och bindningar, enligt beskrivningen i nästa avsnitt, för att begränsa användare eller grupper till minst den mängd behörigheter som krävs.

Utvecklare och program ägare av ditt Kubernetes-kluster behöver åtkomst till olika resurser. Kubernetes tillhandahåller inte en identitets hanterings lösning som styr vilka användare som kan interagera med vilka resurser. I stället integrerar du normalt klustret med en befintlig identitets lösning. Azure Active Directory (AD) tillhandahåller en företags klar identitets hanterings lösning som kan integreras med AKS-kluster.

Med Azure AD-integrerade kluster i AKS skapar du *roller* eller *ClusterRoles* som definierar åtkomst behörigheter till resurser. Du kan sedan *binda* rollerna till användare eller grupper från Azure AD. Dessa Kubernetes-rollbaserade åtkomst kontroll (RBAC) beskrivs i nästa avsnitt. Integreringen av Azure AD och hur du styr åtkomsten till resurser kan visas i följande diagram:

![Autentisering på kluster nivå för Azure Active Directory integrering med AKS](media/operator-best-practices-identity/cluster-level-authentication-flow.png)

1. Utvecklare autentiseras med Azure AD.
1. Utfärdande slut punkten för Azure AD-token utfärdar åtkomst-token.
1. Utvecklaren utför en åtgärd med Azure AD-token, till exempel `kubectl create pod`
1. Kubernetes validerar token med Azure Active Directory och hämtar utvecklarens grupp medlemskap.
1. Kubernetes rollbaserad åtkomst kontroll (RBAC) och kluster principer tillämpas.
1. Utvecklarens begäran har lyckats eller är inte baserad på tidigare validering av Azure AD-gruppmedlemskap och Kubernetes RBAC och principer.

Om du vill skapa ett AKS-kluster som använder Azure AD kan du läsa [integrera Azure Active Directory med AKS][aks-aad].

## <a name="use-kubernetes-role-based-access-control-rbac"></a>Använda Kubernetes-rollbaserad åtkomst kontroll (RBAC)

**Vägledning för bästa praxis** – Använd Kubernetes RBAC för att definiera de behörigheter som användare eller grupper måste ha till gång till i klustret. Skapa roller och bindningar som tilldelar minst den mängd behörigheter som krävs. Integrera med Azure AD så att alla ändringar i användar status eller grupp medlemskap uppdateras automatiskt och åtkomst till kluster resurser är aktuell.

I Kubernetes kan du ge detaljerad kontroll över åtkomsten till resurser i klustret. Behörigheter definieras på kluster nivå eller till vissa namn områden. Du kan definiera vilka resurser som kan hanteras och med vilka behörigheter. Rollerna tillämpas sedan på användare eller grupper med en bindning. Mer information om *roller*, *ClusterRoles*och *bindningar*finns i åtkomst- [och identitets alternativ för Azure Kubernetes service (AKS)][aks-concepts-identity].

Som exempel kan du skapa en roll som ger fullständig åtkomst till resurser i namn området med namnet *ekonomi-app*, som du ser i följande exempel yaml manifest:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role
  namespace: finance-app
rules:
- apiGroups: [""]
  resources: ["*"]
  verbs: ["*"]
```

En RoleBinding skapas då som binder Azure AD User *developer1- \@ contoso.com* till RoleBinding, som du ser i följande yaml-manifest:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: finance-app-full-access-role-binding
  namespace: finance-app
subjects:
- kind: User
  name: developer1@contoso.com
  apiGroup: rbac.authorization.k8s.io
roleRef:
  kind: Role
  name: finance-app-full-access-role
  apiGroup: rbac.authorization.k8s.io
```

När *developer1 \@ contoso.com* autentiseras mot AKS-klustret har de fullständig behörighet till resurser i namn området *ekonomi-app* . På så sätt kan du logiskt separera och kontrol lera åtkomsten till resurser. Kubernetes RBAC bör användas tillsammans med Azure AD-integration, enligt beskrivningen i föregående avsnitt.

Om du vill se hur du använder Azure AD-grupper för att kontrol lera åtkomsten till Kubernetes-resurser med RBAC, se [kontrol lera åtkomst till kluster resurser med hjälp av rollbaserad åtkomst kontroll och Azure Active Directory identiteter i AKS][azure-ad-rbac].

## <a name="use-azure-rbac"></a>Använda Azure RBAC 
**Vägledning för bästa praxis** – Använd Azure RBAC för att definiera minimi kravet på nödvändiga behörigheter som användare eller grupper måste AKS resurser i en eller flera prenumerationer.

Det finns två åtkomst nivåer som krävs för att fullständigt kunna använda ett AKS-kluster: 
1. Få åtkomst till AKS-resursen på din Azure-prenumeration. Med den här åtkomst nivån kan du styra sakernas skalning eller uppgradera klustret med hjälp av AKS-API: er samt hämta din kubeconfig.
Information om hur du styr åtkomsten till AKS-resursen och kubeconfig finns i [begränsa åtkomsten till kluster konfigurations filen](control-kubeconfig-access.md).

2. Åtkomst till Kubernetes-API: et. Den här åtkomst nivån styrs antingen av [KUBERNETES RBAC](#use-kubernetes-role-based-access-control-rbac) (traditionellt) eller genom att integrera Azure RBAC med AKS för Kubernetes-auktorisering.
Om du vill se hur detaljerad information ska ges till Kubernetes-API: et med Azure RBAC ser du [använda Azure RBAC för Kubernetes-auktorisering](manage-azure-rbac.md).

## <a name="use-pod-identities"></a>Använda Pod identiteter

**Vägledning för bästa praxis** – Använd inte fasta autentiseringsuppgifter i poddar eller behållar avbildningar eftersom de är utsatta för exponering eller missbruk. Använd i stället Pod-identiteter för att begära åtkomst automatiskt med hjälp av en central Azure AD-identitets lösning. Pod-identiteter är endast avsedda att användas med Linux-poddar och behållar avbildningar.

När poddar behöver åtkomst till andra Azure-tjänster, till exempel Cosmos DB, Key Vault eller Blob Storage, behöver Pod åtkomst-autentiseringsuppgifter. Dessa autentiseringsuppgifter för åtkomst kan definieras med behållar avbildningen eller matas in som en Kubernetes hemlighet, men måste skapas och tilldelas manuellt. Ofta återanvänds autentiseringsuppgifterna i poddar och roteras inte regelbundet.

Hanterade identiteter för Azure-resurser (som för närvarande implementeras som ett associerat AKS-projekt med öppen källkod) gör att du automatiskt kan begära åtkomst till tjänster via Azure AD. Du definierar inte autentiseringsuppgifter manuellt för poddar, i stället för att begära en åtkomsttoken i real tid och kan använda den för att få åtkomst till sina tilldelade tjänster. I AKS distribueras två komponenter av kluster operatören för att tillåta poddar att använda hanterade identiteter:

* **NMI-servern (Node Management Identity)** är en pod som körs som en DaemonSet på varje nod i AKS-klustret. NMI-servern lyssnar efter Pod-förfrågningar till Azure-tjänster.
* **Den hanterade identitets styrenheten (MIC)** är en central Pod med behörighet att fråga Kubernetes API-servern och söka efter en Azure Identity-mappning som motsvarar en pod.

När poddar begär åtkomst till en Azure-tjänst omdirigerar nätverks reglerna trafiken till NMI-servern (Node Management Identity). NMI-servern identifierar poddar som begär åtkomst till Azure-tjänster baserat på deras Fjärradress och skickar en fråga till den hanterade identitets styrenheten (MIC). MIC söker efter Azure Identity-mappningar i AKS-klustret och NMI-servern begär sedan en åtkomsttoken från Azure Active Directory (AD) baserat på pod identitets mappning. Azure AD ger åtkomst till NMI-servern, som returneras till pod. Denna åtkomsttoken kan användas av Pod för att sedan begära åtkomst till tjänster i Azure.

I följande exempel skapar en utvecklare en pod som använder en hanterad identitet för att begära åtkomst till Azure SQL Database:

![Pod-identiteter låter en POD automatiskt begära åtkomst till andra tjänster](media/operator-best-practices-identity/pod-identities.png)

1. Kluster operatör skapar först ett tjänst konto som kan användas för att mappa identiteter när poddar begär åtkomst till tjänster.
1. NMI-servern och MIC distribueras för att vidarebefordra eventuella Pod-förfrågningar om åtkomsttoken till Azure AD.
1. En utvecklare distribuerar en POD med en hanterad identitet som begär en åtkomsttoken via NMI-servern.
1. Token returneras till Pod och används för att komma åt Azure SQL Database

> [!NOTE]
> Hanterade Pod-identiteter är ett projekt med öppen källkod och stöds inte av teknisk support för Azure.

Information om hur du använder Pod-identiteter finns i [Azure Active Directory identiteter för Kubernetes-program][aad-pod-identity].

## <a name="next-steps"></a>Nästa steg

Den här tips artikeln fokuserar på autentisering och auktorisering för ditt kluster och dina resurser. Information om hur du implementerar några av dessa metod tips finns i följande artiklar:

* [Integrera Azure Active Directory med AKS][aks-aad]
* [Använda hanterade identiteter för Azure-resurser med AKS][aad-pod-identity]

Mer information om kluster åtgärder i AKS finns i följande metod tips:

* [Flera innehavare och isolering av kluster][aks-best-practices-cluster-isolation]
* [Basic Kubernetes Scheduler-funktioner][aks-best-practices-scheduler]
* [Avancerade funktioner i Kubernetes Scheduler][aks-best-practices-advanced-scheduler]

<!-- EXTERNAL LINKS -->
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-aad]: azure-ad-integration-cli.md
[managed-identities:]: ../active-directory/managed-identities-azure-resources/overview.md
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[azure-ad-rbac]: azure-ad-rbac.md
