---
title: A feltételes hozzáférés használati feltételeket igényel – Azure Active Directory
description: Ebből a rövid útmutatóból megtudhatja, hogyan kérheti a használati feltételek elfogadását, mielőtt a kiválasztott felhőalapú alkalmazásokhoz való hozzáférést Azure Active Directory feltételes hozzáférési jogosultsággal adja meg.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: quickstart
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ab484e8caaffaf57f19f1fcd1e65f4b8e723f86
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93077897"
---
# <a name="quickstart-require-terms-of-use-to-be-accepted-before-accessing-cloud-apps"></a>Rövid útmutató: a használati feltételek elfogadásának megkövetelése a felhőalapú alkalmazások elérése előtt

A környezet bizonyos felhőalapú alkalmazásaihoz való hozzáférés előtt érdemes lehet a felhasználók beleegyezni a használati feltételek elfogadásával. A Azure Active Directory (Azure AD) feltételes hozzáférés a következőket biztosítja:

- Egyszerű módszer a felhasználási feltételek konfigurálására
- A használati feltételek feltételes hozzáférési szabályzattal való elfogadásának megkövetelése  

Ez a rövid útmutató bemutatja, hogyan konfigurálhat egy olyan [Azure ad feltételes hozzáférési szabályzatot](./overview.md) , amely megköveteli, hogy a környezetében egy kiválasztott felhőalapú alkalmazásnak el kell fogadnia a felhasználási feltételeket.

:::image type="content" source="./media/require-tou/5555.png" alt-text="Képernyőkép a Azure Portalról. Megjelenik egy olyan ablaktábla, amely egy szükséges T O U-re vonatkozó szabályzatot definiál" border="false":::

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

Az ebben a rövid útmutatóban szereplő forgatókönyv végrehajtásához a következőkre lesz szüksége:

- **Hozzáférés egy prémium szintű Azure ad kiadáshoz** – az Azure ad feltételes hozzáférés egy prémium szintű Azure ad képesség.
- **Egy Isabella Simonsen nevű tesztüzenet** – ha nem tudja, hogyan hozhat létre egy teszt fiókot, tekintse meg a [felhőalapú felhasználók hozzáadása](../fundamentals/add-users-azure-active-directory.md#add-a-new-user)című témakört.

## <a name="test-your-sign-in"></a>A bejelentkezés tesztelése

Ennek a lépésnek a célja, hogy betekintse a bejelentkezési élményt feltételes hozzáférési szabályzat nélkül.

**A bejelentkezés tesztelése:**

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) Isabella Simonsen.
1. Jelentkezzen ki.

## <a name="create-your-terms-of-use"></a>Használati feltételek létrehozása

Ez a szakasz a minta felhasználási feltételek létrehozásának lépéseit ismerteti. A használati feltételek létrehozásakor ki kell választania egy értéket a **kényszerített hozzáférési házirend-sablonokkal**. Ha az **Egyéni házirendet** választja, megnyílik a párbeszédpanel, amely azonnal létrehoz egy új feltételes hozzáférési szabályzatot, amint a rendszer létrehozta a felhasználási feltételeket.

**A használati feltételek létrehozása:**

1. A Microsoft Wordben hozzon létre egy új dokumentumot.
1. Írja be a **saját használati feltételeket**, majd mentse a dokumentumot a számítógépen **mytou.pdf**.
1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférési rendszergazdaként.
1. A Azure Portal a bal oldali navigációs sávon kattintson az **Azure Active Directory** elemre.

   ![Azure Active Directory](./media/require-tou/02.png)

1. A **Azure Active Directory** lap **Biztonság** szakaszában kattintson a **feltételes hozzáférés** elemre.

   ![Feltételes hozzáférés](./media/require-tou/03.png)

1. A **kezelés** szakaszban kattintson a **használati feltételek** elemre.

   :::image type="content" source="./media/require-tou/04.png" alt-text="Képernyőkép a Azure Active Directory lap kezelés szakaszáról. A Használati feltételek eleme ki van emelve." border="false":::

1. A felső menüben kattintson az **új feltételek** elemre.

   :::image type="content" source="./media/require-tou/05.png" alt-text="Képernyőkép a Azure Active Directory oldalon található menüről. Az új kifejezések eleme ki van emelve." border="false":::

