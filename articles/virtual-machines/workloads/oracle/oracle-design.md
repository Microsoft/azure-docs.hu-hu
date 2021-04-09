---
title: Oracle-adatbázis tervezése és megvalósítása az Azure-ban | Microsoft Docs
description: Egy Oracle-adatbázis megtervezése és megvalósítása az Azure-környezetben.
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 12/17/2020
ms.author: kegorman
ms.reviewer: tigorman
ms.openlocfilehash: 6e59d0065dfa74979bf3bbc72458bda516e3b641
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101669989"
---
# <a name="design-and-implement-an-oracle-database-in-azure"></a>Oracle-adatbázis tervezése és implementálása az Azure-ban

## <a name="assumptions"></a>Feltételezések

- Egy Oracle-adatbázist szeretne áttelepíteni a helyszínről az Azure-ba.
- Az áttelepíteni kívánt Oracle Database [diagnosztikai csomagja](https://docs.oracle.com/cd/E11857_01/license.111/e11987/database_management.htm) vagy [automatikus munkaterhelés-tárháza](https://www.oracle.com/technetwork/database/manageability/info/other-manageability/wp-self-managing-database18c-4412450.pdf) van
- Megértette az Oracle különféle mérőszámait.
- Az alkalmazások teljesítményének és a platform kihasználtságának alapvető ismerete.

## <a name="goals"></a>Célok

- Ismerje meg, hogyan optimalizálhatja Oracle-alapú üzembe helyezését az Azure-ban.
- Ismerje meg az Oracle-adatbázisok teljesítmény-hangolási beállításait egy Azure-környezetben.
- Egyértelmű elvárások vannak a fizikai hangolás korlátai között az architektúra és az előnyök, illetve az adatbázis kódjának (SQL) és az adatbázis teljes kialakításának logikai finomhangolása között.

## <a name="the-differences-between-an-on-premises-and-azure-implementation"></a>A helyszíni és az Azure-implementáció közötti különbségek 

Az alábbiakban néhány fontos dolgot figyelembe kell venni, amikor helyszíni alkalmazásokat telepít át az Azure-ba. 

Az egyik fontos különbség az, hogy egy Azure-implementációban az erőforrások, például a virtuális gépek, a lemezek és a virtuális hálózatok megoszthatók a többi ügyfél között. Emellett az erőforrások a követelmények alapján is szabályozható. Ahelyett, hogy a sikertelenség elkerülésére (MTBF) koncentráljon, az Azure sokkal inkább a sikertelenség (MTTR) túlélésére koncentrál.

Az alábbi táblázat a helyszíni megvalósítás és az Oracle-adatbázisok Azure-beli megvalósítása közötti különbségeket ismerteti.


|  | Helyszíni megvalósítás | Azure-implementáció |
| --- | --- | --- |
| **Hálózat** |LAN/WAN  |SDN (szoftveresen definiált hálózat)|
| **Biztonsági csoport** |IP-/portokra vonatkozó korlátozási eszközök |[Hálózati biztonsági csoport (NSG)](https://azure.microsoft.com/blog/network-security-groups) |
| **Rugalmasság** |MTBF (a hibák közötti átlagos idő) |MTTR (a helyreállítás átlagos ideje)|
| **Tervezett karbantartás** |Javítások/frissítések|[Rendelkezésre állási készletek](/previous-versions/azure/virtual-machines/windows/infrastructure-example) (az Azure által kezelt javítások/frissítések) |
| **Erőforrás** |Dedikált  |Megosztva más ügyfelekkel|
| **Régiók** |Adatközpontok |[Régiópárok](../../regions.md#region-pairs)|
| **Storage** |SAN/fizikai lemezek |[Azure által felügyelt tároló](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
| **Méretezés** |Függőleges skála |Horizontális skálázhatóság|


### <a name="requirements"></a>Követelmények

- Határozza meg a valós CPU-használatot, mivel az Oracle az alapszintű licenccel rendelkezik, és a vCPU igényeknek megfelelően a költségmegtakarításhoz elengedhetetlen. 
- Határozza meg az adatbázis méretét, a biztonsági mentési tárterületet és a növekedési arányt.
- Határozza meg az i/o-követelményeket, amelyeket az Oracle-Statspack és a AWR-jelentések, illetve az operációs rendszer szintű tárolási figyelési eszközök alapján lehet megbecsülni.

## <a name="configuration-options"></a>Beállítási lehetőségek

Négy lehetséges terület közül választhat, amelyek segítségével javíthatja a teljesítményt egy Azure-környezetben:

- Virtuális gép mérete
- Hálózati átviteli sebesség
- Lemezek típusai és konfigurációi
- Lemezgyorsítótár beállításai

### <a name="generate-an-awr-report"></a>AWR-jelentés létrehozása

Ha már rendelkezik egy Oracle Enterprise Edition-adatbázissal, és azt tervezi, hogy áttelepíti az Azure-ba, több lehetősége is van. Ha rendelkezik az Oracle-példányok [diagnosztikai csomagjával](https://www.oracle.com/technetwork/oem/pdf/511880.pdf) , futtathatja az Oracle AWR-jelentést a metrikák (IOPS, Mbps, GiBs stb.) lekéréséhez. A diagnosztikai csomag licence vagy a Standard Edition rendszerű adatbázisok esetében a manuális Pillanatképek gyűjtése után a Statspack-jelentéssel megegyező fontos mérőszámok is összegyűjthetők.  A két jelentési módszer közötti fő különbség az, hogy a rendszer automatikusan összegyűjti az AWR-t, és további információkat nyújt az adatbázisról, mint a Statspack korábbi jelentési lehetősége.

Érdemes lehet a AWR-jelentést a normál és a maximális számítási feladatokhoz is futtatni, hogy össze lehessen hasonlítani. A pontosabb munkaterhelések összegyűjtéséhez vegye fontolóra egy hét egy hétig, a 24 HR-ben futtatott jelentést, valamint azt, hogy a AWR a jelentésben szereplő számítások részeként átlagot biztosít.  Az adatközpontok áttelepítése esetén ajánlott összegyűjteni az éles rendszerek méretezésére vonatkozó jelentéseket, és megbecsülni a felhasználói teszteléshez, teszteléshez, fejlesztéshez stb. százalékos arányban használt fennmaradó adatbázis-másolatokat (a ellenőrzését egyenlő a termelési, tesztelési és fejlesztési 50%-os termelési mérettel stb.).

Alapértelmezés szerint a AWR-tárház 8 napi adatmennyiséget tart fenn, és óránkénti időközönként pillanatképeket küld.  Egy AWR-jelentés parancssorból való futtatásához a következő műveleteket végezheti el egy terminálról:

```bash
$ sqlplus / as sysdba
SQL> @$ORACLE_HOME/rdbms/admin/awrrpt.sql;
```

### <a name="key-metrics"></a>Fő metrikák

A jelentés a következő információkat fogja kérni:
- Jelentés típusa: HTML vagy szöveg, (a 12,1-es HTML-fájl, amely további információkat tartalmaz, mint a SZÖVEGES formátum).
- A megjelenő Pillanatképek száma (egy órás időközök esetében egy hét jelentés a pillanatkép-azonosítókban a 168-ben különbözik)
- A jelentés ablakának kezdő SnapshotID.
- A jelentés ablakának záró SnapshotId.
- A AWR-szkript által létrehozandó jelentés neve.

Ha a AWR egy valós alkalmazás-fürtön futtatja, a parancssori jelentés a awrgrpt. SQL a awrrpt. SQL helyett.  A "g" jelentés egy jelentést hoz létre a RAC-adatbázis összes csomópontja számára egyetlen jelentésben, és mindegyik RAC-csomóponton futtatnia kell egyet.

A következő mérőszámok olvashatók be a AWR jelentésből:

- Az adatbázis neve, a példány neve és az állomásnév
- Adatbázis verziója (az Oracle általi támogatás)
- PROCESSZOR/magok
- SGA/PGA, (és az Advisors, amely lehetővé teszi, hogy a méreten alul)
- Teljes memória GB-ban
- % Foglalt CPU
- ADATBÁZIS-processzorok
- IOPs (írás/írás)
- MB/s (olvasási/írási)
- Hálózati átviteli sebesség
- Hálózati késési arány (alacsony/magas)
- Leggyakoribb várakozási események 
- Az adatbázis paramétereinek beállításai
- Adatbázis-RAC, Exadata, speciális funkciók vagy konfigurációk használatával

### <a name="virtual-machine-size"></a>Virtuális gép mérete

#### <a name="1-estimate-vm-size-based-on-cpu-memory-and-io-usage-from-the-awr-report"></a>1. a virtuális gép méretének becslése a AWR-jelentés CPU-, memória-és I/O-használata alapján

Az egyik dolog, amit megnézhet, az első öt időzített esemény, amely a rendszer szűk keresztmetszetének helyét jelzi.

Az alábbi ábrán például a naplófájl szinkronizálása felül van. Azt jelzi, hogy hány várakozásra van szükség, mielőtt a LGWR beírja a log puffert az újra naplófájlba. Ezek az eredmények azt jelzik, hogy a tárolók vagy lemezek jobb teljesítményére van szükség. Emellett a diagram a CPU (magok) számát és a memória mennyiségét is megjeleníti.

![A táblázat tetején található naplófájl-szinkronizálást bemutató képernyőkép.](./media/oracle-design/cpu_memory_info.png)

Az alábbi ábrán az olvasási és írási műveletek teljes I/O-értéke látható. A jelentés ideje alatt 59 GB olvasható és 247,3 GB volt.

![Az olvasási és írási műveletek teljes I/O-listáját bemutató képernyőkép.](./media/oracle-design/io_info.png)

#### <a name="2-choose-a-vm"></a>2. Válasszon egy virtuális gépet

A AWR jelentésből összegyűjtött információk alapján a következő lépés egy hasonló méretű virtuális gép kiválasztása, amely megfelel az igényeinek. A rendelkezésre álló virtuális gépek listáját a cikk a [memória optimalizált](../../sizes-memory.md)verziójában találja.

#### <a name="3-fine-tune-the-vm-sizing-with-a-similar-vm-series-based-on-the-acu"></a>3. a virtuális gép méretezése a ACU alapuló hasonló virtuálisgép-sorozattal

A virtuális gép kiválasztása után figyeljen a virtuális gép ACU. Választhat egy másik virtuális gépet is a ACU érték alapján, amely jobban megfelel a követelményeinek. További információ: [Azure számítási egység](../../acu.md).

![Képernyőfelvétel a ACU-egységek oldalról](./media/oracle-design/acu_units.png)

### <a name="network-throughput"></a>Hálózati átviteli sebesség

Az alábbi ábrán az átviteli sebesség és a IOPS közötti kapcsolat látható:

![Az átviteli sebesség képernyőképe](./media/oracle-design/throughput.png)

A teljes hálózati átviteli sebesség a következő információk alapján becsülhető meg:
- SQL * net-forgalom
- MBps x kiszolgálók száma (kimenő adatfolyam, például Oracle-adatőr)
- Egyéb tényezők, például az alkalmazás replikálása

![Az SQL * net átviteli sebességének képernyőképe](./media/oracle-design/sqlnet_info.png)

A hálózati sávszélességre vonatkozó követelmények alapján különböző típusú átjárók közül választhat. Ezek közé tartoznak az alapszintű, a VpnGw és az Azure ExpressRoute. További információt a [VPN Gateway díjszabási oldalán](https://azure.microsoft.com/pricing/details/vpn-gateway/?v=17.23h)talál.

**Javaslatok**

- A hálózati késés magasabb a helyszíni üzembe helyezéshez képest. A hálózati utak csökkentése jelentősen növelheti a teljesítményt.
- Az egyidejű váltás csökkentése érdekében a nagy tranzakciós vagy "Csevegő" alkalmazásokkal rendelkező alkalmazások összevonása ugyanazon a virtuális gépen.
- A gyorsabb hálózati teljesítmény érdekében a Virtual Machinest [gyorsított hálózatkezeléssel](../../../virtual-network/create-vm-accelerated-networking-cli.md) használhatja.
- Bizonyos Linux-disztribúciók esetében érdemes lehet a [Trim/leképezésének megszüntetése támogatásának](/previous-versions/azure/virtual-machines/linux/configure-lvm#trimunmap-support)engedélyezése.
- Telepítse az [Oracle Enterprise Managert](https://www.oracle.com/technetwork/oem/enterprise-manager/overview/index.html) egy különálló virtuális gépre.
- Alapértelmezés szerint a hatalmas lapok nincsenek engedélyezve a Linuxon. Érdemes lehet hatalmas lapokat engedélyezni és beállítani `use_large_pages = ONLY` a Oracle db. Ez segíthet a teljesítmény növelésében. További információt [itt](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/refrn/USE_LARGE_PAGES.html#GUID-1B0F4D27-8222-439E-A01D-E50758C88390) találhat.

### <a name="disk-types-and-configurations"></a>Lemezek típusai és konfigurációi

- *Alapértelmezett operációsrendszer-lemezek*: ezek a lemezek állandó adatmennyiséget és gyorsítótárazást biztosítanak. A rendszer indításkor optimalizált operációsrendszer-hozzáférésre, és nem a tranzakciós vagy adatraktár (analitikus) számítási feladatokhoz lett tervezve.

- *Felügyelt lemezek*: az Azure kezeli a virtuálisgép-lemezekhez használt Storage-fiókokat. Megadhatja a lemez típusát (leggyakrabban az Oracle számítási feladataihoz használható SSD-ket) és a szükséges lemez méretét. Az Azure létrehozza és kezeli a lemezt.  Prémium SSD felügyelt lemez csak a memóriára optimalizált és a kifejezetten virtuálisgép-sorozatokhoz érhető el. Egy adott virtuálisgép-méret kiválasztása után a menü csak a virtuális gép méretétől függően elérhető Premium Storage SKU-t jeleníti meg.

![A felügyelt lemez oldalának képernyőképe](./media/oracle-design/premium_disk01.png)

Miután konfigurálta a tárolót egy virtuális gépen, érdemes lehet betölteni a lemezek tesztelését az adatbázis létrehozása előtt. Az I/O-sebesség a késés és az átviteli sebesség tekintetében való ismerete segíthet megállapítani, hogy a virtuális gépek támogatják-e a várt átviteli sebességet a késési célokkal.

Számos eszköz használható az alkalmazások terhelésének tesztelésére, például az Oracle Orion, a Sysbench, az iszap és a Fio.

Futtassa újra a terhelési tesztet, miután telepített egy Oracle-adatbázist. Indítsa el a normál és a legmagasabb munkaterhelést, és az eredmények megmutatják a környezet alapkonfigurációját.  Legyen reális a munkaterhelés-tesztben – nem érdemes olyan számítási feladatot futtatni, amely nem hasonlít a virtuális gépen a valóságban futtatott terhelésre.

Mivel az Oracle sok esetben egy IO-igényes adatbázis, nagyon fontos, hogy a tárterületet a IOPS arány alapján, a tárterület mérete helyett. Ha például a szükséges IOPS értéke 5 000, de csak 200 GB szükséges, akkor is előfordulhat, hogy a P30 osztály prémium lemeze még akkor is elérhető, ha több mint 200 GB tárterületet tartalmaz.

A IOPS arány a AWR jelentésből szerezhető be. Ezt a rendszer a visszaállítási napló, a fizikai olvasások és az írási arány alapján határozza meg.  Mindig ellenőrizze, hogy a kiválasztott virtuálisgép-sorozat képes-e a számítási feladatok i/o-igényének kezelésére is.  Ha a virtuális gép a tárterületnél alacsonyabb i/o-korláttal rendelkezik, a maximális korlátot a virtuális gép fogja beállítani.

![A AWR-jelentés oldalának képernyőképe](./media/oracle-design/awr_report.png)

Például az ismétlés mérete 12 200 000 bájt/másodperc, ami egyenlő a 11,63 MBPs-val.
A IOPS a 12 200 000/2 358 = 5 174.

Az I/O-követelmények egyértelmű képe után kiválaszthatja a követelmények teljesítéséhez legmegfelelőbb meghajtók kombinációját.

**Javaslatok**

- Az adatkezelők esetében a felügyelt tároló vagy az Oracle ASM használatával vigye át az I/O-munkaterhelést több lemezre.
- Az Oracle Advanced Compression használatával csökkentheti az I/O-t (az adatmennyiség és az indexek esetében egyaránt).
- Külön naplófájlok, ideiglenes és visszavonási tablespace-ket külön adatlemezeken.
- Ne helyezzen minden alkalmazásfájl alapértelmezett operációsrendszer-lemezre (/dev/sda). Ezek a lemezek nem optimalizálták a virtuális gép rendszerindítási idejét, és előfordulhat, hogy nem biztosítanak jó teljesítményt az alkalmazás számára.
- Ha az M sorozatú virtuális gépeket Premium Storage-on használja, engedélyezze a [írásgyorsítót](../../how-to-enable-write-accelerator.md) a naplók megismétlése lemezre.
- Érdemes lehet áthelyezni a nagy késésű, ultra Disk-re való átállási naplókat.

### <a name="disk-cache-settings"></a>Lemezgyorsítótár beállításai

A gazdagépek gyorsítótárazására három lehetőség van, de egy Oracle-adatbázis esetében csak az írásvédett gyorsítótárazás javasolt az adatbázis-munkaterheléshez.  A ReadWrite jelentős biztonsági réseket vezethet be egy adatfájlhoz, ahol az adatbázis írási célja az, hogy rögzítse az adatfájlhoz, és ne gyorsítótárazza az adatokat.

A fájlrendszerrel vagy alkalmazással ellentétben az adatbázisok esetében a gazdagépek gyorsítótárazására vonatkozó javaslat *írásvédett*: az összes kérelem gyorsítótárazva lesz a későbbi olvasási műveletekhez. Az összes írás továbbra is lemezre íródik.

**Javaslatok**

Az átviteli sebesség maximalizálása érdekében javasoljuk, hogy ha lehetséges, a gazdagépek gyorsítótárazásához **readonly** módon kezdjen. Premium Storage esetében ne feledje, hogy a "korlátok" letiltására akkor van szükség, ha a fájlrendszert a **readonly** kapcsolóval csatlakoztatja. Frissítse az/etc/fstab fájlt az UUID használatával a lemezekre.

![Képernyőkép a felügyelt lemezről, amely a ReadOnly és a none beállításokat jeleníti meg.](./media/oracle-design/premium_disk02.png)

- OPERÁCIÓSRENDSZER-lemezek esetén használja az alapértelmezett **írási/olvasási** gyorsítótárazást, és használja a prémium SSD-t az Oracle számítási feladatokhoz.  Győződjön meg arról is, hogy a swap szolgáltatáshoz használt kötet prémium SSD-re is épül.
- Az összes adatfájlhoz **readonly** használatával kell használni a gyorsítótárazást. A írásvédett gyorsítótárazás csak a prémium szintű felügyelt lemezeken, a P30 és a fentiekben érhető el.  Az írásvédett gyorsítótárazással használható 4095GiB-kötetek száma korlátozott.  Az összes foglalás nagyobb, alapértelmezés szerint letiltja a gazdagép-gyorsítótárazást.

Ha a munkaterhelések nagy mértékben változnak a nap és az idő között, és az IO-számítási feladat támogatja azt, a P1-P20 prémium SSD a betöltéssel az éjszakai kötegek terhelése vagy a korlátozott IO-igények esetében szükséges teljesítményt nyújthatja.  

## <a name="security"></a>Biztonság

Az Azure-környezet beállítása és konfigurálása után a következő lépés a hálózat védelme. Íme néhány javaslat:

- *NSG házirend*: a NSG alhálózat vagy hálózati adapter használatával lehet definiálni. Egyszerűbben szabályozható a hozzáférés az alhálózat szintjén, mind a biztonság, mind a kényszerített útválasztás, például az alkalmazási tűzfalak esetében.

- *Jumpbox*: a biztonságosabb hozzáférés érdekében a rendszergazdáknak nem kell közvetlenül kapcsolódniuk az alkalmazás-szolgáltatáshoz vagy-adatbázishoz. A Jumpbox a felügyeleti gép és az Azure-erőforrások közötti adathordozóként használják.
![A Jumpbox-topológia oldalának képernyőképe](./media/oracle-design/jumpbox.png)

    A rendszergazda számítógép csak a Jumpbox számára biztosít IP-hozzáférésre korlátozott hozzáférést. A Jumpbox hozzáféréssel kell rendelkeznie az alkalmazáshoz és az adatbázishoz.

- *Magánhálózat* (alhálózatok): javasoljuk, hogy az Application Service-t és az adatbázist külön alhálózatokon, így a NSG szabályzat által beállítható jobb szabályozás.


## <a name="additional-reading"></a>További információ

- [Oracle ASM konfigurálása](configure-oracle-asm.md)
- [Oracle Data Guard konfigurálása](configure-oracle-dataguard.md)
- [Az Oracle Golden Gate konfigurálása](configure-oracle-golden-gate.md)
- [Oracle biztonsági mentés és helyreállítás](./oracle-overview.md)

## <a name="next-steps"></a>Következő lépések

- [Oktatóanyag: kiválóan elérhető virtuális gépek létrehozása](../../linux/create-cli-complete.md)
- [A virtuális gépek üzembe helyezésének megismerése Azure CLI-mintákkal](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)
