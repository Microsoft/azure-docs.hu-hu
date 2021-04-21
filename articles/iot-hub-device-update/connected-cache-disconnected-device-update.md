---
title: A leválasztott eszközfrissítés Microsoft Csatlakoztatott gyorsítótár | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: A leválasztott eszközfrissítés Microsoft Csatlakoztatott gyorsítótár
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: e216d42ff1f279d87e657126514fcfb50960f806
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811903"
---
# <a name="understand-support-for-disconnected-device-updates"></a>A leválasztott eszközfrissítések támogatásának

Transzparens átjáró esetén egy vagy több eszköz egyetlen átjáróeszközön keresztül tudja átadni az üzeneteket, amely fenntartja a kapcsolatot a Azure IoT Hub. Ezekben az esetekben előfordulhat, hogy a gyermekeszközök nem rendelkezik internetkapcsolattal, vagy nem engedélyezett a tartalom letöltése az internetről. A Microsoft Csatlakoztatott gyorsítótár Preview IoT Edge-modul biztosítja az Azure IoT Hub-ügyfelek eszközfrissítését egy intelligens hálózati gyorsítótárral, amely lehetővé teszi a Linux operációsrendszer-alapú eszközök képalapú és csomagalapú frissítését a mögöttes és IoT Edge-átjárók (lefelé irányuló IoT-eszközök) számára, és segít sávszélességet megtakarékosni az Azure IoT Hub-ügyfelek eszközfrissítése esetében.

## <a name="how-does-microsoft-connected-cache-preview-for-device-update-for-azure-iot-hub-work"></a>Hogyan működik Csatlakoztatott gyorsítótár Microsoft Azure IoT Hub eszközfrissítések előzetes verziója?

A Microsoft Csatlakoztatott gyorsítótár előzetes verziója egy intelligens, transzparens gyorsítótár az Azure IoT Hub-tartalmak eszközfrissítéséhez közzétett tartalmakhoz, és testreszabható úgy, hogy más forrásokból, például csomagtárakból származó tartalmakat is gyorsítótárazjon. A Microsoft Csatlakoztatott gyorsítótár egy hideg gyorsítótár, amelyet a Kézbesítésoptimalizálás-ügyfél által kért pontos fájltartományra vonatkozó ügyfélkérések melegítik, és nem számítja előre a tartalmat. Az alábbi diagram és részletes leírás azt mutatja be, hogyan működik a Microsoft Csatlakoztatott gyorsítótár az eszközfrissítésen belül a Azure IoT Hub infrastruktúrában.

>[!Note]
>A folyamat meghatározásakor feltételezzük, hogy a IoT Edge átjáró rendelkezik internetkapcsolattal. Az alsóbb rétegbeli IoT Edge (beágyazott peremhálózat) esetében az "Content Delivery Network" (CDN) az átjáró szülő-átjárón üzemeltetett MCC-nek IoT Edge tekinthető.

  :::image type="content" source="media/connected-cache-overview/disconnected-device-update.png" alt-text="Leválasztott eszköz frissítése" lightbox="media/connected-cache-overview/disconnected-device-update.png":::

1. A Microsoft Csatlakoztatott gyorsítótár modulként IoT Edge a rendszer a virtuális gépre.
2. Az Azure IoT Hub-ügyfelek eszközfrissítése úgy van konfigurálva, hogy tartalmat töltsön le a Microsoft Csatlakoztatott gyorsítótár-ból az IoT levéleszközök eszközkapcsolati sztringje GatewayHostName attribútumának vagy parent_hostname config.toml fájlban beállított IoT Edge gyermekeszközökhöz. 
3. A Azure IoT Hub-ügyfelek eszközfrissítése mindkét esetben frissítési tartalomletöltési parancsokat kap az Azure IoT Hub eszközfrissítési szolgáltatástól, és a CDN helyett frissítési tartalmat kér a Microsoft Csatlakoztatott gyorsítótár számára. A Microsoft Csatlakoztatott gyorsítótár alapértelmezés szerint úgy van konfigurálva, hogy a 80-as HTTP-portot figyelje, és a Kézbesítésoptimalizálás-ügyfél a 80-as porton keresztül teszi a tartalomkérést, így a szülőt úgy kell konfigurálni, hogy ezen a porton figyeljen.  Jelenleg csak a HTTP protokoll támogatott.
4. A Microsoft Csatlakoztatott gyorsítótár-kiszolgáló letölti a tartalmat a CDN-ről, felrakja a lemezen tárolt helyi gyorsítótárat, és kézbesíti a tartalmat az Azure IoT Hub ügyfélnek.
   
>[!Note]
>Csomagalapú frissítések használata esetén a rendszergazda konfigurálja a Microsoft Csatlakoztatott gyorsítótár-kiszolgálót a szükséges csomag gazdanevéhez.

5. Az ugyanannak a frissítési tartalomnak az Azure IoT Hub-ügyfelekre vonatkozó további eszközfrissítési kérései mostantól a gyorsítótárból, a Microsoft Csatlakoztatott gyorsítótár pedig nem fog kérelmeket kérni a CDN-nek ugyanannak a tartalomnak.

### <a name="supporting-industrial-iot-iiot-with-parentchild-hosting-scenarios"></a>Ipari IoT (IIoT) támogatása szülő/gyermek üzemeltetési forgatókönyvekkel

Ha egy lefelé irányuló vagy gyermek IoT Edge-átjáró a Microsoft Csatlakoztatott gyorsítótár-kiszolgálót üzemeltető, akkor a rendszer úgy konfigurálja, hogy frissítési tartalmat kérjen a Microsoft Csatlakoztatott gyorsítótár-kiszolgálót üzemeltető szülő IoT Edge-átjárótól. Ez annyi szinten szükséges, amennyire csak szüksége van, mielőtt elérné az interneteléréssel IoT Edge Microsoft Csatlakoztatott gyorsítótár-kiszolgálót üzemeltető szülő átjárót. Az internethez csatlakozó kiszolgálóról a tartalom lekérése a CDN-ről kérhető le, amikor a rendszer visszaküldi a tartalmat IoT Edge gyermek-átjárónak, amely eredetileg lekérte a tartalmat. A tartalom minden szinten lemezen lesz tárolva.

## <a name="access-to-the-microsoft-connected-cache-preview-for-device-update-for-azure-iot-hub"></a>Hozzáférés a Microsoft Csatlakoztatott gyorsítótár eszközfrissítés előzetes verziójához a Azure IoT Hub

A Microsoft Csatlakoztatott gyorsítótár IoT Edge modul előzetes verzióként érhető el az olyan ügyfelek számára, akik a Device Update for Azure IoT Hub. Az előzetes verzió meghívással érhető el. [Kérjen hozzáférést a](https://aka.ms/MCCForDeviceUpdateForIoT) Microsoft Csatlakoztatott gyorsítótár Előzetes verziójához az IoT Hub eszközfrissítéséhez, és adja meg a modulhoz való hozzáféréshez kért információkat.
