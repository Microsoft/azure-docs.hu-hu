---
title: Az Azure Blob Storage elérése az Azure Databricks és az Azure Key Vault használatával
description: Ebből az oktatóanyagból megtudhatja, hogyan érheti el az Azure Blob Storaget a Azure Databricksban tárolt titkos kód használatával Azure Key Vault
author: msmbaldwin
ms.author: mbaldwin
ms.service: key-vault
ms.topic: tutorial
ms.date: 06/16/2020
ms.openlocfilehash: fc33b24aac8964d0a8390b4b38c5e1862ed639a8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102198914"
---
# <a name="tutorial-access-azure-blob-storage-using-azure-databricks-and-azure-key-vault"></a>Oktatóanyag: az Azure Blob Storage elérése Azure Databricks és Azure Key Vault használatával

Ebből az oktatóanyagból megtudhatja, hogyan érheti el az Azure Blob Storaget a Azure Databricks a Azure Key Vaultban tárolt titkos kulcs használatával. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Storage-fiók és blob-tároló létrehozása az Azure CLI-vel
> * Key Vault létrehozása és titkos kód beállítása
> * Azure Databricks munkaterület létrehozása és Key Vault titkos hatókör hozzáadása
> * A blob-tároló elérése Azure Databricks munkaterületről

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

Az oktatóanyag elindítása előtt telepítse az [Azure CLI](/cli/azure/install-azure-cli-windows)-t.

## <a name="create-a-storage-account-and-blob-container-with-azure-cli"></a>Storage-fiók és blob-tároló létrehozása az Azure CLI-vel

Először létre kell hoznia egy általános célú Storage-fiókot a Blobok használatához. Ha nem rendelkezik [erőforráscsoporthoz](/cli/azure/group#az-group-create), hozzon létre egyet a parancs futtatása előtt. A következő parancs létrehozza és megjeleníti a Storage-tároló metaadatait. Másolja le az **azonosítót**.

```azurecli
az storage account create --name contosoblobstorage5 --resource-group contosoResourceGroup --location eastus --sku Standard_ZRS --encryption-services blob
```

![A fenti parancs konzoljának kimenete. Az azonosító zöld színnel jelenik meg a végfelhasználók számára.](../media/databricks-command-output-1.png)

Ahhoz, hogy létre lehessen hozni egy tárolót, amelybe fel szeretné tölteni a blobot, hozzá kell rendelnie a [Storage blob-adatközreműködői](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) szerepkört. Ebben a példában a szerepkör a korábban létrehozott Storage-fiókhoz lesz hozzárendelve.

```azurecli
az role assignment create --role "Storage Blob Data Contributor" --assignee t-trtr@microsoft.com --scope "/subscriptions/885e24c8-7a36-4217-b8c9-eed31e110504/resourceGroups/contosoResourceGroup5/providers/Microsoft.Storage/storageAccounts/contosoblobstorage5
```

Most, hogy hozzárendelte a szerepkört a Storage-fiókhoz, létrehozhat egy tárolót a blobhoz.

```azurecli
az storage container create --account-name contosoblobstorage5 --name contosocontainer5 --auth-mode login
```

Miután létrehozta a tárolót, feltöltheti a tárolóba egy blobot (tetszőleges fájlt). Ebben a példában egy. txt kiterjesztésű fájl van feltöltve a HelloWorld.

```azurecli
az storage blob upload --account-name contosoblobstorage5 --container-name contosocontainer5 --name helloworld --file helloworld.txt --auth-mode login
```

A tárolóban lévő Blobok listázásával ellenőrizze, hogy a tároló rendelkezik-e.

```azurecli
az storage blob list --account-name contosoblobstorage5 --container-name contosocontainer5 --output table --auth-mode login
```

![A fenti parancs konzoljának kimenete. Megjeleníti a tárolóban éppen tárolt fájlt.](../media/databricks-command-output-2.png)

Szerezze be a **key1** értékét a következő parancs használatával. Másolja le az értéket.

```azurecli
az storage account keys list -g contosoResourceGroup5 -n contosoblobstorage5
```

![A fenti parancs konzoljának kimenete. A key1 értéke zöld mezőben van kiemelve.](../media/databricks-command-output-3.png)

## <a name="create-a-key-vault-and-set-a-secret"></a>Key Vault létrehozása és titkos kód beállítása

A következő parancs használatával hoz létre Key Vault. Ezzel a paranccsal a Key Vault metaadatai is megjelennek. Másolja le az **azonosítót** és a **vaultUri**.

```azurecli
az keyvault create --name contosoKeyVault10 --resource-group contosoResourceGroup5 --location eastus
```

![](../media/databricks-command-output-4.png)
 ![ A fenti parancs rendszerkép-konzoljának kimenete. Az azonosító és a vaultUri egyaránt zöld színnel jelenik meg a felhasználó számára.](../media/databricks-command-output-5.png)

A titok létrehozásához használja a következő parancsot. Állítsa a titkos kulcs értékét a **key1** értékre a Storage-fiókban.

```azurecli
az keyvault secret set --vault-name contosoKeyVault10 --name storageKey --value "value of your key1"
```

## <a name="create-an-azure-databricks-workspace-and-add-key-vault-secret-scope"></a>Azure Databricks munkaterület létrehozása és Key Vault titkos hatókör hozzáadása

Ez a szakasz nem hajtható végre a parancssoron keresztül. Kövesse ezt az [útmutatót](/azure/databricks/scenarios/store-secrets-azure-key-vault#create-an-azure-databricks-workspace-and-add-a-secret-scope). A következőhöz kell hozzáférnie a [Azure Portal](https://ms.portal.azure.com/#home) :

1. A Azure Databricks-erőforrás létrehozása
1. A munkaterület elindítása
1. Key Vault által támogatott titkos kód hatókörének létrehozása

## <a name="access-your-blob-container-from-azure-databricks-workspace"></a>A blob-tároló elérése Azure Databricks munkaterületről

Ez a szakasz nem hajtható végre a parancssoron keresztül. Kövesse ezt az [útmutatót](/azure/databricks/scenarios/store-secrets-azure-key-vault#access-your-blob-container-from-azure-databricks). A következőre kell használnia a Azure Databricks munkaterületet:

1. **Új fürt** létrehozása
1. **Új jegyzetfüzet** létrehozása
1. Töltse ki a megfelelő mezőket a Python-szkriptben
1. A Python-szkript futtatása

```python
dbutils.fs.mount(
source = "wasbs://<your-container-name>@<your-storage-account-name>.blob.core.windows.net",
mount_point = "/mnt/<mount-name>",
extra_configs = {"<conf-key>":dbutils.secrets.get(scope = "<scope-name>", key = "<key-name>")})

df = spark.read.text("/mnt/<mount-name>/<file-name>")

df.show()
```

## <a name="next-steps"></a>Következő lépések

Győződjön meg arról, hogy a Key Vault helyreállítható:
> [!div class="nextstepaction"]
> [Az erőforrások törlése](../../azure-resource-manager/management/delete-resource-group.md?tabs=azure-powershell)