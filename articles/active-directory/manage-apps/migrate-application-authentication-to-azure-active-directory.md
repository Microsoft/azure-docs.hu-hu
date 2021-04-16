---
title: Alkalmazáshitelesítés áttelepítése a Azure Active Directory
description: Ez a tanulmány az alkalmazáshitelesítés Azure AD-be való áttelepítésének tervezését és előnyeit részletezi.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 02/05/2021
ms.author: iangithinji
ms.reviewer: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3458f358c12ef33a337e50066e83b6e59273ccf1
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376749"
---
# <a name="migrate-application-authentication-to-azure-active-directory"></a>Alkalmazáshitelesítés áttelepítése a Azure Active Directory

## <a name="about-this-paper"></a>A tanulmányról

Ez a tanulmány az alkalmazáshitelesítés Azure AD-be való áttelepítésének tervezését és előnyeit részletezi. Azure-rendszergazdák és identitás-szakemberek számára készült.

A folyamat négy fázisra való lebontása részletes tervezési és kilépési feltételekkel úgy lett kialakítva, hogy segítsen megtervezni a migrálási stratégiát, és megérteni, hogyan támogatja az Azure AD-hitelesítés a szervezeti céljait.

## <a name="introduction"></a>Bevezetés

Napjainkban a szervezetnek alkalmazásokra (alkalmazásokra) van szüksége a felhasználók számára a munka munkához. Az alkalmazásokat valószínűleg minden nap hozzáadja, fejleszti vagy kivezeti. A felhasználók számos vállalati és személyes eszközről és helyről férnek hozzá ezekhez az alkalmazásokhoz. Számos módon nyitnak meg alkalmazásokat, például:

- a vállalat kezdőlapján vagy portálján keresztül

- a böngészőjük könyvjelzőinek használatával

- szolgáltatott szoftver (SaaS) alkalmazások szállítói URL-címével

- a felhasználók asztalára vagy mobileszközére mobileszköz-kezelési (MDM/MAM) megoldáson keresztül közvetlenül lekért hivatkozások

Az alkalmazások valószínűleg a következő típusú hitelesítést használják:

- Helyszíni összevonási megoldások (például Active Directory összevonási szolgáltatások (AD FS) (ADFS) és pingelés)

- Active Directory (például Kerberos-hitelesítés és Windows-Integrated hitelesítés)

- Egyéb felhőalapú identitás- és hozzáférés-kezelési (IAM) megoldások (például Okta vagy Oracle)

- Helyszíni webes infrastruktúra (például IIS és Apache)

- Felhőben üzemeltetett infrastruktúra (például Az Azure és az AWS)

**Annak biztosítása érdekében, hogy a felhasználók könnyedén és biztonságosan férnek hozzá az alkalmazásokhoz, a cél az, hogy egyetlen hozzáférés-vezérléssel és szabályzatokkal rendelkezik a helyszíni és a felhőalapú környezetekben.**

[Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) egy univerzális identitásplatformot kínál, amely egyetlen identitást biztosít a felhasználók, partnerek és ügyfelek számára a kívánt alkalmazások eléréséhez, és bármilyen platformról és eszközről együttműködhet.

![A kapcsolat Azure Active Directory diagramja](media/migrating-application-authentication-to-azure-active-directory-1.jpg)

