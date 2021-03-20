---
title: Azure Notification hub létrehozása a Azure Portal használatával | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre Azure Notification hub-t a Azure Portal használatával.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 08/04/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 02/14/2019
ms.openlocfilehash: 141812e83653b458a2562bf73b70117c39bf7be0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92426569"
---
# <a name="quickstart-create-an-azure-notification-hub-in-the-azure-portal"></a>Gyors útmutató: Azure Notification hub létrehozása a Azure Portalban

Az Azure Notification Hubs egy egyszerűen használható és kibővített leküldéses értesítési alrendszert biztosít, amellyel értesítéseket küldhet bármilyen platformra (iOS, Android, Windows, Kindle, Baidu stb.) bármilyen háttérrendszerből (felhőbeli vagy helyszíni). A szolgáltatással kapcsolatos további információkért lásd: [Mi az az Azure Notification Hubs?](notification-hubs-push-notification-overview.md)

Ebben a rövid útmutatóban egy értesítési központot hoz létre a Azure Portal. Az első szakasz lépéseit követve létrehozhat egy Notification Hubs névteret és egy hubot a névtérben. A második szakasz egy értesítési központ létrehozásához szükséges lépéseket ismerteti egy meglévő Notification Hubs névtérben.

## <a name="create-a-namespace-and-a-notification-hub"></a>Névtér és értesítési központ létrehozása

Ebben a szakaszban egy névteret és egy hubot hoz létre a névtérben.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

## <a name="create-a-notification-hub-in-an-existing-namespace"></a>Értesítési központ létrehozása meglévő névtérben

Ebben a szakaszban egy meglévő névtérben hoz létre egy értesítési központot.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. A bal oldali menüben válassza a **minden szolgáltatás** lehetőséget **, majd a**  `*` bal oldali menüben a **Kedvencek** szakaszhoz adja hozzá a következőt: **értesítési központ névterek** melletti keresés. Válassza az **értesítési központ névterek** lehetőséget.

      ![Azure Portal – értesítési központ névterének kiválasztása](./media/create-notification-hub-portal/select-notification-hub-namespaces-all-services.png)
3. Az **értesítési központ névterei** lapon válassza ki a névteret a listából.

      ![Válassza ki a névteret a listából](./media/create-notification-hub-portal/select-namespace.png)
4. Az **értesítési központ névtere** lapon válassza a **hub hozzáadása** lehetőséget az eszköztáron.

      ![Értesítési központ névterei – hub hozzáadása gomb](./media/create-notification-hub-portal/add-hub-button.png)
5. Az **új értesítési központ** lapon adja meg az értesítési központ nevét, majd kattintson **az OK gombra**.

      ![Új értesítési központ lap – > adja meg a központ nevét](./media/create-notification-hub-portal/new-notification-hub-page.png)
6. Válassza az **értesítések** (harang ikon) lehetőséget a felső részen az új központ központi telepítésének állapotának megtekintéséhez. Az értesítési ablak bezárásához válassza az **X** lehetőséget a jobb oldali sarokban.

      ![Üzembe helyezési értesítés](./media/create-notification-hub-portal/deployment-notification.png)
7. Frissítse az **értesítési központ névtereit** tartalmazó weblapot, és tekintse meg az új hubot a listában.

      ![Képernyőfelvétel: az értesítési központ névtereit tartalmazó weblap, amely az új hubot tartalmazza a listában.](./media/create-notification-hub-portal/new-hub-in-list.png)
8. Válassza ki az **értesítési** központot, és tekintse meg az értesítési központ kezdőlapját.

      ![Képernyőkép, amely megjeleníti az értesítési központ kezdőlapját.](./media/create-notification-hub-portal/hub-home-page.png)

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy értesítési központot. Ha szeretné megtudni, hogyan konfigurálhatja az elosztót a platform Notification System (PNS) beállításaival, tekintse meg az [értesítési központ konfigurálása PNS-beállításokkal](configure-notification-hub-portal-pns-settings.md)című témakört.
