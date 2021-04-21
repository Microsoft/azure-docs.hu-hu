---
title: Windows Server-tároló létrehozása AKS-fürtön az Azure CLI használatával
description: Megtudhatja, hogyan hozhat létre gyorsan Kubernetes-fürtöt, hogyan helyezhet üzembe egy alkalmazást egy Windows Server-tárolóban Azure Kubernetes Service (AKS) az Azure CLI használatával.
services: container-service
ms.topic: article
ms.date: 07/16/2020
ms.openlocfilehash: 617590a3f482e246b8af5db6dd906591c16b20fa
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769426"
---
# <a name="create-a-windows-server-container-on-an-azure-kubernetes-service-aks-cluster-using-the-azure-cli"></a>Windows Server-tároló létrehozása egy Azure Kubernetes Service -fürtön az Azure CLI használatával

Azure Kubernetes Service (AKS) egy felügyelt Kubernetes-szolgáltatás, amely lehetővé teszi a fürtök gyors üzembe helyezését és kezelését. Ebben a cikkben egy AKS-fürtöt fog üzembe helyezni az Azure CLI használatával. Emellett üzembe helyez egy ASP.NET mintaalkalmazást egy Windows Server-tárolóban a fürtön.

![Mintaalkalmazás ASP.NET tallózásának képe](media/windows-container/asp-net-sample-app.png)

Ez a cikk feltételezi a Kubernetes alapfogalmainak alapszintű megértését. További információkért lásd a Kubernetes alapfogalmait a Azure Kubernetes Service [(AKS) című oldalon.][kubernetes-concepts]

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

### <a name="limitations"></a>Korlátozások

A több csomópontkészletet támogató AKS-fürtök létrehozásakor és kezelésekor a következő korlátozások érvényesek:

* Az első csomópontkészlet nem törölhető.

A Windows Server-csomópontkészletek a következő további korlátozásokat vonatkoznak:

* Az AKS-fürt legfeljebb 10 csomópontkészletet tartalmazhat.
* Az AKS-fürt minden csomópontkészletben legfeljebb 100 csomóponttal lehet.
* A Windows Server-csomópontkészlet neve legfeljebb 6 karakterből állhat.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai csoport, amelyben az Azure-erőforrások üzembe helyezése és kezelése zajlik. Az erőforráscsoportok létrehozásakor meg kell adnia egy helyet. Ezen a helyen tárolja a rendszer az erőforráscsoport metaadatait, és az erőforrások is ott futnak az Azure-ban, ha nem ad meg másik régiót az erőforrás létrehozása során. Hozzon létre egy erőforráscsoportot [az az group create paranccsal.][az-group-create]

A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen.

> [!NOTE]
> Ez a cikk a Bash szintaxisát használja az oktatóanyagban használt parancsokhoz.
> Ha a windowsos Azure Cloud Shell, győződjön meg arról, hogy a bal felső sarokban található legördülő menü Cloud Shell Bash van **beállítva.**

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Az alábbi példakimeneten a sikeresen létrehozott erőforráscsoport látható:

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

A Windows Server-tárolók csomópontkészleteit támogató [AKS-fürt][azure-cni-about] futtatásához a fürtnek olyan hálózati házirendet kell használnia, amely Azure CNI (speciális) hálózati beépülő modult használ. A szükséges alhálózati tartományok és hálózati szempontok tervezésével kapcsolatos részletesebb információkért lásd: [hálózati Azure CNI konfigurálása.][use-advanced-networking] Az [az aks create paranccsal][az-aks-create] hozzon létre egy *myAKSCluster nevű AKS-fürtöt.* Ez a parancs létrehozza a szükséges hálózati erőforrásokat, ha azok még nem léteznek.

* A fürt két csomóponttal van konfigurálva.
* A és a paraméter beállítja a fürtön létrehozott Windows Server-tárolók rendszergazdai hitelesítő adatait, és meg kell felelnie a `--windows-admin-password` `--windows-admin-username` Windows Server [jelszókövetelményeinek.][windows-server-password] Ha nem adja meg a *windows-admin-password* paramétert, a rendszer kérni fogja egy érték megadását.
* A csomópontkészlet a következőt `VirtualMachineScaleSets` használja: .

> [!NOTE]
> A fürt megbízható működéséhez legalább 2 (két) csomópontot kell futtatnia az alapértelmezett csomópontkészletben.

