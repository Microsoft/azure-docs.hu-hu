---
title: Azure arc-kompatibilis Kubernetes-fürt összekapcsolása (előzetes verzió)
services: azure-arc
ms.service: azure-arc
ms.date: 02/09/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Azure arc-kompatibilis Kubernetes-fürt összekapcsolása az Azure Arcmal
keywords: Kubernetes, arc, Azure, K8s, tárolók
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: e68eccf998592aa7d1ebfea51e4ca66d577b3c7f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100390555"
---
# <a name="connect-an-azure-arc-enabled-kubernetes-cluster-preview"></a>Azure arc-kompatibilis Kubernetes-fürt összekapcsolása (előzetes verzió)

Ez a cikk a felhőalapú natív számítástechnikai alaprendszer (CNCF) által tanúsított Kubernetes-fürtök (például az Azure-beli, az AK-motor, az Azure Stack hub, a GKE, a EKS és a VMware vSphere-fürt Azure-ba való összekapcsolásának folyamatát ismerteti.

## <a name="before-you-begin"></a>Előkészületek

Győződjön meg arról, hogy előkészítette a következő előfeltételeket:

* Egy felhasználható Kubernetes-fürt. Ha nem rendelkezik meglévő Kubernetes-fürttel, a következő útmutatók egyikével hozhat létre egy tesztelési fürtöt:
  * Hozzon létre egy Kubernetes [-fürtöt a Kubernetes használatával a Docker-ben (Kind)](https://kind.sigs.k8s.io/).
  * Hozzon létre egy Kubernetes-fürtöt a Docker használatával [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) vagy [Windows rendszerhez](https://docs.docker.com/docker-for-windows/#kubernetes).
* Kubeconfig-fájl, amely a fürt és a fürt rendszergazdai szerepkörének elérésére használható az arc-kompatibilis Kubernetes-ügynökök telepítéséhez.
* A (z) és a (z) paranccsal használt felhasználónak vagy szolgáltatásnak " `az login` `az connectedk8s connect` READ" és "Write" engedélyekkel kell rendelkeznie a "Microsoft. Kubernetes/connectedclusters" erőforrástípus számára. A "Kubernetes-fürt – Azure arc bevezetése" szerepkör rendelkezik ezekkel az engedélyekkel, és a felhasználó vagy az egyszerű szolgáltatás szerepkör-hozzárendeléseihez is használható.
* 3. Helm a fürt bevezetéséhez connectedk8s-bővítmény használatával. [Telepítse a Helm 3 legújabb kiadását](https://helm.sh/docs/intro/install) , hogy megfeleljen ennek a követelménynek.
* Azure CLI-verzió 2.15 + az Azure arc-kompatibilis Kubernetes CLI-bővítményeinek telepítéséhez. [Telepítse az Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) -t vagy frissítsen a legújabb verzióra.
* Az arc-kompatibilis Kubernetes CLI-bővítményeinek telepítése:
  
  * Telepítse a `connectedk8s` bővítményt, amely segít a Kubernetes-fürtök az Azure-hoz való összekapcsolásában:
  
  ```azurecli
  az extension add --name connectedk8s
  ```
  
   * A `k8sconfiguration` bővítmény telepítése:
  
  ```azurecli
  az extension add --name k8sconfiguration
  ```

  * Ha később szeretné frissíteni ezeket a bővítményeket, futtassa a következő parancsokat:
  
  ```azurecli
  az extension update --name connectedk8s
  az extension update --name k8sconfiguration
  ```

## <a name="supported-regions"></a>Támogatott régiók

* USA keleti régiója
* Nyugat-Európa

## <a name="network-requirements"></a>A hálózatra vonatkozó követelmények

Az Azure arc-ügynökök a következő protokollok/portok/kimenő URL-címek működéséhez szükségesek:

* TCP a 443-as porton: `https://:443`
* TCP a 9418-as porton: `git://:9418`

| Végpont (DNS)                                                                                               | Description                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `https://management.azure.com`                                                                                 | Ahhoz szükséges, hogy az ügynök csatlakozhasson az Azure-hoz, és regisztrálja a fürtöt.                                                        |
| `https://eastus.dp.kubernetesconfiguration.azure.com`, `https://westeurope.dp.kubernetesconfiguration.azure.com` | Adatsík-végpont az ügynök számára az állapot leküldéséhez és a konfigurációs adatok beolvasásához.                                      |
| `https://login.microsoftonline.com`                                                                            | Azure Resource Manager tokenek beolvasásához és frissítéséhez szükséges.                                                                                    |
| `https://mcr.microsoft.com`                                                                            | A tároló lemezképének lekéréséhez szükséges az Azure arc-ügynökökhöz.                                                                  |
| `https://eus.his.arc.azure.com`, `https://weu.his.arc.azure.com`                                                                            |  A rendszer által hozzárendelt felügyelt azonosító tanúsítványok lekéréséhez szükséges.                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Regisztrálja a két szolgáltatót az Azure arc-kompatibilis Kubernetes:

```console
az provider register --namespace Microsoft.Kubernetes

az provider register --namespace Microsoft.KubernetesConfiguration
```

A regisztráció aszinkron folyamat, és körülbelül 10 percet is igénybe vehet. A következő parancsokkal figyelheti a regisztrációs folyamatot:

```console
az provider show -n Microsoft.Kubernetes -o table
```

```console
az provider show -n Microsoft.KubernetesConfiguration -o table
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Egy erőforráscsoport használatával tárolhatja a fürt metaadatait.

Először hozzon létre egy erőforráscsoportot a csatlakoztatott fürterőforrás tárolásához.

```console
az group create --name AzureArcTest -l EastUS -o table
```

**Kimeneti**

```console
Location    Name
----------  ------------
eastus      AzureArcTest
```

## <a name="connect-a-cluster"></a>Fürt összekötése

Ezután a következővel fogunk csatlakozni a Kubernetes-fürthöz az Azure-hoz `az connectedk8s connect` :

1. Ellenőrizze a Kubernetes-fürthöz való csatlakozást a következők egyikével:
   * `KUBECONFIG`
   * `~/.kube/config`
   * `--kube-config`
1. Azure arc-ügynökök üzembe helyezése a Kubernetes a Helm 3 használatával a `azure-arc` névtérben:

    ```console
    az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
    ```

**Kimeneti**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Helm release deployment succeeded

{
  "aadProfile": {
    "clientAppId": "",
    "serverAppId": "",
    "tenantId": ""
  },
  "agentPublicKeyCertificate": "...",
  "agentVersion": "0.1.0",
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1",
  "identity": {
    "principalId": null,
    "tenantId": null,
    "type": "None"
  },
  "kubernetesVersion": "v1.15.0",
  "location": "eastus",
  "name": "AzureArcTest1",
  "resourceGroup": "AzureArcTest",
  "tags": {},
  "totalNodeCount": 1,
  "type": "Microsoft.Kubernetes/connectedClusters"
}
```

## <a name="verify-connected-cluster"></a>Csatlakoztatott fürt ellenőrzése

A csatlakoztatott fürtök listázásához használja az alábbi parancsot:

```console
az connectedk8s list -g AzureArcTest -o table
```

**Kimeneti**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
```

Ezt az erőforrást a [Azure Portal](https://portal.azure.com/)is megtekintheti. Nyissa meg a portált a böngészőben, és navigáljon az erőforráscsoporthoz és az Azure arc-kompatibilis Kubernetes-erőforráshoz a parancsban korábban használt erőforrás neve és erőforráscsoport-név bemenete alapján `az connectedk8s connect` .  
> [!NOTE]
> A fürt bevezetését követően 5 – 10 percet vesz igénybe, hogy a fürt metaadatai (a fürt verziója, az ügynök verziója, a csomópontok száma stb.) az Azure arc-kompatibilis Kubernetes-erőforrás áttekintés lapján legyenek felszínre Azure Portal.

## <a name="connect-using-an-outbound-proxy-server"></a>Csatlakozás kimenő proxykiszolgáló használatával

Ha a fürt egy kimenő proxykiszolgáló mögött van, az Azure CLI és az arc-kompatibilis Kubernetes-ügynököknek a kimenő proxykiszolgálón keresztül kell átirányítani a kéréseiket:

1. Győződjön meg arról, hogy a `connectedk8s` bővítmény telepített verziója telepítve van a gépen:

    ```console
    az -v
    ```

    `connectedk8s`A kimenő proxyval rendelkező ügynökök beállításához a 0.2.5 + kiterjesztésű verzióra van szükség. Ha a 0.2.3 vagy régebbi verziója van telepítve a gépen, kövesse a [frissítés lépéseit](#before-you-begin) a bővítmény legújabb verziójának lekéréséhez a gépen.

2. Állítsa be az Azure CLI-hez szükséges környezeti változókat a kimenő proxykiszolgáló használatára:

    * Ha bash-et használ, futtassa a következő parancsot a megfelelő értékekkel:

        ```bash
        export HTTP_PROXY=<proxy-server-ip-address>:<port>
        export HTTPS_PROXY=<proxy-server-ip-address>:<port>
        export NO_PROXY=<cluster-apiserver-ip-address>:<port>
        ```

    * Ha a PowerShellt használja, futtassa a következő parancsot a megfelelő értékekkel:

        ```powershell
        $Env:HTTP_PROXY = "<proxy-server-ip-address>:<port>"
        $Env:HTTPS_PROXY = "<proxy-server-ip-address>:<port>"
        $Env:NO_PROXY = "<cluster-apiserver-ip-address>:<port>"
        ```

3. Futtassa a csatlakozás parancsot a megadott proxy-paraméterekkel:

    ```console
    az connectedk8s connect -n <cluster-name> -g <resource-group> --proxy-https https://<proxy-server-ip-address>:<port> --proxy-http http://<proxy-server-ip-address>:<port> --proxy-skip-range <excludedIP>,<excludedCIDR> --proxy-cert <path-to-cert-file>
    ```

> [!NOTE]
> * `excludedCIDR`A alatt a `--proxy-skip-range` következő megadása fontos, hogy a fürtön belüli kommunikáció ne legyen megszakítva az ügynökök esetében.
> * A `--proxy-http` `--proxy-https` és a a `--proxy-skip-range` legtöbb kimenő proxy esetében várhatóan `--proxy-cert` csak akkor szükséges, ha a proxy megbízható tanúsítványait az ügynök hüvelyének megbízható tanúsítványtárolóba kell befecskendezni.
> * A fenti proxy-specifikációt jelenleg csak az ív-ügynökökre alkalmazza a rendszer, a sourceControlConfiguration használt Flux-hüvelyek esetében nem. Az arc-kompatibilis Kubernetes csapata aktívan dolgozik ezen a szolgáltatáson, és hamarosan elérhető lesz.

## <a name="azure-arc-agents-for-kubernetes"></a>Azure arc-ügynökök a Kubernetes

Az Azure arc-kompatibilis Kubernetes üzembe helyez néhány operátort a `azure-arc` névtérben. Ezek a központi telepítések és hüvelyek a következővel tekinthetők meg:

```console
kubectl -n azure-arc get deployments,pods
```

**Kimeneti**

```console
NAME                                        READY      UP-TO-DATE  AVAILABLE  AGE
deployment.apps/cluster-metadata-operator     1/1             1        1      16h
deployment.apps/clusteridentityoperator       1/1             1        1      16h
deployment.apps/config-agent                  1/1             1        1      16h
deployment.apps/controller-manager            1/1             1        1      16h
deployment.apps/flux-logs-agent               1/1             1        1      16h
deployment.apps/metrics-agent                 1/1             1        1      16h
deployment.apps/resource-sync-agent           1/1             1        1      16h

NAME                                           READY    STATUS   RESTART AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

Az Azure arc-kompatibilis Kubernetes néhány ügynököt (operátort) tartalmaz, amelyek a névtérben üzembe helyezett fürtön futnak `azure-arc` .

| Ügynökök (operátorok)                                                                                               | Description                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `deployment.apps/config-agent`                                                                                 | Figyeli a csatlakoztatott fürtöt a forrás-ellenőrzési konfigurációs erőforrásokon a fürtön, és frissíti a megfelelőségi állapotot.                                                        |
| `deployment.apps/controller-manager` | Az Azure arc-összetevők közötti interakciókat kezelő operátorok.                                      |
| `deployment.apps/metrics-agent`                                                                            | Más ív-ügynökök teljesítmény-metrikáit gyűjti.                                                                                    |
| `deployment.apps/cluster-metadata-operator`                                                                            | A fürt metaadatait, például a fürt verzióját, a csomópontok darabszámát és az Azure arc-ügynök verzióját gyűjti.                                                                  |
| `deployment.apps/resource-sync-agent`                                                                            |  Szinkronizálja a fent említett fürt metaadatait az Azure-ba.                                                                  |
| `deployment.apps/clusteridentityoperator`                                                                            |  Az Azure arc-kompatibilis Kubernetes jelenleg támogatja a rendszer által hozzárendelt identitást. `clusteridentityoperator` karbantartja a más ügynökök által az Azure-nal folytatott kommunikációhoz használt felügyelt szolgáltatás-identitás (MSI) tanúsítványát.                                                                  |
| `deployment.apps/flux-logs-agent`                                                                            |  Gyűjti a naplókat a verziókövetés konfigurációjának részeként üzembe helyezett Flux-kezelők számára.                                                                  |

## <a name="delete-a-connected-cluster"></a>Csatlakoztatott fürt törlése

`Microsoft.Kubernetes/connectedcluster`Az Azure CLI vagy a Azure Portal használatával törölheti az erőforrásokat.


* **Törlés az Azure CLI használatával**: az Azure arc-kompatibilis Kubernetes-erőforrás törlésének elindításához használja az alábbi Azure CLI-parancsot.
  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```
  Ez a parancs eltávolítja az `Microsoft.Kubernetes/connectedCluster` erőforrást és az összes kapcsolódó `sourcecontrolconfiguration` erőforrást az Azure-ban. Az Azure CLI használatával `helm uninstall` eltávolíthatja a fürtön futó ügynököket is.

* **Törlés Azure Portal**: az Azure arc-kompatibilis Kubernetes-erőforrás törlése a Azure Portal törli az `Microsoft.Kubernetes/connectedcluster` erőforrást és a hozzá tartozó összes `sourcecontrolconfiguration` erőforrást az Azure-ban, de *nem* távolítja el a fürtön futó ügynököket. 

  A fürtön futó ügynökök eltávolításához futtassa a következő parancsot:

  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```

## <a name="next-steps"></a>Következő lépések

* [GitOps használata csatlakoztatott fürtben](./use-gitops-connected-cluster.md)
* [A fürt konfigurációjának szabályozása Azure Policy használatával](./use-azure-policy.md)
