---
title: fájlbefoglalás
description: fájlbefoglalás
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 2ed5cbc8c855d2f81986964c93009d75ed28fb8e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026316"
---
**Adatgyűjtés mennyisége és megőrzése** 

| Szint | Napi korlát | Adatmegőrzés | Megjegyzés |
|:---|:---|:---|:---|
| Aktuális/GB-os árképzési szintek<br>(bevezetve április 2018.) | Korlátlan | 30-730 nap | A 31 napos adatmegőrzés további költségek mellett is elérhető. További információ a Azure Monitor díjszabásáról. |
| Örökölt ingyenes szintek<br>(bevezetve április 2016.) | 500 MB | 7 nap | Ha a munkaterület eléri a napi 500 MB-os korlátot, az adatfeldolgozás leáll, és a következő nap elején folytatódik. A napi elszámolás UTC-alapú. Vegye figyelembe, hogy a Azure Security Center által gyűjtött adatok nem szerepelnek a napi 500 MB-os korláton, és ezen a korláton felül tovább lesznek gyűjtve.  |
| Örökölt önálló/GB szintű<br>(bevezetve április 2016.) | Korlátlan | 30 – 730 nap | A 31 napos adatmegőrzés további költségek mellett is elérhető. További információ a Azure Monitor díjszabásáról. |
| Örökölt csomópontok száma (OMS)<br>(bevezetve április 2016.) | Korlátlan | 30 – 730 nap | A 31 napos adatmegőrzés további költségek mellett is elérhető. További információ a Azure Monitor díjszabásáról. |
| Örökölt standard szint | Korlátlan | 30 nap  | A megőrzés nem módosítható |
| Örökölt prémium szint | Korlátlan | 365 nap  | A megőrzés nem módosítható |

**Munkaterületek száma/előfizetés.**

| Tarifacsomag    | Munkaterület korlátja | Megjegyzések
|:---|:---|:---|
| Ingyenes szint  | 10 | Ez a korlát nem növelhető. |
| Az összes többi szintje | Korlátlan | Az erőforráscsoport erőforrásainak száma és az egyes előfizetésekhez tartozó erőforráscsoportok száma korlátozza. |

**Azure Portal**

| Kategória | Korlát | Megjegyzések |
|:---|:---|:---|
| Egy napló lekérdezés által visszaadott maximális rekordok | 10,000 | Csökkentheti az eredményeket a lekérdezés hatóköre, az időtartomány és a lekérdezésben szereplő szűrők használatával. |


**Adatgyűjtő API**

| Kategória | Korlát | Megjegyzések |
|:---|:---|:---|
| Egyetlen bejegyzés maximális mérete | 30 MB | Nagyobb kötetek felosztása több bejegyzésbe. |
| Mezőértékek maximális mérete  | 32 KB | A 32 KB-nál hosszabb mezők csonkolva lesznek. |

**Search API**

| Kategória | Korlát | Megjegyzések |
|:---|:---|:---|
| Egyetlen lekérdezésben visszaadott rekordok maximális száma | 500 000 | |
| Visszaadott adatmennyiség maximális mérete | 64 000 000 bájt (~ 61 MiB)| |
| Lekérdezés maximális futási ideje | 10 perc | További részletek: [időtúllépések](https://dev.loganalytics.io/documentation/Using-the-API/Timeouts) .  |
| Kérelmek maximális száma | 200 kérés/30 másodperc/Azure AD-felhasználó vagy ügyfél IP-címe | Részletekért lásd a [díjszabási korlátokat](https://dev.loganalytics.io/documentation/Using-the-API/Limits) . |

**Általános munkaterület korlátai**

| Kategória | Korlát | Megjegyzések |
|:---|:---|:---|
| Oszlopok maximális száma egy táblában         | 500 | |
| Oszlop nevének maximális száma | 500 | |
| Adatexportálás | Jelenleg nem érhető el | Az Azure Function vagy a Logic App használatával összesítheti és exportálhatja az adatokat. | 

**<a name="data-ingestion-volume-rate">Adatfeldolgozási kötetek aránya</a>**

A Azure Monitor egy nagy léptékű adatszolgáltatás, amely több ezer ügyfelet szolgál ki havonta több, mint havi terabájt adatküldéssel. A mennyiségi korlát a bérlős-környezetben a hirtelen betöltési tüskékkel Azure Monitor ügyfelek elkülönítését tervezi. Az alapértelmezett betöltési mennyiség 500 MB (tömörített), a munkaterületeken van definiálva, ez körülbelül **6 GB/perc** tömörítve van – a tényleges méret a napló hosszától és a tömörítési aránytól függően változhat. A mennyiségi díjszabási korlát minden betöltött adatokra vonatkozik, függetlenül attól, hogy az Azure-erőforrásoktól a [diagnosztikai beállítások](../articles/azure-monitor/platform/diagnostic-settings.md), [az adatgyűjtő API vagy az](../articles/azure-monitor/platform/data-collector-api.md) ügynökök használatával küldtek el.

Ha olyan munkaterületre küldi az adatmennyiséget, amely a munkaterületen konfigurált küszöbérték 80%-ánál nagyobb, akkor az eseményt 6 óránként küldi el a munkaterület *műveleti* táblájába, amíg a küszöbérték továbbra is meghalad. Ha a betöltött mennyiség meghaladja a küszöbértéket, a rendszer bizonyos adatvesztést végez, és az eseményt 6 óránként küldi el a munkaterület *műveleti* táblájába, amíg a küszöbérték továbbra is túllépve lesz. Ha a betöltési mennyiség aránya továbbra is meghaladja a küszöbértéket, vagy hamarosan várhatóan nem éri el, kérheti, hogy egy támogatási kérelem megnyitásával növelje azt. 

Lásd: [log Analytics munkaterület állapotának figyelése Azure monitor-ben](../articles/azure-monitor/platform/monitor-workspace.md) , hogy riasztási szabályokat hozzon létre, hogy proaktívan értesüljön a betöltési korlátok elérésekor.

>[!NOTE]
>Attól függően, hogy mennyi ideig használta a Log Analytics, lehet, hogy hozzáfér a régi díjszabási csomagokhoz. További információ a [log Analytics korábbi díjszabási szintjeiről](../articles/azure-monitor/platform/manage-cost-storage.md#legacy-pricing-tiers).