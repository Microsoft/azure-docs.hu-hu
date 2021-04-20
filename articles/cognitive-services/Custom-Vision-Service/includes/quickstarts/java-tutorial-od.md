---
author: PatrickFarley
ms.custom: devx-track-java
ms.author: pafarley
ms.service: cognitive-services
ms.date: 10/13/2020
ms.openlocfilehash: 16112ffe7ba5fbc23335f9b60cdcbc045ea7cd2b
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725142"
---
Az objektumészlelési modell Custom Vision java-ügyféloldali kódtár használatának első lépések. Kövesse az alábbi lépéseket a csomag telepítéséhez, és próbálja ki az alapszintű feladatokhoz szükséges példakódot. Ezt a példát sablonként használhatja saját képfelismerő alkalmazás építéséhez.

> [!NOTE]
> Ha kódírás nélkül szeretne objektumészlelési modellt felépíteni és betaníteni, tekintse meg inkább a [böngészőalapú útmutatót.](../../get-started-build-detector.md) 

Használja a Custom Vision Java-ügyféloldali kódtárát a következőre:

* Új Custom Vision-projekt létrehozása
* Címkék hozzáadása a projekthez
* Képek feltöltése és címkézése
* A projekt betanítása
* Az aktuális iteráció közzététele
* Az előrejelzési végpont tesztelése

[Referenciadokumentáció](/java/api/overview/azure/cognitiveservices/client/customvision) | Kódtár [forráskódja (betanítás)](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-customvision-training) [(előrejelzés)](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-customvision-prediction)| Összetevő (Maven) [(betanítás)](https://search.maven.org/artifact/com.azure/azure-cognitiveservices-customvision-training/1.1.0-preview.2/jar) [(előrejelzés)](https://search.maven.org/artifact/com.azure/azure-cognitiveservices-customvision-prediction/1.1.0-preview.2/jar)  | 
 [minták](/samples/browse/?products=azure&terms=custom%20vision)


## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [Hozzon létre egyet ingyenesen](https://azure.microsoft.com/free/cognitive-services/)
* A Java fejlesztői készlet [(JDK) aktuális verziója](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* A [Gradle buildeszköz ,](https://gradle.org/install/)vagy egy másik függőségkezelő.
* Ha már rendelkezik Azure-előfizetéssel, hozzon létre egy Custom Vision-erőforrást, és hozzon létre egy Custom Vision-erőforrást a Azure Portal-ban egy betanítás és előrejelzési erőforrás létrehozásához, valamint a kulcsok és a végpont lekért <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision"  title=" "  target="_blank"> </a> létrehozásához. Várja meg az üzembe helyezést, és kattintson az **Erőforráshoz ugrás gombra.**
    * Szüksége lesz a létrehozott erőforrások kulcsra és végpontra ahhoz, hogy az alkalmazást a Custom Vision. A kulcsot és a végpontot a rövid útmutató későbbi, alábbi kódába fogja beilleszteni.
    * Az ingyenes tarifacsomag ( ) használatával kipróbálhatja a szolgáltatást, és később frissíthet fizetős szolgáltatási szintre éles `F0` környezetben.

## <a name="setting-up"></a>Beállítása

### <a name="create-a-new-gradle-project"></a>Új Gradle-projekt létrehozása

Egy konzolablakban (például cmd, PowerShell vagy Bash) hozzon létre egy új könyvtárat az alkalmazáshoz, és keresse meg. 

```console
mkdir myapp && cd myapp
```

Futtassa `gradle init` a parancsot a munkakönyvtárból. Ez a parancs alapvető buildfájlokat hoz létre a Gradle-hez, beleértve a *build.gradle.kts* fájlt, amely futásidőben használatos az alkalmazás létrehozásához és konfigurálásához.

```console
gradle init --type basic
```

Amikor a rendszer kéri, hogy válasszon **egy DSL-t,** válassza a **Kotlin lehetőséget.**

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Keresse *meg a build.gradle.kts* et, és nyissa meg az előnyben részesített IDE-jével vagy szövegszerkesztőjével. Ezután másolja be a következő buildkonfigurációt. Ez a konfiguráció java-alkalmazásként definiálja a projektet, amelynek belépési pontja a **CustomVisionQuickstart osztály.** Importálja a Custom Vision kódtárakat.

```kotlin
plugins {
    java
    application
}
application { 
    mainClassName = "CustomVisionQuickstart"
}
repositories {
    mavenCentral()
}
dependencies {
    compile(group = "com.azure", name = "azure-cognitiveservices-customvision-training", version = "1.1.0-preview.2")
    compile(group = "com.azure", name = "azure-cognitiveservices-customvision-prediction", version = "1.1.0-preview.2")
}
```

### <a name="create-a-java-file"></a>Java-fájl létrehozása


Futtassa a következő parancsot a munkakönyvtárban egy projektforrásmappa létrehozásához:

```console
mkdir -p src/main/java
```

Lépjen az új mappába, és hozzon létre egy *CustomVisionQuickstart.java nevű fájlt.* Nyissa meg a kívánt szerkesztőben vagy IDE-ban, és adja hozzá a következő `import` utasításokat:

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_imports)]

> [!TIP]
> Szeretné egyszerre megtekinteni a teljes gyorsindítási kódfájlt? Ezt a [GitHubon találhatja](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java)meg, amely ebben a rövid útmutatóban található példakódokat tartalmazza.


Az alkalmazás **CustomVisionQuickstart** osztályában hozzon létre változókat az erőforrás kulcsához és végpontjához.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_creds)]


