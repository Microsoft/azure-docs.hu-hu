---
title: Az Azure monitor riasztások gyakori riasztási séma
description: Miért érdemes használnia a és engedélyezését a gyakori riasztási séma ismertetése
author: anantr
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: anantr
ms.component: alerts
ms.openlocfilehash: c18227a491478d0d8010761440a54fd088344b39
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2019
ms.locfileid: "60149376"
---
# <a name="common-alert-schema"></a>Gyakori riasztási séma

Ez a cikk ismerteti, mi a gyakori riasztási séma van, és hogyan engedélyezheti használatának előnyeit.

## <a name="what-is-the-common-alert-schema"></a>Mi az a közös riasztási séma?

A gyakori riasztási séma egységesíti a használati élményt, a riasztási értesítéseket az Azure-ban még ma. A három riasztási típusok az Azure-ban még ma (a mérőszám, a napló és a tevékenységnaplóhoz) volt korábban, a saját e-mail-sablonok, a webhook-sémákat, stb. A közös riasztási sémával rendelkező mostantól fogadni tudják a riasztási értesítések küldése a konzisztens sémák.

Minden riasztási eseményről ismerteti **érintett erőforrás** és **a riasztás okának**, és ezek a példányok a közös séma a következő szakaszokban ismertetett:
* **Essentials**: Egy **szabványos mezők**közös összes riasztás típusa, amelyek leírják **milyen erőforrás** a riasztás mellett további gyakori riasztási metaadatokat (például súlyosság vagy leírása) be van kapcsolva. 
* **Riasztási környezet**: Egy készlet a mezők, amelyek leírják a **a riasztás okának**, mezőkkel, amelyek **a riasztási típus alapján**. Metrikariasztás például kellene mezőket is, például a metrika neve és a riasztási környezetben metrikaérték, mivel a tevékenységnapló-riasztás lenne a riasztást kiváltó eseménnyel kapcsolatos adatokat. 

A tipikus integrációs forgatókönyvek, hogy ügyfelektől kapott járnak továbbítani tudja a riasztás az érintett csapat néhány pivot (például resource group), amely után a felelős csapat használatának megkezdése, alapján-példány. A közös riasztási sémával rendelkező, is rendelkezik szabványosított logikai útválasztási logikát a riasztástípusok között mezők alapvető, kihasználva a környezet mezőket a további vizsgálat érdekében az érintett csapatot, hagyja.

Ez azt jelenti, hogy is csatlakozhat kevesebb Integrációk, így a folyamat, kezelésével és karbantartásával őket egy _sokkal_ egyszerűbb feladat. Ezenkívül jövőbeli riasztási hasznos végrehajtott információbeolvasás (például testreszabási, diagnosztikai Adatbővítés, stb.) lesz csak jelent meg a közös sémában.

## <a name="what-enhancements-does-the-common-alert-schema-bring"></a>Milyen fejlesztések nem hozza a gyakori riasztási séma?

A gyakori riasztási séma elsősorban eredményezi majd magát a riasztási értesítéseket. A fejlesztések, amely megjelenik az alábbiakban olvashatók:

| Műveletek | Fejlesztések|
|:---|:---|
| SMS | Minden riasztástípus konzisztens SMS sablon. |
| E-mail | Egy egységes és részletes e-mail-sablont, így könnyedén diagnosztizálhatja a problémákat, egyetlen pillantással. Beágyazott mély-hivatkozásokat a portál és az érintett erőforrás riasztási példányára győződjön meg arról, hogy Ön is gyorsan nyissa meg a javítási folyamatot. |
| Webhook/Logic App/Azure Function/Automation Runbook | Egy egységes JSON struktúra minden riasztás esetében, amely lehetővé teszi, hogy könnyen hozhat létre integrációkat különböző riasztások különböző típusait. |

Az új sémában azt is lehetővé teszi riasztási fogyasztás sokoldalúbb felhasználói élményben az Azure portal és az Azure mobile app a közeljövőben. 

[További információk a Webhookok/Logic Apps vagy az Azure Functions/Automation-Runbookok sémadefinícióinak.](https://aka.ms/commonAlertSchemaDefinitions)

> [!NOTE]
> A következő műveletek nem támogatják a gyakori riasztási séma: ITSM-összekötő.

## <a name="how-do-i-enable-the-common-alert-schema"></a>Hogyan engedélyezhetem a gyakori riasztási séma?

Részvétel a, illetve kikapcsolhatja az a közös riasztási sémájának Műveletcsoportok, mind a portálon keresztül, és a REST API-n keresztül. Váltson át az új sémában a váltógomb létezik műveleti szinten. Ha például hogy külön igénybe veszik a e-mail-művelet és a egy webhook művelettel.

> [!NOTE]
> 1. A következő típusú riasztásokról alapértelmezés szerint a közös séma támogatja (nem választható a szükséges):
>     * Az intelligens észlelési riasztásokkal
> 1. A következő típusú riasztásokról jelenleg nem támogatja a közös séma:
>     * Által generált riasztások [Azure Monitor-beli virtuális gépek](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview)
>     * Által generált riasztások [Azure Cost Management](https://docs.microsoft.com/azure/billing/billing-cost-management-budget-scenario)

### <a name="through-the-azure-portal"></a>Az Azure Portalon keresztül

![Gyakori riasztási séma részt](media/alerts-common-schema/portal-opt-in.png)

1. Nyissa meg a műveletcsoport minden meglévő vagy új művelet. 
1. Válassza az "Igen" az engedélyezése a gyakori riasztási séma, ahogy a váltógombot.

### <a name="through-the-action-groups-rest-api"></a>A Műveletcsoportok REST API használatával

Is használhatja a [művelet csoportok API](https://docs.microsoft.com/rest/api/monitor/actiongroups) iratkozzanak fel a gyakori riasztási séma. Így amikor a [létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate) REST API-hívás, beállíthatja a jelzőt "useCommonAlertSchema" (engedélyezve), a "true" vagy "false" (a kikapcsolhatja az újat) a következő műveletek - e-mailt és webhookot/logikai alkalmazás vagy az Azure-függvény/automation-runbook számára.

Ha például a következő kéréstörzsben végzett a [létrehozása vagy frissítése](https://docs.microsoft.com/rest/api/monitor/actiongroups/createorupdate) REST API-t fog tegye a következőket:

* Az e-mail-művelet "John Doe e-mail" gyakori riasztási sémáját engedélyezése
* Az e-mail-művelet "Email Szabó Judit" gyakori riasztási sémáját letiltása
* A webhook művelettel "Minta webhook" gyakori riasztási sémáját engedélyezése

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





## <a name="next-steps"></a>További lépések

- [Gyakori riasztási sémadefinícióinak Webhookok/Logic Apps vagy az Azure Functions/Automation-Runbookok.](https://aka.ms/commonAlertSchemaDefinitions)



