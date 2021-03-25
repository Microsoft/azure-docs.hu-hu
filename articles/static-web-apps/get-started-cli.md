---
title: 'Rövid útmutató: az első statikus hely felépítése az Azure statikus Web Apps a parancssori felület használatával'
description: Ismerje meg, hogyan helyezhet üzembe statikus helyet az Azure-beli statikus Web Apps az Azure CLI-vel.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: quickstart
ms.date: 08/13/2020
ms.author: cshoe
ms.openlocfilehash: f3ac1c8076650a4075c33aeaa9128133b5b90296
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105046079"
---
# <a name="quickstart-building-your-first-static-site-using-the-azure-cli"></a>Rövid útmutató: az első statikus hely felépítése az Azure CLI használatával

Az Azure statikus Web Apps egy GitHub-tárházból származó alkalmazások létrehozásával tesz közzé webhelyeket az éles környezetben. Ebben a rövid útmutatóban egy webalkalmazást helyez üzembe az Azure statikus Web Apps szolgáltatásban az Azure CLI használatával.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes próbaverziós fiókot](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Előfeltételek

- [GitHub](https://github.com)-fiók
- [GitHubos személyes hozzáférési jogkivonat](https://docs.github.com/github/authenticating-to-github/creating-a-personal-access-token)
- [Azure](https://portal.azure.com) -fiók
- [Azure CLI](/cli/azure/install-azure-cli) telepítve (2.8.0 és újabb verzió)

[!INCLUDE [create repository from template](../../includes/static-web-apps-get-started-create-repo.md)]

[!INCLUDE [clone the repository](../../includes/static-web-apps-get-started-clone-repo.md)]

Ezután váltson az új mappára az alábbi parancs használatával.

```bash
cd my-first-static-web-app
```

## <a name="create-a-static-web-app"></a>Statikus webalkalmazás létrehozása

Most, hogy létrejött a tárház, létrehozhat egy statikus webalkalmazást az Azure CLI-ből.

> [!IMPORTANT]
> Győződjön meg arról, hogy a _My-First-static-Web-App_ mappában van a terminálon.

1. Jelentkezzen be az Azure CLI-be az alábbi parancs használatával.

    ```azurecli
    az login
    ```

1. Hozzon létre egy új statikus webalkalmazást az adattárból.

    # <a name="no-framework"></a>[Nincs keretrendszer](#tab/vanilla-javascript)

    ```azurecli
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b main \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="angular"></a>[Angular](#tab/angular)

    ```azurecli
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b main \
        --app-artifact-location "dist/angular-basic" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="react"></a>[React](#tab/react)

    ```azurecli
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b main \
        --app-artifact-location "build" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    # <a name="vue"></a>[Vue](#tab/vue)

    ```azurecli
    az staticwebapp create \
        -n my-first-static-web-app \
        -g <RESOURCE_GROUP_NAME> \
        -s https://github.com/<YOUR_GITHUB_ACCOUNT_NAME>/my-first-static-web-app \
        -l <LOCATION> \
        -b main \
        --app-artifact-location "dist" \
        --token <YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>
    ```

    ---
    
    > [!IMPORTANT]
    > A paraméternek átadott URL-cím `s` nem tartalmazhat `.git` utótagot.

    - `<RESOURCE_GROUP_NAME>`: Cserélje le ezt az értéket egy meglévő [Azure-erőforráscsoport nevére](../azure-resource-manager/management/manage-resources-cli.md).

      - Az erőforráscsoportok listázásával kapcsolatos részletekért tekintse meg az az [Group](/cli/azure/group?view=azure-cli-latest#az_group_list) dokumentációját.

    - `<YOUR_GITHUB_ACCOUNT_NAME>`: Cserélje le ezt az értéket a GitHub-felhasználónevére.

    - `<LOCATION>`: Cserélje le ezt az értéket a legközelebbi helyre. A lehetőségek a következők: _CentralUS_, _EastAsia_, _EastUS2_, _WestEurope_ és _WestUS2_.

    - `<YOUR_GITHUB_PERSONAL_ACCESS_TOKEN>`: Cserélje le ezt az értéket a korábban létrehozott [GitHub személyes hozzáférési tokenre](https://docs.github.com/github/authenticating-to-github/creating-a-personal-access-token) .

    Most már megtekintheti a létrehozott alkalmazást az Azure-ban.

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).

1. Keresse meg a **saját-első-web-static-app** elemet a felső keresési sávon.

1. Válassza **a saját-első-web-static-app** elemet.

[!INCLUDE [view website](../../includes/static-web-apps-get-started-view-website.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, az alábbi parancs futtatásával törölheti az Azure statikus Web Apps példányát:

```azurecli
az staticwebapp delete \
    --name my-first-static-web-app \
    --resource-group my-first-static-web-app
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [API hozzáadása](add-api.md)