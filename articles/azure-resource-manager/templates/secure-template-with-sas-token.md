---
title: Sablon biztonságos üzembe helyezése SAS-tokenrel
description: Erőforrások üzembe helyezése az Azure-ban olyan Azure Resource Manager sablonnal, amelyet SAS-jogkivonat véd. A Azure PowerShell és az Azure CLI-t jeleníti meg.
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 8b35e82da8ebca98ec9fe1fb7441612bf61fb142
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88855662"
---
# <a name="deploy-private-arm-template-with-sas-token"></a>Private ARM-sablon üzembe helyezése SAS-tokenrel

Ha a Azure Resource Manager-sablon (ARM-sablon) egy Storage-fiókban található, akkor a sablonhoz való hozzáférést korlátozhatja, hogy ne tegye közzé nyilvánosan. A biztonságos sablont úgy érheti el, hogy létrehoz egy közös hozzáférésű aláírás (SAS) tokent a sablonhoz, és az üzembe helyezés során megadja a tokent. Ez a cikk azt ismerteti, hogyan használható a Azure PowerShell vagy az Azure CLI egy olyan sablon üzembe helyezéséhez, amely SAS-tokent tartalmaz.

> [!IMPORTANT]
> A sablon SAS-jogkivonattal való biztonságossá tétele helyett érdemes lehet a [sablonhoz tartozó specifikációkat](template-specs.md)használni. A sablon specifikációi segítségével megoszthatja a sablonokat a szervezet más felhasználóival, és kezelheti a sablonokat az Azure RBAC keresztül.

## <a name="create-storage-account-with-secured-container"></a>Storage-fiók létrehozása biztonságos tárolóval

A következő parancsfájl egy nyilvános hozzáféréssel rendelkező Storage-fiókot és-tárolót hoz létre.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location "Central US"
New-AzStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name} `
  -Type Standard_LRS `
  -Location "Central US"
Set-AzCurrentStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name}
New-AzStorageContainer `
  -Name templates `
  -Permission Off
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create \
  --name "ExampleGroup" \
  --location "Central US"
az storage account create \
    --resource-group ExampleGroup \
    --location "Central US" \
    --sku Standard_LRS \
    --kind Storage \
    --name {your-unique-name}
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
az storage container create \
    --name templates \
    --public-access Off \
    --connection-string $connection
```

---

## <a name="upload-template-to-storage-account"></a>Sablon feltöltése a Storage-fiókba

Most már készen áll arra, hogy feltöltse a sablont a Storage-fiókba. Adja meg a használni kívánt sablon elérési útját.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzStorageBlobContent `
  -Container templates `
  -File c:\Templates\azuredeploy.json
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az storage blob upload \
    --container-name templates \
    --file azuredeploy.json \
    --name azuredeploy.json \
    --connection-string $connection
```

---

## <a name="provide-sas-token-during-deployment"></a>SAS-token megadása az üzembe helyezés során

Ha privát sablont szeretne üzembe helyezni egy Storage-fiókban, állítson be egy SAS-tokent, és vegye fel azt a sablon URI-kódjába. Állítsa be a lejárati időt, hogy elegendő idő legyen a telepítés befejezésére.

> [!IMPORTANT]
> A sablont tartalmazó blob csak a fiók tulajdonosa számára érhető el. Ha azonban SAS-jogkivonatot hoz létre a blobhoz, a blob mindenki számára elérhető lesz az adott URI-val. Ha egy másik felhasználó elfogja az URI-t, a felhasználó hozzáférhet a sablonhoz. Az SAS-token jó módszer a sablonokhoz való hozzáférés korlátozására, de nem tartalmazhat bizalmas adatokat, például jelszavakat közvetlenül a sablonban.
>

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
# get the URI with the SAS token
$templateuri = New-AzStorageBlobSASToken `
  -Container templates `
  -Blob azuredeploy.json `
  -Permission r `
  -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

# provide URI with SAS token during deployment
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri $templateuri
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az alábbi példa az Cloud Shell bash-környezettel működik. Más környezetek esetében eltérő szintaxisra lehet szükség az SAS-token lejárati idejének létrehozásához.

```azurecli-interactive
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name templates \
    --name azuredeploy.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name azuredeploy.json \
    --output tsv \
    --connection-string $connection)
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

---

A csatolt sablonokkal rendelkező SAS-tokenek használatára vonatkozó példát a [csatolt sablonok használata Azure Resource Manager segítségével](linked-templates.md)című témakörben talál.


## <a name="next-steps"></a>Következő lépések
* A sablonok telepítésének bevezetését lásd: [erőforrások üzembe helyezése ARM-sablonokkal és Azure PowerShell](deploy-powershell.md).
* A sablonban található paraméterek definiálásához lásd: [sablonok készítése](template-syntax.md#parameters).
