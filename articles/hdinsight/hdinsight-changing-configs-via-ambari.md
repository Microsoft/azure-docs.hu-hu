---
title: Fürtök optimalizálása Apache Ambari az Azure HDInsight
description: Az Apache Ambari webes FELÜLETének használatával konfigurálhatja és optimalizálhatja az Azure HDInsight-fürtöket.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 05/04/2020
ms.openlocfilehash: 2146ccb0c4d7f263c3e1a69db9b172649fcd25ea
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863496"
---
# <a name="optimize-clusters-with-apache-ambari-in-azure-hdinsight"></a>Fürtök optimalizálása Apache Ambari az Azure HDInsight

A HDInsight Apache Hadoop fürtöket biztosít nagyméretű adatfeldolgozási alkalmazásokhoz. Az összetett, több csomópontot tartalmazó fürtök kezelése, monitorozása és optimalizálása kihívást jelenthet. Az Apache Ambari egy webes felület a HDInsight Linux-fürtök felügyeletéhez és figyeléséhez.

A Ambari webes felhasználói felületének bevezetését lásd: [HDInsight-fürtök kezelése az Apache Ambari Web UI használatával](hdinsight-hadoop-manage-ambari.md)

Jelentkezzen be a Ambari-ba a `https://CLUSTERNAME.azurehdidnsight.net` fürt hitelesítő adataival. A kezdeti képernyő egy áttekintő irányítópultot jelenít meg.

:::image type="content" source="./media/hdinsight-changing-configs-via-ambari/apache-ambari-dashboard.png" alt-text="Az Apache Ambari felhasználói irányítópultja megjelenik":::

A Ambari webes felhasználói felülete a gazdagépek, a szolgáltatások, a riasztások, a konfigurációk és a nézetek kezelésére szolgál. A Ambari nem használható HDInsight-fürt létrehozásához vagy a szolgáltatások frissítéséhez. A nem tudja kezelni a halmokat és a verzióit, a leszerelést vagy a gazdagépek leszerelését, illetve a szolgáltatások hozzáadását a fürthöz.

## <a name="manage-your-clusters-configuration"></a>A fürt konfigurációjának kezelése

A konfigurációs beállítások segítenek egy adott szolgáltatás finomhangolásában. A szolgáltatás konfigurációs beállításainak módosításához válassza ki a szolgáltatást a **szolgáltatások** oldalsávról (a bal oldalon). Ezután keresse meg a **konfigurációk** lapot a szolgáltatás részletei lapon.

:::image type="content" source="./media/hdinsight-changing-configs-via-ambari/ambari-services-sidebar.png" alt-text="Apache Ambari Services oldalsáv":::

## <a name="modify-namenode-java-heap-size"></a>NameNode Java-halom méretének módosítása

A NameNode Java-halom mérete számos tényezőtől függ, például a fürt terhelésével. Továbbá a fájlok száma és a blokkok száma. Az alapértelmezett 1 GB-os méret a legtöbb fürttel jól működik, bár egyes munkaterhelések esetén több vagy kevesebb memória szükséges.

Az NameNode Java-halom méretének módosítása:

1. Válassza a **HDFS** elemet a szolgáltatások oldalsávon, és navigáljon a **konfigurációk** lapra.

    :::image type="content" source="./media/hdinsight-changing-configs-via-ambari/ambari-apache-hdfs-config.png" alt-text="Apache Ambari HDFS-konfiguráció":::

1. A Java- **NameNode** beállításának megkeresése. A **szűrő** szövegmezővel egy adott beállítást is beírhat, és megkeresheti azt. Válassza a **toll** ikont a beállítás neve mellett.

    :::image type="content" source="./media/hdinsight-changing-configs-via-ambari/ambari-java-heap-size.png" alt-text="Apache Ambari NameNode Java halom mérete":::

1. Írja be az új értéket a szövegmezőbe, majd nyomja le az **ENTER** billentyűt a módosítás mentéséhez.

    :::image type="content" source="./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit1.png" alt-text="Ambari szerkesztése NameNode Java heap Size1":::

1. A NameNode Java-halom mérete 1 GB-ra módosul 2 GB-ról.

    :::image type="content" source="./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png" alt-text="Szerkesztett NameNode Java heap size2":::

1. Mentse a módosításokat a konfigurációs képernyő felső részén található zöld **Mentés** gombra kattintva.

    :::image type="content" source="./media/hdinsight-changing-configs-via-ambari/ambari-save-changes1.png" alt-text="&quot;Apache Ambari-mentési konfigurációk&quot;":::

## <a name="next-steps"></a>Következő lépések

* [HDInsight-fürtök kezelése az Apache Ambari webes FELÜLETtel](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Az Apache HBase optimalizálása](./optimize-hbase-ambari.md)
* [Az Apache Hive optimalizálása](./optimize-hive-ambari.md)
* [Az Apache Pig optimalizálása](./optimize-pig-ambari.md)
