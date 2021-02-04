---
title: Azure Cosmos DB időponthoz tartozó visszaállítási szolgáltatással kapcsolatos gyakori kérdések.
description: Ez a cikk a folyamatos biztonsági mentési mód használatával elérhető Azure Cosmos DB időpontra vonatkozó visszaállítási szolgáltatással kapcsolatos gyakori kérdéseket sorolja fel.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 7a41eb8bdd573ac08b0c76eb9a6c2b0724637c39
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527719"
---
# <a name="frequently-asked-questions-on-the-azure-cosmos-db-point-in-time-restore-feature"></a>Gyakori kérdések a Azure Cosmos DB időponthoz tartozó visszaállítási funkcióról
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Ez a cikk a folyamatos biztonsági mentési mód használatával elérhető Azure Cosmos DB időpontra vonatkozó visszaállítási funkciókkal kapcsolatos gyakori kérdéseket sorolja fel.

## <a name="how-much-time-does-it-takes-to-restore"></a>Mennyi időt vesz igénybe a visszaállítás?
A visszaállítás időtartama az adatai méretétől függ.

### <a name="can-i-submit-the-restore-time-in-local-time"></a>Elküldhetem a visszaállítási időt helyi időben?
Előfordulhat, hogy a visszaállítás nem történik meg attól függően, hogy a kulcsfontosságú erőforrások, például adatbázisok vagy tárolók léteztek-e az adott időpontban. A következő lépésekkel ellenőrizheti az időt, és megtekintheti a kijelölt adatbázist vagy tárolót egy adott időpontra. Ha úgy látja, hogy nem található erőforrás a visszaállításhoz, akkor a visszaállítási folyamat nem működik.

### <a name="how-can-i-track-if-an-account-is-being-restored"></a>Hogyan követhetem nyomon, ha egy fiók visszaállítása folyamatban van?
Miután elküldte a Restore parancsot, és a művelet befejeződése után megvárja, hogy az állapotsor sikeresen visszaállította a fiók üzenetét. Kereshet a visszaállított fiókra is, és [nyomon követheti a visszaállított fiók állapotát](continuous-backup-restore-portal.md#track-restore-status). A visszaállítás folyamatban van, a fiók állapota "létrehozás" lesz, miután a visszaállítási művelet befejeződik, a fiók állapota "online" állapotúra változik.

Ehhez hasonlóan a PowerShell és a parancssori felület esetében is nyomon követheti a visszaállítási művelet állapotát a `az cosmosdb show` következő parancs végrehajtásával:

```azurecli-interactive
az cosmosdb show --name "accountName" --resource-group "resourceGroup"
```

A provisioningState a "sikeres" állapotot jeleníti meg, ha a fiók online állapotban van.

```json
{
"virtualNetworkRules": [],
"writeLocations" : [
{
    "documentEndpoint": "https://<accountname>.documents.azure.com:443/", 
    "failoverpriority": 0,
    "id": "accountName" ,
    "isZoneRedundant" : false, 
    "locationName": "West US 2", 
    "provisioningState": "Succeeded"
}
]
}
```

### <a name="how-can-i-find-out-whether-an-account-was-restored-from-another-account"></a>Honnan tudhatom meg, hogy egy fiókot visszaállítottak-e egy másik fiókból?
Futtassa a `az cosmosdb show` parancsot, a kimenetben láthatja, hogy a `createMode` tulajdonság értéke. Ha az érték **visszaállításra** van beállítva. azt jelzi, hogy a fiók egy másik fiókból lett visszaállítva. A `restoreParameters` tulajdonság további részleteket tartalmaz, például `restoreSource` a forrás fiók azonosítóját. A paraméter utolsó GUID-azonosítója a `restoreSource` instanceId.

A következő kimenetben például a forrásoldali fiók példányának azonosítója: "7b4bb-f6a0-430E-ade1-638d781830cc"

```json
"restoreParameters": {
   "databasesToRestore" : [],
   "restoreMode": "PointInTime",
   "restoreSource": "/subscriptions/2a5b-f6a0-430e-ade1-638d781830dd/providers/Microsoft.DocumentDB/locations/westus/restorableDatabaseAccounts/7b4bb-f6a0-430e-ade1-638d781830cc",
   "restoreTimestampInUtc": "2020-06-11T22:05:09Z"
}
```

### <a name="what-happens-when-i-restore-a-shared-throughput-database-or-a-container-within-a-shared-throughput-database"></a>Mi történik, ha egy megosztott átviteli sebességű adatbázist vagy egy megosztott átviteli sebességű adatbázisban lévő tárolót állítok vissza?
A rendszer visszaállítja a teljes megosztott átviteli sebesség-adatbázist. A tárolók részhalmaza nem választható egy megosztott átviteli sebességű adatbázisban a visszaállításhoz.

### <a name="what-is-the-use-of-instanceid-in-the-account-definition"></a>Mi a InstanceID használata a fiók definíciójában?
Egy adott időpontban Azure Cosmos DB fiók "accountName" tulajdonsága globálisan egyedi, miközben életben van. A fiók törlése után azonban létrehozhat egy másik fiókot ugyanazzal a névvel, így a "accountName" már nem elegendő a fiók egy példányának azonosításához. 

Az azonosító vagy a "instanceId" a fiók egy példányának tulajdonsága, és a rendszer több fiók között egyértelműsítse (élő és törölt), ha a visszaállításhoz ugyanazt a nevet használja. A példány AZONOSÍTÓját a vagy a parancsok futtatásával kérheti le `Get-AzCosmosDBRestorableDatabaseAccount`  `az cosmosdb restorable-database-account` . A Name attribútum értéke jelöli a "InstanceID" értéket.

## <a name="next-steps"></a>Következő lépések

* Mi a [folyamatos biztonsági mentési](continuous-backup-restore-introduction.md) mód?
* A folyamatos biztonsági mentés konfigurálása és kezelése [Azure Portal](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [parancssori](continuous-backup-restore-command-line.md)felület vagy [Azure Resource Manager](continuous-backup-restore-template.md)használatával.
* [Kezelheti](continuous-backup-restore-permissions.md) az adathelyreállításhoz szükséges engedélyeket a folyamatos biztonsági mentési módban.
* [Folyamatos biztonsági mentési mód erőforrás-modellje](continuous-backup-restore-resource-model.md)