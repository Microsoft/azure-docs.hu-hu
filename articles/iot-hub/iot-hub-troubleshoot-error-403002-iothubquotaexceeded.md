---
title: Az Azure IoT Hub hibáinak elhárítása 403002 IoTHubQuotaExceeded
description: Ismerje meg, hogyan javíthatja a 403002-es hibát a IoTHubQuotaExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 3521933baa8dc328910fbacd8b1b6768832fa5f1
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061315"
---
# <a name="403002-iothubquotaexceeded"></a>403002 IoTHubQuotaExceeded

Ez a cikk a **403002 IoTHubQuotaExceeded** -hibák okait és megoldásait ismerteti.

## <a name="symptoms"></a>Hibajelenségek

A IoT Hub összes kérése meghiúsul a 403002-as  **IoTHubQuotaExceeded**. Azure Portal az IoT hub-eszközök listája nem töltődik be.

## <a name="cause"></a>Ok

Túllépte az IoT hub napi üzenetre vonatkozó kvótáját. 

## <a name="solution"></a>Megoldás

[Frissítse vagy növelje az IoT hub egységeit](iot-hub-upgrade.md) , vagy várjon a következő UTC napra a napi kvóta frissítéséhez.

## <a name="next-steps"></a>Következő lépések

* Ha szeretné megtudni, hogy a műveletek hogyan számítanak bele a kvótába, mint például a Twin-lekérdezések és a közvetlen metódusok, tekintse meg a [IoT hub díjszabását](iot-hub-devguide-pricing.md#charges-per-operation)
* A napi kvóta-használat figyelésének beállításához állítson be egy riasztást a *felhasznált üzenetek teljes számával*. Részletes útmutatásért lásd: [mérőszámok és riasztások beállítása IoT hub](tutorial-use-metrics-and-diags.md#set-up-metrics) használatával