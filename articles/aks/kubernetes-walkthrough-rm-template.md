---
title: Rövid útmutató – Azure Kubernetes-szolgáltatás (ak) fürt létrehozása
description: Megtudhatja, hogyan hozhat létre gyorsan Kubernetes-fürtöt egy Azure Resource Manager sablonnal, és hogyan helyezhet üzembe alkalmazást az Azure Kubernetes szolgáltatásban (ak)
services: container-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.custom: mvc,subject-armqs, devx-track-azurecli
ms.openlocfilehash: 56bacf1ae68081d5822fdb0e80762926d4eb581c
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102173732"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-aks-cluster-using-an-arm-template"></a>Gyors útmutató: Azure Kubernetes-szolgáltatás (ak) fürt üzembe helyezése ARM-sablon használatával

Az Azure Kubernetes Service (ak) egy felügyelt Kubernetes szolgáltatás, amely lehetővé teszi fürtök gyors üzembe helyezését és kezelését. Ebben a rövid útmutatóban egy AK-fürtöt telepít Azure Resource Manager sablon (ARM-sablon) használatával. A fürtben fut egy többtárolós alkalmazás, amely tartalmazza a webes kezelőfelületet és a Redis-példányt.

![Az Azure Vote keresését ábrázoló kép](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

A rövid útmutató feltételezi, hogy rendelkezik a Kubernetes használatára vonatkozó alapvető ismeretekkel. További információ: [Az Azure Kubernetes Service (ak) Kubernetes alapfogalmai][kubernetes-concepts].

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-aks%2Fazuredeploy.json)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.61 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

