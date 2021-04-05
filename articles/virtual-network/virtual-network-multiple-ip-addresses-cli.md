---
title: Több IP-címmel rendelkező virtuális gép az Azure CLI használatával
titlesuffix: Azure Virtual Network
description: Megtudhatja, hogyan rendelhet hozzá több IP-címet egy virtuális géphez az Azure parancssori felületének (CLI) használatával.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: allensu
ms.openlocfilehash: 0b5e8f28be9960a2bb4e1406a293ab5e03962681
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98680569"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-cli"></a>Több IP-cím társítása virtuális gépekhez az Azure CLI használatával

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

Ez a cikk azt ismerteti, hogyan hozhat létre virtuális gépet (VM) a Azure Resource Manager üzemi modellel az Azure CLI használatával. Több IP-cím nem rendelhető hozzá a klasszikus üzemi modellel létrehozott erőforrásokhoz. Ha többet szeretne megtudni az Azure-beli üzembe helyezési modellekről, olvassa el a [telepítési modellek ismertetése](../azure-resource-manager/management/deployment-models.md) című cikket.

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Több IP-címmel rendelkező virtuális gép létrehozása

Az alábbi lépések elmagyarázzák, hogyan hozhat létre egy példaként több IP-címmel rendelkező virtuális gépet a forgatókönyvben leírtak szerint. A megvalósításhoz szükség szerint módosítsa a változó értékeit a "" és az IP-cím típusokban. 

1. Ha még nincs telepítve, telepítse az [Azure CLI](/cli/azure/install-azure-cli) -t.
2. Hozzon létre egy nyilvános és titkos SSH-kulcspárt Linux rendszerű virtuális gépekhez az [SSH nyilvános és titkos kulcspár létrehozása Linux rendszerű virtuális gépekhez](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json)című témakör lépéseinek végrehajtásával.
3. A parancssorból jelentkezzen be a paranccsal, `az login` és válassza ki az Ön által használt előfizetést.
4. Hozza létre a virtuális gépet egy Linux vagy Mac rendszerű számítógépen a következő parancsfájl végrehajtásával. A szkript létrehoz egy erőforráscsoportot, egy virtuális hálózatot (VNet), egy hálózati adaptert három IP-konfigurációval és egy virtuális GÉPET, amelyhez a két hálózati adapter csatlakozik. A hálózati adapternek, a nyilvános IP-címnek, a virtuális hálózatnak és a virtuálisgép-erőforrásoknak ugyanabban a helyen és előfizetésben kell lenniük. Bár az erőforrások nem mindegyikének ugyanabban az erőforráscsoporthoz kell lennie, a következő parancsfájlban.

```azurecli
    
#!/bin/sh
    
RgName="myResourceGroup"
Location="westcentralus"
az group create --name $RgName --location $Location
    
# Create a public IP address resource with a static IP address using the `--allocation-method Static` option. If you
# do not specify this option, the address is allocated dynamically. The address is assigned to the resource from a pool
# of IP addresses unique to each Azure region. Download and view the file from
# https://www.microsoft.com/en-us/download/details.aspx?id=41653 that lists the ranges for each region.

PipName="myPublicIP"

# This name must be unique within an Azure location.
DnsName="myDNSName"

az network public-ip create \
--name $PipName \
--resource-group $RgName \
--location $Location \
--dns-name $DnsName\
--allocation-method Static

# Create a virtual network with one subnet

VnetName="myVnet"
VnetPrefix="10.0.0.0/16"
VnetSubnetName="mySubnet"
VnetSubnetPrefix="10.0.0.0/24"

az network vnet create \
--name $VnetName \
--resource-group $RgName \
--location $Location \
--address-prefix $VnetPrefix \
--subnet-name $VnetSubnetName \
--subnet-prefix $VnetSubnetPrefix

# Create a network interface connected to the subnet and associate the public IP address to it. Azure will create the
# first IP configuration with a static private IP address and will associate the public IP address resource to it.

NicName="MyNic1"
az network nic create \
--name $NicName \
--resource-group $RgName \
--location $Location \
--subnet $VnetSubnet1Name \
--private-ip-address 10.0.0.4
--vnet-name $VnetName \
--public-ip-address $PipName
    
# Create a second public IP address, a second IP configuration, and associate it to the NIC. This configuration has a
# static public IP address and a static private IP address.

az network public-ip create \
--resource-group $RgName \
--location $Location \
--name myPublicIP2 \
--dns-name mypublicdns2 \
--allocation-method Static

az network nic ip-config create \
--resource-group $RgName \
--nic-name $NicName \
--name IPConfig-2 \
--private-ip-address 10.0.0.5 \
--public-ip-name myPublicIP2

# Create a third IP configuration, and associate it to the NIC. This configuration has  static private IP address and   # no public IP address.

az network nic ip-config create \
--resource-group $RgName \
--nic-name $NicName \
--private-ip-address 10.0.0.6 \
--name IPConfig-3

# Note: Though this article assigns all IP configurations to a single NIC, you can also assign multiple IP configurations
# to any NIC in a VM. To learn how to create a VM with multiple NICs, read the Create a VM with multiple NICs 
# article: https://docs.microsoft.com/azure/virtual-network/virtual-network-deploy-multinic-arm-cli.

# Create a VM and attach the NIC.

VmName="myVm"

# Replace the value for the following **VmSize** variable with a value from the
# https://docs.microsoft.com/azure/virtual-machines/sizes article. The script fails if the VM size
# is not supported in the location you select. Run the `azure vm sizes --location eastcentralus` command to get a full list
# of VMs in US West Central, for example.

VmSize="Standard_DS1"

# Replace the value for the OsImage variable value with a value for *urn* from the output returned by entering the
# `az vm image list` command.

OsImage="credativ:Debian:8:latest"

Username="adminuser"

# Replace the following value with the path to your public key file. If you're creating a Windows VM, remove the following
# line and you'll be prompted for the password you want to configure for the VM.

SshKeyValue="~/.ssh/id_rsa.pub"

az vm create \
--name $VmName \
--resource-group $RgName \
--image $OsImage \
--location $Location \
--size $VmSize \
--nics $NicName \
--admin-username $Username \
--ssh-key-value $SshKeyValue
```

