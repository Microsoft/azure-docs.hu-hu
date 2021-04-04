---
title: A felügyelt identitásokkal kapcsolatos gyakori kérdések és ismert problémák – Azure AD
description: Ismert problémák az Azure-erőforrások felügyelt identitásával kapcsolatban.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.assetid: 2097381a-a7ec-4e3b-b4ff-5d2fb17403b6
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/04/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 3f1be2e64435cb0bcdb369a398a9a65fc3714fb2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100008536"
---
# <a name="faqs-and-known-issues-with-managed-identities-for-azure-resources"></a>Az Azure-erőforrások felügyelt identitásával kapcsolatos gyakori kérdések és ismert problémák

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

## <a name="frequently-asked-questions-faqs"></a>Gyakori kérdések (GYIK)

> [!NOTE]
> Az Azure-erőforrások felügyelt identitásai a Managed Service Identity (MSI) szolgáltatás új neve.

### <a name="how-can-you-find-resources-that-have-a-managed-identity"></a>Hogyan találhatják meg a felügyelt identitással rendelkező erőforrásokat?

A rendszerhez rendelt felügyelt identitással rendelkező erőforrások listáját az alábbi Azure CLI-paranccsal érheti el: 

```azurecli-interactive
az resource list --query "[?identity.type=='SystemAssigned'].{Name:name,  principalId:identity.principalId}" --output table
```

### <a name="do-managed-identities-have-a-backing-app-object"></a>A felügyelt identitások rendelkeznek egy támogató alkalmazási objektummal?

Nem. A felügyelt identitások és Azure AD alkalmazás regisztrációk nem egyeznek meg a címtárban. 

Alkalmazásregisztrációk két összetevővel rendelkezik: egy Application Object + egy egyszerű szolgáltatásnév objektum. Az Azure-erőforrásokhoz tartozó felügyelt identitások csak a következő összetevők egyikével rendelkeznek: egyszerű szolgáltatásnév-objektum. 

A felügyelt identitások nem rendelkeznek alkalmazás-objektummal a címtárban, ami általában az MS Graph alkalmazás engedélyeinek megadására szolgál. Ehelyett a felügyelt identitásokhoz tartozó MS Graph-engedélyeket közvetlenül az egyszerű szolgáltatásnak kell megadnia.  

### <a name="can-the-same-managed-identity-be-used-across-multiple-regions"></a>Ugyanaz a felügyelt identitás több régióban is használható?

