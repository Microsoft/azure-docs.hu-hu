---
title: Az Azure Cosmos DB időponthoz tartozó visszaállítási funkció erőforrás-modellje.
description: Ez a cikk az Azure Cosmos DB időponthoz kötött visszaállítási funkciójához tartozó erőforrásmodellt mutatja be. Ismerteti azokat a folyamatos biztonsági mentést támogató paramétereket, valamint az SQL-hez és MongoDB-hez készült Azure Cosmos DB API-fiókok esetében visszaállítható erőforrásokat.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: edf60a5c454d34a2424ef7981b02952ffbfd3bde
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097227"
---
# <a name="resource-model-for-the-azure-cosmos-db-point-in-time-restore-feature-preview"></a>Erőforrás-modell a Azure Cosmos DB időponthoz tartozó visszaállítási szolgáltatáshoz (előzetes verzió)
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> A Azure Cosmos DB időponthoz tartozó visszaállítási funkciója (folyamatos biztonsági mentési mód) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a cikk az Azure Cosmos DB időponthoz tartozó visszaállítási funkció (előzetes verzió) erőforrás-modelljét ismerteti. Ismerteti azokat a folyamatos biztonsági mentést támogató paramétereket, valamint az SQL-hez és MongoDB-hez készült Azure Cosmos DB API-fiókok esetében visszaállítható erőforrásokat.

## <a name="database-accounts-resource-model"></a>Adatbázis-fiók erőforrás-modellje

Az adatbázis-fiók erőforrás-modelljét néhány további tulajdonsággal frissíti az új visszaállítási forgatókönyvek támogatásához. Ezek a tulajdonságok a következők: **BackupPolicy, CreateMode és RestoreParameters.**

### <a name="backuppolicy"></a>BackupPolicy

A fiók szintű biztonsági mentési házirendben a (z) paraméterben megnevezett új tulajdonság `Type` `backuppolicy` lehetővé teszi a folyamatos biztonsági mentést és az időponthoz tartozó visszaállítási funkciókat. Ezt a módot nevezzük **folyamatos biztonsági mentésnek**. A nyilvános előzetes verzióban ezt a módot csak a fiók létrehozásakor állíthatja be. Ha engedélyezve van, a fiókon belül létrehozott összes tároló és adatbázis folyamatos biztonsági mentési és időponthoz tartozó visszaállítási funkciói lesznek alapértelmezés szerint engedélyezve.

> [!NOTE]
> Jelenleg az időponthoz tartozó visszaállítási funkció nyilvános előzetes verzióban érhető el, és elérhető a MongoDB és az SQL-fiókok Azure Cosmos DB API-hoz. Miután létrehozta a folyamatos üzemmódú fiókot, nem válthat ismétlődő módba.

### <a name="createmode"></a>CreateMode

Ez a tulajdonság azt jelzi, hogy a fiók hogyan lett létrehozva. A lehetséges értékek: *default* és *Restore*. A visszaállítás végrehajtásához állítsa ezt az értéket a *visszaállításhoz* , és adja meg a megfelelő értékeket a `RestoreParameters` tulajdonságban.

### <a name="restoreparameters"></a>RestoreParameters

Az `RestoreParameters` erőforrás tartalmazza a visszaállítási művelet részleteit, beleértve a fiók azonosítóját, a visszaállítani kívánt időt és a visszaállítani kívánt erőforrásokat.

|Tulajdonság neve |Leírás  |
|---------|---------|
|restoreMode  | A visszaállítási módnak *PointInTime* kell lennie |
|restoreSource   |  Annak a instanceId a címe, amelyből a visszaállítást kezdeményezni kívánja.       |
|restoreTimestampInUtc  | Az UTC szerinti időpontot, amelyhez a fiókot vissza kell állítani. |
|databasesToRestore   | Azon objektumok listája, `DatabaseRestoreSource` amelyek meghatározzák, hogy mely adatbázisokat és tárolókat kell visszaállítani. Ha ez az érték üres, akkor a rendszer visszaállítja a teljes fiókot.   |

