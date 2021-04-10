---
title: Az Azure arc-kompatibilis Kubernetes kapcsolatos gyakori problémák elhárítása
services: azure-arc
ms.service: azure-arc
ms.date: 03/02/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Az arc-kompatibilis Kubernetes-fürtökkel kapcsolatos gyakori problémák elhárítása.
keywords: Kubernetes, arc, Azure, tárolók
ms.openlocfilehash: 992ea75c48b2630032e1314610986fbc610eec7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105025781"
---
# <a name="azure-arc-enabled-kubernetes-troubleshooting"></a>Azure arc-kompatibilis Kubernetes-hibaelhárítás

Ez a dokumentum a kapcsolattal, engedélyekkel és ügynökökkel kapcsolatos problémákhoz nyújt hibaelhárítási útmutatót.

## <a name="general-troubleshooting"></a>Általános hibaelhárítás

### <a name="azure-cli"></a>Azure CLI

`az connectedk8s` `az k8s-configuration` A vagy CLI parancsok használata előtt győződjön meg arról, hogy az Azure CLI úgy van beállítva, hogy a megfelelő Azure-előfizetésen működjön.

```azurecli
az account set --subscription 'subscriptionId'
az account show
```

### <a name="azure-arc-agents"></a>Azure arc-ügynökök

Az Azure arc-kompatibilis Kubernetes összes ügynöke hüvelyként van telepítve a `azure-arc` névtérben. Az összes hüvelynek futnia kell, és át kell haladnia az állapotuk ellenőrzésével.

Először ellenőrizze az Azure arc Helm kiadását:

```console
$ helm --namespace default status azure-arc
NAME: azure-arc
LAST DEPLOYED: Fri Apr  3 11:13:10 2020
NAMESPACE: default
STATUS: deployed
REVISION: 5
TEST SUITE: None
```

Ha a Helm kiadása nem található vagy hiányzik, próbálja újra [csatlakoztatni a fürtöt az Azure arc-hoz](./quickstart-connect-cluster.md) .

Ha a Helm kiadása megtalálható a `STATUS: deployed` -ben, ellenőrizze az ügynökök állapotát az alábbiak használatával `kubectl` :

```console
$ kubectl -n azure-arc get deployments,pods
NAME                                       READY  UP-TO-DATE  AVAILABLE  AGE
deployment.apps/clusteridentityoperator     1/1       1          1       16h
deployment.apps/config-agent                1/1       1          1       16h
deployment.apps/cluster-metadata-operator   1/1       1          1       16h
deployment.apps/controller-manager          1/1       1          1       16h
deployment.apps/flux-logs-agent             1/1       1          1       16h
deployment.apps/metrics-agent               1/1       1          1       16h
deployment.apps/resource-sync-agent         1/1       1          1       16h

NAME                                            READY   STATUS  RESTART  AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

Az összes hüvelynek `STATUS` `Running` `3/3` `2/2` a vagy az `READY` oszlop alá kell mutatnia. A naplók beolvasása és a vagy a által visszaadott hüvelyek leírása `Error` `CrashLoopBackOff` . Ha a hüvelyek állapota elakad `Pending` , előfordulhat, hogy nem áll rendelkezésre elegendő erőforrás a fürtcsomópontokon. [A fürt vertikális Felskálázása](https://kubernetes.io/docs/tasks/administer-cluster/) esetén ezek a hüvelyek `Running` állapotba kerülhetnek.

## <a name="connecting-kubernetes-clusters-to-azure-arc"></a>Kubernetes-fürtök csatlakoztatása az Azure arc-hoz

A fürtök az Azure-hoz való csatlakoztatásához az Azure-előfizetéshez és `cluster-admin` a célként megadott fürthöz való hozzáféréshez is hozzáférést kell adni. Ha nem tudja elérni a fürtöt, vagy nem rendelkezik megfelelő engedélyekkel, a fürt csatlakoztatása az Azure arc-hoz sikertelen lesz.

### <a name="insufficient-cluster-permissions"></a>Nem elégségesek a fürt engedélyei

Ha a megadott kubeconfig-fájl nem rendelkezik megfelelő engedélyekkel az Azure arc-ügynökök telepítéséhez, az Azure CLI-parancs hibát ad vissza.

```azurecli
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...

