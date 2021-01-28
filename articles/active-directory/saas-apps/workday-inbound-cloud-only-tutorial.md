---
title: 'Oktatóanyag: a munkanap bejövő kiépítés konfigurálása a Azure Active Directoryban | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja a beérkező kiépítés a munkanapokból az Azure AD-be
services: active-directory
author: cmmdesai
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.topic: tutorial
ms.workload: identity
ms.date: 05/26/2020
ms.author: chmutali
ms.openlocfilehash: ef4381f305292b366348aa3729209dc3f5e8c87b
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954089"
---
# <a name="tutorial-configure-workday-to-azure-ad-user-provisioning"></a>Oktatóanyag: munkanapok konfigurálása az Azure AD-beli felhasználók üzembe helyezéséhez
Ennek az oktatóanyagnak a célja, hogy megmutassa a munkavégző adatok munkanapokból Azure Active Directoryba való kiépítéséhez szükséges lépéseket. 

>[!NOTE]
>Akkor használja ezt az oktatóanyagot, ha a munkahelyről kiépíteni kívánt felhasználók csak olyan felhőalapú felhasználók, akiknek nincs szükségük helyszíni AD-fiókra. Ha a felhasználók csak helyszíni AD-fiókot vagy AD-és Azure AD-fiókot igényelnek, tekintse meg a [munkanap konfigurálása Active Directory](workday-inbound-tutorial.md) a felhasználók üzembe helyezése című oktatóanyagot. 

## <a name="overview"></a>Áttekintés

A felhasználói fiókok kiépítéséhez az [Azure Active Directory felhasználó-kiépítési szolgáltatás](../app-provisioning/user-provisioning.md) integrálva van a [MUNKANAP emberi erőforrások API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) -val. Az Azure AD-alapú felhasználó-kiépítési szolgáltatás által támogatott munkafolyamatok munkafolyamatai lehetővé teszik a következő emberi erőforrások és az identitás-életciklus felügyeleti forgatókönyvek automatizálását:

* **Új alkalmazottak felvétele** – új alkalmazottak munkanapokhoz való hozzáadásakor a rendszer automatikusan létrehoz egy felhasználói fiókot Azure Active Directory és opcionálisan Microsoft 365 és az [Azure ad által támogatott egyéb SaaS-alkalmazásokban](../app-provisioning/user-provisioning.md), és az e-mail-címet a munkanapokra írja vissza.

* **Alkalmazotti attribútumok és profilok frissítései** – ha egy alkalmazotti rekordot (például a nevét, címét vagy felettesét) frissíti, a felhasználói fiókja automatikusan frissül Azure Active Directory és opcionálisan Microsoft 365 és [Az Azure ad által támogatott egyéb SaaS-alkalmazások](../app-provisioning/user-provisioning.md)esetében is.

* **Alkalmazotti megszakítások** – ha egy alkalmazott munkanapokon leáll, a felhasználói fiókja automatikusan le van tiltva Azure Active Directory és opcionálisan Microsoft 365 és [Az Azure ad által támogatott egyéb SaaS-alkalmazások](../app-provisioning/user-provisioning.md)esetében.

* **Alkalmazottak** újratelepítése – ha egy alkalmazottat munkanapokon helyeztek át újra, a régi fiók automatikusan újraaktiválható vagy újra kiépíthető (az Ön igényeitől függően), hogy Azure Active Directory és opcionálisan Microsoft 365 és [más, az Azure ad által támogatott SaaS-alkalmazásokat](../app-provisioning/user-provisioning.md).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Ki ez a felhasználó-kiépítési megoldás a legmegfelelőbb?

Ez a munkanap Azure Active Directory a felhasználói üzembe helyezési megoldáshoz ideális megoldást kínál a következőkhöz:

* Olyan szervezetek, amelyek előre elkészített, felhőalapú megoldást kívánnak a munkanapokon történő felhasználói kiépítéshez

* Azok a szervezetek, amelyekhez közvetlen felhasználó-kiépítés szükséges a munkanapokról Azure Active Directory

* Azok a szervezetek, amelyeknek a felhasználók számára a munkanapokból beszerzett adatok használatával kell kiépíteni a felhasználókat

* Az e-maileket Microsoft 365 használó szervezetek

## <a name="solution-architecture"></a>Megoldásarchitektúra

Ez a szakasz a teljes körű felhasználói üzembe helyezési megoldás architektúráját ismerteti a csak felhőalapú felhasználók számára. Két kapcsolódó folyamat létezik:

* **Mérvadó HR-adatfolyam – a munkanapoktól a Azure Active Directoryig:** Ebben a folyamatban lévő feldolgozói eseményekben (például az új bérletek, az átvitelek, a megszakítások) először a munkanapokon fordulnak elő, és az esemény-adatforgalom Azure Active Directoryba kerül. Az eseménytől függően előfordulhat, hogy az Azure AD-ban létrehoz/frissít/engedélyez/letilt műveleteket.
* **Visszaírási flow – helyszíni Active Directoryról munkanapokra:** Ha a fiók létrehozása befejeződött a Active Directoryban, az Azure AD-vel való szinkronizálása Azure AD Connect és információk, például az e-mailek, a Felhasználónév és a telefonszám is visszaírható a munkanapokra.

  ![Áttekintés](./media/workday-inbound-tutorial/workday-cloud-only-provisioning.png)

### <a name="end-to-end-user-data-flow"></a>Végpontok közötti felhasználói adatfolyam

1. A HR-csapat munkavégző tranzakciókat hajt végre (összekötők/mozgatók/kimaradók vagy új bérletek/átvitelek/megszakítások) a munkanap alkalmazott középen
2. Az Azure AD-kiépítési szolgáltatás futtatja az identitások ütemezett szinkronizálását a munkanapokból az EK-ból, és azonosítja azokat a módosításokat, amelyeket a helyszíni Active Directorysal való szinkronizáláshoz kell feldolgozni.
3. Az Azure AD kiépítési szolgáltatás határozza meg a módosítást, és meghívja a felhasználó számára az Azure AD-ben a létrehozás/frissítés/engedélyezés/letiltás műveletet.
4. Ha a [munkanap visszaírási](workday-writeback-tutorial.md) -alkalmazás konfigurálva van, a rendszer lekéri az attribútumokat, például az e-maileket, a felhasználónevet és a telefonszámot az Azure ad-ből 
5. Az Azure AD-létesítési szolgáltatás az e-mail-címet, a felhasználónevet és a telefonszámot munkanapokon állítja be

## <a name="planning-your-deployment"></a>Az üzembe helyezés megtervezése

A Felhőbeli HR-alapú felhasználóknak a munkahelyről az Azure AD-be történő konfigurálásához jelentős tervezés szükséges a különböző szempontokat, például az alábbiakat:

* A megfelelő azonosító meghatározása 
* Attribútumleképezés
* Attribútumok átalakítása 
* Hatókörszűrők

A témakör részletes útmutatását a [Cloud HR üzembe helyezési tervében](../app-provisioning/plan-cloud-hr-provision.md) találja. 

## <a name="configure-integration-system-user-in-workday"></a>Integrációs rendszer felhasználójának konfigurálása munkanapokon

Tekintse meg az [integrációs rendszer felhasználójának konfigurálása](workday-inbound-tutorial.md#configure-integration-system-user-in-workday) egy munkanap-integrációs rendszer felhasználói fiók létrehozásához engedéllyel a munkavégzők beolvasására vonatkozó engedélyeket. 

## <a name="configure-user-provisioning-from-workday-to-azure-ad"></a>A felhasználók üzembe helyezésének beállítása a munkanap és az Azure AD között

A következő szakaszok ismertetik a felhasználók kiépítésének a munkahelyről az Azure AD-be való konfigurálásának lépéseit a csak felhőalapú környezetekben.

* [Az Azure AD kiépítési összekötő alkalmazás hozzáadása és a munkanapokhoz való csatlakozás létrehozása](#part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday)
* [A munkanap és az Azure AD attribútum-hozzárendelések konfigurálása](#part-2-configure-workday-and-azure-ad-attribute-mappings)
* [A felhasználók üzembe helyezésének engedélyezése és elindítása](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>1. rész: az Azure AD-kiépítési összekötő alkalmazás hozzáadása és a munkanapokhoz való csatlakozás létrehozása

**Munkanapok konfigurálása a csak felhőalapú felhasználók Azure Active Directory kiépítés esetén:**

1. Nyissa meg a következőt: <https://portal.azure.com>.

2. A Azure Portal keresse meg és válassza a **Azure Active Directory** lehetőséget.

3. Válassza a **vállalati alkalmazások**, majd **az összes alkalmazás** lehetőséget.

4. Válassza az **alkalmazás hozzáadása** lehetőséget, majd válassza az **összes** kategóriát.

5. Keresse meg a **munkanapokat az Azure ad-beli felhasználói kiépítési** feladatokhoz, és adja hozzá az alkalmazást a katalógusból.

6. Miután hozzáadta az alkalmazást, és megjelenik az alkalmazás részletei képernyő, válassza a **kiépítés** lehetőséget.

7. Módosítsa a **kiépítési** **módot** **automatikusra**.

8. Fejezze be a **rendszergazdai hitelesítő adatok** szakaszt a következőképpen:

   * **Munkanap felhasználóneve** – adja meg a munkanap-integrációs rendszer fiókjának felhasználónevét a bérlői tartománynév hozzáfűzésével. Valahogy így kell kinéznie: username@contoso4

   * **Munkanap jelszava –** Adja meg a munkanap-integrációs rendszerfiók jelszavát

   * A **munkanap Web Services API URL-címe –** Adja meg a bérlőhöz tartozó munkanap webszolgáltatások végpontjának URL-címét. Az URL-cím határozza meg az összekötő által használt munkanap Web Services API verzióját. 
   
     | URL-formátum | WWS API-verzió használatban | XPATH-módosítások szükségesek |
     |------------|----------------------|------------------------|
     | https://####.workday.com/ccx/service/tenantName | v 21.1 | No |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources | v 21.1 | No |
     | https://####.workday.com/ccx/service/tenantName/Human_Resources/v##.# | v # #. # | Yes |

      > [!NOTE]
     > Ha nem ad meg verziószámot az URL-címben, az alkalmazás a munkanap webszolgáltatások (WWS) v 21.1 verzióját használja, és nincs szükség módosításra az alkalmazáshoz mellékelt alapértelmezett XPATH API-kifejezésekhez. Ha egy adott WWS API-verziót szeretne használni, az URL-címben válassza a verziószám értéket. <br>
     > Például: `https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources/v34.0` <br>
     > <br> Ha WWS API v 30.0 +-t használ, mielőtt bekapcsolja a létesítési feladatot, frissítse az **XPath API-kifejezéseket** az **attribútumok leképezése – > speciális beállítások –** a konfiguráció és a [munkanap-attribútum](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30) [kezelése](workday-inbound-tutorial.md#managing-your-configuration) című szakaszra hivatkozó munkaterülethez tartozó "> Edit attribútum-lista.  

   * **Értesítő e-mail –** Adja meg az e-mail-címét, és jelölje be az "e-mail küldése, ha hiba történik" jelölőnégyzetet.

   * Kattintson a **kapcsolatok tesztelése** gombra.

   * Ha a kapcsolatok tesztelése sikeres, kattintson a felül található **Save (Mentés** ) gombra. Ha nem sikerül, ellenőrizze, hogy a munkanap URL-címe és a hitelesítő adatok a munkanapokon érvényesek-e.

### <a name="part-2-configure-workday-and-azure-ad-attribute-mappings"></a>2. rész: a munkanap és az Azure AD attribútum-hozzárendelések konfigurálása

Ebben a szakaszban azt fogja konfigurálni, hogy a felhasználói adatok hogyan áramlanak a munkanapokból a csak felhőalapú felhasználók Azure Active Directory.

1. A **hozzárendelések** alatt a kiépítés lapon kattintson a **munkatársak szinkronizálása az Azure ad-be** lehetőségre.

2. A **forrás objektum hatóköre** mezőben kiválaszthatja, hogy a munkanapokon mely felhasználóknak kell szerepelniük az Azure ad-be való kiépítés hatókörében az attribútumok alapján létrehozott szűrők definiálásával. Az alapértelmezett hatókör a "minden felhasználó a munkanapokon". Példa szűrők:

   * Példa: hatókör azon felhasználók számára, akiknek a munkavégző azonosítója 1000000 és 2000000 között van

      * Attribútum: WorkerID

      * Operátor: REGEX egyezés

      * Érték: (1 [0-9] [0-9] [0-9] [0-9] [0-9] [0-9])

   * Példa: csak a feltételes feldolgozók és nem rendszeres alkalmazottak

      * Attribútum: ContingentID

      * Operátor: nem NULL

3. A **cél objektum műveletek** mezőben globálisan szűrheti, hogy milyen műveleteket hajtson végre az Azure ad-ben. A **Létrehozás** és a **frissítés** a leggyakoribb.

4. Az **attribútum-hozzárendelések** szakaszban megadhatja, hogy az egyes munkanapok attribútumai hogyan Active Directory attribútumaikat.

5. Kattintson egy meglévő attribútum-hozzárendelésre a frissítéséhez, vagy kattintson az **Új leképezés hozzáadása** lehetőségre a képernyő alján új hozzárendelések hozzáadásához. Az egyes attribútumok megfeleltetése a következő tulajdonságokat támogatja:

   * **Leképezés típusa**

      * **Direct** – a munkanap attribútum értékének beírása az ad attribútumba módosítás nélkül

      * **Konstans** – statikus, állandó karakterlánc-érték írása az ad-attribútumba

      * **Kifejezés** – lehetővé teszi egyéni érték írását az ad-attribútumnak egy vagy több munkanap-attribútum alapján. [További információkért tekintse meg ezt a cikket a kifejezésekkel kapcsolatban](../app-provisioning/functions-for-customizing-application-data.md).

   * **Forrásoldali attribútum** – a felhasználó attribútuma a munkanap alapján. Ha a keresett attribútum nem található, tekintse meg [a munkanapokat használó felhasználói attribútumok listájának testreszabása](workday-inbound-tutorial.md#customizing-the-list-of-workday-user-attributes)című témakört.

   * **Alapértelmezett érték** – nem kötelező. Ha a forrás attribútum üres értékkel rendelkezik, a leképezés Ehelyett ezt az értéket fogja írni.
            A leggyakoribb konfiguráció az, hogy ezt üresen hagyja.

   * **Target attribútum** – a User attribútum az Azure ad-ben.

   * **Objektumok egyeztetése ezzel az attribútummal** – függetlenül attól, hogy ezt az attribútumot kell-e használni a felhasználók munkanap és az Azure ad közötti egyedi azonosításához. Ez az érték általában a munkavégző azonosító mezőjére van beállítva, amely általában az Employee ID attribútum (új) vagy egy Extension attribútum az Azure AD-ben van leképezve.

   * **Megfeleltetési prioritás** – a rendszer több egyező attribútumot is beállíthat. Ha több is van, a rendszer a mező által meghatározott sorrendben értékeli ki őket. Amint talál egyezést, nem lesz kiértékelve további egyező attribútumok.

   * **A leképezés alkalmazása**

     * **Mindig** – alkalmazza ezt a leképezést a felhasználói létrehozási és frissítési műveletekre is

     * **Csak a létrehozás során** – alkalmazza ezt a leképezést kizárólag felhasználói létrehozási műveletekre

6. A leképezések mentéséhez kattintson a Attribute-Mapping szakasz tetején található **Mentés** gombra.


## <a name="enable-and-launch-user-provisioning"></a>A felhasználók üzembe helyezésének engedélyezése és elindítása

Miután befejezte a munkaidő-kiépítési alkalmazás konfigurációját, bekapcsolhatja a kiépítési szolgáltatást a Azure Portal.

> [!TIP]
> Alapértelmezés szerint a kiépítési szolgáltatás bekapcsolásakor a rendszer kiépítési műveleteket kezdeményez a hatókörben lévő összes felhasználó számára. Ha hibák léptek fel a leképezési vagy a munkanapokon tárolt adatokkal kapcsolatban, a kiépítési feladat meghiúsulhat, és a karanténba helyezési állapotba kerülhet. Ennek elkerüléséhez ajánlott eljárásként Azt javasoljuk, hogy a **forrás objektum hatókör** -szűrőjét konfigurálja, és tesztelje az attribútumok hozzárendeléseit néhány tesztelési felhasználóval, mielőtt elindítja a teljes szinkronizálást az összes felhasználó számára. Miután meggyőződött arról, hogy a leképezések működnek, és megadja a kívánt eredményeket, távolítsa el a szűrőt, vagy fokozatosan bontsa ki, hogy több felhasználót is tartalmazzon.

1. A **létesítés** lapon állítsa be a **kiépítési állapotot** **a** következőre:.

2. Kattintson a **Mentés** gombra.

3. Ez a művelet elindítja a kezdeti szinkronizálást, amely a munkanapokhoz tartozó bérlők számától függően több órát is igénybe vehet. A folyamatjelző sáv a szinkronizálási ciklus előrehaladásának nyomon követésére használható. 

4. A Azure Portal **naplók** lapján bármikor megtekintheti a kiépítési szolgáltatás által végrehajtott műveleteket. A naplók a kiépítési szolgáltatás által végrehajtott összes egyéni szinkronizálási eseményt felsorolják, például hogy mely felhasználók olvasnak el munkanapokon, majd ezt követően hozzáadják vagy frissítették Azure Active Directory. 

5. A kezdeti szinkronizálás befejezésekor a rendszer egy naplózási összesítő jelentést ír a **létesítés** lapon az alább látható módon.

   > [!div class="mx-imgBorder"]
   > ![Kiépítés folyamatjelző sáv](./media/sap-successfactors-inbound-provisioning/prov-progress-bar-stats.png)

## <a name="next-steps"></a>Következő lépések

* [További információ az Azure AD és a munkanap-integrációs forgatókönyvekről és a webszolgáltatás-hívásokról](../app-provisioning/workday-integration-reference.md)
* [További információ a bejövő kiépítés támogatott munkanapokhoz tartozó attribútumairól](../app-provisioning/workday-attribute-reference.md)
* [Tudnivalók a munkanap visszaírási konfigurálásáról](workday-writeback-tutorial.md)
* [Tudnivalók a naplók áttekintéséről és az átadási tevékenységekkel kapcsolatos jelentések lekéréséről](../app-provisioning/check-status-user-account-provisioning.md)
* [Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a munkanap és a Azure Active Directory között](workday-tutorial.md)
* [Útmutató a kiépítési konfigurációk exportálásához és importálásához](../app-provisioning/export-import-provisioning-configuration.md)