1. Az **új használati feltételek** lapon:

   :::image type="content" source="./media/require-tou/112.png" alt-text="Képernyőkép: az új használati feltételek lap, a név, a megjelenítendő név, a dokumentum, a nyelv, a feltételes hozzáférés és a kibővített kifejezések kiemelése." border="false":::

   1. A **név** szövegmezőbe írja be a **saját felhasználási** feltételeket.
   1. A **megjelenítendő név** szövegmezőbe írja be a **saját felhasználási** feltételeket.
   1. Töltse fel a PDF-fájl használati feltételeit.
   1. **Nyelvként** válassza az **angol** nyelvet.
   1. Ahogy a felhasználók a használati **feltételek kibontását kérik**, válassza **a be** lehetőséget.
   1. A **feltételes hozzáférési házirend-sablonokkal való betartatáshoz** válassza az **egyéni házirend** elemet.
   1. Kattintson a **Létrehozás** lehetőségre.

## <a name="create-your-conditional-access-policy"></a>Feltételes hozzáférési szabályzat létrehozása

Ez a szakasz bemutatja, hogyan hozhatja létre a kötelező feltételes hozzáférési szabályzatot. Az ebben a rövid útmutatóban szereplő forgatókönyv a következőket használja:

- A használati feltételek elfogadását igénylő felhőalapú alkalmazások helyőrzőként való Azure Portal. 
- A minta felhasználója a feltételes hozzáférési szabályzat teszteléséhez.  

A házirendben állítsa be a következőket:

| Beállítás | Érték |
| --- | --- |
| Felhasználók és csoportok | Isabella Simonsen |
| Felhőalkalmazások | Microsoft Azure-kezelés |
| Hozzáférés biztosítása | Saját felhasználási feltételek |

:::image type="content" source="./media/require-tou/1234.png" alt-text="A szabályzatot meghatározó Azure Portal panel képernyőképe. A nyilak azt jelzik, hogy a szabályzat hozzáférést biztosít a saját T-hez, és egy felhasználót és egy alkalmazást tartalmaz." border="false":::

**A feltételes hozzáférési szabályzat konfigurálása:**

1. Az **új** lap **név** szövegmezőbe írja be a következőt: az **Isabella kötelező felhasználási** helyének megadása.

   ![Name](./media/require-tou/71.png)

1. A **hozzárendelés** szakaszban kattintson a **felhasználók és csoportok** elemre.

   :::image type="content" source="./media/require-tou/06.png" alt-text="A házirendet meghatározó Azure Portal ablaktábla hozzárendelések szakaszának képernyőképe. A felhasználók és csoportok elem látható, és nincs kiválasztva." border="false":::

1. A **felhasználók és csoportok** lapon:

   :::image type="content" source="./media/require-tou/24.png" alt-text="Képernyőkép a felhasználók és csoportok lap include (beágyazás) lapjáról. Válassza ki a felhasználók és csoportok lehetőséget a felhasználók és csoportok lehetőség kiválasztásával. Válassza ki a kijelölt elemet." border="false":::

   1. Kattintson a **felhasználók és csoportok kiválasztása** elemre, majd válassza a **felhasználók és csoportok** lehetőséget.
   1. Kattintson a **Kiválasztás** elemre.
   1. A **kiválasztás** lapon válassza az **Isabella Simonsen** elemet, majd kattintson a **kiválasztás** elemre.
   1. A **felhasználók és csoportok** lapon kattintson a **kész** gombra.
1. Kattintson a **Cloud apps** lehetőségre.

   :::image type="content" source="./media/require-tou/08.png" alt-text="A házirendet meghatározó Azure Portal ablaktábla hozzárendelések szakaszának képernyőképe. A Cloud apps elem látható, nincs kiválasztva." border="false":::

1. A **Cloud apps** oldalon:

   ![Felhőalapú alkalmazások kiválasztása](./media/require-tou/26.png)

   1. Kattintson az **alkalmazások kiválasztása** elemre.
   1. Kattintson a **Kiválasztás** elemre.
   1. A **kiválasztás** lapon válassza a **Microsoft Azure felügyelet** lehetőséget, majd kattintson a **kiválasztás** gombra.
   1. A **Cloud apps** lapon kattintson a **kész** gombra.
1. A **hozzáférés-vezérlések** szakaszban kattintson a **Grant (Engedélyezés**) elemre.

   ![Hozzáférés-vezérlés](./media/require-tou/10.png)

1. A **támogatás** lapon:

   ![Engedély](./media/require-tou/111.png)

   1. Válassza a **Hozzáférés biztosítása** lehetőséget.
   1. Válassza **a saját felhasználási** feltételek lehetőséget.
   1. Kattintson a **Kiválasztás** elemre.
