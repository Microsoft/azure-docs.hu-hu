---
title: Azure NetApp Files integrálása az Azure Kubernetes szolgáltatással
description: Ismerje meg, hogyan integrálható Azure NetApp Files az Azure Kubernetes Service-szel
services: container-service
ms.topic: article
ms.date: 10/23/2020
ms.openlocfilehash: 1d5aa8232b5d0aaa68e6d7e3dcbb9a7d70d0e8f8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182145"
---
# <a name="integrate-azure-netapp-files-with-azure-kubernetes-service"></a>Azure NetApp Files integrálása az Azure Kubernetes szolgáltatással

[Azure NetApp Files][anf] az Azure-on futó nagyvállalati szintű, nagy teljesítményű és mért használatú file Storage szolgáltatás. Ez a cikk bemutatja, hogyan integrálhatja Azure NetApp Files az Azure Kubernetes szolgáltatással (ak).

## <a name="before-you-begin"></a>Előkészületek
Ez a cikk feltételezi, hogy rendelkezik egy meglévő AK-fürttel. Ha AK-fürtre van szüksége, tekintse meg az AK gyors üzembe helyezését [Az Azure CLI használatával][aks-quickstart-cli] vagy [a Azure Portal használatával][aks-quickstart-portal].

> [!IMPORTANT]
> Az AK-fürtnek olyan régióban is kell lennie [, amely támogatja a Azure NetApp Files][anf-regions].

Szüksége lesz az Azure CLI 2.0.59 vagy újabb verziójára is, valamint a telepítésre és konfigurálásra. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése][install-azure-cli].

### <a name="limitations"></a>Korlátozások

A Azure NetApp Files használatakor a következő korlátozások érvényesek:

