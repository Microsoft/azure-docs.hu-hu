---
title: Adatok áthelyezése a Cassandra használatával Data Factory
description: Ismerje meg, hogyan helyezhetők át adatok egy helyszíni Cassandra-adatbázisból Azure Data Factory használatával.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 005fd85a152ee2765facda0d961bd9119d1598e8
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387410"
---
# <a name="move-data-from-an-on-premises-cassandra-database-using-azure-data-factory"></a>Adatok áthelyezése helyszíni Cassandra-adatbázisból Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-onprem-cassandra-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-cassandra.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, lásd: [Cassandra Connector a v2-ben](../connector-cassandra.md).

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok helyi Cassandra-adatbázisból való áthelyezéséhez. Az [adattovábbítási tevékenységekről](data-factory-data-movement-activities.md) szóló cikkre épül, amely általános áttekintést nyújt az adatáthelyezésről a másolási tevékenységgel.

A helyszíni Cassandra-adattárból bármilyen támogatott fogadó adattárba másolhat adatok. A másolási tevékenység által mosogatóként támogatott adattárak listáját a [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) táblázatban tekintheti meg. A adatfeldolgozó jelenleg csak a Cassandra adattárból származó adatok áthelyezését támogatja más adattárakba, de az adatok más adattárakból egy Cassandra adattárba való áthelyezésére nem.

## <a name="supported-versions"></a>Támogatott verziók
A Cassandra Connector a Cassandra: 2. x és 3. x következő verzióit támogatja. A saját üzemeltetésű Integration Runtimeon futó tevékenységek esetében a Cassandra 3. x az IR 3,7-es és újabb verzió óta támogatott.

## <a name="prerequisites"></a>Előfeltételek
Ahhoz, hogy a Azure Data Factory szolgáltatás csatlakozni tudjon a helyszíni Cassandra-adatbázishoz, telepítenie kell egy adatkezelés-átjárót ugyanarra a gépre, amely az adatbázist üzemelteti, vagy egy különálló gépen, hogy elkerülje az adatbázissal való konkurens erőforrásokat. Adatkezelés átjáró egy olyan összetevő, amely biztonságos és felügyelt módon csatlakoztatja a helyszíni adatforrásokat a Cloud Serviceshez. Adatkezelés átjáróval kapcsolatos részletekért tekintse meg a [adatkezelés Gateway](data-factory-data-management-gateway.md) -cikket. Az adatok áthelyezéséhez az átjáró adatfolyamatának beállításával kapcsolatos részletes utasításokért lásd: [adatok áthelyezése a helyszíni rendszerből a felhőbe](data-factory-move-data-between-onprem-and-cloud.md) .

A Cassandra-adatbázishoz való kapcsolódáshoz az átjárót kell használnia, még akkor is, ha az adatbázist a felhőben üzemeltetik, például egy Azure IaaS virtuális gépen. Az átjáró ugyanazon a virtuális gépen található, amely az adatbázist vagy egy különálló virtuális gépen üzemelteti, feltéve, hogy az átjáró csatlakozni tud az adatbázishoz.

Az átjáró telepítésekor a automatikusan telepíti a Cassandra-adatbázishoz való kapcsolódáshoz használt Microsoft Cassandra ODBC-illesztőt. Ezért nem kell manuálisan telepítenie az átjárót kezelő gépre az adatoknak a Cassandra-adatbázisból történő másolásakor.

> [!NOTE]
> A kapcsolat/átjáróval kapcsolatos problémák elhárításához kapcsolódó tippekért lásd: [átjárókkal kapcsolatos problémák elhárítása](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) .

## <a name="getting-started"></a>Első lépések
Létrehozhat egy másolási tevékenységgel rendelkező folyamatot, amely különböző eszközök/API-k használatával helyez át egy helyszíni Cassandra-adattárból származó adatokkal.

