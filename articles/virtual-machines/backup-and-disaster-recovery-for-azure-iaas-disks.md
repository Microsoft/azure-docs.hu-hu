---
title: Biztonsági mentési és vész-helyreállítási IaaS-lemezek Azure-beli virtuális gépeken
description: Ez a cikk bemutatja, hogyan tervezheti meg a IaaS virtuális gépek és lemezek biztonsági mentését és vész-helyreállítását az Azure-ban. Ez a dokumentum a felügyelt és a nem felügyelt lemezeket is tartalmazza.
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 07/19/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e5ae08c23748e55a8c3b75eb8fb9c112684f022e
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102507906"
---
# <a name="backup-and-disaster-recovery-for-azure-iaas-disks"></a>Azure IaaS-lemezek biztonsági mentése és vész-helyreállítás

Ez a cikk bemutatja, hogyan tervezheti meg a IaaS virtuális gépek és lemezek biztonsági mentését és a vész-helyreállítást (DR) az Azure-ban. Ez a dokumentum a felügyelt és a nem felügyelt lemezeket is tartalmazza.

Első lépésként az Azure platform beépített hibatűrési képességei is bemutatják, amelyek segítenek a helyi hibák elleni védelemben. Ezután megbeszéljük azokat a katasztrófa-forgatókönyveket, amelyek nem teljes mértékben fedik a beépített funkciókat. Emellett több példát is mutatunk a munkaterhelés-forgatókönyvekre, amelyekben különböző biztonsági mentési és DR-megfontolások alkalmazhatók. Ezután áttekintjük a DR IaaS-lemezek lehetséges megoldásait.

## <a name="introduction"></a>Bevezetés

Az Azure platform különféle módszereket használ a redundancia és a hibatűrés érdekében, hogy segítsen az ügyfeleknek a honosított hardverhiba elleni védelemben. A helyi hibák olyan Azure Storage Server-géppel kapcsolatos problémákat okozhatnak, amelyek az adott kiszolgálón lévő virtuális lemez vagy az SSD-k vagy HDD-k hibáinak egy részét tárolják. Az ilyen elszigetelt hardver-összetevők meghibásodása a normál műveletek során fordulhat elő.

Az Azure platform úgy van kialakítva, hogy rugalmas legyen ezekkel a hibákkal. A főbb katasztrófák meghibásodásokat okozhatnak, illetve számos tárolási kiszolgáló vagy akár egy teljes adatközpont megközelíthetetlenek. Bár a virtuális gépek és lemezek általában a honosított hibáktól védve vannak, további lépések szükségesek ahhoz, hogy védelmet nyújtson az egész régióra kiterjedő katasztrofális hibák, például egy súlyos katasztrófa, amely hatással lehet a virtuális gépre és a lemezekre.

A platform meghibásodásának lehetősége mellett előfordulhat, hogy az ügyfél alkalmazásával vagy az adatokkal kapcsolatos problémák léphetnek fel. Előfordulhat például, hogy az alkalmazás egy új verziója véletlenül módosítja az adatvesztést okozó adatmódosítást. Ebben az esetben előfordulhat, hogy az alkalmazást és az adathalmazt egy korábbi verzióra szeretné visszaállítani, amely az utolsó ismert jó állapotot tartalmazza. Ehhez rendszeres biztonsági mentéseket kell fenntartani.

A regionális katasztrófa utáni helyreállításhoz a IaaS VM-lemezeit egy másik régióba kell biztonsági másolatot készíteni.

Mielőtt megnézzük a biztonsági mentési és a DR-beállításokat, a honosított hibák kezelése érdekében Ismerkedjen meg néhány módszersel.

### <a name="azure-iaas-resiliency"></a>Az Azure IaaS rugalmassága

A *rugalmasság* a hardveres összetevőkben előforduló normál hibákra vonatkozó tűréshatárra utal. A rugalmasság lehetővé teszi a hibák helyreállítását, és folytathatja a működést. Nem a hibák elkerülését jelenti, hanem a hibákra adott olyan válaszokat, amelyek kiküszöbölik az állásidőt és az adatveszteséget. A rugalmasság célja, hogy az alkalmazás a hibát követően teljesen működőképes állapotba térjen vissza. Az Azure-beli virtuális gépek és lemezek úgy vannak kialakítva, hogy rugalmasak legyenek a gyakori hardveres hibákhoz. Nézzük meg, hogyan biztosítja ezt a rugalmasságot az Azure IaaS platform.

A virtuális gépek főleg két részből állnak: egy számítási kiszolgálóból és az állandó lemezből. Mindkettő befolyásolja a virtuális gépek hibatűrését.

Ha a virtuális gépet tároló Azure-beli számítási kiszolgáló hardveres meghibásodást tapasztal, ami ritka, az Azure úgy lett kialakítva, hogy automatikusan helyreállítsa a virtuális gépet egy másik kiszolgálón. Ha ez a helyzet, a számítógép újraindul, és a virtuális gép egy kis idő elteltével biztonsági másolatot készít. Az Azure automatikusan észleli az ilyen hardveres hibákat, és helyreállításokat hajt végre, így biztosítva, hogy az ügyfél virtuális gépe a lehető leghamarabb elérhető legyen.

