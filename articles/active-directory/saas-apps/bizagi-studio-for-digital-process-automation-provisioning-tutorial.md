---
title: 'Oktatóanyag: a bizarr Studio konfigurálása a digitális folyamatok automatizálásához az automatikus felhasználó-kiépítés Azure Active Directory segítségével | Microsoft Docs'
description: Ismerje meg, hogy miként lehet automatikusan kiépíteni és felépíteni felhasználói fiókokat az Azure AD-ből a bizarr studióba a digitális folyamatok automatizálásához.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 2fbff65a-5345-4c08-a6c7-60b80d867a3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2020
ms.author: Zhchia
ms.openlocfilehash: 72e021f47bb8db4dedf0e434d0d94bb2118a4c00
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98728159"
---
# <a name="tutorial-configure-bizagi-studio-for-digital-process-automation-for-automatic-user-provisioning"></a>Oktatóanyag: a bizarr Studio konfigurálása a digitális folyamatok automatizálásához a felhasználók automatikus üzembe helyezéséhez

Ez az oktatóanyag azokat a lépéseket ismerteti, amelyeket a bizarr Studióban kell elvégezni a digitális folyamatok automatizálásához és Azure Active Directory (Azure AD) az automatikus felhasználó-kiépítés konfigurálásához. Ha erre van konfigurálva, az Azure AD automatikusan kiépíti és kiosztja a felhasználókat és csoportokat a bizarr Studio-hoz a [digitális folyamatok automatizálásához](https://www.bizagi.com/) az Azure ad kiépítési szolgáltatás használatával. A szolgáltatás funkcióival, működésével és a gyakori kérdésekkel kapcsolatos fontos részletekért lásd: [Felhasználók átadásának és megszüntetésének automatizálása a SaaS-alkalmazásokban az Azure Active Directoryval](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Támogatott képességek
> [!div class="checklist"]
> * Felhasználók létrehozása a bizarr Studióban a digitális folyamatok automatizálásához
> * A bizarr Studióban lévő felhasználók eltávolítása a digitális folyamatok automatizálásához, ha már nincs szükség hozzáférésre
> * A felhasználói attribútumok szinkronizálása az Azure AD és a bizarr Studio között a digitális folyamatok automatizálása érdekében
> * [Egyszeri bejelentkezés](./bizagi-studio-for-digital-process-automation-tutorial.md) a bizarr studióba a digitális folyamatok automatizálásához (ajánlott)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyagban ismertetett forgatókönyv feltételezi, hogy már rendelkezik a következőkkel:

* [Egy Azure AD-bérlő](../develop/quickstart-create-new-tenant.md). 
* Egy Azure AD-beli felhasználói fiók, amely [jogosult](../roles/permissions-reference.md) a kiépítés konfigurálására. Ilyenek például az alkalmazás rendszergazdája, a felhőalapú alkalmazás rendszergazdája, az alkalmazás tulajdonosa vagy a globális rendszergazda. 
* A bizarr Studio for digitális Process Automation 11.2.4.2 X vagy újabb verziója.

## <a name="plan-your-provisioning-deployment"></a>Az átadás üzembe helyezésének megtervezése
Kövesse az alábbi lépéseket a tervezéshez:

1. Ismerje meg [az átadási szolgáltatás működését](../app-provisioning/user-provisioning.md).
2. Határozza meg, hogy kik lesznek [a kiépítés hatókörében](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Határozza meg, hogy az [Azure ad és a bizarr Studio milyen adatleképezést biztosít a digitális folyamatok automatizálásához](../app-provisioning/customize-application-attributes.md). 

## <a name="configure-to-support-provisioning-with-azure-ad"></a>Konfigurálás az Azure AD-vel való kiépítés támogatásához
A bizarr Studio for digitális folyamatok automatizálásának konfigurálásához az Azure AD-vel való kiépítés támogatásához kövesse az alábbi lépéseket:

1. Jelentkezzen be a munkahelyi portálra **rendszergazdai engedélyekkel** rendelkező felhasználóként.

2. Lépjen a **rendszergazda**  >  **biztonsági**  >  **OAuth 2 alkalmazáshoz**.

   ![Képernyőkép a bizarr-ről, a OAuth 2 alkalmazással kiemelve.](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/admin.png)

3. Válassza a **Hozzáadás** lehetőséget.
4. A **támogatás típusa** mezőben válassza a **tulajdonosi jogkivonat** lehetőséget. Az **engedélyezett hatókör** beállításnál válassza az **API** és a **felhasználói szinkronizálás** lehetőséget. Kattintson a **Mentés** gombra.

   ![Képernyőkép az alkalmazás regisztrálása beállításról, a támogatás típusa és a megengedett hatókör kiemelve.](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/token.png)

5. Másolja ki és mentse az **ügyfél titkos kulcsát**. A bizarr Studio for digitális Process Automation alkalmazáshoz tartozó Azure Portal a **kiépítés** lapon az ügyfél titkos értéke szerepel a **titkos jogkivonat** mezőben.

   ![Képernyőkép a OAuth, az ügyfél titkos highlighed.](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/secret.png)

## <a name="add-the-application-from-the-azure-ad-gallery"></a>Az alkalmazás hozzáadása az Azure AD-katalógusból

A digitális folyamatok automatizálásához a bizarr studióhoz való kiépítés kezelésének megkezdéséhez vegye fel az alkalmazást az Azure AD Application Gallery webhelyről. Ha korábban már beállította a bizarr Studio for digitális folyamatok automatizálását az egyszeri bejelentkezéshez, ugyanazt az alkalmazást használhatja. Ha először teszteli az integrációt, hozzon létre egy külön alkalmazást. További információ: rövid útmutató [: alkalmazás hozzáadása a Azure Active Directory (Azure ad) bérlőhöz](../manage-apps/add-application-portal.md). 

## <a name="define-who-is-in-scope-for-provisioning"></a>A kiépítés hatókörében lévő felhasználók meghatározása 

Az Azure AD kiépítési szolgáltatásával kiépítheti az alkalmazáshoz való hozzárendelés alapján kiépített hatókört a felhasználó és a csoport attribútumai alapján, vagy mindkettőt. Ha a hatókört a hozzárendelés alapján használja, tekintse meg a felhasználók és csoportok az alkalmazáshoz való hozzárendeléséhez és [Graph API a csoportok](../manage-apps/assign-user-or-group-access-portal.md) hozzárendelésének visszavonásához szükséges lépéseket. Ha a hatóköre kizárólag a felhasználó vagy csoport attribútumain alapul, egy hatókör-szűrőt is használhat. További információ: [attribútum-alapú alkalmazás kiépítés hatóköri szűrőkkel](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

Vegye figyelembe a következő szempontokat a hatókörről:

* Amikor felhasználókat és csoportokat rendel a bizarr studióhoz a digitális folyamatok automatizálásához, ki kell választania az **alapértelmezett hozzáféréstől** eltérő szerepkört. Az alapértelmezett hozzáférési szerepkörrel rendelkező felhasználók ki vannak zárva a kiépítés alól, és a kiépítési naplókban vannak megjelölve, mivel a nem ténylegesen jogosultként lesz megjelölve. Ha az alkalmazás egyetlen szerepköre az alapértelmezett hozzáférési szerepkör, akkor a további szerepkörök hozzáadásához [frissítheti az alkalmazás-jegyzékfájlt](../develop/howto-add-app-roles-in-azure-ad-apps.md) . 

* Kezdje kicsiben. Tesztelje a felhasználók és csoportok kis halmazát, mielőtt mindenkire kiterjesztené. Ha a kiépítés hatóköre a hozzárendelt felhasználókhoz és csoportokhoz van beállítva, ezt úgy szabályozhatja, hogy egy vagy két felhasználót vagy csoportot rendel az alkalmazáshoz. Ha a hatókör minden felhasználóra és csoportra van beállítva, megadhat egy [attribútum-alapú hatókör-szűrőt](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="configure-automatic-user-provisioning"></a>A felhasználók automatikus átadásának konfigurálása 

Ez a szakasz végigvezeti a felhasználók és csoportok létrehozásához, frissítéséhez és letiltásához szükséges Azure AD-kiépítési szolgáltatás konfigurálásának lépésein. Ezt a tesztelési alkalmazásban végezheti el, az Azure AD-ben a felhasználók és csoportok hozzárendelései alapján.

### <a name="configure-automatic-user-provisioning-for-bizagi-studio-for-digital-process-automation-in-azure-ad"></a>Az Azure AD-ben a digitális folyamatok automatizálására szolgáló bizarr Studio automatikus felhasználó-kiépítés konfigurálása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **vállalati alkalmazások**  >  **minden alkalmazás** lehetőséget.

    ![Képernyőkép a Azure Portalről, nagyvállalati alkalmazásokkal és a Kiemelt alkalmazásokkal.](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **bizarr Studio lehetőséget a digitális folyamatok automatizálásához**.

3. Válassza a **Kiépítés** lapot.

    ![Képernyőkép a kezelés lehetőségeiről a kiépítés kiemelésével.](common/provisioning.png)

4. A **kiépítési mód** beállítása **automatikusra**.

    ![Screenshotof kiépítési mód vezérlő, automatikus kiemelve.](common/provisioning-automatic.png)

5. A **rendszergazdai hitelesítő adatok** szakaszban adja meg a bérlői URL-címet és a titkos tokent a bizarr studióhoz a digitális folyamatok automatizálásához. 

      * **Bérlői URL-cím:** Adja meg a bizarr SCIM-végpontot a következő szerkezettel:  `<Your_Bizagi_Project>/scim/v2/` .
         Példa: `https://my-company.bizagi.com/scim/v2/`.

      * **Titkos jogkivonat:** Ezt az értéket a cikk korábbi részében ismertetett lépésből kéri le a rendszer.

      Válassza a **kapcsolat tesztelése** lehetőséget annak biztosításához, hogy az Azure ad csatlakozhasson a digitális folyamatok automatizálásához használható bizarr studióhoz. Ha a kapcsolat meghiúsul, győződjön meg arról, hogy a bizarr Studio for digitális Process Automation-fiók rendszergazdai jogosultságokkal rendelkezik, és próbálkozzon újra.

   ![Képernyőkép a rendszergazdai hitelesítő adatokról, a tesztelési kapcsolatok kiemelésével.](common/provisioning-testconnection-tenanturltoken.png)

6. Az **értesítő e-mailek** esetében adja meg egy olyan személy vagy csoport e-mail-címét, akinek meg kell kapnia a kiépítési hibákra vonatkozó értesítéseket. Válassza az **e-mail-értesítés küldése hiba** esetén lehetőséget.

    ![Az értesítő e-mail-beállítások képernyőképe.](common/provisioning-notification-email.png)

7. Kattintson a **Mentés** gombra.

8. A **leképezések** szakaszban válassza az **Azure Active Directory-felhasználók szinkronizálása a bizarr Studio digitális folyamatok automatizálásához** lehetőséget.

9. Az **attribútum-leképezés** szakaszban tekintse át az Azure ad-ből szinkronizált felhasználói attribútumokat a digitális folyamatok automatizálására szolgáló bizarr studióba. Az **egyeztetési** tulajdonságokként kiválasztott attribútumok a bizarr Studio felhasználói fiókjainak egyeztetésére használhatók a frissítési műveletekhez a digitális folyamatok automatizálásához. Ha megváltoztatja a [megfeleltetési attribútumot](../app-provisioning/customize-application-attributes.md), meg kell győződnie arról, hogy a bizarr Studio for digitális Process Automation API támogatja a felhasználók szűrését az adott attribútum alapján. A módosítások elvégzéséhez válassza a **Mentés** lehetőséget.

   |Attribútum|Típus|Szűréshez támogatott|
   |---|---|---|
   |userName (Felhasználónév)|Sztring|&check;|
   |active|Logikai|
   |emails[type eq "work"].value|Sztring|
   |name.givenName|Sztring|
   |name.familyName|Sztring|
   |név. formázott|Sztring|
   |phoneNumbers[type eq "mobile"].value|Sztring|

   Az egyéni bővítmény attribútumai hozzáadhatók a **Speciális beállítások megjelenítése > a bizarr szerkesztéséhez**. Az egyéni bővítmény attribútumait el kell látni az **urn: IETF: paraméterek: scim: schemas: Extension: bizarr: 2.0: UserProperties:**. Ha például az egyéni bővítmény attribútuma **IdentificationNumber**, az attribútumot **urn: IETF: params: scim: schemas: Extension: bizarr: 2.0: UserProperties: IdentificationNumber** kell hozzáadni. A módosítások elvégzéséhez válassza a **Mentés** lehetőséget.
   
    ![Attribútumok listájának szerkesztése.](media/bizagi-studio-for-digital-process-automation-provisioning-tutorial/edit.png)  

   Az egyéni attribútumok hozzáadásával kapcsolatos további információkért tekintse meg az [alkalmazás attribútumainak testreszabása](../app-provisioning/customize-application-attributes.md)című részt.

> [!NOTE]
> Csak az alapvető típusú tulajdonságok támogatottak (például string, Integer, Boolean, DateTime stb.). A számszerű táblákhoz vagy több típushoz csatolt tulajdonságok még nem támogatottak.

10. A hatóköri szűrők konfigurálásához tekintse meg a [hatóköri szűrő oktatóanyagát](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Ha engedélyezni szeretné az Azure AD kiépítési szolgáltatást a bizarr studióhoz a digitális folyamatok automatizálásához, a **Beállítások** szakaszban módosítsa a **kiépítési állapotot** **a** következőre:.

    ![A kiépítési állapot váltógomb képernyőképe.](common/provisioning-toggle-on.png)

12. Adja meg azokat a felhasználókat és csoportokat, amelyeket a bizarr studióhoz szeretne telepíteni a digitális folyamatok automatizálásához. A **Beállítások** szakaszban válassza ki a kívánt értékeket a **hatókörben**.

    ![Képernyőfelvétel a hatókör beállításairól.](common/provisioning-scope.png)

13. Ha készen áll a létesítésre, válassza a **Mentés** lehetőséget.

    ![Képernyőkép a vezérlő mentéséről.](common/provisioning-configuration-save.png)

Ez a művelet a **Beállítások** szakasz **Hatókör** területén meghatározott összes felhasználó és csoport kezdeti szinkronizálási ciklusát elindítja. A kezdeti ciklus elvégzése hosszabb időt vesz igénybe, mint a későbbi ciklusok, amelyek az Azure AD átadási szolgáltatásának futtatása során körülbelül 40 percenként lesznek végrehajtva. 

## <a name="monitor-your-deployment"></a>Az üzemelő példány figyelése
A kiépítés beállítása után a következő erőforrásokkal figyelheti az üzemelő példányt:

- A [kiépítési naplók](../reports-monitoring/concept-provisioning-logs.md) segítségével határozza meg, hogy mely felhasználók lettek sikeresen kiépítve vagy sikertelenül.
- Ellenőrizze a [folyamatjelző sáv](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) állapotát a kiépítési ciklus állapotának megtekintéséhez és a befejezéshez.
- Ha a létesítési konfiguráció sérült állapotban van, az alkalmazás Karanténba kerül. További információkért lásd: [az alkalmazás üzembe helyezése a karantén állapotában](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="additional-resources"></a>További források

* [Felhasználói fiók átadásának kezelése vállalati alkalmazásokhoz](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>További lépések

* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)