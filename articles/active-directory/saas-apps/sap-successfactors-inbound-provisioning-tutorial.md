---
title: 'Oktatóanyag: a SuccessFactors bejövő kiépítés konfigurálása az AD-ben és az Azure AD-ben | Microsoft Docs'
description: Ismerje meg, hogyan konfigurálhatja a bejövő kiépítés SuccessFactors
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 01/19/2021
ms.author: chmutali
ms.openlocfilehash: 7b59e0ae2fbb73f341d5254fd2804d50ad141a19
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98953801"
---
# <a name="tutorial-configure-sap-successfactors-to-active-directory-user-provisioning"></a>Oktatóanyag: SAP-SuccessFactors konfigurálása Active Directory felhasználó kiépítési felállításához 
Ennek az oktatóanyagnak a célja, hogy megmutassa azokat a lépéseket, amelyeket el kell végeznie ahhoz, hogy a felhasználók SuccessFactors az alkalmazotti központból a Active Directory (AD) és az Azure AD-ba, az e-mail-cím opcionális visszaírásával a SuccessFactors. 

>[!NOTE]
>Akkor használja ezt az oktatóanyagot, ha a SuccessFactors kiépíteni kívánt felhasználóknak helyszíni AD-fiókra és opcionálisan Azure AD-fiókra van szükségük. Ha a SuccessFactors felhasználóinak csak az Azure AD-fiókra van szükségük (csak felhőalapú felhasználók), tekintse meg az [SAP SuccessFactors konfigurálása az Azure ad](sap-successfactors-inbound-provisioning-cloud-only-tutorial.md) -beli felhasználók számára című oktatóanyagot. 


## <a name="overview"></a>Áttekintés

A [Azure Active Directory felhasználó-kiépítési szolgáltatás](../app-provisioning/user-provisioning.md) integrálva van a [SuccessFactors Employee Central](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) szolgáltatással a felhasználók identitási életciklusának kezelése érdekében. 

Az Azure AD-alapú felhasználó-kiépítési szolgáltatás által támogatott SuccessFactors-kiépítési munkafolyamatok lehetővé teszik a következő emberi erőforrások és az identitás-életciklus felügyeleti forgatókönyvek automatizálását:

* **Új alkalmazottak felvétele** – ha új alkalmazottat ad hozzá a SuccessFactors-hoz, a rendszer automatikusan létrehoz egy felhasználói fiókot Active Directory, Azure Active Directory és opcionálisan Microsoft 365 és más, az [Azure ad által támogatott SaaS-alkalmazásokban](../app-provisioning/user-provisioning.md), az e-mail-cím SuccessFactors való visszaírásával.

* **Alkalmazotti attribútumok és profilok frissítései** – ha egy alkalmazotti rekord frissül a SuccessFactors (például a nevük, a cím vagy a felettes), a felhasználói fiókja automatikusan frissül Active Directory, Azure Active Directory, és opcionálisan Microsoft 365 és [más, az Azure ad által támogatott SaaS-alkalmazásokkal](../app-provisioning/user-provisioning.md).

* **Alkalmazotti megszakítások** – ha egy alkalmazott leáll a SuccessFactors-ben, a felhasználói fiókja automatikusan le lesz tiltva Active Directory, Azure Active Directory és opcionálisan Microsoft 365 és [más, az Azure ad által támogatott SaaS-alkalmazásokban](../app-provisioning/user-provisioning.md).

