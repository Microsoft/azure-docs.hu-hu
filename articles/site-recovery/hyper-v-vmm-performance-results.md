---
title: Hyper-V virtuális gép replikálásának tesztelése másodlagos helyre a VMM használatával Azure Site Recovery
description: Ez a cikk a VMM-felhőkben lévő Hyper-V virtuális gépeknek a Azure Site Recovery használatával történő replikálására szolgáló teljesítménytesztek adatait ismerteti.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: sutalasi
ms.openlocfilehash: 6c8219214e7053dcf6b119f6cd5dc97daaa355f7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92327637"
---
# <a name="test-results-for-hyper-v-replication-to-a-secondary-site"></a>A Hyper-V replikálásának tesztelési eredményei másodlagos helyre


Ez a cikk az System Center Virtual Machine Manager-(VMM-) felhőkben lévő Hyper-V virtuális gépek egy másodlagos adatközpontba történő replikálásának eredményeit ismerteti.

## <a name="test-goals"></a>Tesztelési célok

A tesztelés célja az volt, hogy megvizsgálják, hogyan végzi el a Site Recovery az állandó állapotú replikáció során.

- Az állandó állapotú replikáció akkor fordul elő, ha a virtuális gépek befejezték a kezdeti replikálást, és szinkronizálják a különbözeti változásokat.
- Fontos, hogy stabil állapottal mérje a teljesítményt, mert az az állapot, amelyben a legtöbb virtuális gép marad, kivéve, ha váratlan kimaradások történnek.
- A tesztelési üzembe helyezés két helyszíni helyből áll, amelyek mindegyike VMM-kiszolgálóval rendelkezik. Ez a tesztelési üzembe helyezés jellemző a központi iroda/fiókiroda üzembe helyezésére, az elsődleges helyként működő főkiszolgálóval és a fiókirodával a másodlagos vagy helyreállítási helyként.

## <a name="what-we-did"></a>Mi volt

A teszt pass:

1. VMM-sablonok használatával létrehozott virtuális gépek.
2. Elindította a virtuális gépeket, és 12 óra alatt rögzítette az alapkonfiguráció teljesítményének mérőszámait.
3. Az elsődleges és a helyreállítási VMM-kiszolgálókon felhőket hozott létre.
4. Site Recovery konfigurált replikáció, beleértve a forrás-és a helyreállítási felhők közötti leképezést is.
5. Engedélyezve van a virtuális gépek védelme, és lehetővé teszi számukra a kezdeti replikáció befejezését.
6. A rendszer stabilizációja pár órát várt.
7. 12 óra alatt rögzített teljesítmény-mérőszámok, ahol az összes virtuális gép a várt replikálási állapotban maradt az adott 12 órában.
8. Az alapteljesítmény-mérőszámok és a replikálási teljesítmény mérőszámai közötti különbözet mérhető.


## <a name="primary-server-performance"></a>Elsődleges kiszolgáló teljesítménye

* A Hyper-V replika (Site Recovery által használt) aszinkron módon követi nyomon a naplófájlok módosításait, az elsődleges kiszolgáló minimális tárolási terhelésével együtt.
* A Hyper-V replika saját karbantartású memória-gyorsítótárat használ a IOPS terhelés csökkentése érdekében. Az írásokat a memóriában tárolja a VHDX, és a naplófájlnak a helyreállítási helyre történő elküldése előtt kiüríti őket a naplófájlba. A lemez kiürítése akkor is megtörténik, ha az írás előre meghatározott korlátot talált.
* Az alábbi ábrán látható az állandó állapot IOPS terhelése a replikáláshoz. Láthatjuk, hogy a replikálás miatti IOPS-terhelés 5% körül van, ami nagyon alacsony.

  ![Diagram, amely az állandó állapotú IOPS-terhelést jeleníti meg a replikáláshoz.](./media/hyper-v-vmm-performance-results/IC744913.png)

