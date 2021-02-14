---
title: Adatok másolása az Azure szinapszis Analytics szolgáltatásba vagy onnan
description: Megtudhatja, hogyan másolhat adatok az Azure szinapszis Analytics szolgáltatásba vagy onnan az Azure Data Factory használatával
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: aa364ec434db980bf226008537ca928628fcac1b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100392085"
---
# <a name="copy-data-to-and-from-azure-synapse-analytics-using-azure-data-factory"></a>Adatok másolása az Azure szinapszis Analytics és a rendszerből a Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-azure-sql-data-warehouse-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-azure-sql-data-warehouse.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg az [Azure szinapszis Analytics-összekötőt a v2-ben](../connector-azure-sql-data-warehouse.md).

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok Azure szinapszis Analytics szolgáltatásba való áthelyezéséhez. Az [adattovábbítási tevékenységekről](data-factory-data-movement-activities.md) szóló cikkre épül, amely általános áttekintést nyújt az adatáthelyezésről a másolási tevékenységgel.

> [!TIP]
> A legjobb teljesítmény eléréséhez használja a Base-t az adatok Azure szinapszis Analyticsbe való betöltéséhez. Az [adatok Azure szinapszis analyticsbe való betöltéséhez használja](#use-polybase-to-load-data-into-azure-synapse-analytics) a következőt:. A használati eseteket bemutató bemutatóért lásd: [1 TB betöltése az Azure szinapszis Analytics szolgáltatásba 15 perc alatt, Azure Data Factory](data-factory-load-sql-data-warehouse.md).

## <a name="supported-scenarios"></a>Támogatott esetek
Az adatok az **Azure szinapszis Analyticsből** a következő adattárakba másolhatók:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

Az adatok a következő adattárakból másolhatók **Az Azure szinapszis analyticsbe**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!TIP]
> Ha az adatok másolása SQL Server vagy Azure SQL Database az Azure szinapszis Analytics szolgáltatásba történik, ha a tábla nem létezik a célhelyen, Data Factory automatikusan létrehozhatja a táblát az Azure szinapszis Analyticsben a forrás adattárban található tábla sémájának használatával. További részletekért lásd az [automatikus tábla létrehozása](#auto-table-creation) című témakört.

## <a name="supported-authentication-type"></a>Támogatott hitelesítési típus
Az Azure szinapszis Analytics-összekötő támogatja az alapszintű hitelesítést.

## <a name="getting-started"></a>Első lépések
Létrehozhat egy másolási tevékenységgel rendelkező folyamatot, amely különböző eszközök/API-k használatával helyez át egy Azure-beli szinapszis-elemzésbe vagy-re irányuló adatátvitelt.

Az adatok másolása varázslóval az Azure szinapszis Analytics szolgáltatásba másolt adatok másolására szolgáló folyamat létrehozásának legegyszerűbb módja. Tekintse meg az [oktatóanyag: az Azure szinapszis analyticsbe való betöltését](../load-azure-sql-data-warehouse.md) ismertető témakört, amely leírja, hogyan hozhat létre folyamatokat az Adatmásolás varázslóval a Data Factory használatával.

A következő eszközöket is használhatja a folyamat létrehozásához: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager template**, **.NET API** és **REST API**. A másolási tevékenységgel rendelkező folyamat létrehozásával kapcsolatos részletes utasításokat a [másolási tevékenységről szóló oktatóanyagban](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) talál.

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépések végrehajtásával hozhat létre egy folyamatot, amely egy forrás adattárból egy fogadó adattárba helyezi át az adatait:

1. Hozzon létre egy **adatelőállítót**. Egy adatelőállító egy vagy több folyamatot is tartalmazhat. 
2. **Társított szolgáltatások** létrehozása a bemeneti és kimeneti adattáraknak az adat-előállítóhoz való összekapcsolásához. Ha például egy Azure Blob Storage-ból másol egy Azure szinapszis Analytics-re, két társított szolgáltatást hoz létre, amely összekapcsolja az Azure Storage-fiókját és az Azure szinapszis Analytics szolgáltatást az adatokkal rendelkező gyárával. Az Azure szinapszis Analytics szolgáltatáshoz kapcsolódó társított szolgáltatások tulajdonságainál tekintse meg a [társított szolgáltatás tulajdonságai](#linked-service-properties) című szakaszt. 
3. Hozzon létre **adatkészleteket** a másolási művelet bemeneti és kimeneti adatok ábrázolásához. Az utolsó lépésben említett példában létrehoz egy adatkészletet, amely megadja a bemeneti adatokat tartalmazó BLOB-tárolót és mappát. Emellett létrehoz egy másik adatkészletet is, amely megadja a táblázatot az Azure szinapszis Analyticsben, amely a blob Storage-ból másolt adatokat tárolja. Az Azure szinapszis Analytics szolgáltatásra jellemző adatkészlet-tulajdonságokért lásd: [adatkészlet tulajdonságai](#dataset-properties) szakasz.
4. Hozzon **létre egy másolási tevékenységgel rendelkező folyamatot** , amely egy adatkészletet bemenetként és egy adatkészlet kimenetként való elvégzéséhez szükséges. A korábban említett példában a BlobSource forrásként és SqlDWSinkként használja a másolási tevékenységhez. Hasonlóképpen, ha az Azure szinapszis Analyticsről az Azure Blob Storagera másol, a másolási tevékenységben SqlDWSource és BlobSink használ. Az Azure szinapszis Analyticsre jellemző másolási tevékenység tulajdonságairól a [másolási tevékenység tulajdonságai](#copy-activity-properties) című szakaszban olvashat. Az adattár forrásként vagy fogadóként való használatával kapcsolatos részletekért kattintson az adattár előző szakaszában található hivatkozásra.

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory entitások (társított szolgáltatások, adatkészletek és a folyamat) JSON-definícióit. Ha eszközöket/API-kat használ (kivéve a .NET API-t), akkor ezeket a Data Factory entitásokat JSON-formátumban kell megadnia. Az Azure szinapszis Analytics szolgáltatásba az adatok másolásához és az onnan az Azure-ba való másolásához használt Data Factory JSON-definíciókkal rendelkező mintákhoz lásd a jelen cikk [JSON-példák](#json-examples-for-copying-data-to-and-from-azure-synapse-analytics) című szakaszát.

A következő szakaszokban részletesen ismertetjük az Azure szinapszis Analytics szolgáltatáshoz tartozó Data Factory entitások definiálásához használt JSON-tulajdonságokat:

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
Az alábbi táblázat az Azure szinapszis Analytics társított szolgáltatáshoz tartozó JSON-elemek leírását tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| típus |A Type tulajdonságot a következőre kell beállítani: **AzureSqlDW** |Yes |
| connectionString |A connectionString tulajdonsághoz tartozó Azure szinapszis Analytics-példányhoz való kapcsolódáshoz szükséges információk megadása. Csak az alapszintű hitelesítés támogatott. |Yes |

> [!IMPORTANT]
> Konfigurálja [Azure SQL Database tűzfalat](/previous-versions/azure/ee621782(v=azure.100)#ConnectingFromAzure) és az adatbázis-kiszolgálót, hogy [engedélyezze az Azure-szolgáltatások számára a kiszolgáló elérését](/previous-versions/azure/ee621782(v=azure.100)#ConnectingFromAzure). Emellett, ha adatokat másol az Azure-ba az Azure-on kívülről, többek között a helyszíni adatforrásokból a (z) és a (z) adatfeldolgozó-átjáróval, konfigurálja a megfelelő IP-címtartományt azon számítógép számára, amely adatokat küld az Azure szinapszis Analyticsnek.

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Az adatkészletek definiálásához rendelkezésre álló & tulajdonságok teljes listáját az [adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben találja. Az adathalmazok (például a struktúra, a rendelkezésre állás és a szabályzat) minden adatkészlet esetében hasonlóak (például az Azure SQL, az Azure Blob, az Azure Table stb.).

A typeProperties szakasz különbözik az egyes adatkészletek típusaitól, és információt nyújt az adattárban található adatok helyéről. A **AzureSqlDWTable** típusú adatkészlet **typeProperties** szakasza a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| tableName |Annak a táblának vagy nézetnek a neve, amelyre a társított szolgáltatás hivatkozik az Azure szinapszis Analytics-adatbázisban. |Yes |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A tevékenységek definiálásához elérhető & tulajdonságok teljes listáját a [folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben találja. A tulajdonságok, például a név, a leírás, a bemeneti és a kimeneti táblák, valamint a szabályzatok minden típusú tevékenységhez elérhetők.

> [!NOTE]
> A másolási tevékenység csak egy bemenetet hoz létre, és csak egy kimenetet állít elő.

Míg a tevékenység typeProperties szakaszában elérhető tulajdonságok az egyes tevékenységtípusok esetében eltérőek. Másolási tevékenység esetén a források és a nyelők típusaitól függően változnak.

### <a name="sqldwsource"></a>SqlDWSource
Ha a forrás **SqlDWSource** típusú, a következő tulajdonságok érhetők el a **typeProperties** szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| sqlReaderQuery |Az egyéni lekérdezés használatával olvashatja el az adatolvasást. |SQL-lekérdezési karakterlánc. Például: select * from Sajáttábla. |No |
| sqlReaderStoredProcedureName |Azon tárolt eljárás neve, amely beolvassa az adatokat a forrás táblából. |A tárolt eljárás neve. Az utolsó SQL-utasításnak SELECT utasításnak kell lennie a tárolt eljárásban. |No |
| storedProcedureParameters |A tárolt eljárás paraméterei. |Név/érték párok. A paraméterek nevének és burkolatának meg kell egyeznie a tárolt eljárás paramétereinek nevével és házával. |No |

Ha a **sqlReaderQuery** meg van adva a SqlDWSource, a másolási tevékenység futtatja ezt a lekérdezést az Azure szinapszis Analytics-forráson az adatkéréshez.

Azt is megteheti, hogy megadhat egy tárolt eljárást a **sqlReaderStoredProcedureName** és a **storedProcedureParameters** megadásával (ha a tárolt eljárás paraméterekkel rendelkezik).

Ha nem ad meg sqlReaderQuery vagy sqlReaderStoredProcedureName, a JSON-adatkészlet szerkezet szakaszában definiált oszlopok az Azure szinapszis Analytics szolgáltatáson futtatott lekérdezések létrehozásához használatosak. Példa: `select column1, column2 from mytable`. Ha az adatkészlet definíciója nem rendelkezik a struktúrával, az összes oszlop ki lesz választva a táblából.

#### <a name="sqldwsource-example"></a>SqlDWSource példa

```JSON
"source": {
    "type": "SqlDWSource",
    "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
    "storedProcedureParameters": {
        "stringData": { "value": "str3" },
        "identifier": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
    }
}
```
**A tárolt eljárás definíciója:**

```SQL
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sqldwsink"></a>SqlDWSink
A **SqlDWSink** a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |A másolási tevékenységre vonatkozó lekérdezés megadása úgy, hogy egy adott szeletből származó adatmennyiséget takarítson meg. Részletekért lásd: [ismételhetőség szakasz](#repeatability-during-copy). |Egy lekérdezési utasítás. |No |
| allowPolyBase |Azt jelzi, hogy a BULKINSERT mechanizmus helyett a következőt kell-e használni (ha van ilyen). <br/><br/> **Az adatok Azure szinapszis-elemzésbe való betöltésének ajánlott módja a Base használata.** Megkötések és részletek a következő témakörben találhatók: az [adatok betöltése az Azure szinapszis analyticsbe](#use-polybase-to-load-data-into-azure-synapse-analytics) című szakasza. |Igaz <br/>False (alapértelmezett) |No |
| polyBaseSettings |A tulajdonságok olyan csoportja, amely akkor adható meg, ha a **allowPolybase** tulajdonság értéke **true (igaz**). |&nbsp; |No |
| rejectValue |A lekérdezés sikertelensége előtt visszautasítható sorok számát vagy százalékos arányát adja meg. <br/><br/>További információ a [create External Table (Transact-SQL)](/sql/t-sql/statements/create-external-table-transact-sql) című témakör **argumentumok** szakaszában található alapszintű elutasítás beállításairól. |0 (alapértelmezett), 1, 2,... |No |
| rejectType |Megadja, hogy a rejectValue beállítás literál értékként vagy százalékként van-e megadva. |Érték (alapértelmezett), százalék |No |
| rejectSampleValue |Meghatározza a lekérdezni kívánt sorok számát, mielőtt a rendszer újraszámítja az elutasított sorok százalékos arányát. |1, 2,... |Igen, ha  a rejectType **százaléka** |
| useTypeDefault |Meghatározza, hogy a rendszer hogyan kezelje a hiányzó értékeket a tagolt szövegfájlokban, ha a viszonyítási adatok beolvasása a szövegfájlból történik.<br/><br/>Erről a tulajdonságról a [külső fájlformátum létrehozása (Transact-SQL)](/sql/t-sql/statements/create-external-file-format-transact-sql)argumentumai című szakaszban olvashat bővebben. |Igaz, hamis (alapértelmezett) |No |
| writeBatchSize |Az SQL-táblába szúrja be az adatmennyiséget, ha a puffer mérete eléri a writeBatchSize |Egész szám (sorok száma) |Nem (alapértelmezett: 10000) |
| writeBatchTimeout |Várakozási idő a kötegelt beszúrási művelet befejezéséhez, mielőtt időtúllépés történt. |időtartomány<br/><br/> Például: "00:30:00" (30 perc). |No |

#### <a name="sqldwsink-example"></a>SqlDWSink példa

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true
}
```

## <a name="use-polybase-to-load-data-into-azure-synapse-analytics"></a>Adatok betöltése az Azure szinapszis Analytics szolgáltatásba a Base használatával
A **[Base](/sql/relational-databases/polybase/polybase-guide)** használatával nagy mennyiségű adat a nagy adatátviteli sebességű Azure szinapszis-elemzésbe való betöltésének hatékony módja. Az átviteli sebesség nagy mennyiségű nyereségét az alapértelmezett BULKINSERT mechanizmus helyett a Base használatával láthatja. Lásd: a [teljesítmény hivatkozási számának másolása](data-factory-copy-activity-performance.md#performance-reference) részletes összehasonlítással. A használati eseteket bemutató bemutatóért lásd: [1 TB betöltése az Azure szinapszis Analytics szolgáltatásba 15 perc alatt, Azure Data Factory](data-factory-load-sql-data-warehouse.md).

* Ha a forrásadatok az **Azure blobban vagy a Azure Data Lake Storeban** vannak, és a formátum kompatibilis a Base-sel, akkor közvetlenül másolhat az Azure szinapszis analyticsbe a Base használatával. A részleteket lásd: a **[közvetlen másolás a Base használatával](#direct-copy-using-polybase)** .
* Ha a forrás adattárat és a formátumot eredetileg nem a Base támogatja, használhatja a **[szakaszos másolást a albase szolgáltatás használatával](#staged-copy-using-polybase)** . Emellett jobb átviteli sebességet is biztosít, ha automatikusan konvertálja az adatok alap-kompatibilis formátumba, és az adatok tárolása az Azure Blob Storage-ban történik. Ezután betöltődik az Azure szinapszis Analyticsbe.

Állítsa a `allowPolyBase` tulajdonságot **true (igaz** ) értékre, ahogy az alábbi példában látható, hogy a Azure Data Factory, hogy az adatok az Azure szinapszis analyticsbe való másolásához használhatók legyenek. Ha a allowPolyBase értéke TRUE (igaz) értékre van állítva, akkor a tulajdonság csoport segítségével megadhatja a tulajdonságok `polyBaseSettings` csoportot. a polyBaseSettings használatával használható tulajdonságokkal kapcsolatos részletekért tekintse meg a [SqlDWSink](#sqldwsink) szakaszt.

```JSON
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

### <a name="direct-copy-using-polybase"></a>Közvetlen másolás a Base használatával
Az Azure szinapszis Analytics-előAzure Data Lake Store közvetlenül támogatja az Azure blobot és a (egyszerű szolgáltatásnév használatával) forrásként és adott fájlformátumokra vonatkozó követelményekkel. Ha a forrásadatok megfelelnek az ebben a részben ismertetett feltételeknek, a forrás-adattárból közvetlenül másolhatja az Azure szinapszis Analytics szolgáltatást a Base paranccsal. Ellenkező esetben [a szakaszos másolást a Base paranccsal](#staged-copy-using-polybase)végezheti el.

> [!TIP]
> Az adatok Data Lake Storeról az Azure szinapszis Analytics szolgáltatásba való hatékony másolásához további információ [Azure Data Factory az Azure szinapszis Analytics szolgáltatással való Data Lake Store használatakor még egyszerűbbé és kényelmesebbvé teszi az adatok elemzését](/archive/blogs/azuredatalake/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse).

Ha a követelmények nem teljesülnek, Azure Data Factory ellenőrzi a beállításokat, és automatikusan visszakerül a BULKINSERT mechanizmusra az adatáthelyezéshez.

1. A **forráshoz társított szolgáltatás** típusa: **AzureStorage** vagy **AzureDataLakeStore az egyszerű szolgáltatás hitelesítésével**.
2. A **bemeneti adatkészlet** a következő típusú: **AzureBlob** vagy **AzureDataLakeStore**, és a tulajdonságok területen a `type` **OrcFormat**, a **ParquetFormat** vagy a **Szövegformátum** érték szerepel a következő konfigurációkkal:

   1. `rowDelimiter` csak **\n** lehet.
   2. `nullValue`**üres karakterláncra** ("") van beállítva, vagy `treatEmptyAsNull` **igaz** értékre van állítva.
   3. `encodingName` értéke **UTF-8**, amely az **alapértelmezett** érték.
   4. `escapeChar`,,, `quoteChar` `firstRowAsHeader` és `skipLineCount` nincsenek megadva.
   5. `compression` nem lehet **tömörítés**, **gzip** vagy **deflate**.

      ```JSON
      "typeProperties": {
       "folderPath": "<blobpath>",
       "format": {
           "type": "TextFormat",
           "columnDelimiter": "<any delimiter>",
           "rowDelimiter": "\n",
           "nullValue": "",
           "encodingName": "utf-8"
       },
       "compression": {
           "type": "GZip",
           "level": "Optimal"
       }
      },
      ```

3. Nincs beállítás a `skipHeaderLineCount` **BlobSource** vagy a **AzureDataLakeStore** alatt a folyamat másolási tevékenységéhez.
4. A `sliceIdentifierColumnName` folyamat másolási tevékenységéhez nem tartozik beállítás a **SqlDWSink** alatt. (A Base garantálja, hogy minden adat frissítve lett, vagy egyetlen futtatásban sem frissül. Az **ismételhetőség** elérése érdekében használhatja a `sqlWriterCleanupScript` következőt:).
5. Nincs `columnMapping` használatban a társított másolási tevékenységben.

### <a name="staged-copy-using-polybase"></a>Előkészített másolás a Base használatával
Ha a forrásadatok nem felelnek meg az előző szakaszban bemutatott feltételeknek, engedélyezheti az adatok másolását egy átmeneti Azure-Blob Storage használatával (nem lehet Premium Storage). Ebben az esetben a Azure Data Factory automatikusan elvégzi az adatok átalakítását, hogy megfeleljenek az adatformátumra vonatkozó követelményeknek, majd a Base használatával töltse be az Azure szinapszis Analyticsbe az adatok betöltését, és végül törölje a temp-adatokról a blob Storage-ból. Az adatok átmeneti Azure-blobon keresztül történő másolásával kapcsolatos részletekért tekintse meg a [szakaszos másolást](data-factory-copy-activity-performance.md#staged-copy) .

> [!NOTE]
> Ha a helyszíni adattárakból származó adatok az Azure szinapszis Analyticsbe való másolása a Base és az előkészítés használatával történik, ha a adatkezelés-átjáró verziója 2,4, a JRE (Java Runtime Environment) a forrásadatok megfelelő formátumba való átalakításához szükséges. Javasoljuk, hogy az ilyen függőség elkerülése érdekében frissítse az átjárót a legújabb verzióra.
>

A szolgáltatás használatához hozzon létre egy [Azure Storage-beli társított szolgáltatást](data-factory-azure-blob-connector.md#azure-storage-linked-service) , amely az ideiglenes blob Storage-t tartalmazó Azure Storage-fiókra hivatkozik, majd adja meg a `enableStaging` és a tulajdonságokat a `stagingSettings` másolási tevékenységhez, ahogy az a következő kódban is látható:

```json
"activities":[
{
    "name": "Sample copy activity from SQL Server to Azure Synapse Analytics via PolyBase",
    "type": "Copy",
    "inputs": [{ "name": "OnpremisesSQLServerInput" }],
    "outputs": [{ "name": "AzureSQLDWOutput" }],
    "typeProperties": {
        "source": {
            "type": "SqlSource",
        },
        "sink": {
            "type": "SqlDwSink",
            "allowPolyBase": true
        },
        "enableStaging": true,
        "stagingSettings": {
            "linkedServiceName": "MyStagingBlob"
        }
    }
}
]
```

## <a name="best-practices-when-using-polybase"></a>Ajánlott eljárások a Base használatakor
A következő szakaszokban az [Azure szinapszis Analytics ajánlott eljárásaiban](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-best-practices.md)említett további ajánlott eljárásokat ismertetjük.

### <a name="required-database-permission"></a>Szükséges adatbázis-engedély
A viszonyítási alap használatához az szükséges, hogy a felhasználó az adatok betöltését az Azure szinapszis Analyticsben a ["vezérlés" engedéllyel](/sql/relational-databases/security/permissions-database-engine) rendelkezzen a céladatbázis számára. Ennek egyik módja, ha az adott felhasználót a "db_owner" szerepkör tagjaként adja hozzá. [Ebből a szakaszból](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization)megtudhatja, hogyan teheti meg ezt a műveletet.

### <a name="row-size-and-data-type-limitation"></a>Sor mérete és adattípus korlátozása
A kiinduló terhelések csak **1 MB** -nál kisebb sorok betöltésére korlátozódnak, és nem tölthetők be VARCHR (max), NVARCHAR (max) vagy VARBINARY (max) értékre. Tekintse [meg a](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads)következőt:.

Ha 1 MB-nál nagyobb méretű adatsorokkal rendelkezik, érdemes lehet függőlegesen felosztani a forrás táblákat több kis méretre, ahol a legnagyobb sorok mérete nem haladhatja meg a korlátot. Ezután a kisebb táblák betölthetők a Base használatával, és összevonva az Azure szinapszis Analytics szolgáltatásban.

### <a name="azure-synapse-analytics-resource-class"></a>Azure szinapszis analitikai erőforrás-osztály
A lehető legjobb átviteli sebesség elérése érdekében érdemes lehet nagyobb erőforrás-osztályt rendelni ahhoz a felhasználóhoz, amely az adatok Azure szinapszis Analyticsbe való betöltésére használatos. Ebből a témakörből megtudhatja, hogyan teheti ezt meg a [felhasználói erőforrás osztályra vonatkozó példa módosítása](../../synapse-analytics/sql-data-warehouse/resource-classes-for-workload-management.md)után.

### <a name="tablename-in-azure-synapse-analytics"></a>Táblanév az Azure szinapszis Analyticsben
A következő táblázat példákat mutat be arra, hogyan határozhatja meg a **Táblanév** tulajdonságot az adatkészlet JSON-ban a séma és a tábla különböző kombinációi esetében.

| ADATBÁZIS-séma | Table name (Táblázat neve) | Táblanév JSON-tulajdonság |
| --- | --- | --- |
| dbo |Sajáttábla |Sajáttábla vagy dbo. Sajáttábla vagy [dbo]. Sajáttábla |
| dbo1 |Sajáttábla |dbo1. Sajáttábla vagy [dbo1]. Sajáttábla |
| dbo |Saját. tábla |[Saját. table] vagy [dbo]. [Saját tábla] |
| dbo1 |Saját. tábla |[dbo1]. [Saját tábla] |

Ha a következő hiba jelenik meg, akkor a táblanév tulajdonsághoz megadott értékkel kapcsolatos probléma lehet. A táblanév JSON-tulajdonság értékeinek megadásához tekintse meg a táblázatot a megfelelő módon.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Alapértelmezett értékeket tartalmazó oszlopok
A Data Factory-alapú alapszolgáltatások jelenleg csak ugyanannyi oszlopot fogadnak el, mint a célként megadott táblában. Tegyük fel, hogy van egy négy oszlopot tartalmazó táblája, és az egyik az alapértelmezett értékkel van definiálva. A bemeneti adatoknak továbbra is négy oszlopot kell tartalmazniuk. A 3 oszlopos bemeneti adatkészlet megadása a következő üzenethez hasonló hibát eredményez:

```
All columns of the table must be specified in the INSERT BULK statement.
```
A NULL érték az alapértelmezett érték egy speciális formája. Ha az oszlop üres, akkor az oszlop bemeneti adatok (blobban) üresek lehetnek (a bemeneti adatkészletből nem lehet hiányzik). Az Azure szinapszis Analyticsben NULLát szúr be azokhoz.

## <a name="auto-table-creation"></a>Automatikus tábla létrehozása
Ha a másolás varázslót használja az adatok SQL Serverból való másolásához, vagy Azure SQL Database az Azure szinapszis Analytics szolgáltatásba, és a forrástábla megfelelő táblája nem létezik a célhelyen, a Data Factory automatikusan létre tudja hozni a táblát az adattárházban a forrástábla sémájának használatával.

Data Factory létrehozza a tárolóban található táblát a forrás adattárban található azonos nevű táblával. Az oszlopok adattípusai a következő típusú leképezés alapján vannak kiválasztva. Ha szükséges, a típus konverziót hajt végre a forrás-és a célhelyek közötti inkompatibilitás javítása érdekében. Emellett ciklikus multiplexelés-táblázat eloszlást is használ.

| Forrás SQL Database oszlop típusa | Cél Azure szinapszis Analytics-oszlop típusa (méret korlátozása) |
| --- | --- |
| Int | Int |
| BigInt | BigInt |
| SmallInt | SmallInt |
| TinyInt | TinyInt |
| Bites | Bites |
| Tizedesjegy | Tizedesjegy |
| Numerikus | Tizedesjegy |
| Float | Float |
| Money | Money |
| Valós | Valós |
| Túlcsordulási | Túlcsordulási |
| Bináris | Bináris |
| Varbinary | Varbinary (legfeljebb 8000) |
| Dátum | Dátum |
| DateTime | DateTime |
| DateTime2 | DateTime2 |
| Idő | Idő |
| DateTimeOffset | DateTimeOffset |
| Idő adattípusúra | Idő adattípusúra |
| Szöveg | Varchar (legfeljebb 8000) |
| NText | NVarChar (legfeljebb 4000) |
| Kép | VarBinary (legfeljebb 8000) |
| UniqueIdentifier | UniqueIdentifier |
| Char | Char |
| NChar | NChar |
| VarChar | VarChar (legfeljebb 8000) |
| NVarChar | NVarChar (legfeljebb 4000) |
| Xml | Varchar (legfeljebb 8000) |

[!INCLUDE [data-factory-type-repeatability-for-sql-sources](../../../includes/data-factory-type-repeatability-for-sql-sources.md)]

## <a name="type-mapping-for-azure-synapse-analytics"></a>Típus leképezése az Azure szinapszis Analyticshez
Ahogy azt az [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) című cikk ismerteti, a másolási tevékenység az alábbi kétlépéses megközelítéssel hajtja végre az automatikus típus-konverziókat a forrás típusairól a fogadó típusokra:

1. Konvertálás natív forrásokból .NET-típusra
2. Konvertálás .NET-típusról natív fogadó típusra

Amikor az Azure szinapszis Analytics szolgáltatásból &ba helyezi az adatátvitelt, a következő leképezéseket használja az SQL típusa és a .NET típus között, és fordítva.

A leképezés megegyezik a [ADO.net Adattípusának SQL Server-leképezésével](/dotnet/framework/data/adonet/sql-server-data-type-mappings).

| SQL Server adatbázismotor típusa | .NET-keretrendszer típusa |
| --- | --- |
| bigint |Int64 |
| binary |Bájt [] |
| bit |Logikai |
| char |Karakterlánc, char [] |
| dátum |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| DateTimeOffset |DateTimeOffset |
| Tizedesjegy |Tizedesjegy |
| FILESTREAM attribútum (varbinary (max)) |Bájt [] |
| Float |Dupla |
| image |Bájt [] |
| int |Int32 |
| pénzt |Tizedesjegy |
| NCHAR |Karakterlánc, char [] |
| ntext |Karakterlánc, char [] |
| numerikus |Tizedesjegy |
| nvarchar |Karakterlánc, char [] |
| valós szám |Egyirányú |
| ROWVERSION |Bájt [] |
| idő adattípusúra |DateTime |
| smallint |Int16 |
| túlcsordulási |Tizedesjegy |
| sql_variant |Objektum |
| szöveg |Karakterlánc, char [] |
| time |időtartam |
| időbélyeg |Bájt [] |
| tinyint |Bájt |
| uniqueidentifier |Guid |
| varbinary |Bájt [] |
| varchar |Karakterlánc, char [] |
| xml |Xml |

A másolási tevékenység definíciójában a forrás adatkészletből származó oszlopokat is leképezheti a fogadó adatkészletből származó oszlopokra. Részletekért lásd: [adatkészlet-oszlopok leképezése Azure Data Factoryban](data-factory-map-columns.md).

## <a name="json-examples-for-copying-data-to-and-from-azure-synapse-analytics"></a>JSON-példák az Azure szinapszis Analytics szolgáltatásba és onnan történő adatmásolásra
Az alábbi példák olyan JSON-definíciókat biztosítanak, amelyek segítségével a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy a [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával hozhat létre folyamatokat. Bemutatják, hogyan másolhatók adatok az Azure szinapszis Analytics és az Azure Blob Storage. Az adatok azonban **közvetlenül** a forrásokból bármelyik forrásból átmásolhatók, ha a másolási tevékenység a Azure Data Factoryban [szerepel.](data-factory-data-movement-activities.md#supported-data-stores-and-formats)

### <a name="example-copy-data-from-azure-synapse-analytics-to-azure-blob"></a>Példa: adatok másolása az Azure szinapszis Analyticsből az Azure Blobba
A minta a következő Data Factory entitásokat határozza meg:

1. [AzureSqlDW](#linked-service-properties)típusú társított szolgáltatás.
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú társított szolgáltatás.
3. [AzureSqlDWTable](#dataset-properties)típusú bemeneti [adatkészlet](data-factory-create-datasets.md) .
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú kimeneti [adatkészlet](data-factory-create-datasets.md) .
5. [SqlDWSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)használó másolási tevékenységgel rendelkező [folyamat](data-factory-create-pipelines.md) .

A minta idősoros (óránkénti, napi stb.) adatsorozatot másol az Azure szinapszis Analytics-adatbázisban lévő táblából minden órában egy blobba. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszokban ismertetjük.

**Azure szinapszis Analytics társított szolgáltatás:**

```JSON
{
  "name": "AzureSqlDWLinkedService",
  "properties": {
    "type": "AzureSqlDW",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Azure Blob Storage társított szolgáltatás:**

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
**Az Azure szinapszis Analytics bemeneti adatkészlete:**

A minta feltételezi, hogy létrehozott egy "Sajáttábla" táblát az Azure szinapszis Analyticsben, és egy "timestampcolumn" nevű oszlopot tartalmaz az idősorozat-adatsorokhoz.

A "külső": "true" beállítás azt tájékoztatja a Data Factory szolgáltatást, hogy az adatkészlet kívül esik az adat-előállítón, és nem az adat-előállító tevékenysége.

```JSON
{
  "name": "AzureSqlDWInput",
  "properties": {
    "type": "AzureSqlDWTable",
    "linkedServiceName": "AzureSqlDWLinkedService",
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

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Másolási tevékenység SqlDWSource és BlobSink rendelkező folyamatokban:**

A folyamat egy másolási tevékenységet tartalmaz, amely a bemeneti és a kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra van ütemezve. A folyamat JSON-definíciójában a **forrás** típusa **SqlDWSource** értékre van állítva, a **fogadó típusa** pedig **BlobSink**. A **SqlReaderQuery** tulajdonsághoz megadott SQL-lekérdezés a másoláshoz az elmúlt órában kijelöli az adatforrást.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "AzureSQLDWtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSqlDWInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlDWSource",
            "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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
> A példában a **sqlReaderQuery** meg van adva a SqlDWSource. A másolási tevékenység ezt a lekérdezést az Azure szinapszis Analytics-forráson futtatja, hogy lekérje az adatgyűjtést.
>
> Azt is megteheti, hogy megadhat egy tárolt eljárást a **sqlReaderStoredProcedureName** és a **storedProcedureParameters** megadásával (ha a tárolt eljárás paraméterekkel rendelkezik).
>
> Ha nem ad meg sqlReaderQuery vagy sqlReaderStoredProcedureName, az adatkészlet JSON szerkezet szakaszában definiált oszlopok a lekérdezés létrehozásához használatosak (válassza a Column1, Oszlop2 from sajáttábla) parancsot az Azure szinapszis Analyticsen való futtatáshoz. Ha az adatkészlet definíciója nem rendelkezik a struktúrával, az összes oszlop ki lesz választva a táblából.
>
>

### <a name="example-copy-data-from-azure-blob-to-azure-synapse-analytics"></a>Példa: adatok másolása az Azure Blobból az Azure szinapszis Analyticsbe
A minta a következő Data Factory entitásokat határozza meg:

1. [AzureSqlDW](#linked-service-properties)típusú társított szolgáltatás.
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú társított szolgáltatás.
3. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú bemeneti [adatkészlet](data-factory-create-datasets.md) .
4. [AzureSqlDWTable](#dataset-properties)típusú kimeneti [adatkészlet](data-factory-create-datasets.md) .
5. [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) és [SqlDWSink](#copy-activity-properties)használó másolási tevékenységgel rendelkező [folyamat](data-factory-create-pipelines.md) .

A minta idősorozat-adatok (óránként, naponta stb.) másolását végzi az Azure blobból az Azure-beli szinapszis Analytics-adatbázisban lévő egyik táblába óránként. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszokban ismertetjük.

**Azure szinapszis Analytics társított szolgáltatás:**

```JSON
{
  "name": "AzureSqlDWLinkedService",
  "properties": {
    "type": "AzureSqlDW",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
**Azure Blob Storage társított szolgáltatás:**

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
**Azure Blob bemeneti adatkészlet:**

Az adatok minden órában egy új blobból származnak (frekvencia: óra, intervallum: 1). A blob mappa elérési útját és fájlnevét a feldolgozás alatt álló szelet kezdési időpontja alapján dinamikusan értékeli a rendszer. A mappa elérési útja az év, hónap és nap részeként használja a kezdési időt, és a fájlnév a kezdési időpont óra részét használja. a "külső": "true" beállítás tájékoztatja a Data Factory szolgáltatást arról, hogy ez a tábla kívül esik az adatelőállítón, és nem az adatelőállító tevékenysége.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
**Az Azure szinapszis Analytics kimeneti adatkészlete:**

A minta az Azure szinapszis Analytics "Sajáttábla" nevű táblájába másolja az adatmásolt fájlokat. Hozzon létre egy táblázatot az Azure szinapszis Analyticsben azonos számú oszloppal, ahogy a blob CSV-fájlját tartalmazza. Minden órában új sor kerül a táblázatba.

```JSON
{
  "name": "AzureSqlDWOutput",
  "properties": {
    "type": "AzureSqlDWTable",
    "linkedServiceName": "AzureSqlDWLinkedService",
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
**Másolási tevékenység BlobSource és SqlDWSink rendelkező folyamatokban:**

A folyamat egy másolási tevékenységet tartalmaz, amely a bemeneti és a kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra van ütemezve. A folyamat JSON-definíciójában a **forrás** típusa **BlobSource** értékre van állítva, a **fogadó típusa** pedig **SqlDWSink**.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoSQLDW",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlDWOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource",
            "blobColumnSeparators": ","
          },
          "sink": {
            "type": "SqlDWSink",
            "allowPolyBase": true
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
A bemutatókat lásd: az [1 TB betöltése az Azure szinapszis Analytics szolgáltatásba 15 perc alatt, Azure Data Factory](data-factory-load-sql-data-warehouse.md) és az Azure szinapszis Analytics dokumentációjában [Azure Data Factory cikk betöltésével](../load-azure-sql-data-warehouse.md) .

## <a name="performance-and-tuning"></a>Teljesítmény és hangolás
A [másolási tevékenység teljesítményének & hangolási útmutatójában](data-factory-copy-activity-performance.md) megismerheti azokat a főbb tényezőket, amelyek hatással vannak az adatáthelyezés (másolási tevékenység) teljesítményére Azure Data Factory és az optimalizálás különféle módjaival.