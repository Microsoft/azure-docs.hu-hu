---
title: 'Gyors útmutató: meglévő Kubernetes-fürt összekötése az Azure-ív használatával'
description: Ebből a rövid útmutatóból megtudhatja, hogyan csatlakozhat egy Azure arc-kompatibilis Kubernetes-fürthöz.
author: mgoedtel
ms.author: magoedte
ms.service: azure-arc
ms.topic: quickstart
ms.date: 03/03/2021
ms.custom: template-quickstart, references_regions
keywords: Kubernetes, arc, Azure, fürt
ms.openlocfilehash: b4cbd45f8478674c7c6bacc50f068bc0ec691a14
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449919"
---
# <a name="quickstart-connect-an-existing-kubernetes-cluster-to-azure-arc"></a>Gyors útmutató: meglévő Kubernetes-fürt összekötése az Azure-ív használatával 

Ebben a rövid útmutatóban kihasználjuk az Azure arc-kompatibilis Kubernetes előnyeit, és egy meglévő Kubernetes-fürtöt kell összekapcsolni az Azure-beli ív használatával. A fürtök Azure arc-hez való csatlakoztatásával kapcsolatos fogalmakat az [Azure arc-kompatibilis Kubernetes-ügynök architektúrája című cikkben](./conceptual-agent-architecture.md)találja.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

