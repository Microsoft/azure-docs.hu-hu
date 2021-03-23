---
title: A Azure NetApp Files használatának előnyei SQL Server üzembe helyezéshez | Microsoft Docs
description: Részletes díjszabási teljesítménybeli előnyöket mutat a Azure NetApp Files SQL Server üzembe helyezéshez való használatával kapcsolatban.
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
ms.date: 03/19/2021
ms.author: b-juche
ms.openlocfilehash: 46fe7570b7b9ea9446918d407dbe87596b8d0496
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863904"
---
#  <a name="benefits-of-using-azure-netapp-files-for-sql-server-deployment"></a>A Azure NetApp Files használatának előnyei SQL Server üzembe helyezéshez

A Azure NetApp Files a tárolási megoldások letiltásához képest csökkenti SQL Server teljes tulajdonlási költséget (TCO).  A blokkos tárolással a virtuális gépek az I/O-t és a sávszélességet korlátozzák a lemezes műveletekhez, a hálózati sávszélesség korlátozásait csak Azure NetApp Files alkalmazza a rendszer.  Más szóval a rendszer nem alkalmazza a virtuálisgép-I/O-korlátokat Azure NetApp Filesra. Ezen I/O-korlátok nélkül SQL Server a Azure NetApp Fileshoz csatlakoztatott kisebb virtuális gépeken, illetve a jóval nagyobb virtuális gépeken futó SQL Server is. A példányok méretezése lefelé csökken, így a korábbi árcédulát 25%-ra csökkentheti a számítási költségeket.  *Azure NetApp Files segítségével csökkentheti a számítási költségeket.*  

