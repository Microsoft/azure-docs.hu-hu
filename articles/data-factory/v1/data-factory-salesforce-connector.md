---
title: Adatok áthelyezése a Salesforce a Data Factory használatával
description: További információ az adatok Salesforce való áthelyezéséről Azure Data Factory használatával.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: ccc20f415d13356de755af5d1d3afc5b29de72f2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100387053"
---
# <a name="move-data-from-salesforce-by-using-azure-data-factory"></a>Adatok áthelyezése a Salesforce a Azure Data Factory használatával
> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](data-factory-salesforce-connector.md)
> * [2-es verzió (aktuális verzió)](../connector-salesforce.md)

> [!NOTE]
> Ez a cikk a Data Factory 1-es verziójára vonatkozik. Ha a Data Factory szolgáltatás aktuális verzióját használja, tekintse meg a [Salesforce-összekötőt a v2-ben](../connector-salesforce.md).

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység egy Azure-beli adat-előállítóban a Salesforce-ből származó adatok másolásához a [támogatott források és a mosogatók](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tábla fogadó oszlopában felsorolt adattárba. Ez a cikk az [adattovábbítási tevékenységekről](data-factory-data-movement-activities.md) szóló cikket ismerteti, amely általános áttekintést nyújt az adatáthelyezésről másolási tevékenységgel és a támogatott adattár-kombinációkkal.

A Azure Data Factory jelenleg csak a Salesforce adatoknak a [támogatott fogadó adattárakba](data-factory-data-movement-activities.md#supported-data-stores-and-formats)való áthelyezését támogatja, de nem támogatja az adatok áthelyezését más adattárakból a Salesforce-be.

## <a name="supported-versions"></a>Támogatott verziók
Ez az összekötő a következő Salesforce-kiadásokat támogatja: Developer Edition, Professional Edition, Enterprise Edition vagy Unlimited Edition. És támogatja a másolást a Salesforce éles környezetből, a homokozóból és az egyéni tartományból.

## <a name="prerequisites"></a>Előfeltételek
* Az API-engedélyt engedélyezni kell. Lásd: [Hogyan API-hozzáférés engedélyezése a Salesforce-ben az engedélyek beállítása alapján?](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)
* Ahhoz, hogy az adatok a Salesforce-ből a helyszíni adattárakba másolva legyenek, legalább adatkezelés Gateway 2,0-es átjárót kell telepítenie a helyszíni környezetbe.

## <a name="salesforce-request-limits"></a>Salesforce kérelmek korlátai
A Salesforce mind az API-kérelmek, mind az egyidejű API-kérelmek esetében korlátozza a korlátot. Vegye figyelembe a következő szempontokat:

- Ha az egyidejű kérelmek száma meghaladja a korlátot, a szabályozás bekövetkezik, és véletlenszerű hibák jelennek meg.
- Ha a kérelmek száma meghaladja a korlátot, a Salesforce-fiók 24 óráig le lesz tiltva.

Előfordulhat, hogy mindkét esetben a "REQUEST_LIMIT_EXCEEDED" hibát is megkapja. A részletekért tekintse meg a [Salesforce fejlesztői korlátai](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) című témakör "API-kérelmek korlátai" című szakaszát.

## <a name="getting-started"></a>Első lépések
Létrehozhat egy másolási tevékenységgel rendelkező folyamatot, amely különböző eszközök/API-k használatával helyez át Salesforce származó adatokkal.

A folyamat létrehozásának legegyszerűbb módja a **Másolás varázsló** használata. Tekintse meg az [oktatóanyag: folyamat létrehozása a másolás varázslóval](data-factory-copy-data-wizard-tutorial.md) című témakört, amely gyors áttekintést nyújt a folyamat létrehozásáról az adatmásolási varázsló használatával.

A következő eszközöket is használhatja a folyamat létrehozásához: **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager template**, **.NET API** és **REST API**. A másolási tevékenységgel rendelkező folyamat létrehozásával kapcsolatos részletes utasításokat a [másolási tevékenységről szóló oktatóanyagban](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) talál.

Függetlenül attól, hogy az eszközöket vagy API-kat használja, a következő lépések végrehajtásával hozhat létre egy folyamatot, amely egy forrás adattárból egy fogadó adattárba helyezi át az adatait:

1. **Társított szolgáltatások** létrehozása a bemeneti és kimeneti adattáraknak az adat-előállítóhoz való összekapcsolásához.
2. Hozzon létre **adatkészleteket** a másolási művelet bemeneti és kimeneti adatok ábrázolásához.
3. Hozzon **létre egy másolási tevékenységgel rendelkező folyamatot** , amely egy adatkészletet bemenetként és egy adatkészlet kimenetként való elvégzéséhez szükséges.

A varázsló használatakor a rendszer automatikusan létrehozza a Data Factory entitások (társított szolgáltatások, adatkészletek és a folyamat) JSON-definícióit. Ha eszközöket/API-kat használ (kivéve a .NET API-t), akkor ezeket a Data Factory entitásokat JSON-formátumban kell megadnia. A Salesforce származó adatok másolásához használt Data Factory JSON-definíciókkal rendelkező minta esetében lásd a jelen cikk [JSON-példa: adatok másolása a Salesforce-ből az Azure blobba](#json-example-copy-data-from-salesforce-to-azure-blob) című szakaszát.

A következő szakaszokban részletesen ismertetjük a Salesforce specifikus entitások Data Factory definiálásához használt JSON-tulajdonságokat:

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai
A következő táblázat a Salesforce társított szolgáltatáshoz tartozó JSON-elemek leírásait tartalmazza.

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| típus |A Type tulajdonságot a következőre kell beállítani: **Salesforce**. |Yes |
| environmentUrl | Itt adhatja meg az Salesforce-példány URL-címét. <br><br> – Az alapértelmezett érték a "https: \/ /login.Salesforce.com". <br> – Az adatok a homokozóból való másolásához válassza a " https://test.salesforce.com " lehetőséget. <br> – Az adatok egyéni tartományból történő másolásához írja be például a következőt: "https://[tartomány]. my. Salesforce. com". |No |
| username |Adja meg a felhasználói fiók felhasználónevét. |Yes |
| jelszó |A felhasználói fiókhoz tartozó jelszó megadása. |Yes |
| securityToken |A felhasználói fiók biztonsági jogkivonatának megadása. A biztonsági jogkivonat alaphelyzetbe állításával/lekérésével kapcsolatos útmutatásért lásd a [biztonsági jogkivonat beszerzése](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) című témakört. A biztonsági jogkivonatok általános megismeréséhez lásd: [Biztonság és API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). |Yes |

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai
Az adatkészletek definiálásához elérhető csoportok és tulajdonságok teljes listáját az [adatkészletek létrehozása](data-factory-create-datasets.md) című cikkben találja. Az adatkészletek JSON-struktúrája, rendelkezésre állása és házirendje hasonló az összes adatkészlet-típushoz (Azure SQL, Azure Blob, Azure Table stb.).

A **typeProperties** szakasz különbözik az egyes adatkészletek típusaitól, és információt nyújt az adattárban található adatok helyéről. A **RelationalTable** típusú adatkészlet typeProperties szakasza a következő tulajdonságokkal rendelkezik:

| Tulajdonság | Leírás | Kötelező |
| --- | --- | --- |
| tableName |A tábla neve a Salesforce. |Nem (ha meg van adva a **RelationalSource** **lekérdezése** ) |

> [!IMPORTANT]
> Az API-név "__c" része minden egyéni objektumhoz szükséges.

![A képernyőképen az egyéni objektum definíciójának részletei láthatók, ahol megtekintheti az egyéni objektumok P I nevét.](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai
A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok létrehozása](data-factory-create-pipelines.md) című cikkben találja. A tulajdonságok, például a név, a leírás, a bemeneti és a kimeneti táblák, valamint a különböző szabályzatok minden típusú tevékenységhez elérhetők.

A tevékenység typeProperties szakaszában elérhető tulajdonságok, másfelől a tevékenységek típusától függően változnak. Másolási tevékenység esetén a források és a nyelők típusaitól függően változnak.

A másolási tevékenységben, ha a forrás típusa **RelationalSource** (amely magában foglalja a Salesforce), a typeProperties szakaszban a következő tulajdonságok érhetők el:

| Tulajdonság | Leírás | Megengedett értékek | Kötelező |
| --- | --- | --- | --- |
| lekérdezés |Az egyéni lekérdezés használatával olvashatja el az adatolvasást. |SQL-92 lekérdezés vagy [Salesforce objektum lekérdezési nyelve (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) lekérdezése. Példa: `select * from MyTable__c`. |Nem (ha meg van adva az **adatkészlet** **Táblanév** ) |

> [!IMPORTANT]
> Az API-név "__c" része minden egyéni objektumhoz szükséges.

![A képernyőképen az egyéni mezők & kapcsolatok láthatók, ahol megtekintheti az egyéni objektumok P I nevét.](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)

## <a name="query-tips"></a>Lekérdezési tippek
### <a name="retrieving-data-using-where-clause-on-datetime-column"></a>Adatok beolvasása WHERE záradék használatával DateTime oszlopban
A SOQL vagy az SQL-lekérdezés megadásakor ügyeljen arra, hogy a DateTime formátuma legyen a különbség. Például:

* **SOQL minta**: `$$Text.Format('SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= {0:yyyy-MM-ddTHH:mm:ssZ} AND LastModifiedDate < {1:yyyy-MM-ddTHH:mm:ssZ}', WindowStart, WindowEnd)`
* **SQL-minta**:
    * **A másolás varázsló használata a lekérdezés megadásához:**`$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)`
    * **A JSON-szerkesztés használata a lekérdezés megadásához (megfelelő escape-karakter):**`$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\\'{0:yyyy-MM-dd HH:mm:ss}\\'}} AND LastModifiedDate < {{ts\\'{1:yyyy-MM-dd HH:mm:ss}\\'}}', WindowStart, WindowEnd)`

### <a name="retrieving-data-from-salesforce-report"></a>Adatok beolvasása a Salesforce-jelentésből
A Salesforce-jelentésekben lévő adatok lekéréséhez adja meg a lekérdezést úgy `{call "<report name>"}` , mint például:. `"query": "{call \"TestReport\"}"`.

### <a name="retrieving-deleted-records-from-salesforce-recycle-bin"></a>Törölt rekordok beolvasása a Salesforce Lomtárból
Ha le szeretné kérdezni a Salesforce Lomtárában lévő nem törölt rekordokat, megadhatja a **"IsDeleted = 1"** kifejezést a lekérdezésben. Példa:

* Ha csak a törölt rekordokat szeretné lekérdezni, adja meg a "select * from MyTable__c **Where IsDeleted = 1**" elemet.
* Az összes olyan rekord lekérdezéséhez, amely tartalmazza a meglévőt és a törölt adatokat, adja meg a "select * from MyTable__c, **ahol a IsDeleted = 0 vagy a IsDeleted = 1**" értéket.

## <a name="json-example-copy-data-from-salesforce-to-azure-blob"></a>JSON-példa: adatok másolása a Salesforce-ből az Azure-Blobba
Az alábbi példa olyan JSON-definíciókat tartalmaz, amelyekkel a [Visual Studióval](data-factory-copy-activity-tutorial-using-visual-studio.md) vagy a [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)használatával hozhat létre folyamatokat. Bemutatják, hogyan másolhatók adatok a Salesforce-ből az Azure-Blob Storageba. Az adatmásolási művelet azonban az [itt](data-factory-data-movement-activities.md#supported-data-stores-and-formats) megadott összes mosogatóba átmásolható a Azure Data Factoryban.

A forgatókönyv megvalósításához a következő Data Factory összetevőket kell létrehoznia. A következő szakaszokban részletes információkat talál a lépésekről.

* Egy [Salesforce](#linked-service-properties) típusú társított szolgáltatás
* Egy [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) típusú társított szolgáltatás
* A [RelationalTable](#dataset-properties) típusú bemeneti [adatkészlet](data-factory-create-datasets.md)
* A [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) típusú kimeneti [adatkészlet](data-factory-create-datasets.md)
* [RelationalSource](#copy-activity-properties) és [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties) használó másolási tevékenységgel rendelkező [folyamat](data-factory-create-pipelines.md)

**Salesforce társított szolgáltatás**

Ez a példa a **Salesforce** társított szolgáltatást használja. A társított szolgáltatás által támogatott tulajdonságokért tekintse meg a [Salesforce társított szolgáltatás](#linked-service-properties) című szakaszt. A biztonsági jogkivonat alaphelyzetbe állításával/lekérésével kapcsolatos útmutatásért lásd a [biztonsági jogkivonat beszerzése](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) című témakört.

```json
{
    "name": "SalesforceLinkedService",
    "properties":
    {
        "type": "Salesforce",
        "typeProperties":
        {
            "username": "<user name>",
            "password": "<password>",
            "securityToken": "<security token>"
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
**Salesforce bemeneti adatkészlete**

```json
{
    "name": "SalesforceInput",
    "properties": {
        "linkedServiceName": "SalesforceLinkedService",
        "type": "RelationalTable",
        "typeProperties": {
            "tableName": "AllDataType__c"
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

> [!IMPORTANT]
> Az API-név "__c" része minden egyéni objektumhoz szükséges.

![A képernyőképen az egyéni objektum definíciójának részletes adatai láthatók, ahol az egyes feliratok, A többes számú címke, az objektum neve és A P I név látható.](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

**Azure-Blob kimeneti adatkészlete**

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
            "folderPath": "adfgetstarted/alltypes_c"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Másolási tevékenységgel rendelkező folyamat**

A folyamat másolási tevékenységet tartalmaz, amely a bemeneti és kimeneti adatkészletek használatára van konfigurálva, és óránkénti futásra van ütemezve. A folyamat JSON-definíciójában a **forrás** típusa **RelationalSource** értékre van állítva, a **fogadó típusa pedig** **BlobSink**.

A RelationalSource által támogatott tulajdonságok listáját a [RelationalSource típus tulajdonságainál](#copy-activity-properties) tekintheti meg.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2016-06-01T18:00:00",
        "end":"2016-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[
        {
            "name": "SalesforceToAzureBlob",
            "description": "Copy from Salesforce to an Azure blob",
            "type": "Copy",
            "inputs": [
            {
                "name": "SalesforceInput"
            }
            ],
            "outputs": [
            {
                "name": "AzureBlobOutput"
            }
            ],
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "SELECT Id, Col_AutoNumber__c, Col_Checkbox__c, Col_Currency__c, Col_Date__c, Col_DateTime__c, Col_Email__c, Col_Number__c, Col_Percent__c, Col_Phone__c, Col_Picklist__c, Col_Picklist_MultiSelect__c, Col_Text__c, Col_Text_Area__c, Col_Text_AreaLong__c, Col_Text_AreaRich__c, Col_URL__c, Col_Text_Encrypt__c, Col_Lookup__c FROM AllDataType__c"
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
> [!IMPORTANT]
> Az API-név "__c" része minden egyéni objektumhoz szükséges.

![Képernyőfelvétel: az egyéni mezők & kapcsolatok a P I nevekkel.](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)


### <a name="type-mapping-for-salesforce"></a>Típus leképezése Salesforce

| Salesforce típusa | . NET-alapú típus |
| --- | --- |
| Automatikus szám |Sztring |
| Jelölőnégyzet |Logikai |
| Pénznem |Tizedesjegy |
| Date |DateTime |
| Dátum/idő |DateTime |
| E-mail |Sztring |
| Id |Sztring |
| Keresési kapcsolat |Sztring |
| Többszörös kijelölési lista |Sztring |
| Szám |Tizedesjegy |
| Százalék |Tizedesjegy |
| Telefon |Sztring |
| Picklist |Sztring |
| Szöveg |Sztring |
| Szövegterület |Sztring |
| Szövegterület (hosszú) |Sztring |
| Szövegterület (Rich) |Sztring |
| Szöveg (titkosított) |Sztring |
| URL-cím |Sztring |

> [!NOTE]
> Ha az oszlopokat a forrás adatkészletből a fogadó adatkészletből származó oszlopokra kívánja leképezni, tekintse meg [Azure Data Factory az adatkészlet oszlopainak](data-factory-map-columns.md)

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Teljesítmény és finomhangolás
A [másolási tevékenység teljesítményének és hangolásának útmutatójában](data-factory-copy-activity-performance.md) megismerheti azokat a főbb tényezőket, amelyek hatással vannak az adatáthelyezés (másolási tevékenység) teljesítményére Azure Data Factory és az optimalizálásának különböző módjaival.
