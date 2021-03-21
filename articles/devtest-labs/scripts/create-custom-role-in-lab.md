---
title: PowerShell – egyéni szerepkör létrehozása a laborban Azure DevTest Labs
description: Ez a cikk egy Azure PowerShell parancsfájlt tartalmaz, amely egyéni szerepkört hoz létre egy Azure DevTest Labs tesztkörnyezetben.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 3757d6c16a762a0f76f4718f117da93322af074c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96021197"
---
# <a name="use-powershell-to-create-a-custom-role-in-a-lab-in-azure-devtest-labs"></a>Egyéni szerepkör létrehozása a PowerShell használatával Azure DevTest Labs

Ez a minta PowerShell-szkript létrehoz egy egyéni szerepkört, amelyet a Azure DevTest Labsban található laborban kell használni. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Előfeltételek
* **Egy labor**. A parancsfájlhoz meglévő labor szükséges. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/create-custom-role-in-lab/create-custom-role-in-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja: 

| Parancs | Jegyzetek |
|---|---|
| [Get-AzProviderOperation](/powershell/module/az.resources/get-azprovideroperation) | Lekéri egy Azure-erőforrás-szolgáltató műveleteit, amelyek az Azure RBAC segítségével biztonságossá tehetők. |
| [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) | Felsorolja a hozzárendeléshez rendelkezésre álló összes Azure-szerepkört. |
| [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) | Létrehoz egy egyéni szerepkört. |

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/).

További Azure Lab Services PowerShell-szkriptek is találhatók a [Azure Lab Services PowerShell-mintákban](../samples-powershell.md).
