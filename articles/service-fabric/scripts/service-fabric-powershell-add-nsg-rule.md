---
title: Hálózati biztonsági csoport szabályának hozzáadása a PowerShellben
description: Azure PowerShell parancsfájl-minta – egy hálózati biztonsági csoport hozzáadásával engedélyezi a bejövő forgalmat egy adott porton.
services: service-fabric
documentationcenter: ''
author: athinanthny
manager: chackdan
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 11/28/2017
ms.author: atsenthi
ms.custom: mvc
ms.openlocfilehash: 33ee8028031bf94d69d11b08c347f84db94494a3
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610420"
---
# <a name="add-an-inbound-network-security-group-rule"></a>Bejövő hálózati biztonsági csoport szabályának hozzáadása

Ez a példa egy hálózati biztonsági csoportra vonatkozó szabályt hoz létre, amely engedélyezi a bejövő forgalmat a 8081-as porton.  A parancsfájl lekéri a hálózati biztonsági csoportot, létrehoz egy új hálózati biztonsági konfigurációs szabályt, és frissíti a hálózati biztonsági csoportot. Szabja testre a paramétereket szükség szerint.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Ha szükséges, telepítse a Azure PowerShellt a [Azure PowerShell útmutatóban](/powershell/azure/overview)található utasítások alapján. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/service-fabric/add-inbound-nsg-rule/add-inbound-nsg-rule.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Lekéri a `Microsoft.Network/networkSecurityGroups` erőforrást. |
|[Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)| Lekéri a hálózati biztonsági csoportot név szerint.|
|[Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Hálózati biztonsági szabály konfigurációját adja hozzá egy hálózati biztonsági csoporthoz. |
|[Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup)| Beállítja egy hálózati biztonsági csoport céljának állapotát.|

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/overview).
