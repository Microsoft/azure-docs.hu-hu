---
title: 'Oktatóanyag: OfficeSpace szoftver konfigurálása automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a OfficeSpace szoftverekhez.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/02/2019
ms.author: Zhchia
ms.openlocfilehash: fc67d649e3a7cd094eb2c3d633244077abcab308
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94359923"
---
# <a name="tutorial-configure-officespace-software-for-automatic-user-provisioning"></a>Oktatóanyag: OfficeSpace-szoftver konfigurálása automatikus felhasználó-kiépítési szolgáltatáshoz

Az oktatóanyag célja annak bemutatása, hogy milyen lépéseket kell végrehajtani a OfficeSpace szoftverben és Azure Active Directory (Azure AD) az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikusan kiépítsék és kiépítsék a szoftvereket a OfficeSpace.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Azure AD-bérlő
* Egy [OfficeSpace szoftver bérlője](https://www.officespacesoftware.com/)
* Rendszergazdai jogosultságokkal rendelkező felhasználói fiók a OfficeSpace szoftverben.

## <a name="assigning-users-to-officespace-software"></a>Felhasználók OfficeSpace szoftverhez rendelése

Azure Active Directory a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

Az automatikus felhasználó-kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy mely felhasználókra és/vagy csoportokra van szükség az Azure AD-ben a OfficeSpace szoftver eléréséhez. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti OfficeSpace szoftverekhez az alábbi utasításokat követve:
* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-officespace-software"></a>Fontos Tippek a felhasználók OfficeSpace szoftverhez való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a OfficeSpace szoftverhez az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor felhasználót rendel a szoftver OfficeSpace, ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelési párbeszédpanelen. Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="set-up-officespace-software-for-provisioning"></a>OfficeSpace szoftver beállítása a kiépítés számára

1. Jelentkezzen be a [OfficeSpace Software felügyeleti konzolján](https://support.officespacesoftware.com/hc). Navigáljon a **beállítások > összekötők** elemre.

    ![OfficeSpace szoftveres felügyeleti konzol](media/officespace-software-provisioning-tutorial/settings.png)

2.  Navigáljon a **címtár-szinkronizálás > scim**.

    ![OfficeSpace szoftver SCIM hozzáadása](media/officespace-software-provisioning-tutorial/scim.png)

3.  Másolja a **scim hitelesítési tokent**. Ez az érték a OfficeSpace-alkalmazás üzembe helyezés lapjának titkos jogkivonat mezőjében lesz megadva a Azure Portal.

    ![OfficeSpace szoftver-létrehozási token](media/officespace-software-provisioning-tutorial/token.png)

## <a name="add-officespace-software-from-the-gallery"></a>OfficeSpace-szoftver hozzáadása a katalógusból

Mielőtt OfficeSpace szoftvert szeretne konfigurálni az Azure AD-vel való automatikus felhasználói üzembe helyezéshez, hozzá kell adnia a OfficeSpace szoftvert az Azure AD-alkalmazás-katalógusból a felügyelt SaaS-alkalmazások listájára.

**A következő lépések végrehajtásával adhat hozzá OfficeSpace szoftvert az Azure AD-alkalmazás-katalógusból:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory** lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **OfficeSpace szoftvert**, válassza ki a **OfficeSpace szoftvert** az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![OfficeSpace szoftver az eredmények listájában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-officespace-software"></a>Automatikus felhasználó-kiépítés konfigurálása a OfficeSpace szoftverhez 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy OfficeSpace alapuló felhasználókat és/vagy csoportokat lehessen létrehozni, frissíteni és letiltani a szoftverben.

> [!TIP]
> Azt is megteheti, hogy az SAML-alapú egyszeri bejelentkezést is engedélyezi a OfficeSpace szoftverhez a [OfficeSpace szoftveres egyszeri bejelentkezés oktatóanyagában](./officespace-tutorial.md)ismertetett utasításokat követve. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, bár ez a két szolgáltatás kiegészíti egymást.

### <a name="to-configure-automatic-user-provisioning-for-officespace-software-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a OfficeSpace szoftverhez az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza ki a **OfficeSpace szoftvert**.

    ![Az OfficeSpace szoftver hivatkozása az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a kiépítési mód legördülő listájáról az automatikus lehetőséggel.](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a bemeneti `https://<subdomain>.officespacesoftware.com/api/scim/v2/` URL-formátumot a **bérlői URL-címben**. Például: `https://contoso.officespacesoftware.com/api/scim/v2/`. Adja meg a **scim-hitelesítési jogkivonat** értékét a **titkos tokenben**. Kattintson a kapcsolat tesztelése elemre annak **ellenőrzéséhez** , hogy az Azure ad tud-e csatlakozni a OfficeSpace szoftverhez. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a OfficeSpace-fiók rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Az **Értesítés e-mailben** mezőben adja meg annak a személynek vagy csoportnak az e-mail-címét, aki az átadással kapcsolatos hibaüzeneteket kapja, és jelölje be az **E-mail-értesítés küldése hiba esetén** jelölőnégyzetet.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **szinkronizálás Azure Active Directory a felhasználók OfficeSpace a szoftvereket**.

    ![OfficeSpace szoftveres felhasználói leképezések](media/officespace-software-provisioning-tutorial/usermappings.png)

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az attribútum- **hozzárendelési** szakaszban található OfficeSpace szoftverhez. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a OfficeSpace szoftver felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![OfficeSpace szoftver felhasználói attribútumai](media/officespace-software-provisioning-tutorial/userattributes.png)

11. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

12. Az Azure AD-kiépítési szolgáltatás OfficeSpace szoftverhez való engedélyezéséhez módosítsa a **kiépítési állapotot** a  **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

13. Adja meg azokat a felhasználókat és/vagy csoportokat, akik számára a OfficeSpace szoftvert szeretné kiépíteni, ehhez válassza ki a kívánt értékeket a **hatókör** területen a **Beállítások** szakaszban.

    ![Átadási hatókör](common/provisioning-scope.png)

14. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a OfficeSpace szoftvereken végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)