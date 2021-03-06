---
title: Självstudie – webbappen får åtkomst Microsoft Graph som användaren | Azure
description: I den här självstudien får du lära dig hur du kommer åt data i Microsoft Graph för en inloggad användares räkning.
services: microsoft-graph, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/09/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.openlocfilehash: ef007f045a5c53bf70f6d042167c157ab3f4decc
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94428957"
---
# <a name="tutorial-access-microsoft-graph-from-a-secured-app-as-the-user"></a>Självstudie: åtkomst Microsoft Graph från en säker app som användare

Lär dig hur du kommer åt Microsoft Graph från en webbapp som körs på Azure App Service.

:::image type="content" alt-text="Åtkomst Microsoft Graph" source="./media/scenario-secure-app-access-microsoft-graph/web-app-access-graph.svg" border="false":::

Du vill lägga till åtkomst till Microsoft Graph från din webbapp och utföra vissa åtgärder som den inloggade användaren. I det här avsnittet beskrivs hur du tilldelar delegerade behörigheter till webbappen och hämtar den inloggade användarens profil information från Azure Active Directory.

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Bevilja delegerade behörigheter till en webbapp
> * Anropa Microsoft Graph från en webbapp åt en inloggad användare

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

* Ett webb program som körs på Azure App Service som har [modulen App Service autentisering/auktorisering aktive rad](scenario-secure-app-authentication-app-service.md).

## <a name="grant-front-end-access-to-call-microsoft-graph"></a>Bevilja klient åtkomst till anrop Microsoft Graph

Nu när du har aktiverat autentisering och auktorisering i din webbapp registreras webb programmet med Microsoft Identity Platform och backas upp av ett Azure AD-program. I det här steget ger du webbappen behörighet att komma åt Microsoft Graph för användarens räkning. (Tekniskt sett ger du webbappens Azure AD-program behörighet att komma åt Microsoft Graph AD-program för användarens räkning.)

I menyn [Azure Portal](https://portal.azure.com) väljer du **Azure Active Directory** eller söker efter och väljer Azure Active Directory från vilken sida som helst.

Välj **Appregistreringar**  >  **ägda program**  >  **Visa alla program i den här katalogen**. Välj ditt webb program namn och välj sedan **API-behörigheter**.

Välj **Lägg till en behörighet** och välj sedan Microsoft API: er och Microsoft Graph.

Välj **delegerade behörigheter** och välj **användare. Läs** från listan.  Klicka på **Lägg till behörigheter**.

## <a name="configure-app-service-to-return-a-usable-access-token"></a>Konfigurera App Service för att returnera en användbar åtkomsttoken

Webbappen har nu de behörigheter som krävs för att komma åt Microsoft Graph som den inloggade användaren. I det här steget konfigurerar du App Service autentisering och auktorisering för att ge dig en användbar åtkomsttoken för åtkomst till Microsoft Graph. I det här steget behöver du klient/app-ID för den underordnade tjänsten (Microsoft Graph). *00000003-0000-0000-C000-000000000000* är app-ID: t för Microsoft Graph.

> [!IMPORTANT]
> Om du inte konfigurerar App Service för att returnera en användbar åtkomsttoken visas ett ```CompactToken parsing failed with error code: 80049217``` fel meddelande när du anropar Microsoft Graph-API: er i koden.

Navigera till [Azure Resource Explorer](https://resources.azure.com/) och Använd resurs trädet och leta upp din webbapp.  Resurs-URL: en bör likna följande: `https://resources.azure.com/subscriptions/subscription-id/resourceGroups/SecureWebApp/providers/Microsoft.Web/sites/SecureWebApp20200915115914`

Azure Resource Explorer öppnas nu med din webbapp vald i resurs trädet. Överst på sidan klickar du på **Läs/skriv** för att aktivera redigeringen av Azure-resurserna.

Gå nedåt till **config** authsettings i den vänstra webbläsaren  >  **authsettings**.

I vyn **authsettings** (autentiseringsinställningar) klickar du på **Edit** (Redigera). Ange ```additionalLoginParams``` till följande JSON-sträng med det klient-ID som du kopierade.

```json
"additionalLoginParams": ["response_type=code id_token","resource=00000003-0000-0000-c000-000000000000"],
```

Spara inställningarna genom att klicka på **PUT** (Placera). Den här inställningen kan ta flera minuter att börja gälla.  Din webbapp har nu kon figurer ATS för att komma åt Microsoft Graph med en korrekt åtkomsttoken.  Om du inte gör det returnerar Microsoft Graph ett fel som säger att formatet för den komprimerade token är felaktigt.

## <a name="call-microsoft-graph-net"></a>Anrops Microsoft Graph (.NET)

Din webbapp har nu de behörigheter som krävs och lägger även till Microsoft Graph klient-ID: t i inloggnings parametrarna. Med hjälp av [Microsoft. Identity. Web-biblioteket](https://github.com/AzureAD/microsoft-identity-web/)hämtar webbappen en åtkomsttoken för autentisering med Microsoft Graph. I version 1.2.0 och senare integrerar Microsoft. Identity. Web Library med och kan köras tillsammans med modulen App Service autentisering/auktorisering.  Microsoft. Identity. Web upptäcker att webbappen finns i App Services och hämtar åtkomsttoken från modulen App Services autentisering/auktorisering.  Åtkomsttoken skickas sedan till autentiserade begär Anden med Microsoft Graph-API: et.

> [!NOTE]
> Microsoft. Identity. Web-biblioteket krävs inte i din webbapp för grundläggande autentisering/auktorisering eller för att autentisera begär Anden med Microsoft Graph.  Det är möjligt att [anropa underordnade API: er på ett säkert sätt](tutorial-auth-aad.md#call-api-securely-from-server-code) med endast modulen App Service autentisering/auktorisering aktive rad.  
> App Service autentisering/auktorisering är dock utformat för flera grundläggande autentiserings scenarier.  För mer komplexa scenarier (till exempel hantering av anpassade anspråk) behöver du Microsoft. Identity. Web Library eller [Microsoft Authentication Library](/azure/active-directory/develop/msal-overview). Det finns lite mer konfigurations-och konfigurations arbete i början, men Microsoft. Identity. Web-biblioteket kan köras tillsammans med modulen App Service autentisering/auktorisering.  Senare, när din webbapp behöver hantera mer komplexa scenarier, kan du inaktivera modulen App Service autentisering/auktorisering och Microsoft. identitet. Web är redan en del av din app.

### <a name="install-client-library-packages"></a>Installera klient biblioteks paket

Installera [Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web/) och [Microsoft. Graph](https://www.nuget.org/packages/Microsoft.Graph) NuGet-paketen i projektet med .net Core kommando rads gränssnitt eller Package Manager-konsolen i Visual Studio.

# <a name="command-line"></a>[Kommando rad](#tab/command-line)

Öppna en kommando rad och växla till den katalog som innehåller projekt filen.

Kör installations kommandona:

```dotnetcli
dotnet add package Microsoft.Graph

dotnet add package Microsoft.Identity.Web
```

# <a name="package-manager"></a>[Paket hanterare](#tab/package-manager)
Öppna-projektet/lösningen i Visual Studio och öppna konsolen med **verktyg**  >  **NuGet Package Manager**  >  **Package Manager Console** kommando.

Kör installations kommandona:
```powershell
Install-Package Microsoft.Graph

Install-Package Microsoft.Identity.Web
```

---

### <a name="startupcs"></a>Startup.cs

I *startup.cs* -filen ```AddMicrosoftIdentityWebApp``` lägger metoden till Microsoft. Identity. Web i din webbapp.  ```AddMicrosoftGraph```Metoden lägger till Microsoft Graph support.

```csharp
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Microsoft.Identity.Web;
using Microsoft.AspNetCore.Authentication.OpenIdConnect;

// Some code omitted for brevity.
public class Startup
{
    // This method gets called by the runtime. Use this method to add services to the container.
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddAuthentication(OpenIdConnectDefaults.AuthenticationScheme)
                .AddMicrosoftIdentityWebApp(Configuration.GetSection("AzureAd"))
                    .EnableTokenAcquisitionToCallDownstreamApi()
                        .AddMicrosoftGraph(Configuration.GetSection("Graph"))
                        .AddInMemoryTokenCaches();

        services.AddRazorPages();
    }
}

```

### <a name="appsettingsjson"></a>appsettings.json

*AzureAd* anger konfigurationen för biblioteket Microsoft. Identity. Web.  I [Azure Portal](https://portal.azure.com)väljer du **Azure Active Directory** på menyn Portal och väljer **Appregistreringar**. Välj den app-registrering som skapades när du aktiverade modulen App Service autentisering/auktorisering (appens registrering ska ha samma namn som din webbapp).  Du hittar klient-ID och klient-ID på översikts sidan för app-registrering.  Du hittar domän namnet på sidan Azure Active Directory översikt för din klient organisation.

*Graph* anger den Microsoft Graph slut punkten och de initiala omfattningarna som appen behöver.

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "fourthcoffeetest.onmicrosoft.com",
    "TenantId": "[tenant-id]",
    "ClientId": "[client-id]",
    // To call an API
    "ClientSecret": "[secret-from-portal]", // Not required by this scenario
    "CallbackPath": "/signin-oidc"
  },

  "Graph": {
    "BaseUrl": "https://graph.microsoft.com/v1.0",
    "Scopes": "user.read"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft": "Warning",
      "Microsoft.Hosting.Lifetime": "Information"
    }
  },
  "AllowedHosts": "*"
}
```

### <a name="indexcshtmlcs"></a>Index.cshtml.cs

I följande exempel visas hur du anropar Microsoft Graph som den inloggade användaren och får viss användar information.  ```GraphServiceClient```Objektet matas in i styrenheten och autentiseringen har kon figurer ATS åt dig av Microsoft. Identity. Web-biblioteket.

```csharp
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Microsoft.Graph;
using System.IO;
using Microsoft.Identity.Web;
using Microsoft.Extensions.Logging;

// Some code omitted for brevity.

[AuthorizeForScopes(Scopes = new[] { "user.read" })]
public class IndexModel : PageModel
{
    private readonly ILogger<IndexModel> _logger;
    private readonly GraphServiceClient _graphServiceClient;

    public IndexModel(ILogger<IndexModel> logger, GraphServiceClient graphServiceClient)
    {
        _logger = logger;
        _graphServiceClient = graphServiceClient;
    }

    public async Task OnGetAsync()
    {
        try
        {
            var user = await _graphServiceClient.Me.Request().GetAsync();
            ViewData["Me"] = user;
            ViewData["name"] = user.DisplayName;

            using (var photoStream = await _graphServiceClient.Me.Photo.Content.Request().GetAsync())
            {
                byte[] photoByte = ((MemoryStream)photoStream).ToArray();
                ViewData["photo"] = Convert.ToBase64String(photoByte);
            }
        }
        catch (Exception ex)
        {
            ViewData["photo"] = null;
        }
    }
}
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du är klar med den här själv studie kursen och inte längre behöver webbappen eller tillhör ande resurser [rensar du de resurser som du har skapat](scenario-secure-app-clean-up-resources.md).

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
>
> * Bevilja delegerade behörigheter till en webbapp
> * Anropa Microsoft Graph från en webbapp åt en inloggad användare

> [!div class="nextstepaction"]
> [App Service-åtkomster Microsoft Graph som appen](scenario-secure-app-access-microsoft-graph-as-app.md)
