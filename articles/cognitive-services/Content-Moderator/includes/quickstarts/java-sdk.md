---
title: Content Moderator Java-ügyféloldali kódtár – rövid útmutató
titleSuffix: Azure Cognitive Services
description: Ebből a rövid útmutatóból megtudhatja, hogyan ismerkedés az Azure Content Moderator Java-ügyféloldali kódtárával. Tartalomszűrő szoftvereket építhet az alkalmazásába, hogy megfeleljen az előírásoknak, vagy fenntartsa a kívánt környezetet a felhasználók számára.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 10/16/2020
ms.custom: devx-track-java, cog-serv-seo-aug-2020
ms.author: pafarley
ms.openlocfilehash: 76d62806541e0a3186cd17712b7af3930d9ccf9f
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726159"
---
Az Azure Content Moderator Java-ügyféloldali kódtár használatának első lépések. Kövesse az alábbi lépéseket a Maven-csomag telepítéséhez, és próbálja ki az alapszintű feladatokhoz szükséges példakódot. 

Content Moderator egy olyan AI-szolgáltatás, amely lehetővé teszi a potenciálisan sértő, kockázatos vagy egyéb módon nem kívánt tartalmak kezelését. Az AI-alapú tartalommoderálási szolgáltatással szövegeket, képeket és videókat vizsgálhatnak meg, és tartalomjelölőket alkalmazhatnak automatikusan. Tartalomszűrő szoftvereket építhet az alkalmazásába, hogy megfeleljen az előírásoknak, vagy fenntartsa a kívánt környezetet a felhasználók számára.

Használja a Content Moderator Java-ügyféloldali kódtárát a következőre:

* Szöveg moderálása
* Képek moderálása

[Referenciadokumentáció](/java/api/overview/azure/cognitiveservices/client/contentmoderator)  |  [Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-contentmoderator)  | [Összetevő (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-contentmoderator)  |  [Minták](/samples/browse/?products=azure&term=content-moderator)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [Hozzon létre egyet ingyenesen](https://azure.microsoft.com/free/cognitive-services/)
* A Java fejlesztői készlet [(JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html) aktuális verziója
* A [Gradle buildeszköz ,](https://gradle.org/install/)vagy egy másik függőségkezelő.
* Ha már rendelkezik Azure-előfizetéssel, hozzon létre egy Content Moderator-erőforrást, Content Moderator erőforrást a Azure Portal a kulcs és a <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title=" "  target="_blank"> végpont </a> lekért létrehozásához. Várja meg az üzembe helyezést, majd kattintson az **Erőforráshoz ugrás gombra.**
    * Szüksége lesz a létrehozott erőforrás kulcsának és végpontjának létrehozására, hogy az alkalmazást a Content Moderator. A kulcsot és a végpontot a rövid útmutató későbbi, alábbi kódába fogja beilleszteni.
    * Az ingyenes tarifacsomag ( ) használatával kipróbálhatja a szolgáltatást, és később frissíthet fizetős szolgáltatási szintre éles `F0` környezetben.

## <a name="setting-up"></a>Beállítása

### <a name="create-a-new-gradle-project"></a>Új Gradle-projekt létrehozása

Egy konzolablakban (például cmd, PowerShell vagy Bash) hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá. 

```console
mkdir myapp && cd myapp
```

Futtassa `gradle init` a parancsot a munkakönyvtárból. Ez a parancs alapvető buildfájlokat hoz létre a Gradle-hez, beleértve a *build.gradle.kts* fájlt, amely futásidőben használatos az alkalmazás létrehozásához és konfigurálásához.

```console
gradle init --type basic
```

Amikor a rendszer kéri, hogy válasszon **egy DSL-t,** válassza a **Kotlin lehetőséget.**

## <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Keresse *meg a build.gradle.kts* et, és nyissa meg a kívánt IDE-val vagy szövegszerkesztővel. Ezután másolja be a következő buildkonfigurációt. Ez a konfiguráció java-alkalmazásként definiálja a projektet, amelynek belépési pontja a **ContentModeratorQuickstart osztály.** Importálja a Content Moderator ügyféloldali kódtárat és a JSON-szerializáláshoz készült GSON SDK-t.

```kotlin
plugins {
    java
    application
}

application{ 
    mainClassName = "ContentModeratorQuickstart"
}

repositories{
    mavenCentral()
}

dependencies{
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-contentmoderator", version = "1.0.2-beta")
    compile(group = "com.google.code.gson", name = "gson", version = "2.8.5")
}
```

### <a name="create-a-java-file"></a>Java-fájl létrehozása


Futtassa a következő parancsot a munkakönyvtárban egy projektforrásmappa létrehozásához:

```console
mkdir -p src/main/java
```

Lépjen az új mappába, és hozzon létre egy *ContentModeratorQuickstart.java nevű fájlt.* Nyissa meg a kívánt szerkesztőben vagy IDE-ban, és adja hozzá a következő `import` utasításokat:

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imports)]

> [!TIP]
> Szeretné egyszerre megtekinteni a teljes gyorsindítási kódfájlt? Ezt a [GitHubon találhatja](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java)meg, amely ebben a rövid útmutatóban található példakódokat tartalmazza.

Az alkalmazás **ContentModeratorQuickstart osztályában** hozzon létre változókat az erőforrás kulcsához és végpontjához.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_creds)]

