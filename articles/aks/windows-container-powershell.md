---
title: Windows Server-tároló létrehozása AK-fürtön a PowerShell használatával
description: Megtudhatja, hogyan hozhat létre gyorsan Kubernetes-fürtöt, hogyan helyezhet üzembe egy alkalmazást egy Windows Server-tárolóban az Azure Kubernetes szolgáltatásban (ak) a PowerShell használatával.
services: container-service
ms.topic: article
ms.date: 03/12/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b877ecbdca06ff73d152e1b491e993798a99f98a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103233514"
---
# <a name="create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-powershell"></a>Windows Server-tároló létrehozása Azure Kubernetes-szolgáltatásbeli (ak-) fürtön a PowerShell használatával

Az Azure Kubernetes Service (ak) egy felügyelt Kubernetes szolgáltatás, amely lehetővé teszi fürtök gyors üzembe helyezését és kezelését. Ebben a cikkben egy AK-fürtöt helyez üzembe a PowerShell használatával. Egy minta alkalmazást is üzembe helyezhet `ASP.NET` egy Windows Server-tárolóban a fürtön.

![A ASP.NET-minta alkalmazás tallózásának képe](media/windows-container-powershell/asp-net-sample-app.png)

Ez a cikk azt feltételezi, hogy alapvető ismereteket Kubernetes a fogalmakról. További információ: [Az Azure Kubernetes Service (ak) Kubernetes alapfogalmai][kubernetes-concepts].

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

Ha a PowerShell helyi használatát választja, akkor ehhez a cikkhez telepítenie kell az az PowerShell-modult, és csatlakoznia kell az Azure-fiókjához a [AzAccount](/powershell/module/az.accounts/Connect-AzAccount) parancsmag használatával. Az az PowerShell-modul telepítésével kapcsolatos további információkért lásd: [Install Azure PowerShell][install-azure-powershell]. Az az. AK PowerShell-modult is telepítenie kell: 

