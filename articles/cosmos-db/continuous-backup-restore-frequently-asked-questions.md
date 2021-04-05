---
title: Azure Cosmos DB időponthoz tartozó visszaállítási szolgáltatással kapcsolatos gyakori kérdések.
description: Ez a cikk a folyamatos biztonsági mentési mód használatával elérhető Azure Cosmos DB időpontra vonatkozó visszaállítási szolgáltatással kapcsolatos gyakori kérdéseket sorolja fel.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 1cf94964f420f7a7d4fc0f6ba0b77813b3e75787
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100393224"
---
# <a name="frequently-asked-questions-on-the-azure-cosmos-db-point-in-time-restore-feature-preview"></a>Gyakori kérdések a Azure Cosmos DB időponthoz tartozó visszaállítási funkcióról (előzetes verzió)
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> A Azure Cosmos DB időponthoz tartozó visszaállítási funkciója (folyamatos biztonsági mentési mód) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a cikk a folyamatos biztonsági mentési mód használatával elért Azure Cosmos DB időponthoz tartozó visszaállítási funkció (előzetes verzió) gyakori kérdéseit sorolja fel.

## <a name="how-much-time-does-it-takes-to-restore"></a>Mennyi időt vesz igénybe a visszaállítás?
A visszaállítás időtartama az adatai méretétől függ.

### <a name="can-i-submit-the-restore-time-in-local-time"></a>Elküldhetem a visszaállítási időt helyi időben?
Előfordulhat, hogy a visszaállítás nem történik meg attól függően, hogy a kulcsfontosságú erőforrások, például adatbázisok vagy tárolók léteztek-e az adott időpontban. A következő lépésekkel ellenőrizheti az időt, és megtekintheti a kijelölt adatbázist vagy tárolót egy adott időpontra. Ha úgy látja, hogy nem található erőforrás a visszaállításhoz, akkor a visszaállítási folyamat nem működik.

### <a name="how-can-i-track-if-an-account-is-being-restored"></a>Hogyan követhetem nyomon, ha egy fiók visszaállítása folyamatban van?
Miután elküldte a Restore parancsot, és a művelet befejeződése után megvárja, hogy az állapotsor sikeresen visszaállította a fiók üzenetét. Kereshet a visszaállított fiókra is, és [nyomon követheti a visszaállított fiók állapotát](continuous-backup-restore-portal.md#track-restore-status). Ha a visszaállítás folyamatban van, a rendszer *létrehozza* a fiók állapotát, miután a visszaállítási művelet befejeződik, majd a fiók állapota *online* értékre változik.

Ehhez hasonlóan a PowerShell és a parancssori felület esetében is nyomon követheti a visszaállítási művelet állapotát a `az cosmosdb show` következő parancs végrehajtásával:

```azurecli-interactive
az cosmosdb show --name "accountName" --resource-group "resourceGroup"
```

A provisioningState *sikeresnek* bizonyult, ha a fiók online állapotban van.

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

A következő kimenetben például a forrásoldali fiók *7b4bb-f6a0-430E-ade1-638d781830cc*

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
Egy adott időpontban a Azure Cosmos DB fiók `accountName` tulajdonsága globálisan egyedi, miközben életben van. A fiók törlése után azonban létrehozhat egy másik fiókot ugyanazzal a névvel, így a "accountName" már nem elegendő a fiók egy példányának azonosításához. 

Az azonosító vagy a egy `instanceId` fiók egy példányának tulajdonsága, és a rendszer több fiók között (élő és törölt) egyértelműsítse, ha a visszaállításhoz ugyanazt a nevet használja. A példány AZONOSÍTÓját a vagy a parancsok futtatásával kérheti le `Get-AzCosmosDBRestorableDatabaseAccount`  `az cosmosdb restorable-database-account` . A Name attribútum értéke jelöli a "InstanceID" értéket.

## <a name="next-steps"></a>Következő lépések

* Mi a [folyamatos biztonsági mentési](continuous-backup-restore-introduction.md) mód?
* A folyamatos biztonsági mentés konfigurálása és kezelése [Azure Portal](continuous-backup-restore-portal.md), [PowerShell](continuous-backup-restore-powershell.md), [parancssori](continuous-backup-restore-command-line.md)felület vagy [Azure Resource Manager](continuous-backup-restore-template.md)használatával.
* [Kezelheti](continuous-backup-restore-permissions.md) az adathelyreállításhoz szükséges engedélyeket a folyamatos biztonsági mentési módban.
* [Folyamatos biztonsági mentési mód erőforrás-modellje](continuous-backup-restore-resource-model.md)