---
title: SSH-n keresztüli bejelentkezés az Azure Kubernetes Service- (AKS-) fürtcsomópontokba
description: Ismerje meg, hogyan hozhat létre SSH-kapcsolatokat az Azure Kubernetes Service (ak) fürtcsomópontok használatával a hibaelhárítási és karbantartási feladatok elvégzéséhez.
services: container-service
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: 7455b98348f2b8c40f2ffc125abe1297af88fbd8
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102034455"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>SSH-n keresztüli csatlakozás az Azure Kubernetes Service- (AKS-) fürtcsomópontokhoz karbantartás és hibaelhárítás céljából

Az Azure Kubernetes-szolgáltatás (ak) fürtjének életciklusa során előfordulhat, hogy hozzá kell férnie egy AK-csomóponthoz. Ez a hozzáférés lehet karbantartási, naplózási vagy egyéb hibaelhárítási művelet. Az AK-csomópontok SSH-val, így a Windows Server-csomópontokkal is elérhetők. [Távoli asztali protokoll (RDP) kapcsolatok használatával is csatlakozhat a Windows Server-csomópontokhoz][aks-windows-rdp]. Biztonsági okokból az AK-csomópontok nem jelennek meg az interneten. Ha SSH-t használ az AK-csomópontokhoz, használja a magánhálózati IP-címet.

Ez a cikk bemutatja, hogyan hozhat létre SSH-kapcsolatokat egy AK-csomóponttal magánhálózati IP-címeik használatával.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AK-fürttel. Ha AK-fürtre van szüksége, tekintse meg az AK gyors üzembe helyezését [Az Azure CLI használatával][aks-quickstart-cli] vagy [a Azure Portal használatával][aks-quickstart-portal].

Alapértelmezés szerint az SSH-kulcsok beszerzése vagy létrehozása, majd hozzáadása a csomópontokhoz, amikor egy AK-fürtöt hoz létre. Ez a cikk bemutatja, hogyan adhat meg különböző SSH-kulcsokat, mint az AK-fürt létrehozásakor használt SSH-kulcsokat. A cikk azt is bemutatja, hogyan határozhatja meg a csomópont magánhálózati IP-címét, és hogyan kapcsolódhat hozzá az SSH használatával. Ha nem kell másik SSH-kulcsot megadnia, akkor kihagyhatja a lépést az SSH nyilvános kulcsnak a csomóponthoz való hozzáadásához.

A cikk azt is feltételezi, hogy van egy SSH-kulcsa. Az SSH [-kulcs MacOS vagy Linux][ssh-nix] vagy [Windows rendszerű][ssh-windows]használatával is létrehozható. Ha a kulcspár létrehozásához a PuTTY Gen-t használja, mentse a kulcspárt egy OpenSSH formátumban az alapértelmezett Putty titkos kulcs formátuma (. PPK fájl) helyett.

Szüksége lesz az Azure CLI 2.0.64 vagy újabb verziójára is, valamint a telepítésre és konfigurálásra. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="configure-virtual-machine-scale-set-based-aks-clusters-for-ssh-access"></a>Virtuálisgép-méretezési csoport-alapú AK-fürtök konfigurálása SSH-hozzáféréshez

A virtuálisgép-méretezési csoport SSH-hozzáféréshez való konfigurálásához keresse meg a fürt virtuálisgép-méretezési csoportjának nevét, és adja hozzá az SSH nyilvános kulcsát a méretezési csoporthoz.

A méretezési csoport nevének lekéréséhez használja az az [vmss List][az-vmss-list] [parancsot az AK][az-aks-show] -fürt erőforráscsoport-nevének lekéréséhez.

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
SCALE_SET_NAME=$(az vmss list --resource-group $CLUSTER_RESOURCE_GROUP --query '[0].name' -o tsv)
```

A fenti példa a *myResourceGroup* lévő *myAKSCluster* tartozó fürterőforrás-csoport nevét rendeli hozzá a következőhöz: *CLUSTER_RESOURCE_GROUP*. A példa ezután a *CLUSTER_RESOURCE_GROUP* használatával listázza a méretezési csoport nevét, és hozzárendeli a *SCALE_SET_NAMEhoz*.

> [!IMPORTANT]
> Jelenleg csak az Azure CLI használatával kell frissítenie az SSH-kulcsokat a virtuálisgép-méretezési csoport-alapú AK-fürtökhöz.
> 
> Linux-csomópontok esetén az SSH-kulcsok jelenleg csak az Azure CLI használatával vehetők fel. Ha SSH használatával szeretne csatlakozni egy Windows Server-csomóponthoz, használja az AK-fürt létrehozásakor megadott SSH-kulcsokat, és hagyja ki a következő parancsokat az SSH nyilvános kulcs hozzáadásához. Továbbra is szüksége lesz a hibakereséshez használni kívánt csomópont IP-címére, amely a szakasz utolsó parancsában látható. Azt is megteheti, hogy az SSH használata helyett a [Távoli asztal protokoll (RDP) kapcsolatain keresztül tud csatlakozni a Windows Server-csomópontokhoz][aks-windows-rdp] .

Az SSH-kulcsok virtuálisgép-méretezési csoport csomópontjaihoz való hozzáadásához használja az az [vmss Extension set][az-vmss-extension-set] és [az az vmss Update-instances][az-vmss-update-instances] parancsot.

```azurecli-interactive
az vmss extension set  \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --vmss-name $SCALE_SET_NAME \
    --name VMAccessForLinux \
    --publisher Microsoft.OSTCExtensions \
    --version 1.4 \
    --protected-settings "{\"username\":\"azureuser\", \"ssh_key\":\"$(cat ~/.ssh/id_rsa.pub)\"}"

