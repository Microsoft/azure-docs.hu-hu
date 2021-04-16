---
title: Hozzáférési csomag hozzárendelésének megtekintése, hozzáadása és eltávolítása az Azure AD-jogosultságkezelésben – Azure Active Directory
description: Megtudhatja, hogyan lehet megtekinteni, hozzáadni és eltávolítani egy hozzáférési csomag hozzárendelését a Azure Active Directory kezelésével.
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
ms.openlocfilehash: 6e00fe3761824462252ce4984beb754385f3eca9
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532144"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>Hozzáférési csomag hozzárendelésének megtekintése, hozzáadása és eltávolítása az Azure AD-jogosultságkezelésben

Az Azure AD-jogosultságkezelésben láthatja, hogy ki lett hozzárendelve a hozzáférési csomagokhoz, a szabályzatukhoz és az állapotukhoz. Ha egy hozzáférési csomag megfelelő szabályzatokkal rendelkezik, a felhasználót közvetlenül is hozzárendelheti egy hozzáférési csomaghoz. Ez a cikk a hozzáférési csomagok hozzárendelésének megtekintését, hozzáadását és eltávolítását ismerteti.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-jogosultságkezelés és a csomagokhoz való hozzáférés felhasználókhoz való hozzárendeléséhez a következő licencek egyikével kell lennie:

- Prémium szintű Azure AD P2
- Enterprise Mobility + Security (EMS) E5 licenc

## <a name="view-who-has-an-assignment"></a>A hozzárendeléshez hozzárendeltek megtekintése

**Előfeltételként szükséges szerepkör:** globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa, hozzáférésicsomag-kezelő vagy hozzáférésicsomag-hozzárendelés kezelője

1. A Azure Portal **kattintson** a Azure Active Directory, majd az **Identity Governance elemre.**

1. A bal oldali menüben kattintson a **Hozzáférési csomagok elemre,** majd nyissa meg a hozzáférési csomagot.

