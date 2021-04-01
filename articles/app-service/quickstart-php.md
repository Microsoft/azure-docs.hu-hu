---
title: 'Gyors útmutató: PHP-alapú Webalkalmazás létrehozása'
description: Percek alatt üzembe helyezheti az első PHP-Hello World Azure App Service. Az üzembe helyezés a git használatával történik, amely a App Service üzembe helyezésének számos módszerének egyike.
ms.assetid: 6feac128-c728-4491-8b79-962da9a40788
ms.topic: quickstart
ms.date: 08/01/2020
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: f6876d0aef0d3d87e038b623c395f8368a14e90c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97561851"
---
# <a name="create-a-php-web-app-in-azure-app-service"></a>PHP-alapú Webalkalmazás létrehozása Azure App Service

::: zone pivot="platform-windows"  
Az [Azure App Service](overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás.  Ez a rövid útmutató bemutatja, hogyan helyezhet üzembe egy PHP-alkalmazást a Windows Azure App Service.
::: zone-end  

::: zone pivot="platform-linux"
Az [Azure App Service](overview.md) egy hatékonyan méretezhető, önjavító webes üzemeltetési szolgáltatás.  Ez a gyors útmutató azt ismerteti, hogyan helyezhet üzembe egy PHP-alkalmazást a Linuxon futó Azure App Service-ben.
::: zone-end  

A Cloud Shellben az [Azure CLI-vel](/cli/azure/get-started-with-azure-cli) létrehozza a webalkalmazást, a Gittel pedig üzembe helyezi a PHP-mintakódot a webalkalmazásban.

![Az Azure-ban futó mintaalkalmazás](media/quickstart-php/hello-world-in-browser.png)

Ezeket a lépéseket Mac, Windows vagy Linux rendszert futtató gépen is követheti. Az előfeltételek telepítése után a lépések végrehajtása nagyjából öt percet vesz igénybe.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

* <a href="https://git-scm.com/" target="_blank">A Git telepítése</a>
* <a href="https://php.net/manual/install.php" target="_blank">A PHP telepítése</a>

## <a name="download-the-sample-locally"></a>Minta helyi letöltése

Futtassa a következő parancsokat egy terminálablakban. Ezzel klónozza a mintaalkalmazást a helyi gépre, és a mintakódot tartalmazó könyvtárba lép. 

```bash
git clone https://github.com/Azure-Samples/php-docs-hello-world
cd php-docs-hello-world
```

## <a name="run-the-app-locally"></a>Az alkalmazás futtatása helyben

Futtassa helyileg az alkalmazást, hogy lássa, hogyan fog kinézni az Azure-ban üzembe helyezve. Nyisson meg egy terminálablakot, és a `php` parancs használatával indítsa el a beépített PHP-webkiszolgálót.

```bash
php -S localhost:8080
```

Nyisson meg egy webböngészőt, majd keresse meg a mintaalkalmazást a `http://localhost:8080` címen.

Az oldalon látható mintaalkalmazáson ekkor a **Hello World!** üzenet jelenik meg.

![A helyileg futó mintaalkalmazás](media/quickstart-php/localhost-hello-world-in-browser.png)

A terminálablakban nyomja le a **Ctrl+C** billentyűkombinációt a webkiszolgálóból történő kilépéshez.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user.md)]

::: zone pivot="platform-windows"  
[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group.md)]
::: zone-end  

::: zone pivot="platform-linux"
[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-linux.md)]
::: zone-end

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan.md)]

## <a name="create-a-web-app"></a>Webalkalmazás létrehozása

