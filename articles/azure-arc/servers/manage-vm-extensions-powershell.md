---
title: Virtuálisgép-bővítmény engedélyezése a Azure PowerShell
description: Ez a cikk bemutatja, hogyan helyezhet üzembe virtuálisgép-bővítményeket Azure Arc hibridfelhő-környezetekben futó, engedélyezett kiszolgálókon az Azure PowerShell.
ms.date: 04/13/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d5723655b61040c7ddf99e5f11488fff379d96a0
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832877"
---
# <a name="enable-azure-vm-extensions-using-azure-powershell"></a>Azure-beli virtuálisgép-bővítmények engedélyezése Azure PowerShell

Ez a cikk bemutatja, hogyan helyezhet üzembe és távolíthat el az Azure Arc-kompatibilis kiszolgálók által támogatott Azure-beli virtuálisgép-bővítményeket Linux vagy Windows rendszerű hibrid Azure PowerShell.

> [!NOTE]
> Azure Arc kompatibilis kiszolgálók nem támogatják a virtuálisgép-bővítmények Azure-beli virtuális gépeken való üzembe helyezését és felügyeletét. Azure-beli virtuális gépekhez tekintse meg a virtuálisgép-bővítmények [áttekintését ismertető cikket.](../../virtual-machines/extensions/overview.md)

## <a name="prerequisites"></a>Előfeltételek

- Egy számítógép, Azure PowerShell. Útmutatásért lásd: [Install and configure Azure PowerShell.](/powershell/azure/)

Mielőtt a Azure PowerShell arc-kompatibilis kiszolgálók által felügyelt hibrid kiszolgálón felügyeli a virtuálisgép-bővítményeket, telepítenie kell a `Az.ConnectedMachine` modult. Futtassa a következő parancsot az Arc-kompatibilis kiszolgálón:

`Install-Module -Name Az.ConnectedMachine`.

A telepítés befejezésekor a következő üzenet jelenik meg:

`The installed extension `Az.ConnectedMachine` is experimental and not covered by customer support. Please use with discretion.`

## <a name="enable-extension"></a>Bővítmény engedélyezése

Ha engedélyezni szeretné a virtuálisgép-bővítményt az Arc-kompatibilis kiszolgálón, használja a [New-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/new-azconnectedmachineextension) parancsokat a `-Name` , , , , - , és `-ResourceGroupName` `-MachineName` `-Location` `-Publisher` `ExtensionType` `-Settings` paraméterekkel.

Az alábbi példa engedélyezi a Log Analytics virtuálisgép-bővítményt egy Arc-kompatibilis Linux-kiszolgálón:

```powershell
PS C:\> $Setting = @{ "workspaceId" = "workspaceId" }
PS C:\> $protectedSetting = @{ "workspaceKey" = "workspaceKey" }
PS C:\> New-AzConnectedMachineExtension -Name OMSLinuxAgent -ResourceGroupName "myResourceGroup" -MachineName "myMachine" -Location "eastus" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -TypeHandlerVersion "1.10" -Settings $Setting -ProtectedSetting $protectedSetting -ExtensionType "OmsAgentForLinux"
```

Ha engedélyezni szeretné a Log Analytics virtuálisgép-bővítményt egy Arc-kompatibilis Windows-kiszolgálón, módosítsa a paraméter értékét az `-ExtensionType` `"MicrosoftMonitoringAgent"` előző példában értékre.

Az alábbi példa engedélyezi az egyéni szkriptbővítményt egy Arc-kompatibilis kiszolgálón:

```powershell
PS C:\> $Setting = @{ "commandToExecute" = "powershell.exe -c Get-Process" }
PS C:\> New-AzConnectedMachineExtension -Name custom -ResourceGroupName myResourceGroup -MachineName myMachineName -Location eastus -Publisher "Microsoft.Compute" -TypeHandlerVersion 1.10 -Settings $Setting -ExtensionType CustomScriptExtension
```

### <a name="key-vault-vm-extension-preview"></a>Key Vault bővítmény (előzetes verzió)

> [!WARNING]
> A PowerShell-ügyfelek gyakran hozzáadják a hez a settings.js, ami hibát akvvm_service `\` `"` hibát okoz: `[CertificateManagementConfiguration] Failed to parse the configuration settings with:not an object.`

Az alábbi példa engedélyezi Key Vault virtuálisgép-bővítményt (előzetes verzió) egy Arc-kompatibilis kiszolgálón:

```powershell
# Build settings
    $settings = @{
      secretsManagementSettings = @{
       observedCertificates = @(
        "observedCert1"
       )
      certificateStoreLocation = "myMachineName" # For Linux use "/var/lib/waagent/Microsoft.Azure.KeyVault.Store/"
      certificateStore = "myCertificateStoreName"
      pollingIntervalInS = "pollingInterval"
      }
    authenticationSettings = @{
     msiEndpoint = "http://localhost:40342/metadata/identity"
     }
    }

    $resourceGroup = "resourceGroupName"
    $machineName = "myMachineName"
    $location = "regionName"

    # Start the deployment
    New-AzConnectedMachineExtension -ResourceGroupName $resourceGRoup -Location $location -MachineName $machineName -Name "KeyVaultForWindows or KeyVaultforLinux" -Publisher "Microsoft.Azure.KeyVault" -ExtensionType "KeyVaultforWindows or KeyVaultforLinux" -Setting (ConvertTo-Json $settings)
```

## <a name="list-extensions-installed"></a>Telepített bővítmények listája

Az Arc-kompatibilis kiszolgálón található virtuálisgép-bővítmények listáját a [Get-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/get-azconnectedmachineextension) és paraméterekkel `-MachineName` `-ResourceGroupName` használhatja.

Példa:

```powershell
Get-AzConnectedMachineExtension -ResourceGroupName myResourceGroup -MachineName myMachineName

Name    Location  PropertiesType        ProvisioningState
----    --------  --------------        -----------------
custom  westus2   CustomScriptExtension Succeeded
```

## <a name="remove-an-installed-extension"></a>Telepített bővítmény eltávolítása

Ha el szeretne távolítani egy telepített virtuálisgép-bővítményt az Arc-kompatibilis kiszolgálón, használja a [Remove-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/remove-azconnectedmachineextension) paramétert a , és `-Name` `-MachineName` `-ResourceGroupName` paraméterrel.

A Linuxhoz használt Log Analytics virtuálisgép-bővítmény eltávolításához például futtassa a következő parancsot:

```powershell
Remove-AzConnectedMachineExtension -MachineName myMachineName -ResourceGroupName myResourceGroup -Name OmsAgentforLinux
```

## <a name="next-steps"></a>Következő lépések

- A virtuálisgép-bővítményeket az [Azure CLI](manage-vm-extensions-cli.md)használatával telepítheti, kezelheti és távolíthatja el a(Azure Portal) vagy Azure Resource Manager [](manage-vm-extensions-portal.md) [sablonokból.](manage-vm-extensions-template.md)

- A hibaelhárítási információkat a Virtuálisgép-bővítmények [hibaelhárítása útmutatóban talál.](troubleshoot-vm-extensions.md)