1. Kattintson **a Hozzárendelések** elemre az aktív hozzárendelések listájának a listájának a listájához.

    ![Hozzáférési csomaghoz való hozzárendelések listája](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. További részletekért kattintson egy adott hozzárendelésre.

1. Azon hozzárendelések listájának a megtekintése, amelyekhez nem lett megfelelően kiépítve az összes erőforrás-szerepkör, kattintson a szűrő állapotára, és válassza a **Delivering (Kézbesítés) lehetőséget.**

    A kézbesítési hibákkal kapcsolatos további részleteket a felhasználó megfelelő kérésének megkeresését a **Kérések oldalon találhatja** meg.

1. A lejárt hozzárendelések megtekintése előtt kattintson a szűrő állapotára, és válassza a **Lejárt lehetőséget.**

1. A szűrt lista CSV-fájlját a Letöltés gombra kattintva **töltheti le.**

### <a name="viewing-assignments-programmatically"></a>Hozzárendelések megtekintése programozott módon

A hozzáférési csomagokban hozzárendeléseket is lekérhet a Microsoft Graph.  A megfelelő szerepkörű, delegált engedéllyel rendelkező alkalmazással rendelkező felhasználók az API-t hívhatja meg az `EntitlementManagement.ReadWrite.All` [accessPackageAssignments listához.](/graph/api/accesspackageassignment-list?view=graph-rest-beta&preserve-view=true)

## <a name="directly-assign-a-user"></a>Felhasználó közvetlen hozzárendelése

Bizonyos esetekben előfordulhat, hogy közvetlenül szeretne felhasználókat hozzárendelni egy hozzáférési csomaghoz, hogy a felhasználóknak ne kell végigmenni a hozzáférési csomag kérelmezésének folyamatán. A felhasználók közvetlen hozzárendelése érdekében a hozzáférési csomagnak olyan szabályzatra van szükség, amely lehetővé teszi a rendszergazdai közvetlen hozzárendeléseket.

**Előfeltételként szükséges szerepkör:** globális rendszergazda, felhasználói rendszergazda, katalógustulajdonos, hozzáférésicsomag-kezelő vagy hozzáférésicsomag-hozzárendelés-kezelő

1. A Azure Portal **kattintson** a Azure Active Directory, majd az **Identity Governance elemre.**

1. A bal oldali menüben kattintson a **Hozzáférési csomagok elemre,** majd nyissa meg a hozzáférési csomagot.

1. A bal oldali menüben kattintson a **Hozzárendelések elemre.**

1. Kattintson **az Új hozzárendelés elemre** a Felhasználó hozzáadása csomag eléréséhez megnyitásához.

    ![Hozzárendelések – Felhasználó hozzáadása csomag eléréséhez](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1. Kattintson **a Felhasználók hozzáadása** elemre, és válassza ki azokat a felhasználókat, akikhez hozzá szeretné rendelni ezt a hozzáférési csomagot.

1. A Szabályzat **kiválasztása listában** válasszon ki egy szabályzatot, amely a felhasználók jövőbeli kéréseit és életciklusát fogja szabályozza és nyomon követi. Ha azt szeretné, hogy a kijelölt felhasználók más házirend-beállításokkal is, új szabályzat hozzáadásához kattintson az **Új** szabályzat létrehozása elemre.

1. Állítsa be a dátumot és időt, amikor a kiválasztott felhasználók hozzárendelését el szeretné kezdeni és le szeretné állítani. Ha nem ad meg záró dátumot, a szabályzat életciklus-beállításai lesznek használva.

1. Igény szerint meg is indokolhatja a rekordtartás közvetlen hozzárendelését.

1. A **Hozzáadás gombra** kattintva közvetlenül hozzárendelheti a kiválasztott felhasználókat a hozzáférési csomaghoz.

    Néhány pillanat múlva kattintson a **Frissítés** gombra a felhasználók hozzárendelések listájában való megjelenik.

### <a name="directly-assigning-users-programmatically"></a>Felhasználók közvetlen hozzárendelése programozott módon

Közvetlenül is hozzárendelhet egy felhasználót egy hozzáférési csomaghoz a Microsoft Graph.  A megfelelő szerepkörű, delegált engedéllyel rendelkező alkalmazással rendelkező felhasználók az API-t hívva létrehozhatnak egy `EntitlementManagement.ReadWrite.All` [accessPackageAssignmentRequest kérelmet.](/graph/api/accesspackageassignmentrequest-post?view=graph-rest-beta&preserve-view=true)

## <a name="remove-an-assignment"></a>Hozzárendelés eltávolítása

**Előfeltételként szükséges szerepkör:** globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa, hozzáférésicsomag-kezelő vagy hozzáférésicsomag-hozzárendelés kezelője

1. A Azure Portal **kattintson** a Azure Active Directory, majd az **Identity Governance elemre.**

1. A bal oldali menüben kattintson a **Hozzáférési csomagok elemre,** majd nyissa meg a hozzáférési csomagot.

1. A bal oldali menüben kattintson a **Hozzárendelések elemre.**
 
1. Jelölje be annak a felhasználónak a jelölőnégyzetét, akinek a hozzárendelését el szeretné távolítani a hozzáférési csomagból. 

1. Kattintson **az Eltávolítás** gombra a bal oldali panel tetején. 
 
    ![Hozzárendelések – Felhasználó eltávolítása a hozzáférési csomagból](./media/entitlement-management-access-package-assignments/remove-assignment-select-remove-assignment.png)

    Megjelenik egy értesítés, amely tájékoztatja, hogy a hozzárendelés el lett távolítva. 

## <a name="next-steps"></a>Következő lépések

- [Hozzáférési csomag kérésének és beállításainak módosítása](entitlement-management-access-package-request-policy.md)
- [Jelentések és naplók megtekintése](entitlement-management-reports.md)