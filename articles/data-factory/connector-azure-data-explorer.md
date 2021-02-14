---
title: Adatok másolása az Azure Adatkezelőba vagy onnan
description: Megtudhatja, hogyan másolhat adatok az Azure Adatkezelőba vagy onnan egy Azure Data Factory folyamat másolási tevékenységének használatával.
ms.author: orspodek
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2020
ms.openlocfilehash: 16126e8b9e5c34529016018273edcf65a31e2280
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100379981"
---
# <a name="copy-data-to-or-from-azure-data-explorer-by-using-azure-data-factory"></a>Adatok másolása az Azure Adatkezelőba vagy onnan a Azure Data Factory használatával

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok [Azure-Adatkezelőba](/azure/data-explorer/data-explorer-overview)való másolásához. A másolási [tevékenység áttekintő](copy-activity-overview.md) cikkében található, amely általános áttekintést nyújt a másolási tevékenységről.

>[!TIP]
>A Azure Data Factory és az Azure Adatkezelő integrációja általában további információ az [Azure-adatkezelő és a Azure Data Factory integrálásáról](/azure/data-explorer/data-factory-integration).

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az Azure Adatkezelő-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Bármely támogatott forrás adattárból az Azure Adatkezelőba másolhat adatok. Az Azure Adatkezelőról bármilyen támogatott fogadó adattárba másolhatja az adatok adatait is. A másolási tevékenység által forrásként vagy nyelőként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

>[!NOTE]
>A 3,14-es és újabb verziókban a helyszíni adattárakban a helyi adattárból vagy az Azure-ba történő adatmásolással Adatkezelő.

Az Azure Adatkezelő-összekötővel a következőket teheti:

* Az Adatmásolás Azure Active Directory (Azure AD) alkalmazás-jogkivonat-hitelesítéssel egy **egyszerű szolgáltatás** használatával.
* Forrásként egy KQL-(Kusto-) lekérdezés használatával lekérheti az adatgyűjtést.
* Fogadóként fűzze hozzá az adatkészlethez.

## <a name="getting-started"></a>Első lépések

>[!TIP]
>Az Azure Adatkezelő Connector bemutatása: [adatok másolása az Azure-ba vagy az](/azure/data-explorer/data-factory-load-data) Azure-ba Adatkezelő az [adatkezelő adatbázisból](/azure/data-explorer/data-factory-template)az Azure-ba való tömeges másolás Azure Data Factory használatával

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük az Azure Adatkezelő connectorhoz tartozó Data Factory entitások definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az Azure Adatkezelő-összekötő az egyszerű szolgáltatásnév hitelesítését használja. Az alábbi lépéseket követve beszerezhet egy egyszerű szolgáltatásnevet és engedélyeket adhat meg:

1. Az [alkalmazás regisztrálása Azure ad-Bérlővel](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)című témakör lépéseit követve regisztrálja Azure Active Directory alkalmazás entitását. Jegyezze fel a következő értékeket, amelyeket a társított szolgáltatás definiálásához használ:

    - Alkalmazásazonosító
    - Alkalmazás kulcsa
    - Bérlőazonosító

2. Adja meg az egyszerű szolgáltatásnév számára az Azure Adatkezelő megfelelő engedélyeit. A szerepkörökkel és engedélyekkel, valamint az engedélyek kezelésével kapcsolatos részletes információkért tekintse meg az [Azure adatkezelő Database-engedélyek kezelése](/azure/data-explorer/manage-database-permissions) című témakört. Általánosságban a következőket kell tennie:

    - **Forrásként** legalább az **adatbázis-megjelenítői** szerepkört adja meg az adatbázisnak
    - Fogadóként legalább az **adatbázis** betöltési szerepkörét adja meg az adatbázisnak

>[!NOTE]
>Ha a Data Factory felhasználói felületet használja a létrehozáshoz, a bejelentkezési felhasználói fiók az Azure Adatkezelő-fürtök,-adatbázisok és-táblák listázására szolgál. Ha nincs engedélye ezekhez a műveletekhez, manuálisan adja meg a nevet.

