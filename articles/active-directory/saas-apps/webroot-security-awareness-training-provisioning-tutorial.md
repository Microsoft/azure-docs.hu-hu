---
title: 'Oktatóanyag: a Webroot biztonsági betanításának beállítása a felhasználók automatikus üzembe helyezéséhez a Azure Active Directory segítségével | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és kiépíteni felhasználói fiókjait az Azure AD-ből a Webroot biztonsági ismereteinek betanításához.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 455f4396-930e-4db5-a167-d3ea6a860a17
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: Zhchia
ms.openlocfilehash: f015718c59178b8094f535280edadd641d22a806
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98741893"
---
# <a name="tutorial-configure-webroot-security-awareness-training-for-automatic-user-provisioning"></a>Oktatóanyag: a Webroot biztonsági betanításának beállítása a felhasználók automatikus üzembe helyezéséhez

Ez az oktatóanyag leírja, milyen lépéseket kell végrehajtania a Webroot biztonsági tudatosságnövelő képzésben és Azure Active Directory (Azure AD) az automatikus felhasználó-kiépítés konfigurálásához. Ha konfigurálva van, az Azure AD automatikusan kiépíti és lefoglalja a felhasználókat és csoportokat az Azure AD-létesítési szolgáltatás használatával a [Webroot biztonsági tájékoztató képzéséhez](https://www.webroot.com/) . A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a Webroot biztonsági ismereteinek betanításával
> * Ha már nincs szükség hozzáférésre, távolítsa el a felhasználókat a Webroot biztonsági ismereteinek betanításához
> * A felhasználói attribútumok szinkronizálása az Azure AD és a Webroot biztonsági ismereteinek betanítása között
> * Csoportok és csoporttagságok kiépítése a Webroot biztonsági tudatosságnövelő képzésben

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Azure AD-bérlő](../develop/quickstart-create-new-tenant.md) 
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](../roles/permissions-reference.md) a kiépítés konfigurálására (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda, alkalmazás tulajdonosa vagy globális rendszergazda).
* Felügyelt szolgáltatói konzol, amely legalább egy webhelye számára engedélyezve van a Webroot biztonsági tudatosságnövelő képzésen.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés Az átadás üzembe helyezésének megtervezése
1. Ismerje meg [az átadási szolgáltatás működését](../app-provisioning/user-provisioning.md).
2. Határozza meg, hogy ki lesz [az átadás hatókörében](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Határozza meg, hogy az [Azure ad és a Webroot biztonsági betanítása között milyen adatleképezést kell leképezni](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-webroot-security-awareness-training-to-support-provisioning-with-azure-ad"></a>2. lépés Az Azure AD-vel való kiépítés támogatásához a Webroot biztonsági tájékoztatási képzésének konfigurálása

### <a name="obtain-a-secret-token"></a>Titkos jogkivonat beszerzése

A hely Azure AD-hez való összekapcsolásához a Webroot felügyeleti konzolján be kell szereznie egy **titkos jogkivonatot** a helyhez.

1. Jelentkezzen be a [Webroot felügyeleti konzolba](https://identity.webrootanywhere.com/v1/Account/login#tab_customers)

2. A **helyek** lapon kattintson a fogaskerék ikonra az Azure ad-vel összekapcsolni kívánt hely biztonsági tudatosságnövelő képzése oszlopában.

    ![Fogaskerék ikon](./media/webroot-security-awareness-training-provisioning-tutorial/gear-icon.png)

3. Az **Azure ad-integráció konfigurálásához** kattintson a gombra.

    ![Az Azure AD-integráció konfigurálása](./media/webroot-security-awareness-training-provisioning-tutorial/configure-azure-ad-integration.png)

4. Másolja ki és mentse a **titkos jogkivonatot**. Ezt az értéket a rendszer az Azure Portal a Webroot Security tudatosságnövelő betanítási alkalmazás üzembe helyezés lapjának titkos jogkivonat mezőjébe írja.

5. Kattintson a **Kész** gombra.

    ![Titkos token másolása](./media/webroot-security-awareness-training-provisioning-tutorial/copy-secret-token.png)

## <a name="step-3-add-webroot-security-awareness-training-from-the-azure-ad-application-gallery"></a>3. lépés Az Azure AD-alkalmazás-katalógusban a Webroot biztonsági tudatosságnövelő képzésének hozzáadása

Az Azure AD alkalmazás-katalógusában a Webroot Security tudatosságnövelő betanítása segítségével megkezdheti a kiépítés kezelését a Webroot biztonsági ismereteinek betanításához. Az alkalmazások katalógusból való hozzáadásáról [itt](../manage-apps/add-application-portal.md) tudhat meg többet. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Az átadás hatókörében lévő személyek meghatározása 

Az Azure AD átadási szolgáltatása lehetővé teszi az átadott személyek hatókörének meghatározását az alkalmazáshoz való hozzárendelés és/vagy a felhasználó/csoport attribútumai alapján. Ha a hozzárendelés alapján történő hatókör-meghatározást választja, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet felhasználókat és csoportokat az alkalmazáshoz. Ha csak a felhasználó vagy csoport attribútumai alapján történő hatókörmeghatározást választja, az [itt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) leírt hatókörszűrőt használhatja. 

* Amikor felhasználókat és csoportokat rendel a Webroot biztonsági tudatosságnövelő képzéshez, ki kell választania az **alapértelmezett hozzáféréstől** eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva az átadásból, és az átadási naplókban nem jogosultként lesznek megjelölve. Ha az alkalmazáshoz csak az alapértelmezett hozzáférési szerepkör érhető el, akkor további szerepkörök felvételéhez [frissítheti az alkalmazásjegyzéket](../develop/howto-add-app-roles-in-azure-ad-apps.md). 

* Kezdje kicsiben. Tesztelje a felhasználók és csoportok kis halmazát, mielőtt mindenkire kiterjesztené. Amikor az átadás hatóköre a hozzárendelt felhasználókra és csoportokra van beállítva, ennek szabályozásához egy vagy két felhasználót vagy csoportot rendelhet az alkalmazáshoz. Amikor a hatókör az összes felhasználóra és csoportra van beállítva, meghatározhat egy [attribútumalapú hatókörszűrőt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-webroot-security-awareness-training"></a>5. lépés Az automatikus felhasználó-kiépítés konfigurálása a Webroot biztonsági ismereteinek betanításához 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy TestApp alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

### <a name="to-configure-automatic-user-provisioning-for-webroot-security-awareness-training-in-azure-ad"></a>Az Azure AD-ben elérhető, az automatikus felhasználói üzembe helyezés beállítása a Webroot-ban – biztonsági tájékoztatás:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Webroot Security tudatosságnövelő képzés** lehetőséget.

    ![A Webroot Security tudatosságnövelő betanítási hivatkozása az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a kiépítési mód legördülő listájáról az automatikus lehetőséggel.](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja `https://awarenessapi.webrootanywhere.com/api/v2/scim` meg a **bérlői URL-címet**. Adja meg a titkos jogkivonat értékét, amely korábban a **titkos jogkivonatban** lett lekérve. Kattintson a kapcsolat tesztelése elemre annak **ellenőrzéséhez** , hogy az Azure ad tud-e csatlakozni a Webroot Security tudatosságnövelő képzéséhez. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Webroot biztonsági betanítási fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Képernyőfelvétel: a rendszergazdai hitelesítő adatok párbeszédpanel, ahol megadhatja a bérlő U R L-t és a titkos jogkivonatot.](./media/webroot-security-awareness-training-provisioning-tutorial/provisioning.png)

6. Az **Értesítés e-mailben** mezőben adja meg annak a személynek vagy csoportnak az e-mail-címét, aki az átadással kapcsolatos hibaüzeneteket kapja, és jelölje be az **E-mail-értesítés küldése hiba esetén** jelölőnégyzetet.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók kiépítése** lehetőséget.

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-leképezési** szakaszban található, a Webroot biztonsági ismereteinek betanítása című témakörben. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Webroot biztonsági tájékoztató felhasználói fiókjainak egyeztetésére használhatók a frissítési műveletekhez. Ha úgy dönt, hogy megváltoztatja a [megfelelő célként megadott attribútumot](../app-provisioning/customize-application-attributes.md), akkor biztosítania kell, hogy a Webroot Security TUDATOSSÁGNÖVELŐ oktatási API támogassa a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   |Attribútum|Típus|Szűréshez támogatott|
   |---|---|---|
   |userName (Felhasználónév)|Sztring|&check;|
   |externalId|Sztring|
   |name.givenName|Sztring|
   |name.familyName|Sztring|
   |emails[type eq "work"].value|Sztring|

10. A **leképezések** szakaszban válassza ki a **Azure Active Directory csoportok kiépítése** lehetőséget.

11. Tekintse át az Azure AD-ből szinkronizált csoportosítási attribútumokat az **attribútum-leképezési** szakaszban található, a Webroot biztonsági ismereteinek betanítása című témakörben. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Webroot biztonsági ismeretekkel kapcsolatos, frissítési műveletekhez tartozó csoportok egyeztetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

      |Attribútum|Típus|Szűréshez támogatott|
      |---|---|---|
      |displayName|Sztring|&check;|
      |tagok|Referencia|
      |externalId|Sztring|

12. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

13. Ha engedélyezni szeretné az Azure AD-kiépítési szolgáltatást a Webroot biztonsági tudatosságnövelő képzéséhez, módosítsa a **kiépítési állapotot** **a következőre** a **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg azokat a felhasználókat és/vagy csoportokat, akiket a Webroot biztonsági tudatosságnövelő képzésre szeretne kiépíteni a **Beállítások** szakasz **hatókörében** lévő kívánt értékek kiválasztásával.

    ![Átadási hatókör](common/provisioning-scope.png)

15. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet a **Beállítások** szakasz **Hatókör** területén meghatározott összes felhasználó és csoport kezdeti szinkronizálási ciklusát elindítja. A kezdeti ciklus elvégzése hosszabb időt vesz igénybe, mint a későbbi ciklusok, amelyek az Azure AD átadási szolgáltatásának futtatása során körülbelül 40 percenként lesznek végrehajtva. 

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Az átadás konfigurálása után a következő erőforrásokkal monitorozhatja az üzemelő példányt:

1. Az [átadási naplókkal](../reports-monitoring/concept-provisioning-logs.md) határozhatja meg, hogy mely felhasználók átadása sikeres, és melyeké sikertelen.
2. A [folyamatjelzőn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) láthatja az átadási ciklus állapotát és azt, hogy mennyi hiányzik még a befejeződéséhez.
3. Ha úgy tűnik, hogy az átadási konfiguráció állapota nem megfelelő, az alkalmazás karanténba kerül. A karanténállapotokról [itt](../app-provisioning/application-provisioning-quarantine-status.md) találhat további információt.

## <a name="change-log"></a>Változási napló
* 1/21/2021 – a felhasználók számára a "userName" alapattribútum támogatása hozzáadva.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)