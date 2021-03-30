---
title: fájl belefoglalása
description: fájl belefoglalása
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 11/02/2018
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 6f43bbcd83861f7d39de2aa89bbe035c2ff5b809
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "70050411"
---
<!-- This tells how to create a custom shared access policy for your IoT hub and get the connection string for it-->

Ha olyan megosztott hozzáférési szabályzatot szeretne létrehozni, amely biztosítja a **szolgáltatás kapcsolódását** és a **beállításjegyzék olvasási** engedélyeit, és lekéri a Szabályzathoz tartozó kapcsolati karakterláncot, kövesse az alábbi lépéseket:

1. A [Azure Portal](https://portal.azure.com)válassza az **erőforráscsoportok** lehetőséget. Válassza ki azt az erőforráscsoportot, amelyben a hub található, majd válassza ki az elosztót az erőforrások listájából.

1. A központ bal oldali ablaktábláján válassza a **megosztott elérési szabályzatok** elemet.

1. A szabályzatok listájának felső menüjében válassza a **Hozzáadás** lehetőséget.

1. A **megosztott elérési házirend hozzáadása** területen adjon meg egy leíró nevet a szabályzatnak, például *serviceAndRegistryRead*. Az **engedélyek** területen válassza a **beállításjegyzék olvasási** és **szolgáltatás-csatlakozási** lehetőséget, majd válassza a **Létrehozás** lehetőséget.

    ![Új megosztott elérési házirend hozzáadásának megjelenítése](./media/iot-hub-include-find-custom-connection-string/iot-hub-add-custom-policy.png)

1. Válassza ki az új szabályzatot a házirendek listájából.

1. A **megosztott elérési kulcsok** területen válassza a **kapcsolati sztring – elsődleges kulcs** másolási ikonját, és mentse az értéket.

    ![A kapcsolati karakterlánc lekérésének megjelenítése](./media/iot-hub-include-find-custom-connection-string/iot-hub-get-connection-string.png)

További információ a IoT Hub megosztott hozzáférési szabályzatokról és engedélyekről: [hozzáférés-vezérlés és engedélyek](../articles/iot-hub/iot-hub-devguide-security.md#access-control-and-permissions).
