---
title: Migrálás a Azure Firewall Premium előzetes verziójára
description: Megtudhatja, hogyan telepíthet át Azure Firewall standard szintűről Azure Firewall Premium Preview-ra.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: ffdcad33568af9955dbdf5aafb1b6ffe4a51681d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100549714"
---
# <a name="migrate-to-azure-firewall-premium-preview"></a>Migrálás a Azure Firewall Premium előzetes verziójára

Az új prémium szintű képességek kihasználása érdekében áttelepítheti Azure Firewall standard Azure Firewall Premium Preview-ra. Az Azure Firewall Premium előzetes verziójával kapcsolatos további információkért lásd: [Azure Firewall Premium Preview-funkciók](premium-features.md).

A következő két példa a következőket mutatja be:
- Meglévő szabványos szabályzat migrálása Azure PowerShell használatával
- Telepítse át a meglévő szabványos tűzfalat (klasszikus szabályokkal) Azure Firewall prémium szintű szabályzattal.

## <a name="migrate-an-existing-policy-using-azure-powershell"></a>Meglévő szabályzat migrálása Azure PowerShell használatával

`Transform-Policy.ps1` egy Azure PowerShell szkript, amely egy meglévő szabványos házirendből létrehoz egy új prémium szabályzatot.

A standard szintű tűzfalszabály-azonosító miatt a parancsfájl a prémium Azure Firewall házirendre alakítja át. A szkript először csatlakozik az Azure-fiókhoz, lekéri a szabályzatot, átalakítja/hozzáadja a különböző paramétereket, majd feltölt egy új prémium szabályzatot. Az új prémium szabályzat neve `<previous_policy_name>_premium` .

Használati példa:

`Transform-Policy -PolicyId /subscriptions/XXXXX-XXXXXX-XXXXX/resourceGroups/some-resource-group/providers/Microsoft.Network/firewallPolicies/policy-name`

> [!IMPORTANT]
> A parancsfájl nem telepíti át a veszélyforrások hírszerzési beállításait. Ezeket a beállításokat manuálisan kell megadnia, mielőtt továbblépne, majd áttelepíti őket.

```azurepowershell
<#
    .SYNOPSIS
        Given an Azure firewall policy id the script will transform it to a Premium Azure firewall policy. 
        The script will first pull the policy, transform/add various parameters and then upload a new premium policy. 
        The created policy will be named <previous_policy_name>_premium if no new name provided else new policy will be named as the parameter passed.  
    .Example
        Transform-Policy -PolicyId /subscriptions/XXXXX-XXXXXX-XXXXX/resourceGroups/some-resource-group/providers/Microsoft.Network/firewallPolicies/policy-name -NewPolicyName <optional param for the new policy name>
#>

param (
    #Resource id of the azure firewall policy. 
    [Parameter(Mandatory=$true)]
    [string]
    $PolicyId,

    # #new filewallpolicy name, if not specified will be the previous name with the '_premium' suffix
    [Parameter(Mandatory=$false)]
    [string]
    $NewPolicyName = ""
)
$ErrorActionPreference = "Stop"
$script:PolicyId = $PolicyId
$script:PolicyName = $NewPolicyName

function ValidatePolicy {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Object]
        $Policy
    )

    Write-Host "Validating resource is as expected"

    if ($null -eq $Policy) {
        Write-Error "Recived null policy"
        exit(1)
    }
    if ($Policy.GetType().Name -ne "PSAzureFirewallPolicy") {
        Write-Host "Resource must be of type Microsoft.Network/firewallPolicies" -ForegroundColor Red
        exit(1)
    }

    if ($Policy.Sku.Tier -eq "Premium") {
        Write-Host "Policy is already premium"
        exit(1)
    }
}

function GetPolicyNewName {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Microsoft.Azure.Commands.Network.Models.PSAzureFirewallPolicy]
        $Policy
    )

    if (-not [string]::IsNullOrEmpty($script:PolicyName)) {
        return $script:PolicyName
    }

    return $Policy.Name + "_premium"
}

function TransformPolicyToPremium {
    [CmdletBinding()]
    param (
        [Parameter(Mandatory=$true)]
        [Microsoft.Azure.Commands.Network.Models.PSAzureFirewallPolicy]
        $Policy
    )    
    $NewPolicyParameters = @{
                        Name = (GetPolicyNewName -Policy $Policy) 
                        ResourceGroupName = $Policy.ResourceGroupName 
                        Location = $Policy.Location 
                        ThreatIntelMode = $Policy.ThreatIntelMode 
                        BasePolicy = $Policy.BasePolicy 
                        DnsSetting = $Policy.DnsSettings 
                        Tag = $Policy.Tag 
                        SkuTier = "Premium" 
    }

    Write-Host "Creating new policy"
    $premiumPolicy = New-AzFirewallPolicy @NewPolicyParameters

    Write-Host "Populating rules in new policy"
    foreach ($ruleCollectionGroup in $Policy.RuleCollectionGroups) {
        $ruleResource = Get-AzResource -ResourceId $ruleCollectionGroup.Id
        $ruleToTransfom = Get-AzFirewallPolicyRuleCollectionGroup -AzureFirewallPolicy $Policy -Name $ruleResource.Name
        $ruleCollectionGroup = @{
            FirewallPolicyObject = $premiumPolicy
            Priority = $ruleToTransfom.Properties.Priority
            Name = $ruleToTransfom.Name
        }

        if ($ruleToTransfom.Properties.RuleCollection.Count) {
            $ruleCollectionGroup["RuleCollection"] = $ruleToTransfom.Properties.RuleCollection
        }

        Set-AzFirewallPolicyRuleCollectionGroup @ruleCollectionGroup
    }
}

function ValidateAzNetworkModuleExists {
    Write-Host "Validating needed module exists"
    $networkModule = Get-InstalledModule -Name "Az.Network" -ErrorAction SilentlyContinue
    if (($null -eq $networkModule) -or ($networkModule.Version -lt 4.5)){
        Write-Host "Please install Az.Network module version 4.5.0 or higher, see instructions: https://github.com/Azure/azure-powershell#installation"
        exit(1)
    }
    Import-Module Az.Network -MinimumVersion 4.5.0
}

ValidateAzNetworkModuleExists
$policy = Get-AzFirewallPolicy -ResourceId $script:PolicyId
ValidatePolicy -Policy $policy
TransformPolicyToPremium -Policy $policy
```

