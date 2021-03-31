---
title: 'Oktatóanyag: az SAP SuccessFactors visszaírási konfigurálása a Azure Active Directoryban | Microsoft Docs'
description: Megtudhatja, hogyan állíthatja vissza az attribútumot az Azure AD-ből származó SAP-SuccessFactors.
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 10/14/2020
ms.author: chmutali
ms.openlocfilehash: 3260787dec4ae26cd6ef7cc3bd562f39db8e3655
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99526975"
---
# <a name="tutorial-configure-attribute-write-back-from-azure-ad-to-sap-successfactors"></a>Oktatóanyag: az attribútumok az Azure AD-ből az SAP SuccessFactors való visszaírásának konfigurálása
Ennek az oktatóanyagnak a célja, hogy megjelenjenek az Azure AD-ből származó, az SAP SuccessFactors Employee Central-re vonatkozó írási és olvasási műveletek lépései. 

## <a name="overview"></a>Áttekintés

Beállíthatja, hogy az SAP SuccessFactors visszaírási alkalmazás meghatározott attribútumokat írjon a Azure Active Directoryról az SAP SuccessFactors Employee Central szolgáltatásba. A SuccessFactors visszaírási kiépítési alkalmazás támogatja az értékek hozzárendelését a következő alkalmazottak központi attribútumaihoz:

* Munkahelyi E-mail cím
* Felhasználónév
* Munkahelyi telefonszám (beleértve az országkódot, a körzetszámot, a számot és a kiterjesztést)
* Vállalati telefonszám elsődleges jelzője
* Mobiltelefonszám (beleértve az országkódot, a körzetszámot, a számot)
* Mobiltelefon elsődleges jelzője 
* Felhasználói custom01 – custom15 attribútumai
* loginMethod attribútum

> [!NOTE]
> Ez az alkalmazás nem rendelkezik függőséggel a SuccessFactors bejövő felhasználó-integrációs alkalmazásaival. A SuccessFactors-től függetlenül konfigurálhatja [a helyszíni ad-](sap-successfactors-inbound-provisioning-tutorial.md) létesítési alkalmazást vagy a [SUCCESSFACTORS az Azure ad](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) üzembe helyezési alkalmazásba.

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Ki ez a felhasználó-kiépítési megoldás a legmegfelelőbb?

Ez a SuccessFactors visszaírási-alapú felhasználói üzembe helyezési megoldás ideális a következőkhöz:

* A Microsoft 365t használó szervezetek, akik az informatikai részleg által felügyelt mérvadó attribútumokat (például e-mail-címet, telefonszámot, felhasználónevet) szeretnék visszaírni a SuccessFactors Employee Central szolgáltatásba.

## <a name="configuring-successfactors-for-the-integration"></a>Az integráció SuccessFactors konfigurálása

Az összes SuccessFactors-kiépítési összekötőhöz szükség van egy SuccessFactors-fiók hitelesítő adataira, amely a megfelelő engedélyekkel rendelkezik az Employee Central OData API-k meghívásához. Ez a szakasz a szolgáltatásfiók SuccessFactors-ben való létrehozásának és a megfelelő engedélyek megadásának lépéseit ismerteti. 

