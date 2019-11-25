---
title: Tutorial - Deploy from geo-replicated registry
description: Deploy a Linux-based web app to two different Azure regions using a container image from a geo-replicated Azure container registry. Ez egy háromrészes sorozat második része.
ms.topic: tutorial
ms.date: 08/20/2018
ms.custom: seodec18, mvc
ms.openlocfilehash: 5b075e1065ef8c30837000f490cc93525b4b61cc
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456103"
---
# <a name="tutorial-deploy-a-web-app-from-a-geo-replicated-azure-container-registry"></a>Tutorial: Deploy a web app from a geo-replicated Azure container registry

Ez egy háromrészes oktatóanyag-sorozat második része. Az [első részben](container-registry-tutorial-prepare-registry.md) létrehozott egy privát, georeplikált tárolóregisztrációs adatbázist, valamint felépített egy tárolórendszerképet a forrásból, és leküldte a regisztrációs adatbázisba. Az ebben a cikkben leírtakat követve kihasználhatja a georeplikált regisztrációs adatbázis hálózatközeli aspektusát a tároló két, különböző Azure-régióban található webalkalmazás-példányban történő üzembe helyezésével. Ekkor mindegyik példány a legközelebbi regisztrációs adatbázisból kéri le a tárolólemezképet.

Az oktatóanyag-sorozat második része a következő lépésekből áll:

> [!div class="checklist"]
> * Tárolórendszerkép üzembe helyezése két *Web Apps for Containers*-példányban
> * Az üzembe helyezett alkalmazás ellenőrzése

Ha még nem hozott létre a georeplikált regisztrációs adatbázist, és még nem küldte le a tárolóalapú mintaalkalmazás rendszerképét a regisztrációs adatbázisba, térjen vissza a sorozat előző, [Georeplikált Azure tárolóregisztrációs adatbázis előkészítése](container-registry-tutorial-prepare-registry.md) című oktatóanyagára.

A sorozat következő részében frissíti az alkalmazást, majd leküldi a frissített tárolórendszerképet a regisztrációs adatbázisba. Végül pedig tallózással megkeresi a futó webalkalmazás-példányokat az automatikusan végrehajtott módosítások megtekintéséhez, így az Azure Container Registry georeplikációja és a webhookok is láthatók működés közben.

## <a name="automatic-deployment-to-web-apps-for-containers"></a>Automatikus üzembe helyezés a Web Apps for Containersben

Az Azure Container Registry támogatja a tárolóalapú alkalmazások a [Web Apps for Containersben](../app-service/containers/index.yml) való közvetlen üzembe helyezését. Ebben az oktatóanyagban az Azure Portallal üzembe helyezi az előző oktatóanyagban létrehozott tárolórendszerképet két, különböző Azure-régiókban található webalkalmazás-csomagban.

Ha üzembe helyez egy webalkalmazást a regisztrációs adatbázis egyik tárolórendszerképéből, és van egy georeplikált regisztrációs adatbázisa ugyanabban a régióban, az Azure Container Registry létrehoz Önnek egy [webhookot](container-registry-webhook.md) a rendszerkép üzembe helyezéséhez. Amikor leküld egy új rendszerképet a tárolóadattárba, a webhook észleli a módosítást, és automatikusan üzembe helyezi az új tárolórendszerképet a webalkalmazásban.

## <a name="deploy-a-web-app-for-containers-instance"></a>Web Apps for Containers-példány üzembe helyezése

Ebben a lépésben létrehoz egy Web App for Containers-példányt az *USA nyugati régiójában*.

Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és lépjen az előző oktatóanyagban létrehozott regisztrációs adatbázisra.

Válassza ki az **Adattárak** > **acr-helloworld** elemet, kattintson a jobb gombbal a **v1** címkére a **Címkék** területen, majd válassza az **Üzembe helyezés a webalkalmazásban** lehetőséget:

![Üzembe helyezés az App Service-ben az Azure Portalon][deploy-app-portal-01]

