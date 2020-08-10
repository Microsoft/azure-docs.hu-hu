---
title: JSON formátum Azure Data Factory
description: Ez a témakör azt ismerteti, hogyan kezelhető a JSON formátuma Azure Data Factoryban.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/10/2020
ms.author: jingwang
ms.openlocfilehash: 2fc2ccdc7a0520bd156bde8c1da36e19a9e2af1e
ms.sourcegitcommit: 1a0dfa54116aa036af86bd95dcf322307cfb3f83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2020
ms.locfileid: "88042190"
---
# <a name="json-format-in-azure-data-factory"></a>JSON formátum Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Kövesse ezt a cikket, ha **elemezni szeretné a JSON-fájlokat, vagy írja be az adatbevitelt JSON formátumba**. 

A JSON formátum a következő összekötők esetén támogatott: [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure file Storage](connector-azure-file-storage.md), [fájlrendszer](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)és [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz a JSON-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

| Tulajdonság         | Leírás                                                  | Kötelező |
| ---------------- | ------------------------------------------------------------ | -------- |
| típus             | Az adatkészlet Type tulajdonságát **JSON**-ra kell beállítani. | Igen      |
| location         | A fájl (ok) helyének beállításai. Minden fájl alapú összekötőhöz tartozik a saját hely típusa és a támogatott tulajdonságai `location` . **Tekintse meg a részleteket az összekötő cikk-> adatkészlet tulajdonságai szakaszban**. | Igen      |
| encodingName     | A tesztelési fájlok olvasására/írására szolgáló kódolási típus. <br>Az engedélyezett értékek a következők: "UTF-8", "UTF-16", "UTF-16BE", "UTF-32", "UTF-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "JOHAB", "SHIFT-JIS", "CP875", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", "IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149", "", "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13" "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255", "WINDOWS-1256", "WINDOWS-1257", "WINDOWS-1258".| Nem       |
| tömörítés | A fájltömörítés konfigurálására szolgáló tulajdonságok csoportja. Akkor konfigurálja ezt a szakaszt, ha a tevékenység végrehajtása során tömörítést vagy kibontást szeretne végezni. | Nem |
| típus<br/>(*alatt `compression` *) | A JSON-fájlok olvasásához/írásához használt tömörítési kodek. <br>Az engedélyezett értékek a **bzip2**, a **gzip**, a **deflate**, a **ZipDeflate**, a **Snappy**vagy a **lz4**. a fájl mentésekor használatos. Az alapértelmezett érték nincs tömörítve.<br>**Megjegyzés:** a másolási tevékenység nem támogatja a "snappy" & "lz4", és a leképezési adatfolyam nem támogatja a "ZipDeflate".<br>**Megjegyzés** : Ha a másolási tevékenységgel kibontja a **ZipDeflate** -fájl (oka) t, és a fájl alapú fogadó adattárolóba ír fájlokat, az alapértelmezett fájlok a mappába kerülnek, a `<path specified in dataset>/<folder named as source zip file>/` `preserveZipFileNameAsFolder` [másolási tevékenység forrásaként](#json-as-source) pedig megadhatja, hogy a rendszer a zip-fájl nevét a mappa szerkezeteként őrizze meg. | Nem.  |
| szint<br/>(*alatt `compression` *) | A tömörítési arány. <br>Az engedélyezett értékek az **optimálisak** vagy a **leggyorsabbek**.<br>- **Leggyorsabb:** A tömörítési műveletnek a lehető leggyorsabban kell elvégeznie, még akkor is, ha az eredményül kapott fájl nem tömöríthető optimálisan.<br>- **Optimális**: a tömörítési műveletet optimálisan kell tömöríteni, még akkor is, ha a művelet végrehajtása hosszú időt vesz igénybe. További információ: [tömörítési szint](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) témakör. | Nem       |

Alább látható egy példa a JSON-adatkészletre az Azure Blob Storageban:

```json
{
    "name": "JSONDataset",
    "properties": {
        "type": "Json",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "gzip"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a JSON-forrás és a fogadó által támogatott tulajdonságok listáját tartalmazza.

Ismerje meg, hogyan nyerheti ki az adatok JSON-fájlokból való kinyerését, és hogyan képezheti le az adattárakat vagy-formátumokat, illetve fordítva a [séma](copy-activity-schema-and-type-mapping.md)

### <a name="json-as-source"></a>JSON forrásként

A másolási tevékenység *** \* forrása \* *** szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság      | Leírás                                                  | Kötelező |
| ------------- | ------------------------------------------------------------ | -------- |
| típus          | A másolási tevékenység forrásának Type tulajdonságát **JSONSource**értékre kell állítani. | Igen      |
| formatSettings | Tulajdonságok csoportja. Tekintse meg az alábbi **JSON-olvasási beállítások** táblázatot. | Nem       |
| storeSettings | Az adattárakból származó adatok beolvasására szolgáló tulajdonságok csoportja. A fájl alapú összekötők a saját támogatott olvasási beállításaival rendelkeznek `storeSettings` . **Tekintse meg a részleteket az összekötőről szóló cikk – > másolási tevékenység tulajdonságai szakaszban**. | Nem       |

Támogatott **JSON-olvasási beállítások** a következő alatt `formatSettings` :

| Tulajdonság      | Leírás                                                  | Kötelező |
| ------------- | ------------------------------------------------------------ | -------- |
| típus          | A formatSettings típusát **JsonReadSettings**értékre kell beállítani. | Igen      |
| compressionProperties | Egy adott tömörítési kodekhez tartozó adatok kibontására szolgáló tulajdonságok csoportja. | Nem       |
| preserveZipFileNameAsFolder<br>(*alatt `compressionProperties` *) | Akkor érvényes, ha a bemeneti adatkészlet **ZipDeflate** tömörítéssel van konfigurálva. Azt jelzi, hogy a forrás zip-fájlnevet a másolás során a mappa szerkezeteként kell-e megőrizni.<br>– Ha **true (alapértelmezett)** értékre van állítva, Data Factory a kibontott fájlokat ír a következőre: `<path specified in dataset>/<folder named as source zip file>/` .<br>– Ha **hamis**értékre van állítva, Data Factory a kibontott fájlokat közvetlenül a következőre írja: `<path specified in dataset>` . A versenyzés vagy a váratlan viselkedés elkerülése érdekében ügyeljen arra, hogy ne legyenek duplikált fájlnevek különböző zip-fájlokban.  | Nem |

### <a name="json-as-sink"></a>JSON mint fogadó

A másolási *** \* \* tevékenység*** fogadója szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság      | Leírás                                                  | Kötelező |
| ------------- | ------------------------------------------------------------ | -------- |
| típus          | A másolási tevékenység forrásának Type tulajdonságát **JSONSink**értékre kell állítani. | Igen      |
| formatSettings | Tulajdonságok csoportja. Tekintse meg az alábbi **JSON írási beállítások** táblázatát. | Nem       |
| storeSettings | Az adattárakba való adatíráshoz szükséges tulajdonságok csoportja. A fájl alapú összekötők a saját támogatott írási beállításaival rendelkeznek `storeSettings` . **Tekintse meg a részleteket az összekötőről szóló cikk – > másolási tevékenység tulajdonságai szakaszban**. | Nem       |

Támogatott **JSON írási beállítások** a alatt `formatSettings` :

| Tulajdonság      | Leírás                                                  | Kötelező                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| típus          | A formatSettings típusát **JsonWriteSettings**értékre kell beállítani. | Igen                                                   |
| filePattern |Az egyes JSON-fájlokban tárolt adatok mintáját jelzi. Az engedélyezett értékek: **setOfObjects** (JSON-sorok) és **arrayOfObjects**. Az **alapértelmezett** érték a **setOfObjects**. A mintákkal kapcsolatban lásd a [JSON-fájlminták](#json-file-patterns) című szakaszt. |Nem |

### <a name="json-file-patterns"></a>JSON-fájlminták

Ha JSON-fájlokból másol adatokból, a másolási tevékenység automatikusan képes azonosítani és elemezni a JSON-fájlok következő mintáit. A JSON-fájlokba való íráskor beállíthatja a fájl mintáját a másolási tevékenység fogadóján.

- **I. típus: setOfObjects**

    Minden fájl egyetlen objektumot, JSON-sort vagy összefűzett objektumot tartalmaz.

    * **példa egy objektumot tartalmazó JSON-fájlra**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        ```

    * **JSON-sorok (a fogadó alapértelmezett értéke)**

        ```json
        {"time":"2015-04-29T07:12:20.9100000Z","callingimsi":"466920403025604","callingnum1":"678948008","callingnum2":"567834760","switch1":"China","switch2":"Germany"}
        {"time":"2015-04-29T07:13:21.0220000Z","callingimsi":"466922202613463","callingnum1":"123436380","callingnum2":"789037573","switch1":"US","switch2":"UK"}
        {"time":"2015-04-29T07:13:21.4370000Z","callingimsi":"466923101048691","callingnum1":"678901578","callingnum2":"345626404","switch1":"Germany","switch2":"UK"}
        ```

    * **példa összefűzött JSON-fájlra**

        ```json
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        }
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        }
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
        ```

- **II. típus: arrayOfObjects**

    Minden fájl objektumok egy tömbjét tartalmazza.

    ```json
    [
        {
            "time": "2015-04-29T07:12:20.9100000Z",
            "callingimsi": "466920403025604",
            "callingnum1": "678948008",
            "callingnum2": "567834760",
            "switch1": "China",
            "switch2": "Germany"
        },
        {
            "time": "2015-04-29T07:13:21.0220000Z",
            "callingimsi": "466922202613463",
            "callingnum1": "123436380",
            "callingnum2": "789037573",
            "switch1": "US",
            "switch2": "UK"
        },
        {
            "time": "2015-04-29T07:13:21.4370000Z",
            "callingimsi": "466923101048691",
            "callingnum1": "678901578",
            "callingnum2": "345626404",
            "switch1": "Germany",
            "switch2": "UK"
        }
    ]
    ```

## <a name="mapping-data-flow-properties"></a>Adatfolyam-tulajdonságok leképezése

Az adatfolyamatok leképezése során a következő adattárakban olvasható és írható JSON-formátum: [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)és [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties).

### <a name="source-properties"></a>Forrás tulajdonságai

Az alábbi táblázat felsorolja a JSON-forrás által támogatott tulajdonságokat. Ezeket a tulajdonságokat a **forrás beállításai** lapon módosíthatja.

| Név | Leírás | Kötelező | Megengedett értékek | Adatfolyam-parancsfájl tulajdonsága |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Wild kártya elérési útjai | A rendszer feldolgozza a helyettesítő karakteres elérési úttal egyező összes fájlt. Felülbírálja az adatkészletben beállított mappát és a fájl elérési útját. | nem | Karakterlánc [] | wildcardPaths |
| Partíció gyökerének elérési útja | A particionált fájlok esetében megadhatja a partíció gyökerének elérési útját, hogy a particionált mappák oszlopként legyenek olvashatók. | nem | Sztring | partitionRootPath |
| Fájlok listája | Azt jelzi, hogy a forrás egy szövegfájlra mutat-e, amely a feldolgozandó fájlokat listázza | nem | `true` vagy `false` | fileList |
| A fájl nevét tároló oszlop | Új oszlop létrehozása a forrásfájl nevével és elérési útjával | nem | Sztring | rowUrlColumn |
| Befejezés után | A fájlok törlése vagy áthelyezése a feldolgozás után. A fájl elérési útja a tároló gyökeréből indul el | nem | Törlés: `true` vagy`false` <br> Áthelyezése`['<from>', '<to>']` | purgeFiles <br> moveFiles |
| Szűrés utoljára módosítva | Válassza ki a fájlok szűrését az utolsó módosításuk alapján | nem | Timestamp | modifiedAfter <br> modifiedBefore |
| Egyetlen dokumentum | Az adatfolyamatok leképezése egyetlen JSON-dokumentumot olvas be minden fájlból | nem | `true` vagy `false` | singleDocument |
| Nem jegyzett oszlopnevek | Ha nem **jegyzett oszlopnevek** vannak kiválasztva, az adatfolyamatok leképezése beolvassa azokat a JSON-oszlopokat, amelyeket nem idézőjelek öveznek. | nem | `true` vagy `false` |  unquotedColumnNames |
| Megjegyzésekkel rendelkezik | Válassza a **Megjegyzések** lehetőséget, ha a JSON-adatként C vagy C++ stílusú megjegyzések szerepelnek | nem | `true` vagy `false` | asComments |
| Egyszer idézett | Az idézőjelek között nem szereplő JSON-oszlopok beolvasása | nem | `true` vagy `false` | singleQuoted |
| Megmenekült fordított perjel | Válassza ki a **fordított perjelet** , ha fordított perjeleket használ a JSON-adatbevitelhez | nem | `true` vagy `false` | backslashEscape |

### <a name="source-format-options"></a>Forrás formátum beállításai

A JSON-adatkészletek adatáramlási forrásaként való használata lehetővé teszi öt további beállítás megadását. Ezek a beállítások a **forrás beállításai** lap **JSON-beállítások** című részében találhatók.  

![JSON-beállítások](media/data-flow/json-settings.png "JSON-beállítások")

#### <a name="default"></a>Alapértelmezett

Alapértelmezés szerint a JSON-adatolvasás a következő formátumban történik.

```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

#### <a name="single-document"></a>Egyetlen dokumentum

Ha **egyetlen dokumentum** van kiválasztva, az adatforgalom leképezése minden fájlból beolvas egy JSON-dokumentumot. 

``` json
File1.json
{
    "json": "record 1"
}
File2.json
{
    "json": "record 2"
}
File3.json
{
    "json": "record 3"
}
```
> [!NOTE]
> Ha az adatforgalom olyan hibát jelez, amely "corrupt_record" üzenetet jelenít meg a JSON-adatai megtekintésekor, akkor valószínű, hogy az adatai egyetlen dokumentumot tartalmaznak a JSON-fájlban. A "single Document" beállításnak törölnie kell ezt a hibát.

#### <a name="unquoted-column-names"></a>Nem jegyzett oszlopnevek

Ha nem **jegyzett oszlopnevek** vannak kiválasztva, az adatfolyamatok leképezése beolvassa azokat a JSON-oszlopokat, amelyeket nem idézőjelek öveznek. 

```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

#### <a name="has-comments"></a>Megjegyzésekkel rendelkezik

Válassza a **Megjegyzések lehetőséget,** ha a JSON-adatként C vagy C++ stílusú megjegyzések szerepelnek.

``` json
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

#### <a name="single-quoted"></a>Egyszer idézett

Válassza az **egyszeres** idézőjel lehetőséget, ha a JSON-mezők és-értékek idézőjelek helyett szimpla idézőjeleket használnak.

```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

#### <a name="backslash-escaped"></a>Megmenekült fordított perjel

Válassza ki a **fordított perjelet** , ha a rendszer fordított perjeleket használ a JSON-Adatkarakterek kiírásához.

```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

### <a name="sink-properties"></a>Fogadó tulajdonságai

Az alábbi táblázatban a JSON-fogadó által támogatott tulajdonságok szerepelnek. Ezeket a tulajdonságokat a **Beállítások** lapon módosíthatja.

| Név | Leírás | Kötelező | Megengedett értékek | Adatfolyam-parancsfájl tulajdonsága |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Mappa törlése | Ha a célmappa az írás előtt törlődik | nem | `true` vagy `false` | truncate |
| Fájlnév beállítás | Az írt adatnév formátuma. Alapértelmezés szerint egy fájl/partíció formátumban`part-#####-tid-<guid>` | nem | Minta: karakterlánc <br> /Partíció: karakterlánc [] <br> Oszlopbeli adatként: karakterlánc <br> Kimenet egyetlen fájlba:`['<fileName>']`  | filePattern <br> partitionFileNames <br> rowUrlColumn <br> partitionFileNames |

### <a name="creating-json-structures-in-a-derived-column"></a>JSON-struktúrák létrehozása származtatott oszlopban

A származtatott oszlop Expression Builder használatával hozzáadhat egy összetett oszlopot az adatfolyamathoz. A származtatott oszlop transzformációjában adjon hozzá egy új oszlopot, és nyissa meg a Kifejezésszerkesztőt a kék mezőre kattintva. Az oszlopok összetett létrehozásához manuálisan megadhatja a JSON-struktúrát, vagy a UX használatával interaktív módon adhat hozzá aloszlopokat.

#### <a name="using-the-expression-builder-ux"></a>A Expression Builder UX használata

A kimeneti séma oldali ablaktáblán vigye az egérmutatót egy oszlop fölé, és kattintson a plusz ikonra. Válassza az **aloszlop hozzáadása** lehetőséget az oszlop összetett típusának elvégzéséhez.

![Aloszlop hozzáadása](media/data-flow/addsubcolumn.png "Aloszlop hozzáadása")

Ugyanilyen módon adhat hozzá további oszlopokat és aloszlopokat. Minden nem összetett mezőhöz egy kifejezés adható hozzá a jobb oldali kifejezés-szerkesztőben.

![Összetett oszlop](media/data-flow/complexcolumn.png "Összetett oszlop")

#### <a name="entering-the-json-structure-manually"></a>A JSON-struktúra manuális megadása

JSON-struktúra manuális hozzáadásához vegyen fel egy új oszlopot, és adja meg a kifejezést a szerkesztőben. A kifejezés a következő általános formátumot követi:

```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```

Ha ez a kifejezés egy "complexColumn" nevű oszlophoz lett megadva, akkor a rendszer a fogadóba írja a következő JSON-t:

```
{
    "complexColumn": {
        "field1": 0,
        "field2": {
            "field1": 0
        }
    }
}
```

#### <a name="sample-manual-script-for-complete-hierarchical-definition"></a>Minta manuális parancsfájl a teljes hierarchikus definícióhoz
```
@(
    title=Title,
    firstName=FirstName,
    middleName=MiddleName,
    lastName=LastName,
    suffix=Suffix,
    contactDetails=@(
        email=EmailAddress,
        phone=Phone
    ),
    address=@(
        line1=AddressLine1,
        line2=AddressLine2,
        city=City,
        state=StateProvince,
        country=CountryRegion,
        postCode=PostalCode
    ),
    ids=[
        toString(CustomerID), toString(AddressID), rowguid
    ]
)
```

## <a name="next-steps"></a>További lépések

- [Másolási tevékenység – áttekintés](copy-activity-overview.md)
- [Adatfolyam hozzárendelése](concepts-data-flow-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)
- [GetMetadata tevékenység](control-flow-get-metadata-activity.md)
