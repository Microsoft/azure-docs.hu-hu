---
title: Nyilvános IP-cím leválasztása egy Azure-beli virtuális gépről
titlesuffix: Azure Virtual Network
description: Megtudhatja, hogyan szüntetheti meg a nyilvános IP-címet egy virtuális gépről
services: virtual-network
documentationcenter: ''
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2019
ms.author: allensu
ms.openlocfilehash: 0665cbd7aa21575337999fb5c59478955c764048
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98934202"
---
# <a name="dissociate-a-public-ip-address-from-an-azure-vm"></a>Nyilvános IP-cím leválasztása egy Azure-beli virtuális gépről 

Ez a cikk azt ismerteti, hogyan lehet leválasztani egy nyilvános IP-címet egy Azure-beli virtuális gépről (VM).

A virtuális gép nyilvános IP-címének leválasztásához használhatja a [Azure Portal](#azure-portal), az Azure [parancssori felületét](#azure-cli) (CLI) vagy a [PowerShellt](#powershell) .

## <a name="azure-portal"></a>Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Tallózással keresse meg vagy keresse meg azt a virtuális gépet, amelyhez a nyilvános IP-címet le szeretné kapcsolni, majd válassza ki azt.
3. A virtuális gép lapon válassza az **Áttekintés** lehetőséget, válassza ki a nyilvános IP-címet, ahogyan az a következő képen látható:

   ![Nyilvános IP-cím kiválasztása](./media/remove-public-ip-address/remove-public-ip-address-2.png)

4. A nyilvános IP-cím lapon válassza az **Áttekintés**, majd a **Leválasztás lehetőséget**, ahogyan az a következő képen látható:

    ![Nyilvános IP-cím leválasztása](./media/remove-public-ip-address/remove-public-ip-address-3.png)

5. A **nyilvános IP-cím** leválasztása területen válassza az **Igen** lehetőséget.

## <a name="azure-cli"></a>Azure CLI

Telepítse az [Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)-t, vagy használja a Azure Cloud shell. Az Azure Cloud Shell olyan ingyenes Bash-felület, amelyet közvetlenül futtathat az Azure Portalon. A fiókjával való használat érdekében az Azure CLI már előre telepítve és konfigurálva van rajta. Válassza ki az alábbi CLI-parancsok **kipróbálása** gombját. A **kipróbálás** gombra kattintva meghívja a Cloud shell, amelybe bejelentkezhet az Azure-fiókjába.

1. Ha a parancssori felületet a Bashben helyileg használja, jelentkezzen be az Azure-ba a használatával `az login` .
2. A nyilvános IP-cím egy virtuális géphez csatolt hálózati adapter IP-konfigurációjához van társítva. A nyilvános IP-cím IP-konfigurációból való leválasztásához használja az az [Network NIC-IP-config Update](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-update) parancsot. Az alábbi példa egy *myVMPublicIP* nevű nyilvános IP-címet választ ki egy *myVMVMNic* nevű meglévő hálózati adapter *ipconfigmyVM* nevű IP-konfigurációból, amely egy *myVM* nevű virtuális géphez van csatolva egy *myResourceGroup* nevű erőforráscsoporthoz.
  
   ```azurecli-interactive
    az network nic ip-config update \
    --name ipconfigmyVM \
    --resource-group myResourceGroup \
    --nic-name myVMVMNic \
    --remove PublicIpAddress
   ```

   Ha nem ismeri a virtuális géphez csatlakoztatott hálózati adapter nevét, az az [VM NIC List](/cli/azure/vm/nic#az-vm-nic-list) paranccsal tekintheti meg őket. A következő parancs például felsorolja a *myVM* nevű virtuális géphez csatolt hálózati adapterek nevét egy *myResourceGroup* nevű erőforráscsoport:

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     A kimenet egy vagy több olyan sort tartalmaz, amelyek az alábbi példához hasonlóak:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     Az előző példában a *myVMVMNic* a hálózati adapter neve.

   - Ha nem ismeri a hálózati adapter IP-konfigurációjának nevét, az az [Network NIC IP-config List](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-list) paranccsal kérheti le őket. A következő parancs például felsorolja a *myVMVMNic* nevű hálózati adapter nyilvános IP-konfigurációinak neveit egy *myResourceGroup* nevű erőforráscsoport:

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

   - Ha nem ismeri a hálózati adapter nyilvános IP-konfigurációjának nevét, az az [Network NIC IP-config show](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-show) parancs használatával kérheti le őket. A következő parancs például felsorolja a *myVMVMNic* nevű hálózati adapter nyilvános IP-konfigurációinak neveit egy *myResourceGroup* nevű erőforráscsoport:

     ```azurecli-interactive
     az network nic ip-config show --name ipconfigmyVM --nic-name myVMVMNic --resource-group myResourceGroup --query publicIPAddress.id
     ```


## <a name="powershell"></a>PowerShell

Telepítse a [PowerShellt](/powershell/azure/install-az-ps), vagy használja a Azure Cloud shell. Az Azure Cloud Shell olyan ingyenes felület, amelyet közvetlenül futtathat az Azure Portalon. A PowerShell előre telepítve van, és konfigurálva van a fiókkal való használatra. Válassza ki az alábbi PowerShell-parancsok **kipróbálása** gombját. A **kipróbálás** gombra kattintva meghívja a Cloud shell, amelybe bejelentkezhet az Azure-fiókjába.

1. Ha helyileg használja a PowerShellt, jelentkezzen be az Azure-ba a használatával `Connect-AzAccount` .
2. A nyilvános IP-cím egy virtuális géphez csatolt hálózati adapter IP-konfigurációjához van társítva. Hálózati adapter beszerzéséhez használja a [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) parancsot. Állítsa a nyilvános IP-cím értéket NULL értékre, majd a [set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) parancs használatával írja az új IP-konfigurációt a hálózati adapterre.

   Az alábbi példa egy *myVMPublicIP* nevű nyilvános IP-címet választ ki egy *myVMVMNic* nevű hálózati adapterről, amely egy *myVM* nevű virtuális géphez van csatolva. Minden erőforrás egy *myResourceGroup* nevű erőforráscsoport.
  
   ```azurepowershell
    $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroup myResourceGroup
    $nic.IpConfigurations.publicipaddress.id = $null
    Set-AzNetworkInterface -NetworkInterface $nic
   ```

  - Ha nem ismeri a virtuális géphez csatolt hálózati adapter nevét, a [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) paranccsal megtekintheti őket. A következő parancs például felsorolja a *myVM* nevű virtuális géphez csatolt hálózati adapterek nevét egy *myResourceGroup* nevű erőforráscsoport:

    ```azurepowershell
    $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
    $vm.NetworkProfile
    ```

     A kimenet egy vagy több olyan sort tartalmaz, amelyek az alábbi példához hasonlóak. A példában szereplő kimenetben a *myVMVMNic* a hálózati adapter neve.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Ha nem ismeri a hálózati adapter IP-konfigurációjának nevét, a [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) paranccsal kérheti le őket. A következő parancs például felsorolja a *myVMVMNic* nevű hálózati adapter IP-konfigurációinak neveit egy *myResourceGroup* nevű erőforráscsoport:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations.id
     ```

     A kimenet egy vagy több olyan sort tartalmaz, amelyek az alábbi példához hasonlóak. A példában szereplő kimenetben a *ipconfigmyVM* az IP-konfiguráció neve.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM"
     ```

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan [rendeljen hozzá egy nyilvános IP-címet egy virtuális géphez](associate-public-ip-address-vm.md).
