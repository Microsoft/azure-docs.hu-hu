---
title: Apache Spark Pool – fogalmak
description: Bevezetés az Azure szinapszis Analytics Apache Spark készletének méretére és konfigurációjáról.
services: synapse-analytics
author: mlee3gsd
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 12/2/2020
ms.author: martinle
ms.reviewer: euang
ms.openlocfilehash: e68b8aff700519b6bef31e5126c91fa8bc4a3593
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2021
ms.locfileid: "103202528"
---
# <a name="apache-spark-pool-configurations-in-azure-synapse-analytics"></a>Az Azure szinapszis Analytics szolgáltatásban Apache Spark készlet konfigurációi

A Spark-készlet olyan metaadatok halmaza, amely meghatározza a számítási erőforrások követelményeit és a társított viselkedési jellemzőket a Spark-példány példányainak létrehozásakor. Ezek a tulajdonságok közé tartoznak például a név, a csomópontok száma, a csomópont mérete, a skálázási viselkedés és az élettartam. A Spark-készlet önmagában nem használ erőforrást. A Spark-készletek létrehozásával kapcsolatban nem merült fel költségek. A díjakat csak akkor számítjuk fel, ha egy Spark-feladatot hajtanak végre a cél Spark-készleten, és a Spark-példányt igény szerint hozták létre.

Megtudhatja, hogyan hozhat létre egy Spark-készletet, és hogyan tekintheti meg az összes tulajdonságot itt [megkezdheti a Spark-készletek a szinapszis Analyticsben](../quickstart-create-apache-spark-pool-portal.md)

## <a name="nodes"></a>Csomópontok

Apache Spark a készlet példánya egy fő csomópontból és két vagy több feldolgozó csomópontból áll, amelyek legalább három csomóponttal rendelkeznek egy Spark-példányban.  A fő csomópont további felügyeleti szolgáltatásokat futtat, például a Livy, a fonal-erőforrás-kezelőt, a Zookeeper és a Spark-illesztőprogramot.  Minden csomópont olyan szolgáltatásokat futtat, mint a Node Agent és a fonál Node Manager. Minden munkavégző csomópont a Spark végrehajtó szolgáltatást futtatja.

## <a name="node-sizes"></a>Csomópontok méretei

A Spark-készletek olyan csomópont-méretekkel határozhatók meg, amelyek egy kis számítási csomópontból 8 virtuális mag és 64 GB memóriával rendelkeznek, és XXLarge számítási csomóponttal rendelkeznek, 64 virtuális mag és 432 GB memória/csomópont használatával. A csomópontok mérete a készlet létrehozása után módosítható, bár előfordulhat, hogy a példányt újra kell indítani.

|Méret | virtuális mag | Memória|
|-----|------|-------|
|Kicsi|4|32 GB|
|Közepes|8|64 GB|
|Nagy|16|128 GB|
|XLarge|32|256 GB|
|XXLarge|64|432 GB|

## <a name="autoscale"></a>Automatikus méretezés

Apache Spark készletek lehetővé teszik a számítási erőforrások automatikus vertikális felskálázását a tevékenység mennyisége alapján.  Ha az autoscale funkció engedélyezve van, beállíthatja a méretezni kívánt csomópontok minimális és maximális számát.
Ha az autoskálázási funkció le van tiltva, a csomópontok száma rögzített marad.  Ez a beállítás a készlet létrehozása után módosítható, de előfordulhat, hogy a példányt újra kell indítani.

## <a name="automatic-pause"></a>Automatikus szüneteltetés

Az automatikus szüneteltetési funkció egy beállított üresjárati időszak után felszabadítja az erőforrásokat egy Apache Spark készlet teljes díjainak csökkentése érdekében.  A funkció engedélyezése után a tétlenségi idő percben adható meg.  Az automatikus szüneteltetési funkció független az automatikus skálázási szolgáltatástól. Az erőforrások szüneteltethető, ha az automatikus skálázás engedélyezve van vagy le van tiltva.  Ez a beállítás a készlet létrehozása után módosítható, de előfordulhat, hogy a példányt újra kell indítani.

## <a name="next-steps"></a>Következő lépések

* [Azure Synapse Analytics](/azure/synapse-analytics)
* [Apache Spark dokumentáció](https://spark.apache.org/docs/2.4.5/)
