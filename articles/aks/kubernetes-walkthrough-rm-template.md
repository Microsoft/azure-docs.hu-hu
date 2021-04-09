---
title: Rövid útmutató – Azure Kubernetes-szolgáltatás (ak) fürt létrehozása
description: Megtudhatja, hogyan hozhat létre gyorsan Kubernetes-fürtöt egy Azure Resource Manager sablonnal, és hogyan helyezhet üzembe alkalmazást az Azure Kubernetes szolgáltatásban (ak)
services: container-service
ms.topic: quickstart
ms.date: 03/15/2021
ms.custom: mvc,subject-armqs, devx-track-azurecli
ms.openlocfilehash: e88c56f050f2f6d1183eef23a844f5eaf1f671c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103492948"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-an-arm-template"></a>Gyors útmutató: Azure Kubernetes-szolgáltatás (ak) fürt üzembe helyezése ARM-sablon használatával

Az Azure Kubernetes Service (ak) egy felügyelt Kubernetes szolgáltatás, amely lehetővé teszi fürtök gyors üzembe helyezését és kezelését. Ebben a rövid útmutatóban a következőket fogja megtekinteni:
* A Azure Resource Manager sablon használatával helyezzen üzembe egy AK-fürtöt. 
* Futtasson egy többtárolós alkalmazást webes kezelőfelülettel és Redis-példánnyal a fürtben. 

![Az Azure Vote keresését ábrázoló kép](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

A rövid útmutató feltételezi, hogy rendelkezik a Kubernetes használatára vonatkozó alapvető ismeretekkel. További információ: [Az Azure Kubernetes Service (ak) Kubernetes alapfogalmai][kubernetes-concepts].

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aks%2Fazuredeploy.json)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.61 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

- Ha AK-fürtöt szeretne létrehozni Resource Manager-sablonnal, adjon meg egy nyilvános SSH-kulcsot. Ha erre az erőforrásra van szüksége, tekintse meg a következő szakaszt: Ellenkező esetben ugorjon a [sablon áttekintése](#review-the-template) szakaszra.

### <a name="create-an-ssh-key-pair"></a>SSH-kulcs létrehozása

Az AK-csomópontok eléréséhez egy SSH-kulcspár (nyilvános és privát) használatával kapcsolódhat, amelyet a parancs használatával hozhatja meg `ssh-keygen` . Alapértelmezés szerint ezek a fájlok a *~/.ssh* könyvtárban jönnek létre. A parancs futtatásakor a rendszer `ssh-keygen` felülírja az adott helyen már meglévő SSH-kulcspár nevét.

1. [https://shell.azure.com](https://shell.azure.com)Nyissa meg a Cloud shellt a böngészőben.

1. Futtassa a következő parancsot: `ssh-keygen`. Az alábbi példa egy SSH-kulcspárt hoz létre az RSA-titkosítással, és 2048-es hosszúságú:

    ```console
    ssh-keygen -t rsa -b 2048
    ```

Az SSH-kulcsok létrehozásával kapcsolatos további információkért lásd: [ssh-kulcsok létrehozása és kezelése az Azure-ban történő hitelesítéshez][ssh-keys].

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure gyorsindítási sablontárból](https://azure.microsoft.com/resources/templates/101-aks/) származik.

:::code language="json" source="~/quickstart-templates/101-aks/azuredeploy.json":::

További AK-mintákért tekintse meg az AK gyors üzembe helyezési [sablonok][aks-quickstart-templates] webhelyét.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. A következő gombra kattintva jelentkezzen be az Azure-ba, és nyisson meg egy sablont.

    [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aks%2Fazuredeploy.json)

2. Válassza ki vagy adja meg a következő értékeket.

    Ebben a rövid útmutatóban hagyja meg az operációsrendszer- *lemez mérete (GB*), az *ügynökök száma*, az ügynök virtuálisgép- *mérete*, az *operációs rendszer típusa* és a *Kubernetes-verzió* alapértelmezett értékeit. Adja meg a saját értékeit a következő sablon paramétereinek:

    * **Előfizetés**: válasszon ki egy Azure-előfizetést.
    * **Erőforráscsoport**: válassza az **új létrehozása** lehetőséget. Adja meg az erőforráscsoport egyedi nevét, például *myResourceGroup*, majd kattintson **az OK gombra**.
    * **Hely**: válasszon egy helyet, például az **USA keleti** régióját.
    * **Fürt neve**: adjon meg egy egyedi nevet az AK-fürthöz, például *myAKSCluster*.
    * **DNS-előtag**: adjon meg egy egyedi DNS-előtagot a fürthöz, például *myakscluster*.
    * **Linux-rendszergazdai Felhasználónév**: adjon meg egy felhasználónevet az SSH-val való kapcsolódáshoz, például: *azureuser*.
    * **Nyilvános SSH RSA-kulcs**: másolja és illessze be az SSH-kulcspár *nyilvános* részét (alapértelmezés szerint a *~/.ssh/id_rsa. pub* fájl tartalma).

    ![Resource Manager-sablon Azure Kubernetes Service-fürt létrehozásához a portálon](./media/kubernetes-walkthrough-rm-template/create-aks-cluster-using-template-portal.png)

3. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.

Az AKS-fürt létrehozása eltarthat néhány percig. Várjon, amíg a fürt sikeresen üzembe helyezhető, mielőtt továbblép a következő lépésre.

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

### <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

Kubernetes-fürt kezeléséhez használja a [Kubectl][kubectl]Kubernetes parancssori ügyfelet. `kubectl` már telepítve van a Azure Cloud Shell használata esetén. 

1. Telepítse `kubectl` helyileg az az az [AK install-CLI][az-aks-install-cli] paranccsal:

    ```azurecli
    az aks install-cli
    ```

2. Konfigurálja `kubectl` úgy a Kubernetes-fürthöz való kapcsolódást az az [AK Get-hitelesítőadats][az-aks-get-credentials] parancs használatával. Ez a parancs letölti a hitelesítő adatokat, és konfigurálja a Kubernetes CLI-t a használatára.

    ```azurecli-interactive
    az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
    ```

3. A [kubectl Get][kubectl-get] paranccsal ellenőrizze a fürthöz való kapcsolódást. Ez a parancs a fürtcsomópontok listáját adja vissza.

    ```console
    kubectl get nodes
    ```

    A kimenet az előző lépésekben létrehozott csomópontokat jeleníti meg. Győződjön meg arról, hogy az összes csomópont állapota *kész*:

    ```output
    NAME                       STATUS   ROLES   AGE     VERSION
    aks-agentpool-41324942-0   Ready    agent   6m44s   v1.12.6    
    aks-agentpool-41324942-1   Ready    agent   6m46s   v1.12.6
    aks-agentpool-41324942-2   Ready    agent   6m45s   v1.12.6
    ```

### <a name="run-the-application"></a>Az alkalmazás futtatása

A [Kubernetes jegyzékfájl][kubernetes-deployment] határozza meg a fürt kívánt állapotát, például hogy melyik tároló lemezképei futnak. 

Ebben a rövid útmutatóban egy jegyzékfájlt fog használni az [Azure vote-alkalmazás][azure-vote-app]futtatásához szükséges összes objektum létrehozásához. Ez a jegyzékfájl két [Kubernetes-telepítést][kubernetes-deployment]tartalmaz:
* A minta Azure vote Python-alkalmazások.
* Egy Redis-példány. 

A rendszer két [Kubernetes szolgáltatást][kubernetes-service] is létrehoz:
* Belső szolgáltatás a Redis-példányhoz.
* Egy külső szolgáltatás, amely hozzáfér az Azure vote alkalmazáshoz az internetről.

1. Hozzon létre egy `azure-vote.yaml` nevű fájlt.
    * Ha a Azure Cloud Shell használja, akkor ez a fájl a vagy a használatával hozható létre, `vi` `nano` Ha virtuális vagy fizikai rendszeren dolgozik
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

### <a name="test-the-application"></a>Az alkalmazás tesztelése

Az alkalmazás futtatásakor a Kubernetes szolgáltatás az előtér-alkalmazást az internethez teszi elérhetővé. A folyamat eltarthat pár percig.

Figyelje a folyamatot a [kubectl Get Service][kubectl-get] paranccsal az `--watch` argumentummal.

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

![Az Azure Vote keresését ábrázoló kép](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

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
[azure-dev-spaces]: ../dev-spaces/index.yml
[aks-quickstart-templates]: https://azure.microsoft.com/resources/templates/?term=Azure+Kubernetes+Service

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: ../azure-monitor/containers/container-insights-onboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks#az-aks-browse
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks#az-aks-install-cli
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[ssh-keys]: ../virtual-machines/linux/create-ssh-keys-detailed.md
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
