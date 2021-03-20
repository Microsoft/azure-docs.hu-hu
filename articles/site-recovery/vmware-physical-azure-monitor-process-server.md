---
title: A Azure Site Recovery folyamat kiszolgálójának figyelése
description: Ez a cikk bemutatja, hogyan figyelheti a VMware VM/fizikai kiszolgáló vész-helyreállításához használt Azure Site Recovery Process Servert
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: eebaa70cee99380ac67b8f6516a5b08ff2832c86
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86134744"
---
# <a name="monitor-the-process-server"></a>A folyamat kiszolgálójának figyelése

Ez a cikk a [site Recovery](site-recovery-overview.md) Process Server figyelését ismerteti.

- A Process Server a helyszíni VMware virtuális gépek és fizikai kiszolgálók Azure-ba való vész-helyreállításának beállításakor használatos.
- Alapértelmezés szerint a Process Server fut a konfigurációs kiszolgálón. Alapértelmezés szerint telepítve van a konfigurációs kiszolgáló telepítésekor.
- Ha nagyobb számú replikált gépet és nagyobb mennyiségű replikációs forgalmat szeretne méretezni és kezelni, további, kibővített folyamat-kiszolgálókat is telepíthet.

[További](vmware-physical-azure-config-process-server-overview.md) információ a folyamat-kiszolgálók szerepköreiről és központi telepítéséről.

## <a name="monitoring-overview"></a>Monitorozás áttekintése

Mivel a Process Server számos szerepkörrel rendelkezik, különösen a replikált adatgyorsítótárazásban, a tömörítésben és az Azure-ba való átvitelben, fontos a folyamat-kiszolgáló állapotának folyamatos figyelése.

Számos olyan helyzet áll fenn, amely gyakran befolyásolja a Process Server teljesítményét. A teljesítményt befolyásoló problémák a virtuális gép állapotára épülnek, és végül a folyamat-és a replikált gépek kritikus állapotba kerülnek. A helyzetek a következők:

- A nagy számú virtuális gép egy folyamat-kiszolgálót használ, amely közeledik vagy meghaladja az ajánlott korlátozásokat.
- A Process Servert használó virtuális gépek magas adatváltozási aránnyal rendelkeznek.
- A virtuális gépek és a folyamat-kiszolgáló közötti hálózati átviteli sebesség nem elegendő a replikációs adatoknak a Process Server rendszerbe való feltöltéséhez.
- A folyamat-kiszolgáló és az Azure közötti hálózati átviteli sebesség nem elegendő a replikációs adatok feltöltéséhez a Process Serverről az Azure-ba.

Ezen problémák mindegyike hatással lehet a virtuális gépek helyreállítási pontokra vonatkozó célkitűzésére (RPO). 

**Miért?** Mivel a virtuális gép helyreállítási pontjának generálása megköveteli, hogy a virtuális gép összes lemeze rendelkezzen közös ponttal. Ha az egyik lemez magas adatváltozási arányban van, a replikálás lassú, vagy a folyamat kiszolgálója nem optimális, akkor a helyreállítási pontok létrehozásának hatékonyságát is befolyásolja.

## <a name="monitor-proactively"></a>Proaktív monitorozás

A folyamat-kiszolgálóval kapcsolatos problémák elkerüléséhez fontos, hogy:

