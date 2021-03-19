---
title: Azure Event Grid által támogatott metrikák
description: Ez a cikk a Azure Event Grid szolgáltatás által támogatott Azure Monitor mérőszámokat tartalmazza.
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 321e318f9dab87fde20b33a6a3a906b020ada622
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104588732"
---
# <a name="metrics-supported-by-azure-event-grid"></a>Azure Event Grid által támogatott metrikák
Ez a cikk a névterek által kategorizált Event Grid mérőszámok listáját tartalmazza. 

## <a name="system-topics"></a>Rendszerrel kapcsolatos témakörök

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Yes|Speciális szűrési értékelések|Darabszám|Összesen|Az esemény-előfizetések között kiértékelt speciális szűrők összesen ebben a témakörben.|EventSubscriptionName|
|DeadLetteredCount|Yes|Kézbesítetlen levelek eseményei|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő, kézbesítetlen levelekből származó összes esemény|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Sikertelen kézbesítések eseményei|Darabszám|Összesen|Az esemény-előfizetésnek nem sikerült kézbesíteni az összes eseményt|Hiba, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Yes|Kézbesítési események|Darabszám|Összesen|Az esemény-előfizetésbe küldött összes esemény|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Cél feldolgozási időtartama|Ezredmásodpercben|Átlag|Cél feldolgozási időtartama (ezredmásodpercben)|EventSubscriptionName|
|DroppedEventCount|Yes|Eldobott események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes eldobott esemény|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Egyeztetett események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes esemény|EventSubscriptionName|
|PublishFailCount|Yes|Sikertelen események közzététele|Darabszám|Összesen|A témakörben nem sikerült közzétenni az összes eseményt|ErrorType, hiba|
|PublishSuccessCount|Yes|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|Nincsenek méretek|
|PublishSuccessLatencyInMs|Yes|Sikeres közzétételi késés|Ezredmásodpercben|Összesen|Sikeres közzétételi késés (ezredmásodpercben)|Nincsenek méretek|
|UnmatchedEventCount|Yes|Páratlan események|Darabszám|Összesen|A témakörben szereplő esemény-előfizetéseknek nem megfelelő események összesen|Nincsenek méretek|


## <a name="custom-topics"></a>Egyéni témakörök

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Yes|Speciális szűrési értékelések|Darabszám|Összesen|Az esemény-előfizetések között kiértékelt speciális szűrők összesen ebben a témakörben.|EventSubscriptionName|
|DeadLetteredCount|Yes|Kézbesítetlen levelek eseményei|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő, kézbesítetlen levelekből származó összes esemény|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Sikertelen kézbesítések eseményei|Darabszám|Összesen|Az esemény-előfizetésnek nem sikerült kézbesíteni az összes eseményt|Hiba, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Yes|Kézbesítési események|Darabszám|Összesen|Az esemény-előfizetésbe küldött összes esemény|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Cél feldolgozási időtartama|Ezredmásodpercben|Átlag|Cél feldolgozási időtartama (ezredmásodpercben)|EventSubscriptionName|
|DroppedEventCount|Yes|Eldobott események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes eldobott esemény|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Egyeztetett események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes esemény|EventSubscriptionName|
|PublishFailCount|Yes|Sikertelen események közzététele|Darabszám|Összesen|A témakörben nem sikerült közzétenni az összes eseményt|ErrorType, hiba|
|PublishSuccessCount|Yes|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|Nincsenek méretek|
|PublishSuccessLatencyInMs|Yes|Sikeres közzétételi késés|Ezredmásodpercben|Összesen|Sikeres közzétételi késés (ezredmásodpercben)|Nincsenek méretek|
|UnmatchedEventCount|Yes|Páratlan események|Darabszám|Összesen|A témakörben szereplő esemény-előfizetéseknek nem megfelelő események összesen|Nincsenek méretek|

## <a name="domains"></a>Tartományok

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Yes|Speciális szűrési értékelések|Darabszám|Összesen|Az esemény-előfizetések között kiértékelt speciális szűrők összesen ebben a témakörben.|Témakör, EventSubscriptionName, DomainEventSubscriptionName|
|DeadLetteredCount|Yes|Kézbesítetlen levelek eseményei|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő, kézbesítetlen levelekből származó összes esemény|Témakör, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|
|DeliveryAttemptFailCount|No|Sikertelen kézbesítések eseményei|Darabszám|Összesen|Az esemény-előfizetésnek nem sikerült kézbesíteni az összes eseményt|Témakör, EventSubscriptionName, DomainEventSubscriptionName, error, ErrorType|
|DeliverySuccessCount|Yes|Kézbesítési események|Darabszám|Összesen|Az esemény-előfizetésbe küldött összes esemény|Témakör, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|No|Cél feldolgozási időtartama|Ezredmásodpercben|Átlag|Cél feldolgozási időtartama (ezredmásodpercben)|Témakör, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Yes|Eldobott események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes eldobott esemény|Témakör, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|MatchedEventCount|Yes|Egyeztetett események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes esemény|Témakör, EventSubscriptionName, DomainEventSubscriptionName|
|PublishFailCount|Yes|Sikertelen események közzététele|Darabszám|Összesen|A témakörben nem sikerült közzétenni az összes eseményt|Témakör, ErrorType, hiba|
|PublishSuccessCount|Yes|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|Témakör|
|PublishSuccessLatencyInMs|Yes|Sikeres közzétételi késés|Ezredmásodpercben|Összesen|Sikeres közzétételi késés (ezredmásodpercben)|Nincsenek méretek|

