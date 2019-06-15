---
title: Windows virtuális asztal előzetes terheléselosztási módszer konfigurálása – Azure
description: Hogyan lehet egy Windows virtuális asztali környezetben terheléselosztási módszer konfigurálása.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 0c4702dada17e759d89c33be99b3155f4b15ad9e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60328884"
---
# <a name="configure-the-windows-virtual-desktop-preview-load-balancing-method"></a>Windows virtuális asztal előzetes terheléselosztási módszer konfigurálása

A gazdagép-készletre vonatkozó terheléselosztási módszer konfigurálása segítségével állítsa be a Windows virtuális asztal előzetes verziójú környezet, hogy jobban megfeleljen az igényeinek.

>[!NOTE]
> Ez nem vonatkozik állandó asztali gazdagéphez címkészlethez, mert a felhasználók mindig rendelkezzenek 1:1 leképezés egy munkamenet-gazdagépre a gazdagépen készleten belül.

## <a name="configure-breadth-first-load-balancing"></a>Szélesség-és felhőközpontú terheléselosztás beállítása

Szélesség-és felhőközpontú load balancing szolgáltatás az alapértelmezett konfiguráció új állomás nem állandó készletek esetében. Új felhasználói munkamenetek a gazdagép-készlet összes elérhető munkamenet-gazdagépek közötti terheléselosztás szélesség-és felhőközpontú osztja el. Szélesség-és felhőközpontú terheléselosztási konfigurálásakor adhatja meg a maximális munkamenet időkorlátjának munkamenet gazdagépenként a gazdagép-készletben.

Először [letöltése és importálása a Windows virtuális asztal PowerShell-modul](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) használatához a PowerShell-munkamenetben, ha még nem tette.

Egy gazdagép erőforráskészlet tagjai végzik a maximális munkamenet korlát módosításával anélkül szélesség-és felhőközpontú terheléselosztási konfigurálásához futtassa a következő PowerShell-parancsmagot:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer
```

Szélesség-és felhőközpontú terheléselosztás és új maximális munkamenet használja a gazdagép készlet konfigurálásához futtassa a következő PowerShell-parancsmagot:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -BreadthFirstLoadBalancer -MaxSessionLimit ###
```

## <a name="configure-depth-first-load-balancing"></a>Várólistamélység-és felhőközpontú terheléselosztás beállítása

Terheléselosztás mélysége-és felhőközpontú osztja el egy elérhető munkamenetgazda kapcsolatok számát vesszük figyelembe az új felhasználói munkamenetek, de nem érte el a maximális munkamenet korlát küszöbértéket. Terheléselosztás mélysége-és felhőközpontú, konfigurálásakor, **kell** munkamenet gazdagépenként egy maximális munkamenet-korlátjának beállítása a gazdagép-készletben.

Egy gazdagép erőforráskészlet tagjai végzik a mélység-és felhőközpontú terheléselosztás konfigurálásához futtassa a következő PowerShell-parancsmagot:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -DepthFirstLoadBalancer -MaxSessionLimit ###
```
