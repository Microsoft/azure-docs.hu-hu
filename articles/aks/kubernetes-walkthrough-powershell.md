---
title: 'Gyors útmutató: AK-fürt üzembe helyezése a PowerShell használatával'
description: Megtudhatja, hogyan hozhat létre gyorsan Kubernetes-fürtöt, hogyan helyezhet üzembe egy alkalmazást, és hogyan figyelheti a teljesítményt az Azure Kubernetes szolgáltatásban (ak) a PowerShell használatával.
services: container-service
ms.topic: quickstart
ms.date: 01/13/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 131469a955190561d8854aad4a7f77c8ca15a222
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100578775"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-cluster-using-powershell"></a>Gyors útmutató: Azure Kubernetes Service-fürt üzembe helyezése a PowerShell használatával

Ebben a rövid útmutatóban egy Azure Kubernetes-szolgáltatási (ak-) fürtöt helyez üzembe a PowerShell használatával. Az AK egy felügyelt Kubernetes szolgáltatás, amely lehetővé teszi fürtök gyors üzembe helyezését és kezelését. A fürtben fut egy többtárolós alkalmazás, amely tartalmazza a webes felületet és a Redis-példányt. Ezután megtudhatja, hogyan figyelheti a fürt állapotát és az alkalmazást futtató hüvelyeket.

A Windows Server-csomópontok készletének létrehozásával kapcsolatos további tudnivalókért tekintse meg a [Windows Server-tárolókat támogató AK-fürt létrehozása][windows-container-powershell]című témakört.

![Az Azure Kubernetes Service-ben üzembe helyezett szavazási alkalmazás](./media/kubernetes-walkthrough-powershell/voting-app-deployed-in-azure-kubernetes-service.png)

A rövid útmutató feltételezi, hogy rendelkezik a Kubernetes használatára vonatkozó alapvető ismeretekkel. További információ: [Az Azure Kubernetes Service (ak) Kubernetes alapfogalmai][kubernetes-concepts].

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

Ha a PowerShell helyi használatát választja, akkor ehhez a cikkhez telepítenie kell az az PowerShell-modult, és csatlakoznia kell az Azure-fiókjához a [AzAccount](/powershell/module/az.accounts/Connect-AzAccount) parancsmag használatával. Az az PowerShell-modul telepítésével kapcsolatos további információkért lásd: [Install Azure PowerShell][install-azure-powershell].

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Ha több Azure-előfizetéssel rendelkezik, válassza ki a megfelelő előfizetést, amelyben az erőforrásokat számlázni kell. Válasszon egy adott előfizetés-azonosítót a [set-AzContext](/powershell/module/az.accounts/set-azcontext) parancsmag használatával.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az [Azure-erőforráscsoport](../azure-resource-manager/management/overview.md) olyan logikai csoport, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Az erőforráscsoportok létrehozásakor meg kell adnia egy helyet. Ez a hely határozza meg, hogy az erőforráscsoport metaadatai hol vannak tárolva, és az erőforrások hol futnak az Azure-ban, ha nem ad meg másik régiót az erőforrások létrehozásakor. Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup][new-azresourcegroup] parancsmag használatával.

