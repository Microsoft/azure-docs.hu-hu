---
title: Az Azure-szolgáltatáskatalógusban elérhető felügyelt alkalmazás létrehozása és közzététele | Microsoft Docs
description: Bemutatja, hogyan hozható létre egy, a szervezete tagjainak szánt Azure-beli felügyelt alkalmazás.
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: 1d28a9d330dd001e3fdc05e37f7bbcdd2db7ee6e
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "71224544"
---
# <a name="create-and-publish-a-managed-application-definition"></a>Felügyelt alkalmazás definíciójának létrehozása és közzététele

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Létrehozhat és közzétehet a vállalat tagjainak szánt Azure-beli [felügyelt alkalmazásokat](overview.md). Az informatikai részleg közzétehet például olyan felügyelt alkalmazásokat, amelyek megfelelnek a vállalati szabványoknak. Ezeket a felügyelt alkalmazásokat a szolgáltatáskatalóguson keresztül lehet elérni az Azure Marketplace helyett.

A felügyelt alkalmazás szolgáltatáskatalógusban való közzétételéhez a következőket kell tennie:

* Létre kell hoznia egy sablont, amely meghatározza a felügyelt alkalmazással üzembe helyezendő erőforrásokat.
* Meg kell határoznia a felhasználói felület elemeit a portál számára, amikor üzembe helyezi a felügyelt alkalmazást.
* Létre kell hoznia egy .zip csomagot, amely a szükséges sablonfájlokat tartalmazza.
* El kell döntenie, hogy melyik felhasználónak, csoportnak vagy alkalmazásnak kell hozzáférést biztosítani a felhasználó előfizetésében található erőforráscsoporthoz.
* Létre kell hoznia a felügyelt alkalmazás definícióját, amely a .zip csomagra mutat, és hozzáférést kér az identitás számára.

A jelen cikk esetében a felügyelt alkalmazás csak egy tárfiókot tartalmaz. Az a célja, hogy bemutassa a felügyelt alkalmazások közzétételéhez szükséges lépéseket. Átfogó példákat az [Azure-beli felügyelt alkalmazások mintaprojektjeit](sample-projects.md) ismertető témakörben talál.

A cikkben szereplő PowerShell példák használatához az Azure PowerShell 6.2-es vagy újabb verziója szükséges. Ha szükséges, [frissítse a verziót](/powershell/azure/install-Az-ps).

## <a name="create-the-resource-template"></a>Az erőforrássablon létrehozása

Minden felügyelt alkalmazás definíciója tartalmaz egy **mainTemplate.json** nevű fájlt. Ebben lehet meghatározni a telepítendő Azure-erőforrásokat. A sablon pont olyan, mint egy átlagos Resource Manager-sablon.

Hozzon létre egy **mainTemplate.json** nevű fájlt. A név megkülönbözteti a kis- és nagybetűket.

Adja hozzá az alábbi JSON-kódot a fájlhoz. Ez a kód határozza meg a tárfiókok létrehozásához szükséges paramétereket, és megadja a tárfiók tulajdonságait.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
            "name": "[variables('storageAccountName')]",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('storageAccountType')]"
            },
            "kind": "Storage",
            "properties": {}
        }
    ],
    "outputs": {
        "storageEndpoint": {
            "type": "string",
            "value": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
        }
    }
}
```

Mentse a mainTemplate.json fájlt.

## <a name="defining-your-create-experience-using-createuidefinitionjson"></a>A létrehozási élmény meghatározása a CreateUiDefinition. JSON használatával

Közzétevőként a létrehozási élményt a **createUiDefinition. JSON** fájl használatával határozhatja meg, amely a felügyelt alkalmazásokat létrehozó felhasználók felületét hozza létre. Azt határozza meg, hogy a felhasználók hogyan biztosítanak bemenetet az egyes paraméterekhez [vezérlő elemek] (create-uidefinition-elements.md), beleértve a legördülő listákat, a szövegmezőket és a jelszó mezőket.

Hozzon létre egy **createUiDefinition. JSON** nevű fájlt (ez a név megkülönbözteti a kis-és nagybetűket)

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

Adja hozzá a két fájlt az app.zip nevű .zip fájlhoz. A két fájlnak a .zip fájl gyökérszintjén kell lennie. Ha egy mappába helyezi őket, hibaüzenetet fog kapni a felügyelt alkalmazás definíciójának létrehozásakor, amely szerint nem találhatók a szükséges fájlok. 

Töltse fel a csomagot egy elérhető helyre, ahonnan mások használhatják. 

```powershell
New-AzResourceGroup -Name storageGroup -Location eastus
$storageAccount = New-AzStorageAccount -ResourceGroupName storageGroup `
  -Name "mystorageaccount" `
  -Location eastus `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context

New-AzStorageContainer -Name appcontainer -Context $ctx -Permission blob

Set-AzStorageBlobContent -File "D:\myapplications\app.zip" `
  -Container appcontainer `
  -Blob "app.zip" `
  -Context $ctx 
