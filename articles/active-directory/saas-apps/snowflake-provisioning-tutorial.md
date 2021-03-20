---
title: 'Oktatóanyag: a hópehely konfigurálása automatikus felhasználó-kiépítés Azure Active Directorysal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a Azure Active Directoryt a felhasználói fiókok a hópehely-hoz való automatikus kiépítéséhez és megszüntetéséhez.
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
ms.openlocfilehash: 06f11763498e3e8393d688a71e1c37b466be3f6f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99539535"
---
# <a name="tutorial-configure-snowflake-for-automatic-user-provisioning"></a>Oktatóanyag: a hópehely konfigurálása a felhasználók automatikus kiépítési felállításához

Ez az oktatóanyag bemutatja a hópehely és Azure Active Directory (Azure AD) által végrehajtott lépéseket az Azure AD konfigurálásához, hogy a felhasználók és csoportok automatikusan kiépítsék és felépítsék a [hópelyheket](https://www.Snowflake.com/pricing/). A szolgáltatás működéséről, működéséről és gyakori kérdéseiről a [Mi az az Azure ad-beli automatizált SaaS-alkalmazás-kiépítés](../app-provisioning/user-provisioning.md)című cikk nyújt tájékoztatást. 

> [!NOTE]
> Ez az összekötő jelenleg nyilvános előzetes verzióban érhető el. A használati feltételekkel kapcsolatos további információkért lásd: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a hópehely-ban
> * A hópehely felhasználók eltávolítása, ha nem igényelnek hozzáférést többé
> * Felhasználói attribútumok szinkronizálása az Azure AD és a hópehely között
> * Csoportok és csoporttagságok kiépítése a hópehely-ban
> * [Egyszeri bejelentkezés](./snowflake-tutorial.md) engedélyezése a hópehely-be (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Azure AD-bérlő](../develop/quickstart-create-new-tenant.md)
* Felhasználói fiók az Azure AD-ben [a](../roles/permissions-reference.md) kiépítés konfigurálásához (alkalmazás-rendszergazda, Felhőbeli alkalmazás rendszergazdája, alkalmazás tulajdonosa vagy globális rendszergazda)
* [Egy hópehely-bérlő](https://www.Snowflake.com/pricing/)
* Egy felhasználói fiók a hópehely-ban rendszergazdai engedélyekkel

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés: a kiépítési üzembe helyezés megtervezése
1. Ismerje meg [az átadási szolgáltatás működését](../app-provisioning/user-provisioning.md).
2. Határozza meg, hogy ki lesz [az átadás hatókörében](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Határozza meg, hogy az [Azure ad és a hópehely milyen adatleképezést szeretne leképezni](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-snowflake-to-support-provisioning-with-azure-ad"></a>2. lépés: a hópehely konfigurálása az Azure AD-vel való kiépítés támogatásához

Mielőtt konfigurálja a hópehely-t az Azure AD-vel való automatikus felhasználói üzembe helyezéshez, engedélyeznie kell a rendszer számára a tartományon belüli Identitáskezelés (SCIM) üzembe helyezését a hópehely-on.

1. Jelentkezzen be a hópehely felügyeleti konzolra. Adja meg a következő lekérdezést a Kiemelt munkalapon, majd válassza a **Futtatás** lehetőséget.

    ![Képernyőfelvétel a hópehely felügyeleti konzolról a lekérdezés és Futtatás gombbal.](media/Snowflake-provisioning-tutorial/image00.png)

2.  SCIM hozzáférési token jön létre a hópehely bérlőhöz. A lekéréséhez válassza ki az alábbi képernyőképen látható hivatkozást.

    ![Képernyőkép a hópehely U I-ben található, az S C I M hozzáférési jogkivonattal rendelkező munkalapról.](media/Snowflake-provisioning-tutorial/image01.png)

3. Másolja a generált jogkivonat értékét, és válassza a **kész** lehetőséget. Ez az érték szerepel a **titkos jogkivonat** mezőben a hópehely-alkalmazás **kiépítés** lapján a Azure Portalban.

    ![Képernyőkép a részletekről, amely megjeleníti a szövegmezőbe másolt tokent és a kész lehetőséget.](media/Snowflake-provisioning-tutorial/image02.png)

## <a name="step-3-add-snowflake-from-the-azure-ad-application-gallery"></a>3. lépés: a hópehely hozzáadása az Azure AD Application Galleryből

Adja hozzá a hópehely-t az Azure AD-alkalmazás-katalógusból a hópehely kiépítés kezelésének megkezdéséhez. Ha korábban már beállította a hópehely-t az egyszeri bejelentkezéshez (SSO), ugyanazt az alkalmazást használhatja. Javasoljuk azonban, hogy hozzon létre egy külön alkalmazást, amikor először teszteli az integrációt. [További információ az alkalmazások gyűjteményből való hozzáadásáról](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: annak meghatározása, hogy ki lesz a kiépítés hatóköre 

Az Azure AD kiépítési szolgáltatása lehetővé teszi az alkalmazáshoz való hozzárendelés vagy a felhasználó vagy csoport attribútumai alapján kiépített hatókör kiosztását. Ha úgy dönt, hogy a hatókör ki lesz kiépítve az alkalmazáshoz a hozzárendelés alapján, a lépéseket követve [rendelhet hozzá felhasználókat és csoportokat az alkalmazáshoz](../manage-apps/assign-user-or-group-access-portal.md). Ha olyan hatókört választ, amely kizárólag a felhasználó vagy csoport attribútumai alapján lesz kiépítve, [egy hatókör-szűrőt is használhat](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

Tartsa szem előtt a következő tippeket:

* Amikor felhasználókat és csoportokat rendel a hópehely-hoz, ki kell választania az alapértelmezett hozzáféréstől eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva az átadásból, és az átadási naplókban nem jogosultként lesznek megjelölve. Ha az alkalmazás egyetlen szerepköre az alapértelmezett hozzáférési szerepkör, akkor a további szerepkörök hozzáadásához [frissítheti az alkalmazás-jegyzékfájlt](../develop/howto-add-app-roles-in-azure-ad-apps.md) . 

* Kezdje kicsiben. Tesztelje a felhasználók és csoportok kis halmazát, mielőtt mindenkire kiterjesztené. Ha a kiépítés hatóköre a hozzárendelt felhasználókhoz és csoportokhoz van beállítva, ezt úgy szabályozhatja, hogy egy vagy két felhasználót vagy csoportot rendel az alkalmazáshoz. Ha a hatókör minden felhasználóra és csoportra van beállítva, megadhat egy [attribútum-alapú hatókör-szűrőt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-snowflake"></a>5. lépés: az automatikus felhasználó-kiépítés beállítása a hópehely-ra 

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein a hópehely-felhasználók és-csoportok létrehozásához, frissítéséhez és letiltásához. Az Azure AD-ben beállíthatja a konfigurációt a felhasználók és a csoportok hozzárendelései alapján.

A hópehely automatikus felhasználó általi üzembe helyezésének beállítása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások**  >  **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelt bemutató képernyőkép.](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **hópehely** elemet.

    ![Képernyőkép, amely az alkalmazások listáját jeleníti meg.](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

4. A **kiépítési mód** beállítása **automatikusra**.

    ![Képernyőkép a kiépítési mód legördülő listájáról az automatikus beállítással.](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg azt a scim 2,0 alap URL-címet és hitelesítési tokent, amelyet korábban a **bérlői URL** -cím és a **titkos jogkivonat** mezőiben lekért. 

   Válassza a **kapcsolat tesztelése** lehetőséget, hogy az Azure ad képes legyen a hópehely-kapcsolatra. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a hópehely-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![Képernyőkép, amely megjeleníti a bérlői U R L és a titkos jogkivonat mezőit, valamint a kapcsolat tesztelése gombot.](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítési e-mail** mezőbe írja be annak a személynek vagy csoportnak az e-mail-címét, akinek meg kell kapnia a kiépítési hibajelentési értesítéseket. Ezután jelölje be az **e-mailes értesítés küldése hiba** esetén jelölőnégyzetet.

    ![Képernyőkép az értesítő e-mailek mezőiről.](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a hópehely**-ra lehetőséget.

9. Tekintse át az Azure AD-ból a hópehely-ra szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a hópehely felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   |Attribútum|Típus|
   |---|---|
   |active|Logikai|
   |displayName|Sztring|
   |emails[type eq "work"].value|Sztring|
   |userName (Felhasználónév)|Sztring|
   |name.givenName|Sztring|
   |name.familyName|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: defaultRole|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: defaultWarehouse|Sztring|

10. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása a hópehely-hoz** lehetőséget.

11. Tekintse át az Azure AD-ból a hópehely-ra szinkronizált csoportok attribútumait az **attribútumok leképezése** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a hópehely-beli csoportoknak a frissítési műveletekhez való megfeleltetésére szolgálnak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

    |Attribútum|Típus|
    |---|---|
    |displayName|Sztring|
    |tagok|Referencia|

12. A hatóköri szűrők konfigurálásához tekintse meg a [hatóköri szűrő oktatóanyagának](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)utasításait.

13. Ha engedélyezni szeretné az Azure AD-kiépítési szolgáltatást a hópehely-hez, módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

     ![A kiépítési állapotot bemutató képernyőkép a következőn:.](common/provisioning-toggle-on.png)

14. Adja meg azokat a felhasználókat és csoportokat, amelyeket a hópehely számára szeretne kiépíteni, majd válassza ki a kívánt értékeket a **hatókör** területen a **Beállítások** szakaszban. 

    Ha ez a beállítás nem érhető el, konfigurálja a szükséges mezőket a **rendszergazdai hitelesítő adatok** területen, válassza a **Mentés** lehetőséget, és frissítse a lapot. 

     ![A kiépítési hatókörre vonatkozó választási lehetőségeket bemutató képernyőkép.](common/provisioning-scope.png)

15. Ha készen áll a létesítésre, válassza a **Mentés** lehetőséget.

     ![A kiépítési konfiguráció mentéséhez használt gomb képernyőképe.](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** meghatározott összes felhasználó és csoport kezdeti szinkronizálását. A kezdeti szinkronizálás hosszabb időt vesz igénybe, mint a későbbi szinkronizálások. A következő szinkronizálások 40 percenként történnek, feltéve, hogy az Azure AD kiépítési szolgáltatás fut.

## <a name="step-6-monitor-your-deployment"></a>6. lépés: az üzemelő példány figyelése
A kiépítés beállítása után a következő erőforrásokkal figyelheti az üzemelő példányt:

- A [kiépítési naplók](../reports-monitoring/concept-provisioning-logs.md) segítségével határozza meg, hogy mely felhasználók lettek sikeresen kiépítve vagy sikertelenül.
- Tekintse meg a [folyamatjelző sáv](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) állapotát a kiépítési ciklus állapotának megtekintéséhez és a befejezéshez.
- Ha úgy tűnik, hogy az átadási konfiguráció állapota nem megfelelő, az alkalmazás karanténba kerül. [További információ a karantén állapotáról](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="connector-limitations"></a>Összekötő korlátozásai

A hópehely által generált SCIM tokenek 6 hónapon belül lejárnak. Vegye figyelembe, hogy a lejárat előtt frissítenie kell ezeket a jogkivonatokat, hogy a kiépítési szinkronizálások továbbra is működőképesek legyenek. 

## <a name="troubleshooting-tips"></a>Hibaelhárítási tippek

Az Azure AD-kiépítési szolgáltatás jelenleg bizonyos [IP-címtartományok](../app-provisioning/use-scim-to-provision-users-and-groups.md#ip-ranges)alatt működik. Ha szükséges, korlátozhatja az egyéb IP-tartományokat, és hozzáadhatja ezeket az adott IP-tartományokat az alkalmazás engedélyezési listájához. Ez a módszer lehetővé teszi az Azure AD-kiépítési szolgáltatástól az alkalmazás felé irányuló forgalom áramlását.

## <a name="change-log"></a>Változási napló

* 07/21/2020: az összes felhasználó (az aktív attribútumon keresztül) esetében engedélyezve van a helyreállított törlés.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések
* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)