* [API-felhasználói fiók létrehozása/azonosítása a SuccessFactors-ben](#createidentify-api-user-account-in-successfactors)
* [API-engedélyek szerepkör létrehozása](#create-an-api-permissions-role)
* [Engedélyezési csoport létrehozása az API-felhasználó számára](#create-a-permission-group-for-the-api-user)
* [Engedélyezési szerepkör megadása az engedélyezési csoportnak](#grant-permission-role-to-the-permission-group)

### <a name="createidentify-api-user-account-in-successfactors"></a>API-felhasználói fiók létrehozása/azonosítása a SuccessFactors-ben
A SuccessFactors felügyeleti csapatával vagy a megvalósítási partnerrel együttműködve hozzon létre vagy azonosítson egy olyan felhasználói fiókot a SuccessFactors, amelyet a OData API-k meghívásához fog használni. A fiók felhasználónevének és jelszavának hitelesítő adatai a kiépítési alkalmazások Azure AD-ben való konfigurálásakor szükségesek. 

### <a name="create-an-api-permissions-role"></a>API-engedélyek szerepkör létrehozása

1. Jelentkezzen be az SAP SuccessFactors egy olyan felhasználói fiókkal, amely hozzáféréssel rendelkezik a felügyeleti központhoz.
1. Keressen rá az *engedélyek kezelése* lehetőségre, majd válassza a **jogosultsági szerepkörök kezelése** elemet a keresési eredmények közül.

   ![Engedélyezési szerepkörök kezelése](./media/sap-successfactors-inbound-provisioning/manage-permission-roles.png)

1. Az engedélyezési szerepkör listából válassza az **új létrehozása** lehetőséget.

   > [!div class="mx-imgBorder"]
   > ![Új engedélyezési szerepkör létrehozása](./media/sap-successfactors-inbound-provisioning/create-new-permission-role-1.png)

1. Adja hozzá az új engedély szerepkörhöz tartozó **szerepkör nevét** és **leírását** . A név és a Leírás azt jelzi, hogy a szerepkör API-használati engedélyekkel rendelkezik.

   > [!div class="mx-imgBorder"]
   > ![Engedélyezési szerepkör részletei](./media/sap-successfactors-inbound-provisioning/permission-role-detail.png)

1. Az engedély beállításai területen kattintson az **engedély...** elemre, majd görgessen le az engedélyezési listáról, és kattintson az **integrációs eszközök kezelése** lehetőségre. Jelölje be a **rendszergazda számára a ODATA API alapszintű hitelesítéssel való elérésének engedélyezése** jelölőnégyzetet.

   > [!div class="mx-imgBorder"]
   > ![Integrációs eszközök kezelése](./media/sap-successfactors-inbound-provisioning/manage-integration-tools.png)

1. Görgessen le ugyanabban a mezőben, és válassza az **Employee Central API** elemet. Az alább látható engedélyek hozzáadásával olvassa el a ODATA API-t és a szerkesztést a ODATA API használatával. Válassza a szerkesztés lehetőséget, ha azt tervezi, hogy ugyanazt a fiókot használja a SuccessFactors forgatókönyvhöz való visszaíráshoz. 

   > [!div class="mx-imgBorder"]
   > ![Írási engedélyek olvasása](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)

1. Kattintson a **kész** gombra. Kattintson a **Save Changes** (Módosítások mentése) gombra.

### <a name="create-a-permission-group-for-the-api-user"></a>Engedélyezési csoport létrehozása az API-felhasználó számára

1. A SuccessFactors felügyeleti központban keressen rá az *engedélyek kezelése csoportra*, majd válassza az **engedélyek kezelése** lehetőséget a keresési eredmények közül.

   > [!div class="mx-imgBorder"]
   > ![Engedélyezési csoportok kezelése](./media/sap-successfactors-inbound-provisioning/manage-permission-groups.png)

1. A jogosultsági csoportok kezelése ablakban kattintson az **új létrehozása** elemre.

   > [!div class="mx-imgBorder"]
   > ![Új csoport hozzáadása](./media/sap-successfactors-inbound-provisioning/create-new-group.png)

1. Adja hozzá a csoport nevét az új csoporthoz. A csoport nevének azt kell jeleznie, hogy a csoport API-felhasználók számára készült.

   > [!div class="mx-imgBorder"]
   > ![Engedély csoportjának neve](./media/sap-successfactors-inbound-provisioning/permission-group-name.png)

1. Tagok hozzáadása a csoporthoz. Például kiválaszthatja a **Felhasználónév** elemet a személyek készlet legördülő menüből, majd megadhatja az integrációhoz használni kívánt API-fiók felhasználónevét. 

   > [!div class="mx-imgBorder"]
   > ![Csoporttagok hozzáadása](./media/sap-successfactors-inbound-provisioning/add-group-members.png)

1. Kattintson a **kész** gombra az engedély csoport létrehozásának befejezéséhez.

### <a name="grant-permission-role-to-the-permission-group"></a>Engedélyezési szerepkör megadása az engedélyezési csoportnak

1. A SuccessFactors felügyeleti központban keressen rá az *engedélyezési szerepkörök kezelése* lehetőségre, majd válassza az **engedélyezési szerepkörök kezelése** lehetőséget a keresési eredmények közül.
1. Az **engedélyezési szerepkör listából** válassza ki az API-használati engedélyekhez létrehozott szerepkört.
1. Az adja **meg ezt a szerepkört a következőhöz:...** kattintson a **Hozzáadás...** gombra.
1. Válassza az **engedély csoport.** .. lehetőséget a legördülő menüből, majd kattintson a **kiválasztás...** elemre a csoportok ablak megnyitásához, és válassza ki a fenti létrehozott csoportot. 

   > [!div class="mx-imgBorder"]
   > ![Engedély csoportjának hozzáadása](./media/sap-successfactors-inbound-provisioning/add-permission-group.png)

1. Tekintse át az engedélyezési szerepkör engedélyezését az engedély csoport számára. 
   > [!div class="mx-imgBorder"]
   > ![Engedélyezési szerepkör és csoport részletei](./media/sap-successfactors-inbound-provisioning/permission-role-group.png)

1. Kattintson a **Save Changes** (Módosítások mentése) gombra.

## <a name="preparing-for-successfactors-writeback"></a>Felkészülés a SuccessFactors visszaírási

A SuccessFactors visszaírási kiépítési alkalmazás bizonyos *kódokat* használ az e-mailek és a telefonszámok megadásához az Employee Centralban. A *kódok* értéke állandó értékként van beállítva az attribútum-leképezési táblában, és az egyes SuccessFactors-példányok esetében eltérőek. Ez a szakasz a *kódok* értékének rögzítéséhez szükséges lépéseket tartalmazza.

   > [!NOTE]
   > A jelen szakasz lépéseinek végrehajtásához vonja be a SuccessFactors-rendszergazdát. 

### <a name="identify-email-and-phone-number-picklist-names"></a>Az e-mailek és telefonszámok nevének azonosítása 

Az SAP-SuccessFactors a *listára* konfigurálható beállítások állíthatók be, amelyből a felhasználó kiválaszthat. A különböző típusú e-mailek és telefonszámok (például üzleti, személyes és egyéb) a lista egy listás használatával jelennek meg. Ebben a lépésben a SuccessFactors-bérlőben konfigurált listákat azonosítjuk az e-mailek és telefonszámok értékének tárolására. 
 
1. A SuccessFactors felügyeleti központban keressen az *üzleti konfiguráció kezelése* elemre. 

   > [!div class="mx-imgBorder"]
   > ![Üzleti konfiguráció kezelése](./media/sap-successfactors-inbound-provisioning/manage-business-config.png)

1. A **HRIS elemek** területen válassza a **emailInfo** elemet, majd kattintson az **e-mail típusú** mező *részleteire* .

   > [!div class="mx-imgBorder"]
   > ![E-mail-adatok beolvasása](./media/sap-successfactors-inbound-provisioning/get-email-info.png)

1. Az e **-mail-típus** részletei lapon jegyezze fel a mezőhöz társított lista nevét. Alapértelmezés szerint ez a **ecEmailType**. Ez azonban eltérő lehet a bérlőben. 

   > [!div class="mx-imgBorder"]
   > ![E-mail lista azonosítása](./media/sap-successfactors-inbound-provisioning/identify-email-picklist.png)

1. A **HRIS elemek** területen válassza a **phoneInfo** elemet, majd kattintson a **telefonos típus** mező *részleteire* .

   > [!div class="mx-imgBorder"]
   > ![Telefonos adatok beolvasása](./media/sap-successfactors-inbound-provisioning/get-phone-info.png)

1. A **telefon típusa** Részletek lapon jegyezze fel a mezőhöz társított lista nevét. Alapértelmezés szerint ez a **ecPhoneType**. Ez azonban eltérő lehet a bérlőben. 

   > [!div class="mx-imgBorder"]
   > ![Telefonos lista azonosítása](./media/sap-successfactors-inbound-provisioning/identify-phone-picklist.png)

### <a name="retrieve-constant-value-for-emailtype"></a>EmailType állandó értékének beolvasása

1. A SuccessFactors felügyeleti központban keresse meg és nyissa meg a *listás centert*. 
1. Használja az előző szakaszban rögzített e-mail-lista nevét (pl. ecEmailType) az e-mail lista megkereséséhez. 

   > [!div class="mx-imgBorder"]
   > ![E-mail típusú lista keresése](./media/sap-successfactors-inbound-provisioning/find-email-type-picklist.png)

1. Nyissa meg az aktív e-mail listákat. 

   > [!div class="mx-imgBorder"]
   > ![Aktív e-mail típusú lista megnyitása](./media/sap-successfactors-inbound-provisioning/open-active-email-type-picklist.png)

1. Az e-mail típus lista lapon válassza ki az *üzleti* levelezés típusát.

   > [!div class="mx-imgBorder"]
   > ![Üzleti levelezés típusának kiválasztása](./media/sap-successfactors-inbound-provisioning/select-business-email-type.png)

1. Jegyezze fel az *üzleti* e-mailhez társított **Beállítások azonosítóját** . Ezt a kódot fogjuk használni a *emailType* az attribútum-leképezési táblában.

   > [!div class="mx-imgBorder"]
   > ![E-mail típus kódjának beolvasása](./media/sap-successfactors-inbound-provisioning/get-email-type-code.png)

   > [!NOTE]
   > A vessző karakter eldobása az érték átmásolásakor. Például ha az **azonosító** értéke *8 448*, akkor a *emailType* az Azure AD-ben állítsa be a *8448* állandó számra (a vessző karakter nélkül). 

### <a name="retrieve-constant-value-for-phonetype"></a>PhoneType állandó értékének beolvasása

1. A SuccessFactors felügyeleti központban keresse meg és nyissa meg a *listás centert*. 
1. Használja az előző szakaszban rögzített telefonos választási lista nevét a telefonos lista megkereséséhez. 

   > [!div class="mx-imgBorder"]
   > ![Telefon típusának keresése](./media/sap-successfactors-inbound-provisioning/find-phone-type-picklist.png)

1. Nyissa meg az aktív telefonos listákat. 

   > [!div class="mx-imgBorder"]
   > ![Az aktív telefonos típus lista megnyitása](./media/sap-successfactors-inbound-provisioning/open-active-phone-type-picklist.png)

1. A telefonos listára vonatkozó listán tekintse át a különböző típusú telefonszámokat a **lista értékei** között.

   > [!div class="mx-imgBorder"]
   > ![Telefonos típusok áttekintése](./media/sap-successfactors-inbound-provisioning/review-phone-types.png)

1. Jegyezze fel a *vállalati* telefonhoz társított **Beállítások azonosítóját** . Ezt a kódot fogjuk használni a *businessPhoneType* az attribútum-leképezési táblában.

   > [!div class="mx-imgBorder"]
   > ![Üzleti telefon kódjának beolvasása](./media/sap-successfactors-inbound-provisioning/get-business-phone-code.png)

1. Jegyezze fel a *mobiltelefonhoz* társított **Beállítások azonosítóját** . Ezt a kódot fogjuk használni a *cellPhoneType* az attribútum-leképezési táblában.

   > [!div class="mx-imgBorder"]
   > ![Mobiltelefon kódjának beolvasása](./media/sap-successfactors-inbound-provisioning/get-cell-phone-code.png)

   > [!NOTE]
   > A vessző karakter eldobása az érték átmásolásakor. Például ha az **azonosító** értéke *10 606*, akkor a *cellPhoneType* az Azure AD-ben állítsa be a *10606* állandó számra (a vessző karakter nélkül). 


## <a name="configuring-successfactors-writeback-app"></a>SuccessFactors visszaírási-alkalmazás konfigurálása

Ez a szakasz a következő lépéseit tartalmazza: 

* [A kiépítési összekötő alkalmazás hozzáadása és a SuccessFactors-kapcsolat konfigurálása](#part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors)
* [Attribútumok leképezésének konfigurálása](#part-2-configure-attribute-mappings)
* [A felhasználók üzembe helyezésének engedélyezése és elindítása](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-configure-connectivity-to-successfactors"></a>1. rész: a kiépítési összekötő alkalmazás hozzáadása és a SuccessFactors-kapcsolat konfigurálása

**SuccessFactors-visszaírási konfigurálása:**

1. Nyissa meg a következőt: <https://portal.azure.com>

2. A bal oldali navigációs sávon válassza a **Azure Active Directory**

3. Válassza a **vállalati alkalmazások**, majd **az összes alkalmazás** lehetőséget.

4. Válassza az **alkalmazás hozzáadása** lehetőséget, és válassza az **összes** kategóriát.

5. Keresse meg a **SuccessFactors visszaírási**, és adja hozzá az alkalmazást a gyűjteményből.

6. Miután hozzáadta az alkalmazást, és megjelenik az alkalmazás részletei képernyő, válassza a **kiépítés** lehetőséget.

7. A **kiépítési** **mód** módosítása **automatikusra**

8. Fejezze be a **rendszergazdai hitelesítő adatok** szakaszt a következőképpen:

   * **Rendszergazdai Felhasználónév** – adja meg a SuccessFactors API felhasználói fiókjának felhasználónevét a HOZZÁFŰZÖTT vállalati azonosítóval. Formátuma: **username \@ companyID**

   * **Rendszergazdai jelszó –** Adja meg a SuccessFactors API felhasználói fiók jelszavát. 

   * **Bérlői URL-cím –** Adja meg a SuccessFactors OData API Services-végpont nevét. Csak a kiszolgáló állomásnevét adja meg http vagy https nélkül. Az értéknek a következőhöz hasonlóan kell kinéznie: `api4.successfactors.com` .

   * **Értesítő e-mail –** Adja meg az e-mail-címét, és jelölje be az "e-mail küldése, ha hiba történik" jelölőnégyzetet.
    > [!NOTE]
    > Az Azure AD kiépítési szolgáltatás e-mailes értesítést küld, ha a kiépítési feladatok [karantén](../app-provisioning/application-provisioning-quarantine-status.md) állapotba kerülnek.

   * Kattintson a **kapcsolatok tesztelése** gombra. Ha a kapcsolatok tesztelése sikeres, kattintson a felül található **Save (Mentés** ) gombra. Ha nem sikerül, ellenőrizze, hogy a SuccessFactors hitelesítő adatok és az URL-cím érvényes-e.
    >[!div class="mx-imgBorder"]
    >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sfwb-provisioning-creds.png)

   * Miután a hitelesítő adatok mentése sikeresen megtörtént, a **leképezések** szakasz megjeleníti az alapértelmezett leképezést. Frissítse az oldalt, ha az attribútum-hozzárendelések nem láthatók.  

### <a name="part-2-configure-attribute-mappings"></a>2. rész: attribútumok leképezésének konfigurálása

Ebben a szakaszban azt fogja konfigurálni, hogy a felhasználói adatok hogyan áramlanak a SuccessFactors a Active Directoryba.

1. A kiépítés lapon a **leképezések** területen kattintson a **Azure Active Directory felhasználók kiépítése** elemre.

1. A **forrás objektum hatóköre** mezőben kiválaszthatja, hogy az Azure ad mely felhasználói készleteit kell figyelembe venni az attribútum-alapú szűrők definiálásával. Az alapértelmezett hatókör a "minden felhasználó az Azure AD-ben". 
   > [!TIP]
   > Amikor első alkalommal konfigurálja a kiépítési alkalmazást, meg kell vizsgálnia és ellenőriznie kell az attribútumok hozzárendeléseit és kifejezéseit, hogy biztosan megadja a kívánt eredményt. A Microsoft azt javasolja, hogy a **forrás objektum hatókörében** lévő hatókör-szűrők használatával tesztelje a leképezéseket néhány, az Azure ad-ből származó tesztelési felhasználóval. Miután meggyőződött arról, hogy a leképezések működnek, távolítsa el a szűrőt, vagy fokozatosan bontsa ki, hogy több felhasználót is tartalmazzon.

1. A **cél objektum műveletek** mező csak a **frissítési** műveletet támogatja.

1. Az **attribútum-hozzárendelések** szakasz leképezési táblájában a következő Azure Active Directory attribútumokat rendelheti a SuccessFactors. Az alábbi táblázat útmutatást nyújt a írási vissza attribútumok leképezéséhez. 

   | \# | Azure AD-attribútum | SuccessFactors attribútum | Megjegyzések |
   |--|--|--|--|
   | 1 | Alkalmazottkód | personIdExternal | Alapértelmezés szerint ez az attribútum a megfelelő azonosító. Az Alkalmazottkód helyett bármely más Azure AD-attribútumot használhat, amely az értéket a SuccessFactors personIdExternal egyenlően tárolja.    |
   | 2 | Levelezés | e-mail | Leképezi az e-mail-attribútum forrását. Tesztelési célból leképezheti a userPrincipalName e-mail-címre. |
   | 3 | 8448 | emailType | Ez az állandó érték az üzleti levelezéshez társított SuccessFactors-azonosító érték. Frissítse ezt az értéket a SuccessFactors-környezetnek megfelelően. Az érték megadásának lépéseiért tekintse meg az [állandó érték beolvasása a emailType](#retrieve-constant-value-for-emailtype) című szakaszt. |
   | 4 | true | emailIsPrimary | Ezzel az attribútummal állíthatja be az üzleti e-maileket elsődlegesként a SuccessFactors-ben. Ha az üzleti e-mailek nem elsődlegesek, állítsa hamis értékre ezt a jelzőt. |
   | 5 | userPrincipalName | [custom01 – custom15] | Az **Új leképezés hozzáadása** lehetőséggel írhat userPrincipalName vagy bármely Azure ad-attribútumot a SuccessFactors felhasználói objektumában elérhető egyéni attribútumra.  |
   | 6 | Helyszíni SamAccountName | username | Az **Új leképezés hozzáadása** lehetőséggel megadhatja a helyszíni sAMAccountName a SuccessFactors username attribútumhoz. [Azure ad Connect Sync: Directory-bővítmények](../hybrid/how-to-connect-sync-feature-directory-extensions.md) használatával szinkronizálhatja a sAMAccountName az Azure ad-be. Ekkor megjelenik a forrás legördülő lista *extension_yourTenantGUID_samAccountName* |
   | 7 | SSO | loginMethod | Ha a SuccessFactors-bérlő [részleges egyszeri bejelentkezésre](https://apps.support.sap.com/sap/support/knowledge/en/2320766)van beállítva, akkor az új leképezés hozzáadása lehetőséggel megadhatja, hogy a LOGINMETHOD "SSO" vagy "pwd" konstans értékre legyen állítva. |
   | 8 | telephoneNumber | businessPhoneNumber | Ezt a leképezést használhatja a *telephoneNumber* az Azure ad-ből a SuccessFactors Business/Work telefonszámra. |
   | 9 | 10605 | businessPhoneType | Ez az állandó érték a vállalati telefonhoz társított SuccessFactors-azonosító érték. Frissítse ezt az értéket a SuccessFactors-környezetnek megfelelően. Az érték megadásának lépéseiért tekintse meg az [állandó érték beolvasása a phoneType](#retrieve-constant-value-for-phonetype) című szakaszt. |
   | 10 | true | businessPhoneIsPrimary | Ezzel az attribútummal állíthatja be a vállalati telefonszám elsődleges jelzőjét. Az érvényes értékek: true vagy FALSE. |
   | 11 | mobil | cellPhoneNumber | Ezt a leképezést használhatja a *telephoneNumber* az Azure ad-ből a SuccessFactors Business/Work telefonszámra. |
   | 12 | 10606 | cellPhoneType | Ez az állandó érték a mobiltelefonhoz társított SuccessFactors-azonosító érték. Frissítse ezt az értéket a SuccessFactors-környezetnek megfelelően. Az érték megadásának lépéseiért tekintse meg az [állandó érték beolvasása a phoneType](#retrieve-constant-value-for-phonetype) című szakaszt. |
   | 13 | hamis | cellPhoneIsPrimary | Ezzel az attribútummal állíthatja be a Mobiltelefonszám elsődleges jelzőjét. Az érvényes értékek: true vagy FALSE. |
 
1. Érvényesítse és tekintse át az attribútumok hozzárendeléseit. 
 
    >[!div class="mx-imgBorder"]
    >![Visszaírási-attribútumok leképezése](./media/sap-successfactors-inbound-provisioning/writeback-attribute-mapping.png)

1. A leképezések mentéséhez kattintson a **Save (Mentés** ) gombra. Ezután frissítjük a JSON Path API-kifejezéseket, hogy a SuccessFactors-példányban lévő phoneType-kódokat használják. 
1. Válassza a **Speciális beállítások megjelenítése** lehetőséget. 

    >[!div class="mx-imgBorder"]
    >![Speciális beállítások megjelenítése](./media/sap-successfactors-inbound-provisioning/show-advanced-options.png)

1. Kattintson az **attribútumok szerkesztése SuccessFactors** elemre. 

   > [!NOTE] 
   > Ha az **attribútumok szerkesztése SuccessFactors** beállítás nem jelenik meg a Azure Portalban, használja az URL-címet az *https://portal.azure.com/?Microsoft_AAD_IAM_forceSchemaEditorEnabled=true* oldal eléréséhez. 

1. Az ebben a nézetben szereplő **API-kifejezés** oszlop az összekötő által használt JSON-elérésiút-kifejezéseket jeleníti meg. 
1. Frissítse a JSON-elérésiút-kifejezéseket a vállalati telefon és a mobiltelefon számára a környezetnek megfelelő azonosító érték (*businessPhoneType* és *cellPhoneType*) használatára. 

    >[!div class="mx-imgBorder"]
    >![Telefonos JSON-elérésiút változása](./media/sap-successfactors-inbound-provisioning/phone-json-path-change.png)

1. A leképezések mentéséhez kattintson a **Save (Mentés** ) gombra.

## <a name="enable-and-launch-user-provisioning"></a>A felhasználók üzembe helyezésének engedélyezése és elindítása

Miután befejezte az SuccessFactors-létesítési alkalmazás konfigurációját, bekapcsolhatja a kiépítési szolgáltatást a Azure Portal.

> [!TIP]
> Alapértelmezés szerint a kiépítési szolgáltatás bekapcsolásakor a rendszer kiépítési műveleteket kezdeményez a hatókörben lévő összes felhasználó számára. Ha hibák vannak a leképezési vagy az adatproblémákkal kapcsolatban, akkor előfordulhat, hogy a kiépítési feladat meghiúsul, és a karanténba helyezési állapotba lép. Ennek elkerüléséhez ajánlott eljárásként Azt javasoljuk, hogy a **forrás objektum hatókör** -szűrőjét konfigurálja, és tesztelje az attribútumok hozzárendeléseit néhány tesztelési felhasználóval, mielőtt elindítja a teljes szinkronizálást az összes felhasználó számára. Miután meggyőződött arról, hogy a leképezések működnek, és megadja a kívánt eredményeket, távolítsa el a szűrőt, vagy fokozatosan bontsa ki, hogy több felhasználót is tartalmazzon.

1. A **létesítés** lapon állítsa be a **kiépítési állapotot** **a** következőre:.

1. Válassza ki a **hatókört**. Az alábbi lehetőségek közül választhat: 
   * **Minden felhasználó és csoport szinkronizálása**: akkor válassza ezt a lehetőséget, ha az Azure ad-ből az összes felhasználóhoz tartozó leképezett attribútumokat szeretné a SuccessFactors- re írni  ->  . 
   * **Csak a hozzárendelt felhasználók és csoportok szinkronizálása**: akkor válassza ezt a lehetőséget, ha **csak a felhasználók**  ->    ->  **és csoportok** kezelése menüpontban az alkalmazáshoz hozzárendelt felhasználókra vonatkozó leképezett attribútumok írhatók vissza. Ezeket a felhasználókat a **hozzárendelések**  ->  **forrás objektum hatókörében** meghatározott hatóköri szabályok is vonatkoznak.

   > [!div class="mx-imgBorder"]
   > ![Visszaírási hatókörének kiválasztása](./media/sap-successfactors-inbound-provisioning/select-writeback-scope.png)

   > [!NOTE]
   > A SuccessFactors visszaírási kiépítési alkalmazás nem támogatja a "csoport-hozzárendelést". Csak a "felhasználói hozzárendelés" támogatott. 

1. Kattintson a **Mentés** gombra.

1. Ez a művelet elindítja a kezdeti szinkronizálást, amely az Azure AD-bérlőben és a művelethez meghatározott hatókörben lévő felhasználók számától függően több órát is igénybe vehet. A folyamatjelző sáv a szinkronizálási ciklus előrehaladásának nyomon követésére használható. 

1. A Azure Portal **kiépítési naplók** lapján bármikor megtekintheti a kiépítési szolgáltatás által végrehajtott műveleteket. A kiépítési naplók a kiépítési szolgáltatás által végrehajtott összes egyéni szinkronizálási eseményt felsorolják. 

1. A kezdeti szinkronizálás befejezésekor a rendszer egy naplózási összesítő jelentést ír a **létesítés** lapon az alább látható módon.

   > [!div class="mx-imgBorder"]
   > ![Kiépítés folyamatjelző sáv](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="supported-scenarios-known-issues-and-limitations"></a>Támogatott forgatókönyvek, ismert problémák és korlátozások

Tekintse meg az SAP SuccessFactors integrációs útmutatójának [visszaírási-forgatókönyvek című szakaszát](../app-provisioning/sap-successfactors-integration-reference.md#writeback-scenarios) . 

## <a name="next-steps"></a>Következő lépések

* [Az Azure AD és az SAP SuccessFactors integrációs dokumentációjának részletes bemutatása](../app-provisioning/sap-successfactors-integration-reference.md)
* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)
* [Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a SuccessFactors és a Azure Active Directory között](successfactors-tutorial.md)
* [Ismerje meg, hogyan integrálhat más SaaS-alkalmazásokat a Azure Active Directory](tutorial-list.md)
* [Útmutató a kiépítési konfigurációk exportálásához és importálásához](../app-provisioning/export-import-provisioning-configuration.md)