Error: list: failed to list: secrets is forbidden: User "myuser" cannot list resource "secrets" in API group "" at the cluster scope
```

A fürtöt az Azure arc-hoz összekötő felhasználónak hozzá kell `cluster-admin` rendelnie egy szerepkört a fürthöz.

### <a name="installation-timeouts"></a>Telepítési időtúllépések

A Kubernetes-fürtök Azure arc-kompatibilis Kubernetes való csatlakoztatásához Azure arc-ügynököket kell telepíteni a fürtön. Ha a fürt lassú internetkapcsolaton keresztül fut, az ügynökök számára a tároló képe hosszabb időt vehet igénybe, mint az Azure CLI-időtúllépés.

```azurecli
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...
```

### <a name="helm-issue"></a>Helm-probléma

A Helm `v3.3.0-rc.1` verziója olyan [hibát](https://github.com/helm/helm/pull/8527) tartalmaz, amelyben a Helm install/upgrade (a `connectedk8s` CLI bővítmény által használt) az összes Hook futtatását eredményezi, ami a következő hibához vezet:

```console
$ az connectedk8s connect -n shasbakstest -g shasbakstest
Ensure that you have the latest helm version installed before proceeding.
This operation might take a while...

Please check if the azure-arc namespace was deployed and run 'kubectl get pods -n azure-arc' to check if all the pods are in running state. A possible cause for pods stuck in pending state could be insufficientresources on the kubernetes cluster to onboard to arc.
ValidationError: Unable to install helm release: Error: customresourcedefinitions.apiextensions.k8s.io "connectedclusters.arc.azure.com" not found
```

A probléma megoldásához kövesse az alábbi lépéseket:

1. Törölje az Azure arc-kompatibilis Kubernetes-erőforrást a Azure Portal.
2. Futtassa a következő parancsokat a gépen:
    
    ```console
    kubectl delete ns azure-arc
    kubectl delete clusterrolebinding azure-arc-operator
    kubectl delete secret sh.helm.release.v1.azure-arc.v1
    ```

3. Telepítse a Helm 3 [stabil verzióját](https://helm.sh/docs/intro/install/) a gépen a kiadásra jelölt verzió helyett.
4. Futtassa a `az connectedk8s connect` parancsot a megfelelő értékekkel, hogy csatlakozhasson a fürthöz az Azure-ív használatával.

## <a name="configuration-management"></a>Konfigurációkezelés

### <a name="general"></a>Általános kérdések
A konfigurációs erőforrással kapcsolatos problémák elhárításához futtassa az parancsot a `--debug` megadott paraméterrel.

```console
az provider show -n Microsoft.KubernetesConfiguration --debug
az k8s-configuration create <parameters> --debug
```

### <a name="create-configurations"></a>Konfigurációk létrehozása

Az Azure arc-kompatibilis Kubernetes erőforrás () írási engedélyei `Microsoft.Kubernetes/connectedClusters/Write` szükségesek és elegendőek a fürtön lévő konfigurációk létrehozásához.

### <a name="configuration-remains-pending"></a>A konfiguráció továbbra is fennáll `Pending`

```console
kubectl -n azure-arc logs -l app.kubernetes.io/component=config-agent -c config-agent
$ k -n pending get gitconfigs.clusterconfig.azure.com  -o yaml
apiVersion: v1
items:
- apiVersion: clusterconfig.azure.com/v1beta1
  kind: GitConfig
  metadata:
    creationTimestamp: "2020-04-13T20:37:25Z"
    generation: 1
    name: pending
    namespace: pending
    resourceVersion: "10088301"
    selfLink: /apis/clusterconfig.azure.com/v1beta1/namespaces/pending/gitconfigs/pending
    uid: d9452407-ff53-4c02-9b5a-51d55e62f704
  spec:
    correlationId: ""
    deleteOperator: false
    enableHelmOperator: false
    giturl: git@github.com:slack/cluster-config.git
    helmOperatorProperties: null
    operatorClientLocation: azurearcfork8s.azurecr.io/arc-preview/fluxctl:0.1.3
    operatorInstanceName: pending
    operatorParams: '"--disable-registry-scanning"'
    operatorScope: cluster
    operatorType: flux
  status:
    configAppliedTime: "2020-04-13T20:38:43.081Z"
    isSyncedWithAzure: true
    lastPolledStatusTime: ""
    message: 'Error: {exit status 1} occurred while doing the operation : {Installing
      the operator} on the config'
    operatorPropertiesHashed: ""
    publicKey: ""
    retryCountPublicKey: 0
    status: Installing the operator
kind: List
metadata:
  resourceVersion: ""
  selfLink: ""
```
## <a name="monitoring"></a>Figyelés

A tárolók Azure Monitor számára szükséges, hogy a Daemonset elemet privilegizált módban fussanak. Egy Canonical Charmed Kubernetes-fürt sikeres beállításához a figyeléshez futtassa a következő parancsot:

```console
juju config kubernetes-worker allow-privileged=true
```