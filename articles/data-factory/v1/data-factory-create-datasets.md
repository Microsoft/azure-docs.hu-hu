---
title: Adatkészletek létrehozása a Azure Data Factoryban
description: Megtudhatja, hogyan hozhat létre adatkészleteket Azure Data Factoryban olyan példákkal, amelyek olyan tulajdonságokat használnak, mint például az eltolás és a anchorDateTime.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 4b222b387dad1c078cfe2a063ed310ef463b192e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100376836"
---
# <a name="datasets-in-azure-data-factory-version-1"></a>Adatkészletek Azure Data Factory (1. verzió)
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-create-datasets.md)
> * [2-es verzió (aktuális verzió)](../concepts-datasets-linked-services.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, lásd: [adatkészletek a v2-ben](../concepts-datasets-linked-services.md).

Ez a cikk ismerteti, hogy mely adatkészletek, hogyan vannak meghatározva JSON formátumban, és hogyan használják őket Azure Data Factory folyamatokban. Az adatkészlet JSON-definíciójában az egyes szakaszokra (például a szerkezetre, a rendelkezésre állásra és a házirendre) vonatkozó részleteket tartalmaz. A cikk emellett példákat is tartalmaz az **eltolás**, a **anchorDateTime** és a **stílus** tulajdonságainak használatára egy adatkészlet JSON-definíciójában.

> [!NOTE]
> Ha Data Factory új, tekintse meg az áttekintést a [Azure Data Factory bemutatása](data-factory-introduction.md) című témakört. Ha nem rendelkezik gyakorlati tapasztalattal az adatüzemek létrehozásához, akkor jobb megértést nyerhet az [Adatátalakítási oktatóanyag](data-factory-build-your-first-pipeline.md) és az [adatáthelyezési oktatóanyag](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)olvasásával.

## <a name="overview"></a>Áttekintés
A data factory egy vagy több folyamattal rendelkezhet. A **folyamat** olyan **tevékenységek** logikai csoportosítása, amelyek együttesen végeznek feladatokat. A folyamat tevékenységei meghatározzák az adatokon végrehajtandó műveleteket. Előfordulhat például, hogy egy másolási tevékenységgel másol egy SQL Server adatbázis adatait az Azure Blob Storage-ba. Ezt követően felhasználhat egy kaptár-műveletet, amely egy Azure HDInsight-fürtön futó struktúra-parancsfájlt futtat a blob Storage-beli adatok feldolgozásához a kimeneti adatok előállításához. Végezetül pedig használhat egy második másolási tevékenységet, amellyel a kimeneti adatokat az Azure szinapszis Analyticsbe másolhatja, amelyeken az üzleti intelligencia (BI) jelentéskészítési megoldásai épülnek. A folyamatokkal és tevékenységekkel kapcsolatos további információkért lásd: [folyamatok és tevékenységek Azure Data Factoryban](data-factory-create-pipelines.md).

Egy tevékenység nulla vagy több bemeneti **adatkészletet** is igénybe vehet, és egy vagy több kimeneti adatkészletet hoz létre. A bemeneti adatkészlet a folyamat egy tevékenységének bemenetét jelöli, a kimeneti adatkészlet pedig a tevékenység kimenetét jelöli. Az adatkészletek adatokat határoznak meg a különböző adattárakban, például táblákban, fájlokban, mappákban és dokumentumokban. Az Azure Blob-adatkészlet például meghatározza a blob-tárolóban található BLOB-tárolót és mappát, amelyből a folyamat beolvassa az adatokat.

Adatkészlet létrehozása előtt hozzon létre egy **társított szolgáltatást** , amely az adattárat az adat-előállítóhoz kapcsolja. A társított szolgáltatások nagyon hasonlóak a kapcsolati sztringekhoz, amelyek meghatározzák azokat a kapcsolati információkat, amelyeket a Data Factory a külső erőforrásokhoz történő csatlakozáshoz igényel. Az adathalmazok azonosítják a társított adattárakon belüli adatokat, például az SQL-táblákat, a fájlokat, a mappákat és a dokumentumokat. Egy Azure Storage-beli társított szolgáltatás például egy Storage-fiókhoz csatolja az adatelőállítót. Az Azure Blob-adatkészlet a BLOB-tárolót és a feldolgozandó bemeneti blobokat tartalmazó mappát jelöli.

Példa erre a forgatókönyvre. Az adatok blob Storage-ból SQL Databaseba való másolásához létre kell hoznia két társított szolgáltatást: Azure Storage és Azure SQL Database. Ezután hozzon létre két adatkészletet: az Azure Blob-adatkészletet (amely az Azure Storage társított szolgáltatásra vonatkozik) és az Azure SQL Table-adatkészletet (amely a Azure SQL Database társított szolgáltatásra hivatkozik). Az Azure Storage és a Azure SQL Database társított szolgáltatások olyan kapcsolati karakterláncokat tartalmaznak, amelyeket a futtatókörnyezet az Azure Storage-hoz és a Azure SQL Databasehoz való kapcsolódáshoz Data Factory használ. Az Azure Blob-adatkészlet meghatározza a blob-tárolóban található bemeneti blobokat tartalmazó BLOB-tárolót és blob mappát. Az Azure SQL Table adatkészlet meghatározza azt az SQL-táblázatot az SQL-adatbázisban, amelybe az adatokat másolni kívánja.

A következő ábra a folyamat, a tevékenység, az adatkészlet és a társított szolgáltatás közötti kapcsolatokat mutatja Data Factoryban:

![Kapcsolat a folyamat, a tevékenység, az adatkészlet és a társított szolgáltatások között](media/data-factory-create-datasets/relationship-between-data-factory-entities.png)

## <a name="dataset-json"></a>Adatkészlet JSON
A Data Factoryban található adatkészlet JSON formátumban van definiálva, a következőképpen:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "external": "<boolean flag to indicate external data. only for input datasets>",
        "linkedServiceName": "<Name of the linked service that refers to a data store.>",
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        },
        "availability": {
            "frequency": "<Specifies the time unit for data slice production. Supported frequency: Minute, Hour, Day, Week, Month>",
            "interval": "<Specifies the interval within the defined frequency. For example, frequency set to 'Hour' and interval set to 1 indicates that new data slices should be produced hourly>"
        },
        "policy":
        {
        }
    }
}
```

A fenti JSON-tulajdonságokat a következő táblázat ismerteti:

| Tulajdonság | Leírás | Kötelező | Alapértelmezett |
| --- | --- | --- | --- |
| name |Az adatkészlet neve. Lásd: [Azure Data Factory-elnevezési szabályok](data-factory-naming-rules.md) az elnevezési szabályokhoz. |Yes |NA |
| típus |Az adatkészlet típusa. A Data Factory által támogatott típusok egyikét kell megadnia (például: AzureBlob, tulajdonsága azuresqltable). <br/><br/>Részletekért lásd: [adatkészlet típusa](#Type). |Yes |NA |
| szerkezet |Az adatkészlet sémája.<br/><br/>Részletekért lásd az [adatkészlet szerkezetét](#Structure)ismertető témakört. |No |NA |
| typeProperties | A típus tulajdonságai eltérőek az egyes típusoknál (például: Azure Blob, Azure SQL Table). A támogatott típusokkal és azok tulajdonságaival kapcsolatos részletekért lásd: [adatkészlet típusa](#Type). |Yes |NA |
| external | Logikai jelző annak megadásához, hogy az adatkészletet explicit módon állították-e be egy adat-előállító folyamat vagy sem. Ha egy tevékenység bemeneti adatkészletét nem az aktuális folyamat állítja elő, a jelzőt állítsa igaz értékre. Ezt a jelzőt állítsa igaz értékre a folyamat első tevékenységének bemeneti adatkészletében.  |No |hamis |
| availability | Meghatározza a feldolgozási időszakot (például óránként vagy naponta), vagy az adatkészlet-termelés szeletelő modelljét. A tevékenység-Futtatás által felhasznált és előállított adategységeket adatszeletnek nevezzük. Ha a kimeneti adatkészlet rendelkezésre állása napi (Frequency, Interval-1) értékre van beállítva, a szeletek naponta készülnek. <br/><br/>Részletekért lásd: adatkészlet rendelkezésre állása. <br/><br/>Az adatkészlet szeletelő modelljével kapcsolatos részletekért tekintse meg az [Ütemezés és végrehajtás](data-factory-scheduling-and-execution.md) című cikket. |Yes |NA |
| szabályzat |Meghatározza azokat a feltételeket vagy feltételt, amelyeknek az adatkészlet-szeleteknek teljesíteniük kell. <br/><br/>Részletekért tekintse meg az [adatkészlet-házirend](#Policy) szakaszt. |No |NA |

## <a name="dataset-example"></a>Adatkészlet – példa
Az alábbi példában az adatkészlet egy **sajáttábla** nevű táblázatot JELÖL egy SQL-adatbázisban.

```json
{
    "name": "DatasetSample",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties":
        {
            "tableName": "MyTable"
        },
        "availability":
        {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Vegye figyelembe a következő szempontokat:

* a **típus** értéke tulajdonsága azuresqltable.
* a **Táblanév** Type tulajdonsága (amely a tulajdonsága azuresqltable típusra vonatkozik) a sajáttábla értékre van beállítva.
* a **linkedServiceName** egy AzureSqlDatabase típusú társított szolgáltatásra hivatkozik, amely a következő JSON-kódrészletben van meghatározva.
* a **rendelkezésre állási gyakoriság** beállítása nap, az **intervallum** értéke pedig 1. Ez azt jelenti, hogy az adatkészlet szelete naponta készül.

A **AzureSqlLinkedService** a következőképpen van definiálva:

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>@<servername>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
        }
    }
}
```

Az előző JSON-kódrészletben:

* a **típus** értéke AzureSqlDatabase.
* a **ConnectionString** Type tulajdonság az SQL Database-hez való kapcsolódáshoz szükséges adatokat adja meg.

Amint láthatja, a társított szolgáltatás határozza meg, hogyan csatlakozhat egy SQL-adatbázishoz. Az adatkészlet határozza meg, hogy a rendszer milyen táblát használ bemenetként és kimenetként egy folyamat tevékenységében.

> [!IMPORTANT]
> Ha a folyamat nem állít elő adatkészletet, akkor azt **külsőnek** kell megjelölni. Ez a beállítás általában a folyamat első tevékenységének bemenetére vonatkozik.

## <a name="dataset-type"></a><a name="Type"></a> Adatkészlet típusa
Az adatkészlet típusa a használt adattártól függ. A Data Factory által támogatott adattárak listáját a következő táblázat tartalmazza. Kattintson egy adattárra, és Ismerje meg, hogyan hozhat létre egy társított szolgáltatást és egy adatkészletet az adott adattárhoz.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> A *-ban tárolt adattárak a helyszínen vagy az Azure-infrastruktúra szolgáltatásként (IaaS) is lehetnek. Ezek az adattárak a [adatkezelés átjáró](data-factory-data-management-gateway.md)telepítéséhez szükségesek.

Az előző szakaszban szereplő példában az adatkészlet típusa **tulajdonsága azuresqltable** értékre van állítva. Hasonlóképpen, az Azure Blob-adatkészletek esetében az adatkészlet típusa **AzureBlob** értékre van állítva, ahogy az a következő JSON-ban látható:

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```

## <a name="dataset-structure"></a><a name="Structure"></a>Adatkészlet szerkezete
A **struktúra** szakasz nem kötelező. Meghatározza az adatkészlet sémáját úgy, hogy az oszlopok neveinek és adattípusának gyűjteményét tartalmazza. A struktúra szakasz használatával megadhatja a típusokat és a leképezési oszlopokat a forrásról a célhelyre. A következő példában az adatkészlet három oszloppal rendelkezik: `slicetimestamp` , `projectname` és `pageviews` . Karakterlánc, karakterlánc és decimális típusúak.

```json
structure:
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

A struktúra minden oszlopa a következő tulajdonságokat tartalmazza:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| name |Az oszlop neve. |Yes |
| típus |Az oszlop adattípusa.  |No |
| kulturális környezet |. A .NET-típus használata esetén használandó, NET-alapú kulturális környezet: `Datetime` vagy `Datetimeoffset` . A mező alapértelmezett értéke: `en-us`. |No |
| formátumban |A típus .NET-típusú típusaként használandó formázó karakterlánc: `Datetime` vagy `Datetimeoffset` . |No |

A következő irányelvek segítenek meghatározni, hogy mikor kell belefoglalni a szerkezeti adatokat, és mit kell belefoglalni a **struktúra** szakaszba.

* **Strukturált adatforrások** esetén csak akkor válassza a struktúra szakaszt, ha a forrás oszlopokat a fogadó oszlopokra szeretné leképezni, és a nevük nem egyeznek. Az ilyen strukturált adatforrás az adatsémát és a beírási adatokat az adatok mellett tárolja. Strukturált adatforrások például a következők: SQL Server, Oracle és Azure table.
  
    A strukturált adatforrásokhoz már rendelkezésre áll a típus adatai, ezért a szerkezet szakasza nem tartalmazhat beírási adatokat.
* **Az olvasási adatforrások (konkrétan blob Storage) sémája esetében** dönthet úgy, hogy az adatokat bármilyen séma vagy típus adatainak tárolása nélkül tárolja. Ilyen típusú adatforrások esetén olyan struktúrát kell használni, amikor a forrás oszlopokat a fogadó oszlopokra szeretné leképezni. Olyan struktúrát is magában foglal, amelyben az adatkészlet egy másolási tevékenység bemenete, és a forrás-adatkészlet adattípusait át kell alakítani a fogadó natív típusaira.
    
    A Data Factory a következő értékeket támogatja a típusú információk megadásához a struktúrában: **Int16, Int32, Int64, Single, Double, decimális, byte [], Boolean, string, GUID, datetime, DateTimeOffset és TimeSpan**. Ezek az értékek Common Language Specification (CLS)-kompatibilisek. NET-alapú típusú értékek.

Data Factory automatikusan végrehajtja a típus-konverziókat, amikor a forrás adattárból egy fogadó adattárba helyezi át az adatátvitelt.

## <a name="dataset-availability"></a>Adatkészlet rendelkezésre állása
Az adatkészlet **rendelkezésre állási** szakasza meghatározza a feldolgozási időszakot (például óránként, naponta vagy hetente) az adatkészlethez. További információ a tevékenységek Windowsról: [Ütemezés és végrehajtás](data-factory-scheduling-and-execution.md).

A következő rendelkezésre állási szakasz azt határozza meg, hogy a kimeneti adatkészlet vagy óránként jön létre, vagy a bemeneti adatkészlet óránként elérhető:

```json
"availability":
{
    "frequency": "Hour",
    "interval": 1
}
```

Ha a folyamat a következő kezdési és befejezési időpontokkal rendelkezik:

```json
    "start": "2016-08-25T00:00:00Z",
    "end": "2016-08-25T05:00:00Z",
