---
title: 'Oktatóanyag: a Miro konfigurálása automatikus felhasználó-kiépítés Azure Active Directorysal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt a felhasználói fiókok a Miro szolgáltatásba való automatikus kiépítéséhez és üzembe helyezéséhez.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: Zhchia
ms.openlocfilehash: d3dee76ada7a9ed48813928937f8f8da8be4add1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96348041"
---
# <a name="tutorial-configure-miro-for-automatic-user-provisioning"></a>Oktatóanyag: a Miro konfigurálása a felhasználók automatikus üzembe helyezéséhez

Ennek az oktatóanyagnak a célja, hogy bemutassa a Miro és Azure Active Directory (Azure AD) által végrehajtandó lépéseket az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikusan kiépítsék és kiépítsék a Mirot.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Azure AD-bérlő
* [Egy Miro-bérlő](https://miro.com/pricing/)
* Egy felhasználói fiók a Miroban rendszergazdai engedélyekkel.

## <a name="assigning-users-to-miro"></a>Felhasználók társítása a Mirohoz

Azure Active Directory a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználóinak és/vagy csoportjai férhetnek hozzá a Mirohoz. Miután eldöntötte, a felhasználókat és/vagy csoportokat hozzárendelheti a Mirohoz az alábbi utasításokat követve:
* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-miro"></a>Fontos Tippek a felhasználók a Mirohoz való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a Mirohoz az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor egy felhasználót a Mirohoz rendel, ki kell választania egy érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelés párbeszédpanelen. Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="set-up-miro-for-provisioning"></a>A Miro beállítása a kiépítés számára

1.  A szükséges **titkos jogkivonat** lekéréséhez forduljon a következő helyen: support@miro.com . Ez az érték a Azure Portalban található Miro-alkalmazás létesítés lapjának titkos jogkivonat mezőjében lesz megadva.

## <a name="add-miro-from-the-gallery"></a>Miro hozzáadása a katalógusból

Mielőtt a Mirot konfigurálja az Azure AD-vel való automatikus felhasználói üzembe helyezéshez, hozzá kell adnia a Mirot az Azure AD Application Gallery-ből a felügyelt SaaS-alkalmazások listájához.

**A Miro Azure AD-alkalmazás-katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory** lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Miro** kifejezést, válassza ki a **Miro** elemet az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Miro az eredmények listájában](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-miro"></a>A felhasználók automatikus üzembe helyezésének beállítása a Miroba 

Ez a szakasz végigvezeti az Azure AD-létesítési szolgáltatás konfigurálásának lépésein az Azure AD-ben felhasználói és/vagy csoportos hozzárendelések alapján a Miron lévő felhasználók és/vagy csoportok létrehozásához, frissítéséhez és letiltásához.

> [!TIP]
> Azt is megteheti, hogy engedélyezi az SAML-alapú egyszeri bejelentkezést a Miro számára, a [Miro egyszeri bejelentkezés oktatóanyagában](./miro-tutorial.md)szereplő utasításokat követve. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, bár ez a két funkció egymáshoz tartozik.

> [!NOTE]
> A Miro SCIM-végpontjának megismeréséhez tekintse meg [ezt a témakört](https://help.miro.com/hc/en-us/articles/360036777814).

### <a name="to-configure-automatic-user-provisioning-for-miro-in-azure-ad"></a>A Miro szolgáltatás automatikus kiépítési beállítása az Azure AD-ben

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Miro** elemet.

    ![A Miro hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a kiépítési mód legördülő listájáról az automatikus lehetőséggel.](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja `https://miro.com/api/v1/scim` meg a **bérlői URL-címet**. Adja meg a **scim-hitelesítési jogkivonat** értékét a **titkos tokenben**. Kattintson a kapcsolat tesztelése elemre annak **ellenőrzéséhez** , hogy az Azure ad tud-e csatlakozni a mirohöz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Miro-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba** esetén.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory-felhasználók szinkronizálása a Miro-** be lehetőséget.

    ![A Miro felhasználói leképezései](media/miro-provisioning-tutorial/usermappings.png)

9. Tekintse át az Azure AD-ből a Miroba szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a frissítés műveletekhez használhatók a Miro felhasználói fiókjainak megfelelően. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![A Miro felhasználói attribútumai](media/miro-provisioning-tutorial/userattributes.png)

10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása a miroba** lehetőséget.

    ![Miro csoport leképezései](media/miro-provisioning-tutorial/groupmappings.png)

11. Tekintse át az Azure AD-ből a Miroba szinkronizált csoportosítási attribútumokat az **attribútum-hozzárendelési** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a a Miro for Update műveletekhez tartozó csoportokkal egyeznek meg. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra. Szüntesse meg a **Létrehozás** és **Törlés** kijelölését a **cél objektum műveletei** alatt, mivel a Miro scim API nem támogatja a csoportok létrehozását és törlését.

    ![Miro-csoport attribútumai](media/miro-provisioning-tutorial/groupattributes.png)

12. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

13. A Miro Azure AD kiépítési szolgáltatásának engedélyezéséhez módosítsa a **kiépítési állapotot** a **Beállítások** szakaszban  .

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg a Miro számára kiépíteni kívánt felhasználókat és/vagy csoportokat a **Settings (beállítások** ) szakasz **hatókörében** .

    ![Átadási hatókör](common/provisioning-scope.png)

15. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a Miron végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="connector-limitations"></a>Összekötő korlátozásai

* A Miro SCIM-végpontja nem teszi lehetővé a csoportok **létrehozási** és **törlési** műveleteit. Csak a csoport **frissítési** műveletét támogatja.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)