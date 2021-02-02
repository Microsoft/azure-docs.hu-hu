---
title: 'Oktatóanyag: a HelloID konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és kiépíteni felhasználói fiókjait az Azure AD-ből a HelloID.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: ffd450a5-03ec-4364-8921-5c468e119c4d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2021
ms.author: Zhchia
ms.openlocfilehash: c7fd6faa2b35633d530fb3ea98a16f83e3f92a87
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99431212"
---
# <a name="tutorial-configure-helloid-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés HelloID konfigurálása

Ez az oktatóanyag azokat a lépéseket ismerteti, amelyeket a HelloID és a Azure Active Directory (Azure AD) szolgáltatásban kell végrehajtania az automatikus felhasználó-kiépítés konfigurálásához. Ha konfigurálva van, az Azure AD automatikusan kiépíti és kiosztja a felhasználókat és csoportokat az Azure AD kiépítési szolgáltatás [HelloID](https://www.helloid.com/) . A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a HelloID-ben
> * Felhasználók eltávolítása a HelloID-ben, ha már nincs szükség hozzáférésre
> * Felhasználói attribútumok szinkronizálása az Azure AD és a HelloID között
> * Csoportok és csoporttagságok kiépítése a HelloID-ben

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Azure AD-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) a kiépítés konfigurálására (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda, alkalmazás tulajdonosa vagy globális rendszergazda). 
* Egy [HelloID-bérlő](https://www.helloid.com/).
* Rendszergazdai jogosultságokkal rendelkező felhasználói fiók a HelloID-ben.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés Az átadás üzembe helyezésének megtervezése
1. Ismerje meg [az átadási szolgáltatás működését](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Határozza meg, hogy ki lesz [az átadás hatókörében](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Határozza meg, hogy az [Azure ad és a HelloID között milyen adatleképezést kell leképezni](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-helloid-to-support-provisioning-with-azure-ad"></a>2. lépés HelloID konfigurálása az Azure AD-vel való kiépítés támogatásához

1. Jelentkezzen be a HelloID rendszergazdai irányítópultján.

    ![HelloID-rendszergazdai bejelentkezés](media/helloid-provisioning-tutorial/admin-sign-in.png)

2. Nyissa meg a **címtár**  >  **Azure ad**-t.

    ![Directory > Azure AD](media/helloid-provisioning-tutorial/directory-azure-ad.png)

3. Kattintson az **új titok** gombra. 

    ![Új titkos gomb](media/helloid-provisioning-tutorial/new-secret.png)

4. Az **URL-cím** és a **titkos kulcs** mezők automatikusan fel lesznek töltve. Másolja és mentse az URL-címet és a titkos kulcsot. Ezek az értékek a **bérlői URL-cím** * és a **titkos jogkivonat** * mezőben lesznek megadva a HelloID alkalmazás kiépítés lapján a Azure Portalban.

    ![URL-cím és titkos kód létrehozva](media/helloid-provisioning-tutorial/url-secret.png)

## <a name="step-3-add-helloid-from-the-azure-ad-application-gallery"></a>3. lépés HelloID hozzáadása az Azure AD Application Galleryből

Vegyen fel HelloID az Azure AD-alkalmazás-katalógusból a HelloID való kiépítés kezelésének megkezdéséhez. Ha korábban már beállította a HelloID az SSO-hoz, használhatja ugyanazt az alkalmazást. Az integráció első tesztelésekor azonban érdemes létrehozni egy külön alkalmazást. Az alkalmazások katalógusból való hozzáadásáról [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) tudhat meg többet. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Az átadás hatókörében lévő személyek meghatározása 

Az Azure AD átadási szolgáltatása lehetővé teszi az átadott személyek hatókörének meghatározását az alkalmazáshoz való hozzárendelés és/vagy a felhasználó/csoport attribútumai alapján. Ha a hozzárendelés alapján történő hatókör-meghatározást választja, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet felhasználókat és csoportokat az alkalmazáshoz. Ha csak a felhasználó vagy csoport attribútumai alapján történő hatókörmeghatározást választja, az [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) leírt hatókörszűrőt használhatja. 

* Felhasználók és csoportok HelloID való hozzárendeléséhez ki kell választania az **alapértelmezett hozzáféréstől** eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva az átadásból, és az átadási naplókban nem jogosultként lesznek megjelölve. Ha az alkalmazáshoz csak az alapértelmezett hozzáférési szerepkör érhető el, akkor további szerepkörök felvételéhez [frissítheti az alkalmazásjegyzéket](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps). 

* Kezdje kicsiben. Tesztelje a felhasználók és csoportok kis halmazát, mielőtt mindenkire kiterjesztené. Amikor az átadás hatóköre a hozzárendelt felhasználókra és csoportokra van beállítva, ennek szabályozásához egy vagy két felhasználót vagy csoportot rendelhet az alkalmazáshoz. Amikor a hatókör az összes felhasználóra és csoportra van beállítva, meghatározhat egy [attribútumalapú hatókörszűrőt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-helloid"></a>5. lépés Automatikus felhasználó-kiépítés beállítása a HelloID 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy HelloID alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

### <a name="to-configure-automatic-user-provisioning-for-helloid-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a HelloID az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **HelloID** lehetőséget.

    ![Az HelloID hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Kiépítés lap automatikus](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a HelloID bérlői URL-címét és a titkos jogkivonatot. Kattintson a **kapcsolat tesztelése** lehetőségre, hogy az Azure ad képes legyen csatlakozni a HelloID. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a HelloID-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az **Értesítés e-mailben** mezőben adja meg annak a személynek vagy csoportnak az e-mail-címét, aki az átadással kapcsolatos hibaüzeneteket kapja, és jelölje be az **E-mail-értesítés küldése hiba esetén** jelölőnégyzetet.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a HelloID** lehetőséget.

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban lévő HelloID. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a HelloID felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. Ha úgy dönt, hogy módosítja a [megfelelő cél attribútumot](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), akkor biztosítania kell, hogy a HelloID API támogassa a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   |Attribútum|Típus|Szűréshez támogatott|
   |---|---|---|
   |userName (Felhasználónév)|Sztring|&check;|
   |active|Logikai|
   |displayName|Sztring|
   |emails[type eq "work"].value|Sztring|
   |name.givenName|Sztring|
   |name.familyName|Sztring|
   |externalId|Sztring|

10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása a HelloID** lehetőséget.

11. Tekintse át az Azure AD-ből szinkronizált HelloID az **attribútum-hozzárendelés** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a HelloID tartozó csoportok egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

      |Attribútum|Típus|Szűréshez támogatott|
      |---|---|---|
      |displayName|Sztring|&check;|
      |externalId|Sztring|
      |tagok|Referencia|

12. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

13. Az Azure AD-kiépítési szolgáltatás HelloID való engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg a HelloID kiépíteni kívánt felhasználókat és/vagy csoportokat a **Settings (beállítások** ) szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Átadási hatókör](common/provisioning-scope.png)

15. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet a **Beállítások** szakasz **Hatókör** területén meghatározott összes felhasználó és csoport kezdeti szinkronizálási ciklusát elindítja. A kezdeti ciklus elvégzése hosszabb időt vesz igénybe, mint a későbbi ciklusok, amelyek az Azure AD átadási szolgáltatásának futtatása során körülbelül 40 percenként lesznek végrehajtva. 

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Az átadás konfigurálása után a következő erőforrásokkal monitorozhatja az üzemelő példányt:

* Az [átadási naplókkal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) határozhatja meg, hogy mely felhasználók átadása sikeres, és melyeké sikertelen.
* A [folyamatjelzőn](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) láthatja az átadási ciklus állapotát és azt, hogy mennyi hiányzik még a befejeződéséhez.
* Ha úgy tűnik, hogy az átadási konfiguráció állapota nem megfelelő, az alkalmazás karanténba kerül. A karanténállapotokról [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status) találhat további információt.  

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../manage-apps/check-status-user-account-provisioning.md)
