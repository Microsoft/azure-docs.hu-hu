---
title: 'Oktatóanyag: az SAP Analytics-felhő konfigurálása automatikus felhasználó-kiépítés Azure Active Directory használatával | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és kiépíteni felhasználói fiókjait az Azure AD-ből az SAP Analytics-felhőbe.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 27d12989-efa8-4254-a4ad-8cb6bf09d839
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/13/2020
ms.author: Zhchia
ms.openlocfilehash: 31e5393cb5de627ebf8832e43302583d6eacbf59
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96181484"
---
# <a name="tutorial-configure-sap-analytics-cloud-for-automatic-user-provisioning"></a>Oktatóanyag: SAP Analytics-felhő konfigurálása automatikus felhasználó-kiépítési szolgáltatáshoz

Ez az oktatóanyag ismerteti a SAP Analytics-felhőben és Azure Active Directoryban (Azure AD) végrehajtandó lépéseket az automatikus felhasználó-kiépítés konfigurálásához. A konfigurálást követően az Azure AD automatikusan kiépíti a felhasználókat és csoportokat az [SAP Analytics-felhőbe](https://www.sapanalytics.cloud/) az Azure ad-létesítési szolgáltatás használatával. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása az SAP Analytics-felhőben
> * Az SAP Analytics-felhőben lévő felhasználók eltávolítása, ha már nincs szükség hozzáférésre
> * Felhasználói attribútumok szinkronizálása az Azure AD és az SAP Analytics-felhő között
> * [Egyszeri bejelentkezés](sapboc-tutorial.md) az SAP Analytics-felhőbe (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Azure AD-bérlő](../develop/quickstart-create-new-tenant.md) 
* Egy felhasználói fiók az Azure AD-ben az átadás konfigurálására vonatkozó [engedéllyel](../roles/permissions-reference.md) (pl. alkalmazás-rendszergazda, felhőalkalmazás-rendszergazda, alkalmazástulajdonos vagy globális rendszergazda). 
* SAP Analytics felhőalapú bérlő
* Rendszergazdai jogosultságokkal rendelkező felhasználói fiók az SAP Identity kiépítési felügyeleti konzolon. Győződjön meg arról, hogy rendelkezik hozzáféréssel a proxy rendszerekhez az Identity kiépítési felügyeleti konzolon. Ha nem látja a **proxy rendszerek** csempét, hozzon létre egy incidenst a **BC-iam-IPS** összetevőhöz, hogy hozzáférést kérjen ehhez a csempéhez.
* Egy OAuth-ügyfél, amely az SAP Analytics-felhőben engedélyezte az ügyfél hitelesítő adatait. További információ: a [OAuth-ügyfelek és a megbízható identitás-szolgáltatók kezelése](https://help.sap.com/viewer/00f68c2e08b941f081002fd3691d86a7/release/en-US/4f43b54398fc4acaa5efa32badfe3df6.html)

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés Az átadás üzembe helyezésének megtervezése

1. Ismerje meg [az átadási szolgáltatás működését](../app-provisioning/user-provisioning.md).
2. Határozza meg, hogy ki lesz [az átadás hatókörében](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Határozza meg, hogy az [Azure ad és az SAP Analytics-felhő között milyen adatleképezést kell leképezni](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-sap-analytics-cloud-to-support-provisioning-with-azure-ad"></a>2. lépés SAP Analytics-felhő konfigurálása az Azure AD-vel való kiépítés támogatásához

1. Jelentkezzen be az SAP Identity üzembe helyezési [felügyeleti konzolra](https://ips-xlnk9v890j.dispatcher.us1.hana.ondemand.com/) a rendszergazdai fiókjával, majd válassza a **proxy rendszerek** elemet.

   ![SAP-proxy rendszerek](./media/sap-analytics-cloud-provisioning-tutorial/sap-proxy-systems.png.png)

2. Válassza ki a **Tulajdonságok** elemet.

   ![SAP-proxy rendszerek tulajdonságai](./media/sap-analytics-cloud-provisioning-tutorial/sap-proxy-systems-properties.png)

3. Másolja ki az **URL-címet** , és illessze `/api/v1/scim` be az URL-címre. Mentse ezt később a **bérlői URL-cím** mezőben való használatra.

   ![SAP-proxy rendszerek tulajdonságai URL-címe](./media/sap-analytics-cloud-provisioning-tutorial/sap-proxy-systems-details-url.png)

4. A [Poster](https://www.postman.com/) használatával HTTPS-hívást hajthat végre a következő címre: `<Token URL>?grant_type=client_credentials` hol `Token URL` található az URL-cím a **OAuth2TokenServiceURL** mezőben. Ez a lépés szükséges ahhoz, hogy létrehozzon egy hozzáférési jogkivonatot, amelyet a titkos jogkivonat mezőben kell használni az automatikus kiépítés konfigurálásakor.

   ![SAP IP-proxy rendszerek tulajdonságai OAuth](./media/sap-analytics-cloud-provisioning-tutorial/sap-proxy-systems-details-oauth.png)

5. A Poster-ben használja az **egyszerű hitelesítést**, és állítsa be a OAuth ügyfél-azonosítót a felhasználóként és a titkos kulcsot jelszóként. Ez a hívás egy hozzáférési jogkivonatot ad vissza. Ezt a másolást később is megtarthatja a **titkos jogkivonat** mezőben való használathoz.

   ![Post Post kérelem](./media/sap-analytics-cloud-provisioning-tutorial/postman-post-request.png)

## <a name="step-3-add-sap-analytics-cloud-from-the-azure-ad-application-gallery"></a>3. lépés SAP Analytics-felhő hozzáadása az Azure AD Application Galleryből

SAP Analytics-felhő hozzáadása az Azure AD-alkalmazás-katalógusból az SAP Analytics-felhőbe való kiépítés kezelésének megkezdéséhez. Ha korábban már beállította az SAP Analytics Cloud for SSO szolgáltatást, használhatja ugyanazt az alkalmazást. Az integráció első tesztelésekor azonban érdemes létrehozni egy külön alkalmazást. Az alkalmazások katalógusból való hozzáadásáról [itt](../manage-apps/add-application-portal.md) tudhat meg többet. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Az átadás hatókörében lévő személyek meghatározása 

Az Azure AD átadási szolgáltatása lehetővé teszi az átadott személyek hatókörének meghatározását az alkalmazáshoz való hozzárendelés és/vagy a felhasználó/csoport attribútumai alapján. Ha a hozzárendelés alapján történő hatókör-meghatározást választja, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet felhasználókat és csoportokat az alkalmazáshoz. Ha csak a felhasználó vagy csoport attribútumai alapján történő hatókörmeghatározást választja, az [itt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) leírt hatókörszűrőt használhatja. 

* Amikor felhasználókat és csoportokat rendel az SAP Analytics-felhőhöz, ki kell választania az **alapértelmezett hozzáféréstől** eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva az átadásból, és az átadási naplókban nem jogosultként lesznek megjelölve. Ha az alkalmazáshoz csak az alapértelmezett hozzáférési szerepkör érhető el, akkor további szerepkörök felvételéhez [frissítheti az alkalmazásjegyzéket](../develop/howto-add-app-roles-in-azure-ad-apps.md). 

* Kezdje kicsiben. Tesztelje a felhasználók és csoportok kis halmazát, mielőtt mindenkire kiterjesztené. Amikor az átadás hatóköre a hozzárendelt felhasználókra és csoportokra van beállítva, ennek szabályozásához egy vagy két felhasználót vagy csoportot rendelhet az alkalmazáshoz. Amikor a hatókör az összes felhasználóra és csoportra van beállítva, meghatározhat egy [attribútumalapú hatókörszűrőt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-sap-analytics-cloud"></a>5. lépés Az automatikus felhasználó-kiépítés beállítása az SAP Analytics-felhőbe 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy TestApp alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

### <a name="to-configure-automatic-user-provisioning-for-sap-analytics-cloud-in-azure-ad"></a>Az SAP Analytics-felhő automatikus felhasználói üzembe helyezésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza az **SAP Analytics-felhő** elemet.

    ![Az SAP Analytics felhőalapú hivatkozása az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a kiépítési mód legördülő listájáról az automatikus lehetőséggel.](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a bérlő URL **-címében** korábban LEKÉRT bérlői URL-cím értékét. Adja meg a korábban a **titkos tokenben** lekért hozzáférési jogkivonat értékét. Kattintson a **kapcsolat tesztelése** lehetőségre, hogy az Azure ad képes legyen csatlakozni a InVision. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy az SAP Analytics felhőalapú fiókja rendelkezik rendszergazdai jogosultságokkal, és próbálkozzon újra.

    ![Képernyőfelvétel: a rendszergazdai hitelesítő adatok párbeszédpanel, ahol megadhatja a bérlő U R L-t és a titkos jogkivonatot.](./media/sap-analytics-cloud-provisioning-tutorial/provisioning.png)

6. Az **Értesítés e-mailben** mezőben adja meg annak a személynek vagy csoportnak az e-mail-címét, aki az átadással kapcsolatos hibaüzeneteket kapja, és jelölje be az **E-mail-értesítés küldése hiba esetén** jelölőnégyzetet.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók kiépítése** lehetőséget.

9. Tekintse át az Azure AD-ból az SAP Analytics-felhőbe szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelés** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok az SAP Analytics felhő felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. Ha úgy dönt, hogy megváltoztatja a [megfelelő cél attribútumot](../app-provisioning/customize-application-attributes.md), biztosítania kell, hogy az SAP Analytics Cloud API támogassa a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   |Attribútum|Típus|Szűréshez támogatott|
   |---|---|---|
   |userName (Felhasználónév)|Sztring|&check;|
   |name.givenName|Sztring|
   |name.familyName|Sztring|
   |active|Logikai|
   |emails[type eq "work"].value|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: User: Manager|Sztring|

10. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

11. Az Azure AD-kiépítési szolgáltatás SAP Analytics-felhőben való engedélyezéséhez módosítsa a **kiépítési állapotot** **a következőre** a **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

12. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket az SAP Analytics-felhőbe szeretne kiépíteni a **Beállítások** szakasz **hatókörében** lévő kívánt értékek kiválasztásával.

    ![Átadási hatókör](common/provisioning-scope.png)

13. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

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