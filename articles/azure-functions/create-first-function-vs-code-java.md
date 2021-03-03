---
title: Java-függvény létrehozása a Visual Studio Code-Azure Functions használatával
description: Ismerje meg, hogyan hozhat létre Java-függvényeket, és hogyan teheti közzé a helyi projektet a Azure Functions kiszolgáló nélküli üzemeltetésére a Visual Studio Code Azure Functions bővítménnyel.
ms.topic: quickstart
ms.date: 11/03/2020
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-vs-code-java-uiex
ms.openlocfilehash: 10ac677fb65428e8b8855c43b319bbebe7bbbb71
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101701432"
---
# <a name="quickstart-create-a-java-function-in-azure-using-visual-studio-code"></a>Rövid útmutató: Java-függvény létrehozása az Azure-ban a Visual Studio Code használatával

> [!div class="op_single_selector" title1="Válassza ki a függvény nyelvét: "]
> - [Java](create-first-function-vs-code-java.md)
> - [Python](create-first-function-vs-code-python.md)
> - [C#](create-first-function-vs-code-csharp.md)
> - [JavaScript](create-first-function-vs-code-node.md)
> - [PowerShell](create-first-function-vs-code-powershell.md)
> - [TypeScript](create-first-function-vs-code-typescript.md)
> - [Egyéb (Go/Rust)](create-first-function-vs-code-other.md)

Ebben a cikkben a Visual Studio Code használatával hozzon létre egy Java-függvényt, amely válaszol a HTTP-kérelmekre. A kód helyi tesztelését követően a Azure Functions kiszolgáló nélküli környezetében helyezheti üzembe.

A rövid útmutató elvégzésével az Azure-fiókjában néhány USD értékű vagy annál kisebb költséggel jár.

> [!NOTE]
> Ha a Visual Studio Code nem az Ön előnyben részesített fejlesztői eszköze, tekintse meg a Java-fejlesztőknek készült hasonló oktatóanyagokat a [Maven](create-first-function-cli-java.md), a [Gradle](./functions-create-first-java-gradle.md) és a [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions)használatával.

## <a name="configure-your-environment"></a>A környezet konfigurálása

Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik a következő követelményekkel:

+ Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ A [Java fejlesztői csomag](/azure/developer/java/fundamentals/java-jdk-long-term-support)8-as vagy 11-es verziója.

+ [Apache Maven](https://maven.apache.org), 3,0-es vagy újabb verzió.

+ [Visual Studio Code](https://code.visualstudio.com/) az egyik [támogatott platformon](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

+ A [Java-bővítmény csomagja](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)  

+ A Visual Code [Azure Functions-bővítménye](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions). 

## <a name="create-your-local-project"></a><a name="create-an-azure-functions-project"></a>A helyi projekt létrehozása

Ebben a szakaszban a Visual Studio Code használatával hozzon létre egy helyi Azure Functions-projektet Java-ban. A cikk későbbi részében közzéteszi a függvény kódját az Azure-ban. 

1. A műveleti sávban válassza ki az Azure ikont, majd az **Azure: Functions** területen válassza az **Új projekt létrehozása...** ikont.

    ![Új projekt létrehozása lehetőség kiválasztása](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Válasszon egy címtárbeli helyet a projekt munkaterületéhez, és kattintson a **Kiválasztás** lehetőségre.

    > [!NOTE]
    > Ezeket a lépéseket úgy tervezték, hogy a munkaterületen kívül is el lehessen végezni. Ebben az esetben ne válasszon olyan projektmappát, amely valamely munkaterület része.

1. Amikor a rendszer kéri, adja meg az alábbi információkat:

    + **Válasszon nyelvet a függvényprojekthez**: Válassza a `Java` elemet.

    + **Válassza ki a Java-verziót**: válassza ki `Java 8` vagy `Java 11` a Java-verziót, amelyen a függvények futnak az Azure-ban. Válassza ki a helyileg ellenőrzött Java-verziót.

    + **Adja meg a csoport azonosítóját**: válassza a lehetőséget `com.function` .

    + **Adja meg az összetevő azonosítóját**: válassza a lehetőséget `myFunction` .

    + **Adja meg a verziószámot**: válassza a lehetőséget `1.0-SNAPSHOT` .

    + **Adja meg a csomag nevét**: válassza a lehetőséget `com.function` .

    + **Adja meg az alkalmazás nevét**: válassza a lehetőséget `myFunction-12345` .

    + **Engedélyezési szint**: válassza `Anonymous` a lehetőséget, amely lehetővé teszi, hogy bárki meghívja a függvény végpontját. Az engedélyezési szint megismeréséhez tekintse meg az [engedélyezési kulcsok](functions-bindings-http-webhook-trigger.md#authorization-keys)című témakört.

    + **Válassza ki, hogyan szeretné megnyitni a projektet**: Válassza a `Add to workspace` elemet.

1. Ezen információk használatával a Visual Studio Code egy Azure Functions projektet hoz létre egy HTTP-triggerrel. A helyi projektfájlok a Explorerben tekinthetők meg. További információ a létrehozott fájlokról: [generált projektfájlok](functions-develop-vs-code.md#generated-project-files). 

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

Miután meggyőződött róla, hogy a függvény megfelelően fut a helyi számítógépen, ideje, hogy a Visual Studio Code használatával tegye közzé a projektet közvetlenül az Azure-ban.

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

[!INCLUDE [functions-publish-project-vscode](../../includes/functions-publish-project-vscode.md)]

[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]

[!INCLUDE [functions-cleanup-resources-vs-code.md](../../includes/functions-cleanup-resources-vs-code.md)]

## <a name="next-steps"></a>Következő lépések

A [Visual Studio Code](functions-develop-vs-code.md?tabs=java) használatával egy egyszerű http-triggert használó függvényt hozhat létre. A következő cikkben az Azure Storage-hoz való csatlakozással bővítheti ezt a függvényt. További információ az egyéb Azure-szolgáltatásokhoz való csatlakozásról: [kötések hozzáadása meglévő függvényhez Azure functions](add-bindings-existing-function.md?tabs=java). 

> [!div class="nextstepaction"]
> [Kapcsolódás Azure Storage-várólistához](functions-add-output-binding-storage-queue-vs-code.md?pivots=programming-language-java)

[Azure Functions Core Tools]: functions-run-local.md
[Azure Functions extension for Visual Studio Code]: https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions
