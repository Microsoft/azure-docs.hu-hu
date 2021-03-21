---
title: Oktatóanyag – alkalmazások telepítése egy méretezési csoportba Azure-sablonokkal
description: Megismerheti, hogyan telepíthet alkalmazásokat virtuálisgép-méretezési csoportokban az egyéni szkriptbővítménnyel és Azure Resource Manager-sablonok használatával
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: template
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: d5eba5486e7d26e62379e0112cd4b95322e6dae1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97705234"
---
# <a name="tutorial-install-applications-in-virtual-machine-scale-sets-with-an-azure-template"></a>Oktatóanyag: Alkalmazások telepítése virtuálisgép-méretezési csoportokban Azure-sablonnal
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

Az egyéni szkriptbővítmény működés közbeni megtekintéséhez hozzon létre egy méretezési csoportot, amely telepíti az NGINX-webkiszolgálót és visszaadja a méretezési csoport virtuálisgép-példányának gazdagépnevét. Az egyéni szkriptek bővítményének alábbi definíciója egy mintaszkriptet tölt le a GitHubról, telepíti a szükséges csomagokat, majd kiírja a virtuálisgép-példány gazdagépnevét egy alapszintű HTML-oldalra.


## <a name="create-custom-script-extension-definition"></a>Definíció létrehozása az egyéni szkriptek bővítményéhez
Amikor Azure-sablonnal határoz meg egy virtuálisgép-méretezési csoportot, a *Microsoft.Compute/virtualMachineScaleSets* erőforrás-szolgáltató tartalmazhat egy a bővítményekre vonatkozó szakaszt is. Az *extensionsProfile* paraméter megadja a méretezési csoport virtuálisgép-példányain alkalmazandó bővítményeket. Az egyéni szkriptbővítmény használatához adja meg kiadóként a *Microsoft.Azure.Extensions*, típusnak pedig a *CustomScript* értéket.

A *fileUris* tulajdonság használatával meghatározhatók a forrásként szolgáló telepítési szkriptek vagy csomagok. A telepítési folyamat elindításához szükséges szkriptek a *commandToExecute* mezőben vannak meghatározva. Az alábbi példa egy mintaszkriptet határoz meg a GitHubról, amely telepíti és konfigurálja az NGINX-webkiszolgálót:

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "AppInstall",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"
          ],
          "commandToExecute": "bash automate_nginx.sh"
        }
      }
    }
  ]
}
```

A méretezési csoport és az egyéni szkriptek bővítményét üzembe helyező Azure-sablonok teljes példáját a következő témakörben tekintheti meg: [https://github.com/Azure-Samples/compute-automation-configurations/blob/master/scale_sets/azuredeploy.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/scale_sets/azuredeploy.json) . A következő szakaszban ezt a mintasablont fogjuk használni.


## <a name="create-a-scale-set"></a>Méretezési csoport létrehozása
A mintasablon használatával hozzunk létre egy méretezési csoportot, és alkalmazzuk az egyéni szkriptbővítményt. Először hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *eastus* helyen:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Most hozzon létre egy virtuálisgép-méretezési [csoportot az az Deployment Group Create](/cli/azure/deployment/group)paranccsal. Amikor a rendszer kéri, adja meg az egyes virtuálisgép-példányhoz hitelesítő adatként használt saját felhasználó nevét és jelszavát:

```azurecli-interactive
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/azuredeploy.json
```

A méretezési csoport erőforrásainak és virtuális gépeinek létrehozása és konfigurálása néhány percet vesz igénybe.

A méretezési csoport összes virtuálisgép-példánya a GitHubról tölti le és futtatja a szkriptet. Egy összetettebb példában több alkalmazás-összetevő és fájl is telepíthető. A méretezési csoport vertikális felskálázása esetén az új virtuálisgép-példányok automatikusan alkalmazzák az egyéni szkriptek bővítményének definícióját, és telepítik a szükséges alkalmazást.


## <a name="test-your-scale-set"></a>Méretezési csoport tesztelése
Ha működés közben szeretné megtekinteni a webkiszolgálót, kérje le a terheléselosztó nyilvános IP-címét az [az network public-ip show](/cli/azure/network/public-ip) paranccsal. A következő példa a *myScaleSetPublicIP* méretezési csoport részeként létrehozott IP-címét kéri le:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetPublicIP \
  --query [ipAddress] \
  --output tsv
```

