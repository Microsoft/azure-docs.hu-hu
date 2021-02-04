---
title: Visszaállítható SQL API-tárolók listázása Azure Cosmos DB a REST API használatával
description: Az adott adatbázishoz tartozó összes Azure Cosmos DB SQL-tárolón végzett összes mutáció esemény-hírcsatornájának megjelenítése. Ez segít abban a helyzetben, hogy a tárolót véletlenül törölték.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: bba00b10d1a24dc29c1e986de1f0144c00350f5d
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537424"
---
# <a name="list-restorable-sql-api-containers-in-azure-cosmos-db-using-rest-api"></a>Visszaállítható SQL API-tárolók listázása Azure Cosmos DB a REST API használatával

> [!IMPORTANT]
> A Azure Cosmos DB időponthoz tartozó visszaállítási funkciója (folyamatos biztonsági mentési mód) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az adott adatbázishoz tartozó összes Azure Cosmos DB SQL-tárolón végzett összes mutáció esemény-hírcsatornájának megjelenítése. Ez segít abban a helyzetben, hogy a tárolót véletlenül törölték. Ehhez az API-hoz `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` engedély szükséges

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlContainers?api-version=2020-06-01-preview
```

Választható paraméterekkel:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlContainers?api-version=2020-06-01-preview&amp;restorableSqlDatabaseRid={restorableSqlDatabaseRid}
```

## <a name="uri-parameters"></a>URI-paraméterek

| Name | In | Kötelező | Típus | Leírás |
| --- | --- | --- | --- | --- |
| **instanceId** | path | True (Igaz) |sztring| Egy helyreállítható adatbázis-fiók instanceId GUID azonosítója. |
| **helyen** | path | True (Igaz) | sztring| Azure Cosmos DB régió, a szavak és az egyes szavak közötti szóközökkel. |
| **subscriptionId** | path | True (Igaz) | sztring| A cél-előfizetés azonosítója. |
| **API-Version** | lekérdezés | True (Igaz) | sztring | A művelethez használandó API-verzió. |
| **restorableSqlDatabaseRid** | lekérdezés | |sztring| Az SQL-adatbázis erőforrás-azonosítója. |

## <a name="responses"></a>Válaszok

