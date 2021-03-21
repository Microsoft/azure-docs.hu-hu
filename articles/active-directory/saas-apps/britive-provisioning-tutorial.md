---
title: 'Oktatóanyag: a nagyAzure Active Directoryi használatra való automatikus kiépítés beállítása a Nagya felhasználó számára | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és kiépíteni felhasználói fiókjait az Azure AD-ből a brit rendszerbe.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 622688b3-9d20-482e-aab9-ce2a1f01e747
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2021
ms.author: Zhchia
ms.openlocfilehash: 8bebcb49bc7bf31614a161c08d33d5910679b614
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103225726"
---
# <a name="tutorial-configure-britive-for-automatic-user-provisioning"></a>Oktatóanyag: a Nagya felhasználók automatikus üzembe helyezésének konfigurálása

Ez az oktatóanyag azokat a lépéseket ismerteti, amelyeket a nagyvállalati és Azure Active Directory (Azure AD) szolgáltatásban kell elvégezni az automatikus felhasználó-kiépítés konfigurálásához. Ha konfigurálva van, az Azure AD automatikusan kiépíti és kiosztja a felhasználókat és [csoportokat az Azure](https://www.britive.com/) ad kiépítési szolgáltatásával. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a brit
> * Ha már nincs szükség hozzáférésre, távolítsa el a felhasználókat a britek számára
> * A felhasználói attribútumok szinkronizálása az Azure AD és a brit rendszer között
> * Csoportok és csoporttagságok kiépítése a brit rendszerekben
> * [Egyszeri bejelentkezés](britive-tutorial.md) a brit rendszerbe (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Azure AD-bérlő](../develop/quickstart-create-new-tenant.md) 
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](../roles/permissions-reference.md) a kiépítés konfigurálására (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda, alkalmazás tulajdonosa vagy globális rendszergazda). 
* [Brit](https://www.britive.com/) bérlő.
* Egy rendszergazdai engedélyekkel rendelkező felhasználói fiók.


## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés Az átadás üzembe helyezésének megtervezése
1. Ismerje meg [az átadási szolgáltatás működését](../app-provisioning/user-provisioning.md).
1. Határozza meg, hogy ki lesz [az átadás hatókörében](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Határozza meg, hogy az [Azure ad és a britek között milyen adatleképezést kell leképezni](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-britive-to-support-provisioning-with-azure-ad"></a>2. lépés A Britség konfigurálása az Azure AD-vel való kiépítés támogatására

Az alkalmazást manuálisan kell konfigurálni az alábbi lépésekkel:
1. Jelentkezzen be a brit alkalmazásba rendszergazdai jogosultságokkal
1. Kattintson a **rendszergazda – >felhasználói adminisztráció – >Identity Providers** elemre.
1. Kattintson az **Identity Provider hozzáadása** elemre. Adja meg a nevet és a leírást. Kattintson az Identity Provider hozzáadása gombra.

    ![Identitásszolgáltató](media/britive-provisioning-tutorial/identity.png)

1. Ekkor megjelenik egy, az alább láthatóhoz hasonló konfigurációs oldal.

    ![Konfigurációs lap](media/britive-provisioning-tutorial/configuration.png)

1. Kattintson a **scim** fülre. Módosítsa a SCIM-szolgáltatót az Általánosról az Azure-ra, és mentse a módosításokat. Másolja a SCIM URL-címét, és jegyezze fel. Ezeket az értékeket a Azure Portal a brit alkalmazás üzembe helyezés lapjának **bérlői URL-címe** mezőibe írja a rendszer.

    ![SCIM lap](media/britive-provisioning-tutorial/scim.png)

1. Kattintson a **create token (jogkivonat létrehozása**) elemre. Válassza ki a jogkivonat érvényességét kötelezőként, majd kattintson a jogkivonat létrehozása gombra.

    ![Jogkivonat létrehozása](media/britive-provisioning-tutorial/create-token.png)

1. Másolja a generált tokent, és jegyezze fel. Kattintson az OK gombra. Vegye figyelembe, hogy a felhasználó nem fogja tudni újra megtekinteni a tokent. Ha szükséges, kattintson Re-Create gombra az új jogkivonat létrehozásához. Ez az érték a getAbstract alkalmazás üzembe helyezés lapjának **titkos jogkivonat** és bérlői URL-címe mezőiben jelenik meg a Azure Portal.

    ![Másolási jogkivonat](media/britive-provisioning-tutorial/copy-token.png) 


## <a name="step-3-add-britive-from-the-azure-ad-application-gallery"></a>3. lépés A Britség hozzáadása az Azure AD Application Galleryből

Az Azure AD-alkalmazás-katalógusban a britek hozzáadásával megkezdheti a kiépítés felügyeletét. Ha korábban már beállította a brit SSO-t, ugyanazt az alkalmazást használhatja. Az integráció első tesztelésekor azonban érdemes létrehozni egy külön alkalmazást. Az alkalmazások katalógusból való hozzáadásáról [itt](../manage-apps/add-application-portal.md) tudhat meg többet. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Az átadás hatókörében lévő személyek meghatározása 

Az Azure AD átadási szolgáltatása lehetővé teszi az átadott személyek hatókörének meghatározását az alkalmazáshoz való hozzárendelés és/vagy a felhasználó/csoport attribútumai alapján. Ha a hozzárendelés alapján történő hatókör-meghatározást választja, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet felhasználókat és csoportokat az alkalmazáshoz. Ha csak a felhasználó vagy csoport attribútumai alapján történő hatókörmeghatározást választja, az [itt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) leírt hatókörszűrőt használhatja. 

* Amikor felhasználókat és csoportokat rendel a britek számára, ki kell választania az **alapértelmezett hozzáféréstől** eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva az átadásból, és az átadási naplókban nem jogosultként lesznek megjelölve. Ha az alkalmazáshoz csak az alapértelmezett hozzáférési szerepkör érhető el, akkor további szerepkörök felvételéhez [frissítheti az alkalmazásjegyzéket](../develop/howto-add-app-roles-in-azure-ad-apps.md). 

* Kezdje kicsiben. Tesztelje a felhasználók és csoportok kis halmazát, mielőtt mindenkire kiterjesztené. Amikor az átadás hatóköre a hozzárendelt felhasználókra és csoportokra van beállítva, ennek szabályozásához egy vagy két felhasználót vagy csoportot rendelhet az alkalmazáshoz. Amikor a hatókör az összes felhasználóra és csoportra van beállítva, meghatározhat egy [attribútumalapú hatókörszűrőt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-britive"></a>5. lépés Az automatikus felhasználó-kiépítés beállítása a britek számára 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy csoportos hozzárendelések alapján hozza létre, frissítse és tiltsa le a felhasználók és/vagy csoportok felhasználóit és/vagy csoportjait.

### <a name="to-configure-automatic-user-provisioning-for-britive-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a britek számára az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

1. Az alkalmazások listában válassza ki a **britek** elemet.

    ![A Britség hivatkozása az alkalmazások listájában](common/all-applications.png)

1. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

1. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Kiépítés lap automatikus](common/provisioning-automatic.png)

1. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a brit bérlői URL-címet és a titkos tokent. Kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad csatlakozni tudjanak a britek számára. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a brit fiók rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

1. Az **Értesítés e-mailben** mezőben adja meg annak a személynek vagy csoportnak az e-mail-címét, aki az átadással kapcsolatos hibaüzeneteket kapja, és jelölje be az **E-mail-értesítés küldése hiba esetén** jelölőnégyzetet.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

1. Kattintson a **Mentés** gombra.

1. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a britek** számára lehetőséget.

1. Tekintse át az Azure AD-ből az **attribútum-hozzárendelési** szakaszban szinkronizált felhasználói attribútumokat. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a brit rendszerfrissítési műveletek felhasználói fiókjainak megfeleltetésére szolgálnak. Ha úgy dönt, hogy megváltoztatja a [megfelelő cél attribútumot](../app-provisioning/customize-application-attributes.md), akkor biztosítania kell, hogy a brit API támogassa a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   |Attribútum|Típus|Szűréshez támogatott|
   |---|---|---|
   |userName (Felhasználónév)|Sztring|&check;
   |active|Logikai|
   |displayName|Sztring|
   |cím|Sztring|
   |externalId|Sztring|
   |preferredLanguage|Sztring|
   |name.givenName|Sztring|
   |name.familyName|Sztring|
   |nickName|Sztring|
   |userType|Sztring|
   |területi beállítás|Sztring|
   |timezone|Sztring|
   |e-mailek [type EQ "Home"]. Value|Sztring|
   |e-mailek [type EQ "other"]. Value|Sztring|
   |emails[type eq "work"].value|Sztring|
   |phoneNumbers [type EQ "Home"]. Value|Sztring|
   |phoneNumbers [type EQ "other"]. Value|Sztring|
   |phoneNumbers [type EQ "pager"]. Value|Sztring|
   |phoneNumbers[type eq "work"].value|Sztring|
   |phoneNumbers[type eq "mobile"].value|Sztring|
   |phoneNumbers [type EQ "fax"]. Value|Sztring|
   |címek [type EQ "work"]. formázott|Sztring|
   |címek [type EQ "work"]. streetAddress|Sztring|
   |címek [típus EQ "work"]. helység|Sztring|
   |címek [típus EQ "work"]. régió|Sztring|
   |címek [type EQ "work"]. irányítószám|Sztring|
   |címek [type EQ "work"]. Country|Sztring|
   |címek [type EQ "Home"]. formázott|Sztring|
   |címek [type EQ "Home"]. streetAddress|Sztring|
   |címek [type EQ "Home"]. helység|Sztring|
   |címek [type EQ "Home"]. régió|Sztring|
   |címek [type EQ "Home"]. irányítószám|Sztring|
   |címek [type EQ "Home"]. Country|Sztring|
   |címek [type EQ "other"]. formázott|Sztring|
   |címek [type EQ "other"]. streetAddress|Sztring|
   |címek [type EQ "other"]. helység|Sztring|
   |címek [type EQ "other"]. régió|Sztring|
   |címek [type EQ "other"]. irányítószám|Sztring|
   |címek [type EQ "other"]. Country|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: employeeNumber|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: costCenter|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: szervezet|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: User: Division|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: részleg|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: User: Manager|Referencia|


1. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása a britek** között lehetőséget.

1. Tekintse át az Azure AD-ből az **attribútum-hozzárendelési** szakaszban szinkronizált csoportosítási attribútumokat. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a brit rendszerbeli frissítési műveletekhez tartozó csoportok egyeztetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

      |Attribútum|Típus|Szűréshez támogatott|
      |---|---|---|
      |displayName|Sztring|&check;
      |externalId|Sztring|
      |tagok|Referencia|

1. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

1. Ha engedélyezni szeretné az Azure AD kiépítési szolgáltatást a brit rendszer számára, módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

1. Adja meg azokat a felhasználókat és/vagy csoportokat, akiket ki szeretne építeni a britek számára a **Settings (beállítások** ) szakasz **hatókörében** lévő kívánt értékek kiválasztásával.

    ![Átadási hatókör](common/provisioning-scope.png)

1. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

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