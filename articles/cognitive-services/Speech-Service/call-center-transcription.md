---
title: Call Center transzkripció-Speech szolgáltatás
titleSuffix: Azure Cognitive Services
description: A beszédfelismerés gyakori forgatókönyve nagy mennyiségű, különböző rendszerből (például interaktív hangválasztó, IVR) származó telefonos adatok átírása. A beszédfelismerési szolgáltatás és az egyesített beszéd modell használatával a vállalatok kiváló minőségű átírásokat nyerhetnek a hangrögzítési rendszerekkel.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 19d4cc388494e149b7f258a8e9f154041a3dd070
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "95021966"
---
# <a name="speech-service-for-telephony-data"></a>Beszédfelismerési szolgáltatás a telefonos adatkezeléshez

A vezetékes, mobil telefonok és rádiók által generált telefonos adatok jellemzően alacsony minőségűek és 8 KHz-es tartományba tartoznak, ami kihívást jelent a beszédfelismerés szövegének konvertálásakor. A Speech Service Excel legújabb beszédfelismerési modelljei a telefonos adatok átírásakor, még abban az esetben is, ha az adatok nehezen érthetők. Ezek a modellek nagy mennyiségű telefonos adattal vannak betanítva, és a legjobb piaci felismerési pontossággal rendelkeznek, akár zajos környezetekben is.

A beszédfelismerés gyakori forgatókönyve nagy mennyiségű telefonos adatok átírása, amelyek különböző rendszerekről származnak, mint például az interaktív hangválasztó (IVR). Az ezekhez a rendszerekhez tartozó hang lehet sztereó vagy monó, a jeleknél pedig nem végezhető el a RAW. A beszédfelismerési szolgáltatás és az egyesített beszédfelismerési modell használatával a vállalatok kiváló minőségű átírásokat tudnak készíteni, függetlenül attól, hogy milyen rendszerekben használják a hangfelvételt.

A telefonos adat használatával jobban megismerheti az ügyfelek igényeit, azonosíthatja az új értékesítési lehetőségeket, vagy kiértékelheti a Call Center-ügynökök teljesítményét. Az adatok beírása után a vállalat a kimenetet használhatja olyan célokra, mint a továbbfejlesztett telemetria, a kulcsfontosságú kifejezések azonosítása vagy az ügyfelek hangulatának elemzése.

Az ezen a lapon található technológiákat a Microsoft belsőleg végzi a különböző támogatási hívás-feldolgozási szolgáltatások esetében, valós időben és kötegelt módban egyaránt.

Tekintsük át a beszédfelismerési szolgáltatás által kínált technológiákat és kapcsolódó funkciókat.

> [!IMPORTANT]
> A beszédfelismerési szolgáltatás egyesített modellje változatos adatokkal van betanítva, és egyetlen modellből álló megoldást kínál a telefonos elemzések diktálására.

## <a name="azure-technology-for-call-centers"></a>Azure-technológia a call centerek számára

A beszédfelismerési szolgáltatás funkcióinak funkcionális aspektusán felül az elsődleges céljuk – ha a Call Centerre alkalmazva van –, hogy javítsa a felhasználói élményt. Három egyértelmű tartomány létezik ebben a tekintetben:

- A hívás utáni elemzések, amelyek lényegében a hívási felvételek után kötegelt feldolgozást végeznek.
- A valós idejű elemzések, amelyek a hangjelek feldolgozását végzik a különböző elemzések kinyeréséhez, miközben a hívás zajlik (a hangulat Kiemelt használati eset).
- A hangvezérelt asszisztensek (robotok), vagy az ügyfél és a robot közötti párbeszédet vezetik be arra, hogy az ügyfél nem vesz részt az ügynök részvételével, vagy hogy a mesterséges intelligencia (AI) protokollok alkalmazásával segítse az ügynököt.

A Batch-forgatókönyv megvalósításának tipikus architektúrás ábrája a ![ Call Center transzkripciós Architecture alábbi ábrán látható.](media/scenarios/call-center-transcription-architecture.png)

