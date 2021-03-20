---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 007c19a10db5e000770c8c80189453d4a80edec2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "82204560"
---
A következő táblázat felsorolja az S1, S2, S3 és F1 különböző szolgáltatási szintjeihez társított korlátokat. További információ az egyes *egységek* költségéről: [Azure IoT hub díjszabása](https://azure.microsoft.com/pricing/details/iot-hub/).

| Erőforrás | S1 – Standard | S2 – Standard | S3 – Standard | F1 – Ingyenes |
| --- | --- | --- | --- | --- |
| Üzenet/nap |400,000 |6,000,000 |300,000,000 |8,000 |
| Egységek maximális száma |200 |200 |10 |1 |

> [!NOTE]
> Ha várhatóan több mint 200 egységet kíván használni S1 vagy S2 szintű hubhoz, vagy 10 egység S3 szintű hubhoz, forduljon a Microsoft ügyfélszolgálatahoz.
> 
> 

A következő táblázat felsorolja a IoT Hub erőforrásokra vonatkozó korlátozásokat.

| Erőforrás | Korlát |
| --- | --- |
| Azure-előfizetésenkénti fizetős IoT Hubok maximális száma |50 |
| Azure-előfizetésenkénti ingyenes IoT Hubok maximális száma |1 |
| Az eszköz AZONOSÍTÓjában szereplő karakterek maximális száma | 128 |
| Eszközidentitások egyetlen hívásban visszaadott<br/> maximális száma |1,000 |
| Egy eszközről a felhőbe irányuló IoT Hub-üzenetek maximális megőrzési ideje |7 nap |
| Egy eszközről a felhőbe irányuló üzenet maximális mérete |256 KB |
| Egy eszközről a felhőbe irányuló köteg maximális mérete |AMQP és HTTP: 256 KB a teljes köteghez <br/>MQTT: 256 KB minden üzenethez |
| Egy eszközről a felhőbe irányuló kötegben található üzenetek maximális száma |500 |
| A felhőből egy eszközre irányuló üzenet maximális mérete |64 KB |
| A felhőből egy eszközre irányuló üzenetek maximális élettartama |2 nap |
| A felhőből egy eszközre történő kézbesítések maximális száma <br/> üzenet |100 |
| A felhőből az eszközre irányuló üzenetsor maximális mélysége eszközönként |50 |
| A felhőből egy eszközre irányuló üzenetre válaszoló <br/> visszajelzési üzenetek maximális kézbesítésszáma |100 |
| A felhőből egy eszközre irányuló üzenetre válaszoló <br/> visszajelzési üzenetek maximális élettartama |2 nap |
| [Ikereszköz maximális mérete](../articles/iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) | 8 KB a címkék szakaszhoz, és 32 KB a kívánt és jelentett tulajdonságok szakaszokhoz |
| Az eszköz kettős sztringje kulcsának maximális hossza | 1 KB |
| Az eszköz kettős sztringje értékének maximális hossza | 4 KB |
| [Az ikereszközben található objektum maximális mélysége](../articles/iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format) | 10 |
| A közvetlen metódus hasznos adatainak maximális mérete | 128 KB |
| Feladatelőzmények maximális megőrzési ideje | 30 nap |
| Egyidejű feladatok maximális száma | 10 (S3 esetén), 5 (S2 esetén), 1 (S1 esetén) |
| További végpontok maximális száma | 10 (S1, S2 és S3 esetén) |
| Üzenet-útválasztási szabályok maximális száma | 100 (S1, S2 és S3 esetén) |
| Egyidejűleg csatlakoztatott adatfolyamok maximális száma | 50 (csak S1, S2, S3 és F1 esetén) |
| Az eszköz adatfolyamának maximális adatátvitele | 300 MB/nap (csak S1, S2, S3 és F1 esetén) |

> [!NOTE]
> Ha egy Azure-előfizetésben több mint 50 fizetős IoT-hubhoz van szüksége, vegye fel a kapcsolatot Microsoft ügyfélszolgálata.

> [!NOTE]
> Jelenleg az egyetlen IoT-hubhoz regisztrálható eszközök és modulok száma 1 000 000. Ha szeretné megnövelni ezt a korlátot, forduljon a [Microsoft ügyfélszolgálatahoz](https://azure.microsoft.com/support/options/).

A IoT Hub szabályozza a kérelmeket, ha túllépi a következő kvótákat.

| Szabályozás | Kötegenkénti érték |
| --- | --- |
| Identitásjegyzék műveletei <br/> (létrehozás, lekérés, Listázás, frissítés és törlés), <br/> egyéni vagy tömeges importálás/exportálás |83.33/mp/egység (5000/perc/egység) (S3 esetén). <br/> 1.67/mp/egység (100/perc/egység) (S1 és S2 esetén). |
| Eszközkapcsolatok |6000/mp/egység (S3 esetén), 120/mp/egység (S2 esetén), 12/MP/egység (S1 esetén). <br/>Legalább 100/mp. |
| Az eszközről a felhőbe irányuló küldések |6000/mp/egység (S3 esetén), 120/mp/egység (S2 esetén), 12/MP/egység (S1 esetén). <br/>Legalább 100/mp. |
| Küldések a felhőből az eszközökre | 83.33/mp/egység (5000/perc/egység) (S3 esetén), 1.67/mp/egység (100/perc/egység) (S1 és S2 esetén). |
| Fogadások a felhőből az eszközökön |833.33/mp/egység (50000/perc/egység) (S3 esetén), 16.67/s/egység (1000/perc/egység) (S1 és S2 esetén). |
| Fájlfeltöltési műveletek |83,33 fájlfeltöltés-kezdeményezés/mp/egység (5000/perc/egység) (S3 esetén), 1,67 fájlfeltöltés-kezdeményezés/mp/egység (100/perc/egység) (S1 és S2 esetén). <br/> 10 000 SAS URI-k egyszerre egy Azure Storage-fiókhoz is kiállíthatók.<br/> Eszközönként egyszerre 10 SAS URI lehet használatban. |
| Közvetlen metódusok | 24 MB/s/egység (S3 esetén), 480 KB/s/egység (S2 esetén), 160 KB/s/egység (S1 esetén).<br/> 8 KB-os szabályozási mérési méret alapján. |
| Ikereszköz-olvasások | 500/mp/egység (S3 esetén), legfeljebb 100/mp vagy 10/mp/egység (S2 esetén), 100/mp (S1 esetén) |
| Ikereszköz-frissítések | 250/mp/egység (S3 esetén), legfeljebb 50/mp vagy 5/MP/egység (S2 esetén), 50/mp (S1 esetén) |
| Feladatműveletek <br/> (létrehozás, frissítés, Listázás és törlés) | 83.33/mp/egység (5000/perc/egység) (S3 esetén), 1.67/s/egység (100/perc/egység) (S2 esetén), 1.67/mp/egység (100/perc/egység) (S1 esetén). |
| Feladatok eszközönkénti műveleti teljesítménye | 50/mp/egység (S3 esetén), legfeljebb 10/mp vagy 1/MP/egység (S2 esetén), 10/mp (S1 esetén). |
| Az eszköz stream-kezdeményezési sebessége | 5 új stream/mp (csak S1, S2, S3 és F1 esetén). |
