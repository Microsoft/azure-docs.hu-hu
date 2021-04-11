---
title: 'Gyors útmutató: optikai karakterfelismerési ügyféloldali kódtár a .NET-hez'
description: Ebben a rövid útmutatóban megismerheti a .NET-hez készült optikai karakterfelismerési ügyféloldali kódtárat.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 410ced99b1c5053c084921edf4d9bbde1a9443c4
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073690"
---
<a name="HOLTop"></a>

Az OCR-ügyfél függvénytárával olvashatók be a nyomtatott és a kézírásos szöveg egy képből.

[Dokumentáció](/dotnet/api/overview/azure/cognitiveservices/client/computervision)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ComputerVision)  |  [Csomag (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/)  |  [Példák](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* A [Visual Studio ide](https://visualstudio.microsoft.com/vs/) vagy a [.net Core](https://dotnet.microsoft.com/download/dotnet-core)aktuális verziója.
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" hozzon létre egy Computer Vision erőforrást, "  target="_blank"> és hozzon létre egy Computer Vision-erőforrást </a> a Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése** elemre.
    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazás Computer Vision szolgáltatáshoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-c-application"></a>Új C#-alkalmazás létrehozása

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Hozzon létre egy új .NET Core-alkalmazást a Visual Studióval. 

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése 

Miután létrehozott egy új projektet, telepítse az ügyféloldali kódtárat úgy, hogy a **megoldáskezelő** , majd a **NuGet-csomagok kezelése** lehetőségre kattint a jobb gombbal a projekt megoldásra. A megnyíló csomagkezelő válassza a **Tallózás** lehetőséget, jelölje be az **előzetes verzió** használata jelölőnégyzetet, és keressen rá `Microsoft.Azure.CognitiveServices.Vision.ComputerVision` . Válassza `6.0.0-preview.1` a verzió, majd a **telepítés** lehetőséget. 

#### <a name="cli"></a>[Parancssori felület](#tab/cli)

A konzol ablakban (például cmd, PowerShell vagy bash) az `dotnet new` paranccsal hozzon létre egy új, a nevű Console-alkalmazást `computer-vision-quickstart` . Ez a parancs egy egyszerű "Hello World" C#-projektet hoz létre egyetlen forrásfájl: *program. cs*.

```console
dotnet new console -n computer-vision-quickstart
```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazás mappájába. Az alkalmazást az alábbiakkal hozhatja létre:

```console
dotnet build
```

A Build kimenete nem tartalmazhat figyelmeztetést vagy hibát. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Az alkalmazás könyvtárában telepítse az Computer Vision .NET-hez készült ügyféloldali kódtárat a következő paranccsal:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 6.0.0
```

---

> [!TIP]
> Egyszerre szeretné megtekinteni a teljes rövid útmutató kódját? Megtalálhatja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs), amely a jelen rövid útmutatóban szereplő példákat tartalmazza.

A projekt könyvtárában nyissa meg a *program. cs* fájlt az előnyben részesített szerkesztőben vagy az ide-ben.

### <a name="find-the-subscription-key-and-endpoint"></a>Az előfizetés kulcsának és végpontjának megkeresése

Nyissa meg az Azure Portalt. Ha az **Előfeltételek** szakaszban létrehozott Computer Vision erőforrás sikeresen telepítve van, kattintson az **Ugrás erőforrásra** gombra a **következő lépések** alatt. Az előfizetési kulcsot és a végpontot az erőforrás- **kezelés** lehetőség alatt találja az erőforrás **kulcs és végpont** lapján. 

Az alkalmazás **program** osztályában hozzon létre változókat a Computer Vision előfizetési kulcshoz és végponthoz. Illessze be az előfizetési kulcsot és a végpontot a következő kódra, ahol az szerepel. Az Computer Vision-végpont rendelkezik az űrlappal `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/` .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_using_and_vars)]

> [!IMPORTANT]
> Ne feledje, hogy ha elkészült, távolítsa el az előfizetési kulcsot a kódból, és soha ne tegye közzé nyilvánosan. Éles környezetben érdemes lehet biztonságos módszert használni a hitelesítő adatok tárolásához és eléréséhez. Például az [Azure Key Vault](../../../../key-vault/general/overview.md).

Az alkalmazás `Main` metódusában adjon hozzá hívásokat az ebben a rövid útmutatóban használt módszerekhez. Ezeket később fogja létrehozni.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_client)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_extracttextinmain)]

> [!div class="nextstepaction"]
> [Megállítottam az ügyfelet](?success=set-up-client#object-model) , hogy [egy hibába ütközött](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=set-up-client)

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek az OCR .NET SDK főbb funkcióit kezelik.

|Név|Leírás|
|---|---|
| [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) | Ez az osztály minden Computer Vision funkcióhoz szükséges. Ezt az előfizetési adatokkal hozza létre, és a legtöbb képművelet végrehajtásához használja.|
|[ComputerVisionClientExtensions](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions)| Ez az osztály további metódusokat tartalmaz a **ComputerVisionClient**.|

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat a .NET-hez készült OCR ügyféloldali kódtár használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Nyomtatott és kézzel írt szöveg olvasása](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

A **program** osztály új metódusában hozza létre az ügyfelet a végponttal és az előfizetési kulccsal. Hozzon létre egy **[ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.apikeyserviceclientcredentials)** objektumot az előfizetési kulccsal, és használja azt a végponttal egy **[ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient)** objektum létrehozásához.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_auth)]

> [!div class="nextstepaction"]
> [Hitelesítettem a-ügyfelet](?success=authenticate-client#read-printed-and-handwritten-text) [, hogy egy hibába ütközött](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=authenticate-client)

## <a name="read-printed-and-handwritten-text"></a>Nyomtatott és kézzel írt szöveg olvasása

Az OCR szolgáltatás megjelenítheti a képen látható szöveget, és átalakíthatja a karakteres adatfolyamba. A szöveges felismeréssel kapcsolatos további információkért tekintse meg az [optikai karakterfelismerés (OCR)](../../overview-ocr.md) áttekintését. Az ebben a szakaszban található kód az 3,0-es [olvasáshoz a legújabb Computer Vision SDK-kiadást](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/6.0.0-preview.1) használja, és definiál egy metódust, `BatchReadFileUrl` amely az ügyfélalkalmazás segítségével azonosítja és Kinyeri a szöveget a lemezképben.

> [!TIP]
> Helyi rendszerképből is kinyerheti a szöveget. Tekintse meg a [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) metódusokat, például a **ReadInStreamAsync**. Vagy a helyi rendszerképeket érintő forgatókönyvek esetében tekintse meg a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs) található mintakód-kódot.

### <a name="set-up-test-image"></a>Tesztelési rendszerkép beállítása

A **program** osztályban mentsen egy hivatkozást azon rendszerkép URL-címére, amelyből szöveget szeretne kinyerni. Ez a kódrészlet a nyomtatott és a kézírásos szöveghez is tartalmaz mintaképeket.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readtext_url)]

### <a name="call-the-read-api"></a>Az olvasási API meghívása

Adja meg az új metódust a szöveg olvasásához. Adja hozzá az alábbi kódot, amely meghívja a **ReadAsync** metódust az adott képhez. Ez egy műveleti azonosítót ad vissza, és elindít egy aszinkron folyamatot a rendszerkép tartalmának olvasásához.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readfileurl_1)]

### <a name="get-read-results"></a>Olvasási eredmények beolvasása

Ezután kérje le a **ReadAsync** -hívás által visszaadott művelet azonosítóját, és használja a szolgáltatás lekérdezésére a művelet eredményeihez. A következő kód ellenőrzi a műveletet, amíg vissza nem adja az eredményeket. Ezután kinyomtatja a kinyert szöveges adatát a konzolon.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readfileurl_2)]

### <a name="display-read-results"></a>Olvasási eredmények megjelenítése

Adja hozzá a következő kódot a beolvasott szöveges információk elemzéséhez és megjelenítéséhez, majd fejezze be a metódus definícióját.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readfileurl_3)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Futtassa az alkalmazást az IDE-ablak felső részén található **Debug (hibakeresés** ) gombra kattintva.

#### <a name="cli"></a>[Parancssori felület](#tab/cli)

Futtassa az alkalmazást az alkalmazás könyvtárából a `dotnet run` paranccsal.

```dotnet
dotnet run
```

---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan telepítheti az OCR-ügyféloldali kódtárat, és hogyan használhatja az olvasási API-t. Következő lépésként tekintse meg az API-funkciók beolvasása című témakört.

> [!div class="nextstepaction"]
>[Az olvasási API meghívása](../../Vision-API-How-to-Topics/call-read-api.md)

* [OCR – áttekintés](../../overview-ocr.md)
* A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs)található.
