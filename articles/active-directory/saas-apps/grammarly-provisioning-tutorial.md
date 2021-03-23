---
title: 'Oktatóanyag: az automatikus felhasználó-kiépítés nyelvtani beállítása a Azure Active Directory | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és kiépíteni felhasználói fiókjait az Azure AD-ből nyelvtanilag.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: cd2dd9d7-4901-40c8-8888-98850557b072
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2021
ms.author: Zhchia
ms.openlocfilehash: ca01289ce66afe642081e5be17373e640dd1e46d
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104864805"
---
# <a name="tutorial-configure-grammarly-for-automatic-user-provisioning"></a>Oktatóanyag: nyelvtani konfigurálás a felhasználók automatikus üzembe helyezéséhez

Ez az oktatóanyag leírja, hogy milyen lépéseket kell elvégeznie mind a nyelvtani, mind a Azure Active Directory (Azure AD) szolgáltatásban az automatikus felhasználó-kiépítés konfigurálásához. Ha konfigurálva van, az Azure AD automatikusan kiépíti és kiosztja a felhasználókat és a csoportokat az Azure AD kiépítési [szolgáltatás használatával.](https://www.grammarly.com/) A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása nyelvtanilag
> * A felhasználókat nyelvtanilag távolíthatja el, ha már nincs szükség hozzáférésre
> * A felhasználói attribútumok szinkronizálása az Azure AD és a nyelvtan között

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Azure AD-bérlő](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Egy felhasználói fiók az Azure AD-ben az átadás konfigurálására vonatkozó [engedéllyel](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) (pl. alkalmazás-rendszergazda, felhőalkalmazás-rendszergazda, alkalmazástulajdonos vagy globális rendszergazda). 
* Egy nyelvtani üzleti fiók rendszergazdai hozzáféréssel.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés Az átadás üzembe helyezésének megtervezése
1. Ismerje meg [az átadási szolgáltatás működését](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
1. Határozza meg, hogy ki lesz [az átadás hatókörében](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
1. Határozza meg, hogy az [Azure ad és a nyelvtan szerint milyen adatleképezést szeretne leképezni](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-grammarly-to-support-provisioning-with-azure-ad"></a>2. lépés Nyelvtani konfigurálás az Azure AD-vel való kiépítés támogatásához

Forduljon a nyelvtani képviselőhöz, vagy írjon ide a <support@grammarly.com> kiépítési jogkivonat igényléséhez.

## <a name="step-3-add-grammarly-from-the-azure-ad-application-gallery"></a>3. lépés Nyelvtan hozzáadása az Azure AD Application Galleryből

Az Azure AD-alkalmazás-katalógusból nyelvtanilag adhat hozzá a kiépítés nyelvtani kezelésének megkezdéséhez. Ha korábban már beállította a nyelvtant az egyszeri bejelentkezéshez, használhatja ugyanazt az alkalmazást. Javasoljuk, hogy hozzon létre egy külön alkalmazást, amikor először teszteli az integrációt. Ha többet szeretne megtudni arról, hogyan adhat hozzá egy alkalmazást a katalógusból, tekintse meg [ezt a](../manage-apps/add-application-portal.md)rövid útmutatót.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Az átadás hatókörében lévő személyek meghatározása

Az Azure AD-kiépítési szolgáltatás az alkalmazáshoz való hozzárendelés vagy a felhasználó vagy csoport attribútumai alapján kiépített hatókörre használható. Ha a hozzárendelés alapján történő hatókör-meghatározást választja, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet felhasználókat és csoportokat az alkalmazáshoz. Ha olyan hatókört választ ki, amely kizárólag a felhasználó vagy csoport attribútumai alapján lesz kiépítve, az alkalmazások kiosztási [szűrőkkel](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)való kiosztása című témakörben leírtak szerint egy hatókör-szűrőt is használhat.

* Ha a felhasználókat és csoportokat nyelvtanilag rendeli hozzá, ki kell választania az **alapértelmezett hozzáféréstől** eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól, és a kiépítési naplók nem jogosultak arra, hogy ne legyenek ténylegesen feltüntetve. Ha az alkalmazás egyetlen szerepköre az alapértelmezett hozzáférési szerepkör, akkor a további szerepkörök hozzáadásához [frissítheti az alkalmazás-jegyzékfájlt](../develop/howto-add-app-roles-in-azure-ad-apps.md) .

* Kezdje kicsiben. Próbálja ki a felhasználókat és csoportokat egy kis készlettel, mielőtt mindenki számára elérhetővé vált. Ha a kiépítés hatóköre hozzárendelt felhasználókhoz és csoportokhoz van beállítva, ezt a beállítást egy vagy két felhasználó vagy csoport hozzárendelésével szabályozhatja az alkalmazáshoz. Ha a hatókör minden felhasználóra és csoportra van beállítva, megadhat egy [attribútum-alapú hatókör-szűrőt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).


## <a name="step-5-configure-automatic-user-provisioning-to-grammarly"></a>5. lépés Automatikus felhasználó-kiépítés beállítása nyelvtanilag

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, amelyek segítségével a felhasználók vagy csoportok TestApp hozhatók létre, frissíthetők és letilthatók az Azure AD-ben.

### <a name="configure-automatic-user-provisioning-for-grammarly-in-azure-ad"></a>Az automatikus felhasználó-kiépítés beállítása nyelvtanilag az Azure AD-ben

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások**  >  **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelt bemutató képernyőkép.](common/enterprise-applications.png)

1. Az alkalmazások listájában válassza a **nyelvtanilag** lehetőséget.

    ![Képernyőkép, amely megjeleníti a nyelvtani hivatkozást az alkalmazások listájában.](common/all-applications.png)

1. Válassza a **Kiépítés** lapot.

    ![A kiépítési lapot megjelenítő képernyőkép.](common/provisioning.png)

1. A **kiépítési mód** beállítása **automatikusra**.

    ![Képernyőfelvétel: a kiépítési mód beállítása automatikus.](common/provisioning-automatic.png)

1. A **rendszergazdai hitelesítő adatok** szakaszban a **bérlői URL** megadása mezőbe írja be a címet `https://sso.grammarly.com/scim/v2` , és a **titkos jogkivonat** mezőben adja meg a nyelvtanilag megadott jogkivonatot (lásd a fenti 2. lépést). Kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad képes legyen nyelvtanilag csatlakozni. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a nyelvtani fiók rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

    ![A bérlői URL-címet és a titkos jogkivonat-mezőket bemutató képernyőkép.](common/provisioning-testconnection-tenanturltoken.png)

1. Az **értesítési e-mail** mezőbe írja be annak a személynek vagy csoportnak az e-mail-címét, akinek meg kell kapnia a kiépítési hibajelentési értesítéseket. Jelölje be az **e-mail-értesítés küldése hiba** esetén jelölőnégyzetet.

    ![Az értesítő e-mail-címet megjelenítő képernyőkép.](common/provisioning-notification-email.png)

1. Kattintson a **Mentés** gombra.

1. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása nyelvtanilag** lehetőséget.

1. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok használatával a rendszer nyelvtanilag egyezteti a felhasználói fiókokat a frissítési műveletekhez. Ha megváltoztatja a [megfeleltetési cél attribútumot](../app-provisioning/customize-application-attributes.md), akkor biztosítania kell, hogy a nyelvtani API támogassa a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez válassza a **Mentés** lehetőséget.

   |Attribútum|Típus|Szűréshez támogatott|
   |---|---|---|
   |userName (Felhasználónév)|Sztring|&check;|
   |externalId|Sztring|
   |active|Logikai|
   |displayName|Sztring|
   |emails[type eq "work"].value|Sztring|


1. A hatóköri szűrők konfigurálásához tekintse meg a [hatóköri szűrő oktatóanyagában](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)szereplő utasításokat.

1. Az Azure AD-kiépítési szolgáltatás nyelvtanilag történő engedélyezéséhez módosítsa a **kiépítési állapotot** a  **Beállítások** szakaszban.

    ![A kiépítési állapotot bemutató képernyőkép a következőn:.](common/provisioning-toggle-on.png)

1. Adja meg azokat a felhasználókat vagy csoportokat, akiket nyelvtanilag szeretne kiépíteni a **Beállítások** szakasz **hatókörében** lévő kívánt értékek kiválasztásával.

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
