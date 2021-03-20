---
title: Azure-alkalmazás ajánlatának megtervezése a kereskedelmi piactéren
description: Megtudhatja, hogyan tervezhet új Azure-alkalmazást az Azure Marketplace-en való listázáshoz vagy értékesítéshez, vagy a Cloud Solution Provider (CSP) programon keresztül a Microsoft partner Center kereskedelmi piactér portáljának használatával.
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: f98f128e56810e3dd710ce454c8dc34b333690d1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98879292"
---
# <a name="plan-an-azure-application-offer-for-the-commercial-marketplace"></a>Azure-alkalmazás ajánlatának megtervezése a kereskedelmi piactéren

Ez a cikk ismerteti az Azure-alkalmazások Microsoft kereskedelmi piactérről való közzétételének különböző lehetőségeit és követelményeit.

## <a name="before-you-begin"></a>Előkészületek

Az Azure-alkalmazások tervezésével, létrehozásával és tesztelésével az Azure platform és az ajánlat létrehozásához használt technológiák technikai ismerete szükséges. A mérnöki csapatnak ismernie kell a következő Microsoft-technológiákkal kapcsolatos ismereteket:

- Az Azure- [szolgáltatások](https://azure.microsoft.com/services/)alapszintű ismerete.
- [Azure-alkalmazások tervezése és Architect](https://azure.microsoft.com/solutions/architecture/).
- Az [azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), az [Azure Storage](https://azure.microsoft.com/services/?filter=storage#storage)és az [Azure Networking](https://azure.microsoft.com/services/?filter=networking#networking)együttműködésének ismerete.
- [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)működésének ismerete.
- A [JSON](https://www.json.org/)működésének ismerete.

### <a name="technical-documentation-and-resources"></a>Technikai dokumentáció és források

Tekintse át a következő erőforrásokat, amikor megtervezi az Azure-alkalmazás ajánlatát a kereskedelmi piactéren.

- [Azure Resource Manager sablonok ismertetése](../azure-resource-manager/templates/template-syntax.md)
- Gyorsútmutatók:
    - [Azure-gyorssablonok](https://azure.microsoft.com/documentation/templates/)
    - [Azure-sablonok – ajánlott eljárások útmutatója](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)
    - [Alkalmazásdefiníció közzététele](../azure-resource-manager/managed-applications/publish-service-catalog-app.md)
    - [Szolgáltatáskatalógusban elérhető alkalmazás üzembe helyezése](../azure-resource-manager/managed-applications/deploy-service-catalog-quickstart.md)
- Oktatóanyagok:
    - [Definíciós fájlok létrehozása](../azure-resource-manager/managed-applications/publish-service-catalog-app.md)
- Minták
    - [Azure CLI](../azure-resource-manager/managed-applications/cli-samples.md)
    - [Azure PowerShell](../azure-resource-manager/managed-applications/powershell-samples.md)
    - [Felügyelt alkalmazási megoldások](../azure-resource-manager/managed-applications/sample-projects.md)

Az Azure Marketplace-hez készült videó- [létrehozási megoldási sablonok és a felügyelt alkalmazások](https://channel9.msdn.com/Events/Build/2018/BRK3603) átfogó bevezetést biztosítanak az Azure-alkalmazás ajánlatának típusára:

- Milyen ajánlati típusok érhetők el
- Milyen technikai eszközökre van szükség
- Azure Resource Manager sablon létrehozása
- Az alkalmazás felhasználói felületének fejlesztése és tesztelése
- Az alkalmazás ajánlatának közzététele
- Az alkalmazás-felülvizsgálati folyamat

### <a name="suggested-tools"></a>Javasolt eszközök

Az Azure-alkalmazás kezeléséhez válasszon egyet vagy mindkettőt a következő parancsfájl-környezetek közül:

- [Azure PowerShell](/powershell/azure/)
- [Azure CLI](/cli/azure)

Javasoljuk, hogy a következő eszközöket adja hozzá a fejlesztői környezethez:

- [Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md)
- [Visual Studio Code](https://code.visualstudio.com/) a következő kiterjesztésekkel:
    - Kiterjesztés: [Azure Resource Manager eszközök](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    - Kiterjesztés: [szépít](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    - Kiterjesztés: [SZÉPÍT JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Az elérhető eszközöket az [Azure fejlesztői eszközök](https://azure.microsoft.com/tools/) oldalon tekintheti át. Ha a Visual studiót használja, tekintse meg a [Visual Studio Marketplace](https://marketplace.visualstudio.com/)-en.

## <a name="listing-options"></a>Terméklistázási lehetőségek

Az ajánlat közzétételét követően az ajánlat listázási oldalának bal felső sarkában található gomb jelenik meg. Az alábbi képernyőfelvételen az Azure piactéren elérhető ajánlatok listája látható az azonnali _Letöltés_ gombbal. Ha a tesztelési meghajtót is választotta, a _Test Drive_ gomb is megjelenik.

:::image type="content" source="media/create-new-azure-app-offer/azure-app-listing-page.png" alt-text="Az Azure Marketplace-en található listázási oldalt mutatja be.":::

## <a name="test-drive"></a>Tesztelési meghajtó

Engedélyezheti az Azure-alkalmazás ajánlatának tesztelését, amely lehetővé teszi az ügyfelek számára az ajánlat megvásárlását. További információ a tesztelési meghajtókról: [Mi az a test Drive?](what-is-test-drive.md). A különböző típusú tesztelési meghajtók konfigurálásával kapcsolatos információkért lásd: a tesztvezetés [technikai konfigurációjának tesztelése](test-drive-technical-configuration.md).

A [Test Drive ajánlott eljárásairól](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices) is olvashat, és letöltheti a [tesztelési meghajtók áttekintése](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) PDF-fájlt (ellenőrizze, hogy ki van-e kapcsolva az előugró ablakok blokkolása).

> [!NOTE]
> Információk arról, hogy a felhasználónak akkor is meg kell jelennie, ha az összes Azure-alkalmazás egy Azure Resource Manager sablonnal lett implementálva, az Azure-alkalmazásokhoz elérhető skimmingBecause egyetlen [Azure Resource Manager-alapú tesztelési meghajtó](azure-resource-manager-test-drive.md).

## <a name="customer-leads"></a>Ügyfél-érdeklődők

Az ügyfelek adatainak összegyűjtéséhez az ajánlatot az Ügyfélkapcsolat-kezelési (CRM) rendszerhez kell kötni. A rendszer engedélyt kér az ügyféltől az információk megosztására. Az ügyfél adatait, valamint az ajánlat nevét, AZONOSÍTÓját és online áruházát, ahol az ajánlatot megtalálták, a rendszer a konfigurált CRM-rendszerbe küldi el. A kereskedelmi piactér számos CRM-rendszert támogat, valamint egy Azure-tábla használatát vagy egy HTTPS-végpont konfigurálását a Power automatizálás használatával.

A CRM-kapcsolatok bármikor hozzáadhatók vagy módosíthatók az ajánlat létrehozásakor vagy azt követően is. Részletes útmutatásért tekintse [meg a kereskedelmi Marketplace-ajánlat ügyfeleinek vezetőit](partner-center-portal/commercial-marketplace-get-customer-leads.md).

## <a name="categories-and-subcategories"></a>Kategóriák és alkategóriák

Az ajánlatnak a megfelelő kereskedelmi Piactéri keresési területeken való csoportosításához legalább egy legfeljebb két kategóriát választhat. Az egyes elsődleges és másodlagos kategóriák esetében két alkategóriát is választhat. A kategóriák és alkategóriák teljes listájáért tekintse meg az [ajánlott eljárások listáját](gtm-offer-listing-best-practices.md#categories)ismertető témakört.

## <a name="legal-contracts"></a>Jogi szerződések

Az ügyfelek beszerzési folyamatának leegyszerűsítése és a szoftvergyártók jogi összetettségének csökkentése érdekében a Microsoft szabványos szerződést kínál, amely a kereskedelmi piactéren elérhető ajánlatokhoz használható. Ha a standard szintű szerződés keretében nyújtja a szoftverét, az ügyfeleknek csak egyszer kell elolvasniuk és elfogadniuk, és nem kell egyéni használati feltételeket létrehozniuk.

Ha úgy dönt, hogy a normál szerződést használja, lehetősége van arra, hogy univerzális módosítási feltételeket adjon hozzá, és legfeljebb 10 egyéni módosítást alkalmazzon a standard szerződéshez. A normál szerződés helyett a saját használati feltételeit is használhatja. Ezeket a részleteket a **Tulajdonságok** lapon fogja kezelni. Részletes információ: [standard szintű szerződés a Microsoft kereskedelmi piactérről](standard-contract.md).

> [!NOTE]
> Miután közzétett egy ajánlatot a kereskedelmi piactérre vonatkozó standard szerződéssel, nem használhatja a saját egyéni használati feltételeit. Ez egy "vagy" forgatókönyv. A megoldást a standard szerződés vagy a saját használati feltételei alapján ajánljuk fel. Ha módosítani szeretné a standard szintű szerződés feltételeit, ezt a standard szintű szerződés módosításaival teheti meg.

## <a name="offer-listing-details"></a>Ajánlat részletei

Amikor új Azure-alkalmazást hoz létre a partner Centerben, szöveget, képet, választható videókat és egyéb részleteket adhat meg az ajánlati lista oldalán. Ez az információ, amelyet az ügyfelek láthatnak, amikor a következő példában látható módon észlelik az ajánlatok listáját az Azure Marketplace-en.

:::image type="content" source="media/create-new-azure-app-offer/example-azure-marketplace-app.png" alt-text="Bemutatja, hogyan jelenik meg az ajánlat az Azure piactéren.":::

#### <a name="call-out-descriptions"></a>Lehívási leírások

1. Embléma
2. Kategóriák
3. Támogatási címe (hivatkozás)
4. Használati feltételek
5. Adatvédelmi szabályzat címe (hivatkozás)
6. Ajánlat neve
7. Összefoglalás
8. Description
9. Képernyőképek/videók

Az alábbi képernyőképen látható, hogyan jelennek meg az ajánlat adatai a Azure Portalban:

[![Bemutatja, hogyan jelennek meg az ajánlat a Azure Portalban.](media/create-new-azure-app-offer/example-virtual-machine-container-iot-edge-saas.png)](media/create-new-azure-app-offer/example-virtual-machine-container-iot-edge-saas.png#lightbox)

#### <a name="call-out-descriptions"></a>Lehívási leírások

1. Cím
2. Leírás
3. Hasznos hivatkozások
4. Képernyőképek

> [!NOTE]
> Az ajánlatban szereplő tartalom nem kötelező angol nyelven lenni, ha az ajánlat leírása a következő kifejezéssel kezdődik: "Ez az alkalmazás csak [nem angol nyelven]".

Ha könnyebben létre szeretné hozni az ajánlatot, készítse elő ezeket az elemeket az idő előtt. Ha másként nincs jelezve, a következő elemek szükségesek.

- **Name (név**): Ez a név jelenik meg a kereskedelmi piactéren az ajánlati lista címeként. A név lehet védjeggyel ellátott. Nem tartalmazhat hangulatjelek (kivéve, ha a védjegyek és a szerzői jogi szimbólumok), és legfeljebb 50 karakter hosszúnak kell lennie.
- **Keresési eredmények összegzése**: az ajánlat célját vagy funkcióját egyetlen mondatként írja le, egyszerű szövegként, Sortörés nélkül, 100 vagy kevesebb karakter. Ezt az összegzést a kereskedelmi Piactéri lista (ok) keresési eredményei használják.
- **Rövid leírás**: legfeljebb 256 karakter hosszú lehet. Ez az összefoglalás az ajánlat részletek lapján jelenik meg.
- **Leírás**: Ez a leírás az Azure Marketplace-lista (ek) áttekintésében fog megjelenni. Érdemes megfontolni egy érték kiosztását, a legfontosabb előnyöket, a kívánt felhasználói bázist, a kategória-vagy iparági társításokat, az alkalmazáson belüli vásárlási lehetőségeket, az ügyfelek igényét vagy a fájdalmat, amelyet az ajánlat címe, a szükséges információk és a további információkra mutató hivatkozás tartalmaz.

    Ez a szövegmező olyan Rich Text Editor-vezérlőkkel rendelkezik, amelyekkel a Leírás még vonzóbbá teheti a leírást. A leírást a HTML-címkék használatával is formázhatja. Ebben a mezőben legfeljebb 3 000 karaktert adhat meg, amely HTML-jelölést és szóközöket tartalmaz. További tippeket a [kereskedelmi piactér ajánlatának leírásában](supported-html-tags.md), a [nagyszerű alkalmazás leírását](/windows/uwp/publish/write-a-great-app-description) és a HTML-címkéket ismertető témakörben talál.

- **Kulcsszavak keresése** (nem kötelező): legfeljebb három olyan keresési kulcsszót adhat meg, amelyeket az ügyfelek az online áruházban kereshetnek. A legjobb eredmény érdekében ezeket a kulcsszavakat is használhatja a leírásában. Nem kell megadnia az ajánlat **nevét** és **leírását**. A szöveg automatikusan belekerül a keresésbe.
- **Adatvédelmi szabályzat hivatkozása**: a vállalata adatvédelmi szabályzatának URL-címe. Érvényes adatvédelmi szabályzatot kell megadnia, és annak biztosításáért felelős, hogy az alkalmazás megfelel az adatvédelmi törvényeknek és előírásoknak.
- **Hasznos hivatkozások** (nem kötelező): az ajánlat felhasználói számára különböző forrásokra mutató hivatkozásokat is megadhat. Például fórumok, GYIK és kibocsátási megjegyzések.
- **Kapcsolattartási adatok**: a szervezet következő névjegyeit kell kijelölnie:
  - **Támogatási kapcsolattartó**: adja meg a Microsoft-partnerek nevét, telefonszámát és e-mail-címét, amelyet az ügyfelek nyitott jegyekként használhatnak. Meg kell adnia a támogatási webhely URL-címét is.
  - **Mérnöki kapcsolattartó**: adja meg a Microsoft számára a nevét, telefonszámát és e-mail-címét, ha problémák merülnek fel az ajánlatával kapcsolatban. A kapcsolattartási adatok nem szerepelnek a kereskedelmi piactéren.
  - **CSP programbeli kapcsolat** (nem kötelező): adja meg a nevet, a telefonszámot és az e-mailt, ha a Cloud Solution Provider (CSP) programhoz bejelentkezett, így ezek a partnerek bármilyen kérdéssel felvehetik Önnel a kapcsolatot. Megadhatja a marketing-anyagok URL-címét is.
- **Média – logók**: adjon meg egy PNG-fájlt a **nagy** méretű emblémához. A partner Center ezt fogja használni egy **kis** és **közepes** embléma létrehozásához. Ezeket később is lecserélheti a különböző rendszerképekre.
  - Nagyméretű (216 x 216 és 350 x 350 px, kötelező)
  - Közepes (90 x 90 px, nem kötelező)
  - Kicsi (48 x 48 px, opcionális)

  Ezeket az emblémákat az online áruházak különböző helyein használják:
  - A kis embléma az Azure piactér keresési eredményei között jelenik meg.
  - A közepes embléma akkor jelenik meg, amikor új erőforrást hoz létre Microsoft Azureban.
  - A nagyméretű embléma megjelenik az ajánlat listázási lapján az Azure Marketplace-en.

  Kövesse az alábbi irányelveket a logókhoz:

  - Az Azure arculata egyszerű színpalettát használ. Törekedjen minél kevesebb alap- és másodlagos szín használatára az emblémában.
  - A portál témaszínei a fehér és a fekete. Ne használja ezeket a színeket az embléma háttérszíneként. Olyan színt használjon, amelynek hatására az embléma felkelti a figyelmet. Javasoljuk az egyszerű alapszínek használatát.
  - Ha átlátszó hátteret használ, az embléma és a szöveg ne legyen fehér, fekete vagy kék.
  - Az embléma megjelenésének és működésének úgy kell lennie, hogy az embléma vagy a háttér színátmenetét ne használja. Ne helyezzen el szöveget az emblémára, még a vállalat vagy a márka nevét se. A homályos képek miatt a rendszer elutasítja a kérelmet.
  - Ügyeljen arra, hogy az embléma ne legyen széthúzva.

- **Média – képernyőképek** (nem kötelező): javasoljuk, hogy adjon hozzá képernyőképeket, amelyek megmutatják, hogyan működik az ajánlata. Legfeljebb öt képernyőképet adhat hozzá az alábbi követelményekkel, amelyek bemutatják, hogy az ajánlat hogyan működik:
  - 1280 x 720 képpont
  - . png fájl
  - Tartalmaznia kell egy feliratot
- **Média – videók** (nem kötelező): legfeljebb öt videót adhat hozzá az alábbi követelményekkel:
  - Name
  - URL: csak a YouTube vagy a Vimeo szolgáltatásban kell tárolni.
  - Miniatűr: 1280 x 720. png fájl

> [!NOTE]
> Ajánlatának meg kell felelnie a kereskedelmi piactéren közzéteendő általános [kereskedelmi Piactéri minősítési szabályzatoknak](/legal/marketplace/certification-policies#100-general.md) .

## <a name="preview-audience"></a>Előnézet célközönsége

Az előzetes verzió célközönsége az online áruházakban élő közzététel előtt is elérheti az ajánlatát, hogy az élő közzététel előtt tesztelje a végpontok közötti funkcionalitást.

> [!NOTE]
> Az előzetes verzió célközönsége különbözik egy privát csomagtól. Egy privát csomag csak a kiválasztott célközönség számára érhető el. Ez lehetővé teszi, hogy egy egyéni csomagot adott ügyfelekkel egyeztesse.

Az előnézeti célközönséget az Azure-előfizetési azonosítók használatával határozhatja meg, valamint az egyes verziók opcionális leírását is. Ezen mezők egyikét sem láthatja az ügyfelek.

## <a name="technical-configuration"></a>Technikai konfiguráció

Az olyan felügyelt alkalmazások esetében, amelyek mérési eseményeket bocsátanak ki a [Marketplace-mérési szolgáltatás API](partner-center-portal/marketplace-metering-service-apis.md)-k használatával, meg kell adnia azt az identitást, amelyet a szolgáltatás a mérési események kibocsátásakor használni fog.

Erre a konfigurációra akkor van szükség, ha a [Batch-használati eseményt](partner-center-portal/marketplace-metering-service-apis.md#metered-billing-batch-usage-event)szeretné használni. Ha [használati eseményt](partner-center-portal/marketplace-metering-service-apis.md#metered-billing-single-usage-event)szeretne beküldeni, a [példány metaadatainak szolgáltatásával](../active-directory/managed-identities-azure-resources/overview.md) lekérheti a [JSON webes jogkivonat (JWT) tulajdonosi jogkivonatát](partner-center-portal/pc-saas-registration.md#how-to-get-the-publishers-authorization-token)is.

- **Azure Active Directory bérlői azonosító** (kötelező): a Azure Portal belül létre kell [hoznia egy Azure Active Directory (ad) alkalmazást](../active-directory/develop/howto-create-service-principal-portal.md) , hogy a két szolgáltatás közötti kapcsolat ellenőrizhető legyen egy hitelesített kommunikáció mögött. A Azure Active Directory (Azure AD) alkalmazás [bérlői azonosítójának](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) megkereséséhez a Azure Active Directory [Alkalmazásregisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) paneljén. A **megjelenítendő név** oszlopban válassza ki az alkalmazást. Ezután keresse meg a **tulajdonságokat**, majd a **címtár (bérlő) azonosítóját** (például: `50c464d3-4930-494c-963c-1e951d15360e` ).
- **Azure Active Directory alkalmazás azonosítója** (kötelező): szüksége lesz az [alkalmazás azonosítójára](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in) és egy hitelesítési kulcsra is. Az alkalmazás AZONOSÍTÓjának megkereséséhez nyissa meg a Azure Active Directory [Alkalmazásregisztrációk](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) paneljét. A **megjelenítendő név** oszlopban válassza ki az alkalmazást, majd keresse meg az **alkalmazás (ügyfél) azonosítóját** (például `50c464d3-4930-494c-963c-1e951d15360e` ). A hitelesítési kulcs megkereséséhez lépjen a **Beállítások** elemre, és válassza a **kulcsok** lehetőséget. Meg kell adnia egy leírást és egy időtartamot, és ezután meg kell adni egy számértéket.

> [!NOTE]
> Az Azure-alkalmazás azonosítója a közzétevő-AZONOSÍTÓhoz lesz társítva, és csak ebben a közzétevő fiókban használható újra.

## <a name="additional-sales-opportunities"></a>További értékesítési lehetőségek

Dönthet úgy, hogy a Microsoft által támogatott marketing-és értékesítési csatornákat is bekapcsolja. Ha az ajánlatot a partner Centerben hozza létre, két lap jelenik meg a folyamat vége felé:

- **Viszonteladás a CSP**-n keresztül: ezzel a beállítással engedélyezheti, hogy a Microsoft Cloud-szolgáltatói (CSP) partnerek egy csomagban lévő ajánlat részeként eladják a megoldást. További információért lásd a [Cloud Solution Provider programot](./cloud-solution-providers.md) .
- **Közös értékesítés a Microsofttal**: Ez a beállítás lehetővé teszi, hogy a Microsoft értékesítési csapatai az ügyfelek igényeinek kiértékelése során fontolják meg az Ön IP-címének közös értékesítését. Az ajánlat kiértékelésre való előkészítésével kapcsolatos részletes információkért lásd: [közös értékesítés lehetőség a kereskedelmi piactéren](commercial-marketplace-co-sell.md). Az IP-címek közös értékesítésével kapcsolatos követelményekért lásd: [a közös értékesítésre vonatkozó követelmények](/legal/marketplace/certification-policies#3000-requirements-for-co-sell-status). Az ajánlat Microsoft CSP-partneri csatornákon keresztüli forgalmazásával kapcsolatos további információkért tekintse meg a [Cloud Solution Providers](cloud-solution-providers.md)című témakört.

További információ: [a felhőalapú üzleti növekedés az Azure Marketplace-szel](https://azuremarketplace.microsoft.com/sell).

## <a name="plans"></a>Tervek

Az Azure-alkalmazások ajánlatához legalább egy csomag szükséges. A terv meghatározza a megoldás hatókörét és korlátait, valamint a kapcsolódó díjszabást, ha van ilyen. Az ajánlathoz több csomagot is létrehozhat, így különböző technikai és díjszabási lehetőségeket biztosíthat ügyfeleinek.

A csomagokkal, például a díjszabási modellekkel és a saját csomagokkal kapcsolatos általános útmutatásért lásd a [kereskedelmi Piactéri ajánlatok csomagjait és díjszabását](plans-pricing.md). Az alábbi fejezetek az Azure-alkalmazási csomagokra vonatkozó további információkat tárgyalják.

### <a name="types-of-plans"></a>A csomagok típusai

Kétféle Azure-alkalmazási csomag létezik: _megoldás sablon_ és _felügyelt alkalmazás_. Mindkét díjcsomag támogatja a megoldás üzembe helyezésének és konfigurálásának automatizálását egyetlen virtuális gépen (VM) túl. Automatizálhatja több erőforrás, többek között a virtuális gépek, a Hálózatkezelés és a tárolási erőforrások megadásának folyamatát összetett megoldások, például IaaS-megoldások biztosítása érdekében. Mindkét projekttípus számos különböző Azure-erőforrást alkalmazhat, beleértve a virtuális gépeket, de nem korlátozódik rájuk.

- A **megoldási sablonok** az egyik fő módszer a megoldás közzétételére a kereskedelmi piactéren. A megoldási sablonok csomagjai nem vonhatók le a kereskedelmi piactéren, de használhatók a kereskedelmi Piactéren keresztül számlázott fizetős virtuális gépek üzembe helyezésére. Használja a megoldás sablonjának típusát, ha az ügyfél felügyeli a megoldást, és a tranzakciók számlázása egy másik csomagon keresztül történik. A megoldási sablonok létrehozásával kapcsolatos további információkért lásd: [Mi az Azure Resource Manager?](../azure-resource-manager/management/overview.md)
- A **felügyelt alkalmazási** csomagok segítségével egyszerűen hozhat létre és biztosíthat teljes körűen felügyelt, kulcsrakész alkalmazásokat ügyfelei számára. Ugyanazokkal a képességekkel rendelkeznek, mint a megoldási sablonok, és néhány fő különbség:
    - Az erőforrások üzembe helyezése egy erőforráscsoporthoz történik, és az alkalmazás közzétevője felügyeli. Az erőforráscsoport az ügyfél előfizetésében található meg, de a közzétevő bérlőjének egy identitása rendelkezik hozzáféréssel az erőforráscsoporthoz. 
    - Közzétevőként meg kell adnia a megoldás folyamatos támogatásának költségeit, és a tranzakciók a kereskedelmi Piactéren keresztül támogatottak.
 
    Használja a felügyelt alkalmazáscsomag típusát, ha Ön vagy az ügyfél megköveteli, hogy a megoldást egy partner felügyelje, vagy egy előfizetésen alapuló megoldást helyezzen üzembe. További információ a felügyelt alkalmazások előnyeiről és típusairól: az [Azure által felügyelt alkalmazások áttekintése](../azure-resource-manager/managed-applications/overview.md).

## <a name="next-steps"></a>Következő lépések

- A megoldási sablon megtervezéséhez lásd: [megoldási sablon tervezése Azure-alkalmazási ajánlathoz](plan-azure-app-solution-template.md).
- Az Azure által felügyelt alkalmazások megtervezéséhez tekintse meg az Azure [által felügyelt alkalmazások megtervezése Azure-alkalmazásokhoz](plan-azure-app-managed-app.md)című témakört.