---
title: Felügyelt identitások használata a hozzáférés-vezérléshez (előzetes verzió)
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan kezelheti az Azure-erőforrásokhoz való hozzáférést Azure Machine Learning munkaterületről a felügyelt identitások használatával.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 3246f6668a653c05599001a2f8d8fc8d376936ce
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105642420"
---
# <a name="use-managed-identities-with-azure-machine-learning-preview"></a>Felügyelt identitások használata Azure Machine Learning (előzetes verzió)

A [felügyelt identitások](../active-directory/managed-identities-azure-resources/overview.md) lehetővé teszik a munkaterület konfigurálását az *erőforrásokhoz való hozzáférés minimálisan szükséges engedélyeivel*. 

Azure Machine Learning munkaterület megbízható módon történő konfigurálásakor fontos biztosítani, hogy a munkaterülethez társított különböző szolgáltatások rendelkezzenek a megfelelő szintű hozzáféréssel. A Machine learning-munkafolyamatok során például a munkaterületnek hozzá kell férnie a Docker-rendszerképekhez tartozó Azure Container Registry (ACR) és a betanítási adatgyűjtési fiókok számára. 

Emellett a felügyelt identitások lehetővé teszik az engedélyek részletes szabályozását, például megadhatja vagy visszavonhatja a hozzáférést adott számítási erőforrásokból egy adott ACR számára.

Ebből a cikkből megtudhatja, hogyan használhatja a felügyelt identitásokat a következőkre:

 * Konfigurálja és használja az ACR-t az Azure Machine Learning munkaterülethez anélkül, hogy engedélyeznie kellene a rendszergazdai felhasználói hozzáférést az ACR-hez.
 * A saját munkaterületén kívüli privát ACR-t is elérheti, hogy lekérje az alapképek betanítását vagy következtetését.
 * Hozzon létre egy munkaterületet a felhasználó által hozzárendelt felügyelt identitással a társított erőforrások eléréséhez.

