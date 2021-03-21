---
title: 'Oktatóanyag: adatok észlelése és megjelenítése egy rendszerképben a .NET SDK használatával'
titleSuffix: Azure Cognitive Services
description: Ebben az oktatóanyagban létre fog hozni egy Windows-alkalmazást, amely a Face szolgáltatást használja az arcok észlelésére és a képek keretének megjelenítésére.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 7a9caf1c1785055cbc81ef56958fe8ce2aca229c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102428303"
---
# <a name="tutorial-create-a-windows-presentation-framework-wpf-app-to-display-face-data-in-an-image"></a>Oktatóanyag: Windows Presentation Framework (WPF) alkalmazás létrehozása egy Rendszerképbeli Arcfelismerés megjelenítéséhez

Ebből az oktatóanyagból megtudhatja, hogyan használhatja az Azure Face szolgáltatást a .NET Client SDK-n keresztül, hogy felderítse a képekben lévő arcokat, majd a felhasználói felületen megjelenő információkat. Létre fog hozni egy olyan WPF-alkalmazást, amely észleli az arcokat, egy keretet rajzol az egyes arcok körül, és megjeleníti az állapotsorban található arc leírását. 

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> - WPF-alkalmazás létrehozása
> - Az arc ügyféloldali kódtár telepítése
> - Az ügyfélkódtár használata a képeken lévő arcok észleléséhez
> - Keret rajzolása minden észlelt arc köré
> - Az állapotsorban található kijelölt arc leírásának megjelenítése

![Képernyőfelvétel téglalappal bekeretezett arcokról](../Images/getting-started-cs-detected.png)

A teljes mintakód elérhető a GitHubon a [kognitív Face csharp minta](https://github.com/Azure-Samples/Cognitive-Face-CSharp-sample) adattárában.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/cognitive-services/). 


## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" hozzon létre egy Face-erőforrást "  target="_blank"> </a> a Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése** elemre.
    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazás Face APIhoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.
* [Hozzon létre környezeti változókat](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) a kulcs és a szolgáltatás végponti karakterláncához, illetve a nevet `FACE_SUBSCRIPTION_KEY` `FACE_ENDPOINT` .
- A [Visual Studio](https://www.visualstudio.com/downloads/)bármely kiadása.

## <a name="create-the-visual-studio-project"></a>A Visual Studio-projekt létrehozása

Az alábbi lépéseket követve hozzon létre egy új WPF-alkalmazás projektjét.

1. A Visual Studióban nyissa meg az új projekt párbeszédpanelt. Bontsa ki a **telepített**, majd a **Visual C#**, majd a **WPF-alkalmazás (.NET-keretrendszer)** elemet.
1. Adja a **FaceTutorial** nevet az alkalmazásnak, majd kattintson az **OK** gombra.
1. Szerezze be a szükséges NuGet-csomagokat. Kattintson a jobb gombbal a projektre a Megoldáskezelő, majd válassza a **NuGet-csomagok kezelése** lehetőséget. Ezután keresse meg és telepítse a következő csomagot:
    - [Microsoft. Azure. CognitiveServices. vízió. Face 2.6.0 – előzetes verzió. 1](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.6.0-preview.1)

## <a name="add-the-initial-code"></a>Kezdeti kód hozzáadása

Ebben a szakaszban az alkalmazás alapszintű keretrendszerét fogja hozzáadni az arca-specifikus szolgáltatásai nélkül.

### <a name="create-the-ui"></a>A felhasználói felület létrehozása

Nyissa meg a *MainWindow. XAML* mappát, és cserélje le a tartalmát a következő kódra &mdash; . Ez a kód a felhasználói felület ablakát hozza létre. A `FacePhoto_MouseMove` és `BrowseButton_Click` metódusok olyan eseménykezelők, amelyeket később kell meghatározni.

[!code-xaml[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml?name=snippet_xaml)]

### <a name="create-the-main-class"></a>A Main osztály létrehozása

Nyissa meg a *MainWindow. XAML. cs* -t, és adja hozzá az ügyféloldali kódtár névtereit, valamint az egyéb szükséges névtereket. 

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_using)]

Ezután szúrja be a következő kódot a **MainWindow** osztályban. Ez a kód egy **FaceClient** -példányt hoz létre az előfizetési kulcs és a végpont használatával.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mainwindow_fields)]

Ezután adja hozzá a **MainWindow** konstruktort. Ellenőrzi a végpont URL-karakterláncát, majd hozzárendeli azt az ügyfél-objektumhoz.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mainwindow_constructor)]

