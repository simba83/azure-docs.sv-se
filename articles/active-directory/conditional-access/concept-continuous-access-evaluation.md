---
title: Utvärdering av kontinuerlig åtkomst i Azure AD
description: Att svara på ändringar i användar tillstånd snabbare med utvärdering av kontinuerlig åtkomst i Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ca23c1503b01c1aa9523edc2576599d7b6ab458
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2020
ms.locfileid: "91992817"
---
# <a name="continuous-access-evaluation"></a>Utvärdering av kontinuerlig åtkomst

Förfallo datum för token och uppdatering är en standard mekanism i branschen. När ett klient program som Outlook ansluter till en tjänst som Exchange Online, auktoriseras API-begäranden med hjälp av OAuth 2,0-åtkomsttoken. Som standard är dessa åtkomsttoken giltiga i en timme, och när de går ut omdirigeras klienten tillbaka till Azure AD för att uppdatera dem. Uppdaterings perioden ger möjlighet att utvärdera principer för användar åtkomst. Exempel: vi kan välja att inte uppdatera token på grund av en princip för villkorlig åtkomst, eller på grund av att användaren har inaktiverats i katalogen. 

Kunderna har uttryckt frågor om fördröjningen mellan när villkoren ändras för användaren, t. ex. nätverks plats eller stöld av autentiseringsuppgifter, och när principer kan tillämpas som rör den ändringen. Vi har experimentat med "Blunt Object"-metoden för minskad token för token men upptäckte att de kan försämra användar upplevelsen och tillförlitligheten utan att eliminera risker.

Svars tiden för princip överträdelser eller säkerhets problem kräver verkligen en "konversation" mellan token Issuer, t. ex. Azure AD och den förlitande parten, t. ex. Exchange Online. Den här dubbelriktade konversationen ger oss två viktiga funktioner. Den förlitande parten kan se när saker har ändrats, till exempel en klient som kommer från en ny plats, och meddela token utfärdaren. Det ger också token utfärdaren ett sätt att säga till att den förlitande parten slutar att följa tokens för en specifik användare på grund av konto kompromisser, inaktivitet eller andra problem. Mekanismen för den här konversationen är en utvärdering av kontinuerlig åtkomst (CAE). Målet är att svaret är nära real tid, men i vissa fall kan svars tiden på upp till 15 minuter observeras på grund av händelse spridningen.

Den första implementeringen av utvärderingen av kontinuerlig åtkomst fokuserar på Exchange, teams och SharePoint Online. 

### <a name="key-benefits"></a>Viktiga fördelar

- Användarens upphör ande eller lösen ords ändring/återställning: återkallning av användarsessioner kommer att verkställas i nära real tid.
- Nätverks plats ändring: principer för villkorlig åtkomst används i nära real tid.
- Token export till en dator utanför ett betrott nätverk kan förhindras med plats principer för villkorlig åtkomst.

## <a name="scenarios"></a>Scenarier 

Det finns två scenarier som utgör utvärdering av kontinuerlig åtkomst, utvärdering av kritiska händelser och utvärdering av villkorlig åtkomst princip.

### <a name="critical-event-evaluation"></a>Utvärdering av kritisk händelse

Utvärdering av kontinuerlig åtkomst implementeras genom att aktivera tjänster, t. ex. Exchange Online, SharePoint Online och team, för att prenumerera på kritiska händelser i Azure AD så att dessa händelser kan utvärderas och tillämpas nära real tid. Utvärderingen av kritiska händelser förlitar sig inte på principer för villkorlig åtkomst så är tillgängliga i alla klient organisationer. Följande händelser utvärderas för närvarande:

- Användar kontot har tagits bort eller inaktiverats
- Lösen ordet för en användare ändras eller återställs
- Multi-Factor Authentication har Aktiver ATS för användaren
- Administratören återkallar uttryckligen alla uppdateringstoken för en användare
- Utökad användar risk upptäckt av Azure AD Identity Protection

Den här processen gör det möjligt för användaren att förlora åtkomsten till SharePoint Online-filer, e-post, kalender eller uppgifter i organisationen, och team från Microsoft 365-klientens appar i minuter efter en av dessa kritiska händelser. 

