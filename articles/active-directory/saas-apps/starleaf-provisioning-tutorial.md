---
title: 'Oktatóanyag: a StarLeaf konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a StarLeaf.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/19/2019
ms.author: zhchia
ms.openlocfilehash: 07c476c0de644ac63c577d466f4691b5cf415334
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94357934"
---
# <a name="tutorial-configure-starleaf-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés StarLeaf konfigurálása

Az oktatóanyag célja annak bemutatása, hogy milyen lépéseket kell végrehajtani a StarLeaf és a Azure Active Directory (Azure AD) szolgáltatásban az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikusan kiépítsék és kiépítsék a StarLeaf.

> [!NOTE]
>  Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md).
>
> Ez az összekötő jelenleg előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő.
* [Egy StarLeaf-bérlő](https://starleaf.com/).
* Rendszergazdai jogosultságokkal rendelkező felhasználói fiók a StarLeaf-ben.

## <a name="assign-users-to-starleaf"></a>Felhasználók StarLeaf rendelése
Azure Active Directory a hozzárendelések nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt el kell döntenie, hogy mely felhasználókra és csoportokra van szükség az Azure AD-ben a StarLeaf eléréséhez. Ezt követően az [utasításokat](../manage-apps/assign-user-or-group-access-portal.md)követve hozzárendelheti a felhasználókat és a csoportokat a StarLeaf.

## <a name="important-tips-for-assigning-users-to-starleaf"></a>Fontos Tippek a felhasználók StarLeaf való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a StarLeaf-hoz az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. További felhasználók és csoportok később is hozzárendelhetők.

* Amikor StarLeaf rendel hozzá egy felhasználóhoz, a hozzárendelés párbeszédpanelen ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="set-up-starleaf-for-provisioning"></a>StarLeaf beállítása a kiépítés számára

Mielőtt konfigurálja a StarLeaf-t az Azure AD-vel való automatikus felhasználói üzembe helyezéshez, konfigurálnia kell a SCIM-létesítést a StarLeaf-ben:

1. Jelentkezzen be a [StarLeaf felügyeleti konzolra](https://portal.starleaf.com/#page=login). Navigáljon az **integrációk**  >  **hozzáadásához**.

    ![Képernyőkép a StarLeaf felügyeleti konzolról az integrációkkal, és vegye fel az integrációs lehetőségeket.](media/starleaf-provisioning-tutorial/image00.png)

2. Válassza ki a Microsoft Azure Active Directory kívánt **típust** . Adjon meg egy megfelelő nevet a **névben**. Kattintson az **Alkalmaz** gombra.

    ![Képernyőkép az integráció hozzáadása párbeszédpanelről, a típus és a név szövegmezőben.](media/starleaf-provisioning-tutorial/image01.png)

3.  Ekkor megjelenik a **scim alap URL-címe** és a **hozzáférési jogkivonat** értéke. Ezek az értékek a **bérlői URL-cím** és a **titkos jogkivonat** mezőiben lesznek megadva a StarLeaf alkalmazás kiépítés lapján a Azure Portal. 

    ![Képernyőkép: az integráció szerkesztése párbeszédpanel, ahol a Type, a Name és a SCIM Base URL-cím szövegmező található.](media/starleaf-provisioning-tutorial/image02.png)

## <a name="add-starleaf-from-the-gallery"></a>StarLeaf hozzáadása a gyűjteményből

Az Azure AD-vel való automatikus StarLeaf konfigurálásához hozzá kell adnia a StarLeaf az Azure AD Application Gallery-ből a felügyelt SaaS-alkalmazások listájához.

**Ha StarLeaf szeretne hozzáadni az Azure AD-alkalmazás-katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory** lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **StarLeaf** kifejezést, majd az eredmények panelen kattintson a **StarLeaf** elemre.
    ![StarLeaf az eredmények listájában](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-starleaf"></a>Automatikus felhasználó-kiépítés beállítása a StarLeaf

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy StarLeaf alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **StarLeaf** lehetőséget.

    ![Az StarLeaf hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a kiépítési mód legördülő listájáról az automatikus lehetőséggel.](common/provisioning-automatic.png)

5. A rendszergazdai hitelesítő adatok szakaszban adja meg a **scim alap URL-címét** és a **hozzáférési jogkivonat** azon értékeit, amelyeket a **bérlői URL-cím** és a **titkos jogkivonat** korábban lekért. Kattintson a **kapcsolat tesztelése** lehetőségre, hogy az Azure ad képes legyen csatlakozni a StarLeaf. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a StarLeaf-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be az **e-mail-értesítés küldése hiba** esetén jelölőnégyzetet.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a StarLeaf** lehetőséget.

    ![Képernyőfelvétel a leképezésekről szakasz a Azure Active Directory felhasználók szinkronizálása a StarLeaf lehetőségre.](media/starleaf-provisioning-tutorial/usermapping.png)

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban található StarLeaf. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a StarLeaf felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Képernyőkép az attribútum-hozzárendelésekről szakasz, amely megjeleníti a kilenc leképezést.](media/starleaf-provisioning-tutorial/userattribute.png)


10. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.


11. Az Azure AD-kiépítési szolgáltatás StarLeaf való engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

12. Adja meg a StarLeaf kiépíteni kívánt felhasználókat és/vagy csoportokat a **Settings (beállítások** ) szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Átadási hatókör](common/provisioning-scope.png)

13. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a StarLeaf-on végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md) helyezésével kapcsolatban.

## <a name="connector-limitations"></a>Összekötő korlátozásai

* A StarLeaf jelenleg nem támogatja a csoportok üzembe helyezését. 
* A StarLeaf használatához az **e-mailek** és a **felhasználónevek** értékének azonosnak kell lennie.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* Megtudhatja, hogyan [tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../app-provisioning/check-status-user-account-provisioning.md).
