---
title: Azure Analysis Services kiszolgáló metrikáinak monitorozása | Microsoft Docs
description: Megtudhatja, hogyan Analysis Services az Azure Metrikaböngésző egy ingyenes eszközt a portálon, hogy segítse a kiszolgálók teljesítményének és állapotának figyelését.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 3991eb421f42ec6645e3321d3a624e226fd12c67
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107314252"
---
# <a name="monitor-server-metrics"></a>A kiszolgáló metrikáinak monitorozása

A Analysis Services metrikákat biztosít az Azure Metrikaböngészőban, amely egy ingyenes eszköz a portálon, amely segít a kiszolgálók teljesítményének és állapotának figyelésében. Figyelje például a memória és a processzor kihasználtságát, az ügyfélkapcsolatok számát és a lekérdezési erőforrások felhasználását. Analysis Services ugyanazt a figyelési keretrendszert használja, mint a legtöbb más Azure-szolgáltatás. További információ: [Az Azure Metrikaböngésző első lépései](../azure-monitor/essentials/metrics-getting-started.md).

A részletesebb diagnosztika végrehajtásához, a teljesítmény nyomon követéséhez és az erőforráscsoport vagy előfizetés több szolgáltatási erőforrása közötti trendek azonosításához használja a [Azure monitor](../azure-monitor/overview.md). A Azure Monitor (szolgáltatás) számlázható szolgáltatást eredményezhet.


## <a name="to-monitor-metrics-for-an-analysis-services-server"></a>Analysis Services-kiszolgáló metrikáinak figyelése

1. A Azure Portal területen válassza a **metrikák** lehetőséget.

    ![Monitorozás az Azure Portalon](./media/analysis-services-monitor/aas-monitor-portal.png)

2. A **metrika** mezőben válassza ki a diagramba felvenni kívánt metrikákat. 

    ![Diagram figyelése](./media/analysis-services-monitor/aas-monitor-chart.png)

<a id="#server-metrics"></a>

## <a name="server-metrics"></a>Kiszolgálói metrikák

A táblázat segítségével meghatározhatja, hogy mely mérőszámok a legmegfelelőbbek a figyelési forgatókönyvekhez. Ugyanazon a diagramon csak az azonos egység mérőszámai jeleníthetők meg.

