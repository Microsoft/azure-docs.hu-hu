---
title: A Face szolgáltatás újdonságai
titleSuffix: Azure Cognitive Services
description: A Face szolgáltatás kibocsátási megjegyzései közé tartozik a különböző verziók kiadási változásainak előzményei.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 03/30/2021
ms.author: pafarley
ms.custom: contperf-fy21q3
ms.openlocfilehash: c580828d29e92ecef7ecc73b8f3e5843c3ecd23d
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078880"
---
# <a name="whats-new-in-face-service"></a>A Face szolgáltatás újdonságai

Az Azure Face szolgáltatás folyamatosan frissül. Ebből a cikkből megtudhatja, hogyan maradhat naprakészen a funkciók fejlesztései, a javítások és a dokumentációs frissítések segítségével.

## <a name="february-2021"></a>2021. február

### <a name="new-face-api-detection-model"></a>Új Face API észlelési modell
* Az új észlelési 03 modell a jelenleg elérhető legpontosabb észlelési modell. Ha Ön új ügyfél, javasoljuk, hogy használja ezt a modellt. Az észlelési 03 a képeken (64x64 képpontokban) található kisebb arcokon is javítja a visszahívást és a pontosságot. A további fejlesztések közé tartozik a hamis pozitív és az elforgatott arc-tájolások jobb észlelése. Az észlelési 03 és az új felismerési 04 modell együttes kombinálásával jobb felismerési pontosságot is biztosítunk. További részletekért tekintse [meg a Arcfelismerés modell megadása](./face-api-how-to-topics/specify-detection-model.md) című témakört.
### <a name="new-detectable-face-attributes"></a>Új észlelhető arc-attribútumok
* Az `faceMask` attribútum a legújabb észlelési 03 modellel érhető el, valamint a további attribútummal, `"noseAndMouthCovered"` amely azt észleli, hogy az álarc a kívánt módon van-e felhasználva, amely az orrát és a száját is tartalmazza. A legújabb maszk-észlelési képesség használatához a felhasználóknak meg kell adniuk az észlelési modellt az API-kérelemben: a modell verziójának a _detectionModel_ paraméterrel való hozzárendelésével `detection_03` . További részletekért tekintse [meg a Arcfelismerés modell megadása](./face-api-how-to-topics/specify-detection-model.md) című témakört.
### <a name="new-face-api-recognition-model"></a>Új Face API-felismerési modell
* Az új felismerési 04 modell a jelenleg elérhető legpontosabb felismerési modell. Ha Ön új ügyfél, javasoljuk, hogy ezt a modellt használja az ellenőrzéshez és az azonosításhoz. Javítja az elismerési 03 pontosságát, beleértve a bejelentkezett felhasználók (Sebészeti maszkok, N95-maszkok, ruha-maszkok) jobb felismerését. Az ügyfelek mostantól olyan biztonságos és zökkenőmentes felhasználói élményeket hozhatnak létre, amelyek észlelik, hogy egy regisztrált felhasználó visel-e a legújabb észlelési 03 modellt, és ismeri fel, kik a legújabb felismerési 04-es modellel rendelkeznek. További részletekért tekintse [meg a Arcfelismerés modell megadása](./face-api-how-to-topics/specify-recognition-model.md) című témakört.


## <a name="january-2021"></a>2021. január
### <a name="mitigate-latency"></a>Késleltetés csökkentése
* A Face csapata közzétett egy új cikket, amely részletesen ismerteti a késés lehetséges okait a szolgáltatás és a lehetséges kockázatcsökkentő stratégiák használata során. Lásd: [az arc szolgáltatás használata esetén a késés csökkentése](./face-api-how-to-topics/how-to-mitigate-latency.md).

## <a name="december-2020"></a>2020. december
### <a name="customer-configuration-for-face-id-storage"></a>Felhasználói konfiguráció a Face ID tároláshoz
* Míg a Face szolgáltatás nem tárolja az ügyfelek lemezképeit, a kinyert arc funkció (ka) t a kiszolgáló tárolja. A Face azonosító a Face funkció azonosítója, amelyet a rendszer a [Face-IDENTIFY](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), a [Face-ellenőrzés](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)és a [Face-Find hasonló](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)módon fog használni. A tárolt Arcfelismerés funkció lejár, és az eredeti észlelési hívás után 24 órával törlődik. Az ügyfelek mostantól meghatározhatják, hogy a rendszer mennyi ideig gyorsítótárazza ezeket a Face-azonosítókat. A maximális érték akár 24 óra is lehet, de a minimális 60 másodperces érték már beállítható. A gyorsítótárazott arc-azonosítók új időtartománya 60 másodperc és 24 óra közötti érték. További részleteket az [arc-észlelési API-](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) referenciában talál (a *faceIdTimeToLive* paraméter).

