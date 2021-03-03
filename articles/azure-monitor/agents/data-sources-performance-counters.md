---
title: Windows-és Linux-teljesítményű adatforrások összegyűjtése Log Analytics ügynökkel Azure Monitor
description: A teljesítményszámlálókat a Azure Monitor gyűjti a Windows-és Linux-ügynökök teljesítményének elemzéséhez.  Ez a cikk bemutatja, hogyan konfigurálhatja a teljesítményszámlálók gyűjteményét Windows-és Linux-ügynökökhöz, a munkaterületen tárolt adatokat, valamint azt, hogyan elemezheti őket a Azure Portalban.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/21/2020
ms.openlocfilehash: c06123b33c7f467e12742cf6180d821e647b5115
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101711552"
---
# <a name="collect-windows-and-linux-performance-data-sources-with-log-analytics-agent"></a>Windows-és Linux-teljesítményű adatforrások gyűjtése Log Analytics-ügynökkel
A Windows és Linux rendszerű teljesítményszámlálók betekintést nyújtanak a hardver-összetevők, operációs rendszerek és alkalmazások teljesítményére.  A Azure Monitor a teljesítmény-és a hosszú távú elemzéshez és jelentéskészítéshez kapcsolódóan rendszeres időközönként gyűjthetik be a Log Analytics ügynököktől származó teljesítményszámlálókat a közel valós idejű (vizsgálja) elemzéshez.

> [!IMPORTANT]
> Ez a cikk a teljesítményadatok összegyűjtését ismerteti [log Analytics ügynökkel](./log-analytics-agent.md) , amely a Azure monitor által használt ügynökök egyike. Más ügynökök különböző adatokat gyűjtenek, és eltérően vannak konfigurálva. A rendelkezésre álló ügynökök és az összegyűjtött adatok listáját lásd: [Azure monitor ügynökök áttekintése](../agents/agents-overview.md) .

![Teljesítményszámlálók](media/data-sources-performance-counters/overview.png)