> [!IMPORTANT]
> A felügyelt identitások használatával szabályozhatja az erőforrásokhoz való hozzáférést Azure Machine Learning jelenleg előzetes verzióban érhető el. Az előzetes verzió funkcióit a rendszer a támogatás és a szolgáltatói szerződés garanciája nélkül biztosítja. További információkért tekintse meg a [Microsoft Azure előzetesek használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="prerequisites"></a>Előfeltételek

- Egy Azure Machine Learning-munkaterület. További információ: [Azure Machine learning munkaterület létrehozása](how-to-manage-workspace.md).
- [Machine learning szolgáltatás Azure CLI-bővítménye](reference-azure-machine-learning-cli.md)
- A [Azure Machine learning PYTHON SDK](/python/api/overview/azure/ml/intro)-t.
- A szerepkörök hozzárendeléséhez az Azure-előfizetéshez tartozó bejelentkezési azonosítónak rendelkeznie kell a [felügyelt identitás-kezelő](../role-based-access-control/built-in-roles.md#managed-identity-operator) szerepkörrel, vagy más olyan szerepkörrel, amely a szükséges műveleteket (például a __tulajdonost__) biztosítja.
- Ismernie kell a [felügyelt identitások](../active-directory/managed-identities-azure-resources/overview.md)létrehozását és a velük való munkát.

## <a name="configure-managed-identities"></a>Felügyelt identitások konfigurálása

Bizonyos helyzetekben szükség van a rendszergazda felhasználók Azure Container Registryhoz való hozzáférésének letiltására. Előfordulhat például, hogy az ACR meg van osztva, és nem engedélyezi más felhasználók rendszergazdai hozzáférését. Vagy ha az ACR-t a rendszergazda felhasználóval engedélyezte, az előfizetési szint házirendjében nem engedélyezett.

> [!IMPORTANT]
> Ha az Azure Container instance (ACI) szolgáltatáshoz Azure Machine Learningt használ, az ACR-hez rendszergazdai felhasználói hozzáférésre van __szükség__. Ha nem szeretné letiltani, akkor ne tiltsa le, ha modellek üzembe helyezését tervezi az ACI-nak a következtetéshez.

Ha az ACR-t a rendszergazda felhasználói hozzáférés engedélyezése nélkül hozza létre, a rendszer felügyelt identitásokat használ az ACR eléréséhez a Docker-rendszerképek létrehozásához és lekéréséhez.

A munkaterület létrehozásakor letilthatja a saját ACR-t a rendszergazda felhasználóval. Azt is megteheti, hogy Azure Machine Learning létrehozni az ACR-t, és később letiltja a rendszergazda felhasználót.

### <a name="bring-your-own-acr"></a>Saját ACR használata

Ha az ACR rendszergazdai felhasználót nem engedélyezi az előfizetés-szabályzat, először létre kell hoznia az ACR-t rendszergazdai felhasználó nélkül, majd hozzá kell rendelnie a munkaterülethez. Továbbá, ha a meglévő ACR-t a rendszergazda felhasználó letiltotta, csatolhatja azt a munkaterülethez.

[Hozzon létre ACR-t az Azure CLI-ből az](../container-registry/container-registry-get-started-azure-cli.md) argumentum beállítása nélkül ```--admin-enabled``` , vagy Azure Portal a rendszergazda felhasználó engedélyezése nélkül. Ezután Azure Machine Learning munkaterület létrehozásakor adja meg az ACR Azure-erőforrás-AZONOSÍTÓját. Az alábbi példa bemutatja, hogyan hozhat létre egy meglévő ACR-t használó új Azure ML-munkaterületet:

> [!TIP]
> A paraméter értékének lekéréséhez `--container-registry` használja az az [ACR show](/cli/azure/acr#az_acr_show) parancsot az ACR adatainak megjelenítéséhez. A `id` mező tartalmazza az ACR erőforrás-azonosítóját.

```azurecli-interactive
az ml workspace create -w <workspace name> \
-g <workspace resource group> \
-l <region> \
--container-registry /subscriptions/<subscription id>/resourceGroups/<acr resource group>/providers/Microsoft.ContainerRegistry/registries/<acr name>
```

### <a name="let-azure-machine-learning-service-create-workspace-acr"></a>Azure Machine Learning szolgáltatás munkaterületének létrehozása ACR

Ha nem hoz létre saját ACR-t, Azure Machine Learning szolgáltatás létrehoz egyet, ha olyan műveletet hajt végre, amelynek szüksége van rá. Tegyük fel például, hogy beküld egy betanítást Machine Learning Computeba, létrehoz egy környezetet, vagy üzembe helyez egy webszolgáltatás-végpontot. A munkaterület által létrehozott ACR-nek engedélyezve lesz a rendszergazda felhasználója, és manuálisan le kell tiltania a rendszergazda felhasználót.

1. Új munkaterület létrehozása

    ```azurecli-interactive
    az ml workspace show -n <my workspace> -g <my resource group>
    ```

1. Olyan műveletet hajtson végre, amely megköveteli az ACR használatát. Például a [modell betanításának oktatóanyaga](tutorial-train-models-with-aml.md).

1. A fürt által létrehozott ACR-név lekérése:

    ```azurecli-interactive
    az ml workspace show -w <my workspace> \
    -g <my resource group>
    --query containerRegistry
    ```

    Ez a parancs az alábbi szöveghez hasonló értéket ad vissza. Csak a szöveg utolsó részét szeretné használni, amely az ACR-példány neve:

    ```output
    /subscriptions/<subscription id>/resourceGroups/<my resource group>/providers/MicrosoftContainerReggistry/registries/<ACR instance name>
    ```

1. Az ACR frissítése a rendszergazda felhasználó letiltásához:

    ```azurecli-interactive
    az acr update --name <ACR instance name> --admin-enabled false
    ```

### <a name="create-compute-with-managed-identity-to-access-docker-images-for-training"></a>Hozzon létre egy felügyelt identitással rendelkező számítást a Docker-rendszerképek betanításához való hozzáféréshez

A munkaterület ACR eléréséhez hozzon létre egy gépi tanulási számítási fürtöt, amelynek engedélyezve van a rendszerhez rendelt felügyelt identitás. Az identitást Azure Portal vagy studióból is engedélyezheti számítás létrehozásakor vagy az Azure CLI-vel az alábbi használatával. További információ: [felügyelt identitás használata számítási fürtökkel](how-to-create-attach-compute-cluster.md#managed-identity).

# <a name="python"></a>[Python](#tab/python)

Amikor számítási fürtöt hoz létre a [AmlComputeProvisioningConfiguration](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcomputeprovisioningconfiguration), használja a `identity_type` paramétert a felügyelt identitás típusának megadásához.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interaction
az ml computetarget create amlcompute --name cpucluster -w <workspace> -g <resource group> --vm-size <vm sku> --assign-identity '[system]'
```

# <a name="portal"></a>[Portál](#tab/azure-portal)

A felügyelt identitásnak a Studióban történő létrehozásakor történő konfigurálásával kapcsolatos információkért lásd: [felügyelt identitás beállítása](how-to-create-attach-compute-studio.md#managed-identity).

---

A felügyelt identitás automatikusan megkapja a ACRPull szerepkört a munkaterület ACR-ben, így lehetővé téve a Docker-rendszerképek betanítását.

> [!NOTE]
> Ha először hozza létre a számítást, a munkaterület ACR létrehozása előtt manuálisan kell hozzárendelnie a ACRPull szerepkört.

## <a name="access-base-images-from-private-acr"></a>Alapképek elérése a privát ACR-ből

Alapértelmezés szerint a Azure Machine Learning a Microsoft által felügyelt nyilvános tárházból származó Docker-alapú rendszerképeket használ. Ezután létrehozza a betanítást és a következtetési környezetet a képeken. További információ: [Mi az a ml-környezet?](concept-environments.md).

Ha a vállalaton belül egyéni belső rendszerképet szeretne használni, akkor a felügyelt identitások segítségével hozzáférhet a saját ACR-hez. Két felhasználási eset létezik:

 * Az alapszintű rendszerképet használja a betanításhoz.
 * Hozzon létre Azure Machine learning felügyelt rendszerképet egyéni rendszerképpel alapszintű.

### <a name="pull-docker-base-image-to-machine-learning-compute-cluster-for-training-as-is"></a>A Docker alaprendszerképének lekérése a Machine learning számítási fürtre a betanításhoz

Hozzon létre gépi tanulási számítási fürtöt a korábban leírtaknak megfelelően beállított, rendszerhez rendelt felügyelt identitással. Ezután határozza meg a felügyelt identitás résztvevő-AZONOSÍTÓját.

```azurecli-interactive
az ml computetarget amlcompute identity show --name <cluster name> -w <workspace> -g <resource group>
```

A számítási fürtöt a felhasználó által hozzárendelt felügyelt identitás hozzárendeléséhez is frissítheti:

```azurecli-interactive
az ml computetarget amlcompute identity assign --name cpucluster \
-w $mlws -g $mlrg --identities <my-identity-id>
```

Annak engedélyezéséhez, hogy a számítási fürt lekérje az alapképeket, adja meg a felügyelt szolgáltatás identitásának ACRPull szerepkörét a privát ACR-ben.

```azurecli-interactive
az role assignment create --assignee <principal ID> \
--role acrpull \
--scope "/subscriptions/<subscription ID>/resourceGroups/<private ACR resource group>/providers/Microsoft.ContainerRegistry/registries/<private ACR name>"
```

Végül a betanítási futtatáskor adja meg az alaprendszerkép helyét a [környezeti definícióban](how-to-use-environments.md#use-existing-environments).

```python
from azureml.core import Environment
env = Environment(name="private-acr")
env.docker.base_image = "<ACR name>.azurecr.io/<base image repository>/<base image version>"
env.python.user_managed_dependencies = True
```

> [!IMPORTANT]
> Annak érdekében, hogy az alaprendszerkép közvetlenül a számítási erőforráshoz legyen kihúzva, állítsa be, `user_managed_dependencies = True` és ne adja meg a Docker. Ellenkező esetben Azure Machine Learning a szolgáltatás megpróbál létrehozni egy új Docker-rendszerképet, és sikertelen lesz, mert csak a számítási fürt fér hozzá az alaprendszerkép az ACR-ből való lekéréséhez.

### <a name="build-azure-machine-learning-managed-environment-into-base-image-from-private-acr-for-training-or-inference"></a>Azure Machine Learning felügyelt környezet kiépítése a Private ACR-ből képzésre vagy következtetésre épülő alaprendszerképbe

Ebben az esetben a Azure Machine Learning szolgáltatás a privát ACR-ből származó alaprendszerképre épülő képzést vagy következtetési környezetet épít. Mivel a rendszerkép-létrehozási feladat az ACR-feladatok használatával történik a munkaterület ACR-ben, további lépéseket kell végrehajtania a hozzáférés engedélyezéséhez.

1. Hozzon létre __felhasználó által hozzárendelt felügyelt identitást__ , és adja meg az identitás ACRPull hozzáférését a __privát ACR__-hez.  
1. Az előző lépésben a __felhasználó által hozzárendelt felügyelt identitásban__ adjon meg egy felügyelt identitás-kezelő szerepkört a munkaterület __rendszerhez rendelt felügyelt__ identitásnak. Ez a szerepkör lehetővé teszi, hogy a munkaterület hozzárendelje a felhasználóhoz rendelt felügyelt identitást az ACR-feladathoz a felügyelt környezet létrehozásához. 

    1. A munkaterület-rendszerhez rendelt felügyelt identitás rendszerbiztonsági AZONOSÍTÓjának beszerzése:

        ```azurecli-interactive
        az ml workspace show -w <workspace name> -g <resource group> --query identityPrincipalId
        ```

    1. Adja meg a felügyelt identitás-kezelő szerepkört:

        ```azurecli-interactive
        az role assignment create --assignee <principal ID> --role managedidentityoperator --scope <UAI resource ID>
        ```

        A UAI erőforrás-azonosító a felhasználó által hozzárendelt identitás Azure-erőforrás-azonosítója a formátumban `/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<UAI name>` .

1. [Workspace.set_connection metódus](/python/api/azureml-core/azureml.core.workspace.workspace#set-connection-name--category--target--authtype--value-)használatával határozza meg a __felhasználó által hozzárendelt felügyelt identitás__ külső ACR-és ügyfél-azonosítóját a munkaterület-kapcsolatokban:

    ```python
    workspace.set_connection(
        name="privateAcr", 
        category="ACR", 
        target = "<acr url>", 
        authType = "RegistryConnection", 
        value={"ResourceId": "<UAI resource id>", "ClientId": "<UAI client ID>"})
    ```

Ha a konfigurálás befejeződött, használhatja a privát ACR alaplemezképeit a környezetek képzéshez vagy következtetésekhez való kiépítéséhez. A következő kódrészlet azt mutatja be, hogyan lehet megadni a rendszerképeket az ACR-ben és a rendszerkép nevét egy környezeti definícióban:

```python
from azureml.core import Environment

env = Environment(name="my-env")
env.docker.base_image = "<acr url>/my-repo/my-image:latest"
```

Megadhatja a felügyelt identitás erőforrásának URL-címét és az ügyfél-azonosítót a környezeti definícióban a [RegistryIdentity](/python/api/azureml-core/azureml.core.container_registry.registryidentity)használatával. Ha explicit módon használja a beállításjegyzék-identitást, a felülbírálja a korábban megadott munkaterület-kapcsolatokat:

```python
from azureml.core.container_registry import RegistryIdentity

identity = RegistryIdentity()
identity.resource_id= "<UAI resource ID>"
identity.client_id="<UAI client ID>”
env.docker.base_image_registry.registry_identity=identity
env.docker.base_image = "my-acr.azurecr.io/my-repo/my-image:latest"
```

## <a name="use-docker-images-for-inference"></a>Docker-rendszerképek használata a következtetéshez

Miután konfigurálta az ACR-t a korábban leírtaknak megfelelően a rendszergazda felhasználó nélkül, az Azure Kubernetes szolgáltatásban (ak) rendszergazdai kulcsok nélkül érheti el a Docker-rendszerképeket. Ha AK-t hoz létre vagy csatol a munkaterülethez, a rendszer automatikusan hozzárendeli a fürt egyszerű ACRPull hozzáférését a munkaterület ACR-hez.

> [!NOTE]
> Ha saját AK-fürtöt használ, a fürtnek a felügyelt identitás helyett engedélyeznie kell az egyszerű szolgáltatásnevet.

## <a name="create-workspace-with-user-assigned-managed-identity"></a>Munkaterület létrehozása felhasználó által hozzárendelt felügyelt identitással

Munkaterület létrehozásakor megadhat egy felhasználó által hozzárendelt felügyelt identitást, amelyet a rendszer a társított erőforrások eléréséhez fog használni: ACR, kulcstartó, tárolás és alkalmazás-információk.

Először [létre kell hoznia egy felhasználó által hozzárendelt felügyelt identitást](/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli), és jegyezze fel a felügyelt identitás ARM erőforrás-azonosítóját.

Ezután az Azure CLI vagy a Python SDK használatával hozza létre a munkaterületet. A parancssori felület használatakor a paraméter használatával határozza meg az azonosítót `--primary-user-assigned-identity` . Az SDK használatakor használja a t `primary_user_assigned_identity` . Az alábbi példák az Azure CLI és a Python használatával hoznak létre egy új munkaterületet a következő paraméterek használatával:

__Azure CLI__

```azurecli-interactive
az ml workspace create -w <workspace name> -g <resource group> --primary-user-assigned-identity <managed identity ARM ID>
```

__Python__

```python
from azureml.core import Workspace

ws = Workspace.create(name="workspace name", 
    subscription_id="subscription id", 
    resource_group="resource group name",
    primary_user_assigned_identity="managed identity ARM ID")
```

[ARM-sablonnal](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-advanced) is létrehozhat egy olyan munkaterületet, amely felhasználó által hozzárendelt felügyelt identitással rendelkezik.

> [!IMPORTANT]
> Ha saját társított erőforrásokat hoz létre, ahelyett, hogy Azure Machine Learning a szolgáltatást, meg kell adnia a felügyelt identitási szerepköröket ezekre az erőforrásokra. A hozzárendeléseket a [szerepkör-hozzárendelési ARM-sablonnal](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-dependencies-role-assignment) végezheti el.

Ha egy munkaterülethez (az ügyfél által felügyelt kulcsokkal a titkosításhoz) [ https://docs.microsoft.com/azure/machine-learning/concept-data-encryption ], a felhasználó által hozzárendelt felügyelt identitást átadhatja a tárolóból a Key Vaultba való hitelesítéshez. A felügyelt identitáshoz használja a __felhasználó által hozzárendelt-Identity-for-CMK-encryption__ (CLI) vagy __user_assigned_identity_for_cmk_encryption__ (SDK) argumentumot. Ez a felügyelt identitás lehet azonos vagy más, mint a munkaterület elsődleges felhasználó által hozzárendelt felügyelt identitása.

Ha rendelkezik meglévő munkaterülettel, a felhasználó által hozzárendelt felügyelt identitáshoz a ```az ml workspace update``` CLI-parancs vagy a PYTHON SDK-módszer használatával frissítheti a rendszerből ```Workspace.update``` .


## <a name="next-steps"></a>Következő lépések

* További információ a [Azure Machine learning vállalati biztonságáról](concept-enterprise-security.md).
