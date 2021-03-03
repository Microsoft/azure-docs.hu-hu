---
title: Adatcímkéző projekt létrehozása
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhat létre és futtathat címkéző projekteket a gépi tanulásra vonatkozó adatcímkézéshez. A feladattal való segítségnyújtáshoz használjon ML-vel támogatott címkézést vagy humán-t a hurok címkézése során.
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 07/27/2020
ms.custom: data4ml
ms.openlocfilehash: 62801d40295762b0066f0d2887d7d528ee7b7c2a
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101656822"
---
# <a name="create-a-data-labeling-project-and-export-labels"></a>Adatcímkéző projekt létrehozása és címkék exportálása 

Megtudhatja, hogyan hozhat létre és futtathat adatcímkéző projekteket Azure Machine Learningban található adatcímkézéshez.  A feladattal való segítségnyújtáshoz használja a gépi tanulással támogatott adatfeliratot, vagy az emberi-in-the-loop feliratot.


## <a name="data-labeling-capabilities"></a>Adatcímkéző képességek

> [!Important]
> Az adatlemezképeknek elérhetőnek kell lenniük egy Azure Blob-adattárban. (Ha nem rendelkezik meglévő adattárral, képeket tölthet fel a projekt létrehozása során.)

A Azure Machine Learning adatfelirat központi hely a címkézési projektek létrehozásához, kezeléséhez és figyeléséhez:
 - Az adatok, címkék és csoporttagok összehangolása a címkézési feladatok hatékony kezelésére. 
 - Nyomon követi a folyamat előrehaladását, és fenntartja a hiányos címkéző feladatok várólistáját.
 - Indítsa el és állítsa le a projektet, és szabályozza a címkézési folyamat állapotát.
 - Tekintse át a címkével ellátott adatokat, és az exportálást kókusz formátumban vagy Azure Machine Learning adatkészletként.

## <a name="prerequisites"></a>Előfeltételek

* A címkével ellátni kívánt, helyi vagy Azure Blob Storage-beli adatfájlok.
* Az alkalmazni kívánt címkék halmaza.
* A címkézésre vonatkozó utasítások.
* Azure-előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://aka.ms/AMLFree).
* Machine Learning munkaterület. Lásd: [Azure Machine learning munkaterület létrehozása](how-to-manage-workspace.md).

## <a name="create-a-data-labeling-project"></a>Adatcímkéző projekt létrehozása

A címkézési projektek Azure Machine Learningból vannak felügyelve. A projektek **címkézése** lap segítségével kezelheti a projekteket.

Ha az adatai már az Azure Blob Storage-ban vannak, akkor a címkézési projekt létrehozása előtt elérhetővé kell tenni az adattárként. Az adattár használatával kapcsolatos példát az [oktatóanyag: az első képosztályozási címkézési projekt létrehozása](tutorial-labeling.md)című témakörben talál.

Projekt létrehozásához válassza a **projekt hozzáadása** lehetőséget. Adjon megfelelő nevet a projektnek, és válassza ki a **feladattípus címkézése** elemet. A projekt neve nem használható újra, még akkor is, ha a projekt a jövőben is törlődik.

:::image type="content" source="media/how-to-create-labeling-projects/labeling-creation-wizard.png" alt-text="Projekt-létrehozási varázsló címkézése":::

* Válassza a **Képbesorolás többosztályos** projektek számára lehetőséget, ha csak *egyetlen címkét* szeretne alkalmazni egy képcsoportból egy képből.
* Válassza a **Képbesorolás több címkét** a projektekhez lehetőséget, ha egy *vagy több* címkét szeretne alkalmazni egy képcsoportból egy képből. Előfordulhat például, hogy egy kutya fényképét a *kutya* és a *nappali* is címkézi.
* Válassza az **objektum azonosítása (határolókeret)** lehetőséget a projektek esetében, ha címkét és egy határoló mezőt szeretne hozzárendelni egy képen található minden objektumhoz.
* Válassza a **példányok szegmentálása (sokszög) (előzetes verzió)** lehetőséget a projektekhez, ha egy címkét szeretne hozzárendelni, és rajzoljon egy sokszöget a képen belül minden objektumhoz.

