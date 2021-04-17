---
title: Modellek létrehozása az AutoML használatával & üzembe helyezéshez
titleSuffix: Azure Machine Learning
description: Automatizált gépi tanulási modellek létrehozása, áttekintése és üzembe helyezése a Azure Machine Learning stúdió.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: nibaccam
author: cartacioS
ms.reviewer: nibaccam
ms.date: 12/20/2020
ms.topic: conceptual
ms.custom: how-to, automl
ms.openlocfilehash: 5718e0e3732f57b46500f9d2cdb1165e883ca44f
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575581"
---
# <a name="create-review-and-deploy-automated-machine-learning-models-with-azure-machine-learning"></a>Automatizált gépi tanulási modellek létrehozása, áttekintése és üzembe helyezése Azure Machine Learning


Ebből a cikkből megtudhatja, hogyan hozhat létre, fedezhet fel és helyezhet üzembe automatizált gépi tanulási modelleket egyetlen sor kód nélkül a Azure Machine Learning stúdió.

Az automatizált gépi tanulás egy olyan folyamat, amelyben a rendszer kiválasztja az adott adatokhoz leginkább megfelelő gépi tanulási algoritmust. Ezzel a folyamattal gyorsan hozhat létre gépi tanulási modelleket. [További információ az automatizált gépi tanulásról.](concept-automated-ml.md)
 
Teljes példaként próbálja ki a besorolási modell létrehozását ismertető oktatóanyagot a Azure Machine Learning gépi [tanulási felületével.](tutorial-first-experiment-automated-ml.md) 

