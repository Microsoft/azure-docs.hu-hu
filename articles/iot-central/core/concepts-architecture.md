---
title: Architekturális fogalmak a Azure IoT Central | Microsoft Docs
description: Ez a cikk az architektúrával kapcsolatos fő fogalmakat Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 12/19/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: bcda4ca252101ed1505f71a1b5f9fe9a0d8d16b9
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728391"
---
# <a name="azure-iot-central-architecture"></a>Az Azure IoT Central architektúrája

Ez a cikk áttekintést nyújt a Azure IoT Central alapfogalmairól.

## <a name="devices"></a>Eszközök

Az eszközök adatokat cserélnek a Azure IoT Central alkalmazással. Egy eszköz a következőre képes:

- Mérések, például telemetria küldése.
- Beállítások szinkronizálása az alkalmazással.

Az Azure IoT Centralban az eszköz és az alkalmazás közti adatcseréhez használt adatokat egy eszközsablonban adhatja meg. További információ az eszközsablonokkal kapcsolatban: [Eszközsablonok.](concepts-device-templates.md)

További információ arról, hogyan csatlakoznak az eszközök Azure IoT Central alkalmazáshoz: [Eszközkapcsolatok.](concepts-get-connected.md)

## <a name="azure-iot-edge-devices"></a>Azure IoT Edge-eszközök

