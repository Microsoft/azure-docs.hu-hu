---
title: A Service Catalog felügyelt alkalmazás közzététele
description: Bemutatja, hogyan hozható létre egy, a szervezete tagjainak szánt Azure-beli felügyelt alkalmazás.
author: tfitzmac
ms.topic: quickstart
ms.custom: subject-armqs, devx-track-azurecli
ms.date: 04/14/2020
ms.author: tomfitz
ms.openlocfilehash: b255cafb9040f87c902fe6c094c3e0db3d461e4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101093651"
---
# <a name="quickstart-create-and-publish-a-managed-application-definition"></a>Rövid útmutató: Felügyelt alkalmazás definíciójának létrehozása és közzététele

Ez a rövid útmutató a [Azure Managed Applications](overview.md)használatának bevezetését ismerteti. Létrehozhat és közzétehet olyan felügyelt alkalmazást, amely a szervezet tagjai számára készült.

A felügyelt alkalmazások a szolgáltatás-katalógusban való közzétételéhez a következőket kell tennie:

* Létre kell hoznia egy sablont, amely meghatározza a felügyelt alkalmazással üzembe helyezendő erőforrásokat.
* Meg kell határoznia a felhasználói felület elemeit a portál számára, amikor üzembe helyezi a felügyelt alkalmazást.
* Hozzon létre egy _. zip_ -csomagot, amely tartalmazza a szükséges sablonfájlokat.
* El kell döntenie, hogy melyik felhasználónak, csoportnak vagy alkalmazásnak kell hozzáférést biztosítani a felhasználó előfizetésében található erőforráscsoporthoz.
* Hozza létre a felügyelt alkalmazás definícióját, amely a _. zip_ csomagra mutat, és hozzáférést kér az identitáshoz.

## <a name="create-the-arm-template"></a>Az ARM-sablon létrehozása

Minden felügyelt alkalmazás definíciója tartalmaz egy _mainTemplate.json_ nevű fájlt. Ebben lehet meghatározni a telepítendő Azure-erőforrásokat. A sablon nem különbözik a szokásos ARM-sablonoktól.

Hozzon létre egy _mainTemplate.json_ nevű fájlt. A név megkülönbözteti a kis- és nagybetűket.

Adja hozzá az alábbi JSON-kódot a fájlhoz. Ez a kód határozza meg a tárfiókok létrehozásához szükséges paramétereket, és megadja a tárfiók tulajdonságait.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountNamePrefix": {
      "type": "string"
    },
    "storageAccountType": {
      "type": "string"
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "variables": {
    "storageAccountName": "[concat(parameters('storageAccountNamePrefix'), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "storageEndpoint": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
    }
  }
}
```

Mentse a _mainTemplate.js_ fájlt.

## <a name="define-your-create-experience"></a>A létrehozási élmény meghatározása

Közzétevőként megadhatja a felügyelt alkalmazás létrehozásához szükséges portált. A fájl _createUiDefinition.js_ a portál felületét hozza létre. Meghatározhatja, hogy a felhasználók hogyan biztosítanak bemenetet az egyes paraméterekhez a [vezérlési elemek](create-uidefinition-elements.md) , például a legördülő listák, a szövegmezők és a jelszó mezők használatával.

Hozzon létre egy _createUiDefinition.js_ nevű fájlt (ez a név megkülönbözteti a kis-és nagybetűket)

Adja hozzá a következő kezdő JSON-fájlt a fájlhoz, és mentse azt.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
  "handler": "Microsoft.Azure.CreateUIDef",
  "version": "0.1.2-preview",
  "parameters": {
    "basics": [
      {}
    ],
    "steps": [
      {
        "name": "storageConfig",
        "label": "Storage settings",
        "subLabel": {
          "preValidation": "Configure the infrastructure settings",
          "postValidation": "Done"
        },
        "bladeTitle": "Storage settings",
        "elements": [
          {
            "name": "storageAccounts",
            "type": "Microsoft.Storage.MultiStorageAccountCombo",
            "label": {
              "prefix": "Storage account name prefix",
              "type": "Storage account type"
            },
            "defaultValue": {
              "type": "Standard_LRS"
            },
            "constraints": {
              "allowedTypes": [
                "Premium_LRS",
                "Standard_LRS",
                "Standard_GRS"
              ]
            }
          }
        ]
      }
    ],
    "outputs": {
      "storageAccountNamePrefix": "[steps('storageConfig').storageAccounts.prefix]",
      "storageAccountType": "[steps('storageConfig').storageAccounts.type]",
      "location": "[location()]"
    }
  }
}
```

