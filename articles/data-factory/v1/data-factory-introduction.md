---
title: Az adatintegrációs szolgáltatás Data Factory bemutatása
description: 'A témakör ismerteti, hogy mi is az Azure Data Factory: egy felhőalapú adatintegrációs szolgáltatás, amellyel előkészíthető és automatizálható az adatok továbbítása és átalakítása.'
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: overview
ms.date: 01/22/2018
ms.openlocfilehash: 220c1969055ffaa5d1bea41ab547efd4596f8a20
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786464"
---
# <a name="introduction-to-azure-data-factory"></a>Az Azure Data Factory bemutatása 
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-introduction.md)
> * [2-es verzió (aktuális verzió)](../introduction.md)

> [!NOTE]
> Ez a cikk az Azure Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg a [Data Factory 2-es verziójának ismertetését](../introduction.md).


## <a name="what-is-azure-data-factory"></a>Mi az az Azure Data Factory?
A big data világában hogyan aknázhatja ki a vállalkozás a meglévő adatait? Lehetséges a felhőben létrehozott adatokat feldúsítani a helyi vagy más különálló adatforrásokból származó referenciaadatokkal? 

Például egy játékokkal foglalkozó vállalat a játékok által készített naplókat gyűjti össze a felhőben. Ezeket a naplókat szeretné elemezni, hogy betekintést nyerhessen az ügyfelek preferenciáiba, demográfiai adataiba, felhasználói viselkedésébe és egyéb jellemzőibe. A vállalat ezenkívül azonosítani szeretné az értékesítési és keresztértékesítési lehetőségeket, új funkciókat szeretne fejleszteni az üzleti növekedés elősegítése érdekében, és jobb felhasználói élményt szeretne nyújtani az ügyfeleinek. 

A naplók elemzéséhez a vállalatnak a helyszíni adattárban tárolt referenciaadatokat kell felhasználnia, például az ügyféladatokat, a játékadatokat és a reklámkampány-adatokat. Ennélfogva a vállalat szeretne hozzáférni a felhőbeli adattárban található naplóadatokhoz és a helyszíni adattárban található referenciaadatokhoz. 

Ezután fel szeretné dolgozni az adatokat a Hadoop használatával a felhőben (Azure HDInsight). Az eredményeket egy Felhőbeli adattárházba kívánják közzétenni, például az Azure szinapszis Analytics szolgáltatásba vagy egy helyszíni adattárba, például SQL Serverba. A vállalat ezt a munkafolyamatot hetente egyszer szeretné futtatni. 

A vállalatnak egy olyan platformra van szüksége, amellyel létrehozhat egy munkafolyamatot, amely képes kiolvasni az adatokat a helyszíni és a felhőbeli adattárolókból. A vállalatnak ezenkívül képesnek kell lennie arra, hogy átalakítsa és feldolgozza az adatokat létező számítási szolgáltatások, például a Hadoop használatával, és közzétegye az eredményeket helyszíni vagy felhőbeli adattárolókon a BI-alkalmazások általi felhasználáshoz. 

![Data Factory – áttekintés](media/data-factory-introduction/what-is-azure-data-factory.png) 

Az Azure Data Factory az ilyen helyzetekben használható platform. Ez egy *felhőalapú adatintegrációs szolgáltatás, amely lehetővé teszi olyan, a felhőben futó, adatvezérelt munkafolyamatok létrehozását, amelyek irányítják és automatizálják az adatok átvitelét és átalakítását*. Az Azure Data Factoryval a következő feladatokat végezheti el: 

- Különböző adattárolókból adatokat beolvasó adatvezérelt munkafolyamatokat hozhat létre és ütemezhet.

- Adatokat dolgozhat fel és alakíthat át különböző számítási szolgáltatások (például Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics vagy Azure Machine Learning) használatával.

-  A kimeneti adatokat olyan adattárakba teheti közzé, mint például az Azure szinapszis Analytics for Business Intelligence-(BI-) alkalmazások.  

Ez inkább egy kinyerési és betöltési (EL) és egy átalakítási és betöltési (TL) platform, mintsem egy hagyományos kinyerési, átalakítási és betöltési (ETL) platform. Az átalakítások számítási szolgáltatásokkal dolgoznak fel adatokat származtatott oszlopok hozzáadása, sorok megszámlálása vagy adatok rendezése és egyéb műveletek helyett. 

