---
title: 'Gyors útmutató: AK-fürt üzembe helyezése az Azure CLI használatával'
description: Megtudhatja, hogyan hozhat létre gyorsan Kubernetes-fürtöt, hogyan helyezhet üzembe egy alkalmazást, és hogyan figyelheti a teljesítményt az Azure Kubernetes szolgáltatásban (ak) az Azure CLI használatával.
services: container-service
ms.topic: quickstart
ms.date: 02/26/2021
ms.custom:
- H1Hack27Feb2017
- mvc
- devcenter
- seo-javascript-september2019
- seo-javascript-october2019
- seo-python-october2019
- devx-track-azurecli
- contperf-fy21q1
ms.openlocfilehash: 8adfd1a6e26a3381653ca9a794b124e201b9d481
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106106704"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-cluster-using-the-azure-cli"></a>Gyors útmutató: Azure Kubernetes Service-fürt üzembe helyezése az Azure CLI használatával

Az Azure Kubernetes Service (ak) egy felügyelt Kubernetes szolgáltatás, amely lehetővé teszi fürtök gyors üzembe helyezését és kezelését. Ebben a rövid útmutatóban a következőket fogja megtekinteni:
* AK-fürt üzembe helyezése az Azure CLI használatával. 
* Futtasson egy többtárolós alkalmazást webes kezelőfelülettel és Redis-példánnyal a fürtben. 
* A fürt és az alkalmazást futtató hüvelyek állapotának figyelése.

  ![Az Azure Kubernetes Service-ben üzembe helyezett szavazási alkalmazás](./media/container-service-kubernetes-walkthrough/voting-app-deployed-in-azure-kubernetes-service.png)

A rövid útmutató feltételezi, hogy rendelkezik a Kubernetes használatára vonatkozó alapvető ismeretekkel. További információ: [Az Azure Kubernetes Service (ak) Kubernetes alapfogalmai][kubernetes-concepts].

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

A Windows Server-csomópontok készletének létrehozásával kapcsolatos további tudnivalókért tekintse meg a [Windows Server-tárolókat támogató AK-fürt létrehozása][windows-container-cli]című témakört.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.64 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

> [!NOTE]
> Futtassa a parancsokat rendszergazdaként, ha a rövid útmutatóban lévő parancsokat helyileg szeretné futtatni Azure Cloud Shell helyett.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az [Azure-erőforráscsoport](../azure-resource-manager/management/overview.md) olyan logikai csoport, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Ha létrehoz egy erőforráscsoportot, a rendszer kérni fogja a hely megadását. Ez a hely a következő: 
* Az erőforráscsoport metaadatainak tárolási helye.
* Ahol az erőforrások az Azure-ban futnak, ha nem ad meg egy másik régiót az erőforrás létrehozásakor. 

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

Hozzon létre egy erőforráscsoportot az az [Group Create][az-group-create] paranccsal.


```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

A sikeresen létrehozott erőforráscsoport kimenete:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null
}
```

## <a name="enable-cluster-monitoring"></a>Fürt figyelésének engedélyezése

1. Ellenőrizze, hogy a *Microsoft. OperationsManagement* és a *Microsoft. OperationalInsights* regisztrálva van-e az előfizetésében. A regisztráció állapotának ellenõrzése:

    ```azurecli
    az provider show -n Microsoft.OperationsManagement -o table
    az provider show -n Microsoft.OperationalInsights -o table
    ```
 
    Ha nincsenek regisztrálva, regisztráljon a *Microsoft. OperationsManagement* és a *Microsoft. OperationalInsights* a következőkkel:
 
    ```azurecli
    az provider register --namespace Microsoft.OperationsManagement
    az provider register --namespace Microsoft.OperationalInsights
    ```

2. Engedélyezze a [tárolók Azure monitor][azure-monitor-containers] a *--enable-addons figyelési* paraméterrel. 

## <a name="create-aks-cluster"></a>AKS-fürt létrehozása

