---
title: Az Azure CLI-vel konfigurálhatja a folyamatos biztonsági mentést és az időpontra történő visszaállítást Azure Cosmos DBban.
description: Ismerje meg, hogyan helyezhet üzembe egy fiókot az Azure CLI használatával folyamatos biztonsági mentéssel és visszaállítással.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 9ea71dae746ac423e7b17b6235b4d5cd3e143cd7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100377329"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-cli"></a>A folyamatos biztonsági mentés és az időponthoz tartozó visszaállítás (előzetes verzió) konfigurálása és kezelése – Azure CLI használata
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> A Azure Cosmos DB időponthoz tartozó visszaállítási funkciója (folyamatos biztonsági mentési mód) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Cosmos DB az időponthoz tartozó visszaállítási funkció (előzetes verzió) segítségével helyreállíthatja a tárolók véletlen változását, a törölt fiókok, adatbázisok vagy tárolók visszaállítását, illetve bármely régióba való visszaállítást (ahol a biztonsági másolatok léteztek). A folyamatos biztonsági mentési mód lehetővé teszi, hogy az elmúlt 30 napban bármikor visszaállítsa az adott időpontra.

Ez a cikk bemutatja, hogyan helyezhet üzembe egy fiókot folyamatos biztonsági mentéssel és visszaállítással az Azure CLI használatával.

## <a name="install-azure-cli"></a><a id="install"></a>Az Azure CLI telepítése

1. Az Azure CLI legújabb verziójának telepítése

   * Telepítse az [Azure CLI](/cli/azure/install-azure-cli) legújabb verzióját vagy a 2.17.1-nál magasabb verziót.
   * Ha már telepítette a CLI-t, futtassa a `az upgrade` parancsot a legújabb verzióra való frissítéshez. Ez a parancs csak a 2,11-nál nagyobb CLI-verzióval működik. Ha egy korábbi verzióval rendelkezik, a fenti hivatkozásra kattintva telepítse a legújabb verziót.

1. Telepítse a `cosmosdb-preview` CLI-bővítményt.

   * Az időponthoz tartozó visszaállítási parancsok a bővítmény alatt érhetők el `cosmosdb-preview` .
   * Ezt a bővítményt a következő parancs futtatásával is telepítheti: `az extension add --name cosmosdb-preview`
   * Ezt a bővítményt a következő parancs futtatásával távolíthatja el: `az extension remove --name cosmosdb-preview`

1. Jelentkezzen be, és válassza ki az előfizetését

   * Jelentkezzen be az Azure-fiókjába a `az login` paranccsal.
   * Válassza ki a szükséges előfizetést a `az account set -s <subscriptionguid>` parancs használatával.

## <a name="provision-a-sql-api-account-with-continuous-backup"></a><a id="provision-sql-api"></a>SQL API-fiók kiépítése folyamatos biztonsági mentéssel

Ha egy SQL API-fiókot folyamatos biztonsági mentéssel szeretne kiépíteni, egy további argumentumot `--backup-policy-type Continuous` is át kell adni a normál kiépítési paranccsal. Az alábbi parancs egy, az `pitracct2` *USA nyugati* régiójában, a *myrg* erőforráscsoport alatt létrehozott, folyamatos biztonsági mentési szabályzattal ellátott, egyetlen régióból álló írási fiókot mutat be:

```azurecli-interactive

az cosmosdb create \
  --name pitracct2 \
  --resource-group myrg \
  --backup-policy-type Continuous \
  --default-consistency-level Session \
  --locations regionName="West US"

```

## <a name="provision-an-azure-cosmos-db-api-for-mongodb-account-with-continuous-backup"></a><a id="provision-mongo-api"></a>Azure Cosmos DB API kiépítése a MongoDB-fiókhoz folyamatos biztonsági mentéssel

Az alábbi parancs egy, a folyamatos biztonsági mentési szabályzattal ellátott, egyetlen régióból álló írási fiókot mutat be, amely `pitracct3` az *USA nyugati* régiójában, az *myrg* erőforráscsoport alatt lett létrehozva:

```azurecli-interactive

az cosmosdb create \
  --name pitracct3 \
  --kind MongoDB \
  --resource-group myrg \
  --server-version "3.6" \
  --backup-policy-type Continuous \
  --default-consistency-level Session \
  --locations regionName="West US"

```

## <a name="trigger-a-restore-operation-with-cli"></a><a id="trigger-restore"></a>Visszaállítási művelet indítása a parancssori felülettel