További információ: Ismerkedés [a CreateUiDefinition szolgáltatással](create-uidefinition-overview.md).

## <a name="package-the-files"></a>A fájlok becsomagolása

Adja hozzá a két fájlt egy _app.zip_ nevű _. zip_ -fájlhoz. A két fájlnak a _. zip_ fájl legfelső szintjén kell lennie. Ha egy mappába helyezi őket, hibaüzenetet fog kapni a felügyelt alkalmazás definíciójának létrehozásakor, amely szerint nem találhatók a szükséges fájlok.

Töltse fel a csomagot egy elérhető helyre, ahonnan mások használhatják. Meg kell adnia egy egyedi nevet a Storage-fiókhoz.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name storageGroup -Location eastus

$storageAccount = New-AzStorageAccount `
  -ResourceGroupName storageGroup `
  -Name "mystorageaccount" `
  -Location eastus `
  -SkuName Standard_LRS `
  -Kind StorageV2

$ctx = $storageAccount.Context

New-AzStorageContainer -Name appcontainer -Context $ctx -Permission blob

Set-AzStorageBlobContent `
  -File "D:\myapplications\app.zip" `
  -Container appcontainer `
  -Blob "app.zip" `
  -Context $ctx
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name storageGroup --location eastus

az storage account create \
    --name mystorageaccount \
    --resource-group storageGroup \
    --location eastus \
    --sku Standard_LRS \
    --kind StorageV2

az storage container create \
    --account-name mystorageaccount \
    --name appcontainer \
    --public-access blob

az storage blob upload \
    --account-name mystorageaccount \
    --container-name appcontainer \
    --name "app.zip" \
    --file "D:\myapplications\app.zip"

```

---

## <a name="create-the-managed-application-definition"></a>A felügyelt alkalmazás definíciójának létrehozása

### <a name="create-an-azure-active-directory-user-group-or-application"></a>Azure Active Directory felhasználói csoport vagy -alkalmazás létrehozása

A következő lépés egy felhasználói csoport, felhasználó vagy alkalmazás kiválasztása az ügyfél erőforrásainak kezeléséhez. Ez az identitás engedélyeket kap a felügyelt erőforráscsoporthoz a kijelölt szerepkörnek megfelelően. A szerepkör bármely olyan Azure beépített szerepkör lehet, mint a tulajdonos vagy a közreműködő. Új Azure Active Directory felhasználói csoport létrehozásához tekintse meg [az Azure Active Directoryban csoportok létrehozását és tagok hozzáadását ismertető](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) cikket.

Az erőforrások felügyeletéhez szüksége lesz a felhasználói csoport objektumazonosítójára.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$groupID=(Get-AzADGroup -DisplayName mygroup).Id
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
groupid=$(az ad group show --group mygroup --query objectId --output tsv)
```

---

### <a name="get-the-role-definition-id"></a>A szerepkördefiníció-azonosító lekérése

Ezután szüksége lesz az Azure beépített szerepkörének szerepkör-definíciós AZONOSÍTÓJÁRA, amelyhez hozzáférést szeretne adni a felhasználónak, a felhasználói csoportnak vagy az alkalmazásnak. Általában a Tulajdonos, a Közreműködő vagy az Olvasó szerepkört használják. Az alábbi parancs bemutatja, hogyan kérheti le a tulajdonos szerepkör szerepkördefiníció-azonosítóját:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$ownerID=(Get-AzRoleDefinition -Name Owner).Id
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
ownerid=$(az role definition list --name Owner --query [].name --output tsv)
```

---

### <a name="create-the-managed-application-definition"></a>A felügyelt alkalmazás definíciójának létrehozása

