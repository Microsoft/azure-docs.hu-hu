---
title: 'Oktatóanyag: Felhasználók átadása a Slackhez – Azure AD'
description: Ebből a cikkből megtudhatja, hogyan konfigurálhatja az Azure Active Directoryt a felhasználói fiókok Slackbe való automatikus átadására és megszüntetésére.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: arvinh
ms.openlocfilehash: 92c2ae13b840d7a73d86365ce88584bcafc878e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181458"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>Oktatóanyag: A Slack konfigurálása a felhasználók automatikus átadására

Ennek az oktatóanyagnak az a célja, hogy bemutassa a felhasználói fiókok Azure AD-ból Slackbe való átadása és azok megszüntetése érdekében a Slackben és az Azure AD-ban elvégzendő lépéseket. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a Slackben
> * Felhasználók eltávolítása a Slackben, ha már nincs szükségük hozzáférésre
> * A felhasználói attribútumok folyamatos szinkronizálása az Azure AD és a Slack között
> * Csoportok és csoporttagságok átadása a Slackbe
> * [Egyszeri bejelentkezés](./slack-tutorial.md) a Slackbe (ajánlott)


## <a name="prerequisites"></a>Előfeltételek

Az ebben az oktatóanyagban felvázolt forgatókönyv feltételezi, hogy már rendelkezik a következőkkel:

