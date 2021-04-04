---
title: 'Oktatóanyag: a 15Five konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a 15Five.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 00f713e8d319d7ad8dcea014429c57d7fba40541
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96181220"
---
# <a name="tutorial-configure-15five-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés 15Five konfigurálása

Az oktatóanyag célja annak bemutatása, hogy milyen lépéseket kell végrehajtani a 15Five és a Azure Active Directory (Azure AD) szolgáltatásban az Azure AD konfigurálásához, hogy a felhasználók és/vagy csoportok automatikusan kiépítsék és kiépítsék a [15Five](https://www.15five.com/pricing/). A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval.

> [!NOTE]
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. Az előzetes verziójú funkciók általános Microsoft Azure használati feltételeivel kapcsolatos további információkért tekintse meg a [Microsoft Azure-előnézetek kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a 15Five-ben
> * Felhasználók eltávolítása a 15Five-ben, ha már nincs szükség hozzáférésre
> * Felhasználói attribútumok szinkronizálása az Azure AD és a 15Five között
> * Csoportok és csoporttagságok kiépítése a 15Five-ben
> * [Egyszeri bejelentkezés](./15five-tutorial.md) a 15Five-be (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Egy Azure ad-bérlő](../develop/quickstart-create-new-tenant.md) .
* Egy felhasználói fiók az Azure AD-ben az átadás konfigurálására vonatkozó [engedéllyel](../roles/permissions-reference.md) (pl. alkalmazás-rendszergazda, felhőalkalmazás-rendszergazda, alkalmazástulajdonos vagy globális rendszergazda).
* [Egy 15Five-bérlő](https://www.15five.com/pricing/).
* Rendszergazdai jogosultságokkal rendelkező felhasználói fiók a 15Five-ben.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés Az átadás üzembe helyezésének megtervezése
1. Ismerje meg [az átadási szolgáltatás működését](../app-provisioning/user-provisioning.md).
2. Határozza meg, hogy ki lesz [az átadás hatókörében](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Határozza meg, hogy az [Azure ad és a 15Five között milyen adatleképezést kell leképezni](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-15five-to-support-provisioning-with-azure-ad"></a>2. lépés 15Five konfigurálása az Azure AD-vel való kiépítés támogatásához

Az Azure AD-vel való automatikus 15Five konfigurálása előtt engedélyeznie kell a SCIM-létesítést a 15Five-on.

1. Jelentkezzen be a [15Five felügyeleti konzolra](https://my.15five.com/). Navigáljon a **funkciók > integrációk elemre**.

    :::image type="content" source="media/15five-provisioning-tutorial/integration.png" alt-text="Képernyőkép a 15Five felügyeleti konzolról. Az integrációk a menü funkciók területén jelennek meg, és a funkciók és az integrációk is ki vannak emelve." border="false":::

2.  Kattintson a **SCIM 2,0** elemre.

    :::image type="content" source="media/15five-provisioning-tutorial/image00.png" alt-text="Képernyőkép az integrációk lapról a 15Five felügyeleti konzolon. Az eszköz alatt, a C I M 2,0 van kiemelve." border="false":::

3.  Navigáljon a **scim-integráció > OAuth-token létrehozásához**.

    :::image type="content" source="media/15five-provisioning-tutorial/image02.png" alt-text="Képernyőkép a 15Five felügyeleti konzoljának S C I M integrációs oldaláról. A OAuth jogkivonat előállítása ki van emelve." border="false":::

4.  Másolja a **SCIM 2,0 alap URL-cím** és a **hozzáférési jogkivonat** értékeit. Ez az érték a **bérlői URL-cím** és a **titkos jogkivonat** mezőben jelenik meg a 15Five alkalmazás kiépítés lapján a Azure Portalban.
    
    :::image type="content" source="media/15five-provisioning-tutorial/image03.png" alt-text="Képernyőfelvétel az S C I M integrációs oldaláról. A jogkivonat táblában a S C I M 2,0 alapszintű U R L és a hozzáférési jogkivonat melletti értékek ki vannak emelve." border="false":::

## <a name="step-3-add-15five-from-the-azure-ad-application-gallery"></a>3. lépés 15Five hozzáadása az Azure AD Application Galleryből

Vegyen fel 15Five az Azure AD-alkalmazás-katalógusból a 15Five való kiépítés kezelésének megkezdéséhez. Ha korábban már beállította a 15Five az SSO-hoz, használhatja ugyanazt az alkalmazást. Az integráció első tesztelésekor azonban érdemes létrehozni egy külön alkalmazást. Az alkalmazások katalógusból való hozzáadásáról [itt](../manage-apps/add-application-portal.md) tudhat meg többet. 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Az átadás hatókörében lévő személyek meghatározása 

Az Azure AD átadási szolgáltatása lehetővé teszi az átadott személyek hatókörének meghatározását az alkalmazáshoz való hozzárendelés és/vagy a felhasználó/csoport attribútumai alapján. Ha a hozzárendelés alapján történő hatókör-meghatározást választja, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet felhasználókat és csoportokat az alkalmazáshoz. Ha csak a felhasználó vagy csoport attribútumai alapján történő hatókörmeghatározást választja, az [itt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) leírt hatókörszűrőt használhatja. 

* Felhasználók és csoportok 15Five való hozzárendeléséhez ki kell választania az **alapértelmezett hozzáféréstől** eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva az átadásból, és az átadási naplókban nem jogosultként lesznek megjelölve. Ha az alkalmazáshoz csak az alapértelmezett hozzáférési szerepkör érhető el, akkor további szerepkörök felvételéhez [frissítheti az alkalmazásjegyzéket](../develop/howto-add-app-roles-in-azure-ad-apps.md). 

* Kezdje kicsiben. Tesztelje a felhasználók és csoportok kis halmazát, mielőtt mindenkire kiterjesztené. Amikor az átadás hatóköre a hozzárendelt felhasználókra és csoportokra van beállítva, ennek szabályozásához egy vagy két felhasználót vagy csoportot rendelhet az alkalmazáshoz. Amikor a hatókör az összes felhasználóra és csoportra van beállítva, meghatározhat egy [attribútumalapú hatókörszűrőt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="step-5-configure-automatic-user-provisioning-to-15five"></a>5. lépés Automatikus felhasználó-kiépítés beállítása a 15Five 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy 15Five alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

### <a name="to-configure-automatic-user-provisioning-for-15five-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a 15Five az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **15Five** lehetőséget.

    ![Az 15Five hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a kiépítési mód legördülő listájáról az automatikus lehetőséggel.](common/provisioning-automatic.png)

5.  A rendszergazdai hitelesítő adatok szakaszban adja meg a **SCIM 2,0 alap URL-címét és a hozzáférési jogkivonat** azon értékeit, amelyeket korábban a **bérlői URL-cím** és a **titkos jogkivonat** mezőiben lekért. Kattintson a **kapcsolat tesztelése** lehetőségre, hogy az Azure ad képes legyen csatlakozni a 15Five. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a 15Five-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Bérlői URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítési e-mail** mezőben adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba** esetén.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a 15Five** lehetőséget.

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban található 15Five. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a 15Five felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.


   |Attribútum|Típus|
   |---|---|
   |active|Logikai|
   |cím|Sztring|
   |emails[type eq "work"].value|Sztring|
   |userName (Felhasználónév)|Sztring|
   |name.givenName|Sztring|
   |name.familyName|Sztring|
   |externalId|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: User: Manager|Referencia|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: employeeNumber|Sztring|
   |urn: IETF: params: scim: sémák: kiterjesztés: 15Five: 2.0: felhasználó: hely|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: 15Five: 2.0: felhasználó: startDate|Sztring|

10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása a 15Five** lehetőséget.

11. Tekintse át az Azure AD-ből szinkronizált 15Five az attribútumok **leképezése** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a 15Five tartozó csoportok egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

      |Attribútum|Típus|
      |---|---|
      |externalId|Sztring|
      |displayName|Sztring|
      |tagok|Referencia|

12. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

13. Az Azure AD-kiépítési szolgáltatás 15Five való engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

14. Adja meg a 15Five kiépíteni kívánt felhasználókat és/vagy csoportokat a **Settings (beállítások** ) szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Átadási hatókör](common/provisioning-scope.png)

15. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

    Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és/vagy csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut.

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Az átadás konfigurálása után a következő erőforrásokkal monitorozhatja az üzemelő példányt:

1. Az [átadási naplókkal](../reports-monitoring/concept-provisioning-logs.md) határozhatja meg, hogy mely felhasználók átadása sikeres, és melyeké sikertelen.
2. A [folyamatjelzőn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) láthatja az átadási ciklus állapotát és azt, hogy mennyi hiányzik még a befejeződéséhez.
3. Ha úgy tűnik, hogy az átadási konfiguráció állapota nem megfelelő, az alkalmazás karanténba kerül. A karanténállapotokról [itt](../app-provisioning/application-provisioning-quarantine-status.md) találhat további információt.  
    
## <a name="connector-limitations"></a>Összekötő korlátozásai

* a 15Five nem támogatja a Soft deletes használatát a felhasználók számára.

## <a name="change-log"></a>Változási napló

* 06/16/2020 – a vállalati bővítmény "Manager" attribútumának és egyéni attribútumának "location" és "Start date" támogatása a felhasználók számára.

## <a name="additional-resources"></a>További források

* [A vállalati alkalmazások felhasználói fiókok üzembe](../app-provisioning/configure-automatic-user-provisioning-portal.md)helyezésének kezelése.
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Megtudhatja, hogyan tekintheti át a naplókat, és hogyan kérhet jelentéseket a kiépítési tevékenységekről](../app-provisioning/check-status-user-account-provisioning.md).