```azurepowershell-interactive
Install-Module Az.Aks
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Ha több Azure-előfizetéssel rendelkezik, válassza ki a megfelelő előfizetést, amelyben az erőforrásokat számlázni kell. Válasszon egy adott előfizetés-azonosítót a [set-AzContext](/powershell/module/az.accounts/set-azcontext) parancsmag használatával.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="limitations"></a>Korlátozások

A több csomópontot támogató AK-fürtök létrehozásakor és kezelésekor a következő korlátozások érvényesek:

* Az első csomópont-készlet nem törölhető.

A következő további korlátozások érvényesek a Windows Server Node-készletekre:

* Az AK-fürt legfeljebb 10 csomópont-készletet tartalmazhat.
* Az AK-fürt legfeljebb 100 csomópontot tartalmazhat az egyes csomópont-készletekben.
* A Windows Server-csomópontok készletének neve legfeljebb 6 karakterből állhat.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az [Azure-erőforráscsoport](../azure-resource-manager/management/overview.md) olyan logikai csoport, amelyben a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Az erőforráscsoportok létrehozásakor meg kell adnia egy helyet. Ez a hely határozza meg, hogy az erőforráscsoport metaadatai hol vannak tárolva, és az erőforrások hol futnak az Azure-ban, ha nem ad meg másik régiót az erőforrások létrehozásakor. Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup][new-azresourcegroup] parancsmag használatával.

A következő példában létrehozunk egy **myResourceGroup** nevű erőforráscsoportot az **eastus** helyen.

> [!NOTE]
> Ez a cikk PowerShell-szintaxist használ az oktatóanyag parancsaihoz. Ha Azure Cloud Shell használ, győződjön meg arról, hogy a Cloud Shell ablak bal felső részén található legördülő lista a **PowerShell** értékre van állítva.

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

## <a name="create-an-aks-cluster"></a>AKS-fürt létrehozása

Az `ssh-keygen` SSH-kulcspár létrehozásához használja a parancssori segédprogramot. További részletekért lásd a [gyors lépések: SSH nyilvános magánhálózati kulcspár létrehozása és használata az Azure-ban linuxos virtuális gépekhez](../virtual-machines/linux/mac-create-ssh-keys.md)című témakört.

Ha olyan AK-fürtöt szeretne futtatni, amely támogatja a Windows Server-tárolók csomópont-készleteit, a fürtnek az [Azure CNI][azure-cni-about] (Advanced) hálózati beépülő modult használó hálózati házirendet kell használnia. További információ a szükséges alhálózati tartományok és hálózati megfontolások megtervezéséhez: az [Azure CNI hálózatkezelésének konfigurálása][use-advanced-networking]. Az alábbi [New-AzAks][new-azaks] parancsmag használatával hozzon létre egy **myAKSCluster** nevű AK-fürtöt. A következő példa létrehozza a szükséges hálózati erőforrásokat, ha nem léteznek.

> [!NOTE]
> Annak biztosítása érdekében, hogy a fürt megbízhatóan működjön, legalább 2 (két) csomópontot kell futtatnia az alapértelmezett csomópont-készletben.

```azurepowershell-interactive
$Username = Read-Host -Prompt 'Please create a username for the administrator credentials on your Windows Server containers: '
$Password = Read-Host -Prompt 'Please create a password for the administrator credentials on your Windows Server containers: ' -AsSecureString
New-AzAksCluster -ResourceGroupName myResourceGroup -Name myAKSCluster -NodeCount 2 -NetworkPlugin azure -NodeVmSetType VirtualMachineScaleSets -WindowsProfileAdminUserName $Username -WindowsProfileAdminUserPassword $Password
```

> [!Note]
> Ha nem tudja létrehozni az AK-fürtöt, mert a verzió nem támogatott ebben a régióban, akkor a `Get-AzAksVersion -Location eastus` paranccsal megkeresheti a régió támogatott verzióinak listáját.

Néhány perc elteltével a parancs befejeződik, és visszaadja a fürtre vonatkozó információkat. A fürt esetenként több percig is eltarthat. Ezekben az esetekben akár 10 percet is igénybe vehet.

## <a name="add-a-windows-server-node-pool"></a>Windows Server Node-készlet hozzáadása

Alapértelmezés szerint a rendszer egy AK-fürtöt hoz létre egy olyan csomópont-készlettel, amely képes Linux-tárolók futtatására. A `New-AzAksNodePool` parancsmag használatával hozzáadhat egy olyan csomópont-készletet, amely Windows Server-tárolókat is futtathat a Linux-csomópontos készlettel együtt.

```azurepowershell-interactive
New-AzAksNodePool -ResourceGroupName myResourceGroup -ClusterName myAKSCluster -VmSetType VirtualMachineScaleSets -OsType Windows -Name npwin
```

A fenti parancs létrehoz egy **npwin** nevű új csomópont-készletet, és hozzáadja azt a **myAKSCluster**. Ha Windows Server-tárolók futtatásához hoz létre csomópont-készletet, a **VmSize** alapértelmezett értéke **Standard_D2s_v3**. Ha úgy dönt, hogy beállítja a **VmSize** paramétert, ellenőrizze a [korlátozott][restricted-vm-sizes]virtuálisgép-méretek listáját. A minimális ajánlott méret **Standard_D2s_v3**. Az előző parancs az alapértelmezett alhálózatot is használja a futtatáskor létrehozott alapértelmezett vnet `New-AzAks` .

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

A Kubernetes-fürtök kezeléséhez a [kubectl][kubectl], a Kubernetes parancssori ügyfélprogramot kell használnia. Ha Azure Cloud Shellt használ, `kubectl` már telepítve van. `kubectl`A helyi telepítéshez használja a következő `Install-AzAksKubectl` parancsmagot:

```azurepowershell-interactive
Install-AzAksKubectl
```

A `kubectl` Kubernetes-fürthöz való kapcsolódás konfigurálásához használja az [import-AzAksCredential][import-azakscredential] parancsmagot. Ez a parancs letölti a hitelesítő adatokat, és konfigurálja a Kubernetes CLI-t a használatára.

```azurepowershell-interactive
Import-AzAksCredential -ResourceGroupName myResourceGroup -Name myAKSCluster
```

A fürthöz való csatlakozás ellenőrzéséhez használja a [kubectl get][kubectl-get] parancsot a fürtcsomópontok listájának lekéréséhez.

```azurepowershell-interactive
kubectl get nodes
```

A következő példa kimenete a fürt összes csomópontját megjeleníti. Győződjön meg arról, hogy az összes csomópont állapota **kész**:

```plaintext
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.16.7
aksnpwin987654                      Ready    agent   108s   v1.16.7
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

A Kubernetes jegyzékfájl a fürt kívánt állapotát határozza meg, például a tároló lemezképeit. Ebben a cikkben egy jegyzékfájlt használunk a ASP.NET-minta alkalmazás Windows Server-tárolóban való futtatásához szükséges összes objektum létrehozásához. Ez a jegyzékfájl egy [Kubernetes-telepítést][kubernetes-deployment] tartalmaz a ASP.net-minta alkalmazáshoz és egy külső [Kubernetes szolgáltatáshoz][kubernetes-service] az alkalmazás internetről való eléréséhez.

A ASP.NET minta alkalmazás a [.NET-keretrendszer mintáinak][dotnet-samples] részeként van megadva, és Windows Server-tárolóban fut. Az AK megköveteli, hogy a Windows Server-tárolók a **Windows server 2019** -es vagy újabb rendszerképein alapulnak. A Kubernetes jegyzékfájljának meg kell határoznia egy [csomópont-választót][node-selector] is, hogy a ASP.net-minta alkalmazás Pod-t futtasson a Windows Server-tárolókat futtató csomóponton.

