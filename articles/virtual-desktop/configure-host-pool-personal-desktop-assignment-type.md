---
title: Windows rendszerű virtuális asztali személyes asztal hozzárendelésének típusa – Azure
description: A hozzárendelés típusának konfigurálása a Windows rendszerű virtuális asztali személyes asztali készlethez.
services: virtual-desktop
author: HeidiLohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: helohr
ms.openlocfilehash: 8065be841b18fa0f8706a3bea3739ee2cb013323
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2020
ms.locfileid: "77367585"
---
# <a name="configure-the-personal-desktop-host-pool-assignment-type"></a>A személyes asztali címkészlet-hozzárendelés típusának konfigurálása

A személyes asztali címkészlet hozzárendelési típusának konfigurálásával beállíthatja a Windows rendszerű virtuális asztali környezetét, hogy jobban megfeleljen az igényeinek. Ebben a témakörben bemutatjuk, hogyan konfigurálhatja az automatikus vagy közvetlen hozzárendelést a felhasználók számára.

>[!NOTE]
> A cikkben szereplő utasítások csak a személyes asztali gazdagépekre érvényesek, nem készletezett gazdagépek, mivel a készletezett gazdagépek felhasználói nem vannak hozzárendelve adott munkamenet-gazdagépekhez.

## <a name="configure-automatic-assignment"></a>Automatikus hozzárendelés konfigurálása

Az automatikus hozzárendelés az alapértelmezett hozzárendelési típus a Windows rendszerű virtuális asztali környezetben létrehozott új személyes asztali gazdagépek számára. A felhasználók automatikus hozzárendeléséhez nincs szükség egy adott munkamenet-gazdagépre.

A felhasználók automatikus hozzárendeléséhez először rendelje hozzá azokat a személyes asztali gazdagéphez, hogy azok a hírcsatornában lássák az asztalt. Amikor egy hozzárendelt felhasználó elindítja az asztalt a hírcsatornájában, a rendszer egy rendelkezésre álló munkamenet-gazdagépet igényel, ha még nem csatlakoztak a gazdagéphez, amely befejezi a hozzárendelési folyamatot.

Mielőtt elkezdené, [töltse le és importálja a Windows rendszerű virtuális asztali PowerShell-modult](/powershell/windows-virtual-desktop/overview/) , ha még nem tette meg. 

> [!NOTE]
> Az utasítások követése előtt győződjön meg arról, hogy telepítette a Windows rendszerű virtuális asztali PowerShell-modul 1.0.1534.2001 vagy újabb verzióját.

Ezután futtassa a következő parancsmagot a fiókjába való bejelentkezéshez:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

A következő PowerShell-parancsmag futtatásával konfigurálhatja, hogy a gazdagépek automatikusan rendeljenek felhasználókat a virtuális gépekhez:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Automatic
```

A következő PowerShell-parancsmag futtatásával rendelhet hozzá felhasználót a személyes asztali gazdagéphez:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

## <a name="configure-direct-assignment"></a>Közvetlen hozzárendelés konfigurálása

Az automatikus hozzárendeléstől eltérően a közvetlen hozzárendelés használatakor a felhasználót a személyes asztali gazdagéphez és egy adott munkamenet-gazdagéphez is hozzá kell rendelnie, mielőtt csatlakozni tudnak a személyes asztalhoz. Ha a felhasználó csak a munkamenetgazda-hozzárendelés nélküli gazdagépekhez van hozzárendelve, nem férhetnek hozzá az erőforrásokhoz.

A következő PowerShell-parancsmag futtatásával konfigurálhatja a gazdagépeket, hogy a felhasználók közvetlen hozzárendelését igénylik a munkamenet-gazdagépekhez:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Direct
```

A következő PowerShell-parancsmag futtatásával rendelhet hozzá felhasználót a személyes asztali gazdagéphez:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Ha egy felhasználót egy adott munkamenet-gazdagéphez szeretne rendelni, futtassa a következő PowerShell-parancsmagot:

```powershell
Set-RdsSessionHost <tenantname> <hostpoolname> -Name <sessionhostname> -AssignedUser <userupn>
```

## <a name="next-steps"></a>Következő lépések

Most, hogy konfigurálta a személyes asztal hozzárendelési típusát, bejelentkezhet egy Windows rendszerű virtuális asztali ügyfélbe, és tesztelheti azt egy felhasználói munkamenet részeként. A következő két útmutató bemutatja, hogyan csatlakozhat egy munkamenethez az Ön által választott ügyfél használatával:

- [Kapcsolat a Windows asztali ügyféllel](connect-windows-7-and-10.md)
- [A webes ügyféllel való kapcsolat](connect-web.md)
