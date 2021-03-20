---
title: Windows rendszerű virtuális asztali alkalmazáskészlet törlése – Azure
description: Gazdagép-készlet törlése a Windows Virtual Desktopban.
author: Heidilohr
ms.topic: how-to
ms.date: 07/11/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: dfc9858bea468389d8ce90677f048e5d1fd3bb82
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88007590"
---
# <a name="delete-a-host-pool"></a>Gazdagépkészlet törlése

A Windows virtuális asztal szolgáltatásban létrehozott összes gazdagép a munkamenet-gazdagépekhez és az alkalmazás-csoportokhoz van csatolva. A gazdagépek törléséhez törölnie kell a hozzá tartozó alkalmazás-csoportokat és munkamenet-gazdagépeket. Az alkalmazás-csoport törlése meglehetősen egyszerű, de a munkamenet-gazdagépek törlése bonyolultabb. Amikor töröl egy munkamenet-gazdagépet, meg kell győződnie arról, hogy nincs aktív felhasználói munkamenete. A munkamenet-gazdagép összes felhasználói munkamenetét ki kell jelentkeznie, hogy a felhasználók ne veszítsék el az adatvesztést.

## <a name="delete-a-host-pool-with-powershell"></a>Gazdagép törlése a PowerShell-lel

Ha egy gazdagépet a PowerShell használatával szeretne törölni, először törölnie kell a gazdagép összes alkalmazás-csoportját. Az összes alkalmazáscsoport törléséhez futtassa a következő PowerShell-parancsmagot:

```powershell
Remove-AzWvdApplicationGroup -Name <appgroupname> -ResourceGroupName <resourcegroupname>
```

Ezután futtassa ezt a parancsmagot a következő alkalmazáskészlet törléséhez:

```powershell
Remove-AzWvdHostPool -Name <hostpoolname> -ResourceGroupName <resourcegroupname> -Force:$true
```

Ez a parancsmag eltávolítja az összes meglévő felhasználói munkamenetet a gazdagép-készlet munkamenet-gazdagépén. Emellett megszünteti a munkamenet-gazdagép regisztrációját a gazdagép-készletből. Az előfizetésen belül minden kapcsolódó virtuális gép (VM) továbbra is létezik.

## <a name="delete-a-host-pool-with-the-azure-portal"></a>Gazdagép-készlet törlése a Azure Portal

Gazdagép-készlet törlése a Azure Portalban:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. Keresse meg és válassza ki a **Windows virtuális asztal** elemet.

3. A lap bal oldalán található menüben válassza a **gazdagép-készletek** lehetőséget, majd válassza ki a törölni kívánt gazdagép-készlet nevét.

4. Az oldal bal oldalán lévő menüben válassza az **alkalmazáscsoport** lehetőséget.

5. Válassza ki az összes alkalmazáscsoport elemet a törölni kívánt gazdagépen, majd válassza az **Eltávolítás** lehetőséget.

6. Miután eltávolította az alkalmazás-csoportokat, lépjen a lap bal oldalán található menüre, és válassza az **Áttekintés** lehetőséget.

7. Válassza az **Eltávolítás** lehetőséget.

8. Ha vannak olyan munkamenet-gazdagépek a készletben, amelyeket törölni szeretne, megjelenik egy üzenet, amely arra kéri, hogy folytassa a folytatást. Válassza az **Igen** lehetőséget.

9. Ekkor a Azure Portal eltávolítja az összes munkamenet-gazdagépet, és törli a gazdagép-készletet. A rendszer nem törli a munkamenet-gazdagéphez kapcsolódó virtuális gépeket, és az előfizetésében marad.

## <a name="next-steps"></a>Következő lépések

A gazdagépek létrehozásával kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Gazdagépcsoport létrehozása az Azure Portalon](create-host-pools-azure-marketplace.md)
- [Gazdagépcsoport létrehozása a PowerShell-lel](create-host-pools-powershell.md)

A gazdagép-készlet beállításainak konfigurálásával kapcsolatos további információkért tekintse meg a következő cikkeket:

- [Gazdagépek RDP protokoll tulajdonságainak testreszabása](customize-rdp-properties.md)
- [A Windows Virtual Desktop terheléselosztási módjának beállítása](configure-host-pool-load-balancing.md)
- [A személyes asztali címkészlet-hozzárendelés típusának konfigurálása](configure-host-pool-personal-desktop-assignment-type.md)