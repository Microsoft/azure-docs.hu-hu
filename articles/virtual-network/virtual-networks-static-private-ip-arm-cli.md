---
title: Virtuális gépek magánhálózati IP-címeinek konfigurálása – Azure CLI
description: Ismerje meg, hogyan konfigurálhatja a virtuális gépek magánhálózati IP-címeit az Azure parancssori felület (CLI) használatával.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
editor: ''
tags: azure-resource-manager
ms.assetid: 40b03a1a-ea00-454c-b716-7574cea49ac0
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: allensu
ms.openlocfilehash: c34ab73422d8dd41feb9da542ed63fdba060fe3f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "84708161"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-cli"></a>Magánhálózati IP-címek konfigurálása virtuális géphez az Azure CLI használatával


[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

> [!NOTE]
> A következő minta Azure CLI-parancsok egy meglévő egyszerű környezetet várnak. Ha a jelen dokumentumban megjelenő parancsokat szeretné futtatni, először [hozza létre a vnet létrehozása](quick-create-cli.md)című témakörben leírt tesztkörnyezetben.

## <a name="specify-a-static-private-ip-address-when-creating-a-vm"></a>Statikus magánhálózati IP-cím megadása virtuális gép létrehozásakor

A következő lépésekkel hozhat létre egy *DNS01* nevű virtuális gépet egy *TestVNet* nevű VNet előtér- *alhálózatában* a *192.168.1.101* statikus magánhálózati IP-címével:

1. Ha még nem tette meg, telepítse és konfigurálja a legújabb [Azure CLI](/cli/azure/install-azure-cli) -t, és jelentkezzen be egy Azure-fiókba az [az login](/cli/azure/reference-index)használatával.

2. Futtassa az az [Network NIC Create](/cli/azure/network/nic) parancsot egy statikus magánhálózati IP-címmel rendelkező hálózati adapter létrehozásához. A kimenet után látható lista ismerteti a használt paramétereket. 
   
    ```azurecli
    az network nic create \
    --resource-group TestRG \
    --name TestNIC \
    --location centralus \
    --subnet FrontEnd \
    --private-ip-address 192.168.1.101 \
    --vnet-name TestVNet
    ```

    Várt kimenet:
   
    ```json
    {
        "newNIC": {
            "dnsSettings": {
            "appliedDnsServers": [],
            "dnsServers": []
            },
            "enableIPForwarding": false,
            "ipConfigurations": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/ipconfig1",
                "name": "ipconfig1",
                "properties": {
                "primary": true,
                "privateIPAddress": "192.168.1.101",
                "privateIPAllocationMethod": "Static",
                "provisioningState": "Succeeded",
                "subnet": {
                    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "resourceGroup": "TestRG"
                }
                },
                "resourceGroup": "TestRG"
            }
            ],
            "provisioningState": "Succeeded",
            "resourceGuid": "<guid>"
        }
    }
    ```
    
    Paraméterek:

    * `--private-ip-address`: Statikus magánhálózati IP-cím a hálózati adapterhez.
    * `--vnet-name`: Annak a VNet a neve, amelyben létre kívánja hozni a hálózati adaptert.
    * `--subnet`: Annak az alhálózatnak a neve, amelyben létre kívánja hozni a hálózati adaptert.

3. Futtassa az [Azure VM Create](/cli/azure/vm/nic) parancsot a virtuális gép létrehozásához a korábban létrehozott nyilvános IP-cím és NIC használatával. A kimenet után látható lista ismerteti a használt paramétereket.
   
    ```azurecli
    az vm create \
    --resource-group TestRG \
    --name DNS01 \
    --location centralus \
    --image Debian \
    --admin-username adminuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics TestNIC
    ```

    Várt kimenet:
   
    ```json
    {
        "fqdns": "",
        "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01",
        "location": "centralus",
        "macAddress": "00-0D-3A-92-C1-66",
        "powerState": "VM running",
        "privateIpAddress": "192.168.1.101",
        "publicIpAddress": "",
        "resourceGroup": "TestRG"
    }
    ```
   
   Az alapszintű az [VM Create](/cli/azure/vm) Parameters paramétertől eltérő paraméterek.

   * `--nics`: Annak a hálózati adapternek a neve, amelyhez a virtuális gép csatolva van.
   
Javasoljuk, hogy a virtuális gép operációs rendszerén belül ne rendeljen statikusan az Azure virtuális géphez rendelt magánhálózati IP-címet, ha szükséges, például ha [több IP-címet rendel egy Windows rendszerű virtuális géphez](virtual-network-multiple-ip-addresses-cli.md). Ha a magánhálózati IP-címet manuálisan állítja be az operációs rendszeren belül, győződjön meg arról, hogy a cím megegyezik az Azure [hálózati adapterhez](virtual-network-network-interface-addresses.md#change-ip-address-settings)rendelt magánhálózati IP-címmel, vagy megszakadhat a kapcsolat a virtuális géppel. További információ a [magánhálózati IP-címek](virtual-network-network-interface-addresses.md#private) beállításairól.

## <a name="retrieve-static-private-ip-address-information-for-a-vm"></a>Statikus magánhálózati IP-címek adatainak beolvasása egy virtuális géphez

Futtassa az alábbi Azure CLI-parancsot a *MAGÁNHÁLÓZATI IP-foglalási-metódus* és a *magánhálózati IP-cím* értékének megfigyeléséhez:

```azurecli
az vm show -g TestRG -n DNS01 --show-details --query 'privateIps'
```

Várt kimenet:

```json
"192.168.1.101"
```

Az adott virtuális géphez tartozó hálózati adapter adott IP-címének megjelenítéséhez konkrétan kérdezze le a hálózati adaptert:

```azurecli
az network nic show \
-g testrg \
-n testnic \
--query 'ipConfigurations[0].{PrivateAddress:privateIpAddress,IPVer:privateIpAddressVersion,IpAllocMethod:p
rivateIpAllocationMethod,PublicAddress:publicIpAddress}'
```

A kimenet a következőképpen fog kinézni:

```json
{
    "IPVer": "IPv4",
    "IpAllocMethod": "Static",
    "PrivateAddress": "192.168.1.101",
    "PublicAddress": null
}
```

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>Statikus magánhálózati IP-cím eltávolítása egy virtuális gépről

Nem távolíthat el statikus magánhálózati IP-címet az Azure CLI-ben lévő hálózati adapterről Azure Resource Manager üzemelő példányokhoz. A következőket kell tennie:
- Dinamikus IP-címet használó új hálózati adapter létrehozása
- Állítsa be a virtuális gép hálózati adapterét az újonnan létrehozott hálózati adapterre. 

Ha módosítani szeretné az előző parancsokban használt virtuális gép hálózati adapterét, hajtsa végre a következő lépéseket:

1. Az **Azure hálózati NIC Create** parancs futtatásával hozzon létre egy új hálózati adaptert egy új IP-címmel rendelkező dinamikus IP-foglalás használatával. Mivel nincs megadva IP-cím, a kiosztási módszer **dinamikus**.

    ```azurecli
    az network nic create     \
    --resource-group TestRG     \
    --name TestNIC2     \
    --location centralus     \
    --subnet FrontEnd    \
    --vnet-name TestVNet
    ```        
   
    Várt kimenet:

    ```json
    {
        "newNIC": {
            "dnsSettings": {
            "appliedDnsServers": [],
            "dnsServers": []
            },
            "enableIPForwarding": false,
            "ipConfigurations": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2/ipConfigurations/ipconfig1",
                "name": "ipconfig1",
                "properties": {
                "primary": true,
                "privateIPAddress": "192.168.1.4",
                "privateIPAllocationMethod": "Dynamic",
                "provisioningState": "Succeeded",
                "subnet": {
                    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "resourceGroup": "TestRG"
                }
                },
                "resourceGroup": "TestRG"
            }
            ],
            "provisioningState": "Succeeded",
            "resourceGuid": "0808a61c-476f-4d08-98ee-0fa83671b010"
        }
    }
    ```

2. Futtassa az **Azure VM set** parancsot a virtuális gép által használt hálózati adapter módosításához.
   
    ```azurecli
   az vm nic set --resource-group TestRG --vm-name DNS01 --nics TestNIC2
    ```

    Várt kimenet:
   
    ```json
    [
        {
            "id": "/subscriptions/0e220bf6-5caa-4e9f-8383-51f16b6c109f/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC3",
            "primary": true,
            "resourceGroup": "TestRG"
        }
    ]
    ```

    > [!NOTE]
    > Ha a virtuális gép mérete elég nagy ahhoz, hogy egynél több hálózati adapterrel rendelkezzen, futtassa az **Azure hálózati NIC delete** parancsot a régi hálózati adapter törléséhez.

## <a name="next-steps"></a>Következő lépések

További információ az [IP-címek beállításainak](virtual-network-network-interface-addresses.md)kezeléséről.
