---
title: Bästa praxis för Windows Virtual Desktop-säkerhet – Azure
description: Metod tips för att skydda din Windows-miljö för virtuella skriv bord.
author: heidilohr
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: a194074b75a404e5a28e86015b0d0bcea2619fc2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "88002287"
---
# <a name="security-best-practices"></a>Rekommenderade säkerhetsmetoder

Windows Virtual Desktop är en hanterad virtuell Skriv bords tjänst som innehåller många säkerhetsfunktioner för att hålla organisationen säker. I en distribution av virtuella Windows-datorer hanterar Microsoft delar av tjänsterna för kundens räkning. Tjänsten har många inbyggda avancerade säkerhetsfunktioner, till exempel omvänd anslutning, vilket minskar risken för att fjärrskrivbord är åtkomligt från var som helst.

Den här artikeln beskriver ytterligare steg som du kan vidta som administratör för att hålla dina kunders distributioner av Windows virtuella skriv bord säkra.

## <a name="security-responsibilities"></a>Säkerhets ansvars områden

Det som gör moln tjänster annorlunda än traditionella lokala infrastrukturer för virtuella skriv bord (VDIs) är hur de hanterar säkerhets ansvars områden. I en traditionell lokal VDI är kunden till exempel ansvarig för alla säkerhets aspekter. I de flesta moln tjänster delas dock dessa ansvars områden mellan kunden och företaget.

När du använder det virtuella Windows-skrivbordet är det viktigt att förstå att även om vissa komponenter redan är säkrade för din miljö måste du konfigurera andra områden så att de passar din organisations säkerhets behov.

Här är de säkerhets behov som du är ansvarig för i distributionen av Windows Virtual Desktop:

| Säkerhets behov | Är kunden ansvarig för detta? |
|---------------|:-------------------------:|
|Identitet|Ja|
|Användar enheter (mobil och PC)|Ja|
|App Security|Ja|
|Sessionens värd operativ system|Ja|
|Distributions konfiguration|Ja|
|Nätverkskontroller|Ja|
|Kontroll plan för nätverksvirtualisering|Inga|
|Fysiska värdar|Inga|
|Fysiskt nätverk|Inga|
|Fysiskt Data Center|Inga|

Säkerheten behöver kunden är inte ansvarig för hanteras av Microsoft.

## <a name="azure-security-best-practices"></a>Metodtips för Azure Security

Windows Virtual Desktop är en tjänst under Azure. För att maximera säkerheten för distributionen av virtuella Windows-datorer bör du se till att även skydda den omgivande Azure-infrastrukturen och hanterings planet. Om du vill skydda din infrastruktur bör du tänka på hur Windows Virtual Desktop passar in i ditt större Azure-eko system. Mer information om Azure-eko systemet finns i [metod tips och mönster för Azure-säkerhet](../security/fundamentals/best-practices-and-patterns.md).

Det här avsnittet beskriver metod tips för att skydda ditt Azure-eko system.

### <a name="enable-azure-security-center"></a>Aktivera Azure Security Center

Vi rekommenderar att du aktiverar Azure Security Center standard för prenumerationer, virtuella datorer, nyckel valv och lagrings konton.

Med Azure Security Center standard kan du:

* Hantera sårbarheter.
* Utvärdera efterlevnaden av vanliga ramverk som PCI.
* Förstärk den övergripande säkerheten för din miljö.

Mer information finns i [publicera din Azure-prenumeration till Security Center standard](../security-center/security-center-get-started.md).

### <a name="improve-your-secure-score"></a>Förbättra dina säkerhetspoäng

Säkra poäng ger rekommendationer och metod tips för att förbättra den övergripande säkerheten. Dessa rekommendationer prioriteras för att hjälpa dig att välja vilka som är viktigast och snabb korrigerings alternativ hjälper dig att snabbt hantera potentiella sårbarheter. Dessa rekommendationer uppdateras också med tiden och du bör hålla dig uppdaterad om de bästa sätten att underhålla din miljös säkerhet. Mer information finns [i förbättra dina säkra poäng i Azure Security Center](../security-center/security-center-secure-score.md).

## <a name="windows-virtual-desktop-security-best-practices"></a>Metod tips för Windows Virtual Desktop-säkerhet

Virtuella Windows-datorer har många inbyggda säkerhets kontroller. I det här avsnittet får du lära dig om säkerhets kontroller som du kan använda för att hålla dina användare och data säkra.

### <a name="require-multi-factor-authentication"></a>Kräv Multi-Factor Authentication

Att kräva Multi-Factor Authentication för alla användare och administratörer i den virtuella Windows-skrivbordet förbättrar säkerheten för hela distributionen. Mer information finns i [Aktivera Azure Multi-Factor Authentication för Windows Virtual Desktop](set-up-mfa.md).

### <a name="enable-conditional-access"></a>Aktivera villkorlig åtkomst

