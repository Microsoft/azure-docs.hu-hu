---
title: 'Azure Premium Storage: nagy teljesítményű kialakítás'
description: Nagy teljesítményű alkalmazások tervezése az Azure Premium SSD Managed Disks használatával. A Premium Storage nagy teljesítményű, kis késleltetésű lemezes támogatást biztosít az Azure-Virtual Machines futó I/O-igényes számítási feladatokhoz.
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 10/05/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 7e93c659ad58db8d82e68380ab6a0855af27e1bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98882382"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Azure Premium Storage: nagy teljesítményű kialakítás

Ez a cikk útmutatást nyújt a nagy teljesítményű alkalmazások létrehozásához az Azure Premium Storage használatával. A jelen dokumentumban ismertetett útmutatást az alkalmazás által használt technológiákra vonatkozó legjobb teljesítményű gyakorlattal kombinálva is használhatja. Az irányelvek szemléltetése érdekében a jelen dokumentumban SQL Server a Premium Storage-on való futtatását használták példaként.

A cikkben található tárolási réteg teljesítmény-forgatókönyvei esetében optimalizálni kell az alkalmazás rétegét. Ha például egy SharePoint-farmot futtat az Azure Premium Storageon, a cikk SQL Server példáit követve optimalizálhatja az adatbázis-kiszolgálót. Emellett optimalizálja a SharePoint-farm webkiszolgálóját és az alkalmazáskiszolgáló a legtöbb teljesítmény eléréséhez.

Ez a cikk segítséget nyújt az alkalmazások teljesítményének az Azure Premium Storage-on való optimalizálásával kapcsolatos gyakori kérdések megválaszolásához.

* Az alkalmazás teljesítményének mérése  
* Miért nem látja a várt nagy teljesítményt?  
* Milyen tényezők befolyásolják az alkalmazás teljesítményét Premium Storage?  
* Hogyan befolyásolják ezek a tényezők az alkalmazás teljesítményét Premium Storage?  
* Hogyan lehet optimalizálni a IOPS, a sávszélességet és a késést?  

Ezeket az irányelveket kifejezetten a Premium Storagehoz, mert a Premium Storage futó munkaterhelések nagy teljesítményű. Szükség esetén példákat is biztosítottunk. Ezen irányelvek némelyikét a standard szintű IaaS virtuális gépeken futó alkalmazásokra is alkalmazhatja.

> [!NOTE]
> Időnként előfordulhat, hogy a lemez teljesítményével kapcsolatos probléma valójában a hálózat szűk keresztmetszete. Ilyen helyzetekben érdemes optimalizálni a [hálózati teljesítményt](../virtual-network/virtual-network-optimize-network-bandwidth.md).
>
> Ha a lemez teljesítménytesztét szeretné használni, tekintse meg a következő cikkeket: a lemez teljesítményértékelése:
>
> * Linux esetén: [az alkalmazás teljesítményteszte Azure Disk Storage](./disks-benchmarks.md)
> * Windows esetén: [egy lemez teljesítményértékelése](./disks-benchmarks.md).
>
> Ha a virtuális gép támogatja a gyorsított hálózatkezelést, győződjön meg arról, hogy engedélyezve van. Ha nincs engedélyezve, a már telepített virtuális gépeken is engedélyezheti a Windows és [Linux](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms) [rendszereken](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) .

Mielőtt elkezdené, ha a Premium Storage új, először olvassa el az [Azure-lemez kiválasztása a IaaS virtuális gépekhez](disks-types.md) és [méretezhetőségi célokat a prémium szintű blob Storage-fiókokhoz](../storage/blobs/scalability-targets-premium-page-blobs.md).

## <a name="application-performance-indicators"></a>Alkalmazás-teljesítménymutatók

A rendszer azt vizsgálja, hogy egy alkalmazás megfelelően működnek-e, vagy nem használ-e teljesítménymutatókat (például:), hogy az alkalmazás hogyan dolgozza fel a felhasználói kéréseket, mennyi adat feldolgozására kerül sor, és hogy hány kérelem van egy alkalmazás feldolgozására egy adott időszakban, hogy mennyi ideig kell várni a felhasználónak, hogy a kérés elküldése után kapjon választ. A teljesítménymutatók technikai feltételei: IOPS, átviteli sebesség, sávszélesség és késés.

Ebben a szakaszban a közös teljesítménymutatókat tárgyaljuk a Premium Storage kontextusában. A következő, az alkalmazásokra vonatkozó követelmények összegyűjtését ismertető szakaszban megtudhatja, hogyan mérjük az alkalmazáshoz tartozó teljesítménymutatókat. Az alkalmazások teljesítményének optimalizálásában később megismerheti azokat a tényezőket, amelyek hatással vannak a teljesítménymutatók és a javaslatok optimalizálására.

## <a name="iops"></a>IOPS

A IOPS, illetve a másodpercenkénti bemeneti/kimeneti műveletek száma az, hogy az alkalmazás milyen kérelmeket küld a Storage-lemezeknek egy másodperc alatt. Egy bemeneti/kimeneti művelet olvasható vagy írható, szekvenciális vagy véletlenszerű lehet. Az online tranzakció-feldolgozási (OLTP) alkalmazások, mint például az online kiskereskedelmi webhelyek, azonnal fel kell dolgoznia számos egyidejű felhasználói kérést. A felhasználói kérések olyan intenzív adatbázis-tranzakciókat szúrnak be és frissítenek, amelyeket az alkalmazásnak gyorsan kell feldolgoznia. Ezért a OLTP-alkalmazások nagyon magas IOPS igényelnek. Ilyen alkalmazások több millió kis-és véletlenszerű IO-kérelmet kezelnek. Ha van ilyen alkalmazás, meg kell terveznie az alkalmazás-infrastruktúrát, hogy optimalizálja a IOPS. A későbbi, az *alkalmazások teljesítményének optimalizálása* című szakaszban részletesen ismertetjük az összes olyan tényezőt, amelyet meg kell fontolnia, hogy magas IOPS kapjon.

Ha prémium szintű tárolót csatlakoztat a nagy méretű virtuális gépéhez, az Azure a lemez specifikációinak megfelelően kiosztja a garantált IOPS-t. A P50-es lemeznél például 7500 IOPS lesz kiosztva. Minden nagy méretű virtuális gép rendelkezik egy IOPS-korláttal is, amelyet még fenn tud tartani. A standard GS5 virtuális gép például 80 000 IOPS korláttal rendelkezik.

## <a name="throughput"></a>Teljesítmény

Az átviteli sebesség vagy a sávszélesség azt az adatmennyiséget adja meg, amelyet az alkalmazás a megadott intervallumban küld a Storage-lemezeknek. Ha az alkalmazás nagy i/o-méretű bemeneti/kimeneti műveleteket végez, nagy átviteli sebességre van szükség. Az adatraktár-alkalmazások általában nagy mennyiségű adathoz hozzáférő, nagyméretű műveleteket végző, nagyszámú adatokhoz hozzáférő, intenzíven végrehajtott ellenőrzéseket adnak ki. Más szóval az ilyen alkalmazások esetében nagyobb átviteli sebességre van szükség. Ha van ilyen alkalmazás, meg kell terveznie az infrastruktúráját az átviteli sebesség optimalizálása érdekében. A következő szakaszban részletesen ismertetjük azokat a tényezőket, amelyeket meg kell valósítania.

Ha egy prémium szintű Storage-lemezt nagy léptékű virtuális géphez csatol, az Azure a lemez specifikációjának megfelelően kiépíti az adatátviteli sebességet. A P50-es lemeznél például a lemez átviteli sebessége 250 MB másodpercenként. Minden nagy méretű virtuális gép rendelkezik egy átviteli sebessége vonatkozó korláttal, amelyet még fenn tud tartani. A Standard GS5 virtuális gép maximális átviteli sebessége például 2000 MB másodpercenként.