## <a name="speech-analytics-technology-components"></a>A Speech Analytics technológiai összetevői

Akár a tartomány, akár post-vagy valós idejű, az Azure számos fejlett és új technológiával rendelkezik, amelyek javítják a felhasználói élményt.

### <a name="speech-to-text-stt"></a>Beszéd szövege (STT)

A hívási központ bármely megoldásában a legkeresettebb funkció a [beszéd – szöveg](speech-to-text.md) . Mivel az alsóbb rétegbeli elemzési folyamatok többsége az átadott szövegre támaszkodik, a Word-hibák aránya (_wer_) rendkívül fontos. A Call Center átiratának egyik fő kihívása a Call Centerben elterjedt zaj (például a háttérben megjelenő más ügynökök), a különböző nyelvi területi beállítások és dialektusok, valamint a tényleges telefonos jel alacsony minősége. A WER szorosan összefügg azzal, hogy az akusztikai és nyelvi modellek hogyan vannak betanítva egy adott területi beállításhoz, ezért fontos a modell testreszabásának lehetősége a területi beállításra. A legújabb, egységes 4-es verziójú 4. x modellek az átírás pontosságát és késését jelentik. Több tízezer akusztikus adatmennyiséggel és több milliárd lexikális adattal van kiképezve, a piacon a legpontosabb modellek a Call Center adatainak átírása.

### <a name="sentiment"></a>Hangulat

Annak mérése, hogy az ügyfél jó tapasztalattal rendelkezik-e a Speech Analytics egyik legfontosabb területén, ha alkalmazva van a Call Center területére. A [Batch-átírási API](batch-transcription.md) -ban a vélemények elemzését is kimondjuk. Összeállíthatja a hívási átirat részeként kapott értékek halmazát, hogy meghatározza az ügynökök és az ügyfél hívásának hangulatát.

### <a name="silence-non-talk"></a>Csend (nem beszélő)

Nem ritka, hogy egy támogatási hívás 35%-ában a nem beszélgetési időpontot nevezzük. Bizonyos forgatókönyvek, amelyeken nem beszélhetünk, a következő esetekben fordulnak elő: az ügynökökkel kapcsolatos korábbi előzmények megkeresése az ügyfelekkel, az olyan eszközöket használó ügynökök, amelyek lehetővé teszik az ügyfelek asztalhoz való hozzáférését Rendkívül fontos, hogy a rendszer megtekintse a csendet a hívás során, mivel számos fontos ügyfél-érzékenység fordul elő, amelyek az ilyen típusú forgatókönyvek körül történnek, és amelyek a hívás során jelentkeznek.

### <a name="translation"></a>Fordítás

Néhány vállalat kísérletezik az idegen nyelvi támogatási hívások fordításával, így a kézbesítő vezetők megismerhetik az ügyfelek globális felhasználói élményét. A [fordítási](./speech-translation.md) képességek felülmúlhatatlanok. Nagy számú területi beállításhoz hang-hang vagy hang-szöveg is fordítható.

### <a name="text-to-speech"></a>Text to Speech

A [szöveg és a beszéd](text-to-speech.md) egy másik fontos része az ügyfelekkel kommunikáló robotok megvalósításának. A tipikus elérési út az, hogy az ügyfél beszél, a hangjuk szövegbe kerül, a szöveget pedig a szándékok alapján elemezzük, a válasz a felismert szándékon alapul, és az eszköz felszínre kerül az ügyfél számára, vagy egy szintetizált hangválasztó jön létre. Természetesen mindeznek gyorsan kell történnie – ezért az alacsony késés a rendszerek sikeres sikerének fontos eleme.

