---
title: Rövid útmutató – Linux rendszerű virtuális gépek konfigurálása az Azure-ban az Ansible-lel |} A Microsoft Docs
description: Ebből a gyorsútmutatóból megtudhatja, hogyan Linux rendszerű virtuális gép létrehozása az Azure-ban az Ansible-lel
keywords: ansible, azure, devops, virtuális gép
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: 4bf7d43b682a2a42d9909f9cd33aa9542a1a9330
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2019
ms.locfileid: "63760514"
---
# <a name="quickstart-configure-linux-virtual-machines-in-azure-using-ansible"></a>Gyors útmutató: Az Azure-ban az Ansible konfigurálása a Linux rendszerű virtuális gépek

Az Ansible deklaratív nyelv használatával lehetővé válik az Azure-beli erőforrások létrehozásának, konfigurálásának és üzembe helyezésének automatizálása az Ansible *forgatókönyveivel*. Ez a cikk bemutatja egy minta Ansible forgatókönyv Linux rendszerű virtuális gépek konfigurálása. A [teljes Ansible-forgatókönyv](#complete-sample-ansible-playbook) a cikk végén található.

## <a name="prerequisites"></a>Előfeltételek

- [!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)]

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az Ansible használatához szükség van egy erőforráscsoportra, amelyben az erőforrások üzembe lesznek helyezve. Az Ansible-mintaforgatókönyv következő szakasza egy erőforráscsoportot hoz létre `eastus` néven az `myResourceGroup` helyen:

```yaml
- name: Create resource group
  azure_rm_resourcegroup:
    name: myResourceGroup
    location: eastus
```

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Azure virtuális gép létrehozásakor létre kell hoznia egy [virtuális hálózatot](/azure/virtual-network/virtual-networks-overview), vagy egy meglévőt kell használnia. Arról is döntenie kell, hogy a virtuális gépek milyen módon legyenek elérhetők a virtuális hálózaton. Az Ansible-mintaforgatókönyv következő szakasza egy virtuális hálózatot hoz létre `10.0.0.0/16` néven a `myVnet` címtérben:

```yaml
- name: Create virtual network
  azure_rm_virtualnetwork:
    resource_group: myResourceGroup
    name: myVnet
    address_prefixes: "10.0.0.0/16"
```

A virtuális hálózatban üzembe helyezett Azure-erőforrások a virtuális hálózat egy [alhálózatán](/azure/virtual-network/virtual-network-manage-subnet) vannak üzembe helyezve. 

Az Ansible-mintaforgatókönyv következő szakasza egy alhálózatot hoz létre `myVnet` néven a `mySubnet` virtuális hálózaton:

```yaml
- name: Add subnet
  azure_rm_subnet:
    resource_group: myResourceGroup
    name: mySubnet
    address_prefix: "10.0.1.0/24"
    virtual_network: myVnet
```

## <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet





A [nyilvános IP-címek](/azure/virtual-network/virtual-network-ip-addresses-overview-arm) lehetővé teszik az internetes erőforrások bejövő kommunikációját az Azure-erőforrásokkal. Nyilvános IP-címek az Azure-erőforrások kimenő kommunikációját az ezeknek a nyilvánosan elérhető Azure-szolgáltatásokat is engedélyezheti. Mindkét esetben hozzáfér az erőforráshoz hozzárendelt IP-cím. A cím az erőforráshoz van kijelölve, amíg Ön hozzárendeltem. Nyilvános IP-cím nincs hozzárendelve egy erőforráshoz, amennyiben az erőforrás is továbbra is képes kimenő kommunikációra az interneten. A kapcsolat az Azure-ban elérhető IP-cím dinamikus hozzárendelése. A dinamikusan kiosztott címet az erőforrás számára nincs kijelölve.

Az Ansible-mintaforgatókönyv következő szakasza egy nyilvános IP-címet hoz létre `myPublicIP` néven:

```yaml
- name: Create public IP address
  azure_rm_publicipaddress:
    resource_group: myResourceGroup
    allocation_method: Static
    name: myPublicIP
```

## <a name="create-a-network-security-group"></a>Hálózati biztonsági csoport létrehozása

[Hálózati biztonsági csoportok](/azure/virtual-network/security-overview) virtuális hálózatban lévő Azure-erőforrások közötti hálózati forgalom szűrése. Biztonsági szabályok vannak meghatározva, amely az Azure-erőforrások bejövő és kimenő forgalom szabályozására. Azure-erőforrások és a hálózati biztonsági csoportokkal kapcsolatos további információkért lásd: [virtuális hálózati integráció az Azure-szolgáltatásokhoz](/azure/virtual-network/virtual-network-for-azure-services)

A következő forgatókönyv nevű hálózati biztonsági csoportot hoz létre `myNetworkSecurityGroup`. A hálózati biztonsági csoport tartalmaz egy szabályt, amely lehetővé teszi, hogy SSH-forgalmat a 22-es TCP-portot.

```yaml
- name: Create Network Security Group that allows SSH
  azure_rm_securitygroup:
    resource_group: myResourceGroup
    name: myNetworkSecurityGroup
    rules:
      - name: SSH
        protocol: Tcp
        destination_port_range: 22
        access: Allow
        priority: 1001
        direction: Inbound
```

## <a name="create-a-virtual-network-interface-card"></a>Virtuális hálózati kártya létrehozása

A virtuális hálózati kártya a virtuális gépet egy adott virtuális hálózathoz, nyilvános IP-címhez és hálózati biztonsági csoporthoz csatlakoztatja. 

A következő szakasz egy minta Ansible forgatókönyv szakaszban létrehoz egy virtuális hálózati kártya nevű `myNIC` csatlakozik a létrehozott virtuális hálózati erőforrások:

```yaml
- name: Create virtual network inteface card
  azure_rm_networkinterface:
    resource_group: myResourceGroup
    name: myNIC
    virtual_network: myVnet
    subnet: mySubnet
    public_ip_name: myPublicIP
    security_group: myNetworkSecurityGroup
```

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Az utolsó lépés egy virtuális gép létrehozása, amely a cikk előző szakaszaiban létrehozott erőforrásokat használja. 

Az Ansible-mintaforgatókönyv a jelen szakaszban bemutatott szakasza egy virtuális gépet hoz létre `myVM` néven, és csatlakoztatja a `myNIC` nevű virtuális hálózati kártyát. Cserélje le a &lt;your-key-data> helyőrzőt a saját teljes nyilvános kulcsának adataira.

```yaml
- name: Create VM
  azure_rm_virtualmachine:
    resource_group: myResourceGroup
    name: myVM
    vm_size: Standard_DS1_v2
    admin_username: azureuser
    ssh_password_enabled: false
    ssh_public_keys:
      - path: /home/azureuser/.ssh/authorized_keys
        key_data: <your-key-data>
    network_interfaces: myNIC
    image:
      offer: CentOS
      publisher: OpenLogic
      sku: '7.5'
      version: latest
```

## <a name="complete-sample-ansible-playbook"></a>Teljes Ansible-mintaforgatókönyv

Ez a szakasz a cikk során összeállított teljes Ansible-mintaforgatókönyvet mutatja be. 

