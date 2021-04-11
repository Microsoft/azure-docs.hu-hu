---
title: Felügyelt identitások az Azure-erőforrásokhoz – gyakran ismételt kérdések – Azure AD
description: A felügyelt identitásokkal kapcsolatos gyakori kérdések
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 04/08/2021
ms.author: barclayn
ms.openlocfilehash: 3d3ab9859eb9f85d5ca7d0573fa79443ae9fe964
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107251010"
---
# <a name="managed-identities-for-azure-resources-frequently-asked-questions---azure-ad"></a>Felügyelt identitások Azure-erőforrásokhoz – gyakori kérdések – Azure AD

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

> [!NOTE]
> Az Azure-erőforrások felügyelt identitásai a Managed Service Identity (MSI) szolgáltatás új neve.

## <a name="administration"></a>Felügyelet

### <a name="how-can-you-find-resources-that-have-a-managed-identity"></a>Hogyan találhatják meg a felügyelt identitással rendelkező erőforrásokat?

A rendszerhez rendelt felügyelt identitással rendelkező erőforrások listáját az alábbi Azure CLI-paranccsal érheti el: 

```azurecli-interactive
az resource list --query "[?identity.type=='SystemAssigned'].{Name:name,  principalId:identity.principalId}" --output table
```


### <a name="what-azure-rbac-permissions-are-required-to-managed-identity-on-a-resource"></a>Milyen Azure RBAC-engedélyekre van szükség egy erőforrás felügyelt identitásához? 

