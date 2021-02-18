---
title: Capacity and Performance megoldás a Azure Monitorban | Microsoft Docs
description: A figyelő Capacity and Performance megoldásával megismerheti a Hyper-V-kiszolgálók kapacitását.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/13/2017
ms.openlocfilehash: 9fce67c0ffe1951b0bfc325603b6d8f985dd804d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100592204"
---
# <a name="plan-hyper-v-virtual-machine-capacity-with-the-capacity-and-performance-solution-deprecated"></a>A Hyper-V virtuális gépek kapacitásának megtervezése a Capacity and Performance megoldással (elavult)

![Capacity and Performance szimbólum](./media/capacity-performance/capacity-solution.png)

> [!NOTE]
> A Capacity and Performance megoldás elavult.  Azok a felhasználók, akik már telepítették a megoldást, továbbra is használhatják, de Capacity and Performance nem vehetők fel új munkaterületekre.

A figyelő Capacity and Performance megoldásával könnyebben megismerheti a Hyper-V-kiszolgálók kapacitását. A megoldás betekintést nyújt a Hyper-V környezetbe a gazdagépek és a Hyper-V-gazdagépeken futó virtuális gépek általános kihasználtságával (processzor, memória és lemez). A rendszer az összes gazdagép és a rajtuk futó virtuális gépek által használt CPU-, memória-és lemez-metrikákat gyűjti.

A megoldás:

-   Megjeleníti a legmagasabb és a legalacsonyabb CPU-és memória-kihasználtságú gazdagépeket
-   Megjeleníti a legmagasabb és a legalacsonyabb CPU-és memória-kihasználtságú virtuális gépeket
-   A legmagasabb és a legalacsonyabb IOPS és az átviteli sebesség kihasználtságával rendelkező virtuális gépek megjelenítése
-   Megjeleníti, hogy mely virtuális gépek futnak a gazdagépeken
-   A nagy teljesítményű, IOPS és késéssel rendelkező legfelső lemezeket jeleníti meg a fürt megosztott köteteiben
- Lehetővé teszi a csoportok alapján történő testreszabást és szűrést

> [!NOTE]
> A Capacity and Performance-megoldás előző, a kapacitás-felügyelet nevű verziója System Center Operations Manager és System Center Virtual Machine Manager egyaránt szükséges. Ez a frissített megoldás nem rendelkezik ezekkel a függőségekkel.


## <a name="connected-sources"></a>Összekapcsolt források

Az alábbi táblázat áttekintést nyújt az ebben a megoldásban támogatott összekapcsolt forrásokról.