```yaml
- name: Create Azure VM
  hosts: localhost
  connection: local
  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
      name: myResourceGroup
      location: eastus
  - name: Create virtual network
    azure_rm_virtualnetwork:
      resource_group: myResourceGroup
      name: myVnet
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: myResourceGroup
      name: mySubnet
      address_prefix: "10.0.1.0/24"
      virtual_network: myVnet
  - name: Create public IP address
    azure_rm_publicipaddress:
      resource_group: myResourceGroup
      allocation_method: Static
      name: myPublicIP
    register: output_ip_address
  - name: Dump public IP for VM which will be created
    debug:
      msg: "The public IP is {{ output_ip_address.state.ip_address }}."
  - name: Create Network Security Group that allows SSH
    azure_rm_securitygroup:
      resource_group: myResourceGroup
      name: myNetworkSecurityGroup
      rules:
        - name: SSH
          protocol: Tcp
          destination_port_range: 22
          access: Allow
          priority: 1001
          direction: Inbound
  - name: Create virtual network inteface card
    azure_rm_networkinterface:
      resource_group: myResourceGroup
      name: myNIC
      virtual_network: myVnet
      subnet: mySubnet
      public_ip_name: myPublicIP
      security_group: myNetworkSecurityGroup
  - name: Create VM
    azure_rm_virtualmachine:
      resource_group: myResourceGroup
      name: myVM
      vm_size: Standard_DS1_v2
      admin_username: azureuser
      ssh_password_enabled: false
      ssh_public_keys:
        - path: /home/azureuser/.ssh/authorized_keys
          key_data: <your-key-data>
      network_interfaces: myNIC
      image:
        offer: CentOS
        publisher: OpenLogic
        sku: '7.5'
        version: latest
```

## <a name="run-the-sample-ansible-playbook"></a>Az Ansible-mintaforgatókönyv futtatása

Ez a szakasz végigvezeti a cikkben bemutatott Ansible-mintaforgatókönyv futtatásának lépésein.

1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Nyissa meg a [Cloud Shellt](/azure/cloud-shell/overview).

1. Hozzon létre egy fájlt `azure_create_complete_vm.yml` néven (amely a forgatókönyvet tartalmazza majd), és nyissa meg a VI-szerkesztőben a következők szerint:

   ```azurecli-interactive
   vi azure_create_complete_vm.yml
   ```

1. Az **I** billentyű lenyomásával lépjen beszúrási módba.

1. Illessze be a [teljes Ansible-mintaforgatókönyvet](#complete-sample-ansible-playbook) a szerkesztőbe.

1. A beszúrás módból az **Esc** billentyűvel léphet ki.

1. Mentse a fájlt, és lépjen ki a VI-szerkesztőből a következő parancs megadásával:

    ```bash
    :wq
    ```

1. Futtassa az Ansible-mintaforgatókönyvet.

   ```bash
   ansible-playbook azure_create_complete_vm.yml
   ```

1. A kimenet a következőképpen néz ki, és látható, hogy sikeresen létrejött egy virtuális gép:

   ```bash
   PLAY [Create Azure VM] ****************************************************

   TASK [Gathering Facts] ****************************************************
   ok: [localhost]

   TASK [Create resource group] *********************************************
   changed: [localhost]

   TASK [Create virtual network] *********************************************
   changed: [localhost]

   TASK [Add subnet] *********************************************************
   changed: [localhost]

   TASK [Create public IP address] *******************************************
   changed: [localhost]

   TASK [Dump public IP for VM which will be created] ********************************************************************
   ok: [localhost] => {
      "msg": "The public IP is <ip-address>."
   }

   TASK [Create Network Security Group that allows SSH] **********************
   changed: [localhost]

   TASK [Create virtual network inteface card] *******************************
   changed: [localhost]

   TASK [Create VM] **********************************************************
   changed: [localhost]

   PLAY RECAP ****************************************************************
   localhost                  : ok=8    changed=7    unreachable=0    failed=0
   ```

1. A Linux rendszerű virtuális gép az SSH paranccsal érhető el. Cserélje le az &lt;ip-address> helyőrzőt az előző lépésben kapott nyilvános IP-címre.

    ```bash
    ssh azureuser@<ip-address>
    ```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"] 
> [Rövid útmutató: Az Ansible segítségével Azure-beli Linuxos virtuális gépek kezelése](./ansible-manage-linux-vm.md)