### <a name="conditional-access-policy-evaluation-preview"></a>Utvärdering av princip för villkorlig åtkomst (för hands version)

Exchange och SharePoint kan synkronisera viktiga principer för villkorlig åtkomst så att de kan utvärderas i själva tjänsten.

Den här processen gör det möjligt för användaren att förlora åtkomst till organisatoriska filer, e-post, kalender eller uppgifter från Microsoft 365-klient program eller SharePoint Online omedelbart efter ändringar i nätverks platsen.

> [!NOTE]
> Det finns inte stöd för all kombination av appar och resurs leverantörer. Se tabellen nedan. Office refererar till Word, Excel och PowerPoint

| | Outlook Web | Outlook Win32 | Outlook iOS | Outlook Android | Outlook Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | Stöds | Stöds | Stöds inte | Stöds inte | Stöds |
| **Exchange Online** | Stöds | Stöds | Stöds | Stöds | Stöds |

| | Office-webbappar | Office Win32-appar | Office för iOS | Office för Android | Office för Mac |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **SharePoint Online** | Stöds inte | Stöds | Stöds | Stöds | Stöds |
| **Exchange Online** | Stöds inte | Stöds | Stöds | Stöds | Stöds |

### <a name="client-side-claim-challenge"></a>Anspråks kontroll på klient Sidan

Innan utvärderingen av den kontinuerliga åtkomsten görs försöker klienter alltid att spela upp åtkomsttoken från sin cache så länge den inte har upphört att gälla. Med CAE presenterar vi ett nytt ärende som en resurs leverantör kan avvisa en token även när den inte har upphört att gälla. För att informera klienter om att kringgå cacheminnet även om cachelagrade token inte har gått ut, introducerar vi en mekanism som kallas **anspråks utmaning** för att indikera att token avvisades och att en ny åtkomsttoken måste utfärdas av Azure AD. CAE kräver en klient uppdatering för att förstå anspråks utmaningen. Den senaste versionen av följande program nedan stöder anspråks utmaning:

- Outlook-fönster
- Outlook iOS
- Outlook Android
- Outlook Mac
- Outlook Web App
- Team för Windows (endast för team resurser)
- Teams iOS (endast för team resurser)
- Teams Android (endast för team resurser)
- Teams Mac (endast för team resurser)
- Word/Excel/PowerPoint för Windows
- Word/Excel/PowerPoint för iOS
- Word/Excel/PowerPoint för Android
- Word/Excel/PowerPoint för Mac

### <a name="token-lifetime"></a>Livs längd för token

Eftersom risk och principer utvärderas i real tid, förlitar sig klienterna som förhandlar om att en utvärdering av kontinuerlig åtkomst utvärderas på CAE i stället för befintliga livs längds principer för statisk åtkomst, vilket innebär att den konfigurerbara livs längden för token inte kommer att användas längre för CAE-kompatibla klienter som förhandlar om CAE-medvetna sessioner.

Livs längden för token höjs till lång livs längd, upp till 28 timmar, i CAE-sessioner. Återkallning sköts av kritiska händelser och princip utvärdering, inte bara en godtycklig tids period. Den här ändringen ökar stabiliteten för program utan att det påverkar säkerhets position. 

Om du inte använder CAE-kompatibla klienter blir din standardtoken för åtkomsttoken kvar 1 timme, om du inte har konfigurerat din livstid för åtkomsttoken med hjälp av för hands versionen av den [konfigurerbara token (CTL)](../develop/active-directory-configurable-token-lifetimes.md) .

## <a name="example-flows"></a>Exempel flöden

### <a name="user-revocation-event-flow"></a>Händelse flöde för återkallade användare:

![Händelse flöde för återkallning av användare](./media/concept-continuous-access-evaluation/user-revocation-event-flow.png)

