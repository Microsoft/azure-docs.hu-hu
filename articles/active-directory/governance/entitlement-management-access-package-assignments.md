---
title: Hozzáférési csomaghoz tartozó hozzárendelések megtekintése, hozzáadása és eltávolítása az Azure AD-jogosultságok kezelésében – Azure Active Directory
description: Megtudhatja, hogyan tekintheti meg, adhatja hozzá és távolíthat el egy hozzáférési csomaghoz tartozó hozzárendeléseket Azure Active Directory jogosultságok felügyeletében.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18bc7ca9e108a35fd27f7b3155f186221a8caae3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90980066"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>Hozzáférési csomaghoz tartozó hozzárendelések megtekintése, hozzáadása és eltávolítása az Azure AD-jogosultságok kezelésében

Az Azure AD-jogosultságok kezelése szolgáltatásban megtekintheti, hogy kinek van hozzárendelve hozzáférési csomagokhoz, azok házirendjéhez és állapotához. Ha egy hozzáférési csomag megfelelő szabályzattal rendelkezik, közvetlenül is hozzárendelheti a felhasználót egy hozzáférési csomaghoz. Ez a cikk a hozzáférési csomagok hozzárendeléseinek megtekintését, hozzáadását és eltávolítását ismerteti.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-jogosultságok felügyeletének használatához és a felhasználók a csomagok eléréséhez való hozzárendeléséhez a következő licencek egyike szükséges:

- Prémium szintű Azure AD P2
- Enterprise Mobility + Security (EMS) E5 licenc

## <a name="view-who-has-an-assignment"></a>Hozzárendelést használó nézet megtekintése

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa, Access Package Manager vagy Access Package hozzárendelés-kezelő

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás** elemre.

1. A bal oldali menüben kattintson a **hozzáférési csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. Kattintson a **hozzárendelések** elemre az aktív hozzárendelések listájának megtekintéséhez.

    ![Egy hozzáférési csomag hozzárendeléseinek listája](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. A további részletek megtekintéséhez kattintson egy konkrét hozzárendelésre.

1. Ha meg szeretné tekinteni azoknak a hozzárendeléseknek a listáját, amelyeken nincs megfelelően kiépítve az összes erőforrás-szerepkör, kattintson a szűrő állapotára, és válassza a **kézbesítés** lehetőséget.

    A kézbesítési hibákkal kapcsolatos további részletekért tekintse meg a felhasználó megfelelő kérését a **kérelmek** lapon.

1. A lejárt hozzárendelések megjelenítéséhez kattintson a szűrő állapotára, és válassza a **lejárt** lehetőséget.

1. A szűrt lista CSV-fájljának letöltéséhez kattintson a **Letöltés** gombra.

### <a name="viewing-assignments-programmatically"></a>A hozzárendelések programozott megtekintése

Microsoft Graph használatával is lekérheti a hozzárendeléseket egy hozzáférési csomagban.  Egy megfelelő szerepkörbe tartozó felhasználó, aki a delegált engedéllyel rendelkezik, `EntitlementManagement.ReadWrite.All` meghívhatja az API-t a [accessPackageAssignments listázására](/graph/api/accesspackageassignment-list?view=graph-rest-beta).

## <a name="directly-assign-a-user"></a>Felhasználó közvetlen kiosztása

Bizonyos esetekben előfordulhat, hogy közvetlenül egy adott felhasználót szeretne hozzárendelni egy hozzáférési csomaghoz, hogy a felhasználóknak ne kelljen átesniük a hozzáférési csomag igénylésének folyamatán. A felhasználók közvetlen hozzárendeléséhez a hozzáférési csomagnak rendelkeznie kell egy olyan házirenddel, amely lehetővé teszi a közvetlen rendszergazdai hozzárendeléseket.

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa, Access Package Manager vagy Access Package hozzárendelés-kezelő

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás** elemre.

1. A bal oldali menüben kattintson a **hozzáférési csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. A bal oldali menüben kattintson a **hozzárendelések** elemre.

1. Kattintson az **új hozzárendelés** lehetőségre a felhasználó hozzáadása a csomaghoz lehetőség megnyitásához.

    ![Hozzárendelések – felhasználó hozzáadása a csomag eléréséhez](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1. Kattintson a **felhasználók hozzáadása** elemre azon felhasználók kiválasztásához, akik számára a hozzáférési csomagot hozzá szeretné rendelni.

1. A **házirend kiválasztása** listából válassza ki azt a szabályzatot, amelyet a felhasználók jövőbeli kéréseinek és életciklusának szabályozására és nyomon követésére fog alkalmazni. Ha azt szeretné, hogy a kiválasztott felhasználók különböző házirend-beállításokkal rendelkezzenek, kattintson az **új házirend létrehozása** lehetőségre új szabályzat hozzáadásához.

1. Adja meg azt a dátumot és időpontot, ameddig a kiválasztott felhasználók hozzárendelésének kezdési és befejezési időpontja. Ha nincs megadva befejezési dátum, a rendszer a szabályzat életciklus-beállításait fogja használni.

1. Igény szerint megadhatja a közvetlen hozzárendelését a rekordok megőrzése érdekében.

1. A **Hozzáadás** gombra kattintva közvetlenül hozzárendelheti a kiválasztott felhasználókat a hozzáférési csomaghoz.

    Néhány pillanat elteltével kattintson a **frissítés** gombra, hogy megjelenjenek a felhasználók a hozzárendelések listájában.

### <a name="directly-assigning-users-programmatically"></a>Felhasználók közvetlen kiosztása programozott módon

Microsoft Graph használatával közvetlenül is hozzárendelhet egy felhasználót egy hozzáférési csomaghoz.  Egy megfelelő szerepkörbe tartozó felhasználó, aki a delegált engedéllyel rendelkezik, `EntitlementManagement.ReadWrite.All` meghívhatja az API-t [egy accessPackageAssignmentRequest létrehozására](/graph/api/accesspackageassignmentrequest-post?view=graph-rest-beta).

## <a name="remove-an-assignment"></a>Hozzárendelés eltávolítása

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa, Access Package Manager vagy Access Package hozzárendelés-kezelő

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás** elemre.

1. A bal oldali menüben kattintson a **hozzáférési csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. A bal oldali menüben kattintson a **hozzárendelések** elemre.
 
1. Jelölje be azon felhasználó melletti jelölőnégyzetet, amelynek hozzárendelését el szeretné távolítani a hozzáférési csomagból. 

1. Kattintson a bal oldali ablaktábla tetején található **Eltávolítás** gombra. 
 
    ![Hozzárendelések – felhasználó eltávolítása a hozzáférési csomagból](./media/entitlement-management-access-package-assignments/remove-assignment-select-remove-assignment.png)

    Ekkor megjelenik egy értesítés, amely tájékoztatja, hogy a hozzárendelés el lett távolítva. 

## <a name="next-steps"></a>Következő lépések

- [Hozzáférési csomag kérésének és beállításainak módosítása](entitlement-management-access-package-request-policy.md)
- [Jelentések és naplók megtekintése](entitlement-management-reports.md)