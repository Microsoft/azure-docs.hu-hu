---
title: Azure Policy-minta – Transzparens adattitkosítás naplózása az SQL Database-hez
description: Ez a mintaszabályzat naplózza, ha az SQL Database-adatbázisban nincs engedélyezve a transzparens adattitkosítás.
services: azure-policy
author: DCtheGeek
manager: carmonm
ms.service: azure-policy
ms.topic: sample
ms.date: 10/29/2018
ms.author: dacoulte
ms.custom: mvc
ms.openlocfilehash: d7e8571c0b8301da953d84893ac48934a2392d55
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230003"
---
# <a name="audit-sql-database-encryption"></a>SQL Database-adatbázis titkosításának naplózása

Ez a beépített szabályzat naplózza, ha az SQL Database-adatbázisban nincs engedélyezve a transzparens adattitkosítás.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>Példasablon

[!code-json[main](../../../../policy-templates/samples/SQL/audit-sql-db-tde-status/azurepolicy.json "Audit TDE for SQL Database")]

A sablon az [Azure Portal](#deploy-with-the-portal), a [PowerShell](#deploy-with-powershell) vagy az [Azure CLI](#deploy-with-azure-cli) használatával helyezhető üzembe. A beépített szabályzat lekéréséhez használja a következő azonosítót: `17k78e20-9358-41c9-923c-fb736d382a12`.

## <a name="deploy-with-the-portal"></a>Üzembe helyezés a portállal

Szabályzat hozzárendelésekor válassza a **Transzparens adattitkosítás állapotának naplózása** lehetőséget az elérhető beépített definíciók közül.

## <a name="deploy-with-powershell"></a>Üzembe helyezés a PowerShell-lel

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh.md)]

```azurepowershell-interactive
$definition = Get-AzureRmPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/17k78e20-9358-41c9-923c-fb736d382a12

New-AzureRmPolicyAssignment -name "SQL TDE Audit" -PolicyDefinition $definition -Scope <scope>
```

### <a name="clean-up-powershell-deployment"></a>PowerShell-üzembehelyezés eltávolítása

A szabályzat-hozzárendelés törléséhez futtassa a következő parancsot.

```azurepowershell-interactive
Remove-AzureRmPolicyAssignment -Name "SQL TDE Audit" -Scope <scope>
```

## <a name="deploy-with-azure-cli"></a>Üzembe helyezés az Azure CLI-vel

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy assignment create --scope <scope> --name "SQL TDE Audit" --policy 17k78e20-9358-41c9-923c-fb736d382a12
```

### <a name="clean-up-azure-cli-deployment"></a>Az Azure CLI üzemelő példányának eltávolítása

A szabályzat-hozzárendelés törléséhez futtassa a következő parancsot.

```azurecli-interactive
az policy assignment delete --name "SQL TDE Audit" --resource-group myResourceGroup
```

## <a name="next-steps"></a>További lépések

- További példákat [Az Azure Policy-minták](index.md) oldalon talál.