---
title: Microsoft OPC-közzétevő teljesítmény-és memória-finomhangolása
description: Ebből az oktatóanyagból megtudhatja, hogyan hangolhatja be az OPC-közzétevő teljesítményét és memóriáját.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 89e288d1186efd405019d6474dcbd332e7925d67
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787732"
---
# <a name="tutorial-tune-the-opc-publisher-performance-and-memory"></a>Oktatóanyag: az OPC-közzétevő teljesítményének és memóriájának hangolása

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A teljesítmény módosítása
> * Az üzenet folyamatának módosítása a memória erőforrásaira

Az OPC-közzétevő éles környezetben való futtatásakor figyelembe kell venni a hálózati teljesítményre vonatkozó követelményeket (átviteli sebességet és késést) és a memória-erőforrásokat. Az OPC-közzétevő a következő parancssori paramétereket teszi elérhetővé a követelmények teljesítéséhez:

* Az üzenetsor kapacitása ( `mq` 2,5-es és újabb verzió esetén nem érhető el az 2,6-es verzióban az 2,7-es verzió esetében `om` )
* IoT Hub küldési időköz ( `si` )
* IoT Hub üzenet mérete ( `ms` )

## <a name="adjusting-iot-hub-send-interval-and-iot-hub-message-size"></a>IoT Hub küldési időköz és IoT Hub üzenet méretének módosítása

A `mq/om` paraméter szabályozza a belső üzenet-várólista kapacitásának felső határát. Ez a várólista a IoT Hubba való küldés előtt minden üzenetet pufferbe helyez. A várólista alapértelmezett mérete legfeljebb 2 MB az OPC-közzétevő 2,5-es vagy újabb verziójában, illetve 4000 IoT Hub az 2,7-as verziónál (azaz ha a IoT Hub üzenet méretének értéke 256 KB, a várólista mérete legfeljebb 1 GB lesz). Ha az OPC-közzétevő nem tud üzeneteket küldeni IoT Hub elég gyorsan, az ebben a várólistában lévő elemek száma nő. Ha ez a tesztek futtatásakor történik, a következők közül egyet vagy mindkettőt el lehet végezni a enyhítése érdekében:

* IoT Hub küldési időköz ( `si` ) csökkentése

* növelje a IoT Hub üzenet méretét ( `ms` a maximális érték 256 kb lehet)

Ha az üzenetsor továbbra is növekszik, bár a `si` és a `ms` Paraméterek be lettek állítva, a rendszer végül eléri a maximális várólista-kapacitást, és az üzenetek elvesznek. Ennek az az oka, hogy mind a, mind a `si` `ms` paraméternek van fizikai korlátja, és az OPC-közzétevő és a IoT hub közötti internetkapcsolat nem elég gyors ahhoz, hogy hány üzenetet kell elküldeni egy adott forgatókönyvben. Ebben az esetben a rendszer csak több, párhuzamos OPC-közzétevő beállítását segíti. A `mq/om` paraméter a legnagyobb hatással van az OPC-közzétevő által felhasználható memória-felhasználásra is. 

A `si` paraméter arra kényszeríti az OPC-közzétevőt, hogy a megadott időközönként üzeneteket küldjön a IoT hubnak. A rendszer akkor küld üzenetet, ha a maximális IoT Hub üzenet mérete (256 KB) elérhető (a küldési időközt az alaphelyzetbe állítja), vagy ha a megadott időközi idő eltelt.

A `ms` paraméter lehetővé teszi a IoT hubba küldött üzenetek feldolgozását. A legtöbb hálózati beállítás esetében az egyetlen üzenet küldésének késése az IoT Hub számára magas, az adattartalom továbbításához szükséges időtartamhoz képest. Ez főleg a szolgáltatásminőség (QoS) követelményeinek köszönhető, mivel az üzeneteket csak egyszer fogadja el a rendszer, ha IoT Hub). Ezért ha az IoT Hub elfogadható az adatelérési idő, az OPC-közzétevőt úgy kell konfigurálni, hogy az 256 KB-os maximális méretű üzenetet használja a paraméter 0 értékre való beállításával `ms` . Emellett az OPC-közzétevő használatának leghatékonyabb módja.

Az alapértelmezett konfiguráció 10 másodpercenként küldi az adatokat IoT Hub ( `si=10` ), vagy ha rendelkezésre áll a 256 KB IoT hub üzenet ( `ms=0` ). Ez legfeljebb 10 másodpercet vesz igénybe, de a nagyméretű üzenetek mérete miatt kevés a valószínűsége az adatvesztésnek. Az `monitored item notifications enqueue failure`  OPC-közzétevő 2,5-es és újabb verziója, valamint `messages lost` az OPC-közzétevő 2,7-es verziójában látható metrika azt mutatja, hogy hány üzenet elvész.

Ha a kettő `si` és a `ms` paraméterek értéke 0, az OPC-közzétevő IoT hub azonnal küld üzenetet, amint az adatok rendelkezésre állnak. Ennek eredményeképpen átlagosan IoT Hub üzenet mérete meghaladja a 200 bájtot. Azonban ennek a konfigurációnak az előnye, hogy az OPC-közzétevő késedelem nélkül elküldi az adatokat a csatlakoztatott eszközről. Az elveszett üzenetek száma magas lesz olyan használati esetekben, amikor nagy mennyiségű adattal kell közzétenni őket, és ezért ez nem ajánlott ezekhez a forgatókönyvekhez.

Az OPC-közzétevő teljesítményének méréséhez a `di` paraméter használatával kinyomtathatja a teljesítménymutatókat a nyomkövetési naplóba a megadott intervallumban (másodpercben).

## <a name="next-steps"></a>Következő lépések
Most, hogy megismerte, hogyan hangolhatja be az OPC-közzétevő teljesítményét és memóriáját, további forrásokért tekintse meg az OPC-közzétevő GitHub-tárházát:

> [!div class="nextstepaction"]
> [OPC-közzétevő GitHub-tárháza](https://github.com/Azure/Industrial-IoT)