Hozzon létre egy felhasználónevet, amely rendszergazdai hitelesítő adatokként szolgál a fürtön található Windows Server-tárolókhoz. Az alábbi parancsok egy felhasználónevet kérnek, és WINDOWS_USERNAME egy későbbi parancsban való használatra beállítják (ne feledje, hogy a cikkben megadott parancsok egy BASH-rendszerhéjban vannak megadva).

```azurecli-interactive
echo "Please enter the username to use as administrator credentials for Windows Server containers on your cluster: " && read WINDOWS_USERNAME
```

Hozza létre a fürtöt, és adja meg a `--windows-admin-username` paramétert. Az alábbi példaparancs egy fürtöt hoz létre az előző *WINDOWS_USERNAME* parancsban beállított értékkel. Másik lehetőségként közvetlenül a paraméterben is meg lehet adni egy másik felhasználónevet a *WINDOWS_USERNAME.* A következő parancs arra is kérni fogja, hogy hozzon létre egy jelszót a fürtön található Windows Server-tárolók rendszergazdai hitelesítő adataihoz. Másik lehetőségként használhatja a *windows-admin-password* paramétert, és itt megadhatja a saját értékét.

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
> Ha jelszóérvényesítési hibát kap, ellenőrizze, hogy a beállított jelszó megfelel-e a [Windows Server jelszókövetelményeinek.][windows-server-password] Ha a jelszó megfelel a követelményeknek, próbálja meg létrehozni az erőforráscsoportot egy másik régióban. Ezután próbálja meg létrehozni a fürtöt az új erőforráscsoporttal.

Néhány perc múlva befejeződik a parancs, és visszaadja a fürttel kapcsolatos JSON-formátumú információkat. A fürt kiépítése esetenként több percig is eltarthat. Ezekben az esetekben akár 10 percig is eltarthat.

## <a name="add-a-windows-server-node-pool"></a>Windows Server-csomópontkészlet hozzáadása

Alapértelmezés szerint az AKS-fürtök Linux-tárolók futtatására képes csomópontkészletekkel vannak létrehozva. A `az aks nodepool add` paranccsal adjon hozzá egy további csomópontkészletet, amely Windows Server-tárolókat futtathat a Linux-csomópontkészlet mellett.

```azurecli
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --os-type Windows \
    --name npwin \
    --node-count 1
```

A fenti parancs létrehoz egy *új, npwin* nevű csomópontkészletet, és hozzáadja a *myAKSClusterhez.* Windows Server-tárolók futtatására szolgáló csomópontkészlet létrehozásakor a *node-vm-size* alapértelmezett értéke *a következő: Standard_D2s_v3.* Ha a *node-vm-size* paramétert választja, tekintse meg a korlátozott [virtuálisgép-méretek listáját.][restricted-vm-sizes] A minimális javasolt méret a *Standard_D2s_v3.* A fenti parancs az alapértelmezett virtuális hálózat alapértelmezett alhálózatát is használja, amely a futtatásakor jött `az aks create` létre.

## <a name="connect-to-the-cluster"></a>Csatlakozás a fürthöz

A Kubernetes-fürtök kezeléséhez használja a [kubectl][kubectl], a Kubernetes parancssori ügyfelét. Ha az alkalmazást Azure Cloud Shell, `kubectl` a már telepítve van. A helyi `kubectl` telepítéshez használja az [az aks install-cli][az-aks-install-cli] parancsot:

```azurecli
az aks install-cli
```

Az [az aks get-credentials][az-aks-get-credentials] paranccsal konfigurálható `kubectl` a Kubernetes-fürthöz való csatlakozásra. Ez a parancs letölti a hitelesítő adatokat, és konfigurálja a Kubernetes parancssori felületét azok használatára.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

A fürthöz való csatlakozás ellenőrzéséhez használja a [kubectl get][kubectl-get] parancsot a fürtcsomópontok listájának lekéréséhez.

```console
kubectl get nodes
```

Az alábbi példakimenet a fürt összes csomópontját megjeleníti. Győződjön meg arról, hogy az összes csomópont Ready *(Kész) állapotú:*

