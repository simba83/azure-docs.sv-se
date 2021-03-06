---
title: Migrera till Azure-filresurser
description: Lär dig mer om migreringar till Azure-filresurser och hitta din guide för migrering.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 3/18/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: e3ecf29be94074f51ead3173f997154df6dfa88f
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92785620"
---
# <a name="migrate-to-azure-file-shares"></a>Migrera till Azure-filresurser

Den här artikeln beskriver de grundläggande aspekterna av en migrering till Azure-filresurser.

Den här artikeln innehåller grundläggande information om migrering och en tabell över stöd guider för migrering. Dessa rikt linjer hjälper dig att flytta dina filer till Azure-filresurser. Guiderna organiseras utifrån var dina data finns och vilken distributions modell (enbart molnbaserad eller hybrid) som du flyttar till.

## <a name="migration-basics"></a>Grundläggande om migrering

Azure har flera tillgängliga typer av moln lagring. En grundläggande aspekt av filmigreringar till Azure är att avgöra vilket alternativ för Azure Storage som är rätt för dina data.

[Azure-filresurser](storage-files-introduction.md) är lämpliga för fil data i generella syften. Dessa data omfattar allt du använder en lokal SMB-eller NFS-resurs för. Med [Azure File Sync](storage-sync-files-planning.md)kan du cachelagra innehållet i flera Azure-filresurser på servrar som kör Windows Server lokalt.

För en app som för närvarande körs på en lokal server kan det vara ett bra alternativ att lagra filer i en Azure-filresurs. Du kan flytta appen till Azure och använda Azure-filresurser som delad lagring. Du kan också överväga [Azure-diskar](../../virtual-machines/managed-disks-overview.md) för det här scenariot.

Vissa molnappar är inte beroende av SMB eller dator-lokal data åtkomst eller delad åtkomst. För dessa appar är det ofta det bästa valet av objekt lagring som [Azure-blobbar](../blobs/storage-blobs-overview.md) .

Nyckeln i en migrering är att avbilda all tillämplig fil åter givning när du flyttar dina filer från den aktuella lagrings platsen till Azure. Hur mycket god åter givning Azure Storage-alternativet stöder och hur mycket ditt scenario kräver för att du ska kunna välja rätt Azure-lagring. Generella fil data är vanligt vis beroende av fil-metadata. AppData kanske inte är det.

Här är de två grundläggande komponenterna i en fil:

- **Data ström** : data strömmen i en fil lagrar fil innehållet.
- **Fil-metadata** : filens metadata har följande del komponenter:
   * Filattribut som skrivskyddade
   * Fil behörigheter, som kan kallas för NTFS- *behörigheter* eller *ACL: er för filer och mappar*
   * Tidsstämplar, främst när de skapas och senaste ändrade tidsstämplar
   * En alternativ data ström, som är ett utrymme för att lagra större mängder egenskaper som inte är standard

Fil åter givning i en migrering kan definieras som möjlighet att:

- Lagra all tillämplig fil information på källan.
- Överför filer med migrations verktyget.
- Lagra filer i mål lagret för migreringen.

