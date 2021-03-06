---
title: Skapa en säkerhetsautomation för vissa säkerhets aviseringar med hjälp av en Azure Resource Manager mall (ARM-mall)
description: Lär dig hur du skapar en Azure Security Center Automation för att utlösa en Logic app, som kommer att utlösas av vissa Security Center aviseringar med hjälp av en Azure Resource Manager mall (ARM-mall).
services: azure-resource-manager
author: memildin
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: memildin
ms.date: 08/20/2020
ms.openlocfilehash: 12b7c86e528af6c174f456add4d29a92239cd01e
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92678015"
---
# <a name="quickstart-create-an-automatic-response-to-a-specific-security-alert-using-an-arm-template"></a>Snabb start: skapa ett automatiskt svar på en speciell säkerhets avisering med en ARM-mall

I den här snabb starten beskrivs hur du använder en Azure Resource Manager mall (ARM-mall) för att skapa en arbets flödes automatisering som utlöser en Logic app när vissa säkerhets aviseringar tas emot av Azure Security Center.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure** . Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-securitycenter-create-automation-for-alertnamecontains%2fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

En lista över de roller och behörigheter som krävs för att arbeta med Azure Security Center funktion för arbets flödes automatisering finns i [arbets flödes automatisering](workflow-automation.md).

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-securitycenter-create-automation-for-alertnamecontains/).

:::code language="json" source="~/quickstart-templates/101-securitycenter-create-automation-for-alertnamecontains/azuredeploy.json":::

### <a name="relevant-resources"></a>Relevanta resurser

- [**Microsoft. Security/automations**](/azure/templates/microsoft.security/automations): den automatisering som utlöser Logic-appen när en Azure Security Center-avisering som innehåller en angiven sträng tas emot.
- [**Microsoft. Logic/-arbets flöden**](/azure/templates/microsoft.logic/workflows): en tom, Utlös ande Logic-app.

För andra Security Center snabb starts mallar, se de här [communityn bidragit till mallar](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Security&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Distribuera mallen

- **PowerShell** :

  ```azurepowershell-interactive
  New-AzResourceGroup -Name <resource-group-name> -Location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  New-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-securitycenter-create-automation-for-alertnamecontains/azuredeploy.json
  ```

- **CLI** :

  ```azurecli-interactive
  az group create --name <resource-group-name> --location <resource-group-location> #use this command when you need to create a new resource group for your deployment
  az group deployment create --resource-group <my-resource-group> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-securitycenter-create-automation-for-alertnamecontains/azuredeploy.json
  ```

- **Portal** :

  [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-securitycenter-create-automation-for-alertnamecontains%2fazuredeploy.json)

  Du hittar mer information om det här distributions alternativet i [Använd en distributions knapp för att distribuera mallar från GitHub-lagringsplatsen](../azure-resource-manager/templates/deploy-to-azure-button.md).

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

Använd Azure Portal för att kontrol lera att arbets flödes automatiseringen har distribuerats.

1. Öppna **Security Center** från [Azure Portal](https://portal.azure.com).
1. Välj filter ikonen i det övre meny fältet och välj den prenumeration som du har distribuerat den nya automatiseringen av arbets flödet på.
1. Från Security Centers sid panelen öppnar du **arbets flödes automatisering** och söker efter din nya automatisering.
    :::image type="content" source="./media/quickstart-automation-alert/validating-template-run.png" alt-text="Lista över konfigurerade automations" lightbox="./media/quickstart-automation-alert/validating-template-run.png":::
    >[!TIP]
    > Om du har många arbets flödes automatiseringar i din prenumeration använder du alternativet **Filtrera efter namn** .

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver kan du ta bort arbets flödes automatiseringen med hjälp av Azure Portal.

1. Öppna **Security Center** från [Azure Portal](https://portal.azure.com).
1. Välj filter ikonen i det övre meny fältet och välj den prenumeration som du har distribuerat den nya automatiseringen av arbets flödet på.
1. Från Security Centers sid panelen öppnar du **arbets flödes automatisering** och söker efter den automatisering som ska tas bort.
    :::image type="content" source="./media/quickstart-automation-alert/deleting-workflow-automation.png" alt-text="Lista över konfigurerade automations" lightbox="./media/quickstart-automation-alert/deleting-workflow-automation.png":::
1. Markera kryss rutan för det objekt som ska tas bort.
1. Välj **ta bort** i verktygsfältet.

## <a name="next-steps"></a>Nästa steg

En stegvis själv studie kurs som vägleder dig genom processen för att skapa en mall finns i:

> [!div class="nextstepaction"]
> [Självstudie: skapa och distribuera din första ARM-mall](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