A IaaS lemezek esetében az adattartósság az állandó tárolási platform szempontjából kritikus fontosságú. Az Azure-ügyfelek a IaaS-on futó fontos üzleti alkalmazásokkal rendelkeznek, és az adatok megőrzésével függenek. Az Azure ezeket a IaaS-lemezeket, a helyileg tárolt adatmennyiség három redundáns másolatát is megtervezi. Ezek a másolatok magas tartósságot biztosítanak a helyi hibákkal szemben. Ha a lemezt tároló hardver-összetevők egyike meghibásodik, a rendszer nem érinti a virtuális gépet, mert két további másolattal rendelkezik a lemezes kérelmek támogatásához. Jól működik, még akkor is, ha két különböző, a lemezeket támogató hardver-összetevő (ami ritka) egy időben meghiúsul. 

Annak érdekében, hogy mindig három replikát őrizzen meg, az Azure Storage automatikusan elindít egy új példányt a háttérben, ha a három példány egyike elérhetetlenné válik. Ezért nem szükséges a RAID és az Azure-lemezek használata a hibatűréshez. Az egyszerű RAID 0 konfigurációnak elegendőnek kell lennie ahhoz, hogy nagyobb kötetek létrehozásához szükség esetén a lemezek csíkozása szükséges legyen.

