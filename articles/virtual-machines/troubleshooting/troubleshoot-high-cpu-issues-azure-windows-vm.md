---
title: Azure-beli windowsos virtuális gépek magas processzorhasználatával kapcsolatos problémák elhárítása
description: .
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: mnanda
manager: dcscontentpm
editor: v-jesits
tags: azure-resource-manager
ms.assetid: 9971cc26-916f-4e49-83cd-71eca74804f0
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 9/24/2020
ms.author: mnanda
ms.openlocfilehash: 124650f4570608efabba3d8002c14ad06c4782ad
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100571509"
---
# <a name="troubleshoot-high-cpu-issues-for-azure-windows-virtual-machines"></a>Azure-beli windowsos virtuális gépek magas processzorhasználatával kapcsolatos problémák elhárítása

## <a name="summary"></a>Összefoglalás

A teljesítménnyel kapcsolatos problémák különböző operációs rendszerekben vagy alkalmazásokban fordulnak elő, és minden probléma egyedi módszert igényel a hibaelhárításhoz. Ezeknek a problémáknak a nagy része a CPU, a memória, a Hálózatkezelés és a bemeneti/kimeneti (I/O) elemek körül forog, ahol a probléma előfordul. Ezen területek mindegyike különböző tüneteket állít elő (esetenként egyszerre), és más diagnózist és megoldást igényel.

Ez a cikk a Windows operációs rendszert futtató Azure Virtual Machines (VM) szolgáltatásban előforduló nagy CPU-használattal kapcsolatos problémákat ismerteti.

### <a name="high-cpu-issues-on-azure-windows-vms"></a>Nagy CPU-problémák az Azure Windows rendszerű virtuális gépeken

Az I/O-és a hálózati késési problémáktól eltekintve a processzor és a memória hibaelhárításához ugyanazok az eszközök és lépések szükségesek, mint a helyszíni kiszolgálók. A Microsoft által jellemzően támogatott eszközök egyike a PerfInsights (Windows és Linux rendszereken egyaránt elérhető). A PerfInsights egy felhasználóbarát jelentésben biztosíthatja az Azure-beli virtuális gépek ajánlott eljárásainak diagnosztizálását. A PerfInsights olyan burkoló eszköz is, amely segít a perfmon-, XPerf-és netmon-adatok gyűjtésében az eszközön kiválasztott jelzők függvényében.

A meglévő teljesítmény-hibaelhárító eszközök, például a perfmon vagy a Procmon nagy része az Azure Windows rendszerű virtuális gépeken fog működni. A PerfInsights azonban kifejezetten az Azure-beli virtuális gépekhez lett kialakítva, így több információ érhető el, például az Azure ajánlott eljárásai, az SQL ajánlott eljárásai, a nagy felbontású I/O-késési diagramok, a processzor és a memória lapjai stb.

Függetlenül attól, hogy User-Mode vagy kernel módban fut-e, az aktív folyamatok bármilyen szálához CPU-ciklus szükséges ahhoz, hogy a rendszer az általa létrehozott kódot futtassa. Számos probléma közvetlenül kapcsolódik a munkaterheléshez. A kiszolgálón található erőforrás-felhasználás típusa, beleértve a PROCESSZORt is.

#### <a name="common-factors"></a>Gyakori tényezők

A következő tényezők gyakoriak a nagy CPU-helyzetekben:

- Egy közelmúltbeli kód módosítása vagy központi telepítése, amely főleg olyan alkalmazásokra vonatkozik, mint a Internet Information Services (IIS), a Microsoft SharePoint, a Microsoft SQL Server vagy a harmadik féltől származó alkalmazások.

- Egy közelmúltbeli frissítés, amely egy operációsrendszer-szintű frissítéssel, illetve az alkalmazás szintű összesítő frissítésekkel és javításokkal kapcsolatos lehet.

- Egy lekérdezés módosul vagy elavult indexek. A SQL Server és az Oracle adatcsomag-alkalmazásaiban a lekérdezési terv optimalizálása is egy másik tényező. Az adatváltozások vagy a megfelelő indexek hiánya miatt több lekérdezés nagyobb számítási igényű lehet.

