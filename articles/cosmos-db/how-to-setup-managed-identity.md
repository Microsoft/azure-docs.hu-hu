---
title: Felügyelt identitások konfigurálása az Azure AD-vel a Azure Cosmos DB-fiókjához
description: Ismerje meg, hogyan konfigurálhatja a felügyelt identitásokat a Azure Cosmos DB-fiókhoz Azure Active Directory
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/02/2021
ms.author: thweiss
ms.openlocfilehash: 30efaed09a400611861bdd3adeae1f650054b405
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231427"
---
# <a name="configure-managed-identities-with-azure-active-directory-for-your-azure-cosmos-db-account"></a>Felügyelt identitások konfigurálása a Azure Cosmos DB-fiókhoz Azure Active Directory
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Az Azure-erőforrások felügyelt identitásai Azure-szolgáltatásokat biztosítanak a Azure Active Directory automatikusan felügyelt identitással. Ez a cikk bemutatja, hogyan hozhat létre felügyelt identitást Azure Cosmos DB fiókokhoz.

> [!NOTE]
> A Azure Cosmos DB jelenleg csak a rendszer által hozzárendelt felügyelt identitásokat támogatja.

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg a [Mi az Azure-erőforrások felügyelt identitásai?](../active-directory/managed-identities-azure-resources/overview.md)című témakört. A felügyelt identitások típusairól a [felügyelt identitások típusai](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)című témakörben olvashat bővebben.
- A felügyelt identitások beállításához a fióknak rendelkeznie kell a [DocumentDB-fiók közreműködői szerepkörrel](../role-based-access-control/built-in-roles.md#documentdb-account-contributor).

## <a name="add-a-system-assigned-identity"></a>Rendszerhez rendelt identitás hozzáadása

### <a name="using-an-azure-resource-manager-arm-template"></a>Azure Resource Manager-(ARM-) sablon használata

> [!IMPORTANT]
> `apiVersion`Felügyelt identitások használata esetén ügyeljen arra, hogy a `2021-03-15` vagy magasabb szintű legyen.

Ha a rendszer által hozzárendelt identitást szeretné engedélyezni egy új vagy meglévő Azure Cosmos DB-fiókon, vegye fel a következő tulajdonságot az erőforrás-definícióba:

```json
"identity": {
    "type": "SystemAssigned"
}
```

Az `resources` ARM-sablon szakaszának a következőhöz hasonlóan kell kinéznie:

```json
"resources": [
    {
        "type": " Microsoft.DocumentDB/databaseAccounts",
        "identity": {
            "type": "SystemAssigned"
        },
        // ...
    },
    // ...
 ]
```

A Azure Cosmos DB fiók létrehozása vagy frissítése után a következő tulajdonság jelenik meg:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<azure-ad-tenant-id>",
    "principalId": "<azure-ad-principal-id>"
}
```

## <a name="next-steps"></a>Következő lépések

- További információ az [Azure-erőforrások felügyelt identitásáról](../active-directory/managed-identities-azure-resources/overview.md)
- További információ az [ügyfél által felügyelt kulcsokról Azure Cosmos db](how-to-setup-cmk.md)
