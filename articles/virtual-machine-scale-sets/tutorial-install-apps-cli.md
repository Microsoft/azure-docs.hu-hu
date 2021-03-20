---
title: Oktatóanyag – alkalmazások telepítése egy méretezési csoportba az Azure CLI-vel
description: Megismerheti, hogyan telepíthet alkalmazásokat virtuálisgép-méretezési csoportokban az egyéni szkriptbővítménnyel és az Azure CLI-vel
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: cli
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: d9969cf0fa453f857de421dd10934f63f5773f6c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94516747"
---
# <a name="tutorial-install-applications-in-virtual-machine-scale-sets-with-the-azure-cli"></a>Oktatóanyag: Alkalmazások telepítése virtuálisgép-méretezési csoportokban az Azure CLI-vel
Ha alkalmazásokat szeretne futtatni egy méretezési csoport virtuálisgép-példányán, először telepítenie kell az alkalmazás összetevőit és szükséges fájljait. Egy korábbi oktatóanyagból megtudhatta, hogyan hozhat létre és használhat egyéni virtuálisgép-rendszerképeket a virtuálisgép-példányok üzembe helyezéséhez. Ez az egyéni rendszerkép tartalmazott manuális alkalmazástelepítéseket és -konfigurációkat. Az egyes virtuálisgép-példányok üzembe helyezése után lehetősége van az alkalmazások méretezési csoportokon történő telepítésének automatizálására, vagy egy a méretezési csoporton már futó alkalmazás frissítésére. Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Alkalmazások automatikus telepítése a méretezési csoporton
> * Az egyéni Azure-szkriptek bővítményének használata
> * Egy méretezési csoporton futtatott alkalmazás frissítése

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.29 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van. 


## <a name="what-is-the-azure-custom-script-extension"></a>Mi az egyéni Azure-szkriptek bővítménye?
Az egyéni szkriptek bővítménye szkripteket tölt le és futtat az Azure-beli virtuális gépeken. A bővítmény az üzembe helyezést követő konfiguráció, szoftvertelepítés, illetve bármely konfigurációs/felügyeleti feladat végrehajtása során hasznos. A szkriptek az Azure Storage-ből vagy a GitHubról tölthetők le, illetve megadhatók az Azure Portalon a bővítmény futásidejében.

Az egyéni szkriptek bővítménye az Azure Resource Manager-sablonokkal van integrálva, és az Azure CLI-vel, az Azure PowerShell-lel, az Azure Portallal vagy a REST API-val együtt is használható. További információ: [Az egyéni szkriptbővítmény áttekintése](../virtual-machines/extensions/custom-script-linux.md).

Az egyéni szkriptbővítmény Azure CLI-vel való használatához létre kell hoznia egy JSON-fájlt, amely meghatározza a lekérendő fájlokat és a végrehajtandó parancsokat. Ezek a JSON-definíciók újra felhasználhatók a méretezési csoportok üzembe helyezése során, így konzisztens alkalmazás-telepítés érhető el.


## <a name="create-custom-script-extension-definition"></a>Definíció létrehozása az egyéni szkriptek bővítményéhez
Az egyéni szkriptbővítmény működés közbeni megtekintéséhez hozzon létre egy méretezési csoportot, amely telepíti az NGINX-webkiszolgálót és visszaadja a méretezési csoport virtuálisgép-példányának gazdagépnevét. Az egyéni szkriptek bővítményének alábbi definíciója egy mintaszkriptet tölt le a GitHubról, telepíti a szükséges csomagokat, majd kiírja a virtuálisgép-példány gazdagépnevét egy alapszintű HTML-oldalra.

Az aktuális parancshéjban hozzon létre egy *customConfig.json* nevű fájlt, és illessze bele a következő konfigurációt. Például hozza létre a fájlt a Cloud Shellben, és ne a helyi gépén. Bármelyik szerkesztőt használhatja. Írja be a `sensible-editor customConfig.json` parancsot Cloud Shellbe a fájl létrehozásához és az elérhető szerkesztők listájának megtekintéséhez.

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"],
  "commandToExecute": "./automate_nginx.sh"
}
```

> [!CAUTION]
> Előfordulhat, hogy meg kell szüntetnie az egyszeres (") és az idézőjelek (") használatát a JSON-blokkon belül, ha úgy dönt, hogy közvetlenül hivatkozik a JSON-ra (a fájl *customConfig.jsára* nem hivatkozik) az alábbi *--Settings* paraméterben. 


## <a name="create-a-scale-set"></a>Méretezési csoport létrehozása
Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *eastus* helyen:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Most hozzon létre egy virtuálisgép-méretezési csoportot az [az vmss create](/cli/azure/vmss) paranccsal. A következő példa egy *myScaleSet* nevű méretezési csoportot, valamint SSH-kulcsokat hoz létre, amennyiben azok még nem léteznének:

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys
```

A méretezési csoport erőforrásainak és virtuális gépeinek létrehozása és konfigurálása néhány percet vesz igénybe.


