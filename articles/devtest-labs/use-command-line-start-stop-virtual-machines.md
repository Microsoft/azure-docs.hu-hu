---
title: Virtuális gépek indítása és leállítása parancssori eszközök használatával Azure DevTest Labs
description: Megtudhatja, hogyan indíthatja el és állíthatja le a virtuális gépeket a Azure DevTest Labsban a parancssori eszközök használatával.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 2ddc1620cf86fa203b2f0e31359f9fd262df8916
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102499543"
---
# <a name="use-command-line-tools-to-start-and-stop-azure-devtest-labs-virtual-machines"></a>Azure DevTest Labs virtuális gépek indítása és leállítása parancssori eszközök használatával
Ez a cikk bemutatja, hogyan indíthatja el vagy állíthatja le a virtuális gépeket a Azure PowerShell vagy az Azure CLI használatával a Azure DevTest Labs tesztkörnyezetben. A műveletek automatizálásához létrehozhat PowerShell-vagy CLI-parancsfájlokat is. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Áttekintés
Azure DevTest Labs gyors, könnyen használható és karcsú fejlesztési és tesztelési környezeteket hozhat létre. Lehetővé teszi a díjak kezelését, a virtuális gépek gyors üzembe helyezését és a hulladék minimalizálását.  A Azure Portal beépített funkciói lehetővé teszik, hogy a virtuális gépeket egy laborban konfigurálja, hogy automatikusan induljon el és álljon le adott időpontokban. 

Bizonyos esetekben azonban érdemes lehet automatizálni a virtuális gépek indítását és leállítását a PowerShell-vagy CLI-parancsfájlokból. Némi rugalmasságot biztosít az egyes gépek elindításához és leállításához, a megadott időpontok helyett. Íme néhány olyan helyzet, amelyben a feladatok parancsfájlok használatával történő futtatása hasznos lehet.

- Ha egy tesztelési környezet részeként 3 rétegbeli alkalmazást használ, a rétegeket egy sorba kell kezdeni. 
- Kapcsolja ki a virtuális gépet, ha az egyéni feltételek teljesülnek a pénz megtakarítása érdekében. 
- Egy CI/CD-munkafolyamaton belül feladatként használhatja a folyamat elejére, a virtuális gépeket Build Machines, test Machines vagy Infrastructure néven használhatja, majd leállíthatja a virtuális gépeket, amikor a folyamat befejeződött. Erre példa az egyéni rendszerkép-előállító Azure DevTest Labs.  

## <a name="azure-powershell"></a>Azure PowerShell

> [!NOTE]
> A következő szkript a Azure PowerShell az modult használja. 

A következő PowerShell-parancsfájl egy virtuális gépet indít el egy laborban. A [meghívó-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) a parancsfájl elsődleges fókusza. A **ResourceId** paraméter a virtuális gép teljes erőforrás-azonosítója a laborban. A **műveleti** paraméter az a hely, ahol a szükséges **indítási** vagy **leállítási** beállítások vannak megadva.

```powershell
# The id of the subscription
$subscriptionId = "111111-11111-11111-1111111"

# The name of the lab
$devTestLabName = "yourlabname"

# The name of the virtual machine to be started
$vMToStart = "vmname"

# The action on the virtual machine (Start or Stop)
$vmAction = "Start"

# Select the Azure subscription
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab information
$devTestLab = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $devTestLabName

# Start the VM and return a succeeded or failed status
$returnStatus = Invoke-AzResourceAction `
                    -ResourceId "$($devTestLab.ResourceId)/virtualmachines/$vMToStart" `
                    -Action $vmAction `
                    -Force

if ($returnStatus.Status -eq 'Succeeded') {
    Write-Output "##[section] Successfully updated DTL machine: $vMToStart, Action: $vmAction"
}
else {
    Write-Error "##[error]Failed to update DTL machine: $vMToStart, Action: $vmAction"
}
```


## <a name="azure-cli"></a>Azure CLI
Az [Azure CLI](/cli/azure/get-started-with-azure-cli) egy másik módszer a DevTest Labs-beli virtuális gépek indításának és leállításának automatizálására. Az Azure CLI különböző operációs rendszerekre [telepíthető](/cli/azure/install-azure-cli) . A következő szkript egy virtuális gép elindítására és leállítására szolgáló parancsokat biztosít a laborban. 

```azurecli
# Sign in to Azure
az login 

## Get the name of the resource group that contains the lab
az resource list --resource-type "Microsoft.DevTestLab/labs" --name "yourlabname" --query "[0].resourceGroup" 

## Start the VM
az lab vm start --lab-name yourlabname --name vmname --resource-group labResourceGroupName

## Stop the VM
az lab vm stop --lab-name yourlabname --name vmname --resource-group labResourceGroupName
```


## <a name="next-steps"></a>Következő lépések
Az alábbi cikkből megtudhatja, hogyan használhatja a Azure Portal a következő műveletek elvégzéséhez: [indítsa újra a virtuális gépet](devtest-lab-restart-vm.md).
