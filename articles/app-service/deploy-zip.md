---
title: Kód üzembe helyezése ZIP- vagy WAR-fájllal
description: Megtudhatja, hogyan helyezheti üzembe alkalmazását Azure App Service zip-fájllal (vagy Java-fejlesztőknek készült WAR-fájllal).
ms.topic: article
ms.date: 08/12/2019
ms.reviewer: sisirap
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: fe906a63a681515d401d005bf3357a4e7218ae66
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771424"
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>Az alkalmazás üzembe helyezése Azure App Service ZIP- vagy WAR-fájllal

Ez a cikk bemutatja, hogyan helyezheti üzembe a webalkalmazást ZIP-fájl vagy [WAR-fájl](overview.md)használatával a Azure App Service. 

Ez a ZIP-fájltelepítés ugyanazt a Kudu-szolgáltatást használja, amely a folyamatos integrációs alapú üzembe helyezéseket használja. A Kudu a következő funkciókat támogatja a ZIP-fájlok üzembe helyezéséhez: 

- Az előző üzembe helyezésből megmaradt fájlok törlése.
- Az alapértelmezett buildfolyamat bekapcsolási lehetősége, beleértve a csomag-visszaállítást is.
- Az üzembe helyezés testreszabása, beleértve az üzembe helyezési szkriptek futtatását.  
- Üzembe helyezési naplók. 
- A fájlméret korlátja 2048 MB.

További információt a [Kudu dokumentációjában talál.](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)

A WAR-fájl üzembe helyezése telepíti a [WAR-App Service](https://wikipedia.org/wiki/WAR_(file_format)) a Java-webalkalmazás futtatásához. Lásd: [WAR-fájl üzembe helyezése.](#deploy-war-file)

> [!NOTE]
> A használata esetén a fájlok csak akkor lesznek átmásolva, ha az időbélyegeik nem egyeznek meg a már `ZipDeploy` üzembe helyezett fájlokkal. Ha a kimeneteket gyorsítótárazza egy zip-fájl buildfolyamattal, az gyorsabb üzembe helyezést eredményezhet. További információ: Üzembe helyezés [zip-fájlból vagy URL-címből.](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)

## <a name="prerequisites"></a>Előfeltételek

A cikkben található lépések befejezéséhez hozzon létre egy App Service [alkalmazást,](./index.yml)vagy használjon egy másik oktatóanyaghoz létrehozott alkalmazást.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]
A fenti végpont jelenleg nem működik linuxos App Services esetén. Fontolja meg inkább az FTP vagy [a ZIP üzembe helyezési API](faq-app-service-linux.md#continuous-integration-and-deployment) használatát.

## <a name="deploy-zip-file-with-azure-cli"></a>ZIP-fájl üzembe helyezése az Azure CLI használatával

A feltöltött ZIP-fájlt az az [webapp deployment source config-zip](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config_zip) paranccsal helyezheti üzembe a webalkalmazásban.  

Az alábbi példa a feltöltött ZIP-fájlt telepíti. Az Azure CLI helyi telepítésekor adja meg a helyi ZIP-fájl elérési `--src` útját.

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src clouddrive/<filename>.zip
```

Ez a parancs üzembe helyezi a ZIP-fájlban szereplő fájlokat és könyvtárakat az alapértelmezett App Service-alkalmazásmappában (`\home\site\wwwroot`), majd újraindítja az alkalmazást.

Alapértelmezés szerint az üzembe helyezési motor feltételezi, hogy egy ZIP-fájl készen áll a futtatásra, és nem futtat buildautomatizálást. Ha ugyanazt a buildautomatizálást szeretné engedélyezni, mint a [Git](deploy-local-git.md)üzemelő példányában, állítsa be az alkalmazásbeállítást a következő parancs futtatásával a `SCM_DO_BUILD_DURING_DEPLOYMENT` [Cloud Shell:](https://shell.azure.com)

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings SCM_DO_BUILD_DURING_DEPLOYMENT=true
```

További információt a [Kudu dokumentációjában talál.](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>WAR-fájl üzembe helyezése

HA WAR-fájlt App Service, küldjön egy POST kérést a következőnek: `https://<app-name>.scm.azurewebsites.net/api/wardeploy` . A POST kérelem üzenettörzsének tartalmaznia kell a .war fájlt. Az alkalmazás üzembehelyezési hitelesítő adatai a kérelemben alapszintű HTTP-hitelesítéssel vannak megadva.

`/api/wardeploy`WAR-fájlok üzembe helyezésekor mindig használja a fájlt. Ez az API kibontja a WAR-fájlt, és a megosztott fájlmegosztási meghajtón fogja elhelyzni. más üzembe helyezési API-k használata inkonzisztens viselkedést eredményezhet. 

AZ ALAPSZINTŰ HTTP-hitelesítéshez szüksége lesz a App Service hitelesítő adataira. Az üzembe helyezési hitelesítő adatok beállításának mikéntjéhez lásd: Felhasználói szintű hitelesítő adatok [beállítása és visszaállítása.](deploy-configure-credentials.md#userscope)

### <a name="with-curl"></a>cURL-sel

Az alábbi példa a cURL eszközzel helyez üzembe egy .war-fájlt. Cserélje le a `<username>` , és helyőrzőket. `<war-file-path>` `<app-name>` Amikor a cURL kéri, írja be a jelszót.

```bash
curl -X POST -u <username> --data-binary @"<war-file-path>" https://<app-name>.scm.azurewebsites.net/api/wardeploy
```

### <a name="with-powershell"></a>A PowerShell-lel

Az alábbi példa a [Publish-AzWebapp fájlt használja,](/powershell/module/az.websites/publish-azwebapp) és feltölti a .war fájlt. Cserélje le a `<group-name>` , és helyőrzőket. `<app-name>` `<war-file-path>`

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <war-file-path>
```

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Következő lépések

A speciálisabb üzembe helyezési forgatókönyvekért próbálja meg üzembe [helyezni az Azure-ban a Git használatával.](deploy-local-git.md) A Git-alapú üzembe helyezés az Azure-ban lehetővé teszi a verzióvezérlést, a csomag-visszaállítást, az MSBuildet stb.

## <a name="more-resources"></a>További erőforrások

* [Kudu: Üzembe helyezés zip-fájlból](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Azure App Service üzembe helyezés hitelesítő adatai](deploy-ftp.md)
