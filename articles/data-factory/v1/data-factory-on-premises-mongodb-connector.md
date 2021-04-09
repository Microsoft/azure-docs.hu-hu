---
title: Adatok áthelyezése a MongoDB
description: Ismerje meg, hogyan helyezhetők át adatok a MongoDB-adatbázisból a Azure Data Factory használatával.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/13/2018
ms.openlocfilehash: cedb0b99f04df00763a3ee83287eec90bd5fb45d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100387512"
---
# <a name="move-data-from-mongodb-using-azure-data-factory"></a>Adatok áthelyezése a MongoDB a Azure Data Factory használatával

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-on-premises-mongodb-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-mongodb.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg a [MongoDB-összekötőt a v2-ben](../connector-mongodb.md).


Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok egy helyszíni MongoDB-adatbázisból való áthelyezéséhez. Az [adattovábbítási tevékenységekről](data-factory-data-movement-activities.md) szóló cikkre épül, amely általános áttekintést nyújt az adatáthelyezésről a másolási tevékenységgel.

A helyszíni MongoDB-adattárból bármely támogatott fogadó adattárba másolhat adatok. A másolási tevékenység által mosogatóként támogatott adattárak listáját a [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) táblázatban tekintheti meg. A MongoDB-adattár jelenleg csak az adatok áthelyezését támogatja más adattárakba, de az adatok más adattárakból egy MongoDB adattárba való áthelyezésére nem.

## <a name="prerequisites"></a>Előfeltételek
Ahhoz, hogy a Azure Data Factory szolgáltatás csatlakozni tudjon a helyszíni MongoDB-adatbázishoz, a következő összetevőket kell telepítenie:

- A támogatott MongoDB-verziók a következők: 2,4, 2,6, 3,0, 3,2, 3,4 és 3,6.
- Adatkezelés átjárót ugyanazon a gépen, amely az adatbázist üzemelteti, vagy egy különálló gépen, hogy elkerülje az adatbázissal való versengés elkerülését. Adatkezelés Gateway egy olyan szoftver, amely biztonságos és felügyelt módon köti össze a helyszíni adatforrásokat a Cloud Services szolgáltatással. Adatkezelés átjáróval kapcsolatos részletekért tekintse meg a [adatkezelés Gateway](data-factory-data-management-gateway.md) -cikket. Az adatok áthelyezéséhez az átjáró adatfolyamatának beállításával kapcsolatos részletes utasításokért lásd: [adatok áthelyezése a helyszíni rendszerből a felhőbe](data-factory-move-data-between-onprem-and-cloud.md) .

    Az átjáró telepítésekor a automatikusan telepíti a MongoDB-hez való csatlakozáshoz használt Microsoft MongoDB ODBC-illesztőt.

    > [!NOTE]
    > A MongoDB való kapcsolódáshoz az átjárót kell használnia, még akkor is, ha az Azure IaaS-beli virtuális gépeken van üzemeltetve. Ha a felhőben üzemeltetett MongoDB-példányhoz próbál csatlakozni, akkor a IaaS virtuális gépen is telepítheti az átjáró példányát.

## <a name="getting-started"></a>Első lépések
Létrehozhat egy másolási tevékenységgel rendelkező folyamatot, amely különböző eszközök/API-k használatával helyez át egy helyszíni MongoDB-adattárból származó adatokkal.

A folyamat létrehozásának legegyszerűbb módja a **Másolás varázsló** használata. Tekintse meg az [oktatóanyag: folyamat létrehozása a másolás varázslóval](data-factory-copy-data-wizard-tutorial.md) című témakört, amely gyors áttekintést nyújt a folyamat létrehozásáról az adatmásolási varázsló használatával.

A következő eszközöket is használhatja a folyamat létrehozásához: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager template**, **.NET API** és **REST API**. A másolási tevékenységgel rendelkező folyamat létrehozásával kapcsolatos részletes utasításokat a [másolási tevékenységről szóló oktatóanyagban](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) talál.

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépések végrehajtásával hozhat létre egy folyamatot, amely egy forrás adattárból egy fogadó adattárba helyezi át az adatait:

