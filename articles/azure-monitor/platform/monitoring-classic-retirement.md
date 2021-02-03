---
title: A klasszikus riasztások & figyelésének frissítése Azure Monitor
description: A klasszikus figyelési szolgáltatások és funkciók kivonásának leírása, amely korábban a riasztások (klasszikus) alatt Azure Portal látható.
author: yanivlavi
services: azure-monitor
ms.topic: conceptual
ms.date: 2/7/2019
ms.author: yalavi
ms.subservice: alerts
ms.openlocfilehash: 368ab1bc6a1fc13c3001b437c3c2a8be2bbb9c04
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99525991"
---
# <a name="unified-alerting--monitoring-in-azure-monitor-replaces-classic-alerting--monitoring"></a>Az egyesített riasztási & figyelése Azure Monitor helyettesíti a klasszikus riasztási & figyelését

Azure Monitor mostantól egy egységes, teljes verem-figyelési szolgáltatás lesz, amely mostantól támogatja az "egy metrika" és az "egy riasztások" használatát az erőforrások között. További információkért tekintse meg az [új Azure monitor blogbejegyzését](https://azure.microsoft.com/blog/new-full-stack-monitoring-capabilities-in-azure-monitor/). Az új Azure monitoring-és riasztási platformok gyorsabb, intelligensebb és bővíthetők, így folyamatosan bővülnek a Felhőbeli számítástechnika és a Microsoft intelligens felhőalapú filozófiája.

Az új Azure-figyelési és-riasztási platformmal a Azure Monitor klasszikus riasztásai kimaradnak a nyilvános Felhőbeli felhasználók számára, bár az új riasztásokat még nem támogató erőforrások esetében is korlátozott mértékben használják. A riasztások kivonulási dátuma tovább bővült. A fennmaradó riasztások áttelepítése, [Azure Government a felhő](../../azure-government/documentation-government-welcome.md)és az [Azure China 21Vianet](https://docs.azure.cn/)hamarosan új dátummal fog bejelenteni.

 ![Klasszikus riasztás a Azure Portalban](media/monitoring-classic-retirement/monitor-alert-screen2.png) 

Javasoljuk, hogy kezdje el és hozza létre újra a riasztásokat az új platformon.

> [!IMPORTANT]
> A tevékenység naplójában létrehozott klasszikus riasztási szabályok nem lesznek elavultak vagy migrálva. A tevékenység naplójában létrehozott klasszikus riasztási szabályok az új Azure Monitor-riasztásokból is elérhetők és használhatók. További információ: a [műveletnapló riasztások létrehozása, megtekintése és kezelése Azure monitor használatával](./alerts-activity-log.md). Hasonlóképpen, a Service Health található riasztások elérhetők és használhatók az új Service Health szakaszból. Részletekért lásd: [riasztások a szolgáltatás állapotáról szóló értesítésekről](../../service-health/alerts-activity-log-service-notifications-portal.md).

## <a name="unified-metrics-and-alerts-in-application-insights"></a>Egyesített mérőszámok és riasztások a Application Insights

A Azure Monitor újabb metrikai platformja mostantól a Application Insightstól érkező energiagazdálkodást is figyeli. Ez azt jelenti, hogy Application Insights a műveleti csoportokba fog csatlakozni, ami sokkal több lehetőséget tesz lehetővé, mint csupán az előző e-mail-és webhook-hívások. A riasztások mostantól elindíthatnak hanghívásokat, Azure Functions, Logic Apps, SMS és ITSM eszközöket, például a ServiceNow és az Automation Runbookok. A közel valós idejű figyeléssel és riasztással az új platform lehetővé teszi, hogy Application Insights felhasználók ugyanazt a technológiát használják a többi Azure-erőforráshoz, és a Microsoft-termékek figyelését.

A Application Insights új egységes figyelési és riasztási feladatai a következőket foglalják magukban:

- **Application Insights platform metrikák** – amely a Application Insights termék népszerű, előre összeépített metrikáit biztosítja. További információkért tekintse meg az [új Azure Monitor Application Insights platform metrikáinak](../app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics)használatát ismertető cikket.
- **Application Insights rendelkezésre állás és webes teszt** – lehetővé teszi a webalkalmazás vagy-kiszolgáló válaszadásának és rendelkezésre állásának felmérését. További információkért tekintse meg ezt a cikket a [rendelkezésre állási tesztek és riasztások használatáról az új Azure Monitor Application Insights](../app/monitor-web-app-availability.md).
- **Application Insights egyéni metrikák** – lehetővé teszi saját mérőszámok meghatározását és kibocsátását a figyeléshez és a riasztásokhoz. További információkért tekintse meg ezt a cikket, [amely az egyéni metrika használatát ismerteti Application Insights új Azure monitor](../app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).
- **Application Insights hibák rendellenességei (az intelligens észlelés része)** – amely automatikusan értesíti Önt közel valós időben, ha a webalkalmazás a sikertelen HTTP-kérelmek vagy függőségi hívások gyakoriságának rendellenes növekedését tapasztalja. További információkért tekintse meg ezt a cikket az [intelligens észlelés – meghibásodási rendellenességek](../app/proactive-failure-diagnostics.md)használata című cikkben.

## <a name="unified-metrics-and-alerts-for-other-azure-resources"></a>Egyesített mérőszámok és riasztások más Azure-erőforrásokhoz

Március 2018 óta a riasztások és az Azure-erőforrások többdimenziós monitorozásának következő generációja rendelkezésre áll. Az újabb metrikus platform és a riasztások mostantól a közel valós idejű funkciókkal is gyorsabbak. Ennél is fontosabb, hogy az újabb mérőszámok platformjának riasztásai részletesebbek, mivel az újabb platform magában foglalja a méretek lehetőségét, ami lehetővé teszi, hogy egy adott érték kombinációjának, feltételének vagy műveletnek a szeletét és szűrését adja meg. Az új Azure Monitorban található összes riasztáshoz hasonlóan az újabb metrikai riasztások bővíthetők a ActionGroups használatával, így az értesítések kiterjeszthetők az e-mailek, a webhookok és az SMS, a hang, az Azure Function, az Automation Runbook és egyebek között. További információ: [metrikus riasztások létrehozása, megtekintése és kezelése Azure monitor használatával](./alerts-metric.md).
Az Azure-erőforrások újabb mérőszámai az alábbiak szerint érhetők el:

- **Standard szintű platform-metrikák Azure monitor** , amely a különböző Azure-szolgáltatásokból és-termékekből származó népszerű előre feltöltött mérőszámokat biztosít. További információkért tekintse meg a [Azure monitor támogatott mérőszámait](./alerts-metric-near-real-time.md#metrics-and-dimensions-supported) , és [Azure monitor a metrikai riasztások támogatását](./alerts-metric-overview.md#supported-resource-types-for-metric-alerts)ismertető cikket.
- **Azure monitor egyéni metrikák** – amely a felhasználó által vezérelt forrásokból, például a Azure Diagnostics-ügynökből származó mérőszámokat biztosít. További információkért tekintse meg ezt a cikket a [Azure monitor egyéni metrikák](./metrics-custom-overview.md)témakörében. Egyéni metrikák használatával közzéteheti a [Windows Azure Diagnostics Agent](./collect-custom-metrics-guestos-resource-manager-vm.md) és a [InfluxData-Graf ügynök](./collect-custom-metrics-linux-telegraf.md)által gyűjtött mérőszámokat is.

## <a name="retirement-of-classic-monitoring-and-alerting-platform"></a>A klasszikus monitorozási és riasztási platform kivonulása

A korábban leírtaknak megfelelően a régebbi klasszikus monitorozási és riasztási funkciók kivonásra kerülnek a nyilvános Felhőbeli felhasználók számára; beleértve a kapcsolódó API-k, Azure Portal felület és szolgáltatások bezárását is, bár az új riasztásokat még nem támogató erőforrások esetében is korlátozott használatban van. Ezek a szolgáltatások elavultak lesznek:

- Régebbi (klasszikus) mérőszámok és riasztások az Azure-erőforrásokról, amelyek jelenleg a Azure Portal [riasztások (klasszikus) szakaszán](./alerts-classic.overview.md) keresztül elérhetők. elérhető [Microsoft. ininsights/alertrules-](/rest/api/monitor/alertrules) erőforrásként
- A régebbi (klasszikus) platform és a Application Insights egyéni metrikái, valamint a Azure Portal [riasztások (klasszikus) szakasza](./alerts-classic.overview.md) által jelenleg elérhetővé vált riasztások, valamint a [Microsoft. alertrules](/rest/api/monitor/alertrules) erőforrás
- A régebbi (klasszikus) meghibásodási rendellenességek riasztása jelenleg a Azure Portal [Application Insightson belüli intelligens észlelésként](../app/proactive-diagnostics.md) érhető el; a Azure Portal [riasztások (klasszikus) szakaszában](./alerts-classic.overview.md) látható riasztások vannak konfigurálva

Ez a következőkkel jár:

- A klasszikus figyelési és riasztási szolgáltatás megszűnik, és az új riasztási szabályok létrehozására már nem lesz elérhető.
- Minden olyan riasztási szabály, amely továbbra is létezik a riasztásokban (klasszikus), továbbra is végrehajtja és leküldi az értesítéseket.
- A klasszikus figyelési & riasztásokat tartalmazó riasztási szabályokat, amelyek áttelepíthetők, a Microsoft a néhány héten átívelő fázisokban automatikusan áthelyezi az új Azure monitor platformon. A folyamat zökkenőmentesen, leállás nélkül, és az ügyfelek nem fognak csökkenni a figyelési lefedettségben.
- Az új riasztási platformra migrált riasztási szabályok az előzőekben leírtak szerint figyelik a figyelési lefedettséget, de az új adattartalommal kapcsolatos értesítéseket is A rendszer áttelepíti a klasszikus riasztási szabályhoz társított e-mail-címeket, webhook-végpontokat vagy logikai alkalmazásokra mutató hivatkozásokat, de előfordulhat, hogy az új platformon nem működik megfelelően a riasztási adattartalom.
- Egyes [klasszikus riasztási szabályok, amelyek nem telepíthetők át automatikusan](alerts-understand-migration.md#manually-migrating-classic-alerts-to-newer-alerts) , és a felhasználók manuális műveletének megkövetelése továbbra is futni fog.

> [!IMPORTANT]
> A Microsoft Azure figyelő a fázisok eszközben a klasszikus riasztási szabályoknak az új platformra történő [önkéntes áttelepítését](alerts-using-migration-tool.md) végzi. És futtassa a Force utasítással minden olyan klasszikus riasztási szabályt, amely még létezik és áttelepíthető. Az ügyfeleknek biztosítaniuk kell, hogy a klasszikus riasztási szabályokhoz használt Automation-adattartalmat a rendszer a klasszikus riasztási szabályok áttelepítése után az [egyesített metrikák és riasztások Application Insights](#unified-metrics-and-alerts-in-application-insights) , illetve az [egyesített Mérőszámokban és riasztásokkal](#unified-metrics-and-alerts-for-other-azure-resources)való kezeléséhez igazítsa. További információ: [felkészülés a klasszikus riasztási szabályok áttelepítésére](alerts-prepare-migration.md)

Ez a cikk folyamatosan frissül az új Azure monitoring & riasztási funkciókkal & részletekkel, valamint az új Azure Monitor platform bevezetését segítő eszközök rendelkezésre állásával.

## <a name="pricing-for-migrated-alert-rules"></a>Az áttelepített riasztási szabályok díjszabása

Áttelepítési eszközt vezetünk be, amely segít áttelepíteni a Azure Monitor [klasszikus riasztásokat](./alerts-classic.overview.md) az új riasztások felületére. Az áttelepített riasztási szabályok és a megfelelő áttelepített műveleti csoportok (e-mailek, webhookok vagy LogicApp-EK) díjmentesen maradnak. A klasszikus riasztásokkal rendelkező funkciók, beleértve a küszöbérték, az összesítési típus és az Összesítés részletességének módosítását is, továbbra is ingyenesen elérhetők lesznek az áttelepített riasztási szabállyal. Ha azonban az áttelepített riasztási szabályt úgy szerkeszti, hogy az új riasztási platform funkcióit, értesítéseket vagy műveleti típusokat használja, a megfelelő díjat kell fizetnie. További információ a riasztási szabályok és értesítések díjszabásáról: [Azure monitor díjszabása](https://azure.microsoft.com/pricing/details/monitor/).

Az alábbiakban példákat talál arra az esetre, amikor díjat kell fizetnie a riasztási szabályhoz:

- Az új Azure Monitor platformon az ingyenes egységeken kívül létrehozott új (nem áttelepített) riasztási szabályok
- A Azure Monitor által tartalmazott ingyenes egységeken kívül betöltött és megőrzött adatmennyiség
- A Application Insights által végrehajtott többtesztes webes tesztek
- A Azure Monitorban foglalt ingyenes egységeken kívül tárolt egyéni metrikák
- Minden olyan áttelepített riasztási szabály, amely újabb metrikus riasztási funkciók, például gyakoriság, több erőforrás/dimenzió, [dinamikus küszöbértékek](alerts-dynamic-thresholds.md), erőforrás/jel módosítása stb. használatára van módosítva.
- Minden olyan áttelepített műveleti csoport, amely újabb értesítések használatára van szerkesztve, vagy olyan típusú műveleteket, mint például az SMS, a hanghívás és/vagy a ITSM-integráció.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg az [új egységesített Azure monitor](../overview.md).
* További információ az új [Azure-riasztásokról](./alerts-overview.md).

