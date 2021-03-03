---
title: Automatizált ML besorolási modellek létrehozása
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan taníthatja & a besorolási modellek üzembe helyezését Azure Machine Learning automatikus gépi tanulási (automatikus ML) felületével.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: cartacioS
ms.author: sacartac
ms.reviewer: nibaccam
ms.date: 12/21/2020
ms.custom: automl
ms.openlocfilehash: ad8a9f7af9ddabe969d090f80378ba5ff891d7f1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101691943"
---
# <a name="tutorial-create-a-classification-model-with-automated-ml-in-azure-machine-learning"></a>Oktatóanyag: besorolási modell létrehozása automatizált ML-vel Azure Machine Learning


Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy egyszerű besorolási modellt anélkül, hogy egyetlen kódrészletet írna be a Azure Machine Learning Studióban található automatikus gépi tanulás használatával. Ez a besorolási modell azt jelzi, hogy az ügyfél előfizet-e a pénzügyi intézménnyel kötött, rögzített időre szóló befizetésre.

Az automatizált gépi tanulás segítségével automatizálhatja az időigényes feladatokat. Az automatizált gépi tanulás gyorsan megismétli az algoritmusok és hiperparaméterek beállítása számos kombinációját, így könnyebben megtalálhatja a legjobb modellt a választott sikerességi mérőszám alapján.

Az idősorozat-előrejelzési példa [: oktatóanyag: kereslet-előrejelzési & AutoML](tutorial-automated-ml-forecast.md).

Ebből az oktatóanyagból megtudhatja, hogyan hajthatja végre a következő feladatokat:

> [!div class="checklist"]
> * Hozzon létre egy Azure Machine Learning munkaterületet.
> * Automatizált gépi tanulási kísérlet futtatása.
> * A kísérlet részleteinek megtekintése.
> * A modell üzembe helyezése.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://aka.ms/AMLFree).

* Töltse le a [**bankmarketing_train.csv**](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv) adatfájlt. Az **y** oszlop azt jelzi, hogy az ügyfél egy rögzített lejáratú befizetésre fizetett-e, amelyet később a jelen oktatóanyagban megjelenő előrejelzések céljának oszlopa azonosítottak. 

## <a name="create-a-workspace"></a>Munkaterület létrehozása

Az Azure Machine Learning munkaterület a felhőben található alapvető erőforrás, amely a gépi tanulási modellek kipróbálásához, betanításához és üzembe helyezéséhez használható. Az Azure-előfizetést és az erőforráscsoportot egy könnyen felhasználható objektumhoz fűzi a szolgáltatásban. 

[A munkaterület többféleképpen is létrehozható](how-to-manage-workspace.md). Ebben az oktatóanyagban egy munkaterületet hoz létre az Azure-erőforrások kezeléséhez használható webalapú konzolon Azure Portal használatával.

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Jegyezze fel a **munkaterületet** és az **előfizetést**. Ezekre azért van szükség, hogy a megfelelő helyen hozza létre a kísérletet. 

## <a name="get-started-in-azure-machine-learning-studio"></a>Ismerkedés a Azure Machine Learning Studióval

A következő kísérlettel végezheti el a beállítás és a Futtatás lépéseit a Azure Machine Learning Studióban https://ml.azure.com , egy konszolidált webes felületen, amely tartalmazza a gépi tanulási eszközöket, amelyekkel adatelemzési forgatókönyveket végezhet az összes képzettségi szint adatelemző szakemberek számára. A Studio nem támogatott az Internet Explorer böngészőben.

