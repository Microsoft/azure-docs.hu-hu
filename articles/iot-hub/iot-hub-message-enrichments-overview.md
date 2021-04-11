---
title: Az Azure IoT Hub üzenet-gazdagítás áttekintése
description: Ez a cikk az üzenetek bővítéseit jeleníti meg, amelyek lehetővé teszik a IoT Hub számára, hogy az üzeneteket a kijelölt végpontnak való küldés előtt további információkkal lehessen lepecsételni.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: 10e999a7f0662c421d73872448506a9c9ca05975
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106079029"
---
# <a name="message-enrichments-for-device-to-cloud-iot-hub-messages"></a>Üzenetek bővítése az eszközről a felhőbe IoT Hub üzenetekhez

Az *üzenet-gazdagítás* a IoT hub azon képessége, hogy az üzeneteket a kijelölt végpontnak küldött üzenetek elküldése előtt további információkkal lehessen *lepecsételni* . Az üzenetek dúsításának használatának egyik oka az, hogy olyan adathalmazt tartalmazzon, amely az alsóbb rétegbeli feldolgozás egyszerűsítésére használható. Például az eszközök telemetria-üzeneteinek az eszközökhöz való bővítésével csökkentheti az ügyfelek terhelését, így az eszközökhöz tartozó Twin API-hívásokat is megteheti az adatokhoz.

![Üzenet-dúsítási folyamat](./media/iot-hub-message-enrichments-overview/message-enrichments-flow.png)

Az üzenetek dúsításának három fő eleme van:

* Dúsítási név vagy kulcs

* Egy érték

* Egy vagy több olyan [végpont](iot-hub-devguide-endpoints.md) , amelyhez a dúsítást alkalmazni kell.

A **kulcs** egy karakterlánc. A kulcsok csak alfanumerikus karaktereket vagy a következő speciális karaktereket tartalmazhatják: kötőjel ( `-` ), aláhúzás ( `_` ) és pont ( `.` ).

Az **érték** az alábbi példák bármelyike lehet:

* Bármilyen statikus karakterlánc. A dinamikus értékek, például a feltételek, a logika, a műveletek és a függvények nem engedélyezettek. Ha például olyan SaaS-alkalmazást fejleszt ki, amelyet számos ügyfél használ, hozzárendelhet egy azonosítót az egyes ügyfelekhez, és elérhetővé teheti az azonosítót az alkalmazásban. Az alkalmazás futtatásakor a IoT Hub lepecsételi az eszköz telemetria az ügyfél azonosítójával, így az egyes ügyfeleknél különbözőképpen dolgozhat fel az üzeneteket.

* Az üzenetet küldő IoT hub neve. Ez az érték *$iothubname*.

* Az eszköz különálló információi, például annak elérési útja. Ilyenek például a következők: *$Twin. Tags. Field* és *$Twin. Tags. Latitude*.

   > [!NOTE]
   > Jelenleg csak a $iothubname, a $twin. Tags, a $twin. properties. desired és a $twin. properties. jelentett változók az üzenetek dúsításának támogatott változói.

Az üzenetek gazdagítása alkalmazás-tulajdonságokként történik a kiválasztott végpont (ok) ba küldött üzenetekhez.  

## <a name="applying-enrichments"></a>Dúsítások alkalmazása

Az üzenetek bármilyen, [IoT hub üzenet-útválasztás](iot-hub-devguide-messages-d2c.md)által támogatott adatforrásból származhatnak, beleértve az alábbi példákat is:

* eszköz telemetria, például hőmérséklet vagy nyomás
* eszköz kettős változásával kapcsolatos értesítések – az eszköz kettős változásai
* az eszköz életciklusával kapcsolatos események, például az eszköz létrehozásakor vagy törlésekor

Bővítheti az olyan üzenetekhez tartozó bővítéseket, amelyek a IoT Hub beépített végpontján, illetve az olyan üzeneteknél, amelyek az Azure Blob Storage-hoz, egy Service Bus-várólistához vagy egy Service Bus témakörhöz vannak irányítva.

A Event Grid közzétett üzenetekhez bővítéseket adhat hozzá, ha Event Gridként kiválasztja a végpontot. A Event Grid-előfizetés alapján létrehozunk egy alapértelmezett útvonalat IoT Hub az eszköz telemetria. Ez az egyetlen útvonal képes kezelni az összes Event Grid-előfizetését. Az Event Grid-végpont dúsítását az Event Grid-előfizetés az eszköz telemetria való létrehozása után is konfigurálhatja. További információ: [IOT hub és Event Grid](iot-hub-event-grid.md).

