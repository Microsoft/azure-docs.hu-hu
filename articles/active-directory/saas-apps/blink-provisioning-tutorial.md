---
title: 'Oktatóanyag: a Blink konfigurálása automatikus felhasználó-kiépítés Azure Active Directorysal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt úgy, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: e67c3157f1d354fe79b2e4bb4b6e822aaf3e7506
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98731348"
---
# <a name="tutorial-configure-blink-for-automatic-user-provisioning"></a>Oktatóanyag: a Blink konfigurálása automatikus felhasználó-kiépítés esetén

Ennek az oktatóanyagnak a célja, hogy bemutassa a Blink és Azure Active Directory (Azure AD) által végrehajtandó lépéseket az Azure AD konfigurálásához, hogy automatikusan kiépítse és kiépítse a felhasználókat a Villogáshoz.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Azure AD-bérlő
* [Egy Blink-bérlő](https://joinblink.com/pricing)
* Egy felhasználói fiók a Blink-ben rendszergazdai engedélyekkel.

## <a name="assigning-users-to-blink"></a>Felhasználók kiosztása a Blink-hoz

Azure Active Directory a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak azok a felhasználók és/vagy csoporttagok szinkronizálhatók, akik az Azure AD-alkalmazáshoz vannak rendelve.

Az automatikus felhasználó-kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD-beli felhasználók és/vagy csoporttagok hogyan férhetnek hozzá a Blink szolgáltatáshoz. Miután eldöntötte, az alábbi utasításokat követve rendelheti hozzá ezeket a felhasználókat és/vagy csoportokat a Villogáshoz:
* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-blink"></a>Fontos Tippek a felhasználók a Villogáshoz való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen a Blink elemhez rendelve az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Ha a felhasználó a Villogáshoz van rendelve, a hozzárendelés párbeszédpanelen ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="setup-blink-for-provisioning"></a>A telepítő villog a kiépítés során

1. A SCIM-token igényléséhez jelentkezzen be a [támogatási esetre](https://support.joinblink.com) vagy az e-mailek **Blink-támogatására** support@joinblink.com .

2.  Másolja a **scim hitelesítési tokent**. Ez az érték a Azure Portalban a Blink-alkalmazás üzembe helyezés lapjának titkos jogkivonat mezőjében lesz megadva.

## <a name="add-blink-from-the-gallery"></a>Blink hozzáadása a katalógusból

Mielőtt a Blink-t konfigurálja az Azure AD-vel való automatikus felhasználói üzembe helyezéshez, fel kell vennie a Blink-et az Azure AD Application Gallery-ből a felügyelt SaaS-alkalmazások listájára.

**A következő lépésekkel adhatja hozzá a Blink-et az Azure AD-alkalmazás-katalógusból:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory** lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Blink** kifejezést, válassza az eredmények panelen a **Pislogás** elemet, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Pislogás az eredmények listájában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-blink"></a>Automatikus felhasználó-kiépítés beállítása a Blink-ra 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy csoportos hozzárendelések alapján hozza létre, frissítse és tiltsa le a felhasználók villogását.

> [!TIP]
> Azt is megteheti, hogy az SAML-alapú egyszeri bejelentkezést is engedélyezi a Blink-hez, a [Blink egyszeri bejelentkezés oktatóanyagában](./blink-tutorial.md)szereplő utasításokat követve. Az egyszeri bejelentkezést az automatikus felhasználó-kiépítés függetlenül lehet konfigurálni, bár ez a két funkció

### <a name="to-configure-automatic-user-provisioning-for-blink-in-azure-ad"></a>A felhasználók automatikus üzembe helyezésének beállítása a Blink-hez az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Pislogás** elemet.

    ![A Blink hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a kiépítési mód legördülő listájáról az automatikus lehetőséggel.](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja `https://api.joinblink.com/scim` meg a **bérlői URL-címet**. Adja meg a **scim-hitelesítési jogkivonat** értékét a **titkos tokenben**. Kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad képes legyen a Blink-hez kapcsolódni. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Blink-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba** esetén.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **szinkronizálás Azure Active Directory a felhasználók Pislogás** lehetőséget.

    ![Blink felhasználói leképezések](media/blink-provisioning-tutorial/User_mappings.png)

9. Tekintse át az Azure AD-ból szinkronizált felhasználói attribútumokat az **attribútumok leképezése** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok az Update műveletekhez a Blink-ben használt felhasználói fiókokkal egyeznek meg. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   |Attribútum|Típus|Szűréshez támogatott|
   |---|---|---|
   |userName (Felhasználónév)|Sztring|&check;|
   |active|Logikai|
   |cím|Sztring|
   |emails[type eq "work"].value|Sztring|
   |name.givenName|Sztring|
   |name.familyName|Sztring|
   |phoneNumbers[type eq "work"].value|Sztring|
   |phoneNumbers[type eq "mobile"].value|Sztring|
   |externalId|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: részleg|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: employeeNumber|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: User: Manager|Referencia|
   |urn: IETF: params: scim: sémák: bővítmény: Blink: 2.0: felhasználó: vállalat|Sztring|
   urn: IETF: params: scim: sémák: bővítmény: Blink: 2.0: User: Description|Sztring|
   urn: IETF: params: scim: sémák: bővítmény: Blink: 2.0: felhasználó: hely|Sztring|

10. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

11. Ha engedélyezni szeretné az Azure AD-kiépítési szolgáltatást a Villogáshoz, módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

12. Adja meg azokat a felhasználókat, akik számára a villogást szeretné kiépíteni, majd válassza ki a kívánt értékeket a **hatókör** területen a **Beállítások** szakaszban.

    ![Átadási hatókör](common/provisioning-scope.png)

15. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakaszban a **hatókörben** definiált összes felhasználó kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti az üzembe helyezési tevékenység jelentésre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a Blink-ben végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Az átadás konfigurálása után a következő erőforrásokkal monitorozhatja az üzemelő példányt:

* Az [átadási naplókkal](../reports-monitoring/concept-provisioning-logs.md) határozhatja meg, hogy mely felhasználók átadása sikeres, és melyeké sikertelen.
* A [folyamatjelzőn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) láthatja az átadási ciklus állapotát és azt, hogy mennyi hiányzik még a befejeződéséhez.
* Ha úgy tűnik, hogy az átadási konfiguráció állapota nem megfelelő, az alkalmazás karanténba kerül. A karanténállapotokról [itt](../app-provisioning/application-provisioning-quarantine-status.md) találhat további információt.  


## <a name="change-log"></a>Változási napló

* 01/14/2021 – az egyéni bővítmény attribútumainak **vállalata**, **leírása** és **helye** hozzá lett adva.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)