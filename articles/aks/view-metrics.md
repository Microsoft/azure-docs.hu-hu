---
title: Az Azure Kubernetes szolgáltatás (ak) fürt metrikáinak megtekintése
description: Az Azure Kubernetes Service (ak) fürt metrikáinak megtekintése.
services: container-service
ms.topic: article
ms.date: 03/30/2021
ms.openlocfilehash: 32d8745e0ea780b5f86588fabdc25b244cf2cd8e
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105969326"
---
# <a name="view-cluster-metrics-for-azure-kubernetes-service-aks"></a>Az Azure Kubernetes szolgáltatás (ak) fürt metrikáinak megtekintése

Az AK mérőszámok készletét biztosítja a vezérlési síkon, beleértve az API-kiszolgálót, a fürt autoskálázását és a fürtcsomópontok számát. Ezek a metrikák lehetővé teszik a fürt állapotának figyelését és a problémák elhárítását. A fürt metrikáit a Azure Portal használatával tekintheti meg.

> [!NOTE]
> Ezek az AK-fürt metrikái átfedésben vannak a [Kubernetes által biztosított mérőszámok][kubernetes-metrics]egy részhalmazával.

## <a name="view-metrics-for-your-aks-cluster-using-the-azure-portal"></a>Az AK-fürt metrikáinak megtekintése a Azure Portal használatával

Az AK-fürt metrikáinak megtekintése:

1. Jelentkezzen be a [Azure Portalba][azure-portal] , és navigáljon az AK-fürthöz.
1. A *figyelés* területen a bal oldalon válassza a *metrikák* lehetőséget.
1. Hozzon létre egy diagramot a megtekinteni kívánt mérőszámokhoz. Például hozzon létre egy diagramot:
    1. A *hatókör* területen válassza ki a fürtöt.
    1. A *metrikai névtér* esetében válassza a *Container Service (felügyelt) standard mérőszámok* lehetőséget.
    1. A *metrika* *alatt válassza a* *hüvelyek száma fázis* szerint lehetőséget.
    1. Az *összesítéshez* válassza az *AVG* elemet.

:::image type="content" source="media/metrics/metrics-chart.png" alt-text="{alt-text}":::

A fenti példa a *myAKSCluster* tartozó hüvelyek átlagos számának mérőszámait jeleníti meg.

## <a name="available-metrics"></a>Rendelkezésre álló metrikák

A következő fürt metrikái érhetők el:

| Name | Group | ID (Azonosító) | Leírás |
| --- | --- | --- | ---- |
| Fedélzeti kérelmek | API-kiszolgáló (előzetes verzió) |apiserver_current_inflight_requests | A jelenleg aktív fedélzeti kérelmek maximális száma az API-kiszolgálón, a kérelem típusa szerint. |
| Fürt állapota | Fürt autoskálázása (előzetes verzió) | cluster_autoscaler_cluster_safe_to_autoscale | Meghatározza, hogy a fürt autoskálázása végrehajtja-e a műveletet a fürtön. |
| Leskálázási hűtő | Fürt autoskálázása (előzetes verzió) | cluster_autoscaler_scale_down_in_cooldown | Meghatározza, hogy a leskálázás van-e a hűtőben – ebben az időkeretben egyetlen csomópont sem lesz eltávolítva. |
| Szükségtelen csomópontok | Fürt autoskálázása (előzetes verzió) | cluster_autoscaler_unneeded_nodes_count | A fürt auotscaler a csomópontokat törlésre jelöltként jelöli meg, és végül törli őket. |
| Unschedulable hüvelyek | Fürt autoskálázása (előzetes verzió) | cluster_autoscaler_unschedulable_pods_count | Azon hüvelyek száma, amelyek jelenleg unschedulable a fürtben. |
| A felügyelt fürtben rendelkezésre álló CPU-magok teljes száma | Csomópontok | kube_node_status_allocatable_cpu_cores | A felügyelt fürtben rendelkezésre álló CPU-magok teljes száma. |
| A felügyelt fürtben rendelkezésre álló memória teljes mennyisége | Csomópontok | kube_node_status_allocatable_memory_bytes | A felügyelt fürtben rendelkezésre álló memória teljes mennyisége. |
| Különböző csomóponti feltételek állapota | Csomópontok | kube_node_status_condition | Különböző csomóponti feltételek állapota |
| CPU-használat Millicores | Csomópontok (előzetes verzió) | node_cpu_usage_millicores | A CPU-kihasználtság összesített mérése a millicores-ben a fürtön keresztül. |
| CPU-használat százaléka | Csomópontok (előzetes verzió) | node_cpu_usage_percentage | Összesített átlagos CPU-kihasználtság a fürtben mért százalékban kifejezve. |
| Memória RSS-bájtjai | Csomópontok (előzetes verzió) | node_memory_rss_bytes | A tároló RSS-memóriája bájtban használatos. |
| Memória RSS-százaléka | Csomópontok (előzetes verzió) | node_memory_rss_percentage | A tárolóban használt RSS-memória százalékban megadva. |
| Memória munkakészletének bájtjai | Csomópontok (előzetes verzió) | node_memory_working_set_bytes | A tároló munkakészletének memóriában használt mennyisége. |
| Memória munkakészletének százalékos aránya | Csomópontok (előzetes verzió) | node_memory_working_set_percentage | A tároló munkakészletének%-ban használt memóriája. |
| Felhasznált lemez bájtjai | Csomópontok (előzetes verzió) | node_disk_usage_bytes | Az eszköz által bájtban használt lemezterület. |
| Felhasznált lemez százaléka | Csomópontok (előzetes verzió) | node_disk_usage_percentage | Az eszköz által százalékban használt lemezterület. |
| Hálózati bájtban | Csomópontok (előzetes verzió) | node_network_in_bytes | Hálózati fogadott bájtok száma. |
| Hálózati kimenő bájtok | Csomópontok (előzetes verzió) | node_network_out_bytes | Hálózati továbbított bájtok. |
| A hüvelyek száma üzemkész állapotban | Hüvely | kube_pod_status_ready | A hüvelyek száma *üzemkész* állapotban. |
| Hüvelyek száma fázis szerint | Hüvely | kube_pod_status_phase | Hüvelyek száma fázis szerint |

> [!IMPORTANT]
> Az előzetes verzióban elérhető metrikák frissíthetők és módosíthatók, beleértve a nevüket és a leírásokat is.

## <a name="next-steps"></a>Következő lépések

Az AK-hoz tartozó fürt metrikái mellett a Azure Monitort is használhatja az AK-fürthöz. További információ a Azure Monitor AK-val való használatáról: [Azure monitor for containers][aks-azure-monitory].

[aks-azure-monitory]: ../azure-monitor/containers/container-insights-overview.md
[azure-portal]: https://portal.azure.com/
[kubernetes-metrics]: https://kubernetes.io/docs/concepts/cluster-administration/system-metrics/