* [Egy Azure AD-bérlő](../develop/quickstart-create-new-tenant.md).
* Egy felhasználói fiók az Azure AD-ben az átadás konfigurálására vonatkozó [engedéllyel](../roles/permissions-reference.md) (pl. alkalmazás-rendszergazda, felhőalkalmazás-rendszergazda, alkalmazástulajdonos vagy globális rendszergazda).
* Egy [Plus](https://aadsyncfabric.slack.com/pricing) vagy magasabb szintű csomagot használó Slack-bérlő.
* Egy csapatrendszergazdai engedélyekkel rendelkező felhasználói fiók a Slackben.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés Az átadás üzembe helyezésének megtervezése
1. Ismerje meg [az átadási szolgáltatás működését](../app-provisioning/user-provisioning.md).
2. Határozza meg, hogy ki lesz [az átadás hatókörében](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Határozza meg, milyen adatokat [képez le az Azure AD és a Slack között](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-add-slack-from-the-azure-ad-application-gallery"></a>2. lépés A Slack hozzáadása az Azure AD-alkalmazáskatalógusból

Adja hozzá a Slacket az Azure AD-alkalmazáskatalógusból a Slackbe való átadás kezelésének megkezdéséhez. Ha korábban egyszeri bejelentkezésre állította be a Slacket, használhatja ugyanazt az alkalmazást. Az integráció első tesztelésekor azonban érdemes létrehozni egy külön alkalmazást. Az alkalmazások katalógusból való hozzáadásáról [itt](../manage-apps/add-application-portal.md) tudhat meg többet. 

## <a name="step-3-define-who-will-be-in-scope-for-provisioning"></a>3. lépés Az átadás hatókörében lévő személyek meghatározása 

Az Azure AD átadási szolgáltatása lehetővé teszi az átadott személyek hatókörének meghatározását az alkalmazáshoz való hozzárendelés és/vagy a felhasználó/csoport attribútumai alapján. Ha a hozzárendelés alapján történő hatókör-meghatározást választja, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet felhasználókat és csoportokat az alkalmazáshoz. Ha csak a felhasználó vagy csoport attribútumai alapján történő hatókörmeghatározást választja, az [itt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) leírt hatókörszűrőt használhatja. 

* Amikor felhasználókat és csoportokat rendel a Slackhez, az **alapértelmezett hozzáféréstől** eltérő szerepkört kell választania. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva az átadásból, és az átadási naplókban nem jogosultként lesznek megjelölve. Ha az alkalmazáshoz csak az alapértelmezett hozzáférési szerepkör érhető el, akkor további szerepkörök felvételéhez [frissítheti az alkalmazásjegyzéket](../develop/howto-add-app-roles-in-azure-ad-apps.md). 

* Kezdje kicsiben. Tesztelje a felhasználók és csoportok kis halmazát, mielőtt mindenkire kiterjesztené. Amikor az átadás hatóköre a hozzárendelt felhasználókra és csoportokra van beállítva, ennek szabályozásához egy vagy két felhasználót vagy csoportot rendelhet az alkalmazáshoz. Amikor a hatókör az összes felhasználóra és csoportra van beállítva, meghatározhat egy [attribútumalapú hatókörszűrőt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="step-4-configure-automatic-user-provisioning-to-slack"></a>4. lépés: A felhasználók Slackbe való automatikus átadásának konfigurálása 

Ez a szakasz végigvezeti az Azure AD a Slack felhasználóifiók-átadási API-jához való csatlakoztatásán, valamint az átadási szolgáltatás konfigurálásán a hozzárendelt felhasználói fiókok létrehozására, frissítésére és letiltására a Slackben az Azure AD felhasználó- és csoport-hozzárendelései alapján.

### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>A felhasználói fiókok Slackbe való automatikus átadásának konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Slack** elemet.

    ![A Slack hivatkozása az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a kiépítési mód legördülő listájáról az automatikus lehetőséggel.](common/provisioning-automatic.png)

5. A **Rendszergazdai hitelesítő adatok** szakaszban kattintson az **Engedélyezés** elemre. Ez megnyit egy Slack-engedélyezési párbeszédpanelt egy új böngészőablakban.

    ![A képernyőképen a Rendszergazdai hitelesítő adatok engedélyezése gomb látható.](media/slack-provisioning-tutorial/authorization.png)


6. Az új ablakban jelentkezzen be a Slackbe a csapatrendszergazdai fiókjával. A megjelenő párbeszédpanelen válassza ki azt a Slack-csapatot, amelyhez engedélyezni szeretné az átadást, majd válassza az **Engedélyezés** lehetőséget. Miután végzett, lépjen vissza az Azure Portalra az átadás konfigurációjának befejezéséhez.

    ![Engedélyezési párbeszédpanel](./media/slack-provisioning-tutorial/slackauthorize.png)

7. Az Azure Portalon kattintson a **Kapcsolat tesztelése** lehetőségre annak ellenőrzéséhez, hogy az Azure AD tud-e csatlakozni a Slack alkalmazáshoz. Ha a kapcsolat meghiúsul, ellenőrizze, hogy a Slack-fiók csapatrendszergazdai engedélyekkel rendelkezik-e, majd próbálja megismételni az engedélyezési lépést.

8. Az **Értesítés e-mailben** mezőben adja meg annak a személynek vagy csoportnak az e-mail-címét, aki az átadással kapcsolatos hibaüzeneteket kapja, és jelölje be az **E-mail-értesítés küldése hiba esetén** jelölőnégyzetet.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

9. Kattintson a **Mentés** gombra.

10. A Leképezések szakaszban válassza az **Azure Active Directory-felhasználók szinkronizálása a Slackkel** lehetőséget.

11. Az **Attribútumleképezések** szakaszban tekintse át az Azure AD-ből a Slackbe szinkronizálni kívánt felhasználói attribútumokat. Vegye figyelembe, hogy a rendszer az **Egyező** tulajdonságokként kiválasztott attribútumokkal egyezteti a Slackben a felhasználói fiókokat a frissítési műveletekhez. A módosítások véglegesítéséhez válassza a Mentés gombot.

   |Attribútum|Típus|
   |---|---|
   |active|Logikai|
   |externalId|Sztring|
   |displayName|Sztring|
   |name.familyName|Sztring|
   |name.givenName|Sztring|
   |cím|Sztring|
   |emails[type eq "work"].value|Sztring|
   |userName (Felhasználónév)|Sztring|
   |nickName|Sztring|
   |addresses[type eq "untyped"].streetAddress|Sztring|
   |addresses[type eq "untyped"].locality|Sztring|
   |addresses[type eq "untyped"].region|Sztring|
   |addresses[type eq "untyped"].postalCode|Sztring|
   |addresses[type eq "untyped"].country|Sztring|
   |phoneNumbers[type eq "mobile"].value|Sztring|
   |phoneNumbers[type eq "work"].value|Sztring|
   |roles[primary eq "True"].value|Sztring|
   |területi beállítás|Sztring|
   |name.honorificPrefix|Sztring|
   |photos[type eq "photo"].value|Sztring|
   |profileUrl|Sztring|
   |timezone|Sztring|
   |userType|Sztring|
   |urn:scim:schemas:extension:enterprise:1.0.department|Sztring|
   |urn:scim:schemas:extension:enterprise:1.0.manager|Referencia|
   |urn:scim:schemas:extension:enterprise:1.0.employeeNumber|Sztring|
   |urn:scim:schemas:extension:enterprise:1.0.costCenter|Sztring|
   |urn:scim:schemas:extension:enterprise:1.0.organization|Sztring|
   |urn:scim:schemas:extension:enterprise:1.0.division|Sztring|

12. A **Leképezések** szakaszban válassza az **Azure Active Directory-csoportok szinkronizálása a Slackkel** lehetőséget.

13. Az **Attribútumleképezések** szakaszban tekintse át az Azure AD-ből a Slackbe szinkronizálni kívánt csoportattribútumokat. Vegye figyelembe, hogy a rendszer az **Egyező** tulajdonságokként kiválasztott attribútumokkal egyezteti a Slackben a csoportokat a frissítési műveletekhez. A módosítások véglegesítéséhez válassza a Mentés gombot.

      |Attribútum|Típus|
      |---|---|
      |displayName|Sztring|
      |tagok|Referencia|

14. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

15. Ha engedélyezni szeretné az Azure AD átadási szolgáltatását a Slackhez, a **Beállítások** szakaszban módosítsa a **Kiépítési állapot** beállítást **Be** értékre.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

16. A **Beállítások** szakasz **Hatókör** területén a kívánt értékek kiválasztásával határozza meg azokat a felhasználókat és/vagy csoportokat, amelyeket át szeretne adni a Slackbe.

    ![Átadási hatókör](common/provisioning-scope.png)

17. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet a **Beállítások** szakasz **Hatókör** területén meghatározott összes felhasználó és csoport kezdeti szinkronizálási ciklusát elindítja. A kezdeti ciklus elvégzése hosszabb időt vesz igénybe, mint a későbbi ciklusok, amelyek az Azure AD átadási szolgáltatásának futtatása során körülbelül 40 percenként lesznek végrehajtva. 

## <a name="step-5-monitor-your-deployment"></a>5. lépés Az üzemelő példány figyelése
Az átadás konfigurálása után a következő erőforrásokkal monitorozhatja az üzemelő példányt:

1. Az [átadási naplókkal](../reports-monitoring/concept-provisioning-logs.md) határozhatja meg, hogy mely felhasználók átadása sikeres, és melyeké sikertelen.
2. A [folyamatjelzőn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) láthatja az átadási ciklus állapotát és azt, hogy mennyi hiányzik még a befejeződéséhez.
3. Ha úgy tűnik, hogy az átadási konfiguráció állapota nem megfelelő, az alkalmazás karanténba kerül. A karanténállapotokról [itt](../app-provisioning/application-provisioning-quarantine-status.md) találhat további információt.

## <a name="troubleshooting-tips"></a>Hibaelhárítási tippek

* A Slack **displayName** attribútumának konfigurálásakor ügyeljen a következő viselkedésekre:

  * Az értékek nem teljesen egyediek (például 2 felhasználónak ugyanaz lehet a megjelenített neve).

  * Támogatja a nem angol karakterek, a szóközök és a nagybetűk használatát. 
  
  * Az engedélyezett írásjelek közé tartozik a pont, az aláhúzás, a kötőjel, az aposztróf, a zárójel (például **( [ { } ] )** ) és az elválasztók (például **, / ;** ).
  
  * A displayName tulajdonságban nem szerepelhet @ karakter. Ha @ karakter szerepel benne, kihagyott esemény jelenhet meg az átadási naplókban az AttributeValidationFailed leírással.

  * Csak akkor frissül, ha ez a két beállítás konfigurálva van a Slack munkahelyén/szervezetében – **Profil szinkronizálása engedélyezve** és **A felhasználók nem módosíthatják a megjelenített nevüket**.

* A Slack **userName** attribútuma 21-nél kevesebb karakterből állhat, és egyedi értékűnek kell lennie.

* A Slack csak a **userName** és az **email** attribútummal való egyeztetést engedélyezi.  
  
* A gyakori hibakódok a Slack hivatalos dokumentációjában találhatóak meg – https://api.slack.com/scim#errors

## <a name="change-log"></a>Változási napló

* 2020. 06. 16. – A módosított DisplayName attribútum csak új felhasználó létrehozásakor frissíthető.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)