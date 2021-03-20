---
title: A Windows rendszerű virtuális asztali terheléselosztás konfigurálása – Azure
description: A terheléselosztási módszer konfigurálása Windows rendszerű virtuális asztali környezetekhez.
author: Heidilohr
ms.topic: how-to
ms.date: 10/12/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2c57ac10fbd318dd4bbb2dc86457e186dd824834
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91951655"
---
# <a name="configure-the-windows-virtual-desktop-load-balancing-method"></a>A Windows Virtual Desktop terheléselosztási módjának beállítása

A gazdagép terheléselosztási módszerének konfigurálása lehetővé teszi a Windows rendszerű virtuális asztali környezet beállítását, hogy jobban megfeleljen az igényeinek.

>[!NOTE]
> Ez nem vonatkozik egy állandó asztali gazdagépre, mert a felhasználók mindig rendelkeznek 1:1-hozzárendeléssel a gazdagépen belüli munkamenet-gazdagéphez.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk azt feltételezi, hogy követte a [Windows rendszerű virtuális asztali PowerShell-modul beállítása](powershell-module.md) a PowerShell-modul letöltéséhez és telepítéséhez, valamint az Azure-fiókba való bejelentkezéshez szükséges lépéseket.

## <a name="configure-breadth-first-load-balancing"></a>A szélesség beállítása – első terheléselosztás

Szélesség – az első terheléselosztás az új, nem állandó gazdagép-készletek alapértelmezett konfigurációja. Szélesség – az első terheléselosztás az új felhasználói munkameneteket az összes elérhető munkamenet-gazdagépen elosztja a gazdagépen. A szélesség – első terheléselosztás konfigurálásakor a gazdagép-készletben beállíthatja a munkamenetek maximális számát.

Ha úgy szeretné konfigurálni a gazdagépet, hogy a maximális munkamenet-korlát módosítása nélkül végezze el az első terheléselosztást, futtassa a következő PowerShell-parancsmagot:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'BreadthFirst'
```

Ezt követően, hogy meggyőződjön arról, hogy beállította a szélesség-első terheléselosztási módszert, futtassa a következő parancsmagot:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, LoadBalancerType

Name             : hostpoolname
LoadBalancerType : BreadthFirst
```

A következő PowerShell-parancsmag futtatásával konfigurálhat egy gazdagépet a szélesség – első terheléselosztás végrehajtásához és egy új maximális munkamenet-korlát használatához:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'BreadthFirst' -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>A mélység beállítása – első terheléselosztás

Mélység – az első terheléselosztás új felhasználói munkameneteket oszt ki egy rendelkezésre álló, a legnagyobb számú kapcsolattal rendelkező munkamenet-gazdagépre, de nem érte el a maximális munkamenet-korlátot.

>[!IMPORTANT]
>A mélység beállításakor – az első terheléselosztáshoz a gazdagép-készletben kell beállítani a munkamenetek maximális számát.

A következő PowerShell-parancsmag futtatásával állíthatja be, hogy a gazdagép mélysége – első terheléselosztást végezzen:

```powershell
Update-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> -LoadBalancerType 'DepthFirst' -MaxSessionLimit ###
```

>[!NOTE]
> A mélységi – az első terheléselosztási algoritmus a munkamenet-gazdagépek számára a maximális munkamenet-gazdagép korlátja () alapján osztja el a munkameneteket `-MaxSessionLimit` . Ez a paraméter alapértelmezett értéke a `999999` , amely egyben a legmagasabb lehetséges szám is, amely a változót beállíthatja. Ezt a paramétert akkor kell megadni, ha a mélység – első terheléselosztási algoritmust használja. A lehető legjobb felhasználói élmény érdekében ügyeljen arra, hogy a maximális munkamenet-gazdagép-korlát paramétert egy olyan számra módosítsa, amely a legjobban megfelel a környezetének.

A beállítás frissítése előtt futtassa a következő parancsmagot:

```powershell
Get-AzWvdHostPool -ResourceGroupName <resourcegroupname> -Name <hostpoolname> | format-list Name, LoadBalancerType, MaxSessionLimit

Name             : hostpoolname
LoadBalancerType : DepthFirst
MaxSessionLimit  : 6
```

## <a name="configure-load-balancing-with-the-azure-portal"></a>Terheléselosztás konfigurálása a Azure Portal

A terheléselosztást a Azure Portal is konfigurálhatja.

Terheléselosztás konfigurálása:

1. Jelentkezzen be Azure Portal a következő címen: https://portal.azure.com .
2. Keresse meg és válassza ki a **Windows rendszerű virtuális asztali** szolgáltatások elemet.
3. A Windows rendszerű virtuális asztal lapon válassza a **gazdagép készletek** elemet.
4. Válassza ki a szerkeszteni kívánt gazdagép-készlet nevét.
5. Válassza ki a **Tulajdonságok** elemet.
6. Adja meg a **maximális munkamenet-korlátot** a mezőbe, és válassza ki a használni kívánt **terheléselosztási algoritmust** a legördülő menüben.
7. Kattintson a **Mentés** gombra. Ez a terheléselosztás új beállításait alkalmazza.