## <a name="november-2020"></a>2020. november
### <a name="sample-face-enrollment-app"></a>Minta Face beléptetési alkalmazás
* A csapat közzétett egy mintaként szolgáló beléptetési alkalmazást, amely bemutatja az ajánlott eljárásokat az értelmes beleegyezett és a nagy pontosságú Arcfelismerés rendszerek magas színvonalú regisztrációval történő létrehozásához. A nyílt forráskódú minta a [beléptetési alkalmazás létrehozása](build-enrollment-app.md) útmutatóban és a [githubon](https://github.com/Azure-Samples/cognitive-services-FaceAPIEnrollmentSample)érhető el, és készen áll a fejlesztők számára a telepítésére vagy testreszabására. 

## <a name="august-2020"></a>2020. augusztus
### <a name="customer-managed-encryption-of-data-at-rest"></a>A REST-adatok ügyfél által felügyelt titkosítása
* A Face szolgáltatás automatikusan titkosítja az adatait a felhőben való megőrzés során. Az arc-szolgáltatás titkosítása védi az adatait, hogy megfeleljen a szervezeti biztonsági és megfelelőségi kötelezettségeknek. Alapértelmezés szerint az előfizetés a Microsoft által felügyelt titkosítási kulcsokat használja. Az előfizetését az ügyfél által felügyelt kulcsok (CMK) nevű saját kulcsok használatával is kezelheti. További részletek az [ügyfél által felügyelt kulcsokban](./encrypt-data-at-rest.md)találhatók.

## <a name="april-2020"></a>2020. április
### <a name="new-face-api-recognition-model"></a>Új Face API-felismerési modell
* Az új felismerés 03 modell a jelenleg elérhető legpontosabb modell. Ha Ön új ügyfél, javasoljuk, hogy használja ezt a modellt. A decognition 03 jobb pontosságot biztosít mind a hasonlósági összehasonlításokhoz, mind a személyekre vonatkozó összehasonlításokhoz. További részletekért tekintse meg a következőt: [Arcfelismerés modell megadása](./face-api-how-to-topics/specify-recognition-model.md).

## <a name="june-2019"></a>2019. június

### <a name="new-face-api-detection-model"></a>Új Face API észlelési modell
* Az új észlelési 02-modell nagyobb pontosságot biztosít a kis-, az oldalsó, a bezárt és a homályos arcoknál. Használja a [Face-Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), a [FaceList-Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [a LargeFaceList-Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), a [PersonGroup person – Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) és a [LargePersonGroup person](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) hozzáadására, és adja meg az új arc észlelési modell nevét `detection_02` a `detectionModel` paraméterben. További információ az [észlelési modell megadásáról](Face-API-How-to-Topics/specify-detection-model.md).

## <a name="april-2019"></a>2019. április

### <a name="improved-attribute-accuracy"></a>Továbbfejlesztett attribútum pontossága
* A és attribútumok jobb általános pontossága `age` `headPose` . Az `headPose` attribútum a `pitch` most engedélyezett értékkel is frissül. Ezeket az attribútumokat az `returnFaceAttributes` [arc-észlelési](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) paraméter paraméterében adhatja meg `returnFaceAttributes` . 
### <a name="improved-processing-speeds"></a>Javított feldolgozási sebesség
* A Face [-Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), a [FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), a [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), a Face, a PersonGroup, a [Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) és a [LargePersonGroup személy – Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) művelet hozzáadása

## <a name="march-2019"></a>2019. március

### <a name="new-face-api-recognition-model"></a>Új Face API-felismerési modell
* A felismerési 02 modell nagyobb pontossággal rendelkezik. Használja a [Face-Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), a [FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), a [LargeFaceList-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), a [PersonGroup-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) és a [LargePersonGroup-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) paranccsal az új Arcfelismerés modell nevének megadásával a `recognition_02` `recognitionModel` paraméterben. További információ a [felismerési modell megadásáról](Face-API-How-to-Topics/specify-recognition-model.md).

## <a name="january-2019"></a>2019. január

### <a name="face-snapshot-feature"></a>Face Snapshot funkció
* Ez a funkció lehetővé teszi, hogy a szolgáltatás támogassa az adatok áttelepítését az előfizetések között: [Pillanatkép](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/snapshot-get). További információ a [Face-adatok áttelepítéséről egy másik Face-előfizetésre](Face-API-How-to-Topics/how-to-migrate-face-data.md).

## <a name="october-2018"></a>2018. október

### <a name="api-messages"></a>API-üzenetek
* A, a, a `status` `createdDateTime` `lastActionDateTime` és `lastSuccessfulTrainingDateTime` a PersonGroup kifinomult leírása [– betanítási állapot](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247), [LargePersonGroup-betanítási állapot](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae32c6ac60f11b48b5aa5)és [LargeFaceList-betanítási állapot](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a1582f8d2de3616c086f2cf).

## <a name="may-2018"></a>2018. május

### <a name="improved-attribute-accuracy"></a>Továbbfejlesztett attribútum pontossága
* A továbbfejlesztett `gender` attribútum jelentős és továbbfejlesztett,,,, `age` `glasses` `facialHair` `hair` `makeup` attribútumok. Használja őket a [Face-Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` paraméterrel.
### <a name="increased-file-size-limit"></a>Nagyobb fájlméret-korlát
* Megnövelt képfájlok maximális mérete 4 MB-ról 6 MB-ra a [Face-Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), a [FaceList – Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), a [LargeFaceList-Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3), a [PersonGroup person hozzáadásával – Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) és [LargePersonGroup személy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42)hozzáadása.

## <a name="march-2018"></a>2018. március

### <a name="new-data-structure"></a>Új adatstruktúra
* [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) és [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d). További részletek a [nagyméretű szolgáltatások használatáról](Face-API-How-to-Topics/how-to-use-large-scale.md).
* Megnövelt [arc-azonosító](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) `maxNumOfCandidatesReturned` paraméter [1, 5] és [1, 100] között, és az alapértelmezett érték 10.

## <a name="may-2017"></a>2017. május

### <a name="new-detectable-face-attributes"></a>Új észlelhető arc-attribútumok
* A (z),,,,, `hair` `makeup` `accessory` `occlusion` `blur` `exposure` és `noise` attribútumok a [Face-Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` paraméterben lettek hozzáadva.
* Támogatott 10K-PersonGroup és [-Azonosítás](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).
* Támogatott tördelés a [PersonGroup személyek számára –](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241) választható paraméterekkel: `start` és `top` .
* Támogatott Egyidejűség az arcok hozzáadásával/törlésével szemben a különböző FaceLists és a PersonGroup lévő különböző személyekkel.

## <a name="march-2017"></a>2017. március

### <a name="new-detectable-face-attribute"></a>Új észlelhető Face attribútum
* Attribútum hozzáadva `emotion` a [Face-Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` paraméterben.
### <a name="fixed-issues"></a>Megoldott problémák
* Nem lehetett újra észlelni az arcot a [Face-Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) függvény által visszaadott négyszögtel, mint `targetFace` a [FaceList – Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) és a [PersonGroup person – Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b).
* Az érzékelhető felület mérete úgy van beállítva, hogy a 36x36 4096x4096 képpontok között szigorúan legyen.

## <a name="november-2016"></a>2016. november
### <a name="new-subscription-tier"></a>Új előfizetési szintek
* Arcképtárolás standard előfizetés hozzáadva a további megőrzött arcok tárolásához, ha [PersonGroup-személyt használ – arc](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) -vagy [FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) hozzáadása az azonosításhoz vagy a hasonlóság egyeztetéséhez. A tárolt képek díja 1000 arconként 0,5 dollár és a díjszabás napi lebontású. Az ingyenes szintű előfizetések továbbra is a 1 000 összes személyre korlátozódnak.

## <a name="october-2016"></a>2016. október
### <a name="api-messages"></a>API-üzenetek
* A (z) "from"-ben egynél több arc hibaüzenete módosult, és a képen több arc szerepel a " `targetFace` to" [FaceList –](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) az arc és a [PersonGroup személy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)hozzáadása.

## <a name="july-2016"></a>2016. július
### <a name="new-features"></a>Új funkciók
* A személyre kiterjedő objektum hitelesítése támogatott a [Face-ellenőrzés](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)során.
* Opcionális paraméter hozzáadva `mode` , amely lehetővé teszi két működési mód kijelölését: `matchPerson` és `matchFace` a [Face-Find hasonló](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) és alapértelmezett értéke `matchPerson` .
* Nem kötelező `confidenceThreshold` paraméter a felhasználó számára, amely azt határozza meg, hogy az adott arc egy személy objektumhoz tartozik-e a [Face-IDENTIFY](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)elemben.
* Hozzáadva opcionális `start` és `top` paraméterek a [PersonGroup-listához](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248) annak engedélyezéséhez, hogy a felhasználó megadja a kezdőpontot és a teljes PersonGroups-számot.

## <a name="v10-changes-from-v0"></a>V 1.0 változások a v0-ból

* A szolgáltatás gyökérszintű végpontjának frissítése a verzióról a verzióra ```https://westus.api.cognitive.microsoft.com/face/v0/``` ```https://westus.api.cognitive.microsoft.com/face/v1.0/``` . Alkalmazott módosítások: [szembenézés észlelése](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [Arcfelismerés](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), arc-azonosítás, [Face-Find hasonló](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) és [Face-Group](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).
* A képpontok minimálisan észlelhető mérete a 36x36 képpont értékre módosult. A 36x36 képpontnál kisebb arcok nem észlelhetők.
* A PersonGroup és a személyes adatértékek elavultak a Face v0-ben. Ezek az adatműveletek nem érhetők el a Face V 1.0 szolgáltatással.
* A rendszer a Face API v0-végpontját (2016. június 30-án) elavult.