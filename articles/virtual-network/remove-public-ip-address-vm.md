---
title: Nyilvános IP-cím társításának társításának társítása azure-beli virtuális gépről
titlesuffix: Azure Virtual Network
description: Ismerje meg, hogyan lehet egy nyilvános IP-címet egy virtuális gépről elválasztani
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
ms.openlocfilehash: e9bfadd3e2453f0241dc2f7b8bfa5c964333bcf5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776539"
---
# <a name="dissociate-a-public-ip-address-from-an-azure-vm"></a>Nyilvános IP-cím társításának társításának társítása azure-beli virtuális gépről 

Ebből a cikkből megtudhatja, hogyan lehet egy nyilvános IP-címet egy Azure-beli virtuális gépről (VM) elválasztani.

A nyilvános [](#azure-portal)IP-Azure Portal virtuális gépről való társítását a következő parancsokkal használhatja: Azure Portal [parancssori](#azure-cli) felület (CLI) vagy [a PowerShell.](#powershell)

## <a name="azure-portal"></a>Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Keresse meg vagy keresse meg azt a virtuális gépet, amelyről le szeretné választani a nyilvános IP-címet, majd válassza ki.
3. A virtuális gép oldalán válassza az **Áttekintés lehetőséget,** majd válassza ki a nyilvános IP-címet az alábbi képen látható módon:

   ![Nyilvános IP-cím kiválasztása](./media/remove-public-ip-address/remove-public-ip-address-2.png)

4. A nyilvános IP-cím oldalon válassza az **Áttekintés** lehetőséget, majd válassza a Társítás társításának **mellőzését,** ahogy az a következő képen látható:

    ![Nyilvános IP-cím társításának mellőzása](./media/remove-public-ip-address/remove-public-ip-address-3.png)

5. A **Nyilvános IP-cím társításának mellőzés beállításnál** válassza az **Igen lehetőséget.**

## <a name="azure-cli"></a>Azure CLI

Telepítse az [Azure CLI-t,](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)vagy használja a Azure Cloud Shell. Az Azure Cloud Shell olyan ingyenes Bash-felület, amelyet közvetlenül futtathat az Azure Portalon. A fiókjával való használat érdekében az Azure CLI már előre telepítve és konfigurálva van rajta. Az alábbi **PARANCSSORi felületi** parancsokban válassza a Kipróbálom gombot. A **Kipróbálom lehetőség** választásával meghív egy Cloud Shell, amelyből bejelentkezhet az Azure-fiókjába.

1. Ha a CLI-t helyileg használja a Bashben, jelentkezzen be az Azure-ba a `az login` következővel: .
2. A nyilvános IP-cím egy virtuális géphez csatolt hálózati adapter IP-konfigurációját társítja. Használja az [az network nic-ip-config update parancsot](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_update) egy nyilvános IP-cím IP-konfigurációból való társításának a társításához. Az alábbi példa egy *myVMPublicIP* nevű nyilvános IP-címet a *myResourceGroup* nevű erőforráscsoportban lévő *myVMVMNic* nevű meglévő hálózati  adapter *ipconfigmyVM* IP-konfigurációja alapján nem társítja.
  
   ```azurecli-interactive
    az network nic ip-config update \
    --name ipconfigmyVM \
    --resource-group myResourceGroup \
    --nic-name myVMVMNic \
    --remove PublicIpAddress
   ```

   Ha nem ismeri a virtuális géphez csatolt hálózati adapter nevét, az [az vm nic list](/cli/azure/vm/nic#az_vm_nic_list) paranccsal megtekintheti őket. A következő parancs például a myResourceGroup nevű erőforráscsoportban található *myVM* nevű virtuális géphez csatolt hálózati *adapterek nevét listázza:*

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     A kimenet egy vagy több sort tartalmaz, amelyek az alábbi példához hasonlók:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     Az előző példában *a myVMVMNic* a hálózati adapter neve.

   - Ha nem ismeri egy hálózati adapter IP-konfigurációjának nevét, használja az [az network nic ip-config list parancsot](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_list) azok lekérésére. A következő parancs például a myResourceGroup nevű erőforráscsoport *myVMVMNic* nevű hálózati adapterének nyilvános IP-konfigurációit *listázza:*

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

   - Ha nem ismeri egy hálózati adapter nyilvános IP-konfigurációjának nevét, az [az network nic ip-config show](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_show) paranccsal lekéri őket. A következő parancs például a myResourceGroup nevű erőforráscsoport *myVMVMNic* nevű hálózati adapterének nyilvános IP-konfigurációit *listázza:*

     ```azurecli-interactive
     az network nic ip-config show --name ipconfigmyVM --nic-name myVMVMNic --resource-group myResourceGroup --query publicIPAddress.id
     ```


## <a name="powershell"></a>PowerShell

Telepítse a [PowerShellt,](/powershell/azure/install-az-ps)vagy használja a Azure Cloud Shell. Az Azure Cloud Shell olyan ingyenes felület, amelyet közvetlenül futtathat az Azure Portalon. Előre telepítette és konfigurálta a PowerShellt a fiókjával való használatra. A **következő** PowerShell-parancsokban válassza a Kipróbálom gombot. A **Try it (Próbálkozás)** lehetőség Cloud Shell, hogy be tud jelentkezni az Azure-fiókjába.

1. Ha helyileg használja a PowerShellt, jelentkezzen be az Azure-ba a `Connect-AzAccount` következővel: .
2. A nyilvános IP-cím egy virtuális géphez csatolt hálózati adapter IP-konfigurációjának van társítva. A [Get-AzNetworkInterface paranccsal](/powershell/module/Az.Network/Get-AzNetworkInterface) szerezze be a hálózati adaptert. Állítsa a Nyilvános IP-cím értékét null értékre, majd a [Set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) paranccsal írja az új IP-konfigurációt a hálózati adapterre.

   Az alábbi példa egy *myVMPublicIP* nevű nyilvános IP-címet nem társít a *myVMVMNic* nevű hálózati adapterről, amely egy myVM nevű virtuális géphez *van csatolva.* Minden erőforrás egy *myResourceGroup nevű erőforráscsoportban van.*
  
   ```azurepowershell
    $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroup myResourceGroup
    $nic.IpConfigurations.publicipaddress.id = $null
    Set-AzNetworkInterface -NetworkInterface $nic
   ```

  - Ha nem ismeri a virtuális géphez csatolt hálózati adapter nevét, a [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) paranccsal megtekintheti őket. A következő parancs például a myResourceGroup nevű erőforráscsoportban található *myVM* nevű virtuális géphez csatolt hálózati *adapterek nevét listázza:*

    ```azurepowershell
    $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
    $vm.NetworkProfile
    ```

     A kimenet egy vagy több, az alábbi példához hasonló sort tartalmaz. A példakimenetben *a myVMVMNic* a hálózati adapter neve.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Ha nem ismeri egy hálózati adapter IP-konfigurációjának nevét, a [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) paranccsal lekérhetőek. A következő parancs például egy *myResourceGroup* nevű erőforráscsoport *myVMVMNic* nevű hálózati adapterének IP-konfigurációit listázza:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations.id
     ```

     A kimenet egy vagy több, az alábbi példához hasonló sort tartalmaz. A példakimenetben *az ipconfigmyVM* egy IP-konfiguráció neve.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM"
     ```

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan [társítható nyilvános IP-cím egy virtuális géphez.](associate-public-ip-address-vm.md)
