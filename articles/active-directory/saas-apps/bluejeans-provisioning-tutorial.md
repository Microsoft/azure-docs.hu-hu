---
title: 'Oktatóanyag: a BlueJeans konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt, hogy automatikusan kiépítse és kiépítse a felhasználói fiókokat a BlueJeans.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 58cd69ebe97d9d0965d7e648b0ded012ac71cd0d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101646126"
---
# <a name="tutorial-configure-bluejeans-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés BlueJeans konfigurálása

Ez az oktatóanyag azokat a lépéseket ismerteti, amelyeket a BlueJeans és a Azure Active Directory (Azure AD) szolgáltatásban kell végrehajtania az automatikus felhasználó-kiépítés konfigurálásához. Ha konfigurálva van, az Azure AD automatikusan kiépíti és kiosztja a felhasználókat a [BlueJeans](https://www.bluejeans.com/pricing) az Azure ad kiépítési szolgáltatás használatával. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md). 

## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a BlueJeans-ben
> * Felhasználók eltávolítása a BlueJeans-ben, ha már nincs szükség hozzáférésre
> * Felhasználói attribútumok szinkronizálása az Azure AD és a BlueJeans között
> * [Egyszeri bejelentkezés](./bluejeans-tutorial.md) a BlueJeans-be (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Egy Azure AD-bérlő](../develop/quickstart-create-new-tenant.md).
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](../roles/permissions-reference.md) a kiépítés konfigurálására (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda, alkalmazás tulajdonosa vagy globális rendszergazda). 
* BlueJeans-bérlő a [céges](https://www.bluejeans.com/pricing) csomaggal vagy a jobb engedélyezéssel.
* Rendszergazdai jogosultságokkal rendelkező felhasználói fiók a BlueJeans-ben.
* A SCIM kiépítés engedélyezve a BlueJeans Enterprise-ban.

> [!NOTE]
> Az Azure AD-kiépítés integrációja a [BLUEJEANS API](https://BlueJeans.github.io/developer)-ra támaszkodik, amely a standard csomagon vagy annál jobb BlueJeans csapatoknak érhető el.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés Az átadás üzembe helyezésének megtervezése
1. Ismerje meg [az átadási szolgáltatás működését](../app-provisioning/user-provisioning.md).
2. Határozza meg, hogy ki lesz [az átadás hatókörében](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Határozza meg, hogy az [Azure ad és a BlueJeans között milyen adatleképezést kell leképezni](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-bluejeans-to-support-provisioning-with-azure-ad"></a>2. lépés BlueJeans konfigurálása az Azure AD-vel való kiépítés támogatásához

1. Jelentkezzen be a BlueJeans felügyeleti konzolba. Navigáljon a csoport beállításai > Biztonság elemre.
2. Válassza **az egyszeri bejelentkezés** lehetőséget, és **konfigurálja most**.

    ![most](./media/bluejeans-provisioning-tutorial/configure.png)

3. A **& integrációjának kiépítése** ablakban válassza a **felhasználói fiókok létrehozása és kezelése a identitásszolgáltató** -on keresztül lehetőséget, és kattintson a **Jogkivonat létrehozása** lehetőségre.

    ![létrehozás](./media/bluejeans-provisioning-tutorial/token.png)
    
4. Másolja ki és mentse a jogkivonatot. 
5. A BlueJeans bérlői URL-címe: `https://api.bluejeans.com/v2/scim` . Az előző lépésben a **bérlői URL-címet** és a **titkos jogkivonatot** a BlueJeans alkalmazás üzembe helyezés lapján fogja megadni a Azure Portal.

## <a name="step-3-add-bluejeans-from-the-azure-ad-application-gallery"></a>3. lépés BlueJeans hozzáadása az Azure AD Application Galleryből

Vegyen fel BlueJeans az Azure AD-alkalmazás-katalógusból a BlueJeans való kiépítés kezelésének megkezdéséhez. Ha korábban már beállította a BlueJeans az SSO-hoz, használhatja ugyanazt az alkalmazást. Az integráció első tesztelésekor azonban érdemes létrehozni egy külön alkalmazást. Az alkalmazások katalógusból való hozzáadásáról [itt](../manage-apps/add-application-portal.md) tudhat meg többet.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Az átadás hatókörében lévő személyek meghatározása 

Az Azure AD kiépítési szolgáltatása lehetővé teszi az alkalmazáshoz való hozzárendelés és a felhasználó attribútumai alapján kiépített hatókör kiosztását. Ha úgy dönt, hogy a hatókör ki lesz kiépítve az alkalmazáshoz a hozzárendelés alapján, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet felhasználókat az alkalmazáshoz. Ha olyan hatókört választ ki, amely kizárólag a felhasználó attribútumai alapján lesz kiépítve, az [itt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)leírtak szerint használhat egy hatókör-szűrőt. 

* Amikor felhasználókat rendel a BlueJeans, ki kell választania az **alapértelmezett hozzáféréstől** eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva az átadásból, és az átadási naplókban nem jogosultként lesznek megjelölve. Ha az alkalmazáshoz csak az alapértelmezett hozzáférési szerepkör érhető el, akkor további szerepkörök felvételéhez [frissítheti az alkalmazásjegyzéket](../develop/howto-add-app-roles-in-azure-ad-apps.md). 

* Kezdje kicsiben. Tesztelje a felhasználókat egy kis készlettel, mielőtt mindenki számára elérhetővé tenné. Ha a kiépítés hatóköre a hozzárendelt felhasználók értékre van állítva, akkor ezt úgy szabályozhatja, hogy hozzárendel egy vagy két felhasználót az alkalmazáshoz. Ha a hatókör minden felhasználóra van beállítva, megadhat egy [attribútum-alapú hatóköri szűrőt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

## <a name="step-5-configure-automatic-user-provisioning-to-bluejeans"></a>5. lépés Automatikus felhasználó-kiépítés beállítása a BlueJeans

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói hozzárendelések alapján hozza létre, frissítse és tiltsa le a TestApp felhasználókat.

### <a name="to-configure-automatic-user-provisioning-for-bluejeans-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a BlueJeans az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **BlueJeans** lehetőséget.

    ![Az BlueJeans hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Kiépítés lap automatikus](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a BlueJeans bérlői URL-címét és a titkos tokent a 2. lépésben. Kattintson a **kapcsolat tesztelése** lehetőségre, hogy az Azure ad képes legyen csatlakozni a BlueJeans. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a BlueJeans-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Jogkivonat](common/provisioning-testconnection-tenanturltoken.png)


6. Az **értesítési e-mail** mezőben adja meg egy olyan személy e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, és jelölje be a jelölőnégyzetet – **e-mail-értesítés küldése hiba** esetén.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a BlueJeans** lehetőséget.

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban található BlueJeans. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a BlueJeans felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. Ha úgy dönt, hogy módosítja a [megfelelő cél attribútumot](../app-provisioning/customize-application-attributes.md), akkor biztosítania kell, hogy a BlueJeans API támogassa a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

|Attribútum|Típus|Szűréshez támogatott|
|---|---|---|
|userName (Felhasználónév)|Sztring|&check;|
|active|Logikai|
|cím|Sztring|
|emails[type eq "work"].value|Sztring|
|name.givenName|Sztring|
|name.familyName|Sztring|
|phoneNumbers[type eq "work"].value|Sztring|
|urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: User: Manager|Sztring|

10. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

11. Az Azure AD-kiépítési szolgáltatás BlueJeans való engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

12. Adja meg a BlueJeans kiépíteni kívánt felhasználókat a **Settings (beállítások** ) szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Átadási hatókör](common/provisioning-scope.png)

13. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakaszban a **hatókörben** definiált összes felhasználó kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut. 

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Az átadás konfigurálása után a következő erőforrásokkal monitorozhatja az üzemelő példányt:

1. Az [átadási naplókkal](../reports-monitoring/concept-provisioning-logs.md) határozhatja meg, hogy mely felhasználók átadása sikeres, és melyeké sikertelen.
2. A [folyamatjelzőn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) láthatja az átadási ciklus állapotát és azt, hogy mennyi hiányzik még a befejeződéséhez.
3. Ha úgy tűnik, hogy az átadási konfiguráció állapota nem megfelelő, az alkalmazás karanténba kerül. A karanténállapotokról [itt](../app-provisioning/application-provisioning-quarantine-status.md) találhat további információt.  

## <a name="connector-limitations"></a>Összekötő korlátozásai

* A Bluejeans nem engedélyezi a 30 karakternél hosszabb felhasználóneveket.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)