Röviden, igen, a felhasználóhoz rendelt felügyelt identitásokat több Azure-régióban is használhatja. A hosszú válasz az, hogy míg a felhasználóhoz rendelt felügyelt identitások regionális erőforrásként jönnek létre, az Azure AD-ben létrehozott társított [egyszerű](../develop/app-objects-and-service-principals.md#service-principal-object) szolgáltatásnév (SPN) globálisan elérhető. Az egyszerű szolgáltatás bármely Azure-régióból felhasználható, a rendelkezésre állása pedig az Azure AD rendelkezésre állásának függvénye. Ha például egy felhasználóhoz rendelt felügyelt identitást hozott létre a South-Central régióban, és ez a régió elérhetetlenné válik, akkor ez a probléma csak a felügyelt identitásban lévő [sík tevékenységek vezérlését](../../azure-resource-manager/management/control-plane-and-data-plane.md) befolyásolja.  A felügyelt identitások használatára már konfigurált erőforrások által végrehajtott tevékenységeket nem érinti a program.

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Az Azure-erőforrások felügyelt identitásai működnek az Azure Cloud Services?

Nem, az Azure-Cloud Services nem támogatja az Azure-erőforrások felügyelt identitásának támogatását.

### <a name="what-is-the-credential-associated-with-a-managed-identity-how-long-is-it-valid-and-how-often-is-it-rotated"></a>Mi a felügyelt identitáshoz társított hitelesítő adat? Mennyi ideig érvényes és milyen gyakran forog?

> [!NOTE]
> A felügyelt identitások hitelesítése egy belső megvalósítási részlet, amely figyelmeztetés nélkül változhat.

A felügyelt identitások tanúsítványalapú hitelesítést használnak. Az egyes felügyelt identitások hitelesítő adatai 90 napon belül lejárnak, és a rendszer 45 nap után gördült le.

### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Mi a felügyelt identitások biztonsági határa az Azure-erőforrásokhoz?

Az identitás biztonsági határa az az erőforrás, amelyhez hozzá van rendelve. Az Azure-erőforrások felügyelt identitással rendelkező virtuális gépek biztonsági határa például a virtuális gép. Az adott virtuális gépen futó kódok meghívhatják az Azure-erőforrások végpontjának felügyelt identitásait, és jogkivonatokat kérhetnek. Ez a hasonló élmény az Azure-erőforrások felügyelt identitásait támogató egyéb erőforrásokkal.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>Milyen identitást IMDS az alapértelmezett érték, ha nem ad meg identitást a kérelemben?

- Ha a rendszerhez rendelt felügyelt identitás engedélyezve van, és nincs megadva identitás a kérelemben, a IMDS alapértelmezés szerint a rendszerhez rendelt felügyelt identitást fogja használni.
- Ha a rendszerhez rendelt felügyelt identitás nincs engedélyezve, és csak egy felhasználóhoz rendelt felügyelt identitás létezik, a IMDS alapértelmezés szerint az adott felhasználóhoz rendelt felügyelt identitást fogja használni. 
- Ha a rendszerhez rendelt felügyelt identitás nincs engedélyezve, és több felhasználó által hozzárendelt felügyelt identitás létezik, akkor a kérésben a felügyelt identitás megadása szükséges.

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>A felügyelt identitások automatikusan újra létrejönnek, ha egy előfizetést másik könyvtárba helyezek át?

Nem. Ha egy előfizetést másik könyvtárba helyez át, akkor manuálisan újra létre kell hoznia őket, és újra meg kell adnia az Azure szerepkör-hozzárendeléseket.
- A rendszerhez rendelt felügyelt identitások esetében: letiltás és újbóli engedélyezés. 
- A felhasználóhoz rendelt felügyelt identitások esetében: törölje, hozza létre újra, majd csatolja újra a szükséges erőforrásokhoz (például virtuális gépekhez)

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>Használhatok egy felügyelt identitást egy másik címtárban/bérlőben lévő erőforrás eléréséhez?

Nem. A felügyelt identitások jelenleg nem támogatják a könyvtárak közötti forgatókönyveket. 

### <a name="what-azure-rbac-permissions-are-required-to-managed-identity-on-a-resource"></a>Milyen Azure RBAC-engedélyekre van szükség egy erőforrás felügyelt identitásához? 

- Rendszer által hozzárendelt felügyelt identitás: írási engedélyre van szüksége az erőforráson. Virtuális gépek esetében például a Microsoft.Compute/virtualMachines/write engedélyre van szükség. Ez a művelet az erőforrás-specifikus beépített szerepkörök, például a [virtuális gépek közreműködője](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)része.
- Felhasználó által hozzárendelt felügyelt identitás: írási engedélyre van szüksége az erőforráson. Virtuális gépek esetében például a Microsoft.Compute/virtualMachines/write engedélyre van szükség. A felügyelt identitás- [kezelő](../../role-based-access-control/built-in-roles.md#managed-identity-operator) szerepkör-hozzárendelésen felül a felügyelt identitáson kívül is.

### <a name="how-do-i-prevent-the-creation-of-user-assigned-managed-identities"></a>Hogyan a felhasználó által hozzárendelt felügyelt identitások létrehozásának megakadályozása?

Megtarthatja, hogy a felhasználók az [Azure Policy](../../governance/policy/overview.md) használatával hozzanak létre felhasználó által hozzárendelt felügyelt identitásokat

- Lépjen a [Azure Portalra](https://portal.azure.com) , és nyissa meg a **házirendet**.
- **Definíciók** kiválasztása
- Válassza a **+ szabályzat definíciója** lehetőséget, és adja meg a szükséges információkat.
- A házirend-szabály szakaszban illessze be

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

- Navigáljon az erőforráscsoportok elemre.
- Keresse meg a teszteléshez használt erőforráscsoportot.
- A bal oldali menüben válassza a **szabályzatok** lehetőséget.
- Válassza a **házirend kiosztása** elemet.
- Az **alapok** szakaszban adja meg a következőket:
    - **Hatókör** A teszteléshez használt erőforráscsoport
    - **Házirend-definíció**: a korábban létrehozott szabályzat.
- Hagyja meg az összes többi beállítást az alapértelmezett értékeken, majd válassza a **felülvizsgálat + létrehozás** lehetőséget.

Ezen a ponton sikertelen lesz a felhasználó által hozzárendelt felügyelt identitások létrehozása az erőforráscsoporthoz.

  ![Szabályzat megsértése](./media/known-issues/policy-violation.png)

## <a name="known-issues"></a>Ismert problémák

### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>Az "Automation-parancsfájl" sikertelen, amikor a séma exportálását kísérli meg az Azure-erőforrások bővítmény felügyelt identitásai számára

Ha az Azure-erőforrások felügyelt identitása engedélyezve van egy virtuális gépen, a következő hibaüzenet jelenik meg, amikor a virtuális gép "Automation-parancsfájl" funkcióját vagy az erőforráscsoport használatát kísérli meg:

![Felügyelt identitások az Azure-erőforrások automatizálási parancsfájljának exportálási hibája](./media/msi-known-issues/automation-script-export-error.png)

Az Azure Resources virtuálisgép-bővítmény felügyelt identitásai (2019 januárjában) jelenleg nem támogatják a sémák erőforráscsoport-sablonba való exportálásának lehetőségét. Ennek eredményeképpen a generált sablon nem jeleníti meg a konfigurációs paramétereket az Azure-erőforrások felügyelt identitásának engedélyezéséhez az erőforráson. Ezeket a részeket manuálisan is hozzáadhatja az Azure-beli virtuális gépeken található [felügyelt identitások konfigurálása](qs-configure-template-windows-vm.md)az Azure-beli virtuális gépeken sablonok használatával című részben ismertetett példákkal.

Ha a séma-exportálási funkció elérhetővé válik a felügyelt identitások számára az Azure-erőforrások virtuálisgép-bővítmény számára (amely a 2019 januárjában van tervezve), akkor a rendszer a virtuálisgép- [bővítményeket tartalmazó erőforráscsoportok exportálását fogja tartalmazni](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

### <a name="vm-fails-to-start-after-being-moved-from-resource-group-or-subscription"></a>A virtuális gép nem indul el, miután áthelyezték az erőforráscsoportot vagy az előfizetést

Ha a virtuális gépet futó állapotba helyezi, az áthelyezés során továbbra is futni fog. Az áthelyezést követően azonban, ha a virtuális gép leáll és újraindul, nem fog elindulni. Ez a probléma azért fordul elő, mert a virtuális gép nem frissíti az Azure-erőforrások identitásához tartozó felügyelt identitásokra mutató hivatkozást, és továbbra is a régi erőforráscsoporthoz mutat.

**Áthidaló megoldás** 
 
Indítson el egy frissítést a virtuális gépen, hogy helyes értékeket kapjon az Azure-erőforrások felügyelt identitásai számára. A virtuális gép tulajdonságainak módosításával frissítheti az Azure-erőforrások identitásához tartozó felügyelt identitásokra mutató hivatkozást. Beállíthat például egy új címke értéket a virtuális gépen a következő paranccsal:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --set tags.fixVM=1
```
 
Ez a parancs egy "fixVM" nevű új címkét állít be a virtuális gépen 1 értékkel. 
 
Ennek a tulajdonságnak a beállításával a virtuális gép a megfelelő felügyelt identitásokkal frissíti az Azure-erőforrások erőforrás URI azonosítóját, majd el kell tudnia indítani a virtuális gépet. 
 
A virtuális gép elindítása után a címkét a következő parancs használatával távolíthatja el:

```azurecli-interactive
az vm update -n <VM Name> -g <Resource Group> --remove tags.fixVM
```

### <a name="transferring-a-subscription-between-azure-ad-directories"></a>Előfizetés továbbítása az Azure AD-címtárak között

A felügyelt identitások nem frissülnek, ha egy előfizetést áthelyeznek/átvisznek egy másik könyvtárba. Ennek eredményeképpen minden létező, rendszer által hozzárendelt vagy felhasználó által hozzárendelt felügyelt identitás megszakad. 

Megkerülő megoldás a felügyelt identitások számára egy másik könyvtárba áthelyezett előfizetésben:

 - A rendszerhez rendelt felügyelt identitások esetében: letiltás és újbóli engedélyezés. 
 - A felhasználóhoz rendelt felügyelt identitások esetében: törölje, hozza létre újra, majd csatolja újra a szükséges erőforrásokhoz (például virtuális gépekhez)

További információ: [Azure-előfizetés átadása egy másik Azure AD-címtárba](../../role-based-access-control/transfer-subscription.md).

### <a name="moving-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>Felhasználó által hozzárendelt felügyelt identitás áthelyezése egy másik erőforráscsoporthoz vagy előfizetésbe

A felhasználóhoz rendelt felügyelt identitás más erőforráscsoporthoz való áthelyezése nem támogatott.