**DatabaseRestoreResource** – minden erőforrás egyetlen adatbázist és az adatbázis alatti összes gyűjteményt jelképezi.

|Tulajdonság neve |Leírás  |
|---------|---------|
|databaseName | Az adatbázis nevét |
| collectionNames| Az adatbázishoz tartozó tárolók listája |

### <a name="sample-resource"></a>Minta erőforrás

A következő JSON egy minta adatbázis-fiók erőforrás, amelyen engedélyezve van a folyamatos biztonsági mentés:

```json
{
  "location": "westus",
  "properties": {
    "databaseAccountOfferType": "Standard",
    "locations": [
      {
        "failoverPriority": 0,
        "locationName": "southcentralus",
        "isZoneRedundant": false
      }
    ],
    "createMode": "Restore",
    "restoreParameters": {
      "restoreMode": "PointInTime",
      "restoreSource": "/subscriptions/subid/providers/Microsoft.DocumentDB/locations/westus/restorableDatabaseAccounts/1a97b4bb-f6a0-430e-ade1-638d781830cc",
      "restoreTimestampInUtc": "2020-06-11T22:05:09Z",
      "databasesToRestore": [
        {
          "databaseName": "db1",
          "collectionNames": [
            "collection1",
            "collection2"
          ]
        },
        {
          "databaseName": "db2",
          "collectionNames": [
            "collection3",
            "collection4"
          ]
        }
      ]
    },
    "backupPolicy": {
      "type": "Continuous"
    },
}
}
```

## <a name="restorable-resources"></a>Helyreállítható erőforrások

Új erőforrások és API-k készlete segíti a kritikus fontosságú információk felderítését az erőforrásokról, amelyek visszaállíthatók, a helyekről, ahol visszaállíthatók, valamint az időbélyegek, amikor az adott erőforrásokon végeztek kulcsfontosságú műveleteket.

> [!NOTE]
> Az erőforrások enumerálásához használt összes API-nak a következő engedélyekkel kell rendelkeznie:
> * `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read`
> * `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read`

### <a name="restorable-database-account"></a>Helyreállítható adatbázis-fiók

Ez az erőforrás egy visszaállítható adatbázis-fiók-példányt tartalmaz. Az adatbázis fiókja törölhető vagy élő fiók lehet. Olyan információt tartalmaz, amely lehetővé teszi a visszaállítani kívánt forrásadatbázis-fiók megtalálását.

|Tulajdonság neve |Leírás  |
|---------|---------|
| ID (Azonosító) | Az erőforrás egyedi azonosítója. |
| accountName | A globális adatbázis-fiók neve. |
| creationTime | A fiók létrehozásakor megadott idő (UTC).  |
| deletionTime | A fiók törlésének időpontja (UTC).  Ez az érték üres, ha a fiók élő. |
| apiType | Az Azure Cosmos DB-fiók API-típusa. |
| restorableLocations | Azon helyeknek a listája, ahol a fiók létezett. |
| restorableLocations: locationName | A regionális fiók régiójának neve. |
| restorableLocations: regionalDatabaseAccountInstanceI | A regionális fiók GUID azonosítója. |
| restorableLocations: creationTime | A regionális fiók létrehozásának időpontja (UTC).|
| restorableLocations: deletionTime | A regionális fiók törlésének időpontja (UTC). Ez az érték üres, ha a regionális fiók él.|

Az összes helyreállítható fiók listájának lekéréséhez lásd: [helyreállítható adatbázis-fiókok – lista](/rest/api/cosmos-db-resource-provider/2020-06-01-preview/restorabledatabaseaccounts/list) vagy [helyreállítható adatbázis-fiókok – helyek listázása](/rest/api/cosmos-db-resource-provider/2020-06-01-preview/restorabledatabaseaccounts/listbylocation) .

