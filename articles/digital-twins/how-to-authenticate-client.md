---
title: Skriva appautentiseringskod
titleSuffix: Azure Digital Twins
description: Se så här skriver du en autentiseringsnyckel i ett klient program
author: baanders
ms.author: baanders
ms.date: 10/7/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: bf7b829d70af27850affe619d47ed4a4f5ec1bea
ms.sourcegitcommit: 58f12c358a1358aa363ec1792f97dae4ac96cc4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2020
ms.locfileid: "93279919"
---
# <a name="write-client-app-authentication-code"></a>Skriv kod för klientautentisering för klient program

När du [har konfigurerat en Azure Digital-instans och-autentisering](how-to-set-up-instance-portal.md)kan du skapa ett klient program som du använder för att interagera med instansen. När du har konfigurerat ett start klient projekt måste du **skriva kod i den klient appen för att autentisera den** mot Azure Digitals-instansen.

Azure Digitals dubblare utför autentisering med [Azure AD-säkerhetstoken baserat på OAUTH 2,0](../active-directory/develop/security-tokens.md#json-web-tokens-jwts-and-claims). För att kunna autentisera ditt SDK måste du skaffa en Bearer-token med rätt behörigheter till digitala Azure-meddelanden och skicka dem tillsammans med dina API-anrop. 

I den här artikeln beskrivs hur du hämtar autentiseringsuppgifter med hjälp av `Azure.Identity` klient biblioteket. Även om den här artikeln visar kod exempel i C#, till exempel vad du skriver för [.net (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true), kan du använda en version av `Azure.Identity` oavsett vilken SDK du använder (mer information om SDK: er som är tillgängliga för Azure Digitals finns i [*How-to: använda Azures digitala dubbla API: er och SDK: er*](how-to-use-apis-sdks.md)).

## <a name="prerequisites"></a>Krav

Slutför först installations stegen i [*anvisningar: Konfigurera en instans och autentisering*](how-to-set-up-instance-portal.md). Detta säkerställer att du har en digital Azure-instans och att användaren har åtkomst behörighet. Efter den installationen är du redo att skriva kod för klient program.

Om du vill fortsätta måste du ha ett klient-app-projekt där du skriver koden. Om du inte redan har skapat ett klient-app-projekt skapar du ett Basic-projekt på det språk som du väljer att använda med den här självstudien.

## <a name="common-authentication-methods-with-azureidentity"></a>Vanliga autentiseringsmetoder med Azure. Identity

`Azure.Identity` är ett klient bibliotek som tillhandahåller flera olika autentiseringsuppgifter för att hämta metoder som du kan använda för att hämta en Bearer-token och autentisera med ditt SDK. Även om den här artikeln innehåller exempel i C#, kan du Visa `Azure.Identity` för flera språk, inklusive...

* [.NET (C#)](/dotnet/api/azure.identity?preserve-view=true&view=azure-dotnet)
* [Java](/java/api/overview/azure/identity-readme?preserve-view=true&view=azure-java-stable)
* [JavaScript](/javascript/api/overview/azure/identity-readme?preserve-view=true&view=azure-node-latest)
* [Python](/python/api/overview/azure/identity-readme?preserve-view=true&view=azure-python)

Tre vanliga autentiseringsuppgifter för att hämta metoder i `Azure.Identity` är:

* [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) tillhandahåller ett standard `TokenCredential` flöde för autentisering för program som ska distribueras till Azure och är **det rekommenderade alternativet för lokal utveckling**. Den kan också aktive ras för att prova de andra två metoderna som rekommenderas i den här artikeln. den radbryts `ManagedIdentityCredential` och kan komma åt `InteractiveBrowserCredential` med en konfigurations variabel.
* [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential?preserve-view=true&view=azure-dotnet) fungerar bra i de fall där du behöver [hanterade identiteter (MSI)](../active-directory/managed-identities-azure-resources/overview.md)och är en bra kandidat för att arbeta med Azure Functions och distribuera till Azure-tjänster.
* [InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential?preserve-view=true&view=azure-dotnet) är avsett för interaktiva program och kan användas för att skapa en autentiserad SDK-klient

I följande exempel visas hur du använder var och en av dessa med .NET (C#) SDK.

## <a name="authentication-examples-net-c-sdk"></a>Autentiserings exempel: .NET (C#) SDK

Det här avsnittet visar ett exempel i C# för att använda den tillhandahållna .NET SDK för att skriva autentiserings kod.

Börja med att inkludera SDK-paketet `Azure.DigitalTwins.Core` och `Azure.Identity` paketet i projektet. Beroende på vilka verktyg du väljer kan du inkludera paketen med hjälp av Visual Studio Package Manager eller `dotnet` kommando rads verktyget. 

Du måste också lägga till följande using-uttryck i projekt koden:

```csharp
using Azure.Identity;
using Azure.DigitalTwins.Core;
```

Lägg sedan till kod för att hämta autentiseringsuppgifter med någon av metoderna i `Azure.Identity` .

### <a name="defaultazurecredential-method"></a>DefaultAzureCredential-metod

[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) tillhandahåller ett standard `TokenCredential` flöde för autentisering för program som ska distribueras till Azure och är **det rekommenderade alternativet för lokal utveckling**.

Om du vill använda Azures standard inloggnings uppgifter behöver du Azure Digital-instansen URL ([instruktioner för att hitta](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).

Här är ett kod exempel för att lägga till ett `DefaultAzureCredential` i projektet:

```csharp
// The URL of your instance, starting with the protocol (https://)
private static string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-URL>";

//...

DigitalTwinsClient client;
try
{
    var credential = new DefaultAzureCredential();
    client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
} catch(Exception e)
{
    Console.WriteLine($"Authentication or client creation error: {e.Message}");
    Environment.Exit(0);
}
```

#### <a name="set-up-local-azure-credentials"></a>Konfigurera lokala autentiseringsuppgifter för Azure

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](../../includes/digital-twins-local-credentials-inner.md)]

### <a name="managedidentitycredential-method"></a>ManagedIdentityCredential-metod

Metoden [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential?preserve-view=true&view=azure-dotnet) fungerar bra i de fall där du behöver [hanterade identiteter (MSI)](../active-directory/managed-identities-azure-resources/overview.md), till exempel när du arbetar med Azure Functions.

Det innebär att du kan använda `ManagedIdentityCredential` i samma projekt som `DefaultAzureCredential` eller `InteractiveBrowserCredential` , för att autentisera en annan del av projektet.

Om du vill använda Azures standard inloggnings uppgifter behöver du Azure Digital-instansen URL ([instruktioner för att hitta](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)).

I en Azure-funktion kan du använda autentiseringsuppgifterna för hanterad identitet som detta:

```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
DigitalTwinsClientOptions opts = 
    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
```

### <a name="interactivebrowsercredential-method"></a>InteractiveBrowserCredential-metod

[InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential?preserve-view=true&view=azure-dotnet) -metoden är avsedd för interaktiva program och kommer att hämta en webbläsare för autentisering. Du kan använda det här i stället för `DefaultAzureCredential` i fall där du kräver interaktiv autentisering.

Om du vill använda autentiseringsuppgifter för den interaktiva webbläsaren behöver du en **registrerad app-registrering** som har behörighet till Azure Digitals dubbla API: er. Anvisningar för hur du konfigurerar den här appen finns i [*instruktion: skapa en app-registrering*](how-to-create-app-registration.md). När appens registrering har kon figurer ATS behöver du...
* appens registrerings *program (klient) ID* ( [instruktioner att söka efter](how-to-create-app-registration.md#collect-client-id-and-tenant-id))
* appens registrerings *katalog (klient)-ID* ( [instruktioner att hitta](how-to-create-app-registration.md#collect-client-id-and-tenant-id))
* Azure Digitals sammanflätade instanss URL ([instruktioner att hitta](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values))

Här är ett exempel på koden för att skapa en autentiserad SDK-klient med hjälp av `InteractiveBrowserCredential` .

```csharp
// Your client / app registration ID
private static string clientId = "<your-client-ID>"; 
// Your tenant / directory ID
private static string tenantId = "<your-tenant-ID>";
// The URL of your instance, starting with the protocol (https://)
private static string adtInstanceUrl = "https://<your-Azure-Digital-Twins-instance-URL>";

//...

DigitalTwinsClient client;
try
{
    var credential = new InteractiveBrowserCredential(tenantId, clientId);
    client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
} catch(Exception e)
{
    Console.WriteLine($"Authentication or client creation error: {e.Message}");
    Environment.Exit(0);
}
```

>[!NOTE]
> Även om du kan placera klient-ID, klient-ID och instans-URL direkt i koden som visas ovan, är det en bra idé att låta koden hämta dessa värden från en konfigurations fil eller en miljö variabel i stället.

#### <a name="other-notes-about-authenticating-azure-functions"></a>Andra kommentarer om att autentisera Azure Functions

Se [*anvisningar: Konfigurera en Azure-funktion för att bearbeta data*](how-to-create-azure-function.md) för ett mer komplett exempel som förklarar några av de viktiga konfigurations alternativen i kontexten för functions.

Om du vill använda autentisering i en funktion måste du också komma ihåg att:
* [Aktivera hanterad identitet](../app-service/overview-managed-identity.md?tabs=dotnet)
* Använd [miljövariabler](/sandbox/functions-recipes/environment-variables?tabs=csharp) efter behov
* Tilldela behörigheter till Functions-appen som möjliggör åtkomst till digitala dubbla API: er. Mer information om Azure Functions-processer finns i [*instruktion: Konfigurera en Azure-funktion för bearbetning av data*](how-to-create-azure-function.md).

## <a name="other-credential-methods"></a>Andra autentiseringsmetoder

Om de markerade autentiserings scenarierna ovan inte beskriver appens behov kan du utforska andra typer av autentisering som erbjuds i [**Microsoft Identity Platform**](../active-directory/develop/v2-overview.md#getting-started). Dokumentationen för den här plattformen omfattar ytterligare autentiserings scenarier, ordnade efter program typ.

## <a name="next-steps"></a>Nästa steg

Läs mer om hur säkerheten fungerar i Azure Digitals:
* [*Koncept: säkerhet för Azure Digitals dubbla lösningar*](concepts-security.md)

Eller, nu när autentiseringen har kon figurer ATS, går du vidare till att skapa modeller i din instans:
* [*Anvisningar: Hantera anpassade modeller*](how-to-manage-model.md)