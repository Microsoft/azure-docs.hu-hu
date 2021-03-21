---
title: Az Azure AD-identitás használata a webszolgáltatással
titleSuffix: Azure Machine Learning
description: Az Azure AD-identitást az Azure Kubernetes szolgáltatásban található webszolgáltatással érheti el a Felhőbeli erőforrások eléréséhez a pontozás során.
services: machine-learning
ms.author: larryfr
author: BlackMist
ms.reviewer: aashishb
ms.service: machine-learning
ms.subservice: core
ms.date: 11/16/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: b6e6288f125da2a29a8eff56b64f327914f90cb4
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102520472"
---
# <a name="use-azure-ad-identity-with-your-machine-learning-web-service-in-azure-kubernetes-service"></a>Azure AD-identitás használata a gépi tanulási webszolgáltatással az Azure Kubernetes Service-ben

Ebben az útmutatóban megtudhatja, hogyan rendelhet hozzá egy Azure Active Directory (Azure AD) identitást az üzembe helyezett Machine learning-modellhez az Azure Kubernetes szolgáltatásban. Az [Azure ad Pod Identity](https://github.com/Azure/aad-pod-identity) projekt lehetővé teszi, hogy az alkalmazások biztonságosan hozzáférjenek a felhőalapú erőforrásokhoz az Azure ad-vel [felügyelt identitás](../active-directory/managed-identities-azure-resources/overview.md) -és Kubernetes primitívek használatával. Ez lehetővé teszi a webszolgáltatás számára az Azure-erőforrások biztonságos elérését anélkül, hogy hitelesítő adatokat kellene beágyaznia vagy a tokeneket közvetlenül a `score.py` szkriptben kezelnie. Ez a cikk bemutatja, hogyan hozhat létre és telepíthet Azure-identitást az Azure Kubernetes Service-fürtben, és hogyan rendelheti hozzá az identitást az üzembe helyezett webszolgáltatáshoz.

## <a name="prerequisites"></a>Előfeltételek

- Az [Azure CLI-bővítmény a Machine learning szolgáltatáshoz](reference-azure-machine-learning-cli.md), a [pythonhoz készült Azure Machine learning SDK](/python/api/overview/azure/ml/intro)-hoz vagy a [Visual Studio Code](tutorial-setup-vscode-extension.md)-hoz készült Azure Machine learning.

- Hozzáférés az AK-fürthöz a `kubectl` parancs használatával. További információ: [Kapcsolódás a fürthöz](../aks/kubernetes-walkthrough.md#connect-to-the-cluster)

- Egy Azure Machine Learning webszolgáltatás üzembe helyezése az AK-fürtön.

## <a name="create-and-install-an-azure-identity"></a>Azure-identitás létrehozása és telepítése

1. A következő paranccsal megállapíthatja, hogy az AK-fürt Kubernetes-RBAC engedélyezve van-e:

    ```azurecli-interactive
    az aks show --name <AKS cluster name> --resource-group <resource group name> --subscription <subscription id> --query enableRbac
    ```

    Ez a parancs egy értéket ad vissza, `true` Ha a KUBERNETES RBAC engedélyezve van. Ez az érték határozza meg a következő lépésben használandó parancsot.

1. Telepítse az [Azure ad Pod-identitást](https://azure.github.io/aad-pod-identity/docs/getting-started/installation/) az AK-fürtbe.

1. [Hozzon létre egy identitást az Azure](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#2-create-an-identity-on-azure) -ban az Azure ad Pod Identity projekt oldalon látható lépések követésével.

1. A [AzureIdentity üzembe helyezéséhez](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#3-deploy-azureidentity) kövesse az Azure ad Pod Identity Project oldalon látható lépéseket.

1. A [AzureIdentityBinding üzembe helyezéséhez](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#5-deploy-azureidentitybinding) kövesse az Azure ad Pod Identity Project oldalon látható lépéseket.

1. Ha az előző lépésben létrehozott Azure-identitás nem az AK-fürthöz tartozó csomópont-erőforráscsoport része, kövesse az Azure AD Pod Identity Project oldalon megjelenő [szerepkör-hozzárendelési](https://azure.github.io/aad-pod-identity/docs/getting-started/role-assignment/#user-assigned-identities-that-are-not-within-the-node-resource-group) lépéseket.

## <a name="assign-azure-identity-to-web-service"></a>Azure-identitás kiosztása a webszolgáltatáshoz

A következő lépések az előző szakaszban létrehozott Azure-identitást használják, és a **választó címkén** keresztül rendelheti hozzá az AK-webszolgáltatáshoz.

Először azonosítsa az üzemelő példány nevét és névterét az AK-fürtben, amelyhez hozzá szeretné rendelni az Azure-identitást. Ezt az információt a következő parancs futtatásával kérheti le. A névtereknek a Azure Machine Learning-munkaterület nevével kell rendelkezniük, és a telepítési névnek a-portálon láthatónak kell lennie a végpont neveként.

```azurecli-interactive
kubectl get deployment --selector=isazuremlapp=true --all-namespaces --show-labels
```

Az üzembe helyezési specifikáció szerkesztésével adja hozzá az Azure Identity választó címkét a központi telepítéshez. A választó értékének a [AzureIdentityBinding üzembe helyezésének](https://azure.github.io/aad-pod-identity/docs/demo/standard_walkthrough/#5-deploy-azureidentitybinding)5. lépésében megadott értéknek kell lennie.

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: demo1-azure-identity-binding
spec:
  AzureIdentity: <a-idname>
  Selector: <label value to match>
```

Szerkessze az üzembe helyezést az Azure Identity választó címkéjének hozzáadásához. Lépjen a következő szakaszra `/spec/template/metadata/labels` . A következő értékeket kell megjelennie: `isazuremlapp: “true”` . Adja hozzá az alább látható HRE-Pod-Identity címkét.

```azurecli-interactive
    kubectl edit deployment/<name of deployment> -n azureml-<name of workspace>
```

```yaml
spec:
  template:
    metadata:
      labels:
       aadpodidbinding: "<value of Selector in AzureIdentityBinding>"
      ...
```

A címke megfelelő hozzáadásának ellenőrzéséhez futtassa a következő parancsot. Az újonnan létrehozott hüvelyek állapotát is látnia kell.

```azurecli-interactive
   kubectl get pod -n azureml-<name of workspace> --show-labels
```

Miután a hüvely működik, az üzembe helyezéshez használt webszolgáltatások mostantól hozzáférhetnek az Azure-identitáshoz az Azure-identitáson keresztül anélkül, hogy be kellene ágyaznia a hitelesítő adatokat a kódban.

## <a name="assign-roles-to-your-azure-identity"></a>Szerepkörök társítása az Azure-identitáshoz

[Rendelje hozzá az Azure felügyelt identitását megfelelő szerepkörökkel](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) más Azure-erőforrások eléréséhez. Győződjön meg arról, hogy a hozzárendelt szerepkörök megfelelő **Adatműveletekkel** rendelkeznek. Előfordulhat például, hogy a [Storage blob-Adatolvasó szerepkör](../role-based-access-control/built-in-roles.md#storage-blob-data-reader) olvasási engedéllyel rendelkezik a Storage-blobhoz, amíg az általános [olvasó szerepkör](../role-based-access-control/built-in-roles.md#reader) nem lesz.

## <a name="use-azure-identity-with-your-web-service"></a>Az Azure Identity használata a webszolgáltatással

Modell üzembe helyezése az AK-fürtön. A `score.py` szkript olyan műveleteket tartalmazhat, amelyek az Azure-identitáshoz hozzáférő Azure-erőforrásokra mutatnak. Győződjön meg arról, hogy telepítette a szükséges ügyféloldali függvénytár-függőségeket ahhoz az erőforráshoz, amelyhez hozzáférni próbál. Az alábbiakban néhány példát láthat arra, hogyan használhatja Azure-identitását különböző Azure-erőforrások elérésére a szolgáltatásból.

### <a name="access-key-vault-from-your-web-service"></a>Hozzáférés Key Vault a webszolgáltatásból

Ha az Azure Identity olvasási hozzáférést kapott egy **Key Vaultban** található titkos kulcshoz, az a `score.py` következő kód használatával férhet hozzá.

```python
from azure.identity import DefaultAzureCredential
from azure.keyvault.secrets import SecretClient

my_vault_name = "yourkeyvaultname"
my_vault_url = "https://{}.vault.azure.net/".format(my_vault_name)
my_secret_name = "sample-secret"

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
secret_client = SecretClient(
    vault_url=my_vault_url,
    credential=credential)
secret = secret_client.get_secret(my_secret_name)
```

> [!IMPORTANT]
> Ez a példa a DefaultAzureCredential használja. Ha hozzáférést szeretne biztosítani az identitáshoz egy adott hozzáférési házirend használatával, tekintse meg a [Key Vault hozzáférési szabályzat hozzárendelése az Azure CLI használatával](../key-vault/general/assign-access-policy-cli.md)című témakört.

### <a name="access-blob-from-your-web-service"></a>A blob elérése a webszolgáltatásból

Ha az Azure Identity olvasási hozzáférést kapott a **Storage-blobon** belüli adatokhoz, az a `score.py` következő kód használatával férhet hozzá.

```python
from azure.identity import DefaultAzureCredential
from azure.storage.blob import BlobServiceClient

my_storage_account_name = "yourstorageaccountname"
my_storage_account_url = "https://{}.blob.core.windows.net/".format(my_storage_account_name)

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
blob_service_client = BlobServiceClient(
    account_url=my_storage_account_url,
    credential=credential
)
blob_client = blob_service_client.get_blob_client(container="some-container", blob="some_text.txt")
blob_data = blob_client.download_blob()
blob_data.readall()
```

## <a name="next-steps"></a>Következő lépések

* A Python Azure Identity ügyféloldali kódtár használatával kapcsolatos további információkért tekintse meg a [tárházat](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/identity/azure-identity#azure-identity-client-library-for-python) a githubon.
* A modellek Azure Kubernetes Service-fürtökön való üzembe helyezésével kapcsolatos részletes útmutatóért lásd: [útmutató](how-to-deploy-azure-kubernetes-service.md).