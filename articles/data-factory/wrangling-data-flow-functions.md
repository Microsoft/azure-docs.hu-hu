---
title: Huzavona adatfolyam-átalakítási függvények Azure Data Factory
description: A Azure Data Factory elérhető huzavona-adatáramlási funkcióinak áttekintése
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 3ee7761d43710e0833eb8002851e286ce5449983
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636119"
---
# <a name="transformation-functions-in-wrangling-data-flow"></a>Transzformációs függvények a huzavona-adatfolyamban

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A Azure Data Factory huzavona-adatfolyama lehetővé teszi a kód nélküli agilis adatelőkészítést és huzavona a Felhőbeli méretezéssel. A huzavona-adatfolyam integrálható [Power Query online](/powerquery-m/power-query-m-reference) -nal, és a Spark-végrehajtással elérhetővé teszi a Power Query M funkciókat az adatok huzavona. 

> [!NOTE]
> A huzavona-adatfolyam jelenleg nyilvános előzetes verzióban avilable

Jelenleg nem minden Power Query M függvény támogatott az adatok huzavona, annak ellenére, hogy a létrehozás során elérhetővé válik. A huzavona-adatfolyamatok létrehozásakor a rendszer a következő hibaüzenetet fogja kérni, ha egy függvény nem támogatott:

`The wrangling data flow is invalid. Expression.Error: The transformation logic isn't supported. Please try a simpler expression`

Az alábbi lista a támogatott Power Query M függvények listáját tartalmazza.

## <a name="column-management"></a>Oszlopok kezelése

* Kiválasztás: [table. SelectColumns](/powerquery-m/table-selectcolumns)
* Eltávolítás: [table. RemoveColumns](/powerquery-m/table-removecolumns)
* Átnevezés: [table. RenameColumns](/powerquery-m/table-renamecolumns), [table. PrefixColumns](/powerquery-m/table-prefixcolumns), [table. TransformColumnNames](/powerquery-m/table-transformcolumnnames)
* Átrendezés: [table. ReorderColumns](/powerquery-m/table-reordercolumns)

## <a name="row-filtering"></a>Sorok szűrése

Az M Function [table. SelectRows](/powerquery-m/table-selectrows) használatával szűrheti a következő feltételeket:

* Egyenlőség és egyenlőtlenség
* Numerikus, szöveges és dátum típusú összehasonlítások (de nem DateTime)
* Numerikus adatok, például [Number. IsEven](/powerquery-m/number-iseven) / [páratlan](/powerquery-m/number-iseven)
* Szöveg [szöveggel](/powerquery-m/text-contains)történő elszigetelése. tartalmaz, [Text. StartsWith](/powerquery-m/text-startswith)vagy [Text. EndsWith](/powerquery-m/text-endswith)
* Dátumtartomány, beleértve az összes "IsIn' [Date functions" függvényt](/powerquery-m/date-functions) 
* Ezek kombinációja a és a, a vagy a feltételekkel

## <a name="adding-and-transforming-columns"></a>Oszlopok hozzáadása és átalakítása

A következő M függvény oszlopok hozzáadása vagy átalakítása: [table. AddColumn](/powerquery-m/table-addcolumn), [table. TransformColumns](/powerquery-m/table-transformcolumns), [table. ReplaceValue](/powerquery-m/table-replacevalue), [table. DuplicateColumn](/powerquery-m/table-duplicatecolumn). Alább láthatók a támogatott transzformációs függvények.

