---
title: Oktatóanyag – frissítés leküldése a földrajzilag replikált beállításjegyzékbe
description: Küldjön le egy frissített Docker-rendszerképet a Geo-replikált Azure Container registrybe, majd tekintse meg a több régióban futó webalkalmazások automatikusan telepített módosításait. Ez egy háromrészes sorozat harmadik része.
ms.topic: tutorial
ms.date: 04/30/2018
ms.custom: seodec18, mvc
ms.openlocfilehash: 9222ac31e067cba6a0ffa71143c90f906ba6ff7f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "74454689"
---
# <a name="tutorial-push-an-updated-container-image-to-a-geo-replicated-container-registry-for-regional-web-app-deployments"></a>Oktatóanyag: frissített tároló-rendszerkép leküldése a földrajzi helyről replikált tároló-beállításjegyzékbe a regionális webalkalmazások üzembe helyezéséhez

Ez egy háromrészes sorozat harmadik része. Az [előző oktatóanyagban](container-registry-tutorial-deploy-app.md) a georeplikációt két különböző régióban üzembe helyezett webalkalmazáshoz konfigurálta. Ebben az oktatóanyagban először az alkalmazást módosítjuk, majd egy új tárolórendszerképet hozunk létre, és leküldjük azt a georeplikált beállításjegyzékbe. Végül megtekintjük az Azure Container Registry webhookok által automatikusan üzembe helyezett módosításokat mind a két webalkalmazás esetében.

Az oktatóanyag-sorozat utolsó része a következő lépésekből áll:

> [!div class="checklist"]
> * A webalkalmazás HTML-címének módosítása
> * A Docker-rendszerkép összeállítása és címkézése
> * A módosítás leküldése az Azure Container Registrybe
> * A frissített alkalmazás megtekintése két különböző régióban

Ha még nem konfigurálta a két regionális *Web App for Containers*-példányt, térjen vissza az előző oktatóanyag-sorozathoz, amely a [Webalkalmazás üzembe helyezése az Azure Container Registryből](container-registry-tutorial-deploy-app.md) címet viseli.

## <a name="modify-the-web-application"></a>A webalkalmazás módosítása

Ebben a lépésben olyan módosítást végzünk a webalkalmazáson, amely jól látható lesz, amint leküldjük a frissített tárolórendszerképet az Azure Container Registrybe.

