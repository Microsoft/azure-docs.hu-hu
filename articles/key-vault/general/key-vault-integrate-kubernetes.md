---
title: Az Azure Key Vault integrálása a Kubernetesszel
description: Ebben az oktatóanyagban a titkokat az Azure Key vaultban érheti el, és beolvashatja az Kubernetes hüvelybe való csatlakoztatáshoz a Secrets Store Container Storage Interface (CSI) illesztőprogram használatával.
author: msmbaldwin
ms.author: mbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 09/25/2020
ms.openlocfilehash: fd40ad41bda50d93943a514cd9cc3aeeab4ac948
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102198880"
---
# <a name="tutorial-configure-and-run-the-azure-key-vault-provider-for-the-secrets-store-csi-driver-on-kubernetes"></a>Oktatóanyag: az Azure Key Vault-szolgáltató konfigurálása és futtatása a Secrets Store CSI-illesztőprogramhoz a Kubernetes-ben

> [!IMPORTANT]
> A Secrets Store CSI-illesztőprogram egy nyílt forráskódú projekt, amelyet az Azure technikai támogatása nem támogat. Az oldal alján található GitHub-hivatkozásra kattintva jelentse be a CSI-illesztőprogram Key Vault integrálásával kapcsolatos összes visszajelzést és problémát. Ez az eszköz arra szolgál, hogy a felhasználók önállóan telepítsék a fürtöket, és visszajelzést gyűjtsenek a Közösségtől.

Ebben az oktatóanyagban a titkokat az Azure Key vaultban érheti el és kéri le, a Secrets Store Container Storage Interface (CSI) illesztőprogram használatával a titkokat a Kubernetes hüvelybe csatlakoztatni.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Felügyelt identitások használata.
> * Helyezzen üzembe egy Azure Kubernetes-szolgáltatási (ak-) fürtöt az Azure CLI használatával.
> * Telepítse a Helmt, a Secrets Store CSI-illesztőprogramot és Azure Key Vault szolgáltatót a CSI-illesztőprogramhoz.
> * Hozzon létre egy Azure Key vaultot, és állítsa be a titkokat.
> * Hozzon létre egy saját SecretProviderClass objektumot.
> * Üzembe helyezheti a pod-t a kulcstartóból csatlakoztatott titkos kulcsokkal.

## <a name="prerequisites"></a>Előfeltételek

* Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

* Az oktatóanyag elindítása előtt telepítse az [Azure CLI](/cli/azure/install-azure-cli-windows)-t.

Ez az oktatóanyag feltételezi, hogy az Azure Kubernetes szolgáltatást Linux-csomópontokon futtatja.

## <a name="use-managed-identities"></a>Felügyelt identitások használata

Ez az ábra az AK – Key Vault integrációs folyamatot mutatja be a felügyelt identitás esetében:

![A felügyelt identitáshoz tartozó AK – Key Vault integrációs folyamatot bemutató diagram](../media/aks-key-vault-integration-flow.png)

## <a name="deploy-an-azure-kubernetes-service-aks-cluster-by-using-the-azure-cli"></a>Azure Kubernetes szolgáltatásbeli (ak-) fürt üzembe helyezése az Azure CLI használatával

Nincs szükség a Azure Cloud Shell használatára. Az Azure CLI-vel telepített parancssor (Terminal) elegendő lesz. 

Fejezze be az [Azure Kubernetes Service-fürt üzembe helyezése az Azure CLI használatával](../../aks/kubernetes-walkthrough.md)című szakaszt az "erőforráscsoport létrehozása", "" AK-fürt létrehozása "és" kapcsolódás a fürthöz "című részekben. 

