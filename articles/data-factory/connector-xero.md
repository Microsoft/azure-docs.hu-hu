---
title: Adatok másolása a Xero a Azure Data Factory használatával
description: Bemutatjuk, hogy miként másolhatók adatok a Xero-ből a támogatott fogadó adattárakba egy Azure Data Factory folyamat másolási tevékenységének használatával.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/22/2020
ms.author: jingwang
ms.openlocfilehash: 89ac5645ccbb9c926bc5ff70605dd1e5de14e823
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92427615"
---
# <a name="copy-data-from-xero-using-azure-data-factory"></a>Adatok másolása a Xero a Azure Data Factory használatával

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk azt ismerteti, hogyan használható a másolási tevékenység a Azure Data Factoryban az adatok Xero való másolásához. A másolási [tevékenység áttekintő](copy-activity-overview.md) cikkében található, amely a másolási tevékenység általános áttekintését jeleníti meg.

## <a name="supported-capabilities"></a>Támogatott képességek

Ez a Xero-összekötő a következő tevékenységek esetén támogatott:

- [Másolási tevékenység](copy-activity-overview.md) [támogatott forrás/fogadó mátrixtal](copy-activity-overview.md)
- [Keresési tevékenység](control-flow-lookup-activity.md)

Az adatok a Xero bármely támogatott fogadó adattárba másolhatók. A másolási tevékenység által a forrásként/mosogatóként támogatott adattárak listáját a [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats) táblázatban tekintheti meg.

Ez a Xero-összekötő a következőket támogatja:

