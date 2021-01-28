---
title: Nyilvános IP-cím hozzárendelése virtuális géphez
titlesuffix: Azure Virtual Network
description: A nyilvános IP-címek virtuális géphez (VM) való hozzárendelésének megismerése a Azure Portal vagy az Azure CLI használatával.
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
ms.openlocfilehash: 6ea16da3844b8098d87d65e1016f92c69ae34067
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945155"
---
# <a name="associate-a-public-ip-address-to-a-virtual-machine"></a>Nyilvános IP-cím hozzárendelése virtuális géphez

Ebből a cikkből megtudhatja, hogyan rendeljen hozzá egy nyilvános IP-címet egy meglévő virtuális géphez (VM). Ha az internetről szeretne csatlakozni egy virtuális géphez, a virtuális gépnek hozzá kell rendelnie egy nyilvános IP-címet. Ha egy nyilvános IP-címmel rendelkező új virtuális gépet szeretne létrehozni, ezt a [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md), az [Azure parancssori felület (CLI)](virtual-network-deploy-static-pip-arm-cli.md)vagy a [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)használatával teheti meg. A nyilvános IP-címekhez névleges díj vonatkozik. Részletekért tekintse meg a [díjszabást](https://azure.microsoft.com/pricing/details/ip-addresses/). Az előfizetések által használható nyilvános IP-címek száma korlátozott. Részletekért lásd: [korlátok](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#publicip-address).

Használhatja a [Azure Portal](#azure-portal), az Azure [parancssori felületét](#azure-cli) (CLI) vagy a [PowerShellt](#powershell) , hogy egy nyilvános IP-címet rendeljen hozzá egy virtuális géphez.

## <a name="azure-portal"></a>Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Tallózással keresse meg vagy keresse meg azt a virtuális gépet, amelyhez hozzá szeretné adni a nyilvános IP-címet, majd jelölje ki.
3. A **Beállítások** területen válassza a **hálózatkezelés** lehetőséget, majd válassza ki azt a hálózati adaptert, amelyhez hozzá szeretné adni a nyilvános IP-címet, ahogyan az a következő képen látható:

   ![Hálózati adapter kiválasztása](./media/associate-public-ip-address-vm/select-nic.png)

   > [!NOTE]
   > A nyilvános IP-címek a virtuális géphez csatolt hálózati adapterekhez vannak társítva. Az előző képen a virtuális gép csak egy hálózati adapterrel rendelkezik. Ha a virtuális gépnek több hálózati adapterrel is rendelkezett, akkor mind megjelenik, és azt a hálózati adaptert válassza ki, amelyhez a nyilvános IP-címet hozzá szeretné rendelni.

4. Válassza az **IP-konfigurációk** lehetőséget, majd válasszon ki egy IP-konfigurációt az alábbi ábrán látható módon:

   ![IP-konfiguráció kiválasztása](./media/associate-public-ip-address-vm/select-ip-configuration.png)

   > [!NOTE]
   > A nyilvános IP-címek egy hálózati adapter IP-konfigurációjáról vannak társítva. Az előző képen a hálózati adapter egyetlen IP-konfigurációval rendelkezik. Ha a hálózati adapter több IP-konfigurációval is rendelkezett, azok mind megjelennek a listában, és azt az IP-konfigurációt szeretné kiválasztani, amelyhez hozzá szeretné rendelni a nyilvános IP-címet.

5. Válassza az **engedélyezve** lehetőséget, majd válassza az **IP-cím (*kötelező beállítások konfigurálása*)** lehetőséget. Válasszon egy meglévő nyilvános IP-címet, amely automatikusan bezárja a **nyilvános IP-cím választása** mezőt. Ha nincs elérhető nyilvános IP-cím, létre kell hoznia egyet. További információt a [nyilvános IP-cím létrehozása](virtual-network-public-ip-address.md#create-a-public-ip-address)című témakörben talál. Válassza a **Mentés** lehetőséget, ahogy az a következő képen is látható, majd az IP-konfigurációhoz tartozó mező bezárásához.

   ![Nyilvános IP-cím engedélyezése](./media/associate-public-ip-address-vm/enable-public-ip-address.png)

   > [!NOTE]
   > A megjelenő nyilvános IP-címek azok, amelyek ugyanabban a régióban találhatók, mint a virtuális gép. Ha a régióban több nyilvános IP-cím is létrehozva van, akkor az összes itt jelenik meg. Ha bármelyik szürkén jelenik meg, az azért van, mert a címe már egy másik erőforráshoz van társítva.

6. Tekintse meg az IP-konfigurációhoz rendelt nyilvános IP-címet, ahogy az az alábbi képen is látható. Előfordulhat, hogy egy IP-cím megjelenése eltarthat néhány másodpercig.

   ![Hozzárendelt nyilvános IP-cím megtekintése](./media/associate-public-ip-address-vm/view-assigned-public-ip-address.png)

   > [!NOTE]
   > A cím az egyes Azure-régiókban használt címek készletéből van hozzárendelve. Az egyes régiókban használt címkészlet listáját itt tekintheti meg: [Microsoft Azure Datacenter IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653). A hozzárendelt címek a régióhoz használt készletek bármely címe lehet. Ha szüksége van a régió egy adott készletéhez hozzárendelni kívánt címére, használja a [nyilvános IP-cím előtagját](public-ip-address-prefix.md).

7. Hálózati biztonsági csoportba tartozó biztonsági szabályokkal [engedélyezze a virtuális gép hálózati forgalmát](#allow-network-traffic-to-the-vm) .

## <a name="azure-cli"></a>Azure CLI

Telepítse az [Azure CLI](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json)-t, vagy használja a Azure Cloud shell. Az Azure Cloud Shell olyan ingyenes Bash-felület, amelyet közvetlenül futtathat az Azure Portalon. A fiókjával való használat érdekében az Azure CLI már előre telepítve és konfigurálva van rajta. Válassza ki az alábbi CLI-parancsok **kipróbálása** gombját. A **kipróbálás** gombra kattintva meghívja a Cloud shell, amelybe bejelentkezhet az Azure-fiókjába.

1. Ha a parancssori felületet a Bashben helyileg használja, jelentkezzen be az Azure-ba a használatával `az login` .
2. A nyilvános IP-cím egy virtuális géphez csatolt hálózati adapter IP-konfigurációjához van társítva. A nyilvános IP-cím IP-konfigurációhoz való hozzárendeléséhez használja az az [Network NIC-IP-config Update](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-update) parancsot. A következő példa egy *myVMPublicIP* nevű meglévő nyilvános IP-címet társít a *ipconfigmyVM* nevű meglévő hálózati adapter nevű *myVMVMNic* , amely egy *myResourceGroup* nevű erőforráscsoport.
  
   ```azurecli-interactive
   az network nic ip-config update \
     --name ipconfigmyVM \
     --nic-name myVMVMNic \
     --resource-group myResourceGroup \
     --public-ip-address myVMPublicIP
   ```

   - Ha nem rendelkezik meglévő nyilvános IP-címmel, akkor hozzon létre egyet az az [Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create) paranccsal. A következő parancs például egy *myVMPublicIP* nevű nyilvános IP-címet hoz létre egy *myResourceGroup* nevű erőforráscsoporthoz.
  
     ```azurecli-interactive
     az network public-ip create --name myVMPublicIP --resource-group myResourceGroup
     ```

     > [!NOTE]
     > Az előző parancs egy nyilvános IP-címet hoz létre, amely alapértelmezett értékekkel rendelkezik számos, a testre szabható beállításhoz. Az összes nyilvános IP-cím beállításával kapcsolatos további tudnivalókért tekintse meg [a nyilvános IP-cím létrehozása](virtual-network-public-ip-address.md#create-a-public-ip-address)című témakört. A cím az egyes Azure-régiókban használt nyilvános IP-címek készletéhez van hozzárendelve. Az egyes régiókban használt címkészlet listáját itt tekintheti meg: [Microsoft Azure Datacenter IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653).

   - Ha nem ismeri a virtuális géphez csatlakoztatott hálózati adapter nevét, az az [VM NIC List](/cli/azure/vm/nic#az-vm-nic-list) paranccsal tekintheti meg őket. A következő parancs például felsorolja a *myVM* nevű virtuális géphez csatolt hálózati adapterek nevét egy *myResourceGroup* nevű erőforráscsoport:

     ```azurecli-interactive
     az vm nic list --vm-name myVM --resource-group myResourceGroup
     ```

     A kimenet egy vagy több olyan sort tartalmaz, amelyek az alábbi példához hasonlóak:
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

     Az előző példában a *myVMVMNic* a hálózati adapter neve.

   - Ha nem ismeri a hálózati adapter IP-konfigurációjának nevét, az az [Network NIC IP-config List](/cli/azure/network/nic/ip-config#az-network-nic-ip-config-list) paranccsal kérheti le őket. A következő parancs például felsorolja a *myVMVMNic* nevű hálózati adapter IP-konfigurációinak neveit egy *myResourceGroup* nevű erőforráscsoport:

     ```azurecli-interactive
     az network nic ip-config list --nic-name myVMVMNic --resource-group myResourceGroup --out table
     ```

3. Tekintse meg az IP-konfigurációhoz rendelt nyilvános IP-címet az az [VM List-IP-Addresss](/cli/azure/vm#az-vm-list-ip-addresses) paranccsal. A következő példa egy *myVM* nevű meglévő virtuális géphez HOZZÁRENDELT IP-címeket mutatja be egy *myResourceGroup* nevű erőforráscsoporthoz.

   ```azurecli-interactive
   az vm list-ip-addresses --name myVM --resource-group myResourceGroup --out table
   ```

   > [!NOTE]
   > A cím az egyes Azure-régiókban használt címek készletéből van hozzárendelve. Az egyes régiókban használt címkészlet listáját itt tekintheti meg: [Microsoft Azure Datacenter IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653). A hozzárendelt címek a régióhoz használt készletek bármely címe lehet. Ha szüksége van a régió egy adott készletéhez hozzárendelni kívánt címére, használja a [nyilvános IP-cím előtagját](public-ip-address-prefix.md).

4. Hálózati biztonsági csoportba tartozó biztonsági szabályokkal [engedélyezze a virtuális gép hálózati forgalmát](#allow-network-traffic-to-the-vm) .

## <a name="powershell"></a>PowerShell

Telepítse a [PowerShellt](/powershell/azure/install-az-ps), vagy használja a Azure Cloud shell. Az Azure Cloud Shell olyan ingyenes felület, amelyet közvetlenül futtathat az Azure Portalon. A PowerShell előre telepítve van, és konfigurálva van a fiókkal való használatra. Válassza ki az alábbi PowerShell-parancsok **kipróbálása** gombját. A **kipróbálás** gombra kattintva meghívja a Cloud shell, amelybe bejelentkezhet az Azure-fiókjába.

1. Ha helyileg használja a PowerShellt, jelentkezzen be az Azure-ba a használatával `Connect-AzAccount` .
2. A nyilvános IP-cím egy virtuális géphez csatolt hálózati adapter IP-konfigurációjához van társítva. A [Get-AzVirtualNetwork](/powershell/module/Az.Network/Get-AzVirtualNetwork) és a [Get-AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Get-AzVirtualNetworkSubnetConfig) parancs használatával szerezze be azt a virtuális hálózatot és alhálózatot, amelyen a hálózati adapter található. Ezután a [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) paranccsal szerezzen be egy hálózati adaptert és a [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) parancsot egy meglévő nyilvános IP-cím lekéréséhez. Ezután a [set-AzNetworkInterfaceIpConfig](/powershell/module/Az.Network/Set-AzNetworkInterfaceIpConfig) paranccsal rendelje hozzá a nyilvános IP-címet az IP-konfigurációhoz, és a [set-AzNetworkInterface](/powershell/module/Az.Network/Set-AzNetworkInterface) parancs használatával írja az új IP-konfigurációt a hálózati adapterre.

   Az alábbi példa a *myVMPublicIP* nevű meglévő nyilvános IP-címet társítja egy *myVMVMNic* nevű meglévő hálózati adapter *ipconfigmyVM* nevű, egy *myVMSubnet* nevű alhálózatban egy *myVMVNet* nevű virtuális hálózatban. Minden erőforrás egy *myResourceGroup* nevű erőforráscsoport.
  
   ```azurepowershell-interactive
   $vnet = Get-AzVirtualNetwork -Name myVMVNet -ResourceGroupName myResourceGroup
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name myVMSubnet -VirtualNetwork $vnet
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $pip = Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup
   $nic | Set-AzNetworkInterfaceIpConfig -Name ipconfigmyVM -PublicIPAddress $pip -Subnet $subnet
   $nic | Set-AzNetworkInterface
   ```

   - Ha nem rendelkezik meglévő nyilvános IP-címmel, a [New-AzPublicIpAddress](/powershell/module/Az.Network/New-AzPublicIpAddress) parancs használatával hozzon létre egyet. Az alábbi parancs például egy *myVMPublicIP* nevű *dinamikus* nyilvános IP-címet hoz létre egy *myResourceGroup* nevű erőforráscsoporthoz a *eastus* régióban.
  
     ```azurepowershell-interactive
     New-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup -AllocationMethod Dynamic -Location eastus
     ```

     > [!NOTE]
     > Az előző parancs egy nyilvános IP-címet hoz létre, amely alapértelmezett értékekkel rendelkezik számos, a testre szabható beállításhoz. Az összes nyilvános IP-cím beállításával kapcsolatos további tudnivalókért tekintse meg [a nyilvános IP-cím létrehozása](virtual-network-public-ip-address.md#create-a-public-ip-address)című témakört. A cím az egyes Azure-régiókban használt nyilvános IP-címek készletéhez van hozzárendelve. Az egyes régiókban használt címkészlet listáját itt tekintheti meg: [Microsoft Azure Datacenter IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653).

   - Ha nem ismeri a virtuális géphez csatolt hálózati adapter nevét, a [Get-AzVM](/powershell/module/Az.Compute/Get-AzVM) paranccsal megtekintheti őket. A következő parancs például felsorolja a *myVM* nevű virtuális géphez csatolt hálózati adapterek nevét egy *myResourceGroup* nevű erőforráscsoport:

     ```azurepowershell-interactive
     $vm = Get-AzVM -name myVM -ResourceGroupName myResourceGroup
     $vm.NetworkProfile
     ```

     A kimenet egy vagy több olyan sort tartalmaz, amelyek az alábbi példához hasonlóak. A példában szereplő kimenetben a *myVMVMNic* a hálózati adapter neve.
  
     ```
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic",
     ```

   - Ha nem ismeri annak a virtuális hálózatnak vagy alhálózatnak a nevét, amelyhez a hálózati adapter tartozik, az `Get-AzNetworkInterface` paranccsal tekintheti meg az adatokat. A következő parancs például lekéri a *myVMVMNic* nevű hálózati adapter virtuális hálózatát és alhálózati adatait egy *myResourceGroup* nevű erőforráscsoport-csoportba:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $ipConfigs = $nic.IpConfigurations
     $ipConfigs.Subnet | Select Id
     ```

     A kimenet egy vagy több olyan sort tartalmaz, amelyek az alábbi példához hasonlóak. A példában szereplő kimenetben a *myVMVNET* a virtuális hálózat neve, a *myVMSubnet* pedig az alhálózat neve.
  
     ```
     "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVMVNET/subnets/myVMSubnet",
     ```

   - Ha nem ismeri a hálózati adapter IP-konfigurációjának nevét, a [Get-AzNetworkInterface](/powershell/module/Az.Network/Get-AzNetworkInterface) paranccsal kérheti le őket. A következő parancs például felsorolja a *myVMVMNic* nevű hálózati adapter IP-konfigurációinak neveit egy *myResourceGroup* nevű erőforráscsoport:

     ```azurepowershell-interactive
     $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
     $nic.IPConfigurations
     ```

     A kimenet egy vagy több olyan sort tartalmaz, amelyek az alábbi példához hasonlóak. A példában szereplő kimenetben a *ipconfigmyVM* az IP-konfiguráció neve.
  
     ```
     Id     : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myVMVMNic/ipConfigurations/ipconfigmyVM
     ```

3. Tekintse meg az IP-konfigurációhoz rendelt nyilvános IP-címet a [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) paranccsal. A következő példa egy *myVMPublicIP* nevű nyilvános IP-címhez rendelt címet jelenít meg egy *myResourceGroup* nevű erőforráscsoportban.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name myVMPublicIP -ResourceGroupName myResourceGroup | Select IpAddress
   ```

   Ha nem ismeri az IP-konfigurációhoz rendelt nyilvános IP-cím nevét, futtassa a következő parancsokat a lekéréséhez:

   ```azurepowershell-interactive
   $nic = Get-AzNetworkInterface -Name myVMVMNic -ResourceGroupName myResourceGroup
   $nic.IPConfigurations
   $address = $nic.IPConfigurations.PublicIpAddress
   $address | Select Id
   ```

   A kimenet egy vagy több olyan sort tartalmaz, amelyek az alábbi példához hasonlóak. A példában szereplő kimenetben a *myVMPublicIP* az IP-konfigurációhoz rendelt nyilvános IP-cím neve.

   ```
   "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myVMPublicIP"
   ```

   > [!NOTE]
   > A cím az egyes Azure-régiókban használt címek készletéből van hozzárendelve. Az egyes régiókban használt címkészlet listáját itt tekintheti meg: [Microsoft Azure Datacenter IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653). A hozzárendelt címek a régióhoz használt készletek bármely címe lehet. Ha szüksége van a régió egy adott készletéhez hozzárendelni kívánt címére, használja a [nyilvános IP-cím előtagját](public-ip-address-prefix.md).

4. Hálózati biztonsági csoportba tartozó biztonsági szabályokkal [engedélyezze a virtuális gép hálózati forgalmát](#allow-network-traffic-to-the-vm) .

## <a name="allow-network-traffic-to-the-vm"></a>A virtuális géphez való hálózati forgalom engedélyezése

Ahhoz, hogy az internetről csatlakozhasson a nyilvános IP-címhez, győződjön meg arról, hogy a szükséges portok nyitva vannak minden olyan hálózati biztonsági csoportban, amelyet a hálózati adapterhez, a hálózati adapterhez vagy mindkettőhöz kapcsolódott. Bár a biztonsági csoportok a hálózati adapter magánhálózati IP-címére szűrik a forgalmat, ha a bejövő internetes forgalom a nyilvános IP-címen érkezik, az Azure lefordítja a nyilvános címet a magánhálózati IP-címére, így ha egy hálózati biztonsági csoport meggátolja a forgalmat, a nyilvános IP-címmel folytatott kommunikáció meghiúsul. A hálózati adapterek és az alhálózatok érvényes biztonsági szabályait a [portál](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-portal), a [CLI](diagnose-network-traffic-filter-problem.md#diagnose-using-azure-cli)vagy a [PowerShell](diagnose-network-traffic-filter-problem.md#diagnose-using-powershell)használatával tekintheti meg.

## <a name="next-steps"></a>Következő lépések

Hálózati biztonsági csoporttal engedélyezze a bejövő internetes forgalmat a virtuális gépre. A hálózati biztonsági csoportok létrehozásával kapcsolatos további információkért lásd: [a hálózati biztonsági csoportok használata](manage-network-security-group.md#work-with-network-security-groups). A hálózati biztonsági csoportokkal kapcsolatos további tudnivalókért tekintse meg a [biztonsági csoportok](./network-security-groups-overview.md)című témakört.
