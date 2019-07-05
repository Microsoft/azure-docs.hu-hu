---
title: Azure szerepkör-beállítások konfigurálása a PIM - Azure Active Directory |} A Microsoft Docs
description: Ismerje meg az Azure-erőforrás szerepkör-beállítások konfigurálása az Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6da92ccd50707e676a1f5d583fe22ae8b3d8b73
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476319"
---
# <a name="configure-azure-resource-role-settings-in-pim"></a>A PIM Azure szerepkör-beállítások konfigurálása

Azure-erőforrás beállítások konfigurálásakor adja meg az Azure-erőforrást az Azure Active Directory (Azure AD) Privileged Identity Management (PIM) szerepkör-hozzárendelések alkalmazott alapértelmezett beállításokat. A következő eljárások használatával konfigurálhatja a jóváhagyási munkafolyamatot, és adja meg, akik jóváhagyhatják vagy megtagadhatják a kérelmeket.

## <a name="open-role-settings"></a>Nyissa meg a szerepkör-beállítások

Kövesse az alábbi lépéseket egy Azure-erőforrás szerepkör beállításainak megnyitásához.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) egy felhasználóval, amely tagja a [kiemelt szerepkörű rendszergazda](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) szerepkör.

1. Nyissa meg **az Azure AD Privileged Identity Management**.

1. Kattintson a **Azure-erőforrások**.

1. Kattintson az erőforrás szeretne felügyelni, mint például egy előfizetést vagy felügyeleti csoportot.

    ![Azure-erőforrások lapon kezelhetők erőforrások listázása](./media/pim-resource-roles-configure-role-settings/resources-list.png)

