---
title: Kód üzembe helyezése ZIP-vagy WAR-fájllal
description: Megtudhatja, hogyan helyezheti üzembe az alkalmazást egy ZIP-fájllal (vagy Java-fejlesztőknek szánt WAR-fájllal) való Azure App Service.
ms.topic: article
ms.date: 08/12/2019
ms.reviewer: sisirap
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 150f6b57f1dec0b6d925ef53b4a7001ae9f23607
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97007908"
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>Az alkalmazás üzembe helyezése egy ZIP-vagy WAR-fájllal való Azure App Service

Ebből a cikkből megtudhatja, hogyan helyezhet üzembe a webalkalmazást egy ZIP-fájl vagy háborús fájl használatával [Azure app Service](overview.md). 

Ez a ZIP-fájl központi telepítése ugyanazt a kudu szolgáltatást használja, amely a folyamatos integráció-alapú központi telepítéseket is kihasználja. A kudu a következő funkciókat támogatja a ZIP-fájlok üzembe helyezéséhez: 

- A korábbi telepítésből törölt fájlok törlése.
- Az alapértelmezett felépítési folyamat bekapcsolásának lehetősége, amely magában foglalja a csomagok visszaállítását.
- Központi telepítés testreszabása, beleértve az üzembe helyezési parancsfájlok futtatását is.  
- Üzembe helyezési naplók. 
- 2048 MB-os fájlméret-korlát.

További információ: kudu- [dokumentáció](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

A WAR-fájl központi telepítése a App Service futtatja a [War](https://wikipedia.org/wiki/WAR_(file_format)) -fájlt a Java-webalkalmazás futtatásához. Lásd: a [War-fájl üzembe helyezése](#deploy-war-file).

> [!NOTE]
> A használatakor a `ZipDeploy` rendszer csak akkor másolja a fájlokat, ha az időbélyegük nem egyezik a már telepítettvel. Gyorsabb üzembe helyezést eredményezhet, ha olyan Build folyamattal hoz létre zip-t, amely gyorsítótárazza a kimeneteket. További információért lásd: [telepítés zip-fájlból vagy URL-címről](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

## <a name="prerequisites"></a>Előfeltételek

A cikk lépéseinek elvégzéséhez [hozzon létre egy app Service alkalmazást](./index.yml), vagy használjon egy másik oktatóanyaghoz létrehozott alkalmazást.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]
A fenti végpont jelenleg nem működik Linux App Services esetén. Ehelyett használjon FTP-t vagy a [zip üzembe helyezési API](faq-app-service-linux.md#continuous-integration-and-deployment) -t.

## <a name="deploy-zip-file-with-azure-cli"></a>ZIP-fájl üzembe helyezése az Azure CLI-vel

Telepítse a feltöltött ZIP-fájlt a webalkalmazásba az az [WebApp Deployment Source config-zip](/cli/azure/webapp/deployment/source#az-webapp-deployment-source-config-zip) parancs használatával.  

A következő példa telepíti a feltöltött ZIP-fájlt. Ha az Azure CLI helyi telepítését használja, adja meg a helyi ZIP-fájl elérési útját a következőhöz: `--src` .

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src clouddrive/<filename>.zip
```

Ez a parancs üzembe helyezi a ZIP-fájlban szereplő fájlokat és könyvtárakat az alapértelmezett App Service-alkalmazásmappában (`\home\site\wwwroot`), majd újraindítja az alkalmazást.

Alapértelmezés szerint az üzembe helyezési motor feltételezi, hogy egy ZIP-fájl készen áll a futtatásra, és nem futtat Build automationt. Ha ugyanazt a Build-automatizálást szeretné engedélyezni, mint a [git-telepítésben](deploy-local-git.md), akkor az `SCM_DO_BUILD_DURING_DEPLOYMENT` alábbi parancs futtatásával állítsa be az alkalmazás beállításait a [Cloud Shellban](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings SCM_DO_BUILD_DURING_DEPLOYMENT=true
```

További információ: kudu- [dokumentáció](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>WAR-fájl üzembe helyezése

Ha a WAR-fájlt App Servicere kívánja telepíteni, küldjön POST-kérelmet a következőnek: `https://<app-name>.scm.azurewebsites.net/api/wardeploy` . A POST kérelem üzenettörzsének tartalmaznia kell a .war fájlt. Az alkalmazás üzembehelyezési hitelesítő adatai a kérelemben alapszintű HTTP-hitelesítéssel vannak megadva.

A WAR-fájlok telepítésekor mindig használja a rendszer `/api/wardeploy` . Ez az API kibővíti a WAR-fájlt, és elhelyezi a megosztott fájl meghajtóján. más telepítési API-k használata inkonzisztens viselkedést eredményezhet. 

Az egyszerű HTTP-hitelesítéshez szükség van a App Service központi telepítési hitelesítő adataira. A központi telepítési hitelesítő adatok beállításával kapcsolatban lásd: [felhasználói szintű hitelesítő adatok beállítása és alaphelyzetbe állítása](deploy-configure-credentials.md#userscope).

### <a name="with-curl"></a>A cURLtal

A következő példa a cURL eszközt használja egy. War fájl üzembe helyezéséhez. Cserélje le a helyőrzőket, `<username>` `<war-file-path>` és `<app-name>` . Ha a cURL rákérdez, írja be a jelszót.

```bash
curl -X POST -u <username> --data-binary @"<war-file-path>" https://<app-name>.scm.azurewebsites.net/api/wardeploy
```

### <a name="with-powershell"></a>A PowerShell-lel

A következő példa a [publish-AzWebapp](/powershell/module/az.websites/publish-azwebapp) feltölti a. War fájlt. Cserélje le a helyőrzőket, `<group-name>` `<app-name>` és `<war-file-path>` .

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <war-file-path>
```

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Következő lépések

A speciális üzembe helyezési forgatókönyvek esetében próbálja meg [üzembe helyezni az Azure-ban a git használatával](deploy-local-git.md). Az Azure-hoz készült git-alapú üzembe helyezés lehetővé teszi a verziókövetés, a csomagok visszaállítása, az MSBuild és egyebek használatát.

## <a name="more-resources"></a>További erőforrások

* [Kudu: telepítés zip-fájlból](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Központi telepítési hitelesítő adatok Azure App Service](deploy-ftp.md)
