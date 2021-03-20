---
title: 'Oktatóanyag: a Forts Change Cloud beállítása az automatikus felhasználó-kiépítés a Azure Active Directorykal | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és kiépíteni felhasználói fiókjait az Azure AD-ből a Forts változási felhőbe.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: ef9a8f5e-0bf0-46d6-8e17-3bcf1a5b0a6b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2021
ms.author: Zhchia
ms.openlocfilehash: 43b783d9462205b01d3ac4de0c5779fdc9864470
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99550452"
---
# <a name="tutorial-configure-fortes-change-cloud-for-automatic-user-provisioning"></a>Oktatóanyag: a Forts Change Cloud beállítása a felhasználók automatikus üzembe helyezéséhez

Ez az oktatóanyag leírja, hogy milyen lépéseket kell végrehajtania a Forts Change Cloud és a Azure Active Directory (Azure ad) segítségével a felhasználók automatikus kiépítés konfigurálásához. Ha konfigurálva van, az Azure AD automatikusan kiépíti és kiosztja a felhasználókat és csoportokat a [felhőben](https://fortesglobal.com/) az Azure ad kiépítési szolgáltatás használatával. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a Forts Change Cloud-ban
> * A felhőben lévő felhasználók eltávolítása, ha már nincs szükség hozzáférésre
> * A felhasználói attribútumok szinkronizálása az Azure AD és a fortes között a felhőben
> * [Egyszeri bejelentkezés](fortes-change-cloud-tutorial.md) a Forts Change Cloud (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Azure AD-bérlő](../develop/quickstart-create-new-tenant.md) 
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](../roles/permissions-reference.md) a kiépítés konfigurálására (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda, alkalmazás tulajdonosa vagy globális rendszergazda). 
* A Forts megváltoztatja A felhő bérlőjét.
* A Forts-beli felhasználói fiók rendszergazdai engedélyekkel változtatja meg a felhőt.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés Az átadás üzembe helyezésének megtervezése
1. Ismerje meg [az átadási szolgáltatás működését](../app-provisioning/user-provisioning.md).
2. Határozza meg, hogy ki lesz [az átadás hatókörében](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Határozza meg, hogy az [Azure ad és a fortes milyen adatleképezést szeretne leképezni a felhőben](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-fortes-change-cloud-to-support-provisioning-with-azure-ad"></a>2. lépés Az Azure AD-vel való kiépítés támogatásához konfigurálja a Forts Change Cloud változót

1. Jelentkezzen be a rendszergazdai fiókjával a Forts Change felhőben. Kattintson a **Beállítások ikonra** , majd navigáljon a **felhasználó üzembe helyezése (scim)** elemre.

    [![A Forts Change Cloud scim-beállítás ](media/fortes-change-cloud-provisioning-tutorial/scim-settings.png)](media/fortes-change-cloud-provisioning-tutorial/scim-settings.png#lightbox)

2. Az új ablakban másolja és mentse a **bérlői URL-címet** és az **elsődleges jogkivonatot**. A bérlői URL-címet a **bérlői URL** -cím * mezőben adja meg, az elsődleges token pedig a Forts Cloud alkalmazás üzembe helyezés lapjának **Secret** * token mezőjébe kerül, a Azure Portal.
     
      [![A fortes megváltoztatja a felhő elsődleges tokenjét](media/fortes-change-cloud-provisioning-tutorial/primary-token.png)](media/fortes-change-cloud-provisioning-tutorial/primary-token.png#lightbox)

## <a name="step-3-add-fortes-change-cloud-from-the-azure-ad-application-gallery"></a>3. lépés Erődök változása a felhőben az Azure AD Application Galleryből

Az Azure AD alkalmazás-katalógusában a Forts Change Cloud változóval megkezdheti a felhőre való kiépítés kezelését. Ha korábban már telepítette a Clouds-t az egyszeri bejelentkezéshez, ugyanazt az alkalmazást használhatja. Az integráció első tesztelésekor azonban érdemes létrehozni egy külön alkalmazást. Az alkalmazások katalógusból való hozzáadásáról [itt](../manage-apps/add-application-portal.md) tudhat meg többet. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Az átadás hatókörében lévő személyek meghatározása 

Az Azure AD átadási szolgáltatása lehetővé teszi az átadott személyek hatókörének meghatározását az alkalmazáshoz való hozzárendelés és/vagy a felhasználó/csoport attribútumai alapján. Ha a hozzárendelés alapján történő hatókör-meghatározást választja, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet felhasználókat és csoportokat az alkalmazáshoz. Ha csak a felhasználó vagy csoport attribútumai alapján történő hatókörmeghatározást választja, az [itt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) leírt hatókörszűrőt használhatja. 

* Amikor felhasználókat és csoportokat rendel a Forts változó felhőhöz, ki kell választania az **alapértelmezett hozzáféréstől** eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva az átadásból, és az átadási naplókban nem jogosultként lesznek megjelölve. Ha az alkalmazáshoz csak az alapértelmezett hozzáférési szerepkör érhető el, akkor további szerepkörök felvételéhez [frissítheti az alkalmazásjegyzéket](../develop/howto-add-app-roles-in-azure-ad-apps.md). 

* Kezdje kicsiben. Tesztelje a felhasználók és csoportok kis halmazát, mielőtt mindenkire kiterjesztené. Amikor az átadás hatóköre a hozzárendelt felhasználókra és csoportokra van beállítva, ennek szabályozásához egy vagy két felhasználót vagy csoportot rendelhet az alkalmazáshoz. Amikor a hatókör az összes felhasználóra és csoportra van beállítva, meghatározhat egy [attribútumalapú hatókörszűrőt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-fortes-change-cloud"></a>5. lépés Az automatikus felhasználó-kiépítés beállítása a Felhőbeli váltásra 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy TestApp alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

### <a name="to-configure-automatic-user-provisioning-for-fortes-change-cloud-in-azure-ad"></a>A felhőben az Azure AD-ben való automatikus felhasználó-kiépítés beállítása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Forts Change Cloud (felhő módosítása**) lehetőséget.

    ![A Forts Change Cloud hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Kiépítés lap automatikus](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg az erődök változása a Felhőbeli bérlő URL-címét és a titkos tokent. Kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad csatlakozni tudjanak a Forts Change felhőhöz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Forts Change Cloud-fiók rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az **Értesítés e-mailben** mezőben adja meg annak a személynek vagy csoportnak az e-mail-címét, aki az átadással kapcsolatos hibaüzeneteket kapja, és jelölje be az **E-mail-értesítés küldése hiba esetén** jelölőnégyzetet.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **szinkronizálás Azure Active Directory a felhasználók számára a felhő módosítása** lehetőséget.

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban található, a felhőre való váltáshoz. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Felhőbeli felhasználói fiókok egyeztetésére szolgálnak a frissítési műveletekhez. Ha úgy dönt, hogy megváltoztatja a [megfelelő cél attribútumot](../app-provisioning/customize-application-attributes.md), akkor biztosítania kell, hogy a Forts Change Cloud API támogassa a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   |Attribútum|Típus|Szűréshez támogatott|
   |---|---|---|
   |userName (Felhasználónév)|Sztring|&check;|
   |active|Logikai|
   |emails[type eq "work"].value|Sztring|
   |name.givenName|Sztring|
   |name.familyName|Sztring|
   |név. formázott|Sztring|
   |externalId|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: FCC: 2.0: felhasználó: rendszergazda|Logikai|
   |urn: IETF: params: scim: sémák: bővítmény: FCC: 2.0: felhasználó: loginDisabled|Logikai|

  

10. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

11. Ha engedélyezni szeretné az Azure AD kiépítési szolgáltatást a Forts-re, módosítsa a **kiépítési állapotot** a  **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

12. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket a Forts-re szeretne kiépíteni a felhőben. Ehhez válassza ki a kívánt értékeket a **hatókörben** a **Beállítások** szakaszban.

    ![Átadási hatókör](common/provisioning-scope.png)

13. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet a **Beállítások** szakasz **Hatókör** területén meghatározott összes felhasználó és csoport kezdeti szinkronizálási ciklusát elindítja. A kezdeti ciklus elvégzése hosszabb időt vesz igénybe, mint a későbbi ciklusok, amelyek az Azure AD átadási szolgáltatásának futtatása során körülbelül 40 percenként lesznek végrehajtva. 

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Az átadás konfigurálása után a következő erőforrásokkal monitorozhatja az üzemelő példányt:

* Az [átadási naplókkal](../reports-monitoring/concept-provisioning-logs.md) határozhatja meg, hogy mely felhasználók átadása sikeres, és melyeké sikertelen.
* A [folyamatjelzőn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) láthatja az átadási ciklus állapotát és azt, hogy mennyi hiányzik még a befejeződéséhez.
* Ha úgy tűnik, hogy az átadási konfiguráció állapota nem megfelelő, az alkalmazás karanténba kerül. A karanténállapotokról [itt](../app-provisioning/application-provisioning-quarantine-status.md) találhat további információt.  

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)