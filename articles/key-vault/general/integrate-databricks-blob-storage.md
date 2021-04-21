---
title: Az Azure Blob Storage elérése az Azure Databricks és az Azure Key Vault használatával
description: Ebből az oktatóanyagból megtudhatja, hogyan férhet hozzá a Azure Blob Storage-Azure Databricks tárolt titkos Azure Key Vault
author: msmbaldwin
ms.author: mbaldwin
ms.service: key-vault
ms.topic: tutorial
ms.date: 06/16/2020
ms.openlocfilehash: be1b0da23841b5a63ec044e04a5465e29345f9d1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772198"
---
# <a name="tutorial-access-azure-blob-storage-using-azure-databricks-and-azure-key-vault"></a>Oktatóanyag: Hozzáférés Azure Blob Storage Azure Databricks és Azure Key Vault

Ez az oktatóanyag bemutatja, hogyan férhet hozzá a Azure Blob Storage Azure Databricks tárolt titkos Azure Key Vault. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Tárfiók és blobtároló létrehozása az Azure CLI használatával
> * Hozzon létre egy Key Vault és állítson be egy titkos gombra
> * Hozzon létre egy Azure Databricks munkaterületet, és adja hozzá Key Vault titkos Key Vault hatókört
> * A blobtároló elérése Azure Databricks munkaterületről

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

Mielőtt elkezdené ezt az oktatóanyagot, telepítse az [Azure CLI-t.](/cli/azure/install-azure-cli-windows)

## <a name="create-a-storage-account-and-blob-container-with-azure-cli"></a>Tárfiók és blobtároló létrehozása az Azure CLI használatával

Először létre kell hoznia egy általános célú tárfiókot a blobok használatának érdekében. Ha még nincs erőforráscsoportja, [](/cli/azure/group#az_group_create)hozzon létre egyet a parancs futtatása előtt. A következő parancs létrehozza és megjeleníti a Storage-tároló metaadatait. Másolja le az **azonosítót.**

```azurecli
az storage account create --name contosoblobstorage5 --resource-group contosoResourceGroup --location eastus --sku Standard_ZRS --encryption-services blob
```

![A fenti parancs konzolkimenete. Az azonosító zöld színnel van kiemelve, hogy a végfelhasználó számára látható legyen.](../media/databricks-command-output-1.png)

Mielőtt létrehozhat egy tárolót, amelybe feltölti a blobot, saját magának kell hozzárendelni a [Storage-blobadatok](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) közreműködője szerepkört. Ebben a példában a szerepkör a korábban készített tárfiókhoz lesz hozzárendelve.

```azurecli
az role assignment create --role "Storage Blob Data Contributor" --assignee t-trtr@microsoft.com --scope "/subscriptions/885e24c8-7a36-4217-b8c9-eed31e110504/resourceGroups/contosoResourceGroup5/providers/Microsoft.Storage/storageAccounts/contosoblobstorage5
```

Most, hogy hozzárendeli a szerepkört a tárfiókhoz, létrehozhat egy tárolót a blobhoz.

```azurecli
az storage container create --account-name contosoblobstorage5 --name contosocontainer5 --auth-mode login
```

A tároló létrehozása után feltölthet egy blobot (egy ön által választott fájlt) a tárolóba. Ebben a példában egy .txt fájl lesz feltöltve a helloworld fájllal.

```azurecli
az storage blob upload --account-name contosoblobstorage5 --container-name contosocontainer5 --name helloworld --file helloworld.txt --auth-mode login
```

Sorolja fel a tárolóban lévő blobokat, és ellenőrizze, hogy a tároló rendelkezik-e a tárolóval.

```azurecli
az storage blob list --account-name contosoblobstorage5 --container-name contosocontainer5 --output table --auth-mode login
```

![A fenti parancs konzolkimenete. Megjeleníti a tárolóban tárolt fájlt.](../media/databricks-command-output-2.png)

Az alábbi paranccsal szerezze be a storage-tároló **key1** értékét. Másolja le az értéket.

```azurecli
az storage account keys list -g contosoResourceGroup5 -n contosoblobstorage5
```

![A fenti parancs konzolkimenete. A key1 értéke egy zöld mezőben van kiemelve.](../media/databricks-command-output-3.png)

## <a name="create-a-key-vault-and-set-a-secret"></a>Hozzon létre Key Vault és állítson be egy titkos gombra

Hozzon létre egy Key Vault a következő paranccsal. Ez a parancs megjeleníti a Key Vault metaadatait is. Másolja le az **azonosítót és** **a vaultUri uri-t.**

```azurecli
az keyvault create --name contosoKeyVault10 --resource-group contosoResourceGroup5 --location eastus
```

![A ](../media/databricks-command-output-4.png)
 ![ fenti parancs Image Console-kimenete. Az Azonosító és a vaultUri is zöld színnel van kiemelve, hogy a felhasználó lássa.](../media/databricks-command-output-5.png)

A titkos adatokat a következő paranccsal hozhatja létre. Állítsa a titkos kulcs értékét a **tárfiók key1** értékével.

```azurecli
az keyvault secret set --vault-name contosoKeyVault10 --name storageKey --value "value of your key1"
```

## <a name="create-an-azure-databricks-workspace-and-add-key-vault-secret-scope"></a>Hozzon létre egy Azure Databricks munkaterületet, és adja hozzá Key Vault titkos Key Vault hatókört

Ez a szakasz nem fejezható be a parancssorból. Kövesse ezt az [útmutatót](/azure/databricks/scenarios/store-secrets-azure-key-vault#create-an-azure-databricks-workspace-and-add-a-secret-scope). A következő hozzáféréssel kell [Azure Portal:](https://ms.portal.azure.com/#home)

1. Saját Azure Databricks létrehozása
1. A munkaterület elindítása
1. Titkos Key Vault hatókör létrehozása

## <a name="access-your-blob-container-from-azure-databricks-workspace"></a>A blobtároló elérése Azure Databricks munkaterületről

Ez a szakasz nem fejezható be a parancssorból. Kövesse ezt az [útmutatót](/azure/databricks/scenarios/store-secrets-azure-key-vault#access-your-blob-container-from-azure-databricks). A következőre lesz szüksége a Azure Databricks munkaterületén:

1. Új **fürt létrehozása**
1. Új **jegyzetfüzet létrehozása**
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

Ellenőrizze, hogy a Key Vault helyreállítható-e:
> [!div class="nextstepaction"]
> [Az erőforrások törlése](../../azure-resource-manager/management/delete-resource-group.md?tabs=azure-powershell)