---
title: 'Oktatóanyag: a Zscaler konfigurálása az automatikus felhasználó-kiépítés Azure Active Directorysal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt úgy, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a Zscaler.
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
ms.openlocfilehash: f6725045064b74079e00ca5bbe1d560f3b19f3ff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97937127"
---
# <a name="tutorial-configure-zscaler-one-for-automatic-user-provisioning"></a>Oktatóanyag: a Zscaler konfigurálása az automatikus felhasználó-kiépítés számára

Ez az oktatóanyag bemutatja, hogyan végezheti el a Zscaler az egyik és Azure Active Directory (Azure AD) az Azure AD konfigurálását a felhasználók és csoportok automatikus kiépítéséhez és kiépítéséhez a Zscaler.

> [!NOTE]
> Ez az oktatóanyag egy, az Azure AD-felhasználó kiépítési szolgáltatására épülő összekötőt ismertet. További információ a szolgáltatás működéséről, működéséről és gyakori kérdéseiről: a felhasználók kiépítésének [automatizálása és a szolgáltatott szoftveres (SaaS) alkalmazások kiépítése Azure Active Directory használatával](../app-provisioning/user-provisioning.md).


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy rendelkezik a következővel:

* Egy Azure AD-bérlő.
* Egy Zscaler egy bérlő.
* Egy rendszergazdai engedélyekkel rendelkező felhasználói fiók a Zscaler.

> [!NOTE]
> Az Azure AD kiépítési integrációja a Zscaler egy SCIM API-ra támaszkodik. Ez az API Zscaler egy fejlesztő számára a vállalati csomaggal rendelkező fiókok számára.

## <a name="add-zscaler-one-from-the-azure-marketplace"></a>Zscaler hozzáadása az Azure piactéren

Mielőtt konfigurálja az Zscaler-t az Azure AD-vel való automatikus felhasználói üzembe helyezéshez, adja hozzá a Zscaler egyet az Azure piactéren a felügyelt SaaS-alkalmazások listájához.

Az alábbi lépéseket követve adhat hozzá Zscaler egyet a piactéren.

