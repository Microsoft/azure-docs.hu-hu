---
title: Virtuálisgép-méretezési csoport konfigurálása meglévő Azure Load Balancer-Azure PowerShell
description: Megtudhatja, hogyan konfigurálhat egy virtuálisgép-méretezési készletet egy meglévő Azure Load Balancer.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: how-to
ms.date: 03/26/2020
ms.openlocfilehash: 919883025075ca38bcef6c23ac744528e7bd5502
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88510062"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-azure-powershell"></a>Virtuálisgép-méretezési csoport konfigurálása meglévő Azure Load Balancer használatával Azure PowerShell

Ebből a cikkből megtudhatja, hogyan konfigurálhat egy virtuálisgép-méretezési készletet egy meglévő Azure Load Balancer.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés.
- Egy meglévő standard SKU-terheléselosztó abban az előfizetésben, amelyben a virtuálisgép-méretezési csoport telepítve lesz.
- Egy Azure-Virtual Network a virtuálisgép-méretezési csoport számára.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure-cli"></a>Bejelentkezés az Azure CLI-be

Jelentkezzen be az Azure-ba.

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>Virtuálisgép-méretezési csoport üzembe helyezése meglévő Load balancerrel

Cserélje le a zárójelben lévő értékeket a konfigurációban található erőforrások nevére.

```azurepowershell-interactive

$rsg = <resource-group>
$loc = <location>
$vms = <vm-scale-set-name>
$vnt = <virtual-network>
$sub = <subnet-name>
$lbn = <load-balancer-name>
$pol = <upgrade-policy-mode>

$lb = Get-AzLoadBalancer -ResourceGroupName $rsg -Name $lbn

New-AzVmss -ResourceGroupName $rsg -Location $loc -VMScaleSetName $vms -VirtualNetworkName $vnt -SubnetName $sub -LoadBalancerName $lb -UpgradePolicyMode $pol

```

Az alábbi példa egy virtuálisgép-méretezési csoport üzembe helyezését mutatja be:

- **MyVMSS** nevű virtuálisgép-méretezési csoport
- Azure Load Balancer nevű **myLoadBalancer**
- **MyBackendPool** nevű terheléselosztó-háttérbeli készlet
- **MyVnet** nevű Azure Virtual Network
- **MySubnet** nevű alhálózat
- **MyResourceGroup** nevű erőforráscsoport

```azurepowershell-interactive

$rsg = "myResourceGroup"
$loc = "East US 2"
$vms = "myVMSS"
$vnt = "myVnet"
$sub = "mySubnet"
$pol = "Automatic"
$lbn = "myLoadBalancer"

$lb = Get-AzLoadBalancer -ResourceGroupName $rsg -Name $lbn

New-AzVmss -ResourceGroupName $rsg -Location $loc -VMScaleSetName $vms -VirtualNetworkName $vnt -SubnetName $sub -LoadBalancerName $lb -UpgradePolicyMode $pol
```
> [!NOTE]
> A méretezési csoport létrehozása után a háttér-port nem módosítható a terheléselosztó állapot-mintavételi eljárása által használt terheléselosztási szabályhoz. A port módosításához távolítsa el az állapot-mintavételt az Azure virtuálisgép-méretezési csoport frissítésével, frissítse a portot, majd konfigurálja újra az állapotot.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben egy virtuálisgép-méretezési csoport üzembe helyezését egy meglévő Azure Load Balancer.  A virtuálisgép-méretezési csoportokról és a Load balancerről további információt a következő témakörben talál:

- [Mi az Azure Load Balancer?](load-balancer-overview.md)
- [Mik a virtuálisgép-méretezési csoportok?](../virtual-machine-scale-sets/overview.md)
