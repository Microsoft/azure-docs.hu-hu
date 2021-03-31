---
title: Speaker Recognition áttekintése – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: A Speaker Recognition olyan algoritmusokat biztosít, amelyek a hangbiometry használatával ellenőrzik és azonosítják a hangszórók egyedi hangjellemzőiket. Speaker Recognition a "ki beszél?" kérdésre válaszol. Ez a cikk áttekintést nyújt a Speaker Recognition szolgáltatás előnyeiről és képességeiről.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/02/2020
ms.author: trbye
ms.custom: cog-serv-seo-aug-2020
keywords: hangszórók felismerése, hangvezérelt biometry
ms.openlocfilehash: d07a9960c8a586fa137b4b717afbf91740c265d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "95015324"
---
# <a name="what-is-speaker-recognition-preview"></a>Mi az Speaker Recognition (előzetes verzió)?

A Speaker Recognition szolgáltatás olyan algoritmusokat biztosít, amelyek a hangbiometry használatával ellenőrzik és azonosítják a hangszórók egyedi hangjellemzőiket. Speaker Recognition a "ki beszél?" kérdésre válaszol. A hangképzési adatok egyetlen beszélő számára is megadhatók, amely a beszélő hangja egyedi jellemzői alapján létrehoz egy beléptetési profilt. A profilhoz tartozó hangmintákat úgy ellenőrizheti, hogy a beszélő ugyanaz a személy (előadói ellenőrzés), vagy a hangvételi mintákat a regisztrált hangsugárzó-profilok egy *csoportján* keresztül ellenőrzi, hogy az megfelel-e a csoport összes profiljának (a beszélő azonosítása). Ezzel szemben a [beszélő Diarization](batch-transcription.md#speaker-separation-diarization) csoportosítja a hangfelvételek szegmensét egy batch-műveletben.

## <a name="speaker-verification"></a>Beszélő-ellenőrzés

Speaker Verification leegyszerűsíti a regisztrált hangszórók azonosításának folyamatát a jelszóval vagy a szabad formátumú hangbevitelsel. Felhasználható arra, hogy a felhasználók a biztonságos, a súrlódás nélküli ügyfelek és a megoldások széles körének ellenőrzéséhez, az ügyfelek személyazonosságának ellenőrzése a Calling Centers szolgáltatásban, hogy kapcsolatba lépjenek a szolgáltatással.

### <a name="how-does-speaker-verification-work"></a>Hogyan működik Speaker Verification?

:::image type="content" source="media/speaker-recognition/speaker-rec.png" alt-text="Speaker Verification folyamatábra.":::

A beszélő ellenőrzése szövegtől függő vagy szövegtől független lehet. A **szövegtől függő** ellenőrzés azt jelenti, hogy a hangszóróknak ugyanazt a jelszót kell kiválasztaniuk, amelyet a regisztrálási és ellenőrzési fázisokban is használni kell. A **szövegtől független** ellenőrzés azt jelenti, hogy a hangszórók a beléptetési és ellenőrző kifejezésekben a mindennapos nyelven szólalnak fel.

A **szövegtől függő** ellenőrzéshez a beszélő hangja regisztrálva van az előre definiált kifejezésekből származó hozzáférési kód megadásával. A hangfunkciók a hangrögzítésből származnak, és egyedi hangaláírást alkotnak, míg a kiválasztott hozzáférési kód is felismerhető. A hang aláírása és a jelszó együttes használata a beszélő ellenőrzéséhez használható. 

A **szöveges független** ellenőrzés nem korlátozza azt, amit a beszélő a regisztráció során, vagy a hangmintán ellenőrizni kell, mivel csak a hangfunkciókat fogja kinyerni a hasonlósághoz. 

Az API-k nem határozzák meg, hogy a hang élő személyből, vagy egy beléptetett hangszóró utánzatával vagy felvételével van-e. 

## <a name="speaker-identification"></a>Beszélőazonosítás

Speaker Identification a rendszer egy ismeretlen beszélő identitásának meghatározására szolgál a regisztrált hangszórók egy csoportján belül. A Speaker Identification lehetővé teszi a beszédek egyéni hangszórókhoz való megadását, valamint a több hangszóróval rendelkező forgatókönyvek értékének feloldását, például:

* A távoli értekezletek hatékonyságát támogató megoldások 
* Többfelhasználós eszközök személyre szabásának létrehozása

### <a name="how-does-speaker-identification-work"></a>Hogyan működik Speaker Identification?

A beszélő azonosításának beléptetése **szövegtől független**, ami azt jelenti, hogy nincs korlátozás arra vonatkozóan, hogy a beszélő milyen hangon van. A Speaker Verificationhoz hasonlóan a beléptetési fázisban a beszélő hangja rögzített, a hangfunkciók pedig egyedi hangaláírást alkotnak. Az azonosítási fázisban a bemeneti hangminta összehasonlítható a regisztrált hangok egy adott listájával (az egyes kérésekben legfeljebb 50-re).

## <a name="data-security-and-privacy"></a>Adatbiztonság és adatvédelem

A hangszórók beléptetési adatai biztonságos rendszeren tárolódnak, beleértve a beléptetés hangfelvételét és a hangaláírás funkcióit. A beléptetéshez használt hangfelvételi hang csak akkor használatos, ha az algoritmus frissül, és a szolgáltatásokat újra kell kibontani. A szolgáltatás nem tartja meg a beszédfelismerési fázisban a szolgáltatásnak eljuttatott hangfelvételt vagy kinyert hangfunkciókat. 

Szabályozhatja, hogy mennyi ideig kell megőrizni az adatmegőrzési időtartamot. Az API-hívásokkal létrehozhat, frissíthet és törölhet beléptetési adatkészleteket az egyes hangszórókhoz. Az előfizetés törlésekor a rendszer az előfizetéshez társított összes hangszóró-regisztrációs adatmennyiséget is törli. 

Akárcsak az összes Cognitive Services erőforrás esetében, a Speaker Recognition szolgáltatást használó fejlesztőknek ismerniük kell a Microsoft adatkezelési szabályzatait. Győződjön meg arról, hogy megkapta a megfelelő engedélyeket a felhasználóktól Speaker Recognition számára. További információkért tekintse meg a [](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/)   Microsoft adatvédelmi központjának Cognitive Services lapját. 

## <a name="common-questions-and-solutions"></a>Gyakori kérdések és megoldások

| Kérdés | Megoldás |
|---------|----------|
| Milyen forgatókönyveket lehet Speaker Recognition használni? | Call Center vásárlói adatok ellenőrzése, hangalapú beteg beadása, találkozó átirata, többfelhasználós eszköz személyre szabása|
| Mi a különbség az azonosítás és az ellenőrzés között? | Az azonosítás az a folyamat, amelynek során a beszélők egy csoportjának tagjait észleli. Az ellenőrzés az a művelet, amely megerősíti, hogy egy beszélő ismert vagy **regisztrált** hanggal egyezik.|
| Mi a különbség a szövegtől függő és a szövegtől független ellenőrzés között? | A szövegtől függő ellenőrzéshez egy konkrét, a regisztrációhoz és az elismeréshez megadott pass-mondatra van szükség. A szövegtől független ellenőrzéshez a beléptetéshez már több hangminta szükséges, de minden elbeszélhető, beleértve az elismerést is.|
| Milyen nyelveket támogat a rendszer? | Angol, francia, spanyol, kínai, német, olasz, Japán és portugál |
| Milyen Azure-régiók támogatottak? | Speaker Recognition egy előzetes verziójú szolgáltatás, amely jelenleg csak az USA nyugati régiójában érhető el.|
| Melyek a támogatott hangformátumok? | 16 bites monó, 16kHz PCM-kódolású WAV |
| A válaszok **elfogadása** és **elutasítása** nem pontos, hogyan hangolhatja be a küszöbértéket? | Mivel az optimális küszöbérték nagy mértékben eltér az alkalmazási helyzettől, az API eldönti, hogy az "elfogadás" vagy az "elutasítás" egyszerűen a 0,5 alapértelmezett küszöbértékén alapul-e. A speciális felhasználóknak javasoljuk, hogy bírálják felül az alapértelmezett döntést, és a saját forgatókönyv alapján végezze el az eredmény finomhangolását. |
| Többször is regisztrálhat egy beszélőt? | Igen, a szövegtől függő ellenőrzéshez akár 50 alkalommal is regisztrálhat egy beszélőt. Szövegtől független ellenőrzés vagy beszélő azonosítása esetén akár 300 másodpercig is regisztrálhat. |
| Milyen adattárolók tárolódnak az Azure-ban? | A beléptetési hang a szolgáltatásban tárolódik, amíg a hangprofil nem [törlődik](./get-started-speaker-recognition.md#deleting-voice-profile-enrollments). Az felismerő hangmintákat nem őrzi meg vagy tárolja a rendszer. |

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> * Az alkalmazásokban használható általános tervezési minták futtatásához hajtsa végre a Speaker Recognition [alapjaival](./get-started-speaker-recognition.md) foglalkozó cikket.
> * Tekintse meg a szöveg-független hangszórók ellenőrzésének [videós oktatóanyagát](https://azure.microsoft.com/resources/videos/speaker-recognition-text-independent-verification-developer-tutorial/) .