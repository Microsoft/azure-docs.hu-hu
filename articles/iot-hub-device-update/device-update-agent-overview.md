---
title: A Azure IoT Hub eszközfrissítésének | Microsoft Docs
description: A Azure IoT Hub-ügynök eszközfrissítésének
author: ValOlson
ms.author: valls
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 0d97287657b1e1fe7d540e8811c90794aaa5fece
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739565"
---
# <a name="device-update-for-iot-hub-agent-overview"></a>Eszközfrissítő az IoT Hub-ügynökhöz – áttekintés

Az eszközfrissítési ügynök két fogalmi rétegből áll:

* Az interfészréteg az [Azure IoT Plug and Play (PnP)](../iot-pnp/overview-iot-plug-and-play.md) szolgáltatásra épül, és lehetővé teszi az üzenetküldést az eszközfrissítési ügynök és az eszközfrissítési szolgáltatások között.
* A platformréteg felel a Letöltés, Telepítés és Alkalmazás magas szintű frissítési műveletekért, amelyek platform- vagy eszközspecifikusak lehetnek.

:::image type="content" source="media/understand-device-update/client-agent-reference-implementations.png" alt-text="Ügynök-implementációk." lightbox="media/understand-device-update/client-agent-reference-implementations.png":::

## <a name="the-interface-layer"></a>Az interfészréteg

Az interfészréteg az [ADU](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/adu_core_interface) alapillesztőjéről és az eszközinformációs [felületről áll.](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/device_info_interface)

Ezek az interfészek egy konfigurációs fájlra támaszkodnak az alapértelmezett értékekhez. Az alapértelmezett értékek közé aduc_manufacturer és aduc_model az AzureDeviceUpdateCore interfészhez, valamint a DeviceInformation interfész modelljéhez és gyártójához. [További információ a](device-update-configuration-file.md) konfigurációs fájlról.

### <a name="adu-core-interface"></a>Az ADU alapfelülete

