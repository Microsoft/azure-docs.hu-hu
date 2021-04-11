---
title: Alkalmazás-hitelesítés migrálása Azure Active Directoryre
description: Ez a tanulmány részletesen ismerteti az alkalmazás-hitelesítés Azure AD-be történő áttelepítésének megtervezését és előnyeit.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 02/05/2021
ms.author: kenwith
ms.reviewer: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1e05a7af3f0b95470432b4fb9602e1b41da9f72f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104952966"
---
# <a name="migrate-application-authentication-to-azure-active-directory"></a>Alkalmazás-hitelesítés migrálása Azure Active Directoryre

## <a name="about-this-paper"></a>A tanulmányról

Ez a tanulmány részletesen ismerteti az alkalmazás-hitelesítés Azure AD-be történő áttelepítésének megtervezését és előnyeit. Az Azure-rendszergazdák és az identitás-szakemberek számára készült.

A folyamat négy szakaszra bontva, amelyek mindegyike részletes tervezési és kilépési feltételekkel rendelkezik, úgy lett kialakítva, hogy megkönnyítse az áttelepítési stratégia megtervezését, és hogy az Azure AD-hitelesítés hogyan támogatja a szervezeti célokat

## <a name="introduction"></a>Bevezetés

Napjainkban a szervezete a felhasználók számára a munkához szükséges alkalmazások (alkalmazások) megszerzését igényli. Minden nap valószínűleg továbbra is hozzáadhatja, fejlesztheti vagy kivonja az alkalmazásokat. A felhasználók a vállalati és a személyes eszközök, illetve a helyszínek széles köréből férhetnek hozzá ezekhez az alkalmazásokhoz. Számos módon nyitják meg az alkalmazásokat, beleértve a következőket:

- vállalati Kezdőlap vagy portál

- a böngészőben való könyvjelzők alapján

- a gyártó által szolgáltatott (SaaS) alkalmazásokhoz tartozó URL-cím használatával

- egy mobileszköz/alkalmazás-felügyeleti (MDM/MAM) megoldáson keresztül közvetlenül a felhasználó asztali vagy mobil eszközeire leküldett hivatkozások

Alkalmazásai valószínűleg a következő típusú hitelesítést használják:

- Helyszíni összevonási megoldások (például Active Directory összevonási szolgáltatások (AD FS) (ADFS) és ping)

- Active Directory (például Kerberos-hitelesítés és Windows-Integrated hitelesítés)

- Más felhőalapú identitás-és hozzáférés-kezelési (IAM) megoldások (például okta vagy Oracle)

- Helyszíni webes infrastruktúra (például IIS és Apache)

- Felhőben üzemeltetett infrastruktúra (például Azure és AWS)

**Annak érdekében, hogy a felhasználók könnyen és biztonságosan hozzáférhessenek az alkalmazásokhoz, a cél az, hogy egyetlen hozzáférés-vezérlési és-szabályzatot hozzon létre a helyszíni és a felhőalapú környezetekben.**

A [Azure Active Directory (Azure ad)](../fundamentals/active-directory-whatis.md) univerzális Identity platformot kínál, amely lehetővé teszi, hogy a felhasználók, partnerek és ügyfelek egyetlen identitással férhessenek hozzá a kívánt alkalmazásokhoz, és működjenek együtt bármely platformról és eszközről.

![Azure Active Directory kapcsolat diagramja](media/migrating-application-authentication-to-azure-active-directory-1.jpg)

