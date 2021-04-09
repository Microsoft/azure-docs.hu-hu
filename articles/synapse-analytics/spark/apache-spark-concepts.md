---
title: Apache Spark alapvető fogalmak
description: Az Azure szinapszis Analytics Apache Spark alapfogalmai.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 51b2e8cd968c4c14777d196d90686b13158aef42
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98120308"
---
# <a name="apache-spark-in-azure-synapse-analytics-core-concepts"></a>Apache Spark az Azure szinapszis Analytics alapvető fogalmakkal

A Apache Spark egy párhuzamos feldolgozási keretrendszer, amely támogatja a memóriabeli feldolgozást a Big-adatanalitikai alkalmazások teljesítményének növelése érdekében. Az Azure szinapszis Analytics Apache Spark a Microsoft egyike a Felhőbeli Apache Spark megvalósításának. 

Az Azure szinapszis megkönnyíti a Spark-képességek létrehozását és konfigurálását az Azure-ban. Az Azure szinapszis ezen Spark-képességek eltérő implementációját biztosítja, amelyek dokumentálva vannak itt.

## <a name="spark-pools"></a>Spark-készletek

Kiszolgáló nélküli Apache Spark készlet jön létre a Azure Portal. Ez egy olyan Spark-készlet definíciója, amely a példányok létrehozásakor egy olyan Spark-példány létrehozására szolgál, amely az adatfeldolgozást végzi. Spark-készlet létrehozásakor a rendszer csak metaadatként működik, és nem használja fel, nem futtatja és nem számlázza fel az erőforrásokat. A Spark-készlet olyan tulajdonságokkal rendelkezik, amelyek a Spark-példány jellemzőit vezérlik. Ezek a tulajdonságok közé tartoznak például a név, a méret, a skálázási viselkedés, az élettartam.

Mivel a Spark-készletek létrehozásához nem jár dollár vagy erőforrás-díj, tetszőleges számú különböző konfigurációval hozható létre. Az engedélyek olyan Spark-készletekre is alkalmazhatók, amelyek lehetővé teszik a felhasználók számára, hogy mások számára is hozzáférjenek.

Az ajánlott eljárás az, hogy olyan kisebb Spark-készleteket hozzon létre, amelyek fejlesztési és hibakeresési célokra használhatók, majd nagyobbak a termelési munkaterhelések futtatásához is.

Megtudhatja, hogyan hozhat létre egy Spark-készletet, és hogyan tekintheti meg az összes tulajdonságot itt [megismerheti a Spark-készleteket az Azure szinapszis Analytics szolgáltatásban](../quickstart-create-apache-spark-pool-portal.md)

## <a name="spark-instances"></a>Spark-példányok

A Spark-példányok akkor jönnek létre, amikor egy Spark-készlethez csatlakozik, létrehoz egy munkamenetet, és futtat egy feladatot. Mivel több felhasználó is hozzáférhet egyetlen Spark-készlethez, az összes csatlakozó felhasználóhoz létrejön egy új Spark-példány. 

Ha egy második feladatot küld el, ha van kapacitás a készletben, akkor a meglévő Spark-példány kapacitása is rendelkezésre áll. Ezután a meglévő példány feldolgozza a feladatot. Ellenkező esetben, ha a kapacitás rendelkezésre áll a készlet szintjén, a rendszer új Spark-példányt hoz létre.

## <a name="examples"></a>Példák

### <a name="example-1"></a>1\. példa

- Létrehoz egy SP1 nevű Spark-készletet; a fürt rögzített mérete 20 csomópont.
- Elküld egy jegyzetfüzet-feladatot, a J1, amely 10 csomópontot használ, egy Spark-példányt, a SI1 a feladatok feldolgozásához.
- Most elküld egy másik feladatot, a J2, amely 10 csomópontot használ, mivel a készletben és a példányban is van kapacitás, a J2 a SI1 dolgozza fel.
- Ha a J2 11 csomópontra kérték, a SP1 vagy a SI1 nem volt kapacitása. Ebben az esetben, ha a J2 egy jegyzetfüzetből származik, a rendszer elutasítja a feladatot. Ha a J2 egy batch-feladatokból származik, az várólistára kerül.

