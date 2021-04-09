---
title: 'Oktatóanyag: a Olfeo SAAS konfigurálása automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és kiépíteni felhasználói fiókjait az Azure AD-ből a Olfeo SAAS-be.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 5f6b0320-dfe7-451c-8cd8-6ba7f2e40434
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2021
ms.author: Zhchia
ms.openlocfilehash: b74175c7847bb19aa9410edd613afbfe1d762d05
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105549294"
---
# <a name="tutorial-configure-olfeo-saas-for-automatic-user-provisioning"></a>Oktatóanyag: a Olfeo SAAS konfigurálása automatikus felhasználó-kiépítési szolgáltatáshoz

Ez az oktatóanyag leírja, milyen lépéseket kell tennie a Olfeo SAAS és a Azure Active Directory (Azure AD) szolgáltatásban az automatikus felhasználó-kiépítés konfigurálásához. Ha konfigurálva van, az Azure AD automatikusan kiépíti és kiosztja a felhasználókat és csoportokat az [SaaS Olfeo](https://www.olfeo.com) az Azure ad kiépítési szolgáltatás használatával. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a Olfeo SAAS-ben
> * Felhasználók eltávolítása a Olfeo SAAS-ben, ha már nincs szükség hozzáférésre
> * A felhasználói attribútumok szinkronizálása az Azure AD és a Olfeo SAAS között
> * Csoportok és csoporttagságok kiépítése a Olfeo SAAS-ben
> * [Egyszeri bejelentkezés](olfeo-saas-tutorial.md) a Olfeo SaaS-be (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Azure AD-bérlő](../develop/quickstart-create-new-tenant.md) 
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](../roles/permissions-reference.md) a kiépítés konfigurálására (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda, alkalmazás tulajdonosa vagy globális rendszergazda). 
* Egy [OLFEO SaaS-bérlő](https://www.olfeo.com/).
* Felhasználói fiók a Olfeo SAAS-ben rendszergazdai engedélyekkel.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés Az átadás üzembe helyezésének megtervezése

1. Ismerje meg [az átadási szolgáltatás működését](../app-provisioning/user-provisioning.md).
1. Határozza meg, hogy ki lesz [az átadás hatókörében](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Határozza meg, hogy az [Azure ad és a OLFEO SaaS milyen adatleképezési](../app-provisioning/customize-application-attributes.md)leképezést szeretne.

## <a name="step-2-configure-olfeo-saas-to-support-provisioning-with-azure-ad"></a>2. lépés A Olfeo SAAS konfigurálása az Azure AD-vel való kiépítés támogatásához

1. Jelentkezzen be a Olfeo SAAS felügyeleti konzolra. 
1. Navigáljon a **Configuration > Annuaires**.
1. Hozzon létre egy új könyvtárat, és nevezze el.
1. Válassza az **Azure** Provider lehetőséget, majd kattintson a **CR er** elemre az új könyvtár mentéséhez. 
1. Navigáljon a **szinkronizáció** lapra a **bérlői URL-cím** és a **titkos Brigitta** megtekintéséhez. Ezeket az értékeket a rendszer a **bérlői URL-cím** és a **titkos jogkivonat** mezőibe másolja és beilleszti a Olfeo SaaS-alkalmazás létesítés lapján a Azure Portal.

## <a name="step-3-add-olfeo-saas-from-the-azure-ad-application-gallery"></a>3. lépés A Olfeo SAAS hozzáadása az Azure AD Application Galleryből

Vegye fel a Olfeo SAAS-t az Azure AD-alkalmazás-katalógusból a Olfeo SAAS-re való kiépítés kezelésének megkezdéséhez. Ha korábban már beállította a Olfeo SAAS-t az SSO-hoz, használhatja ugyanazt az alkalmazást. Azonban javasoljuk, hogy hozzon létre egy külön alkalmazást, amikor először teszteli az integrációt. Az alkalmazások katalógusból való hozzáadásáról [itt](../manage-apps/add-gallery-app.md) tudhat meg többet. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Az átadás hatókörében lévő személyek meghatározása 

Az Azure AD átadási szolgáltatása lehetővé teszi az átadott személyek hatókörének meghatározását az alkalmazáshoz való hozzárendelés és/vagy a felhasználó/csoport attribútumai alapján. Ha a hozzárendelés alapján történő hatókör-meghatározást választja, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet felhasználókat és csoportokat az alkalmazáshoz. Ha csak a felhasználó vagy csoport attribútumai alapján történő hatókörmeghatározást választja, az [itt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) leírt hatókörszűrőt használhatja. 

* Amikor felhasználókat és csoportokat rendel a Olfeo SAAS-hez, ki kell választania az **alapértelmezett hozzáféréstől** eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva az átadásból, és az átadási naplókban nem jogosultként lesznek megjelölve. Ha az alkalmazás egyetlen szerepköre az alapértelmezett hozzáférési szerepkör, akkor a további szerepkörök hozzáadásához [frissítheti az alkalmazás-jegyzékfájlt](../develop/howto-add-app-roles-in-azure-ad-apps.md) . 

* Kezdje kicsiben. Tesztelje a felhasználók és csoportok kis halmazát, mielőtt mindenkire kiterjesztené. Amikor az átadás hatóköre a hozzárendelt felhasználókra és csoportokra van beállítva, ennek szabályozásához egy vagy két felhasználót vagy csoportot rendelhet az alkalmazáshoz. Amikor a hatókör az összes felhasználóra és csoportra van beállítva, meghatározhat egy [attribútumalapú hatókörszűrőt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-olfeo-saas"></a>5. lépés Automatikus felhasználó-kiépítés beállítása a Olfeo SAAS-re 

Ez a szakasz végigvezeti az Azure AD-létesítési szolgáltatás konfigurálásának lépésein az Azure AD-beli felhasználói és csoportos hozzárendelések alapján a Olfeo SAAS-alkalmazásban lévő felhasználók és csoportok létrehozásához, frissítéséhez és letiltásához.

### <a name="to-configure-automatic-user-provisioning-for-olfeo-saas-in-azure-ad"></a>Automatikus felhasználó-kiépítés konfigurálása az Olfeo SAAS-hez az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

1. Az alkalmazások listában válassza a **OLFEO SaaS** elemet.

    ![Az Olfeo SAAS-hivatkozás az alkalmazások listájában](common/all-applications.png)

1. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

1.  Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Kiépítés lap automatikus](common/provisioning-automatic.png)

1. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a Olfeo SaaS **-bérlő URL-címét** és a **titkos jogkivonat** adatait. Válassza a **kapcsolat tesztelése** lehetőséget, hogy az Azure ad képes legyen csatlakozni a Olfeo SaaS-hez. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Olfeo SAAS-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

1. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hiba értesítéseit. Jelölje be az **e-mail-értesítés küldése hiba** esetén jelölőnégyzetet.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

1. Kattintson a **Mentés** gombra.

1. A **leképezések** szakaszban válassza az **Azure Active Directory-felhasználók SZINKRONIZÁLÁSa az SaaS-Olfeo** lehetőséget.

1. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az Olfeo SAAS-ben az **attribútum-hozzárendelés** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Olfeo SaaS felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. Ha módosítja a [megfeleltetési cél attribútumot](../app-provisioning/customize-application-attributes.md), gondoskodnia kell arról, hogy a OLFEO SaaS API támogassa a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez válassza a **Mentés** lehetőséget.

   |Attribútum|Típus|Szűréshez támogatott|
   |---|---|---|
   |userName (Felhasználónév)|Sztring|&check;|
   |displayName|Sztring|
   |active|Logikai|
   |emails[type eq "work"].value|Sztring|
   |preferredLanguage|Sztring|
   |name.givenName|Sztring|
   |name.familyName|Sztring|
   |név. formázott|Sztring|
   |externalId|Sztring|  

1. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása az SaaS-Olfeo** lehetőséget.

1. Tekintse át az Azure AD-ből szinkronizált Olfeo az **attribútum-hozzárendelési** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Olfeo SaaS-ben lévő csoportok egyeztetésére használhatók frissítési műveletekhez. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

      |Attribútum|Típus|Szűréshez támogatott|
      |---|---|---|
      |displayName|Sztring|&check;|
      |externalId|Sztring|
      |tagok|Referencia|

1. A hatóköri szűrők konfigurálásához tekintse meg a [hatóköri szűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)szereplő utasításokat.

1. A Olfeo SAAS-hez készült Azure AD-kiépítési szolgáltatás engedélyezéséhez módosítsa a **kiépítési állapot** beállítást **a** **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

1. Adja meg azokat a felhasználókat vagy csoportokat, akik számára a Olfeo SAAS-t szeretné kiépíteni, ehhez válassza ki a kívánt értékeket a **hatókör** területen a **Beállítások** szakaszban.

    ![Átadási hatókör](common/provisioning-scope.png)

1. Ha készen áll a létesítésre, válassza a **Mentés** lehetőséget.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet a **Beállítások** szakasz **Hatókör** területén meghatározott összes felhasználó és csoport kezdeti szinkronizálási ciklusát elindítja. A kezdeti ciklus hosszabb időt vesz igénybe, mint a következő ciklusok, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut.

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése

A kiépítés beállítása után a következő erőforrásokkal figyelheti az üzemelő példányt:

* A [kiépítési naplók](../reports-monitoring/concept-provisioning-logs.md) segítségével határozza meg, hogy mely felhasználók lettek sikeresen kiépítve vagy sikertelenül.
* Ellenőrizze a [folyamatjelző sáv](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) állapotát a kiépítési ciklus állapotának megtekintéséhez és a befejezéshez.
* Ha úgy tűnik, hogy az átadási konfiguráció állapota nem megfelelő, az alkalmazás karanténba kerül. További információ a karanténba helyezési [állapotokról: az alkalmazás üzembe helyezési állapota](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="more-resources"></a>További erőforrások

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)