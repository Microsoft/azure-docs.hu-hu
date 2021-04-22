---
title: Képek címkézése címkézési projektben
title.suffix: Azure Machine Learning
description: Megtudhatja, hogyan készítheti elő gyorsan az adatokat egy Machine Learning egy Azure Machine Learning címkéző projektben az adatcímkézési eszközökkel.
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 07/27/2020
ms.custom: data4ml
ms.openlocfilehash: 8f1d648c38760865752c87624dfcb112933650c7
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872622"
---
# <a name="tag-images-in-a-labeling-project"></a>Képek címkézése címkézési projektben 

Miután a projekt rendszergazdája létrehozott egy adatcímkéző projektet [a](./how-to-create-labeling-projects.md#create-a-data-labeling-project) Azure Machine Learning, a címkéző eszközzel gyorsan előkészítheti az adatokat egy Machine Learning projekthez. Ez a cikk a következőt ismerteti:

> [!div class="checklist"]
> * Címkézési projektek elérése
> * A címkézési eszközök
> * Eszközök használata adott címkézési feladatokhoz

## <a name="prerequisites"></a>Előfeltételek

* Egy [Microsoft-fiók](https://account.microsoft.com/account) vagy Azure Active Directory a szervezethez és a projekthez
* Közreműködői szintű hozzáférés a címkéző projektet tartalmazó munkaterülethez.

## <a name="sign-in-to-the-workspace"></a>Bejelentkezés a munkaterületre

1. Jelentkezzen be a [Azure Machine Learning stúdió.](https://ml.azure.com)

1. Válassza ki az előfizetést és a címkézési projektet tartalmazó munkaterületet.  Szerezze be ezt az információt a projekt rendszergazdájától.

1. A **projekt megkereséhez** válassza a bal oldalon található Adatcímkék lehetőséget.  

## <a name="understand-the-labeling-task"></a>A címkézési feladat

Az adatcímkéző projektek táblázatában  válassza a projekt felirathivatkozását.

A projektre vonatkozó utasításokat láthat. Ismertetik az adatok típusát, a döntések meghozatalának és az egyéb releváns információkat. Miután elolvasta ezt az információt, válassza a Feladatok lehetőséget az oldal **tetején.**  Vagy a lap alján válassza a **Címkézés kezdőoldala lehetőséget.**

## <a name="common-features-of-the-labeling-task"></a>A címkézési feladat gyakori jellemzői

Az összes képcímkéző feladatban kiválaszthat egy megfelelő címkét vagy címkéket a projekt-rendszergazda által megadott készletből. Az első kilenc címkét a billentyűzet számbillentyűivel választhatja ki.  

A képosztályozási feladatokban egyszerre több képet is megtekinthet. Az elrendezés kiválasztásához használja a képterület fölötti ikonokat. 

Az összes megjelenített kép egyidejű kiválasztásához használja az **Összes kijelölése lehetőséget.** Az egyes képek kiválasztásához használja a kép jobb felső sarkában található kör alakú kijelölés gombot. Címke alkalmazáshoz legalább egy képet ki kell választania. Ha több képet választ ki, a rendszer minden kiválasztott címkét alkalmaz az összes kiválasztott képre.

Itt két-a-két elrendezést választottunk, és arra vagyunk készek, hogy alkalmazzuk az "Íme" címkét a maci és az orca képire. A képen a málna már fel van címkézve "Cartilaginous cartilaous cart" címkével, és az iguana még nem lett megcímkézve.

![Több képelrendezés és -kijelölés](./media/how-to-label-images/layouts.png)

> [!Important] 
> Csak akkor váltson elrendezést, ha friss, címkézetlen adatokat tartalmaz. Az elrendezésváltás törli az oldal folyamatban lévő címkézési munkáját.

Az Azure engedélyezi **a Küldés** gombot, ha az oldalon található összes képet felcímkézték. A **mentéshez** válassza a Küldés lehetőséget.

Miután beküldi a címkéket az adott adatokhoz, az Azure frissíti az oldalt a munkasorból származó új képekkel.

### <a name="assisted-machine-learning"></a>Személyes gépi tanulás

A gépi tanulási algoritmusok aktiválva lehetnek. Ha ezek az algoritmusok engedélyezve vannak a projektben, a következőt láthatja:

* Bizonyos mennyiségű kép címkézése után a  projekt neve mellett a képernyő tetején fürtözött feladatok is megjelenik.  Ez azt jelenti, hogy a képek egy csoportba vannak csoportosítva, hogy hasonló képeket jelenülnek meg ugyanazon az oldalon.  Ha igen, váltson a több képnézet egyikére, hogy kihasználja a csoportosítás előnyeit.  

* Egy későbbi időpontban a projekt neve **mellett** előre címkével láthatja a Tasks (Feladatok) címkét.  A képek ezután egy javasolt címkével jelennek meg, amely egy gépi tanulási besorolási modellből származik. Egyetlen gépi tanulási modell sem rendelkezik 100%-os pontossággal. Bár csak olyan képeket használunk, amelyeknél a modell magabiztos, előfordulhat, hogy ezek a képek helytelenül előre vannak címkével helyezve.  Ha látja ezeket a címkéket, javítsa ki a helytelen címkéket az oldal elküldése előtt.  

* Az objektumazonosítási modellek esetében előfordulhat, hogy már jelen vannak határolókeretek és címkék.  Javítsa ki a helyteleneket az oldal elküldése előtt.

* Szegmentációs modellek esetén előfordulhat, hogy már jelen vannak sokszögek és címkék.  Javítsa ki a helyteleneket az oldal elküldése előtt. 

A címkézési projekt korai szakaszában a gépi tanulási modell elég pontos lehet ahhoz, hogy a képek egy kis részkészletét előcímkézze. A képek címkézése után a címkézési projekt visszatér a manuális címkézéshez, hogy további adatokat gyűjtsön a modell következő betanítása során. Idővel a modell egyre biztosabb lesz a képek nagyobb arányában, így a projekt későbbi része több előcímkés feladatot eredményez.

## <a name="tag-images-for-multi-class-classification"></a>Képek címkézése többosztályos besoroláshoz

Ha a projekt típusa "Képbesorolás többosztályos", egyetlen címkét fog hozzárendelni a teljes képhez. Az utasításokat bármikor áttekintheti az Utasítások  oldalon, és válassza a **Részletes utasítások megtekintése lehetőséget.**

Ha rájön, hogy hibát vétett, miután hozzárendelt egy címkét egy képhez, kijavíthatja. A címke **jelölésének** kijelöléséhez válassza a kép alatt megjelenő címke X betűjét. Vagy válassza ki a képet, és válasszon egy másik osztályt. Az újonnan kiválasztott érték lecseréli a korábban alkalmazott címkét.

## <a name="tag-images-for-multi-label-classification"></a>Képek címkézése többcímkés besoroláshoz

Ha egy "Képbesorolási többcímkés" típusú projekten dolgozik,  egy vagy több címkét fog alkalmazni egy képre. A projektspecifikus utasítások megtekintéséhez válassza az **Utasítások lehetőséget,** majd a **Részletes utasítások megtekintése lehetőséget.**

Válassza ki a címkézni kívánt képet, majd válassza ki a címkét. A rendszer alkalmazza a címkét az összes kiválasztott képre, majd a rendszer nem választja ki a képeket. További címkék alkalmazáshoz újra ki kell kijelölését alkalmaznia a képeken. Az alábbi animáció többcímkés címkézést mutat be:

1. **Válassza az Összes** lehetőséget az "Ocean" címke alkalmazásával.
1. Egyetlen kép van kiválasztva, és a "Closeup" (Közelítés) címkét választja.
1. Három kép van kiválasztva, és a "Széles szög" címkével vannak megjelölve.

![Többcímkés folyamatot bemutató animáció](./media/how-to-label-images/multilabel.gif)

A hiba kijavítása előtt kattintson az **"X"** gombra egy adott címke törlésével, vagy válassza ki a képeket, majd válassza ki a címkét, amely törli a címkét az összes kiválasztott képből. Ez a forgatókönyv itt látható. Ha a "Land" elemre kattint, a rendszer törli ezt a címkét a két kiválasztott képből.

![Több szakaszt bemutató képernyőkép](./media/how-to-label-images/multiple-deselection.png)

Az Azure csak akkor engedélyezi a **Küldés** gombot, ha legalább egy címkét alkalmazott mindegyik képre. A **munka mentéshez** válassza a Küldés lehetőséget.


## <a name="tag-images-and-specify-bounding-boxes-for-object-detection"></a>Képek címkézése és határoló keretek meghatározása az objektumok észleléséhez

Ha a projekt típusa "Objektumazonosítás (határolókeretek), akkor a képen egy vagy több határolókeretet kell megadnia, és mindegyik mezőre alkalmaznia kell egy címkét. A képek több határolókeretet is tartalmaznak, amelyek mindegyikének egyetlen címkéje van. A **Részletes utasítások megtekintése használatával** megállapíthatja, hogy több határolókeret van-e használatban a projektben.

1. Válasszon egy címkét a létrehozni kívánt határolókerethez.
1. Jelölje ki **a Téglalap alakú doboz** ![ eszközt, vagy válassza az ](./media/how-to-label-images/rectangular-box-tool.png) "R" lehetőséget.
3. A cél átlósan húzással hozzon létre egy nagy határolókeretet. A határolókeret beállításához húzza az éleket vagy a sarkokat.

![Határolókeret létrehozása](./media/how-to-label-images/bounding-box-sequence.png)

Ha törölni szeretne egy határolókeretet, kattintson a határolókeret mellett a létrehozás után megjelenő X-alakú célra.

Meglévő határolókeret címkéje nem változtathatja meg. Ha hibát vét a címke-hozzárendelésben, törölnie kell a határolókeretet, és létre kell hoznia egy újat a megfelelő címkével.

Alapértelmezés szerint szerkesztheti a meglévő határolókereteket. A **Régiók zárolása/feloldása eszköz** Régiók zárolása/zárolásának feloldása eszköz vagy az ![ ](./media/how-to-label-images/lock-bounding-boxes-tool.png) "L" kapcsolóval bekapcsolja ezt a viselkedést. Ha a régiók zárolva vannak, csak egy új határolókeret alakját vagy helyét módosíthatja.

A Régiók **manipuláló** eszköz használata Ez a régiókat manipulációs eszköz ikonja – négy, a középponttól ![ kifelé mutató nyíl, felfelé, jobbra, lefelé és balra.](./media/how-to-label-images/regions-tool.png) vagy "M" a meglévő határolókeret beállításához. Húzza az éleket vagy sarkokat az alakzat módosításához. Kattintson a belsőre a teljes határolókeret húzáshoz. Ha nem tud szerkeszteni egy régiót, valószínűleg ki-bekapcsolta a Régiók **zárolása/feloldása** eszközt.

Több **azonos méretű** határolókeret létrehozásához használja a Sablonalapú doboz eszközt Vagy a ![ ](./media/how-to-label-images/template-box-tool.png) "T" eszközt. Ha a képen nincsenek határolókeretek, és Ön sablonalapú mezőket aktivál, az eszköz 50 by-50 képpontos mezőket hoz létre. Ha létrehoz egy határolókeretet, majd aktiválja a sablonalapú mezőket, minden új határolókeret az utoljára létrehozott mező mérete lesz. A sablonalapú mezők az elhelyezést követően átméretezhetők. A sablonalapú mező átméretezése csak az adott mezőt méretezi át.

Az aktuális *képen* található határolókeretek törléséhez válassza az Összes régió törlése eszköz Régiók törlése  ![ eszközét. ](./media/how-to-label-images/delete-regions-tool.png)

Miután létrehozott egy kép határolókeretét, válassza a **Küldés** lehetőséget a munka mentésére, különben a folyamatban lévő munka nem lesz mentve.

## <a name="tag-images-and-specify-polygons-for-image-segmentation"></a>Képek címkézése és sokszögek megadása képszegmentáláshoz 

Ha a projekt típusa "Példányszegmentálás (Sokszög), akkor a képen egy vagy több sokszöget kell megadnia, és mindegyik sokszögre alkalmaznia kell egy címkét. A képek több határoló sokszöget is tartalmaznak, amelyek mindegyikének egyetlen címkéje van. A **Részletes utasítások megtekintése segítségével** megállapíthatja, hogy több határoló sokszög van-e használatban a projektben.

1. Válassza ki a létrehozni kívánt sokszög címkét.
1. Válassza a **Sokszög-terület rajzolás eszköz** ![ Sokszög-régió rajzolás ](./media/how-to-label-images/polygon-tool.png) eszközét, vagy válassza a "P" lehetőséget.
1. Kattintson a sokszög minden pontjára.  Az alakzat befejezése után kattintson duplán a befejezéshez.

    :::image type="content" source="media/how-to-label-images/polygon.gif" alt-text="Sokszögek létrehozása macska és kutya számára":::

Sokszög törléséhez kattintson a sokszög mellett a létrehozás után megjelenő X-alakú célra.

Ha módosítani szeretné egy sokszög címkét,  válassza a Régió áthelyezése eszközt, kattintson a sokszögre, és válassza ki a megfelelő címkét.

Szerkesztheti a meglévő sokszögeket. A **Régiók zárolása/zárolásának feloldása** eszköz Sokszögek szerkesztése a zárolási/feloldási régiók eszközével, vagy az "L" kapcsolóval bekapcsolja ![ ezt a ](./media/how-to-label-images/lock-bounding-boxes-tool.png) viselkedést. Ha a régiók zárolva vannak, csak egy új sokszög alakját vagy helyét módosíthatja.

Használja a **Sokszögpontok hozzáadása** vagy eltávolítása eszközt Ez a sokszögpontok hozzáadása vagy eltávolítása ![ eszközikon.](./media/how-to-label-images/add-remove-points-tool.png) vagy "U" a meglévő sokszög módosításához. Kattintson a sokszögre egy pont hozzáadásához vagy eltávolításához. Ha nem tud szerkeszteni egy régiót, valószínűleg ki-bekapcsolta a Régiók **zárolása/feloldása** eszközt.

Az aktuális *képen található* összes sokszög  törléséhez válassza az Összes régió törlése eszközt Az összes régió ![ törlése ](./media/how-to-label-images/delete-regions-tool.png) eszköz.

Miután létrehozott egy sokszöget egy  képhez, válassza a Küldés lehetőséget a munka mentésére, különben a folyamatban lévő munka nem lesz mentve.

## <a name="finish-up"></a>Befejező lépések

A címkézett adatok oldalának elküldésekor az Azure új címkézetlen adatokat rendel hozzá Az Azure egy munkasorból. Ha nincs több elérhető címkézetlen adat, egy üzenet jelenik meg, amely a portál kezdőlapjára mutató hivatkozást is tartalmaz.

Ha végzett a címkézésvel, válassza ki a nevét a címkézési portál jobb felső sarkában, majd válassza a **Kijelentkezés lehetőséget.** Ha nem jelentkezik ki, az Azure végül "időkorábbra" kiveszi az adatokat, és hozzárendeli az adatokat egy másik címkézőhez.

## <a name="next-steps"></a>Következő lépések

* [Képbesorolási modellek betanítása az Azure-ban](./tutorial-train-models-with-aml.md)


