---
title: Rövid útmutató – Azure Kubernetes Service (AKS)-fürt létrehozása
description: Megtudhatja, hogyan hozhat létre gyorsan Kubernetes-fürtöt egy Azure Resource Manager-sablonnal, és hogyan helyezhet üzembe egy alkalmazást Azure Kubernetes Service (AKS)
services: container-service
ms.topic: quickstart
ms.date: 03/15/2021
ms.custom: mvc,subject-armqs, devx-track-azurecli
ms.openlocfilehash: 4bcaafdb1f465fb8568078dfb5bfaf988d0cd587
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764440"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-an-arm-template"></a>Rövid útmutató: Azure Kubernetes Service -fürt üzembe helyezése ARM-sablonnal

Azure Kubernetes Service (AKS) egy felügyelt Kubernetes-szolgáltatás, amely lehetővé teszi a fürtök gyors üzembe helyezését és kezelését. Ebben a rövid útmutatóban a következőt fogja:
* AKS-fürt üzembe helyezése egy Azure Resource Manager sablonnal. 
* Többtárolós alkalmazás futtatása egy webes előfeltűn és egy Redis-példánysal a fürtben. 

![Az Azure Vote keresését ábrázoló kép](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

A rövid útmutató feltételezi, hogy rendelkezik a Kubernetes használatára vonatkozó alapvető ismeretekkel. További információ: [A Kubernetes alapfogalmai Azure Kubernetes Service (AKS).][kubernetes-concepts]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aks%2Fazuredeploy.json)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.61-es vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

