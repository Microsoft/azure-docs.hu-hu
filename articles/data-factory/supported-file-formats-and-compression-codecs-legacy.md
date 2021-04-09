---
title: Támogatott fájlformátumok a Azure Data Factoryban (örökölt)
description: Ez a témakör azokat a fájlformátumokat és tömörítési kódokat ismerteti, amelyeket a Azure Data Factory található fájl alapú összekötők támogatnak.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/10/2019
ms.openlocfilehash: d95927a9ea7d3084387a9aedb0dcdd86f84b8e7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100384826"
---
# <a name="supported-file-formats-and-compression-codecs-in-azure-data-factory-legacy"></a>Támogatott fájlformátumok és tömörítési kodekek a Azure Data Factory (örökölt)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

*Ez a cikk az alábbi összekötőket érinti [: Amazon S3](connector-amazon-simple-storage-service.md), [azure blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure file Storage](connector-azure-file-storage.md), [File System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)és [SFTP](connector-sftp.md).*

>[!IMPORTANT]
>Data Factory új Format-alapú adatkészlet-modellt vezetett be, lásd a megfelelő formátumú cikket a részletekkel: <br>- [Avro formátuma](format-avro.md)<br>- [Bináris formátum](format-binary.md)<br>- [Tagolt szöveg formátuma](format-delimited-text.md)<br>- [JSON-formátum](format-json.md)<br>- [ORK formátum](format-orc.md)<br>- [Parketta formátuma](format-parquet.md)<br>A cikkben említett Rest-konfigurációk továbbra is támogatottak a visszamenőleges compabitility. Azt javasoljuk, hogy használja az új modellt a jövőre. 

## <a name="text-format-legacy"></a><a name="text-format"></a> Szöveg formátuma (örökölt)

>[!NOTE]
>Ismerje meg az új modellt a [tagolt szöveges formátumú](format-delimited-text.md) cikkből. A fájl alapú adattár-adatkészletek következő konfigurációi továbbra is támogatottak, ha visszafelé compabitility. Azt javasoljuk, hogy használja az új modellt a jövőre.

