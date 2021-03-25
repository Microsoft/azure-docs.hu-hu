---
title: Content Moderator Java ügyféloldali kódtár gyors üzembe helyezése
titleSuffix: Azure Cognitive Services
description: Ebből a rövid útmutatóból megtudhatja, hogyan kezdheti el az Azure Content Moderator a Javához készült ügyféloldali kódtárat. Tartalom-szűrési szoftvereket hozhat létre az alkalmazásban, hogy megfeleljen a szabályozásoknak, vagy megőrizze a kívánt környezetet a felhasználók számára.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 10/16/2020
ms.custom: devx-track-java, cog-serv-seo-aug-2020
ms.author: pafarley
ms.openlocfilehash: 646b4378c887bb30bfbd945693aacc4e50c2f02c
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105027965"
---
Ismerkedjen meg a Javához készült Azure Content Moderator ügyféloldali kódtáraval. Az alábbi lépéseket követve telepítheti a Maven-csomagot, és kipróbálhatja az alapszintű feladatokhoz tartozó példa kódját. 

Content Moderator egy AI-szolgáltatás, amely lehetővé teszi a potenciálisan sértő, kockázatos vagy egyéb módon nemkívánatos tartalmak kezelését. A AI-alapú Content moderációs szolgáltatással szövegeket, képeket és videókat kereshet, és automatikusan alkalmazhatja a tartalmi jelzőket. Tartalom-szűrési szoftvereket hozhat létre az alkalmazásban, hogy megfeleljen a szabályozásoknak, vagy megőrizze a kívánt környezetet a felhasználók számára.

A Javához készült Content Moderator ügyféloldali kódtár a következőre használható:

* Mérsékelt szöveg
* Közepes méretű képek

[Dokumentáció](/java/api/overview/azure/cognitiveservices/client/contentmoderator)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-contentmoderator)  | Összetevő [(Maven)](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-contentmoderator)  |  [Példák](/samples/browse/?products=azure&term=content-moderator)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* A [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html) aktuális verziója
* A [Gradle Build eszköz](https://gradle.org/install/)vagy egy másik függőségi kezelő.
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title=" hozzon létre egy Content moderator erőforrást, "  target="_blank"> és hozzon létre egy Content moderator-erőforrást </a> a Azure Portal a kulcs és a végpont beszerzéséhez. Várja meg, amíg üzembe helyezi, majd kattintson az **Ugrás erőforrásra** gombra.
    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazás Content Moderatorhoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-gradle-project"></a>Új Gradle-projekt létrehozása

Egy konzolablak (például a cmd, a PowerShell vagy a bash) ablakban hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá. 

```console
mkdir myapp && cd myapp
```

Futtassa a `gradle init` parancsot a munkakönyvtárból. Ez a parancs alapvető Build-fájlokat hoz létre a Gradle számára, beleértve a *Build. Gradle. KTS* fájlt, amelyet futásidőben használ az alkalmazás létrehozásához és konfigurálásához.

```console
gradle init --type basic
```

Amikor a rendszer rákérdez a **DSL** kiválasztására, válassza a **Kotlin** lehetőséget.

## <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Keresse meg a *Build. gradle. KTS* , és nyissa meg a kívánt ide-vagy szövegszerkesztővel. Ezután másolja a következő Build-konfigurációba. Ez a konfiguráció definiálja a projektet olyan Java-alkalmazásként, amelynek belépési pontja a **ContentModeratorQuickstart** osztály. Importálja az Content Moderator ügyféloldali kódtárat, valamint a GSON SDK-t a JSON-szerializáláshoz.

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


A munkakönyvtárból futtassa a következő parancsot egy projekt forrás mappájának létrehozásához:

```console
mkdir -p src/main/java
```

Navigáljon az új mappára, és hozzon létre egy *ContentModeratorQuickstart. Java* nevű fájlt. Nyissa meg a kívánt szerkesztőben vagy IDE, és adja hozzá a következő `import` utasításokat:

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imports)]

> [!TIP]
> Egyszerre szeretné megtekinteni a teljes rövid útmutató kódját? Megtalálhatja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java), amely a jelen rövid útmutatóban szereplő példákat tartalmazza.

Az alkalmazás **ContentModeratorQuickstart** osztályában hozzon létre változókat az erőforrás kulcsa és végpontja számára.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_creds)]

> [!IMPORTANT]
> Nyissa meg az Azure Portalt. Ha az **Előfeltételek** szakaszban létrehozott Content moderator erőforrás sikeresen telepítve van, kattintson az **Ugrás erőforrásra** gombra a **következő lépések** alatt. A kulcsot és a végpontot az erőforrás- **kezelés** területen, az erőforrás **kulcs és végpont** lapján találja. 
>
> Ne felejtse el eltávolítani a kulcsot a kódból, ha elkészült, és soha ne tegye közzé nyilvánosan. Éles környezetben érdemes lehet biztonságos módszert használni a hitelesítő adatok tárolásához és eléréséhez. További információt a Cognitive Services [biztonsági](../../../cognitive-services-security.md) cikkben talál.

