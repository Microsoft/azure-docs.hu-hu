---
title: 'Oktatóanyag: a G Suite konfigurálása automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Ismerje meg, hogyan lehet automatikusan kiépíteni és kiépíteni felhasználói fiókjait az Azure AD-ből a G Suite-be.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/18/2021
ms.author: Zhchia
ms.openlocfilehash: f6ef2f91d178db01e618c19df55705bc9c1209e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104583751"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Oktatóanyag: a G Suite konfigurálása automatikus felhasználók kiépítési feladatokhoz

Ez az oktatóanyag a G Suite és a Azure Active Directory (Azure AD) szolgáltatásban elvégzendő lépéseket ismerteti az automatikus felhasználó-kiépítés konfigurálásához. A konfigurálást követően az Azure AD automatikusan kiépíti és kiosztja a [G Suite](https://gsuite.google.com/) -hoz tartozó felhasználókat és csoportokat az Azure ad kiépítési szolgáltatás használatával. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md). 

> [!NOTE]
> Ez az oktatóanyag az Azure AD-beli felhasználói kiépítési szolgáltatásra épülő összekötőt ismerteti. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása G Suite-ban
> * Ha már nincs szükség hozzáférésre, távolítsa el a felhasználókat a G Suite-ban
> * A felhasználói attribútumok szinkronizálása az Azure AD és a G Suite között
> * Csoportok és csoporttagságok kiépítése G Suite-ban
> * [Egyszeri bejelentkezés](./google-apps-tutorial.md) a G Suite-be (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Azure AD-bérlő](../develop/quickstart-create-new-tenant.md) 
* Egy felhasználói fiók az Azure AD-ben az átadás konfigurálására vonatkozó [engedéllyel](../roles/permissions-reference.md) (pl. alkalmazás-rendszergazda, felhőalkalmazás-rendszergazda, alkalmazástulajdonos vagy globális rendszergazda). 
* [G Suite-bérlő](https://gsuite.google.com/pricing.html)
* A G Suite felhasználói fiókja rendszergazdai jogosultságokkal.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés Az átadás üzembe helyezésének megtervezése
1. Ismerje meg [az átadási szolgáltatás működését](../app-provisioning/user-provisioning.md).
2. Határozza meg, hogy ki lesz [az átadás hatókörében](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Határozza meg az [Azure ad és a G Suite közötti leképezési](../app-provisioning/customize-application-attributes.md)adatok körét. 

## <a name="step-2-configure-g-suite-to-support-provisioning-with-azure-ad"></a>2. lépés A G Suite konfigurálása az Azure AD-vel való kiépítés támogatásához

Mielőtt konfigurálja a G Suite-t az Azure AD-vel való automatikus felhasználói üzembe helyezéshez, engedélyeznie kell a SCIM-létesítést a G Suite-ban.

1. Jelentkezzen be a [G Suite felügyeleti konzolra](https://admin.google.com/) a rendszergazdai fiókjával, majd válassza a **Biztonság** elemet. Ha nem látja a hivatkozást, akkor előfordulhat, hogy a képernyő alján található **további vezérlők** menü alatt rejtve van.

    ![G Suite biztonság](./media/g-suite-provisioning-tutorial/gapps-security.png)

2. A **Biztonság** lapon válassza az **API-hivatkozás** lehetőséget.

    ![G Suite API](./media/g-suite-provisioning-tutorial/gapps-api.png)

3. Válassza az **API-hozzáférés engedélyezése** lehetőséget.

    ![G Suite API engedélyezve](./media/g-suite-provisioning-tutorial/gapps-api-enabled.png)

    > [!IMPORTANT]
   > Minden, a G Suite-nak kiépíteni kívánt felhasználó számára az **Azure ad-** beli felhasználónevét egy egyéni tartományhoz kell kötni. Például az így megjelenő felhasználóneveket a bob@contoso.onmicrosoft.com G Suite nem fogadja el. Másrészről bob@contoso.com elfogadjuk. A meglévő felhasználó tartományát az [itt](../fundamentals/add-custom-domain.md)található útmutatás alapján módosíthatja.

4. Miután hozzáadta és ellenőrizte a kívánt egyéni tartományokat az Azure AD-vel, újra ellenőriznie kell azokat a G Suite-val. A G Suite tartományának ellenőrzéséhez tekintse meg a következő lépéseket:

    a. A [G Suite felügyeleti konzolon](https://admin.google.com/)válassza a **tartományok** lehetőséget.

    ![G Suite-tartományok](./media/g-suite-provisioning-tutorial/gapps-domains.png)

    b. Válassza **a tartomány hozzáadása vagy a tartományi alias** lehetőséget.

    ![G Suite tartomány hozzáadása](./media/g-suite-provisioning-tutorial/gapps-add-domain.png)

    c. Válassza a **másik tartomány hozzáadása** lehetőséget, majd írja be a hozzáadni kívánt tartomány nevét.

    ![G Suite – másik hozzáadása](./media/g-suite-provisioning-tutorial/gapps-add-another.png)

    d. Válassza **a Folytatás lehetőséget, és ellenőrizze a tartomány tulajdonjogát**. Ezután kövesse a lépéseket annak ellenőrzéséhez, hogy a tartománynév tulajdonosa-e. A tartomány a Google-vel való ellenőrzésével kapcsolatos részletes utasításokért lásd: [a hely tulajdonjogának ellenőrzése](https://support.google.com/webmasters/answer/35179).

    e. Ismételje meg az előző lépéseket a G Suite-ba felvenni kívánt további tartományok esetében.

5. Ezután határozza meg, hogy melyik rendszergazdai fiókot szeretné használni a G Suite-ban a felhasználók üzembe helyezésének kezeléséhez. Navigáljon a **rendszergazdai szerepkörök** elemre.

    ![G Suite-rendszergazda](./media/g-suite-provisioning-tutorial/gapps-admin.png)

6. A fiók **rendszergazdai szerepköréhez** szerkessze az adott szerepkörhöz tartozó **jogosultságokat** . Ügyeljen arra, hogy az összes **rendszergazdai API-jogosultságot** engedélyezze, hogy ez a fiók használható legyen az üzembe helyezéshez.

    ![A G Suite rendszergazdai jogosultságai](./media/g-suite-provisioning-tutorial/gapps-admin-privileges.png)

## <a name="step-3-add-g-suite-from-the-azure-ad-application-gallery"></a>3. lépés G csomag hozzáadása az Azure AD Application Galleryből

Vegye fel a G Suite-t az Azure AD-alkalmazás-katalógusból a G Suite-ba való kiépítés kezelésének megkezdéséhez. Ha korábban már beállította a G Suite-ot az SSO-hoz, használhatja ugyanazt az alkalmazást. Az integráció első tesztelésekor azonban érdemes létrehozni egy külön alkalmazást. Az alkalmazások katalógusból való hozzáadásáról [itt](../manage-apps/add-application-portal.md) tudhat meg többet. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Az átadás hatókörében lévő személyek meghatározása 

Az Azure AD átadási szolgáltatása lehetővé teszi az átadott személyek hatókörének meghatározását az alkalmazáshoz való hozzárendelés és/vagy a felhasználó/csoport attribútumai alapján. Ha a hozzárendelés alapján történő hatókör-meghatározást választja, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet felhasználókat és csoportokat az alkalmazáshoz. Ha csak a felhasználó vagy csoport attribútumai alapján történő hatókörmeghatározást választja, az [itt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) leírt hatókörszűrőt használhatja. 

* Amikor felhasználókat és csoportokat rendel a G Suite-hoz, az **alapértelmezett hozzáféréstől** eltérő szerepkört kell kijelölnie. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva az átadásból, és az átadási naplókban nem jogosultként lesznek megjelölve. Ha az alkalmazáshoz csak az alapértelmezett hozzáférési szerepkör érhető el, akkor további szerepkörök felvételéhez [frissítheti az alkalmazásjegyzéket](../develop/howto-add-app-roles-in-azure-ad-apps.md). 

* Kezdje kicsiben. Tesztelje a felhasználók és csoportok kis halmazát, mielőtt mindenkire kiterjesztené. Amikor az átadás hatóköre a hozzárendelt felhasználókra és csoportokra van beállítva, ennek szabályozásához egy vagy két felhasználót vagy csoportot rendelhet az alkalmazáshoz. Amikor a hatókör az összes felhasználóra és csoportra van beállítva, meghatározhat egy [attribútumalapú hatókörszűrőt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-g-suite"></a>5. lépés Automatikus felhasználó-kiépítés konfigurálása a G Suite-hoz 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy TestApp alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

> [!NOTE]
> A G Suite címtár API-végpontjának megismeréséhez tekintse meg a [Directory API](https://developers.google.com/admin-sdk/directory)-t.

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>A G Suite automatikus felhasználói üzembe helyezésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet. A felhasználóknak be kell jelentkezniük a portal.azure.com, és nem fogják tudni használni a aad.portal.azure.com

    ![Vállalati alkalmazások panel](./media/g-suite-provisioning-tutorial/enterprise-applications.png)

    ![Minden alkalmazás panel](./media/g-suite-provisioning-tutorial/all-applications.png)

2. Az alkalmazások listában válassza a **G Suite** elemet.

    ![A G Suite-hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a **kiépítés** lapot. Kattintson az első **lépések** elemre.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

      ![Első lépések panel](./media/g-suite-provisioning-tutorial/get-started.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a kiépítési mód legördülő listájáról az automatikus lehetőséggel.](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban kattintson az **Engedélyezés** elemre. A rendszer átirányítja az új böngészőablakban található Google-engedélyezési párbeszédpanelre.

      ![G Suite engedélyezése](./media/g-suite-provisioning-tutorial/authorize-1.png)

6. Győződjön meg arról, hogy az Azure AD-beli engedélyeket szeretné megadni a G Suite-bérlő módosításához. Válassza ki az **Elfogadás** lehetőséget.

     ![G Suite bérlői hitelesítés](./media/g-suite-provisioning-tutorial/gapps-auth.png)

7. A Azure Portal kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad tud csatlakozni a G Suite-hoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a G Suite-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra. Ezután próbálja megismételni az **Engedélyezés** lépést.

6. Az **Értesítés e-mailben** mezőben adja meg annak a személynek vagy csoportnak az e-mail-címét, aki az átadással kapcsolatos hibaüzeneteket kapja, és jelölje be az **E-mail-értesítés küldése hiba esetén** jelölőnégyzetet.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók kiépítése** lehetőséget.

9. Tekintse át az Azure AD-ból a G Suite-be szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelés** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a G Suite-ban lévő felhasználói fiókoknak a frissítési műveletekhez való megfeleltetésére szolgálnak. Ha úgy dönt, hogy módosítja a [megfelelő cél attribútumot](../app-provisioning/customize-application-attributes.md), akkor biztosítania kell, hogy a G Suite API támogassa a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   |Attribútum|Típus|
   |---|---|
   |primaryEmail|Sztring|
   |kapcsolatok. [type EQ "Manager"]. Value|Sztring|
   |name.familyName|Sztring|
   |name.givenName|Sztring|
   |függesztve|Sztring|
   |externalIds. [type EQ "Custom"]. Value|Sztring|
   |externalIds. [type EQ "szervezet"]. Value|Sztring|
   |címek. [type EQ "work"]. Country|Sztring|
   |címek. [type EQ "work"]. streetAddress|Sztring|
   |címek. [írja be a következőt: EQ "work"]. régió|Sztring|
   |címek. [írja be a következőt: EQ "work"]. helység|Sztring|
   |címek. [type EQ "work"]. irányítószám|Sztring|
   |e-mailek. [írja be a következőt: EQ "work"]. címe|Sztring|
   |szervezetek. [írja be a következőt: EQ "work"]. részleg|Sztring|
   |szervezetek. [type EQ "work"]. title|Sztring|
   |phoneNumbers. [type EQ "work"]. Value|Sztring|
   |phoneNumbers. [type EQ "Mobile"]. Value|Sztring|
   |phoneNumbers. [type EQ "work_fax"]. Value|Sztring|
   |e-mailek. [írja be a következőt: EQ "work"]. címe|Sztring|
   |szervezetek. [írja be a következőt: EQ "work"]. részleg|Sztring|
   |szervezetek. [type EQ "work"]. title|Sztring|
   |phoneNumbers. [type EQ "work"]. Value|Sztring|
   |phoneNumbers. [type EQ "Mobile"]. Value|Sztring|
   |phoneNumbers. [type EQ "work_fax"]. Value|Sztring|
   |címek. [type EQ "Home"]. Country|Sztring|
   |címek. [type EQ "Home"]. formázott|Sztring|
   |címek. [type EQ "Home"]. helység|Sztring|
   |címek. [type EQ "Home"]. irányítószám|Sztring|
   |címek. [type EQ "Home"]. régió|Sztring|
   |címek. [type EQ "Home"]. streetAddress|Sztring|
   |címek. [type EQ "other"]. Country|Sztring|
   |címek. [type EQ "other"]. formázott|Sztring|
   |címek. [írja be a következőt: EQ "other"]. helység|Sztring|
   |címek. [type EQ "other"]. irányítószám|Sztring|
   |címek. [type EQ "other"]. régió|Sztring|
   |címek. [type EQ "other"]. streetAddress|Sztring|
   |címek. [type EQ "work"]. formázott|Sztring|
   |changePasswordAtNextLogin|Sztring|
   |e-mailek. [type EQ "Home"]. címe|Sztring|
   |e-mailek. [type EQ "other"]. címe|Sztring|
   |externalIds. [írja be a következőt: EQ "account"]. Value|Sztring|
   |externalIds. [type EQ "Custom"]. customType|Sztring|
   |externalIds. [type EQ "Customer"]. Value|Sztring|
   |externalIds. [type EQ "login_id"]. Value|Sztring|
   |externalIds. [type EQ "Network"]. Value|Sztring|
   |nemre. Type|Sztring|
   |GeneratedImmutableId|Sztring|
   |Azonosító|Sztring|
   |IMS. [type EQ "Home"]. protokoll|Sztring|
   |IMS. [type EQ "other"]. Protocol|Sztring|
   |IMS. [írja be a következőt: EQ "work"]. protokoll|Sztring|
   |includeInGlobalAddressList|Sztring|
   |ipWhitelisted|Sztring|
   |szervezetek. [type EQ "School"]. costCenter|Sztring|
   |szervezetek. [type EQ "iskola"]. részleg|Sztring|
   |szervezetek. [type EQ "School"]. domain|Sztring|
   |szervezetek. [type EQ "School"]. fullTimeEquivalent|Sztring|
   |szervezetek. [írja be a következőt: EQ "School"]. hely|Sztring|
   |szervezetek. [type EQ "School"]. név|Sztring|
   |szervezetek. [type EQ "iskola"]. szimbólum|Sztring|
   |szervezetek. [type EQ "iskola"]. title|Sztring|
   |szervezetek. [type EQ "work"]. costCenter|Sztring|
   |szervezetek. [type EQ "work"]. domain|Sztring|
   |szervezetek. [type EQ "work"]. fullTimeEquivalent|Sztring|
   |szervezetek. [írja be a következőt: EQ "work"]. hely|Sztring|
   |szervezetek. [írja be a következőt: EQ "work"]. név|Sztring|
   |szervezetek. [írja be a következőt: EQ "work"]. szimbólum|Sztring|
   |OrgUnitPath|Sztring|
   |phoneNumbers. [type EQ "Home"]. Value|Sztring|
   |phoneNumbers. [type EQ "other"]. Value|Sztring|
   |weboldalak. [type EQ "Home"]. Value|Sztring|
   |weboldalak. [type EQ "other"]. Value|Sztring|
   |weboldalak. [type EQ "work"]. Value|Sztring|
   

10. A **leképezések** szakaszban válassza ki a **Azure Active Directory csoportok kiépítése** lehetőséget.

11. Tekintse át az Azure AD-ból a G Suite-be szinkronizált csoport attribútumait az **attribútum-hozzárendelés** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a G Suite-ban a frissítési műveletekhez tartozó csoportokkal egyeznek meg. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

      |Attribútum|Típus|
      |---|---|
      |e-mail|Sztring|
      |Tagok|Sztring|
      |name|Sztring|
      |leírás|Sztring|

12. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

13. A G Suite-hoz készült Azure AD-kiépítési szolgáltatás engedélyezéséhez módosítsa a **kiépítési állapotot** **a következőre** a **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg a G Suite-hoz kiépíteni kívánt felhasználókat és/vagy csoportokat, ha a **Settings (beállítások** ) szakaszban a megfelelő értékeket választja ki a **hatókörben** .

    ![Átadási hatókör](common/provisioning-scope.png)

15. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet a **Beállítások** szakasz **Hatókör** területén meghatározott összes felhasználó és csoport kezdeti szinkronizálási ciklusát elindítja. A kezdeti ciklus elvégzése hosszabb időt vesz igénybe, mint a későbbi ciklusok, amelyek az Azure AD átadási szolgáltatásának futtatása során körülbelül 40 percenként lesznek végrehajtva.

> [!NOTE]
> Ha a felhasználók már rendelkeznek egy meglévő személyes/fogyasztói fiókkal az Azure AD-felhasználó e-mail-címével, akkor előfordulhat, hogy a címtár-szinkronizálás végrehajtása előtt a Google átadási eszköz használatával feloldható néhány probléma.

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Az átadás konfigurálása után a következő erőforrásokkal monitorozhatja az üzemelő példányt:

1. Az [átadási naplókkal](../reports-monitoring/concept-provisioning-logs.md) határozhatja meg, hogy mely felhasználók átadása sikeres, és melyeké sikertelen.
2. A [folyamatjelzőn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) láthatja az átadási ciklus állapotát és azt, hogy mennyi hiányzik még a befejeződéséhez.
3. Ha úgy tűnik, hogy az átadási konfiguráció állapota nem megfelelő, az alkalmazás karanténba kerül. A karanténállapotokról [itt](../app-provisioning/application-provisioning-quarantine-status.md) találhat további információt.  

## <a name="change-log"></a>Változási napló

* 10/17/2020 – további támogatás a G Suite-felhasználók és-csoportok további attribútumaihoz.
* 10/17/2020 – a G Suite-beli célként megadott attribútumok neve megegyezik az [itt](https://developers.google.com/admin-sdk/directory)definiált értékkel.
* 10/17/2020 – az alapértelmezett attribútum-hozzárendelések frissítve.
* 03/18/2021 – a kezelő e-mail-címe mostantól az összes új felhasználó azonosítója helyett szinkronizálva lett. Minden olyan meglévő felhasználó számára, aki AZONOSÍTÓként lett kiépítve, újraindítást végezhet [Microsoft Graph](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http) a "teljes" hatókörrel, hogy biztosítsa az e-mail kiosztását. Ez a módosítás csak a GSuite kiépítési feladataira van hatással, nem pedig a Goov2OutDelta-től kezdődően a régebbi probisioning-feladatokra. Vegye figyelembe, hogy a kezelő e-mail-címe a felhasználó első létrehozásakor vagy a kezelő módosításakor lett kiépítve. A kezelő e-mail-címe nincs kiépítve, ha a kezelő megváltoztatja az e-mail-címét. 

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)
