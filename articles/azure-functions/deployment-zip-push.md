---
title: Azure Functions zip-leküldéses üzembe helyezése
description: A Azure Functions közzétételéhez használja a kudu üzembe helyezési szolgáltatás. zip fájljának központi telepítési létesítményeit.
ms.topic: conceptual
ms.date: 08/12/2018
ms.openlocfilehash: e104661dcdf1f6c6fd6dd5eb1024748980e7931f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018433"
---
# <a name="zip-deployment-for-azure-functions"></a>Azure Functions üzembe helyezése ZIP-fájlokkal

Ez a cikk bemutatja, hogyan helyezheti üzembe a Function app Project-fájljait az Azure-ban egy. zip (tömörített) fájlból. Megtudhatja, hogyan végezheti el a leküldéses telepítést az Azure CLI és a REST API-k használatával. A [Azure functions Core Tools](functions-run-local.md) ezeket a telepítési API-kat használja, amikor helyi projektet tesz közzé az Azure-ban.

A Azure Functions a Azure App Service által biztosított folyamatos üzembe helyezési és integrációs lehetőségek teljes skáláját ismerteti. További információ: [Azure functions folyamatos üzembe helyezése](functions-continuous-deployment.md).

A fejlesztés felgyorsításához könnyebben telepítheti a Function app Project-fájljait közvetlenül egy. zip-fájlból. A. zip telepítési API egy. zip fájl tartalmát veszi fel, és kibontja a tartalmat a `wwwroot` Function alkalmazás mappájába. Ez a. zip-fájl központi telepítése ugyanazt a kudu szolgáltatást használja, amely a folyamatos integráción alapuló központi telepítéseket is magában foglalja, beleértve a következőket:

+ A korábbi központi telepítések során hagyott fájlok törlése.
+ Központi telepítés testreszabása, beleértve az üzembe helyezési parancsfájlok futtatását is.
+ Üzembe helyezési naplók.
+ A Function triggerek szinkronizálása egy [felhasználási terv](functions-scale.md) Function alkalmazásban.

További információ: [. zip telepítési útmutató](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

## <a name="deployment-zip-file-requirements"></a>Az üzembe helyezés. zip fájlra vonatkozó követelmények

A leküldéses telepítéshez használt. zip fájlnak tartalmaznia kell a függvény futtatásához szükséges összes fájlt.

>[!IMPORTANT]
> Ha a. zip-telepítést használja, a. zip fájlban nem található, meglévő telepítésből származó összes fájl törlődik a Function alkalmazásból.  

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

A Function alkalmazás tartalmazza a címtárban található összes fájlt és mappát `wwwroot` . A. zip fájlok központi telepítése magában foglalja a `wwwroot` könyvtár tartalmát, a könyvtárat azonban nem. C#-beli függvénytár-projekt telepítésekor a lefordított függvénytár-fájlokat és a függőségeket is bele kell foglalni a `bin` . zip-csomagban található almappába.

## <a name="download-your-function-app-files"></a>A Function alkalmazás fájljainak letöltése

Ha helyi számítógépen fejleszt, egyszerűen létrehozhat egy. zip fájlt a Function app Project mappából a fejlesztői számítógépen.

Előfordulhat azonban, hogy a Azure Portal szerkesztője segítségével hozta létre a függvényeket. Egy meglévő Function app-projektet a következő módokon tölthet le:

+ **A Azure Portal:**

  1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), majd nyissa meg a Function alkalmazást.

  2. Az **Áttekintés** lapon válassza az **alkalmazás tartalmának letöltése** lehetőséget. Válassza ki a letöltési beállításokat, majd válassza a **Letöltés** lehetőséget.

      ![A Function app-projekt letöltése](./media/deployment-zip-push/download-project.png)

     A letöltött. zip fájl formátuma megfelelő, és a. zip leküldéses telepítés használatával újra közzé kell tenni a Function alkalmazásban. A portál letöltése azt is felveheti a szükséges fájlokat, hogy közvetlenül a Visual Studióban nyissa meg a Function alkalmazást.

