---
title: Figyelési Azure Machine Learning | Microsoft Docs
description: Megtudhatja, hogyan használhatja a Azure Monitor a metrikák megtekintésére, elemzésére és riasztások létrehozásához Azure Machine Learningokból.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.custom: subject-monitoring
ms.date: 10/01/2020
ms.openlocfilehash: c9b7230ca734bba1527ec5ecd975e1bae141a9b4
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99584685"
---
# <a name="monitor-azure-machine-learning"></a>Az Azure Machine Learning monitorozása

Ha kritikus fontosságú alkalmazásokat és üzleti folyamatokat kíván használni az Azure-erőforrásokon, figyelnie kell ezeket az erőforrásokat a rendelkezésre állással, a teljesítménnyel és a művelettel kapcsolatban. Ez a cikk a Azure Machine Learning által generált figyelési információkat ismerteti, valamint azt, hogy miként elemezheti és figyelmeztetheti ezeket az információkat a Azure Monitor.

> [!TIP]
> A jelen dokumentumban található információk elsősorban __rendszergazdák__ számára készültek, mivel a Azure Machine learning szolgáltatás és az ahhoz kapcsolódó Azure-szolgáltatások figyelését ismerteti. Ha Ön egy __adattudós__ vagy __fejlesztő__, és szeretné figyelni a *modell-oktatóprogramokra* vonatkozó információkat, tekintse meg a következő dokumentumokat:
>
> * [Betanítási futtatások indítása, figyelése és megszakítása](how-to-manage-runs.md)
> * [A betanítási futtatások mérőszámainak naplózása](how-to-track-experiments.md)
> * [Kísérletek nyomon követése az MLflow használatával](how-to-use-mlflow.md)
> * [Futtatások megjelenítése a TensorBoard használatával](how-to-monitor-tensorboard.md)
>
> Ha a webszolgáltatásként vagy IoT Edge-modulként telepített modellek által létrehozott információkat szeretné figyelni, tekintse meg a [modell adatainak](how-to-enable-data-collection.md) és [a figyelő összegyűjtése a Application Insights](how-to-enable-app-insights.md)használatával című témakört.

## <a name="what-is-azure-monitor"></a>Mi az Azure Monitor?

A Azure Machine Learning a [Azure monitor](../azure-monitor/overview.md)használatával hoz létre figyelési adatgyűjtési szolgáltatást, amely az Azure-ban teljes verem-figyelési szolgáltatás. Azure Monitor az Azure-erőforrások figyelésére szolgáló funkciók teljes készletét biztosítja. Más felhőkben és a helyszínen is figyelheti az erőforrásokat.

A következő fogalmakat ismerteti az [Azure-erőforrások Azure Monitorával való monitorozásával foglalkozó](../azure-monitor/insights/monitor-azure-resource.md)cikkből:

- Mi az Azure Monitor?
- A figyeléshez kapcsolódó költségek
- Az Azure-ban összegyűjtött adatok figyelése
- Adatgyűjtés konfigurálása
- Szabványos eszközök az Azure-ban a figyelési adatok elemzéséhez és riasztásához

Az alábbi részekben a cikk a Azure Machine Learninghoz összegyűjtött adatok leírásával épít. Ezek a szakasz példákat is tartalmaznak az adatgyűjtés konfigurálására és az adatok elemzésére az Azure-eszközökkel.

> [!TIP]
> A Azure Monitorhez kapcsolódó költségek megismeréséhez tekintse meg a [használati és becsült költségeket](../azure-monitor/platform/usage-estimated-costs.md). Ha meg szeretné tudni, hogy mire van szüksége ahhoz, hogy az adatai megjelenjenek Azure Monitorban, tekintse meg az [adatfeldolgozási idő naplózása](../azure-monitor/platform/data-ingestion-time.md)című témakört.

## <a name="monitoring-data-from-azure-machine-learning"></a>Adatok figyelése Azure Machine Learningról

Azure Machine Learning ugyanolyan típusú figyelési adatokat gyűjt, mint az [Azure-erőforrások monitorozásával](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data)kapcsolatos további Azure-erőforrások. 

A Azure Machine Learning által létrehozott naplók és metrikák részletes ismertetését lásd: [Azure Machine learning monitorozási adatok referenciája](monitor-resource-reference.md) .

<a id="configuration"></a>

## <a name="collection-and-routing"></a>Gyűjtés és Útválasztás

A platform metrikáit és a tevékenység naplóját a rendszer automatikusan összegyűjti és tárolja, de a diagnosztikai beállítások segítségével más helyekre is átirányíthatja őket.  

Az erőforrás-naplók gyűjtése és tárolása addig nem történik meg, amíg létre nem hozza a diagnosztikai beállításokat, és egy vagy több helyre irányítja őket.

