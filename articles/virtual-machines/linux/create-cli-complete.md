---
title: Linux-környezet létrehozása az Azure CLI-vel
description: Hozzon létre egy tárolót, egy Linux virtuális gépet, egy virtuális hálózatot és egy alhálózatot, egy terheléselosztó, egy hálózati adapter, egy nyilvános IP-címet és egy hálózati biztonsági csoportot, amely az Azure CLI használatával az alapoktól kezdve.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.date: 12/14/2017
ms.author: cynthn
ms.openlocfilehash: e8c21df3b783241a3a4bfdbe28b28b2bceb89b4d
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102509115"
---
# <a name="create-a-complete-linux-virtual-machine-with-the-azure-cli"></a>Teljes linuxos virtuális gép létrehozása az Azure CLI-vel
Virtuális gép (VM) gyors létrehozásához az Azure-ban használhat egyetlen Azure CLI-parancsot, amely az alapértelmezett értékeket használja a szükséges támogatási erőforrások létrehozásához. A rendszer automatikusan létrehozza az erőforrásokat, például a virtuális hálózatot, a nyilvános IP-címet és a hálózati biztonsági csoport szabályait. A környezet éles környezetben való használatának szabályozása érdekében érdemes lehet előre létrehozni ezeket az erőforrásokat, majd hozzáadni a virtuális gépeket. Ez a cikk végigvezeti a virtuális gépek és az egyes támogatási erőforrások egyenkénti létrehozásán.

Győződjön meg arról, hogy telepítette a legújabb [Azure CLI](/cli/azure/install-az-cli2) -t, és bejelentkezett egy Azure-fiókba az az [login](/cli/azure/reference-index)paranccsal.

Az alábbi példákban cserélje le a példában szereplő paraméterek nevét a saját értékeire. A paraméterek nevei például a következők: *myResourceGroup*, *myVnet* és *myVM*.

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása
Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Létre kell hozni egy erőforráscsoportot a virtuális gép és a virtuális hálózati erőforrások támogatása előtt. Hozza létre az erőforráscsoportot az [az Group Create](/cli/azure/group)paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *eastus* helyen:

```azurecli
az group create --name myResourceGroup --location eastus
```

Alapértelmezés szerint az Azure CLI-parancsok kimenete JSON (JavaScript Object Notation). Az alapértelmezett kimenet listára vagy táblára való módosításához használja például az [az configure--output](/cli/azure/reference-index)értéket. Bármely parancshoz hozzáadhatja azt is, ha `--output` a kimeneti formátum egy alkalommal változik. A következő példa a parancs JSON-kimenetét mutatja `az group create` :

```json                       
{
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup",
  "location": "eastus",
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="create-a-virtual-network-and-subnet"></a>Virtuális hálózat és alhálózat létrehozása
Ezután hozzon létre egy virtuális hálózatot az Azure-ban, és egy alhálózatot, amelybe létrehozhatja a virtuális gépeket. Az [az Network vnet Create](/cli/azure/network/vnet) paranccsal hozzon létre egy *myVnet* nevű virtuális hálózatot a *192.168.0.0/16-* címe előtaggal. A *mySubnet* nevű alhálózatot is hozzáadhatja a *192.168.1.0/24* címének előtagjaként:

```azurecli
az network vnet create \
    --resource-group myResourceGroup \
    --name myVnet \
    --address-prefix 192.168.0.0/16 \
    --subnet-name mySubnet \
    --subnet-prefix 192.168.1.0/24
