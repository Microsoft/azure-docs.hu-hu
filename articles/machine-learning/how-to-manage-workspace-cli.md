---
title: Munkaterületek létrehozása az Azure CLI-vel
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan hozhat létre új Azure Machine Learning munkaterületet az Azure CLI használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: larryfr
author: Blackmist
ms.date: 07/28/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 6c2d1b3db422a40f7bcf237c292b48183d99962b
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88121272"
---
# <a name="create-a-workspace-for-azure-machine-learning-with-azure-cli"></a>Munkaterület létrehozása Azure Machine Learninghoz az Azure CLI-vel
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ebből a cikkből megtudhatja, hogyan hozhat létre Azure Machine Learning munkaterületet az Azure CLI használatával. Az Azure CLI parancsokat biztosít az Azure-erőforrások kezeléséhez. A gépi tanulási bővítmény a parancssori felülethez parancsokat biztosít a Azure Machine Learning erőforrásainak használatához.

## <a name="prerequisites"></a>Előfeltételek

* Egy **Azure-előfizetés**. Ha még nem rendelkezik ilyennel, próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree).

* Ha a jelen dokumentumban a CLI-parancsokat a **helyi környezetből**szeretné használni, szüksége lesz az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)-re.

    Ha a [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/)használja, a CLI a böngészőn keresztül érhető el, és a felhőben él.

## <a name="connect-the-cli-to-your-azure-subscription"></a>A CLI összekötése az Azure-előfizetéssel

> [!IMPORTANT]
> Ha a Azure Cloud Shell használja, akkor kihagyhatja ezt a szakaszt. A Cloud Shell automatikusan hitelesíti Önt az Azure-előfizetésében bejelentkezett fiók használatával.

Az Azure-előfizetések több módon is hitelesíthetők a parancssori felületről. A legalapvetőbb az interaktív hitelesítés böngésző használatával. Az interaktív hitelesítéshez nyisson meg egy parancssort vagy terminált, és használja a következő parancsot:

```azurecli-interactive
az login
```

