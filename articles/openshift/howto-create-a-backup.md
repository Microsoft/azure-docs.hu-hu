---
title: 4 Azure Red Hat OpenShift fürtalkalmazás biztonsági másolatának létrehozása a Velero használatával
description: Megtudhatja, hogyan hozhat létre biztonsági másolatot a Azure Red Hat OpenShift-alkalmazásokról a Velero használatával
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 06/22/2020
author: troy0820
ms.author: b-trconn
keywords: aro, openshift, az aro, red hat, cli
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: c8bf722bd77372cd89e7c64757347b5fd07eb1ed
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481361"
---
# <a name="create-an-azure-red-hat-openshift-4-cluster-application-backup"></a>4.Azure Red Hat OpenShift fürt létrehozása – Alkalmazás biztonsági mentése

Ebben a cikkben előkészíti a környezetet egy 4 Azure Red Hat OpenShift biztonsági mentés létrehozására. A következőket fogja megtanulni:

> [!div class="checklist"]
> * Az előfeltételek beállítása és a szükséges eszközök telepítése
> * 4. Azure Red Hat OpenShift biztonsági mentés létrehozása

Ha a CLI helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.6.0-s vagy újabb verziójára lesz szükség. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Előkészületek

### <a name="install-velero"></a>A Velero telepítése

