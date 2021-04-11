---
author: PatrickFarley
ms.custom: devx-track-java
ms.author: pafarley
ms.service: cognitive-services
ms.date: 10/13/2020
ms.openlocfilehash: c169572e8af8ba992e604fc51b31db1146f11fda
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106112788"
---
Ismerkedjen meg a Java-Custom Vision ügyféloldali kódtár használatával a rendszerkép besorolási modelljének létrehozásához. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja az alapszintű feladatokhoz tartozó példa kódját. Ez a példa sablonként használható a saját rendszerkép-felismerő alkalmazás létrehozásához.

> [!NOTE]
> Ha a besorolási modellt kód írása _nélkül_ szeretné felépíteni és betanítani, tekintse meg a [böngészőalapú útmutatást](../../getting-started-build-a-classifier.md) .

A Javához készült Custom Vision ügyféloldali kódtár a következőre használható:

* Új Custom Vision-projekt létrehozása
* Címkék hozzáadása a projekthez
* Képek feltöltése és címkézése
* A projekt betanítása
* Az aktuális iteráció közzététele
* Az előrejelzési végpont tesztelése

[Referenciák dokumentációja](/java/api/overview/azure/cognitiveservices/client/customvision) | Könyvtár forráskódja [(képzés)](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-customvision-training) [(előrejelzés)](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-customvision-prediction)| Összetevő (Maven) ( [képzési)](https://search.maven.org/artifact/com.azure/azure-cognitiveservices-customvision-training/1.1.0-preview.2/jar) [(előrejelzési](https://search.maven.org/artifact/com.azure/azure-cognitiveservices-customvision-prediction/1.1.0-preview.2/jar))  | 
 [minták](/samples/browse/?products=azure&terms=custom%20vision)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* A [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html) aktuális verziója
* A [Gradle Build eszköz](https://gradle.org/install/)vagy egy másik függőségi kezelő.
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision"  title=" hozzon létre egy Custom Vision erőforrást egy "  target="_blank"> Custom Vision erőforrás létrehozásához </a> a Azure Portal egy képzési és előrejelzési erőforrás létrehozásához, valamint a kulcsok és a végpont beszerzéséhez. Várja meg, amíg üzembe helyezi, majd kattintson az **Ugrás erőforrásra** gombra.
    * Az alkalmazás Custom Visionhoz való összekapcsolásához szüksége lesz a létrehozott erőforrások kulcsára és végpontra. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
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

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Keresse meg a *Build. gradle. KTS* , és nyissa meg a kívánt ide-vagy szövegszerkesztővel. Ezután másolja a következő Build-konfigurációba. Ez a konfiguráció definiálja a projektet olyan Java-alkalmazásként, amelynek belépési pontja a **CustomVisionQuickstart** osztály. Importálja a Custom Vision kódtárakat.

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


A munkakönyvtárból futtassa a következő parancsot egy projekt forrás mappájának létrehozásához:

```console
mkdir -p src/main/java
```

Navigáljon az új mappára, és hozzon létre egy *CustomVisionQuickstart. Java* nevű fájlt. Nyissa meg a kívánt szerkesztőben vagy IDE, és adja hozzá a következő `import` utasításokat:

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_imports)]

> [!TIP]
> Egyszerre szeretné megtekinteni a teljes rövid útmutató kódját? Megtalálhatja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java), amely a jelen rövid útmutatóban szereplő példákat tartalmazza.


Az alkalmazás **CustomVisionQuickstart** osztályában hozzon létre változókat az erőforrás kulcsainak és végpontjának megfelelően.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_creds)]


> [!IMPORTANT]
> Nyissa meg az Azure Portalt. Ha az **Előfeltételek** szakaszban létrehozott Custom Vision erőforrások telepítése sikeresen megtörtént, kattintson az **Ugrás erőforrásra** gombra a **következő lépések** alatt. A kulcsok és a végpont az erőforrások **kulcs és végpont** oldalain található. Be kell szereznie a képzési és előrejelzési erőforrások kulcsait, valamint a képzési erőforrás API-végpontját.
>
> A jóslat erőforrás-azonosító értékét az erőforrás **Tulajdonságok** lapján, az **előfizetés azonosítója** mezőben találja.
> 
> Ne felejtse el eltávolítani a kulcsot a kódból, ha elkészült, és soha ne tegye közzé nyilvánosan. Éles környezetben érdemes lehet biztonságos módszert használni a hitelesítő adatok tárolásához és eléréséhez. További információt a Cognitive Services [biztonsági](../../../cognitive-services-security.md) cikkben talál.

Az alkalmazás **fő** metódusában adjon hozzá hívásokat az ebben a rövid útmutatóban használt módszerekhez. Ezeket később is megadhatja.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_maincalls)]

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek a Custom Vision Java ügyféloldali kódtár főbb funkcióit kezelik.

