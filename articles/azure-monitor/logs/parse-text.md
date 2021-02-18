---
title: Szöveges adatelemzés Azure Monitor naplókban | Microsoft Docs
description: Ismerteti a naplózási adatok Azure Monitor rekordokban való elemzésének különböző lehetőségeit, ha az adatok betöltésére és a lekérdezésbe való beolvasására szolgálnak, összehasonlítva az egyes lehetőségek relatív előnyeit.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/04/2018
ms.openlocfilehash: 29393126f94d77dbced0ef896616b945443184fd
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100620008"
---
# <a name="parse-text-data-in-azure-monitor-logs"></a>Szöveges adatelemzés Azure Monitor naplókban
Néhány Azure Monitor által összegyűjtött naplózási adat több adatot is tartalmaz egyetlen tulajdonságban. Ha ezeket az adatelemzéseket több tulajdonságba elemezze, egyszerűbbé teheti a lekérdezésekben való használatát. Az általános példa egy olyan [Egyéni napló](../agents/data-sources-custom-logs.md) , amely egy teljes naplóbejegyzést gyűjt több értékkel egyetlen tulajdonságban. A különböző értékek külön tulajdonságainak létrehozásával megkeresheti és összesítheti az egyes értékeket.

Ez a cikk különböző lehetőségeket ismertet a naplófájlok elemzéséhez Azure Monitor az adatfeldolgozás során, valamint a lekérdezésekben való lekéréskor, az egyes lehetőségek relatív előnyeinek összehasonlításával.


## <a name="parsing-methods"></a>Elemzési módszerek
Az adatokat betöltési időben is elemezheti, ha az adatok gyűjtése vagy lekérdezési ideje történik az adatok lekérdezéssel történő elemzésekor. Minden stratégia egyedi előnyökkel rendelkezik, az alábbiakban leírtak szerint.

### <a name="parse-data-at-collection-time"></a>Adatgyűjtés a gyűjtés időpontjában
Amikor begyűjtési időpontban elemezi az adatelemzést, olyan [egyéni mezőket](../logs/custom-fields.md) állít be, amelyek új tulajdonságokat hoznak létre a táblában. A lekérdezéseknek nem kell semmilyen elemzési logikát tartalmazniuk, és egyszerűen ezeket a tulajdonságokat kell használniuk a tábla bármely más mezőjéhez.

A módszer előnyei többek között a következők:

- Könnyebben lekérdezheti az összegyűjtött adatokat, mivel nem kell belefoglalnia az elemzési parancsokat a lekérdezésbe.
- Jobb lekérdezési teljesítmény, mivel a lekérdezésnek nem kell végrehajtania az elemzést.
 
A módszer hátrányai a következők:

- Előre kell definiálni. A már összegyűjtött adatok nem szerepelhetnek bele.
- Ha megváltoztatja az elemzési logikát, akkor csak az új adatértékekre fog vonatkozni.
- Kevesebb elemzési lehetőség a lekérdezésekben elérhetőnél.
- Növeli az adatok gyűjtésének késleltetési idejét.
- A hibák kezelése nehéz lehet.


### <a name="parse-data-at-query-time"></a>Az adatelemzés lekérdezési időpontban
Amikor lekérdezési időpontban elemezi az adatelemzést, a lekérdezésben logikát is tartalmaz, amely több mezőbe elemezi az adatelemzést. Maga a tényleges tábla nem módosul.

A módszer előnyei többek között a következők:

- Az adatokra vonatkozik, beleértve a már összegyűjtött adatokat is.
- A Logic változásai azonnal alkalmazhatók az összes adattal.
- Rugalmas elemzési lehetőségek, beleértve az előre definiált logikát az egyes adatstruktúrákhoz.
 
A módszer hátrányai a következők:

- Összetettebb lekérdezésekre van szükség. Ez enyhíthető a [függvények használatával egy tábla szimulálása érdekében](#use-function-to-simulate-a-table).
- Több lekérdezésben kell replikálni az elemzési logikát. Megoszthat valamilyen logikát a functions használatával.
- Létrehozhat terhelést, ha összetett logikát futtat a nagyon nagy rekordhalmazok (több milliárd rekord) alapján.

## <a name="parse-data-as-its-collected"></a>Adatok elemzése az adatgyűjtés során
A gyűjtött adatok elemzésével kapcsolatos részletekért lásd: [Egyéni mezők létrehozása a Azure monitorban](../logs/custom-fields.md) . Ez a tábla olyan egyéni tulajdonságokat hoz létre, amelyeket a lekérdezések a többi tulajdonsághoz hasonlóan használhatnak.

## <a name="parse-data-in-query-using-patterns"></a>Az adatelemzés a lekérdezésekben minták használatával
Ha az elemezni kívánt adatok a rekordok között ismétlődő mintázattal azonosíthatók, akkor a [Kusto lekérdezési nyelvében](/azure/kusto/query/) különböző operátorok segítségével kinyerheti az adott adatokat egy vagy több új tulajdonságba.

### <a name="simple-text-patterns"></a>Egyszerű szöveges minták

A lekérdezésben szereplő [elemzési](/azure/kusto/query/parseoperator) operátor használatával létrehozhat egy vagy több olyan egyéni tulajdonságot, amely egy karakterlánc-kifejezésből kinyerhető. Meg kell adnia az azonosított mintát és a létrehozandó tulajdonságok nevét. Ez különösen hasznos a Key-Value sztringekkel rendelkező, a _Key = Value értékhez_ hasonló formában.

Vegyünk fel egy egyéni naplófájlt az alábbi formátumban.

```
Time=2018-03-10 01:34:36 Event Code=207 Status=Success Message=Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
Time=2018-03-10 01:33:33 Event Code=208 Status=Warning Message=Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
Time=2018-03-10 01:35:44 Event Code=209 Status=Success Message=Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
Time=2018-03-10 01:38:22 Event Code=302 Status=Error Message=Application could not connect to database
Time=2018-03-10 01:31:34 Event Code=303 Status=Error Message=Application lost connection to database
```

A következő lekérdezés az adatelemzést egyedi tulajdonságokra fogja elemezni. A _projekttel_ rendelkező sor csak a számított tulajdonságokat adja vissza, nem pedig a _RawData_, amely az egyéni napló teljes bejegyzését birtokló egyetlen tulajdonság.

```Kusto
MyCustomLog_CL
| parse RawData with * "Time=" EventTime " Event Code=" Code " Status=" Status " Message=" Message
| project EventTime, Code, Status, Message
```

A következő példa egy másik példát mutat be, amely kiszakítja egy egyszerű felhasználónév felhasználónevét a _AzureActivity_ táblában.

```Kusto
AzureActivity
| parse  Caller with UPNUserPart "@" * 
| where UPNUserPart != "" //Remove non UPN callers (apps, SPNs, etc)
| distinct UPNUserPart, Caller
```


### <a name="regular-expressions"></a>Reguláris kifejezések
Ha az adatok azonosíthatók reguláris kifejezéssel, az egyes értékek kinyeréséhez [reguláris kifejezéseket használó függvények](/azure/kusto/query/re2) is használhatók. A következő példa a [kinyerést](/azure/kusto/query/extractfunction) használja az _UPN_ mező kibontásához a _AzureActivity_ -rekordokból, majd a különböző felhasználókat adja vissza.

```Kusto
AzureActivity
| extend UPNUserPart = extract("([a-z.]*)@", 1, Caller) 
| distinct UPNUserPart, Caller
```

A hatékony, nagy méretekben történő elemzés engedélyezéséhez Azure Monitor a reguláris kifejezések RE2 verzióját használja, amely hasonló, de nem azonos a többi reguláris kifejezés változatával. A részletekért tekintse meg a [RE2 kifejezés szintaxisát](https://aka.ms/kql_re2syntax) .


## <a name="parse-delimited-data-in-a-query"></a>Tagolt adatelemzés egy lekérdezésben
A tagolt adat elválasztja a mezőket egy közös karakterrel, például egy CSV-fájlban található vesszőt. A [tagolt függvénnyel](/azure/kusto/query/splitfunction) elemezheti a tagolt fájlokat egy Ön által megadott határolójel használatával. Ezt a [kibővítési](/azure/kusto/query/extendoperator) operátorral végezheti el az adat összes mezőjének visszaadásához, vagy megadhatja a kimenetben szerepeltetni kívánt egyes mezőket.

> [!NOTE]
> Mivel a felosztás dinamikus objektumot ad vissza, előfordulhat, hogy az eredményeket explicit módon kell átadni olyan adattípusokhoz, mint például a kezelők és szűrők által használandó karakterlánc.

Vegyünk fel egy egyéni naplófájlt az alábbi CSV-formátumban.

```
2018-03-10 01:34:36, 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
2018-03-10 01:33:33, 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
2018-03-10 01:35:44, 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
2018-03-10 01:38:22, 302,Error,Application could not connect to database
2018-03-10 01:31:34, 303,Error,Application lost connection to database
```

A következő lekérdezés elemezni fogja ezeket az adatelemzéseket, és összesíti a számított tulajdonságok közül kettőt. Az első sor a  _RawData_ tulajdonságot egy karakterlánc-tömbre osztja fel. A következő sorok mindegyike egy nevet ad az egyes tulajdonságoknak, és hozzáadja őket a kimenethez függvények használatával, hogy a megfelelő adattípusra alakítsa őket.

```Kusto
MyCustomCSVLog_CL
| extend CSVFields  = split(RawData, ',')
| extend EventTime  = todatetime(CSVFields[0])
| extend Code       = toint(CSVFields[1]) 
| extend Status     = tostring(CSVFields[2]) 
| extend Message    = tostring(CSVFields[3]) 
| where getyear(EventTime) == 2018
| summarize count() by Status,Code
```

## <a name="parse-predefined-structures-in-a-query"></a>Előre definiált struktúrák elemzése egy lekérdezésben
Ha az adatok egy ismert struktúrában vannak formázva, az előre definiált struktúrák elemzéséhez használhatja a [Kusto lekérdezési nyelvének](/azure/kusto/query/) egyik függvényét is:

- [JSON](/azure/kusto/query/parsejsonfunction)
- [XML](/azure/kusto/query/parse-xmlfunction)
- [IPv4](/azure/kusto/query/parse-ipv4function)
- [URL-cím](/azure/kusto/query/parseurlfunction)
- [URL-lekérdezés](/azure/kusto/query/parseurlqueryfunction)
- [Fájl elérési útja](/azure/kusto/query/parsepathfunction)
- [Felhasználói ügynök](/azure/kusto/query/parse-useragentfunction)
- [Verzió karakterlánca](/azure/kusto/query/parse-versionfunction)

A következő példa a _AzureActivity_ tábla _Tulajdonságok_ mezőjét elemzi, amely a JSON-ban van strukturálva. Az eredményeket egy _parsedProp_ nevű dinamikus tulajdonságba menti, amely magában foglalja a JSON-ban megnevezett értékeket is. Ezek az értékek a lekérdezés eredményeinek szűrésére és összefoglalására szolgálnak.

```Kusto
AzureActivity
| extend parsedProp = parse_json(Properties) 
| where parsedProp.isComplianceCheck == "True" 
| summarize count() by ResourceGroup, tostring(parsedProp.tags.businessowner)
```

Ezek az elemzési függvények felhasználhatják a processzort, ezért csak akkor használhatók, ha a lekérdezés több tulajdonságot használ a formázott adatokból. Ellenkező esetben az egyszerű minták egyeztetésének feldolgozása gyorsabb lesz.

Az alábbi példa a tartományvezérlő TGT-előhitelesítési típusának lebontását mutatja be. A típus csak a EventData mezőben létezik, amely egy XML-karakterlánc, de a mezőtől eltérő adatok nem szükségesek. Ebben az esetben az [elemzés](/azure/kusto/query/parseoperator) a szükséges adat kiválasztására szolgál.

```Kusto
SecurityEvent
| where EventID == 4768
| parse EventData with * 'PreAuthType">' PreAuthType '</Data>' * 
| summarize count() by PreAuthType
```

## <a name="use-function-to-simulate-a-table"></a>Táblázat szimulálása függvény használatával
Lehet, hogy több lekérdezés is van, amelyek egy adott tábla azonos elemzését végzik. Ebben az esetben [hozzon létre egy függvényt](../logs/functions.md) , amely visszaadja az elemzett adatok helyett az elemzési logika replikálását az egyes lekérdezésekben. Ezután használhatja a függvény aliasát az eredeti tábla helyett más lekérdezésekben.

Vegye figyelembe a fenti példában szereplő, vesszővel tagolt egyéni naplót. Az elemzett adat több lekérdezésben való használatához hozzon létre egy függvényt a következő lekérdezés használatával, és mentse azt az alias _MyCustomCSVLog_.

```Kusto
MyCustomCSVLog_CL
| extend CSVFields = split(RawData, ',')
| extend DateTime  = tostring(CSVFields[0])
| extend Code      = toint(CSVFields[1]) 
| extend Status    = tostring(CSVFields[2]) 
| extend Message   = tostring(CSVFields[3]) 
```

Mostantól a _MyCustomCSVLog_ aliast is használhatja a tényleges táblanév helyett a következőhöz hasonló lekérdezésekben.

```Kusto
MyCustomCSVLog
| summarize count() by Status,Code
```


## <a name="next-steps"></a>Következő lépések
* További információ az adatforrásokból és megoldásokból gyűjtött adatok elemzéséhez szükséges [naplók lekérdezéséről](../log-query/log-query-overview.md) .