### <a name="example-2"></a>2\. példa

- Létrehoz egy Spark Pool-hívást SP2; engedélyezve van a 10 – 20 csomópontos autoskálázás
- Elküld egy jegyzetfüzet-feladatot, a J1, amely 10 csomópontot használ, egy Spark-példányt, a SI1-t, amely a feladatok feldolgozásához jön létre.
- Most elküld egy másik, 10 csomópontot használó feladatot, a J2, mert a készletben továbbra is kapacitás áll rendelkezésre, így a példányok automatikusan 20 csomópontra növekednek, és J2 a folyamatokat.

### <a name="example-3"></a>3\. példa

- Létrehoz egy SP1 nevű Spark-készletet; a fürt rögzített mérete 20 csomópont.
- Elküld egy jegyzetfüzet-feladatot, a J1, amely 10 csomópontot használ, egy Spark-példányt, a SI1 a feladatok feldolgozásához.
- Egy másik felhasználó, a U2, a J3, amely 10 csomópontot használ, egy új Spark-példányt hoz létre a SI2, amely feldolgozza a feladatot.
- Most elküld egy másik feladatot, a J2, amely 10 csomópontot használ, mivel a készletben továbbra is rendelkezésre áll a kapacitása, a J2 pedig a SI1 dolgozza fel.

## <a name="quotas-and-resource-constraints-in-apache-spark-for-azure-synapse"></a>Az Azure szinapszis Apache Spark kvótái és erőforrás-korlátozásai

### <a name="workspace-level"></a>Munkaterület szintje

Minden Azure szinapszis-munkaterület tartalmaz egy alapértelmezett virtuális mag-kvótát, amelyet a Spark használhat. A kvóta felosztása a felhasználói kvóta és a adatfolyam kvóta között történik, így a használati minta sem használja fel a munkaterület összes virtuális mag. A kvóta az előfizetés típusától függően eltérő, de a felhasználó-és adatfolyam között szimmetrikus. Ha azonban több virtuális mag igényel, mint amennyi a munkaterületen marad, akkor a következő hibaüzenetet kapja:

```console
Failed to start session: [User] MAXIMUM_WORKSPACE_CAPACITY_EXCEEDED
Your Spark job requested 480 vcores.
However, the workspace only has xxx vcores available out of quota of yyy vcores.
Try reducing the numbers of vcores requested or increasing your vcore quota. Click here for more information - https://go.microsoft.com/fwlink/?linkid=213499
```

Az üzenetben szereplő hivatkozás erre a cikkre mutat.

A következő cikk azt ismerteti, hogyan lehet a munkaterület virtuális mag-kvótájának növekedését kérni.

- A szolgáltatás típusaként válassza az "Azure szinapszis Analytics" lehetőséget.
- A kvóta részletei ablakban válassza ki a Apache Spark (virtuális mag) munkaterületen

[Kapacitás növelésének kérése a Azure Portal használatával](../../azure-portal/supportability/per-vm-quota-requests.md#request-a-standard-quota-increase-from-help--support)

### <a name="spark-pool-level"></a>Spark-készlet szintje

Ha megad egy Spark-készletet, akkor az adott készlethez felhasználónként kell meghatároznia a kvótát, ha több jegyzetfüzetet vagy feladatot futtat, vagy a 2 kombinációja is lehetséges, hogy kimeríti a készlet kvótáját. Ha így tesz, a következőhöz hasonló hibaüzenet jelenik meg:

```console
Failed to start session: Your Spark job requested xx vcores.
However, the pool is consuming yy vcores out of available zz vcores.Try ending the running job(s) in the pool, reducing the numbers of vcores requested, increasing the pool maximum size or using another pool
```

Ennek a problémának a megoldásához csökkentenie kell a készlet erőforrásainak használatát, mielőtt egy jegyzetfüzetet vagy feladatot futtasson egy új erőforrás-kérelem elküldése előtt.

## <a name="next-steps"></a>Következő lépések

- [Azure Synapse Analytics](../index.yml)
- [Apache Spark dokumentáció](https://spark.apache.org/docs/2.4.5/)