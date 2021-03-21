---
title: A Azure PowerShell használatával konfigurálhatja a folyamatos biztonsági mentést és az időpontra történő visszaállítást a Azure Cosmos DBban.
description: Megtudhatja, hogyan helyezhet üzembe egy fiókot folyamatos biztonsági mentéssel és visszaállítással az Azure PowerShell használatával.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 5261075a82eaefd91cbedd2dd2fe08cb1e0a20b4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100381834"
---
# <a name="configure-and-manage-continuous-backup-and-point-in-time-restore-preview---using-azure-powershell"></a>A folyamatos biztonsági mentés és az időponthoz tartozó visszaállítás (előzetes verzió) konfigurálása és kezelése – Azure PowerShell
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> A Azure Cosmos DB időponthoz tartozó visszaállítási funkciója (folyamatos biztonsági mentési mód) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Cosmos DB az időponthoz tartozó visszaállítási funkció (előzetes verzió) segítségével helyreállíthatja a tárolók véletlen változását, a törölt fiókok, adatbázisok vagy tárolók visszaállítását, illetve bármely régióba való visszaállítást (ahol a biztonsági másolatok léteztek). A folyamatos biztonsági mentési mód lehetővé teszi, hogy az elmúlt 30 napban bármikor visszaállítsa az adott időpontra.

Ez a cikk bemutatja, hogyan helyezhet üzembe egy fiókot folyamatos biztonsági mentéssel és visszaállítással az Azure PowerShell használatával.

## <a name="install-azure-powershell"></a><a id="install-powershell"></a>Az Azure PowerShell telepítése

1. Futtassa a következő parancsot a Azure PowerShellról az `Az.CosmosDB` előzetes verziójú modul telepítéséhez, amely tartalmazza az adott időponthoz kapcsolódó parancsokat:

   ```azurepowershell
   Install-Module -Name Az.CosmosDB -AllowPrerelease
   ```

1. A modulok telepítése után jelentkezzen be az Azure-ba a következővel:

   ```azurepowershell
   Connect-AzAccount
   ```

1. Válasszon egy adott előfizetést a következő paranccsal:

   ```azurepowershell
   Select-AzSubscription -Subscription <SubscriptionName>
   ```

## <a name="provision-a-sql-api-account-with-continuous-backup"></a><a id="provision-sql-api"></a>SQL API-fiók kiépítése folyamatos biztonsági mentéssel

Ha folyamatos biztonsági mentéssel rendelkező fiókot szeretne kiépíteni, adjon hozzá egy argumentumot `-BackupPolicyType Continuous` a normál kiépítési paranccsal együtt.

A következő parancsmag egy, az `pitracct2` *USA nyugati* régiójában, az *myrg* erőforráscsoport alatt létrehozott folyamatos biztonsági mentési szabályzattal rendelkező egyetlen régióból álló írási fiókra mutat példát:

```azurepowershell

New-AzCosmosDBAccount `
  -ResourceGroupName "myrg" `
  -Location "West US" `
  -BackupPolicyType Continuous `
  -Name "pitracct2" `
  -ApiKind "Sql"
      
```

## <a name="provision-a-mongodb-api-account-with-continuous-backup"></a><a id="provision-mongodb-api"></a>MongoDB API-fiók kiépítése folyamatos biztonsági mentéssel

A következő parancsmag egy példa az *USA nyugati* régiójában létrehozott folyamatos biztonsági mentési fiók *pitracct2* a *myrg* erőforráscsoport alatt:

```azurepowershell

New-AzCosmosDBAccount `
  -ResourceGroupName "myrg" `
  -Location "West US" `
  -BackupPolicyType Continuous `
  -Name "pitracct3" `
  -ApiKind "MongoDB" `
  -ServerVersion "3.6"

```

## <a name="trigger-a-restore-operation"></a><a id="trigger-restore"></a>Visszaállítási művelet indítása

A következő parancsmag egy példa arra, hogy a visszaállítási műveletet a cél fiók, a forrás fiók, a hely, az erőforráscsoport és az időbélyeg használatával aktiválja a Restore paranccsal:

```azurepowershell

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName <resourceGroupName> `
  -TargetDatabaseAccountName <restored-account-name> `
  -SourceDatabaseAccountName <sourceDatabaseAccountName> `
  -RestoreTimestampInUtc <UTC time> `
  -Location <Azure Region Name>

