---
title: 'Oktatóanyag: a PaperCut Cloud Print Management konfigurálása az automatikus felhasználó-kiépítés a Azure Active Directorysal | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és kiépíteni felhasználói fiókjait az Azure AD-ből a PaperCut Cloud Print Management szolgáltatásba.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 7e65d727-2951-4aec-a7a3-7bde49ed09e2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2020
ms.author: Zhchia
ms.openlocfilehash: c0738e7033f0aa39ca5f12a44c06d086175eb8fe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98731447"
---
# <a name="tutorial-configure-papercut-cloud-print-management-for-automatic-user-provisioning"></a>Oktatóanyag: a PaperCut Cloud Print Management konfigurálása a felhasználók automatikus kiépítési felállításához

Ez az oktatóanyag azokat a lépéseket ismerteti, amelyeket a PaperCut Cloud Print Management és a Azure Active Directory (Azure AD) szolgáltatásban kell végrehajtania az automatikus felhasználó-kiépítés konfigurálásához. Ha konfigurálva van, az Azure AD automatikusan kiépíti és kiosztja a felhasználókat és csoportokat, hogy az Azure AD üzembe helyezési szolgáltatás használatával [PaperCut a Felhőbeli nyomtatást](https://www.papercut.com/products/papercut-pocket/) . A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Támogatott képességek

> [!div class="checklist"]
> * Felhasználók létrehozása a PaperCut Cloud Print Management szolgáltatásban
> * Felhasználók eltávolítása a PaperCut Cloud Print Management szolgáltatásban, ha már nincs szükség hozzáférésre
> * A felhasználói attribútumok szinkronizálása az Azure AD és a PaperCut Cloud Print Management között

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

* [Egy Azure AD-bérlő](../develop/quickstart-create-new-tenant.md). 
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](../roles/permissions-reference.md) a kiépítés konfigurálására (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda, alkalmazás tulajdonosa vagy globális rendszergazda). 
* Egy PaperCut Cloud Print Management rendszergazdai fiók.


## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés Az átadás üzembe helyezésének megtervezése

