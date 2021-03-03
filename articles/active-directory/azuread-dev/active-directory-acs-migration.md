---
title: Migrálás az Azure Access Control Serviceról | Microsoft Docs
description: Ismerje meg az alkalmazások és szolgáltatások Azure Access Control Service (ACS) szolgáltatásból való áthelyezésének lehetőségeit.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.custom: aaddev
ms.topic: how-to
ms.workload: identity
ms.date: 10/03/2018
ms.author: ryanwi
ms.reviewer: jlu, annaba, hirsin
ROBOTS: NOINDEX
ms.openlocfilehash: d68cfb91445e2055cb3c3feb88bf925987ea9852
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101687396"
---
# <a name="how-to-migrate-from-the-azure-access-control-service"></a>Útmutató: áttelepítés az Azure Access Control Service

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

A Microsoft Azure Access Control Service (ACS), Azure Active Directory (Azure AD) szolgáltatása 2018 november 7-én megszűnik. Az Access Controlt jelenleg használó alkalmazásokat és szolgáltatásokat a rendszernek egy másik hitelesítési mechanizmusba kell áttelepítenie. Ez a cikk az aktuális ügyfelekre vonatkozó javaslatokat ismerteti, ahogy azt tervezi, hogy a Access Control használatát elavultnak tekinti. Ha jelenleg nem használja a Access Controlt, semmilyen műveletet nem kell elvégeznie.

## <a name="overview"></a>Áttekintés

A Access Control egy felhőalapú hitelesítési szolgáltatás, amely egyszerű módszert kínál a felhasználók hitelesítésére és engedélyezésére a webalkalmazásokhoz és szolgáltatásokhoz való hozzáféréshez. Lehetővé teszi a hitelesítés és az engedélyezés számos funkciójának kiszámítását a kód alapján. A Access Control elsődlegesen Microsoft .NET ügyfelek, ASP.NET web Applications és Windows Communication Foundation (WCF) webszolgáltatások fejlesztői és tervezői használják.

Access Control esetén a használati esetek három fő kategóriára bonthatók:

- Hitelesítés bizonyos Microsoft Cloud Services-szolgáltatásokban, többek között a Azure Service Bus és a Dynamics CRM-ben. Az ügyfélalkalmazások a Access Controli jogkivonatok beszerzésével hitelesítik ezeket a szolgáltatásokat a különböző műveletek végrehajtásához.
- Hitelesítés hozzáadása a webalkalmazásokhoz, egyéni és előre csomagolt (például SharePoint) szolgáltatásokhoz. A "passzív" hitelesítés Access Control használatával a webalkalmazások támogatják a bejelentkezést Microsoft-fiók (korábban élő AZONOSÍTÓval), valamint a Google, a Facebook, a Yahoo, az Azure AD és a Active Directory összevonási szolgáltatások (AD FS) (AD FS) fiókokkal.
- Egyéni webszolgáltatások biztonságossá tétele Access Control által kiállított jogkivonatokkal. Az "Active" hitelesítés használatával a webszolgáltatások biztosítják, hogy csak olyan ismert ügyfelek számára engedélyezzenek hozzáférést, amelyek hitelesítése Access Control.

A következő szakaszokban az egyes használati esetek és a hozzájuk javasolt áttelepítési stratégiákat tárgyaljuk.

> [!WARNING]
> A legtöbb esetben jelentős módosításokra van szükség a meglévő alkalmazások és szolgáltatások újabb technológiákba való áttelepíthetővé tételéhez. Javasoljuk, hogy azonnal kezdje meg az áttelepítés megtervezését és végrehajtását, hogy elkerülje az esetleges kimaradásokat vagy állásidőt.

Access Control a következő összetevőkkel rendelkezik:

- Egy biztonságos jogkivonat-szolgáltatás (STS), amely a hitelesítési kéréseket fogadja, és biztonsági jogkivonatokat bocsát ki a cserébe.
- A klasszikus Azure portál, amelyen a Access Control névterek létrehozását, törlését és engedélyezését és letiltását végezheti el.
- Külön Access Control felügyeleti portál, ahol testreszabhatja és konfigurálhatja Access Control névtereket.
- Egy felügyeleti szolgáltatás, amely a portálok funkcióinak automatizálására használható.
- Egy jogkivonat-transzformációs szabály motorja, amellyel összetett logikát határozhat meg a Access Control problémákkal rendelkező tokenek kimeneti formátumának kezeléséhez.

Ezeknek az összetevőknek a használatához létre kell hoznia egy vagy több Access Control névteret. A *névtér* a Access Control dedikált példánya, amelyet az alkalmazásai és szolgáltatásai kommunikálnak. A névtér minden más Access Control ügyféltől el van különítve. Más Access Control ügyfelek saját névtereket használnak. A Access Control egyik névtere egy dedikált URL-címmel rendelkezik, amely a következőképpen néz ki:

```HTTP
https://<mynamespace>.accesscontrol.windows.net
```

Az összes STS-és felügyeleti művelettel folytatott kommunikáció ezen az URL-címen történik. Különböző elérési utakat használ különböző célokra. Annak megállapításához, hogy az alkalmazások vagy szolgáltatások Access Control használnak-e, figyelje a https:// &lt; névtér &gt; . accesscontrol.Windows.net. Az erre az URL-címre irányuló összes forgalmat Access Control kezeli, és a rendszernek megszűnni kell. 

