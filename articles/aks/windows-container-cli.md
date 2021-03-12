---
title: Windows Server-tároló létrehozása AK-fürtön az Azure CLI használatával
description: Ismerje meg, hogyan hozhat létre gyorsan Kubernetes-fürtöt, hogyan helyezhet üzembe egy alkalmazást egy Windows Server-tárolóban az Azure Kubernetes szolgáltatásban (ak) az Azure CLI használatával.
services: container-service
ms.topic: article
ms.date: 07/16/2020
ms.openlocfilehash: 13b4fbd21bb348d1ef79a3ca68128869115745cc
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200909"
---
# <a name="create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>Windows Server-tároló létrehozása Azure Kubernetes szolgáltatásbeli (ak) fürtön az Azure CLI használatával

Az Azure Kubernetes Service (ak) egy felügyelt Kubernetes szolgáltatás, amely lehetővé teszi fürtök gyors üzembe helyezését és kezelését. Ebben a cikkben egy AK-fürtöt helyez üzembe az Azure CLI használatával. Egy ASP.NET-minta alkalmazást is üzembe helyezhet egy Windows Server-tárolóban a fürtön.

![A ASP.NET-minta alkalmazás tallózásának képe](media/windows-container/asp-net-sample-app.png)

Ez a cikk azt feltételezi, hogy alapvető ismereteket Kubernetes a fogalmakról. További információ: [Az Azure Kubernetes Service (ak) Kubernetes alapfogalmai][kubernetes-concepts].

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

### <a name="limitations"></a>Korlátozások

A több csomópontot támogató AK-fürtök létrehozásakor és kezelésekor a következő korlátozások érvényesek:

* Az első csomópont-készlet nem törölhető.

A következő további korlátozások érvényesek a Windows Server Node-készletekre:

* Az AK-fürt legfeljebb 10 csomópont-készletet tartalmazhat.
* Az AK-fürt legfeljebb 100 csomópontot tartalmazhat az egyes csomópont-készletekben.
* A Windows Server-csomópontok készletének neve legfeljebb 6 karakterből állhat.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai csoport, amelyben az Azure-erőforrások üzembe helyezése és kezelése zajlik. Az erőforráscsoportok létrehozásakor meg kell adnia egy helyet. Ez a hely határozza meg, hogy az erőforráscsoport metaadatai hol vannak tárolva, és az erőforrások hol futnak az Azure-ban, ha nem ad meg másik régiót az erőforrások létrehozásakor. Hozzon létre egy erőforráscsoportot az az [Group Create][az-group-create] paranccsal.

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

> [!NOTE]
> Ez a cikk bash-szintaxist használ az oktatóanyag parancsaihoz.
> Ha Azure Cloud Shell használ, győződjön meg arról, hogy a Cloud Shell ablak bal felső részén található legördülő lista **bash** értékre van állítva.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

A következő példa kimenete azt mutatja, hogy az erőforráscsoport sikeresen létrejött:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

## <a name="create-an-aks-cluster"></a>AKS-fürt létrehozása

Ha olyan AK-fürtöt szeretne futtatni, amely támogatja a Windows Server-tárolók csomópont-készleteit, a fürtnek az [Azure CNI][azure-cni-about] (Advanced) hálózati beépülő modult használó hálózati házirendet kell használnia. További információ a szükséges alhálózati tartományok és hálózati megfontolások megtervezéséhez: az [Azure CNI hálózatkezelésének konfigurálása][use-advanced-networking]. Az az [AK Create][az-aks-create] paranccsal hozzon létre egy *myAKSCluster* nevű AK-fürtöt. Ez a parancs létrehozza a szükséges hálózati erőforrásokat, ha nem léteznek.

* A fürt két csomóponttal van konfigurálva.
* A `--windows-admin-password` és a `--windows-admin-username` paraméterek a fürtön létrehozott Windows Server-tárolók rendszergazdai hitelesítő adatait, valamint a [Windows Server-jelszóra vonatkozó követelményeket][windows-server-password]is kielégítik. Ha nem adja meg a *Windows-admin-password* paramétert, a rendszer kérni fogja egy érték megadását.
* A csomópont-készletet használja `VirtualMachineScaleSets` .

