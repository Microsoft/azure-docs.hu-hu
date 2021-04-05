---
title: Adatok másolása az Azure-Blob Storageba
description: 'Megtudhatja, hogyan másolhat blob-információkat Azure Data Factoryba. A minta: adatok másolása Azure-Blob Storage és-Azure SQL Databaseba.'
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: f1343f900e12bff09c0436ca52d8b091fe48a181
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100393547"
---
# <a name="copy-data-to-or-from-azure-blob-storage-using-azure-data-factory"></a>Adatok másolása az Azure Blob Storageba vagy onnan az Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-azure-blob-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-azure-blob-storage.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse [meg az Azure Blob Storage Connector v2-ben](../connector-azure-blob-storage.md)című témakört.


Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok Azure-Blob Storageba való másolásához. Az [adattovábbítási tevékenységekről](data-factory-data-movement-activities.md) szóló cikkre épül, amely általános áttekintést nyújt az adatáthelyezésről a másolási tevékenységgel.

## <a name="overview"></a>Áttekintés
Bármilyen támogatott forrásból származó adattárból átmásolhatja az adatait az Azure Blob Storageba vagy az Azure Blob Storageból bármely támogatott fogadó adattárba. A következő táblázat felsorolja a másolási tevékenység által a forrásként vagy a nyelőként támogatott adattárakat. Áthelyezheti például **az adatait egy** SQL Server adatbázisból vagy egy Azure SQL Database-adatbázisból egy Azure Blob **Storage-ba** . Az Azure **blob Storage-** **ból** az Azure szinapszis analyticsbe vagy egy Azure Cosmos db gyűjteménybe is másolhat adatok.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Támogatott esetek
Az **Azure Blob Storageról** a következő adattárakba másolhatja az adatait:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Az adatok a következő adattárakból másolhatók az **Azure Blob Storageba**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!IMPORTANT]
> A másolási tevékenység támogatja az adatok másolását az általános célú Azure Storage-fiókokba, valamint a gyors/ritka elérésű blob Storage-ba. A tevékenység támogatja a **blokk, Hozzáfűzés vagy oldal Blobok olvasását**, de **csak a Blobok blokkolását** támogatja. Az Azure Premium Storage nem támogatott fogadóként, mert az oldal Blobok támogatják.
>
> A másolási tevékenység nem törli a forrás adatait, miután az adatok sikeresen át lettek másolva a célhelyre. Ha a sikeres másolás után törölni szeretné a forrásadatokat, hozzon létre egy [egyéni tevékenységet](data-factory-use-custom-activities.md) az adattörléshez és a folyamatban lévő tevékenység használatához. Példaként tekintse meg a [blob vagy mappa törlésére szolgáló mintát a githubon](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/DeleteBlobFileFolderCustomActivity).

## <a name="get-started"></a>Bevezetés
Létrehozhat egy másolási tevékenységgel rendelkező folyamatot, amely az Azure-Blob Storage különböző eszközök/API-k használatával helyezi át az adatátvitelt.