```output
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.16.9
aksnpwin987654                      Ready    agent   108s   v1.16.9
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

A Kubernetes-jegyzékfájl meghatározza a fürt célállapotát, például hogy milyen tárolólemezképeket kell futtatni. Ebben a cikkben egy jegyzékfájlt használunk a mintaalkalmazás Windows Server-tárolóban ASP.NET futtatásához szükséges összes objektum létrehozásához. Ez a jegyzék egy [Kubernetes-telepítést][kubernetes-deployment] tartalmaz a ASP.NET mintaalkalmazáshoz és egy külső [Kubernetes-szolgáltatáshoz,][kubernetes-service] amely az internetről fér hozzá az alkalmazáshoz.

A ASP.NET mintaalkalmazás a mintaminták részeként [.NET-keretrendszer,][dotnet-samples] és Egy Windows Server-tárolóban fut. Az AKS megköveteli, hogy a Windows Server-tárolók *Windows Server 2019* vagy annál nagyobb rendszerképeken alapulnak. A Kubernetes-jegyzékfájlnak [][node-selector] egy csomópontválasztót is meg kell határoznia, amely arra adja meg az AKS-fürtöt, hogy futtassa a ASP.NET mintaalkalmazás podját egy Windows Server-tárolókat futtatni képes csomóponton.

Hozzon létre egy nevű `sample.yaml` fájlt, és másolja be az alábbi YAML-definíciót. Ha a Azure Cloud Shell, ez a fájl a vagy a használatával, vagy virtuális vagy fizikai rendszeren `vi` `nano` való munkavégzéshez használható:

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

Telepítse az alkalmazást a [kubectl apply paranccsal,][kubectl-apply] és adja meg a YAML-jegyzék nevét:

```console
kubectl apply -f sample.yaml
```

Az alábbi példakimeneten a sikeresen létrehozott üzembe helyezés és szolgáltatás látható:

```output
deployment.apps/sample created
service/sample created
```

## <a name="test-the-application"></a>Az alkalmazás tesztelése

Az alkalmazás futtatásakor egy Kubernetes-szolgáltatás teszi elérhetővé az alkalmazás előtere számára az internetet. A folyamat eltarthat pár percig. A szolgáltatás kiépítése esetenként több percig is eltarthat. Ezekben az esetekben akár 10 percig is eltarthat.

A folyamat állapotának monitorozásához használja [kubectl get service][kubectl-get] parancsot a `--watch` argumentummal.

```console
kubectl get service sample --watch
```

Kezdetben a *mintaszolgáltatás EXTERNAL-IP (KÜLSŐ* IP-címe)  függőben *lévőként jelenik meg.*

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
sample             LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Ha *az EXTERNAL-IP*  cím függőben értékről tényleges nyilvános IP-címre változik, a használatával állítsa le a `CTRL-C` `kubectl` figyelés folyamatát. Az alábbi példakimenet egy érvényes, a szolgáltatáshoz rendelt nyilvános IP-címet mutat be:

```output
sample  LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

A mintaalkalmazás használatban való megnyitásához nyisson meg egy webböngészőt a szolgáltatás külső IP-címére.

![Mintaalkalmazás ASP.NET képe](media/windows-container/asp-net-sample-app.png)

> [!Note]
> Ha kapcsolati időtúllépést kap az oldal betöltésekor, ellenőrizze, hogy a mintaalkalmazás készen áll-e a következő paranccsal: [kubectl get pods --watch]. Előfordulhat, hogy a Windows-tároló nem lesz elindítva, amikor a külső IP-cím elérhetővé válik.

## <a name="delete-cluster"></a>Fürt törlése

Ha a fürtre már nincs szükség, az [az group delete][az-group-delete] paranccsal törölheti az erőforráscsoportot, a tárolószolgáltatást és az összes kapcsolódó erőforrást.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

> [!NOTE]
> A fürt törlésekor az AKS-fürt által használt Azure Active Directory-szolgáltatásnév nem lesz eltávolítva. A szolgáltatásnév eltávolításának lépéseiért lásd [az AKS-szolgáltatásnevekre vonatkozó szempontokat és a szolgáltatásnevek törlését][sp-delete] ismertető cikket. Ha felügyelt identitást használt, az identitást a platform kezeli, és nem igényel eltávolítást.

## <a name="next-steps"></a>További lépések

Ebben a cikkben üzembe helyezett egy Kubernetes-fürtöt, és üzembe helyezett egy ASP.NET-mintaalkalmazást egy Windows Server-tárolóban. [Az újonnan létrehozott fürt Kubernetes webes][kubernetes-dashboard] irányítópultjának elérése.

Az AKS-sel kapcsolatos további információkért és a kódtól az üzembe helyezésig terjedő teljes útmutatóért folytassa a Kubernetes-fürtöket bemutató oktatóanyaggal.

> [!div class="nextstepaction"]
> [AKS-oktatóanyag][aks-tutorial]

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
[az-aks-browse]: /cli/azure/aks#az_aks_browse
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[az-provider-register]: /cli/azure/provider#az_provider_register
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
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[windows-server-password]: /windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements#reference