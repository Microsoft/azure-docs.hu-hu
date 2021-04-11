---
title: Face JavaScript ügyféloldali kódtár rövid útmutatója
description: Használja a Face ügyféloldali függvénytárát a JavaScripthez az arcok észleléséhez, hasonló kereséséhez (képkeresés a képhez), az arcok azonosításához (arc-felismerési keresés) és az arc-adatok migrálása.
services: cognitive-services
author: v-jaswel
manager: chrhoder
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 11/05/2020
ms.author: v-jawe
ms.openlocfilehash: 8f968572a357bb3c98d9c3133a7ec0a0a94dbf93
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105958404"
---
## <a name="quickstart-face-client-library-for-javascript"></a>Gyors útmutató: Face ügyféloldali kódtár a JavaScripthez

Ismerkedés az arcfelismerő szolgáltatással a JavaScripthez készült Face ügyféloldali kódtár használatával. Az alábbi lépéseket követve telepítheti a csomagot, és kipróbálhatja az alapszintű feladatokhoz tartozó példa kódját. A Face szolgáltatás hozzáférést biztosít a speciális algoritmusokhoz a képeken található emberi arcok észleléséhez és felismeréséhez.

A következőhöz használhatja a Face ügyféloldali kódtárat a JavaScripthez:

* [Arcok felismerése a képeken](#detect-faces-in-an-image)
* [Hasonló arcok keresése](#find-similar-faces)
* [PersonGroup létrehozása](#create-a-persongroup)
* [Arc azonosítása](#identify-a-face)

[Dokumentáció](/javascript/api/@azure/cognitiveservices-face/)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/cognitiveservices/cognitiveservices-face)  |  [Csomag (NPM)](https://www.npmjs.com/package/@azure/cognitiveservices-face)  |  [Példák](/samples/browse/?products=azure&term=face&languages=javascript)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services/)
* A [Node.js](https://nodejs.org/en/) legújabb verziója
* Ha már rendelkezik Azure-előfizetéssel, [hozzon létre egy arc-erőforrást](https://portal.azure.com/#create/Microsoft.CognitiveServicesFace) a Azure Portal a kulcs és a végpont beszerzéséhez. Az üzembe helyezést követően kattintson **az erőforrás keresése** elemre.
    * Szüksége lesz a létrehozott erőforrás kulcsára és végpontra az alkalmazás Face APIhoz való összekapcsolásához. A kulcsot és a végpontot a rövid útmutató későbbi részében található kódra másolja.
    * Az ingyenes díjszabási csomag () segítségével `F0` kipróbálhatja a szolgáltatást, és később is frissítheti az éles környezetben futó fizetős szintre.

## <a name="setting-up"></a>Beállítás

### <a name="create-a-new-nodejs-application"></a>Új Node.js-alkalmazás létrehozása

Egy konzolablak (például a cmd, a PowerShell vagy a bash) ablakban hozzon létre egy új könyvtárat az alkalmazáshoz, és navigáljon hozzá. 

```console
mkdir myapp && cd myapp
```

Futtassa a `npm init` parancsot egy Node-alkalmazás fájlhoz való létrehozásához `package.json` . 

```console
npm init
```

### <a name="install-the-client-library"></a>Az ügyféloldali kódtár telepítése 

Telepítse a `ms-rest-azure` és a `azure-cognitiveservices-face` NPM csomagokat:

```console
npm install @azure/cognitiveservices-face @azure/ms-rest-js
```

Az alkalmazás `package.json` fájlja a függőségekkel lesz frissítve.

Hozzon létre egy nevű fájlt `index.js` , és importálja a következő könyvtárakat:

> [!TIP]
> Egyszerre szeretné megtekinteni a teljes rövid útmutató kódját? Megtalálhatja a [githubon](), amely a jelen rövid útmutatóban szereplő példákat tartalmazza.

```javascript
const msRest = require("@azure/ms-rest-js");
const Face = require("@azure/cognitiveservices-face");
const uuid = require("uuid/v4");
```

Hozzon létre változókat az erőforrás Azure-végpontjának és-kulcsának létrehozásához. 

> [!IMPORTANT]
> Nyissa meg az Azure Portalt. Ha az **Előfeltételek** szakaszban létrehozott Face erőforrás sikeresen telepítve van, kattintson az **Ugrás erőforrásra** gombra a **következő lépések** alatt. A kulcsot és a végpontot az erőforrás- **kezelés** területen, az erőforrás **kulcs és végpont** lapján találja. 
>
> Ne felejtse el eltávolítani a kulcsot a kódból, ha elkészült, és soha ne tegye közzé nyilvánosan. Éles környezetben érdemes lehet biztonságos módszert használni a hitelesítő adatok tárolásához és eléréséhez. További információt a Cognitive Services [biztonsági](../../../cognitive-services-security.md) cikkben talál.

```javascript
key = "<paste-your-face-key-here>"
endpoint = "<paste-your-face-endpoint-here>"
```

## <a name="object-model"></a>Objektummodell

A következő osztályok és felületek kezelik a Face .NET ügyféloldali kódtár főbb funkcióit:

|Név|Leírás|
|---|---|
|[FaceClient](/javascript/api/@azure/cognitiveservices-face/faceclient) | Ez az osztály a Face szolgáltatás használatára vonatkozó engedélyt jelöli, és minden arc funkcióhoz szüksége van rá. Ezt az előfizetési adatok alapján hozza létre, és más osztályok példányainak előállítására használja. |
|[Face](/javascript/api/@azure/cognitiveservices-face/face)|Ez az osztály az emberi arcokkal elvégezhető alapvető észlelési és felismerési feladatokat kezeli. |
|[DetectedFace](/javascript/api/@azure/cognitiveservices-face/detectedface)|Ez az osztály a rendszerkép egyetlen arca által észlelt összes adatmennyiséget jelképezi. Ezzel az oldallal kapcsolatos részletes információkat kérhet le.|
|[FaceList](/javascript/api/@azure/cognitiveservices-face/facelist)|Ez az osztály kezeli a felhőben tárolt **FaceList** -szerkezeteket, amelyek az arcok válogatott készleteit tárolják. |
|[PersonGroupPerson](/javascript/api/@azure/cognitiveservices-face/persongroupperson)| Ez az osztály kezeli a felhőalapú tárolt **személyek** szerkezetét, amelyek egyetlen személyhez tartozó arcok készletét tárolják.|
|[PersonGroup](/javascript/api/@azure/cognitiveservices-face/persongroup)| Ez az osztály kezeli a felhőben tárolt **PersonGroup** -szerkezeteket, amelyek a különböző **személyre** kiterjedő objektumok készletét tárolják. |

## <a name="code-examples"></a>Kódpéldák

Az alábbi kódrészletek azt mutatják be, hogyan végezheti el a következő feladatokat a .NET-hez készült Face ügyféloldali kódtár használatával:

* [Az ügyfél hitelesítése](#authenticate-the-client)
* [Arcok felismerése a képeken](#detect-faces-in-an-image)
* [Hasonló arcok keresése](#find-similar-faces)
* [PersonGroup létrehozása](#create-a-persongroup)
* [Arc azonosítása](#identify-a-face)

> [!TIP]
> Egyszerre szeretné megtekinteni a teljes rövid útmutató kódját? Megtalálhatja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/Face/sdk_quickstart.js), amely a jelen rövid útmutatóban szereplő példákat tartalmazza.

## <a name="authenticate-the-client"></a>Az ügyfél hitelesítése

Ügyfelet hoz létre a végponttal és a kulccsal. Hozzon létre egy **[ApiKeyCredentials](/javascript/api/@azure/ms-rest-js/apikeycredentials)** objektumot a kulccsal, és használja a végpontján egy **[FaceClient](/javascript/api/@azure/cognitiveservices-face/faceclient)** objektum létrehozásához.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="credentials":::

## <a name="declare-global-values-and-helper-function"></a>Globális értékek és segítő függvények deklarálása

A következő globális értékekre van szükség a később felvenni kívánt arc-műveletek közül.

Az URL-cím egy minta lemezképek mappájára mutat. Az UUID a létrehozandó PersonGroup nevét és AZONOSÍTÓját is fogja szolgálni.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="globals":::

A következő függvénnyel megvárhatja, amíg a PersonGroup betanítása befejeződik.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="helpers":::

## <a name="detect-faces-in-an-image"></a>Arcok felismerése a képeken

### <a name="get-detected-face-objects"></a>Észlelt Arcfelismerés-objektumok

Hozzon létre egy új metódust az arcok észleléséhez. A `DetectFaceExtract` metódus három képet dolgoz fel a megadott URL-címen, és létrehozza a **[DetectedFace](/javascript/api/@azure/cognitiveservices-face/detectedface)** objektumok listáját a program memóriájában. A **[FaceAttributeType](/javascript/api/@azure/cognitiveservices-face/faceattributetype)** értékek listája határozza meg a kinyerni kívánt szolgáltatásokat. 

A `DetectFaceExtract` metódus ezután elemzi és kinyomtatja az összes észlelt arc attribútum-adatát. Minden attribútumot külön kell megadni az eredeti Arcfelismerés API-hívásban (a **[FaceAttributeType](/javascript/api/@azure/cognitiveservices-face/faceattributetype)** listában). A következő kód dolgozza fel az összes attribútumot, de valószínűleg csak egy vagy néhányat kell használnia.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="detect":::

> [!TIP]
> A helyi rendszerképben is felderítheti az arcokat. Tekintse meg a [Face](/javascript/api/@azure/cognitiveservices-face/face) metódusokat, például a [DetectWithStreamAsync](/javascript/api/@azure/cognitiveservices-face/face#detectWithStream_msRest_HttpRequestBody__FaceDetectWithStreamOptionalParams__ServiceCallback_DetectedFace____).

## <a name="find-similar-faces"></a>Hasonló arcok keresése

A következő kód egyetlen észlelt arcot (forrást) használ, és a találatok között megkeresi a többi arcot (TARGET) Ha egyezést talál, kinyomtatja az egyeztetett arc AZONOSÍTÓját a konzolon.

### <a name="detect-faces-for-comparison"></a>Arcok észlelése összehasonlításhoz

Először határozza meg a második Arcfelismerés módszert. A képek összevetése előtt fel kell ismernie az arcokat, és ez az észlelési módszer összehasonlítási műveletekre van optimalizálva. Nem kibontja a részletes Arcfelismerés-attribútumokat, például a fenti részben, és egy másik felismerési modellt használ.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="recognize":::

### <a name="find-matches"></a>Egyezések keresése

A következő metódus észleli a megcélzott lemezképekben található arcokat és egyetlen forrás képét. Ezután összehasonlítja őket, és megkeresi a forrás képéhez hasonló összes megcélzott képet. Végül kiírja a megfeleltetés részleteit a-konzolra.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="find_similar":::

## <a name="identify-a-face"></a>Arc azonosítása

Az [azonosítási](/javascript/api/@azure/cognitiveservices-face/face#identify_string____FaceIdentifyOptionalParams__ServiceCallback_IdentifyResult____) művelet egy személy (vagy több személy) képét veszi igénybe, és megkeresi a rendszerképben lévő egyes arcok identitását (Arcfelismerés-keresés). Összehasonlítja az észlelt elemeket egy [PersonGroup](/javascript/api/@azure/cognitiveservices-face/persongroup), egy olyan, a különböző [személy](/javascript/api/@azure/cognitiveservices-face/person) objektumokat tartalmazó adatbázissal, amelyek az arc funkciói ismertek. Az azonosítási művelet végrehajtásához először létre kell hoznia és be kell tanítania egy [PersonGroup](/javascript/api/@azure/cognitiveservices-face/persongroup).

### <a name="add-faces-to-persongroup"></a>Arcok hozzáadása a PersonGroup

Hozza létre a következő függvényt az arcok hozzáadásához a [PersonGroup](/javascript/api/@azure/cognitiveservices-face/persongroup).

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="add_faces":::

### <a name="wait-for-training-of-persongroup"></a>Várakozás a PersonGroup betanítására

Hozza létre a következő segítő függvényt, és várjon, amíg a **PersonGroup** befejeződik.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="wait_for_training":::

### <a name="create-a-persongroup"></a>PersonGroup létrehozása

A következő kód:
- Létrehoz egy [PersonGroup](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-face/persongroup)
- A korábban definiált hívással hozzáadja az arcokat a **PersonGroup** `AddFacesToPersonGroup` .
- Betanítja a **PersonGroup**.
- A **PersonGroup** lévő arcokat azonosítja.

A **PersonGroup** és a hozzá tartozó **személyre** vonatkozó objektumok most már készen állnak az ellenőrzési, azonosítási vagy csoportosítási műveletekben való használatra.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="identify":::

> [!TIP]
> Helyi rendszerképekből is létrehozhat **PersonGroup** . Tekintse meg a [PersonGroupPerson](/javascript/api/@azure/cognitiveservices-face/persongroupperson) metódusokat, például a [AddFaceFromStream](/javascript/api/@azure/cognitiveservices-face/persongroupperson#addFaceFromStream_string__string__msRest_HttpRequestBody__Models_PersonGroupPersonAddFaceFromStreamOptionalParams_).

## <a name="main"></a>Fő

Végül hozza létre a `main` függvényt, és hívja meg.

:::code language="js" source="~/cognitive-services-quickstart-code/javascript/Face/sdk_quickstart.js" id="main":::

## <a name="run-the-application"></a>Az alkalmazás futtatása

Futtassa az alkalmazást a gyors üzembe helyezési `node` fájlban található paranccsal.

```console
node index.js
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban megtanulta, hogyan használhatja a Face ügyféloldali függvénytárat a JavaScripthez, hogy elvégezzék az Arcfelismerés feladatait. Ezután tekintse át a dokumentációt, és ismerkedjen meg a könyvtárral.

> [!div class="nextstepaction"]
> [Face API referenciája (JavaScript)](/javascript/api/@azure/cognitiveservices-face/)

* [Mi az a Face szolgáltatás?](../../overview.md)
* A minta forráskódja a [githubon](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/Face/sdk_quickstart.js)található.