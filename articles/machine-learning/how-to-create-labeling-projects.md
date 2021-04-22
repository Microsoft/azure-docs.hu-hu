---
title: Adatcímkéző projekt létrehozása
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhat létre és futtathat címkézési projekteket az adatok címkézéséhez a gépi tanuláshoz. A feladat segítése érdekében használjon gépi tanulási személyes vagy emberi hurkos címkézést.
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 07/27/2020
ms.custom: data4ml
ms.openlocfilehash: ff1783dd31b8139940e56d24ae82866b428838b1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861156"
---
# <a name="create-a-data-labeling-project-and-export-labels"></a>Adatcímkéző projekt létrehozása és címkék exportálása 

Megtudhatja, hogyan hozhat létre és futtathat adatcímkéző projekteket az adatok címkézéséhez a Azure Machine Learning.  A feladat segítése érdekében használjon gépi tanulással támogatott adatcímkét vagy emberi ciklusban való címkézést.


## <a name="data-labeling-capabilities"></a>Adatcímkéző képességek

> [!Important]
> Az adatképeknek elérhetőnek kell lennie egy Azure Blob-adattárban. (Ha még nem létezik adattár, képeket tölthet fel a projekt létrehozása során.)

Azure Machine Learning adatok címkézése egy központi hely a címkézési projektek létrehozásához, kezeléséhez és figyeléséhez:
 - Koordinálhatja az adatokat, a címkéket és a csapattagokat a címkézési feladatok hatékony kezelése érdekében. 
 - Nyomon követi az előrehaladást, és fenntartja a hiányos címkézési feladatok várólistáját.
 - Indítsa el és állítsa le a projektet, és vezérelje a címkézés előrehaladását.
 - Tekintse át a címkével jelölt adatokat, és exportálja COCO vagy Azure Machine Learning címkével.

## <a name="prerequisites"></a>Előfeltételek

* A címkézni kívánt adatok a helyi fájlokban vagy az Azure Blob Storage-ban.
* Az alkalmazni kívánt címkék halmaza.
* A címkézésre vonatkozó utasítások.
* Azure-előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://aka.ms/AMLFree).
* Egy Machine Learning munkaterület. Lásd: [Munkaterület Azure Machine Learning létrehozása.](how-to-manage-workspace.md)

## <a name="create-a-data-labeling-project"></a>Adatcímkéző projekt létrehozása

A projektek címkézése a Azure Machine Learning. A projektek **kezeléséhez a** Projektek címkézése oldalt használhatja.

Ha az adatok már az Azure Blob Storage-ban vannak, a címkézési projekt létrehozása előtt érdemes elérhetővé tenni adattárként. Az adattár használatának egy példájért lásd: [Oktatóanyag: Az első képosztályozási címkézési projekt létrehozása.](tutorial-labeling.md)

Projekt létrehozásához válassza a **Projekt hozzáadása lehetőséget.** Adjon egy megfelelő nevet a projektnek, és válassza a **Címkézési feladattípus lehetőséget.** A projekt neve nem használható újra, még akkor sem, ha a projekt a jövőben törlődik.

:::image type="content" source="media/how-to-create-labeling-projects/labeling-creation-wizard.png" alt-text="Projekt-létrehozási varázsló címkézése":::