Az Azure AD teljes [körű identitáskezelési képességekkel rendelkezik.](../fundamentals/active-directory-whatis.md#which-features-work-in-azure-ad) Az alkalmazás hitelesítésének és az Azure AD-beli engedélyezésnek a szabványosításával kihozhatja a képességek által nyújtott előnyöket.

További migrálási erőforrásokat itt talál: [https://aka.ms/migrateapps](./migration-resources.md)

## <a name="benefits-of-migrating-app-authentication-to-azure-ad"></a>Az alkalmazáshitelesítés Azure AD-be való áttelepítésének előnyei

Az alkalmazáshitelesítés Azure AD-be való áthelyezésével kezelheti a kockázatokat és a költségeket, növelheti a hatékonyságot, valamint megfelelhet a megfelelőségi és irányítási követelményeknek.

### <a name="manage-risk"></a>A kockázatok kezelése

Az alkalmazások védelme érdekében teljes körűen meg kell tekintenie az összes kockázati tényezőt. Az alkalmazások Azure AD-be való áttelepítése konszolidálja a biztonsági megoldásokat. A következőt használhatja:

- Az alkalmazások és a kapcsolódó vállalati [](../conditional-access/overview.md)adatok biztonságos felhasználói hozzáférésének javítása feltételes hozzáférési szabályzatokkal, [többtényezős](../authentication/concept-mfa-howitworks.md)hitelesítéssel és valós idejű kockázatalapú [identitásvédelmi technológiákkal.](../identity-protection/overview-identity-protection.md)

- Az emelt szintű felhasználók környezethez való hozzáférésének védelme a rendszergazdai [hozzáféréssel.](../../azure-resource-manager/managed-applications/request-just-in-time-access.md)

- Az [Azure AD több-bérlős, földrajzi eloszlású,](https://cloudblogs.microsoft.com/enterprisemobility/2014/09/02/azure-ad-under-the-hood-of-our-geo-redundant-highly-available-distributed-cloud-directory/)magas rendelkezésre állású kialakítása a legkritikusabb üzleti igényeknek megfelelően.

- Az esetleg már üzembe helyezett [](https://aka.ms/secure-hybrid-access) biztonságos hibrid hozzáférési partnerintegrációink egyikével megvédheti örökölt alkalmazásait.

### <a name="manage-cost"></a>Költségek kezelése

Előfordulhat, hogy a szervezet több identitás-hozzáférés-kezelési (IAM) megoldással is rendelkezik. Az egyik Azure AD-infrastruktúrára való miggálás lehetőséget nyújt az IAM-licencektől (helyszíni vagy felhőbeli) függőségek és az infrastruktúra költségeinek csökkentésére. Ha esetleg már fizetett az Azure AD-nek Microsoft 365-licencekkel, nincs ok egy másik IAM-megoldás további költségeinek kifizetésre.

**Az Azure AD-val a következővel csökkentheti az infrastruktúra költségeit:**

- Biztonságos távoli hozzáférés biztosítása helyszíni alkalmazásokhoz az [Azure AD alkalmazásproxy.](./application-proxy.md)

- Az alkalmazások elkülönítése a bérlőben használt, a hitelesítő adatokkal kapcsolatos megközelítéstől az Azure AD megbízható univerzális identitásszolgáltatóként [való beállításával.](../hybrid/plan-connect-user-signin.md#choosing-the-user-sign-in-method-for-your-organization)

### <a name="increase-productivity"></a>A hatékonyság növelése

A gazdasági és biztonsági előnyök az Azure AD bevezetéséhez vezetik a szervezeteket, de a teljes körű alkalmazás és megfelelőség nagyobb valószínűséggel érhető el, ha a felhasználók számára is előnyös. Az Azure AD-val a következő funkciókat használhatja:

- Javíthatja a végfelhasználói egyszeri [Sign-On (SSO)](./what-is-single-sign-on.md) élményét az alkalmazásokhoz való zökkenőmentes és biztonságos hozzáféréssel, bármilyen eszközről és helyről.

- Használjon önkiszolgáló IAM-képességeket, például új jelszó önkiszolgáló alaphelyzetbe állítását és [önkiszolgáló csoportkezelést.](../enterprise-users/groups-self-service-management.md) [](../authentication/concept-sspr-howitworks.md)

- Csökkentheti az adminisztrációs terhelést azáltal, hogy minden felhasználóhoz egyetlen identitást kell külön-külön a felhőben és a helyszíni környezetekben is:

  - [Felhasználói fiókok automatizálhatóak](../app-provisioning/user-provisioning.md) [(az Azure AD katalógusában)](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)az Azure AD-identitások alapján
  - Az alkalmazásokat a panel MyApps paneljére Azure Portal [](https://portal.azure.com/)

- A Microsoft [Identity Platform](../develop/v2-overview.md) és a Microsoft Authentication Library (MSAL) használatával lehetővé teheti a fejlesztők számára az alkalmazások biztonságos hozzáférését, és javíthatják a végfelhasználói élményt.

- Felhőbeli erőforrásokhoz való hozzáférés lehetővé teszi a partnerek számára [az Azure AD B2B-együttműködés használatával.](../external-identities/what-is-b2b.md) A felhőerőforrások megszüntetik a partnerekkel való pont–pont összevonás konfigurálásával kapcsolatos többletterhelést.

### <a name="address-compliance-and-governance"></a>A megfelelőség és az irányítás kezelése

Az integrált naplózási eszközök és API-k használatával biztosíthatja a jogszabályi követelményeknek való megfelelőséget a vállalati hozzáférési szabályzatok betartatásával és az alkalmazásokhoz és a kapcsolódó adatokhoz való felhasználói hozzáférés figyelése révén. Az Azure AD-val biztonsági incidens- és eseményfigyelési [(SIEM)-eszközöket (SIEM)](../reports-monitoring/plan-monitoring-and-reporting.md)eszközök használatával figyelheti az alkalmazásokba való bejelentkezéseket. A jelentéseket a portálról vagy API-kból is elérheti, és programozott módon naplózhatja, hogy ki fér hozzá az alkalmazásokhoz, és hozzáférési felülvizsgálatokkal eltávolíthatja az inaktív felhasználók hozzáférését.

## <a name="plan-your-migration-phases-and-project-strategy"></a>A migrálási fázisok és a projektstratégia megterve

Ha a technológiai projektek meghiúsulnak, azt gyakran a nem egyező elvárások, a megfelelő érdekelt felek nem vesznek részt, vagy a kommunikáció hiánya miatt. A projekt megtervezésével győződjön meg a sikerről.

### <a name="the-phases-of-migration"></a>A migrálás fázisai

Mielőtt belefogunk az eszközökbe, tisztában kell lennie a migrálási folyamat végigfogatásán. Számos, az ügyfelekhez való közvetlen kapcsolattal kapcsolatos workshopon keresztül a következő négy szakaszt javasoljuk:

![A migrálás fázisai diagramja](media/migrating-application-authentication-to-azure-active-directory-2.jpg)

### <a name="assemble-the-project-team"></a>A projektcsapat összeállítása

Az alkalmazás migrálása csapatmunka, és gondoskodnia kell arról, hogy minden fontos pozíció meg legyen töltve. Fontos a vezető üzleti vezetők támogatása. Győződjön meg arról, hogy a megfelelő vezető szponzorokat, üzleti döntéshozókat és témaszakértőket (SRE-ket) bevonja.

A migrálási projekt során egy személy több szerepkört is betölthet, vagy több személy is betöltheti az egyes szerepköröket a szervezet méretétől és szerkezetétől függően. Előfordulhat, hogy más olyan csapatoktól is függ, amelyek kulcsfontosságú szerepet játszanak a biztonsági környezetben.

Az alábbi táblázat a főbb szerepköröket és azok hozzájárulását tartalmazza:

| Szerepkör          | Közreműködések                                              |
| ------------- | ---------------------------------------------------------- |
| **Projektvezető** | A projektvezetőnek kell figyelembe vennie a projekt vezetését, beleértve a következőket:<br /> – vezetői támogatás szerzése<br /> – az érdekelt felek bebevonásával<br /> – ütemezések, dokumentáció és kommunikáció kezelése |
| **Identitástervező /Azure AD alkalmazás rendszergazda** | Ezek a következőkért felelősek:<br /> – a megoldás megtervezése az érdekelt felekkel együttműködésben<br /> – a megoldás tervezési és üzemeltetési eljárásainak dokumentálása az üzemeltetési csapatnak való átosztáshoz<br /> – az üzem előtti és az éles környezetek kezelése |
| **Helyszíni AD-üzemeltetési csapat** | A különböző helyszíni identitásforrások, például AD-erdők, LDAP-címtárak, HR-rendszerek stb. kezeléséhez tartozó szervezet.<br /> – a szinkronizálás előtt szükséges szervizelési feladatok végrehajtása<br /> – Adja meg a szinkronizáláshoz szükséges szolgáltatásfiókokat<br /> – hozzáférés beállítása az Azure AD-hez való összevonás konfiguráláshoz |
| **It-támogatási vezető** | Az it-támogatási szervezet egy olyan képviselőjét, aki segítséget nyújthat a változás támogatási szolgálata szempontjából. |
| **Biztonsági tulajdonos**  | A biztonsági csapat egy olyan képviselőjét, aki gondoskodik arról, hogy a terv megfeleljen a szervezet biztonsági követelményeinek. |
| **Alkalmazás technikai tulajdonosai** | Tartalmazza az Azure AD-val integrálható alkalmazások és szolgáltatások műszaki tulajdonosait. Ezek biztosítják az alkalmazások identitásattribútumait, amelyeknek bele kell foglalniuk a szinkronizálási folyamatba. Általában kapcsolatban áll a CSV-képviselőkkel. |
| **Alkalmazástulajdonosok** | Olyan reprezentatív munkatársak, akik a felhasználó szemszögéből meg tudnak adni adatokat a felhasználói élményről és a változás hasznosságról, és az alkalmazás általános üzleti aspektusával is rendelkeznek, amely magában foglalhatja a hozzáférések felügyeletét is. |
| **Felhasználók próbacsoportja** | Azok a felhasználók, akik a mindennapi munkájuk és a próbaélmény részeként tesztelnek, és visszajelzést adnak a többi üzembe helyezéshez. |

### <a name="plan-communications"></a>A kommunikáció tervezése

A siker kulcsa a hatékony üzleti együttműködés és kommunikáció. Fontos, hogy az érdekelt felek és a végfelhasználók számára elérhető legyen az információhoz való lehetőség, és folyamatosan értesülni kell az ütemezési frissítésekről. Tájékoztassa mindenkit a migrálás értékéről, a várható ütemtervről, és az ideiglenes üzletkimaradások tervezéséről. Többféle utat is használhat, például eligazítási munkameneteket, e-maileket, egy-az-egyhez értekezleteket, szalagcímeket és városokat.

Az alkalmazáshoz választott kommunikációs stratégia alapján talán emlékeztetheti a felhasználókat a függőben lévő állásidőre. Azt is ellenőrizze, hogy nincsenek-e olyan legutóbbi módosítások vagy üzleti hatások, amelyek miatt el kellene halasztani az üzembe helyezést.

Az alábbi táblázatban megtalálja a minimális javasolt kommunikációt az érdekelt felek tájékoztatásához:

**Tervfázisok és projektstratégia:**

| Kommunikáció      | Célközönség                                          |
| ------------------ | ------------------------------------------------- |
| A projekt tudatossága és üzleti/műszaki értéke | A végfelhasználók kivételével minden |
| Próbaalkalmazások lekérése | - Alkalmazás üzleti tulajdonosai<br />- Alkalmazás műszaki tulajdonosai<br />- Architects and Identity team |

**1. fázis – Felderítés és hatókör:**

| Kommunikáció      | Célközönség                                          |
| ------------------ | ------------------------------------------------- |
| - Alkalmazásinformációk kérése<br />– A hatókör-hatóköri gyakorlat eredménye | - Alkalmazás technikai tulajdonosai<br />- Alkalmazás üzleti tulajdonosai |

**2. fázis – Alkalmazások besorolása és próbatervek:**

| Kommunikáció      | Célközönség                                          |
| ------------------ | ------------------------------------------------- |
| - Besorolások eredménye, és ez mit jelent a migrálási ütemezéshez<br />- Előzetes migrálási ütemezés | - Alkalmazás technikai tulajdonosai<br /> - Alkalmazás üzleti tulajdonosai |

**3. fázis – A migrálás és a tesztelés megtervelése:**

| Kommunikáció      | Célközönség                                          |
| ------------------ | ------------------------------------------------- |
| - Az alkalmazás migrálási tesztelésének eredménye | - Alkalmazás technikai tulajdonosai<br />- Alkalmazás üzleti tulajdonosai |
| – Értesítés a migrálásról, és az eredményül kapott végfelhasználói élmény magyarázata.<br />- Állásidő közeledik, és teljes körű kommunikáció, beleértve azt is, hogy mit kell tenniük, visszajelzést adni, és segítségre van szüksége | - Végfelhasználók (és minden más) |

**4. fázis – Elemzések kezelése és betekintés:**

| Kommunikáció      | Célközönség                                          |
| ------------------ | ------------------------------------------------- |
| Rendelkezésre álló elemzések és hozzáférés | - Alkalmazás technikai tulajdonosai<br />- Alkalmazás üzleti tulajdonosai |

### <a name="migration-states-communication-dashboard"></a>Migrálási államok kommunikációs irányítópultja

A migrálási projekt általános állapotának kommunikálása létfontosságú, mivel előrehaladást mutat, és segít az alkalmazástulajdonosoknak, akiknek az alkalmazásai hamarosan a migrálásra készülnek, felkészülnek az áthelyezésre. A migrálás során egy egyszerű irányítópultot Power BI vagy más jelentéskészítő eszközökkel, hogy betekintést nyújtson az alkalmazások állapotába.

A következő áttelepítési államokat érdemes használnia:

| Migrálási államok       | Műveletterv                                   |
| ---------------------- | --------------------------------------------- |
| **Kezdeti kérés** | Keresse meg az alkalmazást, és további információért forduljon a tulajdonoshoz |
| **Értékelés kész** | Az alkalmazás tulajdonosa kiértékeli az alkalmazás követelményeit, és visszaküldi az alkalmazás kérdőívét</td>
| **Konfigurálás folyamatban** | Az Azure AD-hitelesítés kezeléséhez szükséges módosítások kidolgozása |
| **A konfiguráció tesztelése sikeres** | Értékelje ki a módosításokat, és hitelesítse az alkalmazást a tesztkörnyezetben használt Azure AD-bérlő tesztkörnyezetében |
| **Az éles konfiguráció sikeres** | Módosítsa úgy a konfigurációkat, hogy azok az éles AD-bérlőn is működjön, és értékeljék az alkalmazáshitelesítést a tesztkörnyezetben |
| **Teljes / Kijelentkezés** | Az alkalmazás módosításainak üzembe helyezése az éles környezetben, és a végrehajtása az éles Azure AD-bérlőn |

Ez biztosítja, hogy az alkalmazástulajdonosok tudják, milyen ütemezés szerint telepítik át az alkalmazásokat, és milyen eredményeket fognak elérni a már migrált alkalmazások. Érdemes lehet hivatkozásokat is megszabadolni a hibakövető adatbázishoz, hogy a tulajdonosok be tudják- és megtekinteni a migrált alkalmazások problémáit.

### <a name="best-practices"></a>Ajánlott eljárások

Az alábbiakban az ügyfeleink és a partnerek sikertörténeteit és a javasolt ajánlott eljárásokat íme:

- [Öt tipp a](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Five-tips-to-improve-the-migration-process-to-Azure-Active/ba-p/445364) Migrálási folyamat javításához, Azure Active Directory a Patriot Consulting partnerhálózatunk tagja, amely arra összpontosít, hogy segítsen az ügyfeleknek a Microsoft-felhőmegoldások biztonságos üzembe helyezésében.

- Az IAM- és kockázatkezelési megoldásokra összpontosító partner, az Edgile edgile kockázatkezelési stratégiáját fejleszti ki az [Azure AD-alkalmazások](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Develop-a-risk-management-strategy-for-your-Azure-AD-application/ba-p/566488) migrálásához.

## <a name="phase-1-discover-and-scope-apps"></a>1. fázis: Alkalmazások felderítése és hatóköre

**Az alkalmazásfelderítés és -elemzés egy alapvető gyakorlat, amely jó kezdést tesz lehetővé.** Lehet, hogy nem tud mindent, ezért készüljön fel az ismeretlen alkalmazások elhelyezésére.

### <a name="find-your-apps"></a>Alkalmazások megkerese

Az alkalmazások migrálásának első döntési pontja az, hogy mely alkalmazásokat kell átemelni, melyeknek meg kell maradniuk, és mely alkalmazásokat kell elavultnak lennie. Mindig van lehetőség a szervezetben nem használt alkalmazások elavultként való használatára. A cégnél többféleképpen is megkereshet alkalmazásokat. **Az alkalmazások felderítése közben győződjön meg arról, hogy a fejlesztés alatt áll és a tervezett alkalmazásokat is tartalmazza. Az Azure AD-t minden jövőbeli alkalmazásban használhatja a hitelesítéshez.**

**A Active Directory összevonási szolgáltatások (AD FS) (AD FS) használatával gyűjtse össze a megfelelő alkalmazásleltárt:**

- **Használja az Azure AD Connect Health eszközt.** Ha licenccel prémium szintű Azure AD, javasoljuk, hogy [](../hybrid/how-to-connect-health-adfs.md) Azure AD Connect Health a helyszíni környezetben az alkalmazáshasználat elemzéséhez. Az [ADFS-alkalmazásjelentés](./migrate-adfs-application-activity.md) (előzetes verzió) segítségével felderítheti az áttelepíthető ADFS-alkalmazásokat, és kiértékelheti az áttelepíteni szükséges alkalmazás készenlétét. A migrálás befejezése [](/cloud-app-security/set-up-cloud-discovery) után üzembe helyezheti Cloud Discovery, amely lehetővé teszi az árnyék-ita- és árnyék-árnyék-figyelés folyamatos monitorozását a szervezetben, ha már a felhőben van.

- **AD FS naplók elemezve.** Ha nem rendelkezik licenccel prémium szintű Azure AD, javasoljuk, hogy használja az ADFS-ből az Azure AD-be a [PowerShellen](https://github.com/AzureAD/Deployment-Plans/tree/master/ADFS%20to%20AzureAD%20App%20Migration)alapuló alkalmazásáttelepítési eszközöket. . Tekintse meg [a megoldási útmutatót:](./migrate-adfs-apps-to-azure.md)

[Alkalmazások áttelepítése Active Directory összevonási szolgáltatások (AD FS) (AD FS) az Azure AD-be.](./migrate-adfs-apps-to-azure.md)

### <a name="using-other-identity-providers-idps"></a>Más identitásszolgáltatók (IDP-k) használata

Más identitásszolgáltatók (például az Okta vagy a Ping) esetében az eszközeik használatával exportálhatja az alkalmazásleltárt. Érdemes megfontolni a szervezetben lévő webalkalmazások Active Directory való regisztrációját.

### <a name="using-cloud-discovery-tools"></a>Felhőfelderítési eszközök használata

A felhőalapú környezetben gazdag átláthatóságra, az adatok utazásának szabályozására, valamint kifinomult elemzésekre van szükség az összes felhőszolgáltatás kibertámadásának megkeresése és elleni védelem érdekében. A felhőalkalmazások leltárát a következő eszközökkel gyűjtheti össze:

- **Cloud Access Security Broker (CASB)**– A [CASB](/cloud-app-security/) általában a tűzfal mellett működik, hogy betekintést nyújtson az alkalmazottak felhőalapú alkalmazáshasználatába, és segít megvédeni a vállalati adatokat a kiberbiztonsági fenyegetésektől. A CASB-jelentés segítségével meghatározhatja a szervezetében leggyakrabban használt alkalmazásokat, valamint az Azure AD-be való mirate korai célokat.

- **Cloud Discovery** – A Cloud Discovery [segítségével](/cloud-app-security/set-up-cloud-discovery)betekintést nyerhet a felhőalkalmazások használatára, és felderítheti a nem megfelelő vagy árnyék-it-alkalmazásokat.

- **API-k** – A felhőalapú infrastruktúrához csatlakoztatott alkalmazások esetében az ilyen rendszerek API-it és eszközeit használva megkezdheti az üzemeltetett alkalmazások leltárának leltározását. Az Azure-környezetben:

  - A [Get-AzureWebsite](/powershell/module/servicemanagement/azure.service/get-azurewebsite) parancsmag használatával lekért információk az Azure-webhelyekről.

  - A [Get-AzureRMWebApp](/powershell/module/azurerm.websites/get-azurermwebapp) parancsmag használatával lekért információ az Azure-Web Apps.
T
  - A Microsoft IIS-on futó összes alkalmazást megtalálja a Windows parancssorában a [AppCmd.exe. ](/iis/get-started/getting-started-with-iis/getting-started-with-appcmdexe#working-with-sites-applications-virtual-directories-and-application-pools)

  - Az [Alkalmazások és](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) [szolgáltatásnév használatával](/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity) információkat kap egy Azure AD-címtárban található alkalmazásról és alkalmazáspéldányról.

### <a name="using-manual-processes"></a>Manuális folyamatok használata

A fent ismertetett automatizált megközelítések alkalmazása után jól fogja kezelni az alkalmazásokat. Érdemes lehet azonban a következőket tenni, hogy minden felhasználói hozzáférési területen megfelelő legyen a lefedettség:

- Lépjen kapcsolatba a szervezet különböző üzleti tulajdonosaival, és keresse meg a szervezetben használt alkalmazásokat.

- Futtatassa a HTTP-ellenőrzési eszközt a proxykiszolgálón, vagy elemezze a proxynaplókat, hogy lássa, hol van általában irányítva a forgalom.

- Tekintse át a népszerű vállalati portál webhelyein található weblogokat, hogy lássa, milyen hivatkozásokhoz férnek hozzá a leggyakrabban a felhasználók.

- A vezetők vagy más kulcsfontosságú üzleti tagok segítségével biztosíthatja, hogy az üzlet szempontjából kritikus fontosságú alkalmazásokat is lefedje.

### <a name="type-of-apps-to-migrate"></a>Az átemelni szükséges alkalmazások típusa

Ha megtalálja az alkalmazásokat, a következő típusú alkalmazásokat fogja azonosítani a szervezetben:

- Már modern hitelesítési protokollokat felhasználó alkalmazások

- Olyan alkalmazások, amelyek modernizálásra választott régi hitelesítési protokollokat használnak

- Olyan alkalmazások, amelyek régi hitelesítési protokollokat használnak, és nem a modernizálást választják

- Új üzletági (LoB-) alkalmazások

### <a name="apps-that-use-modern-authentication-already"></a>Már modern hitelesítést felhasználó alkalmazások

A már modernizált alkalmazások a legnagyobb valószínűséggel lesznek áthelyezve az Azure AD-be. Ezek az alkalmazások már modern hitelesítési protokollokat használnak (például SAML vagy OpenID Connect), és újrakonfigurálhatóak az Azure AD-val való hitelesítéshez.

Az [Azure AD](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) alkalmazás-katalógusban található lehetőségek mellett ezek olyan alkalmazások is, amelyek már léteznek a szervezetben, vagy olyan gyártótól származó harmadik féltől származó alkalmazások, amelyek nem részei az Azure AD-katalógusnak (nem katalógusbeli[alkalmazások).](./add-application-portal.md)

Régi alkalmazások, amelyek modernizálását választja

A modernizálni kívánt örökölt alkalmazások esetében az azure AD-be való alaphitelesítéshez és [](https://developer.microsoft.com/graph/gallery/?filterBy=Samples,SDKs) engedélyezéshez való [](https://www.microsoft.com/security/operations/intelligence?rtc=1) áttérés minden olyan hatékonyságot és adatgazdagságot biztosít, Microsoft Graph és Intelligens biztonsági gráf funkciókkal rendelkezik.

**Javasoljuk,** hogy frissítse ezen alkalmazások hitelesítési veremkódját az örökölt protokollról (például Windows-Integrated Authentication, Kerberos Constrained Delegation, HTTP Headers-based Authentication) egy modern protokollra (például SAML vagy OpenID Connect).

### <a name="legacy-apps-that-you-choose-not-to-modernize"></a>Örökölt alkalmazások, amelyek modernizálása NEM

Az örökölt hitelesítési protokollokat használó egyes alkalmazások esetében előfordulhat, hogy a hitelesítés modernizálása üzleti okokból nem a megfelelő teendő. Ezek közé tartoznak az alábbi típusú alkalmazások:

- Az alkalmazások megfelelőségi vagy ellenőrzési okokból a helyszínen vannak.

- Helyszíni identitáshoz vagy összevonási szolgáltatóhoz csatlakoztatott alkalmazások, amelyeken nem szeretne változtatni.

- Helyszíni hitelesítési szabványok használatával fejlesztett alkalmazások, amelyek áthelyezését nem tervezi

Az Azure AD nagyszerű előnyökkel jár ezeknek a régebbi alkalmazásoknak, mivel anélkül engedélyezheti az Azure AD modern [](./access-panel-manage-self-service-access.md)biztonsági és cégirányítási funkcióit, mint a [Multi-Factor Authentication,](../authentication/concept-mfa-howitworks.md)a feltételes [hozzáférés,](../conditional-access/overview.md)az [Identity Protection,](../identity-protection/index.yml)a delegált alkalmazás-hozzáférés és a hozzáférési felülvizsgálatok az alkalmazásokon anélkül, hogy az alkalmazásra lenne szükség. [](../governance/manage-user-access-with-access-reviews.md#create-and-perform-an-access-review)

Első  lépésként kiterjesztheti ezeket az alkalmazásokat a felhőbe az Azure AD [alkalmazásproxy-val](./application-proxy-configure-single-sign-on-password-vaulting.md) egyszerű hitelesítési módszerrel [](https://azure.microsoft.com/services/active-directory/sso/secure-hybrid-access/) (például Jelszótartó használatával), hogy a felhasználókat gyorsan migrálják, vagy az esetleg már üzembe helyezett alkalmazásküldetési vezérlők partnerintegrációi révén.

### <a name="new-line-of-business-lob-apps"></a>Új üzletági (LoB)-alkalmazások

LoB-alkalmazásokat általában a szervezet házon belül való használatra fejleszt. Ha új alkalmazások vannak folyamatban, javasoljuk, hogy a [Microsoft Identity Platformot](../develop/v2-overview.md) használja a OpenID Connect.

### <a name="apps-to-deprecate"></a>Elavult alkalmazások

Az egyértelmű tulajdonos nélküli alkalmazások, valamint az egyértelmű karbantartás és monitorozás biztonsági kockázatot jelent a szervezet számára. Vegye fontolóra az alkalmazások elavultként való elavultságát, ha:

- a **funkciójuk nagymértékben redundáns más** rendszerekkel • nincs **üzlettulajdonos**

- egyértelműen **nincs használat.**

Javasoljuk, hogy ne elavultsa a nagy **hatású, üzleti fontosságú alkalmazásokat.** Ilyen esetekben a vállalat tulajdonosaival együtt kell meghatározni a megfelelő stratégiát.

### <a name="exit-criteria"></a>Kilépési feltételek

Ebben a fázisban a következő műveletekkel lehet sikeres:

- A migrálás hatókörében (az Azure AD-be való migrálás után kivezetheti a rendszert)

- Az alábbi alkalmazásokat tartalmazó lista:

  - Milyen rendszerekhez csatlakoznak ezek az alkalmazások?
  - Honnan és milyen eszközökön férnek hozzájuk a felhasználók
  - Migrálják, elavultják vagy csatlakoztatják őket a [Azure AD Connect.](../hybrid/whatis-azure-ad-connect.md)

> [!NOTE]
> Az [Alkalmazásfelderítési](https://download.microsoft.com/download/2/8/3/283F995C-5169-43A0-B81D-B0ED539FB3DD/Application%20Discovery%20worksheet.xlsx) munkalap letöltésével rögzítheti az Azure AD-hitelesítésre áttérni kívánt alkalmazásokat, valamint azokat, amelyek elhagyása után a kezelésük a következő [Azure AD Connect.](../hybrid/whatis-azure-ad-connect.md)

## <a name="phase-2-classify-apps-and-plan-pilot"></a>2. fázis: Alkalmazások besorolása és próbatervek

Az alkalmazások migrálásának besorolása fontos gyakorlat. Nem minden alkalmazást kell egyszerre migrálni és áttelepíteni. Miután összegyűjtötte az egyes alkalmazásokkal kapcsolatos információkat, észszerűsítheti, hogy mely alkalmazásokat kell először migrálni, ami további időt is okozhat.

### <a name="classify-in-scope-apps"></a>Hatókörbe tartozó alkalmazások besorolása

Ennek egyik módja az üzleti kritikusság, a használat és az élettartam tengelye, amelyek több tényezőtől függenek.

### <a name="business-criticality"></a>Üzleti kritikusság

Az üzleti kritikusság minden vállalkozás esetében különböző dimenziókat fog figyelembe venni, de a két megfontolni javasolt intézkedés a **funkciók,** a funkciók és a **felhasználói profilok.** A redundáns vagy elavult funkcióknál magasabb értékű, egyedi funkciókkal rendelkezik alkalmazásokat rendelhet hozzá.

![A funkciók és felhasználói profilok & diagramja](media/migrating-application-authentication-to-azure-active-directory-3.jpg)

### <a name="usage"></a>Használat

A magas **kihasználtságú alkalmazásoknak** magasabb értéket kell kapniuk, mint az alacsony kihasználtságú alkalmazásoknak. Magasabb értéket rendelhet a külső, vezető vagy biztonsági csapat felhasználóit használó alkalmazásokhoz. A migrálási portfólió minden alkalmazásában töltse ki ezeket az értékeléseket.

![A felhasználói mennyiség és a felhasználói szélességek spektrumát ábrázoló diagram](media/migrating-application-authentication-to-azure-active-directory-4.jpg)

Miután meghatározta az üzleti kritikusság és használat értékeit, meghatározhatja az alkalmazás **élettartamát,** és létrehozhat egy prioritási mátrixot. Tekintse meg az alábbi mátrixot:

![Háromszögdiagram a használat, a várható élettartam és az üzleti kritikusság közötti kapcsolatokról](media/migrating-application-authentication-to-azure-active-directory5.jpg)

### <a name="prioritize-apps-for-migration"></a>Alkalmazások rangsorolása migráláshoz

Dönthet úgy, hogy az alkalmazás migrálását a vállalat igényeitől függően a legalacsonyabb prioritású alkalmazásokkal vagy a legmagasabb prioritású alkalmazásokkal kezdi.

Ha nem rendelkezik tapasztalattal az Azure AD és az Identity  szolgáltatások használatában, érdemes lehet először az Azure AD-be áttérni a legalacsonyabb prioritású alkalmazásokra. Ez minimálisra csökkenti az üzletre gyakorolt hatást, és ön is építhet. Miután sikeresen áthelyezte ezeket az alkalmazásokat, és magabiztosan bízik meg az érintett felekben, folytathatja a többi alkalmazás áttelepítését.

Ha nincs egyértelmű prioritás, érdemes először az Azure [AD-katalógusban](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps) található alkalmazásokat áttérni, és támogatni több identitásszolgáltatót (ADFS vagy Okta), mert azok könnyebben integrálhatók. Valószínű, hogy ezek az alkalmazások **a legmagasabb** prioritású alkalmazások a szervezetben. Az SaaS-alkalmazások Azure AD-val való integrálásának [](../saas-apps/tutorial-list.md) segítée érdekében kifejlesztettünk egy oktatóanyag-gyűjteményt, amely végigveszi a konfiguráláson.

Ha van egy határidő az alkalmazások áttelepítésére, ez a legmagasabb prioritású alkalmazások gyűjtő veszi át a nagyobb számítási feladatot. Végül kiválaszthatja az alacsonyabb prioritású alkalmazásokat, mivel azok nem módosítják a költségeket annak ellenére, hogy áthelyezte a határidő lejártakor. Még ha meg is kell újítania a licencet, az csak kis mennyiségben lesz érvényes.

Ezen besorolás mellett és a migrálás sürgősségétől függően érdemes  lehet olyan migrálási ütemtervet is beütemeket összeadni, amelyeken belül az alkalmazástulajdonosoknak kapcsolatba kell lépniük az alkalmazásaik migrálását. A folyamat végén meg kell lennie az összes olyan alkalmazás listájának, amely rangsorolásos gyűjtőkben található a migráláshoz.

### <a name="document-your-apps"></a>Az alkalmazások dokumentálása

Először is gyűjti össze az alkalmazások legfontosabb adatait. Az [Alkalmazásfelderítés](https://download.microsoft.com/download/2/8/3/283F995C-5169-43A0-B81D-B0ED539FB3DD/Application%20Discovery%20worksheet.xlsx)munkalap segít gyorsan meghozni a migrálási döntéseket, és rövid idő alatt javaslatot tesz az üzleti csoportnak.

A migrálási döntés meghozatala során fontos információk a következőket tartalmazzák:

- **Alkalmazás neve** – mit nevezünk az alkalmazásnak a vállalat számára?

- **Alkalmazástípus** – Külső SaaS-alkalmazásról van-e még ilyen? Egy egyéni üzletági webalkalmazást? API-t?

- **Üzleti kritikusság** – magas kritikussága van? Alacsony? Vagy valahol a kettő között?

- **Felhasználói hozzáférés mennyisége** – mindenki hozzáfér ehhez az alkalmazáshoz, vagy csak néhány ember?

- **Tervezett élettartam** – mennyi ideig lesz ez az alkalmazás? Kevesebb mint hat hónap? Több mint két év?

- **Aktuális identitásszolgáltató** – mi az alkalmazás elsődleges identitásszolgáltatója? Vagy a helyi tárolóra támaszkodik?

- **Hitelesítési módszer –** nyílt szabványok használatával hitelesíti az alkalmazást?

- **Függetlenül attól, hogy frissíteni tervezi-e az** alkalmazás kódját – az alkalmazás tervezett vagy aktív fejlesztés alatt áll?

- **Akár a helyszínen** szeretné tartani az alkalmazást, akár hosszú távon is az adatközpontban szeretné tartani?

- **Függ-e az alkalmazás más alkalmazásoktól vagy API-któl** – az alkalmazás jelenleg más alkalmazásokba vagy API-kba hív meg?

- Azt határozza meg, hogy az alkalmazás az **Azure AD-katalógusban** található-e – az alkalmazás jelenleg már integrálva van az [Azure AD-katalógussal?](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps)

A későbbiekben további segítséget, de azonnali migrálási döntést nem kell hoznia, többek között az alábbi adatokat tartalmazza:

- **Alkalmazás URL-címe** – Hol férhetnek hozzá a felhasználók az alkalmazáshoz?

- **Alkalmazás leírása** – mit jelent az alkalmazás rövid leírása?

- **Alkalmazástulajdonos** – a vállalaton belül ki az alkalmazás fő poC-je?

- **Általános megjegyzések vagy megjegyzések** – az alkalmazással vagy az üzlet tulajdonosával kapcsolatos egyéb általános információk

Miután besorolta az alkalmazást, és dokumentálta a részleteket, mindenképpen vásároljon üzleti tulajdonosi hozzáférést a tervezett migrálási stratégiához.

### <a name="plan-a-pilot"></a>Próbaterv

A próbaalkalmazás(nak) a szervezet legfontosabb identitási és biztonsági követelményeit kell képviselnie, és egyértelműen meg kell kapnia az alkalmazástulajdonosoktól a részvételt. A próbakörnyezetek általában külön tesztkörnyezetben futnak. Tekintse [meg a próbatervekkel kapcsolatos ajánlott eljárásokat](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot) az üzembehely helyezési tervek oldalán.

**Ne feledkezzünk meg a külső partnerekről.** Győződjön meg arról, hogy részt vesznek a migrálási ütemezésben és a tesztelésben. Végül ellenőrizze, hogy van-e módjuk hozzáférni az ön súgójához, ha problémákat okozhatnak.

### <a name="plan-for-limitations"></a>Korlátozások megterve

Bár egyes alkalmazásokat könnyű átemelni, más alkalmazások több kiszolgáló vagy példány miatt hosszabb időt is igénybe vehetnek. Az egyéni bejelentkezési oldalak miatt például a SharePoint-migrálás hosszabb időt is igénybe vehet.

Számos SaaS-alkalmazásgyártó díjat számol fel az SSO-kapcsolat módosításáért. Ellenőrizze őket, és tervezze meg ezt.

Az Azure AD [szolgáltatáskorlátokkal és -korlátozásokkal](../enterprise-users/directory-service-limits-restrictions.md) is rendelkezik, amelyekről tudnia kell.

### <a name="app-owner-sign-off"></a>Alkalmazástulajdonosi kijelentkezás

Az üzletileg kritikus és általánosan használt alkalmazásokhoz próbafelhasználók egy csoportjára lehet szükség az alkalmazás próbaszakaszban való teszteléséhez. Miután tesztelt egy alkalmazást az éles üzem előtti vagy a próbakörnyezetben, győződjön meg arról, hogy az alkalmazás üzleti tulajdonosai az alkalmazás migrálását megelőzően a teljesítményt, valamint az összes felhasználót az Azure AD éles környezetben való hitelesítésre való használatára jelentkeztetik.

### <a name="plan-the-security-posture"></a>A biztonsági rendszer megterve

A migrálási folyamat kezdeményezése előtt gondolja át teljes mértékben a vállalati identitásrendszerhez fejleszteni kívánt biztonsági rendszert. Ennek alapja a következő értékes információk összegyűjtése: Az adatokhoz hozzáférő **identitások, eszközök és helyek.**

### <a name="identities-and-data"></a>Identitások és adatok

A legtöbb szervezet olyan, az identitásokkal és adatvédelemmel kapcsolatos speciális követelményekkel rendelkezik, amelyek iparági szegmensenként és a szervezeteken belüli munkakörök szerint változnak. A [javaslatainkért tekintse](/microsoft-365/enterprise/microsoft-365-policies-configurations) meg az identitás- és [](../conditional-access/overview.md) eszköz-hozzáférési konfigurációkat, beleértve a feltételes hozzáférési szabályzatok és a kapcsolódó képességek előírt készletét.

Ezzel az információval megvédheti az Azure AD-val integrált összes szolgáltatáshoz való hozzáférést. Ezek a javaslatok a Microsoft biztonsági pontszámhoz és az [Azure AD-beli identitáspontszámhoz igazodnak.](../fundamentals/identity-secure-score.md) A pontszám az alábbiakban nyújt segítséget:

- Az identitásbiztonsági rendszer objektív felmérése

- Identitásbiztonsági fejlesztések tervezése

- A fejlesztések hatásának felmérése

Ez segít megvalósítani az identitás-infrastruktúra biztonságossá tétele érdekében [szükséges öt lépést is.](../../security/fundamentals/steps-secure-identity.md) Az útmutatást kiindulópontként használhatja a szervezet számára, és a szabályzatokat a szervezet konkrét követelményeinek megfelelően módosíthatja.

### <a name="who-is-accessing-your-data"></a>Ki fér hozzá az adataihoz?

Az Azure AD által támogatott alkalmazások és erőforrások felhasználóinak két fő kategóriája van:

- **Belső:** Az identitásszolgáltatón belül fiókokkal rendelkező alkalmazottak, alvállalkozók és szállítók. Ehhez további részletekre lehet szükség a vezetőkre vagy a vezetőségre és más alkalmazottakra vonatkozó különböző szabályokkal.

- **Külső:** Beszállítók, szállítók, forgalmazók vagy más üzleti partnerek, akik az [Azure AD B2B](../external-identities/what-is-b2b.md) együttműködés rendszeres üzletében lépnek kapcsolatba a szervezettel.

Csoportok definiálhatók ezekhez a felhasználókhoz, és sokféleképpen feltöltheti őket. Dönthet úgy, hogy a rendszergazdának manuálisan kell tagokat hozzáadnia egy csoporthoz, vagy engedélyezheti az önkiszolgáló csoporttagságot. Szabályokat lehet létrehozni, amelyek automatikusan hozzáadják a tagokat a csoportokhoz a megadott feltételek alapján dinamikus [csoportok használatával.](../enterprise-users/groups-dynamic-membership.md)

A külső felhasználók ügyfelekre is hivatkozhatnak. [Azure AD B2C](../../active-directory-b2c/overview.md)termék támogatja az ügyfél-hitelesítést. Ez azonban a jelen dokumentum hatókörét nem terjed ki.

### <a name="devicelocation-used-to-access-data"></a>Az adatok eléréséhez használt eszköz/hely

Az alkalmazás eléréséhez használt eszköz és hely is fontos. A vállalati hálózathoz fizikailag csatlakozó eszközök biztonságosabbak. A hálózaton kívülről, VPN-en keresztüli kapcsolatoknál vizsgálatra lehet szükség.

![Diagram a felhasználói hely és az adatelérés közötti kapcsolatról](media/migrating-application-authentication-to-azure-active-directory-6.jpg)

Az erőforrások, felhasználók és eszközök ezen aspektusait szem előtt tartva dönthet úgy, hogy az [Azure AD](../conditional-access/overview.md) feltételes hozzáférési képességeit használja. A feltételes hozzáférés túlmutat a felhasználói engedélyeken: olyan tényezők kombinációján alapul, mint például egy felhasználó vagy csoport identitása, a hálózat, amelyhez a felhasználó csatlakozik, az eszköz és az alkalmazás, amit használ, valamint az elérni próbált adatok típusán. A felhasználónak biztosított hozzáférés alkalmazkodik ehhez a szélesebb feltételkészlethez.

### <a name="exit-criteria"></a>Kilépési feltételek

Ebben a fázisban a következő sikereket tudja:

- Az alkalmazások használata
  - Teljes körűen dokumentálta az átemelni kívánt alkalmazásokat
  - Az alkalmazások rangsorolása az üzleti kritikusság, a használat mennyisége és az élettartam alapján

- Kiválasztotta azokat az alkalmazásokat, amelyek megfelelnek a próba követelményeinek

- Üzleti tulajdonosi vásárlás a rangsoroláshoz és a stratégiához

- A biztonsági igények és azok megvalósítása

## <a name="phase-3-plan-migration-and-testing"></a>3. fázis: A migrálás és a tesztelés megtervelése

Miután vállalati vásárlásra tett szert, a következő lépés ezeknek az alkalmazásoknak az Azure AD-hitelesítésre való miratingolása.

### <a name="migration-tools-and-guidance"></a>Migrálási eszközök és útmutatók

Az alábbi eszközökkel és útmutatóval pontosan kövesse az alkalmazások Azure AD-be való áttelepítéséhez szükséges lépéseket:

- **Általános migrálási** útmutató – Az [Azure AD-alkalmazások](./migration-resources.md) migrálási eszközkészletében elérhető tanulmány, eszközök, e-mail-sablonok és alkalmazások kérdőívével felfedezheti, besorolhatja és migrálhatja az alkalmazásokat.

- **SaaS-alkalmazások** – Több száz [SaaS-alkalmazás](../saas-apps/tutorial-list.md) oktatóanyagának listáját és a teljes [Azure AD SSO](https://aka.ms/ssodeploymentplan) üzembe helyezési tervet itt láthatja, amely végigveszi a teljes folyamatot.

- **Helyszínen futó** alkalmazások – Megismerheti az [Azure AD-alkalmazásproxy,](./application-proxy.md) és használhatja a teljes [Azure AD alkalmazásproxy üzembe](https://aka.ms/AppProxyDPDownload) helyezési tervet a gyors üzembe helyezéshez.

- **Fejlesztés alatt áll alkalmazások** – Olvassa el [](../develop/quickstart-register-app.md) a részletes integrációs és regisztrációs [útmutatót.](../develop/quickstart-register-app.md)

A migrálás után dönthet úgy, hogy kommunikációt küld a felhasználóknak a sikeres üzembe helyezésről, és emlékezteti őket az esetlegesen szükséges új lépésekre.

### <a name="plan-testing"></a>Tesztelés megtervelése

A migrálás folyamata során előfordulhat, hogy az alkalmazás már rendelkezik tesztkörnyezettel a normál üzembe helyezések során. Ezt a környezetet továbbra is használhatja migrálási teszteléshez. Ha a tesztkörnyezet jelenleg nem érhető el, az alkalmazás architektúrája alapján Azure App Service azure Virtual Machines-t használva állíthat be egyet. Dönthet úgy, hogy külön Teszt Azure AD-bérlőt hoz létre az alkalmazáskonfigurációk fejlesztése során. Ez a bérlő tiszta állapotban indul el, és nem lesz konfigurálva semmilyen rendszerrel való szinkronizálásra.

Az egyes alkalmazásokat tesztelheti úgy, hogy bejelentkezik egy tesztfelhasználóval, és az összes funkció azonos lesz a migrálás előtt. Ha a tesztelés során úgy dönt, hogy a felhasználóknak frissíteniük kell [az MFA-](/azure/active-directory/authentication/howto-mfa-userstates) vagy [SSPR-beállításokat,](../authentication/tutorial-enable-sspr.md)vagy ezt a funkciót az áttelepítés során adja hozzá, akkor ezt mindenképpen adja hozzá a végfelhasználói kommunikációs tervhez. Lásd: [MFA](https://aka.ms/mfatemplates) és [SSPR](https://aka.ms/ssprtemplates) végfelhasználói kommunikációs sablonok.

Miután migrálta az alkalmazásokat, a Azure Portal, [hogy](https://aad.portal.azure.com/) a migrálás sikeres volt-e. Kövesse az alábbi utasításokat:

- Válassza **a Vállalati alkalmazások Minden alkalmazás &gt; lehetőséget,** és keresse meg az alkalmazást a listában.

- Válassza **a Felhasználók és csoportok kezelése &gt; lehetőséget,** ha legalább egy felhasználót vagy csoportot szeretne hozzárendelni az alkalmazáshoz.

- Válassza **a Feltételes hozzáférés kezelése &gt; lehetőséget.** Tekintse át a szabályzatok listáját, és győződjön meg arról, hogy nem blokkolja az alkalmazáshoz való hozzáférést egy feltételes hozzáférési [szabályzat segítségével.](../conditional-access/overview.md)

Az alkalmazás konfigurálásától függően ellenőrizze, hogy az SSO megfelelően működik-e.

| Hitelesítéstípus      | Tesztelés                                             |
| ------------------------ | --------------------------------------------------- |
| **OAuth/ OpenID Connect** | Válassza **a Vállalati alkalmazások Engedélyek &gt; lehetőséget,** és győződjön meg arról, hogy hozzájárult ahhoz, hogy az alkalmazást használni tudja a szervezetben az alkalmazás felhasználói beállításaiban. |
| **SAML-alapú egyszeri bejelentkezés** | Használja [az Egyszeri bejelentkezés](./debug-saml-sso-issues.md) alatt található **SAML-beállítások tesztelése gombot.** |
| **Jelszóalapú SSO** | Töltse le és telepítse a [MyApps biztonságos bejelentkezési bővítményt.](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension) Ez a bővítmény segít elindítani a szervezet bármely olyan felhőalkalmazását, amely egy SSO-folyamat használatát igényli. |

| **[alkalmazásproxy](./application-proxy.md)** | Győződjön meg arról, hogy az összekötő fut, és hozzá van rendelve az alkalmazáshoz. További [segítségért alkalmazásproxy hibaelhárítási útmutatót.](./application-proxy-troubleshoot.md) |

### <a name="troubleshoot"></a>Hibaelhárítás

Ha problémákba merül fel, segítségért tekintse meg az alkalmazások [hibaelhárítási](../app-provisioning/isv-automatic-provisioning-multi-tenant-apps.md) útmutatóját. Tekintse meg a hibaelhárítási cikkeket is: Problémák az SAML-alapú egyszeri bejelentkezéshez konfigurált [alkalmazásokba való bejelentkezéssel.](/troubleshoot/azure/active-directory/troubleshoot-sign-in-saml-based-apps)

### <a name="plan-rollback"></a>Visszaállítás megterve

Ha a migrálás sikertelen, a legjobb stratégia a visszaállítás és a tesztelés. A migrálási problémák elhárításához a következő lépéseket használhatja:

- **Képernyőképek készítése** az alkalmazás meglévő konfigurációról. Ha újra kell konfigurálnia az alkalmazást, vissza is vissza tud nézni.

- Érdemes lehet az örökölt **hitelesítésre** mutató hivatkozásokat is megszabadolni, ha probléma adva a felhőalapú hitelesítéssel.

- A migrálás befejezése előtt ne módosítsa a meglévő **konfigurációt** a korábbi identitásszolgáltatónál.

- Elsőnek a több **ip-t támogató alkalmazások áttelepítését kell elkezdenie.** Ha valami hiba történik, mindig módosíthatja az előnyben részesített internetszolgáltató konfigurációját.

- Győződjön meg arról, hogy az alkalmazás felhasználói élménye rendelkezik **Visszajelzés** gombbal vagy a segítségével kapcsolatos **problémákra mutató mutatókval.**

### <a name="exit-criteria"></a>Kilépési feltételek

Ebben a fázisban az a siker, ha:

- Meghatározta, hogy az egyes alkalmazások migrálva lesznek

- Áttekintve a migrálási eszközöket

- Megtervezte a tesztelést, beleértve a tesztkörnyezeteket és csoportokat

- Tervezett visszaállítás

## <a name="phase-4-plan-management-and-insights"></a>4. fázis: Tervezéskezelés és elemzések

Az alkalmazások áttelepítése után gondoskodnia kell a következőről:

- A felhasználók biztonságosan férhetnek hozzá és kezelhet

- Megfelelő használati és alkalmazás-állapotelemzéseket kaphat

Javasoljuk, hogy a szervezetének megfelelően a következő műveleteket kell követnie.

### <a name="manage-your-users-app-access"></a>A felhasználók alkalmazás-hozzáférésének kezelése

Miután migrálta az alkalmazásokat, számos módon bővítheti a felhasználói élményt

**Az alkalmazások felderíthetővé tehetők**

**Mutasson a felhasználónak** a [MyApps portál](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)felületre. Itt hozzáférhetnek az összes felhőalapú alkalmazáshoz, az Ön által az [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md)használatával elérhetővé tenni, és az alkalmazásproxy alkalmazásokat, feltéve, hogy van engedélyük ezeknek az alkalmazásoknak a eléréséhez. [](./application-proxy.md)


Útmutatást kaphat a felhasználók számára az alkalmazások felfedezéséhez:

- A meglévő [egyszeri bejelentkezés funkcióval](./view-applications-portal.md) bármely **alkalmazáshoz csatolja a felhasználókat**


- Önkiszolgáló [alkalmazás-hozzáférés engedélyezése](./manage-self-service-access.md)egy alkalmazáshoz, és lehetővé teszi a felhasználók számára, hogy **összegyűzt alkalmazásokat adjanak hozzá**

- [Elrejtheti az alkalmazásokat](./hide-application-from-user-portal.md) a végfelhasználók elől (alapértelmezett Microsoft-alkalmazások vagy más alkalmazások), így jobban felderíthetővé tehetik az **alkalmazásokat**

### <a name="make-apps-accessible"></a>Alkalmazások elérhetővé tétele

**Lehetővé teszi, hogy a felhasználók hozzáférjenek az alkalmazásokhoz a mobileszközükről.** A felhasználók az Intune által felügyelt böngészővel férhetnek hozzá a MyApps portálhoz [iOS](./hide-application-from-user-portal.md) 7.0-s vagy újabb vagy [androidos](./hide-application-from-user-portal.md) eszközükön.

A felhasználók letölthetik az **Intune által felügyelt böngészőt:**

- **Android-eszközök esetén a** [Google Play Áruházból](https://play.google.com/store/apps/details?id=com.microsoft.intune)

- **Apple-eszközök esetén az** [Apple App Store,](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) vagy letölthetik Saját alkalmazások iOS-hez elérhető [mobilalkalmazást](https://apps.apple.com/us/app/my-apps-azure-active-directory/id824048653)

**Lehetővé teszi, hogy a felhasználók böngészőbővítményből nyissék meg az alkalmazásokat.**

A felhasználók letölthetik a [MyApps Secure Sign-in Extension](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) bővítményt a [Chrome-ba,](https://chrome.google.com/webstore/detail/my-apps-secure-sign-in-ex/ggjhpefgjjfobnfoldnjipclpcfbgbhl) a [FireFoxba](https://addons.mozilla.org/firefox/addon/access-panel-extension/) vagy a [Microsoft Edge](https://www.microsoft.com/p/my-apps-secure-sign-in-extension/9pc9sckkzk84?rtc=1&activetab=pivot%3Aoverviewtab) és közvetlenül a böngészősávból elindíthat alkalmazásokat a következőre:

- **Keresse meg az alkalmazásokat, és jelenjenek meg a legutóbb használt alkalmazások**

- **Automatikusan konvertálja a** belső URL-címeket, amelyek a alkalmazásproxy a megfelelő külső URL-címekké. [](./application-proxy.md) A felhasználók mostantól dolgozhatnak a már ismert hivatkozásokkal, függetlenül attól, hogy hol vannak.

**Lehetővé teszi, hogy a felhasználók a Office.com.**

A felhasználók a [Office.com](https://www.office.com/) **kereshetik** meg az alkalmazásokat, és a legutóbb használt alkalmazásaik közvetlenül a munkahelyükről jelennek meg hozzájuk.

### <a name="secure-app-access"></a>Biztonságos alkalmazás-hozzáférés

Az Azure AD központosított hozzáférési helyet biztosít a migrált alkalmazások kezeléséhez. A Azure Portal [és](https://portal.azure.com/) engedélyezze a következő képességeket:

- **Biztonságos felhasználói hozzáférés az alkalmazásokhoz.** A [feltételes hozzáférési szabályzatok vagy](../conditional-access/overview.md)az Identity [Protection](../identity-protection/overview-identity-protection.md)engedélyezésével biztosíthatja a felhasználók hozzáférését az alkalmazásokhoz az eszköz állapota, helye stb. alapján.

- **Automatikus kiépítés.** A felhasználók [automatikus építésének beállítása](../app-provisioning/user-provisioning.md) különböző külső SaaS-alkalmazásokkal, amelyekhez a felhasználóknak hozzá kell férni. A felhasználói identitások létrehozása mellett az állapot vagy a szerepkörök változásának megfelelő felhasználói identitások karbantartását és eltávolítását is magában foglalja.

- **Felhasználói hozzáférés-kezelés** **delegálható.** Szükség esetén engedélyezze az önkiszolgáló alkalmazás-hozzáférést az alkalmazásokhoz, és rendeljen hozzá egy üzleti jóváhagyót az alkalmazásokhoz *való hozzáférés jóváhagyásához.* Az [önkiszolgáló csoportkezelést](../enterprise-users/groups-self-service-management.md)az alkalmazásgyűjteményhez rendelt csoportokhoz használhatja.

- **Rendszergazdai hozzáférés delegálva.** rendszergazdai **szerepkör** (például alkalmazás-rendszergazda, felhőalapú alkalmazás-rendszergazda vagy alkalmazásfejlesztő) hozzárendelése a felhasználóhoz a Címtárszerepk szerepkör használatával.

### <a name="audit-and-gain-insights-of-your-apps"></a>Naplózás és elemzések az alkalmazásokról

A központi Azure Portal [az](https://portal.azure.com/) összes alkalmazás naplózását egy központi helyről.

- **Az alkalmazás naplózása** **Vállalati alkalmazások, Naplózás vagy hozzáférés az [Azure AD Reporting API](../reports-monitoring/concept-reporting-api.md) ugyanazon adataihoz a kedvenc eszközeibe való integrálás érdekében" használatával.

- **Megtekintheti egy alkalmazás engedélyeinek megtekintéséhez a** Vállalati **alkalmazások,** az OAuth/OpenID Connect.

- **A vállalati alkalmazások és bejelentkezések** használatával kaphat bejelentkezési **elemzéseket.** Ugyanezeket az adatokat az [Azure AD Reporting API-ból is elérheti.](../reports-monitoring/concept-reporting-api.md)

- **Az alkalmazás használatának vizualizációja** az [Azure AD Power BI tartalomcsomagból](../reports-monitoring/howto-use-azure-monitor-workbooks.md)

### <a name="exit-criteria"></a>Kilépési feltételek

Ebben a fázisban a következő sikeres:

- Biztonságos alkalmazás-hozzáférés biztosítása a felhasználók számára

- Kezelés a migrált alkalmazások naplózása és elemzéséhez

### <a name="do-even-more-with-deployment-plans"></a>Még több alkalmazástelepítési terv

Az üzembehelyezés-tervek az Azure AD-megoldások üzleti értékét, tervezését, implementálási lépéseit és felügyeletét, beleértve az alkalmazás-migrálási forgatókönyveket is. Minden, az üzembe helyezés elkezdéséhez és az Azure AD-képességekből való kihozáshoz szükséges szolgáltatásokat összeküldenek. Az üzembe helyezési útmutatók olyan tartalmakat tartalmaznak, mint például a Microsoft ajánlott eljárásai, a végfelhasználói kommunikáció, a tervezési útmutatók, az implementációs lépések, a tesztelési esetek stb.

Számos [üzembe helyezési](../fundamentals/active-directory-deployment-plans.md) terv áll rendelkezésre az Ön számára, és mi mindig többet készítünk!

### <a name="contact-support"></a>Kapcsolatfelvétel a támogatási szolgáltatással

A támogatási jegy létrehozásához vagy nyomon követéséhez, valamint az állapot figyelése érdekében látogasson el az alábbi támogatási hivatkozásokra.

- **Azure ügyfélszolgálata:** Az [Azure-Microsoft ügyfélszolgálata,](https://azure.microsoft.com/support) és nyisson meg egy jegyet bármely Azure-hoz

Identitás üzembe helyezési problémája attól függően, hogy milyen Nagyvállalati Szerződés Microsofttal.

- **FastTrack:** Ha Nagyvállalati mobilitási és biztonsági (EMS) vagy prémium szintű Azure AD-licencet vásárolt, akkor jogosult a [FastTrack](/enterprise-mobility-security/solutions/enterprise-mobility-fasttrack-program) program telepítési támogatására.

- **Lépjen kapcsolatba a termékmérnöki csapattal:** Ha több millió felhasználóval dolgozik egy nagyobb ügyféltelepítésen, akkor a támogatási csapat vagy a cloud Microsoft-fiók jogosult megoldásarchitektúra-szakértő. A projekt üzembe helyezésének összetettsége alapján közvetlenül az [Azure Identity termékmérnöki csapatával is dolgozhat.](https://aad.portal.azure.com/#blade/Microsoft_Azure_Marketplace/MarketplaceOffersBlade/selectedMenuItemId/solutionProviders)

- **Azure AD Identity blog:** Iratkozzon fel az [Azure AD Identity blogra,](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/bg-p/Identity) és mindig naprakész maradhat a termékekkel kapcsolatos legújabb bejelentésekkel, részletes információkkal és az identitásmérnöki csapat által biztosított ütemterv-információkkal.