- Azure-beli VM-specifikus. Bizonyos folyamatok, például a RDAgent és a bővítményekre jellemző folyamatok, például a figyelési ügynök, az MMA-ügynök vagy a biztonsági ügyfél, nagy CPU-fogyasztást okozhatnak. Ezeket a folyamatokat konfigurációból vagy ismert problémák perspektívából kell megtekinteni.

## <a name="troubleshoot-the-issue"></a>A probléma megoldása

Ez a cikk a problémás folyamat elkülönítésére koncentrál. A további elemzés a nagy CPU-fogyasztást eredményező folyamatra jellemző.

Ha például a folyamat SQL Server (sqlservr.exe), a következő lépésekkel elemezheti, hogy melyik lekérdezés használta a legtöbb CPU-ciklust egy adott időszakban.

### <a name="scope-the-issue"></a>A probléma hatóköre

Íme néhány kérdés, amely a probléma megoldásakor kérdezi fel a problémát:

- Létezik a probléma mintája? A nagy CPU-probléma például minden nap, hét vagy hónap bizonyos időpontjában fordul elő? Ha igen, összekapcsolhatja ezt a problémát egy feladatokkal, jelentésekkel vagy felhasználói bejelentkezéssel?

- A nagy CPU-probléma a legutóbbi kód módosítása után indul el? Alkalmazta a frissítést a Windowsban vagy egy alkalmazásban?

- A nagy CPU-probléma a munkaterhelés változása után indul el, például a felhasználók számának növekedését, a nagyobb beáramlást vagy nagyobb számú jelentést?

- Az Azure-ban a nagy CPU-problémák a következő feltételek bármelyikével kezdődnek?

  - Egy közelmúltbeli újratelepítést vagy újraindítást követően
  - SKU vagy VM típusának módosításakor
  - Új bővítmény hozzáadásakor
  - A terheléselosztó módosítása után

### <a name="azure-caveats"></a>Azure-figyelmeztetések

A számítási feladatok megismerése. Ha kijelöl egy virtuális GÉPET, akkor előfordulhat, hogy alábecsüli a virtuális CPU (vCPU) számát, ha megtekinti a teljes havi üzemeltetési költséget. Ha a munkaterhelés nagy számítási igényű, egy vagy két vCPU rendelkező kisebb virtuálisgép-SKU kiválasztásával munkaterhelési problémák merülhetnek fel. A számítási feladatok különböző konfigurációinak tesztelésével meghatározhatja a szükséges legjobb számítástechnikai képességet.

Vannak olyan virtuálisgép-sorozatok, mint például a B (burst módú) sorozatok, amelyek minőségbiztosítási (QA) és tesztelési célokra ajánlottak. Ezen adatsorozatok üzemi környezetben való használata korlátozza a számítási képességet a CPU-kreditek kimerülése után.

Az olyan ismert alkalmazások esetében, mint például az SQL Server, az Oracle, az RDS (Távoli asztali szolgáltatások), a Windows Virtual Desktop, az IIS vagy a SharePoint, vannak olyan Azure ajánlott eljárásokat tartalmazó cikkek, amelyek a számítási feladatok minimális konfigurálására vonatkozó javaslatokat tartalmaznak.

### <a name="ongoing-high-cpu-issues"></a>Folyamatos, nagy CPU-problémák

Ha a probléma most fordul elő, ez a legjobb lehetőség a folyamat nyomkövetésének rögzítésére a probléma okának meghatározásához. A folyamat megkereséséhez használhatja a helyi Windows-kiszolgálókon használt meglévő eszközöket. Az Azure-beli virtuális gépek Azure-támogatása az alábbi eszközöket ajánlja.

### <a name="perfinsights"></a>PerfInsights

A PerfInsights az Azure támogatásának ajánlott eszköze a virtuális gépek teljesítményével kapcsolatos problémák megoldásához. Ez az ajánlott eljárások és a processzor, a memória és a nagy felbontású I/O-diagramok dedikált elemzési lapjaira szolgál. Az IT-OnDemand a Azure Portalon vagy a virtuális gépen belül is futtathatja. Az Azure-támogatási csapattal megoszthatja az adatközpontot.

#### <a name="run-perfinsights"></a>PerfInsights futtatása

