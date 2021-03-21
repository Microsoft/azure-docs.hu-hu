---
title: Event Grid összehasonlítása, IoT Hub útválasztása | Microsoft Docs
description: A IoT Hub a saját üzenet-útválasztási szolgáltatást nyújtja, de a Event Grid is integrálható az események közzétételéhez. Hasonlítsa össze a két funkciót.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.custom: fasttrack-edit
ms.openlocfilehash: 37b411792ea1a3e21e0f26df0c7905eb8d46310e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97897705"
---
# <a name="compare-message-routing-and-event-grid-for-iot-hub"></a>Az üzenetek útválasztásának és Event Gridének összehasonlítása IoT Hub

Az Azure IoT Hub lehetőséget biztosít a csatlakoztatott eszközökről érkező adatok továbbítására és az adatok üzleti alkalmazásokba való integrálására. IoT Hub két módszert kínál a IoT események más Azure-szolgáltatásokba vagy üzleti alkalmazásokba való integrálására. Ez a cikk az ezt a képességet biztosító két szolgáltatást ismerteti, így kiválaszthatja, hogy melyik lehetőség a legmegfelelőbb a forgatókönyvhöz.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

**[IoT hub üzenet-útválasztás](iot-hub-devguide-messages-d2c.md)**: ez a IoT hub funkció lehetővé teszi, hogy a felhasználók az eszközről a felhőbe irányuló üzeneteket az Azure Storage-tárolók, a Event Hubs, a Service Bus várólisták és a Service Bus témakörök számára irányítsák. Az Útválasztás egy lekérdezési képességet is biztosít az adatszűréshez, mielőtt átirányítja azt a végpontokhoz. Az eszközök telemetria kívül a műveletek elindításához használható [nem telemetria eseményeket](iot-hub-devguide-messages-d2c.md#non-telemetry-events) is küldhet. 

**IoT hub Integration with Event Grid**: Azure Event Grid egy teljes körűen felügyelt esemény-útválasztási szolgáltatás, amely egy közzétételi-előfizetési modellt használ. IoT Hub és Event Grid együttműködve [IoT hub eseményeket integrálhat az Azure-ba és a nem Azure-szolgáltatásokba](iot-hub-event-grid.md), közel valós időben. A IoT Hub az [eszköz eseményeit](iot-hub-event-grid.md#event-types) és a telemetria eseményeket is közzéteszi.

## <a name="differences"></a>Eltérések

Noha az üzenet-útválasztás és a Event Grid is lehetővé teszi a riasztások konfigurálását, a két fő különbség van. A részletekért tekintse meg az alábbi táblázatot:

| Szolgáltatás | IoT Hub üzenet-útválasztás | IoT Hub integráció a Event Grid |
| ------- | --------------- | ---------- |
| **Eszközök üzenetei és eseményei** | Igen, az üzenet-útválasztás telemetria, az eszköz kettős változásaira, az eszköz életciklusának eseményeire és a digitális kettős változási eseményekre is használható. | Igen, Event Grid használhatók telemetria-és eszköz-események, például a létrehozott/törölt/csatlakoztatott/leválasztott eszközökhöz. Az Event Grid azonban nem használható az eszköz kettős változási eseményeihez és a digitális kettős változási eseményekhez. |
| **Rendezés** | Igen, az események rendezése megmarad.  | Nem, az események sorrendje nem garantált. | 
| **Szűrés** | Részletes szűrés az üzenetsor-tulajdonságok, az üzenetsor-tulajdonságok, az üzenetek szövegtörzse, az eszköz Twin-címkék és az eszközök Twin tulajdonságai között. A szűrés nincs alkalmazva a digitális kettős változási eseményekre. Példák: üzenet- [útválasztási lekérdezés szintaxisa](iot-hub-devguide-routing-query-syntax.md). | Szűrés az esemény típusa, a tulajdonos típusa és az egyes események attribútumai alapján. Példák: [Event Grid-előfizetések szűrési eseményeinek megismerése](../event-grid/event-filtering.md). A telemetria eseményekre való feliratkozáskor további szűrőket is alkalmazhat az adatain az üzenet tulajdonságaira, az üzenetek szövegtörzsére és az eszköz IoT Hubre való kiszűrésére, mielőtt közzéteszi a Event Grid. Lásd: [Események szűrése](../iot-hub/iot-hub-event-grid.md#filter-events). |
| **Végpontok** | <ul><li>Event Hubs</li> <li>Azure Blob Storage</li> <li>Service Bus üzenetsor</li> <li>Service Bus-témakörök</li></ul><br>A fizetős IoT Hub SKU-i (S1, S2 és S3) 10 egyéni végpontra korlátozódnak. 100 útvonal hozható létre IoT Hub. | <ul><li>Azure Functions</li> <li>Azure Automation</li> <li>Event Hubs</li> <li>Logic Apps</li> <li>Storage Blob</li> <li>Egyéni témakörök</li> <li>Queue Storage</li> <li>Power Automate</li> <li>Harmadik féltől származó szolgáltatások webhookokon keresztül</li></ul><br>500 végpontok száma IoT Hub támogatott. A végpontok legnaprakészebb listáját lásd: [Event Grid eseménykezelők](../event-grid/overview.md#event-handlers). |
| **Költségek** | Az üzenet-útválasztásnak nincs külön díja. A rendszer csak a telemetria beérkező IoT Hub számítja fel. Ha például egy üzenet három különböző végpontra van irányítva, akkor csak egy üzenetért kell fizetnie. | IoT Hub nem számítunk fel díjat. A Event Grid havonta ingyenes, majd $0,60/millió művelet után kínálja az első 100 000 műveletet. |

## <a name="similarities"></a>Hasonlóságok

IoT Hub üzenet-útválasztás és Event Grid hasonlóságokkal is rendelkezik, amelyek némelyike az alábbi táblázatban látható:

| Szolgáltatás | IoT Hub üzenet-útválasztás | IoT Hub integráció a Event Grid |
| ------- | --------------- | ---------- |
| **Üzenetek maximális mérete** | 256 KB, eszközről a felhőbe | 256 KB, eszközről a felhőbe |
| **Megbízhatóság** | Magas: minden egyes útvonal esetében legalább egyszer kézbesíti az egyes üzeneteket a végpontnak. Minden olyan üzenetet lejár, amely egy órán belül nem érkezik meg. | Magas: minden egyes előfizetés esetében legalább egyszer kézbesít minden üzenetet a webhooknak. Az összes olyan eseményt lejár, amely 24 órán belül nem érkezik meg. | 
| **Méretezhetőség** | Magas: optimalizált több millió egyidejűleg csatlakoztatott eszköz támogatásához, több milliárd üzenet küldéséhez. | Magas: az 10 000 000 események másodpercenkénti útválasztása régiónként. |
| **Késés** | Alacsony: majdnem valós időben. | Alacsony: majdnem valós időben. |
| **Küldés több végpontra** | Igen, küldjön egyetlen üzenetet több végpontnak. | Igen, küldjön egyetlen üzenetet több végpontnak.  
| **Biztonság** | Az IOT hub eszközönkénti identitást és visszavont hozzáférés-vezérlést biztosít. További információ: [IoT hub hozzáférés-vezérlés](iot-hub-devguide-security.md). | A Event Grid három ponton biztosít ellenőrzést: esemény-előfizetések, esemény-közzététel és webhook-esemény kézbesítése. További információ: [Event Grid biztonság és hitelesítés](../event-grid/security-authentication.md). |

## <a name="how-to-choose"></a>Mi alapján válasszon?

IoT Hub üzenet-útválasztás és a IoT Hub integrációja Event Grid különböző műveleteket hajthat végre a hasonló eredmények eléréséhez. A IoT Hub-megoldástól származó adatokat is továbbítanak, így más szolgáltatások is reagálni tudnak. Tehát hogyan döntheti el, hogy melyiket használja? A döntés meghozatala érdekében vegye figyelembe a következő kérdéseket: 

* **Milyen típusú adatokat küld a rendszer a végpontoknak?**

   Ha telemetria-adatküldést szeretne küldeni más szolgáltatásoknak, használja IoT Hub üzenet-útválasztást. Az üzenet-útválasztás lehetővé teszi az üzenet-alkalmazás és a Rendszertulajdonságok, az üzenetek szövegtörzse, az eszköz Twin-címkék és az eszközök Twin tulajdonságainak lekérdezését is.

   A IoT Hub integrációja Event Grid a IoT Hub szolgáltatásban előforduló eseményekkel működik. Ezek a IoT Hub események közé tartoznak a telemetria-, a létrehozott, a törölt, a csatlakoztatott és a leválasztott eszközök. A telemetria eseményekre való feliratkozáskor további szűrőket is alkalmazhat az adatain az üzenet tulajdonságaira, az üzenetek szövegtörzsére és az eszköz IoT Hubre való kiszűrésére, mielőtt közzéteszi a Event Grid. Lásd: [Események szűrése](../iot-hub/iot-hub-event-grid.md#filter-events).

* **Milyen végpontoknak kell megkapniuk ezeket az információkat?**

   IoT Hub üzenet-útválasztás csak korlátozott számú egyedi végpontot és végpontot támogat, de összekötők segítségével átirányíthatja az adatelemeket és az eseményeket további végpontokra. A támogatott végpontok teljes listájáért tekintse meg az előző szakaszban található táblázatot. 

   A IoT Hub integrációja Event Grid támogatja az 500-es végpontokat IoT Hub és számos végponti típust. Natív módon integrálódik Azure Functions-, Logic Apps-, Storage-és Service Bus-várólistákkal, és webhookokkal is együttműködik, hogy az Azure-szolgáltatás ökoszisztémáján és külső üzleti alkalmazásain kívül is kiterjessze az adatokat.

* **Számít, ha az adatai sorrendben érkeznek?**

   IoT Hub üzenet-útválasztás megtartja az üzenetek küldésének sorrendjét, így azok ugyanúgy érkeznek.

   A Event Grid nem garantálja, hogy a végpontok ugyanabban a sorrendben kapják meg az eseményeket. Azokban az esetekben, amelyekben az üzenetek abszolút sorrendje jelentős és/vagy amelyben a fogyasztónak megbízható egyedi azonosítóval kell rendelkeznie az üzenetekhez, javasoljuk, hogy az üzenet-útválasztást használják. 

## <a name="next-steps"></a>Következő lépések

* További információ az [IoT hub üzenet-útválasztásról](iot-hub-devguide-messages-d2c.md) és a [IoT hub végpontokról](iot-hub-devguide-endpoints.md).
* További tudnivalók az [Azure Event Grid](../event-grid/overview.md) szolgáltatásról.
* Az üzenetsor-útvonalak létrehozásával kapcsolatos további információkért tekintse meg a [IoT hub eszközről a felhőbe irányuló üzenetek feldolgozását az útvonalak oktatóanyag használatával](../iot-hub/tutorial-routing.md) .
* Próbálja ki a Event Grid integrációt az [Azure IoT hub-eseményekkel kapcsolatos e-mail-értesítések küldésével Logic Apps használatával](../event-grid/publish-iot-hub-events-to-logic-apps.md).
