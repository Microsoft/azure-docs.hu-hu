---
title: Eszköz frissítése IoT Hub hálózati követelményekhez | Microsoft Docs
description: A IoT Hub eszköz frissítése különféle hálózati portokat használ különböző célokra.
author: philmea
ms.author: philmea
ms.date: 1/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 0512308fbaa0a725c6ecca573c70c90d8c04e247
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558380"
---
# <a name="ports-used-with-device-update-for-iot-hub"></a>Az IoT Hub eszköz frissítésével használt portok
A ADU különféle hálózati portokat használ különböző célokra.

## <a name="default-ports"></a>Alapértelmezett portok

Cél|Portszám |
---|---
Letöltés hálózatokról/CDNs  | 80 (HTTP protokoll)
Letöltés az MCC/CDNs | 80 (HTTP protokoll)
ADU-ügynök kapcsolódása az Azure IoT Hubhoz  | 8883 (MQTT protokoll)

## <a name="use-azure-iot-hub-supported-protocols"></a>Az Azure IoT Hub támogatott protokolljainak használata
Az ADU-ügynök úgy módosítható, hogy a támogatott Azure IoT Hub protokollok bármelyikét használhassa.

[További](../iot-hub/iot-hub-devguide-protocols.md) információ a támogatott protokollok aktuális listájáról.
