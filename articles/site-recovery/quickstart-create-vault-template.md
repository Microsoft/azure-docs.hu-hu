---
title: Rövid útmutató Azure Recovery Services-tároló létrehozásához egy Azure Resource Manager sablon használatával.
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Azure Recovery Services-tárolót egy Azure Resource Manager sablon (ARM-sablon) használatával.
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 04/29/2020
ms.openlocfilehash: 62bfe71c397367282c696eecdf55ab9c900c69a1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92745875"
---
# <a name="quickstart-create-a-recovery-services-vault-using-an-arm-template"></a>Rövid útmutató: Recovery Services-tároló létrehozása ARM-sablonnal

Ez a rövid útmutató azt ismerteti, hogyan állíthat be egy Recovery Services-tárolót egy Azure Resource Manager-sablon (ARM-sablon) használatával. Az [Azure site Recovery](site-recovery-overview.md) szolgáltatás hozzájárul az üzletmenet-folytonossági és a vész-helyreállítási (BCDR) stratégiához, így az üzleti alkalmazásai a tervezett és nem tervezett leállások során online maradnak. Site Recovery kezeli a helyszíni gépek és az Azure-beli virtuális gépek (VM) vész-helyreállítását, beleértve a replikálást, a feladatátvételt és a helyreállítást.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-recovery-services-vault-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik aktív Azure-előfizetéssel, akkor a Kezdés előtt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-recovery-services-vault-create/) közül származik.

:::code language="json" source="~/quickstart-templates/101-recovery-services-vault-create/azuredeploy.json":::

Két Azure-erőforrás van definiálva a sablonban:

- [Microsoft. recoveryservices szolgáltatónál](/azure/templates/microsoft.recoveryservices/vaults)-tárolók: létrehozza a tárolót.
- [Microsoft. recoveryservices szolgáltatónál/Vaults/backupstorageconfig](/rest/api/backup/backupresourcestorageconfigs): a tár biztonsági mentési redundancia-beállításainak konfigurálása.

A sablon a tár biztonsági mentési konfigurációjához választható paramétereket tartalmaz. A tárolási redundancia-beállítások a helyileg redundáns tárolás (LRS) vagy a Geo-redundáns tárolás (GRS). További információ: [set Storage redundancia](../backup/backup-create-rs-vault.md#set-storage-redundancy).

Az Azure Recovery Services-sablonokkal kapcsolatos további információkért lásd: [Azure Gyorsindítás sablonok](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Recoveryservices&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

A sablon üzembe helyezéséhez az **előfizetés**, az **erőforráscsoport** és a tár **neve** szükséges.

1. Az Azure-ba való bejelentkezéshez és a sablon megnyitásához válassza ki az **üzembe helyezés az Azure** -ban lemezképet.

   [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-recovery-services-vault-create%2Fazuredeploy.json)

1. Válassza ki vagy adja meg a következő értékeket:

   :::image type="content" source="media/quickstart-create-vault-template/create-vault-template.png" alt-text="Recovery Services-tároló létrehozására szolgáló sablon.":::

   - **Előfizetés**: válassza ki az Azure-előfizetését.
   - **Erőforráscsoport**: válasszon ki egy meglévő csoportot, vagy válassza az **új létrehozása** lehetőséget egy csoport hozzáadásához.
   - **Hely**: alapértelmezés szerint az erőforráscsoport helye, és az erőforráscsoport kijelölése után elérhetetlenné válik.
   - Tár **neve**: adjon nevet a tárolónak.
   - **Tárolási típus módosítása**: az alapértelmezett érték a **false**. Csak akkor válassza az **igaz** értéket, ha módosítania kell a tár tárolási típusát.
   - **Tár tárolási típusa**: az alapértelmezett érték a **GloballyRedundant**. Ha a tárolási típus értéke TRUE ( **igaz**), válassza a **LocallyRedundant** lehetőséget.
   - **Hely**: a függvény `[resourceGroup().location]` alapértelmezett értéke az erőforráscsoport helye. A hely módosításához adjon meg egy értéket, például **westus**.
   - Jelölje be az **Elfogadom a fenti feltételeket és kikötéseket** jelölőnégyzetet.

1. A tár üzembe helyezésének megkezdéséhez kattintson a **vásárlás** gombra. Sikeres üzembe helyezés után megjelenik egy értesítés.

   :::image type="content" source="media/quickstart-create-vault-template/deployment-success.png" alt-text="A tár üzembe helyezése sikeres volt.":::

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

A tár létrehozásának ellenőrzéséhez használja az Azure CLI-t vagy a Azure PowerShell.

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
echo "Enter the vault name:" &&
read vaultName &&
az backup vault show --name $vaultName --resource-group $resourceGroupName &&
az backup vault backup-properties show --name $vaultName --resource-group $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resouceGroupName = Read-Host -Prompt "Enter the resource group name"
$vaultName = Read-Host -Prompt "Enter the vault name"
$vaultBackupConfig = Get-AzRecoveryServicesVault -Name $vaultName
Get-AzRecoveryServicesVault -ResourceGroupName $resouceGroupName -Name $vaultName
Get-AzRecoveryServicesBackupProperty -Vault $vaultBackupConfig
Write-Host "Press [ENTER] to continue..."
```

---

A következő kimenet a tár információinak kivonata:

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

```Output
"id": "/subscriptions/<Subscription Id>/resourceGroups/myResourceGroup
         /providers/Microsoft.RecoveryServices/vaults/myVault"
"location": "eastus"
"name": "myVault"
"resourceGroup": "myResourceGroup"

"storageModelType": "GeoRedundant"
"storageType": "GeoRedundant"
"type": "Microsoft.RecoveryServices/vaults/backupstorageconfig"
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```Output
Name              : myVault
Type              : Microsoft.RecoveryServices/vaults
Location          : eastus
ResourceGroupName : myResourceGroup
SubscriptionId    : <Subscription Id>

BackupStorageRedundancy
-----------------------
GeoRedundant
```

---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy az új erőforrásokat használja, nincs szükség beavatkozásra. Ellenkező esetben eltávolíthatja az ebben a rövid útmutatóban létrehozott erőforráscsoportot és tárat. Az erőforráscsoport és a hozzá tartozó erőforrások törléséhez használja az Azure CLI-t vagy a Azure PowerShell.

# <a name="cli"></a>[Parancssori felület](#tab/CLI)

```azurecli-interactive
echo "Enter the resource group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resouceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resouceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Recovery Services-tárolót. Ha többet szeretne megtudni a vész-helyreállításról, folytassa a következő rövid útmutatóval.

> [!div class="nextstepaction"]
> [Vészhelyreállítás beállítása](azure-to-azure-quickstart.md)
