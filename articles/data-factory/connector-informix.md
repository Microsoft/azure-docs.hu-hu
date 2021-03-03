---
title: Adatok másolása az IBM Informix-ből és-ből a Azure Data Factory használatával
description: Megtudhatja, hogyan másolhat át és átmásolhatja az információkat az IBM Informix-be egy Azure Data Factory folyamat másolási tevékenységének használatával.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/20/2021
ms.author: jingwang
ms.openlocfilehash: 23fc5cabb947f579177a26afc6baec873f2df154
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101727889"
---
# <a name="copy-data-from-and-to-ibm-informix-using-azure-data-factory"></a>Adatok másolása az IBM Informix-ből és-ből a Azure Data Factory használatával

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok egy IBM Informix-adattárból történő másolásához. A másolási [tevékenység áttekintő](copy-activity-overview.md) cikkében található, amely a másolási tevékenység általános áttekintését jeleníti meg.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez az Informix-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Az Informix-forrásból származó adatok bármely támogatott fogadó adattárba másolhatók, vagy bármely támogatott forrás adattárból átmásolhatók az Informix-gyűjtőbe. A másolási tevékenység által a forrásként/mosogatóként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.


## <a name="prerequisites"></a>Előfeltételek

Az Informix-összekötő használatához a következőket kell tennie:

- Saját üzemeltetésű Integration Runtime beállítása. További részletekért tekintse meg a saját üzemeltetésű [Integration Runtime](create-self-hosted-integration-runtime.md) szóló cikket.
- Telepítse az Informix ODBC-illesztőt az adattárhoz a Integration Runtime gépen. Az illesztőprogram telepítéséhez és beállításához tekintse meg a részleteket a következő témakörben: [Informix ODBC-illesztőprogram útmutatója](https://www.ibm.com/support/knowledgecenter/SSGU8G_11.70.0/com.ibm.odbc.doc/odbc.htm) , vagy az illesztőprogram-telepítési útmutatóért vegye fel a kapcsolatot az IBM támogatási csapatával.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük a Data Factory az Informix-összekötőhöz tartozó entitások definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

Az Informix társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A Type tulajdonságot a következőre kell beállítani: **Informix** | Igen |
| connectionString | Az ODBC-kapcsolatok karakterlánca a hitelesítő adatok kivételével. Megadhatja a kapcsolati karakterláncot, vagy használhatja a Integration Runtime gépen beállított rendszeradatforrás-nevet (az adatforrás nevét) (ennek megfelelően a társított szolgáltatás hitelesítő adatait is meg kell adnia). <br> A jelszót a Azure Key Vaultban is elhelyezheti, és lekérheti a `password` konfigurációt a kapcsolatok karakterláncáról. További részletekért tekintse meg a [hitelesítő adatok tárolása Azure Key Vaultban](store-credentials-in-key-vault.md) című témakört.| Igen |
| authenticationType | Az Informix-adattárhoz való kapcsolódáshoz használt hitelesítés típusa.<br/>Az engedélyezett értékek a következők: **Alapszintű** és **Névtelen**. | Igen |
| userName (Felhasználónév) | Ha alapszintű hitelesítést használ, adja meg a felhasználónevet. | Nem |
| jelszó | Adja meg a felhasználónévhez megadott felhasználói fiókhoz tartozó jelszót. Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). | Nem |
| hitelesítő adat | Az illesztőprogram-specifikus tulajdonság-érték formátumban megadott kapcsolati karakterlánc hozzáférési hitelesítő része. A mező megjelölése SecureString. | Nem |
| Connectvia tulajdonsággal | Az adattárhoz való kapcsolódáshoz használt [Integration Runtime](concepts-integration-runtime.md) . A saját üzemeltetésű Integration Runtime az [Előfeltételek](#prerequisites)szakaszban említettek szerint kell megadni. |Igen |

**Példa**

```json
{
    "name": "InformixLinkedService",
    "properties": {
        "type": "Informix",
        "typeProperties": {
            "connectionString": "<Informix connection string or DSN>",
            "authenticationType": "Basic",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz az Informix-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az Informix-ből származó adatok másolásához a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **InformixTable** | Igen |
| tableName | A tábla neve az Informix-ben. | Nem forrásként (ha a "Query" a tevékenység forrásában van megadva);<br/>Igen a fogadó számára |

**Példa**

```json
{
    "name": "InformixDataset",
    "properties": {
        "type": "InformixTable",
        "linkedServiceName": {
            "referenceName": "<Informix linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz az Informix-forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="informix-as-source"></a>Informix forrásként

Az Informix-ből származó adatok másolásához a másolási tevékenység **forrása** szakaszban a következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **InformixSource** | Igen |
| lekérdezés | Az egyéni lekérdezés használatával olvashatja el az adatolvasást. Példa: `"SELECT * FROM MyTable"`. | Nem (ha meg van adva a "táblanév" az adatkészletben) |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromInformix",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Informix input dataset name>",
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
                "type": "InformixSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="informix-as-sink"></a>Informix fogadóként

Az adattárba másolt adatmásoláshoz a másolási tevékenység fogadója szakasz  a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység fogadójának Type tulajdonságát a következőre kell beállítani: **InformixSink** | Igen |
| writeBatchTimeout |Várakozási idő a kötegelt beszúrási művelet befejezéséhez, mielőtt időtúllépés történt.<br/>Az engedélyezett értékek a következők: TimeSpan. Például: "00:30:00" (30 perc). |Nem |
| writeBatchSize |Beilleszti az adatmennyiséget az SQL-táblába, ha a puffer mérete eléri a writeBatchSize.<br/>Az engedélyezett értékek: Integer (sorok száma). |Nem (alapértelmezett érték: 0 – automatikusan észlelve) |
| preCopyScript |Adja meg az SQL-lekérdezést a másolási tevékenység végrehajtásához, mielőtt az egyes futtatásokban az adattárba írja az adattárban. Ezt a tulajdonságot használhatja az előre betöltött adatértékek törléséhez. |Nem |

**Példa**

```json
"activities":[
    {
        "name": "CopyToInformix",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Informix output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "InformixSink"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Következő lépések
A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
