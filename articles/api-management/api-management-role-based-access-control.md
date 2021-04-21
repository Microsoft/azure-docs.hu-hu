---
title: A virtuális Role-Based Access Control használata az Azure API Management | Microsoft Docs
description: Megtudhatja, hogyan használhatja a beépített szerepköröket, és hogyan hozhat létre egyéni szerepköröket az Azure API Management
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 364cd53e-88fb-4301-a093-f132fa1f88f5
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ef71591d6d5a26aa737db4e7cb547c8b2c39d92a
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812173"
---
# <a name="how-to-use-role-based-access-control-in-azure-api-management"></a>Szerepköralapú hozzáférés-vezérlés használata az Azure API Managementben

Az Azure API Management az Azure szerepköralapú hozzáférés-vezérlésére (Azure RBAC) támaszkodik az API Management-szolgáltatások és -entitások (például API-k és szabályzatok) hozzáférés-vezérlésének engedélyezéséhez. Ez a cikk áttekintést nyújt a beépített és egyéni szerepkörökről a API Management. További információ a hozzáférés-kezelésről a Azure Portal: [A hozzáférés-kezelés](../role-based-access-control/overview.md)első lépések a Azure Portal.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="built-in-roles"></a>Beépített szerepkörök

API Management jelenleg három beépített szerepkört biztosít, és a közeljövőben két további szerepkört is hozzáad. Ezek a szerepkörök különböző hatókörökben rendelhetők hozzá, beleértve az előfizetést, az erőforráscsoportot és az egyes API Management is. Ha például a "API Management Szolgáltatásolvasó" szerepkört hozzárendeli egy felhasználóhoz az erőforráscsoport szintjén, akkor a felhasználó olvasási hozzáféréssel rendelkezik az erőforráscsoportban lévő összes API Management példányhoz. 

Az alábbi táblázat a beépített szerepkörök rövid leírását tartalmazza. Ezeket a szerepköröket a következő eszközökkel rendelheti hozzá: Azure Portal vagy más eszközök, például az Azure [PowerShell,](../role-based-access-control/role-assignments-powershell.md) [az Azure CLI](../role-based-access-control/role-assignments-cli.md)és a [REST API.](../role-based-access-control/role-assignments-rest.md) A beépített szerepkörök hozzárendelésével kapcsolatos részletekért lásd: Azure-szerepkörök hozzárendelése az Azure-előfizetés erőforrásaihoz való [hozzáférés kezeléséhez.](../role-based-access-control/role-assignments-portal.md)

| Szerepkör          | Olvasási hozzáférés<sup>[1]</sup> | Írási<sup>hozzáférés [2]</sup> | Szolgáltatás létrehozása, törlése, skálázása, VPN és egyéni tartománykonfiguráció | Hozzáférés az örökölt közzétevő portálhoz | Description
| ------------- | ---- | ---- | ---- | ---- | ---- 
| API Management szolgáltatás közreműködője | ✓ | ✓ | ✓ | ✓ | Felügyelő. Teljes KÖRŰ CRUD-hozzáféréssel rendelkezik API Management szolgáltatásokhoz és entitásokhoz (például API-khoz és szabályzatokhoz). Hozzáféréssel rendelkezik az örökölt közzétevő portálhoz. |
| API Management Szolgáltatásolvasó | ✓ | | || Csak olvasási hozzáféréssel rendelkezik API Management szolgáltatásokhoz és entitásokhoz. |
| API Management Service Operator | ✓ | | ✓ | | Kezelheti a API Management, entitásokat azonban nem.|
| API Management Service Editor<sup>*</sup> | ✓ | ✓ | |  | Kezelheti a API Management, de a szolgáltatásokat nem.|
| API Management Content Manager<sup>*</sup> | ✓ | | | ✓ | Kezelheti a fejlesztői portált. Csak olvasási hozzáférés a szolgáltatásokhoz és entitásokhoz.|

