---
title: Hozzáférési csomag igénylése – Azure AD-jogosultságok kezelése
description: Megtudhatja, hogyan kérhet hozzáférést egy hozzáférési csomaghoz Azure Active Directory jogosultságok kezelése a saját hozzáférési portál használatával.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 06/18/2020
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: 726507fa6ea0651b23d46424bda669a2d8ad41e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95996644"
---
# <a name="request-access-to-an-access-package-in-azure-ad-entitlement-management"></a>Hozzáférési csomaghoz való hozzáférés kérése az Azure AD-jogosultságok kezelésében

Az Azure AD-jogosultságok kezelésével a hozzáférési csomag lehetővé teszi az erőforrások és házirendek egyszeri beállítását, amely automatikusan felügyeli a hozzáférést a hozzáférési csomag élettartamához. 

Az Access Package Manager olyan házirendeket konfigurálhat, amelyek jóváhagyást igényelnek a felhasználók számára a hozzáférési csomagokhoz való hozzáféréshez. Egy hozzáférési csomag elérését igénylő felhasználónak kérést küldhet a hozzáféréshez. Ez a cikk egy hozzáférési kérelem küldését ismerteti.

## <a name="sign-in-to-the-my-access-portal"></a>Bejelentkezés a saját hozzáférési portálra

Első lépésként jelentkezzen be a saját hozzáférési portálra, ahol hozzáférést igényelhet egy hozzáférési csomaghoz.

**Előfeltételként szükséges szerepkör:** Kérelmező

1. Keressen egy e-mailt vagy egy üzenetet a projektből vagy a Business managerből, amelyen dolgozik. Az e-mailnek tartalmaznia kell a hozzáférési csomagra mutató hivatkozást, amelyre szüksége lesz. A hivatkozás a-vel kezdődik `myaccess` , tartalmaz egy könyvtári mutatót, és egy hozzáférési csomag azonosítójával végződik.  (Az USA kormányzati szervei esetében előfordulhat, hogy a tartomány `https://myaccess.microsoft.us` helyette.)
 
    `https://myaccess.microsoft.com/@<directory_hint>#/access-packages/<access_package_id>`

1. Nyissa meg a hivatkozást.

1. Jelentkezzen be a saját hozzáférési portálra.

    Ügyeljen arra, hogy a szervezeti (munkahelyi vagy iskolai) fiókot használja. Ha nem biztos a dolgában, forduljon a projekthez vagy a Business Managerhez.

## <a name="request-an-access-package"></a>Hozzáférési csomag igénylése

Miután megtalálta a hozzáférési csomagot a saját hozzáférési portálon, elküldheti a kérelmet.

**Előfeltételként szükséges szerepkör:** Kérelmező

1. Keresse meg a hozzáférési csomagot a listában.  Ha szükséges, a kereséshez írja be a keresési karakterláncot, majd válassza ki a **nevet**, a **katalógust** vagy az **erőforrások** szűrőt.

    ![Saját hozzáférési portál – erőforrás-keresés](./media/entitlement-management-request-access/my-access-resource-search.png)

1. Kattintson a pipa jelre a hozzáférési csomag kiválasztásához.

1. Kattintson a **hozzáférés kérése** elemre a hozzáférés kérése panel megnyitásához.

    ![Hozzáférési portál – hozzáférési csomagok](./media/entitlement-management-request-access/my-access-request-access-button.png)

1. Ha megjelenik az **üzleti indoklás** mező, írja be a hozzáféréshez szükséges indoklást.

1. Ha az **adott időszakra vonatkozó kérelem** van engedélyezve, válassza az **Igen** vagy a **nem** lehetőséget.

1. Szükség esetén a kezdési és a befejezési dátumot is meg kell adni.

    ![Hozzáférési portál – hozzáférés kérése](./media/entitlement-management-shared/my-access-request-access.png)

1. Ha elkészült, kattintson a **Submit (Küldés** ) gombra a kérelem elküldéséhez.

1. Kattintson a kérelmek **előzményei** gombra a kérések és az állapot listájának megtekintéséhez.

    Ha a hozzáférési csomag jóváhagyást igényel, a kérelem már függőben lévő jóváhagyási állapotban van.

