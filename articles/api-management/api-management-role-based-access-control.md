---
title: A Role-Based Access Control használata az Azure-ban API Management | Microsoft Docs
description: Ismerje meg, hogyan használhatja a beépített szerepköröket, és hogyan hozhat létre egyéni szerepköröket az Azure-ban API Management
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
ms.openlocfilehash: fc41d2904d5a6676f1e9e1e2d178985bef0a9aa3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101095546"
---
# <a name="how-to-use-role-based-access-control-in-azure-api-management"></a>Szerepköralapú hozzáférés-vezérlés használata az Azure API Managementben

Az Azure API Management az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) segítségével lehetővé teszi a részletes hozzáférés-kezelést a API Management-szolgáltatásokhoz és-entitásokhoz (például API-k és házirendek). Ez a cikk áttekintést nyújt a API Management beépített és egyéni szerepköreiről. A Azure Portal hozzáférés-kezelésével kapcsolatos további információkért lásd: Ismerkedés a [hozzáférés-kezeléssel a Azure Portal](../role-based-access-control/overview.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="built-in-roles"></a>Beépített szerepkörök

API Management jelenleg három beépített szerepkört biztosít, és a közeljövőben két további szerepkört fog felvenni. Ezeket a szerepköröket különböző hatókörökhöz lehet rendelni, beleértve az előfizetést, az erőforráscsoportot és az egyes API Management-példányokat. Ha például a "API Management szolgáltatás-olvasó" szerepkört a felhasználóhoz az erőforráscsoport szintjén rendeli hozzá, akkor a felhasználó olvasási hozzáféréssel rendelkezik az erőforráscsoporthoz tartozó összes API Management példányhoz. 

A következő táblázat a beépített szerepkörök rövid leírását tartalmazza. Ezeket a szerepköröket a Azure Portal vagy más eszközök, például az Azure [PowerShell](../role-based-access-control/role-assignments-powershell.md), az [Azure CLI](../role-based-access-control/role-assignments-cli.md)és a [REST API](../role-based-access-control/role-assignments-rest.md)használatával rendelheti hozzá. A beépített szerepkörök hozzárendelésével kapcsolatos további információkért lásd: [Azure-szerepkörök társítása az Azure-előfizetési erőforrások elérésének kezeléséhez](../role-based-access-control/role-assignments-portal.md).

| Szerepkör          | Olvasási hozzáférés<sup>[1]</sup> | Írási hozzáférés<sup>[2]</sup> | Szolgáltatás létrehozása, törlése, skálázás, VPN és egyéni tartomány konfigurálása | Hozzáférés a régi közzétevői portálhoz | Description
| ------------- | ---- | ---- | ---- | ---- | ---- 
| API Management szolgáltatás közreműködői | ✓ | ✓ | ✓ | ✓ | Felügyelői rendszergazda. Teljes körű, API Management szolgáltatásokhoz és entitásokhoz (például API-khoz és házirendekhez) való hozzáférése. Hozzáfér a régi közzétevői portálhoz. |
| API Management szolgáltatás-olvasó | ✓ | | || Csak olvasási hozzáféréssel rendelkezik API Management szolgáltatásokhoz és entitásokhoz. |
| API Management szolgáltatás operátora | ✓ | | ✓ | | Felügyelheti API Management-szolgáltatásokat, az entitásokat azonban nem.|
| API Management Service Editor<sup>*</sup> | ✓ | ✓ | |  | Felügyelheti API Management entitásokat, de nem szolgáltatásokat.|
| API Management Content Manager<sup>*</sup> | ✓ | | | ✓ | Kezelheti a fejlesztői portált. Csak olvasási hozzáférés a szolgáltatásokhoz és az entitásokhoz.|

<sup>[1] olvasási hozzáférés API Management szolgáltatásokhoz és entitásokhoz (például API-k és házirendek).</sup>

<sup>[2] írási hozzáférés API Management szolgáltatásokhoz és entitásokhoz a következő műveletek kivételével: példány létrehozása, törlése és skálázása; VPN-konfiguráció; és az egyéni tartomány beállítása.</sup>

<sup>\* A Service Editor szerepkör akkor lesz elérhető, ha a meglévő közzétevő portál összes rendszergazdai felhasználói felületét áttelepítette a Azure Portalba. A Content Manager szerepkör a közzétevői portál újraszámítása után lesz elérhető, hogy csak a fejlesztői portál kezelésével kapcsolatos funkciókat tartalmazzon.</sup>  

## <a name="custom-roles"></a>Egyéni szerepkörök

Ha a beépített szerepkörök egyike sem felel meg a konkrét igényeinek, létrehozhat egyéni szerepköröket, amelyekkel részletesebb hozzáférés-kezelést biztosíthat API Management entitásokhoz. Létrehozhat például olyan egyéni szerepkört, amely csak olvasási hozzáféréssel rendelkezik egy API Management szolgáltatáshoz, de csak írási hozzáféréssel rendelkezik egy adott API-hoz. Az egyéni szerepkörökkel kapcsolatos további tudnivalókért tekintse meg [az egyéni szerepkörök az Azure RBAC](../role-based-access-control/custom-roles.md)című témakört. 

> [!NOTE]
> Ahhoz, hogy meg tudja tekinteni a Azure Portal API Management példányát, egy egyéni szerepkörnek tartalmaznia kell a ```Microsoft.ApiManagement/service/read``` műveletet.

Ha létrehoz egy egyéni szerepkört, könnyebben megkezdheti a beépített szerepkörök egyikét. Szerkessze az attribútumokat a **műveletek**, a eltérések **vagy a** **AssignableScopes** hozzáadásához, majd mentse a módosításokat új szerepkörként. Az alábbi példa a "API Management szolgáltatás-olvasó" szerepkörrel kezdődik, és létrehoz egy "Calculator API Editor" nevű egyéni szerepkört. Az egyéni szerepkört hozzárendelheti egy adott API-hoz. Ennek következtében ennek a szerepkörnek csak az adott API-hoz van hozzáférése. 

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

A [Azure Resource Manager erőforrás-szolgáltató operatív](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement) cikk a API Management szinten megadható engedélyek listáját tartalmazza.

## <a name="video"></a>Videó


> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Role-Based-Access-Control-in-API-Management/player]
>
>

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni az Azure-beli Role-Based Access Controlról, tekintse meg a következő cikkeket:
  * [Bevezetés a hozzáférés-kezelés Azure Portalon történő használatába](../role-based-access-control/overview.md)
  * [Azure-szerepkörök kiosztása az Azure-előfizetések erőforrásaihoz való hozzáférés kezeléséhez](../role-based-access-control/role-assignments-portal.md)
  * [Egyéni szerepkörök az Azure RBAC](../role-based-access-control/custom-roles.md)
  * [Azure Resource Manager erőforrás-szolgáltatói műveletek](../role-based-access-control/resource-provider-operations.md#microsoftapimanagement)
