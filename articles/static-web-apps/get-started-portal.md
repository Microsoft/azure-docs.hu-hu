---
title: 'Rövid útmutató: Az első statikus webalkalmazás Azure Static Web Apps a Azure Portal'
description: Megtudhatja, hogyan helyezhet üzembe statikus webhelyet Azure Static Web Apps a Azure Portal.
services: static-web-apps
author: craigshoemaker
ms.author: cshoe
ms.date: 09/03/2020
ms.topic: quickstart
ms.service: static-web-apps
ms.custom:
- mode-portal
ms.openlocfilehash: 78af2290a2e71d349303d3913f8a40510eb9c6a0
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531591"
---
# <a name="quickstart-building-your-first-static-site-in-the-azure-portal"></a>Rövid útmutató: Az első statikus webhely Azure Portal

Azure Static Web Apps közzétesz egy webhelyet egy éles környezetben úgy, hogy alkalmazásokat hoz létre egy GitHub-adattárból. Ebben a rövid útmutatóban egy webalkalmazást helyez üzembe az Azure Static Web Apps szolgáltatásban a Azure Portal.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes próbafiókot.](https://azure.microsoft.com/free)

## <a name="prerequisites"></a>Előfeltételek

- [GitHub](https://github.com)-fiók
- [Azure-fiók](https://portal.azure.com)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

## <a name="create-a-static-web-app"></a>Statikus webalkalmazás létrehozása

Most, hogy létrehozta az adattárat, létrehozhat egy statikus webalkalmazást a Azure Portal.

1. Lépjen az [Azure Portalra](https://portal.azure.com)
1. Válassza **az Erőforrás létrehozása lehetőséget**
1. Keressen rá a **Static Web Apps** kifejezésre
1. Válassza **Static Web Apps (előzetes verzió) lehetőséget**
1. Kattintson a **Létrehozás** elemre.

Az Alapvető _beállítások szakaszban_ először konfigurálja az új alkalmazást, és csatolja egy GitHub-adattárhoz.

:::image type="content" source="media/getting-started-portal/basics-tab.png" alt-text="Alapvető funkciók szakasz":::

1. Az _Azure-előfizetés kiválasztása_
1. Új erőforráscsoport kiválasztása _vagy létrehozása_
1. Az alkalmazásnak nevezze el **a my-first-static-web-app nevet.**
      1. Az érvényes karakterek az `a-z` (kis- és nagybetűk megkülönböztetése nélkül) `0-9`és az `-`.
1. Válassza ki az _Önhöz_ legközelebbi régiót
1. Válassza ki az **ingyenes** _termékváltozatot_
1. Válassza a **Bejelentkezés GitHubbal gombot,** és hitelesítse magát a GitHubbal

Miután bejelentkezik a GitHubbal, adja meg az adattár adatait.

:::image type="content" source="media/getting-started-portal/repository-details.png" alt-text="Adattár részletei":::

1. Válassza ki a kívánt _szervezetet_
1. Válassza **a my-first-web-static-app** lehetőséget az _Adattár_ legördülő menüből
1. Fő **kiválasztása** a _Branch (Ág)_ legördülő menüből

> [!NOTE]
> Ha nem lát adattárakat, előfordulhat, hogy engedélyeznie kell a Azure Static Web Apps GitHubon. Keresse meg a GitHub-adattárat, és lépjen a **Settings > Applications > Authorized OAuth Apps (Engedélyezett OAuth-alkalmazások)** elemre, válassza a **Azure Static Web Apps** lehetőséget, majd válassza az Grant (Engedély) **lehetőséget.** A szervezeti adattárakhoz az engedélyek megadásához a szervezet tulajdonosának kell lennie.

1. A _Build details (Build részletei) szakaszban_ adja meg az Ön által előnyben részesített előoldali keretrendszerre jellemző konfigurációs adatokat.

    # <a name="no-framework"></a>[Nincs keretrendszer](#tab/vanilla-javascript)

    1. Az **Előre beállított buildek** _legördülő menüben_ válassza az Egyéni lehetőséget
    1. Tartsa meg az alapértelmezett értéket az Alkalmazás _helye mezőben_
    1. Törölje az alapértelmezett értéket az _API helye mezőből_
    1. Hagyja üresen _az Alkalmazás-összetevő_ helye mezőt

    # <a name="angular"></a>[Angular](#tab/angular)

    1. Az Előre beállított buildek legördülő _menüből_ válassza az **Angular** lehetőséget
    1. Tartsa meg az alapértelmezett értéket az Alkalmazás _helye mezőben_
    1. Törölje az alapértelmezett értéket az _API helye mezőből_
    1. Írja **be a dist/angular-basic** parancsot az _Alkalmazás-összetevő helye mezőbe_

    # <a name="react"></a>[React](#tab/react)

    1. A **Build Presets** (Előzetes _buildbeállítások) legördülő menüben_ válassza a React (Reagálás) lehetőséget
    1. Tartsa meg az alapértelmezett értéket az Alkalmazás _helye mezőben_
    1. Törölje az alapértelmezett értéket az _API helye mezőből_
    1. Írja **be a build** parancsot az _Alkalmazás-összetevő helye mezőbe_

    # <a name="vue"></a>[Vue](#tab/vue)

    1. Válassza **Vue.js** lehetőséget a _Build Presets (Előzetes beállítások összeállítása)_ legördülő menüből
    1. Tartsa meg az alapértelmezett értéket az _Alkalmazás helye mezőben_
    1. Törölje az alapértelmezett értéket az _API-hely mezőből_
    1. Tartsa meg az alapértelmezett értéket az _Alkalmazás-összetevő helye mezőben_

    ---

1. Válassza az **Áttekintés + létrehozás** lehetőséget.

    :::image type="content" source="media/getting-started-portal/review-create.png" alt-text="A Létrehozás gomb áttekintése":::

    > [!NOTE]
    > Ezeket az értékeket az alkalmazás létrehozása után [szerkesztheti](github-actions-workflow.md) a munkafolyamat-fájlban.

1. Válassza a **Létrehozás** lehetőséget.

    :::image type="content" source="media/getting-started-portal/create-button.png" alt-text="Létrehozás gomb":::

1. Válassza az **Erőforrás megnyitása** lehetőséget.

    :::image type="content" source="media/getting-started-portal/resource-button.png" alt-text="Erőforrás megnyitása gomb":::

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, a következő lépésekkel törölheti Azure Static Web Apps-példányt:

1. Nyissa meg az [Azure Portalt](https://portal.azure.com)
1. Keressen rá **a my-first-web-static-app** kifejezésre a felső keresősávban
1. Az alkalmazás nevének kiválasztása
1. Válassza a **Törlés** gombot
1. Válassza **az Igen** lehetőséget a törlési művelet megerősítéséhez (a művelet befejezése igénybe vehet néhány percet)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [API hozzáadása](add-api.md)
