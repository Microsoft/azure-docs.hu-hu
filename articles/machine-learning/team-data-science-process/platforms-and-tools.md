---
title: Adatelemzési projektek platformja és eszközei – csoportos adatelemzési folyamat
description: Körvonalazza és ismerteti a csoportos adatelemzési folyamattal szabványosított vállalatok számára elérhető adatelemzési és elemzési erőforrásokat.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4ba7b8af9b50b9173f5e2040bb8b623eeafdd538
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96453862"
---
# <a name="platforms-and-tools-for-data-science-projects"></a>Az adatelemzési projektek platformja és eszközei

A Microsoft a felhőalapú és a helyszíni platformokhoz egyaránt teljes körű elemzési erőforrásokat biztosít. Üzembe helyezhetők az adatelemzési projektek hatékony és skálázható végrehajtásához. Útmutató az adatelemzési projektek nyomon követésére szolgáló, ellenőrzött, és együttműködési módon megvalósított csapatok számára a [csoportos adatelemzési folyamat](overview.md) (TDSP) révén.  A munkatársak szerepköreinek, valamint a folyamaton alapuló adatelemzési csapat által kezelt kapcsolódó feladatoknak a vázlatát lásd: [csoportos adatelemzési folyamat szerepkörei és feladatai](roles-tasks.md).

A TDSP használó adatelemző csapatok számára elérhető elemzési erőforrások a következők:

- Adatelemzési Virtual Machines (Windows és Linux CentOS)
- HDInsight Spark-fürtök
- Azure Synapse Analytics
- Azure Data Lake
- HDInsight-struktúra fürtök
- Azure File Storage
- SQL Server 2019 R és Python szolgáltatások
- Azure Databricks

Ebben a dokumentumban röviden leírjuk az erőforrásokat, és hivatkozásokat biztosítunk a TDSP csapatok által közzétett oktatóanyagokhoz és útmutatóhoz. Segítségükkel megtudhatja, hogyan használhatja őket lépésről lépésre, és hogyan kezdheti el használni őket az intelligens alkalmazások létrehozásához. Az erőforrásokkal kapcsolatos további információk a termék lapjain érhetők el. 

## <a name="data-science-virtual-machine-dsvm"></a>Data Science Virtual Machine (DSVM)

A Microsoft által a Windows és a Linux rendszeren egyaránt kínált adatelemzési virtuális gép népszerű eszközöket tartalmaz az adatelemzési modellezéshez és a fejlesztési tevékenységekhez. Olyan eszközöket tartalmaz, mint például a következők:

- Microsoft R Server Developer Edition 
- Anaconda Python-disztribúció
- Jupyter-jegyzetfüzetek a Pythonhoz és az R-hez 
- Visual Studio Community Edition Python és R Tools on Windows/Eclipse Linux rendszeren
- Windows asztali Power BI
- SQL Server 2016 fejlesztői kiadás Windows/postgres Linux rendszeren

Ide tartozik a **ml és AI-eszközök** is, például a xgboost, a mxnet és a Vowpal Wabbit.

Jelenleg a DSVM a **Windows** és a **Linux CentOS** operációs rendszerekben érhető el. Válassza ki a DSVM méretét (a CPU-magok számát és a memória mennyiségét) azon adatelemzési projektek igényei alapján, amelyeket végre szeretne hajtani. 

A DSVM Windows-kiadásával kapcsolatos további információkért lásd: [Microsoft Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019) az Azure piactéren. A DSVM Linux-kiadását lásd: [linux Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804).

Ha szeretné megtudni, hogyan hajthat végre a közös adatelemzési feladatokat a DSVM hatékonyan, tekintse meg [az adatelemzési virtuális gépen 10 dolgot](../data-science-virtual-machine/vm-do-ten-things.md) .


## <a name="azure-hdinsight-spark-clusters"></a>Fürtök Azure HDInsight Spark

Az Apache Spark egy nyílt forráskódú, párhuzamos feldolgozást végző keretrendszer, amely támogatja a memóriabeli feldolgozást a big data elemző alkalmazások teljesítményének növelése érdekében. A Spark-feldolgozó motor a sebességre, a könnyű használatra és a kifinomult elemzésekre épül. A Spark memóriában tárolt számítási képességei jó választást biztosítanak a gépi tanulásban és a Graph-számításokban az ismétlődő algoritmusokhoz. A Spark az Azure Blob Storage (WASB) szolgáltatással is kompatibilis, így az Azure-ban tárolt meglévő adatai könnyen feldolgozhatók a Spark használatával.

