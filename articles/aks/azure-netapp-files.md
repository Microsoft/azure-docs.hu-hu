---
title: Integráció Azure NetApp Files és Azure Kubernetes Service
description: Megtudhatja, hogyan integrálhatja Azure NetApp Files a Azure Kubernetes Service
services: container-service
ms.topic: article
ms.date: 10/23/2020
ms.openlocfilehash: 28c5b77f06bc48bf06575e45194adfaed068b30f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776050"
---
# <a name="integrate-azure-netapp-files-with-azure-kubernetes-service"></a>Integráció Azure NetApp Files és Azure Kubernetes Service

[Azure NetApp Files][anf] egy nagyvállalati szintű, nagy teljesítményű, forgalmi díjas fájltárolási szolgáltatás, amely az Azure-ban fut. Ez a cikk bemutatja, hogyan integrálhatja a Azure NetApp Files a Azure Kubernetes Service (AKS) szolgáltatásba.

## <a name="before-you-begin"></a>Előkészületek
Ez a cikk feltételezi, hogy már van AKS-fürte. Ha AKS-fürtre van szüksége, tekintse meg az AKS rövid útmutatóját az [Azure CLI][aks-quickstart-cli] vagy a [Azure Portal.][aks-quickstart-portal]

> [!IMPORTANT]
> Az AKS-fürtnek olyan régióban kell lennie, amely [támogatja a Azure NetApp Files.][anf-regions]

Az Azure CLI 2.0.59-es vagy újabb verziójára is telepítve és konfigurálva kell. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

### <a name="limitations"></a>Korlátozások

A következő korlátozások érvényesek a Azure NetApp Files:

* Azure NetApp Files csak a kiválasztott [Azure-régiókban érhető el.][anf-regions]
* A szolgáltatás Azure NetApp Files hozzáférést kell biztosítani a Azure NetApp Files szolgáltatáshoz. A hozzáférésre való jelentkezéshez használhatja a Azure NetApp Files [a várakozási lista beküldési][anf-waitlist] űrlapját, vagy ugrás a következőre: https://azure.microsoft.com/services/netapp/#getting-started . Nem férhet hozzá a Azure NetApp Files szolgáltatáshoz, amíg meg nem kapja a hivatalos megerősítő e-mailt Azure NetApp Files csapattól.
* Az AKS-fürt kezdeti üzembe helyezése után csak a fürtök statikus Azure NetApp Files támogatott.
* Ha dinamikus kiépítést használ a Azure NetApp Files, telepítse és konfigurálja a [NetApp Trident](https://netapp-trident.readthedocs.io/) 19.07-es vagy újabb verzióját.

## <a name="configure-azure-netapp-files"></a>A Azure NetApp Files

> [!IMPORTANT]
> A  *Microsoft.NetApp* erőforrás-szolgáltató regisztrálása előtt ki kell egészítenünk a Azure NetApp Files [beküldési űrlapot,][anf-waitlist] vagy meg kell keresnünk https://azure.microsoft.com/services/netapp/#getting-started az előfizetését. Nem regisztrálhatja a megadott erőforrást, amíg meg nem kapja a hivatalos megerősítő e-mailt a Azure NetApp Files csapattól.

Regisztrálja *a Microsoft.NetApp* erőforrás-szolgáltatót:

```azurecli
az provider register --namespace Microsoft.NetApp --wait
```

> [!NOTE]
> Ez némi időt vehet igénybe.

Amikor létrehoz egy Azure NetApp-fiókot az AKS-sel való használatra, a fiókot a csomópont erőforráscsoportban **kell** létrehoznia. Először szerezze be az erőforráscsoport nevét az [az aks show paranccsal,][az-aks-show] és adja hozzá a `--query nodeResourceGroup` lekérdezési paramétert. A következő példa lekérte a myResourceGroup nevű erőforráscsoportban található *myAKSCluster* nevű AKS-fürt *csomópont-erőforráscsoportját:*

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

```output
MC_myResourceGroup_myAKSCluster_eastus
```

Hozzon létre egy Azure NetApp Files-fiókot a csomópont-erőforráscsoportban és az AKS-fürt régiójában [az az netappfiles account create használatával.][az-netappfiles-account-create]  Az alábbi példa létrehoz egy *myaccount1* nevű fiókot a MC_myResourceGroup_myAKSCluster_eastus *erőforráscsoportban* és *az eastus régióban:*

```azurecli
az netappfiles account create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1
```

Hozzon létre egy új kapacitáskészletet [az az netappfiles pool create használatával.][az-netappfiles-pool-create] Az alábbi példa egy *mypool1* nevű új kapacitáskészletet hoz létre 4 TB mérettel és *prémium szolgáltatási szinttel:*

```azurecli
az netappfiles pool create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1 \
    --pool-name mypool1 \
    --size 4 \
    --service-level Premium
```

Hozzon létre egy alhálózatot, amely [a][anf-delegate-subnet] Azure NetApp Files [az network vnet subnet create használatával.][az-network-vnet-subnet-create] *Ennek az alhálózatnak és az AKS-fürtnek ugyanabban a virtuális hálózatban kell lennie.*

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
az network vnet subnet create \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $VNET_NAME \
    --name $SUBNET_NAME \
    --delegations "Microsoft.NetApp/volumes" \
    --address-prefixes 10.0.0.0/28
```

Hozzon létre egy kötetet [az az netappfiles volume create használatával.][az-netappfiles-volume-create]

```azurecli
RESOURCE_GROUP=MC_myResourceGroup_myAKSCluster_eastus
LOCATION=eastus
ANF_ACCOUNT_NAME=myaccount1
POOL_NAME=mypool1
SERVICE_LEVEL=Premium
VNET_NAME=$(az network vnet list --resource-group $RESOURCE_GROUP --query [].name -o tsv)
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP --name $VNET_NAME --query "id" -o tsv)
SUBNET_NAME=MyNetAppSubnet
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP --vnet-name $VNET_NAME --name $SUBNET_NAME --query "id" -o tsv)
VOLUME_SIZE_GiB=100 # 100 GiB
UNIQUE_FILE_PATH="myfilepath2" # Please note that file path needs to be unique within all ANF Accounts

