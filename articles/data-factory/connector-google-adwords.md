---
title: Adatok másolása a Google AdWordsből
description: Megtudhatja, hogyan másolhat adatokat a Google AdWordsből egy Azure Data Factory adatcsatorna másolási tevékenységének használatával támogatott fogadó adattárakba.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/25/2019
ms.openlocfilehash: 79e277cb3d512ee17da866a61e9d6d66a50cd902
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81415024"
---
# <a name="copy-data-from-google-adwords-using-azure-data-factory"></a>Adatok másolása a Google AdWordsből Azure Data Factory használatával

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok Google AdWordsből való másolásához. A másolási [tevékenység áttekintő](copy-activity-overview.md) cikkében található, amely a másolási tevékenység általános áttekintését jeleníti meg.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a Google AdWords-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)


A Google AdWordsből származó adatok másolása bármely támogatott fogadó adattárba lehetséges. A másolási tevékenység által a forrásként/mosogatóként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

A Azure Data Factory egy beépített illesztőprogramot biztosít a kapcsolat engedélyezéséhez, ezért nem kell manuálisan telepítenie az adott összekötőt használó illesztőprogramokat.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyeket a Google AdWords Connector szolgáltatáshoz tartozó Data Factory-entitások definiálásához használnak.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A Google AdWords társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A Type tulajdonságot a következőre kell beállítani: **GoogleAdWords** | Igen |
| clientCustomerID | Azon AdWords-fiók ügyfél-ügyfél-azonosítója, amelyre vonatkozóan be szeretné olvasni a jelentési adatmennyiséget.  | Igen |
| developerToken | Az AdWords API-hoz való hozzáférés biztosításához használt Manager-fiókhoz társított fejlesztői jogkivonat.  Kiválaszthatja, hogy ezt a mezőt SecureString szeretné tárolni az ADF-ben való biztonságos tároláshoz, vagy a jelszó tárolásához Azure Key Vaultban, majd az ADF másolási tevékenységének lekérése az adatok másolásakor – további információ a [tárolt hitelesítő adatokról Key Vault](store-credentials-in-key-vault.md). | Igen |
| authenticationType | A hitelesítéshez használt OAuth 2,0 hitelesítési mechanizmus. A ServiceAuthentication csak saját üzemeltetésű IR-ben használható. <br/>Az engedélyezett értékek a következők: **ServiceAuthentication**, **UserAuthentication** | Igen |
| refreshToken | A Google által a UserAuthentication-hez való hozzáférés engedélyezéséhez beszerzett frissítési jogkivonat. Kiválaszthatja, hogy ezt a mezőt SecureString szeretné tárolni az ADF-ben való biztonságos tároláshoz, vagy a jelszó tárolásához Azure Key Vaultban, majd az ADF másolási tevékenységének lekérése az adatok másolásakor – további információ a [tárolt hitelesítő adatokról Key Vault](store-credentials-in-key-vault.md). | Nem |
| ügyfél-azonosító | A frissítési jogkivonat beszerzéséhez használt Google-alkalmazás ügyfél-azonosítója. Kiválaszthatja, hogy ezt a mezőt SecureString szeretné tárolni az ADF-ben való biztonságos tároláshoz, vagy a jelszó tárolásához Azure Key Vaultban, majd az ADF másolási tevékenységének lekérése az adatok másolásakor – további információ a [tárolt hitelesítő adatokról Key Vault](store-credentials-in-key-vault.md). | Nem |
| clientSecret | A frissítési jogkivonat beszerzéséhez használt Google-alkalmazás ügyfél-titka. Kiválaszthatja, hogy ezt a mezőt SecureString szeretné tárolni az ADF-ben való biztonságos tároláshoz, vagy a jelszó tárolásához Azure Key Vaultban, majd az ADF másolási tevékenységének lekérése az adatok másolásakor – további információ a [tárolt hitelesítő adatokról Key Vault](store-credentials-in-key-vault.md). | Nem |
| e-mail | A ServiceAuthentication használt szolgáltatásfiók-e-mail-azonosító, amely csak saját üzemeltetésű integrációs modulban használható.  | Nem |
| keyFilePath | A szolgáltatásfiók e-mail-címének hitelesítéséhez használt. P12 fájl teljes elérési útja, amely csak a saját üzemeltetésű integrációs modulban használható.  | Nem |
| trustedCertPath | A megbízható HITELESÍTÉSSZOLGÁLTATÓI tanúsítványokat tartalmazó. PEM fájl teljes elérési útja a kiszolgáló TLS-kapcsolaton keresztüli ellenőrzéséhez. Ez a tulajdonság csak akkor állítható be, ha a TLS-t saját üzemeltetésű IR-vel használja. Az alapértelmezett érték az IR-vel telepített hitesítésszolgáltatói. PEM fájl.  | Nem |
| useSystemTrustStore | Megadja, hogy a rendszer a rendszermegbízhatósági tárolóból vagy egy megadott PEM-fájlból kíván-e HITELESÍTÉSSZOLGÁLTATÓI tanúsítványt használni. Az alapértelmezett érték a hamis.  | Nem |

**Például**

```json
{
    "name": "GoogleAdWordsLinkedService",
    "properties": {
        "type": "GoogleAdWords",
        "typeProperties": {
            "clientCustomerID" : "<clientCustomerID>",
            "developerToken": {
                "type": "SecureString",
                "value": "<developerToken>"
            },
            "authenticationType" : "ServiceAuthentication",
            "refreshToken": {
                "type": "SecureString",
                "value": "<refreshToken>"
            },
            "clientId": {
                "type": "SecureString",
                "value": "<clientId>"
            },
            "clientSecret": {
                "type": "SecureString",
                "value": "<clientSecret>"
            },
            "email" : "<email>",
            "keyFilePath" : "<keyFilePath>",
            "trustedCertPath" : "<trustedCertPath>",
            "useSystemTrustStore" : true,
        }
    }
}

```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz a Google AdWords-adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Ha adatokat szeretne másolni a Google AdWordsből, állítsa az adatkészlet Type (típus) tulajdonságát **GoogleAdWordsObject**értékre. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **GoogleAdWordsObject** | Igen |
| tableName | A tábla neve. | Nem (ha a "lekérdezés" van megadva a tevékenység forrásában) |

**Például**

```json
{
    "name": "GoogleAdWordsDataset",
    "properties": {
        "type": "GoogleAdWordsObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<GoogleAdWords linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}

```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a Google AdWords forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="google-adwords-as-source"></a>Google AdWords forrásként

Az adatok Google AdWordsből való másolásához állítsa a forrás típusát a másolás tevékenység **GoogleAdWordsSource**. A másolási tevékenység **forrása** szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| type | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **GoogleAdWordsSource** | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés használatával olvassa be az adatolvasást. Például: `"SELECT * FROM MyTable"`. | Nem (ha meg van adva a "táblanév" az adatkészletben) |

**Például**

```json
"activities":[
    {
        "name": "CopyFromGoogleAdWords",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleAdWords input dataset name>",
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
                "type": "GoogleAdWordsSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).


## <a name="next-steps"></a>További lépések
A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
