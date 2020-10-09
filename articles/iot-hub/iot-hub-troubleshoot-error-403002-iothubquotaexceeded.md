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
ms.openlocfilehash: 8312c3267e826088f34b3bffe1520703eec00bdc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "76961113"
---
# <a name="403002-iothubquotaexceeded"></a>403002 IoTHubQuotaExceeded

Ez a cikk a **403002 IoTHubQuotaExceeded** -hibák okait és megoldásait ismerteti.

## <a name="symptoms"></a>Hibajelenségek

A IoT Hub összes kérése meghiúsul a 403002-as  **IoTHubQuotaExceeded**. Azure Portal az IoT hub-eszközök listája nem töltődik be.

## <a name="cause"></a>Ok

Túllépte az IoT hub napi üzenetre vonatkozó kvótáját. 

## <a name="solution"></a>Megoldás

[Frissítse vagy növelje az IoT hub egységeit](iot-hub-upgrade.md) , vagy várjon a következő UTC napra a napi kvóta frissítéséhez.

## <a name="next-steps"></a>További lépések

* Ha szeretné megtudni, hogy a műveletek hogyan számítanak bele a kvótába, mint például a Twin-lekérdezések és a közvetlen metódusok, tekintse meg a [IoT hub díjszabását](iot-hub-devguide-pricing.md#charges-per-operation)
* A napi kvóta-használat figyelésének beállításához állítson be egy riasztást a *felhasznált üzenetek teljes számával*. Részletes útmutatásért lásd: [mérőszámok és riasztások beállítása IoT hub](tutorial-use-metrics-and-diags.md#set-up-metrics) használatával