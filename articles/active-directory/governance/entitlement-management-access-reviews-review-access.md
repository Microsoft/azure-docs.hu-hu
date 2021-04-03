---
title: Hozzáférési csomag hozzáférésének áttekintése az Azure AD-jogosultságok kezelésében
description: Megtudhatja, hogyan végezheti el a jogosultságok felügyeleti hozzáférési csomagjainak hozzáférési felülvizsgálatát Azure Active Directory hozzáférési felülvizsgálatokban (előzetes verzió).
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
ms.openlocfilehash: d1115c08214db19227b4b8d7be671ce4da1cf2b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87798597"
---
# <a name="review-access-of-an-access-package-in-azure-ad-entitlement-management"></a>Hozzáférési csomag hozzáférésének áttekintése az Azure AD-jogosultságok kezelésében

Az Azure AD jogosultság-kezelési szolgáltatás leegyszerűsíti, hogy a vállalatok hogyan kezelhetik a csoportokat, az alkalmazásokat és a SharePoint-webhelyeket. Ez a cikk azt ismerteti, hogyan hajtható végre a hozzáférési felülvizsgálatok olyan más felhasználók számára, akik hozzá vannak rendelve egy hozzáférési csomaghoz a kijelölt felülvizsgáló.

## <a name="prerequisites"></a>Előfeltételek

A felhasználók aktív hozzáférési csomagjának hozzárendeléseinek áttekintéséhez meg kell felelnie a hozzáférési felülvizsgálat előfeltételeinek:
- Prémium szintű Azure AD P2
- Globális rendszergazda
- Kijelölt felhasználói rendszergazda, katalógus tulajdonosa vagy hozzáférési csomag kezelője

További információkért lásd a [licencekre vonatkozó követelményeket](entitlement-management-overview.md#license-requirements).


## <a name="open-the-access-review"></a>A hozzáférési felülvizsgálat megnyitása

A hozzáférési felülvizsgálat megkereséséhez és megnyitásához kövesse az alábbi lépéseket:

1. Előfordulhat, hogy a Microsoft e-mailt kap, amely arra kéri, hogy tekintse át a hozzáférést. Keresse meg az e-mailt a hozzáférési felülvizsgálat megnyitásához. Íme egy példa e-mailben a hozzáférés áttekintéséhez:
    
    ![Hozzáférési felülvizsgálati felülvizsgáló e-mail-címe](./media/entitlement-management-access-reviews-review-access/review-access-reviewer-email.png)

1. A hozzáférési felülvizsgálat megnyitásához kattintson a **felhasználói hozzáférés áttekintése** hivatkozásra. 

1. Ha nem rendelkezik az e-mail-címmel, akkor a függőben lévő hozzáférési felülvizsgálatok közvetlenül a alkalmazásban találhatók https://myaccess.microsoft.com .  (Az USA kormányzati szervei számára használja `https://myaccess.microsoft.us` helyette.)

1. A bal oldali navigációs sávon kattintson a **hozzáférési felülvizsgálatok** lehetőségre az Önhöz rendelt hozzáférési felülvizsgálatok listájának megtekintéséhez.
    
    ![Hozzáférési felülvizsgálatok kiválasztása a saját hozzáférés lehetőségnél](./media/entitlement-management-access-reviews-review-access/review-access-myaccess-select-access-review.png)

1. Kattintson a megkezdeni kívánt áttekintésre.
    
    ![Hozzáférési felülvizsgálat kiválasztása](./media/entitlement-management-access-reviews-review-access/review-access-select-access-review.png)

## <a name="perform-the-access-review"></a>A hozzáférési felülvizsgálat végrehajtása

A hozzáférési felülvizsgálat megnyitása után megtekintheti azoknak a felhasználóknak a nevét, amelyeknek át kell tekintenie. Két módon engedélyezheti vagy tilthatja le a hozzáférést:
- Egy vagy több felhasználóhoz manuálisan is jóváhagyhatja vagy megtagadhatja a hozzáférést
- Elfogadhatja a rendszerjavaslatokat

### <a name="manually-approve-or-deny-access-for-one-or-more-users"></a>Egy vagy több felhasználó hozzáférésének manuális jóváhagyása vagy megtagadása
1. Tekintse át a felhasználók listáját, és határozza meg, hogy mely felhasználóknak kell továbbra is hozzáférése.

    ![A megtekinteni kívánt felhasználók listája](./media/entitlement-management-access-reviews-review-access/review-access-list-of-users.png)

1. A hozzáférés jóváhagyásához vagy megtagadásához válassza a felhasználó neve bal oldalán található választógombot.

1. A felhasználónevek feletti sávban válassza a **jóváhagyás** vagy a **Megtagadás** lehetőséget.

    ![Válassza ki a felhasználót](./media/entitlement-management-access-reviews-review-access/review-access-select-users.png)

1. Ha nem biztos benne, akkor kattintson a **nem tud** gombra.

    Ha ezt a beállítást választja, a felhasználó fenntartja a hozzáférést, és ez a kijelölés a naplókba kerül. A napló megjeleníti azokat a véleményezőket, amelyeken még befejezte a felülvizsgálatot.

1. Előfordulhat, hogy meg kell adnia a döntés okát. Írjon be egy okot, és kattintson a **Submit (Küldés**) gombra.

    ![Hozzáférés jóváhagyása vagy megtagadása](./media/entitlement-management-access-reviews-review-access/review-access-decision-approve.png)

1. A döntés a felülvizsgálat vége előtt bármikor módosítható. Ehhez válassza ki a felhasználót a listából, és változtassa meg a döntést. Jóváhagyhatja például a korábban megtagadott felhasználó hozzáférését.

Ha több felülvizsgáló is van, a rendszer rögzíti a legutóbbi elküldött választ. Vegyünk például egy olyan példát, amelyben a rendszergazda két véleményezőt jelöl ki: Alice és Bob. Alice először megnyitja a felülvizsgálatot, és jóváhagyja a hozzáférést. A felülvizsgálat vége előtt Bob megnyitja a felülvizsgálatot, és megtagadja a hozzáférést. Ebben az esetben az utolsó megtagadási hozzáférési döntés rögzítve lesz.

>[!NOTE]
>Ha a felhasználó hozzáférése megtagadva, a rendszer nem távolítja el azonnal a hozzáférési csomagból. A rendszer eltávolítja a felhasználót a hozzáférési csomagból, ha a felülvizsgálat véget ér, vagy a rendszergazda befejezi a felülvizsgálatot.

### <a name="approve-or-deny-access-using-the-system-generated-recommendations"></a>Hozzáférés jóváhagyása vagy megtagadása a rendszer által létrehozott javaslatok használatával

Ha több felhasználó hozzáférését szeretné áttekinteni, használja a rendszer által létrehozott javaslatokat, és fogadja el a javaslatokat egyetlen kattintással. A javaslatok a felhasználó bejelentkezési tevékenysége alapján jönnek létre.

1.  A lap tetején található sávban kattintson a **javaslatok elfogadása** elemre.
    
    ![Javaslatok fogadásának kiválasztása](./media/entitlement-management-access-reviews-review-access/review-access-use-recommendations.png)
    
    Ekkor megjelenik a javasolt műveletek összegzése.

1.  A javaslatok elfogadásához kattintson a **Submit (Küldés** ) gombra.

## <a name="next-steps"></a>Következő lépések

- [Hozzáférési csomagok önálló felülvizsgálata](entitlement-management-access-reviews-self-review.md)