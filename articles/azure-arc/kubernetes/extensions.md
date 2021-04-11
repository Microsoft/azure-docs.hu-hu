---
title: Azure arc-kompatibilis Kubernetes-fürt bővítményei
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Bővítmények életciklusának üzembe helyezése és kezelése az Azure arc-kompatibilis Kubernetes
ms.openlocfilehash: 63fb14818d148dcc579300fdb4c89d636b47fd05
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451038"
---
# <a name="kubernetes-cluster-extensions"></a>Kubernetes-fürt bővítményei

A Kubernetes Extensions szolgáltatás lehetővé teszi a következőket az Azure arc-kompatibilis Kubernetes-fürtökön:

* A fürt bővítményének Azure Resource Manager-alapú telepítése.
* A Extension Helm-diagramok életciklus-kezelése.

A szolgáltatás fogalmi áttekintése a [fürt bővítményeiben érhető el – Azure arc-kompatibilis Kubernetes-](conceptual-extensions.md) cikk.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Előfeltételek

- [Telepítse vagy frissítse az Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) -t a következő verzióra: >= 2.16.0.
- `connectedk8s` (verzió: >= 1.1.0) és `k8s-extension` (version >= 0.2.0) Azure CLI-bővítmények. Telepítse ezeket az Azure CLI-bővítményeket a következő parancsok futtatásával:
  
    ```azurecli
    az extension add --name connectedk8s
    az extension add --name k8s-extension
    ```
    
    Ha a `connectedk8s` és a `k8s-extension` bővítmény már telepítve van, a következő parancs használatával frissítheti azokat a legújabb verzióra:

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8s-extension
    ```

- Egy meglévő Azure arc-kompatibilis Kubernetes csatlakoztatott fürt.
    - Ha még nem csatlakoztatta a fürtöt, használja a gyors üzembe helyezési [útmutatót.](quickstart-connect-cluster.md)
    - [Frissítse az ügynököket](agent-upgrade.md#manually-upgrade-agents) a >= 1.1.0 verzióra.

## <a name="currently-available-extensions"></a>Jelenleg elérhető bővítmények

| Mellék | Leírás |
| --------- | ----------- |
| [Azure Monitor](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md?toc=/azure/azure-arc/kubernetes/toc.json) | A Kubernetes-fürtön üzembe helyezett számítási feladatok teljesítményének láthatóságát biztosítja. Gyűjti a memória-és CPU-kihasználtsági mérőszámokat a vezérlőkből, a csomópontokból és a tárolóból. |
| [Azure Defender](../../security-center/defender-for-kubernetes-azure-arc.md?toc=/azure/azure-arc/kubernetes/toc.json) | Gyűjti a naplózási adatokat a Kubernetes-fürt vezérlési sík csomópontjairól. Ajánlásokat és veszélyforrási riasztásokat biztosít az összegyűjtött adatok alapján. |

## <a name="usage-of-cluster-extensions"></a>A fürt bővítményeinek használata

### <a name="create-extensions-instance"></a>Bővítmény-példány létrehozása

Hozzon létre egy új bővítmény-példányt a paranccsal, és adja `k8s-extension create` meg a kötelező paraméterek értékét. Az alábbi parancs létrehoz egy Azure Monitor a containers Extension példányhoz az Azure arc-kompatibilis Kubernetes-fürtön:

```azurecli
az k8s-extension create --name azuremonitor-containers  --extension-type Microsoft.AzureMonitor.Containers --scope cluster --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

**Kimeneti**

```json
{
  "autoUpgradeMinorVersion": true,
  "configurationProtectedSettings": null,
  "configurationSettings": {
    "logAnalyticsWorkspaceResourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/defaultresourcegroup-eus/providers/microsoft.operationalinsights/workspaces/defaultworkspace-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-eus"
  },
  "creationTime": "2021-04-02T12:13:06.7534628+00:00",
  "errorInfo": {
    "code": null,
    "message": null
  },
  "extensionType": "microsoft.azuremonitor.containers",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/demo/providers/Microsoft.Kubernetes/connectedClusters/demo/providers/Microsoft.KubernetesConfiguration/extensions/azuremonitor-containers",
  "identity": null,
  "installState": "Pending",
  "lastModifiedTime": "2021-04-02T12:13:06.753463+00:00",
  "lastStatusTime": null,
  "name": "azuremonitor-containers",
  "releaseTrain": "Stable",
  "resourceGroup": "demo",
  "scope": {
    "cluster": {
      "releaseNamespace": "azuremonitor-containers"
    },
    "namespace": null
  },
  "statuses": [],
  "systemData": null,
  "type": "Microsoft.KubernetesConfiguration/extensions",
  "version": "2.8.2"
}
```

> [!NOTE]
> * A szolgáltatás nem tudja megőrizni a bizalmas adatokat több mint 48 óráig. Ha az Azure arc-kompatibilis Kubernetes-ügynökök több mint 48 órán keresztül nem rendelkeznek hálózati kapcsolattal, és nem tudja megállapítani, hogy létre kell-e hozni egy bővítményt a fürtön, akkor a bővítmény az `Failed` állapotra vált. Ha az `Failed` állapotban van, újra kell futtatnia `k8s-extension create` egy új bővítmény Azure-erőforrás létrehozásához.
> * * A tárolók Azure Monitor egy egypéldányos bővítmény (fürthöz csak egy szükséges). Mielőtt ugyanezeket a bővítményeket telepítené, törölje a Azure Monitor összes korábbi Helm-diagramjának telepítését a tárolók (bővítmények nélkül) használatával. A [Futtatás `az k8s-extension create` előtt kövesse a Helm-diagram törlésére ](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-optout-hybrid)vonatkozó utasításokat.

**Szükséges paraméterek**

| Paraméter neve | Leírás |
|----------------|------------|
| `--name` | A bővítmény példányának neve |
| `--extension-type` | A fürtön telepíteni kívánt bővítmény típusa. Például: Microsoft. AzureMonitor. containers, Microsoft. azuredefender. kubernetes | 
| `--scope` | A bővítmény telepítési hatóköre – `cluster` vagy `namespace` |
| `--cluster-name` | Annak az Azure arc-kompatibilis Kubernetes-erőforrásnak a neve, amelyen a bővítményt létre kell hozni |
| `--resource-group` | Az Azure arc-kompatibilis Kubernetes-erőforrást tartalmazó erőforráscsoport |
| `--cluster-type` | A fürt típusa, amelyre a bővítményt létre kell hozni. Csak a jelenlegi `connectedClusters` , amely az Azure arc-kompatibilis Kubernetes felel meg, elfogadott érték |

**Választható paraméterek**

