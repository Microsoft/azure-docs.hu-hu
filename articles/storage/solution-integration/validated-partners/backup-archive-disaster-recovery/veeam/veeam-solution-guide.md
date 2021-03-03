---
title: Biztonsági másolatok készítése az Azure-ba az Veeam-mel
titleSuffix: Azure Blob Storage Docs
description: A weblap áttekintést nyújt azokról a tényezőkről, amelyeket figyelembe kell venni, és követnie kell, hogy az Azure hogyan hasznosítható tárolási célként és helyreállítási helyként a Veeam biztonsági mentéshez és helyreállításhoz
keywords: Veeam,, biztonsági mentés a felhőbe, biztonsági mentés, biztonsági mentés az Azure-ba, vész-helyreállítás, Üzletmenet-folytonosság
author: Karl Rautenstrauch
ms.author: karauten
ms.date: 11/11/2020
ms.topic: article
ms.service: Storage
ms.subservice: Blob Storage
ms.openlocfilehash: 9f07703d23c3be6e842a54ba4bb0d46467ccb71d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745952"
---
# <a name="backup-to-azure-with-veeam"></a>Biztonsági mentés az Azure-ba a Veeam

Ez a cikk útmutatást nyújt a Veeam-infrastruktúra Azure-Blob Storage való integrálásához. Előfeltételeket, Azure Storage-alapelveket, implementációt és üzemeltetési útmutatást tartalmaz. Ez a cikk csak az Azure-t használja telephelyen kívüli biztonsági mentési célként és helyreállítási helyként, ami megakadályozza az elsődleges helyen belüli normál működést. A Veeam egy alacsonyabb RTO megoldást is kínál, amely Veeam-replikációt biztosít, így a készenléti virtuális gépek a rendszerindításhoz és az Azure-beli éles környezetben található erőforrások védelméhez is gyorsabban elindíthatók és helyreállíthatók. A Veeam az Azure és az Office 365-erőforrások biztonsági mentéséhez is eszközöket nyújt. Ezek a képességek kívül esik a dokumentum hatókörén. 

## <a name="reference-architecture-for-on-premises-to-azure-and-in-azure-deployments"></a>A helyszíni és az Azure közötti, valamint In-Azure üzemelő példányokra vonatkozó hivatkozási architektúra

![Veeam az Azure-referenciák architektúrája](../media/veeam-architecture.png)

A meglévő Veeam üzembe helyezése egyszerűen integrálható az Azure-ba egy Azure Storage-fiók vagy több fiók hozzáadásával a Felhőbeli biztonsági mentési tárházként. A Veeam lehetővé teszi, hogy az Azure-ban helyszíni biztonsági másolatokat is helyreállítson, így az Azure-ban egy helyreállítási igény szerinti webhely is elérhetővé válik.

## <a name="veeam-interoperability-matrix"></a>Veeam együttműködési mátrix
| Számítási feladat | GPv2 és Blob Storage | A ritka elérésű szint támogatása | Archiválási szint támogatása | Data Box család támogatása |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| Helyszíni virtuális gépek/adatkészletek | v10a | v10a | NA | 10a |
| Azure-beli virtuális gépek | v10a | v10a | NA | 10a |
| Azure-blob | v10a | v10a | NA | 10a |
| Azure Files | v10a | v10a | NA | 10a | 

> [!Note]
A Veeam biztonsági mentése és replikálása csak Azure Data Box REST API támogatja, ezért Azure Data Box Disk nem támogatott. Az Azure Blob Storage archiválási szintének támogatása várható a Veeam v11.

## <a name="before-you-begin"></a>Előkészületek

Egy kis előzetes tervezéssel az Azure-t a több, sok elégedett ügyfelünk soraihoz csatlakoztathatja a telephelyen kívüli biztonsági mentési cél és helyreállítási hely helyett.

### <a name="are-you-new-to-azure"></a>Új az Azure?

