---
title: Linux rendszerű virtuális gép felügyelt rendszerképének rögzítése az Azure CLI használatával
description: Rögzítsen egy Azure-beli virtuális gép felügyelt rendszerképét, amelyet tömeges üzembe helyezéshez használhat az Azure CLI használatával.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.date: 10/08/2018
ms.author: cynthn
ms.custom: legacy, devx-track-azurecli
ms.collection: linux
ms.openlocfilehash: dddbad2403734bc749497a7acca16b2a5b6076f4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792254"
---
# <a name="how-to-create-a-managed-image-of-a-virtual-machine-or-vhd"></a>Felügyelt rendszerkép létrehozása virtuális gépről vagy virtuális merevlemezről

Ha egy virtuális gép (VM) több példányát is létre kell hoznia az Azure-ban történő fejlesztéshez és teszteléshez, rögzítse a virtuális gép vagy az operációs rendszer virtuális merevlemezének felügyelt rendszerképét. A rendszerképek nagy méretekben való létrehozásáról, tárolására és megosztására lásd: [Shared Image Galleries (Megosztott rendszerkép-katalógusok).](../shared-images-cli.md)

Egy felügyelt rendszerkép legfeljebb 20 egyidejű üzembe helyezést támogat. Ha egyidejűleg több mint 20 virtuális gépet próbál létrehozni ugyanabba a felügyelt rendszerképbe, az időtúllépéseket eredményezhet egy vhD tárolási teljesítménykorlátozásai miatt. Több mint 20 virtuális gép egyidejű létrehozásához használjon egy 1 replikával konfigurált Shared [Image Katalógus](../shared-image-galleries.md) rendszerképet minden 20 egyidejű virtuálisgép-telepítéshez.

Felügyelt rendszerkép létrehozásához el kell távolítania a személyes fiókadatokat. A következő lépésekben megszüntet egy meglévő virtuális gépet, felszabadítja azt, és létrehoz egy rendszerképet. Ezzel a rendszerképpel virtuális gépeket hozhat létre az előfizetés bármely erőforráscsoportja számára.

A meglévő Linux rendszerű virtuális gép másolatának biztonsági mentéshez vagy hibakereséshez való létrehozásához, illetve egy specializált Linux virtuális merevlemez helyszíni virtuális gépről való feltöltéséhez lásd: [Linux](upload-vhd.md)rendszerű virtuális gép feltöltése és létrehozása egyéni lemezképből.  

Az Azure VM Image Builder **(nyilvános** előzetes verzió) szolgáltatással létrehozhatja az egyéni rendszerképet, nem kell megtanulnia semmilyen eszközt, vagy beállíthatja a build folyamatait, csupán egy rendszerképkonfigurációt kell beállítania, és a Image Builder létrehozza a rendszerképet. További információ: [Ismerkedés](../image-builder-overview.md)a Azure VM Image Builder.

A rendszerkép létrehozása előtt a következő elemekre lesz szüksége:

* A felügyelt lemezeket használó Resource Manager létrehozott Azure-beli virtuális gép. Ha még nem hozott létre Linux rendszerű virtuális [](quick-create-portal.md)gépet, használhatja a portált, az [Azure CLI-t,](quick-create-cli.md)vagy Resource Manager [sablonokat.](create-ssh-secured-vm-from-template.md) Szükség szerint konfigurálja a virtuális gépet. Hozzáadhat például [adatlemezeket,](add-disk.md)frissítéseket alkalmazhat, és alkalmazásokat telepíthet. 

