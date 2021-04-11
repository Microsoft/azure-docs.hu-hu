---
title: SaaS-ajánlat tervezése a Microsoft kereskedelmi piactéren
description: A Microsoft partner Center kereskedelmi piactér programjának használatával megtervezheti, hogyan lehet új (SaaS) szolgáltatást használni az Microsoft AppSource, az Azure Marketplace-en vagy a Cloud Solution Provider (CSP) programon keresztül.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: 6f08fa0b2126112fa17fd61be6f44bb5cc6d5396
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552155"
---
# <a name="how-to-plan-a-saas-offer-for-the-commercial-marketplace"></a>SaaS-ajánlat tervezése a kereskedelmi piactéren

Ez a cikk ismerteti a Microsoft kereskedelmi piactéren szolgáltatott szoftverként (SaaS) való közzététel különböző lehetőségeit és követelményeit. Az SaaS lehetővé teszi, hogy az online előfizetéseken keresztül szoftveres megoldásokat nyújtson az ügyfeleknek. SaaS-közzétevőként Ön kezeli és fizeti az Ön ajánlata ügyfelei általi használatát támogató infrastruktúrát. Ez a cikk segít felkészülni az ajánlat közzétételére a kereskedelmi piactéren a partner centerrel.

## <a name="listing-options"></a>Terméklistázási lehetőségek

Az új SaaS-ajánlat közzétételének előkészítése során el kell döntenie, hogy melyik _listaelem_ közül választhat. A listában kiválasztott listaelem határozza meg, hogy milyen további információkat kell megadnia az ajánlat a partner Centerben való létrehozásakor. Az  **ajánlat beállítása** lapon a " [SaaS-ajánlat létrehozása a kereskedelmi piactéren](create-new-saas-offer.md)" című cikkben leírtak alapján határozhatja meg a listázási lehetőséget.

A következő táblázat az SaaS-ajánlatok kereskedelmi piactéren való listázási lehetőségeit mutatja be.

