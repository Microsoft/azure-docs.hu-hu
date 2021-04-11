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
ms.openlocfilehash: cd531abe1a10abead26055c6b0d4dd328cd3e836
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061366"
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