---
title: Rövid útmutató – Azure Active Directory feltételes hozzáférés a munkamenet kockázata észlelésekor elérésének letiltása |} A Microsoft Docs
description: Ebben a rövid útmutatóban megismerheti, hogy hogyan konfigurálhat egy Azure Active Directory (Azure AD) feltételes hozzáférési szabályzat blokkolja a bejelentkezések alapján a munkamenet kockázatok.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: quickstart
ms.date: 12/14/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: d5defdf2d33d32042775271fe01aba377687ae75
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60413434"
---
# <a name="quickstart-block-access-when-a-session-risk-is-detected-with-azure-active-directory-conditional-access"></a>Gyors útmutató: Letiltja a hozzáférést az Azure Active Directory feltételes hozzáférés a munkamenet kockázata észlelésekor  

A környezetet védett megtartásához Érdemes megakadályozhatja a gyanús felhasználók jelentkezzen be. [Az Azure Active Directory (Azure AD) Identity Protection](../active-directory-identityprotection.md) minden bejelentkezési elemzi, és kiszámítja a valószínűsége, hogy egy bejelentkezési kísérlet nem hajtottak végre egy felhasználói fiók jogos tulajdonosa. Annak a valószínűségét (alacsony, közepes, nagy) nevű számított érték képernyőn jelzett [bejelentkezési kockázati szintek](conditions.md#sign-in-risk). A bejelentkezési kockázati feltétellel beállításával konfigurálhatja a feltételes hozzáférési szabályzatot, konkrét bejelentkezési kockázati szinteknek megfelelően válaszolnak.

Ez a rövid útmutató ismerteti, hogyan konfigurálható egy [feltételes hozzáférési szabályzat](../active-directory-conditional-access-azure-portal.md) , amely blokkolja egy bejelentkezési beállított bejelentkezési kockázati szint észlelésekor.

![Szabályzat létrehozása](./media/app-sign-in-risk/1000.png)

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

- **Hozzáférés az Azure AD Premium P2 kiadás** – amíg feltételes hozzáférés az Azure AD Premium P1 képesség, mivel ez a rövid útmutató példahelyzet Identity Protection egy P2 kiadás kell.

- **Identity Protection** – ebben a rövid útmutatóban példahelyzet Identity Protection engedélyezni kell. Ha nem ismeri az Identity Protection használatának engedélyezése, [engedélyezése az Azure Active Directory Identity Protection](../identity-protection/enable.md).

- **Tor böngésző** – a [Tor böngésző](https://www.torproject.org/projects/torbrowser.html.en) célja, hogy segít megőrizni az adatvédelemmel kapcsolatban. Identity Protection észleli a bejelentkezési, Tor böngészőből **névtelen IP-címekről történő bejelentkezések**, amely rendelkezik egy közepes méretű kockázati szintű. További információkért tekintse át [Az Azure Active Directory kockázati eseményeivel](../reports-monitoring/concept-risk-events.md) foglalkozó cikket.  

- **Tesztfiók nevű Alain Charon** – Ha nem tudja, hogyan hozzon létre egy olyan fiókot, lásd: [adja hozzá a felhőalapú felhasználók](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

## <a name="test-your-sign-in"></a>A bejelentkezés tesztelése

Ez a lépés célja, győződjön meg arról, hogy a teszt fiók hozzáférhessen-e a Tor böngészővel a bérlő.

**A bejelentkezés teszteléséhez:**

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com) , **Alain Charon**.
1. Jelentkezzen ki.

## <a name="create-your-conditional-access-policy"></a>A feltételes hozzáférési szabályzat létrehozása

Ebben a rövid útmutatóban a forgatókönyvet használja a bejelentkezési Tor böngészőből létrehozni egy észlelt **névtelen IP-címekről történő bejelentkezések** kockázati esemény. A kockázati esemény kockázati szintje közepes. A kockázati esemény válaszolni a bejelentkezési kockázati feltétellel közepes beállítása. Éles környezetben a bejelentkezési kockázati feltétellel kell beállítania, vagy a magas, közepes és magas.

Ez a szakasz bemutatja, hogyan hozhat létre a feltételes hozzáférési szabályzat. Állítsa be a szabályzat:

| Beállítás | Value |
| --- | --- |
| Felhasználók és csoportok | Alain Charon  |
| Felhőalkalmazások | Minden felhőalkalmazás |
| Bejelentkezési kockázat | Közepes |
| Hozzáférés | Hozzáférés letiltása |

![Szabályzat létrehozása](./media/app-sign-in-risk/130.png)

**A feltételes hozzáférési szabályzat konfigurálása:**

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) globális, biztonsági vagy feltételes hozzáférés rendszergazdájaként.

1. Az Azure Portalon, a bal oldali navigációs sávon kattintson **Azure Active Directory**.

   ![Azure Active Directory](./media/app-sign-in-risk/02.png)

1. Az a **Azure Active Directory** lap a **biztonsági** területén kattintson **feltételes hozzáférési**.

   ![Feltételes hozzáférés](./media/app-sign-in-risk/03.png)

1. Az a **feltételes hozzáférési** oldalon, a felső eszköztáron kattintson **Hozzáadás**.

   ![Name (Név)](./media/app-sign-in-risk/108.png)

1. Az a **új** lap a **neve** szövegmezőbe írja be **közepes kockázati szint hozzáférésének blokkolása**.

   ![Name (Név)](./media/app-sign-in-risk/104.png)

1. Az a **hozzárendelés** területén kattintson **felhasználók és csoportok**.

   ![Felhasználók és csoportok](./media/app-sign-in-risk/06.png)

1. Az a **felhasználók és csoportok** oldalon:

   ![Feltételes hozzáférés](./media/app-sign-in-risk/107.png)

   1. Kattintson a **válassza ki a felhasználók és csoportok**, majd válassza ki **felhasználók és csoportok**.

   1. Kattintson a **Kiválasztás** gombra.

   1. Az a **válassza** lapra, jelölje be **Alain Charon**, és kattintson a **kiválasztása**.

   1. Az a **felhasználók és csoportok** kattintson **kész**.

1. Kattintson a **Felhőalkalmazások**.

   ![Felhőalkalmazások](./media/app-sign-in-risk/08.png)

1. Az a **Felhőalkalmazások** oldalon:

   ![Feltételes hozzáférés](./media/app-sign-in-risk/109.png)

   1. Kattintson a **az összes felhőalapú alkalmazások**.

   1. Kattintson a **Done** (Kész) gombra.

1. Kattintson a **feltételek**.

   ![Hozzáférés-szabályozás](./media/app-sign-in-risk/19.png)

1. Az a **feltételek** oldalon:

   ![Bejelentkezés kockázati szintje](./media/app-sign-in-risk/21.png)

   1. Kattintson a **bejelentkezési kockázat**.

   1. Mint **konfigurálása**, kattintson a **Igen**.

   1. Bejelentkezési kockázati szintet, válassza ki a **Közepes**.

   1. Kattintson a **Kiválasztás** gombra.

   1. Az a **feltételek** kattintson **kész**.

1. Az a **hozzáférés-vezérlés** területén kattintson **Grant**.

   ![Hozzáférés-szabályozás](./media/app-sign-in-risk/10.png)

1. Az a **Grant** oldalon:

   ![Feltételes hozzáférés](./media/app-sign-in-risk/105.png)

   1. Válassza ki **blokkolhatja**.

   1. Kattintson a **Kiválasztás** gombra.

1. Az a **házirend engedélyezése** területén kattintson **a**.

   ![Házirend engedélyezése](./media/app-sign-in-risk/18.png)

1. Kattintson a **Create** (Létrehozás) gombra.

## <a name="evaluate-a-simulated-sign-in"></a>Egy szimulált bejelentkezési kiértékelése

Most, hogy konfigurálta a feltételes hozzáférési szabályzatot, érdemes ellenőriznie, hogy a várt módon működik-e. Első lépésként, a feltételes hozzáférés használata a **mi történik, ha házirend eszközzel** egy jelentkezzen be a tesztfelhasználó szimulálásához. A szimuláció megbecsüli a bejelentkezésnek a szabályzatokra gyakorolt hatását, és létrehoz egy szimulációs jelentést.  

Futtatásakor a **mi történik, ha a házirend-eszköz** ebben a forgatókönyvben a **közepes kockázati szint hozzáférésének blokkolása** alatt kell szerepelnie **érvényes szabályzatok**.

![Felhasználó](./media/app-sign-in-risk/117.png)

**A feltételes hozzáférési szabályzat kiértékelése:**

1. Az a [feltételes hozzáférés – szabályzatok](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) lapon, a felső menüben kattintson **mi történik, ha**.  

   ![What If](./media/app-sign-in-risk/14.png)

1. Kattintson a **felhasználói**, jelölje be **Alan Charon** a a **felhasználók** lapon, és kattintson a **kiválasztása**.

   ![Felhasználó](./media/app-sign-in-risk/116.png)

1. Mint **bejelentkezési kockázat**válassza **Közepes**.

   ![Felhasználó](./media/app-sign-in-risk/119.png)

1. Kattintson a **mi történik, ha**.

## <a name="test-your-conditional-access-policy"></a>A feltételes hozzáférési szabályzat tesztelése

Az előző szakaszban megtanulhatta egy szimulált bejelentkezési kiértékelése. A szimuláció mellett is érdemes tesztelnie győződjön meg arról, hogy az elvárt módon működik, a feltételes hozzáférési szabályzatot.

Ha tesztelni szeretné a szabályzatot, próbálja meg, jelentkezzen be a [az Azure portal](https://portal.azure.com) , **Alan Charon** a Tor böngészővel. A bejelentkezési kísérlet a feltételes hozzáférési szabályzat által le kell tiltani.

![Multi-Factor Authentication](./media/app-sign-in-risk/118.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a tesztfelhasználó számára, a Tor-böngésző és a feltételes hozzáférési szabályzatot:

- Ha nem ismeri az Azure AD-felhasználó törlése, lásd: [felhasználók törlése az Azure ad-ből](../fundamentals/add-users-azure-active-directory.md#delete-a-user).

- Törli a szabályzatot, válassza ki a szabályzatot, és kattintson **törlése** a gyorselérési eszköztáron.

   ![Multi-Factor Authentication](./media/app-sign-in-risk/33.png)

- Eltávolítja a Tor böngésző útmutatásért lásd: [eltávolítása](https://tb-manual.torproject.org/uninstalling/).

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Használati feltételek elfogadása szükséges](require-tou.md)
> [adott alkalmazások a többtényezős hitelesítés megkövetelése](app-based-mfa.md)
