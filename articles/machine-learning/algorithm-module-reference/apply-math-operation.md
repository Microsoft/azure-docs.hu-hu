---
title: Matematikai művelet alkalmazása
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan használható a matematikai műveleti modul alkalmazása a Azure Machine Learningban egy matematikai művelet a folyamat oszlopainak értékeire való alkalmazásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: cc2afb2b29c69e9687dbd3b194b6a1626d4131b3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105625194"
---
# <a name="apply-math-operation"></a>Matematikai művelet alkalmazása

Ez a cikk a Azure Machine Learning Designer modulját ismerteti.

Használja a matematikai művelet alkalmazása lehetőséget a bemeneti adatkészlet numerikus oszlopaira alkalmazott számítások létrehozásához. 

A matematikai műveletek közé tartoznak az aritmetikai függvények, a trigonometriai függvények, a kerekítési függvények, valamint az adatelemzésekben használt speciális függvények, például a gamma és a Error függvények.  

A művelet definiálása és a folyamat futtatása után a rendszer hozzáadja az értékeket az adatkészlethez. A modul konfigurálásának módjától függően a következőket teheti:

+ Fűzze hozzá az eredményeket az adatkészlethez (ez a művelet eredményének ellenőrzésekor hasznos).
+ Cserélje le az oszlopok értékeit az új, számított értékekre.
+ Új oszlop létrehozása az eredményekhez, és nem jeleníti meg az eredeti adatmennyiséget. 

Keresse meg a szükséges műveletet ezekben a kategóriákban:  

