---
title: 'Oktatóanyag: Flock konfigurálása automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a nyájba.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/30/2019
ms.author: Zhchia
ms.openlocfilehash: 01c3f6429d2a5c8443ac128d763033dc8c53cbc7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94359408"
---
# <a name="tutorial-configure-flock-for-automatic-user-provisioning"></a>Oktatóanyag: Flock konfigurálása automatikus felhasználó-kiépítési szolgáltatáshoz

Ennek az oktatóanyagnak a célja, hogy bemutassa a Flock és Azure Active Directory (Azure AD) által elvégzendő lépéseket az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikusan kiépítsék és kiépítsék a nyájat.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő.
* [Egy Flock-bérlő](https://flock.com/pricing/)
* Olyan felhasználói fiók, amely rendszergazdai engedélyekkel rendelkezik a nyájban.

## <a name="assigning-users-to-flock"></a>Felhasználók társítása a nyájhoz 

Azure Active Directory a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

Az automatikus felhasználó-kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD-beli felhasználók és/vagy csoportok hozzáférhessenek-e a nyájhoz. A döntés után ezeket a felhasználókat és/vagy csoportokat a következő utasítások követésével rendelheti hozzá a Flockhoz:
* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-flock"></a>Fontos Tippek a felhasználók a nyájhoz való hozzárendeléséhez 

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a nyájhoz az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor egy felhasználót a Flock szolgáltatáshoz rendel, ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelés párbeszédpanelen. Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="setup-flock--for-provisioning"></a>Állomány beállítása a kiépítés számára

Az Azure AD-vel való automatikus felhasználó-kiépítés előtt engedélyeznie kell a SCIM-létesítést az Flock-ben.

1. Jelentkezzen be a [nyájba](https://web.flock.com/?). Kattintson a **Beállítások ikonra**  >  **a csapat kezelése** elemre.

    :::image type="content" source="media/flock-provisioning-tutorial/icon.png" alt-text="Képernyőfelvétel a Flock webhelyéről. A beállítások ikon ki van emelve, és a helyi menü látható. Ebben a menüben a csapat kezelése kiemelve." border="false":::

2. Válassza **az Auth és a kiépítés** lehetőséget.

    :::image type="content" source="media/Flock-provisioning-tutorial/auth.png" alt-text="Képernyőkép a Flock webhelyén lévő menüről. Az Auth és a kiépítési elemek ki vannak emelve." border="false":::

3. Másolja az **API-tokent**. Ezeket az értékeket a rendszer a Flock-alkalmazás létesítés lapjának **titkos jogkivonat** mezőjébe írja be a Azure Portal.

    :::image type="content" source="media/Flock-provisioning-tutorial/provisioning.png" alt-text="Képernyőfelvétel a Flock webhelyén található kiépítés lapról. A P I token alatt egy érték van kiemelve. A jogkivonat mellett egy másolási jogkivonat gomb." border="false":::


## <a name="add-flock--from-the-gallery"></a>Flock hozzáadása a gyűjteményből

Az Azure AD-vel való automatikus felhasználó-kiépítés esetén a Flock az Azure AD-alkalmazás-katalógusból a felügyelt SaaS-alkalmazások listájára van beállítva.

**Az Azure AD-alkalmazás-katalógusból származó Flock hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory** lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Flock** kifejezést, válassza a **Flock** elemet az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Flock az eredmények listájában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-flock"></a>Az automatikus felhasználó-kiépítés beállítása a Flock-re  

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein az Azure AD-ben felhasználói és/vagy csoport-hozzárendelések alapján a felhasználók és/vagy csoportok létrehozásához, frissítéséhez és letiltásához.

> [!TIP]
> Azt is megteheti, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a Flock számára, a [Flock egyszeri bejelentkezés oktatóanyagában](Flock-tutorial.md)szereplő utasításokat követve. Az egyszeri bejelentkezést az automatikus felhasználó-kiépítés függetlenül lehet konfigurálni, bár ez a két funkció

### <a name="to-configure-automatic-user-provisioning-for-flock--in-azure-ad"></a>Automatikus felhasználó-kiépítés beállítása az Azure AD-beli Flock számára:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Flock** elemet.

    ![A Flock hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a kiépítési mód legördülő listájáról az automatikus lehetőséggel.](common/provisioning-automatic.png)

5. A rendszergazdai hitelesítő adatok szakaszban adja meg a `https://api.flock-staging.com/v2/scim` **bérlői URL-címben** és a **titkos jogkivonatban** korábban beolvasott és **API-tokenek** értékeit. Kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad képes legyen csatlakozni a nyájhoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Flock-fiók rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba** esetén.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a nyájhoz** lehetőséget.

    ![Flock-felhasználók leképezései](media/flock-provisioning-tutorial/usermapping.png)

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a-beli felhasználói fiókoknak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Flock felhasználói attribútumai](media/flock-provisioning-tutorial/userattribute.png)

11. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

12. Ha engedélyezni szeretné az Azure AD-kiépítési szolgáltatást a Flock számára, módosítsa a **kiépítési állapotot** **a következőre** a **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

13. Adja meg azokat a felhasználókat és/vagy csoportokat, akiket ki szeretne építeni a nyájba. Ehhez válassza ki a kívánt értékeket a **hatókörben** a **Beállítások** szakaszban.

    ![Átadási hatókör](common/provisioning-scope.png)

14. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások. További információ arról, hogy mennyi ideig tart a felhasználók és/vagy csoportok kiépítése, hogy [mennyi ideig tart a felhasználók kiépítése](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

A **jelenlegi állapot** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenység jelentésére mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a Flock-ben végrehajtott összes műveletet ismertetik. További információ: [a felhasználó kiépítési állapotának ellenõrzése](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Az Azure AD-létesítési naplók olvasásához lásd: [jelentéskészítés az automatikus felhasználói fiók kiépítés](../app-provisioning/check-status-user-account-provisioning.md)során.



## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)