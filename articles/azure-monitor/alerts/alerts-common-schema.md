---
title: Azure Monitor riasztások általános riasztási sémája
description: Az általános riasztási séma megismerése, miért érdemes használni, és hogyan kell engedélyezni
ms.topic: conceptual
ms.date: 03/14/2019
ms.openlocfilehash: 39ea469b4109f4715db0d0a73ad069121058a46f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102038257"
---
# <a name="common-alert-schema"></a>Gyakori riasztási séma

Ez a cikk ismerteti a gyakori riasztási sémát, a használatának előnyeit és annak engedélyezését.

## <a name="what-is-the-common-alert-schema"></a>Mi a közös riasztási séma?

A Common Alert séma szabványosítja a riasztási értesítések fogyasztási élményét az Azure-ban még ma. Az Azure-ban a három riasztási típust (metrikus, log és Activity log) a saját e-mail-sablonjai, webhook-sémái stb. is megkapta. A Common Alert séma használatával mostantól konzisztens sémával fogadhat riasztási értesítéseket.

Minden riasztási példány leírja **az érintett erőforrást** , valamint **a riasztás okát**, és ezeket a példányokat az alábbi szakaszokban ismertetett általános séma ismerteti:
* **Essentials**: az összes riasztási típussal közös **szabványosított mezők** összessége, amelyek leírják, hogy a riasztás **milyen erőforrást** tartalmaz, valamint a további gyakori riasztási metaadatokat (például a súlyosságot vagy a leírást). 
* **Riasztási környezet**: olyan mezők összessége, amelyek leírják a **riasztás okát**, a **riasztás típusától** függően változó mezőkkel. Egy metrikai riasztás például olyan mezőkkel rendelkezhet, mint a metrika neve és a metrika értéke a riasztási környezetben, míg a tevékenység naplójának riasztása a riasztást kiváltó eseményről tartalmaz információt. 

Az ügyfeleinktől származó tipikus integrációs forgatókönyvek a riasztási példánynak az érintett csapathoz való továbbítását foglalják magukban egy adott pivot (például erőforráscsoport) alapján, amely után a felelős csapat elkezdi a munkát. Az általános riasztási sémával szabványosított útválasztási logikával rendelkezhet a riasztási típusok között, ha kihasználja az alapvető mezőket, így a környezeti mezőket az érintett csapatok továbbra is kivizsgálják.

Ez azt jelenti, hogy lehetséges, hogy kevesebb integrációval rendelkezik, így _sokkal_ egyszerűbb feladat lesz a kezelése és karbantartása. Emellett a riasztások jövőbeli bővítései (például a testreszabás, a diagnosztika és a titkosítás stb.) csak a közös sémában lesznek felkészítve.

## <a name="what-enhancements-does-the-common-alert-schema-bring"></a>Milyen fejlesztéseket végez a közös riasztási séma?

Az általános riasztási séma elsődlegesen a riasztási értesítésekben is megnyilvánulhat. A megjelenő fejlesztések az alábbi listában láthatók:

| Művelet | Fejlesztések|
|:---|:---|
| SMS | Konzisztens SMS-sablon az összes riasztási típushoz. |
| E-mail | Konzisztens és részletes e-mail sablon, amely lehetővé teszi, hogy könnyedén diagnosztizálja a problémákat egy pillantással. Beágyazott mély – hivatkozások a riasztási példányra a Portálon és az érintett erőforráson gondoskodjon arról, hogy gyorsan beugorjon a Szervizelési folyamatba. |
| Webhook/logikai alkalmazás/Azure Function/Automation Runbook | Konzisztens JSON-struktúra az összes riasztási típushoz, amely lehetővé teszi, hogy könnyedén építsen be integrációkat a különböző riasztási típusok között. |

Az új séma többek között a Azure Portal és a Azure mobile app is lehetővé teszi, hogy az azonnali jövőben is gazdagabb legyen a riasztások felhasználásának élménye. 