Hozzon létre egy AK-fürtöt az az [AK Create][az-aks-create] paranccsal. A következő példa egy *myAKSCluster* nevű fürtöt hoz létre egy csomóponttal: 

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --node-count 1 --enable-addons monitoring --generate-ssh-keys
```

Néhány perc elteltével a parancs befejeződik, és a fürthöz tartozó JSON-formátumú adatokat adja vissza.

> [!NOTE]
> Ha AK-fürtöt hoz létre, a rendszer automatikusan létrehoz egy második erőforráscsoportot az AK-erőforrások tárolásához. További információ: [miért jön létre két erőforráscsoport az AK-val?](./faq.md#why-are-two-resource-groups-created-with-aks)

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

Kubernetes-fürt kezeléséhez használja a [Kubectl][kubectl]Kubernetes parancssori ügyfelet. `kubectl` már telepítve van a Azure Cloud Shell használata esetén. 

1. Telepítse `kubectl` helyileg az az az [AK install-CLI][az-aks-install-cli] paranccsal:

    ```azurecli
    az aks install-cli
    ```

2. Konfigurálja `kubectl` úgy a Kubernetes-fürthöz való kapcsolódást az az [AK Get-hitelesítőadats][az-aks-get-credentials] parancs használatával. A következő parancs:  
      * Letölti a hitelesítő adatokat, és konfigurálja a Kubernetes CLI-t a használatára.
      * A a `~/.kube/config` [Kubernetes konfigurációs fájl][kubeconfig-file]alapértelmezett helyét használja. Adja meg a Kubernetes konfigurációs fájljának egy másik helyét a *--file* használatával.


    ```azurecli-interactive
    az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
    ```

3. A [kubectl Get][kubectl-get] paranccsal ellenőrizze a fürthöz való kapcsolódást. Ez a parancs a fürtcsomópontok listáját adja vissza.

    ```azurecli-interactive
    kubectl get nodes
    ```

    A kimenet az előző lépésekben létrehozott egyetlen csomópontot jeleníti meg. Győződjön meg arról, hogy a csomópont állapota *kész*:

    ```output
    NAME                       STATUS   ROLES   AGE     VERSION
    aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.12.8
    ```

## <a name="run-the-application"></a>Az alkalmazás futtatása

A [Kubernetes jegyzékfájl][kubernetes-deployment] határozza meg a fürt kívánt állapotát, például hogy melyik tároló lemezképei futnak. 

Ebben a rövid útmutatóban egy jegyzékfájlt fog használni az [Azure vote-alkalmazás][azure-vote-app]futtatásához szükséges összes objektum létrehozásához. Ez a jegyzékfájl két [Kubernetes-telepítést][kubernetes-deployment]tartalmaz:
* A minta Azure vote Python-alkalmazások.
* Egy Redis-példány. 

A rendszer két [Kubernetes szolgáltatást][kubernetes-service] is létrehoz:
* Belső szolgáltatás a Redis-példányhoz.
* Egy külső szolgáltatás, amely hozzáfér az Azure vote alkalmazáshoz az internetről.

1. Hozzon létre egy `azure-vote.yaml` nevű fájlt.
    * Ha a Azure Cloud Shell használja, akkor ez a fájl a vagy a használatával hozható létre, `code` `vi` vagy `nano` virtuális vagy fizikai rendszer használata esetén.
1. Másolja a következő YAML-definíciót:

    ```yaml
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: azure-vote-back
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: azure-vote-back
      template:
        metadata:
          labels:
            app: azure-vote-back
        spec:
          nodeSelector:
            "beta.kubernetes.io/os": linux
          containers:
          - name: azure-vote-back
            image: mcr.microsoft.com/oss/bitnami/redis:6.0.8
            env:
            - name: ALLOW_EMPTY_PASSWORD
              value: "yes"
            resources:
              requests:
                cpu: 100m
                memory: 128Mi
              limits:
                cpu: 250m
                memory: 256Mi
            ports:
            - containerPort: 6379
              name: redis
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: azure-vote-back
    spec:
      ports:
      - port: 6379
      selector:
        app: azure-vote-back
    ---
    apiVersion: apps/v1
    kind: Deployment
    metadata:
      name: azure-vote-front
    spec:
      replicas: 1
      selector:
        matchLabels:
          app: azure-vote-front
      template:
        metadata:
          labels:
            app: azure-vote-front
        spec:
          nodeSelector:
            "beta.kubernetes.io/os": linux
          containers:
          - name: azure-vote-front
            image: mcr.microsoft.com/azuredocs/azure-vote-front:v1
            resources:
              requests:
                cpu: 100m
                memory: 128Mi
              limits:
                cpu: 250m
                memory: 256Mi
            ports:
            - containerPort: 80
            env:
            - name: REDIS
              value: "azure-vote-back"
    ---
    apiVersion: v1
    kind: Service
    metadata:
      name: azure-vote-front
    spec:
      type: LoadBalancer
      ports:
      - port: 80
      selector:
        app: azure-vote-front
    ```

1. Telepítse az alkalmazást a [kubectl Apply][kubectl-apply] paranccsal, és adja meg a YAML-jegyzékfájl nevét:

    ```console
    kubectl apply -f azure-vote.yaml
    ```

    A kimenet a sikeresen létrehozott központi telepítéseket és szolgáltatásokat mutatja:

    ```output
    deployment "azure-vote-back" created
    service "azure-vote-back" created
    deployment "azure-vote-front" created
    service "azure-vote-front" created
    ```

## <a name="test-the-application"></a>Az alkalmazás tesztelése

Az alkalmazás futtatásakor a Kubernetes szolgáltatás az előtér-alkalmazást az internethez teszi elérhetővé. A folyamat eltarthat pár percig.

Figyelje a folyamatot a [kubectl Get Service][kubectl-get] paranccsal az `--watch` argumentummal.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

A szolgáltatás **külső IP-** kimenete `azure-vote-front` kezdetben *függőben* jelenik meg.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Ha a **külső IP-** cím *függőben* ÁLLAPOTRÓL tényleges nyilvános IP-címről változik, akkor a `CTRL-C` figyelési folyamat leállításához használja a következőt: `kubectl` . A következő példa kimenete a szolgáltatáshoz hozzárendelt érvényes nyilvános IP-címet jeleníti meg:

```output
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Az Azure vote alkalmazás működés közbeni megtekintéséhez nyisson meg egy webböngészőt a szolgáltatás külső IP-címére.

