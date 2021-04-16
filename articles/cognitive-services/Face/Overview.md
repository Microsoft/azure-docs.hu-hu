---
title: Mi az az Azure Face szolgáltatás?
titleSuffix: Azure Cognitive Services
description: Az Azure Face szolgáltatás olyan AI-algoritmusokat biztosít, amelyek segítségével emberi arcokat észlelhet, felismerhet és elemezhet képeken.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: arcfelismerés, arcfelismerési szoftver, arcelemzés, arcegyezés, arcfelismerési alkalmazás, arckeresés kép alapján, arcfelismerési keresés
ms.openlocfilehash: a999b2100dc57d7b623cebbbe261f821a80835e3
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505122"
---
# <a name="what-is-the-azure-face-service"></a>Mi az az Azure Face szolgáltatás?

> [!WARNING]
> 2020. június 11-én a Microsoft bejelentette, hogy nem értékesít arcfelismerő technológiát az Egyesült Államok rendőrségének egészen addig, amíg az emberi jogokra alapozott erős szabályozást nem vezetnek be. Ezért az ügyfelek nem használhatnak arcfelismerési funkciókat vagy az Azure-szolgáltatásokban foglalt funkciókat (például Face vagy Video Indexer, ha az ügyfél a Egyesült Államok.

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Az Azure Face szolgáltatás olyan AI-algoritmusokat biztosít, amelyek emberi arcokat észlelnek, ismernek fel és elemeznek képeken. Az arcfelismerő szoftver számos különböző helyzetben fontos, például a biztonság, a természetes felhasználói felület, a képtartalmak elemzése és kezelése, a mobilalkalmazások és a robotika.

A Face szolgáltatás számos különböző arcelemzési funkciót biztosít, amelyeket a következő szakaszok ismertetnek.

Ez a dokumentáció a következő típusú cikkeket tartalmazza:
* A [rövid útmutatók olyan](./Quickstarts/client-libraries.md) részletes utasítások, amelyek segítségével hívásokat kezdeményez a szolgáltatáshoz, és rövid idő alatt lekért eredményeket. 
* Az [útmutatók a](./Face-API-How-to-Topics/HowtoDetectFacesinImage.md) szolgáltatás pontosabb vagy testreszabottabb módon való használatával kapcsolatos utasításokat tartalmaznak.
* Az [elméleti cikkek](./concepts/face-detection.md) részletes magyarázatot nyújtanak a szolgáltatás funkcióiról és funkcióiról.
* Az [oktatóanyagok hosszabb](./enrollment-overview.md) útmutatók, amelyekből megtudhatja, hogyan használhatja ezt a szolgáltatást összetevőként a szélesebb körű üzleti megoldásokban.

## <a name="face-detection"></a>Arcfelismerés

A Detect API észleli a képeken látható emberi arcokat, és visszaadja azok helyének téglalap-koordinátáit. Az arcfelismerés igény szerint archoz kapcsolódó attribútumok sorozatát is kinyerheti, például fejlök, nem, életkor, érzelem, arcszőrzet és szemüveg. Ezek az attribútumok általános előrejelzések, nem tényleges besorolások. 

> [!NOTE]
> Az arcfelismerési funkció a Computer Vision [is elérhető.](../computer-vision/overview.md) Ha azonban további Arcműveleteket szeretne, például azonosítani, ellenőrizni, hasonlót találni vagy csoportot, akkor inkább ezt a Face szolgáltatást használja.

![Egy nő és egy férfi képe, téglalapokkal az arcuk körül, valamint az életkoruk és a nemük](./Images/Face.detection.jpg)

Az arcfelismeréssel kapcsolatos további információkért tekintse meg az Arcfelismerés [fogalmai](concepts/face-detection.md) cikket. Lásd még a Detect API referenciadokumentációját. [](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

## <a name="face-verification"></a>Arcellenőrzés

A Verify API az Észlelésre épül, és a következő kérdésre válaszol: "Ez a két kép ugyanaz a személy?". Az ellenőrzést "egy-az-egyhez" egyezésnek is nevezik, mert a rendszer csak egy regisztrált sablonhoz hasonlítja össze a mintavételi rendszerképet. Az ellenőrzés személyazonosság-ellenőrzési vagy hozzáférés-vezérlési forgatókönyvekben használható annak ellenőrzésére, hogy a kép egyezik-e egy korábban rögzített képpel (például egy kormányzati azonosítókártyáról származó fényképről). További információért tekintse meg az [arcfelismerési](concepts/face-recognition.md) fogalmak útmutatóját vagy a Verify API referenciadokumentációt. [](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)

## <a name="face-identification"></a>Arcok azonosítása

Az Identify API az Észleléssel is kezdődik, és a következő kérdésre ad választ: "Az észlelt arc megfeleltethető egy adatbázisban regisztrált arcnak?" Mivel az arcfelismerési kereséshez hasonló, az "egy-a-többhez" egyezésnek is nevezik. A rendszer a jelölt egyezéseket annak alapján ad vissza, hogy a mintavételi sablon és az észlelt arc milyen szorosan egyezik az egyes regisztrált sablonokkal.

Az alábbi képen egy nevű adatbázis látható `"myfriends"` egy példán. Minden csoport legfeljebb 1 millió különböző személyobjektumot tartalmazhat. Mindegyik személyobjektumhoz legfeljebb 248 arc lehet regisztrálva.

![Egy rács három oszloppal különböző személyek számára, amelyek mind három sor arcképet tartalmaznak](./Images/person.group.clare.jpg)

Miután létrehozott és betanított egy adatbázist, egy új észlelt arccal azonosítást is készíthet a csoporton. Ha a rendszer a csoport egyik tagjaként azonosítja az arcot, visszaadja a személyobjektumot.

A személyazonosítással kapcsolatos [](concepts/face-recognition.md) további információkért tekintse meg az arcfelismerési fogalmak útmutatóját vagy az [Identify API referenciadokumentációját.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)

## <a name="find-similar-faces"></a>Hasonló arcok keresése

A Hasonló keresése API megfelel a cél arcnak és a jelölt arcok halmazának, így a cél archoz hasonló arcok kisebb halmazát találja meg. Ez kép alapján való arckereséshez hasznos. 

Két munkamód támogatott: **a matchPerson** és a **matchFace.** A **matchPerson** mód hasonló arcokat ad vissza, miután ugyanazon személyre szűrt a [Verify API használatával.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) A **matchFace mód** figyelmen kívül hagyja a same-person szűrőt. Visszaadja a hasonló jelölt arcok listáját, amelyek ugyanannak a személynek lehetnek vagy nem.

Az alábbi példa a cél arcot mutatja be:

![Mosolygó nő](./Images/FaceFindSimilar.QueryFace.jpg)

Ezek a képek a jelölt arcok:

![Öt mosolygó személy. Az a és b képeken ugyanaz a személy látható.](./Images/FaceFindSimilar.Candidates.jpg)

Négy hasonló arc megkeresését a **matchPerson** mód a és a b értéket adja vissza, amelyek ugyanazt a személyt mutatják, mint a cél arc. A **matchFace** mód a, b, c és d pontosan négy jelöltet ad vissza, még akkor is, ha némelyik nem ugyanaz a személy, mint a cél, vagy alacsony a &mdash; hasonlóságuk. További információkért tekintse meg az [arcfelismerési](concepts/face-recognition.md) fogalmak útmutatóját vagy a [Hasonló API-k megkeresésével kapcsolatos](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) referenciadokumentációt.

## <a name="face-grouping"></a>Arccsoportosítás

A Group API több csoportra osztja az ismeretlen arcokat a hasonlóság alapján. Mindegyik csoport az eredeti arcok halmazának különálló valódi részhalmaza. Egy csoportban valószínűleg az összes arc ugyanannak az személynek a tagja lesz. Egy személyhez több különböző csoport is lehet. A csoportokat egy másik tényező, például a kifejezés különbözteti meg. További információért tekintse meg az [arcfelismerési](concepts/face-recognition.md) fogalmak útmutatóját vagy a [Group API referenciadokumentációját.](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238)


## <a name="sample-apps"></a>Mintaalkalmazások

Az alábbi mintaalkalmazások a Face szolgáltatás használatának néhány módját mutatják be:

- [Face API: A Windows ügyféloldali](https://github.com/Microsoft/Cognitive-Face-Windows) kódtár és minta egy WPF-alkalmazás, amely többféle arcfelismerési, -elemzési és -azonosítási forgatókönyvet mutat be.
- [A FamilyNotes UWP-alkalmazás](https://github.com/Microsoft/Windows-appsample-familynotes) egy Univerzális Windows-platform (UWP) alkalmazás, amely arcfelismerést, valamint beszédet, Cortanát, tinta- és kamerát használ egy családi jegyzetmegosztási forgatókönyvben.

## <a name="data-privacy-and-security"></a>Adatvédelem és biztonság

Ahogy az összes Cognitive Services, a Face szolgáltatást felhasználó fejlesztőknek is tisztában kell lenniük a Microsoft ügyféladatokkal kapcsolatos szabályzatával. További információt a Microsoft [Cognitive Services lapján.](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)

## <a name="next-steps"></a>Következő lépések

Kövesse a rövid útmutatót egy arcfelismerési alkalmazás alapösszetevőinek a választott nyelven való kódhoz.

- [Ügyféloldali kódtár – rövid útmutató.](quickstarts/client-libraries.md)