> [!IMPORTANT]
> A példányok szegmentálása nyilvános előzetes verzióban érhető el.
> Az előzetes verzió szolgáltatási szintű szerződés nélkül van megadva, és nem ajánlott éles számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ha készen áll a folytatásra, válassza a **tovább** lehetőséget.

## <a name="specify-the-data-to-label"></a>A címkéhez használandó adatértékek meghatározása

Ha már létrehozott egy adatkészletet, amely az adatokat tartalmazza, válassza ki azt a **meglévő adatkészlet kiválasztása** legördülő listából. Vagy válassza az **adatkészlet létrehozása** meglévő Azure-adattár használatára vagy a helyi fájlok feltöltéséhez lehetőséget.

> [!NOTE]
> Egy projekt legfeljebb 500 000 képet tartalmazhat.  Ha az adatkészlet több is van, akkor csak az első 500 000 lemezkép lesz betöltve.  

### <a name="create-a-dataset-from-an-azure-datastore"></a>Adatkészlet létrehozása Azure-adattárból

Sok esetben elég a helyi fájlok feltöltése. A [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) azonban gyorsabb és hatékonyabb megoldást kínál nagy mennyiségű adatok átvitelére. A fájlok áthelyezésének alapértelmezett módja a Storage Explorer.

Adatkészlet létrehozása az Azure Blob Storage-ban már tárolt adatokból:

1. Válassza az **adatkészlet létrehozása**  >  **adattárból** lehetőséget.
1. Rendeljen egy **nevet** az adatkészlethez.
1. Válassza a **fájl** lehetőséget **adatkészlet típusaként**.  Csak a fájl típusú adatkészletek támogatottak.
1. Válassza ki az adattárt.
1. Ha az adatai a blob Storage-ban található almappában találhatók, válassza a **Tallózás** lehetőséget az elérési út kiválasztásához.
    * Fűzze hozzá a "/* *" utótagot az elérési úthoz, hogy az tartalmazza a kijelölt útvonal almappáiban található összes fájlt.
    * A "* */* . *" hozzáfűzésével adja meg az aktuális tárolóban és annak almappáiban lévő összes adatforrást.
1. Adja meg az adatkészlet leírását.
1. Kattintson a **Tovább** gombra.
1. Erősítse meg a részleteket. A **vissza** gombra kattintva módosíthatja a beállításokat, vagy **létrehozhatja** az adatkészletet.


### <a name="create-a-dataset-from-uploaded-data"></a>Adatkészlet létrehozása a feltöltött adatokból

Az adatok közvetlen feltöltéséhez:

1. Válassza az **adatkészlet létrehozása**  >  **helyi fájlokból** lehetőséget.
1. Rendeljen egy **nevet** az adatkészlethez.
1. Válassza a "fájl" lehetőséget az **adatkészlet típusaként**.
1. Nem *kötelező:* Válassza a **Speciális beállítások** lehetőséget az adattár, a tároló és az adatelérési út testreszabásához.
1. Válassza a **Tallózás** lehetőséget a feltölteni kívánt helyi fájlok kiválasztásához.
1. Adja meg az adatkészlet leírását.
1. Kattintson a **Tovább** gombra.
1. Erősítse meg a részleteket. A **vissza** gombra kattintva módosíthatja a beállításokat, vagy **létrehozhatja** az adatkészletet.

A rendszer feltölti az adatait a Machine Learning munkaterület alapértelmezett blob-tárolójába ("workspaceblobstore").

## <a name="configure-incremental-refresh"></a><a name="incremental-refresh"></a> Növekményes frissítés konfigurálása

Ha új lemezképeket szeretne hozzáadni az adatkészlethez, a Növekményes frissítés használatával adja hozzá ezeket az új lemezképeket a projekthez.   Ha a **Növekményes frissítés** engedélyezve van, a rendszer rendszeres időközönként ellenőrzi, hogy az új lemezképek felvehetők-e a projektbe a címkézési befejezési arány alapján.   Az új adatértékek ellenőrzése leáll, ha a projekt tartalmazza a maximális 500 000-es képet.

Ha további lemezképeket szeretne hozzáadni a projekthez, a [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) használatával töltse fel a blob Storage megfelelő mappájába. 