```

A kimeneti adatkészlet óránként jön létre a folyamat kezdési és befejezési időpontjain belül. Ezért a folyamat öt adatkészlet-szeletet állít elő, egyet az egyes tevékenységek ablakokhoz (12 – 1, 1 – 2, 2, 3, 3 – 4, 4 – 5).

A következő táblázat a rendelkezésre állási szakaszban használható tulajdonságokat ismerteti:

| Tulajdonság | Leírás | Kötelező | Alapértelmezett |
| --- | --- | --- | --- |
| frequency |Megadja az adatkészlet-szelet gyártásának időegységét.<br/><br/><b>Támogatott gyakoriság</b>: perc, óra, nap, hét, hónap |Yes |NA |
| interval |A gyakoriság szorzóját adja meg.<br/><br/>A "Frequency x Interval" érték határozza meg, hogy milyen gyakran történjen a szelet előállítása. Ha például az adatkészletet óránként kell darabolni, a <b>gyakoriságot</b> <b>óra</b>értékre kell beállítani, és az <b>intervallumot</b> <b>1-re</b>kell állítania.<br/><br/>Vegye figyelembe, hogy ha a **gyakoriságot** **percben** adja meg, az intervallumot 15-nél nem kisebbre kell beállítani. |Yes |NA |
| stílus |Megadja, hogy a szelet az intervallum elején vagy végén készüljön-e.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul>Ha a **gyakoriság** értéke **hónap**, és a **Style** értéke **EndOfInterval**, a szelet a hónap utolsó napján jön létre. Ha a **stílus** értéke **StartOfInterval**, a szelet a hónap első napján jön létre.<br/><br/>Ha a **gyakoriság** beállítása **nap**, és a **Style** értéke **EndOfInterval**, a szelet a nap utolsó órájában jön létre.<br/><br/>Ha a **gyakoriság** értéke **óra**, és a **Style** értéke **EndOfInterval**, a szelet az óra végén jön létre. Például az 1 PM-2 PM-időszakhoz tartozó szeletek esetében a SZELET 2 ÓRAKOR jön létre. |No |EndOfInterval |
| anchorDateTime |Az ütemező által az adatkészlet-szeletek határainak kiszámításához használt abszolút időpontot határozza meg. <br/><br/>Vegye figyelembe, hogy ha ez a tulajdonság a megadott gyakoriságnál részletesebb részletességi részeket tartalmaz, a rendszer figyelmen kívül hagyja a részletesebb részeket. Ha például az intervallum **óránként** (Frequency: Hour és Interval: 1), a **anchorDateTime** pedig **perc és másodperc** **értéket** tartalmaz, a rendszer figyelmen kívül hagyja a **anchorDateTime** perc és másodperc részeit. |No |01/01/0001 |
| offset |TimeSpan, amely az összes adatkészlet összes szeletének kezdetét és végét eltolja. <br/><br/>Vegye figyelembe, hogy ha a **anchorDateTime** és az **eltolás** is meg van adva, az eredmény a kombinált eltolás. |No |NA |

### <a name="offset-example"></a>eltolási példa
Alapértelmezés szerint a napi ( `"frequency": "Day", "interval": 1` ) szeletek az egyezményes világidő (UTC) szerint kezdődnek: 12 órakor (éjfélkor). Ha a kezdési időpontot 6 UTC-időre szeretné használni, állítsa be az eltolást az alábbi kódrészletben látható módon:

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>anchorDateTime példa
A következő példában az adatkészlet 23 óránként egyszer jön létre. Az első szelet a **anchorDateTime** által megadott időpontban kezdődik, amely a következőre van beállítva: `2017-04-19T08:00:00` (UTC).

```json
"availability":
{
    "frequency": "Hour",
    "interval": 23,
    "anchorDateTime":"2017-04-19T08:00:00"
}
```

### <a name="offsetstyle-example"></a>eltolás/stílus – példa
A következő adatkészlet havonta történik, és minden hónap harmadik napján, 8:00-kor () jön létre `3.08:00:00` :

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

## <a name="dataset-policy"></a><a name="Policy"></a>Adatkészlet-házirend
Az adatkészlet definíciójának **szabályzat** szakasza határozza meg azokat a feltételeket vagy feltételt, amelyeknek az adatkészlet-szeleteknek teljesíteniük kell.

### <a name="validation-policies"></a>Ellenőrzési házirendek
| Házirend neve | Description | Alkalmazva erre | Kötelező | Alapértelmezett |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Ellenőrzi, hogy az **Azure Blob Storage** -ban tárolt adat megfelel-e a minimális méretre vonatkozó követelményeknek (megabájtban). |Azure Blob Storage |No |NA |
| minimumRows |Ellenőrzi, hogy egy **Azure SQL Database-adatbázisban** vagy egy **Azure-táblában** lévő összes érték tartalmazza-e a sorok minimális számát. |<ul><li>Azure SQL Database</li><li>Azure-tábla</li></ul> |No |NA |

#### <a name="examples"></a>Példák
**minimumSizeMB:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

**minimumRows:**

```json
"policy":
{
    "validation":
    {
        "minimumRows": 100
    }
}
```

### <a name="external-datasets"></a>Külső adatkészletek
Azokat a külső adatkészleteket, amelyeket nem az adat-előállító futó folyamata állít elő. Ha az adatkészlet **külsőként** van megjelölve, a **ExternalData** házirend úgy definiálható, hogy befolyásolja az adatkészlet-szelet rendelkezésre állásának viselkedését.

Ha Data Factory nem állít elő adatkészletet, akkor azt **külsőnek** kell megjelölni. Ez a beállítás általában a folyamat első tevékenységének bemenetére vonatkozik, kivéve, ha a tevékenység vagy a folyamat láncolására kerül sor.

| Név | Leírás | Kötelező | Alapértelmezett érték |
| --- | --- | --- | --- |
| dataDelay |Az adott szelet külső adatának rendelkezésre állására vonatkozó ellenőrzések késleltetésének ideje. A beállítással például késleltetheti az óránkénti ellenőrzéseket.<br/><br/>A beállítás csak a jelen időpontra érvényes. Ha például ez a 1:00 PM, és ez az érték 10 perc, az érvényesítés 1:10 ÓRAKOR kezdődik.<br/><br/>Vegye figyelembe, hogy ez a beállítás nem érinti a szeleteket a múltban. A szeletek dataDelay- **befejezési idejét** tartalmazó szeletek  +    <   késleltetés nélkül lesznek feldolgozva.<br/><br/>23:59 óránál nagyobb időt kell megadni a `day.hours:minutes:seconds` formátum használatával. Ha például 24 órát szeretne megadni, ne használja a 24:00:00-et. Ehelyett használja az 1.00:00:00 értéket. Ha 24:00:00-et használ, azt 24 nap (24.00:00:00) kezeli. 1 és 4 óra esetén a 1:04:00:00-es megadását kell megadnia. |No |0 |
| retryInterval |A hiba és a következő próbálkozás közötti várakozási idő. Ez a beállítás a jelenlegi időpontra vonatkozik. Ha az előző próbálkozás sikertelen volt, a következő próbálkozás a **retryInterval** időszak után következik be. <br/><br/>Ha jelenleg 1:00 PM, kezdjük az első próbálkozással. Ha az első érvényesítési ellenőrzés befejezésének időtartama 1 perc, és a művelet meghiúsult, a következő újrapróbálkozás a 1:00 + 1min (időtartam) + 1min (újrapróbálkozás időköze) = 1:02 PM címen érhető el. <br/><br/>A múltban a szeletek esetében nincs késés. Az újrapróbálkozás azonnal megtörténik. |No |00:01:00 (1 perc) |
| retryTimeout |Az újrapróbálkozási kísérletek időtúllépése.<br/><br/>Ha ez a tulajdonság 10 percre van beállítva, az érvényesítést 10 percen belül el kell végezni. Ha az ellenőrzés elvégzése 10 percnél hosszabb időt vesz igénybe, az újrapróbálkozások időtúllépést jelentenek.<br/><br/>Ha a rendszer minden alkalommal megkísérli az érvényesítést, a szelet **időtúllépés** lesz megjelölve. |No |00:10:00 (10 perc) |
| maximumRetry |A külső adatforrások rendelkezésre állását megkereső időpontok száma. A maximálisan megengedett érték 10. |No |3 |


## <a name="create-datasets"></a>Adatkészletek létrehozása
Adatkészleteket az alábbi eszközök vagy SDK-k egyikével hozhat létre:

- Másolás varázsló
- Visual Studio
- PowerShell
- Azure Resource Manager-sablon
- REST API
- .NET API

A következő oktatóanyagok részletes útmutatást biztosítanak a folyamatok és adatkészletek létrehozásához az alábbi eszközök vagy SDK-k egyikével:

- [Adatátalakítási tevékenységgel rendelkező folyamat létrehozása](data-factory-build-your-first-pipeline.md)
- [Adatátviteli tevékenységgel rendelkező folyamat létrehozása](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

A folyamat létrehozása és üzembe helyezése után a folyamatokat a Azure Portal-pengék, illetve a figyelési és felügyeleti alkalmazás használatával kezelheti és figyelheti. Részletes útmutatásért tekintse meg a következő témaköröket:

- [Folyamatok monitorozása és kezelése Azure Portal pengék használatával](data-factory-monitor-manage-pipelines.md)
- [Folyamatok monitorozása és kezelése a figyelési és felügyeleti alkalmazás használatával](data-factory-monitor-manage-app.md)

## <a name="scoped-datasets"></a>Hatókörrel rendelkező adatkészletek
Az **adatkészletek** tulajdonság használatával olyan adatkészleteket hozhat létre, amelyek hatóköre egy folyamatra terjed ki. Ezeket az adatkészleteket csak a folyamaton belüli tevékenységek használhatják, nem pedig más folyamatok tevékenységei. A következő példa egy folyamatot definiál két adatkészlettel (InputDataset-RDC és OutputDataset-RDC), amelyet a folyamaton belül szeretne használni.

> [!IMPORTANT]
> A hatókörrel rendelkező adatkészletek csak egyszeri folyamatokkal támogatottak (ahol a **pipelineMode** az **egyszeri** bejelentkezésre van állítva). Részletekért lásd: az [egykori folyamat](data-factory-create-pipelines.md#onetime-pipeline) .
>
>

```json
{
    "name": "CopyPipeline-rdc",
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
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataset-rdc"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataset-rdc"
                    }
                ],
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1,
                    "style": "StartOfInterval"
                },
                "name": "CopyActivity-0"
            }
        ],
        "start": "2016-02-28T00:00:00Z",
        "end": "2016-02-28T00:00:00Z",
        "isPaused": false,
        "pipelineMode": "OneTime",
        "expirationTime": "15.00:00:00",
        "datasets": [
            {
                "name": "InputDataset-rdc",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "InputLinkedService-rdc",
                    "typeProperties": {
                        "fileName": "emp.txt",
                        "folderPath": "adftutorial/input",
                        "format": {
                            "type": "TextFormat",
                            "rowDelimiter": "\n",
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
            },
            {
                "name": "OutputDataset-rdc",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "OutputLinkedService-rdc",
                    "typeProperties": {
                        "fileName": "emp.txt",
                        "folderPath": "adftutorial/output",
                        "format": {
                            "type": "TextFormat",
                            "rowDelimiter": "\n",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "external": false,
                    "policy": {}
                }
            }
        ]
    }
}
```

## <a name="next-steps"></a>Következő lépések
- A folyamatokkal kapcsolatos további információkért lásd: [folyamatok létrehozása](data-factory-create-pipelines.md).
- További információ a folyamatok ütemezéséről és végrehajtásáról: [Ütemezés és végrehajtás Azure Data Factoryban](data-factory-scheduling-and-execution.md).