- A folyamat létrehozásának legegyszerűbb módja a **Másolás varázsló** használata. Tekintse meg az [oktatóanyag: folyamat létrehozása a másolás varázslóval](data-factory-copy-data-wizard-tutorial.md) című témakört, amely gyors áttekintést nyújt a folyamat létrehozásáról az adatmásolási varázsló használatával.
- A következő eszközöket is használhatja a folyamat létrehozásához: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager template**, **.NET API** és **REST API**. A másolási tevékenységgel rendelkező folyamat létrehozásával kapcsolatos részletes utasításokat a [másolási tevékenységről szóló oktatóanyagban](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) talál.

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépések végrehajtásával hozhat létre egy folyamatot, amely egy forrás adattárból egy fogadó adattárba helyezi át az adatait:

1. **Társított szolgáltatások** létrehozása a bemeneti és kimeneti adattáraknak az adat-előállítóhoz való összekapcsolásához.
2. Hozzon létre **adatkészleteket** a másolási művelet bemeneti és kimeneti adatok ábrázolásához.
3. Hozzon **létre egy másolási tevékenységgel rendelkező folyamatot** , amely egy adatkészletet bemenetként és egy adatkészlet kimenetként való elvégzéséhez szükséges.

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory entitások (társított szolgáltatások, adatkészletek és a folyamat) JSON-definícióit. Ha eszközöket/API-kat használ (kivéve a .NET API-t), akkor ezeket a Data Factory entitásokat JSON-formátumban kell megadnia. A helyszíni Cassandra-adattárakból származó adatok másolásához használt Data Factory JSON-definíciókkal rendelkező minta esetében lásd a [JSON-példa: adatok másolása a Cassandra-ből az Azure blobba](#json-example-copy-data-from-cassandra-to-azure-blob) című szakaszt a jelen cikkből.

A következő szakaszokban részletesen ismertetjük a Cassandra-adattárra jellemző Data Factory-entitások definiálásához használt JSON-tulajdonságokat:

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
A következő táblázat a Cassandra társított szolgáltatáshoz tartozó JSON-elemek leírását tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| típus |A Type tulajdonságot a következőre kell beállítani: **OnPremisesCassandra** |Yes |
| gazda |A Cassandra-kiszolgálók egy vagy több IP-címe vagy állomásneve.<br/><br/>Megadhatja az IP-címek vagy állomásnevek vesszővel tagolt listáját, hogy az összes kiszolgálóhoz egyszerre kapcsolódjon. |Yes |
| port |A Cassandra-kiszolgáló által az ügyfélkapcsolatok figyeléséhez használt TCP-port. |Nem, alapértelmezett érték: 9042 |
| authenticationType |Alapszintű vagy névtelen |Yes |
| username |Adja meg a felhasználói fiók felhasználónevét. |Igen, ha a authenticationType értéke alapszintű. |
| jelszó |A felhasználói fiók jelszavának megadása. |Igen, ha a authenticationType értéke alapszintű. |
| Átjáró neve |A helyszíni Cassandra-adatbázishoz való kapcsolódáshoz használt átjáró neve. |Yes |
| encryptedCredential |Az átjáró által titkosított hitelesítő adat. |No |

>[!NOTE]
>Jelenleg nem támogatott a Cassandra-hez való kapcsolódás a TLS használatával.

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Az adatkészletek definiálásához rendelkezésre álló & tulajdonságok teljes listáját az [adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben találja. Az adathalmazok (például a struktúra, a rendelkezésre állás és a szabályzat) minden adatkészlet esetében hasonlóak (például az Azure SQL, az Azure Blob, az Azure Table stb.).

A **typeProperties** szakasz különbözik az egyes adatkészletek típusaitól, és információt nyújt az adattárban található adatok helyéről. A **CassandraTable** típusú adatkészlet typeProperties szakasza a következő tulajdonságokkal rendelkezik

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| kulcstartomány |A térköz vagy séma neve a Cassandra adatbázisban. |Igen (ha a **CassandraSource** - **lekérdezés** nincs definiálva). |
| tableName |A tábla neve a Cassandra adatbázisban. |Igen (ha a **CassandraSource** - **lekérdezés** nincs definiálva). |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A tevékenységek definiálásához elérhető & tulajdonságok teljes listáját a [folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben találja. A tulajdonságok, például a név, a leírás, a bemeneti és a kimeneti táblák, valamint a szabályzatok minden típusú tevékenységhez elérhetők.

Míg a tevékenység typeProperties szakaszában elérhető tulajdonságok az egyes tevékenységtípusok esetében eltérőek. Másolási tevékenység esetén a források és a nyelők típusaitól függően változnak.

Ha a forrás **CassandraSource** típusú, a következő tulajdonságok érhetők el a typeProperties szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés használatával olvashatja el az adatolvasást. |SQL-92 lekérdezés vagy CQL-lekérdezés. Lásd: [CQL-hivatkozás](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>SQL-lekérdezés használatakor adja meg a **térköz nevét. a tábla neve** , amely a lekérdezni kívánt táblát jelöli. |Nem (ha meg van adva a táblanév és a szóköz az adatkészleten). |
| consistencyLevel |A konzisztencia szintje határozza meg, hogy hány replikának kell válaszolnia egy olvasási kérelemre, mielőtt adatvisszaad az ügyfélalkalmazás számára. Cassandra ellenőrzi a megadott számú replikát az adatolvasási kérelem teljesítéséhez. |EGY, KETTŐ, HÁROM, KVÓRUM, MIND, LOCAL_QUORUM, EACH_QUORUM, LOCAL_ONE. További részletek: az [adatkonzisztencia konfigurálása](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) . |Nem. Az alapértelmezett érték egy. |

## <a name="json-example-copy-data-from-cassandra-to-azure-blob"></a>JSON-példa: adatok másolása a Cassandra-ből az Azure-Blobba
Ez a példa JSON-definíciókat tartalmaz, amelyek segítségével a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy a [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával hozhat létre folyamatokat. Bemutatja, hogyan másolhat adatok egy helyszíni Cassandra-adatbázisból egy Azure-Blob Storageba. Az adatmásolási művelet azonban az [itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) megadott összes mosogatóba átmásolható a Azure Data Factoryban.

> [!IMPORTANT]
> Ez a példa JSON-kódrészleteket biztosít. Nem tartalmaz részletes útmutatást az adatelőállító létrehozásához. Részletes útmutatásért lásd: az [adatáthelyezés a helyszíni helyszínek és a felhőalapú cikkek között](data-factory-move-data-between-onprem-and-cloud.md) .

A minta a következő adatgyári entitásokat tartalmazhatja:

* [OnPremisesCassandra](#linked-service-properties)típusú társított szolgáltatás.
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú társított szolgáltatás.
* [CassandraTable](#dataset-properties)típusú bemeneti [adatkészlet](data-factory-create-datasets.md) .
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú kimeneti [adatkészlet](data-factory-create-datasets.md) .
* [CassandraSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)használó másolási tevékenységgel rendelkező [folyamat](data-factory-create-pipelines.md) .

**Cassandra társított szolgáltatás:**

Ez a példa a **Cassandra** társított szolgáltatást használja. A társított szolgáltatás által támogatott tulajdonságokért tekintse meg a [Cassandra társított szolgáltatás](#linked-service-properties) című szakaszt.

```json
{
    "name": "CassandraLinkedService",
    "properties":
    {
        "type": "OnPremisesCassandra",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "host": "mycassandraserver",
            "port": 9042,
            "username": "user",
            "password": "password",
            "gatewayName": "mygateway"
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

**Cassandra bemeneti adatkészlet:**

```json
{
    "name": "CassandraInput",
    "properties": {
        "linkedServiceName": "CassandraLinkedService",
        "type": "CassandraTable",
        "typeProperties": {
            "tableName": "mytable",
            "keySpace": "mykeyspace"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
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

A **külső** beállítása **igaz** érték esetén a Data Factory szolgáltatás, amely az adatkészletet az adat-előállítón kívülre helyezi, és nem az adat-előállító tevékenysége állítja elő.

**Azure-Blob kimeneti adatkészlete:**

A rendszer óránként egy új blobba írja az adatbevitelt (frekvencia: óra, intervallum: 1).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/fromcassandra"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Másolási tevékenység egy folyamatban a Cassandra Source és a blob fogadó:**

A folyamat egy másolási tevékenységet tartalmaz, amely a bemeneti és a kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra van ütemezve. A folyamat JSON-definíciójában a **forrás** típusa **CassandraSource** értékre van állítva, a **fogadó típusa** pedig **BlobSink**.

A RelationalSource által támogatott tulajdonságok listáját a [RelationalSource típusának tulajdonságainál](#copy-activity-properties) tekintheti meg.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2016-06-01T18:00:00",
        "end":"2016-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[
        {
            "name": "CassandraToAzureBlob",
            "description": "Copy from Cassandra to an Azure blob",
            "type": "Copy",
            "inputs": [
            {
                "name": "CassandraInput"
            }
            ],
            "outputs": [
            {
                "name": "AzureBlobOutput"
            }
            ],
            "typeProperties": {
                "source": {
                    "type": "CassandraSource",
                    "query": "select id, firstname, lastname from mykeyspace.mytable"

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

### <a name="type-mapping-for-cassandra"></a>Típus leképezése Cassandra
| Cassandra típus | .NET-alapú típus |
| --- | --- |
| ASCII |Sztring |
| BIGINT |Int64 |
| BLOB |Bájt [] |
| LOGIKAI |Logikai |
| DECIMÁLIS |Tizedesjegy |
| DUPLÁN |Dupla |
| FLOAT |Egyirányú |
| INET |Sztring |
| INT |Int32 |
| TEXT |Sztring |
| IDŐBÉLYEGZŐ |DateTime |
| TIMEUUID |Guid |
| UUID |Guid |
| VARCHAR |Sztring |
| VARINT |Tizedesjegy |

> [!NOTE]
> A gyűjtési típusok (Térkép, beállítás, lista stb.) esetében tekintse át a következőt: a [Cassandra Collection types használata virtuális tábla használatával](#work-with-collections-using-virtual-table) szakasz.
>
> A felhasználó által definiált típusok nem támogatottak.
>
> A bináris oszlop és a sztring oszlop hosszának hossza nem lehet nagyobb, mint 4000.
>
>

## <a name="work-with-collections-using-virtual-table"></a>Gyűjtemények használata virtuális tábla használatával
A Azure Data Factory egy beépített ODBC-illesztővel csatlakozik a Cassandra-adatbázishoz, és másolja azokat. A gyűjtemény típusainak, például a map, a set és a List beállítás esetében az illesztőprogram a megfelelő virtuális táblákba újranormalizálja az adattípusokat. Pontosabban, ha egy tábla bármely gyűjtemény oszlopot tartalmaz, az illesztőprogram a következő virtuális táblákat hozza létre:

* Egy **alaptábla**, amely ugyanazokat az adatokkal rendelkezik, mint a valódi tábla, kivéve a gyűjtemény oszlopait. Az alaptábla ugyanazt a nevet használja, mint az azt jelképező valódi tábla.
* Az egyes gyűjtemények oszlopokhoz tartozó **virtuális táblázat** , amely kibővíti a beágyazott adattípusokat. A gyűjteményeket jelképező virtuális táblák a valódi tábla nevével, a "*VT*" elválasztóval és az oszlop nevével vannak elnevezve.

A virtuális táblák a valós táblázatba tartozó, az illesztőprogramnak a denormalizált információhoz való hozzáférésének engedélyezésére vonatkoznak. Részletekért lásd a példa szakaszt. A Cassandra Collections tartalmát a virtuális táblák lekérdezésével és a hozzájuk való csatlakozással érheti el.

A [Másolás varázslóval](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity) intuitív módon megtekintheti a Cassandra-adatbázisban található táblák listáját, beleértve a virtuális táblákat, és megtekintheti az adatmegjelenítést a belsejében. Létrehozhat egy lekérdezést is a másolás varázslóban, és ellenőrizheti, hogy az eredmény megjelenik-e.

### <a name="example"></a>Példa
Például a következő "ExampleTable" egy Cassandra adatbázis-tábla, amely egy "pk_int" nevű egész számú elsődleges kulcs oszlopot tartalmaz, egy érték nevű szöveges oszlop, egy lista oszlop, egy Térkép oszlop és egy beállított oszlop ("StringSet").

| pk_int | Érték | Lista | Térkép | StringSet |
| --- | --- | --- | --- | --- |
| 1 |"Sample Value 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"3. minta érték" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

Az illesztőprogram több virtuális táblát fog előállítani, hogy ezt az egyetlen táblát képviseljék. A virtuális táblákban található idegenkulcs-oszlopok hivatkoznak a valós tábla elsődleges kulcs oszlopaira, és jelzik, hogy a virtuális tábla sorai melyik valós táblázatnak felelnek meg.

Az első virtuális tábla a "ExampleTable" nevű alaptábla, amely az alábbi táblázatban látható. Az alaptábla ugyanazokat az adatokkal rendelkezik, mint az eredeti adatbázistábla, kivéve azokat a gyűjteményeket, amelyek ki vannak hagyva ebből a táblából, és más virtuális táblákban kibontva vannak.

| pk_int | Érték |
| --- | --- |
| 1 |"Sample Value 1" |
| 3 |"3. minta érték" |

A következő táblázatok azokat a virtuális táblákat mutatják be, amelyek a lista, a Térkép és a StringSet oszlopokból származó adatok újranormalizálása. A "_index" vagy "_key" végződésű neveket jelölő oszlopok az eredeti listán vagy a térképen belüli pozíciót jelölik. A "_value" végződésű neveket tartalmazó oszlopok a gyűjteményből származó kibontott adatokkal rendelkeznek.

#### <a name="table-exampletable_vt_list"></a>"ExampleTable_vt_List" tábla:
| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

#### <a name="table-exampletable_vt_map"></a>"ExampleTable_vt_Map" tábla:
| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |A |
| 1 |S2 |b |
| 3 |S1 |t |

#### <a name="table-exampletable_vt_stringset"></a>"ExampleTable_vt_StringSet" tábla:
| pk_int | StringSet_value |
| --- | --- |
| 1 |A |
| 1 |B |
| 1 |C |
| 3 |A |
| 3 |E |

## <a name="map-source-to-sink-columns"></a>Forrás leképezése a fogadó oszlopokra
A forrás adatkészletben lévő oszlopok a fogadó adatkészlet oszlopaihoz való leképezésével kapcsolatos további tudnivalókért lásd: [adatkészlet oszlopainak leképezése Azure Data Factoryban](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Megismételhető olvasás a rokon forrásokból
Az adatok a kapcsolódó adattárakból való másolása során érdemes megismételni a nem kívánt eredmények elkerülését. Azure Data Factory a szeleteket manuálisan is újra futtathatja. Az újrapróbálkozási szabályzatot is konfigurálhatja egy adatkészlethez, hogy a rendszer hiba esetén újrafuttassa a szeleteket. Ha egy szeletet mindkét módon újrafuttat, meg kell győződnie arról, hogy a szeletek hányszor futnak. Lásd: [megismételhető olvasás a rokon forrásokból](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="performance-and-tuning"></a>Teljesítmény és hangolás
A [másolási tevékenység teljesítményének & hangolási útmutatójában](data-factory-copy-activity-performance.md) megismerheti azokat a főbb tényezőket, amelyek hatással vannak az adatáthelyezés (másolási tevékenység) teljesítményére Azure Data Factory és az optimalizálás különféle módjaival.
