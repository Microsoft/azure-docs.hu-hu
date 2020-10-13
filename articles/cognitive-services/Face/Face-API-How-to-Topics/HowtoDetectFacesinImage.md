---
title: Arcok észlelése egy képképpel
titleSuffix: Azure Cognitive Services
description: Ez az útmutató bemutatja, hogyan használható a Arcfelismerés olyan attribútumok kinyeréséhez, mint a nemek, az életkor vagy az adott rendszerképből származó adatok.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: sbowles
ms.custom: devx-track-csharp
ms.openlocfilehash: 500099753ee4fe47f02e7f09d9732b71aa3bae36
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91856365"
---
# <a name="get-face-detection-data"></a>Arcfelismerés-adatlekérdezés

Ez az útmutató bemutatja, hogyan használható a Arcfelismerés olyan attribútumok kinyeréséhez, mint a nemek, az életkor vagy az adott rendszerképből származó adatok. Az útmutatóban szereplő kódrészletek a C# nyelven íródnak az Azure Cognitive Services Face ügyféloldali kódtár használatával. Ugyanez a funkció a [Rest APIon](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)keresztül érhető el.

Ez az útmutató a következőket mutatja be:

- Az arcok helyeinek és méreteinek beolvasása egy képben.
- Különböző arc-tereptárgyak (például tanulók, orr és száj) helyeinek beszerzése egy képben.
- Kitalálhatja az észlelt arc nemet, korát, érzelemét és egyéb attribútumait.

## <a name="setup"></a>Telepítés

Ez az útmutató feltételezi, hogy már létrehozta a nevű [FaceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient?view=azure-dotnet) -objektumot `faceClient` egy Face előfizetési kulccsal és egy végpont URL-címmel. Innen a Arcfelismerés funkciót az útmutatóban vagy a [DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet)használt [DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet)meghívásával is használhatja. A szolgáltatás beállításával kapcsolatos utasításokért kövesse az egyik rövid útmutatót.

Ez az útmutató az észlelési hívás sajátosságait mutatja be, például azt, hogy milyen argumentumokat adhat át, és hogy mit tehet a visszaadott adatmennyiséggel. Javasoljuk, hogy csak a szükséges szolgáltatásokat kérdezze le. Az egyes műveletek végrehajtása további időt vesz igénybe.

## <a name="get-basic-face-data"></a>Alapszintű Face-adatok beolvasása

Az arcok megkereséséhez és a helyüknek a képen való lekéréséhez hívja meg a [DetectWithUrlAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync?view=azure-dotnet) vagy a [DetectWithStreamAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync?view=azure-dotnet) metódust az _returnFaceId_ paraméter **true**értékre állításával. Ez az alapértelmezett beállítás.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic1":::

Lekérdezheti a visszaadott [DetectedFace](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface?view=azure-dotnet) objektumokat egyedi azonosítójuk és egy olyan négyszög között, amely az arc képpontjának koordinátáit adja meg.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic2":::

További információ az arc helyének és méreteinek elemzéséről: [FaceRectangle](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle?view=azure-dotnet). Ez a négyszög általában a szemet, a szemöldökét, az orrát és a száját tartalmazza. A fej, a fülek és az álla teteje nem feltétlenül szerepel. Ha a Face téglalapot szeretné használni egy teljes fej kivágásához vagy egy közép-shot portré létrehozásához, például egy fénykép-azonosító típusú képhez, kibonthatja a téglalapot az egyes irányokban.

## <a name="get-face-landmarks"></a>Face-tereptárgyak beolvasása

Az [arc tereptárgyak](../concepts/face-detection.md#face-landmarks) könnyen megtalált pontok, például a tanulók és az orr hegye. A tereptárgyak beszerzéséhez állítsa a _detectionModel_ paramétert a **detectionModel. Detection01** értékre, a _returnFaceLandmarks_ paramétert pedig **true**értékre.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks1":::

A következő kód bemutatja, hogyan kérheti le az orr és a tanulók helyét:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks2":::

A Face tereptárgyak adataival az arc irányának pontos kiszámításához is használható. Például megadhatja az arc elforgatását vektorként a száj közepétől a szem közepéig. A következő kód kiszámítja ezt a vektort:

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="direction":::

Ha ismeri az arc irányát, elforgathatja a téglalap alakú keretet úgy, hogy az megfelelően illeszkedjen. A képeken lévő arcok kivágásához programozott módon forgathatja el a képet úgy, hogy az arcok mindig egyenesen jelenjenek meg.

## <a name="get-face-attributes"></a>Arc attribútumainak beolvasása

A Face észlelési API-k mellett az arc téglalapok és a tereptárgyak is elemezhetők az arc számos fogalmi attribútuma. A teljes listát a [Face attributes](../concepts/face-detection.md#attributes) fogalmi szakasza tartalmazza.

A Face attribútumok elemzéséhez állítsa a _detectionModel_ paramétert a **detectionModel. Detection01** és a _ReturnFaceAttributes_ paraméterre a [FaceAttributeType enumerálási](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype?view=azure-dotnet) értékek listájára.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes1":::

Ezután szerezzen be a visszaadott adatokra mutató hivatkozásokat, és tegye meg az igényeinek megfelelő további műveleteket.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes2":::

Ha többet szeretne megtudni az egyes attribútumokról, tekintse meg a [arcfelismerés és attribútumok](../concepts/face-detection.md) fogalmi útmutatót.

## <a name="next-steps"></a>Következő lépések

Ebben az útmutatóban megtanulta, hogyan használhatja a Arcfelismerés különböző funkcióit. Ezután integrálja ezeket a funkciókat az alkalmazásba egy részletes oktatóanyag követésével.

- [Oktatóanyag: WPF-alkalmazás létrehozása az Arcfelismerés képeken való megjelenítéséhez](../Tutorials/FaceAPIinCSharpTutorial.md)

## <a name="related-topics"></a>Kapcsolódó témakörök

- [Dokumentáció (REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [Hivatkozási dokumentáció (.NET SDK)](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/faceapi?view=azure-dotnet)