A Hyper-V replika az elsődleges kiszolgáló memóriáját használja a lemezek teljesítményének optimalizálása érdekében. Ahogy az az alábbi ábrán is látható, a memória terhelése az elsődleges fürt összes kiszolgálóján marginális. A memória terhelése a replikáció által használt memória százalékos arányát mutatja, a Hyper-V kiszolgáló összes telepített memóriájának összehasonlítva.

![Elsődleges eredmények](./media/hyper-v-vmm-performance-results/IC744914.png)

A Hyper-V replika minimális CPU-terheléssel rendelkezik. Ahogy az ábrán is látható, a replikálási terhelés a 2-3%-os tartományba esik.

![A replikálási terhelést bemutató gráf 2-3%-os tartományba esik.](./media/hyper-v-vmm-performance-results/IC744915.png)

## <a name="secondary-server-performance"></a>Másodlagos kiszolgáló teljesítménye

A Hyper-V replika kis mennyiségű memóriát használ a helyreállítási kiszolgálón a tárolási műveletek számának optimalizálása érdekében. A Graph összefoglalja a memória használatát a helyreállítási kiszolgálón. A memória terhelése a replikáció által használt memória százalékos arányát mutatja, a Hyper-V kiszolgáló összes telepített memóriájának összehasonlítva.

![Az a gráf, amely összegzi a memória használatát a helyreállítási kiszolgálón.](./media/hyper-v-vmm-performance-results/IC744916.png)

A helyreállítási helyen lévő I/O-műveletek mennyisége az írási műveletek számának függvénye az elsődleges helyen. Tekintse meg a helyreállítási helyen lévő összes I/O-műveletet a teljes I/O-műveletekkel és az elsődleges helyen található írási műveletekkel összehasonlítva. A diagramok azt mutatják, hogy a helyreállítási hely teljes IOPS

* Körülbelül 1,5 alkalommal az írási IOPS az elsődlegesen.
* Az elsődleges hely teljes IOPS körülbelül 37%-a.

![Az elsődleges és másodlagos helyek IOPS összehasonlítását bemutató gráf.](./media/hyper-v-vmm-performance-results/IC744917.png)

![Másodlagos eredmények](./media/hyper-v-vmm-performance-results/IC744918.png)

## <a name="effect-on-network-utilization"></a>A hálózat kihasználtságának hatása

A hálózati sávszélesség átlagosan 275 MB-os átlagát használták az elsődleges és a helyreállítási csomópontok (a tömörítést engedélyező) között, egy meglévő, 5 GB/s sávszélességgel.

![Eredmények hálózati kihasználtsága](./media/hyper-v-vmm-performance-results/IC744919.png)

## <a name="effect-on-vm-performance"></a>A virtuális gép teljesítményére gyakorolt hatás

Fontos szempont, hogy a replikáció a virtuális gépeken futó éles munkaterhelésekre is hatással van. Ha az elsődleges hely megfelelően van kiépítve a replikáláshoz, akkor nem lesz hatással a munkaterhelésekre. A Hyper-V replika Lightweight követési mechanizmusa biztosítja, hogy a virtuális gépeken futó munkaterhelések ne legyenek hatással az állandósult állapotú replikáció során. Ezt a következő diagramok szemléltetik.

Ez a diagram a replikáció engedélyezése előtt és után a különböző számítási feladatokat futtató virtuális gépek által végrehajtott IOPS jeleníti meg. Azt is megfigyelheti, hogy a kettő között nincs különbség.

![Replika hatásának eredményei](./media/hyper-v-vmm-performance-results/IC744920.png)

A következő gráf a replikáció engedélyezése előtt és után a különböző számítási feladatokat futtató virtuális gépek átviteli sebességét jeleníti meg. Azt is megfigyelheti, hogy a replikációnak nincs jelentős hatása.

![Eredmények replikájának hatásai](./media/hyper-v-vmm-performance-results/IC744921.png)

## <a name="conclusion"></a>Összegzés

Az eredmények egyértelműen azt mutatják, hogy Site Recovery, a Hyper-V replikával párosítva, jól méretezhető a nagyméretű fürtök minimális terhelésével. Site Recovery egyszerű üzembe helyezést, replikálást, felügyeletet és figyelést tesz lehetővé. A Hyper-V replika biztosítja a szükséges infrastruktúrát a replikálás sikeres skálázásához. 

