---
title: Háttérbeli készlet kezelése
titleSuffix: Azure Load Balancer
description: Ismerkedés a Azure Load Balancer háttér-készletének konfigurálásával és kezelésével
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 01/28/2021
ms.author: allensu
ms.openlocfilehash: 65a00cd898ef63dd3194e48ad6dd3fb73a211d6f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101709869"
---
# <a name="backend-pool-management"></a>Háttérbeli készlet kezelése
A háttér-készlet a terheléselosztó kritikus összetevője. A háttér-készlet meghatározza azt az erőforrás-csoportot, amely egy adott terheléselosztási szabály forgalmát fogja szolgálni.

A háttér-készleteket kétféleképpen lehet konfigurálni:
* Hálózati kártya (NIC)
* Az IP-cím és a Virtual Network (VNET) erőforrás-azonosító kombinációja

A meglévő virtuális gépek és virtuálisgép-méretezési csoportok használata esetén konfigurálja a háttérbeli készletet hálózati adapteren. Ez a módszer az erőforrás és a háttér-készlet közötti legközvetlenebb kapcsolatot hozza létre. 

Ha a háttérbeli készletet egy olyan IP-címtartomány alapján rendeli hozzá, amelyet később virtuális gépek és virtuálisgép-méretezési csoportok létrehozására tervez, konfigurálja a háttér-készletet IP-cím és VNET-azonosító kombinációja szerint.

Az IP-alapú és a NIC-alapú háttér-készleteket egyazon terheléselosztó számára is konfigurálhatja, de nem hozhat létre egyetlen háttérbeli készletet, amely a hálózati adapterek és az azonos készleten belüli IP-címek által megcélozott címeket is ötvözi.

A cikk konfigurációs fejezetei a következőkre összpontosítanak:

* Azure PowerShell
* Azure CLI
* REST API
* Azure Resource Manager-sablonok 

Ezek a lépések betekintést nyújtanak a háttérbeli készletek strukturálása céljából az egyes konfigurációs beállításokhoz.

## <a name="configuring-backend-pool-by-nic"></a>Háttérbeli készlet konfigurálása hálózati adapter alapján
A rendszer létrehozza a háttér-készletet a terheléselosztó művelet részeként. A hálózati adapter IP-konfigurációs tulajdonsága a háttérbeli készlet tagjainak hozzáadására szolgál.

Az alábbi példák a háttér-készlet létrehozási és feltöltési műveleteire összpontosítanak a munkafolyamat és a kapcsolat kiemeléséhez.

  >[!NOTE] 
  >Fontos megjegyezni, hogy a hálózati adapteren keresztül konfigurált háttér-készletek nem frissíthetők egy művelet részeként a háttér-készleten. A háttérbeli erőforrások hozzáadásának vagy törlésének az erőforrás hálózati adapterén kell történnie.

### <a name="powershell"></a>PowerShell
Új háttér-készlet létrehozása:
 
```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$loadBalancerName = "myLoadBalancer"
$backendPoolName = "myBackendPool"

$backendPool = 
New-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -BackendAddressPoolName $backendPoolName  
```

Hozzon létre egy új hálózati adaptert, és adja hozzá a háttér-készlethez:

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$loadBalancerName = "myLoadBalancer"
$backendPoolName = "myBackendPool"
$nicname = "myNic"
$location = "eastus"
$vnetname = <your-vnet-name>

$vnet = 
Get-AzVirtualNetwork -Name $vnetname -ResourceGroupName $resourceGroup

$nic = 
New-AzNetworkInterface -ResourceGroupName $resourceGroup -Location $location -Name $nicname -LoadBalancerBackendAddressPool $backendPoolName -Subnet $vnet.Subnets[0]
```

A terheléselosztó háttér-készletével kapcsolatos információk beolvasása annak megerősítéséhez, hogy a hálózati adapter hozzá lett adva a háttér-készlethez:

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$loadBalancerName = "myLoadBalancer"
$backendPoolName = "myBackendPool"

$lb =
Get-AzLoadBalancer -ResourceGroupName $res
Get-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -BackendAddressPoolName $backendPoolName 
```

Hozzon létre egy új virtuális gépet, és csatolja a hálózati adaptert a háttér-készletbe való helyezéshez:

