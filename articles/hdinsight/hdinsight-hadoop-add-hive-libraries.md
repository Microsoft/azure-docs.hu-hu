---
title: Könyvtárak Apache Hive a fürt létrehozása során – Azure HDInsight
description: Megtudhatja, hogyan adhat hozzá Apache Hive kódtárakat (jar-fájlokat) egy HDInsight-fürthöz a fürt létrehozása során.
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 02/14/2020
ms.openlocfilehash: b6695e5e985a30d6f912095225c4899e1c910e34
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945951"
---
# <a name="add-custom-apache-hive-libraries-when-creating-your-hdinsight-cluster"></a>Egyéni Apache Hive-kódtárak hozzáadása a HDInsight-fürt létrehozásakor

Megtudhatja, hogyan tölthetők be [Apache Hive](https://hive.apache.org/) -kódtárak a HDInsight. Ez a dokumentum a fürt létrehozása során a kódtárak előzetes betöltésére szolgáló parancsfájl-művelettel kapcsolatos információkat tartalmaz. A jelen dokumentumban ismertetett lépések használatával hozzáadott könyvtárak globálisan elérhetők a kaptárban – nem szükséges a [jar hozzáadása](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) a betöltéshez.

## <a name="how-it-works"></a>Működés

Fürt létrehozásakor parancsfájl-művelettel módosíthatja a fürt csomópontjait a létrehozásuk során. A dokumentumban szereplő parancsfájl egyetlen paramétert fogad el, amely a kódtárak helye. Ennek a helynek egy Azure Storage-fiókban kell lennie, és a kódtárakat jar-fájlként kell tárolni.

A fürt létrehozása során a parancsfájl enumerálja a fájlokat, átmásolja őket a `/usr/lib/customhivelibs/` fej és a munkavégző csomópontok könyvtárába, majd hozzáadja azokat a `hive.aux.jars.path` fájlban lévő tulajdonsághoz `core-site.xml` . A Linux-alapú fürtökön a `hive-env.sh` fájl helyét is frissíti.

A cikkben szereplő parancsfájl-művelettel elérhetővé válik a kódtárak használata a **webhcaten** és a **HiveServer2**.

## <a name="the-script"></a>A parancsfájl

**Szkript helye**

[https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

### <a name="requirements"></a>Követelmények

* A parancsfájlokat a **főcsomópontokra** és a **munkavégző csomópontokra** egyaránt alkalmazni kell.

* A telepíteni kívánt tégelyeket **egyetlen tárolóban** kell tárolni az Azure Blob Storageban.

* A jar **-fájlok** könyvtárát tartalmazó Storage-fióknak a létrehozás során a HDInsight-fürthöz kell kapcsolódnia. Az alapértelmezett Storage-fióknak vagy a __Storage-fiók beállításain__ keresztül hozzáadott fióknak kell lennie.

* A tároló WASB elérési útját paraméterként kell megadni a parancsfájl művelethez. Ha például az adattárolók a **mystorage** nevű Storage-fiókban a **libs** nevű tárolóban tárolódnak, akkor a paraméter a következő lesz: `wasbs://libs@mystorage.blob.core.windows.net/` .

  > [!NOTE]  
  > Ez a dokumentum azt feltételezi, hogy már létrehozott egy Storage-fiókot, egy BLOB-tárolót, és feltöltötte a fájlokat.
  >
  > Ha nem hozott létre Storage-fiókot, azt a [Azure Portalon](https://portal.azure.com)keresztül teheti meg. Ezután használhat olyan segédprogramot, mint például a [Azure Storage Explorer](https://storageexplorer.com/) , amely létrehoz egy tárolót a fiókban, és feltölti a fájlokat.

## <a name="create-a-cluster-using-the-script"></a>Fürt létrehozása a parancsfájl használatával

1. Hozzon létre egy fürtöt a [HDInsight-fürtök Linuxon](hdinsight-hadoop-provision-linux-clusters.md)való üzembe helyezésének lépéseivel, de ne fejezze be a telepítést. A parancsfájl használatával a Azure PowerShell vagy a HDInsight .NET SDK használatával is létrehozhat fürtöt. A módszerek használatával kapcsolatos további információkért lásd: [HDInsight-fürtök testreszabása parancsfájl-műveletekkel](hdinsight-hadoop-customize-cluster-linux.md). A Azure Portal a **konfiguráció + díjszabás** lapon válassza a **+ parancsfájl hozzáadása műveletet**.

1. A **Storage** esetében, ha a JAR-fájlok könyvtárát tartalmazó Storage-fiók eltér a fürthöz használt fióktól, akkor végezze el a **további Storage-fiókokat**.

1. A **parancsfájlok műveleteihez** adja meg a következő információkat:

    |Tulajdonság |Érték |
    |---|---|
    |Parancsfájl típusa|– Egyéni|
    |Name|Kódtárak |
    |Bash-parancsfájl URI-ja|`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`|
    |Csomópont típusa (i)|Head, Worker|
    |Paraméterek|Adja meg az WASB-címeket az edényt tartalmazó tároló-és Storage-fiókhoz. Például: `wasbs://libs@mystorage.blob.core.windows.net/`.|

    > [!NOTE]
    > Apache Spark 2,1 esetében használja ezt a bash-parancsfájl URI-JÁT: `https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v00.sh` .

1. Folytassa a fürt kiépítését a [Linuxon futó HDInsight-fürtök kiépítése](hdinsight-hadoop-provision-linux-clusters.md)című témakörben leírtak szerint.

Miután a fürt létrehozása befejeződött, használhatja az ezen a parancsfájlon keresztül hozzáadott tégelyeket a kaptáron anélkül, hogy az utasítást kellene használnia `ADD JAR` .

## <a name="next-steps"></a>Következő lépések

További információ a kaptár [használatáról: Apache Hive használata a HDInsight](hadoop/hdinsight-use-hive.md)