Adja meg a terheléselosztó nyilvános IP-címét egy webböngészőben. A terheléselosztó az egyik virtuálisgép-példányra terjeszti a forgalmat, ahogy az a következő példában látható:

![Alapszintű weboldal az NGINX-ben](media/tutorial-install-apps-template/running-nginx.png)

Ne zárja be a böngészőt, hogy megtekinthesse a frissített verziót a következő lépés során.


## <a name="update-app-deployment"></a>Alkalmazástelepítés frissítése
A méretezési csoport életciklusa során előfordulhat, hogy telepítenie kell az alkalmazás frissített verzióját. Az egyéni szkriptek bővítményével hivatkozhat egy frissített telepítési szkriptre, majd alkalmazhatja ismét a bővítményt a méretezési csoporton. Ha a méretezési csoport egy korábbi lépésben lett létrehozva, a *UpgradePolicy* *automatikus* értékre lett állítva. Ez a beállítás lehetővé teszi a méretezési csoportban található virtuálisgép-példányok számára az automatikus frissítést és az alkalmazás legfrissebb verziójának alkalmazását.

Az egyéni szkriptbővítmény definíciójának frissítéséhez módosítsa a sablont úgy, hogy az egy új telepítési szkriptre hivatkozzon. Ahhoz, hogy az egyéni szkriptbővítmény felismerje a módosítást, új fájlnevet kell használnia. Az egyéni szkriptbővítmény nem vizsgálja át a szkript tartalmát, hogy történt-e benne változás. Az alábbi definíció egy frissített telepítési szkriptet használt, amelynek a neve ki lett egészítve a *_v2* kifejezéssel:

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "AppInstall",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx_v2.sh"
          ],
          "commandToExecute": "bash automate_nginx_v2.sh"
        }
      }
    }
  ]
}
```

Alkalmazza az egyéni szkriptek bővítményének konfigurációját a méretezési csoportban lévő virtuálisgép-példányokra az [az Deployment Group Create](/cli/azure/deployment/group)paranccsal. Ez az *azuredeployv2.json* nevű sablon az alkalmazás frissített verziójának alkalmazására használatos. Gyakorlatban módosítania kell a létező *azuredeploy.json* sablont úgy, hogy az a frissített telepítési szkriptre hivatkozzon, ahogy az az előző szakaszban is látható. Amikor a rendszer kéri, adja meg a méretezési csoport első létrehozásakor használt felhasználónevet és jelszót:

```azurecli-interactive
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/azuredeploy_v2.json
```

A méretezési csoport összes virtuálisgép-példánya automatikusan frissül a minta weboldal legfrissebb verziójával. A frissített verzió megtekintéséhez frissítse a webhelyet a böngészőben:

![Frissített weboldal az NGINX-ben](media/tutorial-install-apps-template/running-nginx-updated.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
A méretezési csoport és a további erőforrások eltávolításához törölje az erőforráscsoportot és a hozzá tartozó összes erőforrást az [az Group delete](/cli/azure/group)paranccsal. A `--no-wait` paraméter visszaadja a vezérlést a parancssornak, és nem várja meg a művelet befejeztét. A `--yes` paraméter megerősíti, hogy további kérdés nélkül szeretné törölni az erőforrásokat.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>Következő lépések
Ez az oktatóanyag bemutatta, hogy telepíthet és frissíthet alkalmazásokat automatikusan a méretezési csoportban Azure-sablonok segítségével:

> [!div class="checklist"]
> * Alkalmazások automatikus telepítése a méretezési csoporton
> * Az egyéni Azure-szkriptek bővítményének használata
> * Egy méretezési csoporton futtatott alkalmazás frissítése

A következő oktatóanyag azt mutatja be, hogyan skálázhatja automatikusan a méretezési csoportját.

> [!div class="nextstepaction"]
> [Méretezési csoportok automatikus skálázása](tutorial-autoscale-template.md)