- [Basic](#basic-math-operations)  
  
     Az **alapszintű** kategóriába tartozó függvények egyetlen érték vagy oszlop értékének módosítására használhatók. Előfordulhat például, hogy egy adott oszlopban az összes szám abszolút értékét kapja meg, vagy kiszámítja az egyes értékek négyzetes gyökerét egy adott oszlopban.  
  
-   [Összehasonlítás](#comparison-operations)  
  
      Az **összehasonlítás kategóriába tartozó függvények** mindegyike az összehasonlításhoz használatos: a két oszlopban lévő értékek párok közötti összehasonlítását, illetve az egyes értékek adott konstanshoz való összehasonlítását is elvégezheti. Az oszlopok összehasonlításával például megállapíthatja, hogy az értékek megegyeznek-e két adatkészletben. Vagy használhat állandót is, például a maximálisan megengedett értéket, hogy kiugró értékeket keressen egy numerikus oszlopban.  
  
-   [Műveletek](#arithmetic-operations)  
  
     Az **operatív** kategória alapszintű matematikai függvényeket tartalmaz: összeadás, kivonás, szorzás és osztás. Akár oszlopokkal, akár állandókkal is dolgozhat. Például hozzáadhatja az A oszlop értékét a B oszlop értékéhez. Vagy előfordulhat, hogy ki kell vonni egy állandót, például egy korábban számított középértéket az A oszlop minden értékéből.  
  
-   [Kerekítés](#rounding-operations)  
  
     A **kerekítési** kategória számos olyan függvényt tartalmaz, amelyek különböző szintű pontosságú műveleteket hajtanak végre, például a kerekítést, a plafont, a padlót és a csonkítás mértékét. Megadhatja a pontosság szintjét a decimális és egész számok esetében is.  
  
-   [Speciális](#special-math-functions)  
  
     A **speciális** kategória olyan matematikai függvényeket tartalmaz, amelyeket különösen az adatelemzés, például az elliptikus és a Gauss-hiba funkció használ.  
  
-   [Trigonometrikus](#trigonometric-functions)  
  
     A **trigonometriai** kategória tartalmazza az összes szabványos trigonometriai függvényt. Átalakíthatja például a radián-t fok-ra, vagy a számítási függvényeket, például az tangenset radiánban vagy fokban.
     Ezek a függvények egyoperandusú, ami azt jelenti, hogy az értékek egy oszlopát adja meg bemenetként, alkalmazza a trigonometriai függvényt, és az értékek oszlopát adja vissza eredményként. Győződjön meg arról, hogy a bemeneti oszlop a megfelelő típusú, és a megadott műveletnek megfelelő típusú értékeket tartalmaz.   

## <a name="how-to-configure-apply-math-operation"></a>A matematikai műveletek alkalmazásának konfigurálása  

A **matematikai művelet alkalmazása** modulhoz olyan adatkészlet szükséges, amely legalább egy olyan oszlopot tartalmaz, amely csak számokat tartalmaz. A számok lehetnek különállóak vagy folyamatosak, de numerikus adattípusnak kell lennie, nem sztringnek.

Ugyanezt a műveletet több numerikus oszlopra is alkalmazhatja, de az összes oszlopnak ugyanabban az adatkészletben kell lennie. 

A modul minden példánya egyszerre csak egy típusú műveletet tud végrehajtani. Összetett matematikai műveletek elvégzéséhez előfordulhat, hogy a **matematikai műveleti modul alkalmazása** több példányát is össze kell láncolva.  
  
1.  Adja hozzá a **matematikai műveleti modul alkalmazása** a folyamathoz lehetőséget.

1. Csatlakoztasson egy olyan adatkészletet, amely legalább egy numerikus oszlopot tartalmaz.  

1.  Válasszon ki egy vagy több olyan oszlopot, amelyen végre szeretné hajtani a számítást.   
  
    - Bármely kiválasztott oszlopnak numerikus adattípusnak kell lennie. 
    - Az adattartománynak érvényesnek kell lennie a kiválasztott matematikai művelethez. Ellenkező esetben hiba vagy NaN (nem szám) eredmény jelenhet meg. Például az ln (-1,0) érvénytelen művelet, és a következő értéket eredményezi: `NaN` .
  
1.  Válassza ki a **kategóriát** a végrehajtandó matematikai művelet **típusának** kiválasztásához.
    
1. Válasszon egy adott műveletet a Kategória listából.
  
1.  Adja meg az egyes műveletek által igényelt további paramétereket.  
  
1.  A **kimeneti mód** beállítással jelezze, hogyan kívánja létrehozni a matematikai műveletet: 

    - **Hozzáfűzés**. Az összes bemenetként használt oszlop szerepel a kimeneti adatkészletben, valamint egy további oszlop is, amely a matematikai művelet eredményét tartalmazza.
    - **Helyben**. A bemenetként használt oszlopokban szereplő értékeket az új számított értékekkel helyettesíti a rendszer. 
    - **ResultOnly**. A rendszer egyetlen oszlopot ad vissza, amely a matematikai művelet eredményét tartalmazza.
  
1.  A folyamat elküldése.  
  
## <a name="results"></a>Results (Eredmények)

Ha a **Hozzáfűzés** vagy a **ResultOnly** lehetőség használatával hoz eredményt, a visszaadott adatkészlet oszlopai a művelet és a használt oszlopok láthatók. Ha például összehasonlítja két oszlopot az **EQUAL** operátor használatával, az eredmények a következőképpen néznek ki:  
  
-   **Egyenlő (Col2_Col1)**, amely azt jelzi, hogy tesztelte a Col2 a Col1.  
-   **Egyenlő (Col2_ $10)**, amely azt jelzi, hogy a 2. oszlopot összehasonlítja a konstans 10 értékkel.  

Még ha a **helyben** lehetőséget is használja, a forrásadatok nem törlődnek vagy módosulnak; az eredeti adatkészlet oszlopa továbbra is elérhető a tervezőben. Az eredeti adatokat úgy tekintheti meg, ha csatlakoztatja az [Oszlopok hozzáadása](add-columns.md) modult, és összekapcsolja a **matematikai művelet alkalmazása** kimenetével.  
    
## <a name="basic-math-operations"></a>Alapszintű Matematikai műveletek 

Az **alapszintű** kategóriában lévő függvények általában egyetlen értéket foglalnak egy oszlopból, végrehajtják az előre meghatározott műveletet, és egyetlen értéket adnak vissza. Egyes függvények esetében megadhat egy állandót vagy egy oszlopot, amely második argumentumként van beállítva.  
  
 A Azure Machine Learning az **alapszintű** kategóriában a következő függvényeket támogatja:  

### <a name="abs"></a>Abs

A kijelölt oszlopok abszolút értékét adja vissza.  
  
### <a name="atan2"></a>Atan2

Egy négy íves, inverz tangenset ad vissza.  

Válassza ki a pont koordinátáit tartalmazó oszlopokat. A második argumentumhoz, amely az x-koordináta értéknek felel meg, állandó is megadható.  

Megfelel a ATAN2 függvénynek a MATLAB-ben.  

### <a name="conj"></a>Conj

A kijelölt oszlopban lévő értékek konjugáltját adja vissza.  

### <a name="cuberoot"></a>CubeRoot

Kiszámítja a kocka gyökerét a kijelölt oszlopban lévő értékeknél.  

### <a name="doublefactorial"></a>DoubleFactorial  
 Kiszámítja a kijelölt oszlopban lévő értékek dupla faktoriálisát. A Double faktoriálisát a normál faktoriálisát-függvény kiterjesztése, amely a következőképpen van kijelölve: `x!!` .  

### <a name="eps"></a>EPS

Az aktuális érték és a következő legmagasabb, kétszeres pontosságú szám közötti hézag méretét adja vissza. Megfelel a MATLAB EPS-függvényének.  
  
### <a name="exp"></a>Exp

Az e értéket adja vissza a kijelölt oszlopban lévő érték hatványára. Ez a függvény megegyezik az Excel EXP függvénnyel.  

### <a name="exp2"></a>Exp2

Az argumentumok alap-2 exponenciális értékét adja vissza, amely az y = x * 2<sup>t</sup> , ahol a t a kitevőket tartalmazó értékek oszlopa.  

Az  **oszlop beállítása** területen válassza ki azt az oszlopot, amely a kitevő értékeit tartalmazza (t).

A **Exp2** esetében megadhat egy második argumentum x értéket, amely lehet konstans vagy más oszlop is. A **második argumentum típusa mezőben** adja meg, hogy az x szorzót állandóként vagy egy oszlopban lévő értékként adja meg.  

Ha például kijelöl egy oszlopot a {0,1,2,3,4,5} szorzó és a kitevő értékével együtt, a függvény {0, 2, 8, 24, 64 160) értéket ad vissza.  

### <a name="expminus1"></a>ExpMinus1 

A kijelölt oszlopban szereplő értékek negatív kitevőjét adja vissza.  

### <a name="factorial"></a>Faktoriális
A kijelölt oszlopban lévő értékek faktoriálisát adja vissza.  

### <a name="hypotenuse"></a>Átfogó
Kiszámítja egy olyan háromszög átfogó, amelyben az egyik oldalon az értékek oszlopa van megadva, a második oldal hossza pedig állandó vagy két oszlopként van megadva.  

### <a name="ln"></a>Természetes logaritmus

A kijelölt oszlopban lévő értékek természetes alapú logaritmusát adja vissza.  

### <a name="lnplus1"></a>LnPlus1

A természetes logaritmust, valamint a kijelölt oszlopban lévő értékek egyikét adja vissza.  

### <a name="log"></a>Napló

A kijelölt oszlopban szereplő értékek naplóját adja vissza a megadott alap alapján.  

Megadhatja az alapot (a második argumentumot) állandóként, vagy egy másik oszlop értékének kiválasztásával.  

### <a name="log10"></a>Log10

A kijelölt oszlop 10-es alapú logaritmusának értékét adja vissza.  

### <a name="log2"></a>Log2

A kijelölt oszlophoz tartozó 2. alap értéket adja vissza.  

### <a name="nthroot"></a>NthRoot
Az érték n/s gyökerét adja vissza, amely a megadott n értéket használja.  

Válassza ki azokat az oszlopokat, amelyekhez a gyökeret szeretné kiszámítani a **ColumnSet** beállítás használatával.  

A **második argumentum típusa** mezőben válasszon egy másik oszlopot, amely tartalmazza a gyökeret, vagy adjon meg egy állandót, amelyet gyökérként kíván használni.  

Ha a második argumentum egy oszlop, a rendszer az oszlopban szereplő összes értéket használja az n értékként a megfelelő sorhoz. Ha a második argumentum állandó, írja be az n értéket a **második argumentum** szövegmezőbe.
### <a name="pow"></a>Pow

Kiszámítja az X értékét a kijelölt oszlop egyes értékeinek Y teljesítményére.  

Először válassza ki azokat az oszlopokat, amelyek a **ColumnSet** beállítás használatával lebegőpontos **alapot** tartalmaznak.  

A **második argumentum típusa** mezőben válassza ki a kitevőt tartalmazó oszlopot, vagy adjon meg egy konstanst, amelyet kitevőként kíván használni.  

Ha a második argumentum egy oszlop, a rendszer az oszlopban szereplő összes értéket használja a megfelelő sor kitevője. Ha a második argumentum állandó, írja be a kitevő értékét a **második argumentum** szövegmezőbe.  

### <a name="sqrt"></a>Sqrt

A kijelölt oszlopban lévő értékek négyzet gyökerét adja vissza.  

### <a name="sqrtpi"></a>SqrtPi

A kijelölt oszlopban lévő minden értéknél szorozza meg az értéket PI-vel, majd az eredmény négyzet gyökerét adja vissza.  

### <a name="square"></a>Square

A kijelölt oszlopban lévő értékek négyzeteit jelöli.  

## <a name="comparison-operations"></a>Összehasonlítási műveletek  

A Azure Machine Learning Designerben használja az összehasonlító függvényeket bármikor, amikor két érték tesztelését kell végezni egymásra. Például egy folyamat esetében szükség lehet az alábbi összehasonlítási műveletek elvégzésére:  

- Értékelje ki a valószínűség pontszám-modell oszlopát egy küszöbérték alapján.
- Állapítsa meg, hogy az eredmények két készlete azonos-e. Minden különböző sor esetében adjon hozzá egy hamis jelzőt, amely további feldolgozáshoz vagy szűréshez használható.  

### <a name="equalto"></a>EqualTo

Igaz értéket ad vissza, ha az értékek azonosak.  

### <a name="greaterthan"></a>GreaterThan

Igaz értéket ad eredményül, ha a **készletben** lévő értékek nagyobbak a megadott konstansnál, vagy nagyobbak, mint a megfelelő értékek az összehasonlítás oszlopban.  

### <a name="greaterthanorequalto"></a>GreaterThanOrEqualTo

Igaz értéket ad vissza, ha a **készletben** lévő értékek nagyobbak vagy egyenlőek a megadott állandóval, vagy nagyobbak vagy egyenlőek az összehasonlítás oszlopban szereplő megfelelő értékekkel.  

### <a name="lessthan"></a>LessThan

Igaz értéket ad eredményül, ha a **készletben** lévő értékek kisebbek a megadott konstansnál, vagy kisebbek, mint az összehasonlítás oszlopban lévő megfelelő értékek.  
  
### <a name="lessthanorequalto"></a>LessThanOrEqualTo

Igaz értéket ad vissza, ha a **készletben** lévő értékek értéke kisebb vagy egyenlő, mint a megadott konstans, vagy kisebb vagy egyenlő, mint az összehasonlító oszlop megfelelő értékei.  

### <a name="notequalto"></a>NotEqualTo

Igaz értéket ad vissza, ha a **készletben** lévő értékek nem egyenlőek az állandó vagy az összehasonlító oszlop értékével, és hamis értéket adnak vissza, ha egyenlőek.  

### <a name="pairmax"></a>PairMax

A nagyobb értéket adja vissza – az **oszlop** értékét vagy az állandó vagy összehasonlító oszlop értékét.  

### <a name="pairmin"></a>PairMin

A kisebb értéket adja vissza – a **készletben** lévő értéket vagy az állandó vagy összehasonlító oszlop értékét.  
  
##  <a name="arithmetic-operations"></a>Aritmetikai műveletek   

Az alapszintű aritmetikai műveleteket tartalmazza: összeadás és kivonás, osztás és szorzás.  Mivel a legtöbb művelet bináris, két számot igényel, először válassza ki a műveletet, majd válassza ki az első és második argumentumban használni kívánt oszlopot vagy számokat.

A divízió és a kivonás sorrendje a következő: 
- Kivonás (Arg1_Arg2) = Arg1-ARG 2
- Osztás (Arg1_Arg2) = Arg1/ARG 2

Az alábbi táblázat néhány példát mutat be

Művelet|Num1|Num2|Eredmény oszlop|Eredmény értéke|
----|----|----|----|----
|Összeadás|1|5|Hozzáadás (Num2_Num1)| 6|
|Szorzás|1|5|Több (Num2_Num1)|5|
|Kivonás|5|1|Kivonás (Num2_Num1)|4|
|Kivonás|0|1|Kivonás (Num2_Num1)|-1|
|Részleg|5|1|Osztás (Num2_Num1)|5|
|Részleg|1|0|Osztás (Num2_Num1)|Végtelen|

### <a name="add"></a>Hozzáadás

Adja meg a forrás oszlopokat a **set oszlop** használatával, majd adja hozzá azokat az értékeket a **második argumentumban** megadott számú értékhez.  

Ha két oszlopban szeretné felvenni az értékeket, válasszon ki egy oszlopot vagy oszlopot az **oszlop készlet** használatával, majd válasszon egy második oszlopot a **második argumentum** használatával.  

### <a name="divide"></a>Osztás

Az **oszlop** értékeit állandó vagy a **második argumentumban** definiált oszlopok értékei alapján osztja el.  Más szóval először az osztót kell kiválasztani, majd az osztalékot. A kimeneti érték a hányados.

### <a name="multiply"></a>Szorzás

A megadott konstans vagy oszlop értékeit tartalmazó oszlop értékeinek szorzatát **adja meg** .  

### <a name="subtract"></a>Kivonás

Adja meg a működéshez használni kívánt értékek oszlopát (a *kisebbítendőt*) egy másik oszlop kiválasztásával az **oszlop beállítása** beállítással. Ezután adja meg a kivonni kívánt számot (a *kivonandó*) a **második argumentum** legördülő lista használatával. Kiválaszthatja az értékek állandó vagy oszlopát is.

##  <a name="rounding-operations"></a>Kerekítési műveletek 

A Azure Machine Learning Designer számos kerekítési műveletet támogat. Számos művelet esetében meg kell adnia a kerekítéskor használandó pontosság mértékét. Használhat állandóként megadott statikus pontossági szintet, vagy az értékek oszlopaiból beszerzett dinamikus pontossági értéket is alkalmazhat.  

- Ha állandót használ, állítsa a **pontosság típusát** **állandó** értékre, majd írja be a számjegyek számát egész számként az **állandó pontosság** szövegmezőbe. Ha nem egész számot állít be, a modul nem eredményez hibát, de az eredmények váratlanok lehetnek.  

- Ha más pontossági értéket szeretne használni az adatkészlet minden sorához, állítsa a **pontosság típusát** **ColumnSet** értékre, majd válassza ki a megfelelő pontossági értékeket tartalmazó oszlopot.  

### <a name="ceiling"></a>Ceiling

Az **oszlop** értékeinek felső határát adja vissza.  

### <a name="ceilingpower2"></a>CeilingPower2

Az **oszlop** értékeinek négyzetes felső határát adja vissza.  

### <a name="floor"></a>Floor

A megadott pontosságú **oszlop** értékeinek a padlóját adja vissza.  

### <a name="mod"></a>Mod

A megadott pontosságú **oszlop** értékeinek tört részét adja vissza.  

### <a name="quotient"></a>Hányados

A megadott pontosságú **oszlop** értékeinek tört részét adja vissza.  

### <a name="remainder"></a>Maradék

Az **oszlop** értékeinek maradékát adja vissza.  

### <a name="rounddigits"></a>RoundDigits

A megadott számú számjegyre kerekítve adja vissza az **oszlop** értékeit az 4/5-szabály alapján.  

### <a name="rounddown"></a>RoundDown

A **beállított oszlopban** szereplő értékeket adja vissza, a megadott számú számjegyre kerekítve.  

### <a name="roundup"></a>RoundUp

A **beállított oszlopban** szereplő értékeket adja vissza, a megadott számú számjegyre kerekítve.  

### <a name="toeven"></a>ToEven

A **beállított oszlopban** szereplő értékeket adja vissza, a legközelebbi egész számra kerekítve.  

### <a name="toodd"></a>ToOdd

A **beállított oszlopban** szereplő értékeket adja vissza, a legközelebbi egész számra kerekítve.  

### <a name="truncate"></a>Truncate

A megadott pontosság által nem engedélyezett összes számjegy eltávolításával csonkolja az **oszlop** értékeit.  
  
## <a name="special-math-functions"></a>Speciális matematikai függvények

Ez a kategória olyan speciális matematikai függvényeket tartalmaz, amelyek gyakran használják az adatelemzést. Ha másként nincs jelezve, a függvény egyoperandusú, és a kijelölt oszlopban vagy oszlopokban szereplő értékek esetében a megadott számítást adja vissza.  

### <a name="beta"></a>Beta

A Euler Beta függvényének értékét adja vissza.  

### <a name="ellipticintegrale"></a>EllipticIntegralE
A nem teljes elliptikus szerves érték értékét adja vissza.  
  

### <a name="ellipticintegralk"></a>EllipticIntegralK

A teljes elliptikus szerves (K) értékének beolvasása.  

### <a name="erf"></a>ERF

A Error függvény értékét adja vissza.  

A Error függvény (más néven a Gauss Error függvény) a szigmabél alakzat egy speciális funkciója, amely a diffúzió leírására szolgál.  

### <a name="erfc"></a>ERFC

A kiegészítő hiba funkció értékét adja vissza.  

`Erfc` 1 – EMA (x) értékkel van definiálva.  

### <a name="erfscaled"></a>ErfScaled

A méretezett hiba függvény értékét adja vissza.  

A Error függvény méretezett verziója használható az aritmetikai folyamat elkerüléséhez.  

### <a name="erfinverse"></a>ErfInverse

Az inverz függvény értékét adja vissza `erf` .  

### <a name="exponentialintegralein"></a>ExponentialIntegralEin

Az exponenciális szerves ei értékét adja vissza.  

### <a name="gamma"></a>Gamma

A gamma függvény értékét adja vissza.  

### <a name="gammaln"></a>GammaLn

A gamma függvény természetes alapú logaritmusát adja vissza.  

### <a name="gammaregularizedp"></a>GammaRegularizedP

A szabályos, hiányos gamma függvény értékét adja vissza.  

Ez a függvény egy második argumentumot vesz igénybe, amely akár állandóként, akár értékek oszlopaiként is megadható.  

### <a name="gammaregularizedpinverse"></a>GammaRegularizedPInverse

Az inverz, szabályos, hiányos gamma függvény értékét adja vissza.  

Ez a függvény egy második argumentumot vesz igénybe, amely akár állandóként, akár értékek oszlopaiként is megadható.  

### <a name="gammaregularizedq"></a>GammaRegularizedQ  

A szabályos, hiányos gamma függvény értékét adja vissza.  

Ez a függvény egy második argumentumot vesz igénybe, amely akár állandóként, akár értékek oszlopaiként is megadható.  

### <a name="gammaregularizedqinverse"></a>GammaRegularizedQInverse

Az inverz általánosított, hiányos gamma-függvény értékét adja vissza.

Ez a függvény egy második argumentumot vesz igénybe, amely akár állandóként, akár értékek oszlopaiként is megadható.  

### <a name="polygamma"></a>Multigamma

A multigamma függvény értékét adja vissza.  

Ez a függvény egy második argumentumot vesz igénybe, amely akár állandóként, akár értékek oszlopaiként is megadható.

##  <a name="trigonometric-functions"></a>Trigonometriai függvények 

Ez a kategória a legfontosabb trigonometriai és inverz trigonometriai függvények többségét iIncludes. Minden trigonometriai függvény egyoperandusú, és nem igényel további argumentumokat.  

### <a name="acos"></a>Acos

Kiszámítja a szám arkusz koszinuszát az oszlop értékeihez.  

### <a name="acosdegree"></a>AcosDegree

Kiszámítja az oszlopok értékeinek szám arkusz koszinuszát fokban megadva.  

### <a name="acosh"></a>ACOSH

Kiszámítja az oszlop értékeinek hiperbolikus szám arkusz koszinuszát.  

### <a name="acot"></a>Acot

Kiszámítja az oszlopok értékeinek kotangensének sajátértékét.  

### <a name="acotdegrees"></a>AcotDegrees

Kiszámítja az oszlopok értékeinek kotangensének sajátértékét fokban megadva.  

### <a name="acoth"></a>Acoth

Kiszámítja az oszlop értékeinek hiperbolikus kotangensének sajátértékét.  

### <a name="acsc"></a>ASCs

Kiszámítja az oszlopok értékeinek arccosecant.  

### <a name="acscdegrees"></a>AcscDegrees

Kiszámítja az oszlopok értékeinek arccosecant fokban megadva.  
### <a name="asec"></a>ASec

Kiszámítja az oszlopok értékeinek arcsecant.  

### <a name="asecdegrees"></a>AsecDegrees

Kiszámítja az oszlopok értékeinek arcsecant fokban megadva.  

### <a name="asech"></a>Asech

Kiszámítja az oszlop értékeinek hiperbolikus arcsecant.  

### <a name="asin"></a>Asin

Kiszámítja az oszlopok értékeinek arkusz szinuszát.  

### <a name="asindegrees"></a>AsinDegrees

Kiszámítja az oszlop értékeinek arkusz szinuszát fokban megadva.  

### <a name="asinh"></a>ASINH

Kiszámítja az oszlop értékeinek szinusz hiperbolikus értékét.  

### <a name="atan"></a>Atan

Kiszámítja az oszlopok értékeinek tangensét.  

### <a name="atandegrees"></a>AtanDegrees

Kiszámítja az oszlop értékeinek tangensét fokban megadva.  

### <a name="atanh"></a>ATANH

Kiszámítja az oszlop értékeinek hiperbolikus tangensét.  

### <a name="cos"></a>Cos

Kiszámítja az oszlopok értékeinek koszinuszát.  

### <a name="cosdegrees"></a>CosDegrees

Kiszámítja az oszlop értékeinek koszinuszát fokban megadva.  

### <a name="cosh"></a>Cosh

Kiszámítja az oszlopok értékeinek koszinusz hiperbolikusát.  

### <a name="cot"></a>Cot

Kiszámítja az oszlop értékeinek tangensét.  

### <a name="cotdegrees"></a>CotDegrees

Kiszámítja az oszlop értékeit fokban megadva.  

### <a name="coth"></a>Coth
Kiszámítja az oszlop értékeinek hiperbolikus tangensét.  

### <a name="csc"></a>CSC

Kiszámítja a koszekáns az oszlop értékeihez.  

### <a name="cscdegrees"></a>CscDegrees

Kiszámítja az oszlopok értékeinek koszekáns fokban megadva.  

### <a name="csch"></a>Csch

Kiszámítja az oszlop értékeinek hiperbolikus koszekáns.  

### <a name="degreestoradians"></a>DegreesToRadians

Radiánná konvertálja a fokban megadott értéket.  

### <a name="sec"></a>SEC

Kiszámítja az oszlopok értékeinek szekáns.  

### <a name="asecdegrees"></a>aSecDegrees

Kiszámítja az oszlopok értékeinek szekáns fokban megadva.  

### <a name="asech"></a>aSech

Kiszámítja az oszlop értékeinek hiperbolikus szekáns.  

### <a name="sign"></a>Előjel

Az oszlop értékeinek előjelét adja vissza.  

### <a name="sin"></a>Sin

Kiszámítja az oszlop értékeinek szinuszát.  

### <a name="sinc"></a>Sinc

Kiszámítja az oszlop értékeinek szinusz-koszinusz értékét.  

### <a name="sindegrees"></a>SinDegrees

Kiszámítja az oszlop értékeinek szinuszát fokban megadva.  

### <a name="sinh"></a>Sinh

Kiszámítja az oszlopok értékeinek szinusz hiperbolikusát.  

### <a name="tan"></a>Tan

Kiszámítja az oszlop értékeinek tangensét.  

### <a name="tandegrees"></a>TanDegrees

Kiszámítja az argumentum tangensét fokban megadva.  

### <a name="tanh"></a>Tanh

Kiszámítja az oszlopok értékeinek tangensét.  
  
## <a name="technical-notes"></a>Technikai megjegyzések

Legyen körültekintő, ha egynél több oszlopot választ ki második operátorként. Az eredmények könnyen megérthetők, ha a művelet egyszerű, például állandó hozzáadása az összes oszlophoz. 

Tegyük fel, hogy az adatkészlet több oszloppal rendelkezik, és saját maga adja hozzá az adatkészletet. Az eredményekben az egyes oszlopok hozzáadása magához az alábbiak szerint történik:  
  
|Num1|Num2|Num3|Hozzáadás (Num1_Num1)|Hozzáadás (Num2_Num2)|Hozzáadás (Num3_Num3)|
|----|----|----|----|----|----|
|1|5|2|2|10|4|
|2|3|-1|4|6|-2|
|0|1|-1|0|2|-2|

Ha összetettebb számításokra van szüksége, több, a **matematikai műveletek alkalmazására** szolgáló példányt is láncba foglalhat. Felvehet például két oszlopot a **matematikai művelet alkalmazása** egyik példányának használatával, majd a **matematikai művelet** egy másik példányával oszthatja meg az összeget állandó értékkel a középérték beszerzéséhez.  
  
Azt is megteheti, hogy az alábbi modulok egyikével végrehajtja az összes számítást az SQL, az R vagy a Python parancsfájl használatával:
 
+ [R-szkript végrehajtása](execute-r-script.md)
+ [Python-szkript futtatása](execute-python-script.md)
+ [SQL-átalakítás alkalmazása](apply-sql-transformation.md)   
  
## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 
