---
title: 'Oktatóanyag: az Templafy OpenID Connect konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt úgy, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat az OpenID Connect Templafy.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.assetid: 8cbb387a-e3fb-4588-bb87-bf4f88144361
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: zhchia
ms.openlocfilehash: 6b1894d832c3a74916418aa06885c2917267f4b1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101652589"
---
# <a name="tutorial-configure-templafy-openid-connect-for-automatic-user-provisioning"></a>Oktatóanyag: az Templafy OpenID Connect konfigurálása a felhasználók automatikus kiépítési felállításához

Ennek az oktatóanyagnak a célja, hogy bemutassa az OpenID Connect és Azure Active Directory (Azure AD) Templafy végrehajtandó lépéseket az Azure AD konfigurálásához, hogy automatikusan kiépítse és kiépítse a felhasználókat és/vagy csoportokat az OpenID Connect Templafy.

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
3. Határozza meg, hogy az [Azure ad és a Templafy OpenID Connect között milyen adatleképezést kell leképezni](../app-provisioning/customize-application-attributes.md). 

## <a name="assigning-users-to-templafy-openid-connect"></a>Felhasználók Templafy OpenID Connecthez rendelése

Azure Active Directory a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD-beli felhasználók és/vagy csoportok hozzáférhessenek-e az Templafy OpenID Connecthez. Miután eldöntötte, hogy ezek a felhasználók és/vagy csoportok Templafy az OpenID Connecthez, kövesse az alábbi utasításokat:
* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-templafy-openid-connect"></a>Fontos Tippek a felhasználók Templafy OpenID Connecthez való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve az Templafy OpenID Connecthez az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. További felhasználók és/vagy csoportok később is hozzárendelhetők.

* Amikor egy felhasználót az OpenID Connect Templafy rendel hozzá, ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelési párbeszédpanelen. Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="step-2-configure-templafy-openid-connect-to-support-provisioning-with-azure-ad"></a>2. lépés Templafy OpenID-kapcsolat konfigurálása az Azure AD-vel való kiépítés támogatásához

Mielőtt konfigurálja a Templafy OpenID connectet az Azure AD-vel való automatikus felhasználói üzembe helyezéshez, engedélyeznie kell a SCIM üzembe helyezését az Templafy OpenID Connectben.

1. Jelentkezzen be a Templafy felügyeleti konzolra. Kattintson a **felügyelet** elemre.

    ![Templafy felügyeleti konzol](media/templafy-openid-connect-provisioning-tutorial/templafy-admin.png)

2. Kattintson a **hitelesítési módszerre**.

    ![Képernyőkép a Templafy felügyeleti szakaszról, az elnevezett hitelesítési módszer lehetőséggel.](media/templafy-openid-connect-provisioning-tutorial/templafy-auth.png)

3. Másolja a **scim API-kulcs** értékét. Ezt az értéket a Templafy OpenID Connect alkalmazás létesítés lapjának **titkos jogkivonat** mezőjébe írja a Azure Portal.

    ![Egy képernyőkép az S C I M A P I kulcsról.](media/templafy-openid-connect-provisioning-tutorial/templafy-token.png)

## <a name="step-3-add-templafy-openid-connect-from-the-gallery"></a>3. lépés Templafy OpenID-kapcsolat hozzáadása a katalógusból

Ha az Templafy OpenID connectet szeretné konfigurálni az Azure AD-vel való automatikus felhasználó-kiépítési művelethez, az Azure AD-alkalmazás-katalógusból hozzá kell adnia az Templafy OpenID connectet a felügyelt SaaS-alkalmazások listájához.

**Az Templafy OpenID Connect az Azure AD alkalmazás-katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory** lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **Templafy OpenID Connect** kifejezést, válassza az **Templafy OpenID Connect** elemet az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Templafy OpenID Connect az eredmények listájában](common/search-new-app.png)

## <a name="step-4-configure-automatic-user-provisioning-to-templafy-openid-connect"></a>4. lépés: Automatikus felhasználó-kiépítés konfigurálása az Templafy OpenID Connecthez 

Ez a szakasz végigvezeti az Azure AD-létesítési szolgáltatás konfigurálásának lépésein, amelyekkel a felhasználók és/vagy csoportok Templafy OpenID-kapcsolaton keresztül hozhatók létre, frissíthetők és letilthatók az Azure AD-ben, felhasználói és/vagy csoportos hozzárendelések alapján.

> [!TIP]
> Azt is megteheti, hogy engedélyezi az OpenID Connect-alapú egyszeri bejelentkezést a Templafy-hez, az [Templafy egyszeri bejelentkezés oktatóanyagában](templafy-tutorial.md)szereplő utasításokat követve. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, bár ez a két funkció egymáshoz tartozik.

### <a name="to-configure-automatic-user-provisioning-for-templafy-openid-connect-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása az Templafy OpenID Connecthez az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza az **Templafy OpenID Connect** elemet.

    ![Az Templafy OpenID Connect hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a kiépítési mód legördülő listájáról az automatikus lehetőséggel.](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja `https://scim.templafy.com/scim` meg a **bérlői URL-címet**. Adja meg a **scim API-kulcs** értékét, amely korábban a **titkos jogkivonatban** lett lekérve. Kattintson a **kapcsolat tesztelése** lehetőségre, hogy az Azure ad képes legyen csatlakozni a Templafy. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Templafy-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba** esetén.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **szinkronizálás Azure Active Directory a felhasználók Templafy OpenID Connect** elemet.

    ![Templafy OpenID Connect felhasználói leképezések](media/templafy-openid-connect-provisioning-tutorial/user-mapping.png)

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az Templafy OpenID Connect ( **attribútumok leképezése** ) szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Templafy OpenID connectben a frissítési műveletekhez használt felhasználói fiókokkal egyeznek meg. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

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

    ![Templafy OpenID Connect Group-leképezések](media/templafy-openid-connect-provisioning-tutorial/group-mapping.png)

11. Tekintse át az Azure AD-ből szinkronizált Templafy az OpenID Connectben az **attribútum-hozzárendelés** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Templafy OpenID Connect for Update műveletekhez tartozó csoportok egyeztetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

      |Attribútum|Típus|Szűréshez támogatott|
      |---|---|---|
      |displayName|Sztring|&check;|
      |tagok|Referencia|
      |externalId|Sztring|      

12. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

13. Ha engedélyezni szeretné az Azure AD-kiépítési szolgáltatást a Templafy OpenID Connect szolgáltatáshoz, módosítsa a **kiépítési állapotot** **a következőre** a **Settings (beállítások** ) szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket az OpenID-kapcsolat Templafy kíván kiépíteni, ehhez válassza ki a kívánt értékeket a **hatókörben** a **Beállítások** szakaszban.

    ![Átadási hatókör](common/provisioning-scope.png)

15. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

    Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a Templafy OpenID connectben végrehajtott összes műveletet ismertetik.

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