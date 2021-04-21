---
title: Virtuális gépek üzembe helyezése rendelkezésre állási készletben az Azure CLI használatával
description: Megtudhatja, hogyan helyezhet üzembe magas rendelkezésre állású virtuális gépeket a rendelkezésre állási készletekben az Azure CLI használatával
documentationcenter: ''
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 3/8/2021
ms.author: mimckitt
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 21f7d3e6f624c3a8e550a33293b46284bf5c5815
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816295"
---
# <a name="create-and-deploy-virtual-machines-in-an-availability-set-using-azure-cli"></a>Virtuális gépek létrehozása és üzembe helyezése rendelkezésre állási csoportban az Azure CLI használatával

Ebben az oktatóanyagban megtanulhatja, hogyan növelheti Azure-beli virtuálisgép-megoldásai rendelkezésre állását és megbízhatóságát a rendelkezésre állási csoportok elnevezésű képesség használatával. A rendelkezésre állási csoportok biztosítják, hogy az Azure-ban üzembe helyezett virtuális gépek több elkülönített hardverfürt között legyenek elosztva. Ezáltal biztosítható, hogy ha hardveres vagy Azure-beli szoftveres hiba fordul elő, az a virtuális gépeknek csak egy részhalmazát érintse, és a teljes megoldás továbbra is elérhető és működőképes maradjon.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Rendelkezésre állási csoport létrehozása
> * Virtuális gép létrehozása rendelkezésre állási csoportban
> * Elérhető virtuálisgép-méretek ellenőrzése

Ez az oktatóanyag a cli-t használja [a Azure Cloud Shell,](../../cloud-shell/overview.md)amely folyamatosan frissül a legújabb verzióra. A kód Cloud Shell válassza a **Kipróbálom** lehetőséget bármely kódblokk tetején.

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.30-as vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése]( /cli/azure/install-azure-cli).

## <a name="create-an-availability-set"></a>Rendelkezésre állási csoport létrehozása

Rendelkezésre állási csoportot az [az vm availability-set create](/cli/azure/vm/availability-set) paranccsal hozhat létre. Ebben a példában a frissítési és a tartalék tartományok számát egyaránt *2*-re állítjuk a *myResourceGroupAvailability* erőforráscsoport *myAvailabilitySet* nevű rendelkezésre állási csoportjánál.

Először is hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) parancsmaggal, majd hozza létre a rendelkezésre állási csoportot:

```azurecli-interactive
az group create --name myResourceGroupAvailability --location eastus

az vm availability-set create \
    --resource-group myResourceGroupAvailability \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

A rendelkezésre állási csoportok segítségével elkülönítheti az erőforrásokat a tartalék tartományok és a frissítési tartományok között. A **tartalék tartomány** kiszolgáló-, hálózati és tárolási erőforrások egy elkülönített gyűjteményét képviseli. Az előző példában a rendelkezésre állási csoportot legalább két tartalék tartományon van elosztva a virtuális gépek üzembe helyezésekor. A rendelkezésre állási csoport két **frissítés tartományon** is el van osztva. A két frissítési tartomány biztosítja, hogy amikor az Azure frissíti a szoftvereket, a virtuális gépeink erőforrásai el legyenek különítve. Ezzel megakadályozhatjuk, hogy minden, a virtuális gépen futó szoftver egyszerre frissüljön.


## <a name="create-vms-inside-an-availability-set"></a>Virtuális gépek létrehozása rendelkezésre állási csoportban

A virtuális gépeket a rendelkezésre állási csoportban kell létrehozni, hogy azok biztosan megfelelően legyenek elosztva a hardveren. Meglévő virtuális gépet nem adhat hozzá rendelkezésre állási csoporthoz annak létrejötte után.

Ha az [az vm create](/cli/azure/vm) paranccsal hoz létre virtuális gépet, az `--availability-set` paraméterrel adhatja meg a rendelkezésre állási csoport nevét.

```azurecli-interactive
for i in `seq 1 2`; do
   az vm create \
     --resource-group myResourceGroupAvailability \
     --name myVM$i \
     --availability-set myAvailabilitySet \
     --size Standard_DS1_v2  \
     --vnet-name myVnet \
     --subnet mySubnet \
     --image UbuntuLTS \
     --admin-username azureuser \
     --generate-ssh-keys
done
```

Most már két virtuális gép van a rendelkezésre állási csoportban. Minthogy ugyanabban a rendelkezésre állási csoportban vannak, az Azure biztosítja, hogy a virtuális gépek és az erőforrásaik (az adatlemezeket is beleértve) el legyenek osztva az elkülönített fizikai hardverek között. Ez az eloszlás magasabb rendelkezésre állást biztosít a teljes virtuálisgép-megoldás számára.

A rendelkezésre állási csoport eloszlását megtekintheti a Portalon az Erőforráscsoportok > myResourceGroupAvailability > myAvailabilitySet területen. A virtuális gépek a két tartalék és frissítési tartomány között oszlanak el az alábbi példában látható módon:

![Rendelkezésre állási csoport a portálon](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Elérhető virtuálisgép-méretek ellenőrzése

Később további virtuális gépeket is hozzá lehet adni a rendelkezésre állási csoporthoz, ahol a virtuális gépek mérete rendelkezésre áll a hardveren. Használja [az az vm availability-set list-sizes](/cli/azure/vm/availability-set#az_vm_availability_set_list_sizes) et a rendelkezésre állási csoport hardverfürtön elérhető méretének felsorolásához:

```azurecli-interactive
az vm availability-set list-sizes \
     --resource-group myResourceGroupAvailability \
     --name myAvailabilitySet \
     --output table
```

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Rendelkezésre állási csoport létrehozása
> * Virtuális gép létrehozása rendelkezésre állási csoportban
> * Elérhető virtuálisgép-méretek ellenőrzése

Folytassa a következő oktatóanyaggal, amely a virtuálisgép-méretezési csoportokat ismerteti.

> [!div class="nextstepaction"]
> [Virtuálisgép-méretezési csoport létrehozása](tutorial-create-vmss.md)

* A rendelkezésre állási zónákkal kapcsolatos további információkért olvassa el a Availability Zones [dokumentációját.](../../availability-zones/az-overview.md)
* A rendelkezésre állási csoportokkal és a rendelkezésre állási zónákkal kapcsolatos további dokumentáció itt is [elérhető.](../availability.md)
* A rendelkezésre állási zónák kipróbálása: Linux rendszerű virtuális gép létrehozása rendelkezésre állási [zónában az Azure CLI használatával](./create-cli-availability-zone.md)