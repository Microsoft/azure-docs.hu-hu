---
title: 'Oktatóanyag: a PrinterLogic SaaS konfigurálása automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és kiépíteni felhasználói fiókjait az Azure AD-ből a PrinterLogic SaaS-be.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 001cfccf-b8a4-46e6-b355-94e8b694b122
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2020
ms.author: Zhchia
ms.openlocfilehash: 6b00067e7f13e609c2999d6949429038e9a7dfd8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96181747"
---
# <a name="tutorial-configure-printerlogic-saas-for-automatic-user-provisioning"></a>Oktatóanyag: a PrinterLogic SaaS konfigurálása automatikus felhasználó-kiépítési szolgáltatáshoz

Ez az oktatóanyag azokat a lépéseket ismerteti, amelyeket a PrinterLogic SaaS és a Azure Active Directory (Azure AD) szolgáltatásban kell elvégezni az automatikus felhasználó-kiépítés konfigurálásához. Ha konfigurálva van, az Azure AD automatikusan kiépíti és kiosztja a felhasználókat és csoportokat az [SaaS PrinterLogic](https://www.printerlogic.com/) az Azure ad kiépítési szolgáltatás használatával. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a PrinterLogic SaaS-ben
> * Felhasználók eltávolítása a PrinterLogic SaaS-ben, ha már nincs szükség hozzáférésre
> * A felhasználói attribútumok szinkronizálása az Azure AD és a PrinterLogic SaaS között
> * Csoportok és csoporttagságok kiépítése a PrinterLogic SaaS-ben
> * [Egyszeri bejelentkezés](./printerlogic-saas-tutorial.md) a PrinterLogic SaaS-be (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Azure AD-bérlő](../develop/quickstart-create-new-tenant.md) 
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](../roles/permissions-reference.md) a kiépítés konfigurálására (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda, alkalmazás tulajdonosa vagy globális rendszergazda). 
* Egy [PrinterLogic SaaS](https://www.printerlogic.com/) -bérlő.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés Az átadás üzembe helyezésének megtervezése
1. Ismerje meg [az átadási szolgáltatás működését](../app-provisioning/user-provisioning.md).
2. Határozza meg, hogy ki lesz [az átadás hatókörében](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Határozza meg, hogy az [Azure ad és a PrinterLogic SaaS milyen adatleképezési](../app-provisioning/customize-application-attributes.md)leképezést szeretne. 

## <a name="step-2-configure-printerlogic-saas-to-support-provisioning-with-azure-ad"></a>2. lépés A PrinterLogic SaaS konfigurálása az Azure AD-vel való kiépítés támogatásához

1. A PrinterLogic-ben navigáljon az **eszközök > beállítások > általános** elemre.

2. Görgessen az **Identity Provider beállításai** szakaszhoz.

3. Kattintson a **scim** lehetőségre.

4. Győződjön meg arról, hogy az **Azure ad** ki van választva a legördülő menüben.

5. Kattintson a **scim jogkivonat előállítása** elemre.

      ![Scim token](media/printer-logic-saas-provisioning-tutorial/token.png)

6. Másolja és mentse a **tulajdonosi jogkivonatot**. Ez az érték a PrinterLogic SaaS-alkalmazás üzembe helyezés lapjának **titkos jogkivonat** mezőjében jelenik meg a Azure Portal.

7. Adja meg a https://gw.app.printercloud.com/{instance_name}/scim/v2 PrinterLogic SaaS-alkalmazás üzembe helyezés lapján a **bérlői URL-cím** mezőben a Azure Portal.

## <a name="step-3-add-printerlogic-saas-from-the-azure-ad-application-gallery"></a>3. lépés A PrinterLogic SaaS hozzáadása az Azure AD Application Galleryből

Vegye fel a PrinterLogic SaaS-t az Azure AD-alkalmazás-katalógusból a PrinterLogic SaaS-re való kiépítés kezelésének megkezdéséhez. Ha korábban már beállította a PrinterLogic SaaS-t az SSO-hoz, használhatja ugyanazt az alkalmazást. Az integráció első tesztelésekor azonban érdemes létrehozni egy külön alkalmazást. Az alkalmazások katalógusból való hozzáadásáról [itt](../manage-apps/add-application-portal.md) tudhat meg többet. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Az átadás hatókörében lévő személyek meghatározása 

Az Azure AD átadási szolgáltatása lehetővé teszi az átadott személyek hatókörének meghatározását az alkalmazáshoz való hozzárendelés és/vagy a felhasználó/csoport attribútumai alapján. Ha a hozzárendelés alapján történő hatókör-meghatározást választja, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet felhasználókat és csoportokat az alkalmazáshoz. Ha csak a felhasználó vagy csoport attribútumai alapján történő hatókörmeghatározást választja, az [itt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) leírt hatókörszűrőt használhatja. 

* Amikor felhasználókat és csoportokat rendel a PrinterLogic SaaS-hez, ki kell választania az **alapértelmezett hozzáféréstől** eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva az átadásból, és az átadási naplókban nem jogosultként lesznek megjelölve. Ha az alkalmazáshoz csak az alapértelmezett hozzáférési szerepkör érhető el, akkor további szerepkörök felvételéhez [frissítheti az alkalmazásjegyzéket](../develop/howto-add-app-roles-in-azure-ad-apps.md). 

* Kezdje kicsiben. Tesztelje a felhasználók és csoportok kis halmazát, mielőtt mindenkire kiterjesztené. Amikor az átadás hatóköre a hozzárendelt felhasználókra és csoportokra van beállítva, ennek szabályozásához egy vagy két felhasználót vagy csoportot rendelhet az alkalmazáshoz. Amikor a hatókör az összes felhasználóra és csoportra van beállítva, meghatározhat egy [attribútumalapú hatókörszűrőt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-printerlogic-saas"></a>5. lépés Automatikus felhasználó-kiépítés beállítása a PrinterLogic SaaS-re 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy TestApp alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

### <a name="to-configure-automatic-user-provisioning-for-printerlogic-saas-in-azure-ad"></a>Automatikus felhasználó-kiépítés konfigurálása az PrinterLogic SaaS-hez az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **PrinterLogic SaaS** elemet.

    ![Az PrinterLogic SaaS-hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Kiépítés lap automatikus](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a PrinterLogic SaaS-bérlő URL-címét és a 2. lépésben beolvasott titkos tokent. Kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad csatlakozhasson a PrinterLogic SaaS szolgáltatáshoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a PrinterLogic SaaS-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Azure AD-jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az **Értesítés e-mailben** mezőben adja meg annak a személynek vagy csoportnak az e-mail-címét, aki az átadással kapcsolatos hibaüzeneteket kapja, és jelölje be az **E-mail-értesítés küldése hiba esetén** jelölőnégyzetet.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza az **Azure Active Directory-felhasználók szinkronizálása az SaaS-PrinterLogic** lehetőséget.

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az PrinterLogic SaaS **-re az attribútum-hozzárendelés** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a PrinterLogic SaaS felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. Ha úgy dönt, hogy megváltoztatja a [megfelelő cél attribútumot](../app-provisioning/customize-application-attributes.md), akkor biztosítania kell, hogy a PRINTERLOGIC SaaS API támogassa a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   |Attribútum|Típus|Szűréshez támogatott|
   |---|---|---|
   |userName (Felhasználónév)|Sztring|&check;|
   |active|Logikai|
   |cím|Sztring|
   |name.givenName|Sztring|
   |name.familyName|Sztring|
   |emails[type eq "work"].value|Sztring|
   |externalId|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: részleg|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: User: Manager|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: printercloud: 2.0: felhasználó: authPin|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: printercloud: 2.0: felhasználó: authPinUser|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: printercloud: 2.0: felhasználó: badgeId|Sztring|

10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása az SaaS-PrinterLogic** lehetőséget.

11. Tekintse át az Azure AD-ből szinkronizált PrinterLogic az **attribútum-hozzárendelési** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a PrinterLogic SaaS-ben lévő csoportok egyeztetésére használhatók frissítési műveletekhez. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

      |Attribútum|Típus|Szűréshez támogatott|
      |---|---|---|
      |displayName|Sztring|&check;|
      |externalId|Sztring|
      |tagok|Referencia|

12. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

13. A PrinterLogic SaaS-hez készült Azure AD-kiépítési szolgáltatás engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket szeretne PrinterLogic SaaS-ként kiépíteni a **Settings (beállítások** ) szakasz **hatókörében** lévő kívánt értékek kiválasztásával.

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