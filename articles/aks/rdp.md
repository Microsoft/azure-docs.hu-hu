---
title: RDP-ből AK-ra Windows Server-csomópontok
titleSuffix: Azure Kubernetes Service
description: Ismerje meg, hogyan hozhat létre RDP-kapcsolatokat az Azure Kubernetes Service (ak) fürt Windows Server-csomópontjaival hibaelhárítási és karbantartási feladatokhoz.
services: container-service
ms.topic: article
ms.date: 06/04/2019
ms.openlocfilehash: 4cfac73c66969148927897de7ed6da0c56aa276a
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102173817"
---
# <a name="connect-with-rdp-to-azure-kubernetes-service-aks-cluster-windows-server-nodes-for-maintenance-or-troubleshooting"></a>Kapcsolódás RDP-vel az Azure Kubernetes Service (ak) fürthöz Windows Server-csomópontok karbantartáshoz vagy hibaelhárításhoz

Az Azure Kubernetes Service (ak) fürt életciklusa során előfordulhat, hogy egy AK-beli Windows Server-csomóponthoz kell hozzáférnie. Ez a hozzáférés lehet karbantartási, naplózási vagy egyéb hibaelhárítási művelet. Az AK Windows Server-csomópontok RDP használatával érhetők el. Ha azt szeretné, hogy az SSH-val hozzáférjen az AK Windows Server-csomópontokhoz, és a fürt létrehozásakor használt kulcspárt is elérheti, kövesse az SSH-ban az [Azure Kubernetes Service (ak) fürtcsomópontok csomópontot][ssh-steps]. Biztonsági okokból az AK-csomópontok nem érhetők el az interneten.

Ebből a cikkből megtudhatja, hogyan hozható létre RDP-kapcsolat egy AK-csomóponttal magánhálózati IP-címeik használatával.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy rendelkezik egy meglévő AK-fürttel egy Windows Server-csomóponttal. Ha AK-fürtre van szüksége, tekintse meg a következő cikket: [AK-fürt létrehozása Windows-tárolóval az Azure CLI használatával][aks-windows-cli]. Szüksége lesz a Windows Server-csomópontra, amelyet a hibakereséshez használni szeretne. Ha nem ismeri őket, visszaállíthatja őket a [Távoli asztali szolgáltatások vagy annak rendszergazdai jelszavának alaphelyzetbe állítása után egy Windows rendszerű virtuális gépen ](../virtual-machines/troubleshooting/reset-rdp.md). Szüksége lesz egy RDP-ügyfélre is, például a [Microsoft távoli asztalra][rdp-mac].

Szüksége lesz az Azure CLI 2.0.61 vagy újabb verziójára is, valamint a telepítésre és konfigurálásra. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="deploy-a-virtual-machine-to-the-same-subnet-as-your-cluster"></a>Virtuális gép üzembe helyezése a fürttel azonos alhálózaton

Az AK-fürt Windows Server-csomópontjai nem rendelkeznek külsőleg elérhető IP-címekkel. RDP-kapcsolat létrehozásához egy nyilvánosan elérhető IP-címmel rendelkező virtuális gépet is telepíthet a Windows Server-csomópontokkal megegyező alhálózatra.

A következő példában létrehozunk egy *myVM* nevű virtuális gépet a *myResourceGroup* erőforráscsoporthoz.

Először szerezze be a Windows Server Node-készlet által használt alhálózatot. Az alhálózat azonosítójának lekéréséhez szüksége lesz az alhálózat nevére. Az alhálózat nevének lekéréséhez szüksége lesz a vnet nevére. Szerezze be a vnet nevét úgy, hogy lekérdezi a fürtöt a hálózatok listájára. A fürt lekérdezéséhez a nevét kell megadnia. Mindezt az alábbi Azure Cloud Shell futtatásával érheti el:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
VNET_NAME=$(az network vnet list -g $CLUSTER_RG --query [0].name -o tsv)
SUBNET_NAME=$(az network vnet subnet list -g $CLUSTER_RG --vnet-name $VNET_NAME --query [0].name -o tsv)
SUBNET_ID=$(az network vnet subnet show -g $CLUSTER_RG --vnet-name $VNET_NAME --name $SUBNET_NAME --query id -o tsv)
```

Most, hogy már rendelkezik a SUBNET_ID, futtassa a következő parancsot ugyanabban a Azure Cloud Shell ablakban a virtuális gép létrehozásához:

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2019datacenter \
    --admin-username azureuser \
    --admin-password myP@ssw0rd12 \
    --subnet $SUBNET_ID \
    --query publicIpAddress -o tsv
```

A következő példa kimenete azt mutatja, hogy a virtuális gép sikeresen létrejött, és a virtuális gép nyilvános IP-címét jeleníti meg.

```console
13.62.204.18
```

Jegyezze fel a virtuális gép nyilvános IP-címét. Ezt a címeket egy későbbi lépésben fogja használni.

## <a name="allow-access-to-the-virtual-machine"></a>A virtuális géphez való hozzáférés engedélyezése

