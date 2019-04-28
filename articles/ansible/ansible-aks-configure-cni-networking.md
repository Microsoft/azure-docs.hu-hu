---
title: Oktatóanyag – Azure CNI a hálózatkezelés konfigurálását az Azure Kubernetes Service (AKS) az Ansible-lel |} A Microsoft Docs
description: Ismerje meg, hogyan használja az Ansible konfigurálása az Azure Kubernetes Service (AKS)-fürt hálózati kubenet
keywords: az ansible, azure, devops, bash, cloud Shell, forgatókönyv, az aks, tároló, az aks, kubernetes
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: e4d5e68d13f00b79a309fab5e315747e934a0577
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2019
ms.locfileid: "63767221"
---
# <a name="tutorial-configure-azure-cni-networking-in-azure-kubernetes-service-aks-using-ansible"></a>Oktatóanyag: Azure CNI a hálózatkezelés konfigurálását az Azure Kubernetes Service (AKS) az Ansible-lel

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-aks.md](../../includes/open-source-devops-intro-aks.md)]

Aks-beli használatával telepíthet egy fürtöt az alábbi hálózati modellek:

- [Kubenet hálózatkezelés](/azure/aks/configure-kubenet) -hálózati erőforrások általában létrehozza és beállítja, az AKS-fürt üzembe lesz helyezve.
- [Az Azure-hálózatok CNI](/azure/aks/configure-azure-cni) -AKS-fürt csatlakozik-e meglévő virtuális hálózat (VNET) erőforrásokat és konfigurációkat.

Az aks-ben az alkalmazások hálózatokkal kapcsolatos további információkért lásd: [fogalmak az aks-ben az alkalmazások hálózati](/azure/aks/concepts-network).

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * AKS-fürt létrehozása
> * Az Azure CNI hálózatkezelésének konfigurálása

## <a name="prerequisites"></a>Előfeltételek

