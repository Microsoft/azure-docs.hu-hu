---
title: Azure-beli virtuális gépek teljesítményének megoldása Linux vagy Windows rendszeren
description: Ez a cikk a virtuális gép (VM) általános teljesítményének hibaelhárítását ismerteti a szűk keresztmetszetek figyelésével és megfigyelésével, valamint az esetlegesen felmerülő problémák megoldásával kapcsolatos esetleges szervizelést.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/18/2019
ms.author: v-miegge
ms.openlocfilehash: cde84a018d307bada6a6b6f3b5467b8addd007b1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100571442"
---
# <a name="troubleshoot-azure-virtual-machine-performance-on-linux-or-windows"></a>Azure-beli virtuális gépek teljesítményének megoldása Linux vagy Windows rendszeren

Ez a cikk a virtuális gép (VM) általános teljesítményének hibaelhárítását ismerteti a szűk keresztmetszetek figyelésével és megfigyelésével, valamint az esetlegesen felmerülő problémák megoldásával kapcsolatos esetleges szervizelést. A monitorozás mellett a Perfinsights is használható, amely az ajánlott eljárásokkal kapcsolatos ajánlásokat és az IO/CPU/memória körüli szűk keresztmetszeteket tartalmazó jelentést biztosít. A Perfinsights [Windows](./how-to-use-perfinsights.md) és [Linux](./how-to-use-perfinsights-linux.md) rendszerű virtuális gépekhez is elérhető az Azure-ban.

Ez a cikk végigvezeti a teljesítménybeli szűk keresztmetszetek diagnosztizálásának monitorozásán.

## <a name="enabling-monitoring"></a>Figyelés engedélyezése

### <a name="azure-iaas-virtual-machine-monitoring"></a>Azure IAAS-beli virtuális gépek figyelése

A vendég virtuális gép figyeléséhez használja az Azure-beli virtuális gépek figyelését, amely riasztást küld az egyes magas szintű erőforrás-feltételekről. Ellenőrizze, hogy engedélyezve van-e a virtuálisgép-diagnosztika, lásd: [Azure-erőforrások naplói – áttekintés](../../azure-monitor/essentials/tutorial-resource-logs.md). Ha a következő jelenik meg, akkor valószínűleg nincs engedélyezve a diagnosztika:

![A figyelés nincs engedélyezve](media/troubleshoot-performance-virtual-machine-linux-windows/1-virtual-machines-monitoring-not-enabled.png)
 
### <a name="enable-vm-diagnostics-through-microsoft-azure-portal"></a>A VM Diagnostics engedélyezése a Microsoft Azure Portal

A VM-diagnosztika engedélyezése:

1. Ugrás a virtuális gépre
2. Kattintson a **diagnosztikai beállítások** elemre.
3. Válassza ki a Storage-fiókot, és kattintson a **vendég szintű figyelés engedélyezése** lehetőségre.

   ![Kattintson a beállítások, majd a diagnosztika elemre.](media/troubleshoot-performance-virtual-machine-linux-windows/2-virtual-machines-diagnostics.png)

Az **ügynök** lapon a diagnosztika beállításnál használt Storage-fiókot a **diagnosztikai beállítások** területen tekintheti meg.

![Storage-fiók keresése](media/troubleshoot-performance-virtual-machine-linux-windows/3-check-storage-account.png)

### <a name="enable-storage-account-diagnostics-through-azure-portal"></a>A Storage-fiók diagnosztika engedélyezése Azure Portal

A tárterület nagyon fontos, ha az Azure-ban egy virtuális gép i/o-teljesítményét szeretnénk elemezni. A tárolással kapcsolatos mérőszámokhoz további lépésként engedélyeznie kell a diagnosztikát. Ezt is engedélyezheti, ha csak a Storage szolgáltatással kapcsolatos számlálókat szeretné elemezni.

1. A virtuális gép kiválasztásával azonosítsa a virtuális gép által használt Storage-fiókot (vagy fiókokat). Kattintson a **Beállítások**, majd a **lemezek** elemre:

   ![Kattintson a beállítások, majd a lemezek elemre.](media/troubleshoot-performance-virtual-machine-linux-windows/4-storage-disks-disks-selection.png)

2. A portálon lépjen a virtuális gép Storage-fiókjához (vagy fiókjaihoz), és végezze el a következő lépéseket:

   1. Kattintson az Áttekintés elemre a fenti lépésben megtalált Storage-fiókhoz.
   2. A rendszer az alapértelmezett metrikákat jeleníti meg. 

    ![Alapértelmezett metrikák](media/troubleshoot-performance-virtual-machine-linux-windows/5-default-metrics.png)

