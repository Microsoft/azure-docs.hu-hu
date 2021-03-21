---
title: Az Azure IoT Hub konfigurációs fájl eszköz-frissítésének ismertetése | Microsoft Docs
description: Az Azure IoT Hub konfigurációs fájl eszköz frissítésének megismerése.
author: ValOlson
ms.author: valls
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 39ecd9d6d0deec942d5c8cce9357c779a4b328d3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101662341"
---
# <a name="device-update-for-iot-hub-configuration-file"></a>IoT Hub konfigurációs fájl eszközének frissítése

A "adu-conf.txt" egy opcionális fájl, amely a következő konfigurációk kezeléséhez hozható létre.

* AzureDeviceUpdateCore: 4. ClientMetadata: 4. deviceProperties ["gyártó"]
* AzureDeviceUpdateCore: 4. ClientMetadata: 4. deviceProperties ["modell"]
* DeviceInformation. gyártó
* DeviceInformation. Model
* Eszköz-csatlakoztatási karakterlánc (ha az eszköz frissítési ügynöke nem ismeri).

## <a name="purpose"></a>Cél
Az eszköz frissítési ügynöke először megpróbálja lekérni a `manufacturer` és az `model` értékeket az eszközről az [interfész réteghez](device-update-agent-overview.md#the-interface-layer)való használatra. Ha ez nem sikerül, az eszköz frissítési ügynöke a következő megkeresi a "adu-conf.txt" fájlt, és az ott található értékeket fogja használni. Ha mindkét próbálkozás sikertelen, az eszköz frissítési ügynöke az [alapértelmezett](https://github.com/Azure/iot-hub-device-update/blob/main/CMakeLists.txt) értékeket fogja használni.

További információ az [adu Alapfelületéről](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/adu_core_interface) és az [eszköz információs felületéről](https://github.com/Azure/iot-hub-device-update/tree/main/src/agent/device_info_interface).

## <a name="file-location"></a>Fájl helye

A Linux rendszeren belül a nevű partícióban vagy lemezen `adu` hozzon létre egy "adu-conf.txt" nevű szövegfájlt a következő mezőkkel.

## <a name="list-of-fields"></a>Mezők listája

|Név|Leírás|
|-----------|--------------------|
|connection_string|Előre kiépített kapcsolati sztring, amelyet az eszköz használhat a IoT Hubhoz való csatlakozáshoz. Megjegyzés: nem szükséges, ha az eszköz frissítési ügynökét az [Azure IoT Identity Service](https://azure.github.io/iot-identity-service/) -ben szeretné kiépíteni|
|aduc_manufacturer|A csatoló a `AzureDeviceUpdateCore:4.ClientMetadata:4` frissítés központi telepítésének célzására szolgáló eszköz besorolására.|
|aduc_model|A csatoló a `AzureDeviceUpdateCore:4.ClientMetadata:4` frissítés központi telepítésének célzására szolgáló eszköz besorolására.|
|gyártó|Az eszköz frissítési ügynöke az illesztőfelület részeként jelentett `DeviceInformation` .|
|modell|Az eszköz frissítési ügynöke az illesztőfelület részeként jelentett `DeviceInformation` .|

## <a name="example-adu-conftxt-file-contents"></a>Példa: "adu-conf.txt" fájl tartalma

```markdown

connection_string = `HostName=<yourIoTHubName>;DeviceId=<yourDeviceId>;SharedAccessKey=<yourSharedAccessKey>`
aduc_manufacturer = <value to send through `AzureDeviceUpdateCore:4.ClientMetadata:4.deviceProperties["manufacturer"]`
aduc_model = <value to send through `AzureDeviceUpdateCore:4.ClientMetadata:4.deviceProperties["model"]`
manufacturer = <value to send through `DeviceInformation.manufacturer`
model = <value to send through `DeviceInformation.manufacturer`
```
