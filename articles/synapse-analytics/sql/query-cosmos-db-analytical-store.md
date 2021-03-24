---
title: AdatAzure Cosmos DB lekérdezése kiszolgáló nélküli SQL-készlettel az Azure szinapszis-hivatkozás használatával
description: Ebből a cikkből megtudhatja, hogyan kérdezheti le Azure Cosmos DB egy kiszolgáló nélküli SQL-készlet használatával az Azure szinapszis-hivatkozásban.
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 03/02/2021
ms.author: jovanpop
ms.reviewer: jrasnick
ms.custom: cosmos-db
ms.openlocfilehash: 10262b168b91370956c9559ba688c72213ba7618
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104870993"
---
# <a name="query-azure-cosmos-db-data-with-a-serverless-sql-pool-in-azure-synapse-link"></a>Kiszolgáló nélküli SQL-készlettel rendelkező lekérdezés Azure Cosmos DB az Azure-beli szinapszis-hivatkozással

A kiszolgáló nélküli SQL-készletek lehetővé teszik az [Azure szinapszis-hivatkozással](../../cosmos-db/synapse-link.md) rendelkező Azure Cosmos db tárolókban lévő adatok elemzését, a tranzakciós feladatok teljesítményének befolyásolása nélkül. Jól ismert T-SQL-szintaxist biztosít az [analitikus áruházból](../../cosmos-db/analytical-store-introduction.md) származó adatok lekérdezéséhez és az üzleti intelligenciával (BI) és az alkalmi lekérdezési eszközökhöz való integrált kapcsolódáshoz a t-SQL felületen keresztül.

