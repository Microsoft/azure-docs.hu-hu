---
title: Támogatott adatplatformok
titleSuffix: Azure Data Science Virtual Machine
description: Ismerje meg az Azure Data Science Virtual Machine által támogatott adatplatformokat és eszközöket.
keywords: adatelemzési eszközök, adatelemző virtuális gép, eszközök adatelemzéshez, linux adatelemzés
services: machine-learning
ms.service: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 927e945a0d045abcd1caa2951dbd484224b2f425
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100519541"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>A Data Science Virtual Machine támogatott adatplatformok

Data Science Virtual Machine (DSVM) használatával számos adatplatformon létrehozhatja az elemzést. A távoli adatplatformokhoz kapcsolódó interfészeken kívül a DSVM helyi példányt biztosít a gyors fejlesztéshez és a prototípusokhoz.

A DSVM a következő adatplatform-eszközöket támogatja.

## <a name="sql-server-developer-edition"></a>SQL Server Developer Edition

| Kategória | Érték |
| ------------- | ------------- |
| mi ez?   | Helyi viszonyítási adatbázis-példány      |
| Támogatott DSVM-kiadások      | Windows 2016: SQL Server 2017, Windows 2019: SQL Server 2019      |
| Jellemző felhasználások      | <ul><li>Gyors fejlesztés helyileg, kisebb adatkészlettel</li><li>Futtatás a-adatbázisban R</li></ul> |
| Mintákra mutató hivatkozások      | <ul><li>A New York City-adathalmazok egy kis mintája betöltődik az SQL Database-be:<br/>  `nyctaxi`</li><li>A Microsoft Machine Learning Server és az adatbázison belüli elemzéseket bemutató Jupyter-minta a következő helyen található:<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`</li></ul> |
| A DSVM kapcsolódó eszközök       | <ul><li>SQL Server Management Studio</li><li>ODBC/JDBC-illesztőprogramok</li><li>pyodbc, RODBC</li><li>Apache Drill</li></ul> |

> [!NOTE]
> SQL Server Developer kiadás csak fejlesztési és tesztelési célokra használható. Az üzemi környezetben való futtatásához licencre vagy a SQL Server virtuális gépek egyikére van szükség.


### <a name="setup"></a>Beállítás

Az adatbázis-kiszolgáló már előre konfigurálva van, és a SQL Serverhoz (például) kapcsolódó Windows-szolgáltatások `SQL Server (MSSQLSERVER)` automatikus futásra vannak beállítva. Az egyetlen manuális lépés magában foglalja az adatbázis-elemzések Microsoft Machine Learning Server használatával történő engedélyezését. Az elemzés engedélyezéséhez futtassa a következő parancsot egyszeri műveletként a SQL Server Management Studio (SSMS) alkalmazásban. Futtassa ezt a parancsot a számítógép-rendszergazdaként való bejelentkezés után, nyisson meg egy új lekérdezést a SSMS, és győződjön meg arról, hogy a kiválasztott adatbázis a következő `master` :

```sql
CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 
```

(Cserélje le a% számítógépnév%-ot a virtuális gép nevére.)

SQL Server Management Studio futtatásához megkeresheti a programok listájában a "SQL Server Management Studio" kifejezést, vagy a Windows Search használatával megkeresheti és futtathatja. Amikor a rendszer kéri a hitelesítő adatokat, válassza a **Windows-hitelesítés** lehetőséget, és használja a gép nevét vagy ```localhost``` a **SQL Server neve** mezőt.

### <a name="how-to-use-and-run-it"></a>Használat és Futtatás

Alapértelmezés szerint az alapértelmezett adatbázis-példánnyal rendelkező adatbázis-kiszolgáló automatikusan lefut. Az SQL Server-adatbázis helyi eléréséhez olyan eszközöket használhat, mint a virtuális gép SQL Server Management Studio. A helyi rendszergazdai fiókok rendszergazdai hozzáféréssel rendelkeznek az adatbázishoz.

Emellett a DSVM ODBC-és JDBC-illesztőprogramokkal is rendelkezik, amelyekkel a SQL Server, az Azure SQL Database és az Azure szinapszis Analytics több nyelven írt alkalmazásokból, például a Pythonból és a Machine Learning Serverból is kommunikálhat.

### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Hogyan van konfigurálva és telepítve a DSVM? 

 A SQL Server a szabványos módon települ. A következő címen érhető el: `C:\Program Files\Microsoft SQL Server` . Az adatbázison belüli Machine Learning Server példány a következő címen található: `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES` . A DSVM külön önálló Machine Learning Server-példánnyal is rendelkezik, amely a következő helyen található: `C:\Program Files\Microsoft\R Server\R_SERVER` . Ez a két Machine Learning Server példány nem osztja meg a kódtárakat.


## <a name="apache-spark-2x-standalone"></a>Apache Spark 2. x (önálló)

| Kategória | Érték |
| ------------- | ------------- |
| mi ez?   | A népszerű Apache Spark platform önálló (egyetlen csomóponton belüli) példánya; gyors, nagy léptékű adatfeldolgozási és gépi tanulási rendszer     |
| Támogatott DSVM-kiadások      | Linux     |
| Jellemző felhasználások      | <ul><li>A Spark-és PySpark-alkalmazások gyors fejlesztése egy kisebb adatkészlettel, és a későbbi üzembe helyezés nagyméretű Spark-fürtökön, például az Azure HDInsight</li><li>Microsoft Machine Learning Server Spark-környezet tesztelése</li><li>A SparkML vagy a Microsoft nyílt forráskódú [MMLSpark](https://github.com/Azure/mmlspark) -függvénytárának használata ml-alkalmazások készítéséhez</li></ul> |
| Mintákra mutató hivatkozások      |    Jupyter minta:<ul><li>~/notebooks/SparkML/pySpark</li><li>~/notebooks/MMLSpark</li></ul><p>Microsoft Machine Learning Server (Spark Context):/dsvm/samples/MRS/MRSSparkContextSample.R</p> |
| A DSVM kapcsolódó eszközök       | <ul><li>PySpark, Scala</li><li>Jupyter (Spark/PySpark kernelek)</li><li>Microsoft Machine Learning Server, Sparker, Sparklyr</li><li>Apache Drill</li></ul> |

### <a name="how-to-use-it"></a>Használat
A (z `spark-submit` ) vagy parancs futtatásával elküldheti a Spark-feladatokat a parancssorba `pyspark` . Jupyter jegyzetfüzet létrehozásához hozzon létre egy új jegyzetfüzetet a Spark kernel segítségével.

A Spark for R használatával olyan könyvtárakat használhat, mint a Sparker, a Sparklyr és a Microsoft Machine Learning Server, amelyek elérhetők a DSVM. Lásd az előző táblázatban szereplő mintákhoz tartozó mutatókat.

### <a name="setup"></a>Beállítás
Mielőtt a (z) Ubuntu Linux DSVM kiadásban Microsoft Machine Learning Server Spark-kontextusban fut, végre kell hajtania egy egyszeri telepítési lépést, amely lehetővé teszi a helyi egycsomópontos Hadoop HDFS és a fonal példányának engedélyezését. Alapértelmezés szerint a Hadoop Services telepítve van, de le van tiltva a DSVM. Az engedélyezéshez futtassa a következő parancsokat root-ként az első alkalommal:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

Ha már nincs szüksége rájuk, leállíthatja a Hadoop kapcsolódó szolgáltatásokat ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn``` .

