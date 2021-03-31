---
title: 'Oktatóanyag: a tabló online beállítása az automatikus felhasználó-kiépítés Azure Active Directory | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt a felhasználói fiókok automatikus üzembe helyezéséhez és megszüntetéséhez a tabló Online szolgáltatásban.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: a42790e079985b003776b381c74f837b0ba619b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94359204"
---
# <a name="tutorial-configure-tableau-online-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználói üzembe helyezéshez a tabló online beállítása

Ez az oktatóanyag bemutatja, hogyan végezheti el az Azure AD-t a tabló online és Azure Active Directory (Azure AD) szolgáltatásban a felhasználók és csoportok automatikus üzembe helyezéséhez és megszüntetéséhez a tabló online-ban.

> [!NOTE]
> Ez az oktatóanyag egy, az Azure AD-felhasználó kiépítési szolgáltatására épülő összekötőt ismertet. További információ a szolgáltatás működéséről, működéséről és gyakori kérdéseiről: a felhasználók kiépítésének [automatizálása és a szolgáltatott szoftveres (SaaS) alkalmazások kiépítése Azure Active Directory használatával](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy rendelkezik a következővel:

*   Egy Azure AD-bérlő.
*   Egy [tabló online bérlő](https://www.tableau.com/).
*   Rendszergazdai jogosultságokkal rendelkező felhasználói fiók a tabló online-ban.

> [!NOTE]
> Az Azure AD kiépítési integrációja a [tabló online REST API](https://onlinehelp.tableau.com/current/api/rest_api/en-us/help.htm)-ra támaszkodik. Ez az API a tabló online fejlesztői számára érhető el.

## <a name="add-tableau-online-from-the-azure-marketplace"></a>Tabló online hozzáadása az Azure Marketplace-ről
Mielőtt beállítja a tabló online-t az Azure AD-vel való automatikus felhasználói üzembe helyezéshez, adja hozzá a tablót az Azure piactéren a felügyelt SaaS-alkalmazások listájához.

Az alábbi lépéseket követve adhat hozzá online tablót a piactéren.

1. A [Azure Portal](https://portal.azure.com)a bal oldali navigációs ablaktáblán válassza a **Azure Active Directory** lehetőséget.

    ![A Azure Active Directory ikon](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához válassza a párbeszédpanel tetején található **új alkalmazás** lehetőséget.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **tabló online** kifejezést, majd válassza a **tabló online** lehetőséget az eredmények panelen. Az alkalmazás hozzáadásához válassza a **Hozzáadás** lehetőséget.

    ![Tabló online az eredmények listájában](common/search-new-app.png)

## <a name="assign-users-to-tableau-online"></a>Felhasználók kiosztása a tabló online-hoz

Azure Active Directory a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók vagy csoportok lesznek szinkronizálva.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD mely felhasználói vagy csoportjai férhetnek hozzá a tabló online-hoz. Ha ezeket a felhasználókat vagy csoportokat szeretné hozzárendelni a tabló online-hoz, kövesse a [felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)című témakör utasításait.

### <a name="important-tips-for-assigning-users-to-tableau-online"></a>Fontos Tippek a felhasználók online Tablóhoz rendeléséhez

*   Azt javasoljuk, hogy egyetlen Azure AD-felhasználót rendeljen a tabló online-hoz az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat vagy csoportokat is hozzárendelhet.

*   Ha egy felhasználót a tabló online szolgáltatáshoz rendel hozzá, akkor a hozzárendelés párbeszédpanelen válasszon egy érvényes alkalmazásspecifikus szerepkört, ha van ilyen. Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="configure-automatic-user-provisioning-to-tableau-online"></a>Automatikus felhasználó-kiépítés beállítása a tabló online-ba

Ez a szakasz végigvezeti az Azure AD kiépítési szolgáltatás konfigurálásának lépésein. Ezzel a beállítással felhasználókat vagy csoportokat hozhat létre, frissíthet és letilthat a tabló online-ban az Azure AD-ben lévő felhasználói vagy csoportos hozzárendelések alapján.

> [!TIP]
> Engedélyezheti az SAML-alapú egyszeri bejelentkezést is a tabló online-hoz. Kövesse a [tabló online egyszeri bejelentkezés oktatóanyagának](tableauonline-tutorial.md)utasításait. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, bár ez a két funkció kiegészíti egymást.

### <a name="configure-automatic-user-provisioning-for-tableau-online-in-azure-ad"></a>Automatikus felhasználó-kiépítés beállítása a Tablóhoz az Azure AD-ben

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások**  >  **minden alkalmazás**  >  **tabló online** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **tabló online** lehetőséget.

    ![A tabló online hivatkozása az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Tabló online kiépítés](./media/tableau-online-provisioning-tutorial/ProvisioningTab.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Tabló online kiépítési mód](./media/tableau-online-provisioning-tutorial/ProvisioningCredentials.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a tabló online-fiókjának tartományát, rendszergazdai felhasználónevét, rendszergazdai jelszavát, valamint a tartalom URL-címét:

   * A **tartomány** mezőbe írja be az altartományt a 6. lépés alapján.

   * A **rendszergazda felhasználóneve** mezőben adja meg a tabló online bérlője rendszergazdai fiókjának felhasználónevét. Például: admin@contoso.com.

   * A **rendszergazdai jelszó** mezőbe írja be a rendszergazdai felhasználónévnek megfelelő rendszergazdai fiók jelszavát.

   * A **tartalom URL-címe** mezőben adja meg az altartományt a 6. lépés alapján.

6. Miután bejelentkezett a felügyeleti fiókjába a tabló Online szolgáltatásban, a **tartomány** és a **tartalom URL-címéhez** tartozó értékeket a rendszergazda lap URL-címéből érheti el.

    * A tabló online-fiókjához tartozó **tartomány** az URL-cím ezen részéből is átmásolható:

        ![Tabló online tartomány](./media/tableau-online-provisioning-tutorial/DomainUrlPart.png)

    * A tabló online-fiókjához tartozó **tartalom URL-címe** átmásolható ebből a szakaszból. A fiók beállítása során megadott érték. Ebben a példában az érték a "contoso":

        ![Tabló online tartalom URL-címe](./media/tableau-online-provisioning-tutorial/ContentUrlPart.png)

        > [!NOTE]
        > Előfordulhat, hogy a **tartománya** különbözik az itt láthatók közül.

7. Miután kitöltötte az 5. lépésben látható mezőket, válassza a **kapcsolat tesztelése** lehetőséget, és győződjön meg arról, hogy az Azure ad csatlakozni tud a tabló online-hoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a tabló online-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Tabló online tesztelési kapcsolat](./media/tableau-online-provisioning-tutorial/TestConnection.png)

8. Az **értesítési e-mail** mezőbe írja be annak a személynek vagy csoportnak az e-mail-címét, aki a kiépítési hibákra vonatkozó értesítéseket szeretné kapni. Jelölje be az **e-mail-értesítés küldése hiba** esetén jelölőnégyzetet.

    ![Tabló online értesítő E-mail](./media/tableau-online-provisioning-tutorial/EmailNotification.png)

9. Kattintson a **Mentés** gombra.

10. A **leképezések** szakaszban válassza a **szinkronizálás Azure Active Directory felhasználók a tablóhoz** lehetőséget.

    ![A tabló online felhasználói szinkronizálása](./media/tableau-online-provisioning-tutorial/UserMappings.png)

11. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az attribútum- **hozzárendelések** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a tabló online felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások mentéséhez válassza a **Mentés** lehetőséget.

    ![Tabló online – egyező felhasználói attribútumok](./media/tableau-online-provisioning-tutorial/attribute.png)

12. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása a tablóhoz** lehetőséget.

    ![Tabló online csoport szinkronizálása](./media/tableau-online-provisioning-tutorial/GroupMappings.png)

13. Tekintse át az Azure AD-ből szinkronizált csoportosítási attribútumokat az **attribútum-hozzárendelések** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a tabló online felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások mentéséhez válassza a **Mentés** lehetőséget.

    ![Tabló online egyeztetési csoport attribútumai](./media/tableau-online-provisioning-tutorial/GroupAttributeMapping.png)

14. A hatóköri szűrők konfigurálásához kövesse a hatókör- [szűrő oktatóanyagának](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)utasításait.

15. Ha engedélyezni szeretné az Azure AD-kiépítési szolgáltatást a tabló online számára, a **Beállítások** szakaszban módosítsa a **kiépítési állapot** beállítást **a** következőre:.

    ![Tabló online kiépítési állapot](./media/tableau-online-provisioning-tutorial/ProvisioningStatus.png)

16. Adja meg azokat a felhasználókat és csoportokat, amelyeket a tabló online-hoz szeretne kiépíteni. A **Beállítások** szakaszban válassza ki a **hatókörben** használni kívánt értékeket.

    ![Tabló online hatóköre](./media/tableau-online-provisioning-tutorial/ScopeSync.png)

17. Ha készen áll a létesítésre, válassza a **Mentés** lehetőséget.

    ![Tabló online mentés](./media/tableau-online-provisioning-tutorial/SaveProvisioning.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások. Körülbelül 40 percenként történnek, amíg az Azure AD-kiépítési szolgáltatás fut. 

A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenység jelentésének hivatkozásait. A jelentés az Azure AD-kiépítési szolgáltatás által a tabló online platformon végrehajtott összes műveletet ismerteti.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="change-log"></a>Változási napló
* 09/30/2020 – a "authSetting" attribútum támogatása a felhasználók számára.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/tableau-online-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/tableau-online-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/tableau-online-provisioning-tutorial/tutorial_general_03.png