| Név | Típus | Leírás |
| --- | --- | --- |
| 200 OK | [RestorableSqlContainersListResult](#restorablesqlcontainerslistresult)| A művelet sikeresen befejeződött. |
| Egyéb állapotkódok | [DefaultErrorResponse](#defaulterrorresponse)| Hiba történt a művelet hibája miatt. |

## <a name="examples"></a>Példák

### <a name="cosmosdbrestorablesqlcontainerlist"></a>CosmosDBRestorableSqlContainerList

**Példa a kérelemre**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableSqlContainers?api-version=2020-06-01-preview&amp;restorableSqlDatabaseRid=3fu-hg==
```

**Mintaválasz**

Állapotkód: 200

```json
{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableSqlContainers/79609a98-3394-41f8-911f-cfab0c075c86",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlContainers",
      "name": "79609a98-3394-41f8-911f-cfab0c075c86",
      "properties": {
        "resource": {
          "_rid": "zAyAPQAAAA==",
          "eventTimestamp": "2020-10-13T04:56:42Z",
          "ownerId": "Container1",
          "ownerResourceId": "V18LoLrv-qA=",
          "operationType": "Create",
          "container": {
            "id": "Container1",
            "indexingPolicy": {
              "indexingMode": "Consistent",
              "automatic": true,
              "includedPaths": [
                {
                  "path": "/*"
                },
                {
                  "path": "/\"_ts\"/?"
                }
              ],
              "excludedPaths": [
                {
                  "path": "/\"_etag\"/?"
                }
              ]
            },
            "conflictResolutionPolicy": {
              "mode": "LastWriterWins",
              "conflictResolutionPath": "/_ts",
              "conflictResolutionProcedure": ""
            },
            "_rid": "V18LoLrv-qA=",
            "_self": "dbs/V18LoA==/colls/V18LoLrv-qA=/",
            "_etag": "\"00003e00-0000-0700-0000-5f85338a0000\""
          }
        }
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDb/locations/westus/restorableDatabaseAccounts/98a570f2-63db-4117-91f0-366327b7b353/restorableSqlContainers/e85298a1-c631-4726-825e-a7ca092e9098",
      "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restorableSqlContainers",
      "name": "e85298a1-c631-4726-825e-a7ca092e9098",
      "properties": {
        "resource": {
          "_rid": "PrArcgAAAA==",
          "eventTimestamp": "2020-10-13T05:03:27Z",
          "ownerId": "Container1",
          "ownerResourceId": "V18LoLrv-qA=",
          "operationType": "Replace",
          "container": {
            "id": "Container1",
            "indexingPolicy": {
              "indexingMode": "Consistent",
              "automatic": true,
              "includedPaths": [
                {
                  "path": "/*"
                },
                {
                  "path": "/\"_ts\"/?"
                }
              ],
              "excludedPaths": [
                {
                  "path": "/\"_etag\"/?"
                }
              ]
            },
            "defaultTtl": 12345,
            "conflictResolutionPolicy": {
              "mode": "LastWriterWins",
              "conflictResolutionPath": "/_ts",
              "conflictResolutionProcedure": ""
            },
            "_rid": "V18LoLrv-qA=",
            "_self": "dbs/V18LoA==/colls/V18LoLrv-qA=/",
            "_etag": "\"00004400-0000-0700-0000-5f85351f0000\""
          }
        }
      }
    }
  ]
}
```

## <a name="definitions"></a>Definíciók

| Definíció | Leírás | | --- || --- | | [Tároló](#container) | SQL Container erőforrás-objektum Azure Cosmos DB | | [DefaultErrorResponse](#defaulterrorresponse) | Hiba történt a szolgáltatástól érkező válaszban. | | [ErrorResponse](#errorresponse) | Hiba válasza. | | [OperationType](#operationtype) | Enumerálás az esemény Művelettípus jelzéséhez. | | [Erőforrás](#resource) | Egy Azure Cosmos DB SQL-tároló eseményének erőforrása | | [RestorableSqlContainerGetResult](#restorablesqlcontainergetresult) | Egy Azure Cosmos DB SQL Container esemény | | [RestorableSqlContainerProperties](#restorablesqlcontainerproperties) | Egy Azure Cosmos DB SQL Container esemény tulajdonságai | | [RestorableSqlContainersListResult](#restorablesqlcontainerslistresult) | A List műveletre adott válasz, amely az SQL-tároló eseményeit és azok tulajdonságait tartalmazza. |

### <a name="container"></a><a id="container"></a>Tároló

SQL Container erőforrás-objektum Azure Cosmos DB

| **Név**  |  **Típus**  |  **Leírás** | | --- || --- | ---| | _etag | karakterlánc | Egy rendszer által generált tulajdonság, amely az optimista Egyidejűség-vezérléshez szükséges erőforrás-ETAG jelöli. | | _rid | karakterlánc | Rendszer által generált tulajdonság. Egyedi azonosító. | | _self | karakterlánc | Egy rendszer által generált tulajdonság, amely a tároló-erőforrás címezhető elérési útját határozza meg. | | _ts | | Egy rendszer által generált tulajdonság, amely az erőforrás utolsó frissített időbélyegét jelöli. | | AZONOSÍTÓ | karakterlánc | Az Azure Cosmos DB SQL-tároló neve |

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
| Létrehozás |sztring|tároló-létrehozási esemény|
| Törlés |sztring|tároló törlési eseménye|
| Csere |sztring|tároló módosítási eseménye|
| SystemOperation |sztring|a szolgáltatás által aktivált tároló-módosítási esemény. Ezt az eseményt a felhasználó nem indítja el|

### <a name="resource"></a><a id="resource"></a>Erőforrás

Egy Azure Cosmos DB SQL-tároló eseményének erőforrása

| **Név** | **Típus** | **Leírás** |
| --- | --- | --- |
| _rid |sztring| Rendszer által generált tulajdonság. Egyedi azonosító. |
| tároló |[Tároló](#container)| SQL Container erőforrás-objektum Azure Cosmos DB |
| eventTimestamp |sztring| A tároló eseményének időpontja. |
| operationType |[OperationType](#operationtype)| A tároló eseményének Művelettípus. |
| ownerId |sztring| Az SQL-tároló neve. |
| ownerResourceId |sztring| Az SQL-tároló erőforrás-azonosítója. |

### <a name="restorablesqlcontainergetresult"></a><a id="restorablesqlcontainergetresult"></a>RestorableSqlContainerGetResult

Egy Azure Cosmos DB SQL-tároló esemény

| **Név** | **Típus** | **Leírás** |
| --- | --- | ---
| ID (Azonosító) |sztring| A Azure Resource Manager erőforrás egyedi erőforrás-azonosítója. |
| name |sztring| Az Azure Resource Manager erőforrás neve. |
| properties |[RestorableSqlContainerProperties](#restorablesqlcontainerproperties)| Az SQL-tároló eseményének tulajdonságai. |
| típus |sztring| Az Azure-Erőforrás típusa. |

### <a name="restorablesqlcontainerproperties"></a><a id="restorablesqlcontainerproperties"></a>RestorableSqlContainerProperties

Egy Azure Cosmos DB SQL-tároló eseményének tulajdonságai

| **Név** | **Típus** | **Leírás** |
| --- | --- | --- |
| erőforrás |[Erőforrás](#resource)| Egy Azure Cosmos DB SQL-tároló eseményének erőforrása |

### <a name="restorablesqlcontainerslistresult"></a><a id="restorablesqlcontainerslistresult"></a>RestorableSqlContainersListResult

A List műveletre adott válasz, amely az SQL-tároló eseményeit és azok tulajdonságait tartalmazza.

| **Név** | **Típus** | **Leírás** |
| --- | --- | --- |
| érték |[RestorableSqlContainerGetResult](#restorablesqlcontainergetresult)[]| Az SQL-tároló eseményeinek és azok tulajdonságainak listája. |

## <a name="next-steps"></a>Következő lépések

* A MongoDB Azure Cosmos DB API-ban található [helyreállítható adatbázisok listázása](restorable-mongodb-databases-list.md) REST API használatával.
* A folyamatos biztonsági mentési mód [erőforrás-modellje](continuous-backup-restore-resource-model.md) .