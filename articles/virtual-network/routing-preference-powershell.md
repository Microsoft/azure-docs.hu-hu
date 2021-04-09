---
title: Útválasztási beállítások konfigurálása nyilvános IP-címekhez – Azure PowerShell
titlesuffix: Azure Virtual Network
description: Megtudhatja, hogyan konfigurálhatja a nyilvános IP-címek útválasztási beállításait a Azure PowerShell használatával.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: 5fac791247dbb785e087ac21f3fd5532064c6c8a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101667889"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-azure-powershell"></a>A nyilvános IP-cím útválasztási beállításának konfigurálása Azure PowerShell használatával

Ebből a cikkből megtudhatja, hogyan konfigurálhatja az útválasztási beállításokat a nyilvános IP-címekhez tartozó ISP-hálózaton (**Internet** -beállítás) a Azure PowerShell használatával. A nyilvános IP-cím létrehozása után a következő Azure-erőforrásokkal társíthatja a bejövő és kimenő adatforgalmat az internethez:

* Virtuális gép
* Virtuálisgép-méretezési csoport
* Azure Kubernetes Service (AKS)
* Internetkapcsolattal rendelkező Load Balancer
* Application Gateway
* Azure Firewall

Alapértelmezés szerint a nyilvános IP-cím útválasztási beállítása az összes Azure-szolgáltatáshoz a Microsoft globális hálózatra van állítva, és bármely Azure-szolgáltatáshoz társítható.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]
Ha a PowerShell helyi telepítését és használatát választja, akkor ehhez a cikkhez a Azure PowerShell modul 6.9.0 vagy újabb verziójára van szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup). Ez a példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot a *eastus* helyen:

```azurepowershell
$rg = New-AzResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-public-ip-with-internet-routing-preference"></a>Nyilvános IP-cím létrehozása internetes útválasztási beállítással

Az alábbi parancs egy új nyilvános IP-címet hoz létre, amely egy *, az* *USA keleti* régiójában lévő Azure-régióban elérhető útválasztási preferencia-típussal rendelkezik:

```azurepowershell
$iptagtype="RoutingPreference"
$tagName = "Internet"
$ipTag = New-AzPublicIpTag -IpTagType $iptagtype -Tag $tagName 
# attach the tag
$publicIp = New-AzPublicIpAddress  `
-Name "MyPublicIP" `
-ResourceGroupName $rg.ResourceGroupName `
-Location $rg.Location `
-IpTag $ipTag `
-AllocationMethod Static `
-Sku Standard `
-IpAddressVersion IPv4
```

A fenti létrehozott nyilvános IP-címet egy Windows vagy [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [rendszerű](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális géppel társíthatja. A CLI szakasz az oktatóanyag oldalon: [nyilvános IP-cím hozzárendelése egy virtuális géphez](associate-public-ip-address-vm.md#azure-cli) , hogy a nyilvános IP-címet a virtuális géphez rendelje. A fent létrehozott nyilvános IP-címet társíthatja egy [Azure Load Balancer](../load-balancer/load-balancer-overview.md)is, ha hozzárendeli a terheléselosztó előtér **-konfigurációjához.** A nyilvános IP-cím terheléselosztásos virtuális IP-címként (VIP) szolgál majd.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, használhatja a [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancsot az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás eltávolításához.

```azurepowershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

- További információ az [útválasztási beállításokról a nyilvános IP-címekben](routing-preference-overview.md).
- [Konfigurálja a virtuális gép útválasztási beállításait a Azure PowerShell használatával](configure-routing-preference-virtual-machine-powershell.md).
