---
title: Egyéni szerepkörök az Azure-erőforrások |} A Microsoft Docs
description: Megtudhatja, hogyan hozhat létre egyéni szerepköröket a szerepköralapú hozzáférés-vezérléssel (RBAC) részletes hozzáférés-vezérlést Azure-erőforrások számára.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/07/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8b628086a67f1d76357fda4f753350b6411b8f15
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273714"
---
# <a name="custom-roles-for-azure-resources"></a>Egyéni szerepkörök az Azure-erőforrásokhoz

Ha a [beépített szerepkörök az Azure-erőforrások](built-in-roles.md) nem felelnek meg a szervezet konkrét igényeinek, saját egyéni szerepköröket is létrehozhat. Csakúgy, mint a beépített szerepkörök felhasználók, csoportok és szolgáltatásnevek előfizetés, erőforráscsoport és erőforrás-hatókörök is egyéni szerepkörök hozzárendelése.

Egyéni szerepkörök az Azure Active Directory (Azure AD) könyvtárban tárolja, és előfizetések között megoszthatók. Minden könyvtár lehet akár **5000** egyéni szerepkörök. (A speciális felhőkben, mint az Azure Government, az Azure Germany és az Azure China 21Vianet, az egyéni szerepkörök száma 2000-re van korlátozva.) Egyéni szerepkörök az Azure PowerShell, az Azure CLI vagy a REST API használatával is létrehozható.

## <a name="custom-role-example"></a>Egyéni szerepkör-példa

Az alábbiakban látható egy egyéni szerepkör néz JSON formátumban jelenik meg. Az egyéni szerepkör figyelése és a virtuális gépek is használható.

```json
{
  "Name": "Virtual Machine Operator",
  "Id": "88888888-8888-8888-8888-888888888888",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}",
    "/subscriptions/{subscriptionId3}"
  ]
}
```

Amikor létrehoz egy egyéni biztonsági szerepkört, az Azure Portalon egy narancssárga erőforrás ikon jelenik meg.

![Egyéni szerepkör ikon](./media/custom-roles/roles-custom-role-icon.png)

## <a name="steps-to-create-a-custom-role"></a>Egyéni szerepkör létrehozásának lépései

1. Annak eldöntése, hogyan szeretné az egyéni szerepkör létrehozása

    Egyéni szerepkörök használatával hozhat létre [Azure PowerShell-lel](custom-roles-powershell.md), [Azure CLI-vel](custom-roles-cli.md), vagy a [REST API-val](custom-roles-rest.md).

1. Határozza meg a szükséges engedélyekkel

    Amikor létrehoz egy egyéni biztonsági szerepkört, az erőforrás-szolgáltatói műveletek elérhető engedélyek meghatározásához ismernie kell. A műveletek listájának megtekintéséhez lásd a [Azure Resource Manager erőforrás-szolgáltatói műveletek](resource-provider-operations.md). A művelet hozzáadja a `Actions` vagy `NotActions` tulajdonságait a [szerepkör-definíció](role-definitions.md). Ha az üzemeltetés, hozzáadja ezeket az a `DataActions` vagy `NotDataActions` tulajdonságait.

