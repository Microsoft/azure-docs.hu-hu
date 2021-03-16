---
title: Biztonsági másolatok készítése az Azure-ba az Veeam-mel
titleSuffix: Azure Storage
description: Áttekintést nyújt azokról a tényezőkről, amelyeket figyelembe kell venni, és követni kell, hogy az Azure hogyan használható tárolási célként és helyreállítási helyként a Veeam biztonsági mentéséhez és helyreállításához
author: karauten
ms.author: karauten
ms.date: 03/15/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 15ab808bcb8521b0bd3a5ebd0f56bf336571d676
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103562025"
---
# <a name="backup-to-azure-with-veeam"></a>Biztonsági mentés az Azure-ba a Veeam

Ebből a cikkből megtudhatja, hogyan integrálhat egy Veeam-infrastruktúrát az Azure Blob Storage használatával. Ez magában foglalja az előfeltételeket, a szempontokat, a megvalósítást és az operatív útmutatást. Ez a cikk az Azure-t a telephelyen kívüli biztonsági mentési célként és egy helyreállítási webhelyként használja, amely megakadályozza az elsődleges helyen lévő normál működést.

> [!NOTE]
> A Veeam a helyreállítási időre vonatkozó célkitűzés (RTO) megoldást is kínál, Veeam-replikációval. Ez a megoldás lehetővé teszi, hogy olyan készenléti virtuális géppel rendelkezzen, amely segít a gyorsabb helyreállításban egy Azure-beli éles környezetben felhasználható vészhelyzet esetén. A Veeam emellett dedikált eszközökkel is rendelkezik az Azure és az Office 365-erőforrások biztonsági mentéséhez. Ezek a képességek kívül esnek a dokumentum hatókörén.

## <a name="reference-architecture"></a>Referenciaarchitektúra

Az alábbi ábra az Azure-ba és az Azure-ba történő helyszíni üzembe helyezési architektúrát ismerteti.

![Veeam az Azure Reference Architecture diagramra.](../media/veeam-architecture.png)

A meglévő Veeam üzembe helyezése egyszerűen integrálható az Azure-ba egy Azure Storage-fiók vagy több fiók hozzáadásával a Felhőbeli biztonsági mentési tárházként. A Veeam lehetővé teszi, hogy az Azure-ban helyszíni biztonsági másolatokat is helyreállítson, így az Azure-ban egy helyreállítási igény szerinti webhely is elérhetővé válik.

## <a name="veeam-interoperability-matrix"></a>Veeam együttműködési mátrix

| Számítási feladat | GPv2 és Blob Storage | A ritka elérésű szint támogatása | Archiválási szint támogatása | Data Box család támogatása |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| Helyszíni virtuális gépek/adatkészletek | v10a | v10a | N/A | 10a<sup>*</sup> |
| Azure-beli virtuális gépek | v10a | v10a | N/A | 10a<sup>*</sup> |
| Azure-blob | v10a | v10a | N/A | 10a<sup>*</sup> |
| Azure Files | v10a | v10a | N/A | 10a<sup>*</sup> |

<sup>*</sup>A Veeam biztonsági mentési és replikációs támogatási REST API csak Azure Data Box esetében használható. Ezért a Azure Data Box Disk nem támogatott.

## <a name="before-you-begin"></a>Előkészületek

Egy kis előzetes tervezési megoldás segítségével az Azure-t külső biztonsági mentési célpontként és helyreállítási helyként használhatja.

### <a name="get-started-with-azure"></a>Az Azure használatának első lépései

