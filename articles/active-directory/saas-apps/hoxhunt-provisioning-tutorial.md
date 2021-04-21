---
title: 'Oktatóanyag: A Hoxhunt konfigurálása automatikus felhasználóátadáshoz Azure Active Directory | Microsoft Docs'
description: Megtudhatja, hogyan létesítsen automatikusan felhasználói fiókokat az Azure AD-ból a Hoxhuntba.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 24fbe0a4-ab2d-4e10-93a6-c87d634ffbcf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/28/2021
ms.author: Zhchia
ms.openlocfilehash: db33cc43419b4228ca270d3a69c0e88de2c05638
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752047"
---
# <a name="tutorial-configure-hoxhunt-for-automatic-user-provisioning"></a>Oktatóanyag: A Hoxhunt konfigurálása automatikus felhasználóátadáshoz

Ez az oktatóanyag azokat a lépéseket ismerteti, amelyekre szüksége lesz a Hoxhuntban és a Azure Active Directory (Azure AD) az automatikus felhasználóátadás konfigurálásában. Ha konfigurálva van, az Azure AD automatikusan kiépíti és kiépíti a felhasználókat és csoportokat a [Hoxhunt](https://www.hoxhunt.com/) számára az Azure AD provisioning service használatával. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a Hoxhuntban
> * Felhasználók eltávolítása a Hoxhuntból, ha már nincs szükségük hozzáférésre
> * A felhasználói attribútumok szinkronizálása az Azure AD és a Hoxhunt között
> * [Egyszeri bejelentkezés a](hoxhunt-tutorial.md) Hoxhuntba (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik az alábbi előfeltételek valamelyikével:

* [Egy Azure AD-bérlő](../develop/quickstart-create-new-tenant.md) 
* Felhasználói fiók az Azure [](../roles/permissions-reference.md) AD-ban, amely engedéllyel rendelkezik a kiépítés konfigurálásához (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda, alkalmazástulajdonos vagy globális rendszergazda). 
* Egy Hoxhunt-bérlő.
* SCIM API-kulcs és SCIM-végpont URL-címe a szervezet számára (a Hoxhunt-támogatás konfigurálja).
## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés Az átadás üzembe helyezésének megtervezése
1. Ismerje meg [az átadási szolgáltatás működését](../app-provisioning/user-provisioning.md).
2. Határozza meg, hogy ki lesz [az átadás hatókörében](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Határozza meg, hogy milyen [adatokat kell leképezni az Azure AD és a Hoxhunt között.](../app-provisioning/customize-application-attributes.md) 

## <a name="step-2-configure-hoxhunt-to-support-provisioning-with-azure-ad"></a>2. lépés A Hoxhunt konfigurálása az Azure AD-beli kiépítés támogatására
Vegye fel a kapcsolatot a [Hoxhunt](mailto:support@hoxhunt.com) ügyfélszolgálatával az SCIM API-kulcs és az SCIM-végpont URL-címének fogadásához, és konfigurálja a Hoxhuntot az Azure AD-beli kiépítés támogatására.
## <a name="step-3-add-hoxhunt-from-the-azure-ad-application-gallery"></a>3. lépés Hoxhunt hozzáadása az Azure AD alkalmazásgyűjteményből

Adja hozzá a Hoxhuntot az Azure AD alkalmazásgyűjteményből a Hoxhunt kiépítés kezelésének elkezdéséhez. Ha korábban már beállít egy Hoxhuntot az SSO-nak, használhatja ugyanazt az alkalmazást. Az integráció első tesztelésekor azonban érdemes létrehozni egy külön alkalmazást. Az alkalmazások katalógusból való hozzáadásáról [itt](../manage-apps/add-application-portal.md) tudhat meg többet. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Az átadás hatókörében lévő személyek meghatározása 

Az Azure AD átadási szolgáltatása lehetővé teszi az átadott személyek hatókörének meghatározását az alkalmazáshoz való hozzárendelés és/vagy a felhasználó/csoport attribútumai alapján. Ha a hozzárendelés alapján történő hatókör-meghatározást választja, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet felhasználókat és csoportokat az alkalmazáshoz. Ha csak a felhasználó vagy csoport attribútumai alapján történő hatókörmeghatározást választja, az [itt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) leírt hatókörszűrőt használhatja. 

* Amikor felhasználókat és csoportokat rendel a Hoxhunthoz, az alapértelmezett hozzáféréstől különböző **szerepkört kell választania.** Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva az átadásból, és az átadási naplókban nem jogosultként lesznek megjelölve. Ha az alkalmazáshoz csak az alapértelmezett hozzáférési szerepkör érhető el, akkor további szerepkörök felvételéhez [frissítheti az alkalmazásjegyzéket](../develop/howto-add-app-roles-in-azure-ad-apps.md). 

* Kezdje kicsiben. Tesztelje a felhasználók és csoportok kis halmazát, mielőtt mindenkire kiterjesztené. Amikor az átadás hatóköre a hozzárendelt felhasználókra és csoportokra van beállítva, ennek szabályozásához egy vagy két felhasználót vagy csoportot rendelhet az alkalmazáshoz. Amikor a hatókör az összes felhasználóra és csoportra van beállítva, meghatározhat egy [attribútumalapú hatókörszűrőt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-hoxhunt"></a>5. lépés Automatikus felhasználóátadás konfigurálása a Hoxhunthoz 

Ez a szakasz végigvezeti azon lépéseken, amelyek segítségével konfigurálhatja az Azure AD kiépítési szolgáltatást felhasználók és/vagy csoportok létrehozására, frissítésére és letiltására a TestAppben felhasználói és/vagy csoport-hozzárendelések alapján az Azure AD-ban.

### <a name="to-configure-automatic-user-provisioning-for-hoxhunt-in-azure-ad"></a>Automatikus felhasználóátadás konfigurálása a Hoxhunthoz az Azure AD-ban:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Hoxhunt lehetőséget.**

    ![A Hoxhunt hivatkozás az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Kiépítés lap automatikus](common/provisioning-automatic.png)

5. A **Rendszergazdai hitelesítő adatok szakaszban** adja meg a Hoxhunt-bérlő URL-címét és titkos jogkivonatát. Kattintson **a Kapcsolat tesztelése elemre** annak biztosításához, hogy az Azure AD csatlakozni tud a Hoxhunthoz. Ha a kapcsolat sikertelen, ellenőrizze, hogy a Hoxhunt-fiókja rendelkezik-e rendszergazdai engedélyekkel, majd próbálkozzon újra.

    ![Jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az **Értesítés e-mailben** mezőben adja meg annak a személynek vagy csoportnak az e-mail-címét, aki az átadással kapcsolatos hibaüzeneteket kapja, és jelölje be az **E-mail-értesítés küldése hiba esetén** jelölőnégyzetet.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **Leképezések szakaszban** válassza a **Felhasználók szinkronizálása Azure Active Directory a Hoxhuntba lehetőséget.**

9. Tekintse át az Azure AD-ről a Hoxhuntra szinkronizált felhasználói attribútumokat az **Attribútumleképezés szakaszban.** A Egyeztetési tulajdonságokként **kiválasztott** attribútumok a Hoxhunt felhasználói fiókjaihoz illeszkednek a frissítési műveletekhez. Ha úgy dönt, [](../app-provisioning/customize-application-attributes.md)hogy módosítja az egyező célattribútumot, meg kell győződni arról, hogy a Hoxhunt API támogatja a felhasználók ezen attribútum alapján való szűrését. A módosítások **véglegesítéshez** kattintson a Mentés gombra.

   |Attribútum|Típus|Szűréshez támogatott|
   |---|---|---|
   |userName (Felhasználónév)|Sztring|&check;|
   |emails[type eq "work"].value|Sztring|
   |active|Logikai|
   |name.givenName|Sztring|
   |name.familyName|Sztring|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|Sztring|
   |addresses[type eq "work"].country|Sztring|

10. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

11. Ha engedélyezni szeretné az Azure AD kiépítési  szolgáltatást a Hoxhunt számára, módosítsa a Kiépítési állapot beállítást **Be** állapotra a **Beállítások szakaszban.**

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

12. Határozza meg a Hoxhunt számára kiépítni kívánt felhasználókat és/vagy  csoportokat a Kívánt értékek kiválasztásával a Hatókör területen a **Beállítások szakaszban.**

    ![Átadási hatókör](common/provisioning-scope.png)

13. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet a **Beállítások** szakasz **Hatókör** területén meghatározott összes felhasználó és csoport kezdeti szinkronizálási ciklusát elindítja. A kezdeti ciklus elvégzése hosszabb időt vesz igénybe, mint a későbbi ciklusok, amelyek az Azure AD átadási szolgáltatásának futtatása során körülbelül 40 percenként lesznek végrehajtva. 

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Az átadás konfigurálása után a következő erőforrásokkal monitorozhatja az üzemelő példányt:

* Az [átadási naplókkal](../reports-monitoring/concept-provisioning-logs.md) határozhatja meg, hogy mely felhasználók átadása sikeres, és melyeké sikertelen.
* A [folyamatjelzőn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) láthatja az átadási ciklus állapotát és azt, hogy mennyi hiányzik még a befejeződéséhez.
* Ha úgy tűnik, hogy az átadási konfiguráció állapota nem megfelelő, az alkalmazás karanténba kerül. A karanténállapotokról [itt](../app-provisioning/application-provisioning-quarantine-status.md) találhat további információt.  

## <a name="change-log"></a>Módosítási napló
* 2021.04.20. – Támogatás hozzáadva a "preferredLanguage" és az "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division" vállalati bővítményattribútumhoz.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)