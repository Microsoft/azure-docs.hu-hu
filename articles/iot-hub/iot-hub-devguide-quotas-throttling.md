---
title: Az Azure IoT Hub kvótáinak és szabályozásának megismerése | Microsoft Docs
description: Fejlesztői útmutató – a IoT Hubra és a várható szabályozási viselkedésre vonatkozó kvóták leírása.
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/18/2021
ms.custom:
- 'Role: Cloud Development'
- 'Role: Operations'
- 'Role: Technical Support'
ms.openlocfilehash: 4b65d42522f40eb7d0e65356223313a924de3039
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104656991"
---
# <a name="reference---iot-hub-quotas-and-throttling"></a>Hivatkozás – IoT Hub kvóták és szabályozás

Ez a cikk egy IoT Hub kvótáit ismerteti, és információt nyújt a szabályozás működésének megismeréséhez.

## <a name="quotas-and-throttling"></a>Kvóták és szabályozás

Minden Azure-előfizetés legfeljebb 50 IoT-hubhoz és legfeljebb 1 ingyenes központtal rendelkezhet.

Üzembe helyezéskor minden IoT hub bizonyos számú egységet tartalmaz egy adott szinten. Az egységek szintje és száma határozza meg az elküldhető üzenetek maximális napi kvótáját. A napi kvóta kiszámításához használt üzenet mérete 0,5 KB az ingyenes szintű hub-hoz és az összes többi 4KB. További információ: [Azure IoT hub díjszabása](https://azure.microsoft.com/pricing/details/iot-hub/).

A (z) szinten meghatározza azokat a szabályozási korlátokat is, amelyek az összes műveletre IoT Hub érvényesítenek.

## <a name="iot-plug-and-play"></a>IoT Plug and Play

A IoT Plug and Play-eszközök legalább egy telemetria-üzenetet küldenek minden egyes csatolóhoz, beleértve a gyökeret is, ami növelheti az üzenetek számának megszámlálását az üzenet kvótája felé.

## <a name="operation-throttles"></a>Műveletek szabályozása

A műveletek szabályozása a percenkénti tartományokra érvényes, és a visszaélések megelőzésére szolgál. A [forgalom formálása](#traffic-shaping)is tárgya.

A következő táblázat a kényszerített szabályozásokat mutatja be. Az értékek egy adott hubhoz vonatkoznak.

| Szabályozás | Ingyenes, B1 és S1 | B2 és S2 | B3 és S3 | 
| -------- | ------- | ------- | ------- |
| [Identitás-beállításjegyzék műveletei](#identity-registry-operations-throttle) (létrehozás, lekérés, Listázás, frissítés, törlés) | 1.67/mp/egység (100/perc/egység) | 1.67/mp/egység (100/perc/egység) | 83.33/mp/egység (5000/perc/egység) |
| [Új eszközök kapcsolatai](#device-connections-throttle) (ez a korlát az _új kapcsolatok_ arányára vonatkozik, nem a kapcsolatok teljes száma) | Nagyobb, mint 100/mp vagy 12/MP/egység <br/> A két S1 egység például 2 \* 12 = 24 új kapcsolat/mp, de legalább 100 új kapcsolat/MP van az egységen belül. Kilenc S1 egység esetén a teljes egységen 108 új kapcsolat/mp (9 \* 12). | 120 új kapcsolat/mp/egység | 6 000 új kapcsolat/mp/egység |
| Az eszközről a felhőbe irányuló küldések | Magasabb 100 küldési művelet/mp vagy 12 küldési művelet/mp/egység <br/> A két S1 egység például 2 \* 12 = 24/MP, de legalább 100-as küldési művelet/mp egységben. Kilenc S1 egység esetén 108 küldési művelet/mp (9 \* 12) van az egységeken belül. | 120 küldési művelet/mp/egység | 6 000 küldési művelet/mp/egység |
| A felhőből az eszközre küldött<sup>1</sup> | 1,67 küldési művelet/mp/egység (100 üzenet/perc/egység) | 1,67 küldési művelet/mp/egység (100 küldési művelet/perc/egység) | 83,33 küldési művelet/mp/egység (5 000 küldési művelet/perc/egység) |
| A felhőből az eszközre kapott<sup>1</sup> <br/> (csak akkor, ha az eszköz HTTPS protokollt használ)| 16,67 fogadási művelet/mp/egység (1 000 fogadási művelet/perc/egység) | 16,67 fogadási művelet/mp/egység (1 000 fogadási művelet/perc/egység) | 833,33 fogadási művelet/mp/egység (50 000 fogadási művelet/perc/egység) |
| Fájlfeltöltés | 1,67 fájlfeltöltés-kezdeményezés/mp/egység (100/perc/egység) | 1,67 fájlfeltöltés-kezdeményezés/mp/egység (100/perc/egység) | 83,33 fájlfeltöltés-kezdeményezés/mp/egység (5000/perc/egység) |
| Közvetlen metódusok<sup>1</sup> | 160KB/mp/<sup>2</sup> . egység | 480KB/mp/<sup>2</sup> . egység | 24MB/mp/<sup>2</sup> . egység | 
| Lekérdezések | 20/perc/egység | 20/perc/egység | 1000/perc/egység |
| Twin (eszköz és modul) –<sup>1</sup> | 100 másodpercenként | Nagyobb, mint 100/mp vagy 10/mp/egység | 500/mp/egység |
| Twin Updates (eszköz és modul)<sup>1</sup> | 50 másodpercenként | Nagyobb, mint 50/mp vagy 5/MP/egység | 250/mp/egység |
| Feladatok műveletei<sup>1</sup> <br/> (létrehozás, frissítés, listázás, törlés) | 1.67/mp/egység (100/perc/egység) | 1.67/mp/egység (100/perc/egység) | 83.33/mp/egység (5000/perc/egység) |
| Feladatok Device Operations<sup>1</sup> <br/> (Update Twin, Direct metódus meghívása) | 10 másodpercenként | Nagyobb, mint 10/mp vagy 1/MP/egység | 50/mp/egység |
| Konfigurációk és Edge-telepítések<sup>1</sup> <br/> (létrehozás, frissítés, listázás, törlés) | 0.33/mp/egység (20/perc/egység) | 0.33/mp/egység (20/perc/egység) | 0.33/mp/egység (20/perc/egység) |
| Az eszköz stream-kezdeményezési sebessége<sup>1</sup> | 5 új stream/mp | 5 új stream/mp | 5 új stream/mp |
| Egyidejű csatlakoztatott adatfolyamok maximális száma<sup>1</sup> | 50 | 50 | 50 |
| Az eszköz adatfolyam-továbbításának maximális száma<sup>1</sup> (naponta összesített kötet) | 300 MB | 300 MB | 300 MB |

<sup>1</sup> Ez a funkció nem érhető el az alapszintű IoT Hub. További információ: [How to Choo the right IoT hub](iot-hub-scaling.md). <br/><sup>2</sup> A szabályozási mérési méret 4 KB. A szabályozás csak a kérelmek hasznos mérete alapján történik.

### <a name="throttling-details"></a>Szabályozás részletei

* A mérőszám mérete határozza meg, hogy a rendszer milyen növekményeket használ a sávszélesség-szabályozási korláton. Ha a közvetlen hívás tartalma 0 és 4 KB közé esik, a függvény 4 KB-ként számít. Egységenként legfeljebb 40 hívást hozhat létre másodpercenként, a 160 KB/s/s/egység korlátjának megkezdése előtt.

   Hasonlóképpen, ha a hasznos adatok 4 KB-os és 8 KB-os értékre vannak beállítva, minden hívási fiók 8 KB-nál több másodpercenként 20 hívást tesz elérhetővé, mielőtt eléri a maximális korlátot.

   Végül, ha a hasznos adatok mérete 156KB és 160 KB között van, akkor az adott egységen belüli egységenként csak 1 hívást lehet megtenni, mielőtt eléri a 160 KB/s/s értéket.

*  A *feladatok eszköz műveleteihez (a Twin, a Direct metódus meghívása)* az S3. szinten a 50/mp/Unit művelet csak akkor érvényes, ha a metódusokat feladatok használatával indítja el. Ha közvetlenül hívja meg a közvetlen metódusokat, a rendszer az eredeti sávszélesség-szabályozási korlátot (az S3 esetében) alkalmazza.

*  A **kvóta** a központba naponta küldendő üzenetek összesített *száma.* A hub kvótájának korlátját a [IoT hub díjszabási oldalán](https://azure.microsoft.com/pricing/details/iot-hub/)található **üzenetek teljes száma** oszlopban tekintheti meg.

*  A felhőből az eszközre és az eszközről a felhőbe irányuló szabályozások határozzák meg az üzenetek küldésének maximális *sebességét* – a 4 KB-os adattömböktől függetlenül. Az üzenetek [maximális mérete](iot-hub-devguide-quotas-throttling.md#other-limits)legfeljebb 256 kb lehet.

*  A hívások szabályozása jó megoldás, így nem kell meghaladnia a sávszélesség-szabályozási korlátokat. Ha elérte a korlátot, IoT Hub válaszol a 429 hibakódra, és az ügyfélnek vissza kell térnie, és újra kell próbálkoznia. Ezek a korlátok a következők: hub (vagy bizonyos esetekben egy hubhoz/egységenként). További információt a [kapcsolat kezelése és a megbízható üzenetkezelési/újrapróbálkozási minták](iot-hub-reliability-features-in-sdks.md#retry-patterns)című témakörben talál.

### <a name="traffic-shaping"></a>Traffic Shaping

A burst adatforgalom befogadásához IoT Hub a szabályozáson felüli kérelmeket korlátozott időre fogadja el. A kérések első néhány feldolgozása azonnal megtörténik. Ha azonban a kérelmek száma továbbra is sérti a szabályozást, IoT Hub elindítja a kérelmek várólistára helyezését, és feldolgozza a határértéket. Ezt a hatást nevezzük *Traffic Shaping*-nek. Emellett a várólista mérete korlátozott. Ha a szabályozás megsértése folytatódik, végül a várólista betöltődik, és IoT Hub elindítja a kérelmek elutasítását `429 ThrottlingException` .

Például egy szimulált eszköz használatával 200 eszközről a felhőbe irányuló üzeneteket küldhet másodpercenként az S1 IoT Hub (amely legfeljebb 100 másodpercenkénti D2C küld). Az első vagy két percnél az üzenetek feldolgozása azonnal megtörténik. Mivel azonban az eszköz továbbra is több üzenetet küld a szabályozási korlátnál, IoT Hub megkezdi a másodpercenkénti 100-üzenetek feldolgozását, és a REST-t egy várólistán helyezi el. A megnövelt késés megkezdése. Végül elkezdi `429 ThrottlingException` az üzenetsor betöltését, és a ["sávszélesség-szabályozási hibák száma" IoT hub metrika](monitor-iot-hub-reference.md#device-telemetry-metrics) megkezdi a növekedést. A riasztások és diagramok mérőszámok alapján történő létrehozásával kapcsolatos további információkért lásd: [IoT hub figyelése](monitor-iot-hub.md).

### <a name="identity-registry-operations-throttle"></a>Identitás-beállításjegyzék működési szabályozása

Az eszköz-identitás beállításjegyzékbeli műveletei az eszközkezelés és a kiépítési forgatókönyvek futtatására szolgálnak. A nagy számú eszköz identitásának olvasása vagy frissítése az [importálási és exportálási feladatok](iot-hub-devguide-identity-registry.md#import-and-export-device-identities)használatával támogatott.

Ha a [tömeges beállításjegyzék-frissítési műveleteken](https://docs.microsoft.com/rest/api/iothub/service/bulkregistry/updateregistry) (*nem* tömeges importálási és exportálási feladatok) keresztül indítja el az identitási műveleteket, ugyanazok a szabályozási korlátozások érvényesek. Ha például az 50-es eszközök létrehozásához tömeges műveletet szeretne küldeni, és 1 egységgel rendelkező S1-IoT Hub rendelkezik, akkor percenként csak kettőt fogad el. Ez azért történt, mert egy S1-es IoT Hubhoz tartozó Identity Operation szabályozása 1 egységgel 100/perc/egység. Ebben az esetben a rendszer visszautasítja a harmadik kérelmet (és azon kívül is), hogy a korlát már el lett utasítva. 

### <a name="device-connections-throttle"></a>Eszközök kapcsolatainak szabályozása

Az *eszközök kapcsolatainak* szabályozása szabályozza, hogy az új eszközök kapcsolatai milyen mértékben hozhatók IoT hubhoz. Az *eszközök kapcsolatainak* szabályozása nem szabályozza az egyidejű csatlakoztatott eszközök maximális számát. Az *eszköz kapcsolatainak* szabályozása az IoT hub számára kiépített egységek számától függ.

Ha például egyetlen S1 egységet vesz igénybe, akkor a másodpercenkénti 100-kapcsolatok szabályozása is elérhető. Ezért az 100 000-es eszközök csatlakoztatásához legalább 1 000 másodpercre van szüksége (körülbelül 16 perc). Ugyanakkor egyszerre több csatlakoztatott eszközt is megadhat, ahogy az identitás-beállításjegyzékben regisztrálva van az eszközökön.

## <a name="other-limits"></a>Egyéb korlátok

IoT Hub érvényesíti a többi működési korlátot:

| Művelet | Korlát |
| --------- | ----- |
| Eszközök | Az egyetlen IoT hub-ban regisztrálható eszközök és modulok száma 1 000 000. A korlát növelésének egyetlen módja a [Microsoft ügyfélszolgálatahoz](https://azure.microsoft.com/support/options/)való kapcsolódás.|
| Fájlfeltöltés | 10 egyidejű fájl feltöltése eszközönként. |
| <sup>1</sup> . feladatok | Az egyidejű feladatok maximális száma 1 (ingyenes és S1), 5 (S2 esetén) és 10 (S3 esetén). Az egyidejű [eszköz-importálási/exportálási feladatok](iot-hub-bulk-identity-mgmt.md) maximális száma azonban az összes szinten 1. <br/>A feladatok előzményeit 30 napig őrzi meg a rendszer. |
| További végpontok | A fizetős SKU-hubok 10 további végponttal rendelkezhetnek. Az ingyenes SKU-hubok egy további végponttal rendelkezhetnek. |
| Üzenet-útválasztási lekérdezések | A fizetős SKU-hubok 100-es útválasztási lekérdezéssel rendelkezhetnek. Előfordulhat, hogy az ingyenes SKU-hubok öt útválasztási lekérdezéssel rendelkeznek. |
| Üzenetbővítések | A fizetős SKU-hubok legfeljebb 10 üzenet-bővítéssel rendelkezhetnek. Az ingyenes SKU-hubok legfeljebb 2 üzenet-dúsítást tartalmazhatnak.|
| Eszközről a felhőbe irányuló üzenetkezelés | Az üzenet maximális mérete 256 KB |
| A felhőből az eszközre irányuló üzenetkezelés<sup>1</sup> | Az üzenet maximális mérete 64 KB. A kézbesítésre váró üzenetek maximális száma 50 eszközönként. |
| <sup>1</sup> . közvetlen módszer | A közvetlen metódusok maximális hasznos mérete 128 KB. |
| Automatikus eszköz-és modul-konfigurációk<sup>1</sup> | 100 konfiguráció/fizetős SKU-hub. 20 konfiguráció egy ingyenes SKU-hubhoz. |
| Automatikus központi telepítések IoT Edge<sup>1</sup> | 50 modul üzembe helyezése. 100 üzemelő példányok (beleértve a rétegzett központi telepítéseket) fizetett SKU-hubhoz. 10 üzemelő példány ingyenes SKU-hubhoz. |
| Ikrek<sup>1</sup> | A kívánt tulajdonságok maximális mérete és a jelentett tulajdonságok rész 32 KB. A címkék maximális mérete 8 KB. |
| Megosztott elérési szabályzatok | A megosztott hozzáférési házirendek maximális száma 16. |
| x509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok | A IoT Hubban regisztrálható x509 HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok maximális száma 25. |

<sup>1</sup> Ez a funkció nem érhető el az alapszintű IoT Hub. További információ: [How to Choo the right IoT hub](iot-hub-scaling.md).

## <a name="increasing-the-quota-or-throttle-limit"></a>A kvóta vagy a szabályozás korlátjának növelése

Egy adott időpontban növelheti a kvótákat és a szabályozási korlátokat azáltal, hogy [növeli a kiépített egységek számát egy IoT hub-ban](iot-hub-upgrade.md).

## <a name="latency"></a>Késés

IoT Hub arra törekszik, hogy alacsony késést biztosítson az összes művelethez. Azonban a hálózati feltételek és az egyéb előre nem látható tényezők miatt nem garantálható bizonyos késés. A megoldás tervezésekor a következőket kell tennie:

* Kerülje a IoT Hub műveletek maximális késésével kapcsolatos feltételezéseket.
* Az IoT hub kiépítése az eszközökhöz legközelebb eső Azure-régióban.
* Érdemes lehet a Azure IoT Edge használatával késésre érzékeny műveleteket végezni az eszközön vagy az eszközhöz közel lévő átjárón.

Több IoT Hub egység is befolyásolja a szabályozást a korábban leírtak szerint, de nem biztosít további késési előnyöket vagy garanciákat.

Ha a művelet késése váratlan növekedést lát, forduljon a [Microsoft ügyfélszolgálatahoz](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Következő lépések

IoT Hub szabályozási viselkedés részletes ismertetését a következő blogbejegyzésben találja: [IoT hub szabályozás és Ön](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/).

A IoT Hub Fejlesztői útmutatóban szereplő további témakörök a következők:

* [IoT Hub-végpontok](iot-hub-devguide-endpoints.md)
* [Az IoT Hub monitorozása](monitor-iot-hub.md)
