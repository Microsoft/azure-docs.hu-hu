---
title: 'Gyors útmutató: optikai karakterfelismerési ügyféloldali kódtár a Go-hoz'
titleSuffix: Azure Cognitive Services
description: Ismerkedjen meg az optikai karakterfelismerési ügyféloldali függvénytárral a következő lépésekkel:.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.openlocfilehash: 0ae059054c68da662e05342525987f6925184906
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073633"
---
<a name="HOLTop"></a>

Az OCR ügyféloldali kódtár használatával a nyomtatott és a kézírásos szöveg olvasható a képekből.

[Dokumentáció](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision)  |  [Csomag](https://github.com/Azure/azure-sdk-for-go)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* A [Go](https://golang.org/dl/) legújabb verziója
* Ha már rendelkezik Azure-előfizetéssel, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" hozzon létre egy Computer Vision erőforrást, "  target="_blank"> és hozzon létre egy Computer Vision-erőforrást </a> a Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése** elemre.
    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazás Computer Vision szolgáltatáshoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-go-project-directory"></a>Go-projekt könyvtárának létrehozása

A konzol ablakban (cmd, PowerShell, Terminal, bash) hozzon létre egy új munkaterületet a go-projekt számára, `my-app` és keresse meg a nevet.

```
mkdir -p my-app/{src, bin, pkg}  
cd my-app
```

A munkaterület három mappát fog tartalmazni:

* **src** – ez a könyvtár a forráskódot és a csomagokat fogja tartalmazni. A paranccsal telepített csomagok `go get` ebben a könyvtárban lesznek.
* **pkg** – ez a könyvtár tartalmazni fogja a lefordított go Package objektumokat. Ezek a fájlok mindegyike rendelkezik `.a` bővítménnyel.
* **bin** – ez a könyvtár fogja tartalmazni a futtatásakor létrehozott bináris végrehajtható fájlokat `go install` .

> [!TIP]
> Ha többet szeretne megtudni a go-munkaterület struktúrájáról, tekintse meg a [Go Language dokumentációját](https://golang.org/doc/code.html#Workspaces). Ez az útmutató a és a beállításával kapcsolatos információkat tartalmaz `$GOPATH` `$GOROOT` .

### <a name="install-the-client-library-for-go"></a>Az ügyféloldali kódtár telepítése a Go-hoz

Ezután telepítse az ügyféloldali kódtárat a Go-hoz:

```bash
go get -u https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

vagy ha a DEP-t használja a tárházon belül, futtassa a következőket:

```bash
dep ensure -add https://github.com/Azure/azure-sdk-for-go/tree/master/services/cognitiveservices/v2.1/computervision
```

### <a name="create-a-go-application"></a>Go-alkalmazás létrehozása 

Következő lépésként hozzon létre egy fájlt a (z) nevű **src** könyvtárban `sample-app.go` :

```bash
cd src
touch sample-app.go
```

> [!TIP]
> Egyszerre szeretné megtekinteni a teljes rövid útmutató kódját? Megtalálhatja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go), amely a jelen rövid útmutatóban szereplő példákat tartalmazza.

Nyissa meg az `sample-app.go` előnyben részesített ide-vagy szövegszerkesztőben.

Deklaráljon egy környezetet a szkript gyökerében. Erre az objektumra szüksége lesz a legtöbb Computer Vision függvény hívásának végrehajtásához.

### <a name="find-the-subscription-key-and-endpoint"></a>Az előfizetés kulcsának és végpontjának megkeresése

Nyissa meg az Azure Portalt. Ha az **Előfeltételek** szakaszban létrehozott Computer Vision erőforrás sikeresen telepítve van, kattintson az **Ugrás erőforrásra** gombra a **következő lépések** alatt. Az előfizetési kulcsot és a végpontot az erőforrás- **kezelés** lehetőség alatt találja az erőforrás **kulcs és végpont** lapján. 

Hozzon létre változókat a Computer Vision előfizetési kulcs és végpont számára. Illessze be az előfizetési kulcsot és a végpontot a következő kódra, ahol az szerepel. Az Computer Vision-végpont rendelkezik az űrlappal `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/` .

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_imports_and_vars)]

> [!IMPORTANT]
> Ne feledje, hogy ha elkészült, távolítsa el az előfizetési kulcsot a kódból, és soha ne tegye közzé nyilvánosan. Éles környezetben érdemes lehet biztonságos módszert használni a hitelesítő adatok tárolásához és eléréséhez. Például az [Azure Key Vault](../../../../key-vault/general/overview.md).

Ezután megkezdheti a kód hozzáadását a különböző OCR-műveletek végrehajtásához.

> [!div class="nextstepaction"]
> [Megállítottam az ügyfelet](?success=set-up-client#object-model) , hogy [egy hibába ütközött](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=set-up-client)

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek az OCR go SDK főbb funkcióit kezelik.

|Név|Leírás|
|---|---|
| [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) | Ez az osztály az összes Computer Vision funkcióhoz szükséges, például a képek elemzéséhez és a szöveges olvasáshoz. Ezt az előfizetési adatokkal hozza létre, és a legtöbb képművelet végrehajtásához használja.|
|[ReadOperationResult](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#ReadOperationResult)| Ez a típus a Batch olvasási műveletének eredményét tartalmazza. |

## <a name="code-examples"></a>Kódpéldák

Ezek a kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat az OCR ügyféloldali kódtár for go használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Nyomtatott és kézzel írt szöveg olvasása](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

> [!NOTE]
> Ez a lépés azt feltételezi, hogy [létrehozott egy környezeti változót](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) a Computer Vision kulcshoz és a végponthoz, a nevet `COMPUTER_VISION_SUBSCRIPTION_KEY` és a-t `COMPUTER_VISION_ENDPOINT` .

Hozzon létre egy `main` függvényt, és adja hozzá a következő kódot egy ügyfél létrehozásához a végponttal és a kulccsal.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_client)]

> [!div class="nextstepaction"]
> [Hitelesítettem a-ügyfelet](?success=authenticate-client#read-printed-and-handwritten-text) [, hogy egy hibába ütközött](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=authenticate-client)



## <a name="read-printed-and-handwritten-text"></a>Nyomtatott és kézzel írt szöveg olvasása

Az OCR szolgáltatás megjelenítheti a képen látható szöveget, és átalakíthatja a karakteres adatfolyamba. Az ebben a szakaszban szereplő kód egy olyan függvényt határoz meg, `RecognizeTextReadAPIRemoteImage` amely az ügyfél objektumával azonosítja és Kinyeri a nyomtatott vagy kézírásos szöveget a képen.

Adja hozzá a minta rendszerkép-referenciát és a függvény hívását a `main` függvényhez.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_readinmain)]

> [!TIP]
> Helyi rendszerképből is kinyerheti a szöveget. Tekintse meg a [BaseClient](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/cognitiveservices/v2.1/computervision#BaseClient) metódusokat, például a **BatchReadFileInStream**. Vagy a helyi rendszerképeket érintő forgatókönyvek esetében tekintse meg a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go) található mintakód-kódot.

### <a name="call-the-read-api"></a>Az olvasási API meghívása

Adja meg az új függvényt a szöveg olvasásához `RecognizeTextReadAPIRemoteImage` . Adja hozzá az alábbi kódot, amely meghívja a **BatchReadFile** metódust az adott képhez. Ez a metódus egy műveleti azonosítót ad vissza, és egy aszinkron folyamatot indít el a rendszerkép tartalmának olvasásához.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_call)]

### <a name="get-read-results"></a>Olvasási eredmények beolvasása

Ezután kérje le a **BatchReadFile** -hívás által visszaadott művelet azonosítóját, és használja a **GetReadOperationResult** metódussal, hogy lekérdezze a szolgáltatást a művelet eredményeihez. A következő kód ellenőrzi a műveletet egy másodperces időközönként, amíg az eredmények vissza nem állnak. Ezután kinyomtatja a kinyert szöveges adatát a konzolon.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_response)]

### <a name="display-read-results"></a>Olvasási eredmények megjelenítése

Adja hozzá a következő kódot a beolvasott szöveges adat elemzéséhez és megjelenítéséhez, majd fejezze be a függvény definícióját.

[!code-go[](~/cognitive-services-quickstart-code/go/ComputerVision/ComputerVisionQuickstart.go?name=snippet_read_display)]

> [!div class="nextstepaction"]
> [Beolvastam](?success=read-printed-handwritten-text#run-the-application) a [problémát](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=read-printed-handwritten-text)

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást az alkalmazás könyvtárából a `go run` paranccsal.

```bash
go run sample-app.go
```

> [!div class="nextstepaction"]
> [Futtattam az alkalmazást a](?success=run-the-application#clean-up-resources) [probléma megoldásához](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=run-the-application)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [](?success=clean-up-resources#next-steps) [Egy hibába ütközött az](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Go&Section=clean-up-resources) erőforrások

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan telepítheti az OCR-ügyféloldali kódtárat, és hogyan használhatja az olvasási API-t. Következő lépésként tekintse meg az API-funkciók beolvasása című témakört.

> [!div class="nextstepaction"]
>[Az olvasási API meghívása](../../Vision-API-How-to-Topics/call-read-api.md)

* [OCR – áttekintés](../../overview-ocr.md)
* A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/go/ComputerVision/ComputerVisionQuickstart.go)található.
