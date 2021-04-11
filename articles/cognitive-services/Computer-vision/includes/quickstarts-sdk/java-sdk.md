---
title: 'Gyors útmutató: optikai karakterfelismerési ügyféloldali kódtár Javához'
description: Ebben a rövid útmutatóban megismerheti a Javához készült optikai karakterfelismerési ügyféloldali kódtárat.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.custom: devx-track-java
ms.author: pafarley
ms.openlocfilehash: 84ac8e8309d9f1d0536d0f7a16ab9cd9f3c10a2c
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284779"
---
<a name="HOLTop"></a>

Az optikai karakterfelismerési ügyféloldali kódtár használatával a nyomtatott és a kézírásos szöveg olvasható a képekben.

[Dokumentáció](/java/api/overview/azure/cognitiveservices/client/computervision)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-computervision)  | Összetevő [(Maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-computervision)  |  [Példák](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* A [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html) aktuális verziója
* A [Gradle Build eszköz](https://gradle.org/install/)vagy egy másik függőségi kezelő.
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" hozzon létre egy Computer Vision erőforrást, "  target="_blank"> és hozzon létre egy Computer Vision-erőforrást </a> a Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése** elemre.
    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazás Computer Vision szolgáltatáshoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
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

Ez a rövid útmutató a Gradle függőség-kezelőt használja. Megtalálhatja az ügyféloldali függvénytárat és az egyéb függőségi kezelők információit a [Maven központi adattárában](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-computervision).

Keresse meg a *Build. gradle. KTS* , és nyissa meg a kívánt ide-vagy szövegszerkesztővel. Ezután másolja a következő Build-konfigurációba. Ez a konfiguráció definiálja a projektet olyan Java-alkalmazásként, amelynek belépési pontja a **ComputerVisionQuickstarts** osztály. Importálja a Computer Vision könyvtárat.

```kotlin
plugins {
    java
    application
}
application { 
    mainClassName = "ComputerVisionQuickstarts"
}
repositories {
    mavenCentral()
}
dependencies {
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-computervision", version = "1.0.4-beta")
}
```

### <a name="create-a-java-file"></a>Java-fájl létrehozása

A munkakönyvtárból futtassa a következő parancsot egy projekt forrás mappájának létrehozásához:

```console
mkdir -p src/main/java
```

> [!TIP]
> Egyszerre szeretné megtekinteni a teljes rövid útmutató kódját? Megtalálhatja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java), amely a jelen rövid útmutatóban szereplő példákat tartalmazza.

Navigáljon az új mappára, és hozzon létre egy *ComputerVisionQuickstarts. Java* nevű fájlt. Nyissa meg az előnyben részesített szerkesztőben vagy IDE.

### <a name="find-the-subscription-key-and-endpoint"></a>Az előfizetés kulcsának és végpontjának megkeresése

Nyissa meg az Azure Portalt. Ha az **Előfeltételek** szakaszban létrehozott Computer Vision erőforrás sikeresen telepítve van, kattintson az **Ugrás erőforrásra** gombra a **következő lépések** alatt. Az előfizetési kulcsot és a végpontot az erőforrás- **kezelés** lehetőség alatt találja az erőforrás **kulcs és végpont** lapján. 

Adja meg a **ComputerVisionQuickstarts** osztályt. Hozzon létre változókat a Computer Vision előfizetési kulcs és végpont számára. Illessze be az előfizetési kulcsot és a végpontot a következő kódra, ahol az szerepel. Az Computer Vision-végpont rendelkezik az űrlappal `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/` .

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imports_and_vars)]

> [!IMPORTANT]
> Ne feledje, hogy ha elkészült, távolítsa el az előfizetési kulcsot a kódból, és soha ne tegye közzé nyilvánosan. Éles környezetben érdemes lehet biztonságos módszert használni a hitelesítő adatok tárolásához és eléréséhez. Például az [Azure Key Vault](../../../../key-vault/general/overview.md).

Az alkalmazás **fő** metódusában adjon hozzá hívásokat az ebben a rövid útmutatóban használt módszerekhez. Ezeket később is megadhatja.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_beginmain)]

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_authinmain)]

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_readinmain)]

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_endmain)]
> [!div class="nextstepaction"]
> [Megállítottam az ügyfelet](?success=set-up-client#object-model) , hogy [egy hibába ütközött](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=set-up-client)

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek az OCR Java SDK főbb funkcióit kezelik.

|Név|Leírás|
|---|---|
| [ComputerVisionClient](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) | Ez az osztály minden Computer Vision funkcióhoz szükséges. Ezt az előfizetési adatok alapján hozza létre, és más osztályok példányainak előállítására használja.|

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat a Javához készült OCR ügyféloldali kódtár használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Nyomtatott és kézzel írt szöveg olvasása](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Egy új metódusban hozza létre a [ComputerVisionClient](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) objektumot a végponttal és a kulccsal.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_auth)]

