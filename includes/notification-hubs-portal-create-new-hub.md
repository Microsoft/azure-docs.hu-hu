---
title: fájl belefoglalása
description: fájl belefoglalása
services: notification-hubs
author: jwargo
ms.service: notification-hubs
ms.topic: include
ms.date: 01/17/2019
ms.author: jowargo
ms.custom: include file
ms.openlocfilehash: 2ec602f056b339a1b1dcb78d6b8d7583aeaf0434
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96009107"
---
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A bal oldali menüben válassza a **minden szolgáltatás** lehetőséget, majd a **mobil** szakaszban válassza a **Notification Hubs** lehetőséget. A szolgáltatás neve melletti csillag ikonra kattintva adja hozzá a szolgáltatást a **Kedvencek** szakaszhoz a bal oldali menüben. Miután hozzáadta **Notification Hubs** a **kedvencekhez**, válassza ki a bal oldali menüben.

      ![Azure Portal – Notification Hubs kiválasztása](./media/notification-hubs-portal-create-new-hub/all-services-select-notification-hubs.png)

1. A **Notification Hubs** lapon válassza a **Hozzáadás** lehetőséget az eszköztáron.

      ![Notification Hubs – eszköztár hozzáadása gomb](./media/notification-hubs-portal-create-new-hub/add-toolbar-button.png)

1. Az **értesítési központ** oldalon hajtsa végre a következő lépéseket:

    1. Adjon meg egy nevet az **értesítési központban**.  

    1. Adjon meg egy nevet az **új névtér létrehozása** mezőben. Egy névtér legalább egy hubokat tartalmaz.

    1. Válasszon ki egy értéket a **hely** legördülő listából. Ez az érték határozza meg azt a helyet, amelyben létre kívánja hozni a hubot.

    1. Válasszon ki egy meglévő erőforráscsoportot az **erőforráscsoporthoz**, vagy hozzon létre egy új erőforráscsoport nevét.

    1. Válassza a **Létrehozás** lehetőséget.

        ![Azure Portal – értesítési központ tulajdonságainak megadása](./media/notification-hubs-portal-create-new-hub/notification-hubs-azure-portal-settings.png)

1. Válassza az **értesítések** (harang ikon) lehetőséget, majd válassza **az Ugrás az erőforráshoz** lehetőséget. A listát a **Notification Hubs** oldalon is frissítheti, és kiválaszthatja a hubot.

      ![Azure Portal – erőforrás-ugrás](./media/notification-hubs-portal-create-new-hub/go-to-notification-hub.png)

1. A listáról válassza a **Hozzáférési szabályzatok** lehetőséget. Vegye figyelembe, hogy a két kapcsolódási karakterlánc elérhető az Ön számára. A leküldéses értesítések kezeléséhez később szüksége lesz rájuk.

      >[!IMPORTANT]
      >Ne *használja az* **DefaultFullSharedAccessSignature** szabályzatot az alkalmazásban. Ezt csak a háttérben való használatra szántuk.
      >

      ![Azure Portal – értesítési központ kapcsolati sztringjei](./media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png)
