---
title: Nagyszámítógépek számítási teljesítményének áthelyezése az Azure Virtual Machinesba
description: Az Azure számítási erőforrásai kedvezően összehasonlítják a nagyszámítógépi kapacitást, így áttelepíthetik és modernizálják az IBM z14-alkalmazásokat.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines
ms.openlocfilehash: 32f259f20e0e24b4d5346c598e23fdc2df48dee6
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102556434"
---
# <a name="move-mainframe-compute-to-azure"></a>Nagyszámítógépek számítási teljesítményének áthelyezése az Azure-ba

A nagyvállalatok nagy megbízhatóságot és rendelkezésre állást biztosítanak, és számos vállalat megbízható gerincét jelentik. Gyakran úgy gondolják, hogy szinte korlátlan skálázhatóságot és számítási teljesítményt is biztosítanak. Egyes vállalatok azonban kinőtték a legnagyobb rendelkezésre álló nagyszámítógépek képességeit. Ha ez úgy hangzik, mint Ön, az Azure rugalmasságot, elérhetőséget és infrastrukturális megtakarítást nyújt.

A nagyvállalati munkaterhelések Microsoft Azureon való futtatásához tudnia kell, hogy a mainframe számítási képességei hogyan hasonlíthatók össze az Azure-ban. A jelen cikk az IBM z14 mainframe (a jelenlegi modell) alapján mutatja be, hogyan szerezhet be hasonló eredményeket az Azure-ban.

Első lépésként vegye figyelembe a környezeteket egymás mellett. Az alábbi ábra összehasonlítja az alkalmazások Azure üzemeltetési környezetbe való futtatásához szükséges mainframe-környezetet.

![Az Azure-szolgáltatások és az emulációs környezetek hasonló támogatást nyújtanak, és egyszerűbbé teszik a migrációt](media/mainframe-compute-azure.png)

A nagyszámítógépek hatékonyságát gyakran használják olyan online tranzakció-feldolgozási (OLTP) rendszerekhez, amelyek több ezer felhasználó több millió frissítését kezelik. Ezek az alkalmazások gyakran használnak szoftvereket a tranzakciók feldolgozásához, a képernyő-kezeléshez és az űrlap-bejegyzésekhez. Az ügyfél-ellenőrzési rendszer (CICS), az Information Management rendszer (IMS) vagy a tranzakciós illesztőfelület-csomag (TIP) használatával is rendelkezhetnek.

Ahogy az ábrán látható, az Azure-beli TPM-emulátor képes kezelni a CICS és az IMS-alapú számítási feladatokat. Az Azure-ban futó batch rendszeremulátor a Feladatkezelő nyelvének (JCL) szerepkörét hajtja végre. A rendszer áttelepíti a mainframe-adatbázisokat az Azure-adatbázisokba, például a Azure SQL Databaseba. A rendszerfelügyelethez az Azure-szolgáltatások és az Azure Virtual Machines üzemeltetett egyéb szoftverek is használhatók.

## <a name="mainframe-compute-at-a-glance"></a>Mainframe-számítások áttekintése

A z14 mainframe-ben a processzorok legfeljebb négy *megrajzolási* lehetőséggel vannak elrendezve. A *húzó* egyszerűen a processzorok és a chipsetek fürtje. Minden egyes fióknak hat aktív központi processzor-(CP-) zsetonja lehet, és mindegyik CP 10 rendszervezérlő (SC) zsetonnal rendelkezik. Az Intel x86-terminológiában hat szoftvercsatorna van, 10 mag/szoftvercsatorna és négy húzó. Ez az architektúra 24 szoftvercsatorna és 240 mag, maximális érték esetén a z14 esetében a durva megfelelőt biztosítja.

A Fast z14 CP 5,2 GHz-es órajeltel rendelkezik. A z14 általában az összes CPs-vel érkeznek a mezőbe. Szükség szerint aktiválva vannak. Az ügyfeleket általában legalább négy órányi számítási idő után számítjuk fel havonta a tényleges használat ellenére.