Jelölje be a **Növekményes frissítés engedélyezése** jelölőnégyzetet, ha azt szeretné, hogy a projekt folyamatosan figyelje az adattár új adatait. Ezeket az adategységeket a rendszer naponta egyszer lekéri a projektbe, így várnia kell, hogy új adatait vegye fel az adattárba, mielőtt a projektben megjelenik.  A projekt **részletek** lapjának **Növekményes frissítés** szakaszában láthatja, hogy mikor frissült az adatok utolsó frissítése.

Törölje a jelet a jelölőnégyzetből, ha nem szeretné, hogy az adattárban megjelenő új lemezképek megjelenjenek a projekthez.

## <a name="specify-label-classes"></a>Címke osztályok meghatározása

A **címkézési osztályok** lapon adja meg az adatkategorizálni kívánt osztályok készletét. A címkéző pontosságát és sebességét az osztályok közötti választás befolyásolhatja. Például a növények vagy állatok teljes nemzetségének és fajainak helyesírása helyett használjon mezőkód vagy rövidítse a nemet.

Soronként egy címkét adjon meg. **+** Új sor hozzáadásához használja a gombot. Ha több mint 3 vagy 4 címke van, de kevesebb, mint 10, érdemes lehet előtagot ("1:", "2:") használni, hogy a jelölők a számgombok használatával gyorsítsák fel a munkájukat.

## <a name="describe-the-data-labeling-task"></a>Az adatfelirat-feladat leírása

Fontos, hogy egyértelműen ismertesse a címkézési feladatot. A **címkézési utasítások** lapon hozzáadhat egy külső webhelyre mutató hivatkozást az utasítások címkézéséhez, vagy megadhatja az oldalon található Szerkesztés mezőben megjelenő utasításokat is. Tartsa meg az utasítások feladatait és a célközönségnek megfelelőt. Vegye figyelembe a következő kérdéseket:

* Mik a megjelenő címkék, és ezek közül választhatnak? Van egy hivatkozási szöveg, amelyre hivatkozni kell?
* Mi a teendő, ha nem tűnik megfelelő címkének?
* Mi a teendő, ha több címke is megfelelőnek tűnik?
* Milyen megbízhatósági küszöbértéket kell alkalmazni a címkére? Szeretné a "legjobb becslést", ha nem biztos benne?
* Mi a teendő a részben lefedett vagy átfedésben lévő objektumokkal?
* Mi a teendő, ha egy érdekes objektumot levágja a rendszerkép széle?
* Mi a teendő, ha a címkét elküldték, ha úgy vélik, hogy hiba történt?

A határoló mezőkhöz a következő fontos kérdések tartoznak:

* Hogyan van definiálva a feladathoz tartozó határoló mező? Teljes egészében az objektum belsejében kell lennie, vagy a külseje legyen? Ha a lehető legszorosabban kell lenyírni, vagy a kimaradás elfogadható?
* Milyen szintű ellátást és következetességet vár a címkéző a határoló mezők definiálásakor?
* Hogyan lehet címkével ellátni a képen részben látható objektumot? 
* Hogyan címkézheti az objektumot, amelyet részben egy másik objektum is lefedi?

>[!NOTE]
> Ügyeljen arra, hogy a címkéző az 1-9-as számú kulcsok használatával kiválaszthatják az első 9 címkét.

## <a name="use-ml-assisted-data-labeling"></a>ML által támogatott adatfeliratok használata

A **ml-támogatott címkézési** oldal lehetővé teszi az automatikus gépi tanulási modellek aktiválását a címkézési feladat felgyorsításához. A címkézési projekt elején a rendszer véletlenszerű sorrendben rendezi a képeket a lehetséges torzulások csökkentése érdekében. A betanított modellben azonban megjelennek az adatkészletben található összes torzítás is. Ha például a lemezképek 80%-a egyetlen osztályból áll, akkor a modell betanításához használt adatmennyiség körülbelül 80%-a az adott osztály lesz. Ez a képzés nem tartalmazza az aktív tanulást.


Válassza a *ml támogatott címkézés engedélyezése* lehetőséget, és adjon meg egy GPU-t a támogatott címkézés engedélyezéséhez, amely két fázisból áll:
* Fürtözés
* Előcímkézés

