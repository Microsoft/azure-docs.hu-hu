---
title: 'Oktatóanyag: A Jostle konfigurálása automatikus felhasználóátépítéshez Azure Active Directory | Microsoft Docs'
description: Ismerje meg, hogyan lehet automatikusan kiépítni és megszűkni a felhasználói fiókokat az Azure AD-ból a Jostle-be.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 6dbb744f-8b8e-4988-b293-ebe079c8c5c5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2021
ms.author: Zhchia
ms.openlocfilehash: d2ab0009f036afa38dc9e401223854a034d45e42
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368671"
---
# <a name="tutorial-configure-jostle-for-automatic-user-provisioning"></a>Oktatóanyag: A Jostle konfigurálása automatikus felhasználóátépítéshez

Ez az oktatóanyag azokat a lépéseket ismerteti, amelyekre a Jostle és a Azure Active Directory (Azure AD) esetén is szükség van az automatikus felhasználóátépítés konfigurálása érdekében. Ha konfigurálva van, az Azure AD automatikusan kiépíti és kiépíti a felhasználókat és csoportokat a [Jostle](https://www.jostle.me/) számára az Azure AD provisioning service használatával. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a Jostle-ban
> * Felhasználók eltávolítása a Jostle-ban, ha már nincs szükségük hozzáférésre
> * A felhasználói attribútumok szinkronizálásának megtartása az Azure AD és a Jostle között
> * [Egyszeri bejelentkezés](jostle-tutorial.md) a Jostle-be (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételek valamelyikével:

* [Egy Azure AD-bérlő](../develop/quickstart-create-new-tenant.md) 
* Felhasználói fiók az Azure [](../roles/permissions-reference.md) AD-ban, amely engedéllyel rendelkezik a kiépítés konfigurálásához (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda, alkalmazástulajdonos vagy globális rendszergazda). 
* [Egy Jostle-bérlő.](https://www.jostle.me/)
* Rendszergazdai engedélyekkel rendelkező felhasználói fiók a Jostle-ban.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés Az átadás üzembe helyezésének megtervezése

1. Ismerje meg [az átadási szolgáltatás működését](../app-provisioning/user-provisioning.md).
1. Határozza meg, hogy ki lesz [az átadás hatókörében](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Határozza meg, hogy milyen [adatokat kell leképezni az Azure AD és a Jostle között.](../app-provisioning/customize-application-attributes.md)

## <a name="step-2-configure-jostle-to-support-provisioning-with-azure-ad"></a>2. lépés A Jostle konfigurálása az Azure AD-beli kiépítés támogatására

### <a name="automation-account"></a>Automation-fiók

Mielőtt hozzákezd, létre kell hoznia egy **Automation-felhasználót** a Jostle intraneten. Ezt a fiókot fogja használni az Azure-ral való konfiguráláshoz. Az Automation-felhasználók az Automation-felhasználók **kezeléséhez > felhasználói** fiókok és adatok felügyeleti beállításaiban > létre.

Az Automation-felhasználókkal és azok létrehozási mikéntjére vonatkozó további részleteket ebben [a cikkben talál.](https://forum.jostle.us/hc/en-us/articles/360057364073)

A létrehozás után az Automation felhasználói fiókot aktiválni **kell** (azaz legalább egyszer be kell jelentkeznie az intranetre), mielőtt az Azure konfigurálni tudja.

### <a name="manage-user-provisioning"></a>Felhasználókiépítés kezelése

Mielőtt hozzákezd, győződjön meg arról, hogy a fiók-előfizetés tartalmazza az **SSO/felhasználóátépítési szolgáltatásokat.** Ha nem, akkor kapcsolatba léphet az ügyfél sikermenedzserével, és ők segíthetnek a fiókhoz <success@jostle.me> való hozzáadásában.

A következő lépés az **API URL-címének** és **API-kulcsának beszerzése** a Jostle-től:

1. A Fő navigációs menüben kattintson a **Rendszergazdai beállítások elemre.**
1. A **Felhasználói adatok más rendszerekbe vagy rendszerekből alatt kattintson** a **Felhasználókiépítés kezelése elemre.** Ha itt nem látja a "Felhasználókiépítés kezelése" lehetőséget, és ellenőrizte, hogy a fiókja tartalmazza-e az SSO/felhasználóátépítést, lépjen kapcsolatba az ügyfélszolgálattal, hogy az oldal engedélyezve legyen a <support@jostle.me> rendszergazdai beállításokban).
1. A **User Provisioning API details (Felhasználóátépítési API** részletei) szakaszban válassza a Your Base URL (Alap **URL-cím)** mezőt, kattintson a Copy (Másolás) gombra, és mentse az URL-címet egy olyan helyre, ahol később könnyen elérheti.                                                           

   ![Kiépítés](media/jostle-provisioning-tutorial/manage-user-provisioning.png)
                
1. Ezután kattintson az **Új kulcs hozzáadása... gombra.** Gomb
1. A következő képernyőn az **Automation-felhasználó** mezőben válassza ki az Automation felhasználói fiókját a legördülő menüben. 

   ![Integrációs fiók](media/jostle-provisioning-tutorial/select-integration-account.png)                                                                                                                                     
1. A **Provisioning API key description (Kiépítési API-kulcs leírása)** mezőben adjon nevet a kulcsnak (pl. "Azure"), majd kattintson a **Hozzáadás gombra.**

1. A kulcs létrehozása  után azonnal másolja ki, és mentse az URL-cím mentett útjára (mivel ez lesz az egyetlen alkalom, amikor a kulcs megjelenik).                                                               
1. Ezután az API URL-címével és **API-kulcsával** konfigurálja az integrációt az Azure-ban. 
## <a name="step-3-add-jostle-from-the-azure-ad-application-gallery"></a>3. lépés Jostle hozzáadása az Azure AD alkalmazáskatatárból

Adja hozzá a Jostle-t az Azure AD alkalmazáskatatárból a Jostle-beli kiépítés kezelésének elkezdéséhez. Ha korábban már telepítette a Jostle-t az SSO-hez, használhatja ugyanazt az alkalmazást. Javasoljuk azonban, hogy az integráció első tesztelésekor hozzon létre egy külön alkalmazást. Az alkalmazások katalógusból való hozzáadásáról [itt](../manage-apps/add-gallery-app.md) tudhat meg többet. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Az átadás hatókörében lévő személyek meghatározása 

Az Azure AD átadási szolgáltatása lehetővé teszi az átadott személyek hatókörének meghatározását az alkalmazáshoz való hozzárendelés és/vagy a felhasználó/csoport attribútumai alapján. Ha a hozzárendelés alapján történő hatókör-meghatározást választja, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet felhasználókat és csoportokat az alkalmazáshoz. Ha csak a felhasználó vagy csoport attribútumai alapján történő hatókörmeghatározást választja, az [itt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) leírt hatókörszűrőt használhatja. 

* Amikor felhasználókat és csoportokat rendel a Jostle-hoz, az alapértelmezett hozzáféréstől különböző **szerepkört kell választania.** Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva az átadásból, és az átadási naplókban nem jogosultként lesznek megjelölve. Ha az alkalmazásban csak az alapértelmezett hozzáférési szerepkör [](../develop/howto-add-app-roles-in-azure-ad-apps.md) érhető el, akkor frissítheti az alkalmazásjegyzéket további szerepkörök hozzáadásához. 

* Kezdje kicsiben. Tesztelje a felhasználók és csoportok kis halmazát, mielőtt mindenkire kiterjesztené. Amikor az átadás hatóköre a hozzárendelt felhasználókra és csoportokra van beállítva, ennek szabályozásához egy vagy két felhasználót vagy csoportot rendelhet az alkalmazáshoz. Amikor a hatókör az összes felhasználóra és csoportra van beállítva, meghatározhat egy [attribútumalapú hatókörszűrőt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-jostle"></a>5. lépés A Jostle automatikus felhasználóátépítésének konfigurálása 

Ez a szakasz végigvezeti az Azure AD kiépítési szolgáltatás konfigurálásához szükséges lépéseken, hogy felhasználókat és csoportokat hozzon létre, frissítsen és tiltson le a Jostle-alkalmazásban az Azure AD-beli felhasználói és csoport-hozzárendelések alapján.

### <a name="to-configure-automatic-user-provisioning-for-jostle-in-azure-ad"></a>A Jostle automatikus felhasználóátépítésének konfigurálása az Azure AD-ban:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

1. Az alkalmazások listájában válassza a **Jostle lehetőséget.**

    ![A Jostle-hivatkozás az Alkalmazások listában](common/all-applications.png)

1. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

1.  Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Kiépítés lap automatikus](common/provisioning-automatic.png)

1. A **Rendszergazdai hitelesítő adatok szakaszban** adja meg a Jostle-bérlő **URL-címét** és a **titkos jogkivonat adatait.** Válassza **a Kapcsolat tesztelése** lehetőséget annak biztosításához, hogy az Azure AD kapcsolódhat a Jostle-hoz. Ha a kapcsolat sikertelen, ellenőrizze, hogy a Jostle-fiókja rendelkezik-e rendszergazdai engedélyekkel, majd próbálkozzon újra.

    ![Jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

1. Az **Értesítési e-mail** mezőben adja meg annak a személynek vagy csoportnak az e-mail-címét, akinek a kiépítési hibákról szóló értesítéseket kell kapnia. Jelölje be **az E-mail-értesítés küldése hiba esetén jelölőnégyzetet.**

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

1. Kattintson a **Mentés** gombra.

1. A **Leképezések szakaszban** válassza a **Felhasználók szinkronizálása Azure Active Directory a Jostle-hez lehetőséget.**

1. Tekintse át az Azure AD-ről a Jostle-be szinkronizált felhasználói attribútumokat az **Attribútumleképezés szakaszban.** Az Egyező tulajdonságokként **kiválasztott** attribútumok a Jostle-hez tartozó felhasználói fiókoknak megfelelők a frissítési műveletekhez. Ha módosítja az [egyező](../app-provisioning/customize-application-attributes.md)célattribútumot, meg kell győződni arról, hogy a Jostle API támogatja a felhasználók ezen attribútum alapján való szűrését. A **módosítások véglegesítéshez** válassza a Mentés lehetőséget.

   |Attribútum|Típus|Szűréshez támogatott|
   |---|---|---|
   |userName (Felhasználónév)|Sztring|&check;|
   |active|Logikai|
   |name.givenName|Sztring|
   |name.familyName|Sztring|
   |emails[type eq "work"].value|Sztring|
   |emails[type eq "personal"].value|Sztring|
   |emails[type eq "alternate1"].value|Sztring|
   |emails[type eq "alternate2"].value|Sztring|  
   |urn:ietf:params:scim:schemas:extension:jostle:2.0:User:alternateEmail1Label|Sztring|
   |urn:ietf:params:scim:schemas:extension:jostle:2.0:User:alternateEmail2Label |Sztring|  

1. A hatókörszűrők konfigurálásával lásd a hatókörszűrő oktatóanyagában található [utasításokat.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

1. Ha engedélyezni szeretné az Azure AD kiépítési szolgáltatást a Jostle-hez, módosítsa a **Kiépítési** állapot beállítást **Be** állapotra a **Beállítások szakaszban.**

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

1. Határozza meg a Jostle-hez kiépítni kívánt felhasználókat  vagy csoportokat a Kívánt értékek kiválasztásával a Hatókör területen a **Beállítások szakaszban.**

    ![Átadási hatókör](common/provisioning-scope.png)

1. Amikor készen áll a kiépítésre, válassza a **Mentés lehetőséget.**

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet a **Beállítások** szakasz **Hatókör** területén meghatározott összes felhasználó és csoport kezdeti szinkronizálási ciklusát elindítja. A kezdeti ciklus hosszabb időt vesz igénybe, mint a következő ciklusok, amelyek körülbelül 40 percenként következnek be, amíg az Azure AD kiépítési szolgáltatás fut.

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése

A kiépítés konfigurálása után a következő erőforrások használatával monitorozással figyelheti az üzemelő példányát:

* A [kiépítési naplók segítségével](../reports-monitoring/concept-provisioning-logs.md) állapítsa meg, hogy mely felhasználók voltak sikeresen vagy sikertelenül kiépítve.
* A [folyamatjelzőn ellenőrizheti](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) a kiépítési ciklus állapotát és azt, hogy milyen közel van a befejezéshez.
* Ha úgy tűnik, hogy az átadási konfiguráció állapota nem megfelelő, az alkalmazás karanténba kerül. A karantén állapotával kapcsolatos további információkért lásd: Az alkalmazás [kiépítési állapota karantén.](../app-provisioning/application-provisioning-quarantine-status.md)

## <a name="more-resources"></a>További erőforrások

* [Felhasználói fiókok létesítésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)