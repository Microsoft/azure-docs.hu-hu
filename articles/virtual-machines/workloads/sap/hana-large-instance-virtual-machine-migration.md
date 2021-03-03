---
title: SAP HANA migrálása az Azure-ban (nagyméretű példányok) az Azure Virtual Machines szolgáltatásba | Microsoft Docs
description: SAP HANA migrálása az Azure-ban (nagyméretű példányok) Azure-beli virtuális gépekre
services: virtual-machines-linux
documentationcenter: ''
author: bentrin
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/11/2020
ms.author: bentrin
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cd1cfb0cc8e1868e78b4d284d1b1f4e7e85aa318
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101677042"
---
# <a name="sap-hana-on-azure-large-instance-migration-to-azure-virtual-machines"></a>SAP HANA Azure-beli nagyméretű példányok áttelepítése az Azure-ba Virtual Machines
Ez a cikk a nagyméretű Azure-példányok lehetséges üzembe helyezési forgatókönyveit ismerteti, és tervezési és áttelepítési módszert kínál a lehető legkevesebb átmeneti állásidő

## <a name="overview"></a>Áttekintés
A nagySAP HANA (HLI) Azure nagyméretű példányainak bejelentése óta a 2016-es verzióban számos ügyfelünk fogadta el ezt a hardvert a memóriában lévő számítási platform szolgáltatásként.  Az elmúlt években az Azure-beli virtuálisgép-méretezési bővítmény a HANA-kibővített üzembe helyezés támogatásával túllépte a nagyvállalati ügyfelek ERP-adatbázisának kapacitását.  Kezdjük azon ügyfeleinket látni, akik érdeklődnek arra, hogy áttelepítsek SAP HANA számítási feladatait a fizikai kiszolgálókról az Azure-beli virtuális gépekre.
Ez az útmutató nem egy lépésenkénti konfigurációs dokumentum. Ismerteti a közös üzembe helyezési modelleket, és tervezési és áttelepítési tanácsokat kínál.  A cél az áttérési állásidő minimalizálásához szükséges megfontolások meghívása.

## <a name="assumptions"></a>Feltételezések
Ez a cikk a következő feltételezéseket biztosítja:
- Az egyetlen megfontolandó érdek egy homogén HANA-adatbázis számítási szolgáltatásának áttelepítése a Hana Large instance (HLI)-ből az Azure virtuális gépre jelentős szoftverfrissítés vagy javítás nélkül. Ezek a kisebb frissítések a megfelelő SAP-megjegyzések által támogatottnál újabb operációsrendszer-verzió vagy HANA-verzió használatát tartalmazzák. 
- Az összes frissítést/frissítést el kell végezni az áttelepítés előtt vagy után.  Például SAP HANA MCOS konvertálása MDC-telepítésre. 
- A lehető legkevesebb állásidőt biztosító áttelepítési megközelítés SAP HANA a rendszer replikálását. Más áttelepítési módszerek nem tartoznak a dokumentum hatókörébe.
- Ez az útmutató a HLI Rev3 és Rev4 SKU-ra is érvényes.
- A HANA üzembe helyezési architektúrája elsősorban változatlan marad az áttelepítés során.  Ez azt eredményezi, hogy egy önálló példánnyal rendelkező rendszer ugyanúgy marad a célhelyen.
- Az ügyfelek áttekintették és megértették a cél (-be) architektúrájának szolgáltatói szerződés (SLA). 
- A HLIs és a virtuális gépek közötti kereskedelmi feltételek eltérőek. Az ügyfeleknek figyelniük kell a virtuális gépek használatát a Cost Management szolgáltatásban.
- Az ügyfelek tisztában vannak azzal, hogy a HLI dedikált számítási platform, míg a virtuális gépek megosztott, de elkülönített infrastruktúrán futnak.
- Az ügyfelek ellenőrizték, hogy a cél virtuális gépek támogatják a kívánt architektúrát. Az SAP HANA-telepítésre tanúsított összes támogatott virtuálisgép-SKU megjelenítéséhez tekintse meg a [SAP HANA hardver könyvtárat](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).
- Az ügyfelek ellenőrizték a tervezési és áttelepítési tervet.
- Tervezze meg a vész-helyreállítási virtuális gépet az elsődleges hellyel együtt.  Az ügyfelek nem használhatják a HLI az áttelepítés után virtuális gépeken futó elsődleges hely DR-csomópontjának.
- Az ügyfelek az üzleti helyreállítás és a megfelelőségi követelmények alapján másolták a szükséges biztonságimásolat-fájlokat a cél virtuális gépekre. A virtuális gépekről elérhető biztonsági másolatok lehetővé teszik az időponthoz való visszaállítást az átmeneti időszakban.
- Ha a HSR, az ügyfeleknek be kell állítania és konfigurálniuk kell a STONITH-eszközt a [SLES](./high-availability-guide-suse-pacemaker.md) és a [RHEL](./high-availability-guide-rhel-pacemaker.md)-hez SAP HANA ha útmutatók.  Nincs előre konfigurálva, mint a HLI eset.
- Ez az áttelepítési módszer nem fedi le a Optane-konfigurációval rendelkező HLI SKU-ket.

