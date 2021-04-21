---
title: Üzembe helyezés helyi Git-adattárból
description: Ismerje meg, hogyan engedélyezheti a helyi Git-telepítést a Azure App Service. A kód helyi gépről való üzembe helyezésének egyik legegyszerűbb módja.
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.topic: article
ms.date: 02/16/2021
ms.reviewer: dariac
ms.custom: seodec18, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 3196233728bb7f6493bbc06234c62d261ac99254
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832355"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Helyi Git üzembe helyezése Azure App Service

Ez az útmutató bemutatja, hogyan helyezheti üzembe az alkalmazást Azure App Service helyi számítógépen található Git-adattárból. [](overview.md)

## <a name="prerequisites"></a>Előfeltételek

Az útmutató lépéseit a következőképpen követjük:

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Telepítse a Gitet.](https://www.git-scm.com/downloads)

- Legyen egy helyi Git-adattára, amely tartalmazza az üzembe helyezni kívánt kódot. Mintaadattár letöltéséhez futtassa a következő parancsot a helyi terminálablakban:
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="configure-a-deployment-user"></a>Üzembe helyező felhasználó konfigurálása

Lásd: [Telepítési hitelesítő adatok konfigurálása Azure App Service.](deploy-configure-credentials.md) Felhasználói vagy alkalmazáshatókörű hitelesítő adatokat is használhat.

## <a name="create-a-git-enabled-app"></a>Git-kompatibilis alkalmazás létrehozása

Ha már rendelkezik egy App Service alkalmazással, és szeretné konfigurálni hozzá a Git helyi üzemelő példányát, tekintse meg a Meglévő [alkalmazás konfigurálása](#configure-an-existing-app) ehelyett.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Futtassa [`az webapp create`](/cli/azure/webapp#az_webapp_create) a következőt a `--deployment-local-git` kapcsolóval: . Például:

```azurecli-interactive
az webapp create --resource-group <group-name> --plan <plan-name> --name <app-name> --runtime "<runtime-flag>" --deployment-local-git
```

A kimenet a következő URL-címet tartalmazza: `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git` . Használja ezt az URL-címet az alkalmazás üzembe helyezéséhez a következő lépésben.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Futtassa [a New-AzWebApp](/powershell/module/az.websites/new-azwebapp) alkalmazást a Git-adattár gyökerében. Például:

```azurepowershell-interactive
New-AzWebApp -Name <app-name>
```

Amikor ezt a parancsmagot egy Git-adattárból futtatja, automatikusan létrehoz egy távoli Git-et a App Service alkalmazásához nevű `azure` névvel.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

A portálon először létre kell hoznia egy alkalmazást, majd konfigurálnia kell hozzá az üzembe helyezést. Lásd: [Meglévő alkalmazás konfigurálása.](#configure-an-existing-app)

-----

## <a name="configure-an-existing-app"></a>Meglévő alkalmazás konfigurálása

Ha még nem hozott létre alkalmazást, tekintse meg [a Git-kompatibilis alkalmazás létrehozása ehelyett.](#create-a-git-enabled-app)

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Futtassa [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source#az_webapp_deployment_source_config_local_git) az et. Például:

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```

A kimenet a következő URL-címet tartalmazza: `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git` . Használja ezt az URL-címet az alkalmazás üzembe helyezéséhez a következő lépésben.

> [!TIP]
> Ez az URL-cím tartalmazza a felhasználói hatókör üzembe helyezési felhasználónevét. Ha szeretné, használhatja [helyette az alkalmazáshatókörű hitelesítő adatokat.](deploy-configure-credentials.md#appscope) 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Állítsa be `scmType` az alkalmazás parancsát a [Set-AzResource parancsmag](/powershell/module/az.resources/set-azresource) futtatásával.

```powershell-interactive
$PropertiesObject = @{
    scmType = "LocalGit";
}

Set-AzResource -PropertyObject $PropertiesObject -ResourceGroupName <group-name> `
-ResourceType Microsoft.Web/sites/config -ResourceName <app-name>/web `
-ApiVersion 2015-08-01 -Force
```

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. A [Azure Portal](https://portal.azure.com)lépjen az alkalmazás felügyeleti oldalára.

1. A bal oldali menüben válassza az **Üzembe helyezési központ beállításai**  >  **lehetőséget.** Válassza a **Local Git (Helyi Git)** **lehetőséget a Source (Forrás) mezőben,** majd kattintson **a Save (Mentés) gombra.**

    ![Bemutatja, hogyan engedélyezheti a helyi Git-App Service a Azure Portal](./media/deploy-local-git/enable-portal.png)

1. A Helyi Git szakaszban másolja ki a Git Clone **URI-t** későbbi használatra. Ez az URI nem tartalmaz hitelesítő adatokat.

-----

## <a name="deploy-the-web-app"></a>A webalkalmazás üzembe helyezése

1. Egy helyi terminálablakban módosítsa a könyvtárat a Git-adattár gyökerére, és adjon hozzá egy távoli Git-et az alkalmazásból kapott URL-cím használatával. Ha a választott metódus nem ad URL-címet, használja a nevet `https://<app-name>.scm.azurewebsites.net/<app-name>.git` az alkalmazás nevével a fájlban. `<app-name>`
   
   ```bash
   git remote add azure <url>
   ```

    > [!NOTE]
    > Ha a [New-AzWebApp használatával létrehozott egy Git-kompatibilis alkalmazást a PowerShellben,](#create-a-git-enabled-app)a távoli alkalmazás már létrejött.
   
1. A távoli Azure-fiókba való leküldés a következővel: `git push azure master` . 
   
1. A **Git Hitelesítőadat-kezelő** adja meg a felhasználói vagy alkalmazáshatókörű hitelesítő adatait, és ne az Azure bejelentkezési hitelesítő adatait. [](#configure-a-deployment-user)

    Ha a Távoli Git URL-cím már tartalmazza a felhasználónevet és a jelszót, a rendszer nem fogja kérni. 
   
1. Tekintse át a kimenetet. Előfordulhat, hogy futásidő-specifikus automatizálást lát, például MSBuild for ASP.NET, `npm install` Node.js és `pip install` Pythonhoz. 
   
1. Keresse meg az alkalmazást a Azure Portal, és ellenőrizze, hogy a tartalom telepítve van-e.

## <a name="troubleshoot-deployment"></a>Üzembe helyezés hibaelhárítása

A következő gyakori hibaüzenetek jelenhetnek meg, ha a Git használatával tesz közzé App Service azure-beli alkalmazásba:

|Üzenet|Ok|Feloldás
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|Az alkalmazás nem működik.|Indítsa el az alkalmazást a Azure Portal. A Git üzembe helyezése nem érhető el a webalkalmazás leállított példánya esetén.|
|`Couldn't resolve host 'hostname'`|Az "azure" távoli kapcsolat címinformációi helytelenek.|Az `git remote -v` paranccsal listába használhatja az összes távoli et és a hozzá tartozó URL-címet. Ellenőrizze, hogy az "azure" távoli fájl URL-címe helyes-e. Szükség esetén távolítsa el, majd hozza létre újra a távolit a megfelelő URL-cím használatával.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'main'.`|Nem adott meg ágat a alatt, vagy nem adott meg `git push` `push.default` értéket a `.gitconfig` -ban.|Futtassa `git push` újra a következőt, és adja meg a főágat: `git push azure main` .|
|`Error - Changes committed to remote repository but deployment to website failed.`|Lekért egy helyi ágat, amely nem felel meg az "azure" alkalmazástelepítési ágának.|Ellenőrizze, hogy az aktuális ág a `master` következő-e: . Az alapértelmezett ágat az `DEPLOYMENT_BRANCH` alkalmazásbeállítással módosíthatja.|
|`src refspec [branchname] does not match any.`|Az "azure" távoli main ágtól különböző ágba próbált leküldést.|Futtassa `git push` újra a következőt, és adja meg a főágat: `git push azure main` .|
|`RPC failed; result=22, HTTP code = 5xx.`|Ez a hiba akkor fordulhat elő, ha nagy git-adattárat próbál lekultálni HTTPS-protokollon keresztül.|Módosítsa a git-konfigurációt a helyi gépen a `postBuffer` nagyobbra. Példa: `git config --global http.postBuffer 524288000`.|
|`Error - Changes committed to remote repository but your web app not updated.`|Üzembe helyezett egy Node.js egy _fájlonpackage.js,_ amely további szükséges modulokat ad meg.|Tekintse át `npm ERR!` a hiba előtti hibaüzeneteket, hogy további információt kapjon a hibáról. A hiba ismert okai és a kapcsolódó üzenetek a `npm ERR!` következők:<br /><br />**Hibásan formázott package.jsfájlban:**`npm ERR! Couldn't read dependencies.`<br /><br />**A natív modul nem bináris elosztást ad a Windowshoz:**<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />vagy <br />`npm ERR! [modulename@version] preinstall: \make || gmake\ `|

## <a name="additional-resources"></a>További források

- [App Service buildkiszolgáló (a Kudu projekt dokumentációja)](https://github.com/projectkudu/kudu/wiki)
- [ Folyamatos üzembe helyezés az Azure App Service-be](deploy-continuous-deployment.md)
- [Minta: Webalkalmazás létrehozása és kód üzembe helyezése helyi Git-adattárból (Azure CLI)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [Minta: Webalkalmazás létrehozása és kód üzembe helyezése helyi Git-adattárból (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
