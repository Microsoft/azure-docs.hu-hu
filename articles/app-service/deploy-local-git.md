---
title: Üzembe helyezés helyi git-tárházból
description: Megtudhatja, hogyan engedélyezheti a helyi git-telepítést a Azure App Service. A kód a helyi gépről való üzembe helyezésének egyik legegyszerűbb módja.
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.topic: article
ms.date: 02/16/2021
ms.reviewer: dariac
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: faf3afc60c8517509199e6a306f511a15b32358c
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105732839"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Helyi git üzembe helyezése Azure App Service

Ez az útmutató bemutatja, hogyan helyezheti üzembe az alkalmazást [Azure app Service](overview.md) egy git-tárházból a helyi számítógépen.

## <a name="prerequisites"></a>Előfeltételek

A útmutató lépéseinek követéséhez tegye a következőket:

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Telepítse a git](https://www.git-scm.com/downloads)-t.

- Rendelkeznie kell egy helyi git-tárházmal, amelybe telepíteni kívánja a kódot. Egy minta tárház letöltéséhez futtassa a következő parancsot a helyi terminál ablakban:
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="configure-a-deployment-user"></a>Üzembe helyező felhasználó konfigurálása

Lásd: [telepítési hitelesítő adatok konfigurálása Azure app Servicehoz](deploy-configure-credentials.md). Használhat felhasználói vagy alkalmazási hatókörbeli hitelesítő adatokat is.

## <a name="create-a-git-enabled-app"></a>Git-kompatibilis alkalmazás létrehozása

Ha már rendelkezik App Service alkalmazással, és szeretné konfigurálni a helyi git-telepítést, tekintse meg a [meglévő alkalmazások konfigurálását](#configure-an-existing-app) ismertető témakört.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Futtassa [`az webapp create`](/cli/azure/webapp#az_webapp_create) a `--deployment-local-git` parancsot a kapcsolóval. Például:

```azurecli-interactive
az webapp create --resource-group <group-name> --plan <plan-name> --name <app-name> --runtime "<runtime-flag>" --deployment-local-git
```

A kimenet tartalmaz egy URL-címet, például: `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git` . Ezzel az URL-címmel telepítheti az alkalmazást a következő lépésben.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Futtassa a [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) parancsot a git-tárház gyökeréből. Például:

```azurepowershell-interactive
New-AzWebApp -Name <app-name>
```

Ha ezt a parancsmagot egy git-tárházból származó könyvtárból futtatja, az automatikusan létrehoz egy git-távvezérlőt a App Service-alkalmazáshoz `azure` .

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

A portálon először létre kell hoznia egy alkalmazást, majd konfigurálnia kell az üzembe helyezést. Lásd: [meglévő alkalmazás konfigurálása](#configure-an-existing-app).

-----

## <a name="configure-an-existing-app"></a>Meglévő alkalmazás konfigurálása

Ha még nem hozott létre alkalmazást, olvassa el helyette [a git-kompatibilis alkalmazás létrehozása](#create-a-git-enabled-app) című témakört.

# <a name="azure-cli"></a>[Azure CLI](#tab/cli)

Futtassa a parancsot [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source#az-webapp-deployment-source-config-local-git) . Például:

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```

A kimenet tartalmaz egy URL-címet, például: `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git` . Ezzel az URL-címmel telepítheti az alkalmazást a következő lépésben.

> [!TIP]
> Ez az URL-cím tartalmazza a felhasználói hatókörű központi telepítési felhasználónevet. Ha szeretné, [az alkalmazás-hatókörű hitelesítő adatokat is használhatja](deploy-configure-credentials.md#appscope) helyette. 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Állítsa be az `scmType` alkalmazást a [set-AzResource](/powershell/module/az.resources/set-azresource) parancsmag futtatásával.

```powershell-interactive
$PropertiesObject = @{
    scmType = "LocalGit";
}

Set-AzResource -PropertyObject $PropertiesObject -ResourceGroupName <group-name> `
-ResourceType Microsoft.Web/sites/config -ResourceName <app-name>/web `
-ApiVersion 2015-08-01 -Force
```

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. A [Azure Portal](https://portal.azure.com)navigáljon az alkalmazás felügyeleti lapjára.

1. A bal oldali menüben válassza a **központi telepítési központ**  >  **beállításai** lehetőséget. Válassza a **helyi git** lehetőséget a **forrás** területen, majd kattintson a **Mentés** gombra.

    ![Bemutatja, hogyan lehet engedélyezni a helyi git-telepítést a App Service a Azure Portal](./media/deploy-local-git/enable-portal.png)

1. A helyi git szakaszban másolja a **git Clone URI** -t később. Ez az URI nem tartalmaz hitelesítő adatokat.

-----

## <a name="deploy-the-web-app"></a>A webalkalmazás üzembe helyezése

1. Egy helyi terminál ablakban módosítsa a könyvtárat a git-tárház gyökerére, és vegyen fel egy git-távvezérlőt az alkalmazásból kapott URL-cím használatával. Ha a választott módszer nem ad meg URL-címet, használja az `https://<app-name>.scm.azurewebsites.net/<app-name>.git` alkalmazás nevét a ben `<app-name>` .
   
   ```bash
   git remote add azure <url>
   ```

    > [!NOTE]
    > Ha [git-kompatibilis alkalmazást hozott létre a PowerShellben a New-AzWebApp használatával](#create-a-git-enabled-app), a távoli felhasználó már létre van hozva.
   
1. Küldje el az Azure Remote-t a következővel: `git push azure master` . 
   
1. A **git Hitelesítőadat-kezelő** ablakban adja meg a [felhasználói vagy alkalmazás-hatókörbeli hitelesítő adatokat](#configure-a-deployment-user), ne az Azure bejelentkezési hitelesítő adatait.

    Ha a git távoli URL-címe már tartalmazza a felhasználónevet és a jelszót, a rendszer nem kéri. 
   
1. Tekintse át a kimenetet. Előfordulhat, hogy a futásidejű automatizálás, például az MSBuild for ASP.NET, `npm install` a Node.js és `pip install` a Python. 
   
1. Keresse meg az alkalmazást a Azure Portalban annak ellenőrzéséhez, hogy a tartalom telepítve van-e.

## <a name="troubleshoot-deployment"></a>Üzembe helyezési hibák

A következő gyakori hibaüzenetek jelenhetnek meg, ha a git használatával tesz közzé egy App Service alkalmazást az Azure-ban:

|Üzenet|Ok|Feloldás
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|Az alkalmazás nem működik.|Indítsa el az alkalmazást a Azure Portal. A git-telepítés nem érhető el a webalkalmazás leállításakor.|
|`Couldn't resolve host 'hostname'`|Az "Azure" távoli adatcímeinek adatai helytelenek.|A `git remote -v` parancs használatával listázhatja az összes távoli, valamint a hozzá tartozó URL-címet. Győződjön meg arról, hogy az "Azure" távoli URL-címe helyes. Ha szükséges, távolítsa el, majd hozza létre újra a távoli elérést a megfelelő URL-cím használatával.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'main'.`|Nem adott meg ágat a alatt `git push` , vagy nem állította be a `push.default` értéket `.gitconfig` .|Futtassa `git push` újra a főág megadását: `git push azure main` .|
|`Error - Changes committed to remote repository but deployment to website failed.`|Olyan helyi ágat küldött le, amely nem felel meg az alkalmazás üzembe helyezési ágának az Azure-ban.|Ellenőrizze, hogy az aktuális ág van-e `master` . Az alapértelmezett ág módosításához használja az `DEPLOYMENT_BRANCH` Alkalmazásbeállítás értéket.|
|`src refspec [branchname] does not match any.`|Megpróbált leküldeni az "Azure" távoli számítógépének főoldalán kívüli ágra.|Futtassa `git push` újra a főág megadását: `git push azure main` .|
|`RPC failed; result=22, HTTP code = 5xx.`|Ez a hiba akkor fordulhat elő, ha egy nagyméretű git-tárházat próbál leküldeni HTTPS-kapcsolaton keresztül.|Módosítsa a git-konfigurációt a helyi gépen, hogy minél `postBuffer` nagyobb legyen. Példa: `git config --global http.postBuffer 524288000`.|
|`Error - Changes committed to remote repository but your web app not updated.`|Egy Node.js alkalmazást telepített egy _package.jsa_ fájlon, amely további szükséges modulokat határoz meg.|A hiba előtt tekintse át a hibaüzeneteket `npm ERR!` , hogy a probléma további kontextusban legyen. A hiba ismert okai és a hozzájuk tartozó üzenetek a következők `npm ERR!` :<br /><br />**Helytelenül formázott package.jsa következő fájlon**: `npm ERR! Couldn't read dependencies.`<br /><br />A **natív modul nem rendelkezik bináris terjesztéssel a Windows rendszerhez**:<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />vagy <br />`npm ERR! [modulename@version] preinstall: \make || gmake\ `|

## <a name="additional-resources"></a>További források

- [App Service-kiszolgáló létrehozása (a Project kudu dokumentációja)](https://github.com/projectkudu/kudu/wiki)
- [ Folyamatos üzembe helyezés az Azure App Service-be](deploy-continuous-deployment.md)
- [Minta: Webalkalmazás létrehozása és kód üzembe helyezése helyi git-adattárból (Azure CLI)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [Példa: Webalkalmazás létrehozása és kód üzembe helyezése helyi git-adattárból (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
