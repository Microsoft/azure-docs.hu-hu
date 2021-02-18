---
title: Tároló-példányok figyelése
description: A számítási erőforrások (például a processzor és a memória) használatának figyelése a tárolók Azure Container Instancesban.
ms.topic: article
ms.date: 12/17/2020
ms.openlocfilehash: ae9725ffe66bebbed26745c311b2ada07d5d2c00
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100589301"
---
# <a name="monitor-container-resources-in-azure-container-instances"></a>Tároló-erőforrások figyelése az Azure Container Instances-ben

[Azure monitor][azure-monitoring] betekintést nyújt a tárolók példányai által használt számítási erőforrásokra. Az erőforrás-használati adatok segítségével meghatározhatja a tároló csoportok legjobb erőforrás-beállításait. A Azure Monitor olyan metrikákat is biztosít, amelyek nyomon követik a hálózati tevékenységeket a tároló példányaiban.

Ez a dokumentum a Azure Portal és az Azure CLI használatával a Container instances Azure Monitor metrikáinak összegyűjtését ismerteti.

> [!IMPORTANT]
> A Azure Container Instances Azure Monitor metrikái jelenleg előzetes verzióban érhetők el, és bizonyos [korlátozások érvényesek](#preview-limitations). Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="preview-limitations"></a>Előzetes verzió korlátozásai

Jelenleg Azure Monitor mérőszámok csak Linux-tárolók esetén érhetők el.

## <a name="available-metrics"></a>Rendelkezésre álló metrikák

A Azure Monitor a következő [metrikákat biztosítja a Azure Container Instanceshoz][supported-metrics]. Ezek a metrikák a tárolók és az egyes tárolók számára érhetők el. Alapértelmezés szerint a metrikák összesítése átlagként történik.

- **CPU-használat** a **millicores**-ben mérve. 
  - Az egyik millicore egy CPU-mag 1/1000th, így a 500 millicores a 0,5 CPU Core használatát jelöli.
- **Memória kihasználtsága** bájtban
- Másodpercenként **fogadott hálózati bájtok** száma
- Másodpercenként **továbbított hálózati bájtok** száma 

## <a name="get-metrics---azure-portal"></a>Metrika beolvasása – Azure Portal

Tárolócsoport létrehozásakor az Azure Monitor adatai elérhetők az Azure Portalon. Egy tároló-csoport metrikáinak megjelenítéséhez nyissa meg a tároló csoport **Áttekintés** lapját. Itt láthatja az összes elérhető metrikához tartozó előre létrehozott diagramokat.

![kettős diagram][dual-chart]

Egy több tárolót tartalmazó tároló csoportban használjon egy [dimenziót][monitor-dimension] a metrikák tároló alapján történő megjelenítéséhez. Az egyes tárolómetrikák diagramjának létrehozásához hajtsa végre az alábbi lépéseket:

1. Az **Áttekintés** oldalon válassza ki az egyik mérőszám-diagramot, például a **processzort**. 
1. Válassza a **felosztás alkalmazása** gombot, és válassza a **tároló neve** lehetőséget.

![A képernyőfelvételek egy tároló példány metrikáit jelenítik meg, a kiválasztott felosztással és a tároló nevének kiválasztásával.][dimension]

## <a name="get-metrics---azure-cli"></a>Metrika beolvasása – Azure CLI

A Container instances mérőszámai az Azure CLI használatával is összegyűjthetők. Első lépésként olvassa be a csoport azonosítóját az alábbi paranccsal. Cserélje le a `<resource-group>` kifejezést az erőforráscsoport-nevével, a `<container-group>` kifejezést pedig a tárolócsoport nevével.


```console
CONTAINER_GROUP=$(az container show --resource-group <resource-group> --name <container-group> --query id --output tsv)
```

Használja az alábbi parancsot a **CPU**-használati metrikák lekérésére.

```azurecli
az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --output table
```

```output
Timestamp            Name       Average
-------------------  ---------  ---------
2020-12-17 23:34:00  CPU Usage
. . .
2020-12-18 00:25:00  CPU Usage
2020-12-18 00:26:00  CPU Usage  0.4
2020-12-18 00:27:00  CPU Usage  0.0
```

Módosítsa a `--metric` paraméter értékét a parancsban más [támogatott metrikák][supported-metrics]beszerzéséhez. Használja például a következő parancsot a **memóriahasználat** metrikáinak beolvasásához. 

```azurecli
az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --output table
```

```output
Timestamp            Name          Average
-------------------  ------------  ----------
2019-04-23 22:59:00  Memory Usage
2019-04-23 23:00:00  Memory Usage
2019-04-23 23:01:00  Memory Usage  0.0
2019-04-23 23:02:00  Memory Usage  8859648.0
2019-04-23 23:03:00  Memory Usage  9181184.0
2019-04-23 23:04:00  Memory Usage  9580544.0
2019-04-23 23:05:00  Memory Usage  10280960.0
2019-04-23 23:06:00  Memory Usage  7815168.0
2019-04-23 23:07:00  Memory Usage  7739392.0
2019-04-23 23:08:00  Memory Usage  8212480.0
2019-04-23 23:09:00  Memory Usage  8159232.0
2019-04-23 23:10:00  Memory Usage  8093696.0
```

A többtárolós csoportok esetében a `containerName` dimenzió hozzáadható a metrikák visszaküldéséhez.

```azurecli
az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --dimension containerName --output table
```

```output
Timestamp            Name          Containername             Average
-------------------  ------------  --------------------  -----------
2019-04-23 22:59:00  Memory Usage  aci-tutorial-app
2019-04-23 23:00:00  Memory Usage  aci-tutorial-app
2019-04-23 23:01:00  Memory Usage  aci-tutorial-app      0.0
2019-04-23 23:02:00  Memory Usage  aci-tutorial-app      16834560.0
2019-04-23 23:03:00  Memory Usage  aci-tutorial-app      17534976.0
2019-04-23 23:04:00  Memory Usage  aci-tutorial-app      18329600.0
2019-04-23 23:05:00  Memory Usage  aci-tutorial-app      19742720.0
2019-04-23 23:06:00  Memory Usage  aci-tutorial-app      14786560.0
2019-04-23 23:07:00  Memory Usage  aci-tutorial-app      14651392.0
2019-04-23 23:08:00  Memory Usage  aci-tutorial-app      15470592.0
2019-04-23 23:09:00  Memory Usage  aci-tutorial-app      15450112.0
2019-04-23 23:10:00  Memory Usage  aci-tutorial-app      15339520.0
2019-04-23 22:59:00  Memory Usage  aci-tutorial-sidecar
2019-04-23 23:00:00  Memory Usage  aci-tutorial-sidecar
2019-04-23 23:01:00  Memory Usage  aci-tutorial-sidecar  0.0
2019-04-23 23:02:00  Memory Usage  aci-tutorial-sidecar  884736.0
2019-04-23 23:03:00  Memory Usage  aci-tutorial-sidecar  827392.0
2019-04-23 23:04:00  Memory Usage  aci-tutorial-sidecar  831488.0
2019-04-23 23:05:00  Memory Usage  aci-tutorial-sidecar  819200.0
2019-04-23 23:06:00  Memory Usage  aci-tutorial-sidecar  843776.0
2019-04-23 23:07:00  Memory Usage  aci-tutorial-sidecar  827392.0
2019-04-23 23:08:00  Memory Usage  aci-tutorial-sidecar  954368.0
2019-04-23 23:09:00  Memory Usage  aci-tutorial-sidecar  868352.0
2019-04-23 23:10:00  Memory Usage  aci-tutorial-sidecar  847872.0
```

## <a name="next-steps"></a>Következő lépések

Az Azure-alapú figyelésről további információt az [Azure-alapú figyelés áttekintése][azure-monitoring] szakaszban talál.

Megtudhatja, hogyan hozhat létre [metrikus riasztásokat][metric-alert] , hogy értesítést kapjon, ha Azure Container instances mérőszáma egy küszöbértéket keresztez.

<!-- IMAGES -->
[cpu-chart]: ./media/container-instances-monitor/cpu-multi.png
[dimension]: ./media/container-instances-monitor/dimension.png
[dual-chart]: ./media/container-instances-monitor/metrics.png
[memory-chart]: ./media/container-instances-monitor/memory-multi.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-monitoring]: ../azure-monitor/overview.md
[metric-alert]: ..//azure-monitor/alerts/alerts-metric.md
[monitor-dimension]: ../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics
[supported-metrics]: ../azure-monitor/essentials/metrics-supported.md#microsoftcontainerinstancecontainergroups
