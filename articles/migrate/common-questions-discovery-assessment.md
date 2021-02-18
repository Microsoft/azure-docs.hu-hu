---
title: A felderítéssel, értékeléssel és függőségi elemzéssel kapcsolatos kérdések Azure Migrate
description: Választ kaphat a felderítéssel, értékeléssel és függőségi elemzéssel kapcsolatos gyakori kérdésekre Azure Migrateban.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/09/2020
ms.openlocfilehash: 40afa1d743b8d074fa46dde46163f6479ebf87c2
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100589079"
---
# <a name="discovery-assessment-and-dependency-analysis---common-questions"></a>Felderítés, értékelés és függőségek elemzése – gyakori kérdések

Ez a cikk a felderítéssel, értékeléssel és függőségi elemzéssel kapcsolatos gyakori kérdésekre ad választ a Azure Migrateban. Ha további kérdései vannak, tekintse meg a következő erőforrásokat:

- [Általános kérdések](resources-faq.md) a Azure Migrate
- Az [Azure Migrate berendezéssel](common-questions-appliance.md) kapcsolatos kérdések
- A [kiszolgálók áttelepítésével](common-questions-server-migration.md) kapcsolatos kérdések
- A [Azure Migrate fórumban](https://aka.ms/AzureMigrateForum) választ kaphat kérdéseire


## <a name="what-geographies-are-supported-for-discovery-and-assessment-with-azure-migrate"></a>Milyen földrajzi területek támogatottak a felderítéshez és az értékeléshez Azure Migrate?

Tekintse át a [nyilvános](migrate-support-matrix.md#supported-geographies-public-cloud) és a [Government-felhők](migrate-support-matrix.md#supported-geographies-azure-government) támogatott régióit.


## <a name="how-many-vms-can-i-discover-with-an-appliance"></a>Hány virtuális gépet tudok felderíteni egy berendezéssel?

Akár 10 000 VMware virtuális gépet, akár 5 000 Hyper-V virtuális gépet és akár 1000 fizikai kiszolgálót is felderítheti egyetlen készülék használatával. Ha több géppel rendelkezik, olvassa el a [Hyper-V felmérés skálázását](scale-hyper-v-assessment.md), [a VMware-értékelés méretezését](scale-vmware-assessment.md)vagy [a fizikai kiszolgáló értékelésének](scale-physical-assessment.md)méretezését ismertető cikkét.

## <a name="how-do-i-choose-the-assessment-type"></a>Hogyan választhatom ki az értékelési típust?

- Az **Azure-beli virtuális gépek felméréseit** az Azure-beli virtuális gépekre való áttelepítéshez használni kívánt helyszíni [VMWare virtuális gépek](how-to-set-up-appliance-vmware.md), [Hyper-V virtuális gépek](how-to-set-up-appliance-hyper-v.md)és [fizikai kiszolgálók](how-to-set-up-appliance-physical.md) értékeléséhez használhatja. [További információ](concepts-assessment-calculation.md)

- Ha a helyszíni [VMWare virtuális gépeket](how-to-set-up-appliance-vmware.md) szeretné felmérni az [Azure VMware-megoldásba (AVS)](../azure-vmware/introduction.md) való áttelepítéshez, használja az **Azure VMware Solution (AVS)** értékeléseit. [További információ](concepts-azure-vmware-solution-assessment-calculation.md)

- A VMware rendszerű gépekkel közös csoportot csak akkor használhat, ha mindkét típusú értékelést futtatni kívánja. Vegye figyelembe, hogy ha először futtat AVS-értékeléseket az Azure Migrate-ben, akkor érdemes létrehozni egy új csoportot VMware rendszerű gépekből.
 

## <a name="why-is-performance-data-missing-for-someall-vms-in-my-assessment-report"></a>Miért hiányoznak egyes virtuális gépek/az összes virtuális gép teljesítményadatai az értékelési jelentésből?

„Teljesítményalapú” értékelés esetén az értékelési jelentés exportálása PercentageOfCoresUtilizedMissing vagy PercentageOfMemoryUtilizedMissing hibát jelez, amikor az Azure Migrate-berendezés nem tud teljesítményadatokat gyűjteni a helyszíni virtuális gépekhez. Ellenőrizze a következőket:

- A virtuális gépek be vannak kapcsolva abban az időtartamban, amelyre az értékelést létrehozta
- Ha csak a teljesítményszámlálók hiányoznak, és a rendszer megkísérli felmérni a Hyper-V virtuális gépeket. Ebben a forgatókönyvben engedélyezze a dinamikus memóriát a virtuális gépeken, és "számítsa újra" az értékelést, hogy tükrözze a legújabb módosításokat. A készülék csak akkor gyűjthet memória-kihasználtsági értékeket a Hyper-V virtuális gépekhez, ha a virtuális gépnek engedélyezve van a dinamikus memória.

- Ha az összes teljesítményszámláló hiányzik, győződjön meg arról, hogy a 443-as (HTTPS) portok kimenő kapcsolatai engedélyezve vannak.

Megjegyezés – Ha bármely teljesítményszámláló hiányzik, az Azure Migrate: Server Assessment áttér a helyszíni lefoglalt magokra/memóriára és ennek megfelelően javasol virtuálisgép-méretet.

## <a name="why-is-the-confidence-rating-of-my-assessment-low"></a>Miért alacsony az értékelésem megbízhatósági minősítése?

A megbízhatósági minősítés az értékelés kiszámításához szükséges [elérhető adatpontok](./concepts-assessment-calculation.md#ratings) százalékán alapuló „Teljesítményalapú” értékelésekhez van kiszámítva. Alább láthatók azok az okok, amelyek miatt egy értékelés alacsony megbízhatósági minősítést kaphat:

- Nem végzett profilkészítést a környezeten abban az időtartamban, amelyre az értékelést létrehozta. Ha például egyhetes teljesítmény-időtartamú értékelést hoz létre, akkor a felderítés indítását követően legalább egy hetet várnia kell az összes adatpont összegyűjtésére. Ha nem tudja megvárni az időtartam végét, módosítsa a teljesítmény időtartamát egy kisebb időszakra, és számítsa újra az értékelést.
 
- A kiszolgáló értékelése nem tudja összegyűjteni az értékelési időszakban egy vagy több virtuális gép teljesítményadatait. Magas megbízhatósági minősítés esetén ügyeljen a következőre: 
    - A virtuális gépek az értékelés időtartamára vannak bekapcsolva
    - A 443-es portokon engedélyezett kimenő kapcsolatok engedélyezettek
    - Hyper-V virtuális gépek dinamikus memóriájának engedélyezése 

    Számítsa újra az értékelést, hogy tükrözze a megbízhatósági minősítés legújabb módosításait.

- Kevés virtuális gép lett létrehozva a felderítés indítását követően a Server Assessmentben. Ha például az utolsó egy hónap teljesítményelőzményeinek értékelését hozza létre, de néhány virtuális gép csak egy hete jött létre a környezetben. Ilyen esetekben az új virtuális gépek teljesítményadatai a teljes időtartamra vonatkozóan nem lesznek elérhetőek, és a megbízhatósági minősítés alacsony lesz.

[Itt tudhat meg többet](./concepts-assessment-calculation.md#confidence-ratings-performance-based) a megbízhatósági minősítésről.

## <a name="i-cant-see-some-groups-when-i-am-creating-an-azure-vmware-solution-avs-assessment"></a>Nem látok egyes csoportokat, amikor létrehozok egy Azure VMware-megoldás (AVS) felmérését

- Az AVS-értékelés olyan csoportokon hajtható végre, amelyekben csak VMware rendszerű gépek találhatók. Ha AVS-értékelést szeretne végezni, távolítson el minden nem VMware rendszerű gépet a csoportból.
- Ha először futtat AVS-értékeléseket az Azure Migrate-ben, akkor érdemes létrehozni egy új csoportot VMware rendszerű gépekből.

## <a name="i-cant-see-some-vm-types-in-azure-government"></a>Nem látok néhány virtuálisgép-típust Azure Government

Az értékeléshez és áttelepítéshez támogatott virtuálisgép-típusok Azure Government helyen rendelkezésre állástól függenek. Azure Governmentban [áttekintheti és összehasonlíthatja](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) a virtuális gépek típusait.

## <a name="the-size-of-my-vm-changed-can-i-run-an-assessment-again"></a>A virtuális gép mérete megváltozott. Újraértékelést is Futtathatok?

A Azure Migrate berendezés folyamatosan gyűjt információkat a helyszíni környezetről.  Az értékelés a helyszíni virtuális gépekre vonatkozó időponthoz kapcsolódó pillanatkép. Ha módosítja egy olyan virtuális gép beállításait, amelyet fel szeretne mérni, használja az újraszámítási lehetőséget az értékelés frissítéséhez a legújabb módosításokkal.

## <a name="how-do-i-discover-vms-in-a-multitenant-environment"></a>Hogyan a virtuális gépek felderítését több-bérlős környezetben?

- **VMware**: Ha egy környezet megosztott a bérlők között, és nem szeretné felderíteni a bérlő virtuális gépei egy másik bérlő előfizetésében, hozzon létre VMware vCenter Server hitelesítő adatokat, amelyek csak a felderíteni kívánt virtuális gépekhez férhetnek hozzá. Ezután használja ezeket a hitelesítő adatokat, amikor elindítja a felderítést a Azure Migrate berendezésben.
- **Hyper-v**: a felderítés a Hyper-v gazdagép hitelesítő adatait használja. Ha a virtuális gépek ugyanazt a Hyper-V-gazdagépet használják, jelenleg nincs lehetőség a felderítés elválasztására.  

## <a name="do-i-need-vcenter-server"></a>Szükségem van vCenter Serverre?

Igen, Azure Migrate a felderítés végrehajtásához VMware-környezetben vCenter Server szükséges. A Azure Migrate nem támogatja a vCenter Server által nem felügyelt ESXi-gazdagépek felderítését.

## <a name="what-are-the-sizing-options-in-an-azure-vm-assessment"></a>Mik a méretezési lehetőségek az Azure-beli virtuális gépek felmérésében?

Helyszíni méretezés esetén a Azure Migrate nem veszi figyelembe a virtuális gépek teljesítményadatait az értékeléshez. Azure Migrate a virtuális gépek méretét a helyszíni konfiguráció alapján méri. A teljesítmény-alapú méretezéssel a méretezés a kihasználtsági adatain alapul.

Ha például egy helyszíni virtuális gép négy maggal és 8 GB memóriával rendelkezik, 50%-os CPU-kihasználtság és 50% memória kihasználtsága:
- A helyszíni méretezés olyan Azure VM SKU-t javasol, amely négy maggal és 8 GB memóriával rendelkezik.
- A teljesítmény-alapú méretezési szolgáltatás olyan virtuálisgép-SKU-t javasol, amely két maggal és 4 GB memóriával rendelkezik, mert a kihasználtság százalékos arányát veszi figyelembe.

Hasonlóképpen, a lemezek méretezése a méretezési feltételektől és a tárolási típustól függ:
- Ha a méretezési feltétel teljesítmény-alapú, és a tárolási típus automatikus, Azure Migrate a lemez IOPS és átviteli értékeit veszi figyelembe, amikor azonosítja a céllemez típusát (standard vagy prémium).
- Ha a méretezési feltétel teljesítmény-alapú, és a tárolási típus prémium, Azure Migrate a helyszíni lemez méretétől függően a prémium szintű lemezes SKU-t javasolja. Ugyanez a logikát alkalmazza a lemez méretezésére, ha a méretezés a helyszínen történik, és a tárolási típus a standard vagy a prémium.

## <a name="does-performance-history-and-utilization-affect-sizing-in-an-azure-vm-assessment"></a>Befolyásolja a teljesítmény előzményeit és kihasználtságát az Azure-beli virtuális gépek értékelésében?

Igen, a teljesítmény előzményei és kihasználtsága befolyásolja az Azure-beli virtuális gépek értékelésének méretezését.

### <a name="performance-history"></a>Teljesítményelőzmények

A csak teljesítmény-alapú méretezéshez Azure Migrate gyűjti a helyszíni gépek teljesítményének előzményeit, majd az Azure-ban a virtuális gép méretének és a lemez típusának a használatát javasolja:

1. A készülék folyamatosan a helyszíni környezetet gyűjti a valós idejű kihasználtsági adatok gyűjtésére 20 másodpercenként.
2. A készülék összesíti az összegyűjtött 20 másodperces mintákat, és a használatával 15 percenként egyetlen adatpontot hoz létre.
3. Az adatpont létrehozásához a készülék az összes 20 másodperces mintából kiválasztja a csúcsérték értékét.
4. A készülék elküldi az adatpontot az Azure-nak.

### <a name="utilization"></a>Kihasználtság

Ha az Azure-ban hoz létre értékelést, a teljesítmény időtartamától és a beállított teljesítménybeli százalékos értéktől függően a Azure Migrate kiszámítja a tényleges kihasználtság értékét, majd ezt használja a méretezéshez.

Ha például a teljesítmény időtartamát egy napra állítja be, és a percentilis értéke 95. percentilis értékre van állítva, akkor a Azure Migrate a gyűjtő által az elmúlt napra eljuttatott 15 perces mintavételi pontokat növekvő sorrendbe rendezi. A 95. percentilis értéket a tényleges kihasználtságként választja.

A 95. percentilis értékének használata biztosítja, hogy a rendszer figyelmen kívül hagyja a kiugró értékeket. A kiugró értékek akkor is szerepelhetnek, ha a Azure Migrate a esetek 99% percentilis-t használja. Ha ki szeretné választani az időszak maximális kihasználtságát anélkül, hogy a kiugró értékek hiányoznak, állítsa be Azure Migrate a esetek 99% percentilis használatára.


## <a name="how-are-import-based-assessments-different-from-assessments-with-discovery-source-as-appliance"></a>Miben különböznek az importálási alapú értékelések a felderítési forrásokkal, mint a készülék?

Az importálási alapú Azure-beli VM-értékelések olyan, a Azure Migrateba importált gépekkel létrehozott értékelések, amelyek CSV-fájl használatával lettek importálva. Csak négy mező importálására van kötelező: kiszolgálónév, magok, memória és operációs rendszer. Néhány Tudnivaló: 
 - A készültségi feltételek kevésbé szigorúak a rendszerindítási típus paraméterének importálási alapú értékelésében. Ha a rendszerindítás típusa nincs megadva, a rendszer feltételezi, hogy a gép BIOS-rendszerindítási típussal rendelkezik, és a gép nem **feltételesen készként** van megjelölve. A felderítési forrásként készülékként végzett értékelésekben a készenléti **állapot feltételként** van megjelölve, ha hiányzik a rendszerindítási típus. Ez a készültségi számításban szereplő különbség azért van, mert a felhasználók nem rendelkezhetnek az áttelepítés megtervezésének korai szakaszaiban lévő összes információval az importálási felmérések elvégzése során. 
 - A teljesítmény-alapú importálási felmérések a felhasználó által megadott kihasználtsági értéket használják a jobb méretezési számításokhoz. Mivel a felhasználó a kihasználtság értékét megadja, a **teljesítmény** -és a percentilis- **kihasználtsági** beállítások le vannak tiltva az értékelés tulajdonságaiban. A felderítési forrásként készülékként elvégzett értékelésekben a kiválasztott percentilis-érték a készülék által gyűjtött teljesítményadatok közül lesz kiválasztva.

## <a name="why-is-the-suggested-migration-tool-in-import-based-avs-assessment-marked-as-unknown"></a>Miért van a javasolt áttelepítési eszköz az importálási alapú AVS-felmérésben ismeretlenként megjelölve?

CSV-fájlon keresztül importált gépek esetén az AVS-felmérésben az alapértelmezett áttelepítési eszköz ismeretlen. A VMware-gépek esetében azonban ajánlott a VMware Hybrid Cloud Extension (HCX) megoldás használata. [További információk](../azure-vmware/tutorial-deploy-vmware-hcx.md).


## <a name="what-is-dependency-visualization"></a>Mi a függőségi vizualizáció?

A függőségi vizualizáció segíthet felmérni a virtuális gépek csoportjait, hogy nagyobb megbízhatósággal legyenek áttelepítve. A függőségi vizualizációk átvizsgálják a számítógép függőségeit az értékelés futtatása előtt. Így biztosítható, hogy semmi sincs hátra, és segít elkerülni a váratlan kimaradásokat az Azure-ba való Migrálás során. Azure Migrate a függőségi vizualizáció engedélyezéséhez a Azure Monitor Service Map megoldását használja. [További információ](concepts-dependency-visualization.md).

> [!NOTE]
> Az ügynök-alapú függőség elemzése nem érhető el Azure Governmentban. Az ügynök nélküli függőségek elemzését is használhatja

## <a name="whats-the-difference-between-agent-based-and-agentless"></a>Mi a különbség az ügynök-alapú és az ügynök nélkül?

Az ügynök nélküli vizualizáció és az ügynök-alapú vizualizáció közötti különbségeket a táblázat foglalja össze.

**Követelmény** | **Ügynök nélküli** | **Ügynök-alapú**
--- | --- | ---
Támogatás | Ez a beállítás jelenleg előzetes verzióban érhető el, és csak a VMware virtuális gépekhez használható. [Tekintse át](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) a támogatott operációs rendszereket. | Általánosan elérhető (GA).
Ügynök | Nem kell telepítenie az ügynököket az áttekinteni kívánt gépekre. | Az elemezni kívánt helyszíni gépekre telepítendő ügynökök: a [Microsoft monitoring Agent (MMA)](../azure-monitor/agents/agent-windows.md)és a [függőségi ügynök](../azure-monitor/agents/agents-overview.md#dependency-agent). 
Előfeltételek | [Tekintse át](concepts-dependency-visualization.md#agentless-analysis) az előfeltételeket és az üzembe helyezésre vonatkozó követelményeket. | [Tekintse át](concepts-dependency-visualization.md#agent-based-analysis) az előfeltételeket és az üzembe helyezésre vonatkozó követelményeket.
Log Analytics | Nem szükségesek. | A Azure Migrate a [Service Map](../azure-monitor/vm/service-map.md) megoldást használja [Azure monitor naplókban](../azure-monitor/logs/log-query-overview.md) a függőségi vizualizációhoz. [További információ](concepts-dependency-visualization.md#agent-based-analysis).
Működés | Rögzíti a TCP-kapcsolatok mennyiségét a függőségi vizualizációhoz engedélyezett gépeken. A felderítést követően öt percen belül gyűjti az adatokat. | A gépen telepített Service Map ügynökök gyűjtenek adatokat a TCP-folyamatokról és a bejövő/kimenő kapcsolatokról az egyes folyamatokhoz.
Adatok | Forrásoldali gép kiszolgálójának neve, folyamata, alkalmazás neve.<br/><br/> Célszámítógép kiszolgálójának neve, folyamat, alkalmazás neve és port. | Forrásoldali gép kiszolgálójának neve, folyamata, alkalmazás neve.<br/><br/> Célszámítógép kiszolgálójának neve, folyamat, alkalmazás neve és port.<br/><br/> A kapcsolatok száma, a késés és az adatátviteli adatok összegyűjtése és Log Analytics lekérdezések számára elérhetők. 
Vizualizáció | Az önálló kiszolgálók függőségi térképe egy óra és 30 nap közötti időtartamon belül megtekinthető. | Egyetlen kiszolgáló függőségi térképe.<br/><br/> A Térkép csak egy órán át jeleníthető meg.<br/><br/> Kiszolgálók egy csoportjának függőségi térképe.<br/><br/> Kiszolgálók hozzáadása és eltávolítása a Térkép nézetből.
Adatexportálás | Az elmúlt 30 nap során az adatletöltés CSV formátumban is letölthető. | Az adatlekérdezés a Log Analytics használatával kérdezhető le.


## <a name="do-i-need-to-deploy-the-appliance-for-agentless-dependency-analysis"></a>Telepíteni kell a készüléket az ügynök nélküli függőségek elemzéséhez?

Igen, a [Azure Migrate készüléket](migrate-appliance.md) telepíteni kell.

## <a name="do-i-pay-for-dependency-visualization"></a>Fizetnem kell a függőségi vizualizációért?

Nem. További információ a [Azure Migrate díjszabásáról](https://azure.microsoft.com/pricing/details/azure-migrate/).

## <a name="what-do-i-install-for-agent-based-dependency-visualization"></a>Mit kell telepíteni az ügynök-alapú függőségi vizualizációhoz?

Az ügynök-alapú függőségi vizualizációk használatához töltse le és telepítse az ügynököket minden olyan helyszíni gépen, amelyet ki szeretne értékelni:

- [Microsoft monitoring Agent (MMA)](../azure-monitor/agents/agent-windows.md)
- [Függőségi ügynök](../azure-monitor/agents/agents-overview.md#dependency-agent)
- Ha olyan gépekkel rendelkezik, amelyeknek nincs internetkapcsolata, töltse le és telepítse a Log Analytics átjárót rajtuk.

Ezeket az ügynököket csak akkor kell használni, ha ügynök-alapú függőségi vizualizációt használ.

## <a name="can-i-use-an-existing-workspace"></a>Használhatok egy meglévő munkaterületet?

Igen, az ügynök-alapú függőségi vizualizációhoz csatolhat egy meglévő munkaterületet az áttelepítési projekthez, és használhatja azt a függőségi vizualizációhoz. 

## <a name="can-i-export-the-dependency-visualization-report"></a>Exportálhatom a függőség vizualizációjáról szóló jelentést?

Nem, az ügynök-alapú vizualizációban nem lehet exportálni a függőség vizualizációs jelentését. A Azure Migrate azonban Service Mapt használ, és az [Service Map REST API](/rest/api/servicemap/machines/listconnections) használatával kérheti le a függőségeket JSON formátumban.

## <a name="can-i-automate-agent-installation"></a>Automatizálható az ügynök telepítése?

Ügynök-alapú függőségi vizualizáció esetén:

- Parancsfájl használatával [telepítse a függőségi ügynököt](../azure-monitor/vm/vminsights-enable-hybrid.md#dependency-agent).
- Az MMA esetében [használja a parancssort vagy az automationt](../azure-monitor/agents/log-analytics-agent.md#installation-options), vagy használjon [parancsfájlt](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).
- A parancsfájlok mellett olyan központi telepítési eszközöket is használhat, mint például a Microsoft Endpoint Configuration Manager és a [Intigua](https://www.intigua.com/intigua-for-azure-migration) az ügynökök üzembe helyezéséhez.

## <a name="what-operating-systems-does-mma-support"></a>Milyen operációs rendszereket támogat az MMA?

- Tekintse meg az [MMA által támogatott Windows operációs rendszerek](../azure-monitor/agents/log-analytics-agent.md#installation-options)listáját.
- Megtekintheti az [MMA által támogatott Linux operációs rendszerek](../azure-monitor/agents/log-analytics-agent.md#installation-options)listáját.

## <a name="can-i-visualize-dependencies-for-more-than-one-hour"></a>Megjeleníthető több mint egy órányi függőség?

Az ügynök-alapú vizualizációk esetében akár egy óráig is megjelenítheti a függőségeket. Akár egy hónapig is visszatérhet egy adott dátumra az előzményekben, de a vizualizációk maximális időtartama egy óra. Használhatja például az időtartamot a függőségi térképen a tegnapi függőségek megtekintéséhez, de a függőségeket csak egy órás időszakra lehet megtekinteni. Azonban Azure Monitor naplók használatával hosszabb időtartamra [kérdezheti le a függőségi adatmennyiséget](./how-to-create-group-machine-dependencies.md) .

Az ügynök nélküli vizualizációk esetében egy adott kiszolgáló függőségi térképét egy óra és 30 nap közötti időtartamon belül tekintheti meg.

## <a name="can-i-visualize-dependencies-for-groups-of-more-than-10-vms"></a>Megjeleníthető a több mint 10 virtuális gépen lévő csoportok függőségei?

Megjelenítheti a [függőségeket](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping) a legfeljebb 10 virtuális géppel rendelkező csoportok esetében. Ha több mint 10 virtuális géppel rendelkező csoporttal rendelkezik, javasoljuk, hogy ossza szét a csoportot kisebb csoportokba, majd jelenítse meg a függőségeket.

## <a name="next-steps"></a>Következő lépések

Olvassa el a [Azure Migrate áttekintést](migrate-services-overview.md).