3. Kattintson bármelyik mérőszámra, amely egy másik panelt jelenít meg, amely további lehetőségeket kínál a metrikák konfigurálásához és hozzáadásához.

   ![Metrikák hozzáadása](media/troubleshoot-performance-virtual-machine-linux-windows/6-add-metrics.png)

A következő beállítások konfigurálása:

1.  Válassza a **Metrikák** lehetőséget.
2.  Válassza ki az **erőforrást** (Storage-fiók).
3.  Válassza ki a **névteret**
4.  Válassza a **metrika** elemet.
5.  Válassza ki az **Összesítés** típusát
6.  Ezt a nézetet rögzítheti az irányítópulton.

## <a name="observing-bottlenecks"></a>Szűk keresztmetszetek megfigyelése

Ha a szükséges mérőszámok kezdeti telepítési folyamatán keresztül következik be, és a virtuális gép és a kapcsolódó Storage-fiók diagnosztikai funkciójának engedélyezése után is át lehet térni az elemzési fázisra.

### <a name="accessing-the-monitoring"></a>A figyelés elérése

Válassza ki a vizsgálni kívánt Azure-beli virtuális gépet, és válassza a **figyelés** lehetőséget.

![Figyelés kiválasztása](media/troubleshoot-performance-virtual-machine-linux-windows/7-select-monitoring.png)
 
### <a name="timelines-of-observation"></a>Megfigyelési határidők

Annak megállapításához, hogy van-e erőforrás-szűk keresztmetszete, tekintse át az adatait. Ha úgy találja, hogy a számítógépe nem működik megfelelően, de a rendszer azt jelezte, hogy a teljesítmény a közelmúltban csökkent, tekintse át a teljesítménnyel kapcsolatos mérőszámok adatait tartalmazó időtartományt a jelentett változás előtt és után.

### <a name="check-for-cpu-bottleneck"></a>A CPU szűk keresztmetszetének keresése

![A CPU szűk keresztmetszetének keresése](media/troubleshoot-performance-virtual-machine-linux-windows/8-cpu-bottleneck-time-range.png)

1. Szerkessze a diagramot.
2. Állítsa be az időtartományt.
3. Ezután fel kell vennie a következőt: CPU százalék vendég operációs rendszer
4. Mentse el.

### <a name="cpu-observe-trends"></a>A CPU megfigyelésének trendjei

Ha teljesítménnyel kapcsolatos problémákat tapasztal, vegye figyelembe a trendeket, és Ismerje meg, ha azok érintik Önt. A következő fejezetekben a portálon a figyelési grafikonokat használjuk a trendek megjelenítéséhez. Emellett hasznosak lehetnek az erőforrás-viselkedésre vonatkozó kereszthivatkozások egyazon időszakban. A diagramok testreszabásához kattintson [Azure monitor adatplatformra](../../azure-monitor/data-platform.md).

Beszúró – a beszúró folyamat egy ütemezett feladathoz/ismert eseményhez kapcsolódhat. Ha azonosítani tudja a feladatot, állapítsa meg, hogy a feladat a szükséges teljesítményszint szerint fut-e. Ha a teljesítmény elfogadható, előfordulhat, hogy nem kell megemelnie az erőforrásokat.

Tüskén vagy állandó – gyakran egy új munkaterhelést jelez. Ha nem ismeri fel a munkaterhelést, a virtuális gépen engedélyezze a figyelést, hogy megtudja, milyen folyamat (vagy folyamatok) okozza a működést. A folyamat felismerése után állapítsa meg, hogy a megnövekedett felhasználás a nem hatékony kód vagy a normál fogyasztás okozta-e. Normál fogyasztás esetén döntse el, hogy a folyamat a szükséges teljesítményszint szerint működik-e.

Konstans – megállapítja, hogy a virtuális gép mindig ezen a szinten fut-e, vagy csak ezen a szinten fut, mivel a diagnosztika engedélyezve van. Ha igen, azonosítsa a problémát okozó folyamatot (vagy folyamatokat), és vegye fontolóra az erőforrás további részét.

Folyamatosan növekvő – A felhasználás állandó növekedése gyakran vagy nem hatékony kód, vagy egy folyamat, amely több felhasználói munkaterhelést vesz igénybe.

