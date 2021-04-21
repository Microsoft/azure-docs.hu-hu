---
title: Zip-leküldéses üzembe helyezés Azure Functions
description: A Kudu telepítési szolgáltatás .zip fájltelepítési létesítményei segítségével közzéteheti a Azure Functions.
ms.topic: conceptual
ms.date: 08/12/2018
ms.openlocfilehash: fb6867d7719f9650acb00f80ac3a933713ce0e23
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777650"
---
# <a name="zip-deployment-for-azure-functions"></a>Azure Functions üzembe helyezése ZIP-fájlokkal

Ez a cikk azt ismerteti, hogyan helyezheti üzembe a függvényalkalmazás projektfájlját az Azure-ban egy .zip -fájlból (tömörített). Megtudhatja, hogyan kell leküldéses üzembe helyezést az Azure CLI és a REST API-k használatával is. [Azure Functions Core Tools](functions-run-local.md) ezeket az üzembe helyezési API-kat is használja, amikor közzéten egy helyi projektet az Azure-ban.

Azure Functions a szolgáltatás által biztosított folyamatos üzembe helyezési és integrációs lehetőségek Azure App Service. További információ: Folyamatos üzembe [helyezés a Azure Functions.](functions-continuous-deployment.md)

A fejlesztés felgyorsítása érdekében könnyebben helyezheti üzembe a függvényalkalmazás projektfájlját közvetlenül egy .zip-fájlból. A .zip üzembe helyezési API egy .zip-fájl tartalmát kibontja a függvényalkalmazás `wwwroot` mappájába. Ez a .zip-fájltelepítés ugyanazt a Kudu-szolgáltatást használja, amely folyamatos integrációs alapú üzembe helyezéseket biztosít, beleértve a következőket:

+ A korábbi üzembe helyezésekből megmaradt fájlok törlése.
+ Az üzembe helyezés testreszabása, beleértve az üzembe helyezési szkriptek futtatását.
+ Üzembe helyezési naplók.
+ A függvényszinkronizálási eseményindítók a [használatban van a csomag](functions-scale.md) függvényalkalmazásában.

További információkért tekintse meg a [.zip üzembe helyezési referenciát.](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)

## <a name="deployment-zip-file-requirements"></a>Az üzembe helyezés .zip-fájlra vonatkozó követelményei

A leküldéses üzembe helyezéshez használt .zip-fájlnak tartalmaznia kell a függvény futtatásához szükséges összes fájlt.

>[!IMPORTANT]
> .zip üzemelő példány használata esetén a .zip fájlban nem található meglévő üzemelő példány fájljai törlődnek a függvényalkalmazásból.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

A függvényalkalmazás a könyvtár összes fájlját és mappáját `wwwroot` tartalmazza. A .zip-fájlok telepítése magában foglalja a könyvtár tartalmát, magát a `wwwroot` könyvtárat azonban nem. C#-osztálytárprojekt telepítésekor a lefordított kódtárfájlokat és függőségeket a .zip csomag egyik almappába `bin` kell foglalnia.

## <a name="download-your-function-app-files"></a>A függvényalkalmazás fájljainak letöltése

Ha helyi számítógépen fejleszt, egyszerűen létrehozhatja a függvényalkalmazás projektmappjának .zip-fájlját a fejlesztői számítógépen.

Előfordulhat azonban, hogy a függvényeket a szerkesztővel hozta létre a Azure Portal. A következő módokon tölthet le egy meglévő függvényalkalmazás-projektet:

+ **A következő Azure Portal:**

  1. Jelentkezzen be a [Azure Portal,](https://portal.azure.com)majd a függvényalkalmazáshoz.

  2. Az Áttekintés **lapon** válassza az **Alkalmazás tartalmának letöltése lehetőséget.** Válassza ki a letöltési lehetőségeket, majd válassza a **Letöltés lehetőséget.**

      ![A függvényalkalmazás-projekt letöltése](./media/deployment-zip-push/download-project.png)

     A letöltött .zip fájl formátuma megfelelő ahhoz, hogy .zip leküldéses üzembe helyezéssel újra közzéte legyen közzéteve a függvényalkalmazásban. A portál letöltésével a függvényalkalmazás közvetlenül a portálon való megnyitásához szükséges fájlokat is Visual Studio.

+ **REST API-k használata:**

    A következő üzembe helyezési GET API használatával töltse le a fájlokat a `<function_app>` projektből: 

    ```http
    https://<function_app>.scm.azurewebsites.net/api/zip/site/wwwroot/
    ```

    Többek között arról is győződjön meg, hogy a zip-fájl csak a függvényalkalmazás `/site/wwwroot/` projektfájlját tartalmazza, a teljes webhelyet nem. Ha még nem jelentkezett be az Azure-ba, a következőt kell megtennie: .  

Egy .zip-fájlt is letölthet egy GitHub-adattárból. Ha .zip-fájlként tölt le egy GitHub-adattárat, a GitHub hozzáad egy további mappaszintet a ághoz. Ez az extra mappaszint azt jelenti, hogy a .zip fájlt nem helyezheti üzembe közvetlenül, amikor letöltötte a GitHubról. Ha GitHub-adattárat használ a függvényalkalmazás karbantartásához, az [](functions-continuous-deployment.md) alkalmazás üzembe helyezéséhez folyamatos integrációt kell használnia.  

## <a name="deploy-by-using-azure-cli"></a><a name="cli"></a>Üzembe helyezés az Azure CLI-vel

Az Azure CLI használatával elindíthat egy leküldéses üzembe helyezést. Az [az functionapp deployment source config-zip](/cli/azure/functionapp/deployment/source#az_functionapp_deployment_source_config_zip) paranccsal telepítsen egy .zip fájlt a függvényalkalmazásba. A parancs csak akkor használható, ha az Azure CLI 2.0.21-es vagy újabb verzióját használja. A használt Azure CLI-verziót az paranccsal `az --version` láthatja.

A következő parancsban cserélje le a helyőrzőt a .zip fájl helyének `<zip_file_path>` elérési útjára. Emellett cserélje le a helyére a függvényalkalmazás egyedi nevét, a helyére pedig `<app_name>` `<resource_group>` az erőforráscsoport nevét.

```azurecli-interactive
az functionapp deployment source config-zip -g <resource_group> -n \
<app_name> --src <zip_file_path>
```

Ez a parancs projektfájlokat helyez üzembe a letöltött .zip fájlból az Azure-beli függvényalkalmazásban. Ezután újraindítja az alkalmazást. A függvényalkalmazás üzemelő példányai listájának megtekintéséhez a REST API-kat kell használnia.

Ha az Azure CLI-t használja a helyi számítógépén, a a `<zip_file_path>` számítógépen található .zip fájl elérési útja. Az Azure CLI-t a következő [Azure Cloud Shell.](../cloud-shell/overview.md) A fájlok Cloud Shell fel kell töltenie az üzembe helyezési .zip-fájlt a Azure Files-fiókhoz társított Cloud Shell. Ebben az esetben `<zip_file_path>` a az a tárolási hely, ahol a Cloud Shell használ. További információ: [Fájlok megőrzésének Azure Cloud Shell.](../cloud-shell/persisting-shell-storage.md)

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]

## <a name="run-functions-from-the-deployment-package"></a>Függvények futtatása a központi telepítési csomagból

A függvényeket közvetlenül a központi telepítési csomag fájljában is futtathatja. Ez a metódus kihagyja a fájlok csomagból a függvényalkalmazás könyvtárába `wwwroot` való másolásának telepítési lépését. Ehelyett a csomagfájlt a Functions-futtatás csatlakoztatja, és a könyvtár tartalma `wwwroot` csak olvashatóvá válik.  

A zip-telepítés integrálható ezzel a funkcióval, amelyet a függvényalkalmazás beállításának értékére való beállításával `WEBSITE_RUN_FROM_PACKAGE` `1` engedélyezhet. További információ: [Függvények futtatása egy telepítőcsomag-fájlból.](run-functions-from-deployment-package.md)

[!INCLUDE [app-service-deploy-zip-push-custom](../../includes/app-service-deploy-zip-push-custom.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Folyamatos üzembe helyezés Azure Functions](functions-continuous-deployment.md)

[.zip push deployment reference topic]: https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file
