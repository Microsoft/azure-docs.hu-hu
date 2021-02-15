---
title: Az Azure Data Lake Storage Gen2t érintő adatforgatókönyvek | Microsoft Docs
description: Megismerheti azokat a különböző forgatókönyveket és eszközöket, amelyek segítségével az adatok betölthetők, feldolgozhatók, letölthetők és megjeleníthetők Data Lake Storage Gen2ban (korábbi nevén Azure Data Lake Store)
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 02/14/2020
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 1d22a992ac4ae69c8541b6efbaee58340f48caa4
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100517875"
---
# <a name="using-azure-data-lake-storage-gen2-for-big-data-requirements"></a>A Azure Data Lake Storage Gen2 használata big data követelményekhez

A big data feldolgozásának négy fő szakasza van:

> [!div class="checklist"]
> * Nagy mennyiségű adatot tölt be egy adattárba valós időben vagy kötegekben
> * Az adatfeldolgozás
> * Az adatletöltés
> * Az adatmegjelenítés

Ez a cikk az egyes feldolgozási fázisok lehetőségeit és eszközeit emeli ki.

Az Azure Data Lake Storage Gen2rel használható Azure-szolgáltatások teljes listájáért lásd: a [Azure Data Lake Storage integrálása az Azure-szolgáltatásokkal](./data-lake-storage-supported-azure-services.md)

## <a name="ingest-the-data-into-data-lake-storage-gen2"></a>Az adatgyűjtés Data Lake Storage Gen2

Ez a szakasz kiemeli a különböző adatforrásokat, valamint azokat a különböző módokat, amelyekben az adott adatot betöltheti egy Data Lake Storage Gen2-fiókba.

![Adatbevitel a Data Lake Storage Gen2ba](./media/data-lake-storage-data-scenarios/ingest-data.png "Adatbevitel a Data Lake Storage Gen2ba")

### <a name="ad-hoc-data"></a>Ad hoc adatszolgáltatások

Ez kisebb adathalmazokat jelent, amelyeket egy big data alkalmazás prototípusához használ. Az ad hoc adatmennyiség különböző módokon végezhető el az adatforrástól függően. 

Az alábbi lista azokat az eszközöket tartalmazza, amelyeket az ad hoc adatbevitelhez használhat.

| Adatforrás | A |
| --- | --- |
| Helyi számítógép |[Azure PowerShell](data-lake-storage-directory-file-acl-powershell.md)<br><br>[Azure CLI](data-lake-storage-directory-file-acl-cli.md)<br><br>[Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)<br><br>[AzCopy eszköz](../common/storage-use-azcopy-v10.md)|
| Azure Storage Blob |[Azure Data Factory](../../data-factory/connector-azure-data-lake-store.md)<br><br>[AzCopy eszköz](../common/storage-use-azcopy-v10.md)<br><br>[A HDInsight-fürtön futó DistCp](data-lake-storage-use-distcp.md)|

### <a name="streamed-data"></a>Továbbított adattartalom

Ez olyan adatforrásokat jelent, amelyeket különböző források, például alkalmazások, eszközök, érzékelők stb. hozhatnak létre. Ezeket az adatmennyiségeket számos eszköz Data Lake Storage Gen2 is betöltheti. Ezek az eszközök általában valós időben rögzítik és feldolgozzák az adott eseményt, majd a kötegekben lévő eseményeket a Data Lake Storage Gen2ba írják, hogy tovább lehessen feldolgozni őket.

Az alábbi lista azokat az eszközöket tartalmazza, amelyek segítségével betöltheti a továbbított adatmennyiséget.

|Eszköz | Útmutató |
|---|--|
|Azure Stream Analytics|[Útmutató: Stream Analytics-feladat létrehozása az Azure Portal használatával](../../stream-analytics/stream-analytics-quick-create-portal.md) <br> [Kimenő Azure Data Lake Gen2](../../stream-analytics/stream-analytics-define-outputs.md)|
|Azure HDInsight Storm | [Írás a HDInsight Apache Storm HDFS Apache Hadoop](../../hdinsight/storm/apache-storm-write-data-lake-store.md) |

### <a name="relational-data"></a>Relációs adatok

A kapcsolódó adatbázisok adatait is megtekintheti. Egy adott időszakban a kapcsolati adatbázisok nagy mennyiségű adatot gyűjtenek, amelyek kulcsfontosságú elemzéseket biztosítanak, ha big data folyamaton keresztül dolgozzák fel őket. Az alábbi eszközök használatával helyezheti át ezeket az eszközöket Data Lake Storage Gen2ba.

Az alábbi lista azokat az eszközöket tartalmazza, amelyekkel a rendszer a kapcsolatok adatfeldolgozását is felhasználhatja.

