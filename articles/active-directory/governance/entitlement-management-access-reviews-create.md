---
title: Hozzáférési csomag hozzáférési felülvizsgálatának létrehozása az Azure AD-jogosultságok kezelésében
description: Megtudhatja, hogyan hozhat létre hozzáférési felülvizsgálati szabályzatot a jogosultságok felügyeleti hozzáférési csomagjaihoz Azure Active Directory hozzáférési felülvizsgálatokban (előzetes verzió).
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
ms.openlocfilehash: 012eefe9140703a62d7bb1074ab763191a0976cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87798511"
---
# <a name="create-an-access-review-of-an-access-package-in-azure-ad-entitlement-management"></a>Hozzáférési csomag hozzáférési felülvizsgálatának létrehozása az Azure AD-jogosultságok kezelésében

Az elavult hozzáférés kockázatának csökkentése érdekében érdemes engedélyezni azokat a felhasználókat, akik aktív hozzárendelésekkel rendelkeznek egy hozzáférési csomaghoz az Azure AD-jogosultságok kezelésében. Az értékeléseket engedélyezheti új hozzáférési csomag létrehozásakor vagy meglévő hozzáférési csomag szerkesztésekor. Ez a cikk a hozzáférési csomagok hozzáférési felülvizsgálatának engedélyezését ismerteti.

## <a name="prerequisites"></a>Előfeltételek

A hozzáférési csomagok felülvizsgálatának engedélyezéséhez meg kell felelnie a hozzáférési csomag létrehozásának előfeltételeinek:
- Prémium szintű Azure AD P2
- Globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa vagy hozzáférési csomag kezelője