* Azure NetApp Files csak [a kiválasztott Azure-régiókban][anf-regions]érhető el.
* Azure NetApp Files használata előtt hozzáférést kell biztosítania a Azure NetApp Files szolgáltatáshoz. A hozzáférésre való jelentkezéshez használhatja a [Azure NetApp Files várólista-beküldési űrlapot][anf-waitlist] , vagy lépjen a következőre: https://azure.microsoft.com/services/netapp/#getting-started . A Azure NetApp Files szolgáltatás addig nem érhető el, amíg nem kapja meg a hivatalos visszaigazoló e-mailt a Azure NetApp Files csapattól.
* Egy AK-fürt kezdeti üzembe helyezése után a Azure NetApp Files csak a statikus kiépítés támogatott.
* Ha Azure NetApp Files használatával szeretne dinamikus kiépíteni, telepítse és konfigurálja a [NetApp Trident](https://netapp-trident.readthedocs.io/) 19,07-es vagy újabb verzióját.

## <a name="configure-azure-netapp-files"></a>Azure NetApp Files konfigurálása

> [!IMPORTANT]
> A  *Microsoft. NetApp* erőforrás-szolgáltató regisztrálása előtt be kell fejeznie a [Azure NetApp Files várólista-beküldési űrlapot][anf-waitlist] , vagy az https://azure.microsoft.com/services/netapp/#getting-started előfizetéséhez kell lépnie. Az erőforrás nem regisztrálható, amíg meg nem kapja a hivatalos visszaigazoló e-mailt a Azure NetApp Files csapattól.

Regisztrálja a *Microsoft. NetApp* erőforrás-szolgáltatót:

```azurecli
az provider register --namespace Microsoft.NetApp --wait
```

> [!NOTE]
> Ez hosszabb időt is igénybe vehet.

Amikor létrehoz egy Azure NetApp-fiókot az AK-val való használatra, létre kell hoznia a fiókot a **csomópont** -erőforráscsoporthoz. Először kérje le az erőforráscsoport nevét az az [az AK show][az-aks-show] paranccsal, és adja hozzá a `--query nodeResourceGroup` lekérdezési paramétert. A következő példa lekéri a *myAKSCluster* nevű AK-fürthöz tartozó csomópont-erőforráscsoportot az erőforráscsoport neve *myResourceGroup*:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv
```

```output
MC_myResourceGroup_myAKSCluster_eastus
```

Hozzon létre egy Azure NetApp Files fiókot a **csomópont** -erőforráscsoport és az AK-fürttel azonos régióban az [az netappfiles Account Create][az-netappfiles-account-create]paranccsal. A következő példa létrehoz egy *myaccount1* nevű fiókot a *MC_myResourceGroup_myAKSCluster_eastus* erőforráscsoporthoz és a *eastus* régióban:

```azurecli
az netappfiles account create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1
```

Hozzon létre egy új kapacitási készletet az [az netappfiles Pool Create][az-netappfiles-pool-create]paranccsal. Az alábbi példa egy *mypool1* nevű új kapacitási készletet hoz létre 4 TB méretű és *prémium* szintű szolgáltatási szinten:

```azurecli
az netappfiles pool create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --location eastus \
    --account-name myaccount1 \
    --pool-name mypool1 \
    --size 4 \
    --service-level Premium
```

Hozzon létre egy alhálózatot, amelyet [delegálhat Azure NetApp Files][anf-delegate-subnet] az [az Network vnet subnet Create][az-network-vnet-subnet-create]paranccsal. *Ennek az alhálózatnak ugyanabban a virtuális hálózatban kell lennie, mint az AK-fürt.*

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

Hozzon létre egy kötetet az [az netappfiles Volume Create][az-netappfiles-volume-create]paranccsal.

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

A kötet részleteinek listázása az [az netappfiles Volume show][az-netappfiles-volume-show] használatával

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

Hozzon létre egy `pv-nfs.yaml` definiált PersistentVolume. Cserélje le a `path` elemet a *creationToken* és az `server` *IP* -cím elemre az előző parancsban. Például:

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

Frissítse a *kiszolgálót* és az *elérési utat* az NFS-(hálózati fájlrendszer) kötetnek az előző lépésben létrehozott értékére. Hozza létre a PersistentVolume a [kubectl Apply][kubectl-apply] paranccsal:

```console
kubectl apply -f pv-nfs.yaml
```

Ellenőrizze, hogy a PersistentVolume *állapota* *elérhető* -e a [kubectl leíró][kubectl-describe] parancs használatával:

```console
kubectl describe pv pv-nfs
```

## <a name="create-the-persistentvolumeclaim"></a>A PersistentVolumeClaim létrehozása

Hozzon létre egy `pvc-nfs.yaml` definiált PersistentVolume. Például:

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

Hozza létre a PersistentVolumeClaim a [kubectl Apply][kubectl-apply] paranccsal:

```console
kubectl apply -f pvc-nfs.yaml
```

Ellenőrizze, hogy a PersistentVolumeClaim *állapota* *kötve* van-e a [kubectl leíró][kubectl-describe] parancs használatával:

```console
kubectl describe pvc pvc-nfs
```

## <a name="mount-with-a-pod"></a>Csatlakoztatás Pod-mel

Hozzon létre egy olyan `nginx-nfs.yaml` Pod-definíciót, amely a PersistentVolumeClaim használja. Például:

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

Hozza létre a pod-t a [kubectl Apply][kubectl-apply] paranccsal:

```console
kubectl apply -f nginx-nfs.yaml
```

Ellenőrizze, hogy *fut* -e a pod a [kubectl leíró][kubectl-describe] parancs használatával:

```console
kubectl describe pod nginx-nfs
```

Ellenőrizze, hogy a kötet csatlakoztatva van-e a pod-hoz a [kubectl exec][kubectl-exec] használatával, hogy csatlakozik-e a hüvelyhez, `df -h` és ellenőrizze, hogy a kötet csatlakoztatva van-e.

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

További információ a Azure NetApp Filesről: [Mi az Azure NetApp Files][anf]. További információ az NFS-sel való használatáról: [NFS (hálózati fájlrendszer) Linux Server-kötet létrehozása és használata az Azure Kubernetes szolgáltatással (ak)][aks-nfs].


[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-nfs]: azure-nfs-volume.md
[anf]: ../azure-netapp-files/azure-netapp-files-introduction.md
[anf-delegate-subnet]: ../azure-netapp-files/azure-netapp-files-delegate-subnet.md
[anf-quickstart]: ../azure-netapp-files/
[anf-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all
[anf-waitlist]: https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR8cq17Xv9yVBtRCSlcD_gdVUNUpUWEpLNERIM1NOVzA5MzczQ0dQR1ZTSS4u
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-netappfiles-account-create]: /cli/azure/netappfiles/account#az-netappfiles-account-create
[az-netappfiles-pool-create]: /cli/azure/netappfiles/pool#az-netappfiles-pool-create
[az-netappfiles-volume-create]: /cli/azure/netappfiles/volume#az-netappfiles-volume-create
[az-netappfiles-volume-show]: /cli/azure/netappfiles/volume#az-netappfiles-volume-show
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[install-azure-cli]: /cli/azure/install-azure-cli
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