```

A kimenet azt mutatja, hogy az alhálózat logikailag jön létre a virtuális hálózaton belül:

```json
{
  "addressSpace": {
    "addressPrefixes": [
      "192.168.0.0/16"
    ]
  },
  "dhcpOptions": {
    "dnsServers": []
  },
  "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet",
  "location": "eastus",
  "name": "myVnet",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "ed62fd03-e9de-430b-84df-8a3b87cacdbb",
  "subnets": [
    {
      "addressPrefix": "192.168.1.0/24",
      "etag": "W/\"e95496fc-f417-426e-a4d8-c9e4d27fc2ee\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
      "ipConfigurations": null,
      "name": "mySubnet",
      "networkSecurityGroup": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "resourceNavigationLinks": null,
      "routeTable": null
    }
  ],
  "tags": {},
  "type": "Microsoft.Network/virtualNetworks",
  "virtualNetworkPeerings": null
}
```


## <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet
Most hozzon létre egy nyilvános IP-címet az [az Network Public-IP Create](/cli/azure/network/public-ip)paranccsal. Ez a nyilvános IP-cím lehetővé teszi, hogy az internetről csatlakozhasson a virtuális gépekhez. Mivel az alapértelmezett címek dinamikusak, hozzon létre egy nevesített DNS-bejegyzést a `--domain-name-label` paraméterrel. A következő példa egy *myPublicIP* nevű nyilvános IP-címet hoz létre a *mypublicdns* DNS-nevével. Mivel a DNS-névnek egyedinek kell lennie, meg kell adnia a saját egyedi DNS-nevét:

```azurecli
az network public-ip create \
    --resource-group myResourceGroup \
    --name myPublicIP \
    --dns-name mypublicdns
```

Kimenet:

```json
{
  "publicIp": {
    "dnsSettings": {
      "domainNameLabel": "mypublicdns",
      "fqdn": "mypublicdns.eastus.cloudapp.azure.com",
      "reverseFqdn": null
    },
    "etag": "W/\"2632aa72-3d2d-4529-b38e-b622b4202925\"",
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": null,
    "ipConfiguration": null,
    "location": "eastus",
    "name": "myPublicIP",
    "provisioningState": "Succeeded",
    "publicIpAddressVersion": "IPv4",
    "publicIpAllocationMethod": "Dynamic",
    "resourceGroup": "myResourceGroup",
    "resourceGuid": "4c65de38-71f5-4684-be10-75e605b3e41f",
    "tags": null,
    "type": "Microsoft.Network/publicIPAddresses"
  }
}
```


## <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása
A virtuális gépek bejövő és kimenő forgalmának szabályozásához egy hálózati biztonsági csoportot kell alkalmaznia egy virtuális hálózati ADAPTERre vagy alhálózatra. Az alábbi példa az [az Network NSG Create](/cli/azure/network/nsg) paranccsal hoz létre egy *myNetworkSecurityGroup* nevű hálózati biztonsági csoportot:

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --name myNetworkSecurityGroup
```

Olyan szabályokat határozhat meg, amelyek engedélyezik vagy megtagadják a megadott forgalmat. Ha engedélyezni szeretné a bejövő kapcsolatokat a 22-es porton (az SSH-hozzáférés engedélyezéséhez), hozzon létre egy bejövő szabályt az [az Network NSG Rule Create](/cli/azure/network/nsg/rule)paranccsal. A következő példa egy *myNetworkSecurityGroupRuleSSH* nevű szabályt hoz létre:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleSSH \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 22 \
    --access allow
```

Ha engedélyezni szeretné a bejövő kapcsolatokat az 80-as porton (webes forgalomhoz), adjon hozzá egy másik hálózati biztonsági csoportra vonatkozó szabályt. A következő példa egy *myNetworkSecurityGroupRuleHTTP* nevű szabályt hoz létre:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRuleWeb \
    --protocol tcp \
    --priority 1001 \
    --destination-port-range 80 \
    --access allow
```

Vizsgálja meg a hálózati biztonsági csoportot és a szabályokat az [az Network NSG show](/cli/azure/network/nsg)paranccsal:

```azurecli
az network nsg show --resource-group myResourceGroup --name myNetworkSecurityGroup
```

Kimenet:

