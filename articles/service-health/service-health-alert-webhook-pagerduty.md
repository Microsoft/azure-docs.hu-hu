---
title: Az Azure service health-riasztások konfigurálás a pagerduty segítségével |} A Microsoft Docs
description: A Szolgáltatásállapot-események a PagerDuty-példányra vonatkozó személyre szabott értesítések küldése.
author: stephbaron
ms.author: stbaron
ms.topic: article
ms.service: service-health
ms.date: 11/14/2017
ms.openlocfilehash: b78c155fb2f3a13c27f4ff71c4dd37df2dbd2f36
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60620987"
---
# <a name="configure-service-health-alerts-with-pagerduty"></a>Service health-riasztások konfigurálás a pagerduty segítségével

Ez a cikk bemutatja, hogyan állíthatja be az Azure service health értesítések PagerDuty segítségével egy webhook használatával. Használatával [PagerDuty](https://www.pagerduty.com/)a Microsoft Azure integrációs egyéni típus, néhány kattintással hozzáadhatja a Service Health-riasztások az új vagy meglévő PagerDuty-szolgáltatások.

## <a name="creating-a-service-health-integration-url-in-pagerduty"></a>Service health integrációs URL létrehozása a pagerduty szolgáltatásban
1.  Győződjön meg arról, hogy regisztrált-, és bejelentkezett, a [PagerDuty](https://www.pagerduty.com/) fiókot.

1.  Keresse meg a **szolgáltatások** szakasz a pagerduty szolgáltatásban.

    ![A "Szolgáltatások" szakaszban a pagerduty szolgáltatásban](./media/webhook-alerts/pagerduty-services-section.png)

1.  Válassza ki **új szolgáltatás hozzáadása** vagy nyisson meg egy meglévő szolgáltatást állította be.

1.  Az a **integrációs beállítások**, jelölje be az alábbiakat:

    a. **Integráció típus**: Microsoft Azure

    b. **Az integrációs nevének**: \<név\>

    ![A pagerduty szolgáltatásban "integrációs beállítások"](./media/webhook-alerts/pagerduty-integration-settings.png)

1.  Adja meg a többi szükséges mezőt, és válassza ki **Hozzáadás**.

1.  Nyissa meg az új integrációs, és másolja és mentse a **integrációs URL-Címének**.

    ![Az "integrációs URL-Címének" a pagerduty szolgáltatásban](./media/webhook-alerts/pagerduty-integration-url.png)

## <a name="create-an-alert-using-pagerduty-in-the-azure-portal"></a>Hozzon létre egy riasztást, az Azure Portalon PagerDuty segítségével
### <a name="for-a-new-action-group"></a>Az új műveletcsoport:
1. Végezze el az 1 – 8 [az Azure portal segítségével hozzon létre egy riasztást a szolgáltatás állapotával kapcsolatos értesítés az új műveletcsoport](../azure-monitor/platform/alerts-activity-log-service-notifications.md).

1. Adja meg a listában, **műveletek**:

    a. **Művelet típusa:** *Webhook*

    b. **Részletek:** A PagerDuty **integrációs URL-Címének** , korábban mentett.

    c. **név:** A Webhook nevét, alias vagy azonosítója.

1. Válassza ki **mentése** Ha ezzel elkészült, a riasztás létrehozásához.

### <a name="for-an-existing-action-group"></a>A meglévő műveletcsoport:
1. Az a [az Azure portal](https://portal.azure.com/)válassza **figyelő**.

1. Az a **beállítások** szakaszban jelölje be **Műveletcsoportok**.

1. Keresse meg és válassza ki a szerkeszteni kívánt művelet csoportot.

1. Adja hozzá a listához, **műveletek**:

    a. **Művelet típusa:** *Webhook*

    b. **Részletek:** A PagerDuty **integrációs URL-Címének** , korábban mentett.

    c. **név:** A Webhook nevét, alias vagy azonosítója.

1. Válassza ki **mentése** végeztével a műveletcsoport frissítéséhez.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Egy HTTP POST kérelem használatával a webhook-integráció tesztelése
1. Hozza létre a service health hasznos adatokat szeretne küldeni. Egy példa service health webhook hasznos adatai címen található [Webhookok az Azure-tevékenységi naplóriasztások](../azure-monitor/platform/activity-log-alerts-webhook.md).

1. HTTP POST-kérelmet a következőképpen hozhat létre:

    ```
    POST        https://events.pagerduty.com/integration/<IntegrationKey>/enqueue

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Ekkor kap egy `202 Accepted` egy üzenet, amely tartalmazza az "esemény azonosítóját."

1. Lépjen a [PagerDuty](https://www.pagerduty.com/) annak ellenőrzéséhez, hogy az integrációs sikeres volt-e beállítva.

## <a name="next-steps"></a>További lépések
- Ismerje meg, hogyan [konfigurálása webhook-értesítésekkel meglévő probléma felügyeleti rendszerek](service-health-alert-webhook-guide.md).
- Tekintse át a [tevékenység log riasztási webhookséma](../azure-monitor/platform/activity-log-alerts-webhook.md). 
- Ismerje meg [szolgáltatás állapotára vonatkozó értesítések](../azure-monitor/platform/service-notifications.md).
- Tudjon meg többet [Műveletcsoportok](../azure-monitor/platform/action-groups.md).