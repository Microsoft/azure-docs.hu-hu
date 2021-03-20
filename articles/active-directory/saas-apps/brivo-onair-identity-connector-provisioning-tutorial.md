---
title: 'Oktatóanyag: a Brivo onair-identitás-összekötő konfigurálása a felhasználók automatikus üzembe helyezéséhez a Azure Active Directory segítségével | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat az Brivo onair.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: Zhchia
ms.openlocfilehash: 4f230a70ae87c7c0ec9db347e7cf975c0252bb18
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95998089"
---
# <a name="tutorial-configure-brivo-onair-identity-connector-for-automatic-user-provisioning"></a>Oktatóanyag: a Brivo onair Identity Connector konfigurálása a felhasználók automatikus kiépítési feladataihoz

Az oktatóanyag célja annak bemutatása, hogy milyen lépéseket kell végrehajtani a Brivo onair Identity Connector és a Azure Active Directory (Azure AD) számára az Azure AD konfigurálásához, hogy automatikusan kiépítse és kiépítse a felhasználókat és/vagy csoportokat a Brivo onair Identity Connectorhoz.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Azure AD-bérlő
* [Brivo onair-összekötő bérlője](https://www.brivo.com/lp/quote)
* Egy felhasználói fiók a Brivo onair Identity Connector vezető rendszergazdai engedélyekkel.

## <a name="assigning-users-to-brivo-onair-identity-connector"></a>Felhasználók kiosztása a Brivo onair Identity Connectorhoz

Azure Active Directory a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjai férhetnek hozzá a Brivo onair Identity Connectorhoz. Miután eldöntötte, az alábbi utasításokat követve rendelheti hozzá ezeket a felhasználókat és/vagy csoportokat a onair Identity Connector Brivo:
* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-brivo-onair-identity-connector"></a>Fontos Tippek a felhasználók Brivo-onair identitás-összekötőhöz való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve az Brivo onair Identity Connectorhoz a felhasználó automatikus kiépítési konfigurációjának teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor felhasználót rendel a Brivo onair Identity Connectorhoz, ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelési párbeszédpanelen. Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="setup-brivo-onair-identity-connector-for-provisioning"></a>A Brivo onair Identity Connector beállítása a kiépítés számára

1. Jelentkezzen be a [Brivo onair Identity Connector felügyeleti konzolra](https://acs.brivo.com/login/). Válassza a **fiók > Fiókbeállítások lehetőséget**.

   ![Brivo onair Identity Connector felügyeleti konzol](media/brivo-onair-identity-connector-provisioning-tutorial/admin.png)

2. Kattintson az **Azure ad** fülre. Az **Azure ad** -Részletek lapon adja meg újra a legfelső szintű rendszergazdai fiók jelszavát. Kattintson a **Submit (elküldés**) gombra.

   ![Brivo onair Identity Connector – Azure](media/brivo-onair-identity-connector-provisioning-tutorial/azuread.png)

3. Kattintson a **jogkivonat másolása** gombra, és mentse a **titkos jogkivonatot**. Ez az érték a Brivo onair Identity Connector alkalmazás üzembe helyezés lapjának titkos jogkivonat mezőjében lesz megadva a Azure Portal.

   ![Brivo onair-azonosító tokenje](media/brivo-onair-identity-connector-provisioning-tutorial/token.png)

## <a name="add-brivo-onair-identity-connector-from-the-gallery"></a>Brivo onair-identitás összekötő hozzáadása a katalógusból

Mielőtt Brivo a onair Identity Connectort az Azure AD-vel való automatikus felhasználói üzembe helyezéshez, hozzá kell adnia a Brivo onair Identity Connectort az Azure AD Application Gallery-ből a felügyelt SaaS-alkalmazások listájához.

**Az Azure AD Brivo az alábbi lépéseket követve veheti fel a onair Identity Connectort:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory** lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **Brivo onair Identity Connector** kifejezést, válassza az **Brivo onair Identity Connector** elemet az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Brivo onair-identitás összekötője az eredmények listájában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-brivo-onair-identity-connector"></a>Automatikus felhasználó-kiépítés konfigurálása a Brivo onair Identity Connectorhoz 

Ez a szakasz végigvezeti az Azure AD-létesítési szolgáltatás konfigurálásának lépésein, amelyekkel felhasználókat és/vagy csoportokat hozhat létre, frissíthet és letilthat a Brivo onair Identity Connectorban az Azure AD-ben a felhasználók és/vagy csoportok hozzárendelései alapján.

### <a name="to-configure-automatic-user-provisioning-for-brivo-onair-identity-connector-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása az Brivo onair Identity Connectorhoz az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Brivo onair Identity Connector** elemet.

    ![Az Brivo onair Identity Connector hivatkozása az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a kiépítési mód legördülő listájáról az automatikus lehetőséggel.](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja `https://scim.brivo.com/ActiveDirectory/v2/` meg a **bérlői URL-címet**. Adja meg a **scim-hitelesítési jogkivonat** értékét a **titkos tokenben**. Kattintson a kapcsolat tesztelése elemre annak **ellenőrzéséhez** , hogy az Azure ad tud-e csatlakozni a Brivo onair Identity connectorhoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Brivo onair Identity Connector-fiók rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba** esetén.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **szinkronizálás Azure Active Directory a felhasználók Brivo a onair Identity Connector** lehetőséget.

    ![Brivo onair identitás-összekötő felhasználói hozzárendelései](media/brivo-onair-identity-connector-provisioning-tutorial/user-mappings.png )

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban található Brivo onair Identity Connector használatával Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Brivo onair Identity Connector felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Brivo onair identitás-összekötő felhasználói attribútumai](media/brivo-onair-identity-connector-provisioning-tutorial/user-attributes.png)

10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása lehetőséget, hogy Brivo a onair Identity Connectort**.

    ![Brivo-onair identitás-összekötő csoportjának leképezése](media/brivo-onair-identity-connector-provisioning-tutorial/group-mappings.png)

11. Tekintse át az Azure AD-ből szinkronizált Brivo az **attribútum-hozzárendelési** szakaszban található onair-identitás összekötőn. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Brivo onair Identity Connector csoportjának a frissítési műveletekhez való egyeztetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Brivo onair identitás-összekötő csoport attribútumai](media/brivo-onair-identity-connector-provisioning-tutorial/group-attributes.png)

12. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

13. Ha engedélyezni szeretné az Azure AD-kiépítési szolgáltatást a Brivo onair Identity Connectorhoz, módosítsa a **kiépítési állapotot** **a következőre** a **Settings (beállítások** ) szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg azokat a felhasználókat és/vagy csoportokat, akik számára a Brivo onair Identity Connectort szeretné kiépíteni a **Beállítások** szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Átadási hatókör](common/provisioning-scope.png)

15. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a Brivo onair Identity Connectoron végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)

