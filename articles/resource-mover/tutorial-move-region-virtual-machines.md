---
title: Flytta virtuella Azure-datorer mellan regioner med Azure Resource-arbetsstation
description: Lär dig hur du flyttar virtuella Azure-datorer till en annan region med Azure Resource-arbetskraften
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 3a5489241aa15ce105dbe4d89086aff00373ca55
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "90603976"
---
# <a name="tutorial-move-azure-vms-across-regions"></a>Självstudie: flytta virtuella Azure-datorer över regioner

I den här artikeln lär du dig hur du flyttar virtuella Azure-datorer och relaterade nätverks-och lagrings resurser till en annan Azure-region med hjälp av [Azure Resource](overview.md)överbelastning.

> [!NOTE]
> Azure Resource-arbetskraft är för närvarande en offentlig för hands version.


I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Kontrol lera krav och krav.
> * Välj de resurser som du vill flytta.
> * Lös resurs beroenden.
> * Förbered och flytta käll resurs gruppen. 
> * Förbered och flytta de andra resurserna.
> * Bestäm om du vill ta bort eller bekräfta flytten. 
> * Du kan också ta bort resurser i käll regionen efter flytten.

> [!NOTE]
> Självstudier visar den snabbaste sökvägen för att testa ett scenario och använda standard alternativ. 

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) innan du börjar. Logga sedan in på [Azure Portal](https://portal.azure.com).

## <a name="prerequisites"></a>Krav

-  Kontrol lera att du har *ägar* åtkomst till prenumerationen som innehåller de resurser som du vill flytta.
    - Första gången du lägger till en resurs för ett visst käll-och mål par i en Azure-prenumeration skapar resurs förflyttningen en [systemtilldelad hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) (tidigare känd som hanterad tjänst identifiering (MSI)) som är betrodd av prenumerationen.
    - Om du vill skapa identiteten och tilldela den rollen som krävs (deltagare eller administratör för användar åtkomst i käll prenumerationen) måste kontot som du använder för att lägga till resurser ha *ägar* behörigheter för prenumerationen. [Lär dig mer](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) om Azure-roller.
- Prenumerationen måste ha tillräckligt med kvot för att skapa de resurser som du flyttar i mål regionen. Om den inte har någon kvot [begär du ytterligare begränsningar](/azure/azure-resource-manager/management/azure-subscription-service-limits).
- Kontrol lera priser och avgifter som är kopplade till den mål region som du flyttar virtuella datorer till. Använd [pris kalkylatorn](https://azure.microsoft.com/pricing/calculator/) för att hjälpa dig.
    

## <a name="check-vm-requirements"></a>Kontrol lera krav för virtuella datorer

1. Kontrol lera att de virtuella datorer som du vill flytta stöds.

    - [Verifiera](support-matrix-move-region-azure-vm.md#windows-vm-support) stödda virtuella Windows-datorer.
    - [Kontrol lera](support-matrix-move-region-azure-vm.md#linux-vm-support) virtuella Linux-datorer och kernel-versioner som stöds.
    - Kontrol lera inställningar för [beräkning](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [lagring](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings)och [nätverk](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings) som stöds.
2. Kontrol lera att de virtuella datorer som du vill flytta är aktiverade.
3. Kontrol lera att de virtuella datorerna har de senaste betrodda rot certifikaten och en uppdaterad lista över återkallade certifikat (CRL). Gör så här:
    - På virtuella Windows-datorer installerar du de senaste Windows-uppdateringarna.
    - På virtuella Linux-datorer följer du vägledningen för distributör så att datorerna har de senaste certifikaten och CRL: en. 
4. Tillåt utgående anslutning från virtuella datorer:
    - Om du använder en URL-baserad brand Väggs-proxy för att kontrol lera utgående anslutning ger du åtkomst till dessa [URL: er](support-matrix-move-region-azure-vm.md#url-access)
    - Om du använder regler för nätverks säkerhets grupper (NSG) för att kontrol lera utgående anslutningar skapar du dessa [service tag-regler](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-resources"></a>Välj resurser 

Välj de resurser som du vill flytta.

- Alla resurs typer som stöds i resurs grupper i den valda käll regionen visas.
- Resurser som redan har lagts till för flyttning mellan regioner visas inte.
- Du flyttar resurser till en mål region i samma prenumeration som käll regionen. Om du vill ändra prenumerationen kan du göra det när resurserna har flyttats.

1. Sök efter *resurs förflyttning*i Azure Portal. Under **tjänster**väljer du sedan **Azure Resource förflyttning**.

    ![Sök Resultat för resurs förflyttning i Azure Portal](./media/tutorial-move-region-virtual-machines/search.png)

2. I **Översikt**klickar du på **Kom igång**.

    ![För att lägga till resurser som ska flyttas till en annan region](./media/tutorial-move-region-virtual-machines/get-started.png)

3. I **Flytta resurser**  >  **källa + mål**väljer du käll prenumeration och region.
4. I **mål**väljer du den region som du vill flytta de virtuella datorerna till. Klicka på **Nästa**.

    ![Sidan för att välja käll-och mål region](./media/tutorial-move-region-virtual-machines/source-target.png)

6. I **resurser att flytta klickar du**på **Välj resurser**.
7. I **Välj resurser**väljer du den virtuella datorn. Du kan bara lägga till [resurser som stöds för flytt](#check-vm-requirements). Klicka sedan på **färdig**.

    ![Sidan för att välja de virtuella datorer som ska flyttas](./media/tutorial-move-region-virtual-machines/select-vm.png)

8.  I **resurser att flytta klickar du**på **Nästa**.
9. Kontrol lera käll-och mål inställningarna i **Granska + Lägg till**. 

    ![Sida för att granska inställningar och fortsätta med flytten](./media/tutorial-move-region-virtual-machines/review.png)
10. Klicka på **Fortsätt**för att börja lägga till resurserna.
11. När processen har slutförts klickar du på Lägg **till resurser för flytta** i meddelande ikonen.
12. När du har klickat på meddelandet granskar du resurserna på sidan **över flera regioner** .

> [!NOTE]
> - De resurser som har lagts till är i *vänte* läge.
> - Om du vill ta bort en resurs från en flyttnings samling är metoden för att göra det beroende av var du befinner dig i flyttnings processen. [Läs mer](remove-move-resources.md).

## <a name="resolve-dependencies"></a>Matcha beroenden

1. Om resurser visar ett *verifierings beroende* meddelande i kolumnen **problem** klickar du på knappen **Verifiera beroenden** . Verifierings processen börjar.
2. Om det finns beroenden klickar du på **Lägg till beroenden**. 
3. I **Lägg till beroenden**väljer du de beroende resurserna > **lägga till beroenden**. Övervaka förloppet i aviseringarna.

    ![Lägg till beroenden](./media/tutorial-move-region-virtual-machines/add-dependencies.png)

4. Lägg till ytterligare beroenden om det behövs och verifiera beroenden igen. 
    ![Sida för att lägga till ytterligare beroenden](./media/tutorial-move-region-virtual-machines/add-additional-dependencies.png)

4. På sidan **över regioner** kontrollerar du att resurserna nu är i ett *förberedelse* tillstånd utan problem.

    ![Sida som visar resurser i förberedelse av väntande tillstånd](./media/tutorial-move-region-virtual-machines/prepare-pending.png)

> [!NOTE]
> Om du vill redigera mål inställningarna innan du påbörjar flyttningen väljer du länken i kolumnen **mål konfiguration** för resursen och redigerar inställningarna. Om du redigerar inställningarna för den virtuella mål datorn bör storleken på den virtuella mål datorn inte vara mindre än storleken på den virtuella käll datorn.  

## <a name="move-the-source-resource-group"></a>Flytta käll resurs gruppen 

Innan du kan förbereda och flytta virtuella datorer måste resurs gruppen för den virtuella datorn finnas i mål regionen. 

### <a name="prepare-to-move-the-source-resource-group"></a>Förbered för att flytta käll resurs gruppen

Under förberedelse processen genererar resurs förflyttningen Azure Resource Manager (ARM) mallar med hjälp av resurs grupps inställningarna. Resurser i resurs gruppen påverkas inte.

Förbered enligt följande:

1. I **flera regioner**väljer du käll resurs gruppen > **Förbered**.
2. I **Förbered resurser**klickar du på **Förbered**.

    ![Förbered resurs grupp](./media/tutorial-move-region-virtual-machines/prepare-resource-group.png)

> [!NOTE]
> När du har bearbetat resurs gruppen är det i läget *Starta flyttning väntar* . 

 
### <a name="move-the-source-resource-group"></a>Flytta käll resurs gruppen

Starta flyttningen enligt följande:

1. I **flera regioner**väljer du resurs grupp > **initiera flytt**
2. ra **Flytta resurser**, klicka på **initiera flytta**. Resurs gruppen flyttas till en *initierings status som börjar* gälla.
3. När flytten har påbörjats skapas mål resurs gruppen baserat på den genererade ARM-mallen. Käll resurs gruppen flyttas till ett *förväntat flyttnings* tillstånd.

    ![Klicka på knappen påbörja flyttning](./media/tutorial-move-region-virtual-machines/commit-move-pending.png)

För att genomföra och slutföra flytt processen:

1. I **flera regioner**väljer du resurs gruppen > **genomför flytt**.
2. ra **Flytta resurser**, klicka på **genomför**.

> [!NOTE]
> När flytten har genomförts är käll resurs gruppen i ett *väntande tillstånd för att ta bort källan* .

## <a name="prepare-resources-to-move"></a>Förbered resurser för att flytta

Nu när käll resurs gruppen flyttas kan du förbereda för att flytta de andra resurserna.

1. I **flera regioner**väljer du de resurser som du vill förbereda. 

    ![Sidan för att välja Förbered för andra resurser](./media/tutorial-move-region-virtual-machines/prepare-other.png)

2. Välj **Förbered**. 

> [!NOTE]
> - Under förberedelse processen installeras Azure Site Recovery Mobility-agenten på virtuella datorer för att replikera dem.
> - VM-data replikeras regelbundet till mål regionen. Detta påverkar inte den virtuella käll datorn.
> - Resurs flyttning genererar ARM-mallar för de andra käll resurserna.
> - När du har bearbetat resurser är de i ett *initierings* tillstånd som väntar.

![Sida som visar resurser i status för att påbörja flytt](./media/tutorial-move-region-virtual-machines/initiate-move-pending.png)


## <a name="initiate-the-move"></a>Påbörja flytten

När resurserna har förberetts kan du nu initiera flytten. 

1. I **flera regioner**väljer du resurser med tillstånds *initieringen väntar*. Klicka sedan på **Starta flyttning**.
2. I **Flytta resurser**klickar du på **påbörja flyttning**.

    ![Klicka för knappen initiera flyttning](./media/tutorial-move-region-virtual-machines/initiate-move.png)

3. Spåra flyttnings förlopp i meddelande fältet.

> [!NOTE]
> - Virtuella repliker skapas i mål regionen för virtuella datorer. Den virtuella käll datorn stängs av och vissa drift stopp inträffar (vanligt vis minuter).
> - Resurs förflyttningen återskapar andra resurser med ARM-mallarna som för bereddes. Det finns vanligt vis ingen stillestånds tid.
> - När du har flyttat resurserna är de i ett tillstånd där *flytt väntar* .

![Sidan visar resurser i * ta bort källan väntar på tillstånd](./media/tutorial-move-region-virtual-machines/delete-source-pending.png)


## <a name="discard-or-commit"></a>Ta bort eller bekräfta?

Efter den första flyttningen kan du bestämma om du vill att flyttningen ska utföras eller om du vill ta bort den. 

- **Ignorera**: du kan ta bort en flytt om du testar och du inte vill flytta käll resursen. Om du tar bort flytten returneras resursen till ett tillstånd där *initieringen väntar*.
- **Commit**: commit Slutför flyttningen till mål regionen. När du har gjort det kommer en käll resurs att vara i ett tillstånd där *borttagnings källan väntar*, och du kan välja om du vill ta bort den.


## <a name="discard-the-move"></a>Ta bort flytten 

Du kan ta bort flytten på följande sätt:

1. I **flera regioner**väljer du resurser med status *commit flytta väntar*och klickar på **ta bort flyttning**.
2. I **ta bort flyttning**klickar du på **Ignorera**.
3. Spåra flyttnings förlopp i meddelande fältet.


> [!NOTE]
> När du har tagit bort resurser är de virtuella datorerna i ett *initierings* tillstånd som väntar.

## <a name="commit-the-move"></a>Genomför flyttningen

Spara flyttningen om du vill slutföra flyttnings processen. 

1. I **flera regioner**väljer du resurser med status *bekräftelse flytt väntar*och klickar på **genomför flyttning**.
2. I **genomför resurser**klickar du på **genomför**.

    ![Sida för att allokera resurser för att slutföra flytten](./media/tutorial-move-region-virtual-machines/commit-resources.png)

3. Spåra inchecknings förloppet i meddelande fältet.

> [!NOTE]
> - När flytten har genomförts upphör virtuella datorer att replikera. Den virtuella käll datorn påverkas inte av genomförandet.
> - Commit påverkar inte käll nätverks resurser.
> - När flytten har genomförts är resurserna i ett väntande tillstånd för att *ta bort källan* .

![Sidan visar resurser i * ta bort källan väntar på tillstånd](./media/tutorial-move-region-virtual-machines/delete-source-pending.png)


## <a name="configure-settings-after-the-move"></a>Konfigurera inställningar efter flyttningen

- Mobilitets tjänsten avinstalleras inte automatiskt från virtuella datorer. Avinstallera det manuellt eller lämna det om du planerar att flytta servern igen.
- Ändra Azure-rollbaserad åtkomst kontroll (Azure RBAC) regler efter flytten.

## <a name="delete-source-resources-after-commit"></a>Ta bort käll resurser efter incheckning

Efter flytten kan du välja att ta bort resurser i käll regionen. 

1. I **flera regioner**klickar du på namnet på varje käll resurs som du vill ta bort.
2. På sidan Egenskaper för varje resurs väljer du **ta bort**.

## <a name="delete-additional-resources-created-for-move"></a>Ta bort ytterligare resurser som har skapats för flytt

Efter flyttningen kan du ta bort flyttnings samlingen manuellt och Site Recovery resurser som har skapats.

- Flyttnings samlingen är dold som standard. Du måste aktivera dolda resurser för att kunna se det.
- Cache-lagringen har ett lås som måste tas bort innan den kan tas bort.

Ta bort enligt följande: 
1. Leta upp resurserna i resurs gruppen ```RegionMoveRG-<sourceregion>-<target-region>``` .
2. Kontrol lera att alla virtuella datorer och andra käll resurser i käll regionen har flyttats eller tagits bort. Detta säkerställer att det inte finns några väntande resurser som använder dem.
2. Ta bort resurserna:

    - Namnet på flyttnings samlingen är ```movecollection-<sourceregion>-<target-region>``` .
    - Namnet på cachens lagrings konto är ```resmovecache<guid>```
    - Valv namnet är ```ResourceMove-<sourceregion>-<target-region>-GUID``` .
## <a name="next-steps"></a>Nästa steg

I den här kursen får du:

> [!div class="checklist"]
> * Flyttade virtuella Azure-datorer till en annan Azure-region.
> * Flyttade resurser som är kopplade till virtuella datorer till en annan region.

Nu kan du prova att flytta Azure SQL-databaser och elastiska pooler till en annan region.

> [!div class="nextstepaction"]
> [Flytta Azure SQL-resurser](./tutorial-move-region-sql.md)
