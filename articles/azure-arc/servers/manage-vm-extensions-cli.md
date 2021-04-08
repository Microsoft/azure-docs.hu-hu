---
title: VM-bővítmény engedélyezése az Azure CLI-vel
description: Ez a cikk bemutatja, hogyan telepíthet virtuálisgép-bővítményeket hibrid felhőalapú környezetekben futó Azure arc-kompatibilis kiszolgálókra az Azure CLI használatával.
ms.date: 01/05/2021
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6edb7d55e542f963c75693d535fa3b50dc5b827b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97916201"
---
# <a name="enable-azure-vm-extensions-using-the-azure-cli"></a>Azure VM-bővítmények engedélyezése az Azure CLI használatával

Ez a cikk bemutatja, hogyan telepítheti és távolíthatja el az Azure arc-kompatibilis kiszolgálók által támogatott virtuálisgép-bővítményeket egy Linux vagy Windows rendszerű hibrid gépre az Azure CLI használatával.

[!INCLUDE [Azure CLI Prepare your environment](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="install-the-azure-cli-extension"></a>Az Azure CLI-bővítmény telepítése

A ConnectedMachine parancsok nem az Azure CLI részeként jelennek meg. Mielőtt az Azure CLI-t használja a virtuálisgép-bővítmények az arc-kompatibilis kiszolgálók által kezelt hibrid kiszolgálón való kezeléséhez, be kell töltenie a ConnectedMachine-bővítményt. Futtassa a következő parancsot a lekéréséhez:

```azurecli
az extension add --name connectedmachine
```

## <a name="enable-extension"></a>Bővítmény engedélyezése

Ha engedélyezni szeretné a virtuálisgép-bővítményt az ív használatára képes kiszolgálón, használja az [az connectedmachine Extension Create](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_create) paramétert a,,,, `--machine-name` `--extension-name` `--location` `--type` `settings` és `--publisher` paraméterek használatával.

Az alábbi példa engedélyezi a Log Analytics virtuálisgép-bővítményt egy arc-kompatibilis kiszolgálón:

```azurecli
az connectedmachine extension create --machine-name "myMachineName" --name "OmsAgentForLinux or MicrosoftMonitoringAgent" --location "eastus" --settings '{\"workspaceId\":\"myWorkspaceId\"}' --protected-settings '{\"workspaceKey\":\"myWorkspaceKey\"}' --resource-group "myResourceGroup" --type-handler-version "1.13" --type "OmsAgentForLinux or MicrosoftMonitoringAgent" --publisher "Microsoft.EnterpriseCloud.Monitoring" 
```

Az alábbi példa engedélyezi az egyéni szkriptek bővítményét egy arc-kompatibilis kiszolgálón:

```azurecli
az connectedmachine extension create --machine-name "myMachineName" --name "CustomScriptExtension" --location "eastus" --type "CustomScriptExtension" --publisher "Microsoft.Compute" --settings "{\"commandToExecute\":\"powershell.exe -c \\\"Get-Process | Where-Object { $_.CPU -gt 10000 }\\\"\"}" --type-handler-version "1.10" --resource-group "myResourceGroup"
```

Az alábbi példa engedélyezi a Key Vault virtuálisgép-bővítményt (előzetes verzió) egy arc-kompatibilis kiszolgálón:

```azurecli
az connectedmachine extension create --resource-group "resourceGroupName" --machine-name "myMachineName" --location "regionName" --publisher "Microsoft.Azure.KeyVault" --type "KeyVaultForLinux or KeyVaultForWindows" --name "KeyVaultForLinux or KeyVaultForWindows" --settings '{"secretsManagementSettings": { "pollingIntervalInS": "60", "observedCertificates": ["observedCert1"] }, "authenticationSettings": { "msiEndpoint": "http://localhost:40342/metadata/identity" }}'
```

## <a name="list-extensions-installed"></a>Telepített bővítmények listája

Az ív használatára képes kiszolgálón található virtuálisgép-bővítmények listájának lekéréséhez használja az [az connectedmachine Extension List](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_list) `--machine-name` és a `--resource-group` paramétert.

Példa:

```azurecli
az connectedmachine extension list --machine-name "myMachineName" --resource-group "myResourceGroup"
```

Alapértelmezés szerint az Azure CLI-parancsok kimenete JSON (JavaScript Object Notation). Az alapértelmezett kimenet listára vagy táblára való módosításához használja például az [az configure--output](/cli/azure/reference-index)értéket. Bármely parancshoz hozzáadhatja azt is, ha `--output` a kimeneti formátum egy alkalommal változik.

A következő példa a parancs részleges JSON-kimenetét mutatja `az connectedmachine extension -list` :

```json
[
  {
    "autoUpgradingMinorVersion": "false",
    "forceUpdateTag": null,
    "id": "/subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/Microsoft.HybridCompute/machines/SVR01/extensions/DependencyAgentWindows",
    "location": "eastus",
    "name": "DependencyAgentWindows",
    "namePropertiesInstanceViewName": "DependencyAgentWindows",
```

## <a name="remove-an-installed-extension"></a>Telepített bővítmény eltávolítása

Ha el szeretné távolítani egy telepített virtuálisgép-bővítményt az ív használatára képes kiszolgálón, használja az [az connectedmachine Extension delete](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_delete) parancsot a (z `--extension-name` `--machine-name` `--resource-group` ) és paraméterrel.

Ha például el szeretné távolítani a linuxos Log Analytics virtuálisgép-bővítményt, futtassa a következő parancsot:

```azurecli
az connectedmachine extension delete --machine-name "myMachineName" --name "OmsAgentForLinux" --resource-group "myResourceGroup"
```

## <a name="next-steps"></a>Következő lépések

- A virtuálisgép-bővítmények üzembe helyezése, kezelése és eltávolítása a [Azure PowerShell](manage-vm-extensions-powershell.md), a [Azure Portal](manage-vm-extensions-portal.md)vagy a [Azure Resource Manager sablonok](manage-vm-extensions-template.md)használatával végezhető el.

- A hibaelhárítási információk a virtuálisgép- [bővítmények hibaelhárítási útmutatójában](troubleshoot-vm-extensions.md)találhatók.

- A parancsokkal kapcsolatos további információkért tekintse meg az Azure CLI virtuálisgép-bővítmény [áttekintését](/cli/azure/ext/connectedmachine/connectedmachine/extension) ismertető cikket.
