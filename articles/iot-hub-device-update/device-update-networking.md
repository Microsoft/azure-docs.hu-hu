---
title: Eszköz frissítése IoT Hub hálózati követelményekhez | Microsoft Docs
description: A IoT Hub eszköz frissítése különféle hálózati portokat használ különböző célokra.
author: philmea
ms.author: philmea
ms.date: 1/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: e72ff144a56f44ccaa695b7dab328e42052fce39
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101679586"
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

[További](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols#:~:text=Table%202%20%20%20,%201%20more%20rows) információ a támogatott protokollok aktuális listájáról.