- [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-a-virtual-network-and-subnet"></a>Virtuális hálózat és alhálózat létrehozása

A mintakód forgatókönyv ebben a szakaszban a következőkre használható:

- Virtuális hálózat létrehozása
- Hozzon létre egy alhálózatot a virtuális hálózaton belül

Mentse a következő forgatókönyvet `vnet.yml` néven:

```yml
- name: Create vnet
  azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      address_prefixes_cidr:
          - 10.0.0.0/8

- name: Create subnet
  azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      address_prefix_cidr: 10.240.0.0/16
      virtual_network_name: "{{ name }}"
  register: subnet
```

## <a name="create-an-aks-cluster-in-the-virtual-network"></a>AKS-fürt létrehozása a virtuális hálózatban

A mintakód forgatókönyv ebben a szakaszban a következőkre használható:

- Hozzon létre egy AKS-fürt virtuális hálózaton belül.

Mentse a következő forgatókönyvet `aks.yml` néven:

```yml
- name: List supported kubernetes version from Azure
  azure_rm_aks_version:
      location: "{{ location }}"
  register: versions

- name: Create AKS cluster within a VNet
  azure_rm_aks:
      resource_group: "{{ resource_group }}"
      name: "{{ name }}"
      dns_prefix: "{{ name }}"
      kubernetes_version: "{{ versions.azure_aks_versions[-1] }}"
      agent_pool_profiles:
        - count: 3
          name: nodepool1
          vm_size: Standard_D2_v2
          vnet_subnet_id: "{{ vnet_subnet_id }}"
      linux_profile:
          admin_username: azureuser
          ssh_key: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
      service_principal:
          client_id: "{{ lookup('ini', 'client_id section=default file=~/.azure/credentials') }}"
          client_secret: "{{ lookup('ini', 'secret section=default file=~/.azure/credentials') }}"
      network_profile:
          network_plugin: azure
          docker_bridge_cidr: 172.17.0.1/16
          dns_service_ip: 10.2.0.10
          service_cidr: 10.2.0.0/24
  register: aks
```

Az alábbiakban néhány kulcsfontosságú Megjegyzés figyelembe kell venni a minta forgatókönyv használata:

- Használja a `azure_rm_aks_version` modul támogatott verzió azonosításához.
- A `vnet_subnet_id` az előző szakaszban létrehozott alhálózat.
- A forgatókönyv betölti `ssh_key` a `~/.ssh/id_rsa.pub`. Ha módosít, a "ssh-rsa" kezdetű (idézőjelek nélkül) - egysoros formátumot használja.
- A `client_id` és `client_secret` értékek töltődnek be a `~/.azure/credentials`, azaz az alapértelmezett hitelesítő adatait tartalmazó fájlt. Ezeket az értékeket beállítani, ha a szolgáltatás egyszerű, vagy betölteni ezeket az értékeket a környezeti változókat:

    ```yml
    client_id: "{{ lookup('env', 'AZURE_CLIENT_ID') }}"
    client_secret: "{{ lookup('env', 'AZURE_SECRET') }}"
    ```

## <a name="run-the-sample-playbook"></a>A minta forgatókönyv futtatása

Ebben a szakaszban szereplő mintakódban forgatókönyv teszteléséhez látható az oktatóanyag során különböző funkcióihoz használatos.

Mentse a következő forgatókönyvet `aks-azure-cni.yml` néven:

```yml
---
- hosts: localhost
  vars:
      resource_group: aksansibletest
      name: aksansibletest
      location: eastus
  tasks:
     - name: Ensure resource group exists
       azure_rm_resourcegroup:
           name: "{{ resource_group }}"
           location: "{{ location }}"

     - name: Create vnet
       include_tasks: vnet.yml

     - name: Create AKS
       vars:
           vnet_subnet_id: "{{ subnet.state.id }}"
       include_tasks: aks.yml

     - name: Show AKS cluster detail
       debug:
           var: aks
```

Az alábbiakban néhány kulcsfontosságú Megjegyzés figyelembe kell venni a minta forgatókönyv használata:

- Módosítsa a `aksansibletest` értéket az erőforráscsoport nevét.
- Módosítsa a `aksansibletest` az AKS nevének értékét.
- Módosítsa a `eastus` értéket az erőforráscsoport helyét.

Futtassa a forgatókönyvet, az ansible-forgatókönyvek paranccsal:

```bash
ansible-playbook aks-azure-cni.yml
```

A forgatókönyv futtatása után a következő eredményeket hasonló kimenet jelenik meg:

```Output
PLAY [localhost] 

TASK [Gathering Facts] 
ok: [localhost]

TASK [Ensure resource group exists] 
changed: [localhost]

TASK [Create vnet] 
included: /home/devops/aks-cni/vnet.yml for localhost

TASK [Create vnet] 
changed: [localhost]

TASK [Create subnet] 
changed: [localhost]

TASK [Create AKS] 
included: /home/devops/aks-cni/aks.yml for localhost

TASK [List supported kubernetes version from Azure] 
 [WARNING]: Azure API profile latest does not define an entry for
ContainerServiceClient

ok: [localhost]

TASK [Create AKS cluster with vnet] 
changed: [localhost]

TASK [Show AKS cluster detail] 
ok: [localhost] => {
    "aks": {
        "aad_profile": {},
        "addon": {},
        "agent_pool_profiles": [
            {
                "count": 3,
                "name": "nodepool1",
                "os_disk_size_gb": 100,
                "os_type": "Linux",
                "storage_profile": "ManagedDisks",
                "vm_size": "Standard_D2_v2",
                "vnet_subnet_id": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourceGroups/aksansibletest/providers/Microsoft.Network/virtualNetworks/aksansibletest/subnets/aksansibletest"
            }
        ],
        "changed": true,
        "dns_prefix": "aksansibletest",
        "enable_rbac": false,
        "failed": false,
        "fqdn": "aksansibletest-0272707d.hcp.eastus.azmk8s.io",
        "id": "/subscriptions/BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB/resourcegroups/aksansibletest/providers/Microsoft.ContainerService/managedClusters/aksansibletest",
        "kube_config": "..."
        },
        "location": "eastus",
        "name": "aksansibletest",
        "network_profile": {
            "dns_service_ip": "10.2.0.10",
            "docker_bridge_cidr": "172.17.0.1/16",
            "network_plugin": "azure",
            "network_policy": null,
            "pod_cidr": null,
            "service_cidr": "10.2.0.0/24"
        },
        "node_resource_group": "MC_aksansibletest_aksansibletest_eastus",
        "provisioning_state": "Succeeded",
        "service_principal_profile": {
            "client_id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA"
        },
        "tags": null,
        "type": "Microsoft.ContainerService/ManagedClusters",
        "warnings": [
            "Azure API profile latest does not define an entry for ContainerServiceClient",
            "Azure API profile latest does not define an entry for ContainerServiceClient"
        ]
    }
}

PLAY RECAP 
localhost                  : ok=9    changed=4    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az ebben a cikkben létrehozott erőforrásokat. 

A mintakód forgatókönyv ebben a szakaszban a következőkre használható:

- Említett egy erőforráscsoport törlése a `vars` szakaszban.

Mentse a következő forgatókönyvet `cleanup.yml` néven:

```yml
---
- hosts: localhost
  vars:
      resource_group: {{ resource_group_name }}
  tasks:
      - name: Clean up resource group
        azure_rm_resourcegroup:
            name: "{{ resource_group }}"
            state: absent
            force: yes
```

Az alábbiakban néhány kulcsfontosságú Megjegyzés figyelembe kell venni a minta forgatókönyv használata:

- Cserélje le a `{{ resource_group_name }}` helyőrzőt az erőforráscsoport nevét.
- A megadott erőforráscsoportban található összes erőforrás törlődni fog.

Futtassa a forgatókönyvet, az ansible-forgatókönyvek paranccsal:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Az aks-ben az Ansible-lel az Azure Active Directory konfigurálása](./ansible-aks-configure-rbac.md)