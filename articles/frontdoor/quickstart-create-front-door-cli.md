---
title: 'Gyors útmutató: magas rendelkezésre állás beállítása az Azure bejárati ajtajával – Azure CLI'
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre magas rendelkezésre állású és nagy teljesítményű globális webalkalmazást az Azure CLI használatával az Azure bejárati ajtó használatával.
services: front-door
author: duongau
manager: KumudD
ms.assetid: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/21/2020
ms.author: duau
ms.openlocfilehash: a64c91910ba65901a6d1374df9633062398a90e4
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106067654"
---
# <a name="quickstart-create-a-front-door-for-a-highly-available-global-web-application-using-azure-cli"></a>Rövid útmutató: a globális webalkalmazások Azure CLI-vel való létrehozásának első lépései

A magas rendelkezésre állású és nagy teljesítményű globális webalkalmazások létrehozásához az Azure CLI használatával megismerheti az Azure bejárati ajtót.

A bejárati ajtó a webes forgalmat egy háttér-készlet adott erőforrásaira irányítja. Meghatározta a felületi tartományt, erőforrásokat adhat hozzá egy háttér-készlethez, és létrehozhat egy útválasztási szabályt. Ez a cikk egy háttér-készlet egyszerű konfigurációját használja két webalkalmazás-erőforrással és egyetlen útválasztási szabállyal, amely az alapértelmezett elérési utat használja a "/*" értékkel.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Helyileg telepített Azure CLI vagy Azure Cloud Shell
- Győződjön meg arról, hogy az előtérben lévő bővítmény hozzá van adva az Azure CLI-hez

```azurecli-interactive 
az extension add --name front-door
```

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez a rövid útmutatóhoz az Azure CLI 2.0.28 verziójára vagy újabb verziójára van szükség. A verzió megkereséséhez futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető cikket.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-ban kapcsolódó erőforrásokat oszt ki egy erőforráscsoporthoz. Használhat meglévő erőforráscsoportot, vagy létrehozhat egy újat.

Ebben a rövid útmutatóban két erőforráscsoport szükséges. Egy az *USA középső* régiójában, a második pedig az *USA déli középső* régiójában.

Hozzon létre egy erőforráscsoportot az [az Group Create](/cli/azure/group#az-group-create)paranccsal:

```azurecli-interactive
az group create \
    --name myRGFDCentral \
    --location centralus

az group create \
    --name myRGFDSouthCentral \
    --location southcentralus
```

## <a name="create-two-instances-of-a-web-app"></a>Webalkalmazás két példányának létrehozása

Ehhez a rövid útmutatóhoz két, különböző Azure-régiókban futó webalkalmazás-példányra van szükség. Mind a webalkalmazás-példányok aktív/aktív módban futnak, így akár egy is a szolgáltatás forgalmát is.

Ha még nem rendelkezik webalkalmazással, a következő parancsfájl használatával állítson be két példa webalkalmazást.

### <a name="create-app-service-plans"></a>App Service-csomagok létrehozása

A webalkalmazások létrehozása előtt két app Service-csomagra lesz szüksége, egyet az *USA középső* régiójában, a második pedig az *USA déli középső* régiójában.

App Service-csomagok létrehozása az [az appservice Plan Create](/cli/azure/appservice/plan#az_appservice_plan_create&preserve-view=true)paranccsal:

```azurecli-interactive
az appservice plan create \
--name myAppServicePlanCentralUS \
--resource-group myRGFDCentral

az appservice plan create \
--name myAppServicePlanSouthCentralUS \
--resource-group myRGFDSouthCentral
```

### <a name="create-web-apps"></a>Webalkalmazások létrehozása

Az alábbi parancsok futtatásával egy webalkalmazás jön létre az előző lépésben az App Service-csomagokban. A webalkalmazások nevének globálisan egyedinek kell lennie.

Webalkalmazás létrehozása az [az WebApp Create](/cli/azure/webapp#az_webapp_create&preserve-view=true):

```azurecli-interactive
az webapp create \
--name WebAppContoso1 \
--resource-group myRGFDCentral \
--plan myAppServicePlanCentralUS 

az webapp create \
--name WebAppContoso2 \
--resource-group myRGFDSouthCentral \
--plan myAppServicePlanSouthCentralUS
```

Jegyezze fel az egyes webalkalmazások alapértelmezett állomásnevét, hogy megadhatja a háttérbeli címeket, amikor a következő lépésben üzembe helyezi a bejárati ajtót.

## <a name="create-the-front-door"></a>A bejárati ajtó létrehozása

A következő lépésekkel hozhat létre alapszintű bejárati ajtót alapértelmezett terheléselosztási beállításokkal, állapot-mintavételi és útválasztási szabályokkal:

Bejárati ajtó létrehozása az [az Network](/cli/azure/ext/front-door/network/front-door#ext_front_door_az_network_front_door_create&preserve-view=true)előtérben Create:

```azurecli-interactive
az network front-door create \
--resource-group myRGFDCentral \
--name contoso-frontend \
--accepted-protocols http https \
--backend-address webappcontoso1.azurewebsites.net webappcontoso2.azurewebsites.net 
```

**--Resource-Group:** Itt adhatja meg azt az erőforráscsoportot, amelyben a bejárati ajtót telepíteni szeretné.

**--Name:** Adjon meg egy globálisan egyedi nevet az Azure-előtérben. 

**--elfogadva-protokollok:** Az elfogadott értékek a **http** és a **https**. Ha mindkettőt szeretné használni, mindkettőt szóközzel kell elválasztani.

**--háttér-címe:** Adja meg a webalkalmazások állomásnevét is egy szóközzel elválasztva.

Ha az üzembe helyezés sikeresen befejeződött, jegyezze fel az állomásnevet a *frontEndpoints* szakaszban.

## <a name="test-the-front-door"></a>A bejárati ajtó tesztelése

Nyisson meg egy webböngészőt, és adja meg az állomásnév beszerzését a parancsokból. A bejárati ajtó irányítja a kérést az egyik háttérbeli erőforráshoz.

:::image type="content" source="./media/quickstart-create-front-door-cli/front-door-testing-page.png" alt-text="A bejárati ajtó tesztelésének lapja":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a bejárati ajtóval létrehozott erőforrásokra, törölje mindkét erőforráscsoportot. Az erőforráscsoport törlésekor a bejárati ajtót és az ahhoz kapcsolódó összes erőforrást is törli. 

Az erőforráscsoport törléséhez használja az [az Group delete](/cli/azure/group#az_group_delete&preserve-view=true):

```azurecli-interactive
az group delete \
--name myRGFDCentral 

az group delete \
--name myRGFDSouthCentral
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozta a következőket:
* Front Door
* Két webalkalmazás

Ha szeretné megtudni, hogyan adhat hozzá egyéni tartományt a bejárati ajtóhoz, folytassa az előtérben lévő oktatóanyagokkal.

> [!div class="nextstepaction"]
> [Egyéni tartomány hozzáadása](front-door-custom-domain.md)
