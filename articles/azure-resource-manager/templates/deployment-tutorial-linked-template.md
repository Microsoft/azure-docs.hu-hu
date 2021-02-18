---
title: Oktatóanyag – csatolt sablon üzembe helyezése
description: Útmutató csatolt sablon üzembe helyezéséhez
ms.date: 02/12/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 8f2bbd327adca6eef62d5e79f422f61d460ea7a5
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100589269"
---
# <a name="tutorial-deploy-a-linked-template"></a>Oktatóanyag: csatolt sablon üzembe helyezése

Az [előző oktatóanyagokban](./deployment-tutorial-local-template.md)megtanulta, hogyan helyezhet üzembe egy helyi számítógépen tárolt sablont. Összetett megoldások üzembe helyezéséhez megszakíthat egy sablont számos sablonba, és egy fő sablon segítségével telepítheti ezeket a sablonokat. Ebből az oktatóanyagból megtudhatja, hogyan helyezhet üzembe egy olyan fő sablont, amely egy csatolt sablonra mutató hivatkozást tartalmaz. A fősablon üzembe helyezés után elindítja a csatolt sablon üzembe helyezését. Azt is megtudhatja, hogyan tárolhat és biztonságossá teheti a sablonokat SAS-token használatával. A művelet végrehajtása körülbelül **12 percet** vesz igénybe.

## <a name="prerequisites"></a>Előfeltételek

Javasoljuk, hogy fejezze be az előző oktatóanyagot, de ez nem kötelező.

## <a name="review-template"></a>Sablon áttekintése

Az előző oktatóanyagokban egy olyan sablont telepít, amely létrehoz egy Storage-fiókot, App Service-csomagot és egy webalkalmazást. A sablon a következőket használta:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

## <a name="create-a-linked-template"></a>Csatolt sablon létrehozása

A Storage-fiók erőforrását egy csatolt sablonba is elkülönítheti:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/linkedStorageAccount.json":::

A fő sablon a következő sablon. A Kiemelt `Microsoft.Resources/deployments` objektum egy csatolt sablon meghívását mutatja be. A csatolt sablon nem tárolható helyi fájlként vagy olyan fájlként, amely csak a helyi hálózaton érhető el. Megadhatja a társított sablon URI-értékét, amely tartalmazza a HTTP vagy a HTTPS protokollt, vagy a _relativePath_ tulajdonság használatával egy távoli csatolt sablont helyez üzembe a fölérendelt sablonhoz viszonyítva. Az egyik lehetőség, hogy a fő sablont és a csatolt sablont is elhelyezi egy Storage-fiókban.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/azuredeploy.json" highlight="34-52":::

## <a name="store-the-linked-template"></a>A csatolt sablon tárolása

Mind a fő sablon, mind a csatolt sablon a GitHubon tárolódik:

A következő PowerShell-szkript létrehoz egy Storage-fiókot, létrehoz egy tárolót, és átmásolja a két sablont egy GitHub-adattárból a tárolóba. Ez a két sablon a következő:

- A fő sablon: https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/azuredeploy.json
- A csatolt sablon: https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json

Válassza a **kipróbálás-ez** lehetőséget a Cloud Shell megnyitásához, válassza a **Másolás** lehetőséget a PowerShell-parancsfájl másolásához, majd kattintson a jobb gombbal a rendszerhéj ablaktáblára a parancsfájl beillesztéséhez:

> [!IMPORTANT]
> A Storage-fiókok nevének 3 – 24 karakter hosszúnak kell lennie, és csak számokat és kisbetűket használjon. A névnek egyedinek kell lennie. A sablonban a Storage-fiók neve a projekt neve, amely a **tároló** hozzáfűzésével van ellátva, a projekt nevének pedig 3 – 11 karakterből kell állnia. Így a projekt nevének meg kell felelnie a Storage-fiók nevének, és kevesebb, mint 11 karakterből áll.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectName + "rg"
$storageAccountName = $projectName + "store"
$containerName = "templates" # The name of the Blob container to be created.

$mainTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/azuredeploy.json"
$linkedTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json"

$mainFileName = "azuredeploy.json" # A file name used for downloading and uploading the main template.Add-PSSnapin
$linkedFileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

# Download the templates
Invoke-WebRequest -Uri $mainTemplateURL -OutFile "$home/$mainFileName"
Invoke-WebRequest -Uri $linkedTemplateURL -OutFile "$home/$linkedFileName"

# Create a resource group
New-AzResourceGroup -Name $resourceGroupName -Location $location

# Create a storage account
$storageAccount = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $location `
    -SkuName "Standard_LRS"

$context = $storageAccount.Context

# Create a container
New-AzStorageContainer -Name $containerName -Context $context -Permission Container

# Upload the templates
Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$mainFileName" `
    -Blob $mainFileName `
    -Context $context

Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$linkedFileName" `
    -Blob $linkedFileName `
    -Context $context

Write-Host "Press [ENTER] to continue ..."
```

## <a name="deploy-template"></a>Sablon üzembe helyezése

Ha egy Storage-fiókban szeretné üzembe helyezni a sablonokat, állítson be egy SAS-tokent, és adja meg a _-querystring_ paraméternek. Állítsa be a lejárati időt, hogy elegendő idő legyen a telepítés befejezésére. A sablonokat tartalmazó Blobok csak a fiók tulajdonosa számára érhetők el. Ha azonban SAS-jogkivonatot hoz létre egy blobhoz, a blob mindenki számára elérhető lesz az adott SAS-jogkivonattal. Ha egy másik felhasználó elfogja az URI-t és az SAS-tokent, akkor a felhasználó hozzáférhet a sablonhoz. Az SAS-token jó módszer a sablonokhoz való hozzáférés korlátozására, de nem tartalmazhat bizalmas adatokat, például jelszavakat közvetlenül a sablonban.

Ha még nem hozta létre az erőforráscsoportot, tekintse meg az [erőforráscsoport létrehozása](./deployment-tutorial-local-template.md#create-resource-group)című témakört.

> [!NOTE]
> Az alábbi Azure CLI-kódban a `date` paraméter a `-d` MacOS-ben Érvénytelen argumentum. Így a macOS-felhasználók számára, hogy a macOS-ben a terminálon 2 órát vegyen fel az aktuális időpontra, használja a parancsot `-v+2H` .

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive

$projectName = Read-Host -Prompt "Enter the same project name:"   # This name is used to generate names for Azure resources, such as storage account name.

$resourceGroupName="${projectName}rg"
$storageAccountName="${projectName}store"
$containerName = "templates"

$key = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName).Value[0]
$context = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $key

$mainTemplateUri = $context.BlobEndPoint + "$containerName/azuredeploy.json"
$sasToken = New-AzStorageContainerSASToken `
    -Context $context `
    -Container $containerName `
    -Permission r `
    -ExpiryTime (Get-Date).AddHours(2.0)
$newSas = $sasToken.substring(1)


New-AzResourceGroupDeployment `
  -Name DeployLinkedTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri $mainTemplateUri `
  -QueryString $newSas `
  -projectName $projectName `
  -verbose

Write-Host "Press [ENTER] to continue ..."
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
echo "Enter a project name that is used to generate resource names:" &&
read projectName &&

resourceGroupName="${projectName}rg" &&
storageAccountName="${projectName}store" &&
containerName="templates" &&

key=$(az storage account keys list -g $resourceGroupName -n $storageAccountName --query [0].value -o tsv) &&

sasToken=$(az storage container generate-sas \
  --account-name $storageAccountName \
  --account-key $key \
  --name $containerName \
  --permissions r \
  --expiry `date -u -d "120 minutes" '+%Y-%m-%dT%H:%MZ'`) &&
sasToken=$(echo $sasToken | sed 's/"//g')&&

blobUri=$(az storage account show -n $storageAccountName -g $resourceGroupName -o tsv --query primaryEndpoints.blob) &&
templateUri="${blobUri}${containerName}/azuredeploy.json" &&

az deployment group create \
  --name DeployLinkedTemplate \
  --resource-group $resourceGroupName \
  --template-uri $templateUri \
  --parameters projectName=$projectName \
  --query-string $sasToken \
  --verbose
```

---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az erőforráscsoport törlésével törölje az üzembe helyezett erőforrásokat.

1. A Azure Portal válassza ki a bal oldali menüből az **erőforráscsoportot** .
2. A **Szűrés név alapján** mezőben adja meg az erőforráscsoport nevét.
3. Válassza ki az erőforráscsoport nevét.
4. Válassza az **erőforráscsoport törlése** lehetőséget a felső menüben.

## <a name="next-steps"></a>Következő lépések

Megtanulta, hogyan helyezhet üzembe egy csatolt sablont. A következő oktatóanyagban megtudhatja, hogyan hozhat létre egy DevOps folyamatot egy sablon üzembe helyezéséhez.

> [!div class="nextstepaction"]
> [Folyamat létrehozása](./deployment-tutorial-pipeline.md)