A végpontok közötti késés jelentősen alacsony az olyan különböző technológiák esetében, mint például a [beszéd-szöveg](speech-to-text.md), a [Luis](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/), a [bot Framework](https://dev.botframework.com/), a [szöveg és a beszéd](text-to-speech.md).

Az új hangok is megkülönböztetni az emberi hangokat. A hangjukat a robotjának egyedi személyiségét is felhasználhatja.

### <a name="search"></a>Keresés

Az elemzések egy másik része az olyan interakciók azonosítása, amelyekben egy adott esemény vagy tapasztalat történt. Ez általában két módszer egyikével történik. egy alkalmi keresés, ahol a felhasználó egyszerűen csak egy kifejezést és a rendszer válaszait, vagy egy strukturált lekérdezést, amelyben egy elemző olyan logikai utasításokat hozhat létre, amelyek azonosítják a hívást, majd minden egyes hívás indexelhető az adott lekérdezések alapján. Jó keresési példa a mindennapos megfelelőségi nyilatkozat, amely szerint a rendszer a felhívást minőségi célokra rögzíti... ". Számos vállalat szeretné biztosítani, hogy az ügynökök a hívás tényleges rögzítése előtt biztosítsák az ügyfeleknek ezt a jogi nyilatkozatot. A legtöbb elemzési rendszer képes a lekérdezési/keresési algoritmusok által észlelt viselkedések trendjeire, és ez a Trends jelentéskészítés végső soron az analitikai rendszerek legfontosabb funkciói közé tartozik. A [kognitív szolgáltatások címtárában](https://azure.microsoft.com/services/cognitive-services/directory/search/) a végpontok közötti megoldás jelentősen fokozható az indexelési és keresési képességekkel.

### <a name="key-phrase-extraction"></a>Kulcskifejezések kinyerése

Ez a terület az egyik nagyobb kihívást jelentő elemzési alkalmazás, amely az AI és a gépi tanulás alkalmazásából származik. Ebben az esetben az elsődleges forgatókönyv az ügyfél szándékának következtetése. Miért hívja meg az ügyfelet? Mi az ügyfél problémája? Miért adta meg az ügyfél negatív felhasználói élményt? A [text Analytics szolgáltatás](https://azure.microsoft.com/services/cognitive-services/text-analytics/) számos elemzést biztosít a mezőkből, így gyorsan frissítheti a teljes körű megoldást a fontos kulcsszavak vagy kifejezések kinyeréséhez.

Most nézzük meg a kötegelt feldolgozást és a beszédfelismerés valós idejű folyamatait egy kicsit részletesebben.

## <a name="batch-transcription-of-call-center-data"></a>A Call Center-adatfeldolgozás kötegelt átírása

A tömeges hang [átírásához kifejlesztettük a Batch ÁTÍRÓ API](batch-transcription.md)-t. A Batch-átírási API úgy lett kifejlesztve, hogy a nagy mennyiségű hanganyagot aszinkron módon átmásolja. A Call Center-beli adatbevitelt illetően a megoldás a következő pilléreken alapul:

- **Pontosság** – a negyedik generációs egyesített modellek esetében páratlan átírási minőséget biztosítunk.
- **Késés** – megértettük, hogy a tömeges átírások során az átiratok gyorsak lesznek. A [Batch átírási API](batch-transcription.md) -n keresztül kezdeményezett átírási feladatok azonnal várólistára kerülnek, és a feladat futásának megkezdése után a valós idejű átírásnál gyorsabban végezhető el.
- **Biztonság** – tisztában vagyunk vele, hogy a hívások bizalmas adatokat is tartalmazhatnak. Biztos lehet benne, hogy a biztonság a legmagasabb prioritások egyike. Szolgáltatásunk ISO-, SOC-, HIPAA-és PCI-minősítéseket kapott.

A Call Centers nagy mennyiségű hanganyagot állít elő napi rendszerességgel. Ha a vállalat egy központi helyen, például az Azure Storage-ban tárolja a telefonos adatait, a [Batch-átírási API](batch-transcription.md) használatával aszinkron módon kérhet és fogadhat átírásokat.

Egy tipikus megoldás ezeket a szolgáltatásokat használja:

- A beszédfelismerési szolgáltatás a beszédfelismerés szövegének átírására szolgál. A Batch-átírási API használatához szabványos előfizetés (S0) szükséges a beszédfelismerési szolgáltatáshoz. Az ingyenes előfizetések (F0-EK) nem fognak működni.
- Az [Azure Storage](https://azure.microsoft.com/services/storage/) szolgáltatás a telefonos és a Batch-átírási API által visszaadott átiratok tárolására szolgál. Ennek a Storage-fióknak az értesítéseket kell használnia, különösen az új fájlok hozzáadásakor. Ezek az értesítések az átírási folyamat elindítására szolgálnak.
- A [Azure functions](../../azure-functions/index.yml) a közös hozzáférésű aláírások (SAS) URI-azonosítójának létrehozására szolgál az egyes rögzítésekhez, és a http post-kérés elindításával indíthatja el az átírást. Emellett Azure Functions a Batch-átírási API-val történő átírások beolvasására és törlésére vonatkozó kérések létrehozására szolgál.

Belsőleg a fenti technológiákat használjuk a Microsoft ügyfél-hívások kötegelt módban történő támogatásához.
:::image type="content" source="media/scenarios/call-center-batch-pipeline.png" alt-text="A Microsoft ügyfél-hívások kötegelt módban történő támogatásához használt technológiák.":::

## <a name="real-time-transcription-for-call-center-data"></a>A Call Center-adattovábbítás valós idejű átírása

Egyes vállalkozásoknak valós időben kell átírniuk a beszélgetéseket. A valós idejű átírással azonosíthatók a beszélgetéshez kapcsolódó tartalomra és erőforrásokra, a figyelem figyelésére, a kisegítő lehetőségek javítására, illetve az olyan ügyfelek és ügynökök számára készült fordítások, amelyek nem natív hangszórók.

A valós idejű átírást igénylő forgatókönyvek esetében javasoljuk, hogy a [SPEECH SDK](speech-sdk.md)-t használja. Jelenleg a beszéd-szöveg [több mint 20 nyelven](language-support.md)érhető el, az SDK pedig C++, C#, Java, Python, Node.js, Objective-C és JavaScript nyelven érhető el. A minták minden nyelven elérhetők a [githubon](https://github.com/Azure-Samples/cognitive-services-speech-sdk). A legfrissebb hírek és frissítések: [kibocsátási megjegyzések](releasenotes.md).

Belsőleg a fenti technológiákat használjuk a Microsoft ügyfeleinek valós időben történő elemzéséhez, ahogy az a következő ábrán is látható.

![Batch-architektúra](media/scenarios/call-center-reatime-pipeline.png)

## <a name="a-word-on-ivrs"></a>Egy szó a IVRs

A beszédfelismerési szolgáltatás egyszerűen integrálható bármilyen megoldásba a [SPEECH SDK](speech-sdk.md) vagy a [REST API](./overview.md#reference-docs)használatával. A Call Center átirata azonban további technológiákat igényelhet. Általában az IVR rendszer és az Azure közötti kapcsolat szükséges. Habár nem kínálunk ilyen összetevőket, itt látható az a leírás, hogy az IVR-kapcsolatok hogyan járnak.

Számos IVR-vagy telefonos szolgáltatási termék (például a Genesys vagy a AudioCodes) olyan integrációs képességeket kínál, amelyek lehetővé teszik a bejövő és kimenő hangtovábbítást az Azure-szolgáltatásba. Az egyéni Azure-szolgáltatás alapvetően egy adott felületet biztosít a telefonhívási munkamenetek definiálásához (például a hívás indítására vagy a hívás végére), és elérhetővé tehet egy WebSocket API-t, amely a beszédfelismerési szolgáltatással használt bejövő stream-hang fogadására szolgál. A kimenő válaszok, például a beszélgetés átírása vagy a bot-keretrendszerrel létesített kapcsolatok a Microsoft szöveg-beszéd szolgáltatásával állíthatók be, és visszatérhetnek az IVR-hoz a lejátszáshoz.

Egy másik forgatókönyv a közvetlen integráció a munkamenet-kezdeményező protokollal (SIP). Az Azure-szolgáltatás egy SIP-kiszolgálóhoz csatlakozik, így a bejövő adatfolyamot és egy kimenő adatfolyamot kap, amely a beszéd-szöveg és a szöveg-beszéd fázisokhoz használatos. A SIP-kiszolgálóhoz való kapcsolódáshoz olyan kereskedelmi szoftverek állnak rendelkezésre, mint például a Ozeki SDK, vagy [a csapatok hívása és értekezletek API](/graph/api/resources/communications-api-overview) (jelenleg bétaverzióban), amelyek a hanghívások ilyen típusú forgatókönyvének támogatására lettek kialakítva.

## <a name="customize-existing-experiences"></a>Meglévő tapasztalatok testreszabása

 A beszédfelismerési szolgáltatás a beépített modellekkel jól működik. Azonban érdemes lehet tovább testreszabni és hangolni a termék vagy a környezet élményét. A testreszabási lehetőségek köre az akusztikus modell finomhangolása és a márka egyedi hangbetűkészletei között. Miután létrehozott egy egyéni modellt, a beszédfelismerési szolgáltatás bármelyik szolgáltatásával valós időben vagy batch módban használhatja azt.

| Speech szolgáltatás | Modellezés | Leírás |
| -------------- | ----- | ----------- |
| Diktálás | [Akusztikai modell](./how-to-custom-speech-train-model.md) | Hozzon létre egy egyéni akusztikai modellt olyan alkalmazásokhoz, eszközökhöz vagy eszközökhöz, amelyeket az adott környezetben, például autóban vagy gyári szinten használnak, és amelyek külön rögzítési feltételekkel rendelkeznek. Ilyenek például az ékezetes beszédek, a konkrét háttérzajok, vagy egy adott mikrofon használata a rögzítéshez. |
|                | [Nyelvi modell](./how-to-custom-speech-train-model.md) | Hozzon létre egy egyéni nyelvi modellt, amellyel javítható az iparágra jellemző szókincs és nyelvtan, például az orvosi szakkifejezések vagy az informatikai szakzsargonk átírása. |
|                | [Kiejtési modell](./how-to-custom-speech-train-model.md) | Egyéni kiejtési modellel megadhatja a fonetikus űrlapot, és megjelenítheti egy szót vagy kifejezést. Ez hasznos a testreszabott kifejezések, például a terméknév vagy a betűszók kezelésére. Mindössze annyit kell tennie, hogy megkezdi a kiejtési fájlt, amely egy egyszerű `.txt` fájl. |
| Szövegfelolvasás | [Hangtípusok](./how-to-custom-voice-create-voice.md) | Az egyéni hangbetűkészletek lehetővé teszik a márka felismerhető, egyfajta hangjának létrehozását. A kezdéshez csak kis mennyiségű adat szükséges. Minél több, az Ön által megadott információ, annál természetesebb és emberi – például a hangbetűkészletek is megszólalnak. |

## <a name="sample-code"></a>Mintakód

A mintakód a GitHubon érhető el a beszédfelismerési szolgáltatás egyes funkcióihoz. Ezek a minták olyan gyakori forgatókönyveket érintenek, mint például a hang fájlból vagy streamből való olvasása, a folyamatos és az egyszeri felvétel felismerése, valamint az egyéni modellek használata. Az alábbi hivatkozásokkal megtekintheti az SDK-t és a REST-mintákat:

- [Beszéd – szöveg és beszéd fordítási minták (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Batch-átírási minták (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [Szöveg – beszéd minták (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)

## <a name="reference-docs"></a>Dokumentációs dokumentumok

- [Beszéd SDK](./speech-sdk.md)
- [Beszédeszközök SDK](speech-devices-sdk.md)
- [REST API: beszéd – szöveg](rest-speech-to-text.md)
- [REST API: szövegről beszédre](rest-text-to-speech.md)
- [REST API: kötegelt átírás és testreszabás](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Beszédfelismerési szolgáltatás előfizetési kulcsának beszerzése ingyenesen](overview.md#try-the-speech-service-for-free)