---
title: 'PowerShell-parancsfájl: engedélyezett virtuálisgép-méretek beállítása a Azure Lab Servicesban | Microsoft Docs'
description: Ez a cikk egy PowerShell-parancsfájlt tartalmaz, amely az engedélyezett virtuális gépek (VM) méretét állítja be Azure Lab Servicesban.
ms.devlang: azurecli
ms.topic: sample
ms.date: 08/11/2020
ms.openlocfilehash: 476b86b7c577db17efc39dbac64a527432c916b6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95998132"
---
# <a name="use-powershell-to-set-allowed-vm-sizes-in-azure-lab-services"></a>Az engedélyezett virtuálisgép-méretek beállítása a PowerShell használatával Azure Lab Services

Ez a PowerShell-parancsfájl az engedélyezett virtuális gépek (VM) méretét állítja be Azure Lab Servicesban.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Előfeltételek
* **Egy labor**. A parancsfájlhoz meglévő labor szükséges. 

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../powershell_scripts/devtest-lab/set-allowed-vm-sizes-in-lab/set-allowed-vm-sizes-in-lab.ps1 "Add external user to a lab")]

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
