---
title: Pod által Azure Active Directory identitások használata a Azure Kubernetes Service (előzetes verzió)
description: Megtudhatja, hogyan használhatja az AAD pod által felügyelt felügyelt identitásokat Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 3/12/2021
ms.openlocfilehash: f090f5e11688f35ce090bb07ec0d23530bf9d90e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777850"
---
# <a name="use-azure-active-directory-pod-managed-identities-in-azure-kubernetes-service-preview"></a>Pod által Azure Active Directory identitások használata a Azure Kubernetes Service (előzetes verzió)

Azure Active Directory pod által felügyelt identitások Kubernetes-primitívek használatával társítják a podokhoz az Azure Active Directory -ban (AAD) található [Azure-erőforrások][az-managed-identities] és identitások felügyelt identitását. A rendszergazdák Kubernetes-primitívekként hoznak létre identitásokat és kötéseket, amelyek lehetővé teszik a podok számára az AAD-t identitásszolgáltatóként kezelő Azure-erőforrásokhoz való hozzáférést.

> [!NOTE]
> Ha már telepítettE az AADPODIDENTITY-t, el kell távolítania a meglévőt. A funkció engedélyezése azt jelenti, hogy nincs szükség a MIC összetevőre.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Előkészületek

A következő erőforrásnak kell telepítve lennie:

* Az Azure CLI 2.20.0-s vagy újabb verziója
* A `azure-preview` bővítmény 0.5.5-ös vagy újabb verziója

### <a name="limitations"></a>Korlátozások

* Egy fürthöz legfeljebb 200 pod-identitás engedélyezett.
* Fürtön legfeljebb 200 podidentitási kivétel engedélyezett.
* A pod által felügyelt identitások csak Linux-csomópontkészleten érhetők el.

### <a name="register-the-enablepodidentitypreview"></a>Regisztrálja a következőt: `EnablePodIdentityPreview`

A funkció `EnablePodIdentityPreview` regisztrálása:

```azurecli
az feature register --name EnablePodIdentityPreview --namespace Microsoft.ContainerService
```

### <a name="install-the-aks-preview-azure-cli"></a>Az `aks-preview` Azure CLI telepítése