A támogatott címkézés indításához szükséges címkézett képek pontos száma nem rögzített szám.  Ez jelentősen változhat az egyik címkéző projektből a másikra. Egyes projektek esetében időnként előfordulhat, hogy a 300-es lemezképek manuális címkézése után megtekintheti az előcímke vagy a fürt feladatait. A ML-támogatással rendelkező címkézés az *átvitel elsajátítása* nevű technikát használja, amely egy előre betanított modellt használ a betanítási folyamat elindításához. Ha az adatkészlet osztályai hasonlóak az előre betanított modellhez, az előzetes feliratok csak néhány száz manuálisan címkézett kép után érhetők el. Ha az adatkészlet jelentősen eltér a modell előzetes betanításához használt adatoktól, sokkal hosszabb időt vehet igénybe.

Mivel a végső címkék továbbra is a Labeler származó adatokra támaszkodnak, ezt a technológiát néha *emberinek is nevezik a hurok* címkézése során.

> [!NOTE]
> A ML-ben támogatott adatfeliratok nem támogatják a [virtuális hálózat](how-to-network-security-overview.md)mögött biztonságossá tett alapértelmezett Storage-fiókokat. A ML által támogatott adatcímkézéshez nem alapértelmezett Storage-fiókot kell használnia. A nem alapértelmezett Storage-fiók a virtuális hálózat mögött is biztonságossá tehető.

### <a name="clustering"></a>Fürtözés

Bizonyos számú címke elküldése után a lemezkép besorolására szolgáló gépi tanulási modell elkezdi a hasonló rendszerképek csoportosítását.  Ezeket a hasonló képeket a jelölők ugyanazon a képernyőn jelennek meg a manuális címkézés felgyorsításához. A fürtözés különösen akkor hasznos, ha a Labeler 4, 6 vagy 9 rendszerképből álló rácsot tekint meg. 

Miután a gépi tanulási modellt betanítják a manuálisan címkézett adataira, a modell az utolsó teljesen csatlakoztatott rétegre lesz csonkítva. A címke nélküli képeket a rendszer átadja a csonkolt modellnek egy, a "beágyazás" vagy "featurization" néven ismert folyamaton. Ez beágyazza az egyes képeket egy, a modell rétegében definiált, nagy dimenziós területre. A rendszer a területhez legközelebbi szomszédokkal rendelkező képeket használja a fürtözési feladatokhoz. 

A fürtözési fázis nem jelenik meg az objektumok észlelési modelljeinél.

### <a name="prelabeling"></a>Előcímkézés

A megfelelő képfeliratok elküldése után a rendszer egy besorolási modellt használ a képcímkék előrejelzéséhez. Vagy egy objektum-észlelési modellt használ a határolókeretok előrejelzésére. A Labeler mostantól olyan lapokat lát, amelyek az egyes képeken már szerepelnek előre jelzett címkéket. Az objektumok észleléséhez az előre jelzett mezők is megjelennek. A feladat az, hogy áttekintse ezeket az előrejelzéseket, és javítsa ki a helytelenül címkézett képeket az oldal elküldése előtt.  

Miután a gépi tanulási modellt betanítják a manuálisan címkézett adatokra, a modell kiértékelése manuálisan címkézett képekből történik, hogy meghatározza a pontosságot a különböző megbízhatósági küszöbértékeken. Ez a kiértékelési folyamat egy olyan megbízhatósági küszöbérték meghatározására szolgál, amely felett a modell elég pontos ahhoz, hogy megjelenjenek az előzetes feliratok. Ezt követően a modell kiértékelése a címke nélküli adatértékekkel történik. Az előcímkézéshez használt, a küszöbértéknél jobban megjelenő képek.

## <a name="initialize-the-data-labeling-project"></a>Az adatcímkéző projekt inicializálása

A címkézési projekt inicializálását követően a projekt egyes szempontjai nem változtathatók meg. A feladattípus vagy az adatkészlet nem módosítható. A címkék és a feladat leírásának URL-címe *is* módosítható. A projekt létrehozása előtt gondosan tekintse át a beállításokat. A projekt elküldése után visszaadja az **Adatcímkéző** kezdőlapjának, amely a projekt **inicializálását** fogja megjeleníteni.

> [!NOTE]
> Előfordulhat, hogy a lap nem frissül automatikusan. Ezért egy szüneteltetés után manuálisan frissítse az oldalt, hogy megtekintse a projekthez **létrehozott** állapotot.