A következő példában létrehozunk egy **myResourceGroup** nevű erőforráscsoportot a **eastus** régióban.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location eastus
```

A következő példa kimenete azt mutatja, hogy az erőforráscsoport sikeresen létrejött:

```plaintext
ResourceGroupName : myResourceGroup
Location          : eastus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup
```

## <a name="create-aks-cluster"></a>AKS-fürt létrehozása

Az `ssh-keygen` SSH-kulcspár létrehozásához használja a parancssori segédprogramot. További részletekért lásd a [gyors lépések: SSH nyilvános magánhálózati kulcspár létrehozása és használata az Azure-ban linuxos virtuális gépekhez](../virtual-machines/linux/mac-create-ssh-keys.md)című témakört.

A [New-AzAks][new-azaks] parancsmag használatával hozzon létre egy AK-fürtöt. A következő példa egy **myAKSCluster** nevű fürtöt hoz létre egy csomóponttal. Alapértelmezés szerint a tárolók Azure Monitor is engedélyezve van. Ez több percet is igénybe vehet.

> [!NOTE]
> AK-fürt létrehozásakor a rendszer automatikusan létrehoz egy második erőforráscsoportot az AK-erőforrások tárolásához. További információ: [miért jön létre két erőforráscsoport az AK-val?](./faq.md#why-are-two-resource-groups-created-with-aks)

```azurepowershell-interactive
New-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster -NodeCount 1
```

Néhány perc elteltével a parancs befejeződik, és visszaadja a fürtre vonatkozó információkat.

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

A Kubernetes-fürtök kezeléséhez a [kubectl][kubectl], a Kubernetes parancssori ügyfélprogramot kell használnia. Ha Azure Cloud Shellt használ, `kubectl` már telepítve van. `kubectl`A helyi telepítéshez használja a következő `Install-AzAksKubectl` parancsmagot:

```azurepowershell
Install-AzAksKubectl
```

A `kubectl` Kubernetes-fürthöz való kapcsolódás konfigurálásához használja az [import-AzAksCredential][import-azakscredential] parancsmagot. A következő példa a hitelesítő adatokat tölti le, és konfigurálja a Kubernetes CLI-t a használatára.

```azurepowershell-interactive
Import-AzAksCredential -ResourceGroupName myResourceGroup -Name myAKSCluster
```

A fürthöz való csatlakozás ellenőrzéséhez használja a [kubectl get][kubectl-get] parancsot a fürtcsomópontok listájának lekéréséhez.

```azurepowershell-interactive
.\kubectl get nodes
```

A következő példakimenet az előző lépésekben létrehozott csomópontot mutatja be. Győződjön meg arról, hogy a csomópont állapota **kész**:

```plaintext
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.15.10
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

A Kubernetes jegyzékfájl a fürt kívánt állapotát határozza meg, például a tároló lemezképeit. Ebben a rövid útmutatóban egy jegyzékfájlt használunk az Azure Vote-alkalmazás futtatásához szükséges összes objektum létrehozásához. Ez a jegyzékfájl két [Kubernetes-telepítést][kubernetes-deployment] tartalmaz – egyet az Azure-beli szavazás Python-alkalmazásaihoz, a másikat pedig egy Redis-példányhoz. Két [Kubernetes-szolgáltatás is létrehozva – egy belső szolgáltatás a Redis-példányhoz, és egy külső szolgáltatás, amely hozzáfér az Azure vote alkalmazáshoz az internetről.

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

```azurepowershell-interactive
.\kubectl apply -f azure-vote.yaml
```

A következő példa kimenete a sikeresen létrehozott központi telepítéseket és szolgáltatásokat mutatja:

```plaintext
deployment.apps/azure-vote-back created
service/azure-vote-back created
deployment.apps/azure-vote-front created
service/azure-vote-front created
```

## <a name="test-the-application"></a>Az alkalmazás tesztelése

Az alkalmazás futása során a Kubernetes szolgáltatás az alkalmazás felületét az internethez teszi elérhetővé.
A folyamat eltarthat pár percig.

A folyamat állapotának monitorozásához használja [kubectl get service][kubectl-get] parancsot a `--watch` argumentummal.

```azurepowershell-interactive
.\kubectl get service azure-vote-front --watch
```

Kezdetben a **külső IP-cím** az **Azure-vote-elülső** szolgáltatáshoz **függőben** jelenik meg.

```plaintext
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Ha a **külső IP-** cím **függőben** ÁLLAPOTRÓL tényleges nyilvános IP-címről változik, akkor a `CTRL-C` figyelési folyamat leállításához használja a következőt: `kubectl` . A következő példa kimenete a szolgáltatáshoz hozzárendelt érvényes nyilvános IP-címet jeleníti meg:

```plaintext
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Az Azure vote alkalmazás működés közbeni megtekintéséhez nyisson meg egy webböngészőt a szolgáltatás külső IP-címére.

![Az Azure Kubernetes Service-ben üzembe helyezett szavazási alkalmazás](./media/kubernetes-walkthrough-powershell/voting-app-deployed-in-azure-kubernetes-service.png)

Az AK-fürt létrehozásakor a [tárolók Azure monitor](../azure-monitor/containers/container-insights-overview.md) a fürt csomópontjai és a hüvelyek állapotára vonatkozó mérőszámok rögzítésére lettek beállítva. Ezek az állapotmetrikák elérhetők az Azure Portalon.

## <a name="delete-the-cluster"></a>A fürt törlése

Az Azure-költségek elkerülése érdekében törölje a szükségtelen erőforrásokat. Ha a fürtre már nincs szükség, a [Remove-AzResourceGroup][remove-azresourcegroup] parancsmag használatával távolítsa el az erőforráscsoportot, a Container Service-t és az összes kapcsolódó erőforrást.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
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
