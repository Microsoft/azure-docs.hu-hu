---
title: Észlelési modell meghatározása – Face
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan választhatja ki, hogy melyik Arcfelismerés modellt használja az Azure Face alkalmazáshoz.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: yluiu
ms.custom: devx-track-csharp
ms.openlocfilehash: 72fd005ce44d116f86d9a0b4c0d1932e2e4facfb
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102425770"
---
# <a name="specify-a-face-detection-model"></a>Arcfelismerési modell megadása

Ez az útmutató bemutatja, hogyan határozhatja meg az Azure Face szolgáltatás Arcfelismerés modelljét.

A Face szolgáltatás gépi tanulási modelleket használ a képeken található emberi arcokon végzett műveletek végrehajtásához. Folyamatosan fejlesztjük modelljeink pontosságát az ügyfelek visszajelzései és a kutatás előrehaladása alapján, és ezeket a fejlesztéseket a modell frissítéseiként tesszük elérhetővé. A fejlesztők megadhatják, hogy az Arcfelismerés modell melyik verzióját szeretné használni; kiválaszthatják azt a modellt, amelyik a legjobban megfelel a használati esetnek.

Olvassa el, hogy megtudja, hogyan határozhatja meg az Arcfelismerés modellt bizonyos Face műveletekben. A Face szolgáltatás Arcfelismerés, amikor egy arc képét más adatformátumba alakítja át.