Az átviteli sebesség és a IOPS közötti kapcsolat az alábbi képletben látható.

![A IOPS és az átviteli sebesség aránya](linux/media/premium-storage-performance/image1.png)

Ezért fontos megállapítani az alkalmazás által igényelt optimális átviteli sebességet és IOPS értékeket. Ahogy az egyiket próbálja optimalizálni, a másik is hatással lesz rá. Az *alkalmazások teljesítményének optimalizálása* egy későbbi szakaszban a IOPS és az átviteli sebesség optimalizálásával kapcsolatos további részleteket tárgyaljuk.

## <a name="latency"></a>Késés

A késés azt az időtartamot veszi igénybe, ameddig egy alkalmazás egyetlen kérést kap, elküldheti a tároló lemezekre, és elküldheti a választ az ügyfélnek. Ez az alkalmazás teljesítményének kritikus mérőszáma a IOPS és az átviteli sebesség mellett. A Premium Storage-lemez késése a kérelem információinak lekéréséhez és az alkalmazással való kommunikációhoz szükséges idő. Premium Storage konzisztens kis késést biztosít. A prémium szintű lemezeket úgy tervezték, hogy a legtöbb IO-művelet esetében egyszámjegyű ezredmásodperces késést biztosítsanak. Ha a Premium Storage-lemezeken engedélyezte az írásvédett gazdagép gyorsítótárazását, sokkal alacsonyabb olvasási késést érhet el. A lemezes gyorsítótárazás részletes ismertetését a későbbi, az *alkalmazások teljesítményének optimalizálását* ismertető szakaszban tárgyaljuk.

Ha a magasabb IOPS és átviteli sebesség eléréséhez optimalizálja az alkalmazást, az hatással lesz az alkalmazás késésére. Az alkalmazás teljesítményének finomhangolása után mindig mérje fel az alkalmazás késését, hogy elkerülje a váratlan nagy késési viselkedést.

A Managed Disks következő vezérlési sík műveletei a lemez egyik tárolóhelyről a másikra történő áthelyezését is magukban foglalhatják. Ez az adatháttér-másolaton keresztül történik, amely több órát is igénybe vehet, jellemzően kevesebb, mint 24 óra, a lemezeken lévő adatmennyiségtől függően. Ez idő alatt az alkalmazás a szokásos olvasási késésnél magasabb szintű élményt nyújt, mivel egyes olvasási műveletek az eredeti helyre lesznek átirányítva, és hosszabb ideig tarthatnak. Ebben az időszakban nincs hatással az írási késésre.

- Frissítse a tárolási típust.
- Leválaszthatja és csatlakoztathat egy lemezt egy virtuális gépről egy másikhoz.
- Felügyelt lemez létrehozása VHD-fájlból.
- Felügyelt lemez létrehozása pillanatképből.
- Nem felügyelt lemezek konvertálása felügyelt lemezekre.

## <a name="performance-application-checklist-for-disks"></a>Performance Application ellenőrzőlista lemezek számára

Az Azure Premium Storageon futó nagy teljesítményű alkalmazások tervezésének első lépése az alkalmazás teljesítményére vonatkozó követelmények megismerése. A teljesítményre vonatkozó követelmények begyűjtése után optimalizálhatja az alkalmazást a legoptimálisabb teljesítmény eléréséhez.

Az előző szakaszban ismertetjük a közös teljesítménymutatókat, a IOPS, az átviteli sebességet és a késést. Meg kell határoznia, hogy mely teljesítménymutatók kritikus fontosságúak az alkalmazás számára a kívánt felhasználói élmény biztosításához. Például a nagy IOPS a legtöbbet, hogy OLTP a másodpercenként több millió tranzakciót feldolgozó alkalmazásokat. Míg a nagy adatátviteli sebesség kritikus fontosságú az adatraktár-alkalmazások számára, amelyek nagy mennyiségű adattal dolgoznak fel egy másodperc alatt. A rendkívül alacsony késés elengedhetetlen a valós idejű alkalmazások, például az élő videó streaming webhelyeihez.

Ezután mérje fel az alkalmazás maximális teljesítményre vonatkozó követelményeit teljes élettartama alatt. Használja az alábbi minta ellenőrzőlistát a kezdéshez. A maximális teljesítményre vonatkozó követelményeket a normál, a csúcsérték és a munkaidőn kívüli munkaterhelési időszakok során rögzítheti. Az összes számítási feladatra vonatkozó követelmények azonosításával meghatározhatja az alkalmazás általános teljesítménybeli követelményeit. Egy e-kereskedelmi webhely normál számítási feladatait például az adott évben a legtöbb nap során kiszolgált tranzakciók jelentik. A webhely maximális számítási feladatait az ünnepi időszakokban vagy a különleges értékesítési eseményeken kiszolgált tranzakciók jelentik. A maximális számítási feladat általában korlátozott időszakra van felhasználva, de az alkalmazásnak a normál működéséhez két vagy több alkalommal kell méreteznie. Ismerje meg a 50 percentilis, 90 percentilis és 99 percentilis követelményeit. Ez segít kiszűrni a teljesítményre vonatkozó követelmények kiugró adatait, és a megfelelő értékek optimalizálására koncentrálhat.

## <a name="application-performance-requirements-checklist"></a>Az alkalmazás teljesítményére vonatkozó követelmények ellenőrzőlistája

| **Teljesítményre vonatkozó követelmények** | **50 percentilis** | **90 percentilis** | **99 percentilis** |
| --- | --- | --- | --- |
| Legfeljebb Tranzakció/másodperc | | | |
| Olvasási műveletek%-ban | | | |
| Írási műveletek%-ban | | | |
| Véletlenszerű műveletek%-ban | | | |
| Szekvenciális műveletek%-ban | | | |
| I/o-kérelem mérete | | | |
| Átlagos átviteli sebesség | | | |
| Legfeljebb Teljesítmény | | | |
| Min. Késés | | | |
| Átlagos késés | | | |
| Legfeljebb CPU | | | |
| Átlagos CPU | | | |
| Legfeljebb Memória | | | |
| Átlagos memória | | | |
| Várólista mélysége | | | |

> [!NOTE]
> Érdemes megfontolni a számok méretezését az alkalmazás várható jövőbeli növekedése alapján. Érdemes megtervezni a növekedést az idő előtt, mivel ez megnehezítheti az infrastruktúra megváltoztatását a teljesítmény későbbi javítása érdekében.

Ha rendelkezik meglévő alkalmazással, és át szeretne térni Premium Storagere, először hozza létre a fenti ellenőrzőlistát a meglévő alkalmazáshoz. Ezt követően készítse elő az alkalmazás prototípusát Premium Storage és tervezze meg az alkalmazást az alkalmazás *teljesítményének optimalizálása* a jelen dokumentum későbbi szakaszában leírt irányelvek alapján. A következő cikk a teljesítmény-mérések összegyűjtéséhez használható eszközöket ismerteti.

### <a name="counters-to-measure-application-performance-requirements"></a>Az alkalmazás teljesítményére vonatkozó követelményeket mérő számlálók

Az alkalmazás teljesítménybeli követelményeinek mérésére a legjobb módszer, ha a kiszolgáló operációs rendszere által biztosított teljesítmény-figyelési eszközöket használja. Használhatja a PerfMon-t a Windowshoz és a Linux rendszerhez készült iostat. Ezek az eszközök a fenti szakaszban ismertetett egyes mértékeknek megfelelő számlálókat rögzítenek. A számlálók értékeit rögzíteni kell, ha az alkalmazás a normál, a csúcs és a munkaidőn kívüli munkaterhelést futtatja.

