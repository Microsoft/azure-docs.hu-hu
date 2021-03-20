---
title: Oracle Database-teljesítmény Azure NetApp Files egyetlen köteten | Microsoft Docs
description: Az Oracle Database Azure NetApp Files egyetlen kötetének teljesítményteszt-eredményeit ismerteti.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/30/2020
ms.author: b-juche
ms.openlocfilehash: c6cdf2f6dada0aa4dea2f70f18237b7ee39e3ea1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91571337"
---
# <a name="oracle-database-performance-on-azure-netapp-files-single-volumes"></a>Az Oracle-adatbázis teljesítménye az Azure NetApp Files önálló kötetein

Ez a cikk a felhőben található Oracle-vel kapcsolatos alábbi témaköröket tárgyalja. Ezek a témakörök különösen fontosak lehetnek az adatbázis-rendszergazdák, a felhőalapú építészek vagy a tárolási építészek számára:   

* Ha online tranzakció-feldolgozási (OLTP) számítási feladatot (főleg véletlenszerű I/O-t) vagy online analitikus feldolgozási (OLAP) munkaterhelést (általában szekvenciális I/O-t) hajtanak majd ki, mi a teljesítmény?   
* Mi a különbség a normál Linux kernel NFS-(kNFS-) ügyfél és az Oracle saját közvetlen NFS-ügyfele között?
* A sávszélességet illetően elég egyetlen Azure NetApp Files kötet teljesítménye?

## <a name="testing-environment-and-components"></a>Tesztelési környezet és összetevők

A következő ábra a teszteléshez használt környezetet szemlélteti. A konzisztencia és az egyszerűség érdekében Ansible forgatókönyveket használtak a test Bed összes elemének üzembe helyezéséhez.

![Oracle-tesztelési környezet](../media/azure-netapp-files/performance-oracle-test-environment.png)  

### <a name="virtual-machine-configuration"></a>Virtuális gép konfigurációja

A tesztek a virtuális gép következő beállítását használták:
* Operációs rendszer:   
    RedHat Enterprise Linux 7,8 (wle-ora01)
* Példányok típusai:   
    Két modellt használt a tesztelésben – D32s_v3 és D64s_v3
* Hálózati adapterek száma:   
    Egy (1) a 3. alhálózatba helyezve  
* Lemezek   
    Az Oracle bináris fájljai és az operációs rendszer egyetlen prémium szintű lemezre lett helyezve

### <a name="azure-netapp-files-configuration"></a><a name="anf_config"></a>Azure NetApp Files konfiguráció
A tesztek a következő Azure NetApp Files konfigurációt használták:   

* Kapacitási készlet mérete:  
    A készlet különböző méreteket konfigurált: 4 TiB, 8 TiB, 16 TiB, 32 TiB 
* Szolgáltatási szint:  
    Ultra (128 MiB/s sávszélesség 1 TiB-ból lefoglalt kötet kapacitása)
* Kötetek  
    Egy és két mennyiségi teszt lett kiértékelve

### <a name="workload-generator"></a>Munkaterhelés-generátor 

A teszt a felhasznált számítási feladatok által generált iszap 2.5.4.2. Az iszap (buta kis Oracle-teljesítményteszt) egy jól ismert számítási feladatok generátora az Oracle-térben, amely a SGA-pufferelt fizikai I/O-munkaterheléssel kihangsúlyozza és teszteli az I/O-alrendszert.  

Az iszap 2.5.4.2 nem támogatja a csatlakoztatott adatbázist (PDB). Ennek megfelelően a és a parancsfájlok bekerültek `setup.sh` a `runit.sh` PDB-támogatás hozzáadására.  

A tesztekben használt iszap-változókat a következő szakaszokban ismertetjük.

#### <a name="workload-80-select-20-update--random-io--slobconf-variables"></a>Munkaterhelés 80% SELECT, 20% UPDATE | Véletlenszerű I/O – `slob.conf` változók   

`UPDATE_PCT=20`   
`SCAN_PCT=0`   
`RUN_TIME=600`   
`WORK_LOOP=0`   
`SCALE=75G`   
`SCAN_TABLE_SZ=50G`   
`WORK_UNIT=64`   
`REDO_STRESS=LITE`   
`LOAD_PARALLEL_DEGREE=12`   

#### <a name="workload-100-select--sequential-io--slobconf-variables"></a>Munkaterhelés 100% SELECT | Szekvenciális I/O – `slob.conf` változók

