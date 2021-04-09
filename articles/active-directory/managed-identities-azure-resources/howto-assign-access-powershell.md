---
title: Felügyelt identitás-hozzáférés kiosztása egy erőforráshoz a PowerShell használatával – Azure AD
description: Részletes útmutató felügyelt identitások egy erőforráshoz való hozzárendeléséhez, egy másik erőforráshoz való hozzáféréshez a PowerShell használatával.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf1a7a608df7a2b752d9a6bed52a4024fd776c5f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97592500"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-powershell"></a>Felügyelt identitás-hozzáférés kiosztása egy erőforráshoz a PowerShell használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Miután konfigurált egy Azure-erőforrást egy felügyelt identitással, megadhatja a felügyelt identitás hozzáférését egy másik erőforráshoz, ugyanúgy, mint a rendszerbiztonsági tag. Ebből a példából megtudhatja, hogyan biztosíthatja az Azure-beli virtuális gépek felügyelt identitását egy Azure Storage-fiókhoz a PowerShell használatával.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [Áttekintés szakaszt](overview.md). **Mindenképpen tekintse át a [rendszer által hozzárendelt és a felhasználó által hozzárendelt felügyelt identitás közötti különbséget](overview.md#managed-identity-types)**.
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- A példaként szolgáló szkriptek futtatásához két lehetőség közül választhat:
    - Használja a [Azure Cloud shellt](../../cloud-shell/overview.md), amelyet a kódrészletek jobb felső sarkában található **kipróbálás** gomb használatával nyithat meg.
    - Futtassa helyileg a parancsfájlokat a [Azure PowerShell](/powershell/azure/install-az-ps)legújabb verziójának telepítésével, majd jelentkezzen be az Azure-ba a használatával `Connect-AzAccount` . 

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Felügyelt identitás-hozzáférés kiosztása az Azure RBAC használatával egy másik erőforráshoz

1. Felügyelt identitás engedélyezése Azure-erőforrásokon, [például egy Azure-beli virtuális gépen](qs-configure-powershell-windows-vm.md).

1. Ebben a példában egy Azure-beli virtuális gép hozzáférést biztosítunk egy Storage-fiókhoz. Először a [Get-AzVM](/powershell/module/az.compute/get-azvm) használatával szerezheti be a nevű virtuális gép egyszerű szolgáltatását `myVM` , amely akkor jött létre, amikor engedélyezte a felügyelt identitást. Ezután a [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) használatával adja meg a virtuális gép **olvasójának** a következő nevű Storage-fiókhoz való hozzáférést `myStorageAcct` :

    ```azurepowershell-interactive
    $spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="next-steps"></a>Következő lépések

- [Felügyelt identitás az Azure-erőforrásokhoz – áttekintés](overview.md)
- A felügyelt identitás Azure-beli virtuális gépen való engedélyezésével kapcsolatban lásd: [felügyelt identitások konfigurálása](qs-configure-powershell-windows-vm.md)Azure-beli virtuális gépeken a PowerShell használatával.
