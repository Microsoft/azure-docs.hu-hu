---
title: 'Rövid útmutató: Meglévő Kubernetes-fürt csatlakoztatása Azure Arc'
description: Ebből a rövid útmutatóból megtudhatja, hogyan csatlakoztathat Azure Arc kubernetes-fürtöt.
author: mgoedtel
ms.author: magoedte
ms.service: azure-arc
ms.topic: quickstart
ms.date: 03/03/2021
ms.custom: template-quickstart, references_regions, devx-track-azurecli
keywords: Kubernetes, Arc, Azure, fürt
ms.openlocfilehash: 21ec5000ed7ef9df1805fa6ec43e20efc0f82182
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481242"
---
# <a name="quickstart-connect-an-existing-kubernetes-cluster-to-azure-arc"></a>Rövid útmutató: Meglévő Kubernetes-fürt csatlakoztatása Azure Arc 

Ebben a rövid útmutatóban az engedélyezett Kubernetes Azure Arc előnyeit fogjuk kihozni, és egy meglévő Kubernetes-fürtöt csatlakoztatunk a Azure Arc. A fürtök és a fürtök Azure Arc fogalmi bemutatójért tekintse meg a Azure Arc [Kubernetes-ügynök](./conceptual-agent-architecture.md)architektúráját bemutató cikket.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

