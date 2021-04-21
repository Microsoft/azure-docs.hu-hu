---
title: Nyilvános IP-cím társítása virtuális géphez
titlesuffix: Azure Virtual Network
description: Megtudhatja, hogyan társítható nyilvános IP-cím egy virtuális géphez (VM) Azure Portal Azure CLI használatával.
services: virtual-network
documentationcenter: ''
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/21/2019
ms.author: allensu
ms.openlocfilehash: 6e8fe92f88a5934c55febf42a0768274211ed76f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767708"
---
# <a name="associate-a-public-ip-address-to-a-virtual-machine"></a>Nyilvános IP-cím társítása virtuális géphez

Ebből a cikkből megtudhatja, hogyan társítható nyilvános IP-cím egy meglévő virtuális géphez. Ha az internetről szeretne csatlakozni egy virtuális géphez, a virtuális géphez nyilvános IP-címnek kell társítva lennie. Ha nyilvános IP-címmel szeretne létrehozni egy új virtuális gépet, ezt a [Azure Portal,](virtual-network-deploy-static-pip-arm-portal.md)az Azure parancssori felület [(CLI)](virtual-network-deploy-static-pip-arm-cli.md)vagy a PowerShell használatával [használhatja.](virtual-network-deploy-static-pip-arm-ps.md) A nyilvános IP-címek névleges díjat számítnak fel. Részletekért lásd a [díjszabást.](https://azure.microsoft.com/pricing/details/ip-addresses/) Az előfizetésenként használható nyilvános IP-címek száma korlátozva van. Részletekért lásd a [korlátokat.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#publicip-address)

Nyilvános IP-címet [a Azure Portal,](#azure-portal)az [Azure](#azure-cli) parancssori felület (CLI) vagy a [PowerShell](#powershell) használatával társíthat egy virtuális géphez.

[!INCLUDE [ephemeral-ip-note.md](../../includes/ephemeral-ip-note.md)]

## <a name="azure-portal"></a>Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Keresse meg vagy keresse meg azt a virtuális gépet, amelybe a nyilvános IP-címet hozzá szeretné adni, majd válassza ki.
3. A **Beállítások alatt** válassza a Hálózat **lehetőséget,** majd válassza ki azt a hálózati adaptert, amelybe a nyilvános IP-címet hozzá szeretné adni, az alábbi ábrán látható módon:

   ![Hálózati adapter kiválasztása](./media/associate-public-ip-address-vm/select-nic.png)

   > [!NOTE]
   > A nyilvános IP-címek egy virtuális géphez csatolt hálózati adapterhez vannak társítva. Az előző képen a virtuális gép csak egy hálózati adaptert használ. Ha a virtuális gép több hálózati adaptert is használna, azok mind jelenne meg, és ki kell választania azt a hálózati adaptert, amelyhez a nyilvános IP-címet társítani szeretné.

4. Válassza **az IP-konfigurációk** lehetőséget, majd válasszon ki egy IP-konfigurációt az alábbi képen látható módon:

   ![IP-konfiguráció kiválasztása](./media/associate-public-ip-address-vm/select-ip-configuration.png)

   > [!NOTE]
   > A nyilvános IP-címek egy hálózati adapter IP-konfigurációihoz vannak társítva. Az előző képen a hálózati adapter egy IP-konfigurációval rendelkezik. Ha a hálózati adapter több IP-konfigurációval rendelkezik, azok mind megjelennek a listában, és azt az IP-konfigurációt kell kiválasztania, amelyhez társítani szeretné a nyilvános IP-címet.

5. Válassza **az Engedélyezve** lehetőséget, majd az **IP-cím (*Kötelező beállítások konfigurálása*) lehetőséget.** Válasszon egy meglévő nyilvános IP-címet, amely automatikusan bezárja a **Nyilvános IP-cím kiválasztása mezőt.** Ha nincsenek elérhető nyilvános IP-címek a listában, létre kell hoznia egyet. További információ: [Nyilvános IP-cím létrehozása.](virtual-network-public-ip-address.md#create-a-public-ip-address) Válassza **a Mentés** lehetőséget a következő képen látható módon, majd zárja be az IP-konfiguráció jelölőnégyzetét.

   ![Nyilvános IP-cím engedélyezése](./media/associate-public-ip-address-vm/enable-public-ip-address.png)

   > [!NOTE]
   > A megjelenő nyilvános IP-címek azok, amelyek ugyanabban a régióban vannak, mint a virtuális gép. Ha több nyilvános IP-címet hozott létre a régióban, az összes itt fog megjelenni. Ha bármelyik ki van szürkén, annak az az oka, hogy a cím már társítva van egy másik erőforráshoz.

6. Tekintse meg az IP-konfigurációhoz rendelt nyilvános IP-címet az alábbi képen látható módon. Az IP-címek megjelenése néhány másodpercet is igénybe vehet.

   ![Hozzárendelt nyilvános IP-cím megtekintése](./media/associate-public-ip-address-vm/view-assigned-public-ip-address.png)

   > [!NOTE]
   > A cím hozzárendelése az egyes Azure-régiókban használt címkészletből lesz kiosztva. Az egyes régiókhoz használt címkészletek listájának a következő [Microsoft Azure:](https://www.microsoft.com/download/details.aspx?id=41653). A hozzárendelt cím bármilyen cím lehet a régióhoz használt készletekben. Ha a régió egy adott készletéből kell hozzárendelni a címet, használjon nyilvános [IP-címelőtagot.](public-ip-address-prefix.md)

7. [Hálózati biztonsági csoportban található biztonsági](#allow-network-traffic-to-the-vm) szabályokkal engedélyezze a virtuális gépre való hálózati forgalmat.

## <a name="azure-cli"></a>Azure CLI

Telepítse az [Azure CLI-t,](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)vagy használja a Azure Cloud Shell. Az Azure Cloud Shell olyan ingyenes Bash-felület, amelyet közvetlenül futtathat az Azure Portalon. A fiókjával való használat érdekében az Azure CLI már előre telepítve és konfigurálva van rajta. Az alábbi **PARANCSSORi felületi** parancsokban válassza a Kipróbálom gombot. A **Kipróbálom lehetőség** választásával meghív egy Cloud Shell, amelyből bejelentkezhet az Azure-fiókjába.

1. Ha a CLI-t helyileg használja a Bashben, jelentkezzen be az Azure-ba a `az login` következővel: .
2. A nyilvános IP-cím egy virtuális géphez csatolt hálózati adapter IP-konfigurációját társítja. Az [az network nic-ip-config update paranccsal](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_update) társítsa a nyilvános IP-címet egy IP-konfigurációhoz. Az alábbi példa egy *meglévő, myVMPublicIP* nevű nyilvános IP-címet társít egy *meglévő, myVMVMNic* nevű hálózati adapter *ipconfigmyVM* nevű IP-konfigurációjával, amely a *myResourceGroup* nevű erőforráscsoportban található.
  
   ```azurecli-interactive
   az network nic ip-config update \
     --name ipconfigmyVM \
     --nic-name myVMVMNic \
     --resource-group myResourceGroup \
     --public-ip-address myVMPublicIP
   ```

   - Ha még nincs nyilvános IP-címe, hozzon létre egyet [az az network public-ip create paranccsal.](/cli/azure/network/public-ip#az_network_public_ip_create) A következő parancs például létrehoz egy *myVMPublicIP* nevű nyilvános IP-címet egy *myResourceGroup nevű erőforráscsoportban.*
  
     ```azurecli-interactive
     az network public-ip create --name myVMPublicIP --resource-group myResourceGroup
     ```

     > [!NOTE]
     > Az előző parancs egy nyilvános IP-címet hoz létre alapértelmezett értékekkel számos testre szabni kívánt beállításhoz. További információ a nyilvános IP-címek beállításairól: [Nyilvános IP-cím létrehozása.](virtual-network-public-ip-address.md#create-a-public-ip-address) A cím az egyes Azure-régiókhoz használt nyilvános IP-címek készletéből van kiosztva. Az egyes régiókhoz használt címkészletek listájának a következő Microsoft Azure [található:](https://www.microsoft.com/download/details.aspx?id=41653).

   - Ha nem ismeri a virtuális géphez csatolt hálózati adapter nevét, az [az vm nic list](/cli/azure/vm/nic#az_vm_nic_list) paranccsal megtekintheti őket. A következő parancs például a myResourceGroup nevű erőforráscsoportban található *myVM* nevű virtuális géphez csatolt hálózati *adapterek nevét listázza:*

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     A kimenet egy vagy több, az alábbi példához hasonló sort tartalmaz:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     Az előző példában *a myVMVMNic* a hálózati adapter neve.

   - Ha nem ismeri egy hálózati adapter IP-konfigurációjának nevét, használja [az az network nic ip-config list parancsot](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_list) azok lekérésére. A következő parancs például a myResourceGroup nevű erőforráscsoport *myVMVMNic* nevű hálózati adapterének IP-konfigurációit *listázza:*

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

3. Tekintse meg az IP-konfigurációhoz rendelt nyilvános [IP-címet az az vm list-ip-addresses paranccsal.](/cli/azure/vm#az_vm_list_ip_addresses) Az alábbi példa egy myResourceGroup nevű *erőforráscsoportban lévő, myVM* nevű meglévő virtuális géphez rendelt *IP-címeket mutatja be.*

   ```azurecli-interactive
   az vm list-ip-addresses --name myVM --resource-group myResourceGroup --out table
   ```

   > [!NOTE]
   > A cím hozzárendelése az egyes Azure-régiókban használt címkészletből lesz kiosztva. Az egyes régiókhoz használt címkészletek listájának a következő [Microsoft Azure:](https://www.microsoft.com/download/details.aspx?id=41653). A hozzárendelt cím bármilyen cím lehet a régióhoz használt készletekben. Ha a régió egy adott készletéből kell hozzárendelni a címet, használjon nyilvános [IP-címelőtagot.](public-ip-address-prefix.md)

4. [Hálózati biztonsági csoportban található biztonsági](#allow-network-traffic-to-the-vm) szabályokkal engedélyezze a virtuális gépre való hálózati forgalmat.

## <a name="powershell"></a>PowerShell

Telepítse a [PowerShellt,](/powershell/azure/install-az-ps)vagy használja a Azure Cloud Shell. Az Azure Cloud Shell olyan ingyenes felület, amelyet közvetlenül futtathat az Azure Portalon. Előre telepítette és konfigurálta a PowerShellt a fiókjával való használatra. A **következő** PowerShell-parancsokban válassza a Kipróbálom gombot. A **Try it (Próbálkozás)** lehetőség Cloud Shell, hogy be tud jelentkezni az Azure-fiókjába.

1. Ha helyileg használja a PowerShellt, jelentkezzen be az Azure-ba a `Connect-AzAccount` következővel: .
2. A nyilvános IP-cím egy virtuális géphez csatolt hálózati adapter IP-konfigurációjának van társítva. A [Get-AzVirtualNetwork](/powershell/module/Az.Network/Get-AzVirtualNetwork) és [a Get-AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Get-AzVirtualNetworkSubnetConfig) paranccsal lekért virtuális hálózat és alhálózat, amely a hálózati adaptert használja. Ezután a [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) paranccsal szerezze be a hálózati adaptert, a [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) paranccsal pedig egy meglévő nyilvános IP-címet. Ezután a [Set-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/Set-AzNetworkInterfaceIpConfig) paranccsal társítsa a nyilvános IP-címet az IP-konfigurációhoz, a [Set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) paranccsal pedig írja az új IP-konfigurációt a hálózati adapterre.

   Az alábbi példa egy *meglévő, myVMPublicIP* nevű nyilvános IP-címet társít egy *meglévő, myVMVMNic* nevű hálózati adapter *ipconfigmyVM* nevű IP-konfigurációját, amely a *myVMSubnet* nevű alhálózatban található a *myVMVNet* nevű virtuális hálózatban. Minden erőforrás egy *myResourceGroup nevű erőforráscsoportban van.*
  
   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name myVMVNet -ResourceGroupName myResourceGroup
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name myVMSubnet -VirtualNetwork $vnet
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $pip = Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup
   $nic | Set-AzNetworkInterfaceIpConfig -Name ipconfigmyVM -PublicIPAddress $pip -Subnet $subnet
   $nic | Set-AzNetworkInterface
   ```

   - Ha még nincs nyilvános IP-címe, hozzon létre egyet a [New-AzPublicIpAddress](/powershell/module/Az.Network/New-AzPublicIpAddress) paranccsal. A következő parancs például  létrehoz egy *myVMPublicIP* nevű dinamikus nyilvános IP-címet egy *myResourceGroup* nevű erőforráscsoportban az *eastus régióban.*
  
     ```azurepowershell-interactive
     New-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup -AllocationMethod Dynamic -Location eastus
     ```

     > [!NOTE]
     > Az előző parancs egy nyilvános IP-címet hoz létre alapértelmezett értékekkel számos testre szabni kívánt beállításhoz. További információ a nyilvános IP-címek beállításairól: [Nyilvános IP-cím létrehozása.](virtual-network-public-ip-address.md#create-a-public-ip-address) A cím az egyes Azure-régiókhoz használt nyilvános IP-címek készletéből van kiosztva. Az egyes régiókhoz használt címkészletek listájának a következő Microsoft Azure [található:](https://www.microsoft.com/download/details.aspx?id=41653).

   - Ha nem ismeri a virtuális géphez csatolt hálózati adapter nevét, a [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) paranccsal megtekintheti őket. A következő parancs például a myResourceGroup nevű erőforráscsoportban található *myVM* nevű virtuális géphez csatolt hálózati *adapterek nevét listázza:*

     ```azurepowershell-interactive
     $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
     $vm.NetworkProfile
     ```

     A kimenet egy vagy több, az alábbi példához hasonló sort tartalmaz. A példakimenetben *a myVMVMNic* a hálózati adapter neve.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Ha nem ismeri annak a virtuális hálózatnak vagy alhálózatnak a nevét, amely a hálózati adaptert használja, az paranccsal `Get-AzNetworkInterface` megtekintheti az információkat. A következő parancs például lekérte egy *myResourceGroup* nevű erőforráscsoport *myVMVMNic* nevű hálózati adapterének virtuális hálózatát és alhálózati adatait:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $ipConfigs = $nic.IpConfigurations
     $ipConfigs.Subnet | Select Id
     ```

     A kimenet egy vagy több, az alábbi példához hasonló sort tartalmaz. A példakimenetben a *myVMVNET* a virtuális hálózat neve, a *myVMSubnet* pedig az alhálózat neve.
  
     ```
     "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVMVNET/subnets/myVMSubnet",
     ```

   - Ha nem ismeri egy hálózati adapter IP-konfigurációjának nevét, a [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) paranccsal lekéri azokat. A következő parancs például a myResourceGroup nevű erőforráscsoport *myVMVMNic* nevű hálózati adapterének IP-konfigurációit *listázza:*

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations
     ```

     A kimenet egy vagy több, az alábbi példához hasonló sort tartalmaz. A példakimenetben *az ipconfigmyVM* egy IP-konfiguráció neve.
  
     ```
     Id     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM
     ```

3. Tekintse meg az IP-konfigurációhoz rendelt nyilvános [IP-címet a Get-AzPublicIpAddress paranccsal.](/powershell/module/az.network/get-azpublicipaddress) Az alábbi példa a *myResourceGroup* nevű erőforráscsoportban a *myVMPublicIP* nevű nyilvános IP-címhez rendelt címet mutatja be.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup | Select IpAddress
   ```

   Ha nem ismeri az IP-konfigurációhoz rendelt nyilvános IP-cím nevét, futtassa a következő parancsokat a lekért név lehöz:

   ```azurepowershell-interactive
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $nic.IPConfigurations
   $address = $nic.IPConfigurations.PublicIpAddress
   $address | Select Id
   ```

   A kimenet egy vagy több, az alábbi példához hasonló sort tartalmaz. A példakimenetben *a myVMPublicIP* az IP-konfigurációhoz rendelt nyilvános IP-cím neve.

   ```
   "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP"
   ```

   > [!NOTE]
   > A cím hozzárendelése az egyes Azure-régiókban használt címkészletből lesz kiosztva. Az egyes régiókhoz használt címkészletek listájának a következő [Microsoft Azure:](https://www.microsoft.com/download/details.aspx?id=41653). A hozzárendelt cím bármilyen cím lehet a régióhoz használt készletekben. Ha a régió egy adott készletéből kell hozzárendelni a címet, használjon nyilvános [IP-címelőtagot.](public-ip-address-prefix.md)

4. [Hálózati biztonsági csoportban található biztonsági](#allow-network-traffic-to-the-vm) szabályokkal engedélyezze a virtuális gépre való hálózati forgalmat.

## <a name="allow-network-traffic-to-the-vm"></a>Hálózati forgalom engedélyezése a virtuális géphez

Mielőtt csatlakozhat a nyilvános IP-címhez az internetről, győződjön meg arról, hogy a szükséges portok nyitva vannak bármely olyan hálózati biztonsági csoportban, amely a hálózati adapterhez, a hálózati adapter alhálózatához vagy mindkettőhöz társítva lehet. Bár a biztonsági csoportok a hálózati adapter magánhálózati IP-címére szűrik a forgalmat, amikor a bejövő internetes forgalom a nyilvános IP-címre érkezik, az Azure lefordítja a nyilvános címet a magánhálózati IP-címre, így ha egy hálózati biztonsági csoport megakadályozza a forgalmat, a nyilvános IP-címmel való kommunikáció meghiúsul. A hálózati adapterek és alhálózataik hatályos biztonsági szabályait a [Portál,](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal)a [PARANCSSORi](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli)felület vagy a PowerShell használatával [tudja megtekinteni.](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell)

## <a name="next-steps"></a>Következő lépések

Engedélyezze a virtuális gépre irányuló bejövő internetes forgalmat egy hálózati biztonsági csoporttal. A hálózati biztonsági csoportok létrehozásáról lásd: [Hálózati biztonsági csoportok kezelése.](manage-network-security-group.md#work-with-network-security-groups) A hálózati biztonsági csoportokkal kapcsolatos további információkért lásd: [Biztonsági csoportok.](./network-security-groups-overview.md)
