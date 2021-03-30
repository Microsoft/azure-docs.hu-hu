---
title: Csoport hozzáadása vagy eltávolítása egy másik csoportból – Azure AD
description: Útmutatás a csoportok hozzáadásáról vagy eltávolításáról egy másik csoportból a Azure Active Directory használatával.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 10/19/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: cbd7eecd80b528b1d20d016d51c985bc24d1301d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92371800"
---
# <a name="add-or-remove-a-group-from-another-group-using-azure-active-directory"></a>Csoport hozzáadása vagy eltávolítása egy másik csoportból a Azure Active Directory használatával
Ebből a cikkből megtudhatja, hogyan adhat hozzá és távolíthat el egy csoportot egy másik csoportból Azure Active Directory használatával.

>[!Note]
>Ha a szülőobjektum törlését kísérli meg, tekintse [meg a csoport és a tagok frissítése vagy törlése](active-directory-groups-delete-group.md)című témakört.

## <a name="add-a-group-to-another-group"></a>Csoport hozzáadása egy másik csoporthoz
Hozzáadhat egy meglévő biztonsági csoportot egy másik meglévő biztonsági csoporthoz (más néven beágyazott csoporthoz), egy csoporttag (alcsoport) és egy szülő csoport létrehozásához. A csoporttag örökli a szülő csoport attribútumait és tulajdonságait, és menti a konfigurációs időt.

>[!Important]
>Jelenleg nem támogatjuk a következőket:<ul><li>Csoportok hozzáadása a helyszíni Active Directory szinkronizált csoportokhoz.</li><li>Biztonsági csoportok hozzáadása Microsoft 365 csoportokhoz.</li><li>Microsoft 365 csoportok hozzáadása biztonsági csoportokhoz vagy más Microsoft 365-csoportokhoz.</li><li>Alkalmazások kiosztása beágyazott csoportokhoz.</li><li>Licencek alkalmazása beágyazott csoportokra.</li><li>Terjesztési csoportok hozzáadása beágyazási forgatókönyvekben.</li></ul>

### <a name="to-add-a-group-as-a-member-of-another-group"></a>Csoport hozzáadása egy másik csoport tagjaként

1. A címtár eléréséhez globális rendszergazdai fiókkal jelentkezzen be az [Azure portálra](https://portal.azure.com).

2. Válassza a **Azure Active Directory**, majd a **csoportok** lehetőséget.

3. A **csoportok – minden csoport** lapon keresse meg és válassza ki azt a csoportot, amelynek egy másik csoport tagjának kell lennie. Ebben a gyakorlatban a **Mdm Policy-West** csoportot használjuk.

    >[!Note]
    >A csoportot egyszerre csak egy csoportba lehet felvenni. Emellett a **csoport kiválasztása** mező kiszűri a megjelenítést a felhasználó vagy az eszköz neve bármely részének megfelelő bejegyzés alapján. A helyettesítő karakterek azonban nem támogatottak.

    ![Csoportok – minden csoport lap MDM házirenddel – West Group kiválasztva](media/active-directory-groups-membership-azure-portal/group-all-groups-screen.png)

4. A **Mdm házirend-Nyugat-csoport tagsága** lapon válassza a **csoporttagságok lehetőséget, válassza a** **Hozzáadás** lehetőséget, keresse meg azt a csoportot, amelyben a csoport tagja lesz, majd válassza a **kiválasztás** lehetőséget. Ebben a gyakorlatban a **Mdm szabályzatot használjuk – az összes szervezeti** csoportot.

    A **Mdm Policy-West** csoport most már tagja az **Mdm házirendnek – az összes szervezeti** csoportnak, a Mdm házirend összes tulajdonságának és konfigurációjának öröklése – minden szervezeti csoport.

    ![Csoporttagság létrehozása csoport hozzáadásával egy másik csoporthoz](media/active-directory-groups-membership-azure-portal/group-add-group-membership.png)

5. A csoport és a tag kapcsolat megtekintéséhez tekintse át a **Mdm házirend-Nyugat-csoport tagságok** lapot.

6. A csoport és a tag kapcsolat részletesebb megjelenítéséhez válassza ki a csoport nevét (**Mdm Policy-all org**), és vessen egy pillantást a **Mdm Policy-West** oldal részleteire.

## <a name="remove-a-group-from-another-group"></a>Csoport eltávolítása egy másik csoportból
Egy meglévő biztonsági csoportot is eltávolíthat egy másik biztonsági csoportból. A csoport eltávolítása azonban eltávolítja a tagjainak örökölt attribútumait és tulajdonságait is.

### <a name="to-remove-a-member-group-from-another-group"></a>Tag csoport eltávolítása egy másik csoportból
1. A **csoportok – minden csoport** lapon keresse meg és válassza ki azt a csoportot, amelyet el szeretne távolítani egy másik csoport tagjaként. Ebben a gyakorlatban ismét a **Mdm Policy-West** csoportot használjuk.

2. A **Mdm házirend – Nyugat áttekintése** **lapon válassza a csoporttagságok lehetőséget**.

3. Válassza ki a **Mdm szabályzatot – az összes szervezeti** csoportot a **Mdm Policy-West-Group Memberships** lapon, majd válassza az **Eltávolítás** lehetőséget a **Mdm Policy-West** oldal részletei közül.

    ![Csoporttagság lap, amely a tag és a csoport részleteit is tartalmazza](media/active-directory-groups-membership-azure-portal/group-membership-remove.png)

## <a name="additional-information"></a>További információ
E cikkekben további információk találhatók az Azure Active Directoryval kapcsolatban.

- [Csoportok és tagok megtekintése](active-directory-groups-view-azure-portal.md)

- [Alapszintű csoport létrehozása és tagok hozzáadása](active-directory-groups-create-azure-portal.md)

- [Tagok hozzáadása vagy eltávolítása egy csoportból](active-directory-groups-members-azure-portal.md)

- [A csoport beállításainak szerkesztése](active-directory-groups-settings-azure-portal.md)

- [Csoport használata SaaS-alkalmazásokhoz való hozzáférés kezelésére](../enterprise-users/groups-saasapps.md)

- [Forgatókönyvek, korlátozások és ismert problémák csoportok használatával a licencelés kezeléséhez Azure Active Directory](../enterprise-users/licensing-group-advanced.md#limitations-and-known-issues)