[További információ a webhookok/Logic Apps/Azure Functions/Automation Runbookok sémájának definícióit ismertető témakörben.](./alerts-common-schema-definitions.md)

> [!NOTE]
> A következő műveletek nem támogatják az általános riasztási sémát: ITSM-csatoló.

## <a name="how-do-i-enable-the-common-alert-schema"></a>Hogyan engedélyezi a közös riasztási sémát?

A közös riasztási sémát a Portálon és a REST APIon keresztül is engedélyezheti vagy letilthatja. Az új sémára való váltásra szolgáló váltás műveleti szinten van. Például külön kell bejelentkeznie az e-mail-műveletre és egy webhook-műveletre.

> [!NOTE]
> 1. A következő típusú riasztások alapértelmezés szerint támogatják a közös sémát (nem kötelező megadni):
>     * Intelligens észlelési riasztások
> 1. A következő típusú riasztások jelenleg nem támogatják a közös sémát:
>     * A VM- [ismeretek](../vm/vminsights-overview.md) által generált riasztások
>     * [Azure Cost Management](../../cost-management-billing/manage/cost-management-budget-scenario.md) által generált riasztások

### <a name="through-the-azure-portal"></a>A Azure Portal

![Gyakori riasztási séma](media/alerts-common-schema/portal-opt-in.png)

1. Nyisson meg egy meglévő vagy egy új műveletet egy műveleti csoportban. 
1. Válassza az Igen lehetőséget a váltáshoz, hogy a közös riasztási sémát az ábrán látható módon engedélyezze.

### <a name="through-the-action-groups-rest-api"></a>A műveleti csoportok REST API

A [műveleti csoportok API](/rest/api/monitor/actiongroups) -val is használhatja a Common Alert sémát. A [create vagy a update](/rest/api/monitor/actiongroups/createorupdate) REST API hívást követően a "useCommonAlertSchema" jelzőt beállíthatja a "true" értékre (a választáshoz) vagy "false" (letiltáshoz) a következő műveletek bármelyikéhez: e-mailek/webhook/Logic app/Azure Function/Automation runbook.

Például a [Létrehozás vagy frissítés](/rest/api/monitor/actiongroups/createorupdate) REST API a következő kérelem törzse lesz végrehajtva:

* A "John Doe e-mail-címe" e-mail művelet általános riasztási sémájának engedélyezése
* A "Jane Smith e-mail címe" e-mail művelet általános riasztási sémájának letiltása
* Az általános riasztási séma engedélyezése a "minta webhook" webhook művelethez

```json
{
  "properties": {
    "groupShortName": "sample",
    "enabled": true,
    "emailReceivers": [
      {
        "name": "John Doe's email",
        "emailAddress": "johndoe@email.com",
        "useCommonAlertSchema": true
      },
      {
        "name": "Jane Smith's email",
        "emailAddress": "janesmith@email.com",
        "useCommonAlertSchema": false
      }
    ],
    "smsReceivers": [
      {
        "name": "John Doe's mobile",
        "countryCode": "1",
        "phoneNumber": "1234567890"
      },
      {
        "name": "Jane Smith's mobile",
        "countryCode": "1",
        "phoneNumber": "0987654321"
      }
    ],
    "webhookReceivers": [
      {
        "name": "Sample webhook",
        "serviceUri": "http://www.example.com/webhook",
        "useCommonAlertSchema": true
      }
    ]
  },
  "location": "Global",
  "tags": {}
}
```





## <a name="next-steps"></a>Következő lépések

- [Gyakori riasztási séma-definíciók webhookok/Logic Apps/Azure Functions/Automation Runbookok.](./alerts-common-schema-definitions.md)
- [Megtudhatja, hogyan hozhat létre egy logikai alkalmazást, amely a gyakori riasztási sémát használja az összes riasztás kezeléséhez.](./alerts-common-schema-integrations.md)