Genom att aktivera [villkorlig åtkomst](../active-directory/conditional-access/best-practices.md) kan du hantera risker innan du ger användarna åtkomst till din Windows-miljö för virtuella datorer. När du bestämmer vilka användare som ska bevilja åtkomst till rekommenderar vi att du även funderar på vem användaren är, hur de loggar in och vilken enhet de använder.

### <a name="collect-audit-logs"></a>Samla in gransknings loggar

Genom att aktivera Gransknings logg samling kan du Visa användar-och administratörs aktivitet som är relaterad till Windows Virtual Desktop. Några exempel på nyckel gransknings loggar är:

-   [Azure aktivitets logg](../azure-monitor/platform/activity-log-collect.md)
-   [Azure Active Directory aktivitets logg](../active-directory/reports-monitoring/concept-activity-logs-azure-monitor.md)
-   [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md)
-   [Värdar för sessioner](../azure-monitor/platform/agent-windows.md)
-   [Windows Virtual Desktop Diagnostic-logg](../virtual-desktop/diagnostics-log-analytics.md)
-   [Key Vault loggar](../key-vault/general/logging.md)

### <a name="use-remoteapps"></a>Använda RemoteApp

När du väljer en distributions modell kan du antingen ge fjärran vändare åtkomst till hela virtuella skriv bord eller bara välja program. Fjärrprogram eller RemoteApp-program ger en sömlös upplevelse när användaren arbetar med appar på sina virtuella datorer. RemoteApp minskar risken genom att låta användaren arbeta med med en delmängd av den fjärrdator som exponeras av programmet.

### <a name="monitor-usage-with-azure-monitor"></a>Övervaka användning med Azure Monitor

