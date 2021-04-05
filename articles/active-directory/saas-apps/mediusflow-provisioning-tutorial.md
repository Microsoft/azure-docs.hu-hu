---
title: 'Oktatóanyag: a MediusFlow konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és kiépíteni felhasználói fiókjait az Azure AD-ből a MediusFlow.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/30/2020
ms.author: Zhchia
ms.openlocfilehash: a49258208f7a5945ac71c8f17db56fccfdcd6515
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98662000"
---
# <a name="tutorial-configure-mediusflow-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés MediusFlow konfigurálása

Ez az oktatóanyag azokat a lépéseket ismerteti, amelyeket a MediusFlow és a Azure Active Directory (Azure AD) szolgáltatásban kell végrehajtania az automatikus felhasználó-kiépítés konfigurálásához. Ha konfigurálva van, az Azure AD automatikusan kiépíti és kiosztja a felhasználókat és csoportokat az Azure AD kiépítési szolgáltatás [MediusFlow](https://www.mediusflow.com/) . A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a MediusFlow-ben
> * Felhasználók eltávolítása a MediusFlow-ben, ha már nincs szükség hozzáférésre
> * Felhasználói attribútumok szinkronizálása az Azure AD és a MediusFlow között
> * Csoportok és csoporttagságok kiépítése a MediusFlow-ben
> * Egyszeri bejelentkezés a MediusFlow-be (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Azure AD-bérlő](../develop/quickstart-create-new-tenant.md) 
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](../roles/permissions-reference.md) a kiépítés konfigurálására (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda, alkalmazás tulajdonosa vagy globális rendszergazda). 
* Aktív MediusFlow-előfizetés minőségbiztosítási vagy üzemi Bérlővel.
* A MediusFlow felhasználói fiókjának rendszergazdai hozzáférési jogosultságokkal kell rendelkezniük a konfiguráció végrehajtásához a MediusFlow belül.
* Azok a vállalatok, amelyek a MediusFlow-bérlőhöz vannak hozzáadva, ahol a felhasználókat üzembe kell helyezni.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés Az átadás üzembe helyezésének megtervezése
1. Ismerje meg [az átadási szolgáltatás működését](../app-provisioning/user-provisioning.md).
2. Határozza meg, hogy ki lesz [az átadás hatókörében](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Határozza meg, hogy az [Azure ad és a MediusFlow között milyen adatleképezést kell leképezni](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-mediusflow-to-support-provisioning-with-azure-ad"></a>2. lépés MediusFlow konfigurálása az Azure AD-vel való kiépítés támogatásához

### <a name="activate-the-microsoft-365-app-within-mediusflow"></a>Aktiválja a Microsoft 365 alkalmazást a MediusFlow-n belül
Először engedélyezze az Azure AD-bejelentkezés és az Azure AD konfigurációs funkciójának a MediusFlow-n belüli elérését az alábbi lépések végrehajtásával:

#### <a name="user-login"></a>Felhasználói bejelentkezés
Ha engedélyezni szeretné a bejelentkezést Microsoft 365/Azure AD-re, tekintse át a következőt: [this] ( https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#user-login-setup) cikk.

#### <a name="user-transfer-configuration"></a>Felhasználói átvitel konfigurációja
Ha engedélyezni szeretné az Azure AD-ből való kiépítési felhasználók konfigurációs portálját, olvassa el [ezt](
https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#user-sync-setup) a cikket.

#### <a name="configure-user-provisioning"></a>Felhasználók átadásának konfigurálása

1.  Jelentkezzen be a [MediusFlow felügyeleti konzolra](https://office365.cloudapp.mediusflow.com/) a bérlő azonosítójának megadásával.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/1-auth.png" alt-text="Képernyőkép a MediusFlow felügyeleti konzolról. A MediusFlow-bérlő neve és a hitelesítés gomb ki van emelve az első integrációs lépésben." border="false":::

2. Ellenőrizze a MediusFlow-vel való kapcsolatokat.

    ![Ellenőrzés](./media/mediusflow-provisioning-tutorial/2-verify-connection.png)

3. Adja meg az Azure AD-bérlő AZONOSÍTÓját.

    ![adja meg a bérlő AZONOSÍTÓját](./media/mediusflow-provisioning-tutorial/3-provide-azuread-tenantid.png)

   További tudnivalókat a [Gyakori kérdések](https://success.mediusflow.com/documentation/administration_guide/user_login_and_transfer/office365userintegration/#how-do-i-get-the-azure-tenant-id) között találhat.

4. Mentse a konfigurációt.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/4-save-config.png" alt-text="Képernyőkép a MediusFlow felügyeleti konzolról, amely a negyedik integrációs lépést mutatja. A konfiguráció mentése gomb ki van emelve." border="false":::

5. Válassza a felhasználó kiépítés lehetőséget, majd kattintson **az OK** gombra.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/5-select-user-provisioning.png" alt-text="Képernyőkép a MediusFlow felügyeleti konzolról, amely az ötödik integrációs lépést mutatja. A felhasználó kiépítés használata és az OK gomb kiemelve." border="false":::

6. Kattintson a **titkos kulcs előállítása** elemre. Másolja és mentse ezt az értéket. Ez az érték a MediusFLow alkalmazás **üzembe** helyezés lapjának **titkos jogkivonat** mezőjében jelenik meg a Azure Portal.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/6-create-secret-1.png" alt-text="Képernyőkép a MediusFlow felügyeleti konzol felhasználó-üzembe helyezési konfiguráció lapján. A titkos kulcs és a másolás gombok ki vannak emelve." border="false":::

7. Kattintson **az OK gombra**.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/7-confirm-secret.png" alt-text="Képernyőkép a MediusFlow felügyeleti konzolról, amely arról tájékoztat, hogy a felhasználóknak az OK gombra kell kattintaniuk egy új titkos kulcs létrehozásához. Az OK gomb ki van emelve." border="false":::

8. Ahhoz, hogy a felhasználók a szerepkörök, a vállalatok és egyéb általános konfigurációk előre definiált készletével importálva legyenek a MediusFlow-ben, először konfigurálnia kell. Először adja hozzá a konfigurációt az **Új konfiguráció hozzáadása** lehetőségre kattintva.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/8-configure-user-configuration-1.png" alt-text="Képernyőkép a MediusFlow felügyeleti konzol felhasználó-üzembe helyezési konfiguráció lapján. Az új konfiguráció hozzáadása gomb ki van emelve." border="false":::

9. Adja meg a felhasználók alapértelmezett beállításait. Ebben a nézetben az alapértelmezett attribútumot lehet beállítani. Ha a normál beállítások rendben vannak, elég, ha csak egy érvényes vállalat nevét adja meg. Mivel ezek a konfigurációs beállítások beolvasása a Mediusflow-ből történik, először konfigurálni kell őket. További információt a jelen cikk **Előfeltételek** című szakaszában talál.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/9-configure-user-config-detail-1.png" alt-text="Képernyőkép a MediusFlow új konfigurációs ablakának hozzáadásáról. Számos beállítás látható, beleértve a területi beállításokat, a szűrőket és a felhasználói szerepköröket." border="false":::

10. A felhasználói konfiguráció mentéséhez kattintson a **Save (Mentés** ) gombra.

    :::image type="content" source="./media/mediusflow-provisioning-tutorial/10-done-1.png" alt-text="Képernyőkép a MediusFlow felügyeleti konzol felhasználó-üzembe helyezési konfiguráció lapján. A Mentés gomb ki van emelve." border="false":::

11. A felhasználó kiépítési hivatkozásának beszerzéséhez kattintson az **scim másolása hivatkozásra**. Másolja és mentse ezt az értéket. Ez az érték a MediusFLow alkalmazás **kiépítés** lapjának **bérlői URL-címe** mezőjében szerepel a Azure Portal.
 
    :::image type="content" source="./media/mediusflow-provisioning-tutorial/11-get-scim-link.png" alt-text="Képernyőkép a MediusFlow felügyeleti konzol felhasználó-üzembe helyezési konfiguráció lapján. A másolás S C I M hivatkozás gomb ki van emelve." border="false":::

## <a name="step-3-add-mediusflow-from-the-azure-ad-application-gallery"></a>3. lépés MediusFlow hozzáadása az Azure AD Application Galleryből

Vegyen fel MediusFlow az Azure AD-alkalmazás-katalógusból a MediusFlow való kiépítés kezelésének megkezdéséhez. Ha korábban már beállította a MediusFlow-t az egyszeri bejelentkezéshez, használhatja ugyanazt az alkalmazást. Az integráció első tesztelésekor azonban érdemes létrehozni egy külön alkalmazást. Az alkalmazások katalógusból való hozzáadásáról [itt](../manage-apps/add-application-portal.md) tudhat meg többet. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Az átadás hatókörében lévő személyek meghatározása 

Az Azure AD átadási szolgáltatása lehetővé teszi az átadott személyek hatókörének meghatározását az alkalmazáshoz való hozzárendelés és/vagy a felhasználó/csoport attribútumai alapján. Ha a hozzárendelés alapján történő hatókör-meghatározást választja, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet felhasználókat és csoportokat az alkalmazáshoz. Ha csak a felhasználó vagy csoport attribútumai alapján történő hatókörmeghatározást választja, az [itt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) leírt hatókörszűrőt használhatja. 

* Felhasználók és csoportok MediusFlow való hozzárendeléséhez ki kell választania az **alapértelmezett hozzáféréstől** eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva az átadásból, és az átadási naplókban nem jogosultként lesznek megjelölve. Ha az alkalmazáshoz csak az alapértelmezett hozzáférési szerepkör érhető el, akkor további szerepkörök felvételéhez [frissítheti az alkalmazásjegyzéket](../develop/howto-add-app-roles-in-azure-ad-apps.md). 

* Kezdje kicsiben. Tesztelje a felhasználók és csoportok kis halmazát, mielőtt mindenkire kiterjesztené. Ha a kiépítés hatóköre a hozzárendelt felhasználókhoz és csoportokhoz van beállítva, akkor azt úgy szabályozhatja, hogy egy vagy két felhasználót vagy csoportot rendel hozzá az alkalmazáshoz. Amikor a hatókör az összes felhasználóra és csoportra van beállítva, meghatározhat egy [attribútumalapú hatókörszűrőt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-mediusflow"></a>5. lépés Automatikus felhasználó-kiépítés beállítása a MediusFlow 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy TestApp alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

### <a name="to-configure-automatic-user-provisioning-for-mediusflow-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a MediusFlow az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **MediusFlow** lehetőséget.

    ![Az MediusFlow hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a kiépítési mód legördülő listájáról az automatikus lehetőséggel.](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a bérlő URL **-címében** korábban LEKÉRT bérlői URL-cím értékét. Adja meg a titkos jogkivonat értékét, amely korábban a **titkos jogkivonatban** lett lekérve. Kattintson a **kapcsolat tesztelése** lehetőségre, hogy az Azure ad képes legyen csatlakozni a MediusFlow. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a MediusFlow-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

      ![Képernyőfelvétel: a rendszergazdai hitelesítő adatok párbeszédpanel, ahol megadhatja a bérlő U R L-t és a titkos jogkivonatot.](./media/mediusflow-provisioning-tutorial/provisioning.png)

6. Az **Értesítés e-mailben** mezőben adja meg annak a személynek vagy csoportnak az e-mail-címét, aki az átadással kapcsolatos hibaüzeneteket kapja, és jelölje be az **E-mail-értesítés küldése hiba esetén** jelölőnégyzetet.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a MediusFlow** lehetőséget.

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban lévő MediusFlow. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a MediusFlow felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. Ha úgy dönt, hogy módosítja a [megfelelő cél attribútumot](../app-provisioning/customize-application-attributes.md), akkor biztosítania kell, hogy a MediusFlow API támogassa a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   |Attribútum|Típus|Szűréshez támogatott|
   |---|---|---|
   |userName (Felhasználónév)|Sztring|&check;|
   |emails[type eq "work"].value|Sztring|
   |név. displayName|Sztring|
   |active|Logikai|
   |name.givenName|Sztring|
   |name.familyName|Sztring|
   |név. formázott|Sztring|
   |externalId|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: User: Manager|Referencia|
   |urn: IETF: params: scim: sémák: bővítmény: medius: 2.0: felhasználó: configurationFilter|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: medius: 2.0: felhasználó: identityProvider|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: medius: 2.0: felhasználó: nameIdentifier|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: medius: 2.0: felhasználó: customFieldText1|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: medius: 2.0: felhasználó: customFieldText2|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: medius: 2.0: felhasználó: customFieldText3|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: medius: 2.0: felhasználó: customFieldText4|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: medius: 2.0: felhasználó: customFieldText5|Sztring|


10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása a MediusFlow** lehetőséget.

11. Tekintse át az Azure AD-ből szinkronizált MediusFlow az **attribútum-hozzárendelés** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a MediusFlow tartozó csoportok egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    | Attribútum | Típus |
    |--|--|
    | displayName | Sztring |
    | externalID | Sztring |
    | tagok | Referencia |

12. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

13. Az Azure AD-kiépítési szolgáltatás MediusFlow való engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg a MediusFlow kiépíteni kívánt felhasználókat és/vagy csoportokat a **Settings (beállítások** ) szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Átadási hatókör](common/provisioning-scope.png)

15. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet a **Beállítások** szakasz **Hatókör** területén meghatározott összes felhasználó és csoport kezdeti szinkronizálási ciklusát elindítja. A kezdeti ciklus elvégzése hosszabb időt vesz igénybe, mint a későbbi ciklusok, amelyek az Azure AD átadási szolgáltatásának futtatása során körülbelül 40 percenként lesznek végrehajtva. 

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Az átadás konfigurálása után a következő erőforrásokkal monitorozhatja az üzemelő példányt:

1. Az [átadási naplókkal](../reports-monitoring/concept-provisioning-logs.md) határozhatja meg, hogy mely felhasználók átadása sikeres, és melyeké sikertelen.
2. A [folyamatjelzőn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) láthatja az átadási ciklus állapotát és azt, hogy mennyi hiányzik még a befejeződéséhez.
3. Ha úgy tűnik, hogy az átadási konfiguráció állapota nem megfelelő, az alkalmazás karanténba kerül. A karanténállapotokról [itt](../app-provisioning/application-provisioning-quarantine-status.md) találhat további információt.

## <a name="change-log"></a>Változási napló

* 01/21/2021 – az egyéni bővítmény attribútumai **configurationFilter**, **identityProvider**, **nameIdentifier**, **customFieldText1**, **customFieldText2**, **customFieldText3**, **customFieldText3** és **customFieldText5** lettek hozzáadva.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)