| Listaelem-beállítás | Tranzakciós folyamat |
| ------------ | ------------- |
| Kapcsolatfelvétel | Az ügyfél közvetlenül kapcsolatba lép az adatokkal az Ön listájában.``*`` |
| Ingyenes próbaidőszak | Az ügyfél Azure Active Directory (Azure AD) segítségével átirányítja a célként megadott URL-címre.``*`` |
| Letöltés (ingyenes) | Az ügyfél átirányítja a célként megadott URL-címre az Azure AD-n keresztül.``*`` |
| Értékesítés a Microsofton keresztül  | A Microsoft által _eladott ajánlatokat felhívható_ ajánlatoknak nevezzük. Egy olyan ajánlat, amely a Microsoft számára lehetővé teszi, hogy egy szoftverlicenc-licencet nyújtson a közzétevő nevében. Az Ön által választott díjszabási modell alapján az SaaS-ajánlatokat használjuk, és az Ön nevében kezelheti az ügyfelek tranzakcióit. Az Azure-infrastruktúra használati díjait Önnek, a partnernek közvetlenül kell fizetnie. A díjszabási modellben az infrastruktúra költségeit kell figyelembe veszi. Ezt részletesebben is ismertetjük az [SaaS-számlázás](#saas-billing) alatt.  |
|||

``*`` A kiadók felelősek a szoftverlicenc-tranzakció valamennyi aspektusának támogatásához, beleértve a sorrendet, a teljesítést, a mérést, a számlázást, a számlázást, a fizetést és a gyűjtést.

További információ ezekről a listaelem-lehetőségekről: [kereskedelmi Piactéri Transact-képességek](marketplace-commercial-transaction-capabilities-and-considerations.md).

Az ajánlat közzététele után az ajánlathoz választott listaelem az ajánlat listázási oldalának bal felső sarkában jelenik meg. Az alábbi képernyőfelvételen az Azure piactéren elérhető ajánlatok listája látható az azonnali **Letöltés** gombbal.

![Az online áruházban található ajánlati listát ábrázolja.](./media/saas/listing-options-saas.png)

## <a name="technical-requirements"></a>Technikai követelmények

A technikai követelmények az ajánlathoz választott tőzsdei lehetőségtől függően eltérőek.

A _kapcsolatfelvételi_ lista lehetőséghez nem tartozik technikai követelmény. Lehetősége van az Ügyfélkapcsolat-kezelési (CRM) rendszer összekapcsolására az ügyfél-érdeklődők kezeléséhez. Ez a cikk későbbi részében, a [Customer leads](#customer-leads) szakaszban olvasható.

Az azonnali letöltés _(ingyenes)_, az _ingyenes próbaverzió_ és az _értékesítés a Microsoft_ listázási lehetőségei a következő technikai követelményekkel rendelkeznek:

- Az SaaS-alkalmazásnak több-bérlős megoldásnak kell lennie.
- A felhasználók hitelesítéséhez a Microsoft-fiókok (MSA) és a [Azure Active Directory (Azure ad)](https://azure.microsoft.com/services/active-directory/) is engedélyezhető.
- Létre kell hoznia egy kezdőlapot. Miután egy felhasználó megvásárolta az ajánlatát, a rendszer a kezdőlapra irányítja. Ez segít a szükséges további kiépítés vagy beállítás elvégzésében. A Kezdőlap létrehozásával kapcsolatos útmutatásért tekintse meg a következő cikkeket:
  - [A kereskedelmi piactéren felépítheti a transacter SaaS-ajánlat kezdőlapját](azure-ad-transactable-saas-landing-page.md)
  - [Az ingyenes vagy próbaverziós SaaS-ajánlat kezdőlapjának összeállítása a kereskedelmi piactéren](azure-ad-free-or-trial-landing-page.md)

Ezek a kiegészítő technikai követelmények csak a _Microsofton keresztüli értékesítés_ (transactd) listázási lehetőségre vonatkoznak:

- Az Azure AD egyszeri bejelentkezéses (SSO) Identitáskezelés és hitelesítés szükséges ahhoz, hogy a vásárló felhasználó hozzáférjen a kezdőlaphoz. Részletes útmutatásért lásd: [Az Azure ad és a transactd SaaS-ajánlatok a kereskedelmi piactéren](azure-ad-saas.md).
- Az Azure Marketplace és a Microsoft AppSource integrálásához a [SaaS-teljesítési API-kat](./partner-center-portal/pc-saas-fulfillment-api-v2.md) kell használnia. Ki kell jelölnie egy olyan szolgáltatást, amely képes kommunikálni az SaaS-előfizetéssel egy felhasználói fiók és egy szolgáltatáscsomag létrehozásához, frissítéséhez és törléséhez. A kritikus API-módosításokat 24 órán belül támogatni kell. A nem kritikus API-változások rendszeresen jelennek majd meg. Az összegyűjtött mezők használatát leíró diagramok és részletes magyarázatok az [API](./partner-center-portal/pc-saas-fulfillment-api-v2.md)-k dokumentációjában találhatók.
- Létre kell hoznia legalább egy csomagot az ajánlathoz. A csomag díjszabása a közzététel előtt kiválasztott árképzési modell alapján történik: _átalánydíjas_ vagy _felhasználónkénti_. A [csomagokkal](#plans) kapcsolatos további részletekért lásd a cikk későbbi részében leírtakat.
- Az ügyfél bármikor lemondhatja az ajánlatot.

### <a name="technical-information"></a>Technikai információk

Ha Transact-ajánlatot hoz létre, az alábbi információkat kell összegyűjtenie a **technikai konfiguráció** lapon. Ha úgy dönt, hogy a tranzakciókat egymástól függetlenül dolgozza fel egy Transact-ajánlat létrehozása helyett, ugorja át ezt a szakaszt, és lépjen a [teszt meghajtók](#test-drives)elemre.

- Kezdőlap **URL-címe**: a SaaS-webhely URL-címe (például: `https://contoso.com/signup` ) a felhasználók az ajánlatnak a kereskedelmi piactérről való beszerzését követően lesznek átirányítva, és az újonnan létrehozott SaaS-előfizetésből aktiválják a konfigurációs folyamatot. Ez az URL-cím olyan tokent kap, amely a teljesítési API-k meghívására használható az interaktív regisztrációs oldal kiépítési részleteinek beszerzéséhez.

  Ezt az URL-címet a piactér-vásárlási azonosító jogkivonat paraméterrel hívja meg a rendszer, amely egyedileg azonosítja az adott ügyfél SaaS-megvásárlását. Ezt a tokent a megfelelő SaaS-előfizetés részleteire kell cserélnie az [API feloldása](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription)paranccsal. Ezek az adatok és bármely más, az ügyfél interaktív weblapjának részeként gyűjtött adatokat felhasználhatják az ügyfél bevezetési élményének elindítására, amelynek végül az API-val kell megkötnie az előfizetési időszak megkezdéséhez szükséges aktiválási hívást. Ezen az oldalon a felhasználónak az Azure Active Directory (Azure AD) használatával egy kattintással történő hitelesítéssel kell regisztrálnia.

  Ez az URL-cím a piactér-beli vásárlási azonosító jogkivonat paraméterrel is meghívható, ha az ügyfél felügyelt SaaS-élményt indít a Azure Portal vagy Microsoft 365 felügyeleti központból. Mindkét folyamatot kezelni kell: Ha a jogkivonatot az új ügyfél vásárlása után első alkalommal adták meg, és ha a felhasználó az SaaS-megoldást kezelő meglévő ügyfelek számára is elérhetővé válik.

    Az Ön által konfigurált Kezdőlap 24/7-at kell futtatnia. Ez az egyetlen módszer, ha értesítést kap a SaaS-ajánlatok kereskedelmi piactéren végzett új vásárlásáról, vagy az ajánlat aktív előfizetésére vonatkozó konfigurációs kérelmekről.

- **Kapcsolat webhook**: minden olyan aszinkron esemény esetében, amelyet a Microsoftnak el kell küldenie Önnek (például ha egy SaaS-előfizetés meg lett szakítva), a kapcsolat webhook URL-címét kell megadnia. Meghívjuk ezt az URL-címet, hogy értesítse Önt az eseményről.

  Az Ön által megadott webhooknak 24/7-et kell futtatnia. Ez az egyetlen módja, ha értesítést kap a kereskedelmi Piactéren keresztül megvásárolt ügyfelei SaaS-előfizetésével kapcsolatos frissítésekről.

  > [!NOTE]
  > A Azure Portalon belül létre kell hoznia egy egybérlős [Azure Active Directory (Azure ad) alkalmazás regisztrációját](../active-directory/develop/howto-create-service-principal-portal.md). A piactér API-k meghívásakor a megoldás hitelesítéséhez használja az alkalmazás regisztrációjának adatait. A [bérlő azonosítójának](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)megkereséséhez lépjen a Azure Active Directoryra, és válassza a **Tulajdonságok** lehetőséget, majd keresse meg a felsorolt címtár-azonosító számát. Például: `50c464d3-4930-494c-963c-1e951d15360e`.

- **Azure Active Directory bérlő azonosítója**: (más néven CÍMTÁR-azonosító). A Azure Portalon belül [regisztrálnia kell egy Azure Active Directory (ad-) alkalmazást](../active-directory/develop/howto-create-service-principal-portal.md) , hogy hozzá lehessen adni az API hozzáférés-vezérlési listájához (ACL), hogy megbizonyosodjon róla, hogy Ön jogosult a meghívására. A Azure Active Directory (AD) alkalmazáshoz tartozó bérlői azonosító megkereséséhez nyissa meg a [Alkalmazásregisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) panelt Azure Active Directory. A **megjelenítendő név** oszlopban válassza ki az alkalmazást. Ezután keresse meg a felsorolt **címtár (bérlő) azonosítószámát** (például `50c464d3-4930-494c-963c-1e951d15360e` ).

- **Azure Active Directory alkalmazás azonosítója**: az [alkalmazás azonosítóját](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)is meg kell adnia. Az érték beszerzéséhez lépjen a Azure Active Directory [Alkalmazásregisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) paneljére. A **megjelenítendő név** oszlopban válassza ki az alkalmazást. Ezután keresse meg a felsorolt alkalmazás (ügyfél) AZONOSÍTÓSZÁMÁT (például: `50c464d3-4930-494c-963c-1e951d15360e` ).

  Az Azure AD-alkalmazás azonosítója a partner Center-fiókban található közzétevői AZONOSÍTÓhoz van társítva. Ugyanezt az azonosítót kell használnia az adott fiókban lévő összes ajánlathoz.

  > [!NOTE]
  > Ha a közzétevő két vagy több különböző fiókkal rendelkezik a partner Centerben, akkor az Azure AD-alkalmazás regisztrációs adatai csak egyetlen fiókban használhatók. Ha ugyanazt a bérlői azonosítót használja, az alkalmazás-azonosító pár nem támogatott egy másik közzétevői fiókban.

## <a name="test-drives"></a>Tesztverziók
Dönthet úgy, hogy engedélyezi a tesztelési meghajtót az SaaS-alkalmazáshoz. A tesztelési meghajtók adott számú óráig biztosítanak hozzáférést egy előre konfigurált környezethez. Engedélyezheti a tesztelési meghajtókat bármilyen közzétételi lehetőséghez, azonban ez a funkció további követelményekkel is rendelkezik. További információ a tesztelési meghajtókról: [Mi az a test Drive?](what-is-test-drive.md). A különböző típusú tesztelési meghajtók konfigurálásával kapcsolatos információkért lásd: a tesztvezetés [technikai konfigurációjának tesztelése](test-drive-technical-configuration.md).

> [!TIP]
> A tesztelési meghajtó nem azonos az [ingyenes próbaverzióval](plans-pricing.md#free-trials). Tesztelési meghajtót, ingyenes próbaverziót vagy mindkettőt is kipróbálhat. A megoldás a rögzített időszakra is biztosítja ügyfelei számára a megoldást. A test Drive azonban magában foglalja a termék legfontosabb funkcióit és előnyeit is, amelyek egy valós megvalósítási forgatókönyvben jelennek meg.

## <a name="customer-leads"></a>Ügyfél-érdeklődők

Az ügyfelek adatainak összegyűjtéséhez az ajánlatot az Ügyfélkapcsolat-kezelési (CRM) rendszerhez kell kötni. A rendszer engedélyt kér az ügyféltől az információk megosztására. Az ügyfél adatait, valamint az ajánlat nevét, AZONOSÍTÓját és online áruházát, ahol az ajánlatot megtalálták, a rendszer a konfigurált CRM-rendszerbe küldi el. A kereskedelmi piactér számos CRM-rendszert támogat, valamint egy Azure-tábla használatát vagy egy HTTPS-végpont konfigurálását a Power automatizálás használatával.

A CRM-kapcsolatok bármikor hozzáadhatók vagy módosíthatók az ajánlat létrehozásakor vagy azt követően is. Részletes útmutatásért tekintse [meg a kereskedelmi Marketplace-ajánlat ügyfeleinek vezetőit](partner-center-portal/commercial-marketplace-get-customer-leads.md).

## <a name="selecting-an-online-store"></a>Online áruház kiválasztása

Ha egy SaaS-ajánlatot tesz közzé, az Microsoft AppSource, az Azure Marketplace-en vagy mindkettőn jelenik meg. Minden online áruház egyedi ügyfél-követelményeket szolgál ki. Az AppSource üzleti megoldásokhoz, az Azure Marketplace pedig informatikai megoldásokhoz használható. Az ajánlat típusa, a Transact képességek és a kategóriák határozzák meg az ajánlat közzétételének helyét. A kategóriák és alkategóriák a megoldás típusa alapján vannak leképezve minden online áruházhoz. 

Ha az SaaS-ajánlat *az IT* -megoldás (Azure Marketplace) és egy üzleti megoldás (AppSource) is, válasszon ki egy kategóriát és egy alkategóriát az egyes online áruházakhoz. Az online áruházakban közzétett ajánlatoknak IT-megoldásként *és* üzleti megoldásként is rendelkezniük kell az értékkel.

> [!IMPORTANT]
> A [mért számlázással](partner-center-portal/saas-metered-billing.md) rendelkező SaaS-ajánlatok az Azure Marketplace-en és a Azure Portalon keresztül érhetők el. Az SaaS-ajánlatok csak privát csomagokkal érhetők el a Azure Portalon keresztül.

| Mért számlázás | Nyilvános csomag | Saját csomag | Elérhető itt: |
|---|---|---|---|
| Igen             | Igen         | Nem           | Azure Marketplace és Azure Portal |
| Igen             | Igen         | Yes          | Azure Marketplace és Azure Portal * |
| Igen             | Nem          | Igen          | Csak Azure Portal |
| Nem              | Nem          | Igen          | Csak Azure Portal |
|||||

&#42; az ajánlat privát csomagja csak a Azure Portal keresztül érhető el

Például a díjszabással ellátott számlázással és a privát csomaggal (nem nyilvános csomaggal) kapcsolatos ajánlatokat a Azure Portal ügyfelei vásárolják meg. További információ a [Microsoft kereskedelmi piactéren elérhető privát ajánlatokról](private-offers.md).

Az online áruházak által támogatott listaelem-beállításokkal kapcsolatos részletes információkért tekintse meg az [online áruház listázási és díjszabási lehetőségeit](determine-your-listing-type.md#listing-and-pricing-options-by-online-store). A kategóriákkal és alkategóriákkal kapcsolatos további információkért lásd: [kategóriák és alkategóriák a kereskedelmi piactéren](categories.md).

## <a name="legal-contracts"></a>Jogi szerződések

Az ügyfelek beszerzési folyamatának leegyszerűsítése és a szoftvergyártók jogi összetettségének csökkentése érdekében a Microsoft szabványos szerződést kínál, amely a kereskedelmi piactéren elérhető ajánlatokhoz használható. Ha a standard szintű szerződés keretében nyújtja a szoftverét, az ügyfeleknek csak egyszer kell elolvasniuk és elfogadniuk, és nem kell egyéni használati feltételeket létrehozniuk.

Ha úgy dönt, hogy a normál szerződést használja, lehetősége van arra, hogy univerzális módosítási feltételeket adjon hozzá, és legfeljebb 10 egyéni módosítást alkalmazzon a standard szerződéshez. A normál szerződés helyett a saját használati feltételeit is használhatja. Ezeket a részleteket a **Tulajdonságok** lapon fogja kezelni. Részletes információ: [standard szintű szerződés a Microsoft kereskedelmi piactérről](standard-contract.md).

> [!NOTE]
> Miután közzétett egy ajánlatot a kereskedelmi piactérre vonatkozó standard szerződéssel, nem használhatja a saját egyéni használati feltételeit. Ez egy "vagy" forgatókönyv. A megoldást a standard szerződés vagy a saját használati feltételei alapján ajánljuk fel. Ha módosítani szeretné a standard szintű szerződés feltételeit, ezt a standard szintű szerződés módosításaival teheti meg.


## <a name="microsoft-365-integration"></a>Microsoft 365-integráció

A Microsoft 365 integrációja lehetővé teszi, hogy az SaaS-ajánlat a kapcsolódó ingyenes bővítmények, például a Teams alkalmazások, az Office-bővítmények és a SharePoint-keretrendszer megoldásai révén több Microsoft 365 alkalmazás felületén keresztül is elérhetővé tegye a kapcsolódó élményt. Az alábbi információk megadásával megkönnyítheti ügyfelei számára a E2E-megoldás (webszolgáltatás + kapcsolódó beépülő modulok) összes aspektusának felderítését és egy folyamaton belüli üzembe helyezését. 
  - Ha az SaaS-ajánlat integrálva van a Microsoft Graphval, akkor adja meg a SaaS-ajánlat által az integrációhoz használt Azure Active Directory (HRE) alkalmazás AZONOSÍTÓját. A rendszergazdák áttekinthetik a SaaS-ajánlat megfelelő működéséhez szükséges hozzáférési engedélyeket a HRE-alkalmazás AZONOSÍTÓjában, és hozzáférést biztosítanak, ha a telepítés során speciális rendszergazdai engedélyre van szükség. 
    
     Ha úgy dönt, hogy eladja az ajánlatot a Microsofton keresztül, akkor ez ugyanaz a HRE-alkalmazás azonosítója, amelyet a kezdőlapon regisztrált, hogy az ügyfél-előfizetés aktiválásához szükséges alapszintű felhasználói adatokat kapjon. Részletes útmutatásért tekintse [meg a kereskedelmi piactéren a transacter SaaS-ajánlat kezdőlapjának összeállítása](azure-ad-transactable-saas-landing-page.md)című témakört. 
    
   -    Adja meg azoknak a kapcsolódó beépülő moduloknak a listáját, amelyek a csatolni kívánt SaaS-ajánlattal működnek. Az ügyfelek képesek lesznek felderíteni a E2E-megoldást a AppSource, és a rendszergazdák is telepíthetik a SaaS-t és az összes kapcsolódó beépülő modult, amelyhez ugyanahhoz a folyamathoz kapcsolódott Microsoft 365 felügyeleti központban.
    
        A kapcsolódó bővítmények összekapcsolásához meg kell adnia a beépülő modul AppSource hivatkozását, ez azt jelenti, hogy a bővítményt először közzé kell tenni a AppSource-ben. Az összekapcsolható támogatott bővítmény-típusok a következők: Team apps, Office beépülő modulok és SharePoint Framework (SPFx) megoldások. Minden csatolt bővítménynek egyedinek kell lennie egy SaaS-ajánlathoz. 

A kapcsolt termékek esetében a keresés a AppSource-ben egy eredményként fog visszatérni, amely magában foglalja a SaaS és az összes társított bővítményt is. Az ügyfél a SaaS-ajánlat és a társított bővítmények termék részletes oldalai között tud navigálni. A rendszergazdák áttekinthetik és telepíthetik a SaaS-és csatolt bővítményeket ugyanazon a folyamaton belül, a Microsoft 365 felügyeleti központban lévő integrált és csatlakoztatott felhasználói felület segítségével. További információ: [Microsoft 365 alkalmazások tesztelése és telepítése – Microsoft 365-rendszergazda](/microsoft-365/admin/manage/test-and-deploy-microsoft-365-apps).

### <a name="microsoft-365-integration-support-limitations"></a>Microsoft 365 integrációs támogatási korlátozások
A felderítés egyetlen E2E-megoldásként támogatott a AppSource-ben az összes eset esetében, azonban a E2E-megoldás egyszerűsített telepítése a fentiekben leírtak szerint a Microsoft 365 felügyeleti központban nem támogatott a következő esetekben:

   - Ugyanaz a bővítmény több SaaS-ajánlathoz van csatolva.
   - Az SaaS-ajánlat beépülő modulokhoz van csatolva, de nem integrálható Microsoft Graph, és nincs megadva HRE-alkalmazás azonosítója.
  - Az SaaS-ajánlat beépülő modulokhoz van csatolva, de a Microsoft Graph integrációhoz megadott HRE-azonosító több SaaS-ajánlat között is meg van osztva.

 
## <a name="offer-listing-details"></a>Ajánlat részletei

Amikor [új SaaS-ajánlatot hoz létre](create-new-saas-offer.md) a partner Centerben, a szöveg, a képek, a választható videók és az egyéb információk megadására kerül az **ajánlati lista** oldalán. Ez az információ, amelyet az ügyfelek látni fognak, amikor a kereskedelmi piactéren észlelik az ajánlatát, ahogy az a következő példában is látható.

:::image type="content" source="./media/saas/example-saas-1.png" alt-text="Bemutatja, hogyan jelenik meg az ajánlat a Microsoft AppSourceban.":::

**Lehívási leírások**

1. Embléma
2. Kategóriák
3. Iparágak
4. Támogatási címe (hivatkozás)
5. Használati feltételek
6. Adatvédelmi irányelvek
7. Ajánlat neve
8. Összefoglalás
9. Description
10. Képernyőképek/videók
11. Dokumentumok

Az alábbi példa egy ajánlatot mutat be a Azure Portalban.

![A Azure Portal ajánlatát ábrázolja.](./media/example-managed-service-azure-portal.png)

**Leírások meghívása**

1. Cím
1. Leírás
1. Hasznos hivatkozások
1. Képernyőképek

> [!NOTE]
> Az ajánlatban szereplő tartalom nem kötelező angol nyelven lenni, ha az ajánlat leírása a következő kifejezéssel kezdődik: "Ez az alkalmazás csak [nem angol nyelven]".

Ha könnyebben létre szeretné hozni az ajánlatot, készítse elő ezeket az elemeket az idő előtt. Ha másként nincs jelezve, a következő elemek szükségesek.

- **Name (név**): Ez a név jelenik meg a kereskedelmi piactéren az ajánlati lista címeként. A név lehet védjeggyel ellátott. Nem tartalmazhat hangulatjelek (kivéve, ha a védjegyek és a szerzői jogi szimbólumok), és legfeljebb 50 karakter hosszúnak kell lennie.
- **Keresési eredmények összegzése**: az ajánlat célját vagy funkcióját egyetlen mondatként írja le, és ne legyenek sortörések 100 vagy kevesebb karakternél. Ezt az összegzést a kereskedelmi Piactéri lista (ok) keresési eredményei használják.
- **Leírás**: Ez a leírás a kereskedelmi Piactéri listák áttekintésében fog megjelenni. Érdemes figyelembe venni az érték kiosztását, a fő előnyöket, a kívánt felhasználói bázist, a kategória-vagy iparági társításokat, az alkalmazáson belüli vásárlási lehetőségeket, a szükséges közzétételeket, valamint egy hivatkozást, amely további információkat tartalmaz.

    Ez a szövegmező olyan Rich Text Editor-vezérlőkkel rendelkezik, amelyekkel a Leírás még vonzóbbá teheti a leírást. A leírást a HTML-címkék használatával is formázhatja. Ebben a mezőben legfeljebb 3 000 karaktert adhat meg, beleértve a HTML-jelölést is. További tippeket a [nagyszerű alkalmazás leírásának írása](/windows/uwp/publish/write-a-great-app-description)című témakörben talál.

- **Első lépések utasítások**: Ha úgy dönt, hogy eladja az ajánlatot a Microsoft (visszagörgethető ajánlat) szolgáltatáson keresztül, ez a mező kötelező. Ezek az utasítások segítenek az ügyfeleknek az SaaS-ajánlathoz való kapcsolódásban. Akár 3 000 karakterből álló szöveget is hozzáadhat, és a részletesebb online dokumentációra mutató hivatkozásokat is megadhat.
- **Kulcsszavak keresése** (nem kötelező): legfeljebb három olyan kulcsszavat adjon meg, amelyekkel az ügyfelek megtalálják az ajánlatot az online áruházakban. Nem kell megadnia az ajánlat **nevét** és **leírását**: a program automatikusan belefoglalja a szöveget a keresésbe.
- **Adatvédelmi szabályzat hivatkozása**: a vállalata adatvédelmi szabályzatának URL-címe. Érvényes adatvédelmi szabályzatot kell megadnia, és annak biztosításáért felelős, hogy az alkalmazás megfelel az adatvédelmi törvényeknek és előírásoknak.
- **Kapcsolattartási adatok**: a következő partnereket kell megadnia a szervezettől:
  - **Támogatási kapcsolattartó**: adja meg a Microsoft-partnerek nevét, telefonszámát és e-mail-címét, amelyet az ügyfelek nyitott jegyekként használhatnak. Meg kell adnia a támogatási webhely URL-címét is.
  - **Mérnöki kapcsolattartó**: adja meg a Microsoft számára a nevét, telefonszámát és e-mail-címét, ha problémák merülnek fel az ajánlatával kapcsolatban. A kapcsolattartási adatok nem szerepelnek a kereskedelmi piactéren.
  - **CSP programbeli kapcsolat** (nem kötelező): adja meg a nevet, a telefonszámot és az e-mailt, ha bejelentkezett a CSP programba, így ezek a partnerek bármilyen kérdéssel felvehetik Önnel a kapcsolatot. Megadhatja a marketing-anyagok URL-címét is.
- **Hasznos hivatkozások** (nem kötelező): az ajánlat felhasználói számára különböző forrásokra mutató hivatkozásokat is megadhat. Például fórumok, GYIK és kibocsátási megjegyzések.
- **Támogató dokumentumok**: legfeljebb három, az ügyfelek felé irányuló dokumentumot, például tanulmányokat, brosúrákat, ellenőrzőlistákat vagy PowerPoint-bemutatókat biztosíthat.
- **Média – logók**: adjon meg egy PNG-fájlt a **nagyméretű** emblémához. A partner Center ezt fogja használni egy **kis** és **közepes** embléma létrehozásához. Ezeket később is lecserélheti a különböző rendszerképekre.

   - Nagyméretű (216 x 216 és 350 x 350 px, kötelező)
   - Közepes (90 x 90 px, nem kötelező)
   - Kicsi (48 x 48 px, opcionális)

  Ezeket az emblémákat az online áruházak különböző helyein használják:

  - A kis embléma megjelenik az Azure Marketplace keresési eredményei között, valamint a Microsoft AppSource főoldalán és a keresési eredmények oldalon.
  - A közepes embléma akkor jelenik meg, amikor új erőforrást hoz létre Microsoft Azureban.
  - Az Azure Marketplace-en és a Microsoft AppSourceban megjelenik a nagyméretű embléma az ajánlati lista oldalán.

- **Média – képernyőképek**: legalább egy és legfeljebb öt képernyőképet kell hozzáadnia az alábbi követelményekkel, amelyek bemutatják, hogy az ajánlat hogyan működik:
  - 1280 x 720 képpont
  - . png fájl
  - Tartalmaznia kell egy feliratot
- **Média – videók** (nem kötelező): legfeljebb négy videót adhat hozzá az alábbi követelményekkel:
  - Name
  - URL: csak a YouTube vagy a Vimeo szolgáltatásban kell tárolni.
  - Miniatűr: 1280 x 720. png fájl

> [!Note]
> Ajánlatának meg kell felelnie a kereskedelmi piactéren közzétenni kívánt általános [kereskedelmi Piactéri minősítési házirendeknek](/legal/marketplace/certification-policies#100-general) és a [szoftvereknek](/legal/marketplace/certification-policies#1000-software-as-a-service-saas) .

> [!NOTE]
> Az előzetes verzió célközönsége különbözik egy privát csomagtól. Egy privát csomag csak a kiválasztott célközönség számára érhető el. Ez lehetővé teszi, hogy egy egyéni csomagot adott ügyfelekkel egyeztesse. További információt a következő szakaszban talál: csomagok.

Meghívhat a Microsoft-fiók (MSA) vagy a Azure Active Directory (Azure AD) e-mail-címére. Legfeljebb 10 e-mail-címet adjon meg manuálisan, vagy importáljon akár 20-at egy. CSV-fájllal. Ha az ajánlata már élő, akkor is megadhatja az előnézeti közönséget az ajánlat változásainak és frissítéseinek teszteléséhez.

## <a name="plans"></a>Tervek

A visszagörgethető ajánlatok legalább egy csomagot igényelnek. A terv meghatározza a megoldás hatókörét és korlátait, valamint a kapcsolódó díjszabást. Az ajánlathoz több csomagot is létrehozhat, így különböző technikai és díjszabási lehetőségeket biztosíthat ügyfeleinek. Ha úgy dönt, hogy a tranzakciókat egymástól függetlenül dolgozza fel egy Transact-ajánlat létrehozása helyett, a **csomagok** lap nem látható. Ha igen, ugorja át ezt a szakaszt, és folytassa a [további értékesítési lehetőségekkel](#additional-sales-opportunities).

Tekintse meg a [kereskedelmi piactérre vonatkozó terveket és díjszabást](plans-pricing.md) a csomagokkal kapcsolatos általános útmutatásért, beleértve a díjszabási modelleket, az ingyenes próbaverziókat és a saját terveket. Az alábbi fejezetek az SaaS-ajánlatokra vonatkozó további információkat tárgyalják.

### <a name="saas-pricing-models"></a>SaaS-díjszabási modellek

Az SaaS-ajánlatok két díjszabási modell egyikét használhatják az egyes csomagokhoz: _átalánydíjas_ _vagy felhasználónként._ Az ugyanabban az ajánlatban található összes csomagot ugyanahhoz az árképzési modellhez kell társítani. Egy ajánlathoz például nem tartozhat egy átalánydíjas és egy másik, felhasználónként megvásárolt csomag.

**Átalány** – az ajánlathoz való hozzáférés engedélyezése egyetlen havi vagy éves átalány-díjszabással. Ezt más néven a hely alapú díjszabásnak is nevezzük. Ezzel az árképzési modellel meghatározhat olyan mért csomagokat is, amelyek a Piactéri mérési szolgáltatás API-ját használják a nem az átalány által jelzett használati díjakra. A mért számlázással kapcsolatos további információkért tekintse meg [a kereskedelmi Piactéri mérési szolgáltatással rendelkező SaaS díjszabású számlázása](./partner-center-portal/saas-metered-billing.md)című témakört. Ezt a beállítást akkor is érdemes használni, ha az SaaS-szolgáltatás használati viselkedése burst.

**Felhasználónkénti** – az ajánlathoz való hozzáférés engedélyezése az ajánlathoz hozzáférő felhasználók számán vagy a foglalási helyeken. Ezzel a felhasználó-alapú modellel beállíthatja a csomag által támogatott felhasználók minimális és maximális számát. Több tervet is létrehozhat a különböző díjszabások konfigurálásához a felhasználók száma alapján. Ezeket a mezőket nem kötelező megadni. Ha nem jelöli be a jelölőnégyzetet, a rendszer a felhasználók számát úgy értelmezi, hogy nem rendelkezik korláttal (a szolgáltatás által támogatott szolgáltatások közül min. 1 és Max). Ezeket a mezőket a csomag frissítésének részeként lehet szerkeszteni.

> [!IMPORTANT]
> Az ajánlat közzététele után a díjszabási modell nem módosítható. Emellett az ugyanarra az ajánlatra vonatkozó összes csomagnak ugyanazzal az árképzési modellel kell rendelkeznie.

### <a name="saas-billing"></a>SaaS-számlázás

Az Ön (a kiadó) Azure-előfizetésében futó SaaS-alkalmazások esetében az infrastruktúra-használat számlázása Önnek közvetlenül történik. az ügyfelek nem látják a tényleges infrastruktúra-használati díjakat. Az Azure-infrastruktúra használati díját a szoftverlicenc-díjszabásban kell megfizetnie, hogy kompenzálja a megoldás futtatásához üzembe helyezett infrastruktúra költségeit.

A Microsoft által nyújtott SaaS-alkalmazások havi vagy évenkénti számlázása a [mért számlázási szolgáltatás](./partner-center-portal/saas-metered-billing.md)használatával, felhasználónként vagy használati díj alapján történik. A kereskedelmi piactér egy ügynökségi modellen működik, amellyel a kiadók díjszabást, Microsoft-számlákat és a Microsoftot fizetnek a kiadók számára, miközben az ügynökség díját is megtartják.

Az alábbi példa a költségek és a kifizetések mintájának részletezését mutatja be az Ügynökség modelljének bemutatására. Ebben a példában a Microsoft $100,00-es számlát küld az ügyfélnek a szoftverlicenc számára, és kifizeti a $80,00-et a közzétevőnek.

| A licenc díja | $100/hó |
| ------------ | ------------- |
| Azure-használati díj (D1/1-Core) | A számlázás közvetlenül a közzétevőre történik, nem az ügyfél |
| Az ügyfelet a Microsoft számlázza | $100,00/hó (a kiadónak az összes felmerült vagy továbbított infrastruktúra-költséget kell figyelembe vennie a licenc díja alapján) |
| **Microsoft-számlák** | **$100/hó** |
| A Microsoft a licencelési díj 80%-ában fizet<br>`*` A minősített SaaS-alkalmazások esetében a Microsoft a licencelési díj 90%-ában fizet| $80,00/hó<br>``*`` $90,00/hó |
|||

**`*` Csökkentett Piactéri szolgáltatási díj** – a kereskedelmi piactéren közzétett egyes SaaS-ajánlatok esetében a Microsoft a piactér szolgáltatási díját 20%-kal csökkenti (a Microsoft kiadói szerződésben leírtak szerint) 10%-ra. Az ajánlat (ok) minősítéséhez az ajánlat (oka) t a Microsoft Azure IP közös értékesítésű incentivized kell megjelölni. A jogosultságot minden naptári hónap végét megelőzően legalább öt (5) munkanapon belül teljesíteni kell, hogy megkapják a Piactéri szolgáltatási díjat. Ha a jogosultság teljesül, a csökkentett szolgáltatási díjat az összes tranzakció a következő hónap első napján érvényes, és továbbra is érvényben marad, amíg az Azure-beli IP-címek közös értékesítésének incentivized állapota elvész. Az IP-címek közös értékesítésének támogathatóságával kapcsolatos további információkért lásd: [a közös értékesítésre vonatkozó követelmények](/legal/marketplace/certification-policies#3000-requirements-for-co-sell-status). A csökkentett Piactéri szolgáltatási díj az Azure IP-incentivized virtuális gépekre, a felügyelt alkalmazásokra és minden más, a kereskedelmi piactéren elérhetővé tett IaaS-ajánlatra is vonatkozik.

## <a name="preview-audience"></a>Előnézet célközönsége

Az előzetes verzió célközönsége az online áruházakban élő közzététel előtt is elérheti az ajánlatát. Megtekinthetik, hogy az ajánlat hogyan fog megjelenni a kereskedelmi piactéren, és az élő közzététel előtt tesztelje a végpontok közötti funkciókat. 

A **célközönség előnézete** lapon megadhatja az előzetes verziójú célközönséget. Ez a beállítás nem érhető el, ha úgy dönt, hogy a tranzakciókat egymástól függetlenül dolgozza fel, ahelyett, hogy az ajánlatot a Microsofton keresztül eladja. Ha igen, kihagyhatja ezt a szakaszt, és [további értékesítési lehetőségekhez](#additional-sales-opportunities)juthat.

## <a name="test-offer"></a>Tesztelési ajánlat

Mielőtt közzéteszi az ajánlatát, az előzetes verzió funkciójának használatával fejlesztheti technikai megvalósítását, tesztelheti és kísérletezheti a különböző díjszabási modellekkel.

Ha az SaaS-ajánlatot a legalacsonyabb kockázattal kívánja fejleszteni és tesztelni, javasoljuk, hogy hozzon létre egy tesztelési és fejlesztési (DEV) ajánlatot a kísérletezéshez és teszteléshez. A fejlesztői ajánlat külön lesz az éles üzemi (Production) ajánlattól.

A fejlesztői ajánlat véletlen megvásárlásának megelőzése érdekében soha nem fogja leküldeni a **Go Live** gombot a fejlesztői ajánlat élő közzétételéhez.

![A partneri központban elérhető ajánlat áttekintés lapját mutatja be. Megjelenik a Go Live gomb és az előzetes verzió hivatkozásai. Az ellenőrzési jelentés megtekintése hivatkozás az automatikus ellenőrzés alatt is látható.](./media/review-publish-offer/publish-status-saas.png)

Íme néhány ok arra, hogy hozzon létre egy külön fejlesztői ajánlatot a fejlesztési csapat számára, amely a PROD-ajánlat fejlesztéséhez és teszteléséhez használható:

- A véletlen ügyfelek költségeinek elkerülése
- Árképzési modellek kiértékelése
- Nem ad hozzá olyan terveket, amelyek nem célozzák meg a tényleges ügyfeleket

### <a name="avoid-accidental-customer-charges"></a>A véletlen ügyfelek költségeinek elkerülése

Ha a PROD-ajánlat helyett fejlesztői ajánlatot használ, és fejlesztési és éles környezetként kezeli őket, elkerülheti a véletlen díjakat az ügyfeleknek.

Javasoljuk, hogy két különböző Azure AD-alkalmazást regisztráljon a Marketplace API-k meghívásához. A fejlesztők egy Azure AD-alkalmazást fognak használni a fejlesztői ajánlat beállításaival, és az operatív csapat a PROD alkalmazás regisztrációját fogja használni. Ezzel elkülönítheti a fejlesztői csapatot a véletlen hibák kijavításával, például az API meghívásával, hogy megszakítsa az ügyfél előfizetését, amely havonta 100 000 USD-t fizet. Azt is elkerülheti, hogy az ügyfél nem a felhasznált mért használatot használja.

### <a name="evaluate-pricing-models"></a>Árképzési modellek kiértékelése

A fejlesztői ajánlaton belüli tesztelési modellek csökkentik a kockázatokat, amikor a fejlesztők különböző díjszabási modellekkel kísérleteznek.

A kiadók a fejlesztői ajánlatban létrehozhatják a szükséges terveket annak meghatározásához, hogy melyik díjszabási modell a legmegfelelőbb az ajánlathoz. Előfordulhat, hogy a fejlesztők több csomagot kívánnak létrehozni a fejlesztői ajánlatban a különböző díjszabási kombinációk teszteléséhez. Előfordulhat például, hogy az egyéni mért dimenziók különböző csoportjaival hoz létre terveket. Létrehozhat egy másik csomagot, amely az átalány és az egyéni mért dimenziók kombinációját is létrehozhatja.

Több díjszabási lehetőség teszteléséhez létre kell hoznia egy csomagot az egyes egyedi díjszabási modellekhez. További információért lásd: [csomagok](#plans).

### <a name="not-adding-plans-that-do-not-target-actual-customers"></a>Nem ad hozzá olyan terveket, amelyek nem célozzák meg a tényleges ügyfeleket

Fejlesztési és tesztelési célú fejlesztői ajánlatunk segítségével csökkentheti a nem megfelelő adatmennyiséget a gyártási ajánlatban. Nem törölhet például olyan terveket, amelyeket a különböző díjszabási modellek vagy technikai konfigurációk tesztelésére hozott létre (támogatási jegy bejelentése nélkül). Így a fejlesztői ajánlatban tesztelési terveket hozhat létre, így csökkentheti a gyártási ajánlat zsúfoltságát.

A termék-és marketing csapatok a PROD-ajánlatban lévő rendetlenséget okoznak, mivel a tényleges ügyfelek megcélzásához szükséges összes csomagra számíthatnak. Különösen a nagy csapatok esetében, akik az összes különböző munkaterületet szeretnének használni, két ajánlat létrehozása két különböző környezetet biztosít a fejlesztéshez és a gyártási környezetekhez. Bizonyos esetekben előfordulhat, hogy több fejlesztői ajánlatot szeretne létrehozni egy nagyobb csapat támogatásához, amely különböző tesztelési forgatókönyveket futtató személyekkel rendelkezik. A különböző csapattagok számára a fejlesztési ajánlatban elkülönítve dolgozhatnak, és a termelési terveket a lehető leghamarabb megtarthatja az éles környezetben.

A fejlesztői ajánlat tesztelése segít elkerülni a 30 egyéni mért méretet. A fejlesztők különböző mérési kombinációkat kipróbálhatnak a fejlesztői ajánlatban anélkül, hogy ez hatással lenne az egyéni mért dimenzióra a PROD-ajánlatban.

## <a name="additional-sales-opportunities"></a>További értékesítési lehetőségek

Dönthet úgy, hogy a Microsoft által támogatott marketing-és értékesítési csatornákat is bekapcsolja. Ha az ajánlatot a partner Centerben hozza létre, két lap jelenik meg a folyamat vége felé:

- **Viszonteladás a CSP**-n keresztül: ezzel a beállítással engedélyezheti, hogy a Microsoft Cloud-szolgáltatói (CSP) partnerek egy csomagban lévő ajánlat részeként eladják a megoldást. További információ erről a programról: [Cloud Solution Provider program](cloud-solution-providers.md).

- **Közös értékesítés a Microsofttal**: Ez a beállítás lehetővé teszi, hogy a Microsoft értékesítési csapatai az ügyfelek igényeinek kiértékelése során fontolják meg az Ön IP-címének közös értékesítését. A közös értékesítésre vonatkozó jogosultsággal kapcsolatos részletekért lásd: [a közös értékesítésre vonatkozó követelmények](/legal/marketplace/certification-policies#3000-requirements-for-co-sell-status). Az ajánlat értékelésre való előkészítésével kapcsolatos részletes információkért lásd: [közös értékesítés lehetőség a partner Centerben](commercial-marketplace-co-sell.md).

## <a name="next-steps"></a>Következő lépések

- [SaaS-ajánlat létrehozása a kereskedelmi piactéren](create-new-saas-offer.md)
- [Ajánlatlistákra vonatkozó ajánlott eljárások](gtm-offer-listing-best-practices.md)