Ha a CLI megnyithatja az alapértelmezett böngészőt, akkor megnyitja, és betölti a bejelentkezési oldalt. Ellenkező esetben meg kell nyitnia egy böngészőt, és követnie kell a parancssor utasításait. Az utasítások [https://aka.ms/devicelogin](https://aka.ms/devicelogin) egy engedélyezési kód böngészését és beírását foglalják magukban.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

A hitelesítés egyéb módszereivel kapcsolatban lásd: [Bejelentkezés az Azure CLI-vel](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

## <a name="install-the-machine-learning-extension"></a>A Machine learning-bővítmény telepítése

A Machine learning-bővítmény telepítéséhez használja a következő parancsot:

```azurecli-interactive
az extension add -n azure-cli-ml
```

## <a name="create-a-workspace"></a>Munkaterület létrehozása

A Azure Machine Learning munkaterület a következő Azure-szolgáltatásokra vagy-entitásokra támaszkodik:

> [!IMPORTANT]
> Ha nem ad meg meglévő Azure-szolgáltatást, a rendszer automatikusan létrehozza az egyiket a munkaterület létrehozása során. Mindig meg kell adnia egy erőforráscsoportot. Saját Storage-fiók csatolásakor győződjön meg arról, hogy az megfelel a következő feltételeknek:
>
> * A Storage-fiók _nem_ prémium szintű fiók (Premium_LRS és Premium_GRS)
> * Mind az Azure Blob, mind az Azure file funkció engedélyezve van
> * A hierarchikus névtér (ADLS Gen 2) le van tiltva
>
> Ezek a követelmények csak a munkaterület által használt _alapértelmezett_ Storage-fiókra vonatkoznak.

| Szolgáltatás | Paraméter egy meglévő példány megadásához |
| ---- | ---- |
| **Azure-erőforráscsoport** | `-g <resource-group-name>`
| **Azure Storage-tárfiók neve** | `--storage-account <service-id>` |
| **Azure Application Insights** | `--application-insights <service-id>` |
| **Azure Key Vault** | `--keyvault <service-id>` |
| **Azure Container Registry** | `--container-registry <service-id>` |

### <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Az Azure Machine Learning munkaterületet egy erőforráscsoport belsejében kell létrehozni. Használhat meglévő erőforráscsoportot, vagy létrehozhat egy újat. __Új erőforráscsoport létrehozásához__használja a következő parancsot. Cserélje le az `<resource-group-name>` -t az erőforráscsoporthoz használni kívánt névre. Cserélje le `<location>` az az Azure-régiót az erőforráscsoport használatára:

> [!TIP]
> Ki kell választania egy régiót, ahol a Azure Machine Learning elérhető. További információ: [régiónként elérhető termékek](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service).

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

A parancs válasza hasonló a következő JSON-fájlhoz:

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

További információ az erőforráscsoportok használatáról: [az Group](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest).

### <a name="automatically-create-required-resources"></a>Szükséges erőforrások automatikus létrehozása

Ha új munkaterületet szeretne létrehozni, amelyben a __szolgáltatások automatikusan létrejönnek__, használja a következő parancsot:

> [!TIP]
> Az ebben a szakaszban található parancsok alapszintű kiadási munkaterületet hoznak létre. Vállalati munkaterület létrehozásához használja a `--sku enterprise` kapcsolót a `az ml workspace create` paranccsal. Azure Machine Learning kiadásokkal kapcsolatos további információkért lásd: [Mi az Azure Machine learning](overview-what-is-azure-ml.md#sku).

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

> [!NOTE]
> A munkaterület neve megkülönbözteti a kis-és nagybetűket.

A parancs kimenete a következő JSON-hoz hasonló:

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

### <a name="virtual-network-and-private-endpoint"></a>Virtuális hálózat és magánhálózati végpont

Ha korlátozni szeretné a munkaterülethez való hozzáférést egy virtuális hálózatra, a következő paramétereket használhatja:

* `--pe-name`: A létrehozott privát végpont neve.
* `--pe-auto-approval`: Azt határozza meg, hogy a rendszer automatikusan jóváhagyja-e a személyes végponti kapcsolatokat a munkaterülethez.
* `--pe-resource-group`: Az az erőforráscsoport, amelyben létre kell hozni a magánhálózati végpontot a alkalmazásban. A virtuális hálózatot tartalmazó csoportnak kell lennie.
* `--pe-vnet-name`: A meglévő virtuális hálózat, amely létrehozza a magánhálózati végpontot a alkalmazásban.
* `--pe-subnet-name`: Annak az alhálózatnak a neve, amelybe a magánhálózati végpontot létre kívánja hozni. Az alapértelmezett érték `default`.

További információ a saját munkaterülettel rendelkező privát végpontok és virtuális hálózatok használatáról: [hálózati elkülönítés és adatvédelem](how-to-enable-virtual-network.md).

### <a name="customer-managed-key-and-high-business-impact-workspace"></a>Ügyfél által felügyelt kulcs és magas üzleti hatás munkaterülete

Alapértelmezés szerint a munkaterülethez tartozó metrikák és metaadatok a Microsoft által fenntartott Azure Cosmos DB-példányban vannak tárolva. Ezeket az adatfájlokat a Microsoft által felügyelt kulcsokkal titkosítjuk. 

Ha Azure Machine Learning __vállalati__ verzióját hozza létre, a saját kulcs megadása lehetőséget használhatja. Ezzel létrehozza az Azure-előfizetésében szereplő mérőszámokat és metaadatokat tároló Azure Cosmos DB-példányt. Használja a `--cmk-keyvault` paramétert a kulcsot tartalmazó Azure Key Vault megadásához, valamint a `--resource-cmk-uri` kulcs URL-címének megadásához a tárolón belül.

> [!IMPORTANT]
> A és a `--cmk-keyvault` paraméterek használata előtt `--resource-cmk-uri` először végre kell hajtania a következő műveleteket:
>
> 1. Engedélyezze az előfizetéshez tartozó közreműködői engedélyekkel rendelkező __Machine learning alkalmazást__ (az identitás-és hozzáférés-kezelésben).
> 1. Kövesse az [ügyfél által felügyelt kulcsok konfigurálása](/azure/cosmos-db/how-to-setup-cmk) a következőhöz című témakör lépéseit:
>     * A Azure Cosmos DB-szolgáltató regisztrálása
>     * Azure Key Vault létrehozása és konfigurálása
>     * Kulcs létrehozása
>
>     Nem kell manuálisan létrehoznia a Azure Cosmos DB példányt, a rendszer létrehoz egyet a munkaterület létrehozása során. Ez a Azure Cosmos DB-példány egy külön erőforráscsoporthoz jön létre a következő minta alapján: `<your-resource-group-name>_<GUID>` .
>
> Ez a beállítás a munkaterület létrehozása után nem módosítható. Ha törli a munkaterület által használt Azure Cosmos DB, törölnie kell az azt használó munkaterületet is.

A Microsoft által a munkaterületen gyűjtött adatok korlátozásához használja a `--hbi-workspace` paramétert. 

> [!IMPORTANT]
> A magas üzleti hatás kiválasztása csak munkaterületek létrehozásakor végezhető el. Ez a beállítás a munkaterület létrehozása után nem módosítható.

Az ügyfél által felügyelt kulcsokkal és a nagy üzleti hatásokkal foglalkozó munkaterülettel kapcsolatos további információkért tekintse meg a [vállalati biztonsági Azure Machine learning](concept-enterprise-security.md#encryption-at-rest).

### <a name="use-existing-resources"></a>Meglévő erőforrások használata

Meglévő erőforrásokat használó munkaterület létrehozásához meg kell adnia az erőforrások AZONOSÍTÓját. A szolgáltatások AZONOSÍTÓjának beszerzéséhez használja a következő parancsokat:

> [!IMPORTANT]
> Nem kell megadnia az összes meglévő erőforrást. Megadhat egyet vagy többet is. Megadhat például egy meglévő Storage-fiókot, és a munkaterület létrehozza a többi erőforrást is.

+ **Azure Storage-fiók**:`az storage account show --name <storage-account-name> --query "id"`

    A parancs válasza az alábbi szöveghez hasonló, és a Storage-fiók azonosítója:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"`

    > [!IMPORTANT]
    > Ha meglévő Azure Storage-fiókot szeretne használni, akkor nem lehet prémium szintű fiók (Premium_LRS és Premium_GRS). Emellett nem lehet hierarchikus névtér (Azure Data Lake Storage Gen2). Sem a Premium Storage, sem a hierarchikus névtér nem támogatott a munkaterület _alapértelmezett_ Storage-fiókjával. A Premium Storage vagy a hierarchikus névtér _nem alapértelmezett Storage-_ fiókokkal használható.

+ **Azure Application Insights**:

    1. Telepítse az Application Insight bővítményt:

        ```azurecli-interactive
        az extension add -n application-insights
        ```

    2. Szerezze be az Application Insight szolgáltatás AZONOSÍTÓját:

        ```azurecli-interactive
        az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"
        ```

        A parancs válasza az alábbi szöveghez hasonló, és az Application Insight szolgáltatás azonosítója:

        `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>"`

+ **Azure Key Vault**:`az keyvault show --name <key-vault-name> --query "ID"`

    A parancs válasza az alábbi szöveghez hasonló, és a Key Vault azonosítója:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"`

+ **Azure Container Registry**:`az acr show --name <acr-name> -g <resource-group-name> --query "id"`

    A parancs válasza az alábbi szöveghez hasonló, és a tároló-beállításjegyzék azonosítója:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"`

    > [!IMPORTANT]
    > A tároló beállításjegyzékének az Azure Machine Learning munkaterülettel való használata előtt engedélyeznie kell a [rendszergazdai fiókot](/azure/container-registry/container-registry-authentication#admin-account) .

Miután megtörtént a munkaterülethez használni kívánt erőforrás (ok) azonosítóinak azonosítója, használja az `az workspace create -w <workspace-name> -g <resource-group-name>` alapparancst, és adja hozzá a meglévő erőforrások paraméter (eke) t és azonosítóját. A következő parancs például egy meglévő tároló-beállításjegyzéket használó munkaterületet hoz létre:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name> --container-registry "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"
```

A parancs kimenete a következő JSON-hoz hasonló:

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

## <a name="list-workspaces"></a>Munkaterületek listázása

Az Azure-előfizetéshez tartozó összes munkaterület listázásához használja az alábbi parancsot:

```azurecli-interactive
az ml workspace list
```

A parancs kimenete a következő JSON-hoz hasonló:

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

További információ: az [ml Workspace List](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-list) dokumentációja.

## <a name="get-workspace-information"></a>Munkaterület adatainak beolvasása

A munkaterületekkel kapcsolatos információk beszerzéséhez használja a következő parancsot:

```azurecli-interactive
az ml workspace show -w <workspace-name> -g <resource-group-name>
```

A parancs kimenete a következő JSON-hoz hasonló:

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

További információ: az [ml Workspace show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-show) dokumentációja.

## <a name="update-a-workspace"></a>Munkaterület frissítése

Munkaterület frissítéséhez használja a következő parancsot:

```azurecli-interactive
az ml workspace update -w <workspace-name> -g <resource-group-name>
```

A parancs kimenete a következő JSON-hoz hasonló:

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

További információt az az [ml Workspace Update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-update) dokumentációjában talál.

## <a name="share-a-workspace-with-another-user"></a>Munkaterület megosztása egy másik felhasználóval

Ha egy munkaterületet egy másik felhasználóval szeretne megosztani az előfizetésében, használja a következő parancsot:

```azurecli-interactive
az ml workspace share -w <workspace-name> -g <resource-group-name> --user <user> --role <role>
```

A szerepköralapú hozzáférés-vezérléssel (RBAC Azure Machine Learning) kapcsolatos további információkért lásd: [felhasználók és szerepkörök kezelése](how-to-assign-roles.md).

További információ: az [ml Workspace Share](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-share) dokumentáció.

## <a name="sync-keys-for-dependent-resources"></a>Függő erőforrások szinkronizálási kulcsai

Ha módosítja a munkaterület által használt egyik erőforrás elérési kulcsait, a munkaterület körülbelül egy órát vesz igénybe, hogy szinkronizálja az új kulcsot. Ha szeretné kényszeríteni a munkaterületet, hogy azonnal szinkronizálja az új kulcsokat, használja a következő parancsot:

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

A kulcsok módosításával kapcsolatos további információkért lásd: [tároló-hozzáférési kulcsok újragenerálása](how-to-change-storage-access-key.md).

További információ: az [ml Workspace Sync-Keys](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-sync-keys) dokumentáció.

## <a name="delete-a-workspace"></a>Munkaterület törlése

Ha már nincs szüksége a munkaterületek törlésére, használja a következő parancsot:

```azurecli-interactive
az ml workspace delete -w <workspace-name> -g <resource-group-name>
```

> [!IMPORTANT]
> A munkaterület törlése nem törli a munkaterület által használt alkalmazás-betekintést, a Storage-fiókot, a Key vaultot vagy a tároló-beállításjegyzéket.

Törölheti az erőforráscsoportot is, amely törli a munkaterületet és az erőforráscsoport összes többi Azure-erőforrását. Az erőforráscsoport törléséhez használja a következő parancsot:

```azurecli-interactive
az group delete -g <resource-group-name>
```

További információ: az [ml Workspace delete](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-delete) dokumentációja.

## <a name="troubleshooting"></a>Hibaelhárítás

### <a name="resource-provider-errors"></a>Erőforrás-szolgáltatói hibák

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>A munkaterület áthelyezése

> [!WARNING]
> Ha áthelyezi a Azure Machine Learning munkaterületet egy másik előfizetésbe, vagy áthelyezi a tulajdonosi előfizetést egy új bérlőre, nem támogatott. Ez hibákhoz vezethet.

### <a name="deleting-the-azure-container-registry"></a>A Azure Container Registry törlése

A Azure Machine Learning munkaterület egyes műveletekhez Azure Container Registry (ACR) használ. Automatikusan létrehoz egy ACR-példányt, amikor először szüksége lesz rá.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>További lépések

A gépi tanuláshoz készült Azure CLI bővítménnyel kapcsolatos további információkért tekintse meg az az [ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest) dokumentációt.
