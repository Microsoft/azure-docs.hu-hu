---
title: A teljesítmény diagram Azure Monitor for VMs
description: A teljesítmény a Azure Monitor for VMs szolgáltatása, amely automatikusan feltérképezi az alkalmazás-összetevőket Windows-és Linux-rendszereken, és leképezi a szolgáltatások közötti kommunikációt. Ez a cikk részletesen ismerteti, hogyan használhatja azt különböző helyzetekben.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/15/2019
ms.openlocfilehash: a50ba39777e6a9d3d609e584c0c7d872f2a65f35
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80283718"
---
# <a name="how-to-chart-performance-with-azure-monitor-for-vms"></a>A teljesítmény diagram Azure Monitor for VMs

Azure Monitor for VMs olyan teljesítménymutatókat tartalmaz, amelyek több fő teljesítménymutatót (KPI-ket) céloznak meg, amelyek segítségével meghatározhatja, hogy a virtuális gép milyen jól van végrehajtva. A diagramok egy adott időszakon belül jelenítik meg az erőforrás-használatot, így azonosíthatja a szűk keresztmetszeteket, a rendellenességeket, vagy átválthat az egyes gépekre mutató perspektívára, hogy megtekintse az erőforrás-kihasználtságot a kiválasztott metrika alapján. Noha a teljesítmény kezelése során számos szempontot figyelembe kell venni, Azure Monitor for VMs figyeli a processzorral, a memóriával, a hálózati adapterrel és a lemez kihasználtságával kapcsolatos fő operációsrendszer-teljesítménymutatókat. A teljesítmény kiegészíti az állapotfigyelő funkciót, és segít a lehetséges rendszerösszetevő-meghibásodást jelző problémák megoldásában, a hatékonyság növelését és optimalizálását, illetve a kapacitás tervezésének támogatását.  

## <a name="multi-vm-perspective-from-azure-monitor"></a>Több virtuális gépre kiterjedő perspektíva Azure Monitor

Azure Monitor a teljesítmény szolgáltatás az előfizetésben vagy a környezetben lévő munkacsoportokban üzembe helyezett összes figyelt virtuális gép nézetét tartalmazza. A Azure Monitorhoz való hozzáféréshez hajtsa végre az alábbi lépéseket. 

1. A Azure Portal válassza a **figyelő**elemet. 
2. A **megoldások** szakaszban válassza a **Virtual Machines** lehetőséget.
3. Válassza a **teljesítmény** fület.

![A VM-elemzések teljesítménye a legfontosabb N lista nézet](media/vminsights-performance/vminsights-performance-aggview-01.png)

Ha egynél több Log Analytics munkaterülettel rendelkezik, a **felső N diagramok** lapon válassza ki azt a munkaterületet, amelyet a megoldás az oldal tetején lévő **munkaterület** -választóval engedélyez. A **csoport** választója előfizetéseket, erőforráscsoportokat, [számítógépcsoportokat](../platform/computer-groups.md)és virtuálisgép-méretezési csoportokat ad vissza a kiválasztott munkaterülethez kapcsolódó számítógépeken, amelyeket az ezen a lapon és a többi oldalon található diagramokon megjelenített eredmények további szűréséhez használhat. A kijelölés csak a teljesítmény szolgáltatásra vonatkozik, és nem végez átadást az állapotra vagy a térképre.  

Alapértelmezés szerint a diagramok az elmúlt 24 órában láthatók. A **TimeRange** -választó használatával akár 30 napig is lekérdezheti a korábbi időtartományokat, hogy megmutassa, hogyan nézett ki a teljesítmény a múltban.

A lapon látható öt kapacitás-kihasználtsági diagram a következő:

* CPU-kihasználtság (%) – az első öt gépet mutatja, amelynek a legmagasabb az átlagos processzor-kihasználtsága 
* Rendelkezésre álló memória – megjeleníti az első öt gépet a rendelkezésre álló memória legkisebb átlagos mennyiségétől számítva. 
* Használt logikai lemezterület (%) – az első öt gépet mutatja, amely a legnagyobb átlagos lemezterületet használja az összes lemez kötetén. 
* Elküldési sebesség (bájt) – megjeleníti az első öt gépet, amely a legtöbb elküldési bájtos átlagot mutatja 
* Bájtok fogadásának aránya – az első öt, a maximálisan fogadott bájtot tartalmazó gépet mutatja. 

Az öt diagram jobb felső sarkában található rögzítés ikonra kattintva rögzítheti a kiválasztott diagramot az utolsó megtekintett Azure-irányítópulton.  Az irányítópultról átméretezheti és áthelyezheti a diagramot. Ha kiválasztja a diagramot az irányítópultról, a rendszer átirányítja Azure Monitor for VMs és betölti a helyes hatókört és nézetet.  