|Eszköz | Útmutató |
|---|--|
|Azure Data Factory | [Másolási tevékenység az Azure Data Factoryben](../../data-factory/copy-activity-overview.md) |

### <a name="web-server-log-data-upload-using-custom-applications"></a>Webkiszolgáló naplójának adatai (egyéni alkalmazásokkal történő feltöltés)

Az ilyen típusú adathalmazokat a rendszer kifejezetten felhívta, mert a webkiszolgáló naplófájljainak elemzése gyakori használati eset big data alkalmazásokhoz, és nagy mennyiségű naplófájlt igényel a Data Lake Storage Gen2ba való feltöltéshez. Az alábbi eszközök bármelyikével írhat saját parancsfájlokat vagy alkalmazásokat az ilyen adatok feltöltéséhez.

Az alábbi lista azokat az eszközöket tartalmazza, amelyek segítségével betöltheti a webkiszolgáló naplófájljait.

|Eszköz | Útmutató |
|---|--|
|Azure Data Factory | [Másolási tevékenység az Azure Data Factoryben](../../data-factory/copy-activity-overview.md)  |
|Azure CLI|[Azure CLI](data-lake-storage-directory-file-acl-cli.md)|
|Azure PowerShell|[Azure PowerShell](data-lake-storage-directory-file-acl-powershell.md)|

A webkiszolgáló-naplózási adatainak feltöltéséhez, valamint más típusú adatmennyiségek (például a közösségi érzelmek adatainak) feltöltéséhez jó módszer a saját egyéni parancsfájljainak/alkalmazásainak megírása, mivel lehetővé teszi a rugalmasságot, hogy a nagyobb big data alkalmazás részeként feltöltse az adatfeltöltési összetevőt is. Bizonyos esetekben ez a kód parancsfájl vagy egyszerű parancssori segédprogram formájában is elvégezhető. Más esetekben a kód a big data feldolgozás üzleti alkalmazásba vagy megoldásba való integrálására használható.

### <a name="data-associated-with-azure-hdinsight-clusters"></a>Az Azure HDInsight-fürtökhöz kapcsolódó adatmennyiség

A legtöbb HDInsight-fürt (Hadoop, HBase, Storm) támogatja az adattároló adattárként való Data Lake Storage Gen2. A HDInsight-fürtök az Azure Storage-Blobokból (WASB) származó adatokhoz férnek hozzá. A jobb teljesítmény érdekében a WASB származó adatok másolása a fürthöz társított Data Lake Storage Gen2-fiókba. Az adatmásoláshoz a következő eszközök használhatók.

Az alábbi lista azokat az eszközöket tartalmazza, amelyekkel a HDInsight-fürtökhöz kapcsolódó adatbevitelt végezheti el.

|Eszköz | Útmutató |
|---|--|
|Apache DistCp | [Az DistCp használata az Azure Storage-blobok és a Azure Data Lake Storage Gen2 közötti adatmásoláshoz](./data-lake-storage-use-distcp.md) |
|AzCopy eszköz | [Adatok átvitele a AzCopy](../common/storage-use-azcopy-v10.md) |
|Azure Data Factory | [Adatok másolása Azure Data Lake Storage Gen2ba vagy onnan a Azure Data Factory használatával](../../data-factory/load-azure-data-lake-storage-gen2.md) |

### <a name="data-stored-in-on-premises-or-iaas-hadoop-clusters"></a>A helyszíni vagy IaaS Hadoop-fürtökben tárolt adatkészletek

Nagy mennyiségű adattal rendelkezhetnek a meglévő Hadoop-fürtökben, helyileg a HDFS-t használó gépeken. Előfordulhat, hogy a Hadoop-fürtök helyszíni környezetben találhatók, vagy egy Azure-beli IaaS-fürtön belül vannak. Előfordulhat, hogy az ilyen jellegű adatmásolásra vonatkozó követelmények egy egyszeri vagy ismétlődő módon Azure Data Lake Storage Gen2. Ennek eléréséhez számos lehetőség közül választhat. Az alábbiakban felsoroljuk az alternatívák listáját, valamint a hozzájuk kapcsolódó kompromisszumokat.