Ennek az architektúrának az az oka, hogy az Azure nagyvállalati szintű tartósságot szállított a IaaS-lemezek számára, és egy piacvezető, nulla százalékos [időarányú meghibásodást](https://en.wikipedia.org/wiki/Annualized_failure_rate)eredményezett.

A számítási gazdagépen vagy a tárolási platformon a honosított hardverhiba miatt előfordulhat, hogy a virtuális gép átmenetileg nem érhető el, mert az [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) a virtuális gépek rendelkezésre állására vonatkozik. Az Azure piacvezető SLA-t is biztosít az Azure Premium SSD-ket használó egyetlen VM-példányhoz.

Ha az alkalmazások számítási feladatait a lemez vagy a virtuális gép ideiglenes leállása miatt szeretné biztosítani az állásidőtől, az ügyfelek használhatják a [rendelkezésre állási csoportokat](./availability.md). A rendelkezésre állási csoportba tartozó két vagy több virtuális gép redundanciát biztosít az alkalmazás számára. Az Azure ezt követően külön tartalék tartományokban hozza létre ezeket a virtuális gépeket és lemezeket különböző energiaellátási, hálózati és kiszolgáló-összetevőkkel.

Ezen különálló tartalék tartományok miatt a honosított hardverhiba általában nem érinti a készletben lévő több virtuális gépet. A különálló tartalék tartományok magas rendelkezésre állást biztosítanak az alkalmazás számára. A rendelkezésre állási csoportok használata jó gyakorlatnak minősül, ha magas rendelkezésre állásra van szükség. A következő szakasz a vész-helyreállítási aspektust ismerteti.

### <a name="backup-and-disaster-recovery"></a>Biztonsági mentés és vész-helyreállítás

A vész-helyreállítás a ritka, de súlyos incidensekkel való helyreállítás lehetősége. Ezek az incidensek olyan nem átmeneti és széles körű hibákat tartalmaznak, mint például a szolgáltatás megszakadása, amely egy egész régiót érint. A vész-helyreállítási szolgáltatás magában foglalja az adatok biztonsági mentését és archiválását, és manuális beavatkozást is tartalmazhat, például egy adatbázis biztonsági másolatból való visszaállítását.

Előfordulhat, hogy az Azure platform beépített védelme a honosított hibák miatt nem teljes mértékben védelmet biztosít a virtuális gépek/lemezek számára, ha egy jelentős katasztrófa nagy léptékű leállást okoz. Ezek a nagy léptékű leállások katasztrofális eseményeket is tartalmaznak, például ha egy adatközpontot egy hurrikán, földrengés, tűz vagy nagy léptékű hardverhiba okoz. Emellett az alkalmazás-vagy adatproblémák miatt hibák merülhetnek fel.

A IaaS számítási feladatainak kimaradások elleni védelméhez meg kell terveznie a redundanciát, és biztonsági másolatokkal kell rendelkeznie a helyreállítás engedélyezéséhez. A vész-helyreállítás érdekében a biztonsági mentést egy másik földrajzi helyen kell elvégeznie, az elsődleges helytől távolabb. Ez a megközelítés segít biztosítani, hogy a biztonsági mentést a virtuális gépet vagy lemezeket eredetileg érintő esemény ne befolyásolja. További információ: vész- [helyreállítás Azure-alkalmazásokhoz](/azure/architecture/resiliency/disaster-recovery-azure-applications).

A DR-megfontolások a következő szempontokat tartalmazhatják:

- Magas rendelkezésre állás: az alkalmazás azon képessége, hogy a működést kifogástalan állapotban folytassa, jelentős állásidő nélkül. A *Kifogástalan állapot* szerint ez az állapot azt jelenti, hogy az alkalmazás válaszol, és a felhasználók csatlakozhatnak az alkalmazáshoz, és kommunikálhatnak vele. Előfordulhat, hogy bizonyos kritikus fontosságú alkalmazásokat és adatbázisokat mindig elérhetővé kell tennie, még akkor is, ha a platformon hiba történik. A számítási feladatok esetében előfordulhat, hogy az alkalmazáshoz és az adatokhoz is meg kell terveznie a redundanciát.

- Adattartósság: bizonyos esetekben a fő szempont az, hogy az adatvesztés megmaradjon, ha vészhelyzet történik. Ezért előfordulhat, hogy egy másik helyen kell biztonsági másolatot készítenie az adatairól. Ilyen számítási feladatok esetén előfordulhat, hogy nincs szükség teljes redundanciára az alkalmazáshoz, de csak a lemezek rendszeres biztonsági mentése.

## <a name="backup-and-dr-scenarios"></a>Biztonsági mentési és DR-forgatókönyvek

Vizsgáljuk meg az alkalmazások számítási feladatainak példáit, valamint a vész-helyreállítási tervezési szempontokat.

### <a name="scenario-1-major-database-solutions"></a>1. forgatókönyv: főbb adatbázis-megoldások

Vegyünk a magas rendelkezésre állást támogató éles adatbázis-kiszolgálót (például SQL Server vagy Oracle). Az éles környezetben futó alkalmazások és felhasználók ettől az adatbázistól függenek. Előfordulhat, hogy a rendszer vész-helyreállítási tervének a következő követelményeket kell támogatnia:

- Az adatvédelemre és helyreállításra van szükség.
- A kiszolgálónak elérhetőnek kell lennie a használatra.

Előfordulhat, hogy a vész-helyreállítási terv az adatbázis replikájának megőrzését igényli egy másik régióban biztonsági másolatként. A kiszolgáló rendelkezésre állásának és az adatok helyreállításának követelményeitől függően előfordulhat, hogy a megoldás egy aktív-aktív vagy aktív-passzív replika-helyről az adatok rendszeres offline biztonsági mentései közé esik. A rokon adatbázisok, például a SQL Server és az Oracle, különböző replikációs lehetőségeket biztosítanak. SQL Server esetén a magas rendelkezésre állás érdekében használja a [SQL Server AlwaysOn rendelkezésre állási csoportok](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) .

A NoSQL-adatbázisok, például a MongoDB, támogatják a redundancia- [replikákat](https://docs.mongodb.com/manual/replication/) is. A magas rendelkezésre állású replikák használatban vannak.

### <a name="scenario-2-a-cluster-of-redundant-vms"></a>2. forgatókönyv: redundáns virtuális gépekből álló fürt

Vegye figyelembe a redundanciát és a terheléselosztást biztosító virtuális gépek fürtje által kezelt munkaterheléseket. Az egyik példa egy régióban üzembe helyezett Cassandra-fürt. Az ilyen típusú architektúra már magas szintű redundanciát biztosít a régión belül. Ha azonban a munkaterhelést regionális szintű meghibásodás miatt szeretné biztosítani, érdemes megfontolnia a fürt elosztását két régióban, vagy a rendszeres biztonsági mentést egy másik régióba.

### <a name="scenario-3-iaas-application-workload"></a>3. forgatókönyv: IaaS alkalmazás munkaterhelése

Nézzük meg a IaaS alkalmazás munkaterhelését. Előfordulhat például, hogy az alkalmazás egy Azure-beli virtuális gépen futó jellemző üzemi munkaterhelés. Lehet, hogy egy webkiszolgáló vagy fájlkiszolgáló egy adott hely tartalmát és egyéb erőforrásait tartja. Az is előfordulhat, hogy egy olyan, a virtuális gépen futó, egyéni kialakítású üzleti alkalmazás, amely a virtuális gépek lemezén tárolta az adatforrásait, erőforrásait és alkalmazási állapotát. Ebben az esetben fontos, hogy rendszeresen készítsen biztonsági mentést. A biztonsági mentés gyakoriságát a virtuális gép számítási feladatának természetétől függően kell megadni. Ha például az alkalmazás naponta fut, és módosítja az összes adatát, akkor a biztonsági mentést óránként kell elvégezni.

Egy másik példa egy jelentéskészítő kiszolgáló, amely más forrásokból származó adatokat kér le, és összesített jelentéseket hoz létre. A virtuális gép vagy a lemezek elvesztése a jelentések elvesztését eredményezheti. Azonban lehetséges lehet a jelentéskészítési folyamat újrafuttatása és a kimenet újbóli előállítása. Ebben az esetben nem igazán adatvesztéssel jár, még akkor is, ha a jelentéskészítő kiszolgáló katasztrófával ütközik. Ennek eredményeképpen előfordulhat, hogy magasabb szintű tolerancia van a jelentéskészítő kiszolgálón tárolt adatmennyiség elvesztéséhez. Ebben az esetben a kevésbé gyakori biztonsági mentések a költségek csökkentésének lehetőségét jelentik.

### <a name="scenario-4-iaas-application-data-issues"></a>4. forgatókönyv: az IaaS kapcsolatos problémák

Az IaaS kapcsolatos problémák egy másik lehetőség. Vegyünk fontolóra egy olyan alkalmazást, amely kritikus kereskedelmi adatokat, például díjszabási információkat számít ki, tart fenn és szolgáltat. Az alkalmazás új verziója olyan szoftveres hibával rendelkezett, amely helytelenül számította ki a díjszabást, és megsérült a platform által kiszolgált meglévő kereskedelmi adatok. Itt a legjobb művelet az alkalmazás korábbi verziójára és az adatmennyiségre való visszaállítás. Ennek engedélyezéséhez készítsen rendszeres biztonsági mentést a rendszerről.

## <a name="disaster-recovery-solution-azure-backup"></a>Vész-helyreállítási megoldás: Azure Backup 

A [Azure Backup](https://azure.microsoft.com/services/backup/) a biztonsági mentésekhez és a Dr-hez használható, és [felügyelt lemezekkel](managed-disks-overview.md) és nem felügyelt lemezekkel is működik. A biztonsági mentési feladatok időalapú biztonsági mentéssel, egyszerű virtuális gépekkel történő helyreállítással és biztonsági mentési adatmegőrzési szabályzatokkal hozhatók létre.

Ha [prémium SSD](disks-types.md)-ket, [felügyelt lemezeket](managed-disks-overview.md)vagy más lemezeket használ a [helyileg redundáns tárolás](../storage/common/storage-redundancy.md#locally-redundant-storage) lehetőséggel, akkor különösen fontos, hogy rendszeresen készítsen biztonsági mentést a Dr. A Azure Backup a helyreállítási tárban lévő adatok hosszú távú megőrzését is eltárolja. Válassza ki a [geo-redundáns tárolási](../storage/common/storage-redundancy.md#geo-redundant-storage) beállítást a Backup Recovery Services-tárolóhoz. Ez a beállítás biztosítja, hogy a biztonsági mentések egy másik Azure-régióba replikálódnak a regionális katasztrófák elleni védelem érdekében.

A nem felügyelt lemezek esetében használhatja a helyileg redundáns tárolási típust a IaaS-lemezekhez, de győződjön meg arról, hogy a Azure Backup engedélyezve van a helyreállítási tár geo-redundáns tárolási beállításával.

> [!NOTE]
> Ha a nem felügyelt lemezek esetében a [geo-redundáns tárolást](../storage/common/storage-redundancy.md#geo-redundant-storage) vagy az [olvasási hozzáférésű geo-redundáns tárolási](../storage/common/storage-redundancy.md#read-access-to-data-in-the-secondary-region)  lehetőséget használja, akkor továbbra is konzisztens Pillanatképek szükségesek a biztonsági mentéshez és a Dr. Használjon [Azure Backup](https://azure.microsoft.com/services/backup/) vagy [konzisztens pillanatképeket](#alternative-solution-consistent-snapshots).

 A következő táblázat a DR számára elérhető megoldások összegzését tartalmazza.

| Eset | Automatikus replikáció | DR megoldás |
| --- | --- | --- |
| Prémium SSD-lemezek | Helyi ([helyileg redundáns tárolás](../storage/common/storage-redundancy.md#locally-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Felügyelt lemezek | Helyi ([helyileg redundáns tárolás](../storage/common/storage-redundancy.md#locally-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Nem felügyelt helyileg redundáns tároló lemezek | Helyi ([helyileg redundáns tárolás](../storage/common/storage-redundancy.md#locally-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Nem felügyelt geo-redundáns tárolási lemezek | Régiók közötti ([geo-redundáns tárolás](../storage/common/storage-redundancy.md#geo-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Konzisztens Pillanatképek](#alternative-solution-consistent-snapshots) |
| Nem felügyelt olvasási hozzáférésű geo-redundáns Storage-lemezek | Régiók közötti[kapcsolat (olvasási hozzáférés geo-redundáns tárolás](../storage/common/storage-redundancy.md#read-access-to-data-in-the-secondary-region)) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Konzisztens Pillanatképek](#alternative-solution-consistent-snapshots) |

A magas rendelkezésre állás a legjobb megoldás, ha felügyelt lemezeket használ egy rendelkezésre állási csoporton Azure Backup. Ha nem felügyelt lemezeket használ, továbbra is használhatja a DR Azure Backup. Ha nem tudja használni a Azure Backupt, akkor a [konzisztens Pillanatképek](#alternative-solution-consistent-snapshots)készítése a későbbi szakaszban leírtak szerint egy alternatív megoldás a biztonsági mentéshez és a Dr-hez.

Az alkalmazások és az infrastruktúra szintjén a magas rendelkezésre állás, a biztonsági mentés és a DR választható lehetőségei a következőképpen adhatók meg:

| Level |   Magas rendelkezésre állás   | Biztonsági mentés vagy DR |
| --- | --- | --- |
| Alkalmazás | SQL Server AlwaysOn | Azure Backup |
| Infrastruktúra    | Rendelkezésre állási csoport  | Földrajzilag redundáns tárolás konzisztens pillanatképekkel |

### <a name="using-azure-backup"></a>Az Azure Backup használata 

A [Azure Backup](../backup/backup-azure-vms-introduction.md) Windows vagy Linux rendszerű virtuális gépek biztonsági mentését végezheti el az Azure Recovery Services-tárolóba. Az üzleti szempontból kritikus fontosságú adatbiztonsági mentést és helyreállítást az a tény nehezíti, hogy az üzleti szempontból kritikus fontosságú adatbiztonsági mentést kell készíteni, miközben az adatfeldolgozást végző alkalmazások futnak. 

A probléma megoldásához Azure Backup biztosít az alkalmazással konzisztens biztonsági mentést a Microsoft munkaterhelésekhez. A Kötet árnyékmásolata szolgáltatás használatával gondoskodik arról, hogy a rendszer megfelelően írja be az adattárolást. A Linux rendszerű virtuális gépek esetében az alapértelmezett biztonsági mentési konzisztencia a fájlokkal konzisztens biztonsági másolatok, mivel a Linux nem rendelkezik a Kötet árnyékmásolata szolgáltatással egyenértékű funkcióval a Windows esetében. Linux rendszerű gépek esetén lásd: [Az Azure Linux rendszerű virtuális gépek alkalmazás-konzisztens biztonsági mentése](../backup/backup-azure-linux-app-consistent.md).

![Azure Backup folyamat][1]

Amikor a Azure Backup az ütemezett időpontban kezdeményez egy biztonsági mentési feladatot, elindítja a virtuális gépen telepített biztonsági mentési bővítményt, hogy elvégezze az időponthoz tartozó pillanatképet. A Kötet árnyékmásolata szolgáltatással való koordináció során pillanatkép készül, hogy a virtuális gép lemezei konzisztens pillanatképet kapjanak, anélkül, hogy le kellene állítania azt. A virtuális gép biztonsági mentési bővítménye az összes írást kiüríti, mielőtt konzisztens pillanatképet hozna az összes lemezről. A pillanatkép elkészítése után az adatok Azure Backup a Backup-tárolóba kerülnek. A biztonsági mentési folyamat hatékonyabbá tételéhez a szolgáltatás csak azokat az adatblokkokat azonosítja és továbbítja, amelyek az utolsó biztonsági mentés után módosultak.

A visszaállításhoz megtekintheti az elérhető biztonsági másolatokat Azure Backup, majd kezdeményezheti a visszaállítást. Az Azure-beli biztonsági mentéseket a [PowerShell használatával](../backup/backup-azure-vms-automation.md)vagy az [Azure CLI](/cli/azure/)használatával hozhatja létre és állíthatja vissza a [Azure Portalon](https://portal.azure.com/)keresztül.

### <a name="steps-to-enable-a-backup"></a>A biztonsági másolatok engedélyezésének lépései

A következő lépésekkel engedélyezheti a virtuális gépek biztonsági mentését a [Azure Portal](https://portal.azure.com/)használatával. A pontos forgatókönyvtől függően van néhány variáció. A részletekért tekintse meg a [Azure Backup](../backup/backup-azure-vms-introduction.md) dokumentációját. A Azure Backup [a felügyelt lemezekkel rendelkező virtuális gépeket is támogatja](https://azure.microsoft.com/blog/azure-managed-disk-backup/).

1.  Hozzon létre egy Recovery Services-tárolót egy virtuális géphez:

    a. A [Azure Portal](https://portal.azure.com/)tallózással keresse meg az **összes erőforrást** , és keresse meg **Recovery Services**-tárolókat.

    b. A **Recovery Services** -tárolók menüben kattintson a **Hozzáadás** gombra, és kövesse a lépéseket egy új tároló létrehozásához ugyanabban a régióban, ahol a virtuális gép található. Ha például a virtuális gép az USA nyugati régiójában található, válassza az USA nyugati régióját a tárolóhoz.

1.  Ellenőrizze az újonnan létrehozott tároló tárolási replikálását. Nyissa meg **Recovery Services** -tárolók területét, és lépjen a **Tulajdonságok**  >  **biztonsági mentési konfiguráció**  >  **frissítése** elemre. Győződjön meg arról, hogy a **geo-redundáns tárolás** beállítás alapértelmezés szerint ki van választva. Ezzel a beállítással biztosíthatja, hogy a tárolót automatikusan egy másodlagos adatközpontba replikálja a rendszer. Például az USA nyugati régiójában lévő tároló automatikusan replikálódik az USA keleti régiójában.

1.  Konfigurálja a biztonsági mentési szabályzatot, és válassza ki a virtuális gépet ugyanabból a felhasználói felületről.

1.  Győződjön meg arról, hogy a biztonsági mentési ügynök telepítve van a virtuális gépen. Ha a virtuális gépet egy Azure Gallery-rendszerkép használatával hozza létre, a biztonsági mentési ügynök már telepítve van. Ellenkező esetben (ha egyéni rendszerképet használ), az utasításokat követve [telepítse a virtuálisgép-ügynököt egy virtuális gépre](../backup/backup-azure-arm-vms-prepare.md#install-the-vm-agent).

1.  Az előző lépések elvégzése után a biztonsági mentés a biztonsági mentési szabályzatban megadott rendszeres időközönként fut. Szükség esetén manuálisan is aktiválhatja az első biztonsági mentést a tároló irányítópultján a Azure Portal.

A Azure Backup parancsfájlok használatával történő automatizálásához tekintse meg a [virtuális gépek biztonsági mentéséhez készült PowerShell-parancsmagokat](../backup/backup-azure-vms-automation.md).

### <a name="steps-for-recovery"></a>A helyreállítás lépései

Ha egy virtuális gépet kell kijavítania vagy újraépíteni, a virtuális gépet visszaállíthatja a tárolóban található biztonsági mentési helyreállítási pontokból. A helyreállítás végrehajtásához több lehetőség közül választhat:

-   Létrehozhat egy új virtuális gépet a biztonsági másolatban lévő virtuális gép időponthoz tartozó ábrázolásával.

-   Visszaállíthatja a lemezeket, majd a sablon használatával testreszabhatja és újraépítheti a visszaállított virtuális gépet.

További információ: a [virtuális gépek visszaállítására szolgáló Azure Portal használatára](../backup/backup-azure-arm-restore-vms.md)vonatkozó utasítások. Ez a dokumentum azt is ismerteti, hogyan állíthatók vissza a biztonsági másolatok egy párosított adatközpontba a Geo-redundáns Backup-tároló használatával, ha az elsődleges adatközpontban katasztrófa van. Ebben az esetben a Azure Backup a másodlagos régió számítási szolgáltatását használja a visszaállított virtuális gép létrehozásához.

A PowerShell használatával is létrehozhat [egy új virtuális gépet a visszaállított lemezekről](../backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

## <a name="alternative-solution-consistent-snapshots"></a>Alternatív megoldás: konzisztens Pillanatképek

Ha nem tudja használni a Azure Backup, a pillanatképek használatával saját biztonsági mentési mechanizmust alkalmazhat. Konzisztens pillanatképek létrehozása a virtuális gép által használt összes lemez számára, majd a pillanatképek replikálása egy másik régióba bonyolult. Emiatt az Azure a biztonsági mentési szolgáltatást jobb megoldásként használja, mint az egyéni megoldások létrehozásakor.

Ha olvasási hozzáférésű geo-redundáns tárolást vagy geo-redundáns tárolást használ a lemezekhez, a rendszer automatikusan replikálja a pillanatképeket egy másodlagos adatközpontba. Ha helyileg redundáns tárterületet használ a lemezekhez, saját kezűleg kell replikálnia az adatot. További információ: [Azure-nem felügyelt virtuális gépek biztonsági mentése növekményes pillanatképekkel](windows/incremental-snapshots.md).

A pillanatkép egy objektum egy adott időpontban való ábrázolását jelöli. Egy pillanatkép az általa tárolt adatok növekményes méretének számlázására vonatkozik. További információt a blob- [pillanatkép létrehozása](../storage/blobs/snapshots-overview.md)című témakörben talál.

### <a name="create-snapshots-while-the-vm-is-running"></a>Pillanatképek létrehozása a virtuális gép futása közben

Bár a virtuális gép futása közben bármikor elkészítheti a pillanatképet, a rendszer továbbra is továbbít egy lemezt a lemezekre. A pillanatképek olyan részleges műveleteket is tartalmazhatnak, amelyek repülés közben voltak. Ha több lemez is van, akkor előfordulhat, hogy a különböző lemezek pillanatképei különböző időpontokban jelentkeztek. Ezek a forgatókönyvek azt eredményezhetik, hogy a pillanatképek nem lesznek összehangolva. Ez a koordináció hiánya különösen olyan csíkozott kötetek esetében fordul elő, amelyek sérültek lehetnek, ha a biztonsági mentés során módosultak a fájlok.

A probléma elkerülése érdekében a biztonsági mentési folyamatnak a következő lépéseket kell végrehajtania:

1.  Az összes lemez rögzítése.

1.  Az összes függőben lévő írás kiürítése

1.  [Hozzon létre egy blob-pillanatképet](../storage/blobs/snapshots-manage-dotnet.md) az összes lemezhez.

Egyes Windows-alkalmazások, például a SQL Serverek, az alkalmazás-konzisztens biztonsági másolatok létrehozásához egy mennyiségi árnyékmásolat-szolgáltatáson keresztül biztosítanak egy koordinált biztonsági mentési mechanizmust. Linux rendszeren olyan eszközt használhat, mint a *fsfreeze* a lemezek koordinálásához. Ez az eszköz fájl-konzisztens biztonsági mentéseket biztosít, de nem az alkalmazás-konzisztens pillanatképeket. Ez a folyamat összetett, ezért érdemes megfontolni [Azure Backup](../backup/backup-azure-vms-introduction.md) vagy egy harmadik féltől származó biztonsági mentési megoldás használatát, amely már implementálja ezt az eljárást.

Az előző folyamat az összes virtuálisgép-lemezre vonatkozóan koordinált Pillanatképek gyűjteményét eredményezi, amely a virtuális gép egy adott időponthoz tartozó nézetét jelöli. Ez a virtuális gép biztonsági másolatának visszaállítási pontja. Ismétlődő biztonsági másolatok létrehozásához ütemezett időközönként megismételheti a folyamatot. A pillanatképek egy másik régióba való másolásának lépéseiért lásd: [a biztonsági másolatok másolása másik régióba](#copy-the-snapshots-to-another-region) .

### <a name="create-snapshots-while-the-vm-is-offline"></a>Pillanatképek létrehozása, amikor a virtuális gép offline állapotban van

Egy másik lehetőség, hogy konzisztens biztonsági másolatokat hozzon létre a virtuális gép leállítására és az egyes lemezek blob-pillanatképének megadására. A blob-Pillanatképek könnyebben működnek, mint egy futó virtuális gép pillanatképének összehangolása, de néhány perc állásidőt igényel.

1. Állítsa le a virtuális gépet.

1. Hozzon létre egy pillanatképet minden egyes virtuális merevlemez-blobról, amely csak néhány másodpercet vesz igénybe.

    Pillanatkép létrehozásához használhatja a [PowerShellt](/powershell/module/az.storage), az [Azure Storage REST API](/rest/api/storageservices/Snapshot-Blob), az [Azure CLI](/cli/azure/)-t vagy az Azure Storage ügyféloldali kódtárait, például [a Storage ügyféloldali kódtárat a .net-hez](/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob).

1. Indítsa el a virtuális gépet, amely befejezi az állásidőt. Általában a teljes folyamat néhány percen belül befejeződik.

Ez a folyamat egységes Pillanatképek gyűjteményét eredményezi az összes lemezről, és a virtuális gép biztonsági mentési visszaállítási pontját biztosítja.

### <a name="copy-the-snapshots-to-another-region"></a>Pillanatképek másolása másik régióba

Előfordulhat, hogy a pillanatképek létrehozása önmagában nem elegendő a DR számára. A pillanatképek biztonsági másolatait is replikálni kell egy másik régióba.

Ha a lemezek esetében geo-redundáns tárolást vagy olvasási hozzáférésű geo-redundáns tárolót használ, a pillanatképeket a rendszer automatikusan replikálja a másodlagos régióba. A replikáció előtt néhány perc késést is igénybe vehet. Ha az elsődleges adatközpont leáll a pillanatképek replikálásának befejeződése előtt, a másodlagos adatközpontból nem férhet hozzá a pillanatképekhez. Ennek a valószínűsége kicsi.

> [!NOTE]
> Csak a Geo-redundáns tárolóban lévő lemezek, vagy az olvasási hozzáférésű geo-redundáns Storage-fiók nem biztosítja a virtuális gépeket a katasztrófák ellen. Emellett koordinált pillanatképeket is létre kell hoznia, vagy Azure Backup kell használnia. Ez a virtuális gép konzisztens állapotba való helyreállításához szükséges.

Ha helyileg redundáns tárolót használ, a pillanatképek létrehozása után azonnal át kell másolnia egy másik Storage-fiókba. A másolási cél lehet egy helyileg redundáns Storage-fiók egy másik régióban, így a másolás egy távoli régióban történik. A pillanatképet átmásolhatja egy olvasási hozzáférésű geo-redundáns Storage-fiókba is ugyanabban a régióban. Ebben az esetben a pillanatképet a rendszer lustán replikálja a távoli másodlagos régióba. A másolás és a replikáció befejezése után a biztonsági mentés az elsődleges helyen lévő katasztrófák ellen védett.

Az Azure-beli, nem [felügyelt virtuálisgép-lemezek biztonsági mentése növekményes pillanatképekkel](windows/incremental-snapshots.md)című témakör útmutatását követve részletesen átmásolhatja a Dr növekményes pillanatképeit.

![Azure-beli nem felügyelt VM-lemezek biztonsági mentése növekményes pillanatképekkel][2]

### <a name="recovery-from-snapshots"></a>Helyreállítás pillanatképekről

Pillanatkép lekéréséhez másolja azt egy új blob létrehozásához. Ha a pillanatképet az elsődleges fiókból másolja át, a pillanatképet átmásolhatja a pillanatkép alap blobba. Ez a folyamat visszaállít egy lemezt a pillanatképbe. Ezt a folyamatot nevezzük a pillanatkép népszerűsítésének. Ha egy másodlagos fiókból másolja a pillanatkép biztonsági mentését, egy olvasási hozzáférésű geo-redundáns Storage-fiók esetében át kell másolnia azt egy elsődleges fiókba. A pillanatképeket a [PowerShell](/powershell/module/az.storage) vagy a AzCopy segédprogram használatával másolhatja. További információ: [adatok átvitele a AzCopy parancssori segédprogrammal](../storage/common/storage-use-azcopy-v10.md).

Több lemezzel rendelkező virtuális gépek esetén az azonos koordinált visszaállítási pont részét képező összes pillanatképet át kell másolnia. A pillanatképek írható VHD-blobokra másolását követően a Blobokkal újból létrehozhatja a virtuális gépet a virtuális gép sablonjának használatával.

## <a name="other-options"></a>Egyéb lehetőségek

### <a name="sql-server"></a>SQL Server

A virtuális gépen futó SQL Server saját beépített képességekkel rendelkezik a SQL Server-adatbázis Azure Blob Storage-ba vagy fájlmegosztásba való biztonsági mentéséhez. Ha a Storage-fiók földrajzilag redundáns tárolást vagy olvasási hozzáférésű geo-redundáns tárolót használ, a biztonsági másolatok a Storage-fiók másodlagos adatközpontjában, a korábban tárgyalt korlátozásokkal érhetők el. További információ: [SQL Server biztonsági mentése és visszaállítása az Azure Virtual Machines szolgáltatásban](../azure-sql/virtual-machines/windows/azure-storage-sql-server-backup-restore-use.md). A biztonsági mentés és a visszaállítás mellett [SQL Server AlAlwaysOnon rendelkezésre állási csoportok](../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md) is kezelhetik az adatbázisok másodlagos replikáit. Ez a képesség nagy mértékben csökkenti a vész-helyreállítási időt.

## <a name="other-considerations"></a>További szempontok

Ebből a cikkből megtudhatta, hogyan készíthet biztonsági mentést a virtuális gépekről és a lemezekről a vész-helyreállítás támogatásáról, valamint arról, hogyan használhatja ezeket a biztonsági másolatokat vagy pillanatképeket az adatok helyreállításához. A Azure Resource Manager modellel sok ember használ sablonokat a virtuális gépek és egyéb infrastruktúra létrehozásához az Azure-ban. Sablon használatával létrehozhat egy virtuális gépet, amely minden alkalommal ugyanazzal a konfigurációval rendelkezik. Ha egyéni rendszerképeket használ a virtuális gépek létrehozásához, akkor azt is meg kell győződnie arról, hogy a lemezképek védelme egy olvasási hozzáférésű geo-redundáns Storage-fiók használatával történik.

Ennek következtében a biztonsági mentési folyamat két dolog kombinációja lehet:

- Biztonsági másolat készítése az adatlemezekről (lemezek).
- A konfiguráció biztonsági mentése (sablonok és egyéni lemezképek).

Az Ön által választott biztonsági mentési lehetőségtől függően előfordulhat, hogy a biztonsági mentést és a konfigurációt is kezelni kell, vagy a biztonsági mentési szolgáltatás az összes ilyen elemet kezelni tudja.

## <a name="appendix-understanding-the-impact-of-data-redundancy"></a>Függelék: az adatredundancia következményeinek megértése

Az Azure-beli Storage-fiókok esetében háromféle adatredundancia van, amelyeket érdemes figyelembe venni a vész-helyreállítással kapcsolatban: helyileg redundáns, Geo-redundáns vagy geo-redundáns olvasási hozzáféréssel. 

A helyileg redundáns tárolás megtartja az azonos adatközpontban található adat három példányát. Amikor a virtuális gép beírja az adatot, mindhárom másolat frissül, mielőtt a rendszer visszaadja a sikerességet a hívónak, így biztos lehet benne, hogy megegyeznek. A lemez védve van a helyi hibáktól, mert nem valószínű, hogy mindhárom példányra is hatással van. Helyileg redundáns tárolás esetén nincs szükség földrajzi redundanciára, így a lemez nem védett a teljes adatközpontot vagy tárolási egységet befolyásoló katasztrofális hibáktól.

A Geo-redundáns tárolással és az olvasási hozzáféréssel kapcsolatos geo-redundáns tárolással az adatai három másolata marad az Ön által kiválasztott elsődleges régióban. Az Azure által beállított másodlagos régióban az adat három további másolata is megmarad. Ha például az USA nyugati régiójában tárolja az adattárolást, az adatreplikáció az USA keleti régiójába történik. A másolási megőrzés aszinkron módon történik, és az elsődleges és a másodlagos helyek frissítései között kis késleltetés van. A másodlagos helyen lévő lemezek replikái konzisztensek a lemezen (a késéssel), de előfordulhat, hogy több aktív lemez replikái nem szinkronizálhatók egymással. Ahhoz, hogy konzisztens replikák legyenek több lemezen, konzisztens pillanatképekre van szükség.

A földrajzi redundáns tárolás és az olvasási hozzáférésű geo-redundáns tárolás között a fő különbség, hogy az olvasási hozzáférésű geo-redundáns tárolással bármikor elolvashatja a másodlagos másolatot. Ha olyan probléma merül fel, amely az elsődleges régióban nem érhető el, az Azure csapata mindent megtesz a hozzáférés helyreállításához. Amíg az elsődleges érték le van tiltva, ha engedélyezve van az olvasási hozzáférésű geo-redundáns tárterület, akkor a másodlagos adatközpontban is elérhetők az információk. Ezért ha úgy tervezi, hogy beolvassa a replikát, miközben az elsődleges nem érhető el, akkor a rendszer figyelembe veszi az olvasási hozzáférésű geo-redundáns tárolást.

Ha úgy tűnik, hogy jelentős leállás következik be, az Azure-csapat elindíthat egy földrajzi feladatátvételt, és az elsődleges DNS-bejegyzéseket módosíthatja úgy, hogy a másodlagos tárterületre mutasson. Ezen a ponton, ha a Geo-redundáns tárolás vagy az olvasási hozzáférésű geo-redundáns tárterület engedélyezve van, hozzáférhet a másodlagosként használt régióban található információkhoz. Más szóval, ha a Storage-fiók földrajzilag redundáns tárolást tartalmaz, és probléma van, akkor csak akkor férhet hozzá a másodlagos tárolóhoz, ha van földrajzi feladatátvétel.

További információk: [Mi a Mi a teendő az Azure Storage leállása esetén](../storage/common/storage-disaster-recovery-guidance.md).

## <a name="next-steps"></a>Következő lépések

Lásd: Azure-beli nem [felügyelt virtuálisgép-lemezek biztonsági mentése növekményes pillanatképekkel](linux/incremental-snapshots.md).

[1]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-1.png
[2]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-2.png