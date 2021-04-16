---
title: Azure-erőforrások felügyelt identitása – Gyakori kérdések – Azure AD"
description: Gyakori kérdések a felügyelt identitásokkal kapcsolatban
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
ms.openlocfilehash: 07b106630cffae75c5e4588d14de7ae938945614
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534126"
---
# <a name="managed-identities-for-azure-resources-frequently-asked-questions---azure-ad"></a>Azure-erőforrások felügyelt identitása – gyakori kérdések – Azure AD

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

> [!NOTE]
> Az Azure-erőforrások felügyelt identitásai a Managed Service Identity (MSI) szolgáltatás új neve.

## <a name="administration"></a>Felügyelet

### <a name="how-can-you-find-resources-that-have-a-managed-identity"></a>Hogyan találhatja meg a felügyelt identitással rendelkezik erőforrásokat?

A rendszer által hozzárendelt felügyelt identitással rendelkezik erőforrások listáját a következő Azure CLI-paranccsal találhatja meg: 

```azurecli-interactive
az resource list --query "[?identity.type=='SystemAssigned'].{Name:name,  principalId:identity.principalId}" --output table
```


### <a name="what-azure-rbac-permissions-are-required-to-managed-identity-on-a-resource"></a>Milyen Azure RBAC-engedélyek szükségesek egy erőforrás felügyelt identitásához? 