```

**1. példa:** A teljes fiók visszaállítása:

```azurepowershell

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "rg" `
  -TargetDatabaseAccountName "pitrbb-ps-1" `
  -SourceDatabaseAccountName "source-sql" `
  -RestoreTimestampInUtc "2021-01-05T22:06:00" `
  -Location "West US"

```

**2. példa:** Adott gyűjtemények és adatbázisok visszaállítása. Ez a példa visszaállítja a gyűjtemények myCol1, myCol2 a myDB1 és a teljes adatbázis-myDB2, amely tartalmazza az összes tárolót.

```azurepowershell
$datatabaseToRestore1 = New-AzCosmosDBDatabaseToRestore -DatabaseName "myDB1" -CollectionName "myCol1", "myCol2"
$datatabaseToRestore2 = New-AzCosmosDBDatabaseToRestore -DatabaseName "myDB2"

Restore-AzCosmosDBAccount `
  -TargetResourceGroupName "rg" `
  -TargetDatabaseAccountName "pitrbb-ps-1" `
  -SourceDatabaseAccountName "source-sql" `
  -RestoreTimestampInUtc "2021-01-05T22:06:00" `
  -DatabasesToRestore $datatabaseToRestore1, $datatabaseToRestore2 `
  -Location "West US"

```

## <a name="enumerate-restorable-resources-for-sql-api"></a><a id="enumerate-sql-api"></a>Az SQL API helyreállítható erőforrásainak számbavétele

A számbavételi parancsmagok segítségével felderítheti a különböző időbélyegeken visszaállítható erőforrásokat. Emellett a legfontosabb eseményeket is megadhatják a helyreállítható fiók, az adatbázis és a tároló erőforrásaiban.

**Az aktuális előfizetésben visszaállítható fiókok listázása**

Futtassa a `Get-AzCosmosDBRestorableDatabaseAccount` PowerShell-parancsot az összes olyan fiók listázásához, amelyet vissza lehet állítani az aktuális előfizetésben.

A válasz tartalmazza az összes olyan adatbázis-fiókot (mind az élő, mind a törölt), amely visszaállítható, valamint a régiókat, amelyekről vissza lehet állítani.

```console
{
    "accountName": "sampleaccount",
    "apiType": "Sql",
    "creationTime": "2020-08-08T01:04:52.070190+00:00",
    "deletionTime": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locations/West US/restorableDatabaseAccounts/23e99a35-cd36-4df4-9614-f767a03b9995",
    "identity": null,
    "location": "West US",
    "name": "23e99a35-cd36-4df4-9614-f767a03b9995",
    "restorableLocations": [
      {
        "creationTime": "2020-08-08T01:04:52.945185+00:00",
        "deletionTime": null,
        "locationName": "West US",
        "regionalDatabaseAccountInstanceId": "30701557-ecf8-43ce-8810-2c8be01dccf9"
      },
      {
        "creationTime": "2020-08-08T01:15:43.507795+00:00",
        "deletionTime": null,
        "locationName": "East US",
        "regionalDatabaseAccountInstanceId": "8283b088-b67d-4975-bfbe-0705e3e7a599"
      }
    ],
    "tags": null,
    "type": "Microsoft.DocumentDB/locations/restorableDatabaseAccounts"
  },
```

Akárcsak a `CreationTime` vagy a `DeletionTime` fiókhoz, a `CreationTime` vagy a `DeletionTime` régióhoz is. Ezek az időpontok lehetővé teszik a megfelelő régió és érvényes időtartomány kiválasztását az adott régióba való visszaállításhoz.

**Az SQL-adatbázisok összes verziójának listázása egy élő adatbázis-fiókban**

Az adatbázisok összes verziójának listázásával kiválaszthatja a megfelelő adatbázist olyan forgatókönyvben, amelyben az adatbázis létezésének tényleges ideje ismeretlen.

Futtassa a következő PowerShell-parancsot az adatbázisok összes verziójának listázásához. Ez a parancs csak Live-fiókokkal működik. A `DatabaseAccountInstanceId` és a `LocationName` paramétereket a `name` parancsmag válaszában szereplő és tulajdonságok alapján szerezheti `location` be `Get-AzCosmosDBRestorableDatabaseAccount` . Az `DatabaseAccountInstanceId` attribútum a `instanceId` visszaállított forrás adatbázis-fiók tulajdonságára hivatkozik:


```azurepowershell