A folyamat létrehozásának legegyszerűbb módja a **Másolás varázsló** használata. Ebből a cikkből [](#walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage) megtudhatja, hogyan hozhat létre folyamatokat egy Azure Blob Storage helyről egy másik Azure Blob Storage helyre történő adatmásoláshoz. Az adatok Azure-Blob Storageból Azure SQL Databaseba másolásához szükséges folyamat létrehozásával kapcsolatos oktatóanyagért lásd [: oktatóanyag: folyamat létrehozása a másolás varázsló használatával](data-factory-copy-data-wizard-tutorial.md).

A következő eszközöket is használhatja a folyamat létrehozásához: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager template**, **.NET API** és **REST API**. A másolási tevékenységgel rendelkező folyamat létrehozásával kapcsolatos részletes utasításokat a [másolási tevékenységről szóló oktatóanyagban](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) talál.

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépések végrehajtásával hozhat létre egy folyamatot, amely egy forrás adattárból egy fogadó adattárba helyezi át az adatait:

1. Hozzon létre egy **adatelőállítót**. Egy adatelőállító egy vagy több folyamatot is tartalmazhat.
2. **Társított szolgáltatások** létrehozása a bemeneti és kimeneti adattáraknak az adat-előállítóhoz való összekapcsolásához. Ha például egy Azure Blob Storage-ból másolja az adatait Azure SQL Databaseba, két társított szolgáltatást hoz létre az Azure Storage-fiók összekapcsolásához és az adatok Azure SQL Databaseához. Az Azure Blob Storage-ra jellemző társított szolgáltatás tulajdonságainál tekintse meg a [társított szolgáltatás tulajdonságai](#linked-service-properties) című szakaszt.
2. Hozzon létre **adatkészleteket** a másolási művelet bemeneti és kimeneti adatok ábrázolásához. Az utolsó lépésben említett példában létrehoz egy adatkészletet, amely megadja a bemeneti adatokat tartalmazó BLOB-tárolót és mappát. Emellett létrehozhat egy másik adatkészletet is, amely meghatározza a blob Storage-ból másolt adatokat tároló Azure SQL Databaseban található SQL-táblázatot. Az Azure Blob Storagera jellemző adatkészlet-tulajdonságokért lásd: [adatkészlet tulajdonságai](#dataset-properties) szakasz.
3. Hozzon **létre egy másolási tevékenységgel rendelkező folyamatot** , amely egy adatkészletet bemenetként és egy adatkészlet kimenetként való elvégzéséhez szükséges. A korábban említett példában a BlobSource forrásként és SqlSinkként használja a másolási tevékenységhez. Hasonlóképpen, ha Azure SQL Databaseról az Azure Blob Storagera másol, a másolási tevékenységben a SqlSource és a BlobSink is használja. Az Azure Blob Storagera jellemző másolási tevékenység tulajdonságairól a [másolási tevékenység tulajdonságai](#copy-activity-properties) című szakaszban olvashat. Az adattár forrásként vagy fogadóként való használatával kapcsolatos részletekért kattintson az adattár előző szakaszában található hivatkozásra.

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory entitások (társított szolgáltatások, adatkészletek és a folyamat) JSON-definícióit. Ha eszközöket/API-kat használ (kivéve a .NET API-t), akkor ezeket a Data Factory entitásokat JSON-formátumban kell megadnia.  Az adatok Azure Blob Storageba való másolásához használt Data Factory JSON-definíciókkal rendelkező minták esetében lásd a jelen cikk [JSON-példák](#json-examples-for-copying-data-to-and-from-blob-storage  ) című szakaszát.

A következő szakaszokban részletesen ismertetjük azokat a JSON-tulajdonságokat, amelyek az Azure-Blob Storagera jellemző Data Factory entitások definiálásához használatosak.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
Az Azure Storage egy Azure-beli adatgyárhoz való összekapcsolásához két különböző típusú társított szolgáltatás használható. Ezek a következők: **AzureStorage** társított szolgáltatás és **AzureStorageSas** társított szolgáltatás. Az Azure Storage társított szolgáltatása biztosítja az Azure Storage-hoz való globális hozzáféréssel rendelkező adatelőállítót. Míg az Azure Storage SAS (közös hozzáférésű aláírás) társított szolgáltatás az adat-előállító számára korlátozott/időhöz kötött hozzáférést biztosít az Azure Storage-hoz. A két társított szolgáltatás között nincs más különbség. Válassza ki az igényeinek megfelelő társított szolgáltatást. A következő szakaszokban további részleteket talál a két társított szolgáltatásról.

[!INCLUDE [data-factory-azure-storage-linked-services](../../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Egy Azure-Blob Storage bemeneti vagy kimeneti adatokat jelölő adatkészlet megadásához az adatkészlet Type (típus) tulajdonságát állítsa a következőre: **AzureBlob**. Állítsa az adatkészlet **linkedServiceName** tulajdonságát az Azure Storage vagy az Azure Storage sas társított szolgáltatás nevére.  Az adatkészlet típusának tulajdonságai határozzák meg a **blob** tárolót és a blob Storage **mappáját** .

Az adatkészletek definiálásához rendelkezésre álló JSON-elemek teljes listáját & az [adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben találja. Az adathalmazok (például a struktúra, a rendelkezésre állás és a szabályzat) minden adatkészlet esetében hasonlóak (például az Azure SQL, az Azure Blob, az Azure Table stb.).

A adat-előállító a következő CLS-kompatibilis .NET-alapú típusú értékeket támogatja a "Structure" típusú adatforrásokhoz, például az Azure blobhoz: Int16, Int32, Int64, Single, Double, decimális, byte [], bool, string, GUID, datetime, DateTimeOffset, TimeSpan. Data Factory automatikusan végrehajtja a típus-konverziókat, amikor a forrás adattárból egy fogadó adattárba helyezi át az adatátvitelt.

A **typeProperties** szakasz különbözik az egyes adatkészletek típusaitól, és információt nyújt az adattárban található adatok helyéről, formátumáról stb. A **AzureBlob** adatkészlet típusú adatkészlet typeProperties szakasza a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| folderPath |A tároló és a mappa elérési útja a blob Storage-ban. Példa: myblobcontainer\myblobfolder\ |Yes |
| fileName |A blob neve. a fájlnév nem kötelező és megkülönbözteti a kis-és nagybetűket.<br/><br/>Ha fájlnevet ad meg, a tevékenység (beleértve a másolást is) az adott blobon működik.<br/><br/>Ha nincs megadva a fájlnév, a másolás a folderPath található összes blobot tartalmazza a bemeneti adatkészlethez.<br/><br/>Ha a **fájlnév** nincs megadva egy kimeneti adatkészlethez, és a **preserveHierarchy** nincs megadva a tevékenység-fogadóban, a generált fájl neve a következő formátumú lesz: `Data.<Guid>.txt` (például: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |No |
| partitionedBy |a partitionedBy egy nem kötelező tulajdonság. Ezzel a beállítással megadhatja a dinamikus folderPath és a fájlnevet az idősorozat-adatsorokhoz. A folderPath például minden egyes órányi adatértékhez paraméterként lehet megadni. A részleteket és példákat a [partitionedBy tulajdonság használata című szakaszban](#using-partitionedby-property) találja. |No |
| formátumban | A következő típusú formátumok támogatottak: **Szövegformátum**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. A **Type (típus** ) tulajdonságot állítsa a Format értékre a következő értékek egyikére. További információkért lásd: [Szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), JSON- [Formátum](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [ork-formátum](data-factory-supported-file-and-compression-formats.md#orc-format)és a [parketta formátuma](data-factory-supported-file-and-compression-formats.md#parquet-format) című rész. <br><br> Ha **fájlokat szeretne másolni** a fájl alapú tárolók között (bináris másolás), ugorja át a formátum szakaszt mind a bemeneti, mind a kimeneti adatkészlet-definíciókban. |No |
| tömörítés | Adja meg az adattömörítés típusát és szintjét. A támogatott típusok a következők: **gzip**, **deflate**, **BZip2** és **ZipDeflate**. A támogatott szintek a következők: **optimális** és **leggyorsabb**. További információ: [fájl-és Tömörítési formátumok Azure Data Factoryban](data-factory-supported-file-and-compression-formats.md#compression-support). |No |

### <a name="using-partitionedby-property"></a>A partitionedBy tulajdonság használata
Ahogy azt az előző szakaszban is említettük, megadhat egy dinamikus folderPath és fájlnevet az idősorozat-adatokhoz a **partitionedBy** tulajdonsággal, [Data Factory függvénnyel és a rendszerváltozókkal](data-factory-functions-variables.md).

További információ az idősorozat-adatkészletekről, az ütemezésről és a szeletekről: [adatkészletek létrehozása](data-factory-create-datasets.md) és [& végrehajtási cikkek ütemezése](data-factory-scheduling-and-execution.md) .

#### <a name="sample-1"></a>1. példa

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

Ebben a példában a {slice} helyére Data Factory rendszerváltozó értékének a megadott formátumban (YYYYMMDDHH) SliceStart kell lennie. A SliceStart a szelet kezdő időpontját jelöli. A folderPath különbözik az egyes szeletekhez. Például: tulajdonság beállítása wikidatagateway/wikisampledataout/2014100103 vagy tulajdonság beállítása wikidatagateway/wikisampledataout/2014100104

#### <a name="sample-2"></a>2. példa

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
[
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```

Ebben a példában a SliceStart év, hónap, nap és időpont a folderPath és a fájlnév tulajdonságai által használt különálló változókra van kinyerve.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A tevékenységek definiálásához elérhető & tulajdonságok teljes listáját a [folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben találja. A tulajdonságok, például a név, a leírás, a bemeneti és a kimeneti adatkészletek, valamint a házirendek minden típusú tevékenységhez elérhetők. Míg a tevékenység **typeProperties** szakaszában elérhető tulajdonságok az egyes tevékenységtípusok esetében eltérőek. Másolási tevékenység esetén a források és a nyelők típusaitól függően változnak. Ha egy Azure-Blob Storageból helyez át adatátvitelt, a másolási tevékenységben adja meg a forrás típusát a **BlobSource** értékre. Hasonlóképpen, ha az adatáthelyezést egy Azure-Blob Storageba helyezi át, a másolási tevékenységben állítsa be a fogadó típusát a **BlobSink** értékre. Ez a szakasz a BlobSource és a BlobSink által támogatott tulajdonságok listáját tartalmazza.

A **BlobSource** a következő tulajdonságokat támogatja a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| rekurzív |Azt jelzi, hogy az adatok rekurzív módon olvashatók-e az alárendelt mappákból, vagy csak a megadott mappából. |True (alapértelmezett érték), hamis |No |

A **BlobSink** a következő tulajdonságokat támogatja **typeProperties** szakasz:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| copyBehavior |Meghatározza a másolási viselkedést a forrás BlobSource vagy fájlrendszerének használatakor. |<b>PreserveHierarchy</b>: megőrzi a fájl hierarchiáját a célmappában. A forrásfájl a forrás mappájához relatív elérési útja megegyezik a célfájl relatív elérési útjával.<br/><br/><b>FlattenHierarchy</b>: a forrás mappából származó összes fájl a célmappa első szintjén található. A célfájl automatikusan generált névvel rendelkezik. <br/><br/><b>MergeFiles</b>: az összes fájlt egyesíti a forrás mappájából egy fájlba. Ha meg van adva a fájl/blob neve, az egyesített fájl neve a megadott név lesz. Ellenkező esetben az automatikusan generált fájlnév lenne. |No |

A **BlobSource** ezt a két tulajdonságot is támogatja a visszamenőleges kompatibilitás érdekében.

* **treatEmptyAsNull**: meghatározza, hogy a null értékű vagy üres sztringet null értékként kell-e kezelni.
* **skipHeaderLineCount** – azt határozza meg, hogy hány sort kell kihagyni. Csak akkor alkalmazható, ha a bemeneti adatkészlet Szövegformátum használ.

Hasonlóképpen, a **BlobSink** a következő tulajdonságot támogatja a visszamenőleges kompatibilitás érdekében.

* **blobWriterAddHeader**: azt adja meg, hogy a rendszer hozzáadja-e az oszlopok definícióinak fejlécét egy kimeneti adatkészletbe való írás során.

Az adatkészletek mostantól a következő tulajdonságokat támogatják, amelyek ugyanazt a funkciót implementálják: **treatEmptyAsNull**, **skipLineCount**, **firstRowAsHeader**.

Az alábbi táblázat útmutatást nyújt az új adatkészlet tulajdonságainak a blob forrás/fogadó tulajdonságainak helyett történő használatáról.

| Másolási tevékenység tulajdonsága | Adatkészlet tulajdonsága |
|:--- |:--- |
| skipHeaderLineCount a BlobSource |skipLineCount és firstRowAsHeader. A rendszer először kihagyja a vonalakat, majd az első sort beolvassa fejlécként. |
| treatEmptyAsNull a BlobSource |treatEmptyAsNull a bemeneti adatkészleten |
| blobWriterAddHeader a BlobSink |firstRowAsHeader a kimeneti adatkészleten |

A tulajdonságok részletes ismertetését lásd: [Szövegformátum megadása](data-factory-supported-file-and-compression-formats.md#text-format) szakasz.

### <a name="recursive-and-copybehavior-examples"></a>rekurzív és copyBehavior példák
Ez a szakasz a rekurzív és copyBehavior értékek különböző kombinációinak másolási műveletének eredményét írja le.

| rekurzív | copyBehavior | Eredményül kapott viselkedés |
| --- | --- | --- |
| true |preserveHierarchy |A Mappa1 a következő szerkezettel rendelkezik: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a célmappa Mappa1 ugyanazzal a struktúrával jön létre, mint a forrás<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy |A Mappa1 a következő szerkezettel rendelkezik: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a célként megadott Mappa1 a következő szerkezettel jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;a file1 automatikusan generált neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;a Fájl2 automatikusan generált neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;a fájl3 automatikusan generált neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;a File4 automatikusan generált neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;a File5 automatikusan generált neve |
| true |mergeFiles |A Mappa1 a következő szerkezettel rendelkezik: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a célként megadott Mappa1 a következő szerkezettel jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;A file1 + Fájl2 + fájl3 + File4 + file 5 tartalom egyetlen fájlba van egyesítve automatikusan generált fájlnévvel |
| hamis |preserveHierarchy |A Mappa1 a következő szerkezettel rendelkezik: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a célmappa Mappa1 a következő szerkezettel jön létre<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/><br/><br/>A fájl3, a File4 és a File5 Subfolder1 nem vesznek fel. |
| hamis |flattenHierarchy |A Mappa1 a következő szerkezettel rendelkezik:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a célmappa Mappa1 a következő szerkezettel jön létre<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;a file1 automatikusan generált neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;a Fájl2 automatikusan generált neve<br/><br/><br/>A fájl3, a File4 és a File5 Subfolder1 nem vesznek fel. |
| hamis |mergeFiles |A Mappa1 a következő szerkezettel rendelkezik:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a célmappa Mappa1 a következő szerkezettel jön létre<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;A file1 + Fájl2 tartalma egyetlen fájlba van egyesítve, amely automatikusan létrehozott fájlnevet tartalmaz. a file1 automatikusan generált neve<br/><br/>A fájl3, a File4 és a File5 Subfolder1 nem vesznek fel. |

## <a name="walkthrough-use-copy-wizard-to-copy-data-tofrom-blob-storage"></a>Forgatókönyv: másolási varázsló használata az adatok Blob Storageba való másolásához
Nézzük meg, hogyan másolhat gyorsan egy Azure Blob Storage-ba vagy az adatokba. Ebben az útmutatóban a forrás és a cél adattárolók a következő típusúak: Azure Blob Storage. Az ebben a forgatókönyvben szereplő folyamat egy mappából egy másik mappába másolja az adott blob-tárolóban lévő adatok adatait. Ez a bemutató szándékosan egyszerűvé teszi a beállítások vagy tulajdonságok megjelenítését a forrásként vagy fogadóként való Blob Storage használatakor.

### <a name="prerequisites"></a>Előfeltételek
1. Ha még nem rendelkezik ilyennel, hozzon létre egy általános célú **Azure Storage-fiókot** . Ebben az útmutatóban a blob Storage-t a **forrás** és a **cél** adattárként is használhatja. Ha nem rendelkezik Azure Storage-fiókkal, tekintse meg a [Storage-fiók létrehozása](../../storage/common/storage-account-create.md) című cikket a létrehozás lépéseihez.
2. Hozzon létre egy **adfblobconnector** nevű BLOB-tárolót a Storage-fiókban.
4. Hozzon létre egy **input** nevű mappát a **adfblobconnector** -tárolóban.
5. Hozzon létre egy **emp.txt** nevű fájlt az alábbi tartalommal, és töltse fel a **bemeneti** mappába a következő eszközök használatával: [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/)
    ```json
    John, Doe
    Jane, Doe
    ```

### <a name="create-the-data-factory"></a>Az adatelőállító létrehozása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Kattintson az **erőforrás létrehozása** elemre a bal felső sarokban, kattintson az **intelligencia és Analitika** elemre, majd a **Data Factory** elemre.
3. Az **új adatgyár** panelen:  
    1. Adja  meg a ADFBlobConnectorDF **nevet**. Az Azure data factory nevének globálisan egyedinek kell lennie. Ha a következő hibaüzenetet kapja: `*Data factory name "ADFBlobConnectorDF" is not available` , módosítsa az adatgyár nevét (például yournameADFBlobConnectorDF), és próbálkozzon újra a létrehozással. A Data Factory-összetevők elnevezési szabályait a [Data Factory - Naming Rules](data-factory-naming-rules.md) (Data Factory – Elnevezési szabályok) című témakörben találhatja.
    2. Válassza ki az Azure- **előfizetését**.
    3. Az erőforráscsoport területen válassza a **meglévő használata** lehetőséget egy meglévő erőforráscsoport kiválasztásához (vagy) válassza az **új létrehozása** lehetőséget az erőforráscsoport nevének megadásához.
    4. Válassza ki a Data Factory **helyét**.
    5. A panel alján jelölje be a **Pin to dashboard** (Rögzítés az irányítópulton) jelölőnégyzetet.
    6. Kattintson a **Létrehozás** lehetőségre.
3. A létrehozás befejezése után a **Data Factory** panel jelenik meg, ahogy az a következő képen látható: a "a"  ![ adatok gyári kezdőlapja](./media/data-factory-azure-blob-connector/data-factory-home-page.png)

### <a name="copy-wizard"></a>Másolás varázsló
1. A Data Factory kezdőlapján kattintson az **Adatmásolás** csempére **adatok másolása varázsló** elindításához egy külön lapon.  

    > [!NOTE]
    > Ha úgy látja, hogy a webböngésző "engedélyezés..." állapotban van, tiltsa le/törölje a **harmadik féltől származó cookie-k és a hely** adatbeállításának letiltása/törlése beállítást (vagy), és hozzon létre kivételt a **login.microsoftonline.com** , majd próbálja meg újból elindítani a varázslót.
2. A **Properties** (Tulajdonságok) oldalon:
    1. Adja meg a **CopyPipeline** a **feladat neveként**. A feladat neve a folyamat neve az adat-előállítóban.
    2. Adja meg a feladat **leírását** (nem kötelező).
    3. A **feladat lépésszám vagy a feladat ütemezéséhez** tartsa a **futtatást rendszeresen az ütemezés** beállításnál. Ha ezt a feladatot csak egyszer szeretné futtatni az ütemezett futtatás helyett, válassza a **Futtatás most** lehetőséget. Ha a lehetőséget választja, akkor a **most már egyszer futtatott** [folyamat](data-factory-create-pipelines.md#onetime-pipeline) jön létre.
    4. Tartsa meg az **ismétlődő minta** beállításait. Ez a feladat naponta fut a következő lépésben megadott kezdési és befejezési időpontok között.
    5. A **kezdési dátum** és a **04/21/2017** közötti érték módosítása
    6. Módosítsa a **befejezési dátumot** **04/25/2017**-ra. A naptárban való böngészés helyett érdemes beírni a dátumot.
    8. Kattintson a **Tovább** gombra.
        ![Copy (Másolás) eszköz – Properties (Tulajdonságok) oldal](./media/data-factory-azure-blob-connector/copy-tool-properties-page.png)
3. A **Source data store** (Forrásadattár) oldalon kattintson az **Azure Blob Storage** csempére. Az oldal használatával megadhatja a forrásadattárat a másolási feladathoz. Használhatja egy meglévő adattár társított szolgáltatását, vagy megadhat egy új adattárat. Meglévő társított szolgáltatás használatához válasszon **a meglévő társított szolgáltatások közül** , és válassza ki a megfelelő társított szolgáltatást.
    ![Copy (Másolás) eszköz – Source data store (Forrásadattár) oldal](./media/data-factory-azure-blob-connector/copy-tool-source-data-store-page.png)
4. A **Specify the Azure Blob storage account** (Az Azure Blob Storage-fiók megadása) oldalon:
    1. Tartsa meg az automatikusan generált nevet a **kapcsolatok neveként**. A kapcsolat neve a következő típusú társított szolgáltatás neve: Azure Storage.
    2. Győződjön meg arról, hogy az **Account selection method** (Fiókválasztási módszer) mezőben a **From Azure subscriptions** (Azure-előfizetésekből) lehetőség van kiválasztva.
    3. Válassza ki az Azure-előfizetését, vagy **válassza az összes kiválasztása lehetőséget** az **Azure-előfizetéshez**.
    4. A kiválasztott előfizetéshez elérhető Azure Storage-fiókok listájából válasszon ki egy **Azure Storage-fiókot**. Azt is megteheti, hogy manuálisan adja meg a Storage-fiók beállításait a **fiók kiválasztása módszerhez** tartozó **manuális** beállítás bejelölésével.
    5. Kattintson a **Tovább** gombra.  
        ![Copy (Másolás) eszköz – Specify the Azure Blob storage account (Az Azure Blob Storage-fiók megadása) oldal](./media/data-factory-azure-blob-connector/copy-tool-specify-azure-blob-storage-account.png)
5. A **Choose the input file or folder** (A bemeneti fájl vagy mappa kiválasztása) oldalon:
    1. Kattintson duplán a **adfblobcontainer** elemre.
    2. Válassza a **bevitel** lehetőséget, majd kattintson a **kiválasztás** elemre. Ebben az útmutatóban kiválasztja a bemeneti mappát. Ehelyett a mappában lévő emp.txt fájlt is kiválaszthatja.
        ![Másolási eszköz – válassza ki a bemeneti fájlt vagy az 1. mappát](./media/data-factory-azure-blob-connector/copy-tool-choose-input-file-or-folder.png)
6. A **bemeneti fájl vagy mappa kiválasztása** oldalon:
    1. Győződjön meg arról, hogy a **fájl vagy mappa** **adfblobconnector/input** értékűre van beállítva. Ha a fájlok almappákban találhatók, például 2017/04/01, 2017/04/02 stb., írja be a következőt: adfblobconnector/input/{Year}/{month}/{Day} a fájlhoz vagy mappához. Amikor lenyomja a TAB billentyűt a szövegmezőből, három legördülő lista jelenik meg az év (éééé), a hónap (MM) és a nap (NN) formátumának kiválasztásához.
    2. Ne állítsa **rekurzív módon a másolási fájlt**. Ezzel a beállítással rekurzív módon áthaladhat a célhelyre másolandó fájlok mappáin keresztül.
    3. Ne a **bináris másolás** beállítást. Válassza ezt a lehetőséget a forrásfájl bináris másolatának elvégzéséhez a célhelyre. Ne válassza ezt a bemutatót, hogy a következő lapokon további lehetőségek is megjelenjenek.
    4. Győződjön meg arról, hogy a **tömörítési típus** nincs értékre van **állítva.** Válassza ki ezt a beállítást, ha a forrásfájlok a támogatott formátumok egyikében vannak tömörítve.
    5. Kattintson a **Tovább** gombra.
    ![Másolási eszköz – válassza ki a bemeneti fájlt vagy a 2. mappát](./media/data-factory-azure-blob-connector/chose-input-file-folder.png)
7. A **File format settings** (Fájlformátum beállításai) oldalon a fájl elemzése során a varázsló által automatikusan észlelt elválasztó karakterek és séma láthatók.
    1. Erősítse meg a következő beállításokat:  
        a. A **fájlformátum** **szöveg formátumú**. A támogatott formátumok a legördülő listában láthatók. Például: JSON, Avro, ork, Parque.
       b. Az **oszlop elválasztója** a következőre van beállítva: `Comma (,)` . A legördülő listában megtekintheti a Data Factory által támogatott más oszlopos elválasztó karaktereket. Egyéni határolójel is megadható.
       c. A **sor elválasztója** a következőre van beállítva: `Carriage Return + Line feed (\r\n)` . A legördülő listában a Data Factory által támogatott további sorok elválasztó is látható. Egyéni határolójel is megadható.
       d. A **kihagyott sorok száma** értéke **0**. Ha azt szeretné, hogy a rendszer néhány sort kihagyjon a fájl elejére, itt adja meg a számot.
       e. Az **első adatsorban** nincsenek megadva az oszlopnevek. Ha a forrásfájlok az első sorban oszlopnevek szerepelnek, válassza ezt a lehetőséget.
       f. Az **üres oszlop értékének null** értékűként való kezelése beállítás be van állítva.
    2. A **Speciális beállítások** lehetőség kibontásával tekintheti meg a rendelkezésre álló speciális lehetőséget.
    3. A lap alján tekintse meg az adatok **előnézetét** a emp.txt fájlból.
    4. Kattintson a **séma** fülre az alján, és tekintse meg azt a sémát, amelyet a másolási varázsló következtetett a forrásfájl információinak megtekintésével.
    5. Az elválasztó karakterek megtekintése és adatok előzetes megtekintése után kattintson a **Next** (Tovább) gombra.
    ![Copy (Másolás eszköz) – File format settings (Fájlformátum beállításai) oldal](./media/data-factory-azure-blob-connector/copy-tool-file-format-settings.png)
8. A **cél adattár lapon** válassza az **Azure Blob Storage** lehetőséget, majd kattintson a **tovább** gombra. Ebben az útmutatóban az Azure Blob Storage is használja a forrás-és célhelyként szolgáló adattárakként.  
    ![Másolási eszköz – célhely-adattár kiválasztása](media/data-factory-azure-blob-connector/select-destination-data-store.png)
9. **Az Azure Blob Storage-fiók lapon válassza** a következőt:  
    1. Adja meg a **AzureStorageLinkedService** a **kapcsolatok neve** mezőhöz.
    2. Győződjön meg arról, hogy az **Account selection method** (Fiókválasztási módszer) mezőben a **From Azure subscriptions** (Azure-előfizetésekből) lehetőség van kiválasztva.
    3. Válassza ki az Azure- **előfizetését**.
    4. Válassza ki az Azure Storage-fiókját.
    5. Kattintson a **Tovább** gombra.
10. A **kimeneti fájl vagy mappa kiválasztása** oldalon:  
    1. adja meg a **mappa elérési útját** **adfblobconnector/output/{Year}/{month}/{Day**} néven. Adja meg a **fület**.
    1. Az **év** esetében válassza az **éééé** lehetőséget.
    1. A **hónapnál** ellenőrizze, hogy az értéke **mm**.
    1. A **nap** beállításnál ellenőrizze, hogy az a **dd** értékre van-e állítva.
    1. Győződjön meg arról, hogy a **tömörítési típus** nincs értékre van **állítva.**
    1. Győződjön meg arról, hogy a **másolási viselkedés** a **fájlok egyesítésére** van beállítva. Ha már létezik ilyen nevű kimeneti fájl, a rendszer az új tartalmat hozzáadja ugyanahhoz a fájlhoz a végén.
    1. Kattintson a **Tovább** gombra.
       ![Másolási eszköz – a kimeneti fájl vagy mappa kiválasztása](media/data-factory-azure-blob-connector/choose-the-output-file-or-folder.png)
11. A **fájlformátum beállításai** lapon tekintse át a beállításokat, majd kattintson a **tovább** gombra. Az egyik további lehetőség az, hogy egy fejlécet ad hozzá a kimeneti fájlhoz. Ha ezt a beállítást választja, a rendszer egy fejlécsort ad hozzá az oszlopok nevével a forrás sémájában. A forrás sémájának megtekintésekor átnevezheti az alapértelmezett oszlopnevek nevét. Megváltoztathatja például az első oszlop nevét és a vezetéknév és a második oszlop nevét. Ezt követően a kimeneti fájl a következő névvel rendelkező fejléccel lesz létrehozva oszlopnevekként.
    ![Másolási eszköz fájlformátumának beállításai a célhelyhez](media/data-factory-azure-blob-connector/file-format-destination.png)
12. A **teljesítmény beállításai** lapon ellenőrizze, hogy a **felhőalapú egységek** és a **párhuzamos másolatok** értéke **automatikus**-e, majd kattintson a Tovább gombra. Ezekről a beállításokról a [másolási tevékenységek teljesítményének és hangolásának útmutatója](data-factory-copy-activity-performance.md#parallel-copy)című témakörben olvashat bővebben.
    ![Eszköz – teljesítmény beállításainak másolása](media/data-factory-azure-blob-connector/copy-performance-settings.png)
14. Az **Összefoglalás** lapon tekintse át az összes beállítást (a feladat tulajdonságait, a forrás és a cél beállításait és a másolási beállításokat), majd kattintson a **tovább** gombra.
    ![Eszköz másolása – összefoglalás lap](media/data-factory-azure-blob-connector/copy-tool-summary-page.png)
15. Tekintse át a **Summary** (Összegzés) oldalon szereplő információkat, majd kattintson a **Finish** (Befejezés) gombra. A varázsló létrehoz két társított szolgáltatást, két adathalmazt (bemeneti és kimeneti), valamint egy folyamatot a data factoryban (ahonnét elindította a Másolás varázslót).
    ![Eszköz másolása – üzembe helyezés lap](media/data-factory-azure-blob-connector/copy-tool-deployment-page.png)

### <a name="monitor-the-pipeline-copy-task"></a>A folyamat figyelése (másolási feladat)

1. Kattintson a hivatkozásra a `Click here to monitor copy pipeline` **központi telepítés** lapon.
2. Az **alkalmazás figyelése és kezelése** külön lapon jelenik meg.  ![Alkalmazás figyelése és kezelése](media/data-factory-azure-blob-connector/monitor-manage-app.png)
3. Módosítsa a **kezdési** időt a felül és a Befejezés időpontjára `04/19/2017` , majd kattintson az  `04/27/2017` **alkalmaz** gombra.
4. A **tevékenység Windows** listájában öt tevékenység-ablak jelenik meg. A **WindowStart** időpontjának a folyamat első napjától a folyamat végéig terjedő időszakra kell vonatkoznia.
5. Kattintson a **frissítés** gombra a **TEVÉKENYSÉGhez tartozó Windows** -lista számára, amíg meg nem jelenik az összes tevékenység-ablak állapota kész értékre van állítva.
6. Most ellenőrizze, hogy a kimeneti fájlok létrejöttek-e a adfblobconnector tároló kimeneti mappájába. A kimeneti mappában a következő mappastruktúrát kell megjelennie:
    ```
    2017/04/21
    2017/04/22
    2017/04/23
    2017/04/24
    2017/04/25
    ```
   Az adat-előállítók figyelésével és kezelésével kapcsolatos részletes információkért lásd: [Data Factory-folyamat figyelése és kezelése](data-factory-monitor-manage-app.md) .

### <a name="data-factory-entities"></a>Entitások Data Factory
Most váltson vissza a lapra a Data Factory kezdőlapján. Figyelje meg, hogy két társított szolgáltatás, két adatkészlet és egy folyamat van az adat-előállítóban.

![Data Factory Kezdőlap entitásokkal](media/data-factory-azure-blob-connector/data-factory-home-page-with-numbers.png)

Data Factory szerkesztő elindításához kattintson a **Szerző és üzembe helyezés** elemre.

![A Data Factory szerkesztője](media/data-factory-azure-blob-connector/data-factory-editor.png)

A következő Data Factory entitásokat kell megjelennie az adatai-előállítóban:

- Két társított szolgáltatás. Egyet a forráshoz és a másikhoz a célhelyhez. Ebben az útmutatóban a társított szolgáltatások is ugyanarra az Azure Storage-fiókra hivatkoznak.
- Két adatkészlet. Egy bemeneti adatkészletet és egy kimeneti adatkészletet. Ebben az útmutatóban mindkettő ugyanazt a BLOB-tárolót használja, de a különböző mappákra (bemeneti és kimeneti) hivatkozik.
- Egy folyamat. A folyamat egy másolási tevékenységet tartalmaz, amely egy blob-forrást és egy blob-fogadót használ az adatok Azure Blob-helyről egy másik Azure Blob-helyre történő másolásához.

A következő szakaszokban további információk találhatók ezekről az entitásokról.

#### <a name="linked-services"></a>Társított szolgáltatások
Két társított szolgáltatásnak kell megjelennie. Egyet a forráshoz és a másikhoz a célhelyhez. Ebben az útmutatóban mindkét definíció ugyanúgy megtekinthető, mint a neveknél. A társított szolgáltatás **típusa** **AzureStorage** értékre van állítva. A társított szolgáltatás definíciójának legfontosabb tulajdonsága a **ConnectionString**, amelyet a Data Factory használ az Azure Storage-fiókhoz való kapcsolódáshoz futásidőben. Hagyja figyelmen kívül a hubName tulajdonságot a definícióban.

##### <a name="source-blob-storage-linked-service"></a>Forrás blob Storage társított szolgáltatás
```json
{
    "name": "Source-BlobStorage-z4y",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=mystorageaccount;AccountKey=**********"
        }
    }
}
```

##### <a name="destination-blob-storage-linked-service"></a>Cél blob Storage társított szolgáltatás

```json
{
    "name": "Destination-BlobStorage-z4y",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=mystorageaccount;AccountKey=**********"
        }
    }
}
```

Az Azure Storage társított szolgáltatásával kapcsolatos további információkért lásd a [társított szolgáltatás tulajdonságai](#linked-service-properties) című szakaszt.

#### <a name="datasets"></a>Adathalmazok
Két adatkészlet létezik: egy bemeneti adatkészletet és egy kimeneti adatkészletet. Az adatkészlet típusa mindkettő esetén a **AzureBlob** értékre van állítva.

A bemeneti adatkészlet a **adfblobconnector** blob-tárolójának **bemeneti** mappájára mutat. A **külső** tulajdonság értéke TRUE ( **igaz** ) értékre van állítva ennél az adatkészletnél, mert a folyamat nem állítja be az adatokat a másolási tevékenységgel, amely bemenetként veszi át ezt az adatkészletet.

A kimeneti adatkészlet ugyanarra a blob-tároló **kimeneti** mappájára mutat. A kimeneti adatkészlet a **SliceStart** rendszerváltozójának évet, hónapját és napját is használja a kimeneti fájl elérési útjának dinamikus kiértékeléséhez. A Data Factory által támogatott függvények és rendszerváltozók listáját lásd: [Data Factory functions és System változók](data-factory-functions-variables.md). A **külső** tulajdonság értéke **false** (alapértelmezett érték), mert ezt az adatkészletet a folyamat állítja elő.

Az Azure Blob-adatkészletek által támogatott tulajdonságokkal kapcsolatos további információkért lásd: [adatkészlet tulajdonságai](#dataset-properties) szakasz.

##### <a name="input-dataset"></a>Bemeneti adatkészlet

```json
{
    "name": "InputDataset-z4y",
    "properties": {
        "structure": [
            { "name": "Prop_0", "type": "String" },
            { "name": "Prop_1", "type": "String" }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "Source-BlobStorage-z4y",
        "typeProperties": {
            "folderPath": "adfblobconnector/input/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

##### <a name="output-dataset"></a>Kimeneti adatkészlet

```json
{
    "name": "OutputDataset-z4y",
    "properties": {
        "structure": [
            { "name": "Prop_0", "type": "String" },
            { "name": "Prop_1", "type": "String" }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "Destination-BlobStorage-z4y",
        "typeProperties": {
            "folderPath": "adfblobconnector/output/{year}/{month}/{day}",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            },
            "partitionedBy": [
                { "name": "year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
                { "name": "month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
                { "name": "day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }
            ]
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
        "external": false,
        "policy": {}
    }
}
```

#### <a name="pipeline"></a>Folyamat
A folyamatnak csak egy tevékenysége van. A tevékenység **típusa** **másolásra** van beállítva. A tevékenység típusának tulajdonságainál két szakaszt, egyet a forrást és a másikat a fogadóhoz. A forrástípus a **BlobSource** értékre van állítva, mivel a tevékenység a blob Storage-ból másolt adatok másolását adja meg. A fogadó típusa **BlobSink** értékre van állítva, és az adatok blob Storage-tárolóba való másolása tevékenység. A másolási tevékenység kimenetként a bemeneti és a OutputDataset-z4y z4y veszi a InputDataset.

A BlobSource és a BlobSink által támogatott tulajdonságokkal kapcsolatos további információkért lásd: [másolási tevékenység tulajdonságai](#copy-activity-properties) szakasz.

```json
{
    "name": "CopyPipeline",
    "properties": {
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource",
                        "recursive": false
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "MergeFiles",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataset-z4y"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset-z4y"
                    }
                ],
                "policy": {
                    "timeout": "1.00:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "style": "StartOfInterval",
                    "retry": 3,
                    "longRetry": 0,
                    "longRetryInterval": "00:00:00"
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "Activity-0-Blob path_ adfblobconnector_input_->OutputDataset-z4y"
            }
        ],
        "start": "2017-04-21T22:34:00Z",
        "end": "2017-04-25T05:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
```

## <a name="json-examples-for-copying-data-to-and-from-blob-storage"></a>JSON-példák az adatok Blob Storageba való másolásához
Az alábbi példák olyan JSON-definíciókat biztosítanak, amelyek segítségével a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy a [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával hozhat létre folyamatokat. Bemutatják, hogyan másolhatók az adatok az Azure Blob Storage és az Azure SQL Databaseba. Az adatok azonban **közvetlenül** a forrásokból bármelyik forrásból átmásolhatók, ha a másolási tevékenység a Azure Data Factoryban [szerepel.](data-factory-data-movement-activities.md#supported-data-stores-and-formats)

### <a name="json-example-copy-data-from-blob-storage-to-sql-database"></a>JSON-példa: adatok másolása Blob Storageból SQL Database
A következő minta a következőket mutatja be:

1. [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties)típusú társított szolgáltatás.
2. [AzureStorage](#linked-service-properties)típusú társított szolgáltatás.
3. [AzureBlob](#dataset-properties)típusú bemeneti [adatkészlet](data-factory-create-datasets.md) .
4. [Tulajdonsága azuresqltable](data-factory-azure-sql-connector.md#dataset-properties)típusú kimeneti [adatkészlet](data-factory-create-datasets.md) .
5. [BlobSource](#copy-activity-properties) és [SqlSink](data-factory-azure-sql-connector.md#copy-activity-properties)használó másolási tevékenységgel rendelkező [folyamat](data-factory-create-pipelines.md) .

A minta egy Azure-blobból származó idősorozat-adatok óránkénti átmásolását végzi el egy Azure SQL-táblába. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszokban ismertetjük.

**Azure SQL társított szolgáltatás:**

```json
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Azure Storage-beli társított szolgáltatás:**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Azure Data Factory az Azure Storage társított szolgáltatásainak két típusát támogatja: **AzureStorage** és **AzureStorageSas**. Az első beállításnál megadhatja a kapcsolati karakterláncot, amely tartalmazza a fiók kulcsát, és a későbbiekben a közös hozzáférésű aláírás (SAS) URI-ját is megadja. Részletekért lásd a [társított szolgáltatások](#linked-service-properties) szakaszt.

**Azure Blob bemeneti adatkészlet:**

Az adatok minden órában egy új blobból származnak (frekvencia: óra, intervallum: 1). A blob mappa elérési útját és fájlnevét a feldolgozás alatt álló szelet kezdési időpontja alapján dinamikusan értékeli a rendszer. A mappa elérési útja az év, hónap és nap részeként használja a kezdési időt, és a fájlnév a kezdési időpont óra részét használja. a "külső": "true" beállítás tájékoztatja Data Factory arról, hogy a tábla kívül esik az adatelőállítón, és nem az adatelőállító tevékenysége.

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" } }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
**Azure SQL kimeneti adatkészlet:**

A minta egy "Sajáttábla" nevű táblába másol egy Azure SQL Database. Hozza létre a táblát az SQL-adatbázisban ugyanazzal a számú oszloppal, mint amennyit a blob CSV-fájljának tárolására vár. Minden órában új sor kerül a táblázatba.

```json
{
  "name": "AzureSqlOutput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Egy folyamat másolási tevékenysége blob forrással és SQL-fogadóval:**

A folyamat egy másolási tevékenységet tartalmaz, amely a bemeneti és a kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra van ütemezve. A folyamat JSON-definíciójában a **forrás** típusa **BlobSource** értékre van állítva, a **fogadó típusa** pedig **SqlSink**.

```json
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoSQL",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
### <a name="json-example-copy-data-from-azure-sql-to-azure-blob"></a>JSON-példa: adatok másolása az Azure SQL-ből az Azure Blobba
A következő minta a következőket mutatja be:

1. [AzureSqlDatabase](data-factory-azure-sql-connector.md#linked-service-properties)típusú társított szolgáltatás.
2. [AzureStorage](#linked-service-properties)típusú társított szolgáltatás.
3. [Tulajdonsága azuresqltable](data-factory-azure-sql-connector.md#dataset-properties)típusú bemeneti [adatkészlet](data-factory-create-datasets.md) .
4. [AzureBlob](#dataset-properties)típusú kimeneti [adatkészlet](data-factory-create-datasets.md) .
5. [SqlSource](data-factory-azure-sql-connector.md#copy-activity-properties) és [BlobSink](#copy-activity-properties)használó másolási tevékenységgel rendelkező [folyamat](data-factory-create-pipelines.md) .

A minta egy Azure SQL-táblából származó idősorozat-adatok Azure-blobba való másolását végzi óránként. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszokban ismertetjük.

**Azure SQL társított szolgáltatás:**

```json
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Azure Storage-beli társított szolgáltatás:**

```json
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
Azure Data Factory az Azure Storage társított szolgáltatásainak két típusát támogatja: **AzureStorage** és **AzureStorageSas**. Az első beállításnál megadhatja a kapcsolati karakterláncot, amely tartalmazza a fiók kulcsát, és a későbbiekben a közös hozzáférésű aláírás (SAS) URI-ját is megadja. Részletekért lásd a [társított szolgáltatások](#linked-service-properties) szakaszt.

**Azure SQL bemeneti adatkészlet:**

A minta feltételezi, hogy létrehozott egy "Sajáttábla" táblát az Azure SQL-ben, és egy "timestampcolumn" nevű oszlopot tartalmaz az idősoros adatsorokhoz.

A "külső": "true" beállítás azt tájékoztatja Data Factory szolgáltatást, hogy a tábla kívül esik az adatelőállítón, és nem az adatelőállító tevékenysége.

```json
{
  "name": "AzureSqlInput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Azure-Blob kimeneti adatkészlete:**

A rendszer óránként egy új blobba írja az adatbevitelt (frekvencia: óra, intervallum: 1). A blob mappájának elérési útját a rendszer dinamikusan kiértékeli a feldolgozás alatt álló szelet kezdési időpontja alapján. A mappa elérési útja a kezdési idő év, hónap, nap és óra részét használja.

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
      "partitionedBy": [
        {
          "name": "Year",
          "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" } }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Másolási tevékenység egy folyamaton az SQL-forrással és a blob-fogadóval:**

A folyamat egy másolási tevékenységet tartalmaz, amely a bemeneti és a kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra van ütemezve. A folyamat JSON-definíciójában a **forrás** típusa **SqlSource** értékre van állítva, a **fogadó típusa** pedig **BlobSink**. A **SqlReaderQuery** tulajdonsághoz megadott SQL-lekérdezés a másoláshoz az elmúlt órában kijelöli az adatforrást.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[
            {
                "name": "AzureSQLtoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureSQLInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 0,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

> [!NOTE]
> Ha az oszlopokat a forrás adatkészletből a fogadó adatkészletből származó oszlopokra kívánja leképezni, tekintse meg [Azure Data Factory az adatkészlet oszlopainak](data-factory-map-columns.md)

## <a name="performance-and-tuning"></a>Teljesítmény és hangolás
A [másolási tevékenység teljesítményének & hangolási útmutatójában](data-factory-copy-activity-performance.md) megismerheti azokat a főbb tényezőket, amelyek hatással vannak az adatáthelyezés (másolási tevékenység) teljesítményére Azure Data Factory és az optimalizálás különféle módjaival.
