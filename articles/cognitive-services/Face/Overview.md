---
title: Mi az a Face API?
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan használható az arcfelismerési szolgáltatás képeken szereplő arcok észlelésére és elemzésére.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 02/20/2019
ms.author: pafarley
ms.openlocfilehash: 6fba9e6387e60f2156ee01461c3bba22405376fc
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64729574"
---
# <a name="what-is-the-azure-face-api"></a>Mi az Azure Face API?

Az Azure Face API egy kognitív szolgáltatás, amely algoritmusokat biztosít a képeken szereplő emberi arcok észleléséhez, felismeréséhez és elemzéséhez. Az emberi arcokkal kapcsolatos információk feldolgozásának képessége számos különböző szoftveres forgatókönyvben lehet fontos, ilyenek például a biztonsági szolgáltatások, a természetes felhasználói felületek, a képtartalmak elemzése és kezelése, a mobilalkalmazások és a robotika.

A Face API több különféle funkciót biztosít, amelyeket az alábbi szakaszok ismertetnek. Látogasson el lapunkra további információhoz.

## <a name="face-detection"></a>Arcfelismerés

A Face API képes felismerni az emberi arcokat egy képen, és visszaadja a helyüket jelző téglalap koordinátáit. Szükség esetén arcfelismerés kibonthatja a face kapcsolódó attribútumok, például a fő testtartás, gender, kor, érzelem, arcfelismerési haj és szemüveg sorozata.

> [!NOTE]
> Az arcfelismerési szolgáltatás a [Computer Vision API](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) használatával is elérhető, ha azonban további műveleteket szeretne végezni az arcokhoz kapcsolódó adatokkal, a Face API-t (ezt a szolgáltatást) kell használnia.

![Egy nő és egy férfi képe, téglalappal a személyek arca körül, amely alatt az életkoruk és a nemük látható](./Images/Face.detection.jpg)

Arcfelismerés további információkért lásd: a [Arcfelismerés](concepts/face-detection.md) fogalmak cikket, vagy tallózással keresse meg a [észlelése API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) referenciák dokumentációiba.

## <a name="face-verification"></a>Arcellenőrzés

A Verify API hitelesítést végez két észlelt arc között vagy egy észlelt arc és egy személyobjektum között. Lényegében azt értékeli, hogy a két arc ugyanazon személyhez tartozik-e. Ez leginkább a biztonsággal kapcsolatos forgatókönyvekben bizonyulhat hasznosnak. További információkért lásd: a [Arcfelismerési](concepts/face-recognition.md) fogalmak útmutató vagy a [ellenőrzése API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) referenciák dokumentációiba.

## <a name="find-similar-faces"></a>Hasonló arcok keresése

A Find Similar API a célarcot és a jelöltek arcát használva megkeresi azon arcok egy kisebb részhalmazát, amelyek a leginkább hasonlítanak a célarcra. Két működési mód támogatott, a **matchPerson** és a **matchFace**. A **matchPerson** mód hasonló arcokat ad vissza az ugyanazon személyre irányuló szűrést követően (a [Verify API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) használatával). A **matchFace** mód nem foglalkozik az azonos személyekre vonatkozó szűrővel, és a leginkább hasonló jelöltek arcát adja vissza, amelyek nem feltétlenül tartoznak ugyanazon személyhez.

A következő példában ez a célarc:

![Mosolygó nő](./Images/FaceFindSimilar.QueryFace.jpg)

Ezek pedig a jelölt személyek arcai:

![Öt mosolygó személy. Az „a” és a „b” kép ugyanazt a személyt ábrázolja](./Images/FaceFindSimilar.Candidates.jpg)