A PerfMon-számlálók elérhetők a processzor, a memória és a kiszolgáló minden logikai lemeze és fizikai lemeze számára. Ha Premium Storage-lemezeket használ egy virtuális géppel, a fizikai lemezek számlálói a Premium Storage-lemezekhez tartoznak, és a logikai lemezek számlálói a Premium Storage-lemezeken létrehozott minden kötethez tartoznak. Rögzítenie kell az alkalmazás számítási feladatait tároló lemezek értékeit. Ha a logikai és fizikai lemezek között egy hozzárendelés van, akkor a fizikai lemez számlálókat is megtekintheti. egyéb esetben a logikai lemez számlálóit kell megtekinteni. Linux rendszeren a iostat parancs CPU-és lemezhasználat-jelentést hoz létre. A lemezhasználat-jelentés fizikai eszközön vagy partíción jeleníti meg a statisztikát. Ha adatbázis-kiszolgálója van az adatokkal, és külön lemezeket naplóz, gyűjtsön adatokat mindkét lemezre. Az alábbi táblázat a lemezek, processzorok és memória számlálóit ismerteti:

| Számláló | Leírás | PerfMon | Iostat |
| --- | --- | --- | --- |
| **IOPS vagy tranzakciók másodpercenként** |A tárolási lemezre másodpercenként kiadott I/O-kérelmek száma. |Olvasási sebesség (lemez/mp) <br> Írási sebesség (írás/mp) |TPS <br> r/s <br> w/s |
| **Lemez olvasása és írása** |a lemezen végrehajtott olvasási és írási műveletek%-a. |% Lemez olvasási ideje <br> % Lemez írási ideje |r/s <br> w/s |
| **Átviteli sebesség** |A lemezre másodpercenként beolvasott vagy írt adatok mennyisége. |Lemez olvasási sebessége (bájt/s) <br> Lemez írási sebessége (bájt/s) |kB_read/s <br> kB_wrtn/s |
| **Késés** |A lemez i/o-kérések befejezésének teljes ideje. |Átlagos írási idő (mp/olvasás) <br> Átlagos írási idő (mp/írás) |várja <br> svctm |
| **IO-méret** |Az I/O-kérések mérete a tároló lemezei számára. |Lemez átlagos bájtjai/olvasása <br> Átlagos lemez bájt/írás |avgrq – sz |
| **Várólista mélysége** |Azon függő I/O-kérések száma, amelyek a Storage-lemezre való olvasásra vagy írásra várnak. |Lemez aktuális várólistájának hossza |avgqu – sz |
| **Maximális memória** |Az alkalmazás zökkenőmentes futtatásához szükséges memória mennyisége |Előjegyzett memória kihasználtsága (%) |Vmstat használata |
| **Max. CPU** |Az alkalmazás zökkenőmentes futtatásához szükséges mennyiségű CPU |Processzoridő (%) |% util |

