---
title: SSH-n keresztüli bejelentkezés az Azure Kubernetes Service- (AKS-) fürtcsomópontokba
description: Megtudhatja, hogyan hozhat létre SSH-kapcsolatot Azure Kubernetes Service (AKS) fürtcsomópontokkal hibaelhárítási és karbantartási feladatokhoz.
services: container-service
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: 8df3e8be14e258aac34881014057dd7ee7ec3239
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769534"
---
# <a name="connect-with-ssh-to-azure-kubernetes-service-aks-cluster-nodes-for-maintenance-or-troubleshooting"></a>SSH-n keresztüli csatlakozás az Azure Kubernetes Service- (AKS-) fürtcsomópontokhoz karbantartás és hibaelhárítás céljából

A Azure Kubernetes Service (AKS-) fürt életciklusa során szükség lehet egy AKS-csomópont elérésére. Ez a hozzáférés karbantartási, naplógyűjtési vagy egyéb hibaelhárítási műveletekhez lehet szükséges. Az AKS-csomópontokat SSH-kapcsolaton keresztül, beleértve a Windows Server-csomópontokat is. Távoli asztali protokoll (RDP) kapcsolatokkal is csatlakozhat [Windows Server-csomópontokhoz.][aks-windows-rdp] Biztonsági okokból az AKS-csomópontok nincsenek elérhetővé téve az interneten. Az AKS-csomópontokhoz való SSH-kapcsolathoz a magánhálózati IP-címet kell használnia.

Ez a cikk bemutatja, hogyan hozhat létre SSH-kapcsolatot egy AKS-csomóponttal a magánhálózati IP-címük használatával.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy már van AKS-fürte. Ha AKS-fürtre van szüksége, tekintse meg az AKS rövid útmutatóját az [Azure CLI][aks-quickstart-cli] vagy a [Azure Portal.][aks-quickstart-portal]

Az AKS-fürt létrehozásakor alapértelmezés szerint az SSH-kulcsok lekért vagy létrehozottak, majd hozzáadva a csomópontokhoz. Ez a cikk bemutatja, hogyan adhatja meg az AKS-fürt létrehozásakor használt SSH-kulcsoktól eltérő SSH-kulcsokat. A cikk azt is bemutatja, hogyan állapíthatja meg a csomópont magánhálózati IP-címét, és hogyan csatlakozhat hozzá SSH használatával. Ha nem kell másik SSH-kulcsot megadnia, kihagyhatja a nyilvános SSH-kulcs csomóponthoz való hozzáadásának lépését.

Ez a cikk azt is feltételezi, hogy SSH-kulccsal is van. SSH-kulcsot [macOS, Linux vagy Windows][ssh-nix] rendszeren hozhat [létre.][ssh-windows] Ha a PuTTY Gen használatával hozza létre a kulcspárt, mentse a kulcspárt OpenSSH formátumban az alapértelmezett PuTTy titkos kulcsformátum (.ppk fájl) helyett.

Emellett az Azure CLI 2.0.64-es vagy újabb verziójára is telepítve és konfigurálva kell. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="configure-virtual-machine-scale-set-based-aks-clusters-for-ssh-access"></a>Virtuálisgép-méretezési csoporton alapuló AKS-fürtök konfigurálása SSH-hozzáféréshez

A virtuálisgép-méretezési csoport SSH-hozzáférésre való konfiguráláshoz keresse meg a fürt virtuálisgép-méretezési csoportját, és adja hozzá a nyilvános SSH-kulcsot ehhez a méretezési csoporthoz.