1. Az egyéni szerepkör létrehozása

    Általában, indítsa el a meglévő beépített szerepkör, és módosítsa az igényeinek. Akkor használja a [New-AzRoleDefinition](/powershell/module/az.resources/new-azroledefinition) vagy [az szerepkör-definíció létrehozására](/cli/azure/role/definition#az-role-definition-create) parancsokat az egyéni szerepkör létrehozása. Egyéni szerepkör létrehozása, rendelkeznie kell a `Microsoft.Authorization/roleDefinitions/write` engedély az összes `AssignableScopes`, mint például [tulajdonosa](built-in-roles.md#owner) vagy [felhasználói hozzáférés rendszergazdája](built-in-roles.md#user-access-administrator).

1. Az egyéni szerepkör tesztelése

    Miután az egyéni szerepkör, győződjön meg arról, hogy várakozásainak megfelelően működik-e, hogy tesztelni kell. Ha később módosításokra lesz szüksége, frissítheti az egyéni szerepkör.

Egyéni szerepkör létrehozásával egy részletes útmutató: [oktatóanyag: Hozzon létre egy egyéni szerepkört, az Azure PowerShell-lel](tutorial-custom-role-powershell.md) vagy [oktatóanyag: Azure CLI-vel egyéni szerepkör létrehozása](tutorial-custom-role-cli.md).

## <a name="custom-role-properties"></a>Egyéni szerepkör tulajdonságai

Egy egyéni biztonsági szerepkört a következő tulajdonságokkal rendelkezik.

| Tulajdonság | Kötelező | Típus | Leírás |
| --- | --- | --- | --- |
| `Name` | Igen | String | Az egyéni szerepkör megjelenített neve. Előfizetés-szintű erőforrás pedig a szerepkör-definíció egy szerepkör-definíció, amely ugyanazt az Azure AD-címtár megosztása több előfizetés is használható. Ezt a megjelenítési nevet a hatókörben, az Azure AD-címtár egyedinek kell lennie. Betűket, számokat, szóközöket és speciális karaktereket tartalmazhatnak. Karakterek maximális száma 128. |
| `Id` | Igen | String | Az egyéni szerepkör egyedi azonosítója. Az Azure PowerShell és az Azure CLI-vel Ez az azonosító automatikusan jön létre egy új szerepkör létrehozásakor. |
| `IsCustom` | Igen | String | Azt jelzi, hogy ez egy egyéni biztonsági szerepkört. Állítsa be `true` az egyéni szerepkörökhöz. |
| `Description` | Igen | String | Az egyéni szerepkör leírása. Betűket, számokat, szóközöket és speciális karaktereket tartalmazhatnak. Karakterek maximális száma: 1024. |
| `Actions` | Igen | String] | Karakterláncok tömbje, amely meghatározza a szerepkör lehetővé teszi, hogy a végrehajtandó felügyeleti műveleteket. További információkért lásd: [műveletek](role-definitions.md#actions). |
| `NotActions` | Nem | String] | Karakterláncok tömbje, amely meghatározza a felügyeleti műveleteket, amelyek ki vannak zárva az engedélyezett a `Actions`. További információkért lásd: [NotActions](role-definitions.md#notactions). |
| `DataActions` | Nem | String] | Karakterláncok tömbje, amely meghatározza, mely adatműveletekre, amely a szerepkör lehetővé teszi, hogy végrehajtani ahhoz, hogy az adatok az objektumon belül. További információkért lásd: [DataActions](role-definitions.md#dataactions). |
| `NotDataActions` | Nem | String] | Karakterláncok tömbje, amely a kizárt Adatműveletek meghatározza az engedélyezett a `DataActions`. További információkért lásd: [NotDataActions](role-definitions.md#notdataactions). |
| `AssignableScopes` | Igen | String] | Karakterláncok tömbje, amely meghatározza, hogy az egyéni szerepkör-hozzárendelés érhető a hatókörök. Egyéni szerepkörök esetében jelenleg nem lehet beállítani `AssignableScopes` a legfelső szintű hatókörhöz (`"/"`) vagy egy felügyeleti csoport hatóköre. További információkért lásd: [AssignableScopes](role-definitions.md#assignablescopes) és [az erőforrások rendszerezéséhez az Azure felügyeleti csoportok](../governance/management-groups/index.md#custom-rbac-role-definition-and-assignment). |

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Ki is létrehozása, törlése, frissítése, vagy egy egyéni szerepkör megtekintéséhez

Akárcsak a beépített szerepkörök a `AssignableScopes` tulajdonság határozza meg, hogy a szerepkör-hozzárendelés érhető a hatókörök. A `AssignableScopes` tulajdonsága egy egyéni szerepkört is szabályozza, akik létrehozása, törlése, módosítása vagy az egyéni szerepkör megtekintéséhez.

| Tevékenység | Művelet | Leírás |
| --- | --- | --- |
| Egyéni szerepkör létrehozása/törlése | `Microsoft.Authorization/ roleDefinitions/write` | Ez a művelet az összes engedéllyel rendelkező felhasználók a `AssignableScopes` az egyéni szerepkör létrehozhatja (vagy törölheti) használja ezeket az egyéni szerepkörök. Például [tulajdonosok](built-in-roles.md#owner) és [felhasználói rendszergazdák](built-in-roles.md#user-access-administrator) előfizetések, erőforráscsoportok és erőforrásokat. |
| Egyéni szerepkörök frissítése | `Microsoft.Authorization/ roleDefinitions/write` | Ez a művelet az összes engedéllyel rendelkező felhasználók a `AssignableScopes` az egyéni szerepkör frissítheti ezeket az egyéni szerepkörök. Például [tulajdonosok](built-in-roles.md#owner) és [felhasználói rendszergazdák](built-in-roles.md#user-access-administrator) előfizetések, erőforráscsoportok és erőforrásokat. |
| Egyéni szerepkör megtekintése | `Microsoft.Authorization/ roleDefinitions/read` | Ez a művelet egy hatókörben engedéllyel rendelkező felhasználók megtekinthetik az adott hatókörben hozzárendelés elérhető egyéni szerepkört. Összes beépített szerepkört engedélyezése egyéni szerepkörök hozzárendelés elérhető legyen. |

## <a name="next-steps"></a>További lépések
- [Egyéni szerepkörök létrehozása Azure-erőforrásokhoz az Azure PowerShell használatával](custom-roles-powershell.md)
- [Egyéni szerepkörök létrehozása Azure-erőforrásokhoz az Azure CLI használatával](custom-roles-cli.md)
- [Megismerheti az Azure-erőforrások szerepkör-definíciók](role-definitions.md)
- [Az RBAC hibaelhárítása az Azure-erőforrásokhoz](troubleshooting.md)
