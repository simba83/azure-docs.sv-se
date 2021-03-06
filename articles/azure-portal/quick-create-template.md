---
title: Skapa en Azure Portal instrument panel med hjälp av en Azure Resource Manager mall
description: Lär dig hur du skapar en Azure Portal instrument panel med hjälp av en Azure Resource Manager mall.
author: mgblythe
ms.service: azure-portal
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mblythe
ms.date: 06/15/2020
ms.openlocfilehash: fd2d7006e0c13f7e652b4edd973a36f94f2e2265
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "85560490"
---
# <a name="quickstart-create-a-dashboard-in-the-azure-portal-by-using-an-arm-template"></a>Snabb start: skapa en instrument panel i Azure Portal med en ARM-mall

En instrument panel i Azure Portal är en fokuserad och ordnad vy av dina moln resurser. Den här snabb starten fokuserar på processen att distribuera en Azure Resource Manager-mall (ARM-mall) för att skapa en instrument panel. Instrument panelen visar prestanda för en virtuell dator (VM) samt en statisk information och länkar.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-portal-dashboard%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.
- En befintlig virtuell dator.

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Den instrument panel som du skapar i nästa del av den här snabb starten kräver en befintlig virtuell dator. Skapa en virtuell dator genom att följa dessa steg.

1. I Azure Portal väljer du Cloud Shell.

    ![Välj Cloud Shell från menyfliksområdet Azure Portal](media/quick-create-template/cloud-shell.png)

1. Kopiera följande kommando och ange det i kommando tolken för att skapa en resurs grupp.

    ```powershell
    New-AzResourceGroup -Name SimpleWinVmResourceGroup -Location EastUS
    ```

    ![Kopiera ett kommando till kommando tolken](media/quick-create-template/command-prompt.png)

1. Kopiera följande kommando och ange det i kommando tolken för att skapa en virtuell dator i resurs gruppen.

    ```powershell
    New-AzVm `
        -ResourceGroupName "SimpleWinVmResourceGroup" `
        -Name "SimpleWinVm" `
        -Location "East US" 
    ```

1. Ange ett användar namn och lösen ord för den virtuella datorn. Detta är ett nytt användar namn och lösen ord. Det är till exempel inte det konto som du använder för att logga in på Azure. Mer information finns i [användar namn krav](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm) och [lösen ords krav](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

    Distributionen av virtuella datorer startar nu och tar vanligt vis några minuter att slutföra. När distributionen är klar går du vidare till nästa avsnitt.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-azure-portal-dashboard/). Mallen för den här artikeln är för lång för att kunna visas här. Om du vill visa mallen går du till [azuredeploy.jspå](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-azure-portal-dashboard/azuredeploy.json). En Azure-resurs definieras i mallen, [Microsoft. Portal/instrument paneler](/azure/templates/microsoft.portal/dashboards) – skapa en instrument panel i Azure Portal.

## <a name="deploy-the-template"></a>Distribuera mallen

1. Välj följande bild för att logga in på Azure och öppna en mall.

    [![Distribuera till Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azure-portal-dashboard%2Fazuredeploy.json)

1. Välj eller ange följande värden och välj sedan **Granska + skapa**.

    ![ARM-mall, skapa instrument panel, distribuera Portal](media/quick-create-template/create-dashboard-using-template-portal.png)

    Om den inte anges använder du standardvärdena för att skapa instrument panelen.

    * **Prenumeration**: välj en Azure-prenumeration.
    * **Resurs grupp**: Välj **SimpleWinVmResourceGroup**.
    * **Plats**: Välj **USA, östra**.
    * **Namn på virtuell dator**: ange **SimpleWinVm**.
    * **Resurs grupp för virtuell dator**: ange **SimpleWinVmResourceGroup**.

1. Välj **skapa** eller **köp**. När instrument panelen har distribuerats visas ett meddelande:

    ![ARM-mall, skapa instrument panel, distribuera Portal meddelande](media/quick-create-template/resource-manager-template-portal-deployment-notification.png)

Azure Portal användes för att distribuera mallen. Utöver Azure-portalen kan du också använda Azure PowerShell, Azure CLI, and REST API. Mer information om andra distributions metoder finns i [distribuera mallar](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

Kontrol lera att instrument panelen har skapats och att du kan se data från den virtuella datorn.

1. Öppna Azure-portalen och välj **Instrumentpanel**.

    ![Azure Portal navigering till instrument panelen](media/quick-create-template/navigate-to-dashboards.png)

1. På instrument panels sidan väljer du **enkel instrument panel för virtuell dator**.

    ![Navigera till en enkel instrument panel för virtuell dator](media/quick-create-template/select-simple-vm-dashboard.png)

1. Granska den instrument panel som ARM-mallen skapade. Du kan se att en del av innehållet är statiskt, men det finns även diagram som visar prestanda för den virtuella dator som du skapade i början.

    ![Granska enkel instrument panel för virtuell dator](media/quick-create-template/review-simple-vm-dashboard.png)

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort den virtuella datorn och tillhör ande instrument panel tar du bort resurs gruppen som innehåller dem.

1. Sök efter **SimpleWinVmResourceGroup**i Azure Portal och välj den sedan i Sök resultaten.

1. På sidan **SimpleWinVmResourceGroup** väljer du **ta bort resurs grupp**, anger namnet på den resurs grupp som ska bekräftas och väljer sedan **ta bort**.

    ![Ta bort resursgrupp](media/quick-create-template/delete-resource-group.png)

## <a name="next-steps"></a>Nästa steg

Mer information om instrument paneler i Azure Portal finns i:

> [!div class="nextstepaction"]
> [Skapa och dela instrumentpaneler i Azure-portalen](azure-portal-dashboards.md)