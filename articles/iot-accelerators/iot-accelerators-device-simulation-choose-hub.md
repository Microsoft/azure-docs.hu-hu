---
title: Meglévő IoT hub használata az eszköz-szimulációs megoldással – Azure | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan konfigurálható az eszköz-szimulációs megoldás gyorsítása meglévő IoT Hub használatára.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 1f89e23d7bb279e7cce5c104060cc7898517f8b7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96009364"
---
# <a name="use-an-existing-iot-hub-with-the-device-simulation-solution-accelerator"></a>Meglévő IoT Hub használata az Eszközszimulációs megoldásgyorsítóval

Az eszköz szimulációjának telepítésekor dönthet úgy is, hogy üzembe helyez egy IoT hub-t a szimulációban való használathoz. Ez a beállítás egy [S2 szintű IoT hubot telepít egyetlen méretezési egységgel](../iot-hub/iot-hub-scaling.md). Ha ezt a választható IoT hubot telepíti, továbbra is megcélozhat egy másik IoT Hub a szimuláció futtatásához.

Ha úgy dönt, hogy nem helyezi üzembe a választható IoT Hub, a futtatott szimulációk saját hubot kell használniuk.

Ha nem rendelkezik IoT-hubhoz, mindig létrehozhat egy újat a [Azure Portalból](https://portal.azure.com).

Egy már meglévő IoT hub használatához szüksége lesz a **iothubowner** megosztott hozzáférési házirendjének kapcsolati karakterláncára. Ezt a [Azure Portal](https://portal.azure.com)-karakterláncot a következővel érheti el:

1. A portál konfiguráció lapján kattintson a **megosztott elérési házirendek** elemre.

1. Kattintson a **iothubowner** elemre.

1. Másolja az elsődleges vagy a másodlagos kapcsolatok sztringjét.

[![A kapcsolatok karakterláncának beolvasása](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-expanded.png#lightbox)

Használja a szimuláció konfigurálásakor másolt kapcsolódási karakterláncot:

![Szimuláció konfigurálása](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation.png)

### <a name="next-steps"></a>Következő lépések

Ebben a útmutatóban megtanulta, hogyan használhat egy meglévő IoT hubot egy szimulációban. Ezt követően érdemes megismerni, hogyan [hozhat létre egy speciális eszköz-modellt](iot-accelerators-device-simulation-advanced-device.md) a szimulációhoz.
