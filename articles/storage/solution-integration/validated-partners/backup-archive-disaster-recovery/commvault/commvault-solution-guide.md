---
title: Biztonsági másolatok készítése az Azure-ba az CommVault-mel
titleSuffix: Azure Storage
description: Áttekintést nyújt azokról a tényezőkről, amelyeket figyelembe kell venni, és követni kell, hogy az Azure hogyan használható tárolási célként és helyreállítási helyként a CommVault teljes biztonsági mentéséhez és helyreállításához
author: karauten
ms.author: karauten
ms.date: 03/15/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: ce321574ce2878f51864f55bf5618df2c96d1068
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589888"
---
# <a name="backup-to-azure-with-commvault"></a>Biztonsági mentés az Azure-ba a CommVault

Ebből a cikkből megtudhatja, hogyan integrálhat egy CommVault-infrastruktúrát az Azure Blob Storage használatával. Ez magában foglalja az előfeltételeket, a szempontokat, a megvalósítást és az operatív útmutatást. Ez a cikk az Azure-t a telephelyen kívüli biztonsági mentési célként és egy helyreállítási webhelyként használja, amely megakadályozza az elsődleges helyen lévő normál működést.

> [!NOTE]
> A CommVault egy alacsonyabb helyreállítási idő (RTO) megoldást kínál, CommVault élő szinkronizálást. Ez a megoldás lehetővé teszi, hogy olyan készenléti virtuális géppel rendelkezzen, amely segít a gyorsabb helyreállításban egy Azure-beli éles környezetben felhasználható vészhelyzet esetén. Ezek a képességek kívül esnek a dokumentum hatókörén.

## <a name="reference-architecture"></a>Referenciaarchitektúra

Az alábbi ábra az Azure-ba és az Azure-ba történő helyszíni üzembe helyezési architektúrát ismerteti.

![CommVault az Azure-referenciák architektúrája](../media/commvault-diagram.png)

A meglévő CommVault üzembe helyezése egyszerűen integrálható az Azure-ba egy Azure Storage-fiók vagy több fiók felhőalapú tárolási célként való hozzáadásával. A CommVault lehetővé teszi, hogy az Azure-ban helyszíni biztonsági másolatokat is helyreállítson, így az Azure-ban egy helyreállítási igény szerinti webhely is elérhetővé válik.

## <a name="commvault-interoperability-matrix"></a>CommVault együttműködési mátrix

| Számítási feladat | GPv2 és blob Storage | A ritka elérésű szint támogatása | Archiválási szint támogatása | Data Box család támogatása |
|-----------------------|--------------------|--------------------|-------------------------|-------------------------|
| Helyszíni virtuális gépek/adatkészletek | v 11.5 | v 11.5 | v 11.10 | v 11.10 |
| Azure-beli virtuális gépek | v 11.5 | v 11.5 | v 11.5 | N/A |
| Azure-blob | v 11.6 | v 11.6 | v 11.6 | N/A |
| Azure Files | v 11.6 | v 11.6 | v 11.6 | N/A |

## <a name="before-you-begin"></a>Előkészületek

Egy kis előzetes tervezési megoldás segítségével az Azure-t külső biztonsági mentési célpontként és helyreállítási helyként használhatja.

### <a name="get-started-with-azure"></a>Az Azure használatának első lépései

A Microsoft keretrendszert biztosít az Azure-ban való ismerkedéshez. A [Cloud bevezetési keretrendszer](/azure/architecture/cloud-adoption/) (CAF) a nagyvállalati digitális átalakulás részletes megközelítése, és átfogó útmutató a Felhőbeli éles környezetek bevezetésének megtervezéséhez. A CAF egy lépésenkénti [Azure telepítési útmutatót](/azure/cloud-adoption-framework/ready/azure-setup-guide/) tartalmaz, amely segít a gyors és biztonságos működésben. A [Azure Portal](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade)interaktív verzióját is megtalálhatja. Megtalálhatja a minta architektúrákat, az alkalmazások üzembe helyezésére vonatkozó ajánlott eljárásokat, valamint az erőforrások ingyenes képzését, amelyekkel az Azure-szakértelem elérési útját helyezheti el.

### <a name="consider-the-network-between-your-location-and-azure"></a>Vegye figyelembe a hálózat helyét és az Azure-t

Ha Felhőbeli erőforrásokat használ a termelési, tesztelési és fejlesztési, illetve biztonsági mentési cél-és helyreállítási hely futtatásához, fontos tisztában lenni a kezdeti biztonsági mentéshez szükséges sávszélességgel és a folyamatos napi adatátvitelsel.