* A legfrissebb [Azure CLI van](/cli/azure/install-az-cli2) telepítve, és be kell jelentkeznie egy Azure-fiókba az az login [használatával.](/cli/azure/reference-index#az_login)

## <a name="prefer-a-tutorial-instead"></a>Inkább egy oktatóanyagot szeretne?

A cikk egyszerűsített verziójáért, valamint a virtuális gépek Azure-beli tesztelésével, kiértékelésével vagy megismerésével kapcsolatban lásd: Egyéni rendszerkép létrehozása Azure-beli virtuális gépről [a CLI használatával.](tutorial-custom-images.md)  Ellenkező esetben olvass tovább itt, hogy teljes képet kapj.


## <a name="step-1-deprovision-the-vm"></a>1. lépés: A virtuális gép megszüntetése
Először megszüntetjük a virtuális gépet az Azure-beli virtuálisgép-ügynökkel a gépspecifikus fájlok és adatok törléséhez. Használja a `waagent` parancsot a `-deprovision+user` paraméterrel a forrás Linux rendszerű virtuális gépen. További információk: [Azure Linux-ügynök – felhasználói útmutató](../extensions/agent-linux.md). Ez a folyamat nem fordítható meg.

1. Csatlakozzon a Linux rendszerű virtuális géphez egy SSH-ügyféllel.
2. Az SSH ablakban írja be a következő parancsot:
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > Ezt a parancsot csak olyan virtuális gépen futtassa, amit rendszerképként fog rögzíteni. Ez a parancs nem garantálja, hogy a rendszerkép minden bizalmas adatból törlődik, vagy alkalmas az újraterjesztésre. A `+user` paraméter a legutóbb kiépített felhasználói fiókot is eltávolítja. Ha meg kell tartania a felhasználói fiók hitelesítő adatait a virtuális gépen, csak a következőt használja: `-deprovision` .
 
3. A folytatáshoz adja meg **az y** billentyűt. A megerősítő lépés `-force` elkerülése érdekében hozzáadhatja a paramétert.
4. A parancs befejezése után az SSH-ügyfél befejezéséhez **lépjen ki** az exit paranccsal.  A virtuális gép ezen a ponton továbbra is futni fog.

## <a name="step-2-create-vm-image"></a>2. lépés: Virtuálisgép-rendszerkép létrehozása
Az Azure CLI használatával általánosítottként jelölheti meg a virtuális gépet, és rögzítheti a rendszerképet. Az alábbi példákban cserélje le a példaparaméter-neveket a saját értékeire. A paraméternevek közé tartozik például a *myResourceGroup,* *a myVnet* és a *myVM.*

1. Szabadította fel a megszüntetett virtuális gépet az [az vm deallocate sal.](/cli/azure/vm) Az alábbi példa felszabadítja a *myVM* nevű virtuális gépet a *myResourceGroup nevű erőforráscsoportban.*  
   
    ```azurecli
    az vm deallocate \
        --resource-group myResourceGroup \
        --name myVM
    ```
    
    A továbbköltözés előtt várja meg, amíg a virtuális gép teljesen fel lesz szabadodva. Ez eltarthat néhány percig.  A virtuális gép a deallocation során leáll.

2. Jelölje meg a virtuális gépet általánosítottként [az az vm generalize val.](/cli/azure/vm) Az alábbi példa a *myResourceGroup* nevű erőforráscsoportban található *myVM* nevű virtuális gépet általánosítottként jelöli meg.
   
    ```azurecli
    az vm generalize \
        --resource-group myResourceGroup \
        --name myVM
    ```

    Az általánosított virtuális gépeket a továbbiakban nem lehet újraindítani.

3. Hozzon létre egy rendszerképet a virtuálisgép-erőforrásról [az az image create segítségével.](/cli/azure/image#az_image_create) Az alábbi példa létrehoz egy *myImage* nevű rendszerképet a *myResourceGroup* nevű erőforráscsoportban a *myVM nevű virtuálisgép-erőforrás használatával.*
   
    ```azurecli
    az image create \
        --resource-group myResourceGroup \
        --name myImage --source myVM
    ```
   
   > [!NOTE]
   > A rendszerkép ugyanabban az erőforráscsoportban jön létre, mint a forrás virtuális gép. Ebből a rendszerképből az előfizetésében bármely erőforráscsoportban létrehozhat virtuális gépeket. Felügyeleti szempontból előfordulhat, hogy egy adott erőforráscsoportot szeretne létrehozni a virtuális gép erőforrásaihoz és rendszerképéhez.
   >
   > Ha a rendszerképet zónatitkos tárolóban szeretné tárolni, létre kell [](../../availability-zones/az-overview.md) hoznia egy olyan régióban, amely támogatja a rendelkezésre állási zónákat, és tartalmaznia kell a `--zone-resilient true` paramétert.
   
Ez a parancs a virtuálisgép-rendszerképet leíró JSON-t ad vissza. Mentse ezt a kimenetet későbbi referenciaként.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>3. lépés: Virtuális gép létrehozása a rögzített rendszerképből
Hozzon létre egy virtuális gépet az az vm create használatával [létrehozott rendszerkép használatával.](/cli/azure/vm) Az alábbi példa létrehoz egy *myVMDeployed* nevű virtuális gépet a *myImage nevű rendszerképből.*

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>A virtuális gép létrehozása egy másik erőforráscsoportban 

Az előfizetés bármely erőforráscsoportja rendszerképéből létrehozhat virtuális gépeket. Ha a rendszerképtől eltérő erőforráscsoportban hoz létre virtuális gépet, adja meg a rendszerkép teljes erőforrás-azonosítóját. Az [az image list](/cli/azure/image#az_image_list) használatával megtekintheti a képek listáját. A kimenet a következő példához hasonló.

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

Az alábbi példa a [rendszerkép](/cli/azure/vm#az_vm_create) erőforrás-azonosítójának megadásával az az vm create segítségével hoz létre egy virtuális gépet a forrásként használt rendszerképen kívül más erőforráscsoportban.

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>4. lépés: A telepítés ellenőrzése

SSH-kapcsolat a létrehozott virtuális géppel az üzembe helyezés ellenőrzéséhez és az új virtuális gép használatának elkezdéhez. Ha SSH-n keresztül szeretne csatlakozni, keresse meg a virtuális gép IP-címét vagy teljes tartományát [az az vm show kapcsolóval.](/cli/azure/vm#az_vm_show)

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>Következő lépések
A rendszerképek nagy léptékű létrehozásához, tárolására és megosztásához lásd: [Shared Image Galleries (Megosztott rendszerkép-katalógusok).](../shared-images-cli.md)