Ha szövegfájlból szeretne olvasni, vagy szöveges fájlba ír, az `type` adatkészlet szakaszának tulajdonságát állítsa Szövegformátum értékre `format` .  Emellett megadhatja a következő **választható** tulajdonságokat a `format` szakaszban. A konfigurálással kapcsolatban lásd [A TextFormat használatát bemutató példa](#textformat-example) című szakaszt.

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| columnDelimiter |A fájlokban az oszlopok elválasztására használt karakter. Érdemes lehet olyan ritka, nem nyomtatható karaktert használni, amely nem létezik az adataiban. Adja meg például a "\u0001" kifejezést, amely a fejléc kezdetét jelöli (rendszerállapot-kimutatás). |Csak egy karakter használata engedélyezett. Az **alapértelmezett** érték a **vessző (,)**. <br/><br/>Ha Unicode-karaktert szeretne használni, a megfelelő kód beszerzéséhez tekintse meg a [Unicode-karaktereket](https://en.wikipedia.org/wiki/List_of_Unicode_characters) . |No |
| rowDelimiter |A fájlokban a sorok elválasztására használt karakter. |Csak egy karakter használata engedélyezett. Az **alapértelmezett** érték olvasáskor a következő értékek bármelyike: **[„\r\n”, „\r”, „\n”]**, illetve **„\r\n”** írás esetén. |No |
| escapeChar |Az oszlophatároló feloldására szolgáló speciális karakter a bemeneti fájl tartalmában. <br/><br/>Egy táblához nem határozható meg az escapeChar és a quoteChar is. |Csak egy karakter használata engedélyezett. Nincs alapértelmezett érték. <br/><br/>Példa: Ha a vesszőt (",") az oszlop elválasztójának kell lennie, de a szövegben vesszőt szeretne megadni (például: "Hello, World"), a "$" karaktert megadhatja Escape-karakterként, és a "Hello $, World" karakterláncot használhatja a forrásban. |No |
| quoteChar |Egy sztringérték idézéséhez használt karakter. Ekkor az idézőjel-karakterek közötti oszlop- és sorhatárolókat a rendszer a sztringérték részeként kezeli. Ez a tulajdonság a bemeneti és a kimeneti adatkészleteken is alkalmazható.<br/><br/>Egy táblához nem határozható meg az escapeChar és a quoteChar is. |Csak egy karakter használata engedélyezett. Nincs alapértelmezett érték. <br/><br/>Ha például vessző (,) az oszlophatároló, de a vessző karaktert szeretné megjeleníteni a szövegben (például: &lt;Helló, világ&gt;), megadhatja a " (angol dupla idézőjel) értéket idézőjel-karakterként, és a "Helló$, világ" sztringet használhatja a forrásban. |No |
| nullValue |A null értéket jelölő egy vagy több karakter. |Egy vagy több karakter. Az **alapértelmezett** értékek az **„\N” és „NULL”** olvasás, illetve **„\N”** írás esetén. |No |
| encodingName |A kódolási név megadására szolgál. |Egy érvényes kódolási név. Lásd az [Encoding.EncodingName tulajdonságot](/dotnet/api/system.text.encoding). Például: windows-1250 vagy shift_jis. Az **alapértelmezett** érték az **UTF-8**. |No |
| firstRowAsHeader |Megadja, hogy az első sort fejlécnek kell-e tekinteni. A bemeneti adatkészletek első sorát a Data Factory fejlécként olvassa be. A kimeneti adatkészletek első sorát a Data Factory fejlécként írja ki. <br/><br/>[A `firstRowAsHeader` és a `skipLineCount` használatára vonatkozó forgatókönyvekben](#scenarios-for-using-firstrowasheader-and-skiplinecount) tekinthet meg minta-forgatókönyveket. |Igaz<br/><b>False (alapértelmezett)</b> |No |
| skipLineCount |Az adatok bemeneti fájlokból való olvasásakor kihagyható **nem üres** sorok számát jelzi. Ha a skipLineCount és a firstRowAsHeader tulajdonság is meg van adva, a rendszer először kihagyja a sorokat, majd beolvassa a fejléc-információkat a bemeneti fájlból. <br/><br/>[A `firstRowAsHeader` és a `skipLineCount` használatára vonatkozó forgatókönyvekben](#scenarios-for-using-firstrowasheader-and-skiplinecount) tekinthet meg minta-forgatókönyveket. |Egész szám |No |
| treatEmptyAsNull |Meghatározza, hogy az adatok bemeneti fájlból történő olvasásakor a sztring null vagy üres értékeit null értékként kell-e kezelni. |**True (alapértelmezett)**<br/>Hamis |No |

### <a name="textformat-example"></a>A TextFormat használatát bemutató példa

Az adatkészletek következő JSON-definíciójában a választható tulajdonságok némelyike meg van adva.

```json
"typeProperties":
{
    "folderPath": "mycontainer/myfolder",
    "fileName": "myblobname",
    "format":
    {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": ";",
        "quoteChar": "\"",
        "NullValue": "NaN",
        "firstRowAsHeader": true,
        "skipLineCount": 0,
        "treatEmptyAsNull": true
    }
},
```

`quoteChar` helyett `quoteChar` használatához cserélje le a sort `escapeChar` értékre a következő escapeChar kifejezéssel:

```json
"escapeChar": "$",
```

### <a name="scenarios-for-using-firstrowasheader-and-skiplinecount"></a>A firstRowAsHeader és a skipLineCount használatára vonatkozó forgatókönyvek

* Egy nem fájlalapú forrásból másol egy szöveges fájlba, és szeretne hozzáadni egy fejlécsort, amely tartalmazza a séma (például SQL-séma) metaadatait. Ebben a forgatókönyvben adja meg a `firstRowAsHeader` értékét igazként a kimeneti adatkészletben.
* Egy fejlécsort tartalmazó szöveges fájlból másol egy nem fájlalapú fogadóba, és el szeretné hagyni azt a sort. Adja meg a `firstRowAsHeader` értékét igazként a bemeneti adatkészletben.
* Egy szöveges fájlból másol, és szeretne kihagyni néhány sort az elejéről, amelyek nem tartalmaznak adatokat vagy fejléc-információkat. Adja meg a `skipLineCount` értékét a kihagyni kívánt sorok számának jelzéséhez. Ha a fájl hátralévő része fejlécsort tartalmaz, a `firstRowAsHeader` is megadható. Ha a `skipLineCount` és a `firstRowAsHeader` is meg van adva, a rendszer először kihagyja a sorokat, majd beolvassa a fejléc-információkat a bemeneti fájlból

## <a name="json-format-legacy"></a><a name="json-format"></a> JSON formátum (örökölt)

>[!NOTE]
>Ismerje meg az új modellt a [JSON formátumú](format-json.md) cikkből. A fájl alapú adattár-adatkészletek következő konfigurációi továbbra is támogatottak, ha visszafelé compabitility. Azt javasoljuk, hogy használja az új modellt a jövőre.

A **JSON-fájlok Azure Cosmos DBba való importálásához/exportálásához** lásd: importálás/exportálás JSON-dokumentumok szakasz, [adatok áthelyezése Azure Cosmos db](connector-azure-cosmos-db.md) cikkbe.

Ha szeretné elemezni a JSON-fájlokat, vagy JSON formátumban kell írnia az adatírást, állítsa a ( `type` `format` **JsonFormat**) szakaszban található tulajdonságot a következőre:. Emellett megadhatja a következő **választható** tulajdonságokat a `format` szakaszban. A konfigurálással kapcsolatban lásd [A JsonFormat használatát bemutató példa](#jsonformat-example) című szakaszt.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| filePattern |Az egyes JSON-fájlokban tárolt adatok mintáját jelzi. Az engedélyezett értékek a következők: **setOfObjects** és **arrayOfObjects**. Az **alapértelmezett** érték a **setOfObjects**. A mintákkal kapcsolatban lásd a [JSON-fájlminták](#json-file-patterns) című szakaszt. |No |
| jsonNodeReference | Ha egy azonos mintával rendelkező tömbmezőben található objektumokat szeretne iterálni, vagy azokból adatokat kinyerni, adja meg a tömb JSON-útvonalát. Ez a tulajdonság csak akkor támogatott, ha JSON **-** fájlokból másol adatok. | No |
| jsonPathDefinition | Megadja az egyes oszlopmegfeleltetések JSON-útvonalának kifejezését testre szabott oszlopnevekkel (kezdje kisbetűvel). Ez a tulajdonság csak akkor támogatott, ha **JSON-** fájlokból másol adatokból, és kinyeri az adatait az objektumból vagy tömbből. <br/><br/> A gyökérobjektum alatti mezők esetében kezdjen a gyökér $ értékkel. A `jsonNodeReference` tulajdonság által kiválasztott tömbben lévő mezők esetében kezdjen a tömbelemmel. A konfigurálással kapcsolatban lásd [A JsonFormat használatát bemutató példa](#jsonformat-example) című szakaszt. | No |
| encodingName |A kódolási név megadására szolgál. Az érvényes kódolási nevekkel kapcsolatban lásd az [Encoding.EncodingName](/dotnet/api/system.text.encoding) tulajdonságot. Például: windows-1250 vagy shift_jis. Az **alapértelmezett** érték: **UTF-8**. |No |
| nestingSeparator |A beágyazási szinteket elválasztó karakter. Az alapértelmezett érték a „.” (pont). |No |

>[!NOTE]
>A tömbben lévő, több sorból származó adatmennyiség (1. eset – > minta 2 [JsonFormat-példákban](#jsonformat-example)) esetében csak egy tömböt lehet kibontani a tulajdonság használatával `jsonNodeReference` .

### <a name="json-file-patterns"></a>JSON-fájlminták

A másolási tevékenység a JSON-fájlok következő mintáit tudja elemezni:

- **I. típus: setOfObjects**

    Minden fájl egyetlen objektumot, illetve több, sorokkal határolt/összefűzött objektumot tartalmaz. Ha ezt a lehetőséget választja egy kimeneti adatkészletben, a másolási tevékenység egyetlen JSON-fájlt állít elő, soronként egy objektummal (sorokkal határolt).

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

    * **példa sorokkal határolt JSON-fájlra**

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

### <a name="jsonformat-example"></a>A JsonFormat használatát bemutató példa

**1. eset: Adatok másolása JSON-fájlokból**

**1. példa: adatok kigyűjtése objektumból és tömbből**

Ebben a példában egy JSON-gyökérobjektum képződik le egyetlen rekordba táblázatos nézetben. Ha a JSON-fájl a következőt tartalmazza:

```json
{
    "id": "ed0e4960-d9c5-11e6-85dc-d7996816aad3",
    "context": {
        "device": {
            "type": "PC"
        },
        "custom": {
            "dimensions": [
                {
                    "TargetResourceType": "Microsoft.Compute/virtualMachines"
                },
                {
                    "ResourceManagementProcessRunId": "827f8aaa-ab72-437c-ba48-d8917a7336a3"
                },
                {
                    "OccurrenceTime": "1/13/2017 11:24:37 AM"
                }
            ]
        }
    }
}
```

és az adatok objektumokból és tömbökből való kigyűjtésével szeretné átmásolni egy Azure SQL-táblába az alábbi formátumban:

| ID (Azonosító) | deviceType | targetResourceType | resourceManagementProcessRunId | occurrenceTime |
| --- | --- | --- | --- | --- |
| ed0e4960-d9c5-11e6-85dc-d7996816aad3 | PC | Microsoft.Compute/virtualMachines | 827f8aaa-ab72-437c-ba48-d8917a7336a3 | 1/13/2017 11:24:37 AM |

A **JsonFormat** típusú bemeneti adatkészlet a következőképpen van meghatározva (részleges meghatározás, csak a fontos részekkel). Pontosabban:

- A `structure` szakasz határozza meg a testre szabott oszlopneveket és a megfelelő adattípusokat, miközben átalakítja őket táblázatos adatokká. Ez a szakasz **nem kötelező**, kivéve, ha oszlopleképezést kell végeznie. További információ: [a forrás-adatkészlet oszlopainak leképezése a cél adatkészlet oszlopaira](copy-activity-schema-and-type-mapping.md).
- A `jsonPathDefinition` határozza meg az egyes oszlopok JSON-útvonalát, amely jelzi, hogy honnan történjen az adatok kinyerése. Az adatok tömbből való másolásához a segítségével `array[x].property` kinyerheti a megadott tulajdonság értékét az `xth` objektumból, vagy a segítségével `array[*].property` megkeresheti az adott tulajdonságot tartalmazó bármely objektum értékét.

```json
"properties": {
    "structure": [
        {
            "name": "id",
            "type": "String"
        },
        {
            "name": "deviceType",
            "type": "String"
        },
        {
            "name": "targetResourceType",
            "type": "String"
        },
        {
            "name": "resourceManagementProcessRunId",
            "type": "String"
        },
        {
            "name": "occurrenceTime",
            "type": "DateTime"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat",
            "filePattern": "setOfObjects",
            "jsonPathDefinition": {"id": "$.id", "deviceType": "$.context.device.type", "targetResourceType": "$.context.custom.dimensions[0].TargetResourceType", "resourceManagementProcessRunId": "$.context.custom.dimensions[1].ResourceManagementProcessRunId", "occurrenceTime": " $.context.custom.dimensions[2].OccurrenceTime"}
        }
    }
}
```

**2. példa: a tömbből származó ugyanazon minta keresztalkalmazása több objektumra**

Ebben a példában egy JSON-gyökérobjektumot alakít át több rekorddá táblázatos nézetben. Ha a JSON-fájl a következőt tartalmazza:

```json
{
    "ordernumber": "01",
    "orderdate": "20170122",
    "orderlines": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "sanmateo": "No 1" } ]
}
```

és szeretné átmásolni egy Azure SQL-táblába az alábbi formátumban, a tömbben lévő adatok egybesimításával, valamint keresztillesztést létrehozni a közös gyökérinformációval:

| `ordernumber` | `orderdate` | `order_pd` | `order_price` | `city` |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | `[{"sanmateo":"No 1"}]` |
| 01 | 20170122 | P2 | 13 | `[{"sanmateo":"No 1"}]` |
| 01 | 20170122 | P3 | 231 | `[{"sanmateo":"No 1"}]` |


A **JsonFormat** típusú bemeneti adatkészlet a következőképpen van meghatározva (részleges meghatározás, csak a fontos részekkel). Pontosabban:

- A `structure` szakasz határozza meg a testre szabott oszlopneveket és a megfelelő adattípusokat, miközben átalakítja őket táblázatos adatokká. Ez a szakasz **nem kötelező**, kivéve, ha oszlopleképezést kell végeznie. További információ: [a forrás-adatkészlet oszlopainak leképezése a cél adatkészlet oszlopaira](copy-activity-schema-and-type-mapping.md).
- `jsonNodeReference`azt jelzi, hogy az objektumokból származó adatok iterációja és kinyerése  ugyanazzal a mintázattal történik `orderlines` .
- A `jsonPathDefinition` határozza meg az egyes oszlopok JSON-útvonalát, amely jelzi, hogy honnan történjen az adatok kinyerése. Ebben a példában a, a `ordernumber` `orderdate` és a `city` gyökér objektum alatt a JSON elérési úttal kezdődő `$.` , míg `order_pd` és `order_price` a tömb elemből származtatott elérési úttal van meghatározva `$.` .

```json
"properties": {
    "structure": [
        {
            "name": "ordernumber",
            "type": "String"
        },
        {
            "name": "orderdate",
            "type": "String"
        },
        {
            "name": "order_pd",
            "type": "String"
        },
        {
            "name": "order_price",
            "type": "Int64"
        },
        {
            "name": "city",
            "type": "String"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat",
            "filePattern": "setOfObjects",
            "jsonNodeReference": "$.orderlines",
            "jsonPathDefinition": {"ordernumber": "$.ordernumber", "orderdate": "$.orderdate", "order_pd": "prod", "order_price": "price", "city": " $.city"}
        }
    }
}
```

**Vegye figyelembe a következő szempontokat:**

* Ha a `structure` és a `jsonPathDefinition` nincs meghatározva a Data Factory-adatkészletben, a másolási tevékenység az első objektumból észleli a sémát, és a teljes objektumot egybesimítja.
* Ha a JSON-bemenet egy tömböt tartalmaz, a másolási tevékenység alapértelmezés szerint a tömb teljes értékét egy sztringgé alakítja át. Választhatja, hogy a `jsonNodeReference` és/vagy a `jsonPathDefinition` használatával kívánja kinyerni belőle az adatokat, vagy ki is hagyhatja ezt a műveletet, ha nem adja meg a `jsonPathDefinition` értékét.
* Ha ismétlődő nevek szerepelnek ugyanazon a szinten, a másolási tevékenység az utolsót választja ki.
* A tulajdonságnevek megkülönböztetik a kis- és nagybetűket. A rendszer két azonos nevű, de eltérő kis- és nagybetűket tartalmazó tulajdonságot két különálló tulajdonságként kezel.

**2. eset: Adatok írása JSON-fájlba**

Ha a következő táblázat szerepel a SQL Databaseban:

| ID (Azonosító) | order_date | order_price | order_by |
| --- | --- | --- | --- |
| 1 | 20170119 | 2000 | David |
| 2 | 20170120 | 3500 | Patrick |
| 3 | 20170121 | 4000 | Jason |

minden rekord esetében a következő formátumban kell írnia egy JSON-objektumba:

```json
{
    "id": "1",
    "order": {
        "date": "20170119",
        "price": 2000,
        "customer": "David"
    }
}
```

A **JsonFormat** típusú kimeneti adatkészlet a következőképpen van meghatározva (részleges meghatározás, csak a fontos részekkel). Pontosabban `structure` a szakasz a célfájl testreszabott tulajdonságainak nevét határozza meg (az `nestingSeparator` alapértelmezett érték a "."), amely a név alapján azonosítja a beágyazási réteget. Ez a szakasz **nem kötelező**, kivéve, ha módosítani szeretné a tulajdonság nevét a forrásoszlop nevéhez képest, vagy egyes tulajdonságokat egymásba szeretne ágyazni.

```json
"properties": {
    "structure": [
        {
            "name": "id",
            "type": "String"
        },
        {
            "name": "order.date",
            "type": "String"
        },
        {
            "name": "order.price",
            "type": "Int64"
        },
        {
            "name": "order.customer",
            "type": "String"
        }
    ],
    "typeProperties": {
        "folderPath": "mycontainer/myfolder",
        "format": {
            "type": "JsonFormat"
        }
    }
}
```

## <a name="parquet-format-legacy"></a><a name="parquet-format"></a> Parketta formátuma (örökölt)

>[!NOTE]
>Ismerje meg az új modellt a [parketta formátumáról](format-parquet.md) szóló cikkből. A fájl alapú adattár-adatkészletek következő konfigurációi továbbra is támogatottak, ha visszafelé compabitility. Azt javasoljuk, hogy használja az új modellt a jövőre.

Ha elemezni szeretné a Parquet-fájlokat, vagy Parquet formátumban szeretne adatokat írni, állítsa a `format` `type` tulajdonságot **ParquetFormat** értékre. Nem kell meghatároznia semmilyen tulajdonságot a Format szakaszban a typeProperties szakaszon belül. Példa:

```json
"format":
{
    "type": "ParquetFormat"
}
```

Vegye figyelembe a következő szempontokat:

* Az összetett adattípusok nem támogatottak (Térkép, lista).
* Az oszlopnév nem támogatja az üres helyet.
* A Parquet-fájlok a következő tömörítéshez kapcsolódó beállításokat használják: NONE, SNAPPY, GZIP és LZO. Data Factory támogatja a Parquet-fájlból származó adatok olvasását ezen tömörített formátumok bármelyikén, a LZO kivételével – ez a metaadatokban található tömörítési kodeket használja az adatok olvasásához. A Parquet-fájlokba való írás esetén azonban a Data Factory a SNAPPY tömörítést választja, amely az alapértelmezett a Parquet-fájlok esetében. Jelenleg nincs lehetőség ennek a viselkedésnek a felülírására.

> [!IMPORTANT]
> A saját üzemeltetésű Integration Runtime, például a helyszíni és a Felhőbeli adattárak közötti másoláshoz, ha nem a (z) **rendszerű parketta-** fájlokat másolja, telepítenie kell a **64 bites JRE 8 (Java Runtime Environment) vagy a OpenJDK** az IR-gépen. További részletekért tekintse meg a következő bekezdést.

A saját üzemeltetésű, a Parquet-fájlok szerializálásával/deszerializálásával futó másolás esetén az ADF a Java futtatókörnyezetet úgy keresi meg, hogy először ellenőrzi a JRE beállításjegyzékét *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* , ha nem található, másodsorban a OpenJDK rendszer-változó ellenőrzése *`JAVA_HOME`* .

- A **JRE használatához**: a 64 bites IR használatához 64 bites JRE szükséges. [Itt](https://go.microsoft.com/fwlink/?LinkId=808605)találhatja meg.
- **A OpenJDK használata**: az IR 3,13-es verzió óta támogatott. Csomagolja ki a jvm.dllt a OpenJDK összes többi szükséges szerelvényével a saját üzemeltetésű IR-gépre, és ennek megfelelően állítsa be a rendszerkörnyezeti változót JAVA_HOME.

>[!TIP]
>Ha saját üzemeltetésű Integration Runtime használatával másol adatokba vagy a-ból a parketta formátumba, és a "hiba történt a Java, üzenet: **Java. lang. működése OutOfMemoryError: Java heap Space**" kifejezéssel, akkor hozzáadhat egy környezeti változót a saját üzemeltetésű integrációs `_JAVA_OPTIONS` modult futtató gépen, amely a JVM minimális/maximális méretének módosítását teszi lehetővé, majd újból futtatja a folyamatot.

![JVM-halom méretének beállítása a saját üzemeltetésű IR-ben](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

Példa: állítsa be `_JAVA_OPTIONS` a változót értékkel `-Xms256m -Xmx16g` . A jelző `Xms` meghatározza a Java virtuális gép (JVM) kezdeti memória-kiosztási készletét, míg `Xmx` a maximális memória-kiosztási készletet adja meg. Ez azt jelenti, hogy a JVM a memóriával fog elindulni, `Xms` és a memória maximális mennyiségét fogja tudni használni `Xmx` . Alapértelmezés szerint az ADF min 64 MB és Max 1G értéket használ.

### <a name="data-type-mapping-for-parquet-files"></a>A Parquet-fájlok adattípusának leképezése

| Az adatgyár átmeneti adattípusa | Parketta primitív típusa | Parketta eredeti típusa (deszerializálása) | Parketta eredeti típusa (szerializálás) |
|:--- |:--- |:--- |:--- |
| Logikai érték | Logikai érték | N.A. | N.A. |
| Sbyte érték | Int32 | Int8 | Int8 |
| Bájt | Int32 | UInt8 | Int16 |
| Int16 | Int32 | Int16 | Int16 |
| UInt16 | Int32 | UInt16 | Int32 |
| Int32 | Int32 | Int32 | Int32 |
| UInt32 | Int64 | UInt32 | Int64 |
| Int64 | Int64 | Int64 | Int64 |
| UInt64 | Int64/bináris | UInt64 | Tizedesjegy |
| Egyirányú | Float | N.A. | N.A. |
| Dupla | Dupla | N.A. | N.A. |
| Tizedesjegy | Bináris | Tizedesjegy | Tizedesjegy |
| Sztring | Bináris | Utf8 | Utf8 |
| DateTime | Int96 | N.A. | N.A. |
| időtartam | Int96 | N.A. | N.A. |
| DateTimeOffset | Int96 | N.A. | N.A. |
| ByteArray | Bináris | N.A. | N.A. |
| Guid | Bináris | Utf8 | Utf8 |
| Char | Bináris | Utf8 | Utf8 |
| CharArray | Nem támogatott | N.A. | N.A. |

## <a name="orc-format-legacy"></a><a name="orc-format"></a> ORK formátum (örökölt)

>[!NOTE]
>Ismerje meg az új modellt az [ork formátumról](format-orc.md) szóló cikkből. A fájl alapú adattár-adatkészletek következő konfigurációi továbbra is támogatottak, ha visszafelé compabitility. Azt javasoljuk, hogy használja az új modellt a jövőre.

Ha elemezni szeretné a ORC-fájlokat, vagy ORC formátumban szeretne adatokat írni, állítsa a `format` `type` tulajdonságot **OrcFormat** értékre. Nem kell meghatároznia semmilyen tulajdonságot a Format szakaszban a typeProperties szakaszon belül. Példa:

```json
"format":
{
    "type": "OrcFormat"
}
```

Vegye figyelembe a következő szempontokat:

* Az összetett adattípusok nem támogatottak (STRUCT, Térkép, lista, UNION).
* Az oszlopnév nem támogatja az üres helyet.
* Az ORC-fájlok három, [tömörítéshez kapcsolódó beállítással](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/) rendelkeznek: NONE, ZLIB, SNAPPY. A Data Factory a három tömörített formátum bármelyikében lévő ORC-fájlokból támogatja az adatok olvasását. Az adatok olvasásához a metaadatokban szereplő tömörítési kodeket használja. Az ORC-fájlokba való írás esetén azonban a Data Factory a ZLIB tömörítést választja, amely az alapértelmezett az ORC-fájlok esetében. Jelenleg nincs lehetőség ennek a viselkedésnek a felülírására.

> [!IMPORTANT]
> A saját üzemeltetésű Integration Runtime, például a helyszíni és a Felhőbeli adattárolók közötti másoláshoz, ha nem a **-ként** másolja az ork-fájlokat, telepítenie kell a **64 bites JRE 8 (Java Runtime Environment) vagy a OpenJDK** az IR-gépen. További részletekért tekintse meg a következő bekezdést.

A saját üzemeltetésű IR-ben az ork-fájl szerializálásával/deszerializálásával futó másoláshoz az ADF megkeresi a Java-futtatókörnyezetet úgy, hogy először ellenőrzi a JRE beállításjegyzékét *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* , ha nem található, másodsorban a OpenJDK rendszer-változó ellenőrzése *`JAVA_HOME`* .

- A **JRE használatához**: a 64 bites IR használatához 64 bites JRE szükséges. [Itt](https://go.microsoft.com/fwlink/?LinkId=808605)találhatja meg.
- **A OpenJDK használata**: az IR 3,13-es verzió óta támogatott. Csomagolja ki a jvm.dllt a OpenJDK összes többi szükséges szerelvényével a saját üzemeltetésű IR-gépre, és ennek megfelelően állítsa be a rendszerkörnyezeti változót JAVA_HOME.

### <a name="data-type-mapping-for-orc-files"></a>Az adattípusok leképezése az ork-fájlokhoz

| Az adatgyár átmeneti adattípusa | ORK-típusok |
|:--- |:--- |
| Logikai érték | Logikai érték |
| Sbyte érték | Bájt |
| Bájt | Rövid |
| Int16 | Rövid |
| UInt16 | Int |
| Int32 | Int |
| UInt32 | Hosszú |
| Int64 | Hosszú |
| UInt64 | Sztring |
| Egyirányú | Float |
| Dupla | Dupla |
| Tizedesjegy | Tizedesjegy |
| Sztring | Sztring |
| DateTime | Időbélyeg |
| DateTimeOffset | Időbélyeg |
| időtartam | Időbélyeg |
| ByteArray | Bináris |
| Guid | Sztring |
| Char | Char (1) |

## <a name="avro-format-legacy"></a><a name="avro-format"></a> AVRO formátuma (örökölt)

>[!NOTE]
>Ismerje meg az új modellt a [Avro Format](format-avro.md) cikkből. A fájl alapú adattár-adatkészletek következő konfigurációi továbbra is támogatottak, ha visszafelé compabitility. Azt javasoljuk, hogy használja az új modellt a jövőre.

Ha elemezni szeretné a Avro-fájlokat, vagy Avro formátumban szeretne adatokat írni, állítsa a `format` `type` tulajdonságot **AvroFormat** értékre. Nem kell meghatároznia semmilyen tulajdonságot a Format szakaszban a typeProperties szakaszon belül. Példa:

```json
"format":
{
    "type": "AvroFormat",
}
```

Ha a Avro formátumot szeretné használni egy struktúra-táblában, tekintse [meg Apache Hive oktatóanyagát](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).

Vegye figyelembe a következő szempontokat:

* Az [összetett adattípusok](https://avro.apache.org/docs/current/spec.html#schema_complex) nem támogatottak (rekordok, enumerálások, tömbök, térképek, szakszervezetek és rögzített).

## <a name="compression-support-legacy"></a><a name="compression-support"></a> Tömörítési támogatás (örökölt)

Azure Data Factory támogatja az adatok tömörítését vagy kibontását a másolás során. Ha `compression` egy bemeneti adatkészletben megad egy tulajdonságot, a másolási tevékenység beolvassa a forrásból a tömörített adatokat, kibontja azt, és ha a tulajdonságot egy kimeneti adatkészletben adja meg, a másolási tevékenység tömöríti, majd adatokat ír a fogadóba. Íme néhány példa a példákra:

* Olvassa el a GZIP tömörített adatait egy Azure-blobból, bontsa ki, majd írja be az eredményeket Azure SQL Databaseba. A bemeneti Azure Blob-adatkészletet a tulajdonsággal együtt a GZIP-ként definiálhatja `compression` `type` .
* Az adatok beolvasása egy egyszerű szöveges fájlból a helyszíni fájlrendszerből, GZip formátum használatával tömöríthető, és a tömörített adatok megírása egy Azure-blobba. A kimeneti Azure Blob-adatkészletet a `compression` `type` tulajdonsággal együtt gzip-ként definiálhatja.
* Olvassa el a. zip fájlt az FTP-kiszolgálóról, bontsa ki, hogy beolvassa a fájlokat a belsejében, és a fájlokat a Azure Data Lake Store. Definiáljon egy bemeneti FTP-adatkészletet a ( `compression` `type` ZipDeflate) tulajdonsággal.
* Egy Azure-blobból származó, GZIP-tömörített adatok beolvasása, kibontása, a BZIP2 használatával történő tömörítés, valamint az eredmények egy Azure-blobba írása. A bemeneti Azure Blob-adatkészletet a GZIP értékre, `compression` `type` a kimeneti adatkészletet pedig a `compression` `type` bzip2 értékre kell beállítani.

Az adatkészlet tömörítésének megadásához használja az adatkészlet JSON **tömörítés** tulajdonságát az alábbi példában látható módon:

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "fileName": "pagecounts.csv.gz",
            "folderPath": "compression/file/",
            "format": {
                "type": "TextFormat"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

A **tömörítési** szakasz két tulajdonsággal rendelkezik:

* **Típus:** a tömörítési kodek, amely lehet **gzip**, **deflate**, **bzip2** vagy **ZipDeflate**. Vegye figyelembe, hogy ha másolási tevékenységet használ a ZipDeflate fájl (ok) kibontásához és a fájl alapú fogadó adattárba való íráshoz, a fájlok a következő mappába lesznek kibontva: `<path specified in dataset>/<folder named as source zip file>/` .
* **Szint:** a tömörítési arány, amely **optimális** vagy **leggyorsabb** lehet.

  * **Leggyorsabb:** A tömörítési műveletnek a lehető leggyorsabban kell elvégeznie, még akkor is, ha az eredményül kapott fájl nem tömöríthető optimálisan.
  * **Optimális**: a tömörítési műveletet optimálisan kell tömöríteni, még akkor is, ha a művelet végrehajtása hosszú időt vesz igénybe.

    További információ: [tömörítési szint](/dotnet/api/system.io.compression.compressionlevel) témakör.

> [!NOTE]
> A **AvroFormat**, **OrcFormat** vagy **ParquetFormat** lévő adattömörítési beállítások nem támogatottak. Ezekben a formátumokban a fájlok olvasásakor a Data Factory észleli és a metaadatokban használja a tömörítési kodeket. A formátumokban lévő fájlok írásakor Data Factory kiválasztja az alapértelmezett tömörítési kodeket az adott formátumhoz. Például: ZLIB for OrcFormat és SNAPPY for ParquetFormat.

## <a name="unsupported-file-types-and-compression-formats"></a>A fájltípusok és a tömörítési formátumok nem támogatottak

A Azure Data Factory bővíthetőségi funkciói a nem támogatott fájlok átalakítására használhatók.
A Azure Batch használatával két lehetőség Azure Functions és egyéni feladatokat is tartalmaz.

Egy olyan minta látható, amely egy Azure-függvényt használ [egy tar-fájl tartalmának kibontásához](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV2/UntarAzureFilesWithAzureFunction). További információ: [Azure functions tevékenység](./control-flow-azure-function-activity.md).

Ezt a funkciót egyéni DotNet-tevékenység használatával is létrehozhatja. További információ [itt](./transform-data-using-dotnet-custom-activity.md) érhető el

## <a name="next-steps"></a>Következő lépések

Ismerje meg a támogatott fájlformátumok és [tömörítések](supported-file-formats-and-compression-codecs.md)legújabb támogatott fájlformátumait és tömörítéseit.