Jelenleg az Azure Data Factoryben a munkafolyamatok által felhasznált és előállított adatok *időszeletekre osztott adatok* (óránként, naponta, hetente stb.). Például beállítható, hogy egy folyamat naponta egyszer bemeneti adatokat olvasson, feldolgozza őket, és kimeneti adatokat hozzon létre. A munkafolyamatok egyetlen alkalommal is futtathatók.  
  

## <a name="how-does-it-work"></a>Hogyan működik? 
Az adatvezérelt munkafolyamatok az Azure Data Factoryben általában a következő három lépést hajtják végre:

![Az Azure Data Factory három szakasza](media/data-factory-introduction/three-information-production-stages.png)

### <a name="connect-and-collect"></a>Csatlakozás és összegyűjtés
A vállalatok a legkülönfélébb adatokkal rendelkeznek a legkülönfélébb forrásokból. Az információelőállítási rendszerek kiépítésének első lépése az összes szükséges adatforrás és feldolgozó összekapcsolása. Ezekbe az adatforrásokba tartoznak az SaaS-szolgáltatások, a fájlmegosztások, az FTP és a webszolgáltatások. Ezután az adatok igény szerint átkerülnek egy központi helyre további feldolgozás céljából.

A Data Factory nélkül a vállalatoknak egyéni adattovábbítási összetevőket kell készíteniük vagy egyéni szolgáltatásokat kell írniuk az adatforrások és feldolgozók integrálására. Az ilyen rendszerek költségesek, nehezen integrálhatóak és tarthatók karban. Ezekben a rendszerekben továbbá gyakran nem áll rendelkezésre az a vállalati szintű monitorozási, riasztási és vezérlési funkcionalitás, amelyet egy teljes mértékben felügyelt szolgáltatás biztosítani képes.

A Data Factory segítségével a Másolási tevékenység keretében az adatok egyazon adatfolyamatban helyszíni és felhőalapú forrásadattárakból egyaránt továbbíthatóak egy, a felhőben lévő adattárba további elemzésre. 

Begyűjtheti például az adatokat egy Azure Data Lake Store-tárolóból, és később átalakíthatja őket egy Azure Data Lake Analytics számítási szolgáltatás használatával. Vagy begyűjtheti az adatokat egy Azure Blob Storage-tárolóból, és később átalakíthatja őket egy Azure HDInsight Hadoop-fürt használatával.

### <a name="transform-and-enrich"></a>Átalakítás és bővítés
Ha az adatok már jelen vannak egy központi adattárban a felhőben, akkor olyan számítási szolgáltatásokkal dolgozhatók fel és vihetők át, mint a HDInsight Hadoop, a Spark, a Data Lake Analytics vagy a Machine Learning. Az átalakított adatok megbízhatóan állíthatók elő egy fenntartható és szabályozható séma szerint, az éles környezetek megbízható adatokkal való kiszolgálása érdekében. 

### <a name="publish"></a>Közzététel 
Az átalakított adatok a felhőből áthelyezhetők egy helyszíni forrásra, például egy SQL-kiszolgálóra. Azt is megteheti, hogy a felhőbeli tárolóforrásokban tartja őket a BI- és elemzőeszközök, illetve egyéb alkalmazások általi felhasználásra.

## <a name="key-components"></a>A legfontosabb összetevők
Az Azure-előfizetések több Azure Data Factory-példányt (más néven adat-előállítókat) is tartalmazhatnak. Az Azure Data Factory négy fő összetevőből áll. Ezek együtt alkotják azt a platformot, amelyen létrehozhatók olyan adatvezérelt munkafolyamatok, amelyeknek a lépései áthelyezik és átalakítják az adatokat. 

### <a name="pipeline"></a>Folyamat
A data factory egy vagy több folyamattal rendelkezhet. A folyamatok tevékenységek csoportjai. A folyamatban lévő tevékenységek együtt egy feladatot hajtanak végre. 