A PerfInsights a [Windows](./how-to-use-perfinsights.md) és a [Linux](./how-to-use-perfinsights-linux.md) operációs rendszerhez is elérhető. A Windows esetében itt láthatók a lehetőségek.

#### <a name="run-and-analyze-reports-through-azure-portal"></a>Jelentések futtatása és elemzése Azure Portal

Ha [a Azure Portal keresztül települ](./performance-diagnostics.md), a ténylegesen telepít egy bővítményt a virtuális gépen. A felhasználók a PerfInsights bővítményként is telepíthetik úgy, hogy közvetlenül a virtuális gépek paneljén, majd a teljesítmény [-](./performance-diagnostics-vm-extension.md)diagnosztika lehetőséget választja.

#### <a name="azure-portal-option-1"></a>1. Azure Portal lehetőség

Tallózzon a virtuális gép paneljén, és válassza a **teljesítmény-diagnosztika** lehetőséget. A rendszer arra kéri, hogy telepítse a (bővítményeket használó) beállítást azon a virtuális gépen, amelyet kiválasztott.

  ![A teljesítmény-diagnosztika telepítése](./media/troubleshoot-high-cpu-issues-azure-windows-vm/1-install-performance-diagnostics.png)

#### <a name="azure-portal-option-2"></a>Azure Portal 2. lehetőség

Keresse meg a virtuális gép panelen a **problémák diagnosztizálásához és megoldásához szükséges problémákat** , és keresse meg a **virtuális gépek teljesítményével kapcsolatos problémákat**.

  ![A virtuális gépek teljesítményével kapcsolatos problémák elhárítása](./media/troubleshoot-high-cpu-issues-azure-windows-vm/2-troubleshoot-vm-performance-issues.png)

Ha a **hibakeresés** lehetőséget választja, a PerfInsights telepítési képernyője betöltődik.

Ha a **telepítés** lehetőséget választja, a telepítés különböző gyűjteményi lehetőségeket biztosít.

  ![Teljesítményelemzés](./media/troubleshoot-high-cpu-issues-azure-windows-vm/3-performance-analysis.png)

A képernyőképen a számozott beállítások a következő megjegyzéseket érintik:

1. A **nagy CPU** beállításnál válassza a **teljesítmény elemzése** vagy a **speciális** lehetőséget.

2. Ha itt ad hozzá tüneteket, a rendszer hozzáadja a jelentéshez, amely segítséget nyújt az információk Azure-támogatással való megosztásához.

3. Válassza ki az adatgyűjtés időtartamát. A nagy CPU beállításnál válasszon legalább 15 percet vagy többet. Azure Portal módban akár 15 perces adatot is gyűjthet. A hosszabb gyűjtési időszakok esetében a programot a virtuális gépen futtatott végrehajtható fájlként kell futtatni.

4. Ha az Azure-támogatással kéri az adatok gyűjtését, itt adhatja hozzá a jegy számát. A mező nem kötelező.

5. Válassza ezt a mezőt a végfelhasználói licencszerződés (EULA) elfogadásához.

6. Akkor válassza ezt a mezőt, ha a jelentést elérhetővé kívánja tenni az Azure támogatási csapatának ebben az esetben.

A jelentést az előfizetés alatt található egyik Storage-fiókban tárolja a rendszer. Később is megtekintheti és letöltheti.

#### <a name="run-perfinsights-from-within-the-vm"></a>PerfInsights futtatása a virtuális gépről