A Microsoft keretrendszert biztosít az Azure-ban való ismerkedéshez. A [Cloud bevezetési keretrendszer](https://docs.microsoft.com/azure/architecture/cloud-adoption/) (CAF) a nagyvállalati digitális átalakulás részletes megközelítése, és átfogó útmutató a Felhőbeli éles környezetek bevezetésének megtervezéséhez. A CAF egy lépésenkénti [Azure telepítési útmutatót](https://docs.microsoft.com/azure/cloud-adoption-framework/ready/azure-setup-guide/) tartalmaz, amely segít a gyors és biztonságos működésben. A [Azure Portal](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade)interaktív verzióját is megtalálhatja. Megtalálhatja a minta architektúrákat, az alkalmazások üzembe helyezésére vonatkozó ajánlott eljárásokat, valamint az erőforrások ingyenes képzését, amelyekkel az Azure-szakértelem elérési útját helyezheti el.

### <a name="consider-the-network-between-your-location-and-azure"></a>Vegye figyelembe a hálózat helyét és az Azure-t

Ha Felhőbeli erőforrásokat használ a termelési, tesztelési és fejlesztési, illetve biztonsági mentési cél-és helyreállítási hely futtatásához, fontos tisztában lenni a kezdeti biztonsági mentéshez szükséges sávszélességgel és a folyamatos napi adatátvitelsel.

A Azure Data Box a kezdeti biztonsági mentési alapkonfigurációt az Azure-ba anélkül helyezheti át, hogy nagyobb sávszélességre lenne szükség. Ez akkor hasznos, ha az alapértékek várhatóan hosszabb időt vehetnek igénybe, mint amennyit el tud viselni. A kezdeti biztonsági mentés átviteléhez szükséges időt a Storage-fiók létrehozásakor használhatja a Adatátvitel becslést.

![Az Azure Storage adatátviteli kalkulátorát mutatja be a portálon.](../media/az-storage-transfer.png)

Ne feledje, hogy elegendő hálózati kapacitásra van szükség a napi adatátvitelek támogatásához a szükséges adatátviteli ablakban (biztonsági mentési ablak) az üzemi alkalmazások befolyásolása nélkül. Ez a szakasz a hálózati igények felméréséhez rendelkezésre álló eszközöket és technikákat ismerteti.

#### <a name="determine-how-much-bandwidth-youll-need"></a>Határozza meg, hogy mekkora sávszélességre lesz szüksége

Több értékelési lehetőség áll rendelkezésre a változási arány és a biztonságimásolat-készlet teljes méretének meghatározásához az Azure-ba történő kezdeti alapkonfigurációhoz. Íme néhány példa az értékelési és jelentéskészítési eszközökre:

- [MiTrend](https://mitrend.com/)
- [Az apt](https://www.veritas.com/insights/aptare-it-analytics)
- [Datavoss](https://www.datavoss.com/)

#### <a name="determine-unutilized-internet-bandwidth"></a>Nem használt internetes sávszélesség megállapítása

Fontos tudni, hogy Mennyibe kerül a napi rendszerességgel elérhető sávszélesség (vagy *belmagasság*). Ez segít felmérni, hogy a céljai megfelelnek-e az alábbiaknak:

- a feltöltés kezdeti ideje, ha nem használja a Azure Data Box az offline előkészítéshez
- napi biztonsági mentések végrehajtása a korábban azonosított változási arány és a biztonsági mentési ablak alapján

A következő módszerekkel azonosíthatja azt a sávszélesség-belmagasságot, amelyet az Azure-ba készített biztonsági másolatok ingyenesen használhatnak fel.

- Ha Ön egy meglévő Azure ExpressRoute-ügyfél, tekintse meg az [áramköri használatot](../../../../../expressroute/expressroute-monitoring-metrics-alerts.md#circuits-metrics) a Azure Portalban.
- Lépjen kapcsolatba az INTERNETSZOLGÁLTATÓval. Képesnek kell lenniük a meglévő napi és havi kihasználtságot bemutató jelentések megosztására.
- Több eszköz is mérhető a használathoz, ha figyeli a hálózati forgalmat az útválasztó/váltás szintjén. Ezek a következők:

  - [SolarWinds sávszélesség-elemző csomag](https://www.solarwinds.com/network-bandwidth-analyzer-pack?CMP=ORG-BLG-DNS)
  - [Paessler PRTG](https://www.paessler.com/bandwidth_monitoring)
  - [Cisco hálózati segéd](https://www.cisco.com/c/en/us/products/cloud-systems-management/network-assistant/index.html)
  - [WhatsUp arany](https://www.whatsupgold.com/network-traffic-monitoring)

### <a name="choose-the-right-storage-options"></a>Válassza ki a megfelelő tárolási beállításokat

Ha az Azure-t biztonsági mentési célként használja, az [Azure Blob Storage](../../../../blobs/storage-blobs-introduction.md)-t fogja használni. A blob Storage a Microsoft objektumorientált tárolási megoldása. A blob Storage nagy mennyiségű strukturálatlan adat tárolására van optimalizálva, amely olyan adatok, amelyek nem felelnek meg semmilyen adatmodellnek vagy definíciónak. Emellett az Azure Storage tartós, magasan elérhető, biztonságos és skálázható. Kiválaszthatja a számítási feladatok megfelelő tárhelyét, így biztosíthatja a [rugalmasság szintjét](../../../../common/storage-redundancy.md) , hogy megfeleljen a belső SLA-kat. A blob Storage szolgáltatás használata díjköteles. [Havi díjat számítunk](../../../../blobs/storage-blob-storage-tiers.md#pricing-and-billing) fel a tárolt adatok mennyiségére, az adatok elérésére, valamint a lassú és az archív rétegek esetén a minimálisan szükséges megőrzési időtartamra. A biztonsági mentési adatra vonatkozó rugalmassági és a rétegek közötti beállításokat a következő táblázatokban összegzi a rendszer.

**BLOB Storage rugalmassági beállításai:**

|  |Helyileg redundáns  |Zóna – redundáns  |Geo-redundáns  |Földrajzi zóna – redundáns  |
|---------|---------|---------|---------|---------|
|**A másolatok tényleges száma**     | 3         | 3         | 6         | 6 |
|**rendelkezésre állási zónák száma**     | 1         | 3         | 2         | 4 |
|s **régió száma**     | 1         | 1         | 2         | 2 |
|**Manuális feladatátvétel másodlagos régióba**     | N.A.         | N.A.         | Igen         | Igen |

**BLOB Storage-rétegek:**

|  | Gyors elérési szint   |Hűvös szint   | Archiválási szint |
| ----------- | ----------- | -----------  | -----------  |
| **Rendelkezésre állás** | 99.9%         | 99%         | Offline      |
| **Használati díjak** | Magasabb tárolási költségek, alacsonyabb hozzáférés és tranzakciós költségek | Alacsonyabb tárolási költségek, magasabb hozzáférési és tranzakciós költségek | Legalacsonyabb tárolási költségek, legmagasabb hozzáférési és tranzakciós költségek |
| **Minimális adatmegőrzés szükséges** | NA | 30 nap | 180 nap |
| **Késés (az első bájtig eltelt idő)** | Ezredmásodpercben | Ezredmásodpercben | Óra |

#### <a name="sample-backup-to-azure-cost-model"></a>Minta biztonsági mentés az Azure Cost modelre

A használaton kívüli felhasználás a felhőben új ügyfelek számára is ijesztő lehet. Míg csak a felhasznált kapacitásért kell fizetnie, a tranzakciókat (olvasási és írási) és a [kimenő adatforgalomért](https://azure.microsoft.com/pricing/details/bandwidth/) is fizetnie kell a helyszíni környezetre, amikor az [Azure Express Route Direct helyi vagy Express Route Unlimited adatforgalmi](https://azure.microsoft.com/pricing/details/expressroute/) csomagja használatban van, ahol az Azure-ból kimenő adatforgalom szerepel. Az [Azure díjszabási kalkulátorával](https://azure.microsoft.com/pricing/calculator/) elvégezheti a "mit if" elemzést. Az elemzést a lista díjszabása alapján vagy az [Azure Storage szolgáltatásban fenntartott kapacitás díjszabásával](../../../../../cost-management-billing/reservations/save-compute-costs-reservations.md)alapozhatja, amely akár 38%-os megtakarítást is biztosíthat. Íme egy példa a díjszabásra az Azure-ba történő biztonsági mentés havi költségének modellezéséhez. Ez csak egy példa. *A díjszabás az itt nem rögzített tevékenységek miatt változhat.*

|Költségek tényezője  |Havi költség  |
|---------|---------|
|100 TB biztonsági mentési adatok a ritkán használt tárolóban     |$1556,48         |
|2 TB új, naponta írt adat x 30 nap     |$72 a tranzakciókban          |
|Havi becsült összeg     |$1628,48         |
|---------|---------|
|5 TB-os egyszeri visszaállítás a helyszíni hálózaton keresztül   | $527,26         |

> [!Note]
> Ez a becslés az Azure-beli USA-beli utólagos elszámolású díjszabás alapján lett létrehozva az Azure díjszabási Kalkulátorban, és a Veeam alapértelmezett értéke a 256 kb a WAN-átvitelek esetében. Ez a példa nem alkalmazható a követelményekre.

## <a name="implementation-guidance"></a>Megvalósítási útmutató

Ez a szakasz egy rövid útmutatót tartalmaz az Azure Storage helyszíni Veeam-telepítéshez való hozzáadásához. Részletes útmutatást és tervezési szempontokat a [Veeam Cloud csatlakoztatás biztonsági mentési útmutatójában](https://helpcenter.veeam.com/docs/backup/cloud/cloud_backup.html?ver=100)talál.

1. Nyissa meg a Azure Portalt, és keressen rá a **Storage-fiókok** kifejezésre. Az alapértelmezett szolgáltatás ikonra is kattinthat.

      ![Megjeleníti a Azure Portal lévő Storage-fiókok hozzáadását.](../media/azure-portal.png)

      ![Itt látható, hogy hol írta be a tárolót a Azure Portal keresőmezőbe.](../media/locate-storage-account.png)

2. Fiók hozzáadásához válassza a **Létrehozás** lehetőséget. Válasszon ki vagy hozzon létre egy erőforráscsoportot, adjon meg egy egyedi nevet, válassza ki a régiót, válassza a **normál** teljesítmény lehetőséget, a fiók típusa legyen a **Storage v2**, válassza ki a SLA-nak megfelelő replikációs szintet, valamint azt az alapértelmezett szintet, amelyet a biztonsági mentési szoftver alkalmaz. Az Azure Storage-fiók egy fiók-és Veeam-házirendekben elérhető, gyors és ritka elérésű, valamint archív csomagokat tesz lehetővé, így több réteg is használható az adatok életciklusának hatékony kezelésére.

    ![A Storage-fiók beállításainak megjelenítése a portálon](../media/account-create-1.png)

3. Tartsa meg az alapértelmezett hálózati beállításokat, és folytassa az **adatvédelemre**. Itt engedélyezheti a Soft delete használatát, amely lehetővé teszi egy véletlenül törölt biztonságimásolat-fájl helyreállítását a megadott megőrzési időtartamon belül, és védelmet nyújt a véletlen vagy rosszindulatú törlés ellen.

    ![Az adatvédelmi beállítások megjelenítése a portálon.](../media/account-create-2.png)

4. A következő lépésben a **speciális** képernyő alapértelmezett beállításait ajánljuk a biztonsági mentéshez az Azure-használati esetekre.

    ![A portál speciális beállítások lapját jeleníti meg.](../media/account-create-3.png)

5. Címkék hozzáadása a szervezethez, ha címkézést használ, és létrehozza a fiókját.

6. A fiók Veeam-környezetbe való felvétele előtt két gyors lépésre van szükség. Navigáljon a Azure Portal létrehozott fiókhoz, és válassza a **blob Service** menü **tárolók** elemét. Adjon hozzá egy tárolót, és válasszon egy értelmes nevet. Ezután navigáljon a **hozzáférési kulcsok** elemre a **Beállítások** területen, és másolja a **Storage-fiók nevét** és a két hozzáférési kulcs egyikét. A következő lépésekben szüksége lesz a tároló nevére, a fiók nevére és a hozzáférési kulcsra.

    ![Megjeleníti a tárolók létrehozását a portálon.](../media/container-b.png)

    ![Megjeleníti a hozzáférési kulcs beállításait a portálon.](../media/access-key.png)

    > [!Note]
    > A Veeam biztonsági mentése és replikálása további lehetőségeket kínál az Azure-hoz való kapcsolódásra. Ennek a cikknek a használata esetén a Microsoft Azure Blob Storage biztonsági mentési célként használva a fenti módszer az ajánlott eljárás.

7. *(Nem kötelező)* Az üzembe helyezéshez további biztonsági rétegeket is hozzáadhat.

     1. A szerepköralapú hozzáférés konfigurálásával korlátozhatja, hogy ki végezhet módosításokat a Storage-fiókban. További információ: [beépített szerepkörök a felügyeleti műveletekhez](../../../../common/authorization-resource-provider.md#built-in-roles-for-management-operations).

     1. Korlátozza a fiókhoz való hozzáférést meghatározott hálózati szegmensekre a [tárolási tűzfal beállításaival](../../../../common/storage-network-security.md) , hogy megakadályozza a vállalati hálózaton kívülről történő hozzáférési kísérleteket.

        ![Megjeleníti a tároló tűzfal beállításait a portálon.](../media/storage-firewall.png)

     1. A fiók [törlési zárolásának](../../../../../azure-resource-manager/management/lock-resources.md) beállításával megakadályozhatja a Storage-fiók véletlen törlését.

        ![Erőforrás-zárolás](../media/resource-lock.png)

     1. További [ajánlott biztonsági eljárások](../../../../../storage/blobs/security-recommendations.md)konfigurálása.

8. A Veaam biztonsági mentési és replikációs konzolján navigáljon a **biztonsági mentési infrastruktúra** elemre – > kattintson a jobb gombbal az Áttekintés ablaktáblára, majd válassza a **Backup-tárház hozzáadása** elemet a konfigurációs varázsló megnyitásához. A párbeszédpanelen válassza az **objektum Storage**  ->  **Microsoft Azure Blob Storage**  ->  **Azure Blob Storage** lehetőséget.

    ![Megjeleníti az Veeam-tárház varázsló objektumok tárolójának kiválasztását.](../media/veeam-repo-a.png)

    ![Megjeleníti Microsoft Azure Blob Storage kiválasztását a Veeam adattár varázslóban.](../media/veeam-repo-b.png)

    ![Az Azure Blob Storage kiválasztását mutatja a Veeam adattár varázslóban.](../media/veeam-repo-c.png)

9. Ezután adja meg az új blob Storage-tárház nevét és leírását.

    ![Megjeleníti a tárház nevének beírását a Veeam adattár varázslóban.](../media/veeam-repo-d.png)

10. A következő lépésben adja hozzá a hitelesítő adatokat az Azure Storage-fiók eléréséhez. Válassza ki **Microsoft Azure Storage fiókot** a Felhőbeli hitelesítőadat-kezelőben, és adja meg a Storage-fiók nevét és elérési kulcsát. Ha alkalmazható, válassza az **Azure Global** elemet a régió-választóban, és minden átjáró-kiszolgálót.

    ![Egy fiók megadását mutatja a Veeam adattár varázslóban.](../media/veeam-repo-e.png)

    > [!Note]
    > Ha úgy dönt, hogy nem használ Veeam-átjáró kiszolgálót, győződjön meg arról, hogy a kibővíthető Tárházak mindegyike rendelkezik közvetlen internet-hozzáféréssel.

11. A tároló regisztrálása lapon válassza ki az Azure Storage-tárolót, és válasszon ki vagy hozzon létre egy mappát a biztonsági mentések tárolásához. Meghatározhatja a Veeam által használandó teljes tárolókapacitást is, amely a javasolt. Tekintse át a megjelenített információkat az összefoglalás szakaszban, és fejezze be a konfigurációs eszközt. Most már kiválaszthatja az új tárházat a biztonsági mentési feladatok konfigurációjában.

    ![Megjeleníti a tárolók megadását a Veeam adattár varázslóban.](../media/veeam-repo-f.png)

    ![Megjeleníti a mappa létrehozását a Veeam adattár varázslóban.](../media/veeam-repo-g.png)

## <a name="operational-guidance"></a>Üzemeltetési útmutató

### <a name="azure-alerts-and-performance-monitoring"></a>Azure-riasztások és-Teljesítményfigyelés

Javasoljuk, hogy az Azure-erőforrások és a Veeam képes legyen a biztonsági másolatok tárolásához használni kívánt tárolási célra használni. A Azure Monitor és a Veeam figyelési képességeinek kombinációja (a Veeam-kezelő konzol **feladatok** csomópontjának **statisztika** lapja, vagy az olyan speciális beállítások, mint például a Veeam egy jelentéskészítő) segít megőrizni a környezetét.

#### <a name="azure-portal"></a>Azure Portal

Az Azure egy robusztus figyelési megoldást kínál, [Azure monitor](../../../../../azure-monitor/essentials/monitor-azure-resource.md)formájában. A [Azure monitor konfigurálhatja](../../../../common/monitor-storage.md) az Azure Storage-kapacitás, a tranzakciók, a rendelkezésre állás, a hitelesítés és egyéb műveletek nyomon követésére. A nyomon követett mérőszámok teljes referenciája [itt](../../../../blobs/monitor-blob-storage-reference.md)található. Néhány hasznos mérőszám a BlobCapacity – annak érdekében, hogy továbbra is a Storage-fiók maximális [kapacitása](../../../../common/scalability-targets-standard-account.md), a bejövő forgalom és a kimenő adatok mennyisége alatt maradjon, nyomon követheti az Azure Storage-fiókba írt és beolvasott adatok mennyiségét, és SuccessE2ELatency az Azure Storage-ba irányuló és a MediaAgent érkező kérések időpontjának nyomon követésére.

[Létrehozhat napló-riasztásokat](../../../../../service-health/alerts-activity-log-service-notifications-portal.md) az Azure Storage szolgáltatás állapotának nyomon követéséhez, és bármikor megtekintheti az [Azure status irányítópultját](https://status.azure.com/status) .

#### <a name="veeam-reporting"></a>Veeam-jelentéskészítés

- [Veeam konfigurálása egy jelentéskészítéshez](https://helpcenter.veeam.com/docs/one/reporter/configure_reporter.html?ver=100)
- [Veeam biztonsági mentési és replikálási riasztások](https://helpcenter.veeam.com/docs/one/monitor/backup_alarms.html?ver=100)

### <a name="how-to-open-support-cases"></a>Támogatási esetek megnyitása

Ha segítségre van szüksége az Azure-megoldással való biztonsági mentéshez, nyisson meg egy esetet a Veeam és az Azure-ban is. Ez segít a támogatási szervezetek számára, hogy szükség esetén működjenek együtt.

#### <a name="to-open-a-case-with-veeam"></a>Eset megnyitása a Veeam

A [Veeam ügyfél-támogatási webhelyén](https://www.veeam.com/support.html)jelentkezzen be, és nyisson meg egy esetet.

A Veeam által elérhető támogatási lehetőségek megismeréséhez tekintse meg a [Veeam-ügyfél támogatási szabályzatát](https://www.veeam.com/veeam_software_support_policy_ds.pdf).

A következő esetekben is meghívhat egy esetet: [világszerte támogatott számok](https://www.veeam.com/contacts.html?ad=in-text-link#support-numbers)

#### <a name="to-open-a-case-with-azure"></a>Eset megnyitása az Azure-ban

A [Azure Portal](https://portal.azure.com) keressen **támogatást** a felső menüsorban. Válassza a **Súgó + támogatás**  ->  **új támogatási kérés** lehetőséget.

> [!NOTE]
> Amikor megnyit egy esetet, az Azure Storage szolgáltatással vagy az Azure-hálózatkezeléssel kapcsolatos segítségre van szüksége. Ne határozza meg Azure Backup. Azure Backup egy Azure-szolgáltatás neve, és az eset helytelenül lesz átirányítva.

### <a name="links-to-relevant-veeam-documentation"></a>A kapcsolódó Veeam-dokumentációra mutató hivatkozások

További részletekért tekintse meg a következő Veeam dokumentációját:

- [Veeam felhasználói útmutató](https://helpcenter.veeam.com/docs/backup/hyperv/overview.html?ver=100)
- [Veeam-architektúra – útmutató](https://helpcenter.veeam.com/docs/backup/vsphere/backup_architecture.html?ver=100)

### <a name="marketplace-offerings"></a>Piactéri ajánlatok

Továbbra is használhatja a Veeam-megoldást, amely ismeri és megbízik az Azure-on futó munkaterhelések elleni védelemben. A Veeam megkönnyíti a megoldás üzembe helyezését az Azure-ban, és az Azure-Virtual Machines és számos más Azure-szolgáltatást is védetté tenni.

- [A Veeam Backup & replikációjának üzembe helyezése az Azure Marketplace-en keresztül](https://azuremarketplace.microsoft.com/marketplace/apps/veeam.veeam-backup-replication?tab=overview)
- [A Veeam Azure Backup és Recovery webhelye](https://www.veeam.com/backup-azure.html?ad=menu-products)

## <a name="next-steps"></a>Következő lépések

A speciális használati forgatókönyvekkel kapcsolatos információkért tekintse meg az alábbi forrásokat a Veeam webhelyén:

- [A videók Veeam](https://www.veeam.com/how-to-videos.html?ad=menu-resources)
- [Veeam műszaki dokumentációk](https://www.veeam.com/documentation-guides-datasheets.html?ad=menu-resources)
- [Veeam Tudásbázis és gyakori kérdések](https://www.veeam.com/knowledge-base.html?ad=menu-resources)
