---
title: Architektúra IoT – intelligens leltár kezelése | Microsoft Docs
description: A IoT intelligens leltározási felügyeleti sablonjának architektúrája IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: conceptual
ms.date: 10/20/2019
ms.custom: mqtt
ms.openlocfilehash: d7939193b0dad1ed74dccd0ace0b0f0323b17124
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99832640"
---
# <a name="architecture-of-iot-central-smart-inventory-management-application-template"></a>A IoT Central Smart Inventory Management alkalmazás-sablon architektúrája

A partnerek és az ügyfelek az alkalmazás sablonját és az alábbi útmutatást követve fejleszthetik a teljes körű **intelligens leltározási** megoldásokat.

> [!div class="mx-imgBorder"]
> ![intelligens leltár kezelése](./media/concept-smart-inventory-mgmt-architecture/smart-inventory-management-architecture.png)

1. IoT-érzékelők készlete telemetria-adatokat küld egy átjáró-eszközre
2. Telemetria küldő és összesített bepillantást IoT Central
3. Az adattovábbítás a kívánt Azure-szolgáltatásba történik a manipulációhoz
4. Az olyan Azure-szolgáltatások, mint az ASA vagy a Azure Functions használhatók az adatfolyamok újraformázására és a kívánt Storage-fiókokba való küldésre 
5. A feldolgozott adatok tárolása a közel valós idejű műveletekhez vagy a hideg tároláshoz szükséges, és az olyan további elemzéseket is tartalmaz, amelyek az ML-vagy batch-elemzésen alapulnak. 
6. A Logic Apps a végfelhasználói üzleti alkalmazások különböző üzleti munkafolyamatainak kikapcsolására is használható

## <a name="details"></a>Részletek
A következő szakasz ismerteti a koncepcionális architektúra telemetria betöltését a rádiófrekvenciás azonosítás (RFID), a Bluetooth alacsony energiafogyasztású Címkék

## <a name="rfid-tags"></a>RFID-Címkék
Az RFID-címkék rádióhullámokon keresztül továbbítják az adott elemmel kapcsolatos adatokat. Az RFID-címkék általában nem rendelkeznek akkumulátorral, kivéve, ha meg van adva. A címkék energiát kapnak az olvasó által generált rádióhullámokból, és visszaküldik a jeleket az RFID-olvasó felé.

## <a name="ble-tags"></a>A Címkék
Az Energy Beacon rendszeres időközönként közvetíti az adatcsomagokat. A Beacon-alapú adattárakat az intelligens olvasók vagy a telepített szolgáltatások észlelik, majd továbbítják a felhőbe.

## <a name="rfid--ble-readers"></a>RFID-& rendszerolvasók
Az RFID-olvasó a rádióhullámokat egy jobban használható adatformátumba alakítja át. A címkékből gyűjtött információk ezután a helyi peremhálózati kiszolgálón tárolódnak vagy a felhőbe kerülnek a JSON-RPC 2,0-en keresztül, a MQTT-n keresztül.
Az egyhelyes olvasó más néven hozzáférési pontok (AP) hasonlóak az RFID-olvasóhoz. A rendszer a közeli Bluetooth-jelek észlelésére, valamint az üzenet helyi Azure IoT Edge vagy felhőbe való továbbítására szolgál a JSON-RPC 2,0-en keresztül a MQTT-en keresztül.
Számos olvasó képes az RFID-és a Beacon-jelek olvasására, valamint a hőmérséklettel, páratartalommal, gyorsulásmérővel és giroszkópokkal kapcsolatos további érzékelő képesség biztosítására.

## <a name="azure-iot-edge-gateway"></a>Átjáró Azure IoT Edge
Azure IoT Edge kiszolgáló lehetővé teszi az adatok helyi előfeldolgozását, mielőtt elküldené a felhőbe. A Felhőbeli számítási feladatokat mesterséges intelligencia, az Azure és a külső szolgáltatások, az üzleti logika standard szintű tárolók használatával is üzembe helyezheti.

## <a name="device-management-with-iot-central"></a>Eszközkezelés IoT Central 
Az Azure IoT Central egy megoldás-fejlesztői platform, amely leegyszerűsíti a IoT, a konfigurációt és a felügyeletet. A platform jelentősen csökkenti az IoT, a műveletek és a kapcsolódó fejlesztések terheit és költségeit. Az ügyfelek & partnerekkel teljes körű vállalati megoldásokat hozhatnak létre, amelyekkel digitális visszajelzési hurkot érhet el a készletkezelésben.

## <a name="business-insights--actions-using-data-egress"></a>Üzleti elemzések & a kimenő adatforgalommal kapcsolatos műveletek 
A IoT Central platform a folyamatos adatexportálás (CDE) és az API-k révén gazdag bővíthetőségi lehetőségeket biztosít. A telemetria adatfeldolgozási vagy nyers telemetria alapuló üzleti elemzések általában egy előnyben részesített üzletági alkalmazásba exportálhatók. A webhook, a Service Bus, az Event hub vagy a blob Storage használatával a gépi tanulási modellek fejlesztéséhez, betanításához és üzembe helyezéséhez & további ismeretek bevezetéséhez.

## <a name="next-steps"></a>Következő lépések
* Ismerje meg, hogyan telepítheti az [intelligens leltár felügyeleti sablonját](./tutorial-iot-central-smart-inventory-management.md)
* További információ a [IoT Central kiskereskedelmi sablonokról](./overview-iot-central-retail.md)
* További információ a IoT Centralról [IoT Central áttekintés](../core/overview-iot-central.md)
