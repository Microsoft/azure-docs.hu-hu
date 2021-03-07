---
title: 'Oktatóanyag: a munkahelyi konfiguráció konfigurálása a Facebookban a felhasználók automatikus kiépítés Azure Active Directory használatával | Microsoft Docs'
description: Megtudhatja, milyen lépéseket kell elvégeznie a munkahelyen a Facebook és a Azure Active Directory (Azure ad) segítségével a felhasználók automatikus kiépítés konfigurálásához.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/28/2020
ms.author: jeedes
ms.openlocfilehash: 98a151c9f740c3ab2f1471f98c7fab83cc848a28
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102438404"
---
# <a name="tutorial-configure-workplace-by-facebook-for-automatic-user-provisioning"></a>Oktatóanyag: a munkahelyi környezet konfigurálása a Facebook használatával a felhasználók automatikus üzembe helyezéséhez

Ez az oktatóanyag azokat a lépéseket ismerteti, amelyeket a Facebook és Azure Active Directory (Azure AD) munkahelyén kell elvégezni a felhasználók automatikus kiépítés konfigurálásához. Ha konfigurálva van, az Azure AD automatikusan kiépíti és kiosztja a felhasználókat és csoportokat a [Facebook](https://work.workplace.com/) számára az Azure ad kiépítési szolgáltatásával. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a munkahelyen Facebook használatával
> * Felhasználók eltávolítása a munkahelyen a Facebookban, ha már nincs szükség hozzáférésre
> * Felhasználói attribútumok szinkronizálása az Azure AD és a munkahely között a Facebook használatával
> * [Egyszeri bejelentkezés](./workplacebyfacebook-tutorial.md) a munkahelyre Facebook használatával (ajánlott)

>[!VIDEO https://www.youtube.com/embed/oF7I0jjCfrY]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Azure AD-bérlő](../develop/quickstart-create-new-tenant.md) 
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](../roles/permissions-reference.md) a kiépítés konfigurálására (például alkalmazás-rendszergazda, Felhőbeli alkalmazás-rendszergazda, alkalmazás tulajdonosa vagy globális rendszergazda)
* A Facebook egyszeri bejelentkezéses előfizetést használó munkaterülete

> [!NOTE]
> Az oktatóanyag lépéseinek teszteléséhez nem javasoljuk éles környezet használatát.

> [!NOTE]
> Ez az integráció az Azure AD USA kormányzati felhőalapú környezetének használatával is elérhető. Ezt az alkalmazást az Azure AD US government Cloud Application Galleryben találja, és ugyanúgy konfigurálhatja, mint a nyilvános felhőben.

Az oktatóanyag lépéseinek teszteléséhez kövesse az alábbi javaslatokat:

- Ne használja éles környezetét, ha szükséges.
- Ha még nem rendelkezik Azure AD-próbaverzióval, [itt](https://azure.microsoft.com/pricing/free-trial/)kérhet egy hónapos próbaverziót.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés Az átadás üzembe helyezésének megtervezése
1. Ismerje meg [az átadási szolgáltatás működését](../app-provisioning/user-provisioning.md).
2. Határozza meg, hogy ki lesz [az átadás hatókörében](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Határozza meg, hogy az [Azure ad és a munkahely milyen adatleképezést szeretne a Facebook által](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-workplace-by-facebook-to-support-provisioning-with-azure-ad"></a>2. lépés A munkahelyi környezet konfigurálása a Facebook használatával az Azure AD-vel való kiépítés támogatásához

A kiépítési szolgáltatás konfigurálása és engedélyezése előtt el kell döntenie, hogy az Azure AD-beli felhasználók és/vagy csoportok milyen felhasználók számára férhetnek hozzá a munkahelyi Facebook-alkalmazáshoz. Miután eldöntötte, az alábbi utasításokat követve rendelheti hozzá ezeket a felhasználókat a munkahelyéhez a Facebook-alkalmazásban:

*   Azt javasoljuk, hogy az üzembe helyezési konfiguráció teszteléséhez egyetlen Azure AD-felhasználó legyen hozzárendelve a munkahelyhez a Facebookban. Később további felhasználókat és/vagy csoportokat is hozzá lehet rendelni.

*   Ha a Facebook által a munkahelyhez rendeli a felhasználót, ki kell választania egy érvényes felhasználói szerepkört. Az "alapértelmezett hozzáférés" szerepkör nem működik a kiépítés során.

## <a name="step-3-add-workplace-by-facebook-from-the-azure-ad-application-gallery"></a>3. lépés Munkaterület felvétele a Facebook használatával az Azure AD Application Galleryből

Az Azure AD-alkalmazás-katalógusban a Facebook használatával is felveheti a munkahelyet, így megkezdheti a Facebook-ban való kiépítés kezelését. Ha korábban már beállította a munkahelyi beállításokat a Facebook által az SSO-hoz, ugyanazt az alkalmazást használhatja. Az integráció első tesztelésekor azonban érdemes létrehozni egy külön alkalmazást. Az alkalmazások katalógusból való hozzáadásáról [itt](../manage-apps/add-application-portal.md) tudhat meg többet.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Az átadás hatókörében lévő személyek meghatározása 

Az Azure AD átadási szolgáltatása lehetővé teszi az átadott személyek hatókörének meghatározását az alkalmazáshoz való hozzárendelés és/vagy a felhasználó/csoport attribútumai alapján. Ha a hozzárendelés alapján történő hatókör-meghatározást választja, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet felhasználókat és csoportokat az alkalmazáshoz. Ha csak a felhasználó vagy csoport attribútumai alapján történő hatókörmeghatározást választja, az [itt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) leírt hatókörszűrőt használhatja. 

* A felhasználók és csoportok a Facebookhoz való hozzárendeléséhez ki kell választania az **alapértelmezett hozzáféréstől** eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva az átadásból, és az átadási naplókban nem jogosultként lesznek megjelölve. Ha az alkalmazáshoz csak az alapértelmezett hozzáférési szerepkör érhető el, akkor további szerepkörök felvételéhez [frissítheti az alkalmazásjegyzéket](../develop/howto-add-app-roles-in-azure-ad-apps.md). 

* Kezdje kicsiben. Tesztelje a felhasználók és csoportok kis halmazát, mielőtt mindenkire kiterjesztené. Amikor az átadás hatóköre a hozzárendelt felhasználókra és csoportokra van beállítva, ennek szabályozásához egy vagy két felhasználót vagy csoportot rendelhet az alkalmazáshoz. Amikor a hatókör az összes felhasználóra és csoportra van beállítva, meghatározhat egy [attribútumalapú hatókörszűrőt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **munkahely a Facebook alapján** lehetőséget.

    ![A munkahelyi Facebook-hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a kiépítési mód legördülő listájáról az automatikus lehetőséggel.](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban kattintson az **Engedélyezés** elemre. A rendszer átirányítja a munkahelyre a Facebook engedélyezési oldalán. Adja meg a munkahelyi Facebook-felhasználónevet, majd kattintson a **Continue (folytatás** ) gombra. Kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad a Facebook segítségével csatlakozhasson a munkahelyhez. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a munkahelyi Facebook-fiók rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Képernyőfelvétel: rendszergazdai hitelesítő adatok párbeszédpanel engedélyezési lehetőséggel.](./media/workplacebyfacebook-provisioning-tutorial/provisioning.png)

    ![engedélyezés](./media/workplacebyfacebook-provisioning-tutorial/workplacelogin.png)

6. Az **Értesítés e-mailben** mezőben adja meg annak a személynek vagy csoportnak az e-mail-címét, aki az átadással kapcsolatos hibaüzeneteket kapja, és jelölje be az **E-mail-értesítés küldése hiba esetén** jelölőnégyzetet.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a munkahelyhez a Facebook** lehetőséget.

9. Tekintse át az Azure AD-ból a munkahelyre szinkronizált felhasználói attribútumokat a Facebook használatával az **attribútum-hozzárendelés** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Facebook által a frissítési műveletekhez használt felhasználói fiókokkal egyeznek meg. Ha úgy dönt, hogy megváltoztatja a [megfelelő célként megadott attribútumot](../app-provisioning/customize-application-attributes.md), akkor a Facebook API-nak az adott attribútum alapján történő szűrését támogató munkaterületet kell biztosítania. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   |Attribútum|Típus|
   |---|---|
   |userName (Felhasználónév)|Sztring|
   |displayName|Sztring|
   |active|Logikai|
   |cím|Logikai|
   |emails[type eq "work"].value|Sztring|
   |name.givenName|Sztring|
   |name.familyName|Sztring|
   |név. formázott|Sztring|
   |címek [type EQ "work"]. formázott|Sztring|
   |címek [type EQ "work"]. streetAddress|Sztring|
   |címek [típus EQ "work"]. helység|Sztring|
   |címek [típus EQ "work"]. régió|Sztring|
   |címek [type EQ "work"]. Country|Sztring|
   |címek [type EQ "work"]. irányítószám|Sztring|
   |címek [type EQ "other"]. formázott|Sztring|
   |phoneNumbers[type eq "work"].value|Sztring|
   |phoneNumbers[type eq "mobile"].value|Sztring|
   |phoneNumbers [type EQ "fax"]. Value|Sztring|
   |externalId|Sztring|
   |preferredLanguage|Sztring|
   |urn:scim:schemas:extension:enterprise:1.0.manager|Sztring|
   |urn:scim:schemas:extension:enterprise:1.0.department|Sztring|
   |urn:scim:schemas:extension:enterprise:1.0.division|Sztring|
   |urn:scim:schemas:extension:enterprise:1.0.organization|Sztring|
   |urn:scim:schemas:extension:enterprise:1.0.costCenter|Sztring|
   |urn:scim:schemas:extension:enterprise:1.0.employeeNumber|Sztring|
   |urn: scim: sémák: bővítmény: Facebook: auth_method: 1.0: auth_method|Sztring|
   |urn: scim: sémák: bővítmény: Facebook: Frontline: 1.0.is_frontline|Logikai|
   |urn: scim: sémák: bővítmény: Facebook: starttermdates: 1.0. startDate|Egész szám|


10. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

11. Az Azure AD-kiépítési szolgáltatás a Facebook számára történő engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

12. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket a Facebook számára szeretne kiépíteni a munkahelyen a **Beállítások** **szakaszban található kívánt** értékek kiválasztásával.

    ![Átadási hatókör](common/provisioning-scope.png)

13. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet a **Beállítások** szakasz **Hatókör** területén meghatározott összes felhasználó és csoport kezdeti szinkronizálási ciklusát elindítja. A kezdeti ciklus elvégzése hosszabb időt vesz igénybe, mint a későbbi ciklusok, amelyek az Azure AD átadási szolgáltatásának futtatása során körülbelül 40 percenként lesznek végrehajtva. 

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Az átadás konfigurálása után a következő erőforrásokkal monitorozhatja az üzemelő példányt:

1. Az [átadási naplókkal](../reports-monitoring/concept-provisioning-logs.md) határozhatja meg, hogy mely felhasználók átadása sikeres, és melyeké sikertelen.
2. A [folyamatjelzőn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) láthatja az átadási ciklus állapotát és azt, hogy mennyi hiányzik még a befejeződéséhez.
3. Ha úgy tűnik, hogy az átadási konfiguráció állapota nem megfelelő, az alkalmazás karanténba kerül. A karanténállapotokról [itt](../app-provisioning/application-provisioning-quarantine-status.md) találhat további információt.

## <a name="troubleshooting-tips"></a>Hibaelhárítási tippek
*  Ha egy felhasználót nem sikerült létrehozni, és egy "1789003" kóddal rendelkező naplózási esemény van, akkor a felhasználó nem ellenőrzött tartományból származik.

## <a name="change-log"></a>Változási napló

* 09/10/2020 – a (z) "Division", "Organization", "costCenter" és "employeeNumber" vállalati attribútumok támogatása. A "startDate", a "auth_method" és a "Frontline" egyéni attribútumok támogatása

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)