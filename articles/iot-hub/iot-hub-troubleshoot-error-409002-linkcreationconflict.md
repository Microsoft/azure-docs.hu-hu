---
title: Az Azure IoT Hub hibáinak elhárítása 409002 LinkCreationConflict
description: Ismerje meg, hogyan javíthatja a 409002-es hibát a LinkCreationConflict
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: amqp
ms.openlocfilehash: 70b9be6fdb500d9f877659a12e6fdc0e206ea964
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92538221"
---
# <a name="409002-linkcreationconflict"></a>409002 LinkCreationConflict

Ez a cikk a **409002 LinkCreationConflict** -hibák okait és megoldásait ismerteti.

## <a name="symptoms"></a>Hibajelenségek

Az 409002-as hiba a naplók **LinkCreationConflict** , valamint az eszköz leválasztása vagy a felhőből az eszközre küldött üzenet meghibásodása esetén jelenik meg.

<!-- When using AMQP? -->

## <a name="cause"></a>Ok

Ez a hiba általában akkor fordul elő, ha IoT Hub észleli, hogy egy ügyfél több kapcsolatban áll. Abban az esetben, ha egy új kapcsolódási kérelem érkezik egy meglévő csatlakozással rendelkező eszközre, IoT Hub lezárja a meglévő kapcsolódást ezzel a hibával.

### <a name="cause-1"></a>1\. ok

A leggyakoribb esetben a különálló probléma (például az [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)) az eszköz leválasztását okozza. Az eszköz azonnal megpróbálja újból létrehozni a kapcsolatot, de IoT Hub továbbra is a csatlakoztatott eszközt tekinti. IoT Hub bezárja az előző kapcsolatokat, és naplózza ezt a hibát.

### <a name="cause-2"></a>2\. ok

A hibás eszköz-oldalsó logika hatására az eszköz létrehozza a kapcsolatot, ha az egyik már meg van nyitva.

## <a name="solution"></a>Megoldás

Ez a hiba általában egy másik átmeneti probléma mellékhatása jelenik meg, ezért a naplók további hibáit a további hibaelhárítás érdekében keresse. Ellenkező esetben ne felejtsen el új kapcsolódási kérelmet kiadni, ha a kapcsolódás elesik.
