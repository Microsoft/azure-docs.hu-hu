---
title: A leválasztott eszközök frissítésének ismertetése a Microsoft csatlakoztatott gyorsítótárának használatával | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: A leválasztott eszközök frissítésének ismertetése a Microsoft csatlakoztatott gyorsítótárának használatával
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: e2b27934f58402ecfb7dabf5560dc43e45f3f7dd
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679548"
---
# <a name="understand-support-for-disconnected-device-updates"></a>A leválasztott eszközök frissítéseinek támogatása

Transzparens átjáró esetén egy vagy több eszköz továbbíthatja az üzeneteiket egyetlen átjárón keresztül, amely az Azure IoT Hub-hoz való kapcsolódást tartja fenn. Ezekben az esetekben előfordulhat, hogy az alárendelt eszközök nem rendelkeznek internetkapcsolattal, vagy nem engedélyezett a tartalom letöltése az internetről. A Microsoft Connected cache Preview IoT Edge modulja az Azure IoT Hub ügyfelei számára biztosít egy intelligens hálózati gyorsítótárat, amely lehetővé teszi a Linux operációs rendszerű eszközök rendszerkép-alapú és Package-alapú frissítését, IoT Edge valamint az átjárók (downstream IoT-eszközök) használatát, valamint az Azure IoT Hub ügyfelei számára az eszköz frissítéséhez szükséges sávszélesség megtakarítását is.

## <a name="how-does-microsoft-connected-cache-preview-for-device-update-for-azure-iot-hub-work"></a>Hogyan működik a Microsoft Connected cache előzetes verziója az Azure IoT Hub-beli eszközök frissítéséhez?

A Microsoft Connected cache egy intelligens, transzparens gyorsítótár az Azure IoT Hub-tartalmak eszköz-frissítéséhez közzétett tartalmakhoz, és testreszabható a tartalmak más forrásokból, például csomagokból való gyorsítótárazására is. A Microsoft csatlakoztatott gyorsítótár egy olyan, a kézbesítési optimalizálási ügyfél által igényelt pontos tartományokra vonatkozó kérelmek által beérkező, a rendszer által használt, nem a tartalom előírása előtti, hideg gyorsítótár. Az alábbi ábra és részletes leírás azt ismerteti, hogyan működik a Microsoft Connected cache az Azure IoT Hub-infrastruktúra eszközének frissítésében.

>[!Note]
>A folyamat meghatározásakor feltételezzük, hogy az IoT Edge átjáró rendelkezik internetkapcsolattal. Az alsóbb rétegbeli IoT Edge átjáró (beágyazott Edge) forgatókönyv esetén a "Content Delivery Network" (CDN) a szülő IoT Edge átjárón üzemeltetett MCC lehet.

  :::image type="content" source="media/connected-cache-overview/disconnected-device-update.png" alt-text="Leválasztott eszköz frissítése" lightbox="media/connected-cache-overview/disconnected-device-update.png":::

1. A Microsoft csatlakoztatott gyorsítótár IoT Edge-modulként van telepítve a helyszíni kiszolgálón.
2. Az Azure IoT Hub-ügyfelekhez készült eszköz frissítése úgy van konfigurálva, hogy a Microsoft által csatlakoztatott gyorsítótárból letöltse a tartalmat a IoT Leaf-eszközökhöz tartozó GatewayHostName attribútum alapján, **vagy** parent_hostname a IoT Edge alárendelt eszközökhöz tartozó config. toml beállításban.
3. Az Azure IoT Hub-ügyfelek esetében mindkét esetben az Azure IoT Hub-szolgáltatás frissítésére vonatkozó letöltési parancsokat fogadja, és a CDN helyett a Microsofthoz csatlakoztatott gyorsítótárra kéri a frissítés tartalmát. A Microsoft által csatlakoztatott gyorsítótár alapértelmezés szerint úgy van konfigurálva, hogy figyelje a 80-es http-portot, és a kézbesítési optimalizálási ügyfél a 80-es porton kéri a tartalom kérését, hogy a szülőt a port figyelésére kell konfigurálni.  Jelenleg csak a HTTP protokoll támogatott.
4. A Microsoft csatlakoztatott gyorsítótár-kiszolgáló letölti a tartalmat a CDN-ből, megőrzi a helyi gyorsítótárat a lemezen, és továbbítja a tartalmat az Azure IoT Hub-ügyfélhez tartozó eszköz frissítéséhez.
   
>[!Note]
>A Package-alapú frissítések használatakor a rendszer a rendszergazda és a szükséges csomag állomásneve alapján konfigurálja a Microsoft Connected cache kiszolgálót.

5. Az Azure IoT Hub-ügyfelek az azonos frissítési tartalomhoz tartozó más eszközök frissítéseiről érkező további kérések mostantól a gyorsítótárból származnak, és a Microsoft csatlakoztatott gyorsítótára nem kéri a CDN-nek ugyanazt a tartalmat.

### <a name="supporting-industrial-iot-iiot-with-parentchild-hosting-scenarios"></a>Ipari IoT (IIoT) támogatása szülő/gyermek üzemeltetési forgatókönyvekkel

Ha egy alsóbb rétegbeli vagy gyermek IoT Edge átjáró a Microsofthoz csatlakoztatott gyorsítótár-kiszolgálót futtatja, akkor a rendszer úgy konfigurálja, hogy a szülő IoT Edge átjáróról kérjen frissítési tartalmat, amely a Microsoft csatlakoztatott gyorsítótár-kiszolgálót üzemelteti. Ez annyi szinten szükséges, amennyi szükséges ahhoz, hogy elérje a szülő IoT Edge átjárót, amely az internet-hozzáféréssel rendelkező Microsoft csatlakoztatott gyorsítótár-kiszolgálót üzemeltet. Az internetkapcsolattal rendelkező kiszolgálóról a rendszer a CDN-ből kéri a tartalmat, amely a tartalmat visszaküldi a gyermek IoT Edge átjárónak, amely eredetileg kérte a tartalmat. A tartalom minden szinten lemezen lesz tárolva.

## <a name="access-to-the-microsoft-connected-cache-preview-for-device-update-for-azure-iot-hub"></a>Hozzáférés a Microsoft csatlakoztatott gyorsítótár előzetes verziójához az Azure-beli eszköz frissítéséhez IoT Hub

A Microsoft Connected cache IoT Edge modul előzetes verzióként jelenik meg azon ügyfelek számára, akik az Azure IoT Hub eszköz frissítésével telepítik a megoldásokat. Az előzetes verzióhoz való hozzáférés meghívással érhető el. [Kérjen hozzáférést](https://aka.ms/MCCForDeviceUpdateForIoT) a Microsoft Connected cache előzetes verziójához az IoT Hut eszköz frissítéséhez, és adja meg a kért információkat, ha szeretné elérni a modult.
