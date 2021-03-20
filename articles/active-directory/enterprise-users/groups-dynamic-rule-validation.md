---
title: A dinamikus csoporttagság (előzetes verzió) szabályainak érvényesítése – Azure AD | Microsoft Docs
description: Tagok tesztelése a Azure Active Directory dinamikus csoportjaihoz tartozó tagsági szabályokkal.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: yukarppa
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1048284e8e7a492bf0810a16e29409546ed414b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96547559"
---
# <a name="validate-a-dynamic-group-membership-rule-preview-in-azure-active-directory"></a>Dinamikus csoporttagság-szabály (előzetes verzió) ellenőrzése Azure Active Directory

A Azure Active Directory (Azure AD) mostantól lehetővé teszi a dinamikus csoportok szabályainak érvényesítését (nyilvános előzetes verzió). A **szabályok érvényesítése** lapon ellenőrizheti, hogy a dinamikus szabály a minta csoport tagjaira vonatkozik-e, hogy erősítse meg, hogy a szabály a várt módon működik-e. Dinamikus csoportokra vonatkozó szabályok létrehozásakor vagy frissítésekor a rendszergazdák tudni szeretnék, hogy egy felhasználó vagy egy eszköz tagja lesz-e a csoportnak. Ez segít annak kiértékelésében, hogy a felhasználó vagy az eszköz megfelel-e a szabályok feltételeinek, és segítséget nyújt a hibaelhárításban, ha nem várt tagság.

## <a name="step-by-step-walk-through"></a>Lépésről lépésre haladó útmutató

Első lépésként lépjen a **Azure Active Directory**  >  **csoportok** elemre. Válasszon ki egy meglévő dinamikus csoportot, vagy hozzon létre egy új dinamikus csoportot, és kattintson a dinamikus tagsági szabályok elemre. Ezután megtekintheti a **szabályok érvényesítése** lapot.

![A szabályok érvényesítése lap megkeresése és egy meglévő szabály elindítása](./media/groups-dynamic-rule-validation/validate-tab.png)

A **szabályok érvényesítése** lapon kiválaszthatja a felhasználókat a tagságuk ellenőrzéséhez. 20 felhasználó vagy eszköz egyszerre is kiválasztható.

![Felhasználók hozzáadása a meglévő szabály érvényesítéséhez](./media/groups-dynamic-rule-validation/validate-tab-add-users.png)

Miután kiválasztotta a felhasználókat vagy az eszközöket a választóból, és **bejelöli a lehetőséget**, az érvényesítés automatikusan elindul, és a rendszer az érvényesítési eredményeket jeleníti meg.

![A szabály ellenőrzésének eredményeinek megtekintése](./media/groups-dynamic-rule-validation/validate-tab-results.png)

Az eredményekből megtudhatja, hogy a felhasználó tagja-e a csoportnak. Ha a szabály érvénytelen, vagy hálózati probléma van, az eredmény **ismeretlenként** jelenik meg. **Ismeretlen** esetén a részletes hibaüzenet leírja a problémát és a szükséges műveleteket.

![A szabály ellenőrzésének eredményeinek megjelenítése](./media/groups-dynamic-rule-validation/validate-tab-view-details.png)

Megváltoztathatja a tagság szabályát és érvényesítését. Ha szeretné megtekinteni, hogy a felhasználó miért nem tagja a csoportnak, kattintson a részletek megtekintése elemre, és az ellenőrzés részletei a szabályt alkotó kifejezések eredményét jelenítik meg. A kilépéshez kattintson **az OK** gombra.

## <a name="next-steps"></a>Következő lépések

- [Csoportok dinamikus tagsági szabályai](groups-dynamic-membership.md)
