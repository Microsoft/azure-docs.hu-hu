---
title: Linuxos virtuális gép másolása az Azure CLI-vel
description: Ismerje meg, hogyan hozhat létre másolatot az Azure Linux rendszerű virtuális gépről az Azure CLI és a Managed Disks használatával.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 10/17/2018
ms.author: cynthn
ms.custom: legacy, devx-track-azurecli
ms.openlocfilehash: 05b45745565dface6b29f5a05cb1b557f4a49f6e
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102558396"
---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-and-managed-disks"></a>Linux rendszerű virtuális gép másolatának létrehozása az Azure CLI és a Managed Disks használatával

Ez a cikk bemutatja, hogyan hozhat létre egy Linux rendszerű virtuális gépet az Azure CLI használatával. A virtuálisgép-lemezképek méretekben történő másolásához, létrehozásához, tárolásához és megosztásához tekintse meg a [megosztott lemezképek gyűjteményét](../shared-images-cli.md).

A virtuális [merevlemezeket feltöltheti és létrehozhatja egy VHD-ről](upload-vhd.md)is.

## <a name="prerequisites"></a>Előfeltételek

-   Telepítse az [Azure CLI](/cli/azure/install-az-cli2)-t.

-   Jelentkezzen be egy Azure-fiókba az [az login](/cli/azure/reference-index#az-login)paranccsal.

-   Rendelkeznie kell egy Azure-beli virtuális géppel, amelyet a másolat forrásaként használhat.

## <a name="stop-the-source-vm"></a>A forrás virtuális gép leállítása

Szabadítsa fel a forrás virtuális gépet az [az VM felszabadítása](/cli/azure/vm#az-vm-deallocate)paranccsal.
Az alábbi példa felszabadítja a *myVM* nevű virtuális gépet az erőforráscsoport *myResourceGroup*:

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

## <a name="copy-the-source-vm"></a>A forrás virtuális gép másolása

Egy virtuális gép másolásához létre kell hoznia a mögöttes virtuális merevlemez másolatát. Ez a folyamat egy speciális virtuális merevlemezt (VHD-t) hoz létre felügyelt lemezként, amely ugyanazokat a konfigurációkat és beállításokat tartalmazza, mint a forrásoldali virtuális gép.

További információ az Azure Managed Disksről: [Azure Managed Disks – áttekintés](../managed-disks-overview.md). 

1.  Sorolja fel az összes virtuális gépet és az operációsrendszer-lemez nevét az [az VM List](/cli/azure/vm#az-vm-list)paranccsal. Az alábbi példa felsorolja a *myResourceGroup* nevű erőforráscsoport összes virtuális gépet:
    
    ```azurecli
    az vm list -g myResourceGroup \
         --query '[].{Name:name,DiskName:storageProfile.osDisk.name}' \
         --output table
    ```

    A kimenet a következő példához hasonló:

    ```azurecli
    Name    DiskName
    ------  --------
    myVM    myDisk
    ```

1.  Másolja a lemezt egy új felügyelt lemez létrehozásával és az [az Disk Create](/cli/azure/disk#az-disk-create)paranccsal. A következő példa létrehoz egy *myCopiedDisk* nevű lemezt a *myDisk* nevű felügyelt lemezről:

    ```azurecli
    az disk create --resource-group myResourceGroup \
         --name myCopiedDisk --source myDisk
    ``` 

1.  A felügyelt lemezeket az [az Disk List](/cli/azure/disk#az-disk-list)paranccsal ellenőrizheti az erőforráscsoporthoz. A következő példa a *myResourceGroup* nevű erőforráscsoport felügyelt lemezeit sorolja fel:

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```


## <a name="set-up-a-virtual-network"></a>Virtuális hálózat beállítása

A következő választható lépések új virtuális hálózatot, alhálózatot, nyilvános IP-címet és virtuális hálózati adaptert (NIC) hoznak létre.

Ha a virtuális GÉPET hibaelhárítási célokra vagy további központi telepítésekre másolja, előfordulhat, hogy nem szeretne virtuális GÉPET használni egy meglévő virtuális hálózaton.

Ha virtuális hálózati infrastruktúrát szeretne létrehozni a másolt virtuális gépekhez, kövesse a következő néhány lépést. Ha nem szeretne virtuális hálózatot létrehozni, ugorjon a virtuális [gép létrehozása](#create-a-vm)lehetőségre.

1.  Hozza létre a virtuális hálózatot az [az Network vnet Create](/cli/azure/network/vnet#az-network-vnet-create)paranccsal. Az alábbi példa egy *myVnet* nevű virtuális hálózatot és egy *mySubnet* nevű alhálózatot hoz létre:

    ```azurecli
    az network vnet create --resource-group myResourceGroup \
        --location eastus --name myVnet \
        --address-prefix 192.168.0.0/16 \
        --subnet-name mySubnet \
        --subnet-prefix 192.168.1.0/24
    ```

1.  Hozzon létre egy nyilvános IP-címet az [az Network Public-IP Create](/cli/azure/network/public-ip#az-network-public-ip-create)paranccsal. A következő példa egy *myPublicIP* nevű nyilvános IP-címet hoz létre a *mypublicdns* DNS-nevével. (Mivel a DNS-névnek egyedinek kell lennie, egyedi nevet adjon meg.)

    ```azurecli
    az network public-ip create --resource-group myResourceGroup \
        --location eastus --name myPublicIP --dns-name mypublicdns \
        --allocation-method static --idle-timeout 4
    ```

1.  Hozza létre a hálózati ADAPTERt az [az Network NIC Create](/cli/azure/network/nic#az-network-nic-create)paranccsal.
    A következő példa egy *myNic* nevű hálózati adaptert hoz létre, amely a *mySubnet* alhálózathoz van csatolva:

    ```azurecli
    az network nic create --resource-group myResourceGroup \
        --location eastus --name myNic \
        --vnet-name myVnet --subnet mySubnet \
        --public-ip-address myPublicIP
    ```

## <a name="create-a-vm"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet az [az VM Create](/cli/azure/vm#az-vm-create)paranccsal.

A következő módon adhatja meg az operációsrendszer-lemezként használandó átmásolt felügyelt lemezt ( `--attach-os-disk` ):

```azurecli
az vm create --resource-group myResourceGroup \
    --name myCopiedVM --nics myNic \
    --size Standard_DS1_v2 --os-type Linux \
    --attach-os-disk myCopiedDisk
```

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan kezelheti a virtuálisgép-lemezképeket a [megosztott képtárban](../shared-images-cli.md) .
