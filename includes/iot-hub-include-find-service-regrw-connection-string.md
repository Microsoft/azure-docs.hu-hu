---
title: fájl belefoglalása
description: fájl belefoglalása
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 04/03/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 1a0502568c1673bcd7f57d3e9bc9c95ed90bbefa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "80756917"
---
<!-- This tells how to create a custom shared access policy that has service connect and registry RW permissions for your IoT hub and get the connection string for it-->

Ha olyan megosztott hozzáférési szabályzatot szeretne létrehozni, amely biztosítja a **szolgáltatás kapcsolódását** és a **beállításjegyzék írási** engedélyeit, és a Szabályzathoz tartozó kapcsolati karakterláncot kap, kövesse az alábbi lépéseket:

1. A [Azure Portal](https://portal.azure.com)válassza az **erőforráscsoportok** lehetőséget. Válassza ki azt az erőforráscsoportot, amelyben a hub található, majd válassza ki az elosztót az erőforrások listájából.

1. A központ bal oldali ablaktábláján válassza a **megosztott elérési szabályzatok** elemet.

1. A szabályzatok listájának felső menüjében válassza a **Hozzáadás** lehetőséget.

1. A **megosztott elérési házirend hozzáadása** területen adjon meg egy leíró nevet a szabályzatnak, például *serviceAndRegistryReadWrite*. Az **engedélyek** területen válassza a **beállításjegyzék írása** és a **szolgáltatás közötti kapcsolat** lehetőséget, majd válassza a **Létrehozás** lehetőséget. (A **beállításjegyzék** írási engedélyét automatikusan belefoglaljuk, amikor kiválasztja a **beállításjegyzék írását**.)

    ![Új megosztott elérési házirend hozzáadásának megjelenítése](./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-add-svc-regrw-policy.png)

1. Válassza ki az új szabályzatot a házirendek listájából.

1. A **megosztott elérési kulcsok** területen válassza a **kapcsolati sztring – elsődleges kulcs** másolási ikonját, és mentse az értéket.

    ![A kapcsolati karakterlánc lekérésének megjelenítése](./media/iot-hub-include-find-service-regrw-connection-string/iot-hub-get-connection-string.png)

További információ a IoT Hub megosztott hozzáférési szabályzatokról és engedélyekről: [hozzáférés-vezérlés és engedélyek](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
