---
title: Adatrendezési függvények a Azure Data Factory
description: A Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/16/2021
ms.openlocfilehash: f7a4041d87e00fa01ae5ae4dff0cade3b9755d31
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600937"
---
# <a name="transformation-functions-in-power-query-for-data-wrangling"></a>Átalakítási függvények Power Query adatrendezéshez

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A Azure Data Factory adatrendezés lehetővé teszi kódmentes agilis adat-előkészítést és -rendszerezést felhőméretben, Power Query szkriptek lefordítása Adatfolyam ```M``` szkriptekbe. Az ADF integrálható az [Power Query Online-sal,](/powerquery-m/power-query-m-reference) és Power Query-függvényeket az adatfolyam Spark-infrastruktúráját használó Spark-végrehajtáson keresztüli adatrendezéshez. ```M``` 

> [!NOTE]
> Power Query ADF-ben való használat jelenleg a nyilvános előzetes verzióban érhető el

Jelenleg nem minden Power Query M-függvény támogatott az adatrendezéshez annak ellenére, hogy a szerzői feladatok elérhetők. Az adategyesítések kiépítése során a rendszer a következő hibaüzenetet fogja kérni, ha egy függvény nem támogatott:

`UserQuery : Expression.Error: The transformation logic is not supported as it requires dynamic access to rows of data, which cannot be scaled out.`

Az alábbi lista az M Power Query támogatott függvényeket tartalmazza.

## <a name="column-management"></a>Oszlopkezelés

* Kiválasztás: [Table.SelectColumns](/powerquery-m/table-selectcolumns)
* Eltávolítás: [Table.RemoveColumns](/powerquery-m/table-removecolumns)
* Átnevezés: [Table.RenameColumns,](/powerquery-m/table-renamecolumns) [Table.PrefixColumns,](/powerquery-m/table-prefixcolumns) [Table.TransformColumnNames](/powerquery-m/table-transformcolumnnames)
* Átrendezés: [Table.ReorderColumns](/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>Sorszűrés

A [Table.SelectRows](/powerquery-m/table-selectrows) M függvény használatával a következő feltételekre szűrhet:

* Egyenlőség és egyenlőtlenség
* Numerikus, szöveges és dátum-összehasonlítások (dátum/idő nem)
* Numerikus információk, például [Number.IsEven](/powerquery-m/number-iseven) / [Odd](/powerquery-m/number-iseven)
* Szöveg tartalmazottság [a Text.Contains,](/powerquery-m/text-contains) [Text.StartsWith ,](/powerquery-m/text-startswith)vagy [Text.EndsWith használatával](/powerquery-m/text-endswith)
* Dátumtartományok, beleértve az összes IsIn [Date függvényt](/powerquery-m/date-functions)) 
* Ezek kombinációi a és a vagy a feltétel használatával, vagy nem

## <a name="adding-and-transforming-columns"></a>Oszlopok hozzáadása és átalakítása

Az alábbi M-függvények oszlopokat ad hozzá vagy alakít át: [Table.AddColumn,](/powerquery-m/table-addcolumn) [Table.TransformColumns,](/powerquery-m/table-transformcolumns) [Table.ReplaceValue](/powerquery-m/table-replacevalue), [Table.DuplicateColumn](/powerquery-m/table-duplicatecolumn). Az alábbiakban a támogatott átalakítási függvények olvashatók.

