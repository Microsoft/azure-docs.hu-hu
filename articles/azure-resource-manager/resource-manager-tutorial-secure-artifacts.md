---
title: Secure artifacts in templates
description: Learn how to secure the artifacts used in your Azure Resource Manager templates.
author: mumian
ms.date: 10/08/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: b37f7e284b655a362c5a4231a7c1da3719762644
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326443"
---
# <a name="tutorial-secure-artifacts-in-azure-resource-manager-template-deployments"></a>Tutorial: Secure artifacts in Azure Resource Manager template deployments

Learn how to secure the artifacts used in your Azure Resource Manager templates using Azure Storage account with shared access signatures (SAS). Deployment artifacts are any files, in addition to the main template file, that are needed to complete a deployment. For example, in [Tutorial: Import SQL BACPAC files with Azure Resource Manager templates](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md), the main template creates an Azure SQL Database; it also calls a BACPAC file to create tables and insert data. The BACPAC file is an artifact. The artifact is stored in an Azure storage account with public access. In this tutorial, you use SAS to grant limited access to the BACPAC file in your own Azure Storage account. For more information about SAS, see [Using shared access signatures (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

To learn how to secure linked template, see [Tutorial: Create linked Azure Resource Manager templates](./resource-manager-tutorial-create-linked-templates.md).

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
> * A BACPAC-fájl előkészítése
> * Open an existing template
> * A sablon szerkesztése
> * A sablon üzembe helyezése
> * Az üzemelő példány ellenőrzése

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* Visual Studio Code with Resource Manager Tools extension. See [Use Visual Studio Code to create Azure Resource Manager templates](./resource-manager-tools-vs-code.md).
* Review [Tutorial: Import SQL BACPAC files with Azure Resource Manager templates](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md). The template used in this tutorial is the one developed in that tutorial. A download link of the completed template is provided in this article.
* A nagyobb biztonság érdekében használjon automatikusan létrehozott jelszót az SQL Server rendszergazdai fiókjához. Íme egy példa jelszó automatikus létrehozására:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Az Azure Key Vault funkciója a titkosítási kulcsok és egyéb titkos kulcsok biztonságos megőrzése. További információkért lásd [Oktatóanyag: Az Azure Key Vault integrálása a Resource Manager-sablon üzembehelyezési folyamatába](./resource-manager-tutorial-use-key-vault.md). Javasoljuk továbbá, hogy a jelszót három havonta frissítse.

## <a name="prepare-a-bacpac-file"></a>A BACPAC-fájl előkészítése

In this section, you prepare the BACPAC file so the file is accessible securely when you deploy the Resource Manager template. There are five procedures in this section:

* Download the BACPAC file.
* Azure Storage-fiók létrehozása.
* Create a Storage account Blob container.
* Upload the BACPAC file to the container.
* Retrieve the SAS token of the BACPAC file.

To automate these steps using a PowerShell script, see the script from [Upload the linked template](./resource-manager-tutorial-create-linked-templates.md#upload-the-linked-template).

### <a name="download-the-bacpac-file"></a>Download the BACPAC file

Download the [BACPAC file](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac), and save the file to your local computer with the same name, **SQLDatabaseExtension.bacpac**.

### <a name="create-a-storage-account"></a>Create a storage account

1. Select the following image to open a Resource Manager template in the Azure portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-storage-account-create%2fazuredeploy.json" target="_blank"><img src="./media/resource-manager-tutorial-secure-artifacts/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Enter the following properties:

    * **Előfizetés**: Válassza ki az Azure-előfizetést.
    * **Resource Group**: Select **Create new** and give it a name. A resource group is a container for Azure resources for the management purpose. In this tutorial, you can use the same resource group for the storage account and the Azure SQL Database. Make a note of this resource group name, you need it when you create the Azure SQL Database later in the tutorials.
    * **Location**: Select a region. For example, **Central US**.
    * **Storage Account Type**: use the default value, which is **Standard_LRS**.
    * **Location**: Use the default value, which is **[resourceGroup().location]** . That means you use the resource group location for the storage account.
    * **I agree to the terms and conditions started above**: (selected)
3. Válassza a **Beszerzés** lehetőséget.
4. Select the notification icon (the bell icon) on the upper right corner of the portal to see the deployment status.

    ![Resource Manager tutorial portal notifications pane](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-portal-notifications-pane.png)
5. After the storage account is deployed successfully, select **Go to resource group** from the notification pane to open the resource group.

### <a name="create-a-blob-container"></a>Create a Blob container

A Blob container is needed before you can upload any files.

1. A megnyitáshoz válassza ki a tárfiókot. You shall see only one storage account listed in the resource group. Your storage account name is different from the one shown in the following screenshot.

    ![Resource Manager tutorial storage account](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-storage-account.png)

2. Select the **Blobs** tile.

    ![Resource Manager tutorial blobs](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-blobs.png)
3. Select **+ Container** from the top to create a new container.
4. Írja be a következő értékeket:

    * **Name**: enter **sqlbacpac**.
    * **Public access level**: use the default value, **Private (no anonymous access)** .
5. Kattintson az **OK** gombra.
6. Select **sqlbacpac** to open the newly created container.

### <a name="upload-the-bacpac-file-to-the-container"></a>Upload the BACPAC file to the container

1. Válassza a **Feltöltés** lehetőséget.
2. Írja be a következő értékeket:

    * **Files**: Following the instructions to select the BACPAC file you downloaded earlier. The default name is **SQLDatabaseExtension.bacpac**.
    * **Authentication type**: Select **SAS**.  *SAS* is the default value.
3. Válassza a **Feltöltés** lehetőséget.  Once the file is uploaded successfully, the file name shall be listed in the container.

### <a name="a-namegenerate-a-sas-token-generate-a-sas-token"></a><a name="generate-a-sas-token" />Generate a SAS token

1. Right-click **SQLDatabaseExtension.bacpac** from the container, and then select **Generate SAS**.
2. Írja be a következő értékeket:

    * **Permission**: Use the default, **Read**.
    * **Start and expiry date/time**: The default value gives you eight hours to use the SAS token. If you need more time to complete this tutorial, update **Expiry**.
    * **Allowed IP addresses**: Leave this field blank.
    * **Allowed protocols**: use the default value: **HTTPS**.
    * **Signing key**: use the default value: **Key 1**.
3. Select **Generate blob SAS token and URL**.
4. Make a copy of **Blob SAS URL**. In the middle of the URL is the file name **SQLDatabaseExtension.bacpac**.  The file name divides the URL into three parts:

   - **Artifact location**: https://xxxxxxxxxxxxxx.blob.core.windows.net/sqlbacpac/. Make sure the location ends with a "/".
   - **BACPAC file name**: SQLDatabaseExtension.bacpac.
   - **Artifact location SAS token**: Make sure the token precedes with a "?."

     You need these three values in [Deploy the template](#deploy-the-template).

## <a name="open-an-existing-template"></a>Open an existing template

In this session, you modify the template you created in [Tutorial: Import SQL BACPAC files with Azure Resource Manager templates](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md) to call the BACPAC file with a SAS token.  The template developed in the SQL extension tutorial is shared in [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json).

1. A Visual Studio Code-ban válassza a **File** (Fájl) > **Open File** (Fájl megnyitása) elemet.
2. A **File name** (Fájlnév) mezőbe illessze be a következő URL-címet:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json
    ```
3. Az **Open** (Megnyitás) kiválasztásával nyissa meg a fájlt.

    There are five resources defined in the template:

   * `Microsoft.Sql/servers` kérdésre adott válaszban foglalt lépéseket. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers).
   * `Microsoft.SQL/servers/securityAlertPolicies` kérdésre adott válaszban foglalt lépéseket. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/securityalertpolicies).
   * `Microsoft.SQL/servers/filewallRules` kérdésre adott válaszban foglalt lépéseket. Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.sql/2015-05-01-preview/servers/firewallrules).
   * `Microsoft.SQL/servers/databases` kérdésre adott válaszban foglalt lépéseket.  Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.sql/servers/databases).
   * `Microsoft.SQL/server/databases/extensions` kérdésre adott válaszban foglalt lépéseket.  Tekintse meg a [sablonreferenciát](https://docs.microsoft.com/azure/templates/microsoft.sql/2014-04-01/servers/databases/extensions).

     Érdemes megismerkedni a sablon alapvető működésével, mielőtt megkezdi annak testreszabását.
4. A **File** (Fájl) > **Save As** (Mentés másként) kiválasztásával mentheti a fájl egy másolati példányát a helyi számítógépre, **azuredeploy.json** néven.

## <a name="edit-the-template"></a>A sablon szerkesztése

Add the following additional parameters:

```json
"_artifactsLocation": {
    "type": "string",
    "metadata": {
        "description": "The base URI where artifacts required by this template are located."
    }
},
"_artifactsLocationSasToken": {
    "type": "securestring",
    "metadata": {
        "description": "The sasToken required to access _artifactsLocation."
    },
    "defaultValue": ""
},
"bacpacFileName": {
    "type": "string",
    "defaultValue": "SQLDatabaseExtension.bacpac",
    "metadata": {
        "description": "The bacpac for configure the database and tables."
    }
}
```

![Resource Manager tutorial secure artifacts parameters](./media/resource-manager-tutorial-secure-artifacts/resource-manager-tutorial-secure-artifacts-parameters.png)

Update the value of the following two elements:

```json
"storageKey": "[parameters('_artifactsLocationSasToken')]",
"storageUri": "[uri(parameters('_artifactsLocation'), parameters('bacpacFileName'))]",
```

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az üzembe helyezés folyamatát a [Sablon üzembe helyezése](./resource-manager-tutorial-create-multiple-instances.md#deploy-the-template) szakaszban tekintheti meg. Ehelyett használja a következő PowerShell üzembehelyezési szkriptet:

```azurepowershell
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$artifactsLocation = Read-Host -Prompt "Enter the artifacts location"
$artifactsLocationSasToken = Read-Host -Prompt "Enter the artifacts location SAS token" -AsSecureString
$bacpacFileName = Read-Host -Prompt "Enter the BACPAC file name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -_artifactsLocation $artifactsLocation `
    -_artifactsLocationSasToken $artifactsLocationSasToken `
    -bacpacFileName $bacpacFileName `
    -TemplateFile "$HOME/azuredeploy.json"
```

Use a generated password. Lásd: [Előfeltételek](#prerequisites).
For the values of _artifactsLocation, _artifactsLocationSasToken and bacpacFileName, see [Generate a SAS token](#generate-a-sas-token).

## <a name="verify-the-deployment"></a>Az üzemelő példány ellenőrzése

A portálon válassza ki az SQL Database-adatbázist az újonnan üzembe helyezett erőforráscsoportból. Válassza a **Lekérdezésszerkesztő (előzetes verzió)** elemet, majd adja meg a rendszergazdai hitelesítő adatokat. Két, az adatbázisba importált táblázatot fog látni:

![Azure Resource Manager, sql-bővítmények üzembe helyezése, BACPAC](./media/resource-manager-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. Az Azure Portalon válassza az **Erőforráscsoport** lehetőséget a bal oldali menüben.
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.  Összesen hat erőforrásnak kell lennie az erőforráscsoportban.
4. A felső menüben válassza az **Erőforráscsoport törlése** lehetőséget.

## <a name="next-steps"></a>Következő lépések

In this tutorial, you deployed a SQL Server, a SQL Database, and imported a BACPAC file using SAS token. To learn how to create an Azure Pipeline to continuously develop and deploy Resource Manager templates, see

> [!div class="nextstepaction"]
> [Continuous integration with Azure Pipeline](./resource-manager-tutorial-use-azure-pipelines.md)
