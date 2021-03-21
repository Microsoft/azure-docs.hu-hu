---
title: Események összesítése Linux Azure Diagnostics
description: Ismerje meg, hogy az Azure Service Fabric-fürtök monitorozásához és diagnosztizálásához a LAD használatával hogyan összesítheti és gyűjtheti az eseményeket.
author: srrengar
ms.topic: conceptual
ms.date: 2/25/2019
ms.author: srrengar
ms.openlocfilehash: 453258bb5768a2faa8f4e42cce86d02125283026
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96002170"
---
# <a name="event-aggregation-and-collection-using-linux-azure-diagnostics"></a>Események összesítése és gyűjtése linuxos Azure Diagnostics használatával
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Ha Azure Service Fabric-fürtöt futtat, érdemes összegyűjteni a naplókat egy központi helyen lévő összes csomópontról. A központi helyen található naplók segítségével elemezheti és elháríthatja a fürtben felmerülő problémákat, illetve a fürtben futó alkalmazások és szolgáltatások hibáit.

A naplók feltöltésének és összegyűjtésének egyik módja a Linux Azure Diagnostics (LAD) bővítmény használata, amely a naplókat feltölti az Azure Storage-ba, és lehetővé teszi a naplók küldését az Azure-ba Application Insights vagy Event Hubs. Külső folyamattal is elolvashatja az eseményeket a tárolóból, és elhelyezheti azokat egy Analysis platform termékében, például [Azure monitor naplókban](./service-fabric-diagnostics-oms-setup.md) vagy egy másik naplózási megoldásban.

## <a name="log-and-event-sources"></a>Napló-és eseményforrás

### <a name="service-fabric-platform-events"></a>Service Fabric platform eseményei
Service Fabric a [lttng érhető el](https://lttng.org)-on keresztül néhány beépített naplót bocsát ki, beleértve az operatív eseményeket vagy a futásidejű eseményeket. Ezeket a naplókat abban a helyen tárolja a rendszer, ahol a fürt Resource Manager-sablonja meg van határozva. A Storage-fiók adatainak beszerzéséhez vagy beállításához keresse meg a **AzureTableWinFabETWQueryable** címkét, és keresse meg a **StoreConnectionString**.

### <a name="application-events"></a>Alkalmazás eseményei
 Az alkalmazásokból és a szolgáltatásokból származó, a szoftver kiszervezése során megadott események. Bármilyen, szöveges alapú naplófájlokat írásos naplózási megoldást használhat – például Lttng érhető el. További információkért tekintse meg az alkalmazás nyomkövetésének Lttng érhető el dokumentációját.

[A szolgáltatások figyelése és diagnosztizálása a helyi számítógép-fejlesztési beállításokban](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).

## <a name="deploy-the-diagnostics-extension"></a>A diagnosztikai bővítmény üzembe helyezése
A naplók összegyűjtésének első lépéseként telepítenie kell a diagnosztikai bővítményt a Service Fabric-fürtön lévő összes virtuális gépre. A diagnosztikai bővítmény összegyűjti a naplókat az egyes virtuális gépeken, és feltölti azokat a megadott Storage-fiókba. 

Ha a fürt létrehozása során a diagnosztikai bővítményt a fürtben lévő virtuális gépekre szeretné telepíteni, állítsa a **diagnosztika** lehetőséget **a** következőre:. A fürt létrehozása után ez a beállítás nem módosítható a portál használatával, ezért a megfelelő módosításokat el kell végeznie a Resource Manager-sablonban.

Ezzel konfigurálja a LAD-ügynököt a megadott naplófájlok figyelésére. Minden alkalommal, amikor új sort fűz a fájlhoz, létrehoz egy syslog-bejegyzést, amelyet a rendszer a megadott tárolóba (táblára) küld.


## <a name="next-steps"></a>Következő lépések

1. A hibák elhárítása során megfontolandó események részletesebb megismeréséhez tekintse meg a [lttng érhető el dokumentációját](https://lttng.org/docs) és a [Lad használatával](../virtual-machines/extensions/diagnostics-linux.md)foglalkozó témakört.
2. [A log Analytics ügynök beállítása](service-fabric-diagnostics-event-analysis-oms.md) a metrikák gyűjtéséhez, a fürtön üzembe helyezett tárolók figyeléséhez és a naplók megjelenítéséhez 