Végül adja hozzá a **BrowseButton_Click** és a **FacePhoto_MouseMove** metódusokat a osztályhoz. Ezek a módszerek megfelelnek az *MainWindow. XAML* által deklarált eseménykezelőknek. A **BrowseButton_Click** metódus létrehoz egy **OpenFileDialog**, amely lehetővé teszi a felhasználó számára egy. jpg-rendszerkép kiválasztását. Ezután megjeleníti a rendszerképet a főablakban. A további kódokat be kell szúrni **BrowseButton_Click** és **FacePhoto_MouseMove** a későbbi lépésekben. Jegyezze fel a `faceList` &mdash; **DetectedFace** -objektumok listáját is. Ez a hivatkozás azt adja meg, hogy az alkalmazás hogyan fogja tárolni és meghívni a tényleges adatokat.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_start)]

<!-- [!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_end)] -->

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_start)]

<!-- [!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_end)] -->

### <a name="try-the-app"></a>Próbálja ki az alkalmazást

Nyomja meg a **Start** (Indítás) gombot a menüben az alkalmazás teszteléséhez. Amikor megnyílik az alkalmazás ablaka, kattintson a bal alsó sarokban található **Tallózás** gombra. Meg kell jelennie egy **fájl megnyitása** párbeszédpanelnek. Válasszon ki egy rendszerképet a fájlrendszerből, és ellenőrizze, hogy az megjelenik-e az ablakban. Ezután zárjuk be az alkalmazást, és folytassa a következő lépéssel.

![Képernyőfelvétel arcokat ábrázoló képről, amely nem lett módosítva](../Images/getting-started-cs-ui.png)

## <a name="upload-image-and-detect-faces"></a>Rendszerkép feltöltése és arcok észlelése

Az alkalmazás az **FaceClient. Face. DetectWithStreamAsync** metódus meghívásával fogja felderíteni az arcokat, amely a helyi rendszerképek feltöltésének [észlelését](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) REST API.

Szúrja be a következő metódust a **MainWindow** osztályban a **FacePhoto_MouseMove** metódus alá. Ez a metódus a beolvasott képfájl egy **adatfolyamba** való beolvasására és beolvasására szolgáló arc-attribútumok listáját határozza meg. Ezután mindkét objektumot átadja a **DetectWithStreamAsync** metódus hívásának.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_uploaddetect)]

## <a name="draw-rectangles-around-faces"></a>Téglalapok rajzolása az arcok köré

Ezután adja hozzá a kódot a képen látható összes észlelt arc körüli téglalap rajzolásához. A **MainWindow** osztályban szúrja be a következő kódot a **BrowseButton_Click** metódus végén a `FacePhoto.Source = bitmapSource` sor után. Ez a kód feltölti az észlelt arcok listáját a **UploadAndDetectFaces**-hívásból. Ezután egy téglalapot rajzol az egyes arcok körül, és megjeleníti a módosított képet a főablakban.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_mid)]

## <a name="describe-the-faces"></a>Az arcok leírása

Adja hozzá a következő metódust a **MainWindow** osztályhoz a **UploadAndDetectFaces** metódus alatt. Ez a metódus a beolvasott arc attribútumokat egy olyan karakterlánccá alakítja, amely leírja az arcot.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_facedesc)]

## <a name="display-the-face-description"></a>Az arc leírásának megjelenítése

Adja hozzá a következő kódot a **FacePhoto_MouseMove** metódushoz. Ez az eseménykezelő a Face Description karakterláncot jeleníti meg, `faceDescriptionStatusBar` Amikor a kurzor egy észlelt arc négyszög fölé mutat.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_mid)]

## <a name="run-the-app"></a>Az alkalmazás futtatása

Futtassa az alkalmazást, és keressen egy képet, amelyen egy arc látható. Várjon néhány másodpercet, amíg az arcfelismerési szolgáltatás válaszol. A képen egy piros négyszögnek kell megjelennie. Ha az egérmutatót egy arc négyszög fölé viszi, az adott arc leírásának meg kell jelennie az állapotsorban.

![Képernyőfelvétel téglalappal bekeretezett arcokról](../Images/getting-started-cs-detected.png)


## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta a Face Service .NET SDK használatának alapszintű folyamatát, és létrehozott egy alkalmazást az arcok észleléséhez és a képek keretének megjelenítéséhez. Következő lépésként tekintse meg a Arcfelismerés részletes adatait.

> [!div class="nextstepaction"]
> [Arcok észlelése egy képen](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)