A nagyszámítógépes processzorok a következő típusok egyikének használatával konfigurálhatók:

- Általános célú (GP) processzor
- System z integrált információs processzor (zIIP)
- Integrált Linux-(IFL-) processzor
- System Assist processzor (SAP)
- Integrált összekapcsolási létesítmény (ICF) processzora

## <a name="scaling-mainframe-compute-up-and-out"></a>A nagyszámítógépek számítási teljesítményének méretezése

Az IBM mainframe-EK lehetővé teszi, hogy akár 240 magot is kibővítse (egyetlen rendszer aktuális z14 mérete). Emellett az IBM-es nagyszámítógépek a csatlakozási létesítmény (CF) által kibővíthető funkciókon keresztül is méretezhetők. A CF lehetővé teszi, hogy több nagyszámítógépi rendszer egyidejűleg hozzáférjen ugyanahhoz az adatszolgáltatáshoz. A CF, a nagyszámítógépi párhuzamos Sysplex technológiai csoportok a fürtökben lévő nagyszámítógép-processzorokat használják. Az útmutató megírásakor a Parallel Sysplex funkció 32 64 processzort támogat. Akár 2 048 processzor is csoportosítható a számítási kapacitás felskálázásához.

A CF lehetővé teszi, hogy a számítási fürtök közvetlenül hozzáférhessenek az adatok megosztásához. Ez a zárolási információk, a gyorsítótár-információk és a megosztott adatforrások listájának használata. Egy vagy több CFs-t használó párhuzamos Sysplex a "Shared all" kibővíthető számítási fürtnek tekinthető. További információ ezekről a funkciókról: [Parallel Sysplex on IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources) az IBM webhelyén.

Az alkalmazások ezeket a funkciókat a kibővíthető teljesítmény és a magas rendelkezésre állás biztosítására használhatják. Arról, hogy a CICS hogyan használhat párhuzamos Sysplex a CF használatával, töltse le az [IBM CICS és a kapcsolási létesítményt: az alapvető](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf) Redbook.

## <a name="azure-compute-at-a-glance"></a>Azure-beli számítások áttekintése

Vannak, akik tévesen gondolják, hogy az Intel-alapú kiszolgálók nem annyira hatékonyak, mint a nagyszámítógépek. Az új, alapszintű, az Intel-alapú rendszerek azonban annyi számítási kapacitással rendelkeznek, mint a nagyszámítógépek. Ez a szakasz az Azure infrastruktúra-szolgáltatás (IaaS) beállításait mutatja be a számítástechnika és a tárolás számára. Az Azure a szolgáltatásként nyújtott platformot is biztosítja, de ebben a cikkben a hasonló nagyszámítógép-kapacitást biztosító IaaS-választásokra összpontosítunk.

Az Azure Virtual Machines számos méretben és típusban biztosítja a számítási teljesítményt. Az Azure-ban a virtuális CPU (vCPU) nagyjából a nagyszámítógépek magját hasonlítja.

Jelenleg az Azure-beli virtuálisgép-méretek tartománya 1 – 128 vCPU-t biztosít. A virtuális gép (VM) típusai adott számítási feladatokra vannak optimalizálva. Az alábbi lista például a virtuálisgép-típusokat (az aktuális írást) és a javasolt felhasználási módokat mutatja:

| Méret     | Típus és leírás                                                                 |
|----------|--------------------------------------------------------------------------------------|
| D sorozat | Általános célú 64 vCPU és akár 3,5 GHz-es órajel                           |
| E sorozat | Akár 64 vCPU-re optimalizált memória                                                 |
| F sorozat | A számítási sebesség akár 64 vCPU és 3.7 GHz-es órajeltel van optimalizálva                       |
| H sorozat | Nagy teljesítményű számítástechnikai (HPC) alkalmazások számára optimalizált                          |
| L sorozat | Az adatbázisok, például a NoSQL által támogatott nagy átviteli sebességű alkalmazásokhoz optimalizált tárterület |
| M sorozat | Legnagyobb számítási és memória-optimalizált virtuális gépek akár 128 vCPU                        |

