---
title: A MongoDB Azure Cosmos DB API-ban található helyreállítható erőforrások listázása a REST API használatával
description: Az adatbázis és a gyűjtemény kombinált listájának visszaadása, amely szerepel a fiókban a megadott időbélyegen és helyen. Ez segít a forgatókönyvekben, hogy a megadott időbélyegen és helyen ellenőrizze, milyen erőforrások léteznek.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: dc90bfb6325831276b3c6171b73aebfa2877cf68
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527687"
---
# <a name="list-restorable-resources-in-azure-cosmos-db-api-for-mongodb-using-rest-api"></a>A MongoDB Azure Cosmos DB API-ban található helyreállítható erőforrások listázása a REST API használatával

Az adatbázis és a gyűjtemény kombinált listájának visszaadása, amely szerepel a fiókban a megadott időbélyegen és helyen. Ez segít a forgatókönyvekben, hogy a megadott időbélyegen és helyen ellenőrizze, milyen erőforrások léteznek. Ehhez az API-hoz `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` engedély szükséges.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbResources?api-version=2020-06-01-preview
```

Választható paraméterekkel:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbResources?api-version=2020-06-01-preview&amp;restoreLocation={restoreLocation}&amp;restoreTimestampInUtc={restoreTimestampInUtc}
```

## <a name="uri-parameters"></a>URI-paraméterek

| Name | In | Kötelező | Típus | Leírás |
| --- | --- | --- | --- | --- |
| **instanceId** | path | True (Igaz) |sztring| Egy helyreállítható adatbázis-fiók instanceId GUID azonosítója. |
| **helyen** | path | True (Igaz) | sztring| Azure Cosmos DB régió, a szavak és az egyes szavak közötti szóközökkel. |
| **subscriptionId** | path | True (Igaz) | sztring| A cél-előfizetés azonosítója. |
| **API-Version** | lekérdezés | True (Igaz) | sztring | A művelethez használandó API-verzió. |
| **restoreLocation** | lekérdezés | |sztring| Az a hely, ahol a helyreállítható erőforrások találhatók. |
| **restoreTimestampInUtc** | lekérdezés | |sztring| Az időbélyeg, amikor a helyreállítható erőforrások megtalálhatók. |

## <a name="responses"></a>Válaszok

| Név | Típus | Leírás |
| --- | --- | --- |
| 200 OK | [RestorableMongodbResourcesListResult](#restorablemongodbresourceslistresult)| A művelet sikeresen befejeződött. |
| Egyéb állapotkódok | [DefaultErrorResponse](#defaulterrorresponse)| Hiba történt a művelet hibája miatt. |


## <a name="examples"></a>Példák

### <a name="cosmosdbrestorablemongodbresourcelist"></a>CosmosDBRestorableMongodbResourceList

**Példa a kérelemre**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableMongodbResources?api-version=2020-06-01-preview&amp;restoreLocation=WestUS&amp;restoreTimestampInUtc=10/13/2020 4:56
```

**Mintaválasz**

Állapotkód: 200

```json
{
  "value": [
    {
      "databaseName": "Database1",
      "collectionNames": [
        "Collection1"
      ]
    },
    {
      "databaseName": "Database2",
      "collectionNames": [
        "Collection1",
        "Collection2"
      ]
    },
    {
      "databaseName": "Database3",
      "collectionNames": []
    }
  ]
}
```

## <a name="definitions"></a>Definíciók

| Definíció | Leírás | | --- || --- | | [DatabaseRestoreResource](#databaserestoreresource) | A visszaállítani kívánt adatbázisok. | | [DefaultErrorResponse](#defaulterrorresponse) | Hiba történt a szolgáltatástól érkező válaszban. | | [ErrorResponse](#errorresponse) | Hiba válasza. | | [RestorableMongodbResourcesListResult](#restorablemongodbresourceslistresult) | A lista műveleti válasza, amely tartalmazza a helyreállítható SQL-erőforrásokat. |

### <a name="databaserestoreresource"></a><a id="databaserestoreresource"></a>DatabaseRestoreResource

A visszaállítani kívánt adatbázisok.

| **Név** | **Típus** | **Leírás** |
| --- | --- | --- |
| collectionNames |karakterlánc []| A visszaállításhoz elérhető gyűjtemények nevei. |
| databaseName |sztring| A visszaállításhoz elérhető adatbázis neve. |

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

### <a name="restorablemongodbresourceslistresult"></a><a id="restorablemongodbresourceslistresult"></a>RestorableMongodbResourcesListResult

A List műveletre adott válasz, amely tartalmazza a visszaállítható Azure Cosmos DB API-t a MongoDB-erőforrásokhoz.

| **Név** | **Típus** | **Leírás** |
| --- | --- | --- |
| érték |[DatabaseRestoreResource](#databaserestoreresource)[]| A visszaállítható Azure Cosmos DB API-k listája MongoDB-erőforrásokhoz, beleértve az adatbázist és a gyűjtemény nevét. |

## <a name="next-steps"></a>Következő lépések

* A MongoDB Azure Cosmos DB API-ban található [helyreállítható adatbázisok listázása](restorable-mongodb-databases-list.md) REST API használatával.
* A folyamatos biztonsági mentési mód [erőforrás-modellje](continuous-backup-restore-resource-model.md) .