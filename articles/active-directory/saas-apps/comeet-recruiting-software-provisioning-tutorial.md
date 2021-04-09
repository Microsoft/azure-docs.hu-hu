---
title: 'Oktatóanyag: az automatikus felhasználó-kiépítés és a Azure Active Directory összegyűjtésének konfigurálása Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a szoftverek toborzásával.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: 288d1e6cec8ddcf7d4afe5a35f28a022c2a7be10
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96006712"
---
# <a name="tutorial-configure-comeet-recruiting-software-for-automatic-user-provisioning"></a>Oktatóanyag: a felhasználók automatikus kiépítés céljából történő toborzásának konfigurálása

A jelen oktatóanyag célja annak bemutatása, hogy milyen lépéseket kell végrehajtani a szoftver-és Azure Active Directory (Azure AD) bevonásával az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikus kiosztása és kiépítése a szoftverek toborzásával összhangban történjen.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Azure AD-bérlő
* [A szoftveres bérlők toborzása](https://www.comeet.co/)
* Egy felhasználói fiók, amely megfelel a szoftver rendszergazdai engedélyekkel való toborzásának.

## <a name="add-comeet-recruiting-software-from-the-gallery"></a>A gyűjteményhez tartozó szoftverek felvételének hozzáadása

Az Azure AD-vel való automatikus felhasználó-kiépítési szoftver bevezetésének konfigurálása előtt hozzá kell adnia az Azure AD-alkalmazás-katalógusból a felügyelt SaaS-alkalmazások listájához való felvételt.

**A következő lépésekkel adhatja hozzá a szoftvereket az Azure AD Application Galleryből:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory** lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a következőt: **munkatársak toborzása szoftver**, válassza a **munkatársak toborzása** az eredmények panelen lehetőséget, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![A szoftverek toborzása az eredmények listájában](common/search-new-app.png)

## <a name="assigning-users-to-comeet-recruiting-software"></a>Felhasználók kiosztása a szoftverek toborzásával való megfeleléshez

Azure Active Directory a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

Az automatikus felhasználó-kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD mely felhasználói és/vagy csoportjai férhetnek hozzá a szoftverek toborzásához. Miután eldöntötte, a felhasználók és/vagy csoportok hozzárendelésével összefoglalhatja a szoftvereket az itt leírt utasítások követésével:

* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-comeet-recruiting-software"></a>Fontos Tippek a felhasználók a szoftverek toborzásához való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen társítva a szoftverek toborzásához az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor egy felhasználóhoz rendeli a szoftver toborzását, a hozzárendelés párbeszédpanelen ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="configuring-automatic-user-provisioning-to-comeet-recruiting-software"></a>A felhasználók automatikus üzembe helyezésének beállítása a szoftverek toborzásának megtartására 

Ez a szakasz végigvezeti az Azure AD-létesítési szolgáltatás konfigurálásának lépésein a felhasználók és/vagy csoportok az Azure AD-ben történő felhasználói és/vagy csoportos hozzárendelések alapján történő létrehozásához, frissítéséhez és letiltásához.

> [!TIP]
> Azt is megteheti, hogy az SAML-alapú egyszeri bejelentkezést is lehetővé teszi a szoftveres toborzáshoz való megfelelés érdekében, a [közös szoftveres egyszeri bejelentkezés oktatóanyaga](comeetrecruitingsoftware-tutorial.md)című témakörben ismertetett utasításokat követve. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, bár ez a két funkció egymáshoz tartozik.

### <a name="to-configure-automatic-user-provisioning-for-comeet-recruiting-software-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a szoftver Azure AD-ben való felvételéhez:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **szoftver felvétele** lehetőséget.

    ![Az alkalmazások listáján szereplő szoftver-toborzási hivatkozás](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a kiépítési mód legördülő listájáról az automatikus lehetőséggel.](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a **bérlői URL-címét** és **titkos jogkivonatát** , amely a 6. lépésben leírtak szerint tartalmazza a szoftver fiókjának felvételét.

6. A [szoftveres felügyeleti konzol](https://app.comeet.co/)összeadásával kapcsolatban navigáljon a  **> beállítások > a hitelesítés > Microsoft Azure** elemre, és másolja a **vállalati érték titkos tokenjét** az Azure ad **titkos jogkivonat** mezőjébe.

    ![A szoftverek kiépítési feltoborzásának összetartása](./media/comeet-recruiting-software-provisioning-tutorial/secret-token-1.png)

7. Az 5. lépésben megjelenő mezők kitöltése után kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad csatlakozni tudjanak a szoftverek toborzásához. Ha a kapcsolat nem sikerül, győződjön meg arról, hogy a szoftver fiókjának toborzása rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Jogkivonat](common/provisioning-testconnection-token.png)

8. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba** esetén.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

9. Kattintson a **Mentés** gombra.

10. A **leképezések** szakaszban válassza az **Azure Active Directory a felhasználók szinkronizálása** lehetőséget.

    ![A szoftveres felhasználói leképezések toborzása](media/comeet-recruiting-software-provisioning-tutorial/user-mappings.png)

11. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat, hogy azok megfeleljenek a szoftvereknek az **attribútum-hozzárendelési** szakaszban való felvételéhez. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok az adott felhasználói fiókoknak felelnek meg a frissítési műveletekhez való toborzásban. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![A szoftveres csoport attribútumainak toborzása](media/comeet-recruiting-software-provisioning-tutorial/user-mapping-attributes.png)

12. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

13. Ha engedélyezni szeretné az Azure AD-kiépítési szolgáltatás számára a szoftverek toborzását, módosítsa a **kiépítési állapotot** a  **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg azokat a felhasználókat és/vagy csoportokat, akiket szeretne kiépíteni a szoftver toborzásának megkezdéséhez. Ehhez válassza a **Settings (beállítások** ) szakaszban a kívánt értékeket a **hatókörben** .

    ![Átadási hatókör](common/provisioning-scope.png)

15. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által végzett, a szoftverekkel való összekapcsolásra vonatkozó összes műveletet ismertetik.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="connector-limitations"></a>Összekötő korlátozásai

* A munkatársak toborzása jelenleg nem támogatja a csoportokat.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)

