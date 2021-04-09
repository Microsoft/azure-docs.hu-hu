---
title: Azure Application Insights használati kohorszok | Microsoft Docs
description: Különböző készletek vagy felhasználók, munkamenetek, események vagy olyan műveletek elemzése, amelyekben valami közös
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 04/10/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 2c35356951560b11a605334aba4c26dbc38086de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100583545"
---
# <a name="application-insights-cohorts"></a>Application Insights kohorszok

Az kohorsz olyan felhasználók, munkamenetek, események vagy műveletek összessége, amelyek közösen rendelkeznek. Az Azure Application Insightsban a kohorszokat egy elemzési lekérdezés határozza meg. Azokban az esetekben, amikor a felhasználók vagy események egy adott halmazát ismételten kell elemezni, a kohorszok nagyobb rugalmasságot biztosítanak, hogy pontosan azt a készletet adja meg, amelyre kíváncsi.

![Kohorszok ablaktábla](./media/usage-cohorts/001.png)

## <a name="cohorts-versus-basic-filters"></a>Kohorszok és alapszintű szűrők

A kohorszok a szűrőkhöz hasonló módon használatosak. A kohorszok definíciói azonban egyéni analitikai lekérdezésekből készültek, így sokkal jobban alkalmazkodnak és összetettebbek. A szűrőktől eltérően mentheti a kohorszokat, így a csapat más tagjai újra felhasználhatják őket.

Meghatározhatja, hogy a felhasználók milyen kohorszban próbáltak új funkciót használni az alkalmazásban. Ezt a kohorszot mentheti a Application Insights erőforrásban. A későbbiekben könnyen elemezheti az adott felhasználók mentett csoportját.

> [!NOTE]
> A létrehozásuk után a kohorszok elérhetők a felhasználók, a munkamenetek, az események és a Felhasználókövetés eszközök közül.

## <a name="example-engaged-users"></a>Példa: résztvevő felhasználók

A csapata egy résztvevő felhasználót határoz meg, aki egy adott hónapban öt vagy több alkalommal használja az alkalmazást. Ebben a szakaszban megadhatja a résztvevő felhasználók kohorszát.

1. Nyissa meg a Kohorszs eszközt.

2. Válassza a **sablon-gyűjtemény** fület. A különböző kohorszokhoz tartozó sablonok gyűjteménye látható.

3. Válassza ki **a résztvevő felhasználókat – a használat napjai szerint**.

    Az kohorsz három paramétert biztosít:
    * **Tevékenységek**, ahol kiválaszthatja, hogy mely események és oldalletöltések száma "használat".
    * **Időszak**, egy hónap definíciója.
    * **UsedAtLeastCustom**, hogy a felhasználóknak hány alkalommal kell használnia egy adott időszakon belül a befoglalt értéket.

4. Módosítsa  a UsedAtLeastCustom **5 + nap** értékre, és hagyjon **időt** az alapértelmezett 28 napra.

    ![Résztvevő felhasználók](./media/usage-cohorts/003.png)

    Most ez az kohorsz az összes egyéni eseménnyel vagy oldal nézettel ellátott felhasználói azonosítót az elmúlt 28 napban 5 külön nappal mutatja.

5. Kattintson a **Mentés** gombra.

   > [!TIP]
   > Adja meg a kohorsz nevét, például a "résztvevő felhasználók (5 + nap)" nevet. Mentse a "saját jelentések" vagy a "megosztott jelentések" kifejezésre attól függően, hogy szeretné-e, ha a Application Insights erőforráshoz hozzáférő más felhasználók is láthassák ezt a kohorszot.

6. Válassza **a vissza a** katalógushoz lehetőséget.

### <a name="what-can-you-do-by-using-this-cohort"></a>Mit tehet a következő kohorsz használatával?

Nyissa meg a felhasználók eszközt. A **Megjelenítés** legördülő listában válassza ki azokat a kohorszokat, amelyeket a **-hez tartozó felhasználók** alatt hozott létre.

A felhasználók eszköz mostantól a felhasználók ezen kohorszára van szűrve:

![Felhasználók ablaktábla egy adott kohorszra szűrve](./media/usage-cohorts/004.png)

Néhány fontos Tudnivaló:

* Ez a készlet nem hozható létre normál szűrők használatával. A dátum logikája fejlettebb.
* Az kohorszot a felhasználók eszköz normál szűrőinek használatával is szűrheti. Így bár az kohorsz a 28 napos Windowson van meghatározva, a felhasználók eszközön a 30, 60 vagy 90 napig is beállíthatja az időtartományt.

Ezek a szűrők olyan kifinomultabb kérdéseket is támogatnak, amelyeket nem lehet kifejezni a lekérdezés-szerkesztőn keresztül. Ilyenek például az _elmúlt 28 napban résztvevő személyek. Hogyan viselkedtek ugyanezek az elmúlt 60 napban?_

