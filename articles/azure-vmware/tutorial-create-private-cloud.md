---
title: Oktatóanyag – Azure VMware-megoldás saját Felhőbeli létrehozása és üzembe helyezése
description: Ismerje meg, hogyan hozhat létre és helyezhet üzembe egy Azure VMware-megoldást saját felhőben
ms.topic: tutorial
ms.date: 02/22/2021
ms.openlocfilehash: 2afd88bca05a9bcab309faff373bedf6a22e9f4b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101725424"
---
# <a name="tutorial-create-an-azure-vmware-solution-private-cloud"></a>Oktatóanyag: Azure VMware-megoldás – saját felhő létrehozása

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre és helyezhet üzembe egy Azure VMware-megoldás saját felhőjét. A gazdagépek minimális kezdeti telepítése három. A további gazdagépek egyenként is hozzáadhatók, legfeljebb 16 gazdagépre. 

Mivel az Azure VMware-megoldás nem teszi lehetővé, hogy az indításkor a helyszíni vCenter kezelhesse saját felhőjét, további konfigurálásra van szükség. Ezek az eljárások és a kapcsolódó előfeltételek az oktatóanyagban találhatók.

Ebből az oktatóanyagból az alábbiakat sajátíthatja el:

> [!div class="checklist"]
> * Azure VMware-megoldás saját felhő létrehozása
> * A privát felhő üzembe helyezésének ellenőrzése

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Megfelelő rendszergazdai jogosultságok és engedélyek a privát felhő létrehozásához. Az előfizetésben legalább közreműködő szinten kell lennie.
- Az Azure VMware-megoldás üzembe helyezéséhez kövesse a [tervezési](production-ready-deployment-steps.md) cikkben összegyűjtött információkat.
- Győződjön meg arról, hogy a megfelelő hálózatkezelés konfigurálva van az [oktatóanyag: hálózati ellenőrzőlista](tutorial-network-checklist.md)című témakörben leírtak szerint.
- A gazdagépek üzembe helyezése megtörtént, és a Microsoft. AVS erőforrás-szolgáltató regisztrálva van a [kérés gazdagépek és a Microsoft. AVS erőforrás-szolgáltató engedélyezése](enable-azure-vmware-solution.md)című témakörben leírtak szerint.

## <a name="create-a-private-cloud"></a>Magánfelhő létrehozása

A [Azure Portal](#azure-portal) vagy az [Azure parancssori](#azure-cli)felületének használatával létrehozhat egy Azure VMware-megoldáshoz tartozó saját felhőt is.

### <a name="azure-portal"></a>Azure Portal

[!INCLUDE [create-avs-private-cloud-azure-portal](includes/create-private-cloud-azure-portal-steps.md)]

### <a name="azure-cli"></a>Azure CLI

Ahelyett, hogy a Azure Portal egy Azure VMware-megoldáshoz tartozó privát felhőt létrehozni, az Azure CLI-t használhatja a Azure Cloud Shell használatával.  Az Azure VMware megoldással használható parancsok listáját itt találja: [Azure VMware-parancsok](/cli/azure/ext/vmware/vmware).

#### <a name="open-azure-cloud-shell"></a>Az Azure Cloud Shell megnyitása

Válassza a **kipróbálás** lehetőséget a kódrészlet jobb felső sarkában. A Cloud Shell egy külön böngészőablakban is elindíthatja [https://shell.azure.com/bash](https://shell.azure.com/bash) . Válassza a **Másolás** lehetőséget a kód blokkok másolásához, illessze be a Cloud Shellba, majd nyomja le az **ENTER** billentyűt a futtatásához.

#### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az `[az group create](/cli/azure/group)` paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *eastus* helyen:

```azurecli-interactive

az group create --name myResourceGroup --location eastus
```

#### <a name="create-a-private-cloud"></a>Magánfelhő létrehozása

Adja meg az erőforráscsoport nevét, a saját felhőt, a helyet és a fürt méretét.

| Tulajdonság  | Leírás  |
| --------- | ------------ |
| **-g** (erőforráscsoport neve)     | A saját felhőalapú erőforrásaihoz tartozó erőforráscsoport neve.        |
| **-n** (saját felhő neve)     | Az Azure VMware-megoldás saját Felhőbeli neve.        |
| **--hely**     | A saját felhőhöz használt hely.         |
| **– fürt mérete**     | A fürt mérete. A minimális érték 3.         |
| **--Network-Block**     | A CIDR IP-cím hálózati blokkja, amelyet a saját felhőhöz használ. A Címterület nem fedi át az előfizetésben és a helyszíni hálózatokban lévő más virtuális hálózatokban használt címeket.        |
| **--SKU** | Az SKU értéke: AV36 |

```azurecli-interactive
az vmware private-cloud create -g myResourceGroup -n myPrivateCloudName --location eastus --cluster-size 3 --network-block xx.xx.xx.xx/22 --sku AV36
```

## <a name="azure-vmware-commands"></a>Azure VMware-parancsok

Az Azure VMware megoldással használható parancsok listáját itt találja: [Azure VMware-parancsok](/cli/azure/ext/vmware/vmware).

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Azure VMware-megoldás saját felhő létrehozása
> * A privát felhő üzembe helyezésének ellenőrzése
> * Azure VMware-megoldás saját felhő törlése

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan hozhat létre egy Jump Box-t. A Jump Box használatával csatlakozhat a környezethez, hogy helyileg kezelhesse a saját felhőjét.


> [!div class="nextstepaction"]
> [Hozzáférés egy Azure VMware-megoldáshoz – saját felhő](tutorial-access-private-cloud.md)