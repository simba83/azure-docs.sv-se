---
title: Miljövariabler för uppgiftens körtid
description: Vägledning för aktivitetens körnings miljö och referens för Azure Batch analys.
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: 6b8ade312146802ede6e12181a082a8fcd3842fe
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "85960919"
---
# <a name="azure-batch-runtime-environment-variables"></a>Variabler för Azure Batch körnings miljö

[Azure Batchs tjänsten](https://azure.microsoft.com/services/batch/) ställer in följande miljövariabler på datornoderna. Du kan referera till dessa miljövariabler i aktivitets kommando rader och i de program och skript som körs av kommando raderna.

Mer information om hur du använder miljövariabler med batch finns i [miljö inställningar för aktiviteter](./jobs-and-tasks.md#environment-settings-for-tasks).

## <a name="environment-variable-visibility"></a>Synlighet för miljö variabel

De här miljövariablerna visas bara i kontexten för **uppgifts användaren**, användar kontot på noden som en aktivitet körs under. Du ser dem *inte* om du [fjärransluter](./error-handling.md#connect-to-compute-nodes) till en beräkningsnod via RDP (Remote Desktop Protocol) eller SSH (Secure Shell) och visar en lista över miljövariablerna. Det beror på att användarkontot som används för fjärranslutning inte är samma som det konto som används av aktiviteten.

Om du vill hämta det aktuella värdet för en miljö variabel startar du `cmd.exe` på en Windows Compute-nod eller `/bin/sh` på en Linux-nod:

`cmd /c set <ENV_VARIABLE_NAME>`

`/bin/sh printenv <ENV_VARIABLE_NAME>`

## <a name="command-line-expansion-of-environment-variables"></a>Kommando rads expansion av miljövariabler

Kommando raderna som körs av aktiviteter på datornoderna körs inte under ett gränssnitt. Dessa kommando rader kan därför inte internt dra nytta av Shell-funktioner som t. ex. miljö variabel expansion (Detta inkluderar `PATH` ). Om du vill dra nytta av dessa funktioner måste du **anropa gränssnittet** på kommando raden. Starta till exempel `cmd.exe` på Windows Compute-noder eller `/bin/sh` på Linux-noder:

`cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

`/bin/sh -c MyTaskApplication $MY_ENV_VAR`

## <a name="environment-variables"></a>Miljövariabler

| Variabelnamn                     | Beskrivning                                                              | Tillgänglighet | Exempel |
|-----------------------------------|--------------------------------------------------------------------------|--------------|---------|
| AZ_BATCH_ACCOUNT_NAME           | Namnet på batch-kontot som aktiviteten tillhör.                  | Alla aktiviteter.   | mybatchaccount |
| AZ_BATCH_ACCOUNT_URL            | URL: en för batch-kontot. | Alla aktiviteter. | `https://myaccount.westus.batch.azure.com` |
| AZ_BATCH_APP_PACKAGE            | Ett prefix för alla miljövariabler för app-paket. Om till exempel program "FOO" version "1" installeras på en pool, är miljövariabeln AZ_BATCH_APP_PACKAGE_FOO_1 (i Linux) eller AZ_BATCH_APP_PACKAGE_FOO # 1 (i Windows). AZ_BATCH_APP_PACKAGE_FOO_1 pekar på den plats där paketet hämtades (en mapp). När du använder standard versionen av Appaketet använder du AZ_BATCH_APP_PACKAGE-miljövariabeln utan versions nummer. Om i Linux och programpaketets namn är "agent-linux-x64" och versionen är "1.1.46.0, är miljö namnet faktiskt: AZ_BATCH_APP_PACKAGE_agent_linux_x64_1_1_46_0, med under streck och gemener. Mer information finns [här](./batch-application-packages.md#execute-the-installed-applications). | Alla aktiviteter med tillhör ande appaket. Även tillgängligt för alla aktiviteter om själva noden har programpaket. | AZ_BATCH_APP_PACKAGE_FOO_1 (Linux) eller AZ_BATCH_APP_PACKAGE_FOO # 1 (Windows) |
| AZ_BATCH_AUTHENTICATION_TOKEN   | En autentiseringstoken som ger åtkomst till en begränsad uppsättning batch service-åtgärder. Den här miljövariabeln finns bara om [authenticationTokenSettings](/rest/api/batchservice/task/add#authenticationtokensettings) anges när [uppgiften läggs till](/rest/api/batchservice/task/add#request-body). Värdet för token används i batch-API: erna som autentiseringsuppgifter för att skapa en batch-klient, till exempel i [metoden batchclient. Open () .NET-API: et](/dotnet/api/microsoft.azure.batch.batchclient.open#Microsoft_Azure_Batch_BatchClient_Open_Microsoft_Azure_Batch_Auth_BatchTokenCredentials_). | Alla aktiviteter. | OAuth2-åtkomsttoken |
| AZ_BATCH_CERTIFICATES_DIR       | En katalog i [arbets katalogen för aktiviteter][files_dirs] där certifikat lagras för Linux Compute-noder. Denna miljö variabel gäller inte för Windows Compute-noder.                                                  | Alla aktiviteter.   |  /mnt/batch/tasks/workitems/batchjob001/job-1/task001/certs |
| AZ_BATCH_HOST_LIST              | Listan över noder som har tilldelats en [aktivitet med flera instanser][multi_instance] i formatet `nodeIP,nodeIP` . | Primär-och under aktiviteter med flera instanser. | `10.0.0.4,10.0.0.5` |
| AZ_BATCH_IS_CURRENT_NODE_MASTER | Anger om den aktuella noden är huvud-noden för en [aktivitet med flera instanser][multi_instance]. Möjliga värden är `true` och `false` .| Primär-och under aktiviteter med flera instanser. | `true` |
| AZ_BATCH_JOB_ID                 | ID:t för jobbet som aktiviteten hör till. | Alla aktiviteter förutom start aktivitet. | batchjob001 |
| AZ_BATCH_JOB_PREP_DIR           | Den fullständiga sökvägen till [aktivitets katalogen][files_dirs] för jobb förberedelser på noden. | Alla aktiviteter förutom uppgiften starta uppgift och jobb förberedelse. Endast tillgängligt om jobbet har kon figurer ATS med en jobb förberedelse aktivitet. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation |
| AZ_BATCH_JOB_PREP_WORKING_DIR   | Den fullständiga sökvägen till [arbets katalogen][files_dirs] för jobb förberedelse aktiviteten på noden. | Alla aktiviteter förutom uppgiften starta uppgift och jobb förberedelse. Endast tillgängligt om jobbet har kon figurer ATS med en jobb förberedelse aktivitet. | C:\user\tasks\workitems\jobprepreleasesamplejob\job-1\jobpreparation\wd |
| AZ_BATCH_MASTER_NODE            | IP-adressen och porten för den Compute-nod som den primära aktiviteten för en [multi-instance-aktivitet][multi_instance] körs på. Använd inte porten som anges här för MPI-eller NCCL-kommunikation – den är reserverad för Azure Batch tjänsten. Använd variabeln MASTER_PORT i stället, antingen genom att ställa in den med ett värde som skickas via kommando rads argumentet (port 6105 är ett bra standard val) eller med värdet AML anger om det gör det. | Primär-och under aktiviteter med flera instanser. | `10.0.0.4:6000` |
| AZ_BATCH_NODE_ID                | ID: t för noden som uppgiften är tilldelad till. | Alla aktiviteter. | TVM-1219235766_3-20160919t172711z |
| AZ_BATCH_NODE_IS_DEDICATED      | Om `true` är den aktuella noden en dedikerad nod. Om `false` är det en [nod med låg prioritet](batch-low-pri-vms.md). | Alla aktiviteter. | `true` |
| AZ_BATCH_NODE_LIST              | Listan över noder som har tilldelats en [aktivitet med flera instanser][multi_instance] i formatet `nodeIP;nodeIP` . | Primär-och under aktiviteter med flera instanser. | `10.0.0.4;10.0.0.5` |
| AZ_BATCH_NODE_MOUNTS_DIR        | Den fullständiga sökvägen till [fil systemets monterings](virtual-file-mount.md) plats där alla monterings kataloger finns. Windows-filresurser använder en enhets beteckning, så för Windows är monterings enheten en del av enheterna och enheterna.  |  Alla aktiviteter, inklusive start aktiviteten har åtkomst till användaren, eftersom användaren är medveten om monterings behörigheterna för den monterade katalogen. | I Ubuntu är till exempel platsen: `/mnt/batch/tasks/fsmounts` |
| AZ_BATCH_NODE_ROOT_DIR          | Den fullständiga sökvägen till roten för alla [batch-kataloger][files_dirs] på noden. | Alla aktiviteter. | C:\user\tasks |
| AZ_BATCH_NODE_SHARED_DIR        | Den fullständiga sökvägen till den [delade katalogen][files_dirs] på noden. Alla aktiviteter som körs på en nod har Läs-/Skriv behörighet till den här katalogen. Aktiviteter som körs på andra noder har inte fjärråtkomst till den här katalogen (den är inte en delad nätverks katalog). | Alla aktiviteter. | C:\user\tasks\shared |
| AZ_BATCH_NODE_STARTUP_DIR       | Den fullständiga sökvägen till [Start aktivitets katalogen][files_dirs] på noden. | Alla aktiviteter. | C:\user\tasks\startup |
| AZ_BATCH_POOL_ID                | ID:t för poolen som aktiviteten körs i. | Alla aktiviteter. | batchpool001 |
| AZ_BATCH_TASK_DIR               | Den fullständiga sökvägen till [aktivitets katalogen][files_dirs] på noden. Den här katalogen innehåller `stdout.txt` och `stderr.txt` för uppgiften och AZ_BATCH_TASK_WORKING_DIR. | Alla aktiviteter. | C:\user\tasks\workitems\batchjob001\job-1\task001 |
| AZ_BATCH_TASK_ID                | ID:t för den aktuella aktiviteten. | Alla aktiviteter förutom start aktivitet. | task001 |
| AZ_BATCH_TASK_SHARED_DIR | En katalog Sök väg som är identisk för den primära aktiviteten och alla under aktiviteter i en [aktivitet med flera instanser][multi_instance]. Sökvägen finns på varje nod där aktiviteten kör flera instanser körs, och är Läs-/skriv åtkomst till de aktivitets kommandon som körs på noden (både [koordinations kommandot][coord_cmd] och [program kommandot][app_cmd]). Under aktiviteter eller en primär aktivitet som körs på andra noder har inte fjärråtkomst till den här katalogen (den är inte en delad nätverks katalog). | Primär-och under aktiviteter med flera instanser. | C:\user\tasks\workitems\multiinstancesamplejob\job-1\multiinstancesampletask |
| AZ_BATCH_TASK_WORKING_DIR       | Den fullständiga sökvägen till [arbets katalogen för aktiviteten][files_dirs] på noden. Den pågående aktiviteten har Läs-/Skriv behörighet till den här katalogen. | Alla aktiviteter. | C:\user\tasks\workitems\batchjob001\job-1\task001\wd |
| CCP_NODES                       | Listan över noder och antalet kärnor per nod som har tilldelats en [aktivitet med flera instanser][multi_instance]. Noder och kärnor visas i formatet `numNodes<space>node1IP<space>node1Cores<space>`<br/>`node2IP<space>node2Cores<space> ...`, där antalet noder följs av en eller flera nod-IP-adresser och antalet kärnor för var och en. |  Primär-och under aktiviteter med flera instanser. |`2 10.0.0.4 1 10.0.0.5 1` |

[files_dirs]: ./files-and-directories.md
[multi_instance]: ./batch-mpi.md
[coord_cmd]: ./batch-mpi.md#coordination-command
[app_cmd]: ./batch-mpi.md#application-command