> [!div class="nextstepaction"]
> [Hitelesítettem a-ügyfelet](?success=authenticate-client#read-printed-and-handwritten-text) [, hogy egy hibába ütközött](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=authenticate-client)



## <a name="read-printed-and-handwritten-text"></a>Nyomtatott és kézzel írt szöveg olvasása

Az OCR szolgáltatás megjelenítheti a képen látható szöveget, és átalakíthatja a karakteres adatfolyamba. Ez a szakasz egy olyan metódust határoz meg, `ReadFromFile` amely egy helyi fájl elérési útját veszi át, és kiírja a rendszerkép szövegét a konzolra.

> [!TIP]
> Az URL-cím által hivatkozott távoli rendszerkép szövege is olvasható. Tekintse meg a [ComputerVision](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision) metódusokat, például az **olvasást**. Vagy a távoli rendszerképeket érintő forgatókönyvek esetében tekintse meg a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) található mintakód-kódot.

### <a name="set-up-test-image"></a>Tesztelési rendszerkép beállítása

Hozzon létre egy **erőforrást/** mappát a projekt **src/Main/** mappájából, és adjon hozzá egy képet, amelyből szöveget szeretne olvasni. Itt töltheti le az itt használható [minta-rendszerképet](https://raw.githubusercontent.com/MicrosoftDocs/azure-docs/master/articles/cognitive-services/Computer-vision/Images/readsample.jpg) .

Ezután adja hozzá a következő metódus-definíciót a **ComputerVisionQuickstarts** osztályhoz. Módosítsa az értékét a `localFilePath` képfájlnak megfelelő értékre. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_setup)]

### <a name="call-the-read-api"></a>Az olvasási API meghívása

Ezután adja hozzá a következő kódot a **readInStreamWithServiceResponseAsync** metódus meghívásához az adott képhez.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_call)]

A következő kódrészlet kibontja a művelet AZONOSÍTÓját az olvasási hívás válaszával. Ezt az azonosítót egy segítő metódussal használja a szöveg olvasási eredményeinek a konzolra való kinyomtatásához. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_response)]

A try/catch blokk és a metódus definíciójának lezárása.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_catch)]

### <a name="get-read-results"></a>Olvasási eredmények beolvasása

Ezután adjon hozzá egy definíciót a segítő metódushoz. Ez a metódus az előző lépés műveleti AZONOSÍTÓját használja az olvasási művelet lekérdezéséhez és az OCR eredményeinek lekéréséhez, ha elérhetők.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_result_helper_call)]

A metódus többi része elemezi az OCR eredményeit, és kinyomtatja őket a konzolra.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_result_helper_print)]

Végül adja hozzá a fent használt másik segítő módszert, amely kibontja a műveleti azonosítót a kezdeti válaszból.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_opid_extract)]

> [!div class="nextstepaction"]
> [Beolvastam](?success=read-printed-handwritten-text#run-the-application) a [problémát](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=read-printed-handwritten-text)

## <a name="run-the-application"></a>Az alkalmazás futtatása

Az alkalmazást az alábbiakkal hozhatja létre:

```console
gradle build
```

Futtassa az alkalmazást a `gradle run` paranccsal:

```console
gradle run
```

> [!div class="nextstepaction"]
> [Futtattam az alkalmazást a](?success=run-the-application#clean-up-resources) [probléma megoldásához](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=run-the-application)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [](?success=clean-up-resources#next-steps) [Egy hibába ütközött az](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=clean-up-resources) erőforrások

## <a name="next-steps"></a>Következő lépések

Ebből a rövid útmutatóból megtudhatta, hogyan használhatja az OCR Java-függvénytárat a feladatok elvégzésére. Ezután tekintse át a dokumentációt, és ismerkedjen meg a könyvtárral.

> [!div class="nextstepaction"]
>[OCR SDK-dokumentáció (Java)](/java/api/overview/azure/cognitiveservices/client/computervision)


* [OCR – áttekintés](../../overview-ocr.md)
* A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java)található.
