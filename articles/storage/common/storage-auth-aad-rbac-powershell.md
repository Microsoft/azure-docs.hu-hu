---
title: Azure-szerepkör kiosztása az adateléréshez a PowerShell használatával
titleSuffix: Azure Storage
description: Megtudhatja, hogyan rendelhet hozzá engedélyeket egy Azure Active Directory rendszerbiztonsági tag számára az Azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC) a Azure PowerShell modul használatával. Az Azure Storage támogatja az Azure AD-n keresztül történő hitelesítéshez használható beépített és egyéni Azure-szerepköröket.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9e4fea52f56da9f2e84746daf0121df0b4355411
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100375952"
---
# <a name="use-powershell-to-assign-an-azure-role-for-access-to-blob-and-queue-data"></a>A PowerShell használata Azure-szerepkör hozzárendeléséhez a blob-és üzenetsor-adateléréshez

Azure Active Directory (Azure AD) az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/overview.md)segítségével engedélyezi a hozzáférési jogokat a biztonságos erőforrásokhoz. Az Azure Storage egy beépített Azure-beli szerepkört határoz meg, amely magában foglalja a tárolók és a várólisták eléréséhez használt engedélyek közös készletét.

Ha az Azure-szerepköröket egy Azure AD-rendszerbiztonsági tag számára rendeli hozzá, az Azure hozzáférést biztosít az adott rendszerbiztonsági tag erőforrásaihoz. A hozzáférés hatóköre az előfizetés, az erőforráscsoport, a Storage-fiók vagy egy adott tároló vagy várólista szintjére is kiterjed. Az Azure AD rendszerbiztonsági tag lehet egy felhasználó, egy csoport, egy egyszerű alkalmazás vagy egy [felügyelt identitás az Azure-erőforrásokhoz](../../active-directory/managed-identities-azure-resources/overview.md).

