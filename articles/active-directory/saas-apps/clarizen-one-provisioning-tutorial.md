---
title: 'Oktatóanyag: a Clarizen konfigurálása az automatikus felhasználó-kiépítés Azure Active Directorysal | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és felépíteni felhasználói fiókjait az Azure AD-ből a Clarizen.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: d8021105-eb5b-4a20-8739-f02e0e22c147
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/01/2020
ms.author: Zhchia
ms.openlocfilehash: f3a19d3c3bf3e4340bb36fd683453541fa15eb6c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101650830"
---
# <a name="tutorial-configure-clarizen-one-for-automatic-user-provisioning"></a>Oktatóanyag: a Clarizen konfigurálása az automatikus felhasználó-kiépítés számára

Ez az oktatóanyag leírja, milyen lépéseket kell végrehajtania a Clarizen egy és Azure Active Directory (Azure AD) az automatikus felhasználó-kiépítés konfigurálásához. Ha be van állítva, az Azure AD automatikusan kiépíti és kiosztja a felhasználókat és csoportokat, hogy [Clarizen](https://www.clarizen.com/) az Azure ad-létesítési szolgáltatás használatával. A szolgáltatás működésével, működésével és gyakori kérdéseivel kapcsolatos információkért lásd: a felhasználók kiépítésének [automatizálása és az Azure ad-vel való kiépítés a szolgáltatott szoftverek (SaaS) alkalmazásaiba](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Támogatott képességek

> [!div class="checklist"]
> * Hozzon létre felhasználókat a Clarizen egyet.
> * Távolítsa el a felhasználókat a Clarizen-ben, ha már nem igénylik a hozzáférést.
> * A felhasználói attribútumok szinkronizálása az Azure AD és a Clarizen között.
> * Csoportok és csoporttagságok kiépítése a Clarizen-ben.
> * Az [egyszeri bejelentkezés (SSO)](./clarizen-tutorial.md) az Clarizen-re ajánlott.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Egy Azure AD-bérlő](../develop/quickstart-create-new-tenant.md).
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](../roles/permissions-reference.md) a kiépítés konfigurálására. Ilyenek például az alkalmazás rendszergazdája, a felhőalapú alkalmazás rendszergazdája, az alkalmazás tulajdonosa vagy a globális rendszergazda.
* A Clarizen egyik felhasználói fiókja az **Integration User** és a **Lite rendszergazdai** [engedélyekkel](https://success.clarizen.com/hc/articles/360011833079-API-Keys-Support)rendelkezik.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés Az átadás üzembe helyezésének megtervezése

1. Ismerje meg [az átadási szolgáltatás működését](../app-provisioning/user-provisioning.md).
1. Határozza meg, hogy ki lesz [az átadás hatókörében](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Határozza meg, hogy az [Azure ad és a Clarizen között milyen adatleképezést kell leképezni](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-clarizen-one-to-support-provisioning-with-azure-ad"></a>2. lépés A Clarizen konfigurálása az Azure AD-vel való kiépítés támogatásához

1. Válassza ki a négy alábbi bérlői URL-cím egyikét a Clarizen egy környezet és egy adatközpont szerint:
      * Az USA termelési adatközpontja: https://servicesapp2.clarizen.com/scim/v2
      * Az EU termelési adatközpontja: https://serviceseu1.clarizen.com/scim/v2
      * Az USA-beli sandbox adatközpontja: https://servicesapp.clarizentb.com/scim/v2
      * Az EU sandbox adatközpontja: https://serviceseu.clarizentb.com/scim/v2

1. API- [kulcs](https://success.clarizen.com/hc/articles/360011833079-API-Keys-Support)létrehozása. Ez az érték jelenik meg a **titkos jogkivonat** mezőben a Clarizen egy alkalmazásának a Azure Portalban való **kiépítés** lapján.

## <a name="step-3-add-clarizen-one-from-the-azure-ad-application-gallery"></a>3. lépés Clarizen hozzáadása az Azure AD Application Galleryből

Adja hozzá a Clarizen egyet az Azure AD-alkalmazás-katalógusból, hogy megkezdje a Clarizen való kiépítés kezelését. Ha korábban már beállította a Clarizen az egyszeri bejelentkezéshez, ugyanazt az alkalmazást használhatja. Ha először teszteli az integrációt, hozzon létre egy külön alkalmazást. Ha többet szeretne megtudni az alkalmazások katalógusból való hozzáadásáról, tekintse meg [az alkalmazás hozzáadása az Azure ad-bérlőhöz](../manage-apps/add-application-portal.md)című témakört.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Az átadás hatókörében lévő személyek meghatározása

Az Azure AD kiépítési szolgáltatásával az alkalmazáshoz való hozzárendelés vagy a felhasználó vagy csoport attribútumai alapján kell kiépíteni a hatókört. Ha úgy dönt, hogy a hatókör ki lesz kiépítve az alkalmazáshoz a hozzárendelés alapján, a felhasználók és csoportok alkalmazáshoz való hozzárendeléséhez kövesse a [felhasználói hozzárendelés kezelése a Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md) alkalmazásban című témakör lépéseit. Ha olyan hatókört választ ki, amely kizárólag a felhasználó vagy csoport attribútumai alapján lesz kiépítve, használjon egy hatókör-szűrőt az [attribútum-alapú alkalmazás kiépítés hatókör-szűrőkkel](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)című cikkben leírtak szerint.

* Amikor felhasználókat és csoportokat rendel hozzá a Clarizen, ki kell választania az **alapértelmezett hozzáféréstől** eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól, és a kiépítési naplók nem jogosultak arra, hogy ne legyenek ténylegesen feltüntetve. Ha az alkalmazás egyetlen szerepköre az alapértelmezett hozzáférési szerepkör, akkor a további szerepkörök hozzáadásához [frissítheti az alkalmazás-jegyzékfájlt](../develop/howto-add-app-roles-in-azure-ad-apps.md) .
* Kezdje kicsiben. Próbálja ki a felhasználókat és csoportokat egy kis készlettel, mielőtt mindenki számára elérhetővé vált. Ha a kiépítés hatóköre a hozzárendelt felhasználókhoz és csoportokhoz van beállítva, megtarthatja a vezérlést úgy, hogy egy vagy két felhasználót vagy csoportot rendel az alkalmazáshoz. Ha a hatókör minden felhasználóra és csoportra van beállítva, megadhat egy [attribútum-alapú hatókör-szűrőt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="step-5-configure-automatic-user-provisioning-to-clarizen-one"></a>5. lépés Automatikus felhasználó-kiépítés beállítása Clarizen

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, amelyek segítségével a felhasználók vagy csoportok TestApp hozhatók létre, frissíthetők és letilthatók az Azure AD-ben.

### <a name="configure-automatic-user-provisioning-for-clarizen-one-in-azure-ad"></a>Automatikus felhasználó-kiépítés konfigurálása az Azure AD-ben az egyik Clarizen

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások**  >  **minden alkalmazás** lehetőséget.

      ![A vállalati alkalmazások panelt bemutató képernyőkép.](common/enterprise-applications.png)

1. Az alkalmazások listában válassza ki a **Clarizen egyet**.

      ![Képernyőkép, amely a Clarizen egy hivatkozását mutatja az alkalmazások listájában.](common/all-applications.png)

1. Válassza a **Kiépítés** lapot.

      ![A kiépítési lapot megjelenítő képernyőkép.](common/provisioning.png)

1. A **kiépítési mód** beállítása **automatikusra**.

      ![A kiépítési lap automatikus beállítását bemutató képernyőkép.](common/provisioning-automatic.png)

1. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a Clarizen egy **bérlői URL-címet** és egy **titkos jogkivonatot**. Válassza a **kapcsolat tesztelése** lehetőséget, hogy az Azure ad képes legyen csatlakozni a Clarizen. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Clarizen egy fiók rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![A titkos jogkivonat mezőjét megjelenítő képernyőkép.](common/provisioning-testconnection-tenanturltoken.png)

1. Az **értesítési e-mail** mezőbe írja be annak a személynek vagy csoportnak az e-mail-címét, akinek meg kell kapnia a kiépítési hibajelentési értesítéseket. Jelölje be az **e-mail-értesítés küldése hiba** esetén jelölőnégyzetet.

    ![Az értesítő e-mail-címet megjelenítő képernyőkép.](common/provisioning-notification-email.png)

1. Kattintson a **Mentés** gombra.

1. A **leképezések** szakaszban válassza a **szinkronizálás Azure Active Directory felhasználók Clarizen egyet**.

1. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban található Clarizen. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Clarizen lévő felhasználói fiókok egyeztetésére szolgálnak a frissítési műveletekhez. Ha módosítja a [megfeleltetési attribútumot](../app-provisioning/customize-application-attributes.md), meg kell győződnie arról, hogy az CLARIZEN egy API támogatja a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   |Attribútum|Típus|
   |---|---|
   |userName (Felhasználónév)|Sztring|
   |displayName|Sztring|
   |active|Logikai|
   |cím|Sztring|
   |emails[type eq "work"].value|Sztring|
   |e-mailek [type EQ "Home"]. Value|Sztring|
   |e-mailek [type EQ "other"]. Value|Sztring|
   |preferredLanguage|Sztring|
   |name.givenName|Sztring|
   |name.familyName|Sztring|
   |név. formázott|Sztring|
   |name.honorificPrefix|Sztring|
   |név. honorificSuffix|Sztring|
   |címek [type EQ "other"]. formázott|Sztring|
   |címek [type EQ "work"]. formázott|Sztring|
   |címek [type EQ "work"]. Country|Sztring|
   |címek [típus EQ "work"]. régió|Sztring|
   |címek [típus EQ "work"]. helység|Sztring|
   |címek [type EQ "work"]. irányítószám|Sztring|
   |címek [type EQ "work"]. streetAddress|Sztring|
   |phoneNumbers[type eq "work"].value|Sztring|
   |phoneNumbers[type eq "mobile"].value|Sztring|
   |phoneNumbers [type EQ "fax"]. Value|Sztring|
   |phoneNumbers [type EQ "Home"]. Value|Sztring|
   |phoneNumbers [type EQ "other"]. Value|Sztring|
   |phoneNumbers [type EQ "pager"]. Value|Sztring|
   |externalId|Sztring|
   |nickName|Sztring|
   |területi beállítás|Sztring|
   |szerepkörök [elsődleges EQ "true". type]|Sztring|
   |szerepkörök [elsődleges EQ "true". Value]|Sztring|
   |timezone|Sztring|
   |userType|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: részleg|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: employeeNumber|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: User: Manager|Referencia|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: costCenter|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: User: Division|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: User: Division|Sztring|

1. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása lehetőséget, hogy Clarizen egyet**.

1. Tekintse át az Azure AD-ból szinkronizált Clarizen, amely az **attribútum-hozzárendelési** szakaszban található. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Clarizen egyikében a frissítési műveletekhez használatosak. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

      |Attribútum|Típus|
      |---|---|
      |displayName|Sztring|
      |externalId|Sztring|
      |tagok|Referencia|

1. A hatóköri szűrők konfigurálásához tekintse meg a  [hatóköri szűrő oktatóanyagának](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)utasításait.

1. Ha **engedélyezni szeretné az** Azure ad kiépítési szolgáltatást a Clarizen, módosítsa a **kiépítési állapotot** a **Beállítások** szakaszban.

      ![A kiépítési állapotot bemutató képernyőkép a következőn:.](common/provisioning-toggle-on.png)

1. Adja meg a Clarizen kiépíteni kívánt felhasználókat vagy csoportokat, majd a **Beállítások** szakaszban válassza ki a kívánt értékeket a **hatókörben** .

      ![A kiépítési hatókört bemutató képernyőkép.](common/provisioning-scope.png)

1. Ha készen áll a létesítésre, válassza a **Mentés** lehetőséget.

      ![A kiépítési konfiguráció mentését bemutató képernyőkép.](common/provisioning-configuration-save.png)

Ez a művelet a **Beállítások** szakasz **Hatókör** területén meghatározott összes felhasználó és csoport kezdeti szinkronizálási ciklusát elindítja. A kezdeti ciklus elvégzése hosszabb időt vesz igénybe, mint a későbbi ciklusok, amelyek az Azure AD átadási szolgáltatásának futtatása során körülbelül 40 percenként lesznek végrehajtva.

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése

A kiépítés beállítása után a következő erőforrásokkal figyelheti az üzemelő példányt.

1. A [kiépítési naplók](../reports-monitoring/concept-provisioning-logs.md) segítségével határozza meg, hogy mely felhasználók lettek sikeresen kiépítve vagy sikertelenül.
1. Tekintse meg a [folyamatjelző sáv](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) állapotát a kiépítési ciklus állapotának megtekintéséhez és a befejezéshez.
1. Ha úgy tűnik, hogy az átadási konfiguráció állapota nem megfelelő, az alkalmazás karanténba kerül. További információ a karanténba helyezési [állapotokról: az alkalmazás üzembe helyezése a karanténba helyezett állapotban](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="troubleshooting-tips"></a>Hibaelhárítási tippek

Ha a Clarizen egy gyűjtemény-alkalmazáshoz rendel hozzá egy felhasználót, válassza ki a csak a **felhasználói** szerepkört. A következő szerepkörök érvénytelenek:

* Adminisztrátor (rendszergazda)
* E-mail jelentéskészítési felhasználó
* Külső felhasználó
* Pénzügyi felhasználó
* Közösségi felhasználó
* Érvényes felügyelői
* Az idő & a költségek felhasználója

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)