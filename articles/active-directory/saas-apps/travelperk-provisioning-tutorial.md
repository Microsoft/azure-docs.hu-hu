---
title: 'Oktatóanyag: a TravelPerk konfigurálása az automatikus felhasználó-kiépítés Azure Active Directoryhoz | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és felépíteni felhasználói fiókokat az Azure AD-ből a TravelPerk.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 3e40f87d-8624-4b14-b098-80ff916103c3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2021
ms.author: Zhchia
ms.openlocfilehash: 19436b7faef081757e4500c76e7537ee78081bfa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104950380"
---
# <a name="tutorial-configure-travelperk-for-automatic-user-provisioning"></a>Oktatóanyag: az automatikus felhasználó-kiépítés TravelPerk konfigurálása

Ez az oktatóanyag azokat a lépéseket ismerteti, amelyeket a TravelPerk és a Azure Active Directory (Azure AD) szolgáltatásban kell végrehajtania az automatikus felhasználó-kiépítés konfigurálásához. Ha konfigurálva van, az Azure AD automatikusan kiépíti és kiosztja a felhasználókat és csoportokat a [TravelPerk](https://www.travelperk.com/) az Azure ad kiépítési szolgáltatás használatával. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Támogatott képességek

> [!div class="checklist"]
>
> - Felhasználók létrehozása a TravelPerk-ben
> - Felhasználók eltávolítása a TravelPerk-ben, ha már nincs szükség hozzáférésre
> - Felhasználói attribútumok szinkronizálása az Azure AD és a TravelPerk között
> - [Egyszeri bejelentkezés](./travelperk-tutorial.md) a TravelPerk-be (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következő előfeltételekkel:

- [Egy Azure AD-bérlő](../develop/quickstart-create-new-tenant.md).
- Egy Azure AD-beli felhasználói fiók, amely [jogosult](../roles/permissions-reference.md) a kiépítés konfigurálására (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda, alkalmazás tulajdonosa vagy globális rendszergazda).
- Aktív [TravelPerk](https://app.travelperk.com/signup) -rendszergazdai fiók.
- Prémium/Pro [csomag](https://www.travelperk.com/pricing/).


## <a name="step-1-plan-your-provisioning-deployment"></a>1. lépés Az átadás üzembe helyezésének megtervezése

1. Ismerje meg [az átadási szolgáltatás működését](../app-provisioning/user-provisioning.md).
2. Határozza meg, hogy ki lesz [az átadás hatókörében](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Határozza meg, hogy az [Azure ad és a TravelPerk között milyen adatleképezést kell leképezni](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-travelperk-to-support-provisioning-with-azure-ad"></a>2. lépés TravelPerk konfigurálása az Azure AD-vel való kiépítés támogatásához

1. Jelentkezzen be a [TravelPerk](https://app.travelperk.com/company/integrations/scim) alkalmazásba a rendszergazdai fiókjával.

2. Navigáljon a **Vállalati beállítások**  >  **integrációk**  >  **scim**

3. Kattintson a **scim API engedélyezése** lehetőségre.

   ![Engedélyezés](./media/travelperk-provisioning-tutorial/configuration.png)

4. A jóváhagyásokat a SCIM használatával is engedélyezheti. A jóváhagyások segítségével további szabályozást állíthat be azáltal, hogy a megadott jóváhagyók először jóváhagyják az utakat. Erről [itt](https://support.travelperk.com/hc/en-us/articles/360044168971-How-do-approval-processes-work-)talál további információt.

5. Megadhatja, hogy minden személy felettese automatikusan a felhasználónak kell-e lennie az utazások jóváhagyásához. Ezért a jóváhagyó a megfelelő automatikus jóváhagyási eljárásban lesz hozzárendelve. A TravelPerk az Azure **felettesének** értékét a felhasználó kívánt jóváhagyójának fogja képezni. A felhasználónak léteznie kell a platformon, mielőtt a kiépített felhasználó jóváhagyójának kellene lennie.
Nem jönnek létre jóváhagyók, ha nincsenek megfelelően konfigurálva a TravelPerk.

6. Az automatikus jóváhagyási folyamat létrehozása a **scim beállításai** között érhető el, miután engedélyezte a scim az integrációk lapról. A bekapcsolásához válassza az **identitás-szolgáltatót** , és kapcsolja be az **automatikus jóváhagyási folyamat létrehozásának engedélyezése** kapcsolót.

7. Kattintson a **módosítások mentése** gombra a szükséges jóváhagyási folyamat konfigurálása után.

   ![Automatizálás](./media/travelperk-provisioning-tutorial/approval.png)

## <a name="step-3-add-travelperk-from-the-azure-ad-application-gallery"></a>3. lépés TravelPerk hozzáadása az Azure AD Application Galleryből

Vegyen fel TravelPerk az Azure AD-alkalmazás-katalógusból a TravelPerk való kiépítés kezelésének megkezdéséhez. Ha korábban már beállította a TravelPerk-t az egyszeri bejelentkezéshez, használhatja ugyanazt az alkalmazást. Az integráció első tesztelésekor azonban érdemes létrehozni egy külön alkalmazást. Az alkalmazások katalógusból való hozzáadásáról [itt](../manage-apps/add-application-portal.md) tudhat meg többet.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>4. lépés: Az átadás hatókörében lévő személyek meghatározása

Az Azure AD átadási szolgáltatása lehetővé teszi az átadott személyek hatókörének meghatározását az alkalmazáshoz való hozzárendelés és/vagy a felhasználó/csoport attribútumai alapján. Ha a hozzárendelés alapján történő hatókör-meghatározást választja, a következő [lépésekkel](../manage-apps/assign-user-or-group-access-portal.md) rendelhet felhasználókat és csoportokat az alkalmazáshoz. Ha csak a felhasználó vagy csoport attribútumai alapján történő hatókörmeghatározást választja, az [itt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) leírt hatókörszűrőt használhatja.

- Amikor felhasználókat rendel a TravelPerk, ki kell választania az **alapértelmezett hozzáféréstől** eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva az átadásból, és az átadási naplókban nem jogosultként lesznek megjelölve. Ha az alkalmazáshoz csak az alapértelmezett hozzáférési szerepkör érhető el, akkor további szerepkörök felvételéhez [frissítheti az alkalmazásjegyzéket](../develop/howto-add-app-roles-in-azure-ad-apps.md).

- Kezdje kicsiben. Tesztelje a felhasználók és csoportok kis halmazát, mielőtt mindenkire kiterjesztené. Amikor az átadás hatóköre a hozzárendelt felhasználókra és csoportokra van beállítva, ennek szabályozásához egy vagy két felhasználót vagy csoportot rendelhet az alkalmazáshoz. Amikor a hatókör az összes felhasználóra és csoportra van beállítva, meghatározhat egy [attribútumalapú hatókörszűrőt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

## <a name="step-5-configure-automatic-user-provisioning-to-travelperk"></a>5. lépés Automatikus felhasználó-kiépítés beállítása a TravelPerk

Ez a szakasz végigvezeti az Azure AD-kiépítési szolgáltatás konfigurálásának lépésein, hogy az Azure AD-ben felhasználói és/vagy TestApp alapuló felhasználókat és/vagy csoportokat hozzon létre, frissítsen és tiltsa le.

### <a name="to-configure-automatic-user-provisioning-for-travelperk-in-azure-ad"></a>Az automatikus felhasználó-kiépítés konfigurálása a TravelPerk az Azure AD-ben:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **Vállalati alkalmazások** lehetőséget, majd a **Minden alkalmazás** elemet.

   ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **TravelPerk** lehetőséget.

   ![Az TravelPerk hivatkozás az alkalmazások listájában](common/all-applications.png)

3. Válassza a **Kiépítés** lapot.

   ![Kiépítés lap](common/provisioning.png)

4. Állítsa a **Kiépítési mód** mezőt **Automatikus** értékre.

   ![Kiépítés lap automatikus](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban kattintson az **Engedélyezés** elemre. A rendszer átirányítja a **TravelPerk** bejelentkezési oldalára. Adja meg **felhasználónevét** és **jelszavát** , majd kattintson a **Bejelentkezés** gombra. Az engedélyezés lapon kattintson az **alkalmazás engedélyezése** lehetőségre. Kattintson a **kapcsolat tesztelése** lehetőségre, hogy az Azure ad képes legyen csatlakozni a TravelPerk. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a SecureLogin-fiókja rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

   ![Rendszergazdai hitelesítő adatok](./media/travelperk-provisioning-tutorial/authorize.png)

   ![Üdvözlőképernyő](./media/travelperk-provisioning-tutorial/login.png)

   ![Access](./media/travelperk-provisioning-tutorial/authorization.png)

6. Az **Értesítés e-mailben** mezőben adja meg annak a személynek vagy csoportnak az e-mail-címét, aki az átadással kapcsolatos hibaüzeneteket kapja, és jelölje be az **E-mail-értesítés küldése hiba esetén** jelölőnégyzetet.

   ![Értesítés e-mailben](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza a **Azure Active Directory felhasználók szinkronizálása a TravelPerk** lehetőséget.

9. Tekintse át az Azure AD-ből szinkronizált felhasználói attribútumokat az **attribútum-hozzárendelési** szakaszban lévő TravelPerk. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a TravelPerk felhasználói fiókjainak a frissítési műveletekhez való megfeleltetésére szolgálnak. Ha úgy dönt, hogy módosítja a [megfelelő cél attribútumot](../app-provisioning/customize-application-attributes.md), akkor biztosítania kell, hogy a TravelPerk API támogassa a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez kattintson a **Save (Mentés** ) gombra.

   | Attribútum                                                                         | Típus      | Szűréshez támogatott |
   | --------------------------------------------------------------------------------- | --------- | ----------------------- |
   | userName (Felhasználónév)                                                                          | Sztring    | &check;                 |
   | externalId                                                                        | Sztring    |
   | active                                                                            | Logikai   |
   | name.honorificPrefix                                                              | Sztring    |
   | name.familyName                                                                   | Sztring    |
   | name.givenName                                                                    | Sztring    |
   | név. middleName                                                                   | Sztring    |
   | preferredLanguage                                                                 | Sztring    |
   | területi beállítás                                                                            | Sztring    |
   | phoneNumbers[type eq "work"].value                                                | Sztring    |
   | externalId                                                                        | Sztring    |
   | cím                                                                             | Sztring    |
   | urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: felhasználó: costCenter             | Sztring    |
   | urn: IETF: params: scim: sémák: bővítmény: Enterprise: 2.0: User: Manager                | Referencia |
   | urn: IETF: params: scim: sémák: bővítmény: travelperk: 2.0: felhasználó: neme                 | Sztring    |
   | urn: IETF: params: scim: sémák: bővítmény: travelperk: 2.0: felhasználó: dateOfBirth            | Sztring    |
   | urn: IETF: params: scim: sémák: bővítmény: travelperk: 2.0: felhasználó: invoiceProfiles        | Tömb     |
   | urn:IETF:params:scim:schemas:Extension:travelperk: 2.0:User:emergencyContact. Name  | Sztring    |
   | urn: IETF: params: scim: sémák: bővítmény: travelperk: 2.0: felhasználó: emergencyContact. Phone | Sztring    |
   | urn: IETF: params: scim: sémák: bővítmény: travelperk: 2.0: felhasználó: travelPolicy           | Sztring    |

10. Hatókörszűrők konfigurálásához tekintse meg a [hatókörszűrővel kapcsolatos oktatóanyagban](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) szereplő következő utasításokat.

11. Az Azure AD-kiépítési szolgáltatás TravelPerk való engedélyezéséhez módosítsa a **kiépítési állapotot** **a** **Beállítások** szakaszban.

    ![Kiépítési állapot bekapcsolva](common/provisioning-toggle-on.png)

12. Adja meg a TravelPerk kiépíteni kívánt felhasználókat és/vagy csoportokat a **Settings (beállítások** ) szakasz **hatókörében** a kívánt értékek kiválasztásával.

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

- [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

- [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)