|Metric|Metrika megjelenítendő neve|Unit (Egység)|Aggregáció típusa|Description|
|---|---|---|---|---|
|CommandPoolJobQueueLength|Parancssori feladatok várólistájának hossza|Darabszám|Átlag|A parancsfájl-készlet várólistájában lévő feladatok száma.|
|Összege|Kapcsolat: aktuális kapcsolatok|Darabszám|Átlag|A létesített ügyfélkapcsolatok aktuális száma.|
|CurrentUserSessions|Aktuális felhasználói munkamenetek|Darabszám|Átlag|A létrejött felhasználói munkamenetek aktuális száma.|
|mashup_engine_memory_metric|M motor memóriája|Bájt|Átlag|Memóriahasználat az adategyesítési motor folyamatai szerint|
|mashup_engine_qpu_metric|M motor QPU|Darabszám|Átlag|QPU-használat az adategyesítési motor folyamatai szerint|
|memory_metric|Memória|Bájt|Átlag|Memory. 0-25 GB-os tartomány S1, 0-50 GB az S2 és 0-100 GB for S4 esetében|
|memory_thrashing_metric|Memóriaakadozás|Százalék|Átlag|Memória átlagos kiverése.|
|CleanerCurrentPrice|Memória: tisztító – aktuális ár|Darabszám|Átlag|A memória aktuális ára, a $ re normalizálva, 1000-ra normalizálva.|
|CleanerMemoryNonshrinkable|Memória: nem csökkenthető a tisztább memória|Bájt|Átlag|A memória mennyisége (bájtban), amelyet a háttérben futó tisztító nem szabályoz.|
|CleanerMemoryShrinkable|Memória: tisztító memória csökkenthető|Bájt|Átlag|A memória mennyisége (bájtban kifejezve), amely a háttér-tisztító általi törlés tárgya.|
|MemoryLimitHard|Memória: rögzített memória|Bájt|Átlag|Rögzített memória korlátja a konfigurációs fájlból.|
|MemoryLimitHigh|Memória: magas a memória korlátozása|Bájt|Átlag|Magas memória korlátja a konfigurációs fájlból.|
|MemoryLimitLow|Memória: kevés a memória korlátja|Bájt|Átlag|Kevés a memória korlátja a konfigurációs fájlból.|
|MemoryLimitVertiPaq|Memória: VertiPaq korlátja|Bájt|Átlag|Memóriabeli korlát a konfigurációs fájlból.|
|MemoryUsage|Memória: memóriahasználat|Bájt|Átlag|A kiszolgálói folyamat memóriahasználat a tisztább memória árának kiszámításakor használt módon. Egyenlő a számláló Process\PrivateBytes és a memóriához rendelt adatmennyiséggel, figyelmen kívül hagyva a memóriát, amelyet a memória-elemzési motor (VertiPaq) leképezett vagy lefoglalt a memória korlátja felett.|
|private_bytes_metric|Saját bájtok |Bájt|Átlag|A memória teljes mennyisége a Analysis Services motor folyamata és az adategyesítési tároló folyamatai lefoglalva, nem beleértve a más folyamatokkal megosztott memóriát.|
|virtual_bytes_metric|Felhasznált virtuális memória jelenlegi mérete (bájt) |Bájt|Átlag|A Analysis Services motor és az adategyesítési tároló folyamatai által használt virtuális címtartomány jelenlegi mérete.|
|mashup_engine_private_bytes_metric|M motor saját bájtjai |Bájt|Átlag|A memória-adategyesítési tároló folyamatainak teljes mennyisége kiosztott, és nem tartalmazza a más folyamatokkal megosztott memóriát.|
|mashup_engine_virtual_bytes_metric|M motor virtuális bájtjai |Bájt|Átlag|A virtuális címtartomány összemashup-tároló folyamatainak jelenlegi mérete a.|
|Kvóta|Memória: kvóta|Bájt|Átlag|Az aktuális memória kvótája (bájt). A memória kvótáját memória-engedélyezési vagy memória-foglalásnak is nevezzük.|
|QuotaBlocked|Memória: blokkolt kvóta|Darabszám|Átlag|A letiltott kvóta-kérelmek aktuális száma, amíg a többi memória kvótája fel nem szabadul.|
|VertiPaqNonpaged|Memória: VertiPaq, nem lapozható|Bájt|Átlag|A memóriában lévő motor általi használatra a munkakészletben zárolt memória mennyisége (bájtban).|
|VertiPaqPaged|Memória: VertiPaq lapozható|Bájt|Átlag|A memóriában tárolt adatmennyiséghez használt lapozható memória bájtjai.|
|ProcessingPoolJobQueueLength|Feldolgozási készlet nyomtatási várólistájának hossza|Darabszám|Átlag|A feldolgozási szál készletének várólistájában nem I/O típusú feladatok száma.|
|RowsConvertedPerSec|Feldolgozás: másodpercenként konvertált sorok száma|CountPerSecond|Átlag|A sorok konvertálásának sebessége a feldolgozás során.|
|RowsReadPerSec|Feldolgozás: másodpercenként beolvasott sorok száma|CountPerSecond|Átlag|Az összes kapcsolódó adatbázisból beolvasott sorok száma.|
|RowsWrittenPerSec|Feldolgozás: másodpercenként írt sorok száma|CountPerSecond|Átlag|A sorok írásának sebessége a feldolgozás során.|
|qpu_metric|QPU|Darabszám|Átlag|QPU. 0-100-es tartomány S1, 0-200 az S2 és 0-400 for S4 esetében|
|QueryPoolBusyThreads|Lekérdezési készlet foglalt szálai|Darabszám|Átlag|A lekérdezési szál készletében lévő foglalt szálak száma.|
|SuccessfullConnectionsPerSec|Másodpercenkénti sikeres kapcsolatok száma|CountPerSecond|Átlag|A sikeres kapcsolatok gyakorisága.|
|CommandPoolBusyThreads|Szálak: a parancssori készlet foglalt szálak|Darabszám|Átlag|A parancsfájl-készletben lévő foglalt szálak száma.|
|CommandPoolIdleThreads|Szálak: parancssori készlet üresjárati szálai|Darabszám|Átlag|Az üresjárati szálak száma a parancs szálának készletében.|
|LongParsingBusyThreads|Szálak: hosszú elemzés – foglalt szálak|Darabszám|Átlag|A foglalt szálak száma a hosszú elemzési szál készletében.|
|LongParsingIdleThreads|Szálak: tartós folyamatok elemzése – üresjárati szálak|Darabszám|Átlag|Az üresjárati szálak száma a hosszú elemzési szál készletében.|
|LongParsingJobQueueLength|Szálak: hosszú elemzési feladatok várólistájának hossza|Darabszám|Átlag|A hosszú elemzési szál készletének várólistájában lévő feladatok száma.|
|ProcessingPoolIOJobQueueLength|Szálak: feldolgozási készlet – I/O-feladatok várólistájának hossza|Darabszám|Átlag|A feldolgozási szál készletének várólistájában lévő I/O-feladatok száma.|
|ProcessingPoolBusyIOJobThreads|Szálak: feldolgozási készlet – foglalt I/O-feladatok szálai|Darabszám|Átlag|Az I/O feladatokat futtató szálak száma a feldolgozási szál készletében.|
|ProcessingPoolBusyNonIOThreads|Szálak: feldolgozási készlet – nem I/O-szálak elfoglalva|Darabszám|Átlag|A feldolgozási szál készletében nem I/O feladatokat futtató szálak száma.|
|ProcessingPoolIdleIOJobThreads|Szálak: feldolgozási készlet – üresjárati I/O-feladatok szálai|Darabszám|Átlag|Az I/O-feladatokhoz tartozó üresjárati szálak száma a feldolgozási szál készletében.|
|ProcessingPoolIdleNonIOThreads|Szálak: feldolgozási készlet üresjáratban nem I/O-szálai|Darabszám|Átlag|A feldolgozási szál készletében a nem I/O-feladatok számára dedikált üresjárati szálak száma.|
|QueryPoolIdleThreads|Szálak: lekérdezési készlet – üresjárati szálak|Darabszám|Átlag|Az I/O-feladatokhoz tartozó üresjárati szálak száma a feldolgozási szál készletében.|
|QueryPoolJobQueueLength|Szálak: lekérdezési készlet feladatok várólistájának hossza|Darabszám|Átlag|A lekérdezési szál készletének várólistájában lévő feladatok száma.|
|ShortParsingBusyThreads|Szálak: rövid elemzés – foglalt szálak|Darabszám|Átlag|A foglalt szálak száma a rövid elemzési szál készletében.|
|ShortParsingIdleThreads|Szálak: rövid elemzési üresjárati szálak|Darabszám|Átlag|Az üresjárati szálak száma a rövid elemzési szál készletében.|
|ShortParsingJobQueueLength|Szálak: rövid elemzési feladatok várólistájának hossza|Darabszám|Átlag|A rövid elemzési szál készletének várólistájában lévő feladatok száma.|
|TotalConnectionFailures|Összes sikertelen Kapcsolatfelvétel|Darabszám|Átlag|A sikertelen csatlakozási kísérletek teljes száma.|
|TotalConnectionRequests|Kapcsolatkérelmek teljes száma|Darabszám|Átlag|A kapcsolatkérelmek teljes száma. |

## <a name="next-steps"></a>Következő lépések
[Azure Monitor áttekintése](../azure-monitor/overview.md)      
[Első lépések az Azure Metrikaböngésző](../azure-monitor/essentials/metrics-getting-started.md)      
[Metrikák a Azure Monitor REST API](/rest/api/monitor/metrics)