1. En CAE-kompatibel klient visar autentiseringsuppgifter eller en uppdateringstoken till Azure AD som efterfrågar en åtkomsttoken för en viss resurs.
1. En åtkomsttoken returneras tillsammans med andra artefakter till klienten.
1. En administratör [återkallar uttryckligen alla uppdateringstoken för användaren](/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0). En återkallnings händelse kommer att skickas till resurs leverantören från Azure AD.
1. En åtkomsttoken visas för resurs leverantören. Resurs leverantören utvärderar giltigheten hos token och kontrollerar om det finns någon återkallnings händelse för användaren. Resurs leverantören använder den här informationen för att avgöra om du vill bevilja åtkomst till resursen eller inte.
1. I det här fallet nekar resurs leverantören åtkomst och skickar en 401 + anspråks utmaning tillbaka till klienten.
1. Den CAE-kompatibla klienten förstår 401 +-anspråks utmaningen. Den kringgår cacheminnena och går tillbaka till steg 1 och skickar dess uppdateringstoken tillsammans med anspråks utmaningen tillbaka till Azure AD. Azure AD kommer sedan att utvärdera alla villkor och uppmana användaren att autentisera om i det här fallet.

### <a name="user-condition-change-flow-preview"></a>Ändrings flöde för användar villkor (för hands version):

I följande exempel har en administratör för villkorlig åtkomst konfigurerat en plats baserad princip för villkorlig åtkomst för att endast tillåta åtkomst från vissa IP-intervall:

![Händelse flöde för användar villkor](./media/concept-continuous-access-evaluation/user-condition-change-flow.png)

1. En CAE-kompatibel klient visar autentiseringsuppgifter eller en uppdateringstoken till Azure AD som efterfrågar en åtkomsttoken för en viss resurs.
1. Azure AD utvärderar alla principer för villkorlig åtkomst för att se om användaren och klienten uppfyller villkoren.
1. En åtkomsttoken returneras tillsammans med andra artefakter till klienten.
1. Användaren flyttar ut från ett tillåtet IP-intervall
1. Klienten presenterar en åtkomsttoken till resurs leverantören utanför ett tillåtet IP-adressintervall.
1. Resurs leverantören utvärderar giltigheten hos token och kontrollerar plats principen som synkroniseras från Azure AD.
1. I det här fallet nekar resurs leverantören åtkomst och skickar en 401 + anspråks utmaning tillbaka till klienten eftersom den inte kommer från tillåtna IP-adressintervall.
1. Den CAE-kompatibla klienten förstår 401 +-anspråks utmaningen. Den kringgår cacheminnena och går tillbaka till steg 1 och skickar dess uppdateringstoken tillsammans med anspråks utmaningen tillbaka till Azure AD. Azure AD utvärderar alla villkor och kommer att neka åtkomst i det här fallet.

## <a name="enable-or-disable-cae-preview"></a>Aktivera eller inaktivera CAE (för hands version)

1. Logga in på **Azure Portal** som administratör för villkorlig åtkomst, säkerhets administratör eller global administratör
1. Bläddra till **Azure Active Directory**  >  **säkerhet**för  >  **kontinuerlig åtkomst**.
1. Välj **Aktivera förhands granskning**.

Från den här sidan kan du välja att begränsa de användare och grupper som ska omfattas av för hands versionen.

![Aktivera för hands versionen av CAE i Azure Portal](./media/concept-continuous-access-evaluation/enable-cae-preview.png)

## <a name="troubleshooting"></a>Felsökning

### <a name="supported-location-policies"></a>Plats principer som stöds