- Ha egy AKS-fürtöt egy Resource Manager sablonnal, meg kell adnia egy nyilvános SSH-kulcsot. Ha szüksége van erre az erőforrásra, tekintse meg a következő szakaszt; Egyéb esetben ugorjon [a Sablon áttekintése szakaszra.](#review-the-template)

### <a name="create-an-ssh-key-pair"></a>SSH-kulcs létrehozása

Az AKS-csomópontok eléréséhez egy SSH-kulcspár (nyilvános és privát) használatával csatlakozhat, amelyet az paranccsal hozhat `ssh-keygen` létre. Ezek a fájlok alapértelmezés szerint a *~/.ssh könyvtárban vannak létrehozva.* A parancs futtatása felülírja az adott helyen már meglévő `ssh-keygen` SSH-kulcspárt.

1. Nyissa meg [https://shell.azure.com](https://shell.azure.com) a Cloud Shell a böngészőben.

1. Futtassa a következő parancsot: `ssh-keygen`. Az alábbi példa RSA-titkosítással és 2048-as bithosszú SSH-kulcspárt hoz létre:

    ```console
    ssh-keygen -t rsa -b 2048
    ```

További információ az SSH-kulcsok létrehozásáról: SSH-kulcsok létrehozása és kezelése hitelesítéshez az [Azure-ban.][ssh-keys]

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure gyorsindítási sablontárból](https://azure.microsoft.com/resources/templates/101-aks/) származik.

:::code language="json" source="~/quickstart-templates/101-aks/azuredeploy.json":::

További AKS-mintákért tekintse meg az [AKS gyorsindítási sablonok webhelyét.][aks-quickstart-templates]

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Kattintson a következő gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához.

    [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aks%2Fazuredeploy.json)

2. Válassza ki vagy adja meg a következő értékeket.

    Ebben a rövid útmutatóban hagyja meg az alapértelmezett értékeket az operációs rendszer *lemezmérete GB-ban,* az ügynökök száma, az ügynök *virtuális* gépének mérete, az operációs rendszer *típusa* és a *Kubernetes verziója esetén.* Adja meg a saját értékeit a következő sablonparaméterek számára:

    * **Előfizetés:** Válasszon ki egy Azure-előfizetést.
    * **Erőforráscsoport:** Válassza az **Új létrehozása lehetőséget.** Adjon egyedi nevet az erőforráscsoportnak, például *myResourceGroup,* majd kattintson az **OK gombra.**
    * **Hely:** Válasszon ki egy helyet, például az **USA keleti régiója.**
    * **Fürt neve:** Adjon egyedi nevet az AKS-fürtnek, *például: myAKSCluster.*
    * **DNS-előtag:** Adjon meg egy egyedi DNS-előtagot a fürthöz, például *myakscluster.*
    * **Linux-rendszergazdai felhasználónév:** Adjon meg egy felhasználónevet az SSH használatával való csatlakozáshoz, *például: azureuser.*
    * **Nyilvános SSH RSA-kulcs:**  Másolja és illessze be az SSH-kulcspár nyilvános részét (alapértelmezés szerint a *~/.ssh/id_rsa.pub tartalmát).*

    ![Resource Manager sablon létrehozása Azure Kubernetes Service fürt létrehozásához a portálon](./media/kubernetes-walkthrough-rm-template/create-aks-cluster-using-template-portal.png)

3. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.

Az AKS-fürt létrehozása eltarthat néhány percig. Várja meg a fürt sikeres üzembe helyezését, mielőtt továbblép a következő lépésre.

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

### <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

Kubernetes-fürt kezeléséhez használja a Kubernetes kubectl parancssori [ügyfelét.][kubectl] `kubectl` már telepítve van, ha a Azure Cloud Shell. 

1. Helyi `kubectl` telepítés az az [aks install-cli paranccsal:][az-aks-install-cli]

    ```azurecli
    az aks install-cli
    ```

2. Konfigurálja úgy a parancsot, hogy az `kubectl` [az aks get-credentials][az-aks-get-credentials] paranccsal csatlakozzon a Kubernetes-fürthöz. Ez a parancs letölti a hitelesítő adatokat, és konfigurálja a Kubernetes parancssori felületét azok használatára.

    ```azurecli-interactive
    az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
    ```

3. Ellenőrizze a fürthöz való csatlakozást a [kubectl get paranccsal.][kubectl-get] Ez a parancs a fürtcsomópontok listáját adja vissza.

    ```console
    kubectl get nodes
    ```

    A kimenet az előző lépésekben létrehozott csomópontokat jeleníti meg. Győződjön meg arról, hogy az összes csomópont Állapota *Kész:*

    ```output
    NAME                       STATUS   ROLES   AGE     VERSION
    aks-agentpool-41324942-0   Ready    agent   6m44s   v1.12.6    
    aks-agentpool-41324942-1   Ready    agent   6m46s   v1.12.6
    aks-agentpool-41324942-2   Ready    agent   6m45s   v1.12.6
    ```

### <a name="run-the-application"></a>Az alkalmazás futtatása

A [Kubernetes-jegyzékfájl][kubernetes-deployment] határozza meg a fürt célállapotát, például hogy melyik tárolólemezképeket kell futtatni. 

Ebben a rövid útmutatóban egy jegyzékfájlt fog használni az Azure Vote alkalmazás futtatásához szükséges összes [objektum létrehozásához.][azure-vote-app] Ez a jegyzékfájl két [Kubernetes-üzemelő példányból áll:][kubernetes-deployment]
* Az Azure Vote Python-mintaalkalmazások.
* Egy Redis-példány. 

Két [Kubernetes-szolgáltatás][kubernetes-service] is létrejön:
* Belső szolgáltatás a Redis-példányhoz.
* Egy külső szolgáltatás, amely az internetről fér hozzá az Azure Vote alkalmazáshoz.

1. Hozzon létre egy `azure-vote.yaml` nevű fájlt.
    * Ha a Azure Cloud Shell használja, ez a fájl a vagy a használatával, vagy úgy használhatja, mintha egy virtuális vagy fizikai `vi` `nano` rendszeren dolgoz
1. Másolja be a következő YAML-definíciót:

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

1. Telepítse az alkalmazást a [kubectl apply paranccsal,][kubectl-apply] és adja meg a YAML-jegyzék nevét:

    ```console
    kubectl apply -f azure-vote.yaml
    ```

    A kimenet a sikeresen létrehozott üzemelő példányokat és szolgáltatásokat jeleníti meg:

    ```output
    deployment "azure-vote-back" created
    service "azure-vote-back" created
    deployment "azure-vote-front" created
    service "azure-vote-front" created
    ```

### <a name="test-the-application"></a>Az alkalmazás tesztelése

Az alkalmazás futtatásakor a Kubernetes-szolgáltatás elérhetővé teszi az alkalmazás előtere számára az internetet. A folyamat eltarthat pár percig.

A folyamat előrehaladásának figyelése a [kubectl get service paranccsal][kubectl-get] és a `--watch` argumentummal.

```console
kubectl get service azure-vote-front --watch
```

A **szolgáltatás EXTERNAL-IP** kimenete kezdetben függőben `azure-vote-front` *lesz.*

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Miután az **EXTERNAL-IP**  cím függőben értékről tényleges nyilvános IP-címre változik, a használatával állítsa le `CTRL-C` a `kubectl` figyelés folyamatát. Az alábbi példakimenet egy érvényes, a szolgáltatáshoz rendelt nyilvános IP-címet mutat be:

```output
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Az Azure Vote alkalmazás használatban való megnyitásához nyisson meg egy webböngészőt a szolgáltatás külső IP-címéhez.

![Az Azure Vote keresését ábrázoló kép](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az Azure-díjak elkerülése érdekében felesleges erőforrásokat kell megtisztítani. Az [az group delete][az-group-delete] paranccsal eltávolíthatja az erőforráscsoportot, a tárolószolgáltatást és az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> A fürt törlésekor az AKS-fürt által használt Azure Active Directory-szolgáltatásnév nem lesz eltávolítva. A szolgáltatásnév eltávolításának lépéseiért lásd [az AKS-szolgáltatásnevekre vonatkozó szempontokat és a szolgáltatásnevek törlését][sp-delete] ismertető cikket.
> 
> Ha felügyelt identitást használt, az identitást a platform kezeli, és nem igényel eltávolítást.

## <a name="get-the-code"></a>A kód letöltése

Ebben a rövid útmutatóban egy már meglévő tároló rendszerképeket használtunk egy Kubernetes-példány létrehozásához. A kapcsolódó alkalmazáskód, Docker-fájl és Kubernetes-jegyzékfájl elérhető [a GitHubon.][azure-vote-app]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban üzembe helyezett egy Kubernetes-fürtöt, majd egy többtárolós alkalmazást. [Az AKS-fürt Kubernetes webes][kubernetes-dashboard] irányítópultjának elérése.

Az AKS-sel kapcsolatos további információkért és a kódtól az üzembe helyezésig terjedő teljes útmutatóért folytassa a Kubernetes-fürtöket bemutató oktatóanyaggal.

> [!div class="nextstepaction"]
> [AKS-oktatóanyag][aks-tutorial]

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
[az-aks-browse]: /cli/azure/aks#az_aks_browse
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: /cli/azure/install-azure-cli
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[ssh-keys]: ../virtual-machines/linux/create-ssh-keys-detailed.md
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
