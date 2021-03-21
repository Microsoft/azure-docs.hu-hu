---
title: Naplók optimalizálása Azure Monitorban
description: Ajánlott eljárások a Azure Monitor naplózási lekérdezésének optimalizálásához.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/30/2019
ms.openlocfilehash: 7b5412b2ca738f5d2099521062e37afcff90e938
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102047332"
---
# <a name="optimize-log-queries-in-azure-monitor"></a>Naplók optimalizálása Azure Monitorban
Azure Monitor naplók az [Azure adatkezelő (ADX)](/azure/data-explorer/) használatával tárolják a naplófájlokat, és lekérdezéseket futtatnak az adatok elemzéséhez. Létrehozza, kezeli és karbantartja a ADX-fürtöket, és optimalizálja azokat a log Analysis számítási feladatokhoz. Amikor lekérdezést futtat, az optimalizált, és a munkaterület-adatok tárolására szolgáló megfelelő ADX-fürtre irányítja. A Azure Monitor-naplók és az Azure Adatkezelő számos automatikus lekérdezés-optimalizálási mechanizmust használ. Míg az automatikus optimalizálás jelentős lökést nyújt, bizonyos esetekben jelentősen növelheti a lekérdezési teljesítményt. Ez a cikk ismerteti a teljesítménnyel kapcsolatos szempontokat és számos technikát a kijavításához.

A módszerek többsége gyakori az Azure-Adatkezelő és Azure Monitor naplókon futtatott lekérdezések esetében, bár számos egyedi Azure Monitor naplózási szempontot ismertetünk. További Azure Adatkezelő optimalizálási tippek: [lekérdezés – ajánlott eljárások](/azure/kusto/query/best-practices).

Az optimalizált lekérdezések a következőket teszik:

- A lekérdezés-végrehajtás teljes időtartamának csökkentése a gyorsabb futtatással.
- Kisebb eséllyel szabályozható vagy elutasított.

Ügyeljen arra, hogy az ismétlődő és a burst használatra használt lekérdezések, például az irányítópultok, a riasztások, a Logic Apps és a Power BI. A nem hatékony lekérdezések hatása ezekben az esetekben jelentős.

## <a name="query-performance-pane"></a>Lekérdezési teljesítmény ablaktábla
Miután lefuttatott egy lekérdezést a Log Analyticsban, a lekérdezési eredmények fölötti lefelé mutató nyílra kattintva megtekintheti a lekérdezési teljesítmény panelt, amely a lekérdezés számos teljesítménymutatójának eredményét jeleníti meg. Ezeket a teljesítménymutatókat a következő szakaszban ismertetjük.

![Lekérdezési teljesítmény ablaktábla](media/query-optimization/query-performance-pane.png)


## <a name="query-performance-indicators"></a>Lekérdezési teljesítménymutatók

A következő lekérdezési teljesítménymutatók érhetők el minden végrehajtott lekérdezéshez:

