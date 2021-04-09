---
title: Oktatóanyag – egyéni virtuálisgép-rendszerkép használata egy méretezési csoporton az Azure CLI-vel
description: Megismerheti, hogyan hozhat létre egyéni virtuálisgép-rendszerképeket az Azure CLI használatával, amelyek használatával virtuálisgép-méretezési csoportokat helyezhet üzembe
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.topic: tutorial
ms.date: 05/01/2020
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.reviewer: akjosh
ms.openlocfilehash: b12715e299f523d7ace56a72b0098b5d7ffac0ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98683055"
---
# <a name="tutorial-create-and-use-a-custom-image-for-virtual-machine-scale-sets-with-the-azure-cli"></a>Oktatóanyag: Egyéni rendszerkép létrehozása és használata virtuálisgép-méretezési csoportokhoz az Azure CLI-vel
Méretezési csoport létrehozása során meg kell adnia egy rendszerképet a virtuálisgép-példányok üzembe helyezésekor. Egyéni virtuálisgép-rendszerkép használatával csökkentheti a feladatok számát a virtuálisgép-példányok üzembe helyezése után. Ez az egyéni virtuálisgép-rendszerkép tartalmaz minden szükséges alkalmazástelepítést és -konfigurációt. A méretezési csoportban létrehozott összes virtuálisgép-példány az egyéni virtuálisgép-rendszerképet használja, és készen állnak az alkalmazás forgalmának kiszolgálására. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Shared Image Gallery létrehozása
> * Speciális rendszerkép-definíció létrehozása
> * Rendszerképverzió létrehozása
> * Méretezési csoport létrehozása speciális rendszerképből
> * Képtár megosztása


[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.4.0 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="overview"></a>Áttekintés

A [megosztott képgyűjtemény](../virtual-machines/shared-image-galleries.md) egyszerűbbé teszi a szervezeten belüli Egyéni rendszerképek megosztását. Az egyéni rendszerképek olyanok, mint a piactérről beszerzett rendszerképek, de Ön hozza azokat létre. Az egyéni rendszerképek segítségével indíthatók olyan konfigurálások, mint az alkalmazások betöltése, alkalmazások konfigurálása és más operációsrendszer-konfigurálások. 

A megosztott képkatalógus lehetővé teszi az egyéni virtuálisgép-rendszerképek megosztását másokkal. Válassza ki a megosztani kívánt képeket, mely régiókat szeretné elérhetővé tenni a alkalmazásban, és hogy kivel szeretné megosztani azokat. 

## <a name="create-and-configure-a-source-vm"></a>Forrás virtuális gép létrehozása és konfigurálása

Először is hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group), majd egy virtuális gépet az [az vm create](/cli/azure/vm) paranccsal. Ezt a virtuális gépet a rendszer a rendszerkép forrásaként használja. A következő példában létrehozunk egy *myVM* nevű virtuális gépet a *myResourceGroup* nevű erőforráscsoportban:

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
> A virtuális gép **azonosítója** az az [VM Create](/cli/azure/vm) parancs kimenetében látható. Másolja ezt a biztonságos bejelentkezést, hogy később is használhassa az oktatóanyagban.

A virtuális gép nyilvános IP-címe az az [VM Create](/cli/azure/vm) parancs kimenetében is látható. Hozzon létre egy SSH-kapcsolatot a virtuális gép nyilvános IP-címével az alább látható módon:

```console
ssh azureuser@<publicIpAddress>
```

A virtuális gép testre szabásához telepítünk egy alapszintű webkiszolgálót. A méretezési csoport virtuálisgép-példánya az üzembe helyezéskor már rendelkezni fog a webalkalmazás futtatásához szükséges összes csomaggal. Használja az `apt-get` parancsot az *NGINX* telepítéséhez az alábbi módon:

```bash
sudo apt-get install -y nginx
```

Ha elkészült, `exit` az SSH-kapcsolat leválasztásához írja be a következőt:.

## <a name="create-an-image-gallery"></a>Rendszerkép-gyűjtemény létrehozása 

A képgyűjtemény a képmegosztás engedélyezéséhez használt elsődleges erőforrás. 

A katalógus nevének megengedett karaktere nagybetűs vagy kisbetűk, számjegyek, pontok és időszakok. A gyűjtemény neve nem tartalmazhat kötőjeleket.   A katalógus nevének egyedinek kell lennie az előfizetésen belül. 

