---
title: Hibrid és többfelhős Kubernetes-üzembe helyezések biztosítása az Azure Defender for Kubernetes
description: Az Azure Defender használata a helyszíni és a többfelhős Kubernetes-fürtökkel rendelkező Kubernetes
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 04/06/2021
ms.author: memildin
ms.openlocfilehash: 940cae8829a99ee7ffacdb41844237acc85b7761
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029199"
---
# <a name="defend-azure-arc-enabled-kubernetes-clusters-running-in-on-premises-and-multi-cloud-environments"></a>Helyszíni és többfelhős környezetekben futó Azure arc-kompatibilis Kubernetes-fürtök védelme

Az **Azure Defender for Kubernetes-fürtök bővítmény** a helyszíni fürtöket az Azure Kubernetes Service-fürtökhöz felkínált fenyegetések észlelési képességeivel is megvédheti. Engedélyezze az [Azure arc-kompatibilis Kubernetes](../azure-arc/kubernetes/overview.md) a fürtökön, és telepítse a bővítményt az ezen az oldalon leírtak szerint. 

A bővítmény más felhőalapú szolgáltatókon is képes védelemmel ellátni a Kubernetes-fürtöket, de nem a felügyelt Kubernetes-szolgáltatásain.

> [!TIP]
> A [githubon üzembe helyezési példákban](https://aka.ms/kubernetes-extension-installation-examples)a telepítési folyamat megkönnyítéséhez néhány minta fájlt készítettünk.

## <a name="availability"></a>Rendelkezésre állás

| Szempont | Részletek |
|--------|---------|
| Kiadási állapot | **Előnézet**<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]|
| Szükséges szerepkörök és engedélyek | A [biztonsági rendszergazda](../role-based-access-control/built-in-roles.md#security-admin) figyelmen kívül hagyhatja a riasztásokat<br>A [biztonsági olvasó](../role-based-access-control/built-in-roles.md#security-reader) megtekintheti az eredményeket |
| Díjszabás | [Az Azure Defender for Kubernetes](defender-for-kubernetes-introduction.md) használatát igényli |
| Támogatott Kubernetes-disztribúciók | [Azure Kubernetes szolgáltatás Azure Stack HCI-on](/azure-stack/aks-hci/overview)<br>[Kubernetes](https://kubernetes.io/docs/home/)<br> [AKS-motor](https://github.com/Azure/aks-engine)<br> [Red Hat OpenShift](https://www.openshift.com/learn/topics/kubernetes/) (4,6-es vagy újabb verzió) |
| Korlátozások | Az Azure arc-kompatibilis Kubernetes és az Azure Defender bővítmény **nem támogatja** a felügyelt Kubernetes-ajánlatokat, például a Google Kubernetes Engine és a rugalmas Kubernetes szolgáltatást. Az [Azure Defender natív módon elérhető az Azure Kubernetes Service (ak) szolgáltatáshoz](defender-for-kubernetes-introduction.md) , és nem szükséges a fürt csatlakoztatása az Azure-ív használatához. |
| Környezetek és régiók | A bővítmény rendelkezésre állása megegyezik az [Azure arc-kompatibilis Kubernetes](../azure-arc/kubernetes/overview.md)|

## <a name="architecture-overview"></a>Az architektúra áttekintése

Az AK-tól eltérő összes Kubernetes-fürthöz össze kell kapcsolni a fürtöt az Azure-ív használatával. A csatlakozás után az Azure Defender for Kubernetes üzembe helyezhető az [Azure arc-kompatibilis Kubernetes](../azure-arc/kubernetes/overview.md) -erőforrásokon [.](../azure-arc/kubernetes/extensions.md)

A bővítmény összetevői összegyűjtik a Kubernetes-naplókat a fürt összes vezérlő síkja és a felhőben lévő Azure Defender for Kubernetes háttér-elemzés céljából. A bővítmény egy adatfolyamatként használt Log Analytics-munkaterülethez van regisztrálva, de a naplózási napló nem a Log Analytics munkaterületen van tárolva.

Ez az ábra az Azure Defender for Kubernetes és az Azure arc-kompatibilis Kubernetes-fürt közötti interakciót mutatja be:

:::image type="content" source="media/defender-for-kubernetes-azure-arc/defender-for-kubernetes-architecture-overview.png" alt-text="Egy magas szintű architektúra-diagram, amely az Azure Defender for Kubernetes és egy Azure arc-kompatibilis Kubernetes-fürtök közötti interakciót ismerteti." lightbox="media/defender-for-kubernetes-azure-arc/defender-for-kubernetes-architecture-overview.png":::

## <a name="prerequisites"></a>Előfeltételek

- Az Azure Defender for Kubernetes [engedélyezve van az előfizetésében](enable-azure-defender.md)
- A Kubernetes-fürt [csatlakozik az Azure arc-hoz](../azure-arc/kubernetes/quickstart-connect-cluster.md)
- Ön teljesítette az [általános fürtszolgáltatások dokumentációjában](../azure-arc/kubernetes/extensions.md#prerequisites)felsorolt előfeltételeket.

## <a name="deploy-the-azure-defender-extension"></a>Az Azure Defender-bővítmény üzembe helyezése

Az Azure Defender-bővítményt számos módszer használatával telepítheti. A részletes lépések megtekintéséhez válassza a megfelelő lapot.

### <a name="azure-portal"></a>[**Azure Portal**](#tab/k8s-deploy-asc)

### <a name="use-the-quick-fix-option-from-the-security-center-recommendation"></a>Használja a Security Center javaslat "gyors javítás" lehetőségét

A Azure Security Center dedikált javaslata a következőket biztosítja:

- Annak **láthatósága** , hogy a fürtök melyik Kubernetes-bővítményre van telepítve
- **Egy "gyors javítás" lehetőség** , amellyel a fürtöket a bővítmény nélkül helyezheti üzembe

1. A Azure Security Center ajánlásai lapon nyissa meg az **Azure Defender** biztonsági vezérlő engedélyezése lehetőséget.

1. A szűrő használatával megkeresheti az Azure arc-kompatibilis Kubernetes-fürtök nevű javaslatot, hogy **telepítve legyen az Azure Defender bővítménye**.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="Azure Security Center javaslata az Azure Defender-bővítmény Azure arc-kompatibilis Kubernetes-fürtökön való üzembe helyezéséhez." lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::

    > [!TIP]
    > Figyelje meg a műveletek oszlop gyors javítás ikonját.

1. Válassza ki a bővítményt az kifogástalan állapotú és a nem megfelelő erőforrások részleteinek megtekintéséhez – a fürtök és a bővítmény nélkül.

1. A nem kifogástalan erőforrások listából válassza ki a fürtöt, és válassza a **szervizelés** lehetőséget, hogy a panelt a Szervizelési beállításokkal nyissa meg.

1. Válassza ki a megfelelő Log Analytics munkaterületet, és válassza az **x erőforrás szervizelése** lehetőséget.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/security-center-deploy-extension.gif" alt-text="Az Azure Defender-bővítmény üzembe helyezése az Azure arc Security Center gyors javítási lehetőségével.":::


### <a name="azure-cli"></a>[**Azure CLI**](#tab/k8s-deploy-cli)

### <a name="use-azure-cli-to-deploy-the-azure-defender-extension"></a>Az Azure Defender-bővítmény üzembe helyezése az Azure CLI használatával

1. Jelentkezzen be az Azure-ba:

    ```azurecli
    az login
    az account set --subscription <your-subscription-id>
    ```

    > [!IMPORTANT]
    > Győződjön meg arról, hogy ugyanazt az előfizetés-azonosítót használja, ``<your-subscription-id>`` mint amelyet a fürt Azure-ív csatlakoztatásához használt.

1. Futtassa a következő parancsot a bővítmény üzembe helyezéséhez az Azure arc-kompatibilis Kubernetes-fürtön:

    ```azurecli
    az k8s-extension create --name microsoft.azuredefender.kubernetes --cluster-type connectedClusters --cluster-name <cluster-name> --resource-group <resource-group> --extension-type microsoft.azuredefender.kubernetes
    ```

    Az Azure Defender-bővítmény típusának támogatott konfigurációs beállításainak leírását alább találja:

    | Tulajdonság | Leírás |
    |----------|-------------|
    | logAnalyticsWorkspaceResourceID | Nem **kötelező**. A saját Log Analytics munkaterület teljes erőforrás-azonosítója.<br>Ha nincs megadva, a rendszer a régió alapértelmezett munkaterületét fogja használni.<br><br>A teljes erőforrás-azonosító lekéréséhez futtassa a következő parancsot az előfizetésekben lévő munkaterületek listájának megjelenítéséhez az alapértelmezett JSON-formátumban:<br>```az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json```<br><br>A Log Analytics munkaterület erőforrás-azonosítója a következő szintaxissal rendelkezik:<br>/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.OperationalInsights/workspaces/{your-workspace-name}. <br>További információ [log Analytics munkaterületekről](../azure-monitor/logs/data-platform-logs.md#log-analytics-workspaces) |
    | auditLogPath |Nem **kötelező**. A naplófájlok teljes elérési útja.<br>Ha nincs megadva, az alapértelmezett elérési út ``/var/log/kube-apiserver/audit.log`` lesz használatban.<br>Az AK-motor esetében a standard útvonal a következő: ``/var/log/kubeaudit/audit.log`` |

    Az alábbi parancs az összes választható mező használatát mutatja be:

    ```azurecli
    az k8s-extension create --name microsoft.azuredefender.kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --extension-type microsoft.azuredefender.kubernetes --configuration-settings logAnalyticsWorkspaceResourceID=<log-analytics-workspace-resource-id> auditLogPath=<your-auditlog-path>
    ```

### <a name="resource-manager"></a>[**Resource Manager**](#tab/k8s-deploy-resource-manager)

### <a name="use-azure-resource-manager-to-deploy-the-azure-defender-extension"></a>Az Azure Defender bővítmény üzembe helyezése Azure Resource Manager használatával

Ahhoz, hogy az Azure Defender-bővítmény üzembe helyezéséhez Azure Resource Manager használni, szüksége lesz egy Log Analytics munkaterületre az előfizetésében. További információ: [log Analytics munkaterületek](../azure-monitor/logs/data-platform-logs.md#log-analytics-workspaces).

A Resource Manager-sablon **azure-defender-extension-arm-template.jsa** Security Center [telepítési példái](https://aka.ms/kubernetes-extension-installation-examples)alapján használható.

> [!TIP]
> Ha most ismerkedik a Resource Manager-sablonokkal, kezdje itt: [Mik azok a Azure Resource Manager sablonok?](../azure-resource-manager/templates/overview.md)

### <a name="rest-api"></a>[**REST API**](#tab/k8s-deploy-api)

### <a name="use-rest-api-to-deploy-the-azure-defender-extension"></a>Az Azure Defender bővítmény üzembe helyezése REST API használatával 

Ha az Azure Defender-bővítmény üzembe helyezéséhez REST APIt szeretne használni, szüksége lesz egy Log Analytics munkaterületre az előfizetésében. További információ: [log Analytics munkaterületek](../azure-monitor/logs/data-platform-logs.md#log-analytics-workspaces).

> [!TIP]
> Az Azure Defender bővítmény üzembe helyezésének legegyszerűbb módja a mellékelt **Poster Collection JSON** -példa a Security Center [telepítési példái](https://aka.ms/kubernetes-extension-installation-examples)alapján.
- A Poster-gyűjtemény JSON-fájljának módosításához vagy a bővítménynek a REST API használatával történő manuális telepítéséhez futtassa a következő PUT parancsot:

    ```rest
    PUT https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
    ```

    Ahol:

    | Name            | In   | Kötelező | Típus   | Description                                  |
    |-----------------|------|----------|--------|----------------------------------------------|
    | Előfizetés azonosítója | path | True (Igaz)     | sztring | Az Azure arc engedélyezett Kubernetes-erőforrás előfizetési azonosítója |
    | Erőforráscsoport  | path | True (Igaz)     | sztring | Az Azure arc-kompatibilis Kubernetes-erőforrást tartalmazó erőforráscsoport neve |
    | Fürt neve    | path | True (Igaz)     | sztring | Az Azure arc-kompatibilis Kubernetes-erőforrás neve  |


    A **hitelesítéshez** a fejlécnek rendelkeznie kell egy tulajdonosi jogkivonattal (mint más Azure API-kkal). A tulajdonosi jogkivonat beszerzéséhez futtassa a következő parancsot:

    ```az account get-access-token --subscription <your-subscription-id>``` Használja az alábbi struktúrát az üzenet törzséhez:
    ```json
    { 
    "properties": { 
        "extensionType": "microsoft.azuredefender.kubernetes",
    "con    figurationSettings": { 
            "logAnalytics.workspaceId":"YOUR-WORKSPACE-ID"
        // ,    "auditLogPath":"PATH/TO/AUDITLOG"
        },
        "configurationProtectedSettings": {
            "logAnalytics.key":"YOUR-WORKSPACE-KEY"
        }
        }
    } 
    ```

    A tulajdonságok leírását alább találja:

    | Tulajdonság | Leírás | 
    | -------- | ----------- |
    | logAnalytics. munkaterület azonosítója | A Log Analytics erőforrás munkaterület-azonosítója |
    | logAnalytics. Key         | A Log Analytics erőforrás kulcsa | 
    | auditLogPath             | Nem **kötelező**. A naplófájlok teljes elérési útja. Az alapértelmezett érték ``/var/log/kube-apiserver/audit.log`` |

---

## <a name="verify-the-deployment"></a>Az üzemelő példány ellenőrzése

Annak ellenőrzéséhez, hogy a fürtön telepítve van-e az Azure Defender bővítmény, kövesse az alábbi lapokon található lépéseket:

### <a name="azure-portal---security-center"></a>[**Azure Portal – Security Center**](#tab/k8s-verify-asc)

### <a name="use-security-center-recommendation-to-verify-the-status-of-your-extension"></a>Security Center javaslat használata a bővítmény állapotának ellenőrzéséhez

1. A Azure Security Center ajánlásai lapon nyissa meg az  **Azure Defender** biztonsági vezérlő engedélyezése lehetőséget.

1. Válassza ki az **Azure arc-kompatibilis Kubernetes-fürtök nevű javaslatot az Azure Defender bővítményének telepítésével**.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="Azure Security Center javaslata az Azure Defender-bővítmény Azure arc-kompatibilis Kubernetes-fürtökön való üzembe helyezéséhez." lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::

1. Győződjön meg arról, hogy a fürtöt, amelyre központilag telepítette a bővítményt, **kifogástalan** állapotú.


### <a name="azure-portal---azure-arc"></a>[**Azure Portal – Azure arc**](#tab/k8s-verify-arc)

### <a name="use-the-azure-arc-pages-to-verify-the-status-of-your-extension"></a>A bővítmény állapotának ellenőrzése az Azure arc-lapok használatával

1. A Azure Portal nyissa meg az **Azure-ívet**.
1. Az infrastruktúra listából válassza ki a **Kubernetes-fürtök** elemet, majd válassza ki az adott fürtöt.
1. Nyissa meg a kiterjesztések lapot. A fürt bővítményei megjelennek a listáján. A **telepítés állapota** oszlopban ellenőrizze, hogy az Azure Defender bővítmény megfelelően lett-e telepítve.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-installed-clusters-page.png" alt-text="Azure arc lap a Kubernetes-fürtön lévő összes telepített bővítmény állapotának ellenőrzéséhez." lightbox="media/defender-for-kubernetes-azure-arc/extension-installed-clusters-page.png":::

1. További részletekért válassza ki a bővítményt.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-details-page.png" alt-text="A Kubernetes-fürtökön található Azure arc-bővítmény részletes adatai.":::


### <a name="azure-cli"></a>[**Azure CLI**](#tab/k8s-verify-cli)

### <a name="use-azure-cli-to-verify-that-the-extension-is-deployed"></a>A bővítmény üzembe helyezésének ellenőrzése az Azure CLI használatával

1. Futtassa az alábbi parancsot az Azure CLI-ben:

    ```azurecli
    az k8s-extension show --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --name microsoft.azuredefender.kubernetes
    ```

1. A válaszban keresse meg a "extensionType": "Microsoft. azuredefender. kubernetes" és a "installState": "telepítve" lehetőséget.

    > [!NOTE]
    > A "installState": "függőben" érték jelenhet meg az első pár percben.
    
1. Ha az állapot azt mutatja, hogy **telepítve** van, futtassa a következő parancsot a gépen, és ellenőrizze, hogy a `kubeconfig` "azuredefender-xxxxx" nevű Pod "Running" állapotban van-e:
    
    ```console
    kubectl get pods -n azuredefender
    ```

### <a name="rest-api"></a>[**REST API**](#tab/k8s-verify-api)

### <a name="use-the-rest-api-to-verify-that-the-extension-is-deployed"></a>A REST API használatával ellenőrizze, hogy telepítve van-e a bővítmény

A sikeres központi telepítés megerősítéséhez vagy a bővítmény állapotának ellenőrzéséhez bármikor:

1. Futtassa a következő GET parancsot:

    ```rest
    GET https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
    ```

1. A válaszban keresse meg a "extensionType": "Microsoft. azuredefender. kubernetes" kifejezést a "installState": "Installed" kifejezésre.

    > [!TIP]
    > A "installState": "függőben" érték jelenhet meg az első pár percben.
    
1. Ha az állapot azt mutatja, hogy **telepítve** van, futtassa a következő parancsot a gépen, és ellenőrizze, hogy a `kubeconfig` "azuredefender-xxxxx" nevű Pod "Running" állapotban van-e:
    
    ```console
    kubectl get pods -n azuredefender
    ```
---

## <a name="simulate-security-alerts-from-azure-defender-for-kubernetes"></a>Biztonsági riasztások szimulálása az Azure Defendertől a Kubernetes-hez

A támogatott riasztások teljes listája a [Azure Security Center összes biztonsági riasztásának hivatkozási táblázatában](alerts-reference.md#alerts-akscluster)érhető el.

1. Az Azure Defender-riasztások szimulálásához futtassa a következő parancsot:

    ```console
    kubectl get pods --namespace=asc-alerttest-662jfi039n
    ```

    A várt válasz: "nincs erőforrás található".

    Az Azure Defender 30 percen belül felismeri ezt a tevékenységet, és elindít egy biztonsági riasztást.

1. A Azure Portal nyissa meg Azure Security Center biztonsági riasztások lapját, és keresse meg a megfelelő erőforráson a riasztást:

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/sample-kubernetes-security-alert.png" alt-text="Példa a Kubernetes készült Azure Defender-riasztásra." lightbox="media/defender-for-kubernetes-azure-arc/sample-kubernetes-security-alert.png":::

## <a name="removing-the-azure-defender-extension"></a>Az Azure Defender bővítmény eltávolítása

A bővítményt a Azure Portal, az Azure CLI vagy a REST API használatával távolíthatja el az alábbi lapokon leírtak szerint.

### <a name="azure-portal---arc"></a>[**Azure Portal – ív**](#tab/k8s-remove-arc)

### <a name="use-azure-portal-to-remove-the-extension"></a>A bővítmény eltávolításához használja a Azure Portal

1. A Azure Portal nyissa meg az Azure-ívet.
1. Az infrastruktúra listából válassza ki a **Kubernetes-fürtök** elemet, majd válassza ki az adott fürtöt.
1. Nyissa meg a kiterjesztések lapot. A fürt bővítményei megjelennek a listáján.
1. Válassza ki a fürtöt, és válassza az **Eltávolítás** lehetőséget.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-uninstall-clusters-page.png" alt-text="Bővítmény eltávolítása az ív-kompatibilis Kubernetes-fürtről." lightbox="media/defender-for-kubernetes-azure-arc/extension-uninstall-clusters-page.png":::

### <a name="azure-cli"></a>[**Azure CLI**](#tab/k8s-remove-cli)

### <a name="use-azure-cli-to-remove-the-azure-defender-extension"></a>Az Azure Defender-bővítmény eltávolítása az Azure CLI használatával

1. Távolítsa el az Azure Defender for Kubernetes arc bővítményt a következő parancsokkal:

    ```azurecli
    az login
    az account set --subscription <subscription-id>
    az k8s-extension delete --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --name microsoft.azuredefender.kubernetes --yes
    ```

    A bővítmény eltávolítása több percet is igénybe vehet. Azt javasoljuk, hogy várjon, amíg a rendszer megpróbálja ellenőrizni, hogy sikeres volt-e.

1. A bővítmény sikeres eltávolításának ellenőrzéséhez futtassa a következő parancsokat:

    ```azurecli
    az k8s-extension show --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --name microsoft.azuredefender.kubernetes
    ```

    A bővítmény erőforrásának a Azure Resource Managerból való törlése nem lehet késleltetés. Ezt követően ellenőrizze, hogy nincsenek-e "azuredefender-XXXXX" nevű hüvelyek a fürtön a következő parancs futtatásával a `kubeconfig` fürtre mutatott fájllal: 

    ```console
    kubectl get pods -n azuredefender
    ```

    A hüvelyek törléséhez néhány percet is igénybe vehet.

### <a name="rest-api"></a>[**REST API**](#tab/k8s-remove-api)

### <a name="use-rest-api-to-remove-the-azure-defender-extension"></a>REST API használata az Azure Defender-bővítmény eltávolításához 

A bővítmény a REST API használatával történő eltávolításához futtassa a következő TÖRLÉSi parancsot:

```rest
DELETE https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
```

| Name            | In   | Kötelező | Típus   | Description                                           |
|-----------------|------|----------|--------|-------------------------------------------------------|
| Előfizetés azonosítója | path | True (Igaz)     | sztring | Az ív-kompatibilis Kubernetes-fürt előfizetés-azonosítója |
| Erőforráscsoport  | path | True (Igaz)     | sztring | Az ív által engedélyezett Kubernetes-fürt erőforráscsoport  |
| Fürt neve    | path | True (Igaz)     | sztring | Az ív által engedélyezett Kubernetes-fürt neve            |

A **hitelesítéshez** a fejlécnek rendelkeznie kell egy tulajdonosi jogkivonattal (mint más Azure API-kkal). A tulajdonosi jogkivonat beszerzéséhez futtassa a következő parancsot:

```azurecli
az account get-access-token --subscription <your-subscription-id>
```

A kérelem végrehajtása több percet is igénybe vehet.

---

## <a name="next-steps"></a>Következő lépések

Ez az oldal azt ismerteti, hogyan lehet üzembe helyezni az Azure Defender-bővítményt az Azure arc-kompatibilis Kubernetes-fürtökön. Az Azure Defender és a Azure Security Center tároló biztonsági funkcióival kapcsolatban az alábbi lapokon tájékozódhat:

- [Tárolóbiztonság a Security Centerben](container-security.md)
- [Bevezetés az Azure Defender for Kubernetes használatába](defender-for-kubernetes-introduction.md)
- [A Kubernetes számítási feladatok védelme](kubernetes-workload-protections.md)