Az "ADU Core" felület az eszközfrissítési ügynök és a szolgáltatások közötti elsődleges kommunikációs csatorna. [További információ](device-update-plug-and-play.md#adu-core-interface) erről a felületről.

### <a name="device-information-interface"></a>Eszközinformációs felület

A felület implementálja az eszközinformációs `Azure IoT PnP DeviceInformation` felületet. [További információ](device-update-plug-and-play.md#device-information-interface) erről a felületről.

## <a name="the-platform-layer"></a>A platformréteg

A platformrétegnek két implementációja van. A szimulátorplatform-réteg a frissítési műveletek egyszerű implementációval rendelkezik, és az eszközfrissítés gyors tesztelésére és értékelésére használatos a IoT Hub és beállításához. Ha az eszközfrissítési ügynök a szimulátorplatform-réteggel van felépítve, eszközfrissítési szimulátorügynökként vagy csak szimulátorként hivatkozunk rá. [További információ](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) a szimulátorügynök használatával kapcsolatban. A Linux platformréteg [](https://github.com/microsoft/do-client) a Kézbesítésoptimalizálás integrálható a letöltésekért, és a Raspberry Pi referencia-rendszerképben, valamint a Linux rendszeren futó összes ügyfélben használható.

### <a name="simulator-platform-layer"></a>Szimulátorplatform-réteg

A szimulátorplatform-réteg implementációja a(on) alatt található, és használható az eszközfrissítés tesztelésére és értékelésére a `src/platform_layers/simulator_platform_layer` IoT Hub.  A "szimulátor" implementáció számos műveletét utánzik, hogy csökkentse a fizikai módosításokat az eszközfrissítéssel való kísérletezés IoT Hub.  Ezzel a platformréteggel elvégezhető egy végpontok között "szimulált" frissítés. [További információ](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) a szimulátorügynök futtatásáról.

### <a name="linux-platform-layer"></a>Linux-platformréteg

A Linux platformréteg implementációja a fájlban található, és a letöltéshez integrálható a `src/platform_layers/linux_platform_layer` [Kézbesítésoptimalizálás-ügyféllel,](https://github.com/microsoft/do-client/releases) és a Raspberry Pi referencia-rendszerképben, valamint a Linux rendszeren futó összes ügyfélben használható.

Ez a réteg különböző frissítési kezelők integrálásával valósítható meg a telepítő. Az Update Handler például meghív egy héjszkprogramot, amely meghívja a fájlt a végrehajtható fájlba `SWUpdate` `SWUpdate` egy frissítés végrehajtásához.

## <a name="update-handlers"></a>Kezelők frissítése

A frissítési kezelők olyan összetevők, amelyek a frissítés tartalmát vagy telepítőspecifikus részeit kezelik. A frissítési kezelő implementációi a következőben `src/content_handlers` vannak: .

### <a name="simulator-update-handler"></a>Szimulátorfrissítés-kezelő

A szimulátorfrissítés-kezelőt a szimulátorplatform-réteg használja, és a Linux platformréteggel együtt használható a tartalomkezelővel való hamis interakciókhoz. A szimulátorfrissítés-kezelő főleg művelet nélküli frissítéskezelő API-kat implementál. A szimulátorfrissítés-kezelő implementációja az alábbiakban található:
* [Képfrissítési szimulátor](https://github.com/Azure/iot-hub-device-update/blob/main/src/content_handlers/swupdate_handler/inc/aduc/swupdate_simulator_handler.hpp)
* [Csomagfrissítési apt-szimulátor](https://github.com/Azure/iot-hub-device-update/blob/main/src/content_handlers/apt_handler/inc/aduc/apt_simulator_handler.hpp)

Megjegyzés: Az AzureDeviceUpdateCore PnP interfész InstalledCriteria mezőjének a tartalom sha256 kivonatának kell lennie. Ez ugyanaz a kivonat, amely az Import [Manifest Object objektumban is jelen van.](import-update.md#create-a-device-update-import-manifest) [További információ a](device-update-plug-and-play.md) és `installedCriteria` a `AzureDeviceUpdateCore` felületről.

### <a name="swupdate-update-handler"></a>`SWUpdate` Kezelő frissítése

Az Update Handler" (Frissítéskezelő) integrálható a parancssori végrehajtható fájlba és más héjparancsokbe, így kifejezetten a Raspberry Pi referencia-rendszerképhez implementálja az `SWUpdate` `SWUpdate` A/B frissítéseket. A Raspberry Pi legújabb referenciaképét itt [találja.](https://github.com/Azure/iot-hub-device-update/releases) Az `SWUpdate` Update Handler az [src/content_handlers/swupdate_content_handler.](https://github.com/Azure/iot-hub-device-update/tree/main/src/content_handlers/swupdate_handler)

### <a name="apt-update-handler"></a>APT-frissítéskezelő

Az APT-frissítéskezelő feldolgoz egy APT-specifikus frissítési jegyzékfájlt, és meghívja az APT-t a megadott Debian-csomagok telepítéséhez vagy frissítéséhez.

## <a name="self-update-device-update-agent"></a>Önfrissítési eszközfrissítési ügynök

Az eszközfrissítési ügynök és függőségei az eszközfrissítési folyamat eszközfrissítésével IoT Hub frissíthetők. Ha rendszerképalapú frissítést használ, az új rendszerképbe foglalja bele a legújabb eszközfrissítési ügynököt. Ha csomagalapú frissítést használ, a többi csomaghoz hasonló módon foglalja bele az eszközfrissítési ügynököt és annak kívánt verzióját az apt-jegyzékfájlba. [További információ az](device-update-apt-manifest.md) apt jegyzékfájlról. Az Eszközfrissítési ügynök és a Kézbesítésoptimalizálás ügynök telepített verzióját az IoT-ikereszköz Eszköztulajdonságok [szakaszában ellenőrizheti.](../iot-hub/iot-hub-devguide-device-twins.md) [További információ az eszköztulajdonságokról az ADU Core Interface alatt.](device-update-plug-and-play.md#device-properties)

## <a name="next-steps"></a>Következő lépések
[Az eszközfrissítési ügynök konfigurációs fájljának a használata](device-update-configuration-file.md)