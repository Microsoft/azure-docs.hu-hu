---
title: Jelentések megtekintése a jogosultságok kezelése & naplófájljaiban – Azure AD
description: Megtudhatja, hogyan tekintheti meg a felhasználó-hozzárendelési jelentést és a naplókat Azure Active Directory jogosultságok kezelésében.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: jocastel-MSFT
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6eb44c1efd683b6febe9a355ef72c80cc6f2e40d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97746626"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management"></a>Jelentések és naplók megtekintése az Azure AD-jogosultságok kezelésében

Az Azure AD-jogosultságok kezelési jelentései és az Azure AD-napló további részleteket tartalmaz arról, hogy a felhasználók milyen erőforrásokhoz férhetnek hozzá. Rendszergazdaként megtekintheti a felhasználók hozzáférési csomagjait és erőforrás-hozzárendeléseit, valamint naplózási célból megtekintheti a kérelmek naplóit, illetve meghatározhatja a felhasználó kérelmének állapotát. Ez a cikk a jogosultsági felügyeleti jelentések és az Azure AD naplóinak használatát ismerteti.

Tekintse meg az alábbi videót, amelyből megtudhatja, hogy miként tekintheti meg, hogy a felhasználók hogyan férhetnek hozzá a jogosultságok kezeléséhez:

>[!VIDEO https://www.youtube.com/embed/omtNJ7ySjS0]

## <a name="view-access-packages-for-a-user"></a>Felhasználó hozzáférési csomagjainak megtekintése

Ez a jelentés lehetővé teszi a felhasználó által igényelhető összes hozzáférési csomag, valamint a felhasználóhoz jelenleg hozzárendelt hozzáférési csomagok listázását.

**Előfeltételként szükséges szerepkör:** Globális rendszergazda vagy felhasználói rendszergazda

1. Kattintson a **Azure Active Directory** , majd az **identitás-szabályozás** elemre.

1. A bal oldali menüben kattintson a **jelentések** elemre.

1. Kattintson **a felhasználók hozzáférési csomagjai** elemre.

1. Kattintson a **felhasználók kiválasztása** lehetőségre a felhasználók kiválasztása ablaktábla megnyitásához.

1. Keresse meg a felhasználót a listában, majd kattintson a **kiválasztás** elemre.

    A kérhető **kérelem** lap megjeleníti a felhasználó által kérhető hozzáférési csomagok listáját. Ezt a listát a hozzáférési csomagokhoz definiált [kérési szabályzatok](entitlement-management-access-package-request-policy.md#for-users-in-your-directory) határozzák meg. 

    ![Felhasználók hozzáférési csomagjai](./media/entitlement-management-reports/access-packages-report.png)

1. Ha egynél több erőforrás-szerepkör vagy-szabályzat van egy hozzáférési csomaghoz, kattintson az erőforrás-szerepkörök vagy-házirendek bejegyzésre a kijelölés részleteinek megtekintéséhez.

1. A **hozzárendelt** lapra kattintva megtekintheti a felhasználóhoz jelenleg hozzárendelt hozzáférési csomagok listáját. Ha egy hozzáférési csomag hozzá van rendelve egy felhasználóhoz, az azt jelenti, hogy a felhasználó hozzáféréssel rendelkezik a hozzáférési csomagban található összes erőforrás-szerepkörhöz.

## <a name="view-resource-assignments-for-a-user"></a>Felhasználó erőforrás-hozzárendeléseinek megtekintése

Ez a jelentés lehetővé teszi, hogy a jogosultságok kezelésében jelenleg a felhasználóhoz rendelt erőforrásokat sorolja fel. Vegye figyelembe, hogy ez a jelentés a jogosultsági felügyelettel felügyelt erőforrásokhoz használható. Előfordulhat, hogy a felhasználó a jogosultságok felügyelete kívül más erőforrásokhoz is hozzáfér a címtárban.

**Előfeltételként szükséges szerepkör:** Globális rendszergazda vagy felhasználói rendszergazda

1. Kattintson a **Azure Active Directory** , majd az **identitás-szabályozás** elemre.

1. A bal oldali menüben kattintson a **jelentések** elemre.

1. Kattintson **a felhasználó erőforrás-hozzárendelései** elemre.

1. Kattintson a **felhasználók kiválasztása** lehetőségre a felhasználók kiválasztása ablaktábla megnyitásához.

1. Keresse meg a felhasználót a listában, majd kattintson a **kiválasztás** elemre.

    Megjelenik a felhasználóhoz jelenleg hozzárendelt erőforrások listája. A lista tartalmazza azt a hozzáférési csomagot és házirendet is, amelyhez az erőforrás-szerepkör származik, valamint a hozzáférés kezdési és befejezési dátumát.
    
    Ha egy felhasználó ugyanahhoz az erőforráshoz van hozzáférése két vagy több csomagban, akkor a nyílra kattintva megtekintheti az egyes csomagokat és házirendeket.

    ![Felhasználó erőforrás-hozzárendelései](./media/entitlement-management-reports/resource-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>Felhasználói kérés állapotának meghatározása

Ha további részleteket szeretne megtudni arról, hogy a felhasználó hogyan kért és kapott hozzáférést egy hozzáférési csomaghoz, használhatja az Azure AD naplót. A és a kategóriákban található naplóbejegyzések használatával `EntitlementManagement` `UserManagement` További részleteket tudhat meg az egyes kérelmek feldolgozási lépéseiről.  

1. Kattintson a **Azure Active Directory** , majd a **naplók** elemre.

1. A felső részen módosítsa a **kategóriát** a vagy a értékre `EntitlementManagement` `UserManagement` a keresett naplózási rekordtól függően.  

1. Kattintson az **Alkalmaz** gombra.

1. A naplók letöltéséhez kattintson a **Download (Letöltés**) gombra.

Amikor az Azure AD új kérést kap, egy naplózási rekordot ír, amelyben a **Kategória** szerepel, `EntitlementManagement` és a **tevékenység** általában `User requests access package assignment` .  A Azure Portalban létrehozott közvetlen hozzárendelés esetén a naplózási rekord **tevékenység** mezőjében a `Administrator directly assigns user to access package` **ActorUserPrincipalName** azonosítja a hozzárendelést végrehajtó felhasználót.

Az Azure AD további naplózási rekordokat fog írni, amíg a kérelem folyamatban van, beleértve a következőket:

| Kategória | Tevékenység | Kérelem állapota |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | A kérelem nem igényel jóváhagyást |
| `UserManagement` | `Create request approval` | A kérelem jóváhagyást igényel |
| `UserManagement` | `Add approver to request approval` | A kérelem jóváhagyást igényel |
| `EntitlementManagement` | `Approve access package assignment request` | Kérelem jóváhagyva |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |A kérelem jóváhagyva vagy nem igényel jóváhagyást |

Amikor egy felhasználóhoz hozzáférési hozzáférés van rendelve, az Azure AD egy naplózási rekordot ír a `EntitlementManagement` kategóriába a **tevékenységgel** `Fulfill access package assignment` .  Az **ActorUserPrincipalName** mező azonosítja a hozzáférést fogadó felhasználót.

Ha a hozzáférés nem lett hozzárendelve, akkor az Azure AD egy naplózási rekordot ír a `EntitlementManagement` **tevékenység** kategóriába, `Deny access package assignment request` Ha a kérést egy jóváhagyó megtagadta, vagy `Access package assignment request timed out (no approver action taken)` Ha a kérelem túllépte az időkorlátot a jóváhagyó jóváhagyása előtt.

Ha a felhasználó hozzáférési csomagjának hozzárendelése lejár, a felhasználó megszakította vagy eltávolítja a rendszergazdát, akkor az Azure AD naplózási rekordot ír a `EntitlementManagement` **tevékenységgel** rendelkező kategóriához `Remove access package assignment` .

## <a name="next-steps"></a>Következő lépések

- [Jelentések és naplók archiválása](entitlement-management-logs-and-reporting.md)
- [Az Azure AD-jogosultságok kezelése – problémamegoldás](entitlement-management-troubleshoot.md)
- [Gyakori forgatókönyvek](entitlement-management-scenarios.md)
