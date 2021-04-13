---
title: 'Rövid útmutató: Optikai karakterfelismerési ügyféloldali kódtár a .NET-hez'
description: Ebben a rövid útmutatóban a .NET-hez való optikai karakterfelismerés ügyféloldali kódtárával fog kezdeni.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 538b3ce5a268464b9f014dd00b924875824cab3b
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107327418"
---
<a name="HOLTop"></a>

Az OCR ügyféloldali kódtára segítségével nyomtatott és kézzel írt szöveget olvashat be egy képből.

[Referenciadokumentáció](/dotnet/api/overview/azure/cognitiveservices/client/computervision)  |  [Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ComputerVision)  |  [Csomag (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/)  |  [Minták](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [Hozzon létre egyet ingyenesen](https://azure.microsoft.com/free/cognitive-services/)
* A [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) vagy a [.NET Core aktuális verziója.](https://dotnet.microsoft.com/download/dotnet-core)
* Az Azure-előfizetés létrehozása után hozzon létre egy Computer Vision-erőforrást, Computer Vision erőforrást a Azure Portal a kulcs és a végpont <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" "  target="_blank"> </a> lekért létrehozásához. Az üzembe helyezés után kattintson az **Erőforráshoz való ugrás gombra.**
    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontjára az alkalmazás a Computer Vision csatlakoztatásához. A kulcsot és a végpontot a rövid útmutató későbbi, alábbi kódába fogja beilleszteni.
    * Az ingyenes tarifacsomag ( ) használatával kipróbálhatja a szolgáltatást, és később frissíthet fizetős szolgáltatási szintre éles `F0` környezetben.

## <a name="setting-up"></a>Beállítása

### <a name="create-a-new-c-application"></a>Új C#-alkalmazás létrehozása

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

A Visual Studio hozzon létre egy új .NET Core-alkalmazást. 

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése 

Miután létrehozott egy új projektet, telepítse az ügyféloldali kódtárat. Kattintson a jobb gombbal a projektmegoldásra a Megoldáskezelő majd válassza a **Manage NuGet Packages (NuGet-csomagok** **kezelése)** gombra. A megnyíló csomagkezelőben válassza a **Tallózás** lehetőséget, jelölje be **az Include prerelease**(Előzetes) jelölőnégyzetet, és keressen a következőre: `Microsoft.Azure.CognitiveServices.Vision.ComputerVision` . Válassza ki a `7.0.0` verziót, majd telepítse a **alkalmazást.** 

#### <a name="cli"></a>[Parancssori felület](#tab/cli)

Egy konzolablakban (például cmd, PowerShell vagy Bash) az paranccsal hozzon létre egy új konzolalkalmazást `dotnet new` `computer-vision-quickstart` néven. Ez a parancs egy egyszerű "Hello World" C#-projektet hoz létre egyetlen forrásfájllal: *Program.cs*.

```console
dotnet new console -n computer-vision-quickstart
```

Módosítsa a könyvtárat az újonnan létrehozott alkalmazásmappába. Az alkalmazást a következővel építheti fel:

```console
dotnet build
```

A build kimenete nem tartalmazhat figyelmeztetéseket vagy hibákat. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése

Telepítse a .NET-hez Computer Vision ügyféloldali kódtárat az alkalmazáskönyvtárban a következő paranccsal:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 7.0.0
```

---

> [!TIP]
> Szeretné egyszerre megtekinteni a teljes gyorsindítási kódfájlt? Ez a [GitHubon található,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs)amely a rövid útmutatóban található példakódokat tartalmazza.

A projektkönyvtárból nyissa meg a *Program.cs* fájlt a kívánt szerkesztőben vagy IDE-ban.

### <a name="find-the-subscription-key-and-endpoint"></a>Az előfizetői kulcs és a végpont megkeresve

Nyissa meg az Azure Portalt. Ha Computer Vision az Előfeltételek szakaszban  létrehozott erőforrás, kattintson az **Erőforráshoz** ugrás gombra a **Következő lépések területen.** Az előfizetési kulcsot és a végpontot  az erőforrás kulcs- és végpontoldalán, az **erőforrás-kezelés alatt találja.** 

Az alkalmazás **Program** osztályában hozzon létre változókat a Computer Vision előfizetői előfizetési kulcshoz és végponthoz. Illessze be az előfizetői kulcsot és a végpontot az alábbi kódba, ahol a jel látható. A Computer Vision végpont a következő formában van: `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/` .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_using_and_vars)]

> [!IMPORTANT]
> Ne felejtse el eltávolítani az előfizetői kulcsot a kódból, amikor végzett, és soha ne tegye közzé nyilvánosan. Éles környezetben érdemes lehet biztonságos módon tárolni és elérni a hitelesítő adatokat. Például: [Azure Key Vault.](../../../../key-vault/general/overview.md)

Az alkalmazás metódusában adja hozzá az ebben a rövid útmutatóban használt metódusok `Main` hívásait. Ezeket később fogja létrehozni.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_client)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_extracttextinmain)]

> [!div class="nextstepaction"]
> [Beállítottam az ügyfelet,](?success=set-up-client#object-model) [amelynél problémáttam](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=set-up-client)

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek az OCR .NET SDK néhány fő funkcióját kezelik.

|Név|Leírás|
|---|---|
| [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) | Ez az osztály minden funkcióhoz Computer Vision szükséges. Példányosodhat az előfizetés adataival, és a legtöbb képművelethez használhatja.|
|[ComputerVisionClientExtensions](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions)| Ez az osztály további metódusokat tartalmaz a **ComputerVisionClient számára.**|

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek a következő feladatok elvégzését mutatják be a .NET OCR-ügyfélkódtárával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Nyomtatott és kézzel írt szöveg olvasása](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

A **Program** osztály egy új metódusában példányosíthat egy ügyfelet a végponttal és az előfizetői kulccsal. Hozzon létre **[egy ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.apikeyserviceclientcredentials)** objektumot az előfizetői kulccsal, és használja a végponttal egy **[ComputerVisionClient objektum létrehozásához.](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient)**

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_auth)]

> [!div class="nextstepaction"]
> [Hitelesítettem az ügyfelet,](?success=authenticate-client#read-printed-and-handwritten-text) [amely problémát jelent](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=authenticate-client)

## <a name="read-printed-and-handwritten-text"></a>Nyomtatott és kézzel írt szöveg olvasása

Az OCR szolgáltatás képes olvasni a képeken látható szövegeket, és konvertálni karakterfolyamokká. A szövegfelismeréssel kapcsolatos további információkért lásd az optikai karakterfelismerés [(OCR) áttekintését.](../../overview-ocr.md) Az ebben a szakaszban található kód a [Read 3.0 legújabb Computer Vision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) SDK-kiadását használja, és meghatároz egy metódust, amely az ügyfélobjektummal észleli és kinyeri a képeken található `BatchReadFileUrl` szöveget.

> [!TIP]
> Helyi képből is kinyerhet szöveget. Lásd a [ComputerVisionClient metódusokat,](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) például a **ReadInStreamAsync metódust.** Vagy tekintse meg a [GitHubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs) a helyi rendszerképekkel kapcsolatos forgatókönyveket tartalmazó mintakódot.

### <a name="set-up-test-image"></a>Tesztkép beállítása

A **Program osztályban** mentsen egy hivatkozást annak a képnek az URL-címére, amelyből szöveget szeretne kinyerni. Ez a kódrészlet a nyomtatott és a kézzel írt szöveghez is tartalmaz mintaképeket.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readtext_url)]

### <a name="call-the-read-api"></a>A Read API hívása

Definiálja az új metódust a szövegolvasáshoz. Adja hozzá az alábbi kódot, amely a **ReadAsync metódust hívja** meg az adott rendszerképhez. Ez egy műveletazonosítót ad vissza, és elindít egy aszinkron folyamatot a kép tartalmának beolvassa.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readfileurl_1)]

### <a name="get-read-results"></a>Olvasási eredmények lekérte

Ezután szerezze be a **ReadAsync** hívásból visszaadott műveletazonosítót, és a használatával lekérdezheti a szolgáltatásból a műveleti eredményeket. A következő kód ellenőrzi a műveletet, amíg az eredmények vissza nem térnek. Ezután kinyomtatja a kinyert szöveges adatokat a konzolon.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readfileurl_2)]

### <a name="display-read-results"></a>Olvasási eredmények megjelenítése

Adja hozzá a következő kódot a lekért szöveges adatok kinyeréhez és megjelenítéséhez, majd fejezze be a metódus definícióját.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readfileurl_3)]

## <a name="run-the-application"></a>Az alkalmazás futtatása

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Futtassa az alkalmazást  az IDE ablakának tetején található Hibakeresés gombra kattintva.

#### <a name="cli"></a>[Parancssori felület](#tab/cli)

Futtassa az alkalmazást az alkalmazás könyvtárában az `dotnet run` paranccsal.

```dotnet
dotnet run
```

---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha törölni vagy eltávolítani szeretne egy Cognitive Services előfizetést, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított összes többi erőforrást is törli.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan telepítheti az OCR ügyféloldali kódtárat, és hogyan használhatja a Read API-t. A következő lépés a Read API-funkciókkal kapcsolatos további tudnivalók.

> [!div class="nextstepaction"]
>[A Read API hívása](../../Vision-API-How-to-Topics/call-read-api.md)

* [Az OCR áttekintése](../../overview-ocr.md)
* A minta forráskódja a [GitHubon található.](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs)