Az [az aks show][az-aks-show] paranccsal lekérte az AKS-fürt erőforráscsoport-nevét, majd az [az vmss list][az-vmss-list] paranccsal lekérte a méretezési csoport nevét.

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
SCALE_SET_NAME=$(az vmss list --resource-group $CLUSTER_RESOURCE_GROUP --query '[0].name' -o tsv)
```

A fenti példa hozzárendeli a *myResourceGroup* *myAKSCluster* fürterőforrás-csoportjának nevét a *CLUSTER_RESOURCE_GROUP.* A példa ezután *CLUSTER_RESOURCE_GROUP* a méretezési készlet nevének listához, majd a következő *SCALE_SET_NAME.*

> [!IMPORTANT]
> Jelenleg csak a virtuálisgép-méretezési csoporton alapuló AKS-fürtök SSH-kulcsait frissítse az Azure CLI használatával.
> 
> Linux-csomópontok esetén az SSH-kulcsok jelenleg csak az Azure CLI használatával hozzáadhatóak. Ha SSH használatával szeretne csatlakozni egy Windows Server-csomóponthoz, használja az AKS-fürt létrehozásakor megadott SSH-kulcsokat, és hagyja ki a következő parancskészletet a nyilvános SSH-kulcs hozzáadásához. Továbbra is szüksége lesz a elhárítani kívánt csomópont IP-címére, amely a szakasz utolsó parancsában látható. Másik lehetőségként távoli asztali [protokollon (RDP)][aks-windows-rdp] keresztül is csatlakozhat Windows Server-csomópontokhoz SSH használata helyett.

Az SSH-kulcsok virtuálisgép-méretezési csoport csomópontjaihoz való hozzáadásához használja az [az vmss extension set][az-vmss-extension-set] és az az [vmss update-instances][az-vmss-update-instances] parancsot.

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

A fenti példa a *CLUSTER_RESOURCE_GROUP* és *SCALE_SET_NAME* korábbi parancsok változóit használja. A fenti példa a *~/.ssh/id_rsa.pub* helyet is használja a nyilvános SSH-kulcs helyeként.

> [!NOTE]
> Alapértelmezés szerint az AKS-csomópontok felhasználóneve *azureuser*.

Miután hozzáadta a nyilvános SSH-kulcsot a méretezési készlethez, SSH-t is hozzáadhat a méretezési készletben lévő csomópont virtuális géphez annak IP-címével. Tekintse meg az AKS-fürtcsomópontok privát IP-címeit a [kubectl get paranccsal.][kubectl-get]

```console
kubectl get nodes -o wide
```

Az alábbi példakimenet a fürt összes csomópontjának belső IP-címét mutatja, beleértve egy Windows Server-csomópontot is.

```console
$ kubectl get nodes -o wide

NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Jegyezheti fel a elhárítani kívánt csomópont belső IP-címét.