## <a name="configuring-performance-counters"></a>Teljesítményszámlálók konfigurálása
A teljesítményszámlálók konfigurálása a Log Analytics munkaterület [speciális beállításaiban található adatok menüből](../agents/agent-data-sources.md#configuring-data-sources) .

Amikor először konfigurálja a Windows-vagy Linux-teljesítményszámlálókat egy új munkaterülethez, lehetősége van számos gyakori számláló gyors létrehozására.  Ezek mindegyike mellett egy jelölőnégyzet található.  Győződjön meg arról, hogy a kezdetben létrehozni kívánt számlálók be vannak jelölve, majd kattintson **a kijelölt teljesítményszámlálók hozzáadása** lehetőségre.

A Windows-teljesítményszámlálók esetében kiválaszthatja az egyes teljesítményszámlálók egy adott példányát. A Linux-teljesítményszámlálók esetében az egyes kiválasztott számlálók a szülő számláló összes alárendelt számlálóján érvényesek. A következő táblázat a Linux és a Windows teljesítményszámlálói számára elérhető általános példányokat mutatja be.

| Példány neve | Leírás |
| --- | --- |
| \_Összesen |Összes példány összesen |
| \* |Minden példány |
| (/&#124;/var) |A (z):/vagy a/var nevű példányokra illeszkedik |

### <a name="windows-performance-counters"></a>Windows-teljesítményszámlálók

![Windows-teljesítményszámlálók konfigurálása](media/data-sources-performance-counters/configure-windows.png)

Kövesse ezt az eljárást egy új Windows-teljesítményszámláló hozzáadásához a gyűjtéshez. Ne feledje, hogy a v2 Windows-teljesítményszámlálók nem támogatottak.

1. Írja be a számláló nevét a Format *objektum (példány) \ számláló* szövegmezőbe.  A gépelés megkezdése után a rendszer a gyakori számlálók megfelelő listáját mutatja be.  Kiválaszthat egy számlálót a listából, vagy megadhatja a kívánt értéket.  A *object\counter* megadásával egy adott számlálóhoz tartozó összes példányt is visszaadhat.  

    SQL Server teljesítményszámlálók elnevezett példányokból való gyűjtésekor az összes elnevezett példány számlálója az *MSSQL $* értékkel kezdődik, amelyet a példány neve követ.  Ha például a log cache találati arány számlálóját szeretné összegyűjteni az SQL-példány INST2 tartozó adatbázis-teljesítmény objektum összes adatbázisához, akkor a következőt kell megadnia: `MSSQL$INST2:Databases(*)\Log Cache Hit Ratio` .

2. Kattintson **+** vagy nyomja le az **ENTER** billentyűt a számláló a listához való hozzáadásához.
3. Számláló hozzáadásakor a rendszer az alapértelmezett 10 másodpercet használja a **mintavételi intervallumhoz**.  Ez a érték legfeljebb 1800 másodperc (30 perc) lehet, ha csökkenteni szeretné az összegyűjtött teljesítményadatok tárolási követelményeit.
4. Ha elkészült a számlálók hozzáadásával, kattintson a képernyő felső részén található **Mentés** gombra a konfiguráció mentéséhez.

### <a name="linux-performance-counters"></a>Linux-teljesítményszámlálók

![Linux-teljesítményszámlálók konfigurálása](media/data-sources-performance-counters/configure-linux-1.png)

Kövesse ezt az eljárást egy új Linux-teljesítményszámláló hozzáadásához a gyűjtéshez.

1. Írja be a számláló nevét a Format *objektum (példány) \ számláló* szövegmezőbe.  A gépelés megkezdése után a rendszer a gyakori számlálók megfelelő listáját mutatja be.  Kiválaszthat egy számlálót a listából, vagy megadhatja a kívánt értéket.  
1. Kattintson **+** vagy nyomja le az **ENTER** billentyűt a számláló az objektumhoz tartozó egyéb számlálók listájához való hozzáadásához.
1. Egy objektum összes számlálója ugyanazt a **mintavételi időközt** használja.  Az alapértelmezett érték 10 másodperc.  Ezt magasabb értékre kell módosítani, amely legfeljebb 1800 másodperc (30 perc) lehet, ha csökkenteni szeretné az összegyűjtött teljesítményadatok tárolási követelményeit.
1. Ha elkészült a számlálók hozzáadásával, kattintson a képernyő felső részén található **Mentés** gombra a konfiguráció mentéséhez.

#### <a name="configure-linux-performance-counters-in-configuration-file"></a>Linux-teljesítményszámlálók konfigurálása a konfigurációs fájlban
A Linux-teljesítményszámlálók a Azure Portal használatával történő konfigurálása helyett lehetősége van a konfigurációs fájlok szerkesztésére a Linux-ügynökön.  A gyűjteni kívánt teljesítmény-mérőszámokat a **/etc/opt/Microsoft/omsagent/ \<workspace id\> /conf/omsagent.conf** konfigurációja vezérli.

A gyűjteni kívánt teljesítmény-mérőszámok minden objektumát vagy kategóriáját egyetlen elemként kell definiálni a konfigurációs fájlban `<source>` . A szintaxis az alábbi mintát követi.

```xml
<source>
    type oms_omi  
    object_name "Processor"
    instance_regex ".*"
    counter_name_regex ".*"
    interval 30s
</source>
```


Az ebben az elemben található paramétereket a következő táblázat ismerteti.

| Paraméterek | Leírás |
|:--|:--|
| objektum \_ neve | A gyűjtemény objektumának neve. |
| példányok \_ regexje |  Egy *reguláris kifejezés* , amely meghatározza, hogy mely példányokat kell gyűjteni. Az érték: az `.*` összes példányt megadja. Ha csak az összes példány processzor-metrikáit szeretné összegyűjteni \_ , megadhatja a következőt: `_Total` . Ha csak a crond vagy sshd példányok feldolgozási metrikáit szeretné összegyűjteni, megadhatja a következőt: `(crond\|sshd)` . |
| számláló \_ neve \_ regex | Egy *reguláris kifejezés* , amely meghatározza, hogy mely számlálókat (az objektumhoz) kell összegyűjteni. Az objektum összes számlálójának összegyűjtéséhez a következőt kell megadnia: `.*` . Ha csak a memória-objektum lapozófájl-számlálóit szeretné összegyűjteni, például megadhatja a következőket: `.+Swap.+` |
| interval | Az objektum számlálóinak gyűjtésének gyakorisága. |


A következő táblázat felsorolja a konfigurációs fájlban megadható objektumokat és számlálókat.  Bizonyos alkalmazásokhoz további számlálók érhetők el, amelyek a következő témakörben olvashatók: a [teljesítményszámlálók összegyűjtése Linux-alkalmazásokhoz a Azure monitorban](data-sources-linux-applications.md).

| Objektum neve | Számláló neve |
|:--|:--|
| Logikai lemez | Szabad inode%-ban |
| Logikai lemez | Szabad terület (%) |
| Logikai lemez | Felhasznált inode%-ban |
| Logikai lemez | Felhasznált terület (%) |
| Logikai lemez | Lemez olvasási sebessége (bájt/s) |
| Logikai lemez | Olvasási sebesség (lemez/mp) |
| Logikai lemez | Lemez átvitele másodpercenként |
| Logikai lemez | Lemez írási sebessége (bájt/s) |
| Logikai lemez | Írási sebesség (írás/mp) |
| Logikai lemez | Szabad terület (MB) |
| Logikai lemez | Logikai lemez sebessége (bájt/s) |
| Memória | Rendelkezésre álló memória%-ban |
| Memória | Rendelkezésre álló swap-terület (%) |
| Memória | Felhasznált memória (%) |
| Memória | Felhasznált swap-terület%-ban |
| Memória | Rendelkezésre álló memória (MB) |
| Memória | Rendelkezésre álló memória (MB) |
| Memória | Olvasott lap/mp |
| Memória | Írási idő/mp |
| Memória | Lap/mp |
| Memória | Felhasznált memória (MB) – lapozófájl |
| Memória | Felhasznált memória (MB) |
| Network (Hálózat) | Továbbított bájtok összesen |
| Network (Hálózat) | Fogadott bájtok összesen |
| Network (Hálózat) | Bájtok összesen |
| Network (Hálózat) | Továbbított csomagok összesen |
| Network (Hálózat) | Fogadott csomagok összesen |
| Network (Hálózat) | Rx-hibák összesen |
| Network (Hálózat) | TX-hibák összesen |
| Network (Hálózat) | Ütközések összesen |
| Fizikai lemez | Átlagos írási idő (mp/olvasás) |
| Fizikai lemez | Átlagos műveleti idő (mp/átvitel) |
| Fizikai lemez | Átlagos írási idő (mp/írás) |
| Fizikai lemez | Fizikai lemez sebessége (bájt/s) |
| Folyamat | PCT rendszerjogosultságú idő |
| Folyamat | PCT felhasználói idő |
| Folyamat | Felhasznált memória (kilobájt) |
| Folyamat | Virtuális megosztott memória |
| Processzor | % DPC idő |
| Processzor | Üresjáratban eltöltött időhányad (%) |
| Processzor | Megszakítási idő%-ban |
| Processzor | I/o várakozási idő%-ban |
| Processzor | % Nice idő |
| Processzor | %-Os privilegizált idő |
| Processzor | A processzor kihasználtsága (%) |
| Processzor | Felhasználói idő%-ban |
| Rendszer | Szabad fizikai memória |
| Rendszer | Szabad terület a Lapozófájlokban |
| Rendszer | Szabad virtuális memória |
| Rendszer | Folyamatok |
| Rendszer | Lapozófájlokban tárolt méret |
| Rendszer | Üzemidő |
| Rendszer | Felhasználók |


A teljesítmény-metrikák alapértelmezett konfigurációja a következő:

```xml
<source>
    type oms_omi
    object_name "Physical Disk"
    instance_regex ".*"
    counter_name_regex ".*"
    interval 5m
</source>

<source>
    type oms_omi
    object_name "Logical Disk"
    instance_regex ".*"
    counter_name_regex ".*"
    interval 5m
</source>

<source>
    type oms_omi
    object_name "Processor"
    instance_regex ".*"
    counter_name_regex ".*"
    interval 30s
</source>

<source>
    type oms_omi
    object_name "Memory"
    instance_regex ".*"
    counter_name_regex ".*"
    interval 30s
</source>
```

## <a name="data-collection"></a>Adatgyűjtés
Azure Monitor a megadott mintavételi intervallumban gyűjti az összes megadott teljesítményszámlálókat az összes olyan ügynökön, amelyen telepítve van a számláló.  Az adatokat nem összesíti a rendszer, és a nyers adatokat a log Analytics-munkaterület által megadott időtartamra vonatkozó összes naplózási lekérdezési nézetben elérhetővé kell tennie.

## <a name="performance-record-properties"></a>Teljesítmény rekord tulajdonságai
A teljesítményadatokat a teljesítmény **típusa és a** következő táblázatban szereplő tulajdonságok szerepelnek.

| Tulajdonság | Leírás |
|:--- |:--- |
| Computer |Az a számítógép, amelyre az eseményt gyűjtötték. |
| CounterName |A teljesítményszámláló neve |
| CounterPath |A számláló teljes elérési útja az űrlap- \\ \\ \<Computer> \\ objektum (példány) \\ számlálójában. |
| Kártyabirtokos számlájának megterhelését |A számláló numerikus értéke. |
| InstanceName |Az esemény példányának neve.  Üres, ha nincs példány. |
| ObjectName |A teljesítményobjektum neve |
| SourceSystem |Az ügynök típusa, amelyből az adatokat gyűjtötték. <br><br>OpsManager – Windows-ügynök, közvetlen kapcsolat vagy SCOM <br> Linux – minden Linux-ügynök  <br> AzureStorage – Azure Diagnostics |
| TimeGenerated |Az adatok mintavételezésének dátuma és időpontja. |

## <a name="sizing-estimates"></a>Méretezési becslések
 Egy adott számláló 10 másodperces időközönként történő gyűjtésének durva becslése napi 1 MB-onként történik.  Megbecsülheti egy adott számláló tárolási követelményeit a következő képlettel.

> 1 MB x (számlálók száma) x (ügynökök száma) x (példányok száma)

## <a name="log-queries-with-performance-records"></a>Lekérdezések naplózása a teljesítménnyel kapcsolatos rekordokkal
Az alábbi táblázat különböző példákat tartalmaz a teljesítményadatokat lekérő lekérdezések naplózására.

| Lekérdezés | Leírás |
|:--- |:--- |
| Teljesítmény |Minden teljesítményadatok |
| Teljesítményfigyelő &#124;, ahol a számítógép = = "Sajátgép" |Egy adott számítógépről származó összes teljesítményadatok |
| A Teljesítményfigyelő &#124;, ahol a CounterName = = "aktuális lemez várólistájának hossza" |Egy adott számláló összes teljesítményadatokat |
| A Teljesítményfigyelő &#124;, ahol a ObjectName = = "processzor" és a CounterName = = "% Processor Time" és a példánynév = = "_Total" &#124; összefoglalja a AVGCPU = AVG (kártyabirtokos számlájának megterhelését) et a számítógépen |A CPU átlagos kihasználtsága az összes számítógépen |
| A Teljesítményfigyelő &#124;, ahol a CounterName = = "% Processor Time" &#124; összefoglalja a AggregatedValue = Max (kártyabirtokos számlájának megterhelését) t a számítógépen |Maximális CPU-kihasználtság az összes számítógépen |
| A Teljesítményfigyelő &#124;, ahol a ObjectName = = "LogicalDisk" és a CounterName = = "aktuális lemez várólistájának hossza" és a Computer = = "MyComputerName" &#124; összefoglaló AggregatedValue = AVG (kártyabirtokos számlájának megterhelését) by példánynév |Az aktuális Lemezvezérlő-várólista átlagos hossza egy adott számítógép összes példánya között |
| A Perf &#124;, ahol a CounterName = = "Disk Transfers/mp" &#124; összegzi a AggregatedValue = percentilis (kártyabirtokos számlájának megterhelését, 95) elemet a számítógépen |a 95. százalékos aránya/mp az összes számítógép között |
| A Teljesítményfigyelő &#124;, ahol a CounterName = = "% Processor Time" és a példánynév = = "_Total" &#124; foglalja össze a AggregatedValue = AVG (kártyabirtokos számlájának megterhelését) by bin (TimeGenerated, 1h), számítógép |A CPU-használat óránkénti átlaga az összes számítógépen |
| A Teljesítményfigyelő &#124;, ahol a Computer = = "Sajátgép" és a CounterName startswith_cs "%" és a példánynév = = "_Total" &#124; összefoglalja a AggregatedValue = percentilis (kártyabirtokos számlájának megterhelését, 70) by bin (TimeGenerated, 1h), CounterName | Egy adott számítógép% százalékos számlálójának óránként 70 százalékos aránya |
| A Teljesítményfigyelő &#124;, ahol a CounterName = = "% Processor Time" és a példánynév = = "_Total" és a Computer = = "Sajátgép" &#124; összegzése ["min (kártyabirtokos számlájának megterhelését)"] = min (kártyabirtokos számlájának megterhelését), ["AVG (kártyabirtokos számlájának megterhelését)"] = AVG (kártyabirtokos számlájának megterhelését), ["percentile75 (kártyabirtokos számlájának megterhelését)"] = percentilis (kártyabirtokos számlájának megterhelését, 75), ["Max (kártyabirtokos számlájának megterhelését)"] = Max (kártyabirtokos számlájának megterhelését) by bin (TimeGenerated, 1h), számítógép |Egy adott számítógép óránkénti átlaga, minimális, maximális és 75 – percentilis CPU-használata |
| Teljesítményfigyelő &#124;, ahol ObjectName = = "MSSQL $ INST2: adatbázisok" és példánynév = = "Master" | Az adatbázis teljesítményobjektum a Master adatbázishoz tartozó összes teljesítményadatokat a megnevezett SQL Server példány INST2.  




## <a name="next-steps"></a>Következő lépések
* [Teljesítményszámlálók gyűjtése Linux-alkalmazásokból](data-sources-linux-applications.md) , beleértve a MySQL-t és az Apache HTTP-kiszolgálót.
* További információ az adatforrásokból és megoldásokból gyűjtött adatok elemzéséhez szükséges [naplók lekérdezéséről](../logs/log-query-overview.md) .  
* Az összegyűjtött adatok [Power BIba](../visualize/powerbi.md) való exportálása további vizualizációk és elemzések céljából.