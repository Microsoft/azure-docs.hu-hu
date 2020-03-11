---
title: A Grafana használata az Azure HDInsight
description: Ismerje meg, hogyan érheti el a Grafana-irányítópultot Apache Hadoop-fürtökkel az Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/27/2019
ms.openlocfilehash: cd515bfd1dc57e78a041ed96686e1ba692bf6d3f
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2020
ms.locfileid: "79082863"
---
# <a name="access-grafana-in-azure-hdinsight"></a>Grafana elérése az Azure HDInsight

A [Grafana](https://grafana.com/) egy népszerű, nyílt forráskódú gráf-és irányítópult-szerkesztő. A Grafana funkció gazdag; nem csupán azt teszi lehetővé, hogy a felhasználók testre szabható és megosztható irányítópultokat hozzanak létre, valamint a sablonban szereplő/megírt irányítópultokat, az LDAP-integrációt, több adatforrást és egyebeket is kínál.

Jelenleg az Azure HDInsight-ben a Grafana a Spark, a HBase, a Kafka és az interaktív lekérdezési fürtök esetében támogatott. A vállalati biztonsági csomaggal rendelkező fürtök esetén nem támogatott.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-an-apache-hadoop-cluster"></a>Apache Hadoop-fürt létrehozása

Lásd: [Apache Hadoop-fürtök létrehozása a Azure Portal használatával](../hdinsight-hadoop-create-linux-clusters-portal.md). A **fürt típusa**beállításnál válassza a **Spark**, **Kafka**, **HBase**vagy **interaktív lekérdezés**lehetőséget.

## <a name="access-the-grafana-dashboard"></a>Hozzáférés a Grafana irányítópulthoz

1. Egy webböngészőből navigáljon `https://CLUSTERNAME.azurehdinsight.net/grafana/`, ahol a CLUSTERNAME a fürt neve.

1. Adja meg a Hadoop-fürt felhasználói hitelesítő adatait.

1. Megjelenik a Grafana irányítópultja, amely a következő példához hasonlóan néz ki:

    ![HDInsight Grafana webes irányítópult](./media/hdinsight-grafana/hdinsight-grafana-dashboard.png "HDInsight Grafana-irányítópult")

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje a létrehozott fürtöt a következő lépésekkel:

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com/).

1. A felső **keresőmezőbe** írja be a **HDInsight**kifejezést.

1. Válassza ki a **HDInsight-fürtök** elemet a **szolgáltatások**területen.

1. A megjelenő HDInsight-fürtök listájában kattintson a **...** elemre a létrehozott fürt mellett.

1. Válassza a **Törlés** elemet. Válassza az **Igen** lehetőséget.

## <a name="next-steps"></a>Következő lépések

A HDInsight használatával történő adatelemzésről az alábbi cikkekben talál további információt:

* [Apache Hive használata a HDInsight](../hadoop/hdinsight-use-hive.md).

* [A MapReduce használata a HDInsight használatával](../hadoop/hdinsight-use-mapreduce.md).

* Ismerkedés [a Visual Studio Hadoop-eszközök HDInsight való használatával](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
