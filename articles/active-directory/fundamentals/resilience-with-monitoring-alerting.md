---
title: Rugalmasság a monitorozás és az elemzés révén a Azure AD B2C használatával | Microsoft Docs
description: Rugalmasság a monitorozás és az elemzés révén Azure AD B2C
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a834d4d30c40b618b1601a7f8901c68143ef4912
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648517"
---
# <a name="resilience-through-monitoring-and-analytics"></a>Rugalmasság a monitorozás és az elemzés révén

A figyelés maximalizálja az alkalmazások és szolgáltatások rendelkezésre állását és teljesítményét. Átfogó megoldást kínál az infrastruktúra és az alkalmazások telemetria gyűjtésére, elemzésére és működésének elvégzésére. A riasztások proaktívan értesítik Önt, ha a szolgáltatással vagy alkalmazásokkal kapcsolatban problémák léptek fel. Lehetővé teszik a problémák azonosítását és megoldását, mielőtt a szolgáltatás végfelhasználói értesítést kapnak. Az [Azure AD log Analytics](https://azure.microsoft.com/services/monitor/?OCID=AID2100131_SEM_6d16332c03501fc9c1f46c94726d2264:G:s&ef_id=6d16332c03501fc9c1f46c94726d2264:G:s&msclkid=6d16332c03501fc9c1f46c94726d2264#features) segítségével elemezheti, keresheti meg a naplókat és a bejelentkezési naplókat, és létrehozhat egyéni nézeteket.

## <a name="monitor-and-get-notified-through-alerts"></a>Riasztások figyelése és értesítésen keresztüli beszerzése

A rendszer és az infrastruktúra monitorozása kritikus fontosságú a szolgáltatások általános állapotának biztosítása érdekében. Az üzleti mérőszámok meghatározásával kezdődik, például új felhasználói beérkezés, végfelhasználói hitelesítési díjak és konverzió. Adja meg az ilyen kijelzőket a figyeléshez. Ha az előléptetés vagy az üdülési forgalom miatt várhatóan nagy lendületet szeretne, módosítsa a becsléseket kifejezetten az eseményre, és az üzleti metrikák megfelelő teljesítménymutatóját. Az esemény után térjen vissza az előző teljesítményteszthez.

Hasonlóképpen, a hibák és a teljesítménybeli fennakadások észlelése, a jó alapértékek beállítása, majd a riasztások meghatározása elengedhetetlen megoldás a felmerülő problémák azonnali megválaszolásához.

![A képen a monitorozási és elemzési összetevők láthatók](media/resilience-with-monitoring-alerting/monitoring-analytics-architecture.png)

### <a name="how-to-implement-monitoring-and-alerting"></a>A monitorozás és a riasztások megvalósítása

- **Figyelés**: a [Azure monitor](../../active-directory-b2c/azure-monitor.md) használatával folyamatosan figyelheti az állapotot a legfontosabb szolgáltatási szintű célkitűzések (SLO) tekintetében, és értesítéseket kaphat, amikor kritikus változások történnek. Először azonosítsa Azure AD B2C szabályzatot vagy egy olyan alkalmazást, amely a vállalat kritikus fontosságú összetevője, amelynek állapotát figyelni kell az SLO fenntartásához. Azonosítsa a slo illeszkedő legfontosabb kijelzőket.
Például nyomon követheti a következő mérőszámokat, mivel a hirtelen csökkenés vagy az üzleti adatvesztéshez vezet.

  - **Kérelmek összesen**: a Azure ad B2C szabályzatba küldött kérelmek teljes száma.

  - **Sikerességi arány (%)**: sikeres kérések/a kérelmek teljes száma.

  A [legfontosabb mutatók](../../active-directory-b2c/view-audit-logs.md) az [Application ininsightben](../../active-directory-b2c/analytics-with-application-insights.md) érhetők [el, ahol](../../active-directory-b2c/analytics-with-application-insights.md)Azure ad B2C házirend-alapú naplókat, naplókat és bejelentkezési naplókat tárol.  

   - **Vizualizációk**: a log Analytics-Build irányítópultok használatával vizuálisan figyelheti a fő kijelzőket.

   - **Aktuális időszak**: időbeli diagramok létrehozása az összes kérelem és sikerességi arány változásainak megjelenítéséhez (%) az aktuális időszakban, például az aktuális héten.

   - **Előző időszak**: időbeli diagramok létrehozása az összes kérelem és sikerességi arány változásainak megjelenítéséhez (%) egy korábbi időszakra hivatkozva, például a múlt héten.

- **Riasztás**: a log Analytics használatával olyan [riasztásokat](../../azure-monitor/alerts/alerts-log.md) határozhat meg, amelyek akkor jelentkeznek be, amikor hirtelen változások léptek fel a legfontosabb mutatókon. Ezek a változások negatív hatással lehetnek a slo. A riasztások az értesítési módszerek különböző formáját használják, például az e-maileket, az SMS-t és a webhookokat. Első lépésként Definiáljon egy olyan feltételt, amely küszöbértékként működik, és amely riasztást indít el. Például:
  - Riasztás a teljes kérelmek hirtelen eldobásával kapcsolatban: riasztás elindítása, ha a kérelmek teljes száma hirtelen csökken. Ha például 25%-kal csökken a kérelmek teljes száma az előző időszakhoz képest, riasztást kell létrehoznia.  
  - Riasztás a sikerességi arány jelentős csökkenése ellen (%): riasztás elindítása, ha a kiválasztott szabályzat sikerességi aránya jelentősen csökken.
  - Riasztás fogadásakor hárítsa el a problémát a Azure AD B2C [log Analytics](../reports-monitoring/howto-install-use-log-analytics-views.md), [Application Insights](../../active-directory-b2c/troubleshoot-with-application-insights.md)és [vs Code bővítményének](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) használatával. Miután feloldotta a problémát, és telepített egy frissített alkalmazást vagy házirendet, továbbra is figyeli a legfontosabb jelzőket, amíg vissza nem tér a normál tartományhoz.

- **Szolgáltatás-riasztások**: a [Azure ad B2C szolgáltatási szintű riasztások](../../service-health/service-health-overview.md) segítségével értesítést kaphat a szolgáltatással kapcsolatos problémákról, a tervezett karbantartásról, az egészségügyi tanácsadásról és a biztonsági tanácsadóról.

- **Jelentéskészítés**: [a log Analytics használatával](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)olyan jelentéseket hozhat létre, amelyek segítséget nyújtanak a felhasználói információk, a technikai kihívások és a növekedési lehetőségek megismerésében.
  - **Állapot-irányítópult**: [Egyéni irányítópultok létrehozása az Azure irányítópult](../../azure-monitor/app/tutorial-app-dashboards.md) funkciójával, amely támogatja a diagramok log Analytics lekérdezésekkel való hozzáadását. Például azonosíthatja a sikeres és sikertelen bejelentkezések mintáját, a hibák okait és a telemetria a kérések elvégzéséhez használt eszközökről.
  - **Azure ad B2C utazások elhagyása**: a [munkafüzet](https://github.com/azure-ad-b2c/siem#list-of-abandon-journeys) segítségével nyomon követheti az elhagyott Azure ad B2C utak listáját, ahol a felhasználó elindította a bejelentkezést vagy a regisztrációt, de soha nem fejezte be. Részletesen ismerteti a szabályzat AZONOSÍTÓját és a felhasználó által az utazás megszüntetése előtt végrehajtott lépések részletezését.
  - **Azure ad B2C figyelési munkafüzetek**: a [figyelési munkafüzetek](https://github.com/azure-ad-b2c/siem)használata, beleértve a Azure ad B2C irányítópultot, a többtényezős hitelesítési (MFA) műveleteket, a feltételes hozzáférési jelentést és a keresési naplókat a correlationId alapján, így jobban betekintést nyerhet az Azure ad B2C-környezet állapotára.
  
## <a name="next-steps"></a>Következő lépések

- [Rugalmassági erőforrások Azure AD B2C-fejlesztőknek](resilience-b2c.md)
  - [Rugalmas végfelhasználói élmény](resilient-end-user-experience.md)
  - [Rugalmas felületek külső folyamatokkal](resilient-external-processes.md)
  - [Rugalmasság a fejlesztőknek ajánlott eljárások révén](resilience-b2c-developer-best-practices.md)
- [Rugalmasság kialakítása a hitelesítési infrastruktúrában](resilience-in-infrastructure.md)
- [A hitelesítés és az engedélyezés rugalmasságának fokozása az alkalmazásokban](resilience-app-development-overview.md)