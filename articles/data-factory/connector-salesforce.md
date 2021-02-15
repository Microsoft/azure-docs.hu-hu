---
title: Adatok másolása a és a Salesforce
description: Megtudhatja, hogyan másolhat adatokból a Salesforce-ből a támogatott fogadó adattárakba vagy a támogatott forrás-adattárakból a Salesforce egy másolási tevékenységgel egy adatfeldolgozó-folyamaton keresztül.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/02/2021
ms.openlocfilehash: d820be66c70ae336361de7209722c4018ffd5077
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100392170"
---
# <a name="copy-data-from-and-to-salesforce-by-using-azure-data-factory"></a>Adatok másolása a és a Salesforce Azure Data Factory használatával

> [!div class="op_single_selector" title1="Válassza ki az Ön által használt Data Factory-szolgáltatás verzióját:"]
> * [1-es verzió](v1/data-factory-salesforce-connector.md)
> * [Aktuális verzió](connector-salesforce.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok Salesforce történő másolásához. A másolási [tevékenység áttekintő](copy-activity-overview.md) cikkében található, amely a másolási tevékenység általános áttekintését jeleníti meg.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a Salesforce-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Az adatok a Salesforce bármely támogatott fogadó adattárba másolhatók. Az adatok másolása bármely támogatott forrás-adattárból Salesforce is elvégezhető. A másolási tevékenység által forrásként vagy nyelőként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

Ez a Salesforce-összekötő a következőket támogatja:

- Salesforce fejlesztői, Professional, Enterprise vagy Unlimited kiadásokat.
- Adatok másolása a és a rendszerből a Salesforce éles környezetbe, a homokozóba és az egyéni tartományba.

Az Salesforce-összekötő a Salesforce REST/tömeges API-ra épül. Alapértelmezés szerint a Salesforce származó adatok másolása során az összekötő a [v45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) -t használja, és az adatok mérete alapján automatikusan KIVÁLASZTJA a REST és a tömeges API-kat – ha az eredményhalmaz nagy, a tömeges API a jobb teljesítmény érdekében használatos. az adatSalesforceba való íráskor az összekötő a tömeges API [V40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) használja. Explicit módon beállíthatja az API-verziót, amely a társított szolgáltatásban található [ `apiVersion` tulajdonságon](#linked-service-properties) keresztüli adatolvasásra és-írásra szolgál.

## <a name="prerequisites"></a>Előfeltételek

Az API-engedélyt engedélyezni kell a Salesforce-ben. További információ: [API-hozzáférés engedélyezése a Salesforce-ben az engedélyek beállítása alapján](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)

## <a name="salesforce-request-limits"></a>Salesforce kérelmek korlátai

A Salesforce mind az API-kérelmek, mind az egyidejű API-kérelmek esetében korlátozza a korlátot. Vegye figyelembe a következő szempontokat:

- Ha az egyidejű kérelmek száma meghaladja a korlátot, a szabályozás bekövetkezik, és véletlenszerű hibák jelennek meg.
- Ha a kérelmek száma meghaladja a korlátot, a Salesforce-fiók 24 óráig blokkolva lesz.

Előfordulhat, hogy mindkét esetben "REQUEST_LIMIT_EXCEEDED" hibaüzenetet is kap. További információkért tekintse meg a [Salesforce fejlesztői korlátainak](https://developer.salesforce.com/docs/atlas.en-us.218.0.salesforce_app_limits_cheatsheet.meta/salesforce_app_limits_cheatsheet/salesforce_app_limits_platform_api.htm)"API-kérelmek korlátai" című szakaszát.

## <a name="get-started"></a>Bevezetés

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük a Salesforce-összekötőhöz tartozó Data Factory entitások definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A Salesforce társított szolgáltatás a következő tulajdonságokat támogatja.

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus |A Type tulajdonságot **Salesforce** értékre kell beállítani. |Yes |
| environmentUrl | Itt adhatja meg az Salesforce-példány URL-címét. <br> – Az alapértelmezett érték `"https://login.salesforce.com"` . <br> – Adatok másolása a homokozóból, a következőt kell megadnia: `"https://test.salesforce.com"` . <br> – Az adatok egyéni tartományból történő másolásához például a következőt kell megadnia: `"https://[domain].my.salesforce.com"` . |No |
| username |Adja meg a felhasználói fiók felhasználónevét. |Yes |
| jelszó |A felhasználói fiókhoz tartozó jelszó megadása.<br/><br/>Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). |Yes |
| securityToken |A felhasználói fiók biztonsági jogkivonatának megadása. <br/><br/>A biztonsági jogkivonatok általános megismeréséhez lásd: [Biztonság és API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm). A biztonsági jogkivonatot csak akkor lehet kihagyni, ha hozzáadja a Integration Runtime IP- [címét a megbízható IP-címek listájához](https://developer.salesforce.com/docs/atlas.en-us.securityImplGuide.meta/securityImplGuide/security_networkaccess.htm) a Salesforce-on. Azure IR használatakor tekintse meg [Azure Integration Runtime IP-címeket](azure-integration-runtime-ip-addresses.md).<br/><br/>A biztonsági jogkivonat beszerzésével és alaphelyzetbe állításával kapcsolatos utasításokért lásd: [biztonsági jogkivonat beszerzése](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm). Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). |No |
| apiVersion | Itt adhatja meg a használni kívánt REST/tömeges API-verziót, például: Salesforce `48.0` . Alapértelmezés szerint az összekötő [v45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) használ az adatok Salesforce való másolásához, és a [V40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) használatával másolja az adatok Salesforce. | No |
| Connectvia tulajdonsággal | Az adattárhoz való csatlakozáshoz használt [integrációs](concepts-integration-runtime.md) modul. Ha nincs megadva, az alapértelmezett Azure Integration Runtime használja. | No |

**Példa: hitelesítő adatok tárolása Data Factoryban**

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            },
            "securityToken": {
                "type": "SecureString",
                "value": "<security token>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Példa: hitelesítő adatok tárolása Key Vaultban**

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of password in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            },
            "securityToken": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of security token in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz a Salesforce adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Ha adatokat szeretne másolni a és a Salesforce, állítsa az adatkészlet Type (típus) tulajdonságát **SalesforceObject** értékre. A következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A Type tulajdonságot **SalesforceObject** értékre kell beállítani.  | Yes |
| objectApiName | Az Salesforce-objektum neve, amelyből az adatok beolvashatók. | Nem, forrás, igen, fogadó |

> [!IMPORTANT]
> Az **API-név** "__c" része minden egyéni objektumhoz szükséges.

![Data Factory Salesforce-kapcsolatok API-neve](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**Példa**

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "type": "SalesforceObject",
        "typeProperties": {
            "objectApiName": "MyTable__c"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Salesforce linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

>[!NOTE]
>A visszamenőleges kompatibilitás érdekében: amikor adatokat másol a Salesforce-ből, ha az előző "RelationalTable" típusú adatkészletet használja, akkor az továbbra is működik, miközben megjelenik egy javaslat, amely az új "SalesforceObject" típusra vált.

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | Az adatkészlet Type tulajdonságát **RelationalTable** értékre kell állítani. | Yes |
| tableName | A tábla neve a Salesforce. | Nem (ha a tevékenység forrásában a "Query" érték van megadva) |

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a Salesforce forrás és a fogadó által támogatott tulajdonságok listáját tartalmazza.

### <a name="salesforce-as-a-source-type"></a>Salesforce

Az adatok Salesforce való másolásához állítsa a forrás típusát a másolás tevékenység **SalesforceSource** értékére. A másolási tevékenység **forrása** szakaszban a következő tulajdonságok támogatottak.

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység forrásának Type tulajdonságát **SalesforceSource** értékre kell állítani. | Yes |
| lekérdezés |Az egyéni lekérdezés használatával olvashatja el az adatolvasást. Használhatja a [Salesforce Object Query Language (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm) lekérdezést vagy az SQL-92 lekérdezést. További tippeket a [lekérdezési tippek](#query-tips) szakaszban talál. Ha a lekérdezés nincs megadva, a rendszer az adatkészlet "objectApiName" részében megadott Salesforce objektum összes adatát beolvassa. | Nem (ha meg van adva a "objectApiName" az adatkészletben) |
| readBehavior | Azt jelzi, hogy le kell-e kérdezni a meglévő rekordokat, vagy az összes rekordot, beleértve a törölt fájlokat is. Ha nincs megadva, az alapértelmezett viselkedés a korábbi. <br>Engedélyezett értékek: **lekérdezés** (alapértelmezett), **queryAll**.  | No |

> [!IMPORTANT]
> Az **API-név** "__c" része minden egyéni objektumhoz szükséges.

![Data Factory Salesforce-kapcsolatok API-Neveinak listája](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

**Példa**

```json
"activities":[
    {
        "name": "CopyFromSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Salesforce input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SalesforceSource",
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

>[!NOTE]
>Visszamenőleges kompatibilitás esetén: Ha a Salesforce adatok másolása után az előző "RelationalSource" típusú másolatot használja, a forrás továbbra is működik, miközben megjelenik egy javaslat, amely az új "SalesforceSource" típusra vált.

### <a name="salesforce-as-a-sink-type"></a>Salesforce

Az adatmásoláshoz a másolási tevékenységben állítsa be a Salesforce a **SalesforceSink**. A másolási tevékenység fogadója szakaszban a következő  tulajdonságok támogatottak.

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység fogadójának Type tulajdonságát **SalesforceSink** értékre kell állítani. | Yes |
| writeBehavior | A művelet írási viselkedése.<br/>Az engedélyezett értékek: **Insert** és **Upsert**. | Nem (az alapértelmezett érték a Beszúrás) |
| externalIdFieldName | A upsert művelet külső azonosító mezőjének neve. A megadott mezőt "külső azonosító mezőként" kell definiálni a Salesforce objektumban. A megfelelő bemeneti adatokban nem szerepelhet NULL érték. | Igen a "Upsert" |
| writeBatchSize | Az egyes kötegekben Salesforce írt adatsorok száma. | Nem (az alapértelmezett érték 5 000) |
| ignoreNullValues | Azt jelzi, hogy az írási művelet során a rendszer figyelmen kívül hagyja-e a bemeneti adatokból származó NULL értékeket.<br/>Az engedélyezett értékek értéke **igaz** és **hamis**.<br>- **True (igaz**): a célobjektum nem módosult, ha upsert vagy frissítési műveletet végez. Definiált alapértelmezett érték beszúrása egy beszúrási művelet végrehajtásakor.<br/>- **Hamis**: a upsert vagy frissítési művelet végrehajtásakor a cél objektumban lévő ADATFRISSÍTÉS NULL értékűre. Szúrjon be egy NULL értéket a beszúrási művelet végrehajtásakor. | Nem (az alapértelmezett érték hamis) |

**Példa: Salesforce-fogadó egy másolási tevékenységben**

```json
"activities":[
    {
        "name": "CopyToSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Salesforce output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SalesforceSink",
                "writeBehavior": "Upsert",
                "externalIdFieldName": "CustomerId__c",
                "writeBatchSize": 10000,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="query-tips"></a>Lekérdezési tippek

### <a name="retrieve-data-from-a-salesforce-report"></a>Adatok beolvasása egy Salesforce-jelentésből

A Salesforce-jelentésekben lévő adatok beolvasása a lekérdezés megadásával végezhető el `{call "<report name>"}` . Például: `"query": "{call \"TestReport\"}"`.

### <a name="retrieve-deleted-records-from-the-salesforce-recycle-bin"></a>Törölt rekordok lekérése a Salesforce Lomtárból

Ha le szeretné kérdezni a Salesforce lomtárának helyreállított törölt rekordjait, megadhatja a következőt: `readBehavior` `queryAll` . 

### <a name="difference-between-soql-and-sql-query-syntax"></a>A SOQL és az SQL-lekérdezési szintaxis közötti különbség

Az adatok Salesforce való másolása során SOQL-lekérdezést vagy SQL-lekérdezést használhat. Vegye figyelembe, hogy ez a kettő eltérő szintaxissal és funkció-támogatással rendelkezik, ne keverje hozzá. Azt javasoljuk, hogy használja a SOQL lekérdezést, amelyet natív módon támogat a Salesforce. A következő táblázat a fő különbségeket sorolja fel:

| Syntax | SOQL mód | SQL-mód |
|:--- |:--- |:--- |
| Oszlop kijelölése | Fel kell sorolni a lekérdezésbe másolandó mezőket, például: `SELECT field1, filed2 FROM objectname` | `SELECT *` az oszlop kijelölése mellett is támogatott. |
| Idézőjelek | A beiktatott/objektumnév nem lehet idézőjelben. | A mező-vagy objektumnév idézőjelek lehetnek, például: `SELECT "id" FROM "Account"` |
| Dátum és idő formátuma |  Tekintse meg [a részleteket és](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_dateformats.htm) a példákat a következő szakaszban. | Tekintse meg [a részleteket és](/sql/odbc/reference/develop-app/date-time-and-timestamp-literals) a példákat a következő szakaszban. |
| Logikai értékek | Például: `False` `True` `SELECT … WHERE IsDeleted=True` . | 0 vagy 1, például: `SELECT … WHERE IsDeleted=1` . |
| Oszlop átnevezése | Nem támogatott. | Támogatott, például: `SELECT a AS b FROM …` . |
| Kapcsolat | Támogatott, például: `Account_vod__r.nvs_Country__c` . | Nem támogatott. |

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>Az adatlekérdezés a DateTime oszlop where záradékának használatával

Ha megadja a SOQL vagy az SQL-lekérdezést, ügyeljen a DateTime formátum különbségére. Például:

* **SOQL minta**: `SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **SQL-minta**: `SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

### <a name="error-of-malformed_query-truncated"></a>MALFORMED_QUERY hiba: csonkolt

Ha a "MALFORMED_QUERY: csonkolt" hibaüzenet jelenik meg, általában az az oka, hogy a JunctionIdList Type oszlopa van az adathalmazban, és a Salesforce korlátozást tartalmaz az ilyen típusú, nagy mennyiségű sort tartalmazó adatmennyiségre. A megoldáshoz próbálja meg kizárni a JunctionIdList oszlopot, vagy korlátozza a másolandó sorok számát (több másolási tevékenységre is particionálhat).

## <a name="data-type-mapping-for-salesforce"></a>Adattípusok leképezése Salesforce

Az adatok Salesforce-ből való másolása során a rendszer a következő leképezéseket használja a Salesforce adattípusok között az ideiglenes adattípusok Data Factory. Ha szeretné megtudni, hogyan képezi le a másolási tevékenység a forrás sémát és az adattípust a fogadónak, tekintse meg a [séma-és adattípus-leképezéseket](copy-activity-schema-and-type-mapping.md).

| Salesforce adattípusa | Data Factory időközi adattípus |
|:--- |:--- |
| Automatikus szám |Sztring |
| Jelölőnégyzet |Logikai |
| Pénznem |Tizedesjegy |
| Date |DateTime |
| Dátum/idő |DateTime |
| E-mail |Sztring |
| ID (Azonosító) |Sztring |
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

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Következő lépések
A Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).