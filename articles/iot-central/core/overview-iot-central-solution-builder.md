---
title: Megoldás-létrehozás az Azure IoT Centralhoz | Microsoft Docs
description: Az Azure IoT Central egy IoT-alkalmazásplatform, amely megkönnyíti IoT-megoldások létrehozását. Ez a cikk áttekintést nyújt az integrált megoldások IoT Central segítségével történő létrehozásáról.
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 72aa8e5e3284e0ee7fbe63e0fb617b9eba03292e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100417802"
---
# <a name="iot-central-solution-builder-guide"></a>IoT Central megoldás-előkészítő útmutató

*Ez a cikk a megoldás-építők esetében érvényes.*

Egy IoT Central alkalmazás lehetővé teszi több millió eszköz figyelését és kezelését életciklusuk során. Ez az útmutató olyan megoldás-építők számára készült, akik a IoT Central használatával integrált megoldásokat építhetnek ki. Az IoT Central alkalmazások lehetővé teszi az eszközök kezelését, az eszköz telemetria elemzését és más háttér-szolgáltatásokkal való integrálását.

Egy megoldás-szerkesztő:

- Az irányítópultokat és nézeteket konfigurálja a IoT Central webes felhasználói felületén.
- A beépített szabályok és elemzési eszközök segítségével az üzleti elemzéseket származtathatja a csatlakoztatott eszközökről.
- Az adatexportálási és-szabályi képességeket használja a IoT Central más háttér-szolgáltatásokkal való integrálására.

## <a name="configure-dashboards-and-views"></a>Irányítópultok és nézetek konfigurálása

Egy IoT Central alkalmazás egy vagy több irányítópulttal is rendelkezhet, amelyeket az operátor az alkalmazás megtekintésére és használatára használ. Megoldás-szerkesztőként testreszabhatja az alapértelmezett irányítópultot, és létrehozhat speciális irányítópultokat:

- A testreszabott irányítópultok néhány példájának megtekintéséhez tekintse meg az [iparági fókuszált sablonok](concepts-app-templates.md#industry-focused-templates)című témakört.
- További információ az irányítópultokról: [több irányítópult létrehozása és kezelése](howto-create-personal-dashboards.md) és [az alkalmazás irányítópultjának konfigurálása](howto-add-tiles-to-your-dashboard.md).

Amikor egy eszköz csatlakozik egy IoT Centralhoz, az eszköz társítva van az eszköz típusához tartozó eszköz sablonnal. Az eszközök sablonja testreszabható nézeteket tartalmaz, amelyeket az operátor az egyes eszközök kezelésére használ. Megoldás fejlesztőként létrehozhatja és testreszabhatja az eszköz típusához elérhető nézeteket. További információt a [nézetek hozzáadása](howto-set-up-template.md#add-views)című témakörben talál.

## <a name="use-built-in-rules-and-analytics"></a>Beépített szabályok és elemzések használata

A megoldás fejlesztője szabályokat adhat hozzá egy olyan IoT Central alkalmazáshoz, amely testreszabható műveleteket futtat. A szabályok az eszközről érkező adatok alapján értékelik ki a feltételeket az adott művelet futtatásának megállapításához. A szabályokkal kapcsolatos további információkért lásd:

- [Oktatóanyag: Szabály létrehozása és értesítések beállítása az Azure IoT Central-alkalmazásban](tutorial-create-telemetry-rules.md)
- [Webhookműveletek létrehozása szabályokon az Azure IoT Centralban](howto-create-webhooks.md)
- [Több művelet csoportosítása egy vagy több szabályból való futtatáshoz](howto-use-action-groups.md)

A IoT Central beépített elemzési képességekkel rendelkezik, amelyek segítségével az operátor elemezheti a csatlakoztatott eszközökről áramló adatfolyamokat. További információért lásd: [az eszközök](howto-create-analytics.md)elemzésének használata

## <a name="integrate-with-other-services"></a>Integráció más szolgáltatásokkal

Megoldás-szerkesztőként a IoT Central adatexportálási és-szabályi képességeivel integrálhat más szolgáltatásokkal. További információ:

- [IoT-adatexportálás a Felhőbeli célhelyekre az adatexportálás használatával](howto-export-data.md)
- [Munkafolyamatok használata az Azure IoT Central alkalmazás integrálásához más felhőalapú szolgáltatásokkal](howto-configure-rules-advanced.md)
- [Az Azure IoT Central kibővítése egyéni szabályokkal a Stream Analytics, az Azure Functions és a SendGrid használatával](howto-create-custom-rules.md)
- [Az Azure IoT Central kiterjesztése egyéni elemzésekkel Azure Databricks használatával](howto-create-custom-analytics.md)
- [Azure IoT Central-beli adatPower BI-irányítópulton tárolt adatai megjelenítése és elemzése](howto-connect-powerbi.md)

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a IoT Central használatáról, a javasolt következő lépések az [Azure IoT Central-alkalmazás létrehozása](./quick-deploy-iot-central.md)című rövid útmutatók kipróbálása.