> [!IMPORTANT]
> Nyissa meg az Azure Portalt. Ha Custom Vision az Előfeltételek szakaszban  létrehozott erőforrás-erőforrásokat, kattintson az **Erőforrás** ugrás gombra a **Következő lépések területen.** A kulcsokat és a végpontot  az erőforrások kulcs- és végpontoldalán, az **erőforrás-kezelés alatt találja.** A betanítás és az előrejelzési erőforrások kulcsait, valamint a betanító erőforrás API-végpontját is be kell szereznie.
>
> Ne felejtse el eltávolítani a kulcsot a kódból, amikor végzett, és soha ne tegye közzé nyilvánosan. Éles környezetben érdemes lehet biztonságos módon tárolni és elérni a hitelesítő adatokat. További információért tekintse meg a Cognitive Services [cikkét.](../../../cognitive-services-security.md)

Az alkalmazás fő **metódusában** adja hozzá az ebben a rövid útmutatóban használt metódusok hívásait. Ezeket később fogja definiálni.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_maincalls_od)]

## <a name="object-model"></a>Objektummodell

Az alábbi osztályok és felületek a Java ügyféloldali kódtár néhány fő funkcióját Custom Vision kezelik.

|Név|Leírás|
|---|---|
|[CustomVisionTrainingClient](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) | Ez az osztály kezeli a modellek létrehozását, betanítását és közzétételét. |
|[CustomVisionPredictionClient](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient)| Ez az osztály kezeli a modellek objektumészlelési előrejelzésekhez való lekérdezését.|
|[ImagePrediction (Képprediction)](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.prediction.models.imageprediction)| Ez az osztály egyetlen objektum-előrejelzést határoz meg egyetlen képen. Ez tartalmazza az objektumazonosító és -név tulajdonságait, a határolókeret helyét és a megbízhatósági pontszámot.|

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan kell elvégezni a következő feladatokat Custom Vision Java-ügyfélkódtárban:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Új Custom Vision-projekt létrehozása](#create-a-new-custom-vision-project)
* [Címkék hozzáadása a projekthez](#add-tags-to-the-project)
* [Képek feltöltése és címkézése](#upload-and-tag-images)
* [A projekt betanítása](#train-the-project)
* [Az aktuális iteráció közzététele](#publish-the-current-iteration)
* [Az előrejelzési végpont tesztelése](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

A fő **módszerben** példányosodjon a betanító és előrejelzési ügyfelek a végpont és a kulcsok használatával.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>Új Custom Vision-projekt létrehozása

A következő metódus egy objektumészlelési projektet hoz létre. A létrehozott projekt a [Custom Vision webhelyén](https://customvision.ai/) jelenik meg, amelyet korábban felkeresett. A projekt létrehozásakor további beállításokat a [CreateProject](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_&preserve-view=true) metódus-túlterhelések részben talál (lásd [a Detektor](../../get-started-build-detector.md) webportáljának összeállítása útmutatót).

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_create_od)]

## <a name="add-tags-to-your-project"></a>Címkék hozzáadása a projekthez

Ez a metódus határozza meg azokat a címkéket, amelyeken a modellt betanítja.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_tags_od)]

## <a name="upload-and-tag-images"></a>Képek feltöltése és címkézése

Először töltse le a projekthez a mintaképeket. Mentse a Képek [mintamappa tartalmát](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ObjectDetection/Images) a helyi eszközre.

> [!NOTE]
> A betanítás befejezéséhez szélesebb képkészletre van szüksége? A Microsoft Sets-projekt, a Trove lehetővé teszi képek gyűjtését és megvásárlását betanítás céljából. Miután összegyűjtötte a képeket, letöltheti őket, majd a szokásos módon importálhatja Custom Vision saját projektjére. További [információért látogasson el a Trove](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3) oldalára.

Ha képeket címkéz az objektum-észlelési projektekben, az egyes címkézett objektumok régióját normalizált koordináták használatával kell megadnia. A következő kód a mintául szolgáló rendszerképeket társítja a címkézett régiójával.

> [!NOTE]
> Ha nem tudja megjelölni a régiók koordinátáit egy kattintással és húzással, a következő webhelyen használhatja a [webes felhasználói Customvision.ai.](https://www.customvision.ai/) Ebben a példában a koordináták már meg vannak téve.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_od_mapping)]

A következő kódblokk hozzáadja a képeket a projekthez. Módosítania kell a hívások argumentumait, hogy azok a letöltött elot és mappa helyére `GetImage` mutasson.  

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_upload_od)]

Az előző kódrészlet két segítő függvényt használ, amelyek erőforrás-streamként lekérik a képeket, majd feltöltik őket a szolgáltatásba (legfeljebb 64 képet tölthet fel egyetlen kötegbe). Definiálja ezeket a metódusokat. 

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_helpers)]

