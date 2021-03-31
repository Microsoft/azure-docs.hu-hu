---
title: Az Azure IoT Hub hibáinak elhárítása 400027 ConnectionForcefullyClosedOnNewConnection
description: Ismerje meg, hogyan javíthatja a 400027-es hibát a ConnectionForcefullyClosedOnNewConnection
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom:
- mqtt
- fasttrack-edit
- iot
ms.openlocfilehash: e5d1dc345c72d77be6172fb9c3a10eb2f38d186a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94506328"
---
# <a name="400027-connectionforcefullyclosedonnewconnection"></a>400027 ConnectionForcefullyClosedOnNewConnection

Ez a cikk a **400027 ConnectionForcefullyClosedOnNewConnection** -hibák okait és megoldásait ismerteti.

## <a name="symptoms"></a>Hibajelenségek

Az eszköz a .NET SDK-val és a MQTT átviteli típussal való **ConnectionStatusChangeReason** megszakadt **Communication_Error** .

Az eszközről a felhőbe irányuló kettős művelet (például a Read vagy a patch jelentett tulajdonságai) vagy a közvetlen metódus meghívása meghiúsul a **400027** hibakód esetén.

## <a name="cause"></a>Ok

Egy másik ügyfél létrehozott egy új kapcsolódást IoT Hub ugyanazzal a hitelesítő adatokkal, így IoT Hub lezárta az előző kapcsolódást. IoT Hub nem engedélyezi, hogy egynél több ügyfél csatlakozhasson ugyanazzal a hitelesítő adatokkal.

## <a name="solution"></a>Megoldás

Győződjön meg arról, hogy minden ügyfél a saját identitásával csatlakozik a IoT Hubhoz.