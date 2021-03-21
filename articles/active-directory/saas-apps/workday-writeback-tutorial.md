---
title: 'Oktatóanyag: munkaidő-visszaírási konfigurálása Azure Active Directoryban | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat visszaírási attribútumot az Azure AD-ből munkanapokra
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 10/14/2020
ms.author: chmutali
ms.openlocfilehash: 3a623a487dd31caf8c85b18771d90e3a6306df68
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98954004"
---
# <a name="tutorial-configure-attribute-writeback-from-azure-ad-to-workday"></a>Oktatóanyag: az visszaírási attribútum konfigurálása az Azure AD-ből munkanapokra
Ennek az oktatóanyagnak a célja, hogy megmutassa az attribútumok Azure AD-ből munkanapokra történő visszaírási elvégzendő lépéseket. A munkanap visszaírási kiépítési alkalmazás támogatja az értékek hozzárendelését a következő munkanap-attribútumokhoz:
* Munkahelyi E-mail cím 
* Munkanap felhasználóneve
* Munkahelyi vezetékes telefonszám (beleértve az országkódot, a körzetszámot, a számot és a kiterjesztést)
* Munkahelyi vezetékes telefonszám elsődleges jelzője
* Munkahelyi Mobiltelefonszám (beleértve az országkódot, a körzetszámot, a számot)
* Munkahelyi mobil elsődleges jelző

## <a name="overview"></a>Áttekintés

Miután beállította a bejövő üzembe helyezési integrációt a [Munkanapokról a helyszíni ad-](workday-inbound-tutorial.md) létesítési alkalmazásra vagy a [munkanapokra az Azure ad](workday-inbound-cloud-only-tutorial.md) kiépítési alkalmazásához, igény szerint beállíthatja, hogy a munkanap visszaírási alkalmazás a kapcsolattartási adatokat, például a munkahelyi e-maileket és a munkanapokat. 

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Ki ez a felhasználó-kiépítési megoldás a legmegfelelőbb?

Ez a munkanap visszaírási felhasználói üzembe helyezési megoldás ideális a következőkhöz:

* A Microsoft 365t használó szervezetek, akik az informatikai részleg által felügyelt mérvadó attribútumokat (például e-mail-címet, felhasználónevet és telefonszámot) szeretnék visszaírási a munkanapokra

## <a name="configure-integration-system-user-in-workday"></a>Integrációs rendszer felhasználójának konfigurálása munkanapokon

