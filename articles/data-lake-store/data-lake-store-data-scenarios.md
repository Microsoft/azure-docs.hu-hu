---
title: Az Data Lake Storage Gen1t érintő adatforgatókönyvek | Microsoft Docs
description: Megismerheti azokat a különböző forgatókönyveket és eszközöket, amelyek segítségével az adatok betölthetők, feldolgozhatók, letölthetők és megjeleníthetők Data Lake Storage Gen1ban (korábbi nevén Azure Data Lake Store)
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: twooley
ms.openlocfilehash: 0bd895b0c19293a199b2a9b135915b7dc45b9e5e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98702497"
---
# <a name="using-azure-data-lake-storage-gen1-for-big-data-requirements"></a>A Azure Data Lake Storage Gen1 használata big data követelményekhez

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

A big data feldolgozásának négy fő szakasza van:

* Nagy mennyiségű adatot tölt be egy adattárba valós időben vagy kötegekben
* Az adatfeldolgozás
* Az adatletöltés
* Az adatmegjelenítés

Ebben a cikkben ezeket a szakaszokat tekintjük át, hogy Azure Data Lake Storage Gen1 a big data igényeinek megfelelő lehetőségek és eszközök megismerése érdekében.

## <a name="ingest-data-into-data-lake-storage-gen1"></a>Adatbevitel a Data Lake Storage Gen1ba
Ez a szakasz kiemeli a különböző adatforrásokat, valamint azokat a különböző módokat, amelyekben az adott adatot betöltheti egy Data Lake Storage Gen1-fiókba.

![Adatbevitel a Data Lake Storage Gen1ba](./media/data-lake-store-data-scenarios/ingest-data.png "Adatbevitel a Data Lake Storage Gen1ba")

### <a name="ad-hoc-data"></a>Ad hoc adatszolgáltatások
Ez kisebb adathalmazokat jelent, amelyeket egy big data alkalmazás prototípusához használ. Az ad hoc adatmennyiség különböző módokon végezhető el az adatforrástól függően.

| Adatforrás | A |
| --- | --- |
| Helyi számítógép |<ul> <li>[Azure Portalra](data-lake-store-get-started-portal.md)</li> <li>[Azure PowerShell](data-lake-store-get-started-powershell.md)</li> <li>[Azure CLI](data-lake-store-get-started-cli-2.0.md)</li> <li>[A Visual studióhoz készült Data Lake Tools használata](../data-lake-analytics/data-lake-analytics-data-lake-tools-get-started.md) </li></ul> |
| Azure Storage Blob |<ul> <li>[Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)</li> <li>[AdlCopy eszköz](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[A HDInsight-fürtön futó DistCp](data-lake-store-copy-data-wasb-distcp.md)</li> </ul> |

### <a name="streamed-data"></a>Továbbított adattartalom
Ez olyan adatforrásokat jelent, amelyeket különböző források, például alkalmazások, eszközök, érzékelők stb. hozhatnak létre. Ezeket az adatmennyiségeket számos eszköz Data Lake Storage Gen1 is betöltheti. Ezek az eszközök általában valós időben rögzítik és feldolgozzák az adott eseményt, majd a kötegekben lévő eseményeket a Data Lake Storage Gen1ba írják, hogy tovább lehessen feldolgozni őket.

A következő eszközök használhatók:

* [Azure stream Analytics](../stream-analytics/stream-analytics-define-outputs.md) – a Event Hubsba betöltött események Azure Data Lake Storage Gen1 Azure Data Lake Storage Gen1 kimenet használatával is megírhatók.
* [Azure HDInsight Storm](../hdinsight/storm/apache-storm-write-data-lake-store.md) – az adatok közvetlenül a Storm-fürtről Data Lake Storage Gen1 is írhatók.
* [EventProcessorHost](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md) – események fogadása Event Hubsről, majd a [Data Lake Storage Gen1 .net SDK](data-lake-store-get-started-net-sdk.md)-val való Data Lake Storage Gen1ának megírása.

### <a name="relational-data"></a>Relációs adatok
A kapcsolódó adatbázisok adatait is megtekintheti. Egy adott időszakban a kapcsolati adatbázisok nagy mennyiségű adatot gyűjtenek, amelyek kulcsfontosságú elemzéseket biztosítanak, ha big data folyamaton keresztül dolgozzák fel őket. Az alábbi eszközök használatával helyezheti át ezeket az eszközöket Data Lake Storage Gen1ba.

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

### <a name="web-server-log-data-upload-using-custom-applications"></a>Webkiszolgáló naplójának adatai (egyéni alkalmazásokkal történő feltöltés)
Az ilyen típusú adathalmazokat a rendszer kifejezetten felhívta, mert a webkiszolgáló naplófájljainak elemzése gyakori használati eset big data alkalmazásokhoz, és nagy mennyiségű naplófájlt igényel a Data Lake Storage Gen1ba való feltöltéshez. Az alábbi eszközök bármelyikével írhat saját parancsfájlokat vagy alkalmazásokat az ilyen adatok feltöltéséhez.

* [Azure CLI](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Azure Data Lake Storage Gen1 .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)

A webkiszolgáló-naplózási adatainak feltöltéséhez, valamint más típusú adatmennyiségek (például a közösségi érzelmek adatainak) feltöltéséhez jó módszer a saját egyéni parancsfájljainak/alkalmazásainak megírása, mivel lehetővé teszi a rugalmasságot, hogy a nagyobb big data alkalmazás részeként feltöltse az adatfeltöltési összetevőt is. Bizonyos esetekben ez a kód parancsfájl vagy egyszerű parancssori segédprogram formájában is elvégezhető. Más esetekben a kód a big data feldolgozás üzleti alkalmazásba vagy megoldásba való integrálására használható.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Az Azure HDInsight-fürtökhöz kapcsolódó adatmennyiség
A legtöbb HDInsight-fürt (Hadoop, HBase, Storm) támogatja az adattároló adattárként való Data Lake Storage Gen1. A HDInsight-fürtök az Azure Storage-Blobokból (WASB) származó adatokhoz férnek hozzá. A jobb teljesítmény érdekében a WASB származó adatok másolása a fürthöz társított Data Lake Storage Gen1-fiókba. Az adatmásoláshoz a következő eszközök használhatók.

* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)
* [AdlCopy szolgáltatás](data-lake-store-copy-data-azure-storage-blob.md)
* [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md)

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>A helyszíni vagy IaaS Hadoop-fürtökben tárolt adatkészletek
Nagy mennyiségű adattal rendelkezhetnek a meglévő Hadoop-fürtökben, helyileg a HDFS-t használó gépeken. Előfordulhat, hogy a Hadoop-fürtök helyszíni környezetben találhatók, vagy egy Azure-beli IaaS-fürtön belül vannak. Előfordulhat, hogy az ilyen jellegű adatmásolásra vonatkozó követelmények egy egyszeri vagy ismétlődő módon Azure Data Lake Storage Gen1. Ennek eléréséhez számos lehetőség közül választhat. Az alábbiakban felsoroljuk az alternatívák listáját, valamint a hozzájuk kapcsolódó kompromisszumokat.

