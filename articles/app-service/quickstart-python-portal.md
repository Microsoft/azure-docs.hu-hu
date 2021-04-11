---
title: 'Gyors útmutató: Python-alkalmazás létrehozása a Azure Portal'
description: Ismerkedjen meg Azure App Service az első Python-alkalmazásnak egy Linux-tárolóba való üzembe helyezésével App Service a Azure Portal használatával.
ms.topic: quickstart
ms.date: 04/01/2021
ms.custom: devx-track-python
robots: noindex
ms.openlocfilehash: 42f29152521da7bf90a550248e8429e51b728b24
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012356"
---
# <a name="quickstart-create-a-python-app-using-azure-app-service-on-linux-azure-portal"></a>Gyors útmutató: Python-alkalmazás létrehozása Linuxon Azure App Service használatával (Azure Portal)

Ebben a rövid útmutatóban egy Python-webalkalmazást helyez üzembe a [Linuxon](overview.md#app-service-on-linux), az Azure rugalmasan méretezhető, önjavító webes üzemeltetési szolgáltatásának app Service. A Azure Portal segítségével üzembe helyezhet egy mintát a Lombiktal vagy a Django keretrendszerek használatával. A konfigurált webalkalmazás olyan alapszintű App Service szintet használ, amely kis költségekkel jár az Azure-előfizetésében.

## <a name="configure-accounts"></a>Fiókok konfigurálása

- Ha még nem rendelkezik aktív előfizetéssel rendelkező Azure-fiókkal, [hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- Ha nem rendelkezik GitHub-fiókkal, látogasson el a [GitHub.com](https://github.com) webhelyre, és hozzon létre egyet. 

## <a name="fork-the-sample-github-repository"></a>A minta GitHub-tárházának elágazása

1. Nyissa meg a [GitHub.com](https://github.com) , és jelentkezzen be.

1. Navigáljon a következő minta-Tárházak egyikéhez:
    - [Lombik Hello World](https://github.com/Azure-Samples/python-docs-hello-world)
    - [Django Hello World](https://github.com/Azure-Samples/python-docs-hello-django)

1. A GitHub oldal jobb felső sarkában válassza az **elágazás** lehetőséget, hogy másolatot készítsen a tárházról a saját GitHub-fiókjában:

    ![GitHub-elágazási parancs](media/quickstart-python-portal/github-fork-command.png)

    Az Azure használatához hozzáféréssel kell rendelkeznie a tárházat tartalmazó GitHub-szervezethez. A minta saját GitHub-fiókba való elágazásával automatikusan rendelkezik a szükséges hozzáféréssel, és módosíthatja is a kódot.

## <a name="provision-the-app-service-web-app"></a>A App Service webalkalmazás kiépítése

Az App Service webalkalmazás az a webkiszolgáló, amelyre telepíteni kívánja a kódot.

1. Ha szükséges, nyissa meg a Azure Portalt [https://portal.azure.com](https://portal.azure.com) , és jelentkezzen be.

1. A Azure Portal tetején található keresősáv alatt írja be a "App Service" kifejezést, majd válassza a **app Services** lehetőséget.

    ![A portálon található keresősáv és a App Service kiválasztása](media/quickstart-python-portal/portal-search-bar.png)

1. A **app Services** lapon válassza a "**+ Hozzáadás**:

    ![App Service parancs hozzáadása](media/quickstart-python-portal/add-app-service.png)

1. A **create Web App (Webalkalmazás létrehozása** ) lapon hajtsa végre a következő műveleteket:
    
    | Mező | Művelet |
    | --- | --- |
    | Előfizetés | Válassza ki a használni kívánt Azure-előfizetést. |
    | Erőforráscsoport | Válassza az **új létrehozása** lehetőséget a legördülő lista alatt. Az előugró ablakban írja be a "AppService-PythonQuickstart" kifejezést, és válassza **az OK gombot**. |
    | Name | Adjon meg egy olyan nevet, amely az összes Azure-ban egyedi, jellemzően a személyes vagy a vállalati nevek kombinációját használja, például: *contoso-testapp-123*. |
    | Közzététel | Válassza a **Kód** lehetőséget. |
    | Futtatókörnyezet verme | Válassza a **Python 3,8** lehetőséget. |
    | Operációs rendszer | Válassza a **Linux** lehetőséget (a Python csak Linux rendszeren támogatott). |
    | Region | Válasszon egy Önhöz közeli régiót. |
    | Linux-csomag | Válasszon ki egy kilépő App Service csomagot, vagy használja az **új létrehozása** lehetőséget egy új létrehozásához. Javasoljuk, hogy az **alapszintű B1** -csomagot használja. |

    ![Webalkalmazás-űrlap létrehozása a Azure Portal](media/quickstart-python-portal/create-web-app.png)

1. A lap alján válassza a **felülvizsgálat + létrehozás** lehetőséget, tekintse át a részleteket, majd válassza a **Létrehozás** lehetőséget.

1. A kiépítés befejezésekor válassza az **erőforrás** megnyitása lehetőséget az új App Service oldal megnyitásához. A webalkalmazás ezen a ponton csak egy alapértelmezett lapot tartalmaz, ezért a következő lépés a mintakód üzembe helyezését végzi.

Problémák léptek fel? [Tudassa velünk](https://aka.ms/FlaskPortalQuickstartHelp).

## <a name="deploy-the-sample-code"></a>A mintakód üzembe helyezése

1. A Azure Portal webalkalmazás lapján válassza a **központi telepítési központ** elemet:
    
    ![Központi telepítési központ parancs a App Service menüben](media/quickstart-python-portal/deployment-center-command.png)


1. A **központi telepítési központ** lapon válassza a **Beállítások** lapot, ha még nincs megnyitva:

    ![Központi telepítési központ beállításai lap](media/quickstart-python-portal/deployment-center-settings-tab.png)

1. A **forrás** területen válassza a **GitHub** lehetőséget, majd a megjelenő **GitHub** -űrlapon hajtsa végre a következő műveleteket:

    | Mező | Művelet |
    | --- | --- |
    | Bejelentkezve mint | Ha még nincs bejelentkezve a GitHubba, jelentkezzen be most, vagy válassza a **fiók módosítása* lehetőséget, ha szükséges. |
    | Szervezet | Szükség esetén válassza ki a GitHub-szervezetet. |
    | Adattár | Válassza ki a korábban összeválogatott adattár nevét, vagy a **Python-docs-Hello-World** (lombik) vagy a **Python-docs-Hello-Django** (Django) nevet. |
    | Elágaztatás | Válassza a **Main (fő**) lehetőséget. |

    ![Központi telepítési központ GitHub-forrásának konfigurációja](media/quickstart-python-portal/deployment-center-configure-github-source.png)

1. A lap tetején válassza a **Mentés** lehetőséget a beállítások alkalmazásához.:

    ![A GitHub-forrás konfigurációjának mentése a központi telepítési központban](media/quickstart-python-portal/deployment-center-configure-save.png)

1. A központi telepítés állapotának megtekintéséhez válassza a **naplók** lapot. A minta létrehozása és üzembe helyezése néhány percet vesz igénybe, és a folyamat során további naplók is megjelennek. Befejezésekor a naplóknak a sikeres állapotot **(aktív)** kell tükröznie:

    ![A központi telepítési központ naplói lap](media/quickstart-python-portal/deployment-center-logs.png)

Problémák léptek fel? [Tudassa velünk](https://aka.ms/FlaskPortalQuickstartHelp).

## <a name="browse-to-the-app"></a>Az alkalmazás megkeresése tallózással

1. Az üzembe helyezés befejezése után a bal oldali menüben válassza az **Áttekintés** lehetőséget, hogy visszatérjen a webalkalmazás főoldalára.

1. Válassza ki a webalkalmazás címét tartalmazó **URL-** címet:

    ![Webalkalmazás URL-címe az Áttekintés oldalon](media/quickstart-python-portal/web-app-url.png)

1. Ellenőrizze, hogy az alkalmazás kimenete "Hello, World!":

    ![A kezdeti üzembe helyezést követően futó alkalmazás](media/quickstart-python-portal/web-app-first-deploy-results.png)

Problémák léptek fel? Először olvassa el a [hibaelhárítási útmutatót](configure-language-python.md#troubleshooting), ha nem, [tudassa velünk](https://aka.ms/FlaskPortalQuickstartHelp).

## <a name="make-a-change-and-redeploy"></a>Módosítás és újbóli üzembe helyezés

Mivel App Service kapcsolódott a tárházhoz, a rendszer automatikusan a webalkalmazásba helyezi el a forrás-adattárra vonatkozóan véglegesített módosításokat.

1. A GitHubon közvetlenül is végrehajthatja a módosításokat, vagy az adattárat helyileg is elvégezheti, végrehajthatja és véglegesítheti a módosításokat, majd leküldheti ezeket a módosításokat a GitHubra. Bármelyik módszer a App Servicehoz csatlakoztatott adattár módosítását eredményezi.

1. Az **elágazó tárházban** módosítsa az alkalmazás üzenetét a "Hello, World!" kifejezésre. a "Hello, Azure!" az alábbiak szerint:
    - Lombik (Python-docs-Hello-World minta): változtassa meg a szöveges karakterláncot a *Application.py* fájl 6. sorában.
    - Django (Python-docs-Hello-Django minta): a *Hello* mappában található *views.py* -fájl 5. sorában módosítsa a szöveges karakterláncot.

1. Véglegesítse a módosítást a tárházban.

    Ha helyi klónt használ, a módosításokat a GitHubon is elküldheti.

1. A webalkalmazáshoz tartozó Azure Portal térjen vissza a **központi telepítési központba**, válassza a **naplók** lapot, és jegyezze fel az új központi telepítési tevékenységet, amelynek a végrehajtásához szükséges.

1. Ha a telepítés befejeződött, térjen vissza a webalkalmazás **Áttekintés** lapjára, nyissa meg ismét a webalkalmazás URL-címét, és figyelje meg az alkalmazás módosításait:

    ![Az újratelepítést követően futó alkalmazás](media/quickstart-python-portal/web-app-second-deploy-results.png)

Problémák léptek fel? Először olvassa el a [hibaelhárítási útmutatót](configure-language-python.md#troubleshooting), ha nem, [tudassa velünk](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az előző lépésekben létrehozta az Azure-erőforrásokat egy "AppService-PythonQuickstart" nevű erőforráscsoporthoz, amely a webalkalmazás *Áttekintés** oldalán látható. Ha megtartja a webalkalmazást, további költségek merülnek fel (lásd: [app Service díjszabása](https://azure.microsoft.com/pricing/details/app-service/linux/)).

Ha nem várható, hogy a jövőben szüksége lenne ezekre az erőforrásokra, válassza ki az erőforráscsoport nevét a Web App Overview (webalkalmazás **áttekintő** ) lapján, és navigáljon az erőforráscsoportok áttekintéséhez. Ezután válassza az **erőforráscsoport törlése** elemet, és kövesse az utasításokat.

![Az erőforráscsoport törlése](media/quickstart-python-portal/delete-resource-group.png)


Problémák léptek fel? [Tudassa velünk](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Python (Django) webalkalmazás és PostgreSQL](/azure/developer/python/tutorial-python-postgresql-app-portal)

> [!div class="nextstepaction"]
> [Python-alkalmazás konfigurálása](configure-language-python.md)

> [!div class="nextstepaction"]
> [Felhasználói bejelentkezés hozzáadása egy Python-webalkalmazáshoz](../active-directory/develop/quickstart-v2-python-webapp.md)

> [!div class="nextstepaction"]
> [Oktatóanyag: Python-alkalmazás futtatása egyéni tárolóban](tutorial-custom-container.md)
