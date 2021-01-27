---
title: Terv üzembe helyezésének szakaszai
description: Ismerje meg a biztonsági és az összetevővel kapcsolatos lépéseket, amelyekkel az Azure tervrajz-szolgáltatásai áthaladnak a tervrajz-hozzárendelés létrehozása során.
ms.date: 01/27/2021
ms.topic: conceptual
ms.openlocfilehash: e686dec1c9d79e42dafee17d8a937284aac4fdd6
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98918551"
---
# <a name="stages-of-a-blueprint-deployment"></a>Terv üzembe helyezésének szakaszai

Egy terv üzembe helyezése után az Azure BluePrints szolgáltatás számos műveletet végez a tervben meghatározott erőforrások üzembe helyezéséhez. Ez a cikk részletesen ismerteti az egyes lépések részleteit.

A terv üzembe helyezése egy tervnek egy előfizetéshez rendelésével vagy [egy meglévő hozzárendelés frissítésével](../how-to/update-existing-assignments.md)aktiválódik. Az üzembe helyezés során az Azure-tervrajzok a következő magas szintű lépéseket végzik el:

> [!div class="checklist"]
> - Az Azure-tervezetek tulajdonosi jogosultságokat biztosítanak
> - A terv-hozzárendelési objektum létrejött
> - Opcionális – az Azure-tervrajzok **rendszer által hozzárendelt** felügyelt identitást hoznak létre
> - A felügyelt identitás üzembe helyezi a tervrajz-összetevőket
> - Az Azure BluePrints szolgáltatás és a **rendszerhez rendelt** felügyelt identitási jogosultságok visszavonása

## <a name="azure-blueprints-granted-owner-rights"></a>Az Azure-tervezetek tulajdonosi jogosultságokat biztosítanak

Az Azure BluePrints egyszerű szolgáltatás tulajdonosi jogosultságokat kap a hozzárendelt előfizetéshez vagy előfizetésekhez, ha a [rendszerhez hozzárendelt felügyelt identitású](../../../active-directory/managed-identities-azure-resources/overview.md) felügyelt identitás van használatban. A megadott szerepkör lehetővé teszi, hogy az Azure tervrajzai létrehozzák és később visszavonják a **rendszer által hozzárendelt** felügyelt identitást. Ha **felhasználó által hozzárendelt** felügyelt identitást használ, az Azure BluePrints egyszerű szolgáltatás nem kap, és nincs szüksége tulajdonosi jogosultságokra az előfizetésben.

A jogosultságok automatikusan megadhatók, ha a hozzárendelés a portálon keresztül történik. Ha azonban a hozzárendelés a REST APIon keresztül történik, a jogokat külön API-hívással kell megadnia. Az Azure-tervezetek AppId `f71766dc-90d9-4b7d-bd9d-4499c4331c3f` , de az egyszerű szolgáltatásnév a bérlőtől függ. Az egyszerű szolgáltatás beszerzéséhez használja a [Azure Active Directory Graph API](../../../active-directory/develop/active-directory-graph-api.md) és a REST-végpont [servicePrincipals](/graph/api/resources/serviceprincipal) . Ezután adja meg az Azure a _tulajdonosi_ szerepkört a [portálon](../../../role-based-access-control/role-assignments-portal.md), az [azure CLI](../../../role-based-access-control/role-assignments-cli.md)-n, [Azure PowerShellon](../../../role-based-access-control/role-assignments-powershell.md), [Rest APIn](../../../role-based-access-control/role-assignments-rest.md)vagy egy [Azure Resource Manager sablonon](../../../role-based-access-control/role-assignments-template.md)keresztül.

Az Azure BluePrints szolgáltatás nem közvetlenül helyezi üzembe az erőforrásokat.

## <a name="the-blueprint-assignment-object-is-created"></a>A terv-hozzárendelési objektum létrejött

Egy felhasználó, csoport vagy szolgáltatásnév egy tervezetet rendel egy előfizetéshez. A hozzárendelési objektum létezik az előfizetés szintjén, ahol a terv hozzá lett rendelve. A központi telepítés által létrehozott erőforrások nem az üzembe helyezési entitás kontextusában vannak végrehajtva.

