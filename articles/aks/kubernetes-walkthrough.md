---
title: 'Gyors útmutató: AK-fürt üzembe helyezése az Azure CLI használatával'
description: Megtudhatja, hogyan hozhat létre gyorsan Kubernetes-fürtöt, hogyan helyezhet üzembe egy alkalmazást, és hogyan figyelheti a teljesítményt az Azure Kubernetes szolgáltatásban (ak) az Azure CLI használatával.
services: container-service
ms.topic: quickstart
ms.date: 01/12/2021
ms.custom:
- H1Hack27Feb2017
- mvc
- devcenter
- seo-javascript-september2019
- seo-javascript-october2019
- seo-python-october2019
- devx-track-azurecli
- contperf-fy21q1
ms.openlocfilehash: 351b31db01bcfb1ff0e5519750a6a8e2af8c7e7e
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100578765"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-cluster-using-the-azure-cli"></a>Gyors útmutató: Azure Kubernetes Service-fürt üzembe helyezése az Azure CLI használatával

Ebben a rövid útmutatóban egy Azure Kubernetes-szolgáltatási (ak-) fürtöt helyez üzembe az Azure CLI használatával. Az AK egy felügyelt Kubernetes szolgáltatás, amely lehetővé teszi fürtök gyors üzembe helyezését és kezelését. A fürtben fut egy többtárolós alkalmazás, amely tartalmazza a webes kezelőfelületet és a Redis-példányt. Ezután megtudhatja, hogyan figyelheti a fürt állapotát és az alkalmazást futtató hüvelyeket.

A Windows Server-csomópontok készletének létrehozásával kapcsolatos további tudnivalókért tekintse meg a [Windows Server-tárolókat támogató AK-fürt létrehozása][windows-container-cli]című témakört.

![Az Azure Kubernetes Service-ben üzembe helyezett szavazási alkalmazás](./media/container-service-kubernetes-walkthrough/voting-app-deployed-in-azure-kubernetes-service.png)

A rövid útmutató feltételezi, hogy rendelkezik a Kubernetes használatára vonatkozó alapvető ismeretekkel. További információ: [Az Azure Kubernetes Service (ak) Kubernetes alapfogalmai][kubernetes-concepts].

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.64 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

> [!NOTE]
> Ha a rövid útmutatóban helyileg futtatja a parancsokat (Azure Cloud Shell helyett), győződjön meg arról, hogy rendszergazdaként futtatja a parancsokat.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai csoport, amelyben az Azure-erőforrások üzembe helyezése és kezelése zajlik. Az erőforráscsoportok létrehozásakor meg kell adnia egy helyet. Ez a hely határozza meg, hogy az erőforráscsoport metaadatai hol vannak tárolva, és az erőforrások hol futnak az Azure-ban, ha nem ad meg másik régiót az erőforrások létrehozásakor. Hozzon létre egy erőforráscsoportot az az [Group Create][az-group-create] paranccsal.

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Az alábbi példához hasonló kimenet azt jelzi, hogy az erőforráscsoport sikeresen létrejött:

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

## <a name="create-aks-cluster"></a>AKS-fürt létrehozása

Használja az [az aks create][az-aks-create] parancsot egy AKS-fürt létrehozásához. A következő példa egy *myAKSCluster* nevű fürtöt hoz létre egy csomóponttal. A művelet végrehajtása több percet is igénybe vehet.

> [!NOTE]
> A [tárolók Azure monitor][azure-monitor-containers] a *--enable-addons figyelési* paraméterrel engedélyezhető, amelyhez a *Microsoft. OperationsManagement* és a *Microsoft. OperationalInsights* regisztrálása szükséges az előfizetésben. A regisztráció állapotának ellenõrzése:
> 
> ```azurecli
> az provider show -n Microsoft.OperationsManagement -o table
> az provider show -n Microsoft.OperationalInsights -o table
> ```
> 
> Ha nincsenek regisztrálva, használja a következő parancsot a *Microsoft. OperationsManagement* és a *Microsoft. OperationalInsights* regisztrálásához:
> 
> ```azurecli
> az provider register --namespace Microsoft.OperationsManagement
> az provider register --namespace Microsoft.OperationalInsights
> ```

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --node-count 1 --enable-addons monitoring --generate-ssh-keys
```

Néhány perc elteltével a parancs befejeződik, és a fürthöz tartozó JSON-formátumú adatokat adja vissza.

> [!NOTE]
> AK-fürt létrehozásakor a rendszer automatikusan létrehoz egy második erőforráscsoportot az AK-erőforrások tárolásához. További információ: [miért jön létre két erőforráscsoport az AK-val?](./faq.md#why-are-two-resource-groups-created-with-aks)

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

A Kubernetes-fürtök kezeléséhez a [kubectl][kubectl], a Kubernetes parancssori ügyfélprogramot kell használnia. Ha Azure Cloud Shellt használ, `kubectl` már telepítve van. A helyi telepítéshez `kubectl` használja az az [AK install-CLI][az-aks-install-cli] parancsot:

```azurecli
az aks install-cli
```

Az [az aks get-credentials][az-aks-get-credentials] paranccsal konfigurálható `kubectl` a Kubernetes-fürthöz való csatlakozásra. Ez a parancs letölti a hitelesítő adatokat, és konfigurálja a Kubernetes CLI-t a használatára.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

> [!NOTE]
> A fenti parancs a [Kubernetes konfigurációs fájljának][kubeconfig-file]alapértelmezett helyét használja, amely a következő: `~/.kube/config` . Megadhat egy másik helyet a Kubernetes konfigurációs fájljához a *--file* használatával.

A fürthöz való csatlakozás ellenőrzéséhez használja a [kubectl get][kubectl-get] parancsot a fürtcsomópontok listájának lekéréséhez.

```azurecli-interactive
kubectl get nodes
```

A következő példakimenet az előző lépésekben létrehozott csomópontot mutatja be. Győződjön meg arról, hogy a csomópont állapota *kész*:

```output
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.12.8
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