## <a name="train-the-project"></a>A projekt betanítása

Ez a metódus hozza létre az első betanítási iterációt a projektben. A betanítás befejezéséig lekérdezi a szolgáltatást.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_train_od)]


## <a name="publish-the-current-iteration"></a>Az aktuális iteráció közzététele

Ez a metódus elérhetővé teszi a modell aktuális iterációját a lekérdezéshez. A modell nevét használhatja hivatkozásként az előrejelzési kérések elküldését. A saját értékét kell `predictionResourceId` megadnia. Az előrejelzési erőforrás azonosítóját az erőforrás **Áttekintés** lapján találja a Azure Portal előfizetés azonosítója **listában.**

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_publishOD)]

## <a name="test-the-prediction-endpoint"></a>Az előrejelzési végpont tesztelése

Ez a metódus betölti a tesztképet, lekérdezi a modell végpontját, és előrejelzési adatokat ad ki a konzolon.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_predict_od)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Az alkalmazást a következővel építheti fel:

```console
gradle build
```

Futtassa az alkalmazást az `gradle run` paranccsal:

```console
gradle run
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni vagy eltávolítani szeretne egy Cognitive Services előfizetést, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított összes többi erőforrást is törli.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

[!INCLUDE [clean-od-project](../../includes/clean-od-project.md)]

## <a name="next-steps"></a>Következő lépések

Ezzel az objektumészlelési folyamat minden lépését megtette a kódban. Ez a minta egyetlen betanítási iterációt hajt végre, de gyakran többször is be kell majd képezni és tesztelni kell a modellt, hogy pontosabb legyen. Az alábbi útmutató a képosztályozással foglalkozik, az alapelvei azonban hasonlóak az objektumészlelés alapelveihez.

> [!div class="nextstepaction"]
> [Modell tesztelése és újratanítása](../../test-your-model.md)

* [Mi a Custom Vision?](../../overview.md)
* A minta forráskódja a [GitHubon található](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java)
