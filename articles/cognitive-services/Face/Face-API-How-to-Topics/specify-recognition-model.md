---
title: Felismerési modell meghatározása – Face
titleSuffix: Azure Cognitive Services
description: Ebből a cikkből megtudhatja, hogyan választhatja ki, hogy melyik felismerési modellt szeretné használni az Azure Face alkalmazásban.
services: cognitive-services
author: longli0
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: longl
ms.custom: devx-track-csharp
ms.openlocfilehash: ea6b567d7b48e504d9b79dad568da7170ada5326
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101706826"
---
# <a name="specify-a-face-recognition-model"></a>Arcfelismerési modell megadása

Ez az útmutató bemutatja, hogyan határozhatja meg az Arcfelismerés, az azonosítás és a hasonlóságok észlelését az Azure Face szolgáltatás használatával.

A Face szolgáltatás gépi tanulási modelleket használ a képeken található emberi arcokon végzett műveletek végrehajtásához. Folyamatosan fejlesztjük modelljeink pontosságát az ügyfelek visszajelzései és a kutatás előrehaladása alapján, és ezeket a fejlesztéseket a modell frissítéseiként tesszük elérhetővé. A fejlesztők megadhatják, hogy az Arcfelismerés modell melyik verzióját szeretné használni; kiválaszthatják azt a modellt, amelyik a legjobban megfelel a használati esetnek.

Az Azure Face szolgáltatás három felismerési modellel rendelkezik. A _recognition_01_ (közzétett 2017) és _recognition_02_ (közzétett 2019) modellek folyamatosan támogatottak, így biztosítva a visszamenőleges kompatibilitást a FaceLists-t vagy **PersonGroup**-t használó ügyfelek számára ezekkel a modellekkel. A **FaceList** vagy a **Persongroup** mindig a által létrehozott felismerési modellt fogja használni, és az új arcok társítva lesznek ehhez a modellhez, amikor hozzáadják őket. Ez a létrehozás után nem módosítható, és az ügyfeleknek a megfelelő **FaceList** vagy **PersonGroup** kell használniuk a megfelelő felismerési modellt.

A későbbi felismerési modelleket a saját kényelmében helyezheti át. azonban új FaceLists és PersonGroups kell létrehoznia az Ön által választott felismerési modellel.

A _recognition_03_ modell (közzétett 2020) a jelenleg elérhető legpontosabb modell. Ha Ön új ügyfél, javasoljuk, hogy használja ezt a modellt. _Recognition_03_ nagyobb pontosságot biztosít a hasonlósági összehasonlításokhoz és a személyekre vonatkozó összehasonlításokhoz. Vegye figyelembe, hogy az egyes modellek a többitől függetlenül működnek, és az egyik modellhez beállított megbízhatósági küszöbértéket nem hasonlítjuk össze a többi felismerő modellben.