Az Azure Adatkezelő társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A **Type** tulajdonságot **AzureDataExplorer** értékre kell beállítani. | Yes |
| endpoint | Az Azure Adatkezelő-fürt végponti URL-címe, amelynek formátuma: `https://<clusterName>.<regionName>.kusto.windows.net` . | Yes |
| adatbázis | Az adatbázis neve. | Yes |
| Bérlő | Adja meg a bérlői adatokat (tartománynevet vagy bérlői azonosítót), amely alatt az alkalmazás található. Ez a [Kusto-kapcsolatok karakterláncának](/azure/kusto/api/connection-strings/kusto#application-authentication-properties)"Authority id" néven ismert. Lekéréséhez vigye az egérmutatót a Azure Portal jobb felső sarkában. | Yes |
| servicePrincipalId | Határozza meg az alkalmazás ügyfél-AZONOSÍTÓját. Ez az úgynevezett "HRE Application Client ID" a Kusto-alapú [kapcsolatok karakterláncában](/azure/kusto/api/connection-strings/kusto#application-authentication-properties). | Yes |
| servicePrincipalKey | Az alkalmazás kulcsának meghatározása. Ez az úgynevezett "HRE Application Key" néven ismert a [Kusto-kapcsolatok karakterláncában](/azure/kusto/api/connection-strings/kusto#application-authentication-properties). Megjelöli ezt a mezőt **SecureString** , hogy biztonságosan tárolja Data Factoryban, vagy [a Azure Key Vault tárolt biztonságos](store-credentials-in-key-vault.md)adattárolásra hivatkozik. | Yes |

**Társított szolgáltatás tulajdonságai – példa:**

```json
{
    "name": "AzureDataExplorerLinkedService",
    "properties": {
        "type": "AzureDataExplorer",
        "typeProperties": {
            "endpoint": "https://<clusterName>.<regionName>.kusto.windows.net ",
            "database": "<database name>",
            "tenant": "<tenant name/id e.g. microsoft.onmicrosoft.com>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listájáért lásd: [Adatkészletek Azure Data Factoryban](concepts-datasets-linked-services.md). Ez a szakasz felsorolja az Azure Adatkezelő adatkészlet által támogatott tulajdonságokat.

Ha adatokat szeretne másolni az Azure Adatkezelőba, állítsa az adatkészlet Type (típus) tulajdonságát **AzureDataExplorerTable** értékre.

A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A **Type** tulajdonságot **AzureDataExplorerTable** értékre kell beállítani. | Yes |
| tábla | Annak a táblának a neve, amelyre a társított szolgáltatás hivatkozik. | Igen a fogadó számára; Nem a forráshoz |

**Adatkészlet tulajdonságai – példa:**

```json
{
   "name": "AzureDataExplorerDataset",
    "properties": {
        "type": "AzureDataExplorerTable",
        "typeProperties": {
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Data Explorer linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját a következő témakörben találja: [folyamatok és tevékenységek a Azure Data Factoryban](concepts-pipelines-activities.md). Ez a szakasz azoknak a tulajdonságoknak a listáját tartalmazza, amelyeket az Azure Adatkezelő a forrásokhoz és a mosdók támogatásához.

### <a name="azure-data-explorer-as-source"></a>Azure-Adatkezelő forrásként

Az adatok Azure-Adatkezelőból való másolásához állítsa a **Type (típus** ) tulajdonságot a másolási tevékenység forrása **AzureDataExplorerSource** értékre. A másolási tevékenység **forrása** szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység forrásának Type tulajdonságát a **következőre** kell beállítani: **AzureDataExplorerSource** | Yes |
| lekérdezés | Egy [KQL-formátumban](/azure/kusto/query/)megadott írásvédett kérelem. Hivatkozásként használja az egyéni KQL-lekérdezést. | Yes |
| queryTimeout | A lekérdezési kérelem időtúllépése előtti várakozási idő. Az alapértelmezett érték 10 perc (00:10:00); az engedélyezett maximális érték 1 óra (01:00:00). | No |
| nincs csonkítás | Azt jelzi, hogy le kell-e vágni a visszaadott eredményhalmaz értékét. Alapértelmezés szerint a rendszer a 500 000-es rekordok vagy a 64 megabájt (MB) után csonkolja az eredményt. Erősen ajánlott a csonkítás a tevékenység megfelelő működésének biztosítása érdekében. |No |

>[!NOTE]
>Alapértelmezés szerint az Azure Adatkezelő forrásának mérete 500 000 rekord vagy 64 MB. Ha az összes rekordot csonkítás nélkül szeretné lekérni, megadhatja a `set notruncation;` lekérdezés elején. További információ: [lekérdezési korlátok](/azure/kusto/concepts/querylimits).

**Példa**

```json
"activities":[
    {
        "name": "CopyFromAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataExplorerSource",
                "query": "TestTable1 | take 10",
                "queryTimeout": "00:10:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Explorer input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

### <a name="azure-data-explorer-as-sink"></a>Azure-Adatkezelő fogadóként

Az Azure Adatkezelőba való adatmásoláshoz állítsa a másolási tevékenység fogadójának Type (típus) tulajdonságát a **AzureDataExplorerSink** értékre. A másolási tevékenység fogadója szakasz a következő  tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység fogadójának Type tulajdonságát a **következőre** kell beállítani: **AzureDataExplorerSink**. | Yes |
| ingestionMappingName | Egy előre létrehozott [hozzárendelés](/azure/kusto/management/mappings#csv-mapping) neve egy Kusto táblán. Ha az oszlopokat a forrásról az Azure-ra Adatkezelő (amely az [összes támogatott forrás-és formátumra](copy-activity-overview.md#supported-data-stores-and-formats)vonatkozik, beleértve a CSV/JSON/Avro formátumokat), használhatja a másolási tevékenység [oszlop-hozzárendelést](copy-activity-schema-and-type-mapping.md) (implicit módon, név vagy explicit módon konfiguráltként) és/vagy az Azure adatkezelő-hozzárendeléseket. | No |
| additionalProperties | Az Azure Adatkezelő fogadó által már nem beállított betöltési tulajdonságok megadásához használható tulajdonság-táska. Különösen hasznos lehet betöltési címkék megadására. További információ az [Azure-beli adatfeldolgozási dokumentációból](/azure/data-explorer/ingestion-properties). | No |

**Példa**

```json
"activities":[
    {
        "name": "CopyToAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataExplorerSink",
                "ingestionMappingName": "<optional Azure Data Explorer mapping name>",
                "additionalProperties": {<additional settings for data ingestion>}
            }
        },
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Explorer output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

További információ a tulajdonságokról: [keresési tevékenység](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Következő lépések

* A Azure Data Factory másolási tevékenység által forrásként és fogadóként támogatott adattárak listájáért lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).

* További információ az [adatok Azure Data Factoryról az Azure Adatkezelőba való másolásáról](/azure/data-explorer/data-factory-load-data).