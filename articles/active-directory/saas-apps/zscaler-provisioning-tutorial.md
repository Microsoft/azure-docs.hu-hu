---
title: 'Oktatóanyag: a Zscaler konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a Zscaler.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 9f368a4aebc4d5de38ebbab800241366650633e6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97936583"
---
# <a name="tutorial-configure-zscaler-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés Zscaler konfigurálása

Az oktatóanyag célja annak bemutatása, hogy milyen lépéseket kell végrehajtani a Zscaler és a Azure Active Directory (Azure AD) szolgáltatásban az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikusan kiépítsék és kiépítsék a Zscaler.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md).
>

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következőkkel:

* Egy Azure AD-bérlő.
* Egy Zscaler-bérlő.
* Rendszergazdai jogosultságokkal rendelkező felhasználói fiók a Zscaler-ben.

> [!NOTE]
> Az Azure AD-kiépítés integrációja a Zscaler SCIM API-ra támaszkodik, amely Zscaler-fejlesztők számára elérhető a vállalati csomaggal rendelkező fiókok számára.

## <a name="adding-zscaler-from-the-gallery"></a>Zscaler hozzáadása a gyűjteményből

Az Azure AD-vel való automatikus Zscaler konfigurálása előtt hozzá kell adnia a Zscaler az Azure AD Application Gallery-ből a felügyelt SaaS-alkalmazások listájához.

**Ha Zscaler szeretne hozzáadni az Azure AD-alkalmazás-katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Zscaler** kifejezést, válassza a **Zscaler** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Zscaler az eredmények listájában](common/search-new-app.png)

## <a name="assigning-users-to-zscaler"></a>Felhasználók kiosztása a Zscaler

Azure Active Directory a "hozzárendelések" nevű fogalom használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában a rendszer csak azokat a felhasználókat és/vagy csoportokat szinkronizálja, akik az Azure AD-alkalmazáshoz lettek rendelve.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD mely felhasználóinak és/vagy csoportjai számára szükséges a Zscaler való hozzáférés. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a Zscaler az alábbi utasításokat követve:

* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zscaler"></a>Fontos Tippek a felhasználók Zscaler való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a Zscaler-hoz az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor Zscaler rendel hozzá egy felhasználóhoz, a hozzárendelés párbeszédpanelen ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="configuring-automatic-user-provisioning-to-zscaler"></a>Automatikus felhasználó-kiépítés beállítása a Zscaler

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy Zscaler alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

> [!TIP]
> Azt is megteheti, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a Zscaler számára, az [Zscaler egyszeri bejelentkezés oktatóanyagában](zscaler-tutorial.md)megadott utasításokat követve. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, bár ez a két funkció egymáshoz tartozik.

> [!NOTE]
> Ha a felhasználók és csoportok kiosztása vagy kiosztása megtörtént, javasoljuk, hogy rendszeres időközönként indítsa újra az üzembe helyezést, hogy a csoporttagság megfelelően frissüljenek. Az újraindítás után a szolgáltatás kikényszeríti a szolgáltatást az összes csoport újraértékelésére és a tagságok frissítésére. 

### <a name="to-configure-automatic-user-provisioning-for-zscaler-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a Zscaler az Azure AD-ben:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és válassza a **vállalati alkalmazások** lehetőséget, válassza a **minden alkalmazás** lehetőséget, majd válassza a **Zscaler** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Zscaler** lehetőséget.

    ![Az Zscaler hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a Zscaler-kiépítési vállalati alkalmazás oldalsávról a kiépítési lehetőség kiválasztásával.](./media/zscaler-provisioning-tutorial/provisioning-tab.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a kiépítési oldalról a kiépítési móddal automatikus beállítással.](./media/zscaler-provisioning-tutorial/provisioning-credentials.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a Zscaler **-fiók bérlői URL-címét** és **titkos jogkivonatát** a 6. lépésben leírtak szerint.

6. A **bérlői URL-cím** és a **titkos jogkivonat** beszerzéséhez navigáljon az **Adminisztráció > hitelesítési beállítások** elemre a Zscaler-portál felhasználói felületén, és kattintson az **SAML** elemre a **Hitelesítés típusa** alatt.

    ![Képernyőkép a hitelesítési beállítások lapról.](./media/zscaler-provisioning-tutorial/secret-token-1.png)

    Az **SAML konfigurálása** elemre kattintva nyissa meg a **konfigurációs SAML** -beállításokat.

    ![Képernyőkép: az M L konfigurálása párbeszédpanel, amely az U R L és a tulajdonosi jogkivonat szövegmezőit jelöli.](./media/zscaler-provisioning-tutorial/secret-token-2.png)

    Válassza az **SCIM-Based kiépítés engedélyezése** lehetőséget az **alap URL-cím** és a **tulajdonosi jogkivonat** lekéréséhez, majd mentse a beállításokat. Másolja az **alap URL-címet** a **bérlői URL**-címre, és a **tulajdonosi jogkivonatot**  a Azure Portal **titkos jogkivonatára** .

7. Az 5. lépésben megjelenő mezők kitöltése után kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad képes legyen csatlakozni a Zscaler. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Zscaler-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Képernyőkép a rendszergazdai hitelesítő adatokról című szakaszban, az elnevezett kapcsolatok tesztelése lehetőséggel.](./media/zscaler-provisioning-tutorial/test-connection.png)

8. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be az **e-mail-értesítés küldése hiba** esetén jelölőnégyzetet.

    ![Képernyőkép az értesítő e-mailek szövegmezőről.](./media/zscaler-provisioning-tutorial/notification.png)

9. Kattintson a **Mentés** gombra.

10. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a Zscaler** lehetőséget.

    ![A leképezések szakasz képernyőképe a Azure Active Directory felhasználók szinkronizálása a Zscaler lehetőségre kiemelve.](./media/zscaler-provisioning-tutorial/user-mappings.png)

11. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban található Zscaler. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Zscaler felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Képernyőfelvétel: az attribútum-hozzárendelések szakasz, amelyen hét leképezés látható.](./media/zscaler-provisioning-tutorial/user-attribute-mappings.png)

12. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása a Zscaler** lehetőséget.

    ![A leképezések szakasz képernyőképe a Azure Active Directory csoportok szinkronizálása a Zscaler beállítás kiemelve.](./media/zscaler-provisioning-tutorial/group-mappings.png)

13. Tekintse át az Azure AD-ből szinkronizált Zscaler az attribútumok **leképezése** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Zscaler tartozó csoportok egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Képernyőfelvétel: az attribútum-hozzárendelések szakasz, amelyen három leképezés látható.](./media/zscaler-provisioning-tutorial/group-attribute-mappings.png)

14. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

15. Az Azure AD-kiépítési szolgáltatás Zscaler való engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Képernyőfelvétel: a létesítési állapot beállítás értéke: on.](./media/zscaler-provisioning-tutorial/provisioning-status.png)

16. Adja meg a Zscaler kiépíteni kívánt felhasználókat és/vagy csoportokat a **Settings (beállítások** ) szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Képernyőfelvétel a hatókör beállításról a kijelölt felhasználók és csoportok szinkronizálása lehetőség kiemelve.](./media/zscaler-provisioning-tutorial/scoping.png)

17. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Képernyőkép a Zscaler-kiépítési vállalati alkalmazás oldalsávról a Save (Mentés) lehetőséggel.](./media/zscaler-provisioning-tutorial/save-provisioning.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a Zscaler-on végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zscaler-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-provisioning-tutorial/tutorial-general-03.png