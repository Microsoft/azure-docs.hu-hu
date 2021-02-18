---
title: Új Azure Kubernetes Service-(ak-) fürt figyelése | Microsoft Docs
description: Megtudhatja, hogyan engedélyezheti a figyelést egy új Azure Kubernetes Service (ak) fürthöz a Azure Monitor for containers előfizetéssel.
ms.topic: conceptual
ms.date: 04/25/2019
ms.custom: devx-track-terraform, devx-track-azurecli
ms.openlocfilehash: 19c4a88cee8776136593b041e94dd14c7c9c28d6
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100620055"
---
# <a name="enable-monitoring-of-a-new-azure-kubernetes-service-aks-cluster"></a>Új Azure Kubernetes Service-(ak-) fürt figyelésének engedélyezése

Ez a cikk azt ismerteti, hogyan állítható be Azure Monitor for containers az [Azure Kubernetes Service](../../aks/index.yml) -ben üzemeltetett felügyelt Kubernetes-fürt figyelésére, amelyet az előfizetésében üzembe helyezésre készül.

A támogatott módszerek egyikének használatával engedélyezheti az AK-fürtök figyelését:

* Azure CLI
* Terraform

## <a name="enable-using-azure-cli"></a>Engedélyezés az Azure CLI használatával

Az Azure CLI-vel létrehozott új AK-fürt figyelésének engedélyezéséhez kövesse a rövid útmutató című cikk lépéseit az [AK-fürt létrehozása](../../aks/kubernetes-walkthrough.md#create-aks-cluster)című szakaszban.  

>[!NOTE]
>Ha úgy dönt, hogy az Azure CLI-t használja, először telepítenie és használnia kell a CLI-t helyileg. Az Azure CLI 2.0.74 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a parancsot `az --version` . Ha telepítenie vagy frissítenie kell az Azure CLI-t, tekintse meg [Az Azure CLI telepítését](/cli/azure/install-azure-cli)ismertető témakört. Ha telepítette az AK-előnézet CLI-bővítményének 0.4.12 vagy újabb verzióját, távolítsa el az előnézeti bővítmény engedélyezéséhez szükséges módosításokat, mivel ez felülbírálhatja az alapértelmezett Azure CLI-viselkedést, mivel az AK előzetes verziójának funkciói nem érhetők el az Azure US Governmnet-felhőben.

## <a name="enable-using-terraform"></a>Engedélyezés a Terraform használatával

Ha [új AK-fürtöt helyez üzembe a Terraform használatával](/azure/developer/terraform/create-k8s-cluster-with-tf-and-aks), akkor a profilban szükséges argumentumokat kell megadnia [log Analytics munkaterület létrehozásához](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_workspace.html) , ha a felhasználó nem ad meg egy meglévőt. 

>[!NOTE]
>Ha a Terraform használata mellett dönt, akkor a Terraform Azure RM Provider 1.17.0 vagy újabb verzióját kell futtatnia.

Ha Azure Monitor szeretne hozzáadni a tárolóhoz a munkaterülethez, tekintse meg a [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) és a profil befejezéséhez a [**addon_profile**](https://www.terraform.io/docs/providers/azurerm/r/kubernetes_cluster.html#addon_profile) , majd a **oms_agent** megadása című témakört. 

Miután engedélyezte a figyelést, és az összes konfigurációs feladat sikeresen befejeződött, két módon figyelheti a fürt teljesítményét:

* Közvetlenül az AK-fürtben válassza az **állapot** lehetőséget a bal oldali ablaktáblán.
* A kiválasztott fürt AK-fürt lapjának **figyelés tároló-bepillantások** csempéjét választva. Azure Monitor a bal oldali ablaktáblán válassza az **állapot** lehetőséget. 

  ![A tárolók Azure Monitor kiválasztásának lehetőségei az AK-ban](./media/container-insights-onboard/kubernetes-select-monitoring-01.png)

A figyelés engedélyezése után körülbelül 15 percet is igénybe vehet, mielőtt megtekintheti a fürthöz tartozó állapot mérőszámait. 

## <a name="verify-agent-and-solution-deployment"></a>Ügynök és megoldás központi telepítésének ellenőrzése
Az ügynök *06072018* -es vagy újabb verziójával ellenőrizheti, hogy az ügynök és a megoldás telepítése is sikeres volt-e. Az ügynök korábbi verzióiban csak az ügynök üzembe helyezését ellenőrizheti.

### <a name="agent-version-06072018-or-later"></a>Ügynök 06072018-es vagy újabb verziója
A következő parancs futtatásával ellenőrizheti, hogy az ügynök telepítése sikeresen megtörtént-e. 

```
kubectl get ds omsagent --namespace=kube-system
```

A kimenetnek az alábbihoz hasonlónak kell lennie, ami azt jelzi, hogy megfelelően lett telepítve:

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

A megoldás üzembe helyezésének ellenőrzéséhez futtassa a következő parancsot:

```
kubectl get deployment omsagent-rs -n=kube-system
```

A kimenetnek az alábbihoz hasonlónak kell lennie, ami azt jelzi, hogy megfelelően lett telepítve:

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>Ügynök verziója 06072018-nál korábbi

A következő parancs futtatásával ellenőrizheti, hogy a Log Analytics ügynöknek a *06072018* -es verziójának megfelelő telepítését adta-e meg:  

```
kubectl get ds omsagent --namespace=kube-system
```

A kimenetnek az alábbihoz hasonlónak kell lennie, ami azt jelzi, hogy megfelelően lett telepítve:  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>Konfiguráció megtekintése a parancssori felülettel
A `aks show` paranccsal olyan részleteket kaphat, mint például a megoldás, amely a log Analytics munkaterület resourceID, valamint a fürt összegző részleteit is lehetővé teszi.  

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

Néhány perc elteltével a parancs befejeződik, és a megoldáshoz tartozó JSON-formátumú adatokat adja vissza.  A parancs eredményeinek meg kell jeleníteniük a figyelési bővítmény profilját, és a következő példában szereplő kimenetre hasonlítanak:

```
"addonProfiles": {
    "omsagent": {
      "config": {
        "logAnalyticsWorkspaceResourceID": "/subscriptions/<WorkspaceSubscription>/resourceGroups/<DefaultWorkspaceRG>/providers/Microsoft.OperationalInsights/workspaces/<defaultWorkspaceName>"
      },
      "enabled": true
    }
  }
```

## <a name="next-steps"></a>Következő lépések

* Ha problémákat tapasztal a megoldás bevezetésére tett kísérlet során, tekintse át a [hibaelhárítási útmutatót](container-insights-troubleshoot.md) .

* Megtudhatja, [hogyan használhatja](container-insights-analyze.md) a Azure monitor a tárolók számára, hogy az AK-fürtök és a rajtuk futó munkaterhelések állapotát és erőforrás-kihasználtságát összegyűjtse.