Az alkalmazás **fő** metódusában adjon hozzá hívásokat az ebben a rövid útmutatóban használt módszerekhez. Ezeket később is megadhatja.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_maincalls)]


## <a name="object-model"></a>Objektummodell

A következő osztályok a Content Moderator Java ügyféloldali kódtár főbb funkcióit kezelik.

|Név|Leírás|
|---|---|
|[ContentModeratorClient](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient)|Ez az osztály minden Content Moderator funkcióhoz szükséges. Ezt az előfizetési adatok alapján hozza létre, és más osztályok példányainak előállítására használja.|
|[ImageModeration](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.imagemoderations)|Ez az osztály lehetővé teszi a képek elemzését a felnőtt tartalmak, a személyes adatok vagy az emberi arcok számára.|
|[TextModerations](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.textmoderations)|Ez az osztály a nyelv, a káromkodás, a hibák és a személyes adatok szövegének elemzéséhez nyújt funkciókat.|
|[Felülvizsgálatok](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.reviews)|Ez az osztály a felülvizsgálati API-k funkcionalitását biztosítja, beleértve a feladatok létrehozására, az egyéni munkafolyamatokra és az emberi felülvizsgálatokra vonatkozó módszereket is.|


## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat a Javához készült Content Moderator ügyféloldali kódtár használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Mérsékelt szöveg](#moderate-text)
* [Közepes méretű képek](#moderate-images)


## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Az alkalmazás `main` metódusában hozzon létre egy [ContentModeratorClient](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient) objektumot az előfizetési végpont értéke és az előfizetés kulcsa alapján.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_client)]



## <a name="moderate-text"></a>Mérsékelt szöveg

### <a name="set-up-sample-text"></a>Minta szövegének beállítása

A **ContentModeratorQuickstart** osztály tetején Definiáljon egy helyi szövegfájlra mutató hivatkozást. Vegyen fel egy. txt fájlt a projekt könyvtárába, és írja be az elemezni kívánt szöveget.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod_var)]

### <a name="analyze-text"></a>Szöveg elemzése

Hozzon létre egy új metódust, amely beolvassa a. txt fájlt, és minden sorban meghívja a **screenText** metódust.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod)]

### <a name="print-text-moderation-results"></a>Szöveg moderálási eredményeinek nyomtatása

Adja hozzá a következő kódot a moderálási eredmények egy. JSON-fájlba való nyomtatásához a projekt könyvtárába.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod_print)]

A `try` metódus befejezéséhez lépjen ki a és a `catch` utasításból.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod_catch)]


## <a name="moderate-images"></a>Közepes méretű képek

### <a name="set-up-sample-image"></a>Minta rendszerképének beállítása

Egy új metódusban állítson össze egy **[BodyModelModel](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.models.bodymodelmodel)** objektumot egy adott URL-karakterlánccal, amely egy képre mutat.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod)]


### <a name="define-helper-class"></a>Segítő osztály definiálása

Ezután adja hozzá a *ContentModeratorQuickstart. Java* fájlban a következő osztály-definíciót a **ContentModeratorQuickstart** osztályban. Ez a belső osztály a képmoderálási folyamat során használatos.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_evaluationdata)]


### <a name="analyze-content"></a>Tartalom elemzése
Ez a kódrészlet a megadott URL-címen ellenőrzi a felnőtt vagy a zamatos tartalomhoz tartozó képet. A jelen feltételekkel kapcsolatos információkért tekintse meg a képmoderálás fogalmi útmutatóját.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_ar)]

### <a name="check-for-text"></a>Szöveg keresése
Ez a kódrészlet a képen látható szöveget ellenőrzi.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Arcok keresése
Ez a kódrészlet ellenőrzi az emberi arcok képét.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_faces)]

Végül a listában tárolja a visszaadott adatokat `EvaluationData` .

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_storedata)]

### <a name="print-results"></a>Nyomtatási eredmények

A `while` hurok után adja hozzá a következő kódot, amely kiírja az eredményeket a konzolra, valamint egy kimeneti fájlba, az *src/Main/Resources/ModerationOutput.json* értékre.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_printdata)]

Fejezze be az `try` utasítást, és adjon hozzá egy `catch` utasítást a metódus végrehajtásához.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_catch)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Az alkalmazást az alábbiakkal hozhatja létre:

```console
gradle build
```

Futtassa az alkalmazást a `gradle run` paranccsal:

```console
gradle run
```

Ezután navigáljon a fájlhoz tartozó *src/Main/Resources/ModerationOutput.js* elemre, és tekintse meg a tartalom moderálásának eredményét.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="source-code"></a>Forráskód

* A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java)található.

## <a name="next-steps"></a>Következő lépések

Ebből a rövid útmutatóból megtudhatta, hogyan használhatja a Content Moderator Java-függvénytárat moderálási feladatok végrehajtásához. Következő lépésként tekintse meg a képek vagy más média moderálását egy elméleti útmutató elolvasásával.

> [!div class="nextstepaction"]
> [Képek moderálási fogalmai](../../image-moderation-api.md)