|Név|Leírás|
|---|---|
|[CustomVisionTrainingClient](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) | Ez az osztály kezeli a modellek létrehozását, betanítását és közzétételét. |
|[CustomVisionPredictionClient](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient)| Ez az osztály kezeli a modellek lekérdezését a képbesorolási előrejelzésekhez.|
|[ImagePrediction](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.prediction.models.imageprediction)| Ez az osztály egyetlen előrejelzést definiál egyetlen rendszerképhez. Az objektum AZONOSÍTÓjának és nevének, valamint a megbízhatósági pontszámnak a tulajdonságait tartalmazza.|

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat a Javához készült Custom Vision ügyféloldali kódtár használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Új Custom Vision-projekt létrehozása](#create-a-new-custom-vision-project)
* [Címkék hozzáadása a projekthez](#add-tags-to-the-project)
* [Képek feltöltése és címkézése](#upload-and-tag-images)
* [A projekt betanítása](#train-the-project)
* [Az aktuális iteráció közzététele](#publish-the-current-iteration)
* [Az előrejelzési végpont tesztelése](#test-the-prediction-endpoint)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

A **Main** metódusban hozza létre a képzési és előrejelzési ügyfeleket a végpont és a kulcsok használatával.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_auth)]


## <a name="create-a-custom-vision-project"></a>Custom Vision projekt létrehozása

T # # hozzon létre egy új Custom Vision projektet

Ez a következő módszer rendszerkép-besorolási projektet hoz létre. A létrehozott projekt a [Custom Vision webhelyén](https://customvision.ai/) jelenik meg, amelyet korábban felkeresett. A projekt létrehozásakor további beállítások megadásához tekintse meg a [CreateProject](/java/api/com.microsoft.azure.cognitiveservices.vision.customvision.training.trainings.createproject#com_microsoft_azure_cognitiveservices_vision_customvision_training_Trainings_createProject_String_CreateProjectOptionalParameter_&preserve-view=true) metódus túlterhelését (a [Kiderítő webportál összeállításának](../../get-started-build-detector.md) útmutatója).

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_create)]

## <a name="add-tags-to-your-project"></a>Címkék hozzáadása a projekthez

Ez a módszer határozza meg azokat a címkéket, amelyeken a modellt be fogja tanítani.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_tags)]

## <a name="upload-and-tag-images"></a>Képek feltöltése és címkézése

Először töltse le a projekthez tartozó mintaképeket. Mentse a [Sample images mappa](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ImageClassification/Images) tartalmát a helyi eszközre.

> [!NOTE]
> Szüksége van egy szélesebb körű rendszerképekre a képzés befejezéséhez? A Microsoft garázs-projekt, amely lehetővé teszi, hogy beszerezze a betanítási célokra szánt lemezképek készleteit. A képek összegyűjtése után letöltheti őket, majd a szokásos módon importálhatja őket a Custom Vision-projektbe. További információért látogasson el az [adattárház oldalára](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3) .

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_upload)]

Az előző kódrészlet két segítő függvényt használ, amely erőforrás-adatfolyamként kéri le a lemezképeket, és feltölti őket a szolgáltatásba (egy kötegben akár 64 képet is feltölthet).

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_helpers)]

## <a name="train-the-project"></a>A projekt betanítása

Ez a metódus létrehozza az első betanítási iterációt a projektben. A szolgáltatás lekérdezi a szolgáltatást, amíg a képzés be nem fejeződik.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_train)]


## <a name="publish-the-current-iteration"></a>Az aktuális iteráció közzététele

Ezzel a módszerrel elérhetővé válik a modell aktuális iterációja a lekérdezéshez. A modell neve hivatkozásként használható az előrejelzési kérelmek küldéséhez. Meg kell adnia a saját értékét `predictionResourceId` . Az előrejelzési erőforrás azonosítója az erőforrás **Áttekintés** lapján, az **előfizetés-azonosítóként** felsorolt Azure Portalban található.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_publish)]

## <a name="test-the-prediction-endpoint"></a>Az előrejelzési végpont tesztelése

Ez a módszer betölti a teszt képét, lekérdezi a modell végpontját, és előrejelzési adatokat küld a konzolnak.

[!code-java[](~/cognitive-services-quickstart-code/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java?name=snippet_predict)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

Az alkalmazást az alábbiakkal hozhatja létre:

```console
gradle build
```

Futtassa az alkalmazást a `gradle run` paranccsal:

```console
gradle run
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan végezhető el a képbesorolási folyamat minden lépése a kódban. Ez a minta egyetlen betanítási iterációt hajt végre, de gyakran több alkalommal kell betanítania és tesztelni a modellt, hogy pontosabb legyen.

> [!div class="nextstepaction"]
> [Modell tesztelése és újratanítása](../../test-your-model.md)

* [Mi a Custom Vision?](../../overview.md)
* A minta forráskódja megtalálható a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/CustomVision/src/main/java/com/microsoft/azure/cognitiveservices/vision/customvision/samples/CustomVisionSamples.java)