Az AK Node-készlet alhálózatai alapértelmezés szerint a NSG (hálózati biztonsági csoportok) védelemmel vannak ellátva. A virtuális gép eléréséhez engedélyezni kell a hozzáférést a NSG.

> [!NOTE]
> A NSG az AK szolgáltatás vezérli. A NSG végzett módosításokat a vezérlési síkon bármikor felülírja a rendszer.
>

Először kérje le a NSG erőforráscsoport-és NSG-nevét, és adja hozzá a szabályt a következőhöz:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NSG_NAME=$(az network nsg list -g $CLUSTER_RG --query [].name -o tsv)
```

Ezután hozza létre a NSG szabályt:

```azurecli-interactive
az network nsg rule create --name tempRDPAccess --resource-group $CLUSTER_RG --nsg-name $NSG_NAME --priority 100 --destination-port-range 3389 --protocol Tcp --description "Temporary RDP access to Windows nodes"
```

## <a name="get-the-node-address"></a>A csomópont-címek beolvasása

A Kubernetes-fürtök kezeléséhez a [kubectl][kubectl], a Kubernetes parancssori ügyfélprogramot kell használnia. Ha Azure Cloud Shellt használ, `kubectl` már telepítve van. A helyi telepítéshez `kubectl` használja az az [AK install-CLI][az-aks-install-cli] parancsot:
    
```azurecli-interactive
az aks install-cli
```

Az [az aks get-credentials][az-aks-get-credentials] paranccsal konfigurálható `kubectl` a Kubernetes-fürthöz való csatlakozásra. Ez a parancs letölti a hitelesítő adatokat, és konfigurálja a Kubernetes CLI-t a használatára.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

A Windows Server-csomópontok belső IP-címének listázása a [kubectl Get][kubectl-get] paranccsal:

```console
kubectl get nodes -o wide
```

A következő példa kimenet a fürt összes csomópontjának belső IP-címét jeleníti meg, beleértve a Windows Server-csomópontokat is.

```console
$ kubectl get nodes -o wide
NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Jegyezze fel a Windows Server azon csomópontjának belső IP-címét, amelyet a hibakereséshez kíván használni. Ezt a címeket egy későbbi lépésben fogja használni.

## <a name="connect-to-the-virtual-machine-and-node"></a>Kapcsolódás a virtuális géphez és a csomóponthoz

Kapcsolódjon a korábban létrehozott virtuális gép nyilvános IP-címéhez egy RDP-ügyfél (például [Microsoft távoli asztal][rdp-mac]) használatával.

![Az RDP-ügyfél használatával a virtuális géphez való csatlakozás képe](media/rdp/vm-rdp.png)

Miután csatlakozott a virtuális géphez, kapcsolódjon ahhoz a Windows Server *-csomópont belső IP-címéhez* , amelyet a virtuális GÉPEN lévő RDP-ügyfél használatával szeretne elhárítani.

![Az RDP-ügyfél használatával a Windows Server-csomóponthoz való csatlakozás képe](media/rdp/node-rdp.png)

Ezzel csatlakozott a Windows Server-csomóponthoz.

![A Windows Server-csomópont cmd ablakának képe](media/rdp/node-session.png)

Mostantól a *cmd* ablakban is futtathat hibaelhárítási parancsokat. Mivel a Windows Server-csomópontok a Windows Server Core-t használják, nincs teljes grafikus felhasználói felület vagy más GUI-eszköz, amikor RDP-kapcsolaton keresztül csatlakozik egy Windows Server-csomóponthoz.

## <a name="remove-rdp-access"></a>RDP-hozzáférés eltávolítása

Ha elkészült, lépjen ki az RDP-kapcsolattal a Windows Server-csomópontra, majd lépjen ki az RDP-munkamenetből a virtuális gépre. Az RDP-munkamenetek kilépését követően törölje a virtuális gépet az az [VM delete][az-vm-delete] paranccsal:

```azurecli-interactive
az vm delete --resource-group myResourceGroup --name myVM
```

És a NSG szabály:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NSG_NAME=$(az network nsg list -g $CLUSTER_RG --query [].name -o tsv)
```

```azurecli-interactive
az network nsg rule delete --resource-group $CLUSTER_RG --nsg-name $NSG_NAME --name tempRDPAccess
```

## <a name="next-steps"></a>Következő lépések

Ha további hibaelhárítási adatokra van szüksége, [megtekintheti a Kubernetes fő csomópontjának naplóit][view-master-logs] vagy [Azure monitor][azure-monitor-containers].

<!-- EXTERNAL LINKS -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[rdp-mac]: https://aka.ms/rdmac

<!-- INTERNAL LINKS -->
[aks-windows-cli]: windows-container-cli.md
[az-aks-install-cli]: /cli/azure/aks#az-aks-install-cli
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-vm-delete]: /cli/azure/vm#az-vm-delete
[azure-monitor-containers]: ../azure-monitor/containers/container-insights-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ssh-steps]: ssh.md
[view-master-logs]: view-master-logs.md