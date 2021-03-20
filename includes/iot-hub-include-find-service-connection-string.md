---
title: fájl belefoglalása
description: fájl belefoglalása
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 07/16/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 55cdd864d73ce084d994c64233e79d5a58b17def
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "69558448"
---
<!-- This tells how to get the connection string for the service shared access policy of your IoT hub -->

A **szolgáltatási** házirendhez tartozó IoT hub kapcsolódási karakterlánc beszerzéséhez kövesse az alábbi lépéseket:

1. A [Azure Portal](https://portal.azure.com)válassza az **erőforráscsoportok** lehetőséget. Válassza ki azt az erőforráscsoportot, amelyben a hub található, majd válassza ki az elosztót az erőforrások listájából.

1. Az IoT hub bal oldali ablaktábláján válassza a **megosztott hozzáférési házirendek** elemet.

1. A házirendek listájából válassza ki a **szolgáltatási** házirendet.

1. A **megosztott elérési kulcsok** területen válassza a **kapcsolati sztring – elsődleges kulcs** másolási ikonját, és mentse az értéket.

    ![A kapcsolati karakterlánc lekérésének megjelenítése](./media/iot-hub-include-find-service-connection-string/iot-hub-get-connection-string.png)

További információ a IoT Hub megosztott hozzáférési szabályzatokról és engedélyekről: [hozzáférés-vezérlés és engedélyek](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
