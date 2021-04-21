---
title: 'Rövid útmutató: Profil létrehozása a nagy elérésű alkalmazásokhoz – Azure CLI – Azure Traffic Manager'
description: Ez a rövid útmutató azt ismerteti, hogyan hozhat létre egy Traffic Manager-profilt magas rendelkezésre álló webalkalmazás létrehozásához az Azure CLI használatával.
services: traffic-manager
author: duongau
manager: kumud
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/19/2021
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: e4be2e887876f85e75df254fd6c6a19003ca8a07
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792488"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-cli"></a>Rövid útmutató: Traffic Manager profil létrehozása magas rendelkezésre áll rendelkezésre álló webalkalmazáshoz az Azure CLI használatával

Ez a rövid útmutató ismerteti, hogyan hozhat létre olyan Traffic Manager profilt, amely magas rendelkezésre állást biztosít a webalkalmazás számára.

Ebben a rövid útmutatóban egy webalkalmazás két példányát fogja létrehozni. Mindegyik másik Azure-régióban fut. A végpont prioritása Traffic Manager [létre.](traffic-manager-routing-methods.md#priority-traffic-routing-method) A profil a felhasználói forgalmat a webalkalmazást futtató elsődleges webhelyre irányítja. Traffic Manager folyamatosan figyeli a webalkalmazást. Ha az elsődleges hely nem érhető el, automatikus feladatátvételt biztosít a biztonsági mentési helynek.

:::image type="content" source="./media/quickstart-create-traffic-manager-profile/environment-diagram.png" alt-text="Az üzembehely Traffic Manager CLI használatával való üzembe helyezést szemléltető ábra." border="false":::

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.28-as vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Az alábbi példa létrehoz egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen:

```azurecli-interactive

  az group create \
    --name myResourceGroup \
    --location eastus

```

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager-profil létrehozása

Hozzon létre Traffic Manager [profilt az az network traffic-manager profile create](/cli/azure/network/traffic-manager/profile#az_network_traffic_manager_profile_create) parancs használatával, amely a felhasználói forgalmat a végpont prioritása alapján irányítja.

A következő példában cserélje le a **<profile_name>** egy egyedi Traffic Manager profilnévre.

```azurecli-interactive

az network traffic-manager profile create \
    --name <profile_name> \
    --resource-group myResourceGroup \
    --routing-method Priority \
    --path "/" \
    --protocol HTTP \
    --unique-dns-name <profile_name> \
    --ttl 30 \
    --port 80

```

## <a name="create-web-apps"></a>Webalkalmazások létrehozása

Ebben a rövid útmutatóban egy webalkalmazás két példányára lesz szüksége, amelyek két különböző Azure-régióban *(az* USA keleti régiójában és *Nyugat-Európában) üzembe helyezhetők.* Mindegyik elsődleges és feladatátvételi végpontként szolgál majd a Traffic Manager.

### <a name="create-web-app-service-plans"></a>Web App Service-csomagok létrehozása
Web App Service-csomagok létrehozása [az az appservice plan create](/cli/azure/appservice/plan#az_appservice_plan_create) használatával a két különböző Azure-régióban üzembe helyező webalkalmazás két példányához.

A következő példában cserélje le a **<appspname_eastus>** és **<appspname_westeurope>** egy egyedi App Service tervnévre

```azurecli-interactive

az appservice plan create \
    --name <appspname_eastus> \
    --resource-group myResourceGroup \
    --location eastus \
    --sku S1

az appservice plan create \
    --name <appspname_westeurope> \
    --resource-group myResourceGroup \
    --location westeurope \
    --sku S1

```

### <a name="create-a-web-app-in-the-app-service-plan"></a>Webalkalmazás létrehozása az App Service-csomagban
Hozzon létre két példányt a [webalkalmazáshoz az az webapp create](/cli/azure/webapp#az_webapp_create) App Service az *USA* keleti régiójában és Nyugat-Európában *Azure-régiókban.*

A következő példában cserélje le a **<app1name_eastus>** és az **<app2name_westeurope>** helyére egy egyedi alkalmazásnevet, az **<appspname_eastus>** és **az<appspname_westeurope>** helyére pedig az előző szakaszban az App Service-csomagok létrehozásához használt nevet.

```azurecli-interactive

az webapp create \
    --name <app1name_eastus> \
    --plan <appspname_eastus> \
    --resource-group myResourceGroup

az webapp create \
    --name <app2name_westeurope> \
    --plan <appspname_westeurope> \
    --resource-group myResourceGroup

```

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager-végpontok hozzáadása
Adja hozzá a két Web Apps végpontként Traffic Manager [az az network traffic-manager endpoint create](/cli/azure/network/traffic-manager/endpoint#az_network_traffic_manager_endpoint_create) használatával a Traffic Manager profilhoz az alábbiak szerint:

- Határozza meg a webalkalmazás azonosítóját, és adja hozzá az USA keleti *régiójában* található webalkalmazást elsődleges végpontként az összes felhasználói forgalom útválasztásához. 
- Határozza meg a webalkalmazás azonosítóját,  és adja hozzá a nyugat-európai Azure-régióban található webalkalmazást feladatátvételi végpontként. 

Ha az elsődleges végpont nem érhető el, a forgalom automatikusan a feladatátvételi végpontra kerül.

A következő példában cserélje le **a<app1name_eastus>** és **<app2name_westeurope>** az előző szakaszban az egyes régiókhoz létrehozott alkalmazásnevekre. Ezután cserélje **<profile_name>** helyére az előző szakaszban használt profilnevet. 

**USA keleti régiója végpont**

```azurecli-interactive

az webapp show \
    --name <app1name_eastus> \
    --resource-group myResourceGroup \
    --query id

```

Jegyezze fel a kimenetben megjelenő azonosítót, és adja hozzá a végpontot a következő parancsban:

```azurecli-interactive

az network traffic-manager endpoint create \
    --name <app1name_eastus> \
    --resource-group myResourceGroup \
    --profile-name <profile_name> \
    --type azureEndpoints \
    --target-resource-id <ID from az webapp show> \
    --priority 1 \
    --endpoint-status Enabled
```

**Nyugat-Európa végpont**

```azurecli-interactive

az webapp show \
    --name <app2name_westeurope> \
    --resource-group myResourceGroup \
    --query id

```

Jegyezze fel a kimenetben megjelenő azonosítót, és adja hozzá a végpontot a következő parancsban:

```azurecli-interactive

az network traffic-manager endpoint create \
    --name <app1name_westeurope> \
    --resource-group myResourceGroup \
    --profile-name <profile_name> \
    --type azureEndpoints \
    --target-resource-id <ID from az webapp show> \
    --priority 2 \
    --endpoint-status Enabled

```

## <a name="test-your-traffic-manager-profile"></a>A saját Traffic Manager tesztelése

Ebben a szakaszban a saját profilja tartománynevét Traffic Manager ellenőrizni. Emellett úgy konfigurálja az elsődleges végpontot, hogy az ne legyen elérhető. Végül láthatja, hogy a webalkalmazás továbbra is elérhető. Ennek az az oka, Traffic Manager elküldi a forgalmat a feladatátvételi végpontra.

A következő példában cserélje le **a<app1name_eastus>** és **<app2name_westeurope>** az előző szakaszban az egyes régiókhoz létrehozott alkalmazásnevekre. Ezután cserélje **<profile_name>** helyére az előző szakaszban használt profilnevet.

### <a name="determine-the-dns-name"></a>A DNS-név meghatározása

Állapítsa meg a Traffic Manager profil [DNS-nevét az az network traffic-manager profile show használatával.](/cli/azure/network/traffic-manager/profile#az_network_traffic_manager_profile_show)

```azurecli-interactive

az network traffic-manager profile show \
    --name <profile_name> \
    --resource-group myResourceGroup \
    --query dnsConfig.fqdn

```

Másolja ki **a RelativeDnsName értéket.** A profil DNS-Traffic Manager *http://<* relativednsname *>.trafficmanager.net.* 

### <a name="view-traffic-manager-in-action"></a>A Traffic Manager megtekintése működés közben
1. Egy webböngészőben adja meg a Traffic Manager-profil DNS-nevét *(http://<* relativednsname *>.trafficmanager.net*) a webalkalmazás alapértelmezett webhelyének megtekintéséhez.

    > [!NOTE]
    > Ebben a rövid útmutatóban minden kérés az elsődleges végpontra lesz irányítva. A prioritása **1.**
2. A feladatátvételi Traffic Manager megtekintéséhez tiltsa le az elsődleges helyet [az az network traffic-manager endpoint update használatával.](/cli/azure/network/traffic-manager/endpoint#az_network_traffic_manager_endpoint_update)

   ```azurecli-interactive

    az network traffic-manager endpoint update \
        --name <app1name_eastus> \
        --resource-group myResourceGroup \
        --profile-name <profile_name> \
        --type azureEndpoints \
        --endpoint-status Disabled
    
   ```

3. Másolja a saját Traffic Manager *(http://<* relativednsname *>.trafficmanager.net*) DNS-nevét a webhely új webböngésző-munkamenetben való megtekintéséhez.
4. Ellenőrizze, hogy a webalkalmazás továbbra is elérhető-e.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett, törölje az erőforráscsoportokat, a webalkalmazásokat és az összes kapcsolódó erőforrást [az az group delete parancs használatával.](/cli/azure/group#az_group_delete)

```azurecli-interactive

az group delete \
    --resource-group myResourceGroup

```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Traffic Manager, amely magas rendelkezésre állást biztosít a webalkalmazás számára. Ha többet szeretne megtudni a forgalom útválasztásról, folytassa a Traffic Manager oktatóanyagokkal.

> [!div class="nextstepaction"]
> [Traffic Manager-oktatóanyagok](tutorial-traffic-manager-improve-website-response.md)