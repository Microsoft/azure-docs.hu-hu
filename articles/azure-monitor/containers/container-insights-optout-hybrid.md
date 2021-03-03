---
title: A hibrid Kubernetes-fürt figyelésének leállítása | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan állíthatja le a hibrid Kubernetes-fürtök figyelését a Container-információkkal.
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: e8708d6b860683cc96a806160ccc7c8e33949ab2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713694"
---
# <a name="how-to-stop-monitoring-your-hybrid-cluster"></a>A hibrid fürt figyelésének leállítása

Miután engedélyezte a Kubernetes-fürt figyelését, leállíthatja a fürt figyelését a tároló-megállapításokkal, ha úgy dönt, hogy már nem szeretné figyelni. Ez a cikk bemutatja, hogyan hajthatja végre ezt a következő környezetekben:

- AK-motor az Azure-ban és Azure Stack
- OpenShift 4-es és újabb verziója
- Azure Arc-kompatibilis Kubernetes (előzetes verzió)

## <a name="how-to-stop-monitoring-using-helm"></a>A figyelés leállítása a Helm használatával

A következő lépések a következő környezetekre vonatkoznak:

- AK-motor az Azure-ban és Azure Stack
- OpenShift 4-es és újabb verziója

1. A következő Helm-parancs futtatásával azonosíthatja a fürtön telepített Container bepillantást az Helm chart kiadásban.

    ```
    helm list
    ```

    A kimenet a következőhöz hasonló lesz:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    azmon-containers-release-1      default         3               2020-04-21 15:27:24.1201959 -0700 PDT   deployed        azuremonitor-containers-2.7.0   7.0.0-1
    ```

    *azmon – tárolók – a Release-1* a Helm chart kiadását jelöli a tárolók bepillantást.

2. A diagram kiadásának törléséhez futtassa a következő Helm-parancsot.

    `helm delete <releaseName>`

    Példa:

    `helm delete azmon-containers-release-1`

    Ezzel törli a kiadást a fürtből. A parancs futtatásával ellenőrizheti a `helm list` következőket:

    ```
    NAME                            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                           APP VERSION
    ```

A konfiguráció módosítása több percet is igénybe vehet. Mivel a Helm a törlése után is nyomon követi a kiadásokat, naplózhatja a fürt előzményeit, és visszavonhatja a kiadás törlését is `helm rollback` .

## <a name="how-to-stop-monitoring-on-arc-enabled-kubernetes"></a>Az ív-kompatibilis Kubernetes figyelésének leállítása

### <a name="using-powershell"></a>A PowerShell használata

1. Töltse le és mentse a parancsfájlt egy helyi mappába, amely a következő parancsokkal konfigurálja a fürtöt a figyelési bővítmény használatával:

    ```powershell
    wget https://aka.ms/disable-monitoring-powershell-script -OutFile disable-monitoring.ps1
    ```

2. Konfigurálja a `$azureArcClusterResourceId` változót úgy, hogy beállítja a megfelelő értékeit `subscriptionId` , `resourceGroupName` és `clusterName` Az Azure arc-kompatibilis Kubernetes-fürterőforrás erőforrás-azonosítóját jelképezi.

    ```powershell
    $azureArcClusterResourceId = "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. Konfigurálja a `$kubeContext` változót a fürt **Kube-környezetével** a parancs futtatásával `kubectl config get-contexts` . Ha az aktuális környezetet szeretné használni, állítsa a értéket a következőre: `""` .

    ```powershell
    $kubeContext = "<kubeContext name of your k8s cluster>"
    ```

4. Futtassa a következő parancsot a fürt figyelésének leállításához.

    ```powershell
    .\disable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -kubeContext $kubeContext
    ```