## <a name="event-subscriptions"></a>Esemény-előfizetések

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|Kézbesítetlen levelek eseményei|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő, kézbesítetlen levelekből származó összes esemény|DeadLetterReason|
|DeliveryAttemptFailCount|No|Sikertelen kézbesítések eseményei|Darabszám|Összesen|Az esemény-előfizetésnek nem sikerült kézbesíteni az összes eseményt|Hiba, ErrorType|
|DeliverySuccessCount|Yes|Kézbesítési események|Darabszám|Összesen|Az esemény-előfizetésbe küldött összes esemény|Nincsenek méretek|
|DestinationProcessingDurationInMs|No|Cél feldolgozási időtartama|Ezredmásodpercben|Átlag|Cél feldolgozási időtartama (ezredmásodpercben)|Nincsenek méretek|
|DroppedEventCount|Yes|Eldobott események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes eldobott esemény|DropReason|
|MatchedEventCount|Yes|Egyeztetett események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes esemény|Nincsenek méretek|


## <a name="extension-topics"></a>Bővítményekkel kapcsolatos témakörök

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|PublishFailCount|Yes|Sikertelen események közzététele|Darabszám|Összesen|A témakörben nem sikerült közzétenni az összes eseményt|ErrorType, hiba|
|PublishSuccessCount|Yes|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|Nincsenek méretek|
|PublishSuccessLatencyInMs|Yes|Sikeres közzétételi késés|Ezredmásodpercben|Összesen|Sikeres közzétételi késés (ezredmásodpercben)|Nincsenek méretek|
|UnmatchedEventCount|Yes|Páratlan események|Darabszám|Összesen|A témakörben szereplő esemény-előfizetéseknek nem megfelelő események összesen|Nincsenek méretek|

## <a name="partner-namespaces"></a>Partneri névterek

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|Kézbesítetlen levelek eseményei|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő, kézbesítetlen levelekből származó összes esemény|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Sikertelen kézbesítések eseményei|Darabszám|Összesen|Az esemény-előfizetésnek nem sikerült kézbesíteni az összes eseményt|Hiba, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Yes|Kézbesítési események|Darabszám|Összesen|Az esemény-előfizetésbe küldött összes esemény|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Cél feldolgozási időtartama|Ezredmásodpercben|Átlag|Cél feldolgozási időtartama (ezredmásodpercben)|EventSubscriptionName|
|DroppedEventCount|Yes|Eldobott események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes eldobott esemény|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Egyeztetett események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes esemény|EventSubscriptionName|
|PublishFailCount|Yes|Sikertelen események közzététele|Darabszám|Összesen|A témakörben nem sikerült közzétenni az összes eseményt|ErrorType, hiba|
|PublishSuccessCount|Yes|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|Nincsenek méretek|
|PublishSuccessLatencyInMs|Yes|Sikeres közzétételi késés|Ezredmásodpercben|Összesen|Sikeres közzétételi késés (ezredmásodpercben)|Nincsenek méretek|
|UnmatchedEventCount|Yes|Páratlan események|Darabszám|Összesen|A témakörben szereplő esemény-előfizetéseknek nem megfelelő események összesen|Nincsenek méretek|


## <a name="partner-topics"></a>Partnertémakörök

|Metric|Exportálható diagnosztikai beállításokkal?|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|Dimenziók|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Yes|Speciális szűrési értékelések|Darabszám|Összesen|Az esemény-előfizetések között kiértékelt speciális szűrők összesen ebben a témakörben.|EventSubscriptionName|
|DeadLetteredCount|Yes|Kézbesítetlen levelek eseményei|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő, kézbesítetlen levelekből származó összes esemény|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Sikertelen kézbesítések eseményei|Darabszám|Összesen|Az esemény-előfizetésnek nem sikerült kézbesíteni az összes eseményt|Hiba, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Yes|Kézbesítési események|Darabszám|Összesen|Az esemény-előfizetésbe küldött összes esemény|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Cél feldolgozási időtartama|Ezredmásodpercben|Átlag|Cél feldolgozási időtartama (ezredmásodpercben)|EventSubscriptionName|
|DroppedEventCount|Yes|Eldobott események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes eldobott esemény|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Egyeztetett események|Darabszám|Összesen|Az esemény-előfizetésnek megfelelő összes esemény|EventSubscriptionName|
|PublishFailCount|Yes|Sikertelen események közzététele|Darabszám|Összesen|A témakörben nem sikerült közzétenni az összes eseményt|ErrorType, hiba|
|PublishSuccessCount|Yes|Közzétett események|Darabszám|Összesen|A témakörben közzétett összes esemény|Nincsenek méretek|
|UnmatchedEventCount|Yes|Páratlan események|Darabszám|Összesen|A témakörben szereplő esemény-előfizetéseknek nem megfelelő események összesen|Nincsenek méretek|


## <a name="next-steps"></a>Következő lépések
Tekintse meg a következő cikket: [diagnosztikai naplók](diagnostic-logs.md)
