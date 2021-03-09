---
title: 'Oktatóanyag: a LinkedIn learning beállítása az automatikus felhasználó-kiépítés Azure Active Directorysal | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és kiépíteni felhasználói fiókjait az Azure AD-ből a LinkedIn learning szolgáltatásba.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 21e2f470-4eb1-472c-adb9-4203c00300be
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/30/2020
ms.author: Zhchia
ms.openlocfilehash: e2fb2ea9cac9994541b28855841f715fbb50c621
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102509536"
---
# <a name="tutorial-configure-linkedin-learning-for-automatic-user-provisioning"></a>Oktatóanyag: a LinkedIn learning beállítása a felhasználók automatikus kiépítési felállításához

Ez az oktatóanyag azokat a lépéseket ismerteti, amelyeket a LinkedIn learning és a Azure Active Directory (Azure AD) alkalmazásban kell elvégezni az automatikus felhasználó-kiépítés konfigurálásához. A konfigurálást követően az Azure AD automatikusan előállítja a felhasználókat és csoportokat a [LinkedIn learninghez](https://learning.linkedin.com/) az Azure ad kiépítési szolgáltatás használatával. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a LinkedIn learningben
> * Felhasználók eltávolítása a LinkedIn learning szolgáltatásban, ha már nincs szükség hozzáférésre
> * A felhasználói attribútumok szinkronizálása az Azure AD és a LinkedIn learning között
> * Csoportok és csoporttagságok kiépítése a LinkedIn learningben
> * [Egyszeri bejelentkezés](linkedinlearning-tutorial.md) a LinkedIn Learningbe (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Azure AD-bérlő](../develop/quickstart-create-new-tenant.md) 
* Egy felhasználói fiók az Azure AD-ben az átadás konfigurálására vonatkozó [engedéllyel](../roles/permissions-reference.md) (pl. alkalmazás-rendszergazda, felhőalkalmazás-rendszergazda, alkalmazástulajdonos vagy globális rendszergazda). 
* Jóváhagyás és SCIM engedélyezve a LinkedIn learninghez (Kapcsolatfelvétel e-mailben).

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés Az átadás üzembe helyezésének megtervezése
1. Ismerje meg [az átadási szolgáltatás működését](../app-provisioning/user-provisioning.md).
2. Határozza meg, hogy ki lesz [az átadás hatókörében](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Határozza meg, hogy az [Azure ad és a LinkedIn learning milyen adatleképezést szeretne leképezni](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-linkedin-learning-to-support-provisioning-with-azure-ad"></a>2. lépés A LinkedIn learning konfigurálása az Azure AD-vel való kiépítés támogatásához
1. Jelentkezzen be a [LinkedIn learning-beállításokba](https://www.linkedin.com/learning-admin/settings/global). Válassza a **scim beállítása** lehetőséget, majd válassza az **új scim-konfiguráció hozzáadása** lehetőséget.

   ![SCIM-telepítő konfigurációja](./media/linkedinlearning-provisioning-tutorial/learning-scim-settings.png)

2. Adja meg a konfiguráció nevét, és állítsa be a **licencek automatikus hozzárendelését** a következőre:. Ezután kattintson a **jogkivonat előállítása** elemre.

   ![SCIM-konfiguráció neve](./media/linkedinlearning-provisioning-tutorial/learning-scim-configuration.png)

3. A konfiguráció létrehozása után létre kell hozni egy **hozzáférési jogkivonatot** . A másolás később is megtartható.

   ![SCIM hozzáférési token](./media/linkedinlearning-provisioning-tutorial/learning-scim-token.png)

4. A meglévő konfigurációk újbóli kiadhatók (amelyek új tokent hoznak majd ki) vagy eltávolíthatók.

## <a name="step-3-add-linkedin-learning-from-the-azure-ad-application-gallery"></a>3. lépés LinkedIn learning hozzáadása az Azure AD Application Galleryből

A LinkedIn learning szolgáltatással való kiépítés kezelésének megkezdéséhez vegye fel a LinkedIn Learningt az Azure AD-alkalmazás galériájában. Ha korábban már beállította az egyszeri bejelentkezéshez készült LinkedIn-tanulást, használhatja ugyanazt az alkalmazást. Az integráció első tesztelésekor azonban érdemes létrehozni egy külön alkalmazást. Az alkalmazások katalógusból való hozzáadásáról [itt](../manage-apps/add-application-portal.md) tudhat meg többet. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Az átadás hatókörében lévő személyek meghatározása 

Az Azure AD átadási szolgáltatása lehetővé teszi az átadott személyek hatókörének meghatározását az alkalmazáshoz való hozzárendelés és/vagy a felhasználó/csoport attribútumai alapján. Ha a hozzárendelés alapján történő hatókör-meghatározást választja, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet felhasználókat és csoportokat az alkalmazáshoz. Ha csak a felhasználó vagy csoport attribútumai alapján történő hatókörmeghatározást választja, az [itt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) leírt hatókörszűrőt használhatja. 

* Amikor felhasználókat és csoportokat rendel a LinkedIn learninghez, ki kell választania az **alapértelmezett hozzáféréstől** eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva az átadásból, és az átadási naplókban nem jogosultként lesznek megjelölve. Ha az alkalmazáshoz csak az alapértelmezett hozzáférési szerepkör érhető el, akkor további szerepkörök felvételéhez [frissítheti az alkalmazásjegyzéket](../develop/howto-add-app-roles-in-azure-ad-apps.md). 

* Kezdje kicsiben. Tesztelje a felhasználók és csoportok kis halmazát, mielőtt mindenkire kiterjesztené. Amikor az átadás hatóköre a hozzárendelt felhasználókra és csoportokra van beállítva, ennek szabályozásához egy vagy két felhasználót vagy csoportot rendelhet az alkalmazáshoz. Amikor a hatókör az összes felhasználóra és csoportra van beállítva, meghatározhat egy [attribútumalapú hatókörszűrőt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-linkedin-learning"></a>5. lépés A felhasználók automatikus üzembe helyezésének konfigurálása a LinkedIn learning szolgáltatásban 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy TestApp alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

### <a name="to-configure-automatic-user-provisioning-for-linkedin-learning-in-azure-ad"></a>Az automatikus felhasználó-kiépítés beállítása a LinkedIn learninghez az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **LinkedIn learning** elemet.

    ![A LinkedIn learning hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a kiépítési mód legördülő listájáról az automatikus lehetőséggel.](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja `https://api.linkedin.com/scim` meg a **bérlői URL-címet**. Adja meg a korábban a **titkos tokenben** lekért hozzáférési jogkivonat értékét. Kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad csatlakozhasson a LinkedIn learning szolgáltatáshoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a LinkedIn learning-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Képernyőfelvétel: a rendszergazdai hitelesítő adatok párbeszédpanel, ahol megadhatja a bérlő U R L-t és a titkos jogkivonatot.](./media/linkedinlearning-provisioning-tutorial/provisioning.png)

6. Az **Értesítés e-mailben** mezőben adja meg annak a személynek vagy csoportnak az e-mail-címét, aki az átadással kapcsolatos hibaüzeneteket kapja, és jelölje be az **E-mail-értesítés küldése hiba esetén** jelölőnégyzetet.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók kiépítése** lehetőséget.

9. Tekintse át az Azure AD-ból a LinkedIn learninghez szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a LinkedIn learning-beli felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. Ha úgy dönt, hogy megváltoztatja a [megfelelő cél attribútumot](../app-provisioning/customize-application-attributes.md), akkor biztosítania kell, hogy a LINKEDIN learning API támogassa a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   |Attribútum|Típus|Szűréshez támogatott|
   |---|---|---|
   |externalId|Sztring|&check;|
   |userName (Felhasználónév)|Sztring|
   |name.givenName|Sztring|
   |name.familyName|Sztring|
   |displayName|Sztring|
   |címek [típus EQ "work"]. helység|Sztring|
   |cím|Sztring|
   |emails[type eq "work"].value|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: User: Manager|Referencia|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: részleg|Sztring|

10. A **leképezések** szakaszban válassza ki a **Azure Active Directory csoportok kiépítése** lehetőséget.

11. Tekintse át az Azure AD-ból a LinkedIn learninghez szinkronizált csoport attribútumait az **attribútum-hozzárendelés** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a LinkedIn learning szolgáltatásban a frissítési műveletekhez tartozó csoportok egyeztetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    |Attribútum|Típus|Szűréshez támogatott|
    |---|---|---|
    |displayName|Sztring|&check;|
    |tagok|Referencia|
    |externalId|Sztring|

12. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

13. Ha engedélyezni szeretné az Azure AD-kiépítési szolgáltatást a LinkedIn learninghez, módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg azokat a felhasználókat és/vagy csoportokat, akik számára a LinkedIn learninget szeretné kiépíteni, ehhez válassza ki a kívánt értékeket a **hatókör** területen a **Beállítások** szakaszban.

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