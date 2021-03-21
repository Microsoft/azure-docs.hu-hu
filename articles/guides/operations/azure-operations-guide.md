---
title: Első lépések útmutató az Azure IT-kezelők számára | Microsoft Docs
description: Első lépések útmutató az Azure IT-kezelők számára
author: RicksterCDN
ms.author: rclaus
tags: azure-resource-manager
ms.service: azure
ms.topic: overview
ms.workload: infrastructure
ms.date: 08/24/2018
ms.openlocfilehash: 1eb0bf2c0e214c3e076895903176d15ee792c70c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101093052"
---
# <a name="get-started-for-azure-it-operators"></a>Első lépések az Azure IT-kezelők számára

Ez az útmutató bemutatja a Microsoft Azure infrastruktúra üzembe helyezésével és kezelésével kapcsolatos főbb fogalmakat. Ha még nem ismeri a felhő-számítástechnika vagy az Azure-t, ez az útmutató segítséget nyújt a fogalmakkal, üzembe helyezéssel és felügyelettel kapcsolatos részletek gyors megkezdésében. Az útmutató számos szakasza tárgyal egy olyan műveletet, mint például egy virtuális gép üzembe helyezése, és részletes technikai részletekre mutató hivatkozást biztosít.

## <a name="cloud-computing-overview"></a>A felhőalapú számítógép-használat áttekintése

A felhő-számítástechnika modern alternatívát biztosít a hagyományos helyszíni adatközponthoz. A nyilvános Felhőbeli szállítók az összes számítástechnikai infrastruktúrát és a mögöttes felügyeleti szoftvert biztosítják és kezelik. Ezek a szállítók a Cloud Services széles választékát nyújtják. Ebben az esetben egy felhőalapú szolgáltatás lehet egy virtuális gép, egy webkiszolgáló vagy egy felhőben üzemeltetett adatbázismotor. A felhőalapú szolgáltatóként igény szerint kell kibérelni ezeket a felhőalapú szolgáltatásokat. Ennek során a hardveres karbantartás Capital-költségét egy működési költségre alakítja át. A felhőalapú szolgáltatás a következő előnyöket is biztosítja:

- Nagyméretű számítási környezetek gyors üzembe helyezése

- A már nem szükséges rendszerek gyors felszabadítása

- A hagyományosan összetett rendszerek, például a terheléselosztó egyszerű üzembe helyezése

- Rugalmas számítási kapacitás vagy méretezés igény szerinti megadásának képessége

- További költséghatékony számítástechnikai környezetek

- Hozzáférés bárhonnan webalapú portálról vagy programozott automatizálásról

- A legtöbb számítási és alkalmazási igényeknek megfelelő felhőalapú szolgáltatások

A helyszíni infrastruktúrával teljes mértékben szabályozhatja az üzembe helyezett hardvereket és szoftvereket. Ez a cikk a felskálázásra koncentráló hardveres beszerzési döntéseket vezetett. Erre példa egy olyan kiszolgáló vásárlása, amely több magot is biztosít a maximális teljesítménybeli igények kielégítése érdekében. Ez az infrastruktúra sajnos az igény szerinti időszakon kívül is felhasználható. Az Azure-ban csak a szükséges infrastruktúra helyezhető üzembe, és a beállítás bármikor beállítható. Ez azt eredményezi, hogy a további számítási csomópontok üzembe helyezése a teljesítmény igényének kielégítése érdekében a középpontba kerül. A Cloud Services horizontális felskálázása sokkal költséghatékonyabb, mint a költséges hardverek használata.

A Microsoft számos Azure-adatközpontot üzembe helyezett világszerte, és több megtervezéssel. A Microsoft emellett szuverén felhőket is egyre több régióban, például Kínában és Németországban is. Ebben az esetben csak a legnagyobb globális vállalatok telepíthetnek adatközpontokat, így az Azure-t használva bármilyen méretű vállalat számára könnyedén üzembe helyezhetik szolgáltatásaikat az ügyfelek számára.

A kisvállalkozások számára az Azure egy alacsony díjszabású belépési pontot tesz lehetővé, amellyel gyorsan méretezheti a számítási kapacitások igényét. Ez meggátolja a nagy mértékű kezdeti tőkebefektetést az infrastruktúrában, és szükség szerint rugalmasságot biztosít az építész és a rendszerek újratervezéséhez. A felhő-számítástechnika használata jól illeszkedik az indítási növekedésnek megfelelő, gyors és sikertelen működésű modellhez.

