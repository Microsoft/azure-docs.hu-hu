---
title: Felügyelt identitás hozzáférésének hozzárendelése erőforráshoz az Azure CLI használatával – Azure AD
description: Részletes útmutató felügyelt identitás hozzárendeléshez egy erőforráshoz, hozzáférés egy másik erőforráshoz az Azure CLI használatával.
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
ms.date: 01/29/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1e1fa22cc36df00b098274002b6bd444be4140ff
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783286"
---
# <a name="assign-a-managed-identity-access-to-a-resource-using-azure-cli"></a>Felügyelt identitás hozzáférésének hozzárendelése erőforráshoz az Azure CLI használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Miután konfigurált egy Azure-erőforrást egy felügyelt identitással, hozzáférést adhat a felügyelt identitásnak egy másik erőforráshoz, akárcsak bármely más rendszerbiztonsági tag. Ez a példa bemutatja, hogyan adhat hozzáférést egy Azure-beli virtuális gépnek vagy virtuálisgép-méretezési készlet felügyelt identitásának egy Azure Storage-fiókhoz az Azure CLI használatával.

Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitását, lásd: Mik azok az [Azure-erőforrások felügyelt identitások?](overview.md). A rendszer által hozzárendelt és a felhasználó által hozzárendelt felügyelt identitástípusokkal kapcsolatos további információkért lásd: [Felügyelt identitástípusok.](overview.md#managed-identity-types)

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Felügyelt identitás hozzáférésének hozzárendelése egy másik erőforráshoz az Azure RBAC használatával

Miután engedélyezte a felügyelt identitást egy Azure-erőforráson, például [egy Azure-beli](qs-configure-cli-windows-vm.md) virtuális gépen vagy egy Azure-beli virtuálisgép-méretezési [csoporton:](qs-configure-cli-windows-vmss.md) 

1. Ebben a példában hozzáférést biztosítunk egy Azure-beli virtuális gépnek egy tárfiókhoz. Először az [az resource list](/cli/azure/resource/#az_resource_list) használatával kérjük le a myVM nevű virtuális gép szolgáltatásnevét:

   ```azurecli-interactive
   spID=$(az resource list -n myVM --query [*].identity.principalId --out tsv)
   ```
   Azure-beli virtuálisgép-méretezési készletben a parancs ugyanaz, kivéve itt, a "DevTestVMSS" nevű virtuálisgép-méretezési készlet szolgáltatásnevét:
   
   ```azurecli-interactive
   spID=$(az resource list -n DevTestVMSS --query [*].identity.principalId --out tsv)
   ```

1. Ha már rendelkezik a szolgáltatásnév azonosítójával, az [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) használatával adjon "Olvasó" hozzáférést a virtuális gépnek vagy a virtuálisgép-méretezési csoportnak a "myStorageAcct" nevű tárfiókhoz:

   ```azurecli-interactive
   az role assignment create --assignee $spID --role 'Reader' --scope /subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/myStorageAcct
   ```

## <a name="next-steps"></a>Következő lépések

- [Az Azure-erőforrások felügyelt identitásának áttekintése](overview.md)
- Ha engedélyezni szeretné a felügyelt identitást egy Azure-beli virtuális gépen, tekintse meg az Azure-beli virtuális gépen található Azure-erőforrások felügyelt identitásának [az Azure CLI használatával való konfigurálásával kapcsolatban lásd:](qs-configure-cli-windows-vm.md).
- Ha engedélyezni szeretné a felügyelt identitást egy Azure-beli virtuálisgép-méretezési készleten, lásd: Azure-erőforrások felügyelt identitásának konfigurálása virtuálisgép-méretezési készleten [az Azure CLI használatával.](qs-configure-cli-windows-vmss.md)
