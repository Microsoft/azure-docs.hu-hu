---
title: Windows rendszerű virtuális asztali személyes asztal hozzárendelésének típusa – Azure
description: Automatikus vagy közvetlen hozzárendelés konfigurálása egy Windows rendszerű virtuális asztali személyes asztali készlethez.
author: Heidilohr
ms.topic: how-to
ms.date: 07/09/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 446dae3c064f5f23d35cb12b2b24bdfea9e27012
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88007811"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>A személyes asztali címkészlet-hozzárendelés típusának konfigurálása

>[!IMPORTANT]
>Ez a tartalom a Windows rendszerű virtuális asztali Azure Resource Manager Windows virtuális asztali objektumokkal vonatkozik. Ha a Windowsos virtuális asztalt (klasszikus) Azure Resource Manager objektumok nélkül használja, tekintse meg [ezt a cikket](./virtual-desktop-fall-2019/configure-host-pool-personal-desktop-assignment-type-2019.md).

A személyes asztali címkészlet hozzárendelési típusának konfigurálásával beállíthatja a Windows rendszerű virtuális asztali környezetét, hogy jobban megfeleljen az igényeinek. Ebben a témakörben bemutatjuk, hogyan konfigurálhatja az automatikus vagy közvetlen hozzárendelést a felhasználók számára.

>[!NOTE]
> A cikkben szereplő utasítások csak a személyes asztali gazdagépekre érvényesek, nem készletezett gazdagépek, mivel a készletezett gazdagépek felhasználói nem vannak hozzárendelve adott munkamenet-gazdagépekhez.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy már letöltötte és telepítette a Windows rendszerű virtuális asztali PowerShell-modult. Ha még nem tette meg, kövesse a [PowerShell-modul beállítása](powershell-module.md)című témakör utasításait.

## <a name="configure-automatic-assignment"></a>Automatikus hozzárendelés konfigurálása

Az automatikus hozzárendelés az alapértelmezett hozzárendelési típus a Windows rendszerű virtuális asztali környezetben létrehozott új személyes asztali gazdagépek számára. A felhasználók automatikus hozzárendeléséhez nincs szükség egy adott munkamenet-gazdagépre.

A felhasználók automatikus hozzárendeléséhez először rendelje hozzá azokat a személyes asztali gazdagéphez, hogy azok a hírcsatornában lássák az asztalt. Amikor egy hozzárendelt felhasználó elindítja az asztalt a hírcsatornájában, a rendszer egy rendelkezésre álló munkamenet-gazdagépet igényel, ha még nem csatlakoztak a gazdagéphez, amely befejezi a hozzárendelési folyamatot.

A következő PowerShell-parancsmag futtatásával konfigurálhatja, hogy a gazdagépek automatikusan rendeljenek felhasználókat a virtuális gépekhez:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -PersonalDesktopAssignmentType Automatic
```

A következő PowerShell-parancsmag futtatásával rendelhet hozzá felhasználót a személyes asztali gazdagéphez:

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

## <a name="configure-direct-assignment"></a>Közvetlen hozzárendelés konfigurálása

Az automatikus hozzárendeléstől eltérően a közvetlen hozzárendelés használatakor a felhasználót a személyes asztali gazdagéphez és egy adott munkamenet-gazdagéphez is hozzá kell rendelnie, mielőtt csatlakozni tudnak a személyes asztalhoz. Ha a felhasználó csak a munkamenetgazda-hozzárendelés nélküli gazdagépekhez van hozzárendelve, nem férhetnek hozzá az erőforrásokhoz.

A következő PowerShell-parancsmag futtatásával konfigurálhatja a gazdagépeket, hogy a felhasználók közvetlen hozzárendelését igénylik a munkamenet-gazdagépekhez:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -PersonalDesktopAssignmentType Direct
```

A következő PowerShell-parancsmag futtatásával rendelhet hozzá felhasználót a személyes asztali gazdagéphez:

```powershell
New-AzRoleAssignment -SignInName <userupn> -RoleDefinitionName "Desktop Virtualization User" -ResourceName <appgroupname> -ResourceGroupName <resourcegroupname> -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups'
```

Ha egy felhasználót egy adott munkamenet-gazdagéphez szeretne rendelni, futtassa a következő PowerShell-parancsmagot:

```powershell
Update-AzWvdSessionHost -HostPoolName <hostpoolname> -Name <sessionhostname> -ResourceGroupName <resourcegroupname> -AssignedUser <userupn>
```

Ha közvetlenül szeretne hozzárendelni egy felhasználót egy munkamenet-gazdagéphez a Azure Portalban:

1. Jelentkezzen be az Azure Portalra a <https://portal.azure.com> webhelyen.
2. Adja meg a **Windows rendszerű virtuális asztalt** a keresősávban.
3. A **szolgáltatások** területen válassza a **Windows virtuális asztal** elemet.
4. A Windows rendszerű virtuális asztal lapon lépjen az ablak bal oldalán található menüre, és válassza a **gazdagép-készletek** lehetőséget.
5. Válassza ki a frissíteni kívánt gazdagép-készlet nevét.
6. Ezután nyissa meg az ablak bal oldalán található menüt, és válassza az **alkalmazáscsoport** lehetőséget.
7. Válassza ki a szerkeszteni kívánt asztali alkalmazás-csoport nevét, majd válassza a **hozzárendelések** lehetőséget az ablak bal oldalán található menüben.
8. Válassza a **+ Hozzáadás** lehetőséget, majd válassza ki azokat a felhasználókat vagy felhasználói csoportokat, akik számára közzé szeretné tenni ezt az asztali alkalmazást.
9. Válassza az információs sávban a **virtuális gép kiosztása** lehetőséget, ha egy munkamenet-gazdagépet szeretne hozzárendelni egy felhasználóhoz.
10. Válassza ki azt a munkamenet-gazdagépet, amelyet hozzá szeretne rendelni a felhasználóhoz, majd válassza a **hozzárendelés** lehetőséget.
11. Válassza ki azt a felhasználót, akihez a munkamenet-gazdagépet hozzá szeretné rendelni az elérhető felhasználók listájából.
12. Ha elkészült, válassza a **kiválasztás** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Most, hogy konfigurálta a személyes asztal hozzárendelési típusát, bejelentkezhet egy Windows rendszerű virtuális asztali ügyfélbe, és tesztelheti azt egy felhasználói munkamenet részeként. A következő két útmutató bemutatja, hogyan csatlakozhat egy munkamenethez az Ön által választott ügyfél használatával:

- [Kapcsolódás a Windows asztali ügyféllel](connect-windows-7-10.md)
- [Kapcsolódás a webügyféllel](connect-web.md)
- [Kapcsolódás az Android-ügyféllel](connect-android.md)
- [Kapcsolódás az iOS-ügyfélhez](connect-ios.md)
- [Kapcsolódás a macOS-ügyfélhez](connect-macos.md)