---
title: 'Oktatóanyag: a AlertMedia konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és kiépíteni felhasználói fiókjait az Azure AD-ből a AlertMedia.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: a5df0dd7-05a3-4744-9d51-ec33e89a934f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/15/2020
ms.author: Zhchia
ms.openlocfilehash: b15b20ee8e643dc260578d17913810aaf68cc929
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181095"
---
# <a name="tutorial-configure-alertmedia-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés AlertMedia konfigurálása

Ez az oktatóanyag azokat a lépéseket ismerteti, amelyeket a AlertMedia és a Azure Active Directory (Azure AD) szolgáltatásban kell végrehajtania az automatikus felhasználó-kiépítés konfigurálásához. Ha konfigurálva van, az Azure AD automatikusan kiépíti és kiosztja a felhasználókat és csoportokat az Azure AD kiépítési szolgáltatás [AlertMedia](https://www.alertmedia.com/) . A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a AlertMedia-ben
> * Felhasználók eltávolítása a AlertMedia-ben, ha már nincs szükség hozzáférésre
> * Felhasználói attribútumok szinkronizálása az Azure AD és a AlertMedia között
> * Csoportok és csoporttagságok kiépítése a AlertMedia-ben
> * [Egyszeri bejelentkezés](./alertmedia-tutorial.md) a AlertMedia-be (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* Egy [Azure ad-bérlő](../develop/quickstart-create-new-tenant.md).
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](../roles/permissions-reference.md) a kiépítés konfigurálására (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda, alkalmazás tulajdonosa vagy globális rendszergazda). 
* Egy [AlertMedia-bérlő](https://dashboard.alertmedia.com/#/login).
* A AlertMedia felhasználói fiókja rendszergazdai jogosultságokkal rendelkezik az API-integráció konfigurálásához.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés Az átadás üzembe helyezésének megtervezése
1. Ismerje meg [az átadási szolgáltatás működését](../app-provisioning/user-provisioning.md).
2. Határozza meg, hogy ki lesz [az átadás hatókörében](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Határozza meg, hogy az [Azure ad és a AlertMedia között milyen adatleképezést kell leképezni](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-alertmedia-to-support-provisioning-with-azure-ad"></a>2. lépés AlertMedia konfigurálása az Azure AD-vel való kiépítés támogatásához

1. Jelentkezzen be a AlertMedia-fiókjába. Navigáljon a **vállalati > API**-hoz.
2. Kattintson az **új hozzáadása** gombra.
3. Válassza ki, hogy az **API-integráció** egy nevet adjon, hogy könnyebben felismerhető legyen a kulcsok használatának helye.
4. Válassza ki azt a rendszergazdát, amelyhez hozzá szeretné rendelni az integrációt.
5. Kattintson a **kulcsok generálása** és **Mentés** gombra.
6. Másolja és mentse az **ügyfél tokenjét** az integrációból. Ezt a rendszer a AlertMedia alkalmazás üzembe helyezés lapján található **titkos jogkivonatként** használja a Azure Portal.


## <a name="step-3-add-alertmedia-from-the-azure-ad-application-gallery"></a>3. lépés AlertMedia hozzáadása az Azure AD Application Galleryből

Vegyen fel AlertMedia az Azure AD-alkalmazás-katalógusból a AlertMedia való kiépítés kezelésének megkezdéséhez. Ha korábban már beállította a AlertMedia-t az egyszeri bejelentkezéshez, használhatja ugyanazt az alkalmazást. Az integráció első tesztelésekor azonban érdemes létrehozni egy külön alkalmazást. Az alkalmazások katalógusból való hozzáadásáról [itt](../manage-apps/add-application-portal.md) tudhat meg többet. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Az átadás hatókörében lévő személyek meghatározása 

Az Azure AD átadási szolgáltatása lehetővé teszi az átadott személyek hatókörének meghatározását az alkalmazáshoz való hozzárendelés és/vagy a felhasználó/csoport attribútumai alapján. Ha a hozzárendelés alapján történő hatókör-meghatározást választja, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet felhasználókat és csoportokat az alkalmazáshoz. Ha csak a felhasználó vagy csoport attribútumai alapján történő hatókörmeghatározást választja, az [itt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) leírt hatókörszűrőt használhatja. 

* Felhasználók és csoportok AlertMedia való hozzárendeléséhez ki kell választania az **alapértelmezett hozzáféréstől** eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva az átadásból, és az átadási naplókban nem jogosultként lesznek megjelölve. Ha az alkalmazáshoz csak az alapértelmezett hozzáférési szerepkör érhető el, akkor további szerepkörök felvételéhez [frissítheti az alkalmazásjegyzéket](../develop/howto-add-app-roles-in-azure-ad-apps.md). 

* Kezdje kicsiben. Tesztelje a felhasználók és csoportok kis halmazát, mielőtt mindenkire kiterjesztené. Amikor az átadás hatóköre a hozzárendelt felhasználókra és csoportokra van beállítva, ennek szabályozásához egy vagy két felhasználót vagy csoportot rendelhet az alkalmazáshoz. Amikor a hatókör az összes felhasználóra és csoportra van beállítva, meghatározhat egy [attribútumalapú hatókörszűrőt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-alertmedia"></a>5. lépés Automatikus felhasználó-kiépítés beállítása a AlertMedia 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy TestApp alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

### <a name="to-configure-automatic-user-provisioning-for-alertmedia-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a AlertMedia az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **AlertMedia** lehetőséget.

    ![Az AlertMedia hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Kiépítés lap automatikus](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a AlertMedia **bérlői URL-címét** az alábbiak egyikének megfelelően.
      * (nincs egyéni tartomány) `https://dashboard.alertmedia.com/api/scim/v3`

      * (egyéni tartomány) `https://subdomain.alertmedia.com/api/scim/v3`

      Adja meg a **titkos tokent** a 2. lépésben korábban lekért módon. Kattintson a **kapcsolat tesztelése** lehetőségre, hogy az Azure ad képes legyen csatlakozni a AlertMedia. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a AlertMedia-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

      ![Jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az **Értesítés e-mailben** mezőben adja meg annak a személynek vagy csoportnak az e-mail-címét, aki az átadással kapcsolatos hibaüzeneteket kapja, és jelölje be az **E-mail-értesítés küldése hiba esetén** jelölőnégyzetet.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a AlertMedia** lehetőséget.

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban lévő AlertMedia. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a AlertMedia felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. Ha úgy dönt, hogy módosítja a [megfelelő cél attribútumot](../app-provisioning/customize-application-attributes.md), akkor biztosítania kell, hogy a AlertMedia API támogassa a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   |Attribútum|Típus|
   |---|---|
   |userName (Felhasználónév)|Sztring|
   |active|Logikai|
   |urn: IETF: params: scim: sémák: bővítmény: alertmedia: 2.0: CustomAttribute: User: first_name|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: alertmedia: 2.0: CustomAttribute: User: last_name|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: alertmedia: 2.0: CustomAttribute: User: e-mail|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: alertmedia: 2.0: CustomAttribute: felhasználó: email2|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: alertmedia: 2.0: CustomAttribute: felhasználó: email3|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: alertmedia: 2.0: CustomAttribute: User: title|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: alertmedia: 2.0: CustomAttribute: User: mobile_phone|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: alertmedia: 2.0: CustomAttribute: User: mobile_phone_post_dial|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: alertmedia: 2.0: CustomAttribute: User: mobile_phone2|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: alertmedia: 2.0: CustomAttribute: User: mobile_phone2_post_dial|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: alertmedia: 2.0: CustomAttribute: User: mobile_phone3|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: alertmedia: 2.0: CustomAttribute: User: mobile_phone3_post_dial|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: alertmedia: 2.0: CustomAttribute: User: home_phone|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: alertmedia: 2.0: CustomAttribute: User: home_phone_post_dial|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: alertmedia: 2.0: CustomAttribute: User: office_phone|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: alertmedia: 2.0: CustomAttribute: User: office_phone_post_dial|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: alertmedia: 2.0: CustomAttribute: User: címe|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: alertmedia: 2.0: CustomAttribute: felhasználó: Cím2|Sztring|
   |urn: IETF: params: scim: sémák: kiterjesztés: alertmedia: 2.0: CustomAttribute: felhasználó: város|Sztring|
   |urn: IETF: params: scim: sémák: kiterjesztés: alertmedia: 2.0: CustomAttribute: felhasználó: állapot|Sztring|
   |urn: IETF: params: scim: sémák: kiterjesztés: alertmedia: 2.0: CustomAttribute: felhasználó: ország|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: alertmedia: 2.0: CustomAttribute: User: irányítószám|Sztring|
   |urn: IETF: params: scim: sémák: kiterjesztés: alertmedia: 2.0: CustomAttribute: felhasználó: megjegyzések|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: alertmedia: 2.0: CustomAttribute: User: customer_user_id|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: alertmedia: 2.0: CustomAttribute: User: user_type|Sztring|

10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása a AlertMedia** lehetőséget.

11. Tekintse át az Azure AD-ből szinkronizált AlertMedia az **attribútum-hozzárendelés** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a AlertMedia tartozó csoportok egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

      |Attribútum|Típus|
      |---|---|
      |displayName|Sztring|
      |tagok|Referencia|

12. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

13. Az Azure AD-kiépítési szolgáltatás AlertMedia való engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg a AlertMediaAlertMedia kiépíteni kívánt felhasználókat és/vagy csoportokat a **Settings (beállítások** ) szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Átadási hatókör](common/provisioning-scope.png)

15. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet a **Beállítások** szakasz **Hatókör** területén meghatározott összes felhasználó és csoport kezdeti szinkronizálási ciklusát elindítja. A kezdeti ciklus elvégzése hosszabb időt vesz igénybe, mint a későbbi ciklusok, amelyek az Azure AD átadási szolgáltatásának futtatása során körülbelül 40 percenként lesznek végrehajtva. 

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Az átadás konfigurálása után a következő erőforrásokkal monitorozhatja az üzemelő példányt:

1. Az [átadási naplókkal](../reports-monitoring/concept-provisioning-logs.md) határozhatja meg, hogy mely felhasználók átadása sikeres, és melyeké sikertelen.
2. A [folyamatjelzőn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) láthatja az átadási ciklus állapotát és azt, hogy mennyi hiányzik még a befejeződéséhez.
3. Ha úgy tűnik, hogy az átadási konfiguráció állapota nem megfelelő, az alkalmazás karanténba kerül. A karanténállapotokról [itt](../app-provisioning/application-provisioning-quarantine-status.md) találhat további információt.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)