Az [Azure IoT SDK-k](https://github.com/Azure/azure-iot-sdks)használatával létrehozott eszközökön [](../../iot-edge/about-iot-edge.md) kívül Azure IoT Edge eszközöket is csatlakoztathat egy IoT Central alkalmazáshoz. IoT Edge lehetővé teszi a felhőintelligencia és az egyéni logika futtatását közvetlenül a felügyelt IoT-IoT Central. A IoT Edge a következőt teszi lehetővé:

- Számítási feladatok telepítése és frissítése az eszközön.
- Tartsa IoT Edge biztonsági szabványokat az eszközön.
- Győződjön meg arról, IoT Edge modulok mindig futnak.
- A modul állapotának jelentése a felhőbe távoli monitorozáshoz.
- Kezelheti az lefelé irányuló levéleszközök és egy IoT Edge-eszköz, egy IoT Edge-eszközön található modulok, valamint egy IoT Edge-eszköz és a felhő közötti kommunikációt.

![Azure IoT Central és Azure IoT Edge](./media/concepts-architecture/iotedge.png)

IoT Central a következő képességeket teszi elérhetővé a IoT Edge eszközökhöz:

- Eszközsablonok, amelyek egy IoT Edge funkcióit írják le, például:
  - Az üzembe helyezési jegyzék feltöltési képessége, amely segít egy eszközflotta jegyzékfájljának kezelésében.
  - Az eszközön futó IoT Edge futnak.
  - Az egyes modulok által küldött telemetria.
  - Az egyes modulok által jelentéseket küldő tulajdonságok.
  - Az egyes modulok által megválaszolt parancsok.
  - Egy átjáróeszköz IoT Edge lefelé irányuló eszköz közötti kapcsolatok.
  - Felhőtulajdonságok, amelyek nincsenek tárolva a IoT Edge eszközön.
  - Az alkalmazás részét IoT Central testreszabások, irányítópultok és űrlapok.

  További információért tekintse meg a Azure IoT Edge csatlakoztatása egy Azure IoT Central [alkalmazáshoz.](./concepts-iot-edge.md)

- Nagy léptékű IoT Edge kiépítése az Azure IoT eszközátépítési szolgáltatással
- Szabályok és műveletek.
- Egyéni irányítópultok és elemzések.
- Telemetriai adatok folyamatos exportálása IoT Edge eszközökről.

### <a name="iot-edge-device-types"></a>IoT Edge eszköztípusok

IoT Central az IoT Edge osztályba az alábbiak szerint:

- Levéleszközök. Egy IoT Edge eszköznek lehet lefelé irányuló levéleszköze, de ezek az eszközök nincsenek üzembe építve a IoT Central.
- Átjáróeszközök lefelé irányuló eszközökkel. Az átjáróeszköz és az lefelé irányuló eszközök is a IoT Central

![IoT Central áttekintést IoT Edge áttekintést](./media/concepts-architecture/gatewayedge.png)

### <a name="iot-edge-patterns"></a>IoT Edge minták

IoT Central következő eszközmintákat IoT Edge támogatja:

#### <a name="iot-edge-as-leaf-device"></a>IoT Edge levéleszközként

![IoT Edge levéleszközként](./media/concepts-architecture/edgeasleafdevice.png)

A IoT Edge eszköz a IoT Central van kiépítve, és az összes lefelé irányuló eszköz és a telemetria úgy van ábrázolva, mint a IoT Edge eszközről. A IoT Edge csatlakoztatott lefelé irányuló eszközök nincsenek kiépítve a IoT Central.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity"></a>IoT Edge lefelé irányuló eszközökhöz identitással csatlakoztatott átjáróeszköz

![IoT Edge eszközidentitással való adatokat](./media/concepts-architecture/edgewithdownstreamdeviceidentity.png)

Az IoT Edge eszköz a IoT Central az eszközhöz csatlakoztatott lefelé irányuló eszközökkel együtt IoT Edge ki. Az alhálózati eszközök átjárón keresztüli üzembe állásának futásidejű támogatása jelenleg nem támogatott.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity-provided-by-the-iot-edge-gateway"></a>IoT Edge lefelé irányuló eszközökhöz csatlakoztatott átjáróeszköz a IoT Edge által biztosított identitással

![IoT Edge nélküli, lefelé irányuló eszközzel való adatokat](./media/concepts-architecture/edgewithoutdownstreamdeviceidentity.png)

Az IoT Edge eszköz a IoT Central az eszközhöz csatlakoztatott lefelé irányuló eszközökkel együtt IoT Edge ki. Az alhálózati eszközökhöz identitást biztosító átjárók futásidejű támogatása és az lefelé irányuló eszközök kiépítése jelenleg nem támogatott. Ha saját identitásfordítási modult hoz létre, IoT Central ezt a mintát.

## <a name="cloud-gateway"></a>Felhőátjáró

Azure IoT Central felhőátjáróként Azure IoT Hub, amely lehetővé teszi az eszközkapcsolatot. IoT Hub a következőt engedélyezi:

- Nagy léptékű adatbevallódás a felhőben.
- Eszközkezelés.
- Biztonságos eszközkapcsolat.

További információ a IoT Hub: [Azure IoT Hub.](../../iot-hub/index.yml)

Az eszközkapcsolatokkal kapcsolatos további Azure IoT Central: [Eszközkapcsolat.](concepts-get-connected.md)

## <a name="data-stores"></a>Adattárolók

Azure IoT Central alkalmazásadatokat a felhőben tárolja. A tárolt alkalmazásadatok a következőket tartalmazzák:

- Eszközsablonok.
- Eszköz identitása.
- Eszköz metaadatai.
- Felhasználói és szerepköradatok.

Azure IoT Central idősortárat használ az eszközökről küldött mérési adatokhoz. Idősorozat-adatok az elemzési szolgáltatás által használt eszközökről.

## <a name="data-export"></a>Adatexportálás

Egy Azure IoT Central alkalmazásban folyamatosan exportálhatja az adatokat a saját Azure Event Hubs és Azure Service Bus példányaiba. [](howto-export-data.md) Az adatokat rendszeres időközönként exportálhatja az Azure Blob Storage-fiókjába. IoT Central exportálhat méréseket, eszközöket és eszközsablonokat.

## <a name="batch-device-updates"></a>Batch-eszközfrissítések

A Azure IoT Central alkalmazásokban feladatokat hozhat létre és [futtathat a](howto-run-a-job.md) csatlakoztatott eszközök kezeléséhez. Ezekkel a feladatokkal tömegesen frissítéseket is futtathat az eszköztulajdonságokon vagy -beállításokon, vagy parancsokat futtathat. Létrehozhat például egy feladatot több hűtőautomata ventilátorsebességének növeléséhez.

## <a name="role-based-access-control-rbac"></a>Szerepköralapú hozzáférés-vezérlés (RBAC)

Minden IoT Central alkalmazás saját beépített RBAC-rendszerrel rendelkezik. A [rendszergazdák az előre](howto-manage-users-roles.md) meghatározott szerepkörök egyikének Azure IoT Central egyéni szerepkör létrehozásával határozhatnak meg hozzáférési szabályokat egy alkalmazáshoz. A szerepkörök határozzák meg, hogy az alkalmazás mely területeihez férhet hozzá a felhasználó, és milyen műveleteket hajthat végre.

## <a name="security"></a>Biztonság

A biztonsági funkciók a Azure IoT Central a következők:

- Az adatok titkosítva vannak az átvitel során és az adatok nem átvitele során.
- A hitelesítést egy vagy Azure Active Directory Microsoft-fiók biztosítja. A kétfaktoros hitelesítés támogatott.
- Bérlők teljes elkülönítése.
- Eszközszintű biztonság.

## <a name="next-steps"></a>Következő lépések

Most, hogy megismert egy új Azure IoT Central architektúráját, a javasolt következő [](concepts-get-connected.md) lépés a hálózati eszközök kapcsolatának Azure IoT Central.