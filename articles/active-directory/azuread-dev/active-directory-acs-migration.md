---
title: Áttelepítés az Azure-Access Control Service | Microsoft Docs
description: Ismerje meg az alkalmazások és szolgáltatások Azure-beli virtuális Access Control Service (ACS) áthelyezésének lehetőségeit.
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
ms.openlocfilehash: bf50db4c463f5161adcc88d69eb2ae8970526103
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515617"
---
# <a name="how-to-migrate-from-the-azure-access-control-service"></a>How to: Migrate from the Azure Access Control Service

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Microsoft Azure Access Control Service (ACS) szolgáltatás, Azure Active Directory (Azure AD) 2018. november 7-én ki leszvezetve. A jelenleg Access Control alkalmazásokat és szolgáltatásokat teljes mértékben át kell migrálni egy másik hitelesítési mechanizmusba. Ez a cikk a jelenlegi ügyfelekre vonatkozó javaslatokat ismerteti, mivel azt tervezi, hogy a meglévő ügyfelek Access Control. Ha jelenleg nem használ Access Control, nincs szükség semmilyen műveletre.

## <a name="overview"></a>Áttekintés

Access Control egy felhőalapú hitelesítési szolgáltatás, amely egyszerű módszert kínál a felhasználók hitelesítésére és hitelesítésére a webalkalmazások és szolgáltatások eléréséhez. Lehetővé teszi a hitelesítés és az engedélyezés számos funkcióját a kódból való kiveszi. Access Control elsősorban a fejlesztők és a Microsoft .NET-ügyfelek, ASP.NET-webalkalmazások és Windows Communication Foundation (WCF) webszolgáltatások használják.

A Access Control három fő kategóriára bonthatók:

- Hitelesítés bizonyos Microsoft-felhőszolgáltatásokban, beleértve a Azure Service Bus a Dynamics CRM-et. Az ügyfélalkalmazások jogkivonatokat szereznek Access Control a szolgáltatásokban való hitelesítéshez a különböző műveletek végrehajtásához.
- Hitelesítés hozzáadása webalkalmazásokhoz egyéni és előrecsomagolt (például SharePoint-) alkalmazásokhoz. A Access Control "passzív" hitelesítés használatával a webalkalmazások támogatják a bejelentkezést egy Microsoft-fiók -val (korábban Live ID), valamint Google-, Facebook-, Yahoo-, Azure AD- és Active Directory összevonási szolgáltatások (AD FS)-fiókokkal (AD FS).
- Egyéni webszolgáltatások biztonságossá tétele a Access Control. Az "aktív" hitelesítéssel a webszolgáltatások biztosítják, hogy csak az olyan ismert ügyfelek számára engedélyezzenek hozzáférést, amelyek hitelesítettek a Access Control.

A következő szakaszok ismertetik ezeket a használati eseteket és a javasolt migrálási stratégiáikat.

> [!WARNING]
> A legtöbb esetben jelentős kódváltozások szükségesek a meglévő alkalmazások és szolgáltatások újabb technológiákba való áttelepítéséhez. Javasoljuk, hogy a lehetséges leállások vagy leállások elkerülése érdekében azonnal kezdje meg a migrálás megtervezését és futtatását.

Access Control a következő összetevőkből áll:

- Egy biztonságos jogkivonat-szolgáltatás (STS), amely fogadja a hitelesítési kéréseket, és biztonsági jogkivonatokat ad ki cserébe.
- A klasszikus Azure portál, ahol létrehozhatja, törölheti, engedélyezheti és letilthatja Access Control névtereket.
- Egy külön Access Control felügyeleti portálon, ahol testreszabhatja és konfigurálhatja Access Control névtereket.
- Felügyeleti szolgáltatás, amellyel automatizálhatja a portálok funkcióit.
- Egy jogkivonat-átalakítási szabálymotor, amellyel összetett logikát definiálhat a problémákat jelző jogkivonatok kimeneti formátumának Access Control manipulálására.

Ezen összetevők csak akkor használhatók, ha létrehoz egy vagy több Access Control névteret. A *névtér* az alkalmazások és szolgáltatások Access Control dedikált példánya. A névtér el van különítve az összes többi Access Control ügyféltől. Más Access Control felhasználók saját névtereket használnak. A névtér Access Control egy dedikált URL-cím, amely a következőre hasonlít:

```HTTP
https://<mynamespace>.accesscontrol.windows.net
```

Az STS-sel és a felügyeleti műveletekkel való kommunikáció ezen az URL-címen történik. Különböző elérési utakat használ különböző célokra. Annak megállapításához, hogy az alkalmazások vagy szolgáltatások Access Control-e, figyelje a &lt; .https:// névtérbe &gt; accesscontrol.windows.net. Az URL-címre való minden forgalmat a Access Control kezel, és meg kell szakítani. 

Ez alól kivételt képeznek az -hoz való minden `https://accounts.accesscontrol.windows.net` forgalom. Az ERRE az URL-címre mutató  forgalmat már egy másik szolgáltatás kezeli, és nincs hatással az Access Control elalasztás. 

