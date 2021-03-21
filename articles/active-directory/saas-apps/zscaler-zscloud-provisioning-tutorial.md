---
title: 'Oktatóanyag: Zscaler-ZSCloud konfigurálása automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Ebből az oktatóanyagból megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a Zscaler ZSCloud.
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
ms.openlocfilehash: fa90cbf1e467416010ae0ba83e9344a84ce52e21
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97936498"
---
# <a name="tutorial-configure-zscaler-zscloud-for-automatic-user-provisioning"></a>Oktatóanyag: a Zscaler-ZSCloud konfigurálása a felhasználók automatikus kiépítési felállításához

Ebből az oktatóanyagból megtudhatja, hogyan konfigurálhatja Azure Active Directory (Azure AD) a felhasználók és/vagy csoportok automatikus kiépítésére és megszüntetésére a Zscaler ZSCloud.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás működéséről és működéséről, valamint a gyakori kérdésekre adott válaszokról a következő témakörben talál további információt: a felhasználók kiépítésének [automatizálása és az SaaS-alkalmazások kiépítése a Azure Active Directory használatával](../app-provisioning/user-provisioning.md).


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett lépések végrehajtásához a következőkre lesz szüksége:

* Egy Azure AD-bérlő.
* Egy Zscaler ZSCloud-bérlő.
* A Zscaler ZSCloud felhasználói fiókja rendszergazdai jogosultságokkal rendelkezik.

> [!NOTE]
> Az Azure AD kiépítési integrációja a Zscaler ZSCloud SCIM API-ra támaszkodik, amely vállalati fiókok számára érhető el.

## <a name="add-zscaler-zscloud-from-the-gallery"></a>Zscaler-ZSCloud hozzáadása a gyűjteményből

Mielőtt Zscaler a ZSCloud az Azure AD-vel való automatikus felhasználói üzembe helyezéshez, a felügyelt SaaS-alkalmazások listájához hozzá kell adnia a Zscaler ZSCloud az Azure AD Application Gallery-ből.

A [Azure Portal](https://portal.azure.com)a bal oldali ablaktáblán válassza a **Azure Active Directory**:

![Válassza az Azure Active Directory elemet.](common/select-azuread.png)

Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** elemet:

![Vállalati alkalmazások](common/enterprise-applications.png)

Alkalmazás hozzáadásához válassza az ablak tetején található **új alkalmazás** elemet:

![Új alkalmazás kiválasztása](common/add-new-app.png)

A keresőmezőbe írja be a **Zscaler ZSCloud** kifejezést. Válassza ki a **Zscaler ZSCloud** az eredmények között, majd válassza a **Hozzáadás** lehetőséget.

![Találatok listája](common/search-new-app.png)

## <a name="assign-users-to-zscaler-zscloud"></a>Felhasználók kiosztása Zscaler ZSCloud

Az Azure AD-felhasználóknak hozzá kell rendelniük a hozzáférést a kiválasztott alkalmazásokhoz, mielőtt azok használni tudnák őket. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók vagy csoportok lesznek szinkronizálva.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt el kell döntenie, hogy mely felhasználókra és/vagy csoportokra van szükség az Azure AD-ben a Zscaler-ZSCloud eléréséhez. Miután eldöntötte, hogy ezeket a felhasználókat és csoportokat hozzárendelheti a Zscaler ZSCloud, a [felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)című témakör útmutatásait követve.

### <a name="important-tips-for-assigning-users-to-zscaler-zscloud"></a>Fontos Tippek a felhasználók Zscaler-ZSCloud való hozzárendeléséhez

* Javasoljuk, hogy először rendeljen egyetlen Azure AD-felhasználót, hogy Zscaler a ZSCloud az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és csoportokat is hozzárendelhet.

* Amikor felhasználót rendel a Zscaler ZSCloud, ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető) a hozzárendelés párbeszédpanelen. Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="set-up-automatic-user-provisioning"></a>Automatikus felhasználó-kiépítés beállítása

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, amelyekkel felhasználókat és csoportokat hozhat létre, frissíthet és letilthat a Zscaler ZSCloud az Azure AD-ben a felhasználók és csoportok hozzárendelései alapján.

> [!TIP]
> Érdemes lehet az SAML-alapú egyszeri bejelentkezést is engedélyezni a Zscaler ZSCloud. Ha ezt teszi, kövesse az [Zscaler ZSCloud egyszeri bejelentkezési oktatóanyagának](zscaler-zsCloud-tutorial.md)utasításait. Az egyszeri bejelentkezés az automatikus felhasználó-kiépítés függetlenül is konfigurálható, de a két szolgáltatás kiegészíti egymást.