Hozzon létre egy nevű fájlt `sample.yaml` , és másolja a következő YAML-definícióba. Ha a Azure Cloud Shell használja, akkor ez a fájl a vagy a használatával hozható létre, `vi` `nano` Ha virtuális vagy fizikai rendszeren dolgozik:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sample
  labels:
    app: sample
spec:
  replicas: 1
  template:
    metadata:
      name: sample
      labels:
        app: sample
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": windows
      containers:
      - name: sample
        image: mcr.microsoft.com/dotnet/framework/samples:aspnetapp
        resources:
          limits:
            cpu: 1
            memory: 800M
          requests:
            cpu: .1
            memory: 300M
        ports:
          - containerPort: 80
  selector:
    matchLabels:
      app: sample
---
apiVersion: v1
kind: Service
metadata:
  name: sample
spec:
  type: LoadBalancer
  ports:
  - protocol: TCP
    port: 80
  selector:
    app: sample
```

Telepítse az alkalmazást a [kubectl Apply][kubectl-apply] paranccsal, és adja meg a YAML-jegyzékfájl nevét:

```azurepowershell-interactive
kubectl apply -f sample.yaml
```

A következő példa kimenete a központi telepítés és a szolgáltatás sikeres létrehozását mutatja be:

```plaintext
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>Az alkalmazás tesztelése

Az alkalmazás futása során a Kubernetes szolgáltatás az alkalmazás felületét az internethez teszi elérhetővé.
A folyamat eltarthat pár percig. Alkalmanként a szolgáltatás több percig is eltarthat. Ezekben az esetekben akár 10 percet is igénybe vehet.

A folyamat állapotának monitorozásához használja [kubectl get service][kubectl-get] parancsot a `--watch` argumentummal.

```azurepowershell-interactive
kubectl get service sample --watch
```

Kezdetben a **minta** szolgáltatás **külső IP-címe** **függőben** jelenik meg.

```plaintext
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Ha a **külső IP-** cím **függőben** ÁLLAPOTRÓL tényleges nyilvános IP-címről változik, akkor a `CTRL-C` figyelési folyamat leállításához használja a következőt: `kubectl` . A következő példa kimenete a szolgáltatáshoz hozzárendelt érvényes nyilvános IP-címet jeleníti meg:

```plaintext
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

A minta alkalmazás működés közbeni megtekintéséhez nyisson meg egy webböngészőt a szolgáltatás külső IP-címére.

![A ASP.NET-minta alkalmazás tallózásának képe](media/windows-container-powershell/asp-net-sample-app.png)

> [!Note]
> Ha a lap betöltésére tett kísérlet közben kapcsolati időtúllépést kap, ellenőrizze, hogy a minta alkalmazás készen áll-e a következő parancsra `kubectl get pods --watch` . Előfordulhat, hogy a Windows-tárolót nem indítja el a külső IP-cím elérhetővé tételének időpontjában.

## <a name="delete-cluster"></a>Fürt törlése

Ha a fürtre már nincs szükség, a [Remove-AzResourceGroup][remove-azresourcegroup] parancsmag használatával távolítsa el az erőforráscsoportot, a Container Service-t és az összes kapcsolódó erőforrást.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

> [!NOTE]
> A fürt törlésekor az AKS-fürt által használt Azure Active Directory-szolgáltatásnév nem lesz eltávolítva. A szolgáltatásnév eltávolításának lépéseiért lásd [az AKS-szolgáltatásnevekre vonatkozó szempontokat és a szolgáltatásnevek törlését][sp-delete] ismertető cikket. Felügyelt identitás használata esetén az identitást a platform felügyeli, és nem szükséges az eltávolítás.

## <a name="next-steps"></a>További lépések

Ebben a cikkben üzembe helyezett egy Kubernetes-fürtöt, és üzembe helyezett egy `ASP.NET` minta alkalmazást egy Windows Server-tárolóban. Nyissa meg a létrehozott fürt [Kubernetes webes irányítópultját][kubernetes-dashboard] .

Az AKS-sel kapcsolatos további információkért és a kódtól az üzembe helyezésig terjedő teljes útmutatóért folytassa a Kubernetes-fürtöket bemutató oktatóanyaggal.

> [!div class="nextstepaction"]
> [AK-oktatóanyag][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[install-azure-powershell]: /powershell/azure/install-az-ps
[new-azresourcegroup]: /powershell/module/az.resources/new-azresourcegroup
[azure-cni-about]: concepts-network.md#azure-cni-advanced-networking
[use-advanced-networking]: configure-azure-cni.md
[new-azaks]: /powershell/module/az.aks/new-azaks
[restricted-vm-sizes]: quotas-skus-regions.md#restricted-vm-sizes
[import-azakscredential]: /powershell/module/az.aks/import-azakscredential
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[remove-azresourcegroup]: /powershell/module/az.resources/remove-azresourcegroup
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[kubernetes-dashboard]: kubernetes-dashboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
