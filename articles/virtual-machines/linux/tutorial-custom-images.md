---
title: Oktatóanyag – Egyéni virtuálisgép-rendszerképek létrehozása az Azure CLI használatával
description: Ez az oktatóanyag ismerteti, hogyan használja az Azure CLI-t egyéni virtuálisgép-rendszerképek az Azure-ban történő létrehozásához
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.reviewer: akjosh
ms.openlocfilehash: 81bbf0b49919db68407a85b9ea2f731c5f8e1d91
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769876"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-the-azure-cli"></a>Oktatóanyag: Azure-beli virtuális gép egyéni rendszerképének létrehozása az Azure CLI használatával

Az egyéni rendszerképek olyanok, mint a piactérről beszerzett rendszerképek, de Ön hozza azokat létre. Az egyéni rendszerképek segítségével indíthatók olyan konfigurálások, mint az alkalmazások betöltése, alkalmazások konfigurálása és más operációsrendszer-konfigurálások. Ebben az oktatóanyagban létrehoz egy egyéni rendszerképet egy Azure-beli virtuális gépről. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Shared Image Gallery létrehozása
> * Rendszerkép-definíció létrehozása
> * Rendszerképverzió létrehozása
> * Virtuális gép létrehozása rendszerképből 
> * Képgaléria megosztása


Ez az oktatóanyag a cli-t használja [a Azure Cloud Shell,](../../cloud-shell/overview.md)amely folyamatosan frissül a legújabb verzióra. A kód Cloud Shell válassza a Try **it (Próbálja** ki) gombra a kódblokkok tetején.

Ha a CLI helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.4.0-s vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="overview"></a>Áttekintés

A [Shared Image Gallery](../shared-image-galleries.md) leegyszerűsíti az egyéni rendszerképmegosztást a szervezeten belül. Az egyéni rendszerképek olyanok, mint a piactérről beszerzett rendszerképek, de Ön hozza azokat létre. Az egyéni rendszerképek segítségével indíthatók olyan konfigurálások, mint az alkalmazások betöltése, alkalmazások konfigurálása és más operációsrendszer-konfigurálások. 

A Shared Image Gallery lehetővé teszi az egyéni virtuálisgép-rendszerképek másokkal való megosztását. Válassza ki, hogy mely rendszerképeket szeretné megosztani, mely régiókban szeretné elérhetővé tenni őket, és hogy kivel szeretné megosztani őket. 

A Shared Image Gallery funkció több erőforrástípussal rendelkezik:

[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../../includes/virtual-machines-shared-image-gallery-resources.md)]

## <a name="before-you-begin"></a>Előkészületek

Az alábbi lépések ismertetik, hogyan alakíthat egy meglévő virtuális gépet újrahasznosítható egyéni rendszerképpé, amellyel új virtuálisgép-példányokat hozhat létre.

