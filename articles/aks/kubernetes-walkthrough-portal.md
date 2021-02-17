---
title: AK-fürt létrehozása a portálon
titleSuffix: Azure Kubernetes Service
description: Megtudhatja, hogyan hozhat létre gyorsan Kubernetes-fürtöt, hogyan helyezhet üzembe egy alkalmazást, és hogyan figyelheti a teljesítményt az Azure Kubernetes szolgáltatásban (ak) a Azure Portal használatával.
services: container-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.custom: mvc, seo-javascript-october2019
ms.openlocfilehash: 7f59924b2a50f29e01d46e12389e5ca52769225d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100578694"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-the-azure-portal"></a>Gyors útmutató: Azure Kubernetes Service (ak) fürt üzembe helyezése a Azure Portal használatával

Az Azure Kubernetes Service (ak) egy felügyelt Kubernetes szolgáltatás, amely lehetővé teszi fürtök gyors üzembe helyezését és kezelését. Ebben a rövid útmutatóban egy AKS-fürtöt helyezünk üzembe az Azure Portal használatával. A fürtben fut egy többtárolós alkalmazás, amely tartalmazza a webes kezelőfelületet és a Redis-példányt. Ezután megtudhatja, hogyan figyelheti a fürt állapotát és az alkalmazást futtató hüvelyeket.