| Módszer | Részletek | Előnyök | Megfontolandó szempontok |
| --- | --- | --- | --- |
| Azure Data Factory (ADF) használatával közvetlenül a Hadoop-fürtökről másolhatja az adatok Azure Data Lake Storage Gen1 |[Az ADF támogatja a HDFS adatforrásként](../data-factory/connector-hdfs.md) |Az ADF beépített támogatást nyújt a HDFS és az első osztályú végpontok közötti felügyelethez és figyeléshez |A adatkezelés átjárót a helyszíni vagy a IaaS-fürtön kell telepíteni |
| Adatok exportálása Hadoop fájlként. Ezután másolja a fájlokat Azure Data Lake Storage Gen1 a megfelelő mechanizmus használatával. |A következő paranccsal másolhat fájlokat Azure Data Lake Storage Gen1ba: <ul><li>[Azure PowerShell a Windows operációs rendszerhez](data-lake-store-get-started-powershell.md)</li><li>[Azure CLI](data-lake-store-get-started-cli-2.0.md)</li><li>Bármely Data Lake Storage Gen1 SDK-t használó egyéni alkalmazás</li></ul> |Gyors kezdés. Testre szabott feltöltések is megadhatók |Többlépéses folyamat, amely több technológiát is magában foglal. A felügyelet és a monitorozás az eszközök testreszabott jellegéből adódóan az idő múlásával növekedni fog. |
| Adatok másolása a Hadoop-ből az Azure Storage-ba a Distcp használatával. Ezután másolja az Azure Storage-ból származó adatok Data Lake Storage Gen1 a megfelelő mechanizmus használatával. |Az adatok az Azure Storage-ból Data Lake Storage Gen1 a következő használatával másolhatók: <ul><li>[Azure Data Factory](../data-factory/copy-activity-overview.md)</li><li>[AdlCopy eszköz](data-lake-store-copy-data-azure-storage-blob.md)</li><li>[HDInsight-fürtökön futó Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)</li></ul> |Használhatja a nyílt forráskódú eszközöket. |Többlépéses folyamat, amely több technológiát is magában foglal |