| Paraméter neve | Leírás |
|--------------|------------|
| `--auto-upgrade-minor-version` | Logikai tulajdonság, amely megadja, hogy a bővítmény alverziójának frissítése automatikusan történik-e. Alapértelmezett: `true`.  Ha a paraméter értéke TRUE (igaz), a paraméter nem állítható be `version` , mert a verzió dinamikusan frissül. Ha a értékre van állítva `false` , a bővítmény nem lesz automatikusan frissítve a javítási verziók esetében is. |
| `--version` | A telepítendő bővítmény verziója (adott verzió a kiterjesztési példány rögzítéséhez). Nem adható meg, ha az automatikus frissítés-másodlagos verzió beállítása a következőre van beállítva: `true` . |
| `--configuration-settings` | A bővítmény számára a funkció működésének szabályozásához átadható beállítások. `key=value`A paramétereket a paraméter neve után szóközökkel elválasztott párokként kell átadni. Ha ezt a paramétert használja a parancs, akkor `--configuration-settings-file` nem használható ugyanabban a parancsban. |
| `--configuration-settings-file` | A konfigurációs beállításoknak a bővítménybe való átadásához használt kulcs érték párokkal rendelkező JSON-fájl elérési útja. Ha ezt a paramétert használja a parancs, akkor `--configuration-settings` nem használható ugyanabban a parancsban. |
| `--configuration-protected-settings` | Ezek a beállítások nem olvashatók `GET` be az API-hívásokkal vagy- `az k8s-extension show` parancsokkal, így a bizalmas beállítások átadására szolgálnak. `key=value`A paramétereket a paraméter neve után szóközökkel elválasztott párokként kell átadni. Ha ezt a paramétert használja a parancs, akkor `--configuration-protected-settings-file` nem használható ugyanabban a parancsban. |
| `--configuration-protected-settings-file` | A fontos érték párokkal rendelkező JSON-fájl elérési útja, amely a bizalmas beállítások kiterjesztésére való továbbításra szolgál. Ha ezt a paramétert használja a parancs, akkor `--configuration-protected-settings` nem használható ugyanabban a parancsban. |
| `--release-namespace` | Ez a paraméter azt a névteret jelzi, amelyen belül a kiadást létre kell hozni. Ez a paraméter csak akkor érvényes, ha `scope` a paraméter értéke `cluster` . |
| `--release-train` |  A bővítmények szerzője különböző kiadási szerelvényekben (például `Stable` , stb.) is közzéteheti a verziókat `Preview` . Ha a paraméter nincs explicit módon beállítva, `Stable` a rendszer az alapértelmezett értéket használja. Ez a paraméter nem használható, ha a `autoUpgradeMinorVersion` paraméter értéke `false` . |
| `--target-namespace` | Ez a paraméter azt a névteret jelzi, amelyen belül a rendszer létrehozza a kiadást. Az ehhez a bővítményi példányhoz létrehozott rendszerfiók engedélyei erre a névtérre lesznek korlátozva. Ez a paraméter csak akkor érvényes, ha a `scope` paraméter értéke `namespace` . |

### <a name="show-details-of-an-extension-instance"></a>Kiterjesztési példány részleteinek megjelenítése

Megtekintheti a jelenleg telepített, a (z) és a `k8s-extension show` (z) értékeit a kötelező paraméterek értékeinek átadásával:

```azurecli
az k8s-extension show --name azuremonitor-containers --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

**Kimeneti**

```json
{
  "autoUpgradeMinorVersion": true,
  "configurationProtectedSettings": null,
  "configurationSettings": {
    "logAnalyticsWorkspaceResourceID": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/defaultresourcegroup-eus/providers/microsoft.operationalinsights/workspaces/defaultworkspace-xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-eus"
  },
  "creationTime": "2021-04-02T12:13:06.7534628+00:00",
  "errorInfo": {
    "code": null,
    "message": null
  },
  "extensionType": "microsoft.azuremonitor.containers",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/demo/providers/Microsoft.Kubernetes/connectedClusters/demo/providers/Microsoft.KubernetesConfiguration/extensions/azuremonitor-containers",
  "identity": null,
  "installState": "Installed",
  "lastModifiedTime": "2021-04-02T12:13:06.753463+00:00",
  "lastStatusTime": "2021-04-02T12:13:49.636+00:00",
  "name": "azuremonitor-containers",
  "releaseTrain": "Stable",
  "resourceGroup": "demo",
  "scope": {
    "cluster": {
      "releaseNamespace": "azuremonitor-containers"
    },
    "namespace": null
  },
  "statuses": [],
  "systemData": null,
  "type": "Microsoft.KubernetesConfiguration/extensions",
  "version": "2.8.2"
}
```

### <a name="list-all-extensions-installed-on-the-cluster"></a>A fürtön telepített összes bővítmény felsorolása

A fürtön telepített összes bővítmény felsorolása `k8s-extension list` a kötelező paraméterek értékének megadásával.

```azurecli
az k8s-extension list --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

**Kimeneti**