Ha nem biztos abban, hogy a legújabb modellt használja, ugorjon a [különböző modellek kiértékelése](#evaluate-different-models) szakaszra az új modell kiértékeléséhez és az eredmények összehasonlításához a jelenlegi adatkészlettel.

## <a name="prerequisites"></a>Előfeltételek

Ismernie kell a mesterséges intelligencia Arcfelismerés fogalmát. Ha nem, tekintse meg az Arcfelismerés fogalmi útmutatóját vagy útmutatóját:

* [Arcfelismerés – fogalmak](../concepts/face-detection.md)
* [Arcok észlelése egy képben](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>A megadott modellel rendelkező arcok észlelése

Az Arcfelismerés megkeresi az emberi arcok határos helyét, és azonosítja a vizuális tereptárgyait. Kibontja az arc funkcióit, és a későbbi használat érdekében tárolja azokat az [elismerési](../concepts/face-recognition.md) műveletekben.

Az [arc-észlelési] API használatakor a modell verzióját hozzárendelheti a (z `detectionModel` ) paraméterrel. Az elérhető értékek a következők:

* `detection_01`
* `detection_02`
* `detection_03`

Az [arc-észlelési] Rest APIhoz tartozó kérelem URL-címe a következőképpen fog kinézni:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel][&detectionModel]&subscription-key=<Subscription key>`

Ha az ügyféloldali függvénytárat használja, a értékét a `detectionModel` megfelelő sztring átadásával rendelheti hozzá. Ha nem törli a hozzárendelést, az API az alapértelmezett modell () verziót fogja használni `detection_01` . Tekintse meg a .NET-ügyfél függvénytárának következő kódrészletét.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, false, false, recognitionModel: "recognition_04", detectionModel: "detection_03");
```

## <a name="add-face-to-person-with-specified-model"></a>Arc hozzáadása a megadott modellel rendelkező személyhez

A Face szolgáltatás képes kinyerni az adatokból a képet, és társíthatja azt egy **személy** objektumhoz a [PersonGroup-Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API használatával. Ebben az API-hívásban megadhatja az észlelési modellt ugyanúgy, mint az [Arcfelismerés funkcióban].

Tekintse meg a .NET-ügyfél függvénytárának következő kódrészletét.

```csharp
// Create a PersonGroup and add a person with face detected by "detection_03" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_04");

string personId = (await faceClient.PersonGroupPerson.CreateAsync(personGroupId, "My Person Name")).PersonId;

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.PersonGroupPerson.AddFaceFromUrlAsync(personGroupId, personId, imageUrl, detectionModel: "detection_03");
```

Ez a kód egy AZONOSÍTÓval rendelkező **PersonGroup** hoz létre, `mypersongroupid` és egy **személyt** helyez el hozzá. Ezután hozzáadja az adott **személyt** a `detection_03` modell használatával. Ha nem ad meg *detectionModel* paramétert, az API az alapértelmezett modellt fogja használni `detection_01` .

> [!NOTE]
> Nem kell ugyanazt az észlelési modellt használnia egy **személy** objektum összes arcához, és nem kell ugyanazt az észlelési modellt használnia, amikor az új arcok észlelésével összehasonlítja a **személy** objektummal (például a [Face-IDENTIFY] API-val).

## <a name="add-face-to-facelist-with-specified-model"></a>Arc hozzáadása a FaceList a megadott modellel

Az észlelési modellt is megadhatja, ha egy meglévő **FaceList** -objektumhoz ad hozzá egy arcot. Tekintse meg a .NET-ügyfél függvénytárának következő kódrészletét.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_04");

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.FaceList.AddFaceFromUrlAsync(faceListId, imageUrl, detectionModel: "detection_03");
```

Ez a kód létrehoz egy nevű **FaceList** , `My face collection` és hozzáadja a `detection_03` modellt a modellhez. Ha nem ad meg *detectionModel* paramétert, az API az alapértelmezett modellt fogja használni `detection_01` .

> [!NOTE]
> Nem kell ugyanazt az észlelési modellt használnia egy **FaceList** objektum összes arcához, és nem kell ugyanazt az észlelési modellt használnia, amikor a **FaceList** objektummal összehasonlítandó új arcokat észlel.

## <a name="evaluate-different-models"></a>Különböző modellek kiértékelése

A különböző Arcfelismerés modellek különböző feladatokra vannak optimalizálva. A különbségek áttekintését a következő táblázat tartalmazza.

|**detection_01**  |**detection_02**  |**detection_03** 
|---------|---------|---|
|Alapértelmezett választás az összes Arcfelismerés művelethez. | Május 2019-ben jelent meg, és opcionálisan elérhető az összes Arcfelismerés műveletben. |  Február 2021-én megjelent, és opcionálisan elérhető az összes Arcfelismerés műveletben.
|Kisméretű, oldalnézet vagy elmosódott arcokat nem optimalizált.  | Nagyobb pontosság a kis, az oldalsó és a homályos arcokon. | További nagyobb pontosság, többek között a kisebb arcok (64x64 képpont) és az elforgatott arc tájolása.
|A fő arc attribútumokat (a fej, az életkor, az érzelem stb.) adja vissza, ha az észlelési hívásban van megadva. |  Nem ad vissza arc-attribútumokat.     | A "maszk" és a "noseAndMouthCovered" attribútumot adja vissza, ha az észlelési hívásban van megadva.
|Arc tereptárgyak visszaadása, ha meg vannak adva az észlelési hívásban.   | Nem tér vissza az arcokhoz tartozó tereptárgyak.  | Nem tér vissza az arcokhoz tartozó tereptárgyak.

Az észlelési modellek teljesítményének összehasonlítására a legjobb módszer, ha egy minta adatkészleten használják őket. Azt javasoljuk, hogy az egyes észlelési modellek használatával különböző rendszerképeken hívja meg az [arc-észlelési API-] t, különösen a sok arc vagy olyan arc képeit, amelyek nehezen láthatók. Ügyeljen arra, hogy hány arcot ad vissza az egyes modellek.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan határozhatja meg a különböző Face API-kkal használandó észlelési modellt. Ezután kövessen egy rövid útmutatót a Arcfelismerés használatának megkezdéséhez.

* [Face .NET SDK](../quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)
* [Face Python SDK](../quickstarts/client-libraries.md?pivots=programming-language-python%253fpivots%253dprogramming-language-python)
* [Face go SDK](../quickstarts/client-libraries.md?pivots=programming-language-go%253fpivots%253dprogramming-language-go)

[Face – Detect]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Face - Find Similar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Face – Identify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Face - Verify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[FaceList - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250
[LargeFaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc