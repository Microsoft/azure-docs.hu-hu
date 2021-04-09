---
title: 'Gyors útmutató: AK-fürt üzembe helyezése a PowerShell használatával'
description: Megtudhatja, hogyan hozhat létre gyorsan Kubernetes-fürtöt, hogyan helyezhet üzembe egy alkalmazást, és hogyan figyelheti a teljesítményt az Azure Kubernetes szolgáltatásban (ak) a PowerShell használatával.
services: container-service
ms.topic: quickstart
ms.date: 03/15/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2b61c791390200beac4a18422a4de58dd94fa711
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103492897"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-cluster-using-powershell"></a>Gyors útmutató: Azure Kubernetes Service-fürt üzembe helyezése a PowerShell használatával

Az Azure Kubernetes Service (ak) egy felügyelt Kubernetes szolgáltatás, amely lehetővé teszi fürtök gyors üzembe helyezését és kezelését. Ebben a rövid útmutatóban a következőket fogja megtekinteni:
* AK-fürt üzembe helyezése a PowerShell használatával. 
* Futtasson egy többtárolós alkalmazást webes kezelőfelülettel és Redis-példánnyal a fürtben. 
* A fürt és az alkalmazást futtató hüvelyek állapotának figyelése.

A Windows Server-csomópontok készletének létrehozásával kapcsolatos további tudnivalókért tekintse meg a [Windows Server-tárolókat támogató AK-fürt létrehozása][windows-container-powershell]című témakört.

![Az Azure Kubernetes Service-ben üzembe helyezett szavazási alkalmazás](./media/kubernetes-walkthrough-powershell/voting-app-deployed-in-azure-kubernetes-service.png)

A rövid útmutató feltételezi, hogy rendelkezik a Kubernetes használatára vonatkozó alapvető ismeretekkel. További információ: [Az Azure Kubernetes Service (ak) Kubernetes alapfogalmai][kubernetes-concepts].

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

Ha helyileg futtatja a PowerShellt, telepítse az az PowerShell-modult, és kapcsolódjon az Azure-fiókjához a [AzAccount](/powershell/module/az.accounts/Connect-AzAccount) parancsmag használatával. Az az PowerShell-modul telepítésével kapcsolatos további információkért lásd: [Install Azure PowerShell][install-azure-powershell].

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Ha több Azure-előfizetéssel rendelkezik, válassza ki azt a megfelelő előfizetési azonosítót, amelyben az erőforrásokat a [set-AzContext](/powershell/module/az.accounts/set-azcontext) parancsmag használatával kell számlázni.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az [Azure-erőforráscsoport](../azure-resource-manager/management/overview.md) olyan logikai csoport, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Ha létrehoz egy erőforráscsoportot, a rendszer kérni fogja a hely megadását. Ez a hely a következő: 
* Az erőforráscsoport metaadatainak tárolási helye.
* Ahol az erőforrások az Azure-ban futnak, ha nem ad meg egy másik régiót az erőforrás létrehozásakor. 

A következő példában létrehozunk egy **myResourceGroup** nevű erőforráscsoportot a **eastus** régióban.

Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup][new-azresourcegroup] parancsmag használatával.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location eastus
```

A sikeresen létrehozott erőforráscsoport kimenete:

```plaintext
ResourceGroupName : myResourceGroup
Location          : eastus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup
```

## <a name="create-aks-cluster"></a>AKS-fürt létrehozása

1. SSH kulcspár létrehozása a `ssh-keygen` parancssori segédprogram használatával. 
    * További részletekért lásd a [gyors lépések: SSH nyilvános magánhálózati kulcspár létrehozása és használata az Azure-ban linuxos virtuális gépekhez](../virtual-machines/linux/mac-create-ssh-keys.md)című témakört.

1. Hozzon létre egy AK-fürtöt a [New-AzAks][new-azaks] parancsmag használatával. A tárolók Azure Monitor alapértelmezés szerint engedélyezve van.

    A következő példa egy **myAKSCluster** nevű fürtöt hoz létre egy csomóponttal. 

    ```azurepowershell-interactive
    New-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster -NodeCount 1
    ```

Néhány perc elteltével a parancs befejeződik, és visszaadja a fürtre vonatkozó információkat.

> [!NOTE]
> Ha AK-fürtöt hoz létre, a rendszer automatikusan létrehoz egy második erőforráscsoportot az AK-erőforrások tárolásához. További információ: [miért jön létre két erőforráscsoport az AK-val?](./faq.md#why-are-two-resource-groups-created-with-aks)

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

Kubernetes-fürt kezeléséhez használja a [Kubectl][kubectl]Kubernetes parancssori ügyfelet. `kubectl` már telepítve van a Azure Cloud Shell használata esetén. 

1. Telepítse `kubectl` helyileg a `Install-AzAksKubectl` parancsmag használatával:

    ```azurepowershell
    Install-AzAksKubectl
    ```

2. Konfigurálja `kubectl` úgy a Kubernetes-fürthöz való kapcsolódáshoz, hogy az [import-AzAksCredential][import-azakscredential] parancsmagot használja. A következő parancsmag a hitelesítő adatokat tölti le, és konfigurálja a Kubernetes CLI-t a használatára.

    ```azurepowershell-interactive
    Import-AzAksCredential -ResourceGroupName myResourceGroup -Name myAKSCluster
    ```

3. A [kubectl Get][kubectl-get] paranccsal ellenőrizze a fürthöz való kapcsolódást. Ez a parancs a fürtcsomópontok listáját adja vissza.

    ```azurepowershell-interactive
    .\kubectl get nodes
    ```

    A kimenet az előző lépésekben létrehozott egyetlen csomópontot jeleníti meg. Győződjön meg arról, hogy a csomópont állapota *kész*:

    ```plaintext
    NAME                       STATUS   ROLES   AGE     VERSION
    aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.15.10
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

    ```azurepowershell-interactive
    .\kubectl apply -f azure-vote.yaml
    ```

    A kimenet a sikeresen létrehozott központi telepítéseket és szolgáltatásokat mutatja:

    ```plaintext
    deployment.apps/azure-vote-back created
    service/azure-vote-back created
    deployment.apps/azure-vote-front created
    service/azure-vote-front created
    ```

## <a name="test-the-application"></a>Az alkalmazás tesztelése

Az alkalmazás futtatásakor a Kubernetes szolgáltatás az előtér-alkalmazást az internethez teszi elérhetővé. A folyamat eltarthat pár percig.

Figyelje a folyamatot a [kubectl Get Service][kubectl-get] paranccsal az `--watch` argumentummal.

```azurepowershell-interactive
.\kubectl get service azure-vote-front --watch
```

A szolgáltatás **külső IP-** kimenete `azure-vote-front` kezdetben *függőben* jelenik meg.

```plaintext
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Ha a **külső IP-** cím *függőben* ÁLLAPOTRÓL tényleges nyilvános IP-címről változik, akkor a `CTRL-C` figyelési folyamat leállításához használja a következőt: `kubectl` . A következő példa kimenete a szolgáltatáshoz hozzárendelt érvényes nyilvános IP-címet jeleníti meg:

```plaintext
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Az Azure vote alkalmazás működés közbeni megtekintéséhez nyisson meg egy webböngészőt a szolgáltatás külső IP-címére.

![Az Azure Kubernetes Service-ben üzembe helyezett szavazási alkalmazás](./media/kubernetes-walkthrough-powershell/voting-app-deployed-in-azure-kubernetes-service.png)

A Azure Portal tárolók Azure Monitor által rögzített fürtcsomópontok és hüvelyek állapotának megtekintése. 

## <a name="delete-the-cluster"></a>A fürt törlése

Az Azure-költségek elkerülése érdekében törölje a szükségtelen erőforrásokat. A [Remove-AzResourceGroup][remove-azresourcegroup] parancsmag használatával távolítsa el az erőforráscsoportot, a Container Service-t és az összes kapcsolódó erőforrást.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
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
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[azure-dev-spaces]: ../dev-spaces/index.yml
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git

<!-- LINKS - internal -->
[windows-container-powershell]: windows-container-powershell.md
[kubernetes-concepts]: concepts-clusters-workloads.md
[install-azure-powershell]: /powershell/azure/install-az-ps
[new-azresourcegroup]: /powershell/module/az.resources/new-azresourcegroup
[new-azaks]: /powershell/module/az.aks/new-azaks
[import-azakscredential]: /powershell/module/az.aks/import-azakscredential
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[remove-azresourcegroup]: /powershell/module/az.resources/remove-azresourcegroup
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[kubernetes-dashboard]: kubernetes-dashboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
