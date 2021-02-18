---
title: Azure-beli bejárati standard/prémium szintű monitorozási mérőszámok
description: Ez a cikk bemutatja az Azure bejárati ajtó standard/Premium monitorozási metrikáit.
services: frontdoor
author: duau
manager: KumudD
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: bb10fb337972db2696960b530f2d7538bd36a2fb
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098846"
---
# <a name="real-time-monitoring-in-azure-front-door-standardpremium"></a>Valós idejű figyelés az Azure-beli előtérben standard/prémium szintű

> [!Note]
> Ez a dokumentáció az Azure bejárati ajtó standard/Premium (előzetes verzió) verziójához készült. Információt keres az Azure bejárati ajtóról? Megtekintés [itt](../front-door-overview.md).

Az Azure bejárati ajtó standard/Premium integrálva van Azure Monitor, és 11 mérőszámmal rendelkezik, amelyekkel az Azure bejárati ajtó standard/prémium szintű monitorozását valós időben követheti nyomon, hibaelhárítási és hibakeresési problémák esetén.  

Az Azure bejárati ajtó standard/prémium szintű mértékeit, és 60 másodperces időközönként elküldi a metrikákat. A metrikák akár 3 percet is igénybe vehetnek, hogy megjelenjenek a portálon. A metrikák a választott diagramokban vagy rácsban jeleníthetők meg, és a portálon, a PowerShellen, a parancssori felületen és az API-n keresztül érhetők el. További információ: [Azure monitor mérőszámok](../../azure-monitor/platform/data-platform-metrics.md).  

Az alapértelmezett mérőszámok díjmentesek. Felárért további mérőszámokat is engedélyezhet. 

Beállíthat riasztásokat az egyes mérőszámokhoz, például a 4XXErrorRate vagy a 5XXErrorRate küszöbértékét. Ha a hiba mértéke meghaladja a küszöbértéket, a rendszer a konfigurált módon aktiválja a riasztást. További információ: [metrikus riasztások létrehozása, megtekintése és kezelése Azure monitor használatával](../../azure-monitor/platform/alerts-metric.md). 

> [!IMPORTANT]
> Az Azure bejárati ajtó standard/Premium (előzetes verzió) jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="metrics-supported-in-azure-front-door-standardpremium"></a>Az Azure Door standard/Premium kiadásban támogatott metrikák

| Mérőszámok  | Description | Dimenziók |
| ------------- | ------------- | ------------- |
| Találatok aránya (bájt) | A kimenő forgalom AFD-gyorsítótárból való kilépésének aránya a teljes kimenő forgalomra kiszámítva. </br> A **bájtok aránya** = (kimenő forgalomtól a forrástól kimenő)/egress a szélétől. </br> **A bájtok találati arányának kiszámítása során kizárt forgatókönyvek**:</br> 1. explicit módon nem konfigurálhat gyorsítótárat a szabályok motorja vagy a lekérdezési karakterlánc gyorsítótárazási viselkedése alapján. </br> 2. explicit módon konfigurálta a gyorsítótár-vezérlési direktívát tároló nélküli vagy privát gyorsítótárral. </br>3. a bájtok találati aránya alacsony lehet, ha a forgalom nagy része nem a konfiguráció vagy forgatókönyvek alapján történő gyorsítótárazás helyett a forrásra van továbbítva. | Végpont |
| RequestCount | A CDN által kiszolgált ügyfél-kérelmek száma. | Végpont, ügyfél ország, ügyfél-régió, HTTP-állapot, HTTP-állapot csoport |
| ResponseSize | A AFD által kiszolgált ügyfelek kéréseinek száma. |Végpont, ügyfél ország, ügyfél-régió, HTTP-állapot, HTTP-állapot csoport |
| TotalLatency | A CDN által az ügyfélnek **küldött, a CDN által az utolsó válasz bájtig** fogadott kérelmek teljes ideje. |Végpont, ügyfél ország, ügyfél-régió, HTTP-állapot, HTTP-állapot csoport |
| RequestSize | Az ügyfelektől a AFD küldött kérelmekként küldött bájtok száma. | Végpont, ügyfél ország, ügyfél-régió, HTTP-állapot, HTTP-állapot csoport |
| 4XX% ErrorRate | Az összes olyan ügyfél-kérelem százaléka, amelynél a válasz állapotkód 4XX. | Végpont, ügyfél országa, ügyfél régiója |
| 5XX% ErrorRate | Az összes olyan ügyfél-kérelem százaléka, amelynél a válasz állapotkód 5XX. | Végpont, ügyfél országa, ügyfél régiója |
| OriginRequestCount  | A AFD és a forrás között küldött kérések száma | Végpont, forrás, HTTP-állapot, HTTP-állapot csoport |
| OriginLatency | A AFD Edge által a háttérbe irányuló kérés elküldésekor kiszámított idő, amíg a AFD nem kapta meg a háttér utolsó válaszának bájtját. | Végpont, forrás |
| OriginHealth% | A AFD és a forrás közötti sikeres állapot-mintavételek százalékos aránya.| Forrás, származási csoport |
| WAF-kérelmek száma | Egyeztetett WAF-kérelem. | Művelet, szabály neve, házirend neve |

## <a name="access-metrics-in-azure-portal"></a>Hozzáférési metrikák a Azure Portal

1. A Azure Portal menüben válassza a **minden erőforrás** elemet  >>  **\<your-AFD Standard/Premium (Preview) -profile>** .

2. A **figyelés** területen válassza a **metrikák** elemet:

3. A **metrikák** területen válassza ki a hozzáadni kívánt metrikát:

   :::image type="content" source="../media/how-to-monitoring-metrics/front-door-metrics-1.png" alt-text="Képernyőkép a metrikák lapról." lightbox="../media/how-to-monitoring-metrics/front-door-metrics-1-expanded.png":::

4. Szűrő hozzáadásához válassza a **szűrő hozzáadása** elemet:

    :::image type="content" source="../media/how-to-monitoring-metrics/front-door-metrics-2.png" alt-text="Képernyőfelvétel a szűrők mérőszámokhoz való hozzáadásáról." lightbox="../media/how-to-monitoring-metrics/front-door-metrics-2-expanded.png":::
    
5. Válassza az **adatfelosztás alkalmazása** az adatfelosztáshoz különböző dimenziók szerint lehetőséget:

   :::image type="content" source="../media/how-to-monitoring-metrics/front-door-metrics-4.png" alt-text="Képernyőkép a dimenziók mérőszámokhoz való hozzáadásáról." lightbox="../media/how-to-monitoring-metrics/front-door-metrics-4-expanded.png":::

6. Új diagram hozzáadásához válassza az **új diagram** lehetőséget:

## <a name="configure-alerts-in-azure-portal"></a>Riasztások konfigurálása Azure Portalban

1. Riasztásokat állíthat be az Azure bejárati ajtó standard/prémium (előzetes verzió) beállításához a **figyelési**  >>  **riasztások** lehetőség kiválasztásával.

1. Válassza a metrikák szakaszban felsorolt metrikák **új riasztási szabályát** .

A riasztások Azure Monitor alapján lesznek felszámítva. További információ a riasztásokról: [Azure monitor riasztások](../../azure-monitor/platform/alerts-overview.md).

## <a name="next-steps"></a>Következő lépések

- Ismerje meg az [Azure bejárati ajtó standard/prémium szintű jelentéseit](how-to-reports.md).
- Ismerje meg az [Azure bejárati ajtó standard/Premium naplóit](how-to-logs.md).