Ha az öt diagram egyikén a rögzítés ikontól balra található ikonra kattint, megnyílik a **legfontosabb N listanézet** .  Itt láthatja az adott teljesítmény-mérőszámhoz tartozó erőforrás-felhasználást az egyes virtuális gépeknél a listanézetban, és hogy melyik gép a legmagasabb trendet eredményezi.  

![A kijelölt teljesítmény metrikájának legfontosabb N listanézet](media/vminsights-performance/vminsights-performance-topnlist-01.png)

Ha a virtuális gépre kattint, a **Tulajdonságok** ablaktábla ki van bontva a jobb oldalon, hogy megjelenjenek a kiválasztott elem tulajdonságai, például az operációs rendszer által jelentett rendszerinformációk, az Azure-beli virtuális gép tulajdonságai stb. A **gyors hivatkozások** szakaszban található egyik lehetőségre kattintva közvetlenül a kiválasztott virtuális gépről irányítja át a szolgáltatást.  

![Virtuális gép tulajdonságai ablaktábla](./media/vminsights-performance/vminsights-properties-pane-01.png)

Váltson az **összesített diagramok** lapra, ahol megtekintheti az átlagos vagy a percentilis mértékek alapján szűrt teljesítménymutatókat.  

![Virtuálisgép-betekintő teljesítmény összesített nézete](./media/vminsights-performance/vminsights-performance-aggview-02.png)

A következő kapacitás-kihasználtsági diagramok vannak megadva:

* CPU-kihasználtság (%) – az átlagos és legfelső 95. százalékos értékeit jelző alapértelmezett értékek 
* Rendelkezésre álló memória – az alapértelmezett értékek az átlagot, az első 5. és a 10. százalékos értéket mutatják 
* Felhasznált logikai lemezterület (%) – az átlag és a 95. százalékos értékeit jelző alapértelmezett értékek 
* Elküldhető bájtok aránya – az alapértelmezett értékek az elküldés átlagos bájtjait mutatják 
* Bájtok fogadásának aránya – az alapértelmezett érték a fogadott bájtok átlagos számát mutatja.

A diagramok részletességét az időtartományon belül is módosíthatja, ha az **AVG**, a **min**, a **Max**, az **50**, a **90**és a **95.** lehetőséget választja a percentilis-választóban.

Ha szeretné megtekinteni az egyes virtuális gépek erőforrás-felhasználását egy lista nézetben, és megtekintheti, hogy melyik gép a legmagasabb kihasználtsággal rendelkezik, válassza az **első N lista** fület.  A **felső N lista** oldal az első 20 gépet mutatja, amelyet a legtöbbet a 95. percentilis használ a metrikus *CPU-kihasználtság%*-ban.  Ha több gépet szeretne látni, válassza a **Betöltés**lehetőséget, és az eredmények kibontásával jelenítse meg a legfontosabb 500-es gépeket. 

>[!NOTE]
>A lista egyszerre legfeljebb 500 gépet tud megjeleníteni.  
>

![Legfelső N lista oldalának példája](./media/vminsights-performance/vminsights-performance-topnlist-01.png)

Ha egy adott virtuális gépen szeretné szűrni az eredményeket a listában, adja meg a számítógép nevét a **Keresés név alapján** szövegmezőbe.  

Ha inkább a kihasználtságot szeretné megtekinteni egy másik teljesítménymutató alapján, a **metrika** legördülő listából válassza ki a **rendelkezésre álló memóriát**, a **használt logikai lemezterületet (%)**, a **hálózat fogadott bájt/mp**vagy a **hálózat által küldött bájt/mp** elemet, valamint a lista frissítéseit, hogy megjelenjenek az adott metrika hatókörének kihasználtsága.  

Ha kiválasztja a virtuális gépet a listából, megnyílik a **Tulajdonságok** panel a lap jobb oldalán, és Itt választhatja ki a **teljesítmény részleteit**.  Megnyílik a **virtuális gép részletei** lap, és az adott virtuális gépre van kiterjedően, ami a virtuálisgép-elemzések teljesítményének közvetlenül az Azure-beli virtuális gépről való elérésekor is hasonló élményt nyújt.  

## <a name="view-performance-directly-from-an-azure-vm"></a>Teljesítmény megtekintése közvetlenül egy Azure-beli virtuális gépről

Közvetlenül egy virtuális gépről való hozzáféréshez hajtsa végre az alábbi lépéseket.