- Rendszer által hozzárendelt felügyelt identitás: Írási engedélyekkel kell rendelkeznie az erőforráson. Virtuális gépek esetében például a Microsoft.Compute/virtualMachines/write engedélyre van szükség. Ez a művelet olyan erőforrás-specifikus beépített szerepkörökben szerepel, mint a [Virtuális gépek közreműködője.](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
- Felhasználó által hozzárendelt felügyelt identitás: Írási engedélyekkel kell rendelkeznie az erőforráson. Virtuális gépek esetében például a Microsoft.Compute/virtualMachines/write engedélyre van szükség. A Felügyelt identitás [kezelője](../../role-based-access-control/built-in-roles.md#managed-identity-operator) szerepkör-hozzárendelése a felügyelt identitáson kívül.

### <a name="how-do-i-prevent-the-creation-of-user-assigned-managed-identities"></a>Hogyan megakadályozza a felhasználó által hozzárendelt felügyelt identitások létrehozását?

A felhasználók így nem tudnak felhasználó által hozzárendelt felügyelt identitásokat létrehozni [Azure Policy](../../governance/policy/overview.md)

1. Lépjen a [Azure Portal](https://portal.azure.com) a Szabályzat **lapra.**
2. Definíciók **kiválasztása**
3. Válassza **a + Szabályzatdefiníció lehetőséget,** és adja meg a szükséges információkat.
4. A szabályzatszabály szakaszban illessze be a következőt:
    
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

Miután létrehozta a szabályzatot, rendelje hozzá ahhoz az erőforráscsoporthoz, amit használni szeretne.

1. Lépjen az erőforráscsoportokhoz.
2. Keresse meg a teszteléshez használt erőforráscsoportot.
3. A **bal oldali menüben** válassza a Szabályzatok lehetőséget.
4. Válassza a **Szabályzat hozzárendelése lehetőséget**
5. Az Alapvető **szolgáltatások szakaszban adja** meg a következő adatokat:
    1. **Hatókör** A teszteléshez használt erőforráscsoport
    1. **Szabályzatdefiníció:** A korábban létrehozott szabályzat.
6. Hagyja meg az összes többi beállítás alapértelmezett beállítását, és válassza az **Áttekintés + létrehozás lehetőséget**

Ezen a ponton az erőforráscsoportban a felhasználó által hozzárendelt felügyelt identitás létrehozására tett minden kísérlet sikertelen lesz.

  ![Szabályzat megsértése](./media/known-issues/policy-violation.png)

## <a name="concepts"></a>Alapelvek

### <a name="do-managed-identities-have-a-backing-app-object"></a>A felügyelt identitások vannak háttéralkalmazás-objektummal?

Nem. A felügyelt identitások és Azure AD alkalmazás regisztrációk nem azonosak a címtárban.

Alkalmazásregisztrációk összetevőből áll: Egy alkalmazásobjektumból és egy egyszerű szolgáltatásobjektumból.
Az Azure-erőforrások felügyelt identitása csak az egyik ilyen összetevővel rendelkezik: egy egyszerű szolgáltatásobjektummal.

A felügyelt identitások nem rendelkezik alkalmazásobjektummal a címtárban, ami gyakran használatos az MS Graph alkalmazásengedélyeinek megadásához. Ehelyett a felügyelt identitások MS Graph-engedélyét közvetlenül a szolgáltatásnévnek kell megadni.

### <a name="what-is-the-credential-associated-with-a-managed-identity-how-long-is-it-valid-and-how-often-is-it-rotated"></a>Mi a felügyelt identitáshoz társított hitelesítő adat? Mennyi ideig érvényes, és milyen gyakran van elforgatva?

> [!NOTE]
> A felügyelt identitások hitelesítésének egy belső megvalósítási részlete, amely értesítés nélkül változhat.

A felügyelt identitások tanúsítványalapú hitelesítést használnak. Minden felügyelt identitás hitelesítő adatai 90 nap után lejárnak, és 45 nap után le vannak gördülve.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>Milyen identitás lesz az IMDS alapértelmezett beállítása, ha nem adja meg az identitást a kérésben?

- Ha a rendszer által hozzárendelt felügyelt identitás engedélyezve van, és nincs megadva identitás a kérésben, az IMDS alapértelmezés szerint a rendszer által hozzárendelt felügyelt identitást használja.
- Ha a rendszer által hozzárendelt felügyelt identitás nincs engedélyezve, és csak egy felhasználóhoz hozzárendelt felügyelt identitás létezik, az IMDS alapértelmezés szerint az adott felhasználó által hozzárendelt felügyelt identitást használja.
- Ha a rendszer által hozzárendelt felügyelt identitás nincs engedélyezve, és több felhasználóhoz hozzárendelt felügyelt identitás is létezik, akkor a kérésben meg kell adnia egy felügyelt identitást.

## <a name="limitations"></a>Korlátozások

### <a name="can-the-same-managed-identity-be-used-across-multiple-regions"></a>Használható ugyanaz a felügyelt identitás több régióban is?

Röviden: igen, több Azure-régióban is használhat felhasználó által hozzárendelt felügyelt identitásokat. A hosszabb válasz az, hogy míg a felhasználó által [](../develop/app-objects-and-service-principals.md#service-principal-object) hozzárendelt felügyelt identitások regionális erőforrásként vannak létrehozva, az Azure AD-ban létrehozott társított szolgáltatásnév (SP) globálisan elérhető. A szolgáltatásnév bármely Azure-régióból használható, és a rendelkezésre állása az Azure AD elérhetőségétől függ. Ha például létrehozott egy felhasználóhoz hozzárendelt felügyelt identitást a South-Central-régióban, és [](../../azure-resource-manager/management/control-plane-and-data-plane.md) ez a régió elérhetetlenné válik, a probléma csak magára a felügyelt identitásra van hatással a vezérlősík tevékenységeire.  A felügyelt identitások használatára már konfigurált erőforrások által végrehajtott tevékenységek nem lesznek hatással.

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Az Azure-erőforrások felügyelt identitása működik a Azure Cloud Services?

Nem, nincsenek tervek az Azure-erőforrások felügyelt identitásának támogatására a Azure Cloud Services.


### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Mi az Azure-erőforrások felügyelt identitásának biztonsági határa?

Az identitás biztonsági határa az az erőforrás, amelyhez csatolva van. Például egy olyan virtuális gép biztonsági határa, amelynél engedélyezve vannak az Azure-erőforrások felügyelt identitásai, a virtuális gép. A virtuális gépen futó bármely kód képes az Azure-erőforrások felügyelt identitásának végpontjának hívása és a jogkivonatok kérése. Ez hasonló a más erőforrásokhoz, amelyek támogatják az Azure-erőforrások felügyelt identitását.

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>Automatikusan újra létrejönnek a felügyelt identitások, ha áthelyezek egy előfizetést egy másik címtárba?

Nem. Ha egy előfizetést egy másik címtárba költöztet, manuálisan újra létre kell hoznia őket, és újra meg kell adnia az Azure-beli szerepkör-hozzárendeléseket.
- Rendszer által hozzárendelt felügyelt identitások esetében: tiltsa le és engedélyezze újra. 
- Felhasználó által hozzárendelt felügyelt identitások esetén: törölje, hozza létre újra és csatolja őket újra a szükséges erőforrásokhoz (például virtuális gépekhez)

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>Használhatok felügyelt identitást egy másik címtárban vagy bérlőben található erőforrás eléréséhez?

Nem. A felügyelt identitások jelenleg nem támogatják a címtárak közötti forgatókönyveket. 

### <a name="are-there-any-rate-limits-that-apply-to-managed-identities"></a>Vonatkoznak sebességkorlátok a felügyelt identitások esetében?

A felügyelt identitások korlátai az Azure-szolgáltatások korlátaitól, az Azure Instance Metadata Service (IMDS) korlátaitól és a Azure Active Directory függnek.

- **Az Azure szolgáltatási korlátai** határozzák meg a bérlői és az előfizetési szinten elvégezhető létrehozási műveletek számát. A felhasználó által hozzárendelt felügyelt identitások neve is korlátozott. [](../../azure-resource-manager/management/azure-subscription-service-limits.md#managed-identity-limits)
- **IMDS** Az IMDS-nek általában másodpercenként öt kérésre van korlátozva. A küszöbértéket meghaladó kérelmeket a rendszer 429 válasz esetén elutasítja. A felügyelt identitás kategóriájába másodpercenként legfeljebb 20 kérelem és 5 egyidejű kérelem tartozik. További információt az [Azure Instance Metadata Service (Windows) cikkben](../../virtual-machines/windows/instance-metadata-service.md?tabs=windows#managed-identity) talál.
- **Azure Active Directory szolgáltatás** Az egyes felügyelt identitások beleszámnak az Azure AD-bérlőkben az objektumkvótakorlátba az Azure AD szolgáltatási korlátozásait és [korlátozásait ismertetik.](../enterprise-users/directory-service-limits-restrictions.md)


### <a name="is-it-possible-to-move-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>Áthelyezhető egy felhasználó által hozzárendelt felügyelt identitás egy másik erőforráscsoportba/előfizetésbe?

A felhasználó által hozzárendelt felügyelt identitások másik erőforráscsoportba való áthelyezése nem támogatott.

## <a name="next-steps"></a>Következő lépések

- [Megtudhatja, hogyan működnek a felügyelt identitások a virtuális gépekkel](how-managed-identities-work-vm.md)
