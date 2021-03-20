---
title: 'Oktatóanyag: a iProva konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a iProva.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/29/2019
ms.author: Zhchia
ms.openlocfilehash: dbaea9d4aaea982165c96af00f75524b15fa3015
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96178093"
---
# <a name="tutorial-configure-iprova-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés iProva konfigurálása

Az oktatóanyag célja annak bemutatása, hogy milyen lépéseket kell végrehajtani a iProva és a Azure Active Directory (Azure AD) szolgáltatásban az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikusan kiépítsék és kiépítsék a [iProva](https://www.iProva.com/). A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md). 

> [!NOTE]
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a iProva-ben
> * Felhasználók eltávolítása a iProva-ben, ha már nincs szükség hozzáférésre
> * Felhasználói attribútumok szinkronizálása az Azure AD és a iProva között
> * Csoportok és csoporttagságok kiépítése a iProva-ben
> * [Egyszeri bejelentkezés](./iprova-tutorial.md) a iProva-be (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Egy Azure AD-bérlő](../develop/quickstart-create-new-tenant.md).
* Egy felhasználói fiók az Azure AD-ben az átadás konfigurálására vonatkozó [engedéllyel](../roles/permissions-reference.md) (pl. alkalmazás-rendszergazda, felhőalkalmazás-rendszergazda, alkalmazástulajdonos vagy globális rendszergazda).
* [Egy iProva-bérlő](https://www.iProva.com/).
* Rendszergazdai jogosultságokkal rendelkező felhasználói fiók a iProva-ben.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés Az átadás üzembe helyezésének megtervezése
1. Ismerje meg [az átadási szolgáltatás működését](../app-provisioning/user-provisioning.md).
2. Határozza meg, hogy ki lesz [az átadás hatókörében](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Határozza meg, hogy az [Azure ad és a iProva között milyen adatleképezést kell leképezni](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-iprova-to-support-provisioning-with-azure-ad"></a>2. lépés IProva konfigurálása az Azure AD-vel való kiépítés támogatásához

1. Jelentkezzen be a [IProva felügyeleti konzolra](https://www.iProva.com/). Lépjen a **> alkalmazás-kezelés** elemre.

    ![iProva felügyeleti konzol](media/iprova-provisioning-tutorial/admin.png)

2.  Kattintson a **külső felhasználók kezelése** elemre.

    ![iProva-SCIM hozzáadása](media/iprova-provisioning-tutorial/external.png)

3. Új szolgáltató hozzáadásához kattintson a **plusz** ikonra. Az új **szolgáltató hozzáadása** párbeszédpanelen adja meg a **címet**. Dönthet úgy, hogy **IP-alapú hozzáférési korlátozást** ad hozzá. Kattintson **az OK** gombra.

    ![új iProva hozzáadása](media/iprova-provisioning-tutorial/add.png)

    ![iProva-szolgáltató hozzáadása](media/iprova-provisioning-tutorial/addprovider.png)

4.  Kattintson az **állandó jogkivonat** gombra. Másolja ki az **állandó jogkivonatot** , és mentse, mivel ez az egyetlen időpontot jeleníti meg. Ez az érték a iProva alkalmazás üzembe helyezés lapjának titkos jogkivonat mezőjében jelenik meg a Azure Portal.

    ![iProva-létrehozási jogkivonat](media/iprova-provisioning-tutorial/token.png)

## <a name="step-3-add-iprova-from-the-azure-ad-application-gallery"></a>3. lépés IProva hozzáadása az Azure AD Application Galleryből

Vegyen fel iProva az Azure AD-alkalmazás-katalógusból a iProva való kiépítés kezelésének megkezdéséhez. Ha korábban már beállította a iProva az SSO-hoz, használhatja ugyanazt az alkalmazást. Az integráció első tesztelésekor azonban érdemes létrehozni egy külön alkalmazást. Az alkalmazások katalógusból való hozzáadásáról [itt](../manage-apps/add-application-portal.md) tudhat meg többet. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Az átadás hatókörében lévő személyek meghatározása 

Az Azure AD átadási szolgáltatása lehetővé teszi az átadott személyek hatókörének meghatározását az alkalmazáshoz való hozzárendelés és/vagy a felhasználó/csoport attribútumai alapján. Ha a hozzárendelés alapján történő hatókör-meghatározást választja, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet felhasználókat és csoportokat az alkalmazáshoz. Ha csak a felhasználó vagy csoport attribútumai alapján történő hatókörmeghatározást választja, az [itt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) leírt hatókörszűrőt használhatja. 

* Felhasználók és csoportok iProva való hozzárendeléséhez ki kell választania az **alapértelmezett hozzáféréstől** eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva az átadásból, és az átadási naplókban nem jogosultként lesznek megjelölve. Ha az alkalmazáshoz csak az alapértelmezett hozzáférési szerepkör érhető el, akkor további szerepkörök felvételéhez [frissítheti az alkalmazásjegyzéket](../develop/howto-add-app-roles-in-azure-ad-apps.md). 

* Kezdje kicsiben. Tesztelje a felhasználók és csoportok kis halmazát, mielőtt mindenkire kiterjesztené. Amikor az átadás hatóköre a hozzárendelt felhasználókra és csoportokra van beállítva, ennek szabályozásához egy vagy két felhasználót vagy csoportot rendelhet az alkalmazáshoz. Amikor a hatókör az összes felhasználóra és csoportra van beállítva, meghatározhat egy [attribútumalapú hatókörszűrőt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

## <a name="step-5-configure-automatic-user-provisioning-to-iprova"></a>5. lépés Automatikus felhasználó-kiépítés beállítása a iProva 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy iProva alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

### <a name="to-configure-automatic-user-provisioning-for-iprova-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a iProva az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **iProva** lehetőséget.

    ![Az iProva hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a kiépítési mód legördülő listájáról az automatikus lehetőséggel.](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a **scim 2,0 alap URL-címét és az állandó jogkivonat** azon értékeit, amelyeket korábban a **bérlői URL-cím** és a **titkos jogkivonat** mezőiben olvas be. Kattintson a **kapcsolat tesztelése** lehetőségre, hogy az Azure ad képes legyen csatlakozni a iProva. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a iProva-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba** esetén.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a iProva** lehetőséget.

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban található iProva. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a iProva felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   |Attribútum|Típus|
   |---|---|
   |active|Logikai|
   |displayName|Sztring|
   |cím|Sztring|
   |emails[type eq "work"].value|Sztring|
   |preferredLanguage|Sztring|
   |userName (Felhasználónév)|Sztring|
   |címek [type EQ "work"]. Country|Sztring|
   |címek [típus EQ "work"]. helység|Sztring|
   |címek [type EQ "work"]. irányítószám|Sztring|
   |címek [type EQ "work"]. formázott|Sztring|
   |címek [típus EQ "work"]. régió|Sztring|
   |címek [type EQ "work"]. streetAddress|Sztring|
   |címek [type EQ "other"]. formázott|Sztring|
   |name.givenName|Sztring|
   |name.familyName|Sztring|
   |név. formázott|Sztring|
   |phoneNumbers [type EQ "fax"]. Value|Sztring|
   |phoneNumbers[type eq "mobile"].value|Sztring|
   |phoneNumbers[type eq "work"].value|Sztring|
   |externalId|Sztring|
   |szerepkörök [elsődleges EQ "true"]. megjelenítés|Sztring|
   |szerepkörök [elsődleges EQ "true"]. Type|Sztring|
   |roles[primary eq "True"].value|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: részleg|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: User: Division|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: costCenter|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: szervezet|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: employeeNumber|Sztring|


10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása a iProva** lehetőséget.

11. Tekintse át az Azure AD-ből szinkronizált iProva az attribútumok **leképezése** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a iProva tartozó csoportok egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

      |Attribútum|Típus|
      |---|---|
      |displayName|Sztring|
      |tagok|Referencia|

12. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

13. Az Azure AD-kiépítési szolgáltatás iProva való engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg a iProva kiépíteni kívánt felhasználókat és/vagy csoportokat a **Settings (beállítások** ) szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Átadási hatókör](common/provisioning-scope.png)

15. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. 


## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Az átadás konfigurálása után a következő erőforrásokkal monitorozhatja az üzemelő példányt:

1. Az [átadási naplókkal](../reports-monitoring/concept-provisioning-logs.md) határozhatja meg, hogy mely felhasználók átadása sikeres, és melyeké sikertelen.
2. A [folyamatjelzőn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) láthatja az átadási ciklus állapotát és azt, hogy mennyi hiányzik még a befejeződéséhez.
3. Ha úgy tűnik, hogy az átadási konfiguráció állapota nem megfelelő, az alkalmazás karanténba kerül. A karanténállapotokról [itt](../app-provisioning/application-provisioning-quarantine-status.md) találhat további információt.  

## <a name="change-log"></a>Változási napló

* 06/17/2020 – a vállalati bővítmény "Manager" attribútuma el lett távolítva.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)