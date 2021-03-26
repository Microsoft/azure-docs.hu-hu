---
title: 'Gyors útmutató: AK-fürt üzembe helyezése a Azure Portal használatával'
titleSuffix: Azure Kubernetes Service
description: Megtudhatja, hogyan hozhat létre gyorsan Kubernetes-fürtöt, hogyan helyezhet üzembe egy alkalmazást, és hogyan figyelheti a teljesítményt az Azure Kubernetes szolgáltatásban (ak) a Azure Portal használatával.
services: container-service
ms.topic: quickstart
ms.date: 03/15/2021
ms.custom: mvc, seo-javascript-october2019, contperf-fy21q3
ms.openlocfilehash: aa07dd84cbd107aca77236f4d084ef95a7f1005b
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2021
ms.locfileid: "105544281"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-the-azure-portal"></a>Gyors útmutató: Azure Kubernetes Service (ak) fürt üzembe helyezése a Azure Portal használatával

Az Azure Kubernetes Service (ak) egy felügyelt Kubernetes szolgáltatás, amely lehetővé teszi fürtök gyors üzembe helyezését és kezelését. Ebben a rövid útmutatóban a következőket fogja megtekinteni:
* A Azure Portal használatával helyezzen üzembe egy AK-fürtöt. 
* Futtasson egy többtárolós alkalmazást webes kezelőfelülettel és Redis-példánnyal a fürtben. 
* A fürt és az alkalmazást futtató hüvelyek állapotának figyelése.

