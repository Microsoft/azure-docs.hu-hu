---
title: Azure értesítési központ létrehozása a Azure Portal | Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre Azure értesítési központot a Azure Portal.
services: notification-hubs
author: sethmanheim
ms.author: sethm
manager: femila
ms.reviewer: thsomasu
ms.date: 08/04/2020
ms.lastreviewed: 02/14/2019
ms.topic: quickstart
ms.service: notification-hubs
ms.workload: mobile
ms.custom:
- mode-portal
ms.openlocfilehash: 4381c9259788982832a02ecbe2c87c6f6e723772
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533592"
---
# <a name="quickstart-create-an-azure-notification-hub-in-the-azure-portal"></a>Rövid útmutató: Azure értesítési központ létrehozása a Azure Portal

Az Azure Notification Hubs egy egyszerűen használható és kibővített leküldéses értesítési alrendszert biztosít, amellyel értesítéseket küldhet bármilyen platformra (iOS, Android, Windows, Kindle, Baidu stb.) bármilyen háttérrendszerből (felhőbeli vagy helyszíni). A szolgáltatással kapcsolatos további információkért lásd: [Mi az az Azure Notification Hubs?](notification-hubs-push-notification-overview.md).

Ebben a rövid útmutatóban egy értesítési központot hoz létre a Azure Portal. Az első szakasz lépésekkel hoz létre egy Notification Hubs és egy központot a névtérben. A második szakasz lépésekkel hoz létre értesítési központot egy meglévő Notification Hubs névtérben.

## <a name="create-a-namespace-and-a-notification-hub"></a>Névtér és értesítési központ létrehozása

Ebben a szakaszban egy névteret és egy központot hoz létre a névtérben.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

## <a name="create-a-notification-hub-in-an-existing-namespace"></a>Értesítési központ létrehozása meglévő névtérben

Ebben a szakaszban egy értesítési központot hoz létre egy meglévő névtérben.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza **a Minden szolgáltatás** lehetőséget a bal oldali menüben, keressen rá az **Értesítési** központ kifejezésre, válassza a **csillag** ( ) lehetőséget az Értesítési központ névterei mellett, hogy hozzáadja a bal oldali menü `*` **KEDVENCEK** szakaszához.  Válassza **az Értesítési központ névterei lehetőséget.**

      ![Azure Portal – Notification Hub-névterek kiválasztása](./media/create-notification-hub-portal/select-notification-hub-namespaces-all-services.png)
3. Az **Értesítési központ névterei lapon** válassza ki a névteret a listából.

      ![Válassza ki a névteret a listából](./media/create-notification-hub-portal/select-namespace.png)
4. Az **Értesítési központ névtere lapon** válassza az **eszköztár Hub** hozzáadása gombját.

      ![Notification Hub-névterek – Hub hozzáadása gomb](./media/create-notification-hub-portal/add-hub-button.png)
5. Az Új **értesítési központ lapon** adja meg az értesítési központ nevét, majd kattintson az **OK gombra.**

      ![Új értesítési központ lap – > adja meg a központ nevét](./media/create-notification-hub-portal/new-notification-hub-page.png)
6. Az **új központ üzembe** helyezésének állapotához válassza a felső Értesítések (harang ikon) lehetőséget. A **jobb sarokban** lévő X gombot választva zárja be az értesítési ablakot.

      ![Üzembe helyezési értesítés](./media/create-notification-hub-portal/deployment-notification.png)
7. Frissítse az **Értesítési központ névterei weblapot,** hogy az új központ megjelenik a listában.

      ![Képernyőkép a Notification Hub-névterek weblapjáról, amely az új központot tartalmazza a listában.](./media/create-notification-hub-portal/new-hub-in-list.png)
8. Válassza ki **az értesítési központot** az értesítési központ kezdőlapjára.

      ![Az értesítési központ kezdőlapját bemutató képernyőkép.](./media/create-notification-hub-portal/hub-home-page.png)

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy értesítési központot. A központ platformértesítési rendszer (PNS) beállításokkal való konfigurálásával kapcsolatos további információkért lásd: Értesítési központ konfigurálása [PNS-beállításokkal.](configure-notification-hub-portal-pns-settings.md)
