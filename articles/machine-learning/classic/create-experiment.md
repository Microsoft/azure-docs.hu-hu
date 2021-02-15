---
title: 'ML Studio (klasszikus): gyors útmutató: adatelemzési kísérlet létrehozása – Azure'
description: Ez a Machine learning-útmutató végigvezeti egy egyszerű adatelemzési kísérleten. Egy regressziós algoritmus használatával fogjuk előre megbecsülni egy autó árát.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: quickstart
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/06/2019
ms.openlocfilehash: 843845e6099bdf43f7cd37b0f3e6f82d3ed50b4c
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520612"
---
# <a name="quickstart-create-your-first-data-science-experiment-in-machine-learning-studio-classic"></a>Rövid útmutató: az első adatelemzési kísérlet létrehozása Machine Learning Studio (klasszikus)

**a következőkre vonatkozik:** ![ Ez egy pipa, ami azt jelenti, hogy ez a cikk a Machine Learning Studio (klasszikus) elemre vonatkozik. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasszikus) ![ Ez egy X, ami azt jelenti, hogy ez a cikk Azure Machine learningre vonatkozik.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

Ebben a rövid útmutatóban egy gépi tanulási kísérletet hoz létre [Azure Machine learning Studio (klasszikus)](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio) , amely az autó árát különböző változók, például gyártmányok és technikai specifikációk alapján Jósolja meg.

Ha teljesen új a gépi tanuláshoz, a [kezdőknek szóló videó-adatelemzési](data-science-for-beginners-the-5-questions-data-science-answers.md) sorozat a mindennapi nyelvhasználattal és fogalmakkal remek bevezetést nyújt a gépi tanuláshoz.

Ez a rövid útmutató egy kísérlet alapértelmezett munkafolyamatát követi:

1. **Modell létrehozása**
    - [Az adatok lekérése]
    - [Az adatok előkészítése]
    - [Funkciók definiálása]
1. **A modell betanítása**
    - [Algoritmus kiválasztása és alkalmazása]
1. **A modell pontozása és tesztelése**
    - [Új személygépkocsik árának előrejelzése]

[Az adatok lekérése]: #get-the-data
[Az adatok előkészítése]: #prepare-the-data
[Funkciók definiálása]: #define-features
[Algoritmus kiválasztása és alkalmazása]: #choose-and-apply-an-algorithm
[Új személygépkocsik árának előrejelzése]: #predict-new-automobile-prices

## <a name="get-the-data"></a>Az adatok lekérése

Az első dolog, amire szüksége van a Machine learning szolgáltatásban.
A Studio (klasszikus) számos minta adatkészletet tartalmaz, amelyeket használhat, vagy több forrásból is importálhat adatokat. Ebben a példában a munkaterületén megtalálható **Automobile price data (Raw)** (Nyers autóáradatok) nevű mintahalmazt fogjuk használni.
Ebben az adathalmazban számos különböző autót bemutató bejegyzés szerepel. A bejegyzések számos adatot (például márka, típus, műszaki specifikációk, ár) tartalmaznak.

> [!TIP]
> Az [Azure AI katalógusban](https://gallery.azure.ai) megtalálja az alábbi kísérlet egy működő példányát. Nyissa meg az **[első adatelemzési kísérletet – az autó árának előrejelzését](https://gallery.azure.ai/Experiment/Your-first-data-science-experiment-Automobile-price-prediction-1)** , és kattintson a **Megnyitás a Studióban** lehetőségre a kísérlet másolatának letöltéséhez a Machine learning Studio (klasszikus) munkaterületre.

A következőképpen vonhatja be az adathalmazt a kísérletbe.

1. Hozzon létre egy új kísérletet az Machine Learning Studio (klasszikus) ablak alján található **+ új** elemre kattintva. Válassza a **kísérlet**  >   **üres kísérlet** lehetőséget.

1. A kísérlet kap egy alapértelmezett nevet, amelyet a vászon tetején láthat. Jelölje ki ezt a szöveget, és módosítsa valami értelmesebbre, például arra, hogy **Autó árának előrejelzése**. A névnek nem kell egyedinek lennie.

    ![A kísérlet átnevezése](./media/create-experiment/rename-experiment.png)

1. A kísérletvászontól balra az adathalmazokat és modulokat tartalmazó paletta látható. A paletta tetején található keresőmezőbe gépelje be, hogy **automobile**. A rendszer megjeleníti az **Automobile price data (Raw)** (Nyers autóáradatok) nevű adathalmazt. Húzza rá az adathalmazt a kísérletvászonra.

    ![Az autókat tartalmazó adathalmaz megkeresése és a kísérleti vászonra húzása](./media/create-experiment/type-automobile.png)

Ha szeretné megtekinteni az adatokat, kattintson az autó adatkészletének alján található kimeneti portra, majd válassza a **Megjelenítés** lehetőséget.

![Kattintson a kimeneti portra, majd válassza a „Visualize” (Képi megjelenítés) lehetőséget](./media/create-experiment/select-visualize.png)

> [!TIP]
> Az adathalmazok és modulok kis körökkel jelölt bemeneti és kimeneti portokkal rendelkeznek – a bemeneti portok felül, a kimeneti portok alul találhatók.
Az adatfolyam létrehozásához a kísérlet során össze fogja kötni az egyik modul kimeneti portját egy másik modul bemeneti portjával.
Ha meg szeretné tekinteni, hogyan jelennek meg az adatok az adatfolyam egy adott pontján, kattintson az adathalmaz vagy modul kimeneti portjára.

Ebben az adatkészletben minden sor egy automobilt jelöl, és az egyes autókhoz tartozó változók oszlopként jelennek meg. Az árat a jobb szélső oszlopban fogjuk megjósolni (26. oszlop, "Price" címmel) egy adott autó változóit használva.

![Az autókra vonatkozó adatok megtekintése az adatokat megjelenítő ablakban](./media/create-experiment/visualize-auto-data.png)

A jobb felső sarokban látható „**x**” gombra kattintva zárja be a képi megjelenítési ablakot.

## <a name="prepare-the-data"></a>Az adatok előkészítése

Az adathalmazok elemzése előtt általában némi előfeldolgozás szükséges. Talán észrevette, hogy az oszlopok számos sorából hiányoztak az értékek. Ahhoz, hogy a modell elemezni tudja az adatokat, el kell távolítani a hiányzó értékeket. Töröljük a hiányzó értékeket tartalmazó sorokat. A **normalized-losses** (normalizált veszteségek) című oszlopból ráadásul rendkívül sok érték hiányzik, ezért ezt az oszlopot teljesen kizárjuk a modellből.

> [!TIP]
> A legtöbb modul használatának előfeltétele a bemeneti adatok hiányzó értékeinek törlése.

Először hozzáadunk egy modult, amely teljesen eltávolítja a **normalizált veszteségek** oszlopot. Ezután hozzáadunk egy másik modult, amely eltávolítja a hiányzó adatsorokat.

1. A modul paletta tetején található keresőmezőbe írja be a Select Columns ( **Oszlopok kiválasztása** ) elemet, hogy megtalálja a [Select Columns elemet az adatkészlet][select-columns] modulban. Ezután húzza a kísérlet vászonra. Ezzel a modullal kiválaszthatjuk, hogy melyik adatoszlopokat szeretnénk bevonni a modellbe, vagy éppen kizárni a modellből.

1. Az **Automobile Price (nyers)** adathalmaz kimeneti portjának csatlakoztatása az adatkészlet Select oszlopainak bemeneti portjához.

    ![A „Select Columns in Dataset” (Adathalmaz oszlopainak kijelölése) modul hozzáadása a kísérletvászonhoz, majd összekötése](./media/create-experiment/type-select-columns.png)

1. Kattintson a [Select Columns in Dataset][select-columns] (Adathalmaz oszlopainak kijelölése) modulra, majd a **Properties** (Tulajdonságok) panelen kattintson a **Launch column selector** (Oszlopválasztó elindítása) elemre.

   - A bal oldalon kattintson a **With rules** (Szabályokkal) lehetőségre
   - A **Begin With** (Kezdés a következővel) területen kattintson az **All columns** (Minden oszlop) lehetőségre. Ezek a szabályok közvetlenül [kiválasztja az adatkészlet oszlopait az összes oszlop átadásához][select-columns] (kivéve a kizárni kívánt oszlopokat).
   - A legördülő listákból válassza az **Exclude** (Kizárás) és a **column names** (oszlopnevek) lehetőséget, majd kattintson a szövegmezőbe. Megjelenik az oszlopnevek listája. Válassza a **normalized-losses** (normalizált veszteségek) lehetőséget, amely aztán bekerül a szövegdobozba.
   - Kattintson a pipa (OK) gombra az oszlop választójának bezárásához (a jobb alsó sarokban).

     ![Az oszlopválasztó elindítása és a „normalized-losses” (normalizált veszteségek) oszlop kizárása](./media/create-experiment/launch-column-selector.png)

     Ekkor a **Select Columns in Dataset** (Adathalmaz oszlopainak kijelölése) modul Properties (Tulajdonságok) panelje jelzi, hogy a modul a **normalized-losses** ( normalizált veszteségek) kivételével az adathalmaz összes oszlopát fel fogja dolgozni.

     ![A tulajdonságok panelen látható, hogy a „normalized-losses” (normalizált veszteségek) oszlop ki van zárva](./media/create-experiment/showing-excluded-column.png)

     > [!TIP] 
     > A modulokhoz megjegyzéseket adhat. Ehhez kattintson duplán a kívánt modulra, majd gépelje be a megjegyzés szövegét. Így egyetlen pillantással felmérheti, hogy mire szolgál az adott modul a kísérletben. A jelen esetben kattintson duplán a [Select Columns in Dataset][select-columns] (Adathalmaz oszlopainak kijelölése) modulra, és írja be az „Exclude normalized losses” (A normalized-losses oszlop kizárása) szöveget.

     ![Megjegyzés hozzáadása egy modulhoz dupla kattintással](./media/create-experiment/add-comment.png)

1. Húzza a [Clean Missing Data][clean-missing-data] (Hiányzó adatok törlése) modult a kísérletvászonra, és kösse össze a [Select Columns in Dataset][select-columns] (Adathalmaz oszlopainak kijelölése) modullal. A **Properties** (Tulajdonságok) panel **Cleaning mode** (Törlés módja) beállításánál válassza a **Remove entire row** (Teljes sor eltávolítása) lehetőséget. Ezek a beállítások közvetlenül [törlik a hiányzó adatokból][clean-missing-data] az adatok törlését a hiányzó értékeket tartalmazó sorok eltávolításával. Kattintson duplán a modulra, és írja be a következő megjegyzést: „Hiányzó értéket tartalmazó sorok törlése”.

    ![Törlés módjának beállítása „Remove entire row” (Teljes sor eltávolítása) lehetőségre a „Clean Missing Data” (Hiányzó adatok törlése) modulnál](./media/create-experiment/set-remove-entire-row.png)

1. A kísérlet futtatásához kattintson a lap alján található **RUN** (Futtatás) parancsra.

    A kísérlet befejezését követően az összes modulnál megjelenik egy zöld pipa, amely jelzi, hogy az adott modul sikeresen lefutott. A jobb felső sarokban pedig megjelenik a **Finished running** (Futtatás befejeződött) állapot.

    ![A kísérlet várható megjelenése a futtatás után](./media/create-experiment/early-experiment-run.png)

> [!TIP]
> Miért futtattuk a kísérletet most? A kísérlet futtatásával biztosítható, hogy az adatokhoz tartozó oszlopdefiníciók az adatkészletből áthaladnak a [Select Columns in Dataset][select-columns] (Adathalmaz oszlopainak kijelölése) modulon és a [Clean Missing Data][clean-missing-data] (Hiányzó adatok törlése) modulon. Ez azt jelenti, hogy a [Clean Missing Data][clean-missing-data] (Hiányzó adatok törlése) modulhoz kapcsolt modulok is megkapják ugyanezeket az adatokat.

Most már tiszta adattal rendelkezünk. Ha szeretné megtekinteni a megtisztított adathalmazt, kattintson a [Clean Missing Data][clean-missing-data] (Hiányzó adatok törlése) modul bal oldali kimeneti portjára, és válassza a **Visualize** (Képi megjelenítés) lehetőséget. Láthatja, hogy a **normalized-losses** oszlop eltűnt, ahogy a hiányzó értékek is.

Most, hogy megtisztítottuk az adatokat, megadhatjuk, hogy mely jellemzőket szeretnénk felhasználni a prediktív modellben.

## <a name="define-features"></a>Funkciók definiálása

A gépi tanulásban a *funkciók* a érdeklik egyes, mérhető tulajdonságai. Adathalmazunk minden sora egy-egy autót képvisel, az oszlopok pedig az autók különböző jellemzőit tartalmazzák.

A prediktív modellben használandó jellemzők helyes megválasztásához fontos a kísérletezés, illetve a megoldani kívánt probléma jó ismerete. Bizonyos jellemzők ugyanis hasznosabbak a cél előrejelzéséhez, mint mások. Egyes funkciók erős korrelációt mutatnak más funkciókkal, és eltávolíthatók. A példánkban például szorosan összefügg a city-mpg (fogyasztás városban) és highway-mpg (fogyasztás autópályán), ezért az egyiket eltávolíthatjuk anélkül, hogy lényegesen befolyásolnánk az előrejelzést.

Ideje, hogy létrehozzuk a modellt az adathalmaz jellemzőinek meghatározott részhalmaza alapján. Később visszatérhet ehhez a lépéshez, és más jellemzőket kiválasztva ismét lefuttathatja a kísérletet, ha kíváncsi rá, hogy úgy jobb eredményeket kap-e. Kezdésként azonban a következő funkciókat próbáljuk ki:

> Gyártmány, szövegtörzs, kerék-alap, motor-méret, lóerő, csúcs-RPM, autópálya-MPG, Ár

1. Húzzon egy újabb [Select Columns in Dataset][select-columns] (Adathalmaz oszlopainak kijelölése) modult a kísérletvászonra. Kösse össze a [Clean Missing Data][clean-missing-data] (Hiányzó adatok törlése) modul bal oldali kimeneti portját a [Select Columns in Dataset][select-columns] (Adathalmaz oszlopainak kijelölése) modul bemenetével.

    ![A „Select Columns in Dataset” (Adathalmaz oszlopainak kijelölése) modul összekötése a „Clean Missing Data” (Hiányzó adatok törlése) modullal](./media/create-experiment/connect-clean-to-select.png)

1. Kattintson duplán a modulra, és írja be: „Az előrejelzéshez használatos jellemzők kiválasztása”.

1. Kattintson a **Properties** (Tulajdonságok) panel **Launch column selector** (Oszlopválasztó indítása) elemére.

1. Kattintson a **With rules** (Szabályokkal) lehetőségre.

1. A **Begin With** (Kezdés a következővel) területen kattintson a **No columns** (Egyetlen oszlop sem) lehetőségre. A szűrősorban válassza ki az **Include** (Belefoglalás) és a **column names** (oszlopnevek) lehetőséget, és jelölje ki az oszlopnevek listáját a szövegmezőben. Ez a szűrő arra utasítja a modult, hogy ne továbbítson semmilyen oszlopot (funkciót), kivéve a megadott beállításokat.

1. Kattintson a pipa (OK) gombra.

    ![Az előrejelzésbe bevonni kívánt oszlopok (tulajdonságok) kijelölése](./media/create-experiment/select-columns-to-include.png)

Ez a modul olyan szűrt adatkészletet hoz létre, amely csak azokat a funkciókat tartalmazza, amelyeket a következő lépésben fogunk használni. Később visszatérhet ide, és más jellemzőkkel is elvégezheti az előrejelzést.

## <a name="choose-and-apply-an-algorithm"></a>Algoritmus kiválasztása és alkalmazása

Most, hogy előkészítettük az adatokat, a prediktív modell létrehozásához már csak a tanítás és a tesztelés szükséges. A következőkben az adatok segítségével elvégezzük a modell betanítását, majd a modell tesztelésével megállapítjuk, hogy milyen pontossággal képes előre jelezni az árakat.
<!-- For now, don't worry about *why* we need to train and then test a model.-->

A *besorolás* és a *regresszió* két algoritmus, amelynek segítségével felügyelt gépi tanítás valósítható meg. Besoroláskor a válaszok előrejelzése megadott kategóriakészletből történik (például: színek (vörös, kék vagy zöld)). A rendszer a számok előrejelzésére regressziós módszert használ.

Mivel az árat szeretnénk előre jelezni, ami egy szám, regressziós algoritmust fogunk használni. Ebben a példában egy *lineáris regressziós* modellt fogunk használni.


A modell betanításához az árat tartalmazó adathalmazt biztosítunk számára. A modell megvizsgálja adatokat, és összefüggéseket keres az autó tulajdonságai és az ára között. Ezután teszteljük a modellt. Ehhez olyan autók tulajdonságkészletét töltjük be, amelyeket ismerünk, és megnézzük, hogy mennyire sikeresen tudja a modell előre jelezni az ismert árakat.

Az adatok a modell betanítására és tesztelésére is használhatók. Ehhez két halmazra, egy tanítási és egy tesztelési halmazra osztjuk fel az adatokat.

1. Jelölje ki, majd húzza a kísérletvászonra a [Split Data][split] (Adatok felosztása) modult, majd kösse össze a legutóbb használt [Select Columns in Dataset][select-columns] (Adathalmaz oszlopainak kijelölése) modullal.

1. Kattintással jelölje ki a [Split Data][split] (Adatok felosztása) modult. Keresse meg a **Properties** (Tulajdonságok) panelen a vászontól jobbra a **Fraction of rows in the first output dataset** (Sorok hányadosa az első kimeneti adathalmazban) beállítást, és adja meg a 0,75 értéket. Így az adatok 75 százalékát a modell betanítására, 25 százalékát pedig a modell tesztelésére használhatjuk.

    ![A „Split Data” (Adatok felosztása) modul felosztási értékének beállítása 0,75-re](./media/create-experiment/set-split-data-percentage.png)

    > [!TIP]
    > A **Random seed** (Véletlenszám-generálás kezdőértéke) paraméter módosításával különböző véletlenszerűen kiválasztott mintákat hozhat létre, amelyeket szintén felhasználhat a modell betanítására és tesztelésére. Ez a paraméter szabályozza a pszeudo-véletlenszám-generátor kezdőértékét.

1. Futtassa a kísérletet. A kísérlet futtatásakor a [Select Columns in Dataset][select-columns] (Adathalmaz oszlopainak kijelölése) és a [Split Data][split] (Adatok felosztása) modul átadja a következőkben hozzáadott moduloknak az oszlopdefiníciókat.  

1. A tanulási algoritmus kiválasztásához bontsa ki a vászontól balra, a modulpalettán található **Machine Learning** (Gépi tanulás) kategóriát, majd bontsa ki az **Initialize Model** (Inicializálási modell) kategóriát is. Itt számos modulkategória közül választhat, amelyek segítségével inicializálható a gépi tanulási algoritmus. Ehhez a kísérlethez válassza a **Regression** (Regresszió) kategóriában található [Linear Regression][linear-regression] (Lineáris regresszió) modult, majd húzza a kísérletvászonra. (A modult úgy is megkeresheti, ha a paletta keresőmezőjébe beírja a „linear regression” kifejezést.)

1. Keresse meg, majd húzza a kísérletvászonra a [Train Model][train-model] (Modell betanítása) modult. Kösse össze a [Linear Regression][linear-regression] (Lineáris regresszió) modul kimenetét a [Train Model][train-model] (Modell betanítása) modul bal oldali bemenetével, és kösse össze a [Split Data][split] (Adatok felosztása) modul adatbetanítási kimenetét (bal oldali port) a [Train Model][train-model] (Modell betanítása) modul jobb oldali bemenetével.

    ![A „Train Model” (Modell betanítása) modul összekötése a „Linear Regression” (Lineáris regresszió) és a „Split Data” (Adatok felosztása) modulokkal](./media/create-experiment/connect-train-model.png)

1. Kattintson a [Train Model][train-model] (Modell betanítása) modulra, kattintson a **Properties** (Tulajdonságok) panel **Launch column selector** (Oszlopválasztó indítása) elemére, és válassza ki a **price** (ár) oszlopot. Az **Ár** az az érték, amelyet a modellünk előre fog jelezni.

    Jelölje ki a **price** (ár) oszlopot az oszlopválasztóban. Ehhez helyezze át az **Available columns** (Elérhető oszlopok) listáról a **Selected columns** (Kiválasztott oszlopok) listára.

    ![Az ár oszlop kiválasztása a „Train Model” (Modell betanítása) modulhoz](./media/create-experiment/select-price-column.png)

1. Futtassa a kísérletet.

Ezzel kapunk egy betanított regressziós modellt, amely képes pontszámot rendelni az új autóadatokhoz, és így előre jelezni az árakat.

![A kísérlet várható megjelenése a futtatás után](./media/create-experiment/second-experiment-run.png)

## <a name="predict-new-automobile-prices"></a>Új személygépkocsik árának előrejelzése

Most, hogy adataink 75 százalékával betanítottuk a modellt, a maradék 25 százalék pontozásával megállapíthatjuk, hogy mennyire működik jól.

1. Keresse meg, majd húzza a kísérletvászonra a [Score Model][score-model] (Modell pontozása) modult. Kösse össze a [Train Model][train-model] (Modell betanítása) modul kimenetét a [Score Model][score-model] (Modell pontozása) modul bal oldali bemeneti portjával. Kösse össze a [Split Data][split] (Adatok felosztása) modul tesztelési adatokat tartalmazó kimenetét (jobb oldali portját) a [Score Model][score-model] (Modell pontozása) modul jobb oldali bemeneti portjával.

    ![A „Score Model” (Modell pontozása) modul összekötése a „Train Model” (Modell betanítása) és a „Split Data” (Adatok felosztása) modulokkal](./media/create-experiment/connect-score-model.png)

1. Futtassa a kísérletet, és tekintse meg a [pontszám modell][score-model] modul kimenetét úgy, hogy a [pontszám modell][score-model] kimeneti portjára kattint, **és kiválasztja a** vizualizáció lehetőséget. A modul megjeleníti az előre jelzett árat, valamint a tesztadatokból ismert tényleges értéket.  

    ![A „Score Model” (Modell pontozása) modul kimenete](./media/create-experiment/score-model-output.png)

1. Végül teszteljük az eredmény minőségét. Jelölje ki, majd húzza a kísérletvászonra az [Evaluate Model][evaluate-model] (Modell kiértékelése) modult, és kösse össze a [Score Model][score-model] (Modell pontozása) modul kimenetét az [Evaluate Model][evaluate-model] (Modell kiértékelése) bal oldali bemeneti portjával. Az elkészült kísérletnek a következőképpen kell kinéznie:

    ![Az elkészült kísérlet](./media/create-experiment/complete-linear-regression-experiment.png)

1. Futtassa a kísérletet.

Az [Evaluate Model][evaluate-model] (Modell kiértékelése) modul eredményének megtekintéséhez kattintson a kimeneti portra, majd válassza a **Visualize** (Képi megjelenítés) lehetőséget.

![A kísérlet kiértékelésének eredménye](./media/create-experiment/evaluation-results.png)

A következő statisztikák tekinthetők meg:

- **Mean Absolute Error** (átlagos abszolút eltérés, MAE): az abszolút eltérések átlaga (*eltérésnek* az előre jelzett érték és a tényleges érték közötti különbséget nevezzük).
- **Root Mean Squared Error** (gyökátlagos négyzetes eltérés, RMSE): a tesztelési adathalmazon végzett előrejelzések eltéréseinek négyzetéből számított átlag négyzetgyöke.
- **Relative Absolute Error** (relatív abszolút eltérés): a tényleges értékek és az összes tényleges értékek átlaga közötti különbségek abszolút eltérésének átlaga.
- **Relative Squared Error** (relatív négyzetes eltérés): a négyzetes eltérések átlaga a tényleges értékek és az összes tényleges érték átlaga közötti különbség négyzetes értékéhez viszonyítva.
- **Coefficient of Determination** (determinációs együttható): ez az **R-négyzet értéke** néven is ismert statisztikai mérőszám azt mutatja, hogy a modell mennyire illik az adatokhoz.

Az összes hibastatisztikára igaz, hogy minél kisebb az érték, annál jobb a modell. A kisebb értékek azt jelzik, hogy az előrejelzés közelebb van a tényleges értékekhez. A **Coefficient of Determination** (determinációs együttható) értéke minél közelebb van az egyhez (1,0-hoz), annál pontosabb az előrejelzés.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy egyszerű kísérletet egy minta adatkészlet használatával. Ha szeretné megvizsgálni, hogyan hozhat létre és helyezhet üzembe egy modellt részletesebben, folytassa a prediktív megoldás oktatóanyagával.

> [!div class="nextstepaction"]
> [Oktatóanyag: prediktív megoldás fejlesztése a Studióban (klasszikus)](tutorial-part1-credit-risk.md)

<!-- Module References -->
[evaluate-model]: /azure/machine-learning/studio-module-reference/evaluate-model
[linear-regression]: /azure/machine-learning/studio-module-reference/linear-regression
[clean-missing-data]: /azure/machine-learning/studio-module-reference/clean-missing-data
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset
[score-model]: /azure/machine-learning/studio-module-reference/score-model
[split]: /azure/machine-learning/studio-module-reference/split-data
[train-model]: /azure/machine-learning/studio-module-reference/train-model