* Numerikus aritmetika
* Szöveg össze concatenációja
* Dátum és idő Aritmetikai (aritmetikai operátorok, [Date.AddDays](/powerquery-m/date-adddays), [Date.AddMonths](/powerquery-m/date-addmonths), [Date.AddQuarters](/powerquery-m/date-addquarters), [Date.AddWeeks](/powerquery-m/date-addweeks), [Date.AddYears](/powerquery-m/date-addyears))
* Az időtartamok használhatók dátum és idő aritmetikára, de a fogadóba való írás előtt át kell alakítani őket egy másik típusba (aritmetikai operátorok, [#duration](/powerquery-m/sharpduration), [Duration.Days , Duration.Hours](/powerquery-m/duration-days), [Duration.Minutes](/powerquery-m/duration-hours), [Duration.Seconds](/powerquery-m/duration-minutes), [Duration.TotalDays , Duration.TotalHours](/powerquery-m/duration-totaldays), [Duration.TotalMinutes](/powerquery-m/duration-totalminutes), [Duration.TotalSeconds](/powerquery-m/duration-totalseconds)) [](/powerquery-m/duration-seconds) [](/powerquery-m/duration-totalhours)    
* A legtöbb szabványos, tudományos és trigonometriai numerikus függvény [](/powerquery-m/number-functions#rounding)(a [Műveletek,](/powerquery-m/number-functions#operations)Kerekítés és [Trigonometry](/powerquery-m/number-functions#trigonometry) függvények minden függvénye a Number.Factorial, Number.Permutations és Number.Combinations kivételével) 
* Csere ([Replacer.ReplaceText](/powerquery-m/replacer-replacetext), [Replacer.ReplaceValue,](/powerquery-m/replacer-replacevalue) [Text.Replace](/powerquery-m/text-replace), [Text.Remove](/powerquery-m/text-remove))
* Pozíciós szöveg kinyerése ([Text.PositionOf,](/powerquery-m/text-positionof) [Text.Length,](/powerquery-m/text-length) [Text.Start,](/powerquery-m/text-start) [Text.End,](/powerquery-m/text-end) [Text.Middle,](/powerquery-m/text-middle) [Text.ReplaceRange,](/powerquery-m/text-replacerange) [Text.RemoveRange](/powerquery-m/text-removerange))
* Alapszintű szövegformázás ([Text.Lower,](/powerquery-m/text-lower) [Text.Upper,](/powerquery-m/text-upper) [Text.Trim](/powerquery-m/text-trim) / [Start](/powerquery-m/text-trimstart) / [End,](/powerquery-m/text-trimend) [Text.PadStart](/powerquery-m/text-padstart) / [End,](/powerquery-m/text-padend) [Text.Reverse](/powerquery-m/text-reverse))
* Dátum/idő függvények ([Date.Day](/powerquery-m/date-day), [Date.Month](/powerquery-m/date-month), [Date.Year](/powerquery-m/date-year) [Time.Hour](/powerquery-m/time-hour), [Time.Minute](/powerquery-m/time-minute), [Time.Second](/powerquery-m/time-second), [Date.DayOfWeek](/powerquery-m/date-dayofweek), [Date.DayOfYear](/powerquery-m/date-dayofyear), [Date.DaysInMonth](/powerquery-m/date-daysinmonth))
* Ha a kifejezések (de az ágaknak egyező típusokkal kell)
* Sorszűrők logikai oszlopként
* Szám, szöveg, logikai, dátum és dátum/idő konstansok

<a name="mergingjoining-tables"></a>Táblák egyesítése/összefésülése
----------------------
* Power Query egy beágyazott illesztés (Table.NestedJoin; a felhasználók manuálisan is megírhatjak a [Table.AddJoinColumn függvényt).](/powerquery-m/table-addjoincolumn)
    A felhasználóknak ezután ki kell bontanunk a beágyazott illesztés oszlopot egy nem beágyazott illesztésbe (Table.ExpandTableColumn, más környezetben nem támogatott).
* A   [Table.Join](/powerquery-m/table-join) M függvény közvetlenül megírható, hogy ne legyen szükség további bővítési lépésre, de a felhasználónak biztosítania kell, hogy ne legyen ismétlődő oszlopnév az összekapcsolt táblák között
* Támogatott illesztés-változatok:   [Inner](/powerquery-m/joinkind-inner),   [LeftOuter,](/powerquery-m/joinkind-leftouter)   [RightOuter,](/powerquery-m/joinkind-rightouter)   [FullOuter](/powerquery-m/joinkind-fullouter)
* A   [Value.Equals és](/powerquery-m/value-equals)   [a Value.NullableEquals](/powerquery-m/value-nullableequals) is támogatott kulcs egyenlőségi összehasonlítóként

## <a name="group-by"></a>Csoportosítási szempont

Értékek [összesítése a Table.Group](/powerquery-m/table-group) használatával.
* Összesítő függvényekkel együtt kell használni
* Támogatott aggregátációs függvények:   [List.Sum,](/powerquery-m/list-sum)   [List.Count,](/powerquery-m/list-count)   [List.Average,](/powerquery-m/list-average)   [List.Min,](/powerquery-m/list-min)   [List.Max,](/powerquery-m/list-max)   [List.StandardDeviation,](/powerquery-m/list-standarddeviation)   [List.First,](/powerquery-m/list-first)   [List.Last](/powerquery-m/list-last)

## <a name="sorting"></a>Rendezés

Értékek [rendezése a Table.Sort](/powerquery-m/table-sort) használatával.

## <a name="reducing-rows"></a>Sorok számának csökkentése

Keep and Remove Top, Keep Range (corresponding M functions, only supporting counts, not conditions: [Table.FirstN,](/powerquery-m/table-firstn) [Table.Skip](/powerquery-m/table-skip), [Table.RemoveFirstN,](/powerquery-m/table-removefirstn) [Table.Range](/powerquery-m/table-range), [Table.MinN](/powerquery-m/table-minn), [Table.MaxN](/powerquery-m/table-maxn))

## <a name="known-unsupported-functions"></a>Ismert nem támogatott függvények

| Függvény | Állapot |
| -- | -- |
| Table.PromoteHeaders | Nem támogatott. Ugyanez az eredmény az "Első sor fejlécként" beállítással érhető el az adatkészletben. |
| Table.CombineColumns | Ez egy gyakori forgatókönyv, amely közvetlenül nem támogatott, de egy új oszlop hozzáadásával érhető el, amely két adott oszlopot egyesít.  Például: Table.AddColumn(RemoveEmailColumn, "Name", each [FirstName] & " " & [LastName]) |
| Table.TransformColumnTypes | Ez a legtöbb esetben támogatott. A következő forgatókönyvek nem támogatottak: sztring átalakítása pénznem típusúvá, sztring átalakítása időtípusra, sztring átalakítása Százalék típusra. |
| Table.NestedJoin | Az illesztés csak érvényesítési hibát eredményez. Az oszlopokat ki kell bontani, hogy működjön. |
| Table.Distinct | Az ismétlődő sorok eltávolítása nem támogatott. |
| Table.RemoveLastN | Az utolsó sorok eltávolítása nem támogatott. |
| Table.RowCount | Nem támogatott, de úgy érhető el, ha hozzáad egy egyéni oszlopot, amely az 1 értéket tartalmazza, majd az oszlopot a List.Sum értékkel összesítő oszlopot adja hozzá. A Table.Group támogatott. | 
| Sorszintű hibakezelés | A sorszintű hibakezelés jelenleg nem támogatott. A nem numerikus értékek oszlopból való kiszűrését például az egyik módszer a szöveges oszlop számra való átalakítása. Az átalakítást sikertelenül el nem hozó cellák hibás állapotban lesznek, és szűrni kell őket. Ez a forgatókönyv horizontálisan felskálált M esetén nem lehetséges. |
| Table.Transpose | Nem támogatott |
| Table.Pivot | Nem támogatott |
| Table.SplitColumn | Részlegesen támogatott |

## <a name="m-script-workarounds"></a>M-szkript áthidaló megoldásai

### <a name="for-splitcolumn-there-is-an-alternate-for-split-by-length-and-by-position"></a>A helyett a felosztás hossz és pozíció ```SplitColumn``` szerint van megosztva.

* Table.AddColumn(Source, "First characters", each Text.Start([Email], 7), type text)
* Table.AddColumn(#"Inserted first characters", "Text range", each Text.Middle([Email], 4, 9), type text)

Ez a lehetőség a menüszalag Kinyerás lehetőségével érhető el

![Power Query hozzáadása](media/wrangling-data-flow/pq-split.png)

### <a name="for-tablecombinecolumns"></a>A ```Table.CombineColumns```

* Table.AddColumn(RemoveEmailColumn, "Name", each [FirstName] & " " & [LastName])


## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan hozhat létre [adatrendezési Power Query az ADF-ben.](wrangling-tutorial.md)
