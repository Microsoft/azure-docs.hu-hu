---
title: Az Azure Kubernetes szolgáltatás (ak) vezérlő naplófájljainak megtekintése
description: Megtudhatja, hogyan engedélyezheti és tekintheti meg a Kubernetes fő csomópontjának naplóit az Azure Kubernetes szolgáltatásban (ak)
services: container-service
ms.topic: article
ms.date: 10/14/2020
ms.openlocfilehash: 1089cb4ea52efaa545478ced053a921728a894ef
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92368451"
---
# <a name="enable-and-review-kubernetes-master-node-logs-in-azure-kubernetes-service-aks"></a>A Kubernetes főcsomópont-naplóinak engedélyezése és áttekintése az Azure Kubernetes Service-ben (AKS)

Az Azure Kubernetes szolgáltatással (ak) a fő összetevők, például a *Kube-apiserver* és a *Kube-Controller-Manager* felügyelt szolgáltatásként vannak megadva. Létrehozhatja és kezelheti a *kubelet* és a tároló futtatókörnyezetét futtató csomópontokat, és az alkalmazásokat a felügyelt Kubernetes API-kiszolgálón keresztül helyezheti üzembe. Az alkalmazások és szolgáltatások hibakereséséhez előfordulhat, hogy meg kell tekintenie a fő összetevők által létrehozott naplókat. Ez a cikk bemutatja, hogyan engedélyezheti és kérdezheti le a naplókat a Kubernetes fő összetevőiről Azure Monitor naplók használatával.

## <a name="before-you-begin"></a>Előkészületek

Ehhez a cikkhez egy Azure-fiókban futó meglévő AK-fürtre van szükség. Ha még nem rendelkezik AK-fürttel, hozzon létre egyet az [Azure CLI][cli-quickstart] vagy a [Azure Portal][portal-quickstart]használatával. Azure Monitor naplók a RBAC és a nem RBAC-kompatibilis AK-fürtökkel is működnek.

## <a name="enable-resource-logs"></a>Erőforrásnaplók engedélyezése

A több forrásból származó adatok gyűjtésének és áttekintésének megkönnyítéséhez Azure Monitor-naplók egy lekérdezési nyelvet és elemzési motort biztosítanak, amely elemzéseket biztosít a környezet számára. A munkaterületek az adatválogatásra és az adatelemzésre használhatók, és integrálható más Azure-szolgáltatásokkal, például Application Insightsekkel és Security Centerokkal. Ha egy másik platformot szeretne használni a naplók elemzéséhez, dönthet úgy, hogy erőforrás-naplókat küld egy Azure Storage-fiókba vagy az Event hub-ba. További információ: [Mi az a Azure monitor logs?][log-analytics-overview].

Azure Monitor naplók engedélyezve vannak és kezelhetők a Azure Portal. Ha az AK-fürt Kubernetes fő összetevőinek naplózási gyűjteményét szeretné engedélyezni, nyissa meg a Azure Portal egy böngészőben, és végezze el a következő lépéseket:

1. Válassza ki az AK-fürthöz tartozó erőforráscsoportot, például *myResourceGroup*. Ne válassza ki azt az erőforráscsoportot, amely az egyedi AK-fürterőforrás (például *MC_myResourceGroup_myAKSCluster_eastus*) tartalmazza.
1. A bal oldali oldalon válassza a **diagnosztikai beállítások**lehetőséget.
1. Válassza ki az AK-fürtöt, például *myAKSCluster*, majd válassza a **diagnosztikai beállítás hozzáadását**.
1. Adjon meg egy nevet, például *myAKSClusterLogs*, majd válassza ki a **log Analytics küldésére szolgáló**lehetőséget.
1. Válasszon ki egy meglévő munkaterületet, vagy hozzon létre egy újat. Ha létrehoz egy munkaterületet, adja meg a munkaterület nevét, egy erőforráscsoportot és egy helyet.
1. Az elérhető naplók listájában válassza ki az engedélyezni kívánt naplókat. Ebben a példában engedélyezze a *Kube-audit* és a *Kube-audit-admin* naplókat. A gyakori naplók közé tartozik a *Kube-apiserver*, a *Kube-Controller-Manager*és a *Kube-Scheduler*. Ha Log Analytics munkaterületek engedélyezve vannak, visszaállíthatja és módosíthatja az összegyűjtött naplókat.
1. Ha elkészült, válassza a **Mentés** lehetőséget a kiválasztott naplók gyűjtésének engedélyezéséhez.

## <a name="log-categories"></a>Naplók kategóriái

A Kubernetes által írt bejegyzések mellett a projekt naplóinak bejegyzései is az AK-ból származnak.

A naplók három kategóriába vannak rögzítve: *Kube*, Kube-audit *-Admin*és *Guard*.

- Az *Kube-naplózási* kategória minden naplózási eseményhez tartalmaz minden naplózási naplót, beleértve a *Get*, a *List*, a *create*, a *Update*, a *delete*, a *patch*és a *post bejegyzést*.
- A *Kube-audit-admin* kategória a *Kube* napló kategóriájának egy részhalmaza. *Kube – a naplózási rendszergazda* csökkenti a naplók számát a naplóból a *Get* és a *List* naplózási események kizárásával.
- Az *őr* kategóriája felügyelt Azure ad-és Azure RBAC-naplózási szolgáltatás. A felügyelt Azure AD: token in esetében a felhasználói adatok kijelentkezése. Azure RBAC esetén: hozzáférési felülvizsgálatok be-és kijelentkezés.

## <a name="schedule-a-test-pod-on-the-aks-cluster"></a>Tesztelési Pod-t ütemezhet az AK-fürtön

A naplók létrehozásához hozzon létre egy új Pod-t az AK-fürtben. Az alábbi példa YAML-jegyzék használatával hozhat létre alapszintű NGINX-példányt. Hozzon létre egy nevű fájlt `nginx.yaml` egy tetszőleges szerkesztőben, és illessze be az alábbi tartalmat:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
  - name: mypod
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    ports:
    - containerPort: 80
```

Hozza létre a pod-t a [kubectl Create][kubectl-create] paranccsal, és adja meg a YAML-fájlt az alábbi példában látható módon:

```
$ kubectl create -f nginx.yaml

pod/nginx created
```

## <a name="view-collected-logs"></a>Összegyűjtött naplók megtekintése

A diagnosztikai naplók engedélyezése és megjelenése akár 10 percet is igénybe vehet.

> [!NOTE]
> Ha az összes naplózási adatot megfelelőségi vagy egyéb célokra szüksége van, gyűjtsön és tároljon az olcsó tárolóban, például a blob Storage-ban. A *Kube-audit-admin* naplózási kategóriával összegyűjtheti és mentheti a naplózási és riasztási célokra szolgáló naplóbeli adatok jelentését.

A Azure Portal navigáljon az AK-fürthöz, és válassza a bal oldalon található **naplók** elemet. Ha megjelenik a *lekérdezési* ablak, akkor zárjuk be a példát.

A bal oldali oldalon válassza a **naplók**lehetőséget. A *Kube* naplófájlok megtekintéséhez írja be a következő lekérdezést a szövegmezőbe:

```
AzureDiagnostics
| where Category == "kube-audit"
| project log_s
```

Sok napló valószínűleg visszaadott. Ha le szeretné tekinteni a lekérdezés hatókörét az előző lépésben létrehozott NGINX Pod-naplók megtekintéséhez, adjon hozzá egy további *Where* utasítást az *Nginx* kereséséhez az alábbi példában látható módon:

```
AzureDiagnostics
| where Category == "kube-audit"
| where log_s contains "nginx"
| project log_s
```

Az *Kube-audit-admin* naplók megtekintéséhez írja be a következő lekérdezést a szövegmezőbe:

```
AzureDiagnostics
| where Category == "kube-audit-admin"
| project log_s
```

Ebben a példában a lekérdezés az összes létrehozási feladatot megjeleníti a *Kube-audit-adminban*. Valószínűleg sok eredményt adott vissza, a lekérdezés hatókörét az előző lépésben létrehozott NGINX Pod naplófájlok megtekintéséhez, adjon hozzá egy további *Where* utasítást az *Nginx* kereséséhez, ahogy az az alábbi példában is látható.

```
AzureDiagnostics
| where Category == "kube-audit-admin"
| where log_s contains "nginx"
| project log_s
```


A naplózási adatok lekérdezésével és szűrésével kapcsolatos további információkért lásd a [log Analytics-naplóbeli kereséssel gyűjtött adatok megtekintése és elemzése][analyze-log-analytics]című témakört.

## <a name="log-event-schema"></a>Napló esemény sémája

Az AK a következő eseményeket naplózza:

* [AzureActivity][log-schema-azureactivity]
* [AzureDiagnostics][log-schema-azurediagnostics]
* [AzureMetrics][log-schema-azuremetrics]
* [ContainerImageInventory][log-schema-containerimageinventory]
* [ContainerInventory][log-schema-containerinventory]
* [ContainerLog][log-schema-containerlog]
* [ContainerNodeInventory][log-schema-containernodeinventory]
* [ContainerServiceLog][log-schema-containerservicelog]
* [Szívverés][log-schema-heartbeat]
* [InsightsMetrics][log-schema-insightsmetrics]
* [KubeEvents][log-schema-kubeevents]
* [KubeHealth][log-schema-kubehealth]
* [KubeMonAgentEvents][log-schema-kubemonagentevents]
* [KubeNodeInventory][log-schema-kubenodeinventory]
* [KubePodInventory][log-schema-kubepodinventory]
* [KubeServices][log-schema-kubeservices]
* [Perf][log-schema-perf]

## <a name="log-roles"></a>Naplók szerepkörei

| Szerepkör                     | Leírás |
|--------------------------|-------------|
| *aksService*             | A vezérlő síkja művelethez tartozó megjelenített név a naplóban (a hcpService) |
| *MasterClient*           | A MasterClientCertificate naplójának megjelenített neve, az az AK Get-hitelesítő adatokkal rendelkező tanúsítvány |
| *nodeclient*             | Az ügynök csomópontjai által használt ClientCertificate megjelenített neve |

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan engedélyezheti és tekintheti át a Kubernetes fő összetevőinek naplóit az AK-fürtben. A további figyeléshez és a hibák megoldásához tekintse meg [a Kubelet-naplókat][kubelet-logs] , és [engedélyezze az SSH-csomópontok elérését][aks-ssh].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create

<!-- LINKS - internal -->
[cli-quickstart]: kubernetes-walkthrough.md
[portal-quickstart]: kubernetes-walkthrough-portal.md
[log-analytics-overview]: ../azure-monitor/log-query/log-query-overview.md
[analyze-log-analytics]: ../azure-monitor/log-query/get-started-portal.md
[kubelet-logs]: kubelet-logs.md
[aks-ssh]: ssh.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[log-schema-azureactivity]: /azure/azure-monitor/reference/tables/azureactivity
[log-schema-azurediagnostics]: /azure/azure-monitor/reference/tables/azurediagnostics
[log-schema-azuremetrics]: /azure/azure-monitor/reference/tables/azuremetrics
[log-schema-containerimageinventory]: /azure/azure-monitor/reference/tables/containerimageinventory
[log-schema-containerinventory]: /azure/azure-monitor/reference/tables/containerinventory
[log-schema-containerlog]: /azure/azure-monitor/reference/tables/containerlog
[log-schema-containernodeinventory]: /azure/azure-monitor/reference/tables/containernodeinventory
[log-schema-containerservicelog]: /azure/azure-monitor/reference/tables/containerservicelog
[log-schema-heartbeat]: /azure/azure-monitor/reference/tables/heartbeat
[log-schema-insightsmetrics]: /azure/azure-monitor/reference/tables/insightsmetrics
[log-schema-kubeevents]: /azure/azure-monitor/reference/tables/kubeevents
[log-schema-kubehealth]: /azure/azure-monitor/reference/tables/kubehealth
[log-schema-kubemonagentevents]: /azure/azure-monitor/reference/tables/kubemonagentevents
[log-schema-kubenodeinventory]: /azure/azure-monitor/reference/tables/kubenodeinventory
[log-schema-kubepodinventory]: /azure/azure-monitor/reference/tables/kubepodinventory
[log-schema-kubeservices]: /azure/azure-monitor/reference/tables/kubeservices
[log-schema-perf]: /azure/azure-monitor/reference/tables/perf
