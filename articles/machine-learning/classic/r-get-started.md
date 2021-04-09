---
title: Az R használata Machine Learning Studio (klasszikus) használatával – Azure
description: Ezzel az R programozási oktatóanyaggal megkezdheti a Azure Machine Learning Studio (klasszikus) használatának első lépéseit az R-ben az előrejelzési megoldás létrehozásához.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2019
ms.openlocfilehash: c619b51f9323477bda4f1ec99aeeb1bfa01028fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "100517739"
---
# <a name="get-started-with-azure-machine-learning-studio-classic-in-r"></a>Első lépések az Azure Machine Learning Studio (klasszikus) az R-ben

**a következőkre vonatkozik:** ![ Ez egy pipa, ami azt jelenti, hogy ez a cikk a Machine Learning Studio (klasszikus) elemre vonatkozik. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasszikus) ![ Ez egy X, ami azt jelenti, hogy ez a cikk Azure Machine learningre vonatkozik.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

<!-- Stephen F Elston, Ph.D. -->
Ebből az oktatóanyagból megtudhatja, hogyan használhatja a Azure Machine Learning Studio (klasszikus) t az R-kód létrehozásához, teszteléséhez és végrehajtásához. A végén egy teljes előrejelző megoldás fog megjelenni.

> [!div class="checklist"]
> * Kód létrehozása az adattisztításhoz és az átalakításhoz.
> * Elemezze az adatkészlet több változója közötti korrelációkat.
> * Hozzon létre egy szezonális idősorozat-előrejelzési modellt a tejtermékek termeléséhez.


A Machine Learning Studio (klasszikus) számos hatékony gépi tanulási és adatkezelési modult tartalmaz. Az R programozási nyelvvel ez a kombináció a Machine Learning Studio (klasszikus) méretezhetőségét és egyszerű üzembe helyezését teszi lehetővé az R rugalmasságával és mélyreható elemzésével.

Az előrejelzés egy széles körben alkalmazott és hasznos analitikai módszer. Az általános felhasználási tartomány a szezonális elemek előrejelzése és az optimális leltározási szintek meghatározása a makrogazdasági változók előrejelzéséhez. Az előrejelzés általában idősorozat-modellekkel történik. Az idősorozat-adatok olyan adatok, amelyekben az értékek egy időindextel rendelkeznek. Az idő indexe lehet rendszeres, például havonta vagy percenként. Az időindex is szabálytalan lehet. Az idősorozat-modell az idősorozat-adatsorokon alapul. Az R programozási nyelv rugalmas keretrendszert és átfogó elemzést tartalmaz az idősorozat-adatokat illetően.

## <a name="get-the-data"></a>Az adatok lekérése

Ebben az oktatóanyagban a kaliforniai tejtermék-termelési és díjszabási adatokat használja, amely havi információkat tartalmaz számos tejtermék előállításáról és a tejzsír árának kialakulásáról, amely egy teljesítményteszt-termék.

A cikkben használt adatok, valamint az R-parancsfájlok is letölthetők a [MachineLearningSamples-Notebooks/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples)-ből. A fájlban lévő adatokat `cadairydata.csv` eredetileg a Wisconsin [Dairy Markets webhelyéről](https://dairymarkets.com)származó információk alapján állították elő.

## <a name="interact-with-r-language-in-machine-learning-studio-classic"></a><a id="mlstudio"></a>Az R nyelvvel való interakció Machine Learning Studio (klasszikus)

Ez a szakasz végigvezeti az R programozási nyelv Machine Learning Studio (klasszikus) környezetben való interakciójának alapjain. Az R nyelv hatékony eszközt biztosít a testreszabott elemzési és adatkezelési modulok létrehozásához a Machine Learning Studio (klasszikus) környezetben.

A RStudio használatával kis méretben fejlesztheti, tesztelheti és hibakeresést végezhet az R-kódban. Ezt a kódot ezután kivágja és beilleszti egy [végrehajtási R-parancsfájlba][execute-r-script] , amely készen áll a Machine learning Studio (klasszikus) futtatására.

### <a name="the-execute-r-script-module"></a>Az R-szkript végrehajtása modul

Machine Learning Studio (klasszikus) belül az R-parancsfájlok az R- [szkript végrehajtása][execute-r-script] modulon belül futnak. Itt látható egy példa az [R szkript végrehajtása][execute-r-script] modulra Machine learning Studio (klasszikus).

 ![Az R programozási nyelvet bemutató képernyőkép: a Machine Learning Studio (klasszikus) alkalmazásban kiválasztott R-parancsfájl végrehajtása modul.](./media/r-quickstart/fig1.png)

Az előző képen a Machine Learning Studio (klasszikus) környezet néhány kulcsfontosságú része látható az [R-szkript végrehajtásához][execute-r-script] használt modul használatához:

* A kísérlet moduljai a középső ablaktáblán jelennek meg.
* A jobb oldali ablaktábla felső részén található egy ablak, amely az R-parancsfájlok megtekintésére és szerkesztésére használható.
* A jobb oldali ablaktábla alsó része az [R-szkript végrehajtásának][execute-r-script]néhány tulajdonságát mutatja. A hibaüzeneteket és a kimeneti naplókat úgy tekintheti meg, hogy kiválasztja az ablaktábla megfelelő területeit.

A cikk további részében részletesebben ismertetjük az [R-szkript végrehajtását][execute-r-script] .

Ha összetett R-függvényekkel dolgozik, javasoljuk, hogy szerkessze, tesztelje és végezzen hibakeresést a RStudio-ben. Csakúgy, mint bármely szoftverfejlesztés esetében, fokozatosan kiterjesztheti a kódot, és tesztelheti kis, egyszerű tesztelési esetekben. Ezután vágja ki és illessze be a függvényeket az R-szkript [végrehajtása][execute-r-script] modul r-szkript ablakába. Ez a módszer lehetővé teszi a RStudio integrált fejlesztési környezet (IDE) és a Machine Learning Studio (klasszikus) teljesítményének kihasználását.

#### <a name="execute-r-code"></a>R-kód végrehajtása

Az [r-szkript végrehajtása][execute-r-script] modulban az r-kód a **Futtatás** gomb kiválasztásával fog futni a kísérlet futtatásakor. A végrehajtás befejezése után egy pipa jelenik meg az R- [szkript végrehajtása][execute-r-script] ikonon.

#### <a name="defensive-r-coding-for-machine-learning-studio-classic"></a>Védelmi R-kódolás a Machine Learning Studio (klasszikus)

Ha R-kódot fejleszt, például egy webszolgáltatást Machine Learning Studio (klasszikus) használatával, érdemes megterveznie, hogyan kezelje a kód váratlan adatbevitelt és kivételeket. Az egyértelműség érdekében nem vesszük sok lehetőséget a példákban szereplő programkódok többségének ellenőrzéséhez vagy kivételek kezeléséhez. A folytatáshoz több példát is biztosítunk a függvények számára az R kivétel-kezelési képességének használatával.

Ha az R-kivételek kezelésének átfogóbb kezelésére van szüksége, olvassa el a könyv megfelelő részeit a [További olvasnivalók](#appendixb)Wickham listájában.

#### <a name="debug-and-test-r-in-machine-learning-studio-classic"></a>Hibakeresés és tesztelés az R-ben Machine Learning Studio (klasszikus)

Az R-kód tesztelése és hibakeresése egy kis méretben a RStudio-ben. Vannak olyan esetek is, amikor le kell követnie az R-kód problémáit az [r-szkript végrehajtásakor][execute-r-script] . Emellett érdemes megnézni az eredményeket Machine Learning Studio (klasszikus).

Az R-kód és a Machine Learning Studio (klasszikus) platform végrehajtásának kimenete elsődlegesen a kimenet. log naplófájlban található. Néhány további információ a error. log naplófájlban található.

Ha az R-kód futtatása közben Machine Learning Studio (klasszikus) hiba történik, az első lépés a következő: error. log. Ez a fájl hasznos hibaüzeneteket tartalmazhat, amelyek segítenek megérteni és elhárítani a hibát. A hiba. log naplófájl megtekintéséhez válassza a hibanapló **megtekintése** elemet a hibát tartalmazó [végrehajtási R-parancsfájl][execute-r-script] tulajdonságok paneljén.

Például a következő R-kódot futtatta egy nem definiált változó y értékkel egy [Execute R script][execute-r-script] modulban.

```r
x <- 1.0
z <- x + y
```

Ez a kód nem hajtható végre, ami egy hiba feltételét eredményezi. Ha a Tulajdonságok ablaktáblán a **hibanapló megtekintése** elemre kattint, a következő jelenik meg.

  ![Az előugró hibaüzenetet megjelenítő képernyőkép.](./media/r-quickstart/fig2.png)

Úgy tűnik, hogy az R-hibaüzenet megjelenítéséhez meg kell keresnie a kimenet. log naplófájlt. Válassza az [R-parancsfájl végrehajtása][execute-r-script] modult, majd a Tulajdonságok ablaktáblán kattintson a **kimenet megtekintése** elemre a jobb oldalon. Megnyílik egy új böngészőablak, és megjelenik a következő hibaüzenet.

```output
[Critical]     Error: Error 0063: The following error occurred during evaluation of R script:
---------- Start of error message from R ----------
object 'y' not found


object 'y' not found
----------- End of error message from R -----------
```

Ez a hibaüzenet nem tartalmaz meglepetéseket, és egyértelműen azonosítja a problémát.

Az R-ben található bármely objektum értékének vizsgálatához ezeket az értékeket a kimeneti. log fájlba is kinyomtathatja. Az objektumok értékeinek vizsgálatára szolgáló szabályok lényegében megegyeznek az interaktív R-munkamenetekben. Ha például egy változó nevét adja meg egy sorban, az objektum értéke a kimeneti. log fájlba lesz kinyomtatva.

#### <a name="packages-in-machine-learning-studio-classic"></a>Csomagok Machine Learning Studio (klasszikus)

A Machine Learning Studio (klasszikus) több mint 350 előre telepített R nyelvi csomagot tartalmaz. Az előtelepített csomagok listájának beolvasásához használja a következő kódot az [R-parancsfájl végrehajtása][execute-r-script] modulban.

```r
data.set <- data.frame(installed.packages())
maml.mapOutputPort("data.set")
```

Ha jelenleg nem érti a kód utolsó sorát, olvassa el a következőt:. A cikk további részében részletesen tárgyaljuk az R használatát a Machine Learning Studio (klasszikus) környezetben.

### <a name="introduction-to-rstudio"></a>A RStudio bemutatása

A RStudio egy széles körben használt IDE az R-hez. Az útmutatóban használt R-kód némelyikének szerkesztéséhez, teszteléséhez és hibakereséséhez a RStudio-t fogjuk használni. Az R-kód tesztelése és elkészítése után a RStudio-szerkesztőből kivágással és beillesztéssel egy Machine Learning Studio (klasszikus) [r script][execute-r-script] modult használhat.

Ha nincs telepítve az R programozási nyelv az asztali gépen, tegye meg most. A nyílt forráskódú R nyelv ingyenes letöltése a [teljes r Archive Network (Cran)](https://www.r-project.org/)nyelven érhető el. A letölthető fájlok Windows, macOS és Linux/UNIX rendszereken érhetők el. Válasszon egy közeli tükrözést, és kövesse a letöltési utasításokat. Emellett a CRAN számos hasznos elemzési és adatkezelési csomagot tartalmaz.

Ha most ismerkedik a RStudio, töltse le és telepítse az asztali verziót. A Windows, macOS és Linux/UNIX rendszerhez készült RStudio-letöltések a következő címen találhatók: [RStudio](http://www.rstudio.com/products/RStudio/). Kövesse a RStudio telepítéséhez szükséges utasításokat az asztali gépen.

A RStudio oktatóanyagának bemutatása [a RSTUDIO ide használatával](https://support.rstudio.com/hc/sections/200107586-Using-RStudio)érhető el.

A RStudio használatával kapcsolatos további információkért tekintse meg a [RStudio dokumentációjának útmutatóját](#appendixa).

## <a name="get-data-in-and-out-of-the-execute-r-script-module"></a><a id="scriptmodule"></a>Az R-szkript végrehajtása modulba beolvasott és kívüli adatlekérdezés

Ebben a szakaszban azt mutatjuk be, hogy az [R-szkriptek végrehajtásának][execute-r-script] folyamata milyen módon jelenik meg. Azt is megvizsgáljuk, hogyan kezelheti a különböző adattípusokat az [R-szkriptek végrehajtása][execute-r-script] modulból és onnan.

A szakasz teljes kódja a [MachineLearningSamples-notebook/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="load-and-check-data"></a>Az adatterhelés és-ellenőrzési

#### <a name="load-the-dataset"></a><a id="loading"></a>Az adatkészlet betöltése

Először töltse be a **csdairydata.csv** -fájlt Machine learning Studio (klasszikus) fájlba.

1. Indítsa el a Machine Learning Studio (klasszikus) környezetet.
1. Válassza az **+ új** lehetőséget a képernyő bal alsó részén, és válassza az **adatkészlet** lehetőséget.
1. Válasszon a **helyi fájlból**, majd válassza a **Tallózás** lehetőséget a fájl kiválasztásához.
1. Ügyeljen arra, hogy az adatkészlethez tartozó **általános CSV-fájlt (fejléc (. csv)** adja meg.
1. Jelölje be a pipa.
1. Az adatkészlet feltöltése után az új adatkészlet jelenik meg, amikor kiválasztja az **adatkészletek** lapot.

#### <a name="create-an-experiment"></a>Kísérlet létrehozása

Most, hogy Machine Learning Studio (klasszikus) adataink vannak, létre kell hozni egy kísérletet az elemzés elvégzéséhez.  

1. Válassza az **+ új** lehetőséget a képernyő bal alsó részén, és válassza a **kísérlet**  >  **üres kísérlet** lehetőséget.
1. A kísérletet a lap tetején található címmel **létrehozott kísérlet** kiválasztásával és módosításával nevezheti ki. Módosítsa például a **CA Dairy Analysis** értékre.
1. A kísérlet lap bal oldalán válassza a **mentett adatkészletek**  >  **saját adatkészletek** lehetőséget. Ekkor meg kell jelennie a korábban feltöltött **cadairydata.csv** fájlnak.
1. Húzzon **csdairydata.csv adatkészletet** a kísérletre.
1. A bal oldali ablaktábla felső részén található **kísérleti kísérletek keresése** mezőben írja be az [R-szkript végrehajtása][execute-r-script]elemet. A modul megjelenik a keresési listán.
1. Húzza az [R szkript végrehajtása][execute-r-script] modult a raklapra.
1. **csdairydata.csv adatkészlet** kimenetének csatlakoztatása a [végrehajtási R-szkript][execute-r-script]bal szélső bemenetéhez (**DataSet1 elemet**).
1. Kattintson a **Mentés** gombra.

Ezen a ponton a kísérletnek ehhez a példához hasonlóan kell kinéznie.

![Diagram, amely megjeleníti a CA Dairy Analysis kísérletet az adatkészlet és az R-parancsfájl végrehajtása modulban.](./media/r-quickstart/fig3.png)


#### <a name="check-on-the-data"></a>Az adatkeresés

Tekintsük át a kísérletbe betöltött adatgyűjtést. A kísérletben válassza ki a **cadairydata.csv adatkészlet** kimenetét, és válassza a **Megjelenítés** lehetőséget. Ehhez az összegzéshez hasonlóan kell megjelennie.

![A cadairydata.csv adatkészlet összegzését bemutató képernyőkép.](./media/r-quickstart/fig4.png)

Ez a nézet sok hasznos információt mutat be. Az adatkészlet első néhány sorát láthatjuk. Ha kiválasztunk egy oszlopot, a **statisztikai** szakasz további információkat jelenít meg az oszlopról. A **szolgáltatás típusa** sorban például látható, hogy milyen adattípusok vannak hozzárendelve az oszlophoz Machine learning Studio (klasszikus). Tekintse át ezt a nézetet, mielőtt elkezdené a komoly munkát.

### <a name="first-r-script"></a>Első R-szkript

Hozzunk létre egy egyszerű, első R-szkriptet, amely Machine Learning Studio (klasszikus) belül kísérletezni próbál. A következő szkriptet hoztuk létre és teszteltük a RStudio-ben.

```r
## Only one of the following two lines should be used
## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
## If in RStudio, use the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
str(cadairydata)
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('cadairydata')
```

Most át kell másolnia ezt a szkriptet Machine Learning Studio (klasszikus). Kivágással és beillesztéssel, de ebben az esetben a zip-fájl használatával vigye át az R-szkriptet.

### <a name="data-input-to-the-execute-r-script-module"></a>Adatok bevitele az R-parancsfájl végrehajtása modulba

Tekintse át az [R szkript végrehajtása][execute-r-script] modul bemeneteit. Ebben a példában a California Dairy-adatbázisokat az [R szkript végrehajtása][execute-r-script] modulba fogjuk beolvasni.

Az [R-szkript végrehajtása][execute-r-script] modulnak három lehetséges bemenete van. Az alkalmazástól függően a bemenetek bármelyike vagy mindegyike használható. Olyan R-szkriptet is használhat, amely egyáltalán nem vesz fel bemenetet.

Nézzük meg ezeket a bemeneteket, balról jobbra haladva. Az egyes bemenetek nevét megtekintheti, ha az egérmutatót a bemenet fölé helyezi, és beolvassa az elemleírást.

#### <a name="script-bundle"></a>Parancsfájl-csomag

A parancsfájl-csomag bemenete lehetővé teszi egy zip-fájl tartalmának átadását az [R-parancsfájl végrehajtása][execute-r-script] modulba. Az alábbi parancsok egyikével beolvashatja a zip-fájl tartalmát az R-kódjába.

```r
source("src/yourfile.R") # Reads a zipped R script
load("src/yourData.rdata") # Reads a zipped R data file
```

> [!NOTE]
> A Machine Learning Studio (klasszikus) a zip-fájlban található fájlokat úgy kezeli, mintha az src/könyvtárban vannak, ezért a fájlneveket előtaggal kell elvégeznie. Ha például a zip-fájl tartalmazza a fájlokat `yourfile.R` és a `yourData.rdata` zip-fájl gyökerét, akkor a és a `src/yourfile.R` `src/yourData.rdata` használatakor a `source` és a `load` .

Már beszéltünk az adathalmaz [betöltési](#loading)adatkészletének betöltéséről. Miután létrehozta és tesztelte az előző szakaszban bemutatott R-szkriptet, hajtsa végre a következő lépéseket.

1. Mentse az R-szkriptet a-ba. R-fájl. Meghívjuk a parancsfájl **simpleplot. R**. Itt a fájlban:

   ```r
   ## Only one of the following two lines should be used
   ## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
   ## If in RStudio, use the second line with read.csv()
   cadairydata <- maml.mapInputPort(1)
   # cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
   str(cadairydata)
   pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
   ## The following line should be executed only when running in
   ## Azure Machine Learning Studio (classic)
   maml.mapOutputPort('cadairydata')
   ```

1. Hozzon létre egy zip-fájlt, és másolja a szkriptet ebbe a zip-fájlba. A Windowsban kattintson a jobb gombbal a fájlra, és válassza a **Küldés**  >  **tömörített mappába** lehetőséget. Ez a művelet létrehoz egy új zip-fájlt, amely tartalmazza a **simpleplot. R** -fájl.

1. Adja hozzá a fájlt a Machine Learning Studio (klasszikus) **adatkészletekhez** , és adja meg a típust **zip**-ként. Ekkor megjelenik a zip-fájl az adatkészletekben.

1. Húzza a zip-fájlt az **adatkészletek** rajzsablonról a **ml Studio (klasszikus) vászonra**.

1. A **zip-adatok** ikon kimenetének összekötése az [R-parancsfájl végrehajtása][execute-r-script] modul **parancsfájl-kötegbeli** bemenetével.

1. Adja meg a `source()` zip-fájl nevét tartalmazó függvényt az [R-szkript végrehajtása][execute-r-script] modul kódjának ablakában. Ebben az esetben a következőt írtunk be: `source("src/simpleplot.R")` .

1. Kattintson a **Mentés** gombra.

A lépések elvégzése után az [r szkript végrehajtása][execute-r-script] modul végrehajtja az r-szkriptet a zip-fájlban a kísérlet futtatásakor. Ezen a ponton a kísérletnek ehhez a példához hasonlóan kell kinéznie.

![Egy, a tömörített R-parancsfájlt használó kísérletet bemutató diagram.](./media/r-quickstart/fig6.png)

#### <a name="dataset1"></a>DataSet1 elemet

A Dataset1 elemet bemenet használatával átadhat egy téglalap alakú adattábla adatait az R-kódra. Az egyszerű szkriptben a `maml.mapInputPort(1)` függvény az 1. portból olvassa be az adatokat. Ezt követően a rendszer a kódban egy dataframe-változóhoz rendeli hozzá ezeket az adattípusokat. Az egyszerű szkriptben a kód első sora hajtja végre a hozzárendelést.

```r
cadairydata <- maml.mapInputPort(1)
```

A kísérlet végrehajtásához kattintson a **Futtatás** gombra. A végrehajtás befejeződése után válassza az R- [parancsfájl végrehajtása][execute-r-script] modult, majd válassza a Tulajdonságok ablaktábla **kimeneti napló megtekintése** elemét. Egy új oldalnak kell megjelennie a böngészőben, amely a kimeneti. log fájl tartalmát mutatja. Ha lefelé görget, a következő kimenethez hasonlóan kell megjelennie.

```output
[ModuleOutput] InputDataStructure
[ModuleOutput]
[ModuleOutput] {
[ModuleOutput]  "InputName":Dataset1
[ModuleOutput]  "Rows":228
[ModuleOutput]  "Cols":9
[ModuleOutput]  "ColumnTypes":System.Int32,3,System.Double,5,System.String,1
[ModuleOutput] }
```

A lap részletesebben részletezi az oszlopokat, ami a következő kimenethez hasonlóan fog kinézni.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput]
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput]
[ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput]
[ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
[ModuleOutput]
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput]
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput]
[ModuleOutput]  $ Month            : chr  "Jan" "Feb" "Mar" "Apr" ...
[ModuleOutput]
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput]
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput]
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput]
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
```

Ezek az eredmények többnyire a vártnak megfelelően működnek, és a dataframe a 228-es és 9 oszlopot is megtekintheti. Láthatjuk az oszlopok nevét, az R-adattípust és az egyes oszlopok mintáit.

> [!NOTE]
> Ugyanez a nyomtatott kimenet kényelmesen elérhető az [r-szkript végrehajtása][execute-r-script] modul r Device kimenetében. A következő szakaszban megbeszéljük az [R szkript végrehajtása][execute-r-script] modul kimeneteit.  

#### <a name="dataset2"></a>Dataset2

A Dataset2-bemenet viselkedése megegyezik a Dataset1 elemet. Ennek a bemenetnek a használatával egy második téglalap alakú táblázatot adhat át az R-kódjába. A függvény a `maml.mapInputPort(2)` 2. argumentummal továbbítja ezeket az adatfájlokat.  

### <a name="execute-r-script-outputs"></a>R-szkriptek kimenetének végrehajtása

#### <a name="output-a-dataframe"></a>Dataframe kimenete

Az R-dataframe tartalmát téglalap alakú táblázatként is kiválaszthatja az eredményül kapott Dataset1 elemet-porton keresztül a `maml.mapOutputPort()` függvény használatával. Az egyszerű R-szkriptben ezt a műveletet a következő sor hajtja végre.

```r
maml.mapOutputPort('cadairydata')
```

A kísérlet futtatása után válassza ki az eredmény Dataset1 elemet kimeneti portot, majd válassza a **Megjelenítés** lehetőséget. Ehhez a példához hasonlóan kell megjelennie.

![Képernyőkép, amely a kaliforniai tejtermékek eredményeinek megjelenítését jeleníti meg.](./media/r-quickstart/fig7.png)

Ez a kimenet megegyezik a bemenettel, pontosan a várt módon.

### <a name="r-device-output"></a>R-eszköz kimenete

Az [R-szkript végrehajtása][execute-r-script] modul kimenete üzenetekkel és grafikus kimenettel rendelkezik. Az R szabványos kimeneti és standard hibaüzeneteket küld az R-eszköz kimeneti portjára.

Az R-eszköz kimenetének megtekintéséhez válassza ki a portot, majd válassza a **Megjelenítés** lehetőséget. Az R-szkript standard kimeneti és standard hibáját itt találja.

![Képernyőkép, amely az R-eszköz portjának standard kimenetét és standard hibáját mutatja.](./media/r-quickstart/fig8.png)

Lefelé görgetve láthatjuk az R-szkriptek grafikus kimenetét.

![Képernyőkép, amely az R-eszköz portjának grafikus kimenetét jeleníti meg.](./media/r-quickstart/fig9.png)

## <a name="data-filtering-and-transformation"></a><a id="filtering"></a>Adatszűrés és-átalakítás

Ebben a szakaszban néhány alapszintű adatszűrési és-átalakítási műveletet végzünk a kaliforniai tejtermékek adatain. Ennek a szakasznak a végére egy analitikai modell létrehozásához megfelelő formátumú adatformátummal rendelkezünk.

Pontosabban, ebben a szakaszban számos gyakori adattisztítási és-átalakítási feladatot végzünk: átalakítás, szűrés a dataframes, új számított oszlopok hozzáadása és értékek átalakítása. Ez a háttér segíthet a valós problémákkal kapcsolatos számos változat kezelésében.

A szakasz teljes R-kódja a [MachineLearningSamples-Notebooks/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples)-ben érhető el.

### <a name="type-transformations"></a>Átalakítások típusa

Most, hogy beolvasjuk a California Dairy-beli adattárat az r-kódban az [r-szkript végrehajtása][execute-r-script] modulban, biztosítaniuk kell, hogy az oszlopokban lévő információk a kívánt típussal és formátummal legyenek elküldve.

Az R egy dinamikusan beírt nyelv, ami azt jelenti, hogy az adattípusokat az egyikről a másikra kell kényszeríteni. Az R-beli atomi adattípusok a következők: numerikus, logikai és karakter. A faktor típusa a kategorikus adattárolási művelet tömörítésére szolgál. Az adattípusokkal kapcsolatos további információkért tekintse meg a hivatkozásokat a [további olvasmányokban](#appendixb).

Ha a táblázatos adatokat egy külső forrásból olvassa be az R-be, mindig érdemes megtekinteni az eredményül kapott típusokat az oszlopokban. Előfordulhat, hogy egy karakter típusú oszlopot szeretne használni, de sok esetben az oszlop faktorként jelenik meg, vagy fordítva. Más esetekben előfordulhat, hogy egy olyan oszlopot, amelyet úgy gondol, hogy a karakteres adat, például "1,23", nem pedig 1,23 lebegőpontos szám.

Szerencsére az egyik típust könnyedén át lehet alakítani egy másikra, amíg a leképezés lehetséges. Például a "Nevada" nem alakítható át numerikus értékké, de a faktor (kategorikus változó) értékre is konvertálható. Egy másik példaként átalakíthatja a numerikus 1-től az "1" karaktert vagy egy faktort.

A konverziók szintaxisa egyszerű: `as.datatype()` . Az ilyen típusú átalakítási függvények a következő függvényeket tartalmazzák:

* `as.numeric()`
* `as.character()`
* `as.logical()`
* `as.factor()`

Az előző szakaszban bevitt oszlopok adattípusait megtekintve minden oszlop numerikus típusú. A kivétel a "Month" feliratú oszlop, amely típusú karakter. Alakítsa át ezt a típust egy tényezőre, és tesztelje az eredményeket.

Törölte azt a sort, amely létrehozta a scatter Plot mátrixot, és hozzáadta azt a sort, amely a hónap oszlopot egy tényezővé alakítja. Ebben a kísérletben kivágja és beilleszti az R-kódot az [r-szkript végrehajtása][execute-r-script] modul kódjának ablakába. Frissítheti a zip-fájlt, és feltöltheti Machine Learning Studio (klasszikus), de ez a lehetőség több lépést is igénybe vehet.

```r
## Only one of the following two lines should be used
## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
## If in RStudio, use the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(cadairydata$Month)
str(cadairydata) # Check the result
## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('cadairydata')
```

Hajtsa végre ezt a kódot, és tekintse meg az R-szkript kimeneti naplóját. Itt jelennek meg a megfelelő adatok a naplóból.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput] 
[ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 14 levels "Apr","April",..: 6 5 9 1 11 8 7 3 14 13 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```

A hónap típusának mostantól a következő tényezőt kell tartalmaznia: **w/14 szint**. Ez a típus problémát jelent, mert az év során csak 12 hónap van. Azt is megtekintheti, hogy az eredményül kapott adatkészlet-port **megjelenítésének** típusa **kategorikus**-e.

A probléma az, hogy a hónap oszlop nem lett szisztematikusan kódolva. Bizonyos esetekben egy hónapot április, mások pedig ápr rövidítenek. Ezt a problémát úgy oldhatja meg, hogy a sztringet három karakterre vágja. A kód most a következő példához hasonlóan jelenik meg.

```r
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(substr(cadairydata$Month, 1, 3))
```

Futtassa újra a kísérletet, és tekintse meg a kimeneti naplót. A várt eredmények itt láthatók.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput] 
[ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```


A faktor változó most már a kívánt 12 szinttel rendelkezik.

### <a name="basic-dataframe-filtering"></a>Alapszintű dataframe-szűrés

Az R dataframes támogatja a hatékony szűrési képességeket. Az adatkészletek kiállíthatók a logikai szűrők használatával sorokon vagy oszlopokon. Sok esetben az összetett szűrési feltételekre lesz szükség. A dataframes szűrésének részletes ismertetését a [További olvasnivalók című részben olvashatja](#appendixb).

Egy kis szűrésre van szükségünk az adatkészleten. Ha megtekinti a cadairydata dataframe lévő oszlopokat, két felesleges oszlop jelenik meg. Az első oszlop egy sorszámmal rendelkezik, ami nem nagyon hasznos. A második oszlop, év. hónap, redundáns adatokat tartalmaz. Ezeket az oszlopokat egyszerűen kizárhatja a következő R-kód használatával.

> [!NOTE]
> Az ebben a szakaszban szereplő további kódokat az [R szkript végrehajtása][execute-r-script] modulban mutatjuk be. Minden új sort a függvény *előtt* adunk hozzá `str()` . Ezt a függvényt használjuk a Machine Learning Studio (klasszikus) eredményeinek ellenőrzéséhez.

A következő sort adja hozzá az R-kódhoz az [r-szkript végrehajtása][execute-r-script] modulban.

```r
# Remove two columns we do not need
cadairydata <- cadairydata[, c(-1, -2)]
```

Futtassa ezt a kódot a kísérletben, és vizsgálja meg az eredményt a kimeneti naplóból. Ezek az eredmények itt jelennek meg.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  7 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```

Most megkapjuk a várt eredményeket.

### <a name="add-a-new-column"></a>Új oszlop hozzáadása

Az idősorozat-modellek létrehozásához érdemes lehet egy olyan oszlopot megadni, amely az idősorozat kezdete óta eltelt hónapokat tartalmazza. Létrehozjuk az új oszlop hónapját. darabszám.

A kód rendszerezésének megkönnyítéséhez hozzuk létre az első egyszerű függvényt `num.month()` . Ezután alkalmazza ezt a függvényt egy új oszlop létrehozásához a dataframe. Az új kód a következő:

```r
## Create a new column with the month count
## Function to find the number of months from the first
## month of the time series
num.month <- function(Year, Month) {
  ## Find the starting year
  min.year  <- min(Year)

  ## Compute the number of months from the start of the time series
  12 * (Year - min.year) + Month - 1
}

## Compute the new column for the dataframe
cadairydata$Month.Count <- num.month(cadairydata$Year, cadairydata$Month.Number)
```

Most futtassa a frissített kísérletet, és a kimeneti napló használatával tekintse meg az eredményeket. Ezek az eredmények itt jelennek meg.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  8 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```


Úgy tűnik, minden működik. Az új oszlop a várt értékekkel rendelkezik a dataframe.

### <a name="value-transformations"></a>Értékek átalakítása

Ebben a szakaszban néhány egyszerű átalakítást végzünk a dataframe egyes oszlopaiban lévő értékekre. Az R nyelv szinte tetszőleges értékű átalakításokat támogat. További példákért tekintse meg a hivatkozásokat a [további olvasmányokban](#appendixb).

Ha megtekinti a dataframe összefoglalóinak értékeit, itt valami furcsaat kell látnia. Több fagylaltot, mint a Kaliforniában előállított tej? Nem, természetesen nem. A probléma az, hogy az egységek eltérőek. Az ár az Egyesült államokbeli font egységében van, a tej a 1 000 000 Amerikai font egységben található, az Ice Cream pedig az 1 000-es US gallon egységben, a túró pedig 1 000 Amerikai font egységben van. Feltételezve, hogy az Ice Cream körülbelül 6,5 kilót foglal le, a szorzást egyszerűen elvégezheti az értékek konvertálásával, így azok mind a 1 000 font értékkel egyenlő egységben vannak.

Az előrejelzési modellhez multiplikatív modellt használunk az adattrendek és a szezonális beállítások számára. A naplók átalakítása lehetővé teszi, hogy lineáris modellt használjon, amely leegyszerűsíti ezt a folyamatot. A log-transzformációt ugyanabban a függvényben lehet alkalmazni, ahol a szorzót alkalmazták.

A következő kódban definiálunk egy új függvényt, `log.transform()` és azt a numerikus értékeket tartalmazó sorokra alkalmazza. Az R `Map()` függvény a `log.transform()` függvény a dataframe kijelölt oszlopaira való alkalmazására szolgál. A `Map()` függvény hasonló `apply()` , de több argumentumot is engedélyez a függvénynek. Vegye figyelembe, hogy a multiplikátorok listája a függvény második argumentumát adja meg `log.transform()` . A `na.omit()` függvény egy kis tisztítást használ annak biztosítására, hogy a dataframe ne legyenek hiányzó vagy nem definiált értékek.

```r
log.transform <- function(invec, multiplier = 1) {
  ## Function for the transformation, which is the log
  ## of the input value times a multiplier

  warningmessages <- c("ERROR: Non-numeric argument encountered in function log.transform",
                       "ERROR: Arguments to function log.transform must be greate than zero",
                       "ERROR: Aggurment multiplier to funcition log.transform must be a scaler",
                       "ERROR: Invalid time seies value encountered in function log.transform"
                       )

  ## Check the input arguments
  if(!is.numeric(invec) | !is.numeric(multiplier)) {warning(warningmessages[1]); return(NA)}  
  if(any(invec < 0.0) | any(multiplier < 0.0)) {warning(warningmessages[2]); return(NA)}
  if(length(multiplier) != 1) {{warning(warningmessages[3]); return(NA)}}

  ## Wrap the multiplication in tryCatch
  ## If there is an exception, print the warning message to
  ## standard error and return NA
  tryCatch(log(multiplier * invec),
           error = function(e){warning(warningmessages[4]); NA})
}


## Apply the transformation function to the 4 columns
## of the dataframe with production data
multipliers  <- list(1.0, 6.5, 1000.0, 1000.0)
cadairydata[, 4:7] <- Map(log.transform, cadairydata[, 4:7], multipliers)

## Get rid of any rows with NA values
cadairydata <- na.omit(cadairydata)  
```

A függvényben meglehetősen sok történik `log.transform()` . A kód nagy része az argumentumokkal kapcsolatos lehetséges problémákat ellenőrzi, vagy a kivételekkel foglalkozik, amelyek a számítások során továbbra is felmerülhetnek. Ennek a kódnak csak néhány sora végzi a számítások elvégzését.

A védekező programozás célja, hogy megakadályozza egy olyan függvény meghibásodását, amely megakadályozza a feldolgozást a folytatásból. A hosszú ideig futó elemzések hirtelen meghibásodása bosszantó lehet a felhasználók számára. A probléma elkerülése érdekében az alapértelmezett visszatérési értékeket úgy kell megválasztani, hogy az a sérülést az alsóbb rétegbeli feldolgozásra korlátozza. Egy üzenet figyelmezteti a felhasználókat, hogy valamilyen hiba történt.

Ha nem használja az R-programozást, akkor ez a kód esetleg túlterhelt. Ismerkedjen meg a főbb lépésekkel:

1. Négy üzenet vektora van definiálva. Ezek az üzenetek a kóddal megjelenő lehetséges hibákkal és kivételekkel kapcsolatos információk közlésére szolgálnak.
1. Minden esetben a NA értéket adunk vissza. Számos más lehetőség is van, amelyek kevesebb mellékhatással rendelkezhetnek. Visszatérhetünk a nulla vagy az eredeti bemeneti vektor vektorára, például:.
1. Az ellenőrzések a függvény argumentumai szerint futnak. Minden esetben, ha a rendszer hibát észlel, egy alapértelmezett értéket ad vissza, és a függvény egy üzenetet hoz létre `warning()` . `warning()`Nem `stop()` azért használjuk, mert az utóbbi leállítja a végrehajtást, ami az, amit megpróbálunk elkerülni. Ez a kód eljárási stílusban íródott, mert ebben az esetben a funkcionális megközelítés összetett és homályosnak tűnt.
1. A rendszer becsomagolja a naplózási számításokat, `tryCatch()` hogy a kivételek ne okozzák hirtelen a feldolgozást. Nélkül `tryCatch()` az R functions által generált legtöbb hiba egy leállítási jelet eredményez, amely éppen ezt teszi.

Hajtsa végre ezt az R-kódot a kísérletben, és tekintse meg a kimeneti. log fájl nyomtatott kimenetét. Ekkor megtekintheti a naplóban szereplő négy oszlop átalakított értékeit az itt látható módon.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  8 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```

Láthatjuk, hogy az értékek át lettek alakítva. A tejtermelés mostantól nagy mértékben meghaladja az összes többi tejtermék-termelést, és emlékeztet arra, hogy most már a naplózási léptéket vizsgáljuk.

Ezen a ponton a rendszer megtisztítja az adatgyűjtést, és készen áll néhány modellezésre. Ha megtekinti az eredményül szolgáló [R-parancsfájl][execute-r-script] eredményeinek adatkészletének kimenetét, akkor a hónap oszlop a 12 egyedi értékkel van kategorikusan megjelölve, amely csak a kívánt módon jelenik meg.

## <a name="time-series-objects-and-correlation-analysis"></a><a id="timeseries"></a>Idősorozat-objektumok és korrelációs elemzés

Ebben a szakaszban néhány alapszintű R idősorozat-objektumot ismertetünk, és elemezjük az egyes változók közötti összefüggéseket. Célunk egy olyan dataframe kimenete, amely a páros korrelációs információit tartalmazza több lemaradásban.

Ennek a szakasznak a teljes R-kódja a [MachineLearningSamples-notebook/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="time-series-objects-in-r"></a>Idősorozat-objektumok az R-ben

Ahogy már említettük, az idősorozat az adatértékek egy sorozata, az idő szerint indexelve. Az R idősorozat-objektumok az időindex létrehozásához és kezeléséhez használatosak. Az idősorozat-objektumok használatának számos előnye van. Az idősorozat-objektumok ingyenesen használhatók az objektumban beágyazott idősorozat-indexek kezelésének számos adatával. Emellett az idősorozat-objektumok lehetővé teszik a több idősorozat-módszer használatát a nyomtatáshoz, a nyomtatáshoz, a modellezéshez stb.

A POSIXct Time Series osztály általában használatban van, és viszonylag egyszerű. Ez az idősorozat-osztály az év elejétől, a 1970. január 1-jén méri az időt. Ebben a példában a POSIXct idősorozat-objektumokat fogjuk használni. Más, széles körben használt R idősorozat-objektumosztály például az Állatkert és a XTS (bővíthető idősorozat).

### <a name="time-series-object-example"></a>Példa idősorozat-objektumra

Ismerkedjen meg a példával. Húzzon egy új [végrehajtási R szkript][execute-r-script] modult a kísérletbe. Kapcsolódjon a meglévő [r-szkriptek][execute-r-script] eredményének DataSet1 elemet kimeneti portjához az új [végrehajtási r szkript][execute-r-script] modul DataSet1 elemet bemeneti portjához.

Ahogy az első példákban is tettük, haladunk végig a példán. Bizonyos pontokon az egyes lépésekben csak az R-kód növekményes további sorai láthatók.

#### <a name="read-the-dataframe"></a>A dataframe beolvasása

Első lépésként olvassa el a dataframe, és győződjön meg arról, hogy megkapjuk a várt eredményeket. A következő kódnak kell elvégeznie a feladatot.

```r
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)
str(cadairydata) # Check the results
```

Most futtassa a kísérletet. Az új végrehajtási R parancsfájl-alakzat naplójának az alábbi példához hasonlóan kell kinéznie.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  8 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
```

Ezek az adattípusok a várt típusú és formátumúak. Most a hónap oszlop típusú faktor, és a várt számú szinttel rendelkezik.

#### <a name="create-a-time-series-object"></a>Idősorozat-objektum létrehozása

Hozzá kell adnia egy idősorozat-objektumot a dataframe. Cserélje le az aktuális kódot a következő kódra, amely hozzáadja a POSIXct osztály új oszlopát.

```r
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata) # Check the results
```

Most keresse meg a naplót. Ehhez a példához hasonlóan kell kinéznie.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
[ModuleOutput] 
[ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...
```

Az összegzésből láthatjuk, hogy az új oszlop valójában a POSIXct osztály.

### <a name="explore-and-transform-the-data"></a>Az adatgyűjtés feltárása és átalakítása

Vizsgáljuk meg az adatkészlet egyes változóit. A scatter Plot mátrix jó módszer a gyors megjelenítéshez. A `str()` függvényt az előző R-kódban a következő sorral cseréljük.

```r
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata, main = "Pairwise Scatterplots of dairy time series")
```

Futtassa ezt a kódot, és nézze meg, mi történik. Az R-eszköz portján létrehozott ábrának ehhez a példához hasonlóan kell kinéznie.

![A kiválasztott változók pontdiagram-mátrixát bemutató képernyőkép.](./media/r-quickstart/fig17.png)

A változók közötti kapcsolatokban valamilyen furcsa megjelenésű struktúra szerepel. Lehet, hogy ez a struktúra az adatok trendjeiből ered, és nem szabványosított változók.

### <a name="correlation-analysis"></a>Korrelációs elemzés

A korrelációs elemzés végrehajtásához a változókat is el kell végezni. Egyszerűen használhatja az R- `scale()` függvényt, amely a központok és a skála változókat is használja. Előfordulhat, hogy ez a függvény jóval gyorsabban fut. Lássunk egy példát a védekező programozásra az R-ben.

A `ts.detrend()` következő függvény mindkét műveletet végrehajtja. A következő két sornyi kód lenyomja az adatokat, majd egységesíti az értékeket.

```r
ts.detrend <- function(ts, Time, min.length = 3){
  ## Function to de-trend and standardize a time series

  ## Define some messages if they are NULL  
  messages <- c('ERROR: ts.detrend requires arguments ts and Time to have the same length',
                'ERROR: ts.detrend requires argument ts to be of type numeric',
                paste('WARNING: ts.detrend has encountered a time series with length less than', as.character(min.length)),
                'ERROR: ts.detrend has encountered a Time argument not of class POSIXct',
                'ERROR: Detrend regression has failed in ts.detrend',
                'ERROR: Exception occurred in ts.detrend while standardizing time series in function ts.detrend'
  )
  # Create a vector of zeros to return as a default in some cases
  zerovec  <- rep(length(ts), 0.0)

  # The input arguments are not of the same length, return ts and quit
  if(length(Time) != length(ts)) {warning(messages[1]); return(ts)}

  # If the ts is not numeric, just return a zero vector and quit
  if(!is.numeric(ts)) {warning(messages[2]); return(zerovec)}

  # If the ts is too short, just return it and quit
  if((ts.length <- length(ts)) < min.length) {warning(messages[3]); return(ts)}

  ## Check that the Time variable is of class POSIXct
  if(class(cadairydata$Time)[[1]] != "POSIXct") {warning(messages[4]); return(ts)}

  ## De-trend the time series by using a linear model
  ts.frame  <- data.frame(ts = ts, Time = Time)
  tryCatch({ts <- ts - fitted(lm(ts ~ Time, data = ts.frame))},
           error = function(e){warning(messages[5]); zerovec})

  tryCatch( {stdev <- sqrt(sum((ts - mean(ts))^2))/(ts.length - 1)
             ts <- ts/stdev},
            error = function(e){warning(messages[6]); zerovec})

  ts
}  
## Apply the detrend.ts function to the variables of interest
df.detrend <- data.frame(lapply(cadairydata[, 4:7], ts.detrend, cadairydata$Time))

## Plot the results to look at the relationships
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = df.detrend, main = "Pairwise Scatterplots of detrended standardized time series")
```

A függvényben meglehetősen sok történik `ts.detrend()` . A kód nagy része az argumentumokkal kapcsolatos lehetséges problémákat ellenőrzi, vagy a kivételekkel foglalkozik, amelyek a számítások során továbbra is felmerülhetnek. A kód csak néhány sora valójában a számításokat.

Már beszéltünk egy példát a védekező programozásra az érték-átalakításokban. Mindkét számítási blokk be van csomagolva `tryCatch()` . Bizonyos hibák esetén érdemes visszaadni az eredeti bemeneti vektort. Más esetekben nulla típusú vektort adunk vissza.

Vegye figyelembe, hogy a megszüntetéshez használt lineáris regresszió egy idősorozat-regresszió. A prediktív változó egy idősorozat-objektum.

A `ts.detrend()` meghatározása után a rendszer a dataframe érdeklődési változóit alkalmazza. Az eredményül kapott listát a használatával kell kikényszeríteni `lapply()` , amelyet a dataframe lévő adataihoz hozott létre `as.data.frame()` . A védelmi szempontjai miatt a `ts.detrend()` változók egyikének feldolgozása nem akadályozza meg a többiek helyes feldolgozását.

A kód utolsó sora létrehoz egy páros Scatter-ábrát. Az R-kód futtatása után itt jelennek meg a pontdiagram eredményei.

![Képernyőkép, amely megjeleníti a páros-szórási ábrát a de-trended és a szabványosított idősorozatok között.](./media/r-quickstart/fig18.png)

Ezeket az eredményeket az előző példában látható módon hasonlíthatja össze. Ha a trend el lett távolítva, és a változók szabványosítva vannak, sokkal kevesebb struktúrát látunk a változók közötti kapcsolatokban.

A korrelációk R CCF-objektumként való számításához szükséges kód a következő.

```r
## A function to compute pairwise correlations from a
## list of time series value vectors
pair.cor <- function(pair.ind, ts.list, lag.max = 1, plot = FALSE){
  ccf(ts.list[[pair.ind[1]]], ts.list[[pair.ind[2]]], lag.max = lag.max, plot = plot)
}

## A list of the pairwise indices
corpairs <- list(c(1,2), c(1,3), c(1,4), c(2,3), c(2,4), c(3,4))

## Compute the list of ccf objects
cadairycorrelations <- lapply(corpairs, pair.cor, df.detrend)  

cadairycorrelations
```

A kód futtatásával az itt látható napló jön létre.

```output
[ModuleOutput] Loading objects:
[ModuleOutput]   port1
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] [[1]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]    -1     0     1 
[ModuleOutput] 0.148 0.358 0.317 
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] [[2]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]     -1      0      1 
[ModuleOutput] -0.395 -0.186 -0.238 
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] [[3]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]     -1      0      1 
[ModuleOutput] -0.059 -0.089 -0.127 
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] [[4]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]    -1     0     1 
[ModuleOutput] 0.140 0.294 0.293 
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] [[5]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]     -1      0      1 
[ModuleOutput] -0.002 -0.074 -0.124 
```

Minden késéshez van korrelációs érték. A korrelációs értékek egyike sem elég nagy ahhoz, hogy jelentős legyen. Azt is megállapítjuk, hogy az egyes változókat egymástól függetlenül lehet modellezni.

### <a name="output-a-dataframe"></a>Dataframe kimenete
A páros korrelációkat az R CCF-objektumok listájának megfelelően számítottuk ki. Ez egy kis problémát jelent, mivel az eredményül kapott adatkészlet kimeneti portja valóban dataframe igényel. Ezen túlmenően a CCF objektum maga a lista, és csak a lista első elemében található értékeket szeretnénk használni, a különböző késések összefüggéseit.

A következő kód kibontja a késési értékeket a CCF-objektumok listájából, amelyek magukban foglalják a listákat.

```r
df.correlations <- data.frame(do.call(rbind, lapply(cadairycorrelations, '[[', 1)))

c.names <- c("correlation pair", "-1 lag", "0 lag", "+1 lag")
r.names  <- c("Corr Cot Cheese - Ice Cream",
              "Corr Cot Cheese - Milk Prod",
              "Corr Cot Cheese - Fat Price",
              "Corr Ice Cream - Mik Prod",
              "Corr Ice Cream - Fat Price",
              "Corr Milk Prod - Fat Price")

## Build a dataframe with the row names column and the
## correlation dataframe and assign the column names
outframe <- cbind(r.names, df.correlations)
colnames(outframe) <- c.names
outframe


## WARNING!
## The following line works only in Azure Machine Learning Studio (classic)
## When running in RStudio, this code will result in an error
#maml.mapOutputPort('outframe')
```

A kód első sora egy kicsit trükkös, és néhány magyarázat segíthet megérteni. A bevezetésen kívüli munka a következő:

1. A **[[** **1** . argumentummal rendelkező operátor kiválasztja a nem a CCF-objektumok listájának első elemében lévő késések vektorát.
1. A függvény `do.call()` alkalmazza a `rbind()` függvényt a lista azon elemeire, amelyeket az ad vissza `lapply()` .
1. A `data.frame()` függvény kikényszeríti a által előállított eredményt `do.call()` egy dataframe.

Vegye figyelembe, hogy a sorok nevei a dataframe egyik oszlopában vannak. Ezzel a művelettel megőrzi a sorok nevét, amikor a [végrehajtás R-szkriptből][execute-r-script]származnak.

A kód futtatásával az itt látható kimenet jelenik meg, amikor a **Megjelenítés** lehetőséget választja a kimenet megtekintéséhez az eredmény adatkészlet portján. A sorok neve az első oszlopban szerepel, a kívánt módon.

![Képernyőkép, amely a korrelációs elemzés eredményeinek kimenetét jeleníti meg.](./media/r-quickstart/fig20.png)

## <a name="time-series-example-seasonal-forecasting"></a><a id="seasonalforecasting"></a>Idősorozat-példa: szezonális előrejelzés

Az adataink mostantól elemzésre alkalmas formában jelennek meg, és megállapítottuk, hogy a változók között nincsenek jelentős korrelációk. Térjünk át, és hozzon létre egy idősorozat-előrejelzési modellt. Ennek a modellnek a használatával a kaliforniai tejtermékek előállítását fogjuk megbecsülni a 12 hónapig 2013.

Az előrejelzési modell két összetevővel, egy trend-összetevővel és egy szezonális összetevővel fog rendelkezni. A teljes előrejelzés a két összetevő terméke. Az ilyen típusú modellt multiplikatív-modellnek nevezzük. Az alternatíva egy adalékanyag-modell. Már alkalmazta a naplózási átalakítást az érdeklődésre számot tartó változóra, ami ezt az elemzést elvégezheti.

Ennek a szakasznak a teljes R-kódja a [MachineLearningSamples-notebook/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="create-the-dataframe-for-analysis"></a>A dataframe létrehozása elemzéshez

Első lépésként vegyen fel egy új [végrehajtási R script][execute-r-script] modult a kísérletbe. Kapcsolja össze a meglévő [R-szkriptek][execute-r-script] eredményét az új modul **DataSet1 elemet** bemenetével. Ennek az eredménynek a példához hasonlóan kell kinéznie.

![Diagram, amely megjeleníti a kísérletet az új végrehajtási R-parancsfájl hozzáadásával.](./media/r-quickstart/fig21.png)

Csakúgy, mint a korrelációs elemzések esetében, hozzá kell adnia egy oszlopot egy POSIXct idősorozat-objektummal. A következő kód hozzáadja az oszlopot.

```r
# If running in Machine Learning Studio (classic), uncomment the first line with maml.mapInputPort()
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata)
```

Futtassa ezt a kódot, és tekintse meg a naplót. Az eredménynek ehhez a példához hasonlóan kell kinéznie.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
[ModuleOutput] 
[ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...
```

Ennek eredményeképpen készen áll az elemzés megkezdésére.

### <a name="create-a-training-dataset"></a>Képzési adatkészlet létrehozása

A létrehozott dataframe létre kell hozni egy betanítási adatkészletet. Ez az adat tartalmazza az összes észrevételt, kivéve a 2013-as év utolsó 12 számát, amely a tesztelési adatkészlet. A következő kód kijelöli a dataframe, és létrehozza a tejtermékek termelési és árképzési változóit. Ezután létrehozunk egy négy éles és árképzési változóból álló ábrákat. A Névtelen függvények segítségével megadható a mintaterület egyes kibővítései, majd megismételhető a másik két argumentum listája a következővel: `Map()` . Ha úgy gondolja, hogy a for loop a következőhöz lett volna kidolgozva: helyes. Mivel azonban az R funkcionális nyelv, a funkcionális megközelítést vizsgáljuk.

```r
cadairytrain <- cadairydata[1:216, ]

Ylabs  <- list("Log CA Cotage Cheese Production, 1000s lb",
               "Log CA Ice Cream Production, 1000s lb",
               "Log CA Milk Production 1000s lb",
               "Log North CA Milk Milk Fat Price per 1000 lb")

Map(function(y, Ylabs){plot(cadairytrain$Time, y, xlab = "Time", ylab = Ylabs, type = "l")}, cadairytrain[, 4:7], Ylabs)
```

A kód futtatásával az itt látható R-eszköz kimenetében az idősorozat-ábrázolások sorozata jelenik meg. Az időtengely a dátumok egységében van, ami az idősorozat-ábrázolási módszer jó előnye.

![A kaliforniai tejtermékek termelési és díjszabási szolgáltatásának első idősorozata.](./media/r-quickstart/unnamed-chunk-161.png)

![A második idősorozat a kaliforniai tejtermékek termelési és díjszabási területét ábrázolja.](./media/r-quickstart/unnamed-chunk-162.png)

![Harmadik idősorozat a kaliforniai tejtermék-termeléssel és az árakkal kapcsolatban.](./media/r-quickstart/unnamed-chunk-163.png).

![A kaliforniai tejtermékek és az árak negyedik idősorozata](./media/r-quickstart/unnamed-chunk-164.png)

### <a name="a-trend-model"></a>Egy trend Model

Most, hogy létrehoztunk egy idősorozat-objektumot, és megtekintette az adatokat, kezdjük egy trend-modellt létrehozni a kaliforniai tejtermékek termelési adatokat illetően. Használhatunk egy idősorozat-regressziót. A mintaterületből egyértelmű, hogy a betanítási adatokban a megfigyelt trend pontos modellezéséhez több mint egy lejtőre lesz szükség.

Az adat kis mérete miatt kiépítjük a trendet a RStudio-ben, majd kivágták és beillesztjük az eredményül kapott modellt Machine Learning Studioba (klasszikus). A RStudio interaktív környezetet biztosít az ilyen típusú interaktív elemzésekhez.

Első kísérletként egy polinom-regressziót próbálunk ki, amely legfeljebb hármat biztosít. Az ilyen típusú modellek valós veszélyben vannak. A legjobb megoldás a magas rendű feltételek elkerülése. A `I()` függvény gátolja a tartalom értelmezését (a tartalom értelmezése), és lehetővé teszi, hogy a szó szerint értelmezett függvényt egy regressziós egyenletben írja.

```r
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3), data = cadairytrain)
summary(milk.lm)
```

Ez a függvény a következő kimenetet hozza létre.

```output
##
## Call:
## lm(formula = Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3),
##     data = cadairytrain)
##
## Residuals:
##      Min       1Q   Median       3Q      Max
## -0.12667 -0.02730  0.00236  0.02943  0.10586
##
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)
## (Intercept)       6.33e+00   1.45e-01   43.60   <2e-16 ***
## Time              1.63e-09   1.72e-10    9.47   <2e-16 ***
## I(Month.Count^2) -1.71e-06   4.89e-06   -0.35    0.726
## I(Month.Count^3) -3.24e-08   1.49e-08   -2.17    0.031 *  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Residual standard error: 0.0418 on 212 degrees of freedom
## Multiple R-squared:  0.941,    Adjusted R-squared:  0.94
## F-statistic: 1.12e+03 on 3 and 212 DF,  p-value: <2e-16
```

A `Pr(>|t|)` kimenetben látható P értékek () esetében láthatjuk, hogy a négyzetes kifejezés nem lehet jelentős. Ezt a modellt használjuk a `update()` modell módosítására a négyzetes kifejezés eldobásával.

```r
milk.lm <- update(milk.lm, . ~ . - I(Month.Count^2))
summary(milk.lm)
```

Ez a függvény a következő kimenetet hozza létre.

```output
##
## Call:
## lm(formula = Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)
##
## Residuals:
##      Min       1Q   Median       3Q      Max
## -0.12597 -0.02659  0.00185  0.02963  0.10696
##
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)
## (Intercept)       6.38e+00   4.07e-02   156.6   <2e-16 ***
## Time              1.57e-09   4.32e-11    36.3   <2e-16 ***
## I(Month.Count^3) -3.76e-08   2.50e-09   -15.1   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Residual standard error: 0.0417 on 213 degrees of freedom
## Multiple R-squared:  0.941,  Adjusted R-squared:  0.94
## F-statistic: 1.69e+03 on 2 and 213 DF,  p-value: <2e-16
```

Ez a kimenet jobban néz ki. Az összes feltétel jelentős. A 2e-16 érték alapértelmezett érték, ezért nem lehet túl komolyan venni.  

A "józan ész" teszt során tegyük fel, hogy a kaliforniai tejtermék-termelési adatokat tartalmazó idősorozat ábrázolja a látható trend görbét. A modell létrehozásához és a mintaterület készítéséhez a következő kódot adtuk hozzá a Machine Learning Studio (klasszikus) "R script Model (nem RStudio)" [végrehajtásához][execute-r-script] . Az eredmény az alábbi példában látható.

```r
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm, cadairytrain), lty = 2, col = 2)
```

![Kalifornia tejtermék-termelési adatokat a trend Model látható.](./media/r-quickstart/unnamed-chunk-18.png)

Úgy tűnik, hogy a trend Model meglehetősen jól illeszkedik az adatokat. Továbbá úgy tűnik, hogy a modell görbén túl sok a terhelés, például a páratlan kígyózik.

### <a name="seasonal-model"></a>Szezonális modell

A trend Model esetében be kell jelentkeznie, és tartalmaznia kell az idényjellegű hatásokat. Az év hónapját fogjuk használni a lineáris modellben lévő dummy változóként, hogy rögzítse a hónapról hónapra kiterjedő hatást. Ha faktor változókat vezet be egy modellbe, az elfogás nem számítható ki. Ha ezt nem teszi meg, a képlet túl van megadva, és az R elveszi az egyik kívánt tényezőt, de megtartja az elfogási kifejezést.

Mivel megfelelő trend-modellel rendelkezünk, a `update()` függvény használatával adhatjuk hozzá az új feltételeket a meglévő modellhez. A frissítési képletben az-1 az elfogási kifejezést eldobja. Folytatás a RStudio a pillanatra:

```r
milk.lm2 <- update(milk.lm, . ~ . + Month - 1)
summary(milk.lm2)
```

Ez a függvény a következő kimenetet hozza létre.

```output
##
## Call:
## lm(formula = Milk.Prod ~ Time + I(Month.Count^3) + Month - 1,
##     data = cadairytrain)
##
## Residuals:
##      Min       1Q   Median       3Q      Max
## -0.06879 -0.01693  0.00346  0.01543  0.08726
##
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)
## Time              1.57e-09   2.72e-11    57.7   <2e-16 ***
## I(Month.Count^3) -3.74e-08   1.57e-09   -23.8   <2e-16 ***
## MonthApr          6.40e+00   2.63e-02   243.3   <2e-16 ***
## MonthAug          6.38e+00   2.63e-02   242.2   <2e-16 ***
## MonthDec          6.38e+00   2.64e-02   241.9   <2e-16 ***
## MonthFeb          6.31e+00   2.63e-02   240.1   <2e-16 ***
## MonthJan          6.39e+00   2.63e-02   243.1   <2e-16 ***
## MonthJul          6.39e+00   2.63e-02   242.6   <2e-16 ***
## MonthJun          6.38e+00   2.63e-02   242.4   <2e-16 ***
## MonthMar          6.42e+00   2.63e-02   244.2   <2e-16 ***
## MonthMay          6.43e+00   2.63e-02   244.3   <2e-16 ***
## MonthNov          6.34e+00   2.63e-02   240.6   <2e-16 ***
## MonthOct          6.37e+00   2.63e-02   241.8   <2e-16 ***
## MonthSep          6.34e+00   2.63e-02   240.6   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Residual standard error: 0.0263 on 202 degrees of freedom
## Multiple R-squared:     1,    Adjusted R-squared:     1
## F-statistic: 1.42e+06 on 14 and 202 DF,  p-value: <2e-16
```

Láthatjuk, hogy a modell már nem rendelkezik elfogási kifejezéssel, és 12 jelentős hónapra vonatkozó tényezővel rendelkezik. Ez az eredmény pontosan az, amit látni akartunk.

Tegyük fel, hogy a kaliforniai tejtermékek termelési információinak egy másik idősorozata látható, hogy milyen jól működik a szezonális modell. Felvettük a következő kódot a Machine Learning Studio (klasszikus) [R-szkript végrehajtásával][execute-r-script] a modell létrehozásához és egy mintaterület készítéséhez.

```r
milk.lm2 <- lm(Milk.Prod ~ Time + I(Month.Count^3) + Month - 1, data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm2, cadairytrain), lty = 2, col = 2)
```

A kód Machine Learning Studioban (klasszikus) való futtatása az itt látható ábrát állítja elő.

![Kalifornia tejtermékek gyártása modellekkel, beleértve az idényjellegű hatásokat.](./media/r-quickstart/unnamed-chunk-20.png)

Az ebben a példában szereplő értékekhez való igazítás inkább biztató. A trend és a szezonális effektus (havi változat) is ésszerűnek tűnik.

A modell egy másik beadásával nézzük meg a maradványokat. A következő kód kiszámítja a két modell előre jelzett értékeit, kiszámítja az idényjellegű modell maradékait, majd kirajzolja ezeket a fennmaradó értékeket a betanítási adatokhoz.

```r
## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute and plot the residuals
residuals <- cadairydata$Milk.Prod - predict2
plot(cadairytrain$Time, residuals[1:216], xlab = "Time&quot;, ylab =&quot;Residuals of Seasonal Model")
```

A fennmaradó mintaterület itt látható.

![A betanítási adattípushoz tartozó szezonális modell maradványai.](./media/r-quickstart/unnamed-chunk-21.png)

Ezek a maradványok ésszerűnek tűnnek. Az 2008-2009-es recesszió hatására nem számítunk fel konkrét struktúrát, amelyet a modellünk nem vesz figyelembe különösen jól.

Az ebben a példában bemutatott ábra hasznos lehet a maradékokban lévő időfüggő minták észleléséhez. A számítási és a felhasznált maradványok kirajzolásának explicit megközelítése a maradványok időbeli sorrendbe helyezése a parcellán. Ha már megtörtént `milk.lm$residuals` a nyomtatás, a mintaterület nem lett volna időbeli sorrend.

A használatával `plot.lm()` diagnosztikai mintaterületek sorozatát is létrehozhatja.

```r
## Show the diagnostic plots for the model
plot(milk.lm2, ask = FALSE)
```

Ez a kód a következő példákban bemutatott diagnosztikai mintaterületek sorozatát eredményezi.

![A szezonális modell első diagnosztikai ábrázolása.](./media/r-quickstart/unnamed-chunk-221.png)

![Második diagnosztikai terület az idényjellegű modellhez.](./media/r-quickstart/unnamed-chunk-222.png)

![Harmadik diagnosztikai terület az idényjellegű modellhez.](./media/r-quickstart/unnamed-chunk-223.png)

![Negyedik diagnosztikai terület az idényjellegű modellhez.](./media/r-quickstart/unnamed-chunk-224.png)

Ezekben a mintaterületeken néhány nagy befolyású pont van azonosítva, de semmi sem okoz gondot. Ezen kívül láthatjuk a normál Q-Q ábrán, hogy a maradékok általában kiterjesztve vannak, ami a lineáris modellek fontos feltételezése.

### <a name="forecasting-and-model-evaluation"></a>Előrejelzés és modell kiértékelése

A példához csak még egy dolgot kell elvégeznie. Az előrejelzéseket ki kell számítani, és a tényleges adatok alapján kell mérni a hibát. Az előrejelzések a 2013-es 12 hónapig érvényesek. Ezt az előrejelzést a betanítási adatkészlet részét nem képező tényleges adatokra is kiszámíthatja. Emellett a 18 éves betanítási adatmennyiséget is összehasonlíthatja a 12 hónapig tartó tesztelési adattal.

Az idősorozat-modellek teljesítményének méréséhez számos metrika használatos. Ebben az esetben a root Mean Square (RMS) hibát fogjuk használni. A következő függvény két adatsorozat között kiszámítja az RMS-hibát.

```r
RMS.error <- function(series1, series2, is.log = TRUE, min.length = 2){
  ## Function to compute the RMS error or difference between two
  ## series or vectors

  messages <- c("ERROR: Input arguments to function RMS.error of wrong type encountered",
                "ERROR: Input vector to function RMS.error is too short",
                "ERROR: Input vectors to function RMS.error must be of same length",
                "WARNING: Funtion rms.error has received invald input time series.")

  ## Check the arguments
  if(!is.numeric(series1) | !is.numeric(series2) | !is.logical(is.log) | !is.numeric(min.length)) {
    warning(messages[1])
    return(NA)}

  if(length(series1) < min.length) {
    warning(messages[2])
    return(NA)}

  if((length(series1) != length(series2))) {
       warning(messages[3])
    return(NA)}

  ## If is.log is TRUE exponentiate the values, else just copy
  if(is.log) {
    tryCatch( {
      temp1 <- exp(series1)
      temp2 <- exp(series2) },
      error = function(e){warning(messages[4]); NA}
    )
  } else {
    temp1 <- series1
    temp2 <- series2
  }

 ## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute the RMS error in a dataframe
  tryCatch( {
    sqrt(sum((temp1 - temp2)^2) / length(temp1))},
    error = function(e){warning(messages[4]); NA})
}
```

Ahogy az `log.transform()` "érték-átalakítások" szakaszban tárgyalt függvényhez hasonlóan, a függvény számos hiba-ellenőrzési és kivétel-helyreállítási kódot tartalmaz. Az alkalmazott elvek ugyanazok. A munka két helyen történik `tryCatch()` . Először is exponentiated az idősorozat, mert már dolgozunk az értékek naplóival. Másodszor, a tényleges RMS-hiba számítása.

Az RMS-hiba mérésére szolgáló függvénnyel egy dataframe készítünk, amely az RMS-hibákat tartalmazza. A trend modelhez és a teljes modellhez szezonális tényezőket tartalmazó feltételek tartoznak. A következő kód végzi el a feladatot a létrehozott két lineáris modell használatával.

```r
## Compute the RMS error in a dataframe
## Include the row names in the first column so they will
## appear in the output of the Execute R Script
RMS.df  <-  data.frame(
rowNames = c("Trend Model", "Seasonal Model"),
  Traing = c(
  RMS.error(predict1[1:216], cadairydata$Milk.Prod[1:216]),
  RMS.error(predict2[1:216], cadairydata$Milk.Prod[1:216])),
  Forecast = c(
    RMS.error(predict1[217:228], cadairydata$Milk.Prod[217:228]),
    RMS.error(predict2[217:228], cadairydata$Milk.Prod[217:228]))
)
RMS.df

## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('RMS.df')
```

A kód futtatásával az itt látható kimenet jön létre az eredmény-adatkészlet kimeneti portján.

![Képernyőkép, amely az RMS-hibák összehasonlítását mutatja a modellekhez.](./media/r-quickstart/fig26.png)

Ezekből az eredményekből láthatjuk, hogy a modellhez tartozó szezonális tényezők hozzáadása jelentősen csökkenti az RMS-hibát. Nem meglepő, hogy a betanítási adatmennyiséghez tartozó RMS-hiba egy kicsit kisebb az előrejelzésnél.

## <a name="guide-to-rstudio-documentation"></a><a id="appendixa"></a>Útmutató a RStudio dokumentációhoz

A RStudio jól dokumentálva van. Íme néhány hivatkozás a RStudio dokumentációjának főbb részeire, hogy megismerkedjen az első lépésekkel.

* **Projektek létrehozása**: a RStudio használatával rendszerezheti és kezelheti az R-kódját projektjeiben. További információ: [using Projects (projektek használata](https://support.rstudio.com/hc/articles/200526207-Using-Projects)). Kövesse ezeket az utasításokat, és hozzon létre egy projektet az R Code-példákhoz ebben a cikkben.
* **R-kód szerkesztése és végrehajtása**: a RStudio integrált környezetet biztosít az r-kód szerkesztéséhez és végrehajtásához. További információ: [kód szerkesztése és végrehajtása](https://support.rstudio.com/hc/articles/200484448-Editing-and-Executing-Code).
* **Hibakeresés**: a RStudio hatékony hibakeresési képességeket tartalmaz. További információ ezekről a funkciókról: [hibakeresés a RStudio](https://support.rstudio.com/hc/articles/200713843-Debugging-with-RStudio)-mel. A Töréspont hibaelhárítási funkcióiról a [töréspont hibaelhárítása](https://support.rstudio.com/hc/articles/200534337-Breakpoint-Troubleshooting)című témakörben talál további információt.

## <a name="further-reading"></a><a id="appendixb"></a>További olvasnivalók

Ez az R-programozási oktatóanyag ismerteti az alapismereteket, amelyekkel a Machine Learning Studio (klasszikus) R nyelvét kell használnia. Ha nem ismeri az R-t, két bevezető érhető el a CRAN-on:

* A [kezdőknek](https://cran.r-project.org/doc/contrib/Paradis-rdebuts_en.pdf) az Emmanuel Paradis jó kiindulópontja az R.
* [Az R bevezetője](https://cran.r-project.org/doc/manuals/R-intro.html) a W. N használatával. A Venables és az al. mélyebbre vált.

Az R-ben számos könyv található, amely segítséget nyújt az első lépésekhez:

* **R-programozás művészete: a Norman Matloff által a statisztikai szoftverek tervezésének** bemutatása kiváló bevezetést mutat az r-ben való programozáshoz.
* Az **r szakácskönyv** Paul Teetor az r használatának probléma-és megoldási megközelítését nyújtja.
* Az **R in Action** by Robert Kabacoff egy másik hasznos bevezető könyv. A Companion [gyors R-webhely](https://www.statmethods.net/) hasznos erőforrás.
* **A Patrick Burns r Inferno** egy meglepően vicces könyv, amely számos olyan trükkös és bonyolult témakört tartalmaz, amelyek az R-ben végzett programokban fordulhatnak elő. A könyv ingyenesen elérhető az [R infernonél](https://www.burns-stat.com/documents/books/the-r-inferno/).
* A Hadley Wickham **fejlett r** -je részletesen ismerteti az r-ben található speciális témákat. A könyv online verziója a [speciális R](http://adv-r.had.co.nz/)verzióban ingyenesen elérhető.
* Az R Paul Cowpertwait és Andrew Metcalfe **bevezető idősorozata** az r for Time Series Analysis használatának bevezetését kínálja. Számos további elméleti szöveg R-példákat tartalmaz.

Íme néhány nagyszerű internetes erőforrás:

* A [Cran-feladat nézete: az Idősorozat-elemzés](https://cran.r-project.org/web/views/TimeSeries.html) az R idősorozat-csomagok katalógusát tartalmazza. Az adott idősorozat-objektum csomagjaival kapcsolatos információkért tekintse meg a csomag dokumentációját.
* Az [r](https://www.datacamp.com/courses/introduction-to-r) egy ingyenes interaktív tanfolyam a DataCamp, amely az r-t tanítja meg a böngésző kényelmében, a videók és a kódolási gyakorlatok használatával. A legújabb R-technikák és csomagok interaktív oktatóanyagokkal rendelkeznek.
* [Ismerkedjen meg az R programozással, a végleges útmutatóval](https://www.datamentor.io/r-programming/) a DataMentor.
* Az [r CODER](https://r-coder.com/) részletes r-oktatóanyagokat és ingyenes r-tanfolyamot biztosít kezdőknek.
* Egy gyors oktatóanyag az [R-oktatóanyag](https://www.cyclismo.org/tutorial/R/) Kelly blackból a Clark Egyetemen.
* [A legnépszerűbb r nyelvi erőforrások az adatkezelési képességek javítására](https://www.computerworld.com/article/2497464/business-intelligence-60-r-resources-to-improve-your-data-skills.html) több mint 60 R-erőforrást listáznak.

<!-- Module References -->
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
