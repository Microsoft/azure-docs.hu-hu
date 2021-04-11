---
title: 'Rövid útmutató: az első statikus hely felépítése az Azure statikus Web Apps'
description: Ismerje meg, hogyan helyezhet üzembe statikus helyet az Azure statikus Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: 335f78bba24947b1b6c3d6132bc38f237b3298b9
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449135"
---
# <a name="quickstart-building-your-first-static-site-with-azure-static-web-apps"></a>Rövid útmutató: az első statikus hely felépítése az Azure statikus Web Apps

Az Azure statikus Web Apps webhelyeket tesz közzé az alkalmazások egy kódból való létrehozásával. Ebben a rövid útmutatóban egy alkalmazást helyez üzembe az Azure statikus Web Apps szolgáltatásban a Visual Studio Code bővítménnyel.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes próbaverziós fiókot](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Előfeltételek

- [GitHub](https://github.com)-fiók
- [Azure](https://portal.azure.com) -fiók
- [Visual Studio Code](https://code.visualstudio.com)
- [A Visual Studio Code Azure Static Web Apps-bővítménye](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurestaticwebapps)
- [A Git telepítése](https://www.git-scm.com/downloads)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

Ezután nyissa meg a Visual Studio Code-ot, és nyissa meg a **fájl > megnyitása mappát** a szerkesztőben a gépre klónozott tárház megnyitásához.

## <a name="create-a-static-web-app"></a>Statikus webalkalmazás létrehozása

1. Az Azure-bővítmények ablak megnyitásához a Visual Studio Code-ban kattintson a Tevékenység sávon az Azure-emblémára.

    :::image type="content" source="media/getting-started/extension-azure-logo.png" alt-text="Azure-embléma":::

    > [!NOTE]
    > Be kell jelentkeznie az Azure-ba és a GitHubra. Ha még nincs bejelentkezve az Azure-ba és a GitHubra a Visual Studio Code-ban, a bővítmény arra fogja kérni, hogy a létrehozás folyamata során jelentkezzen be mindkét szolgáltatásba.

1. A _statikus Web Apps_ címke alatt válassza ki a **plusz jelet**.

    :::image type="content" source="media/getting-started/extension-create-button.png" alt-text="Alkalmazás neve":::

1. A szájpadlás parancs a szerkesztő tetején nyílik meg, és rákérdez az alkalmazás nevének megadására.

    Adja meg a **my-first-static-web-app** nevet, és nyomja le az **Enter** billentyűt.

    :::image type="content" source="media/getting-started/extension-create-app.png" alt-text="Statikus webalkalmazás létrehozása":::

1. Válassza ki az alkalmazás típusának megfelelő előbeállításokat.

    # <a name="no-framework"></a>[Nincs keretrendszer](#tab/vanilla-javascript)
    :::image type="content" source="media/getting-started/extension-presets-no-framework.png" alt-text="Alkalmazás-beállításkészletek: nincs keretrendszer":::

    Adja meg a **./** értéket az alkalmazásfájlok helyeként

    :::image type="content" source="media/getting-started/extension-app-location.png" alt-text="Alkalmazásfájlok helye":::

    Válassza a **kihagyás** lehetőséget a Azure functions API helyének kiválasztásához

    :::image type="content" source="media/getting-started/extension-api-location.png" alt-text="Az API helye":::

    Adja meg a **./** értéket a Build kimeneti helyeként

    :::image type="content" source="media/getting-started/extension-build-location.png" alt-text="Alkalmazás-Build kimeneti helye":::

    # <a name="angular"></a>[Angular](#tab/angular)

    :::image type="content" source="media/getting-started/extension-presets-angular.png" alt-text="Alkalmazás-előállítók: szögletes":::

    # <a name="react"></a>[React](#tab/react)

    :::image type="content" source="media/getting-started/extension-presets-react.png" alt-text="Alkalmazás-előállítók: reagálás":::

    # <a name="vue"></a>[Vue](#tab/vue)

    :::image type="content" source="media/getting-started/extension-presets-vue.png" alt-text="Alkalmazás-beállításkészlet: Vue":::

    ---

1. Válassza ki a legközelebbi helyet, és nyomja le az **Enter** billentyűt.

    :::image type="content" source="media/getting-started/extension-location.png" alt-text="Erőforrás helye":::

1. Az alkalmazás létrehozása után megerősítő értesítés jelenik meg a Visual Studio Code-ban.

    :::image type="content" source="media/getting-started/extension-confirmation.png" alt-text="Létrehozás megerősítése":::

    Ezután kattintson a gombra a **githubon a műveletek megnyitása** lehetőségre. Ezen az oldalon az alkalmazás Build állapota látható.

    Miután befejeződött a GitHub-művelet, megkeresheti a közzétett webhelyet.

1. Ha meg szeretné tekinteni a webhelyet a böngészőben, kattintson a jobb gombbal a projektre a statikus Web Apps-bővítményben, majd válassza a **Tallózás webhely** lehetőséget.

    :::image type="content" source="media/getting-started/extension-browse-site.png" alt-text="Webhely tallózása":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölheti az Azure statikus Web Apps példányát a bővítmény használatával.

A Visual Studio Code Explorer ablakban térjen vissza a _statikus Web Apps_ szakaszhoz, és kattintson a jobb gombbal a **My-First-static-Web-App** elemre, és válassza a **Törlés** lehetőséget.

:::image type="content" source="media/getting-started/extension-delete.png" alt-text="Alkalmazás törlése":::

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [API hozzáadása](add-api.md)
