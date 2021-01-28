---
title: Az Azure HDInsight Apache Spark hibáinak megoldása
description: Választ kaphat a Apache Spark és az Azure HDInsight való használattal kapcsolatos gyakori kérdésekre.
ms.service: hdinsight
ms.reviewer: jasonh
ms.topic: troubleshooting
ms.date: 08/22/2019
ms.custom: seodec18
ms.openlocfilehash: af488cd253e8a8ebedd838aa5286185ea556f69d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942512"
---
# <a name="troubleshoot-apache-spark-by-using-azure-hdinsight"></a>Az Apache Spark hibaelhárítása az Azure HDInsighttal

Ismerje meg a leggyakoribb problémákat és azok megoldásait, amikor Apache Spark hasznos adatokkal dolgozik az [Apache Ambari](https://ambari.apache.org/).

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters"></a>Hogyan konfigurálható egy Apache Spark-alkalmazás az Apache Ambari fürtökön történő használatával?

A Spark konfigurációs értékei a Apache Spark alkalmazás kivételének elkerülése érdekében állíthatók be `OutofMemoryError` . Az alábbi lépések az alapértelmezett Spark-konfigurációs értékeket mutatják be az Azure HDInsight:

1. Jelentkezzen be a Ambari-ba a `https://CLUSTERNAME.azurehdidnsight.net` fürt hitelesítő adataival. A kezdeti képernyő egy áttekintő irányítópultot jelenít meg. A HDInsight 3,6 és 4,0 közötti enyhe kozmetikai különbségek vannak.

1. Navigáljon a **Spark2**-  >  **konfigurációkhoz**.

    ![Válassza a konfigurációk fület](./media/apache-troubleshoot-spark/apache-spark-ambari-config2.png)

1. A konfigurációk listájában válassza ki és bontsa ki az **Egyéni-spark2 – alapértelmezett értékeket**.

1. Keresse meg a módosítani kívánt érték beállítást, például **spark.executor. Memory**. Ebben az esetben a **9728m** értéke túl magas.

    ![Egyéni-Spark – alapértékek kiválasztása](./media/apache-troubleshoot-spark/apache-spark-ambari-config4.png)

1. Állítsa be az értéket az ajánlott beállításra. Ehhez a beállításhoz a **2048m** érték használata javasolt.

1. Mentse az értéket, majd mentse a konfigurációt. Kattintson a **Mentés** gombra.

    ![Érték módosítása 2048m](./media/apache-troubleshoot-spark/apache-spark-ambari-config6a.png)

    Jegyezze fel a konfigurációs módosításokat, majd kattintson a **Mentés** gombra.

    ![Adjon meg egy megjegyzést az elvégzett módosításokról](./media/apache-troubleshoot-spark/apache-spark-ambari-config6c.png)

    Értesítést kap, ha bármilyen konfigurációhoz beavatkozásra van szükség. Jegyezze fel az elemeket, majd válassza a **Folytatás egyébként** lehetőséget.

    ![Válassza a folytatás](./media/apache-troubleshoot-spark/apache-spark-ambari-config6b.png)

1. Amikor egy konfiguráció mentve lett, a rendszer felszólítja a szolgáltatás újraindítására. Válassza az **Újraindítás** lehetőséget.

    ![Újraindítás kiválasztása](./media/apache-troubleshoot-spark/apache-spark-ambari-config7a.png)

    Erősítse meg az újraindítást.

    ![Válassza az összes újraindításának megerősítése lehetőséget.](./media/apache-troubleshoot-spark/apache-spark-ambari-config7b.png)

    Áttekintheti a futó folyamatokat.

    ![Futó folyamatok áttekintése](./media/apache-troubleshoot-spark/apache-spark-ambari-config7c.png)

1. Hozzáadhat konfigurációkat is. A konfigurációk listájában válassza az **Egyéni-spark2-alapértékek** lehetőséget, majd kattintson a **tulajdonság hozzáadása** lehetőségre.

    ![Válassza a tulajdonság hozzáadása lehetőséget](./media/apache-troubleshoot-spark/apache-spark-ambari-config8.png)

1. Adjon meg egy új tulajdonságot. Egyetlen tulajdonságot az egyes beállításokhoz, például az adattípushoz tartozó párbeszédpanel használatával adhat meg. Másik lehetőségként több tulajdonságot is megadhat, ha egy definíciót használ soronként.

    Ebben a példában a **Spark. Driver. Memory** tulajdonság a **4G** értékkel van definiálva.

    ![Új tulajdonság definiálása](./media/apache-troubleshoot-spark/apache-spark-ambari-config9.png)

1. Mentse a konfigurációt, majd indítsa újra a szolgáltatást a 6. és 7. lépésekben leírtak szerint.

Ezek a változások a fürtre kiterjedő, de a Spark-feladatok elküldésekor felülbírálható.

## <a name="how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>Hogyan konfiguráljon egy Apache Spark alkalmazást a fürtökön lévő Jupyter Notebook használatával?

A Jupyter Notebook első cellájában a **(z)%% configure** direktíva után adja meg a Spark-konfigurációkat érvényes JSON formátumban. Módosítsa a tényleges értékeket szükség szerint:

![Konfiguráció hozzáadása](./media/apache-troubleshoot-spark/add-configuration-cell.png)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters"></a>Hogyan konfigurálható egy Apache Spark-alkalmazás az Apache Livy fürtökön történő használatával?

Küldje el a Spark-alkalmazást a Livy egy REST-ügyfél, például a cURL használatával. Használjon az alábbihoz hasonló parancsot. Módosítsa a tényleges értékeket szükség szerint:

```apache
curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
```

## <a name="how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters"></a>Hogyan konfigurálható egy Apache Spark-alkalmazás a spark-submit fürtökön történő használatával?

Indítsa el a Spark-shellt az alábbihoz hasonló parancs használatával. Szükség szerint módosítsa a konfigurációk tényleges értékét:

```apache
spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
```

### <a name="additional-reading"></a>További információ

[Apache Spark a feladatok beküldését a HDInsight-fürtökön](https://web.archive.org/web/20190112152841/https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/)

## <a name="next-steps"></a>Következő lépések

Ha nem látja a problémát, vagy nem tudja megoldani a problémát, további támogatásért látogasson el az alábbi csatornák egyikére:

* A [Spark memória-kezelési áttekintése](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [A Spark-alkalmazás hibakeresése a HDInsight-fürtökön](/archive/blogs/azuredatalake/spark-debugging-101).

* Azure-szakértőktől kaphat válaszokat az [Azure közösségi támogatásával](https://azure.microsoft.com/support/community/).

* Kapcsolódjon [@AzureSupport](https://twitter.com/azuresupport) a-a hivatalos Microsoft Azure fiókhoz a felhasználói élmény javítása érdekében. Az Azure-Közösség összekapcsolása a megfelelő erőforrásokkal: válaszok, támogatás és szakértők.

* Ha további segítségre van szüksége, támogatási kérést küldhet a [Azure Portaltól](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Válassza a menüsor **támogatás** elemét, vagy nyissa meg a **Súgó + támogatás** hubot. Részletesebb információkért tekintse át az [Azure-támogatási kérelem létrehozását](../../azure-portal/supportability/how-to-create-azure-support-request.md)ismertető témakört. Az előfizetés-kezeléshez és a számlázási támogatáshoz való hozzáférés a Microsoft Azure-előfizetés része, és a technikai támogatás az egyik [Azure-támogatási csomagon](https://azure.microsoft.com/support/plans/)keresztül érhető el.