További információ az elérhető virtuális gépekről: [virtuálisgép-sorozat](https://azure.microsoft.com/pricing/details/virtual-machines/series/).

A z14 nagyszámítógépek akár 240 maggal is rendelkezhetnek. A z14-nagyszámítógépek azonban szinte soha nem használják az összes magot egyetlen alkalmazáshoz vagy munkaterheléshez. Ehelyett a nagyszámítógépek elkülönítik a számítási feladatokat logikai partíciókra (LPARs), és a LPARs minősítéssel rendelkeznek – a MIPS (másodpercenként több millió utasítás) vagy MSU (millió szolgáltatási egység). A mainframe-feladatok Azure-ban való futtatásához szükséges hasonló virtuálisgép-méret meghatározásakor a következő tényezőt kell figyelembe venni: MIPS (vagy MSU) minősítés.

Az általános becslések a következők:

-   150 MIPS/vCPU

-   1 000 MIPS/processzor

A virtuális gép megfelelő méretének meghatározásához egy LPAR egy adott munkaterhelés esetében először optimalizálja a virtuális gépet a munkaterhelés számára. Ezután határozza meg, hogy hány vCPU szükséges. A konzervatív becslés 150 MIPS/vCPU. A becslés alapján például egy 16 vCPU rendelkező F sorozatú virtuális gép könnyedén támogat egy, az 2 400 MIPS-vel rendelkező LPAR érkező IBM DB2-munkaterhelést.

## <a name="azure-compute-scale-up"></a>Azure-beli számítási felskálázás

Az M sorozatú virtuális gépek akár 128 vCPU is méretezhetők (a cikk írásának időpontjában). Az M sorozatú virtuális gép a 150 MIPS vCPU-es konzervatív becslést használva a 19 000 MIPS-et használja. A MIPS-becslés általános szabálya a mainframe esetében 1 000 MIPS. A z14-nagyszámítógépek legfeljebb 24 processzorral rendelkezhetnek, és 24 000 MIPS-et biztosítanak egyetlen mainframe rendszerhez.

A legnagyobb egyetlen z14-mainframe körülbelül 5 000 MIPS-rel rendelkezik, mint az Azure-ban elérhető legnagyobb virtuális gép. Ugyanakkor fontos összehasonlítani a számítási feladatok üzembe helyezésének módját. Ha a nagyszámítógéprendszer egy alkalmazással és egy kapcsolati adatbázissal is rendelkezik, azok általában ugyanazon a fizikai nagyszámítógépeken vannak telepítve, amelyek mindegyike a saját LPAR. Ugyanezt az Azure-megoldást gyakran az alkalmazás egy virtuális gépén, egy külön, megfelelően méretű virtuális gépen helyezik üzembe az adatbázishoz.

Ha például egy M64-vCPU rendszer támogatja az alkalmazást, és M96 vCPU használ az adatbázishoz, körülbelül 150 vCPU szükséges – vagy körülbelül 24 000 MIPS, ahogy az alábbi ábrán látható.

![A 24 000 MIPS munkaterhelések összevetésének összehasonlítása](media/mainframe-compute-mips.png)

A módszer a LPARs áttelepítése az egyes virtuális gépekre. Ezt követően az Azure könnyedén méretezhető az egyetlen nagyszámítógépi rendszeren üzembe helyezett legtöbb alkalmazáshoz szükséges méretig.

## <a name="azure-compute-scale-out"></a>Azure számítási felskálázás

Az Azure-alapú megoldás egyik előnye, hogy kibővíthető. A skálázás szinte korlátlan számítási kapacitást tesz elérhetővé egy alkalmazás számára. Az Azure több módszert is támogat a számítási teljesítmény kiskálázásához:

- **Terheléselosztás fürtön keresztül.** Ebben az esetben az alkalmazás egy [terheléselosztó](../../../../load-balancer/load-balancer-overview.md) vagy egy erőforrás-kezelő használatával terjeszti ki a munkaterhelést a fürtben lévő több virtuális gép között. Ha további számítási kapacitásra van szükség, további virtuális gépek lesznek hozzáadva a fürthöz.

- **Virtuális gépek méretezési csoportjai.** Ebben a burst forgatókönyvben az alkalmazás a virtuális gépek használata alapján további [számítási erőforrásokra](../../../../virtual-machine-scale-sets/overview.md) is méretezhető. Ha az igény csökken, a méretezési csoportba tartozó virtuális gépek száma is lemehet, így biztosítva a számítási teljesítmény hatékony kihasználását.

- **Pásti skálázás.** Az Azure-beli Pásti-ajánlatok méretezése számítási erőforrások. Az [Azure Service Fabric](../../../../service-fabric/service-fabric-overview.md) például a kérelmek mennyiségének növeléséhez a számítási erőforrásokat foglalja le.

- **Kubernetes-fürtök.** Az Azure-beli alkalmazások [Kubernetes-fürtöket](../../../../aks/concepts-clusters-workloads.md) használhatnak a számítási szolgáltatások számára a megadott erőforrásokhoz. Az Azure Kubernetes Service (ak) egy felügyelt szolgáltatás, amely Kubernetes-csomópontokat,-készleteket és-fürtöket szervez az Azure-ban.

A számítási erőforrások méretezésének megfelelő módszer kiválasztásához fontos megérteni, hogy az Azure és a nagyszámítógépek hogyan térnek el egymástól. A legfontosabb az, hogy hogyan történik az adatok megosztása a számítási erőforrások között. Az Azure-ban általában nem több virtuális gép osztja meg az adattípusokat (alapértelmezés szerint). Ha több virtuális gép igényel adatmegosztást egy kibővíthető számítási fürtben, a megosztott adatoknak olyan erőforráson kell lenniük, amely támogatja ezt a funkciót. Az Azure-ban az adatmegosztás magában foglalja a tárolást a következő szakasz ismerteti.

## <a name="azure-compute-optimization"></a>Az Azure számítási optimalizálása

Egy Azure-architektúrában optimalizálhatja az egyes feldolgozási szinteket. Az egyes környezetekhez legmegfelelőbb virtuális gépeket és szolgáltatásokat használhatja. Az alábbi ábra egy lehetséges mintát mutat be a virtuális gépek Azure-beli üzembe helyezéséhez, hogy támogassa a DB2-t használó CICS alkalmazást. Az elsődleges helyen a termelés, az üzem előtt álló és a tesztelési virtuális gépek magas rendelkezésre állással vannak üzembe helyezve. A másodlagos hely a biztonsági mentéshez és a vész-helyreállításhoz szükséges.

Minden egyes szinten a megfelelő vész-helyreállítási szolgáltatások is megadhatók. Előfordulhat például, hogy a termelési és az adatbázis-alapú virtuális gépek gyors vagy meleg helyreállítást igényelnek, míg a fejlesztési és tesztelési virtuális gépek a hideg helyreállítást támogatják.

![A vész-helyreállítást támogató, magasan elérhető központi telepítés](media/mainframe-compute-dr.png)

## <a name="next-steps"></a>Következő lépések

- [Nagyszámítógép migrálása](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Az Azure Virtual Machines-t futtató nagyszámítógépek](../overview.md)
- [Nagyszámítógépes tároló áthelyezése az Azure-ba](mainframe-storage-Azure.md)

### <a name="ibm-resources"></a>IBM-erőforrások

- [Párhuzamos Sysplex az IBM Z-ben](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [Az IBM CICS és a kapcsolási létesítmény: az alapjain túl](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Szükséges felhasználók létrehozása a DB2 pureScale szolgáltatás telepítéséhez](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt – példány létrehozása parancs](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [DB2 pureScale fürtözött adatbázis-megoldás](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM-es adatstúdió](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Microsoft Azure Government felhő nagyszámítógépi alkalmazások számára](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft és FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>További áttelepítési erőforrások

- [Az Azure Virtual adatközpontjának átemelési és átváltási útmutatója](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://glusterdocs.readthedocs.io/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