Egy minta, amely bemutatja, hogyan fejlesztheti és tesztelheti a MRS-t egy távoli Spark-környezetben (amely a DSVM önálló Spark-példánya), és elérhető a `/dsvm/samples/MRS` címtárban.


### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Hogyan van konfigurálva és telepítve a DSVM? 
|Platform|Telepítési hely ($SPARK _HOME)|
|:--------|:--------|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


Az Azure Blob Storage-ból vagy a Azure Data Lake Storageból származó adatok eléréséhez szükséges kódtárak a Microsoft MMLSpark gépi tanulási kódtárak használatával $SPARK _HOME/jars. Ezek a tégelyek automatikusan betöltődnek a Spark indításakor. Alapértelmezés szerint a Spark a helyi lemezen lévő adatátvitelt használja. 

Ahhoz, hogy a DSVM található Spark-példány hozzáférjen a blob Storage-ban vagy a Azure Data Lake Storageban tárolt adatfájlokhoz, a `core-site.xml` fájlt a $SPARK _HOME/conf/core-site.xml. template fájlban található sablon alapján kell létrehoznia és konfigurálnia. A blob Storage-hoz és a Azure Data Lake Storagehoz is hozzá kell férnie a megfelelő hitelesítő adatokkal. (Vegye figyelembe, hogy a sablonfájlok helyőrzőket használnak a blob Storage és a Azure Data Lake Storage konfigurációkhoz.)

Azure Data Lake Storage szolgáltatás hitelesítő adatainak létrehozásával kapcsolatos részletes információkért lásd: [hitelesítés a Azure Data Lake Storage Gen1](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md)használatával. Miután megadta a blob Storage vagy a Azure Data Lake Storage hitelesítő adatait a core-site.xml fájlban, az ezekben a forrásokban tárolt adatokra a wasb://vagy a adl://URI-előtagjának használatával hivatkozhat.