az vmss update-instances --instance-ids '*' \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --name $SCALE_SET_NAME
```

A fenti példa az előző parancsokban szereplő *CLUSTER_RESOURCE_GROUP* és *SCALE_SET_NAME* változókat használja. A fenti példa a *~/.ssh/id_rsa. pub* néven is használja az SSH nyilvános kulcs helyét.

> [!NOTE]
> Alapértelmezés szerint az AK-csomópontok felhasználóneve a következő: *azureuser*.

Miután hozzáadta az SSH nyilvános kulcsát a méretezési csoporthoz, az SSH-t a méretezési csoport egy csomópontos virtuális gépén az IP-címével is megadhatja. Tekintse meg az AK-fürtcsomópontok magánhálózati IP-címeit a [kubectl Get paranccsal][kubectl-get].

```console
kubectl get nodes -o wide
```

A következő példa kimenet a fürt összes csomópontjának belső IP-címét jeleníti meg, beleértve a Windows Server-csomópontot is.

```console
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Jegyezze fel a hibához használni kívánt csomópont belső IP-címét.

Ha a csomópontot SSH használatával szeretné elérni, kövesse az [SSH-kapcsolat létrehozása](#create-the-ssh-connection)című témakör lépéseit.

## <a name="configure-virtual-machine-availability-set-based-aks-clusters-for-ssh-access"></a>Virtuális gépek rendelkezésre állási csoport-alapú AK-fürtök konfigurálása SSH-hozzáféréshez

A virtuális gépek rendelkezésre állási csoportjának SSH-hozzáféréshez való konfigurálásához keresse meg a fürt Linux-csomópontjának nevét, és adja hozzá az SSH nyilvános kulcsát az adott csomóponthoz.

Az az [AK show][az-aks-show] paranccsal lekérheti az AK-fürt erőforráscsoport-nevét, majd az az [VM List][az-vm-list] paranccsal megtekintheti a fürt Linux-csomópontjának virtuális gép nevét.

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az vm list --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

A fenti példa a *myResourceGroup* lévő *myAKSCluster* tartozó fürterőforrás-csoport nevét rendeli hozzá a következőhöz: *CLUSTER_RESOURCE_GROUP*. A példa ezután a *CLUSTER_RESOURCE_GROUP* használatával listázza a virtuális gép nevét. A példa kimenete a virtuális gép nevét mutatja:

```
Name                      ResourceGroup                                  Location
------------------------  ---------------------------------------------  ----------
aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
```

Az SSH-kulcsok csomóponthoz való hozzáadásához használja az az [VM User Update][az-vm-user-update] parancsot.

```azurecli-interactive
az vm user update \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --name aks-nodepool1-79590246-0 \
    --username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

A fenti példa a *CLUSTER_RESOURCE_GROUP* változót és a csomópont virtuális gép nevét használja az előző parancsokból. A fenti példa a *~/.ssh/id_rsa. pub* néven is használja az SSH nyilvános kulcs helyét. Az elérési út megadása helyett a nyilvános SSH-kulcs tartalmát is használhatja.

> [!NOTE]
> Alapértelmezés szerint az AK-csomópontok felhasználóneve a következő: *azureuser*.

Miután hozzáadta az SSH nyilvános kulcsát a csomópont virtuális géphez, a saját IP-címével SSH-t telepíthet az adott virtuális gépre. Az az [VM List-IP-Addresss][az-vm-list-ip-addresses] paranccsal tekintheti meg egy AK-fürt csomópontjának magánhálózati IP-címét.

```azurecli-interactive
az vm list-ip-addresses --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

A fenti példában az előző parancsokban beállított *CLUSTER_RESOURCE_GROUP* változót használja. A következő példa kimenete az AK-csomópontok magánhálózati IP-címeit mutatja be:

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

## <a name="create-the-ssh-connection"></a>Az SSH-kapcsolatok létrehozása

Egy AK-csomóponthoz való SSH-kapcsolatok létrehozásához egy segítő Pod-t kell futtatnia az AK-fürtben. Ez a segítő Pod SSH-hozzáférést biztosít a fürthöz, majd az SSH-csomópont további elérését. A segítő Pod létrehozásához és használatához hajtsa végre a következő lépéseket:

1. Futtasson egy `debian` tároló-rendszerképet, és csatolja hozzá a terminál-munkamenetet. Ezzel a tárolóval létrehozhat egy SSH-munkamenetet az AK-fürt bármely csomópontjának használatával:

    ```console
    kubectl run -it --rm aks-ssh --image=debian
    ```

    > [!TIP]
    > Ha Windows Server-csomópontokat használ, adjon hozzá egy csomópont-választót a parancshoz, hogy egy Linux-csomóponton ütemezze a Debian-tárolót:
    >
    > ```console
    > kubectl run -it --rm aks-ssh --image=debian --overrides='{"apiVersion":"v1","spec":{"nodeSelector":{"beta.kubernetes.io/os":"linux"}}}'
    > ```

1. Ha a terminál-munkamenet csatlakozik a tárolóhoz, telepítsen egy SSH-ügyfelet a következő használatával `apt-get` :

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. Nyisson meg egy új terminált, amely nem csatlakozik a tárolóhoz, másolja a titkos SSH-kulcsot a Helper Pod-ba. Ez a titkos kulcs az SSH-nak az AK-csomópontba való létrehozásához használatos. 

   Ha szükséges, módosítsa a *~/.ssh/id_rsa* a saját SSH-kulcsának helyére:

    ```console
    kubectl cp ~/.ssh/id_rsa $(kubectl get pod -l run=aks-ssh -o jsonpath='{.items[0].metadata.name}'):/id_rsa
    ```

1. Térjen vissza a terminál-munkamenetbe a tárolóba, frissítse a másolt `id_rsa` titkos SSH-kulcs engedélyeit, hogy az csak a felhasználó számára írásvédett legyen:

    ```console
    chmod 0400 id_rsa
    ```

1. Hozzon létre egy SSH-kapcsolatokat az AK-csomóponthoz. Az AK-csomópontok alapértelmezett felhasználóneve a következő: *azureuser*. Fogadja el a kérést a kapcsolódás folytatásához, mivel az SSH-kulcs először megbízható. Ekkor megjelenik az AK-csomópont bash-üzenete:

    ```console
    $ ssh -i id_rsa azureuser@10.240.0.4

    ECDSA key fingerprint is SHA256:A6rnRkfpG21TaZ8XmQCCgdi9G/MYIMc+gFAuY9RUY70.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added '10.240.0.4' (ECDSA) to the list of known hosts.

    Welcome to Ubuntu 16.04.5 LTS (GNU/Linux 4.15.0-1018-azure x86_64)

     * Documentation:  https://help.ubuntu.com
     * Management:     https://landscape.canonical.com
     * Support:        https://ubuntu.com/advantage

      Get cloud support with Ubuntu Advantage Cloud Guest:
        https://www.ubuntu.com/business/services/cloud

    [...]

    azureuser@aks-nodepool1-79590246-0:~$
    ```

## <a name="remove-ssh-access"></a>SSH-hozzáférés eltávolítása

Ha elkészült, `exit` az SSH-munkamenetet, majd `exit` az interaktív tároló munkamenetét. A tároló-munkamenet bezárásakor a rendszer törli az AK-fürtről az SSH-hozzáféréshez használt Pod-hozzáférést.

## <a name="next-steps"></a>Következő lépések

Ha további hibaelhárítási adatokra van szüksége, megtekintheti [a kubelet-naplókat][view-kubelet-logs] , vagy [megtekintheti a Kubernetes fő csomópontjának naplóit][view-master-logs].

<!-- EXTERNAL LINKS -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-vm-list]: /cli/azure/vm#az-vm-list
[az-vm-user-update]: /cli/azure/vm/user#az-vm-user-update
[az-vm-list-ip-addresses]: /cli/azure/vm#az-vm-list-ip-addresses
[view-kubelet-logs]: kubelet-logs.md
[view-master-logs]: ./view-control-plane-logs.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-windows-rdp]: rdp.md
[ssh-nix]: ../virtual-machines/linux/mac-create-ssh-keys.md
[ssh-windows]: ../virtual-machines/linux/ssh-from-windows.md
[az-vmss-list]: /cli/azure/vmss#az-vmss-list
[az-vmss-extension-set]: /cli/azure/vmss/extension#az-vmss-extension-set
[az-vmss-update-instances]: /cli/azure/vmss#az-vmss-update-instances