> [!NOTE] 
> Ha a pod Identity használatát tervezi, a javasolt hálózati beépülő modul a következő: `azure` . További részletekért tekintse meg a [doc](https://azure.github.io/aad-pod-identity/docs/configure/aad_pod_identity_on_kubenet/) -t. Hozza létre a Kubernetes-fürtöt az alábbi parancsban látható módon:
>
> ```azurecli
> az aks create -n contosoAKSCluster -g contosoResourceGroup --kubernetes-version 1.16.9 --node-count 1 --enable-managed-identity
> ```

1. [Állítsa a PATH környezeti változót](https://www.java.com/en/download/help/path.xml) a letöltött *kubectl.exe* fájlra.
2. A következő parancs használatával keresse meg a Kubernetes verzióját, amely az ügyfél és a kiszolgáló verzióját adja meg. Az ügyfél verziószáma a telepített *kubectl.exe* fájl, és a kiszolgáló verziója a fürtön futó Azure Kubernetes Services (ak).
    ```azurecli
    kubectl version
    ```
3. Győződjön meg arról, hogy a Kubernetes verziója 1.16.0 vagy újabb. Windows-fürtök esetén győződjön meg arról, hogy a Kubernetes verziója 1.18.0 vagy újabb. A következő parancs frissíti a Kubernetes-fürtöt és a csomópont-készletet is. A parancs végrehajtása néhány percet is igénybe vehet. Ebben a példában az erőforráscsoport *contosoResourceGroup*, a Kubernetes-fürt pedig *contosoAKSCluster*.
    ```azurecli
    az aks upgrade --kubernetes-version 1.16.9 --name contosoAKSCluster --resource-group contosoResourceGroup
    ```
4. A létrehozott AK-fürt metaadatainak megjelenítéséhez használja a következő parancsot. Másolja a **principalId**, a **clientId**, a **subscriptionId** és a **nodeResourceGroup** a későbbi használatra. Ha az AK-fürt nem lett engedélyezve a felügyelt identitásokkal, a **principalId** és a **clientId** értéke NULL lesz. 

    ```azurecli
    az aks show --name contosoAKSCluster --resource-group contosoResourceGroup
    ```

    A kimenet mindkét paraméter kiemelve jelenik meg:
    
    ![Képernyőkép az Azure CLI-ről a principalId és a clientId értékkel az Azure CLI-vel, a ](../media/kubernetes-key-vault-2.png) ![ subscriptionId és a nodeResourceGroup értékekkel kiemelve](../media/kubernetes-key-vault-3.png)
    
## <a name="install-helm-and-the-secrets-store-csi-driver"></a>A Helm és a Secrets Store CSI-illesztőprogram telepítése
> [!NOTE]
> Az alábbi telepítés csak a Linuxon futó AK-on működik. A Secrets Store CSI-illesztőprogram telepítésével kapcsolatos további információkért lásd: [Azure Key Vault Provider for Secrets Store CSI-illesztőprogram](https://azure.github.io/secrets-store-csi-driver-provider-azure/getting-started/installation/) 

A Secrets Store CSI-illesztőprogram és Azure Key Vault-szolgáltató telepítéséhez először telepítenie kell a [Helm](https://helm.sh/docs/intro/install/)-t.

A [Secrets Store CSI](https://azure.github.io/secrets-store-csi-driver-provider-azure/) -illesztőprogram felületével a Azure Key Vault-példányban tárolt titkokat szerezheti be, majd az illesztőprogram felületével csatlakoztathatja a titkos tartalmat a Kubernetes hüvelyekhez.

1. Győződjön meg arról, hogy a Helm verziója v3 vagy újabb:
    ```azurecli
    helm version
    ```
1. Telepítse a Secrets Store CSI-illesztőprogramot és a Azure Key Vault szolgáltatót az illesztőprogramhoz:
    ```azurecli
    helm repo add csi-secrets-store-provider-azure https://raw.githubusercontent.com/Azure/secrets-store-csi-driver-provider-azure/master/charts

    helm install csi-secrets-store-provider-azure/csi-secrets-store-provider-azure --generate-name
    ```
> [!NOTE] 
> Ha azt tervezi, hogy a Secrets Store CSI-illesztőprogramot és Azure Key Vault szolgáltatót használja Windows-csomópontokon, a [Helm konfigurációs értékeivel](https://github.com/Azure/secrets-store-csi-driver-provider-azure/tree/master/charts/csi-secrets-store-provider-azure#configuration) engedélyezze az illesztőprogramot és a szolgáltatót a Windows-csomópontokon.

## <a name="create-an-azure-key-vault-and-set-your-secrets"></a>Azure Key Vault létrehozása és a titkok beállítása

A saját kulcstartó létrehozásához és a titkos kulcsok beállításához kövesse az Azure CLI-vel való [Azure Key Vault titkának beállítása és beolvasása](../secrets/quick-create-cli.md)című témakör utasításait.

> [!NOTE] 
> Nem kell használnia Azure Cloud Shell, vagy létre kell hoznia egy új erőforráscsoportot. Használhatja a korábban a Kubernetes-fürthöz létrehozott erőforráscsoportot.

## <a name="create-your-own-secretproviderclass-object"></a>Saját SecretProviderClass objektum létrehozása

Ha a Secrets Store CSI-illesztőprogramhoz tartozó, szolgáltatói specifikus paraméterekkel rendelkező egyéni SecretProviderClass-objektumot szeretne létrehozni, [használja ezt a sablont](https://raw.githubusercontent.com/Azure/secrets-store-csi-driver-provider-azure/master/examples/pod-identity/v1alpha1_secretproviderclass_pod_identity.yaml). Ez az objektum identitás-hozzáférést biztosít a kulcstartóhoz.

A minta SecretProviderClass YAML fájljában adja meg a hiányzó paramétereket. A következő paraméterek szükségesek:

* **keyvaultName**: a kulcstartó neve
* **objektumok**: a csatlakoztatni kívánt titkos tartalmak listája
    * **objectName**: a titkos tartalom neve
    * **objektumtípus**: az objektum típusa (titok, kulcs, tanúsítvány)
* **tenantID**: a Key Vault bérlői azonosítója vagy CÍMTÁR-azonosítója

Az összes kötelező mező és támogatott konfiguráció dokumentációja itt érhető el: [hivatkozás](https://azure.github.io/secrets-store-csi-driver-provider-azure/getting-started/usage/#create-your-own-secretproviderclass-object)

A frissített sablon a következő kódban látható. Töltse le YAML-fájlként, és töltse ki a kötelező mezőket. Ebben a példában a Key Vault **contosoKeyVault5** van. Két titkot, **secret1** és **secret2** tartalmaz.

> [!NOTE] 
> Ha Pod-identitásokat használ, állítsa a **usePodIdentity** értéket *igaz* értékre, és állítsa be a **userAssignedIdentityID** értéket idézőjelek közé (**""**). Ha felügyelt identitásokat használ, állítsa a **useVMManagedIdentity** értéket *igaz* értékre, és állítsa be a **userAssignedIdentityID** értéket a felhasználó által hozzárendelt identitás clientID.

```yaml
apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
kind: SecretProviderClass
metadata:
  name: azure-kvname
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"                                                 # [OPTIONAL] if not provided, will default to "false". Set to "true" if using pod identities.
    useVMManagedIdentity: "false"                                           # [OPTIONAL] if not provided, will default to "false". Set to "true" if using managed identities.
    userAssignedIdentityID: "<clientID of user-assigned managed identity"   # [OPTIONAL] If you're using managed identities, use the client id to specify which user-assigned managed identity to use. If the value is empty, it defaults to use the system-assigned identity on the VM
    keyvaultName: "contosoKeyVault5"                                        # [REQUIRED] the name of the key vault
                                                                            #     az keyvault show --name contosoKeyVault5
                                                                            #     the preceding command will display the key vault metadata, which includes the subscription ID, resource group name, key vault 
    cloudName: ""                                                           # [OPTIONAL] if not provided, Azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1                                               # [REQUIRED] object name
                                                                            #     az keyvault secret list --vault-name "contosoKeyVault5"
                                                                            #     the above command will display a list of secret names from your key vault
          objectType: secret                                                # [REQUIRED] object types: secret, key, or cert
          objectVersion: ""                                                 # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: secret2
          objectType: secret
          objectVersion: ""
    tenantId: "tenantID"                                                    # [REQUIRED] the tenant ID of the key vault
```
Az alábbi képen az az kulcstartó **show--Name contosoKeyVault5** konzol kimenete látható a kapcsolódó Kiemelt metaadatokkal:

![Az "az kulcstartó show--Name contosoKeyVault5" konzol kimenetét ábrázoló képernyőkép](../media/kubernetes-key-vault-4.png)

## <a name="install-azure-active-directory-azure-ad-pod-identity"></a>Azure Active Directory (Azure AD) Pod-identitás telepítése

1. Rendeljen meghatározott szerepköröket a létrehozott AK-fürthöz. 

    Az Azure Active Directory (Azure AD) Pod-identitással rendelkező összes szükséges szerepkör-hozzárendelés dokumentációja itt található: [hivatkozás](https://azure.github.io/aad-pod-identity/docs/getting-started/role-assignment/)

    ```azurecli
    RESOURCE_GROUP=contosoResourceGroup
    
    az role assignment create --role "Managed Identity Operator" --assignee $clientId --scope /subscriptions/<SUBID>/resourcegroups/$RESOURCE_GROUP
    
    az role assignment create --role "Virtual Machine Contributor" --assignee $clientId --scope /subscriptions/<SUBID>/resourcegroups/$RESOURCE_GROUP
    ```

2. Telepítse a Azure Active Directory (Azure AD) identitását az AK-ba.

    > [!NOTE] 
    > Ha AK-fürtöt használ a kubenet hálózati beépülő modullal, tekintse át ezt a [dokumentációt](https://azure.github.io/aad-pod-identity/docs/configure/aad_pod_identity_on_kubenet/) , hogy miként helyezheti üzembe a pod-identitást a fürtben.

    ```azurecli
    helm repo add aad-pod-identity https://raw.githubusercontent.com/Azure/aad-pod-identity/master/charts

    helm install pod-identity aad-pod-identity/aad-pod-identity
    ```

3. Hozzon létre egy User-Assigned felügyelt identitást. A kimenetben másolja a **clientId** későbbi használatra.
    ```azurecli
    az identity create -g $resourceGroupName -n $identityName
    ```

4. Adja meg az identitás engedélyeit a kulcsok a kulcstartóból való beolvasásához. Használja a **clientId** a felhasználó által hozzárendelt felügyelt identitásból.
    ```azurecli
    az keyvault set-policy -n contosoKeyVault5 --secret-permissions get --spn $clientId
    az keyvault set-policy -n contosoKeyVault5 --key-permissions get --spn $clientId
    az keyvault set-policy -n contosoKeyVault5 --certificate-permissions get --spn $clientId
    ```

## <a name="deploy-your-pod-with-mounted-secrets-from-your-key-vault"></a>A pod üzembe helyezése csatlakoztatott titkos kulcsokkal a Key vaultból

A SecretProviderClass objektum konfigurálásához futtassa a következő parancsot:
```azurecli
kubectl apply -f secretProviderClass.yaml
```

### <a name="use-managed-identities"></a>Felügyelt identitások használata

Ha Pod-identitásokat használ, hozzon létre egy *AzureIdentity* a fürtben, amely a korábban létrehozott identitásra hivatkozik. Ezután hozzon létre egy *AzureIdentityBinding* , amely a létrehozott AzureIdentity hivatkozik. Adja meg a paramétereket a következő sablonban, majd mentse a *podIdentityAndBinding. YAML* néven.  

```yml
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentity
metadata:
    name: azureIdentityName                 # The name of your Azure identity
spec:
    type: 0                                 # Set type: 0 for managed identity
    resourceID: /subscriptions/<SUBID>/resourcegroups/<RESOURCEGROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<AZUREIDENTITYNAME>
    clientID: "<managed identity clientID>"   # The clientId of the User-assigned managed identity that you created earlier
---
apiVersion: aadpodidentity.k8s.io/v1
kind: AzureIdentityBinding
metadata:
    name: azure-pod-identity-binding
spec:
    azureIdentity: "azureIdentityName"      # The name of your Azure identity
    selector: azure-pod-identity-binding-selector
```
    
Futtassa a következő parancsot a kötés konfigurálásához:

```azurecli
kubectl apply -f podIdentityAndBinding.yaml
```

Ezután üzembe helyezi a pod-t. A következő kód a telepítési YAML-fájl, amely az előző lépésben a pod Identity kötést használja. Mentse ezt a fájlt *podBindingDeployment. YAML* néven.

```yml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-secrets-store-inline
  labels:
    aadpodidbinding: azure-pod-identity-binding-selector # The selector defined in AzureIdentityBinding in the previous step
spec:
  containers:
    - name: nginx
      image: nginx
      volumeMounts:
        - name: secrets-store-inline
          mountPath: "/mnt/secrets-store"
          readOnly: true
  volumes:
    - name: secrets-store-inline
      csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
          secretProviderClass: azure-kvname
```

Futtassa a következő parancsot a pod üzembe helyezéséhez:

```azurecli
kubectl apply -f podBindingDeployment.yaml
```

### <a name="check-the-pod-status-and-secret-content"></a>A pod-állapot és a titkos tartalom keresése 

Az üzembe helyezett hüvelyek megjelenítéséhez futtassa a következő parancsot:
```azurecli
kubectl get pods
```

A pod állapotának megtekintéséhez futtassa a következő parancsot:
```azurecli
kubectl describe pod/nginx-secrets-store-inline
```

![Képernyőfelvétel: a Pod "Running" állapotát megjelenítő Azure CLI-kimenet, amely az összes eseményt "normál" értékre mutat. ](../media/kubernetes-key-vault-6.png)

A kimeneti ablakban az üzembe helyezett Pod-nek *futó* állapotban kell lennie. A lenti **események** szakaszban az összes eseménytípus *normál* formában jelenik meg.

Miután ellenőrizte, hogy a pod fut-e, ellenőrizheti, hogy a pod tartalmazza-e a Key Vault titkait.

A pod-ban található összes titok megjelenítéséhez futtassa a következő parancsot:
```azurecli
kubectl exec nginx-secrets-store-inline -- ls /mnt/secrets-store/
```

Egy adott titok tartalmának megjelenítéséhez futtassa a következő parancsot:
```azurecli
kubectl exec nginx-secrets-store-inline -- cat /mnt/secrets-store/secret1
```

Ellenőrizze, hogy a titkos kód tartalma megjelenik-e.

## <a name="next-steps"></a>Következő lépések

A Key Vault helyreállításának biztosításához lásd:
> [!div class="nextstepaction"]
> [A Soft delete bekapcsolása](./key-vault-recovery.md)