Keresse meg az `AcrHelloworld/Views/Home/Index.cshtml` fájlt abban az alkalmazásforrásban, amelyet a [GitHubból klónozott](container-registry-tutorial-prepare-registry.md#get-application-code) egy előző oktatóanyagban, és nyissa meg a kedvenc szövegszerkesztőjével. Adja hozzá a következő sort a már meglévő `<h1>` sor után:

```html
<h1>MODIFIED</h1>
```

A módosított `Index.cshtml`-nek az alábbihoz hasonlónak kell lennie:

```html
@{
    ViewData["Title"] = "Azure Container Registry :: Geo-replication";
}
<style>
    body {
        background-image: url('images/azure-regions.png');
        background-size: cover;
    }
    .footer {
        position: fixed;
        bottom: 0px;
        width: 100%;
    }
</style>

<h1 style="text-align:center;color:blue">Hello World from:  @ViewData["REGION"]</h1>
<h1>MODIFIED</h1>
<div class="footer">
    <ul>
        <li>Registry URL: @ViewData["REGISTRYURL"]</li>
        <li>Registry IP: @ViewData["REGISTRYIP"]</li>
        <li>Registry Region: @ViewData["REGION"]</li>
    </ul>
</div>
```

## <a name="rebuild-the-image"></a>A rendszerkép újraépítése

Most, hogy frissítette a webalkalmazást, építse újra a tárolórendszerképét. Mint korábban, használja a címkéhez a teljes rendszerképnevet, beleértve a bejelentkezési kiszolgáló teljes tartománynevét (FQDN):

```bash
docker build . -f ./AcrHelloworld/Dockerfile -t <acrName>.azurecr.io/acr-helloworld:v1
```

## <a name="push-image-to-azure-container-registry"></a>Rendszerkép leküldése az Azure Container Registrybe

Következőként küldje le a frissített *acr-helloworld* tárolórendszerképet a georeplikált beállításjegyzékbe. Itt egy `docker push` paranccsal üzembe helyezi a frissített tárolórendszerképet a beállításjegyzék replikációiban, az *USA nyugati régiójában* és az *USA keleti régiójában* is.

```bash
docker push <acrName>.azurecr.io/acr-helloworld:v1
```

A `docker push` kimenet az alábbihoz hasonló lesz:

```console
$ docker push uniqueregistryname.azurecr.io/acr-helloworld:v1
The push refers to a repository [uniqueregistryname.azurecr.io/acr-helloworld]
5b9454e91555: Pushed
d6803756744a: Layer already exists
b7b1f3a15779: Layer already exists
a89567dff12d: Layer already exists
59c7b561ff56: Layer already exists
9a2f9413d9e4: Layer already exists
a75caa09eb1f: Layer already exists
v1: digest: sha256:4c3f2211569346fbe2d1006c18cbea2a4a9dcc1eb3a078608cef70d3a186ec7a size: 1792
```

## <a name="view-the-webhook-logs"></a>A webhooknaplók megtekintése

A rendszerkép replikálása során láthatja az aktiválódó Azure Container Registry webhookokat.

Azon regionális webhookok megtekintéséhez, amelyek akkor jöttek létre, amikor az előző oktatóanyagban a *Web Apps for Containers* alkalmazásban üzembe helyezte a tárolót, lépjen az Azure Portalon a tárolóregisztrációs adatbázisára, majd válassza a **Webhookok** lehetőséget a **SZOLGÁLTATÁSOK** területen.

![A tárolóregisztrációs adatbázisokban található webhookok az Azure Portalon][tutorial-portal-01]

Az egyes webhookok kiválasztásával tekintheti meg a hívásaik és válaszaik előzményeit. Mindkét webhook naplójában látnia kell egy sort, amely a **leküldés** művelethez tartozik. Itt az *USA nyugati* régiójában található webhook naplójában a **leküldés** műveletet a `docker push` váltotta ki az előző lépésben:

![A tárolóregisztrációs adatbázisokban található webhooknaplók az Azure Portalon (az USA nyugati régiója)][tutorial-portal-02]

## <a name="view-the-updated-web-app"></a>Az frissített webalkalmazás megtekintése

A webhookok értesítik a webalkalmazásokat, hogy egy új rendszerkép lett leküldve a beállításjegyzékbe, amely automatikusan üzembe helyezi a frissített tárlót a két regionális webalkalmazásban.

Győződjön meg arról, hogy az alkalmazás mindkét üzemelő példányon frissült. Ehhez nyissa meg mindkét regionális webalkalmazás üzemelő példányát a böngészőjében. Megjegyzés: Az üzembe helyezett webalkalmazás URL-címét az egyes App Service áttekintési lapok jobb felső sarkában találja.

![Az App Service áttekintési lapja az Azure Portalon][tutorial-portal-03]

A frissített alkalmazás megtekintéséhez kattintson a hivatkozásra az App Service áttekintési lapján. Az alábbi kép az *USA nyugati régiójában* futó alkalmazásra mutat példát:

![Egy, az USA nyugati régiójában futó módosított webalkalmazás böngészőnézete][deployed-app-westus-modified]

Győződjön meg arról, hogy a frissített tárolórendszerkép az *USA keleti régiójában* is üzembe lett helyezve. Ehhez tekintse meg a böngészőben.

![Egy, az USA keleti régiójában futó módosított webalkalmazás böngészőnézete][deployed-app-eastus-modified]

Egyetlen `docker push`, lehetővé tette mindkét regionális Web App üzemelő példányában futó webalkalmazás automatikus frissítését. És az Azure Container Registry az egyes üzemelő példányokhoz legközelebb eső adattárakból szolgáltatta a tárolólemezképeket.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban frissítette és leküldte a webalkalmazás tárolójának egy új verzióját a georeplikált beállításjegyzékbe. Az Azure Container Registry webhookjai értesítették a Web Apps for Containers alkalmazást a frissítésről, amely egy helyi lekérést indított a beállításjegyzékek legközelebbi replikájából.

### <a name="acr-build-automated-image-build-and-patch"></a>ACR Build: Automatikus lemezképépítés és javítás

A georeplikáción kívül az ACR Build az Azure Container Registry egy másik funkciója, amely segíthet a tároló üzembe helyezési folyamatának optimalizálásban. A képességeinek megismeréséhez kezdjen az ACR Build áttekintésével:

[Operációs rendszer és keretrendszer javításának automatizálása az ACR Builddel](container-registry-tasks-overview.md)

<!-- IMAGES -->
[deployed-app-eastus-modified]: ./media/container-registry-tutorial-deploy-update/deployed-app-eastus-modified.png
[deployed-app-westus-modified]: ./media/container-registry-tutorial-deploy-update/deployed-app-westus-modified.png
[local-container-01]: ./media/container-registry-tutorial-deploy-update/local-container-01.png
[tutorial-portal-01]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-01.png
[tutorial-portal-02]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-02.png
[tutorial-portal-03]: ./media/container-registry-tutorial-deploy-update/tutorial-portal-03.png