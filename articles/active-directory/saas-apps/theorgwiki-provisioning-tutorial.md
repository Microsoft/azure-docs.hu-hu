---
title: 'Oktatóanyag: a TheOrgWiki konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a TheOrgWiki.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 8238b9902aafcabc079c551a0eabc7170042209a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94357623"
---
# <a name="tutorial-configure-theorgwiki-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés TheOrgWiki konfigurálása

Az oktatóanyag célja annak bemutatása, hogy milyen lépéseket kell végrehajtani a TheOrgWiki és a Azure Active Directory (Azure AD) szolgáltatásban az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikusan kiépítsék és kiépítsék a TheOrgWiki.

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md).
>
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy Azure AD-bérlő.
* [Egy OrgWiki-bérlő](https://www.theorgwiki.com/welcome/).
* Rendszergazdai jogosultságokkal rendelkező felhasználói fiók a TheOrgWiki-ben.

## <a name="assign-users-to-theorgwiki"></a>Felhasználók TheOrgWiki rendelése

Azure Active Directory a hozzárendelések nevű koncepció használatával határozza meg, hogy mely felhasználók kapnak hozzáférést a kiválasztott alkalmazásokhoz. Az automatikus felhasználó-kiépítés kontextusában csak az Azure AD-alkalmazáshoz hozzárendelt felhasználók és/vagy csoportok lesznek szinkronizálva.

A felhasználók automatikus üzembe helyezésének konfigurálása és engedélyezése előtt döntse el, hogy az Azure AD mely felhasználóinak és/vagy csoportjai számára szükséges a TheOrgWiki való hozzáférés. Miután eldöntötte, ezeket a felhasználókat és/vagy csoportokat hozzárendelheti a TheOrgWiki az alábbi utasításokat követve:

* [Felhasználó vagy csoport társítása vállalati alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-theorgwiki"></a>Fontos Tippek a felhasználók TheOrgWiki való hozzárendeléséhez

* Azt javasoljuk, hogy egyetlen Azure AD-felhasználó legyen hozzárendelve a TheOrgWiki-hoz az automatikus felhasználó-kiépítési konfiguráció teszteléséhez. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

* Amikor TheOrgWiki rendel hozzá egy felhasználóhoz, a hozzárendelés párbeszédpanelen ki kell választania bármely érvényes alkalmazásspecifikus szerepkört (ha elérhető). Az **alapértelmezett hozzáférési** szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól.

## <a name="set-up-theorgwiki-for-provisioning"></a>TheOrgWiki beállítása a kiépítés számára

Az Azure AD-vel való automatikus TheOrgWiki konfigurálása előtt engedélyeznie kell a SCIM-létesítést a TheOrgWiki-on.

1. Jelentkezzen be a [TheOrgWiki felügyeleti konzolra](https://www.theorgwiki.com/login/). Kattintson a **felügyeleti konzol** elemre.

    ![Képernyőkép a szervezeti wikiről a felhasználó avatárja és az adminisztrátori konzol neve mellett.](media/theorgwiki-provisioning-tutorial/login.png)

2. A felügyeleti konzolon kattintson a **Beállítások fülre**. 

    ![Képernyőkép a szervezeti wiki felügyeleti konzolról, a beállítások lapon.](media/theorgwiki-provisioning-tutorial/settings.png)
    
3. Navigáljon a **szolgáltatásfiókok** elemre.

    ![Képernyőkép a Service accounts (szolgáltatásfiókok) lapról a szervezeti wiki felügyeleti konzolján.](media/theorgwiki-provisioning-tutorial/serviceaccount.png)

4. Kattintson a **+ szolgáltatás fiók** lehetőségre. A **szolgáltatásfiók típusa** területen válassza a **jogkivonat-alapú** lehetőséget. Kattintson a **Mentés** gombra.

    ![Képernyőkép az új szolgáltatásfiók párbeszédpanelről, amely a szolgáltatásfiók típusát, a jogkivonat-alapú és a mentési beállításokat veszi igénybe.](media/theorgwiki-provisioning-tutorial/auth.png)

5.  Másolja az **aktív jogkivonatokat**. Ez az érték a TheOrgWiki alkalmazás üzembe helyezés lapjának titkos jogkivonat mezőjében jelenik meg a Azure Portal.
     
    ![Képernyőkép az S C I-kiépítési tokenek kezelése párbeszédpanelről.](media/theorgwiki-provisioning-tutorial/token.png)

## <a name="add-theorgwiki-from-the-gallery"></a>TheOrgWiki hozzáadása a gyűjteményből

Az Azure AD-vel való automatikus TheOrgWiki konfigurálásához hozzá kell adnia a TheOrgWiki az Azure AD Application Gallery-ből a felügyelt SaaS-alkalmazások listájához.

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen válassza a **Azure Active Directory** lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a panel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **TheOrgWiki** kifejezést, majd az eredmények panelen kattintson a **TheOrgWiki** elemre. 

    ![TheOrgWiki az eredmények listájában](common/search-new-app.png)

5. Válassza ki a **TheOrgWiki (regisztráció)** gombot, amely átirányítja Önt a TheOrgWiki bejelentkezési oldalára. 

    ![A szervezeti wiki bejelentkezési oldalának képernyőképe az elnevezett URL-címmel](media/theorgwiki-provisioning-tutorial/image00.png)

6.  A jobb felső sarokban válassza a **Bejelentkezés** lehetőséget.

    ![Képernyőkép a bejelentkezési oldal jobb felső sarkáról, a bejelentkezés lehetőséggel.](media/theorgwiki-provisioning-tutorial/image02.png)

7. Mivel a TheOrgWiki egy OpenIDConnect-alkalmazás, a Microsoft munkahelyi fiókjával való bejelentkezéshez válassza a OrgWiki lehetőséget.

    ![Képernyőkép a szervezeti wiki bejelentkezési oldaláról, az Bejelentkezés Microsoft-fiókkal lehetőséggel.](media/theorgwiki-provisioning-tutorial/image03.png)
    
8. Sikeres hitelesítés után a rendszer automatikusan hozzáadja az alkalmazást a bérlőhöz, és a rendszer átirányítja a TheOrgWiki-fiókjába.

    ![OrgWiki-SCIM hozzáadása](media/theorgwiki-provisioning-tutorial/image04.png)

## <a name="configure-automatic-user-provisioning-to-theorgwiki"></a>Automatikus felhasználó-kiépítés beállítása a TheOrgWiki 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy TheOrgWiki alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.


### <a name="to-configure-automatic-user-provisioning-for-theorgwiki-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a TheOrgWiki az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **TheOrgWiki** lehetőséget.

    ![Az OrgWiki hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a kiépítési mód legördülő listájáról az automatikus lehetőséggel.](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja `https://<TheOrgWiki Subdomain        value>.theorgwiki.com/api/v2/scim/v2/` meg a **bérlői URL-címet**. 

    Például: `https://test1.theorgwiki.com/api/v2/scim/v2/`

> [!NOTE]
> Az **altartomány értéke** csak a TheOrgWiki kezdeti regisztrálási folyamata során állítható be.
 
6. Adja meg a jogkivonat értékét a **titkos jogkivonat** mezőben, amelyet korábban a TheOrgWiki-ből adott vissza. Kattintson a **kapcsolat tesztelése** lehetőségre, hogy az Azure ad képes legyen csatlakozni a TheOrgWiki. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a TheOrgWiki-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

7. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba** esetén.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

8. Kattintson a **Mentés** gombra.

9. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a TheOrgWiki** lehetőséget.

    ![TheOrgWiki felhasználói leképezések](media/theorgwiki-provisioning-tutorial/usermapping.png)

10. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban lévő TheOrgWiki. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a TheOrgWiki felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    ![TheOrgWiki felhasználói attribútumai](media/theorgwiki-provisioning-tutorial/userattribute.png).

11. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

12. Az Azure AD-kiépítési szolgáltatás TheOrgWiki való engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

13. Adja meg a OrgWiki kiépíteni kívánt felhasználókat és/vagy csoportokat a **Settings (beállítások** ) szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Átadási hatókör](common/provisioning-scope.png)

14. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások. További információ arról, hogy mennyi ideig tart a felhasználók és/vagy csoportok kiépítése, hogy [mennyi ideig tart a felhasználók kiépítése](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

A **jelenlegi állapot** szakasz segítségével figyelheti a folyamat előrehaladását, és követheti a kiépítési tevékenység jelentésére mutató hivatkozásokat, amelyek az Azure ad-kiépítési szolgáltatás által a TheOrgWiki-on végrehajtott összes műveletet ismertetik. További információ: [a felhasználó kiépítési állapotának ellenõrzése](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). Az Azure AD-létesítési naplók olvasásához lásd: [jelentéskészítés az automatikus felhasználói fiók kiépítés](../app-provisioning/check-status-user-account-provisioning.md)során.

## <a name="additional-resources"></a>További források

* [A vállalati alkalmazások felhasználói fiókok üzembe](../app-provisioning/configure-automatic-user-provisioning-portal.md)helyezésének kezelése.
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../app-provisioning/check-status-user-account-provisioning.md).