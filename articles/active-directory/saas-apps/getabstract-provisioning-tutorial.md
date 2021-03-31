---
title: 'Oktatóanyag: a getAbstract konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és felépíteni a felhasználói fiókokat a Azure Active Directoryból a getAbstract.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: bd8898f9-7a01-4e85-9dd4-61ae4b01ab5b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2021
ms.author: Zhchia
ms.openlocfilehash: 1d1b2417750b917f5b09bb53ee980887218a785c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102616128"
---
# <a name="tutorial-configure-getabstract-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés getAbstract konfigurálása

Ez az oktatóanyag azokat a lépéseket ismerteti, amelyeket a getAbstract és a Azure Active Directory (Azure AD) szolgáltatásban kell végrehajtania az automatikus felhasználó-kiépítés konfigurálásához. Ha konfigurálva van, az Azure AD automatikusan kiépíti és kiosztja a felhasználókat és csoportokat a [getAbstract](https://www.getabstract.com) az Azure ad kiépítési szolgáltatásával. A szolgáltatás működésének, működésének és gyakori kérdéseinek részletes ismertetését lásd: a felhasználók kiépítésének [automatizálása és az Azure ad-vel való kiépítés a szolgáltatott szoftverek (SaaS) alkalmazásaiba](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Támogatott képességek

> [!div class="checklist"]
> * Felhasználók létrehozása a getAbstract-ben.
> * Távolítsa el a felhasználókat a getAbstract, amikor már nincs szükség hozzáférésre.
> * A felhasználói attribútumok szinkronizálása az Azure AD és a getAbstract között.
> * Csoportok és csoporttagságok kiépítése a getAbstract-ben.
> * Engedélyezze az [egyszeri bejelentkezést (SSO)](getabstract-tutorial.md) a getAbstract (ajánlott).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Egy Azure AD-bérlő](../develop/quickstart-create-new-tenant.md).
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](../roles/permissions-reference.md) a kiépítés konfigurálására. Ilyenek például az alkalmazás rendszergazdája, a felhőalapú alkalmazás rendszergazdája, az alkalmazás tulajdonosa vagy a globális rendszergazda.
* Egy getAbstract-bérlő (getAbstract vállalati licenc).
* Egyszeri bejelentkezés engedélyezve az Azure AD-bérlőn és a getAbstract-bérlőn.
* Jóváhagyás és rendszer a tartományok közötti Identitáskezelés (SCIM) számára, amely lehetővé teszi a getAbstract. (E-mail küldése a b2b.itsupport@getabstract.com következőnek:.)

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés Az átadás üzembe helyezésének megtervezése

