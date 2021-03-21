---
title: 'Szűrésen alapuló funkció kiválasztása: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan használhatja a Azure Machine Learning szűrő alapú funkció-kiválasztási modulját a legnagyobb prediktív teljesítménnyel rendelkező adatkészlet funkcióinak azonosítására.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2020
ms.openlocfilehash: f4a7f5581703ae6932f3b40e62085fed76f5e6f2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91945702"
---
# <a name="filter-based-feature-selection"></a>Szűrőalapú szolgáltatásválasztás

Ez a cikk azt ismerteti, hogyan használható a Azure Machine Learning Designer szűrő alapú funkció-kiválasztási modulja. Ez a modul segít azonosítani a bemeneti adatkészletben található azon oszlopokat, amelyek a legnagyobb prediktív teljesítménnyel rendelkeznek. 

Általánosságban a *szolgáltatás kiválasztása* arra utal, hogy a megadott kimenet alapján statisztikai teszteket alkalmazzon a bemenetekre. A cél annak meghatározása, hogy mely oszlopok legyenek a kimenet prediktív megjelenítése. A szűrésen alapuló funkció kiválasztási modulja több szolgáltatás-kiválasztási algoritmust is biztosít, amelyek közül választhat. A modul olyan korrelációs módszereket tartalmaz, mint például a Pearson korrelációs és a KHI-négyzetes értékek. 

A szűrő alapú szolgáltatás kiválasztási moduljának használatakor meg kell adnia egy adatkészletet, és azonosítania kell a címkét vagy a függő változót tartalmazó oszlopot. Ezt követően egyetlen módszert kell megadnia a funkció fontosságának méréséhez.

A modul a legjobb funkciók oszlopait tartalmazó adatkészletet jeleníti meg, a prediktív Power alapján rangsorolva. Emellett a funkciók nevét és a hozzájuk tartozó pontszámokat is megjeleníti a kiválasztott metrika alapján.  

## <a name="what-filter-based-feature-selection-is"></a>A szűrésen alapuló funkció kiválasztása  

Ennek a funkciónak a kiválasztásához a "szűrő-alapú" nevű modult kell használni, mert a kijelölt metrika a releváns attribútumok keresésére szolgál. Ezután kiszűri a redundáns oszlopokat a modellből. Egyetlen statisztikai mértéket választ ki, amely megfelel az adatainak, a modul pedig kiszámítja az egyes szolgáltatások oszlopainak pontszámát. Az oszlopokat a szolgáltatás pontszámai rangsorolják. 

A megfelelő funkciók kiválasztásával növelheti a besorolás pontosságát és hatékonyságát. 

Általában csak azokat az oszlopokat használja, amelyek a legjobb pontszámot használják a prediktív modell létrehozásához. A gyenge funkciók kiválasztásával rendelkező oszlopok maradhatnak az adatkészletben, és figyelmen kívül hagyhatók a modell létrehozásakor.  

## <a name="how-to-choose-a-feature-selection-metric"></a>Szolgáltatás kiválasztási mérőszámának kiválasztása

