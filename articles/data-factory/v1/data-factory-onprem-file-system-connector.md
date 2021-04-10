---
title: Adatok másolása egy fájlrendszerbe a Azure Data Factory használatával
description: Bemutatjuk, hogy miként másolhatók az adatok egy helyszíni fájlrendszerből és a Azure Data Factory használatával.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 9acd2adb68347978bb123d2239eedbb3e5595bc3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100387393"
---
# <a name="copy-data-to-and-from-an-on-premises-file-system-by-using-azure-data-factory"></a>Adatok másolása helyszíni fájlrendszerből és a Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-onprem-file-system-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-file-system.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, lásd: [fájlrendszer-összekötő a v2-ben](../connector-file-system.md).


Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok helyszíni fájlrendszerbe történő másolásához. Az [adattovábbítási tevékenységekről](data-factory-data-movement-activities.md) szóló cikkre épül, amely általános áttekintést nyújt az adatáthelyezésről a másolási tevékenységgel.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="supported-scenarios"></a>Támogatott esetek
Az adatok a helyszíni **fájlrendszerből** a következő adattárakba másolhatók:

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

Az adatok a következő adattárakból egy helyszíni **fájlrendszerbe** másolhatók:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> A másolási tevékenység nem törli a forrásfájlt, miután sikeresen átmásolta a célhelyre. Ha sikeres másolás után törölni kell a forrásfájlt, hozzon létre egy egyéni tevékenységet a fájl törléséhez, és használja a folyamatot a folyamatban.

## <a name="enabling-connectivity"></a>Kapcsolat engedélyezése
A Data Factory **adatkezelés átjárón** keresztül támogatja a helyszíni fájlrendszerhez való csatlakozást. Telepítenie kell a adatkezelés átjárót a helyszíni környezetbe ahhoz, hogy a Data Factory szolgáltatás csatlakozhasson bármely támogatott helyszíni adattárhoz, beleértve a fájlrendszert is. Az adatkezelés átjáróval és az átjáró beállításával kapcsolatos részletes utasításokért lásd: az [adatáthelyezés a helyszíni források és a felhő között adatkezelés átjáróval](data-factory-move-data-between-onprem-and-cloud.md). A adatkezelés átjárón kívül más bináris fájlokat sem kell telepíteni a helyszíni fájlrendszerről történő kommunikációhoz. Akkor is telepítenie kell a adatkezelés-átjárót, ha a fájlrendszer az Azure IaaS virtuális gépen van. Az átjáróval kapcsolatos részletes információkért lásd: [adatkezelés átjáró](data-factory-data-management-gateway.md).