További információ a [iostat](https://linux.die.net/man/1/iostat) és a [perfmon](/windows/win32/perfctrs/performance-counters-portal)szolgáltatásról.



## <a name="optimize-application-performance"></a>Az alkalmazások teljesítményének optimalizálása

Az Premium Storage-on futó alkalmazások teljesítményét befolyásoló fő tényezők az IO-kérések, a virtuális gépek mérete, a lemez mérete, a lemezek száma, a lemezes gyorsítótárazás, a többszálú működés és a várólista mélysége. Ezeket a tényezőket a rendszeren keresztül megadott gombokkal szabályozhatja. Előfordulhat, hogy a legtöbb alkalmazás nem ad lehetőséget az IO-méret és a várólista-mélység módosítására. Ha például SQL Server használ, az IO-méret és a várólista mélysége nem választható ki. SQL Server kiválasztja az optimális i/o-méretet és a várólista mélységi értékeit a legtöbb teljesítmény eléréséhez. Fontos megérteni mindkét típusú tényező hatását az alkalmazás teljesítményére, így a megfelelő erőforrásokat kiépítheti a teljesítményre vonatkozó igények kielégítése érdekében.

Ebben a szakaszban a létrehozott alkalmazás-követelmények ellenőrzőlistáján tájékozódhat az alkalmazás teljesítményének optimalizálásához szükséges mennyiségről. Ennek alapján meghatározhatja, hogy a szakasz mely tényezőit kell hangolni. Ha meg szeretné tudni, hogy az egyes tényezők milyen hatással vannak az alkalmazás teljesítményére, futtassa a teljesítményteszt-eszközöket az alkalmazás beállításán. A Windows-és Linux-alapú virtuális gépeken a gyakori teljesítményteszt-eszközök futtatásának lépéseiért tekintse meg a következő témakört:.

### <a name="optimize-iops-throughput-and-latency-at-a-glance"></a>IOPS, átviteli sebesség és késés optimalizálása egy pillantással

Az alábbi táblázat összefoglalja a teljesítménnyel kapcsolatos tényezőket és a IOPS, az átviteli sebesség és a késés optimalizálásához szükséges lépéseket. Az összegzést követő szakaszok részletes leírást kapnak az egyes tényezőkről.

A virtuálisgép-méretekről, valamint a IOPS, az átviteli sebességről és az egyes virtuális gépekhez rendelkezésre álló késésről további információt az [Azure-beli virtuális gépek méretei](sizes.md)című témakörben talál.

| | **IOPS** | **Átviteli sebesség** | **Késés** |
| --- | --- | --- | --- |
| **Példa forgatókönyvre** |Nagyvállalati OLTP alkalmazás, amely másodpercenként nagy mennyiségű tranzakciót igényel. |Nagyvállalati adattárház-kezelő alkalmazás nagy mennyiségű adattal. |Közel valós idejű alkalmazások, amelyek azonnali válaszokat igényelnek a felhasználói kérésekre, például az online játékokba. |
| **Teljesítménnyel kapcsolatos tényezők** | &nbsp; | &nbsp; | &nbsp; |
| **IO-méret** |A kisebb IO-méret magasabb IOPS eredményez. |Nagyobb IO-méret, amely magasabb átviteli sebességet eredményez. | &nbsp;|
| **Virtuális gép mérete** |Használjon olyan virtuálisgép-méretet, amely IOPS nagyobb, mint az alkalmazásra vonatkozó követelmény. |Használjon olyan virtuálisgép-méretet, amelynek átviteli korlátja nagyobb, mint az alkalmazásra vonatkozó követelmény. |Használjon olyan virtuálisgép-méretet, amely az alkalmazásra vonatkozó követelménynél nagyobb skálázási korlátokat biztosít. |
| **Lemez mérete** |Használjon olyan méretű IOPS, amely nagyobb, mint az alkalmazásra vonatkozó követelmény. |Az alkalmazásra vonatkozó követelménynél nagyobb méretű adatátviteli korláttal rendelkező lemezeket használjon. |Használjon olyan méretű lemezt, amely az alkalmazás követelményeinél nagyobb léptéket biztosít. |
| **VIRTUÁLIS gépek és lemezek méretezési korlátai** |A kiválasztott virtuálisgép-méret IOPS-korlátjának nagyobbnak kell lennie, mint a IOPS csatlakoztatott tárolók teljes száma. |A kiválasztott virtuális gép maximális átviteli sebességének nagyobbnak kell lennie, mint a Premium Storage-lemezek által csatlakoztatott teljes átviteli sebesség. |A kiválasztott virtuálisgép-méret skálázási korlátjának nagyobbnak kell lennie a csatolt Premium Storage-lemezek teljes méretezési korlátainál. |
| **Lemezes gyorsítótárazás** |A magasabb olvasási IOPS érdekében engedélyezze az írásvédett gyorsítótárat a prémium szintű Storage-lemezeken. | &nbsp; |A prémium szintű Storage-lemezek írásvédett gyorsítótárazásának engedélyezése kész, nagy mennyiségű olvasási késést biztosító műveletekkel. |
| **Lemez felosztása adatcsíkokra** |Több lemezt is használhat, és összekapcsolhatja őket, hogy egy kombinált, magasabb szintű IOPS és adatátviteli korlátot kapjon. A virtuális gépek összesített korlátjának nagyobbnak kell lennie, mint a csatlakoztatott prémium szintű lemezek összesített korlátai. | &nbsp; | &nbsp; |
| **Sáv mérete** |Kisebb szalagos méret a OLTP-alkalmazásokban látott véletlenszerű, kisméretű i/o-mintákhoz. Például a 64 KB-os sáv méretének használata SQL Server OLTP alkalmazáshoz. |Nagyobb szalagos méret az adatraktár-alkalmazásokban látott szekvenciális, nagy i/o-mintázathoz. Például 256 KB-os sáv méretet használhat SQL Server adatraktár-alkalmazáshoz. | &nbsp; |
| **Több szál használata** |A többszálas küldéssel nagyobb számú kérést küldhet Premium Storagenak, ami magasabb IOPS és átviteli sebességhez vezet. SQL Server például magas MAXDOP értéket állít be, hogy több processzort foglaljon le a SQL Server. | &nbsp; | &nbsp; |
| **Várólista mélysége** |A nagyobb várólista-mélység nagyobb IOPS eredményez. |A nagyobb várólista-mélység nagyobb átviteli sebességet eredményez. |A kisebb várólista-mélység kisebb késéseket eredményez. |

## <a name="nature-of-io-requests"></a>Az IO-kérések természete

Az i/o-kérések az alkalmazás által elvégezhető bemeneti/kimeneti műveletek egységei. Az i/o-kérések, véletlenszerű vagy szekvenciális, olvasási vagy írási, kis-és nagybetűk azonosításához segítséget nyújt az alkalmazás teljesítménybeli követelményeinek meghatározásában. Fontos megérteni az IO-kérelmek természetét, hogy a megfelelő döntéseket hozza az alkalmazás-infrastruktúra tervezésekor. Az IOs-t egyenletesen kell terjeszteni a lehető legjobb teljesítmény eléréséhez.

Az IO-méret az egyik legfontosabb tényező. Az i/o-méret az alkalmazás által generált bemeneti/kimeneti műveletre vonatkozó kérelem mérete. Az i/o-méret jelentős hatással van a teljesítményre, különösen a IOPS és a sávszélességre, amelyet az alkalmazás képes elérni. A következő képlet a IOPS, az i/o-méret és a sávszélesség/átviteli sebesség közötti kapcsolatot mutatja.  
    ![Egy diagram, amely az I O P S i/o-időpontok közötti adatátviteli sebességet mutatja.](media/premium-storage-performance/image1.png)

Egyes alkalmazások lehetővé teszik az IO-méret módosítását, míg egyes alkalmazások nem. A SQL Server például meghatározza az optimális IO-méretet, és nem biztosítja a felhasználóknak semmilyen gomb megváltoztatását. Másfelől az Oracle egy [db- \_ blokk \_ méret](https://docs.oracle.com/cd/B19306_01/server.102/b14211/iodesign.htm#i28815) nevű paramétert biztosít, amellyel konfigurálhatja az adatbázis I/O-kérelmének méretét.

Ha olyan alkalmazást használ, amely nem teszi lehetővé az i/o-méret módosítását, a cikk útmutatásai alapján optimalizálhatja az alkalmazáshoz leginkább kapcsolódó teljesítménymutatókat. Példa:

* Egy OLTP-alkalmazás több millió kis-és véletlenszerű IO-kérelmet hoz létre. Az ilyen típusú IO-kérelmek kezeléséhez meg kell terveznie az alkalmazás-infrastruktúrát, hogy nagyobb IOPS kapjon.  
* Az adattárház-alkalmazások nagy és szekvenciális IO-kérelmeket generálnak. Az ilyen típusú IO-kérelmek kezeléséhez meg kell terveznie az alkalmazás-infrastruktúrát, hogy nagyobb sávszélességet vagy átviteli sebességet kapjon.

Ha olyan alkalmazást használ, amely lehetővé teszi az IO-méret módosítását, akkor az egyéb teljesítményre vonatkozó irányelvek mellett használja az IO-méretet.

* Kisebb i/o-méret, hogy magasabb IOPS kapjon. Például 8 KB egy OLTP-alkalmazáshoz.  
* Nagyobb IO-méret a nagyobb sávszélesség/teljesítmény eléréséhez. Például 1024 KB egy adatraktár-alkalmazáshoz.

Itt látható egy példa arra, hogyan számítható ki az alkalmazás IOPS és átviteli sebessége/sávszélessége. Vegye fontolóra egy alkalmazás P30 lemez használatával történő használatát. A P30-lemez maximális IOPS és átviteli sebessége/sávszélessége 5000 IOPS és 200 MB/másodperc. Ha az alkalmazása megköveteli a maximális IOPS a P30-lemezről, és kisebb i/o-méretet (például 8 KB) használ, az eredményül kapott sávszélesség 40 MB/s lesz. Ha azonban az alkalmazás a maximális átviteli sebességet/sávszélességet igényli a P30 lemezről, és nagyobb i/o-méretet használ, mint például a 1024 KB, az eredményül kapott IOPS kevesebb lesz, 200 IOPS. Ezért állítsa be úgy az IO-méretet, hogy az megfeleljen az alkalmazás IOPS, valamint az átviteli sebességre/sávszélességre vonatkozó követelménynek. A következő táblázat összefoglalja a különböző i/o-méreteket, valamint a P30-lemezek megfelelő IOPS és átviteli sebességét.

| Alkalmazásra vonatkozó követelmény | I/O-méret | IOPS | Átviteli sebesség/sávszélesség |
| --- | --- | --- | --- |
| Maximális IOPS-érték |8 KB |5000 |40 MB/másodperc |
| Maximális átviteli sebesség |1024 KB |200 |200 MB/másodperc |
| Maximális átviteli sebesség + magas IOPS |64 KB |3 200 |200 MB/másodperc |
| Max IOPS + magas átviteli sebesség |32 KB |5000 |160 MB/másodperc |

Ahhoz, hogy a IOPS és a sávszélesség nagyobb legyen, mint egy prémium szintű Storage-lemez maximális értéke, több prémium lemez is használható egymásba. Tegyük fel például, hogy a két P30 lemez a 10 000 IOPS kombinált IOPS, vagy 400 MB/s kombinált átviteli sebességgel rendelkezik. A következő szakaszban leírtaknak megfelelően olyan virtuálisgép-méretet kell használniuk, amely támogatja a kombinált lemez IOPS és átviteli sebességét.

> [!NOTE]
> Ahogy a IOPS vagy az átviteli sebesség is növekszik, ügyeljen arra, hogy ne elérje a lemez vagy a virtuális gép átviteli sebességét vagy IOPS korlátját, amikor eggyel nő.

Ha meg szeretné tudni, hogy az IO-méret milyen hatással van az alkalmazás teljesítményére, a virtuális gépre és a lemezekre is futtathat teljesítményteszt-eszközöket. Hozzon létre több tesztet, és használja az egyes futtatások esetén az i/o-méretet, hogy láthassa a hatását. További részletekért tekintse meg a befejezéshez csatolt teljesítményteszt-cikket.

## <a name="high-scale-vm-sizes"></a>Nagy léptékű virtuális gépek méretei

Amikor elkezd megtervezni egy alkalmazást, az egyik első teendő, válasszon egy virtuális gépet az alkalmazás üzemeltetéséhez. A Premium Storage nagy méretű virtuálisgép-méretekkel rendelkezik, amelyek nagyobb számítási teljesítményt igénylő alkalmazásokat futtathatnak, és nagy helyi lemezes I/O-teljesítményt igényelnek. Ezek a virtuális gépek gyorsabb processzorokat, nagyobb memória-mag arányt és Solid-State meghajtót (SSD) biztosítanak a helyi lemez számára. Példák a DS és a GS sorozatú virtuális gépeket Premium Storage támogató nagy méretű virtuális gépekre.

A nagy léptékű virtuális gépek különböző méretekben érhetők el, különböző számú CPU-mag, memória, operációs rendszer és ideiglenes lemez mérete miatt. Minden virtuálisgép-mérethez a virtuális géphez csatolható adatlemezek maximális száma is tartozik. Ezért a kiválasztott virtuálisgép-méret befolyásolja, hogy mennyi feldolgozási, memória-és tárolókapacitás érhető el az alkalmazás számára. Emellett a számítási és tárolási költségeket is befolyásolja. Az alábbi példa a DS-sorozatok és a GS-sorozatok legnagyobb virtuálisgép-méretének specifikációit mutatja be:

| Virtuális gép mérete | Processzormagok | Memória | VM-lemezek mérete | Legfeljebb adatlemezek | Gyorsítótár mérete | IOPS | Sávszélesség-gyorsítótár i/o-korlátai |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_DS14 |16 |112 GB |OPERÁCIÓS RENDSZER = 1023 GB <br> Helyi SSD = 224 GB |32 |576 GB |50 000 IOPS <br> 512 MB/másodperc |4 000 IOPS és 33 MB/másodperc |
| Standard_GS5 |32 |448 GB |OPERÁCIÓS RENDSZER = 1023 GB <br> Helyi SSD = 896 GB |64 |4224 GB |80 000 IOPS <br> 2 000 MB/másodperc |5 000 IOPS és 50 MB/másodperc |

Az összes elérhető Azure-beli virtuálisgép-méret teljes listájának megtekintéséhez tekintse meg a [virtuális gépek méretei az Azure-ban](sizes.md) vagy a-ben című témakört. Válasszon olyan virtuálisgép-méretet, amely képes megfelelni és méretezni a kívánt alkalmazás teljesítményére vonatkozó követelményeket. Emellett a virtuális gépek méretének kiválasztásakor figyelembe kell vennie a következő fontos szempontokat is.

*Skálázási korlátok*  
A maximális IOPS korlátja virtuális gépenként és lemezenként eltérő, egymástól független. Győződjön meg arról, hogy az alkalmazás a virtuális gép korlátain belül IOPS, valamint a hozzá csatolt prémium lemezeken. Ellenkező esetben az alkalmazás teljesítménye a szabályozást fogja tapasztalni.

Tegyük fel például, hogy egy alkalmazásra vonatkozó követelmény legfeljebb 4 000 IOPS. Ennek eléréséhez P30-lemezt kell kiépíteni egy DS1 virtuális gépre. A P30-lemez akár 5 000 IOPS-t is továbbíthat. A DS1 virtuális gép azonban 3 200 IOPS-ra van korlátozva. Ennek következtében az alkalmazás teljesítményét a virtuális gép 3 200 IOPS korlátja korlátozza, és a teljesítmény csökken. Ezen helyzet elkerülése érdekében válasszon egy virtuális gépet és a lemez méretét, amely megfelel az alkalmazás követelményeinek.

*Működési díj*  
Sok esetben előfordulhat, hogy az Premium Storage használatával járó általános üzemeltetési díj alacsonyabb, mint a standard Storage használata.

Vegyünk például egy 16 000-IOPS igénylő alkalmazást. Ennek a teljesítménynek a megvalósításához szüksége lesz egy standard \_ D14 Azure IaaS virtuális gépre, amely maximális IOPS biztosít a 16 000 32 standard Storage 1 TB lemez használatával. Mindegyik 1 TB-os standard Storage-lemez legfeljebb 500 IOPS érhet el. A virtuális gép havi becsült díja $1 570 lesz. A standard szintű Storage-lemezek havi 32 díja $1 638. A becsült teljes havi költség $3 208 lesz.

Ha azonban ugyanazt az alkalmazást Premium Storage-on üzemelteti, akkor a virtuális gép mérete és a kevesebb prémium szintű Storage-lemez szükséges, így csökkentve a teljes költségeket. A standard \_ DS13 virtuális gép négy P30-lemezzel képes megfelelni az 16 000 IOPS-követelménynek. A DS13 virtuális gép 25 600 maximális IOPS rendelkezik, és az egyes P30-lemezek maximális IOPS 5 000. Ez a konfiguráció összességében elérheti a 5 000 x 4 = 20 000 IOPS. A virtuális gép havi becsült díja $1 003 lesz. A négy P30 Premium Storage-lemez havi díja $544,34 lesz. A becsült teljes havi költség $1 544 lesz.

Az alábbi táblázat összefoglalja a forgatókönyvnek a standard és a Premium Storage szerinti lebontását.

| &nbsp; | **Standard** | **Prémium** |
| --- | --- | --- |
| **Virtuális gép ára havonta** |$1 570,58 (szabványos \_ D14) |$1 003,66 (szabványos \_ DS13) |
| **Lemezek havi díja** |$1 638,40 (32 x 1 – TB lemez) |$544,34 (4 x P30-lemez) |
| **Teljes díj havonta** |$3 208,98 |$1 544,34 |

*Linux-disztribúciók*  

Az Azure Premium Storage a Windows és a Linux rendszerű virtuális gépek esetében azonos szintű teljesítményt nyújt. A Linux-disztribúciók számos változatát támogatjuk, és [itt](linux/endorsed-distros.md)láthatja a teljes listát. Fontos megjegyezni, hogy a különböző disztribúciók jobban illeszkednek a különböző típusú munkaterhelésekhez. A számítási feladatok különböző szintjeit fogja látni, attól függően, hogy milyen disztribúció fut a számítási feladatban. Tesztelje a Linux-disztribúciókat az alkalmazással, és válassza ki azt, amely a legjobban működik.

A Linux és a Premium Storage használatával a nagy teljesítmény biztosításához tekintse meg a szükséges illesztőprogramok legújabb frissítéseit.

## <a name="premium-storage-disk-sizes"></a>Prémium szintű Storage-lemezek mérete

Az Azure Premium Storage különböző méreteket kínál, így kiválaszthatja az igényeinek leginkább megfelelőt. Az egyes lemezek mérete eltérő a IOPS, a sávszélességet és a tárterületet illetően. Az alkalmazás követelményeitől és a nagy méretű virtuális gép méretétől függően válassza ki a megfelelő Premium Storage a lemez méretét. Az alábbi táblázat a lemezek méretét és azok képességeit mutatja be. A P4, a P6, a P15, a P60, a P70 és a P80 méretek jelenleg csak Managed Disks támogatottak.

[!INCLUDE [disk-storage-premium-ssd-sizes](../../includes/disk-storage-premium-ssd-sizes.md)]

A választott lemezek számától függ, hogy hány lemez van kiválasztva. Az alkalmazási követelményeknek való megfelelés érdekében egyetlen P50-lemezt vagy több P10-lemezt is használhat. A választás során vegye figyelembe az alább felsorolt szempontokat.

*Skálázási korlátok (IOPS és átviteli sebesség)*  
A prémium szintű lemezek IOPS és átviteli sebessége eltér a virtuális gépek méretezési korlátaitól. Győződjön meg arról, hogy a lemezek teljes IOPS és átviteli sebessége a virtuális gép kiválasztott méretének korlátain belül van.

Ha például egy alkalmazásra vonatkozó követelmény legfeljebb 250 MB/s átviteli sebesség, és egyetlen P30-lemezzel rendelkező DS4 virtuális gépet használ. A DS4 virtuális gép legfeljebb 256 MB/s átviteli sebességet biztosíthat. Egyetlen P30-lemez esetében azonban az átviteli sebesség 200 MB/s-ra van korlátozva. Ennek következtében az alkalmazás a lemez korlátja miatt 200 MB/s-ra lesz korlátozva. A korlát leküzdéséhez hozzon létre egynél több adatlemezt a virtuális gépen, vagy méretezze át a lemezeket a P40 vagy a P50.

> [!NOTE]
> A gyorsítótár által kiszolgált olvasások nem szerepelnek a lemez IOPS és átviteli sebességében, ezért nem tartoznak a lemezre vonatkozó korlátok. A gyorsítótárnak külön IOPS és átviteli korlátja van a virtuális gépenként.
>
> A kezdeti olvasási és írási műveletek például a következők: 60MB/s és 40MB/s. Az idő múlásával a gyorsítótár felmelegszik, és a gyorsítótárból több és több olvasást is kiszolgál. Ezután a lemezről magasabb írási sebességet érhet el.

*Lemezek száma*  
Határozza meg, hogy hány lemezre lesz szüksége az alkalmazásra vonatkozó követelmények kiértékelésével. Minden virtuálisgép-mérethez a virtuális géphez csatolható lemezek száma is korlátozott. Ez általában kétszer a magok száma. Győződjön meg arról, hogy a kiválasztott VM-méret támogatja a szükséges lemezek számát.

Ne feledje, hogy a Premium Storage lemezek nagyobb teljesítménybeli képességekkel rendelkeznek, mint a standard Storage-lemezek. Ezért ha az alkalmazást az Azure IaaS virtuális gépről a standard szintű tárterülettel Premium Storagere telepíti át, valószínűleg kevesebb prémium szintű lemezre lesz szüksége az alkalmazáshoz tartozó azonos vagy magasabb teljesítmény eléréséhez.

## <a name="disk-caching"></a>Lemez gyorsítótárazása

Az Azure Premium Storaget használó, nagy teljesítményű virtuális gépek többrétegű gyorsítótárazási technológiával rendelkeznek, amelyet BlobCache-nek nevezünk. A BlobCache a gazdagép RAM és a helyi SSD kombinációját használja a gyorsítótárazáshoz. Ez a gyorsítótár elérhető a Premium Storage állandó lemezek és a virtuális gép helyi lemezei számára. Alapértelmezés szerint ez a gyorsítótár-beállítás az operációsrendszer-lemezek olvasására és írására, valamint a Premium Storage tárolt adatlemezek ReadOnly-ra van állítva. Ha a lemezes gyorsítótárazás engedélyezve van a Premium Storage lemezeken, a nagy léptékű virtuális gépek rendkívül magas szintű teljesítményt érhetnek el, amely meghaladja az alapul szolgáló lemez teljesítményét.

> [!WARNING]
> A lemezes gyorsítótárazás nem támogatott a 4 TiB és nagyobb lemezek esetén. Ha több lemez van csatlakoztatva a virtuális géphez, akkor minden 4 TiB-nál kisebb lemez támogatja a gyorsítótárazást.
>
> Egy Azure-beli lemez gyorsítótár-beállításainak módosításakor a céllemez le lesz választva, és újra lesz csatlakoztatva. Ha az operációs rendszer lemeze, a virtuális gép újraindul. A lemezgyorsítótár beállításainak módosítása előtt állítson le minden olyan alkalmazást és szolgáltatást, amelyet ez a megszakítás érinthet. A javaslatok követése nem eredményezheti az adatsérülést.

Ha többet szeretne megtudni a BlobCache működéséről, tekintse meg az [Azure Premium Storage](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/) blogbejegyzését.

Fontos, hogy a gyorsítótárat a megfelelő készleten engedélyezze. Azt határozza meg, hogy a lemezes gyorsítótárazást egy prémium szintű lemezen kell-e engedélyezni, vagy nem függ attól, hogy a lemez milyen számítási feladattal lesz kezelve. Az alábbi táblázat az operációs rendszer és az adatlemezek alapértelmezett gyorsítótár-beállításait mutatja be.

| **Lemeztípus** | **Alapértelmezett gyorsítótár-beállítás** |
| --- | --- |
| Operációsrendszer-lemez |ReadWrite |
| Adatlemez |ReadOnly |

Az adatlemezek ajánlott lemezgyorsítótár-beállításai a következők:

| **Lemezes gyorsítótárazási beállítás** | **a beállítás használatára vonatkozó javaslat** |
| --- | --- |
| Nincsenek |Konfigurálja a gazdagép-gyorsítótárat a Nincs értékre a csak írható és a nagy írási sebességű lemezekhez. |
| ReadOnly |A gazdagép-gyorsítótár írásvédettként való konfigurálása írásvédett és írható lemezekhez. |
| ReadWrite |Konfigurálja a gazdagép-gyorsítótárat úgy, hogy csak akkor ReadWrite, ha az alkalmazás megfelelően kezeli a gyorsítótárazott adatlemezek írását, ha szükséges. |

*ReadOnly*  
A Premium Storage adatlemezek ReadOnly gyorsítótárazásának konfigurálásával alacsony olvasási késést érhet el, és nagyon nagy olvasási IOPS és adatátviteli sebességet érhet el az alkalmazás számára. Ez két okból kifolyólag esedékes,

1. A virtuális gép memóriájában és a helyi SSD-lemezen található gyorsítótárból végrehajtott beolvasás sokkal gyorsabb, mint az adatlemezről beolvasott olvasási művelet, amely az Azure Blob Storage szolgáltatásban található.  
1. A Premium Storage nem számítja ki a gyorsítótárból kiszolgált olvasást a lemez IOPS és átviteli sebessége felé. Ezért az alkalmazása magasabb teljes IOPS és átviteli sebesség elérésére képes.

*ReadWrite*  
Alapértelmezés szerint az operációsrendszer-lemezeken engedélyezve van a ReadWrite gyorsítótárazás. Mostanában a ReadWrite gyorsítótárazásának támogatása is támogatott az adatlemezeken. Ha ReadWrite gyorsítótárazást használ, megfelelő módon kell megadnia az adatok gyorsítótárból állandó lemezzé való írását. Például a SQL Server kezeli a gyorsítótárazott adatlemezek saját tárolóba való írását. Ha a ReadWrite cache-t olyan alkalmazással használja, amely nem kezeli a szükséges adatmegőrzést, adatvesztést okozhat, ha a virtuális gép összeomlik.

*Nincs*  
Jelenleg a **none** érték csak adatlemezeken támogatott. OPERÁCIÓSRENDSZER-lemezeken nem támogatott. Ha a **nincs** beállítást egy operációsrendszer-lemezen állítja be, akkor a rendszer felülbírálja ezt belsőleg, és **readonly** értékre állítja azt.

Az alábbi irányelvek alkalmazásával például Premium Storage futtathatja SQL Server a következő műveleteket:

1. A "ReadOnly" gyorsítótár konfigurálása az adatfájlokat üzemeltető Premium Storage-lemezeken.  
   a.  A gyorsítótárban lévő gyors olvasások csökkentik a SQL Server lekérdezési időt, mivel az adatlapokat sokkal gyorsabban lekérik a gyorsítótárból, mint közvetlenül az adatlemezekről.  
   b.  A gyorsítótárból történő olvasások kiszolgálása azt jelenti, hogy a prémium szintű adatlemezekről további átviteli sebesség is rendelkezésre áll. SQL Server ezt a további adatátviteli sebességet további adatlapok és más, például biztonsági mentési/visszaállítási, kötegelt betöltési és index-újraépítési műveletek beolvasására használhatja.  
1. A "None" gyorsítótár konfigurálása a naplófájlokat üzemeltető Premium Storage-lemezeken.  
   a.  A naplófájlok elsősorban írási-nehéz műveletekkel rendelkeznek. Ezért nem élvezik az írásvédett gyorsítótár előnyeit.

## <a name="optimize-performance-on-linux-vms"></a>Teljesítmény optimalizálása Linux rendszerű virtuális gépeken

Az összes prémium szintű SSD-vagy Ultra-lemez esetében letilthatja a lemezen lévő fájlrendszerek "korlátozásait", hogy javítsa a teljesítményt, ha ismert, hogy nincsenek olyan gyorsítótárak, amelyek adatvesztést okozhatnak.  Ha az Azure Disk gyorsítótárazása ReadOnly vagy none értékre van beállítva, letilthatja a korlátozásokat.  Ha azonban a gyorsítótárazás a ReadWrite értékre van állítva, a korlátozásoknak engedélyezve kell maradniuk az írás tartósságának biztosítása érdekében.  A korlátok általában alapértelmezés szerint engedélyezve vannak, de az alábbi módszerek egyikével letilthatja a korlátozásokat a fájlrendszer típusától függően:

* A **reiserFS** esetében a Barrier = none csatlakoztatási lehetőséggel tiltsa le a korlátokat.  A korlátok explicit engedélyezéséhez használja a Barrier = flush lehetőséget.
* Az **ext3/ext4** esetén használja a Barrier = 0 csatlakoztatási lehetőséget a korlátok letiltásához.  A korlátok explicit engedélyezéséhez használja a Barrier = 1.
* A **XFS** esetében használja a nem Barrier Mount kapcsolót a korlátok letiltásához.  A korlátok explicit engedélyezéséhez használja a gáton.  Vegye figyelembe, hogy a Linux kernel későbbi verzióiban a XFS fájlrendszer kialakítása mindig biztosítja a tartósságot, és a korlátok letiltásának nincs hatása.  

## <a name="disk-striping"></a>Lemezek csíkozása

Ha egy nagy méretű virtuális gép több Premium Storage állandó lemezzel van csatlakoztatva, a lemezek összeállíthatók úgy, hogy összesítsék a IOPs, a sávszélességet és a tárolási kapacitást.

Windows rendszeren a tárolóhelyek együtt használhatók a lemezek szalagos tárolására. A készlet minden lemezéhez egy oszlopot kell konfigurálnia. Ellenkező esetben a csíkozott kötet teljes teljesítménye a vártnál alacsonyabb lehet, mert a forgalom a lemezeken nem egyenletes eloszlású.

Fontos: a Kiszolgálókezelő felhasználói felületének használatával beállíthatja, hogy az oszlopok száma összesen legfeljebb 8 legyen a csíkozott kötetnél. Nyolcnál több lemez csatolásakor a PowerShell használatával hozza létre a kötetet. A PowerShell használatával megadhatja az oszlopok számát a lemezek számával megegyezően. Ha például 16 lemez van egyetlen szalagos készletben, a *New-VirtualDisk PowerShell-* parancsmag *NumberOfColumns* paraméterében 16 oszlopot kell megadnia.

Linux rendszeren a lemezek együttes kiépítéséhez használja a MDADM segédprogramot. A Linux rendszerű lemezek csíkozásával kapcsolatos részletes lépéseket a [szoftveres RAID konfigurálása Linuxon](/previous-versions/azure/virtual-machines/linux/configure-raid)című témakörben talál.

*Sáv mérete*  
A lemezes csíkozás fontos konfigurációja a sáv mérete. A sáv mérete vagy a blokk mérete az alkalmazás által a csíkozott köteten címezhető legkisebb adatmennyiség. A konfigurált sáv mérete az alkalmazás típusától és a kérelem mintától függ. Ha rossz szalagos méretet választ, az IO-hibákhoz vezethet, ami az alkalmazás csökkentett teljesítményéhez vezet.

Ha például az alkalmazás által generált i/o-kérelem nagyobb, mint a lemezes szalag mérete, akkor a tárolási rendszer több lemezen írja azt át a csíkozott egység határai közé. Amikor a rendszer elvégzi az adatelérést, több csíkozott egységen kell keresnie a kérelem teljesítéséhez. Az ilyen viselkedés kumulatív hatása jelentős teljesítménybeli romláshoz vezethet. Ha azonban az IO-kérelmek mérete kisebb, mint a szalag mérete, és ha véletlenszerű jellegű, az i/o-kérések hozzáadhatnak ugyanazon a lemezen, ami torlódást okoz, és végső soron az i/o-teljesítmény csökkenését eredményezi.

Az alkalmazás által futtatott számítási feladat típusától függően válasszon egy megfelelő szalagos méretet. Véletlenszerű kisméretű i/o-kérelmek esetén használjon kisebb szalagos méretet. Míg a nagy sorszámú IO-kérelmeknél nagyobb szalagos méret van használatban. Ismerje meg a Premium Storageon futó alkalmazáshoz tartozó Stripe-méretre vonatkozó javaslatokat. SQL Server esetében konfigurálja a 64 KB-ot a OLTP számítási feladatokhoz és a 256 KB-ban az adatraktározási munkaterhelésekhez. További információért tekintse meg [Az Azure-beli virtuális gépek SQL Server teljesítményével kapcsolatos ajánlott eljárásokat](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md#disks-guidance) .

> [!NOTE]
> A DS-sorozatú virtuális gépeken és a 64 Premium Storage-lemezeken legfeljebb 32 Premium Storage-lemez használható a GS sorozatú virtuális gépeken.

## <a name="multi-threading"></a>Több szálon futó

Az Azure nagymértékben párhuzamosan alakított Premium Storage platformot. Ezért egy többszálas alkalmazás sokkal nagyobb teljesítményt érhet el, mint egy többszálú alkalmazás. A többszálas alkalmazások több szálra osztják fel a feladatait, és a végrehajtás hatékonyságát növelik a virtuális gép és a lemez erőforrásainak maximális kihasználásával.

Ha például az alkalmazás két szálat használó, egyetlen alapszintű virtuális gépen fut, a CPU képes váltani a két szál között a hatékonyság elérése érdekében. Míg az egyik szál arra vár, hogy a lemez i/o-értéke befejeződik, a processzor átválthat a másik szálra. Így két szál több, mint egyetlen szálat tud elvégezni. Ha a virtuális gépnek több mag is van, akkor tovább csökkenti a futási időt, mivel minden mag párhuzamosan hajthat végre feladatokat.

Előfordulhat, hogy nem tudja módosítani, hogy egy offline alkalmazás hogyan valósítja meg az egyszálas vagy a többszálas alkalmazást. A SQL Server például képes a többprocesszoros és a többmagos kezelésére. SQL Server azonban úgy dönt, hogy milyen feltételek mellett fogja kihasználni egy vagy több szálat a lekérdezések feldolgozásához. Lekérdezéseket futtathat, és több szálon hozhat létre indexeket. A nagyméretű táblákhoz való csatlakozást és az adatrendezést megelőzően a felhasználónak való visszatérés előtt SQL Server valószínűleg több szálat fog használni. A felhasználó azonban nem tudja szabályozni, hogy SQL Server végrehajt-e lekérdezést egyetlen szál vagy több szál használatával.

Olyan konfigurációs beállítások is megváltozhatnak, amelyek befolyásolhatják az alkalmazások többszálas vagy párhuzamos feldolgozását. SQL Server esetén például a maximális párhuzamossági fok-konfiguráció. A MAXDOP nevű beállítás lehetővé teszi, hogy beállítsa a párhuzamos feldolgozás során SQL Server használható processzorok maximális számát. A MAXDOP egyéni lekérdezésekhez vagy indexelési műveletekhez is konfigurálhatja. Ez akkor hasznos, ha a rendszer erőforrásainak kiegyensúlyozására van szükség a teljesítmény szempontjából kritikus fontosságú alkalmazáshoz.

Tegyük fel például, hogy az SQL Servert használó alkalmazás nagyméretű lekérdezést és egy indexelési műveletet hajt végre. Tegyük fel, hogy az indexelési művelet jobban teljesített, mint a nagyméretű lekérdezés. Ilyen esetben beállíthatja, hogy az MAXDOP értéke nagyobb legyen, mint a lekérdezés MAXDOP értéke. Így SQL Server több processzorral rendelkezik, amelyeket használhat az indexelési művelethez a nagyméretű lekérdezéshez hozzárendelt processzorok számához képest. Ne feledje, hogy nem szabályozhatja, hogy hány szálat SQL Server fog használni az egyes műveletekhez. A többszálas használatra dedikált processzorok maximális számát szabályozhatja.

További információk a [párhuzamossági fok](/previous-versions/sql/sql-server-2008-r2/ms188611(v=sql.105)) SQL Server. Megtudhatja, hogy az alkalmazásban több szálat befolyásoló beállítások és konfigurációik a teljesítmény optimalizálása érdekében.

## <a name="queue-depth"></a>Várólista mélysége

A várólista mélysége vagy a várólista hossza vagy a várólista mérete a rendszer függőben lévő i/o-kéréseinek száma. A várólista mélysége határozza meg, hogy az alkalmazás legfeljebb hány IO-műveletet tud feldolgozni. Ez a cikk a következő három alkalmazás-teljesítménymutatóra vonatkozik, amelyeket a jelen cikkben ismertetünk: viz., IOPS, átviteli sebesség és késés.

A várólista mélysége és a többszálúság szorosan összefügg egymással. A várólista mélysége érték azt jelzi, hogy az alkalmazás mennyi több szálat érhet el. Ha a várólista mélysége nagy, az alkalmazás egyidejűleg több műveletet is végrehajthat, más szóval több szálat. Ha a várólista mélysége kicsi, még akkor is, ha az alkalmazás többszálú, az egyidejű végrehajtáshoz nem tartozik elegendő kérés.

A polcon lévő alkalmazások általában nem teszik lehetővé a várólista mélységének módosítását, mert ha helytelenül van beállítva, akkor a megfelelőnél nagyobb kárt fog okozni. Az alkalmazások az optimális teljesítmény érdekében a várólista mélységének megfelelő értékét fogják beállítani. Fontos azonban megérteni ezt a fogalmat, így az alkalmazással kapcsolatos teljesítményproblémák is orvosolhatók. A várólista mélységének hatásait a rendszeren található teljesítményteszt-eszközök futtatásával is megfigyelheti.

Egyes alkalmazások a várólista mélységének befolyásolására szolgáló beállításokat biztosítanak. Az előző szakaszban ismertetett SQL Server például a MAXDOP (a maximális párhuzamossági fok) beállítást. A MAXDOP a várólista mélységének és a többszálas működésének befolyásolására szolgál, bár nem változtatja meg közvetlenül a SQL Server üzenetsor-mélység értékét.

*Magas várólista-mélység*  
A nagy várólista-mélységi vonalak több műveletet végeznek a lemezen. A lemez előre ismeri a soron következő kérelmet a várólistán. Ennek következtében a lemez előre ütemezheti a műveleteket, és az optimális sorozatban dolgozza fel őket. Mivel az alkalmazás több kérést küld a lemezre, a lemez képes több párhuzamos IOs-t feldolgozni. Végső soron az alkalmazás képes lesz magasabb IOPS elérni. Mivel az alkalmazás több kérelmet dolgoz fel, az alkalmazás teljes átviteli sebessége is növekszik.

Az alkalmazások általában 8 – 16 + kimagasló IOs-alapú, csatlakoztatott lemez esetén maximális átviteli sebességet érhetnek el. Ha a várólista mélysége az egyik, az alkalmazás nem elég IOs-et küld a rendszernek, és egy adott időszakban kevesebb időt fog feldolgozni. Más szóval, kevesebb átviteli sebesség.

SQL Server például a lekérdezés MAXDOP értékét "4" értékre állítja be, SQL Server, hogy a lekérdezést akár négy maggal is használhatja a lekérdezés végrehajtásához. SQL Server meg fogja határozni, hogy mi a legjobb üzenetsor mélységi értéke és a magok száma a lekérdezés végrehajtásához.

*Az optimális üzenetsor mélysége*  
A nagyon magas várólista-mélységi érték a hátrányaival is rendelkezik. Ha a várólista mélysége túl magas, az alkalmazás nagyon magas IOPS próbál meg vezetni. Ha az alkalmazás rendelkezik olyan állandó lemezzel, amely elegendő kiosztott IOPS rendelkezik, ez negatív hatással lehet az alkalmazás késésére. A következő képlet a IOPS, a késés és a várólista mélysége közötti kapcsolatot mutatja.  
    ![Egy diagram, amely azt mutatja, hogy az I/O P S időpontok késése a várólista mélységével egyenlő.](media/premium-storage-performance/image6.png)

A várakozási sor mélységét ne konfigurálja olyan magas értékre, hanem egy optimális értékre, amely a késések hiánya nélkül is elegendő IOPS tud adni az alkalmazás számára. Ha például az alkalmazás késésének 1 ezredmásodpercnek kell lennie, a 5 000 IOPS eléréséhez szükséges üzenetsor-mélység: QD = 5000 x 0,001 = 5.

*A csíkozott kötet üzenetsor-mélysége*  
A csíkozott kötetek esetében elég magas a várólista mélységének megőrzése, hogy minden lemezhez külön-külön legyen a maximális várólista mélysége. Vegyünk például egy olyan alkalmazást, amely egy 2. üzenetsor-mélységet küld, és négy lemez van a sávban. A két IO-kérelem két lemezre mutat, és a fennmaradó két lemez üresjáratban lesz. Ezért konfigurálja úgy a várólista mélységét, hogy az összes lemez foglalható legyen. Az alábbi képlet azt mutatja be, hogyan határozható meg a csíkozott kötetek üzenetsor-mélysége.  
    ![Egy olyan diagram, amely a Q D/lemez/idő oszlopban megadott számú oszlop számát mutatja, és a köteten a csíkozott kötet Q D értékkel egyenlő.](media/premium-storage-performance/image7.png)

## <a name="throttling"></a>Throttling

Az Azure Premium Storage a kiválasztott virtuálisgép-mérettől és a választott IOPS függően megadott számú adatmennyiséget és átviteli sebességet biztosít. Bármikor, amikor az alkalmazás megkísérli a IOPS vagy az átviteli sebesség megadását a virtuális gép vagy a lemez által kezelhető korlátok felett, Premium Storage fogja szabályozni. Ez a jegyzék az alkalmazás csökkentett teljesítményének formáját képezi. Ez magasabb késést, alacsonyabb átviteli sebességet vagy alacsonyabb IOPS jelenthet. Ha Premium Storage nem szabályozza a szabályozást, az alkalmazás teljesen meghiúsulhat, ha túllépi az erőforrások eléréséhez szükséges erőforrásokat. Így a teljesítménybeli problémák elkerülése érdekében a szabályozás miatt mindig elegendő erőforrást kell kiépíteni az alkalmazáshoz. Vegye figyelembe, hogy a fentiekben ismertetett virtuálisgép-méretek és a lemez méretei című szakaszban leírt módon. A teljesítményértékelés a legjobb módszer arra, hogy kiderítse, milyen erőforrásokra lesz szüksége az alkalmazás üzemeltetéséhez.

## <a name="next-steps"></a>Következő lépések

Ha a lemez teljesítménytesztét szeretné használni, tekintse meg a következő cikkeket: a lemez teljesítményértékelése:

* Linux esetén: [az alkalmazás teljesítményteszte Azure Disk Storage](./disks-benchmarks.md)
* Windows esetén: [egy lemez teljesítményértékelése](./disks-benchmarks.md).

További információ a rendelkezésre álló lemezek típusairól:

* Linux esetén: [válassza ki a lemez típusát](disks-types.md)
* Windows esetén: [válassza ki a lemez típusát](disks-types.md)

SQL Server felhasználók esetében olvassa el a SQL Server teljesítményére vonatkozó ajánlott eljárásokat ismertető cikket:

* [Az Azure-beli SQL Server teljesítményének bevált eljárásai Virtual Machines](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md)
* [Az Azure Premium Storage a legmagasabb teljesítményt nyújtja az Azure-beli virtuális gépek SQL Server számára](https://cloudblogs.microsoft.com/sqlserver/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm/)