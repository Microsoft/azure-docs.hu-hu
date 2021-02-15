---
title: 'Oktatóanyag: a Smartsheet konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a Smartsheet.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/07/2019
ms.author: jeedes
ms.openlocfilehash: e9ee994564e175d3c41cfd5ce415ead8c67df353
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100103549"
---
# <a name="tutorial-configure-smartsheet-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés Smartsheet konfigurálása

Az oktatóanyag célja annak bemutatása, hogy milyen lépéseket kell végrehajtani a Smartsheet és a Azure Active Directory (Azure AD) szolgáltatásban az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikusan kiépítsék és kiépítsék a [Smartsheet](https://www.smartsheet.com/pricing). A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a Smartsheet-ben
> * Felhasználók eltávolítása a Smartsheet-ben, ha már nincs szükség hozzáférésre
> * Felhasználói attribútumok szinkronizálása az Azure AD és a Smartsheet között
> * Egyszeri bejelentkezés a Smartsheet-be (ajánlott)

> [!NOTE]
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Egy Azure AD-bérlő](../develop/quickstart-create-new-tenant.md).
* Egy felhasználói fiók az Azure AD-ben az átadás konfigurálására vonatkozó [engedéllyel](../roles/permissions-reference.md) (pl. alkalmazás-rendszergazda, felhőalkalmazás-rendszergazda, alkalmazástulajdonos vagy globális rendszergazda).
* [Egy Smartsheet-bérlő](https://www.smartsheet.com/pricing).
* Felhasználói fiók Smartsheet Enterprise vagy Enterprise Premier csomaggal, rendszergazdai engedélyekkel.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés Az átadás üzembe helyezésének megtervezése
1. Ismerje meg [az átadási szolgáltatás működését](../app-provisioning/user-provisioning.md).
2. Határozza meg, hogy ki lesz [az átadás hatókörében](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Határozza meg, hogy az [Azure ad és a Smartsheet között milyen adatleképezést kell leképezni](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-smartsheet-to-support-provisioning-with-azure-ad"></a>2. lépés Smartsheet konfigurálása az Azure AD-vel való kiépítés támogatásához

Az Azure AD-vel való automatikus Smartsheet konfigurálása előtt engedélyeznie kell a SCIM-létesítést a Smartsheet-on.

1. Jelentkezzen be **rendszergazdaként** a **[Smartsheet-portálon](https://app.smartsheet.com/b/home)** , és navigáljon a **fiók rendszergazdája** elemre.

    ![Smartsheet-fiók rendszergazdája](media/smartsheet-provisioning-tutorial/smartsheet-accountadmin.png)

2. Lépjen a **biztonsági vezérlők > felhasználó automatikus kiépítés > szerkesztés** gombra.

    ![Smartsheet biztonsági vezérlők](media/smartsheet-provisioning-tutorial/smartsheet-securitycontrols.png)

3. Adja hozzá és ellenőrizze az e-mail tartományokat azon felhasználók számára, akik számára az Azure AD-ből Smartsheet kíván kiépíteni. A **nincs engedélyezve** beállítás megadásával biztosíthatja, hogy a kiépítési műveletek csak az Azure ad-ből származzanak, valamint hogy a Smartsheet-felhasználók listája szinkronban legyen az Azure ad-hozzárendelésekkel.

    ![Smartsheet-felhasználó kiépítés](media/smartsheet-provisioning-tutorial/smartsheet-userprovisioning.png)

4. Az érvényesítés befejeződése után aktiválni kell a tartományt. 

    ![Smartsheet aktiválása tartomány](media/smartsheet-provisioning-tutorial/smartsheet-activatedomain.png)

5. Az **alkalmazásokhoz és az integrációhoz** való navigálással létrehozhatja az automatikus felhasználó-kiépítés az Azure ad-vel való konfigurálásához szükséges **titkos jogkivonatot** .

    ![Képernyőkép a Smartsheet-adminisztrátor oldalról, a felhasználó avatárja és az alkalmazások & integrációk lehetőség.](media/smartsheet-provisioning-tutorial/Smartsheet05.png)

6. Válassza az **API-hozzáférés** lehetőséget. Kattintson az **új hozzáférési jogkivonat előállítása** elemre.

    ![Képernyőkép a személyes beállítások párbeszédpanelről, amely az API-hozzáféréssel rendelkezik, és új hozzáférési jogkivonat-beállításokat hoz végre.](media/smartsheet-provisioning-tutorial/Smartsheet06.png)

7. Adja meg az API-hozzáférési jogkivonat nevét. Kattintson az **OK** gombra.

    ![Képernyőfelvétel a 2. lépésről: API-hozzáférési jogkivonat előállítása az OK lehetőséggel.](media/smartsheet-provisioning-tutorial/Smartsheet07.png)

8. Másolja az API-hozzáférési jogkivonatot, és mentse, mivel ez az egyetlen időpontig megtekinthető. Erre szükség van az Azure AD **titkos jogkivonat** mezőjében.

    ![Smartsheet token](media/smartsheet-provisioning-tutorial/Smartsheet08.png)

## <a name="step-3-add-smartsheet-from-the-azure-ad-application-gallery"></a>3. lépés Smartsheet hozzáadása az Azure AD Application Galleryből

Vegyen fel Smartsheet az Azure AD-alkalmazás-katalógusból a Smartsheet való kiépítés kezelésének megkezdéséhez. Ha korábban már beállította a Smartsheet az SSO-hoz, használhatja ugyanazt az alkalmazást. Az integráció első tesztelésekor azonban érdemes létrehozni egy külön alkalmazást. Az alkalmazások katalógusból való hozzáadásáról [itt](../manage-apps/add-application-portal.md) tudhat meg többet. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Az átadás hatókörében lévő személyek meghatározása 

Az Azure AD átadási szolgáltatása lehetővé teszi az átadott személyek hatókörének meghatározását az alkalmazáshoz való hozzárendelés és/vagy a felhasználó/csoport attribútumai alapján. Ha a hozzárendelés alapján történő hatókör-meghatározást választja, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet felhasználókat és csoportokat az alkalmazáshoz. Ha csak a felhasználó vagy csoport attribútumai alapján történő hatókörmeghatározást választja, az [itt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) leírt hatókörszűrőt használhatja. 

* Felhasználók és csoportok Smartsheet való hozzárendeléséhez ki kell választania az **alapértelmezett hozzáféréstől** eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva az átadásból, és az átadási naplókban nem jogosultként lesznek megjelölve. Ha az alkalmazáshoz csak az alapértelmezett hozzáférési szerepkör érhető el, akkor további szerepkörök felvételéhez [frissítheti az alkalmazásjegyzéket](../develop/howto-add-app-roles-in-azure-ad-apps.md). 

* A Smartsheet és az Azure AD közötti felhasználói szerepkör-hozzárendelések paritásának biztosítása érdekében ajánlott ugyanazokat a szerepkör-hozzárendeléseket használni, amelyek a teljes Smartsheet-felhasználók listájában fel vannak töltve. A felhasználói lista Smartsheet való lekéréséhez navigáljon a **fiók rendszergazdája > a felhasználói felügyelet > további műveletek > a felhasználói lista letöltése (CSV)** elemre.

* Az alkalmazás egyes funkcióinak eléréséhez a Smartsheet-nek több szerepkörrel kell rendelkeznie. Ha többet szeretne megtudni a Smartsheet felhasználói típusairól és engedélyeiről, lépjen a [Felhasználói típusok és engedélyek](https://help.smartsheet.com/learning-track/shared-users/user-types-and-permissions)elemre.

*  Ha egy felhasználó több, a Smartsheet-ban hozzárendelt szerepkörrel rendelkezik, meg **kell** győződnie arról, hogy a szerepkör-hozzárendelések replikálva vannak az Azure ad-ben, így elkerülhető, hogy a felhasználók ne férhessenek hozzá a Smartsheet objektumokhoz. A Smartsheet minden egyes egyedi szerepkörét **hozzá kell rendelni** egy másik csoporthoz az Azure ad-ben. **Ezután a felhasználót hozzá kell adni** a kívánt szerepköröknek megfelelő csoportokhoz. 

* Kezdje kicsiben. Tesztelje a felhasználók és csoportok kis halmazát, mielőtt mindenkire kiterjesztené. Amikor az átadás hatóköre a hozzárendelt felhasználókra és csoportokra van beállítva, ennek szabályozásához egy vagy két felhasználót vagy csoportot rendelhet az alkalmazáshoz. Amikor a hatókör az összes felhasználóra és csoportra van beállítva, meghatározhat egy [attribútumalapú hatókörszűrőt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

## <a name="step-5-configure-automatic-user-provisioning-to-smartsheet"></a>5. lépés Automatikus felhasználó-kiépítés beállítása a Smartsheet 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy Smartsheet alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

### <a name="to-configure-automatic-user-provisioning-for-smartsheet-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a Smartsheet az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Smartsheet** lehetőséget.

    ![Az Smartsheet hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a kiépítési mód legördülő listájáról az automatikus lehetőséggel.](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a **scim 2,0 alap URL-címet és a hozzáférési jogkivonat** azon értékeit, amelyeket a rendszer a **bérlői URL-cím** és a **titkos jogkivonat** Smartsheet korábban lekérdezett. Kattintson a **kapcsolat tesztelése** lehetőségre, hogy az Azure ad képes legyen csatlakozni a Smartsheet. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Smartsheet-fiók rendelkezik SysAdmin engedélyekkel, és próbálkozzon újra.

    ![Jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba** esetén.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a Smartsheet** lehetőséget.

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban található Smartsheet. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Smartsheet felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   |Attribútum|Típus|Szűréshez támogatott|
   |---|---|---|
   |userName (Felhasználónév)|Sztring|&check;|
   |active|Logikai|
   |cím|Sztring|
   |name.givenName|Sztring|
   |name.familyName|Sztring|
   |phoneNumbers[type eq "work"].value|Sztring|
   |phoneNumbers[type eq "mobile"].value|Sztring|
   |phoneNumbers [type EQ "fax"]. Value|Sztring|
   |emails[type eq "work"].value|Sztring|
   |externalId|Sztring|
   |szerepkörök|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: részleg|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: User: Division|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: costCenter|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: User: Manager|Sztring|


10. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

11. Az Azure AD-kiépítési szolgáltatás Smartsheet való engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

12. Adja meg a Smartsheet kiépíteni kívánt felhasználókat és/vagy csoportokat a **Settings (beállítások** ) szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Átadási hatókör](common/provisioning-scope.png)

13. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. 

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Az átadás konfigurálása után a következő erőforrásokkal monitorozhatja az üzemelő példányt:

1. Az [átadási naplókkal](../reports-monitoring/concept-provisioning-logs.md) határozhatja meg, hogy mely felhasználók átadása sikeres, és melyeké sikertelen.
2. A [folyamatjelzőn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) láthatja az átadási ciklus állapotát és azt, hogy mennyi hiányzik még a befejeződéséhez.
3. Ha úgy tűnik, hogy az átadási konfiguráció állapota nem megfelelő, az alkalmazás karanténba kerül. A karanténállapotokról [itt](../app-provisioning/application-provisioning-quarantine-status.md) találhat további információt.  

## <a name="connector-limitations"></a>Összekötő korlátozásai

* A Smartsheet nem támogatja a Soft-delete használatát. Ha a felhasználó **aktív** attribútuma hamis értékre van állítva, a Smartsheet véglegesen törli a felhasználót.

## <a name="change-log"></a>Változási napló

* 06/16/2020 – a felhasználók számára a "Cost Center", a "Division", a "Manager" és a "Department" nagyvállalati szintű bővítmények támogatása.
* 02/10/2021 – a felhasználók számára az "e-mailek [type EQ" work "]" alapvető attribútumok támogatása.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)