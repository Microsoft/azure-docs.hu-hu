---
title: Azure PowerShell-példaszkript – Az RPD-porttartomány módosítása | Microsoft Docs
description: Azure PowerShell-példaszkript – Módosítja egy üzembe helyezett fürt RPD-porttartományát.
services: service-fabric
tags: azure-service-management
author: athinanthny
ms.author: atsenthi
ms.service: service-fabric
ms.workload: multiple
ms.topic: sample
ms.date: 03/19/2018
ms.openlocfilehash: 750a2100d34e02cac7c613cc6b95160fc348b535
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96576366"
---
# <a name="update-the-rdp-port-range-values"></a>Az RPD-porttartomány értékeinek frissítése

Ez a példaszkript módosítja az RPD-porttartomány értékeit a fürtcsomópont virtuális gépein a fürt üzembe helyezése után.  Az Azure PowerShellre azért van szükség, hogy a rendszer ne váltson a háttérben lévő virtuális gépek között.  A parancsfájl lekéri az `Microsoft.Network/loadBalancers` erőforrást a fürt erőforráscsoporthoz, és frissíti a `inboundNatPools.frontendPortRangeStart` és az `inboundNatPools.frontendPortRangeEnd` értékeket. Szabja testre a paramétereket szükség szerint.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Szükség esetén telepítse az Azure PowerShellt az [Azure PowerShell útmutatójának](/powershell/azure/) utasításait követve.

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/service-fabric/change-rdp-port-range/change-rdp-port-range.ps1 "Update the RDP port range values")]

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Lekéri a `Microsoft.Network/loadBalancers` erőforrást. |
|[Set-AzResource](/powershell/module/az.resources/set-azresource)|Frissíti a `Microsoft.Network/loadBalancers` erőforrást.|

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShell modullal kapcsolatos további információért lásd az [Azure PowerShell dokumentációját](/powershell/azure/).

További Azure Powershell-példákat az Azure Service Fabrichez az [Azure PowerShell-példák](../service-fabric-powershell-samples.md) között találhat.
