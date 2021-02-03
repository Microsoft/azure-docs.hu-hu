---
title: Helyreállítható adatbázis-fiókok listázása Azure Cosmos DB REST API használatával
description: Az előfizetésben elérhető összes helyreállítható Azure Cosmos DB adatbázis-fiók listája.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 442f7e3abdf065377c78f71f003733ee295b312a
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527659"
---
# <a name="list-restorable-database-accounts-using-azure-cosmos-db-rest-api"></a>Helyreállítható adatbázis-fiókok listázása Azure Cosmos DB REST API használatával

Az előfizetésben elérhető összes helyreállítható Azure Cosmos DB adatbázis-fiók listája. Ehhez a híváshoz `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read` engedély szükséges.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/restorableDatabaseAccounts?api-version=2020-06-01-preview
```

## <a name="uri-parameters"></a>URI-paraméterek

| Name | In | Kötelező | Típus | Leírás |
| --- | --- | --- | --- | --- |
| **subscriptionId** | path | True (Igaz) | sztring| A cél-előfizetés azonosítója. |
| **API-Version** | lekérdezés | True (Igaz) | sztring | A művelethez használandó API-verzió. |

## <a name="responses"></a>Válaszok

| Név | Típus | Leírás |
| --- | --- | --- |
| 200 OK | [RestorableDatabaseAccountsListResult](#restorabledatabaseaccountslistresult)| A művelet sikeresen befejeződött. |
| Egyéb állapotkódok | [DefaultErrorResponse](#defaulterrorresponse)| Hiba történt a művelet hibája miatt. |

## <a name="examples"></a>Példák

### <a name="cosmosdbdatabaseaccountlist"></a>CosmosDBDatabaseAccountList

**Példa a kérelemre**

```http
GET https://management.azure.com/subscriptions/subid/providers/Microsoft.DocumentDB/restorableDatabaseAccounts?api-version=2020-06-01-preview
```

**Mintaválasz**

Állapotkód: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d",
      "name": "d9b26648-2f53-4541-b3d8-3044f4f9810d",
      "location": "West US",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts",
      "properties": {
        "accountName": "ddb1",
        "creationTime": "2020-04-11T21:56:15Z",
        "deletionTime": "2020-06-12T22:05:09Z",
        "apiType": "Sql",
        "restorableLocations": [
          {
            "locationName": "South Central US",
            "regionalDatabaseAccountInstanceId": "d7a01f78-606f-45c6-9dac-0df32f433bb5",
            "creationTime": "2020-10-30T21:13:10Z",
            "deletionTime": "2020-10-30T21:13:35Z"
          },
          {
            "locationName": "West US",
            "regionalDatabaseAccountInstanceId": "fdb43d84-1572-4697-b6e7-2bcda0c51b2c",
            "creationTime": "2020-10-30T21:13:10Z"
          }
        ]
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/4f9e6ace-ac7a-446c-98bc-194c502a06b4",
      "name": "4f9e6ace-ac7a-446c-98bc-194c502a06b4",
      "location": "East US",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts",
      "properties": {
        "accountName": "ddb2",
        "creationTime": "2020-05-01T08:05:18Z",
        "apiType": "Sql",
        "restorableLocations": [
          {
            "locationName": "South Central US",
            "regionalDatabaseAccountInstanceId": "d7a01f78-606f-45c6-9dac-0df32f433bb5",
            "creationTime": "2020-10-30T21:13:10Z",
            "deletionTime": "2020-10-30T21:13:35Z"
          },
          {
            "locationName": "West US",
            "regionalDatabaseAccountInstanceId": "fdb43d84-1572-4697-b6e7-2bcda0c51b2c",
            "creationTime": "2020-10-30T21:13:10Z"
          }
        ]
      }
    }
  ]
}
```

## <a name="definitions"></a>Definíciók

