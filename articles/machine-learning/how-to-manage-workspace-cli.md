---
title: Munkaterületek létrehozása az Azure CLI használatával
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhat létre új munkaterületet a gépi tanuláshoz használható Azure CLI Azure Machine Learning használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: larryfr
author: Blackmist
ms.date: 04/02/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-azurecli
ms.openlocfilehash: 8a00f5474fb73677125b85e48fcc2a42f34fdeb0
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876402"
---
# <a name="create-a-workspace-for-azure-machine-learning-with-azure-cli"></a>Munkaterület létrehozása Azure Machine Learning Azure CLI használatával


Ebből a cikkből megtudhatja, hogyan hozhat létre Azure Machine Learning munkaterületet az Azure CLI használatával. Az Azure CLI parancsokat biztosít az Azure-erőforrások kezeléséhez. A parancssori felület machine learning bővítménye parancsokat biztosít a Azure Machine Learning számára.

## <a name="prerequisites"></a>Előfeltételek

* Egy **Azure-előfizetés**. Ha még nincs ilyen verziója, próbálja ki az ingyenes vagy fizetős [Azure Machine Learning.](https://aka.ms/AMLFree)

* Az ebben a dokumentumban használt CLI-parancsoknak a helyi **környezetből** való használathoz az Azure CLI-re [van szükség.](/cli/azure/install-azure-cli)

    Ha a Azure Cloud Shell [használja,](https://azure.microsoft.com//features/cloud-shell/)a CLI a böngészőn keresztül érhető el, és a felhőben van.

## <a name="limitations"></a>Korlátozások

[!INCLUDE [register-namespace](../../includes/machine-learning-register-namespace.md)]

## <a name="connect-the-cli-to-your-azure-subscription"></a>A CLI csatlakoztatása az Azure-előfizetéshez

> [!IMPORTANT]
> Ha a következőt használja Azure Cloud Shell, kihagyhatja ezt a szakaszt. A Cloud Shell automatikusan hitelesíti Az Azure-előfizetésbe bejelentkező fiókkal.

Az Azure-előfizetésben többféleképpen is hitelesítheti magát a CLI-ről. A legegyszerűbb az interaktív hitelesítés böngészővel. Az interaktív hitelesítéshez nyisson meg egy parancssort vagy terminált, és használja a következő parancsot:

```azurecli-interactive
az login
```

Ha a CLI megnyithatja az alapértelmezett böngészőt, akkor megnyitja, és betölti a bejelentkezési oldalt. Ellenkező esetben meg kell nyitnia egy böngészőt, és követnie kell az utasításokat a parancssorban. Az utasítások az engedélyezési kód [https://aka.ms/devicelogin](https://aka.ms/devicelogin) tallózását és beírását foglalják magukban.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

A hitelesítés egyéb módjaiért lásd: [Bejelentkezés az Azure CLI-val.](/cli/azure/authenticate-azure-cli)

## <a name="create-a-workspace"></a>Munkaterület létrehozása

A Azure Machine Learning munkaterület a következő Azure-szolgáltatásokra vagy -entitásokra támaszkodik:

> [!IMPORTANT]
> Ha nem ad meg meglévő Azure-szolgáltatást, a rendszer automatikusan létrehoz egyet a munkaterület létrehozása során. Mindig meg kell adnia egy erőforráscsoportot. Saját tárfiók csatolásakor győződjön meg arról, hogy az megfelel a következő feltételeknek:
>
> * A tárfiók _nem prémium_ szintű fiók (Premium_LRS és Premium_GRS)
> * Az Azure Blob és az Azure File képességei is engedélyezve vannak
> * A hierarchikus névtér (ADLS Gen 2) le van tiltva
>
> Ezek a követelmények csak a munkaterület _által_ használt alapértelmezett tárfiókra vonatkoznak.

| Szolgáltatás | Paraméter meglévő példány megadásához |
| ---- | ---- |
| **Azure-erőforráscsoport** | `-g <resource-group-name>`
| **Azure Storage-fiók** | `--storage-account <service-id>` |
| **Azure Application Insights** | `--application-insights <service-id>` |
| **Azure Key Vault** | `--keyvault <service-id>` |
| **Azure Container Registry** | `--container-registry <service-id>` |

Azure Container Registry (ACR) jelenleg nem támogatja a Unicode-karaktereket az erőforráscsoportok nevében. A probléma megoldásához használjon olyan erőforráscsoportot, amely nem tartalmazza ezeket a karaktereket.

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

A Azure Machine Learning munkaterületet egy erőforráscsoporton belül kell létrehozni. Használhat egy meglévő erőforráscsoportot, vagy hozzon létre egy újat. Új __erőforráscsoport létrehozásához használja__ a következő parancsot. Cserélje `<resource-group-name>` le a helyére az erőforráscsoporthoz használni szükséges nevet. Cserélje `<location>` le a helyére az erőforráscsoporthoz használni szükséges Azure-régiót:

> [!TIP]
> Olyan régiót kell választania, amelyben Azure Machine Learning elérhető. További információ: [Régiónként elérhető termékek.](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

A parancs válasza az alábbi JSON-hoz hasonló:

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

További információ az erőforráscsoportokról: [az group](/cli/azure/group).

### <a name="automatically-create-required-resources"></a>Szükséges erőforrások automatikus létrehozása

Új munkaterület létrehozásához, ahol a szolgáltatások automatikusan __létrejönnek,__ használja a következő parancsot:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

> [!NOTE]
> A munkaterület neve nem érzékeny a kis- és a kis- és a kis- és a nagy- és a kis- és a nagy- és a kis- és nagy között.

A parancs kimenete az alábbi JSON-hoz hasonló:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

### <a name="virtual-network-and-private-endpoint"></a>Virtuális hálózat és privát végpont

> [!IMPORTANT]
> A Azure Machine Learning nem használható privát kapcsolatokkal a Azure Government régióban.

Ha egy virtuális hálózatra szeretné korlátozni a munkaterülethez való hozzáférést, a következő paramétereket használhatja:

* `--pe-name`: A létrehozott privát végpont neve.
* `--pe-auto-approval`: Azt határozza meg, hogy a munkaterület privát végponti kapcsolatait automatikusan jóvá kell-e hagyni.
* `--pe-resource-group`: A privát végpont létrehozásához használt erőforráscsoport. Egy csoportnak kell lennie, amely a virtuális hálózatot tartalmazza.
* `--pe-vnet-name`: A meglévő virtuális hálózat, amely a privát végpontot létrehozza.
* `--pe-subnet-name`: Annak az alhálózatnak a neve, amely a privát végpontot létrehozza. Az alapértelmezett érték `default`.

A privát végpontok és virtuális hálózatok munkaterülettel való használatával kapcsolatos további információkért lásd: Virtuális hálózatok elkülönítése [és adatvédelmi áttekintése.](how-to-network-security-overview.md)

### <a name="customer-managed-key-and-high-business-impact-workspace"></a>Felhasználó által felügyelt kulcs és jelentős üzleti hatással lévő munkaterület

Alapértelmezés szerint a munkaterület metaadatai egy, a Microsoft által Azure Cosmos DB példányban vannak tárolva. Ezek az adatok a Microsoft által felügyelt kulcsokkal vannak titkosítva.

> [!NOTE]
> Azure Cosmos DB nem __használható__ olyan információk tárolására, mint a modell teljesítménye, a kísérletek által naplózott információk vagy a modelltelepítések során naplózott információk. Az elemek monitorozásáról az [](concept-azure-machine-learning-architecture.md) architektúra és fogalmak című cikk Figyelés és naplózás című szakaszában talál további információt.

A Microsoft által felügyelt kulcs használata helyett használhatja a saját kulcsát is. Ezzel létrehozza a Azure Cosmos DB, amely az Azure-előfizetésben tárolja a metaadatokat. A paraméterrel adhatja meg Azure Key Vault kulcsot tartalmazó tárolót, valamint a kulcs URL-címét a `--cmk-keyvault` `--resource-cmk-uri` tárolóban.

A és paraméterek használata előtt el kell `--cmk-keyvault` `--resource-cmk-uri` végeznie a következő műveleteket:

1. Engedélyezze a __Machine Learning alkalmazást__ (az Identitás- és hozzáférés-kezelésben) közreműködői engedélyekkel az előfizetésen.
1. Kövesse az Ügyfél által kezelt [kulcsok konfigurálásacímű lépéseit](../cosmos-db/how-to-setup-cmk.md) a következőkhöz:
    * A Azure Cosmos DB regisztrálása
    * Hozzon létre és konfigurálja a Azure Key Vault
    * Kulcs létrehozása

Nem kell manuálisan létrehoznia a Azure Cosmos DB, a munkaterület létrehozása során létre fog hozni egyet. Ez Azure Cosmos DB példány egy különálló erőforráscsoportban jön létre, a következő mintán alapuló névvel: `<your-resource-group-name>_<GUID>` .

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

A Microsoft által a munkaterületen gyűjtött adatok korlátozására használja a `--hbi-workspace` paramétert. 

> [!IMPORTANT]
> A nagy üzleti hatást csak munkaterület létrehozásakor lehet kiválasztani. Ez a beállítás a munkaterület létrehozása után nem változtatható meg.

Az ügyfél által kezelt kulcsokkal és a jelentős üzleti hatással kapcsolatos munkaterületekkel kapcsolatos további információkért lásd: Vállalati biztonság [Azure Machine Learning.](concept-data-encryption.md#encryption-at-rest)

### <a name="use-existing-resources"></a>Meglévő erőforrások használata

Meglévő erőforrásokat használó munkaterület létrehozásához meg kell adnia az erőforrások azonosítóját. A következő parancsokkal lekérte a szolgáltatások azonosítóját:

> [!IMPORTANT]
> Nem kell megadnia az összes meglévő erőforrást. Megadhat egy vagy több értéket is. Megadhat például egy meglévő tárfiókot, és a munkaterület létrehozza a többi erőforrást.

+ **Azure Storage-fiók:**`az storage account show --name <storage-account-name> --query "id"`

    A parancs válasza a következő szöveghez hasonló, és a tárfiók azonosítója:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"`

    > [!IMPORTANT]
    > Ha egy meglévő Azure Storage-fiókot szeretne használni, az nem lehet prémium szintű fiók (Premium_LRS és Premium_GRS). Emellett nem lehet hierarchikus névtér (amely a Azure Data Lake Storage Gen2). A munkaterület alapértelmezett tárfiókja sem  támogatja a Prémium szintű tárolót vagy a hierarchikus névteret. Használhat prémium szintű tárolót vagy hierarchikus névteret nem _alapértelmezett tárfiókokkal._

+ **Azure Application Insights:**

    1. Telepítse az Application Insights bővítményt:

        ```azurecli-interactive
        az extension add -n application-insights
        ```

    2. Szerezze be az Application Insight szolgáltatás azonosítóját:

        ```azurecli-interactive
        az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"
        ```

        A parancs válasza az alábbi szöveghez hasonló, és az Application Insights szolgáltatás azonosítója:

        `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>"`

+ **Azure Key Vault:**`az keyvault show --name <key-vault-name> --query "ID"`

    A parancs válasza a következő szöveghez hasonló, és a kulcstartó azonosítója:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"`

+ **Azure Container Registry:**`az acr show --name <acr-name> -g <resource-group-name> --query "id"`

    A parancs válasza az alábbi szöveghez hasonló, és a tároló-beállításjegyzék azonosítója:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"`

    > [!IMPORTANT]
    > A tároló-beállításjegyzékben [](../container-registry/container-registry-authentication.md#admin-account) engedélyezni kell a rendszergazdai fiókot, mielőtt használható lenne egy Azure Machine Learning munkaterülettel.

Ha rendelkezik a munkaterülettel használni kívánt erőforrás(k) azonosítójával, használja az alapparancsot, és adja hozzá a meglévő erőforrások `az workspace create -w <workspace-name> -g <resource-group-name>` paraméter(öke)et és azonosítóját. A következő parancs például egy meglévő tároló-beállításjegyzéket használó munkaterületet hoz létre:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name> --container-registry "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"
```

A parancs kimenete az alábbi JSON-hoz hasonló:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

## <a name="list-workspaces"></a>Munkaterületek list mentése

Az Azure-előfizetés összes munkaterületének listához használja a következő parancsot:

```azurecli-interactive
az ml workspace list
```

A parancs kimenete az alábbi JSON-hoz hasonló:

```json
[
  {
    "resourceGroup": "myresourcegroup",
    "subscriptionId": "<subscription-id>",
    "workspaceName": "myml"
  },
  {
    "resourceGroup": "anotherresourcegroup",
    "subscriptionId": "<subscription-id>",
    "workspaceName": "anotherml"
  }
]
```

További információt az [az ml workspace list dokumentációjában](/cli/azure/ml/workspace#az_ml_workspace_list) talál.

## <a name="get-workspace-information"></a>Munkaterület-információk lekérte

A munkaterülettel kapcsolatos információk lekért létrehozásához használja a következő parancsot:

```azurecli-interactive
az ml workspace show -w <workspace-name> -g <resource-group-name>
```

A parancs kimenete az alábbi JSON-hoz hasonló:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/application-insight-name>",
  "creationTime": "2019-08-30T18:55:03.1807976+00:00",
  "description": "",
  "friendlyName": "",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "tags": {},
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

További információt az [az ml workspace show dokumentációjában](/cli/azure/ml/workspace#az_ml_workspace_show) talál.

## <a name="update-a-workspace"></a>Munkaterület frissítése

Munkaterület frissítéséhez használja a következő parancsot:

```azurecli-interactive
az ml workspace update -w <workspace-name> -g <resource-group-name>
```

A parancs kimenete az alábbi JSON-hoz hasonló:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/application-insight-name>",
  "creationTime": "2019-08-30T18:55:03.1807976+00:00",
  "description": "",
  "friendlyName": "",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "tags": {},
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

További információt az [az ml workspace update dokumentációjában](/cli/azure/ml/workspace#az_ml_workspace_update) talál.

## <a name="share-a-workspace-with-another-user"></a>Munkaterület megosztása másik felhasználóval

Ha egy munkaterületet az előfizetés egy másik felhasználójának is meg kell osztania, használja a következő parancsot:

```azurecli-interactive
az ml workspace share -w <workspace-name> -g <resource-group-name> --user <user> --role <role>
```

További információ az Azure szerepköralapú hozzáférés-vezérlésről (Azure RBAC) és a Azure Machine Learning: Felhasználók és szerepkörök [kezelése.](how-to-assign-roles.md)

További információt az [az ml workspace share dokumentációjában](/cli/azure/ml/workspace#az_ml_workspace_share) talál.

## <a name="sync-keys-for-dependent-resources"></a>Függő erőforrások kulcsának szinkronizálása

Ha módosítja a munkaterület által használt egyik erőforrás hozzáférési kulcsait, a munkaterület szinkronizálása az új kulccsal körülbelül egy órát vesz igénybe. A következő paranccsal kényszeríti ki a munkaterületet az új kulcsok azonnali szinkronizálására:

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

A kulcsok módosításával kapcsolatos további információkért lásd: [Tárelérési kulcsok újragenerálása.](how-to-change-storage-access-key.md)

További információt az [az ml workspace sync-keys dokumentációjában](/cli/azure/ml/workspace#az_ml_workspace_sync-keys) talál.

## <a name="delete-a-workspace"></a>Munkaterület törlése

Ha törölni szeretne egy munkaterületet, miután már nincs rá szükség, használja a következő parancsot:

```azurecli-interactive
az ml workspace delete -w <workspace-name> -g <resource-group-name>
```

> [!IMPORTANT]
> A munkaterület törlésével nem törli a munkaterület által használt alkalmazás-elemzéseket, tárfiókot, kulcstartót vagy tároló-beállításjegyzéket.

Törölheti is az erőforráscsoportot, amely törli a munkaterületet és az erőforráscsoportban lévő összes többi Azure-erőforrást. Az erőforráscsoport törléséhez használja a következő parancsot:

```azurecli-interactive
az group delete -g <resource-group-name>
```

További információt az [az ml workspace delete dokumentációjában](/cli/azure/ml/workspace#az_ml_workspace_delete) talál.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="resource-provider-errors"></a>Erőforrás-szolgáltatói hibák

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>A munkaterület áthelyezése

> [!WARNING]
> A munkaterület Azure Machine Learning előfizetésbe vagy a tulajdonában lévő előfizetés új bérlőre való áthelyezése nem támogatott. Ez hibákat okozhat.

### <a name="deleting-the-azure-container-registry"></a>A Azure Container Registry

A Azure Machine Learning munkaterület Azure Container Registry műveletekhez Azure Container Registry ACR-t. Automatikusan létrehoz egy ACR-példányt, amikor először szüksége van rá.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Következő lépések

A gépi tanuláshoz használt Azure CLI-bővítményről az [az ml dokumentációjában talál további](/cli/azure/ml) információt.