```json
[
  {
    "autoUpgradeMinorVersion": true,
    "creationTime": "2020-09-15T02:26:03.5519523+00:00",
    "errorInfo": {
      "code": null,
      "message": null
    },
    "extensionType": "Microsoft.AzureMonitor.Containers",
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myRg/providers/Microsoft.Kubernetes/connectedClusters/myCluster/providers/Microsoft.KubernetesConfiguration/extensions/myExtInstanceName",
    "identity": null,
    "installState": "Pending",
    "lastModifiedTime": "2020-09-15T02:48:45.6469664+00:00",
    "lastStatusTime": null,
    "name": "myExtInstanceName",
    "releaseTrain": "Stable",
    "resourceGroup": "myRG",
    "scope": {
      "cluster": {
        "releaseNamespace": "myExtInstanceName1"
      }
    },
    "statuses": [],
    "type": "Microsoft.KubernetesConfiguration/extensions",
    "version": "0.1.0"
  },
  {
    "autoUpgradeMinorVersion": true,
    "creationTime": "2020-09-02T00:41:16.8005159+00:00",
    "errorInfo": {
      "code": null,
      "message": null
    },
    "extensionType": "microsoft.azuredefender.kubernetes",
    "id": "/subscriptions/0e849346-4343-582b-95a3-e40e6a648ae1/resourceGroups/myRg/providers/Microsoft.Kubernetes/connectedClusters/myCluster/providers/Microsoft.KubernetesConfiguration/extensions/defender",
    "identity": null,
    "installState": "Pending",
    "lastModifiedTime": "2020-09-02T00:41:16.8005162+00:00",
    "lastStatusTime": null,
    "name": "microsoft.azuredefender.kubernetes",
    "releaseTrain": "Stable",
    "resourceGroup": "myRg",
    "scope": {
      "cluster": {
        "releaseNamespace": "myExtInstanceName2"
      }
    },
    "type": "Microsoft.KubernetesConfiguration/extensions",
    "version": "0.1.0"
  }
]
```

### <a name="update-an-existing-extension-instance"></a>Meglévő bővítmény-példány frissítése

Frissítsen egy bővítmény-példányt egy fürtön a `k8s-extension update` használatával, és adja át a frissítendő értékeket.  Ez a parancs csak a `auto-upgrade-minor-version` , `release-train` , és `version` tulajdonságokat frissíti. Például:

- **A kiadási vonat frissítése:**

    ```azurecli
    az k8s-extension update --name azuremonitor-containers --cluster-type connectedClusters --cluster-name <clusterName> --resource-group <resourceGroupName> --release-train Preview
    ```

- **Kapcsolja ki az automatikus frissítést, és rögzítse a bővítmény példányát egy adott verzióra:**

    ```azurecli
    az k8s-extension update --name azuremonitor-containers --cluster-type connectedClusters --cluster-name <clusterName> --resource-group <resourceGroupName> --auto-upgrade-minor-version false --version 2.2.2
    ```

- **Az automatikus frissítés bekapcsolása a bővítmény példánya esetében:**

    ```azurecli
    az k8s-extension update --name azuremonitor-containers --cluster-type connectedClusters --cluster-name <clusterName> --resource-group <resourceGroupName> --auto-upgrade-minor-version true
    ```

> [!NOTE]
> A `version` paraméter csak akkor állítható be, ha a értéke `--auto-upgrade-minor-version` `false` .

### <a name="delete-extension-instance"></a>Bővítmény példányának törlése

Törölje a kiterjesztésű példányt egy fürtön a következővel `k8s-extension delete` , amely a kötelező paraméterek értékeit adja meg.

```azurecli
az k8s-extension delete --name azuremonitor-containers --cluster-name <clusterName> --resource-group <resourceGroupName> --cluster-type connectedClusters
```

>[!NOTE]
> Az ezt a bővítményt jelképező Azure-erőforrás azonnal törölve lesz. Az ezzel a bővítménnyel társított fürtön lévő Helm kiadás csak akkor törlődik, ha a Kubernetes-fürtön futó ügynökök hálózati kapcsolattal rendelkeznek, és a kívánt állapot beolvasásához ismét elérhetők az Azure-szolgáltatások.


## <a name="next-steps"></a>Következő lépések

További információ az Azure arc-kompatibilis Kubernetes jelenleg elérhető fürtcsomópontokon:
> [!div class="nextstepaction"]
> [Azure monitor](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md?toc=/azure/azure-arc/kubernetes/toc.json) 
>  [Azure Defender](../../security-center/defender-for-kubernetes-azure-arc.md?toc=/azure/azure-arc/kubernetes/toc.json)