### <a name="high-cpu-utilization-remediation"></a>Magas CPU-kihasználtság szervizelése

Ha az alkalmazás vagy folyamat nem a megfelelő teljesítményszint fut, és a 95% + CPU-kihasználtsági állandót látja, akkor a következő feladatok egyikét hajthatja végre:

* Azonnali használatra – növelje a virtuális gép méretét több maggal rendelkező méretre
* A probléma megismerése – az alkalmazás/folyamat és a hibakeresés megkeresése ennek megfelelően.

Ha megnövelte a virtuális gépet, és a processzor továbbra is 95%-ot futtat, állapítsa meg, hogy ez a beállítás a jobb teljesítményt vagy magasabb szintű alkalmazások átviteli sebességét kínálja elfogadható szintre. Ha nem, akkor hárítsa el az egyes application\process.

A Perfinsights for [Windows](./how-to-use-perfinsights.md) vagy a [Linux](./how-to-use-perfinsights-linux.md) segítségével elemezheti, hogy melyik folyamat hajtja a CPU-felhasználást. 

## <a name="check-for-memory-bottleneck"></a>Memória szűk keresztmetszetének keresése

A metrikák megtekintése:

1. Szakasz hozzáadása.
2. Csempe hozzáadása
3. Nyissa meg a gyűjteményt.
4. Válassza ki a memóriahasználat és a húzás elemet. Ha a csempe dokkolva van, kattintson a jobb gombbal, és válassza a **6x4** lehetőséget.

### <a name="memory-observe-trends"></a>Memória megfigyelésének trendjei

A memóriahasználat megmutatja, hogy mennyi memóriát használ a virtuális gép. Ismerje meg a trendet, és azt, hogy a probléma mikor jelenik meg. Mindig több mint 100 MB szabad memóriával kell rendelkeznie.

Spike és állandó/állandó fogyasztás – a nagy memória kihasználtsága nem lehet a rossz teljesítmény oka, mivel egyes alkalmazások, például a viszonyítási adatbázisok nagy mennyiségű memóriát foglalnak le, és ez a kihasználtság nem jelent jelentős értéket. Ha azonban több memóriára éhes alkalmazás van, előfordulhat, hogy gyenge teljesítményt tapasztal a memóriában, így a vágás és a lapozás/csere lemezre történik. Ez a gyenge teljesítmény gyakran észlelhető az alkalmazás teljesítményére gyakorolt hatás miatt.

Folyamatosan növekvő fogyasztás – egy lehetséges "felmelegedés" alkalmazás, ez a fogyasztás gyakori az adatbázis-hajtóműveknél. Ugyanakkor jelezheti egy alkalmazás memóriavesztését is. Azonosítsa az alkalmazást, és Ismerje meg, hogy a működés várható-e.

Oldal-vagy lapozófájl-használat – győződjön meg arról, hogy a Windows lapozófájlt használja (a (z) D: \) vagy a Linux swap-fájlban ( `/dev/sdb` ) található). Ha ezekkel a kötetekkel nem rendelkezik ezekkel a fájlokkal, akkor ellenőrizze, hogy vannak-e magas olvasási/írási műveletek a lemezeken. Ez a probléma kevés memóriát jelez.

### <a name="high-memory-utilization-remediation"></a>Nagy memória-kihasználtság szervizelése

A magas memóriahasználat feloldásához hajtsa végre a következő feladatok bármelyikét:

* Azonnali támogatás vagy lapozófájl használata esetén – növelje a virtuális gép méretét több memóriával, majd figyelje a figyelést.
* A probléma ismertetése – alkalmazások/folyamat és hibakeresés a nagy mennyiségű memóriabeli alkalmazások azonosításához.
* Ha ismeri az alkalmazást, tekintse meg a memória kiosztásának korlátját.

Ha egy nagyobb virtuális gépre való frissítés után kiderül, hogy továbbra is állandó a 100%-os folyamatos növekedés, azonosítsa az alkalmazást/folyamatot és a hibakeresést.

A Perfinsights [Windows](./how-to-use-perfinsights.md) vagy [Linux](./how-to-use-perfinsights-linux.md) rendszeren is használható, hogy elemezze, melyik folyamat használja a memóriát. 

## <a name="check-for-disk-bottleneck"></a>A lemez szűk keresztmetszetének keresése