A [Kubernetes jegyzékfájl][kubernetes-deployment] a fürt kívánt állapotát határozza meg, például a tároló lemezképeit. Ebben a rövid útmutatóban egy jegyzékfájlt használunk az [Azure vote-alkalmazás][azure-vote-app]futtatásához szükséges összes objektum létrehozásához. Ez a jegyzékfájl két [Kubernetes-telepítést][kubernetes-deployment] tartalmaz – egyet az Azure-beli szavazás Python-alkalmazásaihoz, a másikat pedig egy Redis-példányhoz. Két [Kubernetes][kubernetes-service] -szolgáltatás is létrejön – egy belső szolgáltatás a Redis-példányhoz, és egy külső szolgáltatás, amely az Azure vote alkalmazást az internetről éri el.

Hozzon létre egy nevű fájlt `azure-vote.yaml` , és másolja a következő YAML-definícióba. Ha a Azure Cloud Shell használja, akkor ez a fájl a vagy a használatával hozható létre, `code` `vi` vagy `nano` virtuális vagy fizikai rendszeren végzett munka esetén:

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

Telepítse az alkalmazást a [kubectl Apply][kubectl-apply] paranccsal, és adja meg a YAML-jegyzékfájl nevét:

```console
kubectl apply -f azure-vote.yaml
```

A következő példa kimenete a sikeresen létrehozott központi telepítéseket és szolgáltatásokat mutatja:

```output
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Az alkalmazás tesztelése

Az alkalmazás futtatásakor a Kubernetes szolgáltatás az előtér-alkalmazást az internethez teszi elérhetővé. A folyamat eltarthat pár percig.

A folyamat állapotának monitorozásához használja [kubectl get service][kubectl-get] parancsot a `--watch` argumentummal.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

Kezdetben a *külső IP-cím* az *Azure-vote-elülső* szolgáltatáshoz *függőben* jelenik meg.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Ha a *külső IP-* cím *függőben* ÁLLAPOTRÓL tényleges nyilvános IP-címről változik, akkor a `CTRL-C` figyelési folyamat leállításához használja a következőt: `kubectl` . A következő példa kimenete a szolgáltatáshoz hozzárendelt érvényes nyilvános IP-címet jeleníti meg:

```output
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Az Azure vote alkalmazás működés közbeni megtekintéséhez nyisson meg egy webböngészőt a szolgáltatás külső IP-címére.

![Az Azure Kubernetes Service-ben üzembe helyezett szavazási alkalmazás](./media/container-service-kubernetes-walkthrough/voting-app-deployed-in-azure-kubernetes-service.png)

Az AK-fürt létrehozásakor a [tárolók Azure monitor][azure-monitor-containers] a fürt csomópontjai és a hüvelyek állapotára vonatkozó mérőszámok rögzítésére lettek beállítva. Ezek az állapotmetrikák elérhetők az Azure Portalon.

## <a name="delete-the-cluster"></a>A fürt törlése

Az Azure-költségek elkerülése érdekében törölje a szükségtelen erőforrásokat.  Ha a fürtre már nincs szükség, az [az group delete][az-group-delete] paranccsal törölheti az erőforráscsoportot, a tárolószolgáltatást és az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> A fürt törlésekor az AKS-fürt által használt Azure Active Directory-szolgáltatásnév nem lesz eltávolítva. A szolgáltatásnév eltávolításának lépéseiért lásd [az AKS-szolgáltatásnevekre vonatkozó szempontokat és a szolgáltatásnevek törlését][sp-delete] ismertető cikket. Felügyelt identitás használata esetén az identitást a platform felügyeli, és nem szükséges az eltávolítás.

## <a name="get-the-code"></a>A kód letöltése

Ebben a rövid útmutatóban előre létrehozott tároló-lemezképeket használtak a Kubernetes központi telepítésének létrehozásához. A kapcsolódó alkalmazáskód, Docker-fájl és Kubernetes-jegyzékfájl a GitHubon érhetőek el.

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy Kubernetes-fürtöt és azon egy többtárolós alkalmazást helyezett üzembe. [A Kubernetes webes irányítópultját is elérheti][kubernetes-dashboard] az AK-fürthöz.

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
[az-aks-browse]: /cli/azure/aks?view=azure-cli-latest&preserve-view=true#az-aks-browse
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest&preserve-view=true#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest&preserve-view=true#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest&preserve-view=true#az-aks-install-cli
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-monitor-containers]: ../azure-monitor/containers/container-insights-overview.md
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[windows-container-cli]: windows-container-cli.md