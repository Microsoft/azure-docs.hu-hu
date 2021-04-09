---
title: Megerősített gazdagép létrehozása a Azure PowerShell használatával | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan hozhat létre egy Azure Bastion-gazdagépet
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/14/2020
ms.author: cherylmc
ms.openlocfilehash: ba2716613a0e950cbae5c65add410ac8a8b38955
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92077725"
---
# <a name="create-an-azure-bastion-host-using-azure-powershell"></a>Azure Bastion-gazdagép létrehozása az Azure PowerShell használatával

Ez a cikk bemutatja, hogyan hozhat létre egy Azure Bastion-gazdagépet a PowerShell használatával. Miután kiépítte az Azure Bastion szolgáltatást a virtuális hálózatban, a zökkenőmentes RDP/SSH-élmény az azonos virtuális hálózatban lévő összes virtuális gép számára elérhető. Az Azure Bastion üzembe helyezése virtuális hálózatonként történik, nem pedig előfizetés/fiók vagy virtuális gép esetében.

A [Azure Portal](./tutorial-create-host-portal.md)használatával is létrehozhat egy Azure-beli megerősített gazdagépet.

## <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy rendelkezik Azure-előfizetéssel. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Megerősített gazdagép létrehozása

Ez a szakasz segítséget nyújt egy új Azure-beli megerősített erőforrás létrehozásában Azure PowerShell használatával.

1. Hozzon létre egy virtuális hálózatot és egy Azure Bastion-alhálózatot. Létre kell hoznia az Azure Bastion-alhálózatot a **AzureBastionSubnet** név értékkel. Ez az érték lehetővé teszi, hogy az Azure tudja, melyik alhálózaton telepítse a megerősített erőforrásokat a következőre:. Ez nem más, mint az átjáró alhálózata. Legalább/27 vagy nagyobb alhálózat (/27,/26 stb.) alhálózatot kell használnia. Hozza létre a **AzureBastionSubnet** útválasztási táblák vagy delegálások nélkül. Ha hálózati biztonsági csoportokat használ a **AzureBastionSubnet**, tekintse meg a [Work with NSG](bastion-nsg.md) című cikket.

   ```azurepowershell-interactive
   $subnetName = "AzureBastionSubnet"
   $subnet = New-AzVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
   $vnet = New-AzVirtualNetwork -Name "myVnet" -ResourceGroupName "myBastionRG" -Location "westeurope" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
   ```

2. Hozzon létre egy nyilvános IP-címet az Azure Bastion számára. A nyilvános IP-cím a nyilvános IP-cím, amely az RDP/SSH elérésére használt megerősített erőforrás (az 443-as porton keresztül). A nyilvános IP-címnek ugyanabban a régióban kell lennie, mint a létrehozandó megerősített erőforrásnak.

   ```azurepowershell-interactive
   $publicip = New-AzPublicIpAddress -ResourceGroupName "myBastionRG" -name "myPublicIP" -location "westeurope" -AllocationMethod Static -Sku Standard
   ```

3. Hozzon létre egy új Azure Bastion-erőforrást a virtuális hálózat AzureBastionSubnet. A megerősített erőforrás létrehozása és üzembe helyezése körülbelül 5 percet vesz igénybe.

   ```azurepowershell-interactive
   $bastion = New-AzBastion -ResourceGroupName "myBastionRG" -Name "myBastion" -PublicIpAddress $publicip -VirtualNetwork $vnet
   ```

## <a name="next-steps"></a>Következő lépések

* További információért olvassa el a [megerősített gyakori kérdések](bastion-faq.md) című témakört.
* Ha hálózati biztonsági csoportokat kíván használni az Azure megerősített alhálózattal, tekintse meg a következő témakört: a [NSG](bastion-nsg.md)használata.
