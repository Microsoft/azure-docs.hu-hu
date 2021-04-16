---
title: NSG-forgalom naplóinak kezelése – Azure REST API
titleSuffix: Azure Network Watcher
description: Ez az oldal azt ismerteti, hogyan kezelheti a hálózati biztonsági csoportok folyamatnaplóit az Azure Network Watcher REST API
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
ms.openlocfilehash: b45d066d0996aaba2a25500f8134085f5e9b6ffb
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535187"
---
# <a name="configuring-network-security-group-flow-logs-using-rest-api"></a>Hálózati biztonsági csoport forgalomnaplóinak konfigurálása a REST API

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

A hálózati biztonsági csoport forgalmi naplói a hálózati Network Watcher, amely lehetővé teszi a hálózati biztonsági csoporton keresztül bejövő és bejövő IP-forgalomra vonatkozó információk megtekintését. Ezek a forgalmi naplók JSON formátumban vannak megírva, és szabályonként mutatják a kimenő és bejövő folyamatokat, a folyamat hálózati adapterét, a folyamat öt rekordos információit (Forrás/cél IP-cím, Forrás/célport, Protokoll), és hogy a forgalom engedélyezve vagy tiltva lett-e.

## <a name="before-you-begin"></a>Előkészületek

Az ARMclient használatával hívható meg REST API PowerShell használatával. Az ARMClient a Chocolatey csokoládés ágon található az [ARMClientnél.](https://chocolatey.org/packages/ARMClient) Az NSG-forgalom naplóinak részletes REST API itt [található](https://docs.microsoft.com/rest/api/network-watcher/flowlogs) 

Ez a forgatókönyv feltételezi, hogy már követte a Create [a Network Watcher](network-watcher-create.md) to create a Network Watcher.

> [!Important]
> A Network Watcher REST API a kérés URI-ján az erőforráscsoport nevét hívja meg, amely az erőforráscsoportot Network Watcher, nem pedig azokat az erőforrásokat, amelyeken a diagnosztikai műveleteket végzi.

## <a name="scenario"></a>Eset

A cikkben szereplő forgatókönyv bemutatja, hogyan engedélyezheti, tilthatja le és kérdezheti le a folyamatnaplókat a REST API. A hálózati biztonsági csoportok folyamatnaplózásával kapcsolatos további információkért olvassa el a Hálózati biztonsági csoportok folyamatnaplózása [– Áttekintés témakört.](network-watcher-nsg-flow-logging-overview.md)

Ebben a forgatókönyvben a következőt fogja:

* Folyamatnaplók engedélyezése (2-es verzió)
* Folyamatnaplók letiltása
* Folyamatnaplók állapotának lekérdezése

## <a name="log-in-with-armclient"></a>Bejelentkezés az ARMClienttel

Jelentkezzen be az armclientbe az Azure-beli hitelesítő adataival.

```powershell
armclient login
```

## <a name="register-insights-provider"></a>Insights-szolgáltató regisztrálása

A folyamatnaplózás sikeres munkához regisztrálni kell a **Microsoft.Insights** szolgáltatót. Ha nem biztos benne, hogy a **Microsoft.Insights** szolgáltató regisztrálva van,futtassa a következő szkriptet.

```powershell
$subscriptionId = "00000000-0000-0000-0000-000000000000"
armclient post "https://management.azure.com//subscriptions/${subscriptionId}/providers/Microsoft.Insights/register?api-version=2016-09-01"
```

## <a name="enable-network-security-group-flow-logs"></a>Hálózati biztonsági csoport forgalomnaplóinak engedélyezése

A folyamatnaplók 2-es verziójának engedélyezésére való parancs az alábbi példában látható. Az 1. verziónál cserélje le a "version" (verzió) mezőt az "1" (1) szövegre:

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

Az előző példában visszaadott válasz a következő:

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

## <a name="disable-network-security-group-flow-logs"></a>Hálózati biztonsági csoport forgalomnaplóinak letiltása

Az alábbi példa segítségével tiltsa le a folyamatnaplókat. A hívás megegyezik a folyamatnaplók engedélyezésével, de az engedélyezett tulajdonsághoz **a false** (hamis) érték van beállítva.

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

Az előző példában visszaadott válasz a következő:

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

## <a name="query-flow-logs"></a>Folyamatnaplók lekérdezése

A következő REST-hívás lekérdezi egy hálózati biztonsági csoport folyamatnaplóinak állapotát.

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

Az alábbiakban egy példát mutatunk be a visszaadott válaszra:

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

## <a name="download-a-flow-log"></a>Folyamatnapló letöltése

A folyamatnapló tárolási helye a létrehozáskor van meghatározva. A tárfiókba mentett folyamatnaplók eléréséhez megfelelő eszköz a Microsoft Azure Storage Explorer, amely innen tölthető le:  https://storageexplorer.com/

Ha meg van adva tárfiók, a csomagrögzítési fájlokat a rendszer a következő helyen lévő tárfiókba menti:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [vizualizálhatja az NSG-forgalom naplóit a PowerBI segítségével](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Ismerje meg, hogyan [vizualizálhatja az NSG-forgalom naplóit nyílt forráskódú eszközökkel](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
