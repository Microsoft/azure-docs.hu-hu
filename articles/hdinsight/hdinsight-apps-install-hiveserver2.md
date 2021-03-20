---
title: HiveServer2 méretezése az Azure HDInsight
description: Horizontálisan méretezhető HiveServer2 az Azure HDInsight-fürtökön a peremhálózati csomópontok segítségével növelheti a hibatűrést és a rendelkezésre állást.
services: hdinsight
ms.service: hdinsight
ms.topic: conceptual
ms.reviewer: hrasheed-msft
ms.author: kecheung
author: kcheeeung
ms.date: 08/12/2020
ms.openlocfilehash: d0a0df4791492c1c9f0d600630d723024c46c1b8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88227193"
---
# <a name="scale-hiveserver2-on-azure-hdinsight-clusters-for-high-availability"></a>Az Azure HDInsight-fürtök HiveServer2 méretezése magas rendelkezésre állás érdekében

Megtudhatja, hogyan helyezhet üzembe egy további HiveServer2 a fürtön a rendelkezésre állás és a terhelés elosztásának növeléséhez. Ha nem kívánja megnövelni a átjárócsomóponthoz méretét, a peremhálózati csomópontok használatával is telepítheti a HiveServer2. 

> [!NOTE]
> A használattól függően a HiveServer2 számának növelése növelheti a Hive-metaadattár kapcsolatainak számát. Győződjön meg arról is, hogy az Azure SQL-adatbázis megfelelő méretű.

## <a name="prerequisites"></a>Előfeltételek

Az útmutató használatához meg kell ismernie a következő cikket:
- [Üres peremhálózati csomópontok használata Apache Hadoop-fürtökön a HDInsight-ben](hdinsight-apps-use-edge-node.md)

## <a name="install-hiveserver2"></a>A HiveServer2 telepítése

Ebben a szakaszban egy további HiveServer2 fog telepíteni a cél gazdagépekre.

1. Nyissa meg a Ambari a böngészőben, és kattintson a kívánt gazdagépre.

:::image type="content" source="media/hdinsight-apps-install-hiveserver2/hdinsight-install-hiveserver2-a.png" alt-text="A Ambari gazdagépek menüje.":::

2. Kattintson a Hozzáadás gombra, és kattintson a HiveServer2 elemre.

:::image type="content" source="media/hdinsight-apps-install-hiveserver2/hdinsight-install-hiveserver2-b.png" alt-text="Adja hozzá a HiveServer2 panelt a gazdagéphez.":::

3. Erősítse meg, hogy a folyamat futni fog. Ismételje meg a 1-3-et az összes kívánt gazdagép esetében.

4. A telepítés befejezése után indítsa újra az összes szolgáltatást elavult konfigurációkkal, és indítsa el a HiveServer2.

:::image type="content" source="media/hdinsight-apps-install-hiveserver2/hdinsight-install-hiveserver2-c.png" alt-text="Indítsa el a HiveServer2 panelt.":::

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan telepítheti a HiveServer2-t a fürtre. Az Edge-csomópontokkal és-alkalmazásokkal kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

* Az [Edge-csomópont telepítése](hdinsight-apps-use-edge-node.md): megtudhatja, hogyan telepíthet egy Edge-csomópontot a HDInsight-fürtre.
* [HDInsight-alkalmazások telepítése](hdinsight-apps-install-applications.md): Megtudhatja, hogyan telepíthet HDInsight-alkalmazásokat a fürtjeire.
* [Azure SQL DTU-kapcsolatok korlátai](../azure-sql/database/resource-limits-dtu-single-databases.md): az Azure SQL Database korlátainak megismerése a DTU használatával.
* [Azure SQL virtuális mag-kapcsolatok korlátai](../azure-sql/database/resource-limits-vcore-elastic-pools.md): az Azure SQL Database korlátainak megismerése a virtuális mag használatával.