Szüksége lesz az *aks-preview Azure CLI-bővítmény* 0.4.64-es vagy újabb verziójára is. Telepítse *az aks-preview* Azure CLI-bővítményt az [az extension add paranccsal.][az-extension-add] Vagy telepítse az elérhető frissítéseket az [az extension update paranccsal.][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-aks-cluster-with-azure-cni"></a>AKS-fürt létrehozása Azure CNI

> [!NOTE]
> Ez az alapértelmezett ajánlott konfiguráció

AKS-fürt létrehozása engedélyezett Azure CNI és pod által felügyelt identitással. A következő parancsok az [az group create][az-group-create] paranccsal hoznak létre egy *myResourceGroup* nevű erőforráscsoportot és az [az aks create][az-aks-create] parancsot egy *myAKSCluster* nevű AKS-fürt létrehozásához a *myResourceGroup* erőforráscsoportban.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
az aks create -g myResourceGroup -n myAKSCluster --enable-pod-identity --network-plugin azure
```

Az [az aks get-credentials használatával][az-aks-get-credentials] jelentkezzen be az AKS-fürtbe. Ez a parancs letölti és konfigurálja az ügyfél-tanúsítványt `kubectl` a fejlesztői számítógépen.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="update-an-existing-aks-cluster-with-azure-cni"></a>Meglévő AKS-fürt frissítése Azure CNI

Frissít egy meglévő AKS-fürtöt Azure CNI, hogy pod által felügyelt identitást tartalmaznak.

```azurecli-interactive
az aks update -g $MY_RESOURCE_GROUP -n $MY_CLUSTER --enable-pod-identity --network-plugin azure
```
## <a name="using-kubenet-network-plugin-with-azure-active-directory-pod-managed-identities"></a>Kubenet hálózati beépülő modul használata Azure Active Directory pod által felügyelt identitásokkal 

> [!IMPORTANT]
> Az aad-pod-identity kubenetet futtató fürtben való futtatása biztonsági okokból nem ajánlott konfiguráció. Mielőtt engedélyez egy aad-pod-identityt egy Kubenet-fürtben, kövesse a megoldás lépéseit és konfigurálja a szabályzatokat.

## <a name="mitigation"></a>Kockázatcsökkentés

A fürt szintjén a biztonsági rés csökkentéséhez használhatja az OpenPolicyAgent belépésvezérlőt és a webhookot érvényesítő Gatekeepert. Ha a Gatekeeper már telepítve van a fürtben, adja hozzá a K8sPSPCapabilities típusú ConstraintTemplate típust:

```
kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper-library/master/library/pod-security-policy/capabilities/template.yaml
```
Adjon hozzá egy sablont a podok a NET_RAW korlátozására:

```
apiVersion: constraints.gatekeeper.sh/v1beta1
kind: K8sPSPCapabilities
metadata:
  name: prevent-net-raw
spec:
  match:
    kinds:
      - apiGroups: [""]
        kinds: ["Pod"]
    excludedNamespaces:
      - "kube-system"
  parameters:
    requiredDropCapabilities: ["NET_RAW"]
```

## <a name="create-an-aks-cluster-with-kubenet-network-plugin"></a>AKS-fürt létrehozása a Kubenet hálózati beépülő modullal

AKS-fürt létrehozása engedélyezett Kubenet hálózati beépülő modullal és pod által felügyelt identitással.

```azurecli-interactive
az aks create -g $MY_RESOURCE_GROUP -n $MY_CLUSTER --enable-pod-identity --enable-pod-identity-with-kubenet
```

## <a name="update-an-existing-aks-cluster-with-kubenet-network-plugin"></a>Meglévő AKS-fürt frissítése a Kubenet hálózati beépülő modullal

Frissítsen egy meglévő AKS-fürtöt a Kubnet hálózati beépülő modullal, hogy tartalmazza a pod által felügyelt identitást.

```azurecli-interactive
az aks update -g $MY_RESOURCE_GROUP -n $MY_CLUSTER --enable-pod-identity --enable-pod-identity-with-kubenet
```

## <a name="create-an-identity"></a>Identitás létrehozása

Hozzon létre egy identitást [az az identity create][az-identity-create] használatával, és állítsa be *IDENTITY_CLIENT_ID* *és* IDENTITY_RESOURCE_ID változókat.

```azurecli-interactive
az group create --name myIdentityResourceGroup --location eastus
export IDENTITY_RESOURCE_GROUP="myIdentityResourceGroup"
export IDENTITY_NAME="application-identity"
az identity create --resource-group ${IDENTITY_RESOURCE_GROUP} --name ${IDENTITY_NAME}
export IDENTITY_CLIENT_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query clientId -otsv)"
export IDENTITY_RESOURCE_ID="$(az identity show -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME} --query id -otsv)"
```

## <a name="assign-permissions-for-the-managed-identity"></a>Engedélyek hozzárendelése a felügyelt identitáshoz

A *IDENTITY_CLIENT_ID* identitásnak Olvasó engedéllyel kell rendelkeznie abban az erőforráscsoportban, amely az AKS-fürt virtuálisgép-méretezési csoportját tartalmazza.

```azurecli-interactive
NODE_GROUP=$(az aks show -g myResourceGroup -n myAKSCluster --query nodeResourceGroup -o tsv)
NODES_RESOURCE_ID=$(az group show -n $NODE_GROUP -o tsv --query "id")
az role assignment create --role "Reader" --assignee "$IDENTITY_CLIENT_ID" --scope $NODES_RESOURCE_ID
```

## <a name="create-a-pod-identity"></a>Podidentitás létrehozása

Hozzon létre egy podidentitást a fürthöz a `az aks pod-identity add` használatával.

> [!IMPORTANT]
> Az identitás- és szerepkörkötés létrehozásához megfelelő engedélyekkel kell rendelkeznie az `Owner` előfizetésében( például ).

```azurecli-interactive
export POD_IDENTITY_NAME="my-pod-identity"
export POD_IDENTITY_NAMESPACE="my-app"
az aks pod-identity add --resource-group myResourceGroup --cluster-name myAKSCluster --namespace ${POD_IDENTITY_NAMESPACE}  --name ${POD_IDENTITY_NAME} --identity-resource-id ${IDENTITY_RESOURCE_ID}
```

> [!NOTE]
> Ha engedélyezi a pod által felügyelt identitást az AKS-fürtön, egy *aks-addon-exception* nevű AzurePodIdentityException lesz hozzáadva a *kube-system* névtérhez. Az AzurePodIdentityException lehetővé teszi, hogy bizonyos címkékkel a podok anélkül férnek hozzá az Azure Instance Metadata Service- (IMDS-) végponthoz, hogy a csomópont által felügyelt identitáskiszolgáló elfogja őket. Az *aks-addon-exception* lehetővé teszi az AKS belső bővítményei, például az AAD pod által felügyelt identitások működését anélkül, hogy manuálisan konfigurálnia kell az AzurePodIdentityException kivételt. Másik lehetőségként hozzáadhat, eltávolíthat és frissíthet egy AzurePodIdentityException-t a `az aks pod-identity exception add` `az aks pod-identity exception delete` , , vagy `az aks pod-identity exception update` `kubectl` használatával.

## <a name="run-a-sample-application"></a>Mintaalkalmazás futtatása

Ahhoz, hogy egy pod AAD pod által felügyelt identitást használjon, a podnak szüksége van egy *aadpodidbinding* címkére, amely egy olyan értékkel egyezik, amely megegyezik egy *AzureIdentityBinding-választóval.* Mintaalkalmazás AAD pod által felügyelt identitással való futtatásához hozzon létre egy fájlt az `demo.yaml` alábbi tartalommal. Cserélje *POD_IDENTITY_NAME*, *IDENTITY_CLIENT_ID* és *IDENTITY_RESOURCE_GROUP* az előző lépések értékeire. Cserélje *SUBSCRIPTION_ID* az előfizetés azonosítójára.

> [!NOTE]
> Az előző lépésekben létrehozta a POD_IDENTITY_NAME *,* *IDENTITY_CLIENT_ID* és *IDENTITY_RESOURCE_GROUP* változókat. A változókhoz beállított érték megjelenítéséhez használhat például egy `echo` parancsot. `echo $IDENTITY_NAME`

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

Figyelje meg, hogy a poddefiníció rendelkezik *egy aadpodidbinding címkével,* amely egy olyan értékkel rendelkezik, amely megegyezik az előző lépésben futtatott podidentitás `az aks pod-identity add` nevével.

A használatával telepítse az üzembe helyezést a `demo.yaml` podidentitással azonos `kubectl apply` névtérbe:

```azurecli-interactive
kubectl apply -f demo.yaml --namespace $POD_IDENTITY_NAMESPACE
```

Ellenőrizze, hogy a mintaalkalmazás sikeresen fut-e a `kubectl logs` használatával.

```azurecli-interactive
kubectl logs demo --follow --namespace $POD_IDENTITY_NAMESPACE
```

Ellenőrizze, hogy a naplókban a jogkivonat sikeres beszerzése és a GET művelet *sikeres* volt-e.
 
```output
...
successfully doARMOperations vm count 0
successfully acquired a token using the MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token)
successfully acquired a token, userAssignedID MSI, msiEndpoint(http://169.254.169.254/metadata/identity/oauth2/token) clientID(xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx)
successfully made GET on instance metadata
...
```

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Ha el szeretné távolítani az AAD pod által felügyelt identitást a fürtből, távolítsa el a mintaalkalmazást és a podidentitást a fürtből. Ezután távolítsa el az identitást.

```azurecli-interactive
kubectl delete pod demo --namespace $POD_IDENTITY_NAMESPACE
az aks pod-identity delete --name ${POD_IDENTITY_NAME} --namespace ${POD_IDENTITY_NAMESPACE} --resource-group myResourceGroup --cluster-name myAKSCluster
az identity delete -g ${IDENTITY_RESOURCE_GROUP} -n ${IDENTITY_NAME}
```

## <a name="next-steps"></a>Következő lépések

A felügyelt identitásokkal kapcsolatos további információkért lásd: [Azure-erőforrások felügyelt identitása.][az-managed-identities]

<!-- LINKS - external -->
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-group-create]: /cli/azure/group#az_group_create
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-managed-identities]: ../active-directory/managed-identities-azure-resources/overview.md
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
