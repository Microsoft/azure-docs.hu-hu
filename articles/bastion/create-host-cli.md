---
title: Megerősített gazdagép létrehozása az Azure CLI használatával | Azure-bástya
description: Ebből a cikkből megtudhatja, hogyan hozhat létre és törölhet egy megerősített gazdagépet
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/14/2020
ms.author: cherylmc
ms.openlocfilehash: 8654b557de8242910c665596ca8bfd29fe86d1ff
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106578830"
---
# <a name="create-an-azure-bastion-host-using-azure-cli"></a>Azure Bastion-gazdagép létrehozása az Azure CLI használatával

Ez a cikk bemutatja, hogyan hozhat létre Azure Bastion-gazdagépet az Azure CLI használatával. Miután kiépítte az Azure Bastion szolgáltatást a virtuális hálózatban, a zökkenőmentes RDP/SSH-élmény az azonos virtuális hálózatban lévő összes virtuális gép számára elérhető. Az Azure Bastion üzembe helyezése virtuális hálózatonként történik, nem pedig előfizetés/fiók vagy virtuális gép esetében.

Lehetőség van arra is, hogy az [Azure Portal](./tutorial-create-host-portal.md)használatával vagy [Azure PowerShell](bastion-create-host-powershell.md)használatával hozzon létre egy Azure Bastion-gazdagépet.

## <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy rendelkezik Azure-előfizetéssel. Ha még nincs Azure-előfizetése, aktiválhatja [MSDN-előfizetői előnyeit](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details), vagy regisztrálhat egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [Cloud Shell CLI](../../includes/vpn-gateway-cloud-shell-cli.md)]

 >[!NOTE]
 >Az Azure Bastion és az Azure saját DNS zónák használata jelenleg nem támogatott. Mielőtt elkezdené, győződjön meg arról, hogy a megerősített erőforrás üzembe helyezését megtervező virtuális hálózat nem kapcsolódik privát DNS-zónához.
 >

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Megerősített gazdagép létrehozása

Ez a szakasz segítséget nyújt egy új Azure Bastion-erőforrás létrehozásában az Azure CLI használatával.

> [!NOTE]
> Ahogy az a példákban is látható, a `--location` paramétert használja `--resource-group` minden parancshoz, hogy az erőforrások együtt legyenek telepítve.

1. Hozzon létre egy virtuális hálózatot és egy Azure Bastion-alhálózatot. Létre kell hoznia az Azure Bastion-alhálózatot a **AzureBastionSubnet** név értékkel. Ez az érték lehetővé teszi, hogy az Azure tudja, melyik alhálózaton telepítse a megerősített erőforrásokat a következőre:. Ez nem más, mint az átjáró alhálózata. Legalább/27 vagy nagyobb alhálózat (/27,/26 stb.) alhálózatot kell használnia. Hozza létre a **AzureBastionSubnet** útválasztási táblák vagy delegálások nélkül. Ha hálózati biztonsági csoportokat használ a **AzureBastionSubnet**, tekintse meg a [Work with NSG](bastion-nsg.md) című cikket.

   ```azurecli-interactive
   az network vnet create --resource-group MyResourceGroup --name MyVnet --address-prefix 10.0.0.0/16 --subnet-name AzureBastionSubnet --subnet-prefix 10.0.0.0/24 --location northeurope
   ```

2. Hozzon létre egy nyilvános IP-címet az Azure Bastion számára. A nyilvános IP-cím a nyilvános IP-cím, amely az RDP/SSH elérésére használt megerősített erőforrás (az 443-as porton keresztül). A nyilvános IP-címnek ugyanabban a régióban kell lennie, mint a létrehozandó megerősített erőforrásnak.

   ```azurecli-interactive
   az network public-ip create --resource-group MyResourceGroup --name MyIp --sku Standard --location northeurope
   ```

3. Hozzon létre egy új Azure Bastion-erőforrást a virtuális hálózat AzureBastionSubnet. A megerősített erőforrás létrehozása és üzembe helyezése körülbelül 5 percet vesz igénybe.

   ```azurecli-interactive
   az network bastion create --name MyBastion --public-ip-address MyIp --resource-group MyResourceGroup --vnet-name MyVnet --location northeurope
   ```

## <a name="next-steps"></a>Következő lépések

* További információért olvassa el a [megerősített gyakori kérdések](bastion-faq.md) című témakört.
* Ha hálózati biztonsági csoportokat kíván használni az Azure megerősített alhálózattal, tekintse meg a következő témakört: a [NSG](bastion-nsg.md)használata.