Övervaka användning och tillgänglighet för Windows Virtual Desktop-tjänsten med [Azure Monitor](https://azure.microsoft.com/services/monitor/). Överväg att skapa [service Health-aviseringar](../service-health/alerts-activity-log-service-notifications.md) för Windows Virtual Desktop-tjänsten för att ta emot meddelanden när en händelse som rör tjänsten påverkas.

## <a name="session-host-security-best-practices"></a>Rekommenderade säkerhets metoder för Session Host

Sessions-värdar är virtuella datorer som körs i en Azure-prenumeration och ett virtuellt nätverk. Den övergripande säkerheten för din Windows-distribution för Windows är beroende av de säkerhets kontroller som du har gjort på dina sessions-värdar. I det här avsnittet beskrivs de bästa metoderna för att hålla värdarna för dina sessioner säkra.

### <a name="enable-endpoint-protection"></a>Aktivera Endpoint Protection

För att skydda din distribution från känd skadlig program vara rekommenderar vi att du aktiverar Endpoint Protection på alla värdar i sessionen. Du kan använda antingen Windows Defender Antivirus eller ett program från tredje part. Mer information finns i [distributions guide för Windows Defender Antivirus i en VDI-miljö](/windows/security/threat-protection/windows-defender-antivirus/deployment-vdi-windows-defender-antivirus).

För profil lösningar som FSLogix eller andra lösningar som monterar VHD-filer rekommenderar vi att du undantar fil namns tillägg för virtuella hård diskar.

### <a name="install-an-endpoint-detection-and-response-product"></a>Installera en produkt för slut punkts identifiering och-svar

Vi rekommenderar att du installerar en produkt för slut punkts identifiering och-svar (EDR) för att tillhandahålla avancerade funktioner för identifiering och svar. För att serveroperativ system med [Azure Security Center](../security-center/security-center-services.md) aktive rad, ska installera en EDR-produkt som distribuerar Defender ATP. För klient operativ system kan du distribuera [Defender ATP](/windows/security/threat-protection/microsoft-defender-atp/onboarding) eller en produkt från tredje part till dessa slut punkter.

### <a name="enable-threat-and-vulnerability-management-assessments"></a>Aktivera utvärdering av hot-och sårbarhets hantering

Det är viktigt att identifiera program sårbarheter som finns i operativ system och program för att skydda din miljö. Azure Security Center kan hjälpa dig att identifiera problem punkter genom sårbarhets bedömningar för serveroperativ system. Du kan också använda Defender ATP, som ger hot-och sårbarhets hantering för Skriv bords operativ system. Du kan också använda produkter från tredje part om du är så lutad, men vi rekommenderar att du använder Azure Security Center och Defender ATP.

### <a name="patch-software-vulnerabilities-in-your-environment"></a>Korrigera sårbarheter för program vara i din miljö

När du har identifierat ett säkerhets problem måste du korrigera det. Detta gäller även för virtuella miljöer, som omfattar de operativ system som körs, de program som distribueras i dem och de avbildningar som du skapar nya datorer från. Följ dina meddelanden om leverantörs korrigering och Använd korrigeringsfiler i rimlig tid. Vi rekommenderar att du uppdaterar dina bas avbildningar varje månad för att säkerställa att nyligen distribuerade datorer är så säkra som möjligt.

### <a name="establish-maximum-inactive-time-and-disconnection-policies"></a>Upprätta maximalt antal inaktiva tid och från kopplings principer

Att logga ut användare när de är inaktiva bevarar resurser och förhindrar åtkomst av obehöriga användare. Vi rekommenderar att tids gränsen balanserar användar produktiviteten och resursanvändningen. För användare som interagerar med tillstånds lösa program bör du överväga mer aggressiva principer som stänger av datorer och bevarar resurser. Frånkopplade program som körs länge och som fortsätter att köras om en användare är inaktiv, till exempel en simulering eller en CAD-åter givning, kan avbryta användarens arbete och kan till och med behöva starta om datorn.

### <a name="set-up-screen-locks-for-idle-sessions"></a>Konfigurera skärm lås för inaktiva sessioner

Du kan förhindra oönskad åtkomst till systemet genom att konfigurera Windows Virtual Desktop för att låsa dator skärmen vid inaktivitet och kräva autentisering för att låsa upp den.

### <a name="establish-tiered-admin-access"></a>Upprätta nivå administratörs åtkomst

Vi rekommenderar att du inte beviljar dina användare administratörs åtkomst till virtuella skriv bord. Om du behöver program varu paket rekommenderar vi att du gör dem tillgängliga via konfigurations hanterings verktyg som Microsoft Endpoint Manager. I en miljö med flera sessioner rekommenderar vi att du låter användarna inte installera program vara direkt.

### <a name="consider-which-users-should-access-which-resources"></a>Överväg vilka användare som ska ha åtkomst till vilka resurser

Betrakta värdarna som en utökning av din befintliga Skriv bords distribution. Vi rekommenderar att du styr åtkomsten till nätverks resurser på samma sätt som för andra datorer i din miljö, t. ex. genom att använda nätverks segmentering och filtrering. Som standard kan sessionernas värdar ansluta till alla resurser på Internet. Det finns flera sätt att begränsa trafiken, inklusive att använda Azure-brandväggen, virtuella nätverks installationer eller proxyservrar. Om du behöver begränsa trafiken kontrollerar du att du lägger till rätt regler så att det virtuella Windows-skrivbordet fungerar som det ska.

### <a name="manage-office-pro-plus-security"></a>Hantera Office Pro plus-säkerhet

Förutom att skydda dina värdbaserade sessioner är det viktigt att även skydda programmen som körs i dem. Office Pro Plus är ett av de vanligaste programmen som distribueras i värdbaserade sessioner. För att förbättra säkerheten för Office-distribution rekommenderar vi att du använder [säkerhets princip Advisor](/DeployOffice/overview-of-security-policy-advisor) för Microsoft 365 appar för företag. Det här verktyget identifierar principer som kan användas för din distribution för mer säkerhet. Säkerhets princip Advisor rekommenderar också principer som baseras på deras inverkan på din säkerhet och produktivitet.

### <a name="other-security-tips-for-session-hosts"></a>Andra säkerhets tips för värdbaserade sessioner

Genom att begränsa operativ systemets funktioner kan du förbättra säkerheten för dina sessions värdar. Här är några saker du kan göra:

- Kontrol lera enhets omdirigering genom att omdirigera enheter, skrivare och USB-enheter till en användares lokala enhet i en fjärrskrivbordssession. Vi rekommenderar att du utvärderar dina säkerhets krav och kontrollerar om dessa funktioner bör inaktive ras eller inte.

- Begränsa åtkomst till Utforskaren genom att dölja lokala mappningar och fjärranslutna enheter. Detta förhindrar användare från att identifiera oönskad information om system konfiguration och användare.

- Undvik direkt RDP-åtkomst till sessionsbaserade värdar i din miljö. Om du behöver direkt RDP-åtkomst för administration eller fel sökning kan du aktivera [just-in-Time-](../security-center/security-center-just-in-time.md) åtkomst för att begränsa den potentiella angrepps ytan på en sessions värd.

- Bevilja användare begränsade behörigheter när de ansluter till lokala och fjärranslutna fil system. Du kan begränsa behörigheterna genom att se till att dina lokala och fjärranslutna fil system använder åtkomst kontrol listor med minsta behörighet. På så sätt kan användarna bara komma åt vad de behöver och inte kan ändra eller ta bort viktiga resurser.

- Förhindra oönskad program vara från att köras på sessionsbaserade värdar. Du kan aktivera app Locker för ytterligare säkerhet på sessionsbaserade, och se till att endast de appar som du tillåter kan köras på värden.

## <a name="next-steps"></a>Nästa steg

Information om hur du aktiverar Multi-Factor Authentication finns i [Konfigurera Multi-Factor Authentication](set-up-mfa.md).