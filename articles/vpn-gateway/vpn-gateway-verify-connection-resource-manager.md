---
title: 'Azure VPN Gateway: verifiera en gateway-anslutning'
description: Den här artikeln visar hur du verifierar ett virtuellt nätverk VPN Gateway anslutning.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/19/2020
ms.author: cherylmc
ms.custom: devx-track-azurecli
ms.openlocfilehash: fef9eb49b10008c86ee044a199ae69a43585f4f3
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/20/2020
ms.locfileid: "92217991"
---
# <a name="verify-a-vpn-gateway-connection"></a>Verifiera en VPN Gateway anslutning

Den här artikeln visar hur du verifierar en VPN gateway-anslutning för både den klassiska distributions modellen för och Resource Manager.

## <a name="azure-portal"></a>Azure Portal

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="powershell"></a>PowerShell

Om du vill verifiera en VPN gateway-anslutning för distributions modellen i Resource Manager med hjälp av PowerShell installerar du den senaste versionen av [PowerShell-cmdletarna för Azure Resource Manager](/powershell/azure/).

[!INCLUDE [PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="azure-cli"></a>Azure CLI

Om du vill verifiera en VPN gateway-anslutning för distributions modellen i Resource Manager med hjälp av Azure CLI installerar du den senaste versionen av [CLI-kommandona](https://docs.microsoft.com/cli/azure/install-azure-cli) (2,0 eller senare).

[!INCLUDE [CLI](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]

## <a name="azure-portal-classic"></a>Azure Portal (klassisk)

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

## <a name="powershell-classic"></a>PowerShell (klassisk)

Om du vill verifiera din VPN gateway-anslutning för den klassiska distributions modellen med PowerShell, installerar du de senaste versionerna av Azure PowerShell-cmdletarna. Se till att ladda ned och installera [Service Management-](/powershell/azure/servicemanagement/install-azure-ps?#azure-service-management-cmdlets) modulen. Använd Add-AzureAccount för att logga in på den klassiska distributions modellen.

[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>Nästa steg

* Du kan lägga till virtuella datorer till dina virtuella nätverk. Se [Skapa en virtuell dator](../virtual-machines/windows/quick-create-portal.md) för anvisningar.
