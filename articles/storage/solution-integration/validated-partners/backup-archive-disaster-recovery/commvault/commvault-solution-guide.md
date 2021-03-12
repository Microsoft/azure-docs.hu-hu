---
title: Biztonsági másolatok készítése az Azure-ba az CommVault-mel
titleSuffix: Azure Blob Storage Docs
description: A weblap áttekintést nyújt azokról a tényezőkről, amelyeket figyelembe kell venni, és követnie kell, hogy az Azure hogyan hasznosítható tárolási célként és helyreállítási helyként a CommVault teljes biztonsági mentéséhez és helyreállításához
keywords: CommVault, biztonsági mentés a felhőbe, biztonsági mentés, biztonsági mentés az Azure-ba, vész-helyreállítás, Üzletmenet-folytonosság
author: karauten
ms.author: karauten
ms.date: 11/11/2020
ms.topic: article
ms.service: storage
ms.subservice: blobs
ms.openlocfilehash: bc5bcca394fa66cea9cbf6bc20ac7d164c671cf7
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "102632951"
---
# <a name="back-up-to-azure-with-commvault"></a>Biztonsági mentés az Azure-ba a CommVault használatával

Ez a cikk útmutatást nyújt a CommVault-infrastruktúra Azure-Blob Storage való integrálásához. Előfeltételeket, Azure Storage-alapelveket, implementációt és üzemeltetési útmutatást tartalmaz. Ez a cikk csak az Azure-t használja telephelyen kívüli biztonsági mentési célként és helyreállítási helyként, ami megakadályozza az elsődleges helyen belüli normál működést. A CommVault egy alacsonyabb RTO-megoldást is kínál, amely a CommVault élő szinkronizálást is biztosítja, így a készenléti virtuális gépek a rendszerindításhoz és az Azure-beli éles környezetben található erőforrások védelméhez is gyorsabban elindíthatók. Ezek a képességek kívül esik a dokumentum hatókörén. 

## <a name="reference-architecture-for-on-premises-to-azure-and-in-azure-deployments"></a>A helyszíni és az Azure közötti, valamint In-Azure üzemelő példányokra vonatkozó hivatkozási architektúra

![CommVault az Azure-referenciák architektúrája](../media/commvault-diagram.png)

A meglévő CommVault üzembe helyezése egyszerűen integrálható az Azure-ba egy Azure Storage-fiók vagy több fiók felhőalapú tárolási célként való hozzáadásával. A CommVault lehetővé teszi, hogy az Azure-ban helyszíni biztonsági másolatokat is helyreállítson, így az Azure-ban egy helyreállítási igény szerinti webhely is elérhetővé válik.   

## <a name="commvault-interoperability-matrix"></a>CommVault együttműködési mátrix
| Számítási feladat | GPv2 és Blob Storage | A ritka elérésű szint támogatása | Archiválási szint támogatása | Data Box család támogatása |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| Helyszíni virtuális gépek/adatkészletek | v 11.5 | v 11.5 | v 11.10 | v 11.10 |
| Azure-beli virtuális gépek | v 11.5 | v 11.5 | v 11.5 | NA |
| Azure-blob | v 11.6 | v 11.6 | v 11.6 | NA |
| Azure Files | v 11.6 | v 11.6 | v 11.6 | NA | 

## <a name="before-you-begin"></a>Előkészületek

Egy kis előzetes tervezéssel az Azure-t a több, sok elégedett ügyfelünk soraihoz csatlakoztathatja a telephelyen kívüli biztonsági mentési cél és helyreállítási hely helyett.

### <a name="are-you-new-to-azure"></a>Új az Azure?

