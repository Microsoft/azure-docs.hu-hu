---
title: RDP-ről AKS-hez Windows Server-csomópontok
titleSuffix: Azure Kubernetes Service
description: Megtudhatja, hogyan hozhat létre RDP-kapcsolatot Azure Kubernetes Service -fürt Windows Server-csomópontjaival hibaelhárítási és karbantartási feladatokhoz.
services: container-service
ms.topic: article
ms.date: 06/04/2019
ms.openlocfilehash: 62f29c0550b858e34d888da61f1bd7fbd358f82d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782926"
---
# <a name="connect-with-rdp-to-azure-kubernetes-service-aks-cluster-windows-server-nodes-for-maintenance-or-troubleshooting"></a>Csatlakozás RDP-kapcsolattal Azure Kubernetes Service (AKS-) fürt Windows Server-csomópontjaihoz karbantartás vagy hibaelhárítás érdekében

A Azure Kubernetes Service (AKS-) fürt életciklusa során szükség lehet egy AKS Windows Server-csomópont elérésére. Ez a hozzáférés karbantartási, naplógyűjtési vagy egyéb hibaelhárítási műveletekhez lehet szükséges. Az AKS Windows Server-csomópontokat RDP használatával is elérheti. Ha SSH-t szeretne használni az AKS Windows Server-csomópontokhoz való hozzáféréshez, és hozzáfér a fürt létrehozásakor használt kulcspairhoz, kövesse az SSH és az [Azure Kubernetes Service (AKS)][ssh-steps]fürtcsomópontokba lépéseit. Biztonsági okokból az AKS-csomópontok nincsenek elérhetővé téve az interneten.

Ez a cikk bemutatja, hogyan hozhat létre RDP-kapcsolatot egy AKS-csomóponttal a magánhálózati IP-címük használatával.

## <a name="before-you-begin"></a>Előkészületek

Ez a cikk feltételezi, hogy már van egy Meglévő AKS-fürt egy Windows Server-csomóponttal. Ha AKS-fürtre van szüksége, tekintse meg az [AKS-fürt][aks-windows-cli]Windows-tárolóval az Azure CLI használatával történő létrehozását. Szüksége lesz a windowsos rendszergazda felhasználónevére és jelszavára ahhoz a Windows Server-csomóponthoz, amelyről hibaelhárítást szeretne. Ha nem ismeri őket, alaphelyzetbe állíthatja őket az új jelszó Távoli asztali szolgáltatások windowsos virtuális gépen található rendszergazdai [jelszavával. ](/troubleshoot/azure/virtual-machines/reset-rdp) Szüksége lesz egy RDP-ügyfélre is, például a [Microsoft Távoli asztal.][rdp-mac]

Az Azure CLI 2.0.61-es vagy újabb verziójára is telepítve és konfigurálva kell. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

## <a name="deploy-a-virtual-machine-to-the-same-subnet-as-your-cluster"></a>Virtuális gép üzembe helyezése ugyanazon az alhálózaton, mint a fürt

Az AKS-fürt Windows Server-csomópontjai nem állnak rendelkezésre külsőleg elérhető IP-címmel. RDP-kapcsolat létesítéhez üzembe helyezhet egy nyilvánosan elérhető IP-címmel bíró virtuális gépet ugyanazon az alhálózaton, mint a Windows Server-csomópontok.

Az alábbi példa egy *myVM* nevű virtuális gépet hoz létre a *myResourceGroup* erőforráscsoportban.

Először szerezze be a Windows Server-csomópontkészlet által használt alhálózatot. Az alhálózat azonosítójának lekért névre van szükség. Az alhálózat nevének lekért nevéhez szüksége lesz a virtuális hálózat nevére. A virtuális hálózat nevének lekérdezhető a fürt hálózatlistából való lekérdezésével. A fürt lekérdezéséhez szüksége lesz a nevére. Ezek mindegyikét le tudja szerezni, ha a következőt futtatja a Azure Cloud Shell:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
VNET_NAME=$(az network vnet list -g $CLUSTER_RG --query [0].name -o tsv)
SUBNET_NAME=$(az network vnet subnet list -g $CLUSTER_RG --vnet-name $VNET_NAME --query [0].name -o tsv)
SUBNET_ID=$(az network vnet subnet show -g $CLUSTER_RG --vnet-name $VNET_NAME --name $SUBNET_NAME --query id -o tsv)
```

Most, hogy megvan a SUBNET_ID, futtassa a következő parancsot ugyanabban a Azure Cloud Shell ablakban a virtuális gép létrehozásához:

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

Az alábbi példakimenet azt mutatja, hogy a virtuális gép sikeresen létrejött, és megjeleníti a virtuális gép nyilvános IP-címét.

```console
13.62.204.18
```

Rögzítse a virtuális gép nyilvános IP-címét. Ezt a címet egy későbbi lépésben fogja használni.

## <a name="allow-access-to-the-virtual-machine"></a>A virtuális géphez való hozzáférés engedélyezése

Az AKS-csomópontkészlet alhálózatait alapértelmezés szerint NSG-k (hálózati biztonsági csoportok) védik. A virtuális géphez való hozzáféréshez engedélyeznie kell a hozzáférést az NSG-ben.

> [!NOTE]
> Az NSG-ket az AKS-szolgáltatás vezérli. Az NSG bármilyen módosítása bármikor felülíródik a vezérlősíkon.
>

Először szerezze be az erőforráscsoportot és az nsg nevét, hogy hozzáadja a szabályt a következő hez:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NSG_NAME=$(az network nsg list -g $CLUSTER_RG --query [].name -o tsv)
```