Az oktatóanyagban található példa elvégzéséhez szüksége lesz egy meglévő virtuális gépre. Ha szükséges, tekintse meg a [CLI](quick-create-cli.md) rövid útmutatóját, amelyből megtudhatja, hogyan hozhat létre virtuális gépet ehhez az oktatóanyaghoz. Az oktatóanyag munka közben szükség szerint cserélje le az erőforrásneveket.

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell elindítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A böngészőablakot Cloud Shell böngészőlapon is elindíthatja a következő gombra: [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

## <a name="create-an-image-gallery"></a>Rendszerkép-katalógus létrehozása 

A rendszerkép-katalógus a rendszerképmegosztás engedélyezéséhez használt elsődleges erőforrás. 

A katalógus nevének engedélyezett karakterei a kis- és nagybetűk, számjegyek, pont és pont. A katalógus neve nem tartalmazhat kötőjeleket.   A katalógusneveknek egyedinek kell lennie az előfizetésen belül. 

Hozzon létre egy [képkatatárat az az sig create használatával.](/cli/azure/sig#az_sig_create) A következő példában létrehozunk egy *myGalleryRG* nevű erőforráscsoportot az *USA* keleti részén, valamint egy *myGallery nevű katalógust.*

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="get-information-about-the-vm"></a>Információk lekért a virtuális gépről

A rendelkezésre álló virtuális gépek listáját az [az vm list listában láthatja.](/cli/azure/vm#az_vm_list) 

```azurecli-interactive
az vm list --output table
```

Ha már ismeri a virtuális gép nevét és erőforráscsoportját, az [az vm get-instance-view](/cli/azure/vm#az_vm_get_instance_view)használatával szerezze be a virtuális gép azonosítóját. 

```azurecli-interactive
az vm get-instance-view -g MyResourceGroup -n MyVm --query id
```

Másolja ki a virtuális gép azonosítóját későbbi használatra.

## <a name="create-an-image-definition"></a>Rendszerkép-definíció létrehozása

A képdefiníciók logikai csoportosítást hoznak létre a képekhez. Ezek a bennük létrehozott rendszerképverziók információinak kezelésére használhatók. 

A képdefiníciók nevei nagybetűkből vagy kisbetűkből, számjegyekből, pontból, kötőjelekből és pontból is felhasználhatja őket. 

További információ a képdefiníciókhoz megadható értékekről: [Képdefiníciók.](../shared-image-galleries.md#image-definitions)

Hozzon létre egy képdefiníciót a katalógusban [az az sig image-definition create használatával.](/cli/azure/sig/image-definition#az_sig_image_definition_create) 

Ebben a példában a képdefiníció neve *myImageDefinition,* és [egy](../shared-image-galleries.md#generalized-and-specialized-images) specializált Linux operációsrendszer-rendszerképhez készült. 

```azurecli-interactive 
az sig image-definition create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```

Másolja ki a kimenetből a képdefiníció azonosítóját későbbi használatra.

## <a name="create-the-image-version"></a>A rendszerkép verziójának létrehozása

Hozzon létre egy rendszerképverziót a virtuális gépről [az az image gallery create-image-version használatával.](/cli/azure/sig/image-version#az_sig_image_version_create)  

A képverzió megengedett karakterei a számok és a időszakok. A számoknak egy 32 bites egész szám tartományán belül kell lennie. Formátum: *MajorVersion.* *Alverzió.* *Javítás.*

Ebben a példában a rendszerkép verziója *1.0.0,* és 2 replikát fogunk létrehozni az USA  nyugati középső régiójában, 1 replikát az USA déli középső régiójában és 1 replikát az USA *2.* keleti régiójában zónaredundáns tárolás használatával.  A replikációs régióknak tartalmazniuk kell azt a régiót, ahol a forrás virtuális gép található.

Cserélje le a értékét ebben a példában az előző lépésben található virtuális `--managed-image` gép azonosítójára.

```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "westcentralus" "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> Meg kell várnia, amíg a rendszerkép teljes létrehozása és replikálása befejeződik, mielőtt ugyanezt a felügyelt rendszerképet egy másik rendszerképverzió létrehozására használhatja.
>
> A rendszerképet az előzetes tárolóban is tárolhatja egy hozzáadásával, vagy zónaredundáns tárolás hozzáadásával a `--storage-account-type  premium_lrs` rendszerképverzió [](../../storage/common/storage-redundancy.md) `--storage-account-type  standard_zrs` létrehozásakor.
>

 
## <a name="create-the-vm"></a>A virtuális gép létrehozása

Hozza létre a virtuális gépet [az az vm create](/cli/azure/vm#az_vm_create) használatával a --specializált paraméterrel, jelezve, hogy a rendszerkép egy specializált rendszerkép. 

A rendszerkép-definíció azonosítójával hozza létre a virtuális gépet a rendszerkép legújabb elérhető `--image` verziójából. A virtuális gépet egy adott verzióból is létrehozhatja, ha a rendszerkép verzióazonosítóját meg kell adva `--image` a számára. 

Ebben a példában egy virtuális gépet hozunk létre a *myImageDefinition rendszerkép legújabb verziójából.*

```azurecli
az group create --name myResourceGroup --location eastus
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
    --specialized
```

## <a name="share-the-gallery"></a>A katalógus megosztása

Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával rendszerképeket oszthat meg az előfizetések között. A rendszerképeket a katalógusban, a rendszerkép definíciójában vagy a rendszerkép verziójában oszthatja meg. Minden olyan felhasználó, aki olvasási engedéllyel rendelkezik egy rendszerképverzióhoz, akár előfizetések között is, üzembe helyezhet egy virtuális gépet a rendszerkép verziójával.

Javasoljuk, hogy a katalógus szintjén ossza meg a többi felhasználóval. A katalógus objektumazonosítójának leához használja [az az sig show használhatja a következőt:](/cli/azure/sig#az_sig_show).

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Használja az objektumazonosítót hatókörként, valamint egy e-mail-címet és [az az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) címet, hogy hozzáférést biztosítson a felhasználónak a megosztott rendszerkép-katalógushoz. Cserélje le `<email-address>` a és `<gallery iD>` a adatokat a saját adataira.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

További információ az erőforrások Azure RBAC használatával való megosztásáról: Azure-beli szerepkör-hozzárendelések hozzáadása vagy eltávolítása [az Azure CLI használatával.](../../role-based-access-control/role-assignments-cli.md)

## <a name="azure-image-builder"></a>Azure Image Builder

Az Azure egy Packerre épülő szolgáltatást is kínál, amely [Azure VM Image Builder.](../image-builder-overview.md) Egyszerűen írja le a sablon testreszabásait, és ez kezeli a rendszerkép létrehozását. 

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozott egy egyéni virtuálisgép-rendszerképet. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * Shared Image Gallery létrehozása
> * Rendszerkép-definíció létrehozása
> * Rendszerképverzió létrehozása
> * Virtuális gép létrehozása rendszerképből 
> * Rendszerkép-katalógus megosztása

A következő oktatóanyagban a magas rendelkezésre állású virtuális gépeket ismerheti meg.

> [!div class="nextstepaction"]
> [Magas rendelkezésre állású virtuális gépek létrehozása](tutorial-availability-sets.md)