---
title: 'Oktatóanyag: a SolarWinds Service Desk (korábban Samanage) konfigurálása a felhasználók automatikus üzembe helyezéséhez a Azure Active Directory segítségével | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és kiépíteni felhasználói fiókjait az Azure AD-ből a SolarWinds Service Deskbe (korábban Samanage).
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2020
ms.author: Zhchia
ms.openlocfilehash: d68811c5e7b92698944d7b1b1736eef864d97d20
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104802301"
---
# <a name="tutorial-configure-solarwinds-service-desk-previously-samanage-for-automatic-user-provisioning"></a>Oktatóanyag: a SolarWinds Service Desk (korábban Samanage) konfigurálása a felhasználók automatikus üzembe helyezéséhez

Ez az oktatóanyag azokat a lépéseket ismerteti, amelyeket a SolarWinds Service Desk (korábban Samanage) és a Azure Active Directory (Azure AD) használatával kell elvégezni az automatikus felhasználó-kiépítés konfigurálásához. Ha konfigurálva van, az Azure AD automatikusan kiépíti és kiosztja a felhasználókat és csoportokat a [SolarWinds](https://www.samanage.com/pricing/) az Azure ad-létesítési szolgáltatás használatával. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md).

## <a name="migrate-to-the-new-solarwinds-service-desk-application"></a>Migrálás az új SolarWinds Service Desk-alkalmazásba

Ha már rendelkezik a SolarWinds Service Desktel való integrációval, tekintse meg a következő, a közelgő változásokról szóló szakaszt. Ha első alkalommal állítja be a SolarWinds-szolgáltatást, kihagyhatja ezt a szakaszt, és áthelyezheti a **támogatott funkciókra**.

#### <a name="whats-changing"></a>Mi változik?

* Változások az Azure AD-oldalon: a felhasználók Samange való kiépítésének engedélyezési módszere történelmileg **Egyszerű hitelesítés**. Hamarosan látni fogja, hogy az engedélyezési módszer **hosszú élettartamú titkos jogkivonatra** változott.


#### <a name="what-do-i-need-to-do-to-migrate-my-existing-custom-integration-to-the-new-application"></a>Mit kell tennem a meglévő egyéni integráció új alkalmazásba való átállításához?

Ha rendelkezik egy meglévő SolarWinds-szolgáltatással, amely érvényes rendszergazdai hitelesítő adatokkal rendelkezik, **nincs szükség beavatkozásra**. A rendszer automatikusan áttelepíti az ügyfeleket az új alkalmazásba. Ez a folyamat teljesen a színfalak mögött történik. Ha a meglévő hitelesítő adatok lejárnak, vagy ha újra engedélyezni szeretné az alkalmazáshoz való hozzáférést, hosszú élettartamú titkos jogkivonatot kell létrehoznia. Új jogkivonat létrehozásához tekintse meg a jelen cikk 2. lépését.


#### <a name="how-can-i-tell-if-my-application-has-been-migrated"></a>Honnan tudhatom meg, hogy az alkalmazás migrálása megtörtént-e? 

Az alkalmazás migrálása után a **rendszergazdai hitelesítő adatok** szakaszban a **rendszergazdai Felhasználónév** és a **rendszergazdai jelszó** mezők egyetlen **titkos jogkivonat** mezővel lesznek lecserélve.

## <a name="capabilities-supported"></a>Támogatott képességek

> [!div class="checklist"]
> * Felhasználók létrehozása a SolarWinds Service Desk-ben
> * Távolítsa el a felhasználókat a SolarWinds Service Desk szolgáltatásban, ha már nincs szükség hozzáférésre
> * Felhasználói attribútumok szinkronizálása az Azure AD és a SolarWinds Service Desk között
> * Csoportok és csoporttagságok kiépítése a SolarWinds Service Desk szolgáltatásban
> * [Egyszeri bejelentkezés](./samanage-tutorial.md) a SolarWinds Service Desk szolgáltatásba (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Azure AD-bérlő](../develop/quickstart-create-new-tenant.md) 
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](../roles/permissions-reference.md) a kiépítés konfigurálására (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda, alkalmazás tulajdonosa vagy globális rendszergazda). 
* A professzionális csomaggal rendelkező [SolarWinds Service Desk-bérlő](https://www.samanage.com/pricing/) .
* Egy felhasználói fiók a SolarWinds Service Desk-ben rendszergazdai engedélyekkel.

> [!Note]
> A szerepkörök importálásakor a szerepköröket nem szabad manuálisan szerkeszteni Azure Active Directoryban.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés Az átadás üzembe helyezésének megtervezése
1. Ismerje meg [az átadási szolgáltatás működését](../app-provisioning/user-provisioning.md).
2. Határozza meg, hogy ki lesz [az átadás hatókörében](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Határozza meg, hogy az [Azure ad és a SolarWinds Service Desk milyen adatleképezési](../app-provisioning/customize-application-attributes.md)leképezéseket biztosít. 

## <a name="step-2-configure-solarwinds-service-desk-to-support-provisioning-with-azure-ad"></a>2. lépés A SolarWinds Service Desk konfigurálása az Azure AD-vel való kiépítés támogatásához

Titkos jogkivonat hitelesítéshez való létrehozásával kapcsolatban lásd az [oktatóanyag-tokenek hitelesítése az API-integrációhoz](https://help.samanage.com/s/article/Tutorial-Tokens-Authentication-for-API-Integration-1536721557657)című témakört.

## <a name="step-3-add-solarwinds-service-desk-from-the-azure-ad-application-gallery"></a>3. lépés A SolarWinds Service Desk hozzáadása az Azure AD Application Galleryből

Adja hozzá a SolarWinds Service Deskt az Azure AD-alkalmazás-katalógusból a SolarWinds Service Desk üzembe helyezésének kezeléséhez. Ha korábban már beállította a SolarWinds Service Desk egyszeri bejelentkezést, ugyanazt az alkalmazást használhatja. Az integráció első tesztelésekor azonban érdemes létrehozni egy külön alkalmazást. Az alkalmazások katalógusból való hozzáadásáról [itt](../manage-apps/add-application-portal.md) tudhat meg többet. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Az átadás hatókörében lévő személyek meghatározása 

Az Azure AD átadási szolgáltatása lehetővé teszi az átadott személyek hatókörének meghatározását az alkalmazáshoz való hozzárendelés és/vagy a felhasználó/csoport attribútumai alapján. Ha a hozzárendelés alapján történő hatókör-meghatározást választja, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet felhasználókat és csoportokat az alkalmazáshoz. Ha csak a felhasználó vagy csoport attribútumai alapján történő hatókörmeghatározást választja, az [itt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) leírt hatókörszűrőt használhatja. 

* Amikor felhasználókat és csoportokat rendel a SolarWinds Service Desk szolgáltatáshoz, ki kell választania az **alapértelmezett hozzáféréstől** eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva az átadásból, és az átadási naplókban nem jogosultként lesznek megjelölve. Ha az alkalmazáshoz csak az alapértelmezett hozzáférési szerepkör érhető el, akkor további szerepkörök felvételéhez [frissítheti az alkalmazásjegyzéket](../develop/howto-add-app-roles-in-azure-ad-apps.md). 

* Kezdje kicsiben. Tesztelje a felhasználók és csoportok kis halmazát, mielőtt mindenkire kiterjesztené. Amikor az átadás hatóköre a hozzárendelt felhasználókra és csoportokra van beállítva, ennek szabályozásához egy vagy két felhasználót vagy csoportot rendelhet az alkalmazáshoz. Amikor a hatókör az összes felhasználóra és csoportra van beállítva, meghatározhat egy [attribútumalapú hatókörszűrőt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-solarwinds-service-desk"></a>5. lépés Automatikus felhasználó-kiépítés konfigurálása a SolarWinds Service Desk szolgáltatáshoz 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy TestApp alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

### <a name="to-configure-automatic-user-provisioning-for-solarwinds-service-desk-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a SolarWinds Service Desk számára az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **SolarWinds Service Desk** elemet.

3. Válassza a **Kiépítés** lapot.

    ![A kiépítési lapot kijelölő képernyőkép.](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőfelvétel: a kiépítési mód beállítása automatikus.](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja `https://api.samanage.com` meg a **bérlői URL-címet**.  Adja meg a titkos jogkivonat értékét, amely korábban a **titkos jogkivonatban** lett lekérve. Válassza a **kapcsolat tesztelése** lehetőséget, hogy az Azure ad képes legyen csatlakozni a SolarWinds Service Desk szolgáltatáshoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a SolarWinds Service Desk-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Képernyőfelvétel: a kiválasztott tesztelési gomb megjelenítése.](./media/samanage-provisioning-tutorial/provisioning.png)

6. Az **Értesítés e-mailben** mezőben adja meg annak a személynek vagy csoportnak az e-mail-címét, aki az átadással kapcsolatos hibaüzeneteket kapja, és jelölje be az **E-mail-értesítés küldése hiba esetén** jelölőnégyzetet.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a SolarWinds Service Desk-** be lehetőséget.

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban található SolarWinds. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a SolarWinds Service Desk felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. Ha úgy dönt, hogy módosítja a [megfelelő cél attribútumot](../app-provisioning/customize-application-attributes.md), akkor biztosítania kell, hogy a SolarWinds Service Desk API támogassa a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

      ![Samange felhasználói leképezések](./media/samanage-provisioning-tutorial/user-attributes.png)

10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása lehetőséget a SolarWinds Service Desk** elemre.

11. Tekintse át az Azure AD-ből szinkronizált SolarWinds az **attribútum-hozzárendelési** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a SolarWinds Service deskben lévő csoportok egyeztetésére használhatók frissítési műveletekhez. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

      ![Samange csoport-hozzárendelések](./media/samanage-provisioning-tutorial/group-attributes.png)

12. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

13. Ha engedélyezni szeretné az Azure AD-kiépítési szolgáltatást a SolarWinds Service Desk számára, módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg azokat a felhasználókat és/vagy csoportokat, akik számára a SolarWinds szeretné kiépíteni a **Settings (beállítások** ) szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Átadási hatókör](common/provisioning-scope.png)

15. Ha készen áll a létesítésre, válassza a **Mentés** lehetőséget.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet a **Beállítások** szakasz **Hatókör** területén meghatározott összes felhasználó és csoport kezdeti szinkronizálási ciklusát elindítja. A kezdeti ciklus elvégzése hosszabb időt vesz igénybe, mint a későbbi ciklusok, amelyek az Azure AD átadási szolgáltatásának futtatása során körülbelül 40 percenként lesznek végrehajtva. 

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Az átadás konfigurálása után a következő erőforrásokkal monitorozhatja az üzemelő példányt:

1. Az [átadási naplókkal](../reports-monitoring/concept-provisioning-logs.md) határozhatja meg, hogy mely felhasználók átadása sikeres, és melyeké sikertelen.
2. A [folyamatjelzőn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) láthatja az átadási ciklus állapotát és azt, hogy mennyi hiányzik még a befejeződéséhez.
3. Ha úgy tűnik, hogy az átadási konfiguráció állapota nem megfelelő, az alkalmazás karanténba kerül. A karanténállapotokról [itt](../app-provisioning/application-provisioning-quarantine-status.md) találhat további információt.

## <a name="connector-limitations"></a>Összekötő korlátozásai

Ha bejelöli a **minden felhasználó és csoport szinkronizálása** lehetőséget, és beállítja a SolarWinds Service Desk **roles** attribútum értékét, akkor az **alapértelmezett érték alatti értéket, ha a null (nem kötelező)** mezőt a következő formátumban kell megadni:

- {"displayName": "role"}, ahol a szerepkör a kívánt alapértelmezett érték.

## <a name="change-log"></a>Változási napló

* 09/14/2020 – a vállalat nevét két SaaS-oktatóanyagban módosította a Samanage-ről a SolarWinds Service deskre (korábban Samanage) https://github.com/ravitmorales .
* 04/22/2020 – frissített engedélyezési módszer az alapszintű hitelesítésből a hosszú élettartamú titkos jogkivonatba.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)