Az Filter-Based funkció kiválasztási modulja számos mérőszámot biztosít az egyes oszlopokban található információ értékének értékeléséhez. Ez a szakasz az egyes mérőszámok általános leírását és alkalmazási módját ismerteti. További követelményeket is megtalálhat az egyes mérőszámok használatához a [technikai megjegyzésekben](#technical-notes) és az egyes modulok konfigurálásához szükséges [utasításokban](#how-to-configure-filter-based-feature-selection) .

-   **Pearson korreláció**  

    A Pearson korrelációs statisztikája vagy a Pearson korrelációs együtthatója a statisztikai modellekben is ismert `r` értékként. Bármely két változó esetében egy olyan értéket ad vissza, amely jelzi a korreláció erősségét.

    A Pearson korrelációs együtthatóját úgy számítjuk ki, hogy a két változó egymástól való eltérését és a szórásuk szorzatát választja. A két változó méretezésének módosítása nem befolyásolja az együtthatót.  

-   **Chi négyzetes**  

    A kétirányú Chi-négyzetes teszt olyan statisztikai módszer, amely azt méri, hogy a várt értékek a tényleges eredmények legyenek. A metódus feltételezi, hogy a változók véletlenszerűek, és a független változók megfelelő mintáján alapulnak. Az eredményül kapott KHI-négyzetes statisztikai érték azt jelzi, hogy az eredmények milyen mértékben származnak a várt (véletlenszerű) eredményből.  


> [!TIP]
> Ha az egyéni funkciók kiválasztási módszeréhez másik lehetőségre van szüksége, használja az [R-parancsfájl végrehajtása](execute-r-script.md) modult. 

## <a name="how-to-configure-filter-based-feature-selection"></a>Filter-Based szolgáltatás kijelölésének konfigurálása

A standard statisztikai mérőszámot kell választania. A modul kiszámítja az egyes oszlopok közötti korrelációt: a Label oszlop és a szolgáltatás oszlopát.

1.  Adja hozzá a Filter-Based funkció kiválasztási modulját a folyamathoz. A tervezőben a **funkció kiválasztása** kategóriában található.

2. Csatlakoztasson olyan bemeneti adatkészletet, amely legalább két olyan oszlopot tartalmaz, amelyek lehetséges funkciók.  

    A **IsFeature** attribútum beállításához használja a [metaadatok szerkesztése](edit-metadata.md) modult, és győződjön meg arról, hogy az oszlop elemzése és a szolgáltatás pontszáma jön létre. 

    > [!IMPORTANT]
    > Győződjön meg arról, hogy a bemenetként megadott oszlopok lehetséges funkciók. Egy olyan oszlop például, amely egyetlen értéket tartalmaz, nem rendelkezik információs értékkel.
    >
    > Ha tudja, hogy egyes oszlopok helytelen funkciókat végeznek, akkor eltávolíthatja őket az oszlop kiválasztásával. A [metaadatok szerkesztése](edit-metadata.md) modullal is megjelölheti azokat **kategorikusként**. 
3.  A **szolgáltatás pontozási módszere** esetében válassza az alábbi, a pontszámok kiszámításakor használandó statisztikai módszerek egyikét.  

    | Metódus              | Követelmények                             |
    | ------------------- | ---------------------------------------- |
    | Pearson korreláció | A címke lehet szöveg vagy numerikus. A funkcióknak numerikusnak kell lenniük. |
    Chi négyzetes| A címkék és a szolgáltatások lehetnek szöveg vagy numerikus elemek. Használja ezt a módszert a számítási funkció fontosságához két kategorikus oszlop esetében.|

    > [!TIP]
    > Ha módosítja a kijelölt metrikát, az összes többi beállítás alaphelyzetbe áll. Ügyeljen arra, hogy először ezt a beállítást állítsa be.
4.  Jelölje be a csak szolgáltatásként megjelölt oszlopok esetében a **csak a funkció bekapcsolása oszlopok** esetén lehetőséget. 

    Ha törli ezt a beállítást, a modul egy pontszámot hoz létre minden olyan oszlophoz, amely egyébként megfelel a feltételeknek, akár a **kívánt funkciók számában** megadott oszlopok számával.  

5.  A **cél oszlopnál** válassza az **oszlop kiválasztásának elindítása** lehetőséget a címke oszlop kiválasztásához név vagy az index alapján. (Az indexek egy-alapúak.)  
    A statisztikai korrelációt magában foglaló összes módszerhez címkével ellátott oszlop szükséges. A modul tervezési idejű hibát ad vissza, ha a nem címke vagy több címke oszlopot választja. 

6.  A **kívánt funkciók számának** megadásához adja meg az eredményként visszaadott szolgáltatások oszlopainak számát:  

    - A minimálisan megadható szolgáltatások közül csak egy, de azt javasoljuk, hogy növelje ezt az értéket.  

    - Ha a megadott számú kívánt funkció nagyobb, mint az adatkészlet oszlopainak száma, az összes funkció visszakerül. Még a nulla pontszámmal rendelkező funkciók is visszakerülnek.  

    - Ha kevesebb eredményt ad meg, mint a szolgáltatások oszlopai, a szolgáltatások csökkenő pontszám szerint vannak rangsorolva. A rendszer csak a legfontosabb funkciókat adja vissza. 

7.  A folyamat elküldése.

> [!IMPORTANT]
> Ha a lekérdezésen **alapuló funkció kiválasztását** fogja használni a következtetésben, akkor az [Oszlopok kiválasztása átalakítót](./select-columns-transform.md) kell használnia a szolgáltatás kiválasztott eredményének tárolására, és az [átalakítás alkalmazásával](./apply-transformation.md) alkalmaznia kell a szolgáltatás kiválasztott átalakítását a pontozási adatkészletre.
>
> A folyamat létrehozásához tekintse meg a következő képernyőképet, amely biztosítja, hogy az Oszlopok kiválasztása azonos legyen a pontozási eljáráshoz.
> [!div class="mx-imgBorder"]
> ![Mintavételezési folyamat](media/module/filter-based-feature-selection-score.png)

## <a name="results"></a>Results (Eredmények)

A feldolgozás befejezése után:

+ Az elemzett funkciók oszlopainak és a hozzájuk tartozó pontszámok teljes listájának megtekintéséhez kattintson a jobb gombbal a modulra, és válassza a **Megjelenítés** lehetőséget.  

+ Ha a szolgáltatás kiválasztási feltételei alapján szeretné megtekinteni az adathalmazt, kattintson a jobb gombbal a modulra, és válassza a **Megjelenítés** lehetőséget. 

Ha az adatkészlet kevesebb oszlopot tartalmaz, mint amennyit várt, ellenőrizze a modul beállításait. Tekintse meg a bemenetként megadott oszlopok adattípusait is. Ha például az 1 értékre állítja be a **kívánt funkciókat** , a kimeneti adatkészlet csak két oszlopot tartalmaz: a Label oszlopot és a legmagasan rangsorolt funkció oszlopot.


##  <a name="technical-notes"></a>Technikai megjegyzések  

### <a name="implementation-details"></a>Megvalósítás részletei

Ha a Pearson korrelációt egy numerikus szolgáltatáson és egy kategorikus címkén használja, a szolgáltatás pontszámának kiszámítása a következőképpen történik:  

1.  A kategorikus oszlopban minden szinten a numerikus oszlop feltételes középértékét kell kiszámítani.  

2.  A feltételes oszlop oszlopának korrelációja a numerikus oszloppal.  

### <a name="requirements"></a>Követelmények  

-   Nem hozható létre funkció-kiválasztási pontszám a **label** vagy **score** oszlopként megjelölt oszlopokhoz.  

-   Ha a metódus által nem támogatott adattípusú oszlophoz próbál pontozási módszert használni, a modul hibát jelez. Vagy nulla pontszámot fog rendelni az oszlophoz.  

-   Ha egy oszlop logikai (igaz/hamis) értékeket tartalmaz, azok a következő módon lesznek feldolgozva: `True = 1` és `False = 0` .  

-   Egy oszlop nem lehet olyan szolgáltatás, amely **címkeként** vagy **pontszámként** lett megjelölve.  

### <a name="how-missing-values-are-handled"></a>A hiányzó értékek kezelése  

-   Nem adhat meg célként (label) oszlopot olyan oszlopként, amely minden hiányzó értéket tartalmaz.  

-   Ha egy oszlop hiányzó értékeket tartalmaz, a modul figyelmen kívül hagyja azokat, amikor az oszlophoz tartozó pontszámot felszámítja.  

-   Ha egy szolgáltatásként kijelölt oszlop minden hiányzó értékkel rendelkezik, a modul nulla pontszámot rendel hozzá.   


## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) . 

