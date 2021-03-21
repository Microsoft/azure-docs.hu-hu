---
title: Virtuálisgép-bővítmény engedélyezése Azure PowerShell használatával
description: Ez a cikk bemutatja, hogyan telepíthet virtuálisgép-bővítményeket hibrid felhőalapú környezetekben futó Azure arc-kompatibilis kiszolgálókra Azure PowerShell használatával.
ms.date: 01/05/2021
ms.topic: conceptual
ms.openlocfilehash: 9b1f83ad976aa3471430a912280fac25dc5c5c0c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97916184"
---
# <a name="enable-azure-vm-extensions-using-azure-powershell"></a>Azure virtuálisgép-bővítmények engedélyezése Azure PowerShell használatával

Ez a cikk bemutatja, hogyan telepítheti és távolíthatja el az Azure arc-kompatibilis kiszolgálók által támogatott Azure-beli virtuálisgép-bővítményeket egy Linux vagy Windows rendszerű hibrid gépre Azure PowerShell használatával.

## <a name="prerequisites"></a>Előfeltételek

- Azure PowerShell-t futtató számítógép. Útmutatásért lásd: [Azure PowerShell telepítése és konfigurálása](/powershell/azure/).

Mielőtt a Azure PowerShell használatával kezelhesse a virtuálisgép-bővítményeket az arc-kompatibilis kiszolgálók által felügyelt hibrid kiszolgálón, telepítenie kell a `Az.ConnectedMachine` modult. Futtassa a következő parancsot az ív használatára képes kiszolgálón:

`Install-Module -Name Az.ConnectedMachine`.

A telepítés befejeztével a következő üzenet jelenik meg:

`The installed extension `Az. ConnectedMachine` is experimental and not covered by customer support. Please use with discretion.`

## <a name="enable-extension"></a>Bővítmény engedélyezése

Ha engedélyezni szeretné a virtuálisgép-bővítményt az ív használatára képes kiszolgálón, használja a [New-AzConnectedMachineExtension-](/powershell/module/az.connectedmachine/new-azconnectedmachineextension) t a,,,,, `-Name` `-ResourceGroupName` `-MachineName` `-Location` `-Publisher` , `ExtensionType` és `-Settings` paraméterek használatával.

Az alábbi példa engedélyezi a Log Analytics virtuálisgép-bővítményt egy arc-kompatibilis Linux-kiszolgálón:

```powershell
PS C:\> $Setting = @{ "workspaceId" = "workspaceId" }
PS C:\> $protectedSetting = @{ "workspaceKey" = "workspaceKey" }
PS C:\> New-AzConnectedMachineExtension -Name OMSLinuxAgent -ResourceGroupName "myResourceGroup" -MachineName "myMachine" -Location "eastus" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -TypeHandlerVersion "1.10" -Settings $Setting -ProtectedSetting $protectedSetting -ExtensionType "OmsAgentForLinux"
```

Ha engedélyezni szeretné a Log Analytics virtuálisgép-bővítményt egy arc-kompatibilis Windows Serveren, módosítsa a paraméter értékét az `-ExtensionType` `"MicrosoftMonitoringAgent"` előző példában szereplő értékre.

Az alábbi példa engedélyezi az egyéni szkriptek bővítményét egy arc-kompatibilis kiszolgálón:

```powershell
PS C:\> $Setting = @{ "commandToExecute" = "powershell.exe -c Get-Process" }
PS C:\> New-AzConnectedMachineExtension -Name custom -ResourceGroupName myResourceGroup -MachineName myMachineName -Location eastus -Publisher "Microsoft.Compute" -TypeHandlerVersion 1.10 -Settings $Setting -ExtensionType CustomScriptExtension
```

### <a name="key-vault-vm-extension-preview"></a>Key Vault VM-bővítmény (előzetes verzió)

> [!WARNING]
> A PowerShell-ügyfelek gyakran felvesznek `\` `"` a settings.jsba, amelynek hatására a akvvm_service hibát jelez: `[CertificateManagementConfiguration] Failed to parse the configuration settings with:not an object.`

Az alábbi példa engedélyezi a Key Vault virtuálisgép-bővítményt (előzetes verzió) egy arc-kompatibilis kiszolgálón:

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

Az ív használatára képes kiszolgálón található virtuálisgép-bővítmények listájának lekéréséhez használja a [Get-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/get-azconnectedmachineextension) a `-MachineName` és a `-ResourceGroupName` paramétereket.

Példa:

```powershell
Get-AzConnectedMachineExtension -ResourceGroupName myResourceGroup -MachineName myMachineName

Name    Location  PropertiesType        ProvisioningState
----    --------  --------------        -----------------
custom  westus2   CustomScriptExtension Succeeded
```

## <a name="remove-an-installed-extension"></a>Telepített bővítmény eltávolítása

Ha el szeretné távolítani egy telepített virtuálisgép-bővítményt az ív használatára képes kiszolgálón, használja a [Remove-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/remove-azconnectedmachineextension) a `-Name` és a `-MachineName` `-ResourceGroupName` paramétereket.

Ha például el szeretné távolítani a linuxos Log Analytics virtuálisgép-bővítményt, futtassa a következő parancsot:

```powershell
Remove-AzConnectedMachineExtension -MachineName myMachineName -ResourceGroupName myResourceGroup -Name OmsAgentforLinux
```

## <a name="next-steps"></a>Következő lépések

- A virtuálisgép-bővítményeket az [Azure CLI](manage-vm-extensions-cli.md)-vel, a [Azure Portal](manage-vm-extensions-portal.md)vagy [Azure Resource Manager sablonokból](manage-vm-extensions-template.md)is üzembe helyezheti, kezelheti és távolíthatja el.

- A hibaelhárítási információk a virtuálisgép- [bővítmények hibaelhárítási útmutatójában](troubleshoot-vm-extensions.md)találhatók.
