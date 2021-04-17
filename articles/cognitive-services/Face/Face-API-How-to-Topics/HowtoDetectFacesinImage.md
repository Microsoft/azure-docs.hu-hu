---
title: Arcok felismerése egy képen – Face
titleSuffix: Azure Cognitive Services
description: Ez az útmutató bemutatja, hogyan használható az arcfelismerés olyan attribútumok kinyerése egy adott képből, mint a nem, az életkor vagy a testkép.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: sbowles
ms.custom: devx-track-csharp
ms.openlocfilehash: 71e98b735b4aa4631d73f8730a48c56a8c7585ab
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497642"
---
# <a name="get-face-detection-data"></a>Arcészlelési adatok lekérte

Ez az útmutató bemutatja, hogyan használható az arcfelismerés olyan attribútumok kinyerése egy adott képből, mint a nem, az életkor vagy a testkép. Az útmutatóban található kódrészletek C# nyelven íródottak a Azure Cognitive Services Face ügyfélkódtár használatával. Ugyanez a funkció a következőn keresztül [érhető REST API.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

Ez az útmutató a következőt mutatja be:

- A képeken található arcok helyének és dimenzióinak leése.
- Különböző arcok, például a pupillák, az orr és a orr helyét egy képen.
- Az észlelt arc nemének, korának, érzelmeinek és egyéb jellemzőinek kitalálása.

## <a name="setup"></a>Beállítás

Ez az útmutató feltételezi, hogy már felépített egy [Nevű FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient) objektumot face előfizetői kulccsal és `faceClient` végponti URL-címekkel. Innen használhatja az arcészlelési funkciót az útmutatóban használt [DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync)vagy a [DetectWithStreamAsync hívásával.](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync) A szolgáltatás beállításának utasításaiért kövesse az egyik rövid útmutatót.

Ez az útmutató a Detect hívás pontos adataival foglalkozik, például azzal, hogy milyen argumentumokat lehet átadni, és mit lehet tenni a visszaadott adatokkal. Javasoljuk, hogy csak a szükséges funkciókat lekérdezi. Minden művelet további időt vesz igénybe.

## <a name="get-basic-face-data"></a>Alapszintű arcadatok lekérte

Ha arcokat keres, és le tudja szerezni a helyüket egy képen, hívja meg a [DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync) vagy a [DetectWithStreamAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync) metódust, és állítsa a _returnFaceId_ paramétert **true (igaz) értékre.** Ez az alapértelmezett beállítás.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic1":::

A visszaadott [DetectedFace](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface) objektumokból lekérdezheti az egyedi azonosítójukat, valamint egy téglalapot, amely megadja az arc képpontkoordinátáit.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic2":::

Az arc helyének és dimenzióinak elemzésével kapcsolatos információkért lásd: [FaceRectangle.](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle) Ez a téglalap általában tartalmazza a szemeket, a szemüveget, az orrot és a szájat. A fej, a fül és az áll nem feltétlenül szerepel benne. Ha az arcot tartalmazó téglalapot egy teljes fej körülvágására vagy egy közepes álló tájolásra használja, például fényképazonosító típusú kép esetén, mindkét irányban kibonthatja a téglalapot.

## <a name="get-face-landmarks"></a>Arcok nevezetességének lekérte

[Az arc nevezetességei](../concepts/face-detection.md#face-landmarks) könnyen megtalálható pontok az arcon, például a pupillák vagy az orr csúcsa. Az arc nevezetesség-adatainak lekereséséhez állítsa a _detectionModel paramétert_ **DetectionModel.Detection01,** a _returnFaceLandmarks_ paramétert pedig **true (igaz) értékre.**

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks1":::

A következő kód bemutatja, hogyan lehet lekérni az orr és a pupillák helyét:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks2":::

Arcok nevezetesség-adatait is használhatja az arc irányának pontos kiszámításához. Meghatározhatja például az arc elforgatását vektorként a száj közepétől a szem közepéig. A következő kód kiszámítja ezt a vektort:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="direction":::

Ha ismeri az arc irányát, elforgathatja a téglalap alakú arckeretet a megfelelőbb igazítás érdekében. A képeken látható arcok körülvágására programozott módon elforgathatja a képet, hogy az arcok mindig függőlegesen jelenjenek meg.

## <a name="get-face-attributes"></a>Arcattribútumok lekérte

Az arcokat jelölő téglalapok és nevezetességek mellett az arcészlelési API az arcok több fogalmi attribútumát is képes elemezni. A teljes listát az [Arcattribútumok fogalmi szakaszban](../concepts/face-detection.md#attributes) láthatja.

Az arcattribútumok elemzéséhez állítsa a _detectionModel_ paramétert **DetectionModel.Detection01,** a _returnFaceAttributes_ paramétert pedig a [FaceAttributeType enum](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype) értékek listájára.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes1":::

Ezután lekérte a visszaadott adatokra mutató hivatkozásokat, és igény szerint további műveleteket is el lehet látni.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes2":::

Az egyes attribútumokkal kapcsolatos további információkért tekintse meg az Arcfelismerés és az attribútumok fogalmi [útmutatóját.](../concepts/face-detection.md)

## <a name="next-steps"></a>Következő lépések

Ebben az útmutatóban megtanulta, hogyan használhatja az arcfelismerés különböző funkcióit. Ezután integrálja ezeket a funkciókat egy alkalmazásba, és adjon hozzá arcadatokat a felhasználóktól.

- [Oktatóanyag: Felhasználók hozzáadása Face-szolgáltatáshoz](../enrollment-overview.md)

## <a name="related-topics"></a>Kapcsolódó témakörök

- [Referenciadokumentáció (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Referenciadokumentáció (.NET SDK)](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)
