---
title: Használja az Azure IoT Edge-eszközök átjáróként |} A Microsoft Docs
description: Használja az Azure IoT Edge hozhat létre egy transzparens átlátszatlan vagy a proxy átjáróeszköz, amely adatokat küld a több alárendelt eszközről a felhőbe vagy helyi feldolgozza azt.
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 09/21/2017
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 71fce04eb4f21065184d86301689039294da8b61
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741912"
---
# <a name="how-an-iot-edge-device-can-be-used-as-a-gateway"></a>IoT Edge-eszköz segítségével hogyan biztosítható átjáróként

IoT-megoldások átjárók célja a megoldáshoz és kombinálja az eszközkapcsolat peremhálózati elemzést. Az Azure IoT Edge segítségével IoT-átjáró, függetlenül attól, hogy azok kapcsolódó kapcsolat, identity vagy peremhálózati elemzés futtatása a szükségletét. Ez a cikk az átjáró minták csak tekintse meg jellemzői alsóbb rétegbeli eszközök csatlakoztatása és az eszközidentitással, nem hogyan eszköz adatok feldolgozása az átjárón.

## <a name="patterns"></a>Minták
Nincsenek az IoT Edge-eszköz használata átjáróként a három minták: transzparens, protokoll, fordítás, és identitás fordítási:
* **Transzparens** – sikerült elméletileg csatlakoztatása az IoT hubhoz eszközök csatlakozhatnak átjáróeszköz helyette. Az alsóbb rétegbeli eszközök a saját IoT Hub-identitásokat, és az MQTT, AMQP vagy HTTP-protokollokat használja. Az átjáró egyszerűen továbbítja a kommunikációt az eszközök és az IoT Hub között. Az eszközöket, hogy a felhő egy átjárón keresztül kommunikálnak, és az eszközöket az IoT Hub használata a felhasználók nem észleli a köztes átjáróeszköz deduplikálta. Így az átjáró az átlátható. Tekintse meg a [transzparens átjáró létrehozása] [ lnk-iot-edge-as-transparent-gateway] útmutató bírálattal transzparens átjáróként IoT Edge-eszköz használatával.
* **Fordítási protokoll** – más néven átlátszatlan átjáró-minta, eszközök, amelyek nem támogatják az MQTT, AMQP vagy HTTP-használati adatok küldését az IoT Hub átjáróeszköz. Az átjáró nem elég intelligensek ahhoz, hogy az alsóbb rétegbeli eszközök; által használt protokoll megismeréséhez azonban az egyetlen eszköz, amely az IoT Hub identitás rendelkezik. Minden információt tűnik, hogy egy eszközt, az átjáró érkezik. Alsóbb rétegbeli eszközök kell beágyazása az üzenetek további információkra, ha a felhőalapú alkalmazások szeretné, hogy az adatok eszközönkénti alapon vonatkozó döntések meghozatalát. Emellett az IoT Hub primitívek, például a páros, és módszereket csak az átjáróeszköz nem alsóbb rétegbeli eszközök számára érhető el.
* **Identitás fordítási** -eszközöket, amelyek nem kapcsolódnak az IoT Hub inkább átjáróeszköz csatlakozni. Az átjáró biztosítja az IoT Hub identitás- és protokoll fordítási az alsóbb rétegbeli eszközök nevében. Az átjáró nem elég intelligensek ahhoz az alsóbb rétegbeli eszközök által használt protokollt, identitást biztosíthat, és az IoT Hub primitívek fordítása. Alsóbb rétegbeli eszközök első osztályú eszközökön twins és módszerek az IoT Hub jelennek meg. Egy felhasználó kezelheti az eszközöket az IoT Hubban, és nem észleli a köztes átjáróeszköz.

![Átjáró minták ábrái](./media/iot-edge-as-gateway/edge-as-gateway.png)