A Microsoft keretrendszert biztosít az Azure-ban való ismerkedéshez. A [Cloud bevezetési keretrendszer](https://docs.microsoft.com/azure/architecture/cloud-adoption/) \( CAF \) részletesen ismerteti a vállalati digitális átalakítást, és átfogó útmutatót biztosít a felhőbe való bevezetésének tervezéséhez. A CAF egy lépésenkénti [Azure telepítési útmutatót](https://docs.microsoft.com/azure/cloud-adoption-framework/ready/azure-setup-guide/) tartalmaz az Azure-hoz, amely segít a gyors és biztonságos használatban, és a [Azure Portal](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade)interaktív verzióját is megtalálhatja. Az alkalmazások és az ingyenes képzési erőforrások üzembe helyezéséhez példákat és ajánlott eljárásokat talál, amelyek az Azure-szakértelem elérési útjára kerülnek.

### <a name="consider-the-network-between-your-location-and-azure"></a>Vegye figyelembe a hálózat helyét és az Azure-t

A felhő erőforrásainak a termelési, tesztelési és fejlesztési, illetve biztonsági mentési cél és helyreállítási hely futtatásához való kihasználása fontos, hogy megértse a szükséges sávszélességet a kezdeti biztonsági mentéshez és a napi adatátvitelhez.

Azure Data Box lehetővé teszi, hogy a kezdeti biztonsági mentési alapkonfigurációt az Azure-ba továbbítsa anélkül, hogy további sávszélességre lenne szükség, ha az alapértékek várhatóan hosszabbak lesznek, mint amennyit el tud viselni. A kezdeti biztonsági mentés átadásához szükséges időt a Storage-fiók létrehozásakor kihasználhatja a Adatátvitel kalkulátor használatával.

![Azure Storage Adatátvitel kalkulátor](../media/az-storage-transfer.png)

Ne feledje, hogy elegendő hálózati kapacitásra van szükség a napi adatátvitelek támogatásához a szükséges adatátviteli ablakban (biztonsági mentési ablak) az üzemi alkalmazások befolyásolása nélkül. Ez a szakasz a hálózati igények felméréséhez rendelkezésre álló eszközöket és technikákat vázolja fel.

#### <a name="how-can-you-determine-how-much-bandwidth-you-will-need"></a>Hogyan állapítható meg, hogy mekkora sávszélességre lesz szüksége?

Több értékelési lehetőség áll rendelkezésre a változási arány és a biztonságimásolat-készlet teljes méretének meghatározásához az Azure-ba történő kezdeti alapkonfigurációhoz. Íme néhány példa az értékelési és jelentéskészítési eszközökre, például a következőkre:
  - [MiTrend](https://mitrend.com/)
  - [Aptare](https://www.veritas.com/insights/aptare-it-analytics)
  - [Datavoss](https://www.datavoss.com/)

#### <a name="how-will-i-know-how-much-headroom-i-have-with-my-current-internet-connection"></a>Honnan tudhatom, hogy mekkora belmagassággal rendelkezem a jelenlegi internetkapcsolatom?

Fontos tudni, hogy mekkora belmagasságú vagy jellemzően nem használt sávszélesség érhető el a napi szinten. Ez lehetővé teszi a megfelelő értékelést, ha a célokat a feltöltés kezdeti ideje szerint teljesíti, ha nem használja a Azure Data Boxt az offline előkészítéshez, illetve a biztonsági mentést a fentiekben megadott változási arány alapján a napi biztonsági másolatok elvégzéséhez. Az alábbi módszerek segítségével azonosíthatja a sávszélességet az Azure-ba irányuló biztonsági másolatok számára.

- Ön egy meglévő Azure ExpressRoute-ügyfél? Megtekintheti az [áramköri használatot](https://docs.microsoft.com/azure/expressroute/expressroute-monitoring-metrics-alerts#circuits-metrics) a Azure Portalban.
- Kapcsolatba léphet az INTERNETSZOLGÁLTATÓval. A felhasználók a meglévő napi és havi kihasználtságot bemutató jelentésekkel oszthatnak meg.
- Több eszköz is mérhető a használathoz, ha figyeli a hálózati forgalmat az útválasztó/váltás szintjén, beleértve a következőket:
  - [SolarWinds sávszélesség-elemző csomag](https://www.solarwinds.com/network-bandwidth-analyzer-pack?CMP=ORG-BLG-DNS)
  - [Paessler PRTG](https://www.paessler.com/bandwidth_monitoring)
  - [Cisco hálózati segéd](https://www.cisco.com/c/en/us/products/cloud-systems-management/network-assistant/index.html)
  - [WhatsUp arany](https://www.whatsupgold.com/network-traffic-monitoring)

### <a name="choosing-the-right-storage-options"></a>A megfelelő tárolási beállítások kiválasztása

Az Azure biztonsági mentési célként való használatakor az ügyfelek az [azure blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)\. Az Azure Blob Storage a Microsoft objektum-tárolási megoldása. A blob Storage nagy mennyiségű strukturálatlan adat tárolására van optimalizálva, amely olyan adatok, amelyek nem felelnek meg semmilyen adatmodellnek vagy definíciónak. Emellett az Azure Storage tartós, magasan elérhető, biztonságos és skálázható. A Microsoft platformja rugalmasságot biztosít a megfelelő tárterület kiválasztásához a megfelelő számítási feladatokhoz, így biztosítva a [rugalmasság szintjét](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=/azure/storage/blobs/toc.json) a belső SLA-k kielégítése érdekében. A Blob Storage díjköteles szolgáltatás. [Havi díjat számítunk](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers?tabs=azure-portal#pricing-and-billing) fel a tárolt adatok mennyiségére, az adatok elérésére, és – a lassú és az archív rétegek esetében – a minimálisan szükséges megőrzési időtartamot. A biztonsági mentési adatra vonatkozó rugalmassági és a rétegek közötti beállításokat az alábbi táblázat foglalja össze.

**Az Azure Blob Storage rugalmassági lehetőségei:**

|  |Helyileg redundáns  |Felesleges zóna  |Földrajzilag redundáns  |Geo-zóna redundáns  |
|---------|---------|---------|---------|---------|
|A másolatok tényleges száma     | 3         | 3         | 6         | 6 |
|Availability Zones száma     | 1         | 3         | 2         | 4 |
|Régiók száma     | 1         | 1         | 2         | 2 |
|Manuális feladatátvétel másodlagos régióba     | NA         | NA         | Igen         | Igen |

**Az Azure Blob Storage szintjei:**

|  | Gyors elérési szint   |Hűvös szint   | Archiválási szint |
| ----------- | ----------- | -----------  | -----------  |
| Rendelkezésre állás | 99.9%         | 99%         | Offline      |
| Használati díjak | Magasabb tárolási költségek, alacsonyabb hozzáférés és tranzakciós költségek | Alacsonyabb tárolási költségek, magasabb hozzáférési és tranzakciós költségek | Legalacsonyabb tárolási költségek, legmagasabb hozzáférési és tranzakciós költségek |
| Minimális adatmegőrzés szükséges | NA | 30 nap | 180 nap |
| Késés (az első bájtig eltelt idő) | Ezredmásodpercben | Ezredmásodpercben | Óra |

#### <a name="sample-backup-to-azure-cost-model"></a>Minta biztonsági mentés az Azure Cost modelre

A használaton kívüli fizetési koncepció a nyilvános felhőben új ügyfelek számára is ijesztő lehet. Míg csak a felhasznált kapacitásért kell fizetnie, a tranzakciókat (olvasási és írási) és a [kimenő adatforgalomért](https://azure.microsoft.com/pricing/details/bandwidth/) is fizetnie kell a helyszíni környezetre, amikor az [Azure Express Route Direct helyi vagy Express Route Unlimited adatforgalmi](https://azure.microsoft.com/pricing/details/expressroute/) csomagja használatban van, ahol az Azure-ból kimenő adatforgalom szerepel. Elvégezheti, hogy mi történik, ha az elemzés a lista díjszabása alapján vagy az [Azure Storage szolgáltatás számára fenntartott kapacitás díjszabásával](https://docs.microsoft.com/azure/cost-management-billing/reservations/save-compute-costs-reservations)érhető el, amely akár 38%-os megtakarítást is biztosíthat az [Azure díjszabási számológépében](https://azure.microsoft.com/pricing/calculator/) Íme egy példa a díjszabásra az Azure-ba történő biztonsági mentés havi költségének modellezéséhez, ez azonban csak egy példa, és ***a díjszabás az itt nem rögzített tevékenységek miatt változhat:***


|Költségek tényezője  |Havi költség  |
|---------|---------|
|100 TB biztonsági mentési adatok a ritkán használt tárolóban     |$1556,48         |
|2 TB új, naponta írt adat x 30 nap     |$72 a tranzakciókban          |
|Havi becsült összeg     |$1628,48         |
|---------|---------|
|5 TB-os egyszeri visszaállítás a helyszíni hálózaton keresztül   | $527,26         |

> [!Note]
Ez a becslés az Azure-beli USA-beli utólagos elszámolású díjszabás alapján lett létrehozva az Azure díjszabási Kalkulátorban, amely a WAN-átvitelek 256 kb Veeam alapértelmezett méretén alapul. Ez a példa nem alkalmazható a követelményekre.

## <a name="implementation-and-operational-guidance"></a>Megvalósítási és üzemeltetési útmutató

Ez a szakasz rövid útmutatót nyújt az Azure Storage helyszíni Veeam-telepítéshez való hozzáadásához. Ha érdeklik a részletes útmutatások és tervezési szempontok, javasoljuk, hogy tekintse át a [Veeam Cloud csatlakoztatás biztonsági mentési útmutatóját](https://helpcenter.veeam.com/docs/backup/cloud/cloud_backup.html?ver=100).

1. Nyissa meg a Azure Portal, és keressen rá a "Storage-fiókok" kifejezésre, vagy kattintson az alapértelmezett szolgáltatások ikonra.

      ![Azure Portal](../media/azure-portal.png)

      ![Storage-fiókok az Azure Portalon](../media/locate-storage-account.png)

2. Válassza a fiók hozzáadása lehetőséget, majd válasszon ki vagy hozzon létre egy erőforráscsoportot, adjon meg egy egyedi nevet, válassza ki a régiót, válassza a "normál" teljesítményt, mindig a "Storage v2" lehetőséget, válassza ki a replikációs szintet, amely megfelel a SLA-nak, és az alapértelmezett szintet, amelyet a biztonsági mentési szoftver fog használni. Az Azure Storage-fiók egy fiók-és Veeam-házirendekben elérhető, gyors, ritka elérésű és archív csomagokat tesz lehetővé, így több szintet is használhat az adatok életciklusának hatékony kezelésére. Folytassa a következő lépéssel. 
    
      ![Storage-fiók létrehozása](../media/account-create-1.png)

3. Tartsa meg az alapértelmezett hálózatkezelési lehetőségeket, és lépjen az "Adatvédelem" lehetőségre. Itt engedélyezheti a "Soft Delete" használatát, amely lehetővé teszi egy véletlenül törölt biztonságimásolat-fájl helyreállítását a megadott megőrzési időtartamon belül, és védelmet nyújt a véletlen vagy rosszindulatú törlés ellen. 
    ![Storage-fiók létrehozása 2. rész](../media/account-create-2.png)

4. A következő lépésben a "speciális" képernyő alapértelmezett beállításait ajánljuk az Azure-beli használatra való biztonsági mentéshez.

    ![Storage-fiók létrehozása – 3. rész](../media/account-create-3.png) 

5. Címkék hozzáadása a szervezethez, ha kihasználja a címkézést, és létrehozza a fiókját. A rendelkezésére áll az igény szerinti tárolás petabájt.

6. A fiók Veeam-környezetbe való felvétele előtt két gyors lépésre van szükség. Navigáljon az Azure Portalon létrehozott fiókhoz, és válassza a "tárolók" lehetőséget a portál paneljének "blob szolgáltatás" menüjében. Adjon hozzá egy új tárolót, és válasszon egy értelmes nevet. Ezután navigáljon a "beállítások" alatt a "hozzáférési kulcsok" elemre, és másolja a "Storage-fiók nevét" és a két hozzáférési kulcs egyikét. A következő lépésekben szüksége lesz a tároló nevére, a fiók nevére és a hozzáférési kulcsra.

    ![Tároló létrehozása](../media/container-b.png)
    
    ![A fiók adatainak megragadása](../media/access-key.png)
    
    > [!Note]
A Veeam biztonsági mentése és replikálása további lehetőségeket kínál az Azure-hoz való kapcsolódásra. Ennek a cikknek a használata esetén a Microsoft Azure Blob Storage biztonsági mentési célként való kihasználása a fenti módszer használata ajánlott eljárás.

7. ***(Nem kötelező)*** További biztonsági rétegeket is hozzáadhat az üzembe helyezéshez.

     1. A szerepköralapú hozzáférés konfigurálásával korlátozhatja, hogy ki végezhet módosításokat a Storage-fiókban. [További információ](https://docs.microsoft.com/azure/storage/common/authorization-resource-provider?toc=/azure/storage/blobs/toc.json)

    1. A fiókhoz való hozzáférés korlátozása a [tárolási tűzfallal](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal) megadott hálózati szegmensekre, hogy megakadályozza a vállalati hálózaton kívülről érkező hozzáférési kísérletek elvégzését.

    ![Tárolási tűzfal](../media/storage-firewall.png) 

    1. A fiók [törlési zárolásának](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) beállításával megakadályozhatja a Storage-fiók véletlen törlését.

    ![Erőforrás ](../media/resource-lock.png) -zárolás 1.) További [ajánlott biztonsági eljárások](https://docs.microsoft.com/azure/storage/blobs/security-recommendations)konfigurálása. 
8. A Veaam biztonsági mentési és replikációs konzolján navigáljon a biztonsági mentési infrastruktúra elemre – > kattintson a jobb gombbal az Áttekintés panelre, és válassza a "Backup-tárház hozzáadása" lehetőséget a konfigurációs varázsló megnyitásához. A párbeszédpanelen válassza az objektum Storage--> Microsoft Azure Blob Storage--> Azure Blob Storage elemet.
    
    ![Veeam-tárház varázsló – képernyő](../media/veeam-repo-a.png)

    ![Veeam-tárház varázsló – b képernyő](../media/veeam-repo-b.png)

    ![Veeam-tárház varázsló – c képernyő](../media/veeam-repo-c.png)

9. Ezután adja meg az új Microsoft Azure Blob-tárház nevét és leírását.
    
    ![Veeam-adattár varázsló képernyő d](../media/veeam-repo-d.png)

10. A következő lépésben adja hozzá a hitelesítő adatokat az Azure Storage-fiók eléréséhez. Válassza a "Microsoft Azure Storage fiók" lehetőséget a Felhőbeli hitelesítőadat-kezelőben, és adja meg a Storage-fiók nevét és a hozzáférési kulcsot. Ha alkalmazható, válassza az Azure Global elemet a régió-választóban és az átjáró-kiszolgálón.
    
    ![Veeam adattár varázsló képernyő e](../media/veeam-repo-e.png)

> [!Note]
Ha úgy dönt, hogy nem használ Veeam-átjáró kiszolgálót, győződjön meg arról, hogy a kibővíthető Tárházak mindegyike rendelkezik közvetlen internet-hozzáféréssel.

11. A tároló regisztrálása lapon válassza ki az Azure Storage-tárolót, és válasszon ki vagy hozzon létre egy mappát a biztonsági mentések tárolásához. Meghatározhatja a Veeam által használandó teljes tárolókapacitást is (ajánlott). Tekintse át a megjelenített információkat az összefoglalás szakaszban, és fejezze be a konfigurációs eszközt. Ezután az új tárház kiválasztható a biztonsági mentési feladatok konfigurációjában.

    ![Veeam-adattár varázsló képernyő f](../media/veeam-repo-f.png)
    
    ![Veeam-tárház varázsló – g képernyő](../media/veeam-repo-g.png)

### <a name="azure-alerting-and-performance-monitoring"></a>Azure-riasztások és teljesítmény-figyelés

Javasoljuk, hogy az Azure-erőforrások és a Veeam képes legyen a biztonsági másolatok tárolásához használni kívánt tárolási célra használni. A Azure Monitor és a Veeam figyelési képességeinek kombinációja (például a Veeam felügyeleti konzol feladatok csomópontjának statisztika lapja, vagy az olyan speciális beállítások, mint például a Veeam-kezelő) segít a környezet kifogástalan állapotának megőrzésében.

#### <a name="microsoft-azure-portal"></a>Microsoft Azure Portal

Microsoft Azure egy robusztus figyelési megoldást biztosít [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)formájában. A [Azure monitor konfigurálhatja](https://docs.microsoft.com/azure/storage/common/monitor-storage?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-powershell#configuration) az Azure Storage-kapacitás, a tranzakciók, a rendelkezésre állás, a hitelesítés és egyéb műveletek nyomon követésére. A nyomon követett mérőszámok teljes referenciája [itt](https://docs.microsoft.com/azure/storage/common/monitor-storage-reference)található. Néhány hasznos mérőszám a BlobCapacity – annak érdekében, hogy továbbra is a Storage-fiók maximális [kapacitása](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account), a bejövő forgalom és a kimenő adatok mennyisége alatt maradjon, nyomon követheti az Azure Storage-fiókba írt és beolvasott adatok mennyiségét, és SuccessE2ELatency az Azure Storage-ba irányuló és a MediaAgent érkező kérések időpontjának nyomon követésére. 

[Létrehozhat napló-riasztásokat](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications) az Azure Storage szolgáltatás állapotának nyomon követéséhez, és bármikor megtekintheti az [Azure status irányítópultját](https://status.azure.com/status) .

#### <a name="veeam-reporting"></a>Veeam-jelentéskészítés

[A Veeam konfigurálása egy jelentéskészítéshez](https://helpcenter.veeam.com/docs/one/reporter/configure_reporter.html?ver=100)

[Veeam biztonsági mentési és replikálási riasztások](https://helpcenter.veeam.com/docs/one/monitor/backup_alarms.html?ver=100) 

### <a name="how-to-open-support-cases"></a>Támogatási esetek megnyitása

Ha segítségre van szüksége a biztonsági mentésről az Azure-megoldáshoz, javasoljuk, hogy nyisson meg egy esetet a Veeam és az Azure-ban is, hogy a támogatási szervezetek szükség esetén is közösen vehessenek igénybe.

#### <a name="how-to-open-a-case-with-veeam"></a>Eset megnyitása a Veeam

Navigáljon a [Veeam ügyfélszolgálati portálra](https://www.veeam.com/support.html), jelentkezzen be, és nyisson meg egy esetet.

Ha meg kell ismernie a Veeam által elérhető támogatási lehetőségeket, tekintse meg a [Veeam-támogatási szabályzatot](https://www.veeam.com/veeam_software_support_policy_ds.pdf) .

A következő esetekben is meghívhat egy esetet:

[Világszerte támogatott számok](https://www.veeam.com/contacts.html?ad=in-text-link#support-numbers)

#### <a name="how-to-open-a-case-with-the-azure-support-team"></a>Eset megnyitása az Azure támogatási csapatával

[Azure Portal](https://portal.azure.com) a portál felső részén található keresési sávban a "támogatás" kifejezésre, majd válassza az "+ új támogatási kérelem" lehetőséget. 
> [!Note]
Egy eset megnyitásakor konkrétan az "Azure Storage" vagy az "Azure Networking" és **nem** "Azure Backup" használata esetén segítségre van szüksége. A Azure Backup Microsoft Azure natív szolgáltatás, és az eset nem megfelelően lesz átirányítva.

### <a name="links-to-relevant-veeam-documentation"></a>A kapcsolódó Veeam-dokumentációra mutató hivatkozások

A Veeam dokumentációja további részleteket biztosít:

[Veeam felhasználói útmutató](https://helpcenter.veeam.com/docs/backup/hyperv/overview.html?ver=100)

[Veeam-architektúra – útmutató](https://helpcenter.veeam.com/docs/backup/vsphere/backup_architecture.html?ver=100)

### <a name="link-to-marketplace-offering"></a>A Marketplace-ajánlatra mutató hivatkozás

Továbbra is használhatja a Veeam-megoldást, amely ismeri és megbízik az Azure-on futó munkaterhelések elleni védelemben. A Veeam megkönnyíti a megoldás üzembe helyezését az Azure-ban, és az Azure-Virtual Machines és számos más Azure-szolgáltatást is védetté tenni.

[A Veeam B&R üzembe helyezése az Azure Marketplace-en keresztül](https://azuremarketplace.microsoft.com/marketplace/apps/veeam.veeam-backup-replication?tab=overview)

[Azure-Adatlap](https://www.veeam.com/backup-azure.html?ad=menu-products)


## <a name="next-steps"></a>Következő lépések

A külső webhelyeken további forrásokat is megtudhat a speciális használati forgatókönyvekkel kapcsolatos információk megismeréséhez:

[A videók Veeam](https://www.veeam.com/how-to-videos.html?ad=menu-resources)

[Veeam műszaki dokumentációk](https://www.veeam.com/documentation-guides-datasheets.html?ad=menu-resources)

[Veeam Tudásbázis és gyakori kérdések](https://www.veeam.com/knowledge-base.html?ad=menu-resources)
