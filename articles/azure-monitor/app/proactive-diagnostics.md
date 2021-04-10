---
title: Intelligens észlelés az Azure Application Insightsban | Microsoft Docs
description: Application Insights végrehajtja az alkalmazás telemetria automatikus elemzését, és figyelmezteti a lehetséges problémákra.
ms.topic: conceptual
ms.date: 02/07/2019
ms.openlocfilehash: 974795e853416ff7a4c051530b050fc663c9423d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100585652"
---
# <a name="smart-detection-in-application-insights"></a>Intelligens detektálás az Application Insightsban
 Az intelligens észlelés automatikusan figyelmezteti a webalkalmazás lehetséges teljesítménybeli problémáit és meghibásodási rendellenességeit. Az alkalmazás által a [Application Insightsnak](./app-insights-overview.md)küldött telemetria proaktív elemzését végzi. Ha hirtelen megnőtt a meghibásodások aránya, vagy az ügyfél vagy a kiszolgáló teljesítménye rendellenes, akkor riasztást kap. Ehhez a szolgáltatáshoz nincs szükség konfigurációra. Akkor működik, ha az alkalmazása elég telemetria küld.

A Smart Detection által kiadott észleléseket elérheti a kapott e-mailekkel, és az intelligens észlelés panelen is.

## <a name="review-your-smart-detections"></a>Az intelligens észlelések áttekintése
Két módon derítheti fel az észleléseket:

* **E-mailt kap** Application Insights. Íme egy tipikus példa:
  
    ![E-mail riasztás](./media/proactive-diagnostics/03.png)
  
    Kattintson a Big (nagy) gombra, és nyissa meg részletesebben a portálon.
* **Az intelligens észlelés** panel Application Insights. A legutóbbi észlelések listájának megtekintéséhez válassza a **vizsgálat** menü **intelligens felismerés** elemét.

![Legutóbbi észlelések megtekintése](./media/proactive-diagnostics/04.png)

Válasszon ki egy észlelést a részletek megtekintéséhez.

## <a name="what-problems-are-detected"></a>Milyen problémák észlelhetők?
Az intelligens észlelés észleli és értesíti a különböző problémákról, például a következőkről:

* [Intelligens észlelés – hibák rendellenességei](./proactive-failure-diagnostics.md). A gépi tanulás segítségével megadhatja az alkalmazáshoz való sikertelen kérelmek várható arányát, a terhelést és más tényezőket. Ha a hiba aránya a várt borítékon kívül esik, riasztást küldünk.
* [Intelligens észlelés – teljesítménybeli rendellenességek](./proactive-performance-diagnostics.md). Értesítéseket kaphat, ha egy művelet vagy függőségi időtartam válaszideje lelassul a korábbi alapkonfigurációhoz képest, vagy ha rendellenes mintázatot azonosít a válaszidő vagy az oldal betöltési ideje alapján.   
* Az általános romlások és problémák, például a [nyomkövetés romlása](./proactive-trace-severity.md), a [memória szivárgása](./proactive-potential-memory-leak.md), a kivételek és a [biztonság elleni](./proactive-application-security-detection-pack.md) [eltérések rendellenes növekedése](./proactive-exception-volume.md) .

(A Súgó hivatkozásai az egyes értesítésekben a megfelelő cikkekre mutatnak.)

## <a name="smart-detection-email-notifications"></a>E-mail-értesítések intelligens észlelése

Az _előzetes_ verzióként megjelölt szabályok kivételével az összes intelligens észlelési szabály alapértelmezés szerint e-mail-értesítések küldésére szolgál az észlelések észlelésekor.

Az e-mail-értesítések egy adott intelligens észlelési szabályhoz való konfigurálásához nyissa meg az intelligens észlelési **Beállítások** panelt, és válassza ki azt a szabályt, amely megnyitja a **szabály szerkesztése** panelt.

Azt is megteheti, hogy Azure Resource Manager-sablonok használatával módosítja a konfigurációt. További részletekért [lásd: Application Insights intelligens észlelési szabályok kezelése Azure Resource Manager-sablonok használatával](./proactive-arm-config.md) .

## <a name="video"></a>Videó

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/112/player]

## <a name="next-steps"></a>Következő lépések
Ezek a diagnosztikai eszközök segítenek megvizsgálni a telemetria az alkalmazásból:

* [Metrika-kezelő](../essentials/metrics-charts.md)
* [Keresési ablak](./diagnostic-search.md)
* [Analitika – hatékony lekérdezési nyelv](../logs/log-analytics-tutorial.md)

Az intelligens észlelés teljesen automatikus. De lehet, hogy néhány riasztást szeretne beállítani?

* [Manuálisan konfigurált metrikai riasztások](../alerts/alerts-log.md)
* [Rendelkezésre állási webes tesztek](./monitor-web-app-availability.md)