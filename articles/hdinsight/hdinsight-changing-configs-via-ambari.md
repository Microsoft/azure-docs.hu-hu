---
title: Fürtök optimalizálása Apache Ambari az Azure HDInsight
description: Az Apache Ambari webes FELÜLETének használatával konfigurálhatja és optimalizálhatja az Azure HDInsight-fürtöket.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 05/04/2020
ms.openlocfilehash: 52eb1b6c89ff02cb44fe731c2463ab02c284f26c
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86086449"
---
# <a name="optimize-clusters-with-apache-ambari-in-azure-hdinsight"></a>Fürtök optimalizálása Apache Ambari az Azure HDInsight

A HDInsight Apache Hadoop fürtöket biztosít nagyméretű adatfeldolgozási alkalmazásokhoz. Az összetett, több csomópontot tartalmazó fürtök kezelése, monitorozása és optimalizálása kihívást jelenthet. Az Apache Ambari egy webes felület a HDInsight Linux-fürtök felügyeletéhez és figyeléséhez.

A Ambari webes felhasználói felületének bevezetését lásd: [HDInsight-fürtök kezelése az Apache Ambari Web UI használatával](hdinsight-hadoop-manage-ambari.md)

Jelentkezzen be a Ambari-ba a `https://CLUSTERNAME.azurehdidnsight.net` fürt hitelesítő adataival. A kezdeti képernyő egy áttekintő irányítópultot jelenít meg.

![Az Apache Ambari felhasználói irányítópultja megjelenik](./media/hdinsight-changing-configs-via-ambari/apache-ambari-dashboard.png)

A Ambari webes felhasználói felülete a gazdagépek, a szolgáltatások, a riasztások, a konfigurációk és a nézetek kezelésére szolgál. A Ambari nem használható HDInsight-fürt létrehozásához vagy a szolgáltatások frissítéséhez. A nem tudja kezelni a halmokat és a verzióit, a leszerelést vagy a gazdagépek leszerelését, illetve a szolgáltatások hozzáadását a fürthöz.

## <a name="manage-your-clusters-configuration"></a>A fürt konfigurációjának kezelése

A konfigurációs beállítások segítenek egy adott szolgáltatás finomhangolásában. A szolgáltatás konfigurációs beállításainak módosításához válassza ki a szolgáltatást a **szolgáltatások** oldalsávról (a bal oldalon). Ezután keresse meg a **konfigurációk** lapot a szolgáltatás részletei lapon.

![Apache Ambari Services oldalsáv](./media/hdinsight-changing-configs-via-ambari/ambari-services-sidebar.png)

## <a name="modify-namenode-java-heap-size"></a>NameNode Java-halom méretének módosítása

A NameNode Java-halom mérete számos tényezőtől függ, például a fürt terhelésével. Továbbá a fájlok száma és a blokkok száma. Az alapértelmezett 1 GB-os méret a legtöbb fürttel jól működik, bár egyes munkaterhelések esetén több vagy kevesebb memória szükséges.

Az NameNode Java-halom méretének módosítása:

1. Válassza a **HDFS** elemet a szolgáltatások oldalsávon, és navigáljon a **konfigurációk** lapra.

    ![Apache Ambari HDFS-konfiguráció](./media/hdinsight-changing-configs-via-ambari/ambari-apache-hdfs-config.png)

1. A Java- **NameNode**beállításának megkeresése. A **szűrő** szövegmezővel egy adott beállítást is beírhat, és megkeresheti azt. Válassza a **toll** ikont a beállítás neve mellett.

    ![Apache Ambari NameNode Java halom mérete](./media/hdinsight-changing-configs-via-ambari/ambari-java-heap-size.png)

1. Írja be az új értéket a szövegmezőbe, majd nyomja le az **ENTER** billentyűt a módosítás mentéséhez.

    ![Ambari szerkesztése NameNode Java heap Size1](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edit1.png)

1. A NameNode Java-halom mérete 1 GB-ra módosul 2 GB-ról.

    ![Szerkesztett NameNode Java heap size2](./media/hdinsight-changing-configs-via-ambari/java-heap-size-edited.png)

1. Mentse a módosításokat a konfigurációs képernyő felső részén található zöld **Mentés** gombra kattintva.

    !["Apache Ambari-mentési konfigurációk"](./media/hdinsight-changing-configs-via-ambari/ambari-save-changes1.png)

## <a name="next-steps"></a>További lépések

* [HDInsight-fürtök kezelése az Apache Ambari webes FELÜLETtel](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Az Apache HBase optimalizálása](./optimize-hbase-ambari.md)
* [Az Apache Hive optimalizálása](./optimize-hive-ambari.md)
* [Az Apache Pig optimalizálása](./optimize-pig-ambari.md)