1. Kattintson a **szerepkör-beállítások**.

    ![Szerepkör-beállítások lapon listázása az Azure-erőforrásszerepkörök](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. Kattintson a szerepkör, amelynek beállításait konfigurálni szeretné.

    ![Szerepkör beállítások részletei oldalon több hozzárendelés és aktiválás beállítások listázása](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. Kattintson a **szerkesztése** a szerepkör-beállítások panel megnyitásához.

    ![Szerepkör beállításai lapon-hozzárendelés és aktiválás beállításainak frissítése beállításokkal szerkesztése](./media/pim-resource-roles-configure-role-settings/resources-role-settings-edit.png)

    A szerepkör beállítás panelen az egyes szerepkörökhöz vannak több beállítást is beállíthat.

## <a name="assignment-duration"></a>Hozzárendelés időtartama

Szerepkör beállításainak konfigurálásakor minden hozzárendelés-típus (elérhető és aktív) két hozzárendelés időtartama lehetőség közül választhat. Ezek a beállítások a alapértelmezett maximális időtartamot, amikor egy tag szerepkör van rendelve, a PIM a válnak.

Ezek közül kiválaszthatja **jogosult** hozzárendelés időtartam beállításai:

| | |
| --- | --- |
| **Állandó jogosult hozzárendelés engedélyezése** | Állandó jogosult tagsági erőforrás rendszergazdák rendelhet hozzá. |
| **Jogosult hozzárendelés után lejár.** | Erőforrás rendszergazdák megkövetelhetik, hogy az összes jogosult hozzárendelés rendelkezik-e a megadott kezdő és záró dátumát. |

És a egy ezek közül kiválaszthatja **aktív** hozzárendelés időtartam beállításai:

| | |
| --- | --- |
| **Állandó aktív hozzárendelés engedélyezése** | Erőforrás-rendszergazdák állandó használatához aktív tagság rendelhet hozzá. |
| **Aktív hozzárendelés után lejár.** | Erőforrás rendszergazdák megkövetelhetik, hogy az összes aktív hozzárendelések rendelkezik-e a megadott kezdő és záró dátumát. |

> [!NOTE] 
> Erőforrás-rendszergazdák által meg lehet újítani a megadott befejezési dátummal rendelkező összes hozzárendelést. Emellett tagokat is kezdeményezhető a kérések az önkiszolgáló [meghosszabbítása vagy megújítása a szerepkör-hozzárendelések](pim-resource-roles-renew-extend.md).

## <a name="require-multi-factor-authentication"></a>Többtényezős hitelesítés megkövetelése

A PIM biztosít két különböző forgatókönyvekhez kényszerítési az Azure multi-factor Authentication (MFA) nem kötelező.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Többtényezős hitelesítés megkövetelése aktív hozzárendeléskor

Bizonyos esetekben érdemes tag hozzárendelése szerepkörhöz (egy nap, a példában) rövid időre. Ebben az esetben nem szükséges a hozzárendelt tagokkal aktiválás kéréséhez. Ebben a forgatókönyvben a PIM nem MFA kényszerítése, ha a tag használja a saját szerepkör-hozzárendelés, mivel azok még aktívak attól a pillanattól kezdve hozzá vannak rendelve a szerepkörben.

Annak érdekében, hogy az erőforrás-kezelője a hozzárendelés foglaltak akik mondják, kényszerítheti a többtényezős Hitelesítést aktív hozzárendeléskor ellenőrzésével a **többtényezős hitelesítés megkövetelése aktív hozzárendeléskor** mezőbe.

### <a name="require-multi-factor-authentication-on-activation"></a>Többtényezős hitelesítés megkövetelése aktiváláskor

Megkövetelheti az MFA futtatása, mielőtt aktiválna a szerepkör jogosult tagjai. Ez a folyamat biztosítja, hogy a felhasználó az aktiválás, akinek mondják ésszerű bizonyossággal kér. Kiemelt fontosságú erőforrások helyzetekben kényszerítése ezt a beállítást védi, ha a felhasználói fiók esetleg sérült a biztonsága.

Az MFA futtatása előtt aktiválási jogosult tag van szüksége, ellenőrizze a **multi-factor Authentication megkövetelése aktiváláskor** mezőbe.

További információkért lásd: [multi-factor authentication (MFA) és a PIM](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Aktiválás maximális időtartama

Használja a **aktiválás maximális időtartama** csúszka, hogy a szerepkör aktív marad-e után járjon le (óra), a maximális idő beállítása. Ez az érték 1 és 24 óra közötti lehet.

## <a name="require-justification"></a>Indoklás megkövetelése

Megkövetelheti, hogy a tagok adja meg az aktív hozzárendeléskor, vagy ha aktiválja. Indoklás megkövetelése, hogy ellenőrizze a **megkövetelése aktív hozzárendeléskor indoklás** mezőben vagy a **indoklás megkövetelése aktiváláskor** mezőbe.

## <a name="require-approval-to-activate"></a>Aktiválásához jóváhagyás szükséges

Ha szeretné a szerepkör aktiválásához jóváhagyás szükséges, kövesse az alábbi lépéseket.

1. Ellenőrizze a **aktiválásához jóváhagyás szükséges** jelölőnégyzetet.

1. Kattintson a **jóváhagyók kiválasztása** válassza a-tag vagy a csoport panel megnyitásához.

    ![Egy tag vagy a csoport panelen válassza ki a jóváhagyók kiválasztása](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Válasszon ki legalább egy tagot vagy csoportot, és kattintson a **kiválasztása**. A tagok és csoportok tetszőleges kombinációját is hozzáadhat. Ki kell választania legalább egy jóváhagyó. Nincsenek nem alapértelmezett jóváhagyónak.

    A beállításokat a kijelölt jóváhagyók listájában jelenik meg.

1. Miután megadta az összes szerepkör beállítást, kattintson a **frissítés** a módosítások mentéséhez.

## <a name="next-steps"></a>További lépések

- [Rendelje hozzá a PIM az Azure-erőforrásszerepkörök](pim-resource-roles-assign-roles.md)
- [Az Azure-erőforrások szerepköreihez tartozó biztonsági riasztások konfigurálása az PIM-ben](pim-resource-roles-configure-alerts.md)