```azurepowershell-interactive
# Create a username and password for the virtual machine
$cred = Get-Credential

# Create a virtual machine configuration
$vmname = "myVM1"
$vmsize = "Standard_DS1_v2"
$pubname = "MicrosoftWindowsServer"
$nicname = "myNic"
$off = "WindowsServer"
$sku = "2019-Datacenter"
$resourceGroup = "myResourceGroup"
$location = "eastus"

$nic =
Get-AzNetworkInterface -Name $nicname -ResourceGroupName $resourceGroup

$vmConfig = 
New-AzVMConfig -VMName $vmname -VMSize $vmsize | Set-AzVMOperatingSystem -Windows -ComputerName $vmname -Credential $cred | Set-AzVMSourceImage -PublisherName $pubname -Offer $off -Skus $sku -Version latest | Add-AzVMNetworkInterface -Id $nic.Id
 
# Create a virtual machine using the configuration
$vm1 = New-AzVM -ResourceGroupName $resourceGroup -Zone 1 -Location $location -VM $vmConfig
```

### <a name="cli"></a>parancssori felület
A háttér-készlet létrehozása:

```azurecli-interactive
az network lb address-pool create \
--resource-group myResourceGroup \
--lb-name myLB \
--name myBackendPool 
```

Hozzon létre egy új hálózati adaptert, és adja hozzá a háttér-készlethez:

```azurecli-interactive
az network nic create \
--resource-group myResourceGroup \
--name myNic \
--vnet-name myVnet \
--subnet mySubnet \
--network-security-group myNetworkSecurityGroup \
--lb-name myLB \
--lb-address-pools myBackEndPool
```

A háttér-készlet beolvasásával erősítse meg, hogy az IP-cím helyesen lett hozzáadva:

```azurecli-interactive
az network lb address-pool show \
--resource-group myResourceGroup \
--lb-name myLb \
--name myBackendPool
```

Hozzon létre egy új virtuális gépet, és csatolja a hálózati adaptert a háttér-készletbe való helyezéshez:

```azurecli-interactive
az vm create \
--resource-group myResourceGroup \
--name myVM \
--nics myNic \
--image UbuntuLTS \
--admin-username azureuser \
--generate-ssh-keys
```

### <a name="rest-api"></a>REST API
A háttér-készlet létrehozása:

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/loadBalancers/{load-balancer-name}/backendAddressPools/{backend-pool-name}?api-version=2020-05-01
```

Hozzon létre egy hálózati adaptert, és adja hozzá azt a háttérrendszer-készlethez, amelyet a hálózati adapter IP-konfigurációk tulajdonságán keresztül hozott létre:

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/networkInterfaces/{nic-name}?api-version=2020-05-01
```

JSON-kérelem törzse:
```json
{
  "properties": {
    "enableAcceleratedNetworking": true,
    "ipConfigurations": [
      {
        "name": "ipconfig1",
        "properties": {
          "subnet": {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}/subnets/{subnet-name}"
          },
          "loadBalancerBackendAddressPools": {
                                    "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/loadBalancers/{load-balancer-name}/backendAddressPools/{backend-pool-name}"
          }
        }
      }
    ]
  },
  "location": "eastus"
}
```

