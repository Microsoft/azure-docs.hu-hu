---
title: A tárolók Azure Monitorának figyelési díja | Microsoft Docs
description: Ez a cikk ismerteti a metrikák figyelési költségeit, & a Azure Monitor által gyűjtött leltározási adatokat, amelyek segítenek az ügyfeleknek a használat és a kapcsolódó költségek kezelésében.
ms.topic: conceptual
ms.date: 05/29/2020
ms.openlocfilehash: 0a3118e1dd839eced5e1f15d28feff4bbb58014f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100620095"
---
# <a name="understand-monitoring-costs-for-azure-monitor-for-containers"></a>Az Azure Monitor tárolók monitorozásával kapcsolatos költségeinek ismertetése

Ez a cikk a tárolók Azure Monitorának díjszabását ismerteti a következők megismerése érdekében:

* A költségek kezdeti megbecslése az elemzés engedélyezése előtt

* A költségek mérése a tárolók Azure Monitor után egy vagy több tároló számára engedélyezve

* Az adatgyűjtés szabályozása és a költséghatékonyság csökkentése

Azure Monitor naplók a Kubernetes-fürt által generált adatokat gyűjtik, indexelik és tárolják. 

A Azure Monitor díjszabási modell elsősorban a GB-ban betöltött adatmennyiségtől függ a Log Analytics munkaterületen. Egy Log Analytics munkaterület díja nem csak a begyűjtött adatok mennyiségétől függ, hanem a kiválasztott csomagtól, és azt is, hogy mennyi ideig kell tárolnia a fürtökből generált adatokat.

