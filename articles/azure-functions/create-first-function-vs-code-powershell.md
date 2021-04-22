---
title: PowerShell-függvény létrehozása a Visual Studio Code használatával – Azure Functions
description: Megtudhatja, hogyan hozhat létre Egy PowerShell-függvényt, majd hogyan tehet közzé egy helyi projektet a Azure Functions-ban kiszolgáló nélküli üzemeltetésre az Azure Functions Code Visual Studio bővítményének használatával.
ms.topic: quickstart
ms.date: 11/04/2020
ms.openlocfilehash: 58a2b8a5fb386603361afa9fc26b4d323c487691
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862542"
---
# <a name="quickstart-create-a-powershell-function-in-azure-using-visual-studio-code"></a>Rövid útmutató: PowerShell-függvény létrehozása az Azure-ban az Visual Studio Code használatával

[!INCLUDE [functions-language-selector-quickstart-vs-code](../../includes/functions-language-selector-quickstart-vs-code.md)]

Ebben a cikkben a Visual Studio használatával hoz létre egy PowerShell-függvényt, amely HTTP-kérésekre válaszol. A kód helyi tesztelése után üzembe helyezheti a virtuális gép kiszolgáló nélküli Azure Functions.

Ennek a rövid útmutatónak az elvégzése néhány dollár vagy annál kisebb költséggel jár az Azure-fiókjában.

A cikknek [cli-alapú](create-first-function-cli-powershell.md) verziója is van.

## <a name="configure-your-environment"></a>A környezet konfigurálása

Mielőtt hozzá kezd, győződjön meg arról, hogy a következő követelmények vonatkoznak rá:

+ Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ A [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) 3.x verzió.

+ [PowerShell 7](/powershell/scripting/install/installing-powershell-core-on-windows)

+ A [.NET Core 3.1-es és](https://dotnet.microsoft.com/download) a [.NET Core 2.1-es futtatás is](https://dotnet.microsoft.com/download/dotnet/2.1)  

+ [Visual Studio Code](https://code.visualstudio.com/) az egyik [támogatott platformon](https://code.visualstudio.com/docs/supporting/requirements#_platforms).    

+ A [kódhoz Visual Studio PowerShell-bővítmény.](https://marketplace.visualstudio.com/items?itemName=ms-vscode.PowerShell)  

+ A Visual Code [Azure Functions-bővítménye](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions). 

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>A helyi projekt létrehozása

Ebben a szakaszban az Visual Studio Code használatával hoz létre helyi Azure Functions PowerShellben. A cikk későbbi részében közzéteszi a függvény kódját az Azure-ban.

1. A műveleti sávban válassza ki az Azure ikont, majd az **Azure: Functions** területen válassza az **Új projekt létrehozása...** ikont.

    ![Új projekt létrehozása lehetőség kiválasztása](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Válasszon egy címtárbeli helyet a projekt munkaterületéhez, és kattintson a **Kiválasztás** lehetőségre.

    > [!NOTE]
    > Ezeket a lépéseket munkaterületen kívülre tervezték. Ebben az esetben ne válasszon olyan projektmappát, amely valamely munkaterület része.

1. Amikor a rendszer kéri, adja meg az alábbi információkat:

    + **Válasszon nyelvet a függvényprojekthez**: Válassza a `PowerShell` elemet.

    + **Válasszon sablont a projekt első függvényéhez**: Válassza a `HTTP trigger` elemet.

    + **Adjon meg egy függvénynevet**: Gépelje be: `HttpExample`.

    + **Engedélyezési szint:** Válassza a lehetőséget, amely lehetővé teszi bárki `Anonymous` számára a függvényvégpont hívását. Az engedélyezési szinttel kapcsolatos további információkért lásd: [Engedélyezési kulcsok.](functions-bindings-http-webhook-trigger.md#authorization-keys)

    + **Válassza ki, hogyan szeretné megnyitni a projektet**: Válassza a `Add to workspace` elemet.

1. Ezen információk alapján a Visual Studio Code létrehoz egy Azure Functions projektet egy HTTP-eseményindítóval. A helyi projektfájlokat az Explorerben tudja megtekinteni. A létrehozott fájlokkal kapcsolatos további információkért lásd a létrehozott [projektfájlokat.](functions-develop-vs-code.md#generated-project-files) 

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

Miután ellenőrizte, hogy a függvény megfelelően fut-e a helyi számítógépen, ideje a Visual Studio Code használatával közvetlenül az Azure-ban közzétenni a projektet. 

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>Következő lépések

A Visual Studio [használatával](functions-develop-vs-code.md?tabs=powershell) létrehozott egy függvényalkalmazást egy egyszerű, HTTP-eseményindítóval aktivált függvény használatával. A következő cikkben kibonthatja ezt a függvényt az Azure Storage-hoz való csatlakozással. További információ a más Azure-szolgáltatásokhoz való csatlakozásról: Kötések hozzáadása meglévő [függvényhez a Azure Functions.](add-bindings-existing-function.md?tabs=powershell)  

> [!div class="nextstepaction"]
> [Csatlakozás Azure Storage-üzenetsorhoz](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-powershell)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