1. A **házirend engedélyezése** szakaszban kattintson **a be** gombra.

   ![Szabályzat engedélyezése](./media/require-tou/18.png)

1. Kattintson a **Létrehozás** lehetőségre.

## <a name="evaluate-a-simulated-sign-in"></a>Szimulált bejelentkezés kiértékelése

Most, hogy konfigurálta a feltételes hozzáférési házirendet, érdemes tudnia, hogy a várt módon működik-e. Első lépésként használja a feltételes hozzáférés, mi a teendő, ha a szabályzat eszközzel szimulálni kívánja a felhasználó bejelentkezési adatait. A szimuláció megbecsüli a bejelentkezésnek a szabályzatokra gyakorolt hatását, és létrehoz egy szimulációs jelentést.  

A **What if** Policy értékelési eszköz inicializálásához állítsa be a következőt:

- **Isabella Simonsen** felhasználóként
- **Microsoft Azure-kezelés** Felhőbeli alkalmazásként

A **What if** elemre kattintva létrehoz egy szimulációs jelentést, amely a következőket jeleníti meg:

- **Felhasználási feltételek megkövetelése az Isabella számára** az **alkalmazandó szabályzatok** alatt
- **A saját felhasználási** feltételek az **engedélyezési vezérlők**.

![Mi a teendő, ha a házirend eszköz](./media/require-tou/79.png)

**A feltételes hozzáférési szabályzat kiértékelése:**

1. A [feltételes hozzáférés – házirendek](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) lapon, a felső menüben kattintson a **What if** elemre.  

   ![What If](./media/require-tou/14.png)

1. Kattintson a **felhasználók** elemre, jelölje ki az **Isabella Simonsen**, majd kattintson a **kiválasztás** elemre.

   ![User](./media/require-tou/15.png)

1. Felhőalapú alkalmazás kiválasztása:

   :::image type="content" source="./media/require-tou/16.png" alt-text="Képernyőkép a Cloud apps szakaszról. A szöveg azt jelzi, hogy egy alkalmazás van kiválasztva." border="false":::

   1. Kattintson a **Cloud apps** lehetőségre.
   1. A **Cloud apps lapon** kattintson az **alkalmazások kiválasztása** elemre.
   1. Kattintson a **Kiválasztás** elemre.
   1. A **kiválasztás** lapon válassza a **Microsoft Azure felügyelet** lehetőséget, majd kattintson a **kiválasztás** gombra.
   1. A Cloud apps lapon kattintson a **kész** gombra.
1. Kattintson **What if**.

## <a name="test-your-conditional-access-policy"></a>A feltételes hozzáférési szabályzat tesztelése

Az előző szakaszban megtanulta, hogyan értékelheti ki a szimulált bejelentkezést. A szimuláción kívül a feltételes hozzáférési szabályzatot is tesztelni kell, hogy az a várt módon működjön.

A szabályzat teszteléséhez próbáljon meg bejelentkezni a [Azure Portalba](https://portal.azure.com) az **Isabella Simonsen** -teszt fiók használatával. Meg kell jelennie egy párbeszédpanelnek, amely megköveteli a használati feltételek elfogadását.

:::image type="content" source="./media/require-tou/57.png" alt-text="Képernyőkép az Identity Security Protection használati feltételeit tartalmazó párbeszédpanelről, az elutasítás és az elfogadás gombokkal, valamint a T O U-vel jelölt gombokkal." border="false":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje a teszt felhasználót és a feltételes hozzáférési szabályzatot:

- Ha nem tudja, hogyan törölhet egy Azure AD-felhasználót, tekintse meg [a felhasználók törlése az Azure ad-ból](../fundamentals/add-users-azure-active-directory.md#delete-a-user)című témakört.
- A szabályzat törléséhez válassza ki a szabályzatot, majd kattintson a gyorselérési eszköztár **Törlés** elemére.

    :::image type="content" source="./media/require-tou/33.png" alt-text="Képernyőfelvétel: az M F A megkövetelése Azure Portal felhasználók számára. A helyi menü látható, a törlés kijelölve." border="false":::

- A használati feltételek törléséhez jelölje ki azt, majd kattintson a **feltételek törlése** elemre a felül található eszköztárban.

    :::image type="content" source="./media/require-tou/29.png" alt-text="Képernyőfelvétel: a táblázat egy részét jeleníti meg a használati feltételek dokumentációjában. A saját T O-dokumentum látható. A menüben a feltételek törlése kiemelve." border="false":::

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [MFA megkövetelése adott alkalmazásokhoz](../authentication/tutorial-enable-azure-mfa.md)