Tekintse meg az [integrációs rendszer felhasználójának konfigurálása](workday-inbound-tutorial.md#configure-integration-system-user-in-workday) egy munkanap-integrációs rendszer felhasználói fiók létrehozásához engedéllyel a munkavégzők beolvasására vonatkozó engedélyeket. 

## <a name="configuring-azure-ad-attribute-writeback-to-workday"></a>Az Azure AD-attribútumok visszaírási konfigurálása munkanapokra

Kövesse ezeket az utasításokat a felhasználói e-mail-címek és felhasználónevek visszaírási konfigurálásához Azure Active Directoryról munkanapokra.

* [A visszaírási-összekötő alkalmazás hozzáadása és a munkanapokhoz való csatlakozás létrehozása](#part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday)
* [Visszaírási-attribútumok megfeleltetésének konfigurálása](#part-2-configure-writeback-attribute-mappings)
* [A felhasználók üzembe helyezésének engedélyezése és elindítása](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday"></a>1. rész: a visszaírási-összekötő alkalmazás hozzáadása és a kapcsolat létrehozása a munkanapokhoz

**A munkanap visszaírási-összekötő konfigurálása:**

1. Nyissa meg a következőt: <https://portal.azure.com>.

2. A Azure Portal keresse meg és válassza a **Azure Active Directory** lehetőséget.

3. Válassza a **vállalati alkalmazások**, majd **az összes alkalmazás** lehetőséget.

4. Válassza az **alkalmazás hozzáadása** lehetőséget, majd válassza az **összes** kategóriát.

5. Keresse meg a **munkanap visszaírási**, és adja hozzá az alkalmazást a katalógusból.

6. Miután hozzáadta az alkalmazást, és megjelenik az alkalmazás részletei képernyő, válassza a **kiépítés** lehetőséget.

7. Módosítsa a **kiépítési** **módot** **automatikusra**.

8. Fejezze be a **rendszergazdai hitelesítő adatok** szakaszt a következőképpen:

   * **Rendszergazdai Felhasználónév** – adja meg a munkanap-integrációs rendszer fiókjának felhasználónevét a bérlői tartománynév hozzáfűzésével. Valahogy így kell kinéznie: *Felhasználónév \@ contoso4*

   * **Rendszergazdai jelszó –** Adja meg a munkanap-integrációs rendszerfiók jelszavát

   * **Bérlői URL-cím –** Adja meg a bérlőhöz tartozó munkanap webszolgáltatások végpontjának URL-címét. Ennek az értéknek a következőképpen kell kinéznie: `https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources` , ahol a *contoso4* helyére a megfelelő bérlő nevét kell cserélni, és a *wd3-Impl* helyére a megfelelő környezeti karakterlánc kerül (ha szükséges).

   * **Értesítő e-mail –** Adja meg az e-mail-címét, és jelölje be az "e-mail küldése, ha hiba történik" jelölőnégyzetet.

   * Kattintson a **kapcsolatok tesztelése** gombra. Ha a kapcsolatok tesztelése sikeres, kattintson a felül található **Save (Mentés** ) gombra. Ha nem sikerül, ellenőrizze, hogy a munkanap URL-címe és a hitelesítő adatok a munkanapokon érvényesek-e.

### <a name="part-2-configure-writeback-attribute-mappings"></a>2. rész: a visszaírási attribútum-hozzárendelések konfigurálása

Ebben a szakaszban azt fogja beállítani, hogy a visszaírási-attribútumok hogyan áramlanak az Azure AD-től a munkanapokig. 

1. A **hozzárendelések** alatt a kiépítés lapon kattintson a megfeleltetés nevére.

2. A **forrásoldali objektum hatóköre** mezőben opcionálisan szűrheti, hogy az Azure Active Directory mely felhasználói készleteknek kell szerepelniük a visszaírási. Az alapértelmezett hatókör a "minden felhasználó az Azure AD-ben".

3. Az **attribútum-hozzárendelések** szakaszban frissítse a megfelelő azonosítót, hogy jelezze a Azure Active Directory attribútumát, ahol a munkanapon dolgozó azonosítója vagy az alkalmazott azonosítója található. A népszerű egyeztetési módszer a munkanap munkavégző AZONOSÍTÓjának vagy az alkalmazott AZONOSÍTÓjának szinkronizálása az Azure AD-ben a extensionAttribute1-15 értékre, majd az Azure AD-ben ezt az attribútumot használva a felhasználók visszatérhetnek a munkanapokhoz.

4. Általában az Azure AD *userPrincipalName* attribútumát a munkanapokhoz tartozó *userid* attribútumra képezi le, és leképezi az Azure ad *mail* attribútumot a munkanap *EmailAddress* attribútumára. 

     >[!div class="mx-imgBorder"]
     >![Azure Portal](./media/workday-inbound-tutorial/workday-writeback-mapping.png)

5. Az alábbi útmutatást használva leképezheti a telefonszám-attribútum értékeit az Azure AD-ből munkanapokra. 

     | Munkanap Phone-attribútum | Várt érték | Megfeleltetési útmutató |
     |-------------------------|----------------|------------------|
     | WorkphoneLandlineIsPrimary | Igaz/hamis | Konstans vagy kifejezés leképezése, amelynek kimenete "true" vagy "false" sztring. |
     | WorkphoneLandlineCountryCodeName | [Kétbetűs ISO 3166-1 országkód](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-3) | Konstans vagy kifejezés leképezése, amelynek kimenete egy három betűs országkód. |
     | WorkphoneLandlineCountryCodeNumber | [Nemzetközi ország hívási kódja](https://en.wikipedia.org/wiki/List_of_country_calling_codes) | Konstans vagy kifejezés leképezése, amelynek kimenete érvényes országkód (a + jel nélkül). |
     | WorkphoneLandlineNumber | Teljes telefonszám, beleértve a körzetszámot | Leképezés a *telephoneNumber* attribútumra. A regex használatával távolítsa el a szóközöket, a zárójeleket és az országkódot. Lásd az alábbi példát. |
     | WorkphoneLandlineExtension | Bővítmény száma | Ha a *telephoneNumber* bővítményt tartalmaz, a regex használatával bontsa ki az értéket. |
     | WorkphoneMobileIsPrimary | Igaz/hamis | Állandó leképezés vagy kifejezés leképezése, amelynek kimenete "true" vagy "false" karakterlánc-érték |
     | WorkphoneMobileCountryCodeName | [Kétbetűs ISO 3166-1 országkód](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-3) | Konstans vagy kifejezés leképezése, amelynek kimenete egy három betűs országkód. |
     | WorkphoneMobileCountryCodeNumber | [Nemzetközi ország hívási kódja](https://en.wikipedia.org/wiki/List_of_country_calling_codes) | Konstans vagy kifejezés leképezése, amelynek kimenete érvényes országkód (a + jel nélkül). |
     | WorkphoneMobileNumber | Teljes telefonszám, beleértve a körzetszámot | Leképezés a *Mobile* attribútumra. A regex használatával távolítsa el a szóközöket, a zárójeleket és az országkódot. Lásd az alábbi példát. |

     > [!NOTE]
     > Az Change_Work_Contact munkanap webszolgáltatás meghívásakor az Azure AD a következő állandó értékeket küldi el: <br>
     > * **Communication_Usage_Type_ID** a "work" állandó sztringre van állítva <br>
     > * A **Phone_Device_Type_ID** a mobil telefonszámok és a vezetékes telefonszámok esetében a "mobil" állandó sztringre van állítva. <br>
     > 
     > A visszaírási hibákba ütközik, ha a munkanap bérlője eltérő Type_IDs használ. Az ilyen hibák megelőzése érdekében használhatja a munkaidő- **nyilvántartási hivatkozási azonosítók** feladatot, és frissítheti a Type_IDs az Azure ad által használt értékeknek megfelelően. <br>
     >  

     **Hivatkozás regex-kifejezések – 1. példa**

     Használja az alábbi reguláris kifejezést, ha a telefonszám az Azure AD-ben beállítás az önkiszolgáló jelszó-visszaállítás (SSPR) számára szükséges formátum használatával van beállítva. <br>
     Példa: Ha a telefonszám értéke + 1 1112223333 – >, akkor a regex kifejezés kimenete 1112223333 lesz

     ```C#
     Replace([telephoneNumber], , "\\+(?<isdCode>\\d* )(?<phoneNumber>\\d{10})", , "${phoneNumber}", , )
     ```

     **Hivatkozás regex-kifejezések – 2. példa**

     Használja az alábbi reguláris kifejezést, ha a telefonszám az Azure AD-ben a következő formátumban van beállítva: (XXX) XXX-XXXX. <br>
     Példa: Ha a telefonszám értéke (111) 222-3333->, akkor a regex kifejezés kimenet 1112223333 lesz.

     ```C#
     Replace([mobile], , "[()\\s-]+", , "", , )
     ```

6. A leképezések mentéséhez kattintson a Attribute-Mapping szakasz tetején található **Mentés** gombra.

## <a name="enable-and-launch-user-provisioning"></a>A felhasználók üzembe helyezésének engedélyezése és elindítása

Miután befejezte a munkaidő-kiépítési alkalmazás konfigurációját, bekapcsolhatja a kiépítési szolgáltatást a Azure Portal.

> [!TIP]
> Alapértelmezés szerint a kiépítési szolgáltatás bekapcsolásakor a rendszer kiépítési műveleteket kezdeményez a hatókörben lévő összes felhasználó számára. Ha hibák léptek fel a leképezési vagy a munkanapokon tárolt adatokkal kapcsolatban, a kiépítési feladat meghiúsulhat, és a karanténba helyezési állapotba kerülhet. Ennek elkerüléséhez ajánlott eljárásként Azt javasoljuk, hogy a **forrás objektum hatókör** -szűrőjét konfigurálja, és tesztelje az attribútumok hozzárendeléseit néhány tesztelési felhasználóval, mielőtt elindítja a teljes szinkronizálást az összes felhasználó számára. Miután meggyőződött arról, hogy a leképezések működnek, és megadja a kívánt eredményeket, távolítsa el a szűrőt, vagy fokozatosan bontsa ki, hogy több felhasználót is tartalmazzon.

1. A **létesítés** lapon állítsa be a **kiépítési állapotot** **a** következőre:.

1. A **hatókör** legördülő menüben válassza a **minden felhasználó és csoport szinkronizálása** lehetőséget. Ha ezt a lehetőséget választja, a visszaírási-alkalmazás az Azure ad-ból minden felhasználóhoz tartozó leképezett attribútumokat ír a munkanapokra, a **társítási** szabályok  ->  **hatókörében** meghatározott hatóköri szabályok alapján. 

   > [!div class="mx-imgBorder"]
   > ![Visszaírási hatókörének kiválasztása](./media/sap-successfactors-inbound-provisioning/select-writeback-scope.png)

   > [!NOTE]
   > A munkanap visszaírási kiépítési alkalmazás nem támogatja a **csak a hozzárendelt felhasználók és csoportok szinkronizálását**.
 

2. Kattintson a **Mentés** gombra.

3. Ez a művelet elindítja a kezdeti szinkronizálást, amely a forrás címtárban lévő felhasználók számától függően több órát is igénybe vehet. A folyamatjelző sáv a szinkronizálási ciklus előrehaladásának nyomon követésére használható. 

4. A Azure Portal **kiépítési naplók** lapján bármikor megtekintheti a kiépítési szolgáltatás által végrehajtott műveleteket. A naplók a kiépítési szolgáltatás által végrehajtott összes egyéni szinkronizálási eseményt felsorolják, például hogy mely felhasználók importálhatók a forrásból, és exportálhatók a célalkalmazás.  

5. A kezdeti szinkronizálás befejeződése után egy összegző jelentést fog írni a **létesítés** lapon az alábbi ábrán látható módon.

     > [!div class="mx-imgBorder"]
     > ![Kiépítés folyamatjelző sáv](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="known-issues-and-limitations"></a>Ismert problémák és korlátozások

* A visszaírási alkalmazás egy előre meghatározott értéket használ a paraméterekhez **Communication_Usage_Type_ID** és **Phone_Device_Type_ID**. Ha a munkanap bérlője más értéket használ ezeknek az attribútumoknak az esetében, akkor a visszaírási művelet sikertelen lesz. A javasolt Áthidaló megoldás a Type_IDs frissítése munkanapokon. 
* Ha a visszaírási alkalmazás a másodlagos telefonszámok frissítésére van konfigurálva, nem helyettesíti a meglévő másodlagos telefonszámot a munkanapokon. Hozzáadja még egy másodlagos telefonszámot a feldolgozói rekordhoz. Ehhez a viselkedéshez nem kerülő megoldás. 


## <a name="next-steps"></a>Következő lépések

* [További információ az Azure AD és a munkanap-integrációs forgatókönyvekről és a webszolgáltatás-hívásokról](../app-provisioning/workday-integration-reference.md)
* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)
* [Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a munkanap és a Azure Active Directory között](workday-tutorial.md)
* [Ismerje meg, hogyan integrálhat más SaaS-alkalmazásokat a Azure Active Directory](tutorial-list.md)
* [Útmutató a kiépítési konfigurációk exportálásához és importálásához](../app-provisioning/export-import-provisioning-configuration.md)

