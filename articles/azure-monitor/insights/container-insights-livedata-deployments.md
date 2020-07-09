---
title: Tárolók üzembe helyezésének Azure Monitor megtekintése (előzetes verzió) | Microsoft Docs
description: Ez a cikk a Kubernetes-telepítések valós idejű nézetét írja le anélkül, hogy a kubectl-t használja a tárolók Azure Monitor.
ms.topic: conceptual
ms.date: 10/15/2019
ms.custom: references_regions
ms.openlocfilehash: 2f1eac82ce67818c7bf86ce3ca8924155d8ee2aa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85337995"
---
# <a name="how-to-view-deployments-preview-in-real-time"></a>Központi telepítések (előzetes verzió) valós idejű megtekintése

A Azure Monitor for containers (előzetes verzió) szolgáltatásban a központi telepítések (előzetes verzió) funkció valós időben közvetlen hozzáférést emulál a Kubernetes üzembe helyezési objektumaihoz a `kubeclt get deployments` és a `kubectl describe deployment {your deployment}` parancsokkal.

>[!NOTE]
>Ez a funkció nem támogatja a [privát fürtökként](https://azure.microsoft.com/updates/aks-private-cluster/) engedélyezett AK-fürtöket. Ez a funkció arra támaszkodik, hogy közvetlenül a böngészőből egy proxykiszolgálón keresztül éri el a Kubernetes API-t. A hálózati biztonság engedélyezésével letilthatja a Kubernetes API-t ebből a proxyból, és letiltja a forgalmat.

További tudnivalókért tekintse át a [központi telepítések](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)Kubernetes dokumentációját.

## <a name="how-it-works"></a>Működés

Az élő adatok (előzetes verzió) funkció közvetlenül a Kubernetes API-hoz érhető el, és a hitelesítési modellel kapcsolatos további információk [itt](https://kubernetes.io/docs/concepts/overview/kubernetes-api/)találhatók.

A központi telepítések (előzetes verzió) funkció egy egyszeri (frissíthető) terhelést hajt végre a központi telepítések végpontján `/apis/apps/v1/deployments` . Lehetővé teszi egy adott központi telepítés kiválasztását, és betölti az adott központi telepítési végpontra vonatkozó leírás részleteit `/apis/apps/v1/namespaces/${nameSpace}/deployments/${deploymentName}` .

Ha a lap bal felső részén a **frissítés** lehetőséget választja, a rendszer frissíti a központi telepítési listát. Ez szimulálja a parancs újbóli futtatását `kubectl` .

>[!IMPORTANT]
>A szolgáltatás működése során a rendszer nem tárolja véglegesen az adattárolást. A rendszer a munkamenet során rögzített összes információt törli a böngésző bezárásakor, vagy innen navigál.

>[!NOTE]
>Az élő adatok (előzetes verzió) adatait nem rögzítheti a konzolról egy Azure-irányítópultra.

## <a name="deployments-describe"></a>Központi telepítések leírása

Az alábbi lépéseket követve tekintheti meg a központi telepítés részletes leírását, amely egyenértékű a `kubectl describe deployment` következő lépésekkel:.

1. A Azure Portal tallózással keresse meg az AK fürterőforrás-csoportot, és válassza ki az AK-erőforrást.

2. Az AK-fürt irányítópultjának bal oldali **figyelés** területén válassza az **eredmények elemet.**

3. Válassza ki a **központi telepítések (előzetes verzió)** lapot.

    ![Központi telepítések nézet a Azure Portal](./media/container-insights-livedata-deployments/deployment-view.png)

A nézet megjeleníti az összes futó üzemelő példány listáját, valamint a névteret és egyéb részletes információkat, a parancs végrehajtásának emulálása mellett `kubectl get deployments –all-namespaces` . Az eredményeket az oszlopok valamelyikének kiválasztásával rendezheti.

![Központi telepítések tulajdonságai ablaktábla részletei](./media/container-insights-livedata-deployments/deployment-properties-pane-details.png)

Amikor kijelöl egy központi telepítést a listából, a rendszer automatikusan megjeleníti a tulajdonságok panelt a lap jobb oldalán. A kiválasztott központi telepítéssel kapcsolatos információkat jeleníti meg, amelyeket a parancs futtatásakor megtekint `kubectl describe deployment {deploymentName}` . Előfordulhat, hogy észrevette, hogy az információ leírása hiányzik néhány részletről. A **sablon** többnyire hiányzik. A **RAW** lapon való kiválasztással megnyithatja a nem elemzett Leírás részleteit.

![Központi telepítések tulajdonságai ablaktábla nyers részletei](./media/container-insights-livedata-deployments/deployment-properties-pane-raw.png)

Az üzembe helyezés részleteinek áttekintése közben valós időben láthatja a tároló naplóit és eseményeit. Jelölje be az **élő konzol megjelenítése** és az élő adat(előnézet) konzol ablaktábla az üzembe helyezések adatrács alatt jelenik meg, ahol a folyamatos adatfolyamban megtekintheti az élő naplókat. Ha a beolvasás állapotjelzője egy zöld pipa jelenik meg, amely a panel jobb szélén található, az azt jelenti, hogy a rendszer lekéri az adatait, és megkezdi a folyamatos átvitelt a konzolra.

Azt is megteheti, hogy a névtér vagy a fürt szintjén események alapján végzi a szűrést. Ha többet szeretne megtudni a konzolon tárolt adatmegjelenítésről, tekintse meg az [élő adatszolgáltatások (előzetes verzió) megtekintését a tárolók Azure monitor](container-insights-livedata-overview.md).

![Az üzemelő példányok élő adatmegjelenítést mutatnak a konzolon](./media/container-insights-livedata-deployments/deployments-console-view-events.png)

## <a name="next-steps"></a>További lépések

- A Azure Monitor használatának megismeréséhez és az AK-fürt egyéb szempontjainak figyeléséhez lásd: az [Azure Kubernetes szolgáltatás állapotának megtekintése](container-insights-analyze.md).

- Megtekintheti a [napló lekérdezési példáit](container-insights-log-search.md#search-logs-to-analyze-data) , amelyekkel előre definiált lekérdezéseket és példákat tekinthet meg a riasztások, a vizualizációk vagy a fürtök további elemzéséhez.
