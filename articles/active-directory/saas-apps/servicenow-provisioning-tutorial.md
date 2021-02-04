---
title: 'Oktatóanyag: a ServiceNow konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és felépíteni felhasználói fiókokat az Azure AD-ből a ServiceNow.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/10/2019
ms.author: jeedes
ms.openlocfilehash: b3b62e7c16106fd9d94d4a3438331dab4ce8b6e8
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539043"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés ServiceNow konfigurálása

Ez az oktatóanyag ismerteti a ServiceNow és a Azure Active Directory (Azure AD) szolgáltatásban végrehajtott lépéseket az automatikus felhasználó-kiépítés konfigurálásához. Ha az Azure AD konfigurálva van, az automatikusan kiépíti és kiosztja a felhasználókat és csoportokat az Azure AD kiépítési szolgáltatással történő [ServiceNow](https://www.servicenow.com/) . 

A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a ServiceNow-ben
> * Távolítsa el a felhasználókat a ServiceNow, ha nincs szükségük a hozzáférésre
> * Felhasználói attribútumok szinkronizálása az Azure AD és a ServiceNow között
> * Csoportok és csoporttagságok kiépítése a ServiceNow-ben
> * [Egyszeri bejelentkezés](servicenow-tutorial.md) engedélyezése a ServiceNow (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Azure AD-bérlő](../develop/quickstart-create-new-tenant.md) 
* Felhasználói fiók az Azure AD-ben [a](../roles/permissions-reference.md) kiépítés konfigurálásához (alkalmazás-rendszergazda, Felhőbeli alkalmazás rendszergazdája, alkalmazás tulajdonosa vagy globális rendszergazda)
* Calgary vagy magasabb [ServiceNow-példány](https://www.servicenow.com/)
* [ServiceNow Express-példány](https://www.servicenow.com/) (Helsinki vagy újabb)
* Felhasználói fiók a ServiceNow-ben a rendszergazdai szerepkörrel

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés: a kiépítési üzembe helyezés megtervezése
1. Ismerje meg [az átadási szolgáltatás működését](../app-provisioning/user-provisioning.md).
2. Határozza meg, hogy ki lesz [az átadás hatókörében](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Határozza meg, hogy az [Azure ad és a ServiceNow között milyen adatleképezést kell leképezni](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>2. lépés: a ServiceNow konfigurálása az Azure AD-vel való kiépítés támogatására

1. Azonosítsa a ServiceNow-példány nevét. A ServiceNow eléréséhez használt URL-címen megkeresheti a példány nevét. A következő példában a példány neve **dev35214**.

   ![Képernyőkép, amely egy ServiceNow-példányt mutat be.](media/servicenow-provisioning-tutorial/servicenow-instance.png)

2. Hitelesítő adatok beszerzése egy rendszergazda számára a ServiceNow-ben. Nyissa meg a ServiceNow felhasználói profilját, és ellenőrizze, hogy a felhasználó rendelkezik-e rendszergazdai szerepkörrel. 

   ![Képernyőkép, amely a ServiceNow rendszergazdai szerepkörét jeleníti meg.](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)


## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>3. lépés: ServiceNow hozzáadása az Azure AD Application Galleryből

Vegyen fel ServiceNow az Azure AD-alkalmazás-katalógusból a ServiceNow való kiépítés kezelésének megkezdéséhez. Ha korábban már beállította a ServiceNow az egyszeri bejelentkezéshez (SSO), ugyanazt az alkalmazást használhatja. Azt javasoljuk azonban, hogy az integráció teszteléséhez hozzon létre egy külön alkalmazást. [További információ az alkalmazások gyűjteményből való hozzáadásáról](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: annak meghatározása, hogy ki lesz a kiépítés hatóköre 

Az Azure AD kiépítési szolgáltatása lehetővé teszi az alkalmazáshoz való hozzárendelés vagy a felhasználó vagy csoport attribútumai alapján kiépített hatókör kiosztását. Ha úgy dönt, hogy a hatókör ki lesz kiépítve az alkalmazáshoz a hozzárendelés alapján, a lépéseket követve [rendelhet hozzá felhasználókat és csoportokat az alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md). Ha olyan hatókört választ, amely kizárólag a felhasználó vagy csoport attribútumai alapján lesz kiépítve, [egy hatókör-szűrőt is használhat](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

Tartsa szem előtt a következő tippeket:

* Amikor felhasználókat és csoportokat rendel a ServiceNow-hez, ki kell választania az alapértelmezett hozzáféréstől eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva az átadásból, és az átadási naplókban nem jogosultként lesznek megjelölve. Ha az alkalmazás egyetlen szerepköre az alapértelmezett hozzáférési szerepkör, akkor a további szerepkörök hozzáadásához [frissítheti az alkalmazás-jegyzékfájlt](../develop/howto-add-app-roles-in-azure-ad-apps.md) . 

* Kezdje kicsiben. Tesztelje a felhasználók és csoportok kis halmazát, mielőtt mindenkire kiterjesztené. Ha a kiépítés hatóköre a hozzárendelt felhasználókhoz és csoportokhoz van beállítva, ezt úgy szabályozhatja, hogy egy vagy két felhasználót vagy csoportot rendel az alkalmazáshoz. Ha a hatókör minden felhasználóra és csoportra van beállítva, megadhat egy [attribútum-alapú hatókör-szűrőt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>5. lépés: az automatikus felhasználó-kiépítés konfigurálása a ServiceNow 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein a felhasználók és csoportok TestApp való létrehozásához, frissítéséhez és letiltásához. Az Azure AD-ben beállíthatja a konfigurációt a felhasználók és a csoportok hozzárendelései alapján.

Az automatikus felhasználó-kiépítés konfigurálása a ServiceNow az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások**  >  **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelt bemutató képernyőkép.](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **ServiceNow** lehetőséget.

    ![Képernyőkép, amely az alkalmazások listáját jeleníti meg.](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

4. A **kiépítési mód** beállítása **automatikusra**.

    ![Képernyőkép a kiépítési mód legördülő listájáról az automatikus beállítással.](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a ServiceNow rendszergazdai hitelesítő adatait és felhasználónevét. Válassza a **kapcsolat tesztelése** lehetőséget, hogy az Azure ad képes legyen csatlakozni a ServiceNow. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a ServiceNow-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![A szolgáltatás kiépítési lapját bemutató képernyőkép, amelyen megadhatja a rendszergazdai hitelesítő adatokat.](./media/servicenow-provisioning-tutorial/servicenow-provisioning.png)

6. Az **értesítési e-mail** mezőbe írja be annak a személynek vagy csoportnak az e-mail-címét, akinek meg kell kapnia a kiépítési hibajelentési értesítéseket. Ezután jelölje be az **e-mailes értesítés küldése hiba** esetén jelölőnégyzetet.

    ![Képernyőkép az értesítő e-mailek mezőiről.](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a ServiceNow** lehetőséget.

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban lévő ServiceNow. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a ServiceNow felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. 

   Ha úgy dönt, hogy megváltoztatja a [megfelelő cél attribútumot](../app-provisioning/customize-application-attributes.md), akkor biztosítania kell, hogy a ServiceNow API támogassa a felhasználók szűrését az adott attribútum alapján. 
   
   A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása a ServiceNow** lehetőséget.

11. Tekintse át az Azure AD-ből szinkronizált ServiceNow az **attribútum-hozzárendelés** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a ServiceNow tartozó csoportok egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

12. A hatóköri szűrők konfigurálásához tekintse meg a [hatóköri szűrő oktatóanyagának](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)utasításait.

13. Az Azure AD kiépítési szolgáltatás ServiceNow való engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![A kiépítési állapotot bemutató képernyőkép a következőn:.](common/provisioning-toggle-on.png)

14. Adja meg a ServiceNow kiépíteni kívánt felhasználókat és csoportokat a **Settings (beállítások** ) szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![A kiépítési hatókörre vonatkozó választási lehetőségeket bemutató képernyőkép.](common/provisioning-scope.png)

15. Ha készen áll a létesítésre, válassza a **Mentés** lehetőséget.

    ![A kiépítési konfiguráció mentéséhez használt gomb képernyőképe.](common/provisioning-configuration-save.png)

Ez a művelet a **Beállítások** szakasz **Hatókör** területén meghatározott összes felhasználó és csoport kezdeti szinkronizálási ciklusát elindítja. A kezdeti ciklus hosszabb időt vesz igénybe, mint a későbbi ciklusok. A következő ciklusok körülbelül 40 percenként történnek, feltéve, hogy az Azure AD kiépítési szolgáltatás fut. 

## <a name="step-6-monitor-your-deployment"></a>6. lépés: az üzemelő példány figyelése
A kiépítés beállítása után a következő erőforrásokkal figyelheti az üzemelő példányt:

- A [kiépítési naplók](../reports-monitoring/concept-provisioning-logs.md) segítségével határozza meg, hogy mely felhasználók lettek sikeresen kiépítve vagy sikertelenül.
- Tekintse meg a [folyamatjelző sáv](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) állapotát a kiépítési ciklus állapotának megtekintéséhez és a befejezéshez.
- Ha úgy tűnik, hogy az átadási konfiguráció állapota nem megfelelő, az alkalmazás karanténba kerül. [További információ a karantén állapotáról](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="troubleshooting-tips"></a>Hibaelhárítási tippek
* Ha bizonyos attribútumokat (például **részleget** és **helyet**) épít ki a ServiceNow-ben, akkor az értékeknek már léteznie kell a ServiceNow található egyik hivatkozási táblában. Ha nem, akkor **InvalidLookupReference** hibaüzenet jelenik meg. 

   Előfordulhat például, hogy két helyet (Seattle, Los Angeles) és három részleget (Sales, Finance, marketing) tartalmaz a ServiceNow egy bizonyos táblájában. Ha olyan felhasználót próbál kiépíteni, amelynek a részlege "Sales", és amelynek a helye a "Seattle", a felhasználó üzembe helyezése sikeres lesz. Ha olyan felhasználót próbál kiépíteni, amelynek a részlege "Sales", és amelynek a helye "LA", a felhasználó nem lesz kiépítve. A "LA" helyet fel kell venni a ServiceNow található hivatkozási táblába, vagy az Azure AD felhasználói attribútumát frissíteni kell, hogy az megfeleljen a ServiceNow formátumának. 
* Ha **EntryJoiningPropertyValueIsMissing** hibaüzenetet kap, tekintse át az [attribútum-hozzárendeléseket](../app-provisioning/customize-application-attributes.md) a megfelelő attribútum azonosításához. Ennek az értéknek jelen kell lennie a kiépíteni kívánt felhasználón vagy csoportban. 
* A követelmények és korlátozások (például a felhasználó országkódének megadására szolgáló formátum) megismeréséhez tekintse át a [SERVICENOW SOAP API](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html)-t.
* A rendszer alapértelmezés szerint a kiépítési kérelmeket a https://{saját példányának neve}. Service-Now. com/{Table-Name} értékre továbbítja. Ha egyéni bérlői URL-címre van szüksége, a teljes URL-címet megadhatja a példány neveként.
* A **ServiceNowInstanceInvalid** hiba azt jelzi, hogy hiba történt a ServiceNow-példánnyal való kommunikáció során. Itt látható a hiba szövege:
  
  `Details: Your ServiceNow instance name appears to be invalid.  Please provide a current ServiceNow administrative user name and          password along with the name of a valid ServiceNow instance.`                                                              

  Ha kapcsolatban áll a kapcsolódási problémákkal, próbálkozzon a **nem** beállítás kiválasztásával a következő beállításokhoz a ServiceNow-ben:
   
  - **Rendszerbiztonság**  >  **Magas biztonsági beállítások**  >  **Alapszintű hitelesítés megkövetelése a bejövő séma-kérelmekhez**
  - **Rendszertulajdonságok**  >  **Webszolgáltatások**  >  **Alapszintű hitelesítés megkövetelése a bejövő SOAP-kérelmekhez**

     ![A SOAP-kérések engedélyezésének lehetőségét bemutató képernyőkép.](media/servicenow-provisioning-tutorial/servicenow-webservice.png)

  Ha továbbra sem tudja megoldani a problémát, lépjen kapcsolatba a ServiceNow támogatási szolgálatával, és kérje meg, hogy a hibaelhárításhoz kapcsolja be a SOAP-hibakeresést. 

* Az Azure AD-kiépítési szolgáltatás jelenleg bizonyos [IP-címtartományok](../app-provisioning/use-scim-to-provision-users-and-groups.md#ip-ranges)alatt működik. Ha szükséges, korlátozhatja az egyéb IP-tartományokat, és hozzáadhatja ezeket az adott IP-tartományokat az alkalmazás engedélyezési listájához. Ez a módszer lehetővé teszi az Azure AD-kiépítési szolgáltatástól az alkalmazás felé irányuló forgalom áramlását.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)
