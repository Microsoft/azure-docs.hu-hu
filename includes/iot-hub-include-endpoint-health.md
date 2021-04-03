---
title: fájl belefoglalása
description: fájl belefoglalása
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 24a07109fc8f4d6ebd283dee7ee00107f0eb49b7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95564258"
---
A végpontok állapotának beolvasásához használja a REST API a [végpont állapota](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) lehetőséget. Azt javasoljuk, hogy az útválasztási üzenet késéséhez kapcsolódó [IoT hub útválasztási metrikák](../articles/iot-hub/monitor-iot-hub-reference.md#routing-metrics) használatával azonosítsa és hibakeresési hibákat, amikor a végpont állapota halott vagy sérült, mivel a késés várhatóan magasabb, ha a végpont az egyik államban van. Ha többet szeretne megtudni IoT Hub metrikák használatáról, tekintse meg a [IoT hub figyelése](../articles/iot-hub/monitor-iot-hub.md)című témakört.

|Állapotadatok|Leírás|
|---|---|
|kifogástalan|A végpont fogadja az üzeneteket a várt módon.|
|sérült|A végpont nem fogad üzeneteket, és IoT Hub megpróbálja elküldeni az üzeneteket erre a végpontra.|
|ismeretlen|IoT Hub nem próbált üzeneteket kézbesíteni erre a végpontra.|
|leromlott|A végpont fogadja az üzeneteket a vártnál lassabban, vagy sérült állapotból helyreállítja.|
|kézbesítetlen|A IoT Hub már nem kézbesít üzeneteket erre a végpontra. Az üzenetek erre a végpontra való küldésének újrapróbálása sikertelen volt.|