A Microsoft keretrendszert biztosít az Azure-ban való ismerkedéshez. A [Cloud bevezetési keretrendszer](https://docs.microsoft.com/azure/architecture/cloud-adoption/) \( CAF \) részletesen ismerteti a vállalati digitális átalakítást, és átfogó útmutatót biztosít a felhőbe való bevezetésének tervezéséhez. A CAF egy lépésenkénti [Azure-telepítési útmutatót](https://docs.microsoft.com/azure/cloud-adoption-framework/ready/azure-setup-guide/) tartalmaz az Azure-hoz, amely megkönnyíti a gyors és biztonságos működést, és az [Azure Portalon](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade)interaktív verziót is talál. Az alkalmazások és az ingyenes képzési erőforrások üzembe helyezéséhez példákat és ajánlott eljárásokat talál, amelyek az Azure-szakértelem elérési útjára kerülnek.

### <a name="consider-the-network-between-your-location-and-azure"></a>Vegye figyelembe a hálózat helyét és az Azure-t

A felhő erőforrásainak a termelési, tesztelési és fejlesztési, illetve biztonsági mentési cél és helyreállítási hely futtatásához való kihasználása fontos, hogy megértse a szükséges sávszélességet a kezdeti biztonsági mentéshez és a napi adatátvitelhez. 

Azure Data Box lehetővé teszi, hogy a kezdeti biztonsági mentési alapkonfigurációt az Azure-ba továbbítsa anélkül, hogy további sávszélességre lenne szükség, ha az alapértékek várhatóan hosszabbak lesznek, mint amennyit el tud viselni. A kezdeti biztonsági mentés átadásához szükséges időt a Storage-fiók létrehozásakor kihasználhatja a Adatátvitel kalkulátor használatával.

![Azure Storage Adatátvitel kalkulátor](../media/az-storage-transfer.png)

Ne feledje, hogy elegendő hálózati kapacitásra van szükség a napi adatátvitelek támogatásához a szükséges adatátviteli ablakban (biztonsági mentési ablak) az üzemi alkalmazások befolyásolása nélkül. Ez a szakasz a hálózati igények felméréséhez rendelkezésre álló eszközöket és technikákat vázolja fel.

#### <a name="how-can-you-determine-how-much-bandwidth-you-will-need"></a>Hogyan állapítható meg, hogy mekkora sávszélességre lesz szüksége?

-  A biztonsági mentési szoftver jelentései. 
  A CommVault standard jelentéseket biztosít a [változási arány](https://documentation.commvault.com/commvault/v11_sp19/article?p=39699.htm) és a [biztonságimásolat-készlet teljes méretének](https://documentation.commvault.com/commvault/v11_sp19/article?p=39621.htm) meghatározásához az Azure-ba történő kezdeti alapkonfigurációhoz.
- Backup szoftver – független értékelési és jelentéskészítési eszközök, például:
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
|Manuális feladatátvétel másodlagos régióba     | NA         | NA         | Igen         | Yes |

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
|2 TB új, naponta írt adat x 30 nap     |$39 a tranzakciókban          |
|Havi becsült összeg     |$1595,48         |
|---------|---------|
|5 TB-os egyszeri visszaállítás a helyszíni hálózaton keresztül   | $491,26         |


> [!Note] 
Ez a becslés az Azure-beli USA-beli utólagos elszámolású díjszabás alapján lett létrehozva, és a CommVault alapértelmezett 32MB, amely a 65 536 PUT-kérelmeket, valamint a napi írási tranzakciókat hozza létre. Ez a példa nem alkalmazható a követelményekre.

## <a name="implementation-and-operational-guidance"></a>Megvalósítási és üzemeltetési útmutató

Ez a szakasz rövid útmutatót nyújt az Azure Storage helyszíni CommVault-telepítéshez való hozzáadásához. Ha szeretne részletes útmutatást és tervezési szempontokat figyelembe venni, javasoljuk, hogy tekintse át a [CommVault Azure Architecture útmutatót](https://www.commvault.com/resources/public-cloud-architecture-guide-for-microsoft-azure-v11-sp16).

1. Nyissa meg a Azure Portal, és keressen rá a "Storage-fiókok" kifejezésre, vagy kattintson az alapértelmezett szolgáltatások ikonra.
    
    1. ![Azure Portal](../media/azure-portal.png)
  
    1. ![Storage-fiókok az Azure Portalon](../media/locate-storage-account.png)

2. Válassza a fiók hozzáadása lehetőséget, majd válasszon ki vagy hozzon létre egy erőforráscsoportot, adjon meg egy egyedi nevet, válassza ki a régiót, válassza a "normál" teljesítményt, mindig a "Storage v2" lehetőséget, válassza ki a replikációs szintet, amely megfelel a SLA-nak, és az alapértelmezett szintet, amelyet a biztonsági mentési szoftver fog használni. Az Azure Storage-fiók egy fiók-és CommVault-házirendekben elérhető, gyors, ritka elérésű és archív csomagokat tesz lehetővé, így több szintet is használhat az adatok életciklusának hatékony kezelésére. Folytassa a következő lépéssel. 

    ![Storage-fiók létrehozása](../media/account-create-1.png)

3. Tartsa meg az alapértelmezett hálózatkezelési lehetőségeket, és lépjen az "Adatvédelem" lehetőségre. Itt engedélyezheti a "Soft Delete" használatát, amely lehetővé teszi egy véletlenül törölt biztonságimásolat-fájl helyreállítását a megadott megőrzési időtartamon belül, és védelmet nyújt a véletlen vagy rosszindulatú törlés ellen. 
    
    ![Storage-fiók létrehozása 2. rész](../media/account-create-2.png)

4. A következő lépésben a "speciális" képernyő alapértelmezett beállításait ajánljuk az Azure-beli használatra való biztonsági mentéshez.

    ![Storage-fiók létrehozása – 3. rész](../media/account-create-3.png) 

5. Címkék hozzáadása a szervezethez, ha kihasználja a címkézést, és létrehozza a fiókját. A rendelkezésére áll az igény szerinti tárolás petabájt.

6. A fiók CommVault-környezetbe való felvétele előtt két gyors lépésre van szükség. Navigáljon a Azure Portalban létrehozott fiókhoz, és válassza a "tárolók" lehetőséget a portál panel "blob szolgáltatás" menüjében. Adjon hozzá egy új tárolót, és válasszon egy értelmes nevet. Ezután navigáljon a "beállítások" alatt a "hozzáférési kulcsok" elemre, és másolja a "Storage-fiók nevét" és a két hozzáférési kulcs egyikét. A következő lépésekben szüksége lesz a tároló nevére, a fiók nevére és a hozzáférési kulcsra.
    
    ![Tároló létrehozása](../media/container.png)
    
    ![A fiók adatainak megragadása](../media/access-key.png)

7. ***(Nem kötelező)*** További biztonsági rétegeket is hozzáadhat az üzembe helyezéshez.
    
    1. A szerepköralapú hozzáférés konfigurálásával korlátozhatja, hogy ki végezhet módosításokat a Storage-fiókban. [További információ](https://docs.microsoft.com/azure/storage/common/authorization-resource-provider?toc=/azure/storage/blobs/toc.json)
    1. A fiókhoz való hozzáférés korlátozása a [tárolási tűzfallal](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-portal) megadott hálózati szegmensekre, hogy megakadályozza a vállalati hálózaton kívülről érkező hozzáférési kísérletek elvégzését.

    ![Tárolási tűzfal](../media/storage-firewall.png) 

    1. A fiók [törlési zárolásának](https://docs.microsoft.com/azure/azure-resource-manager/management/lock-resources) beállításával megakadályozhatja a Storage-fiók véletlen törlését.

    ![Erőforrás-zárolás](../media/resource-lock.png)
    
    1. További [ajánlott biztonsági eljárások](https://docs.microsoft.com/azure/storage/blobs/security-recommendations)konfigurálása.
    
1. A CommVault-parancssorban navigáljon a "Manage" – > "Security"--> "Hitelesítőadat-kezelő" elemre. Válassza ki a "Cloud account" ("szállító típusa") Microsoft Azure Storage, válassza ki a "MediaAgent", amely az Azure-ba irányuló és onnan érkező adatok átvitelét adja meg, vegye fel a Storage-fiók nevét és a hozzáférési kulcsot.
    
    ![CommVault hitelesítő adatai](../media/commvault-credential.png)

9. Ezután navigáljon a "Storage" – > "Cloud" kifejezésre a CommVault-parancssorban. Válassza a Hozzáadás lehetőséget. Adjon egy rövid nevet a Storage-fióknak, majd válassza a "Microsoft Azure Storage" lehetőséget a "típus" listából. Válassza ki a biztonsági másolatok Azure Storage-ba történő átviteléhez használni kívánt Media Agent-kiszolgálót. Adja hozzá a létrehozott tárolót, válassza ki az Azure Storage-fiókban használni kívánt tárolási szintet, majd válassza ki a #8 lépésben létrehozott hitelesítő adatokat. Végezetül adja meg, hogy a deduplikált biztonsági mentéseket vagy sem, sem a deduplikált adatbázis helyét szeretné-e átvinni.
    
     ![Képernyőkép a Felhőbeli felhasználói felület hozzáadásáról. Az Archive (archiválás) legördülő menüben a * * Archive * * (Archívum * *) lehetőség van kiválasztva.](../media/commvault-add-storage.png)

10. Végül adja hozzá az új Azure Storage-erőforrást egy meglévő vagy új csomaghoz a CommVault-ben a "Manage"--> "Plans" (biztonsági mentés célhelye) használatával.

    ![Képernyőkép a COMMVAULT Command Center felhasználói felületéről. A bal oldali navigációs menüben válassza a * * felügyelet * * * * * csomagok * * lehetőséget.](../media/commvault-plan.png)

11. ***(Nem kötelező)*** Ha az Azure-t helyreállítási helyként vagy CommVault szeretné használni a kiszolgálók és alkalmazások Azure-ba való áttelepítéséhez, ajánlott a VSA-proxy üzembe helyezése az Azure-ban. Részletes útmutatást [itt](https://documentation.commvault.com/commvault/v11/article?p=106208.htm)talál.  

### <a name="azure-alerting-and-performance-monitoring"></a>Azure-riasztások és teljesítmény-figyelés

Javasoljuk, hogy az Azure-erőforrások és a CommVault képes legyen a biztonsági másolatok tárolásához használni kívánt tárolási célra használni. A Azure Monitor és a CommVault Command Center monitorozásának kombinációja segít a környezet kifogástalan állapotának megőrzésében.

#### <a name="microsoft-azure-portal"></a>Microsoft Azure Portal

Microsoft Azure egy robusztus figyelési megoldást biztosít [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource)formájában. A [Azure monitor konfigurálhatja](https://docs.microsoft.com/azure/storage/common/monitor-storage?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=azure-powershell#configuration) az Azure Storage-kapacitás, a tranzakciók, a rendelkezésre állás, a hitelesítés és egyéb műveletek nyomon követésére. A nyomon követett mérőszámok teljes referenciája [itt](https://docs.microsoft.com/azure/storage/common/monitor-storage-reference)található. Néhány hasznos mérőszám a BlobCapacity – annak érdekében, hogy továbbra is a Storage-fiók maximális [kapacitása](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account), a bejövő forgalom és a kimenő adatok mennyisége alatt maradjon, nyomon követheti az Azure Storage-fiókba írt és beolvasott adatok mennyiségét, és SuccessE2ELatency az Azure Storage-ba irányuló és a MediaAgent érkező kérések időpontjának nyomon követésére. 

[Létrehozhat napló-riasztásokat](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications) az Azure Storage szolgáltatás állapotának nyomon követéséhez, és bármikor megtekintheti az [Azure status irányítópultját](https://status.azure.com/status) .

#### <a name="commvault-command-center"></a>CommVault-központ

[Felhőalapú tárolóeszközökhöz tartozó riasztások létrehozása](https://documentation.commvault.com/commvault/v11/article?p=100514_3.htm)

[Hol tekinthetik meg az ügyfelek a teljesítményadatokat, a feladat befejezését és az alapszintű hibaelhárítást](https://documentation.commvault.com/commvault/v11/article?p=95306_1.htm) 

### <a name="how-to-open-support-cases"></a>Támogatási esetek megnyitása

Ha segítségre van szüksége a biztonsági mentésről az Azure-megoldáshoz, javasoljuk, hogy nyisson meg egy esetet a CommVault és az Azure-ban is, hogy a támogatási szervezetek szükség esetén is közösen vehessenek igénybe.

#### <a name="how-to-open-a-case-with-commvault"></a>Eset megnyitása a CommVault

Navigáljon a [CommVault támogatási webhelyéhez](https://www.commvault.com/support), jelentkezzen be, és nyisson meg egy esetet.

Ha tisztában kell lennie az Ön számára elérhető támogatási szerződési lehetőségekkel, tekintse meg a [CommVault támogatási lehetőségeit](https://ma.commvault.com/support) .

Az is előfordulhat, hogy meghívja a-t egy eset megnyitására, vagy a CommVault-támogatás elérését e-mailben:

Díjmentes: + 1 877-780-3077

[Világszerte támogatott számok](https://ma.commvault.com/Support/TelephoneSupport)

[E-mail CommVault-támogatás](mailto:support@commvault.com)

#### <a name="how-to-open-a-case-with-the-azure-support-team"></a>Eset megnyitása az Azure támogatási csapatával

[Azure Portal](https://portal.azure.com) a portál felső részén található keresési sávban a "támogatás" kifejezésre, majd válassza az "+ új támogatási kérelem" lehetőséget. 
> [!Note]
Egy eset megnyitásakor egyedinek kell lennie, ha segítségre van szüksége az "Azure Storage" vagy az "Azure Networking" szolgáltatással, és **nem** "Azure Backup." A Azure Backup Microsoft Azure natív szolgáltatás, és az eset nem megfelelően lesz átirányítva.

### <a name="links-to-relevant-commvault-documentation"></a>A kapcsolódó CommVault-dokumentációra mutató hivatkozások

A CommVault dokumentációja további részleteket biztosít:

[CommVault felhasználói útmutató](https://documentation.commvault.com/commvault/v11/article?p=37684_1.htm)

[CommVault Azure-architektúra – útmutató](https://www.commvault.com/resources/public-cloud-architecture-guide-for-microsoft-azure-v11-sp16) 

### <a name="link-to-marketplace-offering"></a>A Marketplace-ajánlatra mutató hivatkozás

Továbbra is használhatja a CommVault-megoldást, amely ismeri és megbízik az Azure-on futó munkaterhelések elleni védelemben. A CommVault megkönnyíti a megoldás üzembe helyezését az Azure-ban, és az Azure-Virtual Machines és számos más Azure-szolgáltatást is védetté tenni.

[A CommVault üzembe helyezése az Azure Marketplace-en keresztül](https://azuremarketplace.microsoft.com/marketplace/apps/commvault.commvault?tab=Overview)

[Azure-Adatlap](https://www.commvault.com/resources/microsoft-azure-cloud-platform-datasheet)

[A támogatott Azure-funkciók és-szolgáltatások átfogó listája](https://documentation.commvault.com/commvault/v11/article?p=109795_1.htm)

[A CommVault használata az Azure-beli SAP HANAek elleni védelemhez](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/)

## <a name="next-steps"></a>Következő lépések

A külső webhelyeken további forrásokat is megtudhat a speciális használati forgatókönyvekkel kapcsolatos információk megismeréséhez:

[Kiszolgálók és alkalmazások áttelepíthetők az Azure-ba a CommVault használatával](https://www.commvault.com/resources/demonstration-vmware-to-azure-migrations-with-commvault)

[Az SAP biztosítása az Azure-ban az CommVault-mel](https://www.youtube.com/watch?v=4ZGGE53mGVI)

[A Office 365 és a CommVault elleni védelem](https://www.youtube.com/watch?v=dl3nvAacxZU)
