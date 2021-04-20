---
title: 'Rövid útmutató: Magas rendelkezésre állás beállítása a Azure Front Door – Azure CLI'
description: Ez a rövid útmutató bemutatja, hogyan hozhat Azure Front Door magas rendelkezésre állású és nagy teljesítményű globális webalkalmazásokat az Azure CLI használatával.
services: front-door
author: duongau
manager: KumudD
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/19/2021
ms.author: duau
ms.openlocfilehash: f697606e195f102d2bfb5535c92e5c78eb44cdbe
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727210"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application-using-azure-cli"></a>Rövid útmutató: Front Door létrehozása magas rendelkezésre álló globális webalkalmazáshoz az Azure CLI használatával

A Azure Front Door használatának első lépésekhez hozzon létre egy magas rendelkezésre álló és nagy teljesítményű globális webalkalmazást az Azure CLI használatával.

A Front Door a webes forgalmat a háttérkészlet adott erőforrásaihoz irányítja. Meghatározta az előteretartományt, erőforrásokat adott hozzá egy háttérkészlethez, és létrehozott egy útválasztási szabályt. Ez a cikk az egyik háttérkészlet egyszerű konfigurációját használja két webalkalmazás-erőforrással, valamint egyetlen útválasztási szabályt a "/*" alapértelmezett elérésiút-megfeleltetéssel.

:::image type="content" source="media/quickstart-create-front-door/environment-diagram.png" alt-text="Az üzembe Front Door Azure CLI használatával való üzembe helyezést szemléltető ábra." border="false":::

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Helyileg telepített Azure CLI vagy Azure Cloud Shell
- Győződjön meg arról, hogy a front-door bővítmény hozzá van adva az Azure CLI-hez

```azurecli-interactive 
az extension add --name front-door
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a CLI helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0.28-as vagy újabb verziójára lesz szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-ban a kapcsolódó erőforrásokat egy erőforráscsoporthoz rendeli hozzá. Használhat egy meglévő erőforráscsoportot, vagy létrehozhat egy újat.

Ehhez a rövid útmutatóhoz két erőforráscsoportra lesz szüksége. Az egyik *az USA középső részén,* a másik pedig az *USA déli középső részén található.*

Hozzon létre egy erőforráscsoportot [az az group create gombra:](/cli/azure/group#az-group-create)

```azurecli-interactive
az group create \
    --name myRGFDCentral \
    --location centralus

az group create \
    --name myRGFDEast \
    --location eastus
```

## <a name="create-two-instances-of-a-web-app"></a>Webalkalmazás két példányának létrehozása

A rövid útmutatóhoz egy webalkalmazás két példánya szükséges, amelyek különböző Azure-régiókban futnak. Mindkét webalkalmazáspéldány aktív/aktív módban fut, így bármelyik képes a forgalom kiszolgálására.

Ha még nem rendelkezik webalkalmazással, a következő szkript használatával állítson be két példa webalkalmazást.

### <a name="create-app-service-plans"></a>App Service-csomagok létrehozása

A webalkalmazások létrehozása előtt két App Service-csomagra lesz szüksége, amelyek közül az egyik az *USA* középső régiója, a másik pedig az USA keleti *régiója.*

App Service-csomagok létrehozása az [az appservice plan create használatával:](/cli/azure/appservice/plan#az_appservice_plan_create&preserve-view=true)

```azurecli-interactive
az appservice plan create \
--name myAppServicePlanCentralUS \
--resource-group myRGFDCentral

az appservice plan create \
--name myAppServicePlanEastUS \
--resource-group myRGFDEast
```

### <a name="create-web-apps"></a>Webalkalmazások létrehozása

Az alábbi parancsok futtatásával egy webalkalmazást hozhat létre az előző lépésben található összes App Service-csomagban. A webalkalmazások nevének globálisan egyedinek kell lennie.

Webalkalmazás létrehozása az [az webapp create használatával:](/cli/azure/webapp#az_webapp_create&preserve-view=true)

```azurecli-interactive
az webapp create \
--name WebAppContoso-1 \
--resource-group myRGFDCentral \
--plan myAppServicePlanCentralUS 

az webapp create \
--name WebAppContoso-2 \
--resource-group myRGFDEast \
--plan myAppServicePlanEastUS
```

Jegyezze fel az egyes webalkalmazások alapértelmezett gazdanevét, hogy a következő lépésben a háttércímeket Front Door üzembe helyezésekor.

## <a name="create-the-front-door"></a>A Front Door

Hozzon létre egy alapszintű Front Door alapértelmezett terheléselosztási beállításokkal, állapot-mintavétellel és útválasztási szabályokkal a következő futtatásával:

Hozzon Front Door [az az network front-door create segítségével:](/cli/azure/ext/front-door/network/front-door#ext_front_door_az_network_front_door_create&preserve-view=true)

```azurecli-interactive
az network front-door create \
--resource-group myRGFDCentral \
--name contoso-frontend \
--accepted-protocols http https \
--backend-address webappcontoso-1.azurewebsites.net webappcontoso-2.azurewebsites.net 
```

**--resource-group:** Adjon meg egy erőforráscsoportot, amelyben üzembe szeretné helyezni a Front Door.

**--name:** Adjon meg egy globálisan egyedi nevet a Azure Front Door. 

**--accepted-protocols:** Az elfogadott értékek a **http és a** **https.** Ha mindkettőt használni szeretné, mindkettőt szóköz választja el egymástól.

**--backend-address:** Itt definiálja mindkét webalkalmazás gazdanevét szóközvel elválasztva.

Miután az üzembe helyezés sikeresen befejeződött, jegyezze fel az állomásnevet a *frontEndpoints szakaszban.*

## <a name="test-the-front-door"></a>A Front Door

Nyisson meg egy webböngészőt, és írja be a parancsokból lekért gazdanevet. A Front Door a kérést az egyik háttérerőforráshoz irányítja.

:::image type="content" source="./media/quickstart-create-front-door-cli/front-door-testing-page.png" alt-text="Front Door tesztelési oldal":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a Front Door létrehozott erőforrásokra, törölje mindkét erőforráscsoportot. Az erőforráscsoport törlésekor törli a Front Door az összes kapcsolódó erőforrást is. 

Az erőforráscsoport törléséhez használja [az az group delete parancsot:](/cli/azure/group#az_group_delete&preserve-view=true)

```azurecli-interactive
az group delete \
--name myRGFDCentral 

az group delete \
--name myRGFDEast
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban a következőt hozta létre:
* Front Door
* Két webalkalmazás

Ha meg szeretne ismerkedni az egyéni tartomány hozzáadásának Front Door, folytassa a Front Door oktatóanyagokkal.

> [!div class="nextstepaction"]
> [Egyéni tartomány hozzáadása](front-door-custom-domain.md)