Négy hasonló arc megkereséséhez a **matchPerson** mód az (a) és a (b) képet adja vissza, amely a célarccal egyező személyt ábrázolja. A **matchFace** mód az (a), (b), (c) és (d)&mdash;képet adja vissza, pontosan négy jelöltet, még akkor is, ha közülük egyesek nem ugyanazok a személyek, mint a célszemély, vagy a hasonlóság kicsi. További információkért lásd: a [Arcfelismerési](concepts/face-recognition.md) fogalmak útmutató vagy [hasonló API található](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) referenciák dokumentációiba.

## <a name="face-grouping"></a>Arccsoportosítás

A Group API több csoportra osztja az ismeretlen arcokat a hasonlóság alapján. Mindegyik csoport az eredeti arcok halmazának különálló valódi részhalmaza. A csoportokban található arcok mindegyike vélhetően ugyanahhoz a személyhez tartozik, azonban több különböző csoport is lehet egyetlen személyhez kapcsolódóan (mely csoportok egy másik tényező, például az arckifejezés alapján jönnek létre). További információkért lásd: a [Arcfelismerési](concepts/face-recognition.md) fogalmak útmutató vagy a [csoport API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) referenciák dokumentációiba.

## <a name="person-identification"></a>Személyazonosítás

Az Identify API-val az észlelt arcok azonosíthatók a személyek adatbázisa alapján. Ez a fényképkezelési szoftverekben végzett automatikus képcímkézéskor bizonyulhat hasznosnak. Az adatbázis előre létrehozható, és menet közben szerkeszthető.

Az alábbi kép a „myfriends” nevű példaadatbázist ábrázolja. Mindegyik csoport legfeljebb 1 000 000 különböző személyobjektumot tartalmazhat, és mindegyik személyobjektum legfeljebb 248 regisztrált arccal rendelkezhet.

![Táblázat, amely a különböző személyeket 3 oszlopban, az arcképeket pedig 3 sorban tartalmazza](./Images/person.group.clare.jpg)

Egy adatbázis létrehozása és betanítása után azonosítást hajthat végre a csoport és egy új észlelt arc között. Ha a rendszer a csoport egyik tagjaként azonosítja az arcot, visszaadja a személyobjektumot.

Személyek azonosítása kapcsolatos további információkért lásd: a [Arcfelismerési](concepts/face-recognition.md) fogalmak útmutató vagy a [azonosítása API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) referenciák dokumentációiba.

## <a name="use-containers"></a>Tárolók használata

[Arcfelismerés a tárolót használja](face-how-to-install-containers.md) észleléséhez ismeri fel, és arcazonosítás szabványos Docker-tároló közelebb telepítse az adatokhoz.

## <a name="sample-apps"></a>Mintaalkalmazások

Az alábbi mintaalkalmazások a Face API használatának néhány módját mutatják be.

- [Microsoft Face API: Windows-Klienskódtár és a minta](https://github.com/Microsoft/Cognitive-Face-Windows) – a WPF-alkalmazás azt mutatja be, számos forgatókönyv Face észlelési, elemzési és azonosítása.
- [FamilyNotes UWP-alkalmazás](https://github.com/Microsoft/Windows-appsample-familynotes) – Univerzális Windows Platform-alkalmazás (UWP-alkalmazás), amely a beszédfelismerés, Cortana, a szabadkézi bevitel és a kamera használata mellett arcazonosítást alkalmaz egy családi üzenetmegosztási forgatókönyvben.

## <a name="data-privacy-and-security"></a>Adatvédelem és biztonság

Csakúgy, mint a Cognitive Services összes az Arcfelismerés szolgáltatással a fejlesztők célszerű tisztában lennie a vásárlói adatokat a Microsoft házirendeket. Tekintse meg a [Cognitive Services-lapra](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) a további Microsoft Trust Center.

## <a name="next-steps"></a>További lépések

Kövesse egy egyszerű arcfelismerési forgatókönyv kóddal történő implementálásának rövid útmutatóját.

- [Rövid útmutató: Arcfelismerés a képet a .NET SDK használatával C# ](quickstarts/csharp.md) (más nyelvek esetén érhető el)
