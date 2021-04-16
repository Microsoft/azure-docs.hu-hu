---
title: Rövid útmutató Azure Recovery Services-tároló létrehozásához Azure Resource Manager sablon használatával.
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Azure Recovery Services-tárolót egy Azure Resource Manager (ARM-sablon) használatával.
ms.date: 04/29/2020
ms.topic: quickstart
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 11a88b5485ad970802a65af31daccdb30a1c86df
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533247"
---
# <a name="quickstart-create-a-recovery-services-vault-using-an-arm-template"></a>Rövid útmutató: Recovery Services-tároló létrehozása ARM-sablonnal

Ez a rövid útmutató azt ismerteti, hogyan állíthat be Recovery Services-tárolót egy Azure Resource Manager (ARM-sablon) használatával. Az [Azure Site Recovery](site-recovery-overview.md) szolgáltatás hozzájárul az üzletmenet-folytonossági és vészhelyreállítási (BCDR) stratégia megvalósításához, így az üzleti alkalmazások online állapotban maradnak a tervezett és nem tervezett leállások során. Site Recovery felügyeli a helyszíni gépek és az Azure-beli virtuális gépek vészhelyreállítását, beleértve a replikációt, a feladatátvételt és a helyreállítást.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-recovery-services-vault-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik aktív Azure-előfizetéssel, a kezdés előtt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) fiókot.

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a gyorsútmutatóban használt sablon az [Azure-gyorssablonok](https://azure.microsoft.com/resources/templates/101-recovery-services-vault-create/) közül származik.

:::code language="json" source="~/quickstart-templates/101-recovery-services-vault-create/azuredeploy.json":::

A sablonban két Azure-erőforrás van meghatározva:

- [Microsoft.RecoveryServices-tárolók:](/azure/templates/microsoft.recoveryservices/vaults)létrehozza a tárolót.
- [Microsoft.RecoveryServices/vaults/backupstorageconfig:](/rest/api/backup/backupresourcestorageconfigs)konfigurálja a tároló biztonsági mentési redundanciabeállítását.

A sablon a tároló biztonsági mentési konfigurációjának opcionális paramétereit is tartalmazza. A tárolási redundancia beállításai a helyileg redundáns tárolás (LRS) vagy a georedundáns tárolás (GRS). További információ: [Set storage redundancy (Tárhelyredundania beállítása).](../backup/backup-create-rs-vault.md#set-storage-redundancy)

További Azure Recovery Services-sablonok: [Azure gyorsindítási sablonok.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Recoveryservices&pageNumber=1&sort=Popular)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

A sablon üzembe helyezéséhez szükség van az **előfizetésre,** az **erőforráscsoportra** és **a tároló nevére.**

1. Az Azure-ba való bejelentkezéshez és a sablon megnyitásához válassza az Üzembe **helyezés az Azure-ban rendszerképet.**

   [![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-recovery-services-vault-create%2Fazuredeploy.json)

1. Válassza ki vagy adja meg a következő értékeket:

   :::image type="content" source="media/quickstart-create-vault-template/create-vault-template.png" alt-text="Recovery Services-tároló létrehozására használható sablon.":::

   - **Előfizetés:** válassza ki az Azure-előfizetését.
   - **Erőforráscsoport:** válasszon ki egy meglévő csoportot, vagy válassza az **Új létrehozása** lehetőséget egy csoport hozzáadásához.
   - **Hely:** az alapértelmezett érték az erőforráscsoport helye, és az erőforráscsoport kiválasztása után elérhetetlenné válik.
   - **Tároló neve:** Adja meg a tároló nevét.
   - **Tárolási típus módosítása:** Az alapértelmezett érték **hamis.** Csak **akkor válassza** a true (igaz) lehetőséget, ha módosítania kell a tároló tárolótípusát.
   - **Tároló tárolótípusa:** Az alapértelmezett érték **a GloballyRedundant.** Ha a tároló típusa true **(igaz)** volt, válassza a **LocallyRedundant lehetőséget.**
   - **Hely:** a függvény `[resourceGroup().location]` alapértelmezés szerint az erőforráscsoport helyét használja. A hely módosítása egy érték, például **westus.**
   - Jelölje be az Elfogadom a fenti feltételeket és **feltételeket jelölőnégyzetet.**

1. A tároló üzembe helyezésének megkezdéséhez kattintson a **Vásárlás gombra.** Sikeres üzembe helyezés után megjelenik egy értesítés.

   :::image type="content" source="media/quickstart-create-vault-template/deployment-success.png" alt-text="A tároló üzembe helyezése sikeres volt.":::

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

A tároló létrejöttének megerősítéséhez használja az Azure CLI-t vagy a Azure PowerShell.

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

Az alábbi kimenet a tároló információinak egy részlete:

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

Ha az új erőforrások használatát tervezi, nincs szükség műveletre. Ellenkező esetben eltávolíthatja az ebben a rövid útmutatóban létrehozott erőforráscsoportot és tárolót. Az erőforráscsoport és az erőforrásainak törléséhez használja az Azure CLI-t vagy a Azure PowerShell.

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

Ebben a rövid útmutatóban létrehozott egy Recovery Services-tárolót. Ha többet szeretne megtudni a vészhelyreállításról, folytassa a következő rövid útmutatóval.

> [!div class="nextstepaction"]
> [Vészhelyreállítás beállítása](azure-to-azure-quickstart.md)