Például a folyamat tartalmazhat egy csoportnyi műveletet, amelyek adatokat fogadnak egy Azure-blobból, majd egy Hive-lekérdezést futtatnak egy HDInsight-fürtön az adatok particionálásához. A folyamatok használatának az az előnye, hogy így a tevékenységek egy készletben kezelhetők, nem pedig külön-külön. Például, különböző tevékenységek ütemezése helyett, maga a folyamat helyezhető üzembe és ütemezhető. 

### <a name="activity"></a>Tevékenység
A folyamatok egy vagy több tevékenységből állhatnak. A tevékenységek meghatározzák az adatokon végrehajtandó műveleteket. A másolási tevékenység használatával például az egyik adattárból a másikba másolhatja az adatokat. Ha szeretne, Hive-tevékenységet is használhat. A Hive-tevékenység Hive-lekérdezést futtat egy Azure HDInsight-fürtön az adatok átalakításához és elemzéséhez. A Data Factory két típusú tevékenységet támogat: az adattovábbítási tevékenységeket és az adatátalakítási tevékenységeket.

### <a name="data-movement-activities"></a>Adattovábbítási tevékenységek
A Data Factory másolási tevékenysége adatokat másol egy forrásadattárból egy fogadó adattárba. Az adatok bármilyen forrásból bármilyen fogadóba másolhatók. Egy adattár kiválasztásával megtudhatja, hogy az adott tárolóba, illetve tárolóból hogyan másolhat adatokat. A Data Factory a következő adattárakat támogatja:

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

További információ: [Adatok áthelyezése másolási tevékenységgel](data-factory-data-movement-activities.md).

### <a name="data-transformation-activities"></a>Adatátalakítási tevékenységek
[!INCLUDE [data-factory-transformation-activities](../../../includes/data-factory-transformation-activities.md)]

További információ: [Adatok áthelyezése másolási tevékenységgel](data-factory-data-transformation-activities.md).

### <a name="custom-net-activities"></a>Egyéni .NET-tevékenységek
Akkor hozzon létre egy egyéni .NET-tevékenységet, ha olyan adattárból/adattárba szeretne adatokat továbbítani, amely nem támogatja a másolási tevékenységet, vagy saját logika szerint szeretne adatátalakítást végezni. További információ az egyéni tevékenységek létrehozásával és használatával kapcsolatban: [Egyéni tevékenységek használata Azure Data Factory-folyamatban](data-factory-use-custom-activities.md).

### <a name="datasets"></a>Adathalmazok
Minden tevékenység nulla vagy több adatkészletet fogad bemenetként, és egy vagy több adatkészletet állít elő kimenetként. Az adatkészletek az adattárakon belüli adatstruktúrákat jelölik. Ezek a struktúrák egyszerűen rámutatnak azokra az adatokra vagy hivatkoznak rájuk, amelyeket a tevékenységekben használni szeretne (például be- vagy kimenetként). 

Az Azure Blob-adatkészlet például meghatározza a blobtárolót és azt az Azure Blob Storage-mappát, amelyből a folyamat beolvassa az adatokat. Az Azure SQL Table adatkészlet megadhatja, hogy a tevékenység melyik táblára írja a kimeneti adatokat. 

### <a name="linked-services"></a>Társított szolgáltatások
A társított szolgáltatások nagyon hasonlóak a kapcsolati sztringekhoz, amelyek meghatározzák azokat a kapcsolati információkat, amelyeket a Data Factory a külső erőforrásokhoz történő csatlakozáshoz igényel. Tulajdonképpen a társított szolgáltatás határozza meg az adatforrással való kapcsolatot, míg az adatkészlet jelöli az adatok struktúráját. 

Az Azure Storage társított szolgáltatása például kapcsolati sztringet szolgáltat az Azure Storage-fiókhoz való csatlakozáshoz. Az Azure Blob-adatkészlet meghatározza a blobtárolót és az adatokat tartalmazó mappát.   

A társított szolgáltatásokat két okból használjuk a Data Factoryban:

