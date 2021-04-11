---
title: Helm-diagramok üzembe helyezése GitOps használatával az arc-kompatibilis Kubernetes-fürtön
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Az GitOps és a Helm használata az Azure arc-kompatibilis fürtkonfiguráció konfigurálásához
keywords: GitOps, Kubernetes, K8s, Azure, Helm, arc, AK, Azure Kubernetes szolgáltatás, tárolók
ms.openlocfilehash: df9b40764ec463553659803749f282bbc4587bde
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449543"
---
# <a name="deploy-helm-charts-using-gitops-on-an-arc-enabled-kubernetes-cluster"></a>Helm-diagramok üzembe helyezése GitOps használatával egy arc-kompatibilis Kubernetes-fürtön

A Helm egy nyílt forráskódú csomagoló eszköz, amely a Kubernetes-alkalmazások telepítését és életciklusának kezelését segíti. A Linux-csomagkezelő, például az APT és a yum hasonló, a Helm a Kubernetes-diagramok kezelésére szolgál, amelyek előre konfigurált Kubernetes-erőforrások csomagjai.

Ez a cikk bemutatja, hogyan konfigurálhatja és használhatja a Helm-t az Azure arc-kompatibilis Kubernetes.

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Egy meglévő Azure arc-kompatibilis Kubernetes csatlakoztatott fürt.
    - Ha még nem csatlakoztatta a fürtöt, tekintse meg a [Connect an Azure arc enabled Kubernetes-fürt](quickstart-connect-cluster.md)gyors üzembe helyezését ismertető útmutatót.
- A szolgáltatás előnyeinek és architektúrájának megismerése. További információk: [konfigurációk és GitOps – Azure arc-kompatibilis Kubernetes-cikk](conceptual-configurations.md).
- Telepítse az `k8s-configuration` Azure CLI-bővítményt >= 1.0.0 verzióra:
  
  ```azurecli
  az extension add --name k8s-configuration
  ```

## <a name="overview-of-using-gitops-and-helm-with-azure-arc-enabled-kubernetes"></a>A GitOps és a Helm használatának áttekintése az Azure arc-kompatibilis Kubernetes

 A Helm operátor a Helm chart kiadásait automatizáló adatáramlási bővítményt biztosít. A Helm diagram kiadása egy HelmRelease nevű egyéni Kubernetes-erőforráson keresztül van leírva. A Flux szinkronizálja ezeket az erőforrásokat a git-ből a fürtbe, míg a Helm operátor biztosítja, hogy a Helm-diagramok az erőforrásokban megadott módon legyenek közzétéve.

 A cikkben használt [példa adattár](https://github.com/Azure/arc-helm-demo) a következő módon van strukturálva:

```console
├── charts
│   └── azure-arc-sample
│       ├── Chart.yaml
│       ├── templates
│       │   ├── NOTES.txt
│       │   ├── deployment.yaml
│       │   └── service.yaml
│       └── values.yaml
└── releases
    └── app.yaml
```

A git-tárházban két könyvtárunk van: az egyik egy Helm-diagramot, egy pedig a releases config fájlt tartalmazza. A `releases` címtárban a `app.yaml` tartalmazza a HelmRelease konfigurációját, amely az alábbi ábrán látható:

```yaml
apiVersion: helm.fluxcd.io/v1
kind: HelmRelease
metadata:
  name: azure-arc-sample
  namespace: arc-k8s-demo
spec:
  releaseName: arc-k8s-demo
  chart:
    git: https://github.com/Azure/arc-helm-demo
    ref: master
    path: charts/azure-arc-sample
  values:
    serviceName: arc-k8s-demo
```

A Helm kiadásának konfigurációja a következő mezőket tartalmazza:

| Mező | Leírás |
| ------------- | ------------- | 
| `metadata.name` | Kötelező mező. A Kubernetes elnevezési konvenciókat kell követnie. |
| `metadata.namespace` | Nem kötelező kitölteni. Meghatározza, hogy a rendszer hol hozza létre a kiadást. |
| `spec.releaseName` | Nem kötelező kitölteni. Ha nincs megadva, a kiadás neve lesz `$namespace-$name` . |
| `spec.chart.path` | A diagramot tartalmazó könyvtár (az adattár gyökeréhez képest). |
| `spec.values` | A diagram alapértelmezett paramétereinek értékeinek felhasználói testreszabása. |

A HelmRelease megadott beállítások `spec.values` felülbírálják a `values.yaml` diagram forrásában megadott beállításokat.

A HelmRelease kapcsolatos további információkért tekintse meg a hivatalos [Helm-kezelő dokumentációját](https://docs.fluxcd.io/projects/helm-operator/en/stable/).

## <a name="create-a-configuration"></a>Konfiguráció létrehozása

Az Azure CLI-bővítményének használatával `k8s-configuration` csatlakoztassa a csatlakoztatott fürtöt a példa git-tárházhoz. Adja meg ezt a konfigurációt a név `azure-arc-sample` és a Flux-operátor üzembe helyezése a `arc-k8s-demo` névtérben.

```console
az k8s-configuration create --name azure-arc-sample --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name flux --operator-namespace arc-k8s-demo --operator-params='--git-readonly --git-path=releases' --enable-helm-operator --helm-operator-chart-version='1.2.0' --helm-operator-params='--set helm.versions=v3' --repository-url https://github.com/Azure/arc-helm-demo.git --scope namespace --cluster-type connectedClusters
```

### <a name="configuration-parameters"></a>Konfigurációs paraméterek

A konfiguráció létrehozásának testreszabásához további tudnivalókat a [paramétereket ismertető](./tutorial-use-gitops-connected-cluster.md#additional-parameters)témakörben olvashat.

## <a name="validate-the-configuration"></a>A konfiguráció ellenőrzése

Az Azure CLI használatával ellenőrizze, hogy a konfiguráció létrehozása sikeres volt-e.

```console
az k8s-configuration show --name azure-arc-sample --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

A konfigurációs erőforrás a megfelelőségi állapottal, az üzenetekkel és a hibakeresési információkkal frissül.

```output
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2019-12-05T05:34:41.481000",
    "message": "{\"OperatorMessage\":null,\"ClusterState\":null}",
    "messageLevel": "3"
  },
  "enableHelmOperator": "True",
  "helmOperatorProperties": {
    "chartValues": "--set helm.versions=v3",
    "chartVersion": "1.2.0"
  },
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/azure-arc-sample",
  "name": "azure-arc-sample",
  "operatorInstanceName": "flux",
  "operatorNamespace": "arc-k8s-demo",
  "operatorParams": "--git-readonly --git-path=releases",
  "operatorScope": "namespace",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "",
  "repositoryUrl": "https://github.com/Azure/arc-helm-demo.git",
  "resourceGroup": "AzureArcTest",
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

## <a name="validate-application"></a>Alkalmazás érvényesítése

Futtassa az alábbi parancsot, és navigáljon a `localhost:8080` böngészőjében, és ellenőrizze, hogy fut-e az alkalmazás.

```console
kubectl port-forward -n arc-k8s-demo svc/arc-k8s-demo 8080:8080
```

## <a name="next-steps"></a>Következő lépések

A fürtök konfigurációjának alkalmazása a [Azure Policy](./use-azure-policy.md)használatával.
