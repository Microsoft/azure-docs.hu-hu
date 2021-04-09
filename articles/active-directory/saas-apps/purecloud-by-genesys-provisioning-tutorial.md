---
title: 'Oktatóanyag: a PureCloud konfigurálása a Genesys-ben a felhasználók automatikus üzembe helyezéséhez a Azure Active Directory használatával | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és kiépíteni felhasználói fiókjait az Azure AD-ből a Genesys által PureCloud.
services: active-directory
author: Zhchia
writer: Zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2020
ms.author: Zhchia
ms.openlocfilehash: bbb9b47e42ce195a98801ee08d177efd409c597e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181662"
---
# <a name="tutorial-configure-purecloud-by-genesys-for-automatic-user-provisioning"></a>Oktatóanyag: a PureCloud konfigurálása a Genesys-ben a felhasználók automatikus kiépítési felállításához

Ez az oktatóanyag azokat a lépéseket ismerteti, amelyeket a Genesys és Azure Active Directory (Azure AD) mindkét PureCloud végre kell hajtania az automatikus felhasználó-kiépítés konfigurálásához. Ha konfigurálva van, az Azure AD automatikusan kiépíti és lefoglalja a felhasználókat és a csoportokat, hogy az Azure AD-létesítési szolgáltatással [PureCloud az Genesys](https://www.genesys.com) . A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a PureCloud-ben Genesys szerint
> * Felhasználók eltávolítása a PureCloud-ben a Genesys, ha már nincs szükség hozzáférésre
> * A felhasználói attribútumok szinkronizálása az Azure AD és a PureCloud között Genesys szerint
> * Csoportok és csoporttagságok kiépítése a PureCloud-ben Genesys szerint
> * [Egyszeri bejelentkezés](./purecloud-by-genesys-tutorial.md) a Genesys PureCloud (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Azure AD-bérlő](../develop/quickstart-create-new-tenant.md) 
* Egy felhasználói fiók az Azure AD-ben az átadás konfigurálására vonatkozó [engedéllyel](../roles/permissions-reference.md) (pl. alkalmazás-rendszergazda, felhőalkalmazás-rendszergazda, alkalmazástulajdonos vagy globális rendszergazda). 
* Egy PureCloud- [szervezet](https://help.mypurecloud.com/?p=81984).
* Egy OAuth-ügyfél létrehozásához [szükséges engedélyekkel](https://help.mypurecloud.com/?p=24360) rendelkező felhasználó.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés Az átadás üzembe helyezésének megtervezése
1. Ismerje meg [az átadási szolgáltatás működését](../app-provisioning/user-provisioning.md).
2. Határozza meg, hogy ki lesz [az átadás hatókörében](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Határozza meg, hogy az [Azure ad és a PureCloud milyen adatleképezést Genesys alapján](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-purecloud-by-genesys-to-support-provisioning-with-azure-ad"></a>2. lépés A PureCloud konfigurálása a Genesys használatával az Azure AD-vel való kiépítés támogatásához

1. Hozzon létre egy [OAuth-ügyfelet](https://help.mypurecloud.com/?p=188023) , amely konfigurálva van a PureCloud-szervezetben.
2. Jogkivonat létrehozása [a OAuth-ügyféllel](https://developer.mypurecloud.com/api/rest/authorization/use-client-credentials.html).
3. Ha szeretné, hogy a csoporttagság automatikusan kiépíthető legyen a PureCloud-on belül, a PureCloud azonos névvel kell [létrehoznia](https://help.mypurecloud.com/?p=52397) a csoportot az Azure ad-ben.

## <a name="step-3-add-purecloud-by-genesys-from-the-azure-ad-application-gallery"></a>3. lépés PureCloud hozzáadása a Genesys-ből az Azure AD Application Galleryből

Az Azure AD Genesys származó PureCloud hozzáadásával megkezdheti a Genesys által PureCloud kiépítés kezelését. Ha korábban már beállította a Genesys for SSO PureCloud, ugyanazt az alkalmazást használhatja. Az integráció első tesztelésekor azonban érdemes létrehozni egy külön alkalmazást. Az alkalmazások katalógusból való hozzáadásáról [itt](../manage-apps/add-application-portal.md) tudhat meg többet. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Az átadás hatókörében lévő személyek meghatározása 

Az Azure AD átadási szolgáltatása lehetővé teszi az átadott személyek hatókörének meghatározását az alkalmazáshoz való hozzárendelés és/vagy a felhasználó/csoport attribútumai alapján. Ha a hozzárendelés alapján történő hatókör-meghatározást választja, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet felhasználókat és csoportokat az alkalmazáshoz. Ha csak a felhasználó vagy csoport attribútumai alapján történő hatókörmeghatározást választja, az [itt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) leírt hatókörszűrőt használhatja. 

* Ha a Genesys által PureCloud felhasználók és csoportok hozzárendelését végzi, az **alapértelmezett hozzáféréstől** eltérő szerepkört kell kijelölnie. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva az átadásból, és az átadási naplókban nem jogosultként lesznek megjelölve. Ha az alkalmazáshoz csak az alapértelmezett hozzáférési szerepkör érhető el, akkor további szerepkörök felvételéhez [frissítheti az alkalmazásjegyzéket](../develop/howto-add-app-roles-in-azure-ad-apps.md). 

* Kezdje kicsiben. Tesztelje a felhasználók és csoportok kis halmazát, mielőtt mindenkire kiterjesztené. Amikor az átadás hatóköre a hozzárendelt felhasználókra és csoportokra van beállítva, ennek szabályozásához egy vagy két felhasználót vagy csoportot rendelhet az alkalmazáshoz. Amikor a hatókör az összes felhasználóra és csoportra van beállítva, meghatározhat egy [attribútumalapú hatókörszűrőt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-purecloud-by-genesys"></a>5. lépés Az automatikus felhasználó-kiépítés konfigurálása a Genesys-PureCloud 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy TestApp alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

### <a name="to-configure-automatic-user-provisioning-for-purecloud-by-genesys-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a PureCloud számára az Azure AD Genesys-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **PureCloud a Genesys alapján** lehetőséget.

    ![A PureCloud by Genesys hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a kiépítési mód legördülő listájáról az automatikus lehetőséggel.](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a PureCloud a Genesys API URL-cím és a OAuth-jogkivonat alapján a **bérlői URL** -cím és a **titkos jogkivonat** mezőiben. Az API URL-címe strukturálva lesz `{{API Url}}/api/v2/scim/v2` , a PureCloud-régió API URL-címének használatával a [PureCloud fejlesztői központból](https://developer.mypurecloud.com/api/rest/index.html). Kattintson a kapcsolat tesztelése elemre annak **ellenőrzéséhez** , hogy az Azure ad tud-e csatlakozni a PureCloud-hez Genesys. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Genesys-fiók PureCloud rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Képernyőfelvétel: a rendszergazdai hitelesítő adatok párbeszédpanel, ahol megadhatja a bérlő U R L-t és a titkos jogkivonatot.](./media/purecloud-by-genesys-provisioning-tutorial/provisioning.png)

6. Az **Értesítés e-mailben** mezőben adja meg annak a személynek vagy csoportnak az e-mail-címét, aki az átadással kapcsolatos hibaüzeneteket kapja, és jelölje be az **E-mail-értesítés küldése hiba esetén** jelölőnégyzetet.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **szinkronizálás Azure Active Directory a felhasználók PureCloud a Genesys alapján** lehetőséget.

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakasz Genesys PureCloud. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a PureCloud által a Genesys for Update műveletekhez használt felhasználói fiókokkal egyeznek meg. Ha úgy dönt, hogy megváltoztatja a [megfelelő cél attribútumot](../app-provisioning/customize-application-attributes.md), akkor biztosítania kell, hogy a Genesys API által biztosított PureCloud támogassa a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

     |Attribútum|Típus|
     |---|---|
     |userName (Felhasználónév)|Sztring|
     |active|Logikai|
     |displayName|Sztring|
     |emails[type eq "work"].value|Sztring|
     |cím|Sztring|
     |phoneNumbers[type eq "mobile"].value|Sztring|
     |phoneNumbers[type eq "work"].value|Sztring|
     |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: részleg|Sztring|
     |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: User: Manager|Referencia|
     |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: employeeNumber|Sztring|
     

10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása lehetőséget, hogy a Genesys PureCloud**.

11. Tekintse át az Azure AD-ból szinkronizált PureCloud az **attribútum-hozzárendelési** szakaszban található Genesys alapján. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a PureCloud által a Genesys for Update műveletekhez tartozó csoportok egyeztetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra. A Genesys által PureCloud nem támogatja a csoportok létrehozását vagy törlését, és csak a csoportok frissítését támogatja.

      |Attribútum|Típus|
      |---|---|
      |displayName|Sztring|
      |externalId|Sztring|
      |tagok|Referencia|

12. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

13. Ha engedélyezni szeretné az Azure AD kiépítési szolgáltatást a PureCloud számára a Genesys, módosítsa a **kiépítési állapotot** **a következőre** a **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket a Genesys PureCloud szeretne kiépíteni, ehhez válassza ki a kívánt értékeket a **hatókörben** a **Beállítások** szakaszban.

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

09/10 – a (z) "employeeNumber" vállalati attribútum támogatása hozzáadva.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)