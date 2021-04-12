---
title: 'Rövid útmutató: az első statikus webalkalmazás felépítése az Azure statikus Web Apps a Azure Portal használatával'
description: Megtudhatja, hogyan helyezhet üzembe statikus helyet az Azure statikus Web Apps a Azure Portal használatával.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 09/03/2020
ms.author: cshoe
ms.openlocfilehash: b19c37a45131e3689fdc9726d186f220ab5ba9fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103149528"
---
# <a name="quickstart-building-your-first-static-site-in-the-azure-portal"></a>Rövid útmutató: az első statikus hely felépítése a Azure Portal

Az Azure statikus Web Apps egy GitHub-tárházból származó alkalmazások létrehozásával tesz közzé webhelyeket az éles környezetben. Ebben a rövid útmutatóban egy webalkalmazást helyez üzembe az Azure static Web Apps szolgáltatásban a Azure Portal használatával.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes próbaverziós fiókot](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Előfeltételek

- [GitHub](https://github.com)-fiók
- [Azure](https://portal.azure.com) -fiók

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

## <a name="create-a-static-web-app"></a>Statikus webalkalmazás létrehozása

Most, hogy létrejött az adattár, létrehozhat egy statikus webalkalmazást a Azure Portal.

1. Navigáljon a [Azure Portal](https://portal.azure.com)
1. Válassza **az erőforrás létrehozása** lehetőséget.
1. Keressen rá a **Static Web Apps** kifejezésre
1. **Statikus Web Apps kiválasztása (előzetes verzió)**
1. Kattintson a **Létrehozás** elemre.

Az _alapok_ szakaszban kezdje az új alkalmazás konfigurálásával és a GitHub-tárházhoz való csatolásával.

:::image type="content" source="media/getting-started-portal/basics-tab.png" alt-text="Alapismeretek szakasz":::

1. Azure- _előfizetés_ kiválasztása
1. Válasszon ki vagy hozzon létre egy új _erőforráscsoportot_
1. Nevezze el az alkalmazás **az első-statikus-Web-App** nevet.
      1. Az érvényes karakterek az `a-z` (kis- és nagybetűk megkülönböztetése nélkül) `0-9`és az `-`.
1. Válasszon egy Önhöz legközelebb eső _régiót_
1. Válassza ki az **ingyenes** _SKU_ -t
1. Jelölje ki a GitHub gombbal a **bejelentkezést** , és végezze el a hitelesítést a GitHub használatával

Miután bejelentkezett a GitHubba, adja meg a tárház adatait.

:::image type="content" source="media/getting-started-portal/repository-details.png" alt-text="Adattár részletei":::

1. Válassza ki az előnyben részesített _szervezetet_
1. Válassza a **saját-első-web-static-app** elemet az _adattár_ legördülő menüből.
1. Válassza ki a **fő** elemet az _ág_ legördülő menüből

> [!NOTE]
> Ha nem lát tárházat, lehetséges, hogy engedélyeznie kell az Azure statikus Web Apps a GitHubon. Keresse meg a GitHub-tárházat, és lépjen a **beállítások > alkalmazások > a OAuth-alkalmazások engedélyezve** lehetőségre, válassza az **Azure statikus Web Apps** lehetőséget, majd válassza a **támogatás** lehetőséget. A szervezeti adattárak esetében a szervezet tulajdonosának kell lennie az engedélyek megadásához.

1. A _Build Details (részletek összeállítása_ ) szakaszban adja meg az előnyben részesített kezelőfelületi keretrendszerhez tartozó konfigurációs adatokat.

    # <a name="no-framework"></a>[Nincs keretrendszer](#tab/vanilla-javascript)

    1. Válassza az **Egyéni** lehetőséget a _Build-előállítók_ legördülő listából
    1. Az alapértelmezett érték megtartása az _alkalmazás helye_ mezőben
    1. Az alapértelmezett érték törlése az _API helye_ mezőben
    1. Hagyja üresen az alkalmazás-összetevő _helye_ mezőt

    # <a name="angular"></a>[Angular](#tab/angular)

    1. Válassza ki a _kiépítési készletek_ legördülő menü **szögletes** elemét
    1. Az alapértelmezett érték megtartása az _alkalmazás helye_ mezőben
    1. Az alapértelmezett érték törlése az _API helye_ mezőben
    1. Az alkalmazás-összetevő _helye_ mezőben adja meg a **dist/szögletes-Basic** értéket

    # <a name="react"></a>[React](#tab/react)

    1. Válassza a **reagálás** lehetőséget a _Build-előállítók_ legördülő menüből
    1. Az alapértelmezett érték megtartása az _alkalmazás helye_ mezőben
    1. Az alapértelmezett érték törlése az _API helye_ mezőben
    1. Írja be a **Build** értéket az alkalmazás-összetevő _helye_ mezőbe

    # <a name="vue"></a>[Vue](#tab/vue)

    1. **Vue.js** kiválasztása a _Build-előállítók_ legördülő menüből
    1. Az alapértelmezett érték megtartása az _alkalmazás helye_ mezőben
    1. Az alapértelmezett érték törlése az _API helye_ mezőben
    1. Az alapértelmezett érték megtartása az alkalmazás-összetevő _helye_ mezőben

    ---

1. Válassza az **Áttekintés + létrehozás** lehetőséget.

    :::image type="content" source="media/getting-started-portal/review-create.png" alt-text="A létrehozás gomb áttekintése":::

    > [!NOTE]
    > A [munkafolyamat-fájl](github-actions-workflow.md) szerkesztésével módosíthatja ezeket az értékeket az alkalmazás létrehozása után.

1. Válassza a **Létrehozás** lehetőséget.

    :::image type="content" source="media/getting-started-portal/create-button.png" alt-text="Létrehozás gomb":::

1. Válassza az **Erőforrás megnyitása** lehetőséget.

    :::image type="content" source="media/getting-started-portal/resource-button.png" alt-text="Erőforrás megnyitása gomb":::

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, az alábbi lépésekkel törölheti az Azure statikus Web Apps példányát:

1. Nyissa meg az [Azure Portalt](https://portal.azure.com)
1. Keresés az **én-első-web-static-app** a felső keresési sávon
1. Válassza ki az alkalmazás nevét
1. Kattintson a **Törlés** gombra
1. Válassza az **Igen** lehetőséget a törlési művelet megerősítéséhez (ez a művelet eltarthat néhány másodpercig)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [API hozzáadása](add-api.md)
