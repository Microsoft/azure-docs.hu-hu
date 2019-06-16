---
title: Az Azure service health-riasztások küldése az opsgenie segítségével webhookok használata
description: A Szolgáltatásállapot-események az opsgenie segítségével példányra kapcsolatos személyre szabott értesítések küldése.
author: stephbaron
ms.author: stbaron
ms.topic: article
ms.service: service-health
ms.date: 06/10/2019
ms.openlocfilehash: fab99b7093ac3f18f6313273d21905e0a3ed7e5b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67067161"
---
# <a name="send-azure-service-health-alerts-with-opsgenie-using-webhooks"></a>Az Azure service health-riasztások küldése az opsgenie segítségével webhookok használata

Ez a cikk bemutatja, hogyan állíthatja be az Azure service health-riasztások az opsgenie segítségével egy webhook használatával. Használatával [opsgenie segítségével](https://www.opsgenie.com/)az Azure Service Health integráció, a továbbíthatja az Azure Service Health-riasztások az opsgenie segítségével. Opsgenie segítségével megadhatja, hogy a megfelelő értesítendő személyeket és az alapján a készenléti ütemezéseket, e-mailben, szöveges üzenetben (SMS), telefonhívások, iOS és Android leküldéses értesítések használatával, és a problémák továbbítása riasztások, amíg a riasztás nem igazoltak vissza vagy lezárt.

## <a name="creating-a-service-health-integration-url-in-opsgenie"></a>Service health integrációs URL létrehozása a opsgenie segítségével
1.  Győződjön meg arról, hogy regisztrált-, és bejelentkezett, a [opsgenie segítségével](https://www.opsgenie.com/) fiókot.

1.  Keresse meg a **Integrációk** opsgenie segítségével szakaszát.

    ![Az "Integrációs" szakaszban az opsgenie segítségével](./media/webhook-alerts/opsgenie-integrations-section.png)

1.  Válassza ki a **Azure Service Health** integrációs gombra.

    ![A "az Azure Service Health gombot" opsgenie segítségével](./media/webhook-alerts/opsgenie-azureservicehealth-button.png)

1.  **Név** a riasztást, és adja meg a **csapathoz rendelt** mező.

1.  Adja meg például a többi mező **címzettek**, **engedélyezve**, és **értesítések elrejtése**.

1.  Másolja ki és mentse a **integrációs URL-címet**, amely már tartalmaznia kell a `apiKey` hozzáfűzi a teljes körű.

    ![Az "integrációs URL-címet" opsgenie segítségével](./media/webhook-alerts/opsgenie-integration-url.png)

1.  Válassza ki **integrációs mentése**

## <a name="create-an-alert-using-opsgenie-in-the-azure-portal"></a>Hozzon létre egy riasztást opsgenie segítségével az Azure portál használatával
### <a name="for-a-new-action-group"></a>Az új műveletcsoport:
1. Végezze el az 1 – 8 [az Azure portal segítségével hozzon létre egy riasztást a szolgáltatás állapotával kapcsolatos értesítés az új műveletcsoport](../azure-monitor/platform/alerts-activity-log-service-notifications.md).

1. Adja meg a listában, **műveletek**:

    a. **Művelet típusa:** *Webhook*

    b. **Részletek:** Az opsgenie segítségével **integrációs URL-Címének** , korábban mentett.

    c. **név:** A Webhook nevét, alias vagy azonosítója.

1. Válassza ki **mentése** Ha ezzel elkészült, a riasztás létrehozásához.

### <a name="for-an-existing-action-group"></a>A meglévő műveletcsoport:
1. Az a [az Azure portal](https://portal.azure.com/)válassza **figyelő**.

1. Az a **beállítások** szakaszban jelölje be **Műveletcsoportok**.

1. Keresse meg és válassza ki a szerkeszteni kívánt művelet csoportot.

1. Adja hozzá a listához, **műveletek**:

    a. **Művelet típusa:** *Webhook*

    b. **Részletek:** Az opsgenie segítségével **integrációs URL-Címének** , korábban mentett.

    c. **név:** A Webhook nevét, alias vagy azonosítója.

1. Válassza ki **mentése** végeztével a műveletcsoport frissítéséhez.

## <a name="testing-your-webhook-integration-via-an-http-post-request"></a>Egy HTTP POST kérelem használatával a webhook-integráció tesztelése
1. Hozza létre a service health hasznos adatokat szeretne küldeni. Egy példa service health webhook hasznos adatai címen található [Webhookok az Azure-tevékenységi naplóriasztások](../azure-monitor/platform/activity-log-alerts-webhook.md).

1. HTTP POST-kérelmet a következőképpen hozhat létre:

    ```
    POST        https://api.opsgenie.com/v1/json/azureservicehealth?apiKey=<APIKEY>

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
1. Ekkor kap egy `200 OK` válasz állapota "sikeres" üzenet

1. Lépjen a [opsgenie segítségével](https://www.opsgenie.com/) annak ellenőrzéséhez, hogy az integrációs sikeres volt-e beállítva.

## <a name="next-steps"></a>További lépések
- Ismerje meg, hogyan [konfigurálása webhook-értesítésekkel meglévő probléma felügyeleti rendszerek](service-health-alert-webhook-guide.md).
- Tekintse át a [tevékenység log riasztási webhookséma](../azure-monitor/platform/activity-log-alerts-webhook.md). 
- Ismerje meg [szolgáltatás állapotára vonatkozó értesítések](../azure-monitor/platform/service-notifications.md).
- Tudjon meg többet [Műveletcsoportok](../azure-monitor/platform/action-groups.md).