## <a name="run-and-monitor-the-project"></a>A projekt futtatása és figyelése
A projekt inicializálását követően az Azure elindítja a futtatását. Válassza ki a projektet a fő **adatfelirat** lapon a projekt részleteinek megtekintéséhez.

A projekt szüneteltetéséhez vagy újraindításához kapcsolja be a **futó** állapotot a jobb felső sarokban. Csak akkor címkézheti az adatfeliratot, ha a projekt fut.

### <a name="dashboard"></a>Irányítópult

Az **irányítópult** lapon látható a címkézési feladat állapota.

:::image type="content" source="media/how-to-create-labeling-projects/labeling-dashboard.png" alt-text="Adatcímkéző irányítópult":::

Az állapotjelző diagramon látható, hogy hány elem van megcímkézve, és hogy hányat még nem végeztek el.  A függőben lévő elemek a következők lehetnek:

* Még nincs hozzáadva egy feladathoz
* Egy Labeler rendelt feladatban szerepel, de még nem fejeződött be 
* A feladatok várólistáján még hozzá kell rendelni

A középső szakasz a feladat-várólistát jeleníti meg, amelyet még hozzá kell rendelni. Ha a ML által támogatott címkézés ki van kapcsolva, ez a szakasz a hozzárendelni kívánt manuális feladatok számát mutatja. Ha a ML által támogatott címkézés be van kapcsolva, ez a következőket is megjeleníti:

* A várólistában fürtözött elemeket tartalmazó feladatok
* A várólistában lévő, előcímkével ellátott elemeket tartalmazó feladatok

Továbbá, ha a ML által támogatott címkézés engedélyezve van, egy kis folyamatjelző sáv jelenik meg, amikor a következő betanítási művelet bekövetkezik.  A kísérletek szakaszban hivatkozások találhatók a gépi tanulási futtatásokhoz.

* Képzés – modell betanítása a címkék előrejelzéséhez
* Ellenőrzés – meghatározza, hogy a rendszer a modell előrejelzését használja-e az elemek előzetes címkézéséhez 
* Következtetés – előrejelzési Futtatás új elemekhez
* Featurization – fürtök elemei (csak képbesorolási projektekhez)

A jobb oldalon a Befejezett feladatok címkéjének eloszlása látható.  Ne feledje, hogy egyes projekttípus esetében egy elem több címkével is rendelkezhet, ebben az esetben a címkék teljes száma nagyobb lehet, mint az összes elem.

### <a name="data-tab"></a>Az adatlap

Az **adat** lapon megtekintheti az adatkészletet, és ellenőrizheti a címkézett adatokat. Ha nem megfelelően címkézett információt lát, jelölje ki, majd válassza az **elutasítás** lehetőséget, amely eltávolítja a címkéket, és visszahelyezi az adatfelirat nélküli várólistába.

### <a name="details-tab"></a>Részletek lap

A projekt részleteinek megtekintése.  Ezen a lapon a következőket teheti:

* A projekt részleteinek és a bemeneti adatkészleteknek a megtekintése
* Növekményes frissítés engedélyezése
* A projektben címkézett kimenetek tárolásához használt tárolási tároló részleteinek megtekintése
* Címkék hozzáadása a projekthez
* A címkékhez adni kívánt utasítások szerkesztése
* A ML által támogatott címkézés részleteinek szerkesztése, beleértve az engedélyezést/letiltást

### <a name="access-for-labelers"></a>Címkékhez való hozzáférés

