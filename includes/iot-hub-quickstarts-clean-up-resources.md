---
title: fájl belefoglalása
description: fájl belefoglalása
services: iot-hub
author: wesmc7777
ms.service: iot-hub
ms.topic: include
ms.date: 06/19/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: 49fbfe116a2fe554abb3a3ca4d1dcd6cab2b746d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "69626341"
---
Ha azt tervezi, hogy a következő ajánlott cikkel folytatja, megtarthatja és újból felhasználhatja a már létrehozott erőforrásokat.

Ellenkező esetben, a díjak elkerülése érdekében törölheti a jelen cikkben létrehozott Azure-erőforrásokat. 

> [!IMPORTANT]
> Az erőforráscsoport törlése nem vonható vissza. Az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen arra, hogy ne töröljön véletlenül erőforráscsoportot vagy erőforrásokat. Ha az IoT Hubot egy meglévő, megtartani kívánt erőforrásokat tartalmazó erőforráscsoportban hozta létre, az erőforráscsoport törlése helyett törölheti csak magát az IoT Hub-erőforrást.
>

Erőforráscsoport törlése név alapján:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és válassza az **Erőforráscsoportok** elemet.

2. A **szűrés név alapján** szövegmezőbe írja be a IoT hub tartalmazó erőforráscsoport nevét. 

3. Az erőforráscsoport jobb oldalán, az eredmények listájában válassza a **...** , majd az **erőforráscsoport törlése** elemet.

    ![Törlés](./media/iot-hub-quickstarts-clean-up-resources/iot-hub-delete-resource-group.png)

4. A rendszer az erőforráscsoport törlésének megerősítését fogja kérni. A megerősítéshez írja be ismét az erőforráscsoport nevét, majd válassza a **Törlés** lehetőséget. A rendszer néhány pillanaton belül törli az erőforráscsoportot és a benne foglalt erőforrásokat.