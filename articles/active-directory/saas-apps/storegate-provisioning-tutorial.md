---
title: 'Oktatóanyag: a Storegate konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a Storegate.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/15/2019
ms.author: Zhchia
ms.openlocfilehash: c984beff630ef90ea33a13e2fef1bca0189c2314
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94357939"
---
# <a name="tutorial-configure-storegate-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés Storegate konfigurálása

Az oktatóanyag célja annak bemutatása, hogy milyen lépéseket kell végrehajtani a Storegate és a Azure Active Directory (Azure AD) szolgáltatásban az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikusan kiépítsék és kiépítsék a Storegate.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Azure AD-bérlő
* [Storegate-bérlő](https://www.storegate.com)
* Rendszergazdai jogosultságokkal rendelkező Storegate felhasználói fiókja.

## <a name="assign-users-to-storegate"></a>Felhasználók Storegate rendelése

Azure Active Directory a hozzárendelések nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD mely felhasználóinak és/vagy csoportjai számára szükséges a Storegate való hozzáférés. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a Storegate az alábbi utasításokat követve:

* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-storegate"></a>Fontos Tippek a felhasználók Storegate való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a Storegate-hoz az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor Storegate rendel hozzá egy felhasználóhoz, a hozzárendelés párbeszédpanelen ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="set-up-storegate-for-provisioning"></a>Storegate beállítása a kiépítés számára

Az Azure AD-vel való automatikus Storegate konfigurálása előtt le kell kérnie néhány kiépítési információt a Storegate-ből.

1. Jelentkezzen be a [Storegate felügyeleti konzolra](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) , és navigáljon a beállításokhoz a jobb felső sarokban található felhasználó ikonra kattintva, és válassza a **Fiókbeállítások** lehetőséget.

    ![Storegate-SCIM hozzáadása](media/storegate-provisioning-tutorial/admin.png)

2. A beállítások területen lépjen a **Team > beállítások** elemre, és ellenőrizze, hogy az **egyszeri bejelentkezés** szakaszban be van-e kapcsolva a váltógomb.

    ![Storegate-beállítások](media/storegate-provisioning-tutorial/team.png)

    ![Storegate váltógomb](media/storegate-provisioning-tutorial/sso.png)

3. Másolja a **bérlői URL-címet** és a **tokent**. Ezek az értékek a **bérlői URL-cím** és a **titkos jogkivonat** mezőiben lesznek megadva a Storegate alkalmazás kiépítés lapján a Azure Portalban. 

    ![Storegate-létrehozási jogkivonat](media/storegate-provisioning-tutorial/token.png)

## <a name="add-storegate-from-the-gallery"></a>Storegate hozzáadása a gyűjteményből

Az Azure AD-vel való automatikus Storegate konfigurálásához hozzá kell adnia a Storegate az Azure AD Application Gallery-ből a felügyelt SaaS-alkalmazások listájához.

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory** lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Storegate** kifejezést, majd az eredmények panelen kattintson a **Storegate** elemre. 

    ![Storegate az eredmények listájában](common/search-new-app.png)

5. Válassza ki a **Storegate (regisztráció)** gombot, amely átirányítja Önt a Storegate bejelentkezési oldalára. 

    ![Storegate OIDC hozzáadása](media/storegate-provisioning-tutorial/signup.png)

6.  Jelentkezzen be a [Storegate felügyeleti konzolra](https://ws1.storegate.com/identity/core/login?signin=c71fb8fe18243c571da5b333d5437367) , és navigáljon a beállításokhoz a jobb felső sarokban található felhasználó ikonra kattintva, és válassza a **Fiókbeállítások** lehetőséget.

    ![Storegate bejelentkezés](media/storegate-provisioning-tutorial/admin.png)

7. A beállítások között navigáljon a **csapat > beállítások** elemre, majd kattintson az egyszeri bejelentkezési szakaszban a váltás váltógomb lehetőségre, Ezzel elindítja a belefoglalt adatforgalmat. Kattintson az **aktiválás** gombra.

    ![Storegate csapat](media/storegate-provisioning-tutorial/team.png)

    ![Storegate egyszeri bejelentkezés](media/storegate-provisioning-tutorial/sso.png)

    ![Storegate aktiválása](media/storegate-provisioning-tutorial/activate.png)

8. Mivel a Storegate egy OpenIDConnect-alkalmazás, a Microsoft munkahelyi fiókjával való bejelentkezéshez válassza a Storegate lehetőséget.

    ![Storegate OIDC-bejelentkezés](media/storegate-provisioning-tutorial/login.png)

9. Sikeres hitelesítés után fogadja el a jóváhagyást kérő lapot. Az alkalmazás ezután automatikusan hozzáadódik a bérlőhöz, és a rendszer átirányítja a Storegate-fiókjába.

    ![Storegate OIDc-beleegyezett](media/storegate-provisioning-tutorial/accept.png)

## <a name="configure-automatic-user-provisioning-to-storegate"></a>Automatikus felhasználó-kiépítés beállítása a Storegate 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy Storegate alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

> [!NOTE]
> Ha többet szeretne megtudni a Storegate SCIM-végpontról, olvassa el [ezt a témakört](https://en-support.storegate.com/article/step-by-step-instruction-how-to-enable-azure-provisioning-to-your-storegate-team-account/).

### <a name="to-configure-automatic-user-provisioning-for-storegate-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a Storegate az Azure AD-ben

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Storegate** lehetőséget.

    ![Az Storegate hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a kiépítési mód legördülő listájáról az automatikus lehetőséggel.](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja `https://dialpad.com/scim` meg a **bérlői URL-címet**. Adja meg a beolvasott és a korábban mentett értéket a **titkos token** Storegate. Kattintson a **kapcsolat tesztelése** lehetőségre, hogy az Azure ad képes legyen csatlakozni a Storegate. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Storegate-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba** esetén.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a Storegate** lehetőséget.

    ![Storegate felhasználói leképezések](media/storegate-provisioning-tutorial/usermappings.png)

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban található Storegate. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Storegate felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Storegate felhasználói attribútumai](media/storegate-provisioning-tutorial/userattributes.png)

10. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

11. Az Azure AD-kiépítési szolgáltatás Storegate való engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

12. Adja meg a Storegate kiépíteni kívánt felhasználókat és/vagy csoportokat a **Settings (beállítások** ) szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Átadási hatókör](common/provisioning-scope.png)

13. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a Storegate-on végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)
