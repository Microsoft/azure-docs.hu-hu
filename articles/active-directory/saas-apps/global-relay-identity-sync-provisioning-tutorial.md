---
title: 'Oktatóanyag: a globális továbbító identitás-szinkronizálásának konfigurálása a felhasználók automatikus kiépítéséhez Azure Active Directorysal | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és kiépíteni felhasználói fiókjait az Azure AD-ből a globális Relay Identity Sync szolgáltatásba.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 0c4a3bf0-d0a6-4eab-909b-6cf9f9234e4c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/22/2020
ms.author: Zhchia
ms.openlocfilehash: 400e82780abd08e0db4f49d72b352e290ea1f212
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2020
ms.locfileid: "92900295"
---
# <a name="tutorial-configure-global-relay-identity-sync-for-automatic-user-provisioning"></a>Oktatóanyag: a globális továbbító identitás-szinkronizálásának konfigurálása a felhasználók automatikus kiépítéséhez

Ez az oktatóanyag azokat a lépéseket ismerteti, amelyeket a globális továbbító identitás-szinkronizálásban és Azure Active Directory (Azure AD) a felhasználók automatikus kiépítésének konfigurálásához kell végrehajtania. Ha konfigurálva van, az Azure AD automatikusan kiépíti a felhasználókat és csoportokat a globális továbbító identitás-szinkronizálásra az Azure AD-létesítési szolgáltatás használatával. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a globális Relay Identity Sync szolgáltatásban
> * A globális Relay Identity Sync felhasználóinak eltávolítása, ha már nincs szükség hozzáférésre
> * A felhasználói attribútumok szinkronizálása az Azure AD és a globális továbbító identitás-szinkronizálás között
> * Csoportok és csoporttagságok kiépítése a globális Relay Identity Sync szolgáltatásban

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Azure AD-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) a kiépítés konfigurálására (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda, alkalmazás tulajdonosa vagy globális rendszergazda).

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés Az átadás üzembe helyezésének megtervezése
1. Ismerje meg [az átadási szolgáltatás működését](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Határozza meg, hogy ki lesz [az átadás hatókörében](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Határozza meg, hogy milyen adatokra van szükség az [Azure ad és a globális továbbító identitás-szinkronizálás között](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-global-relay-identity-sync-to-support-provisioning-with-azure-ad"></a>2. lépés A globális továbbító identitás-szinkronizálásának konfigurálása az Azure AD-vel való kiépítés támogatásához

A bérlői URL-cím fogadásához forduljon a globális továbbító Identity Sync-képviselőjéhez. Ez az érték jelenik meg a **bérlői URL-cím** mezőben a globális Relay Identity Sync alkalmazás üzembe helyezés lapján a Azure Portal.

## <a name="step-3-add-global-relay-identity-sync-from-the-azure-ad-application-gallery"></a>3. lépés Globális Relay Identity Sync hozzáadása az Azure AD Application Galleryből

A globális Relay Identity Sync hozzáadásával megkezdheti az Azure AD alkalmazás-katalógusból való kiépítés kezelését. További információ az alkalmazások a katalógusból való hozzáadásáról [.](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Az átadás hatókörében lévő személyek meghatározása 

Az Azure AD átadási szolgáltatása lehetővé teszi az átadott személyek hatókörének meghatározását az alkalmazáshoz való hozzárendelés és/vagy a felhasználó/csoport attribútumai alapján. Ha a hozzárendelés alapján történő hatókör-meghatározást választja, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet felhasználókat és csoportokat az alkalmazáshoz. Ha csak a felhasználó vagy csoport attribútumai alapján történő hatókörmeghatározást választja, az [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) leírt hatókörszűrőt használhatja. 

* Kezdje kicsiben. Tesztelje a felhasználók és csoportok kis halmazát, mielőtt mindenkire kiterjesztené. Amikor az átadás hatóköre a hozzárendelt felhasználókra és csoportokra van beállítva, ennek szabályozásához egy vagy két felhasználót vagy csoportot rendelhet az alkalmazáshoz. Amikor a hatókör az összes felhasználóra és csoportra van beállítva, meghatározhat egy [attribútumalapú hatókörszűrőt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-global-relay-identity-sync"></a>5. lépés Automatikus felhasználó-kiépítés beállítása a globális továbbító identitás-szinkronizálásra 

Ez a szakasz végigvezeti az Azure AD-létesítési szolgáltatás konfigurálásának lépésein, hogy az Azure ad-ben felhasználói és/vagy csoportos hozzárendelések alapján hozzon létre, frissítsen és tiltsa le a globális továbbító identitás-szinkronizáló alkalmazásban lévő felhasználókat és/vagy csoportokat.

### <a name="to-configure-automatic-user-provisioning-for-global-relay-identity-sync-in-azure-ad"></a>Az Azure AD-beli globális továbbító identitás-szinkronizálás automatikus felhasználó általi üzembe helyezésének konfigurálása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **globális továbbító identitás-szinkronizálás** lehetőséget.

    ![A globális Relay Identity Sync hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Kiépítés lap automatikus](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a globális továbbító identitás-szinkronizálási **bérlői URL-címét** . Kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad csatlakozhasson a globális Relay Identity Sync szolgáltatáshoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a globális Relay Identity Sync-fiók rendelkezik rendszergazdai engedélyekkel, és a probléma megoldásához lépjen kapcsolatba a globális továbbító képviselőjével.

    ![Engedélyezés gomb](media/global-relay-identity-sync-provisioning-tutorial/authorization.png)

6. Az **Értesítés e-mailben** mezőben adja meg annak a személynek vagy csoportnak az e-mail-címét, aki az átadással kapcsolatos hibaüzeneteket kapja, és jelölje be az **E-mail-értesítés küldése hiba esetén** jelölőnégyzetet.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a globális továbbító identitás-szinkronizáláshoz** lehetőséget.

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban található, a globális Relay Identity Sync szolgáltatásban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a globális továbbító Identity Sync felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. Ha úgy dönt, hogy megváltoztatja a [megfelelő cél attribútumot](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), akkor biztosítania kell, hogy a globális továbbító Identity Sync API támogassa a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   |Attribútum|Típus|
   |---|---|
   |userName (Felhasználónév)|Sztring|
   |active|Logikai|
   |displayName|Sztring|
   |cím|Sztring|
   |preferredLanguage|Sztring|
   |name.givenName|Sztring|
   |name.familyName|Sztring|
   |név. formázott|Sztring|
   |címek [type EQ "work"]. formázott|Sztring|
   |címek [type EQ "work"]. streetAddress|Sztring|
   |emails[type eq "work"].value|Sztring|
   |címek [típus EQ "work"]. helység|Sztring|
   |címek [típus EQ "work"]. régió|Sztring|
   |címek [type EQ "work"]. irányítószám|Sztring|
   |címek [type EQ "work"]. Country|Sztring|
   |címek [type EQ "other"]. formázott|Sztring|
   |phoneNumbers[type eq "work"].value|Sztring|
   |phoneNumbers[type eq "mobile"].value|Sztring|
   |phoneNumbers [type EQ "fax"]. Value|Sztring|
   |externalId|Sztring|
   |name.honorificPrefix|Sztring|
   |név. honorificSuffix|Sztring|
   |nickName|Sztring|
   |userType|Sztring|
   |területi beállítás|Sztring|
   |timezone|Sztring|
   |e-mailek [type EQ "Home"]. Value|Sztring|
   |e-mailek [type EQ "other"]. Value|Sztring|
   |phoneNumbers [type EQ "Home"]. Value|Sztring|
   |phoneNumbers [type EQ "other"]. Value|Sztring|
   |phoneNumbers [type EQ "pager"]. Value|Sztring|
   |címek [type EQ "Home"]. streetAddress|Sztring|
   |címek [type EQ "Home"]. helység|Sztring|
   |címek [type EQ "Home"]. régió|Sztring|
   |címek [type EQ "Home"]. irányítószám|Sztring|
   |címek [type EQ "Home"]. Country|Sztring|
   |címek [type EQ "Home"]. formázott|Sztring|
   |címek [type EQ "other"]. streetAddress|Sztring|
   |címek [type EQ "other"]. helység|Sztring|
   |címek [type EQ "other"]. régió|Sztring|
   |címek [type EQ "other"]. irányítószám|Sztring|
   |címek [type EQ "other"]. Country|Sztring|
   |szerepkörök [elsődleges EQ "true"]. megjelenítés|Sztring|
   |szerepkörök [elsődleges EQ "true"]. Type|Sztring|
   |roles[primary eq "True"].value|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: employeeNumber|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: costCenter|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: szervezet|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: User: Division|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: részleg|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: User: Manager|Referencia|
   |urn: IETF: params: scim: sémák: bővítmény: GlobalRelay: 2.0: felhasználó: proxyAddresses|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: GlobalRelay: 2.0: felhasználó: extensionAttribute1|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: GlobalRelay: 2.0: felhasználó: extensionAttribute2|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: GlobalRelay: 2.0: felhasználó: extensionAttribute3|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: GlobalRelay: 2.0: felhasználó: extensionAttribute4|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: GlobalRelay: 2.0: felhasználó: extensionAttribute5|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: GlobalRelay: 2.0: felhasználó: extensionAttribute6|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: GlobalRelay: 2.0: felhasználó: extensionAttribute7|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: GlobalRelay: 2.0: felhasználó: extensionAttribute8|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: GlobalRelay: 2.0: felhasználó: extensionAttribute9|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: GlobalRelay: 2.0: felhasználó: extensionAttribute10|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: GlobalRelay: 2.0: felhasználó: extensionAttribute11|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: GlobalRelay: 2.0: felhasználó: extensionAttribute12|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: GlobalRelay: 2.0: felhasználó: extensionAttribute13|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: GlobalRelay: 2.0: felhasználó: extensionAttribute14|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: GlobalRelay: 2.0: felhasználó: extensionAttribute15|Sztring|



10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása a globális továbbító identitás-szinkronizáláshoz** lehetőséget.

11. Tekintse át az Azure AD-ből szinkronizált csoport-attribútumokat az **attribútum-hozzárendelési** szakaszban található, a globális Relay Identity Sync szolgáltatásban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a globális továbbító identitás-szinkronizálásban található csoportokkal egyeznek a frissítési műveletek során. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

      |Attribútum|Típus|
      |---|---|
      |displayName|Sztring|
      |tagok|Referencia|

12. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

13. Ha engedélyezni szeretné az Azure AD-kiépítési szolgáltatást a globális továbbító identitás-szinkronizáláshoz, módosítsa a **kiépítési állapotot** **a következőre** a **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg azokat a felhasználókat és/vagy csoportokat, akik számára a globális továbbító identitás-szinkronizálást szeretné kiépíteni, ehhez válassza ki a kívánt értékeket a **hatókörben** a **Beállítások** szakaszban.

    ![Átadási hatókör](common/provisioning-scope.png)

15. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet a **Beállítások** szakasz **Hatókör** területén meghatározott összes felhasználó és csoport kezdeti szinkronizálási ciklusát elindítja. A kezdeti ciklus elvégzése hosszabb időt vesz igénybe, mint a későbbi ciklusok, amelyek az Azure AD átadási szolgáltatásának futtatása során körülbelül 40 percenként lesznek végrehajtva. 

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Az átadás konfigurálása után a következő erőforrásokkal monitorozhatja az üzemelő példányt:

1. Az [átadási naplókkal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) határozhatja meg, hogy mely felhasználók átadása sikeres, és melyeké sikertelen.
2. A [folyamatjelzőn](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) láthatja az átadási ciklus állapotát és azt, hogy mennyi hiányzik még a befejeződéséhez.
3. Ha úgy tűnik, hogy az átadási konfiguráció állapota nem megfelelő, az alkalmazás karanténba kerül. A karanténállapotokról [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status) találhat további információt.  

## <a name="additional-resources"></a>További erőforrások

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../manage-apps/check-status-user-account-provisioning.md)
