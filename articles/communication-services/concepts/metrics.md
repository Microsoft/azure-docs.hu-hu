---
title: Az Azure kommunikációs szolgáltatás metrika-definíciói
titleSuffix: An Azure Communication Services concept document
description: Ez a dokumentum a Azure Portal elérhető metrikák definícióit tartalmazza.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 129a718175fdda80d4d6852e3d3b4cea609da64d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103492412"
---
# <a name="metrics-overview"></a>A metrikák áttekintése

Az Azure kommunikációs szolgáltatásai jelenleg a csevegéshez és az SMS-hez biztosítanak mérőszámokat. Az [Azure Metrikaböngésző](../../azure-monitor/essentials/metrics-getting-started.md) felhasználható a saját diagramok ábrázolására, a metrikai értékek rendellenességének kivizsgálására, valamint az API-forgalom megismerésére a csevegési és SMS-kérések által kibocsátható mérőszámok használatával.

## <a name="where-to-find-metrics"></a>Hol találhatók a metrikák

Az Azure kommunikációs szolgáltatások csevegési és SMS-szolgáltatásai metrikákat bocsátanak ki az API-kérelmekhez. Ezek a metrikák a kommunikációs szolgáltatások erőforrásának mérőszámok paneljén találhatók. Állandó irányítópultokat is létrehozhat a kommunikációs szolgáltatások erőforrásának munkafüzetek paneljén.

## <a name="metric-definitions"></a>Metrika-definíciók

A kommunikációs szolgáltatások metrikáján két típusú kérelem szerepel: **csevegés API-kérelmek** és **SMS API-kérelmek**.

A csevegési és SMS API-kérelmek metrikája három olyan dimenziót tartalmaz, amelyeket a metrikák adatai szűrésére használhat. Ezek a méretek összevonhatók az `Count` összesítési típussal, és támogatják az összes szabványos Azure-összesítési idősorozatot, beleértve a következőket:,, `Sum` `Average` `Min` és `Max` .

További információ a támogatott összesítési típusokról és az idősorozatok összesítéséről [Az Azure speciális funkciói találhatók Metrikaböngésző](../../azure-monitor/essentials/metrics-charts.md#aggregation)

- **Művelet** – az ACS-csevegési átjárón hívható összes művelet vagy útvonal.
- **Állapotkód** – az állapotkód válasza a kérelem után elküldve.
- **StatusSubClass** – a válasz után küldött állapotkód-sorozat. 


### <a name="chat-api-request-metric-operations"></a>Csevegési API-kérelmek metrikai műveletei

A következő műveletek érhetők el a csevegési API-kérelmek metrikáján:

| Művelet/útvonal    | Leírás                                                                                    |
| -------------------- | ---------------------------------------------------------------------------------------------- |
| GetChatMessage       | Üzenet beolvasása üzenet azonosítója alapján. |
| ListChatMessages     | Lekérdezi a csevegési üzenetek listáját egy adott szálból. |
| SendChatMessage      | Csevegési üzenet küldése egy szálnak. |
| UpdateChatMessage    | Egy csevegési üzenet frissítése. |
| DeleteChatMessage    | Csevegési üzenet törlése. |
| GetChatThread        | Csevegési szál beolvasása. |
| ListChatThreads      | Egy felhasználó csevegési szálai listájának beolvasása. |
| UpdateChatThread     | Egy csevegési szál tulajdonságainak frissítése. |
| CreateChatThread     | Csevegési szál létrehozása. |
| DeleteChatThread     | Egy szál törlése. |
| GetReadReceipts      | Olvasási visszaigazolások beolvasása egy szálhoz. |
| SendReadReceipt      | Olvasási beérkezési eseményt küld egy szálnak egy felhasználó nevében. |
| SendTypingIndicator           | Begépelési eseményt könyvel egy szálra egy felhasználó nevében. |
| ListChatThreadParticipants    | Egy szál tagjainak beolvasása. |
| AddChatThreadParticipants     | Szál tagokat szúr be egy szálba. Ha a tagok már léteznek, a változás nem történik meg. |
| RemoveChatThreadParticipant   | Tag eltávolítása egy szálból. |

:::image type="content" source="./media/chat-metric.png" alt-text="Csevegési API-kérelem metrikája.":::

Ha egy olyan műveletre vonatkozó kérés érkezik, amely nem ismerhető fel, "rossz útvonal" értékű választ fog kapni.

### <a name="sms-api-requests"></a>SMS API-kérelmek

Az SMS API-kérelmek metrikái a következő műveletek érhetők el:

| Művelet/útvonal    | Leírás                                                                                    |
| -------------------- | ---------------------------------------------------------------------------------------------- |
| SMSMessageSent       | SMS-üzenet küldése. |
| SMSDeliveryReportsReceived     | SMS-Kézbesítési jelentések beolvasása |
| SMSMessagesReceived      | SMS-üzenetek beolvasása. |


:::image type="content" source="./media/sms-metric.png" alt-text="SMS API-kérelem metrikája":::

### <a name="authentication-api-requests"></a>Hitelesítési API-kérelmek

A következő műveletek érhetők el a hitelesítési API-kérelmek metrikáján:

| Művelet/útvonal    | Leírás                                                                                    |
| -------------------- | ---------------------------------------------------------------------------------------------- |
| CreateIdentity       | Egy egyedi felhasználót jelképező identitást hoz létre. |
| DeleteIdentity       | Identitás törlése. |
| Okta          | Létrehoz egy hozzáférési jogkivonatot. |
| RevokeToken          | A megadott idő előtt visszavonja az identitáshoz létrehozott összes hozzáférési tokent. |

:::image type="content" source="./media/acs-auth-metrics.png" alt-text="Hitelesítési kérelem metrikája":::

## <a name="next-steps"></a>Következő lépések

- További információ az [adatplatform-mérőszámokról](../../azure-monitor/essentials/data-platform-metrics.md)