- [Összes CPU](#total-cpu): teljes számítási művelet, amely a lekérdezés feldolgozására szolgál az összes számítási csomóponton. Ez a módszer a számítástechnikai, elemzési és beolvasási időt jelöli. 

- [Feldolgozott lekérdezéshez használt adatmennyiség](#data-used-for-processed-query): a lekérdezés feldolgozásához elért összesített adatmennyiség. A cél tábla mérete, a használt időtartomány, az alkalmazott szűrők és a hivatkozott oszlopok száma befolyásolja.

- A [feldolgozott lekérdezés időbeli](#time-span-of-the-processed-query)időtartama: a lekérdezés feldolgozásához használt legújabb és legrégebbi adatmennyiség közötti különbség. A lekérdezéshez megadott explicit időtartomány befolyásolja.

- A [feldolgozott adatmennyiség kora](#age-of-processed-data): a jelenlegi és a legrégebbi, a lekérdezés feldolgozásához elért adatmennyiség. Ez nagy mértékben befolyásolja az beolvasás hatékonyságát.

- [Munkaterületek száma](#number-of-workspaces): az implicit vagy explicit kijelölés miatt a lekérdezés feldolgozásakor hány munkaterület érhető el.

- [Régiók száma](#number-of-regions): a lekérdezés feldolgozásakor hány régió fér hozzá a munkaterületek implicit vagy explicit kiválasztása miatt. A többrégiós lekérdezések sokkal kevésbé hatékonyak, a teljesítménymutatók pedig részleges lefedettséget jelentenek.

- [Párhuzamosság](#parallelism): azt jelzi, hogy a rendszer milyen mértékben tudta végrehajtani ezt a lekérdezést több csomóponton. Csak a nagy CPU-fogyasztással rendelkező lekérdezésekre vonatkozik. Az adott függvények és operátorok használatának befolyásolására.


## <a name="total-cpu"></a>Teljes CPU
A tényleges számítási CPU, amely a lekérdezés feldolgozására lett befektetve az összes lekérdezés-feldolgozó csomóponton. Mivel a legtöbb lekérdezés nagy számú csomóponton fut, ez általában sokkal nagyobb, mint a lekérdezés tényleges végrehajtásának időtartama. 

A több mint 100 másodperces CPU-t használó lekérdezés olyan lekérdezésnek minősül, amely túlzott erőforrásokat használ fel. A több mint 1 000 másodperces CPU-t használó lekérdezések visszaélésszerű lekérdezésnek minősülnek, és lehetséges, hogy szabályozva vannak.

A lekérdezés feldolgozási idejének elköltése:
- Adatok beolvasása – a régi adatok lekérése több időt vesz igénybe, mint a legutóbbi adatok beolvasása.
- Adatfeldolgozás – az adatgyűjtés logikája és kiértékelése. 

A lekérdezés-feldolgozási csomópontokon töltött idő kivételével további időt Azure Monitor naplók is töltenek: hitelesítse a felhasználót, és ellenőrizze, hogy engedélyezett-e az ilyen adathozzáférés, az adattár megkeresése, a lekérdezés elemzése és a lekérdezés-feldolgozási csomópontok lefoglalása. Ez az idő nem szerepel a lekérdezés teljes CPU-ideje alatt.

### <a name="early-filtering-of-records-prior-of-using-high-cpu-functions"></a>A rekordok korai szűrése a nagy CPU-függvények használata előtt

A lekérdezési parancsok és függvények némelyike nehéz a CPU-használatban. Ez különösen igaz azokra a parancsokra, amelyek a JSON-t és az XML-t elemezik, vagy az összetett reguláris kifejezéseket Az ilyen elemzések [parse_json ()](/azure/kusto/query/parsejsonfunction) vagy [parse_xml ()](/azure/kusto/query/parse-xmlfunction) függvények vagy implicit módon történhetnek, amikor dinamikus oszlopokra hivatkoznak.

Ezek a függvények a feldolgozott sorok számának arányában használják a PROCESSZORt. A leghatékonyabb optimalizálás a lekérdezés korai szakaszában való hozzáadás, amely a CPU-igényes funkció végrehajtása előtt a lehető legtöbb rekordot képes kiszűrni.

Például a következő lekérdezések pontosan ugyanazt az eredményt eredményezik, de a második a leghatékonyabb, mint a [Where](/azure/kusto/query/whereoperator) feltétel, mielőtt az elemzés során kizárja a sok rekordot:

```Kusto
//less efficient
SecurityEvent
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| where FileHash != "" and FilePath !startswith "%SYSTEM32"  // Problem: irrelevant results are filtered after all processing and parsing is done
| summarize count() by FileHash, FilePath
```
```Kusto
//more efficient
SecurityEvent
| where EventID == 8002 //Only this event have FileHash
| where EventData !has "%SYSTEM32" //Early removal of unwanted records
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| where FileHash != "" and FilePath !startswith "%SYSTEM32"  // exact removal of results. Early filter is not accurate enough
| summarize count() by FileHash, FilePath
| where FileHash != "" // No need to filter out %SYSTEM32 here as it was removed before
```

### <a name="avoid-using-evaluated-where-clauses"></a>Kerülje a kiértékelt where záradékok használatát

Azok a lekérdezések, amelyek [Where](/azure/kusto/query/whereoperator) záradékot tartalmaznak egy kiértékelt oszlopon, és nem az adathalmazban fizikailag szereplő oszlopok, elvesztik a hatékonyságot. A kiértékelt oszlopok szűrése megakadályozza a rendszeroptimalizálást, ha nagy adathalmazok vannak kezelve.
Például a következő lekérdezések pontosan ugyanazt az eredményt eredményezik, a második pedig hatékonyabb, mivel a [Where](/azure/kusto/query/whereoperator) feltétel a beépített oszlopra hivatkozik.

```Kusto
//less efficient
Syslog
| extend Msg = strcat("Syslog: ",SyslogMessage)
| where  Msg  has "Error"
| count 
```
```Kusto
//more efficient
Syslog
| where  SyslogMessage  has "Error"
| count 
```

Bizonyos esetekben a kiértékelt oszlopot a lekérdezés-feldolgozó motor implicit módon hozza létre, mivel a szűrés nem csupán a következő mezőn történik:
```Kusto
//less efficient
SecurityEvent
| where tolower(Process) == "conhost.exe"
| count 
```
```Kusto
//more efficient
SecurityEvent
| where Process =~ "conhost.exe"
| count 
```




### <a name="use-effective-aggregation-commands-and-dimensions-in-summarize-and-join"></a>Hatékony aggregációs parancsok és méretek használata az összegzésben és a csatlakozáshoz

Míg egyes aggregációs parancsok, például a [Max ()](/azure/kusto/query/max-aggfunction), a [Sum ()](/azure/kusto/query/sum-aggfunction), a [Count ()](/azure/kusto/query/count-aggfunction)és az [AVG ()](/azure/kusto/query/avg-aggfunction) az alacsony CPU-hatást okoznak a logikájuk miatt, más összetettebbek, és olyan becsléseket tartalmaznak, amelyek lehetővé teszik a hatékony végrehajtást. Például a [DCount ()](/azure/kusto/query/dcount-aggfunction) a HyperLogLog algoritmus használatával közelíti meg a nagy adathalmazok különböző számú adatát az egyes értékek tényleges számbavétele nélkül. a percentilis függvények hasonló közelítéseket végeznek a legközelebbi rangsorú percentilis algoritmus használatával. A parancsok közül több olyan választható paramétereket is tartalmaz, amelyek csökkentik a hatásukat. Például a [makeset ()](/azure/kusto/query/makeset-aggfunction) függvény nem kötelező paraméterrel határozhatja meg a maximálisan megengedett méretet, ami jelentősen befolyásolja a processzort és a memóriát.

A [Csatlakozás](/azure/kusto/query/joinoperator?pivots=azuremonitor) és a parancsok [összefoglalása](/azure/kusto/query/summarizeoperator) magas CPU-kihasználtságot okozhat, ha nagy mennyiségű adathalmazt dolgoz fel. Az összetettségük közvetlenül kapcsolódik a lehetséges értékek számához, amelyet az  `by` Összefoglalásként vagy illesztési attribútumokként használt oszlopok közül a függvénynek nevezünk. A JOIN és az összefoglalás ismertetését és optimalizálását a dokumentációs cikkek és optimalizálási tippek című cikkben találja.

Például a következő lekérdezések pontosan ugyanazt az eredményt eredményezik, mivel a **CounterPath** mindig egy-az-egyhez van leképezve a **CounterName** és a **ObjectName**. A második egy hatékonyabb, mivel az összesítési dimenzió kisebb:

```Kusto
//less efficient
Perf
| summarize avg(CounterValue) 
by CounterName, CounterPath, ObjectName
```
```Kusto
//make the group expression more compact improve the performance
Perf
| summarize avg(CounterValue), any(CounterName), any(ObjectName) 
by CounterPath
```

A CPU-használatot befolyásolhatja az olyan feltételek vagy kibővített oszlopok esetében is, amelyek intenzív számítástechnikai Igényűek. Minden olyan triviális karakterlánc-összehasonlítás, mint például az [EQUAL = =](/azure/kusto/query/datatypes-string-operators) és a [startswith](/azure/kusto/query/datatypes-string-operators) , nagyjából ugyanaz a CPU-hatás, míg a speciális szöveges találatok nagyobb hatással vannak. Pontosabban a [has](/azure/kusto/query/datatypes-string-operators) operátora hatékonyabb, hogy a [tartalmazza](/azure/kusto/query/datatypes-string-operators) az operátort. A karakterlánc-kezelési technikák miatt hatékonyabb a rövid sztringnél hosszabb karakterláncok megkeresése.

Az alábbi lekérdezések például a számítógép elnevezési házirendjétől függően hasonló eredményeket hoznak létre, a második pedig hatékonyabb:

```Kusto
//less efficient – due to filter based on contains
Heartbeat
| where Computer contains "Production" 
| summarize count() by ComputerIP 
```
```Kusto
//less efficient – due to filter based on extend
Heartbeat
| extend MyComputer = Computer
| where MyComputer startswith "Production" 
| summarize count() by ComputerIP 
```
```Kusto
//more efficient
Heartbeat
| where Computer startswith "Production" 
| summarize count() by ComputerIP 
```

> [!NOTE]
> Ez a mutató csak a közvetlen fürtből származó CPU-t jeleníti meg. A többrégiós lekérdezésekben csak az egyik régiót jelöli. Több munkaterület lekérdezése esetén előfordulhat, hogy nem tartalmazza az összes munkaterületet.

### <a name="avoid-full-xml-and-json-parsing-when-string-parsing-works"></a>A karakterlánc-elemzés működése során Kerülje a teljes XML-és JSON-elemzést
Egy XML-vagy JSON-objektum teljes elemzése nagy mennyiségű processzor-és memória-erőforrást is igénybe vehet. Sok esetben, ha csak egy vagy két paraméterre van szükség, és az XML-vagy JSON-objektumok egyszerűek, az [elemzési operátor](/azure/kusto/query/parseoperator) vagy más [szöveges elemzési módszerek](./parse-text.md)használatával könnyebben elemezheti őket karakterláncként. A teljesítmény növelése nagyobb jelentőséggel bír, mert az XML-vagy JSON-objektumban lévő rekordok száma növekszik. Elengedhetetlen, ha a rekordok száma több tízezer milliót is elér.

A következő lekérdezés például pontosan ugyanazokat az eredményeket fogja visszaadni, mint a fenti lekérdezéseket anélkül, hogy teljes XML-elemzést végezne. Vegye figyelembe, hogy az XML-fájl struktúrájában bizonyos feltételezések merülnek fel, például az, hogy a FilePath elem a FileHash után jön, és ezek egyike sem rendelkezik attribútumokkal. 

```Kusto
//even more efficient
SecurityEvent
| where EventID == 8002 //Only this event have FileHash
| where EventData !has "%SYSTEM32" //Early removal of unwanted records
| parse EventData with * "<FilePath>" FilePath "</FilePath>" * "<FileHash>" FileHash "</FileHash>" *
| summarize count() by FileHash, FilePath
| where FileHash != "" // No need to filter out %SYSTEM32 here as it was removed before
```


## <a name="data-used-for-processed-query"></a>Feldolgozott lekérdezéshez használt adatértékek

A lekérdezés feldolgozásának kritikus tényezője a lekérdezett és a lekérdezések feldolgozásához használt adatmennyiség. Az Azure Adatkezelő agresszív optimalizációt használ, amely jelentősen csökkenti az adatmennyiséget más adatplatformokhoz képest. A lekérdezésben kritikus tényezők is vannak, amelyek befolyásolhatják a használt adatmennyiséget.

A kettőnél több 000KB feldolgozó lekérdezés olyan lekérdezésnek minősül, amely túlzott erőforrást használ. A 20-nál több 000KB feldolgozás alatt álló lekérdezések visszaélésszerű lekérdezésnek minősülnek, és lehetséges, hogy szabályozva vannak.

Azure Monitor naplókban a **TimeGenerated** oszlop használható az adatindexeléshez. Ha a **TimeGenerated** értékeket úgy korlátozzák, hogy a lehető legkeskenyebbek legyenek, jelentős javulást eredményez a lekérdezési teljesítmény, mivel jelentősen korlátozza a feldolgozandó adatok mennyiségét.

### <a name="avoid-unnecessary-use-of-search-and-union-operators"></a>A keresési és a Union operátorok szükségtelen használatának elkerülése

Egy másik tényező, amely növeli a feldolgozott adatmennyiséget, nagy számú tábla használata. Ez általában akkor fordul elő, ha a `search *` és a `union *` parancsokat használja. Ezek a parancsok kényszerítik a rendszertől a munkaterület összes táblájából származó adatok kiértékelését és vizsgálatát. Bizonyos esetekben előfordulhat, hogy több száz tábla található a munkaterületen. Próbálja meg a lehető legnagyobb mértékben elkerülni a "keresés *" vagy bármilyen keresés használatát anélkül, hogy egy adott táblára kellene azt használni.

Például a következő lekérdezések pontosan ugyanazt az eredményt eredményezik, de az utolsó a leghatékonyabb:

```Kusto
// This version scans all tables though only Perf has this kind of data
search "Processor Time" 
| summarize count(), avg(CounterValue)  by Computer
```
```Kusto
// This version scans all strings in Perf tables – much more efficient
Perf
| search "Processor Time" 
| summarize count(), avg(CounterValue)  by Computer
```
```Kusto
// This is the most efficient version 
Perf 
| where CounterName == "% Processor Time"  
| summarize count(), avg(CounterValue)  by Computer
```

### <a name="add-early-filters-to-the-query"></a>Korai szűrők hozzáadása a lekérdezéshez

Az adatmennyiség csökkentésének egy másik módja, [Ha](/azure/kusto/query/whereoperator) a lekérdezésben korai feltételek vannak. Az Azure Adatkezelő platform egy olyan gyorsítótárat tartalmaz, amely lehetővé teszi, hogy mely partíciók tartalmazzák az adott feltételhez kapcsolódó adatokat. Ha például egy lekérdezés tartalmazza, `where EventID == 4624` akkor a lekérdezés csak olyan csomópontokra terjeszthető, amelyek a megfelelő eseményekkel rendelkező partíciókat kezelik.

A következő példában a lekérdezések pontosan ugyanazt az eredményt eredményezik, a második pedig hatékonyabb:

```Kusto
//less efficient
SecurityEvent
| summarize LoginSessions = dcount(LogonGuid) by Account
```
```Kusto
//more efficient
SecurityEvent
| where EventID == 4624 //Logon GUID is relevant only for logon event
| summarize LoginSessions = dcount(LogonGuid) by Account
```

### <a name="avoid-multiple-scans-of-same-source-data-using-conditional-aggregation-functions-and-materialize-function"></a>A feltételes összesítési függvények és a megvalósult függvények használatával ne vizsgáljon meg azonos forrásadatokat
Ha egy lekérdezés több allekérdezéssel is egyesítve van a JOIN vagy a Union operátorral, akkor minden allekérdezés külön ellenőrzi a teljes forrást, majd egyesíti az eredményeket. Ez többszörösen ellenőrzi, hogy az adat hányszor van beolvasva – kritikus tényező a nagyon nagy adatkészletekben.

Ennek elkerülésére szolgáló módszer a feltételes összesítési függvények használatával. Az összegző operátorban használt [aggregációs függvények](/azure/data-explorer/kusto/query/summarizeoperator#list-of-aggregation-functions) többsége egy feltételes verzió, amely lehetővé teszi egyetlen, több feltételt tartalmazó összefoglaló operátor használatát. 

Az alábbi lekérdezések például a bejelentkezési események számát és az egyes fiókok folyamat-végrehajtási eseményeinek számát mutatják. Ugyanezeket az eredményeket adják vissza, de az első az, hogy kétszer vizsgálja meg a második vizsgálatot:

```Kusto
//Scans the SecurityEvent table twice and perform expensive join
SecurityEvent
| where EventID == 4624 //Login event
| summarize LoginCount = count() by Account
| join 
(
    SecurityEvent
    | where EventID == 4688 //Process execution event
    | summarize ExecutionCount = count(), ExecutedProcesses = make_set(Process) by Account
) on Account
```

```Kusto
//Scan only once with no join
SecurityEvent
| where EventID == 4624 or EventID == 4688 //early filter
| summarize LoginCount = countif(EventID == 4624), ExecutionCount = countif(EventID == 4688), ExecutedProcesses = make_set_if(Process,EventID == 4688)  by Account
```

Egy másik eset, ahol az allekérdezések szükségtelenek az [elemzési operátor](/azure/data-explorer/kusto/query/parseoperator?pivots=azuremonitor) előzetes szűrésével, így biztosítható, hogy csak az adott mintának megfelelő rekordokat dolgozza fel. Ez szükségtelen, mert az elemzési operátor és más hasonló operátorok üres eredményeket adnak vissza, ha a minta nem egyezik. Az alábbiakban két olyan lekérdezés látható, amely pontosan ugyanazokat az eredményeket jeleníti meg, míg a második lekérdezési adatvizsgálat csak egyszer történik meg. A második lekérdezésben minden elemzési parancs csak az eseményeire vonatkozik. A kibővítő operátor ezt követően azt mutatja be, hogyan lehet az üres adathelyzetekre hivatkozni.

```Kusto
//Scan SecurityEvent table twice
union(
SecurityEvent
| where EventID == 8002 
| parse EventData with * "<FilePath>" FilePath "</FilePath>" * "<FileHash>" FileHash "</FileHash>" *
| distinct FilePath
),(
SecurityEvent
| where EventID == 4799
| parse EventData with * "CallerProcessName\">" CallerProcessName1 "</Data>" * 
| distinct CallerProcessName1
)
```

```Kusto
//Single scan of the SecurityEvent table
SecurityEvent
| where EventID == 8002 or EventID == 4799
| parse EventData with * "<FilePath>" FilePath "</FilePath>" * "<FileHash>" FileHash "</FileHash>" * //Relevant only for event 8002
| parse EventData with * "CallerProcessName\">" CallerProcessName1 "</Data>" *  //Relevant only for event 4799
| extend FilePath = iif(isempty(CallerProcessName1),FilePath,"")
| distinct FilePath, CallerProcessName1
```

Ha a fentiek nem teszik lehetővé az allekérdezések használatának elkerülését, egy másik megoldás a lekérdezési motorra mutat, hogy az egyes példányok mindegyike a [() függvényt](/azure/data-explorer/kusto/query/materializefunction?pivots=azuremonitor)használja. Ez akkor hasznos, ha a forrásadatok olyan függvényből érkeznek, amely többször is szerepel a lekérdezésben. A beléptetés akkor lép életbe, ha az allekérdezés kimenete sokkal kisebb, mint a bemenet. A lekérdezési motor gyorsítótárazza és újra felhasználja a kimenetet az összes előfordulásban.



### <a name="reduce-the-number-of-columns-that-is-retrieved"></a>Csökkentse a beolvasott oszlopok számát

Mivel az Azure Adatkezelő egy oszlopos adattár, minden oszlop lekérése független a többitől. A beolvasott oszlopok száma közvetlenül befolyásolja a teljes adatmennyiséget. Az eredmények [összesítésével](/azure/kusto/query/summarizeoperator) vagy az adott oszlopok [kivetítésével](/azure/kusto/query/projectoperator) csak a szükséges kimeneti oszlopokat kell tartalmaznia. Az Azure Adatkezelő számos optimalizálással rendelkezik, hogy csökkentse a beolvasott oszlopok számát. Ha úgy dönt, hogy nincs szükség oszlopra, például ha az [Összefoglaló](/azure/kusto/query/summarizeoperator) parancs nem hivatkozik rá, nem fogja lekérni.

Előfordulhat például, hogy a második lekérdezés háromszor feldolgozza az adatfeldolgozást, mivel nem egy oszlopot kell beolvasnia, hanem hármat:

```Kusto
//Less columns --> Less data
SecurityEvent
| summarize count() by Computer  
```
```Kusto
//More columns --> More data
SecurityEvent
| summarize count(), dcount(EventID), avg(Level) by Computer  
```

## <a name="time-span-of-the-processed-query"></a>A feldolgozott lekérdezés időbeli időtartama

Azure Monitor naplókban lévő összes napló particionálva van a **TimeGenerated** oszlop szerint. Az elérni kívánt partíciók száma közvetlenül kapcsolódik az időtartományhoz. Az időtartomány csökkentése a leghatékonyabb megoldás a gyors lekérdezés-végrehajtás biztosításához.

A 15 napnál hosszabb időtartamú lekérdezések olyan lekérdezések, amelyek túlzott erőforrást használnak. A 90 napnál hosszabb időtartamú lekérdezések visszaélésszerű lekérdezésnek minősülnek, és lehetséges, hogy szabályozva vannak.

Az időtartomány beállítható az időtartomány-választóval a Log Analytics képernyőn a [lekérdezési hatókör és az időtartomány Azure Monitor log Analyticsban](./scope.md#time-range)leírt módon. Ez az ajánlott módszer, mivel a kiválasztott időtartomány átadása a háttérnek a lekérdezés metaadatainak használatával történik. 

Egy másik módszer, ha explicit módon belefoglalja a lekérdezés **TimeGenerated** a [Where](/azure/kusto/query/whereoperator) feltételt. Ezt a módszert kell használnia, mivel biztosítja, hogy az időtartam rögzített, még akkor is, ha a lekérdezés más felületről van használatban.
Győződjön meg arról, hogy a lekérdezés minden részén van **TimeGenerated** szűrő. Ha egy lekérdezés allekérdezéseket tartalmaz a különböző táblákból vagy ugyanabból a táblából származó adatok beolvasására, akkor mindegyiknek saját, [Where](/azure/kusto/query/whereoperator) feltételt kell tartalmaznia.

### <a name="make-sure-all-sub-queries-have-timegenerated-filter"></a>Győződjön meg arról, hogy az összes allekérdezés rendelkezik TimeGenerated-szűrővel

Például a következő lekérdezésben, míg a **perf** tábla csak az elmúlt napra lesz megvizsgálva, a **szívverési** tábla az összes előzményét megvizsgálja, ami akár két évig is tarthat:

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    //No time span filter in this part of the query
    | summarize IPs = makeset(ComputerIP, 10) by  Computer
) on Computer
```

Gyakori eset, ha az ilyen hiba akkor fordul elő, amikor a [arg_max ()](/azure/kusto/query/arg-max-aggfunction) a legutóbbi előfordulás megkeresésére szolgál. Például:

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    //No time span filter in this part of the query
    | summarize arg_max(TimeGenerated, *), min(TimeGenerated)   
by Computer
) on Computer
```

Ez a belső lekérdezésben egy Időszűrő hozzáadásával egyszerűen kijavítható:

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    | where TimeGenerated > ago(1d) //filter for this part
    | summarize arg_max(TimeGenerated, *), min(TimeGenerated)   
by Computer
) on Computer
```

Egy másik példa erre a hibára, ha az időtartomány-szűrést csak az [Unió](/azure/kusto/query/unionoperator?pivots=azuremonitor) több táblázata után végezze el. Az Unió végrehajtásakor minden allekérdezés hatókörnek kell lennie. A [let](/azure/kusto/query/letstatement) utasítás használatával biztosíthatja a hatókör egységességét.

Például a következő lekérdezés a *szívverés* és a *perf* tábla összes adatát megvizsgálja, nem csak az utolsó 1 nap:

```Kusto
Heartbeat 
| summarize arg_min(TimeGenerated,*) by Computer
| union (
    Perf 
    | summarize arg_min(TimeGenerated,*) by Computer) 
| where TimeGenerated > ago(1d)
| summarize min(TimeGenerated) by Computer
```

Ezt a lekérdezést a következőképpen kell kijavítani:

```Kusto
let MinTime = ago(1d);
Heartbeat 
| where TimeGenerated > MinTime
| summarize arg_min(TimeGenerated,*) by Computer
| union (
    Perf 
    | where TimeGenerated > MinTime
    | summarize arg_min(TimeGenerated,*) by Computer) 
| summarize min(TimeGenerated) by Computer
```

### <a name="time-span-measurement-limitations"></a>Időtartomány-mérési korlátozások

A mérés mindig nagyobb, mint a megadott tényleges idő. Ha például a lekérdezés szűrője 7 nap, a rendszer 7,5 vagy 8,1 napot vizsgálhat. Ennek az az oka, hogy a rendszer a változó méretű adattömbökbe particionálja az adathalmazokat. Annak biztosítása érdekében, hogy a rendszer az összes releváns rekordot megvizsgálja, megvizsgálja a teljes partíciót, amely több óráig is eltarthat, és akár egy napnál is hosszabb időt is igénybe vehet.

Több eset is létezik, ha a rendszer nem tud pontos mérési értéket biztosítani az időtartományhoz. Ez a legtöbb esetben fordul elő, amikor a lekérdezés egy napnál rövidebb ideig vagy több-munkaterület lekérdezésekben van.


> [!IMPORTANT]
> Ez a kijelző csak a közvetlen fürtben feldolgozott adatfeldolgozást jeleníti meg. A többrégiós lekérdezésekben csak az egyik régiót jelöli. Több munkaterület lekérdezése esetén előfordulhat, hogy nem tartalmazza az összes munkaterületet.

## <a name="age-of-processed-data"></a>Feldolgozott adatmennyiség kora
Az Azure Adatkezelő számos tárolási szintet használ: memóriabeli, helyi SSD-lemezeket és sokkal lassabb Azure-blobokat. Minél újabb adatról van szó, annál nagyobb a valószínűsége, hogy egy nagyobb teljesítményű, kisebb késéssel rendelkező, a lekérdezés időtartamát és a PROCESSZORt is csökkenti. Az adatoktól eltérő esetben a rendszer gyorsítótárat is tartalmaz a metaadatokhoz. Minél régebbiek az adatok, annál kisebb a metaadatok a gyorsítótárban.

A 14 napnál hosszabb ideig feldolgozó lekérdezések olyan lekérdezésnek minősülnek, amely túlzott erőforrást használ.


Míg egyes lekérdezések a régi adatok használatát igénylik, vannak olyan esetek, amikor a régi adatokat tévesen használják. Ez akkor fordul elő, ha a lekérdezések végrehajtása nem biztosít időtartományt a meta-adatokban, és nem minden táblázat hivatkozása tartalmazza a **TimeGenerated** oszlop szűrőjét. Ezekben az esetekben a rendszer megvizsgálja az adott táblázatban tárolt összes adattípust. Ha az adatok megőrzése hosszú, akkor a hosszú időtartományokra, így az adatmegőrzési időszakot megelőzően lévő adatokra is vonatkozhat.

Ilyen eset például a következő lehet:

- A Log Analytics időtartományának beállítása nem korlátozott allekérdezéssel. Lásd a fenti példát.
- Az API használata az időtartomány választható paramétereinek megadása nélkül.
- Olyan ügyfél használata, amely nem kényszeríti az időtartományt, például az Power BI-összekötőt.

Tekintse meg a példákat és a megjegyzéseket a áteresztő szakaszban, mivel ezek a jelen esetben is relevánsak.

## <a name="number-of-regions"></a>Régiók száma
Több olyan helyzet is előfordulhat, amikor egyetlen lekérdezést hajthat végre különböző régiókban:

- Ha több munkaterület van explicit módon listázva, és különböző régiókban találhatók.
- Ha egy erőforrás-hatókörű lekérdezés beolvassa az adatgyűjtést, és az adatai több munkaterületen vannak tárolva, amelyek különböző régiókban találhatók.

A régiók közötti lekérdezés végrehajtása megköveteli, hogy a rendszer szerializálást és átvitelt hajtson végre a közbenső adatok háttérbeli nagy adattömbökben, amelyek általában jóval nagyobbak a lekérdezés végső eredményeinél. Emellett korlátozza a rendszerek optimalizálási, heurisztikus és használati lehetőségeit is.
Ha nincs valós ok az összes ilyen régió vizsgálatára, állítsa be úgy a hatókört, hogy kevesebb régióra kiterjedjen. Ha az erőforrás hatóköre kisebb, de még sok régiót használ, előfordulhat, hogy helytelen a konfiguráció. A naplókat és a diagnosztikai beállításokat például különböző régiókban lévő különböző munkaterületekre küldik, vagy több diagnosztikai beállítási konfiguráció is van. 

A több mint 3 régiót átölelő lekérdezés olyan lekérdezésnek minősül, amely túlzott erőforrást használ. A több mint 6 régióra kiterjedő lekérdezések visszaélésszerű lekérdezésnek minősülnek, és lehetséges, hogy szabályozva vannak.

> [!IMPORTANT]
> Ha egy lekérdezés több régióban fut, a processzor és az adatmérések nem lesznek pontosak, és csak az egyik régióban jelennek meg a mérések.

## <a name="number-of-workspaces"></a>Munkaterületek száma
A munkaterületek logikai tárolók, amelyek a naplók adatai elkülönítésére és felügyeletére szolgálnak. A háttérrendszer a kiválasztott régióban található fizikai fürtökön a munkaterület elhelyezéseit optimalizálja.

Több munkaterület használata a következő eredményekből járhat: 

- Ahol több munkaterület van kifejezetten felsorolva.
- Ha egy erőforrás-hatókörű lekérdezés beolvassa az adatgyűjtést, és az adattárolás több munkaterületen történik.
 
A lekérdezések régiók közötti és több fürtre kiterjedő végrehajtása megköveteli, hogy a rendszer szerializálja és átvigye a háttérbeli adatok nagy részét, amely általában jóval nagyobb a lekérdezés végső eredményeinél. Emellett korlátozza az optimalizálások, a heurisztikus műveletek és a gyorsítótárak kihasználásának rendszerképességét is.

A több mint 5 munkaterületre kiterjedő lekérdezés olyan lekérdezésnek minősül, amely túlzott erőforrást használ. A lekérdezések legfeljebb 100 munkaterületre terjedhetnek ki.

> [!IMPORTANT]
> Egyes többmunkaterületos helyzetekben a processzor-és adatmérések nem pontosak, és a mérések csak néhány munkaterületnek felelnek meg.

## <a name="parallelism"></a>Párhuzamosság
Azure Monitor naplók az Azure Adatkezelő nagyméretű fürtjét használják a lekérdezések futtatásához, és ezek a fürtök mérettől függően változhatnak, akár több tucat számítási csomópontra is. A rendszer a munkaterület elhelyezési logikája és kapacitása alapján automatikusan méretezi a fürtöket.

A lekérdezés hatékony végrehajtásához particionálni és terjeszteni kell a számítási csomópontokat a feldolgozáshoz szükséges adatok alapján. Vannak olyan helyzetek, amikor a rendszer nem tudja ezt hatékonyan végrehajtani. Ez hosszú időtartamot eredményezhet a lekérdezésben. 

A párhuzamosságot csökkentő lekérdezési viselkedések például a következők:

- Szerializálási és ablakos függvények, például a [szerializálási operátor](/azure/kusto/query/serializeoperator), a [Next ()](/azure/kusto/query/nextfunction), a [Prev ()](/azure/kusto/query/prevfunction)és a [Row](/azure/kusto/query/rowcumsumfunction) függvények használata. Ezekben az esetekben a Time Series és a User Analytics functions is használható. A nem hatékony szerializálás akkor is előfordulhat, ha a következő operátorok nem a lekérdezés végén vannak használatban: [Range](/azure/kusto/query/rangeoperator), [sort](/azure/kusto/query/sortoperator), [Order](/azure/kusto/query/orderoperator), [Top](/azure/kusto/query/topoperator), [Top-ütős](/azure/kusto/query/tophittersoperator), [GetSchema](/azure/kusto/query/getschemaoperator).
-    A [DCount ()](/azure/kusto/query/dcount-aggfunction) aggregációs függvény használata azt kényszeríti, hogy a rendszeren a különböző értékek központi másolata legyen. Ha az adatok terjedelme magas, érdemes lehet a DCount függvényt a választható paraméterekkel csökkenteni a pontosság csökkentése érdekében.
-    Sok esetben a [JOIN](/azure/kusto/query/joinoperator?pivots=azuremonitor) operátor csökkenti az általános párhuzamosságot. A teljesítménnyel kapcsolatos probléma megoldásakor vizsgálja meg a shuffle illesztést.
-    Az erőforrás-hatókörű lekérdezésekben a végrehajtás előtti Kubernetes RBAC vagy az Azure RBAC-ellenőrzések olyan helyzetekben merülhetnek fel, amikor nagyon nagy számú Azure-szerepkör-hozzárendelés van. Ez több ellenőrzéshez vezethet, ami alacsonyabb párhuzamosságot eredményezhet. Egy lekérdezés például egy olyan előfizetésen történik, ahol több ezer erőforrás van, és az egyes erőforrások számos szerepkör-hozzárendeléssel rendelkeznek az erőforrás szintjén, nem az előfizetéshez vagy az erőforráscsoporthoz.
-    Ha egy lekérdezés kis adattömböket dolgoz fel, a párhuzamossága alacsony lesz, mivel a rendszer nem fogja elosztani számos számítási csomópont között.



## <a name="next-steps"></a>Következő lépések

- [A Kusto lekérdezési nyelvét ismertető dokumentáció](/azure/kusto/query/).