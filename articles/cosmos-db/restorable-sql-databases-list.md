---
title: Helyreállítható SQL API-adatbázisok listázása Azure Cosmos DB a REST API használatával
description: Megjeleníti a visszaállítható fiókban található összes Azure Cosmos DB SQL-adatbázis összes mutációjának esemény-hírcsatornáját. Ez segítséget nyújt abban az esetben, ha az adatbázist véletlenül törölték, hogy lekérje a törlési időt.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: d3d72cff5fcfeed17d60e2f856be4adc1a983819
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527518"
---
# <a name="list-restorable-sql-api-databases-in-azure-cosmos-db-using-rest-api"></a>Helyreállítható SQL API-adatbázisok listázása Azure Cosmos DB a REST API használatával

Megjeleníti a visszaállítható fiókban található összes Azure Cosmos DB SQL-adatbázis összes mutációjának esemény-hírcsatornáját. Ez segítséget nyújt abban az esetben, ha az adatbázist véletlenül törölték, hogy lekérje a törlési időt. Ehhez az API-hoz `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` engedély szükséges

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlDatabases?api-version=2020-06-01-preview
```

## <a name="uri-parameters"></a>URI-paraméterek

| Name | In | Kötelező | Típus | Leírás |
| --- | --- | --- | --- | --- |
| **instanceId** | path | True (Igaz) |sztring| Egy helyreállítható adatbázis-fiók instanceId GUID azonosítója. |
| **helyen** | path | True (Igaz) | sztring| Azure Cosmos DB régió, a szavak és az egyes szavak közötti szóközökkel. |
| **subscriptionId** | path | True (Igaz) | sztring| A cél-előfizetés azonosítója. |
| **API-Version** | lekérdezés | True (Igaz) | sztring | A művelethez használandó API-verzió. |

## <a name="responses"></a>Válaszok

| Név | Típus | Leírás |
| --- | --- | --- |
| 200 OK | [RestorableSqlDatabasesListResult](#restorablesqldatabaseslistresult)| A művelet sikeresen befejeződött. |
| Egyéb állapotkódok | [DefaultErrorResponse](#defaulterrorresponse)| Hiba történt a művelet hibája miatt. |

## <a name="examples"></a>Példák

### <a name="cosmosdbrestorablesqldatabaselist"></a>CosmosDBRestorableSqlDatabaseList

**Kérésminta**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlDatabases?api-version=2020-06-01-preview
```

**Mintaválasz**