### <a name="select-a-policy"></a>Szabályzat kiválasztása

Ha olyan hozzáférési csomaghoz kér hozzáférést, amelyhez több szabályzat is tartozik, akkor előfordulhat, hogy ki kell választania egy szabályzatot. A hozzáférési csomag kezelője például két, belső alkalmazottak két csoportjára vonatkozó házirendet konfigurálhat. Az első szabályzat 60 napig is engedélyezheti a hozzáférést, és jóváhagyást igényelhet. A második szabályzat 2 napig is engedélyezheti a hozzáférést, és nem igényel jóváhagyást. Ha ebben a forgatókönyvben találkozik, ki kell választania a használni kívánt szabályzatot.

![Hozzáférési portál – hozzáférés kérése – több szabályzat](./media/entitlement-management-request-access/my-access-multiple-policies.png)

### <a name="fill-out-requestor-information"></a>A kérelmező adatainak kitöltése

Hozzáférést kérhet egy olyan hozzáférési csomaghoz, amely üzleti indoklást és további kérelmezői adatokat igényel, mielőtt hozzáférést adna a hozzáférési csomaghoz. Töltse ki a hozzáférési csomag eléréséhez szükséges összes kérő információt.

![Saját hozzáférési portál – hozzáférés kérése – a kérelmező adatainak kitöltése](./media/entitlement-management-request-access/my-access-requestor-information.png)

## <a name="resubmit-a-request"></a>Kérelem újraküldése

Amikor hozzáférést kér egy hozzáférési csomaghoz, előfordulhat, hogy a rendszer megtagadja a kérést, vagy ha a jóváhagyók nem válaszolnak időben. Ha hozzáférésre van szüksége, próbálkozzon újra, és küldje el újra a kérelmet. A következő eljárás azt ismerteti, hogyan lehet újraküldeni egy hozzáférési kérelmet:

**Előfeltételként szükséges szerepkör:** Kérelmező

1. Jelentkezzen be a **saját hozzáférési** portálra.

1. Kattintson a bal oldali navigációs menü **korábbi kérelmek előzményei** elemére.

1. Keresse meg azt a hozzáférési csomagot, amelyhez újraküldi a kérelmet.

1. Kattintson a pipa jelre a hozzáférési csomag kiválasztásához.

1. Kattintson a kiválasztott hozzáférési csomag jobb oldalán látható kék **nézet** hivatkozásra.
    
    ![Hozzáférési csomag és nézet hivatkozásának kiválasztása](./media/entitlement-management-request-access/resubmit-request-select-request-and-view.png)

    A jobb oldali ablaktábla a hozzáférési csomaghoz tartozó kérések előzményeivel fog megnyílni.
    
    ![Az Újraküldés gomb kiválasztása](./media/entitlement-management-request-access/resubmit-request-select-resubmit.png)

1. Kattintson a panel alján található **Újraküldés** gombra.

## <a name="cancel-a-request"></a>Kérelem megszakítása

Ha hozzáférési kérelmet küld be, és a kérés még mindig a **függőben lévő jóváhagyási** állapotban van, megszakíthatja a kérelmet.

**Előfeltételként szükséges szerepkör:** Kérelmező

1. A saját hozzáférési portál bal oldalán kattintson a kérelmek **előzményei** elemre a kérések és az állapot megjelenítéséhez.

1. Kattintson a megszakítani kívánt kérelem **megtekintési** hivatkozására.

1. Ha a kérelem továbbra is a **függőben lévő jóváhagyási** állapotban van, a kérelem megszakításához kattintson a **kérelem megszakítása** lehetőségre.

    ![Saját hozzáférési portál – kérelem megszakítása](./media/entitlement-management-request-access/my-access-cancel-request.png)

1. Kattintson a **kérelmek előzményei** elemre a kérelem törlésének megerősítéséhez.

## <a name="next-steps"></a>Következő lépések

- [Hozzáférési kérelmek jóváhagyása vagy megtagadása](entitlement-management-request-approve.md)
- [Kérelmek feldolgozása és e-mail-értesítések](entitlement-management-process.md)