| Összekapcsolt forrás | Támogatás | Description |
|---|---|---|
| [Windows-ügynökök](../agents/agent-windows.md) | Yes | A megoldás a Windows-ügynököktől származó kapacitás-és teljesítményadatok adatait gyűjti. |
| [Linux-ügynökök](../vm/quick-collect-linux-computer.md) | No    | A megoldás nem gyűjt kapacitás-és teljesítményadatok-adatokat a közvetlen Linux-ügynököktől.|
| [SCOM felügyeleti csoport](../agents/om-agents.md) | Yes |A megoldás gyűjti a kapacitás-és teljesítményadatokat a csatlakoztatott SCOM felügyeleti csoportban lévő ügynököktől. Nem szükséges közvetlen kapcsolódás a SCOM-ügynöktől a Log Analyticshoz.|
| [Azure Storage-fiók](../essentials/resource-logs.md#send-to-log-analytics-workspace) | No | Az Azure Storage nem tartalmaz kapacitás-és teljesítményadatokat.|

## <a name="prerequisites"></a>Előfeltételek

- A Windows-vagy Operations Manager-ügynököket a Windows Server 2012-es vagy újabb Hyper-V-gazdagépekre kell telepíteni, nem virtuális gépekre.


## <a name="configuration"></a>Konfiguráció

A Capacity and Performance megoldás a munkaterülethez való hozzáadásához hajtsa végre a következő lépést.

- Adja hozzá a Capacity and Performance megoldást a Log Analytics munkaterülethez az [Solutions Gallery log Analytics-megoldások hozzáadása](./solutions.md)című témakörben leírt eljárással.

## <a name="management-packs"></a>Felügyeleti csomagok

Ha a SCOM-felügyeleti csoport csatlakozik a Log Analytics munkaterülethez, a megoldás hozzáadásakor a következő felügyeleti csomagok lesznek telepítve a SCOM-ben. Ezek a felügyeleti csomagok nem igényelnek további konfigurációs vagy karbantartási feladatokat.

- Microsoft. IntelligencePacks. CapacityPerformance

Az 1201-es esemény a következőhöz hasonló:


```
New Management Pack with id:"Microsoft.IntelligencePacks.CapacityPerformance", version:"1.10.3190.0" received.
```

A Capacity and Performance-megoldás frissítésekor a verziószám módosul.

A megoldási felügyeleti csomagok frissítéseivel kapcsolatban lásd: [Az Operations Manager csatlakoztatása a Log Analyticshez](../agents/om-agents.md).

## <a name="using-the-solution"></a>A megoldás használata

Amikor hozzáadja a Capacity and Performance megoldást a munkaterülethez, a Capacity and Performance hozzá lesz adva az áttekintő irányítópulthoz. Ez a csempe a jelenleg aktív Hyper-V-gazdagépek számát és a kiválasztott időszakban figyelt aktív virtuális gépek számát jeleníti meg.

![Capacity and Performance csempe](./media/capacity-performance/capacity-tile.png)


### <a name="review-utilization"></a>Kihasználtság áttekintése

A Capacity and Performance csempére kattintva nyissa meg a Capacity and Performance irányítópultot. Az irányítópulton az alábbi táblázatban felsorolt oszlopok találhatóak. Mindegyik oszlopban legfeljebb tíz olyan elem jelenik meg, amely megfelel a megadott hatóköri és időtartományi kritériumoknak. Az oszlop alján található **Az összes megtekintése** elemre vagy az oszlop fejlécére kattintva az összes rekordot megjelenítő keresést végezhet a naplóban.

- **Hosts**
    - **GAZDAGÉP CPU-kihasználtsága** Megjeleníti a gazdagépek CPU-kihasználtságának grafikus trendjét, valamint a gazdagépek listáját a kiválasztott időszak alapján. Vigye az egérmutatót a diagram fölé egy adott időpontra vonatkozó részletek megtekintéséhez. Kattintson a diagramra a naplóbeli keresés további részleteinek megtekintéséhez. Kattintson bármelyik állomásnévre a naplók keresésének megnyitásához, és tekintse meg az üzemeltetett virtuális gépek CPU-számlálójának adatait.
    - **Gazdagép memóriájának kihasználtsága** A gazdagépek memóriájának kihasználtságát és a gazdagépek listáját jeleníti meg a kiválasztott időszak alapján. Vigye az egérmutatót a diagram fölé egy adott időpontra vonatkozó részletek megtekintéséhez. Kattintson a diagramra a naplóbeli keresés további részleteinek megtekintéséhez. Kattintson bármelyik állomásnévre a naplóbeli keresés megnyitásához és a memória számlálójának megtekintéséhez az üzemeltetett virtuális gépekhez.
- **Virtual Machines**
    - **Virtuális gép CPU-kihasználtsága** Megjeleníti a virtuális gépek CPU-kihasználtságának grafikus trendjét, valamint a virtuális gépek listáját a kiválasztott időszak alapján. Vigye a kurzort a diagram fölé egy adott időpontra vonatkozó részletek megtekintéséhez az első 3 virtuális gépen. Kattintson a diagramra a naplóbeli keresés további részleteinek megtekintéséhez. Kattintson a virtuális gép nevére a naplóbeli keresés megnyitásához, és tekintse meg a virtuális gép összesített CPU-számlálójának részleteit.
    - **Virtuális gépek memóriájának kihasználtsága** Megjeleníti a virtuális gépek memóriahasználat és a virtuális gépek listájának grafikus trendjét a kiválasztott időszak alapján. Vigye a kurzort a diagram fölé egy adott időpontra vonatkozó részletek megtekintéséhez az első 3 virtuális gépen. Kattintson a diagramra a naplóbeli keresés további részleteinek megtekintéséhez. Kattintson a virtuális gép nevére a naplóbeli keresés megnyitásához, és tekintse meg a virtuális gép összesített memória-számlálójának részleteit.
    - **Virtuális gép – teljes lemez IOPS** Megjeleníti a virtuális gépek teljes IOPS grafikus trendjeit, valamint a virtuális gépek listáját, amelyekhez a IOPS a kiválasztott időszak alapján. Vigye a kurzort a diagram fölé egy adott időpontra vonatkozó részletek megtekintéséhez az első 3 virtuális gépen. Kattintson a diagramra a naplóbeli keresés további részleteinek megtekintéséhez. A virtuális gép nevére kattintva megnyithatja a naplók keresését, és megtekintheti az összesített lemez IOPS a virtuális gép számlálójának adatait.
    - **Virtuális gép teljes lemezének átviteli sebessége** Megjeleníti a virtuális gépek teljes lemezének átviteli sebességét, valamint a kiválasztott időszak alapján a virtuális gépek teljes lemezes átviteli sebességét tartalmazó listát. Vigye a kurzort a diagram fölé egy adott időpontra vonatkozó részletek megtekintéséhez az első 3 virtuális gépen. Kattintson a diagramra a naplóbeli keresés további részleteinek megtekintéséhez. Kattintson a virtuális gép nevére a naplóbeli keresés megnyitásához, és tekintse meg a virtuális gép összesített teljes lemezének átviteli sebessége számláló részleteit.
- **Fürtözött megosztott kötetek**
    - **Teljes átviteli sebesség** A fürtözött megosztott kötetek olvasási és írási összegét jeleníti meg.
    - **Összes IOPS** A fürtözött megosztott köteteken másodpercenkénti bemeneti/kimeneti műveletek összegét jeleníti meg.
    - **Teljes késés** Megjeleníti a fürtözött megosztott kötetek teljes késését.
- **Gazdagép sűrűsége** A felső csempe a megoldás számára elérhető gazdagépek és virtuális gépek teljes számát jeleníti meg. A naplóbeli keresés további részleteinek megtekintéséhez kattintson a felső csempére. A az összes gazdagépet és a futtatott virtuális gépek számát is felsorolja. Kattintson egy gazdagépre a virtuális gép részletezéséhez a naplóbeli keresés során.


![irányítópult-gazdagépek panel](./media/capacity-performance/dashboard-hosts.png)

![irányítópult virtuális gépek panelje](./media/capacity-performance/dashboard-vms.png)


### <a name="evaluate-performance"></a>Teljesítmény kiértékelése

Az éles számítástechnikai környezetek nagy mértékben különböznek az egyik szervezettől a másikig. A kapacitás-és teljesítménybeli munkaterhelések a virtuális gépek működésének módjától, illetve a megszokott szempontoktól függően változhatnak. A teljesítmény mérésére szolgáló konkrét eljárások valószínűleg nem lesznek érvényesek a környezetére. Így az általánosabb előírási útmutató jobban alkalmazkodik a segítséghez. A Microsoft számos, a teljesítmény mérését segítő útmutatást tesz közzé.

Az összegzéshez a megoldás a különböző forrásokból származó kapacitás-és teljesítményadatokat gyűjti, beleértve a teljesítményszámlálók adatait is. Használja ezt a kapacitást és teljesítményadatokat, amelyet a megoldás különböző felületeinek mutatnak be, és hasonlítsa össze az eredményeket a [Hyper-V cikk mérési teljesítményének](https://www.microsoft.com/en-us/download/details.aspx?id=56495) megfelelő értékekkel. Bár a cikket néhány évvel ezelőtt közzétették, a mérőszámok, megfontolások és irányelvek még érvényesek. A cikk más hasznos erőforrásokra mutató hivatkozásokat tartalmaz.


## <a name="sample-log-searches"></a>Naplókeresési minták

Az alábbi táblázat a megoldás által gyűjtött és kiszámított kapacitás-és teljesítményadatok-kereséseket tartalmazza.


| Lekérdezés | Description |
|:--- |:--- |
| Minden gazdagép memória-konfigurációja | A Perf &#124;, ahol a ObjectName = = "Capacity and Performance" és a CounterName = = "gazdagéphez hozzárendelt memória MB" &#124; összefoglaló MB = AVG (kártyabirtokos számlájának megterhelését) by példánynév |
| Minden virtuális gép memória-konfigurációja | A Teljesítményfigyelő &#124;, ahol a ObjectName = = "Capacity and Performance" és a CounterName = = "virtuális gép hozzárendelt memóriája MB" &#124; a MB = AVG (kártyabirtokos számlájának megterhelését) by példánynév összegzése |
| A teljes lemez IOPS részletezése az összes virtuális gépen | A Perf &#124;, ahol a ObjectName = = "Capacity and Performance" és a (CounterName = = "VHD olvasás/s" vagy CounterName = = "VHD írások/s") &#124; összefoglaló AggregatedValue = AVG (kártyabirtokos számlájának megterhelését) by bin (TimeGenerated, 1h), CounterName, példánynév |
| A lemez teljes átviteli sebességének részletezése minden virtuális gépen | A Perf &#124;, ahol a ObjectName = = "Capacity and Performance" és a (CounterName = = "VHD Read MB/s" vagy CounterName = = "VHD Write MB/s") &#124; összefoglaló AggregatedValue = AVG (kártyabirtokos számlájának megterhelését) by bin (TimeGenerated, 1h), CounterName, példánynév |
| Az összes IOPS részletezése az összes CSV között | A Perf &#124;, ahol a ObjectName = = "Capacity and Performance" és a (CounterName = = "CSV olvasás/s" vagy CounterName = = "CSV írás/s") &#124; összefoglaló AggregatedValue = AVG (kártyabirtokos számlájának megterhelését) by bin (TimeGenerated, 1h), CounterName, példánynév |
| A teljes átviteli sebesség részletezése minden CSV | A Perf &#124;, ahol a ObjectName = = "Capacity and Performance" és a (CounterName = = "CSV olvasás/s" vagy CounterName = = "CSV írás/s") &#124; összefoglaló AggregatedValue = AVG (kártyabirtokos számlájának megterhelését) by bin (TimeGenerated, 1h), CounterName, példánynév |
| Az összes CSV közötti teljes késés részletezése | A Teljesítményfigyelő &#124;, ahol a ObjectName = = "Capacity and Performance" és (CounterName = = "CSV olvasási késés" vagy CounterName = = "CSV írási késése") &#124; foglalja össze a AggregatedValue = AVG (kártyabirtokos számlájának megterhelését) by bin (TimeGenerated, 1h), CounterName, példánynév |


## <a name="next-steps"></a>Következő lépések
* A részletes Capacity and Performance-információk megtekintéséhez használja [a log Analytics a naplóbeli kereséseket](../logs/log-query-overview.md) .

