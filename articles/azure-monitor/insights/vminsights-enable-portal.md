---
title: Aktivera Azure Monitor för en virtuell dator eller skalnings uppsättning för virtuell dator i Azure Portal
description: Lär dig hur du aktiverar Azure Monitor for VMs på en enskild virtuell Azure-dator eller skalnings uppsättning för virtuella datorer med hjälp av Azure Portal.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: ba6ae9262fb0e55c53e1b1421c075e555fae8d98
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87328368"
---
# <a name="enable-azure-monitor-for-single-virtual-machine-or-virtual-machine-scale-set-in-the-azure-portal"></a>Aktivera Azure Monitor för en virtuell dator eller skalnings uppsättning för virtuell dator i Azure Portal
I den här artikeln beskrivs hur du aktiverar Azure Monitor for VMs för en virtuell dator eller skalnings uppsättning för virtuella datorer med hjälp av Azure Portal. Den här proceduren kan användas för följande:

- Virtuell Azure-dator
- Skalnings uppsättning för virtuella Azure-datorer
- Hybrid virtuell dator som är ansluten till Azure-bågen

## <a name="prerequisites"></a>Förutsättningar

- [Skapa och konfigurera en Log Analytics-arbetsyta](vminsights-configure-workspace.md). Du kan också skapa en ny arbets yta under den här processen.
- Se [operativ system som stöds](vminsights-enable-overview.md#supported-operating-systems) för att säkerställa att operativ systemet för den virtuella datorn eller skalnings uppsättningen för virtuella datorer som du aktiverar stöds. 

## <a name="enable-azure-monitor-for-vms"></a>Aktivera Azure Monitor for VMs

Från Azure Portal väljer du **virtuella datorer**, **skalnings uppsättningar för virtuella**datorer eller **datorer – Azure-båge** och väljer en resurs i listan. I avsnittet **övervakning** på menyn väljer du **insikter** och **aktiverar**sedan. I följande exempel visas en virtuell Azure-dator, men menyn liknar skalnings uppsättningen för virtuella Azure-datorer eller Azure-bågen.

![Aktivera Azure Monitor for VMs för en virtuell dator](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

Om den virtuella datorn inte redan är ansluten till en Log Analytics arbets yta, uppmanas du att välja en. Om du inte redan har [skapat en arbets yta](../../azure-monitor/learn/quick-create-workspace.md)kan du välja ett standardvärde för den plats där den virtuella datorn eller skalnings uppsättningen för virtuella datorer distribueras i prenumerationen. Den här arbets ytan kommer att skapas och konfigureras om den inte redan finns. Om du väljer en befintlig arbets yta konfigureras den för Azure Monitor for VMs om den inte redan är det.

> [!NOTE]
> Om du väljer en arbets yta som inte tidigare har kon figurer ATS för Azure Monitor for VMs läggs hanterings paketet för *VMInsights* till i den här arbets ytan. Detta kommer att gälla för alla agenter som redan är anslutna till arbets ytan, oavsett om den är aktive rad för Azure Monitor for VMs. Prestanda data samlas in från de virtuella datorerna och lagras i tabellen *InsightsMetrics* .

![Välj arbetsyta](media/vminsights-configure-workspace/select-workspace.png)

Du får status meddelanden när konfigurationen utförs.

>[!NOTE]
>Om du använder en manuell uppgraderings modell för den virtuella datorns skalnings uppsättning uppgraderar du instanserna för att slutföra installationen. Du kan starta uppgraderingar från sidan **instanser** i avsnittet **Inställningar** .

![Aktivera Azure Monitor for VMs övervakning av distributions bearbetning](media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)



## <a name="next-steps"></a>Nästa steg

* Se [använda Azure Monitor for VMS kartan](vminsights-maps.md) för att Visa identifierade program beroenden. 
* Se [Visa Azure VM-prestanda](vminsights-performance.md) för att identifiera Flask halsar, övergripande användning och den virtuella datorns prestanda.