Azure Cosmos DB lekérdezéséhez a [OpenRowset](develop-openrowset.md) függvény a teljes [kijelölés](/sql/t-sql/queries/select-transact-sql?view=azure-sqldw-latest&preserve-view=true) felületét támogatja, amely az [SQL-függvények és operátorok](overview-features.md)többségét tartalmazza. A lekérdezés eredményeit olyan módon is tárolhatja, amely Azure Cosmos DB adatokat olvas be az Azure Blob Storage vagy Azure Data Lake Storage használatával, [külső tábla létrehozása Select](develop-tables-cetas.md#cetas-in-serverless-sql-pool) (CETAS) néven. A kiszolgáló nélküli SQL-készlet lekérdezési eredményei jelenleg nem tárolhatók a CETAS használatával Azure Cosmos DB.

Ebből a cikkből megtudhatja, hogyan írhat lekérdezést olyan kiszolgáló nélküli SQL-készlettel, amely az Azure szinapszis-hivatkozással engedélyezett Azure Cosmos DB tárolóból származó adatok lekérdezésére szolgál. Ezután további információt olvashat a kiszolgáló nélküli SQL-készlet nézeteinek létrehozásáról Azure Cosmos DB tárolók között, és összekapcsolhatja őket az [oktatóanyag](./tutorial-data-analyst.md)Power bi modelljeivel. Ez az oktatóanyag egy [Azure Cosmos db jól definiált sémával](../../cosmos-db/analytical-store-introduction.md#schema-representation)rendelkező tárolót használ.

## <a name="overview"></a>Áttekintés

A kiszolgáló nélküli SQL-készlet lehetővé teszi Azure Cosmos DB analitikai tár lekérdezését a `OPENROWSET` függvény használatával. 
- `OPENROWSET` beágyazott kulccsal. Ez a szintaxis Azure Cosmos DB gyűjtemények lekérdezésére használható a hitelesítő adatok előkészítése nélkül.
- `OPENROWSET` az Cosmos DB-fiók kulcsát tartalmazó hivatkozott hitelesítő adat. Ez a szintaxis Azure Cosmos DB gyűjtemények nézeteinek létrehozására használható.

### <a name="openrowset-with-key"></a>[OPENROWSET kulccsal](#tab/openrowset-key)

Egy Azure Cosmos DB analitikus tárolóban lévő adatok lekérdezéséhez és elemzéséhez a kiszolgáló nélküli SQL-készlet a következő `OPENROWSET` szintaxist használja:

```sql
OPENROWSET( 
       'CosmosDB',
       '<Azure Cosmos DB connection string>',
       <Container name>
    )  [ < with clause > ] AS alias
```

A Azure Cosmos DB kapcsolódási karakterlánc megadja az Azure Cosmos DB fiók nevét, az adatbázis nevét, az adatbázis-fiók főkulcsát, valamint a függvény választható régiójának nevét `OPENROWSET` .

A kapcsolatok karakterláncának formátuma a következő:
```sql
'account=<database account name>;database=<database name>;region=<region name>;key=<database account master key>'
```

A Azure Cosmos DB tároló neve idézőjelek nélkül van megadva a `OPENROWSET` szintaxisban. Ha a tároló neve speciális karaktereket tartalmaz, például egy kötőjelet (-), a nevet szögletes zárójelbe () kell becsomagolni `[]` a `OPENROWSET` szintaxisban.

### <a name="openrowset-with-credential"></a>[OPENROWSET hitelesítő adatokkal](#tab/openrowset-credential)

Használhatja `OPENROWSET` a hitelesítő adatokra hivatkozó szintaxist:

```sql
OPENROWSET( 
       PROVIDER = 'CosmosDB',
       CONNECTION = '<Azure Cosmos DB connection string without account key>',
       OBJECT = '<Container name>',
       [ CREDENTIAL | SERVER_CREDENTIAL ] = '<credential name>'
    )  [ < with clause > ] AS alias
```

A Azure Cosmos DB-kapcsolatok karakterlánca ebben az esetben nem tartalmaz kulcsot. A kapcsolatok karakterláncának formátuma a következő:
```sql
'account=<database account name>;database=<database name>;region=<region name>'
```

Az adatbázis-fiók főkulcsa kiszolgálói szintű hitelesítő adatok vagy adatbázis-hatókörű hitelesítő adatokba kerül. 

---

> [!IMPORTANT]
> Győződjön meg arról, hogy UTF-8 adatbázis-rendezést használ, például mert a `Latin1_General_100_CI_AS_SC_UTF8` Azure Cosmos db analitikus tárolóban lévő karakterlánc-értékek UTF-8 szövegként vannak kódolva.
> A fájl és a rendezés szöveges kódolása nem egyezik meg a szöveges konvertálás során fellépő hibák miatt.
> A T-SQL-utasítás használatával egyszerűen módosíthatja az aktuális adatbázis alapértelmezett rendezését `alter database current collate Latin1_General_100_CI_AI_SC_UTF8` .

> [!NOTE]
> Egy kiszolgáló nélküli SQL-készlet nem támogatja Azure Cosmos DB tranzakciós tárolók lekérdezését.

## <a name="sample-dataset"></a>Minta adatkészlet

A cikkben szereplő példák az [Európai Betegségmegelőzési és Járványvédelmi Központ (ECDC) COVID-19 esetek](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) és a [COVID-19 nyílt kutatási adatkészlet (Cord-19), Doi: 10.5281/zenodo. 3715505](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/)adatokon alapulnak.

Ezen lapokon láthatja a licencet és az adatszerkezetet. Az [ECDC](https://pandemicdatalake.blob.core.windows.net/public/curated/covid-19/ecdc_cases/latest/ecdc_cases.json) és a [Cord-19](https://azureopendatastorage.blob.core.windows.net/covid19temp/comm_use_subset/pdf_json/000b7d1517ceebb34e1e3e817695b6de03e2fa78.json) adatkészletek mintaadatok is letölthetők.

Ahhoz, hogy követni tudja, hogyan lehet lekérdezni Azure Cosmos DB-adatbázisokat egy kiszolgáló nélküli SQL-készlettel, győződjön meg arról, hogy a következő erőforrásokat hozza létre:

* Egy Azure Cosmos DB adatbázis-fiók, amelyen [engedélyezve van az Azure szinapszis-hivatkozás](../../cosmos-db/configure-synapse-link.md).
* Egy nevű Azure Cosmos DB adatbázis `covid` .
* Két Azure Cosmos DB tároló, `Ecdc` `Cord19` amely az előző minta adatkészletekkel együtt lett megnevezve és betöltve.

Tesztelési célra a következő kapcsolatok karakterláncot használhatja: `Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==` . Vegye figyelembe, hogy ez a kapcsolódás nem garantálja a teljesítményt, mivel ez a fiók a szinapszis SQL-végponthoz képest a távoli régióban található.

## <a name="explore-azure-cosmos-db-data-with-automatic-schema-inference"></a>Ismerkedjen meg Azure Cosmos DBával az automatikus séma-következtetéssel

A Azure Cosmos DBban lévő adatvizsgálatok legegyszerűbb módja az automatikus séma-következtetési lehetőség használata. Ha kihagyja a `WITH` záradékot az `OPENROWSET` utasításból, utasíthatja a kiszolgáló nélküli SQL-készletet a Azure Cosmos db tároló analitikai tárolójának sémájának automatikus észlelésére (következtetésre).

### <a name="openrowset-with-key"></a>[OPENROWSET kulccsal](#tab/openrowset-key)

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Ecdc) as documents
```

### <a name="openrowset-with-credential"></a>[OPENROWSET hitelesítő adatokkal](#tab/openrowset-credential)

```sql
/*  Setup - create server-level or database scoped credential with Azure Cosmos DB account key:
    CREATE CREDENTIAL MyCosmosDbAccountCredential
    WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
*/
SELECT TOP 10 *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      SERVER_CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

---

Az előző példában arra utasította a kiszolgáló nélküli SQL-készletet, hogy a `covid` Azure Cosmos db kulccsal hitelesített Azure Cosmos db fiókban található adatbázishoz kapcsolódjon `MyCosmosDbAccount` (az előző példában szereplő dummy). Ezután elérjük a `Ecdc` tároló analitikus tárolóját a `West US 2` régióban. Mivel a tulajdonságok nem rendelkeznek kivetítéssel, a `OPENROWSET` függvény a Azure Cosmos db elemek összes tulajdonságát visszaküldi.

Feltételezve, hogy a Azure Cosmos DB tárolóban található elemek a `date_rep` `cases` , a és a `geo_id` tulajdonsággal rendelkeznek, a lekérdezés eredményei a következő táblázatban láthatók:

| date_rep | esetekben | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

Ha más tárolóból származó adatokkal kell megvizsgálnia ugyanabban a Azure Cosmos DB-adatbázisban, használhatja ugyanazt a kapcsolódási karakterláncot, és a szükséges tárolóra hivatkozhat harmadik paraméterként:

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Cord19) as cord19
```

## <a name="explicitly-specify-schema"></a>Séma explicit meghatározása

Habár az automatikus séma-következtetési funkció `OPENROWSET` egyszerű és könnyen használható querience biztosít, az üzleti forgatókönyvek esetében előfordulhat, hogy explicit módon meg kell adnia a sémát a csak olvasható tulajdonságok közül a Azure Cosmos db adatokból.

A `OPENROWSET` függvény segítségével explicit módon megadhatja, hogy milyen tulajdonságokat szeretne beolvasni a tárolóban lévő adatokból, és megadhatja az adattípusokat.

Tegyük fel, hogy az [ECDC COVID adatkészletből](https://azure.microsoft.com/services/open-datasets/catalog/ecdc-covid-19-cases/) importáltunk néhány adatát a következő szerkezettel Azure Cosmos DBba:

```json
{"date_rep":"2020-08-13","cases":254,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-12","cases":235,"countries_and_territories":"Serbia","geo_id":"RS"}
{"date_rep":"2020-08-11","cases":163,"countries_and_territories":"Serbia","geo_id":"RS"}
```

Ezek a Azure Cosmos DBban található, a szinapszis SQL-ben található, sorok és oszlopok halmaza használható, egyszerű JSON-dokumentumok. A `OPENROWSET` függvény lehetővé teszi, hogy megadhatja az elolvasni kívánt tulajdonságok egy részhalmazát, valamint a záradékban szereplő pontos oszlop típusait `WITH` :

### <a name="openrowset-with-key"></a>[OPENROWSET kulccsal](#tab/openrowset-key)
```sql
SELECT TOP 10 *
FROM OPENROWSET(
      'CosmosDB',
      'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Ecdc
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```
### <a name="openrowset-with-credential"></a>[OPENROWSET hitelesítő adatokkal](#tab/openrowset-credential)
```sql
/*  Setup - create server-level or database scoped credential with Azure Cosmos DB account key:
    CREATE CREDENTIAL MyCosmosDbAccountCredential
    WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
*/
SELECT TOP 10 *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      SERVER_CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```
---
A lekérdezés eredménye a következő táblázathoz hasonló lehet:

| date_rep | esetekben | geo_id |
| --- | --- | --- |
| 2020-08-13 | 254 | RS |
| 2020-08-12 | 235 | RS |
| 2020-08-11 | 163 | RS |

A Azure Cosmos DB értékekhez használandó SQL-típusokkal kapcsolatos további információkért tekintse meg a cikk végén található [SQL Type-hozzárendelések szabályait](#azure-cosmos-db-to-sql-type-mappings) .

## <a name="create-view"></a>Nézet létrehozása

A mesteralakzatok vagy az alapértelmezett adatbázisok nézeteinek létrehozása nem ajánlott és nem támogatott. Ezért létre kell hoznia egy felhasználói adatbázist a nézetekhez.

Miután azonosította a sémát, előkészítheti a nézetet a Azure Cosmos DB adatain felül. A Azure Cosmos DB-fiók kulcsát külön hitelesítő adatban kell elhelyeznie, és hivatkozni kell erre a hitelesítő adatra a `OPENROWSET` függvényből. Ne tartsa meg a fiók kulcsát a nézet definíciójában.

```sql
CREATE CREDENTIAL MyCosmosDbAccountCredential
WITH IDENTITY = 'SHARED ACCESS SIGNATURE', SECRET = 's5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==';
GO
CREATE OR ALTER VIEW Ecdc
AS SELECT *
FROM OPENROWSET(
      PROVIDER = 'CosmosDB',
      CONNECTION = 'Account=synapselink-cosmosdb-sqlsample;Database=covid',
      OBJECT = 'Ecdc',
      SERVER_CREDENTIAL = 'MyCosmosDbAccountCredential'
    ) with ( date_rep varchar(20), cases bigint, geo_id varchar(6) ) as rows
```

Ne használjon `OPENROWSET` explicit módon definiált sémát, mert ez hatással lehet a teljesítményre. Ügyeljen arra, hogy az oszlopok legkisebb lehetséges méretét használja (például VARCHAR (100) az alapértelmezett VARCHAR (8000) helyett). Az UTF [-8 átalakítási probléma](../troubleshoot/reading-utf8-text.md)elkerülése érdekében használjon néhány UTF-8 rendezést alapértelmezett adatbázis-rendezésként, vagy állítsa explicit oszlop rendezésként. A rendezés `Latin1_General_100_BIN2_UTF8` a legjobb teljesítményt nyújtja, ha a Yu bizonyos karakterlánc-oszlopokkal szűri az adataikat.

## <a name="query-nested-objects-and-arrays"></a>Beágyazott objektumok és tömbök lekérdezése

A Azure Cosmos DB segítségével összetettebb adatmodelleket is megadhat, ha beágyazott objektumként vagy tömbökként készíti el őket. Az Azure szinapszis Azure Cosmos DB-hivatkozás automatikus szinkronizálási funkciója kezeli a séma megjelenítését az analitikai tárolóban, amely magában foglalja a beágyazott adattípusok kezelését, amelyek lehetővé teszik a kiszolgáló nélküli SQL-készletből való széleskörű lekérdezést.

A [kábel-19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) ADATKÉSZLET például JSON-dokumentumokkal rendelkezik, amelyek ezt a struktúrát követik:

```json
{
    "paper_id": <str>,                   # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": <array of objects>    # list of author dicts, in order
        ...
     }
     ...
}
```

A Azure Cosmos DB beágyazott objektumai és tömbök JSON-karakterláncként jelennek meg a lekérdezés eredményében, amikor a `OPENROWSET` függvény beolvassa őket. Az objektumok beágyazott értékeinek elérési útját a záradék használatakor adhatja meg `WITH` :

```sql
SELECT TOP 10 *
FROM OPENROWSET( 
       'CosmosDB',
       'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Cord19)
WITH (  paper_id    varchar(8000),
        title        varchar(1000) '$.metadata.title',
        metadata     varchar(max),
        authors      varchar(max) '$.metadata.authors'
) AS docs;
```

A lekérdezés eredménye a következő táblázathoz hasonló lehet:

| paper_id | cím | metaadatok | szerzők |
| --- | --- | --- |
| bb11206963e831f... | Kiegészítő információk öko-epidemi... | `{"title":"Supplementary Informati…` | `[{"first":"Julien","last":"Mélade","suffix":"","af…`| 
| bb1206963e831f1... | Lábadozó-szérumok használata az immun-E... | `{"title":"The Use of Convalescent…` | `[{"first":"Antonio","last":"Lavazza","suffix":"", …` |
| bb378eca9aac649... | Tylosema esculentum (György) gumó és B... | `{"title":"Tylosema esculentum (Ma…` | `[{"first":"Walter","last":"Chingwaru","suffix":"",…` | 

További információ az [összetett adattípusok elemzéséről az Azure szinapszis-hivatkozásokban](../how-to-analyze-complex-schema.md) és a [beágyazott struktúrákban egy kiszolgáló nélküli SQL-készletben](query-parquet-nested-types.md).

> [!IMPORTANT]
> Ha a szövegben nem várt karakterek jelennek meg, például a `MÃƒÂ©lade` helyett `Mélade` , akkor az adatbázis-rendezés nem [UTF-8](/sql/relational-databases/collations/collation-and-unicode-support#utf8) rendezésre van beállítva.
> [Módosítsa az adatbázis rendezését](/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) UTF-8 rendezésre egy olyan SQL-utasítás használatával, mint például: `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` .

## <a name="flatten-nested-arrays"></a>Beágyazott tömbök összeolvasztása

Azure Cosmos DB adatok lehetnek beágyazott altömbök, például a szerző tömbje egy [kábel-19](https://azure.microsoft.com/services/open-datasets/catalog/covid-19-open-research/) adatkészletből:

```json
{
    "paper_id": <str>,                      # 40-character sha1 of the PDF
    "metadata": {
        "title": <str>,
        "authors": [                        # list of author dicts, in order
            {
                "first": <str>,
                "middle": <list of str>,
                "last": <str>,
                "suffix": <str>,
                "affiliation": <dict>,
                "email": <str>
            },
            ...
        ],
        ...
}
```

Bizonyos esetekben előfordulhat, hogy a felső elemből (metaadatok) a tömb (szerzők) minden elemével "csatlakoztatni kell" a tulajdonságokat. A kiszolgáló nélküli SQL-készletek lehetővé teszik a beágyazott struktúrák összeolvasztását a `OPENJSON` függvény a beágyazott tömbön való alkalmazásával:

```sql
SELECT
    *
FROM
    OPENROWSET(
      'CosmosDB',
      'Account=synapselink-cosmosdb-sqlsample;Database=covid;Key=s5zarR2pT0JWH9k8roipnWxUYBegOuFGjJpSjGlR36y86cW0GQ6RaaG8kGjsRAQoWMw1QKTkkX8HQtFpJjC8Hg==',
       Cord19
    ) WITH ( title varchar(1000) '$.metadata.title',
             authors varchar(max) '$.metadata.authors' ) AS docs
      CROSS APPLY OPENJSON ( authors )
                  WITH (
                       first varchar(50),
                       last varchar(50),
                       affiliation nvarchar(max) as json
                  ) AS a
```

A lekérdezés eredménye a következő táblázathoz hasonló lehet:

| cím | szerzők | első | utolsó | hovatartozás |
| --- | --- | --- | --- | --- |
| Kiegészítő információk öko-epidemi... |   `[{"first":"Julien","last":"Mélade","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Julien | Mélade | `   {"laboratory":"Centre de Recher…` |
Kiegészítő információk öko-epidemi... | `[{"first":"Nicolas","last":"4#","suffix":"","affiliation":{"laboratory":"","institution":"U…` | Nicolas | 4 # |`{"laboratory":"","institution":"U…` | 
| Kiegészítő információk öko-epidemi... |   `[{"first":"Beza","last":"Ramazindrazana","suffix":"","affiliation":{"laboratory":"Centre de Recher…` | Katalin | Ramazindrazana | `{"laboratory":"Centre de Recher…` |
| Kiegészítő információk öko-epidemi... |   `[{"first":"Olivier","last":"Flores","suffix":"","affiliation":{"laboratory":"UMR C53 CIRAD, …` | Olivier | Flores |`{"laboratory":"UMR C53 CIRAD, …` |     

> [!IMPORTANT]
> Ha a szövegben nem várt karakterek jelennek meg, például a `MÃƒÂ©lade` helyett `Mélade` , akkor az adatbázis-rendezés nem [UTF-8](/sql/relational-databases/collations/collation-and-unicode-support#utf8) rendezésre van beállítva. [Módosítsa az adatbázis rendezését](/sql/relational-databases/collations/set-or-change-the-database-collation#to-change-the-database-collation) UTF-8 rendezésre egy olyan SQL-utasítás használatával, mint például: `ALTER DATABASE MyLdw COLLATE LATIN1_GENERAL_100_CI_AS_SC_UTF8` .

## <a name="azure-cosmos-db-to-sql-type-mappings"></a>Azure Cosmos DB az SQL-típusok megfeleltetéséhez

Habár Azure Cosmos DB tranzakciós tároló a séma-agnosztikus, az analitikai tároló sematikus az analitikai lekérdezési teljesítmény optimalizálása érdekében. Az Azure szinapszis-hivatkozás automatikus szinkronizálási funkciójával a Azure Cosmos DB felügyeli a séma megjelenítését az analitikai tárolóban, amely magában foglalja a beágyazott adattípusok kezelését. Mivel a kiszolgáló nélküli SQL-készlet lekérdezi az analitikai tárolót, fontos tisztában lennie azzal, hogyan képezhetők le Azure Cosmos DB bemeneti adattípusok az SQL-adattípusokhoz.

Azure Cosmos DB SQL (Core) API-fiókok esetében a JSON-tulajdonságok száma, karakterlánc, logikai, null, beágyazott objektum vagy tömb. Olyan SQL-típusokat kell választania, amelyek megfelelnek ezeknek a JSON-típusoknak, ha a záradékát használja a alkalmazásban `WITH` `OPENROWSET` . A következő táblázat azokat az SQL-oszlopokat tartalmazza, amelyeket a Azure Cosmos DB különböző típusú tulajdonságaihoz kell használni.

| Azure Cosmos DB tulajdonság típusa | SQL-oszlop típusa |
| --- | --- |
| Logikai | bit |
| Egész szám | bigint |
| Tizedesjegy | float |
| Sztring | varchar (UTF-8 adatbázis-rendezés) |
| Dátum és idő (ISO-formázott karakterlánc) | varchar (30) |
| Dátum és idő (UNIX timestamp) | bigint |
| Null | `any SQL type` 
| Beágyazott objektum vagy tömb | varchar (max) (UTF-8 adatbázis-rendezés), JSON-szövegként szerializálva |

## <a name="full-fidelity-schema"></a>Teljes hűségű séma

Azure Cosmos DB a teljes hűségű séma a tároló összes tulajdonságához az értékeket és a legjobb egyezési típusokat rögzíti. A `OPENROWSET` teljes hűségű sémát tartalmazó tároló függvénye a típust és a tényleges értéket is megadja az egyes cellákban. Tegyük fel, hogy a következő lekérdezés a teljes hűségű sémával rendelkező tároló elemeit olvassa be:

```sql
SELECT *
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Ecdc
    ) as rows
```

A lekérdezés eredménye JSON-szövegként formázott típusokat és értékeket ad vissza:

| date_rep | esetekben | geo_id |
| --- | --- | --- |
| {"date": "2020-08-13"} | {"Int32": "254"} | {"string": "RS"} |
| {"date": "2020-08-12"} | {"Int32": "235"}| {"string": "RS"} |
| {"date": "2020-08-11"} | {"Int32": "316"} | {"string": "RS"} |
| {"date": "2020-08-10"} | {"Int32": "281"} | {"string": "RS"} |
| {"date": "2020-08-09"} | {"Int32": "295"} | {"string": "RS"} |
| {"string": "2020/08/08"} | {"Int32": "312"} | {"string": "RS"} |
| {"date": "2020-08-07"} | {"float64":"339.0"} | {"string": "RS"} |

Minden értéknél megtekintheti az Azure Cosmos DB tároló elemben azonosított típust. A tulajdonsághoz tartozó értékek többsége `date_rep` `date` értékeket tartalmaz, de egyesek helytelenül vannak tárolva karakterláncként Azure Cosmos db. A teljes hűségű séma a helyesen beírt `date` értékeket és a helytelenül formázott értékeket is visszaküldi `string` .
Az esetek száma az `int32` értékként tárolt információ, de egy érték decimális számként van megadva. Az érték típusa a `float64` következő:. Ha vannak olyan értékek, amelyek meghaladják a legnagyobb `int32` számot, a rendszer a `int64` típusként tárolja őket. A `geo_id` példában szereplő összes érték típusaként van tárolva `string` .

> [!IMPORTANT]
> A `OPENROWSET` záradék nélküli függvény `WITH` mindkét értéket a várt típusokkal és a helytelenül beírt típusokkal rendelkező értékekkel teszi elérhetővé. Ez a függvény adatfeltárásra szolgál, nem pedig jelentéskészítéshez. Ne elemezze a függvény által visszaadott JSON-értékeket a jelentések létrehozásához. Jelentések létrehozásához használjon explicit [with záradékot](#query-items-with-full-fidelity-schema) . Az Azure Cosmos DB tárolóban helytelen típusú értékeket kell megtisztítani a teljes hűségű analitikus tárolóban a javítások alkalmazásához.

Ha le kell kérdezni Azure Cosmos DB fiókokat a Mongo DB API-típusból, többet is megtudhat az analitikai tárolóban lévő teljes megbízhatósági sémáról, valamint a [mi Azure Cosmos db analitikus tárolóban](../../cosmos-db/analytical-store-introduction.md#analytical-schema)használandó bővített tulajdonságnév használatáról?.

### <a name="query-items-with-full-fidelity-schema"></a>Teljes megbízhatósági sémával rendelkező elemek lekérdezése

A teljes hűségű séma lekérdezése során explicit módon meg kell adnia az SQL-típust és a várt Azure Cosmos DB tulajdonság típusát a `WITH` záradékban.

Az alábbi példában feltételezzük, hogy a `string` tulajdonság megfelelő típusa, `geo_id` és a tulajdonság típusa a következő `int32` `cases` :

```sql
SELECT geo_id, cases = SUM(cases)
FROM OPENROWSET(
      'CosmosDB'
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Ecdc
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string',
             cases INT '$.cases.int32'
    ) as rows
GROUP BY geo_id
```

A `geo_id` és `cases` más típusokkal rendelkező értékek értékként lesznek visszaadva `NULL` . Ez a lekérdezés csak a `cases` () kifejezésben megadott típusra hivatkozik `cases.int32` .

Ha olyan értékekkel rendelkezik, `cases.int64` `cases.float64` amelyek nem tisztíthatók Azure Cosmos db tárolóban, explicit módon hivatkozni kell rájuk egy `WITH` záradékban, és össze kell kapcsolniuk az eredményeket. A következő lekérdezés összesíti a (z) `int32` `int64` és az `float64` oszlopban tárolt adatokat `cases` :

```sql
SELECT geo_id, cases = SUM(cases_int) + SUM(cases_bigint) + SUM(cases_float)
FROM OPENROWSET(
      'CosmosDB',
      'account=MyCosmosDbAccount;database=covid;region=westus2;key=C0Sm0sDbKey==',
       Ecdc
    ) WITH ( geo_id VARCHAR(50) '$.geo_id.string', 
             cases_int INT '$.cases.int32',
             cases_bigint BIGINT '$.cases.int64',
             cases_float FLOAT '$.cases.float64'
    ) as rows
GROUP BY geo_id
```

Ebben a példában az esetek száma a következőképpen van tárolva: `int32` , `int64` vagy `float64` érték. Az egyes országok esetében az esetek számának kiszámításához minden értéket ki kell vonni.

## <a name="known-issues"></a>Ismert problémák

- A kiszolgáló nélküli SQL-készlet egy fordítási idő figyelmeztetést ad vissza, ha az `OPENROWSET` oszlop rendezése nem rendelkezik UTF-8 kódolással. A `OPENROWSET` T-SQL-utasítás használatával egyszerűen módosíthatja az aktuális adatbázisban futó összes függvény alapértelmezett rendezését `alter database current collate Latin1_General_100_CI_AS_SC_UTF8` .

A lehetséges hibák és hibaelhárítási műveletek az alábbi táblázatban láthatók.

| Hiba | Gyökérok |
| --- | --- |
| Szintaktikai hibák:<br/> -Helytelen szintaxis közel `Openrowset`<br/> - `...` nem ismerhető fel `BULK OPENROWSET` szolgáltatói beállítás.<br/> -Helytelen szintaxis közel `...` | Lehetséges kiváltó okok:<br/> – A CosmosDB nem használja az első paraméterként.<br/> – A harmadik paraméterben szereplő azonosító helyett literális karakterláncot használjon.<br/> -Nem adja meg a harmadik paramétert (tároló neve). |
| Hiba történt a CosmosDB-kapcsolatok karakterláncában. | – Nincs megadva a fiók, az adatbázis vagy a kulcs. <br/> – A rendszer nem ismeri fel a kapcsolatok sztringjét.<br/> – A rendszer egy pontosvesszőt ( `;` ) helyez el a kapcsolatok karakterláncának végére. |
| A CosmosDB elérési útjának feloldása meghiúsult a következő hibával: "helytelen fióknév" vagy "helytelen adatbázisnév". | Nem található a megadott fióknév, adatbázisnév vagy tároló, vagy az analitikai tár nem lett engedélyezve a megadott gyűjteményben.|
| A CosmosDB elérési útjának feloldása meghiúsult a "helytelen titkos érték" vagy a "Secret null értékű vagy üres" hiba miatt. | A fiók kulcsa érvénytelen vagy hiányzik. |
| `column name`A típus oszlopa `type name` nem kompatibilis a külső adattípussal `type name` . | A záradékban megadott oszlop típusa `WITH` nem egyezik a Azure Cosmos db tárolóban szereplő típussal. Próbálja meg módosítani az oszlop típusát, ahogy az az [SQL-típusú hozzárendelések Azure Cosmos db](#azure-cosmos-db-to-sql-type-mappings)szakaszában szerepel, vagy használja a `VARCHAR` típust. |
| Az oszlop az `NULL` összes cellában található értékeket tartalmaz. | Valószínűleg helytelen oszlopnév vagy elérésiút-kifejezés szerepel a `WITH` záradékban. A záradékban szereplő oszlopnév (vagy az oszlop típusa után a Path kifejezés) `WITH` meg kell egyeznie a Azure Cosmos db gyűjtemény egyes tulajdonságainak nevével. Az összehasonlítás *megkülönbözteti a kis-és* nagybetűket. Például a `productCode` és `ProductCode` a különböző tulajdonságok. |

A javaslatok és a problémák jelentését az [Azure szinapszis Analytics visszajelzési oldalán](https://feedback.azure.com/forums/307516-azure-synapse-analytics?category_id=387862)teheti meg.

## <a name="next-steps"></a>Következő lépések

További információért tekintse át a következő cikkeket:

- [Power BI és kiszolgáló nélküli SQL-készlet használata az Azure szinapszis hivatkozásával](../../cosmos-db/synapse-link-power-bi.md)
- [Nézetek létrehozása és használata kiszolgáló nélküli SQL-készletben](create-use-views.md)
- [Oktatóanyag kiszolgáló nélküli SQL Pool-nézetek létrehozásához Azure Cosmos DB és a DirectQuery-en keresztüli Power BI modellekhez való csatlakoztatásához](./tutorial-data-analyst.md)