Ha még nem rendelkezik erőforráscsoporttal a felügyelt alkalmazás definíciójának tárolásához, hozzon létre egyet:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name appDefinitionGroup -Location westcentralus
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name appDefinitionGroup --location westcentralus
```

---

Most hozza létre a felügyelt alkalmazás definíciójához tartozó erőforrást.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$blob = Get-AzStorageBlob -Container appcontainer -Blob app.zip -Context $ctx

New-AzManagedApplicationDefinition `
  -Name "ManagedStorage" `
  -Location "westcentralus" `
  -ResourceGroupName appDefinitionGroup `
  -LockLevel ReadOnly `
  -DisplayName "Managed Storage Account" `
  -Description "Managed Azure Storage Account" `
  -Authorization "${groupID}:$ownerID" `
  -PackageFileUri $blob.ICloudBlob.StorageUri.PrimaryUri.AbsoluteUri
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
blob=$(az storage blob url --account-name mystorageaccount --container-name appcontainer --name app.zip --output tsv)

az managedapp definition create \
  --name "ManagedStorage" \
  --location "westcentralus" \
  --resource-group appDefinitionGroup \
  --lock-level ReadOnly \
  --display-name "Managed Storage Account" \
  --description "Managed Azure Storage Account" \
  --authorizations "$groupid:$ownerid" \
  --package-file-uri "$blob"
```

---

A parancs befejeződésekor egy felügyeltalkalmazás-definíció található az erőforráscsoportban.

Íme néhány az előző példában használt paraméterek közül:

* **erőforráscsoport**: az erőforráscsoport neve, amelyben a felügyelt alkalmazás definíciója létrejött.
* **zárolási szint**: a felügyelt erőforráscsoporthoz helyezett zárolás típusa. Megakadályozza, hogy az ügyfél nemkívánatos műveleteket hajtson végre ezen az erőforráscsoporton. Jelenleg a ReadOnly az egyetlen támogatott zárolási szint. Amikor a ReadOnly van megadva, az ügyfél csak olvashatja a felügyelt erőforráscsoportban lévő erőforrásokat. A kezelt erőforráscsoporthoz hozzáféréssel rendelkező közzétevői identitások mentesítve vannak a zárolás alól.
* **authorizations**: A felügyelt erőforráscsoporthoz való engedély biztosításához használt résztvevő-azonosítót és szerepkördefiníció-azonosítót ismerteti. `<principalId>:<roleDefinitionId>` formátumban van megadva. Ha egynél több érték szükséges, adja meg azokat a következő formátumban: `<principalId1>:<roleDefinitionId1>,<principalId2>:<roleDefinitionId2>`. Az értékeket vesszővel kell elválasztani.
* **csomagfájl URI-ja**: a szükséges fájlokat tartalmazó _. zip_ -csomag helye.

## <a name="bring-your-own-storage-for-the-managed-application-definition"></a>Saját tárterület használata a felügyelt alkalmazás definíciója számára

Megadhatja, hogy a felügyelt alkalmazás definícióját a létrehozás során megadott Storage-fiókon belül tárolja, hogy a saját helye és hozzáférése teljes mértékben felügyelhető legyen a szabályozási igényei szerint.

> [!NOTE]
> Saját tárterület használata csak a felügyelt alkalmazás definíciójának ARM-sablonnal vagy REST API-példányával támogatott.

### <a name="select-your-storage-account"></a>Válassza ki a tárfiókot

Létre kell [hoznia egy Storage-fiókot](../../storage/common/storage-account-create.md) , amely tartalmazza a felügyelt alkalmazás definícióját a Service Catalog szolgáltatással való használatra.

Másolja a Storage-fiók erőforrás-AZONOSÍTÓját. A definíció telepítésekor később lesz használatban.

### <a name="set-the-role-assignment-for-appliance-resource-provider-in-your-storage-account"></a>A "készülék erőforrás-szolgáltatója" szerepkör-hozzárendelésének beállítása a Storage-fiókban

Mielőtt a felügyelt alkalmazás definíciója üzembe helyezhető a Storage-fiókjában, közreműködői engedélyeket kell adnia a **készülék erőforrás-szolgáltatói** szerepkörének, hogy a definiált fájlokat a Storage-fiók tárolójában lehessen írni.

