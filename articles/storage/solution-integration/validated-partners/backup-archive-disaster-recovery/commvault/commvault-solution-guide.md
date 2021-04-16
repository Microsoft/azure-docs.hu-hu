---
title: Adatok biztonságimentés az Azure-ba a Commvault használatával
titleSuffix: Azure Storage
description: Áttekintést nyújt az Azure tárolási célként való használatának megfontolható tényezőiről és a szükséges lépésekről a Commvault teljes biztonsági mentéséhez és helyreállításához
author: karauten
ms.author: karauten
ms.date: 03/15/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: fa60b6f002e49babc1e1f014bcb941e7953a43a8
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484778"
---
# <a name="backup-to-azure-with-commvault"></a>Biztonsági mentés az Azure-ba a Commvault használatával

Ez a cikk segítséget nyújt a Commvault-infrastruktúra és az Azure Blob Storage integrálásához. Tartalmazza az előfeltételeket, a szempontokat, az implementációt és az üzemeltetési útmutatást. Ez a cikk az Azure helyszíni biztonsági mentési célként és vészhelyreállítási helyként való használatával foglalkozik olyan katasztrófa esetén, amely megakadályozza az elsődleges helyen belüli normál működést.

> [!NOTE]
> A Commvault alacsonyabb helyreállítási időre vonatkozó célkitűzést (RTO) kínál, a Commvault Live Sync megoldást. Ezzel a megoldással olyan készenléti virtuális gépeket használhat, amelyek segítségével gyorsabban helyre tud állni egy azure-beli éles környezetben katasztrófa esetén. Ezek a képességek a jelen dokumentum hatókörére nem terjednek ki.

## <a name="reference-architecture"></a>Referenciaarchitektúra

Az alábbi ábra referenciaarchitektúrát biztosít a helyszíniről az Azure-ba és az Azure-ban üzemelő példányok számára.

![Commvault – Azure-referenciaarchitektúra](../media/commvault-diagram.png)

A meglévő Commvault üzemelő példány egyszerűen integrálható az Azure-ral egy Azure-tárfiók vagy több fiók felhőalapú tárolási célként való hozzáadásával. A Commvault azt is lehetővé teszi, hogy az Azure-on belüli helyszíni biztonsági másolatokat helyreállítsa, így az Azure-ban igény szerinti helyreállítást biztosít.

## <a name="commvault-interoperability-matrix"></a>Commvault együttműködési mátrix

| Számítási feladat | GPv2 és Blob Storage | A cool szint támogatása | Archív szintű támogatás | Data Box-család támogatása |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| Helyszíni virtuális gépek/adatok | v11.5 | v11.5 | 11.10-es | 11.10-es |
| Azure-beli virtuális gépek | 11.5-ös vagy | 11.5-ös vagy | 11.5-ös | N/A |
| Azure-blob | 11.6-os v11.6 | 11.6-os v11.6 | 11.6-os v11.6 | N/A |
| Azure Files | 11.6-os v11.6 | 11.6-os v11.6 | 11.6-os v11.6 | N/A |

## <a name="before-you-begin"></a>Előkészületek

Egy kis előzetes tervezés segít az Azure helyszíni biztonsági mentési célként és helyreállítási helyként való használatában.

### <a name="get-started-with-azure"></a>Az Azure használatának első lépései

A Microsoft egy keretrendszert kínál, amely az Azure használatának első lépésekhez nyújt lehetőséget. A [felhőadaptálási keretrendszer](/azure/architecture/cloud-adoption/) (CAF) a vállalati digitális átalakulás részletes megközelítése, valamint átfogó útmutató a felhő éles környezetben való bevezetésének megtervezéséhez. A felhőbevezető rendszer egy részletes Útmutató az [Azure](/azure/cloud-adoption-framework/ready/azure-setup-guide/) beállításához, hogy gyorsan és biztonságosan induljon el. Az interaktív verziót a következő [Azure Portal.](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade) Mintaarchitektúrákat, alkalmazások üzembe helyezéséhez ajánlott eljárásokat és ingyenes képzési erőforrásokat talál, amelyek segítségével az Azure szakértelmének útjára áshat.

### <a name="consider-the-network-between-your-location-and-azure"></a>Fontolja meg a hely és az Azure közötti hálózatot

