---
title: 'Oktatóanyag: a HootSuite konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és kiépíteni felhasználói fiókjait az Azure AD-ből a HootSuite.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/15/2020
ms.author: Zhchia
ms.openlocfilehash: 011071c9aa1722b8f1cf81ec8d9ef88e8d91ea03
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2020
ms.locfileid: "94359051"
---
# <a name="tutorial-configure-hootsuite-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés HootSuite konfigurálása

Ez az oktatóanyag azokat a lépéseket ismerteti, amelyeket a HootSuite és a Azure Active Directory (Azure AD) szolgáltatásban kell végrehajtania az automatikus felhasználó-kiépítés konfigurálásához. Ha konfigurálva van, az Azure AD automatikusan kiépíti és kiosztja a felhasználókat és csoportokat az Azure AD kiépítési szolgáltatás [HootSuite](https://hootsuite.com/) . A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a HootSuite-ben
> * Felhasználók eltávolítása a HootSuite-ben, ha már nincs szükség hozzáférésre
> * Felhasználói attribútumok szinkronizálása az Azure AD és a HootSuite között
> * Csoportok és csoporttagságok kiépítése a HootSuite-ben
> * [Egyszeri bejelentkezés](./hootsuite-tutorial.md) a HootSuite-be (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Azure AD-bérlő](../develop/quickstart-create-new-tenant.md) 
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](../users-groups-roles/directory-assign-admin-roles.md) a kiépítés konfigurálására (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda, alkalmazás tulajdonosa vagy globális rendszergazda). 
* Olyan [HootSuite](http://www.hootsuite.com/) rendelkező felhasználói fiók, amely a szervezeten belüli jogosultságokat **felügyeli** .

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés Az átadás üzembe helyezésének megtervezése
1. Ismerje meg [az átadási szolgáltatás működését](../app-provisioning/user-provisioning.md).
2. Határozza meg, hogy ki lesz [az átadás hatókörében](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Határozza meg, hogy az [Azure ad és a HootSuite között milyen adatleképezést kell leképezni](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-hootsuite-to-support-provisioning-with-azure-ad"></a>2. lépés HootSuite konfigurálása az Azure AD-vel való kiépítés támogatásához

A dev.support@hootsuite.com későbbi lépésekben szükséges tartós titkos jogkivonat beszerzése. 

## <a name="step-3-add-hootsuite-from-the-azure-ad-application-gallery"></a>3. lépés Hootsuite hozzáadása az Azure AD Application Galleryből

Vegyen fel HootSuite az Azure AD-alkalmazás-katalógusból a HootSuite való kiépítés kezelésének megkezdéséhez. Ha korábban már beállította a HootSuite-t az egyszeri bejelentkezéshez, használhatja ugyanazt az alkalmazást. Az integráció első tesztelésekor azonban érdemes létrehozni egy külön alkalmazást. Az alkalmazások katalógusból való hozzáadásáról [itt](../manage-apps/add-application-portal.md) tudhat meg többet. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Az átadás hatókörében lévő személyek meghatározása 

Az Azure AD átadási szolgáltatása lehetővé teszi az átadott személyek hatókörének meghatározását az alkalmazáshoz való hozzárendelés és/vagy a felhasználó/csoport attribútumai alapján. Ha a hozzárendelés alapján történő hatókör-meghatározást választja, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet felhasználókat és csoportokat az alkalmazáshoz. Ha csak a felhasználó vagy csoport attribútumai alapján történő hatókörmeghatározást választja, az [itt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) leírt hatókörszűrőt használhatja. 

* Felhasználók és csoportok HootSuite való hozzárendeléséhez ki kell választania az **alapértelmezett hozzáféréstől** eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva az átadásból, és az átadási naplókban nem jogosultként lesznek megjelölve. Ha az alkalmazáshoz csak az alapértelmezett hozzáférési szerepkör érhető el, akkor további szerepkörök felvételéhez [frissítheti az alkalmazásjegyzéket](../develop/howto-add-app-roles-in-azure-ad-apps.md). 

* Kezdje kicsiben. Tesztelje a felhasználók és csoportok kis halmazát, mielőtt mindenkire kiterjesztené. Amikor az átadás hatóköre a hozzárendelt felhasználókra és csoportokra van beállítva, ennek szabályozásához egy vagy két felhasználót vagy csoportot rendelhet az alkalmazáshoz. Amikor a hatókör az összes felhasználóra és csoportra van beállítva, meghatározhat egy [attribútumalapú hatókörszűrőt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-hootsuite"></a>5. lépés Automatikus felhasználó-kiépítés beállítása a HootSuite 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy TestApp alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

### <a name="to-configure-automatic-user-provisioning-for-hootsuite-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a HootSuite az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](./media/hootsuite-provisioning-tutorial/enterprise-applications.png)

    ![Minden alkalmazás panel](./media/hootsuite-provisioning-tutorial/all-applications.png)

2. Az alkalmazások listában válassza a **HootSuite** lehetőséget.

    ![Az HootSuite hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza ki a **kiépítés** lapot. Kattintson az első **lépések** elemre.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

    ![Első lépések panel](./media/hootsuite-provisioning-tutorial/get-started.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a kiépítési mód legördülő listájáról az automatikus lehetőséggel.](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja `https://platform.hootsuite.com/scim/v2` meg a bérlői URL-címet. Adja meg a korábban a **2. lépésben** lekért tartós titkos jogkivonat értékét. Kattintson a **kapcsolat tesztelése** lehetőségre, hogy az Azure ad képes legyen csatlakozni a HootSuite. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a HootSuite-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Képernyőfelvétel: a rendszergazdai hitelesítő adatok párbeszédpanel, ahol megadhatja a bérlő U R L-t és a titkos jogkivonatot.](./media/hootsuite-provisioning-tutorial/provisioning.png)

6. Az **Értesítés e-mailben** mezőben adja meg annak a személynek vagy csoportnak az e-mail-címét, aki az átadással kapcsolatos hibaüzeneteket kapja, és jelölje be az **E-mail-értesítés küldése hiba esetén** jelölőnégyzetet.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók kiépítése** lehetőséget.

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban lévő HootSuite. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a HootSuite felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. Ha úgy dönt, hogy módosítja a [megfelelő cél attribútumot](../app-provisioning/customize-application-attributes.md), akkor biztosítania kell, hogy a HootSuite API támogassa a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   |Attribútum|Típus|
   |---|---|
   |userName (Felhasználónév)|Sztring|
   |emails[type eq "work"].value|Sztring|
   |active|Logikai|
   |displayName|Sztring|
   |preferredLanguage|Sztring|
   |timezone|Sztring|
   |name.givenName|Sztring|
   |name.familyName|Sztring|

10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása** lehetőséget.

11. Tekintse át az Azure AD-ből szinkronizált HootSuite az **attribútum-hozzárendelés** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a HootSuite tartozó csoportok egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

      |Attribútum|Típus|
      |---|---|
      |displayName|Sztring|
      |externalId|Sztring|
      |tagok|Referencia|

12. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

13. Az Azure AD-kiépítési szolgáltatás HootSuite való engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg a HootSuite kiépíteni kívánt felhasználókat és/vagy csoportokat a **Settings (beállítások** ) szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Átadási hatókör](common/provisioning-scope.png)

15. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet a **Beállítások** szakasz **Hatókör** területén meghatározott összes felhasználó és csoport kezdeti szinkronizálási ciklusát elindítja. A kezdeti ciklus elvégzése hosszabb időt vesz igénybe, mint a későbbi ciklusok, amelyek az Azure AD átadási szolgáltatásának futtatása során körülbelül 40 percenként lesznek végrehajtva. 

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Az átadás konfigurálása után a következő erőforrásokkal monitorozhatja az üzemelő példányt:

* Az [átadási naplókkal](../reports-monitoring/concept-provisioning-logs.md) határozhatja meg, hogy mely felhasználók átadása sikeres, és melyeké sikertelen.
* A [folyamatjelzőn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) láthatja az átadási ciklus állapotát és azt, hogy mennyi hiányzik még a befejeződéséhez.
* Ha úgy tűnik, hogy az átadási konfiguráció állapota nem megfelelő, az alkalmazás karanténba kerül. A karanténállapotokról [itt](../app-provisioning/application-provisioning-quarantine-status.md) találhat további információt.  

## <a name="change-log"></a>Változási napló

* 10/22/2020 – a következő felhasználói attribútumok támogatása: "name. givenName" és "name. familyName". A felhasználók számára a "organizationIds" és a "teamIds" egyéni kiterjesztési attribútumok el lettek távolítva.
A "displayName", "Members" és "externalId" csoportosítási attribútumok támogatása.

## <a name="additional-resources"></a>További erőforrások

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)