> [!NOTE]
> Annak biztosítása érdekében, hogy a fürt megbízhatóan működjön, legalább 2 (két) csomópontot kell futtatnia az alapértelmezett csomópont-készletben.

Hozzon létre egy felhasználónevet, amelyet rendszergazdai hitelesítő adatként kell használni a Windows Server-tárolók számára a fürtön. A következő parancsok megkérik a felhasználónevek megadására és a későbbi parancsokban való használatra WINDOWS_USERNAME (ne feledje, hogy a cikkben szereplő parancsok egy BASH-rendszerhéjba kerülnek).

```azurecli-interactive
echo "Please enter the username to use as administrator credentials for Windows Server containers on your cluster: " && read WINDOWS_USERNAME
```

Hozzon létre egy fürtöt, amely biztosítja a paraméter megadását `--windows-admin-username` . A következő példában a parancs létrehoz egy fürtöt az előző parancsban beállított *WINDOWS_USERNAME* értékének használatával. Egy másik felhasználónevet is megadhat közvetlenül a paraméterben a *WINDOWS_USERNAME* használata helyett. A következő parancs azt is kéri, hogy hozzon létre egy jelszót a Windows Server-tárolók rendszergazdai hitelesítő adataihoz a fürtön. Másik lehetőségként használhatja a *Windows-admin-password* paramétert, és megadhatja a saját értékét is.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 2 \
    --enable-addons monitoring \
    --generate-ssh-keys \
    --windows-admin-username $WINDOWS_USERNAME \
    --vm-set-type VirtualMachineScaleSets \
    --network-plugin azure
```

> [!NOTE]
> Ha jelszó-érvényesítési hibát kap, ellenőrizze, hogy a beállított jelszó megfelel-e a [Windows Server-jelszó követelményeinek][windows-server-password]. Ha a Jelszó megfelel a követelményeknek, próbálja meg létrehozni az erőforráscsoportot egy másik régióban. Ezután próbálja meg létrehozni a fürtöt az új erőforrás-csoporttal.

Néhány perc elteltével a parancs befejeződik, és a fürthöz tartozó JSON-formátumú adatokat adja vissza. A fürt esetenként több percig is eltarthat. Ezekben az esetekben akár 10 percet is igénybe vehet.

## <a name="add-a-windows-server-node-pool"></a>Windows Server Node-készlet hozzáadása

Alapértelmezés szerint a rendszer egy AK-fürtöt hoz létre egy olyan csomópont-készlettel, amely képes Linux-tárolók futtatására. `az aks nodepool add`A parancs használatával hozzáadhat egy további csomópont-készletet, amely Windows Server-tárolókat is futtathat a Linux-csomópontos készlettel együtt.

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1
```

A fenti parancs létrehoz egy *npwin* nevű új csomópont-készletet, és hozzáadja azt a *myAKSCluster*. Ha Windows Server-tárolókat futtató csomópont-készletet hoz létre, akkor a *Node-VM méret* alapértelmezett értéke *Standard_D2s_v3*. Ha úgy dönt, hogy beállítja a *Node-VM-size* paramétert, ellenőrizze a korlátozott virtuálisgép- [méretek][restricted-vm-sizes]listáját. A minimális ajánlott méret *Standard_D2s_v3*. A fenti parancs a futtatáskor létrehozott alapértelmezett vnet található alapértelmezett alhálózatot is használja `az aks create` .

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

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

A következő példa kimenete a fürt összes csomópontját mutatja. Győződjön meg arról, hogy az összes csomópont állapota *kész*:

```output
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.16.9
aksnpwin987654                      Ready    agent   108s   v1.16.9
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

A Kubernetes jegyzékfájl a fürt kívánt állapotát határozza meg, például a tároló lemezképeit. Ebben a cikkben egy jegyzékfájlt használunk a ASP.NET-minta alkalmazás Windows Server-tárolóban való futtatásához szükséges összes objektum létrehozásához. Ez a jegyzékfájl egy [Kubernetes-telepítést][kubernetes-deployment] tartalmaz a ASP.net-minta alkalmazáshoz és egy külső [Kubernetes szolgáltatáshoz][kubernetes-service] az alkalmazás internetről való eléréséhez.

A ASP.NET minta alkalmazás a [.NET-keretrendszer mintáinak][dotnet-samples] részeként van megadva, és Windows Server-tárolóban fut. Az AK megköveteli, hogy a Windows Server-tárolók a *Windows server 2019* -es vagy újabb rendszerképein alapulnak. A Kubernetes jegyzékfájljának meg kell határoznia egy [csomópont-választót][node-selector] is, hogy a ASP.net-minta alkalmazás Pod-t futtasson a Windows Server-tárolókat futtató csomóponton.

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

```console
kubectl apply -f sample.yaml
```

A következő példa kimenete a központi telepítés és a szolgáltatás sikeres létrehozását mutatja be:

```output
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>Az alkalmazás tesztelése

