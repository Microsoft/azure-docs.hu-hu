---
title: A Azure Kubernetes Service (AKS) vezérlő naplóinak megtekintése
description: Megtudhatja, hogyan engedélyezheti és megtekintheti a Kubernetes-vezérlősík naplóit a Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 01/27/2020
ms.openlocfilehash: 395422ca97b43488a7d7ad1c7434b20ccc59f2b0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767290"
---
# <a name="enable-and-review-kubernetes-control-plane-logs-in-azure-kubernetes-service-aks"></a>Kubernetes-vezérlősík naplóinak engedélyezése és áttekintése Azure Kubernetes Service (AKS)

A Azure Kubernetes Service (AKS) esetén a vezérlősík összetevői, például a *kube-apiserver* és a *kube-controller-manager* felügyelt szolgáltatásként biztosítanak. Létrehozhatja és kezelheti a *kubeletet* és a tároló-futáskörnyezetet használó csomópontokat, és üzembe helyezheti az alkalmazásokat a felügyelt Kubernetes API-kiszolgálón keresztül. Az alkalmazás és a szolgáltatások hibaelhárítása érdekében előfordulhat, hogy meg kell tekintenie a vezérlősík ezen összetevői által létrehozott naplókat. Ez a cikk bemutatja, hogyan engedélyezheti és Azure Monitor a naplókat a Kubernetes vezérlősík összetevőiből a naplók engedélyezéséhez és lekérdezéséhez.

## <a name="before-you-begin"></a>Előkészületek

Ehhez a cikkhez egy, az Azure-fiókjában futó meglévő AKS-fürtre van szükség. Ha még nem rendelkezik AKS-fürtgal, hozzon létre egyet az [Azure CLI][cli-quickstart] vagy a [Azure Portal.][portal-quickstart] Azure Monitor-naplók Kubernetes RBAC-val, Azure RBAC-val és nem RBAC-kompatibilis AKS-fürttel is működnek.

## <a name="enable-resource-logs"></a>Erőforrásnaplók engedélyezése

A több forrásból származó adatok gyűjtésének és áttekintésének segítéséhez a Azure Monitor-naplók lekérdezési nyelvet és elemzési motort nyújtanak, amely betekintést nyújt a környezetbe. A munkaterület az adatok rendezésére és elemzésére szolgál, és integrálható más Azure-szolgáltatásokkal, például a Application Insights és Security Center. Ha egy másik platformot használ a naplók elemzéséhez, dönthet úgy, hogy erőforrásnaplókat küld egy Azure-tárfiókba vagy eseményközpontba. További információ: [Mi az a Azure Monitor naplók?][log-analytics-overview].

Azure Monitor naplók engedélyezve vannak és kezelhetők a Azure Portal. Ha engedélyezni szeretné a naplógyűjtést az AKS-fürt kubernetes vezérlősík-összetevői számára, nyissa meg a Azure Portal böngészőben, és kövesse az alábbi lépéseket:

1. Válassza ki az AKS-fürt erőforráscsoportját, *például: myResourceGroup.* Ne válassza ki az AKS-fürt erőforrásait tartalmazó erőforráscsoportot, például a *MC_myResourceGroup_myAKSCluster_eastus.*

2. A bal oldalon válassza a Diagnosztikai **beállítások lehetőséget.**

3. Válassza ki az AKS-fürtöt,például *myAKSCluster,* majd válassza a **Diagnosztikai beállítás hozzáadása lehetőséget.**
  :::image type="content" source="media\view-control-plane-logs\select-add-diagnostic-setting.PNG" alt-text="Képernyőkép egy Azure Portal a diagnosztikai beállításokat megjelenítő böngészőablakban, amely azt jelzi, hogy a &quot;Diagnosztikai beállítás hozzáadása&quot; lehetőség legyen kiválasztva":::

4. Adjon meg egy nevet, például *myAKSClusterLogs,* majd válassza a **Küldés a Log Analytics-munkaterületre lehetőséget.**

5. Válasszon ki egy meglévő munkaterületet, vagy hozzon létre egy újat. Ha létrehoz egy munkaterületet, adja meg a munkaterület nevét, az erőforráscsoportot és a helyet.

6. Az elérhető naplók listájában válassza ki az engedélyezni kívánt naplókat. Ebben a példában engedélyezze a *kube-audit és* *a kube-audit-admin naplókat.* Gyakori naplók például a *kube-apiserver,* *a kube-controller-manager* és a *kube-scheduler.* Ha a Log Analytics-munkaterületek engedélyezve vannak, a gyűjtött naplókat vissza is használhatja és módosíthatja.

7. Ha elkészült, válassza a **Mentés lehetőséget** a kiválasztott naplók gyűjtésének engedélyezéséhez.
  :::image type="content" source="media\view-control-plane-logs\settings-selected.PNG" alt-text="Képernyőkép Azure Portal &quot;Diagnosztikai beállítás hozzáadása&quot; képernyőről. A &quot;Küldés a Log Analytics-munkaterületre&quot; célhely és a &quot;kube-audit&quot; és a &quot;kube-audit-admin&quot; naplók ki vannak jelölve":::

## <a name="log-categories"></a>Naplókategóriák

A Kubernetes által írt bejegyzések mellett a projekt auditnaplói is tartalmaznak bejegyzéseket az AKS-től.

Az auditnaplók három kategóriába sorolhatók: *kube-audit,* *kube-audit-admin* és *guard*.

- A *kube-audit* kategória minden naplózási esemény naplózási adatait tartalmazza, beleértve a *get*, *list*, *create,* *update,* *delete,* *patch* és *post adatokat.*
- A *kube-audit-admin* kategória a *kube-auditnapló kategóriájának egy* része. *A kube-audit-admin* jelentősen csökkenti a naplók számát azáltal, hogy kihagyja a *naplóból* a get és *list* naplózási eseményeket.
- Az *őr kategória* felügyelt Azure AD- és Azure RBAC-naplózás. Felügyelt Azure AD: jogkivonat be, felhasználói adatok. Azure RBAC: hozzáférési felülvizsgálatok be- és ki- és be.

## <a name="schedule-a-test-pod-on-the-aks-cluster"></a>Tesztpod ütemezése az AKS-fürtön

Egyes naplók létrehozásához hozzon létre egy új podot az AKS-fürtben. Az alábbi PÉLDA YAML-jegyzékfájl egy alapszintű NGINX-példány létrehozására használható. Hozzon létre egy nevű fájlt egy választott `nginx.yaml` szerkesztőben, és illessze be a következő tartalmat:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  nodeSelector:
    "beta.kubernetes.io/os": linux
  containers:
  - name: mypod
    image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
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

Hozza létre a podot a [kubectl create paranccsal,][kubectl-create] és adja meg a YAML-fájlt az alábbi példában látható módon:

```
$ kubectl create -f nginx.yaml

pod/nginx created
```

## <a name="view-collected-logs"></a>Összegyűjtött naplók megtekintése

A diagnosztikai naplók engedélyezése és megjelenése akár 10 percet is igénybe vehet.

> [!NOTE]
> Ha megfelelőségi vagy egyéb célból minden auditnapló-adatra szüksége van, gyűjtse össze és tárolja őket olcsó tárolóban, például blobtárolóban. A *kube-audit-admin* naplókategória használatával összegyűjtheti és mentheti a naplóadatok jelentéssel bíró készletét monitorozási és riasztási célokra.

A Azure Portal keresse meg az AKS-fürtöt,  és válassza a bal oldalon a Naplók lehetőséget. Ha *megjelenik, zárja* be a Példalekérdezések ablakot.

A bal oldalon válassza a Naplók **lehetőséget.** A *kube-auditnaplók megtekintéséhez* írja be a következő lekérdezést a szövegmezőbe:

```
AzureDiagnostics
| where Category == "kube-audit"
| project log_s
```

A rendszer valószínűleg számos naplót ad vissza. Az előző lépésben létrehozott NGINX pod naplóira vonatkozó lekérdezés hatókörének megtekintéséhez adjon hozzá egy további *where* utasítást *az nginx* kereséséhez az alábbi példalekérdezésben látható módon:

```
AzureDiagnostics
| where Category == "kube-audit"
| where log_s contains "nginx"
| project log_s
```

A *kube-audit-admin naplók megtekintéséhez* írja be a következő lekérdezést a szövegmezőbe:

```
AzureDiagnostics
| where Category == "kube-audit-admin"
| project log_s
```

Ebben a példában a lekérdezés a *kube-audit-admin összes létrehozási feladatát megjeleníti.* Valószínűleg számos eredmény van visszaadva, hogy a lekérdezés hatókörét az előző lépésben létrehozott NGINX-pod naplóinak megtekintéséhez egy további *where* utasítással keresse meg az *nginx-et,* ahogy az az alábbi példalekérdezésben látható.

```
AzureDiagnostics
| where Category == "kube-audit-admin"
| where log_s contains "nginx"
| project log_s
```


A naplóadatok lekérdezésére és szűrésére vonatkozó további információkért lásd: Naplóelemzési naplókereséssel gyűjtött [adatok megtekintése és elemzése.][analyze-log-analytics]

## <a name="log-event-schema"></a>Eseménysémák naplózása

Az AKS a következő eseményeket naplózza:

* [AzureActivity][log-schema-azureactivity]
* [AzureDiagnostics][log-schema-azurediagnostics]
* [AzureMetrics][log-schema-azuremetrics]
* [ContainerImageInventory][log-schema-containerimageinventory]
* [ContainerInventory][log-schema-containerinventory]
* [ContainerLog][log-schema-containerlog]
* [ContainerNodeInventory][log-schema-containernodeinventory]
* [ContainerServiceLog][log-schema-containerservicelog]
* [Szívverés][log-schema-heartbeat]
* [ElemzésekMetriák][log-schema-insightsmetrics]
* [KubeEvents][log-schema-kubeevents]
* [KubeHealth][log-schema-kubehealth]
* [KubeMonAgentEvents][log-schema-kubemonagentevents]
* [KubeNodeInventory][log-schema-kubenodeinventory]
* [KubePodInventory][log-schema-kubepodinventory]
* [KubeServices][log-schema-kubeservices]
* [Perf][log-schema-perf]

## <a name="log-roles"></a>Naplószerepk szerepkörök

| Szerepkör                     | Leírás |
|--------------------------|-------------|
| *aksService*             | A vezérlősík-művelet megjelenített neve az auditnaplóban (a hcpService szolgáltatásból) |
| *masterclient*           | A MasterClientCertificate auditnaplójában megjelenő név, az az aks get-credentials által lekért tanúsítvány |
| *nodeclient*             | Az ügynökcsomópontok által használt ClientCertificate megjelenített neve |

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan engedélyezheti és olvashatja át az AKS-fürtön található Kubernetes-vezérlősík összetevőinek naplóit. A további figyelése és hibaelhárítása érdekében megtekintheti a [Kubelet-naplókat,][kubelet-logs] és engedélyezheti [az SSH-csomópontok hozzáférését.][aks-ssh]

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create

<!-- LINKS - internal -->
[cli-quickstart]: kubernetes-walkthrough.md
[portal-quickstart]: kubernetes-walkthrough-portal.md
[log-analytics-overview]: ../azure-monitor/logs/log-query-overview.md
[analyze-log-analytics]: ../azure-monitor/logs/log-analytics-tutorial.md
[kubelet-logs]: kubelet-logs.md
[aks-ssh]: ssh.md
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
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