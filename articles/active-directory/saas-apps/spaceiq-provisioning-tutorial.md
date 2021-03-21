---
title: 'Oktatóanyag: a SpaceIQ konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a SpaceIQ.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/07/2019
ms.author: Zhchia
ms.openlocfilehash: a5bd3c39c293880e31ebe63f85b3814725f6027a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96347293"
---
# <a name="tutorial-configure-spaceiq-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés SpaceIQ konfigurálása

Az oktatóanyag célja annak bemutatása, hogy milyen lépéseket kell végrehajtani a SpaceIQ és a Azure Active Directory (Azure AD) szolgáltatásban az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikusan kiépítsék és kiépítsék a SpaceIQ.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Azure AD-bérlő
* [SpaceIQ-bérlő](https://spaceiq.com/)
* Rendszergazdai jogosultságokkal rendelkező felhasználói fiók a SpaceIQ-ben.

## <a name="assigning-users-to-spaceiq"></a>Felhasználók kiosztása a SpaceIQ

Azure Active Directory a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD mely felhasználóinak és/vagy csoportjai számára szükséges a SpaceIQ való hozzáférés. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a SpaceIQ az alábbi utasításokat követve:
* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-spaceiq"></a>Fontos Tippek a felhasználók SpaceIQ való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a SpaceIQ-hoz az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor SpaceIQ rendel hozzá egy felhasználóhoz, a hozzárendelés párbeszédpanelen ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="set-up-spaceiq-for-provisioning"></a>SpaceIQ beállítása a kiépítés számára

1. Jelentkezzen be a [SpaceIQ felügyeleti konzolra](https://main.spaceiq.com/login/). A képernyő jobb felső sarkában található legördülő menüből kiválasztva válassza ki a **beállításokat** .

    ![SpaceIQ felügyeleti konzol](media/spaceiq-provisioning-tutorial/admin.png)

2.  A **Beállítások** lapon válassza a **harmadik féltől származó integrációk** lehetőséget.

    ![SpaceIQ-SCIM hozzáadása](media/spaceiq-provisioning-tutorial/thirdparty.png)

3.  Navigáljon a **kiépítés és az egyszeri bejelentkezés** lapra. Keresse meg az **Azure** csempét. Kattintson az **aktiválás** gombra.

    ![SpaceIQ kiépítés és SSO](media/spaceiq-provisioning-tutorial/provisioning.png)

    ![SpaceIQ aktiválása az Azure-ban ](media/spaceiq-provisioning-tutorial/azure.png)

3.  Másolja a **scim tulajdonosi jogkivonatát**. Ez az érték a SpaceIQ alkalmazás üzembe helyezés lapjának titkos jogkivonat mezőjében jelenik meg a Azure Portal. Kattintson az **aktiválás** gombra.

    ![SpaceIQ-létrehozási jogkivonat](media/spaceiq-provisioning-tutorial/token.png)

## <a name="add-spaceiq-from-the-gallery"></a>SpaceIQ hozzáadása a gyűjteményből

Az Azure AD-vel való automatikus SpaceIQ konfigurálása előtt hozzá kell adnia a SpaceIQ az Azure AD Application Gallery-ből a felügyelt SaaS-alkalmazások listájához.

**Ha SpaceIQ szeretne hozzáadni az Azure AD-alkalmazás-katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory** lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **SpaceIQ** kifejezést, válassza az **SpaceIQ** elemet az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![SpaceIQ az eredmények listájában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-spaceiq"></a>Automatikus felhasználó-kiépítés beállítása a SpaceIQ 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy SpaceIQ alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

> [!TIP]
> Azt is megteheti, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a SpaceIQ számára, az [SpaceIQ egyszeri bejelentkezés oktatóanyagában](./spaceiq-tutorial.md)megadott utasításokat követve. Az egyszeri bejelentkezést az automatikus felhasználó-kiépítés függetlenül lehet konfigurálni, bár ez a két funkció

### <a name="to-configure-automatic-user-provisioning-for-spaceiq-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a SpaceIQ az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **SpaceIQ** lehetőséget.

    ![Az SpaceIQ hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a kiépítési mód legördülő listájáról az automatikus lehetőséggel.](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja `https://api.spaceiq.com/scim` meg a **bérlői URL-címet**. Adja meg a **scim-hitelesítési jogkivonat** értékét a **titkos tokenben**. Kattintson a **kapcsolat tesztelése** lehetőségre, hogy az Azure ad képes legyen csatlakozni a SpaceIQ. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a SpaceIQ-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba** esetén.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a SpaceIQ** lehetőséget.

    ![SpaceIQ felhasználói leképezések](media/spaceiq-provisioning-tutorial/usermapping.png)

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban található SpaceIQ. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a SpaceIQ felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![SpaceIQ felhasználói attribútumai](media/spaceiq-provisioning-tutorial/userattributes.png)

11. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

12. Az Azure AD-kiépítési szolgáltatás SpaceIQ való engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

13. Adja meg a SpaceIQ kiépíteni kívánt felhasználókat és/vagy csoportokat a **Settings (beállítások** ) szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Átadási hatókör](common/provisioning-scope.png)

14. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a SpaceIQ-on végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)