> [!NOTE]
> Ha a felhasználók és csoportok kiosztása vagy kiosztása megtörtént, javasoljuk, hogy rendszeres időközönként indítsa újra az üzembe helyezést, hogy a csoporttagság megfelelően frissüljenek. Az újraindítás után a szolgáltatás kikényszeríti a szolgáltatást az összes csoport újraértékelésére és a tagságok frissítésére. 

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és válassza a **vállalati alkalmazások**  >  **minden alkalmazás**  >  **Zscaler ZSCloud**:

    ![Vállalati alkalmazások](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Zscaler ZSCloud**:

    ![Alkalmazások listája](common/all-applications.png)

3. Válassza ki a **kiépítés** fület:

    ![Zscaler-ZSCloud kiépítés](./media/zscaler-zscloud-provisioning-tutorial/provisioningtab.png)

4. A **kiépítési mód** beállítása **automatikusra**:

    ![A kiépítési mód beállítása](./media/zscaler-zscloud-provisioning-tutorial/provisioningcredentials.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a Zscaler ZSCloud-fiókjának **bérlői URL-címét** és **titkos jogkivonatát** a következő lépésben leírtak szerint.

6. A **bérlői URL-cím** és a **titkos jogkivonat** beszerzéséhez nyissa meg a **felügyeleti**  >  **hitelesítési beállításokat** a Zscaler ZSCloud-portálon, és válassza az **SAML** lehetőséget a **Hitelesítés típusa** területen:

    ![Zscaler ZSCloud hitelesítési beállításai](./media/zscaler-zscloud-provisioning-tutorial/secrettoken1.png)

    Válassza az **SAML konfigurálása** lehetőséget az **SAML konfigurálása** ablak megnyitásához:

    ![SAML-ablak konfigurálása](./media/zscaler-zscloud-provisioning-tutorial/secrettoken2.png)

    Válassza az **SCIM-Based kiépítés engedélyezése** lehetőséget, és másolja ki az **alap URL-címet** és a **tulajdonosi jogkivonatot**, majd mentse a beállításokat. A Azure Portal illessze be az **alap URL-címet** a **bérlői URL-cím** mezőbe és a **tulajdonos** tokenjét a **titkos jogkivonat** mezőbe.

7. A **bérlői URL-cím** és a **titkos jogkivonat** mezőiben szereplő értékek megadása után válassza a **kapcsolat tesztelése** lehetőséget, hogy az Azure ad képes legyen csatlakozni a Zscaler-ZSCloud. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Zscaler ZSCloud-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![A kapcsolat tesztelése](./media/zscaler-zscloud-provisioning-tutorial/testconnection.png)

8. Az **értesítési e-mail** mezőbe írja be annak a személynek vagy csoportnak az e-mail-címét, akinek meg kell kapnia a kiépítési hibajelentési értesítéseket. **Ha hiba történik, válassza az e-mail-értesítés küldése** lehetőséget:

    ![Értesítő e-mail beállítása](./media/zscaler-zscloud-provisioning-tutorial/Notification.png)

9. Kattintson a **Mentés** gombra.

10. A **leképezések** szakaszban válassza a **szinkronizálás Azure Active Directory felhasználók ZscalerZSCloud**:

    ![Azure AD-felhasználók szinkronizálása](./media/zscaler-zscloud-provisioning-tutorial/usermappings.png)

11. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az attribútum- **hozzárendelések** szakasz Zscaler ZSCloud. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Zscaler ZSCloud felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez válassza a **Mentés** lehetőséget.

    ![Képernyőfelvétel: az attribútum-hozzárendelések szakasz, amelyen hét leképezés látható.](./media/zscaler-zscloud-provisioning-tutorial/userattributemappings.png)

12. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása ZscalerZSCloud** lehetőséget:

    ![Azure AD-csoportok szinkronizálása](./media/zscaler-zscloud-provisioning-tutorial/groupmappings.png)

13. Tekintse át az Azure AD-ből szinkronizált Zscaler ZSCloud az **attribútum-hozzárendelések** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Zscaler ZSCloud tartozó csoportok egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások elvégzéséhez válassza a **Mentés** lehetőséget.

    ![Képernyőfelvétel: az attribútum-hozzárendelések szakasz, amelyen három leképezés látható.](./media/zscaler-zscloud-provisioning-tutorial/groupattributemappings.png)

14. A hatóköri szűrők konfigurálásához tekintse meg a [hatóköri szűrő oktatóanyagának](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)utasításait.

15. Az Azure AD kiépítési szolgáltatás Zscaler ZSCloud való engedélyezéséhez módosítsa a **kiépítési állapotot** **a következőre** a **Beállítások** szakaszban:

    ![Kiépítési állapot](./media/zscaler-zscloud-provisioning-tutorial/provisioningstatus.png)

16. Adja meg a Zscaler ZSCloud kiépíteni kívánt felhasználókat és/vagy csoportokat, ha a **Beállítások** szakaszban a **hatókör** területen a kívánt értékeket választja:

    ![Hatókör értékei](./media/zscaler-zscloud-provisioning-tutorial/scoping.png)

17. Ha készen áll a létesítésre, válassza a **Mentés** lehetőséget:

    ![Mentés kiválasztása](./media/zscaler-zscloud-provisioning-tutorial/saveprovisioning.png)

Ez a művelet elindítja a **Beállítások** szakaszban a **hatókör** szakaszban meghatározott összes felhasználó és csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, feltéve, hogy az Azure AD kiépítési szolgáltatás fut. Nyomon követheti a folyamat előrehaladását a **szinkronizálás részletei** szakaszban. A kiépítési tevékenységre vonatkozó jelentésre mutató hivatkozásokat is követheti, amelyek az Azure AD-kiépítési szolgáltatás által a Zscaler-ZSCloud végzett összes műveletet ismertetik.

Az Azure AD-kiépítési naplók beolvasásával kapcsolatos információkért lásd: [jelentéskészítés az automatikus felhasználói fiókok üzembe](../app-provisioning/check-status-user-account-provisioning.md)helyezéséhez.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-03.png