---
title: 'Oktatóanyag: az Adobe Identity Management konfigurálása automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és kiépíteni felhasználói fiókjait az Azure AD-ből az Adobe Identity Management szolgáltatásba.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 6ae05dc7-1265-44b4-a20c-512b5218b9d1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2020
ms.author: Zhchia
ms.openlocfilehash: 7b4dd01b74f81318338196a40949d339f13d16da
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97673300"
---
# <a name="tutorial-configure-adobe-identity-management-for-automatic-user-provisioning"></a>Oktatóanyag: az Adobe Identity Management konfigurálása automatikus felhasználó-kiépítési szolgáltatáshoz

Ez az oktatóanyag azokat a lépéseket ismerteti, amelyeket az Adobe Identity Management és a Azure Active Directory (Azure AD) alkalmazásban kell elvégezni az automatikus felhasználó-kiépítés konfigurálásához. A konfigurálást követően az Azure AD automatikusan kiépíti és kiosztja a felhasználókat és csoportokat az Adobe Identity Management szolgáltatásban az Azure AD-létesítési szolgáltatás használatával. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása az Adobe Identity Management szolgáltatásban
> * Felhasználók eltávolítása az Adobe Identity Management szolgáltatásban, ha már nincs szükség hozzáférésre
> * A felhasználói attribútumok szinkronizálása az Azure AD és az Adobe Identity Management között
> * Csoportok és csoporttagságok kiépítése az Adobe Identity Management szolgáltatásban
> * Egyszeri bejelentkezés az Adobe Identity Management szolgáltatásba (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Egy Azure AD-bérlő](../develop/quickstart-create-new-tenant.md).
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](../roles/permissions-reference.md) a kiépítés konfigurálására (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda, alkalmazás tulajdonosa vagy globális rendszergazda). 
* Egy összevont címtár az [Adobe felügyeleti konzolon](https://adminconsole.adobe.com/) ellenőrzött tartományokkal.

> [!NOTE]
> Ha a szervezet a felhasználó-szinkronizáló eszközt vagy egy UMAPI-integrációt használ, először szüneteltetni kell az integrációt. Ezután adja hozzá az Azure AD automatikus kiépítési szolgáltatását az Azure Portalon a felhasználók felügyeletének automatizálásához. Ha az Azure AD automatikus kiépítés konfigurálva van és fut, teljes mértékben eltávolíthatja a felhasználó-szinkronizáló eszközt vagy a UMAPI-integrációt.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés Az átadás üzembe helyezésének megtervezése
1. Ismerje meg [az átadási szolgáltatás működését](../app-provisioning/user-provisioning.md).
2. Határozza meg, hogy ki lesz [az átadás hatókörében](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Határozza meg az [Azure ad és az Adobe Identity Management közötti leképezési](../app-provisioning/customize-application-attributes.md)adatok körét. 

## <a name="step-2-configure-adobe-identity-management-to-support-provisioning-with-azure-ad"></a>2. lépés Az Adobe Identity Management konfigurálása az Azure AD-vel való kiépítés támogatásához

1. Jelentkezzen be az [Adobe felügyeleti konzolba](https://adminconsole.adobe.com/). Navigáljon a **beállítások > könyvtár részletei > szinkronizálás** elemre. 

2. Kattintson a **szinkronizálás hozzáadása** lehetőségre.

    ![Hozzáadás](media/adobe-identity-management-provisioning-tutorial/add-sync.png)

3. Válassza **a felhasználók szinkronizálása Microsoft Azure** lehetőséget, majd kattintson a **tovább** gombra.

    ![Képernyőfelvétel: a "felhasználók szinkronizálása Microsoft Azure Active Directory" beállítás látható.](media/adobe-identity-management-provisioning-tutorial/sync-users.png)

4. Másolja és mentse a **bérlői URL-címet** és a **titkos jogkivonatot**. Ezek az értékek a **bérlői URL-cím** és a **titkos jogkivonat** mezőiben jelennek meg az Adobe Identity Management alkalmazás üzembe helyezés lapján a Azure Portal.

    ![Sync](media/adobe-identity-management-provisioning-tutorial/token.png)

## <a name="step-3-add-adobe-identity-management-from-the-azure-ad-application-gallery"></a>3. lépés Adobe Identity Management hozzáadása az Azure AD Application Galleryből

Az Adobe Identity Management az Azure AD-alkalmazás-katalógusból való hozzáadásával megkezdheti az Adobe Identity Management kiépítés kezelését. Ha korábban már telepítette az Adobe Identity managementet az egyszeri bejelentkezéshez, ugyanazt az alkalmazást használhatja. Az integráció első tesztelésekor azonban érdemes létrehozni egy külön alkalmazást. Az alkalmazások katalógusból való hozzáadásáról [itt](../manage-apps/add-application-portal.md) tudhat meg többet. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Az átadás hatókörében lévő személyek meghatározása 

Az Azure AD átadási szolgáltatása lehetővé teszi az átadott személyek hatókörének meghatározását az alkalmazáshoz való hozzárendelés és/vagy a felhasználó/csoport attribútumai alapján. Ha a hozzárendelés alapján történő hatókör-meghatározást választja, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet felhasználókat és csoportokat az alkalmazáshoz. Ha csak a felhasználó vagy csoport attribútumai alapján történő hatókörmeghatározást választja, az [itt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) leírt hatókörszűrőt használhatja. 

* Amikor felhasználókat és csoportokat rendel az Adobe Identity Managementhez, ki kell választania az **alapértelmezett hozzáféréstől** eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva az átadásból, és az átadási naplókban nem jogosultként lesznek megjelölve. Ha az alkalmazáshoz csak az alapértelmezett hozzáférési szerepkör érhető el, akkor további szerepkörök felvételéhez [frissítheti az alkalmazásjegyzéket](../develop/howto-add-app-roles-in-azure-ad-apps.md). 

* Kezdje kicsiben. Tesztelje a felhasználók és csoportok kis halmazát, mielőtt mindenkire kiterjesztené. Amikor az átadás hatóköre a hozzárendelt felhasználókra és csoportokra van beállítva, ennek szabályozásához egy vagy két felhasználót vagy csoportot rendelhet az alkalmazáshoz. Amikor a hatókör az összes felhasználóra és csoportra van beállítva, meghatározhat egy [attribútumalapú hatókörszűrőt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-adobe-identity-management"></a>5. lépés Az automatikus felhasználó-kiépítés konfigurálása az Adobe Identity Management szolgáltatásban 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy TestApp alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

### <a name="to-configure-automatic-user-provisioning-for-adobe-identity-management-in-azure-ad"></a>Az Azure AD-beli Adobe Identity Management automatikus felhasználói üzembe helyezésének konfigurálása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza az **Adobe Identity Management** lehetőséget.

    ![Az Adobe Identity Management hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Kiépítés lap automatikus](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg az Adobe Identity Management-bérlői URL-címét és a titkos tokent, amely korábban a 2. lépésben lett lekérve. Kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad csatlakozhasson az Adobe Identity Management szolgáltatáshoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy az Adobe Identity Management-fiók rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az **Értesítés e-mailben** mezőben adja meg annak a személynek vagy csoportnak az e-mail-címét, aki az átadással kapcsolatos hibaüzeneteket kapja, és jelölje be az **E-mail-értesítés küldése hiba esetén** jelölőnégyzetet.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása az Adobe Identity Management** szolgáltatásba lehetőséget.

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok az Adobe Identity Management felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. Ha úgy dönt, hogy megváltoztatja a [megfelelő cél attribútumot](../app-provisioning/customize-application-attributes.md), akkor biztosítania kell, hogy az Adobe Identity Management API támogassa a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   |Attribútum|Típus|
   |---|---|
   |userName (Felhasználónév)|Sztring|
   |emails[type eq "work"].value|Sztring|
   |active|Logikai|
   |címek [type EQ "work"]. Country|Sztring|
   |name.givenName|Sztring|
   |name.familyName|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Adobe: 2.0: felhasználó: emailAliases|Sztring|

10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása az Adobe Identity Management** szolgáltatásba lehetőséget.

11. Tekintse át az Azure AD-ből szinkronizált csoportok attribútumait az **attribútum-hozzárendelési** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok az Adobe Identity Management szolgáltatásban a frissítési műveletekhez használt csoportok egyeztetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

      |Attribútum|Típus|
      |---|---|
      |displayName|Sztring|
      |tagok|Referencia|

12. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

13. Ha engedélyezni szeretné az Azure AD-kiépítési szolgáltatást az Adobe Identity Management szolgáltatáshoz, módosítsa a **kiépítési állapotot** **a következőre** a **Settings (beállítások** ) szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket az Adobe Identity Management szolgáltatásban szeretne kiépíteni, ehhez válassza a **Beállítások** szakaszban lévő kívánt értékeket a **hatókörben** .

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