### <a name="restorable-sql-database"></a>Helyreállítható SQL-adatbázis

Minden erőforrás egy mutációs eseményről tartalmaz információkat, például a létrehozás és a törlés a SQL Databaseon történt. Ezek az információk segíthetnek olyan forgatókönyvekben, amelyekben véletlenül törölték az adatbázist, és ha meg kell tudnia, hogy mikor történt ez az esemény.

|Tulajdonság neve |Leírás  |
|---------|---------|
| eventTimestamp | Az adatbázis létrehozásakor vagy törlésekor az időpontot (UTC). |
| ownerId | Az SQL-adatbázis neve. |
| ownerResourceId | Az SQL-adatbázis erőforrás-azonosítója|
| operationType | Az adatbázis-esemény műveletének típusa. A lehetséges értékek a következők:<br/><ul><li>Létrehozás: adatbázis-létrehozási esemény</li><li>Törlés: adatbázis-törlési esemény</li><li>Csere: adatbázis-módosítási esemény</li><li>SystemOperation: adatbázis-módosítási esemény, amelyet a rendszeren aktiváltak. Ezt az eseményt a felhasználó nem indítja el</li></ul> |
| adatbázis |Az esemény időpontjában az SQL Database tulajdonságai|

Az adatbázis-mutációk listájának lekéréséhez lásd: [helyreállítható SQL-adatbázisok – cikk listázása](/rest/api/cosmos-db-resource-provider/2020-06-01-preview/restorablesqldatabases/list) .

### <a name="restorable-sql-container"></a>Helyreállítható SQL-tároló

Mindegyik erőforrás olyan mutációs eseményről tartalmaz információkat, mint például az SQL-tárolóban történt létrehozás és törlés. Ezek az információk segíthetnek olyan forgatókönyvekben, amelyekben a tárolót módosították vagy törölték, és ha az esemény megtörtént.

|Tulajdonság neve |Leírás  |
|---------|---------|
| eventTimestamp    | A tároló eseményének időpontja (UTC).|
| ownerId| Az SQL-tároló neve.|
| ownerResourceId   | Az SQL-tároló erőforrás-azonosítója.|
| operationType | A tároló eseményének Művelettípus. A lehetséges értékek a következők: <br/><ul><li>Létrehozás: tároló-létrehozási esemény</li><li>Törlés: tároló törlési eseménye</li><li>Csere: tároló módosításának eseménye</li><li>SystemOperation: a szolgáltatás által aktivált tároló-módosítási esemény. Ezt az eseményt a felhasználó nem indítja el</li></ul> |
| tároló | Az esemény időpontjában az SQL-tároló tulajdonságai.|

Az azonos adatbázishoz tartozó összes tároló-mutáció listájának megtekintéséhez lásd: [helyreállítható SQL-tárolók listája](/rest/api/cosmos-db-resource-provider/2020-06-01-preview/restorablesqlcontainers/list) .

### <a name="restorable-sql-resources"></a>Helyreállítható SQL-erőforrások

Az egyes erőforrások egyetlen adatbázist és az adatbázis alatti összes tárolót jelölik.

|Tulajdonság neve |Leírás  |
|---------|---------|
| databaseName  | Az SQL-adatbázis neve.
| collectionNames   | Az adatbázishoz tartozó SQL-tárolók listája.|

Az SQL Database és a Container combok listájának lekéréséhez, amelyek a megadott időbélyegen és helyen találhatók a fiókban, tekintse meg a [helyreállítható SQL-erőforrások – lista](/rest/api/cosmos-db-resource-provider/2020-06-01-preview/restorablesqlresources/list) cikket.

### <a name="restorable-mongodb-database"></a>Helyreállítható MongoDB-adatbázis

