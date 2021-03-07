---
title: Az Azure IoT Hub hibáinak elhárítása 412002 DeviceMessageLockLost
description: Ismerje meg, hogyan javíthatja a 412002-es hibát a DeviceMessageLockLost
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 53364009f9b9c041c39728e438c3e24eacfd1665
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102435477"
---
# <a name="412002-devicemessagelocklost"></a>412002 DeviceMessageLockLost

Ez a cikk a **412002 DeviceMessageLockLost** -hibák okait és megoldásait ismerteti.

## <a name="symptoms"></a>Hibajelenségek

A felhőből az eszközre irányuló üzenetek küldésére tett kísérlet során a kérelem meghiúsul a **412002**-es hiba DeviceMessageLockLost.

## <a name="cause"></a>Ok

Ha egy eszköz egy felhőből az eszközre irányuló üzenetet kap a sorból (például a használatával [`ReceiveAsync()`](/dotnet/api/microsoft.azure.devices.client.deviceclient.receiveasync) ), az üzenetet IoT hub zárolja a zárolási időkorlát időtartamára egy percen belül. Ha az eszköz a zárolási időkorlát lejárta után megpróbálja befejezni az üzenetet, IoT Hub eldönti ezt a kivételt.

## <a name="solution"></a>Megoldás

Ha IoT Hub nem kap értesítést az egyperces Zárolási időkorlát időtartamán belül, az üzenetet visszaállítja *várólistán lévő* állapotba. Az eszköz megpróbálhatja újra fogadni az üzenetet. Ha meg szeretné akadályozni, hogy a hiba a jövőben is megtörténjen, hajtsa végre az eszköz oldalának logikáját az üzenet fogadása után egy percen belül. Ez az egyperces időtúllépés nem módosítható.