A csomópont SSH-kapcsolattal való eléréséhez kövesse az [SSH-kapcsolat létrehozása lépéseit.](#create-the-ssh-connection)

## <a name="configure-virtual-machine-availability-set-based-aks-clusters-for-ssh-access"></a>Virtuálisgép-rendelkezésre állási csoporton alapuló AKS-fürtök konfigurálása SSH-hozzáféréshez

Ahhoz, hogy a virtuális gép rendelkezésre állási csoporton alapuló AKS-fürtöt SSH-hozzáférésre konfigurálja, keresse meg a fürt Linux-csomópontjának nevét, és adja hozzá a nyilvános SSH-kulcsot ehhez a csomóponthoz.

Az [az aks show][az-aks-show] paranccsal le kell szereznie az AKS-fürt erőforráscsoportjának nevét, majd az [az vm list][az-vm-list] paranccsal listából ki kell sorolni a fürt Linux-csomópontjának virtuálisgép-nevét.

```azurecli-interactive
CLUSTER_RESOURCE_GROUP=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv)
az vm list --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

A fenti példa hozzárendeli a *myResourceGroup* *myAKSCluster* fürterőforrás-csoportjának nevét a *CLUSTER_RESOURCE_GROUP.* A példa ezután *CLUSTER_RESOURCE_GROUP* a virtuális gép nevének listához. A példakimenet a virtuális gép nevét mutatja:

```
Name                      ResourceGroup                                  Location
------------------------  ---------------------------------------------  ----------
aks-nodepool1-79590246-0  MC_myResourceGroupAKS_myAKSClusterRBAC_eastus  eastus
```

Az SSH-kulcsok csomóponthoz való hozzáadásához használja [az az vm user update][az-vm-user-update] parancsot.

```azurecli-interactive
az vm user update \
    --resource-group $CLUSTER_RESOURCE_GROUP \
    --name aks-nodepool1-79590246-0 \
    --username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

A fenti példa a *CLUSTER_RESOURCE_GROUP* változót és a csomópont virtuális gépének nevét használja a korábbi parancsokból. A fenti példa a *~/.ssh/id_rsa.pub* helyet is használja a nyilvános SSH-kulcs helyeként. Elérési út megadása helyett használhatja a nyilvános SSH-kulcs tartalmát is.

> [!NOTE]
> Alapértelmezés szerint az AKS-csomópontok felhasználóneve *azureuser*.

Miután hozzáadta a nyilvános SSH-kulcsot a csomópont virtuális géphez, az IP-címével SSH-t is hozzáadhat a virtuális géphez. Tekintse meg egy AKS-fürtcsomópont magánhálózati [IP-címét az az vm list-ip-addresses paranccsal.][az-vm-list-ip-addresses]

```azurecli-interactive
az vm list-ip-addresses --resource-group $CLUSTER_RESOURCE_GROUP -o table
```

A fenti példa az *előző CLUSTER_RESOURCE_GROUP* beállított változót használja. Az alábbi példakimenet az AKS-csomópontok magánhálózati IP-címeit mutatja be:

```
VirtualMachine            PrivateIPAddresses
------------------------  --------------------
aks-nodepool1-79590246-0  10.240.0.4
```

## <a name="create-the-ssh-connection"></a>Az SSH-kapcsolat létrehozása

Ha SSH-kapcsolatot hoz létre egy AKS-csomóponttal, futtatassa a segítő podot az AKS-fürtben. Ez a segítő pod SSH-hozzáférést biztosít a fürthöz, majd további SSH-csomópont-hozzáférést biztosít. A segítő pod létrehozásához és használathoz kövesse az alábbi lépéseket:

1. Futtatassa a `debian` tároló rendszerképét, és csatoljon hozzá egy terminál-munkamenetet. Ezzel a tárolóval SSH-munkamenetet hozhat létre az AKS-fürt bármely csomópontján:

    ```console
    kubectl run -it --rm aks-ssh --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
    ```

    > [!TIP]
    > Ha Windows Server-csomópontokat használ, adjon hozzá egy csomópontválasztót a parancshoz a Debian-tároló Linux-csomóponton való ütemezéséhez:
    >
    > ```console
    > kubectl run -it --rm aks-ssh --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --overrides='{"apiVersion":"v1","spec":{"nodeSelector":{"beta.kubernetes.io/os":"linux"}}}'
    > ```

1. Miután a terminál-munkamenet csatlakozott a tárolóhoz, telepítsen egy SSH-ügyfelet a `apt-get` használatával:

    ```console
    apt-get update && apt-get install openssh-client -y
    ```

1. Nyisson meg egy új terminálablakot, amely nem csatlakozik a tárolóhoz, másolja a titkos SSH-kulcsot a segítő podba. Ezzel a titkos kulccsal hozhatja létre az SSH-t az AKS-csomópontban. 

   Ha szükséges, módosítsa *a ~/.ssh/id_rsa* a titkos SSH-kulcs helyére:

    ```console
    kubectl cp ~/.ssh/id_rsa $(kubectl get pod -l run=aks-ssh -o jsonpath='{.items[0].metadata.name}'):/id_rsa
    ```

1. Térjen vissza a tároló terminál-munkamenetéhez, és frissítse a másolt titkos SSH-kulcs engedélyét, hogy az csak olvasható `id_rsa` legyen:

    ```console
    chmod 0400 id_rsa
    ```

1. Hozzon létre egy SSH-kapcsolatot az AKS-csomóponttal. Az AKS-csomópontok alapértelmezett felhasználóneve *ismét az azureuser*. Fogadja el a kérést a kapcsolat folytatásához, mivel az SSH-kulcs az első megbízható. Ezután a rendszer meg fogja adni az AKS-csomópont bash parancssorát:

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

Ha végzett, `exit` az SSH-munkamenet, majd `exit` az interaktív tároló-munkamenet. A tároló-munkamenet befejezésekor az AKS-fürtről SSH-hozzáféréshez használt pod törlődik.

## <a name="next-steps"></a>Következő lépések

Ha további hibaelhárítási adatokra van szüksége, megtekintheti a [kubelet-naplókat,][view-kubelet-logs] vagy megtekintheti a Kubernetes-főcsomópont [naplóit.][view-master-logs]

<!-- EXTERNAL LINKS -->
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-vm-list]: /cli/azure/vm#az_vm_list
[az-vm-user-update]: /cli/azure/vm/user#az_vm_user_update
[az-vm-list-ip-addresses]: /cli/azure/vm#az_vm_list_ip_addresses
[view-kubelet-logs]: kubelet-logs.md
[view-master-logs]: ./view-control-plane-logs.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-windows-rdp]: rdp.md
[ssh-nix]: ../virtual-machines/linux/mac-create-ssh-keys.md
[ssh-windows]: ../virtual-machines/linux/ssh-from-windows.md
[az-vmss-list]: /cli/azure/vmss#az_vmss_list
[az-vmss-extension-set]: /cli/azure/vmss/extension#az_vmss_extension_set
[az-vmss-update-instances]: /cli/azure/vmss#az_vmss_update_instances