## <a name="migrate-an-existing-standard-firewall-using-the-azure-portal"></a>Meglévő szabványos tűzfal migrálása a Azure Portal használatával

Ez a példa azt mutatja be, hogyan használható a Azure Portal egy szabványos tűzfal (klasszikus szabályok) áttelepítésére a prémium szintű szabályzattal való Azure Firewall.

1. A Azure Portal válassza ki a szabványos tűzfalat. Az **Áttekintés** lapon válassza a **Migrálás a tűzfal-házirendbe** lehetőséget.

   :::image type="content" source="media/premium-migrate/firewall-overview-migrate.png" alt-text="Migrálás a tűzfal házirendjébe":::

1. Az **áttelepítés a tűzfalra házirend** lapon válassza a **felülvizsgálat + létrehozás** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.

   A telepítés elvégzése néhány percet vesz igénybe.
1. Az `Transform-Policy.ps1` új standard szabályzatot a [Azure PowerShell szkripttel](#migrate-an-existing-policy-using-azure-powershell) alakíthatja át prémium szabályzatba.
1. A portálon válassza ki a szabványos tűzfal-erőforrást. 
1. Az **automatizálás** területen válassza a **sablon exportálása** lehetőséget. Tartsa nyitva a böngésző fület. Később visszatérhet.
   > [!TIP]
   > Annak érdekében, hogy ne veszítse el a sablont, töltse le és mentse azt arra az esetre, ha a böngésző lapja le lesz zárva vagy frissül.
1. Nyisson meg egy új böngésző fület, navigáljon a Azure Portal, és nyissa meg a tűzfalat tartalmazó erőforráscsoportot.
1. Törölje a meglévő szabványos tűzfal-példányt.

   Ez eltarthat néhány percig.

1. Térjen vissza a böngésző lapra az exportált sablonnal.
1. Válassza a **telepítés** lehetőséget, majd az **Egyéni telepítés** lapon válassza a **Sablon szerkesztése** lehetőséget.
1. A sablon szövegének szerkesztése:
   
   1. Az erőforrás alatt, a alatt, a `Microsoft.Network/azureFirewalls` `Properties` `sku` `tier` "standard" értékről a "Prémium" értékre kell váltania.
   1. A sablon alatt `Parameters` váltson `defaultValue` a `firewallPolicies_FirewallPolicy_,<your policy name>_externalid` from:
      
       `"/subscriptions/<subscription id>/resourceGroups/<your resource group>/providers/Microsoft.Network/firewallPolicies/FirewallPolicy_<your policy name>"`

      erre:

      `"/subscriptions/<subscription id>/resourceGroups/<your resource group>/providers/Microsoft.Network/firewallPolicies/FirewallPolicy_<your policy name>_premium"`
1. Kattintson a **Mentés** gombra.
1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.

Az üzembe helyezés befejeztével mostantól konfigurálhatja az összes új Azure Firewall Premium Preview funkciót.

## <a name="next-steps"></a>Következő lépések

- [További információ a Azure Firewall Premium funkcióival kapcsolatban](premium-features.md)