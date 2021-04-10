---
title: A Speech Services kvótái és korlátai
titleSuffix: Azure Cognitive Services
description: Gyors útmutató, részletes leírás és ajánlott eljárások az Azure kognitív Speech Services kvótái és korlátai számára
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/27/2021
ms.author: alexeyo
ms.openlocfilehash: 7ef6ed5293ec9ecf49c16f8dfb0b6604942408f0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105937056"
---
# <a name="speech-services-quotas-and-limits"></a>A Speech Services kvótái és korlátai

Ez a cikk egy gyors referenciát és az Azure kognitív Speech Services kvótáinak **részletes leírását** és az összes [díjszabási](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)csomagra vonatkozó korlátozásokat tartalmaz. Emellett a kérelmek szabályozásának elkerülésére vonatkozó ajánlott eljárásokat is tartalmaz. 

## <a name="quotas-and-limits-quick-reference"></a>Kvóták és korlátozások – rövid útmutató
Ugrás [szöveg – beszéd kvótára és korlátokra](#text-to-speech-quotas-and-limits-per-speech-resource)
### <a name="speech-to-text-quotas-and-limits-per-speech-resource"></a>Diktálási kvóták és Speech-erőforrásonkénti korlátok
Az alábbi táblázatokban az "állítható" sor nélkül **nem** állítható be az összes ár szintje.

#### <a name="online-transcription"></a>Online átírás
A [beszédfelismerési SDK](speech-sdk.md) -val és/vagy [beszéd-szöveg REST API rövid hangfelvételsel](rest-speech-to-text.md#speech-to-text-rest-api-for-short-audio).

| Kvóta | Ingyenes (F0)<sup>1</sup> | Standard (S0) |
|--|--|--|
| **Egyidejű kérelmek korlátja – alapmodell** | 1 | 100 (alapértelmezett érték) |
| Módosítható | Nem<sup>2</sup> | Igen<sup>2</sup> |
| **Egyidejű kérelmek korlátja – egyéni modell** | 1 | 20 (alapértelmezett érték) |
| Módosítható | Nem<sup>2</sup> | Igen<sup>2</sup> |

#### <a name="batch-transcription"></a>Kötegelt átírás
| Kvóta | Ingyenes (F0)<sup>1</sup> | Standard (S0) |
|--|--|--|
| [Beszéd – szöveg REST API v 2.0 és v 3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) korlát | A Batch-átírás nem érhető el a F0 | 300 kérelem percenként |
| Hangbeviteli fájl maximális mérete | N/A | 1 GB |
| A bemeneti Blobok maximális mérete (több fájlt is tartalmazhat, például egy ZIP-archívumban), ügyeljen arra, hogy a fenti fájlméretre vonatkozó korlátot jegyezze fel. | N/A | 2,5 GB |
| BLOB-tároló maximális mérete | N/A | 5 GB |
| Blobok maximális száma egy tárolóban | N/A | 10000 |
| Fájlok maximális száma átírási kérelem esetén (több tartalom URL-címének bemenetként való használata esetén) | N/A | 1000  |
| Egyszerre futó feladatok maximális száma | N/A | 2000  |

#### <a name="model-customization"></a>Modell testreszabása
| Kvóta | Ingyenes (F0)<sup>1</sup> | Standard (S0) |
|--|--|--|
| REST API korlát | 300 kérelem percenként | 300 kérelem percenként |
| A beszédfelismerési adatkészletek maximális száma | 2 | 500 |
| Az adatimportálás maximális akusztikai adatkészlet-fájlmérete | 2 GB | 2 GB |
| Maximális nyelvi adatkészlet fájlmérete adatimportáláshoz | 200 MB | 1,5 GB |
| A kiejtési adatkészlet maximális fájlmérete az adatimportáláshoz | 1 KB | 1 MB |
| Maximális szövegméret `text` a [create Model](https://westcentralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CreateModel/) API-kérelem paraméterének használatakor | 200 KB | 500 kB |

<sup>1</sup> az **ingyenes (F0)** díjszabási szinten a [díjszabási oldalon](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)a havi kedvezmények is szerepelnek.<br/>
<sup>2</sup> [további magyarázatok](#detailed-description-quota-adjustment-and-best-practices), [ajánlott eljárások](#general-best-practices-to-mitigate-throttling-during-autoscaling)és [beállítási utasítások](#speech-to-text-increasing-online-transcription-concurrent-request-limit).<br/> 

### <a name="text-to-speech-quotas-and-limits-per-speech-resource"></a>Szöveg-beszéd kvóták és a beszédfelismerési erőforrások korlátai
Az alábbi táblázatban az "állítható" sor nélküli paraméterek **nem** állíthatók be az összes ár szintjére.

| Kvóta                                                                          | Ingyenes (F0)<sup>3</sup>  | Standard (S0)   |
|--------------------------------------------------------------------------------|------------------------|-----------------|
| **A másodpercenkénti tranzakciók maximális száma (TPS) a standard és a neurális hangok esetében** | 200<sup>4</sup>        | 200<sup>4</sup> |
| **Egyéni hang egyidejű kérelmi korlátja**                                  |                        |                 |
| Alapértelmezett érték                                                                  | 10                     | 10              |
| Módosítható                                                                     | Nem<sup>5</sup>         | Igen<sup>5</sup> |
| **HTTP-specifikus kvóták**                                                       |                        |                 |
| Maximális hanghossz                                          | 10 perc                 | 10 perc          |
| Különálló címkék maximális száma `<voice>` a SSML                                  | 50                     | 50              |
| **WebSocket-specifikus kvóták**                                                  |                        |                 |
| Másodpercenként előállított maximális hanghossz                                             | 10 perc                 | 10 perc          |
| Maximális SSML üzenet mérete másodpercenként                                                 | 64 KB                  | 64 KB           |
| **REST API korlát**                                                             | 20 kérelem percenként | 300 kérelem percenként |


<sup>3</sup> az **ingyenes (F0)** díjszabási szinten a [díjszabási oldalon](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)a havi kedvezmények is szerepelnek.<br/>
<sup>4</sup> [további magyarázatok](#detailed-description-quota-adjustment-and-best-practices) és [ajánlott eljárások](#general-best-practices-to-mitigate-throttling-during-autoscaling).<br/>
<sup>5</sup> [további magyarázatok](#detailed-description-quota-adjustment-and-best-practices), [ajánlott eljárások](#general-best-practices-to-mitigate-throttling-during-autoscaling)és [beállítási utasítások](#text-to-speech-increasing-transcription-concurrent-request-limit-for-custom-voice).<br/> 

## <a name="detailed-description-quota-adjustment-and-best-practices"></a>Részletes leírás, kvóta-kiigazítás és ajánlott eljárások
A kvóta növelésének megkezdése előtt (ha van ilyen) ellenőrizze, hogy szükség van-e rá. A beszédfelismerési szolgáltatás automatikus skálázási technológiákat használ a szükséges számítási erőforrások "igény szerinti" módban történő megtartásához, és ugyanakkor az ügyfelek költségeinek alacsonyra csökkentése, ha nem tart fenn túlságosan nagy mennyiségű hardveres kapacitást. Minden alkalommal, amikor az alkalmazás megkapja a 429-as hibakódot ("túl sok kérést"), miközben a munkaterhelés a meghatározott határértékeken belül van (lásd: [kvóták és korlátok gyors hivatkozása](#quotas-and-limits-quick-reference)), a legvalószínűbb magyarázat, hogy a szolgáltatás az igény szerinti skálázásra van korlátozva, és még nem érte el a szükséges léptéket Ez az állapot általában átmeneti, és nem tarthat sokáig.

### <a name="general-best-practices-to-mitigate-throttling-during-autoscaling"></a>Általános ajánlott eljárások az automatikus skálázás során felmerülő szabályozás mérsékléséhez
A szabályozással kapcsolatos problémák minimalizálásához (429-es hibakód) a következő módszerek használatát javasoljuk:
- Újrapróbálkozási logika implementálása az alkalmazásban
- Kerülje a számítási feladatok éles változásait. Növelje a munkaterhelés fokozatos növelését <br/>
*Példa:* Az alkalmazás szöveg-beszédet használ, és az aktuális munkaterhelés 5 TPS (tranzakció/másodperc). A következő másodpercben a terhelést 20 TPS (ez négyszer több) értékre emeli. A szolgáltatás azonnal megkezdi a skálázást az új terhelés teljesítése érdekében, de valószínű, hogy egy másodpercen belül nem fogja tudni elvégezni, így néhány kérelem 429-as kódú választ kap.   
- Különböző terhelés-növelési minták tesztelése
  - Lásd: [beszéd – szöveg – példa](#speech-to-text-example-of-a-workload-pattern-best-practice)
- Hozzon létre további beszédfelismerési erőforrásokat ugyanabban vagy különböző régiókban, és ossza szét a számítási feladatokat a "ciklikus multiplexelés" módszer használatával. Ez különösen fontos a **TPS (tranzakciók másodpercenkénti száma)** paraméter esetében, amely a Speech erőforrás 200-as értéke, és nem módosítható.  

A következő szakaszok a kvóták módosításának konkrét eseteit ismertetik.<br/>
Ugrás [szövegről beszédre Az egyéni hangra vonatkozó átírások egyidejű kérelmi korlátjának növelése](#text-to-speech-increasing-transcription-concurrent-request-limit-for-custom-voice)

### <a name="speech-to-text-increasing-online-transcription-concurrent-request-limit"></a>Beszéd – szöveg: növekvő online átírási idejű kérelmek korlátozása
Alapértelmezés szerint az egyidejű kérelmek száma legfeljebb 20/Speech Resource (alapmodell) vagy egyéni végpont (egyéni modell) lehet. Standard szintű díjszabás esetén ez az összeg növelhető. A kérelem elküldése előtt győződjön meg arról, hogy ismeri az ebben a [szakaszban](#detailed-description-quota-adjustment-and-best-practices) található anyagokat, és ismerkedjen meg az [ajánlott eljárásokkal](#general-best-practices-to-mitigate-throttling-during-autoscaling).

Az egyidejű kérelmek korlátjának növelése **nem** befolyásolja közvetlenül a költségeit. A Speech Services "csak a ténylegesen használt funkciókért fizet" modellt használja. A korlát határozza meg, hogy a szolgáltatás milyen mértékben méretezhető, mielőtt megkezdi a kérelmek szabályozását.

Az **Alap** -és **Egyéni** modellek egyidejű kérelmi korlátait **külön** kell módosítani.

Az egyidejű kérelmek korlátja paraméter meglévő értéke **nem** látható Azure Portalon, Command-Line eszközökön vagy API-kérelmeken keresztül. A meglévő érték ellenőrzéséhez hozzon létre egy Azure-támogatási kérelmet.

>[!NOTE]
>A [beszédfelismerési tárolók](speech-container-howto.md) nem igénylik az egyidejű kérelmek korlátjának növelését, mivel a tárolókat csak az általuk üzemeltetett hardver processzorai korlátozzák. A beszédfelismerési tárolók azonban saját kapacitásbeli korlátozásokkal rendelkeznek, amelyeket figyelembe kell venni. Tekintse meg a "hasznos lehet a *kapacitás megtervezése és a költséghatékony tárolók becsült díja?"* című kérdést. a [Speech containers GYIK](./speech-container-howto.md)-ból.

#### <a name="have-the-required-information-ready"></a>Készítse elő a szükséges információkat:
- **Alapmodell** esetén:
  - Beszédfelismerési erőforrás azonosítója
  - Region
- **Egyéni modell** esetén: 
  - Region
  - Egyéni végpont azonosítója

- **Információk beszerzése (alapmodell)**:  
  - Ugrás a [Azure Portal](https://portal.azure.com/)
  - Válassza ki azt a Speech-erőforrást, amelyhez meg szeretné emelni a párhuzamossági kérelmek korlátját
  - *Tulajdonságok* kiválasztása (*erőforrás-felügyeleti* csoport) 
  - Másolja ki és mentse a következő mezők értékeit:
    - **Erőforrás-azonosító**
    - **Hely** (a végpont régiója)

- **Információk beszerzése (egyéni modell)**:
  - Ugrás a [Speech Studio](https://speech.microsoft.com/) portálra
  - Szükség esetén jelentkezzen be
  - Ugrás a Custom Speech
  - Projekt kiválasztása
  - Ugrás az *üzembe helyezésre*
  - Válassza ki a szükséges végpontot
  - Másolja ki és mentse a következő mezők értékeit:
    - **Szolgáltatási régió** (az Ön végponti régiója)
    - **Végpont azonosítója**
  
#### <a name="create-and-submit-support-request"></a>Támogatási kérelem létrehozása és elküldése
Kezdeményezheti az erőforrás egyidejű kérelmi korlátjának növelését, vagy ha szükséges, ellenőrizze a mai korlátot a támogatási kérelem elküldésével:

- Győződjön meg arról, hogy rendelkezik a [szükséges adatokkal](#have-the-required-information-ready)
- Ugrás a [Azure Portal](https://portal.azure.com/)
- Válassza ki azt a Speech-erőforrást, amelyhez meg szeretné emelni (vagy ellenőriznie kell) a párhuzamossági kérelmek korlátját
- *Új támogatási kérelem* kiválasztása (*támogatás + hibaelhárítási* csoport) 
- Megjelenik egy új ablak az Azure-előfizetéssel és az Azure-erőforrással kapcsolatos automatikusan feltöltött adatokkal
- Adja meg az *összegzést* (például "a STT párhuzamossági kérelmi korlátjának emelése")
- A *probléma típusa* területen válassza a "kvóta-vagy előfizetési problémák" lehetőséget.
- A megjelenő *probléma altípusban* válassza a következőt:
  - "Kvóta vagy egyidejű kérelmek növekedése" – növelési kérelem esetén
  - "Kvóta vagy használati ellenőrzés" a meglévő korlát ellenőrzéséhez
- Kattintson a *Tovább gombra: megoldások*
- Folytatás a kérések létrehozásával
- A *részletek* lap *Leírás* mezőjébe írja be a következőket:
  - egy megjegyzés, hogy a kérelem a **beszéd-szöveg** kvótára vonatkozik
  - **Alap** -vagy **Egyéni** modell
  - A [korábban összegyűjtött](#have-the-required-information-ready) Azure-erőforrások adatai 
  - Fejezze be a szükséges adatokat, majd kattintson a *Létrehozás* gombra a *felülvizsgálat + létrehozás* lapon.
  - Jegyezze fel Azure Portal értesítésekben szereplő támogatási kérelmek számát. A további feldolgozáshoz hamarosan felveszi a kapcsolatot

### <a name="speech-to-text-example-of-a-workload-pattern-best-practice"></a>Beszéd – szöveg: példa a számítási feladatok mintájának ajánlott eljárására
Ez a példa azt a módszert mutatja be, amelyet a következők ajánlottak a lehetséges kérelmek szabályozásának enyhítésére, mert folyamatban van az automatikus [skálázás](#detailed-description-quota-adjustment-and-best-practices). Ez nem egy "pontos recept", hanem csupán egy sablon, amelyet meg kell hívni, és szükség szerint módosítani kell.

Tegyük fel, hogy egy beszédfelismerési erőforráshoz az egyidejű kérelem korlátja 300 értékre van állítva. Indítsa el a munkaterhelést 20 egyidejű kapcsolaton, és növelje a terhelést 20 egyidejű kapcsolaton keresztül, 1,5 – 2 percenként. Szabályozza a szolgáltatás válaszait, és alkalmazza a visszaadott logikát (csökkenti a terhelést), ha túl sok 429-as hibakódot kap. Ezután próbálkozzon újra 1-2-4-4 perces mintázattal. (Ez azt eredményezi, hogy a terhelés 1 percen belül növekszik, ha még mindig nem működik, majd 2 perc múlva stb.)

Általában erősen ajánlott tesztelni a munkaterhelést és a számítási feladatok mintáit az éles környezetben való üzembe helyezés előtt.

### <a name="text-to-speech-increasing-transcription-concurrent-request-limit-for-custom-voice"></a>Szöveg – beszéd: az egyéni hangra vonatkozó egyidejű kérelmek maximális száma
Alapértelmezés szerint az egyéni hangvégpontok egyidejű kérelmeinek száma legfeljebb 10 lehet. Standard szintű díjszabás esetén ez az összeg növelhető. A kérelem elküldése előtt győződjön meg arról, hogy ismeri az ebben a [szakaszban](#detailed-description-quota-adjustment-and-best-practices) található anyagokat, és ismerkedjen meg az [ajánlott eljárásokkal](#general-best-practices-to-mitigate-throttling-during-autoscaling).

Az egyidejű kérelmek korlátjának növelése **nem** befolyásolja közvetlenül a költségeit. A Speech Services "csak a ténylegesen használt funkciókért fizet" modellt használja. A korlát határozza meg, hogy a szolgáltatás milyen mértékben méretezhető, mielőtt megkezdi a kérelmek szabályozását.

Az egyidejű kérelmek korlátja paraméter meglévő értéke **nem** látható Azure Portalon, Command-Line eszközökön vagy API-kérelmeken keresztül. A meglévő érték ellenőrzéséhez hozzon létre egy Azure-támogatási kérelmet.

>[!NOTE]
>A [beszédfelismerési tárolók](speech-container-howto.md) nem igénylik az egyidejű kérelmek korlátjának növelését, mivel a tárolókat csak az általuk üzemeltetett hardver processzorai korlátozzák.

#### <a name="prepare-the-required-information"></a>A szükséges információk előkészítése:
A növelési kérelem létrehozásához meg kell adnia az üzembe helyezési régiót és az egyéni végpont AZONOSÍTÓját. A beszerzéséhez hajtsa végre a következő műveleteket: 

- Ugrás a [Speech Studio](https://speech.microsoft.com/) portálra
- Szükség esetén jelentkezzen be
- Ugrás az *Egyéni hangra*
- Projekt kiválasztása
- Ugrás az *üzembe helyezésre*
- Válassza ki a szükséges végpontot
- Másolja ki és mentse a következő mezők értékeit:
    - **Szolgáltatási régió** (az Ön végponti régiója)
    - **Végpont azonosítója**
  
#### <a name="create-and-submit-support-request"></a>Támogatási kérelem létrehozása és elküldése
Kezdeményezheti az erőforrás egyidejű kérelmi korlátjának növelését, vagy ha szükséges, ellenőrizze a mai korlátot a támogatási kérelem elküldésével:

- Győződjön meg arról, hogy rendelkezik a [szükséges adatokkal](#prepare-the-required-information)
- Ugrás a [Azure Portal](https://portal.azure.com/)
- Válassza ki azt a Speech-erőforrást, amelyhez meg szeretné emelni (vagy ellenőriznie kell) a párhuzamossági kérelmek korlátját
- *Új támogatási kérelem* kiválasztása (*támogatás + hibaelhárítási* csoport) 
- Megjelenik egy új ablak az Azure-előfizetéssel és az Azure-erőforrással kapcsolatos automatikusan feltöltött adatokkal
- Adja meg az *összegzést* (például "a TTS egyéni végpont párhuzamossági kérelmének korlátozása")
- A *probléma típusa* területen válassza a "kvóta-vagy előfizetési problémák" lehetőséget.
- A megjelenő *probléma altípusban* válassza a következőt:
  - "Kvóta vagy egyidejű kérelmek növekedése" – növelési kérelem esetén
  - "Kvóta vagy használati ellenőrzés" a meglévő korlát ellenőrzéséhez
- Kattintson a *Tovább gombra: megoldások*
- Folytatás a kérések létrehozásával
- A *részletek* lap *Leírás* mezőjébe írja be a következőket:
  - egy megjegyzés, hogy a kérelem a **szöveg és a beszéd közötti** kvótára vonatkozik
  - A [korábban összegyűjtött](#prepare-the-required-information) Azure-erőforrások adatai 
  - Fejezze be a szükséges adatokat, majd kattintson a *Létrehozás* gombra a *felülvizsgálat + létrehozás* lapon.
  - Jegyezze fel Azure Portal értesítésekben szereplő támogatási kérelmek számát. A további feldolgozáshoz hamarosan felveszi a kapcsolatot