Az alkalmazás futtatásakor a Kubernetes szolgáltatás az előtér-alkalmazást az internethez teszi elérhetővé. A folyamat eltarthat pár percig. Alkalmanként a szolgáltatás több percig is eltarthat. Ezekben az esetekben akár 10 percet is igénybe vehet.

A folyamat állapotának monitorozásához használja [kubectl get service][kubectl-get] parancsot a `--watch` argumentummal.

```console
kubectl get service sample --watch
```

Kezdetben a *minta* szolgáltatás *külső IP-címe* *függőben* jelenik meg.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Ha a *külső IP-* cím *függőben* ÁLLAPOTRÓL tényleges nyilvános IP-címről változik, akkor a `CTRL-C` figyelési folyamat leállításához használja a következőt: `kubectl` . A következő példa kimenete a szolgáltatáshoz hozzárendelt érvényes nyilvános IP-címet jeleníti meg:

```output
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

A minta alkalmazás működés közbeni megtekintéséhez nyisson meg egy webböngészőt a szolgáltatás külső IP-címére.

![A ASP.NET-minta alkalmazás tallózásának képe](media/windows-container/asp-net-sample-app.png)

> [!Note]
> Ha a lap betöltésére tett kísérlet közben kapcsolati időtúllépést kap, ellenőrizze, hogy a minta alkalmazás készen áll-e a következő paranccsal [kubectl Get hüvelyek--Watch]. Előfordulhat, hogy a Windows-tárolót nem indítja el a külső IP-cím elérhetővé tételének időpontjában.

## <a name="delete-cluster"></a>Fürt törlése

Ha a fürtre már nincs szükség, az [az group delete][az-group-delete] paranccsal törölheti az erőforráscsoportot, a tárolószolgáltatást és az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> A fürt törlésekor az AKS-fürt által használt Azure Active Directory-szolgáltatásnév nem lesz eltávolítva. A szolgáltatásnév eltávolításának lépéseiért lásd [az AKS-szolgáltatásnevekre vonatkozó szempontokat és a szolgáltatásnevek törlését][sp-delete] ismertető cikket. Felügyelt identitás használata esetén az identitást a platform felügyeli, és nem szükséges az eltávolítás.

## <a name="next-steps"></a>További lépések

Ebben a cikkben üzembe helyezett egy Kubernetes-fürtöt, és telepített egy ASP.NET-minta alkalmazást egy Windows Server-tárolóban. Nyissa meg az imént létrehozott fürthöz tartozó [Kubernetes webes irányítópultot][kubernetes-dashboard] .

Az AKS-sel kapcsolatos további információkért és a kódtól az üzembe helyezésig terjedő teljes útmutatóért folytassa a Kubernetes-fürtöket bemutató oktatóanyaggal.

> [!div class="nextstepaction"]
> [AK-oktatóanyag][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[dotnet-samples]: https://hub.docker.com/_/microsoft-dotnet-framework-samples/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md

<!-- LINKS - internal -->
[kubernetes-concepts]: concepts-clusters-workloads.md
[aks-monitor]: ../azure-monitor/containers/container-insights-onboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-browse]: /cli/azure/aks#az-aks-browse
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-provider-register]: /cli/azure/provider#az-provider-register
[azure-cli-install]: /cli/azure/install-azure-cli
[azure-cni-about]: concepts-network.md#azure-cni-advanced-networking
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[azure-portal]: https://portal.azure.com
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[kubernetes-dashboard]: kubernetes-dashboard.md
[restricted-vm-sizes]: quotas-skus-regions.md#restricted-vm-sizes
[use-advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[windows-server-password]: /windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements#reference