---
title: Függvény közzététele az Azure-ban Java és Gradle használatával
description: HTTP-eseményindítóval aktivált függvény létrehozása és közzététele az Azure-ban a Java és a Gradle használatával.
author: KarlErickson
ms.custom: devx-track-java
ms.author: karler
ms.topic: how-to
ms.date: 04/08/2020
ms.openlocfilehash: d7f8aa990f5a5e64d2d5c59b52457149187acddd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773980"
---
# <a name="use-java-and-gradle-to-create-and-publish-a-function-to-azure"></a>Függvény létrehozása és közzététele az Azure-ban a Java és a Gradle használatával

Ez a cikk bemutatja, hogyan építhet ki és tehet közzé Java-függvényprojektet Azure Functions a Gradle parancssori eszközzel. Ha végzett, [a](consumption-plan.md) függvénykód egy kiszolgáló nélküli üzemeltetési csomagon fut az Azure-ban, és egy HTTP-kérés aktiválja. 

> [!NOTE]
> Ha nem a Gradle-t részesíti előnyben részesítő fejlesztői eszközként, tekintse meg a [Mavent,](./create-first-function-cli-java.md)az [IntelliJ IDEA-t](/azure/developer/java/toolkit-for-intellij/quickstart-functions) és a VS Code-et használó Java-fejlesztőknek készült hasonló [oktatóanyagokat.](./create-first-function-vs-code-java.md)

## <a name="prerequisites"></a>Előfeltételek

Ha függvényeket szeretne fejleszteni a Java használatával, akkor a számítógépre a következőket kell telepíteni:

- A [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support) 8-as verziója
- [Azure CLI]
- [Azure Functions Core Tools](./functions-run-local.md#v2) 2.6.666-os vagy újabb verzió
- [Gradle,](https://gradle.org/)4.10-es és újabb verzió

Szüksége lesz egy aktív Azure-előfizetésre is. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> A rövid útmutató befejezéséhez a JAVA_HOME környezeti változót a JDK telepítési helyére kell beállítani.

## <a name="prepare-a-functions-project"></a>Functions-projekt előkészítése

A mintaprojekt klónozásához használja a következő parancsot:

```bash
git clone https://github.com/Azure-Samples/azure-functions-samples-java.git
cd azure-functions-samples-java/
```

Nyissa meg a következő szakaszt, és módosítsa egy egyedi névre, hogy elkerülje a tartománynévütközést az `build.gradle` `appName` Azure-ban való üzembe helyezéskor. 

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

Nyissa meg az új Function.java fájlt az *src/main/java* útvonalról egy szövegszerkesztőben, és tekintse át a létrehozott kódot. Ez a kód egy [HTTP által aktivált](functions-bindings-http-webhook.md) függvény, amely megismétli a kérés törzsét. 

> [!div class="nextstepaction"]
> [Problémabe futottam](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=generate-project)

## <a name="run-the-function-locally"></a>A függvény helyi futtatása

Futtassa a következő parancsot a buildhez, majd futtassa a függvényprojektet:

```bash
gradle jar --info
gradle azureFunctionsRun
```
A projekt helyi futtatásakor az alábbihoz hasonló kimenet Azure Functions Core Tools a projektből:

<pre>
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

    HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...
</pre>

Aktiválja a függvényt a parancssorból a következő cURL-paranccsal egy új terminálablakban:

```bash
curl -w "\n" http://localhost:7071/api/HttpExample --data AzureFunctions
```

A várt kimenet a következő:

<pre>
Hello, AzureFunctions
</pre>

> [!NOTE]
> Ha vagy hitelesítési szintet ad meg, a függvénykulcsra nincs szükség a `FUNCTION` `ADMIN` helyi futtatáskor. [](functions-bindings-http-webhook-trigger.md#authorization-keys)  

A `Ctrl+C` billentyűparanccsal állítsa le a függvénykódot a terminálablakban.

> [!div class="nextstepaction"]
> [Problémabe futottam](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=local-run)

## <a name="deploy-the-function-to-azure"></a>A függvény üzembe helyezése az Azure-ban

A függvényalkalmazás és a kapcsolódó erőforrások a függvényalkalmazás első üzembe helyezésekor vannak létrehozva az Azure-ban. Az üzembe helyezés előtt jelentkezzen be az Azure-előfizetésbe [az az login](/cli/azure/authenticate-azure-cli) Azure CLI-paranccsal. 

```azurecli
az login
```

> [!TIP]
> Ha a fiókja több előfizetéshez is hozzáfér, az [az account set](/cli/azure/account#az_account_set) használatával állítsa be az alapértelmezett előfizetést ehhez a munkamenethez. 

A következő paranccsal üzembe helyezheti a projektet egy új függvényalkalmazásban. 

```bash
gradle azureFunctionsDeploy
```

Ez a build.gradle fájlban található értékek alapján a következő erőforrásokat hozza létre az Azure-ban:

+ Erőforráscsoport. A megadott _resourceGroup erőforráscsoporttal_ elnevezve.
+ Tárfiók. A Functions által megkövetelt. A nevet a rendszer véletlenszerűen generálja a Storage-fiók nevére vonatkozó követelmények alapján.
+ App Service terv. Kiszolgáló nélküli használatra szánt csomag üzemeltetése a függvényalkalmazáshoz a megadott _appRegionben._ A név véletlenszerűen jön létre.
+ Függvényalkalmazás. A függvényalkalmazás a függvények üzembe helyezési és végrehajtási egysége. A név az _appName_, amely egy véletlenszerűen generált számmal van hozzáfűzve. 

Az üzemelő példány becsomagolja a projektfájlokat, és üzembe helyezheti őket az új függvényalkalmazásban a [zip-telepítés](functions-deployment-technologies.md#zip-deploy)használatával, engedélyezett csomagról futtatás módban.

A mintaprojektBEN a HTTP-eseményindító hitelesítési szintje `ANONYMOUS` , ami kihagyja a hitelesítést. Ha azonban más hitelesítési szintet használ( például vagy , akkor le kell szereznie a függvénykulcsot, hogy HTTP-n keresztül hívja meg a függvény `FUNCTION` `ADMIN` végpontját. A függvénykulcs lekért legegyszerűbb módja a [függvénykulcs Azure Portal.]

> [!div class="nextstepaction"]
> [Problémabe futottam](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=deploy)

## <a name="get-the-http-trigger-url"></a>A HTTP-eseményindító URL-címének le kérése

A függvény aktiválásához szükséges URL-címet a függvénykulcs használatával a függvény Azure Portal. 

1. Lépjen a [Azure Portal,]jelentkezzen be, írja be a függvényalkalmazás  _appName_ nevét az oldal tetején található Keresés mezőbe, majd nyomja le az Enter billentyűt.
 
1. A függvényalkalmazásban válassza a **Függvények** lehetőséget, válassza ki a függvényt, majd kattintson<**/> a függvény** URL-címének bejére a jobb felső sarokban. 

    :::image type="content" source="./media/functions-create-first-java-gradle/get-function-url-portal.png" alt-text="A függvény URL-címének másolása az Azure portálról":::

1. Válassza **az alapértelmezett (függvénykulcs) lehetőséget,** majd a **Másolás lehetőséget.** 

Most már használhatja a másolt URL-címet a függvény eléréséhez.

## <a name="verify-the-function-in-azure"></a>A függvény ellenőrzése az Azure-ban

Az Azure-ban futó függvényalkalmazásnak a használatával való ellenőrzéséhez cserélje le az alábbi minta URL-címét a portálról kimásott `cURL` URL-címre.

```console
curl -w "\n" http://azure-functions-sample-demo.azurewebsites.net/api/HttpExample --data AzureFunctions
```

Ez egy POST kérést küld a függvény végpontjának a kérelem `AzureFunctions` törzsében. A következő válasz látható.

<pre>
Hello, AzureFunctions
</pre>

> [!div class="nextstepaction"]
> [Problémabe futottam](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=verify-deployment)

## <a name="next-steps"></a>Következő lépések

Létrehozott egy Java Functions-projektet egy HTTP által aktivált függvénnyel, futtatta azt a helyi gépen, és üzembe helyezette az Azure-ban. Most terjessze ki a függvényt...

> [!div class="nextstepaction"]
> [Azure Storage-üzenetsor kimeneti kötésének hozzáadása](functions-add-output-binding-storage-queue-java.md)


[Azure CLI]: /cli/azure
[Azure Portal]: https://portal.azure.com