```json
{
  "defaultSecurityRules": [
    {
      "access": "Allow",
      "description": "Allow inbound traffic from all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetInBound",
      "name": "AllowVnetInBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow inbound traffic from azure load balancer",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowAzureLoadBalancerInBou",
      "name": "AllowAzureLoadBalancerInBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "AzureLoadBalancer",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all inbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Inbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllInBound",
      "name": "DenyAllInBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to all VMs in VNET",
      "destinationAddressPrefix": "VirtualNetwork",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowVnetOutBound",
      "name": "AllowVnetOutBound",
      "priority": 65000,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "VirtualNetwork",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": "Allow outbound traffic from all VMs to Internet",
      "destinationAddressPrefix": "Internet",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/AllowInternetOutBound",
      "name": "AllowInternetOutBound",
      "priority": 65001,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Deny",
      "description": "Deny all outbound traffic",
      "destinationAddressPrefix": "*",
      "destinationPortRange": "*",
      "direction": "Outbound",
      "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/defaultSecurityRules/DenyAllOutBound",
      "name": "DenyAllOutBound",
      "priority": 65500,
      "protocol": "*",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "etag": "W/\"3371b313-ea9f-4687-a336-a8ebdfd80523\"",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
  "location": "eastus",
  "name": "myNetworkSecurityGroup",
  "networkInterfaces": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "resourceGuid": "47a9964e-23a3-438a-a726-8d60ebbb1c3c",
  "securityRules": [
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "22",
      "direction": "Inbound",
      "etag": "W/\"9e344b60-0daa-40a6-84f9-0ebbe4a4b640\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleSSH",
      "name": "myNetworkSecurityGroupRuleSSH",
      "priority": 1000,
      "protocol": "Tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    },
    {
      "access": "Allow",
      "description": null,
      "destinationAddressPrefix": "*",
      "destinationPortRange": "80",
      "direction": "Inbound",
      "etag": "W/\"9e344b60-0daa-40a6-84f9-0ebbe4a4b640\"",
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/myNetworkSecurityGroupRuleWeb",
      "name": "myNetworkSecurityGroupRuleWeb",
      "priority": 1001,
      "protocol": "Tcp",
      "provisioningState": "Succeeded",
      "resourceGroup": "myResourceGroup",
      "sourceAddressPrefix": "*",
      "sourcePortRange": "*"
    }
  ],
  "subnets": null,
  "tags": null,
  "type": "Microsoft.Network/networkSecurityGroups"
}
```

## <a name="create-a-virtual-nic"></a>Virtuális hálózati adapter létrehozása
A virtuális hálózati adapterek (NIC-EK) programozott módon elérhetők, mert szabályokat alkalmazhat a használatára. A virtuálisgép- [mérettől](../sizes.md)függően több virtuális hálózati adaptert is csatolhat egy virtuális géphez. A következő az [Network NIC Create](/cli/azure/network/nic) paranccsal hozzon létre egy *MYNIC* nevű hálózati adaptert, és társítsa azt a hálózati biztonsági csoporttal. A nyilvános IP- *myPublicIP* a virtuális hálózati adapterhez is társítva van.

```azurecli
az network nic create \
    --resource-group myResourceGroup \
    --name myNic \
    --vnet-name myVnet \
    --subnet mySubnet \
    --public-ip-address myPublicIP \
    --network-security-group myNetworkSecurityGroup
```

Kimenet:

```json
{
  "NewNIC": {
    "dnsSettings": {
      "appliedDnsServers": [],
      "dnsServers": [],
      "internalDnsNameLabel": null,
      "internalDomainNameSuffix": "brqlt10lvoxedgkeuomc4pm5tb.bx.internal.cloudapp.net",
      "internalFqdn": null
    },
    "enableAcceleratedNetworking": false,
    "enableIpForwarding": false,
    "etag": "W/\"04b5ab44-d8f4-422a-9541-e5ae7de8466d\"",
    "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
    "ipConfigurations": [
      {
        "applicationGatewayBackendAddressPools": null,
        "etag": "W/\"04b5ab44-d8f4-422a-9541-e5ae7de8466d\"",
        "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic/ipConfigurations/ipconfig1",
        "loadBalancerBackendAddressPools": null,
        "loadBalancerInboundNatRules": null,
        "name": "ipconfig1",
        "primary": true,
        "privateIpAddress": "192.168.1.4",
        "privateIpAddressVersion": "IPv4",
        "privateIpAllocationMethod": "Dynamic",
        "provisioningState": "Succeeded",
        "publicIpAddress": {
          "dnsSettings": null,
          "etag": null,
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP",
          "idleTimeoutInMinutes": null,
          "ipAddress": null,
          "ipConfiguration": null,
          "location": null,
          "name": null,
          "provisioningState": null,
          "publicIpAddressVersion": null,
          "publicIpAllocationMethod": null,
          "resourceGroup": "myResourceGroup",
          "resourceGuid": null,
          "tags": null,
          "type": null
        },
        "resourceGroup": "myResourceGroup",
        "subnet": {
          "addressPrefix": null,
          "etag": null,
          "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet",
          "ipConfigurations": null,
          "name": null,
          "networkSecurityGroup": null,
          "provisioningState": null,
          "resourceGroup": "myResourceGroup",
          "resourceNavigationLinks": null,
          "routeTable": null
        }
      }
    ],
    "location": "eastus",
    "macAddress": null,
    "name": "myNic",
    "networkSecurityGroup": {
      "defaultSecurityRules": null,
      "etag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup",
      "location": null,
      "name": null,
      "networkInterfaces": null,
      "provisioningState": null,
      "resourceGroup": "myResourceGroup",
      "resourceGuid": null,
      "securityRules": null,
      "subnets": null,
      "tags": null,
      "type": null
    },
    "primary": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "myResourceGroup",
    "resourceGuid": "b3dbaa0e-2cf2-43be-a814-5cc49fea3304",
    "tags": null,
    "type": "Microsoft.Network/networkInterfaces",
    "virtualMachine": null
  }
}
```