Amikor Spark-fürtöt hoz létre a HDInsightban, olyan Azure számítási erőforrásokat is létrehoz, amelyeken a Spark telepítve és konfigurálva van. Egy Spark-fürt létrehozása körülbelül 10 percet vesz igénybe a HDInsight-ben. Tárolja az Azure Blob Storage-ban feldolgozandó adatfeldolgozási folyamatokat. Az Azure Blob Storage fürttel való használatáról további információért lásd: a [HDFS-kompatibilis Azure Blob Storage használata a Hadoop a HDInsight-ben](../../hdinsight/hdinsight-hadoop-use-blob-storage.md).

A Microsoft TDSP csapata két végpontok közötti bemutatót tett közzé, amelyekkel Azure HDInsight Spark-fürtöket hozhat létre adatelemzési megoldások létrehozásához, amely a Python és a többi Scala használatával történik. Az Azure HDInsight **Spark-fürtökkel** kapcsolatos további információkért tekintse [meg a HDInsight Linux – Áttekintés: Apache Spark](../../hdinsight/spark/apache-spark-overview.md)című témakört. Ha szeretné megismerni, hogyan hozhat létre adatelemzési megoldást a **Python** használatával egy Azure HDInsight Spark-fürtön, tekintse meg [az adatelemzés áttekintése a Spark használatával az Azure HDInsight](spark-overview.md)című cikket. Ha meg szeretné tudni, hogyan hozhat létre adatelemzési megoldást a **Scala** használatával egy Azure HDInsight Spark-fürtön, tekintse meg az [adatelemzést a Scala és a Spark Azure-beli használatával](scala-walkthrough.md)című 


##  <a name="azure-synapse-analytics"></a>Azure Synapse Analytics

Az Azure szinapszis Analytics segítségével könnyedén és másodpercek alatt méretezheti a számítási erőforrásokat, anélkül, hogy túlterhelést vagy túlfizetést kellene fizetnie. Emellett egyedi lehetőséget biztosít a számítási erőforrások használatának szüneteltetésére is, így biztosítva a Felhőbeli költségek jobb kezelését. A méretezhető számítási erőforrások üzembe helyezésének képessége lehetővé teszi az adatok Azure szinapszis-elemzésbe való bevezetését. A tárolási költségek minimálisak, és csak az elemezni kívánt adatkészletek részein lehet számítást futtatni. 

Az Azure szinapszis Analytics szolgáltatással kapcsolatos további információkért tekintse meg az [Azure szinapszis Analytics](https://azure.microsoft.com/services/sql-data-warehouse) webhelyét. Ha szeretné megtudni, hogyan hozhat létre teljes körű, fejlett elemzési megoldásokat az Azure szinapszis Analytics segítségével, tekintse meg a csoportos adatelemzési [folyamat működés közben: az Azure szinapszis Analytics használata](sqldw-walkthrough.md)című témakört.


## <a name="azure-data-lake"></a>Azure Data Lake

A Azure Data Lake a formális követelmények vagy a szükséges sémák megkezdése előtt egyetlen helyen gyűjtött minden típusú adat vállalati szintű tárháza. Ez a rugalmasság lehetővé teszi, hogy a rendszer minden típusú adatot tároljon egy adattóban, függetlenül annak méretétől vagy struktúrájától, illetve hogy milyen gyorsan történjen a betöltés. A szervezetek ezután Hadoop vagy speciális elemzéseket használhatnak az adattavakban található mintázatok kereséséhez. Az adattavak az adattárházat is kihasználhatják az alacsonyabb költséghatékonyságú adatelőkészítéshez, mielőtt az adattárházba helyezné őket.

További információ a Azure Data Lakeről: [Azure Data Lake bemutatása](https://azure.microsoft.com/blog/introducing-azure-data-lake/). Ha szeretné megismerni, hogyan hozhat létre méretezhető, teljes körű adatelemzési megoldást Azure Data Lakekal, tekintse meg a [skálázható adatelemzést a Azure Data Lakeban: teljes körű útmutató](data-lake-walkthrough.md)


## <a name="azure-hdinsight-hive-hadoop-clusters"></a>Azure HDInsight kaptár (Hadoop) fürtök

A Apache Hive egy adattárházrendszer a Hadoop számára, amely lehetővé teszi az adatösszesítést, a lekérdezéseket és az adatelemzést a HiveQL használatával, az SQL-hez hasonló lekérdezési nyelven. A kaptár használatával interaktív módon vizsgálhatja meg az adatait, vagy létrehozhat újrafelhasználható kötegelt feldolgozási feladatokat.

A struktúra lehetővé teszi a nagy strukturálatlan adatmennyiségek struktúrájának kivetítését. A struktúra meghatározása után a kaptár használatával lekérdezheti a Hadoop-fürtben lévő összes adat használatát anélkül, hogy a Java-vagy a MapReduce-t kellene használnia. A HiveQL (a kaptár lekérdezési nyelve) lehetővé teszi, hogy lekérdezéseket írjon a T-SQL-hez hasonló utasításokkal.

Adatszakértők esetében a kaptár a rekordok feldolgozásához a kaptár-lekérdezésekben a Python User-Defined functions (UDF) futtatásával végezhető el. Ez a képesség jelentős mértékben kibővíti a kaptár-lekérdezések adatelemzési funkcióját. Pontosabban lehetővé teszi, hogy az adatszakértők méretezhető funkciók mérnöki használatát használják a leggyakrabban használt nyelveken: az SQL-Like HiveQL és a Python. 

Az Azure HDInsight kaptár-fürtökkel kapcsolatos további információkért lásd: a [kaptár és a HiveQL használata a Hadoop-ben a HDInsight-ben](../../hdinsight/hadoop/hdinsight-use-hive.md). A teljes körű adatelemzési megoldás Azure HDInsight kaptár-fürtökkel való kiépítéséről [a csoportos adatelemzési folyamat működés közben: HDInsight Hadoop-fürtök használata](hive-walkthrough.md)című témakörben olvashat.


## <a name="azure-file-storage"></a>Azure File Storage 

Az Azure File Storage egy olyan szolgáltatás, amely fájlmegosztást biztosít a felhőben a Standard Server Message Block (SMB) protokoll használatával. Az SMB 2.1 és az SMB 3.0 protokollt is támogatja. Az Azure File Storage szolgáltatással költséges újraírások nélkül, gyorsan megoldható a fájlmegosztásra támaszkodó, régi típusú alkalmazások áttelepítése az Azure-ra. Az Azure virtuális gépeken vagy felhőszolgáltatásában, esetleg helyszíni ügyfeleken üzemelő alkalmazások fájlmegosztást csatlakoztathatnak a felhőben. Ez a megosztás hasonló ahhoz a csatlakoztatott SMB-megosztáshoz, amelyet az asztali alkalmazások használnak. Ezután bármennyi alkalmazás-összetevő egyszerre csatlakoztathatja a File Storage-megosztást, és hozzá is férhet.

Különösen az adatelemzési projektek esetében hasznos, ha az Azure file Store-t olyan helyként szeretné létrehozni, amellyel a Project-csoport tagjai megoszthatják a projektadatokat. Mindegyiknek ekkor hozzá kell férnie az Azure file Storage-ban tárolt adatpéldányhoz. Emellett a file Storage használatával is megoszthatják a projekt végrehajtása során létrehozott szolgáltatáskészlet-készleteket. Ha a projekt ügyfél-összevonást használ, az ügyfelek saját Azure-előfizetésük keretében létrehozhatnak egy Azure file Storage-t a projektadatok és szolgáltatások megosztásához. Így az ügyfél teljes mértékben átirányítja a projekt adategységeit. További információ az Azure File Storage-ról: Ismerkedés [Az Azure file Storage](../../storage/files/storage-dotnet-how-to-use-files.md) szolgáltatással Windows rendszeren és az [Azure file Storage használata Linux](../../storage/files/storage-how-to-use-files-linux.md)rendszeren.


## <a name="sql-server-2019-r-and-python-services"></a>SQL Server 2019 R és Python szolgáltatások

Az R Services (in-Database) platformot biztosít olyan intelligens alkalmazások fejlesztéséhez és üzembe helyezéséhez, amelyek új ismereteket tudnak feltárni. Használhatja a gazdag és nagy teljesítményű R nyelvet, beleértve az R-Közösség által biztosított számos csomagot, hogy modelleket hozzon létre, és előrejelzéseket készítsen a SQL Server adataiból. Mivel az R Services (in-Database) az R nyelvet integrálja SQL Serverokkal, az elemzések az adathoz közelednek, ami kiküszöböli az adatáthelyezéshez kapcsolódó költségeket és biztonsági kockázatokat.

Az r Services (in-Database) támogatja a nyílt forráskódú R nyelvet SQL Server eszközök és technológiák átfogó készletével. Kiváló teljesítményt, biztonságot, megbízhatóságot és kezelhetőséget biztosítanak. Az R-megoldásokat kényelmesen és ismerős eszközökkel is üzembe helyezheti. Az éles alkalmazások hívhatják az R futtatókörnyezetet, és lekérhetik az előrejelzéseket és a vizualizációkat a Transact-SQL használatával. A skálázhatósági kódtárakat is használhatja az R-megoldások méretezésének és teljesítményének javítására. További információ: [SQL Server R Services](/sql/advanced-analytics/r/sql-server-r-services).

A Microsoft TDSP csapata két teljes körű bemutatót tett közzé, amelyek bemutatják, hogyan hozhat létre adatelemzési megoldásokat SQL Server 2016 R-szolgáltatásokban: egyet az R-programozók számára, egy pedig az SQL-fejlesztőknek. **R-programozók** esetében lásd: [adatelemzési végpontok közötti útmutató](/sql/advanced-analytics/tutorials/walkthrough-data-science-end-to-end-walkthrough). **SQL-fejlesztők** számára lásd: [az adatbázishoz készült Advanced Analytics for SQL Developers (oktatóanyag)](/sql/advanced-analytics/tutorials/sqldev-in-database-r-for-sql-developers).


## <a name="appendix-tools-to-set-up-data-science-projects"></a><a name="appendix"></a>Függelék: az adatelemzési projektek beállításához szükséges eszközök

### <a name="install-git-credential-manager-on-windows"></a>A git Hitelesítőadat-kezelő telepítése Windows rendszeren

Ha a **Windows** TDSP követi, telepítenie kell a **git hitelesítőadat-kezelőt (GCM)** a git-adattárakkal való kommunikációhoz. A GCM telepítéséhez először a **chocolatt** kell telepítenie. A Chocolat és a GCM telepítéséhez futtassa a következő parancsokat a Windows PowerShellben **rendszergazdaként**:  

```powershell
iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
choco install git-credential-manager-for-windows -y
```  

### <a name="install-git-on-linux-centos-machines"></a>A git Linux (CentOS) gépeken való telepítése

Futtassa a következő bash-parancsot a git Linux (CentOS) gépeken való telepítéséhez:

```powershell
sudo yum install git
```

### <a name="generate-public-ssh-key-on-linux-centos-machines"></a>Nyilvános SSH-kulcs előállítása Linux (CentOS) gépeken

Ha Linux (CentOS) gépeket használ a git-parancsok futtatásához, hozzá kell adnia a számítógép nyilvános SSH-kulcsát az Azure DevOps-szolgáltatásaihoz, hogy ezt a gépet az Azure DevOps-szolgáltatások ismerjék el. Először létre kell hoznia egy nyilvános SSH-kulcsot, és hozzá kell adnia a kulcsot az SSH nyilvános kulcsokhoz az Azure DevOps Services biztonsági beállítás lapján. 

1. Az SSH-kulcs létrehozásához futtassa a következő két parancsot: 

   ```
   ssh-keygen
   cat .ssh/id_rsa.pub
   ```
   
   ![Az SSH-kulcs létrehozásához szükséges parancsok](./media/platforms-and-tools/resources-1-generate_ssh.png)

1. Másolja a teljes SSH-kulcsot, beleértve az *SSH-RSA-* t. 
1. Jelentkezzen be az Azure DevOps-szolgáltatásaiba. 
1. Kattintson a lap jobb felső sarkában található **<nevére \>** , majd a **Biztonság** elemre. 
    
   ![Kattintson a nevére, majd a Biztonság elemre.](./media/platforms-and-tools/resources-2-user-setting.png)

1. Kattintson a **nyilvános ssh-kulcsok** elemre, majd kattintson a **+ Hozzáadás** gombra. 

   ![Kattintson a nyilvános SSH-kulcsok elemre, majd a + Hozzáadás gombra.](./media/platforms-and-tools/resources-3-add-ssh.png)

1. Illessze be a szövegmezőbe másolt SSH-kulcsot, és mentse a fájlt.


## <a name="next-steps"></a>Következő lépések

Teljes körű útmutatók, amelyek bemutatják a folyamat összes lépését az **adott forgatókönyvek** esetében is. Ezek a példákban láthatók és a miniatűr leírásokkal vannak összekapcsolva a [példa útmutatói](walkthroughs.md) című témakörben. Bemutatják, hogyan kombinálhatók a felhő, a helyszíni eszközök és a szolgáltatások egy munkafolyamatban vagy folyamatban egy intelligens alkalmazás létrehozásához. 

A következő példákban megtudhatja, hogyan hajthat végre lépéseket a csoportos adatelemzési folyamat lépéseiben Azure Machine Learning Studio (klasszikus) használatával, lásd: az [Azure ml](./index.yml) képzési útvonala.