---
title: 'Oktatóanyag: Templafy-egy SAML2 konfigurálása automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a Templafy egy SAML2.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.assetid: 8a966ef5-e364-435b-9e29-3caf27ffb498
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: zhchia
ms.openlocfilehash: 4cc89c1ad94ae738b25e4ea18d5fe687880969c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101648220"
---
# <a name="tutorial-configure-templafy-saml2-for-automatic-user-provisioning"></a>Oktatóanyag: a Templafy-egy SAML2 konfigurálása a felhasználók automatikus kiépítési felállításához

Ennek az oktatóanyagnak a célja, hogy bemutassa a Templafy egy SAML2 és Azure Active Directory (Azure AD) végrehajtandó lépéseit az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikus kiosztása és kiépítése a Templafy egy SAML2.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő.
* [Egy Templafy-bérlő](https://www.templafy.com/pricing/).
* Rendszergazdai jogosultságokkal rendelkező felhasználói fiók a Templafy-ben.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés Az átadás üzembe helyezésének megtervezése
1. Ismerje meg [az átadási szolgáltatás működését](../app-provisioning/user-provisioning.md).
2. Határozza meg, hogy ki lesz [az átadás hatókörében](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Határozza meg, hogy az [Azure ad és a TEMPLAFY egy saml2 milyen adatleképezési](../app-provisioning/customize-application-attributes.md)leképezéseket kíván. 

## <a name="assigning-users-to-templafy-saml2"></a>Felhasználók kiosztása Templafy egy SAML2

Azure Active Directory a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

Az automatikus felhasználó-kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy mely felhasználókra és/vagy csoportokra van szükség az Azure AD-ben a Templafy-egy SAML2 eléréséhez. Miután eldöntötte, hogy ezek a felhasználók és/vagy csoportok Templafy egy SAML2, az alábbi utasításokat követve rendelje hozzá ezeket a felhasználókat:
* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-templafy-saml2"></a>Fontos Tippek a felhasználók Templafy-egy SAML2 való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a Templafy-egy SAML2 az automatikus felhasználó-létesítési konfiguráció teszteléséhez. További felhasználók és/vagy csoportok később is hozzárendelhetők.

* Amikor felhasználót rendel a Templafy egy SAML2, ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelés párbeszédpanelen. Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="step-2-configure-templafy-saml2-to-support-provisioning-with-azure-ad"></a>2. lépés Templafy-egy SAML2 konfigurálása az Azure AD-vel való kiépítés támogatásához

Mielőtt Templafy a egy SAML2 az Azure AD-vel való automatikus felhasználói üzembe helyezéshez, engedélyeznie kell a SCIM-létesítést a Templafy-egy SAML2.

1. Jelentkezzen be a Templafy felügyeleti konzolra. Kattintson a **felügyelet** elemre.

    ![Templafy felügyeleti konzol](media/templafy-saml-2-provisioning-tutorial/templafy-admin.png)

2. Kattintson a **hitelesítési módszerre**.

    ![Képernyőkép a Templafy felügyeleti szakaszról, az elnevezett hitelesítési módszer lehetőséggel.](media/templafy-saml-2-provisioning-tutorial/templafy-auth.png)

3. Másolja a **scim API-kulcs** értékét. Ez az érték a Templafy egy SAML2-alkalmazás üzembe helyezés lapjának **titkos jogkivonat** mezőjében jelenik meg a Azure Portal.

    ![Egy képernyőkép az S C I M A P I kulcsról.](media/templafy-saml-2-provisioning-tutorial/templafy-token.png)

## <a name="step-3-add-templafy-saml2-from-the-gallery"></a>3. lépés Templafy-egy SAML2 hozzáadása a gyűjteményből

Az Azure AD-vel való automatikus Templafy egy SAML2 konfigurálásához hozzá kell adnia a Templafy-egy SAML2 az Azure AD Application Gallery-ből a felügyelt SaaS-alkalmazások listájához.

**Az Azure AD Templafy egy SAML2 hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory** lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **TEMPLAFY egy saml2**, válassza az **Templafy egy saml2** elemet az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Templafy egy SAML2 az eredmények listájában](common/search-new-app.png)

## <a name="step-4-configure-automatic-user-provisioning-to-templafy-saml2"></a>4. lépés: Automatikus felhasználó-kiépítés konfigurálása a Templafy egy SAML2 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy Templafy alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le a egy SAML2.

> [!TIP]
> Azt is megteheti, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a Templafy számára, az [Templafy egyszeri bejelentkezés oktatóanyagában](templafy-tutorial.md)megadott utasításokat követve. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, bár ez a két funkció egymáshoz tartozik.

### <a name="to-configure-automatic-user-provisioning-for-templafy-saml2-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a Templafy-egy SAML2 az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **TEMPLAFY egy saml2** elemet.

    ![Az Templafy egy SAML2 hivatkozása az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a kiépítési mód legördülő listájáról az automatikus lehetőséggel.](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja `https://scim.templafy.com/scim` meg a **bérlői URL-címet**. Adja meg a **scim API-kulcs** értékét, amely korábban a **titkos jogkivonatban** lett lekérve. Kattintson a **kapcsolat tesztelése** lehetőségre, hogy az Azure ad képes legyen csatlakozni a Templafy. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Templafy-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba** esetén.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **szinkronizálás Azure Active Directory a felhasználók Templafy egy saml2** lehetőséget.

    ![Templafy egy SAML2 felhasználói leképezései](media/templafy-saml-2-provisioning-tutorial/user-mapping.png)

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az attribútum- **hozzárendelések** szakasz Templafy egy saml2. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Templafy egy saml2 felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   |Attribútum|Típus|Szűréshez támogatott|
   |---|---|---|
   |userName (Felhasználónév)|Sztring|&check;|
   |active|Logikai|
   |displayName|Sztring|
   |cím|Sztring|
   |preferredLanguage|Sztring|
   |name.givenName|Sztring|
   |name.familyName|Sztring|
   |phoneNumbers[type eq "work"].value|Sztring|
   |phoneNumbers[type eq "mobile"].value|Sztring|
   |phoneNumbers [type EQ "fax"]. Value|Sztring|
   |externalId|Sztring|
   |címek [típus EQ "work"]. helység|Sztring|
   |címek [type EQ "work"]. irányítószám|Sztring|
   |címek [típus EQ "work"]. régió|Sztring|
   |címek [type EQ "work"]. streetAddress|Sztring|
   |címek [type EQ "work"]. Country|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: részleg|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: szervezet|Sztring|

10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása a Templafy** lehetőséget.

    ![Templafy egy SAML2 csoport leképezései](media/templafy-saml-2-provisioning-tutorial/group-mapping.png)

11. Tekintse át az Azure AD-ből szinkronizált Templafy egy SAML2 az **attribútum-hozzárendelések** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Templafy egy saml2 tartozó csoportok egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

      |Attribútum|Típus|Szűréshez támogatott|
      |---|---|---|
      |displayName|Sztring|&check;|
      |tagok|Referencia|
      |externalId|Sztring|      


12. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

13. Ha engedélyezni szeretné az Azure AD-kiépítési szolgáltatást a Templafy egy SAML2, módosítsa a **kiépítési állapotot** **a következőre** a **Settings (beállítások** ) szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket a Templafy egy SAML2 szeretne kiépíteni, ehhez válassza ki a kívánt értékeket a **hatókörben** a **Beállítások** szakaszban.

    ![Átadási hatókör](common/provisioning-scope.png)

15. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

    Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a Templafy egy saml2 végzett összes műveletet ismertetik.

## <a name="step-5-monitor-your-deployment"></a>5. lépés Az üzemelő példány figyelése
Az átadás konfigurálása után a következő erőforrásokkal monitorozhatja az üzemelő példányt:

* Az [átadási naplókkal](../reports-monitoring/concept-provisioning-logs.md) határozhatja meg, hogy mely felhasználók átadása sikeres, és melyeké sikertelen.
* A [folyamatjelzőn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) láthatja az átadási ciklus állapotát és azt, hogy mennyi hiányzik még a befejeződéséhez.
* Ha úgy tűnik, hogy az átadási konfiguráció állapota nem megfelelő, az alkalmazás karanténba kerül. A karanténállapotokról [itt](../app-provisioning/application-provisioning-quarantine-status.md) találhat további információt.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)