1. A [Azure Portal](https://portal.azure.com)a bal oldali navigációs ablaktáblán válassza a **Azure Active Directory** lehetőséget.

    ![A Azure Active Directory ikon](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához válassza a párbeszédpanel tetején található **új alkalmazás** lehetőséget.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Zscaler egyet** , és válassza ki az **Zscaler egyet** az eredmény panelen. Az alkalmazás hozzáadásához válassza a **Hozzáadás** lehetőséget.

    ![Zscaler egyet az eredmények listájában](common/search-new-app.png)

## <a name="assign-users-to-zscaler-one"></a>Felhasználók kiosztása egy Zscaler

Azure Active Directory a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók vagy csoportok lesznek szinkronizálva.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD-beli felhasználók vagy csoportok számára szükséges-e a Zscaler-hozzáférés. Ha ezeket a felhasználókat vagy csoportokat egy Zscaler szeretné hozzárendelni, kövesse a [felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)című témakör utasításait.

### <a name="important-tips-for-assigning-users-to-zscaler-one"></a>Fontos Tippek a felhasználók egy Zscaler való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználót rendeljen hozzá egy Zscaler az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat vagy csoportokat is hozzárendelhet.

* Ha egy felhasználót egy Zscaler rendel hozzá, akkor a hozzárendelés párbeszédpanelen válasszon egy érvényes alkalmazásspecifikus szerepkört (ha van ilyen). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="configure-automatic-user-provisioning-to-zscaler-one"></a>Automatikus felhasználó-kiépítés beállítása Zscaler

Ez a szakasz végigvezeti az Azure AD kiépítési szolgáltatás konfigurálásának lépésein. Ezzel a beállítással felhasználókat vagy csoportokat hozhat létre, frissíthet és letilthat a Zscaler-ben az Azure AD-ben.

> [!TIP]
> A Zscaler SAML-alapú egyszeri bejelentkezés is engedélyezhető. Kövesse a [Zscaler egy egyszeri bejelentkezési oktatóanyagban](zscaler-One-tutorial.md)található utasításokat. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, bár ez a két funkció kiegészíti egymást.

> [!NOTE]
> Ha a felhasználók és csoportok kiosztása vagy kiosztása megtörtént, javasoljuk, hogy rendszeres időközönként indítsa újra az üzembe helyezést, hogy a csoporttagság megfelelően frissüljenek. Az újraindítás után a szolgáltatás kikényszeríti a szolgáltatást az összes csoport újraértékelésére és a tagságok frissítésére.  

### <a name="configure-automatic-user-provisioning-for-zscaler-one-in-azure-ad"></a>Automatikus felhasználó-kiépítés konfigurálása az Azure AD-ben az egyik Zscaler

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza ki a **vállalati alkalmazások**  >  **minden alkalmazás**  >  **Zscaler egyet**.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza ki a **Zscaler egyet**.

    ![Az Zscaler egy hivatkozást az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Zscaler egy kiépítés](./media/zscaler-one-provisioning-tutorial/provisioning-tab.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Zscaler egy kiépítési mód](./media/zscaler-one-provisioning-tutorial/provisioning-credentials.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a **bérlői URL-címet** és a **titkos tokent** tartalmazó mezőket a Zscaler egy fiókkal, a 6. lépésben leírtak szerint.

6. A bérlői URL-cím és a titkos jogkivonat beszerzéséhez nyissa meg a **felügyeleti**  >  **hitelesítési beállításokat** a Zscaler egy portál felhasználói felületén. A **Hitelesítés típusa** területen válassza az **SAML** lehetőséget.

    ![Zscaler egy hitelesítési beállítást](./media/zscaler-one-provisioning-tutorial/secret-token-1.png)

    a. Válassza az **SAML konfigurálása** lehetőséget az **SAML** -beállítások konfigurálása lehetőség megnyitásához.

    ![Zscaler egy SAML konfigurálása](./media/zscaler-one-provisioning-tutorial/secret-token-2.png)

    b. Válassza az **SCIM-Based kiépítés engedélyezése** lehetőséget az **alap URL-cím** és a **tulajdonosi jogkivonat** beállításainak beszerzéséhez. Ezután mentse a beállításokat. Másolja az **alap URL-címet** a **bérlő URL-címére** a Azure Portal. Másolja a **tulajdonosi jogkivonat** -beállítást a Azure Portal **titkos jogkivonatára** .

7. Miután kitöltötte az 5. lépésben látható mezőket, válassza a **kapcsolat tesztelése** lehetőséget, és győződjön meg róla, hogy az Azure ad képes csatlakozni a Zscaler. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Zscaler egy fiók rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Zscaler egy tesztelési kapcsolatban](./media/zscaler-one-provisioning-tutorial/test-connection.png)

8. Az **értesítési e-mail** mezőbe írja be annak a személynek vagy csoportnak az e-mail-címét, aki a kiépítési hibákra vonatkozó értesítéseket szeretné kapni. Jelölje be az **e-mail-értesítés küldése hiba** esetén jelölőnégyzetet.

    ![Zscaler egy értesítő e-mailt](./media/zscaler-one-provisioning-tutorial/notification.png)

9. Kattintson a **Mentés** gombra.

10. A **leképezések** szakaszban válassza a **szinkronizálás Azure Active Directory felhasználók Zscaler egyet**.

    ![Zscaler egy felhasználói szinkronizálással](./media/zscaler-one-provisioning-tutorial/user-mappings.png)

11. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútumok leképezése** szakasz Zscaler. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Zscaler lévő felhasználói fiókok egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások mentéséhez válassza a **Mentés** lehetőséget.

    ![Zscaler egy megfelelő felhasználói attribútumot](./media/zscaler-one-provisioning-tutorial/user-attribute-mappings.png)

12. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása lehetőséget, hogy Zscaler egyet**.

    ![Zscaler egy csoport szinkronizálása](./media/zscaler-one-provisioning-tutorial/group-mappings.png)

13. Tekintse át az Azure AD-ból szinkronizált Zscaler az attribútumok **leképezése** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Zscaler egyikében a frissítési műveletekhez használatosak. A módosítások mentéséhez válassza a **Mentés** lehetőséget.

    ![Zscaler egy egyező csoport attribútumai](./media/zscaler-one-provisioning-tutorial/group-attribute-mappings.png)

14. A hatóköri szűrők konfigurálásához kövesse a hatókör- [szűrő oktatóanyagának](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)utasításait.

15. Ha engedélyezni szeretné az Azure AD-kiépítési szolgáltatást az Zscaler, a **Beállítások** szakaszban állítsa be a **kiépítési állapotot** **a** következőre:.

    ![Zscaler egy kiépítési állapottal](./media/zscaler-one-provisioning-tutorial/provisioning-status.png)

16. Adja meg a Zscaler kiépíteni kívánt felhasználókat vagy csoportokat. A **Beállítások** szakaszban válassza ki a **hatókörben** használni kívánt értékeket.

    ![Zscaler egy hatókörrel](./media/zscaler-one-provisioning-tutorial/scoping.png)

17. Ha készen áll a létesítésre, válassza a **Mentés** lehetőséget.

    ![Zscaler egy mentést](./media/zscaler-one-provisioning-tutorial/save-provisioning.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások. Körülbelül 40 percenként történnek, amíg az Azure AD-kiépítési szolgáltatás fut. 

A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenység jelentésének hivatkozásait. A jelentés az Azure AD-kiépítési szolgáltatás által az Zscaler-on végrehajtott összes műveletet ismerteti.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-one-provisioning-tutorial/tutorial-general-03.png