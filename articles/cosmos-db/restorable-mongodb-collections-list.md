---
title: Helyreállítható gyűjtemények listázása Azure Cosmos DB MongoDB API-ban – REST API
description: Megjeleníti az adott adatbázishoz tartozó összes Azure Cosmos DB MongoDB-gyűjtemény összes mutációjának esemény-hírcsatornáját. Ez segít abban a helyzetben, hogy a tárolót véletlenül törölték.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 83e1c7c27e8c5d179c4ec6aa4ba64b3367294a48
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527702"
---
# <a name="list-restorable-collections-in-azure-cosmos-db-api-for-mongodb-using-rest-api"></a>A MongoDB Azure Cosmos DB API-ban található helyreállítható gyűjtemények listázása REST API használatával

A MongoDB-gyűjtemények összes Azure Cosmos DB API-ban végzett összes mutációjának megjelenítése egy adott adatbázison belül. Ez segít abban a helyzetben, hogy a tárolót véletlenül törölték. Ehhez az API-hoz `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` engedély szükséges

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbCollections?api-version=2020-06-01-preview
```

Választható paraméterekkel:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbCollections?api-version=2020-06-01-preview&amp;restorableMongodbDatabaseRid={restorableMongodbDatabaseRid}
```

## <a name="uri-parameters"></a>URI-paraméterek

| Name | In | Kötelező | Típus | Leírás |
| --- | --- | --- | --- | --- |
| **instanceId** | path | True (Igaz) |sztring| Egy helyreállítható adatbázis-fiók instanceId GUID azonosítója. |
| **helyen** | path | True (Igaz) | sztring| Azure Cosmos DB régió, a szavak és az egyes szavak közötti szóközökkel. |
| **subscriptionId** | path | True (Igaz) | sztring| A cél-előfizetés azonosítója. |
| **API-Version** | lekérdezés | True (Igaz) | sztring | A művelethez használandó API-verzió. |
| **restorableMongodbDatabaseRid** | lekérdezés | |sztring| A MongoDB-adatbázishoz tartozó Azure Cosmos DB API erőforrás-azonosítója. |

## <a name="responses"></a>Válaszok

| Név | Típus | Leírás |
| --- | --- | --- |
| 200 OK | [RestorableMongodbCollectionsListResult](#restorablemongodbcollectionslistresult)| A művelet sikeresen befejeződött. |
| Egyéb állapotkódok | [DefaultErrorResponse](#defaulterrorresponse)| Hiba történt a művelet hibája miatt.|

## <a name="examples"></a>Példák

### <a name="cosmosdbrestorablemongodbcollectionlist"></a>CosmosDBRestorableMongodbCollectionList

**Példa a kérelemre**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableMongodbCollections?api-version=2020-06-01-preview&amp;restorableMongodbDatabaseRid=PD5DALigDgw=
```

**Mintaválasz**

Állapotkód: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableMongodbCollections/79609a98-3394-41f8-911f-cfab0c075c86",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableMongodbCollections",
      "name": "79609a98-3394-41f8-911f-cfab0c075c86",
      "properties": {
        "resource": {
          "_rid": "zAyAPQAAAA==",
          "eventTimestamp": "2020-10-13T04:56:42Z",
          "ownerId": "Collection1",
          "ownerResourceId": "V18LoLrv-qA=",
          "operationType": "Create"
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>Definíciók

|Definíció  | Leírás|
| --- | --- |
| [DefaultErrorResponse](#defaulterrorresponse) | Hiba történt a szolgáltatástól érkező válaszban. |
| [ErrorResponse](#errorresponse) | Hiba válasza. |
| [OperationType](#operationtype) | Enumerálás az esemény Művelettípus jelzéséhez. |
| [Erőforrás](#resource) | A MongoDB-gyűjtési eseményhez tartozó Azure Cosmos DB API erőforrása |
| [RestorableMongodbCollectionGetResult](#restorablemongodbcollectiongetresult) | Egy Azure Cosmos DB API a MongoDB-gyűjtési eseményhez |
| [RestorableMongodbCollectionProperties](#restorablemongodbcollectionproperties) | A MongoDB-gyűjtési eseményhez tartozó Azure Cosmos DB API tulajdonságai |
| [RestorableMongodbCollectionsListResult](#restorablemongodbcollectionslistresult) | A lista műveleti válasza, amely tartalmazza a gyűjtemény eseményeit és azok tulajdonságait. |

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

### <a name="operationtype"></a><a id="operationtype"></a>OperationType

Enumerálás az esemény Művelettípus jelzéséhez.

| **Név** | **Típus** | **Leírás** |
| --- | --- | --- |
| Létrehozás |sztring|gyűjtemény-létrehozási esemény|
| Törlés |sztring|gyűjtemény törlési eseménye|
| Csere |sztring|gyűjtemény módosítási eseménye|

### <a name="resource"></a><a id="resource"></a>Erőforrás

Egy Azure Cosmos DB MongoDB-gyűjtési esemény erőforrása

| **Név** | **Típus** | **Leírás** |
| --- | --- | --- |
| _rid |sztring| Rendszer által generált tulajdonság. Egyedi azonosító. |
| eventTimestamp |sztring| A gyűjtemény eseményének időpontja. |
| operationType |[OperationType](#operationtype)|  A gyűjteményi esemény Művelettípus. |
| ownerId |sztring| A MongoDB-gyűjtemény neve.|
| ownerResourceId |sztring| A MongoDB-gyűjtemény erőforrás-azonosítója. |

### <a name="restorablemongodbcollectiongetresult"></a><a id="restorablemongodbcollectiongetresult"></a>RestorableMongodbCollectionGetResult

Egy Azure Cosmos DB MongoDB-gyűjtési esemény

| **Név** | **Típus** | **Leírás** |
| --- | --- | --- |
| ID (Azonosító) |sztring| A Azure Resource Manager erőforrás egyedi erőforrás-azonosítója. |
| name |sztring| A Resource Manager-erőforrás neve. |
| properties |[RestorableMongodbCollectionProperties](#restorablemongodbcollectionproperties)| Egy gyűjteményi esemény tulajdonságai. |
| típus |sztring| Az Azure-Erőforrás típusa. |

### <a name="restorablemongodbcollectionproperties"></a><a id="restorablemongodbcollectionproperties"></a>RestorableMongodbCollectionProperties

Egy Azure Cosmos DB MongoDB-gyűjtési esemény tulajdonságai

| **Név** | **Típus** | **Leírás** |
| --- | --- | --- |
| erőforrás | [Erőforrás](#resource)| A MongoDB-gyűjtési eseményhez tartozó Azure Cosmos DB API erőforrása |

### <a name="restorablemongodbcollectionslistresult"></a><a id="restorablemongodbcollectionslistresult"></a>RestorableMongodbCollectionsListResult

A lista műveleti válasza, amely tartalmazza a gyűjtemény eseményeit és azok tulajdonságait.

| **Név** | **Típus** | **Leírás** |
| --- | --- | --- |
| érték |[RestorableMongodbCollectionGetResult](#restorablemongodbcollectiongetresult)[]| Azure Cosmos DB API-k listája a MongoDB-gyűjtési eseményekhez és azok tulajdonságaihoz. |

## <a name="next-steps"></a>Következő lépések

* A MongoDB Azure Cosmos DB API-ban található [helyreállítható adatbázisok listázása](restorable-mongodb-databases-list.md) REST API használatával.
* A folyamatos biztonsági mentési mód [erőforrás-modellje](continuous-backup-restore-resource-model.md) .