az netappfiles volume create \
    --resource-group $RESOURCE_GROUP \
    --location $LOCATION \
    --account-name $ANF_ACCOUNT_NAME \
    --pool-name $POOL_NAME \
    --name "myvol1" \
    --service-level $SERVICE_LEVEL \
    --vnet $VNET_ID \
    --subnet $SUBNET_ID \
    --usage-threshold $VOLUME_SIZE_GiB \
    --file-path $UNIQUE_FILE_PATH \
    --protocol-types "NFSv3"
```

## <a name="create-the-persistentvolume"></a>A PersistentVolume létrehozása

A kötet részleteinek felsorolása az [az netappfiles volume show használatával][az-netappfiles-volume-show]

```azurecli
az netappfiles volume show --resource-group $RESOURCE_GROUP --account-name $ANF_ACCOUNT_NAME --pool-name $POOL_NAME --volume-name "myvol1"
```

```output
{
  ...
  "creationToken": "myfilepath2",
  ...
  "mountTargets": [
    {
      ...
      "ipAddress": "10.0.0.4",
      ...
    }
  ],
  ...
}
```

Hozzon `pv-nfs.yaml` létre egy PersistentVolume definiáló et. Cserélje `path` le a *helyére a creationToken,* a helyére pedig az előző parancs `server` *ipAddress* parancsát. Például:

```yaml
---
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-nfs
spec:
  capacity:
    storage: 100Gi
  accessModes:
    - ReadWriteMany
  mountOptions:
    - vers=3
  nfs:
    server: 10.0.0.4
    path: /myfilepath2
```

Frissítse *a* *kiszolgálót és* az elérési utat az előző lépésben létrehozott NFS- (hálózati fájlrendszer-) kötet értékeire. Hozza létre a PersistentVolume-t a [kubectl apply paranccsal:][kubectl-apply]

```console
kubectl apply -f pv-nfs.yaml
```

Ellenőrizze, *hogy a* PersistentVolume állapota *Elérhető-e* a [kubectl describe paranccsal:][kubectl-describe]

```console
kubectl describe pv pv-nfs
```

## <a name="create-the-persistentvolumeclaim"></a>A PersistentVolumeClaim létrehozása

Hozzon `pvc-nfs.yaml` létre egy PersistentVolume definiáló et. Például:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-nfs
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
```

Hozza létre a PersistentVolumeClaim et a [kubectl apply paranccsal:][kubectl-apply]

```console
kubectl apply -f pvc-nfs.yaml
```

A [kubectl describe][kubectl-describe] *paranccsal* ellenőrizze, hogy a PersistentVolumeClaim állapota *Bound:*

```console
kubectl describe pvc pvc-nfs
```

## <a name="mount-with-a-pod"></a>Csatlakoztatás podokkal

Hozzon `nginx-nfs.yaml` létre egy podot, amely a PersistentVolumeClaim igénylést használja. Például:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx-nfs
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
    name: nginx-nfs
    command:
    - "/bin/sh"
    - "-c"
    - while true; do echo $(date) >> /mnt/azure/outfile; sleep 1; done
    volumeMounts:
    - name: disk01
      mountPath: /mnt/azure
  volumes:
  - name: disk01
    persistentVolumeClaim:
      claimName: pvc-nfs
```

Hozza létre a podot a [kubectl apply paranccsal:][kubectl-apply]

```console
kubectl apply -f nginx-nfs.yaml
```

Ellenőrizze, hogy a pod *fut-e a* [kubectl describe paranccsal:][kubectl-describe]

```console
kubectl describe pod nginx-nfs
```

Ellenőrizze, hogy a kötet csatlakoztatva van-e a podhoz. A [kubectl exec][kubectl-exec] használatával csatlakozzon a podhoz, majd ellenőrizze, hogy a kötet `df -h` csatlakoztatva van-e.

```console
$ kubectl exec -it nginx-nfs -- sh
```

```output
/ # df -h
Filesystem             Size  Used Avail Use% Mounted on
...
10.0.0.4:/myfilepath2  100T  384K  100T   1% /mnt/azure
...
```

## <a name="next-steps"></a>Következő lépések

További információ a [Azure NetApp Files: Mi a Azure NetApp Files.][anf] Az NFS AKS-sel való használatával kapcsolatos további információkért lásd: NFS (hálózati fájlrendszer) Linux Server-kötet manuális létrehozása és használata Azure Kubernetes Service [(AKS) használatával.][aks-nfs]


[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-nfs]: azure-nfs-volume.md
[anf]: ../azure-netapp-files/azure-netapp-files-introduction.md
[anf-delegate-subnet]: ../azure-netapp-files/azure-netapp-files-delegate-subnet.md
[anf-quickstart]: ../azure-netapp-files/
[anf-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all
[anf-waitlist]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-netappfiles-account-create]: /cli/azure/netappfiles/account#az_netappfiles_account_create
[az-netappfiles-pool-create]: /cli/azure/netappfiles/pool#az_netappfiles_pool_create
[az-netappfiles-volume-create]: /cli/azure/netappfiles/volume#az_netappfiles_volume_create
[az-netappfiles-volume-show]: /cli/azure/netappfiles/volume#az_netappfiles_volume_show
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_create
[install-azure-cli]: /cli/azure/install-azure-cli
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
