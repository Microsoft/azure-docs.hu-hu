---
title: SQL BACPAC-fájlok importálása sablonokkal
description: Ismerje meg, hogyan importálhat Azure SQL Database-bővítményeket az SQL BACPAC-fájlok Azure Resource Manager-sablonokkal (ARM-sablonok) történő importálásához.
author: mumian
ms.date: 12/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 1bd9f7408baf40791c31626ea9e87a73c65b999c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97963997"
---
# <a name="tutorial-import-sql-bacpac-files-with-arm-templates"></a>Oktatóanyag: SQL BACPAC-fájlok importálása ARM-sablonokkal

Megtudhatja, hogyan importálhat Azure SQL Database-bővítményeket egy [BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) -fájl importálásához Azure Resource Manager-sablonokkal (ARM-sablonok). Az üzembe helyezési összetevők a központi telepítés befejezéséhez szükséges fő sablonfájlok mellett bármilyen fájl is. A BACPAC-fájl egy összetevő.

Ebben az oktatóanyagban létrehoz egy sablont a [logikai SQL-kiszolgáló](../../azure-sql/database/logical-servers.md) és egy önálló adatbázis üzembe helyezéséhez, valamint egy BACPAC-fájl importálásához. További információ az Azure-beli virtuálisgép-bővítmények ARM-sablonok használatával történő üzembe helyezéséről [: oktatóanyag: virtuálisgép-bővítmények üzembe helyezése ARM-sablonokkal](./template-tutorial-deploy-vm-extensions.md).

Ez az oktatóanyag a következő feladatokat mutatja be:

> [!div class="checklist"]
>
> * Készítse elő a BACPAC-fájlt.
> * Nyisson meg egy gyors üzembe helyezési sablont.
> * Szerkessze a sablont.
> * A sablon üzembe helyezése.
> * Ellenőrizze az üzemelő példányt.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez az alábbiakra van szükség:

* Visual Studio Code a Resource Manager Tools bővítménnyel. Lásd [a gyors útmutató: ARM-sablonok létrehozása a Visual Studio Code](./quickstart-create-templates-use-visual-studio-code.md)-ban című témakört.
* A biztonság növeléséhez használjon generált jelszót a kiszolgálói rendszergazdai fiókhoz. Az alábbi példa segítségével hozhatja meg a jelszót:

    ```console
    openssl rand -base64 32
    ```

    Az Azure Key Vault funkciója a titkosítási kulcsok és egyéb titkos kulcsok biztonságos megőrzése. További információ: [oktatóanyag: Azure Key Vault integrálása az ARM-sablonok üzembe helyezésével](./template-tutorial-use-key-vault.md). Javasoljuk továbbá, hogy a jelszót három havonta frissítse.

## <a name="prepare-a-bacpac-file"></a>A BACPAC-fájl előkészítése

Egy BACPAC-fájl meg van osztva a [githubon](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac). Saját létrehozásához lásd: [adatbázis exportálása Azure SQL Databaseból egy BACPAC-fájlba](../../azure-sql/database/database-export.md). Ha egy saját helyen kívánja közzétenni a fájlt, frissítenie kell a sablont az oktatóanyag egy későbbi részében.

A BACPAC-fájlt egy ARM-sablon használatával kell tárolni egy Azure Storage-fiókban. A következő PowerShell-szkript előkészíti a BACPAC-fájlt az alábbi lépésekkel:

* Töltse le a BACPAC fájlt.
* Azure Storage-fiók létrehozása.
* Hozzon létre egy Storage-fiók blob-tárolóját.
* Töltse fel a BACPAC-fájlt a tárolóba.
* Jelenítse meg a Storage-fiók kulcsát és a blob URL-címét.

1. Válassza a **kipróbálás** lehetőséget a Azure Cloud Shell megnyitásához. Ezután illessze be a következő PowerShell-szkriptet a rendszerhéj ablakába.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

    $resourceGroupName = "${projectName}rg"
    $storageAccountName = "${projectName}store"
    $containerName = "bacpacfiles"
    $bacpacFileName = "SQLDatabaseExtension.bacpac"
    $bacpacUrl = "https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-sql-extension/SQLDatabaseExtension.bacpac"

    # Download the bacpac file
    Invoke-WebRequest -Uri $bacpacUrl -OutFile "$HOME/$bacpacFileName"

    # Create a resource group
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account
    $storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
                                           -Name $storageAccountName `
                                           -SkuName Standard_LRS `
                                           -Location $location
    $storageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName `
                                                  -Name $storageAccountName).Value[0]

    # Create a container
    New-AzStorageContainer -Name $containerName -Context $storageAccount.Context

    # Upload the BACPAC file to the container
    Set-AzStorageBlobContent -File $HOME/$bacpacFileName `
                             -Container $containerName `
                             -Blob $bacpacFileName `
                             -Context $storageAccount.Context

    Write-Host "The storage account key is $storageAccountKey"
    Write-Host "The BACPAC file URL is https://$storageAccountName.blob.core.windows.net/$containerName/$bacpacFileName"
    Write-Host "Press [ENTER] to continue ..."
    ```

1. Jegyezze fel a Storage-fiók kulcsát és a BACPAC fájl URL-címét. A sablon telepítésekor ezeket az értékeket kell megadnia.

## <a name="open-a-quickstart-template"></a>Gyorsindítási sablon megnyitása

Az oktatóanyagban használt sablont a [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json)tárolja.

1. A Visual Studio Code-ból válassza a **fájl**  >  **megnyitott** fájl elemet.
1. A **File name** (Fájlnév) mezőbe illessze be a következő URL-címet:

    ```url
    https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-sql-extension/azuredeploy.json
    ```

1. Az **Open** (Megnyitás) kiválasztásával nyissa meg a fájlt.

    A sablonban két erőforrás van definiálva:

   * `Microsoft.Sql/servers`. Tekintse meg a [sablonreferenciát](/azure/templates/microsoft.sql/servers).
   * `Microsoft.SQL.servers/databases`. Tekintse meg a [sablonreferenciát](/azure/templates/microsoft.sql/servers/databases).

        A Testreszabás előtt hasznos lehet a sablon alapvető megismerése.
1. Válassza a **fájl**  >  **Mentés másként** lehetőséget a fájl másolatának mentéséhez a helyi számítógépre *azuredeploy.js* a következő néven:.

## <a name="edit-the-template"></a>A sablon szerkesztése

1. Vegyen fel két további paramétert a szakasz végén a `parameters` Storage-fiók kulcsának és a BACPAC URL-címének megadásához.

    ```json
        "storageAccountKey": {
          "type":"string",
          "metadata":{
            "description": "Specifies the key of the storage account where the BACPAC file is stored."
          }
        },
        "bacpacUrl": {
          "type":"string",
          "metadata":{
            "description": "Specifies the URL of the BACPAC file."
          }
        }
    ```

    Adja hozzá a vesszőt a `adminPassword` tulajdonság záró kapcsos zárójel ( `}` ) után. A JSON-fájl Visual Studio Code-ból való formázásához válassza a Shift + Alt + F billentyűkombinációt.

    A két érték beszerzéséhez tekintse meg [a BACPAC-fájl előkészítését](#prepare-a-bacpac-file)ismertető témakört.

1. Adjon hozzá két további erőforrást a sablonhoz.

    * Annak engedélyezéséhez, hogy a SQL Database bővítmény BACPAC-fájlokat importáljon, engedélyeznie kell az Azure-szolgáltatásokból érkező forgalmat. Adja hozzá a következő tűzfalszabály-definíciót a kiszolgáló definíciója alatt:

        ```json
        "resources": [
          {
            "type": "firewallrules",
            "apiVersion": "2015-05-01-preview",
            "name": "AllowAllAzureIps",
            "location": "[parameters('location')]",
            "dependsOn": [
              "[parameters('databaseServerName')]"
            ],
            "properties": {
              "startIpAddress": "0.0.0.0",
              "endIpAddress": "0.0.0.0"
            }
          }
        ]
        ```

        A sablon a következőképpen néz ki:

        ![Sablon tűzfalszabály-definícióval](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-firewall.png)

    * Adjon hozzá egy SQL Database-bővítmény erőforrást az adatbázis definíciójához az alábbi JSON-nal:

        ```json
        "resources": [
          {
            "type": "extensions",
            "apiVersion": "2014-04-01",
            "name": "Import",
            "dependsOn": [
              "[resourceId('Microsoft.Sql/servers/databases', parameters('databaseServerName'), parameters('databaseName'))]"
            ],
            "properties": {
              "storageKeyType": "StorageAccessKey",
              "storageKey": "[parameters('storageAccountKey')]",
              "storageUri": "[parameters('bacpacUrl')]",
              "administratorLogin": "[parameters('adminUser')]",
              "administratorLoginPassword": "[parameters('adminPassword')]",
              "operationMode": "Import"
            }
          }
        ]
        ```

        A sablon a következőképpen néz ki:

        ![Sablon SQL Database bővítménnyel](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac.png)

        További információ az erőforrás-definícióról: [SQL Database-bővítmény referenciája](/azure/templates/microsoft.sql/servers/databases/extensions). A következők a fontosabb elemek:

        * `dependsOn`: Az adatbázis létrehozása után létre kell hozni a bővítmény erőforrását.
        * `storageKeyType`: Adja meg a használni kívánt tárolási kulcs típusát. Az értéke `StorageAccessKey` vagy `SharedAccessKey` lehet. Ebben `StorageAccessKey` az oktatóanyagban használható.
        * `storageKey`: Határozza meg a BACPAC-fájlt tároló Storage-fiók kulcsát. Ha a tárolási kulcs típusa `SharedAccessKey` , akkor meg kell előznie a következőt: "?".
        * `storageUri`: Itt adhatja meg a Storage-fiókban tárolt BACPAC-fájl URL-címét.
        * `administratorLoginPassword`: Az SQL-rendszergazda jelszava. Használjon generált jelszót. Lásd: [Előfeltételek](#prerequisites).

A befejezett sablon ehhez hasonlóan néz ki:

[!code-json[](~/resourcemanager-templates/tutorial-sql-extension/azuredeploy2.json?range=1-106&highlight=38-49,62-76,86-103)]

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Az üzembe helyezés folyamatát a [Sablon üzembe helyezése](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template) szakaszban tekintheti meg. Ehelyett használja a következő PowerShell üzembehelyezési szkriptet:

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used earlier"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUsername = Read-Host -Prompt "Enter the SQL admin username"
$adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
$storageAccountKey = Read-Host -Prompt "Enter the storage account key"
$bacpacUrl = Read-Host -Prompt "Enter the URL of the BACPAC file"
$resourceGroupName = "${projectName}rg"

#New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -adminUser $adminUsername `
    -adminPassword $adminPassword `
    -TemplateFile "$HOME/azuredeploy.json" `
    -storageAccountKey $storageAccountKey `
    -bacpacUrl $bacpacUrl

Write-Host "Press [ENTER] to continue ..."
```