* Numerikus aritmetika
* Szöveg összefűzése
* Dátum andTime aritmetika (aritmetikai operátorok, [Date. AddDays](/powerquery-m/date-adddays), [Date. AddMonths](/powerquery-m/date-addmonths), [Date. AddQuarters](/powerquery-m/date-addquarters), [Date. AddWeeks](/powerquery-m/date-addweeks), [Date. AddYears](/powerquery-m/date-addyears))
* Az időtartamok használhatók a dátum és idő aritmetikaához, de a fogadóba való írás előtt egy másik típusba kell alakítani, mielőtt beírják őket (aritmetikai operátorok, [#duration](/powerquery-m/sharpduration), [időtartam. nap](/powerquery-m/duration-days), [időtartam. óra](/powerquery-m/duration-hours), [időtartam. perc](/powerquery-m/duration-minutes), [időtartam. másodperc](/powerquery-m/duration-seconds), [időtartam. TotalDays](/powerquery-m/duration-totaldays), [időtartam. TotalHours](/powerquery-m/duration-totalhours), [időtartam. összesperc](/powerquery-m/duration-totalminutes), [időtartam. összesmásodperc](/powerquery-m/duration-totalseconds))    
* A standard, a tudományos és a trigonometriai numerikus függvények (az összes [művelet](/powerquery-m/number-functions#operations), [kerekítés](/powerquery-m/number-functions#rounding)és [trigonometria](/powerquery-m/number-functions#trigonometry) , *kivéve* a Number. faktoriálisát, Number. permutációs és Number. kombinációkat)
* Pótlás ([áthelyező. ReplaceText](/powerquery-m/replacer-replacetext), [áthelyező. ReplaceValue](/powerquery-m/replacer-replacevalue), [szöveg. csere](/powerquery-m/text-replace), [szöveg. Eltávolítás](/powerquery-m/text-remove))
* Elhelyezési szöveg kibontása ([Text. PositionOf](/powerquery-m/text-positionof), [Text. length](/powerquery-m/text-length), [Text. Start](/powerquery-m/text-start), [Text. End](/powerquery-m/text-end), [Text. Middle](/powerquery-m/text-middle), [Text. ReplaceRange](/powerquery-m/text-replacerange), [Text. RemoveRange](/powerquery-m/text-removerange))
* Alapszintű szövegformázás ([Text. Lower](/powerquery-m/text-lower), [Text. Upper](/powerquery-m/text-upper), [Text. Trim](/powerquery-m/text-trim) / [kezdő](/powerquery-m/text-trimstart) / [vége](/powerquery-m/text-trimend), [szöveg. PadStart](/powerquery-m/text-padstart) / [vége](/powerquery-m/text-padend), szöveg [. fordított](/powerquery-m/text-reverse))
* Dátum-és időfüggvények (dátum[. nap](/powerquery-m/date-day), [dátum. hónap](/powerquery-m/date-month), [dátum. év](/powerquery-m/date-year) [idő. óra](/powerquery-m/time-hour), [idő. perc](/powerquery-m/time-minute), [idő. másodperc](/powerquery-m/time-second), [dátum. DayOfWeek](/powerquery-m/date-dayofweek), [Date. DayOfYear](/powerquery-m/date-dayofyear), [Date. DaysInMonth](/powerquery-m/date-daysinmonth))
* Ha a kifejezések (de az ágaknak egyező típusúnak kell lenniük)
* Sorok szűrése logikai oszlopként
* Szám, szöveg, logikai, dátum és DateTime állandó

<a name="mergingjoining-tables"></a>Egyesítés/csatlakozás táblákhoz
----------------------
* Power Query egy beágyazott illesztést fog létrehozni (Table. NestedJoin; a felhasználók manuálisan is írhatnak [table. AddJoinColumn](/powerquery-m/table-addjoincolumn)).
    A felhasználóknak ezt követően ki kell bontaniuk a beágyazott illesztési oszlopot egy nem beágyazott illesztésbe (Table. ExpandTableColumn, más kontextusban nem támogatott).
* Az M Function   [tábla. az JOIN](/powerquery-m/table-join) közvetlenül is írható, így elkerülhető, hogy további bővítési lépésre van szükség, de a felhasználónak biztosítania kell, hogy ne legyenek duplikált oszlopnevek az összekapcsolt táblák között.
* Támogatott illesztési típus:   [Inner](/powerquery-m/joinkind-inner),   [LeftOuter](/powerquery-m/joinkind-leftouter),   [RightOuter](/powerquery-m/joinkind-rightouter),   [FullOuter](/powerquery-m/joinkind-fullouter)
* A   [Value. Equals](/powerquery-m/value-equals) és az   [Value. NullableEquals](/powerquery-m/value-nullableequals) egyaránt támogatott a kulcsfontosságú esélyegyenlőségi Összehasonlításként

## <a name="group-by"></a>Csoportosítási szempont

A [table. Group](/powerquery-m/table-group) használatával összesítheti az értékeket.
* Összesítési függvénnyel kell használni
* Támogatott aggregációs függvények:   [table. sorszám](/powerquery-m/table-rowcount),   [List. SZUM](/powerquery-m/list-sum),   [lista. Count](/powerquery-m/list-count),   [Listázás. Average](/powerquery-m/list-average),   [lista. min](/powerquery-m/list-min),   [lista. max](/powerquery-m/list-max),   [List. StandardDeviation](/powerquery-m/list-standarddeviation),   [Listázás. First](/powerquery-m/list-first),   [Listázás. Last](/powerquery-m/list-last)

## <a name="sorting"></a>Rendezés

Az értékek rendezéséhez használja a [table. sort](/powerquery-m/table-sort) .

## <a name="reducing-rows"></a>Sorok csökkentése

Tartsa meg és távolítsa el a Top, Keep Range (megfelelő M függvények, csak a támogatási számok, nem feltételek: [table. FirstN](/powerquery-m/table-firstn), [table. skip](/powerquery-m/table-skip), [table. RemoveFirstN](/powerquery-m/table-removefirstn), [table. Range](/powerquery-m/table-range), table. [MinN](/powerquery-m/table-minn), [table. MaxN](/powerquery-m/table-maxn))

## <a name="known-unsupported-functions"></a>Ismert nem támogatott függvények

| Függvény | status |
| -- | -- |
| Table.PromoteHeaders | Nem támogatott. Ugyanezt az eredményt az "első sor fejlécként" beállítással lehet megvalósítani az adatkészletben. |
| Table.CombineColumns | Ez egy olyan gyakori forgatókönyv, amely közvetlenül nem támogatott, de úgy érhető el, hogy hozzáad egy új oszlopot, amely két adott oszlopot ÖSSZEFŰZ.  Például: table. AddColumn (RemoveEmailColumn, "Name", each [FirstName] & "" & [LastName]) |
| Table.TransformColumnTypes | Ez a legtöbb esetben támogatott. A következő forgatókönyvek nem támogatottak: a sztring átalakítása a pénznem típusára, a karakterlánc átalakítása időtípusra, a sztring átalakítása százalék típusúra. |
| Table.NestedJoin | Az illesztés végrehajtása egy érvényesítési hibát eredményez. Az oszlopokat ki kell bontani ahhoz, hogy működjön. |
| Table.Distinct | Az ismétlődő sorok eltávolítása nem támogatott. |
| Table.RemoveLastN | Az alsó sorok eltávolítása nem támogatott. |
| Table.RowCount | Nem támogatott, de a hozzá tartozó oszlop hozzáadásával az összes cella üres (feltétel oszlop használható), majd a Group By utasítás használatával. A table. Group támogatott. | 
| Sorok szintjének hibakezelés | A sorok szintjének hibakezelés jelenleg nem támogatott. Ha például egy oszlopból nem numerikus értékeket szeretne kiszűrni, az egyik módszer az, hogy a Text (szöveg) oszlopot egy számra alakítsa át. Minden olyan cella, amely nem alakítható át, hibás állapotba kerül, és szűrni kell. Ez a forgatókönyv nem lehetséges a huzavona-adatfolyamban. |
| Table.Transpose | Nem támogatott |
| Table.Pivot | Nem támogatott |

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan [hozhat létre huzavona-adatfolyamokat](wrangling-data-flow-tutorial.md).