>[!NOTE]
>A méretek és a díjszabás csak a mintavételezési becslések esetében használható. A legfrissebb díjszabást a Azure Monitor Log Analytics díjszabási modellje és az Azure-régió Azure Monitor [díjszabási](https://azure.microsoft.com/pricing/details/monitor/) oldalán találja.

A következő összefoglalja, hogy milyen típusú adatokat gyűjt a rendszer a Kubernetes-fürtökről a költségeket befolyásoló és a használat alapján testreszabható tárolók Azure Monitor.

- StdOut, a stderr a fürt összes Kubernetes-névterében lévő összes figyelt tárolóból

- Tároló környezeti változók a fürt összes figyelt tárolójából

- Befejezett Kubernetes-feladatok/hüvelyek a fürtben, amely nem igényel figyelést

- A Prometheus-metrikák aktív selejtezése

- A Kubernetes fő csomópontjának naplóinak [diagnosztikai naplója](../../aks/view-master-logs.md) az AK-fürtben a fő összetevők, például a *Kube-apiserver* és a *Kube-Controller-Manager* által generált naplózási adatok elemzéséhez.

## <a name="what-is-collected-from-kubernetes-clusters"></a>A Kubernetes-fürtökből gyűjtött adatok

A tárolók Azure Monitor a Log Analytics-munkaterületen naplózási adatként begyűjtött mérőszámok és leltári elemek előre meghatározott készletét tartalmazzák. Az alább felsorolt mérőszámok alapértelmezés szerint percenként lesznek gyűjtve.

### <a name="node-metrics-collected"></a>Összegyűjtött csomópont-metrikák

A következő lista az összegyűjtött csomópontok 24 mérőszáma:

- cpuUsageNanoCores
- cpuCapacityNanoCores
- cpuAllocatableNanoCores
- memoryRssBytes
- memoryWorkingSetBytes
- memoryCapacityBytes
- memoryAllocatableBytes
- restartTimeEpoch
- használatban (lemez)
- ingyenes (lemez)
- used_percent (lemez)
- io_time (lemezforgalmát)
- írások (lemezforgalmát)
- olvasás (lemezforgalmát)
- write_bytes (lemezforgalmát)
- write_time (lemezforgalmát)
- iops_in_progress (lemezforgalmát)
- read_bytes (lemezforgalmát)
- read_time (lemezforgalmát)
- err_in (net)
- err_out (net)
- bytes_recv (net)
- bytes_sent (net)
- Kubelet_docker_operations (Kubelet)

### <a name="container-metrics"></a>Tároló metrikái

A következő lista az összegyűjtött tárolók nyolc metrikáját tartalmazza:

- cpuUsageNanoCores
- cpuRequestNanoCores
- cpuLimitNanoCores
- memoryRssBytes
- memoryWorkingSetBytes
- memoryRequestBytes
- memoryLimitBytes
- restartTimeEpoch

### <a name="cluster-inventory"></a>Fürt leltározása

A következő lista az alapértelmezés szerint gyűjtött fürt leltározási adatokat tartalmazza:

- KubePodInventory – másodpercenként 1 percenként
- KubeNodeInventory – 1/perc percenként
- KubeServices – 1 percenként
- ContainerInventory – 1/tároló percenként

## <a name="estimating-costs-to-monitor-your-aks-cluster"></a>Az AK-fürt figyelési költségeinek becslése

Az alábbi becslés az Azure Kubernetes Service (ak) fürtön alapul, amely a következő méretezési példát mutatja. Emellett a becslés csak a begyűjtött metrikák és leltári adatok esetében érvényes. A tárolók naplófájljai (StdOut, stderr és környezeti változók) a számítási feladatok által generált naplók méretétől függően változnak, és a becslésből kizárják őket.

Ha engedélyezte a következőképpen konfigurált AK-fürtök figyelését,

- Három csomópont
- Két lemez/csomópont
- Egy hálózati adapter/csomópont
- 20 hüvely (egy tároló minden Pod = 20 tárolóban összesen)
- Két Kubernetes-névtér
- Öt Kubernetes-szolgáltatás (beleértve a Kube-rendszerhüvelyeket, szolgáltatásokat és névtereket)
- Gyűjtés gyakorisága = 60 mp (alapértelmezett)

A hozzárendelt Log Analytics munkaterületen megtekintheti az óránként generált táblákat és adatmennyiséget. További információ ezekről a táblákról: [tároló rekordjai](container-insights-log-search.md#container-records).

|Tábla | Becsült méret (MB/óra) |
|------|---------------|
|Teljesítmény | 12.9 |
|InsightsMetrics | 11,3 |
|KubePodInventory | 1.5 |
|KubeNodeInventory | 0,75 |
|KubeServices | 0.13 |
|ContainerInventory | 3,6 |
|KubeHealth | 0.1 |
|KubeMonAgentEvents |0,005 |

Összesen = 31 MB/óra = 23,1 GB/hó (egy hónap = 31 nap)

Az utólagos elszámolású modellhez Log Analytics alapértelmezett [díjszabását](https://azure.microsoft.com/pricing/details/monitor/) használva a havi Azure monitor költséget is megbecsülheti. A kapacitás foglalását követően a kiválasztott foglalástól függően a díj havonta magasabb lesz.

## <a name="controlling-ingestion-to-reduce-cost"></a>A lenyelés szabályozása a költségek csökkentése érdekében

Vegyünk egy olyan forgatókönyvet, amelyben a szervezete különböző üzleti egységei megosztják a Kubernetes infrastruktúráját és egy Log Analytics munkaterületet. Minden üzleti egység Kubernetes-névtérrel elválasztva. Megjelenítheti, hogy mennyi adatot tölt be az egyes munkaterületeken az **adatfelhasználás** runbook, amely a **munkafüzetek megtekintése** legördülő menüből érhető el.

[![Munkafüzetek megjelenítése legördülő lista](media/container-insights-cost/workbooks-dropdown.png)](media/container-insights-cost/workbooks-dropdown.png#lightbox)


Ez a munkafüzet segíti az adatok forrásának megjelenítését anélkül, hogy saját, a dokumentációban megosztható lekérdezéseket kellene létrehoznia. Ebben a munkafüzetben vannak olyan diagramok, amelyekkel megtekintheti a számlázható adatait az alábbi szempontok szerint:

- A GB-ban betöltött számlázható összes adat a megoldás szerint
- Tároló-naplók által betöltött számlázható adatmennyiség (alkalmazás-naplók)
- A számlázható tároló a Kubernetes-névtér által betöltött adatot naplózza
- Számlázható tároló naplózza a fürt neve szerint elkülönítve tárolt adatmennyiségeket
- Logsource-bejegyzés által betöltött számlázható tároló naplózási adata
- Diagnosztikai főcsomóponti naplók által betöltött számlázható diagnosztikai adatok

[![Adathasználati munkafüzet](media/container-insights-cost/data-usage-workbook.png)](media/container-insights-cost/data-usage-workbook.png#lightbox)

Ha szeretné megtudni, hogyan kezelhetők a munkafüzetek jogai és engedélyei, tekintse át a [hozzáférés-vezérlés](../visualize/workbooks-access-control.md)című témakört.

Miután elvégezte az elemzést annak meghatározásához, hogy melyik forrás vagy forrás hozza létre a legtöbb adat, vagy több olyan adat, amely meghaladja a követelményeket, újrakonfigurálhatja az adatgyűjtést. Az stdout, a stderr és a környezeti változók gyűjtésének konfigurálásáról az [ügynök adatgyűjtési beállításainak konfigurálása](container-insights-agent-config.md) című cikkben olvashat bővebben.

A következő példák azt szemléltetik, hogy milyen módosításokat alkalmazhat a fürtön a ConfigMap fájl módosításával, hogy segítsen a költségeket megszabni.

1. Tiltsa le az stdout-naplókat a fürt összes névterében a következő ConfigMap fájl módosításával:

    ```
    [log_collection_settings]       
       [log_collection_settings.stdout]          
          enabled = false
    ```

2. Tiltsa le a stderr-naplók gyűjtését a fejlesztői névtérből (például: **dev-test**), és folytassa a stderr-naplók más névterekről való gyűjtését (például a **Prod** és az **alapértelmezett értéket**) a ConfigMap-fájlban található következők módosításával:

    >[!NOTE]
    >A Kube-rendszernapló-gyűjtemény alapértelmezés szerint le van tiltva. A rendszer megőrzi az alapértelmezett beállítást, és a **fejlesztői és tesztelési** névtereket hozzáadja a kizárási névterek listájához a stderr.

    ```
    [log_collection_settings.stderr]          
       enabled = true          
          exclude_namespaces = ["kube-system", "dev-test"]
    ```

3. Tiltsa le a környezeti változók gyűjteményét a fürtön a ConfigMap fájlban található következők módosításával. Ez minden Kubernetes-névtérben található tárolóra érvényes. 

    ```
    [log_collection_settings.env_var]
        enabled = false
    ```

4. A Befejezett feladatok törléséhez a ConfigMap fájl következő elemének módosításával határozza meg a karbantartási házirendet a feladat definíciójában:

    ```
    apiVersion: batch/v1
    kind: Job
    metadata:
      name: pi-with-ttl
    spec:
      ttlSecondsAfterFinished: 100
    ```

Ha a módosítások közül egyet vagy többet alkalmaz a ConfigMaps, tekintse meg a [frissített ConfigMap alkalmazása](container-insights-prometheus-integration.md#applying-updated-configmap) a fürtre való alkalmazásával foglalkozó témakört.

### <a name="prometheus-metrics-scraping"></a>Prometheus-metrikák selejtezése

Ha a Prometheus- [metrikai selejtet](container-insights-prometheus-integration.md)használja, ügyeljen arra, hogy a fürtből összegyűjtött mérőszámok számának korlátozásához vegye figyelembe a következőket:

- Győződjön meg arról, hogy a selejtes gyakoriság beállítása optimális (az alapértelmezett érték 60 másodperc). Amíg 15 másodpercre növelheti a gyakoriságot, meg kell győződnie arról, hogy a felkapart metrikák közzé lesznek téve ezen a gyakoriságon. Ellenkező esetben a rendszer sok ismétlődő metrikát továbbít, és a Log Analytics munkaterületre küldi az adatfeldolgozási és-megőrzési költségek hozzáadásakor, de kevesebb értékkel rendelkezik. 

- A tárolók Azure Monitor támogatja a kizáró &-befoglalási listát metrika neve alapján. Ha például a fürtben **kubedns** -metrikákat használ fel, akkor előfordulhat, hogy több százat kell megadnia, amelyek alapértelmezés szerint lekaparják az adatokat, de valószínűleg csak egy részhalmaz érdekli. Erősítse meg, hogy megadta a kaparni kívánt metrikák listáját, vagy kizár másokat, kivéve, ha az adatfeldolgozási kötetre menteni szeretné. Egyszerűen engedélyezheti a selejtet, és nem használhatja a metrikák nagy részét, ami csak a Log Analytics számla további díját fogja felvenni.

- A pod-megjegyzéseken keresztüli adatkaparás során győződjön meg arról, hogy a névtér alapján szűri, hogy kizárhatja a nem használt névterekről származó Pod-metrikák (például a **fejlesztői és tesztelési** névterek) selejtét.

## <a name="next-steps"></a>Következő lépések

További információ arról, hogyan állapítható meg, hogy milyen költségek várhatók a legutóbbi használati mintákon a tárolók Azure Monitor gyűjtött adatok alapján. lásd: [a használat kezelése és a becsült költségek](../platform/manage-cost-storage.md).