A számítási költségek azonban kisebbek, mint a SQL Server a licencek költségei.  Microsoft SQL Server [Licencelés](https://download.microsoft.com/download/B/C/0/BC0B2EA7-D99D-42FB-9439-2C56880CAFF4/SQL_Server_2017_Licensing_Datasheet.pdf) a fizikai mag számával van társítva. Ennek megfelelően a csökkenő példány mérete még nagyobb költségmegtakarítást mutat be a szoftveres licencelés esetében. *A szoftveres licencek költségeit a Azure NetApp Files segítségével csökkentheti.*

A tárolási díj maga az adatbázis tényleges méretétől függően változó. A kiválasztott tárterülettől függetlenül a kapacitás díja, legyen az felügyelt lemez vagy fájlmegosztás.  Az adatbázis méretének növekedésével és a tárterület költségének növekedésével a tárterület a TCO-növekedéshez járul hozzá, ami hatással van a teljes költségre.  Ennek megfelelően az állítást a következőképpen kell beállítani: az *SQL Server központi telepítési költségeket csökkentheti a Azure NetApp files.* 

Ez a cikk részletes díjszabási és teljesítménybeli előnyöket mutat a Azure NetApp Files SQL Server üzembe helyezéshez való használatával kapcsolatban. Nem csak a kisebb példányok rendelkeznek elegendő CPU-val ahhoz, hogy az adatbázis csak nagyobb példányszámú blokknál legyen lehetséges, *sok esetben a kisebb példányok még nagyobb teljesítményű, mint a nagyobb, lemezes Azure NetApp files miatt.* 

## <a name="detailed-cost-analysis"></a>Részletes költségelemzés 

Ebben a szakaszban a két ábra mutatja be a TCO-példát.  A felügyelt lemezek száma és típusa, a Azure NetApp Files szolgáltatási szint, valamint az egyes forgatókönyvek kapacitása a legjobb ár-kapacitás teljesítmény elérése érdekében lett kiválasztva.  Az egyes ábrák csoportosított gépekből állnak (D16, Azure NetApp Files, például a felügyelt lemezzel való D64 képest), és az árak az egyes gépek típusától függően megoszlanak.  

Az első ábrán a megoldás általános díja látható egy 1 TiB-es adatbázis-mérettel, összehasonlítva a D16s_v3 a D64, a D8 a D32 és a D4-D16. Az egyes konfigurációk tervezett IOPs zöld vagy sárga vonal jelzi, és megfelel a jobb oldali Y tengelynek.

[![Olyan ábra, amely a megoldás általános költségeit jeleníti meg egy 1 – TiB-adatbázis mérete alapján. ](../media/azure-netapp-files/solution-sql-server-cost-1-tib.png)](../media/azure-netapp-files/solution-sql-server-cost-1-tib.png#lightbox)


A második ábrán egy 50-TiB-adatbázis használatával járó teljes díj látható. Az összehasonlítások nem azonosak – a D16 képest a Azure NetApp Files és az D64. 

[Az ![ általános költségeket a 50 – TiB-adatbázis mérete alapján bemutató ábra. ](../media/azure-netapp-files/solution-sql-server-cost-50-tib.png)](../media/azure-netapp-files/solution-sql-server-cost-50-tib.png#lightbox)
 
## <a name="performance-and-lots-of-it"></a>Teljesítmény és sok más  

Ahhoz, hogy a jelentős mértékű csökkentési állítást el lehessen juttatni, nagy teljesítményre van szükség – az általános Azure Inventory támogatás 80 000-es lemez IOPS példaként. Egy Azure NetApp Files kötet 80 000 adatbázis-IOPS érhet el, és az olyan példányok, mint például a D16, ugyanazt a memóriát tudják használni. A D16, amely általában 25 600 lemez IOPS, a D64 mérete 25%.  A D64s_v3 80 000 lemezes IOPS képes, és mint ilyen, kiváló felső szintű összehasonlító pontot mutat be.

A D16s_v3 Azure NetApp Files kötetet 80 000 adatbázis-IOPS tud vezetni. Az SQL Storage benchmark (SSB) teljesítményértékelési eszköz által igazolt módon a D16-példány a D64-példány által elérhetővé tett mennyiségnél nagyobb terhelést ért el a 125%-ban.  Az eszköz részleteiért tekintse meg a [SSB-tesztelési eszköz](#ssb-testing-tool) szakaszt.

1 – TiB-os munkakészlet-méret és 80%-os olvasási, 20%-os frissítési SQL Server munkaterhelés használata esetén a rendszer a D példány osztályában lévő példányok többségének teljesítményét méri. a legtöbb, nem az összes, mert a D2-és D64-példányok magukban is kizárták a tesztelésből. Az előbbi kimaradt, mert nem támogatja a gyorsított hálózatkezelést, az utóbbi pedig az összehasonlítási pont. Tekintse meg a következő gráfot a D4s_v3, D8s_v3, D16s_v3 és D32s_v3 korlátainak megismeréséhez.  A felügyelt lemezes tárolásra vonatkozó tesztek nem jelennek meg a gráfban. Az összehasonlítási értékek közvetlenül az [Azure Virtual Machine Limits táblából](../virtual-machines/dv3-dsv3-series.md) származnak a D osztályú példány típusához.

A Azure NetApp Files a D osztály minden példánya képes megfelelni vagy meghaladni a példányok lemezes teljesítményére vonatkozó képességeket a két alkalommal nagyobb példányszámban.  *A szoftverlicenc-költségek jelentősen csökkenthetők a Azure NetApp Filesával.*  

* A D4 75%-os CPU-kihasználtsága megfelelt a D16 lemezes képességeinek.  
    * A D16 25 600 lemez IOPS.  
* A D8 75%-os CPU-kihasználtsága megfelelt a D32 Lemezes képességeinek.  
    * A D32 51 200 lemez IOPS.  
* A D16 55%-os CPU-kihasználtsága megfelelt a D64 lemezes képességeinek.  
    * A D64 80 000 lemez IOPS.  
* A D32 15%-os CPU-kihasználtsága megfelelt a D64 lemezes képességeinek is.  
    * A fentiekben leírtak szerint a D64 80 000 lemez IOPS.  

### <a name="s3b-cpu-limits-test--performance-versus-processing-power"></a>S3B CPU-korlátok tesztelése – teljesítmény és feldolgozási teljesítmény

A következő ábra összefoglalja a S3B CPU-korlátok tesztjét:

![Olyan diagram, amely átlagos CPU-százalékarányt mutat az Egypéldányos SQL Server Azure NetApp Files.](../media/azure-netapp-files/solution-sql-server-single-instance-average-cpu.png)

A méretezhetőség csak a történet részét képezi. A másik rész a késés.  Az egyik dolog, hogy a kisebb méretű virtuális gépek képesek legyenek a sokkal magasabb I/O-sebességek megadására, ez egy másik dolog, ami az alább láthatónál kevesebb egyszámjegyű késéssel jár.  

* A D4 26 000 IOPS-et vezetett Azure NetApp Files 2,3-MS késéssel.  
* A D8 51 000 IOPS ellen vezetett a 2,0-MS késéssel Azure NetApp Files.  
* A D16 88 000 IOPS ellen vezetett a 2,8-MS késéssel Azure NetApp Files.
* A D32 80 000 IOPS ellen vezetett a 2,4-MS késéssel Azure NetApp Files.  

### <a name="s3b-per-instance-type-latency-results"></a>S3B/példány típusú késés eredményei

Az alábbi ábrán az Egypéldányos SQL Server késése látható Azure NetApp Files:

![Az Egypéldányos SQL Server késését ábrázoló diagram Azure NetApp Files.](../media/azure-netapp-files/solution-sql-server-single-instance-latency.png)

## <a name="ssb-testing-tool"></a>SSB-tesztelési eszköz 
 
A [TPC-E](http://www.tpc.org/tpce/) benchmarking eszköz kialakításával kihangsúlyozza a *számítást* a *tárterület* helyett. Az ebben a szakaszban bemutatott tesztelési eredmények az SQL Storage benchmark (SSB) nevű stressz-tesztelési eszközön alapulnak.  A SQL Server tárolási teljesítményteszt nagy léptékű SQL-végrehajtást hajthat végre egy SQL Server-adatbázison egy OLTP számítási feladat szimulálása érdekében, hasonlóan a [SLOB2 Oracle benchmarking eszközhöz](https://kevinclosson.net/slob/). 

A SSB eszköz létrehoz egy SELECT és UPDATE vezérelt számítási feladatot, amely az említett utasításokat közvetlenül az Azure-beli virtuális gépen futó SQL Server adatbázisra állítja elő.  Ebben a projektben a SSB munkaterhelések 1 – 100 SQL Server felhasználók számára lettek felhasználva, 10 vagy 12 közbenső ponttal, felhasználónként 15 percen belül.  Az ezekből a futtatásokból származó teljesítmény-mérőszámok a perfmon-ből származnak, az ismételhetőségi SSB forgatókönyvek szerint háromszor futott. 

Maguk a tesztek 80% SELECT és 20% UPDATE utasítással lettek konfigurálva, így 90%-os véletlenszerű olvasási.  Maga az adatbázis, amelyet SSB hozott létre, 1000 GB méretű volt. A szolgáltatás összesen 15 felhasználói táblát és 9 000 000 sort tartalmaz felhasználónkénti táblában, és 8192 bájt/sor. 

A SSB teljesítményteszt egy nyílt forráskódú eszköz.  Az [SQL Storage benchmark GitHub oldalán](https://github.com/NetApp/SQL_Storage_Benchmark.git)szabadon elérhető.  


## <a name="in-summary"></a>Összefoglalás  

A Azure NetApp Files segítségével növelheti az SQL Server teljesítményét, miközben jelentősen csökkentheti a teljes tulajdonlási költségeket. 

## <a name="next-steps"></a>Következő lépések

* [SMB-kötet létrehozása az Azure NetApp Files számára](azure-netapp-files-create-volumes-smb.md) 
* [Megoldási architektúrák Azure NetApp Files használatával – SQL Server](azure-netapp-files-solution-architectures.md#sql-server) 