`UPDATE_PCT=0`   
`SCAN_PCT=100`   
`RUN_TIME=600`   
`WORK_LOOP=0`   
`SCALE=75G`   
`SCAN_TABLE_SZ=50G`   
`WORK_UNIT=64`   
`REDO_STRESS=LITE`   
`LOAD_PARALLEL_DEGREE=12`   

### <a name="database"></a>Adatbázis

A tesztekhez használt Oracle-verzió Oracle Database Enterprise Edition 19.3.0.0.

Az Oracle-paraméterek a következők:  
* `sga_max_size`: 4096M
* `sga_target`: 4096
* `db_writer_processes`: 12
* `awr_pdb_autoflush_enabled`: igaz
* `filesystemio_options`: SETALL
* `log_buffer`: 134217728

Egy PDB lett létrehozva az iszap-adatbázishoz.

A következő ábrán a PERFIO nevű tablespace látható, amelynek mérete 600 GB (20 adatfájl, 30 GB mindegyik) a négy sár felhasználói séma üzemeltetésére lett létrehozva. Minden felhasználói séma 125 GB méretű volt.

![Oracle-adatbázis](../media/azure-netapp-files/performance-oracle-tablespace.png)  

## <a name="performance-metrics"></a>Teljesítmény-mérőszámok

A cél az, hogy az alkalmazás által tapasztalt i/o-teljesítményt jelentse. Ezért az ebben a cikkben szereplő összes diagram az Oracle-adatbázis által jelentett metrikákat használja az automatikus munkaterhelés-tárház (AWR) jelentésein keresztül. A diagramokban használt metrikák a következők:   

* **Átlagos IO-kérelmek/másodperc**   
    A Load Profile szakasz átlagos olvasási IO-kérelmek/s-és átlagos írási IO-kérelmek/másodperc összegének felel meg.
* **Átlagos IO MB/mp**   
    A Load Profile szakasz átlagos olvasási IO MB/s és átlagos írási IO MB/s értékének felel meg.
* **Olvasási késleltetés átlagos késése**   
    Megfelel az Oracle WAIT esemény "db file szekvenciális olvasás" átlagos késésének.
* **Szálak/séma száma**   
    Megfelel a felhasználói séma által megadott sár-szálak számának.

## <a name="performance-measurement-results"></a>Teljesítmény-mérési eredmények  

Ez a szakasz a teljesítmény mérésének eredményét írja le.

### <a name="linux-knfs-client-vs-oracle-direct-nfs"></a>Linux kNFS-ügyfél vs. Oracle Direct NFS

Ez a forgatókönyv egy Azure-beli virtuális gépen fut Standard_D32s_v3 (Intel E5-2673 v4 @ 2,30 GHz). A számítási feladat 75% SELECT és 25% UPDATE, többnyire véletlenszerű I/O, valamint a ~ 7,5%-os adatbázis-puffer. 

Ahogy az a következő ábrán is látható, az Oracle DNFS-ügyfél 2,8 x több átviteli sebességet adott meg, mint a normál Linux kNFS-ügyfél:  

![Linux kNFS-ügyfél összehasonlítva az Oracle Direct NFS-sel](../media/azure-netapp-files/performance-oracle-kfns-compared-dnfs.png)  

Az alábbi ábrán az olvasási műveletek késési görbe látható. Ebben a kontextusban a kNFS-ügyfél szűk keresztmetszete az ügyfél és az NFS-kiszolgáló (a Azure NetApp Files kötet) között létrejött egyetlen NFS TCP szoftvercsatorna-kapcsolat.  

![Linux kNFS-ügyfél összehasonlítva az Oracle Direct NFS késési görbével](../media/azure-netapp-files/performance-oracle-latency-curve.png)  