A virtuális gép tárolási alrendszerének ellenőrzéséhez tekintse meg a diagnosztika az Azure-beli virtuálisgép-szinten a virtuális gép diagnosztika és a Storage-fiók diagnosztika használatával.

A virtuálisgép-specifikus hibaelhárítás esetében használhatja a Perfinsights for Windows vagy [Linux](./how-to-use-perfinsights-linux.md) [rendszert](./how-to-use-perfinsights.md) , ami segíthet elemezni, hogy melyik folyamat hajtja az IO-t. 

Ne feledje, hogy a zónák redundáns és Premium Storage fiókjaihoz nem tartoznak számlálók. Az ezekkel a számlálókkal kapcsolatos problémák esetén egy támogatási esetet kell megnövelni.

### <a name="viewing-storage-account-diagnostics-in-monitoring"></a>A Storage-fiók diagnosztika megtekintése a figyelésben

Az alábbi elemek működéséhez lépjen a virtuális gép Storage-fiókjába a portálon:

![A Storage-fiók diagnosztika megtekintése a Figyelésben](media/troubleshoot-performance-virtual-machine-linux-windows/9-virtual-machine-storage-account.png)

1. Szerkessze a figyelési gráfot.
2. Állítsa be az időtartományt.
3. Adja hozzá az alábbi lépésekben ismertetett számlálókat.
4. Mentse a módosításokat.

### <a name="disk-observe-trends-standard-storage-only"></a>A lemezek megfigyelésének trendjei (csak standard Storage)

A tárterülettel kapcsolatos problémák azonosításához tekintse meg a Storage-fiók diagnosztika és a virtuálisgép-diagnosztika teljesítmény-mérőszámait.

Az alábbi ellenőrzéseknél keresse meg a legfontosabb trendeket, amikor a probléma időtartományán belül előforduló problémák történnek.

#### <a name="check-azure-storage-availability--add-the-storage-account-metric-availability"></a>Az Azure Storage rendelkezésre állásának keresése – a Storage-fiók metrikájának hozzáadása: rendelkezésre állás