För CAE har vi bara insikter om namngivna IP-baserade namngivna platser. Vi har inga insikter på andra plats inställningar som [MFA-betrodda IP-adresser](../authentication/howto-mfa-mfasettings.md#trusted-ips) eller landsspecifika platser. När en användare kommer från en MFA-betrodd IP-adress eller betrodda platser som innehåller MFA-betrodda IP-adresser eller land, kommer CAE inte att tillämpas när användaren flyttar till en annan plats. I dessa fall kommer vi att utfärda en CAE-token för en timme utan omedelbar kontroll av IP-tvång.

> [!IMPORTANT]
> När du konfigurerar platser för utvärdering av kontinuerlig åtkomst ska du bara använda den [IP-baserade villkorliga åtkomst platsen](../conditional-access/location-condition.md#preview-features) och konfigurera alla IP-adresser, **inklusive både IPv4 och IPv6**, som kan visas av leverantören av identitets leverantören och resurser. Använd inte villkor för land eller den betrodda IP-funktionen som är tillgänglig på sidan tjänst inställningar för Azure Multi-Factor Authentication.

### <a name="ip-address-configuration"></a>Konfigurera IP-adress

Identitets leverantören och resurs leverantörerna kan se olika IP-adresser. Detta matchnings fel kan inträffa på grund av nätverks-proxy-implementeringar i din organisation eller felaktiga IPv4/IPv6-konfigurationer mellan identitets leverantören och resurs leverantören. Till exempel:

- Din identitetsprovider ser en IP-adress från klienten.
- Resurs leverantören ser en annan IP-adress från klienten när den har passerat via en proxy.
- IP-adressen som identitets leverantören ser är en del av ett tillåtet IP-intervall i principen, men IP-adressen från resurs leverantören är inte.

Om det här scenariot finns i din miljö för att undvika oändliga slingor, utfärdar Azure AD en en timme CAE-token och kommer inte att framtvinga ändring av klientens plats. Även i det här fallet har säkerheten förbättrats jämfört med traditionella token för en timme eftersom vi fortfarande bedömer de [andra händelserna](#critical-event-evaluation) förutom klient plats ändrings händelser.

### <a name="office-and-web-account-manager-settings"></a>Office-och Web Account Manager-inställningar

| Office Update-kanal | DisableADALatopWAMOverride | DisableAADWAM |
| --- | --- | --- |
| Semi-Annual Enterprise-kanal | Om inställningen är aktive rad eller 1, stöds inte CAE. | Om inställningen är aktive rad eller 1, stöds inte CAE. |
| Aktuell kanal <br> eller <br> Månatlig företags kanal | CAE stöds oavsett inställningen | CAE stöds oavsett inställningen |

En förklaring av Office Update-kanaler finns i [Översikt över uppdaterings kanaler för Microsoft 365 appar](/deployoffice/overview-update-channels). Vi rekommenderar att organisationer inte inaktiverar WAM (Web Account Manager).

### <a name="policy-change-timing"></a>Ändrings tid för princip

På grund av risken för fördröjning mellan Azure AD och resurs leverantörer, kan det ta upp till 2 timmar för princip ändringar som görs av administratörer att bli effektiva för Exchange Online.

Exempel: Administratören lägger till en princip för att blockera ett intervall av IP-adresser från åtkomst till e-post på 11:00 AM, en användare som kommer från det IP-intervallet innan de kan komma åt e-post förrän 1:00 PM.

### <a name="coauthoring-in-office-apps"></a>Samredigering i Office-appar

När flera användare samarbetar i samma dokument samtidigt kan användarens åtkomst till dokumentet inte omedelbart återkallas av CAE baserat på användarens återkallning eller princip ändrings händelser. I det här fallet förlorar användaren åtkomst helt efter, stänger dokumentet, stänger Word, Excel eller PowerPoint, eller efter en period på 10 timmar.

För att minska den här tiden kan en SharePoint-administratör alternativt minska den maximala livs längden för samtidiga sessioner för dokument som lagras i SharePoint Online och OneDrive för företag, genom att [Konfigurera en princip för nätverks plats i SharePoint Online](/sharepoint/control-access-based-on-network-location). När den här konfigurationen har ändrats minskas den maximala livs längden för samtidiga sessioner till 15 minuter och kan justeras ytterligare med hjälp av SharePoint Online PowerShell-kommandot Set-SPOTenant – IPAddressWACTokenLifetime

### <a name="enable-after-a-user-is-disabled"></a>Aktivera när en användare har inaktiverats

Om du aktiverar en användar rättighet när den har inaktiverats. Det kommer att finnas en fördröjning innan kontot kan aktive ras. SPO och team kommer att ha en fördröjning på 15 minuter. Fördröjningen är 35-40 minuter för EXO.

## <a name="faqs"></a>Vanliga frågor och svar

### <a name="how-will-cae-work-with-sign-in-frequency"></a>Hur fungerar CAE med inloggnings frekvensen?

Inloggnings frekvensen kommer att behållas med eller utan CAE.

## <a name="next-steps"></a>Nästa steg

[Vi presenterar en utvärdering av kontinuerlig åtkomst](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)