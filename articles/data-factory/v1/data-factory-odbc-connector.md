---
title: Adatok áthelyezése az ODBC-adattárból
description: Ismerje meg, hogyan helyezhetők át adatok az ODBC-adattárakból Azure Data Factory használatával.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/19/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: e847592127d19eba3370255385f5b969b87e886e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100380100"
---
# <a name="move-data-from-odbc-data-stores-using-azure-data-factory"></a>Adatok áthelyezése az ODBC-adattárakból Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-odbc-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-odbc.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg az [ODBC-összekötőt a v2-ben](../connector-odbc.md).


Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok egy helyszíni ODBC-adattárból való áthelyezéséhez. Az [adattovábbítási tevékenységekről](data-factory-data-movement-activities.md) szóló cikkre épül, amely általános áttekintést nyújt az adatáthelyezésről a másolási tevékenységgel.

Az adatok másolása egy ODBC-adattárból bármely támogatott fogadó adattárba lehetséges. A másolási tevékenység által mosogatóként támogatott adattárak listáját a [támogatott adattárak](data-factory-data-movement-activities.md#supported-data-stores-and-formats) táblázatban tekintheti meg. A adatfeldolgozó jelenleg csak az adatok áthelyezését támogatja egy ODBC-adattárból más adattárakba, de az adatok más adattárakból egy ODBC-adattárba való áthelyezésére nem.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="enabling-connectivity"></a>Kapcsolat engedélyezése
A Data Factory szolgáltatás támogatja a helyszíni ODBC-forrásokhoz való kapcsolódást a adatkezelés átjáró használatával. Az átjáró beállításával adatkezelés kapcsolatos további információkért lásd: az [adatáthelyezés a helyszíni helyszínek és a felhő között](data-factory-move-data-between-onprem-and-cloud.md) . Az átjáró használatával csatlakozhat egy ODBC-adattárhoz, még akkor is, ha az egy Azure IaaS virtuális gépen üzemel.

Az átjárót ugyanarra a helyszíni gépre vagy az Azure virtuális gépre is telepítheti ODBC-adattárként. Azt javasoljuk azonban, hogy az átjárót külön gépre/Azure IaaS virtuális gépre telepítse, hogy elkerülje az erőforrás-tartalmat és a jobb teljesítményt. Ha az átjárót külön gépre telepíti, a gépnek el kell tudnia érni a gépet az ODBC-adattárral.

A adatkezelés átjárón kívül telepítenie kell az ODBC-illesztőt is az adattárakhoz az átjárót tároló számítógépen.

> [!NOTE]
> A kapcsolat/átjáróval kapcsolatos problémák elhárításához kapcsolódó tippekért lásd: [átjárókkal kapcsolatos problémák elhárítása](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) .

## <a name="getting-started"></a>Első lépések
Létrehozhat egy másolási tevékenységgel rendelkező folyamatot, amely különböző eszközök/API-k használatával helyez át egy ODBC-adattárból származó adatokkal.

A folyamat létrehozásának legegyszerűbb módja a **Másolás varázsló** használata. Tekintse meg az [oktatóanyag: folyamat létrehozása a másolás varázslóval](data-factory-copy-data-wizard-tutorial.md) című témakört, amely gyors áttekintést nyújt a folyamat létrehozásáról az adatmásolási varázsló használatával.

A következő eszközöket is használhatja a folyamat létrehozásához: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager template**, **.NET API** és **REST API**. A másolási tevékenységgel rendelkező folyamat létrehozásával kapcsolatos részletes utasításokat a [másolási tevékenységről szóló oktatóanyagban](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) talál.

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépések végrehajtásával hozhat létre egy folyamatot, amely egy forrás adattárból egy fogadó adattárba helyezi át az adatait:

1. **Társított szolgáltatások** létrehozása a bemeneti és kimeneti adattáraknak az adat-előállítóhoz való összekapcsolásához.
2. Hozzon létre **adatkészleteket** a másolási művelet bemeneti és kimeneti adatok ábrázolásához.
3. Hozzon **létre egy másolási tevékenységgel rendelkező folyamatot** , amely egy adatkészletet bemenetként és egy adatkészlet kimenetként való elvégzéséhez szükséges.

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory entitások (társított szolgáltatások, adatkészletek és a folyamat) JSON-definícióit. Ha eszközöket/API-kat használ (kivéve a .NET API-t), akkor ezeket a Data Factory entitásokat JSON-formátumban kell megadnia. Az adatok ODBC-adattárból történő másolásához használt Data Factory JSON-definíciókkal rendelkező minta esetében lásd a jelen cikk [JSON-példa: adatok másolása az ODBC-adattárból az Azure blobba](#json-example-copy-data-from-odbc-data-store-to-azure-blob) című szakaszát.

A következő szakaszokban részletesen ismertetjük az ODBC-adattárra jellemző Data Factory entitások definiálásához használt JSON-tulajdonságokat:

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
Az alábbi táblázat az ODBC-hez társított szolgáltatáshoz tartozó JSON-elemek leírását tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| típus |A Type tulajdonságot a következőre kell beállítani: **OnPremisesOdbc** |Yes |
| connectionString |A kapcsolati karakterlánc és egy opcionálisan titkosított hitelesítő adat nem hozzáférési hitelesítő része. Tekintse meg a példákat a következő részekben. <br/><br/>Megadhatja a kapcsolati karakterláncot, mint például a minta `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"` , vagy használhatja a rendszeradatforrás nevét (adatforrás neve) az átjárón a (z) rendszerhez (a társított `"DSN=<name of the DSN>;"` szolgáltatásban még mindig meg kell adnia a hitelesítő adatok részét). |Yes |
| hitelesítő adat |Az illesztőprogram-specifikus tulajdonság-érték formátumban megadott kapcsolati karakterlánc hozzáférési hitelesítő része. Példa: `"Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;"`. |No |
| authenticationType |Az ODBC-adattárhoz való kapcsolódáshoz használt hitelesítés típusa. A lehetséges értékek a következők: névtelen és alapszintű. |Yes |
| userName (Felhasználónév) |Ha alapszintű hitelesítést használ, adja meg a felhasználónevet. |No |
| jelszó |Adja meg a felhasználónévhez megadott felhasználói fiók jelszavát. |No |
| Átjáró neve |Annak az átjárónak a neve, amelyet a Data Factory szolgáltatásnak használnia kell az ODBC-adattárhoz való kapcsolódáshoz. |Yes |

### <a name="using-basic-authentication"></a>Alapszintű hitelesítés használata

```json
{
    "name": "odbc",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```
### <a name="using-basic-authentication-with-encrypted-credentials"></a>Egyszerű hitelesítés használata titkosított hitelesítő adatokkal
A hitelesítő adatokat a [New-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) 1,0 (Azure PowerShell) parancsmag vagy a [New-AzureDataFactoryEncryptValue](/previous-versions/azure/dn834940(v=azure.100)) (0,9 vagy a Azure PowerShell korábbi verziója) használatával titkosíthatja.

```json
{
    "name": "odbc",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=myserver.database.windows.net; Database=TestDatabase;;EncryptedCredential=eyJDb25uZWN0...........................",
            "gatewayName": "mygateway"
        }
    }
}
```

### <a name="using-anonymous-authentication"></a>Névtelen hitelesítés használata

```json
{
    "name": "odbc",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "connectionString": "Driver={SQL Server};Server={servername}.database.windows.net; Database=TestDatabase;",
            "credential": "UID={uid};PWD={pwd}",
            "gatewayName": "mygateway"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Az adatkészletek definiálásához rendelkezésre álló & tulajdonságok teljes listáját az [adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben találja. Az adathalmazok (például a struktúra, a rendelkezésre állás és a szabályzat) minden adatkészlet esetében hasonlóak (például az Azure SQL, az Azure Blob, az Azure Table stb.).

A **typeProperties** szakasz különbözik az egyes adatkészletek típusaitól, és információt nyújt az adattárban található adatok helyéről. A **RelationalTable** típusú (ODBC-adatkészletet tartalmazó) adatkészlet typeProperties szakasza a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| tableName |Az ODBC-adattárban található tábla neve. |Yes |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A tevékenységek definiálásához elérhető & tulajdonságok teljes listáját a [folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben találja. A tulajdonságok, például a név, a leírás, a bemeneti és a kimeneti táblák, valamint a házirendek minden típusú tevékenységhez elérhetők.

A tevékenység **typeProperties** szakaszában elérhető tulajdonságok az egyes tevékenységtípusok esetében eltérőek. Másolási tevékenység esetén a források és a nyelők típusaitól függően változnak.

A másolási tevékenységben ha a forrás **RelationalSource** típusú (amely ODBC-t is tartalmaz), a következő tulajdonságok érhetők el a typeProperties szakaszban:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés használatával olvashatja el az adatolvasást. |SQL-lekérdezési karakterlánc. Például: select * from Sajáttábla. |Yes |


## <a name="json-example-copy-data-from-odbc-data-store-to-azure-blob"></a>JSON-példa: adatok másolása az ODBC-adattárból az Azure-Blobba
Ez a példa JSON-definíciókat tartalmaz, amelyek segítségével létrehozhat egy folyamatot a [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy a [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával. Bemutatja, hogyan másolhat adatok egy ODBC-forrásból egy Azure-Blob Storageba. Az adatmásolási művelet azonban az [itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) megadott összes mosogatóba átmásolható a Azure Data Factoryban.

A minta a következő adatgyári entitásokat tartalmazhatja:

1. [OnPremisesOdbc](#linked-service-properties)típusú társított szolgáltatás.
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)típusú társított szolgáltatás.
3. [RelationalTable](#dataset-properties)típusú bemeneti [adatkészlet](data-factory-create-datasets.md) .
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)típusú kimeneti [adatkészlet](data-factory-create-datasets.md) .
5. [RelationalSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)használó másolási tevékenységgel rendelkező [folyamat](data-factory-create-pipelines.md) .

A minta az adatok lekérdezési eredményét egy ODBC-adattárba másolja a blobba óránként. Az ezekben a mintákban használt JSON-tulajdonságokat a mintákat követő szakaszokban ismertetjük.

Első lépésként állítsa be az adatkezelési átjárót. Az utasítások a helyszíni [helyszínek és a felhő közötti adatáthelyezést](data-factory-move-data-between-onprem-and-cloud.md) ismertetik.

**ODBC-társított szolgáltatás** Ez a példa az alapszintű hitelesítést használja. Lásd: ODBC-alapú [társított szolgáltatás](#linked-service-properties) szakasz, amely különböző típusú hitelesítésekhez használható.

```json
{
    "name": "OnPremOdbcLinkedService",
    "properties":
    {
        "type": "OnPremisesOdbc",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```

**Azure Storage társított szolgáltatás**

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

**ODBC bemeneti adatkészlet**

A minta feltételezi, hogy létrehozott egy "Sajáttábla" táblát egy ODBC-adatbázisban, és egy "timestampcolumn" nevű oszlopot tartalmaz az idősorozat-adatsorokhoz.

A "külső": "true" beállítás azt tájékoztatja a Data Factory szolgáltatást, hogy az adatkészlet kívül esik az adat-előállítón, és nem az adat-előállító tevékenysége.

```json
{
    "name": "ODBCDataSet",
    "properties": {
        "published": false,
        "type": "RelationalTable",
        "linkedServiceName": "OnPremOdbcLinkedService",
        "typeProperties": {},
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

**Azure blobkimeneti adatkészlet**

A rendszer óránként egy új blobba írja az adatbevitelt (frekvencia: óra, intervallum: 1). A blob mappájának elérési útját a rendszer dinamikusan kiértékeli a feldolgozás alatt álló szelet kezdési időpontja alapján. A mappa elérési útja a kezdési idő év, hónap, nap és óra részét használja.

```json
{
    "name": "AzureBlobOdbcDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/odbc/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Másolási tevékenység az ODBC-forrással (RelationalSource) és a blob-fogadóval (BlobSink) rendelkező folyamatokban**

A folyamat tartalmaz egy másolási tevékenységet, amely a bemeneti és a kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra van ütemezve. A folyamat JSON-definíciójában a **forrás** típusa **RelationalSource** értékre van állítva, a **fogadó típusa** pedig **BlobSink**. A **lekérdezési** tulajdonsághoz megadott SQL-lekérdezés a másoláshoz az elmúlt órában kijelöli az összes adatforrást.

```json
{
    "name": "CopyODBCToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "OdbcDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOdbcDataSet"
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
                "name": "OdbcToBlob"
            }
        ],
        "start": "2016-06-01T18:00:00Z",
        "end": "2016-06-01T19:00:00Z"
    }
}
```
### <a name="type-mapping-for-odbc"></a>Típus leképezése ODBC-hez
Ahogy azt az [adattovábbítási tevékenységek](data-factory-data-movement-activities.md) című cikk ismerteti, a másolási tevékenység az alábbi kétlépéses megközelítéssel hajtja végre az automatikus típus-konverziókat a forrás típusairól a fogadó típusokra:

1. Konvertálás natív forrásokból .NET-típusra
2. Konvertálás .NET-típusról natív fogadó típusra

Az adatok az ODBC-adattárakból való áthelyezésekor az ODBC-adattípusok a .NET-típusokra vannak leképezve az [ODBC adattípus-leképezések](/dotnet/framework/data/adonet/odbc-data-type-mappings) témakörben említettek szerint.

## <a name="map-source-to-sink-columns"></a>Forrás leképezése a fogadó oszlopokra
A forrás adatkészletben lévő oszlopok a fogadó adatkészlet oszlopaihoz való leképezésével kapcsolatos további tudnivalókért lásd: [adatkészlet oszlopainak leképezése Azure Data Factoryban](data-factory-map-columns.md).

## <a name="repeatable-read-from-relational-sources"></a>Megismételhető olvasás a rokon forrásokból
Az adatok a kapcsolódó adattárakból való másolása során érdemes megismételni a nem kívánt eredmények elkerülését. Azure Data Factory a szeleteket manuálisan is újra futtathatja. Az újrapróbálkozási szabályzatot is konfigurálhatja egy adatkészlethez, hogy a rendszer hiba esetén újrafuttassa a szeleteket. Ha egy szeletet mindkét módon újrafuttat, meg kell győződnie arról, hogy a szeletek hányszor futnak. Lásd: [megismételhető olvasás a rokon forrásokból](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources).

## <a name="troubleshoot-connectivity-issues"></a>Kapcsolati problémák hibaelhárítása
A kapcsolódási problémák elhárításához használja **adatkezelés átjáró Configuration Manager** **diagnosztika** lapját.

1. **Adatkezelés átjáró Configuration Manager** elindítása. A "C:\Program Files\Microsoft adatkezelés Gateway\1.0\Shared\ConfigManager.exe" parancsot futtathatja közvetlenül (vagy) az **átjáróra** való kereséssel, hogy megtalálja a **Microsoft adatkezelés Gateway** alkalmazásra mutató hivatkozást, ahogy az alábbi képen is látható.

    ![Keresés az átjárón](./media/data-factory-odbc-connector/search-gateway.png)
2. Váltson a **diagnosztika** lapra.

    ![Átjáródiagnosztika](./media/data-factory-odbc-connector/data-factory-gateway-diagnostics.png)
3. Válassza ki az adattár **típusát** (társított szolgáltatás).
4. Adja meg a **hitelesítést** , és adja meg a **hitelesítő adatokat** (vagy) adja meg az adattárhoz való kapcsolódáshoz használt **kapcsolati karakterláncot** .
5. Kattintson a **Kapcsolódás tesztelése** elemre az adattárhoz való kapcsolódás teszteléséhez.

## <a name="performance-and-tuning"></a>Teljesítmény és hangolás
A [másolási tevékenység teljesítményének & hangolási útmutatójában](data-factory-copy-activity-performance.md) megismerheti azokat a főbb tényezőket, amelyek hatással vannak az adatáthelyezés (másolási tevékenység) teljesítményére Azure Data Factory és az optimalizálás különféle módjaival.