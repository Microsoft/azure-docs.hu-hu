---
title: 'Oktatóanyag: dinamikus jel konfigurálása a felhasználók automatikus üzembe helyezéséhez Azure Active Directorysal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a dinamikus jellé.
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
ms.openlocfilehash: 263a67fd8fba2c336d1ed4d91475386a8ae175dd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96005792"
---
# <a name="tutorial-configure-dynamic-signal-for-automatic-user-provisioning"></a>Oktatóanyag: dinamikus jel beállítása a felhasználók automatikus üzembe helyezéséhez

Ennek az oktatóanyagnak a célja, hogy bemutassa a dinamikus és Azure Active Directory (Azure AD) végrehajtandó lépéseit, hogy az Azure AD konfigurálja a felhasználókat és/vagy csoportokat a dinamikus jellé való automatikus kiépítéséhez és kiépítéséhez.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Azure AD-bérlő
* [Dinamikus jel bérlője](https://dynamicsignal.com/)
* Rendszergazdai jogosultságokkal rendelkező dinamikus jellel rendelkező felhasználói fiók.

## <a name="add-dynamic-signal-from-the-gallery"></a>Dinamikus jel hozzáadása a katalógusból

Az Azure AD-vel való automatikus felhasználó-kiépítés előtt dinamikus jelet kell hozzáadnia az Azure AD-alkalmazás-katalógusból a felügyelt SaaS-alkalmazások listájára.

**Ha dinamikus jelet szeretne hozzáadni az Azure AD Application Galleryből, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory** lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **dinamikus jelet**, válassza ki a **dinamikus** jelet az eredmények panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Dinamikus jel az eredmények listájában](common/search-new-app.png)

## <a name="assigning-users-to-dynamic-signal"></a>Felhasználók kiosztása dinamikus jellé

Azure Active Directory a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

Az automatikus felhasználó-kiépítés konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD-beli felhasználóknak és/vagy csoportoknak hogyan kell elérniük a dinamikus jeleket. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti dinamikus jellé a következő utasítások követésével:

* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dynamic-signal"></a>Fontos Tippek a felhasználók dinamikus jellé való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a dinamikus jellé a felhasználó automatikus kiépítési konfigurációjának teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Ha egy felhasználót dinamikus jellé rendel, ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelési párbeszédpanelen. Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="configuring-automatic-user-provisioning-to-dynamic-signal"></a>Automatikus felhasználó-kiépítés beállítása dinamikus jellé 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy csoportos hozzárendelések alapján hozzon létre, frissítsen és tiltsa le a dinamikus jeleket használó felhasználókat és/vagy csoportokat.

> [!TIP]
> Azt is megteheti, hogy az SAML-alapú egyszeri bejelentkezést is engedélyezte a dinamikus jel számára, a [dinamikus jel egyszeri bejelentkezés oktatóanyagában](dynamicsignal-tutorial.md)szereplő utasításokat követve. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, bár ez a két funkció egymáshoz tartozik.

### <a name="to-configure-automatic-user-provisioning-for-dynamic-signal-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása dinamikus jel esetén az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **dinamikus jel** lehetőséget.

    ![A dinamikus jel hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a kiépítési mód legördülő listájáról az automatikus lehetőséggel.](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a dinamikus jel fiókjának **bérlői URL-címét** és **titkos jogkivonatát** a 6. lépésben leírtak szerint.

6. A dinamikus jel felügyeleti konzolon navigáljon a **rendszergazda > Advanced > API** elemre.

    :::image type="content" source="./media/dynamic-signal-provisioning-tutorial/secret-token-1.png" alt-text="Képernyőkép a dinamikus jel felügyeleti konzolról. A speciális elem a felügyeleti menüben van kiemelve. A Speciális menü is látható, egy P kiemelve." border="false":::

    Másolja a **scim API URL-címét** a **bérlői URL**-címre. Kattintson az **új jogkivonat létrehozása** lehetőségre egy **tulajdonosi jogkivonat** létrehozásához és az érték **titkos jogkivonatba** való másolásához.

    :::image type="content" source="./media/dynamic-signal-provisioning-tutorial/secret-token-2.png" alt-text="Képernyőfelvétel a tokenek lapról: S C I M A P I U R L, új jogkivonat létrehozása és a tulajdonosi jogkivonat kiemelése, valamint egy helyőrző a tulajdonosi jogkivonat mezőben." border="false":::

7. Az 5. lépésben megjelenő mezők kitöltése után kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad csatlakozni tudjanak a dinamikus jellé. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a dinamikus jel fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

8. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba** esetén.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

9. Kattintson a **Mentés** gombra.

10. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása dinamikus jellé** lehetőséget.

    ![Dinamikus jelek felhasználói leképezései](media/dynamic-signal-provisioning-tutorial/user-mappings.png)

11. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az attribútum- **hozzárendelési** szakaszban található dinamikus jellé. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a dinamikus jel által a frissítési műveletekhez használt felhasználói fiókokkal egyeznek meg. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![Dinamikus jel felhasználói attribútumai](media/dynamic-signal-provisioning-tutorial/user-mapping-attributes.png)

12. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

13. Ha engedélyezni szeretné az Azure AD-kiépítési szolgáltatást a dinamikus jel számára, módosítsa a **kiépítési állapotot** **a következőre** a **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket szeretne a dinamikus jellé kiépíteni a **Beállítások** szakasz **hatókörében** lévő kívánt értékek kiválasztásával.

    ![Átadási hatókör](common/provisioning-scope.png)

15. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenységre mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a dinamikus jeleken végrehajtott összes műveletet ismertetik.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos további információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="connector-limitations"></a>Összekötő korlátozásai

* A dinamikus jel nem támogatja az állandó felhasználók törlését az Azure AD-ből. Ha véglegesen szeretné törölni a felhasználót a dinamikus jelek között, a műveletet a dinamikus jel felügyeleti konzoljának felhasználói felületén kell elvégezni. 
* A dinamikus jel jelenleg nem támogatja a csoportokat.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)