Ez a cikk azt ismerteti, hogyan használható a Azure PowerShell az Azure beépített szerepköreinek listázásához és a felhasználókhoz való hozzárendeléséhez. További információ a Azure PowerShell használatáról: [Azure PowerShell áttekintése](/powershell/azure/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="azure-roles-for-blobs-and-queues"></a>Blobok és várólisták Azure-szerepkörei

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Erőforrás hatókörének meghatározása

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-azure-roles"></a>Elérhető Azure-szerepkörök listázása

A Azure PowerShell használatával elérhető Azure beépített szerepkörök listázásához használja a [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) parancsot:

```powershell
Get-AzRoleDefinition | FT Name, Description
```

Megjelenik az Azure Storage beépített adatszerepkörei, valamint az Azure egyéb beépített szerepkörei:

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-azure-role-to-a-security-principal"></a>Azure-szerepkör kiosztása rendszerbiztonsági tag számára

Ha Azure-szerepkört szeretne hozzárendelni egy rendszerbiztonsági tag számára, használja a [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) parancsot. A parancs formátuma eltérő lehet a hozzárendelés hatóköre alapján. A parancs futtatásához hozzá kell rendelnie a tulajdonosi vagy közreműködői szerepkört a megfelelő hatókörhöz. Az alábbi példák bemutatják, hogyan rendeljen hozzá egy szerepkört egy felhasználóhoz különböző hatókörökben, de ugyanazt a parancsot használhatja egy szerepkör hozzárendelésére bármely rendszerbiztonsági tag számára.

> [!IMPORTANT]
> Azure Storage-fiók létrehozásakor nem rendel automatikusan engedélyeket az Azure AD-n keresztüli adateléréshez. Az adathozzáféréshez explicit módon hozzá kell rendelnie egy Azure RBAC-szerepkört. Az előfizetés, az erőforráscsoport, a Storage-fiók, a tároló vagy a várólista szintjén rendelhető hozzá.
>
> Ha a Storage-fiók zárolva van egy Azure Resource Manager írásvédett zárolással, akkor a zárolás megakadályozza a Storage-fiókra vagy egy adattárolóra (blob-tárolóra vagy-várólistára) kiterjedő Azure RBAC-szerepkörök hozzárendelését.

### <a name="container-scope"></a>Tároló hatóköre

Egy tárolóhoz hatókörrel rendelkező szerepkör hozzárendeléséhez adjon meg egy karakterláncot, amely a paraméterhez tartozó tároló hatókörét tartalmazza `--scope` . A tároló hatóköre a következő formában van:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container-name>
```

Az alábbi példa a **Storage blob-adatközreműködői** szerepkört rendeli hozzá egy felhasználóhoz, amely egy *minta-tároló* nevű tárolóra terjed ki. Ügyeljen rá, hogy a minták értékeit és a zárójelben lévő helyőrző értékeket a saját értékeire cserélje le: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/sample-container"
```

### <a name="queue-scope"></a>Várólista hatóköre

Ha egy szerepkör hatókörét szeretné hozzárendelni egy várólistához, adjon meg egy karakterláncot, amely a paraméterhez tartozó várólista hatókörét tartalmazza `--scope` . Az üzenetsor hatóköre a következő formában van:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue-name>
```

Az alábbi példa a **Storage üzenetsor-adatközreműködői** szerepkört rendeli hozzá egy felhasználóhoz, hatókörét egy *minta-várólista* nevű várólistára. Ügyeljen rá, hogy a minták értékeit és a zárójelben lévő helyőrző értékeket a saját értékeire cserélje le: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Contributor" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/sample-queue"
```

### <a name="storage-account-scope"></a>Storage-fiók hatóköre

A Storage-fiókhoz tartozó szerepkör hozzárendeléséhez adja meg a Storage-fiók erőforrásának hatókörét a `--scope` paraméterhez. A Storage-fiók hatóköre a következő formában van:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

Az alábbi példa azt szemlélteti, hogyan lehet a Storage- **Adatolvasói** szerepkört egy felhasználóra kiterjeszteni a Storage-fiók szintjén. Győződjön meg arról, hogy a minták értékeit a saját értékeire cseréli: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>/resourceGroups/sample-resource-group/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>Erőforráscsoport hatóköre

Az erőforráscsoport hatókörének hozzárendeléséhez adja meg az erőforráscsoport nevét vagy AZONOSÍTÓját a `--resource-group` paraméterhez. Az alábbi példa a **Storage üzenetsor-Adatolvasói** szerepkört rendeli hozzá egy felhasználóhoz az erőforráscsoport szintjén. Ügyeljen rá, hogy a minták értékeit és a helyőrző értékeket zárójelben adja meg a saját értékeivel: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Queue Data Reader" `
    -ResourceGroupName "sample-resource-group"
```

### <a name="subscription-scope"></a>Előfizetés hatóköre

Ahhoz, hogy az előfizetéshez hatókört rendeljen, adja meg az előfizetés hatókörét a `--scope` paraméterhez. Az előfizetés hatóköre a következő formában van:

```
/subscriptions/<subscription>
```

Az alábbi példa bemutatja, hogyan rendelhető hozzá a **Storage blob-Adatolvasói** szerepkör a felhasználóhoz a Storage-fiók szintjén. Győződjön meg arról, hogy a minták értékeit a saját értékeire cseréli: 

```powershell
New-AzRoleAssignment -SignInName <email> `
    -RoleDefinitionName "Storage Blob Data Reader" `
    -Scope  "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>Következő lépések

- [Azure-beli szerepkör-hozzárendelések hozzáadása vagy eltávolítása a Azure PowerShell modul használatával](../../role-based-access-control/role-assignments-powershell.md)
- [Az Azure CLI-vel hozzárendelhet egy Azure-szerepkört a blob-és üzenetsor-adat eléréséhez](storage-auth-aad-rbac-cli.md)
- [Az Azure Portal használata a blob- és üzenetsoradatok elérésére szolgáló Azure-szerepkör hozzárendeléséhez](storage-auth-aad-rbac-portal.md)
