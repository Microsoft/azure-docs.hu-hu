---
title: PowerShell – Piactéri rendszerkép hozzáadása laborhoz Azure DevTest Labs
description: Ez a PowerShell-parancsfájl egy piactér-rendszerképet hoz létre Azure DevTest Labsban található laborba.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 543f20af270769dd16e4a1ecf6ee93e9259cdfd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96022276"
---
# <a name="use-powershell-to-add-a-marketplace-image-to-a-lab-in-azure-devtest-labs"></a>Piactéri rendszerkép hozzáadása a Azure DevTest Labshoz a PowerShell használatával

Ez a minta PowerShell-parancsfájl egy Piactéri rendszerképet hoz létre Azure DevTest Labsban található laborba. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Előfeltételek
* **Egy labor**. A parancsfájlhoz meglévő labor szükséges. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/add-marketplace-images-to-lab/add-marketplace-images-to-lab.ps1 "Add marketplace images to a lab")]

## <a name="script-explanation"></a>Szkript ismertetése

Ez a szkript a következő parancsokat használja: 

| Parancs | Jegyzetek |
|---|---|
| Find-AzResource | Erőforrások keresése megadott paraméterek alapján. |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Erőforrások beolvasása. |
| [Set-AzResource](/powershell/module/az.resources/set-azresource) | Módosítja az erőforrást. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | Erőforrásokat hozhat létre. |

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/).

További Azure Lab Services PowerShell-szkriptek is találhatók a [Azure Lab Services PowerShell-mintákban](../samples-powershell.md).