Ez a módszer akkor használható, ha hosszabb időtartamra szeretné futtatni a PerfInsights. A [PerfInsights cikk](./how-to-use-perfinsights.md#how-do-i-run-perfinsights) részletesen ismerteti a PerfInsights végrehajthatóként való futtatásához szükséges különböző parancsokat és jelzőket. A magas CPU-használat érdekében a következő módok egyikére lesz szüksége:

- Speciális forgatókönyv

  - `PerfInsights /run advanced xp /d 300 /AcceptDisclaimerAndShareDiagnostics`

- Virtuális gép lassú (teljesítmény) forgatókönyve

  - `PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics /sa <StorageAccountName> /sk <StorageAccountKey>`

A parancs kimenete ugyanabban a mappában lesz, ahol a PerfInsights végrehajtható fájlt mentette.

#### <a name="what-to-look-for-in-the-report"></a>Mit kell keresni a jelentésben

A jelentés futtatása után a tartalom helye attól függ, hogy a Azure Portal vagy végrehajtható fájlként futtatták-e. Bármelyik lehetőség esetében a létrehozott napló mappához vagy a letöltéshez (ha Azure Portal) helyben, az elemzéshez férhet hozzá.

### <a name="run-through-the-azure-portal"></a>Futtatás a Azure Portal

  ![Nagy hatású teljesítmény-diagnosztika](./media/troubleshoot-high-cpu-issues-azure-windows-vm/4-high-impact-performance-diagnostics.png)

  ![Jelentés letöltése](./media/troubleshoot-high-cpu-issues-azure-windows-vm/5-download-report.png)

#### <a name="run-from-within-vm"></a>Futtatás virtuális gépen belülről

A mappa szerkezete a következő képekhez hasonlít:

  ![Kimenet kiválasztása](./media/troubleshoot-high-cpu-issues-azure-windows-vm/6-select-output.png)

  ![Mappa szerkezete](./media/troubleshoot-high-cpu-issues-azure-windows-vm/7-folder-structure.png)

1. A kimeneti mappában található további gyűjtemények, például a perfmon, a XPerf, a netmon, az SMB-naplók, az eseménynaplók és így tovább.

1. A tényleges jelentés elemzéssel és javaslatokkal együtt.

1. A jelentés mind a teljesítmény (VMslow), mind a fejlett esetében a PerfInsights futtatásának időtartamára vonatkozó **perfmon** -adatokat gyűjti.

1. Az eseménynaplók gyors áttekintést nyújtanak a rendszerszintű vagy a folyamatok összeomlásának részleteiről.

#### <a name="where-to-start"></a>A kiindulópont

Nyissa meg az PerfInsights jelentést. Az **eredmények** lap az erőforrás-felhasználás szempontjából naplózza az összes kiugró kifejezést. Ha nagy CPU-használat van, akkor az **eredmények** lap a nagy hatás vagy a közepes hatás szerint kategorizálja.

  ![A hatás szintjének erőforrásai](./media/troubleshoot-high-cpu-issues-azure-windows-vm/8-impact-level-resources.png)

Az előző példához hasonlóan a PerfInsights 30 percig futott. Ebben az esetben a Kiemelt folyamat a CPU-t a magasabb oldalon használta. Ha ugyanez a folyamat futott a gyűjtemény ideje alatt, a hatás szintje **magasra** változott.

Ha kibontja az **eredményekkel** kapcsolatos eseményt, több kulcsfontosságú adat jelenik meg. A lap felsorolja a folyamatokat csökkenő sorrendben, **átlagos CPU** -használat alapján, és megjeleníti, hogy a folyamat kapcsolódott-e a rendszerhez, egy Microsoft tulajdonú alkalmazáshoz (SQL, IIS) vagy egy harmadik féltől származó folyamathoz.

#### <a name="more-details"></a>További részletek

A **CPU** alatt található egy dedikált lapra, amely a részletes minták elemzéséhez, a mag-vagy a folyamatokhoz használható.

A **felső CPU-fogyasztók** lap két külön részből áll, és itt tekintheti meg a processzor statisztikáit. Az alkalmazás kialakítása gyakran Single-Threaded vagy egyetlen processzorhoz is PIN-kód. Ebben a forgatókönyvben egy vagy több mag 100 százalékkal fut, míg más magok a várt szinteken futnak. Ezek a forgatókönyvek összetettebbek, mert a kiszolgáló átlagos PROCESSZORa úgy tűnik, hogy a várt módon fut, de a magas kihasználtságú magokon rögzített folyamatok a vártnál lassabbak lesznek.

  ![magas CPU-használat](./media/troubleshoot-high-cpu-issues-azure-windows-vm/9-high-cpu-usage.png)

A második szakasz (ugyanilyen fontos), a **leghosszabb ideig futó CPU-fogyasztók**. Ez a szakasz a folyamat részleteit és a processzor használati mintáját mutatja be. A lista a legfelső szintű CPU-fogyasztókat terheli.

  ![Tom Long Running CPU-fogyasztó](./media/troubleshoot-high-cpu-issues-azure-windows-vm/10-top-long-running-cpu-consumers.png)

Ez a két lap elegendő lesz a következő hibaelhárítási lépések elérési útjának megadásához. A nagy CPU-állapotot kiváltó folyamattól függően meg kell adnia a korábban kért kérdéseket. Az olyan folyamatokhoz, mint a SQL Server (sqlservr.exe) vagy az IIS (w3wp.exe), egy adott részletezést igényelnek a lekérdezés vagy a kód módosításainál, amelyek ezt a feltételt okozzák. A rendszerfolyamatok, például a WMI vagy a Lsass.exe esetében más elérési utat kell követnie.

Az Azure-beli virtuális gépekkel kapcsolatos folyamatok, például a RDAgent, a OMS és a figyelési bővítmények végrehajtható fájljai esetében előfordulhat, hogy új buildet vagy verziót kell kijavítania az Azure támogatási csapatának súgójában.

### <a name="perfmon"></a>Perfmon

A **perfmon** az egyik legkorábbi eszköz a Windows Serveren futó erőforrásokkal kapcsolatos problémák elhárításához. Nem ad egyértelmű jelentést, amely ajánlásokat vagy megállapításokat tartalmaz. Ehelyett a felhasználónak meg kell vizsgálnia az összegyűjtött adatokat, és egy adott szűrőt kell használnia a különböző számlálók kategóriái között.

A PerfInsights további naplóként gyűjti a perfmon-t a VMSlow és a speciális forgatókönyvekhez. A perfmon azonban egymástól függetlenül is összegyűjthető, és ezekkel a további előnyökkel jár:

- Távolról is gyűjthető.

- **Feladatokkal** ütemezhető.

- Hosszabb időtartamra vagy folyamatos módban is összegyűjthetők a szolgáltatással.

Gondolja át ugyanezt a példát, amely a PerfInsights látható, így láthatja, hogy a perfmon hogyan jeleníti meg ezt az információt. A szükséges számlálók kategóriái a következők:

- A processzor adatai > a processzoridő > _Total

- Folyamat >% ProcessorTime > összes példánya

#### <a name="where-to-start"></a>A kiindulópont

A perfmon kimeneti fájlnevei `.blg` kiterjesztéssel rendelkeznek. Ezeket a fájlokat egymástól függetlenül, vagy a PerfInsights használatával is összegyűjtheti. Ebben a témakörben a PerfInsights-adatokban található Perfmon-t fogja használni, `.blg` amelyet az előző példában gyűjtöttek össze.

A perfmon szolgáltatásban nincsenek elérhető alapértelmezett, felhasználó által készített jelentések. A diagram típusának módosítása különböző nézetekből áll, de a folyamat szűrése (vagy a bűnös folyamatok azonosításához szükséges munka) kézi.

> [!NOTE]
> A [PAL eszköz](https://github.com/clinthuffman/PAL) képes `.blg` fájlokat használni és részletes jelentéseket készíteni.

A kezdéshez válassza a **számlálók hozzáadása** kategóriát.

1. A **rendelkezésre álló számlálók** területen válassza ki a **% ProcessorTime** számlálót a **Processzor adatai** kategóriában.

1. Válassza a **_Total** lehetőséget, amely az összes összesített mag statisztikáit tartalmazza.

1. Válassza a **Hozzáadás** lehetőséget. Az ablakban a **hozzáadott számlálók** alatt a **% ProcessorTime** látható.

  ![Processzoridő hozzáadása](./media/troubleshoot-high-cpu-issues-azure-windows-vm/11-add-processor-time.png)

A számlálók betöltését követően a gyűjtemény időkeretében megjelenik a line trend-diagramok. Kiválaszthatja vagy törölheti a számlálókat. Eddig csak egy számlálót adott hozzá.

  ![Teljesítményfigyelő beállításai](./media/troubleshoot-high-cpu-issues-azure-windows-vm/12-performance-monitor-1.png)

Minden számláló **átlagos**, **minimális** és **maximális** értékkel fog rendelkezni. Koncentráljon az **átlagos** és a **maximális** értékekre is, mivel az átlagos érték az adatgyűjtés időtartamától függően változhat. Ha a nagy CPU-aktivitás 10 percig volt látható, míg a teljes gyűjtemény 40 perc volt, az átlagos értékek sokkal alacsonyabbak lesznek.

Az előző trend Graph azt mutatja, hogy a **teljes processzor** 80%-kal közel körülbelül 15 percet vesz igénybe.

#### <a name="identify-the-process"></a>A folyamat azonosítása

Azonosítottank, hogy a kiszolgálónak egy adott időtartamon át nagy CPU-fogyasztásra volt szükségük, de még nem azonosították az illesztőprogramot. A PerfInsights használatával ellentétben ebben az esetben manuálisan kell keresnie a bűnös folyamatot.

Ehhez a feladathoz törölni vagy törölni kell a korábban hozzáadott **% ProcessorTime** -számlálókat, majd hozzá kell adni egy új kategóriát:

- Folyamat >% ProcessorTime > összes példánya

Ez a kategória az adott időpontban futó összes folyamat számlálóit fogja betölteni.

  ![Számlálók hozzáadása](./media/troubleshoot-high-cpu-issues-azure-windows-vm/13-add-counters.png)

Egy tipikus üzemi számítógépen több száz vagy folyamat is futtatható. Ezért eltarthat egy ideig, amíg az alacsony vagy a sík trend gráfot úgy tűnik, hogy minden számláló törölve lesz.

A folyamat felgyorsításához használja a **hisztogram** nézetet, és változtassa meg a nézet típusát a **vonalról** a **hisztogramra**, amely egy oszlopdiagram használatát teszi lehetővé. Könnyebben kiválaszthatja azokat a folyamatokat, amelyek nagy CPU-használatot tapasztalnak a gyűjtés ideje alatt.

Mivel a rendszer mindig egy **teljes** sávot jelenít meg, a nagy mennyiségű sávokra koncentrálhat. A többi sávot törölheti a nézet tisztításához. Most váltson vissza a **sor** nézetre.

  ![Teljesítményfigyelő kijelzői](./media/troubleshoot-high-cpu-issues-azure-windows-vm/14-performance-monitor-2.png)

Most már könnyebb a bűnös folyamat elfogása. Alapértelmezés szerint a **maximális** és a **minimális** érték többszöröse a kiszolgálón lévő magok vagy a folyamat szálai számának.

  ![Teljesítményfigyelő eredményei](./media/troubleshoot-high-cpu-issues-azure-windows-vm/15-performance-monitor-3.png)

Az elérhető eszközök listája nem fejeződik be a PerfInsights a perfmon-nél. Hozzáférhet más eszközökhöz, például a **ProcessMonitor** (ProcMon) vagy a **XPerf**. Számos, a szükséges módon használható, harmadik féltől származó eszköz is rendelkezésre áll.

### <a name="azure-monitoring-tools"></a>Az Azure figyelési eszközei

Az Azure-beli virtuális gépek megbízható metrikákkal rendelkeznek, amelyek alapvető információkat tartalmaznak, például a PROCESSZORt, a hálózati I/O-t és az I/O-bájtokat. A speciális mérőszámok, például a Azure Monitor esetében csak néhány beállítást kell megadnia a megadott Storage-fiók konfigurálásához és használatához.

#### <a name="basic-default-counters"></a>Alapszintű (alapértelmezett) számlálók

  ![Diagram metrikái](./media/troubleshoot-high-cpu-issues-azure-windows-vm/16-chart-metrics.png)

#### <a name="enabling-azure-monitor"></a>Azure Monitor engedélyezése

Azure Monitor mérőszámok engedélyezése után a szoftver egy bővítményt telepít a virtuális gépre, majd elkezdi a részletes metrikák gyűjtését, beleértve a perfmon-számlálókat is.

  ![Diagnosztikai Storage-fiók](./media/troubleshoot-high-cpu-issues-azure-windows-vm/17-diagnostics-storage-account.png)

Az **alapszintű** számlálók kategóriái **alapértelmezettként** vannak beállítva. Beállíthat azonban **Egyéni** gyűjteményt is.

  ![Teljesítményszámlálók](./media/troubleshoot-high-cpu-issues-azure-windows-vm/18-performance-counters.png)

A beállítások engedélyezése után megtekintheti ezeket a **vendég** számlálókat a **metrikák** szakaszban. Beállíthat **riasztásokat** is (beleértve az e-mail-üzeneteket is), ha a metrikák egy bizonyos küszöbértéket érnek el.

  ![Metrikák névtere](./media/troubleshoot-high-cpu-issues-azure-windows-vm/19-metrics-namespace.png)

További információ arról, hogyan használható az Azure monitor az Azure-beli virtuális gépek kezeléséhez: [Az Azure Virtual Machines szolgáltatás figyelése Azure monitor](../../azure-monitor/vm/monitor-vm-azure.md)használatával.

### <a name="reactive-troubleshooting"></a>Újraaktív hibaelhárítás

Ha a probléma már bekövetkezett, fel kell derítenie, hogy mi okozta a nagy CPU-problémát az első helyen. A reaktív irányvonal trükkös lehet. Az adatgyűjtési mód nem lesz olyan hasznos, mert a probléma már megtörtént.

Ha ez a probléma egyszer fordul elő, nehéz lehet megállapítani, hogy melyik alkalmazás okozta azt. Ha az Azure-beli virtuális gép OMS vagy más diagnosztikai követés használatára lett konfigurálva, akkor továbbra is bepillantást nyerhet a probléma okozta problémákra.

Ha ismétlődő mintázattal rendelkezik, gyűjtsön adatokat az idő alatt, amikor a probléma várhatóan tovább fog történni.

A PerfInsights még nem rendelkezik **ütemezett futtatási** funkcióval. A perfmon azonban futtatható és ütemezhető a parancssoron keresztül is.

### <a name="logman-command"></a>Logman parancs

A **Logman Create Counter** parancs a perfmon-gyűjtemény parancssorból történő futtatására, a **Feladatkezelő** segítségével történő, illetve távoli futtatására szolgál.

**Minta** (magában foglalja a távoli gyűjtési módot)

`Logman create counter LOGNAME -u DOMAIN\USERNAME * -f bincirc -v mmddhhmm -max 300 -c "\\SERVERNAME\LogicalDisk(*)\*" "\\SERVERNAME\Memory\*" "\\SERVERNAME\Network Interface(*)\*" "\\SERVERNAME\Paging File(*)\*" "\\SERVERNAME\PhysicalDisk(*)\*" "\\SERVERNAME\Process(*)\*" "\\SERVERNAME\Redirector\*" "\\SERVERNAME\Server\*" "\\SERVERNAME\System\*" "\\SERVERNAME\Terminal Services\*" "\\SERVERNAME\Processor(*)\*" "\\SERVERNAME\Cache\*" -si 00:01:00`

Az Logman.exe is elindítható egy társ Azure-beli virtuálisgép-számítógépről is ugyanabban a VNET.

Ha többet szeretne megtudni ezekről a paraméterekről, tekintse meg a [Logman-létrehozási számlálót](/windows-server/administration/windows-commands/logman-create-counter).

Miután a rendszer begyűjti a perfmon-adatokat a probléma előfordulásakor, az adatok elemzésének hátralévő lépései megegyeznek a korábban tárgyalt lépésekkel.

## <a name="conclusion"></a>Összegzés

Bármilyen teljesítménnyel kapcsolatos probléma esetén a probléma megoldásához kulcsfontosságú a számítási feladatok ismertetése. A különböző virtuálisgép-SKU-ra és a különböző lemezes tárolási beállításokra vonatkozó beállításokat úgy kell kiértékelni, hogy az éles számítási feladatokra koncentrál. A megoldások különböző virtuális gépeken történő tesztelésének folyamata segíthet a legjobb döntés elvégzésében.

Mivel a felhasználói műveletek és az adatok mennyisége változó, mindig a virtuális gép számítástechnikai, hálózatkezelési és I/O-képességeinek pufferét kell megőrizni. A számítási feladatok hirtelen változása mostantól nem befolyásolja a lehető legjelentősebb változást.

Ha előre látja a munkaterhelést, térjen át egy magasabb szintű SKU-ra, amely több számítási teljesítménnyel rendelkezik. Ha a munkaterhelés nagy számítási igényű, válassza ki a virtuális gép SKU-t.