1. Ismerje meg [az átadási szolgáltatás működését](../app-provisioning/user-provisioning.md).
2. Határozza meg, hogy ki lesz [az átadás hatókörében](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Határozza meg, hogy az [Azure ad és a PaperCut Felhőbeli nyomtatási felügyelete között milyen adatleképezést kell leképezni](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-papercut-cloud-print-management-to-support-provisioning-with-azure-ad"></a>2. lépés A PaperCut Cloud Print Management konfigurálása az Azure AD-vel való kiépítés támogatásához

1. Jelentkezzen be a [PaperCut Pocket felügyeleti konzol](https://pocket.papercut.com/) vagy a [PaperCut struktúra felügyeleti konzolba](https://hive.papercut.com/).

2. Keresse meg   >  az **összes bővítményt**, és keresse meg a **Microsoft Azure ad User Sync addon**.

3. Kattintson a **továbbiak** gombra, és kattintson a **Hozzáadás** gombra a telepítéshez.



4. A telepítés után a rendszer a **bérlő URL-címével** és **titkos jogkivonatával** jeleníti meg az addon részleteit tartalmazó oldalt. Ezek az értékek a bérlői URL-cím \* és a titkos jogkivonat mezőben lesznek megadva a \* PaperCut Cloud Print Management-alkalmazás létesítés lapján a Azure Portal.



## <a name="step-3-add-papercut-cloud-print-management-from-the-azure-ad-application-gallery"></a>3. lépés PaperCut Cloud Print Management hozzáadása az Azure AD Application Galleryből

Vegye fel a PaperCut Cloud Print managementet az Azure AD-alkalmazás-katalógusból a kiépítés felügyeletének megkezdéséhez a felhő PaperCut. Ha korábban már beállította a PaperCut Cloud Print managementet az SSO-hoz, ugyanazt az alkalmazást használhatja. Az integráció első tesztelésekor azonban érdemes létrehozni egy külön alkalmazást. Az alkalmazások katalógusból való hozzáadásáról [itt](../manage-apps/add-application-portal.md) tudhat meg többet.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Az átadás hatókörében lévő személyek meghatározása

Az Azure AD átadási szolgáltatása lehetővé teszi az átadott személyek hatókörének meghatározását az alkalmazáshoz való hozzárendelés és/vagy a felhasználó/csoport attribútumai alapján. Ha a hozzárendelés alapján történő hatókör-meghatározást választja, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet felhasználókat és csoportokat az alkalmazáshoz. Ha csak a felhasználó vagy csoport attribútumai alapján történő hatókörmeghatározást választja, az [itt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) leírt hatókörszűrőt használhatja.

* Amikor felhasználókat és csoportokat rendel a Cloud Print PaperCut, ki kell választania az **alapértelmezett hozzáféréstől** eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva az átadásból, és az átadási naplókban nem jogosultként lesznek megjelölve. Ha az alkalmazáshoz csak az alapértelmezett hozzáférési szerepkör érhető el, akkor további szerepkörök felvételéhez [frissítheti az alkalmazásjegyzéket](../develop/howto-add-app-roles-in-azure-ad-apps.md).

* Kezdje kicsiben. Tesztelje a felhasználók és csoportok kis halmazát, mielőtt mindenkire kiterjesztené. Amikor az átadás hatóköre a hozzárendelt felhasználókra és csoportokra van beállítva, ennek szabályozásához egy vagy két felhasználót vagy csoportot rendelhet az alkalmazáshoz. Amikor a hatókör az összes felhasználóra és csoportra van beállítva, meghatározhat egy [attribútumalapú hatókörszűrőt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

## <a name="step-5-configure-automatic-user-provisioning-to-papercut-cloud-print-management"></a>5. lépés Automatikus felhasználó-kiépítés beállítása a PaperCut Cloud Print Management szolgáltatásba

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, valamint a felhasználók és/vagy csoportok PaperCut való létrehozásához, frissítéséhez és letiltásához az Azure AD-ban felhasználói és/vagy csoport-hozzárendelések alapján.

### <a name="to-configure-automatic-user-provisioning-for-papercut-cloud-print-management-in-azure-ad"></a>Az Azure AD-ben a PaperCut Cloud Print Management automatikus felhasználó-kiépítés beállítása:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

   ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **PaperCut Cloud Print Management** lehetőséget.

   ![A PaperCut Cloud Print Management hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

   ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

   ![Kiépítés lap automatikus](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a PaperCut Cloud Print Management-bérlői URL-címét és a titkos jogkivonatot. Kattintson a **kapcsolat tesztelése** elemre annak biztosításához, hogy az Azure ad csatlakozhasson a PaperCut Cloud Print Management szolgáltatáshoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a PaperCut Cloud Print Management-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

   ![Jogkivonat](common/provisioning-testconnection-tenanturltoken.png)

6. Az **Értesítés e-mailben** mezőben adja meg annak a személynek vagy csoportnak az e-mail-címét, aki az átadással kapcsolatos hibaüzeneteket kapja, és jelölje be az **E-mail-értesítés küldése hiba esetén** jelölőnégyzetet.

   ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók kiépítése** lehetőséget.
   ![HRE leképezése](media/papercut-cloud-print-management-provisioning-tutorial/mapping.png)

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-leképezési** szakaszban található PaperCut Felhőbeli nyomtatási felügyelethez. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a PaperCut Cloud Print Management felhasználói fiókjainak a frissítési műveletekhez való egyeztetésére szolgálnak. Ha úgy dönt, hogy módosítja a [megfelelő cél attribútumot](../app-provisioning/customize-application-attributes.md), akkor biztosítania kell, hogy a PaperCut Cloud Print Management API támogassa a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   |Attribútum|Típus|Szűréshez támogatott|
   |---|---|---|
   |userName (Felhasználónév)|Sztring|&check;|
   |displayName|Sztring|
   |emails[type eq "work"].value|Sztring|

10. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

11. Ha engedélyezni szeretné az Azure AD kiépítési szolgáltatást a PaperCut Cloud Print Management szolgáltatáshoz, módosítsa a **kiépítési állapotot** **a következőre** a **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

12. Adja meg azokat a felhasználókat és/vagy csoportokat, akik számára ki szeretné építeni a PaperCut a Felhőbeli nyomtatás kezeléséhez. Ehhez válassza ki a kívánt értékeket a **hatókörben** a **Beállítások** szakaszban.

    ![Átadási hatókör](common/provisioning-scope.png)

13. Amikor készen áll az átadásra, kattintson a **Mentés** gombra.

    ![Átadási konfiguráció mentése](common/provisioning-configuration-save.png)

Ez a művelet a **Beállítások** szakasz **Hatókör** területén meghatározott összes felhasználó és csoport kezdeti szinkronizálási ciklusát elindítja. A kezdeti ciklus elvégzése hosszabb időt vesz igénybe, mint a későbbi ciklusok, amelyek az Azure AD átadási szolgáltatásának futtatása során körülbelül 40 percenként lesznek végrehajtva.

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