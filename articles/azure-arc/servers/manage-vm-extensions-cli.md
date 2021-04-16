---
title: Virtuálisgép-bővítmény engedélyezése az Azure CLI használatával
description: Ez a cikk bemutatja, hogyan helyezhet üzembe virtuálisgép-bővítményeket Azure Arc hibrid felhőkörnyezetekben futó, engedélyezett kiszolgálókon az Azure CLI használatával.
ms.date: 04/13/2021
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.openlocfilehash: 25e75ede30139201789cd86e6ebddda09a664eb4
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388737"
---
# <a name="enable-azure-vm-extensions-using-the-azure-cli"></a>Azure-beli virtuálisgép-bővítmények engedélyezése az Azure CLI használatával

Ez a cikk bemutatja, hogyan helyezhet üzembe és távolíthat el az engedélyezett Azure Arc által támogatott virtuálisgép-bővítményeket Linux vagy Windows rendszerű hibrid gépekre az Azure CLI használatával.

> [!NOTE]
> Azure Arc kompatibilis kiszolgálók nem támogatják a virtuálisgép-bővítmények Azure-beli virtuális gépeken való üzembe helyezését és felügyeletét. Azure-beli virtuális gépekhez tekintse meg a virtuálisgép-bővítmények [áttekintését ismertető cikket.](../../virtual-machines/extensions/overview.md)

[!INCLUDE [Azure CLI Prepare your environment](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="install-the-azure-cli-extension"></a>Az Azure CLI-bővítmény telepítése

A ConnectedMachine-parancsok nem az Azure CLI részét képezi. Mielőtt az Azure CLI-t az Arc-kompatibilis kiszolgálók által felügyelt hibrid kiszolgálón lévő virtuálisgép-bővítmények kezelésére használna, be kell töltenie a ConnectedMachine bővítményt. Futtassa a következő parancsot a lekért parancshoz:

```azurecli
az extension add --name connectedmachine
```

## <a name="enable-extension"></a>Bővítmény engedélyezése

Ha engedélyezni szeretné a virtuálisgép-bővítményt az Arc-kompatibilis kiszolgálón, használja [az az connectedmachine extension create](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_create) parancsokat a , , , , és `--machine-name` `--extension-name` `--location` `--type` `settings` `--publisher` paraméterekkel.

Az alábbi példa engedélyezi a Log Analytics virtuálisgép-bővítményt egy Arc-kompatibilis kiszolgálón:

```azurecli
az connectedmachine extension create --machine-name "myMachineName" --name "OmsAgentForLinux or MicrosoftMonitoringAgent" --location "eastus" --settings '{\"workspaceId\":\"myWorkspaceId\"}' --protected-settings '{\"workspaceKey\":\"myWorkspaceKey\"}' --resource-group "myResourceGroup" --type-handler-version "1.13" --type "OmsAgentForLinux or MicrosoftMonitoringAgent" --publisher "Microsoft.EnterpriseCloud.Monitoring" 
```

Az alábbi példa engedélyezi az egyéni szkriptbővítményt egy Arc-kompatibilis kiszolgálón:

```azurecli
az connectedmachine extension create --machine-name "myMachineName" --name "CustomScriptExtension" --location "eastus" --type "CustomScriptExtension" --publisher "Microsoft.Compute" --settings "{\"commandToExecute\":\"powershell.exe -c \\\"Get-Process | Where-Object { $_.CPU -gt 10000 }\\\"\"}" --type-handler-version "1.10" --resource-group "myResourceGroup"
```

Az alábbi példa engedélyezi Key Vault virtuálisgép-bővítményt (előzetes verzió) egy Arc-kompatibilis kiszolgálón:

```azurecli
az connectedmachine extension create --resource-group "resourceGroupName" --machine-name "myMachineName" --location "regionName" --publisher "Microsoft.Azure.KeyVault" --type "KeyVaultForLinux or KeyVaultForWindows" --name "KeyVaultForLinux or KeyVaultForWindows" --settings '{"secretsManagementSettings": { "pollingIntervalInS": "60", "observedCertificates": ["observedCert1"] }, "authenticationSettings": { "msiEndpoint": "http://localhost:40342/metadata/identity" }}'
```

## <a name="list-extensions-installed"></a>Telepített bővítmények listája

Az Arc-kompatibilis kiszolgálón található virtuálisgép-bővítmények listáját az [az connectedmachine extension list](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_list) és paraméterekkel `--machine-name` `--resource-group` használhatja.

Példa:

```azurecli
az connectedmachine extension list --machine-name "myMachineName" --resource-group "myResourceGroup"
```

Alapértelmezés szerint az Azure CLI-parancsok kimenete JSON (JavaScript Object Notation). Az alapértelmezett kimenet listához vagy táblához való beállításához például használja [az az configure --output parancsokat.](/cli/azure/reference-index) A kimeneti formátumban egy alkalommal bármilyen parancshoz `--output` hozzáadható.

Az alábbi példa a parancs részleges JSON-kimenetét `az connectedmachine extension -list` mutatja be:

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

Ha el szeretne távolítani egy telepített virtuálisgép-bővítményt az Arc-kompatibilis kiszolgálón, használja [az az connectedmachine extension delete](/cli/azure/ext/connectedmachine/connectedmachine/extension#ext_connectedmachine_az_connectedmachine_extension_delete) parancsot a és a `--extension-name` `--machine-name` `--resource-group` paraméterrel.

A Linuxhoz használt Log Analytics virtuálisgép-bővítmény eltávolításához például futtassa a következő parancsot:

```azurecli
az connectedmachine extension delete --machine-name "myMachineName" --name "OmsAgentForLinux" --resource-group "myResourceGroup"
```

## <a name="next-steps"></a>Következő lépések

- A virtuálisgép-bővítményeket az Azure PowerShell [használatával](manage-vm-extensions-powershell.md)telepítheti, kezelheti és eltávolíthatja a [Azure Portal,](manage-vm-extensions-portal.md)vagy Azure Resource Manager [sablonokkal.](manage-vm-extensions-template.md)

- A hibaelhárítási információkat a Virtuálisgép-bővítmények [hibaelhárítása útmutatóban talál.](troubleshoot-vm-extensions.md)

- A parancsokkal kapcsolatos további információkért [tekintse](/cli/azure/ext/connectedmachine/connectedmachine/extension) át az Azure CLI virtuálisgép-bővítmény áttekintését ismertető cikket.