Érdemes lehet ugyanazt a nevet használni, amelyet a BACPAC-fájl előkészítése során használt, hogy az összes erőforrás ugyanabban az erőforráscsoporthoz legyen tárolva. Így könnyebben kezelhetők az erőforrás-feladatok, például az erőforrások tisztítása. Ha ugyanazt a projekt-nevet használja, eltávolíthatja a parancsot a `New-AzResourceGroup` parancsfájlból, vagy választhatja az igen (y) vagy a nem (n) értéket, ha a rendszer megkérdezi, hogy szeretné-e frissíteni a meglévő erőforráscsoportot.

Használjon generált jelszót. Lásd: [Előfeltételek](#prerequisites).

## <a name="verify-the-deployment"></a>Az üzemelő példány ellenőrzése

Ha az ügyfélszámítógépről szeretné elérni a kiszolgálót, hozzá kell adnia egy további tűzfalszabály-szabályt. További információt az [IP-Tűzfalszabályok létrehozásával és kezelésével](../../azure-sql/database/firewall-configure.md#create-and-manage-ip-firewall-rules)foglalkozó témakörben talál.

A Azure Portal válassza ki az adatbázist az újonnan telepített erőforráscsoporthoz. Válassza a **Lekérdezésszerkesztő (előzetes verzió)** elemet, majd adja meg a rendszergazdai hitelesítő adatokat. Ekkor két, az adatbázisba importált tábla jelenik meg.

![Lekérdezés-szerkesztő (előzetes verzió)](./media/template-tutorial-deploy-sql-extensions-bacpac/resource-manager-tutorial-deploy-sql-extensions-bacpac-query-editor.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével.

1. A Azure Portal válassza ki a bal oldali menüből az **erőforráscsoportot** .
1. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
1. Válassza ki az erőforráscsoport nevét. Az erőforráscsoport összesen hat erőforrást fog látni.
1. Válassza az **erőforráscsoport törlése** lehetőséget a felső menüben.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban üzembe helyezett egy kiszolgálót és egy adatbázist, és importált egy BACPAC-fájlt. A sablonok telepítésével kapcsolatos hibák megismeréséhez lásd:

> [!div class="nextstepaction"]
> [ARM-sablonalapú üzembe helyezések hibaelhárítása](./template-tutorial-troubleshoot.md)