További információ a Access Control: [Access Control Service 2.0 (archivált)](/previous-versions/azure/azure-services/hh147631(v=azure.100)).

## <a name="find-out-which-of-your-apps-will-be-impacted"></a>Derítse ki, hogy mely alkalmazások lesznek hatással az érintett alkalmazásokra

Kövesse az ebben a szakaszban található lépéseket, hogy megtudja, mely alkalmazásokat fogja befolyásolni az ACS kieső alkalmazásai.

### <a name="download-and-install-acs-powershell"></a>Az ACS PowerShell letöltése és telepítése

1. Az oldal PowerShell-galéria [Acs.Namespaces letöltése.](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2)
2. Telepítse a modult a következő futtatásával:

    ```powershell
    Install-Module -Name Acs.Namespaces
    ```

3. Az összes lehetséges parancs listájának lekért futtatásával

    ```powershell
    Get-Command -Module Acs.Namespaces
    ```

    Ha segítségre van szüksége egy adott parancshoz, futtassa a következőt:

    ```
     Get-Help [Command-Name] -Full
    ```
    
    ahol `[Command-Name]` a az ACS-parancs neve.

### <a name="list-your-acs-namespaces"></a>Az ACS-névterek felsorolása

1. Csatlakozzon az ACS-hez a **Connect-AcsAccount** parancsmaggal.
  
    Előfordulhat, hogy a parancsok végrehajtásához futtatnia kell a parancsot, és ezeknek az előfizetésnek a rendszergazdája `Set-ExecutionPolicy -ExecutionPolicy Bypass` kell lennie.

2. Listsa ki az elérhető Azure-előfizetéseket a **Get-AcsSubscription** parancsmag használatával.
3. Listsa az ACS-névtereket a **Get-AcsNamespace** parancsmag használatával.

### <a name="check-which-applications-will-be-impacted"></a>Ellenőrizze, hogy mely alkalmazások lesznek hatással az érintett alkalmazásokra

1. Használja az előző lépésben használt névteret, és lépjen a következőre: `https://<namespace>.accesscontrol.windows.net`

    Ha például az egyik névtér contoso-test, akkor a következő címre kell áttérni: `https://contoso-test.accesscontrol.windows.net`

2. A **Megbízhatósági kapcsolatok alatt** válassza a **Függő** fél alkalmazásai lehetőséget az ACS kiesés által érintett alkalmazások listájának megjelenik.
3. Ismételje meg az 1–2. lépést az összes többi ACS-névtér(ek)hez.

## <a name="retirement-schedule"></a>Kieső időpont

2017 novembere óta az összes Access Control teljes mértékben támogatott és működőképes. Az egyetlen korlátozás az, hogy nem hozhat létre új Access Control a [klasszikus Azure portálon](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/)keresztül.

Az összetevők Access Control ütemezése:

