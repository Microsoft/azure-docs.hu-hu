---
title: 'Oktatóanyag: statikus Webalkalmazás létrehozása az Azure statikus Web Apps villámgyors szolgáltatásával'
description: Ismerje meg, hogyan hozhat létre egy Azure statikus Web Apps webhelyet a Blazer használatával.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: tutorial
ms.date: 09/10/2020
ms.author: cshoe
ms.openlocfilehash: 0086f7f68fd05d6925d19c7ab457fbc125e36be4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96350228"
---
# <a name="tutorial-building-a-static-web-app-with-blazor-in-azure-static-web-apps"></a>Oktatóanyag: statikus Webalkalmazás létrehozása az Azure statikus Web Apps villámgyors szolgáltatásával

Az Azure statikus Web Apps egy GitHub-tárházból származó alkalmazások létrehozásával tesz közzé webhelyeket az éles környezetben. Ebben az oktatóanyagban egy webalkalmazást helyez üzembe az Azure static Web Apps szolgáltatásban a Azure Portal használatával.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes próbaverziós fiókot](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Előfeltételek

- [GitHub](https://github.com)-fiók
- [Azure](https://portal.azure.com) -fiók

## <a name="application-overview"></a>Az alkalmazás áttekintése

Az Azure statikus Web Apps lehetővé teszi a kiszolgáló nélküli háttér által támogatott statikus webalkalmazások létrehozását. A következő oktatóanyag azt mutatja be, hogyan helyezhetők üzembe az időjárási adatokból álló C# Blazer-webalkalmazások.

:::image type="content" source="./media/deploy-blazor/blazor-app-complete.png" alt-text="A Blazer-alkalmazás befejezése":::

Az oktatóanyagban szereplő alkalmazás három különböző Visual Studio-projektből áll:

- **API**: a C# Azure functions alkalmazás, amely megvalósítja az API-végpontot, amely időjárási információkat biztosít a statikus alkalmazásnak. A **WeatherForecastFunction** objektumok tömbjét adja vissza `WeatherForecast` .

- **Client**: az előtér-webszolgáltatások webes szerelvényének projektje. A rendszer egy [tartalék útvonalat](#fallback-route) vezet be annak biztosítására, hogy az összes útvonal a _index.html_ -fájlt szolgálja ki.

- **Shared**: az API-k és az ügyfél-projektek által hivatkozott általános osztályok, amelyek lehetővé teszik az adatok ÁRAMLÁSÁT az API-végpontról az előtér-webalkalmazásba. Az [`WeatherForecast`](https://github.com/staticwebdev/blazor-starter/blob/main/Shared/WeatherForecast.cs) osztály mindkét alkalmazás között meg van osztva.

Ezek a projektek együttesen alkotják a szükséges részeket az API-háttér által támogatott böngészőben futó Blaze web Assembly-alkalmazás létrehozásához.

## <a name="fallback-route"></a>Tartalék útvonal

Az alkalmazás olyan URL-címeket tesz elérhetővé, mint a _/Counter_ és a _/fetchdata_ , amelyek az alkalmazás adott útvonalára vannak leképezve. Mivel ez az alkalmazás egy egyoldalas alkalmazásként lett implementálva, minden útvonal a _index.html_ -fájlt szolgálja ki. Annak biztosítása érdekében, hogy bármely elérési útra vonatkozó kérelem _index.html_ , egy [tartalék útvonal](./routes.md#fallback-routes) kerül megvalósításra az ügyfél-projekt _wwwroot_ mappájában található _routes.js_ fájlban.

```json
{
  "routes": [
    {
      "route": "/*",
      "serve": "/index.html",
      "statusCode": 200
    }
  ]
}
```

A fenti konfiguráció biztosítja, hogy az alkalmazás bármely útvonalára irányuló kérés visszaadja a _index.html_ lapot.

## <a name="create-a-repository"></a>Adattár létrehozása

Ez a cikk egy GitHub-sablon tárházát használja, amely megkönnyíti az első lépéseket. A sablon egy Azure statikus Web Apps üzembe helyezett alapszintű alkalmazást tartalmaz.

1. Győződjön meg arról, hogy be van jelentkezve a GitHubba, és a következő helyre navigálva hozzon létre egy új tárházat:
    - [https://github.com/staticwebdev/blazor-starter/generate](https://github.com/login?return_to=/staticwebdev/blazor-starter/generate)
1. Nevezze el a tárházat **az első-static-Blazer-App**

## <a name="create-a-static-web-app"></a>Statikus webalkalmazás létrehozása

Most, hogy létrejött a tárház, hozzon létre egy statikus webalkalmazást a Azure Portal.

1. Navigáljon a [Azure Portal](https://portal.azure.com)
1. Válassza **az erőforrás létrehozása** lehetőséget.
1. Keressen rá a **Static Web Apps** kifejezésre
1. **Statikus Web Apps kiválasztása (előzetes verzió)**
1. Kattintson a **Létrehozás** elemre.

Az _alapok_ szakaszban kezdje az új alkalmazás konfigurálásával és a GitHub-tárházhoz való csatolásával.

:::image type="content" source="media/deploy-blazor/basics.png" alt-text="Alapbeállítások lap":::

1. Azure- _előfizetés_ kiválasztása
1. Válasszon ki vagy hozzon létre egy új _erőforráscsoportot_
1. Az alkalmazás neve az **első-static-Blazer-App**
    - Az érvényes karakterek az `a-z` (kis- és nagybetűk megkülönböztetése nélkül) `0-9`és az `-`.
1. Válasszon egy Önhöz legközelebb eső _régiót_
1. Válassza ki az **ingyenes** _SKU_ -t
1. Jelölje ki a GitHub gombbal a **bejelentkezést** , és végezze el a hitelesítést a GitHub használatával

Miután bejelentkezett a GitHubba, adja meg a tárház adatait.

:::image type="content" source="media/deploy-blazor/repository-details.png" alt-text="Adattár részletei":::

1. Válassza ki az előnyben részesített _szervezetet_
1. Válassza ki az **én-első-static-Blazer-alkalmazást** az _adattár_ legördülő menüből
1. Válassza ki a **fő** elemet az _ág_ legördülő menüből

    Ha nem lát tárházat, lehetséges, hogy engedélyeznie kell az Azure statikus Web Apps a GitHubon. Keresse meg a GitHub-tárházat, és lépjen a **beállítások > alkalmazások > a OAuth-alkalmazások engedélyezve** lehetőségre, válassza az **Azure statikus Web Apps** lehetőséget, majd válassza a **támogatás** lehetőséget. A szervezeti adattárak esetében a szervezet tulajdonosának kell lennie az engedélyek megadásához.

1. A _Build Details (részletek összeállítása_ ) szakaszban adja meg a Blazer-specifikus konfigurációs adatokat.

    - Válassza a **Blazer** elemet a _Build alapkészletek_ legördülő menüből, és tartsa meg az összes alapértelmezett értéket.

1. Válassza az **Áttekintés + létrehozás** lehetőséget.

    :::image type="content" source="media/deploy-blazor/review-create.png" alt-text="A létrehozás gomb áttekintése":::

1. Válassza a **Létrehozás** lehetőséget.

    :::image type="content" source="media/deploy-blazor/create-button.png" alt-text="Létrehozás gomb":::

1. Válassza az **Erőforrás megnyitása** lehetőséget.

    :::image type="content" source="media/deploy-blazor/resource-button.png" alt-text="Erőforrás megnyitása gomb":::

## <a name="view-the-website"></a>Webhely megtekintése

A statikus alkalmazások üzembe helyezésének két aspektusa van. Az első azokat a mögöttes Azure-erőforrásokat építi ki, amelyekből az alkalmazás áll. A második egy GitHub Actions-munkafolyamat, amely létrehozza és közzéteszi az alkalmazást.

Az új statikus helyhez való csatlakozás előtt az üzembe helyezési buildnek először futnia kell.

A statikus Web Apps áttekintése ablak a webalkalmazással való interakciót segítő hivatkozásokat jelenít meg.

:::image type="content" source="./media/deploy-blazor/overview-window.png" alt-text="Áttekintő ablak":::

1. A szalagcímre kattintva _megtekintheti a GitHub-műveletek_ futtatásának állapotát a tárházon futó GitHub-műveletekkel. Miután meggyőződött arról, hogy befejeződött a telepítési feladatok befejezése, a generált URL-címen keresztül megnyithatja a webhelyet.

2. Miután befejeződött a GitHub-műveletek munkafolyamata, kiválaszthatja az _URL-_ hivatkozást a webhely új lapon való megnyitásához.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, az alábbi lépésekkel törölheti az Azure statikus Web Apps példányát:

1. Nyissa meg az [Azure Portalt](https://portal.azure.com)
1. Keresés az **én-első-static-Blazer-App** a felső keresési sávon
1. Válassza ki az alkalmazás nevét
1. Válassza a **Törlés** gombot
1. Válassza az **Igen** lehetőséget a törlési művelet megerősítéséhez

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Hitelesítés és engedélyezés](./authentication-authorization.md)
