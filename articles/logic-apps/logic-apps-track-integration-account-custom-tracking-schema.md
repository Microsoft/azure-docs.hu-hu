---
title: Egyéni nyomkövetési sémák B2B-üzenetekhez
description: Egyéni nyomkövetési sémák létrehozása a B2B-üzenetek figyeléséhez Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/01/2020
ms.openlocfilehash: c82f9cbfaf2e23ddaa5e4b05f4aac4795d3e16a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "76903052"
---
# <a name="create-custom-tracking-schemas-that-monitor-end-to-end-workflows-in-azure-logic-a"></a>A végpontok közötti munkafolyamatokat figyelő egyéni követési sémák létrehozása az Azure Logic A-ben

A Azure Logic Apps beépített nyomon követésével engedélyezheti a munkafolyamat egyes részeit. Beállíthat azonban olyan egyéni követést is, amely az eseményeket a munkafolyamatok elejétől a végéig naplózza, például egy logikai alkalmazást, BizTalk Server, SQL Server vagy bármely más réteget tartalmazó munkafolyamatokat. Ez a cikk egyéni kódot tartalmaz, amelyet a logikai alkalmazáson kívüli rétegekben is használhat.

## <a name="custom-tracking-schema"></a>Egyéni követési séma

```json
{
   "sourceType": "",
   "source": {
      "workflow": {
         "systemId": ""
      },
      "runInstance": {
         "runId": ""
      },
      "operation": {
         "operationName": "",
         "repeatItemScopeName": "",
         "repeatItemIndex": ,
         "trackingId": "",
         "correlationId": "",
         "clientRequestId": ""
      }
   },
   "events": [
      {
         "eventLevel": "",
         "eventTime": "",
         "recordType": "",
         "record": {}
      }
   ]
}
```

| Tulajdonság | Kötelező | Típus | Description |
|----------|----------|------|-------------|
| Forrás típusa | Igen | Sztring | A futtatási forrás típusa a következő megengedett értékekkel: `Microsoft.Logic/workflows` , `custom` |
| source | Yes | Karakterlánc-vagy JToken | Ha a forrástípus `Microsoft.Logic/workflows` , a forrás adatainak követniük kell ezt a sémát. Ha a forrás típusa `custom` , a séma egy JToken. |
| systemId | Igen | Sztring | Logic app-rendszerazonosító |
| runId | Igen | Sztring | Logikai alkalmazás futtatásának azonosítója |
| operationName | Igen | Sztring | A művelet neve, például művelet vagy trigger |
| repeatItemScopeName | Igen | Sztring | Elem nevének ismétlése, ha a művelet egy `foreach` vagy `until` hurokon belül van |
| repeatItemIndex | Yes | Egész szám | Azt jelzi, hogy a művelet egy `foreach` vagy `until` hurkon belül van, és az ismétlődő elem indexe. |
| trackingId | Nem | Sztring | Nyomkövetési azonosító az üzenetek korrelációja érdekében |
| correlationId | Nem | Sztring | Az üzenetek korrelációs azonosítója |
| ügyfélkérelem | Nem | Sztring | Az ügyfél feltöltheti ezt a tulajdonságot az üzenetek korrelációja érdekében |
| eventLevel | Igen | Sztring | Az esemény szintje |
| eventTime | Yes | DateTime | Az esemény időpontja UTC formátumban: *éééé-hh-NNTóó: PP: mm. 00000Z* |
| Rekordtípus | Igen | Sztring | A nyomkövetési rekord típusa csak az alábbi megengedett értékkel: `custom` |
| rekord | Yes | JToken | Egyéni bejegyzéstípus csak JToken formátumban |
|||||

## <a name="b2b-protocol-tracking-schemas"></a>B2B protokoll-követési sémák

További információ a B2B protokoll-követési sémákkal kapcsolatban:

* [AS2-követési sémák](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [X12-követési sémák](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Következő lépések

* További információ a [B2B-üzenetek Azure monitor naplókkal való monitorozásáról](../logic-apps/monitor-b2b-messages-log-analytics.md)