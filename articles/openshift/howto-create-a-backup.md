---
title: Azure Red Hat OpenShift-alkalmazás biztonsági mentésének létrehozása a Velero használatával
description: Ismerje meg, hogyan készíthet biztonsági másolatot az Azure Red Hat OpenShift-fürtökről a Velero használatával
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 06/22/2020
author: troy0820
ms.author: b-trconn
keywords: ARO, openshift, az ARO, Red Hat, CLI
ms.custom: mvc
ms.openlocfilehash: bbfe280ed0b1b562e0f50b23a09ea159750c4a79
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102217091"
---
# <a name="create-an-azure-red-hat-openshift-4-cluster-application-backup"></a>Azure Red Hat OpenShift-alkalmazás biztonsági másolatának létrehozása

Ebben a cikkben előkészíti a környezetet egy Azure Red Hat OpenShift 4 cluster Application biztonsági másolat létrehozásához. A következőket fogja megtanulni:

> [!div class="checklist"]
> * Az előfeltételek beállítása és a szükséges eszközök telepítése
> * Azure Red Hat OpenShift 4 alkalmazás biztonsági másolatának létrehozása

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.6.0 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="before-you-begin"></a>Előkészületek

### <a name="install-velero"></a>A Velero telepítése

A Velero a rendszerre való [telepítéséhez](https://velero.io/docs/main/basic-install/) kövesse az operációs rendszerének javasolt folyamatát.

### <a name="set-up-azure-storage-account-and-blob-container"></a>Az Azure Storage-fiók és a blob-tároló beállítása

Ez a lépés létrehoz egy erőforráscsoportot az ARO-fürt erőforráscsoport területén kívül.  Ez az erőforráscsoport lehetővé teszi a biztonsági mentések megőrzését, és új fürtökre állíthatja vissza az alkalmazásokat.

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

## <a name="set-permissions-for-velero"></a>Velero engedélyeinek beállítása

### <a name="create-service-principal"></a>Egyszerű szolgáltatás létrehozása

A Velero engedélyekkel kell rendelkeznie a biztonsági mentéshez és a visszaállításhoz. Egyszerű szolgáltatásnév létrehozásakor a Velero engedélyt ad az előző lépésben megadott erőforráscsoport elérésére. Ebben a lépésben a fürt erőforráscsoport jelenik meg:

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

## <a name="install-velero-on-azure-red-hat-openshift-4-cluster"></a>A Velero telepítése az Azure Red Hat OpenShift 4 fürtön

Ez a lépés telepíti a Velero a saját projektbe, valamint a biztonsági másolatok készítéséhez és a Velero-re való visszaállításához szükséges [egyéni erőforrás-definíciókat](https://kubernetes.io/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/) . Győződjön meg arról, hogy sikeresen bejelentkezett egy Azure Red Hat OpenShift v4-fürtbe.


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

## <a name="create-a-backup-with-velero"></a>Biztonsági mentés létrehozása a Velero

Az alkalmazás biztonsági másolatának Velero való létrehozásához meg kell adnia azt a névteret, amelyhez az alkalmazás tartozik.  Ha rendelkezik `nginx-example` névtérrel, és a névtérben lévő összes erőforrást szeretné felvenni a biztonsági másolatba, futtassa a következő parancsot a terminálon:

```bash
velero create backup <name of backup> --include-namespaces=nginx-example
```
A biztonsági mentés állapotát a futtatásával tekintheti meg:

```bash
oc get backups -n velero <name of backup> -o yaml
```

A sikeres biztonsági mentés kimenetet fog eredményezni `phase:Completed` , és az objektumok a Storage-fiókban lesznek élő tárolóban.

## <a name="create-a-backup-with-velero-to-include-snapshots"></a>Pillanatképeket tartalmazó biztonsági másolat létrehozása a Velero

Ahhoz, hogy egy alkalmazás biztonsági másolatát a Velero együtt hozza létre az alkalmazás állandó kötetei közé, meg kell adnia azt a névteret, amelyet az alkalmazás is tartalmaz, valamint a `snapshot-volumes=true` jelzőt a biztonsági mentés létrehozásakor.

```bash
velero backup create <name of backup> --include-namespaces=nginx-example --snapshot-volumes=true --include-cluster-resources=true
```

A biztonsági mentés állapotát a futtatásával tekintheti meg:

```bash
oc get backups -n velero <name of backup> -o yaml
```

Sikeres biztonsági mentés kimenettel `phase:Completed` , és az objektumok a Storage-fiókban lesznek élő tárolóban.

További információ a biztonsági másolatok létrehozásáról és a Velero használatával történő visszaállításról: [OpenShift-erőforrások biztonsági mentése a natív módon](https://www.openshift.com/blog/backup-openshift-resources-the-native-way)

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben egy, a Red Hat OpenShift 4 fürtözött alkalmazásról készített biztonsági mentést. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * OpenShift v4-fürt alkalmazás biztonsági másolatának létrehozása a Velero használatával
> * OpenShift v4-fürt alkalmazás biztonsági másolatának létrehozása pillanatképekkel a Velero használatával


Folytassa a következő cikktel, amelyből megtudhatja, hogyan hozhat létre egy Azure Red Hat OpenShift 4 fürtözött alkalmazás-visszaállítást.

* [Azure Red Hat OpenShift 4-fürt alkalmazás-visszaállításának létrehozása](howto-create-a-restore.md)
* [Azure Red Hat OpenShift 4 rendszerű fürtbeli alkalmazások visszaállításának létrehozása pillanatképekkel együtt](howto-create-a-restore.md)
