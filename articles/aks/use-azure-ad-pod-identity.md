---
title: Azure Active Directory Pod által felügyelt identitások használata az Azure Kubernetes Service-ben (előzetes verzió)
description: Ismerje meg, hogyan használhatja a HRE Pod által felügyelt identitásokat az Azure Kubernetes szolgáltatásban (ak)
services: container-service
ms.topic: article
ms.date: 12/01/2020
ms.openlocfilehash: f5b5095e95115ce5a28d81d83b725349d43ecc8f
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100635540"
---
# <a name="use-azure-active-directory-pod-managed-identities-in-azure-kubernetes-service-preview"></a>Azure Active Directory Pod által felügyelt identitások használata az Azure Kubernetes Service-ben (előzetes verzió)

Azure Active Directory Pod által felügyelt identitások a Kubernetes primitívek használatával társítják a [felügyelt identitásokat az Azure-erőforrásokhoz][az-managed-identities] és az identitásokhoz Azure Active Directory (HRE) és a hüvelyek esetében. A rendszergazdák identitásokat és kötéseket hoznak létre olyan Kubernetes, amelyek lehetővé teszik, hogy a hüvelyek hozzáférjenek az HRE használt Azure-erőforrásokhoz.

> [!NOTE]
> Ha már rendelkezik AADPODIDENTITY-telepítéssel, el kell távolítania a meglévő telepítést. A funkció engedélyezése azt jelenti, hogy nincs szükség a MIC-összetevőre.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Előkészületek

A következő erőforrást kell telepítenie:

* Az Azure CLI, a 2.8.0 vagy újabb verzió
* A `azure-preview` bővítmény 0.4.68 vagy újabb verziója

### <a name="limitations"></a>Korlátozások

* Fürthöz legfeljebb 50 Pod identitás adható meg.
* Fürt esetében legfeljebb 50 Pod Identity-kivétel engedélyezett.
* A pod által felügyelt identitások csak Linux rendszerű csomópont-készleteken érhetők el.

### <a name="register-the-enablepodidentitypreview"></a>Regisztrálja a `EnablePodIdentityPreview`

Regisztrálja a `EnablePodIdentityPreview` szolgáltatást:

```azurecli
az feature register --name EnablePodIdentityPreview --namespace Microsoft.ContainerService
```

### <a name="install-the-aks-preview-azure-cli"></a>Az `aks-preview` Azure CLI telepítése

Szüksége lesz az *AK-előnézeti* Azure CLI-bővítmény 0.4.64 vagy újabb verziójára is. Telepítse az *AK – előzetes* verzió Azure CLI-bővítményét az az [Extension Add][az-extension-add] paranccsal. Vagy telepítse az elérhető frissítéseket az az [Extension Update][az-extension-update] paranccsal.

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-aks-cluster-with-managed-identities"></a>AK-fürt létrehozása felügyelt identitásokkal

Hozzon létre egy AK-fürtöt a felügyelt identitás és a pod által felügyelt identitás engedélyezésével. A következő parancsok az [az Group Create][az-group-create] paranccsal létrehoznak egy *myResourceGroup* nevű erőforráscsoportot és az az az [AK Create][az-aks-create] parancsot egy *myAKSCluster* nevű AK-fürt létrehozásához a *myResourceGroup* erőforráscsoporthoz.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
az aks create -g myResourceGroup -n myAKSCluster --enable-managed-identity --enable-pod-identity --network-plugin azure
```

Az [az AK Get-hitelesítő adatok][az-aks-get-credentials] használatával jelentkezzen be az AK-fürtbe. Ez a parancs az ügyféltanúsítványt is letölti és konfigurálja a `kubectl` fejlesztői számítógépen.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="create-an-identity"></a>Identitás létrehozása

Hozzon létre egy identitást az [az Identity Create][az-identity-create] paranccsal, és állítsa be a *IDENTITY_CLIENT_ID* és *IDENTITY_RESOURCE_ID* változókat.

```azurecli-interactive
az group create --name myIdentityResourceGroup --location eastus
export IDENTITY_RESOURCE_GROUP="myIdentityResourceGroup"
export IDENTITY_NAME="application-identity"
az identity create --resource-group ${IDENTITY_RESOURCE_GROUP} --name ${IDENTITY_NAME}
export IDENTITY_CLIENT_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query clientId -otsv)"
export IDENTITY_RESOURCE_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query id -otsv)"
```

## <a name="assign-permissions-for-the-managed-identity"></a>Engedélyek kiosztása a felügyelt identitáshoz

A *IDENTITY_CLIENT_ID* felügyelt identitásnak rendelkeznie kell olyan olvasási engedéllyel az erőforráscsoporthoz, amely az AK-fürt virtuálisgép-méretezési csoportját tartalmazza.

```azurecli-interactive
NODE_GROUP=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NODES_RESOURCE_ID=$(az group show -n $NODE_GROUP -o tsv --query "id")
az role assignment create --role "Reader" --assignee "$IDENTITY_CLIENT_ID" --scope $NODES_RESOURCE_ID
```

## <a name="create-a-pod-identity"></a>Pod-identitás létrehozása

Hozzon létre egy Pod-identitást a fürthöz a használatával `az aks pod-identity add` .

> [!IMPORTANT]
> Az identitás és a szerepkör-kötés létrehozásához a megfelelő engedélyekkel kell rendelkeznie, például az `Owner` előfizetésben.

```azurecli-interactive
export POD_IDENTITY_NAME="my-pod-identity"
export POD_IDENTITY_NAMESPACE="my-app"
az aks pod-identity add --resource-group myResourceGroup --cluster-name myAKSCluster --namespace ${POD_IDENTITY_NAMESPACE}  --name ${POD_IDENTITY_NAME} --identity-resource-id ${IDENTITY_RESOURCE_ID}
```

> [!NOTE]
> Ha engedélyezi a pod által felügyelt identitást az AK-fürtön, akkor a *rendszer a Kube-System* névtérhez adja hozzá az AzurePodIdentityException *-addon nevű kivételt* . A AzurePodIdentityException lehetővé teszi, hogy a hüvelyek bizonyos címkékkel férjenek hozzá az Azure Instance Metadata Service (IMDS) végponthoz anélkül, hogy a csomópont által felügyelt Identity (NMI) kiszolgáló elfogy volna. Az *AK-addon-Exception* lehetővé teszi, hogy a rendszer a AzurePodIdentityException manuális konfigurálása nélkül működjön, például a HRE Pod által felügyelt identitást. Igény szerint hozzáadhat, eltávolíthat és frissíthet egy AzurePodIdentityException a,, vagy rendszer `az aks pod-identity exception add` használatával `az aks pod-identity exception delete` `az aks pod-identity exception update` `kubectl` .

## <a name="run-a-sample-application"></a>Minta alkalmazás futtatása

Ahhoz, hogy egy Pod HRE Pod-felügyelt identitást használjon, a pod-nek olyan *aadpodidbinding* -címkével kell rendelkeznie, amely megfelel egy *AzureIdentityBinding* megadott választójának. Ha HRE Pod által felügyelt identitást használó minta alkalmazást szeretne futtatni, hozzon létre egy `demo.yaml` fájlt a következő tartalommal. Cserélje le a *POD_IDENTITY_NAME*, *IDENTITY_CLIENT_ID* és *IDENTITY_RESOURCE_GROUP* értéket az előző lépések értékeire. Cserélje le a *SUBSCRIPTION_IDt* az előfizetés-azonosítójával.

> [!NOTE]
> Az előző lépésekben létrehozta a *POD_IDENTITY_NAME*, *IDENTITY_CLIENT_ID* és *IDENTITY_RESOURCE_GROUP* változókat. A `echo` változókhoz megadott értéket például a következő parancs használatával jelenítheti meg: `echo $IDENTITY_NAME` .

```yml
apiVersion: v1
kind: Pod
metadata:
  name: demo
  labels:
    aadpodidbinding: POD_IDENTITY_NAME