Ha az „Üzembe helyezés a webalkalmazásban” lehetőség nem érhető el, előfordulhat, hogy nem engedélyezte a beállításjegyzék rendszergazdai felhasználóját a [Tároló-beállításjegyzék létrehozása](container-registry-tutorial-prepare-registry.md#create-a-container-registry) első oktatóanyagának utasításai szerint. A rendszergazdai felhasználót az Azure Portalon engedélyezheti a **Beállítások** > **Hozzáférési kulcsok** menüben.

Az „Üzembe helyezés a webalkalmazásban” kiválasztása után megjelenített **Web App for Containers** területen adja meg a következő értékeket a beállításokhoz:

| Beállítás | Value (Díj) |
|---|---|
| **Hely neve** | A webalkalmazás globálisan egyedi neve. Ebben a példában az `<acrName>-westus` formátumot használjuk, hogy könnyen azonosítani lehessen a regisztrációs adatbázist és a régiót, amelyből a webalkalmazás telepítve lesz. |
| **Erőforráscsoport** | **Meglévő használata** > `myResourceGroup` |
| **App Service-csomag/Hely** | Hozzon létre `plan-westus` nevű új csomagot az **USA nyugati régiójában**. |
| **Rendszerkép** | `acr-helloworld:v1` |
| **Operációs rendszer** | Linux |

> [!NOTE]
> When you create a new app service plan to deploy your containerized app, a default plan is automatically selected to host your application. The default plan depends on the operating system setting.

Válassza a **Létrehozás** lehetőséget a webalkalmazás az *USA nyugati régiójában* való üzembe helyezéséhez.

![Linuxon futó webalkalmazás konfigurációja az Azure Portalon][deploy-app-portal-02]

## <a name="view-the-deployed-web-app"></a>Az üzembe helyezett webalkalmazás megtekintése

Az üzembe helyezés befejezése után megtekintheti a futó alkalmazást, ha megnyitja az URL-címét a böngészőben.

A Portalon válassza ki az **App Services** elemet, majd az előző lépésben üzembe helyezett webalkalmazást. Ebben a példában a webalkalmazás neve *uniqueregistryname-westus*.

A futó alkalmazás böngészőben való megtekintéséhez kattintson a webalkalmazás hiperhivatkozással ellátott URL-címére az **App Service** áttekintésének jobb felső részén.

![Linuxon futó webalkalmazás konfigurációja az Azure Portalon][deploy-app-portal-04]

A Docker-rendszerkép georeplikált tárolóregisztrációs adatbázisból való üzembe helyezése után a webhely egy képet jelenít meg, amely a tárolóregisztrációs adatbázist üzemeltető Azure-régiót ábrázolja.

![Az üzembe helyezett webalkalmazás, egy böngészőben megtekintve][deployed-app-westus]

## <a name="deploy-second-web-app-for-containers-instance"></a>A második Web Apps for Containers-példány üzembe helyezése

Kövesse az előző szakaszban leírt eljárást egy második webalkalmazás az *USA keleti régiójában* való üzembe helyezéséhez. A **Web App for Containers** területen adja meg a következő értékeket:

| Beállítás | Value (Díj) |
|---|---|
| **Hely neve** | A webalkalmazás globálisan egyedi neve. Ebben a példában az `<acrName>-eastus` formátumot használjuk, hogy könnyen azonosítani lehessen a regisztrációs adatbázist és a régiót, amelyből a webalkalmazás telepítve lesz. |
| **Erőforráscsoport** | **Meglévő használata** > `myResourceGroup` |
| **App Service-csomag/Hely** | Hozzon létre `plan-eastus` nevű új csomagot az **USA keleti régiójában**. |
| **Rendszerkép** | `acr-helloworld:v1` |
| **Operációs rendszer** | Linux |

Válassza a **Létrehozás** lehetőséget a webalkalmazás az *USA keleti régiójában* való üzembe helyezéséhez.

![Linuxon futó webalkalmazás konfigurációja az Azure Portalon][deploy-app-portal-06]

## <a name="view-the-deployed-web-app"></a>Az üzembe helyezett webalkalmazás megtekintése

A korábbiakhoz hasonlóan megtekintheti a futó alkalmazást, ha megnyitja az URL-címét a böngészőben.

A Portalon válassza ki az **App Services** elemet, majd az előző lépésben üzembe helyezett webalkalmazást. Ebben a példában a webalkalmazás neve *uniqueregistryname-eastus*.

A futó alkalmazás böngészőben való megtekintéséhez kattintson a webalkalmazás hiperhivatkozással ellátott URL-címére az **App Service áttekintésének** jobb felső részén.

![Linuxon futó webalkalmazás konfigurációja az Azure Portalon][deploy-app-portal-07]

A Docker-rendszerkép georeplikált tárolóregisztrációs adatbázisból való üzembe helyezése után a webhely egy képet jelenít meg, amely a tárolóregisztrációs adatbázist üzemeltető Azure-régiót ábrázolja.

![Az üzembe helyezett webalkalmazás, egy böngészőben megtekintve][deployed-app-eastus]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban üzembe helyezett két Web App for Containers-példányt egy georeplikált Azure tárolóregisztrációs adatbázisból.

Folytassa a következő oktatóanyaggal, amelyben frissít és üzembe helyez egy új tárolórendszerképet a tárolóregisztrációs adatbázisban, majd ellenőrzi, hogy a két régióban futó webalkalmazások automatikusan frissültek-e.

> [!div class="nextstepaction"]
> [Georeplikált tárolórendszerkép frissítésének központi telepítése](./container-registry-tutorial-deploy-update.md)

<!-- IMAGES -->
[deploy-app-portal-01]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-01.png
[deploy-app-portal-02]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-02.png
[deploy-app-portal-03]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-03.png
[deploy-app-portal-04]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-04.png
[deploy-app-portal-05]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-05.png
[deploy-app-portal-06]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-06.png
[deploy-app-portal-07]: ./media/container-registry-tutorial-deploy-app/deploy-app-portal-07.png
[deployed-app-westus]: ./media/container-registry-tutorial-deploy-app/deployed-app-westus.png
[deployed-app-eastus]: ./media/container-registry-tutorial-deploy-app/deployed-app-eastus.png