Get-AzCosmosdbSqlRestorableDatabase `
  -LocationName "East US" `
  -DatabaseAccountInstanceId <DatabaseAccountInstanceId>

```

**Az adatbázis SQL-tárolóinak összes verziójának listázása egy élő adatbázis-fiókban.**

Használja az alábbi parancsot az SQL-tárolók összes verziójának listázásához. Ez a parancs csak Live-fiókokkal működik. A `DatabaseRid` paraméter a `ResourceId` visszaállítani kívánt adatbázis. Ez a `ownerResourceid` parancsmag válaszában található attribútum értéke `Get-AzCosmosdbSqlRestorableDatabase` . A válasz tartalmazza az adatbázison belüli összes tárolón végrehajtott műveletek listáját is.

```azurepowershell

Get-AzCosmosdbSqlRestorableContainer `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -DatabaseRid "AoQ13r==" `
  -LocationName "West US"

```

**Olyan adatbázisok vagy tárolók keresése, amelyek bármikor visszaállíthatók az adott időbélyegen**

Az alábbi paranccsal lekérheti az adatbázisok vagy tárolók listáját, amelyeket bármikor visszaállíthat egy adott időbélyegen. Ez a parancs csak Live-fiókokkal működik.

```azurepowershell

Get-AzCosmosdbSqlRestorableResource `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -LocationName "West US" `
  -RestoreLocation "East US" `
  -RestoreTimestamp "2020-07-20T16:09:53+0000"

```

## <a name="enumerate-restorable-resources-for-mongodb"></a><a id="enumerate-mongodb-api"></a>Helyreállítható erőforrások számbavétele a MongoDB

Az alább ismertetett enumerálási parancsok segítséget nyújtanak a különböző időbélyegeken való visszaállításhoz elérhető erőforrások felderítésében. Emellett a legfontosabb eseményeket is megadhatják a helyreállítható fiók, az adatbázis és a tároló erőforrásaiban. Ezek a parancsok csak az élő fiókok esetében működnek, és hasonlók az SQL API-parancsokhoz, de `MongoDB` a parancs neve helyett `sql` .

**A MongoDB-adatbázisok összes verziójának listázása egy élő adatbázis-fiókban**

```azurepowershell

Get-AzCosmosdbMongoDBRestorableDatabase `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -LocationName "West US"

```

**Egy élő adatbázis-fiókban lévő adatbázis mongodb-gyűjteményei összes verziójának listázása**

```azurepowershell

Get-AzCosmosdbMongoDBRestorableCollection `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -DatabaseRid "AoQ13r==" `
  -LocationName "West US"
```

**Egy adott időbélyegen és régióban visszaállítható mongodb-adatbázis-fiók összes erőforrásának listázása**

```azurepowershell

Get-AzCosmosdbMongoDBRestorableResource `
  -DatabaseAccountInstanceId "d056a4f8-044a-436f-80c8-cd3edbc94c68" `
  -LocationName "West US" `
  -RestoreLocation "West US" `
  -RestoreTimestamp "2020-07-20T16:09:53+0000"
```

## <a name="next-steps"></a>Következő lépések

* A folyamatos biztonsági mentés konfigurálása és kezelése az [Azure CLI](continuous-backup-restore-command-line.md), [Resource Manager](continuous-backup-restore-template.md)vagy [Azure Portal](continuous-backup-restore-portal.md)használatával.
* [Folyamatos biztonsági mentési mód erőforrás-modellje](continuous-backup-restore-resource-model.md)
* [Kezelheti](continuous-backup-restore-permissions.md) az adathelyreállításhoz szükséges engedélyeket a folyamatos biztonsági mentési módban.
