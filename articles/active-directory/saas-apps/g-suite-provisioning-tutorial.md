---
title: 'Oktatóanyag: A G Suite konfigurálása automatikus felhasználóátépítéshez Azure Active Directory | Microsoft Docs'
description: Ismerje meg, hogyan lehet automatikusan kiépítni és megszűkni a felhasználói fiókokat az Azure AD-ről a G Suite-ba.
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
ms.openlocfilehash: b8513f62b6f181a1490d136062c5de81db847ba7
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533385"
---
# <a name="tutorial-configure-g-suite-for-automatic-user-provisioning"></a>Oktatóanyag: A G Suite konfigurálása automatikus felhasználóátépítéshez

Ez az oktatóanyag azokat a lépéseket ismerteti, amelyek a G Suite-ban és a Azure Active Directory (Azure AD) az automatikus felhasználóátépítés konfigurálásában is szükségesek. Ha konfigurálva van, az Azure AD automatikusan kiépíti és kiépíti a felhasználókat és csoportokat a [G Suite-ban](https://gsuite.google.com/) az Azure AD provisioning service használatával. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md). 

> [!NOTE]
> Ez az oktatóanyag egy, az Azure AD felhasználóátépítési szolgáltatásra épült összekötőt ismertet. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a G Suite-ban
> * Felhasználók eltávolítása a G Suite-ban, ha már nincs szükségük hozzáférésre
> * A felhasználói attribútumok szinkronizálása az Azure AD és a G Suite között
> * Csoportok és csoporttagságok kiépítése a G Suite-ban
> * Egyszeri bejelentkezés a G [Suite-be](./google-apps-tutorial.md) (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik az alábbi előfeltételek valamelyikével:

* [Egy Azure AD-bérlő](../develop/quickstart-create-new-tenant.md) 
* Egy felhasználói fiók az Azure AD-ben az átadás konfigurálására vonatkozó [engedéllyel](../roles/permissions-reference.md) (pl. alkalmazás-rendszergazda, felhőalkalmazás-rendszergazda, alkalmazástulajdonos vagy globális rendszergazda). 
* [G Suite-bérlő](https://gsuite.google.com/pricing.html)
* Rendszergazdai engedélyekkel rendelkező felhasználói fiók a G Suite-on.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés Az átadás üzembe helyezésének megtervezése
1. Ismerje meg [az átadási szolgáltatás működését](../app-provisioning/user-provisioning.md).
2. Határozza meg, hogy ki lesz [az átadás hatókörében](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Határozza meg, hogy milyen adatokat kell [leképezni az Azure AD és a G Suite között.](../app-provisioning/customize-application-attributes.md) 

## <a name="step-2-configure-g-suite-to-support-provisioning-with-azure-ad"></a>2. lépés A G Suite konfigurálása az Azure AD-beli kiépítés támogatására

Mielőtt konfigurálja a G Suite-et az Azure AD automatikus felhasználóátépítéshez, engedélyeznie kell az SCIM-kiépítést a G Suite-ban.

1. Jelentkezzen be a G Suite felügyeleti [konzolra](https://admin.google.com/) a rendszergazdai fiókjával, majd válassza a **Biztonság lehetőséget.** Ha nem látja a hivatkozást, előfordulhat, hogy  a képernyő alján található További vezérlők menüben el van rejtve.

    ![G Suite-biztonság](./media/g-suite-provisioning-tutorial/gapps-security.png)

2. A Biztonság **lapon** válassza az **API-referencia lehetőséget.**

    ![G Suite API](./media/g-suite-provisioning-tutorial/gapps-api.png)

3. Válassza **az API-hozzáférés engedélyezése lehetőséget.**

    ![G Suite API engedélyezve](./media/g-suite-provisioning-tutorial/gapps-api-enabled.png)

    > [!IMPORTANT]
   > A G Suite-ban kiépítni kívánt összes felhasználó felhasználónevének egy egyéni tartományhoz kell kötődnie az Azure AD-ban.  A G Suite nem fogadja el például a hasonló bob@contoso.onmicrosoft.com felhasználóneveket. A viszont bob@contoso.com elfogadva lesz. Egy meglévő felhasználó tartományát az itt található utasítások szerint [módosíthatja.](../fundamentals/add-custom-domain.md)

4. Miután hozzáadta és ellenőrizte a kívánt egyéni tartományokat az Azure AD-val, ezeket újra ellenőriznie kell a G Suite-ban. A G Suite-tartományok ellenőrzéséhez kövesse az alábbi lépéseket:

    a. A [G Suite felügyeleti konzolján válassza a](https://admin.google.com/)Tartományok **lehetőséget.**

    ![G Suite-tartományok](./media/g-suite-provisioning-tutorial/gapps-domains.png)

    b. Válassza **a Tartomány hozzáadása vagy tartományi alias lehetőséget.**

    ![G Suite – Tartomány hozzáadása](./media/g-suite-provisioning-tutorial/gapps-add-domain.png)

    c. Válassza **a Másik tartomány hozzáadása** lehetőséget, majd írja be a hozzáadni kívánt tartomány nevét.

    ![G Suite – Újabb hozzáadása](./media/g-suite-provisioning-tutorial/gapps-add-another.png)

    d. Válassza a **Folytatás lehetőséget, és ellenőrizze a tartomány tulajdonjogát.** Ezután kövesse a lépéseket annak ellenőrzéséhez, hogy Ön-e a tartománynév a tulajdonában. A tartomány Google-lal történő ellenőrzésére vonatkozó átfogó utasításokért lásd: [Webhely tulajdonjogának ellenőrzése.](https://support.google.com/webmasters/answer/35179)

    e. Ismételje meg az előző lépéseket a G Suite-hoz hozzáadni kívánt további tartományokkal is.

5. Ezután határozza meg, hogy melyik rendszergazdai fiókot szeretné használni a felhasználókiépítés kezeléséhez a G Suite-ban. Lépjen a **Rendszergazdai szerepkörök elemre.**

    ![G Suite-rendszergazda](./media/g-suite-provisioning-tutorial/gapps-admin.png)

6. A fiók **rendszergazdai** szerepköreként szerkessze az erre a **szerepkörre** vonatkozó jogosultságokat. Győződjön meg arról, hogy minden **rendszergazdai API-jogosultságot** engedélyez, hogy ez a fiók használható legyen a kiépítéshez.

    ![G Suite rendszergazdai jogosultságok](./media/g-suite-provisioning-tutorial/gapps-admin-privileges.png)

## <a name="step-3-add-g-suite-from-the-azure-ad-application-gallery"></a>3. lépés A G Suite hozzáadása az Azure AD alkalmazásgyűjteményből

Adja hozzá a G Suite-et az Azure AD alkalmazásgyűjteményből a G Suite-ba való kiépítés kezelésének elkezdéséhez. Ha korábban már telepítette a G Suite-et az SSO-hez, használhatja ugyanazt az alkalmazást. Az integráció első tesztelésekor azonban érdemes létrehozni egy külön alkalmazást. Az alkalmazások katalógusból való hozzáadásáról [itt](../manage-apps/add-application-portal.md) tudhat meg többet. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Az átadás hatókörében lévő személyek meghatározása 

Az Azure AD átadási szolgáltatása lehetővé teszi az átadott személyek hatókörének meghatározását az alkalmazáshoz való hozzárendelés és/vagy a felhasználó/csoport attribútumai alapján. Ha a hozzárendelés alapján történő hatókör-meghatározást választja, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet felhasználókat és csoportokat az alkalmazáshoz. Ha csak a felhasználó vagy csoport attribútumai alapján történő hatókörmeghatározást választja, az [itt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) leírt hatókörszűrőt használhatja. 

* Amikor felhasználókat és csoportokat rendel a G Suite-hoz, az Alapértelmezett hozzáféréstől különböző **szerepkört kell választania.** Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva az átadásból, és az átadási naplókban nem jogosultként lesznek megjelölve. Ha az alkalmazáshoz csak az alapértelmezett hozzáférési szerepkör érhető el, akkor további szerepkörök felvételéhez [frissítheti az alkalmazásjegyzéket](../develop/howto-add-app-roles-in-azure-ad-apps.md). 

* Kezdje kicsiben. Tesztelje a felhasználók és csoportok kis halmazát, mielőtt mindenkire kiterjesztené. Amikor az átadás hatóköre a hozzárendelt felhasználókra és csoportokra van beállítva, ennek szabályozásához egy vagy két felhasználót vagy csoportot rendelhet az alkalmazáshoz. Amikor a hatókör az összes felhasználóra és csoportra van beállítva, meghatározhat egy [attribútumalapú hatókörszűrőt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-g-suite"></a>5. lépés A G Suite automatikus felhasználóátépítésének konfigurálása 

Ez a szakasz végigvezeti az Azure AD kiépítési szolgáltatás konfigurálásához szükséges lépéseken felhasználók és/vagy csoportok létrehozásához, frissítéséhez és letiltásához a TestAppben felhasználói és/vagy csoport-hozzárendelések alapján az Azure AD-ban.

> [!NOTE]
> A G Suite Directory API-végpontjára vonatkozó további információkért lásd: [Directory API.](https://developers.google.com/admin-sdk/directory)

### <a name="to-configure-automatic-user-provisioning-for-g-suite-in-azure-ad"></a>A G Suite automatikus felhasználóátépítésének konfigurálása az Azure AD-ban:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet. A felhasználóknak be kell jelentkeznie a portal.azure.com, és nem fogják tudni használni aad.portal.azure.com

    ![Vállalati alkalmazások panel](./media/g-suite-provisioning-tutorial/enterprise-applications.png)

    ![Minden alkalmazás panel](./media/g-suite-provisioning-tutorial/all-applications.png)

2. Az alkalmazások listájában válassza a **G Suite lehetőséget.**

    ![A G Suite hivatkozás az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot. Kattintson az Első **lépések elemre.**

    ![Képernyőkép a Beállítások kezelése lehetőségről a Kiépítés lehetőséggel.](common/provisioning.png)

      ![Első lépések panel](./media/g-suite-provisioning-tutorial/get-started.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a Kiépítési mód legördülő listáról az Automatikus lehetőséggel.](common/provisioning-automatic.png)

5. A **Rendszergazdai hitelesítő adatok szakaszban** kattintson az Authorize (Hitelesítés) **elemre.** A rendszer egy új böngészőablakban átirányítja a Google engedélyezési párbeszédpaneljére.

      ![G Suite-engedély](./media/g-suite-provisioning-tutorial/authorize-1.png)

6. Erősítse meg, hogy engedélyeket szeretne adni az Azure AD-nek a G Suite-bérlő módosításaihoz. Válassza ki az **Elfogadás** lehetőséget.

     ![G Suite-bérlő hitelesítése](./media/g-suite-provisioning-tutorial/gapps-auth.png)

7. A Azure Portal kattintson a **Kapcsolat** tesztelése elemre annak biztosításához, hogy az Azure AD kapcsolódhat a G Suite-hoz. Ha a kapcsolat meghiúsul, ellenőrizze, hogy a G Suite-fiók rendelkezik-e rendszergazdai engedélyekkel, majd próbálkozzon újra. Ezután próbálkozzon újra **az Authorize** lépéssel.

6. Az **Értesítés e-mailben** mezőben adja meg annak a személynek vagy csoportnak az e-mail-címét, aki az átadással kapcsolatos hibaüzeneteket kapja, és jelölje be az **E-mail-értesítés küldése hiba esetén** jelölőnégyzetet.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **Leképezések szakaszban** válassza a **Felhasználók Azure Active Directory lehetőséget.**

9. Tekintse át az Azure AD-ről a G Suite-ba szinkronizált felhasználói attribútumokat az **Attribútumleképezés szakaszban.** Az Egyező  tulajdonságokként kiválasztott attribútumok a G Suite felhasználói fiókjaihoz illeszkednek a frissítési műveletekhez. Ha úgy dönt, [](../app-provisioning/customize-application-attributes.md)hogy módosítja a megfelelő célattribútumot, győződjön meg arról, hogy a G Suite API támogatja a felhasználók ezen attribútum alapján való szűrését. A módosítások **véglegesítéshez** kattintson a Mentés gombra.

   |Attribútum|Típus|
   |---|---|
   |primaryEmail (elsődleges e-mail-cím)|Sztring|
   |Kapcsolatok. [type eq "manager"].value|Sztring|
   |name.familyName|Sztring|
   |name.givenName|Sztring|
   |Felfüggesztett|Sztring|
   |externalId-ekkel. [type eq "custom"].value|Sztring|
   |externalId-ekkel. [type eq "organization"].value|Sztring|
   |Címek. [type eq "work"].country|Sztring|
   |Címek. [type eq "work"].streetAddress|Sztring|
   |Címek. [type eq "work"].region|Sztring|
   |Címek. [type eq "work"].locality|Sztring|
   |Címek. [írja be az eq "work"].postalCode parancsot|Sztring|
   |E-mail. [type eq "work"].address|Sztring|
   |Szervezetek. [type eq "work"].department|Sztring|
   |Szervezetek. [type eq "work"].title|Sztring|
   |phoneNumbers( telefonszámok). [type eq "work"].value|Sztring|
   |phoneNumbers( telefonszámok). [type eq "mobile"].value|Sztring|
   |phoneNumbers( telefonszámok). [type eq "work_fax"].value|Sztring|
   |E-mail. [type eq "work"].address|Sztring|
   |Szervezetek. [type eq "work"].department|Sztring|
   |Szervezetek. [type eq "work"].title|Sztring|
   |phoneNumbers( telefonszámok). [type eq "work"].value|Sztring|
   |phoneNumbers( telefonszámok). [type eq "mobile"].value|Sztring|
   |phoneNumbers( telefonszámok). [type eq "work_fax"].value|Sztring|
   |Címek. [type eq "home"].country|Sztring|
   |Címek. [type eq "home"].formatted|Sztring|
   |Címek. [type eq "home"].locality|Sztring|
   |Címek. [type eq "home"].postalCode|Sztring|
   |Címek. [type eq "home"].region|Sztring|
   |Címek. [type eq "home"].streetAddress|Sztring|
   |Címek. [type eq "other"].country|Sztring|
   |Címek. [type eq "other"].formatted|Sztring|
   |Címek. [type eq "other"].locality|Sztring|
   |Címek. [type eq "other"].postalCode|Sztring|
   |Címek. [type eq "other"].region|Sztring|
   |Címek. [type eq "other"].streetAddress|Sztring|
   |Címek. [type eq "work"].formatted|Sztring|
   |changePasswordAtNextLogin|Sztring|
   |E-mail. [type eq "home"].address|Sztring|
   |E-mail. [type eq "other"].address|Sztring|
   |externalIds ( externalIds) [type eq "account"].value|Sztring|
   |externalIds ( externalIds) [type eq "custom"].customType|Sztring|
   |externalIds ( externalIds) [type eq "customer"].value|Sztring|
   |externalIds ( externalIds) [type eq "login_id"].value|Sztring|
   |externalIds ( externalIds) [type eq "network"].value|Sztring|
   |gender.type|Sztring|
   |GeneratedImmutableId (GeneratedImmutableId)|Sztring|
   |Azonosító|Sztring|
   |Ims. [type eq "home"].protocol|Sztring|
   |Ims. [type eq "other"].protocol|Sztring|
   |Ims. [type eq "work"].protocol|Sztring|
   |includeInGlobalAddressList|Sztring|
   |ipWhitelisted|Sztring|
   |Szervezetek. [type eq "school"].costCenter|Sztring|
   |Szervezetek. [type eq "school"].department|Sztring|
   |Szervezetek. [type eq "school"].domain|Sztring|
   |Szervezetek. [type eq "school"].fullTimeEquivalent|Sztring|
   |Szervezetek. [type eq "school"].location|Sztring|
   |Szervezetek. [type eq "school"].name|Sztring|
   |Szervezetek. [type eq "school"].symbol|Sztring|
   |Szervezetek. [type eq "school"].title|Sztring|
   |Szervezetek. [type eq "work"].costCenter|Sztring|
   |Szervezetek. [type eq "work"].domain|Sztring|
   |Szervezetek. [type eq "work"].fullTimeEquivalent|Sztring|
   |Szervezetek. [type eq "work"].location|Sztring|
   |Szervezetek. [type eq "work"].name|Sztring|
   |Szervezetek. [type eq "work"].symbol|Sztring|
   |OrgUnitPath|Sztring|
   |phoneNumbers. [type eq "home"].value|Sztring|
   |phoneNumbers. [type eq "other"].value|Sztring|
   |Weboldalak. [type eq "home"].value|Sztring|
   |Weboldalak. [type eq "other"].value|Sztring|
   |Weboldalak. [type eq "work"].value|Sztring|
   

10. A **Leképezések szakaszban** válassza a **Kiépítési** Azure Active Directory lehetőséget.

11. Tekintse át az Azure AD-ről a G Suite-ba szinkronizált csoportattribútumokat az **Attribútumleképezés szakaszban.** Az Egyező tulajdonságokként **kiválasztott** attribútumok a G Suite-csoportokkal egyeznek a frissítési műveletekhez. A módosítások **véglegesítéshez** kattintson a Mentés gombra.

      |Attribútum|Típus|
      |---|---|
      |e-mail|Sztring|
      |Tagok|Sztring|
      |name|Sztring|
      |leírás|Sztring|

12. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

13. Ha engedélyezni szeretné az Azure AD kiépítési szolgáltatást a G Suite számára, módosítsa a Kiépítési állapot beállítást **Be** állapotra a **Beállítások szakaszban.** 

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

14. Határozza meg a G Suite-ban kiépítni kívánt felhasználókat és/vagy csoportokat a Beállítások szakasz Hatókör részében a kívánt értékek **kiválasztásával.** 

    ![Átadási hatókör](common/provisioning-scope.png)

15. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet a **Beállítások** szakasz **Hatókör** területén meghatározott összes felhasználó és csoport kezdeti szinkronizálási ciklusát elindítja. A kezdeti ciklus elvégzése hosszabb időt vesz igénybe, mint a későbbi ciklusok, amelyek az Azure AD átadási szolgáltatásának futtatása során körülbelül 40 percenként lesznek végrehajtva.

> [!NOTE]
> Ha a felhasználók már rendelkezik meglévő személyes/fogyasztói fiókkal az Azure AD-felhasználó e-mail-címével, az olyan problémát okozhat, amely megoldható a Google Transfer eszközzel a címtár-szinkronizálás végrehajtása előtt.

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Az átadás konfigurálása után a következő erőforrásokkal monitorozhatja az üzemelő példányt:

1. Az [átadási naplókkal](../reports-monitoring/concept-provisioning-logs.md) határozhatja meg, hogy mely felhasználók átadása sikeres, és melyeké sikertelen.
2. A [folyamatjelzőn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) láthatja az átadási ciklus állapotát és azt, hogy mennyi hiányzik még a befejeződéséhez.
3. Ha úgy tűnik, hogy az átadási konfiguráció állapota nem megfelelő, az alkalmazás karanténba kerül. A karanténállapotokról [itt](../app-provisioning/application-provisioning-quarantine-status.md) találhat további információt.  

## <a name="change-log"></a>Változási napló

* 2020.10.17. – További G Suite felhasználói és csoportattribútumok támogatása.
* 2020.10.17. – A G Suite célattribútum-nevei az itt [meghatározottaknak megfelelően frissültek.](https://developers.google.com/admin-sdk/directory)
* 2020.10.17. – Frissített alapértelmezett attribútumleképezések.
* 2021.03.18. – A kezelő e-mail-címe mostantól szinkronizálva van az összes új felhasználó azonosítója helyett. Minden olyan meglévő felhasználó esetén, akik azonosítóként egy vezetővel [](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http&preserve-view=true) vannak kiépítve, újraindítást "teljes" hatókörrel Microsoft Graph az e-mail-cím érvénybe léptetését biztosítva. Ez a módosítás csak a GSuite-kiépítési feladatra van hatással, a Goov2OutDelta kezdetű régebbi kiépítési feladatra nem. Vegye figyelembe, hogy a felettes e-mail-címe a felhasználó első létrehozásakor vagy a felettes módosításaikor lesz kiépítve. A felettes e-mail-címe nem lesz kiépítve, ha a felettes megváltoztatja az e-mail-címét. 

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)
