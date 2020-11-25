---
title: 'Oktatóanyag: a GroupTalk konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és kiépíteni felhasználói fiókjait az Azure AD-ből a GroupTalk.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: e537d393-2724-450f-9f5b-4611cdc9237c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2020
ms.author: Zhchia
ms.openlocfilehash: 107ce33f753b275f694558ec1fec2f07e2316b36
ms.sourcegitcommit: 2e9643d74eb9e1357bc7c6b2bca14dbdd9faa436
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96031282"
---
# <a name="tutorial-configure-grouptalk-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés GroupTalk konfigurálása

Ez az oktatóanyag azokat a lépéseket ismerteti, amelyeket a GroupTalk és a Azure Active Directory (Azure AD) szolgáltatásban kell végrehajtania az automatikus felhasználó-kiépítés konfigurálásához. Ha konfigurálva van, az Azure AD automatikusan kiépíti és kiosztja a felhasználókat és csoportokat az Azure AD kiépítési szolgáltatás [GroupTalk](https://www.grouptalk.com/) . A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a GroupTalk-ben
> * Felhasználók eltávolítása a GroupTalk-ben, ha már nincs szükség hozzáférésre
> * Felhasználói attribútumok szinkronizálása az Azure AD és a GroupTalk között
> * Csoportok és csoporttagságok kiépítése a GroupTalk-ben

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Azure AD-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) a kiépítés konfigurálására (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda, alkalmazás tulajdonosa vagy globális rendszergazda). 
* Rendszergazdai jogosultságokkal rendelkező felhasználói fiók a GroupTalk-ben.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés Az átadás üzembe helyezésének megtervezése
1. Ismerje meg [az átadási szolgáltatás működését](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Határozza meg, hogy ki lesz [az átadás hatókörében](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Határozza meg, hogy az [Azure ad és a GroupTalk között milyen adatleképezést kell leképezni](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-grouptalk-to-support-provisioning-with-azure-ad"></a>2. lépés GroupTalk konfigurálása az Azure AD-vel való kiépítés támogatásához

1. support@grouptalk.comAz Azure ad-vel integrálni kívánt **bérlői névvel** és **azonosítóval** elérheti a GroupTalk-támogatást.
2. Amikor értesítést kapott arról, hogy az Azure AD-integrációhoz szükséges beállítás elkészült, jelentkezzen be a GroupTalk-Rendszergazdába, és navigáljon a szervezet nézetéhez. 
3. Az Azure AD-integráció konfigurációs elemének láthatónak kell lennie. Kattintson rá, hogy ellenőrizze a **bérlő nevét** és **azonosítóját**  a JWT beszerzéséhez **(titkos jogkivonat)**. 
4. A GroupTalk bérlői URL-címe: `https://api.grouptalk.com/api/scim/` . A **bérlői URL-cím** és az előző lépésben beolvasott **titkos jogkivonat** bekerül a GroupTalk alkalmazás üzembe helyezés lapján a Azure Portal. 

## <a name="step-3-add-grouptalk-from-the-azure-ad-application-gallery"></a>3. lépés GroupTalk hozzáadása az Azure AD Application Galleryből

Vegyen fel **GroupTalk** az Azure ad-alkalmazás-katalógusból a GroupTalk való kiépítés kezelésének megkezdéséhez.

1. Kattintson a **Feliratkozás a GroupTalk** gombra, amely átirányítja a GroupTalk felügyeleti alkalmazásra.
2. Ha már bejelentkezett a GroupTalk-be, akkor jelentkezzen be, és lépjen be a bejelentkezési képernyőre. Válassza az Azure AD fület, majd kattintson a **Bejelentkezés** gombra.

    ![GroupTalk](media/grouptalk-provisioning-tutorial/login.png)

3. Jelentkezzen be az AD rendszergazdai fiókjával, és fogadja el a GroupTalk alkalmazás hozzáférési jogosultságait. Ezt követően hibaüzenet jelenik meg, amely azt jelzi, hogy a felhasználó nincs jelen. Ez azért várható, mert a felhasználó még nincs kiépítve a GroupTalk, de most már hozzáadta a GroupTalk a bérlőhöz.
4. Lépjen vissza a Azure Portalra, és ellenőrizze, hogy a **GroupTalk** most már hozzá van-e adva a vállalati alkalmazásokhoz.

Az alkalmazások katalógusból való hozzáadásáról [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) tudhat meg többet. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Az átadás hatókörében lévő személyek meghatározása 

Az Azure AD átadási szolgáltatása lehetővé teszi az átadott személyek hatókörének meghatározását az alkalmazáshoz való hozzárendelés és/vagy a felhasználó/csoport attribútumai alapján. Ha a hozzárendelés alapján történő hatókör-meghatározást választja, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet felhasználókat és csoportokat az alkalmazáshoz. Ha csak a felhasználó vagy csoport attribútumai alapján történő hatókörmeghatározást választja, az [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) leírt hatókörszűrőt használhatja. 

* Felhasználók és csoportok GroupTalk való hozzárendeléséhez ki kell választania az **alapértelmezett hozzáféréstől** eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva az átadásból, és az átadási naplókban nem jogosultként lesznek megjelölve. Ha az alkalmazáshoz csak az alapértelmezett hozzáférési szerepkör érhető el, akkor további szerepkörök felvételéhez [frissítheti az alkalmazásjegyzéket](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps). 

* Kezdje kicsiben. Tesztelje a felhasználók és csoportok kis halmazát, mielőtt mindenkire kiterjesztené. Amikor az átadás hatóköre a hozzárendelt felhasználókra és csoportokra van beállítva, ennek szabályozásához egy vagy két felhasználót vagy csoportot rendelhet az alkalmazáshoz. Amikor a hatókör az összes felhasználóra és csoportra van beállítva, meghatározhat egy [attribútumalapú hatókörszűrőt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-grouptalk"></a>5. lépés Automatikus felhasználó-kiépítés beállítása a GroupTalk 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy TestApp alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

### <a name="to-configure-automatic-user-provisioning-for-grouptalk-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a GroupTalk az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **GroupTalk** lehetőséget.

    ![Az GroupTalk hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Kiépítés lap automatikus](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a GroupTalk bérlői URL-címét és a titkos jogkivonatot, amely korábban a 2. lépésben lett lekérve. Kattintson a **kapcsolat tesztelése** lehetőségre, hogy az Azure ad képes legyen csatlakozni a GroupTalk. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a GroupTalk-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra. A 2. lépésben leírtak szerint mindig kérhet új titkos jogkivonatot.

    ![Jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az **Értesítés e-mailben** mezőben adja meg annak a személynek vagy csoportnak az e-mail-címét, aki az átadással kapcsolatos hibaüzeneteket kapja, és jelölje be az **E-mail-értesítés küldése hiba esetén** jelölőnégyzetet.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a GroupTalk** lehetőséget.

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban lévő GroupTalk. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a GroupTalk felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. Ha úgy dönt, hogy módosítja a [megfelelő cél attribútumot](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), akkor biztosítania kell, hogy a GroupTalk API támogassa a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   |Attribútum|Típus|Szűréshez támogatott|
   |---|---|---|
   |userName (Felhasználónév)|Sztring|&check;|
   |phoneNumbers[type eq "mobile"].value|Sztring|&check;|
   |emails[type eq "work"].value|Sztring|&check;|
   |active|Logikai|
   |name.givenName|Sztring|
   |name.familyName|Sztring|
   |externalId|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: costCenter|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: szervezet|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: grouptalk: 2.0: felhasználó: Label1|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: grouptalk: 2.0: felhasználó: Label2|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: grouptalk: 2.0: felhasználó: címke3|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: grouptalk: 2.0: felhasználó: Label4|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: grouptalk: 2.0: felhasználó: Label5|Sztring|


10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása a GroupTalk** lehetőséget.

11. Tekintse át az Azure AD-ből szinkronizált GroupTalk az **attribútum-hozzárendelés** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a GroupTalk tartozó csoportok egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

      |Attribútum|Típus|Szűréshez támogatott|
      |---|---|---|
      |displayName|Sztring|&check;|
      |tagok|Referencia|
      |externalId|Sztring|      
      |urn: IETF: params: scim: sémák: bővítmény: grouptalk: 2.0: Csoport: Leírás|Sztring|

12. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

13. Az Azure AD-kiépítési szolgáltatás GroupTalk való engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg a GroupTalk kiépíteni kívánt felhasználókat és/vagy csoportokat a **Settings (beállítások** ) szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Átadási hatókör](common/provisioning-scope.png)

15. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet a **Beállítások** szakasz **Hatókör** területén meghatározott összes felhasználó és csoport kezdeti szinkronizálási ciklusát elindítja. A kezdeti ciklus elvégzése hosszabb időt vesz igénybe, mint a későbbi ciklusok, amelyek az Azure AD átadási szolgáltatásának futtatása során körülbelül 40 percenként lesznek végrehajtva. 

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Az átadás konfigurálása után a következő erőforrásokkal monitorozhatja az üzemelő példányt:

1. Az [átadási naplókkal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) határozhatja meg, hogy mely felhasználók átadása sikeres, és melyeké sikertelen.
2. A [folyamatjelzőn](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) láthatja az átadási ciklus állapotát és azt, hogy mennyi hiányzik még a befejeződéséhez.
3. Ha úgy tűnik, hogy az átadási konfiguráció állapota nem megfelelő, az alkalmazás karanténba kerül. A karanténállapotokról [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status) találhat további információt.
4. A GroupTalk támogatásával további üzembe helyezési naplók állíthatók be a GroupTalk-rendszergazda egyéni jelentéseiként. Ezek további tippeket adhatnak arról, hogy a felhasználók és a csoportok miért nem tudnak megfelelően kiépíteni.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../manage-apps/check-status-user-account-provisioning.md)
