---
title: 'Minta: képfeldolgozó alkalmazás feltárása C-ben #'
titleSuffix: Azure Cognitive Services
description: Ismerkedjen meg az Azure Cognitive Services Computer Vision APIt használó alapszintű Windows-alkalmazással. OCR végrehajtása, miniatűrök létrehozása és képek vizuális jellemzőinek használata.
services: cognitive-services
author: PatrickFarley
manager: nolachar
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 0a1878268beb135151a23b87d03839f3a51f4a96
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102432587"
---
# <a name="sample-explore-an-image-processing-app-with-c"></a>Minta: képfeldolgozó alkalmazás feltárása C-vel #

Fedezzen fel egy alapszintű Windows-alkalmazást, amely a Computer Visiont használja az optikai karakterfelismerés (OCR) elvégzéséhez, intelligens vágású miniatűrök létrehozásához, valamint a vizuális funkciók (például arcok) észleléséhez, kategorizálásához, címkézéséhez és leírásához a képen. Az alábbi példában elküldheti egy kép URL-címét vagy egy helyileg tárolt fájlt. Ez a nyílt forráskódú példa sablonként használható a Windows saját alkalmazásának létrehozásához a Computer Vision API és Windows megjelenítési alaprendszer (WPF) használatával, amely a .NET-keretrendszer részét képezi.

> [!div class="checklist"]
> * Minta alkalmazás beszerzése a GitHubról
> * A minta alkalmazás megnyitása és felépítése a Visual Studióban
> * A minta alkalmazás futtatása és használata különböző forgatókönyvek elvégzéséhez
> * Ismerkedjen meg a minta alkalmazásban található különféle forgatókönyvekkel

## <a name="prerequisites"></a>Előfeltételek

A minta alkalmazás megismerése előtt győződjön meg arról, hogy teljesítette a következő előfeltételeket:

* A [Visual Studio 2015](https://visualstudio.microsoft.com/downloads/) vagy újabb verzióval kell rendelkeznie.
* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" hozzon létre egy Computer Vision erőforrást, "  target="_blank"> és hozzon létre egy Computer Vision-erőforrást </a> a Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése** elemre.
    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazás Computer Vision szolgáltatáshoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.

## <a name="get-the-sample-app"></a>Mintaalkalmazás letöltése

A Computer Vision minta alkalmazás elérhető a GitHubon az `Microsoft/Cognitive-Vision-Windows` adattárból. Ez a tárház a `Microsoft/Cognitive-Common-Windows` tárházat git almodulként is tartalmazza. A tárházat rekurzív módon, a `git clone --recurse-submodules` parancssorból vagy a GitHub Desktop használatával is megadhatja, beleértve az almodult is.

Ha például a Computer Vision minta alkalmazás adattárát rekurzív módon szeretné klónozást futtatni a parancssorból, futtassa a következő parancsot:

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Vision-Windows.git
```

> [!IMPORTANT]
> Ezt a tárházat ne töltse le ZIP-fájlként. A git nem tartalmaz almodulokat a tárház ZIP-fájlként való letöltésekor.

### <a name="get-optional-sample-images"></a>Opcionális mintaképek beolvasása

Igény szerint használhatja az [arc](../../Face/Overview.md) minta alkalmazásban található minta lemezképeket is, amelyek elérhetők a githubról az `Microsoft/Cognitive-Face-Windows` adattárból. Ez a minta egy olyan mappát tartalmaz, `/Data` amely több rendszerképet is tartalmaz. Ezt a tárházat a Computer Vision minta alkalmazáshoz tartozó módszerekkel is rekurzív módon klónozott.

Ha például egy parancssorból szeretné rekurzív módon klónozott adattárat a Face minta alkalmazáshoz, futtassa a következő parancsot:

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Face-Windows.git
```

## <a name="open-and-build-the-sample-app-in-visual-studio"></a>A minta alkalmazás megnyitása és felépítése a Visual Studióban

Először a minta alkalmazást kell létrehoznia, hogy a Visual Studio fel tudja oldani a függőségeket, mielőtt futtatja vagy megtekinti a minta alkalmazást. A minta alkalmazás megnyitásához és létrehozásához hajtsa végre a következő lépéseket:

1. Nyissa meg a Visual Studio-megoldás fájlját `/Sample-WPF/VisionAPI-WPF-Samples.sln` a Visual Studióban.
1. Győződjön meg arról, hogy a Visual Studio-megoldás két projektet tartalmaz:  

   * SampleUserControlLibrary
   * VisionAPI-WPF – minták  

   Ha a SampleUserControlLibrary-projekt nem érhető el, ellenőrizze, hogy a tárházat rekurzív módon klónozotta-e `Microsoft/Cognitive-Vision-Windows` .
1. A Visual Studióban nyomja le a CTRL + SHIFT + B billentyűkombinációt, vagy válassza a **Létrehozás** elemet a menüszalag menüből, majd válassza a **megoldás létrehozása** lehetőséget a megoldás felépítéséhez.

## <a name="run-and-interact-with-the-sample-app"></a>A minta alkalmazás futtatása és használata

A minta alkalmazást futtatva megtekintheti, hogyan működik együtt Önnel és a Computer Vision ügyféloldali függvénytárával, amikor különböző feladatokat hajt végre, például miniatűröket vagy címkéző képeket hoz létre. A minta alkalmazás futtatásához és használatához hajtsa végre a következő lépéseket:

1. A Build befejezése után nyomja le az **F5** billentyűt, vagy a menüszalag menüjében válassza a **hibakeresés** lehetőséget, majd válassza a **hibakeresés elindítása** lehetőséget a minta alkalmazás futtatásához.
1. Amikor megjelenik a minta alkalmazás, válassza az **előfizetés-kulcskezelő** lehetőséget a navigációs ablaktáblán az előfizetés-kulcs kezelése lap megjelenítéséhez.
   ![Előfizetés-kulcs kezelése lap](../Images/Vision_UI_Subscription.PNG)  
1. Adja meg az előfizetési kulcsot az **előfizetési kulcsban**.
1. Adja meg a végpont URL-címét a **végpontban**.  
   [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. Ha nem szeretné megadni az előfizetési kulcsot és a végponti URL-címet a minta alkalmazás következő futtatásakor, válassza a **beállítás mentése** lehetőséget, hogy az előfizetési kulcs és a végpont URL-címét mentse a számítógépre. Ha törölni szeretné a korábban mentett előfizetési kulcsot és a végpont URL-címét, válassza a **beállítás törlése** lehetőséget.

   > [!NOTE]
   > A minta alkalmazás elkülönített tárolót használ, és az `System.IO.IsolatedStorage` előfizetési kulcs és a végpont URL-címének tárolásához.

1. A navigációs ablaktáblán válassza **a forgatókönyv lehetőséget** , válassza ki a minta alkalmazáshoz jelenleg mellékelt forgatókönyvek egyikét:  

   | Forgatókönyv | Leírás |
   |----------|-------------|
   |Rendszerkép elemzése | A [rendszerkép elemzése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) művelettel elemzi a helyi vagy a távoli rendszerképeket. Kiválaszthatja az elemzés vizuális funkcióit és nyelvét, és megtekintheti a képet és az eredményeket is.  |
   |Rendszerkép elemzése a tartományi modellel | A [tartománynév-specifikus modellek](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd) művelettel sorolja fel azokat a tartományi modelleket, amelyekről kiválaszthatja a tartományt, valamint a [tartomány-specifikus tartalom felismerése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) műveletet egy helyi vagy távoli rendszerkép elemzéséhez a kiválasztott tartományi modell használatával. Megadhatja az elemzés nyelvét is. |
   |Rendszerkép leírása | A [képleírási](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fe) művelettel létrehoz egy helyi vagy távoli rendszerkép olvasható leírását. Megadhatja a Leírás nyelvét is. |
   |Címkék készítése | A [kép címkézése](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1ff) műveletet használja a helyi vagy távoli rendszerkép vizualizációs funkcióinak címkézéséhez. Megadhatja a címkékhez használt nyelvet is. |
   |Szövegfelismerés (OCR) | Az [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) művelettel azonosítja és Kinyeri a nyomtatott szöveget egy képből. Kiválaszthatja a használni kívánt nyelvet, vagy engedélyezheti Computer Vision a nyelv automatikus észlelését. |
   |Szövegfelismerés v2 (angol) | A [szövegfelismerés](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) használja, és [lekérdezi a szövegfelismerés műveleti eredmény](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201) műveleteit a nyomtatott vagy kézzel írt szöveg aszinkron felismeréséhez és kinyeréséhez. |
   |Miniatűr beolvasása | A [miniatűr beolvasása](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) művelettel egy helyi vagy távoli rendszerkép miniatűrjét állítja elő. |

   Az alábbi képernyőfelvételen a képelemzési forgatókönyvhöz megadott oldal látható, a minta-rendszerkép elemzése után.
   ![Képernyőkép a rendszerkép elemzése lapról](../Images/Analyze_Image_Example.PNG)

## <a name="explore-the-sample-app"></a>A minta alkalmazás megismerése

A Computer Vision minta alkalmazáshoz készült Visual Studio-megoldás két projektet tartalmaz:

* SampleUserControlLibrary  
  A SampleUserControlLibrary projekt több Cognitive Services-minta által megosztott funkciókat biztosít. A projekt a következőket tartalmazza:
  * SampleScenarios  
    Egy szabványosított bemutatót biztosító UserControl vezérlő, mint például a címsor, a navigációs ablaktábla és a tartalom panel, mintákhoz. A Computer Vision minta alkalmazás ezt a vezérlőt használja a MainWindow. XAML ablakban a forgatókönyvek és az egyes forgatókönyvekben megosztott hozzáférési információk megjelenítéséhez, például az előfizetési kulcs és a végpont URL-címének megadásához.
  * SubscriptionKeyPage  
    Egy olyan oldal, amely szabványosított elrendezést biztosít a minta alkalmazás előfizetési kulcsának és végponti URL-címének megadásához. A Computer Vision minta alkalmazás ezen a lapon kezelheti a forgatókönyv lapjai által használt előfizetési kulcsot és végpont URL-címét.
  * VideoResultControl  
    UserControl vezérlő, amely szabványosított bemutatót biztosít a videóval kapcsolatos információkhoz. A Computer Vision minta alkalmazás nem használja ezt a vezérlőt.
* VisionAPI-WPF – minták  
  A Computer Vision minta alkalmazás fő projektje, ez a projekt a Computer Vision érdekes funkcióit tartalmazza. A projekt a következőket tartalmazza:
  * AnalyzeInDomainPage. XAML  
    Az elemzés rendszerképének a tartományi modellel kapcsolatos forgatókönyvhöz tartozó lapja.
  * AnalyzeImage. XAML  
    A rendszerkép elemzése forgatókönyv forgatókönyv lapja.
  * DescribePage. XAML  
    A rendszerkép leírására szolgáló forgatókönyv lapja.
  * ImageScenarioPage. cs  
    A ImageScenarioPage osztály, amelyből a rendszer a minta alkalmazás összes forgatókönyv lapját származtatja. Ez az osztály kezeli a funkciókat, például a hitelesítő adatok megadását és a kimenet formázását, amelyet az összes forgatókönyv oldalai megosztanak.
  * MainWindow.xaml  
    A minta alkalmazás főablaka a SampleScenarios vezérlőelem használatával mutatja be a SubscriptionKeyPage és a forgatókönyvek lapjait.
  * OCRPage. XAML  
    A szövegfelismerés (OCR) forgatókönyvhöz tartozó forgatókönyv lapja.
  * RecognizeLanguage. cs  
    A RecognizeLanguage osztály, amely információt nyújt a minta alkalmazás különböző módszerei által támogatott nyelvekről.
  * TagsPage. XAML  
    A címkék előállítására szolgáló forgatókönyv lapja.
  * TextRecognitionPage. XAML  
    A szövegfelismerés v2 (angol) forgatókönyvhöz tartozó forgatókönyv lapja.
  * ThumbnailPage. XAML  
    A miniatűr beolvasása forgatókönyv forgatókönyv lapja.

### <a name="explore-the-sample-code"></a>A mintakód megismerése

A mintakód kulcsfontosságú részeit olyan megjegyzési blokkokkal együtt kell kialakítani, amelyek a-val kezdődnek `KEY SAMPLE CODE STARTS HERE` , és a végére kerülnek `KEY SAMPLE CODE ENDS HERE` , hogy könnyebben felderíthető legyen a minta alkalmazás. A mintakód ezen kulcsfontosságú részei tartalmazzák a legfontosabb tudnivalókat a Computer Vision API ügyféloldali kódtár különböző feladatok elvégzéséhez való használatának megismeréséhez. A Visual Studióban megkeresheti a `KEY SAMPLE CODE STARTS HERE` Computer Vision minta alkalmazásban található kód leglényegesebb részei közötti váltást. 

Például a `UploadAndAnalyzeImageAsync` AnalyzePage. XAML elemben látható és azt tartalmazó metódus azt mutatja be, hogyan használható az ügyféloldali kódtár egy helyi rendszerkép elemzéséhez a metódus meghívásával `ComputerVisionClient.AnalyzeImageInStreamAsync` .

```csharp
private async Task<ImageAnalysis> UploadAndAnalyzeImageAsync(string imageFilePath)
{
    // -----------------------------------------------------------------------
    // KEY SAMPLE CODE STARTS HERE
    // -----------------------------------------------------------------------

    //
    // Create Cognitive Services Vision API Service client.
    //
    using (var client = new ComputerVisionClient(Credentials) { Endpoint = Endpoint })
    {
        Log("ComputerVisionClient is created");

        using (Stream imageFileStream = File.OpenRead(imageFilePath))
        {
            //
            // Analyze the image for all visual features.
            //
            Log("Calling ComputerVisionClient.AnalyzeImageInStreamAsync()...");
            VisualFeatureTypes[] visualFeatures = GetSelectedVisualFeatures();
            string language = (_language.SelectedItem as RecognizeLanguage).ShortCode;
            ImageAnalysis analysisResult = await client.AnalyzeImageInStreamAsync(imageFileStream, visualFeatures, null, language);
            return analysisResult;
        }
    }

    // -----------------------------------------------------------------------
    // KEY SAMPLE CODE ENDS HERE
    // -----------------------------------------------------------------------
}
```

### <a name="explore-the-client-library"></a>Az ügyféloldali kódtár megismerése

Ez a példa a Computer Vision API ügyféloldali kódtárat használja, amely egy vékony C#-ügyfél burkolója az Azure Cognitive Services Computer Vision API. Az ügyféloldali kódtár a [Microsoft. Azure. CognitiveServices. vízió. ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) csomagban található NuGet érhető el. A Visual Studio alkalmazás létrehozásakor a megfelelő NuGet-csomagból beolvasta az ügyféloldali kódtárat. Az ügyféloldali kódtár forráskódját a tárház mappájában is megtekintheti `/ClientLibrary` `Microsoft/Cognitive-Vision-Windows` .

Az ügyféloldali függvénytár működési központjai az `ComputerVisionClient` osztályban, a `Microsoft.Azure.CognitiveServices.Vision.ComputerVision` névtérben, míg az osztály által a Computer Vision való `ComputerVisionClient` interakció során használt modellek a `Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models` névtérben találhatók. A minta alkalmazásban található különféle XAML-forgatókönyvek oldalain a következő irányelvek találhatók meg `using` ezekhez a névterekhez:

```csharp
// -----------------------------------------------------------------------
// KEY SAMPLE CODE STARTS HERE
// Use the following namespace for ComputerVisionClient.
// -----------------------------------------------------------------------
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
// -----------------------------------------------------------------------
// KEY SAMPLE CODE ENDS HERE
// -----------------------------------------------------------------------
```

A `ComputerVisionClient` Computer Vision minta alkalmazásban található forgatókönyvek megismeréséhez tekintse meg a osztály különböző módszereit.

## <a name="explore-the-analyze-image-scenario"></a>A képelemzési forgatókönyv megismerése

Ezt a forgatókönyvet a AnalyzePage. XAML lap kezeli. Kiválaszthatja az elemzés vizuális funkcióit és nyelvét, és megtekintheti a képet és az eredményeket is. A forgatókönyv oldal ezt a következő módszerek egyikével hajtja végre a rendszerkép forrásától függően:

* UploadAndAnalyzeImageAsync  
  Ez a módszer helyi rendszerképekhez használatos, amelyekben a lemezképet a metódusnak megfelelően kell kódolni, `Stream` és a Computer Visionnak kell elküldeni `ComputerVisionClient.AnalyzeImageInStreamAsync` .
* AnalyzeUrlAsync  
  Ez a módszer távoli lemezképekhez használatos, amelyben a lemezkép URL-címét a metódus meghívásával Computer Vision elküldjük `ComputerVisionClient.AnalyzeImageAsync` .

A `UploadAndAnalyzeImageAsync` metódus új példányt hoz létre `ComputerVisionClient` a megadott előfizetési kulcs és végpont URL-cím használatával. Mivel a minta alkalmazás egy helyi rendszerképet elemez, el kell küldenie a rendszerkép tartalmát a Computer Visionba. Ekkor megnyílik a alkalmazásban az olvasáshoz megadott helyi fájl `imageFilePath` , majd beolvassa `Stream` a forgatókönyv oldalon kiválasztott vizuális funkciókat és nyelveket. Meghívja a `ComputerVisionClient.AnalyzeImageInStreamAsync` metódust, `Stream` átadja a fájlt, a vizualizáció funkcióit és a nyelvet, majd az eredményt `ImageAnalysis` példányként adja vissza. Az osztálytól örökölt metódusok a `ImageScenarioPage` forgatókönyv lapon a visszaadott eredményeket mutatják.

A `AnalyzeUrlAsync` metódus új példányt hoz létre `ComputerVisionClient` a megadott előfizetési kulcs és végpont URL-cím használatával. Beolvassa a forgatókönyv oldalon kiválasztott vizuális funkciókat és nyelveket. Meghívja a `ComputerVisionClient.AnalyzeImageInStreamAsync` metódust, átadja a kép URL-címét, a vizualizáció funkcióit és a nyelvet, majd az eredményt `ImageAnalysis` példányként adja vissza. Az osztálytól örökölt metódusok a `ImageScenarioPage` forgatókönyv lapon a visszaadott eredményeket mutatják.

## <a name="explore-the-analyze-image-with-domain-model-scenario"></a>A rendszerkép elemzése a tartományi modell forgatókönyvével

Ezt a forgatókönyvet a AnalyzeInDomainPage. XAML lap kezeli. Kiválaszthat egy tartományi modellt, például a `celebrities` vagy a `landmarks` nyelvet, és elvégezheti a rendszerkép tartomány-specifikus elemzését, és megtekintheti a képet és az eredményeket is. A forgatókönyv oldal a következő metódusokat használja a rendszerkép forrásától függően:

* GetAvailableDomainModelsAsync  
  Ez a módszer beolvassa az elérhető tartományi modellek listáját a Computer Visionból, és feltölti az `_domainModelComboBox` oldalon lévő kombinált lista vezérlőelemet a `ComputerVisionClient.ListModelsAsync` metódus használatával.
* UploadAndAnalyzeInDomainImageAsync  
  Ez a módszer helyi rendszerképekhez használatos, amelyekben a lemezképet a metódusnak megfelelően kell kódolni, `Stream` és a Computer Visionnak kell elküldeni `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync` .
* AnalyzeInDomainUrlAsync  
  Ez a módszer távoli lemezképekhez használatos, amelyben a lemezkép URL-címét a metódus meghívásával Computer Vision elküldjük `ComputerVisionClient.AnalyzeImageByDomainAsync` .

A `UploadAndAnalyzeInDomainImageAsync` metódus új példányt hoz létre `ComputerVisionClient` a megadott előfizetési kulcs és végpont URL-cím használatával. Mivel a minta alkalmazás egy helyi rendszerképet elemez, el kell küldenie a rendszerkép tartalmát a Computer Visionba. Ekkor megnyílik a alkalmazásban az `imageFilePath` olvasáshoz megadott helyi fájl `Stream` , majd lekéri a forgatókönyv lapon kiválasztott nyelvet. Meghívja a `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync` metódust, `Stream` átadja a fájlt, a tartományi modell nevét és a nyelvet, majd visszaküldi az eredményt egy `DomainModelResults` példányként. Az osztálytól örökölt metódusok a `ImageScenarioPage` forgatókönyv lapon a visszaadott eredményeket mutatják.

A `AnalyzeInDomainUrlAsync` metódus új példányt hoz létre `ComputerVisionClient` a megadott előfizetési kulcs és végpont URL-cím használatával. A forgatókönyv lapon kiválasztott nyelvet kapja meg. Meghívja a `ComputerVisionClient.AnalyzeImageByDomainAsync` metódust, átadja a kép URL-címét, a vizualizáció funkcióit és a nyelvet, majd az eredményt `DomainModelResults` példányként adja vissza. Az osztálytól örökölt metódusok a `ImageScenarioPage` forgatókönyv lapon a visszaadott eredményeket mutatják.

## <a name="explore-the-describe-image-scenario"></a>Fedezze fel a képleírási forgatókönyvet

Ezt a forgatókönyvet a DescribePage. XAML lap kezeli. Kiválaszthat egy nyelvet a rendszerkép egy ember által olvasható leírásának létrehozásához, és megtekintheti a képet és az eredményeket is. A forgatókönyv oldal a következő metódusokat használja a rendszerkép forrásától függően:

* UploadAndDescribeImageAsync  
  Ez a módszer helyi rendszerképekhez használatos, amelyekben a lemezképet a metódusnak megfelelően kell kódolni, `Stream` és a Computer Visionnak kell elküldeni `ComputerVisionClient.DescribeImageInStreamAsync` .
* DescribeUrlAsync  
  Ez a módszer távoli lemezképekhez használatos, amelyben a lemezkép URL-címét a metódus meghívásával Computer Vision elküldjük `ComputerVisionClient.DescribeImageAsync` .

A `UploadAndDescribeImageAsync` metódus új példányt hoz létre `ComputerVisionClient` a megadott előfizetési kulcs és végpont URL-cím használatával. Mivel a minta alkalmazás egy helyi rendszerképet elemez, el kell küldenie a rendszerkép tartalmát a Computer Visionba. Ekkor megnyílik a alkalmazásban az `imageFilePath` olvasáshoz megadott helyi fájl `Stream` , majd lekéri a forgatókönyv lapon kiválasztott nyelvet. Meghívja a `ComputerVisionClient.DescribeImageInStreamAsync` metódust, továbbítja a `Stream` fájlt, a jelöltek maximális számát (ebben az esetben 3) és a nyelvet, majd visszaadja az eredményt `ImageDescription` példányként. Az osztálytól örökölt metódusok a `ImageScenarioPage` forgatókönyv lapon a visszaadott eredményeket mutatják.

A `DescribeUrlAsync` metódus új példányt hoz létre `ComputerVisionClient` a megadott előfizetési kulcs és végpont URL-cím használatával. A forgatókönyv lapon kiválasztott nyelvet kapja meg. Meghívja a `ComputerVisionClient.DescribeImageAsync` metódust, átadja a képurl-címet, a jelöltek maximális számát (ebben az esetben 3) és a nyelvet, majd az eredményt `ImageDescription` példányként adja vissza. Az osztálytól örökölt metódusok a `ImageScenarioPage` forgatókönyv lapon a visszaadott eredményeket mutatják.

## <a name="explore-the-generate-tags-scenario"></a>A címkék létrehozásának forgatókönyvének megismerése

Ezt a forgatókönyvet a TagsPage. XAML lap kezeli. Megadhat egy nyelvet a képek vizuális funkcióinak címkézéséhez, és megtekintheti a képet és az eredményeket is. A forgatókönyv oldal a következő metódusokat használja a rendszerkép forrásától függően:

* UploadAndGetTagsForImageAsync  
  Ez a módszer helyi rendszerképekhez használatos, amelyekben a lemezképet a metódusnak megfelelően kell kódolni, `Stream` és a Computer Visionnak kell elküldeni `ComputerVisionClient.TagImageInStreamAsync` .
* GenerateTagsForUrlAsync  
  Ez a módszer távoli lemezképekhez használatos, amelyben a lemezkép URL-címét a metódus meghívásával Computer Vision elküldjük `ComputerVisionClient.TagImageAsync` .

A `UploadAndGetTagsForImageAsync` metódus új példányt hoz létre `ComputerVisionClient` a megadott előfizetési kulcs és végpont URL-cím használatával. Mivel a minta alkalmazás egy helyi rendszerképet elemez, el kell küldenie a rendszerkép tartalmát a Computer Visionba. Ekkor megnyílik a alkalmazásban az `imageFilePath` olvasáshoz megadott helyi fájl `Stream` , majd lekéri a forgatókönyv lapon kiválasztott nyelvet. Meghívja a `ComputerVisionClient.TagImageInStreamAsync` metódust, `Stream` átadja a fájlt és a nyelvet, majd az eredményt egy `TagResult` példányként adja vissza. Az osztálytól örökölt metódusok a `ImageScenarioPage` forgatókönyv lapon a visszaadott eredményeket mutatják.

A `GenerateTagsForUrlAsync` metódus új példányt hoz létre `ComputerVisionClient` a megadott előfizetési kulcs és végpont URL-cím használatával. A forgatókönyv lapon kiválasztott nyelvet kapja meg. Meghívja a `ComputerVisionClient.TagImageAsync` metódust, átadja a képurl-címet és a nyelvet, majd az eredményt `TagResult` példányként adja vissza. Az osztálytól örökölt metódusok a `ImageScenarioPage` forgatókönyv lapon a visszaadott eredményeket mutatják.

## <a name="explore-the-recognize-text-ocr-scenario"></a>A szövegfelismerés (OCR) forgatókönyv megismerése

Ezt a forgatókönyvet a OCRPage. XAML lap kezeli. Kiválaszthat egy nyelvet, amellyel felismerheti és kinyerheti a képekből a nyomtatott szöveget, és megtekintheti a képet és az eredményeket is. A forgatókönyv oldal a következő metódusokat használja a rendszerkép forrásától függően:

* UploadAndRecognizeImageAsync  
  Ez a módszer helyi rendszerképekhez használatos, amelyekben a lemezképet a metódusnak megfelelően kell kódolni, `Stream` és a Computer Visionnak kell elküldeni `ComputerVisionClient.RecognizePrintedTextInStreamAsync` .
* RecognizeUrlAsync  
  Ez a módszer távoli lemezképekhez használatos, amelyben a lemezkép URL-címét a metódus meghívásával Computer Vision elküldjük `ComputerVisionClient.RecognizePrintedTextAsync` .

A `UploadAndRecognizeImageAsync` metódus új példányt hoz létre `ComputerVisionClient` a megadott előfizetési kulcs és végpont URL-cím használatával. Mivel a minta alkalmazás egy helyi rendszerképet elemez, el kell küldenie a rendszerkép tartalmát a Computer Visionba. Ekkor megnyílik a alkalmazásban az `imageFilePath` olvasáshoz megadott helyi fájl `Stream` , majd lekéri a forgatókönyv lapon kiválasztott nyelvet. Meghívja a `ComputerVisionClient.RecognizePrintedTextInStreamAsync` metódust, amely azt jelzi, hogy a rendszer nem észleli a tájolást, és továbbítja a `Stream` fájlt és a nyelvet, majd visszaadja az eredményt `OcrResult` példányként. Az osztálytól örökölt metódusok a `ImageScenarioPage` forgatókönyv lapon a visszaadott eredményeket mutatják.

A `RecognizeUrlAsync` metódus új példányt hoz létre `ComputerVisionClient` a megadott előfizetési kulcs és végpont URL-cím használatával. A forgatókönyv lapon kiválasztott nyelvet kapja meg. Meghívja a `ComputerVisionClient.RecognizePrintedTextAsync` metódust, amely azt jelzi, hogy a tájolás nem észlelhető, és a rendszer átadja a képurl-címet és a nyelvet, majd az eredményt `OcrResult` példányként adja vissza. Az osztálytól örökölt metódusok a `ImageScenarioPage` forgatókönyv lapon a visszaadott eredményeket mutatják.

## <a name="explore-the-recognize-text-v2-english-scenario"></a>Ismerkedés a szövegfelismerés v2 (angol) forgatókönyvvel

Ezt a forgatókönyvet a TextRecognitionPage. XAML lap kezeli. Kiválaszthatja a felismerési módot és a nyelvet, hogy aszinkron módon felismerje és kibontsa a nyomtatott vagy kézírásos szövegeket egy képből, és a képet és az eredményeket is megtekintheti. A forgatókönyv oldal a következő metódusokat használja a rendszerkép forrásától függően:

* UploadAndRecognizeImageAsync  
  Ez a módszer helyi rendszerképekhez használatos, amelyekben a lemezképet a metódusnak kell kódolni, `Stream` és el kell juttatni a Computer Visionnak a metódus meghívásával, `RecognizeAsync` és egy paraméteres delegált átadásával a `ComputerVisionClient.RecognizeTextInStreamAsync` metódushoz.
* RecognizeUrlAsync  
  Ez a módszer távoli lemezképekhez használatos, amelyekben a lemezkép URL-címét a metódus meghívásával, `RecognizeAsync` valamint a metódushoz tartozó paraméteres delegált átadásával Computer Vision küldi a rendszer `ComputerVisionClient.RecognizeTextAsync` .
* RecognizeAsync ez a módszer kezeli a és a metódus aszinkron hívását, valamint az `UploadAndRecognizeImageAsync` `RecognizeUrlAsync` eredmények lekérdezését a metódus meghívásával `ComputerVisionClient.GetTextOperationResultAsync` .

A Computer Vision minta alkalmazásban szereplő egyéb forgatókönyvekkel ellentétben ez a forgatókönyv aszinkron módon történik, hogy az egyik metódust a folyamat elindítására hívja meg, de egy másik metódust kell meghívni az állapot vizsgálatára és a folyamat eredményének visszaadására. Ebben a forgatókönyvben a logikai folyamat némileg eltér a többi forgatókönyvtől.

A `UploadAndRecognizeImageAsync` metódus megnyitja a (z) alkalmazásban megadott helyi fájlt `imageFilePath` `Stream` , majd meghívja a `RecognizeAsync` metódust, és átadja a következőt:

* A metódus paraméteres aszinkron delegálására szolgáló lambda kifejezés `ComputerVisionClient.RecognizeTextInStreamAsync` , amely a `Stream` (z) és a (z) és a (z) paraméterként a fájlhoz és az elismeréshez használható `GetHeadersAsyncFunc` .
* Lambda kifejezés egy delegált számára a `Operation-Location` Válasz fejléc értékének beolvasásához a következőben: `GetOperationUrlFunc` .

A `RecognizeUrlAsync` metódus meghívja a `RecognizeAsync` metódust, amely a következőket adja meg:

* A metódus paraméteres aszinkron delegálására szolgáló lambda kifejezés, a `ComputerVisionClient.RecognizeTextAsync` távoli rendszerkép URL-címével és a felismerési móddal paraméterként a alkalmazásban `GetHeadersAsyncFunc` .
* Lambda kifejezés egy delegált számára a `Operation-Location` Válasz fejléc értékének beolvasásához a következőben: `GetOperationUrlFunc` .

Ha a `RecognizeAsync` metódus elkészült, a kettő `UploadAndRecognizeImageAsync` és a `RecognizeUrlAsync` metódusok az eredményt egy `TextOperationResult` példányként adják vissza. Az osztálytól örökölt metódusok a `ImageScenarioPage` forgatókönyv lapon a visszaadott eredményeket mutatják.

A `RecognizeAsync` metódus meghívja a paraméteres delegálást a (z) `ComputerVisionClient.RecognizeTextInStreamAsync` vagy az `ComputerVisionClient.RecognizeTextAsync` átadott metódushoz, `GetHeadersAsyncFunc` és megvárja a választ. A metódus ezután meghívja az átadott delegált, `GetOperationUrlFunc` hogy lekérje a `Operation-Location` Válasz fejlécének értékét a válaszból. Ez az érték a Computer Visionból átadott metódus eredményeinek lekéréséhez használt URL-cím `GetHeadersAsyncFunc` .

A `RecognizeAsync` metódus meghívja a `ComputerVisionClient.GetTextOperationResultAsync` metódust, és átadja a válasz fejlécből beolvasott URL-címet, `Operation-Location` hogy lekérje az adott metódus állapotát és eredményét `GetHeadersAsyncFunc` . Ha az állapot nem jelzi, hogy a metódus befejeződött, sikeres vagy sikertelen volt, a `RecognizeAsync` metódus háromszor többször hívja meg a `ComputerVisionClient.GetTextOperationResultAsync` hívást, és 3 másodpercet vár a hívások között. A `RecognizeAsync` metódus visszaadja az eredményeket a meghívott metódusnak.

## <a name="explore-the-get-thumbnail-scenario"></a>A miniatűr beolvasása forgatókönyv megismerése

Ezt a forgatókönyvet a ThumbnailPage. XAML lap kezeli. Megadhatja, hogy az intelligens levágást kívánja-e használni, és megadja a kívánt magasságot és szélességet, a miniatűrt a rendszerképből, és a képet és az eredményeket is megtekintheti. A forgatókönyv oldal a következő metódusokat használja a rendszerkép forrásától függően:

* UploadAndThumbnailImageAsync  
  Ez a módszer helyi rendszerképekhez használatos, amelyekben a lemezképet a metódusnak megfelelően kell kódolni, `Stream` és a Computer Visionnak kell elküldeni `ComputerVisionClient.GenerateThumbnailInStreamAsync` .
* ThumbnailUrlAsync  
  Ez a módszer távoli lemezképekhez használatos, amelyben a lemezkép URL-címét a metódus meghívásával Computer Vision elküldjük `ComputerVisionClient.GenerateThumbnailAsync` .

A `UploadAndThumbnailImageAsync` metódus új példányt hoz létre `ComputerVisionClient` a megadott előfizetési kulcs és végpont URL-cím használatával. Mivel a minta alkalmazás egy helyi rendszerképet elemez, el kell küldenie a rendszerkép tartalmát a Computer Visionba. Ekkor megnyílik a alkalmazásban az `imageFilePath` olvasáshoz megadott helyi fájl `Stream` . Meghívja a `ComputerVisionClient.GenerateThumbnailInStreamAsync` metódust, átadja a szélességet, a magasságot, a `Stream` fájlt, valamint azt, hogy az intelligens levágást használja-e, majd visszaadja az eredményt a következőként: `Stream` . Az osztálytól örökölt metódusok a `ImageScenarioPage` forgatókönyv lapon a visszaadott eredményeket mutatják.

A `RecognizeUrlAsync` metódus új példányt hoz létre `ComputerVisionClient` a megadott előfizetési kulcs és végpont URL-cím használatával. Meghívja a `ComputerVisionClient.GenerateThumbnailAsync` metódust, átadja a szélességet, a magasságot, a rendszerkép URL-címét, valamint azt, hogy az intelligens levágást használja-e, majd az eredményt a értékre állítja vissza `Stream` . Az osztálytól örökölt metódusok a `ImageScenarioPage` forgatókönyv lapon a visszaadott eredményeket mutatják.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje azt a mappát, amelybe a `Microsoft/Cognitive-Vision-Windows` tárházat klónozotta. Ha a minta lemezképek használatát választotta, törölje azt a mappát is, amelybe a `Microsoft/Cognitive-Face-Windows` tárházat klónozotta.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ismerkedés a Face Service szolgáltatással](../../Face/Tutorials/FaceAPIinCSharpTutorial.md)