- Rendszer által hozzárendelt felügyelt identitás: írási engedélyre van szüksége az erőforráson. Virtuális gépek esetében például a Microsoft.Compute/virtualMachines/write engedélyre van szükség. Ez a művelet az erőforrás-specifikus beépített szerepkörök, például a [virtuális gépek közreműködője](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)része.
- Felhasználó által hozzárendelt felügyelt identitás: írási engedélyre van szüksége az erőforráson. Virtuális gépek esetében például a Microsoft.Compute/virtualMachines/write engedélyre van szükség. A felügyelt identitás- [kezelő](../../role-based-access-control/built-in-roles.md#managed-identity-operator) szerepkör-hozzárendelésen felül a felügyelt identitáson kívül is.

### <a name="how-do-i-prevent-the-creation-of-user-assigned-managed-identities"></a>Hogyan a felhasználó által hozzárendelt felügyelt identitások létrehozásának megakadályozása?

Megtarthatja, hogy a felhasználók az [Azure Policy](../../governance/policy/overview.md) használatával hozzanak létre felhasználó által hozzárendelt felügyelt identitásokat

1. Lépjen a [Azure Portalra](https://portal.azure.com) , és nyissa meg a **házirendet**.
2. **Definíciók** kiválasztása
3. Válassza a **+ szabályzat definíciója** lehetőséget, és adja meg a szükséges információkat.
4. A házirend-szabály szakaszban illessze be
    
    ```json
    {
      "mode": "All",
      "policyRule": {
        "if": {
          "field": "type",
          "equals": "Microsoft.ManagedIdentity/userAssignedIdentities"
        },
        "then": {
          "effect": "deny"
        }
      },
      "parameters": {}
    }
    
    ```

A szabályzat létrehozása után rendelje hozzá a használni kívánt erőforráscsoporthoz.

1. Navigáljon az erőforráscsoportok elemre.
2. Keresse meg a teszteléshez használt erőforráscsoportot.
3. A bal oldali menüben válassza a **szabályzatok** lehetőséget.
4. Válassza a **házirend kiosztása** elemet.
5. Az **alapok** szakaszban adja meg a következőket:
    1. **Hatókör** A teszteléshez használt erőforráscsoport
    1. **Házirend-definíció**: a korábban létrehozott szabályzat.
6. Hagyja meg az összes többi beállítást az alapértelmezett értékeken, majd válassza a **felülvizsgálat + létrehozás** lehetőséget.

Ezen a ponton a felhasználó által hozzárendelt felügyelt identitások az erőforráscsoporthoz való létrehozására tett kísérletek sikertelenek lesznek.

  ![Szabályzat megsértése](./media/known-issues/policy-violation.png)

## <a name="concepts"></a>Alapelvek

### <a name="do-managed-identities-have-a-backing-app-object"></a>A felügyelt identitások rendelkeznek egy támogató alkalmazási objektummal?

Nem. A felügyelt identitások és Azure AD alkalmazás regisztrációk nem egyeznek meg a címtárban.

Alkalmazásregisztrációk két összetevővel rendelkezik: egy Application Object + egy egyszerű szolgáltatásnév objektum.
Az Azure-erőforrásokhoz tartozó felügyelt identitások csak a következő összetevők egyikével rendelkeznek: egyszerű szolgáltatásnév-objektum.

A felügyelt identitások nem rendelkeznek alkalmazás-objektummal a címtárban, ami általában az MS Graph alkalmazás engedélyeinek megadására szolgál. Ehelyett a felügyelt identitásokhoz tartozó MS Graph-engedélyeket közvetlenül az egyszerű szolgáltatásnak kell megadnia.

### <a name="what-is-the-credential-associated-with-a-managed-identity-how-long-is-it-valid-and-how-often-is-it-rotated"></a>Mi a felügyelt identitáshoz társított hitelesítő adat? Mennyi ideig érvényes és milyen gyakran forog?

> [!NOTE]
> A felügyelt identitások hitelesítése egy belső megvalósítási részlet, amely figyelmeztetés nélkül változhat.

A felügyelt identitások tanúsítványalapú hitelesítést használnak. Az egyes felügyelt identitások hitelesítő adatai 90 napon belül lejárnak, és a rendszer 45 nap után gördült le.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>Milyen identitást IMDS az alapértelmezett érték, ha nem ad meg identitást a kérelemben?

- Ha a rendszerhez rendelt felügyelt identitás engedélyezve van, és nincs megadva identitás a kérelemben, a IMDS alapértelmezés szerint a rendszerhez rendelt felügyelt identitást adja meg.
- Ha a rendszerhez rendelt felügyelt identitás nincs engedélyezve, és csak egy felhasználóhoz rendelt felügyelt identitás létezik, a IMDS alapértelmezés szerint az adott felhasználóhoz rendelt felügyelt identitást.
- Ha a rendszerhez rendelt felügyelt identitás nincs engedélyezve, és több felhasználó által hozzárendelt felügyelt identitás létezik, akkor a kérésben meg kell adnia egy felügyelt identitást.

## <a name="limitations"></a>Korlátozások

### <a name="can-the-same-managed-identity-be-used-across-multiple-regions"></a>Ugyanaz a felügyelt identitás több régióban is használható?

Röviden, igen, a felhasználóhoz rendelt felügyelt identitásokat több Azure-régióban is használhatja. A hosszú válasz az, hogy míg a felhasználó által hozzárendelt felügyelt identitások regionális erőforrásként jönnek létre, az Azure AD-ben létrehozott társított [egyszerű szolgáltatás](../develop/app-objects-and-service-principals.md#service-principal-object) (SP) globálisan elérhető. Az egyszerű szolgáltatás bármely Azure-régióból felhasználható, a rendelkezésre állása pedig az Azure AD rendelkezésre állásának függvénye. Ha például egy felhasználóhoz rendelt felügyelt identitást hozott létre a South-Central régióban, és ez a régió elérhetetlenné válik, akkor ez a probléma csak a felügyelt identitásban lévő [sík tevékenységek vezérlését](../../azure-resource-manager/management/control-plane-and-data-plane.md) befolyásolja.  A felügyelt identitások használatára már konfigurált erőforrások által végrehajtott tevékenységeket nem érinti a program.

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Az Azure-erőforrások felügyelt identitásai működnek az Azure Cloud Services?

Nem, az Azure-Cloud Services nem támogatja az Azure-erőforrások felügyelt identitásának támogatását.


### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Mi a felügyelt identitások biztonsági határa az Azure-erőforrásokhoz?

Az identitás biztonsági határa az az erőforrás, amelyhez hozzá van rendelve. Az Azure-erőforrások felügyelt identitással rendelkező virtuális gépek biztonsági határa például a virtuális gép. Az adott virtuális gépen futó kódok meghívhatják az Azure-erőforrások végpontjának felügyelt identitásait, és jogkivonatokat kérhetnek. Ez a hasonló élmény az Azure-erőforrások felügyelt identitásait támogató egyéb erőforrásokkal.

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>A felügyelt identitások automatikusan újra létrejönnek, ha egy előfizetést másik könyvtárba helyezek át?

Nem. Ha egy előfizetést másik könyvtárba helyez át, akkor manuálisan újra létre kell hoznia őket, és újra meg kell adnia az Azure szerepkör-hozzárendeléseket.
- A rendszerhez rendelt felügyelt identitások esetében: letiltás és újbóli engedélyezés. 
- A felhasználóhoz rendelt felügyelt identitások esetében: törölje, hozza létre újra, majd csatolja újra a szükséges erőforrásokhoz (például virtuális gépekhez)

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>Használhatok egy felügyelt identitást egy másik címtárban/bérlőben lévő erőforrás eléréséhez?

Nem. A felügyelt identitások jelenleg nem támogatják a könyvtárak közötti forgatókönyveket. 

### <a name="are-there-any-rate-limits-that-apply-to-managed-identities"></a>Vannak a felügyelt identitásokra érvényes díjszabási korlátok?

A felügyelt identitások korlátai az Azure szolgáltatási korlátaival, az Azure Instance Metadata Service (IMDS) korlátozásokkal és a Azure Active Directory szolgáltatási korlátokkal rendelkeznek.

- Az **Azure-szolgáltatás korlátai** határozzák meg a bérlői és az előfizetési szinten végrehajtható létrehozási műveletek számát. A felhasználóhoz rendelt felügyelt identitások [korlátozásai](../../azure-resource-manager/management/azure-subscription-service-limits.md#managed-identity-limits) is megváltozhatnak.
- **IMDS** Általánosságban elmondható, hogy a IMDS irányuló kérések másodpercenként öt kérelemre korlátozódnak. A küszöbértéket meghaladó kérések elutasítása 429-válaszokkal történik. A felügyelt identitások kategóriába érkező kérések másodpercenként legfeljebb 20 kérést és 5 egyidejű kérelmet kapnak. További információ az [Azure instance metadata Service (Windows)](../../virtual-machines/windows/instance-metadata-service.md?tabs=windows#managed-identity) című cikkben található.
- **Azure Active Directory szolgáltatás** Az egyes felügyelt identitások az Azure ad-bérlőben az Azure [ad szolgáltatás korlátai és korlátozásai](../enterprise-users/directory-service-limits-restrictions.md)című témakörben leírtak szerint számítanak bele az objektum-kvótára.


## <a name="is-it-ok-to-move-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>A felhasználó által hozzárendelt felügyelt identitások áthelyezése egy másik erőforráscsoporthoz vagy előfizetésbe?

A felhasználóhoz rendelt felügyelt identitás más erőforráscsoporthoz való áthelyezése nem támogatott.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg [, hogyan működnek a felügyelt identitások a virtuális gépekkel](how-managed-identities-work-vm.md)