A dúsítások egy végponton vannak alkalmazva. Ha öt dúsítást ad meg egy adott végpontra vonatkozóan, a végponthoz tartozó összes üzenetet ugyanazzal az öt gazdagítva kell lepecsételni.

A dúsítást a következő módszerekkel lehet konfigurálni:

| **Metódus** | **Parancs** |
| ----- | -----| 
| Portál | [Azure Portal](https://portal.azure.com) Lásd az [üzenet gazdagítása oktatóanyagot](tutorial-message-enrichments.md) | 
| Azure CLI   | [az IOT hub Message-gazdagodás](/cli/azure/iot/hub/message-enrichment) |
| Azure PowerShell | [Add-AzIotHubMessageEnrichment](/powershell/module/az.iothub/add-aziothubmessageenrichment) |

Az üzenet-bővítések hozzáadása nem jár késéssel az üzenet-útválasztáshoz.

Az üzenetek dúsításának kipróbálásához tekintse meg az [üzenet gazdagítása című oktatóanyagot](tutorial-message-enrichments.md) .

## <a name="limitations"></a>Korlátozások

* A standard vagy az alapszintű csomag esetében IoT Hub akár 10 dúsítást is hozzáadhat az adott hubokhoz. Az ingyenes szinten található IoT-huboknál legfeljebb 2 dúsítást adhat hozzá.

* Bizonyos esetekben, ha olyan értékkel rendelkező dúsítást alkalmaz, amely az eszköz Twin címkére vagy tulajdonságára van beállítva, akkor az érték karakterlánc-értékként lesz lepecsételve. Ha például a dúsítás értéke $twin. Tags. Field, az üzenetek a "$twin. Tags. Field" karakterlánccal lesznek lepecsételve, és nem az adott mező értékét a Twin-ből. Ez a következő esetekben fordul elő:

   * Az IoT Hub alapszintű. Az alapszintű IoT hubok nem támogatják az eszközökhöz tartozó ikreket.

   * A IoT Hub a standard szinten van, de az üzenetet küldő eszközön nincs külön eszköz.

   * A IoT Hub a standard szinten van, de a dúsítás értékéhez használt eszköz kettős elérési útja nem létezik. Ha például a dúsítás értéke $twin. Tags. location, és az eszköz Twin nem rendelkezik a címkék területen található Location tulajdonsággal, az üzenet a "$twin. Tags. location" karakterláncra van lepecsételve. 

* A két eszköz frissítései akár öt percet is igénybe vehetnek a megfelelő alkoholtartalom-növelési értékben.

* Az üzenetek teljes mérete, beleértve a dúsítást, nem lépheti túl a 256 KB-ot. Ha az üzenet mérete meghaladja az 256 KB-ot, akkor a IoT Hub el fogja dobni az üzenetet. Az üzenetek eldobásakor a [IoT hub metrikák](monitor-iot-hub-reference.md#metrics) használatával azonosíthatók és hibakeresési hibák jelentkezhetnek. Figyelheti például, hogy a *telemetria üzenetei inkompatibilisek* (*D2C. telemetria. kimenő. érvénytelen*) metrika az [útválasztási mérőszámokban](monitor-iot-hub-reference.md#routing-metrics). További információért lásd: [IoT hub figyelése](monitor-iot-hub.md).

* Az üzenet-gazdagítás nem vonatkozik a digitális kettős változási eseményekre.

## <a name="pricing"></a>Díjszabás

Az üzenetek dúsítása díjmentesen elérhető. Jelenleg akkor számítunk fel díjat, amikor üzenetet küld egy IoT Hubnak. Erre az üzenetre csak egyszer kell fizetnie, még akkor is, ha az üzenet több végpontra is érvényes.

## <a name="next-steps"></a>Következő lépések

Tekintse meg ezeket a cikkeket, ha további információt szeretne arról, hogyan lehet útválasztási üzeneteket IoT Hub:

* [Az üzenet-gazdagítás oktatóanyaga](tutorial-message-enrichments.md)

* [Eszközről a felhőbe irányuló üzenetek küldése különböző végpontokra IoT Hub üzenet-útválasztás használatával](iot-hub-devguide-messages-d2c.md)

* [Oktatóanyag: IoT Hub Útválasztás](tutorial-routing.md)
