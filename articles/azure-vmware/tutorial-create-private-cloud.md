---
title: Oktatóanyag – Magánfelhő Azure VMware Solution üzembe helyezése
description: Megtudhatja, hogyan hozhat létre és helyezhet Azure VMware Solution magánfelhőt
ms.topic: tutorial
ms.date: 02/22/2021
ms.openlocfilehash: ea4bf27a1ff14e4872bc2a0e19daa032dd4ba66d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870912"
---
# <a name="tutorial-deploy-an-azure-vmware-solution-private-cloud"></a>Oktatóanyag: Magánfelhő Azure VMware Solution üzembe helyezése

Azure VMware Solution vSphere-fürt üzembe helyezését teszi lehetővé az Azure-ban. A minimális kezdeti üzembe helyezés három gazdagép. Egyszerre csak egy gazdagépet lehet hozzáadni, fürtönként legfeljebb 16 gazdagépet.

Mivel Azure VMware Solution nem teszi lehetővé a magánfelhő kezelését a helyszíni vCenterben indításkor, további konfigurációra van szükség. Ezeket az eljárásokat és a kapcsolódó előfeltételeket ez az oktatóanyag is lefedi.

Ebből az oktatóanyagból az alábbiakat sajátíthatja el:

> [!div class="checklist"]
> * Magánfelhő Azure VMware Solution létrehozása
> * Az üzembe helyezett magánfelhő ellenőrzése

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Megfelelő rendszergazdai jogosultságok és engedélyek a magánfelhők létrehozásához. Legalább közreműködői szinten kell lennie az előfizetésben.
- Kövesse a tervezési cikkben összegyűjtött [információkat](production-ready-deployment-steps.md) a Azure VMware Solution.
- Győződjön meg arról, hogy a megfelelő hálózat konfigurálva van az [Oktatóanyag: Hálózati ellenőrzőlista.](tutorial-network-checklist.md)
- A gazdagépek üzembe vannak írva, és a Microsoft.AVS erőforrás-szolgáltató regisztrálva lett a Gazdagépek kérése és a [Microsoft.AVS erőforrás-szolgáltató engedélyezését tartalmazó cikk alapján.](enable-azure-vmware-solution.md)

## <a name="create-a-private-cloud"></a>Magánfelhő létrehozása

Magánfelhőt a Azure VMware Solution vagy az Azure CLI Azure Portal [használatával](#azure-portal) [hozhat létre.](#azure-cli)

### <a name="azure-portal"></a>Azure Portal

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-private-cloud-azure-portal-steps.md)]

### <a name="azure-cli"></a>Azure CLI

A Azure Portal helyett Azure VMware Solution azure cli-t használhatja az Azure Cloud Shell.  A virtuális gépekkel használható parancsok listájáért lásd: [Azure VMware Azure VMware Solution parancsok.](/cli/azure/vmware)

#### <a name="open-azure-cloud-shell"></a>Az Azure Cloud Shell megnyitása

A **kódblokk jobb** felső sarkában válassza a Kipróbálom lehetőséget. Az alkalmazásokat Cloud Shell böngészőlapon is elindíthatja a következő gombra: [https://shell.azure.com/bash](https://shell.azure.com/bash) . Kattintson **a Másolás** gombra a kódblokkok másoláshoz, illessze be Cloud Shell, majd nyomja le az **Enter** billentyűt a futtatáshoz.

#### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot [az "az group create" paranccsal.](/cli/azure/group) Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen:

```azurecli-interactive

az group create --name myResourceGroup --location eastus
```

#### <a name="create-a-private-cloud"></a>Magánfelhő létrehozása

Adja meg az erőforráscsoport és a magánfelhő nevét, a helyet és a fürt méretét.

| Tulajdonság  | Leírás  |
| --------- | ------------ |
| **-g** (erőforráscsoport neve)     | A magánfelhőbeli erőforrások erőforráscsoport-neve.        |
| **-n** (magánfelhő neve)     | A magánfelhő Azure VMware Solution neve.        |
| **--location**     | A magánfelhőhöz használt hely.         |
| **--cluster-size**     | A fürt mérete. A minimális érték 3.         |
| **--network-block**     | A magánfelhőhöz használt CIDR IP-cím hálózati blokk. A címblokk nem lehet átfedésben az előfizetésben és a helyszíni hálózatokban található más virtuális hálózatokban használt címblokkokkal.        |
| **--sku** | A termékváltozat értéke: AV36 |

```azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22 --sku AV36
```

## <a name="azure-vmware-commands"></a>Azure VMware-parancsok

A virtuális gépekkel használható parancsok listájáért lásd: [Azure VMware Azure VMware Solution parancsok.](/cli/azure/vmware)

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Magánfelhő Azure VMware Solution létrehozása
> * Az üzembe helyezett magánfelhő ellenőrzése
> * Magánfelhő Azure VMware Solution törlése

Folytassa a következő oktatóanyagot, amelyből megtudhatja, hogyan hozhat létre jump boxot. A jump box használatával csatlakozhat a környezetéhez, így helyileg kezelheti a magánfelhőt.


> [!div class="nextstepaction"]
> [Hozzáférés Azure VMware Solution magánfelhőhöz](tutorial-access-private-cloud.md)
