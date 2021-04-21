---
title: Monitorozási Azure Machine Learning | Microsoft Docs
description: Megtudhatja, hogyan használhatja Azure Monitor a metrikákra vonatkozó riasztások megtekintésére, elemzésére és Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.custom: subject-monitoring
ms.date: 10/01/2020
ms.openlocfilehash: e5fd0fdd5a6f9a4a7537a844b096efdfef253638
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816853"
---
# <a name="monitor-azure-machine-learning"></a>Az Azure Machine Learning monitorozása

Ha kritikus fontosságú alkalmazásokkal és üzleti folyamatokkal rendelkezik, amelyek Azure-erőforrásokra támaszkodnak, monitorozással szeretné figyelni az erőforrások rendelkezésre állását, teljesítményét és működését. Ez a cikk bemutatja a Azure Machine Learning által létrehozott monitorozási adatokat, valamint az adatok elemzését és riasztását a Azure Monitor.

> [!TIP]
> A jelen dokumentumban található információk elsősorban rendszergazdák számára készültek, mivel a szolgáltatás és a társított Azure Azure Machine Learning service monitorozását ismerteti. Ha Ön __adattudós__ vagy __fejlesztő,__ és figyelni szeretné a modellbeképzési futtatásokkal kapcsolatos *információkat,* tekintse meg a következő dokumentumokat:
>
> * [Betanítás futtatásának elkezdése, figyelése és megszakítása](how-to-track-monitor-analyze-runs.md)
> * [A betanítási futtatások mérőszámainak naplózása](how-to-log-view-metrics.md)
> * [Kísérletek nyomon követése az MLflow használatával](how-to-use-mlflow.md)
> * [Futtatások megjelenítése a TensorBoard használatával](how-to-monitor-tensorboard.md)
>
> Ha monitor szeretné figyelni a webszolgáltatásként vagy modulként üzembe helyezett modellek által létrehozott IoT Edge, tekintse meg a [Modelladatok](how-to-enable-data-collection.md) gyűjtése és a [Monitor](how-to-enable-app-insights.md)az Application Insights.

## <a name="what-is-azure-monitor"></a>Mi az Azure Monitor?

Azure Machine Learning adatokat hoz létre a [Azure Monitor](../azure-monitor/overview.md)használatával, amely egy teljes körű monitorozási szolgáltatás az Azure-ban. Azure Monitor az Azure-erőforrások figyelése érdekében elérhető funkciók teljes készletét biztosítja. Más felhőkben és a helyszínen található erőforrásokat is képes figyelni.

Kezdje az [Azure-erőforrások monitorozása a Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md)használatával cikkel, amely a következő fogalmakat ismerteti:

- Mi az Azure Monitor?
- A monitorozással kapcsolatos költségek
- Az Azure-ban gyűjtött monitorozási adatok
- Adatgyűjtés konfigurálása
- Standard eszközök az Azure-ban az adatok monitorozásának elemzéséhez és riasztáshoz

A következő szakaszok erre a cikkre épülnek, és ismertetik az egyes Azure Machine Learning. Ezek a szakaszok példákat is tartalmaznak az adatgyűjtés konfigurálásához és az adatok Azure-eszközökkel való elemzéséhez.

