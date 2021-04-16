---
title: Ürítési Windows Virtual Desktop beállítása – Azure
description: Kiürítési mód konfigurálása és használata a Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 19ef7d520800ac703ed77dc0520e5b860306c4bd
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509079"
---
# <a name="set-drain-mode"></a>Kiürítési mód beállítása

A kiürítési mód elkülöníti a munkamenetgazdát, ha javításokat szeretne alkalmazni, és karbantartást szeretne tenni a felhasználói munkamenetek megszakítása nélkül. Ha el van különítve, a munkamenetgazda nem fogad el új felhasználói munkameneteket. A rendszer minden új kapcsolatot átirányít a következő elérhető munkamenetgazdára. A munkamenetgazda meglévő kapcsolatai addig működnek, amíg a felhasználó ki nem jelentkezik, vagy a rendszergazda be nem befejezi a munkamenetet. Ha a munkamenetgazda kiürítési módban van, a rendszergazdák távolról is csatlakozhatnak a kiszolgálóhoz anélkül, hogy végigmennek a Windows Virtual Desktop szolgáltatáson. Ezt a beállítást a készletbe készletett és a személyes asztali számítógépekre is alkalmazhatja.

## <a name="set-drain-mode-using-the-azure-portal"></a>Ürítési mód beállítása a Azure Portal

A kiürítési mód bekapcsolása a Azure Portal:

1. Nyissa meg Azure Portal, és nyissa meg az elkülöníteni kívánt gazdagépkészletet.

2. A navigációs menüben válassza a **Munkamenet-gazdagépek lehetőséget.**

3. Ezután válassza ki azokat a gazdagépeket, amelyek számára be szeretné kapcsolni a kiürítési módot, majd válassza **a Kiürítési mód bekapcsolása lehetőséget.**

4. A kiürítési mód kikapcsolhoz válassza ki azokat a gazdagépkészleteket, amelyek kiürítési üzemmódja be van kapcsolva, majd válassza a Kiürítési mód **kikapcsolása lehetőséget.**

## <a name="set-drain-mode-using-powershell"></a>Kiürítési mód beállítása a PowerShell használatával

A Kiürítési módot a PowerShellben az *AllowNewSessions* paraméterrel állíthatja be, amely az [Update-AzWvdSessionhost parancs](/powershell/module/az.desktopvirtualization/update-azwvdsessionhost?view=azps-5.8.0&preserve-view=true) része.

Futtassa ezt a parancsmagot a kiürítési mód engedélyezéséhez:

```powershell
Update-AzWvdSessionHost -ResourceGroupName <resourceGroupName> -HostPoolName <hostpoolname> -Name <hostname> -AllowNewSession:$False
```

Futtassa ezt a parancsmagot a kiürítési mód letiltásához:

```powershell
Update-AzWvdSessionHost -ResourceGroupName <resourceGroupName> -HostPoolName <hostpoolname> -Name <hostname> -AllowNewSession:$True
```

>[!IMPORTANT]
>Ezt a parancsot minden olyan munkamenetgazda esetén futtatnia kell, amelyre alkalmazza a beállítást.

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a Azure Portal-Windows Virtual Desktop, tekintse meg az [oktatóanyagokat.](create-host-pools-azure-marketplace.md) Ha már ismeri az alapokat, tekintse meg a Azure Portal egyéb funkcióit, például az [MSIX-alkalmazás](app-attach-azure-portal.md) csatolását és [Azure Advisor.](azure-advisor.md)

Ha a PowerShell-metódust használja, és szeretné látni, mire képes még a modul, tekintse meg A [PowerShell-modul](powershell-module.md) beállítása az Windows Virtual Desktop és a [PowerShell-referenciát.](/powershell/module/az.desktopvirtualization/)