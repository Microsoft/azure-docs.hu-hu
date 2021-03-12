---
title: Oktatóanyag – egyéni virtuálisgép-rendszerképek létrehozása az Azure CLI-vel
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
ms.openlocfilehash: 40ec86030e136a06fe240e473a469681dcfa7c4f
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102557761"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-the-azure-cli"></a>Oktatóanyag: Azure-beli virtuális gép egyéni rendszerképének létrehozása az Azure CLI használatával

Az egyéni rendszerképek olyanok, mint a piactérről beszerzett rendszerképek, de Ön hozza azokat létre. Az egyéni rendszerképek segítségével indíthatók olyan konfigurálások, mint az alkalmazások betöltése, alkalmazások konfigurálása és más operációsrendszer-konfigurálások. Ebben az oktatóanyagban létrehoz egy egyéni rendszerképet egy Azure-beli virtuális gépről. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Shared Image Gallery létrehozása
> * Rendszerkép-definíció létrehozása
> * Rendszerképverzió létrehozása
> * Virtuális gép létrehozása rendszerképből 
> * Képtár megosztása


Ez az oktatóanyag a CLI-t használja a [Azure Cloud Shellon](../../cloud-shell/overview.md)belül, amely folyamatosan frissül a legújabb verzióra. A Cloud Shell megnyitásához válassza a **kipróbálás** lehetőséget a kód bármely blokkjának elejéről.

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.4.0 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="overview"></a>Áttekintés

A [megosztott képgyűjtemény](../shared-image-galleries.md) egyszerűbbé teszi a szervezeten belüli Egyéni rendszerképek megosztását. Az egyéni rendszerképek olyanok, mint a piactérről beszerzett rendszerképek, de Ön hozza azokat létre. Az egyéni rendszerképek segítségével indíthatók olyan konfigurálások, mint az alkalmazások betöltése, alkalmazások konfigurálása és más operációsrendszer-konfigurálások. 

A megosztott képkatalógus lehetővé teszi az egyéni virtuálisgép-rendszerképek megosztását másokkal. Válassza ki a megosztani kívánt képeket, mely régiókat szeretné elérhetővé tenni a alkalmazásban, és hogy kivel szeretné megosztani azokat. 

A megosztott képkatalógus funkció több erőforrástípust tartalmaz:

[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../../includes/virtual-machines-shared-image-gallery-resources.md)]

## <a name="before-you-begin"></a>Előkészületek

Az alábbi lépések ismertetik, hogyan alakíthat egy meglévő virtuális gépet újrahasznosítható egyéni rendszerképpé, amellyel új virtuálisgép-példányokat hozhat létre.

