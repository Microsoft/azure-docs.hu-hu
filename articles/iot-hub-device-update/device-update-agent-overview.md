---
title: Az Azure IoT Hub Agent eszköz frissítésének ismertetése | Microsoft Docs
description: Az Azure IoT Hub Agent eszköz frissítésének megismerése.
author: ValOlson
ms.author: valls
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: efe5d0171463668bda19a0d0445fc67f3734aaee
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561236"
---
# <a name="device-update-for-iot-hub-agent-overview"></a>Eszközfrissítő az IoT Hub-ügynökhöz – áttekintés

Az eszköz frissítési ügynöke két fogalmi rétegből áll:

* Az interfész réteg az [Azure IoT Plug and Play (PNP)](../iot-pnp/overview-iot-plug-and-play.md) fölé épít, amely lehetővé teszi az üzenetküldést az eszköz frissítési ügynöke és az eszköz frissítési szolgáltatásai között.
* A platform réteg a letöltés, a telepítés és az alkalmazás által meghatározott magas szintű frissítési műveletért felelős, amely platform vagy eszköz lehet.

:::image type="content" source="media/understand-device-update/client-agent-reference-implementations.png" alt-text="Ügynök-implementációk." lightbox="media/understand-device-update/client-agent-reference-implementations.png":::

## <a name="the-interface-layer"></a>Az illesztőfelület réteg

Az illesztőfelület réteget az [adu Alapfelülete](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/adu_core_interface) és az [eszköz információs felülete](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/device_info_interface)alkotja.

Ezek a felületek az alapértelmezett értékek konfigurációs fájljára támaszkodnak. Az alapértelmezett értékek: aduc_manufacturer és aduc_model a DeviceInformation felület AzureDeviceUpdateCore felületéhez, valamint a modellhez és a gyártóhoz. [További információ](device-update-configuration-file.md) a konfigurációs fájlról.

### <a name="adu-core-interface"></a>ADU Core interfész

