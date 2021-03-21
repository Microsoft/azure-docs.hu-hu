---
title: PowerShell – külső felhasználó hozzáadása laborhoz Azure DevTest Labs
description: Ez a cikk egy Azure PowerShell parancsfájlt tartalmaz, amely egy külső felhasználót ad hozzá egy laborhoz Azure DevTest Labs.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 76fbb5e4d7f5db39a0aadba9098ebf1064b3cda5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96022394"
---
# <a name="use-powershell-to-add-an-external-user-to-a-lab-in-azure-devtest-labs"></a>Külső felhasználó hozzáadása egy laborhoz a PowerShell használatával Azure DevTest Labs

Ez a PowerShell-parancsfájl külső felhasználót hoz létre egy laborban Azure DevTest Labsban. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Előfeltételek
* **Egy labor**. A parancsfájlhoz meglévő labor szükséges. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-external-user-to-lab/add-external-user-to-custom-lab.ps1 "Add external user to a lab")]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja: 

| Parancs | Jegyzetek |
|---|---|
| [Get-AzADUser](/powershell/module/az.resources/get-azaduser) | Újrapróbálkozik a felhasználói objektumot az Azure Active Directoryból. |
| [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) | A megadott szerepkört a megadott hatókörhöz rendeli a megadott rendszerbiztonsági tag számára. |

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/).

További Azure Lab Services PowerShell-szkriptek is találhatók a [Azure Lab Services PowerShell-mintákban](../samples-powershell.md).
