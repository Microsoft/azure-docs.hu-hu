---
title: A kiszolgáló csoport skálázása – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: A kiszolgáló csoport memóriájának, lemezének és CPU-erőforrásainak módosítása a megnövekedett terhelés kezelésére
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: 59e6e73c99569b0a35c56d65c1a7ccdfcb394c0f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026420"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Nagy kapacitású-(Citus-) kiszolgálócsoport méretezése

A Azure Database for PostgreSQL-nagy kapacitású (Citus) önkiszolgáló skálázást biztosít a megnövekedett terhelés kezelésére. A Azure Portal megkönnyíti az új munkavégző csomópontok hozzáadását és a meglévő csomópontok virtuális mag növelését. A csomópontok hozzáadása nem eredményez állásidőt, és még a szegmensek áthelyezését is az új csomópontokra (a szegmensek közötti [újraelosztásnak](howto-hyperscale-scale-rebalance.md)nevezzük) a lekérdezések megszakítása nélkül történik.

## <a name="add-worker-nodes"></a>Munkavégző csomópontok hozzáadása

Csomópontok hozzáadásához nyissa meg a nagy kapacitású-(Citus-) kiszolgálócsoport **számítás + tárolás** lapját.  Ha a **munkavégző csomópontok száma** fölé húzza a csúszkát, az érték módosul.

:::image type="content" source="./media/howto-hyperscale-scaling/01-sliders-workers.png" alt-text="Erőforrás-csúszkák":::

Kattintson a **Save (Mentés** ) gombra a módosított érték életbe léptetéséhez.

> [!NOTE]
> A növelés és a mentés után a munkavégző csomópontok száma nem csökkenthető a csúszkával.

> [!NOTE]
> Az újonnan hozzáadott csomópontok kihasználása érdekében újra kell [osztania az elosztott tábla](howto-hyperscale-scale-rebalance.md)szegmenseit, ami azt jelenti, [hogy a meglévő csomópontokból származó](concepts-hyperscale-distributed-data.md#shards) szegmensek áthelyezése az újakra történik.

## <a name="increase-or-decrease-vcores-on-nodes"></a>A csomópontok virtuális magjai számának növelése vagy csökkentése

Az új csomópontok hozzáadása mellett a meglévő csomópontok képességeit is növelheti. A számítási kapacitás felfelé és lefelé való beállítása hasznos lehet a teljesítmény-kísérleteknél, valamint a forgalmi igények rövid vagy hosszú távú változásaihoz.

Ha módosítani szeretné az összes munkavégző csomópont virtuális mag, állítsa be a **virtuális mag** csúszkát a **konfiguráció alatt (munkavégző csomópont)**. A koordinátor csomópontjának virtuális mag egymástól függetlenül is módosítható. Állítsa be a **virtuális mag** csúszkát a  **konfiguráció (koordinátor csomópont)** alatt.

## <a name="next-steps"></a>Következő lépések

- További információ a kiszolgálói csoportok [teljesítményének lehetőségeiről](concepts-hyperscale-configuration-options.md).
- [Elosztott tábla szegmensek újraelosztása](howto-hyperscale-scale-rebalance.md) , hogy az összes feldolgozó csomópont részt vehessen a párhuzamos lekérdezésekben
