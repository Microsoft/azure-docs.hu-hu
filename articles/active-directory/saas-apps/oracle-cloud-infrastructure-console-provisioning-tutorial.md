---
title: 'Oktatóanyag: az Oracle felhőalapú infrastruktúra-konzol konfigurálása a felhasználók automatikus kiépítéséhez Azure Active Directorysal | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és kiépíteni felhasználói fiókjait az Azure AD-ből az Oracle felhőalapú infrastruktúra-konzolra.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: Zhchia
ms.openlocfilehash: 94de0ca0a5393c891e567e558cbbadd0ca1f453b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97832094"
---
# <a name="tutorial-configure-oracle-cloud-infrastructure-console-for-automatic-user-provisioning"></a>Oktatóanyag: az Oracle felhőalapú infrastruktúra-konzol konfigurálása a felhasználók automatikus üzembe helyezéséhez

Ez az oktatóanyag azokat a lépéseket ismerteti, amelyeket az Oracle Cloud Infrastructure konzolon és a Azure Active Directory (Azure AD) a felhasználók automatikus kiépítésének konfigurálásához kell végrehajtania. Ha konfigurálva van, az Azure AD automatikusan kiépíti és kiosztja a felhasználókat és csoportokat az [Oracle felhőalapú infrastruktúra-konzolon](https://www.oracle.com/cloud/free/?source=:ow:o:p:nav:0916BCButton&intcmp=:ow:o:p:nav:0916BCButton) az Azure ad-létesítési szolgáltatás használatával. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása az Oracle Cloud Infrastructure konzolon
> * Távolítsa el a felhasználókat az Oracle Cloud Infrastructure konzolon, ha már nincs szükség hozzáférésre
> * Felhasználói attribútumok szinkronizálása az Azure AD és az Oracle felhőalapú infrastruktúra konzolja között
> * Csoportok és csoporttagságok kiépítése az Oracle Cloud Infrastructure konzolon
> * [Egyszeri bejelentkezés](./oracle-cloud-tutorial.md) az Oracle Cloud Infrastructure Console-konzolra (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Azure AD-bérlő](../develop/quickstart-create-new-tenant.md) 
* Egy felhasználói fiók az Azure AD-ben az átadás konfigurálására vonatkozó [engedéllyel](../roles/permissions-reference.md) (pl. alkalmazás-rendszergazda, felhőalkalmazás-rendszergazda, alkalmazástulajdonos vagy globális rendszergazda). 
* Egy Oracle felhőalapú infrastruktúra-konzol [bérlője](https://www.oracle.com/cloud/sign-in.html?intcmp=OcomFreeTier&source=:ow:o:p:nav:0916BCButton).
* Egy felhasználói fiók az Oracle Cloud Infrastructure-konzolon rendszergazdai jogosultságokkal.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés Az átadás üzembe helyezésének megtervezése
1. Ismerje meg [az átadási szolgáltatás működését](../app-provisioning/user-provisioning.md).
2. Határozza meg, hogy ki lesz [az átadás hatókörében](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Határozza meg, hogy az [Azure ad és az Oracle felhőalapú infrastruktúra-konzolja milyen adatleképezési](../app-provisioning/customize-application-attributes.md)leképezéseket. 

## <a name="step-2-configure-oracle-cloud-infrastructure-console-to-support-provisioning-with-azure-ad"></a>2. lépés Az Oracle felhőalapú infrastruktúra konzoljának konfigurálása az Azure AD-vel való kiépítés támogatására

1. Jelentkezzen be az Oracle felhőalapú infrastruktúra konzoljának felügyeleti portálján. A képernyő bal felső sarkában navigáljon az **Identity >-összevonás** elemre.

    ![Oracle-rendszergazda](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/identity.png)

2. Kattintson a lapon megjelenő URL-címre az Oracle Identity Cloud Service Console mellett.

    ![Oracle URL-cím](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/url.png)

3. Az új identitás-szolgáltató létrehozásához kattintson az **identitás hozzáadása** lehetőségre. Mentse a identitásszolgáltató-azonosítót a bérlői URL-cím részeként való használatra. Az **alkalmazások** lap melletti plusz ikonra kattintva hozzon létre egy OAuth-ügyfelet, és adja meg a IDCS Identity domain Administrator approle példányoknak.

    ![Oracle Cloud ikon](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/add.png)

4. Az alkalmazás konfigurálásához kövesse az alábbi képernyőképeket. Ha a konfiguráció elkészült, kattintson a **Mentés** gombra.

    ![Oracle-konfiguráció](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/configuration.png)

    ![Oracle-jogkivonat kiállítási szabályzata](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/token-issuance.png)

5. Az alkalmazás konfigurációk lapján bontsa ki az **általános információ** lehetőséget az ügyfél-azonosító és az ügyfél titkos kódjának lekéréséhez.

    ![Oracle-jogkivonat létrehozása](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/general-information.png)

6. A titkos token Base64-kódolásához az ügyfél-azonosító és az ügyfél titkos kulcsát a következő formátumban kell megadni **: Client Secret**(ügyfél-azonosító). Mentse a titkos jogkivonatot. Ez az érték jelenik meg az Oracle Cloud Infrastructure Console alkalmazás üzembe helyezés lapjának **titkos jogkivonat** mezőjében a Azure Portal.

## <a name="step-3-add-oracle-cloud-infrastructure-console-from-the-azure-ad-application-gallery"></a>3. lépés Oracle Cloud Infrastructure-konzol hozzáadása az Azure AD Application Galleryből

Vegye fel az Oracle Cloud Infrastructure-konzolt az Azure AD-alkalmazás-katalógusból az Oracle felhőalapú infrastruktúra-konzolra való kiépítés kezelésének megkezdéséhez. Ha korábban már beállította az Oracle felhőalapú infrastruktúra-konzolt az SSO-hoz, használhatja ugyanazt az alkalmazást. Az integráció első tesztelésekor azonban érdemes létrehozni egy külön alkalmazást. Az alkalmazások katalógusból való hozzáadásáról [itt](../manage-apps/add-application-portal.md) tudhat meg többet. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Az átadás hatókörében lévő személyek meghatározása 

Az Azure AD átadási szolgáltatása lehetővé teszi az átadott személyek hatókörének meghatározását az alkalmazáshoz való hozzárendelés és/vagy a felhasználó/csoport attribútumai alapján. Ha a hozzárendelés alapján történő hatókör-meghatározást választja, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet felhasználókat és csoportokat az alkalmazáshoz. Ha csak a felhasználó vagy csoport attribútumai alapján történő hatókörmeghatározást választja, az [itt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) leírt hatókörszűrőt használhatja. 

* Amikor felhasználókat és csoportokat rendel az Oracle Cloud Infrastructure-konzolhoz, ki kell választania az **alapértelmezett hozzáféréstől** eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva az átadásból, és az átadási naplókban nem jogosultként lesznek megjelölve. Ha az alkalmazáshoz csak az alapértelmezett hozzáférési szerepkör érhető el, akkor további szerepkörök felvételéhez [frissítheti az alkalmazásjegyzéket](../develop/howto-add-app-roles-in-azure-ad-apps.md). 

* Kezdje kicsiben. Tesztelje a felhasználók és csoportok kis halmazát, mielőtt mindenkire kiterjesztené. Amikor az átadás hatóköre a hozzárendelt felhasználókra és csoportokra van beállítva, ennek szabályozásához egy vagy két felhasználót vagy csoportot rendelhet az alkalmazáshoz. Amikor a hatókör az összes felhasználóra és csoportra van beállítva, meghatározhat egy [attribútumalapú hatókörszűrőt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-oracle-cloud-infrastructure-console"></a>5. lépés Az automatikus felhasználó-kiépítés beállítása az Oracle felhőalapú infrastruktúra-konzolra 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy TestApp alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

### <a name="to-configure-automatic-user-provisioning-for-oracle-cloud-infrastructure-console-in-azure-ad"></a>Az Oracle felhőalapú infrastruktúra konzoljának automatikus kiépítésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza az **Oracle Cloud Infrastructure Console** elemet.

    ![Az Oracle felhőalapú infrastruktúra konzoljának hivatkozása az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a kiépítési mód legördülő listájáról az automatikus lehetőséggel.](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a **bérlői URL-címet** a formátumban `https://<IdP ID>.identity.oraclecloud.com/admin/v1` . Például: `https://idcs-0bfd023ff2xx4a98a760fa2c31k92b1d.identity.oraclecloud.com/admin/v1`. Adja meg a titkos jogkivonat értékét, amely korábban a **titkos jogkivonatban** lett lekérve. Kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad csatlakozni tudjanak az Oracle Cloud Infrastructure-konzolhoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy az Oracle felhőalapú infrastruktúra-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Képernyőfelvétel: a rendszergazdai hitelesítő adatok párbeszédpanel, ahol megadhatja a bérlő U R L-t és a titkos jogkivonatot.](./media/oracle-cloud-infratstructure-console-provisioning-tutorial/provisioning.png)

6. Az **Értesítés e-mailben** mezőben adja meg annak a személynek vagy csoportnak az e-mail-címét, aki az átadással kapcsolatos hibaüzeneteket kapja, és jelölje be az **E-mail-értesítés küldése hiba esetén** jelölőnégyzetet.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása Oracle Cloud Infrastructure-konzolra** lehetőséget.

9. Tekintse át az Azure AD-ból az Oracle Cloud Infrastructure Console-be szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelés** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok az Oracle Cloud Infrastructure Console felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. Ha úgy dönt, hogy megváltoztatja a [megfelelő cél attribútumot](../app-provisioning/customize-application-attributes.md), akkor biztosítania kell, hogy az Oracle Cloud Infrastructure Console API támogassa a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    |Attribútum|Típus|
    |---|---|
    |displayName|Sztring|
    |userName (Felhasználónév)|Sztring|
    |active|Logikai|
    |cím|Sztring|
    |emails[type eq "work"].value|Sztring|
    |preferredLanguage|Sztring|
    |name.givenName|Sztring|
    |name.familyName|Sztring|
    |címek [type EQ "work"]. formázott|Sztring|
    |címek [típus EQ "work"]. helység|Sztring|
    |címek [típus EQ "work"]. régió|Sztring|
    |címek [type EQ "work"]. irányítószám|Sztring|
    |címek [type EQ "work"]. Country|Sztring|
    |címek [type EQ "work"]. streetAddress|Sztring|
    |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: employeeNumber|Sztring|
    |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: részleg|Sztring|
    |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: costCenter|Sztring|
    |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: User: Division|Sztring|
    |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: User: Manager|Referencia|
    |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: szervezet|Sztring|
    |urn: IETF: params: scim: sémák: Oracle: IDCs: bővítmény: felhasználó: felhasználó: bypassNotification|Logikai|
    |urn: IETF: params: scim: sémák: Oracle: IDCs: bővítmény: felhasználó: felhasználó: isFederatedUser|Logikai|

10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása Oracle Cloud Infrastructure-konzolra** lehetőséget.

11. Tekintse át az Azure AD-ből szinkronizált csoportosítási attribútumokat az Oracle felhő-infrastruktúra konzolra az **attribútum-hozzárendelés** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a frissítési műveletekhez használhatók az Oracle Cloud Infrastructure konzolon lévő csoportok egyeztetéséhez. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    | Attribútum | Típus |
    |--|--|
    | displayName | Sztring |
    | externalId | Sztring |
    | tagok | Referencia |

12. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

13. Ha engedélyezni szeretné az Azure AD-kiépítési szolgáltatást az Oracle felhőalapú infrastruktúra konzolján, módosítsa a **kiépítési állapotot** **a következőre** a **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg azokat a felhasználókat és/vagy csoportokat, amelyeket szeretne kiépíteni az Oracle felhőalapú infrastruktúra-konzolra. Ehhez válassza ki a kívánt értékeket a **hatókörben** a **Beállítások** szakaszban.

    ![Átadási hatókör](common/provisioning-scope.png)

15. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet a **Beállítások** szakasz **Hatókör** területén meghatározott összes felhasználó és csoport kezdeti szinkronizálási ciklusát elindítja. A kezdeti ciklus elvégzése hosszabb időt vesz igénybe, mint a későbbi ciklusok, amelyek az Azure AD átadási szolgáltatásának futtatása során körülbelül 40 percenként lesznek végrehajtva. 

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Az átadás konfigurálása után a következő erőforrásokkal monitorozhatja az üzemelő példányt:

* Az [átadási naplókkal](../reports-monitoring/concept-provisioning-logs.md) határozhatja meg, hogy mely felhasználók átadása sikeres, és melyeké sikertelen.
* A [folyamatjelzőn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) láthatja az átadási ciklus állapotát és azt, hogy mennyi hiányzik még a befejeződéséhez.
* Ha úgy tűnik, hogy az átadási konfiguráció állapota nem megfelelő, az alkalmazás karanténba kerül. A karanténállapotokról [itt](../app-provisioning/application-provisioning-quarantine-status.md) találhat további információt.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)
