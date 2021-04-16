---
title: 'Rövid útmutató: Az első statikus webhely kiépítése a Azure Static Web Apps'
description: Megtudhatja, hogyan helyezhet üzembe statikus webhelyet Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: fb874c25ab688cc5e6723d1023157b8acd9478b9
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107483844"
---
# <a name="quickstart-building-your-first-static-site-with-azure-static-web-apps"></a>Rövid útmutató: Az első statikus webhely kiépítése Azure Static Web Apps

Azure Static Web Apps közzétesz egy webhelyet, és alkalmazásokat épít fel egy kódtárból. Ebben a rövid útmutatóban egy alkalmazást fog üzembe helyezni az Azure Static Web Apps szolgáltatásban a Visual Studio Code bővítmény használatával.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes próbafiókot.](https://azure.microsoft.com/free)

## <a name="prerequisites"></a>Előfeltételek

- [GitHub](https://github.com)-fiók
- [Azure-fiók](https://portal.azure.com)
- [Visual Studio Code](https://code.visualstudio.com)
- [A Visual Studio Code Azure Static Web Apps-bővítménye](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)
- [A Git telepítése](https://www.git-scm.com/downloads)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

Ezután nyissa meg a Visual Studio Code-et, és nyissa meg **a Fájl > Mappa** megnyitása gombra, és nyissa meg a gépre klónozott adattárat a szerkesztőben.

## <a name="create-a-static-web-app"></a>Statikus webalkalmazás létrehozása

1. Az Azure-bővítmények ablak megnyitásához a Visual Studio Code-ban kattintson a Tevékenység sávon az Azure-emblémára.

    :::image type="content" source="media/getting-started/extension-azure-logo.png" alt-text="Azure-embléma":::

    > [!NOTE]
    > Be kell jelentkeznie az Azure-ba és a GitHubra. Ha még nincs bejelentkezve az Azure-ba és a GitHubra a Visual Studio Code-ban, a bővítmény arra fogja kérni, hogy a létrehozás folyamata során jelentkezzen be mindkét szolgáltatásba.

1. A _Static Web Apps_ válassza a **pluszjelet.**

    :::image type="content" source="media/getting-started/extension-create-button.png" alt-text="Alkalmazás neve":::

1. Megnyílik a parancs a szerkesztő tetején, és felkéri, hogy nevezze el az alkalmazást.

    Adja meg a **my-first-static-web-app** nevet, és nyomja le az **Enter** billentyűt.

    :::image type="content" source="media/getting-started/extension-create-app.png" alt-text="Statikus webalkalmazás létrehozása":::

1. Válassza ki az alkalmazás típusának megfelelő előzetes beállításokat.

    # <a name="no-framework"></a>[Nincs keretrendszer](#tab/vanilla-javascript)
    :::image type="content" source="media/getting-started/extension-presets-no-framework.png" alt-text="Alkalmazásbeállítások: Nincs keretrendszer":::

    Az **alkalmazásfájlok helyeként** adja meg a ./ adatokat.

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="Alkalmazásfájlok helye":::

    Válassza **a Kihagyás most lehetőséget** a Azure Functions API helyeként.

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="Az API helye":::

    A build kimeneti helyeként adja meg a **./** adatokat.

    :::image type="content" source="media/getting-started/extension-build-location.png" alt-text="Az alkalmazás buildkimenetének helye":::

    # <a name="angular"></a>[Angular](#tab/angular)

    Bár van Angular-beállításkészlet,  válassza az Egyéni lehetőséget, hogy megfelelő kimeneti helyet adjon meg az alkalmazás számára.

    :::image type="content" source="media/getting-started/extension-presets-no-framework.png" alt-text="Alkalmazásbeállítások: Angular":::

    Az **alkalmazásfájlok helyeként** adja meg a ./ adatokat.

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="Alkalmazásfájlok helye: Angular":::

    Válassza **a Kihagyás most lehetőséget** a Azure Functions API helyeként.

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="API helye: Angular":::

    A build kimeneti helyeként adja meg **a dist/angular-basic** adatokat.

    :::image type="content" source="media/getting-started/extension-angular.png" alt-text="Az alkalmazás buildkimenetének helye: Angular":::

    # <a name="react"></a>[React](#tab/react)

    :::image type="content" source="media/getting-started/extension-presets-react.png" alt-text="Alkalmazás-beállításkészletek: React":::

    # <a name="vue"></a>[Vue](#tab/vue)

    :::image type="content" source="media/getting-started/extension-presets-vue.png" alt-text="Alkalmazásbeállítások: Vue":::

    ---

1. Válassza ki a legközelebbi helyet, és nyomja le az **Enter** billentyűt.

    :::image type="content" source="media/getting-started/extension-location.png" alt-text="Erőforrás helye":::

1. Az alkalmazás létrehozása után megerősítő értesítés jelenik meg a Visual Studio Code-ban.

    :::image type="content" source="media/getting-started/extension-confirmation.png" alt-text="Létrehozás megerősítése":::

    Ezután kattintson az Open Actions in GitHub (Műveletek **megnyitása a GitHubon) gombra.** Ezen az oldalon az alkalmazás buildállapota látható.

    Ha a GitHub Action művelet befejeződött, megkeresheti a közzétett webhelyet.

1. A webhely böngészőben való megtekintéséhez kattintson a jobb gombbal a projektre a Static Web Apps bővítményben, és válassza a **Webhely tallózása lehetőséget.**

    :::image type="content" source="media/getting-started/extension-browse-site.png" alt-text="Webhely tallózása":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölheti a Azure Static Web Apps-példányt a bővítményen keresztül.

A Visual Studio Code Explorer ablakban térjen vissza a _Static Web Apps_ szakaszhoz, kattintson a jobb gombbal a **my-first-static-web-app** elemre, és válassza a **Törlés lehetőséget.**

:::image type="content" source="media/getting-started/extension-delete.png" alt-text="Alkalmazás törlése":::

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [API hozzáadása](add-api.md)
