---
author: MikeRayMSFT
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 11/25/2018
ms.author: mikeray
ms.openlocfilehash: 27da28073b01c6bc43868172d6c989d9518d8f53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95563580"
---
## <a name="start-your-powershell-session"></a>Indítsa el a PowerShell-munkamenetet
 

Futtassa a [**Kapcsolódás az Account**](/powershell/module/Az.Accounts/Connect-AzAccount) parancsmagot, és megjelenik egy bejelentkezési képernyő a hitelesítő adatok megadásához. Használja a hitelesítő adatokat, amelyeket az Azure Portalra való bejelentkezéshez használ.

```powershell
Connect-AzAccount
```

Ha több előfizetéssel rendelkezik, válassza ki a PowerShell-munkamenet által használandó előfizetést a [**set-AzContext**](/powershell/module/az.accounts/set-azcontext) parancsmag használatával. Ha szeretné megtekinteni, hogy az aktuális PowerShell-munkamenet milyen előfizetést használ, futtassa a [**Get-AzContext**](/powershell/module/az.accounts/get-azcontext)parancsot. Az összes előfizetés megtekintéséhez futtassa a [**Get-AzSubscription**](/powershell/module/az.accounts/get-azsubscription)parancsot.

```powershell
Set-AzContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'
```