A Cloud Shellben az [](/cli/azure/webapp#az-webapp-create) paranccsal hozzon létre egy webalkalmazást`az webapp create` a `myAppServicePlan` App Service-csomagban. 

A következő példában cserélje ki az `<app-name>` nevet egy globálisan egyedi névre (érvényes karakterek: `a-z`, `0-9` és `-`). A futtatókörnyezet beállítása `PHP|7.4` lett. Az összes támogatott futtatókörnyezet megtekintéséhez futtassa a parancsot [`az webapp list-runtimes`](/cli/azure/webapp#az-webapp-list-runtimes) . 

```azurecli-interactive
# Bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.4" --deployment-local-git
# PowerShell
az --% webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.4" --deployment-local-git
```

> [!NOTE]
> A PowerShell 3,0-ben bevezetett leállítás-elemzési szimbólum `(--%)` arra utasítja a PowerShellt, hogy tartózkodjanak a bevitel PowerShell-parancsként vagy-kifejezésként való értelmezésének.
>

A webalkalmazás létrehozása után az Azure CLI az alábbi példához hasonló eredményeket jelenít meg:

<pre>
Local git is configured with url of 'https://&lt;username&gt;@&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;app-name&gt;.azurewebsites.net",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>

Létrehozott egy üres új webalkalmazást, amelyen engedélyezve van a git üzembe helyezése.

> [!NOTE]
> A távoli Git URL-címe a `deploymentLocalGitUrl` tulajdonságban látható, a következő formátumban: `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Mentse ezt az URL-t, mert később még szüksége lesz rá.
>

Tallózással keresse meg az újonnan létrehozott webalkalmazást. Cserélje le az _&lt; app-Name>t_ az előző lépésben létrehozott egyedi alkalmazás nevére.

```bash
http://<app-name>.azurewebsites.net
```

Az új webalkalmazásnak így kell kinéznie:

![Üres webalkalmazás oldal](media/quickstart-php/app-service-web-service-created.png)

[!INCLUDE [Push to Azure](../../includes/app-service-web-git-push-to-azure.md)] 

<pre>
Counting objects: 2, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (2/2), 352 bytes | 0 bytes/s, done.
Total 2 (delta 1), reused 0 (delta 0)
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id '25f18051e9'.
remote: Generating deployment script.
remote: Running deployment command...
remote: Handling Basic Web Site deployment.
remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
remote: Copying file: '.gitignore'
remote: Copying file: 'LICENSE'
remote: Copying file: 'README.md'
remote: Copying file: 'index.php'
remote: Ignoring: .git
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Deployment successful.
To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
   cc39b1e..25f1805  main -> main
</pre>

## <a name="browse-to-the-app"></a>Az alkalmazás megkeresése tallózással

Tallózással keresse meg az üzembe helyezett alkalmazást a webböngésző használatával.

```
http://<app-name>.azurewebsites.net
```

A PHP mintakód az Azure App Service webalkalmazásban fut.

![Az Azure-ban futó mintaalkalmazás](media/quickstart-php/hello-world-in-browser.png)

**Gratulálunk!** Elvégezte az első PHP-webapp üzembe helyezését az App Service-ben.

## <a name="update-locally-and-redeploy-the-code"></a>A kód frissítése helyileg és ismételt üzembe helyezése

Egy helyi szövegszerkesztő használatával nyissa meg a `index.php` fájlt a PHP-alkalmazáson belül, majd módosítsa kissé annak szövegét a `echo` melletti sztringen belül:

```php
echo "Hello Azure!";
```

A helyi terminálablakban mentse a módosításokat a Gitben, majd továbbítsa a kód módosításait az Azure-ba.

```bash
git commit -am "updated output"
git push azure main
```

Az üzembe helyezés befejezését követően térjen vissza **Az alkalmazás megkeresése tallózással** lépésben megnyitott böngészőablakra, és frissítse az oldalt.

![Az Azure-ban futó frissített mintaalkalmazás](media/quickstart-php/hello-azure-in-browser.png)

## <a name="manage-your-new-azure-app"></a>Az új Azure-alkalmazás kezelése

1. A létrehozott webalkalmazás felügyeletéhez ugorjon az <a href="https://portal.azure.com" target="_blank">Azure Portalra</a>. Keresse meg és válassza ki a **app Services**.

    ![App Services keresése, Azure Portal, PHP-alapú Webalkalmazás létrehozása](media/quickstart-php/navigate-to-app-services-in-the-azure-portal.png)

2. Válassza ki az Azure-alkalmazás nevét.

    ![Navigálás a portálon egy Azure-alkalmazáshoz](./media/quickstart-php/php-docs-hello-world-app-service-list.png)

    Ekkor megjelenik a webalkalmazás **áttekintő** lapja. Itt olyan alapszintű felügyeleti feladatokat hajthat végre, mint a **Tallózás**, **Leállítás**, **Újraindítás** és **Törlés**.

    ![Az App Service lap az Azure Portalon](media/quickstart-php/php-docs-hello-world-app-service-detail.png)

    A webalkalmazás menü különböző beállításokat biztosít az alkalmazás konfigurálásához. 

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [PHP és MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [A PHP-alkalmazás konfigurálása](configure-language-php.md)