1. Jelentkezzen be [Azure Machine learning studióba](https://ml.azure.com).

1. Válassza ki az előfizetését és a létrehozott munkaterületet.

1. Válassza az első **lépések** lehetőséget.

1. A bal oldali panelen válassza az **automatikus ml** lehetőséget a **Szerző** szakaszban.

   Mivel ez az első automatizált ML-kísérlet, egy üres lista jelenik meg, amely a dokumentációra mutató hivatkozásokat tartalmaz.

   ![Első lépések lap](./media/tutorial-first-experiment-automated-ml/get-started.png)

1. Válassza az **+ új AUTOMATIZÁLT ml Futtatás** lehetőséget. 

## <a name="create-and-load-dataset"></a>Adatkészlet létrehozása és betöltése

A kísérlet konfigurálása előtt töltse fel az adatfájlt a munkaterületre egy Azure Machine Learning adatkészlet formájában. Így biztosíthatja, hogy az adatai megfelelően legyenek formázva a kísérlethez.

1. Hozzon létre egy új adatkészletet a **helyi fájlok** lehetőség kiválasztásával a  **+ adatkészlet létrehozása** legördülő menüből. 

    1. Az **alapszintű információ** űrlapon adja meg az adatkészlet nevét, és adjon meg egy opcionális leírást. Az automatikus ml felület jelenleg csak a TabularDatasets támogatja, ezért az adatkészlet típusának alapértelmezett értékének *táblázatos* kell lennie.

    1. Kattintson a **Next (tovább) gombra** a bal alsó sarokban

    1. Az **adattár és fájl kiválasztása** űrlapon válassza ki az alapértelmezett adattárat, amelyet a rendszer automatikusan beállított a munkaterület létrehozásakor, **workspaceblobstore (Azure Blob Storage)**. Itt töltheti fel az adatfájlt, hogy elérhető legyen a munkaterületen.

    1. Válassza a **Tallózás** lehetőséget.
    
    1. Válassza ki a **bankmarketing_train.csv** fájlt a helyi számítógépen. Ez az [előfeltételként](https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_train.csv)letöltött fájl.

    1. Adjon egyedi nevet az adatkészletnek, és adjon meg egy opcionális leírást. 

    1. A bal alsó sarokban kattintson a **tovább** gombra, hogy feltöltse azt az alapértelmezett tárolóba, amelyet a rendszer automatikusan beállított a munkaterület létrehozása során.  
    
       A feltöltés befejezésekor a beállítások és az előnézet űrlap előre fel van töltve a fájl típusa alapján. 
       
    1. Győződjön meg arról, hogy a **beállítások és az előnézet** űrlap a következőképpen van feltöltve, majd válassza a **tovább** lehetőséget.
        
        Mező|Leírás| Az oktatóanyag értéke
        ---|---|---
        Fájlformátum|Meghatározza a fájlban tárolt adatelrendezést és-típust.| Tagolt
        Elválasztó|Egy vagy több karakter, amely egy &nbsp; egyszerű szövegben vagy más adatfolyamban található különálló, egymástól független régiók között határozza meg a határt. |Vessző
        Encoding|Meghatározza, hogy az adatkészletek olvasásához milyen bitet kell használni a séma-tábla.| UTF-8
        Oszlopfejlécek| Azt jelzi, hogy a rendszer hogyan kezeli az adatkészlet fejléceit (ha van ilyen).| Minden fájlnak azonos fejléce van
        Sorok kihagyása | Azt jelzi, hogy az adatkészletben hány, ha van ilyen, a sorok kimaradnak.| Nincs

    1. A **séma** űrlap lehetővé teszi az adatai további konfigurálását a kísérlethez. Ebben a példában válassza ki a **day_of_week** váltási kapcsolóját, hogy ne szerepeljen. Kattintson a **Tovább** gombra.
         ![Séma űrlap](./media/tutorial-first-experiment-automated-ml/schema-tab-config.gif)
    1. A **részletek megerősítése** űrlapon ellenőrizze, hogy az információ megegyezik-e a korábban az **alapinformációk, az adattár és a fájl kiválasztása,** valamint a **beállítások és az előnézeti** űrlapok alapján.
    
    1. Válassza a **Létrehozás** lehetőséget az adatkészlet létrehozásának befejezéséhez.
    
    1. Válassza ki az adatkészletet, amint megjelenik a listában.
    
    1. Tekintse át az **adatelőnézett**  , és győződjön meg arról, hogy nem tartalmaz **day_of_week** , majd válassza a **Bezárás** lehetőséget.

    1. Válassza a  **tovább** lehetőséget.

## <a name="configure-run"></a>Futtatás konfigurálása

Az adatai betöltését és konfigurálását követően beállíthatja a kísérletet. Ez a beállítás olyan kísérleti tervezési feladatokat is magában foglal, mint például a számítási környezet méretének kiválasztásával és a megjósolni kívánt oszlop megadásával. 

1. Kattintson az **új választógomb létrehozása** gombra.

1. Töltse fel a **Run (Futtatás** ) űrlapot a következőképpen:
    1. Adja meg a kísérlet nevét: `my-1st-automl-experiment`

    1. Válassza az **y** elemet a cél oszlopként, amit meg szeretne jósolni. Ebben az oszlopban látható, hogy az ügyfél előfizetett-e egy lejárati időszakra.
    
    1. Válassza az **+ új számítás létrehozása** és a számítási cél konfigurálása lehetőséget. A számítási cél egy helyi vagy felhőalapú erőforrás-környezet, amely a betanítási parancsfájl futtatására vagy a szolgáltatás központi telepítésének üzemeltetésére szolgál. Ebben a kísérletben felhőalapú számítást használunk. 
        1. Töltse ki a **virtuális gép** űrlapját a számítás beállításához.

            Mező | Leírás | Az oktatóanyag értéke
            ----|---|---
            Virtuális &nbsp; gép &nbsp; prioritása |Válassza ki, hogy milyen prioritással rendelkezzen a kísérlet| Dedikált
            Virtuális &nbsp; gép &nbsp; típusa| Válassza ki a virtuális gép típusát a számítási feladatokhoz.|CPU (központi feldolgozó egység)
            Virtuális &nbsp; gép &nbsp; mérete| Válassza ki a virtuális gép méretét a számítási feladatokhoz. Az ajánlott méretek listája az adatai és a kísérlet típusa alapján érhető el. |Standard_DS12_V2
        
        1. Válassza a **tovább** lehetőséget a **beállítások konfigurálása űrlap** feltöltéséhez.
        
            Mező | Leírás | Az oktatóanyag értéke
            ----|---|---
            Számítási név |  A számítási környezet azonosítására szolgáló egyedi név. | automl – számítás
            Csomópontok minimális/maximális száma| A profilhoz legalább 1 csomópontot kell megadnia.|Minimális csomópontok: 1<br>Csomópontok maximális száma: 6
            Leskálázás előtt üresjárati másodperc | Üresjárati idő a fürt automatikus skálázása előtt a csomópontok minimális száma szerint.|120 (alapértelmezett)
            Speciális beállítások | Beállítások egy virtuális hálózat konfigurálásához és engedélyezéséhez a kísérlethez.| Nincs               

        1. Válassza a **Létrehozás** lehetőséget a számítási cél létrehozásához. 

            **Ez eltarthat néhány percet.** 

             ![Beállítások lap](./media/tutorial-first-experiment-automated-ml/compute-settings.png)

        1. A létrehozás után válassza ki az új számítási célt a legördülő listából.

    1. Kattintson a **Tovább** gombra.

1. A **feladat típusa és beállításai** űrlapon végezze el az automatikus ml-kísérlet beállítását a Machine learning-feladattípus és a konfigurációs beállítások megadásával.
    
    1.  Válassza a **besorolás** lehetőséget a Machine learning feladattípusként.

    1. Válassza a **további konfigurációs beállítások megtekintése** lehetőséget, és töltse fel a mezőket az alábbiak szerint. Ezek a beállítások hatékonyabban szabályozzák a betanítási feladatot. Ellenkező esetben a rendszer az alapértelmezett értékeket a kísérletezés és az adatértékek alapján alkalmazza.

        További &nbsp; konfigurációk|Leírás|&nbsp;Az &nbsp; oktatóanyag értéke
        ------|---------|---
        Elsődleges metrika| Az értékelési metrika, amelyet a Machine learning algoritmusa fog mérni.|AUC_weighted
        A legjobb modell ismertetése| A automatikusan mutatja az automatizált ML által létrehozott legjobb modell magyarázatát.| Engedélyezés
        Letiltott algoritmusok | A betanítási feladatokból kizárni kívánt algoritmusok| Nincs
        Kilépési feltétel| Ha teljesülnek a feltételek, a betanítási feladatok leállnak. |Betanítási &nbsp; feladatok &nbsp; ideje (óra): 1 <br> Metrika &nbsp; pontszámának &nbsp; küszöbértéke: nincs
        Érvényesítés | Válasszon egy több ellenőrzési típust és a tesztek számát.|Érvényesítés típusa:<br>&nbsp;k-szeres &nbsp; keresztek ellenőrzése <br> <br> Érvényességek száma: 2
        Egyidejűség| A másodpercenként végrehajtott párhuzamos ismétlések maximális száma| &nbsp;Egyidejű &nbsp; Ismétlések maximális száma: 5
        
        Kattintson a **Mentés** gombra.
    
1. A kísérlet futtatásához kattintson a **Befejezés** gombra. Megnyílik a **futtatási részletek**  képernyő, amelyen a kísérlet előkészítésének megkezdése után a **Futtatás állapota** látható. Ez az állapot frissíti a kísérlet előrehaladását. Az értesítések a Studio jobb felső sarkában is megjelennek, hogy tájékoztassák a kísérlet állapotáról.

>[!IMPORTANT]
> Az előkészítés **10-15 percet** vesz igénybe a kísérlet futtatásának előkészítése érdekében.
> A futása után **az egyes iterációk esetében 2-3 percet** vesz igénybe.  <br> <br>
> Éles környezetben valószínűleg egy kicsit. Ebben az oktatóanyagban azonban azt javasoljuk, hogy kezdje el a tesztelt algoritmusok vizsgálatát a **models (modellek** ) lapon, amíg a többiek még futnak. 

##  <a name="explore-models"></a>Modellek megismerése

Navigáljon a **modellek** lapra, és tekintse meg a tesztelt algoritmusokat (modelleket). Alapértelmezés szerint a modelleket metrikai pontszám szerint rendezi a rendszer. Ebben az oktatóanyagban a legfelső szinten a kiválasztott **AUC_weighted** metrika alapján elért modell a lista tetején található.

Amíg megvárja az összes kísérleti modell befejeződését, válassza ki a befejezett modell **algoritmusának nevét** a teljesítmény részleteinek megismeréséhez. 

Az alábbi lépésekben a **részletek** és a **metrikák** lapokon navigálhat a kiválasztott modell tulajdonságainak, metrikáinak és teljesítmény-diagramjainak megtekintéséhez. 

![Iteráció részleteinek futtatása](./media/tutorial-first-experiment-automated-ml/run-detail.gif)

## <a name="model-explanations"></a>Modell magyarázatai

Amíg a modellek befejeződik, megtekintheti a modell magyarázatait is, és megtekintheti, hogy mely adatszolgáltatások (nyers vagy mérnöki) befolyásolhatják az adott modell előrejelzéseit. 

Ezek a modell-magyarázatok igény szerint hozhatók létre, és a **magyarázatok (előzetes verzió)** lap részét képező modell magyarázatai irányítópulton vannak összefoglalva.

A modell magyarázatának létrehozásához 
 
1. Válassza az **1. Futtatás** elemet a **modellek** képernyőre való visszatéréshez. 
1. Válassza a **modellek** fület.
1. Ebben az oktatóanyagban válassza ki az első **MaxAbsScaler, a LightGBM** modellt.
1. Válassza a felső **modell magyarázata** gombot. A jobb oldalon megjelenik a **magyarázat modell** panel. 
1. Válassza ki a korábban létrehozott **automl-számítást** . Ez a számítási fürt egy alárendelt futtatást kezdeményez a modell magyarázatának létrehozásához.
1. Válassza a **Létrehozás** alul lehetőséget. Egy zöld sikert jelző üzenet jelenik meg a képernyő felső részén. 
    >[!NOTE]
    > A magyarázó művelet végrehajtása körülbelül 2-5 percet vesz igénybe.
1. Kattintson a **magyarázatok (előnézet)** gombra. Ez a lap a magyarázat futtatásának befejeződése után töltődik fel.
1. A bal oldali ablaktáblán bontsa ki a panelt, és válassza ki azt a sort, amely a **szolgáltatások** területen a **RAW** elemet tartalmazza. 
1. Kattintson a jobb oldalon az **összesítő funkció fontossága** fülre. Ez a diagram mutatja, hogy mely adatszolgáltatások befolyásolják a kiválasztott modell előrejelzéseit. 

    Ebben a példában az *időtartam* úgy tűnik, hogy a leginkább befolyásolja a modell előrejelzéseit.
    
    ![Modell magyarázatának irányítópultja](media/tutorial-first-experiment-automated-ml/model-explanation-dashboard.png)

## <a name="deploy-the-best-model"></a>A legjobb modell üzembe helyezése

Az automatizált gépi tanulási felület lehetővé teszi a legjobb modell üzembe helyezését webszolgáltatásként néhány lépésben. Az üzembe helyezés a modell integrációja, így előre jelezhető az új adatmennyiség, és azonosíthatók a lehetséges lehetőségek is. 

Ebben a kísérletben a webszolgáltatások üzembe helyezése azt jelenti, hogy a pénzügyi intézmény immár egy iterációs és méretezhető webes megoldást kínál a lehetséges rögzített lejáratú ügyfelek azonosítására. 

Ellenőrizze, hogy befejeződött-e a kísérlet futtatása. Ehhez a képernyő tetején található **1. Futtatás** gombra kattintva térjen vissza a fölérendelt Futtatás lapra. A képernyő bal felső részén megjelenik egy **befejezett** állapot. 

A kísérlet futtatása után a **részletek** lap a **legjobb modell összefoglaló** szakaszával lesz feltöltve. Ebben a kísérleti kontextusban a **VotingEnsemble** a **AUC_weighted** metrika alapján a legjobb modellnek számít.  

Ezt a modellt üzembe helyezjük, de javasoljuk, hogy az üzembe helyezés körülbelül 20 percet vesz igénybe. Az üzembe helyezési folyamat több lépést is magában foglal, beleértve a modell regisztrálását, az erőforrások létrehozását és a webszolgáltatás konfigurálását.

1. Válassza a **VotingEnsemble** lehetőséget a modell-specifikus oldal megnyitásához.

1. Válassza a **telepítés** gombot a bal felső sarokban.

1. Töltse fel a **modell üzembe helyezése** panelt az alábbiak szerint:

    Mező| Érték
    ----|----
    Központi telepítés neve| My-automl – üzembe helyezés
    Központi telepítés leírása| Az első automatizált gépi tanulási kísérlet üzembe helyezése
    Számítási típus | Azure számítási példány kiválasztása (ACI)
    Hitelesítés engedélyezése| Letiltás lehetőséget. 
    Egyéni központi telepítések használata| Letiltás lehetőséget. Lehetővé teszi az alapértelmezett illesztőprogram-fájl (pontozási parancsfájl) és a környezeti fájl automatikus generálását. 
    
    Ebben a példában a *speciális* menüben megadott alapértékeket használjuk. 

1. Válassza az **Üzembe helyezés** lehetőséget.  

    A **futtatási** képernyő felső részén megjelenik egy zöld sikert jelző üzenet, és a **modell összegzése** ablaktáblán megjelenik egy állapotjelző üzenet a **telepítés állapota** területen. A központi telepítés állapotának megtekintéséhez válassza a rendszeres **frissítés** lehetőséget.
    
Most már rendelkezik egy operatív webszolgáltatással előrejelzések létrehozásához. 

Folytassa a [**következő lépésekkel**](#next-steps) , ha többet szeretne megtudni az új webszolgáltatás használatáról, és tesztelje a jóslatokat a Power BI beépített Azure Machine learning-támogatásával.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az üzembe helyezési fájlok nagyobb méretűek, mint az adatmennyiség és a kísérlet során használt fájlok. Csak a központi telepítési fájlokat törölje, hogy csökkentse a költségeket a fiókjához, vagy ha meg szeretné őrizni a munkaterületet és a kísérletet. Ellenkező esetben törölje a teljes erőforráscsoportot, ha nem tervezi az egyik fájl használatát.  

### <a name="delete-the-deployment-instance"></a>A központi telepítési példány törlése

Csak a központi telepítési példányt törölje a Azure Machine Learning a https: \/ /ml.Azure.com/, ha meg szeretné tartani az erőforráscsoportot és a munkaterületet más oktatóanyagok és feltárás céljából. 

1. Lépjen [Azure Machine learning](https://ml.azure.com/). Navigáljon a munkaterülethez, és a bal oldalon az **eszközök** ablaktáblán válassza a **végpontok** lehetőséget. 

1. Válassza ki a törölni kívánt központi telepítést, és válassza a **Törlés** lehetőséget. 

1. Válassza a **Folytatás** lehetőséget.

### <a name="delete-the-resource-group"></a>Az erőforráscsoport törlése

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az automatizált gépi tanulási oktatóanyagban a besorolási modell létrehozásához és üzembe helyezéséhez Azure Machine Learning automatikus ML-interfészét használta. További információkat és további lépéseket a következő cikkekben talál:

> [!div class="nextstepaction"]
> [Webszolgáltatás felhasználása](/power-bi/connect-data/service-aml-integrate?context=azure%2fmachine-learning%2fcontext%2fml-context)

+ További információ az [automatizált gépi tanulásról](concept-automated-ml.md).
+ A besorolási metrikákkal és diagramokkal kapcsolatos további információkért tekintse meg az [automatizált gépi tanulás eredményeinek megismerése](how-to-understand-automated-ml.md) című cikket.
+ További információ a [featurization](how-to-configure-auto-features.md#featurization).
+ További információ az [adatprofilkészítésről](how-to-connect-data-ui.md#profile).


>[!NOTE]
> Ezt a bank marketing-adatkészletet a [Creative Commons (CCO: Public Domain) licence](https://creativecommons.org/publicdomain/zero/1.0/)keretében lehet elérni. Az adatbázis egyéni tartalmában minden jogosultság az [adatbázis tartalma licenc](https://creativecommons.org/publicdomain/zero/1.0/) alatt érhető el, és elérhető a [Kaggle](https://www.kaggle.com/janiobachmann/bank-marketing-dataset)-on. Ez az adatkészlet eredetileg az [UCI Machine learning adatbázisban](https://archive.ics.uci.edu/ml/datasets/bank+marketing)volt elérhető.<br><br>
> [Moro et al., 2014] S. Moro, P. Cortez és P. Rita. A Data-Driven Approach to Predict the Success of Bank Telemarketing. (Adatvezérelt megközelítés a banki telemarketing sikerességének előrejelzéséhez.) Decision Support Systems, Elsevier, 62:22-31, 2014. június