A visszaállítás legegyszerűbb módja, ha a visszaállítási parancsot a célként megadott fiók, a forrásoldali fiók, a hely, az erőforráscsoport, az időbélyeg (UTC) és opcionálisan az adatbázis és a tárolók neve alapján állítja ki. A következő példák a visszaállítási művelet elindítására szolgálnak:

1. Hozzon létre egy új Azure Cosmos DB fiókot egy meglévő fiók visszaállításával.

   ```azurecli-interactive

   az cosmosdb restore \
    --target-database-account-name MyRestoredCosmosDBDatabaseAccount \
    --account-name MySourceAccount \
    --restore-timestamp 2020-07-13T16:03:41+0000 \
    --resource-group MyResourceGroup \
    --location "West US"

   ```

2. Hozzon létre egy új Azure Cosmos DB fiókot úgy, hogy csak a kiválasztott adatbázisokat és tárolókat állítja vissza egy meglévő adatbázis-fiókból.

   ```azurecli-interactive

   az cosmosdb restore \
    --resource-group MyResourceGroup \
    --target-database-account-name MyRestoredCosmosDBDatabaseAccount \
    --account-name MySourceAccount \
    --restore-timestamp 2020-07-13T16:03:41+0000 \
    --location "West US" \
    --databases-to-restore name=MyDB1 collections=collection1 collection2 \
    --databases-to-restore name=MyDB2 collections=collection3 collection4

   ```

## <a name="enumerate-restorable-resources-for-sql-api"></a><a id="enumerate-sql-api"></a>Az SQL API helyreállítható erőforrásainak számbavétele

Az alább ismertetett enumerálási parancsok segítséget nyújtanak a különböző időbélyegeken való visszaállításhoz elérhető erőforrások felderítésében. Emellett a legfontosabb eseményeket is megadhatják a helyreállítható fiók, az adatbázis és a tároló erőforrásaiban.

**Az aktuális előfizetésben visszaállítható fiókok listázása**

Az alábbi CLI-parancs futtatásával listázhatja az aktuális előfizetésben visszaállítható összes fiókot

```azurecli-interactive
az cosmosdb restorable-database-account list --account-name "pitrbb"
```

A válasz tartalmazza az összes olyan adatbázis-fiókot (élő és törölt), amelyek visszaállíthatók, valamint a régiókat, amelyekről visszaállíthatók a következők:

```json
{
    "accountName": "pitrbb",
    "apiType": "Sql",
    "creationTime": "2021-01-08T23:34:11.095870+00:00",
    "deletionTime": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865",
    "identity": null,
    "location": "West US",
    "name": "7133a59a-d1c0-4645-a699-6e296d6ac865",
    "restorableLocations": [
      {
        "creationTime": "2021-01-08T23:34:11.095870+00:00",
        "deletionTime": null,
        "locationName": "West US",
        "regionalDatabaseAccountInstanceId": "f02df26b-c0ec-4829-8bef-3482d36e6230"
      }
    ],
    "tags": null,
    "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts"
  }
```

Akárcsak a `CreationTime` vagy a `DeletionTime` fiókhoz, a `CreationTime` vagy a `DeletionTime` régióhoz is. Ezek az időpontok lehetővé teszik a megfelelő régió és érvényes időtartomány kiválasztását az adott régióba való visszaállításhoz.

**Az adatbázisok összes verziójának listázása egy élő adatbázis-fiókban**

Az adatbázisok összes verziójának listázásával kiválaszthatja a megfelelő adatbázist olyan forgatókönyvben, amelyben az adatbázis létezésének tényleges ideje ismeretlen.

Futtassa az alábbi CLI-parancsot az adatbázisok összes verziójának listázásához. Ez a parancs csak Live-fiókokkal működik. A `instanceId` és a `location` paramétereket a `name` parancs válaszában szereplő és tulajdonságok alapján szerezheti `location` be `az cosmosdb restorable-database-account list` . A instanceId attribútum a visszaállított forrás-adatbázis-fiók tulajdonsága is:

```azurecli-interactive
az cosmosdb sql restorable-database list \
  --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
  --location "West US"
```

Ez a parancs kimenete mostantól egy adatbázis létrehozása és törlése után jelenik meg.

