---
title: Nyilvános IPv6-címek és címtartományok foglalása egy Azure-beli virtuális hálózaton
titlesuffix: Azure Virtual Network
description: Megtudhatja, hogyan foglalhat le nyilvános IPv6-címeket és-címtartományt egy Azure-beli virtuális hálózaton.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: ac79e1eb5c4f7448dc17804cd8aac3cba582497e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88509943"
---
# <a name="reserve-public-ipv6-address-prefix"></a>Nyilvános IPv6-cím előtagjának foglalása
Az IPv6 for Azure Virtual Network (VNet) lehetővé teszi, hogy az Azure-ban IPv6-és IPv4-kapcsolaton keresztül is üzemeltetheti az alkalmazásokat a virtuális hálózaton belül és az internetről. Az egyes IPv6-címek lefoglalása mellett az Azure IPv6-címek (más néven IP-előtag) összefüggő tartományait is fenntarthatja a használatra. Ez a cikk bemutatja, hogyan hozhat létre IPv6 nyilvános IP-címeket és címtartományt a Azure PowerShell és a parancssori felület használatával.


## <a name="create-a-single-reserved-ipv6-public-ip"></a>Egyetlen fenntartott IPv6 nyilvános IP-cím létrehozása

### <a name="using-azure-powershell"></a>Az Azure PowerShell használata

Egy fenntartott (statikus) IPv6 nyilvános IP-címet a következő módon hozhat létre a [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) Azure PowerShell használatával:

```azurepowershell
 $myOwnIPv6Address = New-AzPublicIpAddress `
 -name PIPv6_WestUS `
 -ResourceGroup MyRG `
 -Location "West US" `
 -Sku Standard `
 -allocationMethod static `
 -IpAddressVersion IPv6
 ```

### <a name="using-azure-cli"></a>Az Azure parancssori felület használata

 Létrehozhat egyetlen fenntartott (statikus) IPv6 nyilvános IP-címet az Azure CLI-vel az [az Network Public-IP Create](/cli/azure/network/public-ip) paranccsal:

```azurecli
 az network public-ip create \
 --name dsPublicIP_v6 \
 --resource-group UpgradeInPlace_CLI_RG1 \
 --location WestUS \
 --sku Standard  \
 --allocation-method static  \
 --version IPv6
```

## <a name="create-a-reserved-ipv6-prefix-range"></a>Fenntartott IPv6-előtag létrehozása (tartomány)

IPv6-előtag foglalásához adja hozzá az IPv6 IP-címét ugyanahhoz a parancshoz, amely az IPv4-előtagok létrehozásához használatos. A következő parancsok létrehoznak egy méret/125 (8 IPv6-cím) előtagot.

### <a name="using-azure-powershell"></a>Az Azure PowerShell használata

Nyilvános IPv6-címet az [az Network Public-IP Create](/powershell/module/az.network/new-azpublicipprefix) paranccsal hozhat létre az Azure CLI használatával az alábbi módon:
```azurepowershell
 $myOwnIPv6Prefix = New-AzPublicIpPrefix `
 -name IPv6PrefixWestUS `
 -ResourceGroupName MyRG `
 -Location "West US" `
 -Sku Standard `
 -IpAddressVersion IPv6 `
 -PrefixLength 125
```

### <a name="using-azure-cli"></a>Az Azure parancssori felület használata

Az Azure CLI-vel az alábbi módon hozhat létre nyilvános IPv6-címeket:

```azurecli
az network public-ip prefix create \
--name IPv6PrefixWestUS \
--resource-group MyRG \
--location WestUS \
--version IPv6 \
--length 125
```

## <a name="allocate-a-public-ip-address-from-a-reserved-ipv6-prefix"></a>Nyilvános IP-cím lefoglalása egy fenntartott IPv6-előtagból

### <a name="using-azure-powershell"></a>Az Azure PowerShell használata

 Egy fenntartott előtaggal rendelkező statikus IPv6 nyilvános IP-címet úgy hozhat létre, hogy hozzáadja az `-PublicIpPrefix` argumentumot a nyilvános IP-cím a Azure PowerShell használatával történő létrehozásakor. Az alábbi példa azt feltételezi, hogy egy előtag létrehozása és tárolása egy nevű PowerShell-változóban történik: *$MyOwnIPv 6prefix*.

```azurepowershell
 $MyIPv6PublicIPFromMyReservedPrefix = New-AzPublicIpAddress \
 -name PIPv6_fromPrefix `
 -ResourceGroup DsStdLb04 `
 -Location "West Central US" `
 -Sku Standard `
 -allocationMethod static `
 -IpAddressVersion IPv6 `
 -PublicIpPrefix $myOwnIPv6Prefix
```

### <a name="using-azure-cli"></a>Az Azure parancssori felület használata

Az alábbi példa feltételezi, hogy egy előtagot hoztak létre és tároltak egy nevű CLI-változóban: *IPv6PrefixWestUS*.

```azurecli
az network public-ip create \
--name dsPublicIP_v6 \
--resource-group UpgradeInPlace_CLI_RG1 \
--location WestUS \
--sku Standard \
--allocation-method static \
--version IPv6 \
--public-ip-prefix  IPv6PrefixWestUS
```

## <a name="next-steps"></a>Következő lépések
- További információ az [IPv6-cím előtagjáról](ipv6-public-ip-address-prefix.md).
- További információ az [IPv6-címekről](ipv6-overview.md).
