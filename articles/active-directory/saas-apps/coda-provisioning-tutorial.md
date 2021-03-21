---
title: 'Oktatóanyag: a Coda konfigurálása automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és kiépíteni felhasználói fiókjait az Azure AD-ből a Codaba.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 4d6f06dd-a798-4c22-b84f-8a11f1b8592a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/31/2020
ms.author: Zhchia
ms.openlocfilehash: 8df1588a78829252d55f82349d6889c754c989e6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97673226"
---
# <a name="tutorial-configure-coda-for-automatic-user-provisioning"></a>Oktatóanyag: a Coda konfigurálása automatikus felhasználó-kiépítés esetén

Ez az oktatóanyag azokat a lépéseket ismerteti, amelyeket a Coda és a Azure Active Directory (Azure AD) szolgáltatásban kell elvégezni az automatikus felhasználó-kiépítés konfigurálásához. Ha konfigurálva van, az Azure AD automatikusan kiépíti és kiosztja a felhasználókat a [CODA](https://coda.io/) számára az Azure ad kiépítési szolgáltatás használatával. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md).


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a Coda-ban
> * A Coda-felhasználók eltávolítása, ha már nincs szükség hozzáférésre
> * Felhasználói attribútumok szinkronizálása az Azure AD és a Coda között
> * [Egyszeri bejelentkezés](./coda-tutorial.md) a codaba (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Azure AD-bérlő](../develop/quickstart-create-new-tenant.md)
* Egy felhasználói fiók az Azure AD-ben az átadás konfigurálására vonatkozó [engedéllyel](../roles/permissions-reference.md) (pl. alkalmazás-rendszergazda, felhőalkalmazás-rendszergazda, alkalmazástulajdonos vagy globális rendszergazda).
* Egy [CODA vállalati](https://help.coda.io/en/articles/3520174-getting-started-with-sso) rendszergazdai fiók.

## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés Az átadás üzembe helyezésének megtervezése
1. Ismerje meg [az átadási szolgáltatás működését](../app-provisioning/user-provisioning.md).
2. Határozza meg, hogy ki lesz [az átadás hatókörében](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Határozza meg, hogy az [Azure ad és a Coda között milyen adatleképezést kell leképezni](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-coda-to-support-provisioning-with-azure-ad"></a>2. lépés A Coda konfigurálása az Azure AD-vel való kiépítés támogatásához

1. Nyissa meg a szervezet felügyeleti konzolját a szervezeti beállítások lehetőség kiválasztásával... a munkaterület alatti menü.

    ![A Coda vállalati szervezet SCIM beállításai](media/coda-provisioning-tutorial/coda-scim-enable.png)

2. Győződjön meg arról, hogy a SCIM beállítás engedélyezve van.
3. Jegyezze fel a SCIM alap URL-címét és a SCIM tulajdonosi jogkivonatát. Ha nincs tulajdonosi jogkivonat, kattintson az új jogkivonat előállítása elemre.

## <a name="step-3-add-coda-from-the-azure-ad-application-gallery"></a>3. lépés A Coda hozzáadása az Azure AD Application Galleryből

A Coda hozzáadásával megkezdheti az Azure AD alkalmazás-katalógusból való kiépítés kezelését. Ha korábban már beállította a Coda-t az SSO-hoz, használhatja ugyanazt az alkalmazást. Az integráció első tesztelésekor azonban érdemes létrehozni egy külön alkalmazást. Az alkalmazások katalógusból való hozzáadásáról [itt](../manage-apps/add-application-portal.md) tudhat meg többet.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Az átadás hatókörében lévő személyek meghatározása

Az Azure AD kiépítési szolgáltatása lehetővé teszi az alkalmazáshoz való hozzárendelés és a felhasználó attribútumai alapján kiépített hatókör kiosztását. Ha úgy dönt, hogy a hatókör ki lesz kiépítve az alkalmazáshoz a hozzárendelés alapján, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet felhasználókat az alkalmazáshoz. Ha olyan hatókört választ ki, amely kizárólag a felhasználó attribútumai alapján lesz kiépítve, az [itt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)leírtak szerint használhat egy hatókör-szűrőt.

* Amikor felhasználókat rendel a Codahoz, ki kell választania az **alapértelmezett hozzáféréstől** eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva az átadásból, és az átadási naplókban nem jogosultként lesznek megjelölve. Ha az alkalmazáshoz csak az alapértelmezett hozzáférési szerepkör érhető el, akkor további szerepkörök felvételéhez [frissítheti az alkalmazásjegyzéket](../develop/howto-add-app-roles-in-azure-ad-apps.md).

* Kezdje kicsiben. Tesztelje a felhasználókat egy kis készlettel, mielőtt mindenki számára elérhetővé tenné. Ha a kiépítés hatóköre a hozzárendelt felhasználók értékre van állítva, akkor ezt úgy szabályozhatja, hogy hozzárendel egy vagy két felhasználót az alkalmazáshoz. Ha a hatókör minden felhasználóra van beállítva, megadhat egy [attribútum-alapú hatóköri szűrőt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).


## <a name="step-5-configure-automatic-user-provisioning-to-coda"></a>5. lépés Automatikus felhasználó-kiépítés beállítása a Coda-be

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói hozzárendelések alapján hozza létre, frissítse és tiltsa le a TestApp felhasználókat.

### <a name="to-configure-automatic-user-provisioning-for-coda-in-azure-ad"></a>A Coda automatikus felhasználói üzembe helyezésének konfigurálása az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **CODA** elemet.

    ![A Coda hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a felügyeleti lehetőségek kezeléséről a kiépítési lehetőséggel.](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

    ![Képernyőkép a kiépítési mód legördülő listájáról az automatikus lehetőséggel.](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a Coda-bérlő URL-címét és a titkos tokent, amelyet korábban a 2. lépésben lekért. Kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad csatlakozhasson a Coda szolgáltatáshoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a Coda-fiók rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

     ![Képernyőfelvétel: a rendszergazdai hitelesítő adatok párbeszédpanel, ahol megadhatja a bérlő U R L-t és a titkos jogkivonatot.](./media/coda-provisioning-tutorial/provisioning.png)

6. Az **Értesítés e-mailben** mezőben adja meg annak a személynek vagy csoportnak az e-mail-címét, aki az átadással kapcsolatos hibaüzeneteket kapja, és jelölje be az **E-mail-értesítés küldése hiba esetén** jelölőnégyzetet.

    ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a Coda**-be lehetőséget.

9. Tekintse át az Azure AD-ből a Coda-be szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelés** szakaszban. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a Coda felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. Ha úgy dönt, hogy módosítja a [megfelelő cél attribútumot](../app-provisioning/customize-application-attributes.md), akkor biztosítania kell, hogy a Coda API támogassa a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   |Attribútum|Típus|
   |---|---|
   |userName (Felhasználónév)|Sztring|
   |active|Logikai|
   |name.givenName|Sztring|
   |name.familyName|Sztring|


10. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

11. Az Azure AD kiépítési szolgáltatás a Coda számára történő engedélyezéséhez módosítsa a **kiépítési állapotot** a **következőre** a **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

12. Adja meg a Coda számára kiépíteni kívánt felhasználókat a **Settings (beállítások** ) szakasz **hatókörében** a kívánt értékek kiválasztásával.

    ![Átadási hatókör](common/provisioning-scope.png)

13. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet elindítja a **Beállítások** szakasz **hatókörében** definiált összes felhasználó kezdeti szinkronizálási ciklusát. A kezdeti ciklus elvégzése hosszabb időt vesz igénybe, mint a későbbi ciklusok, amelyek az Azure AD átadási szolgáltatásának futtatása során körülbelül 40 percenként lesznek végrehajtva.

## <a name="step-6-monitor-your-deployment"></a>6. lépés Az üzemelő példány figyelése
Az átadás konfigurálása után a következő erőforrásokkal monitorozhatja az üzemelő példányt:

1. Az [átadási naplókkal](../reports-monitoring/concept-provisioning-logs.md) határozhatja meg, hogy mely felhasználók átadása sikeres, és melyeké sikertelen.
2. A [folyamatjelzőn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) láthatja az átadási ciklus állapotát és azt, hogy mennyi hiányzik még a befejeződéséhez.
3. Ha úgy tűnik, hogy az átadási konfiguráció állapota nem megfelelő, az alkalmazás karanténba kerül. A karanténállapotokról [itt](../app-provisioning/application-provisioning-quarantine-status.md) találhat további információt.

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)