|Definíció  | Leírás|
| --- | --- |
| [ApiType](#apitype) | Enumerálás a helyreállítható adatbázis-fiók API-típusának jelzéséhez. |
| [DefaultErrorResponse](#defaulterrorresponse) | Hiba történt a szolgáltatástól érkező válaszban. |
| [ErrorResponse](#errorresponse) | Hiba válasza. |
| [RestorableDatabaseAccountGetResult](#restorabledatabaseaccountgetresult) | Egy Azure Cosmos DB helyreállítható adatbázis-fiók. |
| [RestorableDatabaseAccountsListResult](#restorabledatabaseaccountslistresult) | A List műveletre adott válasz, amely tartalmazza a helyreállítható adatbázis-fiókokat és azok tulajdonságait. |
| [RestorableLocationResource](#restorablelocationresource) | A regionális helyreállítható fiók tulajdonságai. |

### <a name="apitype"></a><a id="apitype"></a>ApiType

Enumerálás a helyreállítható adatbázis-fiók API-típusának jelzéséhez.

| **Név** | **Típus** |
| --- | --- |
| Cassandra |sztring|
| Gremlin |sztring|
| GremlinV2 |sztring|
| MongoDB |sztring|
| SQL |sztring|
| Tábla |sztring|

### <a name="defaulterrorresponse"></a><a id="defaulterrorresponse"></a>DefaultErrorResponse

Hiba történt a szolgáltatástól érkező válaszban.

| **Név** | **Típus** | **Leírás** |
| --- | --- | --- |
| error | [ErrorResponse](#errorresponse)| Hiba válasza. |

### <a name="errorresponse"></a><a id="errorresponse"></a>ErrorResponse

Hiba válasza.

| **Név** | **Típus** | **Leírás** |
| --- | --- | --- |
| code |sztring| Hibakód. |
| message |sztring| Hibaüzenet, amely azt jelzi, hogy a művelet miért nem sikerült. |

### <a name="restorabledatabaseaccountgetresult"></a><a id="restorabledatabaseaccountgetresult"></a>RestorableDatabaseAccountGetResult

Egy Azure Cosmos DB helyreállítható adatbázis-fiók.

| **Név** | **Típus** | **Leírás** |
| --- | --- | --- |
| ID (Azonosító) |sztring| A Azure Resource Manager erőforrás egyedi erőforrás-azonosítója. |
| location |sztring| Azon erőforráscsoport helye, amelyhez az erőforrás tartozik. |
| name |sztring| A Resource Manager-erőforrás neve. |
| Properties. accountName |sztring| A globális adatbázis-fiók neve |
| Properties. apiType |[ApiType](#apitype)| A helyreállítható adatbázis-fiók API-típusa. |
| Properties. creationTime |sztring| A helyreállítható adatbázis-fiók létrehozásának időpontja (ISO-8601 formátum). |
| Properties. deletionTime |sztring| A helyreállítható adatbázis-fiók törlésének időpontja (ISO-8601 formátum). |
| Properties. restorableLocations |[RestorableLocationResource](#restorablelocationresource)[]| Azon régiók listája, amelyekről az adatbázis-fiókot vissza lehet állítani. |
| típus |sztring| Az Azure-Erőforrás típusa. |

### <a name="restorabledatabaseaccountslistresult"></a><a id="restorabledatabaseaccountslistresult"></a>RestorableDatabaseAccountsListResult

A visszaállítható adatbázis-fiókokat és azok tulajdonságait tartalmazó List művelet-válasz.

| **Név** | **Típus** | **Leírás** |
| --- | --- | --- |
| érték |[RestorableDatabaseAccountGetResult](#restorabledatabaseaccountgetresult)[]| A helyreállítható adatbázis-fiókok és azok tulajdonságainak listája. |

### <a name="restorablelocationresource"></a><a id="restorablelocationresource"></a>RestorableLocationResource

A regionális helyreállítható fiók tulajdonságai.

| **Név** | **Típus** | **Leírás** |
| --- | --- | --- |
| creationTime |sztring| A regionális helyreállítható adatbázis-fiók létrehozásának időpontja (ISO-8601 formátum). |
| deletionTime |sztring| A regionális helyreállítható adatbázis-fiók törlésének időpontja (ISO-8601 formátum). |
| locationName |sztring| A regionális helyreállítható fiók helye. |
| regionalDatabaseAccountInstanceId |sztring| A regionális helyreállítható fiók példányának azonosítója. |

## <a name="next-steps"></a>Következő lépések

* [Helyreállítható adatbázis-fiókok – helyek szerinti Listázás.](restorable-database-accounts-list-by-location.md)
* A folyamatos biztonsági mentési mód [erőforrás-modellje](continuous-backup-restore-resource-model.md) .