1. A Azure Portal válassza a **Virtual Machines**lehetőséget. 
2. A listából **válassza ki a**virtuális gépet, és a **figyelés** területen válassza az eredmények lehetőséget.  
3. Válassza a **teljesítmény** fület. 

Ez a lap nem csak a teljesítmény-kihasználtsági diagramokat tartalmazza, hanem egy táblázatot is, amely minden felderített logikai lemezhez, annak kapacitásához, kihasználtságához és teljes átlagához tartozik.  

A következő kapacitás-kihasználtsági diagramok vannak megadva:

* CPU-kihasználtság (%) – az átlagos és legfelső 95. százalékos értékeit jelző alapértelmezett értékek 
* Rendelkezésre álló memória – az alapértelmezett értékek az átlagot, az első 5. és a 10. százalékos értéket mutatják 
* Felhasznált logikai lemezterület (%) – az átlag és a 95. százalékos értékeit jelző alapértelmezett értékek 
* Logikai lemez IOPS – az átlag és a 95. százalékos értékeit jeleníti meg
* Logikai lemez MB/s – alapértelmezett érték, amely az átlagos és a 95. percentilis értéket jeleníti meg
* A felhasznált logikai lemez maximális száma (%) – az alapértelmezett érték az átlagos és a 95. százalékos értéket mutatja.
* Elküldhető bájtok aránya – az alapértelmezett értékek az elküldés átlagos bájtjait mutatják 
* Bájtok fogadásának aránya – az alapértelmezett érték a fogadott bájtok átlagos számát mutatja.

Az egyik diagram jobb felső sarkában található rögzítés ikonra kattintva a kiválasztott diagramot a legutóbbi Azure-irányítópultra koppintva láthatja. Az irányítópultról átméretezheti és áthelyezheti a diagramot. Ha kiválasztja a diagramot az irányítópultról, azzal átirányítja Azure Monitor for VMs és betölti a teljesítmény részletes nézetét a virtuális géphez.  

![VIRTUÁLIS gépek teljesítményének közvetlen kimutatása a virtuálisgép-nézetből](./media/vminsights-performance/vminsights-performance-directvm-01.png)

## <a name="view-performance-directly-from-an-azure-virtual-machine-scale-set"></a>Teljesítmény megtekintése közvetlenül egy Azure virtuálisgép-méretezési csoportból

Ha közvetlenül egy Azure virtuálisgép-méretezési csoportból szeretne hozzáférni, hajtsa végre a következő lépéseket.

1. A Azure Portal válassza a **virtuálisgép-méretezési**csoportok lehetőséget.
2. A listából **válassza ki a** virtuális gépet, és a **figyelés** területen válassza az eredmények lehetőséget a **teljesítmény** lap megtekintéséhez.

Ezen az oldalon betöltődik a Azure Monitor teljesítmény nézet, amely a kijelölt méretezési csoportra terjed ki. Ez lehetővé teszi, hogy megtekintse a méretezési csoport legfontosabb N példányait a figyelt metrikák készletében, megtekintheti a méretezési csoport összesített teljesítményét, és megtekintheti az egyes példányok N a méretezési csoporton belül a kiválasztott mérőszámok tendenciáit. Ha kiválaszt egy példányt a listanézet segítségével, betöltheti a térképet, vagy megtekintheti az adott példány részletes teljesítmény nézetét.

Az egyik diagram jobb felső sarkában található rögzítés ikonra kattintva a kiválasztott diagramot a legutóbbi Azure-irányítópultra koppintva láthatja. Az irányítópultról átméretezheti és áthelyezheti a diagramot. Ha kiválasztja a diagramot az irányítópultról, azzal átirányítja Azure Monitor for VMs és betölti a teljesítmény részletes nézetét a virtuális géphez.  

![Virtuális gépek teljesítményének beállítása közvetlenül a virtuálisgép-méretezési csoport nézetből](./media/vminsights-performance/vminsights-performance-directvmss-01.png)

>[!NOTE]
>A méretezési csoport példányai nézetében egy adott példányhoz is elérheti a részletes teljesítmény nézetet. A **Beállítások** szakaszban keresse meg a **példányok** **elemet,** majd válassza az eredmények lehetőséget.



## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan használhatók a Azure Monitor for VMsban található [munkafüzetek](vminsights-workbooks.md) a teljesítmény-és hálózati metrikák további elemzéséhez.  

- Az észlelt alkalmazások függőségeivel kapcsolatos további tudnivalókért lásd: [Azure monitor for VMS Térkép megtekintése](vminsights-maps.md).
