---
title: 'Oktatóanyag: Soloinsight-CloudGate egyszeri bejelentkezés beállítása az automatikus felhasználó-kiépítés Azure Active Directoryval | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat az egyszeri bejelentkezés Soloinsight-CloudGate.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: Zhchia
ms.openlocfilehash: 5c42102c0704d7212411d6b86f4210ad8ecd885c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96347497"
---
# <a name="tutorial-configure-soloinsight-cloudgate-sso-for-automatic-user-provisioning"></a>Oktatóanyag: Soloinsight-CloudGate SSO konfigurálása automatikus felhasználó-kiépítés esetén

Ennek az oktatóanyagnak a célja, hogy bemutassa a Soloinsight-CloudGate SSO és Azure Active Directory (Azure AD) szolgáltatásban elvégzendő lépéseket az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikus kiosztása és kiépítése Soloinsight-CloudGate SSO-ra.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Azure AD-bérlő
* [Egy Soloinsight-CloudGate SSO-bérlő](https://www.soloinsight.com/)
* Felhasználói fiók Soloinsight-CloudGate egyszeri bejelentkezés rendszergazdai engedélyekkel.

## <a name="assigning-users-to-soloinsight-cloudgate-sso"></a>Felhasználók kiosztása Soloinsight-CloudGate SSO-hoz

Azure Active Directory a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD-beli felhasználók és/vagy csoportok hozzáférhessenek Soloinsight-CloudGate egyszeri bejelentkezéshez. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti az egyszeri bejelentkezés Soloinsight-CloudGateéhez az alábbi utasításokat követve:
* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-soloinsight-cloudgate-sso"></a>Fontos Tippek a felhasználók Soloinsight-CloudGate egyszeri bejelentkezéshez való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve Soloinsight-CloudGate SSO-hoz a felhasználó automatikus kiépítési konfigurációjának teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor egy felhasználót Soloinsight-CloudGate egyszeri bejelentkezéshez rendel hozzá, ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelési párbeszédpanelen. Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="set-up-soloinsight-cloudgate-sso-for-provisioning"></a>Soloinsight-CloudGate egyszeri bejelentkezés beállítása az üzembe helyezéshez

1. Jelentkezzen be a [Soloinsight-CLOUDGATE SSO felügyeleti konzolra](https://soloinsight.sigateway.com/login). Navigáljon az **adminisztráció > rendszerbeállítások elemre**.

    ![Egyszeri bejelentkezési felügyeleti konzol Soloinsight-CloudGate](media/soloinsight-cloudgate-sso-provisioning-tutorial/admin.png)

2.  Válassza az **általános** lehetőséget.

    ![SSO Soloinsight-CloudGate SCIM hozzáadása](media/soloinsight-cloudgate-sso-provisioning-tutorial/config.png)

3.  Görgessen le a lap végéig a **bérlői URL-cím** és a **titkos jogkivonat** lekéréséhez. Másolja a **titkos jogkivonatot**. Ez az érték jelenik meg a titkos jogkivonat mezőben a Soloinsight-CloudGate SSO-alkalmazás létesítés lapján a Azure Portalban.

    ![Egyszeri bejelentkezés Soloinsight-CloudGate jogkivonat létrehozása](media/soloinsight-cloudgate-sso-provisioning-tutorial/token.png)

## <a name="add-soloinsight-cloudgate-sso-from-the-gallery"></a>Soloinsight-CloudGate SSO hozzáadása a katalógusból

Az Azure AD-vel való automatikus felhasználó-kiépítés előtt Soloinsight-CloudGate egyszeri bejelentkezés konfigurálása előtt hozzá kell adnia Soloinsight-CloudGate SSO-t az Azure AD Application Gallery-ből a felügyelt SaaS-alkalmazások listájához.

**Ha Soloinsight-CloudGate SSO-t szeretne hozzáadni az Azure AD-alkalmazás-katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory** lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a következőt: **Soloinsight-CLOUDGATE SSO**, válassza a **SOLOINSIGHT-CloudGate SSO** elemet az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![SSO Soloinsight-CloudGate az eredmények listájában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-soloinsight-cloudgate-sso"></a>Automatikus felhasználó-kiépítés beállítása Soloinsight-CloudGate egyszeri bejelentkezéshez 

Ez a szakasz végigvezeti az Azure AD-létesítési szolgáltatás konfigurálásának lépésein, amelyekkel felhasználókat és/vagy csoportokat hozhat létre, frissíthet és tilt le Soloinsight-CloudGate SSO-ban az Azure AD-ben a felhasználók és/vagy csoportok hozzárendelései alapján.

> [!TIP]
> Azt is megteheti, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést Soloinsight-CloudGate egyszeri bejelentkezéshez, a [Soloinsight-CLOUDGATE SSO egyszeri bejelentkezés oktatóanyagában](./soloinsight-cloudgate-sso-tutorial.md)megadott utasításokat követve. Az egyszeri bejelentkezést az automatikus felhasználó-kiépítés függetlenül lehet konfigurálni, bár ez a két funkció

### <a name="to-configure-automatic-user-provisioning-for-soloinsight-cloudgate-sso-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása Soloinsight-CloudGate egyszeri bejelentkezéshez az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza az **Soloinsight-CLOUDGATE SSO** elemet.

    ![Az Soloinsight-CloudGate SSO hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a kiépítési mód legördülő listájáról az automatikus lehetőséggel.](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja `https://sigateway.com/scim/v2/sync/serviceproviderconfig` meg a **bérlői URL-címet**. Adja meg a **scim-hitelesítési jogkivonat** értékét a **titkos tokenben**. Kattintson a kapcsolat tesztelése elemre annak **ellenőrzéséhez** , hogy az Azure ad tud-e csatlakozni Soloinsight-CloudGate SSO-hoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Soloinsight-CloudGate SSO-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba** esetén.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **szinkronizálás Azure Active Directory a felhasználók Soloinsight-CloudGate egyszeri bejelentkezés** lehetőséget.

    ![Egyszeri bejelentkezéses felhasználói leképezések Soloinsight-CloudGate](media/soloinsight-cloudgate-sso-provisioning-tutorial/usermappings.png)

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat, hogy Soloinsight-CloudGate egyszeri bejelentkezést az **attribútum-hozzárendelési** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok az Soloinsight-CloudGate SSO-ban lévő felhasználói fiókoknak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![SSO felhasználói attribútumok Soloinsight-CloudGate](media/soloinsight-cloudgate-sso-provisioning-tutorial/userattributes.png)

10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása lehetőséget Soloinsight-CloudGate egyszeri bejelentkezéshez**.

    ![Egyszeri bejelentkezéses csoportos Soloinsight-CloudGate leképezése](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupmappings.png)

11. Tekintse át az Azure AD-ből szinkronizált csoportosítási attribútumokat az **attribútum-hozzárendelési** szakaszban Soloinsight-CloudGate egyszeri bejelentkezésre. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok az Soloinsight-CloudGate SSO-ban a frissítési műveletekhez tartozó csoportok egyeztetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Egyszeri bejelentkezési csoport attribútumainak Soloinsight-CloudGate](media/soloinsight-cloudgate-sso-provisioning-tutorial/groupattributes.png)

12. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

13. Ha engedélyezni szeretné az Azure AD-kiépítési szolgáltatást a Soloinsight-CloudGate egyszeri bejelentkezéshez, módosítsa a **kiépítési állapotot** **a következőre** a **Settings (beállítások** ) szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket szeretne kiépíteni az egyszeri bejelentkezés Soloinsight-CloudGateéhez, ha a **Settings (beállítások** ) szakaszban szeretné kiválasztani a kívánt értékeket a **hatókörben** .

    ![Átadási hatókör](common/provisioning-scope.png)

15. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a Soloinsight-CloudGate SSO-on végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)