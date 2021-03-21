---
title: 'Oktatóanyag: a zendesk konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a zendesk felhasználói fiókjait.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.openlocfilehash: 620dd8fd586352ebeaf097a8f870a606f8e06c01
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94359714"
---
# <a name="tutorial-configure-zendesk-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés zendesk konfigurálása

Ez az oktatóanyag bemutatja, hogyan kell végrehajtani a zendesk és a Azure Active Directory (Azure AD) az Azure AD konfigurálását a felhasználók és csoportok automatikus kiépítéséhez és kiépítéséhez a zendesk.

> [!NOTE]
> Ez az oktatóanyag egy, az Azure AD-felhasználó kiépítési szolgáltatására épülő összekötőt ismertet. További információ a szolgáltatás működéséről, működéséről és gyakori kérdéseiről: a felhasználók kiépítésének [automatizálása és a szolgáltatott szoftveres (SaaS) alkalmazások kiépítése Azure Active Directory használatával](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy rendelkezik a következővel:

* Egy Azure AD-bérlő.
* Zendesk-bérlő a professzionális csomaggal, vagy jobb, ha van ilyen.
* Rendszergazdai jogosultságokkal rendelkező felhasználói fiók a zendesk-ben.

## <a name="add-zendesk-from-the-azure-marketplace"></a>Zendesk hozzáadása az Azure Marketplace-ről

Mielőtt konfigurálja a zendesk-t az Azure AD-vel való automatikus felhasználói üzembe helyezéshez, adja hozzá a zendesk-t az Azure piactéren a felügyelt SaaS-alkalmazások listájához.

Ha zendesk szeretne hozzáadni a piactéren, kövesse az alábbi lépéseket.

1. A [Azure Portal](https://portal.azure.com)a bal oldali navigációs ablaktáblán válassza a **Azure Active Directory** lehetőséget.

    ![A Azure Active Directory ikon](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához válassza a párbeszédpanel tetején található **új alkalmazás** lehetőséget.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **zendesk** kifejezést, és válassza az **zendesk** elemet az eredmény panelen. Az alkalmazás hozzáadásához válassza a **Hozzáadás** lehetőséget.

    ![Zendesk az eredmények listájában](common/search-new-app.png)

## <a name="assign-users-to-zendesk"></a>Felhasználók zendesk rendelése

Azure Active Directory a *hozzárendelések* nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók vagy csoportok lesznek szinkronizálva.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD mely felhasználói vagy csoportjai férhetnek hozzá a zendesk. A felhasználók vagy csoportok zendesk való hozzárendeléséhez kövesse a [felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)című témakör utasításait.

### <a name="important-tips-for-assigning-users-to-zendesk"></a>Fontos Tippek a felhasználók zendesk való hozzárendeléséhez

* Napjainkban a zendesk szerepkörei automatikusan és dinamikusan fel lesznek töltve a Azure Portal felhasználói felületén. Mielőtt zendesk-szerepköröket rendel a felhasználókhoz, győződjön meg arról, hogy a zendesk-bérlő legújabb szerepköreinek beolvasásához a zendesk a kezdeti szinkronizálást hajtja végre.

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználót rendeljen a zendesk-hez a kezdeti automatikus felhasználó-kiépítési konfiguráció teszteléséhez. A tesztek sikeressége után később további felhasználókat vagy csoportokat is hozzárendelhet.

* Amikor zendesk rendel hozzá egy felhasználóhoz, a hozzárendelés párbeszédpanelen válassza ki a megfelelő alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="configure-automatic-user-provisioning-to-zendesk"></a>Automatikus felhasználó-kiépítés beállítása a zendesk 

Ez a szakasz végigvezeti az Azure AD kiépítési szolgáltatás konfigurálásának lépésein. Ezzel a beállítással felhasználókat vagy csoportokat hozhat létre, frissíthet és letilthat a zendesk-ben az Azure AD-ben felhasználói vagy csoport-hozzárendelések alapján.

> [!TIP]
> A zendesk SAML-alapú egyszeri bejelentkezés is engedélyezhető. Kövesse az [zendesk egyszeri bejelentkezési oktatóanyag](zendesk-tutorial.md)utasításait. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, bár ez a két funkció kiegészíti egymást.

### <a name="configure-automatic-user-provisioning-for-zendesk-in-azure-ad"></a>Automatikus felhasználó-kiépítés konfigurálása a zendesk az Azure AD-ben

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások**  >  **minden alkalmazás**  >  **zendesk** lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **zendesk** lehetőséget.

    ![Az zendesk hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Zendesk kiépítés](./media/zendesk-provisioning-tutorial/ZenDesk16.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Zendesk-létesítési mód](./media/zendesk-provisioning-tutorial/ZenDesk1.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a zendesk-fiókja rendszergazdai felhasználónevét, titkos jogkivonatát és tartományát. Ilyen értékek például a következők:

   * A **rendszergazda felhasználóneve** mezőben adja meg a zendesk-bérlő rendszergazdai fiókjának felhasználónevét. Például: admin@contoso.com.

   * A **titkos jogkivonat** mezőben adja meg a titkos jogkivonatot a 6. lépésben leírtak szerint.

   * A **tartomány** mezőbe írja be a zendesk-bérlő altartományát. A bérlői URL-címét tartalmazó fiók esetében például az `https://my-tenant.zendesk.com` altartomány a **saját bérlő**.

6. A zendesk-fiók titkos jogkivonata a **felügyeleti**  >  **API**-  >  **beállításokban** található. Győződjön meg arról, hogy a **jogkivonat-hozzáférés** **engedélyezve** értékre van állítva.

    ![Zendesk-rendszergazdai beállítások](./media/zendesk-provisioning-tutorial/ZenDesk4.png)

    ![Zendesk titkos token](./media/zendesk-provisioning-tutorial/ZenDesk2.png)

7. Miután kitöltötte az 5. lépésben látható mezőket, válassza a **kapcsolat tesztelése** lehetőséget, és győződjön meg arról, hogy az Azure ad képes csatlakozni a zendesk. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a zendesk-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Zendesk-tesztelési kapcsolatok](./media/zendesk-provisioning-tutorial/ZenDesk19.png)

8. Az **értesítési e-mail** mezőbe írja be annak a személynek vagy csoportnak az e-mail-címét, aki a kiépítési hibákra vonatkozó értesítéseket szeretné kapni. Jelölje be az **e-mail-értesítés küldése hiba** esetén jelölőnégyzetet.

    ![Zendesk értesítő E-mail](./media/zendesk-provisioning-tutorial/ZenDesk9.png)

9. Kattintson a **Mentés** gombra.

10. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a zendesk** lehetőséget.

    ![Zendesk-felhasználó szinkronizálása](./media/zendesk-provisioning-tutorial/ZenDesk10.png)

11. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelések** szakasz zendesk. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a zendesk felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások mentéséhez válassza a **Mentés** lehetőséget.

    ![Zendesk egyező felhasználói attribútumok](./media/zendesk-provisioning-tutorial/ZenDesk11.png)

12. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása a zendesk** lehetőséget.

    ![Zendesk csoport szinkronizálása](./media/zendesk-provisioning-tutorial/ZenDesk12.png)

13. Tekintse át az Azure AD-ből szinkronizált zendesk az attribútumok **leképezései** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a zendesk tartozó csoportok egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások mentéséhez válassza a **Mentés** lehetőséget.

    ![Zendesk egyező csoport attribútumai](./media/zendesk-provisioning-tutorial/ZenDesk13.png)

14. A hatóköri szűrők konfigurálásához kövesse a hatókör- [szűrő oktatóanyagának](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)utasításait.

15. Ha engedélyezni szeretné az Azure AD-kiépítési szolgáltatást a zendesk számára, a **Beállítások** szakaszban módosítsa a **kiépítési állapot** beállítást **a** következőre:.

    ![Zendesk kiépítési állapota](./media/zendesk-provisioning-tutorial/ZenDesk14.png)

16. Adja meg a zendesk kiépíteni kívánt felhasználókat vagy csoportokat. A **Beállítások** szakaszban válassza ki a **hatókörben** használni kívánt értékeket.

    ![Zendesk hatóköre](./media/zendesk-provisioning-tutorial/ZenDesk15.png)

17. Ha készen áll a létesítésre, válassza a **Mentés** lehetőséget.

    ![Zendesk mentése](./media/zendesk-provisioning-tutorial/ZenDesk18.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások. Körülbelül 40 percenként történnek, amíg az Azure AD-kiépítési szolgáltatás fut. 

A **szinkronizálás részletei** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenység jelentésének hivatkozásait. A jelentés az Azure AD-kiépítési szolgáltatás által a zendesk-on végrehajtott összes műveletet ismerteti.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="connector-limitations"></a>Összekötő korlátozásai

* A zendesk csak az **ügynöki** szerepkörökkel rendelkező felhasználók számára támogatja a csoportok használatát. További információkért tekintse meg a [zendesk dokumentációját](https://support.zendesk.com/hc/en-us/articles/203661966-Creating-managing-and-using-groups).

* Ha egy felhasználóhoz vagy csoporthoz egyéni szerepkör van rendelve, az Azure AD automatikus felhasználói kiépítési szolgáltatás az alapértelmezett szerepkör- **ügynököt** is hozzárendeli. Csak az ügynökök rendelhetnek egyéni szerepkört. További információkért tekintse meg a [ZENDESK API dokumentációját](https://developer.zendesk.com/rest_api/docs/support/users#json-format-for-agent-or-admin-requests). 

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