1. Az [Azure Portalon](https://portal.azure.com) lépjen a tárfiókra.
1. Válassza a **hozzáférés-vezérlés (iam)** lehetőséget a Storage-fiók hozzáférés-vezérlési beállításainak megjelenítéséhez. Válassza ki a **szerepkör-hozzárendelések** lapot a szerepkör-hozzárendelések listájának megtekintéséhez.
1. A **szerepkör-hozzárendelés hozzáadása** ablakban válassza ki a **közreműködő** szerepkört.
1. A **hozzáférés kiosztása** mezőben válassza az **Azure ad-felhasználó,-csoport vagy egyszerű szolgáltatásnév** elemet.
1. A **kiválasztás** területen keresse meg a **készülék erőforrás-szolgáltatói** szerepkörét, és válassza ki.
1. Mentse a szerepkör-hozzárendelést.

### <a name="deploy-the-managed-application-definition-with-an-arm-template"></a>A felügyelt alkalmazás definíciójának üzembe helyezése ARM-sablonnal

A következő ARM-sablon segítségével telepítheti a csomagolt felügyelt alkalmazást új felügyelt alkalmazás-definícióként a Service Catalog szolgáltatásban, amelynek a definíciós fájljait a saját Storage-fiókjában tárolja és tartja karban:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "applicationName": {
      "type": "string",
      "metadata": {
        "description": "Managed Application name"
      }
    },
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "definitionStorageResourceID": {
      "type": "string",
      "metadata": {
        "description": "Storage account resource ID for where you're storing your definition"
      }
    },
    "_artifactsLocation": {
      "type": "string",
      "metadata": {
        "description": "The base URI where artifacts required by this template are located."
      }
    }
  },
  "variables": {
    "lockLevel": "None",
    "description": "Sample Managed application definition",
    "displayName": "Sample Managed application definition",
    "managedApplicationDefinitionName": "[parameters('applicationName')]",
    "packageFileUri": "[parameters('_artifactsLocation')]",
    "defLocation": "[parameters('definitionStorageResourceID')]",
    "managedResourceGroupId": "[concat(subscription().id,'/resourceGroups/', concat(parameters('applicationName'),'_managed'))]",
    "applicationDefinitionResourceId": "[resourceId('Microsoft.Solutions/applicationDefinitions',variables('managedApplicationDefinitionName'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Solutions/applicationDefinitions",
      "apiVersion": "2020-08-21-preview",
      "name": "[variables('managedApplicationDefinitionName')]",
      "location": "[parameters('location')]",
      "properties": {
        "lockLevel": "[variables('lockLevel')]",
        "description": "[variables('description')]",
        "displayName": "[variables('displayName')]",
        "packageFileUri": "[variables('packageFileUri')]",
        "storageAccountId": "[variables('defLocation')]"
      }
    }
  ],
  "outputs": {}
}
```

Hozzáadott egy új `storageAccountId` , a tulajdonságok nevű tulajdonságot `applicationDefinitions` , és adja meg a Storage-fiók azonosítóját, amelyben a definícióját a következő értékként szeretné tárolni:

Megtekintheti, hogy az alkalmazás-definíciós fájlok a megadott Storage-fiókban vannak-e mentve egy, a nevű tárolóban `applicationDefinitions` .

> [!NOTE]
> A további biztonság érdekében létrehozhat egy felügyelt alkalmazások definícióját egy [Azure Storage-fiók blobjában, ahol engedélyezve](../../storage/common/storage-service-encryption.md)van a titkosítás. A definíció tartalmának titkosítása a Storage-fiók titkosítási beállításain keresztül történik. Csak a fájlra vonatkozó engedélyekkel rendelkező felhasználók tekinthetik meg a szolgáltatás-katalógus definícióját.

## <a name="make-sure-users-can-see-your-definition"></a>Győződjön meg arról, hogy a definíció a felhasználók számára látható

Ön hozzáfér a felügyelt alkalmazás definíciójához, azonban gondoskodnia kell róla, hogy a vállalat más felhasználói is hozzáférjenek. Engedélyezzen számukra legalább Olvasó szerepkört a definícióhoz. Előfordulhat, hogy ezt a hozzáférési szintet már megörökölték az előfizetéstől vagy az erőforráscsoporttól. Ha szeretné megnézni, hogy ki férhet hozzá a definícióhoz, és hogyan adhat hozzá felhasználókat vagy csoportokat, tekintse meg [Az Azure-szerepkörök Azure Portal használatával történő hozzárendelését](../../role-based-access-control/role-assignments-portal.md)

## <a name="next-steps"></a>Következő lépések

Sikeresen közzétette a felügyelt alkalmazás definícióját. Most megismerheti, hogyan helyezheti üzembe a definíció egyik példányát.

> [!div class="nextstepaction"]
> [Rövid útmutató: Szolgáltatáskatalógusban elérhető alkalmazás telepítése](deploy-service-catalog-quickstart.md)