Hozzon létre egy képtárat [az az SIG Create](/cli/azure/sig#az-sig-create)paranccsal. A következő példában létrehozunk egy *myGalleryRG* nevű ERŐFORRÁSCSOPORTOT az *USA keleti* régiójában, valamint egy *MyGallery* nevű katalógust.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="create-an-image-definition"></a>Rendszerkép-definíció létrehozása

A rendszerkép-definíciók logikai csoportosítást hoznak létre a képekhez. Ezek az adatok a bennük létrehozott rendszerkép-verziókra vonatkozó információk kezelésére szolgálnak. 

A képdefiníciók nevei kis-és nagybetűket, számokat, pontokat, kötőjeleket és pontokat tartalmazhatnak. 

Győződjön meg arról, hogy a megfelelő típus a rendszerkép definíciója. Ha általánosított a virtuális gépet (a Windows Sysprep használatával vagy a Linux-waagent), akkor létre kell hoznia egy általánosított rendszerkép-definíciót a használatával `--os-state generalized` . Ha a virtuális gépet a meglévő felhasználói fiókok eltávolítása nélkül szeretné használni, hozzon létre egy speciális rendszerkép-definíciót a használatával `--os-state specialized` .

További információ a képdefiníciók által megadható értékekről: [képdefiníciók](../virtual-machines/shared-image-galleries.md#image-definitions).

Hozzon létre egy rendszerkép-definíciót a galériában az [az SIG rendszerkép-definition Create](/cli/azure/sig/image-definition#az-sig-image-definition-create)paranccsal.

Ebben a példában a képdefiníció neve *myImageDefinition*, és egy [speciális](../virtual-machines/shared-image-galleries.md#generalized-and-specialized-images) Linux operációsrendszer-rendszerképhez van. Ha Windows operációs rendszert használó lemezképek definícióját szeretné létrehozni, használja a következőt: `--os-type Windows` . 

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
> A rendszerkép definíciójának **azonosítója** megjelenik a parancs kimenetében. Másolja ezt a biztonságos bejelentkezést, hogy később is használhassa az oktatóanyagban.


## <a name="create-the-image-version"></a>A rendszerkép verziójának létrehozása

Hozzon létre egy rendszerkép-verziót a virtuális gépről az [az rendszerkép-gyűjtemény létrehozása-rendszerkép-Version](/cli/azure/sig/image-version#az-sig-image-version-create)paranccsal.  

A képverzió megengedett karaktereinek száma számok és időszakok. A számoknak egy 32 bites egész számon belüli tartományba kell esniük. Formátum: *MajorVersion*. *MinorVersion*. *Javítás*.

Ebben a példában a rendszerkép verziója a *1.0.0* , és 1 replikát fogunk létrehozni az *USA déli középső* régiójában és 1 replika az *USA 2. keleti* régiójában. A replikációs régióknak tartalmaznia kell azt a régiót, amelyben a forrás virtuális gép található.

Cserélje le a `--managed-image` példában szereplő értéket a virtuális gép azonosítójával az előző lépésből.

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
> Meg kell várnia, amíg a rendszerkép verziója teljesen elkészült és replikálva lett ahhoz, hogy ugyanazt a felügyelt képet használhassa egy másik rendszerkép-verzió létrehozásához.
>
> A rendszerképet a Premium Storage szolgáltatásban is tárolhatja a `--storage-account-type  premium_lrs` rendszerkép verziójának létrehozásakor a Hozzáadás vagy a [zóna redundáns tárterülete](../storage/common/storage-redundancy.md) használatával `--storage-account-type  standard_zrs` .
>




## <a name="create-a-scale-set-from-the-image"></a>Méretezési csoport létrehozása a rendszerképből
Méretezési csoport létrehozása a speciális rendszerképből a használatával [`az vmss create`](/cli/azure/vmss#az-vmss-create) . 

A méretezési csoport létrehozásához használja [`az vmss create`](/cli/azure/vmss#az-vmss-create) a--speciális paramétert, ezzel jelezve, hogy a rendszerkép egy speciális rendszerkép. 

A rendszerkép-definíció AZONOSÍTÓjának használatával `--image` hozza létre a méretezési csoport példányait az elérhető lemezkép legújabb verziójából. A méretezési csoport példányait egy adott verzióból is létrehozhatja, ha megadja a rendszerkép-verziójának AZONOSÍTÓját `--image` . 

Hozzon létre egy *myScaleSet* nevű méretezési készletet a korábban létrehozott *myImageDefinition* -rendszerkép legújabb verziójával.

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

Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával megoszthat lemezképeket az előfizetések között. A képeket megoszthatja a katalógusban, a kép definíciójában vagy a lemezkép verziójában. Minden olyan felhasználó, aki olvasási engedéllyel rendelkezik a lemezkép verziójához, még az előfizetések között is, a lemezkép verziója segítségével telepítheti a virtuális gépet.

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

Az erőforrások Azure RBAC történő megosztásával kapcsolatos további információkért lásd: Azure-beli [szerepkör-hozzárendelések hozzáadása vagy eltávolítása az Azure CLI használatával](../role-based-access-control/role-assignments-cli.md).


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
A méretezési csoport és a további erőforrások eltávolításához törölje az erőforráscsoportot és a hozzá tartozó összes erőforrást az [az Group delete](/cli/azure/group)paranccsal. A `--no-wait` paraméter visszaadja a vezérlést a parancssornak, és nem várja meg a művelet befejeztét. A `--yes` paraméter megerősíti, hogy további kérdés nélkül szeretné törölni az erőforrásokat.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban megtudhatta, hogyan hozhat létre és használhat egyéni virtuálisgép-rendszerképet a méretezési csoportjai esetében az Azure CLI-vel:

> [!div class="checklist"]
> * Shared Image Gallery létrehozása
> * Speciális rendszerkép-definíció létrehozása
> * Rendszerképverzió létrehozása
> * Méretezési csoport létrehozása speciális rendszerképből
> * Képtár megosztása

A következő oktatóanyag azt mutatja be, hogyan helyezhet alkalmazásokat üzembe a méretezési csoportban.

> [!div class="nextstepaction"]
> [Alkalmazások üzembe helyezése a méretezési csoportban](tutorial-install-apps-cli.md)