Ezután hozza létre az NSG-szabályt:

```azurecli-interactive
az network nsg rule create --name tempRDPAccess --resource-group $CLUSTER_RG --nsg-name $NSG_NAME --priority 100 --destination-port-range 3389 --protocol Tcp --description "Temporary RDP access to Windows nodes"
```

## <a name="get-the-node-address"></a>A csomópont címének lekért száma

A Kubernetes-fürtök kezeléséhez használja a [kubectl][kubectl], a Kubernetes parancssori ügyfelét. Ha az alkalmazást Azure Cloud Shell, `kubectl` a már telepítve van. A helyi `kubectl` telepítéshez használja az [az aks install-cli][az-aks-install-cli] parancsot:
    
```azurecli-interactive
az aks install-cli
```

Az [az aks get-credentials][az-aks-get-credentials] paranccsal konfigurálható `kubectl` a Kubernetes-fürthöz való csatlakozásra. Ez a parancs letölti a hitelesítő adatokat, és konfigurálja a Kubernetes parancssori felületét azok használatára.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Listsa ki a Windows Server-csomópontok belső IP-címét a [kubectl get paranccsal:][kubectl-get]

```console
kubectl get nodes -o wide
```

Az alábbi példakimenet a fürt összes csomópontjának belső IP-címét mutatja, beleértve a Windows Server-csomópontokat is.

```console
$ kubectl get nodes -o wide
NAME                                STATUS   ROLES   AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE                    KERNEL-VERSION      CONTAINER-RUNTIME
aks-nodepool1-42485177-vmss000000   Ready    agent   18h   v1.12.7   10.240.0.4    <none>        Ubuntu 16.04.6 LTS          4.15.0-1040-azure   docker://3.0.4
aksnpwin000000                      Ready    agent   13h   v1.12.7   10.240.0.67   <none>        Windows Server Datacenter   10.0.17763.437
```

Jegyezheti fel annak a Windows Server-csomópontnak a belső IP-címét, amelyről hibaelhárítást szeretne. Ezt a címet egy későbbi lépésben fogja használni.

## <a name="connect-to-the-virtual-machine-and-node"></a>Csatlakozás a virtuális géphez és a csomóponthoz

Csatlakozzon a korábban létrehozott virtuális gép nyilvános IP-címhez egy RDP-ügyféllel, például a [Microsoft Távoli asztal.][rdp-mac]

![A virtuális géphez RDP-ügyféllel való csatlakozást képe](media/rdp/vm-rdp.png)

Miután csatlakozott a virtuális géphez,  csatlakozzon a hibaelhárításhoz használni kívánt Windows Server-csomópont belső IP-címéhez egy RDP-ügyféllel a virtuális gépen belülről.

![Kép a Windows Server-csomóponthoz RDP-ügyféllel való csatlakozásról](media/rdp/node-rdp.png)

Most már csatlakozott a Windows Server-csomóponthoz.

![A Windows Server csomópont cmd ablakának képe](media/rdp/node-session.png)

Most már futtathat hibaelhárítási parancsokat a *parancsablakban.* Mivel a Windows Server-csomópontok a Windows Server Core-t használják, nincs teljes grafikus felhasználói felület vagy más grafikus felhasználói felületi eszköz, amikor RDP-n keresztül csatlakozik egy Windows Server-csomóponthoz.

## <a name="remove-rdp-access"></a>RDP-hozzáférés eltávolítása

Ha végzett, lépjen ki a Windows Server-csomópont RDP-kapcsolatból, majd lépjen ki a virtuális gép RDP-munkamenetből. Miután kilépt mindkét RDP-munkamenetből, törölje a virtuális gépet [az az vm delete paranccsal:][az-vm-delete]

```azurecli-interactive
az vm delete --resource-group myResourceGroup --name myVM
```

Az NSG-szabály pedig:

```azurecli-interactive
CLUSTER_RG=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NSG_NAME=$(az network nsg list -g $CLUSTER_RG --query [].name -o tsv)
```

```azurecli-interactive
az network nsg rule delete --resource-group $CLUSTER_RG --nsg-name $NSG_NAME --name tempRDPAccess
```

## <a name="next-steps"></a>Következő lépések

Ha további hibaelhárítási adatokra van szüksége, megtekintheti a Kubernetes-főcsomópont [naplóit,][view-master-logs] vagy [Azure Monitor.][azure-monitor-containers]

<!-- EXTERNAL LINKS -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[rdp-mac]: https://aka.ms/rdmac

<!-- INTERNAL LINKS -->
[aks-windows-cli]: windows-container-cli.md
[az-aks-install-cli]: /cli/azure/aks#az_aks_install_cli
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-vm-delete]: /cli/azure/vm#az_vm_delete
[azure-monitor-containers]: ../azure-monitor/containers/container-insights-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ssh-steps]: ssh.md
[view-master-logs]: view-master-logs.md