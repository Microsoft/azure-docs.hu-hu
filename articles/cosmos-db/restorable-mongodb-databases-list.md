---
title: A MongoDB Azure Cosmos DB API-ban található helyreállítható adatbázisok listázása a REST API használatával
description: A helyreállítható fiókban található összes Azure Cosmos DB MongoDB-adatbázison végzett összes mutáció esemény-hírcsatornájának megjelenítése. Ez segítséget nyújt abban az esetben, ha az adatbázist véletlenül törölték, hogy lekérje a törlési időt.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: 5ebb0a23822074f61a16bf1d7652dba589399542
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537510"
---
# <a name="list-restorable-databases-in-azure-cosmos-db-api-for-mongodb-using-rest-api"></a>A MongoDB Azure Cosmos DB API-ban található helyreállítható adatbázisok listázása a REST API használatával

> [!IMPORTANT]
> A Azure Cosmos DB időponthoz tartozó visszaállítási funkciója (folyamatos biztonsági mentési mód) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A helyreállítható fiókban található összes Azure Cosmos DB MongoDB-adatbázison végzett összes mutáció esemény-hírcsatornájának megjelenítése. Ez segítséget nyújt abban az esetben, ha az adatbázist véletlenül törölték, hogy lekérje a törlési időt. Ehhez az API-hoz `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` engedély szükséges

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableMongodbDatabases?api-version=2020-06-01-preview
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
| 200 OK | [RestorableMongodbDatabasesListResult](#restorablemongodbdatabaseslistresult)| A művelet sikeresen befejeződött. |
| Egyéb állapotkódok | [DefaultErrorResponse](#defaulterrorresponse)| Hiba történt a művelet hibája miatt.|

## <a name="examples"></a>Példák

### <a name="cosmosdbrestorablemongodbdatabaselist"></a>CosmosDBRestorableMongodbDatabaseList

**Példa a kérelemre**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableMongodbDatabases?api-version=2020-06-01-preview
```

**Mintaválasz**

Állapotkód: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/36f09704-6be3-4f33-aa05-17b73e504c75/restorableMongodbDatabases/59c21367-b98b-4a8e-abb7-b6f46600decc",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableMongodbDatabases",
      "name": "59c21367-b98b-4a8e-abb7-b6f46600decc",
      "properties": {
        "resource": {
          "_rid": "DLB14gAAAA==",
          "eventTimestamp": "2020-09-02T19:45:03Z",
          "ownerId": "Database1",
          "ownerResourceId": "PD5DALigDgw=",
          "operationType": "Create"
        }
      }
    },
    {
      "id": "/subscriptions/2296c272-5d55-40d9-bc05-4d56dc2d7588/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableMongodbDatabases/8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableMongodbDatabases",
      "name": "8456cb17-cdb0-4c6a-8db8-d0ff3f886257",
      "properties": {
        "resource": {
          "_rid": "ESXNLAAAAA==",
          "eventTimestamp": "2020-09-02T19:53:42Z",
          "ownerId": "Database1",
          "ownerResourceId": "PD5DALigDgw=",
          "operationType": "Delete"
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>Definíciók

| Definíció | Leírás | | --- || --- | | [DefaultErrorResponse](#defaulterrorresponse) | Hiba történt a szolgáltatástól érkező válaszban. | | [ErrorResponse](#errorresponse) | Hiba válasza. | | [OperationType](#operationtype) | Enumerálás az esemény Művelettípus jelzéséhez. | | [Erőforrás](#resource) | Egy Azure Cosmos DB API MongoDB adatbázis-eseményének erőforrása | | [RestorableMongodbDatabaseGetResult](#restorablemongodbdatabasegetresult) | Egy Azure Cosmos DB API a MongoDB-adatbázis eseményéhez | | [RestorableMongodbDatabaseProperties](#restorablemongodbdatabaseproperties) | A MongoDB adatbázis-eseményhez tartozó Azure Cosmos DB API tulajdonságai | | [RestorableMongodbDatabasesListResult](#restorablemongodbdatabaseslistresult) | A List műveletre adott válasz, amely tartalmazza a MongoDB adatbázis eseményeinek és tulajdonságainak Azure Cosmos DB API-ját. |

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

### <a name="resource"></a><a id="resource"></a>Erőforrás

Egy Azure Cosmos DB API MongoDB adatbázis-eseményének erőforrása

| **Név** | **Típus** | **Leírás** |
| --- | --- | --- |
| _rid |sztring| Rendszer által generált tulajdonság. Egyedi azonosító. |
| eventTimestamp |sztring| Az az idő, amikor ez az adatbázis-esemény történt. |
| operationType |[OperationType](#operationtype)| Az adatbázis-esemény műveletének típusa.  |
| ownerId |sztring| A MongoDB-adatbázishoz tartozó Azure Cosmos DB API neve.|
| ownerResourceId |sztring| Az erőforrás-azonosító Azure Cosmos DB API a MongoDB-adatbázishoz. |

### <a name="restorablemongodbdatabasegetresult"></a><a id="restorablemongodbdatabasegetresult"></a>RestorableMongodbDatabaseGetResult

Egy Azure Cosmos DB API a MongoDB adatbázis eseményéhez

| **Név** | **Típus** | **Leírás** |
| --- | --- | --- |
| ID (Azonosító) |sztring| A Azure Resource Manager erőforrás egyedi erőforrás-azonosítója. |
| name |sztring| A Resource Manager-erőforrás neve. |
| properties |[RestorableMongodbDatabaseProperties](#restorablemongodbdatabaseproperties)| A MongoDB adatbázis eseményéhez tartozó Azure Cosmos DB API tulajdonságai. |
| típus |sztring| Az Azure-Erőforrás típusa. |

### <a name="restorablemongodbdatabaseproperties"></a><a id="restorablemongodbdatabaseproperties"></a>RestorableMongodbDatabaseProperties

Egy Azure Cosmos DB API MongoDB adatbázis-eseményének tulajdonságai

| **Név** | **Típus** | **Leírás** |
| --- | --- | --- |
| erőforrás |[Erőforrás](#resource)| Egy Azure Cosmos DB API MongoDB adatbázis-eseményének erőforrása |

### <a name="restorablemongodbdatabaseslistresult"></a><a id="restorablemongodbdatabaseslistresult"></a>RestorableMongodbDatabasesListResult

A listázási művelet válasza, amely az adatbázis-eseményeket és azok tulajdonságait tartalmazza.

| **Név** | **Típus** | **Leírás** |
| --- | --- | --- |
| érték |[RestorableMongodbDatabaseGetResult](#restorablemongodbdatabasegetresult)[]| Az adatbázis-események és tulajdonságaik listája. |

## <a name="next-steps"></a>Következő lépések

* A MongoDB Azure Cosmos DB API-ban található [helyreállítható erőforrások listázása](restorable-mongodb-resources-list.md) REST API használatával.
* A MongoDB Azure Cosmos DB API-ban lévő visszaállítható [gyűjtemények listázása](restorable-mongodb-collections-list.md) REST API használatával.
* A folyamatos biztonsági mentési mód [erőforrás-modellje](continuous-backup-restore-resource-model.md) .