+ **REST API-k használata:**

    A következő üzembe helyezési API-val letöltheti a fájlokat a `<function_app>` projektből: 

    ```http
    https://<function_app>.scm.azurewebsites.net/api/zip/site/wwwroot/
    ```

    A `/site/wwwroot/` használatával biztosíthatja, hogy a zip-fájl csak a Function app Project-fájlokat tartalmazza, és ne a teljes helyet. Ha még nem jelentkezett be az Azure-ba, a rendszer erre kéri.  

A. zip fájlt egy GitHub-adattárból is letöltheti. Amikor a GitHub-tárházat. zip fájlként tölti le, a GitHub hozzáadja az ág további mappa szintjét. Ez az extra mappa-szint azt jelenti, hogy a. zip fájlt nem lehet közvetlenül a GitHubról letölteni. Ha GitHub-tárházat használ a Function-alkalmazás karbantartásához, akkor a [folyamatos integrációt](functions-continuous-deployment.md) kell használnia az alkalmazás üzembe helyezéséhez.  

## <a name="deploy-by-using-azure-cli"></a><a name="cli"></a>Üzembe helyezés az Azure CLI-vel

A leküldéses telepítést az Azure CLI használatával aktiválhatja. Az az [functionapp Deployment Source config-zip](/cli/azure/functionapp/deployment/source#az-functionapp-deployment-source-config-zip) paranccsal leküldheti a. zip-fájl üzembe helyezését a Function alkalmazásba. A parancs használatához az Azure CLI 2.0.21 vagy újabb verzióját kell használnia. Ha szeretné megtekinteni, hogy milyen Azure CLI-verziót használ, használja az `az --version` parancsot.

A következő parancsban cserélje le a `<zip_file_path>` helyőrzőt a. zip fájl helyének elérési útjára. Továbbá cserélje le a `<app_name>` függvényt a Function alkalmazás egyedi nevére, és cserélje le az `<resource_group>` erőforráscsoport nevére.

```azurecli-interactive
az functionapp deployment source config-zip -g <resource_group> -n \
<app_name> --src <zip_file_path>
```

Ezzel a paranccsal a letöltött. zip fájlból telepítheti a Project fájlokat a Function alkalmazásba az Azure-ban. Ezután újraindítja az alkalmazást. A Function app-példányok listájának megtekintéséhez a REST API-kat kell használnia.

Amikor az Azure CLI-t használja a helyi számítógépen, `<zip_file_path>` a a. zip-fájl elérési útja a számítógépen. Az Azure CLI-t a [Azure Cloud Shell](../cloud-shell/overview.md)is futtathatja. Cloud Shell használatakor először fel kell töltenie a Deployment. zip fájlt a Cloud Shell társított Azure Files-fiókba. Ebben az esetben `<zip_file_path>` a Cloud Shell-fiók által használt tárolási hely. További információ: fájlok megőrzése [Azure Cloud Shellban](../cloud-shell/persisting-shell-storage.md).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]

## <a name="run-functions-from-the-deployment-package"></a>Függvények futtatása a központi telepítési csomagból

Dönthet úgy is, hogy a függvényeket közvetlenül a központi telepítési csomag fájljából futtatja. Ez a módszer kihagyja a telepítési lépést a fájlok a csomagból a `wwwroot` Function alkalmazás könyvtárába való másolásával. Ehelyett a csomagfájl a functions futtatókörnyezettel van csatlakoztatva, és a `wwwroot` könyvtár tartalma írásvédett lesz.  

A zip-telepítés integrálható ezzel a szolgáltatással, amelyet a Function app beállítás `WEBSITE_RUN_FROM_PACKAGE` értékének beállításával engedélyezhet `1` . További információkért lásd: [függvények futtatása központi telepítési csomagból](run-functions-from-deployment-package.md).

[!INCLUDE [app-service-deploy-zip-push-custom](../../includes/app-service-deploy-zip-push-custom.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Azure Functions – folyamatos üzembe helyezés](functions-continuous-deployment.md)

[.zip push deployment reference topic]: https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file