#### <a name="using-service-principal"></a>Egyszerű szolgáltatásnév használata
A szkript *disable-monitoring.ps1* az interaktív eszköz bejelentkezését használja. Ha inkább a nem interaktív bejelentkezést részesíti előnyben, használhat egy meglévő szolgáltatásnevet, vagy létrehozhat egy újat, amely rendelkezik a szükséges engedélyekkel az [Előfeltételek](container-insights-enable-arc-enabled-clusters.md#prerequisites)szakaszban leírtak szerint. Az egyszerű szolgáltatásnév használatához át kell adnia $servicePrincipalClientId, $servicePrincipalClientSecret és $tenantId paramétereket a szolgáltatás enable-monitoring.ps1 parancsfájlhoz használni kívánt egyszerű szolgáltatásnév értékeivel.

```powershell
$subscriptionId = "<subscription Id of the Azure Arc connected cluster resource>"
$servicePrincipal = New-AzADServicePrincipal -Role Contributor -Scope "/subscriptions/$subscriptionId"

$servicePrincipalClientId =  $servicePrincipal.ApplicationId.ToString()
$servicePrincipalClientSecret = [System.Net.NetworkCredential]::new("", $servicePrincipal.Secret).Password
$tenantId = (Get-AzSubscription -SubscriptionId $subscriptionId).TenantId
```

Például:

```powershell
\disable-monitoring.ps1 -clusterResourceId $azureArcClusterResourceId -kubeContext $kubeContext -servicePrincipalClientId $servicePrincipalClientId -servicePrincipalClientSecret $servicePrincipalClientSecret -tenantId $tenantId
```


### <a name="using-bash"></a>Bash használata

1. Töltse le és mentse a parancsfájlt egy helyi mappába, amely a következő parancsokkal konfigurálja a fürtöt a figyelési bővítmény használatával:

    ```bash
    curl -o disable-monitoring.sh -L https://aka.ms/disable-monitoring-bash-script
    ```

2. Konfigurálja a `azureArcClusterResourceId` változót úgy, hogy beállítja a megfelelő értékeit `subscriptionId` , `resourceGroupName` és `clusterName` Az Azure arc-kompatibilis Kubernetes-fürterőforrás erőforrás-azonosítóját jelképezi.

    ```bash
    export azureArcClusterResourceId="/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
    ```

3. Konfigurálja a `kubeContext` változót a fürt **Kube-környezetével** a parancs futtatásával `kubectl config get-contexts` .

    ```bash
    export kubeContext="<kubeContext name of your k8s cluster>"
    ```

4. A fürt figyelésének leállításához különböző parancsok vannak megadva a telepítési forgatókönyv alapján.

    A következő parancs futtatásával állítsa le a fürt figyelését az aktuális környezettel.

    ```bash
    bash disable-monitoring.sh --resource-id $azureArcClusterResourceId
    ```

    Futtassa a következő parancsot a fürt figyelésének leállításához a környezet megadásával

    ```bash
    bash disable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext
    ```

#### <a name="using-service-principal"></a>Egyszerű szolgáltatásnév használata
A bash parancsfájl *disable-monitoring.sh* az interaktív eszköz bejelentkezését használja. Ha inkább a nem interaktív bejelentkezést részesíti előnyben, használhat egy meglévő szolgáltatásnevet, vagy létrehozhat egy újat, amely rendelkezik a szükséges engedélyekkel az [Előfeltételek](container-insights-enable-arc-enabled-clusters.md#prerequisites)szakaszban leírtak szerint. Az egyszerű szolgáltatás használatához át kell adnia az ügyfél-azonosító,--Client-Secret és a---bérlő-ID értékeket, amelyeket a bash-szkript *enable-monitoring.sh* kíván használni.

```bash
subscriptionId="<subscription Id of the Azure Arc connected cluster resource>"
servicePrincipal=$(az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/${subscriptionId}")
servicePrincipalClientId=$(echo $servicePrincipal | jq -r '.appId')

servicePrincipalClientSecret=$(echo $servicePrincipal | jq -r '.password')
tenantId=$(echo $servicePrincipal | jq -r '.tenant')
```

Például:

```bash
bash disable-monitoring.sh --resource-id $azureArcClusterResourceId --kube-context $kubeContext --client-id $servicePrincipalClientId --client-secret $servicePrincipalClientSecret  --tenant-id $tenantId
```

## <a name="next-steps"></a>Következő lépések

Ha a Log Analytics munkaterület csak a fürt figyelésének támogatására lett létrehozva, és már nincs rá szükség, manuálisan kell törölnie. Ha nem ismeri a munkaterület törlésének módját, tekintse meg [Az Azure log Analytics munkaterület törlése](../logs/delete-workspace.md)című témakört.