Minden erőforrás egy olyan mutációs eseményről tartalmaz információkat, mint például a MongoDB-adatbázisban történt létrehozás és törlés. Ezek az információk segíthetnek abban a forgatókönyvben, amikor az adatbázist véletlenül törölték, és a felhasználónak meg kell találnia az esemény bekövetkezésekor.

|Tulajdonság neve |Leírás  |
|---------|---------|
|eventTimestamp| Az adatbázis eseményének időpontja (UTC).|
| ownerId| A MongoDB-adatbázis neve. |
| ownerResourceId   | A MongoDB-adatbázis erőforrás-azonosítója. |
| operationType |   Az adatbázis-esemény műveletének típusa. A lehetséges értékek a következők:<br/><ul><li> Létrehozás: adatbázis-létrehozási esemény</li><li> Törlés: adatbázis-törlési esemény</li><li> Csere: adatbázis-módosítási esemény</li><li> SystemOperation: adatbázis-módosítási esemény, amelyet a rendszeren aktiváltak. Ezt az eseményt a felhasználó nem indítja el </li></ul> |

Az adatbázis-mutációk listájának lekéréséhez tekintse meg a visszaállítható [Mongodb adatbázisok – lista](/rest/api/cosmos-db-resource-provider/2020-06-01-preview/restorablemongodbdatabases/list) cikket.

### <a name="restorable-mongodb-collection"></a>Helyreállítható MongoDB-gyűjtemény

Minden erőforrás egy olyan mutációs eseményről tartalmaz információkat, mint például a MongoDB-gyűjteményen történt létrehozás és törlés. Ezek az információk segíthetnek olyan forgatókönyvekben, amelyekben a gyűjteményt módosították vagy törölték, és a felhasználónak meg kell találnia az esemény megtörténtét.

|Tulajdonság neve |Leírás  |
|---------|---------|
| eventTimestamp |A gyűjtemény eseményének időpontja (UTC). |
| ownerId| A MongoDB-gyűjtemény neve. |
| ownerResourceId   | A MongoDB-gyűjtemény erőforrás-azonosítója. |
| operationType |A gyűjteményi esemény Művelettípus. A lehetséges értékek a következők:<br/><ul><li>Létrehozás: gyűjtemény-létrehozási esemény</li><li>Törlés: gyűjtemény törlési eseménye</li><li>Csere: gyűjtemény módosítási eseménye</li><li>SystemOperation: a gyűjtemény módosítási eseménye, amelyet a rendszeren aktiváltak. Ezt az eseményt a felhasználó nem indítja el</li></ul> |

Az azonos adatbázishoz tartozó összes tároló-mutáció listájának megtekintéséhez lásd: [helyreállítható Mongodb-gyűjtemények – cikk listázása](/rest/api/cosmos-db-resource-provider/2020-06-01-preview/restorablemongodbcollections/list) .

### <a name="restorable-mongodb-resources"></a>Helyreállítható MongoDB-erőforrások

Mindegyik erőforrás egyetlen adatbázist és az adatbázis alatti összes gyűjteményt jelképezi.

|Tulajdonság neve |Leírás  |
|---------|---------|
| databaseName  |A MongoDB-adatbázis neve. |
| collectionNames | A MongoDB-gyűjtemények listája ebben az adatbázisban. |

Az adott időbélyegzőn és helyen található fiókon létező összes MongoDB-adatbázis és-gyűjtemény kombinációjának megtekintéséhez tekintse meg a visszaállítható [MongoDB-erőforrások – lista](/rest/api/cosmos-db-resource-provider/2020-06-01-preview/restorablemongodbresources/list) cikket.

## <a name="next-steps"></a>Következő lépések

* A folyamatos biztonsági mentés konfigurálása és kezelése [Azure Portal](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [parancssori](continuous-backup-restore-command-line.md)felület vagy [Azure Resource Manager](continuous-backup-restore-template.md)használatával.
* [Kezelheti](continuous-backup-restore-permissions.md) az adathelyreállításhoz szükséges engedélyeket a folyamatos biztonsági mentési módban.
