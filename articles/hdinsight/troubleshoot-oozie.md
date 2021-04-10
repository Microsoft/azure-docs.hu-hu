---
title: Az Apache Oozie hibáinak megoldása az Azure HDInsight
description: Bizonyos Apache Oozie hibák elhárítása az Azure HDInsight-ben.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.openlocfilehash: 2cdd20a5d639f74916657edc3f73183a403204a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944660"
---
# <a name="troubleshoot-apache-oozie-in-azure-hdinsight"></a>Az Apache Oozie hibáinak megoldása az Azure HDInsight

Az Apache Oozie felhasználói felületén megtekintheti a Oozie naplókat. A Oozie felhasználói felülete a munkafolyamat által elindított MapReduce-feladatok JobTracker-naplóira mutató hivatkozásokat is tartalmaz. A hibaelhárítási minta a következő lehet:

1. Tekintse meg a feladatot a Oozie webes felhasználói felületén.

2. Ha egy adott művelet hibával vagy hibával rendelkezik, válassza ki a műveletet, és ellenőrizze, hogy a **hibaüzenet** mezője tartalmaz-e további információt a hibáról.

3. Ha elérhető, a művelet URL-címével megtekintheti a művelethez tartozó további részleteket, például a JobTracker naplókat.

A következő konkrét hibák merülhetnek fel, és elháríthatja azokat.

## <a name="ja009-cant-initialize-cluster"></a>JA009: nem lehet inicializálni a fürtöt

### <a name="issue"></a>Probléma

A feladatok állapota **felfüggesztve** értékre változik. A feladathoz tartozó részletek az `RunHiveScript` állapotot **START_MANUALként** jelenítik meg. A művelet kiválasztásakor a következő hibaüzenet jelenik meg:

```output
JA009: Cannot initialize Cluster. Please check your configuration for map
```

### <a name="cause"></a>Ok

Az **job.xml** fájlban használt Azure Blob Storage-címek nem tartalmazzák a tároló-vagy a Storage-fiók nevét. A blob Storage-címnek a formátumnak kell lennie `wasbs://containername@storageaccountname.blob.core.windows.net` .

### <a name="resolution"></a>Feloldás

Módosítsa a feladatok által használt BLOB Storage-címeket.

---

## <a name="ja002-oozie-isnt-allowed-to-impersonate-ltusergt"></a>JA002: a Oozie nem jogosult megszemélyesíteni a &lt; felhasználót&gt;

### <a name="issue"></a>Probléma

A feladatok állapota **felfüggesztve** értékre változik. A feladathoz tartozó részletek az `RunHiveScript` állapotot **START_MANUALként** jelenítik meg. Ha a műveletet választja, a következő hibaüzenet jelenik meg:

```output
JA002: User: oozie is not allowed to impersonate <USER>
```

### <a name="cause"></a>Ok

Az aktuális engedélyezési beállítások nem teszik lehetővé, hogy a Oozie megszemélyesítse a megadott felhasználói fiókot.

### <a name="resolution"></a>Feloldás

A Oozie megszemélyesítheti a csoport felhasználóit **`users`** . A használatával `groups USERNAME` megtekintheti azokat a csoportokat, amelyeknek a felhasználói fiók tagja. Ha a felhasználó nem tagja a **`users`** csoportnak, akkor a következő paranccsal adhatja hozzá a felhasználót a csoporthoz:

```bash
sudo adduser USERNAME users
```

> [!NOTE]  
> Több percet is igénybe vehet, mielőtt a HDInsight észleli, hogy a felhasználó hozzá lett adva a csoporthoz.

---

## <a name="launcher-error-sqoop"></a>Indító hiba (Sqoop)

### <a name="issue"></a>Probléma

A feladatok állapotának **leölése** megtörtént. A feladathoz tartozó részletek az `RunSqoopExport` állapotot **hibaként** jelenítik meg. Ha a műveletet választja, a következő hibaüzenet jelenik meg:

```output
Launcher ERROR, reason: Main class [org.apache.oozie.action.hadoop.SqoopMain], exit code [1]
```

### <a name="cause"></a>Ok

A Sqoop nem tudja betölteni az adatbázis eléréséhez szükséges adatbázis-illesztőt.

### <a name="resolution"></a>Feloldás

Ha a Sqoop-t egy Oozie-feladatokból használja, akkor az adatbázis-illesztőt is fel kell vennie a többi erőforrással, például a workflow.xml, a feladatokhoz. A workflow.xml szakaszának adatbázis-illesztőprogramját tartalmazó archívumra is hivatkozhat `<sqoop>...</sqoop>` .

Például a [Hadoop Oozie-munkafolyamatok használata](hdinsight-use-oozie-linux-mac.md)a következő lépésekkel végezhető el:

1. Másolja a `mssql-jdbc-7.0.0.jre8.jar` fájlt a **/tutorials/useoozie** könyvtárba:

    ```bash
    hdfs dfs -put /usr/share/java/sqljdbc_7.0/enu/mssql-jdbc-7.0.0.jre8.jar /tutorials/useoozie/mssql-jdbc-7.0.0.jre8.jar
    ```

2. Módosítsa a `workflow.xml` t a következő XML-kód hozzáadásához a fenti új sorban `</sqoop>` :

    ```xml
    <archive>mssql-jdbc-7.0.0.jre8.jar</archive>
    ```

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]