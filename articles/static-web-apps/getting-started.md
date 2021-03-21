---
title: 'Rövid útmutató: az első statikus hely felépítése az Azure statikus Web Apps'
description: Ismerje meg, hogyan helyezhet üzembe statikus helyet az Azure statikus Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: 2d7a2dcbbd0b6e9a10ca8af93798bfddbee94ee3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182655"
---
# <a name="quickstart-building-your-first-static-site-with-azure-static-web-apps"></a>Rövid útmutató: az első statikus hely felépítése az Azure statikus Web Apps

Az Azure statikus Web Apps egy GitHub-tárházból származó alkalmazások létrehozásával tesz közzé webhelyeket az éles környezetben. Ebben a rövid útmutatóban egy webalkalmazást helyez üzembe az Azure statikus Web Apps szolgáltatásban a Visual Studio Code bővítménnyel.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes próbaverziós fiókot](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Előfeltételek

- [GitHub](https://github.com)-fiók
- [Azure](https://portal.azure.com) -fiók
- [Visual Studio Code](https://code.visualstudio.com)
- [A Visual Studio Code Azure Static Web Apps-bővítménye](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)
- [A Git telepítése](https://www.git-scm.com/downloads)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

Ezután nyissa meg a Visual Studio Code-ot, és nyissa meg a **fájl > megnyitása mappát** , ahol megnyithatja azt a tárházat, amelyet az imént klónozott a gépre a szerkesztőben.

## <a name="create-a-static-web-app"></a>Statikus webalkalmazás létrehozása

1. Az Azure-bővítmények ablak megnyitásához a Visual Studio Code-ban kattintson a Tevékenység sávon az Azure-emblémára.

    :::image type="content" source="media/getting-started/extension-azure-logo.png" alt-text="Azure-embléma":::

    > [!NOTE]
    > Be kell jelentkeznie az Azure-ba és a GitHubra. Ha még nincs bejelentkezve az Azure-ba és a GitHubra a Visual Studio Code-ban, a bővítmény arra fogja kérni, hogy a létrehozás folyamata során jelentkezzen be mindkét szolgáltatásba.

1. Vigye az egeret a _Static Web Apps_ címke fölé, és kattintson a **pluszjelre**.

    :::image type="content" source="media/getting-started/extension-create-button.png" alt-text="Alkalmazás neve":::

1. A szájpadlás parancs a szerkesztő tetején nyílik meg, és rákérdez az alkalmazás nevének megadására.

    Adja meg a **my-first-static-web-app** nevet, és nyomja le az **Enter** billentyűt.

    :::image type="content" source="media/getting-started/extension-create-app.png" alt-text="Statikus webalkalmazás létrehozása":::

1. Válassza a **fő** ágat, és nyomja le az **Enter** billentyűt.

    :::image type="content" source="media/getting-started/extension-branch.png" alt-text="Ág neve":::

1. Válassza ki az **/** alkalmazás kódjának helyét, majd nyomja le az **ENTER** billentyűt.

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="Alkalmazáskód helye":::

1. A bővítmény az API helyét keresi az alkalmazásban. Ez a cikk nem tartalmaz API-megvalósítást.

    Válassza a **Kihagyás most** lehetőséget, és nyomja le az **Enter** billentyűt.

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="Az API helye":::

1. Válassza ki azt a helyet, ahol a fájlok elkészülhetnek az alkalmazásbeli üzemeléshez.

    # <a name="no-framework"></a>[Nincs keretrendszer](#tab/vanilla-javascript)

    Törölje a mezőt, majd nyomja le az **ENTER** billentyűt.

    :::image type="content" source="media/getting-started/extension-artifact-no-framework.png" alt-text="Alkalmazás fájljainak elérési útja":::

    # <a name="angular"></a>[Angular](#tab/angular)

    Írja be a **dist/szögletes-Basic** értéket, majd nyomja le az **ENTER** billentyűt.

    :::image type="content" source="media/getting-started/extension-artifact-angular.png" alt-text="Angular-alkalmazásfájlok útvonala":::

    # <a name="react"></a>[React](#tab/react)

    Írja be a **build** szót, majd nyomja le az **Enter** billentyűt.

    :::image type="content" source="media/getting-started/extension-artifact-react.png" alt-text="React-alkalmazásfájlok útvonala":::

    # <a name="vue"></a>[Vue](#tab/vue)

    Írja be a **dist** szót, majd nyomja le az **Enter** billentyűt.

    :::image type="content" source="media/getting-started/extension-artifact-vue.png" alt-text="Vue-alkalmazásfájlok útvonala":::

    ---

1. Válassza ki a legközelebbi helyet, és nyomja le az **Enter** billentyűt.

    :::image type="content" source="media/getting-started/extension-location.png" alt-text="Erőforrás helye":::

1. Az alkalmazás létrehozása után megerősítő értesítés jelenik meg a Visual Studio Code-ban.

    :::image type="content" source="media/getting-started/extension-confirmation.png" alt-text="Létrehozás megerősítése":::

1. A Visual Studio Code Explorer ablakban navigáljon az előfizetés nevét tartalmazó csomóponthoz, és bontsa ki azt. Vegye figyelembe, hogy a telepítés befejezéséhez néhány percet is igénybe vehet. Ezután térjen vissza a statikus Web Apps szakaszra, és válassza ki az alkalmazás nevét, majd kattintson a jobb gombbal az én-első-static-Web-App elemre, és válassza a Megnyitás a portálon lehetőséget az alkalmazás megjelenítéséhez a Azure Portal.

    :::image type="content" source="media/getting-started/extension-open-in-portal.png" alt-text="Portál megnyitása":::

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölheti az Azure statikus Web Apps példányát a bővítmény használatával.

A Visual Studio Code Explorer ablakban térjen vissza a _statikus Web Apps_ szakaszhoz, és kattintson a jobb gombbal a **My-First-static-Web-App** elemre, és válassza a **Törlés** lehetőséget.

:::image type="content" source="media/getting-started/extension-delete.png" alt-text="Alkalmazás törlése":::

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [API hozzáadása](add-api.md)
