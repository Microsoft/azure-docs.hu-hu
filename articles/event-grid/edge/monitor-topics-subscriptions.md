---
title: Témakörök és esemény-előfizetések figyelése – Azure Event Grid IoT Edge | Microsoft Docs
description: Témakörök és esemény-előfizetések figyelése
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: d230be4f74abd61ad7b7f0fdb3facb32ee63da73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "86171533"
---
# <a name="monitor-topics-and-event-subscriptions"></a>Témakörök és esemény-előfizetések figyelése

A Event Grid on Edge számos mérőszámot nyújt a témakörökhöz és az esemény-előfizetésekhez a [Prometheus kiállítási formátumában](https://prometheus.io/docs/instrumenting/exposition_formats/). Ez a cikk a rendelkezésre álló mérőszámokat és azok engedélyezésének módját ismerteti.

## <a name="enable-metrics"></a>Metrikák engedélyezése

Konfigurálja a modult a metrikák kibocsátására úgy, hogy beállítja a `metrics__reporterType` környezeti változót `prometheus` a tároló létrehozási beállításai között:

 ```json
        {
          "Env": [
            "metrics__reporterType=prometheus"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
 ```    

A metrikák `5888/metrics` a http és a https esetében a modulban lesznek elérhetők `4438/metrics` . Http esetében például: `http://<modulename>:5888/metrics?api-version=2019-01-01-preview` . Ezen a ponton a metrikák modulja lekérdezheti a végpontot, hogy összegyűjtse a mérőszámokat az ebben a [példában szereplő architektúrában](https://github.com/veyalla/ehm).

## <a name="available-metrics"></a>Rendelkezésre álló metrikák

A témakörök és az esemény-előfizetések mérőszámokat bocsátanak ki, hogy betekintést nyújtson az események kézbesítésére és a modulok teljesítményére.

### <a name="topic-metrics"></a>Témakör metrikái

| Metric | Leírás |
| ------ | ----------- |
| EventsReceived | A témakörben közzétett események száma
| UnmatchedEvents | A témakörben közzétett események száma, amelyek nem felelnek meg az esemény-előfizetésnek, és el lettek dobva
| SuccessRequests | A témakörben fogadott bejövő közzétételi kérelmek száma
| SystemErrorRequests | A bejövő közzétételi kérelmek száma belső rendszerhiba miatt sikertelen volt.
| UserErrorRequests | A bejövő közzétételi kérelmek száma felhasználói hiba miatt meghiúsult, például helytelenül formázott JSON
| SuccessRequestLatencyMs | Kérelemre adott válasz késésének közzététele ezredmásodpercben


### <a name="event-subscription-metrics"></a>Esemény-előfizetési metrikák

| Metric | Leírás |
| ------ | ----------- |
| DeliverySuccessCounts | A konfigurált végponthoz sikeresen küldött események száma
| DeliveryFailureCounts | A konfigurált végpontnak sikertelenül küldött események száma
| DeliverySuccessLatencyMs | Az események késése (ezredmásodpercben) sikeresen lefutott
| DeliveryFailureLatencyMs | Az események kézbesítési hibáinak késése ezredmásodpercben
| SystemDelayForFirstAttemptMs | Események rendszerkésleltetése az első kézbesítési kísérlet előtt ezredmásodpercben
| DeliveryAttemptsCount | Esemény kézbesítési kísérletek száma – sikeres és sikertelen
| ExpiredCounts | A lejárt és a beállított végpontnak nem továbbított események száma