```json
[
  {
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865/restorableSqlDatabases/40e93dbd-2abe-4356-a31a-35567b777220",
    ..
    "name": "40e93dbd-2abe-4356-a31a-35567b777220",
    "resource": {
      "database": {
        "id": "db1"
      },
      "eventTimestamp": "2021-01-08T23:27:25Z",
      "operationType": "Create",
      "ownerId": "db1",
      "ownerResourceId": "YuZAAA=="
    },
    ..
  },
  {
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/7133a59a-d1c0-4645-a699-6e296d6ac865/restorableSqlDatabases/243c38cb-5c41-4931-8cfb-5948881a40ea",
    ..
    "name": "243c38cb-5c41-4931-8cfb-5948881a40ea",
    "resource": {
      "database": {
        "id": "spdb1"
      },
      "eventTimestamp": "2021-01-08T23:25:25Z",
      "operationType": "Create",
      "ownerId": "spdb1",
      "ownerResourceId": "OIQ1AA=="
    },
   ..
  }
]
```

**Egy élő adatbázis-fiókban lévő adatbázis SQL-tárolóinak összes verziójának listázása**

Használja az alábbi parancsot az SQL-tárolók összes verziójának listázásához. Ez a parancs csak Live-fiókokkal működik. A `databaseRid` paraméter a `ResourceId` visszaállítani kívánt adatbázis. Ez a `ownerResourceid` parancs válaszában található attribútum értéke `az cosmosdb sql restorable-database list` .

```azurecli-interactive
az cosmosdb sql restorable-container list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --database-rid "OIQ1AA==" \
    --location "West US"
```

Ez a parancs kimenete tartalmazza az adatbázisban található összes tárolón végrehajtott műveletek listáját:

```json
[
  {
    ...
    
      "eventTimestamp": "2021-01-08T23:25:29Z",
      "operationType": "Replace",
      "ownerId": "procol3",
      "ownerResourceId": "OIQ1APZ7U18="
...
  },
  {
    ...
      "eventTimestamp": "2021-01-08T23:25:26Z",
      "operationType": "Create",
      "ownerId": "procol3",
      "ownerResourceId": "OIQ1APZ7U18="
  },
]
```

**Olyan adatbázisok vagy tárolók keresése, amelyek bármikor visszaállíthatók az adott időbélyegen**

Az alábbi paranccsal lekérheti az adatbázisok vagy tárolók listáját, amelyeket bármikor visszaállíthat egy adott időbélyegen. Ez a parancs csak Live-fiókokkal működik.

```azurecli-interactive

az cosmosdb sql restorable-resource list \
  --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
  --location "West US" \
  --restore-location "West US" \  
  --restore-timestamp "2021-01-10 T01:00:00+0000"

```

```json
[
  {
    "collectionNames": [
      "procol1",
      "procol2"
    ],
    "databaseName": "db1"
  },
  {
    "collectionNames": [
      "procol3",
       "spcol1"
    ],
    "databaseName": "spdb1"
  }
]
```

## <a name="enumerate-restorable-resources-for-mongodb-api-account"></a><a id="enumerate-mongodb-api"></a>MongoDB API-fiók helyreállítható erőforrásainak számbavétele

Az alább ismertetett enumerálási parancsok segítséget nyújtanak a különböző időbélyegeken való visszaállításhoz elérhető erőforrások felderítésében. Emellett a legfontosabb eseményeket is megadhatják a helyreállítható fiók, az adatbázis és a tároló erőforrásaiban. Az SQL API-hoz hasonlóan a (z `az cosmosdb` ) parancsot használhatja a `mongodb` helyett a as paraméterrel `sql` . Ezek a parancsok csak az élő fiókok esetében működnek.

**A mongodb-adatbázisok összes verziójának listázása egy élő adatbázis-fiókban**

```azurecli-interactive
az cosmosdb mongodb restorable-database list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --location "West US"
```

**Egy élő adatbázis-fiókban lévő adatbázis mongodb-gyűjteményei összes verziójának listázása**

```azurecli-interactive
az cosmosdb mongodb restorable-collection list \
    --instance-id "<InstanceID>" \
    --database-rid "AoQ13r==" \
    --location "West US"
```

**Egy adott időbélyegen és régióban visszaállítható mongodb-adatbázis-fiók összes erőforrásának listázása**

```azurecli-interactive
az cosmosdb mongodb restorable-resource list \
    --instance-id "7133a59a-d1c0-4645-a699-6e296d6ac865" \
    --location "West US" \
    --restore-location "West US" \
    --restore-timestamp "2020-07-20T16:09:53+0000"
```

## <a name="next-steps"></a>Következő lépések

* A folyamatos biztonsági mentés konfigurálása és kezelése [Azure Portal](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md)vagy [Azure Resource Manager](continuous-backup-restore-template.md)használatával.
* [Folyamatos biztonsági mentési mód erőforrás-modellje](continuous-backup-restore-resource-model.md)
* [Kezelheti](continuous-backup-restore-permissions.md) az adathelyreállításhoz szükséges engedélyeket a folyamatos biztonsági mentési módban.