1. Ismerje meg [az átadási szolgáltatás működését](../app-provisioning/user-provisioning.md).
1. Határozza meg, hogy ki lesz [az átadás hatókörében](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Határozza meg, hogy az [Azure ad és a getAbstract között milyen adatleképezést kell leképezni](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-getabstract-to-support-provisioning-with-azure-ad"></a>2. lépés GetAbstract konfigurálása az Azure AD-vel való kiépítés támogatásához

1. Jelentkezzen be a getAbstract.
1. Válassza a jobb felső sarokban található beállítások ikont, és válassza a **saját központi rendszergazda** lehetőséget.

    ![Képernyőkép a getAbstract, amely a központi rendszergazdát mutatja.](media/getabstract-provisioning-tutorial/my-account.png)

1. Keresse meg és válassza ki a **scim felügyeleti** lehetőséget.

    ![A getAbstract SCIM-rendszergazdáját bemutató képernyőkép.](media/getabstract-provisioning-tutorial/scim-admin.png)

1. Válassza az **Indítás** lehetőséget.

    ![Képernyőkép, amely a getAbstract SCIM ügyfél-azonosítót mutatja.](media/getabstract-provisioning-tutorial/scim-client-go.png)

1. Válassza az **új jogkivonat előállítása** lehetőséget.

    ![Képernyőkép, amely az 1. getAbstract SCIM tokent jeleníti meg.](media/getabstract-provisioning-tutorial/scim-generate-token-step-2.png)

1. Ha biztos benne, válassza az **új jogkivonat létrehozása** lehetőséget. Ellenkező esetben válassza a **Mégse** lehetőséget.

    ![Képernyőfelvétel: a getAbstract SCIM token 2.](media/getabstract-provisioning-tutorial/scim-generate-token-step-1.png)

1. Válassza ki a másolás a vágólapra ikont, vagy válassza ki a teljes tokent, és másolja ki. Jegyezze fel azt is, hogy a bérlő/alap URL-cím a `https://www.getabstract.com/api/scim/v2` . Ezek az értékek a getAbstract alkalmazás **üzembe** helyezés lapjának **titkos jogkivonat** és **bérlői URL-címe** mezőiben jelennek meg a Azure Portal.

    ![Képernyőkép a getAbstract SCIM token 3.](media/getabstract-provisioning-tutorial/scim-generate-token-step-3.png)

## <a name="step-3-add-getabstract-from-the-azure-ad-application-gallery"></a>3. lépés GetAbstract hozzáadása az Azure AD Application Galleryből

Vegyen fel getAbstract az Azure AD-alkalmazás-katalógusból a getAbstract való kiépítés kezelésének megkezdéséhez. Ha korábban már beállította a getAbstract-t az egyszeri bejelentkezéshez, használhatja ugyanazt az alkalmazást. Javasoljuk, hogy hozzon létre egy külön alkalmazást, amikor először teszteli az integrációt. Ha többet szeretne megtudni arról, hogyan adhat hozzá egy alkalmazást a katalógusból, tekintse meg [ezt a](../manage-apps/add-application-portal.md)rövid útmutatót.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Az átadás hatókörében lévő személyek meghatározása

Az Azure AD-kiépítési szolgáltatás az alkalmazáshoz való hozzárendelés vagy a felhasználó vagy csoport attribútumai alapján kiépített hatókörre használható. Ha a hozzárendelés alapján történő hatókör-meghatározást választja, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet felhasználókat és csoportokat az alkalmazáshoz. Ha olyan hatókört választ ki, amely kizárólag a felhasználó vagy csoport attribútumai alapján lesz kiépítve, az alkalmazások kiosztási [szűrőkkel](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)való kiosztása című témakörben leírtak szerint egy hatókör-szűrőt is használhat.

* Amikor felhasználókat és csoportokat rendel a getAbstract-hez, ki kell választania az **alapértelmezett hozzáféréstől** eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól, és a kiépítési naplók nem jogosultak arra, hogy ne legyenek ténylegesen feltüntetve. Ha az alkalmazás egyetlen szerepköre az alapértelmezett hozzáférési szerepkör, akkor a további szerepkörök hozzáadásához [frissítheti az alkalmazás-jegyzékfájlt](../develop/howto-add-app-roles-in-azure-ad-apps.md) .

* Kezdje kicsiben. Próbálja ki a felhasználókat és csoportokat egy kis készlettel, mielőtt mindenki számára elérhetővé vált. Ha a kiépítés hatóköre hozzárendelt felhasználókhoz és csoportokhoz van beállítva, ezt a beállítást egy vagy két felhasználó vagy csoport hozzárendelésével szabályozhatja az alkalmazáshoz. Ha a hatókör minden felhasználóra és csoportra van beállítva, megadhat egy [attribútum-alapú hatókör-szűrőt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="step-5-configure-automatic-user-provisioning-to-getabstract"></a>5. lépés Automatikus felhasználó-kiépítés beállítása a getAbstract

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, amelyek segítségével a felhasználók vagy csoportok TestApp hozhatók létre, frissíthetők és letilthatók az Azure AD-ben.

### <a name="configure-automatic-user-provisioning-for-getabstract-in-azure-ad"></a>Automatikus felhasználó-kiépítés konfigurálása a getAbstract az Azure AD-ben

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások**  >  **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelt bemutató képernyőkép.](common/enterprise-applications.png)

1. Az alkalmazások listájában válassza a **getAbstract** lehetőséget.

    ![Képernyőkép, amely megjeleníti a getAbstract hivatkozást az alkalmazások listájában.](common/all-applications.png)

1. Válassza a **Kiépítés** lapot.

    ![A kiépítési lapot megjelenítő képernyőkép.](common/provisioning.png)

1. A **kiépítési mód** beállítása **automatikusra**.

    ![Képernyőfelvétel: a kiépítési mód beállítása automatikus.](common/provisioning-automatic.png)

1. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a getAbstract **bérlői URL-címét** és a **titkos jogkivonat** adatait. Válassza a **kapcsolat tesztelése** lehetőséget, hogy az Azure ad képes legyen csatlakozni a getAbstract. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a getAbstract-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![A bérlői URL-címet és a titkos jogkivonat-mezőket bemutató képernyőkép.](common/provisioning-testconnection-tenanturltoken.png)

1. Az **értesítési e-mail** mezőbe írja be annak a személynek vagy csoportnak az e-mail-címét, akinek meg kell kapnia a kiépítési hibajelentési értesítéseket. Jelölje be az **e-mail-értesítés küldése hiba** esetén jelölőnégyzetet.

    ![Az értesítő e-mail-címet megjelenítő képernyőkép.](common/provisioning-notification-email.png)

1. Kattintson a **Mentés** gombra.

1. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a getAbstract** lehetőséget.

1. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban található getAbstract. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a getAbstract felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. Ha módosítja a [megfeleltetési attribútumot](../app-provisioning/customize-application-attributes.md), akkor biztosítania kell, hogy a getAbstract API támogassa a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez válassza a **Mentés** lehetőséget.

   |Attribútum|Típus|Szűréshez támogatott|
   |---|---|---|
   |userName (Felhasználónév)|Sztring|&check;|
   |active|Logikai|
   |emails[type eq "work"].value|Sztring|
   |name.givenName|Sztring|
   |name.familyName|Sztring|
   |externalId|Sztring|
   |preferredLanguage|Sztring|

1. A **leképezések** szakaszban válassza a **Azure Active Directory csoportok szinkronizálása a getAbstract** lehetőséget.

1. Tekintse át az Azure AD-ből szinkronizált getAbstract az attribútumok **leképezése** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a getAbstract tartozó csoportok egyeztetésére szolgálnak a frissítési műveletekhez. A módosítások elvégzéséhez válassza a **Mentés** lehetőséget.

    |Attribútum|Típus|Szűréshez támogatott|
    |---|---|---|
    |displayName|Sztring|&check;|
    |externalId|Sztring|
    |tagok|Referencia|

1. A hatóköri szűrők konfigurálásához tekintse meg a [hatóköri szűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)szereplő utasításokat.

1. Az Azure AD kiépítési szolgáltatás getAbstract való engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![A kiépítési állapotot bemutató képernyőkép a következőn:.](common/provisioning-toggle-on.png)

1. Adja meg a getAbstract kiépíteni kívánt felhasználókat vagy csoportokat a **Settings (beállítások** ) szakasz **hatókörében** lévő kívánt értékek kiválasztásával.

    ![A kiépítési hatókört bemutató képernyőkép.](common/provisioning-scope.png)

1. Ha készen áll a létesítésre, válassza a **Mentés** lehetőséget.

    ![A Save (Mentés) gombot megjelenítő képernyőkép.](common/provisioning-configuration-save.png)

Ez a művelet a **Beállítások** szakasz **Hatókör** területén meghatározott összes felhasználó és csoport kezdeti szinkronizálási ciklusát elindítja. A kezdeti ciklus hosszabb időt vesz igénybe, mint a következő ciklusok, amelyek körülbelül 40 percenként történnek, amíg az Azure AD kiépítési szolgáltatás fut.

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése

A kiépítés beállítása után a következő erőforrásokkal figyelheti az üzemelő példányt:

* A [kiépítési naplók](../reports-monitoring/concept-provisioning-logs.md) segítségével határozza meg, hogy mely felhasználók lettek sikeresen kiépítve vagy sikertelenül.
* Tekintse meg a [folyamatjelző sáv](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) állapotát a kiépítési ciklus állapotának megtekintéséhez és a befejezéshez.
* Ha úgy tűnik, hogy az átadási konfiguráció állapota nem megfelelő, az alkalmazás karanténba kerül. További információ a karanténba helyezési [állapotokról: az alkalmazás üzembe helyezési állapota](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>További források

* [Felhasználói fiók üzembe helyezésének kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)
