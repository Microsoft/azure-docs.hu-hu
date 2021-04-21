---
title: Oktatóanyag – Egyéni virtuálisgép-rendszerkép használata méretezési csoportban az Azure CLI használatával
description: Megismerheti, hogyan hozhat létre egyéni virtuálisgép-rendszerképeket az Azure CLI használatával, amelyek használatával virtuálisgép-méretezési csoportokat helyezhet üzembe
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.topic: tutorial
ms.date: 05/01/2020
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.reviewer: akjosh
ms.openlocfilehash: a9a4abe550da4f0438f875127b3b689045c06e6f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762997"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-the-azure-cli"></a>Oktatóanyag: Egyéni rendszerkép létrehozása és használata virtuálisgép-méretezési csoportokhoz az Azure CLI-vel
Méretezési csoport létrehozása során meg kell adnia egy rendszerképet a virtuálisgép-példányok üzembe helyezésekor. Egyéni virtuálisgép-rendszerkép használatával csökkentheti a feladatok számát a virtuálisgép-példányok üzembe helyezése után. Ez az egyéni virtuálisgép-rendszerkép tartalmaz minden szükséges alkalmazástelepítést és -konfigurációt. A méretezési csoportban létrehozott összes virtuálisgép-példány az egyéni virtuálisgép-rendszerképet használja, és készen állnak az alkalmazás forgalmának kiszolgálására. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Shared Image Gallery létrehozása
> * Specializált rendszerkép-definíció létrehozása
> * Rendszerképverzió létrehozása
> * Méretezési csoport létrehozása specializált rendszerképből
> * Rendszerkép-katalógus megosztása


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.4.0-s vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="overview"></a>Áttekintés

A [Shared Image Gallery](../virtual-machines/shared-image-galleries.md) leegyszerűsíti az egyéni rendszerképmegosztást a szervezeten belül. Az egyéni rendszerképek olyanok, mint a piactérről beszerzett rendszerképek, de Ön hozza azokat létre. Az egyéni rendszerképek segítségével indíthatók olyan konfigurálások, mint az alkalmazások betöltése, alkalmazások konfigurálása és más operációsrendszer-konfigurálások. 

A Shared Image Gallery lehetővé teszi az egyéni virtuálisgép-rendszerképek másokkal való megosztását. Válassza ki, hogy mely rendszerképeket szeretné megosztani, mely régiókban szeretné elérhetővé tenni őket, és hogy kivel szeretné megosztani őket. 

## <a name="create-and-configure-a-source-vm"></a>Forrás virtuális gép létrehozása és konfigurálása

Először is hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group), majd egy virtuális gépet az [az vm create](/cli/azure/vm) paranccsal. Ezután a rendszer ezt a virtuális gépet használja a rendszerkép forrásaként. A következő példában létrehozunk egy *myVM* nevű virtuális gépet a *myResourceGroup* nevű erőforráscsoportban:

```azurecli-interactive
az group create --name myResourceGroup --location eastus

az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image ubuntults \
  --admin-username azureuser \
  --generate-ssh-keys
```

> [!IMPORTANT]
> A **virtuális** gép azonosítója az az vm create parancs [kimenetében jelenik](/cli/azure/vm) meg. Másolja ki ezt a széfet, hogy az oktatóanyag későbbi oktatóanyagában használni tudja.

A virtuális gép nyilvános IP-címe az az vm create parancs [kimenetében is](/cli/azure/vm) megjelenik. Hozzon létre egy SSH-kapcsolatot a virtuális gép nyilvános IP-címével az alább látható módon:

```console
ssh azureuser@<publicIpAddress>
```

A virtuális gép testre szabásához telepítünk egy alapszintű webkiszolgálót. A méretezési csoport virtuálisgép-példánya az üzembe helyezéskor már rendelkezni fog a webalkalmazás futtatásához szükséges összes csomaggal. Használja az `apt-get` parancsot az *NGINX* telepítéséhez az alábbi módon:

```bash
sudo apt-get install -y nginx
```

Ha végzett, írja be a parancsot `exit` az SSH-kapcsolat leválasztása.

## <a name="create-an-image-gallery"></a>Rendszerkép-katalógus létrehozása 

A rendszerkép-katalógus a rendszerképmegosztás engedélyezéséhez használt elsődleges erőforrás. 