- Xero [privát alkalmazás](https://developer.xero.com/documentation/getting-started/getting-started-guide) , de nem nyilvános alkalmazás.
- Minden Xero-tábla (API-végpont) a "jelentések" kivételével.
- OAuth 1,0 és OAuth 2,0 hitelesítés.

A Azure Data Factory egy beépített illesztőprogramot biztosít a kapcsolat engedélyezéséhez, ezért nem kell manuálisan telepítenie az adott összekötőt használó illesztőprogramokat.

## <a name="getting-started"></a>Első lépések

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

A következő szakaszokban részletesen ismertetjük az Xero-összekötőhöz tartozó Data Factory-entitások definiálásához használt tulajdonságokat.

## <a name="linked-service-properties"></a>Társított szolgáltatás tulajdonságai

A Xero társított szolgáltatás a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A Type tulajdonságot a következőre kell beállítani: **Xero** | Igen |
| connectionProperties | A Xero való kapcsolódás módját meghatározó tulajdonságok csoportja. | Igen |
| **_Alatt `connectionProperties` :_*_ | | |
| gazda | A Xero-kiszolgáló () végpontja `api.xero.com` .  | Igen |
| authenticationType | Az engedélyezett értékek: `OAuth_2.0` és `OAuth_1.0` . | Igen |
| consumerKey | A Xero alkalmazáshoz társított fogyasztói kulcs. Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). | Igen |
| privateKey | A Xero privát alkalmazásához létrehozott. PEM fájl titkos kulcsa. a [nyilvános/titkos kulcspár létrehozása](https://developer.xero.com/documentation/auth-and-limits/create-publicprivate-key)című témakörben talál további információt. Vegye figyelembe, hogy*a privatekey. PEM a numbits 512** használatával történő létrehozásához a `openssl genrsa -out privatekey.pem 512` 1024 nem támogatott. Adja meg a. PEM fájl összes szövegét, beleértve a UNIX-sorok végződését (\n), lásd az alábbi mintát.<br/>Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). | Igen |
| tenantId | A Xero-alkalmazáshoz társított bérlői azonosító. A OAuth 2,0-hitelesítésre alkalmazható.<br>Megtudhatja, hogyan kérheti le a bérlő AZONOSÍTÓját, hogy [ellenőrizzék a jogosult bérlőket](https://developer.xero.com/documentation/oauth2/auth-flow). | Igen a OAuth 2,0-hitelesítéshez |
| refreshToken | A OAuth 2,0-hitelesítésre alkalmazható.<br/>A OAuth 2,0 frissítési token társítva van a Xero alkalmazáshoz, és a hozzáférési token frissítésére szolgál. a hozzáférési jogkivonat 30 perc után lejár. Ismerje meg, hogyan működik a Xero engedélyezési folyamata, és hogyan kérheti le a frissítési tokent [ebből a cikkből](https://developer.xero.com/documentation/oauth2/auth-flow). A frissítési jogkivonat beszerzéséhez a [offline_access hatókört](https://developer.xero.com/documentation/oauth2/scopes)kell kérnie. <br/>A **know korlátozása**: Megjegyzés a Xero alaphelyzetbe állítja a frissítési tokent, miután használta a hozzáférési jogkivonat frissítését. A működés közbeni munkaterhelések esetében az egyes másolási tevékenységek futtatása előtt érvényes frissítési tokent kell beállítania az ADF használatához.<br/>Megjelöli ezt a mezőt SecureString, hogy biztonságosan tárolja Data Factoryban, vagy [hivatkozjon a Azure Key Vault tárolt titkos kulcsra](store-credentials-in-key-vault.md). | Igen a OAuth 2,0-hitelesítéshez |
| useEncryptedEndpoints | Meghatározza, hogy az adatforrás-végpontok HTTPS protokollal legyenek titkosítva. Az alapértelmezett érték az igaz.  | Nem |
| useHostVerification | Azt adja meg, hogy az állomásnév kötelező-e a kiszolgáló tanúsítványában, hogy az megfeleljen a kiszolgáló állomásneve a TLS protokollal való csatlakozáskor. Az alapértelmezett érték az igaz.  | Nem |
| usePeerVerification | Megadja, hogy a rendszer ellenőrizze-e a kiszolgáló identitását TLS-kapcsolaton keresztül. Az alapértelmezett érték az igaz.  | Nem |

**Példa: OAuth 2,0 hitelesítés**

```json
{
    "name": "XeroLinkedService",
    "properties": {
        "type": "Xero",
        "typeProperties": {
            "connectionProperties": { 
                "host": "api.xero.com",
                "authenticationType":"OAuth_2.0", 
                "consumerKey": {
                    "type": "SecureString",
                    "value": "<consumer key>"
                },
                "privateKey": {
                    "type": "SecureString",
                    "value": "<private key>"
                },
                "tenantId": "<tenant ID>", 
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

**Példa: OAuth 1,0 hitelesítés**

```json
{
    "name": "XeroLinkedService",
    "properties": {
        "type": "Xero",
        "typeProperties": {
            "connectionProperties": {
                "host": "api.xero.com", 
                "authenticationType":"OAuth_1.0", 
                "consumerKey": {
                    "type": "SecureString",
                    "value": "<consumer key>"
                },
                "privateKey": {
                    "type": "SecureString",
                    "value": "<private key>"
                }, 
                "useEncryptedEndpoints": true,
                "useHostVerification": true,
                "usePeerVerification": true
            }
        }
    }
}
```

**Minta titkos kulcsának értéke:**

Adja meg a. PEM fájl összes szövegét, beleértve a UNIX-sorok végződését (\n).

```
"-----BEGIN RSA PRIVATE KEY-----\nMII***************************************************P\nbu****************************************************s\nU/****************************************************B\nA*****************************************************W\njH****************************************************e\nsx*****************************************************l\nq******************************************************X\nh*****************************************************i\nd*****************************************************s\nA*****************************************************dsfb\nN*****************************************************M\np*****************************************************Ly\nK*****************************************************Y=\n-----END RSA PRIVATE KEY-----"
```

## <a name="dataset-properties"></a>Adatkészlet tulajdonságai

Az adatkészletek definiálásához rendelkezésre álló csoportok és tulajdonságok teljes listáját az [adatkészletek](concepts-datasets-linked-services.md) című cikkben találja. Ez a szakasz a Xero adatkészlet által támogatott tulajdonságok listáját tartalmazza.

Az adatok Xero való másolásához állítsa az adatkészlet Type (típus) tulajdonságát **XeroObject**értékre. A következő tulajdonságok támogatottak:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | Az adatkészlet Type tulajdonságát a következőre kell beállítani: **XeroObject** | Igen |
| tableName | A tábla neve. | Nem (ha a "lekérdezés" van megadva a tevékenység forrásában) |

**Példa**

```json
{
    "name": "XeroDataset",
    "properties": {
        "type": "XeroObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Xero linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Másolási tevékenység tulajdonságai

A tevékenységek definiálásához elérhető csoportok és tulajdonságok teljes listáját a [folyamatok](concepts-pipelines-activities.md) című cikkben találja. Ez a szakasz a Xero forrás által támogatott tulajdonságok listáját tartalmazza.

### <a name="xero-as-source"></a>Xero forrásként

Az adatok Xero való másolásához állítsa a forrás típusát a másolás tevékenység **XeroSource**értékére. A másolási tevékenység **forrása** szakasz a következő tulajdonságokat támogatja:

| Tulajdonság | Leírás | Kötelező |
|:--- |:--- |:--- |
| típus | A másolási tevékenység forrásának Type tulajdonságát a következőre kell beállítani: **XeroSource** | Igen |
| lekérdezés | Az egyéni SQL-lekérdezés használatával olvassa be az adatolvasást. Például így: `"SELECT * FROM Contacts"`. | Nem (ha meg van adva a "táblanév" az adatkészletben) |

**Példa**

```json
"activities":[
    {
        "name": "CopyFromXero",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Xero input dataset name>",
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
                "type": "XeroSource",
                "query": "SELECT * FROM Contacts"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

A Xero-lekérdezés megadásakor vegye figyelembe a következőket:

- Az összetett elemeket tartalmazó táblák több táblára lesznek felosztva. A banki tranzakciók például a "listaelemek" komplex adatstruktúrával rendelkeznek, így a banki tranzakciós adat a táblára van leképezve `Bank_Transaction` , és a (z) és a (z `Bank_Transaction_Line_Items` `Bank_Transaction_ID` ) összekapcsolására szolgáló külső kulccsal.

- A Xero-adatbázis két sémán keresztül érhető el: `Minimal` (alapértelmezett) és `Complete` . A teljes séma előfeltételként szükséges hívási táblákat tartalmaz, amelyek további adatértékeket (például azonosító oszlopot) igényelnek a kívánt lekérdezés végrehajtása előtt.

Az alábbi táblázatokban ugyanazok az adatok szerepelnek a minimális és a teljes sémában. Az API-hívások számának csökkentéséhez használja a minimális sémát (alapértelmezett).

- Bank_Transactions
- Contact_Groups 
- Kapcsolattartók 
- Contacts_Sales_Tracking_Categories 
- Contacts_Phones 
- Contacts_Addresses 
- Contacts_Purchases_Tracking_Categories 
- Credit_Notes 
- Credit_Notes_Allocations 
- Expense_Claims 
- Expense_Claim_Validation_Errors
- Számlák 
- Invoices_Credit_Notes
- Előfizetések Invoices_ 
- Invoices_Overpayments 
- Manual_Journals 
- Túlfizetések 
- Overpayments_Allocations 
- Előlegek 
- Prepayments_Allocations 
- Nyugták 
- Receipt_Validation_Errors 
- Tracking_Categories

A következő táblázatok csak a teljes sémával kérhetők le:

- Complete.Bank_Transaction_Line_Items 
- Complete.Bank_Transaction_Line_Item_Tracking 
- Complete.Contact_Group_Contacts 
- Complete.Contacts_Contact_ személyek 
- Complete.Credit_Note_Line_Items 
- Complete.Credit_Notes_Line_Items_Tracking 
- Complete.Expense_Claim_ kifizetések 
- Complete.Expense_Claim_Receipts 
- Complete.Invoice_Line_Items 
- Complete.Invoices_Line_Items_Tracking
- Complete.Manual_Journal_Lines 
- Complete.Manual_Journal_Line_Tracking 
- Complete.Overpayment_Line_Items 
- Complete.Overpayment_Line_Items_Tracking 
- Complete.Prepayment_Line_Items 
- Complete.Prepayment_Line_Item_Tracking 
- Complete.Receipt_Line_Items 
- Complete.Receipt_Line_Item_Tracking 
- Complete.Tracking_Category_Options

## <a name="lookup-activity-properties"></a>Keresési tevékenység tulajdonságai

A tulajdonságok részleteinek megismeréséhez tekintse meg a [keresési tevékenységet](control-flow-lookup-activity.md).


## <a name="next-steps"></a>További lépések
A másolási tevékenység által támogatott adattárak listáját lásd: [támogatott adattárak](copy-activity-overview.md#supported-data-stores-and-formats).
