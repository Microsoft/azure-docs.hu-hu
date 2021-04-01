---
title: 'Oktatóanyag: a Leapsome konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a Leapsome.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/28/2019
ms.author: jeedes
ms.openlocfilehash: b7c1c995ead9a8d66cd11fb4579cc49e12b487df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96351588"
---
# <a name="tutorial-configure-leapsome-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés Leapsome konfigurálása

Az oktatóanyag célja annak bemutatása, hogy milyen lépéseket kell végrehajtani a Leapsome és a Azure Active Directory (Azure AD) szolgáltatásban az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikusan kiépítsék és kiépítsék a Leapsome.

> [!NOTE]
>  Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md).
>
> Ez az összekötő jelenleg előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő.
* Egy [Leapsome](https://www.Leapsome.com/en/pricing) -bérlő.
* Rendszergazdai jogosultságokkal rendelkező felhasználói fiók a Leapsome-ben.

## <a name="assigning-users-to-leapsome"></a>Felhasználók kiosztása a Leapsome

Azure Active Directory a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD mely felhasználóinak és/vagy csoportjai számára szükséges a Leapsome való hozzáférés. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a Leapsome az alábbi utasításokat követve:
* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)


## <a name="important-tips-for-assigning-users-to-leapsome"></a>Fontos Tippek a felhasználók Leapsome való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a Leapsome-hoz az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor Leapsome rendel hozzá egy felhasználóhoz, a hozzárendelés párbeszédpanelen ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.


## <a name="setup-leapsome-for-provisioning"></a>Leapsome beállítása a kiépítés számára

1. Jelentkezzen be a [Leapsome felügyeleti konzolra](https://www.Leapsome.com/app/#/login). Navigáljon a **beállítások > a rendszergazdai beállítások** elemre.

    ![Leapsome felügyeleti konzol](media/Leapsome-provisioning-tutorial/leapsome-admin-console.png)

2.  Navigáljon az **integrációk > scim a felhasználók üzembe** helyezése.

    ![Leapsome-SCIM hozzáadása](media/Leapsome-provisioning-tutorial/leapsome-add-scim.png)

3.  Másolja a **scim hitelesítési tokent**. Ez az érték a Leapsome alkalmazás üzembe helyezés lapjának titkos jogkivonat mezőjében jelenik meg a Azure Portal.

    ![Leapsome-létrehozási jogkivonat](media/Leapsome-provisioning-tutorial/leapsome-create-token.png)

## <a name="add-leapsome-from-the-gallery"></a>Leapsome hozzáadása a gyűjteményből

Az Azure AD-vel való automatikus Leapsome konfigurálása előtt hozzá kell adnia a Leapsome az Azure AD Application Gallery-ből a felügyelt SaaS-alkalmazások listájához.

**Ha Leapsome szeretne hozzáadni az Azure AD-alkalmazás-katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory** lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Leapsome** kifejezést, válassza az **Leapsome** elemet az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Leapsome az eredmények listájában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-leapsome"></a>Automatikus felhasználó-kiépítés beállítása a Leapsome 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy Leapsome alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

> [!TIP]
> Azt is megteheti, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a Leapsome számára, az [Leapsome egyszeri bejelentkezés oktatóanyagában](Leapsome-tutorial.md)megadott utasításokat követve. Az egyszeri bejelentkezést az automatikus felhasználó-kiépítés függetlenül lehet konfigurálni, bár ez a két funkció

### <a name="to-configure-automatic-user-provisioning-for-leapsome-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a Leapsome az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Leapsome** lehetőséget.

    ![Az Leapsome hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a kiépítési mód legördülő listájáról az automatikus lehetőséggel.](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja `https://www.leapsome.com/api/scim` meg a **bérlői URL-címet**. Adja meg a **scim-hitelesítési jogkivonat** értékét a **titkos tokenben**. Kattintson a **kapcsolat tesztelése** lehetőségre, hogy az Azure ad képes legyen csatlakozni a Leapsome. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Leapsome-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba** esetén.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a Leapsome** lehetőséget.

    ![Leapsome felhasználói leképezések](media/Leapsome-provisioning-tutorial/Leapsome-user-mappings.png)

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban található Leapsome. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Leapsome felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Leapsome felhasználói attribútumai](media/Leapsome-provisioning-tutorial/Leapsome-user-attributes.png)

10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása a Leapsome** lehetőséget.

    ![Leapsome csoport-hozzárendelések](media/Leapsome-provisioning-tutorial/Leapsome-group-mappings.png)

11. Tekintse át az Azure AD-ből szinkronizált Leapsome az attribútumok **leképezése** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Leapsome tartozó csoportok egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Leapsome csoport attribútumai](media/Leapsome-provisioning-tutorial/Leapsome-group-attributes.png)

12. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

13. Az Azure AD-kiépítési szolgáltatás Leapsome való engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg a Leapsome kiépíteni kívánt felhasználókat és/vagy csoportokat a **Settings (beállítások** ) szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Átadási hatókör](common/provisioning-scope.png)

15. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a Leapsome-on végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="connector-limitations"></a>Összekötő korlátozásai

* A Leapsome egyedi **felhasználónévre** van szükség.
* A Leapsome csak a munkahelyi e-mail-címek mentését engedélyezi.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)