## <a name="create-an-availability-set"></a>Rendelkezésre állási csoport létrehozása
A rendelkezésre állási csoportok segítenek a virtuális gépeknek a tartalék tartományokban és a frissítési tartományokban való terjesztésében. Annak ellenére, hogy jelenleg csak egy virtuális gépet hoz létre, ajánlott a rendelkezésre állási csoportok használata, hogy könnyebben kibővíthető legyen a jövőben. 

A tartalék tartományok definiálják azokat a virtuális gépeket, amelyek közös áramforrással és hálózati kapcsolóval rendelkeznek. Alapértelmezés szerint a rendelkezésre állási csoporton belül konfigurált virtuális gépek legfeljebb három tartalék tartományba vannak elválasztva. Ezen tartalék tartományok egyikének hardveres hibája nem befolyásolja az alkalmazást futtató összes virtuális gépet.

A frissítési tartományok olyan virtuális gépek és mögöttes fizikai hardverek csoportjait jelölik, amelyek egy időben újraindíthatók. A tervezett karbantartás során előfordulhat, hogy a frissítési tartományok újraindítása nem szekvenciális, de egyszerre csak egy frissítési tartományt indít el a rendszer.

Az Azure automatikusan elosztja a virtuális gépeket a hibák és a frissítési tartományok között, amikor rendelkezésre állási csoportba helyezi őket. További információ: [a virtuális gépek rendelkezésre állásának kezelése](../availability.md).

Hozzon létre egy rendelkezésre állási készletet a virtuális géphez az [az VM rendelkezésre állása-set Create](/cli/azure/vm/availability-set)paranccsal. Az alábbi példa egy *myAvailabilitySet* nevű rendelkezésre állási csoportot hoz létre:

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet
```

A kimeneti megjegyzések tartalék tartományai és frissítési tartományai:

```json
{
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/availabilitySets/myAvailabilitySet",
  "location": "eastus",
  "managed": null,
  "name": "myAvailabilitySet",
  "platformFaultDomainCount": 2,
  "platformUpdateDomainCount": 5,
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": null,
    "managed": true,
    "tier": null
  },
  "statuses": null,
  "tags": {},
  "type": "Microsoft.Compute/availabilitySets",
  "virtualMachines": []
}
```


## <a name="create-a-vm"></a>Virtuális gép létrehozása
Létrehozta a hálózati erőforrásokat az internetről elérhető virtuális gépek támogatásához. Most hozzon létre egy virtuális gépet, és gondoskodjon róla, hogy SSH-kulccsal lássa el. Ebben a példában hozzunk létre egy Ubuntu virtuális gépet a legfrissebb LTS alapján. További rendszerképeket az [az VM Image List](/cli/azure/vm/image)paranccsal talál, az [Azure VM-rendszerképek megkeresése](cli-ps-findimage.md)című témakörben leírtak szerint.

A hitelesítéshez használandó SSH-kulcs megadása. Ha nem rendelkezik nyilvános SSH-kulcspárt, [létrehozhatja őket](mac-create-ssh-keys.md) , vagy használhatja a `--generate-ssh-keys` paramétert a létrehozásához. Ha már van kulcspár, a paraméter a alkalmazásban meglévő kulcsokat használ `~/.ssh` .

Hozza létre a virtuális gépet úgy, hogy az az [VM Create](/cli/azure/vm) paranccsal együtt létrehozza az összes erőforrást és információt. A következő példa egy *myVM* nevű virtuális gépet hoz létre:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location eastus \
    --availability-set myAvailabilitySet \
    --nics myNic \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

SSH-t a virtuális géphez a nyilvános IP-cím létrehozásakor megadott DNS-bejegyzéssel. Ez `fqdn` látható a kimenetben a virtuális gép létrehozásakor:

```json
{
  "fqdns": "mypublicdns.eastus.cloudapp.azure.com",
  "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-13-71-C8",
  "powerState": "VM running",
  "privateIpAddress": "192.168.1.5",
  "publicIpAddress": "13.90.94.252",
  "resourceGroup": "myResourceGroup"
}
```

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Kimenet:

```bash
The authenticity of host 'mypublicdns.eastus.cloudapp.azure.com (13.90.94.252)' can't be established.
ECDSA key fingerprint is SHA256:SylINP80Um6XRTvWiFaNz+H+1jcrKB1IiNgCDDJRj6A.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'mypublicdns.eastus.cloudapp.azure.com,13.90.94.252' (ECDSA) to the list of known hosts.
Welcome to Ubuntu 16.04.3 LTS (GNU/Linux 4.11.0-1016-azure x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  Get cloud support with Ubuntu Advantage Cloud Guest:
    https://www.ubuntu.com/business/services/cloud

0 packages can be updated.
0 updates are security updates.


The programs included with the Ubuntu system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
applicable law.

To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

azureuser@myVM:~$
```

Telepítheti az NGINX-t, és megtekintheti a virtuális gép felé irányuló forgalmat. Telepítse az NGINX-et a következőképpen:

```bash
sudo apt-get install -y nginx
```

Az alapértelmezett NGINX-webhely működés közbeni megtekintéséhez nyissa meg a webböngészőt, és adja meg a teljes tartománynevet:

![Alapértelmezett NGINX-webhely a virtuális gépen](media/create-cli-complete/nginx.png)

## <a name="export-as-a-template"></a>Exportálás sablonként
Mi a teendő, ha már szeretne egy további fejlesztési környezetet létrehozni ugyanazzal a paraméterekkel vagy egy olyan éles környezettel, amely megfelel a szolgáltatásnak? A Resource Manager JSON-sablonokat használ, amelyek meghatározzák a környezet összes paraméterét. A JSON-sablonra hivatkozó teljes környezeteket hozhat létre. A JSON- [sablonokat manuálisan](../../azure-resource-manager/templates/template-syntax.md?toc=/azure/virtual-machines/linux/toc.json) is létrehozhatja, vagy exportálhat egy meglévő környezetet a JSON-sablon létrehozásához. Az alábbi módon exportálhatja az erőforráscsoportot az az [Group export](/cli/azure/group) paranccsal:

```azurecli
az group export --name myResourceGroup > myResourceGroup.json
```

Ez a parancs az `myResourceGroup.json` aktuális munkakönyvtárban hozza létre a fájlt. Amikor létrehoz egy környezetet a sablonból, a rendszer az összes erőforrás nevét kéri. Ezeket a neveket feltöltheti a sablon fájljába úgy, hogy hozzáadja a `--include-parameter-default-value` paramétert a `az group export` parancshoz. Szerkessze a JSON-sablont az erőforrásnevek megadásához, vagy [hozzon létre egy parameters.js](../../azure-resource-manager/templates/template-syntax.md?toc=/azure/virtual-machines/linux/toc.json) az erőforrás nevét megadó fájlon.

Ha létre szeretne hozni egy környezetet a sablonból, használja az [az üzembe helyezési csoport létrehozása](/cli/azure/deployment/group) lehetőséget a következő módon:

```azurecli
az deployment group create \
    --resource-group myNewResourceGroup \
    --template-file myResourceGroup.json
```

Előfordulhat, hogy [további információra van szüksége a sablonokból való üzembe helyezésről](../../azure-resource-manager/templates/deploy-cli.md?toc=/azure/virtual-machines/linux/toc.json). További információ a környezetek növekményes frissítéséről, a Parameters fájl használatáról és a sablonok egyetlen tárolási helyről való eléréséről.

## <a name="next-steps"></a>Következő lépések
Most már készen áll a több hálózati összetevő és virtuális gép használatának megkezdésére. Ezzel a minta környezettel kiépítheti az alkalmazást az itt bemutatott alapvető összetevők használatával.