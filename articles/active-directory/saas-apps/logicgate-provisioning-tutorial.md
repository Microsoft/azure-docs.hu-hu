---
title: 'Oktatóanyag: A LogicGate konfigurálása automatikus felhasználóátépítéshez Azure Active Directory | Microsoft Docs'
description: Ismerje meg, hogyan lehet automatikusan kiépítni és visszaveszni felhasználói fiókokat az Azure AD-ból a LogicGate-be.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: eea988ef-b0f1-4d22-b867-310f167540c3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2021
ms.author: Zhchia
ms.openlocfilehash: c9c938ab344a7d861af713fa42e2e39afa1df1b3
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589465"
---
# <a name="tutorial-configure-logicgate-for-automatic-user-provisioning"></a>Oktatóanyag: A LogicGate konfigurálása automatikus felhasználóátépítéshez

Ez az oktatóanyag azokat a lépéseket ismerteti, amelyekre szükség van a LogicGateben és a Azure Active Directory (Azure AD) az automatikus felhasználóátépítés konfigurálásában. Ha konfigurálva van, az Azure AD automatikusan kiépíti és kiépíti a felhasználókat és csoportokat a [LogicGate](https://www.logicgate.com) számára az Azure AD provisioning service használatával. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a LogicGate-ben
> * Felhasználók eltávolítása a LogicGate-ben, ha már nincs szükségük hozzáférésre
> * A felhasználói attribútumok szinkronizálása az Azure AD és a LogicGate között

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik az alábbi előfeltételek valamelyikével:

* [Egy Azure AD-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Egy felhasználói fiók az Azure AD-ben az átadás konfigurálására vonatkozó [engedéllyel](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) (pl. alkalmazás-rendszergazda, felhőalkalmazás-rendszergazda, alkalmazástulajdonos vagy globális rendszergazda). 
* Egy LogicGate-bérlő nagyvállalati csomaggal vagy jobban engedélyezett.
* Rendszergazdai engedélyekkel rendelkező felhasználói fiók a LogicGate-ben.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés Az átadás üzembe helyezésének megtervezése
1. Ismerje meg [az átadási szolgáltatás működését](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Határozza meg, hogy ki lesz [az átadás hatókörében](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Határozza meg, hogy milyen adatokat [kell leképezni az Azure AD és a LogicGate között.](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) 

## <a name="step-2-configure-logicgate-to-support-provisioning-with-azure-ad"></a>2. lépés A LogicGate konfigurálása az Azure AD-beli kiépítés támogatásához

1. Jelentkezzen be a **LogicGate felügyeleti** konzolba. Lépjen a **Kezdőlapra,** és kattintson a **jobb felső sarokban található Profil** ikonra.
2. Lépjen a **Profilelérési** **>** **kulcs lapra.**

    ![Profil lap](./media/logicgate-provisioning-tutorial/profile.png)

3. Kattintson a **Generate Access key (Hozzáférési kulcs létrehozása) elemre.** 
    
    ![Hozzáférés lap](./media/logicgate-provisioning-tutorial/key.png)

4. Másolja és mentse a **hozzáférési kulcsot.** Ez az érték a LogicGate-alkalmazás Provisioning (Kiépítés) lapjának **Secret Token** * (Titkos jogkivonat *) mezőjében lesz megadva a Azure Portal. 
    
    ![Kulcs lap](./media/logicgate-provisioning-tutorial/access.png)

## <a name="step-3-add-logicgate-from-the-azure-ad-application-gallery"></a>3. lépés LogicGate hozzáadása az Azure AD-alkalmazásgyűjteményből

Adja hozzá a LogicGate-et az Azure AD alkalmazásgyűjteményből a LogicGate-be való kiépítés kezelésének elkezdéséhez. Ha korábban már telepítette a LogicGate-et az SSO-hez, használhatja ugyanazt az alkalmazást. Az integráció első tesztelésekor azonban érdemes létrehozni egy külön alkalmazást. Az alkalmazások katalógusból való hozzáadásáról [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) tudhat meg többet. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Az átadás hatókörében lévő személyek meghatározása 

Az Azure AD átadási szolgáltatása lehetővé teszi az átadott személyek hatókörének meghatározását az alkalmazáshoz való hozzárendelés és/vagy a felhasználó/csoport attribútumai alapján. Ha a hozzárendelés alapján történő hatókör-meghatározást választja, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet felhasználókat és csoportokat az alkalmazáshoz. Ha csak a felhasználó vagy csoport attribútumai alapján történő hatókörmeghatározást választja, az [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) leírt hatókörszűrőt használhatja. 

* Amikor felhasználókat és csoportokat rendel a LogicGate-hez, az alapértelmezett hozzáféréstől különböző **szerepkört kell választania.** Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva az átadásból, és az átadási naplókban nem jogosultként lesznek megjelölve. Ha az alkalmazáshoz csak az alapértelmezett hozzáférési szerepkör érhető el, akkor további szerepkörök felvételéhez [frissítheti az alkalmazásjegyzéket](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps). 

* Kezdje kicsiben. Tesztelje a felhasználók és csoportok kis halmazát, mielőtt mindenkire kiterjesztené. Amikor az átadás hatóköre a hozzárendelt felhasználókra és csoportokra van beállítva, ennek szabályozásához egy vagy két felhasználót vagy csoportot rendelhet az alkalmazáshoz. Amikor a hatókör az összes felhasználóra és csoportra van beállítva, meghatározhat egy [attribútumalapú hatókörszűrőt](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-logicgate"></a>5. lépés Automatikus felhasználóátépítés konfigurálása a LogicGate-hez 

Ez a szakasz végigvezeti azon lépéseken, amelyek segítségével konfigurálhatja az Azure AD kiépítési szolgáltatást felhasználók és/vagy csoportok létrehozására, frissítésére és letiltására a TestAppben felhasználói és/vagy csoport-hozzárendelések alapján az Azure AD-ban.

### <a name="to-configure-automatic-user-provisioning-for-logicgate-in-azure-ad"></a>Automatikus felhasználóátépítés konfigurálása a LogicGate-hez az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **LogicGate lehetőséget.**

    ![A LogicGate hivatkozása az Alkalmazások listában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Automatikus lap](common/provisioning-automatic.png)

5. A **Rendszergazdai hitelesítő adatok szakaszban** adja meg a LogicGate-bérlő URL-címét és a titkos jogkivonatot. Kattintson **a Kapcsolat tesztelése elemre** annak biztosításához, hogy az Azure AD kapcsolódhat a LogicGate-hez. Ha a kapcsolat meghiúsul, ellenőrizze, hogy a LogicGate-fiók rendelkezik-e rendszergazdai engedélyekkel, majd próbálkozzon újra.

    ![Jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az **Értesítés e-mailben** mezőben adja meg annak a személynek vagy csoportnak az e-mail-címét, aki az átadással kapcsolatos hibaüzeneteket kapja, és jelölje be az **E-mail-értesítés küldése hiba esetén** jelölőnégyzetet.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **Leképezések szakaszban** válassza a **Felhasználók szinkronizálása Azure Active Directory LogicGate-be lehetőséget.**

9. Tekintse át az Azure AD-ból a LogicGate-be szinkronizált felhasználói attribútumokat az **Attribútumleképezés szakaszban.** Az Egyező tulajdonságokként **kiválasztott** attribútumok a LogicGate felhasználói fiókjaihoz illeszkednek a frissítési műveletekhez. Ha úgy dönt, [](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)hogy módosítja az egyező célattribútumot, meg kell győződni arról, hogy a LogicGate API támogatja a felhasználók ezen attribútum alapján való szűrését. A módosítások **véglegesítéshez** kattintson a Mentés gombra.

   |Attribútum|Típus|Szűréshez támogatott|
   |---|---|---|
   |userName (Felhasználónév)|Sztring|&check;|
   |emails[type eq "work"].value|Sztring|
   |active|Logikai|
   |name.givenName|Sztring|
   |name.familyName|Sztring|

10. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

11. Ha engedélyezni szeretné az Azure AD kiépítési szolgáltatást a LogicGate számára, módosítsa a **Kiépítési** állapot beállítást **Be** állapotra a **Beállítások szakaszban.**

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

12. Határozza meg a LogicGate számára kiépítni kívánt felhasználókat és/vagy  csoportokat a Kívánt értékek kiválasztásával a Hatókör területen a **Beállítások szakaszban.**

    ![Átadási hatókör](common/provisioning-scope.png)

13. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet a **Beállítások** szakasz **Hatókör** területén meghatározott összes felhasználó és csoport kezdeti szinkronizálási ciklusát elindítja. A kezdeti ciklus elvégzése hosszabb időt vesz igénybe, mint a későbbi ciklusok, amelyek az Azure AD átadási szolgáltatásának futtatása során körülbelül 40 percenként lesznek végrehajtva. 

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Az átadás konfigurálása után a következő erőforrásokkal monitorozhatja az üzemelő példányt:

1. Az [átadási naplókkal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) határozhatja meg, hogy mely felhasználók átadása sikeres, és melyeké sikertelen.
2. A [folyamatjelzőn](/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) láthatja az átadási ciklus állapotát és azt, hogy mennyi hiányzik még a befejeződéséhez.
3. Ha úgy tűnik, hogy az átadási konfiguráció állapota nem megfelelő, az alkalmazás karanténba kerül. A karanténállapotokról [itt](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status) találhat további információt.  

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../manage-apps/check-status-user-account-provisioning.md)