Akár éles, tesztelési és fejlesztési, akár biztonsági mentési célhelyként és helyreállítási helyként használ felhőerőforrásokat, fontos megértenie, hogy milyen sávszélességre van szüksége a biztonsági mentés kezdeti átviteléhez és a folyamatos napi átvitelhez.

Azure Data Box lehetővé teszi a kezdeti biztonsági mentési alapkonfiguráció átvitelét az Azure-ba anélkül, hogy nagyobb sávszélességre lenne szükség. Ez akkor hasznos, ha az alapkonfiguráció átvitele a elfogadhatónál hosszabb ideig tart. A tárfiók létrehozásakor az adatátviteli becslátor használatával megbecsülheti a kezdeti biztonsági mentés átviteléhez szükséges időt.

![Megjeleníti az Azure Storage adatátviteli becslátorát a portálon.](../media/az-storage-transfer.png)

Ne feledje, hogy elegendő hálózati kapacitásra van szüksége a napi adatátvitel támogatásához a szükséges átviteli ablakban (biztonsági mentési ablakban) anélkül, hogy ez hatással lenne az éles alkalmazásokra. Ez a szakasz a hálózati igények felméréséhez rendelkezésre álló eszközöket és technikákat ismerteti.

#### <a name="determine-how-much-bandwidth-youll-need"></a>A szükséges sávszélesség meghatározása

A szükséges sávszélesség meghatározásához használja a következő erőforrásokat:

- Jelentések a biztonsági mentési szoftverből.
- A Commvault szabványos [](https://documentation.commvault.com/commvault/v11_sp19/article?p=39699.htm) jelentéseket [](https://documentation.commvault.com/commvault/v11_sp19/article?p=39621.htm) biztosít a változási sebesség és a biztonságimásolat-készlet teljes méretének meghatározásához az Azure-ba történő kezdeti alapkonfiguráció-átvitelhez.
- Szoftvertől független értékelési és jelentéskészítési eszközök biztonsági mentése, például:
  - [MiTrend](https://mitrend.com/)
  - [Aptare](https://www.veritas.com/insights/aptare-it-analytics)
  - [Datavoss](https://www.datavoss.com/)

#### <a name="determine-unutilized-internet-bandwidth"></a>A nem használt internetes sávszélesség meghatározása

Fontos tudni, hogy általában mennyi kihasználatlan sávszélesség (vagy mellékhelyiség) áll rendelkezésre napi szinten. Ez segít felmérni, hogy megfelel-e a következő céloknak:

- kezdeti feltöltési idő, ha nem használ kapcsolat nélküli Azure Data Box-feltöltéshez
- napi biztonsági mentések készítése a korábban azonosított változási arány és a biztonsági mentési ablak alapján

Az alábbi módszerekkel azonosíthatja, hogy az Azure-ba való biztonsági másolatok szabadon használhatók-e a sávszélességgel.

- Ha Ön egy meglévő Azure ExpressRoute, tekintse meg a kapcsolatkapcsolat [használatát](../../../../../expressroute/expressroute-monitoring-metrics-alerts.md#circuits-metrics) a Azure Portal.
- Lépjen kapcsolatba az internetszolgáltatóval. Képesnek kell lennie olyan jelentések megosztására, amelyek a meglévő napi és havi kihasználtságot mutatják.
- Több eszköz is rendelkezésre áll, amelyek mérik a kihasználtságot a hálózati forgalom útválasztó/kapcsoló szintjén való monitorozásával. Ezek a következők:
  - [Solarwinds Bandwidth Analyzer Pack](https://www.solarwinds.com/network-bandwidth-analyzer-pack?CMP=ORG-BLG-DNS)
  - [Paessler PRTG](https://www.paessler.com/bandwidth_monitoring)
  - [Cisco Network Assistant](https://www.cisco.com/c/en/us/products/cloud-systems-management/network-assistant/index.html)
  - [WhatsUp Gold](https://www.whatsupgold.com/network-traffic-monitoring)

### <a name="choose-the-right-storage-options"></a>A megfelelő tárolási lehetőségek kiválasztása

Ha az Azure-t használja biztonsági mentési célként, az [Azure Blob Storage-et fogja használni.](../../../../blobs/storage-blobs-introduction.md) A Blob Storage a Microsoft objektumtárolási megoldása. A Blob Storage nagy mennyiségű strukturálatlan adat tárolására van optimalizálva, amelyek olyan adatok, amelyek semmilyen adatmodellnek vagy definíciónak nem felelnie. Emellett az Azure Storage tartós, magas rendelkezésre áll, biztonságos és skálázható. Kiválaszthatja a számítási feladathoz megfelelő tárolót, hogy megfelelő szintű rugalmasságot [biztosítson](../../../../common/storage-redundancy.md) a belső SZOLGÁLTATÁSSZINT-nek való megfelelőség érdekében. A Blob Storage használatonkénti fizetéses szolgáltatás. Havonta kell [fizetnie](../../../../blobs/storage-blob-storage-tiers.md#pricing-and-billing) a tárolt adatok mennyiségért, az adatokhoz való hozzáférésért, valamint a hűtési és archív tárolási szintek esetében a minimálisan szükséges adatmegőrzési időszakért. A biztonsági mentési adatokra vonatkozó rugalmassági és rétegezési lehetőségeket az alábbi táblázatok foglalják össze.

**A Blob Storage rugalmassági lehetőségei:**

|  |Helyileg redundáns  |Zónaredundáns  |Georedundáns  |Geo-zónaredundáns  |
|---------|---------|---------|---------|---------|
|**A másolatok hatályos számú példánya**     | 3         | 3         | 6         | 6 |
|**Rendelkezésre állási zónák létrehozása**     | 1         | 3         | 2         | 4 |
|**Régiók régióinak a régiói**     | 1         | 1         | 2         | 2 |
|**Manuális feladatátvétel másodlagos régióba**     | N.A.         | N.A.         | Igen         | Igen |

**Blob Storage-szintek:**

|  | Gyors rétegű   |A hűtési szint   | Archív szint |
| ----------- | ----------- | -----------  | -----------  |
| **Rendelkezésre állás** | 99.9%         | 99%         | Offline      |
| **Használati díjak** | Magasabb tárolási, alacsonyabb hozzáférési és tranzakciós költségek | Alacsonyabb tárolási, magasabb hozzáférési és tranzakciós költségek | A legalacsonyabb tárolási, legmagasabb hozzáférési és tranzakciós költségek |
| **Minimális adatmegőrzésre van szükség**| N/A | 30 nap | 180 nap |
| **Késés (az első bájthoz való idő)** | Ezredmásodperc | Ezredmásodperc | Óra |

#### <a name="sample-backup-to-azure-cost-model"></a>Azure-költségmodellbe való biztonsági mentés mintája

A használatalapú fizetés ijesztő lehet az olyan ügyfelek számára, akik még nem használják a felhőt. Bár csak a felhasznált kapacitásért fizet, a tranzakciókért (olvasási és [](https://azure.microsoft.com/pricing/details/bandwidth/) írási műveletekért) és a helyszíni környezetbe visszaolvasott adatokért is fizetnie kell, ha az [Azure Express Route](https://azure.microsoft.com/pricing/details/expressroute/) közvetlen helyi vagy Express Route korlátlan adatforgalmi díjcsomagját használja, ahol az Azure-ból származó adatforgalmat is tartalmazza. Az Azure [díjkalkulátor használatával](https://azure.microsoft.com/pricing/calculator/) "what if" elemzést végezhet. Az elemzést a lista díjszabása vagy a [fenntartott Azure Storage-kapacitás](../../../../../cost-management-billing/reservations/save-compute-costs-reservations.md)díjszabása alapján alapozhatja, amely akár 38%-os megtakarítást is biztosít. Az alábbi példa díjszabási gyakorlata az Azure-ba való biztonságimentés havi költségét modellezheti. Ez csak egy példa. *A díjszabás változhat az itt nem rögzített tevékenységek miatt.*

|Költségtényező  |Havi költség  |
|---------|---------|
|100 TB biztonsági mentési adat a hűtési adatok tárolásához     |1556,48 USD         |
|Naponta 2 TB új adat x 30 nap     |39 USD a tranzakciókban          |
|Havi becsült összeg     |1595,48 USD         |
|---------|---------|
|5 TB-os helyszíni helyreállítás egyszer nyilvános interneten keresztül   | 491,26 USD         |

> [!NOTE]
> Ez a becslés az Azure díjkalkulátorban, az USA keleti régiója használatalapú díjszabás alapján lett létrehozva, és a Commvault alapértelmezett, 32 MB-os résztömbméretén alapul, amely naponta 65 536 PUT kérést (írási tranzakciót) hoz létre. Előfordulhat, hogy ez a példa nem alkalmazható a követelményekre.

## <a name="implementation-guidance"></a>Implementációs útmutató

Ez a szakasz rövid útmutatót tartalmaz az Azure Storage helyszíni Commvault üzemelő példányhoz való hozzáadásához. Részletes útmutatásért és tervezési szempontokért tekintse meg a [Commvault](https://documentation.commvault.com/commvault/v11/others/pdf/public-cloud-architecture-guide-for-microsoft-azure11-19.pdf)nyilvános felhőarchitektúrák útmutatóját a Microsoft Azure.

1. Nyissa meg a Azure Portal, és keressen rá a **tárfiókok kifejezésre.** Az alapértelmezett Tárfiókok ikonra **is kattinthat.**

    ![Megjeleníti a tárfiókok hozzáadását a Azure Portal.](../media/azure-portal.png)
  
    ![Megjeleníti, hogy hová gépelte be a tárolót a Azure Portal.](../media/locate-storage-account.png)

2. Fiók **hozzáadásához** válassza a Létrehozás lehetőséget. Válasszon ki vagy hozzon létre egy erőforráscsoportot, adjon meg egy egyedi nevet, válassza ki a régiót, válassza a **Standard** teljesítmény lehetőséget, mindig hagyja meg a fiók altípusát **Storage V2** értéken, válassza ki a szolgáltatásiszint-nek megfelelő replikációs szintet, és a biztonsági mentési szoftver alapértelmezett szintjét. Az Azure Storage-fiókok a forró, a hűtési és az archív szinteket egyetlen fiókon belül teszik elérhetővé, a Commvault-szabályzatok pedig lehetővé teszik több szint használatát az adatok életciklusának hatékony kezeléséhez.

    ![Megjeleníti a tárfiók beállításait a portálon](../media/account-create-1.png)

3. Most tartsa meg az alapértelmezett hálózati beállításokat, és lépjen tovább az **Adatvédelem elemre.** Itt engedélyezheti a helyreállítható törlést, ami lehetővé teszi egy véletlenül törölt biztonságimásolat-fájl helyreállítását a megadott megőrzési időszakon belül, és védelmet nyújt a véletlen vagy rosszindulatú törlés ellen.

    ![Megjeleníti az Adatvédelmi beállításokat a portálon.](../media/account-create-2.png)

4. Ezután javasoljuk az Alapértelmezett beállításokat a Speciális **képernyőről** az Azure-beli használatra való biztonsági mentéshez.

    ![Megjeleníti a Speciális beállítások lapot a portálon.](../media/account-create-3.png)

5. Ha címkézést használ, adjon hozzá címkéket a szervezethez, és hozza létre a fiókját.

6. Most már csak két gyors lépésre van szükség ahhoz, hogy hozzáadja a fiókot a Commvault-környezethez. Lépjen a létrehozott fiókra a Azure Portal,  és válassza a Tárolók lehetőséget a **Blob service** menüben. Adjon hozzá egy tárolót, és válasszon egy kifejező nevet. Ezután lépjen a Hozzáférési kulcsok **elemre** a Beállítások **alatt,** és másolja ki a **Tárfiók** nevét és a két hozzáférési kulcs egyikét. A következő lépésekben szüksége lesz a tároló nevére, a fiók nevére és a hozzáférési kulcsra.

    ![Megjeleníti a tárolók létrehozását a portálon.](../media/container.png)

    ![Megjeleníti a hozzáférésikulcs-beállításokat a portálon.](../media/access-key.png)

7. (*Nem kötelező*) További biztonsági rétegeket adhat hozzá az üzemelő példányhoz.

    1. Szerepköralapú hozzáférés konfigurálása annak korlátozására, hogy ki módosíthat tárfiókot. További információ: Beépített szerepkörök felügyeleti [műveletekhez.](../../../../common/authorization-resource-provider.md#built-in-roles-for-management-operations)
    1. Korlátozza a fiókhoz való hozzáférést [](../../../../common/storage-network-security.md) adott hálózati szegmensekre a storage tűzfalbeállításokkal, hogy megakadályozza a vállalati hálózaton kívülről való hozzáférési kísérleteket.

        ![Megjeleníti a Storage-tűzfal beállításait a portálon.](../media/storage-firewall.png)

    1. Állítson [be törlési](../../../../../azure-resource-manager/management/lock-resources.md) zárolást a fiókon, hogy megakadályozza a tárfiók véletlen törlését.

        ![Megjeleníti a törlési zárolás beállítását a portálon.](../media/resource-lock.png)

    1. További ajánlott [biztonsági eljárások konfigurálása.](../../../../../storage/blobs/security-recommendations.md)

1. A Commvault parancssori központban lépjen a **Manage**  ->  **Security**  ->  **Hitelesítőadat-kezelő**. Válassza ki a **Cloud Account**(Felhőfiók) és a **Vendor type** of Microsoft Azure Storage **(Felhőfiók)** lehetőséget, majd válassza ki a **MediaAgent** adatokat az Azure-ba és onnan más szolgáltatásokba, majd adja hozzá a tárfiók nevét és hozzáférési kulcsát.

    ![Megjeleníti a hitelesítő adatok hozzáadását a Commvault parancssori központban.](../media/commvault-credential.png)

9. Ezután lépjen a Storage Cloud   ->  **(Tárolófelhő) lapra** a Commvault parancsközpontban. Válassza a **Hozzáadás lehetőséget.** Adjon egy rövid nevet a tárfióknak, majd Microsoft Azure Storage **a** **Típus listából.** Válassza ki a biztonsági másolatok Azure Storage-ba való átviteléhez használni kívánt Media Agent-kiszolgálót. Adja hozzá a létrehozott tárolót, válassza ki az Azure Storage-fiókban használni kívánt tárolási réteget, majd válassza ki a #8. Végül válassza ki, hogy a deduplikált biztonsági másolatokat át kell-e vagy sem, valamint a deduplikáció adatbázisának helyét.

     ![A Commvault Felhő hozzáadása felhasználói felületének képernyőképe. Az Archív legördülő menüben az **Archív** elem van kiválasztva.](../media/commvault-add-storage.png)

10. Végül adja hozzá az új Azure Storage-erőforrást egy meglévő vagy új csomaghoz a Commvault parancssori központban a Csomagok kezelése biztonsági mentési  ->   célhelyként való használatával.

    ![A Commvault parancssori felületének képernyőképe. A bal oldali navigációs sávon a **Kezelés** alatt a **Csomagok** elem van kiválasztva.](../media/commvault-plan.png)

11. *(Nem kötelező)* Ha az Azure-t helyreállítási helyként vagy a Commvaultot tervezi a kiszolgálók és alkalmazások Azure-ba történő áttelepítéséhez, ajánlott vsa-proxyt üzembe helyezni az Azure-ban. Részletes utasításokat itt [talál:](https://documentation.commvault.com/commvault/v11/article?p=106208.htm).

## <a name="operational-guidance"></a>Üzemeltetési útmutató

### <a name="azure-alerts-and-performance-monitoring"></a>Azure-riasztások és teljesítményfigyelés

Célszerű figyelni az Azure-erőforrásokat és a Commvault azon képességét, hogy úgy használja őket, mintha bármilyen tárolási célhelyet tárolna a biztonsági másolatok tárolásához. A környezet Azure Monitor És a Commvault Command Center figyelése segít a környezet megfelelő épentartásában.

#### <a name="azure-portal"></a>Azure Portal

Az Azure robusztus monitorozási megoldást kínál a [Azure Monitor.](../../../../../azure-monitor/essentials/monitor-azure-resource.md) Konfigurálhatja [a Azure Monitor](../../../../blobs/monitor-blob-storage.md) Azure Storage kapacitásának, tranzakcióinak, rendelkezésre állásának, hitelesítésének és egyéb műveleteknek a nyomon követésére. Az itt gyűjtött metrikák teljes hivatkozását itt [találja.](../../../../blobs/monitor-blob-storage-reference.md) Néhány hasznos mérőszám, amelyek nyomon követhetők a BlobCapacity (BlobCapacity) – annak érdekében, hogy a tárfiók maximális kapacitáskorlátja [(](../../../../common/scalability-targets-standard-account.md)bejövő és bejövő) alatt maradjon – az Azure Storage-fiókba és a MediaAgentből beírt és onnan beolvasott adatok mennyiségének nyomon követéséhez, valamint a SuccessE2ELatency.

[Naplóriasztásokat is](../../../../../service-health/alerts-activity-log-service-notifications-portal.md) létrehozhat az Azure Storage szolgáltatás állapotának nyomon követéséhez és az [Azure állapot-irányítópultjának](https://status.azure.com/status) megtekintéséhez.

#### <a name="commvault-command-center"></a>Commvault parancssori központ

- [Riasztás létrehozása felhőalapú tárolókészletek számára](https://documentation.commvault.com/commvault/v11/article?p=100514_3.htm)
- A teljesítményjelentések megtekintésével, a feladat befejezésével és az alapszintű hibaelhárítás megkezdésével kapcsolatos információkért lásd: [Irányítópultok.](https://documentation.commvault.com/commvault/v11/article?p=95306_1.htm)

### <a name="how-to-open-support-cases"></a>Támogatási esetek megnyitása

Ha segítségre van szüksége az Azure-megoldásba való biztonsági mentéssel kapcsolatban, nyisson meg egy esetet a Commvault és az Azure használatával is. Ez segít a támogatási szervezeteknek az együttműködésben, ha szükséges.

#### <a name="to-open-a-case-with-commvault"></a>Eset megnyitása a Commvaulttal

A [Commvault támogatási webhelyén](https://www.commvault.com/support)jelentkezzen be, és nyisson meg egy esetet.

Az Ön számára elérhető támogatási szerződési lehetőségekről a [Commvault támogatási lehetőségeiben található információ](https://ma.commvault.com/support)

Egy eset megnyitásához is behívhatja a címet, vagy e-mailben elérheti a Commvault ügyfélszolgálatát:

- Ingyenesen hívható: +1 877-780-3077
- [Globális támogatási számok](https://ma.commvault.com/Support/TelephoneSupport)
- [Commvault-támogatás e-mailben](mailto:support@commvault.com)

#### <a name="to-open-a-case-with-azure"></a>Eset megnyitása az Azure-ban

A [Azure Portal](https://portal.azure.com) keresse meg **a** támogatást a felső keresősávban. Válassza **a Súgó és támogatás** Új támogatási kérelem  ->  **lehetőséget.**

> [!NOTE]
> Amikor megnyit egy esetet, legyen konkrét, hogy segítségre van szüksége az Azure Storage-hoz vagy a Azure-hálózatkezelés. Ne adja meg a Azure Backup. Azure Backup egy Azure-szolgáltatás neve, és az eset helytelenül lesz irányítva.

### <a name="links-to-relevant-commvault-documentation"></a>A vonatkozó Commvault-dokumentációra mutató hivatkozások

További részletekért tekintse meg a következő Commvault-dokumentációt:

- [Commvault felhasználói útmutató](https://documentation.commvault.com/commvault/v11/article?p=37684_1.htm)
- [Commvault Azure Architecture Guide](https://documentation.commvault.com/commvault/v11/others/pdf/public-cloud-architecture-guide-for-microsoft-azure11-19.pdf)

### <a name="marketplace-offerings"></a>Marketplace-ajánlatok

A Commvault megkönnyíti a megoldás üzembe helyezését az Azure-ban az Azure Virtual Machines és számos más Azure-szolgáltatás védelme érdekében. További információkat az alábbi hivatkozásokon találhat:

- [A Commvault üzembe helyezése a Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/commvault.commvault?tab=Overview)
- [Commvault Azure-adatlaphoz](https://www.commvault.com/resources/microsoft-azure-cloud-platform-datasheet)
- [A Commvault támogatott Azure-szolgáltatások és -szolgáltatások listája](https://documentation.commvault.com/commvault/v11/article?p=109795_1.htm)
- [A Commvault használata a SAP HANA védelmére az Azure-ban](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/)

## <a name="next-steps"></a>Következő lépések

A speciális használati forgatókönyvekkel kapcsolatos információkért tekintse meg ezeket a további Commvault-forrásokat.

- [A Commvault használata a kiszolgálók és alkalmazások Azure-ba való áttelepítéséhez](https://www.commvault.com/resources/demonstration-vmware-to-azure-migrations-with-commvault)
- [Az SAP védelme az Azure-ban a Commvault használatával](https://www.youtube.com/watch?v=4ZGGE53mGVI)
- [Az Office365 védelme a Commvaulttal](https://www.youtube.com/watch?v=dl3nvAacxZU)