---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/09/2018
ms.author: cynthn
ms.openlocfilehash: 994a7726adec07f2f6533d460d05469a0f3c7bf3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87103048"
---
| Resurs | Gräns |
| --- | --- |
| Virtuella datorer per moln tjänst <sup>1</sup> |50 |
| Slut punkter för ingångar per moln tjänst <sup>2</sup> |150 |

<sup>1</sup> virtuella datorer som har skapats med hjälp av den klassiska distributions modellen i stället för Azure Resource Manager lagras automatiskt i en moln tjänst. Du kan lägga till fler virtuella datorer i moln tjänsten för belastnings utjämning och tillgänglighet. 

<sup>2</sup> slut punkter för ingångar tillåter kommunikation till en virtuell dator utanför den virtuella datorns moln tjänst. Virtuella datorer i samma moln tjänst eller virtuella nätverk kan kommunicera automatiskt med varandra.  