## <a name="test-environment-details"></a>Tesztkörnyezet részletei

### <a name="primary-site"></a>Elsődleges hely

* Az elsődleges hely olyan fürttel rendelkezik, amely öt Hyper-V-kiszolgálót tartalmaz, és 470 virtuális gépet futtat.
* A virtuális gépek eltérő munkaterheléseket futtatnak, és az összes Site Recovery védelem engedélyezve van.
* A fürtcsomópont tárolóját egy iSCSI SAN biztosítjuk. Model – Hitachi HUS130.
* Mindegyik fürthöz négy hálózati kártya (NIC) tartozik.
* A hálózati kártyák közül kettő egy iSCSI-magánhálózathoz csatlakozik, és kettő egy külső vállalati hálózathoz csatlakozik. Az egyik külső hálózat csak a fürt kommunikációja számára van fenntartva.

![Elsődleges hardverkövetelmények](./media/hyper-v-vmm-performance-results/IC744922.png)

| Kiszolgáló | RAM | Modellezés | Processzor | Processzorok száma | Hálózati adapter | Szoftverek |
| --- | --- | --- | --- | --- | --- | --- |
| Hyper-V-kiszolgálók a fürtben: <br />ESTLAB-HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25 |128<br />ESTLAB – a HOST25 256 |Dell™ PowerEdge™ R820 |Intel (R) Xeon (R) CPU E5 – 4620 0 \@ 2.20 GHz |4 |1 GB/s x 4 |Windows Server Datacenter 2012 R2 (x64) + Hyper-V szerepkör |
| VMM-kiszolgáló |2 | | |2 |1 Gbps |Windows Server Database 2012 R2 (x64) + VMM 2012 R2 |

### <a name="secondary-site"></a>Másodlagos hely

* A másodlagos hely hat csomópontos feladatátvevő fürttel rendelkezik.
* A fürtcsomópont tárolóját egy iSCSI SAN biztosítjuk. Model – Hitachi HUS130.

![Elsődleges hardver specifikációja](./media/hyper-v-vmm-performance-results/IC744923.png)

| Kiszolgáló | RAM | Modellezés | Processzor | Processzorok száma | Hálózati adapter | Szoftverek |
| --- | --- | --- | --- | --- | --- | --- |
| Hyper-V-kiszolgálók a fürtben: <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB-HOST10 |96 |Dell™ PowerEdge™ R720 |Intel (R) Xeon (R) CPU E5 – 2630 0 \@ 2.30 GHz |2 |1 GB/s x 4 |Windows Server Datacenter 2012 R2 (x64) + Hyper-V szerepkör |
| ESTLAB-HOST17 |128 |Dell™ PowerEdge™ R820 |Intel (R) Xeon (R) CPU E5 – 4620 0 \@ 2.20 GHz |4 | |Windows Server Datacenter 2012 R2 (x64) + Hyper-V szerepkör |
| ESTLAB-HOST24 |256 |Dell™ PowerEdge™ R820 |Intel (R) Xeon (R) CPU E5 – 4620 0 \@ 2.20 GHz |2 | |Windows Server Datacenter 2012 R2 (x64) + Hyper-V szerepkör |
| VMM-kiszolgáló |2 | | |2 |1 Gbps |Windows Server Database 2012 R2 (x64) + VMM 2012 R2 |

### <a name="server-workloads"></a>Kiszolgáló munkaterhelései