* Egy futó Kubernetes-fürt. Ha még nincs fürte, az alábbi lehetőségek egyikének használatával hozhat létre fürtöt:
    * [Kubernetes a Dockerben (KIND)](https://kind.sigs.k8s.io/)
    * Kubernetes-fürt létrehozása Mac [](https://docs.docker.com/docker-for-mac/#kubernetes) vagy [Windows](https://docs.docker.com/docker-for-windows/#kubernetes) rendszerű Docker használatával
    * Saját felügyelt Kubernetes-fürt a [Cluster API használatával](https://cluster-api.sigs.k8s.io/user/quick-start.html)

    >[!NOTE]
    > A fürtnek legalább egy operációsrendszer-csomóponttal és architektúratípussal kell `linux/amd64` rendelkezik. A csak csomópontokkal `linux/arm64` lévő fürtök még nem támogatottak.
    
* A `kubeconfig` fürtre mutató fájl és környezet.
* Olvasási és Írási engedélyek a Azure Arc Kubernetes-erőforrástípuson ( `Microsoft.Kubernetes/connectedClusters` ).

* Telepítse [a Helm 3 legújabb kiadását.](https://helm.sh/docs/intro/install)

- [Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli) vagy frissítése >2.16.0-s verzióra
* Telepítse az `connectedk8s` Azure CLI-bővítmény >= 1.0.0:
  
  ```azurecli
  az extension add --name connectedk8s
  ```

>[!TIP]
> Ha a bővítmény már telepítve van, frissítse a legújabb verzióra `connectedk8s` a következő paranccsal: `az extension update --name connectedk8s`


>[!NOTE]
>Az engedélyezett Kubernetes Azure Arc által támogatott régiók listája itt [található.](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)

>[!NOTE]
> Ha egyéni helyeket szeretne használni a fürtön, akkor az USA keleti régiója vagy Nyugat-Európa régiói segítségével csatlakoztatja a fürtöt, mivel az egyéni helyek jelenleg csak ezekben a régiókban érhetők el. Az összes Azure Arc kubernetes-funkciók a fent felsorolt összes régióban elérhetők.

## <a name="meet-network-requirements"></a>A hálózati követelményeknek való megfelelés

>[!IMPORTANT]
>Azure Arc ügynökök működéséhez a következő protokollokra/portokra/kimenő URL-címekre van szükség:
>* TCP a 443-as porton: `https://:443`
>* TCP a 9418-as porton: `git://:9418`
  
| Végpont (DNS) | Leírás |  
| ----------------- | ------------- |  
| `https://management.azure.com`                                                                                 | Ahhoz szükséges, hogy az ügynök csatlakozzon az Azure-hoz, és regisztrálja a fürtöt.                                                        |  
| `https://<region>.dp.kubernetesconfiguration.azure.com` | Adatsík végpontja az ügynök számára az állapot leküldéséhez és a konfigurációs adatok lekéréséhez.                                      |  
| `https://login.microsoftonline.com`                                                                            | A jogkivonatok beolvasásához és Azure Resource Manager szükséges.                                                                                    |  
| `https://mcr.microsoft.com`                                                                            | Tároló rendszerképeket kell lekérteni Azure Arc ügynökök számára.                                                                  |  
| `https://eus.his.arc.azure.com`, `https://weu.his.arc.azure.com`, `https://wcus.his.arc.azure.com`, `https://scus.his.arc.azure.com`, `https://sea.his.arc.azure.com`, `https://uks.his.arc.azure.com`, `https://wus2.his.arc.azure.com`, `https://ae.his.arc.azure.com`, `https://eus2.his.arc.azure.com`, `https://ne.his.arc.azure.com` |  A rendszer által hozzárendelt felügyeltszolgáltatás-identitási (MSI-) tanúsítványok lekért tanúsítványokhoz szükséges.                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>A két szolgáltató regisztrálása Azure Arc Kuberneteshez

1. Írja be a következő parancsokat:
    ```azurecli
    az provider register --namespace Microsoft.Kubernetes
    az provider register --namespace Microsoft.KubernetesConfiguration
    az provider register --namespace Microsoft.ExtendedLocation
    ```
2. Figyelje a regisztrációs folyamatot. A regisztráció akár 10 percig is eltarthat.
    ```azurecli
    az provider show -n Microsoft.Kubernetes -o table
    az provider show -n Microsoft.KubernetesConfiguration -o table
    az provider show -n Microsoft.ExtendedLocation -o table
    ```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot:  

```console
az group create --name AzureArcTest -l EastUS -o table
```

```output
Location    Name
----------  ------------
eastus      AzureArcTest
```

## <a name="connect-an-existing-kubernetes-cluster"></a>Meglévő Kubernetes-fürt csatlakoztatása

1. Csatlakoztassa a Kubernetes-fürtöt Azure Arc a következő paranccsal:
    ```console
    az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
    ```

    ```output
    Helm release deployment succeeded

    {
      "aadProfile": {
        "clientAppId": "",
        "serverAppId": "",
        "tenantId": ""
      },
      "agentPublicKeyCertificate": "xxxxxxxxxxxxxxxxxxx",
      "agentVersion": null,
      "connectivityStatus": "Connecting",
      "distribution": "gke",
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1",
      "identity": {
        "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "type": "SystemAssigned"
      },
      "infrastructure": "gcp",
      "kubernetesVersion": null,
      "lastConnectivityTime": null,
      "location": "eastus",
      "managedIdentityCertificateExpirationTime": null,
      "name": "AzureArcTest1",
      "offering": null,
      "provisioningState": "Succeeded",
      "resourceGroup": "AzureArcTest",
      "tags": {},
      "totalCoreCount": null,
      "totalNodeCount": null,
      "type": "Microsoft.Kubernetes/connectedClusters"
    }
    ```

> [!TIP]
> A fenti parancs a megadott helyparaméter nélkül hozza Azure Arc kubernetes-erőforrást az erőforráscsoporttal azonos helyen. Az engedélyezett Azure Arc Kubernetes-erőforrás másik helyen való létrehozásához adja meg a vagy a parancsot `--location <region>` `-l <region>` a parancs `az connectedk8s connect` futtatásakor.

## <a name="verify-cluster-connection"></a>Fürtkapcsolat ellenőrzése

Tekintse meg a csatlakoztatott fürtök listáját a következő paranccsal:  

```console
az connectedk8s list -g AzureArcTest -o table
```

```output
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
```

> [!NOTE]
> A fürt létrehozása után körülbelül 5–10 percet vesz igénybe, hogy a fürt metaadatai (fürtverzió, ügynökverzió, csomópontok száma stb.) a fürt Azure Arc-kompatibilis Kubernetes-erőforrásának áttekintő oldalán ömlének Azure Portal.

## <a name="connect-using-an-outbound-proxy-server"></a>Csatlakozás kimenő proxykiszolgálóval

Ha a fürt kimenő proxykiszolgáló mögött található, az Azure CLI-nek és a Azure Arc-kompatibilis Kubernetes-ügynököknek a kimenő proxykiszolgálón keresztül kell irányítanunk a kéréseiket. 


1. Állítsa be az Azure CLI-hez szükséges környezeti változókat a kimenő proxykiszolgáló használatára:

    * Bash használata esetén futtassa a következő parancsot a megfelelő értékekkel:

        ```bash
        export HTTP_PROXY=<proxy-server-ip-address>:<port>
        export HTTPS_PROXY=<proxy-server-ip-address>:<port>
        export NO_PROXY=<cluster-apiserver-ip-address>:<port>
        ```

    * Ha PowerShellt használ, futtassa a következő parancsot a megfelelő értékekkel:

        ```powershell
        $Env:HTTP_PROXY = "<proxy-server-ip-address>:<port>"
        $Env:HTTPS_PROXY = "<proxy-server-ip-address>:<port>"
        $Env:NO_PROXY = "<cluster-apiserver-ip-address>:<port>"
        ```

2. Futtassa a connect parancsot a megadott proxyparaméterekkel:

    ```console
    az connectedk8s connect -n <cluster-name> -g <resource-group> --proxy-https https://<proxy-server-ip-address>:<port> --proxy-http http://<proxy-server-ip-address>:<port> --proxy-skip-range <excludedIP>,<excludedCIDR> --proxy-cert <path-to-cert-file>
    ```

> [!NOTE]
> * A alatt adja meg, hogy a fürtön belül ne `excludedCIDR` hibásodja meg az ügynökök `--proxy-skip-range` kommunikációját.
> * `--proxy-http`, `--proxy-https` és a a legtöbb kimenő `--proxy-skip-range` proxykörnyezetben várható. `--proxy-cert` A *csak akkor* szükséges, ha a proxy által várt megbízható tanúsítványokat kell az ügynökpodok megbízható tanúsítványtárolójában tárolni.

## <a name="view-azure-arc-agents-for-kubernetes"></a>Kubernetes-Azure Arc-ügynökök megtekintése

Azure Arc kubernetes-kompatibilis Kubernetes üzembe helyez néhány operátort a `azure-arc` névtérben. 

1. Tekintse meg ezeket az üzemelő példányokat és podokat a következővel:

    ```console
    kubectl -n azure-arc get deployments,pods
    ```

1. Ellenőrizze, hogy az összes pod állapotban `Running` van-e.

    ```output
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

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A következő paranccsal Azure Arc törölheti az engedélyezett Kubernetes-erőforrást, a társított konfigurációs erőforrásokat és a fürtön futó ügynököket az Azure CLI használatával: 

```azurecli
az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
```

>[!NOTE]
>Az Azure Arc Kubernetes-erőforrás Azure Portal törlésével eltávolítja a társított konfigurációs  erőforrásokat, de nem távolítja el a fürtön futó ügynököket. Az ajánlott eljárás az, hogy Azure Arc kubernetes-erőforrást a használatával törli `az connectedk8s delete` az Azure Portal.

## <a name="next-steps"></a>Következő lépések

A következő cikkből megtudhatja, hogyan helyezhet üzembe konfigurációkat a csatlakoztatott Kubernetes-fürtön a GitOps használatával.
> [!div class="nextstepaction"]
> [Konfigurációk üzembe helyezése a GitOps használatával](tutorial-use-gitops-connected-cluster.md)