spec:
  containers:
  - name: demo
    image: mcr.microsoft.com/oss/azure/aad-pod-identity/demo:v1.6.3
    args:
      - --subscriptionid=SUBSCRIPTION_ID
      - --clientid=IDENTITY_CLIENT_ID
      - --resourcegroup=IDENTITY_RESOURCE_GROUP
    env:
      - name: MY_POD_NAME
        valueFrom:
          fieldRef:
            fieldPath: metadata.name
      - name: MY_POD_NAMESPACE
        valueFrom:
          fieldRef:
            fieldPath: metadata.namespace
      - name: MY_POD_IP
        valueFrom:
          fieldRef:
            fieldPath: status.podIP
  nodeSelector:
    kubernetes.io/os: linux
```

Figyelje meg, hogy a pod-definíció egy olyan *aadpodidbinding* -címkével rendelkezik, amely megegyezik az előző lépésben futtatott Pod-identitás nevével `az aks pod-identity add` .

Üzembe helyezés `demo.yaml` ugyanarra a névtérre, mint a pod-identitás a következő használatával `kubectl apply` :

```azurecli-interactive
kubectl apply -f demo.yaml --namespace $POD_IDENTITY_NAMESPACE
```

Ellenőrizze, hogy az alkalmazás sikeresen fut-e a használatával `kubectl logs` .

```azurecli-interactive
kubectl logs demo --follow --namespace $POD_IDENTITY_NAMESPACE
```

Győződjön meg arról, hogy a naplók a jogkivonat sikeres megszerzését mutatják be, a *Get* művelet pedig sikeres.
 
```output
...
successfully doARMOperations vm count 0
successfully acquired a token using the MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token)
successfully acquired a token, userAssignedID MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token) clientID(xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)
successfully made GET on instance metadata
...
```

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

A HRE Pod által felügyelt identitás fürtből való eltávolításához távolítsa el a minta alkalmazást és a pod-identitást a fürtből. Ezután távolítsa el az identitást.

```azurecli-interactive
kubectl delete pod demo --namespace $POD_IDENTITY_NAMESPACE
az aks pod-identity delete --name ${POD_IDENTITY_NAME} --namespace ${POD_IDENTITY_NAMESPACE} --resource-group myResourceGroup --cluster-name myAKSCluster
az identity delete -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME}
```

## <a name="next-steps"></a>Következő lépések

A felügyelt identitásokkal kapcsolatos további információkért lásd: [felügyelt identitások az Azure-erőforrásokhoz][az-managed-identities].

<!-- LINKS - external -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add&preserve-view=true
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update&preserve-view=true
[az-group-create]: /cli/azure/group#az-group-create
[az-identity-create]: /cli/azure/identity?view=azure-cli-latest#az_identity_create
[az-managed-identities]: ../active-directory/managed-identities-azure-resources/overview.md
[az-role-assignment-create]: /cli/azure/role/assignment?view=azure-cli-latest#az_role_assignment_create