A [](https://velero.io/docs/main/basic-install/) Velero a rendszeren való telepítéséhez kövesse az operációs rendszerének ajánlott eljárást.

### <a name="set-up-azure-storage-account-and-blob-container"></a>Azure Storage-fiók és Blobtároló beállítása

Ez a lépés létrehoz egy erőforráscsoportot az ARO-fürt erőforráscsoportján kívül.  Ez az erőforráscsoport lehetővé teszi, hogy a biztonsági másolatok megmaradnak, és visszaállítsa az alkalmazásokat az új fürtökre.

```bash
AZURE_BACKUP_RESOURCE_GROUP=Velero_Backups
az group create -n $AZURE_BACKUP_RESOURCE_GROUP --location eastus

AZURE_STORAGE_ACCOUNT_ID="velero$(uuidgen | cut -d '-' -f5 | tr '[A-Z]' '[a-z]')"
az storage account create \
    --name $AZURE_STORAGE_ACCOUNT_ID \
    --resource-group $AZURE_BACKUP_RESOURCE_GROUP \
    --sku Standard_GRS \
    --encryption-services blob \
    --https-only true \
    --kind BlobStorage \
    --access-tier Hot

BLOB_CONTAINER=velero
az storage container create -n $BLOB_CONTAINER --public-access off --account-name $AZURE_STORAGE_ACCOUNT_ID
```

## <a name="set-permissions-for-velero"></a>Engedélyek beállítása Veleróhoz

### <a name="create-service-principal"></a>Egyszerű szolgáltatás létrehozása

A Velerónak engedélyekre van szüksége a biztonsági mentések és visszaállítások éhez. Amikor létrehoz egy szolgáltatásnévt, engedélyt ad a Velerónak az előző lépésben létrehozott erőforráscsoport elérésére. Ez a lépés le fogja szerezni a fürt erőforráscsoportot:

```bash
export AZURE_RESOURCE_GROUP=$(az aro show --name <name of cluster> --resource-group <name of resource group> | jq -r .clusterProfile.resourceGroupId | cut -d '/' -f 5,5)
```


```bash
AZURE_SUBSCRIPTION_ID=$(az account list --query '[?isDefault].id' -o tsv)

AZURE_TENANT_ID=$(az account list --query '[?isDefault].tenantId' -o tsv)
```

```bash
AZURE_CLIENT_SECRET=$(az ad sp create-for-rbac --name "velero" --role "Contributor" --query 'password' -o tsv \
--scopes  /subscriptions/$AZURE_SUBSCRIPTION_ID)
AZURE_CLIENT_ID=$(az ad sp list --display-name "velero" --query '[0].appId' -o tsv)

```

```bash
cat << EOF  > ./credentials-velero.yaml
AZURE_SUBSCRIPTION_ID=${AZURE_SUBSCRIPTION_ID}
AZURE_TENANT_ID=${AZURE_TENANT_ID}
AZURE_CLIENT_ID=${AZURE_CLIENT_ID}
AZURE_CLIENT_SECRET=${AZURE_CLIENT_SECRET}
AZURE_RESOURCE_GROUP=${AZURE_RESOURCE_GROUP}
AZURE_CLOUD_NAME=AzurePublicCloud
EOF
```

## <a name="install-velero-on-azure-red-hat-openshift-4-cluster"></a>A Velero telepítése Azure Red Hat OpenShift 4-es fürtre

Ez a lépés telepíti a Velerót a saját projektjére és a Velero biztonsági mentéséhez és visszaállításához szükséges egyéni erőforrás-definíciókhoz. [](https://kubernetes.io/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/) Győződjön meg arról, hogy sikeresen bejelentkezett egy Azure Red Hat OpenShift 4-es Azure Red Hat OpenShift fürtbe.


```bash
velero install \
--provider azure \
--plugins velero/velero-plugin-for-microsoft-azure:v1.1.0 \
--bucket $BLOB_CONTAINER \
--secret-file ~/path/to/credentials-velero.yaml \
--backup-location-config resourceGroup=$AZURE_BACKUP_RESOURCE_GROUP,storageAccount=$AZURE_STORAGE_ACCOUNT_ID \
--snapshot-location-config apiTimeout=15m \
--velero-pod-cpu-limit="0" --velero-pod-mem-limit="0" \
--velero-pod-mem-request="0" --velero-pod-cpu-request="0"
```

## <a name="create-a-backup-with-velero"></a>Biztonsági mentés létrehozása Veleróval

Ahhoz, hogy a Veleróval biztonsági másolatot készítsünk egy alkalmazásról, bele kell foglalnia azt a névteret, amelybe ez az alkalmazás tartozik.  Ha rendelkezik névtér használatával, és a névtér összes erőforrását bele szeretné foglalni a biztonsági mentésbe, futtassa a következő parancsot a `nginx-example` terminálban:

```bash
velero create backup <name of backup> --include-namespaces=nginx-example
```
A biztonsági mentés állapotát a következő futtatásával ellenőrizheti:

```bash
oc get backups -n velero <name of backup> -o yaml
```

A sikeres biztonsági mentés `phase:Completed` kimenete és az objektumok a tárfiókban lévő tárolóban fognak élni.

## <a name="create-a-backup-with-velero-to-include-snapshots"></a>Biztonsági másolat létrehozása a Veleróval pillanatképek készítéséhez

Ahhoz, hogy az Alkalmazás biztonsági mentése a Veleróval együtt tartalmazza az alkalmazás állandó kötetét, meg kell tartalmaznia az alkalmazáshoz használt névteret, valamint a jelölőt a biztonsági mentés létrehozásakor `snapshot-volumes=true`

```bash
velero backup create <name of backup> --include-namespaces=nginx-example --snapshot-volumes=true --include-cluster-resources=true
```

A biztonsági mentés állapotát a következő futtatásával ellenőrizheti:

```bash
oc get backups -n velero <name of backup> -o yaml
```

Egy sikeres biztonsági mentés `phase:Completed` kimenettel, és az objektumok a tárfiókban lévő tárolóban fognak élni.

További információ a biztonsági mentések és visszaállítások Velero használatával való létrehozásáról: [OpenShift-erőforrások](https://www.openshift.com/blog/backup-openshift-resources-the-native-way) natív biztonsági mentése

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben egy 4 Azure Red Hat OpenShift fürtalkalmazásról biztonságimentett. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * OpenShift v4-fürtalkalmazás biztonsági másolatának létrehozása Veleróval
> * OpenShift v4-fürtalkalmazás biztonsági másolatának létrehozása pillanatképekkel a Velero használatával


A következő cikk azt is bemutatja, hogyan hozhat létre Azure Red Hat OpenShift 4 fürtalkalmazás visszaállítását.

* [4 Azure Red Hat OpenShift fürtalkalmazás-visszaállítás létrehozása](howto-create-a-restore.md)
* [4 Azure Red Hat OpenShift fürtalkalmazás visszaállítása pillanatképekkel együtt](howto-create-a-restore.md)
