---
title: Négy lépés egy erős identitás-alaprendszer számára – Azure AD
description: Ez a témakör a hibrid identitást használó ügyfelek számára négy lépést ismertet, amelyek egy erős identitás-alaprendszer kiépítését foglalják magukban.
services: active-directory
author: martincoetzer
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2019
ms.subservice: hybrid
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 795f5ede382e561ee810e54e1f8897c5d806e8b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94412374"
---
# <a name="four-steps-to-a-strong-identity-foundation-with-azure-active-directory"></a>Négy lépés egy erős identitás-alaprendszer számára a Azure Active Directory

Az alkalmazásokhoz és adatokhoz való hozzáférés kezelése már nem támaszkodhat a hagyományos hálózati biztonsági határokra vonatkozó stratégiákra, például a peremhálózat-hálózatokra és a tűzfalakra az alkalmazások Felhőbeli gyors mozgatása miatt. A szervezeteknek most meg kell bízniuk a személyazonossági megoldásban, hogy megszabják, ki és mi férhet hozzá a szervezet alkalmazásaihoz és adataihoz. Több szervezet teszi lehetővé az alkalmazottak számára, hogy saját eszközeiket használják a munkahelyen, és az eszközeiket bárhonnan használhassák az internethez való kapcsolódáshoz. Annak biztosítása, hogy az eszközök megfelelőek és biztonságosak legyenek, a szervezet által megvalósított személyazonossági megoldás fontos szempontja. A mai digitális munkahelyen az identitás a felhőbe áthelyezett bármely szervezet [elsődleges vezérlő síkja](https://www.microsoft.com/security/technology/identity-access-management?rtc=1) .

Egy Azure Active Directory (Azure AD) hibrid identitási megoldás bevezetésével a szervezetek olyan prémium szintű funkciókhoz férnek hozzá, amelyek automatizálás, delegálás, önkiszolgáló és egyszeri bejelentkezési képességek révén oldják fel a hatékonyságot. Lehetővé teszi, hogy a dolgozói bárhonnan hozzáférjenek a vállalati erőforrásokhoz, miközben lehetővé teszik az IT-részleg számára a hozzáférés szabályozását azáltal, hogy a megfelelő személyeknek megfelelő hozzáférést biztosítanak a megfelelő erőforrásokhoz a biztonságos hatékonyság érdekében.

A betanulások alapján ez az ajánlott eljárásokról szóló ellenőrzőlista segítséget nyújt a javasolt műveletek gyors üzembe helyezéséhez a szervezeten belüli *erős* identitás-alapok létrehozásához:

* Könnyedén csatlakozhat az alkalmazásokhoz
* Minden felhasználóhoz automatikusan hozzon létre egy identitást
* Biztonságos felhasználók meghatalmazása
* Működővé tenni a bepillantást

## <a name="step-1---connect-to-apps-easily"></a>1. lépés – egyszerűen csatlakozhat az alkalmazásokhoz

Az alkalmazások Azure AD-vel való csatlakoztatásával az egyszeri bejelentkezés (SSO) engedélyezésével és a felhasználók üzembe helyezésével javíthatja a végfelhasználói hatékonyságot és biztonságot. Az alkalmazások egyetlen helyen, az Azure AD-ben történő kezelésével csökkentheti az adminisztratív terhelést, és egyetlen ellenőrzési pontot érhet el a biztonsági és megfelelőségi szabályzatokhoz.

Ez a szakasz az alkalmazásokhoz való felhasználói hozzáférés kezelésének lehetőségeit, a belső alkalmazások biztonságos távoli elérésének engedélyezését, valamint az alkalmazások Azure AD-ba való áttelepítésének előnyeit ismerteti.

### <a name="make-apps-available-to-your-users-seamlessly"></a>Alkalmazások zökkenőmentes elérhetővé tétele a felhasználók számára

Az Azure AD lehetővé teszi, hogy a rendszergazdák [alkalmazásokat vegyenek fel](../manage-apps/add-application-portal.md) a [Azure Portal](https://portal.azure.com/)vállalati alkalmazások gyűjteményébe. Az alkalmazások vállalati alkalmazások katalógusba való felvételével könnyebben konfigurálhatja az alkalmazásokat, hogy az Azure AD-t használják az identitás-szolgáltatóként. Azt is lehetővé teszi, hogy a feltételes hozzáférési szabályzatokkal kezelje az alkalmazáshoz való felhasználói hozzáférést, és konfigurálja az alkalmazások számára az egyszeri bejelentkezést (SSO), így a felhasználóknak nem kell ismételten megadniuk a jelszavukat, és automatikusan bejelentkeznek mind a helyszíni, mind a felhőalapú alkalmazásokba.

Miután hozzáadta az alkalmazásokat az Azure AD-katalógushoz, a felhasználók megtekinthetik a hozzájuk rendelt alkalmazásokat, és igény szerint kereshetnek és igényelhetnek más alkalmazásokat. Az Azure AD [számos módszert](../manage-apps/end-user-experiences.md) biztosít a felhasználók számára az alkalmazások eléréséhez:

* Hozzáférési panel/saját alkalmazások
* Microsoft 365 alkalmazásindító
* Közvetlen bejelentkezés az összevont alkalmazásokba
* Közvetlen bejelentkezési hivatkozások

További információ az alkalmazásokhoz való felhasználói hozzáférésről: **3. lépés – a felhasználók felhatalmazása** ebben a cikkben.

### <a name="migrate-apps-from-active-directory-federation-services-to-azure-ad"></a>Alkalmazások migrálása Active Directory összevonási szolgáltatások (AD FS)ról az Azure AD-be

A Active Directory összevonási szolgáltatások (AD FS) (ADFS) és az Azure AD közötti egyszeri bejelentkezés konfigurációjának áttelepítése további képességeket tesz lehetővé a biztonság, a konzisztens kezelhetőség és az együttműködés terén. Az optimális eredmény érdekében javasoljuk, hogy telepítse át az alkalmazásokat AD FSról az Azure AD-re. Az alkalmazás hitelesítésének és engedélyezésének az Azure AD-be való használata a következő előnyöket biztosítja:

* A Cost kezelése
* Kockázatkezelés
* A termelékenység növelése
* A megfelelőség és a szabályozás kezelése

További információ: [alkalmazások áttelepítése Azure Active Directory](https://aka.ms/migrateapps/whitepaper) tanulmányba.

### <a name="enable-secure-remote-access-to-apps"></a>Biztonságos távoli hozzáférés engedélyezése az alkalmazásokhoz

Az [Azure ad Application proxy](../manage-apps/what-is-application-proxy.md) egy egyszerű megoldást kínál a szervezetek számára a helyszíni alkalmazások felhőbe történő közzétételére olyan távoli felhasználók számára, akiknek biztonságos módon kell elérniük a belső alkalmazásokat. Az Azure AD-ba való egyszeri bejelentkezés után a felhasználók külső URL-címeken vagy egy belső alkalmazás-portálon keresztül is hozzáférhetnek a Felhőbeli és a helyszíni alkalmazásokhoz.

Az Azure AD Application Proxy a következő előnyöket kínálja:

* Az Azure AD kiterjesztése helyszíni erőforrásokra
  * Felhőbeli biztonság és védelem
  * Olyan funkciók, mint a feltételes hozzáférés és a könnyen engedélyezhető Multi-Factor Authentication
* A peremhálózaton nem található összetevők, például a VPN és a hagyományos fordított proxy megoldások
* Nincs szükség bejövő kapcsolatra
* Egyszeri bejelentkezés (SSO) a felhőben és a helyszínen lévő eszközök, erőforrások és alkalmazások között
* Lehetővé teszi a végfelhasználók számára, hogy bárhol és bármikor termelékenyek legyenek

### <a name="discover-shadow-it-with-microsoft-cloud-app-security"></a>Árnyék felderítése Microsoft Cloud App Security

A modern vállalatoknál az IT-részlegek gyakran nem ismerik az összes olyan felhőalapú alkalmazást, amelyet a felhasználók a munkájuk során használnak. Ha a rendszergazdák azt kérik, hogy hány Felhőbeli alkalmazás szerint használják az alkalmazottaikat, átlagosan 30 vagy 40. A valóságban az átlag több mint 1 000 különálló, a szervezet alkalmazottai által használt alkalmazás. az alkalmazottak 80%-a nem engedélyezett alkalmazásokat használ, amelyeket senki sem tekintett át, és nem felel meg a biztonsági és megfelelőségi szabályzatoknak.

[Microsoft Cloud app Security](/cloud-app-security/what-is-cloud-app-security) (MCAS) segítségével azonosíthatja azokat a hasznos alkalmazásokat, amelyek népszerűek a felhasználók számára, és hozzáadhatja őket a vállalati alkalmazások katalógusához, így a felhasználók az egyszeri bejelentkezést és a feltételes hozzáférést is igénybe vehetik.

<em>"**Cloud app Security** segít biztosítani, hogy a felhasználók a felhő-és SaaS-alkalmazásaikat megfelelően használják, olyan módon, amely támogatja a Accenture védelmére szolgáló alapvető biztonsági házirendeket."</em> --- [John Blasi, ügyvezető igazgató, Information Security, Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

Az árnyék észlelése mellett a MCAS is meghatározhatja az alkalmazások kockázati szintjét, megakadályozhatja a vállalati adatokhoz való jogosulatlan hozzáférést, az esetleges adatszivárgást és az alkalmazásokban rejlő egyéb biztonsági kockázatokat.

## <a name="step-2---establish-one-identity-for-every-user-automatically"></a>2. lépés – minden felhasználóhoz automatikusan hozzon létre egy identitást

A helyszíni és a felhőalapú könyvtárak egy Azure AD Hybrid Identity megoldásban való együttes használata lehetővé teszi a meglévő helyszíni Active Directory-befektetések újbóli felhasználását a felhőben meglévő identitások kiépítésével. A megoldás szinkronizálja a helyszíni identitásokat az Azure AD-vel, miközben az identitások elsődleges forrásaként az összes meglévő irányítási megoldással fut a helyszíni Active Directory. A Microsoft Azure AD Hybrid Identity megoldás a helyszíni és a felhőalapú funkciókat is felöleli, és a hitelesítéshez és az összes erőforrás engedélyezéséhez közös felhasználói identitást hoz létre, függetlenül azok helyétől.

A helyszíni címtárak Azure AD-vel való integrálása hatékonyabbá teszi a felhasználókat, és megakadályozza, hogy a felhasználók a Felhőbeli és a helyszíni erőforrások eléréséhez közös identitással használják az alkalmazások és szolgáltatások több fiókját. Több fiók használata a végfelhasználók és a felhasználók számára egyaránt hasznos. A végfelhasználói szemszögből több fiók is azt jelenti, hogy több jelszót kell megjegyeznie. Ennek elkerüléséhez sok felhasználó ugyanazt a jelszót használja minden fiókhoz, ami rossz a biztonsági szempontból. Egy informatikai szempontból az újrafelhasználás gyakran több jelszó-visszaállítási és ügyfélszolgálati költséget eredményez, valamint a végfelhasználói panaszokat.

A Azure AD Connect a helyszíni identitások Azure AD-be való szinkronizálásához használt eszköz, amely a felhőalapú alkalmazások elérésére használható. Ha az identitások az Azure AD-ben vannak, akkor olyan SaaS-alkalmazásokhoz, mint a Salesforce vagy a egyetértenek.

Ebben a szakaszban a magas rendelkezésre állást, a modern hitelesítést a felhő számára, valamint a helyszíni lábnyomok csökkentését biztosító javaslatokat sorolja fel.

> [!NOTE]
> Ha többet szeretne megtudni a Azure AD Connectről, tekintse meg a [Mi az a Azure ad Connect Sync?](./how-to-connect-sync-whatis.md) című témakört.

### <a name="set-up-a-staging-server-for-azure-ad-connect-and-keep-it-up-to-date"></a>Hozzon létre egy átmeneti kiszolgálót a Azure AD Connectéhez, és tartsa naprakészen

Azure AD Connect kulcsfontosságú szerepet játszik a létesítési folyamatban. Ha a szinkronizálási kiszolgáló valamilyen okból offline állapotba kerül, a helyszíni módosítások nem frissülnek a felhőben, és a felhasználók számára hozzáférési problémákhoz vezethetnek. Fontos olyan feladatátvételi stratégiát definiálni, amely lehetővé teszi a rendszergazdák számára a szinkronizálás gyors folytatását, miután a szinkronizálási kiszolgáló offline állapotba kerül.

Ha magas rendelkezésre állást szeretne biztosítani abban az esetben, ha az elsődleges Azure AD Connect-kiszolgáló offline állapotba kerül, ajánlott külön [átmeneti kiszolgálót](./how-to-connect-sync-staging-server.md) telepíteni a Azure ad Connecthoz. A kiszolgálók üzembe helyezése lehetővé teszi, hogy a rendszergazda egy egyszerű konfigurációs kapcsoló használatával "népszerűsítse" az átmeneti kiszolgálót éles környezetben. Az átmeneti üzemmódban konfigurált készenléti kiszolgáló lehetővé teszi az új konfigurációs módosítások tesztelését és telepítését, valamint egy új kiszolgáló bevezetését, ha a régit leszerelik.

> [!TIP]
> Azure AD Connect rendszeresen frissül. Ezért erősen ajánlott megtartani az előkészítési kiszolgálót, hogy kihasználhassa az egyes új verziók teljesítménybeli javításait, hibajavításait és új képességeit.

### <a name="enable-cloud-authentication"></a>Felhőalapú hitelesítés engedélyezése

A helyszíni Active Directoryekkel rendelkező szervezeteknek Azure AD Connect használatával kell kiterjeszteniük a címtárat az Azure AD-be, és be kell állítania a megfelelő hitelesítési módszert. A [megfelelő hitelesítési módszer kiválasztása](./choose-ad-authn.md) a szervezet számára az alkalmazások felhőbe való áthelyezésének első lépése. Ez egy kritikus összetevő, mivel az az összes Felhőbeli adattal és erőforrással való hozzáférést szabályozza.

Az Azure AD-ben a Felhőbeli hitelesítés engedélyezésének legegyszerűbb és ajánlott módja a [jelszó-kivonat szinkronizálásának](./how-to-connect-password-hash-synchronization.md) (PHS) engedélyezése. Más szervezetek is dönthetnek az [átmenő hitelesítés](./how-to-connect-pta-quick-start.md) (PTA ESP) engedélyezésében.

Függetlenül attól, hogy a PHS vagy a PTA elemet választja, ne felejtsen el [zökkenőmentes egyszeri bejelentkezést](./how-to-connect-sso.md) engedélyezni, hogy a felhasználók hozzáférhessenek a felhőalapú alkalmazásokhoz anélkül, hogy a Windows 7 és 8 rendszerű eszközöket használják a vállalati hálózaton. Egyszeri bejelentkezés nélkül a felhasználóknak emlékezniük kell az alkalmazásfüggő jelszavakra, és be kell jelentkezniük az egyes alkalmazásokra. Hasonlóképpen, az informatikai részlegnek is létre kell hoznia és frissítenie kell az egyes alkalmazásokhoz tartozó felhasználói fiókokat, például a Microsoft 365, a Box és a Salesforce. A felhasználóknak emlékezniük kell a jelszavukat, és időt kell fordítaniuk az egyes alkalmazásokra való bejelentkezésre. Egy szabványosított egyszeri bejelentkezési mechanizmus biztosítása a teljes vállalat számára létfontosságú a legjobb felhasználói élmény, a kockázat csökkentése, a jelentés és a szabályozás terén.

A AD FS vagy egy másik helyszíni hitelesítési szolgáltatót használó szervezetek esetében az Azure AD-re való áttéréssel az identitás-szolgáltató csökkentheti a bonyolultságot, és javíthatja a rendelkezésre állást. Ha nem rendelkezik konkrét használati esetekkel az összevonás használatához, javasoljuk, hogy az összevont hitelesítésből telepítse át a PHS és a zökkenőmentes SSO-t, illetve a PTA SSO-t és a zökkenőmentes egyszeri bejelentkezést. További információ: [áttelepítés az összevonás és jelszó kivonatának szinkronizálása a Azure Active Directoryhoz](./plan-migrate-adfs-password-hash-sync.md).

### <a name="enable-automatic-deprovisioning-of-accounts"></a>Fiókok automatikus megszüntetésének engedélyezése

Az automatizált kiépítés és az alkalmazások megszüntetésének engedélyezése a legjobb stratégia az identitások életciklusának szabályozására több rendszeren keresztül. Az Azure AD a különböző népszerű SaaS-alkalmazásokhoz, például a ServiceNow és a Salesforce, valamint a [SCIM 2,0 protokollt](../app-provisioning/use-scim-to-provision-users-and-groups.md)használó számos népszerű SaaS-alkalmazáshoz is támogatja az [automatizált, házirend-alapú](../app-provisioning/configure-automatic-user-provisioning-portal.md) kiépítést és a felhasználói fiókok üzembe helyezését. A hagyományos kiépítési megoldásoktól eltérően, amelyekhez egyéni kód vagy manuális feltöltés szükséges a CSV-fájlokhoz, a kiépítési szolgáltatás a felhőben üzemel, és az előre integrált összekötőket a Azure Portal használatával lehet beállítani és felügyelni. Az automatikus kiépítés egyik legfőbb előnye, hogy a szervezet védelme érdekében azonnal eltávolítja a felhasználók identitását a Key SaaS-alkalmazásokból, amikor elhagyja a vállalatot.

További információ a felhasználói fiókok automatikus kiépítéséről és működéséről: a felhasználók kiépítésének [automatizálása és az SaaS-alkalmazások kiépítése a Azure Active Directory használatával](../app-provisioning/user-provisioning.md).

## <a name="step-3---empower-your-users-securely"></a>3. lépés – a felhasználók biztonságos kibővítése

A mai digitális munkahelyen fontos egyensúlyba venni a biztonságot a hatékonysággal. A végfelhasználók azonban gyakran visszaküldik azokat a biztonsági intézkedéseket, amelyek lelassítják a hatékonyságot, és hozzáférnek a felhőalapú alkalmazásokhoz. Ennek a megoldásnak a megkönnyítése érdekében az Azure AD olyan önkiszolgáló képességeket biztosít, amelyek lehetővé teszik a felhasználók számára a hatékony munkát, miközben minimalizálják az adminisztratív terhelést.

Ez a szakasz azokat a javaslatokat sorolja fel, amelyek a szervezettől érkező súrlódások eltávolítására szolgálnak.

### <a name="enable-self-service-password-reset-for-all-users"></a>Self-Service jelszó-visszaállítás engedélyezése minden felhasználó számára

Az Azure önkiszolgáló [jelszavának alaphelyzetbe állítása](../authentication/tutorial-enable-sspr.md) (SSPR) egyszerű módszert kínál a rendszergazdáknak arra, hogy rendszergazdai beavatkozás nélkül visszaállítsák és feloldják jelszavukat vagy fiókjaikat. A rendszer részletes, követhető jelentést tartalmaz a felhasználók rendszerhozzáféréséről, továbbá értesítőkkel figyelmeztet az illetéktelen használatra vagy visszaélésre.

Alapértelmezés szerint az Azure AD feloldja a fiókokat, amikor a jelszó-visszaállítást végzi. Ha azonban engedélyezi Azure AD Connect [integrációt a helyszínen](../authentication/concept-sspr-howitworks.md#on-premises-integration), lehetősége van a két művelet elkülönítésére is, amelyek lehetővé teszik a felhasználók számára, hogy a jelszó alaphelyzetbe állítása nélkül feloldják a fiókját.

### <a name="ensure-all-users-are-registered-for-mfa-and-sspr"></a>Győződjön meg arról, hogy minden felhasználó regisztrálva van MFA-és SSPR

Az Azure olyan jelentéseket biztosít, amelyeket Ön és a szervezete is használhat, hogy a felhasználók az MFA és a SSPR számára legyenek regisztrálva. Előfordulhat, hogy a regisztráció során még nem regisztrált felhasználókat be kell tanítani a folyamatba.

Az MFA [-bejelentkezésekről szóló jelentés](../authentication/howto-mfa-reporting.md) az MFA-használattal kapcsolatos információkat tartalmaz, és betekintést nyújt arról, hogyan működik az MFA a szervezetben. Az Azure AD-hez való bejelentkezési tevékenységhez (és az auditálásokhoz és a kockázati észlelésekhez) való hozzáférés elengedhetetlen a hibaelhárításhoz, a használati elemzésekhez és a kriminalisztikai vizsgálatokhoz.

Hasonlóképpen, az [önkiszolgáló jelszó-kezelési jelentés](../authentication/howto-sspr-reporting.md) használatával meghatározhatja, hogy ki (vagy nem) regisztrált a SSPR.

### <a name="self-service-app-management"></a>Önkiszolgáló alkalmazások kezelése

Ahhoz, hogy a felhasználók saját maguk is felfedezzék az alkalmazásokat a hozzáférési paneljéről, engedélyeznie kell az [önkiszolgáló alkalmazásokhoz való hozzáférést](../manage-apps/access-panel-manage-self-service-access.md) minden olyan alkalmazáshoz, amelyet engedélyezni kíván a felhasználók számára az önfelderítéshez és a hozzáférés kéréséhez. Az önkiszolgáló alkalmazás-hozzáférés lehetővé teszi a felhasználók számára az alkalmazások önfelderítését, és opcionálisan lehetővé teszi, hogy az üzleti csoport jóváhagyja az alkalmazásokhoz való hozzáférést. Lehetővé teheti az üzleti csoport számára, hogy a hozzáférési panelektől közvetlenül az [alkalmazásokban a jelszó Single-Signhoz](../manage-apps/troubleshoot-password-based-sso.md#automatically-capture-sign-in-fields-for-an-app) rendelt hitelesítő adatokat felügyelje.

### <a name="self-service-group-management"></a>Önkiszolgáló csoportkezelés

A felhasználók az alkalmazásokhoz való hozzárendelése a csoportok használatakor a legmegfelelőbb, mivel nagy rugalmasságot és méretezési képességet tesznek lehetővé:

* Attribútum – a dinamikus csoporttagság alapján
* Delegálás az alkalmazás tulajdonosainak

Az Azure AD lehetővé teszi az erőforrásokhoz való hozzáférés kezelését a biztonsági csoportok és Microsoft 365 csoportok használatával. Ezeket a csoportokat egy csoport tulajdonosa felügyelheti, aki jóváhagyhatja vagy megtagadhatja a tagsági kérelmeket, és delegálhatja a csoporttagság vezérlését. Ez a szolgáltatás önkiszolgáló [csoportként](../enterprise-users/groups-self-service-management.md)ismert, így időt takaríthat meg azzal, hogy lehetővé teszi, hogy a csoport tulajdonosai nem rendelkeznek rendszergazdai szerepkörrel a csoportok létrehozásához és kezeléséhez anélkül, hogy a rendszergazdáknak kellene hagyatkozni a kéréseik kezeléséhez.

## <a name="step-4---operationalize-your-insights"></a>4. lépés – az működővé tenni

A biztonsággal kapcsolatos események és a kapcsolódó riasztások naplózása és naplózása hatékony stratégia alapvető összetevői, amelyekkel biztosítható, hogy a felhasználók továbbra is produktívak maradjanak, és a szervezet biztonságban legyenek. A biztonsági naplók és jelentések segítséget nyújthatnak a kérdés megválaszolásához, például:

* Ön a fizetését használja?
* Van valami gyanús vagy rosszindulatú esemény a bérlőn?
* Ki volt hatással a biztonsági incidensek során?

A biztonsági naplók és jelentések lehetővé teszik a gyanús tevékenységek elektronikus feljegyzését, valamint a hálózat külső behatolását, illetve a belső támadásokat jelezhet minták észlelését. A naplózás használatával figyelheti a felhasználói tevékenységeket, a dokumentumok megfelelőségét, a kriminalisztikai elemzést és egyebeket. A riasztások a biztonsági események értesítéseit biztosítják.

### <a name="assign-least-privileged-admin-roles-for-operations"></a>A minimális jogosultsági szintű rendszergazdai szerepkörök kiosztása a műveletekhez

Ahogy a műveletek megközelítésére gondol, a felügyeletnek néhány szintje van. Az első szint az adminisztrációs terhet helyezi a globális rendszergazda (ok) ra. Mindig a globális rendszergazdai szerepkört használja, a kisebb vállalatok számára is megfelelő lehet. De a nagyobb szervezetek számára, akik az egyes feladatokért felelős ügyfélszolgálati munkatársakkal és rendszergazdákkal foglalkoznak, a globális rendszergazda szerepkör kiosztása biztonsági kockázatot jelenthet, mivel a felhasználók számára lehetővé teszi, hogy a fenti, illetve azon kívüli feladatokat kezeljék, amelyeken felül kell állnia.

Ebben az esetben érdemes megfontolni a felügyelet következő szintjét. Az Azure AD használatával a végfelhasználók "korlátozott rendszergazdaként" jelölhetők ki, akik kezelhetik a kevésbé Kiemelt szerepkörök feladatait. Például hozzárendelhet egy ügyfélszolgálati munkatársat a [biztonsági olvasó](../roles/permissions-reference.md#security-reader) szerepkörhöz, amely lehetővé teszi számukra, hogy a biztonsággal kapcsolatos funkciókat csak olvasási hozzáféréssel lehessen kezelni. Vagy lehet, hogy a [hitelesítés-rendszergazdai](../roles/permissions-reference.md#authentication-administrator) szerepkört az egyéni felhasználókhoz rendeli hozzá, így lehetővé teszi a nem jelszóval rendelkező hitelesítő adatok alaphelyzetbe állítását, illetve a Azure Service Health olvasását és konfigurálását.

További információ: [rendszergazdai szerepkör engedélyei Azure Active Directory](../roles/permissions-reference.md).

### <a name="monitor-hybrid-components-azure-ad-connect-sync-ad-fs-using-azure-ad-connect-health"></a>Hibrid összetevők (Azure AD Connect Sync, AD FS) monitorozása Azure AD Connect Health használatával

A Azure AD Connect és AD FS olyan kritikus fontosságú összetevők, amelyek képesek az életciklus-kezelés és a hitelesítés megszakítására, és végső soron az kimaradások elvégzéséhez vezethetnek. Ezért az összetevők figyeléséhez és jelentéskészítéséhez Azure AD Connect Health kell telepítenie.

További információért olvassa el a [figyelő AD FS Azure ad Connect Health használatával](./how-to-connect-health-adfs.md)című témakört.

### <a name="use-azure-monitor-to-collect-data-logs-for-analytics"></a>Adatnaplók összegyűjtésének Azure Monitor használata az elemzési adatokhoz

A [Azure monitor](../../azure-monitor/overview.md) egy egységes megfigyelési portál az összes Azure ad-naplóhoz, amely részletes elemzéseket, fejlett elemzési és intelligens gépi tanulást biztosít. A Azure Monitor segítségével mérőszámokat és naplókat használhat a portálon belül és API-kon keresztül, így jobban láthatóvá válik az erőforrások állapota és teljesítménye. Lehetővé teszi, hogy a portálon belül egyetlen üvegtábla-felülettel támogassa a termékek integrálásának széles körét API-k és az adatexportálási lehetőségek révén, amelyek támogatják a hagyományos, harmadik féltől származó SIEM rendszereket. A Azure Monitor a riasztási szabályok konfigurálását is lehetővé teszi, hogy értesítést kapjon, vagy automatizált műveleteket készítsen az erőforrásait érintő problémákkal kapcsolatban.

![Azure Monitor](./media/four-steps/image1.png)

### <a name="create-custom-dashboards-for-your-leadership-and-your-day-to-day"></a>Egyéni irányítópultokat hozhat létre a vezetőnek és a napról napra

Azok a szervezetek, amelyek nem rendelkeznek SIEM-megoldással, le tudják tölteni a [Power bi Content Pack](../reports-monitoring/howto-use-azure-monitor-workbooks.md) for Azure ad-t. A Power BI Content Pack olyan előre elkészített jelentéseket tartalmaz, amelyek segítségével megismerheti, hogy a felhasználók hogyan fogadják el és használják az Azure AD szolgáltatásait, ami lehetővé teszi, hogy betekintést nyerjen a címtárban lévő összes tevékenységbe. Létrehozhatja saját [Egyéni irányítópultját](/power-bi/service-dashboards) is, és megoszthatja a vezető csapatával, hogy jelentést készítsen a napi tevékenységekről. Az irányítópultok nagyszerű lehetőséget kínál az üzleti monitorozásra és az összes legfontosabb mérőszám áttekintésére. Az irányítópulton található vizualizációk egy vagy több adatkészletből vagy jelentésből is származhatnak. Az irányítópult a helyi és a felhőben keletkezett adatokat ötvözi, és egyesített nézetet biztosít függetlenül attól, hogy az adatok hol találhatók.

![Egyéni irányítópult Power BI](./media/four-steps/image2.png)

### <a name="understand-your-support-call-drivers"></a>A támogatási hívások illesztőprogramjainak ismertetése

Ha a jelen cikkben ismertetett módon implementál egy hibrid identitási megoldást, végső soron a támogatási hívások csökkenését kell megfigyelni. Az Azure önkiszolgáló jelszavának alaphelyzetbe állításával, például az elfelejtett jelszavakkal és a fiókzárolási szolgáltatással kapcsolatos gyakori problémák enyhítésére a felhasználók önkiszolgáló hozzáférésének engedélyezése lehetővé teszi a felhasználók számára, hogy az IT-részlegre való támaszkodás nélkül is hozzáférjenek az alkalmazásokhoz.

Ha nem veszi figyelembe a támogatási hívások csökkentését, javasoljuk, hogy elemezze a támogatási hívás illesztőprogramjait, hogy megerősítse, hogy a SSPR vagy az önkiszolgáló alkalmazás-hozzáférés megfelelően van-e konfigurálva, illetve van-e olyan új probléma, amely szisztematikusan orvosolható.

*"A digitális átalakítási úton egy megbízható identitás-és hozzáférés-kezelési szolgáltatóra volt szükségünk, amely lehetővé teszi az USA, a partnerek és a felhőalapú szolgáltatók zökkenőmentes, mégis biztonságos integrációját a hatékony ökoszisztéma érdekében; Az Azure AD volt a legjobb megoldás, amely a kockázatok észlelésére és reagálására szolgáló szükséges képességeket és láthatóságot kínálja számunkra. "* --- [Almasri, globális Információbiztonsági igazgató, Aramex](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en)

### <a name="monitor-your-usage-of-apps-to-drive-insights"></a>Az alkalmazások használatának figyelése az adatok átvezetéséhez

Az árnyék felfedése mellett a [Microsoft Cloud app Security](/cloud-app-security/what-is-cloud-app-security) segítségével figyelheti az alkalmazások használatát a szervezeten belül, így a munkahelye a Felhőbeli alkalmazások ígérete teljes mértékben kihasználható. Segít az eszközök irányításában a tevékenységek jobb láthatóságával és a kritikus adatok Felhőbeli alkalmazásokban való védelmének növelésével. A MCAS használatával a szervezeten belüli alkalmazások használatának figyelése a következő kérdések megválaszolásához nyújt segítséget:

* Milyen nem engedélyezett alkalmazásokat használnak az alkalmazottak az adattároláshoz?
* Hol és mikor történik a felhőben tárolt bizalmas adatok tárolása?
* Ki fér hozzá a bizalmas adatokhoz a felhőben?

*"A Cloud App Security gyorsan észlelheti a rendellenességeket, és műveleteket végezhet."* --- [Eric LePenske, vezető menedzser, információbiztonság, Accenture](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security)

## <a name="summary"></a>Összefoglalás

A hibrid identitási megoldások megvalósításának számos aspektusa van, de ez a négy lépésből álló ellenőrzőlista segít egy olyan identitás-infrastruktúra gyors megvalósításában, amely lehetővé teszi a felhasználók számára, hogy hatékonyabbá és biztonságosabbá tegyék a felhasználókat.

* Könnyedén csatlakozhat az alkalmazásokhoz
* Minden felhasználóhoz automatikusan hozzon létre egy identitást
* Biztonságos felhasználók meghatalmazása
* Működővé tenni a bepillantást

Reméljük, hogy ez a dokumentum hasznos ütemtervet kínál a szervezete számára egy erős identitási alap létrehozásához.

## <a name="identity-checklist"></a>Azonosító ellenőrzőlista

Javasoljuk, hogy az alábbi ellenőrzőlistát nyomtassa ki a szervezete egy szilárdabb identitási alapjaira való utazás során.

### <a name="today"></a>Today

|Bejelentkezett?|Elem|
|:-|:-|
||A kísérleti önkiszolgáló jelszó-visszaállítás (SSPR) egy csoport számára|
||Hibrid összetevők monitorozása Azure AD Connect Health használatával|
||A minimális jogosultsági szintű rendszergazdai szerepkörök kiosztása a művelethez|
||Árnyék felderítése Microsoft Cloud App Security|
||Adatnaplók összegyűjtésének Azure Monitor használata az elemzéshez|

### <a name="next-two-weeks"></a>Következő két hét

|Bejelentkezett?|Elem|
|:-|:-|
||Alkalmazás elérhetővé tétele a felhasználók számára|
||Azure AD-kiépítés kipróbálása a választható SaaS-alkalmazásokhoz|
||Átmeneti kiszolgáló beállítása a Azure AD Connecthoz és naprakészen tartása|
||Alkalmazások áttelepítésének megkezdése az ADFS-ből az Azure AD-be|
||Egyéni irányítópultokat hozhat létre a vezetőnek és a napról napra|

### <a name="next-month"></a>Következő hónap

|Bejelentkezett?|Elem|
|:-|:-|
||Az alkalmazások használatának figyelése az adatok átvezetéséhez|
||Biztonságos távoli hozzáférés az alkalmazásokhoz|
||Győződjön meg arról, hogy minden felhasználó regisztrálva van MFA-és SSPR|
||Felhőalapú hitelesítés engedélyezése|

### <a name="next-three-months"></a>Következő három hónap

|Bejelentkezett?|Elem|
|:-|:-|
||Önkiszolgáló alkalmazások felügyeletének engedélyezése|
||Önkiszolgáló csoportok felügyeletének engedélyezése|
||Az alkalmazások használatának figyelése az adatok átvezetéséhez|
||A támogatási hívások illesztőprogramjainak ismertetése|

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan növelheti a biztonságos testhelyzetet a Azure Active Directory képességeivel, és ez az öt lépésből álló ellenőrzőlista – [öt lépés az identitás-infrastruktúra biztonságossá](../../security/fundamentals/steps-secure-identity.md)tételéhez.

Ismerje meg, hogy az Azure AD Identity funkciói hogyan segíthetnek a felhőbe irányított felügyeletre való áttérésben azáltal, hogy olyan megoldásokat és képességeket biztosítanak, amelyek lehetővé teszik, hogy a szervezetek gyorsan fogadjanak el és helyezzen át több személyazonosság-kezelést a hagyományos helyi rendszerekről az Azure AD-be, hogy az [Azure ad hogyan biztosít felhőalapú felügyeletet](./cloud-governed-management-for-on-premises.md)a helyszíni számítási feladatokhoz.