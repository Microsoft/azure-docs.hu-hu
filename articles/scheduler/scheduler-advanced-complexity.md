---
title: Speciális feladatok ütemezése és ismétlődések létrehozása
description: Ismerje meg, hogyan hozhat létre speciális ütemezéseket és ismétlődéseket a feladatokhoz az Azure Schedulerben
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.suite: infrastructure-services
ms.topic: article
ms.date: 11/14/2018
ms.openlocfilehash: 5a74240e3f116121c0aaddd11c186e6e674ea26a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92368179"
---
# <a name="build-advanced-schedules-and-recurrences-for-jobs-in-azure-scheduler"></a>Speciális ütemezések és ismétlődések létrehozása a feladatokhoz az Azure Schedulerben

> [!IMPORTANT]
> [Azure Logic apps](../logic-apps/logic-apps-overview.md) az Azure Scheduler cseréje [folyamatban](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)van. Ha továbbra is szeretne dolgozni a Feladatütemezőben beállított feladatokkal, akkor a lehető leghamarabb [telepítse át Azure Logic apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) . 
>
> Az ütemező már nem érhető el a Azure Portalban, de a [REST API](/rest/api/scheduler) és az [Azure Scheduler PowerShell-parancsmagjai](scheduler-powershell-reference.md) jelenleg is elérhetők maradnak, így a feladatok és a feladatok gyűjteményei kezelhetők.

Az [Azure Scheduler](../scheduler/scheduler-intro.md) -feladatokon belül az ütemezés az a mag, amely meghatározza, hogy mikor és hogyan futtatja az ütemező szolgáltatás a feladatot. Több egyszeri és ismétlődő ütemezést is beállíthat a Feladatütemezővel végzett feladatokhoz. Az egyszeri ütemtervek csak egyszer futnak egy adott időpontban, és alapvetően ismétlődő ütemtervek, amelyek csak egyszer futnak. Az ismétlődő ütemtervek a megadott gyakorisággal futnak. Ezzel a rugalmassággal különböző üzleti forgatókönyvekhez használhatja a Schedulert, például:

* **Az adatok rendszeres** törlése: hozzon létre egy napi feladatot, amely a három hónapnál régebbi összes tweetet törli.

* **Archivált adatok**: hozzon létre egy havi feladatot, amely leküldi a számlázási előzményeket egy biztonsági mentési szolgáltatásba.

* **Külső adatok kérése**: hozzon létre egy feladatot, amely 15 percenként fut, és lekéri a NOAA új időjárási jelentését.

* **Lemezképek feldolgozása**: hozzon létre egy olyan hétköznap-feladatot, amely a munkaidőn kívül fut, és a felhő-számítástechnika használatával tömöríti a nap folyamán feltöltött képeket.

Ez a cikk az ütemező és az [Azure scheduler REST API](/rest/api/scheduler)használatával létrehozható feladatokat ismerteti, és tartalmazza az egyes ütemezésekhez tartozó JavaScript Object Notation (JSON) definíciót is. 

## <a name="supported-scenarios"></a>Támogatott esetek

Ezek a példák az Azure Scheduler által támogatott forgatókönyvek tartományát, valamint a különböző viselkedési minták ütemezésének létrehozását mutatják be, például:

* Egyszeri Futtatás adott dátummal és időponttal.
* Adott számú alkalommal futtathatja és megismétlődik.
* Azonnali Futtatás és ismétlődés.
* Minden *n* perc, óra, nap, hét vagy hónap elteltével futtasson és ismétlődjön, egy adott időpontban.
* A hetente vagy havonta ismétlődik, de csak a hét meghatározott napjain vagy a hónap adott napjain.
* Egy adott időszakra többször is futtatható és ismétlődik. Például minden hónapban az elmúlt pénteken és hétfőn, vagy naponta 5:15 órakor és 5:15 ÓRAKOR.

Ez a cikk később részletesen ismerteti ezeket a forgatókönyveket.

<a name="create-scedule"></a>

## <a name="create-schedule-with-rest-api"></a>Ütemterv létrehozása REST API

Ha alapszintű ütemezést szeretne létrehozni az [Azure Scheduler REST API](/rest/api/scheduler), kövesse az alábbi lépéseket:

1. Regisztrálja Azure-előfizetését egy erőforrás-szolgáltatónál a [regisztrálási művelet – erőforrás-kezelő REST API](/rest/api/resources/providers)használatával. Az Azure Scheduler szolgáltatás szolgáltatójának neve a **Microsoft. Scheduler**. 

1. Hozzon létre egy feladattípust a Feladatütemező REST API [Létrehozás vagy frissítés műveletének](/rest/api/scheduler/jobcollections) használatával. 

1. Hozzon létre egy feladatot a [feladatok létrehozása vagy frissítése művelet](/rest/api/scheduler/jobs/createorupdate)használatával. 

## <a name="job-schema-elements"></a>A feladatok sémájának elemei

Ez a táblázat a feladatok ismétlődésének és ütemezéseinak beállításakor használható fő JSON-elemek magas szintű áttekintését tartalmazza. 

| Elem | Kötelező | Leírás | 
|---------|----------|-------------|
| **startTime** | No | Az [ISO 8601 formátumú](https://en.wikipedia.org/wiki/ISO_8601) datetime karakterlánc-érték, amely megadja, hogy a feladat Mikor indul el alapszintű ütemterv szerint. <p>Összetett ütemtervek esetén a feladat nem indul el hamarabb, mint a **kezdő időpont**. | 
| **megismétlődésének** | No | Az ismétlődési szabályok a feladatok futásakor. Az **ismétlődési** objektum a következő elemeket támogatja: **gyakoriság**, **intervallum**, **ütemezése**, **darabszám** és **Befejezés**. <p>Ha az **Ismétlődés** elemet használja, a **Frequency** elemet is használnia kell, míg más **ismétlődési** elemek nem kötelezőek. |
| **frequency** | Igen, az **Ismétlődés** használatakor | Az előfordulások közötti időegység, amely a következő értékeket támogatja: "minute", "Hour", "Day", "Week", "Month" és "Year" | 
| **időköz** | No | Pozitív egész szám, amely meghatározza, hogy a **gyakoriság** alapján hány időegység legyen az előfordulások között. <p>Ha például az **intervallum** 10, a **gyakoriság** pedig a "Week", a feladattípus 10 hetente ismétlődik. <p>Az egyes gyakoriságok esetében a legtöbb intervallum a következő: <p>– 18 hónap <br>– 78 hét <br>– 548 nap <br>-Óra és perc esetén a tartomány 1 <= <*intervallum*> <= 1000. | 
| **menetrend** | No | Az ismétlődés változásait határozza meg a megadott percenkénti jelek, óra-jelek, a hét napjai és a hónap napjai alapján. | 
| **count** | No | Pozitív egész szám, amely meghatározza, hogy a feladatok hányszor futnak a befejezés előtt. <p>Ha például egy napi feladatnak 7 **értékűnek** kell lennie, és a kezdő dátum hétfő, a feladatok vasárnap futnak. Ha a kezdő dátum már át lett adva, az első futtatás a létrehozási időpontból lesz kiszámítva. <p>A **Befejezés** vagy a **szám** nélkül a feladatok végtelenül futnak. Ugyanabban a feladatokban nem használhatók a **Count** és a **befejezési** érték, de az első befejezési szabály is tiszteletben van. | 
| **endTime** | No | Az [ISO 8601 formátumban](https://en.wikipedia.org/wiki/ISO_8601) megadott dátum-vagy datetime-karakterlánc, amely meghatározza, hogy a rendszer mikor futtassa a feladatot. Megadhat egy **értéket a korábbi** időpontokban. <p>A **Befejezés** vagy a **szám** nélkül a feladatok végtelenül futnak. Ugyanabban a feladatokban nem használhatók a **Count** és a **befejezési** érték, de az első befejezési szabály is tiszteletben van. |
|||| 

Ez a JSON-séma például egy alapszintű ütemtervet és ismétlődést ír le egy feladathoz: 

```json
"properties": {
   "startTime": "2012-08-04T00:00Z", 
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "weekDays": ["Monday", "Wednesday", "Friday"],
         "hours": [10, 22]                      
      },
      "count": 10,       
      "endTime": "2012-11-04"
   },
},
``` 

*Dátumok és dátum és idő értékek*

* A Scheduler-feladatok dátumai csak a dátumot tartalmazzák, és az [ISO 8601-es specifikációt](https://en.wikipedia.org/wiki/ISO_8601)követik.

* A Scheduler-feladatokban a dátum-és időpontok is szerepelnek, az [ISO 8601-es specifikációnak](https://en.wikipedia.org/wiki/ISO_8601)megfelelően, és a rendszer feltételezi, hogy UTC szerint nincs megadva UTC-eltolás. 

További információ: [fogalmak, terminológia és entitások](../scheduler/scheduler-concepts-terms.md).

<a name="start-time"></a>

## <a name="details-starttime"></a>Részletek: kezdő időpont

Ez a táblázat azt ismerteti, hogy a **kezdő időpont** hogyan szabályozza a feladatok futtatásának módját:

| startTime | Nincs ismétlődés | Ismétlődés, nincs ütemezése | Ismétlődés ütemezéssel |
|-----------|---------------|-------------------------|--------------------------|
| **Nincs kezdési idő** | Azonnali Futtatás. | Azonnali Futtatás. Az utolsó végrehajtási időpontból kiszámított későbbi végrehajtások futtatása. | Azonnali Futtatás. Későbbi végrehajtások futtatása ismétlődési ütemterv alapján. | 
| **Kezdési idő a múltban** | Azonnali Futtatás. | Kiszámítja a kezdési időpont utáni első jövőbeli futási időt, és az adott időpontban fut. <p>Az utolsó végrehajtási időpontból kiszámított későbbi végrehajtások futtatása. <p>Lásd a táblázat utáni példát. | A kezdési feladatot nem a megadott kezdési időpontnál *hamarabb* kell elindítani. Az első előfordulás a kezdési időpontból kiszámított ütemezésen alapul. <p>Későbbi végrehajtások futtatása ismétlődési ütemterv alapján. | 
| **Kezdési idő a jövőben vagy az aktuális idő** | Egyszeri Futtatás a megadott kezdési időpontban. | Egyszeri Futtatás a megadott kezdési időpontban. <p>Az utolsó végrehajtási időpontból kiszámított későbbi végrehajtások futtatása. | A kezdési feladatot nem a megadott kezdési időpontnál *hamarabb* kell elindítani. Az első előfordulás a kezdő időpontból kiszámított ütemezésen alapul. <p>Későbbi végrehajtások futtatása ismétlődési ütemterv alapján. |
||||| 

Tegyük fel, hogy a következő feltételekkel rendelkezik: egy korábbi kezdési időpont egy ismétlődéssel, de nincs ütemezése.

```json
"properties": {
   "startTime": "2015-04-07T14:00Z", 
   "recurrence": {
      "frequency": "Day",
      "interval": 2
   }
}
```

* Az aktuális dátum és idő április 08., 2015, 1:00 PM.

* A kezdési dátum és az idő az aktuális dátum és idő előtti 2015. április 07., 2:00.

* Az ismétlődés két nap.

1. A fenti feltételek szerint az első végrehajtás a 2015-kor Április 09-én, 2:00 órakor. 

   A Scheduler a kezdési időpont alapján kiszámítja a végrehajtás előfordulásait, elveti a múltbeli példányokat, és a későbbiekben a következő példányt használja. 
   Ebben az esetben a kezdési **időpont** április 07., 2015, 2:00 PM, így a következő példány két nappal az idő függvényében, amely a 2015. április 9-én, 2:00 órakor.

   Az első végrehajtás ugyanaz, hogy a **kezdő időpont** 2015-04-05 14:00 vagy 2015-04-01 14:00. Az első végrehajtás után a későbbi végrehajtások kiszámítása az ütemterv alapján történik. 
   
1. A végrehajtások ezután a következő sorrendben jelennek meg: 
   
   1. 2015-04-11, 2:00 PM
   1. 2015-04-13, 2:00 PM 
   1. 2015-04-15, 2:00 PM
   1. És így tovább...

1. Végül, ha egy adott feladatokhoz ütemezve van, de nincs megadott óra és perc, a rendszer alapértelmezés szerint az első végrehajtáshoz tartozó órákat és perceket adja meg.

<a name="schedule"></a>

## <a name="details-schedule"></a>Részletek: ütemterv

Az **ütemező** használatával *korlátozhatja* a feladatok végrehajtásának számát. Ha például egy "Month" **gyakoriságú** feladatnak van egy olyan ütemterve, amely csak a 31. napon fut, a feladatok csak a 31 napos időszakra futnak.

Az **ütemező** használatával is *kibonthatja* a feladatok végrehajtásának számát. Ha például egy "Month" **gyakoriságú** feltételnek van olyan ütemterve, amely az 1. és a 2. hónapban fut, a feladatot a hónap első és második napján futtatja, nem csak egyszer egy hónapban.

Ha egynél több Schedule elemet ad meg, a kiértékelés sorrendje a legnagyobbtól a legkisebbig: hét száma, hónap napja, hétköznap, óra és perc.

A következő táblázat részletesen ismerteti a schedule elemeit:

| JSON-név | Description | Érvényes értékek |
|:--- |:--- |:--- |
| **perc** |A feladatot futtató óra percben. |Egész számok tömbje. |
| **óra** |A nap azon órája, amelyben a feladatot futtatják. |Egész számok tömbje. |
| **weekDays** |A hét azon napjai, amelyeken a feladatok futnak. Csak heti gyakorisággal adható meg. |A következő értékek bármelyikének tömbje (a tömb maximális mérete 7):<br />-"Hétfő"<br />– "Kedd"<br />-"Szerda"<br />-"Csütörtök"<br />– "Péntek"<br />-"Szombat"<br />-"Vasárnap"<br /><br />Nem megkülönbözteti a kis-és nagybetűket. |
| **monthlyOccurrences** |Meghatározza, hogy a hónap mely napjai futnak a feladatokban. Csak havi gyakorisággal adható meg. |**MonthlyOccurrences** objektumok tömbje:<br /> `{ "day": day, "occurrence": occurrence}`<br /><br /> a **nap** a feladatot futtató hét napja. A *{vasárnap}* például a hónap minden vasárnapján. Kötelező.<br /><br />az **előfordulás** a nap előfordulása a hónapban. A hónap utolsó vasárnapján például a következő:  *{vasárnap,-1}* . Választható. |
| **monthDays** |A hónap azon napja, amelyen a feladatok futnak. Csak havi gyakorisággal adható meg. |A következő értékek tömbje:<br />– Bármilyen érték -1 és -31 között<br />– Bármilyen érték 1 és 31 között|

## <a name="examples-recurrence-schedules"></a>Példák: ismétlődési ütemezések

Az alábbi példák különböző ismétlődési ütemezéseket mutatnak be. A példák az Schedule objektumra és annak alelemeire összpontosítanak.

Ezek az ütemtervek azt feltételezik, hogy az **intervallum** értéke 1\. A példák azt is feltételezik, hogy a megfelelő **gyakorisági** értékek szerepelnek az **ütemezett** értékekben. Például nem használhatja a "Day" **gyakoriságát** , és **monthDays** módosítást **ütemezhet**. Ezeket a korlátozásokat a cikk korábbi részeiben ismertetjük.

| Példa | Leírás |
|:--- |:--- |
| `{"hours":[5]}` |Minden nap 5 ÓRAKOR fut.<br /><br />A Scheduler az egyes értékeket "óra" értékben, a "Minutes" értékkel együtt, egyenként, a feladatok futtatásának minden időpontját tartalmazó listát hoz létre. |
| `{"minutes":[15], "hours":[5]}` |Minden nap 05:15-kor fut le. |
| `{"minutes":[15], "hours":[5,17]}` |Minden nap 05:15-kor és 17:15-kor fut le. |
| `{"minutes":[15,45], "hours":[5,17]}` |Minden nap 05:15-kor, 05:45-kor, 17:15-kor és 17:45-kor fut le. |
| `{"minutes":[0,15,30,45]}` |15 percenként fut le. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` |Óránként fut le.<br /><br />Ez a feladatok óránként futnak. Ha meg van adva, a percet a **kezdő időpont** értéke vezérli. Ha nincs megadva **kezdő** érték, a percet a létrehozási idő vezérli. Ha például a kezdési idő vagy a létrehozási idő (amelyik érvényes) a 12:25 PM, a feladatok a következő időpontban futnak: 00:25, 01:25, 02:25,..., 23:25.<br /><br />Az ütemterv megegyezik egy "Hour" **gyakoriságú** feladatokkal, az 1. **intervallummal** és az **ütemezett** értékkel. A különbség az, hogy ezt az ütemtervet más **gyakorisági** és **intervallum** -értékekkel is használhatja más feladatok létrehozásához. Ha például a **gyakoriság** értéke "Month", az ütemterv csak havonta egyszer fut minden nap helyett (ha a **gyakoriság** értéke "Day"). |
| `{minutes:[0]}` |Minden óra kezdetén fut le.<br /><br />Ez a feladatok óránként, de óránként (12, 1, 2 stb.) is futtathatók. Ez az ütemterv megegyezik egy "Hour" **gyakoriságú** feladattal, egy 0 perces **kezdő** értékkel, és nincs **ütemezve**, ha a gyakoriság "Day". Ha azonban a **gyakoriság** "Week" vagy "Month", az ütemterv csak egy hét vagy egy hónap egy napján hajtja végre. |
| `{"minutes":[15]}` |Minden órában 15 perccel az óra végén fut.<br /><br />Óránként fut, 00:15, 1:15, 2:15, és így tovább. 11:15 ÓRAKOR végződik. |
| `{"hours":[17], "weekDays":["saturday"]}` |Minden héten 5 ÓRAKOR, szombaton fut. |
| `{hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Minden héten hétfőn, szerdán és pénteken 5 ÓRAKOR fut. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |Minden héten hétfőn, szerdán és pénteken, 17:15-kor és 17:45-kor fut le. |
| `{"hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Minden héten hétfőn, szerdán és pénteken 5 és 5 ÓRAKOR fut. |
| `{"minutes":[15,45], "hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |Az 5:15-es, 5:45-as és 5:15-kor, 5:45 valamint a minden héten hétfőn, szerdán és pénteken is futtatható. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |Hétköznapokon 15 percenként fut le. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |A hétköznap 15 percenként, 9 és 4:45 óra között fut. |
| `{"weekDays":["sunday"]}` |Vasárnap fut a kezdési időpontban. |
| `{"weekDays":["tuesday", "thursday"]}` |A kezdési időpontban keddenként és csütörtökönként is futtatható. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` |Minden hónap 28. napján, 6 ÓRAKOR fut (feltételezve a "Month" **gyakoriságát** ). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` |A hónap utolsó napján 6 ÓRAKOR fut le.<br /><br />Ha egy hónap utolsó napján szeretné futtatni a feladatot, akkor a 28., 29., 30. vagy 31. nap helyett használja a-1 értéket. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` |Minden hónap első és utolsó napján 6 ÓRAKOR fut le. |
| `{monthDays":[1,-1]}` |Minden hónap első és utolsó napján, a kezdési időpontban fut le. |
| `{monthDays":[1,14]}` |Minden hónap első és 14. napján, a kezdési időpontban fut le. |
| `{monthDays":[2]}` |A hónap második napján fut a kezdeti időpontban. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Minden hónap első pénteken, 5 ÓRAKOR fut le. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |Minden hónap első pénteken, a kezdési időpontban fut le. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` |A hónap végétől számított harmadik pénteken, minden hónapban, a kezdési időpontban fut le. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Minden hónap első és utolsó péntekjén, 05:15-kor fut le. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |Minden hónap első és utolsó pénteken, a kezdési időpontban fut le. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` |Minden hónap ötödik pénteken, a kezdési időpontban fut le.<br /><br />Ha egy hónapban nincs ötödik péntek, a feladatot nem futtatja a rendszer. Ha azt szeretné, hogy a művelet a hónap utolsó napjának napján fusson, érdemes a-1 helyett az 5 értéket használni. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` |Minden hónap utolsó péntekjén, 15 percenként fut le. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` |Minden hónap harmadik szerdáján, 05:15-kor, 05:45-kor, 17:15-kor és 17:45-kor fut le. |

## <a name="next-steps"></a>Következő lépések

* [Az Azure Scheduler alapfogalmai, entitáshierarchiája és terminológiája](scheduler-concepts-terms.md)
* [Az Azure Scheduler REST API-jának leírása](/rest/api/scheduler)
* [Az Azure Scheduler PowerShell-parancsmagjainak leírása](scheduler-powershell-reference.md)
* [Azure Scheduler – korlátozások, alapértékek és hibakódok](scheduler-limits-defaults-errors.md)