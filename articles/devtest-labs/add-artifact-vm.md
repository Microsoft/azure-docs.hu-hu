---
title: Összetevő hozzáadása egy virtuális géphez a Azure DevTest Labsban | Microsoft Docs
description: Megtudhatja, hogyan adhat hozzá egy összetevőt egy virtuális géphez egy tesztkörnyezetben Azure DevTest Labs
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b4772755d8077f7a659c4d403961ffaeb9e1d483
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "85483890"
---
# <a name="add-an-artifact-to-a-vm"></a>Műtermék hozzáadása virtuális géphez
A virtuális gépek létrehozása során meglévő összetevőket is hozzáadhat. Ezek az összetevők lehetnek a [nyilvános DevTest Labs git-tárházból](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) vagy a saját git-tárházból is. Ebből a cikkből megtudhatja, hogyan adhat hozzá összetevőket a Azure Portal és a Azure PowerShell használatával. 

Azure DevTest Labs *az* összetevők lehetővé teszik a virtuális gép üzembe helyezésekor végrehajtandó *műveletek* megadását, például a Windows PowerShell-parancsfájlok futtatását, a bash-parancsok futtatását és a szoftverek telepítését. Az összetevő- *Paraméterek* lehetővé teszik az adott forgatókönyvhöz tartozó összetevők testreszabását.