Az oktatóanyagban található példa elvégzéséhez szüksége lesz egy meglévő virtuális gépre. Ha szükséges, megtekintheti a [CLI](quick-create-cli.md) gyors üzembe helyezését az oktatóanyaghoz használandó virtuális gép létrehozásához. Az oktatóanyagban végzett munka során cserélje le az erőforrás nevét, ahol szükséges.

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell elindítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shell egy külön böngészőablakban is elindíthatja [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

## <a name="create-an-image-gallery"></a>Rendszerkép-gyűjtemény létrehozása 

A képgyűjtemény a képmegosztás engedélyezéséhez használt elsődleges erőforrás. 

A katalógus nevének megengedett karaktere nagybetűs vagy kisbetűk, számjegyek, pontok és időszakok. A gyűjtemény neve nem tartalmazhat kötőjeleket.   A katalógus nevének egyedinek kell lennie az előfizetésen belül. 

Hozzon létre egy képtárat [az az SIG Create](/cli/azure/sig#az-sig-create)paranccsal. A következő példában létrehozunk egy *myGalleryRG* nevű ERŐFORRÁSCSOPORTOT az *USA keleti* régiójában, valamint egy *MyGallery* nevű katalógust.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="get-information-about-the-vm"></a>A virtuális géppel kapcsolatos információk lekérése

Megtekintheti az [az VM List](/cli/azure/vm#az-vm-list)paranccsal elérhető virtuális gépek listáját. 

```azurecli-interactive
az vm list --output table
```

Ha ismeri a virtuális gép nevét és a hozzá tartozó erőforráscsoportot, szerezze be a virtuális gép AZONOSÍTÓját az [az VM Get-instance-View](/cli/azure/vm#az-vm-get-instance-view)paranccsal. 

```azurecli-interactive
az vm get-instance-view -g MyResourceGroup -n MyVm --query id
```

Másolja ki a virtuális gép AZONOSÍTÓját, hogy később használhassa.

## <a name="create-an-image-definition"></a>Rendszerkép-definíció létrehozása

A rendszerkép-definíciók logikai csoportosítást hoznak létre a képekhez. Ezek az adatok a bennük létrehozott rendszerkép-verziókra vonatkozó információk kezelésére szolgálnak. 

A képdefiníciók nevei kis-és nagybetűket, számokat, pontokat, kötőjeleket és pontokat tartalmazhatnak. 

További információ a képdefiníciók által megadható értékekről: [képdefiníciók](../shared-image-galleries.md#image-definitions).

Hozzon létre egy rendszerkép-definíciót a galériában az [az SIG rendszerkép-definition Create](/cli/azure/sig/image-definition#az-sig-image-definition-create)paranccsal. 

Ebben a példában a képdefiníció neve *myImageDefinition*, és egy [speciális](../shared-image-galleries.md#generalized-and-specialized-images) Linux operációsrendszer-rendszerképhez van. 

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

Másolja a rendszerkép-definíció AZONOSÍTÓját a kimenetből későbbi használatra.

## <a name="create-the-image-version"></a>A rendszerkép verziójának létrehozása

Hozzon létre egy rendszerkép-verziót a virtuális gépről az [az rendszerkép-gyűjtemény létrehozása-rendszerkép-Version](/cli/azure/sig/image-version#az-sig-image-version-create)paranccsal.  

A képverzió megengedett karaktereinek száma számok és időszakok. A számoknak egy 32 bites egész számon belüli tartományba kell esniük. Formátum: *MajorVersion*. *MinorVersion*. *Javítás*.

Ebben a példában a rendszerkép verziója a *1.0.0* , és két replikát fogunk létrehozni az *USA nyugati középső* régiójában, 1 replika az *USA déli középső* régiójában és 1 replika az *USA 2. keleti* régiójában, a zóna redundáns tárolásával. A replikációs régióknak tartalmaznia kell azt a régiót, amelyben a forrás virtuális gép található.

Cserélje le a `--managed-image` példában szereplő értéket a virtuális gép azonosítójával az előző lépésből.

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
> Meg kell várnia, amíg a rendszerkép verziója teljesen elkészült és replikálva lett ahhoz, hogy ugyanazt a felügyelt képet használhassa egy másik rendszerkép-verzió létrehozásához.
>
> A rendszerképet a prémium tárolóban is tárolhatja egy hozzáadási `--storage-account-type  premium_lrs` vagy a [zóna redundáns tárterületével](../../storage/common/storage-redundancy.md) , `--storage-account-type  standard_zrs` Ha létrehozza a rendszerkép verzióját.
>

 
## <a name="create-the-vm"></a>A virtuális gép létrehozása

Hozza létre a virtuális gépet az az [VM Create](/cli/azure/vm#az-vm-create) paranccsal a--Specialized paraméterrel, amely jelzi, hogy a rendszerkép egy speciális rendszerkép. 

A rendszerkép-definíció AZONOSÍTÓjának használatával `--image` hozza létre a virtuális gépet a rendelkezésre álló rendszerkép legújabb verziójából. A virtuális gépet egy adott verzióból is létrehozhatja, ha megadja a rendszerkép-verziójának AZONOSÍTÓját `--image` . 

Ebben a példában egy virtuális gépet hozunk létre a *myImageDefinition* -lemezkép legújabb verziójából.

```azurecli
az group create --name myResourceGroup --location eastus
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
    --specialized
```

## <a name="share-the-gallery"></a>A katalógus megosztása

Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával megoszthat lemezképeket az előfizetések között. A képeket megoszthatja a katalógusban, a képdefinícióban vagy a lemezkép verziójában. Minden olyan felhasználó, aki olvasási engedéllyel rendelkezik a lemezkép verziójához, még az előfizetések között is, a lemezkép verziója segítségével telepítheti a virtuális gépet.

Javasoljuk, hogy a katalógus szintjén ossza meg más felhasználókkal. A katalógus objektum-AZONOSÍTÓjának lekéréséhez használja az [az SIG show](/cli/azure/sig#az-sig-show)lehetőséget.

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Használja az objektumazonosító hatókörként, valamint egy e-mail-cím és [az az szerepkör-hozzárendelés létrehozása](/cli/azure/role/assignment#az-role-assignment-create) lehetőséget, hogy a felhasználók hozzáférhessenek a megosztott képgyűjteményhez. Cserélje le `<email-address>` a és a értékét `<gallery iD>` a saját adataira.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

Az erőforrások Azure RBAC történő megosztásával kapcsolatos további információkért lásd: Azure-beli [szerepkör-hozzárendelések hozzáadása vagy eltávolítása az Azure CLI használatával](../../role-based-access-control/role-assignments-cli.md).

## <a name="azure-image-builder"></a>Azure Image Builder

Az Azure a csomagoló, az [Azure VM rendszerkép-készítő](../image-builder-overview.md)szolgáltatásra épülő szolgáltatást is kínál. Egyszerűen írja le a testreszabásokat egy sablonban, és kezeli a képek létrehozását. 

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozott egy egyéni virtuálisgép-rendszerképet. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * Shared Image Gallery létrehozása
> * Rendszerkép-definíció létrehozása
> * Rendszerképverzió létrehozása
> * Virtuális gép létrehozása rendszerképből 
> * Képtár megosztása

A következő oktatóanyagban a magas rendelkezésre állású virtuális gépeket ismerheti meg.

> [!div class="nextstepaction"]
> [Magas rendelkezésre állású virtuális gépek létrehozása](tutorial-availability-sets.md)