A terheléselosztó háttér-készletével kapcsolatos információk beolvasása annak megerősítéséhez, hogy a hálózati adapter hozzá lett adva a háttér-készlethez:

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name/providers/Microsoft.Network/loadBalancers/{load-balancer-name/backendAddressPools/{backend-pool-name}?api-version=2020-05-01
```

Hozzon létre egy virtuális gépet, és csatolja a háttér-készletre hivatkozó hálózati adaptert:

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Compute/virtualMachines/{vm-name}?api-version=2019-12-01
```

JSON-kérelem törzse:
```JSON
{
  "location": "easttus",
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_D1_v2"
    },
    "storageProfile": {
      "imageReference": {
        "sku": "2016-Datacenter",
        "publisher": "MicrosoftWindowsServer",
        "version": "latest",
        "offer": "WindowsServer"
      },
      "osDisk": {
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Standard_LRS"
        },
        "name": "myVMosdisk",
        "createOption": "FromImage"
      }
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/{nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    },
    "osProfile": {
      "adminUsername": "{your-username}",
      "computerName": "myVM",
      "adminPassword": "{your-password}"
    }
  }
}
```

### <a name="resource-manager-template"></a>Resource Manager-sablon
Ezt a gyors útmutató [Resource Manager-sablont](https://github.com/Azure/azure-quickstart-templates/tree/master/101-load-balancer-standard-create/) követve helyezzen üzembe egy terheléselosztó és virtuális gépet, és adja hozzá a virtuális gépeket a háttér-készlethez a hálózati adapteren keresztül.

## <a name="configure-backend-pool-by-ip-address-and-virtual-network"></a>Háttérbeli készlet konfigurálása IP-cím és virtuális hálózat alapján
Az előre kitöltött háttér-készletekkel rendelkező forgatókönyvekben használja az IP-címet és a virtuális hálózatot.

Az alábbi példákban látható módon az összes háttérbeli készlet kezelése közvetlenül a háttér-készlet objektumon történik.

### <a name="limitations"></a>Korlátozások
Az IP-cím által konfigurált háttér-készlet a következő korlátozásokkal rendelkezik:
  * Csak standard Load balancerekhez használható
  * 100 IP-cím korlátja a háttér-készletben
  * A háttérbeli erőforrásoknak ugyanabban a virtuális hálózatban kell lenniük, mint a terheléselosztó
  * Az IP-alapú háttér-készlettel rendelkező Load Balancer nem működhet privát hivatkozási szolgáltatásként
  * Ez a funkció jelenleg nem támogatott a Azure Portal
  * Ez a funkció jelenleg nem támogatja az ACI-tárolókat
  * A terheléselosztó által ellátott terheléselosztó vagy szolgáltatások nem helyezhetők el a terheléselosztó háttér-készletében.
  * A bejövő NAT-szabályok nem adhatók meg IP-cím szerint

### <a name="powershell"></a>PowerShell
Új háttér-készlet létrehozása:

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$loadBalancerName = "myLoadBalancer"
$backendPoolName = "myBackendPool"
$vnetName = "myVnet"
$location = "eastus"
$nicName = "myNic"

$backendPool = New-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -Name $backendPoolName  
```

Háttérbeli készlet frissítése új IP-címmel meglévő virtuális hálózatról:
 
```azurepowershell-interactive
$virtualNetwork = 
Get-AzVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroup 
 
$ip1 = New-AzLoadBalancerBackendAddressConfig -IpAddress "10.0.0.5" -Name "TestVNetRef" -VirtualNetwork $virtualNetwork  
 
$backendPool.LoadBalancerBackendAddresses.Add($ip1) 

Set-AzLoadBalancerBackendAddressPool -InputObject $backendPool
```

A terheléselosztó háttér-készletével kapcsolatos információk beolvasása annak ellenőrzéséhez, hogy a háttérbeli címek hozzá vannak-e adva a háttér-készlethez:

```azurepowershell-interactive
Get-AzLoadBalancerBackendAddressPool -ResourceGroupName $resourceGroup -LoadBalancerName $loadBalancerName -Name $backendPoolName 
```
Hozzon létre egy hálózati adaptert, és adja hozzá a háttér-készlethez. Állítsa be az IP-címet a háttérbeli címek egyikére:

```azurepowershell-interactive
$nic = 
New-AzNetworkInterface -ResourceGroupName $resourceGroup -Location $location -Name $nicName -PrivateIpAddress 10.0.0.4 -Subnet $virtualNetwork.Subnets[0]
```

Hozzon létre egy virtuális gépet, és csatlakoztassa a hálózati adaptert egy IP-címmel a háttér-készletben:
```azurepowershell-interactive
# Create a username and password for the virtual machine
$cred = Get-Credential

# Create a virtual machine configuration
$vmname = "myVM1"
$vmsize = "Standard_DS1_v2"
$pubname = "MicrosoftWindowsServer"
$nicname = "myNic"
$off = "WindowsServer"
$sku = "2019-Datacenter"
$resourceGroup = "myResourceGroup"
$location = "eastus"

$nic =
Get-AzNetworkInterface -Name $nicname -ResourceGroupName $resourceGroup

$vmConfig = 
New-AzVMConfig -VMName $vmname -VMSize $vmsize | Set-AzVMOperatingSystem -Windows -ComputerName $vmname -Credential $cred | Set-AzVMSourceImage -PublisherName $pubname -Offer $off -Skus $sku -Version latest | Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine using the configuration
$vm1 = New-AzVM -ResourceGroupName $resourceGroup -Zone 1 -Location $location -VM $vmConfig
```

### <a name="cli"></a>parancssori felület
A CLI használatával a háttér-készletet parancssori paraméterekkel vagy JSON-konfigurációs fájlon keresztül töltheti fel. 

Hozza létre és töltse fel a háttér-készletet a parancssori paraméterek használatával:

```azurecli-interactive
az network lb address-pool create \
--resource-group myResourceGroup \
--lb-name myLB \
--name myBackendPool \
--vnet {VNET resource ID} \
--backend-address name=addr1 ip-address=10.0.0.4 \
--backend-address name=addr2 ip-address=10.0.0.5
```

A háttér-készlet létrehozása és feltöltése JSON-konfigurációs fájl használatával:

```azurecli-interactive
az network lb address-pool create \
--resource-group myResourceGroup \
--lb-name myLB \
--name myBackendPool \
--vnet {VNET resource ID} \
--backend-address-config-file @config_file.json
```

JSON-konfigurációs fájl:
```JSON
        [
          {
            "name": "address1",
            "virtualNetwork": "/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}",
            "ipAddress": "10.0.0.4"
          },
          {
            "name": "address2",
            "virtualNetwork": "/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}",
            "ipAddress": "10.0.0.5"
          }
        ]
```

A terheléselosztó háttér-készletével kapcsolatos információk beolvasása annak ellenőrzéséhez, hogy a háttérbeli címek hozzá vannak-e adva a háttér-készlethez:

```azurecli-interactive
az network lb address-pool show \
--resource-group myResourceGroup \
--lb-name MyLb \
--name MyBackendPool
```

Hozzon létre egy hálózati adaptert, és adja hozzá a háttér-készlethez. Állítsa be az IP-címet a háttérbeli címek egyikére:

```azurecli-interactive
az network nic create \
  --resource-group myResourceGroup \
  --name myNic \
  --vnet-name myVnet \
  --subnet mySubnet \
  --network-security-group myNetworkSecurityGroup \
  --lb-name myLB \
  --private-ip-address 10.0.0.4
```

Hozzon létre egy virtuális gépet, és csatlakoztassa a hálózati adaptert egy IP-címmel a háttér-készletben:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --nics myNic \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

### <a name="rest-api"></a>REST API

Hozza létre a háttér-készletet, és adja meg a háttérbeli címeket egy PUT háttérbeli készletre vonatkozó kérelem használatával. Konfigurálja a háttérbeli címeket a PUT kérelem JSON-törzsében:

* Név
* IP-cím
* Virtuális hálózat azonosítója 

```
PUT https://management.azure.com/subscriptions/subid/resourceGroups/testrg/providers/Microsoft.Network/loadBalancers/lb/backendAddressPools/backend?api-version=2020-05-01
```

JSON-kérelem törzse:
```JSON
{
  "properties": {
    "loadBalancerBackendAddresses": [
      {
        "name": "address1",
        "properties": {
          "virtualNetwork": {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}"
          },
          "ipAddress": "10.0.0.4"
        }
      },
      {
        "name": "address2",
        "properties": {
          "virtualNetwork": {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}"
          },
          "ipAddress": "10.0.0.5"
        }
      }
    ]
  }
}
```

A terheléselosztó háttér-készletével kapcsolatos információk beolvasása annak ellenőrzéséhez, hogy a háttérbeli címek hozzá vannak-e adva a háttér-készlethez:
```
GET https://management.azure.com/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/loadBalancers/{load-balancer-name}/backendAddressPools/{backend-pool-name}?api-version=2020-05-01
```

Hozzon létre egy hálózati adaptert, és adja hozzá a háttér-készlethez. Állítsa be az IP-címet a háttérbeli címek egyikére:
```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/networkInterfaces/{nic-name}?api-version=2020-05-01
```

JSON-kérelem törzse:
```JSON
{
  "properties": {
    "enableAcceleratedNetworking": true,
    "ipConfigurations": [
      {
        "name": "ipconfig1",
        "properties": {
          "privateIPAddress": "10.0.0.4",
          "subnet": {
            "id": "/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/virtualNetworks/{vnet-name}/subnets/{subnet-name}"
          }
        }
      }
    ]
  },
  "location": "eastus"
}
```

Hozzon létre egy virtuális gépet, és csatlakoztassa a hálózati adaptert egy IP-címmel a háttér-készletben:

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Compute/virtualMachines/{vm-name}?api-version=2019-12-01
```

JSON-kérelem törzse:
```JSON
{
  "location": "eastus",
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_D1_v2"
    },
    "storageProfile": {
      "imageReference": {
        "sku": "2016-Datacenter",
        "publisher": "MicrosoftWindowsServer",
        "version": "latest",
        "offer": "WindowsServer"
      },
      "osDisk": {
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Standard_LRS"
        },
        "name": "myVMosdisk",
        "createOption": "FromImage"
      }
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/{nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    },
    "osProfile": {
      "adminUsername": "{your-username}",
      "computerName": "myVM",
      "adminPassword": "{your-password}"
    }
  }
}
```
  
## <a name="next-steps"></a>Következő lépések
Ebből a cikkből megtudhatta, hogyan Azure Load Balancer végezheti el a háttérrendszer-készlet felügyeletét, és hogyan konfigurálhatja a háttér-készleteket IP-cím és virtuális hálózat alapján.

További információ a [Azure Load Balancerról](load-balancer-overview.md).
