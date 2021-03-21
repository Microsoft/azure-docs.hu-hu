---
author: cherylmc
ms.date: 12/06/2019
ms.service: expressroute
ms.topic: include
ms.author: cherylmc
ms.openlocfilehash: f04861d55c9cea3c79f4983f7be2e1f5a3c6d864
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018159"
---
Telepítse az Azure Service Management (SM) PowerShell-modulok és a ExpressRoute modul legújabb verzióit. Az Azure Cloudshellben-környezet nem használható SM-modulok futtatására.

1. Az Azure Service Management modul telepítéséhez kövesse a [Service Management modul telepítése](/powershell/azure/servicemanagement/install-azure-ps) című cikk utasításait. Ha az az vagy az RM modul már telepítve van, ne felejtse el használni a "-AllowClobber".
2. Importálja a telepített modulokat. A következő példa használata esetén módosítsa az elérési utat úgy, hogy az tükrözze a telepített PowerShell-modulok helyét és verzióját.

   ```powershell
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\Azure.psd1'
   Import-Module 'C:\Program Files\WindowsPowerShell\Modules\Azure\5.3.0\ExpressRoute\ExpressRoute.psd1'
   ```
3. Az Azure-fiókba való bejelentkezéshez nyissa meg a PowerShell-konzolt emelt szintű jogokkal, és kapcsolódjon a fiókjához. A következő példa segítségével csatlakozhat a Service Management modul használatával:

   ```powershell
   Add-AzureAccount
   ```