A DNFS-ügyfél több, IO-kérelmeket/másodpercet tudott leküldeni, mert több száz TCP szoftvercsatorna-kapcsolatot hozhat létre, így kihasználhatja a párhuzamosságot. A [Azure NetApp Files konfigurációban](#anf_config)leírtaknak megfelelően minden további rendelkezésre álló TiB-kapacitás további 128MiB/s sávszélességet tesz lehetővé. Az átviteli sebesség 1 GiB/s DNFS, amely a 8 – TiB kapacitás kiválasztásának korlátja. Nagyobb kapacitás miatt több átviteli sebesség is megvalósult volna.

Az átviteli sebesség csak az egyik szempont. Egy másik szempont a késés, amely elsődleges hatással van a felhasználói élményre. Ahogy az alábbi ábrán is látható, a késés növekedése várhatóan sokkal gyorsabban kNFS, mint a DNFS. 

![Linux kNFS-ügyfél összehasonlítva az Oracle Direct NFS olvasási késésével](../media/azure-netapp-files/performance-oracle-read-latency.png)  

A hisztogramok kiváló képet adnak az adatbázis-késésekről. A következő ábra teljes nézetet biztosít a rögzített "db-fájl szekvenciális olvasása" szempontjából, miközben a DNFS-t használja a legmagasabb egyidejűségi adatponthoz (32 szál/séma). Ahogy az a következő ábrán is látható, az összes olvasási művelet 47%-a a 512-es és a 1000-os másodpercenként megbecsült érték volt, míg az összes olvasási művelet 90%-a 2 ms-os késéssel szolgált.

![Linux kNFS-ügyfél és az Oracle Direct NFS-hisztogramok összehasonlítása](../media/azure-netapp-files/performance-oracle-histogram-read-latency.png)  

Végezetül egyértelmű, hogy a DNFS egy kötelező, ha az Oracle Database-példány teljesítményének javítására van szükség az NFS-en.

### <a name="single-volume-performance-limits"></a>Egyetlen kötetre vonatkozó teljesítményszint

Ez a szakasz a véletlenszerű I/O-t és a szekvenciális I/O-t tartalmazó egyetlen kötet teljesítményének korlátait ismerteti. 

#### <a name="random-io"></a>Véletlenszerű I/O

A DNFS képes sokkal nagyobb sávszélességet fogyasztani, mint amit egy 8 TB-os Azure NetApp Files teljesítményű kvóta biztosít. Ha növeli a Azure NetApp Files kötet kapacitását 16 TiB-ra, amely egy azonnali változás, a kötet sávszélességének mennyisége 1024 MiB/s-ról, 2X – 2048 MiB/s. 

Az alábbi ábrán egy 80%-os Select és 20%-os frissítési munkaterhelés-konfiguráció látható, és egy adatbázis-puffer találati aránya 8%. Az iszap egyetlen kötetet tudott elvinni másodpercenként 200 000 NFS I/O-kérelmekre. Figyelembe véve, hogy minden művelet 8 KiB méretű, a teszt alá tartozó rendszer a ~ 200 000 IO kérelmek/mp vagy a 1600 MiB/s-t tudta kézbesíteni.
 
![Oracle DNFS átviteli sebesség](../media/azure-netapp-files/performance-oracle-dnfs-throughput.png)  

A következő olvasási késési görbe ábrája azt mutatja, hogy az olvasási sebesség növekedésével a késés az 1 MS vonal alatt zökkenőmentesen növekszik, és a görbe térdét a ~ 165 000 átlagos olvasási késleltetéssel (IO-kérelmek/mp) éri el a ~ 1,3 MS átlagos olvasási késése miatt.  Ez az érték hihetetlen késési érték, amely az Azure-felhőben szinte bármilyen más technológiával elérhetetlen I/O-arányt eredményez. 

![Oracle DNFS késési görbe](../media/azure-netapp-files/performance-oracle-dnfs-latency-curve.png)  

#### <a name="sequential-io"></a>Szekvenciális I/O  

Ahogy az a következő ábrán is látható, nem minden I/O véletlenszerű jellegű, figyelembe véve egy Oláh Anna biztonsági mentést vagy teljes táblázatos vizsgálatot, például olyan számítási feladatokat, amelyek a lehető legtöbb sávszélességet igénylik.  Ha ugyanazt a konfigurációt használja, mint a korábban leírtak, de a kötet mérete 32 TiB-ra van méretezve, a következő ábra azt mutatja, hogy egy Oracle DB-példány legfeljebb 3 900 MB/s sebességű teljesítményt tud vezetni, nagyon közel a Azure NetApp Files kötet TB-32 os teljesítmény-kvótához (128 MB/s * 32 = 4096 MB/s).

![Oracle DNFS I/O](../media/azure-netapp-files/performance-oracle-dnfs-io.png)  

Összefoglalva, Azure NetApp Files segít az Oracle-adatbázisoknak a felhőben való elvégzésében. Teljesítményt nyújt, amikor az adatbázis igényli. Dinamikusan és nem leállítása kieséses bármikor átméretezheti a mennyiségi kvótát.

## <a name="next-steps"></a>Következő lépések

- [Az Azure NetApp Files teljesítménytesztjével kapcsolatos javaslatok](azure-netapp-files-performance-metrics-volumes.md)
- [Teljesítménytesztek Linuxhoz](performance-benchmarks-linux.md)