---
title: Felügyelt identitás hozzáférésének hozzárendelése erőforráshoz a PowerShell használatával – Azure AD
description: Részletes útmutató felügyelt identitások egy erőforráshoz való hozzárendeléshez, hozzáférés egy másik erőforráshoz a PowerShell használatával.
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
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fc5df641f27f8d604f7b5647736128856a3ecd51
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764368"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-powershell"></a>Felügyelt identitás hozzáférésének hozzárendelése erőforráshoz a PowerShell használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Miután konfigurált egy Azure-erőforrást egy felügyelt identitással, hozzáférést adhat a felügyelt identitásnak egy másik erőforráshoz, mint bármely más rendszerbiztonsági tag. Ez a példa bemutatja, hogyan adhat hozzáférést egy Azure-beli virtuális gép felügyelt identitásának egy Azure Storage-fiókhoz a PowerShell használatával.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitását, tekintse meg az [áttekintés szakaszt.](overview.md) Mindenképpen tekintse át a rendszer által hozzárendelt és a felhasználó által hozzárendelt felügyelt **[identitások közötti különbséget.](overview.md#managed-identity-types)**
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- A példaszk szkriptek futtatásához két lehetőség áll rendelkezésre:
    - Használja [a Azure Cloud Shell,](../../cloud-shell/overview.md)amelyet a kódblokkok jobb felső sarkában található **Try It (Próbálja** ki) gombbal nyithat meg.
    - Futtatassa helyileg a szkripteket a Azure PowerShell [legújabb](/powershell/azure/install-az-ps)verziójának telepítésével, majd jelentkezzen be az Azure-ba a `Connect-AzAccount` használatával. 

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Felügyelt identitás hozzáférésének hozzárendelése egy másik erőforráshoz az Azure RBAC használatával

1. Felügyelt identitás engedélyezése egy Azure-erőforráson, például egy [Azure-beli virtuális gépen.](qs-configure-powershell-windows-vm.md)

1. Ebben a példában hozzáférést biztosítunk egy Azure-beli virtuális gépnek egy tárfiókhoz. Először a [Get-AzVM](/powershell/module/az.compute/get-azvm) használatával kérjük le a nevű virtuális gép szolgáltatásnevét, amely a felügyelt identitás `myVM` engedélyezésekor jött létre. Ezután a [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) használatával  adjon hozzáférést a virtuális gép olvasója számára a nevű tárfiókhoz: `myStorageAcct`

    ```azurepowershell-interactive
    $spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="next-steps"></a>Következő lépések

- [Az Azure-erőforrások felügyelt identitásának áttekintése](overview.md)
- A felügyelt identitás Azure-beli virtuális gépen való engedélyezéséről lásd: Azure-beli virtuális gépen található Azure-erőforrások felügyelt identitásának konfigurálása [a PowerShell használatával.](qs-configure-powershell-windows-vm.md)
