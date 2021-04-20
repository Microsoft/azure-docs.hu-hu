---
title: 'Oktatóanyag: Az Eletive konfigurálása automatikus felhasználóátépítéshez Azure Active Directory | Microsoft Docs'
description: Megtudhatja, hogyan létesítsen automatikusan felhasználói fiókokat az Azure AD-ről az Eletive-re.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 8a775422-e6d7-4cd5-b8d1-cc8a2db24c4f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2021
ms.author: Zhchia
ms.openlocfilehash: 393629be5c30eb3d97dae1de1c06e2d4cf7af94d
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741756"
---
# <a name="tutorial-configure-eletive-for-automatic-user-provisioning"></a>Oktatóanyag: Az Eletive konfigurálása automatikus felhasználóátépítéshez

Ez az oktatóanyag az Eletive és a Azure Active Directory (Azure AD) által az automatikus felhasználóátépítés konfigurálása érdekében szükséges lépéseket ismerteti. Ha konfigurálva van, az Azure AD automatikusan kiépíti és kiépíti a felhasználókat és csoportokat az [Eletive](https://app.eletive.com/) számára az Azure AD kiépítési szolgáltatás használatával. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása az Eletive-ban
> * Felhasználók eltávolítása az Eletive-ről, ha már nincs szükségük hozzáférésre
> * A felhasználói attribútumok szinkronizálásának megtartása az Azure AD és az Eletive között
> * Egyszeri bejelentkezés az Eletive-be (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik az alábbi előfeltételek valamelyikével:

* [Egy Azure AD-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Felhasználói fiók az Azure [](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) AD-ban, amely engedéllyel rendelkezik a kiépítés konfigurálásához (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda, alkalmazástulajdonos vagy globális rendszergazda). 
* Az Eletive-ban egy felhasználói fiók felügyeleti hozzáféréssel.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés Az átadás üzembe helyezésének megtervezése
1. Ismerje meg [az átadási szolgáltatás működését](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Határozza meg, hogy ki lesz [az átadás hatókörében](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Határozza meg, hogy milyen [adatokat kell leképezni az Azure AD és az Eletive között.](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) 

## <a name="step-2-configure-eletive-to-support-provisioning-with-azure-ad"></a>2. lépés Az Eletive konfigurálása az Azure AD-beli kiépítés támogatásához

1. Jelentkezzen be az [Eletive-be.](https://app.eletive.com/) Lépjen a **Beállítások szolgáltatások**  ->  **lapra.**

    ![Funkciók](media/eletive-provisioning-tutorial/settings.png)

2.  Integrációk **és** **SCIM 2.0 engedélyezése.**

    ![Integráció](media/eletive-provisioning-tutorial/scim.png)

3.  Lépjen a **Beállítások**  ->  **integrációi lapra.**

4.  Kattintson a **Felhasználóátépítés elemre.**

    ![Tab](media/eletive-provisioning-tutorial/user.png)

5.  Kattintson a **Csatlakozás gombra.**

    ![Gomb](media/eletive-provisioning-tutorial/connect.png)

6.  Másolja és mentse az SCIM 2.0 URL-címet és a Bearer tokent. Ezek az értékek a bérlői URL-cím és a titkos jogkivonat mezőben lesznek megadva az Eletive alkalmazás Provisioning (Kiépítés) lapján a Azure Portal.


## <a name="step-3-add-eletive-from-the-azure-ad-application-gallery"></a>3. lépés Az Eletive hozzáadása az Azure AD alkalmazásgyűjteményből

Az Eletive hozzáadása az Azure AD-alkalmazásgyűjteményből a kiépítés eletive-re való kezelésének elkezdéséhez. Ha korábban már telepítette az Eletive-t az SSO-hez, használhatja ugyanazt az alkalmazást. Az integráció első tesztelésekor azonban érdemes létrehozni egy külön alkalmazást. Az alkalmazások katalógusból való hozzáadásáról [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) tudhat meg többet. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Az átadás hatókörében lévő személyek meghatározása 

Az Azure AD átadási szolgáltatása lehetővé teszi az átadott személyek hatókörének meghatározását az alkalmazáshoz való hozzárendelés és/vagy a felhasználó/csoport attribútumai alapján. Ha a hozzárendelés alapján történő hatókör-meghatározást választja, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet felhasználókat és csoportokat az alkalmazáshoz. Ha csak a felhasználó vagy csoport attribútumai alapján történő hatókörmeghatározást választja, az [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) leírt hatókörszűrőt használhatja. 

* Amikor felhasználókat és csoportokat rendel az Eletive-hez, az Alapértelmezett hozzáféréstől különböző **szerepkört kell választania.** Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva az átadásból, és az átadási naplókban nem jogosultként lesznek megjelölve. Ha az alkalmazásban csak az alapértelmezett hozzáférési szerepkör [](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) érhető el, akkor frissítheti az alkalmazásjegyzéket szerepkörök hozzáadásához. 

* Kezdje kicsiben. Tesztelje a felhasználók és csoportok kis halmazát, mielőtt mindenkire kiterjesztené. Ha a kiépítés hatóköre hozzárendelt felhasználókra és csoportokra van beállítva, egy vagy két felhasználó vagy csoport alkalmazáshoz való hozzárendelése lehetővé teszi a hatókör szabályozását. Amikor a hatókör az összes felhasználóra és csoportra van beállítva, meghatározhat egy [attribútumalapú hatókörszűrőt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-eletive"></a>5. lépés Automatikus felhasználóátépítés konfigurálása az Eletive-re 

Ez a szakasz végigvezeti azon lépéseken, amelyek segítségével konfigurálhatja az Azure AD kiépítési szolgáltatást felhasználók és/vagy csoportok létrehozására, frissítésére és letiltására a TestAppben felhasználói és/vagy csoport-hozzárendelések alapján az Azure AD-ban.

### <a name="to-configure-automatic-user-provisioning-for-eletive-in-azure-ad"></a>Automatikus felhasználóátépítés konfigurálása az Eletive számára az Azure AD-ban:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza az **Eletive lehetőséget.**

    ![Az Alkalmazások listában található Eletive hivatkozás](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Kiépítés lap automatikus](common/provisioning-automatic.png)

5. A **Rendszergazdai hitelesítő adatok szakaszban** adja meg az Eletive-bérlői URL-címet és a titkos jogkivonatot. Kattintson **a Kapcsolat tesztelése elemre** annak biztosításához, hogy az Azure AD kapcsolódhat az Eletive-hez. Ha a kapcsolat sikertelen, ellenőrizze, hogy az Eletive-fiók rendelkezik-e rendszergazdai engedélyekkel, majd próbálkozzon újra.

    ![Jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az **Értesítés e-mailben** mezőben adja meg annak a személynek vagy csoportnak az e-mail-címét, aki az átadással kapcsolatos hibaüzeneteket kapja, és jelölje be az **E-mail-értesítés küldése hiba esetén** jelölőnégyzetet.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **Leképezések szakaszban** válassza a **Felhasználók szinkronizálása Azure Active Directory eletive típusúra lehetőséget.**

9. Tekintse át az Azure AD-ről az Eletive-re szinkronizált felhasználói attribútumokat az **Attribútumleképezés szakaszban.** Az Egyező  tulajdonságokként kiválasztott attribútumok az Eletive felhasználói fiókjaihoz illeszkednek a frissítési műveletekhez. Ha úgy dönt, [](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)hogy módosítja az egyező célattribútumot, győződjön meg arról, hogy az Eletive API támogatja a felhasználók ezen attribútum alapján való szűrését. A módosítások **véglegesítéshez** kattintson a Mentés gombra.

   |Attribútum|Típus|Szűréshez támogatott|
   |---|---|---|
   |userName (Felhasználónév)|Sztring|&check;|
   |emails[type eq "work"].value|Sztring|
   |externalId|Sztring|
   |name.givenName|Sztring|
   |name.familyName|Sztring|
   |preferredLanguage (előnyben részesített nyelv)|Sztring|
   |userType|Sztring|
   |urn:ietf:params:scim:schemas:extension:eletive:2.0:User:participateInSurvey|Sztring|

10. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

11. Ha engedélyezni szeretné az Azure AD kiépítési szolgáltatást az Eletive számára, módosítsa a **Kiépítési** állapot beállítást **Be** állapotra a **Beállítások szakaszban.**

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

12. Határozza meg az Eletive számára kiépítni kívánt felhasználókat és/vagy  csoportokat a Kívánt értékek kiválasztásával a Hatókör területen a **Beállítások szakaszban.**

    ![Átadási hatókör](common/provisioning-scope.png)

13. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet a **Beállítások** szakasz **Hatókör** területén meghatározott összes felhasználó és csoport kezdeti szinkronizálási ciklusát elindítja. A kezdeti ciklus elvégzése hosszabb időt vesz igénybe, mint a későbbi ciklusok, amelyek az Azure AD átadási szolgáltatásának futtatása során körülbelül 40 percenként lesznek végrehajtva. 

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Az átadás konfigurálása után a következő erőforrásokkal monitorozhatja az üzemelő példányt:

1. Az [átadási naplókkal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) határozhatja meg, hogy mely felhasználók átadása sikeres, és melyeké sikertelen.
2. A [folyamatjelzőn](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) láthatja az átadási ciklus állapotát és azt, hogy mennyi hiányzik még a befejeződéséhez.
3. Ha úgy tűnik, hogy az átadási konfiguráció állapota nem megfelelő, az alkalmazás karanténba kerül. A karanténállapotokról [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status) találhat további információt.  

## <a name="more-resources"></a>További erőforrások

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../manage-apps/check-status-user-account-provisioning.md)
