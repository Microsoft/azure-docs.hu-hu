---
title: NSG-adatforgalmi naplók kezelése – Azure REST API
titleSuffix: Azure Network Watcher
description: Ez az oldal azt ismerteti, hogyan kezelhetők a hálózati biztonsági csoportok folyamatábrái az Azure Network Watcherban REST API
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: ea24716dba5e4e824a4fa986602007035be8e365
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98018377"
---
# <a name="configuring-network-security-group-flow-logs-using-rest-api"></a>Hálózati biztonsági csoport folyamatábráinak konfigurálása REST API használatával

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

A hálózati biztonsági csoport folyamatábrái a Network Watcher szolgáltatása, amely lehetővé teszi, hogy a hálózati biztonsági csoporton keresztül megtekintse a bejövő és kimenő IP-forgalomra vonatkozó információkat. Ezeket a folyamatokat a rendszer JSON formátumban írja be, és a kimenő és bejövő folyamatokat egy szabály alapján jeleníti meg, a flow a folyamatra vonatkozik, 5 rekordos információ a folyamatról (forrás/cél IP-cím, forrás/cél port, protokoll), és ha a forgalom engedélyezett vagy megtagadott volt.

## <a name="before-you-begin"></a>Előkészületek

A ARMclient a REST API a PowerShell használatával történő meghívására szolgál. A ARMClient a chocolatey címen található a [ARMClient-on](https://chocolatey.org/packages/ARMClient)

Ez a forgatókönyv feltételezi, hogy már követte a [Network Watcher létrehozása](network-watcher-create.md) című témakör lépéseit Network Watcher létrehozásához.

> [!Important]
> A (z) Network Watcher REST API meghívja az erőforráscsoport nevét a kérelem URI azonosítójában az az erőforráscsoport, amely a Network Watcher tartalmazza, és nem a diagnosztikai műveleteket végző erőforrásokat.

## <a name="scenario"></a>Eset

A cikkben ismertetett forgatókönyv bemutatja, hogyan engedélyezheti, tilthatja le és kérdezheti le a flow-naplókat a REST API használatával. Ha többet szeretne megtudni a hálózati biztonsági csoport folyamatának naplózásáról, keresse fel a [hálózati biztonsági csoport flow naplózása – áttekintés](network-watcher-nsg-flow-logging-overview.md)című témakört.

Ebben az esetben a következőket kell tennie:

* Adatforgalmi naplók engedélyezése (2. verzió)
* Folyamatok naplófájljainak letiltása
* Lekérdezési folyamat naplófájljainak állapota

## <a name="log-in-with-armclient"></a>Bejelentkezés a ARMClient

Jelentkezzen be a armclient az Azure-beli hitelesítő adataival.

```powershell
armclient login
```

## <a name="register-insights-provider"></a>Insights-szolgáltató regisztrálása

Ahhoz, hogy a folyamatok naplózása sikeresen működjön, regisztrálni kell a **Microsoft. bepillantást** nyújtó szolgáltatót. Ha nem biztos abban, hogy a **Microsoft.** betekintő szolgáltató regisztrálva van, futtassa az alábbi szkriptet.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
armclient post "https://management.azure.com//subscriptions/${subscriptionId}/providers/Microsoft.Insights/register?api-version=2016-09-01"
```

## <a name="enable-network-security-group-flow-logs"></a>Hálózati biztonsági csoport folyamatábráinak engedélyezése

A flow-naplók 2. verziójának engedélyezésére szolgáló parancs az alábbi példában látható. Az 1. verziónál cserélje le a "version" mezőt az "1" értékre:

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$targetUri = "" # example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
$storageId = "/subscriptions/00000000-0000-0000-0000-000000000000/{resourceGroupName/providers/Microsoft.Storage/storageAccounts/{saName}"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
    'properties': {
    'storageId': '${storageId}',
    'enabled': 'true',
    'retentionPolicy' : {
            days: 5,
            enabled: true
        },
    'format': {
        'type': 'JSON',
        'version': 2
    }
    }
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/configureFlowLog?api-version=2016-12-01" $requestBody
```

Az előző példától kapott válasz a következő:

```json
{
  "targetResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}",
  "properties": {
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{saName}",
    "enabled": true,
    "retentionPolicy": {
      "days": 5,
      "enabled": true
    },
    "format": {
    "type": "JSON",
    "version": 2
    }
  }
}
```

## <a name="disable-network-security-group-flow-logs"></a>Hálózati biztonsági csoport folyamatábráinak letiltása

A következő példa használatával tiltsa le a folyamat naplóit. A hívás megegyezik a flow-naplók engedélyezésével, kivéve a **false értéket** az engedélyezett tulajdonsághoz.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$targetUri = "" # example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
$storageId = "/subscriptions/00000000-0000-0000-0000-000000000000/{resourceGroupName/providers/Microsoft.Storage/storageAccounts/{saName}"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
    'properties': {
    'storageId': '${storageId}',
    'enabled': 'false',
    'retentionPolicy' : {
            days: 5,
            enabled: true
        },
    'format': {
        'type': 'JSON',
        'version': 2
    }
    }
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/configureFlowLog?api-version=2016-12-01" $requestBody
```

Az előző példától kapott válasz a következő:

```json
{
  "targetResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}",
  "properties": {
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{saName}",
    "enabled": false,
    "retentionPolicy": {
      "days": 5,
      "enabled": true
    },
    "format": {
    "type": "JSON",
    "version": 2
    }
  }
}
```

## <a name="query-flow-logs"></a>Lekérdezési folyamat naplófájljai

A következő REST-hívás lekérdezi a folyamat naplóinak állapotát egy hálózati biztonsági csoporton.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
$targetUri = "" # example /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName/providers/Microsoft.Network/networkSecurityGroups/{nsgName}"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$requestBody = @"
{
    'targetResourceId': '${targetUri}',
}
"@

armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/queryFlowLogStatus?api-version=2016-12-01" $requestBody
```

Az alábbi példa a kapott választ:

```json
{
  "targetResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkSecurityGroups/{nsgName}",
  "properties": {
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{saName}",
    "enabled": true,
   "retentionPolicy": {
      "days": 5,
      "enabled": true
    },
    "format": {
    "type": "JSON",
    "version": 2
    }
  }
}
```

## <a name="download-a-flow-log"></a>Flow-napló letöltése

A folyamat naplójának tárolási helye a létrehozáskor van meghatározva. A rendszer a Storage-fiókba mentett adatforgalmi naplók elérésére alkalmas eszközt Microsoft Azure Storage Explorer, amely letölthető innen:  https://storageexplorer.com/

Ha meg van adva egy Storage-fiók, a rendszer a csomag rögzítési fájljait a következő helyen menti a Storage-fiókba:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [jelenítheti meg a NSG flow-naplókat a PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md) használatával

Ismerje meg, hogyan [jelenítheti meg a NSG-flow-naplókat nyílt forráskódú eszközökkel](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