* Válassza **a Képbesorolás** Többosztályos lehetőséget  a projektekhez, ha csak egyetlen címkét szeretne alkalmazni egy képre egy címkekészletből.
* Válassza **a Képbesorolás többcímkés**  lehetőséget a projektekhez, ha címkék egy vagy több címkéjét szeretné alkalmazni egy képre. Egy kutya fényképét például a kutya és  a daytime címkével *is meg lehet címkézni.*
* A projektekhez válassza az Objektumazonosítás **(Határolókeret)** lehetőséget, ha címkét és határolókeretet szeretne hozzárendelni egy képen belüli összes objektumhoz.
* Válassza **az Instance Segmentation (Polygon) (Példányszegmentálás (Sokszög)** lehetőséget a projektekhez, ha egy címkét szeretne hozzárendelni, és egy sokszöget szeretne rajzolni a képen található egyes objektumok köré.

Ha **készen áll** a folytatásra, kattintson a Tovább gombra.

## <a name="specify-the-data-to-label"></a>A címkézni kívánt adatok megadása

Ha már létrehozott egy adatkészletet, amely tartalmazza az adatokat, válassza ki azt a **Meglévő** adatkészlet kiválasztása legördülő listából. Vagy válassza **az Adatkészlet létrehozása** lehetőséget egy meglévő Azure-adattár használatára vagy helyi fájlok feltöltésére.

> [!NOTE]
> Egy projekt legfeljebb 500 000 képet tartalmazhat.  Ha az adatkészletben több van, csak az első 500 000 kép lesz betöltve.  

### <a name="create-a-dataset-from-an-azure-datastore"></a>Adatkészlet létrehozása Azure-adattárból

Sok esetben nem gond, ha csak helyi fájlokat tölt fel. A [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) azonban gyorsabb és robusztusabb módja a nagy mennyiségű adat átvitelének. Javasoljuk, Storage Explorer alapértelmezett módszerként használja a fájlok áthelyezését.

Adatkészlet létrehozása az Azure Blob Storage-ban már tárolt adatokból:

1. Válassza **az Adatkészlet létrehozása**  >  **adattárból lehetőséget.**
1. Rendeljen **egy nevet** az adatkészlethez.
1. Az **Adatkészlet típusaként** válassza **a Fájl lehetőséget.**  Csak a fájladatkészlet-típusok támogatottak.
1. Válassza ki az adattárat.
1. Ha az adatok a blobtároló egyik almappában találhatóak, válassza a Tallózás lehetőséget az elérési út kiválasztásához. 
    * Az elérési úthoz fűzve fűzheti hozzá a "/**" kötőfájlt, hogy a kijelölt elérési út almappákba foglalja bele az összes fájlt.
    * A "* .*" hozzáfűzése az aktuális tároló és annak almappák összes */* adatát tartalmazza.
1. Adja meg az adatkészlet leírását.
1. Kattintson a **Tovább** gombra.
1. Erősítse meg a részleteket. Válassza **a Vissza** lehetőséget a beállítások módosításához, vagy a **Létrehozás** lehetőséget az adatkészlet létrehozásához.


### <a name="create-a-dataset-from-uploaded-data"></a>Adatkészlet létrehozása feltöltött adatokból

Az adatok közvetlen feltöltése:

1. Válassza **az Adatkészlet létrehozása helyi**  >  **fájlokból lehetőséget.**
1. Rendeljen **egy nevet** az adatkészlethez.
1. Az Adatkészlet típusaként válassza a **"File" (Fájl) lehetőséget.**
1. *Nem kötelező:* Válassza **a Speciális beállítások** lehetőséget az adattár, a tároló és az adatok elérési útjának testreszabásához.
1. Válassza **a Tallózás** lehetőséget a feltölteni kívánt helyi fájlok kiválasztásához.
1. Adja meg az adatkészlet leírását.
1. Kattintson a **Tovább** gombra.
1. Erősítse meg a részleteket. Válassza **a Vissza lehetőséget** a beállítások módosításához, vagy a **Létrehozás** lehetőséget az adatkészlet létrehozásához.

Az adatok a munkaterület alapértelmezett blobtárolójába ("workspaceblobstore") Machine Learning fel.

## <a name="configure-incremental-refresh"></a><a name="incremental-refresh"></a> Növekményes frissítés konfigurálása

Ha új képeket szeretne hozzáadni az adatkészlethez, a növekményes frissítés használatával adja hozzá ezeket az új képeket a projekthez.   Ha **engedélyezve van a** növekményes frissítés, a címkézési befejezési arány alapján rendszeres időközönként ellenőrzi, hogy az új képek hozzáadva lesznek-e a projekthez.   Az új adatok ellenőrzése leáll, ha a projekt legfeljebb 500 000 képet tartalmaz.

Ha további képeket szeretne hozzáadni a projekthez, Azure Storage Explorer [a](https://azure.microsoft.com/features/storage-explorer/) blobtároló megfelelő mappájába való feltöltéshez. 

Jelölje be a Növekményes **frissítés engedélyezése** jelölőnégyzetet, ha azt szeretné, hogy a projekt folyamatosan figyelje az új adatokat az adattárban. Az adatokat a rendszer naponta egyszer leküldi a projektbe, ha engedélyezve van, ezért meg kell várnia, hogy új adatokat ad hozzá az adattárhoz, mielőtt azok megjelenikek a projektben.  A projekt Részletek lapjának Növekményes frissítés  szakaszában láthatja  az adatok legutóbbi frissítésének időbélyegét.

Ha nem szeretné, hogy az adattárban megjelenő új képek megjelenjenek a projektben, törölje a jelölést a jelölőnégyzetből.

## <a name="specify-label-classes"></a>Címkeosztályok megadása

A **Címkeosztályok lapon** adja meg az adatok kategorizálásához szükséges osztálykészletet. A címkézők pontosságát és sebességét befolyásolja, hogy melyik osztály közül választhatnak. Ahelyett például, hogy a növényekre vagy állatokra vonatkozó teljes növényeket és állatokat kiíratja, mezőkódot használjon, vagy rövidelje a növényeket.

Adjon meg soronként egy címkét. Új **+** sor hozzáadásához használja a gombot. Ha 3-as vagy 4-esnél több címkével, de 10-nél kevesebbel dolgozik, akkor előfordulhat, hogy a neveket számok előtaggal ("1: ", "2: ") szeretné előtagként használni, hogy a címkézők a számkulcsokkal felgyorsítsák a munkájukat.

## <a name="describe-the-data-labeling-task"></a>Az adatcímkéző feladat leírása

Fontos egyértelműen elmagyarázni a címkézési feladatot. A **Címkézési utasítások** lapon hozzáadhat egy külső webhelyre mutató hivatkozást a címkézési utasításokhoz, vagy utasításokat adhat meg az oldal szerkesztési mezőjén. Tartsa meg az utasításokat feladatorientáltan és a célközönségnek megfelelően. Fontolja meg a következő kérdéseket:

* Milyen címkéket fognak látni, és hogyan fognak választani közöttük? Van hivatkozási szöveg, amelyre hivatkozni kell?
* Mit kell tenniük, ha nem tűnik megfelelőnek egy címke sem?
* Mit kell tenniük, ha több címke is megfelelőnek tűnik?
* Milyen megbízhatósági küszöbértéket kell alkalmazniuk egy címkére? Szeretné a "legjobb tippet", ha nem biztos benne?
* Mit kell tenniük a részlegesen eltolt vagy átfedésben lévő fontos objektumok esetén?
* Mit kell tenniük, ha egy fontos objektumot a kép széle levág?
* Mit kell tenniük, miután beküldik a címkét, ha azt gondolják, hogy hibát vétett?

A határolókeretek fontos kérdései a következők:

* Hogyan van definiálva a határolókeret ehhez a feladathoz? Teljes egészében az objektum belső részén kell lennie, vagy a házon kell lennie? A lehető legnagyobb körülvágásra van-e lehetőség, vagy elfogadható-e valamilyen elfogadható engedély?
* Milyen szintű ellátásra és konzisztenciára számít a feliratozók a határolókeretek definiálása során?
* Hogyan lehet megcímkézni a képen részlegesen látható objektumot? 
* Hogyan lehet megcímkézni a más objektumok által részben lefedett objektumot?

>[!NOTE]
> Ne feledje, hogy a címkézők az 1–9 számkulcsok használatával kiválaszthatják az első 9 címkét.

## <a name="use-ml-assisted-data-labeling"></a>Gépi tanulási támogatású adatcímkék használata

Az **ML-támogatással támogatott címkézési oldalon** automatikus gépi tanulási modelleket aktiválhat a címkézési feladat felgyorsítása érdekében. A címkézési projekt elején a képek véletlenszerű sorrendbe rendezve csökkennek a lehetséges torzítások. Az adatkészletben jelen van minden torzítás azonban megjelenik a betanított modellben. Ha például a képek 80%-a egyetlen osztályba esik, akkor a modell betanításakor használt adatok körülbelül 80%-a lesz az osztályból. Ez a képzés nem tartalmazza az aktív tanulást.


Válassza *a Gépi tanulási támogatás címkézésének engedélyezése lehetőséget,* és adjon meg egy GPU-t a két fázisból áll támogatott címkézés engedélyezéséhez:
* Fürtözés
* Előcímkézás

A támogatott címkézéshez szükséges címkével jelölt képek pontos száma nem rögzített szám.  Ez jelentős mértékben változhat a címkézési projekttől a másikhoz. Egyes projektek esetében előfordulhat, hogy 300 kép manuális címkézése után már látható az előcímkés vagy fürtfeladat. A gépi tanulással támogatott címkézés a *Transfer Learning* nevű technikát használja, amely egy előre betanított modellel indítja el a betanítási folyamatot. Ha az adatkészlet osztályai hasonlóak az előre betanított modell osztályaihoz, előfordulhat, hogy az előcímkék csak néhány száz manuálisan címkézett kép után érhetők el. Ha az adatkészlet jelentősen eltér a modell előzetes betanítása érdekében használt adatoktól, az sokkal tovább is tarthat.

Mivel a végső címkék továbbra is a címkéző bemenetére támaszkodnak, ezt a technológiát néha emberinek is nevezik a *hurok címkézésében.*

> [!NOTE]
> A gépi tanulási személyes adatok címkézése nem támogatja a virtuális hálózat mögött védett alapértelmezett [tárfiókokat.](how-to-network-security-overview.md) A gépi tanulási segédadatok címkézéséhez nem alapértelmezett tárfiókot kell használnia. A nem alapértelmezett tárfiók biztonságos a virtuális hálózat mögött.

### <a name="clustering"></a>Fürtözés

Bizonyos számú címke beküldtét követően a képosztályozás gépi tanulási modellje elkezdi csoportosítni a hasonló képeket.  Ezek a hasonló képek ugyanazon a képernyőn jelennek meg a címkézők számára, hogy felgyorsítsa a manuális címkézést. A fürtözés különösen akkor hasznos, ha a címkéző 4, 6 vagy 9 képet megjelenítő rácsot néz. 

Miután betanított egy gépi tanulási modellt a manuálisan címkézett adatokon, a modellt a rendszer az utolsó teljesen csatlakoztatott rétegére csonkolja. A címkézetlen képek ezután a csonkolt modellen keresztül vannak átküldve egy "beágyazásnak" vagy "featurizálásnak" is ismert folyamat során. Ez beágyazza az egyes képeket egy, a modellréteg által meghatározott magas dimenziós térben. A tér legközelebbi szomszédai a fürtözési feladatokhoz használatosak. 

Az objektumészlelési modellek esetében nem jelenik meg a fürtözési fázis.

### <a name="prelabeling"></a>Előcímkézás

A megfelelő képcímkék beküldése után a rendszer egy besorolási modellel előrejelezni tudja a képcímkéket. Vagy egy objektumészlelési modell használható határolókeretek előrejelzésére. A címkéző most már olyan oldalakat lát, amelyek az egyes képeken már jelen vannak előrejelzött címkékkel. Az objektumészleléshez előrejelezni is lehet a mezőket. A feladat ezután ezeknek az előrejelzéseknek az áttekintése és a helytelen címkével jelölt képek kijavítása az oldal elküldése előtt.  

Miután betanított egy gépi tanulási modellt a manuálisan címkézett adatokon, a modellt a manuálisan címkézett képek tesztkészletén értékeli ki a pontosság különböző megbízhatósági küszöbértékekkel való meghatározása érdekében. Ezzel a kiértékelési folyamattal határozható meg egy megbízhatósági küszöbérték, amely felett a modell elég pontos az előcímkék mutatása érdekében. A modell ezután a címkézetlen adatok alapján lesz kiértékelve. Az ennél a küszöbértéknél magabiztosabb előrejelzéseket a rendszer az előcímkézéshez használja.

## <a name="initialize-the-data-labeling-project"></a>Az adatcímkéző projekt inicializálása

A címkézési projekt inicializálása után a projekt néhány aspektusa nem módosítható. A feladattípust vagy az adatkészletet nem módosíthatja. *Módosíthatja* a tevékenységek leírásának címkéit és URL-címét. A projekt létrehozása előtt gondosan tekintse át a beállításokat. A projekt elküldése után vissza fog térni az Adatcímkézés **kezdőlapjára,** ahol a projekt inicializálásként **fog mutatni.**

> [!NOTE]
> Előfordulhat, hogy az oldal nem frissül automatikusan. Szüneteltetés után tehát manuálisan frissítse az oldalt, hogy a projekt Állapota **Létrehozva.**

## <a name="run-and-monitor-the-project"></a>A projekt futtatása és figyelése
A projekt inicializálása után az Azure megkezdi a futtatását. Válassza ki a projektet a fő **Adatcímkézés oldalon** a projekt részleteinek megtekintése érdekében

A projekt szüneteltetéséhez vagy újraindításához a jobb felső sarokban állítsa be a Futtatás állapotot.  Az adatokat csak akkor címkézheti, ha a projekt fut.

### <a name="dashboard"></a>Irányítópult

Az **Irányítópult** lapon látható a címkézési feladat előrehaladása.

:::image type="content" source="media/how-to-create-labeling-projects/labeling-dashboard.png" alt-text="Adatcímkéző irányítópult":::

Az előrehaladási diagramon látható, hogy hány elem lett címkézve, és hány még nem lett még megcímkézve.  A függőben lévő elemek a következőek lehetnek:

* Még nincs hozzáadva egy feladathoz
* Olyan feladat része, amely hozzá van rendelve egy címkézőhöz, de még nem fejeződött be 
* A még hozzárendelni nem rendelhető tevékenységek várólistán

A középső szakasz a még ki nem osztott feladatok várólistáját mutatja. Ha a gépi tanulási segédcímkék ki vannak kapcsolva, ez a szakasz a hozzárendelni szükséges manuális feladatok számát mutatja. Ha a gépi tanulási segédcímkék be van va, az a következőt is mutatja:

* Az üzenetsorban fürtözött elemeket tartalmazó feladatok
* Az üzenetsor előre felcímkévelt elemeit tartalmazó feladatok

Emellett ha a gépi tanulási segédcímkék engedélyezve vannak, egy kis folyamatjelző sáv jelenik meg a következő betanítás futtatásakor.  A Kísérletek szakasz hivatkozásokat tartalmaz az egyes gépi tanulási futtatásokkal kapcsolatban.

* Betanítás – modell betanítása a címkék előrejelzésére
* Érvényesítés – meghatározza, hogy a rendszer a modell előrejelzését fogja-e használni az elemek előzetes címkézése során 
* Következtetés – előrejelzés futtatása új elemekhez
* Featurizálás – fürtelemek (csak képosztályozási projektekhez)

A jobb oldalon a befejezett feladatok címkéinek eloszlása található.  Ne feledje, hogy egyes projekttípusokban egy elem több címkével is lehet, amely esetben a címkék teljes száma nagyobb lehet az összes számelemnél.

### <a name="data-tab"></a>Adatok lap

Az Adatok **lapon** láthatja az adatkészletet, és áttekintheti a címkével jelölt adatokat. Ha helytelenül címkézett adatokat lát, jelölje ki őket, és válassza az **Elutasítás** lehetőséget, amely eltávolítja a címkéket, és visszatenheti az adatokat a címkézetlen üzenetsorba.

### <a name="details-tab"></a>Részletek lap

Tekintse meg a projekt részleteit.  Ezen a lapon a következőt használhatja:

* A projekt részleteinek és a bemeneti adatkészletek megtekintése
* Növekményes frissítés engedélyezése
* A címkével jelölt kimenetek projektben való tárolásához használt Storage-tároló részleteinek megtekintése
* Címkék hozzáadása a projekthez
* A címkéknek adott utasítások szerkesztése
* Gépi tanulási segédcímkék részleteinek szerkesztése, beleértve az engedélyezést/letiltását

### <a name="access-for-labelers"></a>Hozzáférés a címkézők számára

A projekt adatait bárki felcímkézheti, aki hozzáféréssel rendelkezik a munkaterülethez.  Az engedélyeket testre is szabhatja a címkézők számára, hogy hozzáférnek a címkézéshez, de a munkaterület vagy a címkézési projekt más részeihez ne.  További részletekért lásd: Manage access to an Azure Machine Learning workspace (Hozzáférés kezelése egy [Azure Machine Learning-munkaterülethez)](how-to-assign-roles.md)és további információ a címkéző egyéni [szerepkörének létrehozásáról.](how-to-assign-roles.md#labeler)

## <a name="add-new-label-class-to-a-project"></a>Új címkeosztály hozzáadása projekthez

Az adatcímkéző folyamat során előfordulhat, hogy további címkékre van szükség a képek besorolásához.  Előfordulhat például, hogy egy "Ismeretlen" vagy "Egyéb" címkét szeretne hozzáadni, amely zavaros képeket jelez.

Az alábbi lépésekkel adhat hozzá egy vagy több címkét egy projekthez:

1. Válassza ki a projektet a fő **Adatcímkézés** oldalon.
1. Az oldal jobb felső részen  állítsa  a Futó gombra a Szüneteltetve gombra, hogy a címkézők ne tevékenységet folytatnak.
1. Válassza a **Részletek** fület.
1. A bal oldali listában válassza a **Címkeosztályok lehetőséget.**
1. A lista tetején válassza a **+ Felirat hozzáadása** Felirat hozzáadása ![ lehetőséget](media/how-to-create-labeling-projects/add-label.png)
1. Az űrlapon adja hozzá az új címkét, és válassza ki a folytatást.  Mivel módosította egy kép elérhető címkéit, kiválaszthatja, hogyan kezelje a már címkével jelölt adatokat:
    * Kezdje elölről, és távolítsa el az összes meglévő címkét.  Akkor válassza ezt a lehetőséget, ha a címkézést az elejétől szeretné kezdeni az új teljes címkekészlettel. 
    * Kezdje elölről, és tartsa meg az összes meglévő címkét.  Válassza ezt a lehetőséget, ha minden adatot címkézetlenként jelöl meg, de tartsa meg a meglévő címkéket a korábban címkével jelölt képek alapértelmezett címkéjeként.
    * Folytassa, és tartsa meg az összes meglévő címkét. Ezzel a beállítással megtarthatja a már címkével jelölt összes adatot, és elkezdheti használni az új címkét a még nem címkézett adatokhoz.
1. Módosítsa az utasítások lapját az új címké(k) számára.
1. Miután hozzáadta az összes új címkét, a projekt  újraindításához  állítsa a Szüneteltetve értéket az oldal jobb felső felében a Szüneteltetve gombra.  

## <a name="export-the-labels"></a>A címkék exportálása

A címkeadatokat bármikor exportálhatja Machine Learning kísérletezéshez. A képfeliratok [COCO](http://cocodataset.org/#format-data) formátumban vagy [címkékkel Azure Machine Learning adatkészletként exportálhatók.](how-to-use-labeled-dataset.md) Használja **az Exportálás** gombot a címkézési projekt **Projekt** részletei lapján.

A COCO-fájl a munkaterület alapértelmezett blobtárolójában jön létre Azure Machine Learning *export/coco mappájában.* Az exportált adatkészletet Azure Machine Learning adatkészletet az  Adatkészletek szakaszban Machine Learning. Az adatkészlet részleteit tartalmazó oldal a címkék Pythonból való eléréséhez szükséges mintakódot is tartalmaz.

![Exportált adatkészlet](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="troubleshooting"></a>Hibaelhárítás

Ha ezen problémák bármelyikét látja, használja ezeket a tippeket.

|Probléma  |Feloldás  |
|---------|---------|
|Csak a blob-adattárakban létrehozott adatkészletek használhatók.     |  Ez a jelenlegi kiadás ismert korlátozása.       |
|A létrehozás után a projekt hosszú ideig az "Inicializálás" megjelenik.     | Frissítse manuálisan az oldalt. Az inicializálásnak másodpercenként körülbelül 20 adatponttal kell folytatódni. Az automatikus javítás hiánya ismert probléma.         |
|A képek áttekintésekor az újonnan címkével jelölt képek nem jelennek meg.     |   Az összes címkével címkével jelölt kép betöltéséhez kattintson az **Első gombra.** Az **Első** gomb visszaveszi a lista elejére, de betölti az összes címkével jelölt adatot.      |
|Az Esc billentyű lenyomása az objektumészlelés címkézése közben egy nulla méretű címkét hoz létre a bal felső sarokban. Az ebben az állapotban lévő címkék elküldése sikertelen.     |   Törölje a címkét a mellette található keresztjelre kattintva.  |
|Nem lehet feladatkészletet hozzárendelni egy adott címkézőhez.     |   Ez a jelenlegi kiadás ismert korlátozása.  |

## <a name="next-steps"></a>Következő lépések

* [Oktatóanyag: Hozza létre első képosztályozási címkéző projektjét.](tutorial-labeling.md)
* Képek címkézése [képosztályozáshoz vagy objektumészleléshez](how-to-label-images.md)
* További információ a [Azure Machine Learning és Machine Learning Studióról (klasszikus)](./overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)