![Az Azure Vote mintaalkalmazás keresését ábrázoló kép](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

A rövid útmutató feltételezi, hogy rendelkezik a Kubernetes használatára vonatkozó alapvető ismeretekkel. További információ: [Az Azure Kubernetes Service (ak) Kubernetes alapfogalmai][kubernetes-concepts].

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.

## <a name="create-an-aks-cluster"></a>AKS-fürt létrehozása

1. Az Azure Portal menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet.

2. Válassza a **Tárolók** > **Kubernetes Service** lehetőséget.

3. Az **Alapvető beállítások** lapon konfigurálja az alábbiakat:
    - **Projekt részletei**: 
        * Válasszon ki egy **Azure-előfizetést**.
        * Válasszon ki vagy hozzon létre egy Azure- **erőforráscsoportot**, például *myResourceGroup*.
    - **Fürt részletei**: 
        * Adja meg a **Kubernetes-fürt nevét**, például *myAKSCluster*. 
        * Válasszon **régiót** és **Kubernetes-verziót** az AK-fürthöz.
    - **Elsődleges csomópont-készlet**: 
        * Az AKS-csomópontokhoz válasszon egy VM-**csomópontméretet**. A virtuálisgép-méret az AKS-fürt telepítését követően *nem* módosítható.
        * Adja meg a fürtre telepítendő csomópontok számát. Ehhez a rövid útmutatóhoz a **Csomópontok száma** beállítás értékeként adjon meg *1*-et. A csomópontok száma a fürt telepítése után is *módosítható*.
    
    ![AKS-fürt létrehozása – alapvető adatok megadása](media/kubernetes-walkthrough-portal/create-cluster-basics.png)

4. Válassza a **Next (tovább): node Pools** (Befejezés) lehetőséget.

5. Tartsa meg az alapértelmezett **csomópont-készletek** beállításait. A képernyő alján kattintson a **Tovább: hitelesítés** elemre.
    > [!CAUTION]
    > Az újonnan létrehozott Azure AD-szolgáltatások több percet is igénybe vehetnek, és így elérhetővé válnak, ami az "egyszerű szolgáltatásnév nem található" hibákat és az érvényesítési hibákat Azure Portal. Ha ezt a bumpet találta, tekintse meg a [hibaelhárítással](troubleshooting.md#received-an-error-saying-my-service-principal-wasnt-found-or-is-invalid-when-i-try-to-create-a-new-cluster) foglalkozó cikket.

6. A **Hitelesítés** lapon konfigurálja az alábbiakat:
    - Hozzon létre egy új fürtöt az alábbiak szerint:
        * A **hitelesítési** mező kihagyása a **assinged által felügyelt identitással**, vagy
        * Szolgáltatásnév **kiválasztása egyszerű** szolgáltatásnév használatára. 
            * Válassza a *(új) alapértelmezett egyszerű szolgáltatásnév* lehetőséget az alapértelmezett szolgáltatásnév létrehozásához, vagy
            * Válassza az *egyszerű szolgáltatásnév beállítása* lehetőséget egy meglévő használatára. Meg kell adnia a meglévő rendszerbiztonsági tag SPN-ügyfél-AZONOSÍTÓját és titkos kulcsát.
    - Engedélyezze a Kubernetes szerepköralapú hozzáférés-vezérlés (Kubernetes RBAC) beállítást, hogy részletesebben szabályozható legyen az AK-fürtben üzembe helyezett Kubernetes-erőforrásokhoz való hozzáférés.

    Alapértelmezés szerint a rendszer *alapszintű* hálózatkezelést használ, a tárolók Azure monitor pedig engedélyezve van. 

7. Ha az ellenőrzés véget ért, kattintson az **Áttekintés és létrehozás**, majd a **Létrehozás** lehetőségre. 


8. Az AKS-fürt létrehozása eltarthat néhány percig. A telepítés befejezésekor navigáljon az erőforráshoz az alábbiak szerint:
    * Kattintson **az erőforrás keresése** lehetőségre, vagy
    * Tallózással keresse meg az AK fürterőforrás-csoportot, és válassza ki az AK-erőforrást. 
        * Példaként használható fürt irányítópultja: Tallózás a *myResourceGroup* és a *myAKSCluster* -erőforrás kiválasztásával.

        ![Példa AKS-irányítópult az Azure Portalon](media/kubernetes-walkthrough-portal/aks-portal-dashboard.png)

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

Kubernetes-fürt kezeléséhez használja a [Kubectl][kubectl]Kubernetes parancssori ügyfelet. `kubectl` már telepítve van a Azure Cloud Shell használata esetén. 

1. Nyissa meg Cloud Shell a `>_` Azure Portal tetején található gomb használatával.

    ![Az Azure Cloud Shell megnyitása a portálon](media/kubernetes-walkthrough-portal/aks-cloud-shell.png)

    > [!NOTE]
    > A műveletek helyi rendszerhéj-telepítéssel történő végrehajtásához tegye a következőket:
    > 1. Ellenőrizze, hogy az Azure CLI telepítve van-e.
    > 2. Kapcsolódjon az Azure-hoz a `az login` parancs használatával.

2. Konfigurálja `kubectl` úgy a Kubernetes-fürthöz való kapcsolódást az az [AK Get-hitelesítőadats][az-aks-get-credentials] parancs használatával. A következő parancs letölti a hitelesítő adatokat, és konfigurálja a Kubernetes CLI-t a használatára.

    ```azurecli
    az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
    ```

3. Ellenőrizze a fürthöz való kapcsolódást a használatával `kubectl get` , hogy visszaadja-e a fürtcsomópontok listáját.

    ```console
    kubectl get nodes
    ```

    A kimenet az előző lépésekben létrehozott egyetlen csomópontot jeleníti meg. Győződjön meg arról, hogy a csomópont állapota *kész*:

    ```output
    NAME                       STATUS    ROLES     AGE       VERSION
    aks-agentpool-14693408-0   Ready     agent     15m       v1.11.5
    ```

## <a name="run-the-application"></a>Az alkalmazás futtatása

A Kubernetes jegyzékfájl definiálja a fürt kívánt állapotát, például azt, hogy melyik tároló-lemezképek futnak. 

Ebben a rövid útmutatóban egy jegyzékfájlt fog használni az Azure vote-alkalmazás futtatásához szükséges összes objektum létrehozásához. Ez a jegyzékfájl két Kubernetes-telepítést tartalmaz:
* A minta Azure vote Python-alkalmazások.
* Egy Redis-példány. 

A rendszer két Kubernetes szolgáltatást is létrehoz:
* Belső szolgáltatás a Redis-példányhoz.
* Egy külső szolgáltatás, amely hozzáfér az Azure vote alkalmazáshoz az internetről.

1. A Cloud Shell a szerkesztő használatával hozzon létre egy nevű fájlt `azure-vote.yaml` , például:
    * `code azure-vote.yaml`
    * `nano azure-vote.yaml` vagy  
    * `vi azure-vote.yaml`. 

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

1. Telepítse az alkalmazást a `kubectl apply` paranccsal, és adja meg a YAML-jegyzékfájl nevét:

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

A folyamat figyeléséhez használja az `kubectl get service` `--watch` argumentumot a paranccsal.

```console
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

![Az Azure Vote mintaalkalmazás keresését ábrázoló kép](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="monitor-health-and-logs"></a>Állapot és naplók monitorozása

A fürt létrehozásakor a tárolók Azure Monitor engedélyezve lettek. A tárolók Azure Monitor a fürtön futó AK-fürthöz és hüvelyekhez egyaránt biztosítanak állapot-metrikákat.

A metrikai adatok feltöltése néhány percet vesz igénybe a Azure Portalban. Az Azure vote hüvely aktuális állapotának, üzemidőének és erőforrás-használatának megtekintéséhez:

1. Tallózzon vissza a Azure Portal AK-erőforrásához.
1. A bal oldali **figyelés** **területen válassza az** eredmények lehetőséget.
1. A felső részen válassza a **+ szűrő hozzáadása** elemet.
1. Válassza a **névtér** lehetőséget a tulajdonság mezőben, majd válassza a lehetőséget *\<All but kube-system\>* .
1. Válassza ki a **tárolókat** a megtekintéshez.

A `azure-vote-back` és a `azure-vote-front` tárolók megjelennek a következő példában látható módon:

![Futó tárolók állapotának megtekintése az AKS-ben](media/kubernetes-walkthrough-portal/monitor-containers.png)

A pod naplóinak megtekintéséhez `azure-vote-front` válassza a **tárolók megjelenítése** a tárolók listájából legördülő listát. Ezek a naplók tartalmazzák az *StdOut* és a *stderr* streameket a tárolóból.

![Tárolók naplóinak megtekintése az AKS-ben](media/kubernetes-walkthrough-portal/monitor-container-logs.png)

## <a name="delete-cluster"></a>Fürt törlése

Az Azure-költségek elkerülése érdekében törölje a szükségtelen erőforrásokat. Kattintson a **Törlés** gombra az AK-fürt irányítópultján. Használhatja az az [AK delete][az-aks-delete] parancsot is a Cloud Shellban:

```azurecli
az aks delete --resource-group myResourceGroup --name myAKSCluster --no-wait
```
> [!NOTE]
> A fürt törlésekor az AKS-fürt által használt Azure Active Directory-szolgáltatásnév nem lesz eltávolítva. A szolgáltatásnév eltávolításának lépéseiért lásd [az AKS-szolgáltatásnevekre vonatkozó szempontokat és a szolgáltatásnevek törlését][sp-delete] ismertető cikket.
> 
> Felügyelt identitás használata esetén az identitást a platform felügyeli, és nem szükséges az eltávolítás.

## <a name="get-the-code"></a>A kód letöltése

Ebben a rövid útmutatóban már meglévő tárolók lemezképeit használták egy Kubernetes-telepítés létrehozásához. A kapcsolódó alkalmazás kódja, Docker és Kubernetes jegyzékfájlja [elérhető a githubon.][azure-vote-app]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban üzembe helyezett egy Kubernetes-fürtöt, majd egy többtárolós alkalmazást helyezett üzembe. Nyissa meg a Kubernetes webes irányítópultját az AK-fürthöz.


Ha többet szeretne megtudni az AK-ról egy teljes példán keresztül, beleértve az alkalmazások létrehozását, üzembe helyezését Azure Container Registry, a futó alkalmazások frissítését és a fürt méretezését és frissítését, folytassa a Kubernetes-fürt oktatóanyagával.

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
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-delete]: /cli/azure/aks#az-aks-delete
[aks-monitor]: ../azure-monitor/containers/container-insights-overview.md
[aks-network]: ./concepts-network.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[http-routing]: ./http-application-routing.md
[sp-delete]: kubernetes-service-principal.md#additional-considerations