| Módszer | Részletek | Előnyök | Megfontolandó szempontok |
| --- | --- | --- | --- |
| Azure Data Factory (ADF) használatával közvetlenül a Hadoop-fürtökről másolhatja az adatok Azure Data Lake Storage Gen2 |[Az ADF támogatja a HDFS adatforrásként](../../data-factory/connector-hdfs.md) |Az ADF beépített támogatást nyújt a HDFS és az első osztályú végpontok közötti felügyelethez és figyeléshez |A adatkezelés átjárót a helyszíni vagy a IaaS-fürtön kell telepíteni |
| Adatok másolása a Hadoop-ből az Azure Storage-ba a Distcp használatával. Ezután másolja az Azure Storage-ból származó adatok Data Lake Storage Gen2 a megfelelő mechanizmus használatával. |Az adatok az Azure Storage-ból Data Lake Storage Gen2 a következő használatával másolhatók: <ul><li>[Azure Data Factory](../../data-factory/copy-activity-overview.md)</li><li>[AzCopy eszköz](../common/storage-use-azcopy-v10.md)</li><li>[HDInsight-fürtökön futó Apache DistCp](data-lake-storage-use-distcp.md)</li></ul> |Használhatja a nyílt forráskódú eszközöket. |Többlépéses folyamat, amely több technológiát is magában foglal |

### <a name="really-large-datasets"></a>Nagyon nagy adatkészletek

A több terabájtos tartományba tartozó adatkészletek feltöltéséhez a fent ismertetett módszerek használatával időnként lassú és költséges lehet. Ilyen esetekben használhatja az Azure ExpressRoute-t is.  

Az Azure ExpressRoute lehetővé teszi privát kapcsolatok létrehozását az Azure-adatközpontok és a helyszíni infrastruktúra között. Ez megbízható lehetőséget biztosít a nagy mennyiségű adatátvitelhez. További információt az [Azure ExpressRoute dokumentációjában](../../expressroute/expressroute-introduction.md)talál.

## <a name="process-the-data"></a>Az adatfeldolgozás

Ha az adatai elérhetők a Data Lake Storage Gen2ban, a támogatott big data alkalmazásokkal is futtathatja az elemzést. 

![Data Lake Storage Gen2ban lévő adatelemzés](./media/data-lake-storage-data-scenarios/analyze-data.png "Data Lake Storage Gen2ban lévő adatelemzés")

Az alábbi lista azokat az eszközöket tartalmazza, amelyek segítségével adatelemzési feladatokat futtathat az Data Lake Storage Gen2 tárolt adatokon.

|Eszköz | Útmutató |
|---|--|
|Azure HDInsight | [Az Azure Data Lake Storage Gen2 használata Azure HDInsight-fürtökkel](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) |
|Azure Databricks | [Azure Data Lake Storage Gen2](/azure/databricks/data/data-sources/azure/azure-datalake-gen2)<br><br>[Gyors útmutató: Azure Data Lake Storage Gen2ban lévő adatelemzés Azure Databricks használatával](./data-lake-storage-quickstart-create-databricks-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)<br><br>[Oktatóanyag: Adatok kinyerése, átalakítása és betöltése az Azure Databricks használatával](/azure/databricks/scenarios/databricks-extract-load-sql-data-warehouse?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)|

## <a name="visualize-the-data"></a>Az adatok vizualizációja

A Power BI-összekötővel hozhatja létre a Data Lake Storage Gen2 tárolt adatvizualizációk ábrázolását. Lásd: az [adatelemzés Azure Data Lake Storage Gen2 Power bi használatával](/power-query/connectors/datalakestorage).

## <a name="download-the-data"></a>Az adatok letöltése

Előfordulhat, hogy a következő forgatókönyvek esetében is szeretné letölteni vagy áthelyezni az adatok Azure Data Lake Storage Gen2:

* Más adattárakba helyezheti át az adatait a meglévő adatfeldolgozási folyamatokkal. Előfordulhat például, hogy át szeretné helyezni Data Lake Storage Gen2 adatait Azure SQL Database vagy egy SQL Server példányba.

* Az alkalmazások prototípusának kiépítése során az IDE-környezetekben tárolt adatfeldolgozási folyamatokat a helyi számítógépre töltheti le.

![Kimenő adatok Data Lake Storage Gen2](./media/data-lake-storage-data-scenarios/egress-data.png "Kimenő adatok Data Lake Storage Gen2")

Az alábbi lista azokat az eszközöket tartalmazza, amelyekkel adatok tölthetők le a Data Lake Storage Gen2ról.

|Eszköz | Útmutató |
|---|--|
|Azure Data Factory | [Másolási tevékenység az Azure Data Factoryben](../../data-factory/copy-activity-overview.md) |
|Apache DistCp | [Az DistCp használata az Azure Storage-blobok és a Azure Data Lake Storage Gen2 közötti adatmásoláshoz](./data-lake-storage-use-distcp.md) |
|Azure Storage Explorer|[ kezelése az Azure Storage Explorerrel az Azure Data Lake Storage Gen2-ben](data-lake-storage-explorer.md)|
|AzCopy eszköz|[Adatok átvitele a AzCopy és a blob Storage szolgáltatással](../common/storage-use-azcopy-v10.md#transfer-data)|