A terv-hozzárendelés létrehozásakor a [felügyelt identitás](../../../active-directory/managed-identities-azure-resources/overview.md) típusa van kiválasztva. Az alapértelmezett érték egy **rendszerhez rendelt** felügyelt identitás. A **felhasználó által hozzárendelt** felügyelt identitás választható. **Felhasználó által hozzárendelt** felügyelt identitás használatakor a terv-hozzárendelés létrehozása előtt meg kell határozni és engedélyeket adni. A [tulajdonosi](../../../role-based-access-control/built-in-roles.md#owner) és a [tervrajz-kezelő](../../../role-based-access-control/built-in-roles.md#blueprint-operator) beépített szerepköreinek a megfelelő jogosultsággal rendelkeznek ahhoz, hogy olyan `blueprintAssignment/write` hozzárendelést hozzon létre, amely **felhasználó által hozzárendelt** felügyelt identitást használ.

## <a name="optional---azure-blueprints-creates-system-assigned-managed-identity"></a>Opcionális – az Azure-tervrajzok rendszer által hozzárendelt felügyelt identitást hoznak létre

Ha a hozzárendelés során a [rendszerhez hozzárendelt felügyelt identitás](../../../active-directory/managed-identities-azure-resources/overview.md) van kiválasztva, az Azure-tervrajzok létrehozzák az identitást, és a [tulajdonos](../../../role-based-access-control/built-in-roles.md#owner) szerepkört a felügyelt identitásnak [Meglévő hozzárendelés frissítésekor](../how-to/update-existing-assignments.md)az Azure-tervezetek a korábban létrehozott felügyelt identitást használják.

A terv-hozzárendeléshez kapcsolódó felügyelt identitás a tervben meghatározott erőforrások üzembe helyezésére vagy újratelepítésére szolgál. Ez a kialakítás megakadályozza, hogy a hozzárendelések véletlenül zavarják egymással.
Ez a kialakítás az [erőforrás-zárolási](./resource-locking.md) funkciót is támogatja azáltal, hogy az egyes üzembe helyezett erőforrások biztonságát a tervezetből szabályozza.

## <a name="the-managed-identity-deploys-blueprint-artifacts"></a>A felügyelt identitás üzembe helyezi a tervrajz-összetevőket

A felügyelt identitás ezt követően elindítja az összetevők Resource Manager általi telepítését a tervben a meghatározott sorrend szerinti [sorrendben](./sequencing-order.md). A sorrend beállítható úgy, hogy a többi összetevőtől függő összetevők megfelelő sorrendben legyenek telepítve.

Az üzemelő példányok hozzáférési meghibásodása gyakran a felügyelt identitáshoz megadott hozzáférési szint eredményét eredményezi. Az Azure BluePrints szolgáltatás kezeli a **rendszer által hozzárendelt** felügyelt identitás biztonsági életciklusát. A felhasználó azonban felelős a **felhasználóhoz rendelt** felügyelt identitások jogainak és életciklusának kezeléséért.

## <a name="blueprint-service-and-system-assigned-managed-identity-rights-are-revoked"></a>A terv szolgáltatás és a rendszer által hozzárendelt felügyelt identitási jogosultságok visszavonása

Az üzembe helyezések befejezése után az Azure-tervezetek visszavonják a **rendszer által hozzárendelt** felügyelt identitás jogosultságait az előfizetésből. Ezt követően az Azure BluePrints szolgáltatás visszavonja a hozzá tartozó jogosultságokat az előfizetésből. A Rights eltávolítása megakadályozza, hogy az Azure-tervezetek állandó tulajdonosként legyenek előfizetésben.

## <a name="next-steps"></a>Következő lépések

- A [statikus és dinamikus paraméterek](./parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](./sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](./resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../how-to/update-existing-assignments.md) elsajátítása.
- A tervek hozzárendelése során felmerülő problémák megoldása [általános hibaelhárítással](../troubleshoot/general.md).