1. **Társított szolgáltatások** létrehozása a bemeneti és kimeneti adattáraknak az adat-előállítóhoz való összekapcsolásához.
2. Hozzon létre **adatkészleteket** a másolási művelet bemeneti és kimeneti adatok ábrázolásához.
3. Hozzon **létre egy másolási tevékenységgel rendelkező folyamatot** , amely egy adatkészletet bemenetként és egy adatkészlet kimenetként való elvégzéséhez szükséges.

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory entitások (társított szolgáltatások, adatkészletek és a folyamat) JSON-definícióit. Ha eszközöket/API-kat használ (kivéve a .NET API-t), akkor ezeket a Data Factory entitásokat JSON-formátumban kell megadnia.  A helyszíni MongoDB-adattárakból származó adatok másolásához használt Data Factory JSON-definíciókkal rendelkező minta esetében lásd a jelen cikk [JSON-példa: adatok másolása a MongoDB-ből az Azure blobba](#json-example-copy-data-from-mongodb-to-azure-blob) című szakaszát.

A következő szakaszokban részletesen ismertetjük a MongoDB-forrásra jellemző Data Factory-entitások definiálásához használt JSON-tulajdonságokat:

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
A következő táblázat a **OnPremisesMongoDB** társított szolgáltatáshoz tartozó JSON-elemek leírását tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| típus |A Type tulajdonságot a következőre kell beállítani: **OnPremisesMongoDb** |Yes |
| kiszolgáló |A MongoDB-kiszolgáló IP-címe vagy állomásneve. |Yes |
| port |A MongoDB-kiszolgáló által az ügyfélkapcsolatok figyeléséhez használt TCP-port. |Nem kötelező, alapértelmezett érték: 27017 |
| authenticationType |Alapszintű vagy névtelen. |Yes |
| username |Felhasználói fiók a MongoDB eléréséhez. |Igen (ha alapszintű hitelesítést használ). |
| jelszó |A felhasználó jelszava. |Igen (ha alapszintű hitelesítést használ). |
| authSource |Annak a MongoDB-adatbázisnak a neve, amelyet a hitelesítés hitelesítő adatainak ellenőrzéséhez használni kíván. |Nem kötelező (ha alapszintű hitelesítést használ). alapértelmezett: a databaseName tulajdonsággal megadott rendszergazdai fiókot és adatbázist használja. |
| databaseName |Az elérni kívánt MongoDB-adatbázis neve. |Yes |
| Átjáró neve |Azon átjáró neve, amely hozzáfér az adattárhoz. |Yes |
| encryptedCredential |Az átjáró által titkosított hitelesítő adat. |Választható |

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Az adatkészletek definiálásához rendelkezésre álló & tulajdonságok teljes listáját az [adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben találja. Az adathalmazok (például a struktúra, a rendelkezésre állás és a szabályzat) minden adatkészlet esetében hasonlóak (például az Azure SQL, az Azure Blob, az Azure Table stb.).

A **typeProperties** szakasz különbözik az egyes adatkészletek típusaitól, és információt nyújt az adattárban található adatok helyéről. A **MongoDbCollection** típusú adatkészlet typeProperties szakasza a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| collectionName |A gyűjtemény neve a MongoDB adatbázisban. |Yes |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A tevékenységek definiálásához elérhető & tulajdonságok teljes listáját a [folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben találja. A tulajdonságok, például a név, a leírás, a bemeneti és a kimeneti táblák, valamint a szabályzatok minden típusú tevékenységhez elérhetők.

A tevékenység **typeProperties** szakaszában elérhető tulajdonságok az egyes tevékenységtípusok esetében eltérőek. Másolási tevékenység esetén a források és a nyelők típusaitól függően változnak.

Ha a forrás típusa **MongoDbSource** , a következő tulajdonságok érhetők el a typeProperties szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés használatával olvashatja el az adatolvasást. |SQL-92 lekérdezési karakterlánc. Például: select * from Sajáttábla. |Nem (ha meg van adva az **adatkészlet** **collectionName** ) |



## <a name="json-example-copy-data-from-mongodb-to-azure-blob"></a>JSON-példa: adatok másolása a MongoDB-ből az Azure-Blobba
Ez a példa JSON-definíciókat tartalmaz, amelyek segítségével a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy a [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával hozhat létre folyamatokat. Bemutatja, hogyan másolhat adatok egy helyszíni MongoDB egy Azure-Blob Storageba. Az adatmásolási művelet azonban az [itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) megadott összes mosogatóba átmásolható a Azure Data Factoryban.

A minta a következő adatgyári entitásokat tartalmazhatja:

1. [OnPremisesMongoDb](#linked-service-properties)típusú társított szolgáltatás.
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú társított szolgáltatás.
3. [MongoDbCollection](#dataset-properties)típusú bemeneti [adatkészlet](data-factory-create-datasets.md) .
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú kimeneti [adatkészlet](data-factory-create-datasets.md) .
5. [MongoDbSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)használó másolási tevékenységgel rendelkező [folyamat](data-factory-create-pipelines.md) .

A minta egy lekérdezési eredményből másolja az adatait a MongoDB-adatbázisba óránként egy blobba. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszokban ismertetjük.

Első lépésként állítsa be az adatkezelési átjárót az [adatkezelés átjáró](data-factory-data-management-gateway.md) című cikkben szereplő utasítások szerint.

**MongoDB társított szolgáltatás:**

```json
{
    "name": "OnPremisesMongoDbLinkedService",
    "properties":
    {
        "type": "OnPremisesMongoDb",
        "typeProperties":
        {
            "authenticationType": "<Basic or Anonymous>",
            "server": "< The IP address or host name of the MongoDB server >",
            "port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>",
            "username": "<username>",
            "password": "<password>",
            "authSource": "< The database that you want to use to check your credentials for authentication. >",
            "databaseName": "<database name>",
            "gatewayName": "<mygateway>"
        }
    }
}
```

**Azure Storage-beli társított szolgáltatás:**

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**MongoDB bemeneti adatkészlete:** A "külső" beállítása: az "igaz" érték tájékoztatja a Data Factory szolgáltatást arról, hogy a tábla kívül esik az adatelőállítón, és nem az adatelőállító tevékenysége.

```json
{
    "name": "MongoDbInputDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": "OnPremisesMongoDbLinkedService",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

**Azure-Blob kimeneti adatkészlete:**

A rendszer óránként egy új blobba írja az adatbevitelt (frekvencia: óra, intervallum: 1). A blob mappájának elérési útját a rendszer dinamikusan kiértékeli a feldolgozás alatt álló szelet kezdési időpontja alapján. A mappa elérési útja a kezdési idő év, hónap, nap és óra részét használja.

```json
{
    "name": "AzureBlobOutputDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/frommongodb/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
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
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Másolási tevékenység egy folyamatba MongoDB forrással és blob-fogadóval:**

A folyamat egy másolási tevékenységet tartalmaz, amely a fenti bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra van ütemezve. A folyamat JSON-definíciójában a **forrás** típusa **MongoDbSource** értékre van állítva, a **fogadó típusa** pedig **BlobSink**. A **lekérdezési** tulajdonsághoz megadott SQL-lekérdezés a másoláshoz az elmúlt órában kijelöli az összes adatforrást.

```json
{
    "name": "CopyMongoDBToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "MongoDbSource",
                        "query": "$$Text.Format('select * from MyTable where LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "MongoDbInputDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutputDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "MongoDBToAzureBlob"
            }
        ],
        "start": "2016-06-01T18:00:00Z",
        "end": "2016-06-01T19:00:00Z"
    }
}
```


## <a name="schema-by-data-factory"></a>Séma Data Factory szerint
Azure Data Factory a szolgáltatás egy MongoDB-gyűjtemény sémáját a gyűjteményben a legújabb 100-dokumentumok használatával. Ha ezek a 100-dokumentumok nem tartalmaznak teljes sémát, egyes oszlopok figyelmen kívül hagyhatók a másolási művelet során.

## <a name="type-mapping-for-mongodb"></a>Típus leképezése MongoDB
Ahogy azt az [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) című cikk ismerteti, a másolási tevékenység az alábbi kétlépéses megközelítéssel hajtja végre az automatikus típus-konverziókat a forrás típusairól a fogadó típusokra:

1. Konvertálás natív forrásokból .NET-típusra
2. Konvertálás .NET-típusról natív fogadó típusra

Az adatok MongoDB való áthelyezésekor a rendszer a következő leképezéseket használja a MongoDB típusokból .NET-típusokra.

| MongoDB típusa | .NET-keretrendszer típusa |
| --- | --- |
| Bináris |Bájt [] |
| Logikai érték |Logikai |
| Dátum |DateTime |
| NumberDouble |Dupla |
| NumberInt |Int32 |
| NumberLong |Int64 |
| ObjectID |Sztring |
| Sztring |Sztring |
| UUID |Guid |
| Objektum |Normalizálva a "_" oszlopokkal beágyazott elválasztóként |

> [!NOTE]
> Ha többet szeretne megtudni a virtuális táblák használatával történő tömbök támogatásáról, tekintse meg a következő témakört [: összetett típusok támogatása virtuális táblák használatával](#support-for-complex-types-using-virtual-tables) .

Jelenleg a következő MongoDB-adattípusok nem támogatottak: DBPointer, JavaScript, max/min kulcs, reguláris kifejezés, szimbólum, időbélyeg, nem definiált

## <a name="support-for-complex-types-using-virtual-tables"></a>Összetett típusok támogatása virtuális táblák használatával
A Azure Data Factory egy beépített ODBC-illesztővel csatlakozik a MongoDB-adatbázishoz, és másolja azokat. Az olyan összetett típusok esetében, mint például a tömbök vagy különböző típusú objektumok a dokumentumokban, az illesztőprogram újranormalizálja az adatmennyiséget a megfelelő virtuális táblákba. Pontosabban, ha egy tábla ilyen oszlopokat tartalmaz, az illesztőprogram a következő virtuális táblákat hozza létre:

* Egy **alaptábla**, amely ugyanazokat az adatokkal rendelkezik, mint a valós tábla, kivéve a komplex típusú oszlopokat. Az alaptábla ugyanazt a nevet használja, mint az azt jelképező valódi tábla.
* Az egyes komplex típusú oszlopokhoz tartozó **virtuális táblázat** , amely kibővíti a beágyazott adattípusokat. A virtuális táblák neve a valódi tábla, a "_" elválasztója és a tömb vagy objektum neve alapján történik.

A virtuális táblák a valós táblázatba tartozó, az illesztőprogramnak a denormalizált információhoz való hozzáférésének engedélyezésére vonatkoznak. Lásd az alábbi részleteket. A MongoDB-tömbök tartalmát a virtuális táblák lekérdezésével és a hozzájuk való csatlakozással érheti el.

A [Másolás varázslóval](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) intuitív módon megtekintheti a MongoDB-adatbázisban lévő táblák listáját, beleértve a virtuális táblákat, és megtekintheti az adatmegjelenítést a belsejében. Létrehozhat egy lekérdezést is a másolás varázslóban, és ellenőrizheti, hogy az eredmény megjelenik-e.

### <a name="example"></a>Példa
Például az alábbi "ExampleTable" olyan MongoDB-tábla, amely egyetlen oszlopból áll, és az egyes cellákban található objektumok tömbje, valamint egy skaláris típusok tömbjét tartalmazó oszlop.

| _id | Ügyfél neve | Számlák | Szolgáltatásszint | Minősítések |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id: "123", elem: "kenyérpirító", Ár: "456", kedvezmény: "0.2"}, {invoice_id: "124", elem: "sütő", Ár: "1235", kedvezmény: "0,2"}] |Ezüst |[5, 6] |
| 2222 |XYZ |[{invoice_id: "135", elem: "hűtőszekrény", Ár: "12543", kedvezmény: "0,0"}] |Arany |[1, 2] |

Az illesztőprogram több virtuális táblát fog előállítani, hogy ezt az egyetlen táblát képviseljék. Az első virtuális tábla a "ExampleTable" nevű alaptábla, amely az alábbi ábrán látható. Az alaptábla az eredeti tábla összes adatát tartalmazza, de a tömbökből származó adatok ki lettek hagyva, és ki lettek bontva a virtuális táblákban.

| _id | Ügyfél neve | Szolgáltatásszint |
| --- | --- | --- |
| 1111 |ABC |Ezüst |
| 2222 |XYZ |Arany |

A következő táblázatok a példában szereplő eredeti tömböket képviselő virtuális táblákat mutatják be. Ezek a táblák a következőket tartalmazzák:

* Az eredeti tömb soraihoz tartozó eredeti elsődleges kulcs oszlopra való hivatkozás (a _id oszlopon keresztül)
* Az eredeti tömbben lévő adatok pozíciójának jelzése
* A tömbben lévő egyes elemek kibontott adathalmaza

"ExampleTable_Invoices" tábla:

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | item | price | Kedvezmény |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |kenyérpirító |456 |0,2 |
| 1111 |1 |124 |sütő |1235 |0,2 |
| 2222 |0 |135 |hűtőszekrény |12543 |0,0 |

"ExampleTable_Ratings" tábla:

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="map-source-to-sink-columns"></a>Forrás leképezése a fogadó oszlopokra
A forrás adatkészletben lévő oszlopok a fogadó adatkészlet oszlopaihoz való leképezésével kapcsolatos további tudnivalókért lásd: [adatkészlet oszlopainak leképezése Azure Data Factoryban](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Megismételhető olvasás a rokon forrásokból
Az adatok a kapcsolódó adattárakból való másolása során érdemes megismételni a nem kívánt eredmények elkerülését. Azure Data Factory a szeleteket manuálisan is újra futtathatja. Az újrapróbálkozási szabályzatot is konfigurálhatja egy adatkészlethez, hogy a rendszer hiba esetén újrafuttassa a szeleteket. Ha egy szeletet mindkét módon újrafuttat, meg kell győződnie arról, hogy a szeletek hányszor futnak. Lásd: [megismételhető olvasás a rokon forrásokból](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Teljesítmény és hangolás
A [másolási tevékenység teljesítményének & hangolási útmutatójában](data-factory-copy-activity-performance.md) megismerheti azokat a főbb tényezőket, amelyek hatással vannak az adatáthelyezés (másolási tevékenység) teljesítményére Azure Data Factory és az optimalizálás különféle módjaival.

## <a name="next-steps"></a>Következő lépések
Az adatok [áthelyezése a helyszíni és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) című cikk részletes útmutatást nyújt olyan adatfolyamatok létrehozásához, amelyek egy helyszíni adattárból egy Azure-adattárba helyezik át az adatok áthelyezését.
