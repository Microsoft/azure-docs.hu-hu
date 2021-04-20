---
title: Megoldás kiépítése Azure IoT Central | Microsoft Docs
description: Az Azure IoT Central egy IoT-alkalmazásplatform, amely megkönnyíti IoT-megoldások létrehozását. Ez a cikk áttekintést nyújt az integrált megoldások IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: e762b8c2e2d7f72b89629c520560b205cedcd036
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728555"
---
# <a name="iot-central-solution-builder-guide"></a>IoT Central útmutató a megoldáskészítők számára

*Ez a cikk a megoldáskészítőkre vonatkozik.*

Egy IoT Central alkalmazással eszközök millióit figyelheti és kezelheti azok életciklusa során. Ez az útmutató olyan megoldáskészítők számára nyújt útmutatást, IoT Central integrált megoldásokat hoznak létre. A IoT Central lehetővé teszi az eszközök kezelését, az eszköz telemetriai adatainak elemzését és más háttérszolgáltatásokkal való integrációt.

Megoldáskészítő:

- Irányítópultokat és nézeteket konfigurál a IoT Central felhasználói felületén.
- A beépített szabályok és elemzési eszközök használatával üzleti elemzéseket nyer ki a csatlakoztatott eszközökből.
- Az adatexport és a szabályok képességeinek segítségével integrálja IoT Central más háttérszolgáltatásokkal.

## <a name="configure-dashboards-and-views"></a>Irányítópultok és nézetek konfigurálása

A IoT Central egy vagy több irányítópultot is használhatnak, amelyek használatával az operátorok megtekinthetik és interakcióba léphetnek az alkalmazással. Megoldáskészítőként testre szabhatja az alapértelmezett irányítópultot, és speciális irányítópultokat hozhat létre:

- A testreszabott irányítópultok néhány példáját lásd: [Iparág-központú sablonok.](concepts-app-templates.md#industry-focused-templates)
- Az irányítópultokkal kapcsolatos további információkért lásd: [Több](howto-create-personal-dashboards.md) irányítópult létrehozása és kezelése és [Az alkalmazás irányítópultjának konfigurálása.](howto-add-tiles-to-your-dashboard.md)

Amikor egy eszköz csatlakozik egy IoT Central, az eszköz az eszköztípushoz tartozó eszközsablonhoz lesz társítva. Az eszközsablonok testre szabható nézetekkel rendelkezik, amelyek segítségével az operátorok kezelhetik az egyes eszközöket. Megoldásfejlesztőként létrehozhatja és testre szabhatja az eszköztípushoz elérhető nézeteket. További információ: [Nézetek hozzáadása.](howto-set-up-template.md#add-views)

## <a name="use-built-in-rules-and-analytics"></a>Beépített szabályok és elemzések használata

A megoldásfejlesztők testre szabható műveleteket futtató IoT Central adhatnak hozzá szabályokat egy alkalmazáshoz. A szabályok az eszközről érkező adatok alapján értékelik ki a feltételeket annak meghatározásához, hogy mikor fusson egy művelet. A szabályokkal kapcsolatos további információkért lásd:

- [Oktatóanyag: Szabály létrehozása és értesítések beállítása az Azure IoT Central-alkalmazásban](tutorial-create-telemetry-rules.md)
- [Webhookműveletek létrehozása szabályokon az Azure IoT Centralban](howto-create-webhooks.md)
- [Több művelet csoportosítása egy vagy több szabályból való futtatáshoz](howto-use-action-groups.md)

IoT Central beépített elemzési képességekkel rendelkezik, amelyek segítségével az operátor elemezheti a csatlakoztatott eszközökről áramló adatokat. További tudnivalókért lásd: [Az eszközadatok elemzése az elemzésekkel.](howto-create-analytics.md)

## <a name="integrate-with-other-services"></a>Integráció más szolgáltatásokkal

Megoldáskészítőként használhatja az adatexport- és szabályexport képességeket a IoT Central más szolgáltatásokkal való integrációhoz. További információ:

- [IoT-adatok exportálása felhőbeli célhelyre adatexportáció használatával](howto-export-data.md)
- [Adatok átalakítása IoT Central](howto-transform-data.md)
- [Munkafolyamatok használata a Azure IoT Central más felhőszolgáltatásokkal való integrálásához](howto-configure-rules-advanced.md)
- [Az Azure IoT Central kibővítése egyéni szabályokkal a Stream Analytics, az Azure Functions és a SendGrid használatával](howto-create-custom-rules.md)
- [Az Azure IoT Central kibővítése egyéni elemzésekkel az Azure Databricks használatával](howto-create-custom-analytics.md)
- [A vizualizáció és Azure IoT Central vizualizációja és elemzése egy Power BI irányítópulton](howto-connect-powerbi.md)

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a IoT Central használatával kapcsolatban, a következő javasolt lépések a gyorsútmutatók, kezdve a Create an Azure IoT Central application (Új alkalmazás [létrehozása) Azure IoT Central lépésekkel.](./quick-deploy-iot-central.md)