- **2017.** november: A klasszikus Azure portál Azure AD-rendszergazdai [felületét kivezették.](https://blogs.technet.microsoft.com/enterprisemobility/2017/09/18/marching-into-the-future-of-the-azure-ad-admin-experience-retiring-the-azure-classic-portal/) Ezen a ponton a névterek Access Control egy új, dedikált URL-címen érhető el: `https://manage.windowsazure.com?restoreClassic=true` . Ezzel az URL-vel megtekintheti a meglévő névtereket, engedélyezheti és letilthatja a névtereket, és szükség esetén törölheti a névtereket.
- **2018. április 2.**: A klasszikus Azure-portál teljesen ki van vezetve, ami azt Access Control, hogy a névtérkezelés már semmilyen URL-címen nem érhető el. Ezen a ponton nem tilthatja le, engedélyezheti, törölheti vagy enumerálhatja a Access Control névtereket. A felügyeleti Access Control azonban teljesen működőképes lesz, és a következő helyen található: `https://<namespace>.accesscontrol.windows.net` . A rendszer többi összetevője Access Control továbbra is a megszokott módon működik.
- **2018.** november 7.: Access Control összes összetevő véglegesen leáll. Ez magában foglalja Access Control felügyeleti portált, a felügyeleti szolgáltatást, az STS-t és a jogkivonat-átalakítási szabálymotort. Ezen a ponton az e-mail-Access Control (a \<namespace\> .accesscontrol.windows.net helyen található) kérések meghiúsulnak. Az összes meglévő alkalmazást és szolgáltatást már korábban más technológiákba kellett migrálni.

> [!NOTE]
> A szabályzatok letiltják az olyan névtereket, amelyek egy ideje nem kértek jogkivonatot. 2018 szeptemberének elejétől ez az időtartam jelenleg 14 nap inaktivitás után következik be, de ez a következő hetekben 7 nap tétlenségre lesz rövidül. Ha jelenleg Access Control letiltott névterekkel, letöltheti és telepítheti az [ACS PowerShellt](#download-and-install-acs-powershell) a névtér(ek) újra engedélyezéséhez.

## <a name="migration-strategies"></a>Migrálási stratégiák

A következő szakaszok magas szintű javaslatokat ismertetnek a microsoftos Access Control microsoftos technológiákra való áttelepítéshez.

### <a name="clients-of-microsoft-cloud-services"></a>Microsoft-felhőszolgáltatások ügyfelei

A Microsoft által kiadott jogkivonatokat elfogadó microsoftos felhőszolgáltatások Access Control legalább egy alternatív hitelesítési formát támogatnak. A megfelelő hitelesítési mechanizmus minden szolgáltatás esetén eltérő. Javasoljuk, hogy a hivatalos útmutatásért tekintse meg az egyes szolgáltatásokhoz szükséges dokumentációt. Az egyszerűség kedvéért az egyes dokumentációkat itt találhatja meg:

| Szolgáltatás | Útmutató |
| ------- | -------- |
| Azure Service Bus | [Mirate to shared access signatures (Mi mirate to shared access signatures)](../../service-bus-messaging/service-bus-migrate-acs-sas.md) |
| Azure Service Bus Relay | [Mirate to shared access signatures (Mi mirate to shared access signatures)](../../azure-relay/relay-migrate-acs-sas.md) |
| Azure Managed Cache | [Migrálás az Azure Cache for Redisbe](../../azure-cache-for-redis/cache-faq.md) |
| Azure DataMarket | [Áttelepítés a Cognitive Services API-k](https://azure.microsoft.com/services/cognitive-services/) |
| BizTalk Services | [Áttelepítés a Logic Apps szolgáltatásának Azure App Service](https://azure.microsoft.com/services/cognitive-services/) |
| Azure Media Services | [Áttelepítés Azure AD-hitelesítésre](https://azure.microsoft.com/blog/azure-media-service-aad-auth-and-acs-deprecation/) |
| Azure Backup | [A Azure Backup frissítése](../../backup/backup-azure-file-folder-backup-faq.yml) |

<!-- Dynamics CRM: Migrate to new SDK, Dynamics team handling privately -->
<!-- Azure RemoteApp deprecated in favor of Citrix: https://www.zdnet.com/article/microsoft-to-drop-azure-remoteapp-in-favor-of-citrix-remoting-technologies/ -->
<!-- Exchange push notifications are moving, customers don't need to move -->
<!-- Retail federation services are moving, customers don't need to move -->
<!-- Azure StorSimple: TODO -->
<!-- Azure SiteRecovery: TODO -->

### <a name="sharepoint-customers"></a>SharePoint-ügyfelek

A SharePoint 2013, 2016 és SharePoint Online ügyfelei régóta használják az ACS-t hitelesítési célokra a felhőben, a helyszínen és hibrid forgatókönyvekben. Egyes SharePoint-funkciókat és -eseteket érint az ACS kieső használata, míg mások nem. Az alábbi táblázat összefoglalja a migrálási útmutatót az ACS-t kihasználó legnépszerűbb SharePoint-szolgáltatások közül:

| Szolgáltatás | Útmutató |
| ------- | -------- |
| Felhasználók hitelesítése az Azure AD-ból | Korábban az Azure AD nem támogatja a SharePoint által a hitelesítéshez szükséges SAML 1.1-jogkivonatokat, és az ACS-t közvetítőként használták, amely kompatibilisvé tette a SharePointot az Azure AD tokenformátumokkal. Mostantól közvetlenül csatlakoztathatja a [SharePointot az Azure AD-hez Azure AD alkalmazás helyszíni SharePoint-alkalmazással.](../saas-apps/sharepoint-on-premises-tutorial.md) |
| [Alkalmazáshitelesítés & a helyszíni SharePointban a kiszolgálók között](/SharePoint/security-for-sharepoint-server/authentication-overview) | Az ACS kieshet; nincs szükség módosításra. | 
| [Alacsony megbízhatósági hitelesítés SharePoint-bővítményekhez (szolgáltató által üzemeltetett és SharePoint által üzemeltetett)](/sharepoint/dev/sp-add-ins/three-authorization-systems-for-sharepoint-add-ins) | Az ACS kieshet; nincs szükség módosításra. |
| [SharePoint felhőbeli hibrid keresés](/archive/blogs/spses/cloud-hybrid-search-service-application) | Az ACS kieshet; nincs szükség módosításra. |

### <a name="web-applications-that-use-passive-authentication"></a>Passzív hitelesítést felhasználó webalkalmazások

A felhasználói hitelesítéshez Access Control webalkalmazások esetében a Access Control a következő szolgáltatásokat és képességeket biztosítja a webalkalmazás-fejlesztők és tervezők számára:

- A Windows Identity Foundation (WIF) mély integrációja.
- Összevonás Google-, Facebook-, Yahoo-, Azure Active Directory- és AD FS- és Microsoft-fiókokkal.
- A következő hitelesítési protokollok támogatása: OAuth 2.0 13-as vázlat, WS-Trust és webszolgáltatások összevonása (WS-Federation).
- A következő jogkivonatformátumok támogatása: JSON-webtoken (JWT), SAML 1.1, SAML 2.0 és Simple Web Token (SWT).
- A WIF-be integrált otthoni felderítési élmény, amely lehetővé teszi a felhasználók számára, hogy kiválasztják a bejelentkezéshez használt fiók típusát. Ezt a felhasználói élményt a webalkalmazás üzemelteti, és teljes mértékben testre szabható.
- Jogkivonat-átalakítás, amely lehetővé teszi a webalkalmazás által fogadott jogcímek gazdag testreszabását Access Control többek között:
    - Adja át az identitásszolgáltatóktól származó jogcímeket.
    - További egyéni jogcímek hozzáadása.
    - Egyszerű if-then logika jogcímek kiállításához bizonyos feltételek mellett.

Sajnos nincs olyan szolgáltatás, amely mindegyik egyenértékű képességet kínálta volna. Értékelje ki, hogy a Access Control mely képességeire van szüksége, majd válasszon a [Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/) [,](https://azure.microsoft.com/services/active-directory-b2c/) Azure Active Directory B2C (Azure AD B2C) vagy más felhőalapú hitelesítési szolgáltatás használata között.

#### <a name="migrate-to-azure-active-directory"></a>Áttelepítés Azure Active Directory

A megfontolható út az alkalmazások és szolgáltatások közvetlen integrálása az Azure AD-be. Az Azure AD a microsoftos munkahelyi vagy iskolai fiókok felhőalapú identitásszolgáltatója. Az Azure AD az azure-Microsoft 365, az Azure és még sok más szolgáltatás identitásszolgáltatója. Hasonló összevont hitelesítési képességeket biztosít, mint Access Control, de nem támogatja az összes Access Control funkciót. 

Az elsődleges példa az olyan közösségi identitásszolgáltatókkal való összevonás, mint a Facebook, a Google és a Yahoo. Ha a felhasználók ilyen típusú hitelesítő adatokkal jelentkeznek be, nem az Azure AD a megoldás. 

Az Azure AD nem feltétlenül támogatja pontosan ugyanazt a hitelesítési protokollt, mint Access Control. Annak ellenére például, hogy a Access Control az Azure AD is támogatja az OAuth-hitelesítést, az egyes implementációk között apró különbségek vannak. A különböző implementációkhoz módosítani kell a kódot a migrálás részeként.

Az Azure AD azonban számos lehetséges előnyt kínál az Access Control számára. Natív módon támogatja a felhőben üzemeltetett Munkahelyi vagy iskolai Microsoft-fiókokat, amelyeket gyakran használnak Access Control ügyfelek. 

Az Azure AD-bérlők egy vagy több példánya is össze is helyi Active Directory a AD FS. Így az alkalmazás hitelesítheti a felhőalapú felhasználókat és a helyszínen üzemeltetett felhasználókat. Emellett támogatja a WS-Federation protokollt is, ami viszonylag egyszerűvé teszi a WIF-et használó webalkalmazással való integrációt.

Az alábbi táblázat a webalkalmazások Access Control és az Azure AD-ban elérhető funkciókat hasonlítja össze. 

Magas szinten a Azure Active Directory a legjobb választás a migráláshoz, ha lehetővé teszi, hogy a felhasználók csak munkahelyi vagy iskolai *Microsoft-fiókjukkal jelentkezzenek be.*

| Képesség | Access Control támogatás | Azure AD-támogatás |
| ---------- | ----------- | ---------------- |
| **Fiókok típusai** | | |
| Munkahelyi vagy iskolai Microsoft-fiókok | Támogatott | Támogatott |
| Fiókok Windows Server Active Directory és AD FS |– Azure AD-bérlővel való összevonással támogatott <br />- Közvetlen összevonással támogatott a AD FS | Csak Azure AD-bérlővel való összevonással támogatott | 
| Fiókok más vállalati identitáskezelő rendszerekből |- Azure AD-bérlővel való összevonással lehetséges <br />– Közvetlen összevonással támogatott | Azure AD-bérlővel való összevonással lehetséges |
| Személyes használatra használt Microsoft-fiókok | Támogatott | Az Azure AD 2.0-s verziós OAuth protokollja támogatja, más protokollon keresztül azonban nem. | 
| Facebook-, Google-, Yahoo-fiókok | Támogatott | Nem támogatott |
| **Protokollok és SDK-kompatibilitás** | | |
| WIF | Támogatott | Támogatott, de korlátozott utasítások érhetők el |
| WS-Federation | Támogatott | Támogatott |
| OAuth 2.0 | A 13. piszkozat támogatása | Az RFC 6749, a legújabb specifikáció támogatása |
| WS-Trust | Támogatott | Nem támogatott |
| **Jogkivonat-formátumok** | | |
| JWT | Bétaverzióban támogatott | Támogatott |
| SAML 1.1 | Támogatott | Előnézet |
| SAML 2.0 | Támogatott | Támogatott |
| Swt | Támogatott | Nem támogatott |
| **Testreszabások** | | |
| Testre szabható otthoni felderítés/fiókokat kiválasztó felhasználói felület | Alkalmazásokba beépíthető letölthető kód | Nem támogatott |
| Egyéni jogkivonat-aláíró tanúsítványok feltöltése | Támogatott | Támogatott |
| Jogcímek testreszabása jogkivonatban |– Az identitásszolgáltatóktól származó bemeneti jogcímek haladnak át<br />- Hozzáférési jogkivonat lekért jogcímként az identitásszolgáltatótól<br />– Kimeneti jogcímek kiállítása bemeneti jogcímek értékei alapján<br />– Kimeneti jogcímek kiállítása állandó értékekkel |– Nem lehet összevont identitásszolgáltatóktól származó jogcímeket átadni<br />– Nem lehet lekért hozzáférési jogkivonatot az identitásszolgáltatótól jogcímként<br />– Nem adhat ki kimeneti jogcímeket a bemeneti jogcímek értéke alapján<br />– A kimeneti jogcímeket konstans értékekkel is ki tudja adni<br />– Ki tud adni kimeneti jogcímeket az Azure AD-be szinkronizált felhasználók tulajdonságai alapján |
| **Automatizálás** | | |
| Konfigurációs és felügyeleti feladatok automatizálása | Az Access Control Management Service-en keresztül támogatott | A Microsoft Graph API-val támogatott |

Ha úgy dönt, hogy az Azure AD a legjobb migrálási útvonal az alkalmazások és szolgáltatások számára, akkor tudnia kell, hogy az alkalmazás kétféleképpen integrálható az Azure AD-val.

Ha a WS-Federation WIF-et használni az Azure [AD-vel](../manage-apps/configure-saml-single-sign-on.md)való integrációhoz, javasoljuk, hogy az Összevont egyszeri bejelentkezés konfigurálása katalógusban nem található alkalmazáshoz módszert használja. A cikk az Azure AD SAML-alapú egyszeri bejelentkezéshez való konfigurálását jelenti, de a WS-Federation konfigurálásához is működik. Ennek a megközelítésnek a használatához prémium szintű Azure AD licenc szükséges. Ennek a megközelítésnek két előnye van:

- Az Azure AD-jogkivonatok testreszabása teljes rugalmasságot biztosít. Az Azure AD által kiadott jogcímeket testreszabhatja úgy, hogy azok megegyeznek a Access Control. Ez különösen a felhasználói azonosítóra vagy a névazonosító jogcímre vonatkozik. Annak érdekében, hogy a technológiák módosítása után is egységes felhasználói IDentifiereket kapjon a felhasználók számára, győződjön meg arról, hogy az Azure AD által kiadott felhasználói azonosítók megegyeznek a Access Control.
- Konfigurálhat egy olyan jogkivonat-aláíró tanúsítványt, amely az alkalmazásra jellemző, és egy Ön által vezérelt élettartammal rendelkezik.

> [!NOTE]
> Ehhez a megközelítéshez prémium szintű Azure AD licenc szükséges. Ha Ön egy Access Control ügyfél, és prémium szintű licencre van szüksége az egyszeri bejelentkezés beállításához egy alkalmazáshoz, lépjen kapcsolatba velünk. Szívesen biztosítunk fejlesztői licenceket a használathoz.

Alternatív megoldásként kövesse ezt a [kódmintát,](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation)amely némileg eltérő utasításokat ad a WS-Federation beállításához. Ez a kódminta nem WIF-et használ, hanem a ASP.NET 4.5 OWIN középső szoftverét. Az alkalmazásregisztrációra vonatkozó utasítások azonban érvényesek a WIF-et használó alkalmazásokra, és nem igényelnek prémium szintű Azure AD licencre. 

Ha ezt a módszert választja, meg kell értenie az aláírókulcs-visszaállítást [az Azure AD-ban.](../develop/active-directory-signing-key-rollover.md) Ez a módszer az Azure AD globális aláírókulcsát használja a jogkivonatok kiállításához. Alapértelmezés szerint a WIF nem frissíti automatikusan az aláírókulcsokat. Amikor az Azure AD váltja a globális aláírókulcsokat, a WIF-implementációt fel kell készíteni a módosítások elfogadására. További információ: Fontos információk a kulcsváltásról az [Azure AD-ban.](/previous-versions/azure/dn641920(v=azure.100))

Ha integrálható az Azure AD-val OpenID Connect vagy OAuth protokollal, javasoljuk. Az Azure AD webalkalmazásba való integrálásával kapcsolatos részletes dokumentációt és útmutatást az [Azure AD fejlesztői útmutatójában találhat.](../develop/index.yml)

#### <a name="migrate-to-azure-active-directory-b2c"></a>Áttelepítés Azure Active Directory B2C

A másik megfontolt migrálási útvonal a Azure AD B2C. Azure AD B2C egy felhőalapú hitelesítési szolgáltatás, amely a Access Control-hoz hasonló lehetővé teszi a fejlesztők számára, hogy a hitelesítési és identitáskezelési logikájukat egy felhőszolgáltatáshoz szervezik ki. Ez egy fizetős szolgáltatás (ingyenes és prémium szintekkel), amely felhasználók által használható alkalmazásokhoz készült, és akár több millió aktív felhasználóval is lehet.

A Access Control szolgáltatáshoz Azure AD B2C legvonzóbb funkció, hogy számos különböző fióktípust támogat. A Azure AD B2C a felhasználók bejelentkeztetheti őket a Microsoft-fiók- vagy Facebook-, Google-, LinkedIn-, GitHub- vagy Yahoo-fiókjukkal stb. Azure AD B2C támogatja a "helyi fiókokat", illetve a kifejezetten az alkalmazáshoz létrehozott felhasználóneveket és jelszavakat is. Azure AD B2C gazdag, a bejelentkezési folyamatok testreszabásához használható, részletgazdag hetőséget is biztosít. 

A Azure AD B2C azonban nem támogatja az ügyfelek számára szükséges hitelesítési protokollok és tokenformátumok Access Control használatát. Az identitásszolgáltatótól, a Microsofttól Azure AD B2C lekért jogkivonatok és a felhasználóval kapcsolatos további információk lekérdezése sem használható.

Az alábbi táblázat a webalkalmazások Access Control a webalkalmazások szempontjából releváns funkcióit hasonlítja össze a Azure AD B2C. Magas szinten a Azure AD B2C a megfelelő választás a migráláshoz, ha az alkalmazás fogyasztóval néz szembe, vagy ha számos különböző típusú fiókot *támogat.*

| Képesség | Access Control támogatás | Azure AD B2C támogatás |
| ---------- | ----------- | ---------------- |
| **Fiókok típusai** | | |
| Munkahelyi vagy iskolai Microsoft-fiókok | Támogatott | Egyéni szabályzatokkal támogatott  |
| Fiókok Windows Server Active Directory és AD FS | Közvetlen összevonással támogatott a AD FS | SAML-összevonással támogatott egyéni szabályzatok használatával |
| Fiókok más vállalati identitáskezelő rendszerekből | Közvetlen összevonással támogatott a WS-Federation | SAML-összevonással támogatott egyéni szabályzatok használatával |
| Személyes használatra használt Microsoft-fiókok | Támogatott | Támogatott | 
| Facebook-, Google-, Yahoo-fiókok | Támogatott | A Facebook és a Google natív módon támogatott, a Yahoo OpenID Connect egyéni szabályzatok használatával történő összevonással |
| **Protokollok és SDK-kompatibilitás** | | |
| Windows Identity Foundation (WIF) | Támogatott | Nem támogatott |
| WS-Federation | Támogatott | Nem támogatott |
| OAuth 2.0 | A 13. piszkozat támogatása | Az RFC 6749, a legújabb specifikáció támogatása |
| WS-Trust | Támogatott | Nem támogatott |
| **Jogkivonat-formátumok** | | |
| JWT | Bétaverzióban támogatott | Támogatott |
| SAML 1.1 | Támogatott | Nem támogatott |
| SAML 2.0 | Támogatott | Nem támogatott |
| Swt | Támogatott | Nem támogatott |
| **Testreszabások** | | |
| Testre szabható kezdőlap felderítése/fiókokat kiválasztó felhasználói felület | Letölthető kód, amely beépíthető az alkalmazásokba | Teljes mértékben testre szabható felhasználói felület egyéni CSS használatával |
| Egyéni jogkivonat-aláíró tanúsítványok feltöltése | Támogatott | Egyéni aláírókulcsok, nem tanúsítványok, egyéni házirendek által támogatottak |
| Jogcímek testreszabása jogkivonatban |– Az identitásszolgáltatóktól származó bemeneti jogcímek haladnak át<br />- Hozzáférési jogkivonat lekért jogcímként az identitásszolgáltatótól<br />– Kimeneti jogcímek kiállítása bemeneti jogcímek értékei alapján<br />– Kimeneti jogcímek kiállítása állandó értékekkel |– Átadhat identitásszolgáltatóktól származó jogcímeket; egyes jogcímek számára szükséges egyéni szabályzatok<br />– Nem lehet lekért hozzáférési jogkivonatot az identitásszolgáltatótól jogcímként<br />– Ki tud adni kimeneti jogcímeket a bemeneti jogcímek értékei alapján egyéni szabályzatokkal<br />– Konstans értékű kimeneti jogcímeket is ki tud állítatni egyéni szabályzatokkal |
| **Automatizálás** | | |
| Konfigurációs és felügyeleti feladatok automatizálása | Az Access Control Management Service-en keresztül támogatott |- A Microsoft Graph API használatával engedélyezett felhasználók létrehozása<br />– Nem hozhatók létre B2C-bérlők, -alkalmazások vagy -szabályzatok programozott módon |

Ha úgy dönt, Azure AD B2C a legjobb áttelepítési útvonal az alkalmazások és szolgáltatások számára, kezdje a következő forrásokkal:

- [Azure AD B2C dokumentációja](../../active-directory-b2c/overview.md)
- [Azure AD B2C – egyéni szabályzatok](../../active-directory-b2c/custom-policy-overview.md)
- [Azure AD B2C díjszabása](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Áttelepítés Ping Identityre vagy Auth0-ra

Bizonyos esetekben előfordulhat, hogy az Azure AD és a Azure AD B2C nem elegendő a webalkalmazások Access Control a kód jelentős módosításai nélkül. Néhány gyakori példa:

- A WIF-et vagy WS-Federation közösségi identitásszolgáltatókkal, például Google-vel vagy Facebookkal való bejelentkezéshez használt webalkalmazások.
- Olyan webalkalmazások, amelyek közvetlen összevonást végeznek egy vállalati identitásszolgáltatóval a WS-Federation protokollon keresztül.
- Olyan webalkalmazások, amelyekhez egy közösségi identitásszolgáltató (például a Google vagy a Facebook) által kiadott hozzáférési jogkivonatra van szükség jogcímként a felhasználók által kiadott Access Control.
- Összetett jogkivonat-átalakítási szabályokkal az Azure AD vagy Azure AD B2C nem reprodukálható webalkalmazások.
- Több-bérlős webalkalmazások, amelyek az ACS-t használják a számos különböző identitásszolgáltatóval való összevonás központi kezelésére

Ezekben az esetekben érdemes megfontolni a webalkalmazás másik felhőalapú hitelesítési szolgáltatásba való áttelepítését. Javasoljuk, hogy fedezze fel az alábbi lehetőségeket. Az alábbi lehetőségek közül mindegyik az alábbihoz hasonló képességeket Access Control:

![Ezen a képen az Auth0 embléma látható](./media/active-directory-acs-migration/rsz-auth0.png) 

[Az Auth0](https://auth0.com/acs) egy rugalmas felhőalapú [](https://auth0.com/acs)identitásszolgáltatás, amely magas szintű migrálási útmutatót hozott létre a Access Control ügyfelei számára, és szinte minden funkciót támogat, amit az ACS tesz.

![Ezen a képen a Ping Identity embléma látható](./media/active-directory-acs-migration/rsz-ping.png)

[A Ping Identity](https://www.pingidentity.com) két, az ACS-hez hasonló megoldást kínál. A PingOne egy felhőalapú identitásszolgáltatás, amely számos olyan funkciót támogat, mint az ACS, a PingFederate pedig egy hasonló helyszíni identitástermék, amely nagyobb rugalmasságot biztosít. A termékek használatával kapcsolatos további részletekért tekintse meg a Ping ACS kivezetési útmutatóját.

A pingelési identitással és az Auth0-val való együttműködés célja annak biztosítása, hogy az Access Control-ügyfelek olyan migrálási útvonallal járjon az alkalmazásaikhoz és szolgáltatásaikhoz, amely minimálisra csökkenti a szolgáltatásról való áttéréshez szükséges Access Control.

<!--

## SharePoint 2010, 2013, 2016

TODO: Azure AD only, use Azure AD SAML 1.1 tokens, when we bring it back online.
Other IDPs: use Auth0? https://auth0.com/docs/integrations/sharepoint.

-->

### <a name="web-services-that-use-active-authentication"></a>Aktív hitelesítést felhasználó webszolgáltatások

A Access Control által kiadott jogkivonatokkal Access Control webszolgáltatásokhoz a következő funkciókat és képességeket biztosítjuk:

- Lehetőség van egy vagy több *szolgáltatás-identitás regisztrálására* a Access Control névterében. A szolgáltatás-identitásokkal jogkivonatokat kérhet le.
- Az OAuth WRAP és az OAuth 2.0 Draft 13 protokoll támogatása jogkivonatok kérése esetén a következő hitelesítő adatok használatával:
    - A szolgáltatásidentitáshoz létrehozott egyszerű jelszó
    - Aláírt SWT szimmetrikus kulccsal vagy X509-tanúsítvánnyal
    - Egy megbízható identitásszolgáltató által kiadott SAML-jogkivonat (általában egy AD FS példány)
- A következő jogkivonatformátumok támogatása: JWT, SAML 1.1, SAML 2.0 és SWT.
- Egyszerű jogkivonat-átalakítási szabályok.

A kiszolgálók közötti Access Control általában a kiszolgálók közötti hitelesítés megvalósításához használják. 

#### <a name="migrate-to-azure-active-directory"></a>Áttelepítés Azure Active Directory

Javasoljuk, hogy az ilyen típusú hitelesítési folyamatot a következőre [Azure Active Directory.](https://azure.microsoft.com/develop/identity/signin/) Az Azure AD a microsoftos munkahelyi vagy iskolai fiókok felhőalapú identitásszolgáltatója. Az Azure AD az azure-Microsoft 365, az Azure és még sok más szolgáltatás identitásszolgáltatója. 

Az Azure AD-t kiszolgálók között történő hitelesítéshez is használhatja az OAuth-ügyfél hitelesítő adatainak megadását az Azure AD-implementációval. Az alábbi táblázat a kiszolgálók Access Control és az Azure AD-ban elérhető hitelesítések képességeit hasonlítja össze.

| Képesség | Access Control támogatás | Azure AD-támogatás |
| ---------- | ----------- | ---------------- |
| Webszolgáltatás regisztrálása | Függő fél létrehozása a Access Control felügyeleti portálon | Azure AD-webalkalmazás létrehozása a Azure Portal |
| Ügyfél regisztrálása | Szolgáltatásidentitás létrehozása a Access Control felügyeleti portálon | Hozzon létre egy másik Azure AD-webalkalmazást a Azure Portal |
| Használt protokoll |- OAuth WRAP protokoll<br />- OAuth 2.0 13-as vázlat ügyfél-hitelesítő adatok megadása | OAuth 2.0-ügyfél hitelesítő adatainak megadása |
| Ügyfél-hitelesítési módszerek |- Egyszerű jelszó<br />- Aláírt SWT<br />– SAML-jogkivonat összevont identitásszolgáltatótól |- Egyszerű jelszó<br />- Aláírt JWT |
| Jogkivonat-formátumok |- JWT<br />– SAML 1.1<br />– SAML 2.0<br />- SWT<br /> | Csak JWT |
| Jogkivonat átalakítása |– Egyéni jogcímek hozzáadása<br />- Egyszerű if-then jogcímkiállítási logika | Egyéni jogcímek hozzáadása | 
| Konfigurációs és felügyeleti feladatok automatizálása | Az Access Control Management Service-en keresztül támogatott | A Microsoft Graph API-val támogatott |

A kiszolgálók között forgatókönyvek megvalósításával kapcsolatos útmutatásért tekintse meg a következő forrásforrásokat:

- Az [Azure AD](../develop/index.yml) fejlesztői útmutató szolgáltatások között szakasza
- [Démonkódminta egyszerű jelszó ügyfél-hitelesítő adatok használatával](https://github.com/Azure-Samples/active-directory-dotnet-daemon)
- [Démonkódminta a tanúsítvány ügyfél-hitelesítő adataival](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Áttelepítés Ping Identityre vagy Auth0-ra

Bizonyos esetekben előfordulhat, hogy az Azure AD-ügyfél hitelesítő adatai és az OAuth-engedély implementációja nem elegendő a Access Control-kód jelentős kódváltozások nélküli cseréjéhez. Néhány gyakori példa:

- Kiszolgáló–kiszolgáló hitelesítés a JWT-ktől különböző jogkivonat-formátumokkal.
- Kiszolgálók és kiszolgálók között hitelesítés külső identitásszolgáltató által biztosított bemeneti jogkivonattal.
- Kiszolgálók között, jogkivonat-átalakítási szabályokkal való hitelesítés, amelyek nem reprodukálhatóak az Azure AD-ban.

Ezekben az esetekben érdemes megfontolni a webalkalmazás másik felhőalapú hitelesítési szolgáltatásba való áttelepítését. Javasoljuk, hogy fedezze fel az alábbi lehetőségeket. Az alábbi lehetőségek a következő lehetőségekhez hasonló képességeket Access Control:

![Ezen a képen az Auth0 embléma látható](./media/active-directory-acs-migration/rsz-auth0.png)

[Az Auth0](https://auth0.com/acs) egy rugalmas felhőalapú [](https://auth0.com/acs)identitásszolgáltatás, amely magas szintű migrálási útmutatót hozott létre a Access Control ügyfelei számára, és szinte minden funkciót támogat, amit az ACS tesz.

![Ezen a képen a Ping Identity ](./media/active-directory-acs-migration/rsz-ping.png)
 [embléma látható,](https://www.pingidentity.com) amely az ACS-hez hasonló két megoldást kínál. A PingOne egy felhőalapú identitásszolgáltatás, amely számos olyan funkciót támogat, mint az ACS, a PingFederate pedig egy hasonló helyszíni identitástermék, amely nagyobb rugalmasságot biztosít. A termékek használatával kapcsolatos további részletekért tekintse meg a Ping ACS kivezetési útmutatóját.

A pingelési identitással és az Auth0-val való együttműködés célja annak biztosítása, hogy az Access Control-ügyfelek olyan migrálási útvonallal járjon az alkalmazásaikhoz és szolgáltatásaikhoz, amely minimálisra csökkenti a szolgáltatásról való áttéréshez szükséges Access Control.

#### <a name="passthrough-authentication"></a>Passthrough authentication (Áttért hitelesítés)

A tetszőleges jogkivonat-átalakítással történő áttűnéses hitelesítéshez nincs egyenértékű Microsoft-technológia az ACS-hez. Ha az ügyfeleinek erre van szüksége, az Auth0 az, aki a legközelebbi közelítési megoldást biztosítja.

## <a name="questions-concerns-and-feedback"></a>Kérdések, aggodalmak és visszajelzés

Tisztában vagyunk a Access Control, hogy a cikk elolvasása után számos ügyfél nem talál egyértelmű migrálási útvonalat. Előfordulhat, hogy segítségre vagy útmutatásra van szüksége a megfelelő terv meghatározásához. Ha meg szeretné vitatni a migrálási forgatókönyveket és kérdéseket, írjon megjegyzést ezen az oldalon.