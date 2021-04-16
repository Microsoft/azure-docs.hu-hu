---
title: A Azure Firewall szolgáltatáscímkék áttekintése
description: A szolgáltatáscímkék IP-címelőtagok csoportjait jelölik, így a segítségükkel csökkenthető a biztonsági szabályok létrehozásának összetettsége.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 4/5/2021
ms.author: victorh
ms.openlocfilehash: 3cc1e85a18eab1adb0a1dd8307a074cb43ba0c70
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529551"
---
# <a name="azure-firewall-service-tags"></a>Azure Firewall szolgáltatáscímkék használata

A szolgáltatáscímkék IP-címelőtagok csoportjait jelölik, így a segítségükkel csökkenthető a biztonsági szabályok létrehozásának összetettsége. Nem hozhat létre saját szolgáltatáscímkéket, és nem határozhatja meg, hogy melyik IP-címeket jelöljék az egyes címkék. A szolgáltatáscímkékben lévő címelőtagokat a Microsoft kezeli, és a címek változásával automatikusan frissíti a szolgáltatáscímkéket.

Azure Firewall szolgáltatáscímkék a hálózati szabályok célmezőben használhatók. Adott IP-címek helyett használhatja őket.

## <a name="supported-service-tags"></a>Támogatott szolgáltatáscímkék

Az [Azure-tűzfal hálózati](../virtual-network/service-tags-overview.md#available-service-tags) szabályaiban használható szolgáltatáscímkék listájáért lásd: Virtuális hálózati szolgáltatáscímkék.

## <a name="configuration"></a>Konfiguráció

Azure Firewall támogatja a szolgáltatáscímkék PowerShell, Azure CLI vagy a Azure Portal.

### <a name="configure-via-azure-powershell"></a>Konfigurálás az Azure PowerShell használatával

Ebben a példában először le kell szereznünk a korábban létrehozott Azure Firewall környezetét.

```Get the context to an existing Azure Firewall
$FirewallName = "AzureFirewall"
$ResourceGroup = "AzureFirewall-RG"
$azfirewall = Get-AzFirewall -Name $FirewallName -ResourceGroupName $ResourceGroup
```

Ezután létre kell hoznunk egy új szabályt.  A Source (Forrás) vagy a Destination (Cél) mezőben megadhatja a használni kívánt Szolgáltatáscímke szöveges értékét, ahogy azt a cikkben korábban említettük.

````Create new Network Rules using Service Tags
$rule = New-AzFirewallNetworkRule -Name "AllowSQL" -Description "Allow access to Azure Database as a Service (SQL, MySQL, PostgreSQL, Datawarehouse)" -SourceAddress "10.0.0.0/16" -DestinationAddress Sql -DestinationPort 1433 -Protocol TCP
$ruleCollection = New-AzFirewallNetworkRuleCollection -Name "Data Collection" -Priority 1000 -Rule $rule -ActionType Allow
````

Ezután frissíteni kell az új hálózati szabályokkal Azure Firewall változót, amely tartalmazza az új hálózati definíciót.

````Merge the new rules into our existing Azure Firewall variable
$azFirewall.NetworkRuleCollections.add($ruleCollection)
`````

Végül véglegesnünk kell a hálózati szabály módosításait a futó Azure Firewall példányon.
````Commit the changes to Azure
Set-AzFirewall -AzureFirewall $azfirewall
````

## <a name="next-steps"></a>Következő lépések

A szabályfeldolgozási szabályokkal Azure Firewall lásd: Azure Firewall [szabályfeldolgozási logika.](rule-processing.md)
