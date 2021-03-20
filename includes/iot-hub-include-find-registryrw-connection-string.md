---
title: fájl belefoglalása
description: fájl belefoglalása
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 3386cb51a8a728576f6615002d6154d89ca662c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95993316"
---
<!-- This tells how to get the connection string for the registryReadWrite shared access policy of your IoT hub -->

A **registryReadWrite** szabályzat IoT hub kapcsolódási karakterláncának beszerzéséhez kövesse az alábbi lépéseket:

1. A [Azure Portal](https://portal.azure.com)válassza az **erőforráscsoportok** lehetőséget. Válassza ki azt az erőforráscsoportot, amelyben a hub található, majd válassza ki az elosztót az erőforrások listájából.

2. A központ bal oldali ablaktábláján válassza a **megosztott elérési szabályzatok** elemet.

3. A házirendek listájából válassza ki a **registryReadWrite** házirendet.

4. A **megosztott elérési kulcsok** területen válassza a **kapcsolati sztring – elsődleges kulcs** másolási ikonját, és mentse az értéket.

    ![A kapcsolati karakterlánc lekérésének megjelenítése](./media/iot-hub-include-find-registryrw-connection-string/iot-hub-get-connection-vs2019.png)

További információ a IoT Hub megosztott hozzáférési szabályzatokról és engedélyekről: [hozzáférés-vezérlés és engedélyek](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
