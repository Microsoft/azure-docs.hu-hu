---
title: NSG-forgalom naplóinak kezelése – Azure CLI
titleSuffix: Azure Network Watcher
description: Ez az oldal azt ismerteti, hogyan kezelheti a hálózati biztonsági csoportok folyamatnaplóit az Azure Network Watcher Azure CLI-val
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
ms.openlocfilehash: a25d14660e5006aca2913053b17852c752c786d0
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107535265"
---
# <a name="configuring-network-security-group-flow-logs-with-azure-cli"></a>Hálózati biztonsági csoport forgalomnaplóinak konfigurálása az Azure CLI-val

> [!div class="op_single_selector"]
> - [Azure Portal](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [Azure CLI](network-watcher-nsg-flow-logging-cli.md)
> - [REST API](network-watcher-nsg-flow-logging-rest.md)

A hálózati biztonsági csoport forgalmi naplói a hálózati biztonsági Network Watcher, amely lehetővé teszi a hálózati biztonsági csoporton keresztül bejövő és bejövő IP-forgalomra vonatkozó információk megtekintését. Ezek a forgalmi naplók JSON formátumban vannak megírva, és szabályonként mutatják a kimenő és bejövő folyamatokat, a folyamat hálózati adapterét, a folyamat öt rekordos információit (Forrás/cél IP-cím, Forrás/célport, Protokoll), és hogy a forgalom engedélyezve vagy tiltva lett-e.

A cikkben található lépések végrehajtásához telepítenie kell az Azure parancssori felületét Mac, Linux és [Windows (CLI) rendszerekhez.](/cli/azure/install-azure-cli) Az összes folyamatnapló-parancs részletes specifikációját itt [talál](https://docs.microsoft.com/cli/azure/network/watcher/flow-log?view=azure-cli-latest)

## <a name="register-insights-provider"></a>Insights-szolgáltató regisztrálása

A folyamatnaplózás sikeres munkához regisztrálni kell a **Microsoft.Insights** szolgáltatót. Ha nem biztos benne, hogy a **Microsoft.Insights** szolgáltató regisztrálva van,futtassa a következő szkriptet.

```azurecli
az provider register --namespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs"></a>Hálózati biztonsági csoport forgalomnaplóinak engedélyezése

A folyamatnaplók engedélyezésére való parancs az alábbi példában látható:

```azurecli
az network watcher flow-log create --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName --location location
# Configure 
az network watcher flow-log create --resource-group resourceGroupName --enabled true --nsg nsgName --storage-account storageAccountName --location location --format JSON --log-version 2
```

A megadott tárfiókhoz nem lehet olyan hálózati szabály konfigurálva, amely csak az adott virtuális Microsoft-szolgáltatások korlátozza a hálózati hozzáférést. A tárfiók lehet ugyanabban vagy egy másik Azure-előfizetésben, mint az NSG, amely számára engedélyezi a folyamatnaplót. Ha különböző előfizetéseket használ, mindkettőt ugyanannak a bérlőnek kell Azure Active Directory társítva. Az egyes előfizetések fiókjának rendelkeznie kell a [szükséges engedélyekkel.](required-rbac-permissions.md) 

Ha a tárfiók a hálózati biztonsági csoporttól eltérő erőforráscsoportban vagy előfizetésben van, a név helyett a tárfiók teljes azonosítóját adja meg. Ha például a tárfiók egy *RG-Storage* nevű erőforráscsoportban található, és nem a *storageAccountName* értéket adja meg az előző parancsban, akkor a */subscriptions/{SubscriptionID}/resourceGroups/RG-Storage/providers/Microsoft.Storage/storageAccounts/storageAccountName értéket kell megadnia.*

## <a name="disable-network-security-group-flow-logs"></a>Hálózati biztonsági csoport forgalomnaplóinak letiltása

A következő példával tiltsa le a folyamatnaplókat:

```azurecli
az network watcher flow-log configure --resource-group resourceGroupName --enabled false --nsg nsgName
```

## <a name="download-a-flow-log"></a>Folyamatnapló letöltése

A folyamatnapló tárolási helye a létrehozáskor van meghatározva. A tárfiókba mentett folyamatnaplók eléréséhez megfelelő eszköz a Microsoft Azure Storage Explorer, amely innen tölthető le:  https://storageexplorer.com/

Ha meg van adva tárfiók, a folyamat naplófájlja egy tárfiókba lesz mentve a következő helyen:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```


## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [vizualizálhatja az NSG-forgalom naplóit a PowerBI segítségével](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Ismerje meg, hogyan [vizualizálhatja az NSG-forgalom naplóit nyílt forráskódú eszközökkel](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
