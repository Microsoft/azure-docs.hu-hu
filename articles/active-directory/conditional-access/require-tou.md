---
title: Rövid útmutató – Azure Active Directory feltételes hozzáférés által védett felhőalapú alkalmazások elérése előtt el kell fogadni a használati feltételeket tartalmazó fájl szükséges |} A Microsoft Docs
description: Ez a rövid útmutatóban megismerheti, hogyan megkövetelheti, hogy az Azure Active Directory feltételes hozzáférés által kiválasztott felhőalapú alkalmazásokhoz való hozzáférés megadása előtt elfogadja a használati feltételeket.
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
ms.openlocfilehash: c37a58cb9f9d1082d02854f43e511e5431d90c13
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60302164"
---
# <a name="quickstart-require-terms-of-use-to-be-accepted-before-accessing-cloud-apps"></a>Gyors útmutató: Felhőbeli alkalmazások elérése előtt el kell fogadni a használati feltételeket tartalmazó fájl megkövetelése

Előtt fér hozzá az egyes felhőalapú alkalmazásokhoz a környezetében, érdemes hozzájárulási beolvasni a felhasználó elfogadja a használati feltételeket (feltételek) formájában. Az Azure Active Directory (Azure AD) feltételes hozzáférés a következőket tartalmazza:

- Egy egyszerű módszer a használati feltételek konfigurálása
- Elfogadja a használati feltételeket a feltételes hozzáférési szabályzat segítségével megkövetelése lehetőséggel  

Ez a rövid útmutató ismerteti, hogyan konfigurálható egy [Azure AD feltételes hozzáférési szabályzat](../active-directory-conditional-access-azure-portal.md) , amelyhez a kiválasztott felhőalapú alkalmazások a környezetben el kell fogadni a használati feltételek.

![Szabályzat létrehozása](./media/require-tou/5555.png)

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ebben a rövid útmutatóban a forgatókönyv végrehajtásához szükséges:

- **Hozzáférés az Azure AD Premium Edition** – Azure AD feltételes hozzáférés egy Azure AD Premium-funkció.
- **Tesztfiók nevű Isabella Simonsen** – Ha nem tudja, hogyan hozzon létre egy olyan fiókot, lásd: [adja hozzá a felhőalapú felhasználók](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

## <a name="test-your-sign-in"></a>A bejelentkezés tesztelése

Ebben a lépésben az a célja, hogy egy benyomást kell szereznie a bejelentkezési élmény a feltételes hozzáférési szabályzat nélkül.

**A bejelentkezés teszteléséhez:**

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) Isabella Simonsen szerint.
1. Jelentkezzen ki.

## <a name="create-your-terms-of-use"></a>A használati feltételek létrehozása

A szakasz ismerteti a lépéseket egy minta használati feltételek létrehozásához. A jelen használati feltételek létrehozásakor értéket választja **érvényesítése a feltételes hozzáférési szabályzatok sablonjaival**. Kiválasztásával **egyéni házirend** új feltételes hozzáférési szabályzat létrehozása, amint a használati feltételek létrehozása párbeszédpanel megnyitása.

**A használati feltételeket tartalmazó fájl létrehozása:**

1. A Microsoft Word hozzon létre egy új dokumentumot.

1. Típus **a használati feltételeket tartalmazó fájl**, és a számítógépre, majd mentse a dokumentumot **mytou.pdf**.

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com) globális, biztonsági vagy feltételes hozzáférés rendszergazdájaként.

1. Az Azure Portalon, a bal oldali navigációs sávon kattintson **Azure Active Directory**.

   ![Azure Active Directory](./media/require-tou/02.png)

1. Az a **Azure Active Directory** lap a **biztonsági** területén kattintson **feltételes hozzáférési**.

   ![Feltételes hozzáférés](./media/require-tou/03.png)

1. Az a **kezelés** területén kattintson **használati feltételeket tartalmazó fájl**.

   ![Használati feltételek](./media/require-tou/04.png)

1. A felső menüben kattintson **új feltételek elfogadását**.

   ![Használati feltételek](./media/require-tou/05.png)

1. Az a **új használati feltételek** oldalon:

   ![Használati feltételek](./media/require-tou/112.png)

   1. Az a **neve** szövegmezőbe írja be **saját használati feltételek**.

   1. Az a **megjelenítendő név** szövegmezőbe írja be **saját használati feltételek**.

   1. Töltse fel a PDF-fájl használati feltételeit.

   1. Mint **nyelvi**válassza **angol**.

   1. Mint **bontsa ki a használati feltételeket, hogy a felhasználók**válassza **a**.

   1. Mint **érvényesítése a feltételes hozzáférési szabályzatok sablonjaival**válassza **egyéni házirend**.

   1. Kattintson a **Create** (Létrehozás) gombra.

## <a name="create-your-conditional-access-policy"></a>A feltételes hozzáférési szabályzat létrehozása

Ez a szakasz bemutatja, hogyan hozhat létre a feltételes hozzáférési szabályzat. Ebben a rövid útmutatóban a forgatókönyvet használja:

- Az Azure Portalon, mint a használati feltételek elfogadását igénylő felhőalapú alkalmazás esetében helyőrző. 
- A Mintafelhasználó a feltételes hozzáférési házirend tesztelése.  

Állítsa be a szabályzat:

| Beállítás | Value |
| --- | --- |
| Felhasználók és csoportok | Isabella Simonsen |
| Felhőalkalmazások | A Microsoft Azure Management |
| Hozzáférés biztosítása | A jelen használati feltételek |

![Szabályzat létrehozása](./media/require-tou/1234.png)

**A feltételes hozzáférési szabályzat konfigurálása:**

