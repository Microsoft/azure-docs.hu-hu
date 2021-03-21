---
title: 'Oktatóanyag: az automatikus felhasználó-kiépítés megadásának beállítása a Azure Active Directory'
description: Ismerje meg, hogyan lehet automatikusan kiépíteni és felépíteni a felhasználói fiókokat Azure Active Directory (Azure AD)-ról a tartalomra.
services: active-directory
documentationcenter: ''
author: zchia
manager: beatrizd
ms.assetid: 3b761984-a9a0-4519-b23e-563438978de5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2020
ms.author: zhchia
ms.openlocfilehash: c9d19624d90b1228b2a44caeff7d103af3172ed9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97516333"
---
# <a name="tutorial-configure-contentful-for-automatic-user-provisioning"></a>Oktatóanyag: a felhasználók automatikus kiépítési felállításához szükséges tartalom konfigurálása

Ez a cikk az automatikus felhasználó-kiépítés konfigurálásához szükséges lépéseket ismerteti, és Azure Active Directory (Azure AD). Ha konfigurálva van, az Azure AD automatikusan kiépíti és kiosztja a felhasználókat és a csoportokat az Azure [ad-](https://www.contentful.com/) kiépítési szolgáltatás használatával. A szolgáltatás működéséről és működéséről, valamint a gyakori kérdésekről a következő témakörben talál további információt: a felhasználók kiépítésének [automatizálása és az SaaS-alkalmazások kiépítése a Azure Active Directory használatával](../app-provisioning/user-provisioning.md). 

## <a name="capabilities-supported"></a>Támogatott képességek

> [!div class="checklist"]
> * Felhasználók létrehozása tartalommal
> * Távolítsa el a felhasználókat a tartalomból, ha már nem igénylik a hozzáférést
> * A felhasználói attribútumok szinkronizálása az Azure AD és a tartalmas szolgáltatás között
> * Csoportok és csoporttagságok kiépítése a tartalomból
> * [Egyszeri bejelentkezés](contentful-tutorial.md) a tartalomra (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Egy Azure AD-bérlő](../develop/quickstart-create-new-tenant.md). 
* Egy Azure AD-beli felhasználói fiók, amely [jogosult a](../roles/permissions-reference.md) kiépítés konfigurálására (például alkalmazás-rendszergazda, felhőalapú alkalmazás rendszergazdája, alkalmazás tulajdonosa vagy globális rendszergazda). 
* Egy, a tartományok közötti Identitáskezelés (SCIM) kiépítését támogató előfizetéssel rendelkező, tartalommal rendelkező szervezeti fiók. Ha kérdése van a szervezet előfizetésével kapcsolatban, forduljon a [tartalom-támogatási szolgálathoz](mailto:support@contentful.com).
 
## <a name="plan-your-provisioning-deployment"></a>Az átadás üzembe helyezésének megtervezése

1. Ismerje meg [az átadási szolgáltatás működését](../app-provisioning/user-provisioning.md).
2. Határozza meg, hogy ki lesz [az átadás hatókörében](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Határozza meg, hogy milyen adatokat kell [leképezni az Azure ad és a tartalommal](../app-provisioning/customize-application-attributes.md). 

## <a name="configure-contentful-to-support-provisioning-with-azure-ad"></a>A tartalom konfigurálása az Azure AD-vel való kiépítés támogatásához

1. A tartalommal rendelkező alkalmazásban hozzon létre egy **szolgáltatás-felhasználói** fiókot. Az Azure-hoz tartozó összes kiépítési engedély ezen a fiókon keresztül érhető el. Azt javasoljuk, hogy ehhez a fiókhoz válassza a **tulajdonos** lehetőséget a szervezeti szerepkörként.

2. Jelentkezzen be a **szolgáltatás felhasználója** számára.

3. A bal oldali menüben válassza a **szervezeti beállítások**  >  **hozzáférési eszközök**  >  **felhasználó kiépítése** lehetőséget.

   ![Képernyőfelvétel a szervezeti beállítások menüjéről, a felhasználók kiépítése a hozzáférési eszközök területen.](media/contentful-provisioning-tutorial/access.png)

4. Másolja és mentse a **scim URL-címét**. Ezt az értéket meg kell adnia a Azure Portal a tartalommal rendelkező alkalmazás **létesítés** lapján.

5. Válassza a **személyes hozzáférési jogkivonat előállítása** lehetőséget.

    ![url](media/contentful-provisioning-tutorial/generate.png)

6. A modális ablakban adja meg a személyes hozzáférési jogkivonat nevét, majd válassza a **Létrehozás** lehetőséget.

7. A rendszer létrehoz egy SCIM URL-címet és a titkos jogkivonatot. Másolja és mentse ezeket az értékeket. Ezeket az értékeket a Azure Portalban lévő, a tartalommal rendelkező alkalmazás **üzembe** helyezés lapján adhatja meg.

    ![Képernyőfelvétel a személyes hozzáférési jogkivonat panelről, C F P A T és a token helyőrző neve kiemelve.](media/contentful-provisioning-tutorial/token.png)


Ha kérdése van, miközben a kiépítés konfigurálását végzi a megtekintő felügyeleti konzolon, forduljon a [tartalom-támogatási szolgálathoz](mailto:support@contentful.com).

## <a name="add-contentful-from-the-azure-ad-application-gallery"></a>Tartalom hozzáadása az Azure AD Application Galleryből

A kiépítés a tartalomból való kezeléséhez adja hozzá a tartalmat az Azure AD Application Galleryből. Ha korábban már beállította az egyszeri bejelentkezéshez szükséges tartalmat, ugyanazt az alkalmazást használhatja. Javasoljuk azonban, hogy hozzon létre egy külön alkalmazást, hogy először tesztelje az integrációt. Megtudhatja, hogyan [adhat hozzá egy alkalmazást a](../manage-apps/add-application-portal.md)katalógusban. 

## <a name="define-who-will-be-in-scope-for-provisioning"></a>Az átadás hatókörében lévő személyek meghatározása 

Az Azure AD-kiépítési szolgáltatás az alkalmazáshoz való hozzárendelés vagy a felhasználó vagy csoport attribútumai alapján kiépített hatókörre használható. 

Ha úgy dönt, hogy a hatókör ki lesz kiépítve az alkalmazáshoz a hozzárendelés alapján, hajtsa végre a [felhasználók és csoportok alkalmazáshoz rendelésének](../manage-apps/assign-user-or-group-access-portal.md)lépéseit.

Ha olyan hatókört választ ki, amely kizárólag a felhasználó vagy csoport attribútumai alapján lesz kiépítve, egy hatókör-szűrő használatával [határozhatja meg a felhasználói fiókok üzembe helyezésének feltételes szabályait](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* Amikor felhasználókat és csoportokat rendel hozzá a tartalomhoz, az **alapértelmezett hozzáféréstől** eltérő szerepkört kell kijelölnie. Azok a felhasználók, akik az alapértelmezett hozzáférési szerepkörrel rendelkeznek, ki vannak zárva a kiépítés alól, és a kiépítési naplókban nem jogosultak rá. Ha az alkalmazás egyetlen szerepköre az alapértelmezett hozzáférési szerepkör, akkor a további szerepkörök hozzáadásához [frissítheti az alkalmazás-jegyzékfájlt](../develop/howto-add-app-roles-in-azure-ad-apps.md) . 
* Kezdje kicsiben. Próbálja ki a felhasználókat és csoportokat egy kis készlettel, mielőtt mindenki számára elérhetővé vált. Ha a kiépítési hatókör a hozzárendelt felhasználókhoz és csoportokhoz van beállítva, akkor a hatókört úgy szabályozhatja, hogy egy vagy két felhasználót vagy csoportot rendel hozzá az alkalmazáshoz. Ha a hatókör minden felhasználóra és csoportra van beállítva, megadhat egy [attribútum-alapú hatókör-szűrőt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

## <a name="configure-automatic-user-provisioning-to-contentful"></a>Automatikus felhasználó-kiépítés beállítása a tartalomhoz 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás beállításának lépésein, hogy az Azure AD-ben felhasználói vagy csoport-hozzárendelések alapján hozzon létre, frissítsen és tiltsa le a felhasználók és csoportok egy tesztelési alkalmazásban.

### <a name="configure-automatic-user-provisioning-for-contentful-in-azure-ad"></a>Automatikus felhasználó-kiépítés beállítása az Azure AD-ben elérhető tartalomhoz

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások** lehetőséget, majd válassza **a minden alkalmazás** lehetőséget.

   ![Képernyőfelvétel a Azure Portal vállalati alkalmazások menüjéről, minden alkalmazás kiemelve.](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **tartalom** elemet.

   ![Képernyőfelvétel: az alkalmazások listájában az első 20 találat jelenik meg.](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

   ![Képernyőkép a kiépítés lapról a bal oldali menü kezelés szakaszában.](common/provisioning.png)

4. A **kiépítési mód** beállítása **automatikusra**.

   ![A kiépítési mód beállításait megjelenítő képernyőkép, automatikus kiemelve.](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a tartalommal rendelkező bérlői URL-címet és a titkos jogkivonatot. Ha biztosítani szeretné, hogy az Azure AD képes legyen a tartalomhoz csatlakozni, válassza a **kapcsolat tesztelése** lehetőséget. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a tartalomszolgáltató fiók rendelkezik rendszergazdai engedélyekkel, majd próbálkozzon újra.

   ![Képernyőkép, amely megjeleníti a bérlő U R L és a titkos token szövegmezőket, a kapcsolat tesztelése gomb kiemelve.](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítő e-mailben** adja meg annak a személynek vagy csoportnak az e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket, majd jelölje be az **e-mail-értesítés küldése hiba** esetén jelölőnégyzetet.

   ![Az értesítő e-mail-szövegmezőt megjelenítő képernyőkép.](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a tartalomhoz** lehetőséget.

9. Az **attribútum-leképezés** szakaszban tekintse át az Azure ad-ből a tartalommal szinkronizált felhasználói attribútumokat. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a frissítési műveletekhez használt felhasználói fiókok egyeztetésére szolgálnak. Ha úgy dönt, hogy megváltoztatja a [megfeleltetési cél attribútumot](../app-provisioning/customize-application-attributes.md), akkor biztosítania kell, hogy a tartalommal rendelkező API támogassa a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   |Attribútum|Típus|Szűréshez támogatott|
   |---|---|---|
   |userName (Felhasználónév)|Sztring|&check;|
   |name.givenName|Sztring|
   |name.familyName|Sztring|

10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása a tartalommal** lehetőséget.

11. Az **attribútum-leképezés** szakaszban tekintse át az Azure ad-ből a tartalommal szinkronizált csoportok attribútumait. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a frissítési műveletekhez tartozó csoportok egyeztetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    |Attribútum|Típus|Szűréshez támogatott|
    |---|---|---|
    |displayName|Sztring|&check;|
    |tagok|Referencia|

12. A hatóköri szűrők beállításához hajtsa végre a [hatókör-szűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)ismertetett lépéseket.

13. Ha szeretné engedélyezni az Azure AD kiépítési szolgáltatást a tartalomhoz, a beállítások szakaszban a **kiépítési állapot** **beállításnál** válassza **a** be lehetőséget.

    ![Képernyőfelvétel: a kiépítési állapot be-és kikapcsolása váltógomb.](common/provisioning-toggle-on.png)

14. A tartalomra kiépíteni kívánt felhasználók vagy csoportok definiálásához a **Beállítások** szakaszban a **hatókör** területen válassza a megfelelő lehetőséget.

    ![A hatókör ablaktáblán kiválasztható beállításokat bemutató képernyőkép.](common/provisioning-scope.png)

15. Ha készen áll a létesítésre, válassza a **Mentés** lehetőséget.

    ![A Save (Mentés) gombot és a Cancel (Mégse) gombot megjelenítő képernyőkép.](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** területen a **hatókörben** definiált összes felhasználó és csoport kezdeti szinkronizálási ciklusát. A kezdeti ciklus elvégzése hosszabb időt vesz igénybe, mint a későbbi ciklusok, amelyek az Azure AD átadási szolgáltatásának futtatása során körülbelül 40 percenként lesznek végrehajtva. 

## <a name="monitor-your-deployment"></a>Az üzemelő példány figyelése

A kiépítés beállítása után a következő erőforrásokkal figyelheti az üzemelő példányt:

* Annak megállapításához, hogy mely felhasználók lettek sikeresen kiépítve vagy sikertelenül, tekintse meg a [kiépítési naplókat](../reports-monitoring/concept-provisioning-logs.md).
* Ha meg szeretné tekinteni a kiépítési ciklus állapotát, és azt, hogy a Befejezés hogyan zárul be, ellenőrizze a [folyamatjelző sávot](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md).
* Ha úgy tűnik, hogy a kiépítési konfiguráció sérült állapotban van, az alkalmazás Karanténba kerül. További információ a [karantén állapotáról](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)
* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