Az elérhető Azure-régiókkal kapcsolatos további információkért lásd: [Azure-régiók](https://azure.microsoft.com/regions/).

### <a name="cloud-computing-model"></a>Felhő-számítástechnikai modell

Az Azure egy felhőalapú számítástechnikai modellt használ az ügyfelek számára biztosított szolgáltatási kategóriák alapján. A szolgáltatás három kategóriája magában foglalja az infrastruktúra-szolgáltatást (IaaS), a szolgáltatásként szolgáló platformot és a szolgáltatott szoftvereket (SaaS). A szállítók megosztanak egy vagy több felelősséget az egyes kategóriákban található számítástechnikai verem összetevőihez. Vessünk egy pillantást a felhőalapú számítástechnika egyes kategóriáira.
![Felhő-számítástechnikai verem összehasonlítása](./media/cloud-computing-comparison.png)

#### <a name="iaas-infrastructure-as-a-service"></a>IaaS: szolgáltatásként szolgáló infrastruktúra

A IaaS felhőalapú szállítója az összes fizikai számítási erőforrást és a szükséges szoftvert a számítógép-virtualizálás engedélyezéséhez futtatja és felügyeli. A szolgáltatás ügyfele ezen üzemeltetett adatközpontokban helyez üzembe virtuális gépeket. Bár a virtuális gépek egy külső adatközpontban találhatók, a IaaS-fogyasztó felügyeli az operációs rendszer konfigurálását és felügyeletét, és a mögöttes infrastruktúrát a felhő gyártója számára elhagyhatja.

Az Azure számos IaaS-megoldást tartalmaz, többek között a virtuális gépeket, a virtuálisgép-méretezési csoportokat és a kapcsolódó hálózati infrastruktúrát. A Virtual Machines ideális választás a szolgáltatások Azure-ba való áttelepítésére, mivel ez lehetővé teszi a "lift and SHIFT" áttelepítési modellt. Beállíthat egy virtuális gépet, például a szolgáltatásokat jelenleg futtató infrastruktúrát az adatközpontban, majd áttelepítheti a szoftvert az új virtuális gépre. Előfordulhat, hogy olyan konfigurációs frissítéseket kell létrehoznia, mint az URL-címek más szolgáltatásokhoz vagy tárhelyekhez, de így számos alkalmazást telepíthet át.

A virtuálisgép-méretezési csoportok az Azure Virtual Machinesra épülnek, és egyszerű módot biztosítanak azonos virtuális gépekből álló fürtök üzembe helyezésére. A virtuálisgép-méretezési csoportok támogatják az automatikus skálázást is, így az új virtuális gépek szükség esetén automatikusan üzembe helyezhetők. Így a virtuálisgép-méretezés ideális platformot biztosít a magasabb szintű (például Azure Service Fabric és Azure Container Service) számítási fürtök üzemeltetéséhez.

#### <a name="paas-platform-as-a-service"></a>Péter: szolgáltatásként szolgáló platform

A Pásti segítségével üzembe helyezheti az alkalmazást a Cloud Service-gyártó által biztosított környezetbe. A szállító az összes infrastruktúra-kezelést kihasználva az alkalmazások fejlesztésére és az adatkezelésre koncentrálhat.

Az Azure számos, a többek között elérhető, a Azure App Service és az Azure Cloud Services (webes és feldolgozói szerepkörök) Web Apps funkcióját kínálja. Mindkét esetben a fejlesztőknek több módja is van az alkalmazás üzembe helyezésére anélkül, hogy ismerniük kellene az azt támogató dió-és csavarokat. A fejlesztőknek nem kell virtuális gépeket létrehozniuk, RDP protokoll (RDP) használatával bejelentkezniük, vagy telepíteniük kell az alkalmazást. Csak egy gombot (vagy annak közelségét), a Microsoft által biztosított eszközöket pedig kiépítik a virtuális gépekre, majd üzembe helyezik és telepítik az alkalmazást.

#### <a name="saas-software-as-a-service"></a>SaaS: szolgáltatott szoftver

Az SaaS egy központilag üzemeltetett és felügyelt szoftver. Általában egy több-bérlős architektúrán alapulnak – az alkalmazás egyetlen verzióját használja a rendszer az összes ügyfél számára. Több példányra is méretezhető, így biztosítható a legjobb teljesítmény az összes helyen. Az SaaS-szoftverek általában havi vagy éves előfizetésen keresztüli licenccel rendelkeznek. A SaaS-szoftvergyártók felelősek a szoftver összes összetevőjéért, így minden Ön által kezelt szolgáltatás.

Microsoft 365 egy SaaS-ajánlat jó példája. Az előfizetők havi vagy éves előfizetési díjat fizetnek, és a Microsoft Exchange, a Microsoft OneDrive, valamint a Microsoft Office csomag többi részét szolgáltatásként kapják meg. Az előfizetők mindig a legújabb verziót kapják meg, az Exchange-kiszolgálót pedig felügyeli. Az Office minden évben történő telepítéséhez és frissítéséhez képest ez olcsóbb, és kevesebb erőfeszítést igényel.

## <a name="azure-services"></a>Azure-szolgáltatások

Az Azure számos szolgáltatást kínál a felhőalapú számítástechnikai platformon. Ezek a szolgáltatások többek között a következők:

### <a name="compute-services"></a>Számítási szolgáltatások

Az alkalmazások számítási feladatainak üzemeltetéséhez és futtatásához szükséges szolgáltatások:

- Azure Virtual Machines – Linux és Windows rendszereken egyaránt

- App Services (Web Apps, Mobile Apps, Logic Apps, API Apps és functions alkalmazások)

- Azure Batch (nagyméretű párhuzamos és kötegelt számítási feladatokhoz)

- Azure Service Fabric

- Azure Container Service

### <a name="data-services"></a>Adatszolgáltatások

Az adattárolási és-kezelési szolgáltatások:

- Azure Storage (az Azure Blob, üzenetsor, Table és file Services szolgáltatásokból áll)

- Azure SQL Database

- Azure Cosmos DB

- Microsoft Azure StorSimple

- Azure Cache for Redis

### <a name="application-services"></a>Application Services

Szolgáltatások az alkalmazások létrehozásához és üzemeltetéséhez:

- Azure Active Directory (Azure AD)

- Az elosztott rendszerek csatlakoztatásának Azure Service Bus

- Azure-HDInsight big data feldolgozásához

- Azure Logic Apps integrációs és előkészítési munkafolyamatokhoz

- Azure Media Services

### <a name="network-services"></a>Hálózati szolgáltatások

Szolgáltatások az Azure-ban és az Azure-beli és a helyszíni adatközpontok között:

- Azure Virtual Network

- Azure ExpressRoute

- Azure által biztosított DNS

- Azure Traffic Manager

- Azure Content Delivery Network

Az Azure-szolgáltatásokkal kapcsolatos részletes dokumentációért lásd: az [Azure szolgáltatás dokumentációja](/azure).

## <a name="azure-key-concepts"></a>Az Azure-alapfogalmak

### <a name="datacenters-and-regions"></a>Adatközpontok és régiók

Az Azure egy globális felhőalapú platform, amely világszerte számos régióban általánosan elérhető. Amikor kiépít egy szolgáltatást, alkalmazást vagy virtuális gépet az Azure-ban, a rendszer megkéri, hogy válasszon ki egy régiót. A kiválasztott régió egy adott adatközpontot jelöl, amelyben az alkalmazás fut. További információ: Azure- [régiók](https://azure.microsoft.com/regions/).

Az Azure használatának egyik előnye, hogy az alkalmazásait különböző adatközpontokban helyezheti üzembe az egész világon. A választott régió hatással lehet az alkalmazás teljesítményére. A hálózati kérések késésének csökkentése érdekében optimális megoldás, ha olyan régiót szeretne kiválasztani, amely közelebb van az ügyfelekhez. Azt is megteheti, hogy kijelöl egy régiót, amely megfelel az alkalmazás bizonyos országokban/régiókban való terjesztésének jogi követelményeinek.

### <a name="azure-portal"></a>Azure Portal

A Azure Portal egy webalapú alkalmazás, amely Azure-erőforrások és-szolgáltatások létrehozásához, kezeléséhez és eltávolításához használható. A Azure Portal a következő helyen található: [Portal.Azure.com](https://portal.azure.com). Tartalmaz egy testreszabható irányítópultot és az Azure-erőforrások kezelésére szolgáló eszközöket. Emellett számlázási és előfizetési információkat is biztosít. További információ: [Microsoft Azure Portal áttekintése](https://azure.microsoft.com/documentation/articles/azure-portal-overview/) és az [Azure-erőforrások kezelése a portálon keresztül](../../azure-resource-manager/management/manage-resources-portal.md).

### <a name="resources"></a>Erőforrások

Az Azure-erőforrások az Azure-előfizetésbe telepített egyéni számítási, hálózati, adatkezelési és alkalmazás-üzemeltetési szolgáltatások. Néhány gyakori erőforrás a virtuális gépek, a Storage-fiókok vagy az SQL-adatbázisok. Az Azure-szolgáltatások gyakran több kapcsolódó Azure-erőforrásból állnak. Előfordulhat például, hogy egy Azure-beli virtuális gép tartalmaz egy virtuális gépet, egy Storage-fiókot, egy hálózati adaptert és egy nyilvános IP-címet. Ezek az erőforrások egyenként vagy csoportként hozhatók létre, kezelhetők és törölhetők. Az Azure-erőforrásokat az útmutató későbbi részében részletesebben tárgyaljuk.

### <a name="resource-groups"></a>Erőforráscsoportok

Az Azure-erőforráscsoport egy olyan tároló, amely egy Azure-megoldáshoz kapcsolódó erőforrásokat tárol. Az erőforráscsoport magában foglalhatja a megoldás összes erőforrását, vagy csak azokat az erőforrásokat, amelyeket csoportként szeretne kezelni. Az Azure-erőforráscsoportok részletesebben az útmutató későbbi részében találhatók.

### <a name="resource-manager-templates"></a>Resource Manager-sablonok

Az Azure Resource Manager sablon egy JavaScript Object Notation (JSON) fájl, amely egy vagy több olyan erőforrást határoz meg, amelyet egy erőforráscsoporthoz kíván üzembe helyezni. Meghatározza továbbá az üzembe helyezett erőforrások közötti függőségeket is. A Resource Manager-sablonokat az útmutató későbbi részében részletesebben tárgyaljuk.

### <a name="automation"></a>Automation

A Azure Portal használatával az erőforrások létrehozása, kezelése és törlése mellett a PowerShell vagy az Azure parancssori felület (CLI) segítségével automatizálhatja ezeket a tevékenységeket.

#### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell az Azure kezelésére szolgáló parancsmagokat biztosító modulok összessége. A parancsmagok használatával Azure-szolgáltatásokat hozhat létre, kezelhet és távolíthat el. A parancsmagok segítségével konzisztens, ismételhető és kihasználható központi telepítéseket érhet el. További információt [az Azure PowerShell telepítésével és konfigurálásával](/powershell/azure/install-Az-ps) foglalkozó témakörben talál.

#### <a name="azure-command-line-interface"></a>Azure parancssori felületén

Az Azure parancssori felület egy eszköz, amellyel Azure-erőforrásokat hozhat létre, kezelhet és eltávolíthat a parancssorból. Az Azure CLI Linux, Mac OS X és Windows rendszerekhez érhető el. További információ és technikai részletek: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

#### <a name="rest-apis"></a>REST API-k

Az Azure a Azure Portal felhasználói felületét támogató REST API-k készletére épül. A REST API-k többsége támogatja az Azure-erőforrások és-alkalmazások programozott módon történő üzembe helyezését és kezelését bármely internetalapú eszközről. További információ: [Azure Rest SDK-dokumentáció](/rest/api/index).

### <a name="azure-cloud-shell"></a>Azure Cloud Shell

A rendszergazdák hozzáférhetnek a Azure PowerShell és az Azure CLI-hez egy Azure Cloud Shell nevű böngészővel elérhető felülettel. Ez az interaktív felület rugalmas eszközt biztosít a Linux-és Windows-rendszergazdák számára a választható parancssori felület használatához, vagy bash vagy PowerShell használatával. Azure Cloud Shell a portálon keresztül, önálló webes felületként, illetve számos más hozzáférési pontról is elérheti a [shell.Azure.com](https://shell.azure.com). További információ: [Azure Cloud Shell áttekintése](../../cloud-shell/overview.md).

## <a name="azure-subscriptions"></a>Azure-előfizetések

Az előfizetés egy Azure-fiókhoz kapcsolódó Azure-szolgáltatások logikai csoportosítása. Egyetlen Azure-fiók több előfizetést is tartalmazhat. Az Azure-szolgáltatások számlázása előfizetési alapon történik. Az Azure-előfizetések rendelkeznek egy fiók rendszergazdájával, aki teljes körűen szabályozhatja az előfizetést, valamint egy szolgáltatás-rendszergazdát, aki az előfizetés összes szolgáltatását felügyeli. További információ a klasszikus előfizetési rendszergazdákról: [Azure-előfizetések rendszergazdáinak hozzáadása vagy módosítása](../../cost-management-billing/manage/add-change-subscription-administrator.md). A rendszergazdákon kívül az egyéni fiókok az Azure [-beli szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/overview.md)használatával is megadhatják az Azure-erőforrások részletes ellenőrzését.

### <a name="select-and-enable-an-azure-subscription"></a>Azure-előfizetés kiválasztása és engedélyezése

Az Azure-szolgáltatásokkal való együttműködéshez előfizetésre van szükség. Számos előfizetés-típus érhető el.

**Ingyenes fiókok**: az ingyenes fiók regisztrálására szolgáló hivatkozás az [Azure webhelyén](https://azure.microsoft.com/)található. Ez az Azure-beli erőforrások bármely kombinációjának kipróbálására szolgáló 30 napos időszakra vonatkozó kreditet biztosít. Ha túllépi a kredit összegét, a rendszer felfüggeszti a fiókját. A próbaverzió végén a szolgáltatások leszerelése megszűnik, és a továbbiakban nem fog működni. Bármikor frissítheti az utólagos elszámolású előfizetést.

**MSDN-előfizetések**: Ha rendelkezik MSDN-előfizetéssel, az Azure-kreditek havonta egy adott összeget kapnak. Ha például Microsoft Visual Studio Enterprise with MSDN-előfizetéssel rendelkezik, \$ Az Azure-kreditek havonta 150-et kapnak.

Ha túllépi a kredit összegét, a szolgáltatás le lesz tiltva, amíg a következő hónapban el nem indul. Kikapcsolhatja a költségkeretet, és hozzáadhat egy hitelkártyát, amelyet a további költségekhez kíván használni. A költségek némelyike az MSDN-fiókok esetében diszkontálva van. A Windows Servert futtató virtuális gépek esetében például a Linux árát kell fizetnie, és a Microsoft-kiszolgálókhoz (például Microsoft SQL Server) nem számítunk fel további díjat. Ez lehetővé teszi, hogy az MSDN-fiókok fejlesztési és tesztelési helyzetekben is ideálisak legyenek.

**BizSpark-fiókok**: a Microsoft BizSpark program számos előnyt kínál az indításokhoz. Ezen előnyök egyike az, hogy legfeljebb öt MSDN-fiókhoz fér hozzá a fejlesztési és tesztelési környezetekhez tartozó összes Microsoft-szoftverhez. Ezen öt MSDN-fiókhoz $150 Azure-kreditet kap, és az Azure-szolgáltatások (például Virtual Machines) csökkentett díjait kell fizetnie.

**Utólagos elszámolás: ebben** az előfizetésben a használatért kell fizetnie, ha bankkártyát vagy bankkártyát csatol a fiókhoz. Ha Ön szervezet, akkor is jóváhagyhatja a számlázást.

**Nagyvállalati szerződések**: nagyvállalati szerződéssel a következő évben bizonyos számú szolgáltatást használhat az Azure-ban, és ezt az összeget előre kell fizetnie. Az Ön által létrehozott kötelezettségvállalás az év során lesz felhasználva. Ha túllépi a kötelezettségvállalás összegét, a hátralékos fizetést is kifizetheti. A kötelezettségvállalás mennyiségétől függően kedvezményt kap az Azure szolgáltatásaihoz.

### <a name="grant-administrative-access-to-an-azure-subscription"></a>Rendszergazdai hozzáférés biztosítása egy Azure-előfizetéshez

Az Azure RBAC számos beépített szerepkörrel rendelkezik, amelyek segítségével engedélyeket rendelhet hozzá. Ahhoz, hogy egy felhasználó egy Azure-előfizetés rendszergazdája legyen, rendelje hozzá őket a [tulajdonosi](../../role-based-access-control/built-in-roles.md#owner) szerepkörhöz az előfizetés hatókörében. A Tulajdonos szerepkör teljes hozzáférést biztosít a felhasználónak az előfizetés összes erőforrásához, beleértve a hozzáférés mások számára való delegálásának jogát is.

További információ: Azure- [szerepkörök kiosztása a Azure Portal használatával](../../role-based-access-control/role-assignments-portal.md).

### <a name="view-billing-information-in-the-azure-portal"></a>Számlázási adatok megtekintése a Azure Portalban

Az Azure-t használó fontos összetevő a számlázási információk megtekintésének lehetősége. A Azure Portal részletes betekintést nyújt az Azure számlázási adataiba.

További információ: az [Azure számlázási számla és a napi használati adatok letöltése](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md).

### <a name="get-billing-information-from-billing-apis"></a>Számlázási információk beolvasása a számlázási API-kkal

A számlázási adatokat a portálon is megtekintheti, ha az Azure számlázási REST API-kon keresztül parancsfájl vagy program használatával fér hozzá a számlázási adatokhoz:

- A használati adatok lekéréséhez az Azure használati API-ját használhatja. A számlázási használati információk finomhangolásához a kapcsolódó Azure-erőforrások címkézése is használható. Megcímkézheti például az erőforráscsoport összes erőforrását egy részleg nevével vagy a projekt nevével, majd nyomon követheti az adott címkére vonatkozó költségeket.

- Az Azure-használati [API áttekintésével](../../cost-management-billing/manage/consumption-api-overview.md) az összes elérhető erőforrást listázhatja, valamint a metaadatokat is. A díjszabással kapcsolatos további információkért lásd: az [Azure kiskereskedelmi árainak áttekintése](/rest/api/cost-management/retail-prices/azure-retail-prices).

### <a name="forecast-cost-with-the-pricing-calculator"></a>Előrejelzési költség a díjszabási számológéptel

Az Azure-szolgáltatások díjszabása eltérő. Számos Azure-szolgáltatás alapszintű, standard és prémium szintű csomagokat biztosít. Az egyes szintek általában számos díjszabással és teljesítménnyel rendelkeznek. Az [online árképzési számológép](https://azure.microsoft.com/pricing/calculator)használatával árképzési becsléseket is létrehozhat. A számológép rugalmasságot biztosít egy adott erőforrás vagy egy erőforráscsoport költségeit megbecsülni.

## <a name="azure-resource-manager"></a>Azure Resource Manager

Azure Resource Manager az Azure-erőforrások üzembe helyezési, felügyeleti és szervezeti mechanizmusa. A Resource Manager használatával számos egyéni erőforrást helyezhet el egy erőforráscsoporthoz.

A Resource Manager olyan üzembe helyezési képességeket is tartalmaz, amelyek lehetővé teszik a kapcsolódó erőforrások testreszabható üzembe helyezését és konfigurálását. Például a Resource Manager használatával olyan alkalmazást telepíthet, amely több virtuális gépről, egy terheléselosztóból és egy Azure SQL Database adatbázisból áll, amely egyetlen egységként használható. Ezeket a központi telepítéseket Resource Manager-sablonok használatával fejlesztheti.

A Resource Manager számos előnyt kínál:

- A megoldás összes erőforrását egy csoportként telepítheti, felügyelheti és figyelheti meg az erőforrások különálló kezelése helyett.

- A megoldás többször is üzembe helyezhető a fejlesztési életciklus során, és biztos lehet abban, hogy az erőforrások konzisztens állapotban vannak üzembe helyezve.

- Az infrastruktúrát szkriptek helyett deklaratív sablonok segítségével is kezelheti.

- Meghatározhatja az erőforrások közti függőségeket, hogy azok a megfelelő sorrendben legyenek telepítve.

- Hozzáférés-vezérlést alkalmazhat az erőforráscsoport összes szolgáltatására, mivel az Azure RBAC natív módon integrálva van a felügyeleti platformba.

- Az erőforrásokon címkéket alkalmazhat az előfizetésében lévő összes erőforrás logikus rendszerezéséhez.

- A szervezet számlázását úgy is meghatározhatja, hogy megtekinti az azonos címkével rendelkező erőforrások egy csoportjának költségeit.

### <a name="tips-for-creating-resource-groups"></a>Tippek az erőforráscsoportok létrehozásához

Ha döntéseket hoz az erőforráscsoporthoz, vegye figyelembe a következő tippeket:

- Egy erőforráscsoport összes erőforrásának ugyanazzal az életciklussal kell rendelkeznie.

- Egy erőforrást egyszerre csak egy csoporthoz rendelhet hozzá.

- Bármikor hozzáadhat vagy eltávolíthat egy erőforrást egy erőforráscsoport használatával. Minden erőforrásnak erőforráscsoport-csoporthoz kell tartoznia. Tehát ha eltávolít egy erőforrást egy csoportból, azt hozzá kell adnia egy másikhoz.

- A legtöbb típusú erőforrást bármikor áthelyezheti egy másik erőforráscsoport-csoportba.

- Az erőforráscsoportok erőforrásai különböző régiókban is lehetnek.

- Egy erőforráscsoport használatával szabályozhatja az erőforrásokhoz való hozzáférést.

### <a name="building-resource-manager-templates"></a>Resource Manager-sablonok kiépítése

A Resource Manager-sablonok deklaratívul határozzák meg azokat az erőforrásokat és erőforrás-konfigurációkat, amelyeket a rendszer egyetlen erőforráscsoporthoz fog telepíteni. A Resource Manager-sablonok használatával összetett központi telepítéseket állíthat össze anélkül, hogy felesleges parancsfájlokat vagy manuális konfigurálást kellene végeznie. A sablonok fejlesztése után több alkalommal is üzembe helyezheti azt, és minden alkalommal azonos eredménnyel.

A Resource Manager-sablonok négy szakaszból állnak:

- **Paraméterek**: ezek az üzemelő példány bemenetei. A paraméterek értékét emberi vagy automatizált folyamat is biztosíthatja. Egy példa paraméter a Windows rendszerű virtuális gépek rendszergazdai felhasználóneve és jelszava lehet. A paraméter értékeit a rendszer a telepítés során használja, amikor azok meg vannak adva.

- **Változók**: ezek a telepítés során használt értékek tárolására szolgálnak. A paraméterekkel ellentétben a rendszer nem ad meg változó értéket a telepítési időpontnál. Ehelyett nehéz kódolni vagy dinamikusan generálni.

- **Erőforrások**: a sablon ezen szakasza határozza meg a telepítendő erőforrásokat, például a virtuális gépeket, a Storage-fiókokat és a virtuális hálózatokat.

- **Kimenet**: az üzembe helyezés befejezése után a Resource Manager olyan adatokat adhat vissza, mint a dinamikusan generált kapcsolódási karakterláncok.

Az üzembe helyezés automatizálásához a következő mechanizmusok érhetők el:

- **Függvények**: több függvényt is használhat Resource Manager-sablonokban. Ilyenek például a karakterláncok kisbetűsre konvertálása, egy meghatározott erőforrás több példányának üzembe helyezése és a célként megadott erőforráscsoport dinamikus visszaadása. A Resource Manager-függvények segítenek a dinamikus központi telepítések létrehozásában.

- **Erőforrás-függőségek**: Ha több erőforrást helyez üzembe, néhány erőforrás függőséggel fog rendelkezni másokkal. Az üzembe helyezés megkönnyítése érdekében függőségi deklarációt használhat, hogy a rendszer a többi felhasználó előtt üzembe helyezte a függő erőforrásokat.

- **Sablon csatolása**: egy Resource Manager-sablonon belül egy másik sablonra is hivatkozhat. Ez lehetővé teszi, hogy a központi telepítés lebomlása célzott, célhoz tartozó sablonokba történjen.

Bármely szövegszerkesztőben Resource Manager-sablonokat hozhat létre. A Visual studióhoz készült Azure SDK azonban olyan eszközöket tartalmaz, amelyek segítenek Önnek. A Visual Studióval hozzáadhat erőforrásokat a sablonhoz egy varázsló segítségével, majd közvetlenül a Visual studióból telepítheti és hibakeresést végezhet a sablonon belül. További információ: Azure Resource Manager- [sablonok készítése](../../azure-resource-manager/templates/template-syntax.md).

Végezetül átalakíthatja a meglévő erőforráscsoportokat egy újrafelhasználható sablonba a Azure Portalból. Ez akkor lehet hasznos, ha egy meglévő erőforráscsoport telepíthető sablonját szeretné létrehozni, vagy csak azt szeretné megvizsgálni, hogy az alapul szolgáló JSON-t kívánja-e használni. Egy erőforráscsoport exportálásához válassza az Automation- **parancsfájl** gombot az erőforráscsoport beállításainál.

## <a name="security-of-azure-resources-azure-rbac"></a>Azure-erőforrások biztonsága (Azure RBAC)

A felhasználói fiókokhoz operatív hozzáférést biztosíthat a megadott hatókörben: előfizetés, erőforráscsoport vagy egyedi erőforrás. Ez azt jelenti, hogy erőforrás-készletet telepíthet egy erőforráscsoport, például egy virtuális gép és minden kapcsolódó erőforrás számára, és engedélyeket adhat egy adott felhasználónak vagy csoportnak. Ez a módszer csak a célként megadott erőforráscsoporthoz tartozó erőforrásokhoz való hozzáférést korlátozza. Hozzáférést biztosíthat egyetlen erőforráshoz, például egy virtuális géphez vagy egy virtuális hálózathoz is.

A hozzáférés biztosításához rendeljen hozzá egy szerepkört a felhasználóhoz vagy a felhasználói csoporthoz. Számos előre definiált szerepkör létezik. Saját egyéni szerepköröket is meghatározhat.

Íme néhány példa az [Azure beépített szerepköreire](../../role-based-access-control/built-in-roles.md):

- **Tulajdonos**: az ezzel a szerepkörrel rendelkező felhasználók mindent kezelhetnek, beleértve a hozzáférést is.

- **Olvasó**: az ezzel a szerepkörrel rendelkező felhasználó beolvashatja az összes típusú erőforrást (kivéve a titkokat), de nem végezheti el a módosításokat.

- **Virtuális gép közreműködői**: az ezzel a szerepkörrel rendelkező felhasználók kezelhetik a virtuális gépeket, de nem kezelhetik azt a virtuális hálózatot, amelyhez csatlakoztatva vannak, vagy a VHD-fájlt tartalmazó Storage-fiókkal.

- **SQL**-adatbázis közreműködői: az ezzel a szerepkörrel rendelkező felhasználók kezelhetik az SQL-adatbázisokat, de nem a biztonsággal kapcsolatos házirendjeiket.

- **SQL Security Manager**: az ezzel a szerepkörrel rendelkező felhasználók kezelhetik az SQL-kiszolgálók és-adatbázisok biztonsággal kapcsolatos házirendjeit.

- **Storage-fiók közreműködői**: az ezzel a szerepkörrel rendelkező felhasználók kezelhetik a tárolási fiókokat, de nem kezelhetik a Storage-fiókokhoz való hozzáférést.

További információ: Azure- [szerepkörök kiosztása a Azure Portal használatával](../../role-based-access-control/role-assignments-portal.md).

## <a name="azure-virtual-machines"></a>Azure Virtual Machines

Az Azure Virtual Machines az egyik központi IaaS-szolgáltatás az Azure-ban. Az Azure Virtual Machines támogatja a Windows-vagy Linux-alapú virtuális gépek Microsoft Azure adatközpontban való telepítését. Az Azure Virtual Machines teljes körűen szabályozhatja a virtuális gép konfigurációját, és felelős a szoftverek telepítésével, konfigurálásával és karbantartásával.

Amikor üzembe helyez egy Azure-beli virtuális gépet, kiválaszthat egy rendszerképet az Azure piactéren, vagy megadhatja a saját általánosított képét is. Ez a rendszerkép az operációs rendszer és a kezdeti konfiguráció alkalmazására szolgál. A telepítés során a Resource Manager bizonyos konfigurációs beállításokat fog kezelni, például a számítógép nevének, a rendszergazdai hitelesítő adatoknak és a hálózati konfigurációnak a hozzárendelését. Az Azure virtuálisgép-bővítmények segítségével további automatizálhatja a konfigurációkat, például a Szoftvertelepítés, a víruskereső-konfiguráció és a figyelési megoldások.

Több méretben is létrehozhat virtuális gépeket. A virtuális gép mérete olyan erőforrás-kiosztást diktál, mint például a feldolgozás, a memória és a tárolókapacitás. Bizonyos esetekben, például a RDMA-kompatibilis hálózati adapterek és SSD-lemezek csak bizonyos virtuálisgép-méretekben érhetők el. A virtuálisgép-méretek és-képességek teljes listáját az Azure-beli virtuális gépek méretei [Windows](../../virtual-machines/sizes.md) és [Linux](../../virtual-machines/sizes.md)rendszerhez című témakörben tekintheti meg.

### <a name="use-cases"></a>Használati esetek

Mivel az Azure Virtual Machines szolgáltatás teljes körű felügyeletet biztosít a konfiguráció felett, a kiszolgálói munkaterhelések széles köre számára ideálisak, amelyek nem illeszkednek a Pásti modellbe. A kiszolgálói munkaterhelések, például az adatbázis-kiszolgálók (SQL Server, Oracle vagy MongoDB), a Windows Server Active Directory, a Microsoft SharePoint, és számos további lehetséges a Microsoft Azure platformon való futtatás. Ha kívánja, áthelyezheti az ilyen számítási feladatokat egy helyszíni adatközpontból egy vagy több Azure-régióba anélkül, hogy nagy mennyiségű újrakonfigurálást kellene használnia.

### <a name="deployment-of-virtual-machines"></a>Virtuális gépek üzembe helyezése

Az Azure-beli virtuális gépeket a Azure Portal használatával telepítheti a Azure PowerShell modul automatizálásával, vagy a többplatformos CLI használatával történő automatizálással.

#### <a name="portal"></a>Portál

A virtuális gépek Azure Portal használatával történő telepítése csak aktív Azure-előfizetést és webböngészőhöz való hozzáférést igényel. Számos különböző konfigurációval rendelkező operációsrendszer-lemezképet választhat. Az üzembe helyezés során minden tárolási és hálózati követelmény konfigurálva van. További információ: "virtuális gép létrehozása a Azure Portalban" a [Windows](../../virtual-machines/windows/quick-create-portal.md) és a [Linux](../../virtual-machines/linux/quick-create-portal.md)rendszerhez.

A Azure Portal a virtuális gépek üzembe helyezése mellett a portálról is telepíthet egy Azure Resource Manager sablont. Ez a sablonban definiált összes erőforrás üzembe helyezését és konfigurálását végzi. További információ: [erőforrások üzembe helyezése Resource Manager-sablonokkal és-Azure Portalokkal](../../azure-resource-manager/templates/deploy-portal.md).

#### <a name="powershell"></a>PowerShell

Az Azure-beli virtuális gépek PowerShell használatával történő üzembe helyezése lehetővé teszi az összes kapcsolódó virtuálisgép-erőforrás teljes üzembe helyezésének automatizálását, beleértve a tárolást és a hálózatkezelést is. További információkért lásd: [Windows rendszerű virtuális gép létrehozása a Resource Manager és a PowerShell használatával](../../virtual-machines/windows/quick-create-powershell.md).

Az Azure számítási erőforrások önálló üzembe helyezése mellett a Azure PowerShell modullal is üzembe helyezhet egy Azure Resource Manager-sablont. További információ: [erőforrások üzembe helyezése Resource Manager-sablonokkal és-Azure PowerShellokkal](../../azure-resource-manager/templates/deploy-powershell.md).

#### <a name="command-line-interface-cli"></a>Parancssori felület (CLI)

A PowerShell-modulhoz hasonlóan az Azure parancssori felülete is biztosítja az üzembe helyezés automatizálását, és Windows, OS X és Linux rendszereken is használható. Ha az Azure CLI **virtuális gép gyors létrehozás** parancsát használja, az összes kapcsolódó virtuálisgép-erőforrás (beleértve a tárolást és a hálózatkezelést is) és maga a virtuális gép is telepítve van. További információ: [Linux rendszerű virtuális gép létrehozása az Azure-ban a parancssori felület használatával](../../virtual-machines/linux/quick-create-cli.md).

Hasonlóképpen használhatja az Azure CLI-t egy Azure Resource Manager-sablon üzembe helyezéséhez is. További információ: [erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI-vel](../../azure-resource-manager/templates/deploy-cli.md).

### <a name="access-and-security-for-virtual-machines"></a>Virtuális gépekhez való hozzáférés és biztonság

Ha a virtuális gépet az internetről kívánja elérni, a társított hálózati adaptert, vagy ha szükséges, a Load balancert a nyilvános IP-címmel kell konfigurálni. A nyilvános IP-cím tartalmaz egy DNS-nevet, amely a virtuális gépre vagy a terheléselosztó-re lesz feloldva. További információt az [IP-címek az Azure-ban](../../virtual-network/public-ip-addresses.md)című témakörben talál.

A virtuális géphez a hálózati biztonsági csoport (NSG) erőforrás segítségével kezelheti a hozzáférést a nyilvános IP-címen keresztül. Egy NSG úgy viselkedik, mint egy tűzfal, és lehetővé teszi vagy megtagadja a forgalmat a hálózati adapter vagy az alhálózat között a megadott portok készletén. Ha például egy Távoli asztal munkamenetet szeretne létrehozni egy Azure-beli virtuális géppel, úgy kell konfigurálnia a NSG, hogy engedélyezze a bejövő forgalmat az 3389-as porton. További információ: [portok megnyitása egy virtuális gépen az Azure-ban a Azure Portal használatával](../../virtual-machines/windows/nsg-quickstart-portal.md).

A számítógéprendszer felügyeletéhez hasonlóan a biztonsági hitelesítő adatok és a szoftveres tűzfalak használatával is biztosítania kell az Azure-beli virtuális gépek biztonságát az operációs rendszeren.

## <a name="azure-storage"></a>Azure Storage

Az Azure Storage egy Microsoft által felügyelt szolgáltatás, amely tartós, méretezhető és redundáns tárolást biztosít. Bármilyen erőforrás-telepítési módszer használatával hozzáadhat egy Azure Storage-fiókot erőforrásként bármely erőforráscsoport számára. Az Azure négyféle tárolási típust tartalmaz: blob Storage, File Storage, Table Storage és üzenetsor-tárolás. A Storage-fiókok telepítésekor két fióktípus érhető el, az általános célú és a blob Storage. Az általános célú Storage-fiók mind a négy tárolási típushoz hozzáférést biztosít. A blob Storage-fiókok hasonlóak az általános célú fiókokhoz, de olyan speciális blobokat tartalmaznak, amelyek a gyakori és a ritkán használt hozzáférési rétegeket tartalmazzák. További információ a blob Storage-ról: [Azure Blob Storage](../../storage/blobs/storage-blob-storage-tiers.md).

Az Azure Storage-fiókok különböző szintű redundanciával konfigurálhatók:

- A **helyileg redundáns tárolás** magas rendelkezésre állást biztosít annak biztosításához, hogy az összes információ három példánya legyen szinkronban, mielőtt az írás sikeresnek minősül. Ezeket a másolatokat egyetlen régióban egyetlen létesítmény tárolja. A replikák külön tartalék tartományokban és frissítési tartományokban találhatók. Ez azt jelenti, hogy az adat akkor is elérhető, ha az adattárolási csomópont nem sikerül, vagy offline állapotban van.

- A **geo-redundáns tárolás** a magas rendelkezésre állás érdekében három szinkron másolatot készít az elsődleges régióban tárolt adatmennyiségről, majd aszinkron módon három replikát tesz elérhetővé egy párosított régióban a vész-helyreállítás érdekében.

- A **READ-Access geo-redundáns tárolás** a Geo-redundáns tárolás, valamint a másodlagos régióban tárolt adatolvasási képesség. Ez a funkció lehetővé teszi a részleges katasztrófa utáni helyreállítást. Ha az elsődleges régióval kapcsolatos probléma merül fel, módosíthatja az alkalmazást úgy, hogy csak olvasási hozzáféréssel rendelkezzen a párosított régióhoz.

### <a name="use-cases"></a>Használati esetek

Mindegyik tárolási típus eltérő használati esettel rendelkezik.

#### <a name="blob-storage"></a>Blob Storage

A Word *blob* a *bináris nagyméretű objektum* betűszója. A Blobok strukturálatlan fájlok, például a számítógépen tárolt fájlok. A Blob Storage képes tárolni bármilyen szöveget vagy bináris adatot, például dokumentumot, médiafájlt vagy egy alkalmazástelepítőt. A Blob Storage más néven objektumtárnak is hívható. Az Azure Blob Storage az Azure Virtual Machines adatlemezeket is tartalmazza.

Az Azure Storage háromféle blobot támogat:

- A **blokkos Blobok** a szokásos, legfeljebb 195 GB méretű fájlok tárolására használhatók (4 MB × 50 000 blokk). A blokkblobok elsődleges felhasználási módja olyan fájlok tárolása, amelyeket az elejüktől a végükig olvasnak, ilyenek például a médiafájlok vagy a képfájlok webhelyekhez. Ezek neve blokk Blobok, mert a 64 MB-nál nagyobb fájlokat kis blokkként kell feltölteni. Ezeket a blokkokat ezután konszolidálni (vagy véglegesíteni) kell a végső blobban.

- Az **oldal Blobok** a véletlenszerűen hozzáférő fájlok legfeljebb 1 TB méretű tárolására használhatók. Az oldal-Blobok elsődlegesen az Azure-Virtual Machines, az Azure-beli IaaS számítási szolgáltatás számára tartós lemezeket biztosító virtuális merevlemezek biztonsági másolatának tárolására szolgálnak. Azért hívják őket lapbloboknak, mert 512 bájtos oldalakhoz biztosítanak véletlenszerű olvasási/írási hozzáférést.

- A **hozzáfűzési Blobok** olyan blokkokat tartalmaznak, mint a blokkos Blobok, de a hozzáfűzési műveletekre vannak optimalizálva. Ezeket gyakran használják egy vagy több forrásból származó adatok ugyanarra a blobba való naplózására. Előfordulhat például, hogy az összes nyomkövetési naplózást ugyanabba a hozzáfűző blobba írja, mint egy több virtuális gépen futó alkalmazáshoz. Egy hozzáfűző blob akár 195 GB méretű is lehet.

További információ: az [Azure Blob Storage használatának első lépései a .NET használatával](../../storage/blobs/storage-quickstart-blobs-dotnet.md).

#### <a name="file-storage"></a>File Storage

Az Azure file Storage egy olyan szolgáltatás, amely fájlmegosztást biztosít a felhőben a Standard Server Message Block (SMB) protokoll használatával. A szolgáltatás az SMB 2,1 és az SMB 3,0 egyaránt támogatja. Az Azure file Storage használatával gyorsan és költséges újraírás nélkül áttelepítheti a fájlmegosztást használó alkalmazásokat az Azure-ba. Az Azure-beli virtuális gépeken, a Cloud Servicesben vagy a helyszíni ügyfeleken futó alkalmazások csatlakoztathat egy fájlmegosztást a felhőben. Ez hasonló ahhoz, ahogy egy asztali alkalmazás egy tipikus SMB-megosztást csatlakoztat. Ezután bármennyi alkalmazás-összetevő egyszerre csatlakoztathatja a File Storage-megosztást, és hozzá is férhet.

Mivel a file Storage-megosztás egy szabványos SMB-fájlmegosztás, az Azure-ban futtatott alkalmazások a fájlrendszerbeli I/O-API-kon keresztül férhetnek hozzá a megosztásban lévő információhoz. A fejlesztők ezért meglévő programkódjuk és ismereteik használatával telepíthetik át a meglévő alkalmazásokat. Az informatikai szakemberek PowerShell-parancsmagokkal hozhatják létre, csatlakoztatják és kezelhetik a file Storage-megosztásokat az Azure-alkalmazások felügyeletének részeként.

További információkért lásd: az [Azure file Storage használatának első lépései Windows](../../storage/files/storage-how-to-use-files-windows.md) rendszeren, illetve az [Azure file Storage használata Linux](../../storage/files/storage-how-to-use-files-linux.md)rendszeren.

#### <a name="table-storage"></a>Table Storage

Az Azure Table Storage egy olyan szolgáltatás, amely strukturált NoSQL-adatokat tárol a felhőben. A Table Storage egy séma nélküli kialakítású kulcs/attribútum-tároló. Mivel a Table Storage séma nélküli, egyszerűen alkalmazkodhat az adataihoz az alkalmazás igényeinek megfelelően. Az adatok hozzáférése gyors és költséghatékony, bármilyen alkalmazásról legyen is szó. Hasonló adatmennyiséggel számolva a Table Storage általában határozottan kevesebb költséggel jár, mint egy hagyományos SQL.

A Table Storage segítségével rugalmas adatkészleteket tárolhat, például webalkalmazások felhasználói adatait, címtárakat, eszközadatokat és bármilyen egyéb metaadatot, amelyre a szolgáltatásnak szüksége van. A tábla tetszőleges számú entitását tárolhatja. A Storage-fiókok tetszőleges számú táblát tartalmazhatnak, a Storage-fiók kapacitásának korlátozásával együtt.

További információ: Ismerkedés [Az Azure Table Storage](../../cosmos-db/tutorial-develop-table-dotnet.md)szolgáltatással.

#### <a name="queue-storage"></a>Queue Storage

Az Azure Queue Storage felhőbeli üzenetkezelést biztosít az alkalmazások összetevői között. A méretezhető alkalmazások tervezésekor az alkalmazás-összetevők gyakran le vannak választva, így egymástól függetlenül méretezhetők. A Queue Storage aszinkron üzenetkezelést biztosít az alkalmazások összetevői közötti kommunikációhoz, függetlenül attól, hogy az összetevők a felhőben, asztali gépen, egy helyszíni kiszolgálón vagy egy mobileszközön futnak. A Queue Storage támogatja az aszinkron feladatok kezelését és a feldolgozási munkafolyamatok kialakítását is.

További információ: Ismerkedés [Az Azure üzenetsor-tárolóval](../../storage/queues/storage-dotnet-how-to-use-queues.md).

### <a name="deploying-a-storage-account"></a>Storage-fiók üzembe helyezése

A Storage-fiókok telepítéséhez több lehetőség is rendelkezésre áll.

#### <a name="portal"></a>Portál

A Storage-fiók Azure Portal használatával történő telepítése csak aktív Azure-előfizetést és webböngészőhöz való hozzáférést igényel. Új Storage-fiókot új vagy meglévő erőforráscsoporthoz is telepíthet. A Storage-fiók létrehozása után létrehozhat egy BLOB-tárolót vagy fájlmegosztást a portál használatával. Programozott módon hozhat létre tábla-és üzenetsor-tárolási entitásokat. További információ: [Tárfiók létrehozása](../../storage/common/storage-account-create.md).

Egy Storage-fióknak a Azure Portalból való üzembe helyezésén kívül a portálon üzembe helyezhet egy Azure Resource Manager sablont is. Ezzel telepíti és konfigurálja a sablonban definiált összes erőforrást, beleértve a Storage-fiókokat is. További információ: [erőforrások üzembe helyezése Resource Manager-sablonokkal és-Azure Portalokkal](../../azure-resource-manager/templates/deploy-portal.md).

#### <a name="powershell"></a>PowerShell

Egy Azure Storage-fiók PowerShell használatával történő üzembe helyezése lehetővé teszi a Storage-fiók teljes üzembe helyezésének automatizálását. További információ: [Azure PowerShell használata az Azure Storage](/powershell/module/az.storage/)-ban.

Az Azure-erőforrások önálló üzembe helyezése mellett a Azure PowerShell modullal is üzembe helyezhet egy Azure Resource Manager-sablont. További információ: [erőforrások üzembe helyezése Resource Manager-sablonokkal és-Azure PowerShellokkal](../../azure-resource-manager/templates/deploy-powershell.md).

#### <a name="command-line-interface-cli"></a>Parancssori felület (CLI)

A PowerShell-modulhoz hasonlóan az Azure parancssori felülete is biztosítja az üzembe helyezés automatizálását, és Windows, OS X és Linux rendszereken is használható. A Storage-fiók létrehozásához az Azure CLI **Storage-fiók létrehozása** parancsot használhatja. További információ: [Az Azure CLI használata az Azure Storage](../../storage/blobs/storage-quickstart-blobs-cli.md) -ban.

Hasonlóképpen használhatja az Azure CLI-t egy Azure Resource Manager-sablon üzembe helyezéséhez is. További információ: [erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI-vel](../../azure-resource-manager/templates/deploy-cli.md).

### <a name="access-and-security-for-azure-storage"></a>Hozzáférés és biztonság az Azure Storage-ban

Az Azure Storage különböző módokon érhető el, például a Azure Portal, a virtuális gépek létrehozásakor és működésekor, valamint a Storage ügyféloldali kódtárai között.

#### <a name="virtual-machine-disks"></a>Virtuálisgép-lemezek

Virtuális gép telepítésekor létre kell hoznia egy Storage-fiókot is a virtuális gép operációsrendszer-lemezének és a további adatlemezek tárolásához. Választhat egy meglévő Storage-fiókot, vagy létrehozhat egy újat. Mivel a Blobok maximális mérete 1 024 GB, egyetlen virtuálisgép-lemez maximális mérete 1 023 GB. Nagyobb adatlemezek konfigurálásához több adatlemezt is beállíthat a virtuális gépre, és egyetlen logikai lemezként csoportosíthatja őket. További információ: "Azure-lemezek kezelése" [Windows](../../virtual-machines/windows/tutorial-manage-data-disk.md) és [Linux](../../virtual-machines/linux/tutorial-manage-disks.md)rendszerekhez.

#### <a name="storage-tools"></a>Storage-eszközök

Az Azure Storage-fiókok számos különböző Storage-kezelőn keresztül érhetők el, például a Visual Studio Cloud Explorerben. Ezek az eszközök lehetővé teszik a Storage-fiókok és-adatkeresések tallózását. További információ és a rendelkezésre álló tár-tallózók listája: [Azure Storage-ügyféleszközök](../../storage/common/storage-explorers.md).

#### <a name="storage-api"></a>Tárolási API

A tárolási erőforrásokat bármely olyan nyelv elérheti, amely HTTP/HTTPS-kéréseket tehet elérhetővé. Ezenfelül az Azure Storage számos népszerű nyelvhez biztosít programozási kódtárakat. Ezek a kódtárak leegyszerűsítik az Azure Storage használatát az olyan részletek kezelésével, mint például a szinkron és aszinkron hívás, a műveletek kötegbe állítása, a kivételek kezelése és az automatikus újrapróbálkozások. További információ: az [Azure Storage szolgáltatás REST API referenciája](/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference).

#### <a name="storage-access-keys"></a>Tároló-hozzáférési kulcsok

Mindegyik Storage-fiókhoz két hitelesítő kulcs tartozik, egy elsődleges és egy másodlagos. A tárolási hozzáférési műveletekhez is használható. Ezek a tárolási kulcsok segítenek a Storage-fiókok biztonságossá tételében, és az adatok programozott eléréséhez szükségesek. A kulcsok esetenkénti átváltásának engedélyezése a biztonság növelése érdekében két kulccsal történik. Fontos, hogy a kulcsok biztonságban maradjanak, mert a fiók nevével együtt korlátlan hozzáférést biztosítanak a Storage-fiókban lévő összes adattal.

#### <a name="shared-access-signatures"></a>Közös hozzáférésű aláírások

Ha engedélyezni szeretné a felhasználók számára, hogy szabályozzák a tárolási erőforrásokhoz való hozzáférést, létrehozhat egy közös hozzáférési aláírást. A közös hozzáférésű aláírás egy olyan token, amelyet egy olyan URL-címhez lehet hozzáfűzni, amely lehetővé teszi egy tárolási erőforrás delegált hozzáférését. Bárki, aki rendelkezik a jogkivonattal, elérheti azt az erőforrást, amely az általa megadott engedélyekkel rendelkezik az érvényességi időszakra vonatkozóan. További információ: [közös hozzáférésű aláírások használata](../../storage/common/storage-sas-overview.md).

## <a name="azure-virtual-network"></a>Azure Virtual Network

A virtuális hálózatok a virtuális gépek közötti kommunikáció támogatásához szükségesek. Meghatározhatja az alhálózatokat, az egyéni IP-címet, a DNS-beállításokat, a biztonsági szűrést és a terheléselosztást. Az Azure különböző felhasználási eseteket támogat: csak felhőalapú hálózatok vagy hibrid virtuális hálózatok.

### <a name="cloud-only-virtual-networks"></a>Csak felhőalapú virtuális hálózatok

Alapértelmezés szerint az Azure-beli virtuális hálózatok csak az Azure-ban tárolt erőforrások számára érhetők el. Az ugyanahhoz a virtuális hálózathoz csatlakozó erőforrások kommunikálhatnak egymással. A virtuális gép hálózati adapterei és a terheléselosztó nyilvános IP-címmel társítható, hogy a virtuális gép elérhető legyen az interneten keresztül. Egy hálózati biztonsági csoport segítségével biztonságossá teheti a nyilvánosan elérhető erőforrásokhoz való hozzáférést.

![Azure-Virtual Network 2 szintű webalkalmazáshoz](/azure/load-balancer/media/load-balancer-internal-overview/ic744147.png)

### <a name="hybrid-virtual-networks"></a>Hibrid virtuális hálózatok

A helyszíni hálózatot ExpressRoute vagy helyek közötti VPN-kapcsolat használatával csatlakoztathatja egy Azure-beli virtuális hálózathoz. Ebben a konfigurációban az Azure-beli virtuális hálózat lényegében a helyszíni hálózat felhőalapú kiterjesztése.

Mivel az Azure-beli virtuális hálózat a helyszíni hálózathoz csatlakozik, a létesítmények közötti virtuális hálózatoknak a szervezet által használt címterület egyedi részét kell használniuk. Ugyanúgy, ahogy a különböző vállalati helyek egy adott IP-alhálózathoz vannak rendelve, az Azure a hálózat kibővítésekor egy másik helyen lesz.
A virtuális hálózatok telepítéséhez több lehetőség is rendelkezésre áll.

- [Portál](../..//virtual-network/quick-create-portal.md)

- [PowerShell](../../virtual-network/quick-create-powershell.md)

- [Parancssori felület (CLI)](../../virtual-network/quick-create-cli.md)

- Azure Resource Manager-sablonok

> **Mikor érdemes használni**: Ha az Azure-beli virtuális gépekkel dolgozik, virtuális hálózatokkal fog működni. Ez lehetővé teszi, hogy a virtuális gépeket a helyi adatközpontokhoz hasonló, nyilvános és privát alhálózatokra lehessen szegmentálni.
>
> Első **lépések**: egy Azure-beli virtuális hálózatnak a Azure Portal használatával történő üzembe helyezéséhez csak egy aktív Azure-előfizetés és egy webböngésző hozzáférése szükséges. Új virtuális hálózatot új vagy meglévő erőforráscsoporthoz is telepíthet. Amikor új virtuális gépet hoz létre a portálról, választhat egy meglévő virtuális hálózatot, vagy létrehozhat egy újat. Ismerkedjen meg [a Azure Portal használatával, és hozzon létre egy virtuális hálózatot](../../virtual-network/quick-create-portal.md).

### <a name="access-and-security-for-virtual-networks"></a>Hozzáférés és biztonság virtuális hálózatokhoz

A hálózati biztonsági csoport segítségével biztonságossá teheti az Azure-beli virtuális hálózatokat. A NSG a hozzáférés-vezérlési listák (ACL) azon szabályainak listáját tartalmazza, amelyek engedélyezik vagy megtagadják a virtuális hálózatban lévő virtuálisgép-példányok hálózati forgalmát. A NSG társíthatja az alhálózatokhoz vagy az alhálózaton belüli egyedi virtuálisgép-példányokhoz is. Ha alhálózattal társít egy NSG, az ACL-szabályok az alhálózat összes virtuálisgép-példányára érvényesek lesznek. Emellett tovább korlátozhatja az egyes virtuális gépek forgalmát úgy, hogy közvetlenül az adott virtuális géppel társít egy NSG. További információ: [Hálózati forgalom szűrése hálózati biztonsági csoportokkal](../../virtual-network/network-security-groups-overview.md).

## <a name="next-steps"></a>Következő lépések

- [Windows rendszerű virtuális gép létrehozása](../../virtual-machines/windows/quick-create-portal.md)
- [Linux rendszerű virtuális gép létrehozása](../../virtual-machines/linux/quick-create-portal.md)