A Python kódalapú használatához konfigurálja az automatizált [gépi tanulási](how-to-configure-auto-train.md) kísérleteket az Azure Machine Learning SDK-val.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a Azure Machine Learning [ingyenes vagy fizetős](https://aka.ms/AMLFree) verzióját.

* Egy Azure Machine Learning-munkaterület. Lásd: [Create an Azure Machine Learning workspace (Munkaterület Azure Machine Learning létrehozása).](how-to-manage-workspace.md) 

## <a name="get-started"></a>Bevezetés

1. Jelentkezzen be a [Azure Machine Learning stúdió.](https://ml.azure.com) 

1. Válassza ki az előfizetést és a munkaterületet. 

1. Lépjen a bal oldali panelre. A **Szerző szakaszban válassza** az Automatizált **gépi tanulási lehetőséget.**

[![Azure Machine Learning stúdió navigációs panel](media/how-to-use-automated-ml-for-ml-models/nav-pane.png)](media/how-to-use-automated-ml-for-ml-models/nav-pane-expanded.png)

 Ha most először végez kísérleteket, egy üres lista és a dokumentációra mutató hivatkozások megjelenik. 

Ellenkező esetben megjelenik a legutóbbi automatizált gépi tanulási kísérletek listája, beleértve az SDK-val létrehozott kísérleteket is. 

## <a name="create-and-run-experiment"></a>Kísérlet létrehozása és futtatása

1. Válassza **az + Új automatizált gépi tanulási futtatás lehetőséget,** és töltse ki az űrlapot.

1. Válasszon ki egy adatkészletet a storage-tárolóból, vagy hozzon létre egy újat. Adatkészletek helyi fájlokból, webes URL-címekről, adattárakból vagy nyitott Azure-adatkészletekből is létre lehet hozva. További információ az [adatkészlet létrehozásáról.](how-to-create-register-datasets.md)  

    >[!Important]
    > Betanítási adatokra vonatkozó követelmények:
    >* Az adatoknak táblázatos formában kell lennie.
    >* Az előrejelezni kívánt értéknek (céloszlop) jelen kell lennie az adatokban.

    1. Ha egy új adatkészletet a helyi számítógépen található fájlból létre kell hoznia, válassza a **+Adatkészlet** létrehozása, majd a **Helyi fájlból lehetőséget.** 

    1. Az **Alapszintű információ űrlapon** adjon meg egy egyedi nevet az adatkészletnek, és adjon meg egy leírást (nem kötelező). 

    1. Kattintson **a Tovább** gombra az **Adattár és fájlkiválasztási űrlap megnyitásához.** Ezen az űrlapon kiválaszthatja, hová töltse fel az adatkészletet; az alapértelmezett Storage-tároló, amely automatikusan létrejön a munkaterülettel, vagy válassza ki a kísérlethez használni kívánt tárolót. 
    
        1. Ha az adatok egy virtuális hálózat mögött  vannak, engedélyeznie kell az érvényesítési függvény kihagyása funkciót annak biztosításához, hogy a munkaterület hozzáfér az adatokhoz. További információkért lásd a [Azure Machine Learning stúdió Azure-beli virtuális hálózatokban való használatát.](how-to-enable-studio-virtual-network.md) 
    
    1. Válassza **a Tallózás** lehetőséget az adatfájl feltöltéséhez. 

    1. A **pontosságért tekintse** át a Beállítások és az előnézeti űrlapot. Az űrlap a fájltípus alapján intelligensen van feltöltve. 

        Mező| Leírás
        ----|----
        Fájlformátum| A fájlban tárolt adatok elrendezését és típusát határozza meg.
        Elválasztó| Egy vagy több karakter a különálló, független régiók közötti határ egyszerű szövegként vagy más adatfolyamban való megadásához.
        Encoding| Meghatározza, hogy melyik bit-karakter sématáblát kell használni az adatkészlet beolvassa.
        Oszlopfejlécek| Azt jelzi, hogy a rendszer hogyan kezeli az adatkészlet fejlécét (ha van).
        Sorok kihagyása | Azt jelzi, hogy a rendszer hány sort kihagy az adathalmazból, ha van ilyen.
    
        Kattintson a **Tovább** gombra.

    1. A **Séma** űrlap intelligensen ki van töltve a Beállítások és az Előnézet **űrlapon kiválasztott beállítások** alapján. Itt konfigurálhatja az egyes oszlopok adattípusát, áttekintheti az oszlopneveket, és kiválaszthatja, hogy mely oszlopokat **ne** foglalja bele a kísérletbe. 
            
        Válassza a **Tovább** elemet.

    1. A **Confirm details (Részletek megerősítése)** űrlap az  Alapszintű adatok, a Beállítások és az előnézeti űrlapokon korábban kitöltött **információk összegzése.** Lehetősége van adatprofilt is létrehozni az adatkészlethez egy profilkészítést engedélyező számítás használatával. További információ az [adatprofil-készítésről.](how-to-connect-data-ui.md#profile)

        Kattintson a **Tovább** gombra.
1. Miután megjelenik, válassza ki az újonnan létrehozott adatkészletet. Megtekintheti az adatkészlet és a mintastatisztikák előnézetét is. 

1. A **Futtatás konfigurálása űrlapon** válassza az **Új** létrehozása lehetőséget, és írja be **a Tutorial-automl-deploy** nevet a kísérlet neveként.

1. Válasszon ki egy céloszlopot; Ez az az oszlop, amelyről előrejelzéseket szeretne tenni.

1. Válasszon ki egy számítást az adatprofil-készítéshez és a betanítási feladathoz. A meglévő számítási erőforrások listája a legördülő listában érhető el. Új számítás létrehozásához kövesse a 7. lépésben található utasításokat.

1. Válassza **az Új számítás létrehozása lehetőséget** a számítási környezet konfiguráláshoz ehhez a kísérlethez.

    Mező|Leírás
    ---|---
    Számítási név| Adjon meg egy egyedi nevet, amely azonosítja a számítási környezetet.
    Virtuálisgép-prioritás| Az alacsony prioritású virtuális gépek olcsóbbak, de nem garantálják a számítási csomópontokat. 
    Virtuális gép típusa| A virtuális gép típusának válassza a CPU vagy a GPU lehetőséget.
    Virtuális gép mérete| Válassza ki a virtuális gép méretét a számításhoz.
    Minimális/maximális csomópontok| Az adatok profiljainak megadásához 1 vagy több csomópontot kell megadnia. Adja meg a számítási csomópontok maximális számát. Az alapértelmezett érték 6 csomópont egy AML-számításhoz.
    Speciális beállítások | Ezekkel a beállításokkal konfigurálhatja a kísérlethez a felhasználói fiókot és a meglévő virtuális hálózatot. 
    
    Válassza a **Létrehozás** lehetőséget. Egy új számítás létrehozása eltarthat néhány percig.

    >[!NOTE]
    > A számítási név jelzi, hogy a kiválasztott/létrehozott számítás *profilkészítése engedélyezve van-e.* (További részletekért [lásd az adatprofil-készítés](how-to-connect-data-ui.md#profile) szakaszt).

    Kattintson a **Tovább** gombra.

1. A Feladat **típusa és beállítások űrlapon** válassza ki a feladat típusát: besorolás, regresszió vagy előrejelzés. További [információért tekintse meg a támogatott](concept-automated-ml.md#when-to-use-automl-classify-regression--forecast) feladattípusokat.

    1. A **besoroláshoz** mély tanulást is engedélyezhet.
    
        Ha a mély tanulás engedélyezve van, az érvényesítés a _felosztási train_validation korlátozódik._ [További információ az érvényesítési lehetőségekről.](how-to-configure-cross-validation-data-splits.md)


    1. Az **előrejelzéshez** a következőt használhatja: 
    
        1. Mély tanulás engedélyezése.
    
        1. Időoszlop *kiválasztása:* Ez az oszlop a használni kívánt időadatokat tartalmazza.

        1. Előrejelzési *horizont* kiválasztása: Adja meg, hogy a modell hány időegységet (perc/óra/nap/hét/hónap/év) képes előre jelezni a modellnek. Minél tovább van szükség a modell előrejelzéséhez a jövőbe, annál kevésbé lesz pontos. [További információ az előrejelzési és előrejelzési horizontról.](how-to-auto-train-forecast.md)

1. (Nem kötelező) További konfigurációs beállítások megtekintése: további beállítások a betanítási feladat jobb szabályozása érdekében. Ellenkező esetben az alapértelmezések a kísérletek kiválasztása és az adatok alapján vannak alkalmazva. 

    További konfigurációk|Description
    ------|------
    Elsődleges metrika| A modell pontozásának fő mérőszáma. [További információ a modellmetrikákról.](how-to-configure-auto-train.md#primary-metric)
    A legjobb modell magyarázata | Az ajánlott modell magyarázatának megjelenítése érdekében válassza az engedélyezést vagy letiltását. <br> Ez a funkció jelenleg nem érhető el bizonyos [előrejelzési algoritmusok esetében.](how-to-machine-learning-interpretability-automl.md#interpretability-during-training-for-the-best-model) 
    Letiltott algoritmus| Válassza ki a betanítás feladatból kizárni kívánt algoritmusokat. <br><br> Az algoritmusok csak SDK-kísérletekhez [érhetők el.](how-to-configure-auto-train.md#supported-models) <br> Az egyes [feladattípusokkal támogatott modelleket itt láthatja:](/python/api/azureml-automl-core/azureml.automl.core.shared.constants.supportedmodels).
    Kilépési feltétel| Ha ezen feltételek bármelyike teljesül, a betanítás le lesz állítva. <br> *Betanítás feladatának ideje (óra):* Mennyi ideig futhat a betanítás. <br> *Metrika pontszámának küszöbértéke:* Minimális metrikapontszám az összes folyamathoz. Ez biztosítja, hogy ha egy meghatározott célmetrikát szeretne elérni, akkor nem kell a szükségesnél több időt töltenie a betanítás feladatra.
    Érvényesítés| Válassza ki a betanítás során használni kívánt keresztellenőrzési lehetőségek valamelyikét. <br> [További információ a keresztellenőrzésről.](how-to-configure-cross-validation-data-splits.md#prerequisites)<br> <br>Az előrejelzés csak a k-szoros keresztellenőrzést támogatja.
    Egyidejűség| *Egyidejű iterációk maximális száma:* A betanítási feladatban tesztelni szükséges folyamatok (iterációk) maximális száma. A feladat nem fog a megadott számú iterációnál többet futtatni. További információ arról, hogy az automatizált ML hogyan hajt végre [több gyermekfuttatásokat a fürtökön.](how-to-configure-auto-train.md#multiple-child-runs-on-clusters)

1. (Nem kötelező) A featurizálási beállítások megtekintése:  ha úgy dönt, hogy engedélyezi az Automatikus funkciószűrés beállítást a További konfigurációs beállítások **űrlapon,** a rendszer alkalmazza az alapértelmezett funkcióbeállítási technikákat. A **View featurization settings (Featurizálási beállítások** megtekintése) lapon módosíthatja ezeket az alapértelmezett értékeket, és ennek megfelelően testre szabhatja őket. Megtudhatja, hogyan [szabhatja testre a featurizálásokat.](#customize-featurization) 

    ![Képernyőkép a Feladattípus kiválasztása párbeszédpanelről, a Tulajdonságok beállításainak megtekintése ki van hívva.](media/how-to-use-automated-ml-for-ml-models/view-featurization-settings.png)

## <a name="customize-featurization"></a>A featurizálás testreszabása

A **Featurization űrlapon** engedélyezheti vagy letilthatja az automatikus featurizálást, és testre szabhatja a kísérlet automatikus featurizálási beállításait. Az űrlap megnyitásához tekintse meg a Kísérlet létrehozása és futtatása szakasz 10. [lépését.](#create-and-run-experiment) 

Az alábbi táblázat összefoglalja a Studióban jelenleg elérhető testreszabásokat. 

Oszlop| Testreszabás
---|---
Tartalmazza | Meghatározza, hogy mely oszlopok szerepeljenek a betanítás során.
Szolgáltatástípus| Módosítsa a kiválasztott oszlop értéktípusát.
Impute with| Válassza ki, hogy milyen értékkel számítja ki a hiányzó értékeket az adatokban.

![Azure Machine Learning stúdió egyéni tulajdonságok létrehozása](media/how-to-use-automated-ml-for-ml-models/custom-featurization.png)

## <a name="run-experiment-and-view-results"></a>Kísérlet futtatása és az eredmények megtekintése

A **kísérlet futtatásához** válassza a Befejezés lehetőséget. A kísérlet előkészítése akár 10 percet is igénybe vehet. A betanítási feladatok és az egyes folyamatok futtatásának befejezése további 2-3 percet is igénybe vehet.

> [!NOTE]
> Az automatizált gépi tanulási algoritmusok eredendő véletlenszerűséggel vannak alkalmazva, amely kis mértékben variációt okozhat egy ajánlott modell végső metrikapontszámában, például a pontosságban. Az automatizált ml olyan adatokon is végez műveleteket, mint a betanítás-tesztelés felosztása, a betanítás-ellenőrzés felosztása vagy a keresztellenőrzés, ha szükséges. Ha tehát többször is futtat egy kísérletet ugyanazokkal a konfigurációs beállításokkal és elsődleges metrikával, valószínűleg az egyes kísérletek végső metrikapontszámát fogja látni ezen tényezők miatt. 

### <a name="view-experiment-details"></a>A kísérlet részleteinek megtekintése

A **Futtatás részletei** képernyő a Részletek **lapon nyílik** meg. Ezen a képernyőn a kísérlet futtatásának összegzése látható, a futtatás száma mellett felül egy állapotsáv is látható. 

A **Modellek** lapon a létrehozott modellek listája található metrikaérték szerint rendezve. Alapértelmezés szerint az a modell, amely a kiválasztott metrika alapján a legmagasabb értékű, a lista tetején jelenik meg. A betanítási feladat több modellt is kipróbál, amelyek sorra felkerülnek a listára. Ennek segítségével gyorsan összehasonlíthatja az addig létrehozott modellek metrikáját.

![Futtatás részletei](./media/how-to-use-automated-ml-for-ml-models/explore-models.gif)

### <a name="view-training-run-details"></a>Betanítás futtatás részleteinek megtekintése

Ha le szeretne fúrni bármelyik befejezett modellre, tekintse meg  a betanítási futtatás részleteit, például egy modellösszegzést a Modell lapon vagy a Metrikák lapon található teljesítménymetrika-diagramokat. További információ a [diagramokról.](how-to-understand-automated-ml.md) 

[![Iteráció részletei](media/how-to-use-automated-ml-for-ml-models/iteration-details.png)](media/how-to-use-automated-ml-for-ml-models/iteration-details-expanded.png)

## <a name="model-explanations-preview"></a>Modell magyarázatai (előzetes verzió)

A modell jobb érthetőbb magyarázata érdekében a modell magyarázatait jelző irányítópulton láthatja, hogy mely adat jellemzők (nyers vagy megtervezett) befolyásolták a modell előrejelzéseit. 

A modell magyarázatai irányítópult átfogó elemzést nyújt a betanított modellről, valamint annak előrejelzéseit és magyarázatait. Emellett lehetővé teszi az egyes adatpontokat és az egyes funkciók fontosságának részletezést. [További információ az irányítópultok magyarázatát megjelenítő vizualizációkról.](how-to-machine-learning-interpretability-aml.md#visualizations)

Ha magyarázatokat keres egy adott modellhez, 

1. A **Modellek lapon** válassza ki a megérteni kívánt modellt. 
1. Válassza a **Modell magyarázata** gombot, és adjon meg egy számítást, amely felhasználható a magyarázatok létrehozásához.
1. Ellenőrizze **a Gyermekfutatok** lapon az állapotot. 
1. Ha elkészült, lépjen a **Magyarázatok (előzetes verzió)** lapra, amely a magyarázatok irányítópultot tartalmazza. 

    ![Modell magyarázata irányítópult](media/how-to-use-automated-ml-for-ml-models/model-explanation-dashboard.png)

## <a name="deploy-your-model"></a>A modell üzembe helyezése

Ha már kéznél van a legjobb modell, új adatok előrejelzéséhez helyezze üzembe webszolgáltatásként.

>[!TIP]
> Ha olyan modellt keres, amely a Python SDK csomaggal lett létrehozva, regisztrálnia kell a modellt a `automl` munkaterületen. [](how-to-deploy-and-where.md?tabs=python#register-a-model-from-an-azure-ml-training-run-1) 
>
> Miután regisztrálta a modellt, keresse meg a studióban a bal oldali panel **Modellek** lehetőségének kiválasztásával. A modell megnyitása után válassza  a képernyő tetején található Üzembe helyezés gombot, majd kövesse a Modell üzembe helyezése szakasz **2.** lépésében leírt **utasításokat.**

Az automatizált ML segít programozás nélkül üzembe helyezni a modellt:

1. Az üzembe helyezéshez több lehetőség is rendelkezésre áll. 

    + 1. lehetőség: A legjobb modell üzembe helyezése a megadott metrikakövetelményeknek megfelelően. 
        1. A kísérlet befejezése után a képernyő tetején található **Futtatás 1** lehetőség kiválasztásával lépjen a szülő futtatás lapjára. 
        1.  Válassza ki a Legjobb modell összegzése **szakaszban felsorolt** modellt. 
        1. Válassza **az üzembe** helyezés lehetőséget az ablak bal felső részen. 

    + 2. lehetőség: Adott modell-iteráció üzembe helyezése ebből a kísérletből.
        1. Válassza ki a kívánt modellt a **Modellek** lapról
        1. Válassza **az üzembe** helyezés lehetőséget az ablak bal felső részen.

1. Töltse ki a **Modell üzembe helyezése panelt.**

    Mező| Érték
    ----|----
    Név| Adjon egyedi nevet az üzemelő példánynak.
    Description| Adjon meg egy leírást, hogy jobban azonosítsa, mire való ez az üzembe helyezés.
    Számítási típus| Válassza ki az üzembe helyezni kívánt végpont típusát: *Azure Kubernetes Service (AKS)* vagy *az Azure Container Instance (ACI)*.
    Számítási név| *Csak az AKS-re vonatkozik:* Válassza ki annak az AKS-fürtnek a nevét, amelybe üzembe kívánja helyezni.
    Hitelesítés engedélyezése | Válassza a jogkivonat- vagy kulcsalapú hitelesítés engedélyezése lehetőséget.
    Egyéni üzembe helyezési eszközök használata| Ha saját pontozó szkriptet és környezeti fájlt szeretne feltölteni, engedélyezze ezt a funkciót. [További információk a pontozószkriptekről](how-to-deploy-and-where.md).

    >[!Important]
    > A fájlneveknek 32 karakternél nem hosszabbnak kell lennie, és alfanumerikus karakterekkel kell kezdődnie és végződnie. A neveken belül használhat gondolatjeleket, aláhúzásjeleket, pontokat és alfanumerikus karaktereket. A szóköz nem engedélyezett.

    A *Speciális* menü olyan alapértelmezett üzembehelyezési funkciókat kínál, mint például az [adatgyűjtési](how-to-enable-app-insights.md) és az erőforrás-használati beállítások. Ha felül kívánja írni ezeket az alapértelmezett beállításokat, akkor ebben a menüben megteheti.

1. Válassza az **Üzembe helyezés** lehetőséget. Az üzembe helyezés nagyjából 20 percet vesz igénybe.
    Az üzembe helyezés megkezdése után megjelenik a **Modell összegzése** lap. Az üzembe helyezés folyamatát az **Üzembe helyezés állapota** szakaszban követheti nyomon. 

Ezzel használatba állított egy előrejelzéseket készítő webszolgáltatást! Az előrejelzések teszteléséhez kérdezze le a szolgáltatást a [Power BI beépített Azure Machine Learning-támogatásából](/power-bi/connect-data/service-aml-integrate?context=azure%2fmachine-learning%2fcontext%2fml-context).

## <a name="next-steps"></a>Következő lépések

* [Ismerje meg, hogyan lehet webszolgáltatást igénybeni.](how-to-consume-web-service.md)
* [Az automatizált gépi tanulási eredmények megismerése.](how-to-understand-automated-ml.md)
* [További információ az automatizált gépi tanulásról és Azure Machine Learning.](concept-automated-ml.md)