## <a name="apply-the-custom-script-extension"></a>Az egyéni szkriptbővítmény alkalmazása
Alkalmazza az egyéni szkriptbővítmény konfigurációját a méretezési csoport virtuálisgép-példányain az [az vmss extension set](/cli/azure/vmss/extension) paranccsal. Az alábbi példa a *customConfig.json* konfigurációt alkalmazza a *myResourceGroup* nevű erőforráscsoportban található *myScaleSet* virtuálisgép-példányokon:

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings @customConfig.json
```

A méretezési csoport összes virtuálisgép-példánya a GitHubról tölti le és futtatja a szkriptet. Egy összetettebb példában több alkalmazás-összetevő és fájl is telepíthető. A méretezési csoport vertikális felskálázása esetén az új virtuálisgép-példányok automatikusan alkalmazzák az egyéni szkriptek bővítményének definícióját, és telepítik a szükséges alkalmazást.


## <a name="test-your-scale-set"></a>Méretezési csoport tesztelése
Annak érdekében, hogy a forgalom elérhesse a webkiszolgálót, hozzon létre egy terheléselosztó-szabályt az [az network lb rule create](/cli/azure/network/lb/rule) paranccsal. Az alábbi példa egy *myLoadBalancerRuleWeb* nevű szabályt hoz létre:

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

Ha működés közben szeretné megtekinteni a webkiszolgálót, kérje le a terheléselosztó nyilvános IP-címét az [az network public-ip show](/cli/azure/network/public-ip) paranccsal. A következő példa a *myScaleSetLBPublicIP* a méretezési csoport részeként létrehozott IP-címét kéri le:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query [ipAddress] \
  --output tsv
```

Adja meg a terheléselosztó nyilvános IP-címét egy webböngészőben. A terheléselosztó az egyik virtuálisgép-példányra terjeszti a forgalmat, ahogy az a következő példában látható:

![Alapszintű weboldal az NGINX-ben](media/tutorial-install-apps-cli/running-nginx.png)

Ne zárja be a böngészőt, hogy megtekinthesse a frissített verziót a következő lépés során.


## <a name="update-app-deployment"></a>Alkalmazástelepítés frissítése
A méretezési csoport életciklusa során előfordulhat, hogy telepítenie kell az alkalmazás frissített verzióját. Az egyéni szkriptek bővítményével hivatkozhat egy frissített telepítési szkriptre, majd alkalmazhatja ismét a bővítményt a méretezési csoporton. Ha a méretezési csoport egy korábbi lépésben lett létrehozva, a `--upgrade-policy-mode` beállítás értéke *automatikus*. Ez a beállítás lehetővé teszi a méretezési csoportban található virtuálisgép-példányok számára az automatikus frissítést és az alkalmazás legfrissebb verziójának alkalmazását.

Az aktuális parancshéjban hozzon létre egy *customConfigv2.json* nevű fájlt, és illessze bele a következő konfigurációt. A definíció az alkalmazástelepítési szkript frissített (*v2*) verzióját futtatja:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx_v2.sh"],
  "commandToExecute": "./automate_nginx_v2.sh"
}
```

Alkalmazza újra az egyéni szkriptbővítmény konfigurációját a méretezési csoport virtuálisgép-példányain az [az vmss extension set](/cli/azure/vmss/extension) paranccsal. A *customConfigv2.json* nevű sablon az alkalmazás frissített verziójának alkalmazására használatos:

```azurecli-interactive
az vmss extension set \
    --publisher Microsoft.Azure.Extensions \
    --version 2.0 \
    --name CustomScript \
    --resource-group myResourceGroup \
    --vmss-name myScaleSet \
    --settings @customConfigv2.json
```

A méretezési csoport összes virtuálisgép-példánya automatikusan frissül a minta weboldal legfrissebb verziójával. A frissített verzió megtekintéséhez frissítse a webhelyet a böngészőben:

![Frissített weboldal az NGINX-ben](media/tutorial-install-apps-cli/running-nginx-updated.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
A méretezési csoport és a további erőforrások eltávolításához törölje az erőforráscsoportot és a hozzá tartozó összes erőforrást az [az Group delete](/cli/azure/group)paranccsal. A `--no-wait` paraméter visszaadja a vezérlést a parancssornak, és nem várja meg a művelet befejeztét. A `--yes` paraméter megerősíti, hogy további kérdés nélkül szeretné törölni az erőforrásokat.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Következő lépések
Ez az oktatóanyag bemutatta, hogy telepíthet és frissíthet alkalmazásokat automatikusan a méretezési csoportban az Azure CLI használatával:

> [!div class="checklist"]
> * Alkalmazások automatikus telepítése a méretezési csoporton
> * Az egyéni Azure-szkriptek bővítményének használata
> * Egy méretezési csoporton futtatott alkalmazás frissítése

A következő oktatóanyag azt mutatja be, hogyan skálázhatja automatikusan a méretezési csoportját.

> [!div class="nextstepaction"]
> [Méretezési csoportok automatikus skálázása](tutorial-autoscale-cli.md)
