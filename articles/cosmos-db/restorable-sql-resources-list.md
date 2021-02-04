---
title: Helyreállítható SQL API-erőforrások listázása Azure Cosmos DB a REST API használatával
description: Az adatbázis és a tároló kombinált listájának visszaadása, amely a fiókban a megadott időbélyegen és helyen található. Ez segít a forgatókönyvekben, hogy a megadott időbélyegen és helyen ellenőrizze, milyen erőforrások léteznek.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: govindk
ms.openlocfilehash: e31423ae5afd4955ebe7acf1d456496f15f14a6b
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537289"
---
# <a name="list-restorable-sql-api-resources-in-azure-cosmos-db-using-rest-api"></a>Helyreállítható SQL API-erőforrások listázása Azure Cosmos DB a REST API használatával

> [!IMPORTANT]
> A Azure Cosmos DB időponthoz tartozó visszaállítási funkciója (folyamatos biztonsági mentési mód) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az adatbázis és a tároló kombinált listájának visszaadása, amely a fiókban a megadott időbélyegen és helyen található. Ez segít a forgatókönyvekben, hogy a megadott időbélyegen és helyen ellenőrizze, milyen erőforrások léteznek. Ehhez az API-hoz `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` engedély szükséges.

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlResources?api-version=2020-06-01-preview
```

Választható paraméterekkel:

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.DocumentDB/locations/{location}/restorableDatabaseAccounts/{instanceId}/restorableSqlResources?api-version=2020-06-01-preview&amp;restoreLocation={restoreLocation}&amp;restoreTimestampInUtc={restoreTimestampInUtc}
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
| 200 OK | [RestorableSqlResourcesListResult](#restorablesqlresourceslistresult)| A művelet sikeresen befejeződött. |
| Egyéb állapotkódok | [DefaultErrorResponse](#defaulterrorresponse)| Hiba történt a művelet hibája miatt. |

## <a name="examples"></a>Példák

### <a name="cosmosdbrestorablesqlresourcelist"></a>CosmosDBRestorableSqlResourceList

**Példa a kérelemre**

```http
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/WestUS/restorableDatabaseAccounts/d9b26648-2f53-4541-b3d8-3044f4f9810d/restorableSqlResources?api-version=2020-06-01-preview&amp;restoreLocation=WestUS&amp;restoreTimestampInUtc=10/13/2020 4:56
```

**Mintaválasz**

Állapotkód: 200

```json
{
  "value": [
    {
      "databaseName": "Database1",
      "collectionNames": [
        "Container1"
      ]
    },
    {
      "databaseName": "Database2",
      "collectionNames": [
        "Container1",
        "Container2"
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

| Definíció | Leírás | | --- || --- | | [DatabaseRestoreResource](#databaserestoreresource) | A visszaállítani kívánt adatbázisok. | | [DefaultErrorResponse](#defaulterrorresponse) | Hiba történt a szolgáltatástól érkező válaszban. | | [ErrorResponse](#errorresponse) | Hiba válasza. | | [RestorableSqlResourcesListResult](#restorablesqlresourceslistresult) | A lista műveleti válasza, amely tartalmazza a helyreállítható SQL-erőforrásokat. |

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

### <a name="restorablesqlresourceslistresult"></a><a id="restorablesqlresourceslistresult"></a>RestorableSqlResourcesListResult

A lista műveleti válasza, amely tartalmazza a helyreállítható SQL-erőforrásokat.

| **Név** | **Típus** | **Leírás** |
| --- | --- | --- |
| érték |[DatabaseRestoreResource](#databaserestoreresource)[]| A helyreállítható SQL-erőforrások listája, beleértve az adatbázist és a gyűjtemény nevét. |

## <a name="next-steps"></a>Következő lépések

* Az Azure Cosmos SQL API-ban található [helyreállítható adatbázisok listázása](restorable-sql-databases-list.md) REST API használatával.
* A folyamatos biztonsági mentési mód [erőforrás-modellje](continuous-backup-restore-resource-model.md) .