- Az AK-fürtök Resource Manager-sablonnal történő létrehozásához meg kell adnia egy nyilvános SSH-kulcsot és egy Azure Active Directory egyszerű szolgáltatást. Azt is megteheti, hogy az engedélyekhez egy egyszerű szolgáltatásnév helyett [felügyelt identitást](use-managed-identity.md) is használhat. Ha ezekre az erőforrásokra szüksége van, tekintse meg a következő szakaszt: Ellenkező esetben ugorjon a [sablon áttekintése](#review-the-template) szakaszra.

### <a name="create-an-ssh-key-pair"></a>SSH-kulcs létrehozása

Az AK-csomópontok eléréséhez SSH kulcspár használatával csatlakozhat. Használja az `ssh-keygen` parancsot az SSH nyilvános és titkos kulcs fájljainak létrehozásához. Alapértelmezés szerint ezek a fájlok a *~/.ssh* könyvtárban jönnek létre. Ha a megadott helyen létezik egy azonos nevű SSH-kulcspár, a rendszer felülírja ezeket a fájlokat.

[https://shell.azure.com](https://shell.azure.com)Nyissa meg a Cloud shellt a böngészőben.

Az alábbi parancs egy SSH-kulcspárt hoz létre RSA-titkosítással és 2048-es hosszúságú használatával:

```console
ssh-keygen -t rsa -b 2048
```

Az SSH-kulcsok létrehozásával kapcsolatos további információkért lásd: [ssh-kulcsok létrehozása és kezelése az Azure-ban történő hitelesítéshez][ssh-keys].

### <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

Ahhoz, hogy egy AKS-fürt kommunikálhasson más Azure-erőforrásokkal, Azure Active Directory-szolgáltatásnevet kell használnia. Hozzon létre egy szolgáltatásnevet az [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] paranccsal. A `--skip-assignment` paraméter korlátozza a további engedélyek hozzárendelését. Alapértelmezés szerint ez az egyszerű szolgáltatás egy évig érvényes. Vegye figyelembe, hogy egy egyszerű szolgáltatásnév helyett felügyelt identitást is használhat. További információ: [felügyelt identitások használata](use-managed-identity.md).

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

A kimenet a következő példához hasonló:

```json
{
  "appId": "8b1ede42-d407-46c2-a1bc-6b213b04295f",
  "displayName": "azure-cli-2019-04-19-21-42-11",
  "name": "http://azure-cli-2019-04-19-21-42-11",
  "password": "27e5ac58-81b0-46c1-bd87-85b4ef622682",
  "tenant": "73f978cf-87f2-41bf-92ab-2e7ce012db57"
}
```

Jegyezze fel az *appID* és a *password* értékét. A következő lépésekben szükség lesz ezekre az értékekre.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure gyorsindítási sablontárból](https://azure.microsoft.com/resources/templates/101-aks/) származik.

:::code language="json" source="~/quickstart-templates/101-aks/azuredeploy.json":::

További AK-mintákért tekintse meg az AK gyors üzembe helyezési [sablonok][aks-quickstart-templates] webhelyét.

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

1. Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához.

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
    * **Egyszerű szolgáltatásnév ügyfél-azonosítója**: másolja és illessze be az egyszerű szolgáltatásnév *AppID* a `az ad sp create-for-rbac` paranccsal.
    * **Egyszerű szolgáltatásnév ügyfél titka**: másolja és illessze be az egyszerű szolgáltatás *jelszavát* a `az ad sp create-for-rbac` parancsból.
    * Elfogadom **a fenti feltételeket és kikötéseket**: jelölje be ezt a jelölőnégyzetet az egyeztetéshez.

    ![Resource Manager-sablon Azure Kubernetes Service-fürt létrehozásához a portálon](./media/kubernetes-walkthrough-rm-template/create-aks-cluster-using-template-portal.png)

3. Válassza a **Beszerzés** lehetőséget.

Az AKS-fürt létrehozása eltarthat néhány percig. Várjon, amíg a fürt sikeresen üzembe helyezhető, mielőtt továbblép a következő lépésre.

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

### <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

A Kubernetes-fürtök kezeléséhez a [kubectl][kubectl], a Kubernetes parancssori ügyfélprogramot kell használnia. Ha Azure Cloud Shellt használ, `kubectl` már telepítve van. A helyi telepítéshez `kubectl` használja az az [AK install-CLI][az-aks-install-cli] parancsot:

```azurecli
az aks install-cli
```

Az [az aks get-credentials][az-aks-get-credentials] paranccsal konfigurálható `kubectl` a Kubernetes-fürthöz való csatlakozásra. Ez a parancs letölti a hitelesítő adatokat, és konfigurálja a Kubernetes CLI-t a használatára.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

A fürthöz való csatlakozás ellenőrzéséhez használja a [kubectl get][kubectl-get] parancsot a fürtcsomópontok listájának lekéréséhez.

```console
kubectl get nodes
```

A következő példa kimenetében az előző lépésekben létrehozott csomópontok láthatók. Győződjön meg arról, hogy az összes csomópont állapota *kész*:

```output
NAME                       STATUS   ROLES   AGE     VERSION
aks-agentpool-41324942-0   Ready    agent   6m44s   v1.12.6
aks-agentpool-41324942-1   Ready    agent   6m46s   v1.12.6
aks-agentpool-41324942-2   Ready    agent   6m45s   v1.12.6
```

### <a name="run-the-application"></a>Az alkalmazás futtatása

A Kubernetes jegyzékfájl a fürt kívánt állapotát határozza meg, például a tároló lemezképeit. Ebben a rövid útmutatóban egy jegyzékfájlt használunk az Azure Vote-alkalmazás futtatásához szükséges összes objektum létrehozásához. Ez a jegyzékfájl két [Kubernetes-telepítést][kubernetes-deployment] tartalmaz – egyet az Azure-beli szavazás Python-alkalmazásaihoz, a másikat pedig egy Redis-példányhoz. Két [Kubernetes][kubernetes-service] -szolgáltatás is létrejön – egy belső szolgáltatás a Redis-példányhoz, és egy külső szolgáltatás, amely az Azure vote alkalmazást az internetről éri el.

Hozzon létre egy nevű fájlt `azure-vote.yaml` , és másolja a következő YAML-definícióba. Ha a Azure Cloud Shell használja, akkor ez a fájl a vagy a használatával hozható létre, `vi` `nano` Ha virtuális vagy fizikai rendszeren dolgozik:

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

### <a name="test-the-application"></a>Az alkalmazás tesztelése

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

![Az Azure Vote keresését ábrázoló kép](media/container-service-kubernetes-walkthrough/azure-voting-application.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a fürtre már nincs szükség, az [az group delete][az-group-delete] paranccsal törölheti az erőforráscsoportot, a tárolószolgáltatást és az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> A fürt törlésekor az AKS-fürt által használt Azure Active Directory-szolgáltatásnév nem lesz eltávolítva. A szolgáltatásnév eltávolításának lépéseiért lásd [az AKS-szolgáltatásnevekre vonatkozó szempontokat és a szolgáltatásnevek törlését][sp-delete] ismertető cikket. Felügyelt identitás használata esetén az identitást a platform felügyeli, és nem szükséges az eltávolítás.

## <a name="get-the-code"></a>A kód letöltése

Ebben a rövid útmutatóban előre létrehozott tároló-lemezképeket használtak a Kubernetes központi telepítésének létrehozásához. A kapcsolódó alkalmazáskód, Docker-fájl és Kubernetes-jegyzékfájl a GitHubon érhetőek el.

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy Kubernetes-fürtöt és azon egy többtárolós alkalmazást helyezett üzembe. Nyissa meg a létrehozott fürt [Kubernetes webes irányítópultját][kubernetes-dashboard] .

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