Az Azure AD [teljes körű Identitáskezelés-kezelési képességekkel](../fundamentals/active-directory-whatis.md#which-features-work-in-azure-ad)rendelkezik. Az alkalmazás-hitelesítés és az Azure AD-engedélyezés egységesítése lehetővé teszi, hogy ezek a képességek milyen előnyöket biztosítanak.

További áttelepítési erőforrások a következő helyen találhatók: [https://aka.ms/migrateapps](./migration-resources.md)

## <a name="benefits-of-migrating-app-authentication-to-azure-ad"></a>Az alkalmazások hitelesítésének az Azure AD-be való áttelepítésének előnyei

Az alkalmazás-hitelesítés Azure AD-be való áthelyezésével kezelheti a kockázatkezelést és a költségeket, növelheti a hatékonyságot, valamint a megfelelőségi és irányítási követelményeket.

### <a name="manage-risk"></a>A kockázatok kezelése

Az alkalmazások védelme megköveteli, hogy teljes mértékben megtekinthesse az összes kockázati tényezőt. Az alkalmazások Azure AD-be való áttelepítése konszolidálja biztonsági megoldásait. Ezzel a következőket teheti:

- Az alkalmazásokhoz és a kapcsolódó vállalati adatokhoz való biztonságos hozzáférés javítása [feltételes hozzáférési szabályzatok](../conditional-access/overview.md), [multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)és valós idejű kockázatkezelési [technológiák használatával](../identity-protection/overview-identity-protection.md) .

- A rendszerjogosultságú felhasználó hozzáférésének biztosítása a környezethez [az igény szerinti rendszergazdai](../../azure-resource-manager/managed-applications/request-just-in-time-access.md) hozzáféréssel.

- Használja az [Azure ad több-bérlős, földrajzilag elosztott, magas rendelkezésre állású kialakítását](https://cloudblogs.microsoft.com/enterprisemobility/2014/09/02/azure-ad-under-the-hood-of-our-geo-redundant-highly-available-distributed-cloud-directory/)a legfontosabb üzleti igényeknek megfelelően.

- Az Ön által már telepített [biztonságos hibrid hozzáférési partner-integrációk](https://aka.ms/secure-hybrid-access) egyikével védheti meg korábbi alkalmazásait.

### <a name="manage-cost"></a>A Cost kezelése

Előfordulhat, hogy a szervezete több Identity Access-felügyeleti (IAM) megoldással rendelkezik. Az egyik Azure AD-infrastruktúrába való Migrálás lehetősége van arra, hogy csökkentse az IAM-licencek (helyszíni vagy Felhőbeli) és az infrastrukturális költségek függőségeit. Azokban az esetekben, amikor az Azure AD-t a Microsoft 365 licencek keretében már kifizette, nem kell fizetnie egy másik IAM megoldás hozzáadott költségeiért.

**Az Azure AD-vel csökkentheti az infrastrukturális költségeket:**

- Biztonságos távoli hozzáférés biztosítása helyszíni alkalmazásokhoz az [Azure ad Application proxy](./application-proxy.md)használatával.

- Az alkalmazások leválasztása a helyszíni hitelesítő adatokról a bérlőn az [Azure ad-t megbízható univerzális identitás-szolgáltatóként állítja](../hybrid/plan-connect-user-signin.md#choosing-the-user-sign-in-method-for-your-organization)be.

### <a name="increase-productivity"></a>Termelékenység növelése

A gazdaságosság és a biztonság előnye, hogy a szervezetek az Azure AD-t fogadják el, de a teljes bevezetést és megfelelőséget nagyobb valószínűséggel lehet használni. Az Azure AD-vel a következőket teheti:

- A végfelhasználói [egyszeri Sign-On (SSO)](./what-is-single-sign-on.md) felhasználói élményének javítása bármely alkalmazáshoz, bármely eszközről és helyről való zökkenőmentes és biztonságos hozzáférés révén.

- Önkiszolgáló IAM-képességeket használhat, mint például az önkiszolgáló [jelszavak alaphelyzetbe állítása](../authentication/concept-sspr-howitworks.md) és a [SelfService](../enterprise-users/groups-self-service-management.md).

- Csökkentse az adminisztrációs terhelést úgy, hogy csak egyetlen identitást kezel az egyes felhasználók számára a felhőben és a helyszíni környezetekben:

  - Felhasználói fiókok üzembe helyezésének [automatizálása](../app-provisioning/user-provisioning.md) az Azure [ad-](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)identitások alapján
  - Az összes alkalmazás elérése a [Azure Portal](https://portal.azure.com/) MyApps paneljéről

- Lehetővé teheti a fejlesztők számára, hogy hozzáférjenek az alkalmazásaihoz, és javítsák a végfelhasználói élményt a Microsoft [Identity platform](../develop/v2-overview.md) és a Microsoft Authentication Library (MSAL) használatával.

- A Felhőbeli erőforrásokhoz való hozzáférést az [Azure ad B2B együttműködés](../external-identities/what-is-b2b.md)segítségével teheti elérhetővé. A Felhőbeli erőforrások megszüntetik a pont-pont típusú összevonások a partnerekkel való konfigurálásának terhelését.

### <a name="address-compliance-and-governance"></a>A címek megfelelősége és irányítása

A vállalati hozzáférési házirendek betartatásával, valamint az alkalmazásokhoz és a kapcsolódó adatokhoz való felhasználói hozzáférés figyelése integrált auditálási eszközök és API-k használatával gondoskodhat a szabályozási követelményeknek való megfelelésről. Az Azure AD használatával a [biztonsági incidenseket és az esemény-figyelési (SIEM) eszközöket](../reports-monitoring/plan-monitoring-and-reporting.md)használó jelentések segítségével figyelheti az alkalmazás-bejelentkezéseket. A jelentéseket elérheti a portálról vagy az API-król, valamint programozott módon naplózhatja, hogy ki férhet hozzá az alkalmazásaihoz, és hogyan távolíthatja el az inaktív felhasználók hozzáférését hozzáférési felülvizsgálatok útján.

## <a name="plan-your-migration-phases-and-project-strategy"></a>Tervezze meg az áttelepítési fázisokat és a Project stratégiát

Ha a technológiai projektek sikertelenek, gyakran eltérő elvárások, a megfelelő érintett felek vagy a kommunikáció hiánya miatt. Győződjön meg róla, hogy a projekt megtervezése sikeres.

### <a name="the-phases-of-migration"></a>Az áttelepítési fázisok

Az eszközök beszerzése előtt Ismerje meg, hogyan gondolja át az áttelepítési folyamatot. A következő négy fázist ajánljuk több közvetlen ügyfél-workshopon:

![Az áttelepítési fázisok ábrája](media/migrating-application-authentication-to-azure-active-directory-2.jpg)

### <a name="assemble-the-project-team"></a>A projekt csapatának összeállítása

Az alkalmazások áttelepítése a csapatmunka, és meg kell győződnie arról, hogy az összes létfontosságú pozíció kitöltése megtörtént. Fontos a vezető üzleti vezetők támogatása. Győződjön meg arról, hogy a megfelelő vezetői szponzorokat, üzleti döntéshozókat és tárgyi szakértőket (KKV-kat) tartalmaz.

Az áttelepítési projekt során egy személy több szerepkört is teljesít, vagy a szervezet méretétől és struktúrájától függően több ember teljesíti az egyes szerepköröket. Lehet, hogy más csapatokkal is függ, amelyek kulcsfontosságú szerepet játszanak a biztonsági környezetben.

A következő táblázat tartalmazza a legfontosabb szerepköröket és azokhoz való hozzájárulásukat:

| Szerepkör          | Közreműködések                                              |
| ------------- | ---------------------------------------------------------- |
| **Projektvezető** | A Project edzője a projekt GUID azonosítóját veszi figyelembe, beleértve a következőket:<br /> – vezetői támogatás megszerzése<br /> – az érintett felek bevonása<br /> -az ütemtervek, a dokumentáció és a kommunikáció kezelése |
| **Identitás-építész/Azure AD alkalmazás rendszergazda** | A következőkért felelősek:<br /> -a megoldás megtervezése az érintett felekkel együttműködve<br /> – dokumentálja a megoldás kialakítását és működési eljárásait az operatív csapat handoff<br /> -az üzem előtti és éles környezetek kezelése |
| **Helyszíni AD műveleti csapat** | A különböző helyszíni identitás-forrásokat kezelő szervezet, például AD-erdők, LDAP-címtárak, HR-rendszerek stb.<br /> – a szinkronizálás előtt végezze el a szükséges szervizelési feladatokat<br /> -Adja meg a szinkronizáláshoz szükséges szolgáltatásfiókot<br /> -hozzáférés biztosítása az Azure AD-hez való összevonás konfigurálásához |
| **INFORMATIKAI támogatás kezelője** | Az informatikai támogatási szervezet egyik képviselője, aki a változás támogatását nyújtja a helpdesk szemszögéből. |
| **Biztonsági tulajdonos**  | A biztonsági csapat képviselője, amely képes biztosítani, hogy a csomag megfeleljen a szervezete biztonsági követelményeinek. |
| **Alkalmazás technikai tulajdonosai** | Az Azure AD-vel integrált alkalmazások és szolgáltatások technikai tulajdonosait tartalmazza. Ezek biztosítják az alkalmazások identitási attribútumait, amelyeket tartalmazniuk kell a szinkronizálási folyamatba. Általában a CSV-képviselőkkel való kapcsolattal rendelkeznek. |
| **Alkalmazás üzleti tulajdonosai** | Azok a munkatársak, akik a felhasználói élményhez és a változás hasznosságához szükségesek a felhasználó szemszögéből, és az alkalmazás általános üzleti aspektusával rendelkeznek, ami magában foglalhatja a hozzáférés kezelését is. |
| **Felhasználók kísérleti csoportja** | Azok a felhasználók, akik a napi munkájuk részeként tesztelik a tesztelést, és visszajelzést adnak a többi üzembe helyezési útmutatóhoz. |

### <a name="plan-communications"></a>A kommunikáció tervezése

A sikeres üzleti szerepvállalás és kommunikáció a siker kulcsa. Fontos, hogy az érdekelt felek és a végfelhasználók egy utat kapjanak az információk beszerzéséhez és az ütemezett frissítések folyamatos tájékoztatásához. Ismerje meg mindenki számára az áttelepítés értékét, a várt időkereteket, valamint azt, hogyan tervezze meg az átmeneti üzleti fennakadást. Több utat is használhat, például a tájékoztató előadásokat, az e-maileket, az egy-az-egyhez értekezleteket, a bannereket és a városházát.

Az alkalmazáshoz választott kommunikációs stratégia alapján érdemes lehet emlékeztetni a felhasználókat a függőben lévő állásidőre. Azt is ellenőriznie kell, hogy nincsenek-e olyan közelmúltbeli módosítások vagy üzleti hatások, amelyek az üzemelő példány elhalasztását igénylik.

Az alábbi táblázatban megtalálja a javasolt kommunikációt, amely tájékoztatja az érdekelt feleket:

**A fázisok és a projekt stratégiájának megtervezése**:

| Kommunikáció      | Célközönség                                          |
| ------------------ | ------------------------------------------------- |
| A projekt ismertsége és üzleti/technikai értéke | Az összes végfelhasználó kivételével |
| Kísérleti alkalmazások kérelmezése | – Az alkalmazás üzleti tulajdonosai<br />– Alkalmazás technikai tulajdonosai<br />-Építészek és Identity Team |

**1. fázis – felderítés és hatókör**:

| Kommunikáció      | Célközönség                                          |
| ------------------ | ------------------------------------------------- |
| – Az alkalmazás adatainak kérelmezése<br />– A hatókör gyakorlatának eredménye | – Alkalmazás technikai tulajdonosai<br />– Az alkalmazás üzleti tulajdonosai |

**2. fázis – az alkalmazások besorolása és a kísérleti terv**:

| Kommunikáció      | Célközönség                                          |
| ------------------ | ------------------------------------------------- |
| – A besorolások és az áttelepítési ütemtervek eredményei<br />– Előzetes áttelepítési ütemterv | – Alkalmazás technikai tulajdonosai<br /> – Az alkalmazás üzleti tulajdonosai |

**3. fázis – áttelepítési és tesztelési terv**:

| Kommunikáció      | Célközönség                                          |
| ------------------ | ------------------------------------------------- |
| – Az alkalmazás-áttelepítés tesztelésének eredménye | – Alkalmazás technikai tulajdonosai<br />– Az alkalmazás üzleti tulajdonosai |
| – Értesítés arról, hogy a Migrálás megérkezik, és az eredményül kapott végfelhasználói tapasztalatok magyarázata.<br />– Az állásidőt és a teljes kommunikációt, beleértve a most elvégzendő teendőket, a visszajelzéseket és a segítség kérését | -Végfelhasználók (és minden más) |

**4. fázis – elemzések kezelése és megszerzése**:

| Kommunikáció      | Célközönség                                          |
| ------------------ | ------------------------------------------------- |
| Elérhető Analitika és hozzáférés | – Alkalmazás technikai tulajdonosai<br />– Az alkalmazás üzleti tulajdonosai |

### <a name="migration-states-communication-dashboard"></a>Áttelepítési állapotok kommunikációs irányítópultja

Az áttelepítési projekt általános állapotának kommunikációja elengedhetetlen, mivel a folyamat előrehaladását mutatja, és segít az alkalmazások tulajdonosainak, hogy az áttelepítés előkészítése érdekében a Migrálás elkészüljön. Az Power BI vagy más jelentéskészítő eszközökkel összeállíthat egy egyszerű irányítópultot, amellyel megtekintheti az alkalmazások állapotát az áttelepítés során.

A következő áttelepítési állapotokat érdemes lehet használni:

| Áttelepítési állapotok       | Műveletterv                                   |
| ---------------------- | --------------------------------------------- |
| **Kezdeti kérelem** | Keresse meg az alkalmazást, és további információért forduljon a tulajdonoshoz |
| **Az értékelés befejeződött** | Az alkalmazás tulajdonosa kiértékeli az alkalmazás követelményeit, és visszaadja az alkalmazás kérdőívét</td>
| **Konfiguráció folyamatban** | Az Azure AD-vel való hitelesítés kezeléséhez szükséges módosítások fejlesztése |
| **A konfiguráció tesztelése sikerült** | Értékelje ki a módosításokat, és hitelesítse az alkalmazást az Azure AD-bérlő tesztelési környezetében. |
| **Az éles környezet konfigurálása sikerült** | Módosítsa úgy a konfigurációkat, hogy az éles üzemben lévő AD-bérlőn működjenek, és mérje fel az alkalmazás hitelesítését a tesztkörnyezetben |
| **Befejezés/kijelentkezés** | Telepítse az alkalmazás módosításait az éles környezetbe, és hajtsa végre az éles környezetben futó Azure AD-bérlőt. |

Ezzel biztosíthatja, hogy az alkalmazások tulajdonosai tisztában legyenek az alkalmazás áttelepítési és tesztelési időpontjával, amikor az alkalmazásaiik áttelepítésre kerülnek, és hogy milyen eredmények származnak a már áttelepített más alkalmazásokból. Érdemes lehet megfontolni a hibakövető adatbázisra mutató hivatkozásokat is, amelyek lehetővé teszik a tulajdonosok számára, hogy az áttelepített alkalmazások számára is képesek legyenek fájlok kezelésére és megtekintésére.

### <a name="best-practices"></a>Ajánlott eljárások

A következő ügyfeleink és partnereink sikertörténetei, valamint ajánlott eljárások:

- [Öt tipp az áttelepítési folyamat fejlesztéséhez](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Five-tips-to-improve-the-migration-process-to-Azure-Active/ba-p/445364) , amely a Patriot Consulting által Azure Active Directory, a partneri hálózat egy tagja, amely arra összpontosít, hogy segítsen az ügyfeleknek a Microsoft Cloud Solutions biztonságos üzembe helyezésében.

- [Fejlesszen kockázatkezelési stratégiát az Azure ad-alkalmazás áttelepítéséhez](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Develop-a-risk-management-strategy-for-your-Azure-AD-application/ba-p/566488) a Edgile, amely a iam-és kockázatkezelési megoldásokra összpontosít.

## <a name="phase-1-discover-and-scope-apps"></a>1. fázis: alkalmazások felderítése és hatóköre

**Az alkalmazások felderítése és elemzése egy alapvető feladat, amely jó kezdést biztosít.** Előfordulhat, hogy nem tudja, hogy minden, ami készen áll az ismeretlen alkalmazások befogadására.

### <a name="find-your-apps"></a>Alkalmazások keresése

Az alkalmazások áttelepítésének első döntési pontja, hogy mely alkalmazásokat kell áttelepíteni, és hogy mely alkalmazások legyenek elavultak. Mindig lehetőség van arra, hogy érvénytelenítse azokat az alkalmazásokat, amelyeket nem fog használni a szervezetében. Több módon is megtalálhatja az alkalmazásokat a szervezetében. **Az alkalmazások felfedése közben győződjön meg róla, hogy a fejlesztéssel és a tervezett alkalmazásokkal is rendelkezik. az Azure AD-t használja a hitelesítéshez az összes jövőbeli alkalmazásban.**

**A Active Directory összevonási szolgáltatások (AD FS) (AD FS) használata egy helyes alkalmazás-leltár összegyűjtéséhez:**

- **Használja az Azure AD Connect Health eszközt.** Ha prémium szintű Azure AD licenccel rendelkezik, javasoljuk, hogy [Azure ad Connect Health](../hybrid/how-to-connect-health-adfs.md) üzembe helyezésével elemezze az alkalmazás használatát a helyszíni környezetben. Az [ADFS Application Report](./migrate-adfs-application-activity.md) (előzetes verzió) használatával felderítheti az áttelepíthető ADFS-alkalmazásokat, és kiértékelheti az áttelepítendő alkalmazás készültségét. Az áttelepítés befejezése után telepítsen [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery) , amely lehetővé teszi, hogy folyamatosan figyelje az árnyékot a szervezetben, ha a felhőben van.

- **AD FS a napló-elemzést**. Ha nem rendelkezik prémium szintű Azure AD licenccel, javasoljuk, hogy az ADFS-t az Azure AD alkalmazás-áttelepítési eszközökre használja a [PowerShell alapján.](https://github.com/AzureAD/Deployment-Plans/tree/master/ADFS%20to%20AzureAD%20App%20Migration) Tekintse meg a [megoldási útmutatót](./migrate-adfs-apps-to-azure.md):

[Alkalmazások áttelepítése Active Directory összevonási szolgáltatások (AD FS)ról (AD FS) az Azure AD-be.](./migrate-adfs-apps-to-azure.md)

### <a name="using-other-identity-providers-idps"></a>Más identitás-szolgáltatók (IDP-EK) használata

Más identitás-szolgáltatók (például a okta vagy a ping) esetében a eszközeiket az alkalmazás leltárának exportálására használhatja. Érdemes megfontolni, hogy a szervezete webalkalmazásainak megfelelő Active Directory regisztrált szolgáltatási elveket tekintse át.

### <a name="using-cloud-discovery-tools"></a>Cloud Discovery-eszközök használata

A felhőalapú környezetekben gazdag láthatóságot, az adatutazás vezérlését és a kifinomult elemzési megoldásokat kell használnia a számítógépes fenyegetések az összes felhőalapú szolgáltatásban való megtalálásához és leküzdéséhez. A Cloud app Inventory a következő eszközök használatával gyűjthető össze:

- **Cloud Access Security Broker (CASB**) – a [CASB](/cloud-app-security/) jellemzően a tűzfal mellett működik, így biztosítva az alkalmazottak Felhőbeli alkalmazás-használati adatait, és segít megvédeni a vállalati adatokat a kiberbiztonsági fenyegetésektől. A CASB jelentés segítségével meghatározhatja a szervezet leggyakrabban használt alkalmazásait, valamint az Azure AD-ba való Migrálás korai céljait.

- **Cloud Discovery** – a [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery)konfigurálásával betekintést nyerhet a Cloud App-alkalmazásba, és felderítheti a nem engedélyezett vagy árnyékos informatikai alkalmazásokat.

- **API** -k – a felhőalapú infrastruktúrához kapcsolódó alkalmazások esetében az API-k és eszközök használatával megkezdheti az üzemeltetett alkalmazások leltározását. Az Azure-környezetben:

  - Használja a [Get-AzureWebsite](/powershell/module/servicemanagement/azure.service/get-azurewebsite) parancsmagot az Azure websites szolgáltatással kapcsolatos információk beszerzéséhez.

  - Használja a [Get-AzureRMWebApp](/powershell/module/azurerm.websites/get-azurermwebapp) parancsmagot az Azure-Web Apps adatainak beszerzéséhez.
T
  - A Microsoft IIS-ben futó összes alkalmazást megtalálhatja a Windows parancssorból [AppCmd.exe](/iis/get-started/getting-started-with-iis/getting-started-with-appcmdexe#working-with-sites-applications-virtual-directories-and-application-pools)használatával.

  - Az [alkalmazások](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) és az [egyszerű szolgáltatások](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity) segítségével információkat kaphat az Azure ad-címtárban található alkalmazásokról és alkalmazás-példányokról.

### <a name="using-manual-processes"></a>Manuális folyamatok használata

Miután elvégezte a fentiekben ismertetett automatikus megközelítést, jól kezelheti az alkalmazásait. Azonban érdemes lehet megfontolnia az alábbiakat, hogy biztosítsa az összes felhasználói hozzáférési terület megfelelő lefedettségét:

- A szervezetében használt alkalmazások megkereséséhez vegye fel a kapcsolatot a szervezet különböző üzleti tulajdonosainak.

- Futtasson egy HTTP-ellenőrzési eszközt a proxykiszolgálón, vagy elemezze a proxy naplóit, és tekintse meg, hogy a forgalom általában hová legyen irányítva.

- Tekintse át a webnaplókat a népszerű céges portál webhelyekről, és nézze meg, hogy a felhasználók milyen kapcsolatokhoz férhetnek hozzá

- Ismerje meg a vezetők vagy más kulcsfontosságú üzleti tagok számára az üzleti szempontból kritikus fontosságú alkalmazások biztosítását.

### <a name="type-of-apps-to-migrate"></a>Az áttelepítendő alkalmazások típusa

Az alkalmazások megkeresése után azonosíthatja az alábbi típusú alkalmazásokat a szervezetében:

- A modern hitelesítési protokollokat használó alkalmazások már

- A modernizálni kívánt örökölt hitelesítési protokollokat használó alkalmazások

- Az örökölt hitelesítési protokollokat használó alkalmazások, amelyeket nem szeretne modernizálni

- Új üzletági (LoB) alkalmazások

### <a name="apps-that-use-modern-authentication-already"></a>A modern hitelesítést használó alkalmazások már

A már modernizált alkalmazások legvalószínűbben az Azure AD-be lesznek helyezve. Ezek az alkalmazások már modern hitelesítési protokollokat használnak (például SAML vagy OpenID Connect), és újrakonfigurálható az Azure AD-vel való hitelesítéshez.

Az [Azure ad](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) -katalógusban megjelenő lehetőségek mellett ezek olyan alkalmazások is lehetnek, amelyek már léteznek a szervezetében vagy bármely olyan gyártótól származó alkalmazásból, amely nem része az Azure ad-katalógusnak ([nem Gallery-alkalmazásoknak)](./add-application-portal.md).

A korszerűsítéshez kiválasztott örökölt alkalmazások

A modernizálni kívánt örökölt alkalmazások esetében az Azure AD-be az alapszintű hitelesítéshez és az engedélyezéshez a [Microsoft Graph](https://developer.microsoft.com/graph/gallery/?filterBy=Samples,SDKs) és [intelligens biztonsági gráf](https://www.microsoft.com/security/operations/intelligence?rtc=1) által kínált összes energiaellátási és adatmennyiséget feloldja.

Javasoljuk, hogy **frissítse az alkalmazások hitelesítési verem kódját** az örökölt protokollból (például Windows-Integrated hitelesítés, Kerberos által korlátozott DELEGÁLÁS, HTTP-fejlécek alapú hitelesítés) egy modern protokollra (például SAML vagy OpenID Connect).

### <a name="legacy-apps-that-you-choose-not-to-modernize"></a>Örökölt alkalmazások, amelyek nem modernizálják

Az örökölt hitelesítési protokollokat használó alkalmazások esetében előfordulhat, hogy a hitelesítés korszerűsítése nem a megfelelő dolog üzleti okokból. Ezek a következő típusú alkalmazásokat tartalmazzák:

- A helyszínen őrzött alkalmazások megfelelőségi vagy ellenőrzési okokból.

- Olyan helyszíni identitáshoz vagy összevonási szolgáltatóhoz csatlakoztatott alkalmazások, amelyeket nem szeretne módosítani.

- A helyszíni hitelesítési szabványok használatával fejlesztett alkalmazások, amelyek nem rendelkeznek áthelyezési tervvel

Az Azure AD nagyszerű előnyökkel járhat ezeknél a régi alkalmazásokban, mivel lehetővé teszi a modern Azure AD-beli biztonsági és irányítási funkciók használatát, mint például a [multi-Factor Authentication](../authentication/concept-mfa-howitworks.md), a [feltételes hozzáférés](../conditional-access/overview.md), az [Identitásvédelem](../identity-protection/index.yml), a [delegált alkalmazás-hozzáférés](./access-panel-manage-self-service-access.md)és az alkalmazásokhoz [való hozzáférési felülvizsgálatok](../governance/manage-user-access-with-access-reviews.md#create-and-perform-an-access-review) anélkül, hogy az alkalmazást még nem érintik.

Először is **kiterjesztheti ezeket az alkalmazásokat a felhőbe** az [Azure ad-alkalmazásproxy használatával](./application-proxy-configure-single-sign-on-password-vaulting.md) egyszerű hitelesítéssel (például a jelszó-tárolóval), hogy a felhasználók gyorsan migrálják a felhasználókat, vagy ha a [partner-integrációnk](https://azure.microsoft.com/services/active-directory/sso/secure-hybrid-access/) segítségével már üzembe helyezte az alkalmazás-továbbítási vezérlőket.

### <a name="new-line-of-business-lob-apps"></a>Új üzletági (LoB) alkalmazások

Általában LoB-alkalmazásokat fejleszt a szervezete belső használatra. Ha új alkalmazásai vannak a folyamatban, javasoljuk, hogy a [Microsoft Identity platformot](../develop/v2-overview.md) használja az OpenID Connect megvalósításához.

### <a name="apps-to-deprecate"></a>Elavult alkalmazások

Az alkalmazások tiszta tulajdonosok nélkül, a karbantartás és a monitorozás révén biztonsági kockázatot jelentenek a szervezet számára. Az alkalmazások elavultnak kell lennie:

- a **funkcióik nagyon redundánsak** más rendszerekkel kapcsolatban • nincs **üzleti tulajdonos**

- egyértelműen **nincs használat**.

Javasoljuk, hogy ne **érvénytelenítse a nagy hatású, üzleti szempontból kritikus fontosságú alkalmazásokat**. Ezekben az esetekben a megfelelő stratégiát a vállalat tulajdonosai határozzák meg.

### <a name="exit-criteria"></a>Kilépési feltételek

Ebben a fázisban sikeres:

- A Migrálás hatókörében található rendszerek ismerete (az Azure AD-ba való áttérés után kivonja azokat)

- A következőket tartalmazó alkalmazások listája:

  - Azok a rendszerek, amelyekre ezek az alkalmazások csatlakoznak
  - Honnan és milyen eszközökön férhetnek hozzá a felhasználókhoz
  - Azt határozza meg, hogy a rendszer áttelepíti, elavult vagy [Azure ad Connecthoz](../hybrid/whatis-azure-ad-connect.md)kapcsolódik-e.

> [!NOTE]
> Az [alkalmazás-felderítési munkalap](https://download.microsoft.com/download/2/8/3/283F995C-5169-43A0-B81D-B0ED539FB3DD/Application%20Discovery%20worksheet.xlsx) letöltésével rögzítheti az Azure ad-hitelesítésre áttelepíteni kívánt alkalmazásokat, valamint azokat, amelyeket el szeretne hagyni, de a [Azure ad Connect](../hybrid/whatis-azure-ad-connect.md)segítségével kezelheti azokat.

## <a name="phase-2-classify-apps-and-plan-pilot"></a>2. fázis: alkalmazások osztályozása és a pilóta megtervezése

Az alkalmazások áttelepítésének besorolása fontos feladat. Nem minden alkalmazásnak kell áttelepítenie és áttérnie egy időben. Miután összegyűjtötte az egyes alkalmazásokkal kapcsolatos információkat, racionalizálhatja, hogy mely alkalmazásokat kell áttelepíteni, és melyekhez lehet hozzá időt igénybe venni.

### <a name="classify-in-scope-apps"></a>Hatókörbeli alkalmazások besorolása

Ennek egyik módja az üzleti kritikusság, a használat és az élettartam tengelye, amelyek mindegyike több tényezőtől függ.

### <a name="business-criticality"></a>Üzleti kritikusság

Az üzleti kritikusság az egyes vállalkozások különböző dimenzióit veszi igénybe, de a két szempontot figyelembe kell vennie a **funkciók és a funkciók** , valamint a **felhasználói profilok** számára is. Az egyedi funkciókkal rendelkező alkalmazásokat a redundáns vagy elavult funkcióknál magasabb értékkel rendelheti hozzá.

![A szolgáltatások spektrumának ábrája & funkció és felhasználói profilok](media/migrating-application-authentication-to-azure-active-directory-3.jpg)

### <a name="usage"></a>Használat

A **magas használati számmal** rendelkező alkalmazásoknak magasabb értéket kell kapniuk, mint az alacsony kihasználtságú alkalmazások. Magasabb értéket rendelhet hozzá az alkalmazásokhoz külső, ügyvezető vagy biztonsági csapat felhasználói számára. Az áttelepítési portfólió minden alkalmazásához végezze el ezeket az értékeléseket.

![A felhasználói kötet és a felhasználói szélesség spektrumának ábrája](media/migrating-application-authentication-to-azure-active-directory-4.jpg)

Miután meghatározta az üzleti kritikusság és a használat értékeit, megadhatja az **alkalmazás élettartamát**, és létrehozhatja a prioritási mátrixot. Tekintse meg az alábbi mátrixot:

![Háromszög-diagram, amely a használat, a várt élettartam és az üzleti kritikusság közötti kapcsolatokat mutatja](media/migrating-application-authentication-to-azure-active-directory5.jpg)

### <a name="prioritize-apps-for-migration"></a>Alkalmazások rangsorolása áttelepítéshez

Dönthet úgy, hogy megkezdi az alkalmazás áttelepítését a legalacsonyabb prioritású alkalmazásokkal vagy a legmagasabb prioritású alkalmazásokkal a szervezet igényei szerint.

Olyan helyzetekben, ahol az Azure AD és az Identity Services használata nem tapasztalható, érdemes először áthelyezni a **legalacsonyabb prioritású alkalmazásokat** az Azure ad-be. Ez a művelet a lehető legkisebbre csökkentheti az üzleti hatásokat, és lendületet is készíthet. Miután sikeresen áthelyezte ezeket az alkalmazásokat, és megszerezte az érintettek bizalmát, továbbra is áttelepítheti a többi alkalmazást.

Ha nincs egyértelmű prioritás, érdemes megfontolnia az [Azure ad](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) -katalógusban lévő alkalmazások első áthelyezését, és támogatni a több Identity providert (ADFS vagy okta), mivel azok könnyebben integrálhatók. Valószínű, hogy ezek az alkalmazások a **legmagasabb prioritású alkalmazások** a szervezetben. Az SaaS-alkalmazások Azure AD-val való integrálásának elősegítése érdekében olyan [oktatóanyagokat](../saas-apps/tutorial-list.md) fejlesztettünk ki, amelyek végigvezetik a konfiguráción.

Ha az alkalmazások áttelepítésére vonatkozó határidővel rendelkezik, ezek a legmagasabb prioritású alkalmazások gyűjtője a legfontosabb munkaterhelést fogja igénybe venni. Végül kiválaszthatja az alacsonyabb prioritású alkalmazásokat, mivel azok nem változtatják meg a költségeket annak ellenére, hogy áthelyezte a határidőt. Akkor is, ha meg kell újítania a licencet, kis mennyiségű.

A besorolás és az áttelepítés sürgőssége mellett érdemes lehet **áttelepítési ütemtervet** is megfontolni, amelyen belül az alkalmazás tulajdonosának be kell vonnia az alkalmazásaikat. A folyamat végén az áttelepítéshez rangsorolt gyűjtők listájának kell szerepelnie az összes alkalmazásban.

### <a name="document-your-apps"></a>Az alkalmazások dokumentálása

Először is kezdje az alkalmazásokkal kapcsolatos legfontosabb adatok gyűjtésével. Az [alkalmazás-felderítési munkalapra](https://download.microsoft.com/download/2/8/3/283F995C-5169-43A0-B81D-B0ED539FB3DD/Application%20Discovery%20worksheet.xlsx)kattintva gyorsan elvégezheti az áttelepítési döntéseket, és az üzleti csoportra vonatkozó ajánlásokat bármikor elérheti.

Az áttelepítési döntéshez fontos információk a következők:

- **Alkalmazás neve** – Mit nevezünk az alkalmazásnak a vállalatnak?

- **Alkalmazás típusa** – harmadik féltől származó SaaS-alkalmazás? Egyéni üzletági webes alkalmazás? Egy API-t?

- **Üzleti kritikusság** – magas a kritikusság? Alacsony? Vagy valahol a között?

- **Felhasználói hozzáférés mennyisége** – mindenki hozzáférhet ehhez az alkalmazáshoz, vagy csak néhány ember?

- **Tervezett élettartam** – mennyi ideig tart az alkalmazás? Kevesebb mint hat hónap? Több mint két éve?

- **Aktuális identitás-szolgáltató** – mi az alkalmazás elsődleges identitásszolgáltató? Vagy helyi tárterületre támaszkodik?

- **Hitelesítési módszer** – az alkalmazás nyílt szabványok használatával hitelesíthető?

- Az alkalmazás a tervezett vagy az aktív fejlesztés alatt van-e az alkalmazás **kódjának frissítéséhez** ?

- **Függetlenül attól** , hogy szeretné-e megtartani az alkalmazást a helyszínen? – kívánja megőrizni az alkalmazást az adatközpontban?

- Azt **jelzi, hogy az alkalmazás más alkalmazásokból vagy API** -kkal függ-e, az alkalmazás jelenleg más alkalmazásokba vagy API-khoz hív?

- Azt **jelzi, hogy az alkalmazás az Azure ad** -katalógusban van-e, jelenleg már integrálva van az [Azure ad-galériával](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)?

Más, a későbbiekben segítségre kerülő, de nem szükséges azonnali áttelepítési döntés:

- **Alkalmazás URL-címe** – itt érheti el a felhasználók az alkalmazást?

- **Alkalmazás leírása** – mi a rövid leírás arról, hogy mit tesz az alkalmazás?

- Az **alkalmazás tulajdonosa** – kik a vállalatban az alkalmazás fő POC-je?

- **Általános megjegyzések vagy megjegyzések** – az alkalmazással vagy üzleti tulajdonlással kapcsolatos egyéb általános információk

Miután besorolta az alkalmazást, és dokumentálta a részleteket, ügyeljen arra, hogy a tervezett áttelepítési stratégiához vegyen részt az üzleti tulajdonosnál.

### <a name="plan-a-pilot"></a>Pilóta megtervezése

A teszteléshez kiválasztott alkalmazás (ok) a szervezet legfontosabb identitási és biztonsági követelményeinek felel meg, és az alkalmazás tulajdonosainak egyértelmű bevezetéssel kell rendelkeznie. A pilóták általában külön tesztkörnyezetben futnak. Lásd: [ajánlott eljárások a pilóták számára](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) a központi telepítési tervek lapon.

**Ne felejtse el a külső partnereit.** Győződjön meg arról, hogy részt vesznek az áttelepítési ütemtervekben és a tesztelésben. Végezetül ellenőrizze, hogy van-e lehetőség a segélyszolgálat elérésére, ha problémák léptek fel.

### <a name="plan-for-limitations"></a>A korlátozások megtervezése

Egyes alkalmazások egyszerűen áttelepíthetők, mások pedig több kiszolgáló vagy példány miatt hosszabb időt vehetnek igénybe. A SharePoint-áttelepítés például az egyéni bejelentkezési lapok miatt hosszabb ideig is tarthat.

Sok SaaS-alkalmazás gyártójának díja az egyszeri bejelentkezéses kapcsolatok módosításához. Egyeztessen velük, és tervezze meg a következőt:.

Az Azure AD [szolgáltatás korlátaival és korlátozásokkal](../enterprise-users/directory-service-limits-restrictions.md) is rendelkezik.

### <a name="app-owner-sign-off"></a>Alkalmazás tulajdonosának kijelentkezése

Az üzleti szempontból kritikus és általánosan használt alkalmazásokhoz szükség lehet a kísérleti felhasználók egy csoportjára, hogy tesztelje az alkalmazást a kísérleti fázisban. Miután tesztelt egy alkalmazást az üzem előtti vagy kísérleti környezetben, gondoskodjon arról, hogy az alkalmazás üzleti tulajdonosai kijelentkezzenek a teljesítményre az alkalmazás és az összes felhasználó az Azure AD-ben történő, hitelesítésre való áttelepítése előtt.

### <a name="plan-the-security-posture"></a>A biztonsági testhelyzet megtervezése

Az áttelepítési folyamat megkezdése előtt Szánjon időt arra, hogy teljes mértékben mérlegelje a vállalati identitás rendszerének fejlesztéséhez szükséges biztonsági helyzeteket. Ez a következő értékes adatkészletek összegyűjtésén alapul: **az adatokhoz hozzáférő identitások, eszközök és helyszínek.**

### <a name="identities-and-data"></a>Identitások és adatkezelés

A legtöbb szervezet konkrét követelményekkel rendelkezik az iparági szegmensek és a szervezeteken belüli feladatok által érintett identitások és adatvédelem terén. Tekintse meg az [identitás-és eszköz-hozzáférési konfigurációkat](/microsoft-365/enterprise/microsoft-365-policies-configurations) , beleértve a [feltételes hozzáférési szabályzatok](../conditional-access/overview.md) és a kapcsolódó képességek előírt készletét.

Ezeket az információkat az Azure AD-vel integrált összes szolgáltatáshoz való hozzáféréshez használhatja fel. Ezek az ajánlások a Microsoft biztonságos pontszámával és az [Azure ad-beli identitási pontszámával](../fundamentals/identity-secure-score.md)összhangban vannak. A pontszám az alábbiakban nyújt segítséget:

- Az identitásbiztonsági rendszer objektív felmérése

- Identitásbiztonsági fejlesztések tervezése

- A fejlesztések hatásának felmérése

Ez segít az öt lépés megvalósításában az [identitás-infrastruktúra biztonságossá](../../security/fundamentals/steps-secure-identity.md)tételéhez. Az útmutatást kiindulási pontként használhatja a szervezete számára, és módosíthatja a szabályzatokat, hogy azok megfeleljenek a szervezet igényeinek.

### <a name="who-is-accessing-your-data"></a>Ki fér hozzá az adatokhoz?

Az Azure AD által támogatott alkalmazások és erőforrások felhasználóinak két fő kategóriája van:

- **Belső:** Alkalmazottak, alvállalkozók és szállítók, akik az Ön identitás-szolgáltatóján belüli fiókkal rendelkeznek. Ehhez további kimutatásokra lehet szükség, amelyek különböző szabályokkal rendelkeznek a vezetők vagy más alkalmazottak ellen.

- **Külső:** Gyártók, szállítók, forgalmazók vagy más üzleti partnerek, akik a szervezettel együttműködve az [Azure ad B2B együttműködéssel](../external-identities/what-is-b2b.md) rendszeres üzleti tevékenységet folytatnak.

Ezekhez a felhasználókhoz csoportokat határozhat meg, és különböző módokon töltheti fel ezeket a csoportokat. Dönthet úgy is, hogy a rendszergazdának manuálisan kell felvennie a tagokat egy csoportba, vagy engedélyezheti a selfservice csoport tagságát. Létrehozhatók olyan szabályok, amelyek a [dinamikus csoportok](../enterprise-users/groups-dynamic-membership.md)használatával a megadott feltételek alapján automatikusan hozzáadhatják a tagokat a csoportokhoz.

Előfordulhat, hogy a külső felhasználók is hivatkozhatnak az ügyfelekre. [Azure ad B2C](../../active-directory-b2c/overview.md)egy külön termék támogatja az ügyfél-hitelesítést. Azonban a dokumentum hatókörén kívül esik.

### <a name="devicelocation-used-to-access-data"></a>Az adateléréshez használt eszköz/hely

A felhasználó által az alkalmazás eléréséhez használt eszköz és hely is fontos. A vállalati hálózathoz fizikailag csatlakoztatott eszközök biztonságosabbak. A hálózaton kívülről a VPN-kapcsolaton keresztül létesített kapcsolatok esetében vizsgálatra lehet szükség.

![A felhasználói hely és az adatelérés közötti kapcsolatot bemutató diagram](media/migrating-application-authentication-to-azure-active-directory-6.jpg)

Az erőforrás, a felhasználó és az eszköz ezen szempontjaival az [Azure ad feltételes hozzáférési](../conditional-access/overview.md) képességeit is használhatja. A feltételes hozzáférés túllépi a felhasználói engedélyeket: a rendszer a tényezők, például egy felhasználó vagy csoport identitása, a felhasználóhoz csatlakozó hálózat, a használt eszköz és alkalmazás, valamint az elérni kívánt adatok típusának kombinációján alapul. A felhasználónak biztosított hozzáférés ehhez a szélesebb körű feltételekhez alkalmazkodik.

### <a name="exit-criteria"></a>Kilépési feltételek

Ebben a fázisban sikeres:

- Az alkalmazások megismerése
  - Teljes mértékben dokumentálta az áttelepíteni kívánt alkalmazásokat
  - Az üzleti kritikusság, a használat mennyisége és az élettartam alapján rangsorolt alkalmazásokat kell használnia

- A tesztelési követelményeknek megfelelő alkalmazásokat jelölt ki

- Üzleti tulajdonosi beszálló a rangsorolás és a stratégia számára

- A biztonsági helyzetek igényének megismerése és a megvalósításuk módja

## <a name="phase-3-plan-migration-and-testing"></a>3. fázis: áttelepítés és tesztelés tervezése

Az üzleti vásárlást követően a következő lépés az alkalmazások Azure AD-hitelesítésre való áttelepítésének megkezdése.

### <a name="migration-tools-and-guidance"></a>Áttelepítési eszközök és útmutatás

Az alábbi eszközök és útmutatás segítségével követheti az alkalmazások Azure AD-ba való átirányításához szükséges pontos lépéseket:

- **Általános áttelepítési útmutató** – az [Azure ad-alkalmazások áttelepítési eszközkészletének](./migration-resources.md) tanulmányi, eszközök, e-mail-sablonjai és alkalmazásai az alkalmazások felderítéséhez, besorolásához és áttelepítéséhez használhatók.

- **SaaS-alkalmazások** – tekintse meg a [több száz SaaS-alkalmazás oktatóanyagok](../saas-apps/tutorial-list.md) listáját, valamint a teljes [Azure ad SSO üzembe helyezési tervet](https://aka.ms/ssodeploymentplan) , amely végigvezeti a végpontok közötti folyamaton.

- **A helyszínen futó alkalmazások** – megismerheti [Az Azure ad Application Proxyét](./application-proxy.md) , és a teljes [Azure ad Application proxy üzembe helyezési terv](https://aka.ms/AppProxyDPDownload) használatával gyorsan elsajátíthatja a munkát.

- **Fejlesztett alkalmazások** – olvassa el a lépésenkénti [integrációs](../develop/quickstart-register-app.md) és [regisztrációs](../develop/quickstart-register-app.md) útmutatót.

Az áttelepítés után dönthet úgy, hogy elküldi a sikeres üzembe helyezést a felhasználókat, és emlékezteti őket a szükséges új lépésekre.

### <a name="plan-testing"></a>Tesztelési terv

Az áttelepítés folyamata során előfordulhat, hogy az alkalmazás már rendelkezik egy, a normál üzembe helyezés során használt tesztkörnyezetben. Továbbra is használhatja ezt a környezetet az áttelepítési teszteléshez. Ha a tesztkörnyezet jelenleg nem érhető el, előfordulhat, hogy az alkalmazás architektúrája alapján Azure App Service vagy az Azure Virtual Machines használatával lehet beállítani egyet. Dönthet úgy, hogy külön Azure AD-bérlőt állít be, amelyet az alkalmazás konfigurációjának fejlesztésekor használ. Ez a bérlő tiszta állapotba kerül, és nem lesz konfigurálva a rendszerekkel való szinkronizálásra.

Az alkalmazások teszteléséhez jelentkezzen be egy teszt felhasználóval, és győződjön meg arról, hogy az összes funkció ugyanaz, mint az áttelepítés előtt. Ha a tesztelés során azt állapítja meg, hogy a felhasználóknak frissíteniük kell az [MFA](/azure/active-directory/authentication/howto-mfa-userstates) -vagy [SSPR](../authentication/tutorial-enable-sspr.md)-beállításokat, vagy ha ezt a funkciót az áttelepítés során adja hozzá, ne felejtse el hozzáadni a végfelhasználói kommunikációs tervhez. Lásd: [MFA](https://aka.ms/mfatemplates) és [SSPR](https://aka.ms/ssprtemplates) végfelhasználói kommunikációs sablonok.

Miután áttelepítette az alkalmazásokat, lépjen a [Azure Portal](https://aad.portal.azure.com/) annak teszteléséhez, hogy sikeres volt-e az áttelepítés. Kövesse az alábbi utasításokat:

- Válassza a **vállalati alkalmazások &gt; minden alkalmazás** lehetőséget, és keresse meg az alkalmazást a listából.

- Válassza **a &gt; felhasználók és csoportok kezelése** lehetőséget, ha legalább egy felhasználót vagy csoportot szeretne hozzárendelni az alkalmazáshoz.

- Válassza **a &gt; feltételes hozzáférés kezelése** lehetőséget. Tekintse át a szabályzatok listáját, és ellenőrizze, hogy nem blokkolja-e az alkalmazáshoz való hozzáférést [feltételes hozzáférési szabályzattal](../conditional-access/overview.md).

Az alkalmazás konfigurálásának módjától függően ellenőrizze, hogy az SSO megfelelően működik-e.

| Hitelesítéstípus      | Tesztelés                                             |
| ------------------------ | --------------------------------------------------- |
| **OAuth/OpenID Connect** | Válassza a **vállalati alkalmazások &gt; engedélyei** lehetőséget, és győződjön meg arról, hogy hozzájárult ahhoz, hogy a szervezetében az alkalmazás felhasználói beállításait használja. |
| **SAML-alapú egyszeri bejelentkezés** | Használja az **egyszeri bejelentkezés** alatt található [SAML-beállítások tesztelése](./debug-saml-sso-issues.md) gombot. |
| **Jelszó-alapú egyszeri bejelentkezés** | Töltse le és telepítse a [MyApps biztonságos bejelentkezési bővítményét](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension). Ez a bővítmény segítséget nyújt a szervezet olyan felhőalapú alkalmazásainak elindításához, amelyekhez egyszeri bejelentkezéses folyamatot kell használni. |

| **[Alkalmazásproxy](./application-proxy.md)** | Győződjön meg arról, hogy az összekötő fut, és hozzá van rendelve az alkalmazáshoz. További segítségért tekintse meg az [alkalmazásproxy hibaelhárítási útmutatóját](./application-proxy-troubleshoot.md) . |

### <a name="troubleshoot"></a>Hibaelhárítás

Ha problémákat tapasztal, tekintse meg az [alkalmazások hibaelhárítási útmutatóját](../app-provisioning/isv-automatic-provisioning-multi-tenant-apps.md) , ahol segítséget kaphat. Emellett megtekintheti a hibaelhárítási cikkeket is: [problémák az SAML-alapú egyszeri bejelentkezésre konfigurált alkalmazásokban való bejelentkezéssel](/troubleshoot/azure/active-directory/troubleshoot-sign-in-saml-based-apps)kapcsolatban.

### <a name="plan-rollback"></a>Terv visszaállítása

Ha az áttelepítés sikertelen, a legjobb stratégia a visszaállítás és a tesztelés. Az alábbi lépéseket követve csökkentheti az áttelepítési problémákat:

- **Képernyőkép készítése** az alkalmazás meglévő konfigurációjáról. Ha ismét újra kell konfigurálnia az alkalmazást, tekintse át a következőt:.

- Érdemes lehet megfontolni **az örökölt hitelesítésre mutató hivatkozásokat** is, ha problémák merültek fel a Felhőbeli hitelesítéssel.

- Az áttelepítés befejezése előtt **Ne módosítsa a meglévő konfigurációt** a korábbi identitás-szolgáltatóval.

- Kezdje el áttelepíteni **a több IDP támogató alkalmazásokat**. Ha valami hiba lép fel, bármikor módosíthatja az előnyben részesített identitásszolgáltató konfigurációját.

- Győződjön meg arról, hogy az alkalmazás felhasználói felületén van egy **visszajelzés gomb** vagy mutató az **ügyfélszolgálati** problémákra.

### <a name="exit-criteria"></a>Kilépési feltételek

Ebben a fázisban sikeres:

- Az egyes alkalmazások áttelepítésének meghatározása

- Áttelepítési eszközök áttekintése

- Tervezett tesztelési környezetek és csoportok tesztelése

- Tervezett visszaállítás

## <a name="phase-4-plan-management-and-insights"></a>4. fázis: a felügyelet és az eredmények tervezése

Az alkalmazások migrálása után a következőket kell biztosítania:

- A felhasználók biztonságosan érhetik el és kezelhetik

- A használat és az alkalmazás állapotának megfelelő információkhoz juthat

Javasoljuk, hogy a szervezete számára megfelelő módon tegye meg a következő műveleteket.

### <a name="manage-your-users-app-access"></a>A felhasználók alkalmazás-hozzáférésének kezelése

Miután áttelepítette az alkalmazásokat, számos módon bővítheti a felhasználói élményt

**Alkalmazások felderíthetővé tétele**

**Irányítsa a felhasználót** a [MyApps](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)portál felületére. Itt hozzáférhetnek az összes felhőalapú alkalmazáshoz, az [Azure ad Connect](../hybrid/whatis-azure-ad-connect.md)használatával elérhetővé tett alkalmazásokhoz, valamint az [alkalmazásproxy](./application-proxy.md) használatával, ha az alkalmazások eléréséhez engedélyekkel rendelkeznek.


A felhasználókat az alkalmazások felderítésére vonatkozó útmutatóban találja:

- A [meglévő egyszeri bejelentkezés](./view-applications-portal.md) funkció használatával **összekapcsolhatja a felhasználókat bármely alkalmazáshoz**


- Az [önkiszolgáló alkalmazások hozzáférésének](./manage-self-service-access.md)engedélyezése egy alkalmazáshoz, és a **felhasználók hozzáadása a kurátori alkalmazások hozzáadásához**

- [Alkalmazások elrejtése a végfelhasználók](./hide-application-from-user-portal.md) számára (alapértelmezett Microsoft-alkalmazások vagy más alkalmazások), hogy **az általuk igényelt alkalmazások jobban felderíthetők legyenek**

### <a name="make-apps-accessible"></a>Alkalmazások elérhetővé tétele

**Lehetővé teszi, hogy a felhasználók hozzáférjenek az alkalmazásokhoz a mobileszközökön**. A felhasználók az Intune által felügyelt böngészővel érhetik el az MyApps-portált [iOS](./hide-application-from-user-portal.md) 7,0 vagy újabb vagy [Android](./hide-application-from-user-portal.md) rendszerű eszközökön.

A felhasználók az **Intune által felügyelt böngészőt** tölthetik le:

- **Android-eszközök** esetén a [Google Play áruházból](https://play.google.com/store/apps/details?id=com.microsoft.intune)

- **Apple-eszközök** esetén az [Apple App Store áruházból](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) , vagy letöltheti az iOS rendszerhez készült [My apps Mobile alkalmazást](https://apps.apple.com/us/app/my-apps-azure-active-directory/id824048653)

**Lehetővé teszi, hogy a felhasználók megnyissák az alkalmazásaikat egy böngésző-bővítményből.**

A felhasználók [letöltheti a MyApps biztonságos bejelentkezési bővítményét](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) a [Chrome](https://chrome.google.com/webstore/detail/my-apps-secure-sign-in-ex/ggjhpefgjjfobnfoldnjipclpcfbgbhl) -ban, a [Firefoxban](https://addons.mozilla.org/firefox/addon/access-panel-extension/) vagy a [Microsoft Edge](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) -ben, és közvetlenül a böngésző menüsávján is elindíthatja az alkalmazásokat:

- **Az alkalmazások keresése és a legutóbb használt alkalmazások megjelenítése**

- **Automatikusan konvertálja** az [alkalmazásproxy](./application-proxy.md) -ben konfigurált belső URL-címeket a megfelelő külső URL-címekre. A felhasználók mostantól a már ismerős hivatkozásokkal is dolgozhatnak, függetlenül attól, hogy hol vannak.

**Lehetővé teszi, hogy a felhasználók megnyissák alkalmazásaikat a Office.com.**

A felhasználók a [Office.com](https://www.office.com/) **kereshetik meg az alkalmazásaikat, és a legutóbb használt alkalmazásaikat** a munkájuk helyétől jobbra láthatják.

### <a name="secure-app-access"></a>Biztonságos alkalmazás-hozzáférés

Az Azure AD egy központi hozzáférési helyet biztosít az áttelepített alkalmazások kezeléséhez. Lépjen a [Azure Portalra](https://portal.azure.com/) , és engedélyezze a következő képességeket:

- **Biztonságos felhasználói hozzáférés az alkalmazásokhoz.** A [feltételes hozzáférési szabályzatok](../conditional-access/overview.md)vagy az [identitások védelme](../identity-protection/overview-identity-protection.md)lehetővé teszi az alkalmazásokhoz való felhasználói hozzáférés biztosítását az eszköz állapota, helye és egyéb adatai alapján.

- **Automatikus kiépítés.** A felhasználók [automatikus kiépítés](../app-provisioning/user-provisioning.md) beállítása különböző, harmadik féltől származó SaaS-alkalmazásokkal, amelyekhez a felhasználóknak hozzá kell férniük. A felhasználói identitások létrehozása mellett a felhasználói identitások karbantartását és eltávolítását is magában foglalja az állapot vagy a szerepkörök változása miatt.

- A **felhasználói hozzáférés** **felügyeletének** delegálása. Szükség esetén engedélyezze az önkiszolgáló alkalmazások hozzáférését az alkalmazásokhoz, és *rendeljen hozzá egy üzleti jóváhagyót az alkalmazásokhoz való hozzáférés jóváhagyásához*. Az [önkiszolgáló csoportok kezelése](../enterprise-users/groups-self-service-management.md)az alkalmazások gyűjteményéhez rendelt csoportokhoz.

- **Rendszergazdai hozzáférés delegálása.** a **címtár-szerepkör** használata rendszergazdai szerepkör (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda vagy az alkalmazás fejlesztője) hozzárendeléséhez a felhasználóhoz.

### <a name="audit-and-gain-insights-of-your-apps"></a>Az alkalmazások naplózása és betekintése

A [Azure Portal](https://portal.azure.com/) az összes alkalmazás központi helyről történő naplózására is használható.

- Az alkalmazást * * vállalati alkalmazásokkal, naplózással vagy az [Azure ad Reporting API](../reports-monitoring/concept-reporting-api.md) -val való integrációval, a kedvenc eszközeibe való integrálással **naplózhatja** .

- **Megtekintheti a** **vállalati alkalmazásokat** használó alkalmazások engedélyeit, a OAuth/OpenID connectet használó alkalmazásokra vonatkozó engedélyeket.

- **Vállalati alkalmazások, bejelentkezések és** bejelentkezési eredmények **beszerzése** . Ugyanazokat az információkat érheti el az [Azure ad Reporting API](../reports-monitoring/concept-reporting-api.md) -ból.

- Az **alkalmazás használatának megjelenítése** az [Azure ad Power bi Content Pack csomagból](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

### <a name="exit-criteria"></a>Kilépési feltételek

Ebben a fázisban sikeres:

- Biztonságos alkalmazás-hozzáférés biztosítása a felhasználók számára

- Az áttelepített alkalmazások naplózásának és elemzésének kezelése

### <a name="do-even-more-with-deployment-plans"></a>Még több üzembe helyezési csomagok

A központi telepítési tervek végigvezetik az Azure AD-megoldások üzleti értékeit, megtervezését, megvalósításának lépéseit és felügyeletét, beleértve az alkalmazások áttelepítési forgatókönyveit is. A szolgáltatás minden olyan funkciót egyesít, amely az Azure AD-funkciók üzembe helyezésének és beszerzésének megkezdéséhez szükséges. Az üzembe helyezési útmutatók olyan tartalmakat foglalnak magukban, mint a Microsoft ajánlott eljárásai, a végfelhasználói kommunikáció, a tervezési útmutatók, a megvalósítás lépései, a tesztelési esetek és egyebek.

Számos [üzembe helyezési csomag](../fundamentals/active-directory-deployment-plans.md) elérhető a saját használatra, és így továbbra is!

### <a name="contact-support"></a>Kapcsolatfelvétel a támogatási szolgáltatással

A támogatási jegy létrehozásához vagy nyomon követéséhez, valamint az állapot figyeléséhez látogasson el a következő támogatási hivatkozásokra.

- **Azure-támogatás:** Meghívhatja [Microsoft ügyfélszolgálata](https://azure.microsoft.com/support) és megnyithat egy jegyet bármely Azure-hoz

Az identitás központi telepítésének problémája a Microsoft Nagyvállalati Szerződéstól függően.

- **FastTrack**: Ha nagyvállalati mobilitási és biztonsági (EMS) vagy prémium szintű Azure ad licenceket vásárolt, jogosult a [FastTrack programból](/enterprise-mobility-security/solutions/enterprise-mobility-fasttrack-program) bevezetési segítségre.

- **Folytassa a termékfejlesztési csapatot:** Ha több millió felhasználóval dolgozik egy nagyobb ügyfél-telepítésben, jogosult támogatni a Microsoft-fiók csapattól vagy a felhőalapú megoldások építésztől. A projekt központi telepítési bonyolultsága alapján közvetlenül dolgozhat az [Azure Identity Product Engineering csapatával.](https://aad.portal.azure.com/#blade/Microsoft_Azure_Marketplace/MarketplaceOffersBlade/selectedMenuItemId/solutionProviders)

- **Azure ad-identitás blogja:** Fizessen elő az [Azure ad Identity blogra](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/bg-p/Identity) , hogy naprakész maradjon a termék legújabb bejelentéseit, részletes felmerüléseit és a közvetlenül az Identity Engineering csapata által biztosított útiterv-információkat.
