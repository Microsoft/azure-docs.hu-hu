---
title: A kiszolgáló csoport skálázása – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: A kiszolgáló csoport memóriájának, lemezének és CPU-erőforrásainak módosítása a megnövekedett terhelés kezelésére
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 04/07/2021
ms.openlocfilehash: 905224119b9df4e4003b1736443406e9548a49e3
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012529"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Nagy kapacitású-(Citus-) kiszolgálócsoport méretezése

A Azure Database for PostgreSQL-nagy kapacitású (Citus) önkiszolgáló skálázást biztosít a megnövekedett terhelés kezelésére. A Azure Portal megkönnyíti az új munkavégző csomópontok hozzáadását és a meglévő csomópontok virtuális mag növelését. A csomópontok hozzáadása nem eredményez állásidőt, és még a szegmensek áthelyezését is az új csomópontokra (a szegmensek közötti [újraelosztásnak](howto-hyperscale-scale-rebalance.md)nevezzük) a lekérdezések megszakítása nélkül történik.

## <a name="add-worker-nodes"></a>Munkavégző csomópontok hozzáadása

Csomópontok hozzáadásához nyissa meg a nagy kapacitású-(Citus-) kiszolgálócsoport **számítás + tárolás** lapját.  Ha a **munkavégző csomópontok száma** fölé húzza a csúszkát, az érték módosul.

> [!NOTE]
>
> Az [alapszintű csomaggal (előzetes verzió)](concepts-hyperscale-tiers.md) létrehozott nagy kapacitású-(Citus-) kiszolgálócsoport nem rendelkezik feldolgozókkal. A munkavégzők számának növelése automatikusan bevégezi a kiszolgálói csoportot a standard szinthez.
> Egy kiszolgálócsoport standard szintre való beosztása után az alapszintű csomagra nem állítható vissza.

:::image type="content" source="./media/howto-hyperscale-scaling/01-sliders-workers.png" alt-text="Erőforrás-csúszkák":::

Kattintson a **Save (Mentés** ) gombra a módosított érték életbe léptetéséhez.

> [!NOTE]
> A növelés és a mentés után a munkavégző csomópontok száma nem csökkenthető a csúszkával.

> [!NOTE]
> Az újonnan hozzáadott csomópontok kihasználása érdekében újra kell [osztania az elosztott tábla](howto-hyperscale-scale-rebalance.md)szegmenseit, ami azt jelenti, [hogy a meglévő csomópontokból származó](concepts-hyperscale-distributed-data.md#shards) szegmensek áthelyezése az újakra történik.

## <a name="increase-or-decrease-vcores-on-nodes"></a>A csomópontok virtuális magjai számának növelése vagy csökkentése

Az új csomópontok hozzáadása mellett a meglévő csomópontok képességeit is növelheti. A számítási kapacitás felfelé és lefelé való beállítása hasznos lehet a teljesítmény-kísérleteknél, illetve a forgalmi igények rövid vagy hosszú távú változásaihoz.

Ha módosítani szeretné az összes munkavégző csomópont virtuális mag, állítsa be a **virtuális mag** csúszkát a **konfiguráció alatt (munkavégző csomópont)**. A koordinátor csomópontjának virtuális mag egymástól függetlenül is módosítható. Állítsa be a **virtuális mag** csúszkát a  **konfiguráció (koordinátor csomópont)** alatt.

## <a name="next-steps"></a>Következő lépések

- További információ a kiszolgálói csoportok [teljesítményének lehetőségeiről](concepts-hyperscale-configuration-options.md).
- [Elosztott tábla szegmensek újraelosztása](howto-hyperscale-scale-rebalance.md) , hogy az összes feldolgozó csomópont részt vehessen a párhuzamos lekérdezésekben
