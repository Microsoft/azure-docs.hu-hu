---
title: Azure Migrate kiszolgáló áttelepítésével kapcsolatos gyakori kérdések
description: Választ kaphat a Azure Migrate kiszolgáló áttelepítésének a gépek áttelepítésére való használatával kapcsolatos gyakori kérdésekre.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 08/28/2020
ms.openlocfilehash: 63c7f226dcd99ec8040f2078ce12be0fe3c594df
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99548813"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Azure Migrate kiszolgáló áttelepítése: gyakori kérdések

Ez a cikk a Azure Migrate: Server áttelepítési eszközzel kapcsolatos gyakori kérdésekre ad választ. Ha további kérdései vannak, tekintse meg a következő erőforrásokat:

- [Általános kérdések](resources-faq.md) a Azure Migrate
- Az [Azure Migrate berendezéssel](common-questions-appliance.md) kapcsolatos kérdések
- A [felderítéssel, az értékeléssel és a függőségi vizualizációval](common-questions-discovery-assessment.md) kapcsolatos kérdések
- A [Azure Migrate fórumban](https://aka.ms/AzureMigrateForum) választ kaphat kérdéseire

## <a name="does-azure-migrate-convert-uefi-based-machines-to-bios-based-machines-and-migrate-them-to-azure-as-azure-generation-1-vms"></a>A Azure Migrate UEFI-alapú gépeket konvertál BIOS-alapú gépekre, és migrálja őket az Azure-ba Azure 1. generációs virtuális gépekként?
Azure Migrate: a kiszolgáló áttelepítési eszköze az összes UEFI-alapú gépet áttelepíti az Azure-ba Azure 2. generációs virtuális gépekként. Az UEFI-alapú virtuális gépek BIOS-alapú virtuális gépekre való átalakítása már nem támogatott. Vegye figyelembe, hogy a BIOS-alapú gépeket csak az Azure 1. generációs virtuális gépei telepítik át az Azure-ba.

## <a name="how-can-i-migrate-uefi-based-machines-to-azure-as-azure-generation-1-vms"></a>Hogyan telepíthetek UEFI-alapú gépeket az Azure-ba az 1. generációs virtuális gépeken?
Azure Migrate: a kiszolgáló áttelepítési eszköze UEFI-alapú gépeket telepít át az Azure-ba az Azure 2. generációs virtuális gépeiként. Ha az Azure 1. generációs virtuális gépekre kívánja áttelepíteni őket, a replikálás megkezdése előtt alakítsa át a rendszerindítási típust a BIOS-ba, majd használja a Azure Migrate: Server áttelepítési eszközt az Azure-ba való áttelepítéshez
 
## <a name="which-operating-systems-are-supported-for-migration-of-uefi-based-machines-to-azure"></a>Mely operációs rendszerek támogatottak az UEFI-alapú gépek Azure-ba való áttelepítéséhez?

| **UEFI-alapú gépekhez támogatott operációs rendszerek** | **Ügynök nélkül VMware – Azure**                                                                                                             | **Ügynök nélküli Hyper-V-ről Azure-ra** | **Ügynök-alapú VMware, fizikai és egyéb felhők az Azure-ba** |
| ------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------ | ---------------------------------------------------------- |
| Windows Server 2019, 2016, 2012 R2, 2012                | Y                                                                                                                                         | Y                              | Y                                                          |
| Windows 10 Pro, Windows 10 Enterprise                   | Y                                                                                                                                         | Y                              | Y                                                          |
| SUSE Linux Enterprise Server 15 SP1                     | Y                                                                                                                                         | Y                              | Y                                                          |
| SUSE Linux Enterprise Server 12 SP4                     | Y                                                                                                                                         | Y                              | Y                                                          |
| Ubuntu Server 16,04, 18,04, 19,04, 19,10                | Y                                                                                                                                         | Y                              | Y                                                          |
| RHEL 8,1, 8,0, 7,8, 7,7, 7,6, 7,5, 7,4, 7,0, 6. x        | Y<br>                 _A RHEL 8. x [manuális előkészítést](./prepare-for-migration.md#linux-machines) igényel_   | Y                              | Y                                                          |
| Cent OS 8,1, 8,0, 7,7, 7,6, 7,5, 7,4, 6. x               | Y<br>_A cent OS 8. x [manuális előkészítést](./prepare-for-migration.md#linux-machines) igényel_ | Y                              | Y                                                          |
| Oracle Linux 7,7, 7,7-CI                                |  Y                                                                                                                                        | Y                              | Y                                                          |

## <a name="can-i-use-the-recovery-services-vault-created-by-azure-migrate-for-disaster-recovery-scenarios"></a>Használhatom a Azure Migrate által létrehozott Recovery Services-tárolót a vész-helyreállítási forgatókönyvekhez?
A Azure Migrate által létrehozott Recovery Services-tároló használata vész-helyreállítási forgatókönyvek esetén nem ajánlott. Ennek eredményeképpen a replikálási hibák megkezdődhetnek Azure Migrateban. 

## <a name="where-should-i-install-the-replication-appliance-for-agent-based-migrations"></a>Hol kell telepíteni a replikációs berendezést az ügynök-alapú áttelepítésekhez?

A replikációs berendezést dedikált gépre kell telepíteni. A replikációs berendezést nem szabad olyan számítógépre telepíteni, amelyet replikálni szeretne, vagy a Azure Migrate felderítési és értékelési berendezésen, amelyre korábban már telepítve van. További részletekért kövesse az [oktatóanyagot](./tutorial-migrate-physical-virtual-machines.md) .

## <a name="how-can-i-migrate-my-aws-ec2-instances-to-azure"></a>Hogyan telepíthetem az AWS EC2-példányokat az Azure-ba?

Tekintse át ezt a [cikket](./tutorial-migrate-aws-virtual-machines.md) az AWS EC2-példányok Azure-ba való felkereséséhez, értékeléséhez és migrálása érdekében.

## <a name="can-i-migrate-aws-vms-running-amazon-linux-operating-system"></a>Áttelepíthetem az Amazon Linux operációs rendszert futtató AWS virtuális gépeket?

Az Amazon Linux rendszerű virtuális gépek nem telepíthetők át, mert az Amazon Linux operációs rendszer csak az AWS-t támogatja.
Az Amazon Linux rendszeren futó munkaterhelések áttelepítéséhez egy CentOS/RHEL virtuális gépet is üzembe helyezhet az Azure-ban, és áttelepítheti az AWS linuxos gépen futó munkaterhelést a megfelelő munkaterhelés-áttelepítési módszer használatával. Például a munkaterheléstől függően előfordulhat, hogy a munkaterhelés-specifikus eszközök az áttelepítést segítik – például a webkiszolgálók adatbázisai vagy központi telepítési eszközei esetében.

## <a name="what-geographies-are-supported-for-migration-with-azure-migrate"></a>Milyen földrajzi területek támogatottak a Azure Migrate való áttelepítéshez?

Tekintse át a [nyilvános](migrate-support-matrix.md#supported-geographies-public-cloud) és a [Government-felhők](migrate-support-matrix.md#supported-geographies-azure-government) támogatott régióit.

## <a name="can-we-use-the-same-azure-migrate-project-to-migrate-to-multiple-regions"></a>Használhatjuk ugyanazt a Azure Migrate projektet több régióba való Migrálás céljából?

Noha egy Azure Migrate projekt több régiójához is létrehozhat értékeléseket, az egyik Azure Migrate projekt csak egyetlen Azure-régióba telepítheti át a kiszolgálókat. Létrehozhat további Azure Migrate projekteket minden olyan régióhoz, amelyre át kell térnie.

- Az ügynök nélküli VMware-Migrálás esetén a megcélzott régió zárolva van az első replikáció engedélyezése után.
- Az ügynök-alapú áttelepítések (VMware, fizikai kiszolgálók és más felhőkből származó kiszolgálók) esetében a megcélzott régió zárolva lesz, amint az "erőforrások létrehozása" gombra kattint a portálon a replikációs berendezés beállításakor.
- Az ügynök nélküli Hyper-V áttelepítések esetében a célként megadott régió zárolva lesz, amint az "erőforrások létrehozása" gombra kattint a portálon a Hyper-V replikációs szolgáltató beállításakor.

## <a name="can-we-use-the-same-azure-migrate-project-to-migrate-to-multiple-subscriptions"></a>Használhatjuk ugyanazt a Azure Migrate projektet több előfizetésre való Migrálás céljából? 

Igen, egyszerre több előfizetésre is áttelepíthet egy Azure Migrate projekthez tartozó megcélzott régióban. Kiválaszthatja a cél előfizetést, miközben engedélyezi a replikációt egy gép vagy egy csoport számára. A célként megadott régió zárolva van az ügynök nélküli VMware-Migrálás első replikálása után, valamint a replikálási berendezés és a Hyper-V szolgáltató telepítése esetében az ügynök-alapú áttelepítések és az ügynök nélküli Hyper-V áttelepítések során.

## <a name="what-are-the-migration-options-in-azure-migrate-server-migration"></a>Milyen áttelepítési lehetőségek vannak a Azure Migrate: Server Migration?

A Azure Migrate: a kiszolgáló áttelepítési eszköze két lehetőséget kínál a forráskiszolgáló/virtuális gépek áttelepítésére az Azure-ban – ügynök nélküli áttelepítés és ügynök-alapú áttelepítés.

A kiválasztott áttelepítési lehetőségtől függetlenül, az első lépés egy kiszolgáló Azure-beli áttelepítéssel történő áttelepítése: a kiszolgáló áttelepítése a kiszolgáló replikálásának engedélyezése. Ez elvégzi a virtuális gép/kiszolgáló adatok kezdeti replikálását az Azure-ba. A kezdeti replikálás befejeződése után folyamatban van egy folyamatos replikáció (folyamatos Delta-Sync) a növekményes adatközpontok Azure-ba történő átállításához. Ha a művelet eléri a különbözeti szinkronizálási szakaszt, akkor bármikor áttelepítheti az Azure-ba.  

Az alábbi szempontokat érdemes figyelembe venni az áttelepítési lehetőség eldöntése során.

Az **ügynök nélküli áttelepítések** nem igényelnek olyan szoftvereket (ügynököket), amelyeket telepíteni kell az áttelepített forrásoldali virtuális gépeken/kiszolgálókon. Az ügynök nélküli beállítás összehangolja a replikációt a virtualizációs szolgáltató által biztosított funkciók integrálásával.
A [VMWare virtuális gépek](./tutorial-migrate-vmware.md) és a [Hyper-V virtuális gépek](./tutorial-migrate-hyper-v.md)esetében az ügynök nélküli replikációs lehetőségek érhetők el.

Az **ügynök-alapú áttelepítések** megkövetelik, hogy Azure Migrate szoftvert (ügynököket) telepítsen az áttelepíteni kívánt forrásoldali virtuális gépekre vagy gépekre. Az ügynök-alapú beállítás nem támaszkodik a virtualizációs platformra a replikálási funkciókhoz, ezért az x86/x64 architektúrát futtató kiszolgálókkal, valamint az ügynök-alapú replikációs módszer által támogatott operációs rendszer verziójának használatával használható.

Az ügynök alapú áttelepítési beállítás a [VMWare virtuális gépek](./tutorial-migrate-vmware-agent.md), [a Hyper-V virtuális gépek](./tutorial-migrate-physical-virtual-machines.md), a [fizikai kiszolgálók](./tutorial-migrate-physical-virtual-machines.md), az AWS-en [futó](./tutorial-migrate-aws-virtual-machines.md)virtuális gépek, a GCP-on futó virtuális gépek vagy más virtualizációs szolgáltatón futó virtuális gépek esetében használható. Az ügynök alapú áttelepítés fizikai kiszolgálóként kezeli a gépeket az áttelepítés céljára.

Habár az ügynök nélküli áttelepítés további kényelmet és egyszerűséget biztosít a támogatott forgatókönyvek (VMWare és Hyper-V) ügynök-alapú replikációs lehetőségeinél, érdemes megfontolni az ügynök-alapú forgatókönyv használatát a következő használati esetekben:

- IOPS korlátozott környezet: az ügynök nélküli replikáció pillanatképeket használ, és a tárolási IOPS/sávszélességet használja. Javasoljuk, hogy az ügynök-alapú áttelepítési módszert, ha a környezetében korlátozások vannak a tároló-vagy IOPS.
- Ha nincs vCenter Server, a VMware virtuális gépeket fizikai kiszolgálóként kezelheti, és az ügynök alapú áttelepítési munkafolyamatot is használhatja.

További információért olvassa el ezt a [cikket](./server-migrate-overview.md) a VMware-áttelepítések áttelepítési lehetőségeinek összehasonlításához.

## <a name="how-does-agentless-migration-work"></a>Hogyan működik az ügynök nélküli áttelepítés?

Azure Migrate: a kiszolgáló áttelepítése ügynök nélküli replikációs lehetőségeket biztosít a VMware virtuális gépek és a Windows vagy Linux rendszerű Hyper-V virtuális gépek áttelepítéséhez. Az eszköz egy további ügynök-alapú replikációs lehetőséget is biztosít a Windows-és Linux-kiszolgálókhoz, amelyek segítségével fizikai kiszolgálókat telepíthet át, valamint x86/x64 virtuális gépeket VMware, Hyper-V, AWS, GCP stb. használatával. Az ügynök-alapú replikálási beállításhoz az ügynök szoftverének telepítésére van szükség az áttelepített kiszolgálón/virtuális gépen, míg az ügynök nélküli beállításban nem kell telepíteni a szoftvereket a virtuális gépekre, így az ügynök-alapú replikációs lehetőség további kényelmet és egyszerűséget kínál.

Az ügynök nélküli replikációs beállítás a virtualizációs szolgáltató (VMware, Hyper-V) által biztosított mechanizmusok használatával működik. VMware-alapú virtuális gépek esetén az ügynök nélküli replikációs mechanizmus a VMware-Pillanatképek és a VMware changed Tracking Technology használatával replikálja a virtuális gépek lemezeit. Ez a mechanizmus a számos biztonsági mentési termék által használthoz hasonló. Hyper-V virtuális gépek esetén az ügynök nélküli replikációs mechanizmus virtuálisgép-pillanatképeket használ, és a Hyper-V replika Change Tracking funkciójának használatával replikálja a virtuális gépek lemezeinek adatait.

Ha a replikáció egy virtuális géphez van konfigurálva, először egy kezdeti replikálási fázison halad át. A kezdeti replikáció során létrejön egy virtuális gép pillanatképe, és a pillanatkép-lemezekről származó adatok teljes másolata replikálódik az előfizetésében lévő felügyelt lemezekre. Miután befejeződött a virtuális gép kezdeti replikálása, a replikálási folyamat egy növekményes replikálási (különbözeti replikáció) fázisra vált. A növekményes replikálási fázisban az utolsó befejezett replikációs ciklus óta bekövetkezett adatváltozásokat a rendszer rendszeres időközönként replikálja és alkalmazza a replika által felügyelt lemezekre, így a replikáció szinkronban van a virtuális gépen történt változásokkal. VMware-alapú virtuális gépek esetén a rendszer a VMware által módosított blokk követési technológiát használja a replikálási ciklusok közötti változások nyomon követésére. A replikálási ciklus elején létrejön egy virtuális gép pillanatképe, és a változások blokkolásának követése a jelenlegi pillanatkép és az utolsó sikeresen replikált pillanatkép közötti módosítások beszerzésére szolgál. Így csak a legutóbbi befejezett replikálási ciklus óta módosult adatváltozások replikálása szükséges a virtuális gép replikációjának megőrzéséhez. A rendszer az egyes replikációs ciklusok végén kibocsátja a pillanatképet, és elvégzi a pillanatkép-konszolidációt a virtuális gépen. Hasonlóképpen, a Hyper-V virtuális gépek esetében a Hyper-V replika változás-követési motorja az egymást követő replikálási ciklusok közötti változások nyomon követésére szolgál.
Amikor az áttelepítési műveletet replikáló virtuális gépen hajtja végre, lehetősége van leállítani a helyszíni virtuális gépet, és egy végső növekményes replikálást hajthat végre, így biztosítva a nulla adatvesztést. Az áttelepítési lehetőség végrehajtásakor a virtuális géphez tartozó replika felügyelt lemezeket a rendszer a virtuális gép létrehozásához használja az Azure-ban.

Első lépésként tekintse át a [VMware ügynök nélküli Migrálás](./tutorial-migrate-vmware.md) és a [Hyper-V ügynök nélküli áttelepítési](./tutorial-migrate-hyper-v.md) oktatóanyagok című témakört.

## <a name="how-does-agent-based-migration-work"></a>Hogyan működik az ügynök-alapú áttelepítés?

A VMware virtuális gépek és a Hyper-V virtuális gépek ügynök nélküli áttelepítési lehetőségei mellett a kiszolgáló áttelepítési eszköze egy ügynök-alapú áttelepítési lehetőséget biztosít a fizikai kiszolgálókon futó Windows-és Linux-kiszolgálók áttelepítéséhez, illetve a VMware, Hyper-V, AWS, Google Cloud Platform stb. x86/x64 virtuális gépekként való futtatásához.

Az ügynök-alapú áttelepítési módszer az áttelepített kiszolgálóra telepített ügynök szoftverét használja a kiszolgálói adatszolgáltatások Azure-ba történő replikálásához. A replikálási folyamat olyan kiszervezési architektúrát használ, amelyben az ügynök a replikációs eszköz vagy a konfigurációs kiszolgáló (vagy egy kibővíthető folyamat kiszolgálója) nevű dedikált replikációs kiszolgálóra továbbítja a replikálási adattovábbítást. [További](./agent-based-migration-architecture.md) információ az ügynök alapú áttelepítési lehetőség működéséről. 

Megjegyzés: a replikációs berendezés eltér a Azure Migrate felderítési berendezéstől, és külön/dedikált gépre kell telepíteni.

## <a name="how-do-i-gauge-the-bandwidth-requirement-for-my-migrations"></a>Hogyan a sávszélességre vonatkozó követelményt a saját Migrálás esetén?

Az adatok Azure-ba való replikálásának sávszélessége számos tényezőtől függ, és annak a funkciója, hogy a helyszíni Azure Migrate készülék milyen gyorsan tudja olvasni és replikálni az Azure-ba irányuló adatmennyiséget. A replikációnak két fázisa van: a kezdeti replikálás és a különbözeti replikáció.

Amikor a replikáció elindul egy virtuális gépen, a kezdeti replikálási ciklus jelenik meg, amelyben a lemezek teljes másolata replikálódik. A kezdeti replikáció befejezését követően a növekményes replikálási ciklusok (különbözeti ciklusok) rendszeres időközönként vannak ütemezve az előző replikálási ciklus óta bekövetkezett változások átviteléhez.

### <a name="agentless-vmware-vm-migration"></a>Ügynök nélküli VMware VM-Migrálás

A sávszélességre vonatkozó követelményt a hullámban áthelyezett adatmennyiség és a kezdeti replikálás befejezéséhez szükséges idő alapján lehet elvégezni (ideális esetben a kezdeti replikálást szeretné legalább 3-4 nappal a tényleges áttelepítési időszak előtt fejeződött be, hogy elegendő idő legyen a teszt áttelepítésére a tényleges ablak előtt, valamint az állásidő minimálisra állítása az ablakon belül.

Az ügynök nélküli VMware virtuális gépek áttelepítéséhez szükséges sávszélességet vagy időt az alábbi képlet alapján becsülheti meg:

A kezdeti replikáció befejezésének ideje = {a lemezek mérete (vagy a felhasznált méret, ha elérhető) * 0,7 (feltéve, hogy 30%-os tömörítési átlag – konzervatív becslés)}/Bandwidth érhető el a replikáláshoz.

### <a name="agent-based-vmware-vm-migration"></a>Ügynök-alapú VMware VM-Migrálás

Egy ügynök-alapú replikációs módszer esetén a Deployment Planner segíthet a környezet megadásában az adatváltozáshoz, és a szükséges sávszélesség előrejelzésében is segít. További információkért tekintse meg ezt a [cikket](./agent-based-migration-architecture.md#plan-vmware-deployment). 

## <a name="how-do-i-throttle-replication-in-using-azure-migrate-appliance-for-agentless-vmware-replication"></a>Hogyan a replikációt a Azure Migrate berendezés használatával az ügynök nélküli VMware-replikációhoz?  

A NetQosPolicy használatával szabályozhatja a szabályozást. Például:

A NetQosPolicy használandó AppNamePrefix: "GatewayWindowsService.exe". Létrehozhat egy szabályzatot az Azure Migrate berendezésen a készülék replikációs forgalmának szabályozásához egy olyan házirend létrehozásával, mint például ez:

New-NetQosPolicy-Name "ThrottleReplication"-AppPathNameMatchCondition "GatewayWindowsService.exe"-ThrottleRateActionBitsPerSecond 1 MB

## <a name="how-is-the-data-transmitted-from-on-prem-environment-to-azure-is-it-encrypted-before-transmission"></a>Hogyan történik az adatok továbbítása a helyszíni környezetből az Azure-ba? Titkosítva van az átvitel előtt?

Az ügynök nélküli replikálásban az Azure Migrate készülék tömöríti az adatgyűjtést, és a feltöltés előtt titkosítja azokat. Az adatok továbbítása biztonságos kommunikációs csatornán keresztül történik HTTPS protokollon keresztül, és a TLS 1,2-es vagy újabb verzióját használja. Az Azure Storage emellett automatikusan titkosítja az adatait, ha a felhőbe (titkosítás – REST) megőrzi őket.  

## <a name="how-does-churn-rate-affect-agentless-replication"></a>Hogyan befolyásolja az adatforgalom aránya az ügynök nélküli replikációt?

Mivel az ügynök nélküli replikáció az adatforgalomban található, az *adatváltozási minta* fontosabb, mint a változási *arány*. Ha egy fájl újra és újra íródik, a ráta nem befolyásolja a nagy hatást. A következő ciklusban azonban egy minta, amelyben minden más ágazat írásos, magas adatváltozást okoz. Mivel minimálisra csökkentjük az általunk átvitt adatok mennyiségét, a lehető legnagyobb mértékben engedélyezzük, hogy az adatok minél hamarabb bekerüljön a következő ciklusba.

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>Milyen gyakran ütemezik a replikálási ciklust?

A következő replikálási ciklust ütemezni kívánt képlet (a korábbi ciklus ideje/2) vagy egy óra, attól függően, hogy melyik a magasabb.

Ha például egy virtuális gép négy órát vesz igénybe egy különbözeti ciklus esetében, a következő ciklus két órán belül ütemezve van, és nem a következő órában. A folyamat azonnal eltér a kezdeti replikáció után, amikor az első különbözeti ciklust azonnal ütemezi.

## <a name="how-do-i-migrate-windows-server-2003-running-on-vmwarehyper-v-to-azure"></a>Hogyan a VMware/Hyper-V-n futó Windows Server 2003-et az Azure-ba?

A [Windows Server 2003 kiterjesztett támogatása](/troubleshoot/azure/virtual-machines/run-win-server-2003#microsoft-windows-server-2003-end-of-support) 2015. július 14-én fejeződött be.  Az Azure-támogatási csapat továbbra is segítséget nyújt a Windows Server 2003 Azure-beli futtatásával kapcsolatos problémák elhárításában. Ez a támogatás azonban olyan problémákra korlátozódik, amelyek nem igényelnek operációsrendszer-szintű hibaelhárítást vagy javítást.
Az alkalmazások áttelepítése a Windows Server újabb verzióját futtató Azure-példányokra az ajánlott módszer annak biztosítására, hogy hatékonyan hasznosítsa az Azure-felhő rugalmasságát és megbízhatóságát.

Ha azonban továbbra is a Windows Server 2003-es verzióját telepíti át az Azure-ba, a Azure Migrate: Server áttelepítési eszköz használatával, ha a Windows Server egy VMware vagy Hyper-V rendszeren futó virtuális gép, tekintse át ezt a cikket a [Windows server 2003 rendszerű gépek áttelepítésre való előkészítéséhez](./prepare-windows-server-2003-migration.md).

## <a name="what-is-the-difference-between-the-test-migration-and-migrate-operations"></a>Mi a különbség a tesztelési áttelepítés és az áttelepítési műveletek között?

A tesztelési áttelepítés lehetővé teszi az áttelepítés tesztelését és érvényesítését a tényleges áttelepítés előtt. Az áttelepítés tesztelése úgy működik, hogy a virtuális gépeket az Azure-ban egy homokozó környezetbe replikáló példányok létrehozásával hozza létre. A homokozó környezetét egy Ön által megadott teszt virtuális hálózat határozza meg. A teszt áttelepítési művelete nem zavaró, és az alkalmazások továbbra is futnak a forráson, miközben egy elkülönített sandbox-környezetben futtatott, klónozott példányon végez teszteket. Az áttelepítés ellenőrzéséhez, az alkalmazások teszteléséhez és a tényleges áttelepítés előtti problémák megoldásához szükség szerint több tesztet is végrehajthat.

## <a name="will-windows-server-2008-and-2008-r2-be-supported-in-azure-after-migration"></a>Támogatott-e a Windows Server 2008 és a 2008 R2 az Azure-ban az áttelepítés után?

A helyszíni Windows Server 2008-es és 2008 R2-es kiszolgálókat áttelepítheti Azure-beli virtuális gépekre, és a támogatási dátumok lejárta után három évig kiterjesztheti a kiterjesztett biztonsági frissítéseket, a virtuális gép működtetésének költségét megelőzően. A Azure Migrate: Server áttelepítési eszköz használatával áttelepítheti a Windows Server 2008-es és 2008 R2-es munkaterheléseit.

## <a name="is-there-a-rollback-option-for-azure-migrate"></a>Van-e visszaállítási lehetőség a Azure Migratehoz?

Az Azure-ban az alkalmazás funkcióinak és teljesítményének ellenőrzéséhez használhatja az áttelepítés tesztelése lehetőséget. Tetszőleges számú tesztelési áttelepítést hajthat végre, és a végső áttelepítés után is végrehajthatja a megbízhatóságot a teszt áttelepítési művelettel. Egy teszt áttelepítése nem befolyásolja a helyszíni gépet, amely működőképes marad, és addig folytatja a replikálást, amíg el nem végzi a tényleges áttelepítést. Ha hiba történt a teszt áttelepítési ellenőrzését során, dönthet úgy, hogy elhalasztja a végső áttelepítést, és megtartja a forrásként szolgáló virtuális gépet/kiszolgálót, és replikálja az Azure-ba. A hibák elhárítása után újrapróbálkozhat a végső áttelepítéssel.  
Megjegyzés: miután elvégezte az Azure-ba való végleges áttelepítést, és a helyszíni forrásoldali gép le lett állítva, nem végezhet visszaállítást az Azure-ból a helyszíni környezetbe.

## <a name="can-i-select-the-virtual-network-and-subnet-to-use-for-test-migrations"></a>Kiválaszthatom a Virtual Network és az alhálózatot az áttelepítés teszteléséhez?

Kiválaszthat egy Virtual Network a tesztek áttelepítéséhez. Az alhálózat a következő logika alapján automatikusan ki van választva:

- Ha egy célként megadott alhálózat (az alapértelmezetttől eltérő) bemenetként lett megadva a replikáció engedélyezésekor, akkor Azure Migrate rangsorolja az azonos nevű alhálózatot a teszt áttelepítéshez kiválasztott Virtual Network.
- Ha az azonos nevű alhálózat nem található, Azure Migrate kiválasztja az első olyan alhálózatot, amely nem átjáró/Application Gateway/tűzfal/megerősített alhálózat.

## <a name="why-is-the-test-migration-button-disabled-for-my-server"></a>Miért le van tiltva a teszt áttelepítése gomb a saját kiszolgálón?

Az áttelepítési teszt gomb a következő esetekben lehet letiltott állapotban:

- Nem kezdheti meg a teszt áttelepítését, amíg a virtuális gép kezdeti replikációja (IR) be nem fejeződik. Az áttelepítési teszt gomb le lesz tiltva, amíg az IR folyamat be nem fejeződik. Ha a virtuális gép Delta-szinkronizálási szakaszban van, elvégezheti a teszt áttelepítését.
- A gomb letiltható, ha a teszt áttelepítése már befejeződött, de a rendszer nem hajtotta végre a teszt áttelepítését a virtuális géphez. Végezzen áttelepítési tesztet, majd próbálja megismételni a műveletet.

## <a name="what-happens-if-i-dont-clean-up-my-test-migration"></a>Mi történik, ha nem törlik a teszt áttelepítését?

Az áttelepítési teszt szimulálja a tényleges áttelepítést egy Azure-beli virtuális gép replikált adatokkal történő létrehozásával. A kiszolgáló a replikált adatpontról a cél erőforráscsoporthoz (a replikálás engedélyezésekor kiválasztva) a "-test" utótaggal lesz telepítve. A tesztelési áttelepítések a kiszolgálói funkciók ellenőrzéséhez szükségesek, így az áttelepítés utáni problémák kisebbek lesznek. Ha a teszt áttelepítése nem történik meg a tesztelés után, a teszt virtuális gép továbbra is az Azure-ban fog futni, és díjat számítunk fel. A teszt áttelepítésének elvégzéséhez nyissa meg a kiszolgálók áttelepítési eszközének replikáló gépek nézetét, és használja a számítógépen a "kitakarítási teszt áttelepítése" műveletet.

## <a name="can-i-migrate-active-directory-domain-controllers-using-azure-migrate"></a>Áttelepíthetem Active Directory-tartományvezérlőket a Azure Migrate használatával?

A kiszolgáló-áttelepítési eszköz a legtöbb alkalmazás esetében működik, és az alkalmazás agnosztikus. Amikor a kiszolgáló áttelepítési eszközével telepít át egy kiszolgálót, a rendszer a kiszolgálóra telepített összes alkalmazást áttelepíti a szolgáltatással együtt. Egyes alkalmazások esetében azonban a különböző áttelepítési módszerek is jobban illeszkednek az áttelepítéshez.  Ha Active Directory olyan hibrid környezetek esetén, ahol a helyszíni hely csatlakozik az Azure-környezethez, a címtárat kiterjesztheti az Azure-ba további tartományvezérlők hozzáadásával az Azure-ban, és beállíthatja Active Directory replikációját. Ha az Azure-ban egy elkülönített környezetbe telepíti a saját tartományvezérlőket (vagy a homokozóban futó alkalmazások tesztelését), akkor a kiszolgáló áttelepítési eszközével telepítheti át a kiszolgálókat.

## <a name="what-happens-if-i-dont-stop-replication-after-migration"></a>Mi történik, ha nem tudom leállítani a replikálást az áttelepítés után?

Ha leállítja a replikálást, a Azure Migrate: a kiszolgáló áttelepítési eszköze törli a replikáláshoz létrehozott előfizetésben található felügyelt lemezeket. Ha nem állítja le a replikálást az áttelepítés után, akkor továbbra is díjkötelesek lesznek a lemezek. A replikálás leállítása nem befolyásolja a már áttelepített gépekhez csatolt lemezeket.

## <a name="do-i-need-vmware-vcenter-to-migrate-vmware-vms"></a>A VMWare virtuális gépek áttelepítéséhez VMware vCenter szükséges?

[VMWare virtuális gépek](server-migrate-overview.md) áttelepítéséhez VMware ügynök-alapú vagy ügynök nélküli Migrálás esetén az ESXi-gazdagépeket, amelyeken a virtuális gépek találhatók, vCenter Server kell felügyelni. Ha nem rendelkezik vCenter Serverekkel, áttelepítheti a VMware virtuális gépeket fizikai kiszolgálóként való áttelepítéssel. [További információk](migrate-support-matrix-physical-migration.md).

## <a name="can-i-upgrade-my-os-while-migrating"></a>Frissíthetem az operációs rendszert az áttelepítés során?

A Azure Migrate: a kiszolgáló áttelepítési eszköze jelenleg csak a hasonló típusú áttelepítéseket támogatja. Az eszköz nem támogatja az operációs rendszer verziószámának frissítését az áttelepítés során. Az áttelepített gépen ugyanaz az operációs rendszer lesz, mint a forrásoldali gépnek.

## <a name="i-deployed-two-or-more-appliances-to-discover-vms-in-my-vcenter-server-however-when-i-try-to-migrate-the-vms-i-only-see-vms-corresponding-to-one-of-the-appliances"></a>Két (vagy több) készülék üzembe helyezésével felderítem a virtuális gépeket a vCenter Server. Ha azonban megpróbálom áttelepíteni a virtuális gépeket, csak az egyik készülékhez tartozó virtuális gépeket látok.

Ha több berendezés is telepítve van, feltétel, hogy ne legyen átfedés a megadott vCenter-fiókokban a virtuális gépek között. Az ilyen átfedés mellett zajló felderítés nem támogatott forgatókönyv.

## <a name="how-do-i-know-if-my-vm-was-successfully-migrated"></a>Hogyan tudni, hogy a virtuális gép migrálása sikeres volt-e?

Miután sikeresen áttelepítette a virtuális gépet/kiszolgálót, megtekintheti és kezelheti a virtuális gépet a Virtual Machines lapon. Kapcsolódjon az áttelepített virtuális géphez az ellenőrzéshez.
Azt is megteheti, hogy a művelet állapotát áttekintve ellenőrzi, hogy az áttelepítés sikeresen befejeződött-e. Ha bármilyen hibát tapasztal, hárítsa el, majd próbálja megismételni az áttelepítési műveletet.

## <a name="can-i-consolidate-multiple-source-vms-into-one-vm-while-migrating"></a>Összevonható több forrásoldali virtuális gép egy virtuális gépre az áttelepítés során?

Azure Migrate-kiszolgáló áttelepítési képességeinek támogatása, például jelenleg a hasonló áttelepítésekhez. A Migrálás részeként nem támogatott a kiszolgálók összevonása vagy az operációs rendszer frissítése. 

## <a name="how-does-agentless-replication-affect-vmware-servers"></a>Hogyan befolyásolja az ügynök nélküli replikáció a VMware-kiszolgálókat?

Az ügynök nélküli replikáció a VMware vCenter Server és VMware ESXi gazdagépek teljesítményére gyakorolt hatásukat eredményez. Mivel az ügynök nélküli replikáció pillanatképeket használ, a IOPS-t használja a tárterületen, ezért bizonyos IOPS-sávszélességre van szükség. Nem ajánlott ügynök nélküli replikálást használni, ha a környezetében korlátozásokkal rendelkezik a tároló-vagy IOPs.


## <a name="next-steps"></a>Következő lépések

Olvassa el a [Azure Migrate áttekintést](migrate-services-overview.md).