A Katalógus neve megengedett karakterei a kis- és nagybetűk, számjegyek, pont és pont. A katalógus neve nem tartalmazhat kötőjeleket.   A katalógusok nevének egyedinek kell lennie az előfizetésen belül. 

Hozzon létre egy rendszerkép-katalógust [az az sig create használatával.](/cli/azure/sig#az_sig_create) Az alábbi példa létrehoz egy *myGalleryRG* nevű erőforráscsoportot az USA keleti *részén,* valamint egy *myGallery nevű katalógust.*

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>Rendszerkép-definíció létrehozása

A képdefiníciók logikai csoportosítást hoznak létre a képekhez. Ezek a bennük létrehozott rendszerképverziók információinak kezelésére használhatók. 

A képdefiníciók neve kis- és nagybetűkből, számjegyekből, pontból, kötőjelekből és pontból is áll. 

Győződjön meg arról, hogy a képdefiníció a megfelelő típus. Ha általánosította a virtuális gépet (Windows rendszeren a Sysprep használatával vagy Linuxon a waagent -deprovision használatával), akkor létre kell hoznia egy általánosított rendszerkép-definíciót a `--os-state generalized` használatával. Ha meglévő felhasználói fiókok eltávolítása nélkül szeretné használni a virtuális gépet, hozzon létre egy specializált rendszerkép-definíciót a `--os-state specialized` használatával.

További információ a képdefiníciókhoz megadható értékekről: [Képdefiníciók.](../virtual-machines/shared-image-galleries.md#image-definitions)

Hozzon létre egy képdefiníciót a katalógusban [az az sig image-definition create használatával.](/cli/azure/sig/image-definition#az_sig_image_definition_create)

Ebben a példában a képdefiníció neve *myImageDefinition*, és [egy](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images) specializált Linux operációsrendszer-rendszerképhez készült. A lemezképek definíciójának Windows operációs rendszer használatával történő létrehozásához használja a `--os-type Windows` következőt: . 

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

> [!IMPORTANT]
> A  képdefiníció azonosítója megjelenik a parancs kimenetében. Másolja ki ezt a széfet, hogy később használni tudja az oktatóanyagban.


## <a name="create-the-image-version"></a>A rendszerkép verziójának létrehozása

Hozzon létre egy rendszerképverziót a virtuális gépről [az az image gallery create-image-version használatával.](/cli/azure/sig/image-version#az_sig_image_version_create)  

A képverzió megengedett karakterei a számok és a időszakok. A számoknak egy 32 bites egész szám tartományán belül kell lennie. Formátum: *Főverzió.* *MinorVersion (Alverzió).* *Javítás.*

Ebben a példában a rendszerkép verziója *1.0.0,* és 1  replikát fogunk létrehozni az USA déli középső régiójában, és 1 replikát az *USA 2.* keleti régiójában. A replikációs régióknak tartalmazniuk kell azt a régiót, ahol a forrás virtuális gép található.

Cserélje le a értékét ebben a példában az előző lépésben `--managed-image` lekért virtuális gép azonosítójára.

```azurecli-interactive 
az sig image-version create \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus=1" \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM"
```

> [!NOTE]
> Meg kell várnia, amíg a rendszerkép teljes verziószáma befejeződik és replikálódik, mielőtt ugyanezt a felügyelt rendszerképet egy másik rendszerképverzió létrehozásához használhatja.
>
> A rendszerképet prémium szintű tárolóban is tárolhatja egy hozzáadásával, vagy zónaredundáns tárolással, ha hozzáadja a `--storage-account-type  premium_lrs` [](../storage/common/storage-redundancy.md) `--storage-account-type  standard_zrs` lemezképverziót.
>




## <a name="create-a-scale-set-from-the-image"></a>Méretezési csoport létrehozása a rendszerképből
Méretezési csoport létrehozása a specializált rendszerképből a [`az vmss create`](/cli/azure/vmss#az_vmss_create) használatával. 

Hozza létre a méretezési készletet a --specializált paraméterrel, amely jelzi, hogy [`az vmss create`](/cli/azure/vmss#az_vmss_create) a rendszerkép egy specializált rendszerkép. 

A rendszerképdefiníció-azonosítójával hozza létre a méretezési csoport példányait a rendszerkép legújabb elérhető `--image` verziójából. A méretezési csoport példányait egy adott verzióból is létrehozhatja, ha a rendszerkép verzióazonosítóját adja meg `--image` a számára. 

Hozzon létre egy *myScaleSet nevű* méretezési csoport a korábban létrehozott *myImageDefinition* rendszerkép legújabb verzióját.

```azurecli
az group create --name myResourceGroup --location eastus
az vmss create \
   --resource-group myResourceGroup \
   --name myScaleSet \
   --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
   --specialized
```

A méretezési csoport erőforrásainak és virtuális gépeinek létrehozása és konfigurálása néhány percet vesz igénybe.


## <a name="test-your-scale-set"></a>Méretezési csoport tesztelése
Annak érdekében, hogy a forgalom elérhesse a méretezési csoportot és hogy ellenőrizhesse a webkiszolgáló megfelelő működését, hozzon létre egy terheléselosztó-szabályt az [az network lb rule create](/cli/azure/network/lb/rule) paranccsal. Az alábbi példa egy *myLoadBalancerRuleWeb* nevű szabályt hoz létre, amely engedélyezi a forgalmat a *80*-as *TCP*-porton keresztül:

```azurecli-interactive
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```

Ha működés közben szeretné megtekinteni a méretezési csoportot, kérje le a terheléselosztó nyilvános IP-címét az [az network public-ip show](/cli/azure/network/public-ip) paranccsal. A következő példa a *myScaleSetLBPublicIP* méretezési csoport részeként létrehozott IP-címét kéri le:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

Gépelje be a nyilvános IP-címet a webböngészőjébe. Az alapértelmezett NGINX-weboldal jelenik meg, ahogy az az alábbi példában is látható:

![Egyéni virtuálisgép-rendszerképről futtatott NGINX](media/tutorial-use-custom-image-cli/default-nginx-website.png)



## <a name="share-the-gallery"></a>A katalógus megosztása

Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával rendszerképeket oszthat meg az előfizetések között. A rendszerképeket a katalógusban, a rendszerkép definíciójában vagy a rendszerkép verziójában oszthatja meg. Minden olyan felhasználó, aki olvasási engedéllyel rendelkezik egy rendszerképverzióhoz, akár több előfizetésben is, üzembe helyezhet egy virtuális gépet a rendszerkép verziójával.

Javasoljuk, hogy a katalógus szintjén ossza meg a többi felhasználóval. A katalógus objektumazonosítóját az [az sig show használatával lehet lekérte.](/cli/azure/sig#az_sig_show)

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Használja az objektumazonosítót hatókörként, valamint egy e-mail-címet és [az az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) címet, hogy hozzáférést adjon egy felhasználónak a megosztott rendszerkép-katalógushoz. Cserélje le `<email-address>` a és `<gallery iD>` a adatokat a saját adataira.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

További információ az erőforrások Azure RBAC használatával való megosztásáról: Azure-beli szerepkör-hozzárendelések hozzáadása vagy eltávolítása [az Azure CLI használatával.](../role-based-access-control/role-assignments-cli.md)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
A méretezési csoport és a további erőforrások eltávolításához törölje az erőforráscsoportot és annak összes erőforrását [az az group delete parancsokkal.](/cli/azure/group) A `--no-wait` paraméter visszaadja a vezérlést a parancssornak, és nem várja meg a művelet befejeztét. A `--yes` paraméter megerősíti, hogy további kérdés nélkül szeretné törölni az erőforrásokat.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban megtudhatta, hogyan hozhat létre és használhat egyéni virtuálisgép-rendszerképet a méretezési csoportjai esetében az Azure CLI-vel:

> [!div class="checklist"]
> * Shared Image Gallery létrehozása
> * Specializált rendszerkép-definíció létrehozása
> * Rendszerképverzió létrehozása
> * Méretezési csoport létrehozása specializált rendszerképből
> * Képgaléria megosztása

A következő oktatóanyag azt mutatja be, hogyan helyezhet alkalmazásokat üzembe a méretezési csoportban.

> [!div class="nextstepaction"]
> [Alkalmazások üzembe helyezése a méretezési csoportban](tutorial-install-apps-cli.md)