![Az Azure Kubernetes Service-ben üzembe helyezett szavazási alkalmazás](./media/container-service-kubernetes-walkthrough/voting-app-deployed-in-azure-kubernetes-service.png)

A Azure Portal [tárolók Azure monitor][azure-monitor-containers] által rögzített fürtcsomópontok és hüvelyek állapotának megtekintése. 

## <a name="delete-the-cluster"></a>A fürt törlése

Az Azure-költségek elkerülése érdekében törölje a szükségtelen erőforrásokat. Az [az group delete][az-group-delete] paranccsal eltávolíthatja az erőforráscsoportot, a tárolószolgáltatást és az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> A fürt törlésekor az AKS-fürt által használt Azure Active Directory-szolgáltatásnév nem lesz eltávolítva. A szolgáltatásnév eltávolításának lépéseiért lásd [az AKS-szolgáltatásnevekre vonatkozó szempontokat és a szolgáltatásnevek törlését][sp-delete] ismertető cikket.
> 
> Felügyelt identitás használata esetén az identitást a platform felügyeli, és nem szükséges az eltávolítás.

## <a name="get-the-code"></a>A kód letöltése

Ebben a rövid útmutatóban már meglévő tárolók lemezképeit használták egy Kubernetes-telepítés létrehozásához. A kapcsolódó alkalmazás kódja, Docker és Kubernetes jegyzékfájlja [elérhető a githubon.][azure-vote-app]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban üzembe helyezett egy Kubernetes-fürtöt, majd egy többtárolós alkalmazást helyezett üzembe. [Nyissa meg a Kubernetes webes irányítópultját][kubernetes-dashboard] az AK-fürthöz.

Az AKS-sel kapcsolatos további információkért és a kódtól az üzembe helyezésig terjedő teljes útmutatóért folytassa a Kubernetes-fürtöket bemutató oktatóanyaggal.

> [!div class="nextstepaction"]
> [AK-oktatóanyag][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubeconfig-file]: https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: ../azure-monitor/containers/container-insights-onboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks#az_aks_browse
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: /cli/azure/install_azure_cli
[azure-monitor-containers]: ../azure-monitor/containers/container-insights-overview.md
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[windows-container-cli]: windows-container-cli.md
