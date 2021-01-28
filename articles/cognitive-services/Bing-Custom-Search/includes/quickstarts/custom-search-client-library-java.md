---
title: Bing Custom Search Java ügyféloldali kódtár gyors üzembe helyezése
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: 300c602a62b0d6b3ba579931b2222d2cd8667656
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98948493"
---
Ismerkedjen meg a Javához készült Bing Custom Search-ügyfél függvénytárával. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja az alapszintű feladatokhoz tartozó példa kódját. A Bing Custom Search API lehetővé teszi, hogy testreszabott, ad-ingyenes keresési élményeket hozzon létre az Ön számára fontos témakörökhöz. A minta forráskódja megtalálható a [githubon](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingCustomSearch)

A Javához készült Bing Custom Search ügyféloldali kódtár a következőre használható:

* Keresési eredmények keresése a weben a Bing Custom Search-példányból.

[Dokumentáció](/java/api/overview/azure/cognitiveservices/client/bingcustomsearch)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingCustomSearch)  |  Összetevő [(Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customsearch/)  |  [Példák](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/).
* A [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)aktuális verziója.
* A [Gradle Build eszköz](https://gradle.org/install/)vagy egy másik függőségi kezelő.
* Egy Bing Custom Search példány. További információért tekintse [meg a rövid útmutató: az első Bing Custom Search példány létrehozása](../../quick-start.md) című témakört.

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]

Miután megszerezte a kulcsot az erőforrásból, [hozzon létre egy környezeti változót](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) a (z) nevű kulcshoz `AZURE_BING_CUSTOM_SEARCH_API_KEY` .

### <a name="create-a-new-gradle-project"></a>Új Gradle-projekt létrehozása

> [!TIP]
> Ha nem használja a Gradle-t, a [Maven központi tárházában](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/)megtalálhatja az ügyféloldali függvénytár részleteit más függőségi kezelők számára.

Egy konzolablak (például a cmd, a PowerShell vagy a bash) ablakban hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá.

```console
mkdir myapp && cd myapp
```

Futtassa a `gradle init` parancsot a munkakönyvtárból. Ez a parancs alapvető Build-fájlokat hoz létre a Gradle számára, beleértve az alkalmazás konfigurálásához futásidőben használt *Build. Gradle. KTS* fájlt.

```console
gradle init --type basic
```

Amikor a rendszer rákérdez a **DSL** kiválasztására, válassza a **Kotlin** lehetőséget.

## <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Keresse meg a *Build. gradle. KTS* , és nyissa meg a kívánt ide-vagy szövegszerkesztővel. Ezután másolja ezt a Build-konfigurációt. Ügyeljen rá, hogy az ügyféloldali kódtár szerepeljen a következőben `dependencies` :

```kotlin
plugins {
    java
    application
}
application {
    mainClassName = "main.java.BingCustomSearchSample"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-customsearch:1.0.2")
}
```

Hozzon létre egy mappát a minta alkalmazáshoz. A munkakönyvtárból futtassa a következő parancsot:

```console
mkdir src/main/java
```

Navigáljon az új mappára, és hozzon létre egy *BingCustomSearchSample. Java* nevű fájlt. Nyissa meg, és adja hozzá a következő `import` utasításokat:


[!code-java[import statements](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=imports)]

Hozzon létre egy nevű osztályt. `BingCustomSearchSample`

```java
public class BingCustomSearchSample {
}
```

A osztályban hozzon létre egy `main` metódust és egy változót az erőforrás kulcsaként. Ha a környezeti változót az alkalmazás elindítása után hozta létre, akkor a változó eléréséhez nyissa meg, majd nyissa meg újra a szerkesztőt, az IDE vagy a shellt. A metódusokat később kell megadnia.

[!code-java[main method](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=main)]

## <a name="object-model"></a>Objektummodell

A Bing Custom Search ügyfél egy [BingCustomSearchAPI](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi) objektum, amely a [BingCustomSearchManager](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager) objektum [hitelesítése ()](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager.authenticate) metódusból lett létrehozva. A keresési kérelmet az ügyfél [BingCustomInstances. Search ()](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) metódusának használatával küldheti el.

Az API-válasz egy [SearchResponse](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.models.searchresponse) objektum, amely információkat tartalmaz a keresési lekérdezésről és a keresési eredményekről.

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat a Javához készült Bing Custom Search ügyféloldali kódtár használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Keresési eredmények beolvasása az egyéni keresési példányból](#get-search-results-from-your-custom-search-instance)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

A Main metódusnak tartalmaznia kell egy [BingCustomSearchManager](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi) objektumot, amely a kulcsot veszi át, és meghívja a-t `authenticate()` .

```java
BingCustomSearchAPI client = BingCustomSearchManager.authenticate(subscriptionKey);
```

## <a name="get-search-results-from-your-custom-search-instance"></a>Keresési eredmények beolvasása az egyéni keresési példányból

Használja az ügyfél [BingCustomInstances. Search ()](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) függvényét, hogy keresési lekérdezést küldjön az egyéni példányra. Állítsa be az értéket az `withCustomConfig` egyéni konfigurációs azonosítóra, vagy az alapértelmezett értékre a következőre: `1` . Az API válaszának beolvasása után ellenőrizze, hogy talált-e keresési eredményeket. Ha igen, szerezze be az első keresési eredményt úgy, hogy meghívja a válasz `webPages().value().get()` függvényét, és kinyomtatja az eredmény nevét és URL-címét.

[!code-java[call the custom search API](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=runSample)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Hozza létre az alkalmazást az alábbi paranccsal a projekt fő könyvtárából:

```console
gradle build
```

Futtassa az alkalmazást a `run` célnak megfelelően:

```console
gradle run
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Egyéni keresési Webalkalmazás létrehozása](../../tutorials/custom-search-web-page.md)
