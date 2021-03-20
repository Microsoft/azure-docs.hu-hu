---
title: fájl belefoglalása (eszköz streamek)
description: fájl belefoglalása
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 01/15/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 46ab75f161692dd048c19698af7027d0e0622b37
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "82506440"
---
Ha azt tervezi, hogy folytatja a következő javasolt cikket, megtarthatja és újra használhatja a már létrehozott erőforrásokat.

Ellenkező esetben a költségek elkerülése érdekében törölheti a cikkben létrehozott Azure-erőforrásokat.

> [!IMPORTANT]
> Az erőforráscsoport törlése nem vonható vissza. Az erőforráscsoport és a benne foglalt erőforrások véglegesen törlődnek. Figyeljen, nehogy véletlenül rossz erőforráscsoportot vagy erőforrásokat töröljön. Ha a IoT hub-t egy meglévő erőforráscsoport belsejében hozta létre, amely a megőrizni kívánt erőforrásokat tartalmazza, akkor csak a IoT hub-erőforrást törölje, nem az erőforráscsoportot.
>

Erőforráscsoport törlése név alapján:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com), és válassza az **Erőforráscsoportok** elemet.

1. A **szűrés név alapján** mezőbe írja be az IoT hubot tartalmazó erőforráscsoport nevét.

1. Az eredmény listán az erőforráscsoport jobb oldalán válassza a három pontot (**..**.), majd válassza az **erőforráscsoport törlése** lehetőséget.

    ![Az "erőforráscsoport törlése" gomb](./media/iot-hub-quickstarts-clean-up-resources-device-streams/iot-hub-delete-resource-group.png)

1. Az erőforráscsoport törlésének megerősítéséhez írja be újra az erőforráscsoport nevét, majd válassza a **Törlés** lehetőséget. Néhány pillanat elteltével töröljük az erőforráscsoportot és az összes benne foglalt erőforrást.