För att säkerställa att migreringen fortsätter smidigt kan [du identifiera det bästa kopierings verktyget för dina behov](#migration-toolbox) och matcha ett lagrings mål för din källa.

Med den tidigare informationen i kontot kan du se att mål lagringen för generella filer i Azure är Azure- [filresurser](storage-files-introduction.md).

Till skillnad från objekt lagring i Azure-blobar, kan en Azure-filresurs lagra metadata i en intern fil. Azure-filresurser bevarar också fil-och mapphierarkin, attribut och behörigheter. NTFS-behörigheter kan lagras på filer och mappar eftersom de är lokalt.

En användare av Active Directory, som är den lokala domänkontrollanten, kan få till gång till en Azure-filresurs internt. Detta kan vara en användare av Azure Active Directory Domain Services (Azure AD DS). Varje använder sin aktuella identitet för att få åtkomst baserat på resurs behörigheter och för ACL: er för filer och mappar. Detta fungerar på samma sätt som en användare som ansluter till en lokal fil resurs.

Den alternativa data strömmen är den främsta aspekten av fil åter givning som för närvarande inte kan lagras i en fil i en Azure-filresurs. Den bevaras lokalt när Azure File Sync används.

Lär dig mer om [Azure AD-autentisering](storage-files-identity-auth-active-directory-enable.md) och [Azure AD DS-autentisering](storage-files-identity-auth-active-directory-domain-service-enable.md) för Azure-filresurser.

## <a name="migration-guides"></a>Migreringsguider

I följande tabell visas detaljerade guider för migrering.

Så här använder du tabellen:

1. Leta upp raden för det käll system som dina filer lagras på.

1. Välj något av följande mål:

   - En hybrid distribution som använder Azure File Sync för att cachelagra innehållet i Azure-filresurser lokalt
   - Azure-filresurser i molnet

   Välj den mål kolumn som matchar ditt val.

1. I skärnings punkten mellan källa och mål visar en tabell cell tillgängliga migrerings scenarier. Välj en som länkas direkt till den detaljerade migreringsguiden.

Ett scenario utan en länk har ännu ingen publicerad migreringsguiden. Markera den här tabellen ibland för uppdateringar. Nya guider publiceras när de är tillgängliga.

| Källa | Mål: </br>Hybrid distribution | Mål: </br>Distribution i molnet |
|:---|:--|:--|
| | Verktygs kombination:| Verktygs kombination: |
| Windows Server 2012 R2 och senare | <ul><li>[Azure File Sync](storage-sync-files-deployment-guide.md)</li><li>[Azure File Sync och Azure Data Box](storage-sync-offline-data-transfer.md)</li><li>[Azure File Sync och förinstallerade filer i molnet](storage-sync-offline-data-transfer.md#azure-file-sync-and-pre-seeded-files-in-the-cloud)</li><li>Tjänsten Azure File Sync och lagringsmigrering</li></ul> | <ul><li>Azure File Sync</li><li>Azure File Sync och Data Box-enhet</li><li>Tjänsten Azure File Sync och lagringsmigrering</li><li>RoboCopy</li></ul> |
| Windows Server 2012 och tidigare | <ul><li>Azure File Sync och Data Box-enhet</li><li>Tjänsten Azure File Sync och lagringsmigrering</li></ul> | <ul><li>Tjänsten Azure File Sync och lagringsmigrering</li><li>RoboCopy</li></ul> |
| Nätverksansluten lagring (NAS) | <ul><li>[Azure File Sync och RoboCopy](storage-files-migration-nas-hybrid.md)</li></ul> | <ul><li>RoboCopy</li></ul> |
| Linux eller samba | <ul><li>[Azure File Sync och RoboCopy](storage-files-migration-linux-hybrid.md)</li></ul> | <ul><li>RoboCopy</li></ul> |
| Microsoft Azure StorSimple Cloud-apparaten 8100 eller StorSimple Cloud Appliance 8600 | <ul><li>[Azure File Sync och StorSimple Cloud Appliance 8020](storage-files-migration-storsimple-8000.md)</li></ul> | |
| StorSimple Cloud Appliance 1200 | <ul><li>[Azure File Sync](storage-files-migration-storsimple-1200.md)</li></ul> | |
| | | |

## <a name="migration-toolbox"></a>Verktyg för migrering

### <a name="file-copy-tools"></a>Fil kopierings verktyg

Det finns flera verktyg för fil kopiering som är tillgängliga från Microsoft och andra. Om du vill välja rätt verktyg för ditt migreringsjobb måste du tänka på följande grundläggande frågor:

* Stöder verktyget käll-och mål platserna för din fil kopiering?

* Stöder verktyget nätverks Sök vägen eller tillgängliga protokoll (till exempel REST, SMB eller NFS) mellan käll-och mål lagrings platserna?

* Bevarar verktyget nödvändig fil åter givning som stöds av käll-och mål platserna?

    I vissa fall stöder inte mål lagringen samma åter givning som källan. Om mål lagringen räcker för dina behov måste verktyget bara matcha målets funktioner för fil åter givning.

* Har verktyget funktioner som gör att det passar in i din strategi för migrering?

    Du kan till exempel överväga om verktyget kan minimera stillestånds tiden.
    
    När ett verktyg stöder ett alternativ för att spegla en källa till ett mål, kan du ofta köra det flera gånger på samma källa och mål när källan fortfarande är tillgänglig.

    Första gången du kör verktyget kopieras data mängden. Den första körningen kan vara en stund. Det varar ofta längre än du vill för att ta data källan offline för dina affärs processer.

    Genom att spegla en källa till ett mål (som med **Robocopy/Mir** ) kan du köra verktyget igen på samma källa och mål. Körningen är mycket snabbare eftersom den bara behöver transportera käll ändringar som inträffar efter föregående körning. Om du kör ett kopierings verktyg på det här sättet kan det minska stillestånds tiden avsevärt.

I följande tabell klassificerar vi Microsoft-verktyg och deras aktuella lämplighet för Azure-fil resurser:

| Rekommenderas | Verktyg | Stöd för Azure-filresurser | Bevarande av fil åter givning |
| :-: | :-- | :---- | :---- |
|![Ja, rekommenderas](media/storage-files-migration-overview/circle-green-checkmark.png)| RoboCopy | Stöds. Azure-filresurser kan monteras som nätverks enheter. | Fullständig åter givning. * |
|![Ja, rekommenderas](media/storage-files-migration-overview/circle-green-checkmark.png)| Azure File Sync | Inbyggt i Azure-filresurser. | Fullständig åter givning. * |
|![Ja, rekommenderas](media/storage-files-migration-overview/circle-green-checkmark.png)| Tjänsten för lagringsmigrering | Stöds indirekt. Azure-filresurser kan monteras som nätverks enheter på SMS-mål servrar. | Fullständig åter givning. * |
|![Ja, rekommenderas](media/storage-files-migration-overview/circle-green-checkmark.png)| AzCopy, version 10,4 eller senare| Stöds. | Fullständig åter givning. * |
|![Ja, rekommenderas](media/storage-files-migration-overview/circle-green-checkmark.png)| Data Box | Stöds. | Dataservern har nu fullständigt stöd för metadata. [Data Box-enhet kan också användas i kombination med Azure File Sync](storage-sync-offline-data-transfer.md). |
|![Inte fullständigt rekommenderat](media/storage-files-migration-overview/triangle-yellow-exclamation.png)| Azure Storage Explorer version 1,14 | Stöds. | Kopierar inte ACL: er. Stöder tidsstämplar.  |
|![Rekommenderas inte](media/storage-files-migration-overview/circle-red-x.png)| Azure Data Factory | Stöds. | Kopierar inte metadata. |
|||||

*\* Fullständig åter givning: uppfyller eller överskrider Azures funktioner för fil delning.*

### <a name="migration-helper-tools"></a>Verktyg för migrerings hjälp

I det här avsnittet beskrivs verktyg som hjälper dig att planera och köra migreringar.

#### <a name="robocopy-from-microsoft-corporation"></a>RoboCopy från Microsoft Corporation

RoboCopy är ett av de verktyg som är mest tillämpliga för filmigrering. Den ingår i Windows. Den huvudsakliga [Robocopy-dokumentationen](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy) är en användbar resurs för det här verktygets många alternativ.

#### <a name="treesize-from-jam-software-gmbh"></a>TreeSize från sylt Software GmbH

Azure File Sync skalas i första hand med antalet objekt (filer och mappar) och inte med den totala lagrings mängden. Med verktyget TreeSize kan du bestämma antalet objekt på dina Windows Server-volymer.

Du kan använda verktyget för att skapa ett perspektiv innan en [Azure File Sync distribution](storage-sync-files-deployment-guide.md). Du kan också använda den när du har distribuerat moln nivå. I det scenariot ser du antalet objekt och vilka kataloger som använder serverns cache.

Den testade versionen av verktyget är version 4.4.1. Den är kompatibel med filer i molnet. Verktyget kommer inte att orsaka återställning av nivåbaserade filer under normal drift.

## <a name="next-steps"></a>Nästa steg

1. Skapa en plan för vilken distribution av Azure-filresurser (endast moln eller hybrid) du vill använda.
1. Gå igenom listan med tillgängliga stöd linjer för migrering för att hitta den detaljerade guiden som matchar din källa och distribution av Azure-filresurser.

Här är mer information om Azure Files tekniker som nämns i den här artikeln:

* [Översikt över Azure-filresurs](storage-files-introduction.md)
* [Planera för distribution av Azure File Sync](storage-sync-files-planning.md)
* [Azure File Sync: moln nivåer](storage-sync-cloud-tiering.md)