A virtuális gép 3 IP-konfigurációval rendelkező hálózati adapterrel való létrehozása mellett a szkript a következőt hozza létre:

- Alapértelmezés szerint egy prémium szintű felügyelt lemez, de a létrehozandó lemez típusának más beállításai is vannak. A részletekért olvassa el a [Linux rendszerű virtuális gép létrehozása az Azure CLI használatával](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) című cikket.
- Egy virtuális hálózat, amely egy alhálózattal és két nyilvános IP-címmel rendelkezik. Azt is megteheti, hogy *meglévő* virtuális hálózatot, alhálózatot, hálózati adaptert vagy nyilvános IP-cím erőforrásokat használ. Ha szeretné megtudni, hogyan használhatja a meglévő hálózati erőforrásokat további erőforrások létrehozása helyett, írja be a következőt: `az vm create -h` .

A nyilvános IP-címekhez névleges díj vonatkozik. Ha többet szeretne megtudni az IP-címek díjszabásáról, olvassa el az [IP-címek díjszabását](https://azure.microsoft.com/pricing/details/ip-addresses) ismertető oldalt. Az előfizetésben használható nyilvános IP-címek száma korlátozva van. A korlátozásokkal kapcsolatos további információkért olvassa el az [Azure korlátairól](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) szóló cikket.

A virtuális gép létrehozása után írja be a `az network nic show --name MyNic1 --resource-group myResourceGroup` parancsot a hálózati adapter konfigurációjának megtekintéséhez. A `az network nic ip-config list --nic-name MyNic1 --resource-group myResourceGroup --output table` gombra kattintva megtekintheti a hálózati adapterhez társított IP-konfigurációk listáját.

Adja hozzá a magánhálózati IP-címeket a virtuális gép operációs rendszeréhez úgy, hogy végrehajtja a jelen cikk [IP-címek hozzáadása a virtuális gép operációs rendszeréhez](#os-config) című szakaszának lépéseit.

## <a name="add-ip-addresses-to-a-vm"></a><a name="add"></a>IP-címek hozzáadása egy virtuális géphez

A következő lépések végrehajtásával további magán-és nyilvános IP-címeket adhat hozzá egy meglévő Azure hálózati adapterhez. A példák a jelen cikkben ismertetett [forgatókönyvre](#scenario) épülnek.

1. Nyisson meg egy parancssort, és hajtsa végre a szakasz további lépéseit egyetlen munkameneten belül. Ha még nincs telepítve és konfigurálva az Azure CLI, hajtsa végre az [Azure CLI telepítésének](/cli/azure/install-az-cli2?toc=%2fazure%2fvirtual-network%2ftoc.json) lépéseit, és jelentkezzen be az Azure-fiókjába a `az-login` paranccsal.

2. A követelmények alapján végezze el a következő részekben ismertetett lépéseket:

    **Magánhálózati IP-cím hozzáadása**
    
    Magánhálózati IP-cím hálózati adapterhez való hozzáadásához az alábbi parancs használatával létre kell hoznia egy IP-konfigurációt. A statikus IP-címnek az alhálózat nem használt címének kell lennie.

    ```azurecli
    az network nic ip-config create \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --private-ip-address 10.0.0.7 \
    --name IPConfig-4
    ```
    
    Tetszőleges számú konfigurációt hozhat létre, egyedi konfigurációs neveket és magánhálózati IP-címeket (statikus IP-címekkel rendelkező konfigurációk esetén).

    **Nyilvános IP-cím hozzáadása**
    
    A nyilvános IP-cím hozzáadásához társítsa azt egy új IP-konfigurációhoz vagy egy meglévő IP-konfigurációhoz. Hajtsa végre az alábbi szakasz lépéseit, ahogy szükséges.

    A nyilvános IP-címekhez névleges díj vonatkozik. Ha többet szeretne megtudni az IP-címek díjszabásáról, olvassa el az [IP-címek díjszabását](https://azure.microsoft.com/pricing/details/ip-addresses) ismertető oldalt. Az előfizetésben használható nyilvános IP-címek száma korlátozva van. A korlátozásokkal kapcsolatos további információkért olvassa el az [Azure korlátairól](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) szóló cikket.

    - **Az erőforrás hozzárendelése új IP-konfigurációhoz**
    
        Amikor nyilvános IP-címet ad hozzá egy új IP-konfigurációhoz, hozzá kell adnia egy magánhálózati IP-címet is, mivel minden IP-konfigurációnak magánhálózati IP-címmel kell rendelkeznie. Hozzáadhat egy meglévő nyilvános IP-cím-erőforrást, vagy létrehozhat egy újat is. Új létrehozásához írja be a következő parancsot:
    
        ```azurecli
        az network public-ip create \
        --resource-group myResourceGroup \
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3
        ```

        Ha új IP-konfigurációt szeretne létrehozni statikus magánhálózati IP-címmel és a társított *myPublicIP3* nyilvános IP-cím erőforrással, írja be a következő parancsot:

        ```azurecli
        az network nic ip-config create \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-5 \
        --private-ip-address 10.0.0.8
        --public-ip-address myPublicIP3
        ```

    - **Az erőforrás hozzárendelése meglévő IP-konfigurációhoz** Egy nyilvános IP-cím erőforrás csak olyan IP-konfigurációhoz társítható, amelyhez még nincs társítva. A következő parancs megadásával határozhatja meg, hogy egy IP-konfigurációhoz tartozik-e társított nyilvános IP-cím:

        ```azurecli
        az network nic ip-config list \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --query "[?provisioningState=='Succeeded'].{ Name: name, PublicIpAddressId: publicIpAddress.id }" --output table
        ```

        Visszaadott kimenet:
    
        ```output
        Name        PublicIpAddressId
        
        ipconfig1   /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
        IPConfig-2  /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
        IPConfig-3
        ```

        Mivel az *IpConfig-3* **PublicIpAddressId** oszlopa üres a kimenetben, nincs társítva nyilvános IP-cím erőforrás. Hozzáadhat egy meglévő nyilvános IP-cím-erőforrást az IpConfig-3 számára, vagy megadhatja a következő parancsot egy létrehozásához:

        ```azurecli
        az network public-ip create \
        --resource-group  myResourceGroup
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3 \
        --allocation-method Static
        ```
    
        A következő parancs megadásával rendelje hozzá a nyilvános IP-cím erőforrást az *ipconfig-3* nevű meglévő IP-konfigurációhoz:
    
        ```azurecli
        az network nic ip-config update \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-3 \
        --public-ip myPublicIP3
        ```

3. A következő parancs beírásával tekintse meg a hálózati adapterhez rendelt magánhálózati IP-címeket és nyilvános IP-cím típusú erőforrás-azonosítókat:

    ```azurecli
    az network nic ip-config list \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --query "[?provisioningState=='Succeeded'].{ Name: name, PrivateIpAddress: privateIpAddress, PrivateIpAllocationMethod: privateIpAllocationMethod, PublicIpAddressId: publicIpAddress.id }" --output table
    ```

    Visszaadott kimenet: <br>

    ```output
    Name        PrivateIpAddress    PrivateIpAllocationMethod   PublicIpAddressId
    
    ipconfig1   10.0.0.4            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
    IPConfig-2  10.0.0.5            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
    IPConfig-3  10.0.0.6            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP3
    ```
    
4. Adja hozzá a hálózati adapterhez hozzáadott magánhálózati IP-címeket a virtuális gép operációs rendszeréhez az [IP-címek hozzáadása a virtuális gép operációs rendszeréhez](#os-config) című szakasz utasításait követve. Ne adja hozzá a nyilvános IP-címeket az operációs rendszerhez.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]