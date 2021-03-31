---
title: Notification Hubs névtér árképzési szintjeinek módosítása | Microsoft Docs
description: Megtudhatja, hogyan módosíthatja egy Azure Notification Hubs-névtér díjszabási szintjét.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 08/03/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/28/2019
ms.openlocfilehash: 1455259bc42aea9d506a9a2a19d725cac3d643f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87562769"
---
# <a name="change-pricing-tier-of-an-azure-notification-hubs-namespace"></a>Azure Notification hub-névtér árképzési szintjeinek módosítása

A Notification Hubs három szinten érhető el: **ingyenes**, **alapszintű** és **standard**. Ez a cikk bemutatja, hogyan módosíthatja az Azure Notification Hubs-névtér díjszabási szintjét.

## <a name="overview"></a>Áttekintés

Az Azure Notification Hubsban az *értesítési központ* a legkisebb erőforrás/entitás. Általában egy alkalmazásra mutat, és minden egyes Platform Notification System (PNS) esetében egy tanúsítványt tart fenn, amelyet támogatunk az alkalmazáshoz. Az alkalmazás lehet hibrid, natív és platformfüggetlen alkalmazás is.

A *névtér* az értesítési központok gyűjteménye. Az egyes névterek általában a kapcsolódó és adott célra használt hubokból állnak. Például három különböző névteret használhat fejlesztési, tesztelési és éles üzemi célokra.

Az **ingyenes**, az **alapszintű** vagy a **standard** szintű díjszabással is társíthat névtereket. Bármely névtér esetében használhatja az igényeinek megfelelő szintet. A következő részekben bemutatjuk, hogyan módosíthatja egy Notification Hubs névtér díjszabási szintjét.

## <a name="use-azure-portal"></a>Az Azure Portal használata

A Azure Portal használatakor megváltoztathatja a névtér díjszabási szintjét a névtér lapon vagy egy központi lapon. Ha egy központi lapon módosítja azt, akkor a névtér szintjén kell megváltoztatnia. Megváltoztatja a névtér és a névtér összes csomópontjának árképzési szintjét.

### <a name="change-tier-on-the-namespace-page"></a>A névtér oldalának változási szintje

A következő eljárás azt mutatja be, hogyan változtatható meg a névtér díjszabási szintje a névtér lapon. Ha megváltoztatja egy névtér szintjét, az a névtér összes hubhoz érvényes lesz.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben.
3. Válassza az **értesítési központ névterek** lehetőséget a **eszközök internetes hálózata** szakaszban. Ha a csillag () lehetőséget választja a `*` szöveg mellett, a rendszer hozzáadja a bal oldali navigációs sávra a **Kedvencek** területen. Ez segít a névterek oldal következő alkalommal történő elérésében. Miután hozzáadta a **kedvencekhez**, válassza az **értesítési központ névterek** lehetőséget.

    ![Minden szolgáltatás – > értesítési központ névterei](./media/change-pricing-tier/all-services-nhub.png)

4. Az **értesítési központ névterei** lapon válassza ki azt a névteret, amelynek az árképzési szintjét módosítani kívánja.
5. A névtérhez tartozó **értesítési központ névtér** lapján megtekintheti a névtér aktuális díjszabási szintjét az **alapvető** erőforrások szakaszban. A következő képen láthatja, hogy a névtér díjszabási szintje **ingyenes**.

    ![Aktuális díjszabási csomag a névtér oldalon](./media/change-pricing-tier/pricing-tier-before.png)

6. A névtérhez tartozó **értesítési központ névtér** lapján válassza a **kezelés** szakasz **díjszabási szintje** elemét.

    ![Válassza ki az árképzési szintet a névtér oldalon](./media/change-pricing-tier/namespace-select-pricing-menu.png)

7. Módosítsa az árképzési szintet, majd kattintson a **kiválasztás** gombra.
8. A **riasztásokban** megtekintheti a rétegek módosítása művelet állapotát.
9. Váltson át az **Áttekintés** lapra. Ellenőrizze, hogy megjelenik-e az új **csomag az alapvető** erőforrások szakasz **díjszabási szintje** mezőjében.
10. Ez a lépés nem kötelező. Válasszon ki egy hubot a névtérben. Győződjön meg róla, hogy ugyanazt az árképzési szintet látja az **alapvető** erőforrások szakaszban. A névtérben lévő összes hubok esetében ugyanazt az árképzési szintet kell megtekinteni.

### <a name="change-tier-on-the-hub-page"></a>A központ oldalának változási szintje

Kövesse az alábbi lépéseket a névtér díjszabási szintjeinek módosításához a központ lapon. Annak ellenére, hogy ezek a lépések a hub lapról kezdődnek, a névtér és a névtérben lévő összes központ díjszabási szintjét ténylegesen megváltoztatja:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben.
3. A **eszközök internetes hálózata** szakaszban válassza a **Notification Hubs** lehetőséget.
4. Válassza ki az **értesítési** központot.
5. Válassza ki a bal oldali menüben a **díjszabási szintet** .
6. Módosítsa az árképzési szintet, majd kattintson a **kiválasztás** gombra. Ez a művelet megváltoztatja a hubot tartalmazó névtér díjszabási szintje beállítását. Így az új díjszabási szintet láthatja a névtér és az összes központ oldalain.

> [!NOTE]
> Az árképzési szintek változásai azonnal érvénybe lépnek.

## <a name="use-rest-api"></a>A REST API használata

A következő erőforrás-szolgáltató REST API-k segítségével lekérheti a jelenlegi árképzési szintet, és frissítheti azt.

### <a name="get-current-pricing-tier-for-a-namespace"></a>Névtér aktuális díjszabási szintjeinek beolvasása

Az aktuális névtér-rétegek beszerzéséhez küldje el a GET parancsot az alábbi példában látható módon:

```REST
GET: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
```

### <a name="update-pricing-tier-for-a-namespace"></a>Névtér frissítésének díjszabási szintje

A névtér-rétegek frissítéséhez küldje el a PUT parancsot az alábbi példában látható módon:

```REST
PUT: https://management.core.windows.net/{subscription ID}/services/ServiceBus/Namespaces/{namespace name}/notificationhubplan
Body: <NotificationHubPlan xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/netservices/2010/10/servicebus/connect"><SKU>Standard</SKU></NotificationHubPlan>
```

## <a name="next-steps"></a>Következő lépések

További információ ezekről a csomagokról és díjszabásról: [Notification Hubs díjszabása](https://azure.microsoft.com/pricing/details/notification-hubs/).