## <a name="use-cases"></a>Használati esetek
Az összes átjáró-minták a következő előnyöket nyújtják:
* **Él analytics** – helyileg történő elküldése nélkül alsóbb rétegbeli eszközökről érkező adatok feldolgozása használata AI-szolgáltatások teljes hűség telemetriai adatokat a felhőbe. Keresse meg és helyileg insights reagálhat rájuk, és csak IoT hubnak küldi az adatok egy részét. 
* **Alsóbb rétegbeli eszközök elkülönítési** – az átjáró eszköz valamennyi alsóbb rétegbeli eszközök az interneten való kitettség segédalkalmazási. Azt is Dőljön között egy Zásoktól hálózati kapcsolat nem rendelkező és a egy IT-hálózat, amely a webes hozzáférést biztosít. 
* **Kapcsolat multiplexálási** – minden eszköz csatlakozik az IoT Hub az IoT Edge segítségével eszköz használja ugyanazt a alapul szolgáló kapcsolatot.
* **Forgalom simítás** – az IoT Edge-eszköz automatikus végrehajtása exponenciális visszatartással, ha az IoT Hub szabályozza a forgalom, az üzenetek a helyi megőrzése közben. Ezzel az értékelemmel teszi adatforgalmának rugalmas megoldását.
* **Csak korlátozott támogatást offline** – az átjáró eszköz helyileg üzeneteket fogja tárolni és ikereszköz-frissítések, amelyekhez nem lehet kézbesíteni az IoT hubnak.

Egy átjáró biztosítja protokollfordítást is elvégezheti, peremhálózati elemzést, eszköz elkülönítés, forgalom simítás és offline támogatja a meglévő eszközökkel és új eszközöket, amelyek korlátozott erőforráshoz. Több meglévő eszközt állít elő, amelyek az üzleti elemzések; működtethet adatok azonban nem tervezték őket felhőalapú összefüggő szem előtt. Átlátszatlan átjárók lehetővé teszik a fel és teljes körű IoT-megoldások fel ezeket az adatokat.

Egy átjáró, amely identitás fordítási protokollfordítás előnyét, és emellett lehetővé teszi, hogy a felhőből alsóbb rétegbeli eszközök teljes kezelhetőségét. Beszéljen az IoT megoldás jelennek meg az IoT Hub függetlenül a protokoll, az összes eszközt.

## <a name="cheat-sheet"></a>Hasznos tanácsok
Íme egy gyors Adatlap, amely összehasonlítja az IoT Hub primitívek használatakor transzparens, átlátszatlan (protokoll), és proxy-átjárók.

| &nbsp; | Transzparens átjáró | Protokollfordítás | Identitás-fordítás |
|--------|-------------|--------|--------|
| Az IoT Hub-identitásjegyzék tárolja identitások | Az összes csatlakoztatott eszközön identitások | Csak az átjáró eszköz identitása | Az összes csatlakoztatott eszközön identitások |
| Ikereszközök | Minden csatlakoztatott eszközhöz tartozik a saját ikereszköz | Az átjáró csak egy eszköz- és modul twins rendelkezik | Minden csatlakoztatott eszközhöz tartozik a saját ikereszköz |
| Közvetlen metódusok és a felhőből az eszközre irányuló üzenetek | A felhő külön-külön kezelheti minden csatlakoztatott eszköz | A felhő csak kezelheti az átjáró eszköz | A felhő külön-külön kezelheti minden csatlakoztatott eszköz |
| [Az IoT Hub szabályozások és kvóták](../iot-hub/iot-hub-devguide-quotas-throttling.md) | Egyes eszközök a alkalmazni | Az átjáró eszköz a alkalmazni | Egyes eszközök a alkalmazni |

Egy nem átlátszó átjáró (protokollfordítás) minta használatakor, hogy az átjárón keresztül csatlakozó összes eszközök megosztása azonos felhőből az eszközre beolvasása, amely legfeljebb 50 üzenetet is tartalmazhat. Ebből az következik, hogy az átjáró nem átlátszó minta kell használni, csak akkor, amikor minden mező-átjárón keresztül kapcsolódik néhány olyan eszközt, és a felhőből az eszközre irányuló forgalom értéke alacsony.

## <a name="next-steps"></a>További lépések
Használja az IoT Edge-eszköz, mint egy [transzparens átjáró][lnk-iot-edge-as-transparent-gateway] 

[lnk-iot-edge-as-transparent-gateway]: ./how-to-create-transparent-gateway-linux.md
