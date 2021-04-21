---
title: Azure-beli szerepkör hozzárendelése adateléréshez az Azure CLI használatával
titleSuffix: Azure Storage
description: Megtudhatja, hogyan használhatja az Azure CLI-t arra, hogy engedélyeket rendeljen egy Azure Active Directory rendszerbiztonsági taghoz az Azure szerepköralapú hozzáférés-vezérlésével (Azure RBAC). Az Azure Storage támogatja a beépített és egyéni Azure-szerepköröket az Azure AD-n keresztüli hitelesítéshez.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 93d9a81ab75efe4ea38189a7280e041e545a2b7d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785324"
---
# <a name="use-azure-cli-to-assign-an-azure-role-for-access-to-blob-and-queue-data"></a>Azure CLI használata Azure-szerepkör hozzárendeléséhez a blob- és üzenetsoradatokhoz való hozzáféréshez

Azure Active Directory (Azure AD) az [Azure szerepköralapú hozzáférés-vezérlésével (Azure RBAC)](../../role-based-access-control/overview.md)engedélyezi a biztonságos erőforrásokhoz való hozzáférési jogosultságokat. Az Azure Storage olyan beépített Azure-szerepköröket határoz meg, amelyek a blob- vagy üzenetsoradatok eléréséhez használt általános engedélykészleteket foglalják magukban.

Ha egy Azure-szerepkör hozzá van rendelve egy Azure AD-rendszerbiztonsági taghoz, az Azure hozzáférést biztosít ezekhez az erőforrásokhoz a rendszerbiztonsági tag számára. A hozzáférés az előfizetés, az erőforráscsoport, a tárfiók vagy egy különálló tároló vagy üzenetsor szintjére terjedhet ki. Az Azure AD rendszerbiztonsági tag lehet felhasználó, csoport, alkalmazás-szolgáltatásnév vagy az Azure-erőforrások felügyelt [identitása.](../../active-directory/managed-identities-azure-resources/overview.md)

Ez a cikk azt ismerteti, hogyan használható az Azure CLI az Azure beépített szerepköreinek felsorolására és felhasználókhoz való hozzárendelésére. További információ az Azure CLI használatával kapcsolatban: [Azure Command-Line Interface (CLI).](/cli/azure)

## <a name="azure-roles-for-blobs-and-queues"></a>Azure-szerepkörök blobok és üzenetsorok számára

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Erőforrás hatókörének meghatározása

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-azure-roles"></a>Az elérhető Azure-szerepkörök felsorolása

Az elérhető beépített Azure-szerepkörök Azure CLI-ben való listához használja az [az role definition list parancsot:](/cli/azure/role/definition#az_role_definition_list)

```azurecli-interactive
az role definition list --out table
```

Megjelenik a beépített Azure Storage-adatszerepk szerepkörök listája, valamint az Azure egyéb beépített szerepkörei:

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-azure-role-to-a-security-principal"></a>Azure-szerepkör hozzárendelése rendszerbiztonsági taghoz

Ha Azure-szerepkört szeretne hozzárendelni egy rendszerbiztonsági taghoz, használja [az az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) parancsot. A parancs formátuma a hozzárendelés hatóköre alapján eltérő lehet. Az alábbi példák bemutatják, hogyan rendelhet hozzá egy szerepkört egy felhasználóhoz különböző hatókörökben, de ugyanezen paranccsal bármilyen rendszerbiztonsági taghoz hozzárendelhet egy szerepkört.

> [!IMPORTANT]
> Azure Storage-fiók létrehozásakor nem kap automatikusan engedélyeket az adatok Azure AD-n keresztüli eléréséhez. Az adateléréshez explicit módon azure RBAC-szerepkört kell hozzárendelnie saját magának. Hozzárendelheti az előfizetése, erőforráscsoportja, tárfiókja, tárolója vagy üzenetsora szintjén.
>
> Ha a tárfiók csak olvasható zárolással van zárolva, akkor Azure Resource Manager zárolás megakadályozza a tárfiókra vagy adattárolóra (blobtárolóra vagy üzenetsorra) vonatkozó Azure RBAC-szerepkörök hozzárendelését.

### <a name="container-scope"></a>Tároló hatóköre

Ha egy tárolóhoz hatókörrel tartozó szerepkört szeretne hozzárendelni, adjon meg egy sztringet, amely tartalmazza a tároló hatókörét a `--scope` paraméterhez. A tároló hatóköre a következő formában található:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>
```

Az alábbi példa hozzárendeli a **Storage-blobadatok** közreműködője szerepkört egy felhasználóhoz, amely a tároló szintjére terjed ki. A zárójelben lévő mintaértékeket és helyőrző értékeket cserélje le a saját értékeire:

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>"
```

### <a name="queue-scope"></a>Üzenetsor hatóköre

Ha egy üzenetsorhoz hatókörrel tartozó szerepkört szeretne hozzárendelni, adjon meg egy sztringet, amely az üzenetsor hatókörét tartalmazza a `--scope` paraméterhez. Az üzenetsor hatóköre a következő formában van:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>
```

Az alábbi példa hozzárendeli a **Storage Queue Data Contributor** szerepkört egy felhasználóhoz, amely az üzenetsor szintjére van beható hatókörben. A zárójelben lévő mintaértékeket és helyőrző értékeket cserélje le a saját értékeire:

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>"
```

### <a name="storage-account-scope"></a>Tárfiók hatóköre

A tárfiók hatókörű szerepkörének hozzárendeléshez adja meg a tárfiók-erőforrás hatókörét a `--scope` paraméterhez. A tárfiók hatóköre a következő formában lehet:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

Az alábbi példa bemutatja, hogyan rendelheti hozzá a **Storage-blobok** adatolvasó szerepkörét egy felhasználóhoz a tárfiók szintjén. A mintaértékeket cserélje le a saját értékeire: \

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>Erőforráscsoport hatóköre

Ha az erőforráscsoporthoz hatókörrel tartozó szerepkört szeretne hozzárendelni, adja meg az erőforráscsoport nevét vagy azonosítóját a `--resource-group` paraméterhez. Az alábbi példa hozzárendeli a **Storage-üzenetsor** adatolvasó szerepkörét egy felhasználóhoz az erőforráscsoport szintjén. Mindenképpen cserélje le a zárójelben lévő mintaértékeket és helyőrzőket a saját értékeire:

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Reader" \
    --assignee <email> \
    --resource-group <resource-group>
```

### <a name="subscription-scope"></a>Előfizetés hatóköre

Ha az előfizetéshez hatókörrel tartozó szerepkört szeretne hozzárendelni, adja meg az előfizetés hatókörét a `--scope` paraméterhez. Az előfizetés hatóköre a következő formában lehet:

```
/subscriptions/<subscription>
```

Az alábbi példa bemutatja, hogyan rendelheti hozzá a **Storage-blobok** adatolvasó szerepkörét egy felhasználóhoz a tárfiók szintjén. A mintaértékeket cserélje le a saját értékeire: 

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>Következő lépések

- [Azure-beli szerepkör-hozzárendelések hozzáadása vagy eltávolítása a Azure PowerShell modullal](../../role-based-access-control/role-assignments-powershell.md)
- [A Azure PowerShell modullal hozzárendelhet egy Azure-szerepkört a blob- és üzenetsoradatok eléréséhez](storage-auth-aad-rbac-powershell.md)
- [Az Azure Portal használata a blob- és üzenetsoradatok elérésére szolgáló Azure-szerepkör hozzárendeléséhez](storage-auth-aad-rbac-portal.md)