> [!IMPORTANT]
> Nyissa meg az Azure Portalt. Ha az Content Moderator szakaszban létrehozott  erőforrás sikeresen üzembe lett folyamatban, kattintson az **Erőforrás** ugrás gombra a **Következő lépések területen.** A kulcsot és a végpontot az  erőforrás kulcs- és végpontoldalán, az **erőforrás-kezelés alatt találja.** 
>
> Ne felejtse el eltávolítani a kulcsot a kódból, amikor végzett, és soha ne tegye közzé nyilvánosan. Éles környezetben érdemes lehet biztonságos módon tárolni és elérni a hitelesítő adatokat. További információt a biztonsági Cognitive Services [talál.](../../../cognitive-services-security.md)

Az alkalmazás fő **metódusában** adja hozzá az ebben a rövid útmutatóban használt metódusok hívásait. Ezeket a metódusokat később fogja definiálni.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_maincalls)]


## <a name="object-model"></a>Objektummodell

Az alábbi osztályok a Java-ügyféloldali kódtár néhány Content Moderator kezelik.

|Név|Leírás|
|---|---|
|[ContentModeratorClient](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient)|Erre az osztályra az összes Content Moderator szükség. Példányosodhat az előfizetés adataival, és más osztályok példányainak előállítására használhatja.|
|[ImageModeration (Képmoderálás)](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.imagemoderations)|Ez az osztály a felnőtt tartalmak, személyes adatok vagy emberi arcok képelemzési funkcióját biztosítja.|
|[TextModerations (Szövegmódok)](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.textmoderations)|Ez az osztály biztosítja a nyelv, trágárság, hibák és személyes adatok szövegelemzési funkcióját.|
|[Felülvizsgálatok](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.reviews)|Ez az osztály biztosítja a Felülvizsgálati API-k funkcióit, beleértve a feladatok létrehozásának módszereit, az egyéni munkafolyamatokat és az emberi felülvizsgálatokat.|


## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan kell elvégezni a következő feladatokat Content Moderator Java-ügyfélkódtárával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Szöveg moderálása](#moderate-text)
* [Képek moderálása](#moderate-images)


## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Az alkalmazás metódusában hozzon létre egy `main` [ContentModeratorClient](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient) objektumot az előfizetés végpontértékével és előfizetői kulcsával.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_client)]



## <a name="moderate-text"></a>Szöveg moderálása

### <a name="set-up-sample-text"></a>Mintaszöveg beállítása

A **ContentModeratorQuickstart** osztály tetején definiálja a helyi szövegfájlra való hivatkozást. Adjon hozzá egy .txt fájlt a projektkönyvtárhoz, és írja be az elemezni kívánt szöveget.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod_var)]

### <a name="analyze-text"></a>Szöveg elemzése

Hozzon létre egy új metódust, amely beolvassa a .txt fájlt, és minden sorban behívja **a screenText** metódust.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod)]

### <a name="print-text-moderation-results"></a>Szövegmoderálási eredmények nyomtatása

Adja hozzá a következő kódot, hogy a moderálási eredményeket a projektkönyvtárban található .json fájlba nyomtassa.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod_print)]

Zárja be az `try` és az utasítást a metódus `catch` befejezéséhez.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod_catch)]


## <a name="moderate-images"></a>Képek moderálása

### <a name="set-up-sample-image"></a>Mintakép beállítása

Egy új metódusban egy **[BodyModelModel](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.models.bodymodelmodel)** objektumot hoz létre egy adott URL-sztring használatával, amely egy képre mutat.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod)]


### <a name="define-helper-class"></a>Segítőosztály meghatározása

Ezután a *ContentModeratorQuickstart.java* fájlban adja hozzá a következő osztálydefiníciót a **ContentModeratorQuickstart osztályban.** Ez a belső osztály használatos a képmoderálási folyamatban.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_evaluationdata)]


### <a name="analyze-content"></a>Tartalom elemzése
Ez a kódsor ellenőrzi a képet a megadott URL-címen, hogy nem tartalmaz-e felnőtteknek szóló vagy ritka tartalmat. Ezekkel a kifejezésekkel kapcsolatos információkért tekintse meg a képmoderálási fogalmi útmutatót.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_ar)]

### <a name="check-for-text"></a>Szöveg ellenőrzése
Ez a kódsor ellenőrzi, hogy látható-e a kép.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Arcok ellenőrzése
Ez a kódsor emberi arcokat keres a képen.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_faces)]

Végül tárolja a visszaadott adatokat a `EvaluationData` listában.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_storedata)]

### <a name="print-results"></a>Eredmények nyomtatása

A hurok után adja hozzá a következő kódot, amely kiírja az eredményeket a konzolra és egy kimeneti fájlba `while` *(src/main/resources/ModerationOutput.jsa következőn:*.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_printdata)]

Zárja be az `try` utasítást, és adjon hozzá egy utasítást a `catch` metódus befejezéséhez.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_catch)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Az alkalmazást a következővel építheti fel:

```console
gradle build
```

Futtassa az alkalmazást az `gradle run` paranccsal:

```console
gradle run
```

Ezután lépjen az *src/main/resources/ModerationOutput.jsa* fájlban, és tekintse meg a tartalommoderálás eredményeit.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni vagy eltávolítani szeretne egy Cognitive Services előfizetést, törölheti az erőforrást vagy erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított összes többi erőforrást is törli.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan használhatja a Content Moderator Java-kódtárat moderálási feladatok végrehajtásához. Ezután egy fogalmi útmutatót elolvasva további információt olvashat a képek vagy más médiatartalmak moderálásának módjáról.

> [!div class="nextstepaction"]
> [Képmoderálási fogalmak](../../image-moderation-api.md)