* Olyan *adattár* jelölésére, amely tartalmaz, de nem korlátozódik a SQL Server adatbázisra, az Oracle-adatbázisra, a fájlmegosztás vagy az Azure Blob Storage-fiókra. A támogatott adattárak listája az [Adattovábbítási tevékenységek](#data-movement-activities) című részben található.

* Olyan *számítási erőforrás* jelölésére, amely egy tevékenység végrehajtását képes tárolni. A HDInsightHive-tevékenység végrehajtása például egy HDInsight Hadoop-fürtön történik. A támogatott számítási környezetek listája az [Adatátalakítási tevékenységek](#data-transformation-activities) szakaszban található.

### <a name="relationship-between-data-factory-entities"></a>Data Factory-entitások közötti kapcsolatok

![Ábra: Data Factory áttekintése, felhőalapú adatintegrációs szolgáltatás – fő fogalmak](./media/data-factory-introduction/data-integration-service-key-concepts.png)

## <a name="supported-regions"></a>Támogatott régiók
Jelenleg az USA nyugati régiójában, az USA keleti régiójában és az észak-európai régióban hozhat létre data factoryt. Egy adat-előállító azonban más Azure-régiókban lévő adattárakhoz és számítási szolgáltatásokhoz is hozzáférhet az adatok adattárak közötti mozgatása vagy az adatok számítási szolgáltatásokkal történő feldolgozása érdekében.

Maga az Azure Data Factory nem tárol adatokat. Lehetővé teszi viszont olyan adatvezérelt munkafolyamatok létrehozását, amelyekkel előkészíthető a [támogatott adattárak](#data-movement-activities) közötti adatmozgás. Lehetővé teszi az adatok [számítási szolgáltatásokkal](#data-transformation-activities) történő feldolgozását is más régiókban, illetve helyszíni környezetben. Lehetővé teszi továbbá a [munkafolyamatok monitorozását és kezelését](data-factory-monitor-manage-pipelines.md) a programozott és a felhasználói felületi mechanizmusok használatával.

A Data Factory csak az USA nyugati régiójában, az USA keleti régiójában és az észak-európai régióban érhető el. Az adatok Data Factoryval történő áthelyezését biztosító szolgáltatás azonban [globálisan](data-factory-data-movement-activities.md#global) számos régióban elérhető. Ha egy adattár egy tűzfal mögött található, akkor a helyszíni környezetben telepített [adatkezelés átjáró](data-factory-move-data-between-onprem-and-cloud.md) áthelyezi az adatátvitelt.

Tegyük fel például, hogy számítási környezetei, például az Azure HDInsight-fürt és az Azure Machine Learning, a nyugat-európai régión kívül találhatóak. Létrehozhat egy Azure Data Factory-példányt Észak-Európában, amelyet aztán felhasználhat a Nyugat-Európában található számítási környezetein futtatott feladatok ütemezéséhez. A Data Factory néhány ezredmásodperc alatt aktiválja a feladatot a számítási környezetben, a feladatnak a számítási környezetben való futtatásához szükséges idő viszont nem változik.

## <a name="get-started-with-creating-a-pipeline"></a>Bevezetés a folyamatok létrehozásába
Az Azure Data Factoryben a következő eszközök és API-k használhatók adatfolyamatok létrehozására: 

- Visual Studio
- PowerShell
- .NET API
- REST API
- Azure Resource Manager-sablon

A következő oktatóanyagok részletes utasításait követve megtudhatja, hogyan építhet ki adatfolyamatokkal rendelkező adat-előállítókat:

| Oktatóanyag | Leírás |
| --- | --- |
| [Két felhőalapú adattár közötti adatáthelyezés](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) |Hozzon létre egy adatfeldolgozót egy olyan folyamattal, amely a blob Storage-ból SQL Databaseba helyezi át az adatait. |
| [Adatok átalakítása Hadoop-fürttel](data-factory-build-your-first-pipeline.md) |Kiépítheti az első Azure adat-előállítóját egy olyan adatfolyamattal, amely egy Azure HDInsight- (Hadoop-) fürtön futtatott Hive-szkripttel dolgozza fel az adatokat. |
| [Egy helyszíni és egy felhőalapú adattár közötti adatáthelyezés adatkezelési átjáró segítségével](data-factory-move-data-between-onprem-and-cloud.md) |Hozzon létre egy adatfeldolgozót egy olyan folyamattal, amely egy SQL Server-adatbázisból egy Azure-blobba helyez át egy adatátvitelt. A bemutató részeként telepíti és konfigurálja az adatkezelési átjárót a gépen. |
