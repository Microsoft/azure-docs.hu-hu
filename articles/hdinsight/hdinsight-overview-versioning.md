---
title: Verziószámozás bemutatása – Azure HDInsight
description: Ismerje meg, hogyan működik a verziószámozás az Azure HDInsight-ben.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: deshriva
ms.date: 02/08/2021
ms.openlocfilehash: c4dddeef5daf167eeef92532b61ed986861896e8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745362"
---
# <a name="how-versioning-works-in-hdinsight"></a>A verziószámozás működése a HDInsight-ben

A HDInsight szolgáltatásnak két fő összetevője van: egy erőforrás-szolgáltató és egy fürtön üzembe helyezett összetevők Apache Hadoop. 

## <a name="hdinsight-resource-provider"></a>HDInsight erőforrás-szolgáltató

Az Azure-beli erőforrásokat egy erőforrás-szolgáltató bocsátja rendelkezésre. A HDInsight erőforrás-szolgáltató felelős a fürtök létrehozásához, kezeléséhez és törléséhez.

## <a name="hdinsight-images"></a>HDInsight-lemezképek

A HDInsight képeket használ a fürtön üzembe helyezhető nyílt forráskódú szoftverek (OSS) összetevőinek összeállításához. Ezek a lemezképek tartalmazzák az Ubuntu operációs rendszert és az alapvető összetevőket, például a Spark, a Hadoop, a Kafka, a HBase vagy a kaptár.

## <a name="versioning-in-hdinsight"></a>Verziószámozás a HDInsight-ben

A Microsoft rendszeresen frissíti a lemezképeket és a HDInsight erőforrás-szolgáltatót, hogy új fejlesztéseket és funkciókat tartalmazzon.

Az új HDInsight-verzió akkor hozható létre, ha a következők közül egy vagy több igaz:

- A HDInsight erőforrás-szolgáltatói funkciójának jelentősebb változásai vagy frissítései
- Az OSS-összetevők főbb kiadásai
- Az Ubuntu operációs rendszer jelentős változásai

A rendszer akkor hozza létre az új rendszerkép-verziót, ha a következők közül egy vagy több igaz:

- Az OSS-összetevők fő vagy másodlagos kiadásai és frissítései
- A rendszerkép egyik összetevőjéhez tartozó javítások vagy javítások

## <a name="next-steps"></a>Következő lépések

- [Apache-összetevők és-verziók a HDInsight-ben](./hdinsight-component-versioning.md)