<sup>[1] Olvasási hozzáférés API Management szolgáltatásokhoz és entitásokhoz (például API-khoz és szabályzatokhoz).</sup>

<sup>[2] Írási hozzáférés API Management szolgáltatásokhoz és entitásokhoz, kivéve a következő műveleteket: példány létrehozása, törlése és skálázás; VPN-konfiguráció; és egyéni tartománybeállítás.</sup>

<sup>\* A Szolgáltatásszerkesztő szerepkör azután lesz elérhető, hogy az összes rendszergazdai felhasználói felületet átkértük a meglévő közzétevő portálról a Azure Portal. A Content Manager szerepkör a közzétevő portál újragyártását követően lesz elérhető, és csak a fejlesztői portál kezelésével kapcsolatos funkciókat tartalmazza.</sup>  

## <a name="custom-roles"></a>Egyéni szerepkörök

Ha a beépített szerepkörök egyike sem felel meg az adott igényeknek, egyéni szerepköröket hozhat létre, amelyek részletesebb hozzáférés-API Management biztosítanak az entitásokhoz. Létrehozhat például egy egyéni szerepkört, amely csak olvasási hozzáféréssel rendelkezik egy API Management-szolgáltatáshoz, de csak egy adott API-hoz rendelkezik írási hozzáféréssel. További információ az egyéni szerepkörökről: Egyéni szerepkörök az [Azure RBAC-ban.](../role-based-access-control/custom-roles.md) 

> [!NOTE]
> Ahhoz, hogy egy új API Management is látható legyen a Azure Portal, az egyéni szerepkörnek tartalmaznia kell a ```Microsoft.ApiManagement/service/read``` műveletet.

Egyéni szerepkör létrehozásakor egyszerűbb az egyik beépített szerepkört indítani. Szerkessze az attribútumokat a **Műveletek,** **NotActions**, vagy **AssignableScopes** hozzáadásához, majd mentse a módosításokat új szerepkörként. A következő példa a "API Management Service Reader" szerepkörvel kezdődik, és létrehoz egy "Calculator API Editor" (Kalkulátor API-szerkesztő) nevű egyéni szerepkört. Az egyéni szerepkört hozzárendelheti egy adott API-hoz. Ennek következtében ez a szerepkör csak az API-hoz fér hozzá. 

```powershell
$role = Get-AzRoleDefinition "API Management Service Reader Role"
$role.Id = $null
$role.Name = 'Calculator API Contributor'
$role.Description = 'Has read access to Contoso APIM instance and write access to the Calculator API.'
$role.Actions.Add('Microsoft.ApiManagement/service/apis/write')
$role.Actions.Add('Microsoft.ApiManagement/service/apis/*/write')
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add('/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>')
New-AzRoleDefinition -Role $role
New-AzRoleAssignment -ObjectId <object ID of the user account> -RoleDefinitionName 'Calculator API Contributor' -Scope '/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.ApiManagement/service/<service name>/apis/<api ID>'
```

A [Azure Resource Manager erőforrás-szolgáltatói műveleteket](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement) tartalmazó cikk azon engedélyek listáját tartalmazza, amelyek a API Management adhatók meg.

## <a name="video"></a>Videó


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Role-Based-Access-Control-in-API-Management/player]
>
>

## <a name="next-steps"></a>Következő lépések

Az Azure-beli Role-Based Access Control a következő cikkekben talál további információt:
  * [Bevezetés a hozzáférés-kezelés Azure Portalon történő használatába](../role-based-access-control/overview.md)
  * [Azure-szerepkörök hozzárendelése az Azure-előfizetés erőforrásaihoz való hozzáférés kezeléséhez](../role-based-access-control/role-assignments-portal.md)
  * [Egyéni szerepkörök az Azure RBAC-ban](../role-based-access-control/custom-roles.md)
  * [Azure Resource Manager erőforrás-szolgáltatói műveletek](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement)
