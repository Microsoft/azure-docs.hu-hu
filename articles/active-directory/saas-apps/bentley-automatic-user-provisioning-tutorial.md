---
title: 'Oktatóanyag: A Felhasználóátépítés automatikus konfigurálása a felhasználók automatikus üzembe Azure Active Directory | Microsoft Docs'
description: Ismerje meg, hogyan lehet automatikusan kiépítani és visszaszenedni a felhasználói fiókokat az Azure AD-ről a Automatikusan kiépítő Felhasználóátépítésre.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 08778fff-f252-45c2-95d4-cc640c288af3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2021
ms.author: Zhchia
ms.openlocfilehash: 0d5aaee56606abcc886310049ad5a087f2aedb5e
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590324"
---
# <a name="tutorial-configure-bentley---automatic-user-provisioning-for-automatic-user-provisioning"></a>Oktatóanyag: A Felhasználóátépítés automatikus konfigurálása – Automatikus felhasználóátépítés

Ez az oktatóanyag azokat a lépéseket ismerteti, amelyek az automatikus felhasználóátépítés konfigurálása érdekében mind a Következő, mind az Azure Active Directory -ben (Azure AD) végre kell hajtanunk. Ha konfigurálva van, az Azure AD automatikusan kiépíti és kiépíti a felhasználókat és csoportokat a [Következőre: Automatikus](https://www.bentley.com) felhasználóátépítés az Azure AD kiépítési szolgáltatás használatával. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a Felleyben – Automatikus felhasználóátépítés
> * Felhasználók eltávolítása a Következőben : Automatikus felhasználóátépítés, ha már nincs szükségük hozzáférésre
> * A felhasználói attribútumok szinkronizálásának megtartása az Azure AD és a Majdley között – Automatikus felhasználóátépítés
> * Csoportok és csoporttagságok kiépítése a Következőben: Automatikus felhasználóátépítés

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik az alábbi előfeltételek valamelyikével:

* [Egy Azure AD-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Felhasználói fiók az Azure [](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) AD-ban, amely engedéllyel rendelkezik a kiépítés konfigurálásához (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda, alkalmazástulajdonos vagy globális rendszergazda). 
* Összevont fiók a Majdley IMS-sel.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés Az átadás üzembe helyezésének megtervezése
1. Ismerje meg [az átadási szolgáltatás működését](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Határozza meg, hogy ki lesz [az átadás hatókörében](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Határozza meg, hogy milyen adatokat kell [leképezni az Azure AD és a Melyley – Automatikus felhasználóátépítés között.](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) 

## <a name="step-2-configure-bentley---automatic-user-provisioning-to-support-provisioning-with-azure-ad"></a>2. lépés A Felhasználóátépítés automatikus konfigurálása az Azure AD-vel való kiépítés támogatásához

A bérlői URL-cím [](https://communities.bentley.com/communities/other_communities/licensing_cloud_and_web_services/w/wiki/52836/microsoft-azure-ad-automatic-user-provisioning-configuration) és a titkos jogkivonat stb. elérést a Felhasználóátépítés támogatási csapatának kell elérnie. Ezeket az értékeket a rendszer a kiépítési lapon adja meg a Azure Portal.

## <a name="step-3-add-bentley---automatic-user-provisioning-from-the-azure-ad-application-gallery"></a>3. lépés Hozzá van vay – Automatikus felhasználóátépítés az Azure AD alkalmazásgyűjteményből

Hozzá lesz adjuk a Automatikusan kiépítést az Azure AD alkalmazás-katalógusból a Provisioning to Fogley – Automatic User Provisioning (Automatikus felhasználóátépítés) szolgáltatásra való kiépítés kezelésének elkezdéséhez. Az alkalmazások katalógusból való hozzáadásáról [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) tudhat meg többet. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Az átadás hatókörében lévő személyek meghatározása 

Az Azure AD átadási szolgáltatása lehetővé teszi az átadott személyek hatókörének meghatározását az alkalmazáshoz való hozzárendelés és/vagy a felhasználó/csoport attribútumai alapján. Ha a hozzárendelés alapján történő hatókör-meghatározást választja, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet felhasználókat és csoportokat az alkalmazáshoz. Ha csak a felhasználó vagy csoport attribútumai alapján történő hatókörmeghatározást választja, az [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) leírt hatókörszűrőt használhatja. 

* Amikor felhasználókat és csoportokat rendel hozzá a Amitley – Automatikus felhasználóátépítéshez, az Alapértelmezett hozzáféréstől különböző szerepkört **kell választania.** Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva az átadásból, és az átadási naplókban nem jogosultként lesznek megjelölve. Ha az alkalmazáshoz csak az alapértelmezett hozzáférési szerepkör érhető el, akkor további szerepkörök felvételéhez [frissítheti az alkalmazásjegyzéket](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps). 

* Kezdje kicsiben. Tesztelje a felhasználók és csoportok kis halmazát, mielőtt mindenkire kiterjesztené. Amikor az átadás hatóköre a hozzárendelt felhasználókra és csoportokra van beállítva, ennek szabályozásához egy vagy két felhasználót vagy csoportot rendelhet az alkalmazáshoz. Amikor a hatókör az összes felhasználóra és csoportra van beállítva, meghatározhat egy [attribútumalapú hatókörszűrőt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-bentley---automatic-user-provisioning"></a>5. lépés Automatikus felhasználóátépítés konfigurálása a Megadottley-hez – Automatikus felhasználóátépítés 

Ez a szakasz végigvezeti azon lépéseken, amelyek segítségével konfigurálhatja az Azure AD kiépítési szolgáltatást felhasználók és/vagy csoportok létrehozására, frissítésére és letiltására a TestAppben felhasználói és/vagy csoport-hozzárendelések alapján az Azure AD-ban.

### <a name="to-configure-automatic-user-provisioning-for-bentley---automatic-user-provisioning-in-azure-ad"></a>Automatikus felhasználóátépítés konfigurálása Az Azure AD Automatikus felhasználóátépítés funkcióhoz:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Majdley – Automatikus felhasználóátépítés lehetőséget.**

    ![Az Applications (Alkalmazások) listában található Az automatikus felhasználóátépítés hivatkozás](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Kiépítés lap automatikus](common/provisioning-automatic.png)

5. A **Rendszergazdai hitelesítő adatok szakaszban** adja meg a Következőt: Automatikus felhasználóátépítési bérlői URL-cím és Titkos kód jogkivonat. Kattintson **a Kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure AD kapcsolódhat a Majdley – Automatikus felhasználóátépítéshez. Ha a kapcsolat sikertelen, győződjön meg arról, hogy Az automatikus felhasználóátépítési fiók rendszergazdai engedélyekkel rendelkezik, majd próbálkozzon újra.

    ![Jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az **Értesítés e-mailben** mezőben adja meg annak a személynek vagy csoportnak az e-mail-címét, aki az átadással kapcsolatos hibaüzeneteket kapja, és jelölje be az **E-mail-értesítés küldése hiba esetén** jelölőnégyzetet.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **Leképezések szakaszban** válassza a **Felhasználók szinkronizálása Azure Active Directory a Következővel: Automatikus** felhasználóátépítés lehetőséget.

9. Tekintse át az Azure AD-ről a Automatikusan felhasználóátépítésre szinkronizált felhasználói attribútumokat az **Attribútumleképezés szakaszban.** Az Egyeztetési  tulajdonságokként kiválasztott attribútumok a Frissítési műveletek automatikus felhasználóátépítés felhasználói fiókjaihoz illeszkednek. Ha úgy dönt, [](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)hogy módosítja az egyező célattribútumot, meg kell győződni arról, hogy a Basedley – Automatic User Provisioning API támogatja a felhasználók ezen attribútum alapján való szűrését. A módosítások **véglegesítéshez** kattintson a Mentés gombra.

   |Attribútum|Típus|Szűréshez támogatott|
   |---|---|---|
   |userName (Felhasználónév)|Sztring|&check;|
   |cím|Sztring|
   |emails[type eq "work"].value|Sztring|
   |preferredLanguage (előnyben részesített nyelv)|Sztring|
   |name.givenName|Sztring|
   |name.familyName|Sztring|
   |addresses[type eq "work"].streetAddress|Sztring|
   |addresses[type eq "work"].locality|Sztring|
   |addresses[type eq "work"].region|Sztring|
   |addresses[type eq "work"].postalCode|Sztring|
   |addresses[type eq "work"].country|Sztring|
   |phoneNumbers[type eq "work"].value|Sztring|
   |externalId|Sztring|
   |urn:ietf:params:scim:schemas:extension:Handley:2.0:User:isSoftDeleted|Sztring|

10. A **Leképezések szakaszban** válassza a Szinkronizálási csoportok **Azure Active Directory a Következővel: Automatikus** felhasználóátépítés lehetőséget.

11. Az Attribútumleképezés szakaszban tekintse át az Azure AD-ről a Arraley – Automatikus felhasználóátépítésre szinkronizált **csoportattribútumokat.** Az Egyező  tulajdonságokként kiválasztott attribútumok a Frissítési műveletek automatikus felhasználóátépítése beállításban megadott csoportokkal egyeznek meg. A módosítások **véglegesítéshez** kattintson a Mentés gombra.

      |Attribútum|Típus|Szűréshez támogatott|
      |---|---|---|
      |displayName|Sztring|&check;|
      |externalId|Sztring|
      |tagok|Referencia|
      |urn:ietf:params:scim:schemas:extension:Handley:2.0:Group:description|Sztring|

12. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

13. Ha engedélyezni szeretné az Azure AD kiépítési szolgáltatást a  Következőre: Automatikus felhasználóátépítés, módosítsa a Kiépítési állapot beállítást **Be** állapotra a **Beállítások szakaszban.**

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

14. Határozza meg azokat a felhasználókat és/vagy csoportokat, amelyek a Settings (Beállítások)  szakaszban a Scope (Hatókör) területen kiválasztva megadhatja azokat a felhasználókat és/vagy csoportokat, akik számára az Automatikus felhasználóátépítést szeretné **létesítani.**

    ![Átadási hatókör](common/provisioning-scope.png)

15. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet a **Beállítások** szakasz **Hatókör** területén meghatározott összes felhasználó és csoport kezdeti szinkronizálási ciklusát elindítja. A kezdeti ciklus elvégzése hosszabb időt vesz igénybe, mint a későbbi ciklusok, amelyek az Azure AD átadási szolgáltatásának futtatása során körülbelül 40 percenként lesznek végrehajtva. 

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Az átadás konfigurálása után a következő erőforrásokkal monitorozhatja az üzemelő példányt:

1. Az [átadási naplókkal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) határozhatja meg, hogy mely felhasználók átadása sikeres, és melyeké sikertelen.
2. A [folyamatjelzőn](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) láthatja az átadási ciklus állapotát és azt, hogy mennyi hiányzik még a befejeződéséhez.
3. Ha úgy tűnik, hogy az átadási konfiguráció állapota nem megfelelő, az alkalmazás karanténba kerül. A karanténállapotokról [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status) találhat további információt.

## <a name="connector-limitations"></a>Összekötőkre vonatkozó korlátozások
* Az "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager" vállalati bővítményattribútum nem támogatott, és el lesz távolítva.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../manage-apps/check-status-user-account-provisioning.md)
