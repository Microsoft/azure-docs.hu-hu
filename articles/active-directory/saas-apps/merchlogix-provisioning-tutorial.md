---
title: 'Oktatóanyag: a MerchLogix konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a MerchLogix.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: zhchia
ms.openlocfilehash: 4d0a52f06a751fba57a00615e2d57485ff740d04
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94359697"
---
# <a name="tutorial-configure-merchlogix-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés MerchLogix konfigurálása

Az oktatóanyag célja annak bemutatása, hogy milyen lépéseket kell végrehajtani a MerchLogix és a Azure Active Directory (Azure AD) szolgáltatásban az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikusan kiépítsék és kiépítsék a MerchLogix.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Azure AD-bérlő
* MerchLogix-bérlő
* Technikai kapcsolattartó a MerchLogix, aki megadhatja a SCIM-végpont URL-címét és a felhasználói regisztrációhoz szükséges titkos tokent

## <a name="adding-merchlogix-from-the-gallery"></a>MerchLogix hozzáadása a gyűjteményből

Az Azure AD-vel való automatikus MerchLogix konfigurálása előtt hozzá kell adnia a MerchLogix az Azure AD Application Gallery-ből a felügyelt SaaS-alkalmazások listájához.

**Ha MerchLogix szeretne hozzáadni az Azure AD-alkalmazás-katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson a **Azure Active Directory** ikonra. 

    ![A Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**  >  **minden alkalmazás** elemhez.

    ![A vállalati alkalmazások szakasz][2]

3. MerchLogix hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb][3]

4. A keresőmezőbe írja be a **MerchLogix** kifejezést.

5. Az eredmények panelen válassza a **MerchLogix** lehetőséget, majd a **Hozzáadás** gombra kattintva adja hozzá a MerchLogix az SaaS-alkalmazások listájához.

    ![Képernyőkép a Hozzáadás a hasábról szakaszban a név megadása szövegmezővel.][4]

## <a name="assigning-users-to-merchlogix"></a>Felhasználók kiosztása a MerchLogix

Azure Active Directory a "hozzárendelések" nevű fogalom használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában a rendszer csak azokat a felhasználókat és/vagy csoportokat szinkronizálja, akik az Azure AD-alkalmazáshoz lettek rendelve. 

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD mely felhasználóinak és/vagy csoportjai számára szükséges a MerchLogix való hozzáférés. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a MerchLogix az alábbi utasításokat követve:

* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-merchlogix"></a>Fontos Tippek a felhasználók MerchLogix való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a MerchLogix-hoz a kezdeti automatikus felhasználó-kiépítési konfiguráció teszteléséhez. A tesztek sikeres végrehajtása után később további felhasználók és/vagy csoportok is hozzárendelhetők.

* Amikor MerchLogix rendel hozzá egy felhasználóhoz, a hozzárendelés párbeszédpanelen ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="configuring-automatic-user-provisioning-to-merchlogix"></a>Automatikus felhasználó-kiépítés beállítása a MerchLogix 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy MerchLogix alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

> [!TIP]
> Azt is megteheti, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a MerchLogix számára, az [MerchLogix egyszeri bejelentkezés oktatóanyagában](merchlogix-tutorial.md)megadott utasításokat követve. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, bár ez a két funkció egymáshoz tartozik.

### <a name="to-configure-automatic-user-provisioning-for-merchlogix-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a MerchLogix az Azure AD-ben:

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és keresse meg **Azure Active Directory > vállalati alkalmazások > minden alkalmazás** lehetőséget.

2. Az SaaS-alkalmazások listájából válassza a MerchLogix lehetőséget.

3. Válassza a **Kiépítés** lapot.

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a MerchLogix-Prisioning szakaszról, az üzembe helyezési lehetőséggel, a létesítési mód automatikus értékre állítása és a kapcsolódási teszt beállítása beállítás.](./media/merchlogix-provisioning-tutorial/Merchlogix1.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban:

    * A **bérlői URL-cím** mezőben adja meg a scim-végpont URL-címét, amelyet a MerchLogix technikai kapcsolattartója adott meg.

    * A **titkos jogkivonat** mezőben adja meg a MerchLogix-technikai kapcsolattartó által megadott titkos jogkivonatot.

6. Az 5. lépésben megjelenő mezők kitöltése után kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad képes legyen csatlakozni a MerchLogix. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a MerchLogix-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

7. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba** esetén.

8. Kattintson a **Mentés** gombra.

9. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a MerchLogix** lehetőséget.

10. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban található MerchLogix. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a MerchLogix felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

11. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása a MerchLogix** lehetőséget.

12. Tekintse át az Azure AD-ből szinkronizált MerchLogix az attribútumok **leképezése** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a MerchLogix tartozó csoportok egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

13. Az Azure AD-kiépítési szolgáltatás MerchLogix való engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

14. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a MerchLogix-on végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: common/select-azuread.png
[2]: common/enterprise-applications.png
[3]: common/add-new-app.png
[4]: common/search-new-app.png