Állapotkód: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/36f09704-6be3-4f33-aa05-17b73e504c75/restorableSqlDatabases/59c21367-b98b-4a8e-abb7-b6f46600decc",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlDatabases",
      "name": "59c21367-b98b-4a8e-abb7-b6f46600decc",
      "properties": {
        "resource": {
          "_rid": "DLB14gAAAA==",
          "eventTimestamp": "2020-09-02T19:45:03Z",
          "ownerId": "Database1",
          "ownerResourceId": "3fu-hg==",
          "operationType": "Create",
          "database": {
            "id": "Database1",
            "_rid": "3fu-hg==",
            "_self": "dbs/3fu-hg==/",
            "_etag": "\"0000c20a-0000-0700-0000-5f4ff63f0000\"",
            "_colls": "colls/",
            "_users": "users/"
          }
        }
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlDatabases/8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlDatabases",
      "name": "8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "properties": {
        "resource": {
          "_rid": "ESXNLAAAAA==",
          "eventTimestamp": "2020-09-02T19:53:42Z",
          "ownerId": "Database1",
          "ownerResourceId": "3fu-hg==",
          "database": {
            "id": "Database1",
            "_rid": "3fu-hg==",
            "_self": "dbs/3fu-hg==/",
            "_etag": "\"0000c20a-0000-0700-0000-5f4ff63f0000\"",
            "_colls": "colls/",
            "_users": "users/",
            "_ts": 1599075903
          },
          "operationType": "Delete"
        }
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlDatabases/2c07991b-9c7c-4e85-be68-b18c1f2ff326",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlDatabases",
      "name": "2c07991b-9c7c-4e85-be68-b18c1f2ff326",
      "properties": {
        "resource": {
          "_rid": "aXFqUQAAAA==",
          "eventTimestamp": "2020-09-02T19:53:15Z",
          "ownerId": "Database2",
          "ownerResourceId": "0SziSg==",
          "database": {
            "id": "Database2",
            "_rid": "0SziSg==",
            "_self": "dbs/0SziSg==/",
            "_etag": "\"0000ca0a-0000-0700-0000-5f4ff82b0000\"",
            "_colls": "colls/",
            "_users": "users/"
          },
          "operationType": "Create"
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>Definíciók

| Definíció | Leírás | | --- || --- | | [Adatbázis](#database) | Az SQL Database erőforrás-objektumának Azure Cosmos DBa | | [DefaultErrorResponse](#defaulterrorresponse) | Hiba történt a szolgáltatástól érkező válaszban. | | [ErrorResponse](#errorresponse) | Hiba válasza. | | [OperationType](#operationtype) | Enumerálás az esemény Művelettípus jelzéséhez. | | [Erőforrás](#resource) | Egy Azure Cosmos DB SQL Database-esemény erőforrása | | [RestorableSqlDatabaseGetResult](#restorablesqldatabasegetresult) | Egy Azure Cosmos DB SQL Database-esemény | | [RestorableSqlDatabaseProperties](#restorablesqldatabaseproperties) | Egy Azure Cosmos DB SQL Database-esemény tulajdonságai | | [RestorableSqlDatabasesListResult](#restorablesqldatabaseslistresult) | A List műveletre adott válasz, amely az SQL Database-eseményeket és azok tulajdonságait tartalmazza. |

### <a name="database"></a><a id="database"></a>Adatbázis

Azure Cosmos DB SQL-adatbázis erőforrás-objektuma

| **Név**  |  **Típus**  |  **Leírás** | | --- || --- | ---| | _colls | karakterlánc | Egy rendszer által generált tulajdonság, amely a gyűjtemények erőforrás címezhető elérési útját adta meg. | | _etag | karakterlánc | Egy rendszer által generált tulajdonság, amely az optimista Egyidejűség-vezérléshez szükséges erőforrás-ETAG jelöli. | | _rid | karakterlánc | Rendszer által generált tulajdonság. Egyedi azonosító. | | _self | karakterlánc | Egy rendszer által generált tulajdonság, amely az adatbázis-erőforrás címezhető elérési útját határozza meg. | | _ts | | Egy rendszer által generált tulajdonság, amely az erőforrás utolsó frissített időbélyegét jelöli. | | _users | karakterlánc | Egy rendszer által generált tulajdonság, amely a felhasználó erőforrásának címezhető elérési útját határozza meg. | | AZONOSÍTÓ | karakterlánc | Az Azure Cosmos DB SQL Database neve |

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
| Létrehozás |sztring|adatbázis-létrehozási esemény|
| Törlés |sztring|adatbázis-törlési esemény|
| Csere |sztring|adatbázis-módosítási esemény|
| SystemOperation |sztring|a System által aktivált adatbázis-módosítási esemény. Ezt az eseményt a felhasználó nem indítja el|

### <a name="resource"></a><a id="resource"></a>Erőforrás

Egy Azure Cosmos DB SQL Database-esemény erőforrása

| **Név** | **Típus** | **Leírás** |
| --- | --- | --- |
| _rid |sztring| Rendszer által generált tulajdonság. Egyedi azonosító. |
| adatbázis |[Adatbázis](#database)| Azure Cosmos DB SQL-adatbázis erőforrás-objektuma |
| eventTimestamp |sztring| Az az idő, amikor ez az adatbázis-esemény történt. |
| operationType |[OperationType](#operationtype)| Az adatbázis-esemény műveletének típusa. |
| ownerId |sztring| Az SQL-adatbázis neve. |
| ownerResourceId |sztring| Az SQL-adatbázis erőforrás-azonosítója. |

### <a name="restorablesqldatabasegetresult"></a><a id="restorablesqldatabasegetresult"></a>RestorableSqlDatabaseGetResult

Egy Azure Cosmos DB SQL Database-esemény

| **Név** | **Típus** | **Leírás** |
| --- | --- | --- |
| ID (Azonosító) |sztring| A Azure Resource Manager erőforrás egyedi erőforrás-azonosítója. |
| name |sztring| Az Azure Resource Manager erőforrás neve. |
| properties | [RestorableSqlDatabaseProperties](#restorablesqldatabaseproperties)| Egy SQL Database-esemény tulajdonságai. |
| típus |sztring| Az Azure-Erőforrás típusa. |

### <a name="restorablesqldatabaseproperties"></a><a id="restorablesqldatabaseproperties"></a>RestorableSqlDatabaseProperties

Egy Azure Cosmos DB SQL Database-esemény tulajdonságai

| **Név** | **Típus** | **Leírás** |
| --- | --- | --- |
| erőforrás |[Erőforrás](#resource)| Egy Azure Cosmos DB SQL Database-esemény erőforrása |

### <a name="restorablesqldatabaseslistresult"></a><a id="restorablesqldatabaseslistresult"></a>RestorableSqlDatabasesListResult

A List műveletre adott válasz, amely az SQL Database-eseményeket és azok tulajdonságait tartalmazza.

| **Név** | **Típus** | **Leírás** |
| --- | --- | --- |
| érték |[RestorableSqlDatabaseGetResult](#restorablesqldatabasegetresult)[]| Az SQL Database eseményeinek és azok tulajdonságainak listája. |

## <a name="next-steps"></a>Következő lépések

* Visszaállítható [tárolók listázása](restorable-sql-containers-list.md) Azure Cosmos db SQL API-ban REST API használatával.
* A folyamatos biztonsági mentési mód [erőforrás-modellje](continuous-backup-restore-resource-model.md) .