A diagnosztikai beállításoknak a Azure Portal, a CLI vagy a PowerShell használatával történő létrehozásával kapcsolatos részletes folyamatért lásd: [diagnosztikai beállítás létrehozása a platform-naplók és-metrikák összegyűjtéséhez az Azure-ban](../azure-monitor/platform/diagnostic-settings.md) . Diagnosztikai beállítás létrehozásakor meg kell adnia, hogy a rendszer milyen típusú naplókat gyűjtsön. A Azure Machine Learning kategóriái [Azure Machine learning figyelési adatreferenciában](monitor-resource-reference.md#resource-logs)vannak felsorolva.

> [!IMPORTANT]
> Ezen beállítások engedélyezéséhez további Azure-szolgáltatások (Storage-fiók, Event hub vagy Log Analytics) szükségesek, ami növelheti a költségeket. A becsült költségek kiszámításához tekintse meg az [Azure díjszabási számológépét](https://azure.microsoft.com/pricing/calculator).

A következő naplókat konfigurálhatja Azure Machine Learninghoz:

| Kategória | Leírás |
|:---|:---|
| AmlComputeClusterEvent | Azure Machine Learning számítási fürtök eseményei. |
| AmlComputeClusterNodeEvent | Azure Machine Learning számítási fürt csomópontjain belüli események. |
| AmlComputeJobEvent | Azure Machine Learning számításon futó feladatok eseményei. |

> [!NOTE]
> Ha a mérőszámokat egy diagnosztikai beállításban engedélyezi, a dimenzió adatai jelenleg nem szerepelnek a Storage-fiókba, az Event hub-ba vagy a log analyticsbe továbbított adatok részeként.

A gyűjtött mérőszámokat és naplókat a következő szakaszokban tárgyaljuk.

## <a name="analyzing-metrics"></a>Mérőszámok elemzése

A metrikákat a **Azure monitor** menüből **megnyitva** elemezheti Azure Machine learning mérőszámait, valamint más Azure-szolgáltatások metrikáit is. Az eszköz használatával kapcsolatos részletekért lásd: az [Azure Metrikaböngésző használatának első lépései](../azure-monitor/platform/metrics-getting-started.md) .

A gyűjtött platform metrikáinak listáját itt tekintheti meg: [Monitoring Azure Machine learning adathivatkozási mérőszámok](monitor-resource-reference.md#metrics).

Azure Machine Learning összes mérőszáma a névtér **Machine learning szolgáltatás munkaterületen** található.

![Metrikaböngésző a Machine Learning szolgáltatás munkaterületének kiválasztásával](./media/monitor-azure-machine-learning/metrics.png)

A hivatkozásokat a [Azure monitor által támogatott összes erőforrás-metrika](../azure-monitor/platform/metrics-supported.md)listáját láthatja.

> [!TIP]
> Azure Monitor metrikák adatai 90 napig érhetők el. A diagramok létrehozásakor azonban csak 30 napig lehet vizualizációt készíteni. Ha például egy 90 napos időszakot szeretne megjeleníteni, azt három, a 90 napos időszakon belül 30 napos diagramra kell bontania.
### <a name="filtering-and-splitting"></a>Szűrés és felosztás

A dimenziókat támogató metrikák esetében a dimenzió érték használatával szűrőket alkalmazhat. Például az **aktív magok** szűrése a **fürt nevénél** `cpu-cluster` . 

A mérőszámokat dimenzió alapján is feloszthatja, hogy megjelenítse, hogy a metrika különböző szakaszai hogyan hasonlítanak össze egymással. Például feloszthatja a folyamat **lépésének típusát** , hogy megtekintse a folyamat során használt lépések számát.

További információ a szűrésről és a felosztásról: [Azure monitor speciális szolgáltatásai](../azure-monitor/platform/metrics-charts.md).

<a id="analyzing-log-data"></a>
## <a name="analyzing-logs"></a>Naplók elemzése

A Azure Monitor Log Analytics használatával diagnosztikai konfigurációt kell létrehoznia, és engedélyeznie __kell az adatok küldését a log Analyticsnak__. További információ: [gyűjtemény és útválasztás](#collection-and-routing) szakasz.

Azure Monitor naplókban lévő, az egyes táblákban található, egyedi tulajdonságokkal rendelkező táblázatokban tárolt adathalmazok. A Azure Machine Learning az alábbi táblázatokban tárolja az adattárolást:

| Táblázat | Leírás |
|:---|:---|
| AmlComputeClusterEvent | Azure Machine Learning számítási fürtök eseményei. |
| AmlComputeClusterNodeEvent | Azure Machine Learning számítási fürt csomópontjain belüli események. |
| AmlComputeJobEvent | Azure Machine Learning számításon futó feladatok eseményei. |

> [!IMPORTANT]
> Amikor kijelöli a **naplók** elemet a Azure Machine learning menüben, a rendszer megnyit egy log Analytics a jelenlegi munkaterületre beállított lekérdezési hatókörrel. Ez azt jelenti, hogy a naplók lekérdezése csak az adott erőforrás adatait fogja tartalmazni. Ha olyan lekérdezést szeretne futtatni, amely más adatbázisokból vagy más Azure-szolgáltatásoktól származó adatokból származó adatokkal is rendelkezik, válassza a **naplók** lehetőséget a **Azure monitor** menüből. Részletekért lásd: [a naplózási lekérdezés hatóköre és időbeli tartománya Azure Monitor log Analytics](../azure-monitor/log-query/scope.md) .

A naplók és a metrikák részletes ismertetését lásd: [Azure Machine learning monitorozási adatok referenciája](monitor-resource-reference.md).

### <a name="sample-kusto-queries"></a>Példa Kusto-lekérdezésekre

> [!IMPORTANT]
> Amikor kiválasztja a **naplók** elemet a [szolgáltatásnév] menüben, log Analytics megnyílik a lekérdezés hatóköre beállítással az aktuális Azure Machine learning munkaterületre. Ez azt jelenti, hogy a naplók lekérdezése csak az adott erőforrás adatait fogja tartalmazni. Ha olyan lekérdezést szeretne futtatni, amely más munkaterületekről vagy más Azure-szolgáltatásokból származó adatokból származó adatokkal is rendelkezik, válassza a **naplók** lehetőséget a **Azure monitor** menüből. Részletekért lásd: [a naplózási lekérdezés hatóköre és időbeli tartománya Azure Monitor log Analytics](../azure-monitor/log-query/scope.md) .

A következő lekérdezések segítségével figyelheti Azure Machine Learning erőforrásait: 

+ Sikertelen feladatok beolvasása az elmúlt öt napban:

    ```Kusto
    AmlComputeJobEvent
    | where TimeGenerated > ago(5d) and EventType == "JobFailed"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ Rekordok beolvasása egy adott feladattípushoz:

    ```Kusto
    AmlComputeJobEvent
    | where JobName == "automl_a9940991-dedb-4262-9763-2fd08b79d8fb_setup"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ A fürt eseményeinek beolvasása az elmúlt öt napban azon fürtök esetében, amelyeknél a virtuális gép mérete Standard_D1_V2:

    ```Kusto
    AmlComputeClusterEvent
    | where TimeGenerated > ago(4d) and VmSize == "STANDARD_D1_V2"
    | project  ClusterName , InitialNodeCount , MaximumNodeCount , QuotaAllocated , QuotaUtilized
    ```

+ Az elmúlt nyolc napban lefoglalt csomópontok beolvasása:

    ```Kusto
    AmlComputeClusterNodeEvent
    | where TimeGenerated > ago(8d) and NodeAllocationTime  > ago(8d)
    | distinct NodeId
    ```

## <a name="alerts"></a>Riasztások

Azure Machine Learning riasztásait a **Azure monitor** menüből származó **riasztások** megnyitásával érheti el. A riasztások létrehozásával kapcsolatos részletekért tekintse meg a [metrikus riasztások létrehozása, megtekintése és kezelése Azure monitor használatával](../azure-monitor/platform/alerts-metric.md) című témakört.

A következő táblázat a Azure Machine Learning vonatkozó gyakori és javasolt metrikai szabályokat sorolja fel:

| Riasztástípus | Feltétel | Description |
|:---|:---|:---|
| Modell-üzembehelyezés sikertelen | Összesítés típusa: Total, operátor: nagyobb, mint, küszöbérték: 0 | Ha egy vagy több modell telepítése meghiúsult |
| Kvóta kihasználtsága (%) | Összesítés típusa: átlag, operátor: nagyobb, mint, küszöbérték: 90| Ha a kvóta kihasználtsági aránya nagyobb, mint 90% |
| Használhatatlan csomópontok | Összesítés típusa: Total, operátor: nagyobb, mint, küszöbérték: 0 | Ha egy vagy több használhatatlan csomópont van |

## <a name="next-steps"></a>Következő lépések

- A naplók és a metrikák ismertetését lásd: [Azure Machine learning adathivatkozás figyelése](monitor-resource-reference.md).
- További információ a Azure Machine Learninghoz kapcsolódó kvóták használatáról: az [Azure-erőforrások kezelése és kvóták igénylése](how-to-manage-quotas.md).
- Az Azure-erőforrások monitorozásával kapcsolatos további információkért lásd: [Azure-erőforrások figyelése Azure monitorokkal](../azure-monitor/insights/monitor-azure-resource.md).
