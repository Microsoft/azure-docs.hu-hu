---
title: Azure Files StorageClass létrehozása az Azure Red Hat OpenShift 4 rendszeren
description: Megtudhatja, hogyan hozhat létre Azure Files StorageClass az Azure Red Hat OpenShift
ms.service: container-service
ms.topic: article
ms.date: 10/16/2020
author: grantomation
ms.author: b-grodel
keywords: ARO, openshift, az ARO, Red Hat, CLI, Azure file
ms.custom: mvc
ms.openlocfilehash: c0d809324baa095b2ce8262170289e0dfec8bfd1
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2020
ms.locfileid: "92428876"
---
# <a name="create-an-azure-files-storageclass-on-azure-red-hat-openshift-4"></a>Azure Files StorageClass létrehozása az Azure Red Hat OpenShift 4 rendszeren

Ebben a cikkben egy StorageClass hoz létre az Azure Red Hat OpenShift 4 számára, amely dinamikusan kiépíti a ReadWriteMany (RWX) tárolót a Azure Files használatával. A következő témákkal fog megismerkedni:

> [!div class="checklist"]
> * Az előfeltételek beállítása és a szükséges eszközök telepítése
> * Azure Red Hat OpenShift 4 StorageClass létrehozása az Azure file kiépítő

Ha a parancssori felület helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.6.0 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="before-you-begin"></a>Előkészületek

Helyezzen üzembe egy Azure Red Hat OpenShift 4-fürtöt az előfizetésében: [Azure Red Hat OpenShift 4-fürt létrehozása](tutorial-create-cluster.md)


### <a name="set-up-azure-storage-account"></a>Azure Storage-fiók beállítása

Ez a lépés létrehoz egy erőforráscsoportot az Azure Red Hat OpenShift-fürt erőforráscsoport kívül. Ez az erőforráscsoport tartalmazza azokat a Azure Files-megosztásokat, amelyeket az Azure Red Hat OpenShift dinamikus létesítése hozott létre.

```bash
AZURE_FILES_RESOURCE_GROUP=aro_azure_files
LOCATION=eastus

az group create -l $LOCATION -n $AZURE_FILES_RESOURCE_GROUP

AZURE_STORAGE_ACCOUNT_NAME=aroazurefilessa

az storage account create \
    --name $AZURE_STORAGE_ACCOUNT_NAME \
    --resource-group $AZURE_FILES_RESOURCE_GROUP \
    --kind StorageV2 \
    --sku Standard_LRS
```

## <a name="set-permissions"></a>Engedélyek beállítása
### <a name="set-resource-group-permissions"></a>Erőforráscsoport engedélyeinek beállítása

Az ARO egyszerű szolgáltatásának az új Azure Storage-fiók erőforráscsoporthoz a "Listkeys műveletének beolvasása" engedélyre van szüksége. Rendelje hozzá a közreműködői szerepkört az eléréséhez. 

```bash
ARO_RESOURCE_GROUP=aro-rg
CLUSTER=cluster
ARO_SERVICE_PRINCIPAL_ID=$(az aro show -g $ARO_RESOURCE_GROUP -n $CLUSTER –-query servicePrincipalProfile.clientId -o tsv)

az role assignment create –-role Contributor -–assignee $ARO_SERVICE_PRINCIPAL_ID -g $AZURE_FILES_RESOURCE_GROUP
```

### <a name="set-aro-cluster-permissions"></a>ARO-fürt engedélyeinek beállítása

A OpenShift állandó mennyiségi kötési szolgáltatás fiókjának képesnek kell lennie a titkok beolvasására. Hozzon létre és rendeljen hozzá egy OpenShift-fürt szerepkört ennek eléréséhez.
```bash
ARO_API_SERVER=$(az aro list --query "[?contains(name,'$CLUSTER')].[apiserverProfile.url]" -o tsv)

oc login -u kubeadmin -p $(az aro list-credentials -g $ARO_RESOURCE_GROUP -n $CLUSTER --query=kubeadminPassword -o tsv) $APISERVER

oc create clusterrole azure-secret-reader \
    --verb=create,get \
    --resource=secrets

oc adm policy add-cluster-role-to-user azure-secret-reader system:serviceaccount:kube-system:persistent-volume-binder
```

## <a name="create-storageclass-with-azure-files-provisioner"></a>StorageClass létrehozása Azure Files-kiépítő

Ez a lépés létrehoz egy StorageClass egy Azure Files-kiépítő. A StorageClass-jegyzékben a Storage-fiók részletei szükségesek, hogy az ARO-fürt tudja megtekinteni az aktuális erőforráscsoporthoz kívüli Storage-fiókot.

```bash
cat << EOF >> azure-storageclass-azure-file.yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azure-file
provisioner: kubernetes.io/azure-file
parameters:
  location: $LOCATION
  skuName: Standard_LRS 
  storageAccount: $AZURE_STORAGE_ACCOUNT_NAME
  resourceGroup: $AZURE_FILES_RESOURCE_GROUP
reclaimPolicy: Delete
volumeBindingMode: Immediate
EOF

oc create -f azure-storageclass-azure-file.yaml
```

## <a name="change-the-default-storageclass-optional"></a>Az alapértelmezett StorageClass módosítása (nem kötelező)

Az ARO alapértelmezett StorageClass neve felügyelt prémium, és az Azure-Disk-kiépítő használatával működik. Módosítsa ezt úgy, hogy javítási parancsokat állít ki a StorageClass-jegyzékekhez.

```bash
oc patch storageclass managed-premium -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"false"}}}'

oc patch storageclass azure-file -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```

## <a name="verify-azure-file-storageclass-optional"></a>Az Azure file StorageClass ellenőrzése (nem kötelező)

Hozzon létre egy új alkalmazást, és rendelje hozzá a tárolóhoz.

```bash
oc new-project azfiletest
oc new-app –template httpd-example

#Wait for the pod to become Ready
curl $(oc get route httpd-example -n azfiletest -o jsonpath={.spec.host})

oc set volume dc/httpd-example --add --name=v1 -t pvc --claim-size=1G -m /data

#Wait for the new deployment to rollout
export POD=$(oc get pods --field-selector=status.phase==Running -o jsonpath={.items[].metadata.name})
oc exec $POD -- bash -c "mkdir ./data"
oc exec $POD -- bash -c "echo 'azure file storage' >> /data/test.txt"

oc exec $POD -- bash -c "cat /data/test.txt"
azure file storage
```
A test.txt fájl is látható lesz a Azure Portal Storage Explorer keresztül. 

## <a name="next-steps"></a>További lépések

Ebben a cikkben a dinamikus állandó tárterületet Microsoft Azure fájlok és az Azure Red Hat OpenShift 4 használatával hozta létre. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * Storage-fiók létrehozása
> * StorageClass konfigurálása Azure Red Hat OpenShift 4-fürtön az Azure Files-kiépítő használatával

A következő cikkből megismerheti az Azure Red Hat OpenShift 4 támogatott erőforrásait.

* [Azure Red Hat OpenShift-támogatási szabályzat](support-policies-v4.md)
