---
title: A felderítéssel, értékeléssel és függőségi elemzéssel kapcsolatos kérdések Azure Migrate
description: Választ kaphat a felderítéssel, értékeléssel és függőségi elemzéssel kapcsolatos gyakori kérdésekre Azure Migrateban.
author: rashijoshi
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/09/2020
ms.openlocfilehash: c10963d28e0d2ecee73150e8b5af89cee96d28b2
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077006"
---
# <a name="discovery-assessment-and-dependency-analysis---common-questions"></a>Felderítés, értékelés és függőségek elemzése – gyakori kérdések

Ez a cikk a felderítéssel, értékeléssel és függőségi elemzéssel kapcsolatos gyakori kérdésekre ad választ a Azure Migrateban. Ha további kérdései vannak, tekintse meg a következő erőforrásokat:

- [Általános kérdések](resources-faq.md) a Azure Migrate
- Az [Azure Migrate berendezéssel](common-questions-appliance.md) kapcsolatos kérdések
- A [kiszolgálók áttelepítésével](common-questions-server-migration.md) kapcsolatos kérdések
- A [Azure Migrate fórumban](https://aka.ms/AzureMigrateForum) választ kaphat kérdéseire


## <a name="what-geographies-are-supported-for-discovery-and-assessment-with-azure-migrate"></a>Milyen földrajzi területek támogatottak a felderítéshez és az értékeléshez Azure Migrate?

Tekintse át a [nyilvános](migrate-support-matrix.md#supported-geographies-public-cloud) és a [Government-felhők](migrate-support-matrix.md#supported-geographies-azure-government) támogatott régióit.


## <a name="how-many-servers-can-i-discover-with-an-appliance"></a>Hány kiszolgálót tud felderíteni egy berendezéssel?

Akár 10 000-kiszolgálót is felderítheti VMware-környezetből, akár 5 000-kiszolgálóra a Hyper-V-környezetből, akár 1000 fizikai kiszolgálókkal egyetlen készülék használatával. Ha több kiszolgálóval rendelkezik, olvassa el a [Hyper-V felmérés skálázását](scale-hyper-v-assessment.md), [a VMware-értékelés méretezését](scale-vmware-assessment.md)vagy [a fizikai kiszolgáló értékelésének](scale-physical-assessment.md)méretezését ismertető cikkét.

## <a name="how-do-i-choose-the-assessment-type"></a>Hogyan választhatom ki az értékelési típust?

- Az **Azure-beli virtuális gépek felméréseit** akkor érdemes használni, ha a helyszíni [VMware](how-to-set-up-appliance-vmware.md) -és [Hyper-V-](how-to-set-up-appliance-hyper-v.md) környezetből származó kiszolgálókat kíván felmérni, valamint [fizikai kiszolgálókat](how-to-set-up-appliance-physical.md) szeretne áttelepíteni az Azure-beli virtuális gépekre [További információ](concepts-assessment-calculation.md)

- Használja az értékelés típusát az **Azure SQL** -ben, ha a VMware-környezetből szeretné felmérni a helyszíni SQL Server a Azure SQL Database vagy az Azure SQL felügyelt példányára való áttelepítéshez. [További információ](concepts-assessment-calculation.md)

- Ha a helyszíni [VMWare virtuális gépeket](how-to-set-up-appliance-vmware.md) szeretné felmérni az [Azure VMware-megoldásba (AVS)](../azure-vmware/introduction.md) való áttelepítéshez, használja az **Azure VMware Solution (AVS)** értékeléseit. [További információ](concepts-azure-vmware-solution-assessment-calculation.md)

- A VMware rendszerű gépekkel közös csoportot csak akkor használhat, ha mindkét típusú értékelést futtatni kívánja. Vegye figyelembe, hogy ha először futtat AVS-értékeléseket az Azure Migrate-ben, akkor érdemes létrehozni egy új csoportot VMware rendszerű gépekből.
 

## <a name="why-is-performance-data-missing-for-someall-servers-in-my-azure-vm-andor-avs-assessment-report"></a>Miért hiányoznak a teljesítményadatok az Azure-beli virtuális gépen és/vagy az AVS Assessment-jelentésben szereplő egyes/összes kiszolgálókon?

A "teljesítmény-alapú" felmérés esetében az értékelő jelentés exportálása "PercentageOfCoresUtilizedMissing" vagy "PercentageOfMemoryUtilizedMissing", ha a Azure Migrate készülék nem tud teljesítményadatokat gyűjteni a helyszíni kiszolgálók számára. Ellenőrizze a következőket:

- Ha a kiszolgálók azon időtartamra vannak bekapcsolva, amelyhez az értékelést létrehozza
- Ha csak a teljesítményszámlálók hiányoznak, és a kiszolgálókat szeretné felmérni a Hyper-V környezetben. Ebben a forgatókönyvben engedélyezze a dinamikus memóriát a kiszolgálókon, és "számítsa újra" az értékelést, hogy tükrözze a legújabb módosításokat. A készülék csak akkor gyűjthet memória-kihasználtsági értékeket a kiszolgálókon a Hyper-V környezetben, ha a kiszolgálón engedélyezve van a dinamikus memória.

- Ha az összes teljesítményszámláló hiányzik, győződjön meg arról, hogy a 443-as (HTTPS) portok kimenő kapcsolatai engedélyezve vannak.

    > [!Note]
    > Ha a teljesítményszámlálók bármelyike hiányzik, Azure Migrate: a kiszolgáló értékelése visszakerül a helyszínen lefoglalt magokra/memóriára, és ennek megfelelően javasolja a virtuális gép méretét.


## <a name="why-is-performance-data-missing-for-someall-sql-instancesdatabases-in-my-azure-sql-assessment"></a>Miért hiányoznak a teljesítményadatok az Azure SQL Assessment egyes/összes SQL-példányaihoz/adatbázisaihoz?

A teljesítményadatok gyűjtésének biztosításához győződjön meg az alábbiakról:

- Ha az SQL-kiszolgálók be vannak kapcsolva azon időtartamra, amelyhez az értékelést létrehozza
- Ha a Azure Migrateban található SQL-ügynök kapcsolati állapota "Connected", és ellenőrizze az utolsó szívverést 
- Ha Azure Migrate az összes SQL-példány kapcsolati állapota "Connected" az észlelt SQL-példány panelen
- Ha az összes teljesítményszámláló hiányzik, győződjön meg arról, hogy a 443-as (HTTPS-) portokon engedélyezettek a kimenő kapcsolatok

Ha a teljesítményszámlálók bármelyike hiányzik, az Azure SQL Assessment az adott példány/adatbázis legkisebb Azure SQL-konfigurációját javasolja.

## <a name="why-is-the-confidence-rating-of-my-assessment-low"></a>Miért alacsony az értékelésem megbízhatósági minősítése?

A megbízhatósági minősítés az értékelés kiszámításához szükséges [elérhető adatpontok](./concepts-assessment-calculation.md#ratings) százalékán alapuló „Teljesítményalapú” értékelésekhez van kiszámítva. Alább láthatók azok az okok, amelyek miatt egy értékelés alacsony megbízhatósági minősítést kaphat:

- Nem végzett profilkészítést a környezeten abban az időtartamban, amelyre az értékelést létrehozta. Ha például egyhetes teljesítmény-időtartamú értékelést hoz létre, akkor a felderítés indítását követően legalább egy hetet várnia kell az összes adatpont összegyűjtésére. Ha nem tudja megvárni az időtartamot, módosítsa a teljesítmény időtartamát egy kisebb időszakra, és **számítsa ki** újra az értékelést.
 
- Az értékelés nem tud teljesítményadatokat gyűjteni az értékelési időszakban néhány kiszolgálóról vagy egyik kiszolgálóról sem. Magas megbízhatósági minősítés esetén ügyeljen a következőre: 
    - A kiszolgálók az értékelés időtartamára vannak bekapcsolva
    - A 443-es portokon engedélyezett kimenő kapcsolatok engedélyezettek
    - Hyper-V kiszolgálók esetén a dinamikus memória engedélyezve van 
    - A Azure Migrate lévő ügynökök kapcsolati állapota "Connected", és az utolsó szívverést vizsgálja.
    - Az Azure SQL-értékelések esetében Azure Migrate kapcsolati állapota minden SQL-példányhoz "csatlakoztatva" a felderített SQL-példány panelen

    **Számítsa újra** az értékelést, hogy tükrözze a megbízhatósági minősítés legújabb módosításait.

- Az Azure-beli virtuális gépek és az AVS-értékelések esetében kevés kiszolgáló jött létre a felderítés elindítása után. Ha például az elmúlt egy hónap teljesítmény-előzményeire vonatkozó értékelést hoz létre, néhány kiszolgálót azonban csak egy héttel ezelőtt hoztak létre a környezetben. Ebben az esetben az új kiszolgálókhoz tartozó teljesítményadatok nem lesznek elérhetők a teljes időtartamra, és a megbízhatósági minősítés alacsony lenne. [További információ](./concepts-assessment-calculation.md#confidence-ratings-performance-based)

- Azure SQL-értékelések esetén néhány SQL-példány vagy -adatbázis a felderítés elindítása után jött létre. Ha például az utolsó egy hónap teljesítmény-előzményeire vonatkozó értékelést hoz létre, néhány SQL-példány vagy-adatbázis csak egy héttel ezelőtt jött létre a környezetben. Ebben az esetben az új kiszolgálókhoz tartozó teljesítményadatok nem lesznek elérhetők a teljes időtartamra, és a megbízhatósági minősítés alacsony lenne. [További információ](./concepts-azure-sql-assessment-calculation.md#confidence-ratings)

## <a name="-the-number-of-azure-vm-or-avs-assessments-on-the-discovery-and-assessment-tool-are-incorrect"></a>> az Azure-beli virtuális gép vagy az AVS-értékelések száma a felderítési és értékelési eszközön helytelen
 A probléma megoldásához kattintson a felmérések teljes számára, és válassza ki az összes értékelést, és számítsa ki újra az Azure-beli virtuális gépet vagy az AVS-értékelést. A felderítési és értékelési eszköz ezután megjeleníti az értékelés típusának helyes darabszámát.


## <a name="i-want-to-try-out-the-new-azure-sql-assessment"></a>Szeretném kipróbálni az új Azure SQL Assessment-értékelést
A VMware-környezetben futó SQL Server példányok és adatbázisok felderítése és értékelése már előzetes verzióban érhető el. Ismerkedjen meg az [oktatóanyaggal](tutorial-discover-vmware.md). Ha egy meglévő projektben szeretné kipróbálni ezt a funkciót, győződjön meg arról, hogy végrehajtotta a jelen cikkben ismertetett [előfeltételeket](how-to-discover-sql-existing-project.md) .

## <a name="i-cant-see-some-servers-when-i-am-creating-an-azure-sql-assessment"></a>Nem látok egyes kiszolgálókat, amikor létrehozok egy Azure SQL-értékelést

- Az Azure SQL-értékelés csak olyan futó kiszolgálókon végezhető el, amelyeken SQL-példányok lettek felderítve. Ha nem látja az értékelni kívánt kiszolgálókat és SQL-példányokat, várjon egy ideig, amíg a felderítés befejeződik, majd hozza létre az értékelést. 
- Ha nem látja a korábban létrehozott csoportot az értékelés létrehozásakor, távolítsa el a nem VMware-kiszolgálókat vagy a csoportból SQL-példány nélküli kiszolgálót.
- Ha először futtat Azure SQL-értékeléseket az Azure Migrate-ben, akkor érdemes létrehozni egy új kiszolgálócsoportot.

## <a name="i-want-to-understand-how-was-the-readiness-for-my-instance-computed"></a>Szeretném megérteni, hogy a példányok készültsége hogyan lett kiszámítva?
Az SQL-példányok kompatibilitásának számítása a cél Azure SQL-környezettípus (Azure SQL-adatbázis vagy felügyelt Azure SQL-példány) funkciókompatibilitási ellenőrzésének a végrehajtása után történik. [További információ](./concepts-azure-sql-assessment-calculation.md#calculate-readiness)

## <a name="why-is-the-readiness-for-all-my-sql-instances-marked-as-unknown"></a>Miért van az összes ismeretlenként megjelölt SQL-példány készültsége?
Ha a felderítés a közelmúltban indult el, és még folyamatban van, előfordulhat, hogy az SQL-példányok készültsége ismeretlen. Javasoljuk, hogy várjon egy kis ideig, amíg a berendezés profilt készít a környezetről, majd számítsa újra az értékelést.
A rendszer 24 óránként egyszer hajtja végre az SQL-felderítést, és előfordulhat, hogy várnia kell egy napig, amíg a legújabb konfigurációs változások megjelennek. 

## <a name="why-is-the-readiness-for-some-of-my-sql-instances-marked-as-unknown"></a>Miért van felkészültsége az ismeretlennek jelölt SQL-példányok közül?
Ez akkor fordulhat elő, ha: 
- A felderítés még folyamatban van. Javasoljuk, hogy várjon egy kis ideig, amíg a berendezés profilt készít a környezetről, majd számítsa újra az értékelést.
- Vannak olyan felderítési problémák, amelyeket el kell hárítania a hibákat és értesítéseket tartalmazó panelen.

A rendszer 24 óránként egyszer hajtja végre az SQL-felderítést, és előfordulhat, hogy várnia kell egy napig, amíg a legújabb konfigurációs változások megjelennek.

## <a name="my-assessment-is-in-outdated-state"></a>Az értékelésem állapota Elavult

### <a name="azure-vmavs-assessment"></a>Azure-beli virtuális gép/AVS-Értékelés
Ha helyszíni változások vannak olyan kiszolgálókon, amelyek egy értékelt csoportban vannak, az értékelés elavultként van megjelölve. Az alábbi tulajdonságok egy vagy több módosítása miatt az értékelés "elavultként" jelölhető meg:
- Processzor-magok száma
- Lefoglalt memória
- Rendszerindítási típus vagy belső vezérlőprogram
- Operációs rendszer neve, verziója és architektúrája
- Lemezek száma
- Hálózati adapterek száma
- Lemez méretének változása (GB lefoglalt)
- A hálózati adapter tulajdonságainak frissítése. Például: Mac-cím módosítása, IP-cím hozzáadása stb.

**Számítsa ki** újra az értékelést, hogy tükrözze az értékelés legújabb változásait.

### <a name="azure-sql-assessment"></a>Azure SQL-értékelés
Ha módosítanak olyan helyszíni SQL-példányokat és -adatbázisokat, amelyek egy értékelt csoportban találhatók, az értékelést **elavultként** jelöli meg a rendszer:
- SQL-példányt adtak hozzá egy kiszolgálóhoz vagy távolítottak el róla
- SQL-adatbázist adtak hozzá egy SQL-példányhoz vagy távolítottak el róla
- Egy SQL-példány teljes adatbázismérete több mint 20%-kal változott
- Processzor-magok és/vagy lefoglalt memória számának módosítása

**Számítsa ki** újra az értékelést, hogy tükrözze az értékelés legújabb változásait.

## <a name="why-was-i-recommended-a-particular-target-deployment-type"></a>Miért ajánlottak számomra egy bizonyos cél üzembehelyezési típust?
Az Azure Migrate olyan Azure SQL üzembehelyezési típust javasol, amely kompatibilis az SQL-példánnyal. A Microsoft által ajánlott célra való migrálás lerövidíti a teljes migrálási folyamatot. Ezt az Azure SQL-konfigurációt (termékváltozatot) ajánljuk az SQL-példány és az általa kezelt adatbázisok teljesítménybeli jellemzőinek felmérése után. Ha több Azure SQL-konfiguráció is megfelelő, azt javasoljuk, amelyik a leginkább költséghatékony. [További információ](./concepts-azure-sql-assessment-calculation.md#calculate-sizing)

## <a name="what-deployment-target-should-i-choose-if-my-sql-instance-is-ready-for-azure-sql-db-and-azure-sql-mi"></a>Milyen üzembehelyezési célt válasszak, ha az SQL-példányom kompatibilis az Azure SQL-adatbázissal és a felügyelt Azure SQL-példánnyal is? 
Ha a példány az Azure SQL-adatbázissal és a felügyelt Azure SQL-példánnyal egyaránt kompatibilis, célként azt az üzembehelyezési típust javasoljuk, amely esetén az Azure SQL-konfiguráció becsült költsége alacsonyabb.

## <a name="why-is-my-instance-marked-as-potentially-ready-for-azure-vm-in-my-azure-sql-assessment"></a>Miért van az Azure-beli virtuális gépre felkészült példányom?
Ez akkor fordulhat elő, ha az értékelés tulajdonságai között kiválasztott cél üzembehelyezési típus értéke **Ajánlott**, és az SQL-példány nem kompatibilis az Azure SQL-adatbázissal és felügyelt Azure SQL-példánnyal. A felhasználónak azt javasoljuk, hogy hozzon létre egy értékelést az Azure Migrate-ben **Azure VM** értékelési típussal annak meghatározásához, hogy a példányt futtató kiszolgáló migrálható-e Azure-beli virtuális gépre.
A felhasználónak javasoljuk, hogy hozzon létre egy értékelést Azure Migrate az értékelés típusaként Azure-beli **virtuális gépet** annak megállapításához, hogy a példányt futtató kiszolgáló készen áll-e az Azure-beli virtuális gépekre való áttelepítésre:
- Az Azure-beli virtuális gépek felmérései jelenleg a Azure Migrateban vannak, és nem veszik figyelembe az SQL-példányok és-adatbázisok Azure-beli virtuális gépen való futtatásának adott teljesítmény-mérőszámait. 
- Amikor egy Azure-beli virtuális gépre irányuló értékelését egy kiszolgálón futtat, az ajánlott méret és a költségbecslések a kiszolgálón futó összes példányra vonatkoznak, és a példányok a Kiszolgáló migrálása eszközzel migrálhatók Azure-beli virtuális gépre. A migrálás előtt [tekintse át a teljesítményre vonatkozó irányelveket](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md) az Azure-beli virtuális gépeken futó SQL Serverhez kapcsolódóan.

## <a name="i-cant-see-some-databases-in-my-assessment-even-though-the-instance-is-part-of-the-assessment"></a>Nem látok néhány adatbázist az értékelésben, pedig a példány az értékelés részét képezi

Az Azure SQL-értékelés csak az online állapotú adatbázisokat tartalmazza. Ha az adatbázisok bármilyen más állapotban vannak, az értékelés figyelmen kívül hagyja a kompatibilitás-, méretezési és költségszámítását az ilyen adatbázisokra vonatkozóan. Ha szeretné értékelni ezeket az adatbázisokat, módosítsa az adatbázis állapotát, és számítsa újra az értékelést kis idő elteltével.

## <a name="i-want-to-compare-costs-for-running-my-sql-instances-on-azure-vm-vs-azure-sql-databaseazure-sql-managed-instance"></a>Szeretném összehasonlítani az SQL-példányok futtatásának költségeit az Azure-beli virtuális gépen vagy Azure SQL Database/Azure SQL felügyelt példányon

Létrehozhat egy értékelést **Azure-beli virtuális gép** típussal ugyanarra a csoportra vonatkozóan, amelyet az **Azure SQL**-értékelésben is használt. Ezután összehasonlíthatja a két jelentést egymás mellett. Az Azure-beli virtuális gépek értékelése az Azure Migrate-ben ugyanakkor jelenleg az átemeléses migrálásra összpontosít, és nem veszi figyelembe az SQL-példányok és -adatbázisok Azure-beli virtuális gépen történő futtatásához szükséges teljesítménymetrikákat. Amikor egy Azure-beli virtuális gépre irányuló értékelését egy kiszolgálón futtat, az ajánlott méret és a költségbecslések a kiszolgálón futó összes példányra vonatkoznak, és a példányok a Kiszolgáló migrálása eszközzel migrálhatók Azure-beli virtuális gépre. A migrálás előtt [tekintse át a teljesítményre vonatkozó irányelveket](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md) az Azure-beli virtuális gépeken futó SQL Serverhez kapcsolódóan.

## <a name="the-storage-cost-in-my-azure-sql-assessment-is-zero"></a>Az Azure SQL Assessment szolgáltatás tárolási díja nulla
Az Azure SQL felügyelt példányai esetében az első 32 GB/példány/hónap tárterülethez nem vehető igénybe tárterület, és a tárterülethez további tárolási díj is hozzáadódik 32GB-onként. [További információ](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)

## <a name="i-cant-see-some-groups-when-i-am-creating-an-azure-vmware-solution-avs-assessment"></a>Nem látok egyes csoportokat, amikor létrehozok egy Azure VMware-megoldás (AVS) felmérését

- Az AVS-értékelés olyan csoportokon hajtható végre, amelyekben csak VMware rendszerű gépek találhatók. Ha AVS-értékelést szeretne végezni, távolítson el minden nem VMware rendszerű gépet a csoportból.
- Ha először futtat AVS-értékeléseket az Azure Migrate-ben, akkor érdemes létrehozni egy új csoportot VMware rendszerű gépekből.

## <a name="i-cant-see-some-vm-types-and-sizes-in-azure-government"></a>Nem látok néhány virtuálisgép-típust és-méretet a Azure Government

Az értékeléshez és áttelepítéshez támogatott virtuálisgép-típusok és méretek Azure Government helyen rendelkezésre állástól függenek. Azure Governmentban [áttekintheti és összehasonlíthatja](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) a virtuális gépek típusait.

## <a name="the-size-of-my-server-changed-can-i-run-an-assessment-again"></a>A kiszolgáló mérete megváltozott. Újraértékelést is Futtathatok?

A Azure Migrate berendezés folyamatosan gyűjt információkat a helyszíni környezetről.  Az értékelés egy időponthoz kapcsolódó pillanatkép a helyszíni kiszolgálókról. Ha módosítja egy olyan kiszolgáló beállításait, amelyet szeretne felmérni, használja az újraszámítás lehetőséget, hogy frissítse az értékelést a legújabb módosításokkal.

## <a name="how-do-i-discover-servers-in-a-multitenant-environment"></a>Hogyan a kiszolgálók felderítését több-bérlős környezetben?

- **VMware**: Ha egy környezet megosztott a bérlők között, és nem szeretné felderíteni a bérlő kiszolgálóit egy másik bérlő előfizetésében, akkor hozzon létre VMware vCenter Server hitelesítő adatokat, amelyek csak a felderíteni kívánt kiszolgálókhoz férhetnek hozzá. Ezután használja ezeket a hitelesítő adatokat, amikor elindítja a felderítést a Azure Migrate berendezésben.
- **Hyper-v**: a felderítés a Hyper-v gazdagép hitelesítő adatait használja. Ha a kiszolgálók ugyanazt a Hyper-V-gazdagépet használják, jelenleg nincs lehetőség a felderítés elválasztására.  

## <a name="do-i-need-vcenter-server"></a>Szükségem van vCenter Serverre?

Igen, Azure Migrate a felderítés végrehajtásához VMware-környezetben vCenter Server szükséges. A Azure Migrate nem támogatja a vCenter Server által nem felügyelt ESXi-gazdagépek felderítését.

## <a name="what-are-the-sizing-options-in-an-azure-vm-assessment"></a>Mik a méretezési lehetőségek az Azure-beli virtuális gépek felmérésében?

Helyszíni méretezés esetén a Azure Migrate nem veszi figyelembe a kiszolgálói teljesítményadatok értékelését. Azure Migrate a virtuális gépek méretét a helyszíni konfiguráció alapján méri. A teljesítmény-alapú méretezéssel a méretezés a kihasználtsági adatain alapul.

Ha például egy helyszíni kiszolgáló négy maggal és 8 GB memóriával rendelkezik, 50%-os CPU-kihasználtság és 50% memória kihasználtsága:
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

A függőségi vizualizáció segíthet felmérni a kiszolgálók olyan csoportjait, amelyek nagyobb megbízhatósággal telepíthetők át. A függőségi vizualizációk átvizsgálják a számítógép függőségeit az értékelés futtatása előtt. Így biztosítható, hogy semmi sincs hátra, és segít elkerülni a váratlan kimaradásokat az Azure-ba való Migrálás során. Azure Migrate a függőségi vizualizáció engedélyezéséhez a Azure Monitor Service Map megoldását használja. [További információk](concepts-dependency-visualization.md).

> [!NOTE]
> Az ügynök-alapú függőség elemzése nem érhető el Azure Governmentban. Az ügynök nélküli függőségek elemzését is használhatja

## <a name="whats-the-difference-between-agent-based-and-agentless"></a>Mi a különbség az ügynök-alapú és az ügynök nélkül?

Az ügynök nélküli vizualizáció és az ügynök-alapú vizualizáció közötti különbségeket a táblázat foglalja össze.

**Követelmény** | **Ügynök nélküli** | **Ügynök-alapú**
--- | --- | ---
Támogatás | Ez a beállítás jelenleg előzetes verzióban érhető el, és csak a VMware-környezetben található kiszolgálókhoz használható. [Tekintse át](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) a támogatott operációs rendszereket. | Általánosan elérhető (GA).
Ügynök | Nem kell telepítenie az ügynököket az áttekinteni kívánt gépekre. | Az elemezni kívánt helyszíni gépekre telepítendő ügynökök: a [Microsoft monitoring Agent (MMA)](../azure-monitor/agents/agent-windows.md)és a [függőségi ügynök](../azure-monitor/agents/agents-overview.md#dependency-agent). 
Előfeltételek | [Tekintse át](concepts-dependency-visualization.md#agentless-analysis) az előfeltételeket és az üzembe helyezésre vonatkozó követelményeket. | [Tekintse át](concepts-dependency-visualization.md#agent-based-analysis) az előfeltételeket és az üzembe helyezésre vonatkozó követelményeket.
Log Analytics | Nem szükségesek. | A Azure Migrate a [Service Map](../azure-monitor/vm/service-map.md) megoldást használja [Azure monitor naplókban](../azure-monitor/logs/log-query-overview.md) a függőségi vizualizációhoz. [További információk](concepts-dependency-visualization.md#agent-based-analysis).
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

## <a name="can-i-visualize-dependencies-for-groups-of-more-than-10-servers"></a>Megjeleníthető a több mint 10 kiszolgálóból álló csoportok függőségei?

Megjelenítheti a [függőségeket](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping) a legfeljebb 10 kiszolgálóval rendelkező csoportok esetében. Ha több mint 10 kiszolgálót tartalmazó csoporttal rendelkezik, javasoljuk, hogy ossza szét a csoportot kisebb csoportokba, majd jelenítse meg a függőségeket.

## <a name="next-steps"></a>Következő lépések

Olvassa el a [Azure Migrate áttekintést](migrate-services-overview.md).