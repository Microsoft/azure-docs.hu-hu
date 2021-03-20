---
title: A DDoS-válaszstratégiák összetevői
description: Megtudhatja, hogyan használhatja a Azure DDoS Protection standardot a DDoS-támadásokra való reagálásra.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 2b31a8aa8b126c228ac7e9c3ca182300c710b098
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97814057"
---
# <a name="components-of-a-ddos-response-strategy"></a>A DDoS-válaszstratégiák összetevői

Az Azure-erőforrásokra irányuló DDoS-támadások általában minimális beavatkozást igényelnek a felhasználói szempontból. A DDoS-megoldásnak az incidensekre való reagálási stratégia részeként való beépítésével továbbra is csökkentheti az üzleti folytonosságra gyakorolt hatást.

## <a name="microsoft-threat-intelligence"></a>Microsoft Threat Intelligence

A Microsoft kiterjedt fenyegetést jelentő intelligenciával rendelkezik. Ez a hálózat egy olyan kiterjesztett biztonsági Közösség kollektív ismeretét használja, amely támogatja a Microsoft online szolgáltatások, a Microsoft partnerei és az internetes biztonsági Közösségen belüli kapcsolatokat. 

Kritikus infrastruktúra-szolgáltatóként a Microsoft korai figyelmeztetéseket kap a fenyegetésekkel kapcsolatban. A Microsoft a fenyegetési intelligenciát a online szolgáltatások és a globális ügyfélkörtől gyűjti. A Microsoft az összes fenyegetési intelligenciát visszahelyezi a Azure DDoS Protection termékekbe.

Emellett a Microsoft Digital Crimes Unit (DCU) támadó stratégiákat hajt végre a botneteken. A botnetek a DDoS-támadások irányításának és vezérlésének gyakori forrása.

## <a name="risk-evaluation-of-your-azure-resources"></a>Az Azure-erőforrások kockázati kiértékelése

Fontos, hogy a kockázat hatókörét a DDoS-támadásoktól folyamatosan megértse. Rendszeresen Kérdezzen rá:

- Milyen új, nyilvánosan elérhető Azure-erőforrásokra van szükségük a védelemhez?

- Van egy meghibásodási pont a szolgáltatásban? 

- Hogyan különíthetők el a szolgáltatások a támadás hatásának korlátozásával, miközben a szolgáltatások az érvényes ügyfelek számára is elérhetővé válnak?

- Vannak olyan virtuális hálózatok, amelyeken engedélyezve van a DDoS Protection standard, de nem? 

- A szolgáltatásom aktív/aktív a több régióban feladatátvételsel?

Fontos, hogy tisztában legyen az alkalmazások normál viselkedésével, és felkészüljön a működésre, ha az alkalmazás a DDoS-támadás során nem a várt módon működik. Olyan figyelőket kell konfigurálnia az üzleti szempontból kritikus fontosságú alkalmazásokhoz, amelyek az ügyfél viselkedését utánozzák Az alkalmazás állapotának megállapításához tekintse meg a [monitorozási és diagnosztikai ajánlott eljárásokat](/azure/architecture/best-practices/monitoring#monitoring-and-diagnostics-scenarios) .

Az [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) egy bővíthető Application Performance Management-(APM-) szolgáltatás több platformon futó webes fejlesztőknek. Az élő webalkalmazás figyeléséhez használja a Application Insights. Automatikusan észleli a teljesítménnyel kapcsolatos rendellenességeket. Olyan elemzési eszközöket tartalmaz, amelyekkel diagnosztizálhatja a problémákat, és megtudhatja, hogy a felhasználók milyen műveleteket hajtanak végre az alkalmazással. Úgy tervezték, hogy használatával folyamatosan javíthassa a teljesítményt és a használhatóságot.

## <a name="customer-ddos-response-team"></a>Ügyfél DDoS-válasz csapata

A DDoS-válaszok csapatának létrehozása kulcsfontosságú lépés a támadás gyors és hatékony megválaszolásában. Azonosítsa a szervezeten belüli partnereket, akik a tervezést és a végrehajtást is felügyelik. A DDoS-válasz csapatának alaposan meg kell ismernie a Azure DDoS Protection standard szolgáltatást. Győződjön meg arról, hogy a csapat azonosíthatja és enyhítheti a támadásokat a belső és külső ügyfelekkel, például a Microsoft támogatási csapatával. 

Javasoljuk, hogy a szimulációs gyakorlatokat a szolgáltatás rendelkezésre állásának és a folytonosság megtervezésének szokásos részeként használja, és ezek a gyakorlatok a méretezési teszteket is tartalmazzák. Lásd: [tesztelés szimulációk](test-through-simulations.md) segítségével, amelyből megtudhatja, hogyan szimulálhatja a DDoS-teszt forgalmát az Azure nyilvános végpontokon.

## <a name="alerts-during-an-attack"></a>Riasztások támadás közben

Azure DDoS Protection standard azonosítja és csökkenti a DDoS-támadásokat felhasználói beavatkozás nélkül. Ha értesítést szeretne kapni, ha aktív a védett nyilvános IP-cím, beállíthatja a [riasztásokat](alerts.md).

### <a name="when-to-contact-microsoft-support"></a>Kapcsolatfelvétel a Microsoft ügyfélszolgálatával

Azure DDoS Protection standard ügyfeleink hozzáférhetnek a DDoS Rapid Response (DRR) csapathoz, akik a támadás során segíthetnek a támadási vizsgálatokban, valamint a támadás utáni elemzésekben is. További részletekért lásd a [DDoS gyors válaszát](ddos-rapid-response.md) , beleértve a DRR csapat bevonását.

## <a name="post-attack-steps"></a>Támadás utáni lépések

Mindig jó stratégiát kell elvégeznie a támadás után, és szükség szerint módosítania kell a DDoS-reagálási stratégiát. Megfontolandó szempontok:

- Megszakadt a szolgáltatás vagy a felhasználói élmény a skálázható architektúra hiánya miatt?

- Mely alkalmazások vagy szolgáltatások lettek a leginkább elszenvedve?

- Mennyire hatékony volt a DDoS-reagálási stratégia, és hogyan lehet javítani?

Ha azt gyanítja, hogy a DDoS-támadás alá tartozik, akkor a normál Azure-támogatási csatornákon keresztül is kiterjesztheti.

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [hozhat létre DDoS Protection-tervet](manage-ddos-protection.md).