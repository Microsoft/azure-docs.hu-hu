---
title: Függvények közzététele az Azure-ban a Java és a Gradle használatával
description: HTTP által aktivált függvény létrehozása és közzététele az Azure-ban Java és Gradle használatával.
author: KarlErickson
ms.custom: devx-track-java
ms.author: karler
ms.topic: how-to
ms.date: 04/08/2020
ms.openlocfilehash: c9ecb670cea022988efda8ec690c0724310cdb4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97934849"
---
# <a name="use-java-and-gradle-to-create-and-publish-a-function-to-azure"></a>Függvények létrehozása és közzététele az Azure-ban a Java és a Gradle használatával

Ebből a cikkből megtudhatja, hogyan hozhat létre és tehet közzé Java-függvények projektjét, hogy Azure Functions a Gradle parancssori eszközzel. Ha elkészült, a függvény kódja egy [kiszolgáló nélküli üzemeltetési](consumption-plan.md) csomagban fut az Azure-ban, és egy HTTP-kérelem indítja el. 

> [!NOTE]
> Ha a Gradle nem az Ön által előnyben részesített fejlesztői eszköz, tekintse meg a Java-fejlesztőknek készült hasonló oktatóanyagokat a [Maven](./create-first-function-cli-java.md), a [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions) és a [vs Code](./create-first-function-vs-code-java.md)használatával.

## <a name="prerequisites"></a>Előfeltételek

Ha függvényeket szeretne fejleszteni a Java használatával, akkor a számítógépre a következőket kell telepíteni:

- A [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support) 8-as verziója
- [Azure CLI]
- [Azure functions Core Tools](./functions-run-local.md#v2) 2.6.666 vagy újabb verzió
- [Gradle](https://gradle.org/), 4,10-es vagy újabb verzió

Aktív Azure-előfizetésre is szüksége van. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> A rövid útmutató befejezéséhez a JAVA_HOME környezeti változót a JDK telepítési helyére kell beállítani.

## <a name="prepare-a-functions-project"></a>Functions-projekt előkészítése

A minta projekt klónozásához használja az alábbi parancsot:

```bash
git clone https://github.com/Azure-Samples/azure-functions-samples-java.git
cd azure-functions-samples-java/
```

Nyisson meg `build.gradle` `appName` egy egyedi nevet a következő szakaszban, és módosítsa a tartománynevet az Azure-ba való üzembe helyezéskor. 

```gradle
azurefunctions {
    resourceGroup = 'java-functions-group'
    appName = 'azure-functions-sample-demo'
    pricingTier = 'Consumption'
    region = 'westus'
    runtime {
      os = 'windows'
    }
    localDebug = "transport=dt_socket,server=y,suspend=n,address=5005"
}
```

Nyissa meg az új function. Java fájlt egy szövegszerkesztőben a *src/Main/Java* elérési útról, és tekintse át a generált kódot. Ez a kód egy [http által aktivált](functions-bindings-http-webhook.md) függvény, amely megismétli a kérelem törzsét. 

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=generate-project)

## <a name="run-the-function-locally"></a>A függvény helyi futtatása

A következő parancs futtatásával hozza létre a Function projektet:

```bash
gradle jar --info
gradle azureFunctionsRun
```
A következőhöz hasonló kimenet jelenik meg Azure Functions Core Tools a projekt helyi futtatásakor:

<pre>
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

    HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...
</pre>

Aktiválja a függvényt a parancssorból a következő cURL paranccsal egy új Terminálablak használatával:

```bash
curl -w "\n" http://localhost:7071/api/HttpExample --data AzureFunctions
```

A várt kimenet a következő:

<pre>
Hello, AzureFunctions
</pre>

> [!NOTE]
> Ha a authLevel a vagy a értékre állítja `FUNCTION` `ADMIN` , a [funkcióbillentyű](functions-bindings-http-webhook-trigger.md#authorization-keys) nem szükséges a helyi futtatásakor.  

A `Ctrl+C` billentyűparanccsal állítsa le a függvénykódot a terminálablakban.

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=local-run)

## <a name="deploy-the-function-to-azure"></a>A függvény üzembe helyezése az Azure-ban

A Function alkalmazást és a kapcsolódó erőforrásokat az Azure-ban hozza létre a rendszer az első üzembe helyezéskor. Az üzembe helyezés előtt az az [login](/cli/azure/authenticate-azure-cli) Azure CLI-paranccsal jelentkezzen be az Azure-előfizetésbe. 

```azurecli
az login
```

> [!TIP]
> Ha a fiókja több előfizetéshez is hozzáfér, az [az Account set](/cli/azure/account#az-account-set) paranccsal állíthatja be az alapértelmezett előfizetést ehhez a munkamenethez. 

A következő parancs használatával telepítheti a projektet egy új Function alkalmazásba. 

```bash
gradle azureFunctionsDeploy
```

Ez létrehozza a következő erőforrásokat az Azure-ban a Build. gradle fájl értékei alapján:

+ Erőforráscsoport. Elnevezve a megadott _resourceGroup_ .
+ Storage-fiók. A függvények igénylik. A név véletlenszerűen jön létre a Storage-fióknév követelményei alapján.
+ App Service terv. Kiszolgáló nélküli használati terv a megadott _appRegion_ a Function alkalmazás üzemeltetéséhez. A név véletlenszerűen jön létre.
+ Function alkalmazás. A functions alkalmazás a függvények üzembe helyezési és végrehajtási egysége. A név a _appName_, amely véletlenszerűen generált számmal van hozzáfűzve. 

Az üzemelő példány a Project fájljait is becsomagolja, és az új Function alkalmazásba telepíti a [zip-telepítést](functions-deployment-technologies.md#zip-deploy), és engedélyezve van a csomagon belüli mód.

A authLevel a HTTP-triggerhez a minta projektben `ANONYMOUS` , amely kihagyja a hitelesítést. Ha azonban más authLevel (például vagy) használ, a függvényt a `FUNCTION` `ADMIN` HTTP protokollon keresztül a függvény végpontjának meghívásához le kell kérnie. A funkcióbillentyű beszerzésének legegyszerűbb módja a [Azure Portal].

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=deploy)

## <a name="get-the-http-trigger-url"></a>HTTP-trigger URL-címének beolvasása

A függvény elindításához szükséges URL-címet a Azure Portalból kérheti le. 

1. Keresse meg a [Azure Portalt], jelentkezzen be, írja be a _appName_ az oldal tetején található **Keresés** mezőbe, majd nyomja le az ENTER billentyűt.
 
1. A Function alkalmazásban válassza a **függvények** lehetőséget, válassza ki a függvényt, majd kattintson a jobb felső sarokban található **</> a függvény URL-címének beolvasása** lehetőségre. 

    :::image type="content" source="./media/functions-create-first-java-gradle/get-function-url-portal.png" alt-text="A függvény URL-címének másolása az Azure portálról":::

1. Válassza az **alapértelmezett (funkcióbillentyű)** lehetőséget, majd válassza a **Másolás** lehetőséget. 

Mostantól a másolt URL-cím használatával is elérheti a függvényt.

## <a name="verify-the-function-in-azure"></a>A függvény ellenőrzése az Azure-ban

Az Azure-on futó Function alkalmazás ellenőrzéséhez `cURL` cserélje le az alábbi minta URL-címét a portálról másolt URL-címre.

```console
curl -w "\n" http://azure-functions-sample-demo.azurewebsites.net/api/HttpExample --data AzureFunctions
```

Ez POST-kérést küld a függvény végpontjának a `AzureFunctions` kérelem törzsében. A következő válasz jelenik meg.

<pre>
Hello, AzureFunctions
</pre>

> [!div class="nextstepaction"]
> [Egy hibába ütközött](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=verify-deployment)

## <a name="next-steps"></a>Következő lépések

Létrehozott egy Java functions-projektet egy HTTP által aktivált függvénnyel, futtatta azt a helyi gépen, és üzembe helyezte az Azure-ban. Most terjessze ki a függvényt...

> [!div class="nextstepaction"]
> [Azure Storage-üzenetsor kimeneti kötésének hozzáadása](functions-add-output-binding-storage-queue-java.md)


[Azure CLI]: /cli/azure
[Azure Portal]: https://portal.azure.com