Ha a rendelkezésre állás csökkenését tapasztalja, akkor a platformmal kapcsolatos probléma lehet az [Azure állapotának](https://azure.microsoft.com/status/)vizsgálatával. Ha nem jelenik meg probléma, adjon meg egy új támogatási kérelmet.

#### <a name="check-for-azure-storage-timeout---add-the-storage-account-metrics"></a>Az Azure Storage időtúllépésének keresése – a Storage-fiók metrikáinak hozzáadása:

* ClientTimeOutError
* ServerTimeOutError
* AverageE2ELatency
* AverageServerLatency
* TotalRequests

A * TimeOutError mérőszámokban szereplő értékek azt jelzik, hogy egy IO-művelet túl sokáig tartott, és időtúllépés történt. A következő lépések elvégzése segít azonosítani a lehetséges okokat.

A Averageserverlatency értéket mutatnak egy időben nő a TimeOutErrors. Ebben a helyzetben új támogatási kérést kell létrehoznia.

A AverageE2ELatency az ügyfél késését jelöli. Ellenőrizze, hogy az alkalmazás hogyan végzi el a IOPS. Keressen egy növekedést vagy állandóan magas TotalRequests mérőszámot. Ez a metrika a IOPS jelöli. Ha elindítja a Storage-fiók vagy egy virtuális merevlemez korlátait, a késés a szabályozáshoz köthető.

#### <a name="check-for-azure-storage-throttling---add-the-storage-account-metrics-throttlingerror"></a>Az Azure Storage szabályozásának keresése – a Storage-fiók metrikáinak hozzáadása: ThrottlingError

A szabályozás értéke azt jelzi, hogy a tárolási fiók szintjén szabályozza a szabályozást, ami azt jelenti, hogy a fiók IOPS korlátja. A metrika **TotalRequests** ellenőrzésével meghatározhatja, hogy a IOPs küszöbértéket éri-e el.

Vegye figyelembe, hogy a virtuális merevlemezek 500 IOPS vagy 60 MBit-os korláttal rendelkeznek, de a IOPS 20000-os összesített korlátja köti őket.

Ezzel a metrikával nem állapítható meg, hogy melyik blob okozza a szabályozást, és melyeket érinti. Azonban a Storage-fiók IOPS vagy bejövő/kimenő korlátját is megkeresi.

Ha meg szeretné állapítani, hogy az IOPS-korlátot használja-e, lépjen be a Storage-fiók diagnosztikát, és ellenőrizze a TotalRequests, és nézze meg, hogy közeledik-e a 20000 TotalRequests. Azonosíthatja a minta változását, függetlenül attól, hogy a korlátot az első alkalommal látja-e, vagy hogy a korlát egy adott időpontban történik.

Ha a standard szintű tárolóban új lemezes ajánlatokat használ, a IOPS és az átviteli sebesség korlátai eltérőek lehetnek, de a standard szintű Storage-fiók összesített korlátja 20000 IOPS (a Premium Storage-nak eltérő korlátai vannak a fiók vagy a lemez szintjén). További információ a különböző standard szintű lemezes ajánlatokról és a lemezes korlátokról:

* A [Windows rendszerű virtuálisgép-lemezek méretezhetősége és teljesítménybeli céljai](../disks-scalability-targets.md).

#### <a name="references"></a>Hivatkozások

* [Méretezhetőségi és teljesítményi célok a prémium oldal blob Storage-fiókjaihoz](../../storage/blobs/scalability-targets-premium-page-blobs.md)

A Storage-fiók sávszélességét a Storage-fiók Metrikái határozzák meg: TotalIngress és TotalEgress. A sávszélesség a redundancia és a régiók típusától függően eltérő küszöbértékekkel rendelkezik.

* [A standard szintű Storage-fiókok méretezhetősége és teljesítménybeli céljai](../../storage/common/scalability-targets-standard-account.md)

A TotalIngress és a TotalEgress a Storage-fiók redundancia-típusa és-régiója esetében a bejövő és a kimenő forgalomra vonatkozó korlátok között tájékozódhat.

A virtuális géphez csatlakoztatott virtuális merevlemezek átviteli sebességére vonatkozó korlátok ellenőrzéséhez. Adja hozzá a virtuális gép metrikai lemezét olvasás és írás.

A standard szintű tárolóban az új lemezes ajánlatok különböző IOPS és átviteli korlátokkal rendelkeznek (a IOPS nem teszik közzé VHD-n). Tekintse meg az adatait, és ellenőrizze, hogy a virtuális merevlemezen a VHD (k) kombinált átviteli sebessége (i) a lemez olvasása és írása paranccsal, majd a virtuális gép tárolási konfigurációjának optimalizálása a múltbeli egyszeri VHD-korlátok skálázására. További információ a különböző standard szintű lemezes ajánlatokról és a lemezes korlátokról:

* A [Windows rendszerű virtuálisgép-lemezek méretezhetősége és teljesítménybeli céljai](../disks-scalability-targets.md).

### <a name="high-disk-utilizationlatency-remediation"></a>Nagy lemezterület-kihasználtság/késés szervizelése

Csökkentse az ügyfelek késését, és optimalizálja a virtuális gépek IO-jét a korábbi VHD-korlátok méretezéséhez

* [A Windows rendszerhez készült IO optimalizálása az Azure-ban](../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md?toc=/azure/virtual-machines/windows/toc.json)

* [Az IO for Linux optimalizálása az Azure-ban](/archive/blogs/igorpag/azure-storage-secrets-and-linux-io-optimizations)

#### <a name="reduce-throttling"></a>A szabályozás csökkentése

Ha a Storage-fiókok felső korlátait sújtja, a virtuális merevlemezek átkerülnek a Storage-fiókok között. Tekintse át az [Azure Storage skálázhatósági és teljesítményi célkitűzéseit](../../storage/common/scalability-targets-standard-account.md).

### <a name="increase-throughput-and-reduce-latency"></a>Az átviteli sebesség növelése és a késés csökkentése

Ha késésre érzékeny alkalmazásra van szüksége, és magas átviteli sebességet igényel, telepítse át a virtuális merevlemezeket az Azure Premium Storage-ba a DS és a GS sorozatú virtuális gép használatával.

Ezek a cikkek a konkrét forgatókönyveket tárgyalják:

* [Áttelepítés Azure Premium Storage-ba](../windows/migrate-to-managed-disks.md)

* [Az Azure Premium Storage használata a SQL Server](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-sql-server-premium-storage)

## <a name="next-steps"></a>Következő lépések

Ha a cikk bármely pontján további segítségre van szüksége, vegye fel a kapcsolatot az Azure-szakértőkkel [az MSDN Azure-ban és stack overflow fórumokon](https://azure.microsoft.com/support/forums/).

Másik lehetőségként egy Azure-támogatási incidenst is megadhat. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a **támogatás kérése** lehetőséget.