Ez alól kivételt jelent a szolgáltatásra irányuló forgalom `https://accounts.accesscontrol.windows.net` . Az erre az URL-címre irányuló forgalmat már egy másik szolgáltatás kezeli, és **nem** érinti a Access Control elavulttá. 

További információ a Access Controlről: [Access Control Service 2,0 (archivált)](/previous-versions/azure/azure-services/hh147631(v=azure.100)).

## <a name="find-out-which-of-your-apps-will-be-impacted"></a>Megtudhatja, hogy mely alkalmazásokra lesz hatással

Az ebben a szakaszban ismertetett lépéseket követve megállapíthatja, hogy mely alkalmazásokra lesz hatással az ACS-nyugdíjazás.

### <a name="download-and-install-acs-powershell"></a>Az ACS PowerShell letöltése és telepítése

1. Lépjen a PowerShell-galériara, és töltse le az [ACS. Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2)szolgáltatást.
2. A modul telepítése a futtatásával

    ```powershell
    Install-Module -Name Acs.Namespaces
    ```

3. Az összes lehetséges parancs listájának lekérése futtatásával

    ```powershell
    Get-Command -Module Acs.Namespaces
    ```

    Ha segítséget szeretne kérni egy adott parancsról, futtassa a következőt:

    ```
     Get-Help [Command-Name] -Full
    ```
    
    ahol az `[Command-Name]` ACS-parancs neve.

### <a name="list-your-acs-namespaces"></a>Az ACS-névterek listázása

1. Kapcsolódjon az ACS-hez a **Kapcsolódás-AcsAccount** parancsmag használatával.
  
    Előfordulhat, hogy a parancsok végrehajtása előtt futtatnia kell a `Set-ExecutionPolicy -ExecutionPolicy Bypass` parancsokat, és az előfizetések rendszergazdájának kell lennie ahhoz, hogy végre tudja hajtani a parancsokat.

2. Sorolja fel az elérhető Azure-előfizetéseket a **Get-AcsSubscription** parancsmag használatával.
3. Sorolja fel az ACS-névtereket a **Get-AcsNamespace** parancsmag használatával.

### <a name="check-which-applications-will-be-impacted"></a>Annak meghatározása, hogy mely alkalmazásokat érinti a rendszer

1. Használja az előző lépés névterét, és lépjen a következőre: `https://<namespace>.accesscontrol.windows.net`

    Ha például az egyik névtér a contoso-test, ugorjon a következőre: `https://contoso-test.accesscontrol.windows.net`

2. A **megbízhatósági kapcsolatok** területen válassza a **függő entitások alkalmazások** lehetőséget, hogy megtekintse azon alkalmazások LISTÁJÁT, amelyeket az ACS-nyugdíjazás érint.
3. Ismételje meg a 1-2. lépést minden más ACS-névtérnél.

## <a name="retirement-schedule"></a>Nyugdíjazási ütemterv

November 2017-ig az összes Access Control-összetevő teljes mértékben támogatott és működőképes. Az egyetlen korlátozás, hogy [nem hozhat létre új Access Control névtereket a klasszikus Azure portálon keresztül](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/).

Itt látható a Access Control-összetevők elavulása:

- **November 2017**: a klasszikus [Azure portálon az Azure](https://blogs.technet.microsoft.com/enterprisemobility/2017/09/18/marching-into-the-future-of-the-azure-ad-admin-experience-retiring-the-azure-classic-portal/)ad rendszergazdai felülete megszűnik. Ezen a ponton a Access Control névterek kezelése egy új, dedikált URL-címen érhető el: `https://manage.windowsazure.com?restoreClassic=true` . Ezzel az URl-címmel megtekintheti a meglévő névtereket, engedélyezheti és letilthatja a névtereket, és törölheti a névtereket, ha úgy dönt, hogy.
- **2018. április 2**.: a klasszikus Azure portál teljes mértékben megszűnik, ami azt jelenti, hogy a Access Control névterek kezelése már nem érhető el az URL-címeken keresztül. Ezen a ponton nem tilthatja le, nem engedélyezheti, törölheti vagy enumerálhatja Access Control névtereit. A Access Control felügyeleti portál azonban teljesen működőképes lesz, és a következő helyen található: `https://<namespace>.accesscontrol.windows.net` . A Access Control összes többi összetevője továbbra is megfelelően működik.
- **2018. november 7**.: az összes Access Control-összetevő véglegesen le van állítva. Ide tartozik a Access Control felügyeleti portál, a felügyeleti szolgáltatás, az STS és a jogkivonat-átalakítási szabály motorja. Ezen a ponton a Access Controlnek küldött kérések (a következő helyen: \<namespace\> . accesscontrol.Windows.net) meghiúsulnak. A meglévő alkalmazásokat és szolgáltatásokat más technológiákra is át kell telepítenie.

> [!NOTE]
> A szabályzat letiltja azokat a névtereket, amelyek nem igényeltek jogkivonatot egy adott ideig. A 2018. szeptember elejétől kezdve ez az időszak jelenleg 14 napos inaktivitású, de ez az elkövetkező hetekben 7 napig inaktivitást eredményez. Ha olyan Access Control névterek vannak, amelyek jelenleg le vannak tiltva, [letöltheti és telepítheti az ACS PowerShellt](#download-and-install-acs-powershell) a névtér (ok) újbóli engedélyezéséhez.

## <a name="migration-strategies"></a>Migrálási stratégiák

A következő szakaszok ismertetik a Access Controlról más Microsoft-technológiákra történő Migrálás magas szintű javaslatait.

### <a name="clients-of-microsoft-cloud-services"></a>A Microsoft Cloud Services ügyfelei

Minden olyan Microsoft Cloud Service, amely elfogadja a Access Control által kiállított jogkivonatokat, mostantól legalább egy alternatív hitelesítési formát támogat. A megfelelő hitelesítési mechanizmus az egyes szolgáltatásokra változik. Javasoljuk, hogy tekintse meg az egyes szolgáltatások dokumentációját hivatalos útmutatásként. Az egyszerűség kedvéért a dokumentáció minden készletét itt találja:

| Szolgáltatás | Útmutató |
| ------- | -------- |
| Azure Service Bus | [Migrálás megosztott hozzáférési aláírásokra](../../service-bus-messaging/service-bus-migrate-acs-sas.md) |
| Azure Service Bus Relay | [Migrálás megosztott hozzáférési aláírásokra](../../azure-relay/relay-migrate-acs-sas.md) |
| Azure Managed cache | [Migrálás az Azure Cache for Redisbe](../../azure-cache-for-redis/cache-faq.md) |
| Azure-DataMarket | [Migrálás a Cognitive Services API-kba](https://azure.microsoft.com/services/cognitive-services/) |
| BizTalk Services | [Migrálás a Azure App Service Logic Apps szolgáltatására](https://azure.microsoft.com/services/cognitive-services/) |
| Azure Media Services | [Áttelepítés Azure AD-hitelesítésre](https://azure.microsoft.com/blog/azure-media-service-aad-auth-and-acs-deprecation/) |
| Azure Backup | [A Azure Backup-ügynök frissítése](../../backup/backup-azure-file-folder-backup-faq.md) |

<!-- Dynamics CRM: Migrate to new SDK, Dynamics team handling privately -->
<!-- Azure RemoteApp deprecated in favor of Citrix: https://www.zdnet.com/article/microsoft-to-drop-azure-remoteapp-in-favor-of-citrix-remoting-technologies/ -->
<!-- Exchange push notifications are moving, customers don't need to move -->
<!-- Retail federation services are moving, customers don't need to move -->
<!-- Azure StorSimple: TODO -->
<!-- Azure SiteRecovery: TODO -->

### <a name="sharepoint-customers"></a>SharePoint-ügyfelek

A SharePoint 2013, a 2016 és a SharePoint Online ügyfelei hosszú ideig használják az ACS-t a felhőben, a helyszínen és a hibrid forgatókönyvekben használt hitelesítési célokra. A SharePoint egyes funkcióit és használati eseteit az ACS-nyugdíjazás fogja érinteni, míg mások nem. Az alábbi táblázat az ACS-t használó legnépszerűbb SharePoint-funkciók áttelepítési Útmutatóját foglalja össze:

| Szolgáltatás | Útmutató |
| ------- | -------- |
| Felhasználók hitelesítése az Azure AD-ből | Korábban az Azure AD nem támogatta a SharePoint által a hitelesítéshez szükséges SAML 1,1-tokeneket, és az ACS-t olyan közvetítőként használták, amely az Azure AD-tokenek formátumával kompatibilis a SharePoint rendszerrel. Mostantól [közvetlenül kapcsolódhat a sharepointhoz az Azure ad-hez az Azure ad alkalmazás Gallery SharePoint helyszíni alkalmazás használatával](../saas-apps/sharepoint-on-premises-tutorial.md). |
| [Alkalmazás-hitelesítés & kiszolgáló – kiszolgáló hitelesítés a helyszíni SharePointban](/SharePoint/security-for-sharepoint-server/authentication-overview) | Az ACS-nyugdíjazás nem érinti; nincs szükség módosításra. | 
| [Alacsony megbízhatósági hozzáférés engedélyezése a SharePoint-bővítményekhez (az üzemeltetett és a SharePoint által üzemeltetett szolgáltató)](/sharepoint/dev/sp-add-ins/three-authorization-systems-for-sharepoint-add-ins) | Az ACS-nyugdíjazás nem érinti; nincs szükség módosításra. |
| [SharePoint Cloud Hybrid-keresés](/archive/blogs/spses/cloud-hybrid-search-service-application) | Az ACS-nyugdíjazás nem érinti; nincs szükség módosításra. |

### <a name="web-applications-that-use-passive-authentication"></a>Passzív hitelesítést használó webalkalmazások

A felhasználói hitelesítéshez Access Control használó webalkalmazások esetében a Access Control a következő funkciókat és képességeket biztosítja a webalkalmazás-fejlesztők és az építészek számára:

- Mélyreható integráció a Windows Identity Foundation (WIF) szolgáltatással.
- A Google, a Facebook, a Yahoo, a Azure Active Directory és a AD FS fiókok és a Microsoft-fiókok összevonása.
- A következő hitelesítési protokollok támogatása: OAuth 2,0 draft 13, WS-Trust és Web Services Federation (WS-Federation).
- A következő token-formátumok támogatása: JSON Web Token (JWT), SAML 1,1, SAML 2,0 és Simple web token (SWT).
- A WIF szolgáltatásba integrált, a felhasználók számára a bejelentkezéshez használt fiók típusának kiválasztását lehetővé tevő otthoni tartomány-felderítési felület. Ezt a folyamatot a webalkalmazás üzemelteti, és teljes mértékben testreszabható.
- Jogkivonat-átalakítás, amely lehetővé teszi a webalkalmazás által a Access Controltól kapott jogcímek gazdag testreszabását, beleértve a következőket:
    - Az identitás-szolgáltatók jogcímeinek továbbítása.
    - További egyéni jogcímek hozzáadása.
    - Egyszerű, ha-ezt követően a rendszer bizonyos körülmények között kiállítja a jogcímeket.

Sajnos nincs olyan szolgáltatás, amely az összes egyenértékű funkciót felkínálja. Értékelje ki, hogy milyen képességekre van szüksége Access Control, majd válasszon a [Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/), a [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) (Azure ad B2C) vagy egy másik felhőalapú hitelesítési szolgáltatás használata között.

#### <a name="migrate-to-azure-active-directory"></a>Migrálás Azure Active Directoryre

Az alkalmazások és szolgáltatások közvetlenül az Azure AD-vel való integrálásának elérési útja. Az Azure AD a Microsoft munkahelyi vagy iskolai fiókjainak felhőalapú identitás-szolgáltatója. Az Azure AD a Microsoft 365, az Azure és még sok más identitás-szolgáltató. Hasonló összevont hitelesítési képességeket biztosít a Access Controlhoz, de nem támogatja az összes Access Control funkciót. 

Az elsődleges példa a közösségi identitás-szolgáltatók, például a Facebook, a Google és a Yahoo összevonása. Ha a felhasználók ilyen típusú hitelesítő adatokkal jelentkeznek be, az Azure AD nem a megoldás. 

Az Azure AD emellett nem feltétlenül támogatja ugyanazokat a hitelesítési protokollokat, mint a Access Control. Például bár mind a Access Control, mind az Azure AD támogatja a OAuth, az egyes implementációk között finom különbségek vannak. A különböző implementációk esetében az áttelepítés részeként módosítania kell a kódot.

Az Azure AD azonban számos lehetséges előnyt biztosít az ügyfelek Access Control. Natív módon támogatja a felhőben üzemeltetett Microsoft munkahelyi vagy iskolai fiókokat, amelyeket Access Control ügyfelek általában használnak. 

Az Azure AD-bérlők a helyszíni Active Directory egy vagy több példányához is összevonhatók AD FS használatával. Így az alkalmazás képes hitelesíteni a helyszínen üzemeltetett felhőalapú felhasználókat és felhasználókat. Emellett támogatja a WS-Federation protokollt is, ami viszonylag egyszerűvé teszi a webalkalmazások integrálását a WIF használatával.

A következő táblázat összehasonlítja az Azure AD-ban elérhető szolgáltatásokkal kapcsolatos webalkalmazásokhoz kapcsolódó Access Control funkcióit. 

*Ha a felhasználók csak a Microsoft munkahelyi vagy iskolai fiókjaival jelentkeznek be, magas szinten Azure Active Directory valószínűleg a legjobb választás az áttelepítés során*.

| Képesség | Access Control támogatás | Azure AD-támogatás |
| ---------- | ----------- | ---------------- |
| **Fiókok típusai** | | |
| Microsoft munkahelyi vagy iskolai fiókok | Támogatott | Támogatott |
| Fiókok a Windows Server Active Directory és AD FS |-Az Azure AD-Bérlővel való összevonással támogatott <br />– Közvetlen összevonással támogatott AD FS | Csak az Azure AD-Bérlővel való összevonáson keresztül támogatott | 
| Más vállalati Identitáskezelés-felügyeleti rendszerekből származó fiókok |– Az Azure AD-Bérlővel való összevonáson keresztül lehetséges <br />-Közvetlen összevonás esetén támogatott | Az Azure AD-Bérlővel való összevonáson keresztül lehetséges |
| Személyes használatra készült Microsoft-fiókok | Támogatott | Az Azure AD v 2.0 OAuth protokollon keresztül támogatott, de más protokollokon keresztül nem | 
| Facebook, Google, Yahoo-fiókok | Támogatott | Egyáltalán nem támogatott |
| **Protokollok és SDK-kompatibilitás** | | |
| WIF | Támogatott | Támogatott, de korlátozott utasítások érhetők el |
| WS-Federation | Támogatott | Támogatott |
| OAuth 2.0 | A 13. tervezet támogatása | RFC 6749, a legkorszerűbb specifikáció támogatása |
| WS-Trust | Támogatott | Nem támogatott |
| **Jogkivonat-formátumok** | | |
| JWT | Bétaverzióban támogatott | Támogatott |
| SAML 1,1 | Támogatott | Előnézet |
| SAML 2.0 | Támogatott | Támogatott |
| SWT | Támogatott | Nem támogatott |
| **Testreszabások** | | |
| Testre szabható otthoni tartomány felderítése/fiók – kivételezés felhasználói felülete | Letölthető kód, amely az alkalmazásokba is beépíthető | Nem támogatott |
| Egyéni jogkivonat-aláíró tanúsítványok feltöltése | Támogatott | Támogatott |
| Jogcímek testreszabása jogkivonatokban |– Az identitás-szolgáltatók bemeneti jogcímeinek továbbítása<br />– Hozzáférési jogkivonat beszerzése jogcím-szolgáltatótól<br />– Kimeneti jogcímek kibocsátása bemeneti jogcímek értékei alapján<br />– Állandó értékekkel rendelkező kimeneti jogcímek kibocsátása |– Az összevont identitás-szolgáltatóktól származó jogcímek nem adhatók át<br />– Nem kérhető le jogcímként az Identitáskezelő hozzáférési jogkivonata<br />– A bemeneti jogcímek értékei alapján nem lehet kiállítani a kimeneti jogcímeket<br />– Állandó értékekkel rendelkező kimeneti jogcímeket adhat ki<br />– Az Azure AD-vel szinkronizált felhasználók tulajdonságai alapján kiállíthatja a kimeneti jogcímeket |
| **Automatizálás** | | |
| Konfigurációs és felügyeleti feladatok automatizálása | Access Control felügyeleti szolgáltatáson keresztül támogatott | A Microsoft Graph API használatával támogatott |

Ha úgy dönt, hogy az Azure AD az alkalmazások és szolgáltatások legjobb áttelepítési útvonala, akkor az alkalmazás Azure AD-vel való integrálásának két módját kell figyelembe vennie.

Ha WS-Federation vagy WIF szeretne használni az Azure AD-vel való integrációhoz, javasoljuk, hogy kövesse az [összevont egyszeri bejelentkezés konfigurálása nem katalógusbeli alkalmazáshoz](../manage-apps/configure-saml-single-sign-on.md)című témakörben leírt megközelítést. A cikk az Azure AD és az SAML-alapú egyszeri bejelentkezés konfigurálására vonatkozik, de a WS-Federation konfigurálására is használható. Ezt a módszert követve prémium szintű Azure AD licencre van szükség. Ennek a megközelítésnek két előnye van:

- Az Azure AD-jogkivonat testreszabásának teljes rugalmassága. Testreszabhatja az Azure AD által kiállított jogcímeket, hogy azok megfeleljenek az Access Control által kiadott jogcímeknek. Ez különösen magában foglalja a felhasználói azonosító vagy a név-azonosító jogcímet. Ahhoz, hogy a felhasználók a technológiák módosítása után is konzisztens felhasználói azonosítókat kapjanak, győződjön meg arról, hogy az Azure AD által kiállított felhasználói azonosítók megegyeznek a Access Control által kiállított azonosítókkal.
- Az alkalmazásra jellemző jogkivonat-aláíró tanúsítvány, valamint az Ön által vezérelt élettartam is konfigurálható.

> [!NOTE]
> Ehhez a megközelítéshez prémium szintű Azure AD licenc szükséges. Ha Ön Access Control ügyfél, és prémium szintű licencre van szüksége egy alkalmazás egyszeri bejelentkezésének beállításához, lépjen kapcsolatba velünk. Örömmel biztosítjuk a fejlesztői licencek használatát.

Egy másik módszer a [kód](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation)követése, amely némileg eltérő útmutatást ad a WS-Federation beállításához. Ez a mintakód nem használ WIF, hanem a ASP.NET 4,5 OWIN middleware-t. Az alkalmazás regisztrálására vonatkozó utasítások azonban érvényesek a WIF-t használó alkalmazások esetében, és nem igényelnek prémium szintű Azure AD licencet. 

Ha ezt a módszert választja, az Azure AD-ben meg kell ismernie az [aláíró kulcsok átváltását](../develop/active-directory-signing-key-rollover.md). Ez a módszer az Azure AD globális aláíró kulcsát használja a jogkivonatok kibocsátására. Alapértelmezés szerint a WIF nem frissíti automatikusan az aláíró kulcsokat. Ha az Azure AD elforgatja globális aláíró kulcsait, a WIF megvalósítását elő kell készíteni a módosítások elfogadásához. További információ: [fontos információk az Azure ad-ban az aláíró kulcsok átváltásáról](/previous-versions/azure/dn641920(v=azure.100)).

Ha integrálni tudja az Azure AD-t az OpenID Connect vagy a OAuth protokollok használatával, azt javasoljuk. Részletes dokumentációt és útmutatást talál arról, hogyan integrálhatja az Azure AD-t az [Azure ad fejlesztői útmutatójában](../develop/index.yml)elérhető webalkalmazásba.

#### <a name="migrate-to-azure-active-directory-b2c"></a>Migrálás Azure Active Directory B2Cre

A másik áttelepítési útvonal Azure AD B2C. A Azure AD B2C egy felhőalapú hitelesítési szolgáltatás, amely, például a Access Control, lehetővé teszi a fejlesztők számára, hogy a felhőalapú szolgáltatásokra kiterjedően a hitelesítési és identitás-felügyeleti logikát használják. Ez egy fizetős szolgáltatás (ingyenes és prémium szintű csomag), amelyet olyan, a felhasználók felé irányuló alkalmazásokhoz terveztek, amelyek akár több millió aktív felhasználóval is rendelkezhetnek.

A Access Controlhoz hasonlóan a Azure AD B2C egyik legvonzóbb funkciója, hogy számos különböző típusú fiókot támogat. A Azure AD B2C használatával a felhasználókat a saját Microsoft-fiók, a Facebook, a Google, a LinkedIn, a GitHub vagy a Yahoo-fiókok segítségével, illetve másokkal is bejelentkezhet. A Azure AD B2C támogatja a "helyi fiókokat", illetve a felhasználók által az alkalmazás számára létrehozott felhasználóneveket és jelszavakat is. A Azure AD B2C sokoldalú bővíthetőséget is biztosít, amelyekkel testre szabhatja a bejelentkezési folyamatokat. 

A Azure AD B2C azonban nem támogatja az ügyfelek által igényelt Access Control hitelesítési protokollok és token-formátumok szélességét. Nem használhatja a Azure AD B2Ct a jogkivonatok és a lekérdezések lekérésére, ha további információkra van szüksége a felhasználóról az Identity Provider, a Microsoft vagy egyéb rendszerekben.

A következő táblázat összehasonlítja a webalkalmazásokhoz kapcsolódó Access Control funkcióit, amelyek a Azure AD B2Cban elérhetők. Magas szinten *Azure ad B2C valószínűleg a megfelelő választás az áttelepítéshez, ha az alkalmazás a fogyasztó felé irányul, vagy ha számos különböző típusú fiókot támogat.*

| Képesség | Access Control támogatás | Azure AD B2C támogatás |
| ---------- | ----------- | ---------------- |
| **Fiókok típusai** | | |
| Microsoft munkahelyi vagy iskolai fiókok | Támogatott | Egyéni szabályzatok használatával támogatott  |
| Fiókok a Windows Server Active Directory és AD FS | Közvetlen összevonás által támogatott AD FS | SAML-összevonás által támogatott egyéni szabályzatok használatával |
| Más vállalati Identitáskezelés-felügyeleti rendszerekből származó fiókok | Közvetlen összevonás útján támogatott WS-Federation | SAML-összevonás által támogatott egyéni szabályzatok használatával |
| Személyes használatra készült Microsoft-fiókok | Támogatott | Támogatott | 
| Facebook, Google, Yahoo-fiókok | Támogatott | A Facebook és a Google natív módon támogatott, a Yahoo az OpenID Connect-összevonás segítségével egyéni szabályzatok használatával támogatott |
| **Protokollok és SDK-kompatibilitás** | | |
| Windows Identity Foundation (WIF) | Támogatott | Nem támogatott |
| WS-Federation | Támogatott | Nem támogatott |
| OAuth 2.0 | A 13. tervezet támogatása | RFC 6749, a legkorszerűbb specifikáció támogatása |
| WS-Trust | Támogatott | Nem támogatott |
| **Jogkivonat-formátumok** | | |
| JWT | Bétaverzióban támogatott | Támogatott |
| SAML 1,1 | Támogatott | Nem támogatott |
| SAML 2.0 | Támogatott | Nem támogatott |
| SWT | Támogatott | Nem támogatott |
| **Testreszabások** | | |
| Testre szabható otthoni tartomány felderítése/fiók – kivételezés felhasználói felülete | Letölthető kód, amely az alkalmazásokba is beépíthető | Teljes mértékben testreszabható felhasználói felület egyéni CSS használatával |
| Egyéni jogkivonat-aláíró tanúsítványok feltöltése | Támogatott | Egyéni aláírási kulcsok, nem tanúsítványok, egyéni házirendeken keresztül támogatott |
| Jogcímek testreszabása jogkivonatokban |– Az identitás-szolgáltatók bemeneti jogcímeinek továbbítása<br />– Hozzáférési jogkivonat beszerzése jogcím-szolgáltatótól<br />– Kimeneti jogcímek kibocsátása bemeneti jogcímek értékei alapján<br />– Állandó értékekkel rendelkező kimeneti jogcímek kibocsátása |– Továbbíthatja az identitás-szolgáltatók jogcímeit; egyes jogcímekhez szükséges egyéni szabályzatok<br />– Nem kérhető le jogcímként az Identitáskezelő hozzáférési jogkivonata<br />-Kiállíthatja a kimeneti jogcímeket a bemeneti jogcímek értékei alapján az egyéni házirendek használatával<br />-Az állandó értékekkel rendelkező kimeneti jogcímeket egyéni házirendeken keresztül is kiállíthatja |
| **Automatizálás** | | |
| Konfigurációs és felügyeleti feladatok automatizálása | Access Control felügyeleti szolgáltatáson keresztül támogatott |– A Microsoft Graph API használatával engedélyezett felhasználók létrehozása<br />– A B2C-bérlők, alkalmazások vagy házirendek programozott módon nem hozhatók létre |

Ha úgy dönt, hogy Azure AD B2C az alkalmazások és szolgáltatások legjobb áttelepítési útvonala, kezdje a következő erőforrásokkal:

- [Azure AD B2C dokumentáció](../../active-directory-b2c/overview.md)
- [Azure AD B2C – egyéni szabályzatok](../../active-directory-b2c/custom-policy-overview.md)
- [Díjszabás Azure AD B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Áttelepítés a ping Identity vagy Auth0

Bizonyos esetekben előfordulhat, hogy az Azure AD és a Azure AD B2C nem elegendő ahhoz, hogy a webalkalmazásokban a nagy programkód módosítása nélkül cserélje le a Access Control. Néhány gyakori példa a következőkre utalhat:

- A WIF-t vagy WS-Federationt használó webalkalmazások a közösségi identitás-szolgáltatók, például a Google vagy a Facebook szolgáltatásban való bejelentkezéshez.
- Webalkalmazások, amelyek közvetlen összevonást végeznek a vállalati identitás-szolgáltatóhoz a WS-Federation protokollon keresztül.
- Olyan webalkalmazások, amelyek megkövetelik a közösségi identitás-szolgáltató (például a Google vagy a Facebook) által kiadott hozzáférési jogkivonatot a Access Control által kiállított jogkivonatok jogcímeként.
- Az Azure AD vagy Azure AD B2C által nem reprodukálható összetett jogkivonat-átalakítási szabályokkal rendelkező webalkalmazások.
- Az ACS-t használó több-bérlős webalkalmazások, amelyek számos különböző Identitáskezelő számára központilag kezelik az összevonási szolgáltatásokat

Ezekben az esetekben érdemes lehet áttelepíteni a webalkalmazást egy másik felhőalapú hitelesítési szolgáltatásba. Javasoljuk, hogy vizsgálja meg a következő beállításokat. A következő lehetőségek mindegyike a Access Controlhoz hasonló képességeket kínál:

![Ez a képen a Auth0 embléma látható](./media/active-directory-acs-migration/rsz-auth0.png) 

A [Auth0](https://auth0.com/acs) egy rugalmas felhőalapú Identitáskezelő szolgáltatás, amely [magas szintű áttelepítési útmutatót hozott létre a Access Control ügyfelei számára](https://auth0.com/acs), és szinte minden olyan funkciót támogat, amelyet az ACS tesz.

![Ez a rendszerkép a ping Identity emblémát jeleníti meg](./media/active-directory-acs-migration/rsz-ping.png)

A [ping Identity](https://www.pingidentity.com) két, az ACS-hez hasonló megoldást kínál. A PingOne egy felhőalapú identitás-szolgáltatás, amely az ACS számos szolgáltatását támogatja, és a PingFederate egy hasonló a helyszíni Identity termékhez, amely nagyobb rugalmasságot biztosít. A termékek használatával kapcsolatos további információkért tekintse meg a ping ACS-kivonulási útmutatóját.

Célunk, hogy a ping Identity és Auth0 használatával biztosítható legyen, hogy minden Access Control ügyfél áttelepítési útvonala legyen az alkalmazásokhoz és szolgáltatásokhoz, amelyek minimálisra csökkentsék a Access Controlból való áttéréshez szükséges munkát.

<!--

## SharePoint 2010, 2013, 2016

TODO: Azure AD only, use Azure AD SAML 1.1 tokens, when we bring it back online.
Other IDPs: use Auth0? https://auth0.com/docs/integrations/sharepoint.

-->

### <a name="web-services-that-use-active-authentication"></a>Aktív hitelesítést használó webszolgáltatások

A Access Control által kiállított jogkivonatokkal védett webszolgáltatások esetében a Access Control a következő funkciókat és képességeket kínálja:

- Egy vagy több *szolgáltatás-identitás* regisztrálásának lehetősége a Access Control névtérben. A szolgáltatásbeli identitások a jogkivonatok igénylésére használhatók.
- A OAuth WRAP és a OAuth 2,0-as számú protokollok támogatása a jogkivonatok igényléséhez a következő típusú hitelesítő adatok használatával:
    - A szolgáltatás identitásához létrehozott egyszerű jelszó
    - Aláírt SWT szimmetrikus kulcs vagy X509-tanúsítvány használatával
    - Egy megbízható identitás-szolgáltató által kiadott SAML-jogkivonat (általában egy AD FS-példány)
- A következő token-formátumok támogatása: JWT, SAML 1,1, SAML 2,0 és SWT.
- Egyszerű jogkivonat-átalakítási szabályok.

A Access Control szolgáltatásbeli identitásokat jellemzően a kiszolgálók közötti hitelesítés megvalósítására használják. 

#### <a name="migrate-to-azure-active-directory"></a>Migrálás Azure Active Directoryre

Erre a hitelesítési folyamatra vonatkozó Javaslatunk a [Azure Active Directoryba](https://azure.microsoft.com/develop/identity/signin/)való Migrálás. Az Azure AD a Microsoft munkahelyi vagy iskolai fiókjainak felhőalapú identitás-szolgáltatója. Az Azure AD a Microsoft 365, az Azure és még sok más identitás-szolgáltató. 

Az Azure AD-t a kiszolgálók közötti hitelesítéshez is használhatja az OAuth-ügyfél hitelesítő adatainak megadása Azure AD-implementáció használatával. A következő táblázat összehasonlítja a kiszolgáló és a kiszolgáló közötti hitelesítés Access Control képességeit, amelyek az Azure AD-ben elérhetők.

| Képesség | Access Control támogatás | Azure AD-támogatás |
| ---------- | ----------- | ---------------- |
| Webszolgáltatás regisztrálása | Függő entitás létrehozása a Access Control felügyeleti portálon | Azure AD-Webalkalmazás létrehozása a Azure Portalban |
| Ügyfél regisztrálása | Szolgáltatás identitásának létrehozása a Access Control felügyeleti portálon | Hozzon létre egy másik Azure AD-webalkalmazást a Azure Portal |
| Használt protokoll |-OAuth WRAP protokoll<br />-OAuth 2,0-draft 13 ügyfél-hitelesítő adatok megadása | OAuth 2.0-ügyfél hitelesítő adatainak megadása |
| Ügyfél-hitelesítési módszerek |– Egyszerű jelszó<br />-Aláírt SWT<br />-SAML-jogkivonat összevont identitás-szolgáltatótól |– Egyszerű jelszó<br />-Aláírt JWT |
| Jogkivonat-formátumok |– JWT<br />– SAML 1,1<br />– SAML 2,0<br />– SWT<br /> | Csak JWT |
| Jogkivonat-átalakítás |– Egyéni jogcímek hozzáadása<br />– Egyszerű, ha-akkor jogcím kiállítási logikája | Egyéni jogcímek hozzáadása | 
| Konfigurációs és felügyeleti feladatok automatizálása | Access Control felügyeleti szolgáltatáson keresztül támogatott | A Microsoft Graph API használatával támogatott |

A kiszolgálók közötti forgatókönyvek megvalósításával kapcsolatos útmutatásért tekintse meg a következő forrásokat:

- Az [Azure ad fejlesztői útmutató](../develop/index.yml) szolgáltatások és szolgáltatások szakasza
- [Daemon-kód minta egyszerű jelszó-ügyfél hitelesítő adatok használatával](https://github.com/Azure-Samples/active-directory-dotnet-daemon)
- [Daemon-kód minta tanúsítvány-ügyfél hitelesítő adatainak használatával](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Áttelepítés a ping Identity vagy Auth0

Bizonyos esetekben előfordulhat, hogy az Azure AD-ügyfél hitelesítő adatai és a OAuth-engedélyezési implementációja nem elegendő ahhoz, hogy a jelentős programkód módosítása nélkül cserélje le a Access Control az architektúrában. Néhány gyakori példa a következőkre utalhat:

- Kiszolgálók közötti hitelesítés a JWTs-től eltérő jogkivonat-formátumokkal.
- Kiszolgálók közötti hitelesítés külső identitás szolgáltatója által biztosított bemeneti jogkivonat használatával.
- Kiszolgálók közötti hitelesítés olyan jogkivonat-átalakítási szabályokkal, amelyeket az Azure AD nem tud reprodukálni.

Ezekben az esetekben érdemes lehet áttelepíteni a webalkalmazást egy másik felhőalapú hitelesítési szolgáltatásba. Javasoljuk, hogy vizsgálja meg a következő beállításokat. A következő lehetőségek mindegyike a Access Controlhoz hasonló képességeket kínál:

![Ez a képen a Auth0 embléma látható](./media/active-directory-acs-migration/rsz-auth0.png)

A [Auth0](https://auth0.com/acs) egy rugalmas felhőalapú Identitáskezelő szolgáltatás, amely [magas szintű áttelepítési útmutatót hozott létre a Access Control ügyfelei számára](https://auth0.com/acs), és szinte minden olyan funkciót támogat, amelyet az ACS tesz.

![Ez a képen látható, hogy a ping Identity embléma ](./media/active-directory-acs-migration/rsz-ping.png)
 [ping Identity](https://www.pingidentity.com) két, az ACS-hez hasonló megoldást kínál. A PingOne egy felhőalapú identitás-szolgáltatás, amely az ACS számos szolgáltatását támogatja, és a PingFederate egy hasonló a helyszíni Identity termékhez, amely nagyobb rugalmasságot biztosít. A termékek használatával kapcsolatos további információkért tekintse meg a ping ACS-kivonulási útmutatóját.

Célunk, hogy a ping Identity és Auth0 használatával biztosítható legyen, hogy minden Access Control ügyfél áttelepítési útvonala legyen az alkalmazásokhoz és szolgáltatásokhoz, amelyek minimálisra csökkentsék a Access Controlból való áttéréshez szükséges munkát.

#### <a name="passthrough-authentication"></a>Továbbító hitelesítés

A tetszőleges jogkivonat-transzformációval rendelkező áteresztő hitelesítéshez nincs megfelelő Microsoft-technológia az ACS-hez. Ha az ügyfélnek szüksége van rájuk, Auth0 lehet az a legközelebb közelítő megoldást biztosító megoldás.

## <a name="questions-concerns-and-feedback"></a>Kérdések, megfontolás és visszajelzés

Tisztában vagyunk azzal, hogy számos Access Control ügyfél nem talál egyértelmű áttelepítési útvonalat a cikk elolvasása után. Előfordulhat, hogy a megfelelő terv meghatározásához segítségre vagy útmutatásra van szüksége. Ha meg szeretné vitatni az áttelepítési forgatókönyveket és a kérdéseket, küldjön egy megjegyzést ezen az oldalon.