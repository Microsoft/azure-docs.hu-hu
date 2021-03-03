---
title: 'Oktatóanyag: Tic-Tac Mobile konfigurálása automatikus felhasználói üzembe helyezéshez Azure Active Directory segítségével | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és felépíteni felhasználói fiókjait az Azure AD-ből Tic-Tac Mobile-ba.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: d0f24e81-fecf-4e71-bd8a-ab911366fdf5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: Zhchia
ms.openlocfilehash: 91ae51b9a2785dbc40c55fa58b26763916e8d16c
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644578"
---
# <a name="tutorial-configure-tic-tac-mobile-for-automatic-user-provisioning"></a>Oktatóanyag: Tic-Tac Mobile konfigurálása automatikus felhasználó-kiépítési szolgáltatáshoz

Ez az oktatóanyag azokat a lépéseket ismerteti, amelyeket a Tic-Tac Mobile és a Azure Active Directory (Azure AD) szolgáltatásban kell elvégezni az automatikus felhasználó-kiépítés konfigurálásához. Ha konfigurálva van, az Azure AD automatikusan kiépíti és kiosztja a felhasználókat és csoportokat a [tic-tac mobilra](https://www.tictacmobile.com/) az Azure ad kiépítési szolgáltatás használatával. A szolgáltatás működésével, működésével és gyakori kérdéseivel kapcsolatos információkért lásd: a felhasználók kiépítésének [automatizálása és az Azure ad-vel való kiépítés a szolgáltatott szoftverek (SaaS) alkalmazásaiba](../app-provisioning/user-provisioning.md).


## <a name="capabilities-supported"></a>Támogatott képességek

> [!div class="checklist"]
> * Felhasználók létrehozása Tic-Tac Mobile-ban.
> * Távolítsa el a felhasználókat a Tic-Tac Mobile-ban, ha már nem igénylik a hozzáférést.
> * A felhasználói attribútumok szinkronizálása az Azure AD és a Tic-Tac Mobile között.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Egy Azure AD-bérlő](../develop/quickstart-create-new-tenant.md).
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](../roles/permissions-reference.md) a kiépítés konfigurálására. Ilyenek például az alkalmazás rendszergazdája, a felhőalapú alkalmazás rendszergazdája, az alkalmazás tulajdonosa vagy a globális rendszergazda.
* A [tic-tac Mobile-](https://www.tictacmobile.com/) fiók Super rendszergazdai szerepkörrel.


## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés Az átadás üzembe helyezésének megtervezése

1. Ismerje meg [az átadási szolgáltatás működését](../app-provisioning/user-provisioning.md).
1. Határozza meg, hogy ki lesz [az átadás hatókörében](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Határozza meg, hogy az [Azure ad és a Tic-Tac Mobile között milyen adatleképezést kell leképezni](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-tic-tac-mobile-to-support-provisioning-with-azure-ad"></a>2. lépés Tic-Tac Mobile konfigurálása az Azure AD-vel való kiépítés támogatásához

Vegye fel a kapcsolatot a support@tictacmobile.com **bérlői URL-cím** és a **titkos jogkivonat** beszerzéséhez. A tokenek fogadásához rendszergazdai szerepkörrel kell rendelkeznie a Tic-Tac Mobile-ban. A jogkivonat a Tic-Tac Mobile-alkalmazás **üzembe** helyezés lapjának **titkos jogkivonat** mezőjébe kerül a Azure Portalban.

## <a name="step-3-add-tic-tac-mobile-from-the-azure-ad-application-gallery"></a>3. lépés Tic-Tac Mobile hozzáadása az Azure AD Application Galleryből

Vegye fel Tic-Tac Mobile alkalmazást az Azure AD-alkalmazás-katalógusból a Tic-Tac Mobile-ba való kiépítés kezelésének megkezdéséhez. Ha korábban már beállította Tic-Tac Mobile-t az egyszeri bejelentkezéshez, használhatja ugyanazt az alkalmazást. Ha először teszteli az integrációt, hozzon létre egy külön alkalmazást. Ha többet szeretne megtudni az alkalmazások katalógusból való hozzáadásáról, tekintse meg az [attribútum-alapú alkalmazások kiépítés hatóköri szűrőkkel](../manage-apps/add-application-portal.md)című témakört.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Az átadás hatókörében lévő személyek meghatározása

Az Azure AD kiépítési szolgáltatásával az alkalmazáshoz való hozzárendelés vagy a felhasználó vagy csoport attribútumai alapján kell kiépíteni a hatókört. Ha úgy dönt, hogy a hatókör ki lesz kiépítve az alkalmazáshoz a hozzárendelés alapján, a felhasználók és csoportok alkalmazáshoz való hozzárendeléséhez kövesse a [felhasználói hozzárendelés kezelése a Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md) alkalmazásban című témakör lépéseit. Ha olyan hatókört választ ki, amely kizárólag a felhasználó vagy csoport attribútumai alapján lesz kiépítve, használjon egy hatókör-szűrőt az [attribútum-alapú alkalmazás kiépítés hatókör-szűrőkkel](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)című cikkben leírtak szerint.

* Amikor felhasználókat és csoportokat rendel Tic-Tac Mobile-hoz, ki kell választania az **alapértelmezett hozzáféréstől** eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól, és a kiépítési naplók nem jogosultak arra, hogy ne legyenek ténylegesen feltüntetve. Ha az alkalmazás egyetlen szerepköre az alapértelmezett hozzáférési szerepkör, akkor a további szerepkörök hozzáadásához [frissítheti az alkalmazás-jegyzékfájlt](../develop/howto-add-app-roles-in-azure-ad-apps.md) .
* Kezdje kicsiben. Próbálja ki a felhasználókat és csoportokat egy kis készlettel, mielőtt mindenki számára elérhetővé vált. Ha a kiépítés hatóköre a hozzárendelt felhasználókhoz és csoportokhoz van beállítva, megtarthatja a vezérlést úgy, hogy egy vagy két felhasználót vagy csoportot rendel az alkalmazáshoz. Ha a hatókör minden felhasználóra és csoportra van beállítva, megadhat egy [attribútum-alapú hatókör-szűrőt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="step-5-configure-automatic-user-provisioning-to-tic-tac-mobile"></a>5. lépés Automatikus felhasználó-kiépítés beállítása Tic-Tac Mobile-ra

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, amelyek segítségével a felhasználók vagy csoportok TestApp hozhatók létre, frissíthetők és letilthatók az Azure AD-ben.

### <a name="configure-automatic-user-provisioning-for-tic-tac-mobile-in-azure-ad"></a>Az Azure AD-ben az Tic-Tac Mobile automatikus felhasználó-kiépítési beállítása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások**  >  **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelt bemutató képernyőkép.](common/enterprise-applications.png)

1. Az alkalmazások listában válassza a **tic-tac Mobile** lehetőséget.

    ![A Tic-Tac Mobile hivatkozást megjelenítő képernyőkép az alkalmazások listájában.](common/all-applications.png)

1. Válassza a **Kiépítés** lapot.

    ![A kiépítési lapot megjelenítő képernyőkép.](common/provisioning.png)

1. A **kiépítési mód** beállítása **automatikusra**.

    ![A kiépítési lap automatikus beállítását bemutató képernyőkép.](common/provisioning-automatic.png)

1. A **rendszergazdai hitelesítő adatok** szakaszban adja meg Tic-Tac Mobile **bérlői URL-címét** és **titkos jogkivonatát**. Válassza a **kapcsolat tesztelése** lehetőséget, hogy az Azure ad képes legyen csatlakozni a Tic-Tac Mobile szolgáltatáshoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Tic-Tac Mobile-fiók rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![A titkos jogkivonat mezőjét megjelenítő képernyőkép.](common/provisioning-testconnection-tenanturltoken.png)

1. Az **értesítési e-mail** mezőbe írja be annak a személynek vagy csoportnak az e-mail-címét, akinek meg kell kapnia a kiépítési hibajelentési értesítéseket. Jelölje be az **e-mail-értesítés küldése hiba** esetén jelölőnégyzetet.

    ![Az értesítő e-mail-címet megjelenítő képernyőkép.](common/provisioning-notification-email.png)

1. Kattintson a **Mentés** gombra.

1. A **leképezések** szakaszban válassza a **szinkronizálás Azure Active Directory a felhasználók Tic-Tac Mobile** lehetőséget.

1. Tekintse át az Azure AD-ból szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban lévő Tic-Tac Mobile értékre. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Tic-Tac Mobile felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. Ha módosítja a [megfeleltetési cél attribútumot](../app-provisioning/customize-application-attributes.md), gondoskodnia kell arról, hogy a Tic-Tac Mobile API támogassa a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   |Attribútum|Típus|
   |---|---|
   |userName (Felhasználónév)|Sztring|
   |name.givenName|Sztring|
   |name.familyName|Sztring|
   |externalId|Sztring|
   |cím|Sztring|
   |emails[type eq "work"].value|Sztring|
   |preferredLanguage|Sztring|
   |externalId|Sztring|
   |userType|Sztring|
   |területi beállítás|Sztring|
   |timezone|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: employeeNumber|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: costCenter|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: szervezet|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: User: Division|Sztring|
   |urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: részleg|Sztring|

1. A hatóköri szűrők konfigurálásához tekintse meg a [hatóköri szűrő oktatóanyagának](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)utasításait.

1. A Tic-Tac Mobile-hoz készült Azure AD-kiépítési szolgáltatás engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![A kiépítési állapotot bemutató képernyőkép a következőn:.](common/provisioning-toggle-on.png)

1. Adja meg azokat a felhasználókat és csoportokat, amelyeket a Tic-Tac Mobile számára szeretne kiépíteni, majd válassza ki a kívánt értékeket a **hatókörben** a **Beállítások** szakaszban.

    ![A kiépítési hatókört bemutató képernyőkép.](common/provisioning-scope.png)

1. Ha készen áll a létesítésre, válassza a **Mentés** lehetőséget.

    ![A kiépítési konfiguráció mentését bemutató képernyőkép.](common/provisioning-configuration-save.png)

Ez a művelet a **Beállítások** szakasz **Hatókör** területén meghatározott összes felhasználó és csoport kezdeti szinkronizálási ciklusát elindítja. A kezdeti ciklus elvégzése hosszabb időt vesz igénybe, mint a későbbi ciklusok, amelyek az Azure AD átadási szolgáltatásának futtatása során körülbelül 40 percenként lesznek végrehajtva.

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése

A kiépítés beállítása után a következő erőforrásokkal figyelheti az üzemelő példányt.

1. A [kiépítési naplók](../reports-monitoring/concept-provisioning-logs.md) segítségével határozza meg, hogy mely felhasználók lettek sikeresen kiépítve vagy sikertelenül.
1. Tekintse meg a [folyamatjelző sáv](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) állapotát a kiépítési ciklus állapotának megtekintéséhez és a befejezéshez.
1. Ha úgy tűnik, hogy az átadási konfiguráció állapota nem megfelelő, az alkalmazás karanténba kerül. További információ a karanténba helyezési [állapotokról: az alkalmazás üzembe helyezése a karanténba helyezett állapotban](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)