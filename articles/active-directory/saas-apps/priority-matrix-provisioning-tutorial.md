---
title: 'Oktatóanyag: prioritási mátrix konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a prioritás mátrixba.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/08/2019
ms.author: Zhchia
ms.openlocfilehash: e79f21300325c6b451dd564bf2c69830f003f55c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94357861"
---
# <a name="tutorial-configure-priority-matrix-for-automatic-user-provisioning"></a>Oktatóanyag: prioritási mátrix konfigurálása a felhasználók automatikus kiépítési felállításához

Ennek az oktatóanyagnak a célja, hogy bemutassa a prioritási mátrixban és Azure Active Directory (Azure AD) végrehajtandó lépéseket az Azure AD konfigurálásához a felhasználók és/vagy csoportok prioritási mátrixba való automatikus kiépítéséhez és kiépítéséhez.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Azure AD-bérlő
* [Prioritású mátrix bérlő](https://appfluence.com/pricing/)
* Felhasználói fiók egy prioritási mátrixban rendszergazdai engedélyekkel.

## <a name="assign-users-to-priority-matrix"></a>Felhasználók kiosztása prioritási mátrixba

Azure Active Directory a hozzárendelések nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

Az automatikus felhasználó-kiépítés konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD-beli felhasználók és/vagy csoportok számára szükséges-e a prioritási mátrixhoz való hozzáférés. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a prioritási mátrixhoz az alábbi utasítások követésével:

* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-priority-matrix"></a>Fontos Tippek a felhasználók prioritási mátrixhoz való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a prioritási mátrixhoz az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor egy felhasználót a prioritási mátrixhoz rendel, ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelési párbeszédpanelen. Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="set-up-priority-matrix-for-provisioning"></a>Prioritási mátrix beállítása a kiépítés számára

Mielőtt beállítja a prioritási mátrixot az Azure AD-vel való automatikus felhasználó-kiépítés során, néhány kiépítési információt le kell kérnie a Priority Matrixból.

1. Jelentkezzen be a [priority Matrix felügyeleti konzolra](https://sync.appfluence.com/accounts/login/?next=/accounts/provisioning).

3. Kattintson a **OAuth bejelentkezési jogkivonat** lehetőségre a prioritási mátrixhoz

    ![Prioritási mátrix hozzáadása SCIM](media/priority-matrix-provisioning-tutorial/oauthlogin.png)

4. Kattintson az **új jogkivonat beolvasása** gombra. Másolja a **jogkivonat-karakterláncot**. Ez az érték a Azure Portal a Priority Matrix-alkalmazás létesítés lapjának **titkos jogkivonat** mezőjében jelenik meg. 

    ![Prioritási mátrix létrehozási jogkivonata](media/priority-matrix-provisioning-tutorial/token.png)

## <a name="add-priority-matrix-from-the-gallery"></a>Prioritási mátrix hozzáadása a gyűjteményből

Az Azure AD-vel való automatikus felhasználó-kiépítés prioritási mátrixának konfigurálásához hozzá kell adnia egy prioritási mátrixot az Azure AD-alkalmazás-katalógusból a felügyelt SaaS-alkalmazások listájára.

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory** lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **prioritási mátrix** kifejezést, majd az eredmények panelen válassza a **prioritási mátrix** lehetőséget. 

    ![Prioritási mátrix az eredmények listájában](common/search-new-app.png)

5. Válassza a **Feliratkozás a prioritási mátrixhoz** gombot, amely átirányítja a prioritási mátrix bejelentkezési oldalára. 

    ![Prioritási mátrix OIDC hozzáadása](media/priority-matrix-provisioning-tutorial/signup.png)

6. Mivel a prioritási mátrix egy OpenIDConnect-alkalmazás, a Microsoft munkahelyi fiókjával bejelentkezhet a Priority Matrixba.

    ![Prioritási mátrix OIDC bejelentkezés](media/priority-matrix-provisioning-tutorial/msftsignin.png)

7. Sikeres hitelesítés után fogadja el a jóváhagyást kérő lapot. Az alkalmazást ezután automatikusan hozzáadja a bérlőhöz, és a rendszer átirányítja a Priority Matrix-fiókjába.

    ![Prioritási mátrix OIDc beleegyezett](media/priority-matrix-provisioning-tutorial/consent.png)

## <a name="configure-automatic-user-provisioning-to-priority-matrix"></a>Automatikus felhasználó-kiépítés beállítása a prioritási mátrixba 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein az Azure AD-ben felhasználói és/vagy csoport-hozzárendelések alapján a prioritási mátrixban lévő felhasználók és/vagy csoportok létrehozásához, frissítéséhez és letiltásához.

> [!NOTE]
> Ha többet szeretne megtudni a prioritási mátrix SCIM-végpontról, tekintse meg a [felhasználók kiépítési és prioritási mátrixát](https://appfluence.com/help/article/user-provisioning/).

### <a name="to-configure-automatic-user-provisioning-for-priority-matrix-in-azure-ad"></a>Az automatikus felhasználó-kiépítés beállítása a prioritási mátrixhoz az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **prioritási mátrix** elemet.

    ![A prioritási mátrix hivatkozása az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a kiépítési mód legördülő listájáról az automatikus lehetőséggel.](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja `https://sync.appfluence.com/scim/v2/` meg a **bérlői URL-címet**. Adja meg a beolvasott és a korábban mentett értéket a **titkos jogkivonat** prioritási mátrixában. Kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad csatlakozni tudjanak a prioritási mátrixhoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Priority Matrix-fiók rendelkezik rendszergazdai jogosultságokkal, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba** esetén.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása prioritási mátrixba** lehetőséget.

    ![Prioritási mátrix felhasználói leképezései](media/priority-matrix-provisioning-tutorial/usermappings.png)

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az attribútum- **hozzárendelési** szakaszban található prioritási mátrixba. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a frissítési műveletek prioritási mátrixában szereplő felhasználói fiókokkal egyeznek meg. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Prioritási mátrix felhasználói attribútumai](media/priority-matrix-provisioning-tutorial/userattributes.png)

10. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

11. Ha engedélyezni szeretné az Azure AD kiépítési szolgáltatást a prioritási mátrixhoz, módosítsa a **kiépítési állapotot** **a következőre** a **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

12. Adja meg a prioritási mátrixba kiépíteni kívánt felhasználókat és/vagy csoportokat a **Settings (beállítások** ) szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Átadási hatókör](common/provisioning-scope.png)

13. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a prioritási mátrixban végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)