További információkért lásd a [licencekre vonatkozó követelményeket](entitlement-management-overview.md#license-requirements).


## <a name="create-an-access-review-of-an-access-package"></a>Hozzáférési csomag hozzáférési felülvizsgálatának létrehozása

A hozzáférési felülvizsgálatok [új hozzáférési csomag létrehozásakor](entitlement-management-access-package-create.md) vagy [meglévő hozzáférési csomagra](entitlement-management-access-package-lifecycle-policy.md) vonatkozó házirend szerkesztésekor engedélyezhetők. A hozzáférési csomagok hozzáférési felülvizsgálatának engedélyezéséhez kövesse az alábbi lépéseket:

1. Nyissa meg a hozzáférési csomag **életciklus** lapját, és görgessen le a **hozzáférési felülvizsgálatokhoz**.

1. Helyezze át az **Igen** értékre a **hozzáférési felülvizsgálatok megkövetelése** kapcsolót.

    ![Hozzáférési felülvizsgálat hozzáadása](./media/entitlement-management-access-reviews/access-reviews-pane.png)

1. Itt adhatja meg azt a dátumot, ameddig a felülvizsgálatok **beindulnak**.

1. Ezután állítsa a **felülvizsgálati gyakoriságot** **évente**, **kétévente**, **negyedévente** vagy **havonta**.
Ez a beállítás határozza meg, hogy a rendszer milyen gyakran történjen a hozzáférési felülvizsgálatok.

1. Adja meg az **időtartamot** annak meghatározásához, hogy az ismétlődő adatsorozatok egyes áttekintése hány nap múlva legyen megnyitva a véleményezők számára. Előfordulhat például, hogy egy éves felülvizsgálatot ütemezhet, amely január 1-től kezdődik, és 30 napig nyitva van, hogy a felülvizsgálók a hónap végéig legyenek válaszolva.

1. A felülvizsgálók mellett válassza az **önellenőrzés** **lehetőséget, ha** azt szeretné, hogy a felhasználók elvégezzék a saját hozzáférési felülvizsgálatot, vagy ha egy véleményezőt szeretne kijelölni, válassza ki az **adott véleményező (ka)** t.

    ![Válassza a véleményezők hozzáadása lehetőséget](./media/entitlement-management-access-reviews/access-reviews-add-reviewer.png)

1. Ha megadott **véleményező (ka) t** választott, akkor határozza meg, hogy mely felhasználók hajtják végre a hozzáférési felülvizsgálatot:
    1. Válassza a **véleményezők hozzáadása** lehetőséget.
    1. A **véleményezők kiválasztása** ablaktáblán keresse meg és válassza ki azokat a felhasználókat, akiket felülvizsgáló szeretne használni.
    1. Ha kiválasztotta a véleményező (ka) t, kattintson a **kiválasztás** gombra.

    ![Felülvizsgálók meghatározása](./media/entitlement-management-access-reviews/access-reviews-select-reviewer.png)

1. Kattintson a **felülvizsgálat + létrehozás** lehetőségre, ha új hozzáférési csomagot vagy **frissítést** hoz létre, ha egy hozzáférési csomagot szerkeszt, a lap alján.

## <a name="view-the-status-of-the-access-review"></a>A hozzáférési felülvizsgálat állapotának megtekintése

A kezdési dátum után a hozzáférési felülvizsgálatok a **hozzáférési** felülvizsgálatok szakaszban szerepelnek. A hozzáférési felülvizsgálat állapotának megtekintéséhez kövesse az alábbi lépéseket:

1. Az **identitás irányítása** területen kattintson a **hozzáférési csomagok** lehetőségre, majd válassza ki azt a hozzáférési csomagot, amelynek a hozzáférési felülvizsgálati állapotát ellenőrizni szeretné.   

1. Ha a hozzáférési csomag áttekintése elemre kattint, kattintson a bal oldali menüben a **hozzáférési felülvizsgálatok** lehetőségre.
    
    ![Hozzáférési felülvizsgálatok kiválasztása](./media/entitlement-management-access-reviews/access-review-status-access-package-overview.png)

1. Ekkor megjelenik egy lista, amely tartalmazza az összes olyan házirendet, amelyekhez hozzá van rendelve hozzáférési felülvizsgálat. Kattintson a felülvizsgálatra a jelentés megjelenítéséhez.

    ![Hozzáférési felülvizsgálatok listája](./media/entitlement-management-access-reviews/access-review-status-select-access-reviews.png)
   
1. Amikor megtekinti a jelentést, az azt jelzi, hogy hány felhasználó tekint át, és milyen műveleteket végez rajtuk a véleményező.

    ![Felülvizsgálati állapot megtekintése](./media/entitlement-management-access-reviews/access-review-status.png)
 

## <a name="access-reviews-email-notifications"></a>Hozzáférési felülvizsgálatok e-mail értesítései
Megadhatók a felülvizsgálók, vagy a felhasználók saját maguk is ellenőrizhetik a hozzáférést. Alapértelmezés szerint az Azure AD egy e-mailt küld a felülvizsgálók vagy az önellenőrzők számára röviddel a felülvizsgálat elindítása után.

Az e-mail tartalmazza a hozzáférési csomagokhoz való hozzáférés áttekintésére vonatkozó utasításokat is. Ha a felülvizsgálat célja, hogy a felhasználók áttekintsék a hozzáférésüket, megtekinthetik a hozzáférési csomagjaik önellenőrzésének elvégzéséhez szükséges utasításokat.
  
Ha a vendég felhasználóit felülvizsgáló rendelte hozzá, és nem fogadták el az Azure ad vendég meghívóját, nem kapnak e-mailt az Azure ad hozzáférési felülvizsgálatokból. Először el kell fogadniuk a meghívást, és létre kell hoznia egy fiókot az Azure AD-vel, mielőtt megkapják az e-maileket. 

## <a name="next-steps"></a>Következő lépések

- [Hozzáférési csomagok hozzáférésének áttekintése](entitlement-management-access-reviews-review-access.md)
- [Hozzáférési csomagok önálló felülvizsgálata](entitlement-management-access-reviews-self-review.md)