1. Az a **új** lap a **neve** szövegmezőbe írja be **Isabella a használati feltételek megkövetelése**.

   ![Name (Név)](./media/require-tou/71.png)

1. Az a **hozzárendelés** területén kattintson **felhasználók és csoportok**.

   ![Felhasználók és csoportok](./media/require-tou/06.png)

1. Az a **felhasználók és csoportok** oldalon:

   ![Felhasználók és csoportok](./media/require-tou/24.png)

   1. Kattintson a **válassza ki a felhasználók és csoportok**, majd válassza ki **felhasználók és csoportok**.

   1. Kattintson a **Kiválasztás** gombra.

   1. Az a **válassza** lapra, jelölje be **Isabella Simonsen**, és kattintson a **kiválasztása**.

   1. Az a **felhasználók és csoportok** kattintson **kész**.

1. Kattintson a **Felhőalkalmazások**.

   ![Felhőalkalmazások](./media/require-tou/08.png)

1. Az a **Felhőalkalmazások** oldalon:

   ![Válassza ki a felhőalapú alkalmazások](./media/require-tou/26.png)

   1. Kattintson a **alkalmazások kiválasztása**.

   1. Kattintson a **Kiválasztás** gombra.

   1. A a **válassza** lapra, jelölje be **a Microsoft Azure Management**, és kattintson a **válassza**.

   1. Az a **Felhőalkalmazások** kattintson **kész**.

1. Az a **hozzáférés-vezérlés** területén kattintson **Grant**.

   ![Hozzáférés-szabályozás](./media/require-tou/10.png)

1. Az a **Grant** oldalon:

   ![Hozzáférés](./media/require-tou/111.png)

   1. Válassza ki **hozzáférést**.

   1. Válassza ki **a használati feltételek**.

   1. Kattintson a **Kiválasztás** gombra.

1. Az a **házirend engedélyezése** területén kattintson **a**.

   ![Házirend engedélyezése](./media/require-tou/18.png)

1. Kattintson a **Create** (Létrehozás) gombra.

## <a name="evaluate-a-simulated-sign-in"></a>Egy szimulált bejelentkezési kiértékelése

Most, hogy konfigurálta a feltételes hozzáférési szabályzatot, érdemes ellenőriznie, hogy a várt módon működik-e. Első lépésként, a feltételes hozzáférés használata a Mi történik, ha a házirend eszközzel egy jelentkezzen be a tesztfelhasználó szimulálásához. A szimuláció megbecsüli a bejelentkezésnek a szabályzatokra gyakorolt hatását, és létrehoz egy szimulációs jelentést.  

A mi inicializálni a szabályzat kiértékelési eszközével, ha:

- **Isabella Simonsen** felhasználóként
- **A Microsoft Azure Management** felhőalapú alkalmazásként

Kattintson a **mi történik, ha** tartalmazó szimuláció jelentést hoz létre:

- **Használati feltételek szükségesek a Isabella** alatt **érvényes szabályzatok**
- **A jelen használati feltételek** , **vezérlők biztosítson**.

![Mi történik, ha a házirend-eszköz](./media/require-tou/79.png)

**A feltételes hozzáférési szabályzat kiértékelése:**

1. Az a [feltételes hozzáférés – szabályzatok](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) lapon, a felső menüben kattintson **mi történik, ha**.  

   ![What If](./media/require-tou/14.png)

1. Kattintson a **felhasználók**válassza **Isabella Simonsen**, és kattintson a **kiválasztása**.

   ![Felhasználó](./media/require-tou/15.png)

1. A cloud app kiválasztása:

   ![Felhőalkalmazások](./media/require-tou/16.png)

   1. Kattintson a **Felhőalkalmazások**.

   1. Az a **felhőalapú alkalmazások lapjának**, kattintson a **alkalmazások kiválasztása**.

   1. Kattintson a **Kiválasztás** gombra.

   1. A a **válassza** lapra, jelölje be **a Microsoft Azure Management**, és kattintson a **válassza**.

   1. A felhőalapú alkalmazások lapján kattintson a **kész**.

1. Kattintson a **mi történik, ha**.

## <a name="test-your-conditional-access-policy"></a>A feltételes hozzáférési szabályzat tesztelése

Az előző szakaszban megtanulhatta egy szimulált bejelentkezési kiértékelése. A szimuláció mellett is érdemes tesztelnie a feltételes hozzáférési szabályzat, győződjön meg arról, hogy az elvárt módon működik.

Ha tesztelni szeretné a szabályzatot, próbálja meg, jelentkezzen be a [az Azure portal](https://portal.azure.com) használatával a **Isabella Simonsen** fiók teszteléséhez. Megjelenik egy párbeszédpanel, fogadja el a használati feltételeket tartalmazó fájl szükséges.

![Használati feltételek](./media/require-tou/57.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a tesztfelhasználó számára, és a feltételes hozzáférési szabályzatot:

- Ha nem ismeri az Azure AD-felhasználó törlése, lásd: [felhasználók törlése az Azure ad-ből](../fundamentals/add-users-azure-active-directory.md#delete-a-user).

- Törli a szabályzatot, válassza ki a szabályzatot, és kattintson **törlése** a gyorselérési eszköztáron.

    ![Multi-Factor Authentication](./media/require-tou/33.png)

- Ha törölni szeretné a használati feltételeket, válassza ki, és kattintson **feltételek törlése** felül az eszköztáron.

    ![Multi-Factor Authentication](./media/require-tou/29.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Többtényezős hitelesítés konkrét alkalmazások esetén](app-based-mfa.md)
> [letiltja a hozzáférést, a munkamenet kockázata észlelésekor](app-sign-in-risk.md)
