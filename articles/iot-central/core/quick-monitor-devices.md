---
title: Rövid útmutató – az eszközök figyelése az Azure IoT Central
description: Gyors útmutató – operátorként megtudhatja, hogyan használhatja az Azure IoT Central alkalmazást az eszközök figyelésére.
author: dominicbetts
ms.author: dobett
ms.date: 11/16/2020
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 4c63a9833e6b9a9b243d289d79428ddef1468253
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99833881"
---
# <a name="quickstart-use-azure-iot-central-to-monitor-your-devices"></a>Gyors útmutató: az Azure IoT Central használata az eszközök figyelésére

*Ez a cikk operátorokra, fejlesztőkre és rendszergazdákra vonatkozik.*

Ez a rövid útmutató bemutatja, hogyan használhatja a Microsoft Azure IoT Central alkalmazást az eszközök monitorozásához és a beállítások módosításához.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, végezze el a három korábbi rövid útmutatót, [hozzon létre egy Azure IoT Central alkalmazást](./quick-deploy-iot-central.md), [adjon hozzá egy szimulált eszközt a IoT Central alkalmazáshoz](./quick-create-simulated-device.md) , és [konfigurálja az eszköz szabályait és műveleteit](quick-configure-rules.md).

## <a name="receive-a-notification"></a>Értesítés fogadása

Az Azure IoT Central e-mail-üzenetekként küld értesítéseket az eszközökről. Szerkesztőként hozzáadott egy szabályt, amely értesítést küld egy operátornak, amikor egy csatlakoztatott eszköz páratartalma túllépte a küszöbértéket. Kezelőként az e-maileket kell ellenőriznie az értesítésekhez.

Nyissa meg az eszköz rövid útmutatójában a [szabályok és műveletek konfigurálása](quick-configure-rules.md) végén kapott e-mail-üzenetet. Az e-mailben válassza ki az eszközre mutató hivatkozást:

:::image type="content" source="media/quick-monitor-devices/email.png" alt-text="Az értesítő e-mailt megjelenítő képernyőkép":::

Az előző rövid útmutatókban létrehozott szimulált eszköz **áttekintő** nézete a böngészőben nyílik meg:

:::image type="content" source="media/quick-monitor-devices/dashboard.png" alt-text="Az értesítést kiváltó eszköz áttekintését bemutató képernyőkép":::

## <a name="investigate-an-issue"></a>Probléma vizsgálata

Kezelőként megtekintheti az eszközre vonatkozó információkat az **Áttekintés**, a **Névjegy** és a **parancsok** nézetekben. A készítő létrehozott egy **eszköz kezelése** nézetet az eszköz adatainak szerkesztéséhez és az eszköz tulajdonságainak beállításához.

Az irányítópult diagramja az eszköz nedvességtartalmát ábrázolja. Ön dönti el, hogy az eszköz páratartalma túl magas-e.

## <a name="remediate-an-issue"></a>Probléma elhárítása

Az eszköz módosításához használja az **eszköz kezelése** lapot.

Módosítsa a **cél hőmérsékletét** 80-ra az eszköz bemelegítése és a páratartalom csökkentése érdekében. Az eszköz frissítéséhez válassza a **Mentés** lehetőséget. Amikor az eszköz megerősíti a beállítások változását, a tulajdonság állapota **szinkronizálva** értékre változik:

:::image type="content" source="media/quick-monitor-devices/change-settings.png" alt-text="Az eszköz frissített célként megadott hőmérsékleti beállítását megjelenítő képernyőkép":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Következő lépések

Ennek a rövid útmutatónak a segítségével megtanulta a következőket:

* Értesítés fogadása
* Probléma vizsgálata
* Probléma elhárítása

Most, hogy már ismeri az eszköz figyelését, a javasolt következő lépés:

> [!div class="nextstepaction"]
> [Eszköz-sablon létrehozása és kezelése](howto-set-up-template.md).
