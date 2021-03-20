---
title: Gyors útmutató – Resource Manager-sablon virtuális gép biztonsági mentése
description: Ismerje meg, hogyan készíthet biztonsági mentést a virtuális gépekről Azure Resource Manager sablonnal
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 05/14/2019
ms.custom: mvc,subject-armqs
ms.openlocfilehash: e64aa10d8ddadd367d04b2b480770a99f3ece1dc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88826531"
---
# <a name="quickstart-back-up-a-virtual-machine-in-azure-with-an-arm-template"></a>Gyors útmutató: virtuális gép biztonsági mentése az Azure-ban ARM-sablonnal

[Azure Backup](backup-overview.md) biztonsági mentést készít a helyszíni gépekről és alkalmazásokról, valamint az Azure-beli virtuális gépekről. Ez a cikk bemutatja, hogyan készíthet biztonsági mentést egy Azure-beli virtuális gépről egy Azure Resource Manager sablonnal (ARM-sablon) és Azure PowerShell. Ez a rövid útmutató egy ARM-sablon üzembe helyezésének folyamatát tárgyalja Recovery Services-tároló létrehozásához. Az ARM-sablonok fejlesztésével kapcsolatos további információkért tekintse meg a [Azure Resource Manager dokumentációját](../azure-resource-manager/index.yml) és a [sablonra vonatkozó referenciát](/azure/templates/microsoft.recoveryservices/allversions).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

A [Recovery Services](backup-azure-recovery-services-vault-overview.md) -tár egy logikai tároló, amely a védett erőforrások, például az Azure-beli virtuális gépek biztonsági mentési szolgáltatásait tárolja. A biztonsági mentési feladatok futtatásakor egy helyreállítási pontot hoz létre a Recovery Services-tárolón belül. Ezt követően ezen helyreállítási pontok egyikével állíthatja vissza az adatokat egy adott időpontra. Azt is megteheti, hogy a virtuális gép biztonsági mentését [Azure PowerShell](./quick-backup-vm-powershell.md), az [Azure CLI](quick-backup-vm-cli.md)vagy a [Azure Portal](quick-backup-vm-portal.md)használatával végezheti el.

Ha a környezet megfelel az előfeltételeknek, és már ismeri az ARM-sablonokat, kattintson az **Üzembe helyezés az Azure-ban** gombra. A sablon az Azure Portalon fog megnyílni.

[![Üzembe helyezés az Azure-ban](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-recovery-services-create-vm-and-configure-backup%2Fazuredeploy.json)

## <a name="review-the-template"></a>A sablon áttekintése

Az ebben a rövid útmutatóban használt sablon az [Azure Gyorsindítás sablonjaiból](https://azure.microsoft.com/resources/templates/101-recovery-services-create-vm-and-configure-backup/)származik. Ez a sablon lehetővé teszi, hogy egyszerű Windows-alapú virtuális gépet és Recovery Services-tárolót helyezzen üzembe, amely a DefaultPolicy-védelemmel van konfigurálva.

:::code language="json" source="~/quickstart-templates/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json":::

A sablonban definiált erőforrások a következők:

- [**Microsoft. Storage/storageAccounts**](/azure/templates/microsoft.storage/storageaccounts)
- [**Microsoft. Network/nyilvános IP**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups)
- [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces)
- [**Microsoft. számítás/virutalMachines**](/azure/templates/microsoft.compute/virtualmachines)
- [**Microsoft. Recoveryservices szolgáltatónál/tárolók**](/azure/templates/microsoft.recoveryservices/2016-06-01/vaults)
- [**Microsoft. Recoveryservices szolgáltatónál/Vaults/backupFabrics/protectionContainers/protectedItems**](/azure/templates/microsoft.recoveryservices/vaults/backupfabrics/protectioncontainers/protecteditems)

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

A sablon üzembe helyezéséhez válassza a **kipróbálás** lehetőséget a Azure Cloud Shell megnyitásához, majd illessze be a következő PowerShell-szkriptet a rendszerhéj ablakába. A kód beillesztéséhez kattintson a jobb gombbal a rendszerhéj-ablakra, majd válassza a **Beillesztés** lehetőséget.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name (limited to eight characters) that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (for example, centralus)"
$adminUsername = Read-Host -Prompt "Enter the administrator username for the virtual machine"
$adminPassword = Read-Host -Prompt "Enter the administrator password for the virtual machine" -AsSecureString
$dnsPrefix = Read-Host -Prompt "Enter the unique DNS Name for the Public IP used to access the virtual machine"

$resourceGroupName = "${projectName}rg"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-recovery-services-create-vm-and-configure-backup/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -adminUsername $adminUsername -adminPassword $adminPassword -dnsLabelPrefix $dnsPrefix
```

A Azure PowerShell az ARM-sablon üzembe helyezéséhez használható ebben a rövid útmutatóban. A [Azure Portal](../azure-resource-manager/templates/deploy-portal.md), az [Azure CLI](../azure-resource-manager/templates/deploy-cli.md)és a [REST API](../azure-resource-manager/templates/deploy-rest.md) is használható sablonok telepítéséhez.

## <a name="validate-the-deployment"></a>Az üzembe helyezés ellenőrzése

### <a name="start-a-backup-job"></a>Biztonsági mentési feladat indítása

A sablon létrehoz egy virtuális gépet, és lehetővé teszi a virtuális gép visszaállítását. A sablon üzembe helyezése után el kell indítania egy biztonsági mentési feladatot. További információkért lásd: [biztonsági mentési feladatok elindítása](./quick-backup-vm-powershell.md#start-a-backup-job).

### <a name="monitor-the-backup-job"></a>A biztonsági mentési feladat monitorozása

A biztonsági mentési feladatok figyeléséhez tekintse meg [a biztonsági mentési feladatok figyelése](./quick-backup-vm-powershell.md#monitor-the-backup-job)című témakört.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a virtuális gép biztonsági mentésére, törölheti azt.

- Ha ki szeretné próbálni a virtuális gép visszaállítását, ugorja át a karbantartást.
- Ha meglévő virtuális gépet használt, akkor kihagyhatja a végleges [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) parancsmagot, hogy az erőforráscsoport és a virtuális gép ne legyen a helyén.

Tiltsa le a védelmet, távolítsa el a visszaállítási pontokat és a tárolót. Ezután törölje az erőforráscsoportot és a kapcsolódó virtuálisgép-erőforrásokat az alábbiak szerint:

```powershell
Disable-AzRecoveryServicesBackupProtection -Item $item -RemoveRecoveryPoints
$vault = Get-AzRecoveryServicesVault -Name "myRecoveryServicesVault"
Remove-AzRecoveryServicesVault -Vault $vault
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Következő lépések

A rövid útmutató során létrehozott egy Recovery Services-tárolót, engedélyezte a védelmet a virtuális gépen, valamint létrehozta a kezdeti helyreállítási pontot.

- [Megtudhatja, hogyan](tutorial-backup-vm-at-scale.md) készíthet biztonsági mentést a virtuális gépekről a Azure Portalban.
- [Megtudhatja, hogyan](tutorial-restore-disk.md) állíthatja be gyorsan a virtuális gépeket
- [Ismerje meg, hogyan](../azure-resource-manager/templates/template-tutorial-create-first-template.md) hozhat létre ARM-sablonokat.
