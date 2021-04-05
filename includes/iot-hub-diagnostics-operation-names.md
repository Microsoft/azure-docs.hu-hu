---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 04/28/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 7d6718fb25b3743a50c52f11c8e19d80839b485c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95554430"
---
<!-- operation names for the diag logs for IoT Hub -->

|Művelet neve|Level|Leírás|
|------------- |-----|-----------|
|UndefinedRouteEvaluation|Tájékoztatás|Az üzenet nem értékelhető ki a megadott feltétellel. Ha például az útvonal lekérdezési feltételének egyik tulajdonsága hiányzik az üzenetből. További információ az [útválasztási lekérdezés szintaxisáról](../articles/iot-hub/iot-hub-devguide-routing-query-syntax.md).|
|RouteEvaluationError|Hiba|Hiba történt az üzenet kiértékelése során az üzenet formátuma miatt. Ez a hiba például akkor kerül naplózásra, ha a tartalom kódolása nem lett megadva, vagy a tartalomtípus nem érvényes az üzenetben. Ezeket a [Rendszertulajdonságokban](../articles/iot-hub/iot-hub-devguide-routing-query-syntax.md#system-properties)kell megadni.|
|DroppedMessage|Hiba|Az üzenet el lett dobva, és nem lett átirányítva. Ennek oka az lehet, hogy az üzenet nem felelt meg valamelyik útválasztási lekérdezésnek vagy végpontnak, és több újrapróbálkozás után nem lehetett kézbesíteni az üzenetet. Javasoljuk, hogy a végpont [állapotának lekérése](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth)REST API használatával további részleteket kapjon a végpontról.|
|EndpointUnhealthy|Hiba|A végpont nem fogad üzenetet a IoT Hubból, és IoT Hub megpróbálja újra elküldeni az üzeneteket. Azt javasoljuk, hogy az utolsó ismert hibát a [végpont állapotának lekérése](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth)REST API keresztül figyelje.|
|EndpointDead|Hiba|A végpont nem fogad üzenetet több mint egy óráig IoT Hubról. Azt javasoljuk, hogy az utolsó ismert hibát a [végpont állapotának lekérése](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth)REST API keresztül figyelje.|
|EndpointHealthy|Tájékoztatás|A végpont kifogástalan állapotú, és üzeneteket fogad IoT Hub. Ez az üzenet nem kerül folyamatos naplózásra, de csak akkor, ha a végpont Kifogástalan állapotba kerül. Ez az üzenet azt jelenti, IoT Hub nem tudott üzeneteket küldeni a végpontnak, de a végpont állapota már kifogástalan.|
|OrphanedMessage|Tájékoztatás|Az üzenet nem felel meg egyetlen útvonalnak sem.|
|InvalidMessage|Hiba|Az üzenet érvénytelen a végponttal való inkompatibilitás miatt. Javasoljuk a végpont konfigurációjának ellenőrzését.|


Az Operations *UndefinedRouteEvaluation*, a *RouteEvaluationError* és a *OrphanedMessage* több, mint percenként egyszer van Leszabályozva és naplózva, IoT hub.