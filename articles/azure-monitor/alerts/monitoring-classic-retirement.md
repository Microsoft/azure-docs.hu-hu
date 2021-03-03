---
title: A klasszikus riasztások & figyelésének frissítése Azure Monitor
description: A klasszikus figyelési szolgáltatások és funkciók kivonásának leírása, amely korábban a riasztások (klasszikus) alatt Azure Portal látható.
author: yanivlavi
services: azure-monitor
ms.topic: conceptual
ms.date: 02/14/2021
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: a862e54b5e1eddce5fcabf050eba1cfa1f103e30
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734757"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>Az egyesített riasztási & figyelése Azure Monitor helyettesíti a klasszikus riasztási & figyelését

A Azure Monitor egy egységes figyelési verem, amely az Azure-erőforrások egyetlen mérőszámát és egy riasztását támogatja. További információt ebben a [blogbejegyzésben](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/)talál. Az új Azure monitoring-és riasztási platformok gyorsabb, intelligensebb és bővíthetők, így folyamatosan bővülnek a Felhőbeli számítástechnika és a Microsoft intelligens felhőalapú filozófiája.

Az új Azure monitorozási és riasztási platformot használva a Azure Monitor klasszikus riasztások kimaradnak a nyilvános Felhőbeli felhasználók számára, bár a használat továbbra is korlátozott a **2021**-ig. A Azure Government Cloud és az Azure China 21Vianet klasszikus riasztásai a **2024. február 29**-én törlődnek.

 ![Klasszikus riasztás a Azure Portalban](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

Javasoljuk, hogy kezdje el és hozza létre újra a riasztásokat az új platformon.

> [!IMPORTANT]
> A tevékenység naplójában létrehozott klasszikus riasztási szabályok nem lesznek elavultak vagy migrálva. A tevékenység naplójában létrehozott klasszikus riasztási szabályok az új Azure Monitor-riasztásokból is elérhetők és használhatók. További információ: a [műveletnapló riasztások létrehozása, megtekintése és kezelése Azure monitor használatával](./alerts-activity-log.md). Hasonlóképpen, a Service Health található riasztások elérhetők és használhatók az új Service Health szakaszból. Részletekért lásd: [riasztások a szolgáltatás állapotáról szóló értesítésekről](../../service-health/alerts-activity-log-service-notifications-portal.md).

## <a name="unified-metrics-and-alerts-for-azure-resources"></a>Egyesített mérőszámok és riasztások az Azure-erőforrásokhoz

2018 márciusában megjelent az Azure-erőforrásokra vonatkozó riasztások következő generációja. Az újabb metrikus platform és riasztás gyorsabb, és nagyobb részletességet biztosít a dimenziók használatával. A méretek lehetővé teszik adott érték kombinációjának, feltételnek vagy műveletnek a szeletelését és szűrését. Az újabb metrikai riasztások olyan műveleti csoportokat használnak, amelyek több értesítést és automatizálási műveletet tesznek lehetővé. A [metrikai riasztások Azure monitor használatával történő kezelésével](./alerts-metric.md)kapcsolatos további információkért lásd:.

Az Azure-erőforrások újabb mérőszámai az alábbiak szerint érhetők el:

- **Standard szintű platform-metrikák Azure monitor** , amely a különböző Azure-szolgáltatásokból és-termékekből származó népszerű előre feltöltött mérőszámokat biztosít. További információkért tekintse meg a [Azure monitor támogatott mérőszámait](./alerts-metric-near-real-time.md#metrics-and-dimensions-supported) , és [Azure monitor a metrikai riasztások támogatását](./alerts-metric-overview.md#supported-resource-types-for-metric-alerts)ismertető cikket.
- **Azure monitor egyéni metrikák** – amely a felhasználó által vezérelt forrásokból, például a Azure Diagnostics-ügynökből származó mérőszámokat biztosít. További információkért tekintse meg ezt a cikket a [Azure monitor egyéni metrikák](../essentials/metrics-custom-overview.md)témakörében. Egyéni metrikák használatával közzéteheti a [Windows Azure Diagnostics Agent](../essentials/collect-custom-metrics-guestos-resource-manager-vm.md) és a [InfluxData-Graf ügynök](../essentials/collect-custom-metrics-linux-telegraf.md)által gyűjtött mérőszámokat is.

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>A klasszikus monitorozási és riasztási platform kivonulása

A korábban leírtaknak megfelelően a régebbi klasszikus monitorozási és riasztási funkciók kivonásra kerülnek a nyilvános Felhőbeli felhasználók számára. Magában foglalja a kapcsolódó API-k, Azure Portal interfészek és szolgáltatások bezárását is, bár ez a szolgáltatás azonban továbbra is korlátozott, **2021. május 31-ig**. A Azure Government Cloud és az Azure China 21Vianet klasszikus riasztásai a **2024. február 29**-én törlődnek.

A kivonási hatókör a Azure Portal [riasztások (klasszikus) szakaszában](./alerts-classic.overview.md) jelenleg elérhető klasszikus metrikák esetében érhető el, és [Microsoft. alertrules-](/rest/api/monitor/alertrules) erőforrásokként érhető el.

Ez a következőkkel jár:

- A klasszikus figyelési és riasztási szolgáltatás megszűnik, és az új riasztási szabályok létrehozására már nem lesz elérhető.
- Minden olyan riasztási szabály, amely továbbra is létezik a riasztásokban (klasszikus), továbbra is végrehajtja és leküldi az értesítéseket.
- A klasszikus riasztási szabályok többsége migrálva lesz. A folyamat zökkenőmentesen, leállás nélkül, és az ügyfelek nem fognak csökkenni a figyelési lefedettségben.
- A kiinduló értesítések közé tartoznak az új adattartalom-struktúra is. A célt úgy kell módosítani, hogy az új struktúrával működjön.
- Vannak olyan [klasszikus riasztási szabályok, amelyek nem telepíthetők át automatikusan](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts) , és manuális műveletre van szükségük a felhasználóktól a Futtatás folytatásához.

> [!IMPORTANT]
> Azure Monitor elkészített egy [eszközt, amellyel önként áttelepítheti](alerts-using-migration-tool.md) klasszikus riasztási szabályait az új platformra. A fennmaradó szabályokat a szolgáltatás kivonása után automatikusan áttelepíti a rendszer. A klasszikus riasztási szabályok áttelepítése után az ügyfeleknek biztosítaniuk kell, hogy a klasszikus riasztási szabálynak megfelelő adattartalmat a rendszer az [egységes mérőszámok és riasztások](#unified-metrics-and-alerts-for-azure-resources)új adattartalmainak kezeléséhez igazítsa. További információ: [felkészülés a klasszikus riasztási szabályok áttelepítésére](alerts-prepare-migration.md).

## <a name="pricing-for-migrated-alert-rules"></a>Az áttelepített riasztási szabályok díjszabása

Áttelepítési eszközt vezetünk be, amely segít áttelepíteni a Azure Monitor [klasszikus riasztásokat](./alerts-classic.overview.md) az új riasztások felületére. Az áttelepített riasztási szabályok és a megfelelő áttelepített műveleti csoportok (e-mailek, webhookok vagy LogicApp-EK) díjmentesen maradnak. A klasszikus riasztásokkal rendelkező funkciók, beleértve a küszöbérték, az összesítési típus és az Összesítés részletességének módosítását is, továbbra is ingyenesen elérhetők lesznek az áttelepített riasztási szabállyal. Ha azonban az áttelepített riasztási szabályt úgy szerkeszti, hogy az új riasztási platform funkcióit, értesítéseket vagy műveleti típusokat használja, a megfelelő díjat kell fizetnie. További információ a riasztási szabályok és értesítések díjszabásáról: [Azure monitor díjszabása](https://azure.microsoft.com/pricing/details/monitor/).

A következő példák olyan eseteket mutatnak be, amikor díjat számítunk fel a riasztási szabályhoz:

- Az új Azure Monitor platformon az ingyenes egységeken kívül létrehozott új (nem áttelepített) riasztási szabályok
- A Azure Monitor által tartalmazott ingyenes egységeken kívül betöltött és megőrzött adatmennyiség
- A Application Insights által végrehajtott többtesztes webes tesztek
- A Azure Monitorban foglalt ingyenes egységeken kívül tárolt egyéni metrikák
- Minden olyan áttelepített riasztási szabály, amely újabb metrikus riasztási funkciók, például gyakoriság, több erőforrás/dimenzió, [dinamikus küszöbértékek](../alerts/alerts-dynamic-thresholds.md), erőforrás/jel módosítása stb. használatára van módosítva.
- Minden olyan áttelepített műveleti csoport, amely újabb értesítések használatára van szerkesztve, vagy olyan típusú műveleteket, mint például az SMS, a hanghívás és/vagy a ITSM-integráció.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg az [új egységesített Azure monitor](../overview.md).
* További információ az új [Azure-riasztásokról](./alerts-overview.md).