## <a name="deployment-scenarios"></a>Üzembe helyezési forgatókönyvek
A HLI-ügyfelekkel közös üzembe helyezési modellek a következő táblázatban vannak összegezve.  Az Azure-beli virtuális gépekre való Migrálás minden HLI-forgatókönyv esetén lehetséges.  A további elérhető Azure-szolgáltatások előnyeit kihasználva szükség lehet kisebb építészeti változásokra.

| Forgatókönyv azonosítója | HLI forgatókönyv | Áttelepítés a Verbatim virtuális gépre? | Megjegyzés |
| --- | --- | --- | --- |
| 1 | [Egyetlen csomópont egyetlen SID-vel](./hana-supported-scenario.md#single-node-with-one-sid) | Igen | - |
| 2 | [Egyetlen csomópont a MCOS](./hana-supported-scenario.md#single-node-mcos) | Igen | - |
| 3 | [Önálló csomópont a Storage-replikációt használó DR használatával](./hana-supported-scenario.md#single-node-with-dr-using-storage-replication) | Nem | A Storage-replikáció nem érhető el az Azure Virtual platformon, és nem változtathatja meg a jelenlegi DR-megoldást HSR vagy biztonsági mentésre vagy visszaállításra |
| 4 | [Egyetlen csomópont DR (többcélú) tároló-replikáció használatával](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication) | Nem | A Storage-replikáció nem érhető el az Azure Virtual platformon, és nem változtathatja meg a jelenlegi DR-megoldást HSR vagy biztonsági mentésre vagy visszaállításra |
| 5 | [HSR és STONITH a magas rendelkezésre állás érdekében](./hana-supported-scenario.md#hsr-with-stonith-for-high-availability) | Igen | Nincsenek előre konfigurált SBD a cél virtuális gépekhez.  Válasszon ki és helyezzen üzembe egy STONITH-megoldást.  Lehetséges beállítások: Azure vívó Agent ( [RHEL](./high-availability-guide-rhel-pacemaker.md), [SLES](./high-availability-guide-suse-pacemaker.md)), SBD |
| 6 | [HA a HSR, a DR és a Storage replikációja](./hana-supported-scenario.md#high-availability-with-hsr-and-dr-with-storage-replication) | Nem | Cserélje le a DR HSR vagy a Backup/Restore szolgáltatáshoz tartozó tárolási replikációt |
| 7 | [Gazdagép automatikus feladatátvétele (1 + 1)](./hana-supported-scenario.md#host-auto-failover-11) | Igen | ANF használata megosztott tárolóhoz Azure-beli virtuális gépekkel |
| 8 | [Kibővíthető készenléti állapottal](./hana-supported-scenario.md#scale-out-with-standby) | Igen | BW/4HANA a M128s, a M416s, a M416ms virtuális gépek és a ANF használatával |
| 9 | [Vertikális felskálázás készenlét nélkül](./hana-supported-scenario.md#scale-out-without-standby) | Igen | BW/4HANA a M128s, a M416s és a M416ms virtuális gépekkel (a Storage-ANF használatával vagy anélkül) |
| 10 | [Méretezés a DR használatával a Storage replikálásával](./hana-supported-scenario.md#scale-out-with-dr-using-storage-replication) | Nem | Cserélje le a DR HSR vagy a Backup/Restore szolgáltatáshoz tartozó tárolási replikációt |
| 11 | [Egyetlen csomópont a DR használatával HSR](./hana-supported-scenario.md#single-node-with-dr-using-hsr) | Igen | - |
| 12 | [Egyetlen csomópontos HSR a DR (Cost optimalizált)](./hana-supported-scenario.md#single-node-hsr-to-dr-cost-optimized) | Igen | - |
| 13 | [HA és DR a HSR](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr) | Igen | - |
| 14 | [HA és DR a HSR (Cost optimalizált)](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr-cost-optimized) | Igen | - |
| 15 | [Vertikális felskálázás DR használatával HSR](./hana-supported-scenario.md#scale-out-with-dr-using-hsr) | Igen | BW/4HANA a M128s. M416s, M416ms virtuális gépek (ANF használatával vagy anélkül) |


## <a name="source-hli-planning"></a>Forrás (HLI) tervezése
Egy HLI-kiszolgáló bevezetéséhez a Microsoft Service Management és az ügyfelek is megtervezték a számítási, hálózati, tárolási és operációsrendszer-specifikus beállításokat a SAP HANA adatbázis futtatásához.  Hasonló tervezésre van szükség az Azure-beli virtuális gépre való Migrálás során.

### <a name="sap-hana-housekeeping"></a>SAP HANA háztartás 
Az adatbázis tartalmának kiosztása jó működési gyakorlat, így nemkívánatos, elavult vagy elavult naplók nem lesznek áttelepítve az új adatbázisba.  A háztartások általában a régi, lejárt vagy inaktív adatmennyiségek törlését vagy archiválását foglalják magukban.  Ezeket az adathigiéniai műveleteket nem éles környezetben kell tesztelni, hogy a termelési használat előtt ellenőrizni tudják az adatvágási állapotuk érvényességét.

### <a name="allow-network-connectivity-for-new-vms-and-or-virtual-network"></a>Hálózati kapcsolat engedélyezése az új virtuális gépek és a virtuális hálózatok számára 
Az ügyfél HLI üzembe helyezése során a hálózat beállítása a [SAP HANA (nagyméretű példányok) hálózati architektúrája](./hana-network-architecture.md)című cikkben ismertetett információk alapján történt. Emellett a hálózati forgalom útválasztását az "útválasztás az Azure-ban" szakaszban ismertetett módon végezheti el.
- Ha az új virtuális GÉPET áttelepítési célként állítja be, akkor ha a meglévő virtuális hálózatban olyan IP-címtartományok vannak elhelyezve, amelyek már engedélyezve vannak a HLI való csatlakozáshoz, nincs szükség további kapcsolódási frissítésre.
- Ha az új Azure-beli virtuális gép új Microsoft Azure Virtual Networkba kerül, lehet, hogy egy másik régióban található, és a meglévő virtuális hálózattal van társítva, akkor az eredeti HLI-kiépítés ExpressRoute-szolgáltatási kulcsa és erőforrás-azonosítója használható az új virtuális hálózati IP-címtartomány elérésének engedélyezéséhez.  A Microsoft Service Management szolgáltatással koordinálhatja, hogy a virtuális hálózat HLI a kapcsolatot.  Megjegyzés: az alkalmazás és az adatbázis rétegek közötti hálózati késés csökkentése érdekében az alkalmazás-és az adatbázis-rétegnek ugyanazon a virtuális hálózaton kell lennie.  

### <a name="existing-app-layer-availability-set-availability-zones-and-proximity-placement-group-ppg"></a>Meglévő alkalmazás-réteg rendelkezésre állási csoport, Availability Zones és közelségi csoportok (PPG)
A jelenlegi üzembe helyezési modell bizonyos szolgáltatási szintű célkitűzések kielégítése érdekében történik.  Ebben a mozgásban győződjön meg arról, hogy a cél-infrastruktúra megfelel vagy meghaladja a beállított célokat.  
Valószínűleg nem, az ügyfelek SAP-alkalmazáskiszolgáló egy rendelkezésre állási csoportba kerül.  Ha a jelenlegi központi telepítési szolgáltatási szint kielégítő, és 
- Ha a célként megadott virtuális gép feltételezi, hogy a HLI logikai neve állomásnév, a DNS-cím frissítése a virtuális gép IP-címére mutat, és az SAP-profilok frissítése nélkül működik.
- Ha nem a PPG-t használja, ügyeljen arra, hogy az összes alkalmazás-és adatbázis-kiszolgálót ugyanabban a zónában helyezze el, hogy csökkentse a hálózati késést.
- Ha a PPG-t használja, tekintse meg a jelen dokumentum szakaszát: "cél megtervezése, rendelkezésre állási csoport, Availability Zones és közelség elhelyezése (PPG)".

### <a name="storage-replication-discontinuance-process-if-used"></a>Tárolási replikálás megszakítási folyamata (ha használatban van)
Ha a tárolási replikáció DR-megoldásként van használatban, azt az SAP-alkalmazás leállítása után meg kell szakítani (nem ütemezett).  Emellett az utolsó SAP HANA katalógus, a naplófájl és az adatok biztonsági mentése a távoli DR HLI-tároló köteteire lettek replikálva.  Ez óvintézkedést jelent arra az esetre, ha a fizikai kiszolgáló az Azure-beli virtuális gépekre való áttérés során vész végbe.

### <a name="data-backups-preservation-consideration"></a>Az adatbiztonsági másolatok megőrzésének megfontolása
Miután az Azure-beli virtuális gépen SAP HANA az átméretezést, a HLI lévő összes pillanatkép-alapú adat vagy napló biztonsági mentése nem egyszerűen hozzáférhető vagy nem állítható be a virtuális gépre, ha szükséges. A korai átmeneti időszakban az Azure-alapú biztonsági mentés elég előzményeket hoz létre az időponthoz tartozó helyreállítási követelmények kielégítése érdekében, a pillanatképek mellett a HLI, napokat vagy heteket is figyelembe kell venni.  Ezeket a biztonsági másolatokat az új SAP HANA virtuális gép által elérhető Azure Storage-fiókba másolta.
A HLI-tartalom biztonsági mentése mellett körültekintően kell lennie arra, hogy az SAP-környezet teljes biztonsági másolatait azonnal elérhetővé váljon, ha visszaállításra van szükség.

### <a name="adjusting-system-monitoring"></a>A rendszer figyelésének módosítása 
Az ügyfelek számos különböző eszközt használnak az SAP-környezetben lévő rendszerekre vonatkozó riasztási értesítések figyelésére és küldésére.  Ez az érték csak a megfelelő művelet meghívása, amely a riasztási értesítés címzettjeinek módosítását és szükség esetén frissítését foglalja magában.

### <a name="microsoft-operations-team-involvement"></a>A Microsoft Operations Team bevonása 
Nyisson meg egy jegyet a Azure Portal a meglévő HLI-példány alapján.  A támogatási jegy létrehozása után a támogatási szakember e-mailben fogja felvenni Önnel a kapcsolatot.  

### <a name="engage-microsoft-account-team"></a>Részvétel Microsoft-fiók csapatban
Tervezze meg az áttelepítést a HLI-szerződés évfordulós megújítási ideje felé, hogy csökkentse a számítási erőforrásokra vonatkozó felesleges terheket. A HLI panel leszereléséhez szükség van a szerződés lezárásának és az egység tényleges leállásának koordinálására.

## <a name="destination-planning"></a>Célhely tervezése
Egy új infrastruktúra kiépítésével egy meglévőt kell megérdemelni annak érdekében, hogy az új Hozzáadás a dolgok nagy rendjében is illeszkedjen.  Az alábbiakban néhány kulcsfontosságú pontot talál a szemlélődés számára.

### <a name="resource-availability-in-the-target-region"></a>Erőforrás rendelkezésre állása a célként megadott régióban 
A jelenlegi SAP-alkalmazáskiszolgáló központi telepítési régiója általában közel van a társított HLIs.  A HLIs azonban a rendelkezésre álló Azure-régióknál kevesebb helyen kínáljuk.  Amikor áttelepíti a fizikai HLI az Azure-beli virtuális gépre, az is jó idő, hogy a teljesítmény optimalizálása érdekében felhasználja az összes kapcsolódó szolgáltatás közelségi távolságát.  Eközben az egyik kulcsfontosságú szempont, hogy a kiválasztott régió minden szükséges erőforrással rendelkezzen.  Például a magas rendelkezésre állású virtuális gépek családjának, illetve az Azure-zónák ajánlatának rendelkezésre állása.

### <a name="virtual-network"></a>Virtuális hálózat 
Az ügyfeleknek ki kell választaniuk, hogy az új HANA-adatbázist meglévő virtuális hálózaton szeretné-e futtatni, vagy újat kíván létrehozni.  Az elsődleges döntési tényező az SAP-környezet aktuális hálózati elrendezése.  Akkor is, ha az infrastruktúra az egyik zónából a kétzónás üzemelő példányba kerül, és a PPG-t használja, ez az architektúra-változást is alkalmazza. További információkért tekintse [meg az Azure PPG optimális hálózati késése az SAP-alkalmazással](./sap-proximity-placement-scenarios.md)című cikket.   

### <a name="security"></a>Biztonság
Azt jelzi, hogy az új SAP HANA virtuális gép új vagy meglévő vnet/alhálózaton van-e, egy új, üzleti szempontból kritikus fontosságú szolgáltatást jelent, amely védelmet igényel.  A vállalati adatok biztonsági házirendjének megfelelő hozzáférés-vezérlést ki kell értékelni és telepíteni kell az új szolgáltatási osztályhoz.

### <a name="vm-sizing-recommendation"></a>VM-méretezési javaslat
Ez a Migrálás lehetőséget biztosít a HANA számítási motor megfelelő méretének kiszámítására.  A Hana Studióval együtt a HANA [rendszernézeteket](https://help.sap.com/viewer/7c78579ce9b14a669c1f3295b0d8ca16/Cloud/3859e48180bb4cf8a207e15cf25a7e57.html) is használhatja a rendszererőforrás-felhasználás megismeréséhez, ami lehetővé teszi a jobb méretezést a kiadások hatékonyságának szabályozása érdekében.

### <a name="storage"></a>Tárolás 
A tárolási teljesítmény az SAP-alkalmazások felhasználói élményét befolyásoló tényezők egyike.  Egy adott VM-SKU-ra alapozva SAP HANA Azure-beli [virtuális gépek tárolási konfigurációinak](./hana-vm-operations-storage.md)minimális tárolási elrendezése van közzétéve. Javasoljuk, hogy tekintse át ezeket a minimális specifikációkat, és hasonlítsa össze a meglévő HLI rendszerstatisztikával, hogy biztosítsa az új HANA-beli virtuális gép megfelelő i/o-kapacitását és teljesítményét.

Ha a PPG-t az új HANA-beli virtuális géphez és a hozzá tartozó alrendszerekhez konfigurálja, küldjön egy támogatási jegyet a tároló és a virtuális gép közös elhelyezésére és ellenőrzésére. Mivel előfordulhat, hogy a biztonsági mentési megoldás módosítására van szükség, a tárolási költségeket is újra kell látni, hogy elkerülje a működési kiadások meglepetéseit.

### <a name="storage-replication-for-disaster-recovery"></a>Tárolási replikáció a vész-helyreállításhoz
A HLI esetében a rendszer a tárolási replikációt a vész-helyreállítási alapértelmezett beállításként kínálja. Ez a funkció nem az alapértelmezett beállítás az Azure-beli virtuális gépen SAP HANA. Vegye fontolóra az üzleti igényeinek megfelelő HSR, biztonsági mentési/helyreállítási vagy más támogatott megoldásokat.

### <a name="availability-sets-availability-zones-and-proximity-placement-groups"></a>Rendelkezésre állási csoportok, Availability Zones és Proximity elhelyezési csoportok 
Az alkalmazási réteg és a SAP HANA közötti távolság lerövidítése a hálózati késés minimálisra csökkentése érdekében az új adatbázis virtuális gépet és az aktuális SAP-alkalmazás-kiszolgálókat a PPG-be kell helyezni. Ha szeretné megtudni, hogyan működik együtt az Azure-beli rendelkezésre állási csoport és a Availability Zones a PPG SAP-környezetek számára, tekintse meg a [közelséget](./sap-proximity-placement-scenarios.md) .
Ha a cél HANA rendszer tagjai több Azure-zónában vannak telepítve, akkor az ügyfeleknek egyértelmű képet kell kapniuk a kiválasztott zónák késési profiljáról. Az SAP-rendszerösszetevők elhelyezése optimális az SAP-alkalmazás és az adatbázis közötti proximális távolság tekintetében.  A nyilvános tartomány [rendelkezésre állási zónájának késési teszt eszköze](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) megkönnyíti a mérést.  


### <a name="backup-strategy"></a>Biztonsági mentési stratégia
Számos ügyfél már használ külső gyártótól származó biztonsági mentési megoldásokat a HLI-SAP HANA.  Ebben az esetben csak egy további védett virtuális gép és HANA-adatbázis konfigurálására van szükség.  A folyamatos HLI biztonsági mentési feladatok mostantól ütemezhetők, ha a gép az áttelepítés után le van szerelve.
A virtuális gépen lévő SAP HANA Azure Backup már általánosan elérhető.  A következő hivatkozásokra kattintva részletes információkat talál: [biztonsági mentés](../../../backup/backup-azure-sap-hana-database.md), [visszaállítás](../../../backup/sap-hana-db-restore.md) [, SAP HANA biztonsági](../../../backup/sap-hana-db-manage.md) mentés az Azure-beli virtuális gépeken.

### <a name="dr-strategy"></a>DR-stratégia
Ha a szolgáltatási szint célkitűzései egy hosszú helyreállítási időt foglalnak magukban, a blob Storage-ba való egyszerű biztonsági mentés és a visszaállítás egy új virtuális gépre való visszaállítása és helyreállítása a legegyszerűbb és legkevésbé költséges DR-stratégia.  
A nagyméretű példány platformhoz hasonlóan, ahol a HANA DR jellemzően a HSR-szel együtt történik; Az Azure-beli virtuális gépen a HSR a legtermészetesebb és a natív SAP HANA DR-megoldás is.  Függetlenül attól, hogy a forrás üzembe helyezése egypéldányos vagy fürtözött-e, a DR régióban kell megadnia a forrás-infrastruktúra replikáját.
Ez a DR-replika akkor lesz konfigurálva, ha az elsődleges HLI a virtuális gép migrálása befejeződött.  A DR HANA DB a virtuálisgép-példány elsődleges SAP HANA a másodlagos replikációs helyként fog regisztrálni.  

### <a name="sap-application-server-connectivity-destination-change"></a>Az SAP alkalmazáskiszolgáló kapcsolódási célhelyének változása
A HSR-áttelepítés egy új HANA DB-gazdagépet eredményez, ezért az alkalmazási réteghez új adatbázis-állomásnév szükséges, az SAP-profilokat az új állomásnévnek megfelelően módosítani kell.  Ha a váltást névfeloldással megőrzi a hostname, a profil módosítása nem szükséges.

### <a name="operating-system"></a>Operációs rendszer
A HLI és a virtuális gép operációsrendszer-lemezképei, annak ellenére, hogy ugyanazon a kiadási szinten vannak, a SLES 12 SP4 például nem azonos. Az ügyfeleknek ellenőriznie kell a szükséges csomagokat, a gyakori javításokat, a javításokat, a kernelt és a biztonsági javításokat a HLI, hogy ugyanazt a csomagokat telepítse a célhelyre.  A HSR használatával egy régebbi operációs rendszerről replikálhat egy virtuális gépre egy újabb operációsrendszer-verzióval.  Ellenőrizze az egyes támogatott verziókat a 2763388-es [SAP-Megjegyzés](https://launchpad.support.sap.com/#/notes/2763388)áttekintésével.

### <a name="new-sap-license-request"></a>Új SAP-licencelési kérelem
Egy egyszerű hívás, amely új SAP-licencet igényel az új HANA-rendszer számára, hogy áttelepítse a virtuális gépekre.

### <a name="service-level-agreement-sla-differences"></a>A szolgáltatói szerződés (SLA) közötti különbségek
A szerzők Szeretnék meghívni a HLI és az Azure-beli virtuális gép közötti rendelkezésre állási SLA különbségeit.  Például a fürtözött HLIs HA pár 99,99%-os rendelkezésre állást kínál. Ha ugyanazt a SLA-t szeretné elérni, egy virtuális gépet kell üzembe helyezni a rendelkezésre állási zónákban. Ez a [cikk](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) a kapcsolódó üzembe helyezési architektúrákkal való rendelkezésre állást ismerteti, hogy az ügyfelek ennek megfelelően tervezzék meg a cél infrastruktúrát.


## <a name="migration-strategy"></a>Migrálási stratégia
Ebben a dokumentumban csak a HANA rendszer replikációs megközelítését fedi le a HLI-ről az Azure virtuális gépre való áttelepítésre.  Az üzembe helyezett cél tárolási megoldástól függ, a folyamat némileg eltér. A magas szintű lépéseket az alábbiakban ismertetjük.

### <a name="vm-with-premiumultra-disks-for-data"></a>Virtuális gép Premium/Ultra-Disks szolgáltatással
A prémium vagy ultrakönnyű lemezekkel üzembe helyezett virtuális gépek esetében a szabványos SAP HANA rendszer-replikációs konfiguráció a HSR beállításához szükséges.  Az [SAP-súgótémakör](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/099caa1959ce4b3fa1144562fa09e163.html) áttekintést nyújt a rendszerreplikáció beállításával, a másodlagos rendszer átvételével, az elsődleges és a rendszerreplikáció letiltásával kapcsolatban.  Az áttelepítés szempontjából csak a telepítésre, a átvételre és a replikálási lépések elvégzésére lesz szükség.  

### <a name="vm-with-anf-for-data-and-log-volumes"></a>ANF-alapú virtuális gép adatés naplózási kötetekhez
Magas szinten a teljes adat-és naplózási kötetek legújabb HLI-tárolási pillanatképeit át kell másolni az Azure Storage-ba, ahol elérhetők és a cél HANA virtuális gép helyreállítható.  A másolási folyamat bármilyen natív linuxos másolási eszközzel elvégezhető.  

> [!IMPORTANT]
> A másolás és az adatátvitel órákig változhat a HANA-adatbázis méretétől és a hálózati sávszélességtől.  A másolási folyamat nagy részét az elsődleges HANA-adatbázis leállása előtt kell elvégezni.

### <a name="mcos-to-mdc-conversion"></a>MCOS – MDC átalakítás
Az egyik rendszerbeli (MCOS) telepítési modell több összetevőjét is használták néhány HLI-ügyfél.  A motiváció az volt, hogy megkerüljék a több adatbázis-tároló (MDC) tárolási pillanatképének korlátozását a korábbi SAP HANA verziók esetében.  A MCOS-modellben több független SAP HANA példány van halmozva egy HLI-panelen.  A HSR használata az áttelepítéshez megfelelően működik, de a több HANA-beli virtuális gépeket, amelyek mindegyike egy bérlői DB-vel rendelkezik.  Ez a végső állapot olyan üzleti környezetet tesz lehetővé, mint amit az ügyfél esetleg megszokt.  A SAP HANA 2,0 alapértelmezett üzembe helyezése a MDC, amely életképes alternatíva a [HANA-bérlők](https://launchpad.support.sap.com/#/notes/2472478) a HSR Migrálás utáni áthelyezéséhez.  Ezzel a folyamattal a független HANA-adatbázisokat egyetlen HANA-tárolóban egyesítheti egymással.  

### <a name="application-layer-consideration"></a>Alkalmazás rétegének megfontolása
Az adatbázis-kiszolgáló egy SAP-rendszer középpontjának tekinthető.  Az összes alkalmazás-kiszolgálónak a SAP HANA adatbázis közelében kell lennie.  Bizonyos esetekben, amikor a PPG új használata szükséges, a meglévő alkalmazás-kiszolgálók áthelyezése arra a PPG-ra, ahol a HANA virtuális gép szükség lehet.  Ha már rendelkezik üzembe helyezési sablonokkal, az új alkalmazás-kiszolgálók létrehozása egyszerűbbnek tekinthető.  
Ha a meglévő alkalmazás-kiszolgálók és az új HANA virtuális gép optimális helyen található, akkor nem szükséges új alkalmazás-kiszolgálókat létrehozni, kivéve, ha további kapacitásra van szükség.  
Ha új infrastruktúra van felépítve a szolgáltatás rendelkezésre állásának növelésére, előfordulhat, hogy a meglévő alkalmazás-kiszolgálók szükségtelenek lesznek, és azokat le kell állítani és törölni kell.
Ha a cél virtuális gép állomásneve megváltozott, és eltér a HLI állomásnévtől, az SAP-alkalmazáskiszolgáló profiljait úgy kell beállítani, hogy az új gazdagépre mutasson.  Ha csak a HANA DB IP-címe módosult, a DNS-rekord frissítése szükséges ahhoz, hogy a bejövő kapcsolatokat az új HANA virtuális géphez lehessen vezetni.

### <a name="acceptance-test"></a>Elfogadási teszt
Bár a HLI-ről a virtuális gépre való Migrálás nem változtatja meg az adatbázis tartalmát a heterogén áttelepítéshez képest, továbbra is javasoljuk az új beállítás legfontosabb funkcióinak és teljesítményének érvényesítését.  

### <a name="cutover-plan"></a>Átváltás-csomag
Bár ez az áttelepítés egyenes előre, azonban egy meglévő adatbázis leszerelését is magában foglalja.  A forrásrendszer a kapcsolódó tartalommal való megőrzése és a biztonsági mentési lemezképek alapos megtervezése kritikus fontosságú abban az esetben, ha tartalék szükséges.  A jó tervezés gyorsabb megfordítást tesz lehetővé.   


## <a name="post-migration"></a>Migráció utáni feladatok
Az áttelepítési feladatot addig nem hajtja végre, amíg az adatok integritásának megőrzése érdekében nem biztos, hogy biztonságosan leválasztott bármilyen HLI szolgáltatást vagy kapcsolatot.  Továbbá állítsa le a szükségtelen szolgáltatásokat.  Ez a szakasz néhány legfontosabb elemet hív meg.

### <a name="decommissioning-the-hli"></a>A HLI leszerelése
Miután sikeresen áttelepíti a HANA-adatbázist az Azure-beli virtuális gépre, gondoskodjon arról, hogy a HLI-adatbázis ne futtasson produktív üzleti tranzakciókat.  Ha azonban a HLI egy ideig fut a helyi biztonsági mentés megőrzési ablakával, akkor biztonságos megoldás, ha szükséges, gyorsabb helyreállítást biztosít.  Az HLI panelt csak ezután szerelje le.  Az ügyfeleknek a Microsoft képviselőivel való kapcsolatfelvételsel kell megkötniük HLI-kötelezettségeiket a Microsoftnak.

### <a name="remove-any-proxy-ex-iptables-bigip-configured-for-hli"></a>Távolítsa el a HLI konfigurált bármely proxyt (pl.: iptables, BIGIP) 
Ha egy proxy szolgáltatás, mint például az iptables, a helyszíni forgalom a HLI felé irányuló és onnan történő irányítására szolgál, már nem lesz szükség a virtuális gépre való sikeres áttelepítés után.  Ezt a kapcsolódási szolgáltatást azonban mindaddig meg kell őrizni, amíg a HLI panel továbbra is fennáll.  Csak azt követően állítsa le a szolgáltatást, hogy a HLI panel teljes leszerelése megtörténik.

### <a name="remove-global-reach-for-hli"></a>A HLI Global Reach eltávolítása 
A Global Reach az ügyfelek ExpressRoute-átjárójának a HLI ExpressRoute-átjáróval való összekapcsolására szolgál.  Lehetővé teszi, hogy az ügyfelek helyszíni forgalma közvetlenül a proxy szolgáltatás használata nélkül is elérje a HLI-bérlőt.  Erre a hálózatra már nincs szükség a HLI egységnek az áttelepítés utáni hiányában.  Az iptables proxy szolgáltatáshoz hasonlóan a GlobalReach is meg kell őrizni, amíg a HLI panel teljes leszerelése meg nem történik.

### <a name="operating-system-subscription--movereuse"></a>Operációs rendszer előfizetése – áthelyezés/újrafelhasználás
Ahogy a virtuálisgép-kiszolgálók felálltak, és a HLI-pengék le vannak szerelve, az operációs rendszer előfizetéseit lecserélheti vagy újra felhasználhatja, hogy elkerülje az operációs rendszer licencének dupla kifizetését.



## <a name="next-steps"></a>Következő lépések
Olvassa el a következő cikkeket:
- [SAP HANA infrastruktúra-konfigurációk és-műveletek az Azure-](./hana-vm-operations.md)ban.
- [SAP-munkaterhelések az Azure-ban: tervezési és üzembe helyezési ellenőrzőlista](./sap-deployment-checklist.md).