Az egyéni összetevők létrehozásával kapcsolatos további tudnivalókért tekintse meg a következő cikket: [egyéni összetevők létrehozása](devtest-lab-artifact-author.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-azure-portal"></a>Az Azure Portal használata 
1. Jelentkezzen be az [Azure Portalra](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Válassza a **minden szolgáltatás** lehetőséget, majd válassza ki a **DevTest Labs** elemet a listából.
1. A laborok listájából válassza ki azt a labort, amely a használni kívánt virtuális gépet tartalmazza.  
1. Válassza **a saját virtuális gépek** lehetőséget.
1. Válassza ki a kívánt virtuális gépet.
1. Válassza az összetevők **kezelése** lehetőséget. 
1. Válassza az összetevők **alkalmazása** lehetőséget.
1. Az összetevők **alkalmazása** ablaktáblán válassza ki azt az összetevőt, amelyet hozzá szeretne adni a virtuális géphez.
1. Az összetevő **hozzáadása** panelen adja meg a szükséges paramétereket, valamint az esetlegesen szükséges paramétereket.  
1. A **Hozzáadás** gombra kattintva vegye fel az összetevőt, és térjen vissza az összetevők **alkalmazása** ablaktáblára.
1. Folytassa az összetevők hozzáadását a virtuális géphez szükséges módon.
1. Az összetevők hozzáadása után [megváltoztathatja az összetevők futtatásának sorrendjét](#change-the-order-in-which-artifacts-are-run). Visszatérhet az összetevők [megtekintéséhez vagy módosításához](#view-or-modify-an-artifact)is.
1. Ha elkészült az összetevők hozzáadásával, kattintson az **alkalmaz** gombra.

### <a name="change-the-order-in-which-artifacts-are-run"></a>Az összetevők futtatási sorrendjének módosítása
Alapértelmezés szerint az összetevők műveletei a virtuális géphez való hozzáadásuk sorrendjében hajthatók végre. Az alábbi lépések bemutatják, hogyan módosíthatja az összetevők futtatásának sorrendjét.

1. Az összetevők **alkalmazása** ablaktábla felső részén válassza ki a virtuális géphez hozzáadott összetevők számát jelző hivatkozást.
   
    ![A virtuális géphez hozzáadott összetevők száma](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. A **kiválasztott** összetevők panelen húzza az összetevőket a kívánt sorrendbe. Ha nem sikerül áthúzni az összetevőt, győződjön meg róla, hogy az összetevő bal oldaláról húzza az elemet. 
1. Kattintson az **OK** gombra, amikor végzett.  

### <a name="view-or-modify-an-artifact"></a>Összetevő megtekintése és módosítása
Az alábbi lépések bemutatják, hogyan lehet megtekinteni vagy módosítani egy összetevő paramétereit:

1. Az összetevők **alkalmazása** ablaktábla felső részén válassza ki a virtuális géphez hozzáadott összetevők számát jelző hivatkozást.
   
    ![A virtuális géphez hozzáadott összetevők száma](./media/devtest-lab-add-vm-with-artifacts/devtestlab-add-artifacts-blade-selected-artifacts.png)
1. A **kiválasztott** összetevők ablaktáblán válassza ki a megtekinteni vagy szerkeszteni kívánt összetevőt.  
1. Az összetevő **hozzáadása** panelen végezze el a szükséges módosításokat, majd kattintson az **OK** gombra az összetevők **hozzáadása** ablaktábla bezárásához.
1. A **kijelölt** összetevők ablaktábla bezárásához kattintson **az OK gombra** .

## <a name="use-powershell"></a>A PowerShell használata
A következő parancsfájl a megadott összetevőt alkalmazza a megadott virtuális gépre. A [Meghívási-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) parancs az a művelet, amely végrehajtja a műveletet.  

```powershell
#Requires -Module Az.Resources

param
(
[Parameter(Mandatory=$true, HelpMessage="The ID of the subscription that contains the lab")]
   [string] $SubscriptionId,
[Parameter(Mandatory=$true, HelpMessage="The name of the lab containing the virtual machine")]
   [string] $DevTestLabName,
[Parameter(Mandatory=$true, HelpMessage="The name of the virtual machine")]
   [string] $VirtualMachineName,
[Parameter(Mandatory=$true, HelpMessage="The repository where the artifact is stored")]
   [string] $RepositoryName,
[Parameter(Mandatory=$true, HelpMessage="The artifact to apply to the virtual machine")]
   [string] $ArtifactName,
[Parameter(ValueFromRemainingArguments=$true)]
   $Params
)

# Set the appropriate subscription
Set-AzContext -SubscriptionId $SubscriptionId | Out-Null
 
# Get the lab resource group name
$resourceGroupName = (Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' | Where-Object { $_.Name -eq $DevTestLabName}).ResourceGroupName
if ($resourceGroupName -eq $null) { throw "Unable to find lab $DevTestLabName in subscription $SubscriptionId." }

# Get the internal repo name
$repository = Get-AzResource -ResourceGroupName $resourceGroupName `
                    -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
                    -ResourceName $DevTestLabName `
                    -ApiVersion 2016-05-15 `
                    | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
                    | Select-Object -First 1

if ($repository -eq $null) { "Unable to find repository $RepositoryName in lab $DevTestLabName." }

# Get the internal artifact name
$template = Get-AzResource -ResourceGroupName $resourceGroupName `
                -ResourceType "Microsoft.DevTestLab/labs/artifactSources/artifacts" `
                -ResourceName "$DevTestLabName/$($repository.Name)" `
                -ApiVersion 2016-05-15 `
                | Where-Object { $ArtifactName -in ($_.Name, $_.Properties.title) } `
                | Select-Object -First 1

if ($template -eq $null) { throw "Unable to find template $ArtifactName in lab $DevTestLabName." }

# Find the virtual machine in Azure
$FullVMId = "/subscriptions/$SubscriptionId/resourceGroups/$resourceGroupName`
                /providers/Microsoft.DevTestLab/labs/$DevTestLabName/virtualmachines/$virtualMachineName"

$virtualMachine = Get-AzResource -ResourceId $FullVMId

# Generate the artifact id
$FullArtifactId = "/subscriptions/$SubscriptionId/resourceGroups/$resourceGroupName`
                        /providers/Microsoft.DevTestLab/labs/$DevTestLabName/artifactSources/$($repository.Name)`
                        /artifacts/$($template.Name)"

# Handle the inputted parameters to pass through
$artifactParameters = @()

# Fill artifact parameter with the additional -param_ data and strip off the -param_
$Params | ForEach-Object {
   if ($_ -match '^-param_(.*)') {
      $name = $_.TrimStart('^-param_')
   } elseif ( $name ) {
      $artifactParameters += @{ "name" = "$name"; "value" = "$_" }
      $name = $null #reset name variable
   }
}

# Create structure for the artifact data to be passed to the action

$prop = @{
artifacts = @(
    @{
        artifactId = $FullArtifactId
        parameters = $artifactParameters
    }
    )
}

# Check the VM
if ($virtualMachine -ne $null) {
   # Apply the artifact by name to the virtual machine
   $status = Invoke-AzResourceAction -Parameters $prop -ResourceId $virtualMachine.ResourceId -Action "applyArtifacts" -ApiVersion 2016-05-15 -Force
   if ($status.Status -eq 'Succeeded') {
      Write-Output "##[section] Successfully applied artifact: $ArtifactName to $VirtualMachineName"
   } else {
      Write-Error "##[error]Failed to apply artifact: $ArtifactName to $VirtualMachineName"
   }
} else {
   Write-Error "##[error]$VirtualMachine was not found in the DevTest Lab, unable to apply the artifact"
}

```

## <a name="next-steps"></a>Következő lépések
Tekintse meg a következő cikkeket az összetevőkről:

- [Kötelező összetevők megadása a laborhoz](devtest-lab-mandatory-artifacts.md)
- [Egyéni összetevők létrehozása](devtest-lab-artifact-author.md)
- [Összetevő-adattár hozzáadása egy laborhoz](devtest-lab-artifact-author.md)
- [Az összetevők hibáinak diagnosztizálása](devtest-lab-troubleshoot-artifact-failure.md)
