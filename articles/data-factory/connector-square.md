---
title: Adatok másolása a négyzetből (előzetes verzió)
description: Megtudhatja, hogyan másolhat adatokat a Square-ből a támogatott fogadó adattárakba egy Azure Data Factory folyamat másolási tevékenységének használatával.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/03/2020
ms.openlocfilehash: ac10e42d338e0ddd44cb3c07709645a69653808d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100384792"
---
# <a name="copy-data-from-square-using-azure-data-factory-preview"></a>Adatok másolása a térről a Azure Data Factory használatával (előzetes verzió)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factory az adatok négyzetből való másolásához. A másolási [tevékenység áttekintő](copy-activity-overview.md) cikkében található, amely a másolási tevékenység általános áttekintését jeleníti meg.

> [!IMPORTANT]
> Ez az összekötő jelenleg előzetes verzióban érhető el. Kipróbálhatja, és visszajelzést küldhet nekünk. Ha függőséget szeretne felvenni a megoldásában található előzetes verziójú összekötőkre, lépjen kapcsolatba az [Azure-támogatással](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a négyzet-összekötő a következő tevékenységek esetében támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Az adatok a Square-ből bármilyen támogatott fogadó adattárba másolhatók. A másolási tevékenység által a forrásként/mosogatóként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

A Azure Data Factory egy beépített illesztőprogramot biztosít a kapcsolat engedélyezéséhez, ezért nem kell manuálisan telepítenie az adott összekötőt használó illesztőprogramokat.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük azokat a tulajdonságokat, amelyek a négyzetes összekötőhöz tartozó Data Factory definiálására szolgálnak.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A Square társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A Type tulajdonságot a következőre kell beállítani: **Square** | Yes |
| connectionProperties | A Square-hez való kapcsolódás módját meghatározó tulajdonságok csoportja. | Yes |
| ***Alatt `connectionProperties` :*** | | |
| gazda | A négyzet-példány URL-címe (pl. mystore.mysquare.com)  | Yes |
| ügyfél-azonosító | A Square-alkalmazáshoz társított ügyfél-azonosító.  | Yes |
| clientSecret | A Square-alkalmazáshoz társított ügyfél-titok. Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). | Yes |
| accessToken | A négyzetből beszerzett hozzáférési jogkivonat. Korlátozott hozzáférést biztosít egy négyzetes fiókhoz, ha hitelesített felhasználót kér az explicit engedélyekhez. A OAuth hozzáférési tokenek a kiállítottak után 30 nappal lejárnak, de a frissítési tokenek nem járnak le. A hozzáférési jogkivonatok frissítési token használatával frissíthetők.<br>Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md).  | Yes |
| refreshToken | A négyzetből beszerzett frissítési jogkivonat. Új hozzáférési jogkivonatok beszerzésére szolgál, ha az aktuális lejárat lejár.<br>Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). | No |
| useEncryptedEndpoints | Meghatározza, hogy az adatforrás-végpontok HTTPS protokollal legyenek titkosítva. Az alapértelmezett érték az igaz.  | No |
| useHostVerification | Megadja, hogy szükséges-e az állomásnév a kiszolgáló tanúsítványában, hogy egyezzen a kiszolgáló állomásneve a TLS-kapcsolaton keresztüli csatlakozáskor. Az alapértelmezett érték az igaz.  | No |
| usePeerVerification | Megadja, hogy a rendszer ellenőrizze-e a kiszolgáló identitását TLS-kapcsolaton keresztül. Az alapértelmezett érték az igaz.  | No |

A Square két típusú hozzáférési tokent támogat: a **személyes** és a **OAuth**.

- A személyes hozzáférési jogkivonatok korlátlan számú kapcsolódási API-hozzáférést kapnak a saját Square-fiók erőforrásaihoz.
- A OAuth hozzáférési jogkivonatok a hitelesített és hatókörű csatlakozási API-hozzáférés bármely négyzetes fiókhoz való elérésére szolgálnak. Akkor használja őket, ha az alkalmazás a fiók tulajdonosai nevében más négyzetes fiókok erőforrásaihoz fér hozzá. A OAuth hozzáférési jogkivonatok is használhatók a saját Square-fiók erőforrásainak eléréséhez.

Data Factory a személyes hozzáférési jogkivonaton keresztüli hitelesítés csak a szükséges `accessToken` , a OAuth-n keresztüli hitelesítéshez `accessToken` pedig szükséges `refreshToken` . Megtudhatja, hogyan kérheti [le a](https://developer.squareup.com/docs/build-basics/access-tokens)hozzáférési tokent innen.

**Példa**

```json
{
    "name": "SquareLinkedService",
    "properties": {
        "type": "Square",
        "typeProperties": {
            "connectionProperties": {
                "host": "<e.g. mystore.mysquare.com>", 
                "clientId": "<client ID>", 
                "clientSecrect": {
                    "type": "SecureString",
                    "value": "<clientSecret>"
                }, 
                "accessToken": {
                    "type": "SecureString",
                    "value": "<access token>"
                }, 
                "refreshToken": {
                    "type": "SecureString",
                    "value": "<refresh token>"
                }, 
                "useEncryptedEndpoints": true, 
                "useHostVerification": true, 
                "usePeerVerification": true 
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz a négyzetes adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az adatok négyzetből való másolásához állítsa az adatkészlet Type (típus) tulajdonságát **SquareObject** értékre. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **SquareObject** | Yes |
| tableName | A tábla neve. | Nem (ha a "lekérdezés" van megadva a tevékenység forrásában) |

**Példa**

```json
{
    "name": "SquareDataset",
    "properties": {
        "type": "SquareObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Square linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a négyzet forrása által támogatott tulajdonságok listáját tartalmazza.

### <a name="square-as-source"></a>Négyzet forrásként

Az adatok négyzetből való másolásához állítsa a forrás típusát a másolás tevékenység **SquareSource**. A másolási tevékenység **forrása** szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **SquareSource** | Yes |
| lekérdezés | Az egyéni SQL-lekérdezés használatával olvassa be az adatolvasást. Példa: `"SELECT * FROM Business"`. | Nem (ha meg van adva a "táblanév" az adatkészletben) |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromSquare",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Square input dataset name>",
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
                "type": "SquareSource",
                "query": "SELECT * FROM Business"
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

## <a name="next-steps"></a>Következő lépések
A Azure Data Factory a másolási tevékenység által forrásként és nyelőként támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
