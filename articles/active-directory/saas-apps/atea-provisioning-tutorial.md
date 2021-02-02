---
title: 'Oktatóanyag: a Atea konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és kiépíteni felhasználói fiókjait az Azure AD-ből a Atea.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: b788328b-10fd-4eaa-a4bc-909d738d8b8b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2021
ms.author: Zhchia
ms.openlocfilehash: 51410bd86fa9679aea76f6d5c48f267ddec79026
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430696"
---
# <a name="tutorial-configure-atea-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés Atea konfigurálása

Ez az oktatóanyag leírja, milyen lépéseket kell tennie a Atea és a Azure Active Directory (Azure AD) szolgáltatásban az automatikus felhasználó-kiépítés konfigurálásához. Ha konfigurálva van, az Azure AD automatikusan kiépíti és kiosztja a felhasználókat és csoportokat az Azure AD kiépítési szolgáltatás [Atea](https://www.atea.com/) . A szolgáltatás működésének módjáról és a gyakran feltett kérdésekre vonatkozó részletes információkért tekintse meg a felhasználók kiépítésének [automatizálását és az SaaS-alkalmazások Azure Active Directory használatával történő](../manage-apps/user-provisioning.md)megszüntetését ismertető témakört. 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a Atea-ben
> * Felhasználók eltávolítása a Atea-ben, ha már nincs szükség hozzáférésre
> * Felhasználói attribútumok szinkronizálása az Azure AD és a Atea között

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Azure AD-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) a kiépítés konfigurálására (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda, alkalmazás tulajdonosa vagy globális rendszergazda). 
* Rendszergazdai jogosultságokkal rendelkező felhasználói fiók a Atea-ben.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés Az átadás üzembe helyezésének megtervezése
1. Ismerje meg [az átadási szolgáltatás működését](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Határozza meg, hogy ki lesz [az átadás hatókörében](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Határozza meg, hogy az [Azure ad és a Atea között milyen adatleképezést kell leképezni](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-atea-to-support-provisioning-with-azure-ad"></a>2. lépés Atea konfigurálása az Azure AD-vel való kiépítés támogatásához

Az Iris intranet konfigurálásához az Azure AD-vel való kiépítés támogatásához le kell kérnie a **bérlői URL-címet** és a **titkos jogkivonatot** , ha egy e-mailt küld a [Atea támogatási csapatának](mailto:servicedesk@atea.dk) Ezek az értékek a **titkos jogkivonat** és a **bérlői URL-cím** mezőben jelennek meg a Atea alkalmazásának Azure Portalban való kiépítés lapján.

## <a name="step-3-add-atea-from-the-azure-ad-application-gallery"></a>3. lépés Atea hozzáadása az Azure AD Application Galleryből

Vegyen fel Atea az Azure AD-alkalmazás-katalógusból a Atea való kiépítés kezelésének megkezdéséhez. Ha korábban már beállította a Atea-t az egyszeri bejelentkezéshez, használhatja ugyanazt az alkalmazást. Azonban javasoljuk, hogy hozzon létre egy külön alkalmazást, amikor először teszteli az integrációt. Az alkalmazások katalógusból való hozzáadásáról [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) tudhat meg többet. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Az átadás hatókörében lévő személyek meghatározása 

Az Azure AD kiépítési szolgáltatása lehetővé teszi az alkalmazáshoz való hozzárendelés és a felhasználó és a csoport attribútumai alapján kiépített hatókör kiosztását. Ha a hozzárendelés alapján történő hatókör-meghatározást választja, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet felhasználókat és csoportokat az alkalmazáshoz. Ha csak a felhasználó vagy csoport attribútumai alapján történő hatókörmeghatározást választja, az [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) leírt hatókörszűrőt használhatja. 

* Felhasználók és csoportok Atea való hozzárendeléséhez ki kell választania az **alapértelmezett hozzáféréstől** eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva az átadásból, és az átadási naplókban nem jogosultként lesznek megjelölve. Ha az alkalmazás egyetlen szerepköre az alapértelmezett hozzáférési szerepkör, akkor a többi szerepkör hozzáadásához [frissítheti az alkalmazás-jegyzékfájlt](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) . 

* Kezdje kicsiben. Tesztelje a felhasználók és csoportok kis halmazát, mielőtt mindenkire kiterjesztené. Ha a kiépítés hatóköre a hozzárendelt felhasználókhoz és csoportokhoz van beállítva, akkor azt úgy szabályozhatja, hogy egy vagy két felhasználót vagy csoportot rendel hozzá az alkalmazáshoz. Amikor a hatókör az összes felhasználóra és csoportra van beállítva, meghatározhat egy [attribútumalapú hatókörszűrőt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-atea"></a>5. lépés Automatikus felhasználó-kiépítés beállítása a Atea 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és csoportos hozzárendeléseken alapuló felhasználókat és csoportokat hozzon létre, frissítsen és tiltsa le az Atea-ben.

### <a name="to-configure-automatic-user-provisioning-for-atea-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a Atea az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Atea** lehetőséget.

    ![Az Atea hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Kiépítés lap automatikus](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban válassza az **Engedélyezés** lehetőséget. Megnyílik egy új böngészőablakban egy Atea bejelentkezési párbeszédpanel.

     ![Atea engedélyezése](media/atea-provisioning-tutorial/provisioning-authorize.png)

6. A Atea bejelentkezési párbeszédpaneljén jelentkezzen be a Atea-bérlőbe, és ellenőrizze az identitását.
       
      ![Atea bejelentkezési párbeszédpanel](media/atea-provisioning-tutorial/atea-login.png)

7. Az 5. és a 6. lépés elvégzése után kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad csatlakozhasson a Atea. Ha a kapcsolat meghiúsul, ellenőrizze, hogy a Atea rendelkezik-e rendszergazdai jogosultságokkal, és próbálkozzon újra.
        
      ![Atea-tesztelési kapcsolatok](media/atea-provisioning-tutorial/test-connection.png)

8. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hiba értesítéseit. Ezután jelölje be az **e-mail-értesítés küldése hiba** esetén jelölőnégyzetet.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

9. Kattintson a **Mentés** gombra.

10. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a Atea** lehetőséget.

11. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban lévő Atea. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Atea felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. Ha úgy dönt, hogy megváltoztatja a [megfelelő cél attribútumot](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes), akkor biztosítania kell, hogy a Atea API támogassa a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

      |Attribútum|Típus|Szűréshez támogatott|
      |---|---|---|
      |userName (Felhasználónév)|Sztring|&check;|
      |active|Logikai|
      |emails[type eq "work"].value|Sztring|
      |name.givenName|Sztring|
      |name.familyName|Sztring|
      |név. formázott|Sztring|
      |phoneNumbers[type eq "mobile"].value|Sztring|
      |területi beállítás|Sztring|
      |nickName|Sztring|

12. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

13. Az Azure AD-kiépítési szolgáltatás Atea való engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg a Atea kiépíteni kívánt felhasználókat és csoportokat a **Settings (beállítások** ) szakaszban található **hatókör** megfelelő értékének kiválasztásával.

    ![Átadási hatókör](common/provisioning-scope.png)

15. Ha készen áll a kiépítésre, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet a **Beállítások** szakasz **Hatókör** területén meghatározott összes felhasználó és csoport kezdeti szinkronizálási ciklusát elindítja. A kezdeti ciklus hosszabb időt vesz igénybe, mint a következő ciklusok, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. 

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Az átadás konfigurálása után a következő erőforrásokkal monitorozhatja az üzemelő példányt:

* A [kiépítési naplók](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) segítségével határozza meg, hogy mely felhasználók lettek sikeresen kiépítve vagy sikertelenül.
* Ellenőrizze a [folyamatjelző sáv](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) állapotát a kiépítési ciklus állapotának megtekintéséhez és a befejezéshez.
* Ha úgy tűnik, hogy az átadási konfiguráció állapota nem megfelelő, az alkalmazás karanténba kerül. A karanténállapotokról [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status) találhat további információt.  

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../manage-apps/check-status-user-account-provisioning.md)