## <a name="example-events-cohort"></a>Példa: események kohorsz

Az események kohorszait is elvégezheti. Ebben a szakaszban megadhatja az események és a lapok nézeteinek kohorszját. Ezután láthatja, hogyan használhatja őket a többi eszközről. Ez a kohorsz meghatározhatja azokat az eseményeket, amelyeket a csapata az _aktív használatra_ vagy egy adott új szolgáltatáshoz kapcsolódó készletre gondol.

1. Nyissa meg a Kohorszs eszközt.

2. Válassza a **sablon-gyűjtemény** fület. Különböző kohorszokhoz tartozó sablonok gyűjteménye látható.

3. Válassza az **események választó** elemet.

    ![Képernyőkép az események választóról](./media/usage-cohorts/006.png)

4. A **tevékenységek** legördülő listában válassza ki azokat az eseményeket, amelyeket az kohorszban szeretne használni.

5. Mentse a kohorszt, és adjon neki nevet.

## <a name="example-active-users-where-you-modify-a-query"></a>Példa: az aktív felhasználók, akiknél módosít egy lekérdezést

Az előző két kohorsz a legördülő listák használatával lett meghatározva. De a teljes rugalmasság érdekében elemzési lekérdezésekkel is meghatározhatja a kohorszokat. Ha szeretné megtekinteni, hogyan hozhat létre az Egyesült Királyságból származó felhasználókat.

![Animált rendszerképek a Kohorszs eszköz használatával](./media/usage-cohorts/cohorts0001.gif)

1. Nyissa meg a Kohorszs eszközt, válassza a **sablon** -katalógus lapot, és válassza az **üres felhasználók kohorsz** lehetőséget.

    ![Üres felhasználók kohorsz](./media/usage-cohorts/001.png)

    Három szakaszt tartalmaz:
   * A Markdown szöveg szakasza, ahol részletesebben leírja a kohorszot a csapat többi részén.

   * A parameters (paraméterek) szakasz, amelyben a saját paramétereket, például a **tevékenységeket** és az előző két példa további legördülő mezőit hajtja végre.

   * Egy lekérdezési szakasz, ahol a kohorszt analitikai lekérdezéssel definiálhatja.

     A lekérdezési szakaszban [egy elemzési lekérdezést kell írnia](/azure/kusto/query). A lekérdezés kiválasztja a definiálni kívánt kohorszot leíró sorok bizonyos készletét. A Kohorszs eszköz implicit módon hozzáadja a következőt: "| összefoglalja user_Id "záradékot a lekérdezéshez. Ezek az adatnézetek egy tábla lekérdezése alatt tekinthetők meg, így biztos lehet benne, hogy a lekérdezés az eredményeket adja vissza.

     > [!NOTE]
     > Ha nem látja a lekérdezést, próbálja meg átméretezni a szakaszt, hogy az magasabb legyen, és láthatóvá tegye a lekérdezést. A szakasz elején található animált. gif az átméretezési viselkedést szemlélteti.

2. Másolja és illessze be a következő szöveget a lekérdezés-szerkesztőbe:

    ```KQL
    union customEvents, pageViews
    | where client_CountryOrRegion == "United Kingdom"
    ```

3. Válassza a **Lekérdezés futtatása** lehetőséget. Ha nem látja a felhasználói azonosítókat a táblázatban, váltson arra az országra/régióra, ahol az alkalmazás felhasználókkal rendelkezik.

4. Mentse és nevezze el az kohorszt.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

_Meghatároztam egy adott ország/régió felhasználóinak kohorszát. Ha összehasonlítom ezt az kohorszot a felhasználók eszközben, hogy csak az adott országra/régióra vonatkozó szűrőt állítson be, különböző eredmények jelennek meg. miért?_

A kohorszok és a szűrők eltérőek. Tegyük fel, hogy az Egyesült Királyságból (például az előző példához hasonlóan) a felhasználók kohorsza van, és összehasonlítja az eredményeit az "ország vagy régió = Egyesült Királyság" szűrő beállításához.

* A kohorsz verziója az aktuális időtartományban az Egyesült Királyságból érkező, egy vagy több eseményt küldő felhasználók összes eseményét jeleníti meg. Ha ország vagy régió szerint osztja szét, valószínűleg sok országot és régiót láthat.
* A szűrők verziója csak az Egyesült Királyságból származó eseményeket mutatja. Ha azonban ország vagy régió szerint bontotta, akkor csak az Egyesült Királyság jelenik meg.

## <a name="learn-more"></a>Tudjon meg többet

* [Elemzés lekérdezési nyelve](../logs/log-analytics-tutorial.md?toc=%2fazure%2fazure-monitor%2ftoc.json)
* [Felhasználók, munkamenetek, események](usage-segmentation.md)
* [Felhasználói folyamatok](usage-flows.md)
* [A használat áttekintése](usage-overview.md)