* Egy felhasználható Kubernetes-fürt. Ha még nem rendelkezik ilyennel, létrehozhat egy fürtöt a következő lehetőségek egyikének használatával:
    * [Kubernetes a Docker-ben (KIND)](https://kind.sigs.k8s.io/)
    * Kubernetes-fürt létrehozása a Docker használatával [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) vagy [Windows rendszerhez](https://docs.docker.com/docker-for-windows/#kubernetes)
    * Önállóan felügyelt Kubernetes-fürt a [cluster API](https://cluster-api.sigs.k8s.io/user/quick-start.html) használatával

    >[!NOTE]
    > A fürtnek az operációs rendszer és az architektúra típusának legalább egy csomópontjának kell lennie `linux/amd64` . A csak `linux/arm64` csomópontokkal rendelkező fürtök még nem támogatottak.
    
* A `kubeconfig` fürtre mutató fájl és környezet.
* "READ" és "Write" engedély az Azure arc-kompatibilis Kubernetes erőforrástípus ( `Microsoft.Kubernetes/connectedClusters` ) számára.

* Telepítse a [Helm 3 legújabb kiadását](https://helm.sh/docs/intro/install).

- Az [Azure CLI telepítése vagy frissítése](https://docs.microsoft.com/cli/azure/install-azure-cli) a következő verzióra: >= 2.16.0
* Telepítse az `connectedk8s` Azure CLI-bővítményt >= 1.0.0 verzióra:
  
  ```azurecli
  az extension add --name connectedk8s
  ```

>[!TIP]
> Ha a `connectedk8s` bővítmény már telepítve van, frissítse a legújabb verzióra a következő parancs használatával: `az extension update --name connectedk8s`


>[!NOTE]
>Az Azure arc-kompatibilis Kubernetes által támogatott régiók listája [itt](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)található.

>[!NOTE]
> Ha egyéni helyekkel szeretné használni a fürtöt, akkor az USA keleti régiójában vagy a Nyugat-európai régióban a fürt egyéni helyként való csatlakoztatásához csak az alábbi régiókban érhető el. Az összes többi Azure arc-kompatibilis Kubernetes funkció a fent felsorolt összes régióban elérhető.

## <a name="meet-network-requirements"></a>A hálózati követelmények teljesítése

>[!IMPORTANT]
>Az Azure arc-ügynökök a következő protokollok/portok/kimenő URL-címek működéséhez szükségesek:
>* TCP a 443-as porton: `https://:443`
>* TCP a 9418-as porton: `git://:9418`
  
| Végpont (DNS) | Leírás |  
| ----------------- | ------------- |  
| `https://management.azure.com`                                                                                 | Ahhoz szükséges, hogy az ügynök csatlakozhasson az Azure-hoz, és regisztrálja a fürtöt.                                                        |  
| `https://<region>.dp.kubernetesconfiguration.azure.com` | Adatsík-végpont az ügynök számára az állapot leküldéséhez és a konfigurációs adatok beolvasásához.                                      |  
| `https://login.microsoftonline.com`                                                                            | Azure Resource Manager tokenek beolvasásához és frissítéséhez szükséges.                                                                                    |  
| `https://mcr.microsoft.com`                                                                            | A tároló lemezképének lekéréséhez szükséges az Azure arc-ügynökökhöz.                                                                  |  
| `https://eus.his.arc.azure.com`, `https://weu.his.arc.azure.com`, `https://wcus.his.arc.azure.com`, `https://scus.his.arc.azure.com`, `https://sea.his.arc.azure.com`, `https://uks.his.arc.azure.com`, `https://wus2.his.arc.azure.com`, `https://ae.his.arc.azure.com`, `https://eus2.his.arc.azure.com`, `https://ne.his.arc.azure.com` |  A rendszer által hozzárendelt Managed Service Identity (MSI) tanúsítványok lekéréséhez szükséges.                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>A két szolgáltató regisztrálása az Azure arc-kompatibilis Kubernetes

1. Írja be a következő parancsokat:
    ```azurecli
    az provider register --namespace Microsoft.Kubernetes
    az provider register --namespace Microsoft.KubernetesConfiguration
    az provider register --namespace Microsoft.ExtendedLocation
    ```
2. Figyelje a regisztrációs folyamatot. A regisztráció akár 10 percet is igénybe vehet.
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

## <a name="connect-an-existing-kubernetes-cluster"></a>Meglévő Kubernetes-fürt összekötése

1. A következő paranccsal kapcsolja össze a Kubernetes-fürtöt az Azure arc használatával:
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
> A megadott Location paraméter nélkül a fenti parancs létrehozza az Azure arc-kompatibilis Kubernetes-erőforrást az erőforráscsoporthoz megegyező helyen. Ha egy másik helyen szeretné létrehozni az Azure arc-kompatibilis Kubernetes-erőforrást `--location <region>` , `-l <region>` akkor a vagy a parancsot kell megadnia a parancs futtatásakor `az connectedk8s connect` .

## <a name="verify-cluster-connection"></a>Fürt kapcsolatainak ellenőrzése

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
> A fürt bevezetését követően 5 – 10 percet vesz igénybe, hogy a fürt metaadatai (a fürt verziója, az ügynök verziója, a csomópontok száma stb.) az Azure arc-kompatibilis Kubernetes-erőforrás áttekintés lapján legyenek felszínre Azure Portal.

## <a name="connect-using-an-outbound-proxy-server"></a>Csatlakozás kimenő proxykiszolgáló használatával

Ha a fürt egy kimenő proxykiszolgáló mögött található, az Azure CLI és az Azure arc használatára képes Kubernetes-ügynököknek a kimenő proxykiszolgálón keresztül kell átirányítani a kéréseiket. 


1. Állítsa be az Azure CLI-hez szükséges környezeti változókat a kimenő proxykiszolgáló használatára:

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

2. Futtassa a csatlakozás parancsot a megadott proxy-paraméterekkel:

    ```console
    az connectedk8s connect -n <cluster-name> -g <resource-group> --proxy-https https://<proxy-server-ip-address>:<port> --proxy-http http://<proxy-server-ip-address>:<port> --proxy-skip-range <excludedIP>,<excludedCIDR> --proxy-cert <path-to-cert-file>
    ```

> [!NOTE]
> * Itt adhatja meg `excludedCIDR` `--proxy-skip-range` , hogy a fürtön belüli kommunikáció ne legyen megszakítva az ügynököknél.
> * `--proxy-http`a, a és a a `--proxy-https` `--proxy-skip-range` legtöbb kimenő proxy környezet esetében várható. `--proxy-cert`*csak* akkor szükséges, ha olyan megbízható tanúsítványokat kell beadnia, amelyeket a proxy az ügynök hüvelyének megbízható tanúsítványtárolójában várt.

## <a name="view-azure-arc-agents-for-kubernetes"></a>Azure arc-ügynökök megtekintése a Kubernetes

Az Azure arc-kompatibilis Kubernetes üzembe helyez néhány operátort a `azure-arc` névtérben. 

1. A következő központi telepítések és hüvelyek megtekintése a használatával:

    ```console
    kubectl -n azure-arc get deployments,pods
    ```

1. Ellenőrizze, hogy az összes hüvely `Running` állapota állapotban van-e.

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

A következő paranccsal törölheti az Azure arc-kompatibilis Kubernetes-erőforrást, a hozzá tartozó konfigurációs erőforrásokat, *valamint* a fürtön futó összes ügynököt az Azure CLI használatával:

```azurecli
az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
```

>[!NOTE]
>Az Azure arc-kompatibilis Kubernetes-erőforrás törlése Azure Portal eltávolítja az összes társított konfigurációs erőforrást, de *nem* távolítja el a fürtön futó ügynököket. Ajánlott eljárás az Azure arc-kompatibilis Kubernetes-erőforrás törlése a `az connectedk8s delete` Azure Portal helyett.

## <a name="next-steps"></a>Következő lépések

A következő cikkből megtudhatja, hogyan helyezhet üzembe konfigurációkat a csatlakoztatott Kubernetes-fürtön a GitOps használatával.
> [!div class="nextstepaction"]
> [Konfigurációk üzembe helyezése a Gitops használatával](tutorial-use-gitops-connected-cluster.md)