![Az Azure Vote mintaalkalmazás keresését ábrázoló kép](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

A rövid útmutató feltételezi, hogy rendelkezik a Kubernetes használatára vonatkozó alapvető ismeretekkel. További információ: [Az Azure Kubernetes Service (ak) Kubernetes alapfogalmai][kubernetes-concepts].

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-an-aks-cluster"></a>AKS-fürt létrehozása

Az AKS-fürt létrehozásához hajtsa végre az alábbi lépéseket:

1. Az Azure Portal menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet.

2. Válassza a **Tárolók** >  **Kubernetes Service** lehetőséget.

3. Az **Alapvető beállítások** lapon konfigurálja az alábbiakat:
    - **Projekt részletei**: válasszon ki egy Azure- **előfizetést**, majd válasszon ki vagy hozzon létre egy Azure- **erőforráscsoportot**, például *myResourceGroup*.
    - **Fürt részletei**: adjon meg egy **Kubernetes-fürtöt**, például a *myAKSCluster* nevet. Válasszon **régiót** és **Kubernetes-verziót** az AK-fürthöz.
    - **Elsődleges csomópont-készlet**: válassza ki a virtuális gép **csomópontjának méretét** az AK-csomópontok számára. A virtuális gép mérete *nem* módosítható, ha egy AK-fürt üzembe helyezése megtörtént.
            – Válassza ki a fürtbe telepítendő csomópontok számát. Ehhez a rövid útmutatóhoz a **Csomópontok száma** beállítás értékeként adjon meg *1*-et. A csomópontok száma a fürt telepítése után is *módosítható*.
    
    ![AKS-fürt létrehozása – alapvető adatok megadása](media/kubernetes-walkthrough-portal/create-cluster-basics.png)

    Válassza a **Next (tovább): node Pools** (Befejezés) lehetőséget.

4. A **csomópont-készletek** lapon tartsa meg az alapértelmezett beállításokat. A képernyő alján kattintson a **Tovább: hitelesítés** elemre.
    > [!CAUTION]
    > Az új HRE-szolgáltatások létrehozása több percet is igénybe vehet, így az egyszerű szolgáltatásnév nem talált hibákat és érvényesítési hibákat Azure Portalban. Ha ezt tapasztalja, látogasson el [ide](troubleshooting.md#received-an-error-saying-my-service-principal-wasnt-found-or-is-invalid-when-i-try-to-create-a-new-cluster) a mérsékléshez.

5. A **Hitelesítés** lapon konfigurálja az alábbiakat:
    - Hozzon létre egy új szolgáltatásnevet úgy, hogy a **Szolgáltatásnév** mezőben meghagyja az **(új) alapértelmezett szolgáltatásnév** értéket. De használhat egy már meglévőt is, ha a *Szolgáltatásnév konfigurálása* lehetőséget választja. Ha meglévőt használ, meg kell adnia az SPN ügyfél-azonosítót és a titkos kulcsot.
    - Engedélyezze a Kubernetes szerepköralapú hozzáférés-vezérlés (Kubernetes RBAC) beállítását. Így részletesebben szabályozhatja az AKS-fürtben üzembe helyezett Kubernetes-erőforrásokhoz való hozzáférést.

    Azt is megteheti, hogy az egyszerű szolgáltatásnév helyett felügyelt identitást is használhat. További információért lásd: [felügyelt identitások használata](use-managed-identity.md) .

Alapértelmezés szerint a rendszer *alapszintű* hálózatkezelést használ, a tárolók Azure monitor pedig engedélyezve van. Ha az ellenőrzés véget ért, kattintson az **Áttekintés és létrehozás**, majd a **Létrehozás** lehetőségre.

Az AKS-fürt létrehozása eltarthat néhány percig. Ha befejeződött a telepítés, kattintson az **erőforrás** megnyitása lehetőségre, vagy keresse meg az AK-beli fürterőforrás-csoportot (például *myResourceGroup*), és válassza ki az AK-erőforrást, például *myAKSCluster*. Megjelenik az AK-fürt irányítópultja, ahogy az a következő példában látható:

![Példa AKS-irányítópult az Azure Portalon](media/kubernetes-walkthrough-portal/aks-portal-dashboard.png)

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

A Kubernetes-fürtök kezeléséhez a [kubectl][kubectl], a Kubernetes parancssori ügyfélprogramot kell használnia. A `kubectl` ügyfél előzetesen már telepítve van az Azure Cloud Shellben.

Nyissa meg Cloud Shell a `>_` Azure Portal tetején található gomb használatával.

![Az Azure Cloud Shell megnyitása a portálon](media/kubernetes-walkthrough-portal/aks-cloud-shell.png)

> [!NOTE]
> Ha ezeket a műveleteket egy helyi rendszerhéj-telepítésben szeretné elvégezni, először ellenőriznie kell az Azure CLI-t, majd az parancs használatával kell csatlakoznia az Azure-hoz `az login` .

Az [az aks get-credentials][az-aks-get-credentials] paranccsal konfigurálható `kubectl` a Kubernetes-fürthöz való csatlakozásra. Ez a parancs letölti a hitelesítő adatokat, és konfigurálja a Kubernetes CLI-t a használatára. A következő példa lekéri a *myResourceGroup* erőforrásban lévő *myAKSCluster* fürtnév hitelesítő adatait:

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

A fürthöz való csatlakozás ellenőrzéséhez használja a [kubectl get][kubectl-get] parancsot a fürtcsomópontok listájának lekéréséhez.

```console
kubectl get nodes
```

A következő példakimenet az előző lépésekben létrehozott csomópontot mutatja be. Győződjön meg arról, hogy a csomópont állapota *kész*:

```output
NAME                       STATUS    ROLES     AGE       VERSION
aks-agentpool-14693408-0   Ready     agent     15m       v1.11.5
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

A Kubernetes jegyzékfájl a fürt kívánt állapotát határozza meg, például a tároló lemezképeit. Ebben a rövid útmutatóban egy jegyzékfájlt használunk az Azure Vote-alkalmazás futtatásához szükséges összes objektum létrehozásához. Ez a jegyzékfájl két [Kubernetes-telepítést][kubernetes-deployment] tartalmaz – egyet az Azure-beli szavazás Python-alkalmazásaihoz, a másikat pedig egy Redis-példányhoz. Két [Kubernetes][kubernetes-service] -szolgáltatás is létrejön – egy belső szolgáltatás a Redis-példányhoz, és egy külső szolgáltatás, amely az Azure vote alkalmazást az internetről éri el.

A Cloud Shell egy szövegszerkesztővel hozzon létre egy nevű fájlt `azure-vote.yaml` , például: `code azure-vote.yaml` `nano azure-vote.yaml` vagy `vi azure-vote.yaml` . Ezután másolja a következő YAML-definíciót:

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

```console
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

![Az Azure Vote mintaalkalmazás keresését ábrázoló kép](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="monitor-health-and-logs"></a>Állapot és naplók monitorozása

A fürt létrehozásakor a tárolók Azure Monitor engedélyezve lettek. Ez a monitorozási funkció az AKS-fürt és a fürtön futó podok állapotával kapcsolatos mérőszámokat biztosít.

Az adatok Azure Portalra történő feltöltése eltarthat néhány percig. Az Azure Vote-podok jelenlegi állapotának, üzemidejének és erőforrás-felhasználásának megjelenítéséhez lépjen vissza az AKS-erőforráshoz az Azure Portalon (például *myAKSCluster*). Az állapotot ezután a következő módon érheti el:

1. A bal oldali **figyelés** **területen válassza az** eredmények lehetőséget
1. Válassza a **+ Szűrő hozzáadása** elemet a képernyő felső részén
1. Válassza ki a *névteret* tulajdonságként, majd válassza a *\<All but kube-system\>*
1. Válassza a **Tárolók** nézetet.

Megjelenik az *azure-vote-back* és az *azure-vote-front* tároló, az alábbi példában látható módon:

![Futó tárolók állapotának megtekintése az AKS-ben](media/kubernetes-walkthrough-portal/monitor-containers.png)

Ha meg szeretné tekinteni a `azure-vote-front` Pod-naplókat, válassza a **tárolók megjelenítése** a tárolók listájának legördülő listából. Ezek a naplók tartalmazzák az *StdOut* és a *stderr* streameket a tárolóból.

![Tárolók naplóinak megtekintése az AKS-ben](media/kubernetes-walkthrough-portal/monitor-container-logs.png)

## <a name="delete-cluster"></a>Fürt törlése

Ha a fürtre már nincs szükség, törölje az erőforráscsoportot. Ezzel törli az összes társított erőforrást is. A művelet elvégezhető a Azure Portalban, ha a **Törlés** gombra KATTINT az AK-fürt irányítópultján. Azt is megteheti, hogy az az [AK delete][az-aks-delete] parancs használható a Cloud Shellban:

```azurecli
az aks delete --resource-group myResourceGroup --name myAKSCluster --no-wait
```

> [!NOTE]
> A fürt törlésekor az AKS-fürt által használt Azure Active Directory-szolgáltatásnév nem lesz eltávolítva. A szolgáltatásnév eltávolításának lépéseiért lásd [az AKS-szolgáltatásnevekre vonatkozó szempontokat és a szolgáltatásnevek törlését][sp-delete] ismertető cikket. Felügyelt identitás használata esetén az identitást a platform felügyeli, és nem szükséges az eltávolítás.

## <a name="get-the-code"></a>A kód letöltése

Ebben a rövid útmutatóban előre létrehozott tároló-lemezképeket használtak a Kubernetes központi telepítésének létrehozásához. A kapcsolódó alkalmazáskód, Docker-fájl és Kubernetes-jegyzékfájl a GitHubon érhetőek el.

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy Kubernetes-fürtöt és azon egy többtárolós alkalmazást helyezett üzembe.

Az AKS-sel kapcsolatos további információkért és a kódtól az üzembe helyezésig terjedő teljes útmutatóért folytassa a Kubernetes-fürtöket bemutató oktatóanyaggal.

> [!div class="nextstepaction"]
> [AK-oktatóanyag][aks-tutorial]

<!-- LINKS - external -->
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-documentation]: https://kubernetes.io/docs/home/

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest&preserve-view=true#az-aks-get-credentials
[az-aks-delete]: /cli/azure/aks#az-aks-delete
[aks-monitor]: ../azure-monitor/containers/container-insights-overview.md
[aks-network]: ./concepts-network.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[http-routing]: ./http-application-routing.md
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-dev-spaces]: ../dev-spaces/index.yml
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