A Azure Data Box a kezdeti biztonsági mentési alapkonfigurációt az Azure-ba anélkül helyezheti át, hogy nagyobb sávszélességre lenne szükség. Ez akkor hasznos, ha az alapértékek várhatóan hosszabb időt vehetnek igénybe, mint amennyit el tud viselni. A kezdeti biztonsági mentés átviteléhez szükséges időt a Storage-fiók létrehozásakor használhatja a Adatátvitel becslést.

![Az Azure Storage adatátviteli kalkulátorát mutatja be a portálon.](../media/az-storage-transfer.png)

Ne feledje, hogy elegendő hálózati kapacitásra van szükség a napi adatátvitelek támogatásához a szükséges adatátviteli ablakban (biztonsági mentési ablak) az üzemi alkalmazások befolyásolása nélkül. Ez a szakasz a hálózati igények felméréséhez rendelkezésre álló eszközöket és technikákat ismerteti.

#### <a name="determine-how-much-bandwidth-youll-need"></a>Határozza meg, hogy mekkora sávszélességre lesz szüksége

A szükséges sávszélesség meghatározásához használja a következő erőforrásokat:

- A biztonsági mentési szoftver jelentései.
- A CommVault standard jelentéseket biztosít a [változási arány](https://documentation.commvault.com/commvault/v11_sp19/article?p=39699.htm) és a [biztonságimásolat-készlet teljes méretének](https://documentation.commvault.com/commvault/v11_sp19/article?p=39621.htm) meghatározásához az Azure-ba történő kezdeti alapkonfigurációhoz.
- Backup szoftver – független értékelési és jelentéskészítési eszközök, például:
  - [MiTrend](https://mitrend.com/)
  - [Aptare](https://www.veritas.com/insights/aptare-it-analytics)
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
|**régiók száma**     | 1         | 1         | 2         | 2 |
|**Manuális feladatátvétel másodlagos régióba**     | N.A.         | N.A.         | Igen         | Yes |

**BLOB Storage-rétegek:**

|  | Gyors elérési szint   |Hűvös szint   | Archiválási szint |
| ----------- | ----------- | -----------  | -----------  |
| **Rendelkezésre állás** | 99.9%         | 99%         | Offline      |
| **Használati díjak** | Magasabb tárolási költségek, alacsonyabb hozzáférés és tranzakciós költségek | Alacsonyabb tárolási költségek, magasabb hozzáférési és tranzakciós költségek | Legalacsonyabb tárolási költségek, legmagasabb hozzáférési és tranzakciós költségek |
| **Minimális adatmegőrzés szükséges**| N/A | 30 nap | 180 nap |
| **Késés (az első bájtig eltelt idő)** | Ezredmásodpercben | Ezredmásodpercben | Óra |

#### <a name="sample-backup-to-azure-cost-model"></a>Minta biztonsági mentés az Azure Cost modelre

A használaton kívüli felhasználás a felhőben új ügyfelek számára is ijesztő lehet. Míg csak a felhasznált kapacitásért kell fizetnie, a tranzakciókat (olvasási és írási) és a [kimenő adatforgalomért](https://azure.microsoft.com/pricing/details/bandwidth/) is fizetnie kell a helyszíni környezetre, amikor az [Azure Express Route Direct helyi vagy Express Route Unlimited adatforgalmi](https://azure.microsoft.com/pricing/details/expressroute/) csomagja használatban van, ahol az Azure-ból kimenő adatforgalom szerepel. Az [Azure díjszabási kalkulátorával](https://azure.microsoft.com/pricing/calculator/) elvégezheti a "mit if" elemzést. Az elemzést a lista díjszabása alapján vagy az [Azure Storage szolgáltatásban fenntartott kapacitás díjszabásával](../../../../../cost-management-billing/reservations/save-compute-costs-reservations.md)alapozhatja, amely akár 38%-os megtakarítást is biztosíthat. Íme egy példa a díjszabásra az Azure-ba történő biztonsági mentés havi költségének modellezéséhez. Ez csak egy példa. *A díjszabás az itt nem rögzített tevékenységek miatt változhat.*

|Költségek tényezője  |Havi költség  |
|---------|---------|
|100 TB biztonsági mentési adatok a ritkán használt tárolóban     |$1556,48         |
|2 TB új, naponta írt adat x 30 nap     |$39 a tranzakciókban          |
|Havi becsült összeg     |$1595,48         |
|---------|---------|
|5 TB-os egyszeri visszaállítás a helyszíni hálózaton keresztül   | $491,26         |

> [!NOTE]
> Ez a becslés az Azure-beli USA-beli utólagos elszámolású díjszabás alapján lett létrehozva, és a CommVault alapértelmezett 32 MB-os alméretet eredményez, amely az 65 536 PUT-kérelmeket (írási tranzakciókat) állítja elő naponta. Ez a példa nem alkalmazható a követelményekre.

## <a name="implementation-guidance"></a>Megvalósítási útmutató

Ez a szakasz egy rövid útmutatót tartalmaz az Azure Storage helyszíni CommVault-telepítéshez való hozzáadásához. Részletes útmutatást és tervezési szempontokat a következő témakörben talál: [Microsoft Azure CommVault nyilvános felhőalapú architektúrájának útmutatója](https://documentation.commvault.com/commvault/v11/others/pdf/public-cloud-architecture-guide-for-microsoft-azure11-19.pdf).

1. Nyissa meg a Azure Portalt, és keressen rá a **Storage-fiókok** kifejezésre. Az alapértelmezett **Storage-fiókok** ikonra kattintva is megtekintheti.

    ![Megjeleníti a Azure Portal lévő Storage-fiókok hozzáadását.](../media/azure-portal.png)
  
    ![Itt látható, hogy hol írta be a tárolót a Azure Portal keresőmezőbe.](../media/locate-storage-account.png)

2. Fiók hozzáadásához válassza a **Létrehozás** lehetőséget. Válasszon ki vagy hozzon létre egy erőforráscsoportot, adjon meg egy egyedi nevet, válassza ki a régiót, válassza a **normál** teljesítmény lehetőséget, a fiók típusa legyen a **Storage v2**, válassza ki a SLA-nak megfelelő replikációs szintet, valamint azt az alapértelmezett szintet, amelyet a biztonsági mentési szoftver alkalmaz. Az Azure Storage-fiók egy fiók-és CommVault-házirendekben elérhető, gyors és ritka elérésű, valamint archív csomagokat tesz lehetővé, így több réteg is használható az adatok életciklusának hatékony kezelésére.

    ![A Storage-fiók beállításainak megjelenítése a portálon](../media/account-create-1.png)

3. Tartsa meg az alapértelmezett hálózati beállításokat, és folytassa az **adatvédelemre**. Itt engedélyezheti a Soft delete használatát, amely lehetővé teszi egy véletlenül törölt biztonságimásolat-fájl helyreállítását a megadott megőrzési időtartamon belül, és védelmet nyújt a véletlen vagy rosszindulatú törlés ellen.

    ![Az adatvédelmi beállítások megjelenítése a portálon.](../media/account-create-2.png)

4. A következő lépésben a **speciális** képernyő alapértelmezett beállításait ajánljuk a biztonsági mentéshez az Azure-használati esetekre.

    ![A portál speciális beállítások lapját jeleníti meg.](../media/account-create-3.png)

5. Címkék hozzáadása a szervezethez, ha címkézést használ, és létrehozza a fiókját.

6. A fiók CommVault-környezetbe való felvétele előtt két gyors lépésre van szükség. Navigáljon a Azure Portal létrehozott fiókhoz, és válassza a **blob Service** menü **tárolók** elemét. Adjon hozzá egy tárolót, és válasszon egy értelmes nevet. Ezután navigáljon a **hozzáférési kulcsok** elemre a **Beállítások** területen, és másolja a **Storage-fiók nevét** és a két hozzáférési kulcs egyikét. A következő lépésekben szüksége lesz a tároló nevére, a fiók nevére és a hozzáférési kulcsra.

    ![Megjeleníti a tárolók létrehozását a portálon.](../media/container.png)

    ![Megjeleníti a hozzáférési kulcs beállításait a portálon.](../media/access-key.png)

7. (Nem *kötelező*) További biztonsági rétegeket is hozzáadhat az üzembe helyezéshez.

    1. A szerepköralapú hozzáférés konfigurálásával korlátozhatja, hogy ki végezhet módosításokat a Storage-fiókban. További információ: [beépített szerepkörök a felügyeleti műveletekhez](../../../../common/authorization-resource-provider.md#built-in-roles-for-management-operations).
    1. Korlátozza a fiókhoz való hozzáférést meghatározott hálózati szegmensekre a [tárolási tűzfal beállításaival](../../../../common/storage-network-security.md) , hogy megakadályozza a vállalati hálózaton kívülről történő hozzáférési kísérleteket.

        ![Megjeleníti a tároló tűzfal beállításait a portálon.](../media/storage-firewall.png)

    1. A fiók [törlési zárolásának](../../../../../azure-resource-manager/management/lock-resources.md) beállításával megakadályozhatja a Storage-fiók véletlen törlését.

        ![Megjeleníti a törlési zárolás beállítását a portálon.](../media/resource-lock.png)

    1. További [ajánlott biztonsági eljárások](../../../../../storage/blobs/security-recommendations.md)konfigurálása.

1. A CommVault-parancssorban navigáljon a   ->  **biztonsági**  ->  **Hitelesítőadat-kezelő** kezelése elemre. Válassza ki a **felhőalapú fiókot**, a **Microsoft Azure Storage** **Szállítói típusát** , válassza ki a **MediaAgent**, amely az Azure-ba irányuló és onnan érkező adatok átvitelét adja meg, vegye fel a Storage-fiók nevét és a hozzáférési kulcsot.

    ![Megjeleníti a hitelesítő adatok hozzáadását a CommVault-parancssorban.](../media/commvault-credential.png)

9. Ezután navigáljon a **Storage**  ->  **Cloud** elemre a CommVault-parancssorban. Válassza a **Hozzáadás** lehetőséget. Adjon egy rövid nevet a Storage-fióknak, majd válassza a **Microsoft Azure Storage** lehetőséget a **típus** listából. Válassza ki a biztonsági másolatok Azure Storage-ba történő átviteléhez használni kívánt Media Agent-kiszolgálót. Adja hozzá a létrehozott tárolót, válassza ki az Azure Storage-fiókban használni kívánt tárolási szintet, majd válassza ki a #8 lépésben létrehozott hitelesítő adatokat. Végezetül adja meg, hogy a deduplikált biztonsági mentéseket vagy sem, sem a deduplikált adatbázis helyét szeretné-e átvinni.

     ![Képernyőkép a CommVault Felhőbeli felhasználói felületének hozzáadásáról. Az Archive (archiválás) legördülő menüben a * * Archive * * (Archívum * *) lehetőség van kiválasztva.](../media/commvault-add-storage.png)

10. Végül adja hozzá az új Azure Storage-erőforrást egy meglévő vagy egy új tervhez a CommVault -parancssorban a  ->  **tervek** biztonsági mentési célként való kezelése révén.

    ![Képernyőkép a CommVault Command Center felhasználói felületéről. A bal oldali navigációs menüben válassza a * * felügyelet * * * * * csomagok * * lehetőséget.](../media/commvault-plan.png)

11. *(Nem kötelező)* Ha az Azure-t helyreállítási helyként vagy CommVault szeretné használni a kiszolgálók és alkalmazások Azure-ba való áttelepítéséhez, ajánlott a VSA-proxy üzembe helyezése az Azure-ban. Részletes útmutatást [itt](https://documentation.commvault.com/commvault/v11/article?p=106208.htm)talál.

## <a name="operational-guidance"></a>Üzemeltetési útmutató

### <a name="azure-alerts-and-performance-monitoring"></a>Azure-riasztások és-Teljesítményfigyelés

Javasoljuk, hogy az Azure-erőforrások és a CommVault képes legyen a biztonsági másolatok tárolásához használni kívánt tárolási célra használni. A Azure Monitor és a CommVault Command Center monitorozásának kombinációja segít a környezet kifogástalan állapotának megőrzésében.

#### <a name="azure-portal"></a>Azure Portal

Az Azure egy robusztus figyelési megoldást kínál, [Azure monitor](../../../../../azure-monitor/essentials/monitor-azure-resource.md)formájában. A [Azure monitor konfigurálhatja](../../../../blobs/monitor-blob-storage.md) az Azure Storage-kapacitás, a tranzakciók, a rendelkezésre állás, a hitelesítés és egyéb műveletek nyomon követésére. Itt megtalálhatja az [itt](../../../../blobs/monitor-blob-storage-reference.md)összegyűjtött metrikák teljes hivatkozását. Néhány hasznos mérőszám a BlobCapacity – annak érdekében, hogy továbbra is a Storage-fiók maximális [kapacitása](../../../../common/scalability-targets-standard-account.md), a bejövő forgalom és a kimenő adatok mennyisége alatt maradjon, nyomon követheti az Azure Storage-fiókba írt és beolvasott adatok mennyiségét, és SuccessE2ELatency az Azure Storage-ba irányuló és a MediaAgent érkező kérések időpontjának nyomon követésére.

[Létrehozhat napló-riasztásokat](../../../../../service-health/alerts-activity-log-service-notifications-portal.md) az Azure Storage szolgáltatás állapotának nyomon követéséhez, és bármikor megtekintheti az [Azure status irányítópultját](https://status.azure.com/status) .

#### <a name="commvault-command-center"></a>CommVault-központ

- [Riasztás létrehozása felhőalapú tárolási készletekhez](https://documentation.commvault.com/commvault/v11/article?p=100514_3.htm)
- Arról, hogy hol tekintheti meg a teljesítménymutatókat, a feladatok befejezését és az alapszintű hibaelhárítást, lásd: [irányítópultok](https://documentation.commvault.com/commvault/v11/article?p=95306_1.htm).

### <a name="how-to-open-support-cases"></a>Támogatási esetek megnyitása

Ha segítségre van szüksége az Azure-megoldással való biztonsági mentéshez, nyisson meg egy esetet a CommVault és az Azure-ban is. Ez segít a támogatási szervezetek számára, hogy szükség esetén működjenek együtt.

#### <a name="to-open-a-case-with-commvault"></a>Eset megnyitása a CommVault

A [CommVault támogatási webhelyén](https://www.commvault.com/support)jelentkezzen be, és nyisson meg egy esetet.

Az Ön számára elérhető támogatási szerződési lehetőségek megismeréséhez tekintse meg a [CommVault támogatási lehetőségeit](https://ma.commvault.com/support) .

Az is előfordulhat, hogy meghívja a-t egy eset megnyitására, vagy a CommVault-támogatás elérését e-mailben:

- Díjmentes: + 1 877-780-3077
- [Világszerte támogatott számok](https://ma.commvault.com/Support/TelephoneSupport)
- [E-mail CommVault-támogatás](mailto:support@commvault.com)

#### <a name="to-open-a-case-with-azure"></a>Eset megnyitása az Azure-ban

A [Azure Portal](https://portal.azure.com) keressen **támogatást** a felső menüsorban. Válassza a **Súgó + támogatás**  ->  **új támogatási kérés** lehetőséget.

> [!NOTE]
> Amikor megnyit egy esetet, az Azure Storage szolgáltatással vagy az Azure-hálózatkezeléssel kapcsolatos segítségre van szüksége. Ne határozza meg Azure Backup. Azure Backup egy Azure-szolgáltatás neve, és az eset helytelenül lesz átirányítva.

### <a name="links-to-relevant-commvault-documentation"></a>A kapcsolódó CommVault-dokumentációra mutató hivatkozások

További részletekért tekintse meg a következő CommVault dokumentációját:

- [CommVault felhasználói útmutató](https://documentation.commvault.com/commvault/v11/article?p=37684_1.htm)
- [CommVault Azure-architektúra – útmutató](https://www.commvault.com/resources/public-cloud-architecture-guide-for-microsoft-azure-v11-sp16)

### <a name="marketplace-offerings"></a>Piactéri ajánlatok

A CommVault megkönnyíti a megoldás üzembe helyezését az Azure-ban az Azure-Virtual Machines és számos más Azure-szolgáltatás megvédése érdekében. További információkat az alábbi hivatkozásokon találhat:

- [A CommVault üzembe helyezése az Azure Marketplace-en keresztül](https://azuremarketplace.microsoft.com/marketplace/apps/commvault.commvault?tab=Overview)
- [CommVault az Azure-ban – Adatlap](https://www.commvault.com/resources/microsoft-azure-cloud-platform-datasheet)
- [A támogatott Azure-funkciók és-szolgáltatások CommVault listája](https://documentation.commvault.com/commvault/v11/article?p=109795_1.htm)
- [A CommVault használata az Azure-beli SAP HANAek elleni védelemhez](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/)

## <a name="next-steps"></a>Következő lépések

A speciális használati forgatókönyvekkel kapcsolatos további információkért tekintse meg ezeket a további CommVault-erőforrásokat.

- [Kiszolgálók és alkalmazások áttelepíthetők az Azure-ba a CommVault használatával](https://www.commvault.com/resources/demonstration-vmware-to-azure-migrations-with-commvault)
- [Az SAP biztosítása az Azure-ban az CommVault-mel](https://www.youtube.com/watch?v=4ZGGE53mGVI)
- [A Office 365 és a CommVault elleni védelem](https://www.youtube.com/watch?v=dl3nvAacxZU)