> [!TIP]
> A projekthez kapcsolódó költségek Azure Monitor lásd: [Használat és becsült költségek.](../azure-monitor//usage-estimated-costs.md) Ha meg kell értenie, hogy az adatok milyen idő alatt jelennek meg a Azure Monitor: Naplóadatok [adatbejüklesztés ideje.](../azure-monitor/logs/data-ingestion-time.md)

## <a name="monitoring-data-from-azure-machine-learning"></a>Adatok monitorozása Azure Machine Learning

Azure Machine Learning gyűjti a monitorozási adatokat, mint a többi Azure-erőforrást, amelyek az Adatok monitorozása [Azure-erőforrásokból.](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data) 

A [Azure Machine Learning által](monitor-resource-reference.md) létrehozott naplók és metrikák részletes referencia-Azure Machine Learning.

<a id="configuration"></a>

## <a name="collection-and-routing"></a>Gyűjtés és útválasztás

A rendszer gyűjti és tárolja a platformmetrikákat és a tevékenységnaplót, de diagnosztikai beállítással más helyekre is irányítható.  

A rendszer addig nem gyűjti és tárolja az erőforrásnaplókat, amíg nem hoz létre diagnosztikai beállítást, és nem irányít át egy vagy több helyre.

A diagnosztikai beállítások a parancssori felület, a parancssori felület vagy Azure Portal a PowerShell használatával való létrehozásának részletes folyamatát lásd: Create diagnostic [setting to collect platform logs and metrics in Azure](../azure-monitor/essentials/diagnostic-settings.md) (Diagnosztikai beállítás létrehozása platformnaplók és metrikák gyűjtéséhez az Azure-ban). Diagnosztikai beállítás létrehozásakor meg kell adnia, hogy a naplók mely kategóriáit gyűjtse össze. A figyelési adatok Azure Machine Learning a következő Azure Machine Learning [vannak felsorolva:](monitor-resource-reference.md#resource-logs).

> [!IMPORTANT]
> Ezeknek a beállításoknak az engedélyezéséhez további Azure-szolgáltatásokra van szükség (tárfiók, eseményközpont vagy Log Analytics), ami növelheti a költségeket. A becsült költségek kiszámításához keresse fel az [Azure díjkalkulátorát.](https://azure.microsoft.com/pricing/calculator)

A következő naplókat konfigurálhatja a Azure Machine Learning:

| Kategória | Leírás |
|:---|:---|
| AmlComputeClusterEvent | A számítási Azure Machine Learning események. |
| AmlComputeClusterNodeEvent | A számítási fürtön belüli csomópontok Azure Machine Learning események. |
| AmlComputeJobEvent | A számítási feladaton futó Azure Machine Learning események. |

> [!NOTE]
> Ha engedélyezi a metrikákat egy diagnosztikai beállításban, a dimenzióinformációk jelenleg nem szerepelnek a tárfióknak, eseményközpontnak vagy naplóelemzésnek küldött információk között.

A gyűjthető metrikákat és naplókat a következő szakaszok ismertetik.

## <a name="analyzing-metrics"></a>Metrikák elemzése

A metrikák elemzéséhez Azure Machine Learning Azure-szolgáltatásokból származó metrikákat  is, ha megnyitja a **Metrikákat** a Azure Monitor menüből. Az [eszköz használatával kapcsolatos Metrikaböngésző](../azure-monitor/essentials/metrics-getting-started.md) az Azure Metrikaböngésző ismerkedés.

Az összegyűjtött platformmetrikák listájáért lásd: Monitoring Azure Machine Learning data reference metrics (Az adatok Azure Machine Learning [metrikák figyelése).](monitor-resource-reference.md#metrics)

A szolgáltatás-Azure Machine Learning összes metrika a Szolgáltatás-munkaterület **névterében Machine Learning található.**

![Metrikaböngésző a Machine Learning szolgáltatás-munkaterülettel](./media/monitor-azure-machine-learning/metrics.png)

Referenciaként a következőben támogatott összes [erőforrás-metrikát](../azure-monitor/essentials/metrics-supported.md)Azure Monitor.

> [!TIP]
> Azure Monitor metrikaadatok 90 napig érhetők el. Diagramok létrehozásakor azonban csak 30 nap vizualizálható. Ha például egy 90 napos időszakot szeretne vizualizálni, három, 30 napos diagramra kell lebontani a 90 napos időszakon belül.
### <a name="filtering-and-splitting"></a>Szűrés és felosztás

A dimenziókat támogató metrikákhoz dimenzióértékek használatával alkalmazhat szűrőket. Például szűrheti az **aktív magokat** a fürt **nevére.** `cpu-cluster` 

A metrikákat dimenziók szerint is feloszthatja, így vizualizálhatja, hogy a metrika különböző szegmensei hogyan viszonyulnak egymáshoz. Például a folyamat **lépéstípusának** felosztásával láthatja a folyamatban használt lépések számát.

További információ a szűrésről és a felosztásról: [A](../azure-monitor/essentials/metrics-charts.md)Azure Monitor.

<a id="analyzing-log-data"></a>
## <a name="analyzing-logs"></a>Naplók elemzése

A Azure Monitor Log Analytics használatához diagnosztikai konfigurációt kell létrehoznia, és engedélyeznie kell az Adatok küldése a __Log Analyticsnek beállítást.__ További információkért lásd a Gyűjtemény [és útválasztás szakaszt.](#collection-and-routing)

A Azure Monitor naplókban lévő adatok táblákban vannak tárolva, és mindegyik tábla saját egyedi tulajdonságokkal rendelkezik. Azure Machine Learning a következő táblákban tárolja az adatokat:

| Táblázat | Leírás |
|:---|:---|
| AmlComputeClusterEvent | A számítási Azure Machine Learning események. |
| AmlComputeClusterNodeEvent | A számítási fürtön belüli csomópontok Azure Machine Learning események. |
| AmlComputeJobEvent | A számítási feladaton futó Azure Machine Learning események. |

> [!IMPORTANT]
> Amikor a **Naplók lehetőséget** választja a Azure Machine Learning menüben, a Log Analytics megnyílik, és a lekérdezés hatóköre az aktuális munkaterületre van beállítva. Ez azt jelenti, hogy a naplólekérdezések csak az adott erőforrásból származó adatokat fogják tartalmazni. Ha olyan lekérdezést szeretne futtatni, amely más adatbázisokból vagy más  Azure-szolgáltatásokból származó adatokat tartalmaz, válassza a Naplók lehetőséget a **Azure Monitor** menüben. A részleteket a Log Analytics Azure Monitor [log query scope and time range (Naplólekérdezés](../azure-monitor/logs/scope.md) hatóköre és időtartománya) oldalon talál.

A naplók és metrikák részletes referenciáját a [monitorozási adatok Azure Machine Learning tekintse meg.](monitor-resource-reference.md)

### <a name="sample-kusto-queries"></a>Minta Kusto-lekérdezések

> [!IMPORTANT]
> Amikor **kiválasztja** a Naplók elemet a [szolgáltatásnév] menüben, a Log Analytics úgy nyílik meg, hogy a lekérdezési hatókör az aktuális Azure Machine Learning van beállítva. Ez azt jelenti, hogy a naplólekérdezések csak az adott erőforrásból származó adatokat fogják tartalmazni. Ha olyan lekérdezést szeretne futtatni, amely más munkaterületek adatait vagy  más Azure-szolgáltatásokból származó adatokat tartalmaz, válassza a Naplók lehetőséget a **Azure Monitor** menüben. A részleteket a Log Analytics Azure Monitor [log query scope and time range (Naplólekérdezés](../azure-monitor/logs/scope.md) hatóköre és időtartománya) oldalon talál.

Az alábbi lekérdezések segítségével figyelheti a Azure Machine Learning erőforrásait: 

+ Sikertelen feladatok lekért száma az elmúlt öt napban:

    ```Kusto
    AmlComputeJobEvent
    | where TimeGenerated > ago(5d) and EventType == "JobFailed"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ Egy adott feladatnév rekordjainak lekért neve:

    ```Kusto
    AmlComputeJobEvent
    | where JobName == "automl_a9940991-dedb-4262-9763-2fd08b79d8fb_setup"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ Az elmúlt öt nap fürteseményei lekérte az olyan fürtökét, amelyeken a virtuális gép mérete Standard_D1_V2:

    ```Kusto
    AmlComputeClusterEvent
    | where TimeGenerated > ago(4d) and VmSize == "STANDARD_D1_V2"
    | project  ClusterName , InitialNodeCount , MaximumNodeCount , QuotaAllocated , QuotaUtilized
    ```

+ Az elmúlt nyolc napban lefoglalt csomópontok lekért száma:

    ```Kusto
    AmlComputeClusterNodeEvent
    | where TimeGenerated > ago(8d) and NodeAllocationTime  > ago(8d)
    | distinct NodeId
    ```

## <a name="alerts"></a>Riasztások

A riasztásokat a Azure Machine Learning a **riasztások megnyitásával** Azure Monitor **menüből.** A [riasztások létrehozásával kapcsolatos](../azure-monitor/alerts/alerts-metric.md) részletekért lásd: Metrikariasztás létrehozása, megtekintése és kezelése Azure Monitor használatával.

A következő táblázat a riasztási szabályok gyakori és ajánlott metrikákra vonatkozó riasztási Azure Machine Learning:

| Riasztástípus | Feltétel | Description |
|:---|:---|:---|
| A modell üzembe helyezése sikertelen | Összesítés típusa: Total, Operator: Greater than, Threshold value: 0 | Ha egy vagy több modell üzembe helyezése meghiúsult |
| Kvóta kihasználtságának százalékos aránya | Összesítés típusa: Átlag, Operátor: Nagyobb, mint, Küszöbérték: 90| Ha a kvóta kihasználtsága meghaladja a 90%-ot |
| Nem használható csomópontok | Összesítés típusa: Total, Operator: Greater than, Threshold value: 0 | Ha egy vagy több nem használható csomópont van |

## <a name="next-steps"></a>Következő lépések

- A naplókra és metrikákra vonatkozó referenciáért lásd: Monitoring Azure Machine Learning data reference (Az Azure Machine Learning [adatokra vonatkozó referencia).](monitor-resource-reference.md)
- Az azure-erőforrásokhoz kapcsolódó kvóták kezelésével kapcsolatos Azure Machine Learning lásd: Azure-erőforrások [kvótáinak kezelése és kérése.](how-to-manage-quotas.md)
- Az Azure-erőforrások monitorozásával kapcsolatos részletekért lásd: [Azure-erőforrások monitorozása a Azure Monitor.](../azure-monitor/essentials/monitor-azure-resource.md)