Olvassa el, hogy megtudja, hogyan határozhat meg egy kiválasztott modellt különböző arc-műveletekben a modellek ütközésének elkerülése érdekében. Ha Ön fejlett felhasználó, és szeretné meghatározni, hogy a legújabb modellre kell váltania, ugorjon a [különböző modellek kiértékelése](#evaluate-different-models) szakaszra, és értékelje ki az új modellt, és hasonlítsa össze az eredményeket az aktuális adatkészlettel.


## <a name="prerequisites"></a>Előfeltételek

Ismernie kell az AI arcfelismerés és-azonosítás fogalmait. Ha nem, tekintse meg az alábbi útmutatókat:

* [Arcfelismerés – fogalmak](../concepts/face-detection.md)
* [Arcfelismerés – fogalmak](../concepts/face-recognition.md)
* [Arcok észlelése egy képben](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>A megadott modellel rendelkező arcok észlelése

Az Arcfelismerés azonosítja az emberi arcok vizuális tereptárgyait, és megkeresi a hozzájuk tartozó mezők helyét. Emellett kinyeri az arc funkcióit és tárolja azokat az azonosításban való használatra. Az összes információ egy arc ábrázolását képezi.

Az észlelési modellt a rendszer a Face funkció kibontásakor használja, így megadhatja a modell verzióját az észlelési művelet végrehajtásakor.

Az [arc-észlelési] API használatakor rendelje hozzá a modell verzióját a (z `recognitionModel` ) paraméterrel. Az elérhető értékek a következők:
* recognition_01
* recognition_02
* recognition_03


Opcionálisan megadhatja a _returnRecognitionModel_ paramétert (az alapértelmezett **Hamis értéket**) annak jelzésére, hogy a _recognitionModel_ válaszként kell-e visszaadni. Ezért a REST API [Arcfelismerés URL-] címe a következőképpen fog kinézni:

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]&subscription-key=<Subscription key>`

Ha az ügyféloldali kódtárat használja, a értéket a következőhöz rendelheti hozzá `recognitionModel` :. Ha üresen hagyja, a rendszer a modell alapértelmezett verzióját `recognition_01` fogja használni. Tekintse meg a .NET-ügyfél függvénytárának következő kódrészletét.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_01", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>Arcok azonosítása a megadott modellel

A Face szolgáltatás képes kinyerni az adatokból a képet, és társíthatja azt egy **személy** objektummal (például a Face API-hívás [hozzáadásával](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) ), és több **személy** objektum is tárolható egy **PersonGroup**. Ezt követően egy új arc összehasonlítható egy **PersonGroup** (az [arc-azonosító] hívással), és azonosítható a csoporton belüli megfelelő személy is.

A **PersonGroup** egyetlen egyedi felismerési modellel kell **rendelkezniük az összes s számára**, és ezt a paramétert használva lehet megadni `recognitionModel` a csoport létrehozásakor ([PersonGroup-Create] vagy [LargePersonGroup-Create]). Ha nem megadja ezt a paramétert, a rendszer az eredeti `recognition_01` modellt használja. Egy csoport mindig a-val létrehozott felismerési modellt fogja használni, és az új arcok társítva lesznek ehhez a modellhez, amikor hozzáadja őket a rendszerhez. Ez a csoport létrehozása után nem módosítható. Ha szeretné megtekinteni, hogy a **PersonGroup** melyik modellre van konfigurálva, használja a [PersonGroup-Get API-] t a _returnRecognitionModel_ paraméter **true** értékre állításával.

Tekintse meg a .NET-ügyfél függvénytárának következő kódrészletét.

```csharp
// Create an empty PersonGroup with "recognition_02" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_02");
```

Ebben a kódban létrejön egy AZONOSÍTÓval rendelkező **PersonGroup** `mypersongroupid` , amely a _recognition_02_ modell használatára van beállítva a Face funkcióinak kinyeréséhez.

Ennek megfelelően meg kell adnia, hogy melyik modellt kell használni a **PersonGroup** összehasonlítására szolgáló arcok észleléséhez (az [arc-észlelési] API használatával). Az Ön által használt modellnek mindig konzisztensnek kell lennie a **PersonGroup** konfigurációjával. Ellenkező esetben a művelet nem kompatibilis modellek miatt sikertelen lesz.

Nincs változás a [Face-IDENTIFY API-] ban; az észleléshez csak a modell verzióját kell megadnia.

## <a name="find-similar-faces-with-specified-model"></a>Hasonló arcok keresése a megadott modellel

A hasonlósági kereséshez is megadhat egy felismerési modellt. A modell verzióját hozzárendelheti a `recognitionModel` [FaceList API-] val vagy a [LargeFaceList]létrehozásához használt Arcfelismerés létrehozásához. Ha nem megadja ezt a paramétert, a `recognition_01` rendszer alapértelmezés szerint a modellt használja. Az Arcfelismerés mindig a szolgáltatással létrehozott felismerési modellt fogja használni, és az új arcok társítva lesznek ehhez a modellhez, amikor hozzáadja őket a listához. Ez a létrehozás után nem módosítható. Ha szeretné megtekinteni, hogy milyen modellel van konfigurálva az Arcfelismerés, használja a [FaceList-Get API-] t a _returnRecognitionModel_ paraméter **true** értékkel való beállításával.

Tekintse meg a .NET-ügyfél függvénytárának következő kódrészletét.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_03");
```

Ez a kód egy nevű arc listát hoz létre `My face collection` a _recognition_03_ modell használatával a szolgáltatások kinyeréséhez. Ha egy új észlelt arc fölé keres hasonló arcokat, az adott arcot a _recognition_03_ modell használatával kell észlelni ([Arcfelismerés]). Az előző szakaszban leírtaknak megfelelően a modellnek konzisztensnek kell lennie.

Az [arc-Find hasonló API-] ban nincs változás. az észleléshez csak a modell verzióját kell megadnia.

## <a name="verify-faces-with-specified-model"></a>Arcok ellenőrzése a megadott modellel

Az [arc-ellenőrzés] API ellenőrzi, hogy két arc ugyanahhoz a személyhez tartozik-e. Nem változik az API ellenőrzése a felismerési modellekkel kapcsolatban, de csak az azonos modellel észlelt arcok hasonlíthatók össze.

## <a name="evaluate-different-models"></a>Különböző modellek kiértékelése

Ha a különböző felismerési modellek teljesítményét össze szeretné hasonlítani a saját adataival, a következőkre lesz szüksége:
1. Hozzon létre három PersonGroups _recognition_01_, _recognition_02_ és _recognition_03_ használatával.
1. A képadatokkal felderítheti az arcokat, és regisztrálhatja őket a következő három **PersonGroup** található **személyekhez**. 
1. A PersonGroups a PersonGroup-Train API használatával taníthatja.
1. Tesztelje a Face-azonosulni mindhárom **PersonGroup**, és hasonlítsa össze az eredményeket.


Ha általában a megbízhatósági küszöbértéket adja meg (a nulla és az egyik közötti érték, amely meghatározza, hogy a modellnek milyen mértékben kell azonosítania egy arcot), előfordulhat, hogy különböző küszöbértékeket kell használnia a különböző modellekhez. Az egyik modell küszöbértéke nem osztható meg egy másikhoz, és nem feltétlenül ugyanazt az eredményt fogja eredményezni.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan határozhatja meg a különböző Face Service API-kkal használandó felismerési modellt. Ezután kövessen egy rövid útmutatót a Arcfelismerés használatának megkezdéséhez.

* [Face .NET SDK](../quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)
* [Face Python SDK](../quickstarts/client-libraries.md?pivots=programming-language-python%253fpivots%253dprogramming-language-python)
* [Face go SDK](../quickstarts/client-libraries.md?pivots=programming-language-go%253fpivots%253dprogramming-language-go)

[Face – Detect]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Szembenézés – hasonlók]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[Face – Identify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Szembenézés – ellenőrzés]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup – Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList – létrehozás]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList – Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[LargeFaceList – létrehozás]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc