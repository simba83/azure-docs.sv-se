---
title: Bygg och distribuera en Node.js Express-app till Azure Cloud Services
titleSuffix: Azure Cloud Services
description: Använd den här självstudien för att skapa ett nytt program med hjälp av Express-modulen, som innehåller ett MVC-ramverk för att skapa Node.js webb program.
services: cloud-services
documentationcenter: nodejs
author: tgore03
ms.service: cloud-services
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: tagore
ms.custom: devx-track-js
ms.openlocfilehash: 7ecaccf36988e94d0cb1114bd04c5d571d4fe86d
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2020
ms.locfileid: "92071871"
---
# <a name="build-and-deploy-a-nodejs-web-application-using-express-on-an-azure-cloud-services"></a>Bygg och distribuera ett Node.js-webbprogram med hjälp av Express på en Azure-Cloud Services

Node.js innehåller en minimal uppsättning funktioner i kärn körningen.
Utvecklare använder ofta moduler från tredje part för att tillhandahålla ytterligare funktioner när du utvecklar ett Node.js-program. I den här självstudien får du skapa ett nytt program med hjälp av [Express](https://github.com/expressjs/express) -modulen, som innehåller ett MVC-ramverk för att skapa Node.js webb program.

En skärm bild av det färdiga programmet är nedan:

![En webbläsare som visar Välkommen till Express i Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="create-a-cloud-service-project"></a>Skapa ett moln tjänst projekt
[!INCLUDE [install-dev-tools](../../includes/install-dev-tools.md)]

Utför följande steg för att skapa ett nytt Cloud Service-projekt med namnet "expressapp":

1. Från **Start-menyn** eller **Start skärmen**söker du efter **Windows PowerShell**. Högerklicka slutligen på **Windows PowerShell** och välj **Kör som administratör**.

    ![Azure PowerShell ikon](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)
2. Ändra kataloger till katalogen **c: \\ Node** och ange sedan följande kommandon för att skapa en ny lösning med namnet **expressapp** och en webbroll med namnet **WebRole1**:

   ```powershell
   PS C:\node> New-AzureServiceProject expressapp
   PS C:\Node\expressapp> Add-AzureNodeWebRole
   PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 Node 0.10.21
   ```

   > [!NOTE]
   > Som standard använder **Add-AzureNodeWebRole** en äldre version av Node.js. Satsen **set-AzureServiceProjectRole** ovan instruerar Azure att använda v 0.10.21 of Node.  Observera att parametrarna är Skift läges känsliga.  Du kan kontrol lera att rätt version av Node.js har marker ATS genom att kontrol lera egenskapen **enginess** i **WebRole1\package.jspå**.
>
>

## <a name="install-express"></a>Installera Express
1. Installera Express generatorn genom att utfärda följande kommando:

    ```powershell
    PS C:\node\expressapp> npm install express-generator -g
    ```

    Utdata från kommandot NPM bör likna resultatet nedan.

    ![Windows PowerShell visar utdata från kommandot NPM install Express.](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)
2. Ändra kataloger till katalogen **WebRole1** och skapa ett nytt program genom att använda kommandot Express:

    ```powershell
    PS C:\node\expressapp\WebRole1> express
    ```

    Du uppmanas att skriva över det tidigare programmet. Fortsätt genom att ange **y** eller **Ja** . Express genererar app.js-filen och en mappstruktur som används för att skapa ditt program.

    ![Utdata från kommandot Express](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)
3. Om du vill installera ytterligare beroenden som definierats i package.jsfilen anger du följande kommando:

    ```powershell
    PS C:\node\expressapp\WebRole1> npm install
    ```

   ![Utdata från kommandot NPM install](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)
4. Använd följande kommando för att kopiera **bin-/www-** filen till **server.js**. Detta är så att moln tjänsten kan hitta start punkten för det här programmet.

    ```powershell
    PS C:\node\expressapp\WebRole1> copy bin/www server.js
    ```

   När det här kommandot har slutförts bör du ha en **server.js** -fil i WebRole1-katalogen.
5. Ändra **server.js** för att ta bort ett av tecknen "." från följande rad.

    ```js
    var app = require('../app');
    ```

   När du har gjort den här ändringen bör raden visas på följande sätt.

    ```js
    var app = require('./app');
    ```

   Den här ändringen krävs eftersom vi flyttade filen (tidigare **bin/www**) till samma katalog som app-filen krävs. När du har gjort den här ändringen sparar du **server.js** -filen.
6. Använd följande kommando för att köra programmet i Azure-emulatorn:

    ```powershell
    PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch
    ```

    ![En webb sida som innehåller Välkommen till Express.](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## <a name="modifying-the-view"></a>Ändra vyn
Ändra nu vyn så att meddelandet "Välkommen till Express i Azure" visas.

1. Ange följande kommando för att öppna filen index. Jade:

    ```powershell
    PS C:\node\expressapp\WebRole1> notepad views/index.jade
    ```

   ![Innehållet i filen index. Jade.](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)

   Jade är standard visnings motorn som används av Express-program. Mer information om Jade-vyns motor finns i [http://jade-lang.com][http://jade-lang.com] .
2. Ändra den sista text raden genom att lägga till **i Azure**.

   ![Filen index. Jade, den sista raden läser: p Välkommen till \# {title} i Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)
3. Spara filen och avsluta Anteckningar.
4. Uppdatera webbläsaren så att du ser dina ändringar.

   ![Ett webbläsarfönster, sidan som innehåller Välkommen till Express i Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

När du har testat programmet använder du cmdleten **Stop-AzureEmulator** för att stoppa emulatorn.

## <a name="publishing-the-application-to-azure"></a>Publicera programmet till Azure
I fönstret Azure PowerShell använder du cmdleten **Publish-AzureServiceProject** för att distribuera programmet till en moln tjänst

```powershell
PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch
```

När distributions åtgärden har slutförts öppnas och visas webb sidan i webbläsaren.

![En webbläsare som visar Express-sidan. URL: en anger att den nu finns på Azure.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="next-steps"></a>Nästa steg
Mer information finns i [Node.js Developer Center](/azure/developer/javascript/).

[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Express]: https://expressjs.com/
[http://jade-lang.com]: http://jade-lang.com