```

## <a name="create-the-managed-application-definition"></a>A felügyelt alkalmazás definíciójának létrehozása

### <a name="create-an-azure-active-directory-user-group-or-application"></a>Azure Active Directory felhasználói csoport vagy -alkalmazás létrehozása

A következő lépés egy felhasználói csoport vagy alkalmazás kiválasztása, amely az ügyfél nevében felügyeli az erőforrásokat. Ez a felhasználói csoport vagy alkalmazás engedélyeket kap a felügyelt erőforráscsoporthoz a kijelölt szerepkörnek megfelelően. A szerepkör bármely beépített szerepköralapú hozzáférés-vezérlési (RBAC) szerepkör lehet, például Tulajdonos vagy Közreműködő. Az erőforrások felügyeletére egyéni felhasználói engedélyt is lehet adni, de ezt az engedélyt általában egy felhasználói csoporthoz rendelik hozzá. Új Azure Active Directory felhasználói csoport létrehozásához tekintse meg [az Azure Active Directoryban csoportok létrehozását és tagok hozzáadását ismertető](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) cikket.

Az erőforrások felügyeletéhez szüksége lesz a felhasználói csoport objektumazonosítójára. 

```powershell
$groupID=(Get-AzADGroup -DisplayName mygroup).Id
```

### <a name="get-the-role-definition-id"></a>A szerepkördefiníció-azonosító lekérése

Ezután annak a beépített RBAC-szerepkörnek a szerepkördefiníció-azonosítójára van szükség, amelyhez hozzáférést szeretne biztosítani a felhasználó, a felhasználói csoport vagy az alkalmazás számára. Általában a Tulajdonos, a Közreműködő vagy az Olvasó szerepkört használják. Az alábbi parancs bemutatja, hogyan kérheti le a tulajdonos szerepkör szerepkördefiníció-azonosítóját:

```powershell
$ownerID=(Get-AzRoleDefinition -Name Owner).Id
```

### <a name="create-the-managed-application-definition"></a>A felügyelt alkalmazás definíciójának létrehozása

Ha még nem rendelkezik erőforráscsoporttal a felügyelt alkalmazás definíciójának tárolásához, hozzon létre egyet:

```powershell
New-AzResourceGroup -Name appDefinitionGroup -Location westcentralus
```

Most hozza létre a felügyelt alkalmazás definíciójához tartozó erőforrást.

```powershell
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

### <a name="make-sure-users-can-see-your-definition"></a>Győződjön meg arról, hogy a definíció a felhasználók számára látható

Ön hozzáfér a felügyelt alkalmazás definíciójához, azonban gondoskodnia kell róla, hogy a vállalat más felhasználói is hozzáférjenek. Engedélyezzen számukra legalább Olvasó szerepkört a definícióhoz. Előfordulhat, hogy ezt a hozzáférési szintet már megörökölték az előfizetéstől vagy az erőforráscsoporttól. A definíció elérésének ellenőrzésével, valamint további felhasználók és csoportok hozzáadásával kapcsolatban lásd: [Az Azure-előfizetések erőforrásaihoz való hozzáférés kezelése szerepköralapú hozzáférés-vezérléssel](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>További lépések

* Ha közzétenné felügyelt alkalmazását az Azure Marketplace-en, tekintse meg az [Azure-beli felügyelt alkalmazások a Marketplace piactéren](publish-marketplace-app.md) című témakört.
* A felügyelt alkalmazás egy példányának üzembe helyezéséről további információt [a szolgáltatáskatalógusban elérhető alkalmazás az Azure Portalon keresztül történő üzembe helyezését](deploy-service-catalog-quickstart.md) ismertető cikkben találhat.
