---
title: 'Oktatóanyag: a Proxyclick konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a Proxyclick.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/3/2019
ms.author: jeedes
ms.openlocfilehash: f7d2a6f01e891a7fb1c14cde552d66679e474139
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94359170"
---
# <a name="tutorial-configure-proxyclick-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés Proxyclick konfigurálása

Az oktatóanyag célja annak bemutatása, hogy milyen lépéseket kell végrehajtani a Proxyclick és a Azure Active Directory (Azure AD) szolgáltatásban az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikusan kiépítsék és kiépítsék a Proxyclick.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Azure AD-bérlő
* [Proxyclick-bérlő](https://www.proxyclick.com/pricing)
* Rendszergazdai jogosultságokkal rendelkező felhasználói fiók a Proxyclick-ben.

## <a name="add-proxyclick-from-the-gallery"></a>Proxyclick hozzáadása a gyűjteményből

Az Azure AD-vel való automatikus Proxyclick konfigurálása előtt hozzá kell adnia a Proxyclick az Azure AD Application Gallery-ből a felügyelt SaaS-alkalmazások listájához.

**Ha Proxyclick szeretne hozzáadni az Azure AD-alkalmazás-katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory** lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Proxyclick** kifejezést, válassza az **Proxyclick** elemet az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Proxyclick az eredmények listájában](common/search-new-app.png)

## <a name="assigning-users-to-proxyclick"></a>Felhasználók kiosztása a Proxyclick

Azure Active Directory a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD mely felhasználóinak és/vagy csoportjai számára szükséges a Proxyclick való hozzáférés. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a Proxyclick az alábbi utasításokat követve:

* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-proxyclick"></a>Fontos Tippek a felhasználók Proxyclick való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a Proxyclick-hoz az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor Proxyclick rendel hozzá egy felhasználóhoz, a hozzárendelés párbeszédpanelen ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="configuring-automatic-user-provisioning-to-proxyclick"></a>Automatikus felhasználó-kiépítés beállítása a Proxyclick 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy Proxyclick alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

> [!TIP]
> Azt is megteheti, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a Proxyclick számára, az [Proxyclick egyszeri bejelentkezés oktatóanyagában](proxyclick-tutorial.md)megadott utasításokat követve. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, bár ez a két funkció egymáshoz tartozik.

### <a name="to-configure-automatic-user-provisioning-for-proxyclick-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a Proxyclick az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Proxyclick** lehetőséget.

    ![Az Proxyclick hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a kiépítési mód legördülő listájáról az automatikus lehetőséggel.](common/provisioning-automatic.png)

5. A Proxyclick **-fiók bérlői URL-címének** és **titkos jogkivonatának** lekéréséhez kövesse az útmutatót a 6. lépésben leírtak szerint.

6. Jelentkezzen be a [Proxyclick felügyeleti konzolra](https://app.proxyclick.com/login//?destination=%2Fdefault). Navigáljon a **Beállítások**  >  **integrációk**  >  **böngészés piactéren**.

    ![Proxyclick-beállítások](media/proxyclick-provisioning-tutorial/proxyclick09.png)

    ![Proxyclick-integrációk](media/proxyclick-provisioning-tutorial/proxyclick01.png)

    ![Proxyclick piactér](media/proxyclick-provisioning-tutorial/proxyclick02.png)

    Válassza az **Azure ad** lehetőséget. Kattintson a **Telepítés most** lehetőségre.

    ![Proxyclick az Azure AD-ben](media/proxyclick-provisioning-tutorial/proxyclick03.png)

    ![Proxyclick telepítése](media/proxyclick-provisioning-tutorial/proxyclick04.png)

    Válassza a **felhasználó kiépítése** lehetőséget, majd kattintson az **integráció elindítása** elemre. 

    ![Proxyclick-felhasználó kiépítés](media/proxyclick-provisioning-tutorial/proxyclick05.png)

    A megfelelő beállítások konfigurációs felhasználói felületének most megjelenik a **Beállítások**  >  **integrációja** területen. Válassza a **Beállítások** lehetőséget az **Azure ad (felhasználó kiépítés)** területen.

    ![Proxyclick létrehozása](media/proxyclick-provisioning-tutorial/proxyclick06.png)

    Itt megtalálhatja a **bérlői URL-címet** és a **titkos jogkivonatot** .

    ![Proxyclick-létrehozási jogkivonat](media/proxyclick-provisioning-tutorial/proxyclick07.png)

7. Az 5. lépésben megjelenő mezők kitöltése után kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad képes legyen csatlakozni a Proxyclick. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Proxyclick-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

8. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba** esetén.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

9. Kattintson a **Mentés** gombra.

10. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a Proxyclick** lehetőséget.

    ![Proxyclick felhasználói leképezések](media/proxyclick-provisioning-tutorial/Proxyclick-user-mappings.png)

11. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban található Proxyclick. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Proxyclick felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Proxyclick felhasználói attribútumai](media/proxyclick-provisioning-tutorial/Proxyclick-user-attribute.png)

13. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

14. Az Azure AD-kiépítési szolgáltatás Proxyclick való engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

15. Adja meg a Proxyclick kiépíteni kívánt felhasználókat és/vagy csoportokat a **Settings (beállítások** ) szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Átadási hatókör](common/provisioning-scope.png)

16. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a Proxyclick-on végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="connector-limitations"></a>Összekötő korlátozásai

* A Proxyclick használatához az **e-mailek** és a **felhasználónevek** értékének azonosnak kell lennie. Bármelyik attribútum frissítései módosítják a másik értéket.
* A Proxyclick nem támogatja a csoportok üzembe helyezését.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)

