---
title: Séma-és adattípus-leképezés a másolási tevékenységben
description: Tudnivalók a másolási tevékenység Azure Data Factory Maps-sémák és adattípusok a forrásadatoktől a fogadó adatok elfogadásához.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: jingwang
ms.openlocfilehash: 0aee6030e5608b5413864d6a32dc8442dd346f42
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100392782"
---
# <a name="schema-and-data-type-mapping-in-copy-activity"></a>Séma-és adattípus-leképezés a másolási tevékenységben
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogy a Azure Data Factory másolási tevékenység hogyan hajtja végre a séma-hozzárendelést és az adattípus-leképezést a forrásadatokből a fogadó adatok

## <a name="schema-mapping"></a>Séma-hozzárendelés

### <a name="default-mapping"></a>Alapértelmezett leképezés

Alapértelmezés szerint a másolási tevékenység leképezi a forrásoldali adatokat, hogy az **oszlopok nevei** megkülönböztetik a kis-és nagybetűket. Ha a fogadó nem létezik, például a fájl (ok) ra való írás, a forrás mező neve a fogadó neveként marad. Az ilyen alapértelmezett leképezés támogatja a rugalmas sémákat és a séma-eltolódást a forrástól a fogadónak a végrehajtástól a végrehajtásig – a forrás adattár által visszaadott összes adatok átmásolhatók a fogadóba.

Ha a forrás szöveges fájl fejléc nélküli szövegfájl, akkor [explicit leképezésre](#explicit-mapping) van szükség, mert a forrás nem tartalmaz oszlopnevek.

### <a name="explicit-mapping"></a>Explicit leképezés

A explicit leképezést is megadhatja, hogy az oszlop/mező leképezése a forrásról a fogadóra, igény szerint legyen kialakítva. A explicit leképezéssel csak a részleges adatforrásokat másolhatja a fogadóba, vagy leképezheti a forrásadatokat különböző nevekkel, vagy átalakíthatja a táblázatos/hierarchikus adatokat. Másolási tevékenység:

1. Beolvassa az adatokat a forrásból, és meghatározza a forrás sémát.
2. A megadott leképezést alkalmazza.
3. Az adatot a fogadóba írja.

További információk:

- [Táblázatos forrás – táblázatos fogadó](#tabular-source-to-tabular-sink)
- [Hierarchikus forrás – táblázatos fogadó](#hierarchical-source-to-tabular-sink)
- [Táblázatos/hierarchikus forrás a hierarchikus fogadóhoz](#tabularhierarchical-source-to-hierarchical-sink)

A leképezést konfigurálhatja Data Factory authoring UI-> másolási tevékenység-> leképezés lapon, vagy programozott módon megadhatja a leképezést a másolási tevékenység – > `translator` tulajdonságban. A következő tulajdonságokat támogatja a `translator`  ->  `mappings` Array-> Objects-> `source` és `sink` , amely az adott oszlopra/mezőre mutat az adatleképezéshez.

| Tulajdonság | Leírás                                                  | Kötelező |
| -------- | ------------------------------------------------------------ | -------- |
| name     | A forrás vagy a fogadó oszlop/mező neve. A táblázatos forrás és a fogadó esetében alkalmazható. | Yes      |
| sorszámok  | Oszlop indexe Kezdés: 1. <br>Alkalmazva és kötelező, ha a tagolt szöveg fejléc nélkül van használatban. | No       |
| path     | Az egyes mezőkhöz tartozó JSON-elérésiút-kifejezés kibontása vagy leképezése. Hierarchikus forrásra és fogadóra vonatkozik, például Cosmos DB, MongoDB vagy REST-összekötők.<br>A root objektum alatti mezők esetében a JSON elérési útja a root karakterrel kezdődik `$` ; a tulajdonság által választott tömbben lévő mezők esetében a `collectionReference` JSON-útvonal a tömb elemtől kezdve nem `$` . | No       |
| típus     | Data Factory a forrás vagy a fogadó oszlop közbenső adattípusa. Általánosságban elmondható, hogy nem kell megadnia vagy módosítania ezt a tulajdonságot. További információ az [adattípus-hozzárendelésről](#data-type-mapping). | No       |
| kulturális környezet  | A forrás vagy a fogadó oszlop kulturális környezete. Akkor alkalmazza, ha a típus értéke `Datetime` vagy `Datetimeoffset` . A mező alapértelmezett értéke: `en-us`.<br>Általánosságban elmondható, hogy nem kell megadnia vagy módosítania ezt a tulajdonságot. További információ az [adattípus-hozzárendelésről](#data-type-mapping). | No       |
| formátumban   | A Type vagy a típushoz használandó formázó sztring `Datetime` `Datetimeoffset` . A DateTime formátumának formázásához tekintse meg az [Egyéni dátum-és időformátumot ismertető karakterláncot](/dotnet/standard/base-types/custom-date-and-time-format-strings) . Általánosságban elmondható, hogy nem kell megadnia vagy módosítania ezt a tulajdonságot. További információ az [adattípus-hozzárendelésről](#data-type-mapping). | No       |

A következő tulajdonságokat támogatja a (z) `translator` mellett `mappings` :

| Tulajdonság            | Leírás                                                  | Kötelező |
| ------------------- | ------------------------------------------------------------ | -------- |
| collectionReference | Az adatok hierarchikus forrásból való másolása, például Cosmos DB, MongoDB vagy REST-összekötők esetén alkalmazható.<br>Ha szeretné megismételni és kinyerni a **tömbben** lévő objektumokból származó adatmennyiséget ugyanazzal a mintával, és soronként konvertálja az objektumokat, akkor a tömb JSON-elérési útját kell megadnia. | No       |

#### <a name="tabular-source-to-tabular-sink"></a>Táblázatos forrás – táblázatos fogadó

Például az adatok Salesforce-ből Azure SQL Databaseba másolásához és a három oszlop explicit módon történő hozzárendeléséhez:

1. A másolási tevékenység – > leképezés lapon kattintson a **sémák importálása** gombra a forrás-és fogadó sémák importálásához.

2. Képezze le a szükséges mezőket, és zárja ki/törölje a REST-et.

![Táblázatos leképezése táblázatos értékre](media/copy-activity-schema-and-type-mapping/map-tabular-to-tabular.png)

Ugyanez a leképezés konfigurálható a másolási tevékenység hasznos adatai között (lásd: `translator` ):

```json
{
    "name": "CopyActivityTabularToTabular",
    "type": "Copy",
    "typeProperties": {
        "source": { "type": "SalesforceSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": { "name": "Id" },
                    "sink": { "name": "CustomerID" }
                },
                {
                    "source": { "name": "Name" },
                    "sink": { "name": "LastName" }
                },
                {
                    "source": { "name": "LastModifiedDate" },
                    "sink": { "name": "ModifiedDate" }
                }
            ]
        }
    },
    ...
}
```

Az adatok fejléc nélküli, tagolt szövegfájlból (k) történő másolásához az oszlopokat a nevek helyett sorszámok jelölik. 

```json
{
    "name": "CopyActivityTabularToTabular",
    "type": "Copy",
    "typeProperties": {
        "source": { "type": "DelimitedTextSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": { "ordinal": "1" },
                    "sink": { "name": "CustomerID" }
                }, 
                {
                    "source": { "ordinal": "2" },
                    "sink": { "name": "LastName" }
                }, 
                {
                    "source": { "ordinal": "3" },
                    "sink": { "name": "ModifiedDate" }
                }
            ]
        }
    },
    ...
}
```

#### <a name="hierarchical-source-to-tabular-sink"></a>Hierarchikus forrás – táblázatos fogadó

Ha a hierarchikus forrásból másol adatokat táblázatos fogadóba, a másolási tevékenység a következő képességeket támogatja:

- Adatok kinyerése az objektumokból és tömbökből.
- Több objektumot is alkalmazhat ugyanazzal a mintázattal egy tömbből, ebben az esetben egy JSON-objektumot táblázatos eredményben több rekordba konvertálhat.

A összetettebb hierarchikus és a táblázatos átalakításhoz használhatja az [adatfolyamot](concepts-data-flow-overview.md). 

Ha például a forrás MongoDB-dokumentum a következő tartalommal rendelkezik:

```json
{
    "id": {
        "$oid": "592e07800000000000000000"
    },
    "number": "01",
    "date": "20170122",
    "orders": [
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
    "city": [ { "name": "Seattle" } ]
}
```

És egy szövegfájlba kívánja másolni a következő formátumban a fejlécben a tömbben található adatok összegyűjtésével *(order_pd és order_price)* , valamint a közös legfelső szintű információkkal való összekapcsolással *(szám, dátum és város)*:

| rendelésszáma | RendelésDátuma | order_pd | order_price | city    |
| ----------- | --------- | -------- | ----------- | ------- |
| 01          | 20170122  | P1       | 23          | Seattle |
| 01          | 20170122  | P2       | 13          | Seattle |
| 01          | 20170122  | P3       | 231         | Seattle |

Megadhatja a leképezést Data Factory szerzői felhasználói felületen:

1. A másolási tevékenység – > leképezés lapon kattintson a **sémák importálása** gombra a forrás-és fogadó sémák importálásához. Ahogy Data Factory a séma importálásakor a legfontosabb néhány objektumot, ha bármelyik mező nem jelenik meg, felveheti a hierarchiában a megfelelő rétegbe – vigye a kurzort egy meglévő mezőnév fölé, és válassza a csomópont, objektum vagy tömb hozzáadását.

2. Válassza ki azt a tömböt, amelyről meg szeretné ismételni az adatok kinyerését. A rendszer automatikusan kitölti a **gyűjteményi referenciát**. Vegye figyelembe, hogy ilyen művelet esetén csak egyetlen tömb támogatott.

3. Rendelje hozzá a szükséges mezőket a fogadóhoz. A Data Factory automatikusan meghatározza a hierarchikus oldalhoz tartozó JSON-útvonalakat.

> [!NOTE]
> Azon rekordok esetében, amelyeknél a gyűjteményi hivatkozásként megjelölt tömb üres, és a jelölőnégyzet be van jelölve, a rendszer kihagyja a teljes rekordot.

![Hierarchia leképezése táblázatos KEZELŐFELÜLET használatával](media/copy-activity-schema-and-type-mapping/map-hierarchical-to-tabular-ui.png)

Átválthat a **speciális szerkesztőre** is, amely esetben közvetlenül megtekintheti és szerkesztheti a mezők JSON-elérési útvonalait. Ha ebben a nézetben új leképezést ad hozzá, adja meg a JSON elérési útját.

![Hierarchia leképezése táblázatos formában a speciális szerkesztő használatával](media/copy-activity-schema-and-type-mapping/map-hierarchical-to-tabular-advanced-editor.png)

Ugyanez a leképezés konfigurálható a másolási tevékenység hasznos adatai között (lásd: `translator` ):


```json
{
    "name": "CopyActivityHierarchicalToTabular",
    "type": "Copy",
    "typeProperties": {
        "source": { "type": "MongoDbV2Source" },
        "sink": { "type": "DelimitedTextSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": { "path": "$['number']" },
                    "sink": { "name": "orderNumber" }
                },
                {
                    "source": { "path": "$['date']" },
                    "sink": { "name": "orderDate" }
                },
                {
                    "source": { "path": "['prod']" },
                    "sink": { "name": "order_pd" }
                },
                {
                    "source": { "path": "['price']" },
                    "sink": { "name": "order_price" }
                },
                {
                    "source": { "path": "$['city'][0]['name']" },
                    "sink": { "name": "city" }
                }
            ],
            "collectionReference": "$['orders']"
        }
    },
    ...
}
```

#### <a name="tabularhierarchical-source-to-hierarchical-sink"></a>Táblázatos/hierarchikus forrás a hierarchikus fogadóhoz

A felhasználói élmény folyamata hasonló a [táblázatos fogadó hierarchikus forrásához](#hierarchical-source-to-tabular-sink). 

Ha a táblázatos forrásból származó adatokat hierarchikus fogadóba másolja, az objektumon belüli tömbbe való írás nem támogatott.

Az adatok hierarchikus forrásról hierarchikus fogadóba való másolása során a teljes réteg hierarchiáját is megőrizheti az objektum/tömb kiválasztásával, és a belső mezők érintése nélkül is megtekintheti a fogadót.

A speciális Adatátalakítási folyamatok esetében az [adatfolyamot](concepts-data-flow-overview.md)használhatja. 

### <a name="parameterize-mapping"></a>Parametrizálja leképezése

Ha templatized folyamatot szeretne létrehozni a nagy számú objektum dinamikus másolásához, állapítsa meg, hogy kihasználhatja-e az [alapértelmezett leképezést](#default-mapping) , vagy [explicit leképezést](#explicit-mapping) kell definiálnia a megfelelő objektumokhoz.

Ha explicit leképezésre van szükség, a következőket teheti:

1. Definiáljon egy, az Objektumtípus paramétert a folyamat szintjén, például: `mapping` .

2. A leképezés parametrizálja: a másolási tevékenység – > leképezés lapon válassza a dinamikus tartalom hozzáadása lehetőséget, és válassza ki a fenti paramétert. A tevékenység tartalma a következő lesz:

    ```json
    {
        "name": "CopyActivityHierarchicalToTabular",
        "type": "Copy",
        "typeProperties": {
            "source": {...},
            "sink": {...},
            "translator": {
                "value": "@pipeline().parameters.mapping",
                "type": "Expression"
            },
            ...
        }
    }
    ```

3. Hozza létre a leképezési paraméternek átadandó értéket. A definíciók teljes objektumának kell lennie `translator` , és a mintákat a [explicit leképezés](#explicit-mapping) szakaszban találja. Például a táblázatos forrás és a táblázatos fogadó másolás esetében az értéknek a következőnek kell lennie: `{"type":"TabularTranslator","mappings":[{"source":{"name":"Id"},"sink":{"name":"CustomerID"}},{"source":{"name":"Name"},"sink":{"name":"LastName"}},{"source":{"name":"LastModifiedDate"},"sink":{"name":"ModifiedDate"}}]}` .

## <a name="data-type-mapping"></a>Adattípus-leképezés

A másolási tevékenység a következő folyamattal rendelkező forrás típusokat hajtja végre a fogadó típusok számára: 

1. Konvertálja a forrás natív adattípusait Azure Data Factory átmeneti adattípusokra.
2. Az ideiglenes adattípusok automatikus konvertálása szükség szerint a megfelelő fogadó típusok egyeztetéséhez, amely az [alapértelmezett leképezésre](#default-mapping) és a [explicit leképezésre](#explicit-mapping)is érvényes.
3. A Azure Data Factory átmeneti adattípusokból a natív adattípusok fogadására konvertálhat.

A másolási tevékenység jelenleg a következő közbenső adattípusokat támogatja: logikai, bájt, bájt tömb, datetime, DatetimeOffset, decimális, Double, GUID, Int16, Int32, Int64, sbyte érték, Single, string, TimeSpan, UInt16, UInt32 és UInt64.

A következő adattípus-átalakítások támogatottak a forrás és a fogadó közti közbenső típusok között.

| Source\Sink | Logikai | Bájtos tömb | Tizedesjegy | Dátum/idő <small>(1)</small> | Lebegőpontos pont <small>(2)</small> | GUID | Egész szám <small>(3)</small> | Sztring | időtartam |
| ----------- | ------- | ---------- | ------- | ---------------------------- | ------------------------------ | ---- | -------------------------- | ------ | -------- |
| Logikai     | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| Bájtos tömb  |         | ✓          |         |                              |                                |      |                            | ✓      |          |
| Dátum/idő   |         |            |         | ✓                            |                                |      |                            | ✓      |          |
| Tizedesjegy     | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| Lebegőpontos | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| GUID        |         |            |         |                              |                                | ✓    |                            | ✓      |          |
| Egész szám     | ✓       |            | ✓       |                              | ✓                              |      | ✓                          | ✓      |          |
| Sztring      | ✓       | ✓          | ✓       | ✓                            | ✓                              | ✓    | ✓                          | ✓      | ✓        |
| időtartam    |         |            |         |                              |                                |      |                            | ✓      | ✓        |

(1) dátum/idő tartalmazza a DateTime és a DateTimeOffset.

(2) az lebegőpontos pont tartalmaz egy-egy és egy dupla.

(3) az egész szám tartalmazza a sbyte érték, a byte, a Int16, a UInt16, az Int32, a UInt32, a Int64 és a UInt64 értéket.

> [!NOTE]
> - A táblázatos adatok közötti másolás során jelenleg ez az adattípus-átalakítás támogatott. A hierarchikus források/nyelők nem támogatottak, ami azt jelenti, hogy a forrás-és a fogadó köztes típusok nem rendelkeznek a rendszer által definiált adattípusok átalakításával.
> - Ez a funkció a legújabb adatkészlet-modellel működik. Ha nem látja ezt a lehetőséget a felhasználói felületen, próbálkozzon új adatkészlet létrehozásával.

Az adattípusok átalakításának másolási tevékenységében a következő tulajdonságok támogatottak (a `translator` programozott létrehozás szakasza alatt):

| Tulajdonság                         | Leírás                                                  | Kötelező |
| -------------------------------- | ------------------------------------------------------------ | -------- |
| typeConversion                   | Engedélyezze az új adattípus-átalakítási élményt. <br>Az alapértelmezett érték a visszafelé való kompatibilitás miatt hamis.<br><br>Az Data Factory authoring felhasználói felületen, a 2020-es év végén létrehozott másolási tevékenységek esetében az adattípusok konverziója alapértelmezés szerint engedélyezve van a legjobb megoldáshoz, és a következő típus-átalakítási beállítások jelennek meg a másolási tevékenység-> leképezés lapon a megfelelő forgatókönyvek esetében. <br>A folyamat programozott létrehozásához explicit módon be kell állítania a `typeConversion` tulajdonságot True értékre, hogy engedélyezze azt.<br>A szolgáltatás kiadása előtt létrehozott meglévő másolási tevékenységek esetében nem fog megjelenni a konvertálási beállítások a Data Factory szerzői felhasználói felületen a visszamenőleges kompatibilitás érdekében. | No       |
| typeConversionSettings           | Átalakítási beállítások típusú csoport. Alkalmazza, ha a értékre `typeConversion` van állítva `true` . A következő tulajdonságok a csoport alatt találhatók. | No       |
| *Alatt `typeConversionSettings`* |                                                              |          |
| allowDataTruncation              | Adatcsonkítás engedélyezése, ha a forrásadatok konvertálása a másolás során eltérő típussal történik, például decimálisról egészre, a DatetimeOffset és a DateTime értékre. <br>Az alapértelmezett érték true (igaz). | No       |
| treatBooleanAsNumber             | A logikai értékek számként való kezelése, például igaz, mint 1.<br>Az alapértelmezett érték false (hamis). | No       |
| dateTimeFormat                   | Formázhatja a karakterláncot, ha a dátumok közötti konverzió időzóna-eltolás és karakterláncok nélkül, például: `yyyy-MM-dd HH:mm:ss.fff` .  Részletes információkért tekintse meg az [Egyéni dátum-és időformátum-karakterláncot](/dotnet/standard/base-types/custom-date-and-time-format-strings) . | No       |
| dateTimeOffsetFormat             | Formázhatja a karakterláncot, ha az időzóna-eltolással és a karakterláncokkal, például: `yyyy-MM-dd HH:mm:ss.fff zzz` .  Részletes információkért tekintse meg az [Egyéni dátum-és időformátum-karakterláncot](/dotnet/standard/base-types/custom-date-and-time-format-strings) . | No       |
| timeSpanFormat                   | Formázhatja a karakterláncot az időszakok és karakterláncok közötti átalakításkor, például: `dd\.hh\:mm` . Részletes információkért tekintse meg az [Egyéni TimeSpan formázási karakterláncokat](/dotnet/standard/base-types/custom-timespan-format-strings) . | No       |
| kulturális környezet                          | A típusok konvertálásakor használandó kulturális információk (például `en-us` vagy `fr-fr` ). | No       |

**Példa**

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "ParquetSource"
        },
        "sink": {
            "type": "SqlSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "typeConversion": true,
            "typeConversionSettings": {
                "allowDataTruncation": true,
                "treatBooleanAsNumber": true,
                "dateTimeFormat": "yyyy-MM-dd HH:mm:ss.fff",
                "dateTimeOffsetFormat": "yyyy-MM-dd HH:mm:ss.fff zzz",
                "timeSpanFormat": "dd\.hh\:mm",
                "culture": "en-gb"
            }
        }
    },
    ...
}
```

## <a name="legacy-models"></a>Örökölt modellek

> [!NOTE]
> A következő modellek a forrás oszlopainak/mezőinek a fogadóba való leképezése továbbra is támogatott a visszamenőleges kompatibilitás érdekében. Javasoljuk, hogy használja a [séma-hozzárendelésben](#schema-mapping)említett új modellt. A Data Factory szerzői felhasználói felület az új modell generálására lett kapcsolva.

### <a name="alternative-column-mapping-legacy-model"></a>Alternatív oszlop – leképezés (örökölt modell)

Megadhatja a másolási tevékenység-> `translator`  ->  `columnMappings` a táblázatos adatokat a leképezéshez. Ebben az esetben a "Structure" szakasz szükséges a bemeneti és a kimeneti adatkészletekhez is. Az oszlop-hozzárendelés támogatja a "Structure" **forrás-adatkészlet összes oszlopának vagy részhalmazának leképezését a "Structure" fogadó adatkészlet összes oszlopára**. A következő hibák a kivételt eredményezik:

- A forrás adattároló lekérdezési eredményének nincs olyan oszlopa, amely meg van adva a (z) "Structure" bemeneti adatkészletben.
- A fogadó adattár (ha előre definiált sémával rendelkezik) nem rendelkezik olyan oszloppal, amely meg van adva a "Structure" (kimeneti adatkészlet) szakaszban.
- Kevesebb oszlop vagy több oszlop szerepel a fogadó adatkészlet "szerkezetében", mint a leképezésben megadott érték.
- Ismétlődő leképezés.

A következő példában a bemeneti adatkészlet struktúrát tartalmaz, és egy helyszíni Oracle-adatbázisban lévő táblára mutat.

```json
{
    "name": "OracleDataset",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "OracleLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

Ebben a példában a kimeneti adatkészlet struktúrát tartalmaz, és egy Salesfoce-táblára mutat.

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "SalesforceObject",
        "linkedServiceName": {
            "referenceName": "SalesforceLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

A következő JSON a másolási tevékenységet definiálja egy folyamaton belül. A forrás oszlopai a befogadó oszlopokra vannak leképezve a **Translator**  ->  **columnMappings** tulajdonság használatával.

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "OracleDataset",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "SalesforceDataset",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "OracleSource" },
        "sink": { "type": "SalesforceSink" },
        "translator":
        {
            "type": "TabularTranslator",
            "columnMappings":
            {
                "UserId": "MyUserId",
                "Group": "MyGroup",
                "Name": "MyName"
            }
        }
    }
}
```

Ha a szintaxisát használja az `"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` oszlopok megfeleltetésének megadásához, akkor továbbra is támogatott.

### <a name="alternative-schema-mapping-legacy-model"></a>Alternatív séma – leképezés (örökölt modell)

Megadhatja a másolási tevékenység-> `translator`  ->  `schemaMapping` a hierarchikus és a táblázatos adatok közötti leképezéshez, például másolás a MongoDB/Rest fájlból a szövegfájlba, és a másolás az Oracle-ből Azure Cosmos db API-MongoDB. A másolási tevékenység szakaszban a következő tulajdonságok támogatottak `translator` :

| Tulajdonság            | Leírás                                                  | Kötelező |
| :------------------ | :----------------------------------------------------------- | :------- |
| típus                | A másolási tevékenység fordítójának Type tulajdonságát a következőre kell beállítani: **TabularTranslator** | Yes      |
| schemaMapping       | Kulcs-érték párok gyűjteménye, amely a **forrás oldalról a fogadó oldalra való** leképezési kapcsolatot jelöli.<br/>- **Kulcs:** a forrást jelöli. **Táblázatos forrás** esetén adja meg az oszlop nevét az adatkészlet struktúrájában definiált módon. **hierarchikus forrás** esetén adja meg a JSON-elérésiút-kifejezést az egyes mezők kinyeréséhez és leképezéséhez.<br>- **Érték:** a fogadót jelöli. **Táblázatos** fogadó esetén adja meg az oszlop nevét az adatkészlet struktúrájában definiált módon. **hierarchikus** fogadó esetén adja meg a JSON-elérésiút-kifejezést az egyes mezők kinyeréséhez és leképezéséhez. <br>Hierarchikus adat esetén a gyökér objektum alatti mezők esetében a JSON-útvonal a root $; karakterrel kezdődik. a tulajdonság által választott tömbben lévő mezők esetében `collectionReference` a JSON-útvonal a tömb elemből indul el. | Yes      |
| collectionReference | Ha szeretné megismételni és kinyerni a **tömbben** lévő objektumokból származó adatmennyiséget ugyanazzal a mintával, és soronként konvertálja az objektumokat, akkor a tömb JSON-elérési útját kell megadnia. Ez a tulajdonság csak akkor támogatott, ha a hierarchikus adatforrás a forrás. | No       |

**Példa: másolás a MongoDB-ből az Oracle-be:**

Ha például a következő tartalommal rendelkezik a MongoDB-dokumentummal:

```json
{
    "id": {
        "$oid": "592e07800000000000000000"
    },
    "number": "01",
    "date": "20170122",
    "orders": [
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
    "city": [ { "name": "Seattle" } ]
}
```

és a következő formátumban szeretné átmásolni egy Azure SQL-táblába a tömbben lévő adatok összesimításával *(order_pd és order_price)* , valamint a közös legfelső szintű információkkal való összekapcsolással *(szám, dátum és város)*:

| rendelésszáma | RendelésDátuma | order_pd | order_price | city    |
| ----------- | --------- | -------- | ----------- | ------- |
| 01          | 20170122  | P1       | 23          | Seattle |
| 01          | 20170122  | P2       | 13          | Seattle |
| 01          | 20170122  | P3       | 231         | Seattle |

Konfigurálja a séma-leképezési szabályt a következő másolási tevékenység JSON-mintaként:

```json
{
    "name": "CopyFromMongoDBToOracle",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "MongoDbV2Source"
        },
        "sink": {
            "type": "OracleSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "schemaMapping": {
                "$.number": "orderNumber",
                "$.date": "orderDate",
                "prod": "order_pd",
                "price": "order_price",
                "$.city[0].name": "city"
            },
            "collectionReference":  "$.orders"
        }
    }
}
```

## <a name="next-steps"></a>Következő lépések
Lásd a másolási tevékenység egyéb cikkeit:

- [Másolási tevékenység – áttekintés](copy-activity-overview.md)
