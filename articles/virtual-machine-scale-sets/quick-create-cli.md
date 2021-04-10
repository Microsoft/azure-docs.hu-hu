---
title: Rövid útmutató – virtuálisgép-méretezési csoport létrehozása az Azure CLI-vel
description: Ismerkedjen meg az üzemelő példányokkal az Azure CLI-vel való gyors virtuálisgép-méretezési csoport létrehozásával.
author: ju-shim
ms.author: jushiman
ms.topic: quickstart
ms.service: virtual-machine-scale-sets
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: d65df03bc23704fb4d4c037655cc4b84ccc4e576
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105934648"
---
# <a name="quickstart-create-a-virtual-machine-scale-set-with-the-azure-cli"></a>Rövid útmutató – Virtuálisgép-méretezési csoport létrehozása az Azure CLI használatával
A virtuálisgép-méretezési csoport lehetővé teszi az automatikus méretezésű virtuális gépek készletének üzembe helyezését és kezelését. A méretezési csoportban lévő virtuális gépek számát beállíthatja manuálisan, de automatikus méretezési szabályokat is megadhat az erőforrás-használat (például processzorhasználat, memóriaigény vagy hálózati forgalom) alapján. Egy Azure-terheléselosztó ezután elosztja a forgalmat a méretezési csoportban lévő virtuálisgép-példányok között. Ebben a rövid útmutatóban egy virtuálisgép-méretezési csoportot hozunk létre, és üzembe helyezünk egy mintaalkalmazást az Azure CLI használatával.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.29 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van. 


## <a name="create-a-scale-set"></a>Méretezési csoport létrehozása
Mielőtt létrehozhatna egy méretezési csoportot, létre kell hoznia egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *eastus* helyen:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Most hozzon létre egy virtuálisgép-méretezési csoportot az [az vmss create](/cli/azure/vmss) paranccsal. A következő példa egy *myScaleSet* nevű méretezési csoportot hoz létre, amely automatikusan frissül a módosítások alkalmazásakor, valamint SSH-kulcsokat hoz létre, amennyiben azok még nem léteznének a *~/.ssh/id_rsa* helyen. Ezek az SSH-kulcsok akkor használhatók, ha be kell jelentkeznie a virtuálisgép-példányokra. Az SSH-kulcsok meglévő készletének használata helyett használja az `--ssh-key-value` paramétert, és adja meg a kulcsok helyét.

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


## <a name="deploy-sample-application"></a>Mintaalkalmazás üzembe helyezése
A méretezési csoport teszteléséhez telepítsen egy alapszintű webalkalmazást. Az egyéni Azure-szkriptbővítmények használatával egy olyan szkript tölthető le és futtatható, amely egy alkalmazást telepít a virtuálisgép-példányokon. A bővítmény az üzembe helyezést követő konfiguráció, szoftvertelepítés, illetve bármely konfigurációs/felügyeleti feladat végrehajtása során hasznos. További információ: [Az egyéni szkriptbővítmény áttekintése](../virtual-machines/extensions/custom-script-linux.md).

Telepítsen egy alapszintű NGINX-webkiszolgálót az egyéni szkriptbővítménnyel. Alkalmazza az NGINX-et telepítő egyéni szkriptbővítményt az [az vmss extension set](/cli/azure/vmss/extension) paranccsal következő módon:

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"],"commandToExecute":"./automate_nginx.sh"}'
```


## <a name="allow-traffic-to-application"></a>Forgalom engedélyezése az alkalmazáshoz
A méretezési csoport létrehozásakor a rendszer automatikusan üzembe helyezett egy Azure Load Balancert. A terheléselosztó elosztja a forgalmat a méretezési csoportban lévő virtuálisgép-példányok között. Annak érdekében, hogy a forgalom elérhesse a mintául szolgáló webalkalmazást, hozzon létre egy terheléselosztó-szabályt az [az network lb rule create](/cli/azure/network/lb/rule) paranccsal. Az alábbi példa egy *myLoadBalancerRuleWeb* nevű szabályt hoz létre:

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


## <a name="test-your-scale-set"></a>Méretezési csoport tesztelése
Ha ellenőrizni szeretné, hogyan működik a méretezési csoport, lépjen egy böngészőben a mintául szolgáló webalkalmazáshoz. Kérje le a terheléselosztó nyilvános IP-címét az [az network public-ip show](/cli/azure/network/public-ip) paranccsal. A következő példa a *myScaleSetLBPublicIP* a méretezési csoport részeként létrehozott IP-címét kéri le:

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query '[ipAddress]' \
  --output tsv
```

Adja meg a terheléselosztó nyilvános IP-címét egy webböngészőben. A terheléselosztó az egyik virtuálisgép-példányra terjeszti a forgalmat, ahogy az a következő példában látható:

![Alapértelmezett weboldal az NGINX-ben](media/virtual-machine-scale-sets-create-cli/running-nginx-site.png)


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rájuk szükség, az [az group delete](/cli/azure/group) paranccsal eltávolítható az erőforráscsoport, a méretezési csoport és az összes kapcsolódó erőforrás. A `--no-wait` paraméter visszaadja a vezérlést a parancssornak, és nem várja meg a művelet befejeztét. A `--yes` paraméter megerősíti, hogy további kérdés nélkül szeretné törölni az erőforrásokat.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```


## <a name="next-steps"></a>Következő lépések
Ebben a rövid útmutatóban egy alapszintű méretezési csoportot hoztunk létre, valamint az egyéni szkriptbővítménnyel egy alapszintű NGINX-webkiszolgálót telepítettünk a VM-példányokon. Ha bővebb információra van szüksége, lépjen tovább az Azure-beli virtuálisgép-méretezési csoportok létrehozásáról és kezeléséről szóló oktatóanyagra.

> [!div class="nextstepaction"]
> [Azure-beli virtuálisgép-méretezési csoportok létrehozása és kezelése](tutorial-create-and-manage-cli.md)