* Tesztelési célból a nagyvállalati felhasználói forgatókönyvekben gyakran használt számítási feladatokat választottuk ki.
* A [IOMeter](http://www.iometer.org) -t a szimulált táblázatban összefoglalt számítási feladatok jellemzővel használjuk.
* Az összes IOMeter-profil úgy van beállítva, hogy véletlenszerű bájtokat írjon, hogy szimulálja a számítási feladatokhoz szükséges legrosszabb írási mintákat.

| Számítási feladat | I/O-méret (KB) | %-Os hozzáférés | % Olvasás | Függőben lévő I/o | I/O-minta |
| --- | --- | --- | --- | --- | --- |
| Fájlkiszolgáló |4<br />8<br />16<br />32<br />64 |60%<br />20%<br />5%<br />5%<br />10% |80%<br />80%<br />80%<br />80%<br />80% |8<br />8<br />8<br />8<br />8 |Minden 100% véletlenszerű |
| SQL Server (1. kötet)<br />SQL Server (2. kötet) |8<br />64 |100%<br />100% |70%<br />0% |8<br />8 |100% véletlenszerű<br />100%-os szekvenciális |
| Exchange |32 |100% |67% |8 |100% véletlenszerű |
| Munkaállomás/VDI |4<br />64 |66%<br />34% |70%<br />95% |1<br />1 |100%-os véletlenszerű |
| Webfájl-kiszolgáló |4<br />8<br />64 |33%<br />34%<br />33% |95%<br />95%<br />95% |8<br />8<br />8 |Minden 75% véletlenszerű |

### <a name="vm-configuration"></a>Virtuális gép konfigurációja

* 470 virtuális gépek az elsődleges fürtön.
* Minden VHDX lemezzel rendelkező virtuális gép.
* A táblázatban összefoglalt számítási feladatokat futtató virtuális gépek. Az összes VMM-sablonnal lett létrehozva.

| Számítási feladat | Virtuális gépek száma | Minimális RAM (GB) | Maximális memória (GB) | Logikai lemez mérete (GB) virtuális gépenként | Maximális IOPS |
| --- | --- | --- | --- | --- | --- |
| SQL Server |51 |1 |4 |167 |10 |
| Exchange Server |71 |1 |4 |552 |10 |
| Fájlkiszolgáló |50 |1 |2 |552 |22 |
| Virtuális asztali infrastruktúra (VDI) |149 |.5 |1 |80 |6 |
| Webkiszolgáló |149 |.5 |1 |80 |6 |
| ÖSSZESEN |470 | | |96,83 TB |4108 |

### <a name="site-recovery-settings"></a>Site Recovery beállítások

* Site Recovery a helyszíni környezet és a helyszíni védelem használatára lett konfigurálva
* A VMM-kiszolgálónak négy felhője van konfigurálva, amelyek a Hyper-V fürt kiszolgálóit és a virtuális gépeket tartalmazzák.

| Elsődleges VMM-felhő | Védett virtuális gépek | Replikálás gyakorisága | További helyreállítási pontok |
| --- | --- | --- | --- |
| PrimaryCloudRpo15m |142 |15 perc |Nincsenek |
| PrimaryCloudRpo30s |47 |30 másodperc |Nincsenek |
| PrimaryCloudRpo30sArp1 |47 |30 másodperc |1 |
| PrimaryCloudRpo5m |235 |5 perc |Nincsenek |

### <a name="performance-metrics"></a>Teljesítmény-mérőszámok

A tábla összegzi az üzemelő példányban mért teljesítmény-mérőszámokat és számlálókat.

| Metric | Számláló |
| --- | --- |
| CPU |\Processor(_Total)\% Processor Time |
| Igénybe vehető memória |\Memory\Available MB-ban |
| IOPS |\PhysicalDisk (_Total) \ átvitel másodpercenként |
| VIRTUÁLIS gépek olvasási (IOPS) műveletei (művelet/s) |\Hyper-V virtuális tárolóeszköz ( \<VHD> ) \Read művelet/mp |
| Virtuális gép írási (IOPS) műveletei (művelet/s) |\Hyper-V virtuális tárolóeszköz ( \<VHD> ) \Write művelet/S |
| Virtuális gép olvasási átviteli sebessége |\Hyper-V virtuális tárolóeszköz ( \<VHD> ) \Read sebesség (bájt/s) |
| Virtuális gép írási sebessége |\Hyper-V virtuális tárolóeszköz ( \<VHD> ) \Write sebesség (bájt/s) |

## <a name="next-steps"></a>Következő lépések

[Replikáció beállítása](hyper-v-vmm-disaster-recovery.md)