Linux-fájlmegosztás használatához telepítse a [Samba](https://www.samba.org/) -t a Linux-kiszolgálóra, és telepítse adatkezelés átjárót egy Windows Serveren. Adatkezelés átjáró Linux-kiszolgálón való telepítése nem támogatott.

## <a name="getting-started"></a>Első lépések
Létrehozhat egy másolási tevékenységgel rendelkező folyamatot, amely különböző eszközök/API-k használatával helyezi át az adatait egy fájlrendszerbe.

A folyamat létrehozásának legegyszerűbb módja a **Másolás varázsló** használata. Tekintse meg az [oktatóanyag: folyamat létrehozása a másolás varázslóval](data-factory-copy-data-wizard-tutorial.md) című témakört, amely gyors áttekintést nyújt a folyamat létrehozásáról az adatmásolási varázsló használatával.

A következő eszközöket is használhatja a folyamat létrehozásához: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager template**, **.NET API** és **REST API**. A másolási tevékenységgel rendelkező folyamat létrehozásával kapcsolatos részletes utasításokat a [másolási tevékenységről szóló oktatóanyagban](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) talál.

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépések végrehajtásával hozhat létre egy folyamatot, amely egy forrás adattárból egy fogadó adattárba helyezi át az adatait:

1. Hozzon létre egy **adatelőállítót**. Egy adatelőállító egy vagy több folyamatot is tartalmazhat.
2. **Társított szolgáltatások** létrehozása a bemeneti és kimeneti adattáraknak az adat-előállítóhoz való összekapcsolásához. Ha például egy Azure Blob Storage-ból másol egy helyszíni fájlrendszerbe, két társított szolgáltatást hoz létre, hogy a helyszíni fájlrendszert és az Azure Storage-fiókot a saját adatokkal rendelkező gyárához kapcsolja. A helyi fájlrendszerre jellemző társított szolgáltatások tulajdonságairól a [társított szolgáltatás tulajdonságai](#linked-service-properties) című szakaszban olvashat.
3. Hozzon létre **adatkészleteket** a másolási művelet bemeneti és kimeneti adatok ábrázolásához. Az utolsó lépésben említett példában létrehoz egy adatkészletet, amely megadja a bemeneti adatokat tartalmazó BLOB-tárolót és mappát. Emellett létrehoz egy másik adatkészletet is, amely megadja a mappa és a fájl nevét (opcionális) a fájlrendszerben. A helyszíni fájlrendszerre jellemző adatkészlet-tulajdonságokért lásd az [adatkészlet tulajdonságai](#dataset-properties) szakaszt.
4. Hozzon **létre egy másolási tevékenységgel rendelkező folyamatot** , amely egy adatkészletet bemenetként és egy adatkészlet kimenetként való elvégzéséhez szükséges. A korábban említett példában a BlobSource forrásként és FileSystemSinkként használja a másolási tevékenységhez. Hasonlóképpen, ha a helyszíni fájlrendszerről az Azure Blob Storagera másol, a másolási tevékenységben a FileSystemSource és a BlobSink is használja. A helyi fájlrendszerre jellemző másolási tevékenység tulajdonságairól lásd: [másolási tevékenység tulajdonságai](#copy-activity-properties) szakasz. Az adattár forrásként vagy fogadóként való használatával kapcsolatos részletekért kattintson az adattár előző szakaszában található hivatkozásra.

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory entitások (társított szolgáltatások, adatkészletek és a folyamat) JSON-definícióit. Ha eszközöket/API-kat használ (kivéve a .NET API-t), akkor ezeket a Data Factory entitásokat JSON-formátumban kell megadnia.  Az adatok fájlrendszerbe való másolásához használt Data Factory JSON-definíciókkal rendelkező minták esetében lásd a jelen cikk JSON- [példák](#json-examples-for-copying-data-to-and-from-file-system) című szakaszát.

A következő szakaszokban részletesen ismertetjük azokat a JSON-tulajdonságokat, amelyeket a rendszer a fájlrendszerre vonatkozó Data Factory definiálásához használ:

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
Helyszíni fájlrendszert kapcsolhat egy Azure-beli adatgyárhoz a helyszíni **fájlkiszolgáló** társított szolgáltatásával. A következő táblázat a helyszíni fájlkiszolgáló társított szolgáltatásához tartozó JSON-elemek leírásait tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| típus |Győződjön meg arról, hogy a Type tulajdonság értéke **OnPremisesFileServer**. |Yes |
| gazda |Megadja a másolni kívánt mappa gyökerének elérési útját. A karakterláncban a speciális karaktereknél használja a Escape karaktert. Példákat a következő témakörben talál: példa [társított szolgáltatás és adatkészlet-definíciók](#sample-linked-service-and-dataset-definitions) . |Yes |
| userid |Itt adhatja meg annak a felhasználónak az AZONOSÍTÓját, aki hozzáfér a kiszolgálóhoz. |Nem (ha a encryptedCredential választja) |
| jelszó |A felhasználó jelszavának megadása (userid). |Nem (ha a encryptedCredential választja |
| encryptedCredential |A New-AzDataFactoryEncryptValue parancsmag futtatásával adhatja meg a titkosított hitelesítő adatokat. |Nem (ha úgy dönt, hogy egyszerű szövegként adja meg a felhasználói azonosítót és a jelszót) |
| Átjáró neve |Annak az átjárónak a nevét adja meg, amelyet a Data Factory kell használnia a helyszíni fájlkiszolgálón való kapcsolódáshoz. |Yes |


### <a name="sample-linked-service-and-dataset-definitions"></a>Példa társított szolgáltatás és adatkészlet-definíciók
| Eset | Gazdagép a társított szolgáltatás definíciójában | folderPath az adatkészlet definíciójában |
| --- | --- | --- |
| Helyi mappa adatkezelés átjáró számítógépén: <br/><br/>Példák: D: \\ \* vagy D:\folder\subfolder\\\* |D: \\ \\ (adatkezelés átjáró 2,0-es és újabb verziói esetén) <br/><br/> localhost (a adatkezelés Gateway 2,0-es verziójánál korábbi verziók esetében) |.\\\\ vagy mappa \\ \\ almappája (adatkezelés átjáró 2,0-es és újabb verzióihoz) <br/><br/>D: \\ \\ vagy d: \\ \\ mappa \\ \\ almappája (az átjáró 2,0-es verziójához) |
| Távoli megosztott mappa: <br/><br/>Példák: \\ \\ MyServer \\ Share \\ \* vagy \\ \\ MyServer \\ Share \\ mappa \\ almappája\\\* |\\\\\\\\MyServer- \\ \\ megosztás |.\\\\ vagy mappa \\ \\ almappája |

>[!NOTE]
>Felhasználói felületen keresztüli szerzői műveletek esetén nem szükséges dupla fordított perjelet () megadnia, `\\` mint a JSON-n keresztül, adjon meg egy fordított perjelet.

### <a name="example-using-username-and-password-in-plain-text"></a>Példa: Felhasználónév és jelszó használata egyszerű szövegben

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

### <a name="example-using-encryptedcredential"></a>Példa: a encryptedcredential használata

```JSON
{
  "Name": " OnPremisesFileServerLinkedService ",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "D:\\",
      "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
      "gatewayName": "mygateway"
    }
  }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Az adatkészletek definiálásához elérhető csoportok és tulajdonságok teljes listáját lásd: [adatkészletek létrehozása](data-factory-create-datasets.md). Az adatkészletek JSON-típusai, például a struktúra, a rendelkezésre állás és a szabályzat, az összes adatkészlet esetében hasonlóak.

A typeProperties szakasz eltérő az egyes adatkészletek esetében. Olyan információkat biztosít, mint például az adattárban lévő adatok helye és formátuma. A **fájlmegosztás** típusú adatkészlet typeProperties szakasza a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| folderPath |Megadja a mappa alelérési útját. A \' karakterláncban a speciális karakterekhez használja a Escape karaktert. A helyettesítő karakteres szűrő nem támogatott. Példákat a következő témakörben talál: példa [társított szolgáltatás és adatkészlet-definíciók](#sample-linked-service-and-dataset-definitions) .<br/><br/>Ezt a tulajdonságot kombinálhatja a **partitionBy** , hogy a mappa elérési útjai a szelet kezdő/befejező dátum-és időpontjain alapulnak. |Yes |
| fileName |Adja meg a fájl nevét a **folderPath** , ha azt szeretné, hogy a tábla egy adott fájlra hivatkozzon a mappában. Ha nem ad meg értéket ehhez a tulajdonsághoz, a tábla a mappában található összes fájlra mutat.<br/><br/>Ha a **fájlnév** nincs megadva egy kimeneti adatkészlethez, és a **preserveHierarchy** nincs megadva a tevékenység-fogadóban, a generált fájl neve a következő formátumban jelenik meg: <br/><br/>`Data.<Guid>.txt` (Példa: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) |No |
| fileFilter |Adja meg azt a szűrőt, amelyet a folderPath található fájlok részhalmazának kiválasztására kíván használni az összes fájl helyett. <br/><br/>Az engedélyezett értékek a következők: `*` (több karakter) és `?` (egyetlen karakter).<br/><br/>1. példa: "fileFilter": "*. log"<br/>2. példa: "fileFilter": 2014-1-?. txt<br/><br/>Vegye figyelembe, hogy a fileFilter egy bemeneti fájlmegosztás adatkészlet esetében alkalmazható. |No |
| partitionedBy |A partitionedBy segítségével megadhatja az idősorozat-adatsorok dinamikus folderPath/fájlnevét. Egy példa a folderPath paramétert minden órában. |No |
| formátumban | A következő típusú formátumok támogatottak: **Szövegformátum**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**. A **Type (típus** ) tulajdonságot állítsa a Format értékre a következő értékek egyikére. További információkért lásd: [Szövegformátum](data-factory-supported-file-and-compression-formats.md#text-format), JSON- [Formátum](data-factory-supported-file-and-compression-formats.md#json-format), [Avro formátum](data-factory-supported-file-and-compression-formats.md#avro-format), [ork-formátum](data-factory-supported-file-and-compression-formats.md#orc-format)és a [parketta formátuma](data-factory-supported-file-and-compression-formats.md#parquet-format) című rész. <br><br> Ha **fájlokat szeretne másolni** a fájl alapú tárolók között (bináris másolás), ugorja át a formátum szakaszt mind a bemeneti, mind a kimeneti adatkészlet-definíciókban. |No |
| tömörítés | Adja meg az adattömörítés típusát és szintjét. A támogatott típusok a következők: **gzip**, **deflate**, **BZip2** és **ZipDeflate**. A támogatott szintek a következők: **optimális** és **leggyorsabb**. lásd [a Azure Data Factory fájl-és tömörítési formátumait](data-factory-supported-file-and-compression-formats.md#compression-support). |No |

> [!NOTE]
> A fájlnév és a fileFilter egyidejű használata nem lehetséges.

### <a name="using-partitionedby-property"></a>A partitionedBy tulajdonság használata
Ahogy azt az előző szakaszban is említettük, megadhat egy dinamikus folderPath és fájlnevet az idősorozat-adatokhoz a **partitionedBy** tulajdonsággal, [Data Factory függvénnyel és a rendszerváltozókkal](data-factory-functions-variables.md).

Az idősorozat-adatkészletek, az ütemezés és a szeletek részleteinek megismeréséhez lásd: [adatkészletek létrehozása](data-factory-create-datasets.md), [Ütemezés és végrehajtás](data-factory-scheduling-and-execution.md)és [folyamatok létrehozása](data-factory-create-pipelines.md).

#### <a name="sample-1"></a>1. példa:

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

Ebben a példában a {slice} helyére a Data Factory rendszerváltozó értéke SliceStart (YYYYMMDDHH) formátumban van lecserélve. A SliceStart a szelet kezdő időpontját jelöli. A folderPath különbözik az egyes szeletekhez. Például: tulajdonság beállítása wikidatagateway/wikisampledataout/2014100103 vagy tulajdonság beállítása wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>2. minta:

```JSON
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

Ebben a példában a SliceStart év, hónap, nap és időpont a folderPath és a fájlnév tulajdonságai által használt különálló változókba kerül.

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A tevékenységek definiálásához elérhető & tulajdonságok teljes listáját a [folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben találja. A tulajdonságok, például a név, a leírás, a bemeneti és a kimeneti adatkészletek, valamint a házirendek minden típusú tevékenységhez elérhetők. Míg a tevékenység **typeProperties** szakaszában elérhető tulajdonságok az egyes tevékenységtípusok esetében eltérőek.

Másolási tevékenység esetén a források és a nyelők típusaitól függően változnak. Ha helyszíni fájlrendszerből helyez át adatátvitelt, a másolási tevékenységben adja meg a forrás típusát a **FileSystemSource** értékre. Hasonlóképpen, ha egy helyszíni fájlrendszerbe helyezi át az adatátvitelt, a másolási tevékenységben állítsa be a fogadó típusát a **FileSystemSink** értékre. Ez a szakasz a FileSystemSource és a FileSystemSink által támogatott tulajdonságok listáját tartalmazza.

A **FileSystemSource** a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| rekurzív |Azt jelzi, hogy az adatok rekurzív módon olvashatók-e az almappákból, vagy csak a megadott mappából. |Igaz, hamis (alapértelmezett) |No |

A **FileSystemSink** a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| copyBehavior |Meghatározza a másolási viselkedést a forrás BlobSource vagy fájlrendszerének használatakor. |**PreserveHierarchy:** Megőrzi a fájl hierarchiáját a célmappában. Ez a forrásfájl és a forrásfájl relatív elérési útja megegyezik a célfájl relatív elérési útjával.<br/><br/>**FlattenHierarchy:** A rendszer a forrás mappából származó összes fájlt a célmappa első szintjén hozza létre. A célfájl automatikusan létrehozott névvel lett létrehozva.<br/><br/>**MergeFiles:** Egyesít minden fájlt a forrás mappájából egy fájlba. Ha meg van adva a fájl neve/blob neve, az egyesített fájl neve a megadott név. Ellenkező esetben az automatikusan létrehozott fájlnév. |No |

### <a name="recursive-and-copybehavior-examples"></a>rekurzív és copyBehavior példák
Ez a szakasz ismerteti a másolási művelet eredményét a rekurzív és a copyBehavior tulajdonságainak különböző értékeihez.

| rekurzív érték | copyBehavior érték | Eredményül kapott viselkedés |
| --- | --- | --- |
| true |preserveHierarchy |A következő struktúrával rendelkező Mappa1:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a célmappa Mappa1 ugyanazzal a struktúrával jön létre, mint a forrás:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy |A következő struktúrával rendelkező Mappa1:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a célként megadott Mappa1 a következő szerkezettel jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;a file1 automatikusan generált neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;a Fájl2 automatikusan generált neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;a fájl3 automatikusan generált neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;a File4 automatikusan generált neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;a File5 automatikusan generált neve |
| true |mergeFiles |A következő struktúrával rendelkező Mappa1:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a célként megadott Mappa1 a következő szerkezettel jön létre: <br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;A file1 + Fájl2 + fájl3 + File4 + file 5 tartalom egyetlen fájlba van egyesítve, amely egy automatikusan létrehozott fájlnevet tartalmaz. |
| hamis |preserveHierarchy |A következő struktúrával rendelkező Mappa1:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a célmappa Mappa1 az alábbi struktúrával jön létre:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/><br/>A fájl3, a File4 és a File5 Subfolder1 nem kerül felvételre. |
| hamis |flattenHierarchy |A következő struktúrával rendelkező Mappa1:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a célmappa Mappa1 az alábbi struktúrával jön létre:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;a file1 automatikusan generált neve<br/>&nbsp;&nbsp;&nbsp;&nbsp;a Fájl2 automatikusan generált neve<br/><br/>A fájl3, a File4 és a File5 Subfolder1 nem kerül felvételre. |
| hamis |mergeFiles |A következő struktúrával rendelkező Mappa1:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Fájl2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Fájl3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>a célmappa Mappa1 az alábbi struktúrával jön létre:<br/><br/>Mappa1<br/>&nbsp;&nbsp;&nbsp;&nbsp;A file1 + Fájl2 tartalma egyetlen fájlba van egyesítve egy automatikusan létrehozott fájlnévvel.<br/>&nbsp;&nbsp;&nbsp;&nbsp;A file1 automatikusan generált neve<br/><br/>A fájl3, a File4 és a File5 Subfolder1 nem kerül felvételre. |

## <a name="supported-file-and-compression-formats"></a>Támogatott fájl- és tömörítési formátumok
A részletekért tekintse meg a [fájl-és tömörítési formátumokat Azure Data Factory](data-factory-supported-file-and-compression-formats.md) cikkben.

## <a name="json-examples-for-copying-data-to-and-from-file-system"></a>JSON-példák az adatok fájlrendszerbe történő másolásához
Az alábbi példák olyan JSON-definíciókat biztosítanak, amelyek segítségével a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy a [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával hozhat létre folyamatokat. Bemutatják, hogyan másolhatók az adatok egy helyszíni fájlrendszerből és az Azure Blob Storage-ba. Az adatok másolása azonban *közvetlenül* az egyik forrásból a [támogatott forrásokban](data-factory-data-movement-activities.md#supported-data-stores-and-formats) felsorolt mosogatók egyikére, a másolási tevékenység használatával pedig a Azure Data Factoryban is elsüllyed.

### <a name="example-copy-data-from-an-on-premises-file-system-to-azure-blob-storage"></a>Példa: adatok másolása helyszíni fájlrendszerből az Azure Blob Storage-ba
Ez a minta bemutatja, hogyan másolhat adatok egy helyszíni fájlrendszerből az Azure Blob Storage szolgáltatásba. A mintában a következő Data Factory entitások vannak:

* [OnPremisesFileServer](#linked-service-properties)típusú társított szolgáltatás.
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú társított szolgáltatás.
* [Fájlmegosztás](#dataset-properties)típusú bemeneti [adatkészlet](data-factory-create-datasets.md) .
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú kimeneti [adatkészlet](data-factory-create-datasets.md) .
* [FileSystemSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)használó másolási tevékenységgel rendelkező [folyamat](data-factory-create-pipelines.md) .

Az alábbi minta egy helyszíni fájlrendszerből származó idősorozat-adatok óránkénti Azure Blob Storage-ba történő másolását végzi. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszokban ismertetjük.

Első lépésként állítsa be adatkezelés átjárót az [adatáthelyezés a helyszíni források és a felhő között adatkezelés átjáróval](data-factory-move-data-between-onprem-and-cloud.md)című részben leírtak szerint.

**Helyszíni fájlkiszolgáló társított szolgáltatása:**

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

Azt javasoljuk, hogy használja a **encryptedCredential** tulajdonságot a **userid** és a **Password** tulajdonság helyett. A társított szolgáltatás részletes ismertetését lásd: [fájlkiszolgáló társított szolgáltatása](#linked-service-properties) .

**Azure Storage-beli társított szolgáltatás:**

```JSON
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

**Helyszíni fájlrendszer bemeneti adatkészlete:**

Az adatok minden órában egy új fájlból kerülnek felvételre. A folderPath és a fájlnév tulajdonságainak meghatározása a szelet kezdési időpontja alapján történik.

A beállítás `"external": "true"` azt Data Factory, hogy az adatkészlet kívül esik az adat-előállítón, és nem az adat-előállító tevékenysége.

```JSON
{
  "name": "OnpremisesFileSystemInput",
  "properties": {
    "type": " FileShare",
    "linkedServiceName": " OnPremisesFileServerLinkedService ",
    "typeProperties": {
      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ]
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

**Az Azure Blob Storage kimeneti adatkészlete:**

A rendszer óránként egy új blobba írja az adatbevitelt (frekvencia: óra, intervallum: 1). A blob mappájának elérési útját a rendszer dinamikusan kiértékeli a feldolgozás alatt álló szelet kezdési időpontja alapján. A mappa elérési útja a kezdési időpont év, hónap, nap és óra részét használja.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
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

**Másolási tevékenység egy folyamatban a fájlrendszer forrásával és a blob-fogadóval:**

A folyamat egy másolási tevékenységet tartalmaz, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra van ütemezve. A folyamat JSON-definíciójában a **forrás** típusa **FileSystemSource** értékre van **állítva, a fogadó típusa pedig** **BlobSink**.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2015-06-01T18:00:00",
    "end":"2015-06-01T19:00:00",
    "description":"Pipeline for copy activity",
    "activities":[
      {
        "name": "OnpremisesFileSystemtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "OnpremisesFileSystemInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "FileSystemSource"
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

### <a name="example-copy-data-from-azure-sql-database-to-an-on-premises-file-system"></a>Példa: adatok másolása Azure SQL Databaseból egy helyszíni fájlrendszerbe
A következő minta a következőket mutatja be:

* AzureSqlDatabase típusú társított szolgáltatás [.](data-factory-azure-sql-connector.md#linked-service-properties)
* [OnPremisesFileServer](#linked-service-properties)típusú társított szolgáltatás.
* [Tulajdonsága azuresqltable](data-factory-azure-sql-connector.md#dataset-properties)típusú bemeneti adatkészlet.
* [Fájlmegosztás](#dataset-properties)típusú kimeneti adatkészlet.
* [SqlSource](data-factory-azure-sql-connector.md#copy-activity-properties) és [FileSystemSink](#copy-activity-properties)használó másolási tevékenységgel rendelkező folyamat.

A minta óránként egy Azure SQL-táblából másol idősorozat-adatok egy helyszíni fájlrendszerbe. Az ezekben a mintákban használt JSON-tulajdonságokat a minták után szakasz ismerteti.

**Azure SQL Database társított szolgáltatás:**

```JSON
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

**Helyszíni fájlkiszolgáló társított szolgáltatása:**

```JSON
{
  "Name": "OnPremisesFileServerLinkedService",
  "properties": {
    "type": "OnPremisesFileServer",
    "typeProperties": {
      "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
      "userid": "Admin",
      "password": "123456",
      "gatewayName": "mygateway"
    }
  }
}
```

Azt javasoljuk, hogy használja a **encryptedCredential** tulajdonságot a **userid** és a **Password** tulajdonság használata helyett. A társított szolgáltatás részleteiért lásd: [fájlrendszerhez társított szolgáltatás](#linked-service-properties) .

**Azure SQL bemeneti adatkészlet:**

A minta azt feltételezi, hogy létrehozott egy "Sajáttábla" táblát az Azure SQL-ben, és egy "timestampcolumn" nevű oszlopot tartalmaz az idősoros adatsorokhoz.

A beállítás ``"external": "true"`` azt Data Factory, hogy az adatkészlet kívül esik az adat-előállítón, és nem az adat-előállító tevékenysége.

```JSON
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

**Helyszíni fájlrendszer kimeneti adatkészlete:**

Az rendszer óránként másolja az adatfájlokat egy új fájlba. A blob folderPath és fájlnevének meghatározása a szelet kezdési időpontja alapján történik.

```JSON
{
  "name": "OnpremisesFileSystemOutput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": " OnPremisesFileServerLinkedService ",
    "typeProperties": {
      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ]
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

**Másolási tevékenység egy folyamaton az SQL-forrással és a fájlrendszer-fogadóval:**

A folyamat egy másolási tevékenységet tartalmaz, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra van ütemezve. A folyamat JSON-definíciójában a **forrás** típusa **SqlSource** értékre van állítva, a **fogadó típusa pedig** **FileSystemSink**. A **SqlReaderQuery** tulajdonsághoz megadott SQL-lekérdezés a másoláshoz az elmúlt órában kiválasztott adatértéket jelöli.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2015-06-01T18:00:00",
    "end":"2015-06-01T20:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "AzureSQLtoOnPremisesFile",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSQLInput"
          }
        ],
        "outputs": [
          {
            "name": "OnpremisesFileSystemOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "FileSystemSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 3,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```

A másolási tevékenység definíciójában a forrás adatkészletből származó oszlopokat is leképezheti a fogadó adatkészletből származó oszlopokra. Részletekért lásd: [adatkészlet-oszlopok leképezése Azure Data Factoryban](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás
 Az adatáthelyezés (másolási tevékenység) teljesítményét befolyásoló főbb tényezőkről Azure Data Factory és az optimalizálás különböző módjairól a [másolási tevékenységek teljesítményének és hangolásának útmutatója](data-factory-copy-activity-performance.md)tartalmaz további információt.