A munkaterülethez hozzáférő bárki megcímkézheti az adatait a projektben.  Emellett testre is szabhatja a címkéző engedélyeit, hogy hozzáférhessenek a címkézéshez, de a munkaterület vagy a címkézési projekt más részei ne legyenek elérhetők.  További részletekért lásd: [Azure Machine learning munkaterület hozzáférésének kezelése](how-to-assign-roles.md), és az [Egyéni Labeler-szerepkör](how-to-assign-roles.md#labeler)létrehozásának ismertetése.

## <a name="add-new-label-class-to-a-project"></a>Új címke osztály hozzáadása projekthez

Az adatcímkéző folyamat során előfordulhat, hogy a képek besorolásához további címkék szükségesek.  Előfordulhat például, hogy egy "ismeretlen" vagy "más" címkét szeretne felvenni a zavaró képek jelzésére.

A következő lépések segítségével adhat hozzá egy vagy több címkét egy projekthez:

1. Válassza ki a projektet a fő **adatfelirat** lapon.
1. A lap jobb felső részén állítsa be a **futást** , hogy **szüneteltetve** legyen a címkéző tevékenységének leállítása.
1. Válassza a **Részletek** fület.
1. A bal oldali listában válassza a **címke osztályok** lehetőséget.
1. A lista tetején válassza a **+ Címkék hozzáadása** ![ feliratot](media/how-to-create-labeling-projects/add-label.png)
1. Az űrlapon adja hozzá az új címkét, és válassza ki, hogyan kell továbblépni.  Mivel módosította a képekhez elérhető címkéket, kiválaszthatja, hogyan kezelje a már címkézett adatok:
    * Kezdje újra, és távolítsa el az összes meglévő címkét.  Akkor válassza ezt a lehetőséget, ha az elejétől kezdve a címkék új teljes készletével szeretne feliratot kezdeni. 
    * Kezdjen el az összes meglévő címke megtartásával.  Válassza ezt a lehetőséget, ha az összes adatfelirat nélküli értéket szeretné megjelölni, de a meglévő címkéket a korábban címkézett lemezképek alapértelmezett címkéjéként kívánja megtartani.
    * Folytatás, az összes meglévő címke megtartása. Válassza ezt a lehetőséget, ha az összes olyan adathalmazt meg szeretné őrizni, amely már meg van jelölve, és kezdje el használni az új címkét, amely még nem címkézett.
1. Szükség szerint módosítsa az utasítások lapját az új felirat (ok) hoz.
1. Miután hozzáadta az összes új címkét, a lap jobb felső sarkában a váltás **szüneteltetve** **értékre** vált a projekt újraindításához.  

## <a name="export-the-labels"></a>A címkék exportálása

A Machine Learning kísérletezéshez bármikor exportálhatja a címkézési adattípust. A képfeliratokat [kókusz formátumban](http://cocodataset.org/#format-data) vagy [Azure Machine learning adathalmazként](how-to-use-labeled-dataset.md)lehet exportálni címkékkel. Használja az **Exportálás** gombot a címkéző projekt **Project Details (projekt részletei** ) lapján.

A kókusz-fájl a Azure Machine Learning munkaterület alapértelmezett blob-tárolójában jön létre az *export/Coco* mappában lévő mappában. Az exportált Azure Machine Learning adatkészletet a Machine Learning **adatkészletek** szakaszában érheti el. Az adatkészlet részletei lap a Pythonból elérhető feliratok eléréséhez is tartalmaz mintakód-kódot.

![Exportált adatkészlet](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="troubleshooting"></a>Hibaelhárítás

Ha ezeket a problémákat látja, használja ezeket a tippeket.

|Probléma  |Feloldás  |
|---------|---------|
|Csak a blob-adattárolók által létrehozott adatkészletek használhatók.     |  Ez az aktuális kiadás ismert korlátozása.       |
|A létrehozást követően a projekt hosszú ideig az "inicializálás" kifejezést jeleníti meg.     | Manuálisan frissítse a lapot. Az inicializálásnak másodpercenként körülbelül 20 datapoints kell lennie. Az AutoFrissítés hiánya ismert probléma.         |
|Képek áttekintésekor az újonnan címkézett képek nem jelennek meg.     |   Az összes címkézett kép betöltéséhez válassza az **első** gombot. Az **első** gomb a lista elejére kerül, de az összes címkével ellátott adattal betöltődik.      |
|Az ESC billentyű lenyomásával az objektumok észlelése során a rendszer nulla méretű címkét hoz létre a bal felső sarokban. Az ebben az állapotban lévő címkék elküldése sikertelen.     |   Törölje a címkét a mellette lévő kereszt jelre kattintva.  |
|Nem lehet feladat-készletet hozzárendelni egy adott Labeler.     |   Ez az aktuális kiadás ismert korlátozása.  |

## <a name="next-steps"></a>Következő lépések

* [Oktatóanyag: az első képbesorolási címkézési projekt létrehozása](tutorial-labeling.md).
* [Képbesorolás vagy objektum-észlelési](how-to-label-images.md) lemezképek címkézése
* További információ a [Azure Machine learning és a Machine learning Studio (klasszikus)](./overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)