* **Alkalmazottak** újratelepítése – ha egy alkalmazottat a SuccessFactors-ben bérelnek újra, a régi fiókjuk automatikusan újraaktiválható vagy újraépíthető (az Ön igényeitől függően), hogy Active Directory, Azure Active Directory, és opcionálisan Microsoft 365 és [más, az Azure ad által támogatott SaaS-alkalmazásokat](../app-provisioning/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Ki ez a felhasználó-kiépítési megoldás a legmegfelelőbb?

Ez a SuccessFactors Active Directory a felhasználói üzembe helyezési megoldáshoz ideális megoldást kínál a következőkhöz:

* Olyan szervezetek, amelyek előre elkészített, felhőalapú megoldást kívánnak a SuccessFactors-felhasználók üzembe helyezéséhez

* Azok a szervezetek, amelyekhez közvetlen felhasználó-kiépítés szükséges a SuccessFactors-ből Active Directory

* Azok a szervezetek, amelyek megkövetelik, hogy a felhasználók kiépítsék a [SuccessFactors Employee Central (ek)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html) által beszerzett adatok alapján

* Azok a szervezetek, amelyeknek a felhasználóknak csatlakozniuk kell, át kell helyezniük, és el kell hagyniuk a felhasználókat, hogy szinkronizálva legyenek egy vagy több Active Directory erdőben, tartományban és szervezeti egységben, csak a SuccessFactors-ben [(EC)](https://www.successfactors.com/products-services/core-hr-payroll/employee-central.html)

* Az e-maileket Microsoft 365 használó szervezetek

## <a name="solution-architecture"></a>Megoldásarchitektúra

Ez a szakasz a közös hibrid környezetek teljes körű felhasználói üzembe helyezési megoldásának architektúráját ismerteti. Két kapcsolódó folyamat létezik:

* **Mérvadó HR-adatfolyam – a SuccessFactors és a helyszíni Active Directory között:** Ebben a folyamatban lévő feldolgozói eseményekben (például új bérletek, átvitelek, megszakítások) először a Cloud SuccessFactors Employee Central-ban fordulnak elő, majd az események az Azure AD-n és a kiépítési ügynökön keresztül kerülnek a helyszíni Active Directoryba. Az eseménytől függően előfordulhat, hogy az AD-ben létrehoz/frissít/engedélyez/letilt műveleteket.
* **E-mail-visszaírási folyamat – helyszíni Active Directoryról SuccessFactors:** Ha a fiók létrehozása befejeződött a Active Directoryban, az Azure AD-val szinkronizálva Azure AD Connect Sync és e-mail-attribútummal visszaírhatók a SuccessFactors.

  ![Áttekintés](./media/sap-successfactors-inbound-provisioning/sf2ad-overview.png)

### <a name="end-to-end-user-data-flow"></a>Végpontok közötti felhasználói adatfolyam

1. A HR-csapat munkavégző tranzakciókat (összekötőket/Mozgatókat/kimaradt vagy új bérleteket/átviteleket/megszakításokat) végez a SuccessFactors Employee Centralban
2. Az Azure AD-kiépítési szolgáltatás futtatja az identitások ütemezett szinkronizálását az SuccessFactors-től, és azonosítja azokat a módosításokat, amelyeket fel kell dolgozni a helyszíni Active Directoryval való szinkronizáláshoz.
3. Az Azure AD-kiépítési szolgáltatás meghívja a helyszíni Azure AD Connect kiépítési ügynököt az AD-fiók létrehozási/frissítési/engedélyezési/letiltási műveleteit tartalmazó kérelem-adattartalommal.
4. Az Azure AD Connect-kiépítési ügynök egy szolgáltatásfiókot használ az AD-fiókadatok hozzáadásához/frissítéséhez.
5. A Azure AD Connect szinkronizálási motorja a különbözeti szinkronizálást futtatja a frissítések AD-ben való lekéréséhez.
6. A Active Directory frissítések szinkronizálva vannak a Azure Active Directoryval.
7. Ha a [SuccessFactors visszaírási-alkalmazás](sap-successfactors-writeback-tutorial.md) konfigurálva van, az e-mail-attribútumot a SuccessFactors értékre írja, a használt egyező attribútum alapján.

## <a name="planning-your-deployment"></a>Az üzembe helyezés megtervezése

A Felhőbeli HR-alapú felhasználóknak a SuccessFactors-ből az AD-be történő konfigurálásához jelentős tervezésre van szükség a különböző szempontokat illetően, például:
* Az Azure AD Connect létesítési ügynök telepítése 
* Az Active Directory-felhasználók üzembe helyezéséhez telepítendő SuccessFactors száma
* Egyező azonosító, attribútum-hozzárendelés, átalakítás és hatóköri szűrők

A témakör részletes útmutatását a [Cloud HR üzembe helyezési tervében](../app-provisioning/plan-cloud-hr-provision.md) találja. Tekintse meg az [SAP SuccessFactors-integrációs referenciáját](../app-provisioning/sap-successfactors-integration-reference.md) , ahol megismerheti a támogatott entitásokat, a feldolgozás részleteit és a különböző HR-forgatókönyvek integrálásának testreszabását. 

## <a name="configuring-successfactors-for-the-integration"></a>Az integráció SuccessFactors konfigurálása

A SuccessFactors-létesítési összekötők általános követelménye, hogy a SuccessFactors OData API-k meghívásához a megfelelő engedélyekkel rendelkező SuccessFactors-fiók hitelesítő adatait igénylik. Ez a szakasz a szolgáltatásfiók SuccessFactors-ben való létrehozásának és a megfelelő engedélyek megadásának lépéseit ismerteti. 

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
1. Görgessen le ugyanabban a mezőben, és válassza az **Employee Central API** elemet. Az alább látható engedélyek hozzáadásával olvassa el a ODATA API-t és a szerkesztést a ODATA API használatával. Válassza a szerkesztés lehetőséget, ha azt tervezi, hogy ugyanazt a fiókot használja a visszaírási SuccessFactors-forgatókönyvhöz. 
    > [!div class="mx-imgBorder"]
    > ![Írási engedélyek olvasása](./media/sap-successfactors-inbound-provisioning/odata-read-write-perm.png)

1. Ugyanezen engedélyek mezőben válassza a **felhasználói engedélyek – > alkalmazotti adatok** elemet, és tekintse át azokat az attribútumokat, amelyeket a szolgáltatásfiók a SuccessFactors-bérlőből tud olvasni. Ha például a SuccessFactors-ből szeretné lekérni a *username* attribútumot, győződjön meg arról, hogy a "View" engedély van megadva ehhez az attribútumhoz. Hasonlóan tekintse át az egyes attribútumokat a megtekintési engedélyekhez. 

    > [!div class="mx-imgBorder"]
    > ![Alkalmazotti adatkezelési engedélyek](./media/sap-successfactors-inbound-provisioning/review-employee-data-permissions.png)
   

    >[!NOTE]
    >A kiépítési alkalmazás által beolvasott attribútumok teljes listájáért tekintse meg a [SuccessFactors attribútum-referenciát](../app-provisioning/sap-successfactors-attribute-reference.md) .

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

## <a name="configuring-user-provisioning-from-successfactors-to-active-directory"></a>A felhasználók üzembe helyezésének konfigurálása a SuccessFactors-ből Active Directory

Ez a szakasz a felhasználói fiókok kiépítésének lépéseit ismerteti a SuccessFactors minden Active Directory tartományba az integráció hatókörén belül.

* [Adja hozzá a kiépítési összekötő alkalmazást, és töltse le a kiépítési ügynököt.](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent)
* [Helyszíni kiépítési ügynök (ek) telepítése és konfigurálása](#part-2-install-and-configure-on-premises-provisioning-agents)
* [A SuccessFactors és a Active Directory kapcsolatának konfigurálása](#part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory)
* [Attribútumok leképezésének konfigurálása](#part-4-configure-attribute-mappings)
* [A felhasználók üzembe helyezésének engedélyezése és elindítása](#enable-and-launch-user-provisioning)

### <a name="part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent"></a>1. rész: a kiépítési összekötő alkalmazás hozzáadása és a kiépítési ügynök letöltése

**A SuccessFactors konfigurálása Active Directory kiépítés esetén:**

1. Nyissa meg a következőt: <https://portal.azure.com>

2. A bal oldali navigációs sávon válassza a **Azure Active Directory**

3. Válassza a **vállalati alkalmazások**, majd **az összes alkalmazás** lehetőséget.

4. Válassza az **alkalmazás hozzáadása** lehetőséget, és válassza az **összes** kategóriát.

5. Keresse meg a **SuccessFactors Active Directory a felhasználók üzembe** helyezését, és adja hozzá az alkalmazást a katalógusból.

6. Miután hozzáadta az alkalmazást, és megjelenik az alkalmazás részletei képernyő, válassza a **kiépítés** lehetőséget.

7. A **kiépítési** **mód** módosítása **automatikusra**

8. A kiépítési ügynök letöltéséhez kattintson a megjelenő információs szalagcímre. 
   >[!div class="mx-imgBorder"]
   >![Ügynök letöltése](./media/workday-inbound-tutorial/pa-download-agent.png "Ügynök letöltése képernyő")

### <a name="part-2-install-and-configure-on-premises-provisioning-agents"></a>2. rész: a helyszíni kiépítési ügynök (ek) telepítése és konfigurálása

A helyszíni Active Directory való kiépítéshez a létesítési ügynököt olyan tartományhoz csatlakoztatott kiszolgálóra kell telepíteni, amely hálózati hozzáféréssel rendelkezik a kívánt Active Directory tartomány (ok) hoz.

Vigye át a letöltött ügynök telepítőjét a kiszolgálói gazdagépre, és kövesse az ügynök [telepítése című szakaszban](../cloud-sync/how-to-install.md) felsorolt lépéseket az ügynök konfigurálásának befejezéséhez.

### <a name="part-3-in-the-provisioning-app-configure-connectivity-to-successfactors-and-active-directory"></a>3. rész: a kiépítési alkalmazásban konfigurálja a SuccessFactors-hez és a Active Directoryhoz való kapcsolódást.
Ebben a lépésben kapcsolatot létesít a SuccessFactors és a Active Directoryekkel a Azure Portal. 

1. A Azure Portal lépjen vissza a SuccessFactors az [1. részben](#part-1-add-the-provisioning-connector-app-and-download-the-provisioning-agent) létrehozott felhasználói üzembe helyezési alkalmazás Active Directoryához.

1. Fejezze be a **rendszergazdai hitelesítő adatok** szakaszt a következőképpen:

   * **Rendszergazdai Felhasználónév** – adja meg a SuccessFactors API felhasználói fiókjának felhasználónevét a HOZZÁFŰZÖTT vállalati azonosítóval. Formátuma: **username \@ companyID**

   * **Rendszergazdai jelszó –** Adja meg a SuccessFactors API felhasználói fiók jelszavát. 

   * **Bérlői URL-cím –** Adja meg a SuccessFactors OData API Services-végpont nevét. Csak a kiszolgáló állomásnevét adja meg http vagy https nélkül. Ennek az értéknek a következőhöz hasonlóan kell kinéznie: **<API-Server-name>. SuccessFactors.com**.

   * **Active Directory erdő –** A Active Directory tartományának neve, az ügynökkel való regisztrálással. A legördülő listából válassza ki a kiépítés célját. Ez az érték általában karakterlánc, például: *contoso.com*

   * **Active Directory tároló –** Adja meg a tároló DN-t, amelyben az ügynöknek alapértelmezés szerint létre kell hoznia a felhasználói fiókokat.
        Példa: *ou = Users, DC = contoso, DC = com*
        > [!NOTE]
        > Ez a beállítás csak akkor kerül lejátszásra a felhasználói fiókok létrehozásához, ha a *parentDistinguishedName* attribútum nincs konfigurálva az attribútum-hozzárendelésekben. Ez a beállítás nem használatos a felhasználói keresési vagy frissítési műveletekhez. A tartomány teljes alfájának a keresési művelet hatóköre esik.

   * **Értesítő e-mail –** Adja meg az e-mail-címét, és jelölje be az "e-mail küldése, ha hiba történik" jelölőnégyzetet.
     > [!NOTE]
     > Az Azure AD kiépítési szolgáltatás e-mailes értesítést küld, ha a kiépítési feladatok [karantén](../app-provisioning/application-provisioning-quarantine-status.md) állapotba kerülnek.

   * Kattintson a **kapcsolatok tesztelése** gombra. Ha a kapcsolatok tesztelése sikeres, kattintson a felül található **Save (Mentés** ) gombra. Ha nem sikerül, ellenőrizze, hogy érvényesek-e a SuccessFactors hitelesítő adatai és az ügynök telepítésére konfigurált AD hitelesítő adatok.
     >[!div class="mx-imgBorder"]
     >![Azure Portal](./media/sap-successfactors-inbound-provisioning/sf2ad-provisioning-creds.png)

   * Miután a hitelesítő adatok mentése sikeresen megtörtént, a **leképezések** szakasz megjeleníti az alapértelmezett leképezést, **hogy szinkronizálja a SuccessFactors felhasználókat a helyszíni Active Directory**

### <a name="part-4-configure-attribute-mappings"></a>4. rész: attribútumok hozzárendelésének konfigurálása

Ebben a szakaszban azt fogja konfigurálni, hogy a felhasználói adatok hogyan áramlanak a SuccessFactors a Active Directoryba.

1. A kiépítés lapon a **leképezések** területen kattintson a **SuccessFactors-felhasználók szinkronizálása a helyszíni Active Directory** lehetőségre.

1. A **forrás objektum hatóköre** mezőben kiválaszthatja, hogy a SuccessFactors melyik felhasználói készletének hatóköre legyen az ad-hez való kiépítés az attribútum-alapú szűrők készletének definiálásával. Az alapértelmezett hatókör a "minden felhasználó a SuccessFactors". Példa szűrők:

   * Példa: hatókör a 1000000 és 2000000 közötti personIdExternal rendelkező felhasználók számára (kivéve 2000000)

      * Attribútum: personIdExternal

      * Operátor: REGEX egyezés

      * Érték: (1 [0-9] [0-9] [0-9] [0-9] [0-9] [0-9])

   * Példa: csak alkalmazottak és nem függő feldolgozók

      * Attribútum: Alkalmazottkód

      * Operátor: nem NULL

   > [!TIP]
   > Amikor első alkalommal konfigurálja a kiépítési alkalmazást, meg kell vizsgálnia és ellenőriznie kell az attribútumok hozzárendeléseit és kifejezéseit, hogy biztosan megadja a kívánt eredményt. A Microsoft azt javasolja, hogy a **forrás objektum hatókörében** lévő hatókör-szűrők használatával tesztelje a leképezéseket a SuccessFactors néhány tesztelési felhasználója között. Miután meggyőződött arról, hogy a leképezések működnek, távolítsa el a szűrőt, vagy fokozatosan bontsa ki, hogy több felhasználót is tartalmazzon.

   > [!CAUTION] 
   > A kiépítési motor alapértelmezett viselkedése, hogy letiltsa/törölje a hatókörön kívüli felhasználókat. Előfordulhat, hogy a SuccessFactors az AD-integrációhoz nem lehet szükség. Az alapértelmezett viselkedés felülbírálásához tekintse meg a [Hatókörön kívüli felhasználói fiókok törlésének kihagyása](../app-provisioning/skip-out-of-scope-deletions.md) című cikket.
  
1. A **cél objektum műveletek** mezőben globálisan szűrheti, hogy a Active Directory milyen műveleteket hajtson végre. A **Létrehozás** és a **frissítés** a leggyakoribb.

1. Az **attribútum-hozzárendelések** szakaszban megadhatja, hogy az egyes SuccessFactors attribútumok hogyan képezhetők le Active Directory attribútumokra.

     >[!NOTE]
     >Az alkalmazás által támogatott SuccessFactors-attribútum teljes listájáért tekintse meg a [SuccessFactors attribútum referenciáját](../app-provisioning/sap-successfactors-attribute-reference.md) .

1. Kattintson egy meglévő attribútum-hozzárendelésre a frissítéséhez, vagy kattintson az **Új leképezés hozzáadása** lehetőségre a képernyő alján új hozzárendelések hozzáadásához. Az egyes attribútumok megfeleltetése a következő tulajdonságokat támogatja:

      * **Leképezés típusa**

         * **Direct** – a SuccessFactors attribútum értékét írja a ad attribútumba, módosítás nélkül

         * **Konstans** – statikus, állandó karakterlánc-érték írása az ad-attribútumba

         * **Kifejezés** – lehetővé teszi egyéni érték írását az ad-attribútumnak egy vagy több SuccessFactors-attribútum alapján. [További információkért tekintse meg ezt a cikket a kifejezésekkel kapcsolatban](../app-provisioning/functions-for-customizing-application-data.md).

      * **Forrásoldali attribútum** – a felhasználó attribútuma a SuccessFactors

      * **Alapértelmezett érték** – nem kötelező. Ha a forrás attribútum üres értékkel rendelkezik, a leképezés Ehelyett ezt az értéket fogja írni.
            A leggyakoribb konfiguráció az, hogy ezt üresen hagyja.

      * **Target attribútum** – a felhasználó attribútuma Active Directoryban.

      * **Objektumok egyeztetése ezzel az attribútummal** – függetlenül attól, hogy ezt a leképezést kell-e használni a felhasználók egyedi azonosításához a SuccessFactors és a Active Directory között. Ez az érték általában a SuccessFactors Worker ID mezőjére van beállítva, amely általában a Active Directory egyik alkalmazotti azonosító attribútumára van leképezve.

      * **Megfeleltetési prioritás** – a rendszer több egyező attribútumot is beállíthat. Ha több is van, a rendszer a mező által meghatározott sorrendben értékeli ki őket. Amint talál egyezést, nem lesz kiértékelve további egyező attribútumok.

      * **A leképezés alkalmazása**

         * **Mindig** – alkalmazza ezt a leképezést a felhasználói létrehozási és frissítési műveletekre is

         * **Csak a létrehozás során** – alkalmazza ezt a leképezést kizárólag felhasználói létrehozási műveletekre

1. A leképezések mentéséhez kattintson a Attribute-Mapping szakasz tetején található **Mentés** gombra.

Ha elkészült az attribútum-hozzárendelési konfigurációval, az [igény szerinti](../app-provisioning/provision-on-demand.md) kiépítés használatával tesztelheti az üzembe [helyezést, majd engedélyezheti és elindíthatja a felhasználó kiépítési szolgáltatását](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>A felhasználók üzembe helyezésének engedélyezése és elindítása

Miután befejeződött az SuccessFactors-létesítési alkalmazás konfigurációja, és egy [igény szerinti](../app-provisioning/provision-on-demand.md)kiépítéssel rendelkező egyetlen felhasználó számára ellenőrizte az üzembe helyezést, bekapcsolhatja a kiépítési szolgáltatást a Azure Portal.

> [!TIP]
> Alapértelmezés szerint a kiépítési szolgáltatás bekapcsolásakor a rendszer kiépítési műveleteket kezdeményez a hatókörben lévő összes felhasználó számára. Ha a leképezési vagy SuccessFactors adatokkal kapcsolatos hibák léptek fel, akkor előfordulhat, hogy a kiépítési feladat meghiúsul, és a karanténba helyezési állapotba lép. Ennek elkerüléséhez ajánlott eljárásként Azt javasoljuk, hogy a **forrás objektum hatókör** -szűrőjét konfigurálja, és tesztelje az attribútum-hozzárendeléseket néhány, [igény szerinti](../app-provisioning/provision-on-demand.md) kiosztást használó tesztelési felhasználóval, mielőtt elindítja a teljes szinkronizálást az összes felhasználó számára. Miután meggyőződött arról, hogy a leképezések működnek, és megadja a kívánt eredményeket, távolítsa el a szűrőt, vagy fokozatosan bontsa ki, hogy több felhasználót is tartalmazzon.

1. Lépjen a **kiépítés** panelre, és kattintson a **kiépítés megkezdése** elemre.

1. Ez a művelet elindítja a kezdeti szinkronizálást, amely a SuccessFactors-bérlőben lévő felhasználók számától függően több órát is igénybe vehet. A folyamatjelző sáv a szinkronizálási ciklus előrehaladásának nyomon követésére használható. 

1. A Azure Portal **naplók** lapján bármikor megtekintheti a kiépítési szolgáltatás által végrehajtott műveleteket. A naplók a kiépítési szolgáltatás által végrehajtott összes egyéni szinkronizálási eseményt felsorolják, például hogy mely felhasználók olvasnak el SuccessFactors, majd később hozzáadják vagy frissítették Active Directory. 

1. A kezdeti szinkronizálás befejezésekor a rendszer egy naplózási összesítő jelentést ír a **létesítés** lapon az alább látható módon.

   > [!div class="mx-imgBorder"]
   > ![Kiépítés folyamatjelző sáv](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Következő lépések

* [További információ a bejövő kiépítés támogatott SuccessFactors attribútumairól](../app-provisioning/sap-successfactors-attribute-reference.md)
* [Ismerje meg, hogyan konfigurálhatja az e-mailek visszaírási a SuccessFactors](sap-successfactors-writeback-tutorial.md)
* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)
* [Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a SuccessFactors és a Azure Active Directory között](successfactors-tutorial.md)
* [Ismerje meg, hogyan integrálhat más SaaS-alkalmazásokat a Azure Active Directory](tutorial-list.md)
* [Útmutató a kiépítési konfigurációk exportálásához és importálásához](../app-provisioning/export-import-provisioning-configuration.md)