### <a name="really-large-datasets"></a>Nagyon nagy adatkészletek
A több terabájtos tartományba tartozó adatkészletek feltöltéséhez a fent ismertetett módszerek használatával időnként lassú és költséges lehet. Ilyen esetekben az alábbi beállításokat használhatja.

* **Az Azure ExpressRoute használata**. Az Azure ExpressRoute lehetővé teszi privát kapcsolatok létrehozását az Azure-adatközpontok és a helyszíni infrastruktúra között. Ez megbízható lehetőséget biztosít a nagy mennyiségű adatátvitelhez. További információ: az [Azure ExpressRoute dokumentációja](../expressroute/expressroute-introduction.md).
* **"Kapcsolat nélküli" adatok feltöltése**. Ha az Azure ExpressRoute használata bármilyen okból nem valósítható meg, az [Azure import/export szolgáltatás](../import-export/storage-import-export-service.md) használatával a merevlemezeket az Azure-adatközpontba helyezheti el az adataival. Az adatait először feltölti az Azure Storage-Blobokra. Ezután az [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md) vagy a [AdlCopy eszközzel](data-lake-store-copy-data-azure-storage-blob.md) másolhat adatokból az Azure Storage-blobokból a Data Lake Storage Gen1ba.

  > [!NOTE]
  > Az import/export szolgáltatás használatakor az Azure-adatközpontba szállított lemezek fájlmérete nem haladhatja meg a 195 GB-ot.
  >
  >

## <a name="process-data-stored-in-data-lake-storage-gen1"></a>Data Lake Storage Gen1 tárolt adatfeldolgozás
Ha az adatai elérhetők a Data Lake Storage Gen1ban, a támogatott big data alkalmazásokkal is futtathatja az elemzést. Jelenleg az Azure HDInsight és a Azure Data Lake Analytics használatával futtathatja az adatelemzési feladatokat a Data Lake Storage Gen1 tárolt adatokon.

![Data Lake Storage Gen1ban lévő adatelemzés](./media/data-lake-store-data-scenarios/analyze-data.png "Data Lake Storage Gen1ban lévő adatelemzés")

Az alábbi példákat is megtekintheti.

* [Data Lake Storage Gen1 tárolóval rendelkező HDInsight-fürt létrehozása](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Azure Data Lake Analytics használata a Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

## <a name="download-data-from-data-lake-storage-gen1"></a>Adatok letöltése Data Lake Storage Gen1ról
Előfordulhat, hogy a következő forgatókönyvek esetében is szeretné letölteni vagy áthelyezni az adatok Azure Data Lake Storage Gen1:

* Más adattárakba helyezheti át az adatait a meglévő adatfeldolgozási folyamatokkal. Előfordulhat például, hogy át szeretné helyezni az adatok Data Lake Storage Gen1ról Azure SQL Database vagy SQL Serverre.
* Az alkalmazások prototípusának kiépítése során az IDE-környezetekben tárolt adatfeldolgozási folyamatokat a helyi számítógépre töltheti le.

![Kimenő adatok Data Lake Storage Gen1](./media/data-lake-store-data-scenarios/egress-data.png "Kimenő adatok Data Lake Storage Gen1")

Ilyen esetekben az alábbi lehetőségek közül választhat:

* [Apache Sqoop](data-lake-store-data-transfer-sql-sqoop.md)
* [Azure Data Factory](../data-factory/copy-activity-overview.md)
* [Apache DistCp](data-lake-store-copy-data-wasb-distcp.md)

A következő módszerekkel írhat saját parancsfájlt/alkalmazást az adatok Data Lake Storage Gen1 való letöltéséhez.

* [Azure CLI](data-lake-store-get-started-cli-2.0.md)
* [Azure PowerShell](data-lake-store-get-started-powershell.md)
* [Azure Data Lake Storage Gen1 .NET SDK](data-lake-store-get-started-net-sdk.md)

## <a name="visualize-data-in-data-lake-storage-gen1"></a>Az Data Lake Storage Gen1ban lévő adatmegjelenítés
A szolgáltatások együttes használatával a Data Lake Storage Gen1ban tárolt adatvizualizációk is létrehozhatók.

![Az Data Lake Storage Gen1ban lévő adatmegjelenítés](./media/data-lake-store-data-scenarios/visualize-data.png "Az Data Lake Storage Gen1ban lévő adatmegjelenítés")

* Az [adatok Data Lake Storage Gen1ból az Azure szinapszis analyticsbe való áthelyezéséhez Azure Data Factory](../data-factory/copy-activity-overview.md) használatával kezdheti
* Ezután [integrálhatja Power bi az Azure szinapszis Analytics szolgáltatással](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect) , hogy vizuálisan jelenítse meg az adatmegjelenítést.