- A feldolgozási kiszolgálókkal kapcsolatos konkrét követelmények megértése a [kapacitás és a méretezési útmutató](site-recovery-plan-capacity-vmware.md#capacity-considerations)segítségével, valamint a folyamat-kiszolgálók üzembe helyezése és a javaslatok alapján történő futtatása.
- Figyelje a riasztásokat, és hárítsa el a hibákat, hogy a folyamat-kiszolgálók hatékonyan fussanak.


## <a name="process-server-alerts"></a>Kiszolgálói riasztások feldolgozása

A Process Server számos, az alábbi táblázatban összefoglalt állapot-riasztást hoz létre.

**Riasztástípus** | **Részletek**
--- | ---
![Kifogástalan][green] | A Process Server csatlakoztatva van és kifogástalan állapotú.
![Figyelmeztetés][yellow] | CPU-kihasználtság > 80% az elmúlt 15 percben
![Figyelmeztetés][yellow] | Memóriahasználat > 80% az elmúlt 15 percben
![Figyelmeztetés][yellow] | Gyorsítótár-mappa szabad területe < 30% az elmúlt 15 percben
![Figyelmeztetés][yellow] | A Site Recovery öt percenként figyeli a függőben lévő/kimenő adattípusokat, és becslést készít arról, hogy a folyamat-kiszolgáló gyorsítótárában lévő adatfrissítést nem lehet 30 percen belül feltölteni az Azure
![Figyelmeztetés][yellow] | A Process Server Services nem fut az elmúlt 15 percben
![Kritikus][red] | CPU-kihasználtság > 95% az elmúlt 15 percben
![Kritikus][red] | Memóriahasználat > 95% az elmúlt 15 percben
![Kritikus][red] | Gyorsítótár-mappa szabad területe < 25% az elmúlt 15 percben
![Kritikus][red] | A Site Recovery öt percenként figyeli a függőben lévő/kimenő adatlemezeket, és becslést készít arról, hogy a folyamat-kiszolgáló gyorsítótárában lévő adatfeltöltés 45 percen belül nem tölthető fel az Azure
![Kritikus][red] | 15 percig nem érhető el szívverés a folyamat-kiszolgálótól.

![Tábla kulcsa](./media/vmware-physical-azure-monitor-process-server/table-key.png)

> [!NOTE]
> A folyamat kiszolgálójának általános állapota a generált legrosszabb riasztáson alapul.



## <a name="monitor-process-server-health"></a>A folyamat kiszolgáló állapotának figyelése

A folyamat-kiszolgálók állapotát a következőképpen figyelheti: 

1. Egy replikált gép replikálási állapotának és állapotának figyeléséhez, valamint a folyamat kiszolgálójának a tárolóban > **replikált elemek** területen kattintson a figyelni kívánt gépre.
2. A **replikálás** állapota területen figyelheti a virtuális gép állapotának állapotát. A hiba részleteinek részletezéséhez kattintson az állapotra.

    ![Kiszolgáló állapotának feldolgozása a virtuális gép irányítópultján](./media/vmware-physical-azure-monitor-process-server/vm-ps-health.png)

4. A **Process Server Health** szolgáltatásban nyomon követheti a folyamat kiszolgálójának állapotát. Részletes részletezés.

    ![Kiszolgáló adatainak feldolgozása a virtuális gép irányítópultján](./media/vmware-physical-azure-monitor-process-server/ps-summary.png)

5. Az állapot a virtuális gép oldalán található grafikus ábrázolással is figyelhető.
    - A kibővített feldolgozási kiszolgáló a narancssárga színnel lesz kiemelve, ha figyelmeztetések vannak társítva, és piros, ha bármilyen kritikus probléma van. 
    - Ha a Process Server az alapértelmezett központi telepítésben fut a konfigurációs kiszolgálón, a konfigurációs kiszolgáló ennek megfelelően lesz kiemelve.
    - A részletezéshez kattintson a konfigurációs kiszolgálóra vagy a Process Server elemre. Jegyezze fel a problémákat és az esetleges szervizelési javaslatokat.

A **site Recovery infrastruktúra** alatt figyelheti a tárolóban lévő folyamat-kiszolgálókat is. A **site Recovery-infrastruktúra kezelése** területen kattintson a **konfigurációs kiszolgálók** elemre. Válassza ki a Process Serverhez társított konfigurációs kiszolgálót, és bontsa ki a folyamat kiszolgálójának adatait.


## <a name="next-steps"></a>Következő lépések

- Ha problémái vannak a folyamatok kiszolgálóival, kövesse a [hibaelhárítási útmutatót](vmware-physical-azure-troubleshoot-process-server.md)
- Ha további segítségre van szüksége, tegye fel kérdéseit a [Microsoft Q&a Azure site Recovery kérdését](/answers/topics/azure-site-recovery.html). 

[green]: ./media/vmware-physical-azure-monitor-process-server/green.png
[yellow]: ./media/vmware-physical-azure-monitor-process-server/yellow.png
[red]: ./media/vmware-physical-azure-monitor-process-server/red.png