Az "ADU mag" illesztőfelület az eszköz frissítési ügynöke és szolgáltatásai közötti elsődleges kommunikációs csatorna. [További](device-update-plug-and-play.md#adu-core-interface) információ erről az interfészről.

### <a name="device-information-interface"></a>Eszköz információs felülete

Az eszköz információs felülete az interfész megvalósítására szolgál `Azure IoT PnP DeviceInformation` . [További](device-update-plug-and-play.md#device-information-interface) információ erről az interfészről.

## <a name="the-platform-layer"></a>A platform réteg

A platform rétegnek két implementációja van. A szimulátor platform rétege a frissítési műveletek triviális implementációja, és a IoT Hub szolgáltatások és a telepítő eszközének gyors tesztelésére és értékelésére szolgál. Ha az eszköz frissítési ügynöke a szimulátor platform réteggel van felépítve, akkor az eszköz frissítés-szimulátor ügynökének vagy csak szimulátornak nevezzük. [További](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) információ a szimulátor-ügynök használatáról. A Linux platform rétege a letöltések [továbbítására szolgáló optimalizálással](https://github.com/microsoft/do-client) integrálódik, és az málna PI-referenciák és a Linux rendszeren futó összes ügyfél esetében használatos.

### <a name="simulator-platform-layer"></a>Szimulátor platform réteg

A szimulátor platformjának implementációja a ben található, `src/platform_layers/simulator_platform_layer` és a IoT hub eszköz frissítésének tesztelésére és értékelésére használható.  A "szimulátor" megvalósításának számos művelete ki van gúnyolva, hogy csökkentse a fizikai változásokat a IoT Hub eszköz frissítésével.  A teljes "szimulált" frissítés a platform rétegével végezhető el. [További](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) információ a szimulátor-ügynök futtatásáról.

### <a name="linux-platform-layer"></a>Linux platform réteg

A Linux platform-réteg implementációja megtalálható a ben, `src/platform_layers/linux_platform_layer` és integrálható a [kézbesítési optimalizálási ügyféllel](https://github.com/microsoft/do-client/releases) a letöltésekhez, és az málna PI-referenciák és a Linux rendszeren futó összes ügyfél számára elérhető.

Ez a réteg integrálható különböző frissítési kezelővel a telepítő megvalósításához. A `SWUpdate` frissítési kezelő például egy rendszerhéj-parancsfájlt hív meg, amely a `SWUpdate` végrehajtható fájlra hívja a frissítést.

## <a name="update-handlers"></a>Kezelők frissítése

A frissítési kezelők olyan összetevők, amelyek a frissítés tartalmát vagy telepítő-specifikus részeit kezelik. A frissítési kezelő implementációi a-ben találhatók `src/content_handlers` .

### <a name="simulator-update-handler"></a>Szimulátor frissítési kezelője

A szimulátor-frissítési kezelőt a Simulator platform réteg használja, és a Linux platform rétegével hamis interakciót hajthat végre a Content Handler használatával. A szimulátor-frissítési kezelő a frissítési kezelő API-kat implementálja többnyire No-Ops-vel. A szimulátor-frissítési kezelő megvalósítása a következő címen érhető el:
* [Rendszerkép-frissítési szimulátor](https://github.com/Azure/iot-hub-device-update/blob/main/src/content_handlers/swupdate_handler/inc/aduc/swupdate_simulator_handler.hpp)
* [Csomag frissítése – apt-szimulátor](https://github.com/Azure/iot-hub-device-update/blob/main/src/content_handlers/apt_handler/inc/aduc/apt_simulator_handler.hpp)

Megjegyzés: a AzureDeviceUpdateCore PnP felületének InstalledCriteria mezőjében a tartalom sha256-kivonatának kell lennie. Ez ugyanaz a kivonat, amely szerepel az [import manifest objektumban](import-update.md#create-device-update-import-manifest). [További](device-update-plug-and-play.md) információ `installedCriteria` és az `AzureDeviceUpdateCore` interfész.

### <a name="swupdate-update-handler"></a>`SWUpdate` Kezelő frissítése

A `SWUpdate` frissítési kezelő "integrálható a `SWUpdate` parancssori végrehajtható fájllal és más rendszerhéj-parancsokkal, hogy a/B frissítéseket kifejezetten a málna PI-hivatkozási képhez hozza létre. [Itt](https://github.com/Azure/iot-hub-device-update/releases)megtalálhatja a málna PI-referenciájának legújabb hivatkozási képét. A `SWUpdate` frissítési kezelőt a [src/content_handlers/swupdate_content_handler](https://github.com/Azure/iot-hub-device-update/tree/main/src/content_handlers/swupdate_handler)implementálja.

### <a name="apt-update-handler"></a>APT-frissítési kezelő

Az APT Update kezelője egy APT-specifikus frissítési jegyzékfájlt dolgoz fel, és a megadott Debian-csomag (ok) telepítésére vagy frissítésére vonatkozó APT-t hív meg.

## <a name="self-update-device-update-agent"></a>Az eszköz frissítési ügynökének önkiszolgáló frissítése

Az eszköz frissítési ügynöke és a hozzá tartozó függőségek a IoT Hubi folyamat eszközének frissítésével frissíthetők. Ha lemezkép-alapú frissítést használ, az új rendszerképben adja meg a legújabb eszköz-frissítési ügynököt. Ha csomag alapú frissítést használ, vegye fel az eszköz frissítési ügynökét és a kívánt verzióját az apt manifest-be, például bármely más csomagba. [További](device-update-apt-manifest.md) információ az apt manifestről. Az eszköz frissítési ügynökének és a kézbesítési optimalizálási ügynöknek a telepített verzióját az [IoT Device Twin](../iot-hub/iot-hub-devguide-device-twins.md)eszköz tulajdonságok szakaszában tekintheti meg. [További információ az eszközök tulajdonságairól: adu Core Interface](device-update-plug-and-play.md#device-properties).

## <a name="next-steps"></a>Következő lépések
[Az eszköz frissítési ügynökének konfigurációs fájljának ismertetése](device-update-configuration-file.md)