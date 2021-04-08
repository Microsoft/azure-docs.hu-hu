---
title: 'Oktatóanyag: a Druva konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a Druva.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 2ef564ae61aab10e1d100ccaf2547248e4a6a8e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96352167"
---
# <a name="tutorial-configure-druva-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés Druva konfigurálása

Az oktatóanyag célja annak bemutatása, hogy milyen lépéseket kell végrehajtani a Druva és a Azure Active Directory (Azure AD) szolgáltatásban az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikusan kiépítsék és kiépítsék a Druva.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő.
* [Egy Druva-bérlő](https://www.druva.com/products/pricing-plans/).
* Rendszergazdai jogosultságokkal rendelkező felhasználói fiók a Druva-ben.

## <a name="assigning-users-to-druva"></a>Felhasználók kiosztása a Druva

Azure Active Directory a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD mely felhasználóinak és/vagy csoportjai számára szükséges a Druva való hozzáférés. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a Druva az alábbi utasításokat követve:
* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-druva"></a>Fontos Tippek a felhasználók Druva való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a Druva-hoz az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor Druva rendel hozzá egy felhasználóhoz, a hozzárendelés párbeszédpanelen ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="setup-druva-for-provisioning"></a>Druva beállítása a kiépítés számára

Az Azure AD-vel való automatikus Druva konfigurálása előtt engedélyeznie kell a SCIM-létesítést a Druva-on.

1. Jelentkezzen be a [Druva felügyeleti konzolra](https://console.druva.com). Navigáljon a **Druva > inSync** elemre.

    ![Druva felügyeleti konzol](media/druva-provisioning-tutorial/menubar.png)

2. Navigáljon a  >  **központi telepítések**  >  **felhasználóinak** kezeléséhez.

    :::image type="content" source="media/druva-provisioning-tutorial/manage.png" alt-text="Képernyőkép a Druva felügyeleti konzolról. A kezelés ki van jelölve, és a kezelés menü látható. Ebben a menüben a központi telepítések területen a felhasználók ki vannak emelve." border="false":::

3.  Navigáljon a **Beállítások** elemre. Kattintson a **jogkivonat előállítása** elemre.

    :::image type="content" source="media/druva-provisioning-tutorial/settings.png" alt-text="Képernyőkép a Druva felügyeleti konzolján található oldalról. A beállítások elem ki van jelölve, és a beállítások lap meg van nyitva. A jogkivonat előállítása gomb ki van emelve." border="false":::

4.  Másolja az **Auth-jogkivonat** értékét. Ez az érték a Druva alkalmazás üzembe helyezés lapjának **titkos jogkivonat** mezőjében jelenik meg a Azure Portal.
    
    :::image type="content" source="media/druva-provisioning-tutorial/auth.png" alt-text="Képernyőfelvétel a jogkivonat létrehozása lapról a Druva felügyeleti konzolon. Az Auth token értékének másolásához a másolási tokent tartalmazó hivatkozás is elérhető." border="false":::

## <a name="add-druva-from-the-gallery"></a>Druva hozzáadása a gyűjteményből

Az Azure AD-vel való automatikus Druva konfigurálásához hozzá kell adnia a Druva az Azure AD Application Gallery-ből a felügyelt SaaS-alkalmazások listájához.

**Ha Druva szeretne hozzáadni az Azure AD-alkalmazás-katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory** lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Druva** kifejezést, válassza az **Druva** elemet az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Druva az eredmények listájában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-druva"></a>Automatikus felhasználó-kiépítés beállítása a Druva 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy Druva alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

> [!TIP]
> Azt is megteheti, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a Druva számára, az [Druva egyszeri bejelentkezés oktatóanyagában](druva-tutorial.md)megadott utasításokat követve. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, bár ez a két funkció egymáshoz tartozik.

### <a name="to-configure-automatic-user-provisioning-for-druva-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a Druva az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Druva** lehetőséget.

    ![Az Druva hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a kiépítési mód legördülő listájáról az automatikus lehetőséggel.](common/provisioning-automatic.png)

5.  A rendszergazdai hitelesítő adatok szakaszban adja `https://apis.druva.com/insync/scim` meg a **bérlői URL-címet**. Adja meg az **Auth token** értékét a **titkos jogkivonatban**. Kattintson a **kapcsolat tesztelése** lehetőségre, hogy az Azure ad képes legyen csatlakozni a Druva. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Druva-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítési e-mail** mezőbe írja be annak a személynek vagy csoportnak az e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és válassza az **e-mail-értesítés küldése hiba** esetén lehetőséget.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a Druva** lehetőséget.

    ![Druva felhasználói leképezések](media/druva-provisioning-tutorial/usermapping.png)

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban található Druva. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Druva felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Druva felhasználói attribútumai](media/druva-provisioning-tutorial/userattribute.png)


10. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

11. Az Azure AD-kiépítési szolgáltatás Druva való engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

12. Adja meg a Druva kiépíteni kívánt felhasználókat és/vagy csoportokat a **Settings (beállítások** ) szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Átadási hatókör](common/provisioning-scope.png)

13. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

    Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a Druva-on végrehajtott összes műveletet ismertetik.

    Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.
    
## <a name="connector-limitations"></a>Összekötő korlátozásai

* A Druva kötelező attribútumként kell eltöltenie az **e-maileket** . 

## <a name="additional-resources"></a>További források

* [A vállalati alkalmazások felhasználói fiókok üzembe](../app-provisioning/configure-automatic-user-provisioning-portal.md)helyezésének kezelése.
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../app-provisioning/check-status-user-account-provisioning.md).
