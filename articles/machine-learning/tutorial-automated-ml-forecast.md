---
title: 'Oktatóanyag: kereslet-előrejelzési & AutoML'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhatja be és helyezheti üzembe a kereslet-előrejelzési modellt a Azure Machine Learning Studio automatizált gépi tanulás szolgáltatásával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: sacartac
ms.reviewer: nibaccam
author: cartacioS
ms.date: 12/21/2020
ms.custom: automl
ms.openlocfilehash: 2653161b5828d89858234a9ca98fe432e0eacb5c
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879360"
---
# <a name="tutorial-forecast-demand-with-automated-machine-learning"></a>Oktatóanyag: az automatikus gépi tanulás iránti igény előrejelzése


Ebben az oktatóanyagban az automatikus gépi tanulást vagy az automatikus ML-t használja a Azure Machine Learning Studióban egy idősorozat-előrejelzési modell létrehozásához a bike Sharing szolgáltatáshoz való bérleti igény előrejelzéséhez.

Példa besorolási modellre [: oktatóanyag: besorolási modell létrehozása AUTOMATIZÁLT ml-vel Azure Machine Learningban](tutorial-first-experiment-automated-ml.md).

Ebből az oktatóanyagból megtudhatja, hogyan hajthatja végre a következő feladatokat:

> [!div class="checklist"]
> * Adatkészlet létrehozása és betöltése.
> * Automatikus ML-kísérlet konfigurálása és futtatása.
> * Előrejelzési beállítások megadása.
> * A kísérlet eredményeinek megismerése.
> * A legjobb modell üzembe helyezése.

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure Machine Learning-munkaterület. Lásd: [Azure Machine learning munkaterület létrehozása](how-to-manage-workspace.md). 

* Az [bike-no.csv](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/bike-no.csv) adatfájl letöltése

## <a name="get-started-in-azure-machine-learning-studio"></a>Ismerkedés a Azure Machine Learning Studióval

Ebben az oktatóanyagban létrehozhatja az automatizált ML-kísérletet Azure Machine Learning Studióban, egy összevont webes felületen, amely tartalmazza a gépi tanulási eszközöket, amelyekkel adatelemzési forgatókönyvek készíthetők az összes képzettségi szint adatelemző szakemberek számára. A Studio nem támogatott az Internet Explorer böngészőben.

1. Jelentkezzen be [Azure Machine learning studióba](https://ml.azure.com).

1. Válassza ki az előfizetését és a létrehozott munkaterületet.

1. Válassza az első **lépések** lehetőséget.

1. A bal oldali panelen válassza az **automatikus ml** lehetőséget a **Szerző** szakaszban.

1. Válassza az **+ új AUTOMATIZÁLT ml Futtatás** lehetőséget. 

## <a name="create-and-load-dataset"></a>Adatkészlet létrehozása és betöltése

A kísérlet konfigurálása előtt töltse fel az adatfájlt a munkaterületre egy Azure Machine Learning adatkészlet formájában. Így biztosíthatja, hogy az adatai megfelelően legyenek formázva a kísérlethez.

1. Az **adatkészlet kiválasztása** űrlapon válassza a **helyi fájlok** lehetőséget a  **+ adatkészlet létrehozása** legördülő menüből. 

    1. Az **alapszintű információ** űrlapon adja meg az adatkészlet nevét, és adjon meg egy opcionális leírást. Az adatkészlet típusának alapértelmezettnek kell lennie a **táblázatos** értéknél, mivel a Azure Machine learning Studio automatikus ml-je jelenleg csak a táblázatos adatkészleteket támogatja.
    
    1. Kattintson a **Next (tovább) gombra** a bal alsó sarokban

    1. Az **adattár és fájl kiválasztása** űrlapon válassza ki az alapértelmezett adattárat, amelyet a rendszer automatikusan beállított a munkaterület létrehozásakor, **workspaceblobstore (Azure Blob Storage)**. Ez az a tárolási hely, ahol fel kell töltenie az adatfájlt. 

    1. Válassza a **Tallózás** lehetőséget. 
    
    1. Válassza ki a **bike-no.csv** fájlt a helyi számítógépen. Ez az [előfeltételként](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/bike-no.csv)letöltött fájl.

    1. Kattintson a **Tovább** gombra.

       A feltöltés befejezésekor a beállítások és az előnézet űrlap előre fel van töltve a fájl típusa alapján. 
       
    1. Győződjön meg arról, hogy a **beállítások és az előnézet** űrlap a következőképpen van feltöltve, majd válassza a **tovább** lehetőséget.
        
        Mező|Leírás| Az oktatóanyag értéke
        ---|---|---
        Fájlformátum|Meghatározza a fájlban tárolt adatelrendezést és-típust.| Tagolt
        Elválasztó|Egy vagy több karakter, amely egy &nbsp; egyszerű szövegben vagy más adatfolyamban található különálló, egymástól független régiók között határozza meg a határt. |Vessző
        Encoding|Meghatározza, hogy az adatkészletek olvasásához milyen bitet kell használni a séma-tábla.| UTF-8
        Oszlopfejlécek| Azt jelzi, hogy a rendszer hogyan kezeli az adatkészlet fejléceit (ha van ilyen).| Fejlécek használata az első fájlból
        Sorok kihagyása | Azt jelzi, hogy az adatkészletben hány, ha van ilyen, a sorok kimaradnak.| Nincs

    1. A **séma** űrlap lehetővé teszi az adatai további konfigurálását a kísérlethez. 
    
        1. Ebben a példában válassza az **alkalmi** és a **regisztrált** oszlopok figyelmen kívül hagyása lehetőséget. Ezek az oszlopok a  **CNT** oszlop részletezését jelentik, ezért nem tartalmazzák azokat.

        1. Ebben a példában a **Tulajdonságok** és a **típus** alapértelmezett értékei is megmaradnak. 
        
        1. Kattintson a **Tovább** gombra.

    1. A **részletek megerősítése** űrlapon ellenőrizze, hogy az információ megegyezik-e az **alapinformációk** és **beállítások és az előnézeti** űrlapok által korábban feltöltött adatokkal.

    1. Válassza a **Létrehozás** lehetőséget az adatkészlet létrehozásának befejezéséhez.

    1. Válassza ki az adatkészletet, amint megjelenik a listában.

    1. Válassza a  **tovább** lehetőséget.

## <a name="configure-run"></a>Futtatás konfigurálása

Miután betöltötte és konfigurálta az adatait, állítsa be a távoli számítási célt, és válassza ki, hogy az adatok mely oszlopát szeretné előre jelezni.

1. Töltse fel a **Run (Futtatás** ) űrlapot a következőképpen:
    1. Adja meg a kísérlet nevét: `automl-bikeshare`

    1. Válassza a **CNT** lehetőséget a cél oszlopként, amit előre meg kíván jeleníteni. Ez az oszlop a kerékpáros megosztások teljes bérletének számát jelzi.

    1. Válassza az **új számítás létrehozása** és a számítási cél konfigurálása lehetőséget. Az automatikus ML csak Azure Machine Learning számítást támogatja. 

        1. Töltse ki a **virtuális gép** űrlapját a számítás beállításához.

            Mező | Leírás | Az oktatóanyag értéke
            ----|---|---
            Virtuális &nbsp; gép &nbsp; prioritása |Válassza ki, hogy milyen prioritással rendelkezzen a kísérlet| Dedikált
            Virtuális &nbsp; gép &nbsp; típusa| Válassza ki a virtuális gép típusát a számítási feladatokhoz.|CPU (központi feldolgozó egység)
            Virtuális &nbsp; gép &nbsp; mérete| Válassza ki a virtuális gép méretét a számítási feladatokhoz. Az ajánlott méretek listája az adatai és a kísérlet típusa alapján érhető el. |Standard_DS12_V2
        
        1. Válassza a **tovább** lehetőséget a **beállítások konfigurálása űrlap** feltöltéséhez.
        
             Mező | Leírás | Az oktatóanyag értéke
            ----|---|---
            Számítási név |  A számítási környezet azonosítására szolgáló egyedi név. | Bike – számítás
            Csomópontok minimális/maximális száma| A profilhoz legalább 1 csomópontot kell megadnia.|Minimális csomópontok: 1<br>Csomópontok maximális száma: 6
            Leskálázás előtt üresjárati másodperc | Üresjárati idő a fürt automatikus skálázása előtt a csomópontok minimális száma szerint.|120 (alapértelmezett)
            Speciális beállítások | Beállítások egy virtuális hálózat konfigurálásához és engedélyezéséhez a kísérlethez.| Nincs 
  
        1. A számítási cél beszerzéséhez válassza a **Létrehozás** lehetőséget. 

            **Ez eltarthat néhány percet.** 

        1. A létrehozás után válassza ki az új számítási célt a legördülő listából.

    1. Kattintson a **Tovább** gombra.

## <a name="select-forecast-settings"></a>Előrejelzési beállítások kiválasztása

A Machine learning-feladattípus és a konfigurációs beállítások megadásával fejezze be az automatikus ML-kísérlet beállítását.

1. A feladattípus **és beállítások** űrlapon válassza az **idősorozat-előrejelzés** lehetőséget a Machine learning feladattípusként.

1. Válassza ki a **Date (dátum** ) **oszlopot az idő oszlopban** , és hagyja üresen az **idősorozat-azonosítókat** . 

1. Az **előrejelzési horizont** azt az időtartamot jelzi, ameddig a jövőben meg kívánja jósolni.  Törölje az automatikus észlelést, és írja be a 14 értéket a mezőbe. 

1. Válassza a **további konfigurációs beállítások megtekintése** lehetőséget, és töltse fel a mezőket az alábbiak szerint. Ezekkel a beállításokkal hatékonyabban vezérelheti a betanítási feladatot, és megadhatja az előrejelzés beállításait. Ellenkező esetben a rendszer az alapértelmezett értékeket a kísérletezés és az adatértékek alapján alkalmazza.

    További &nbsp; konfigurációk|Leírás|&nbsp;Az &nbsp; oktatóanyag értéke
    ------|---------|---
    Elsődleges metrika| Az értékelési metrika, amelyet a Machine learning algoritmusa fog mérni.|Normalizált legfelső szintű, négyzetes hiba
    A legjobb modell ismertetése| A automatikusan mutatja az automatizált ML által létrehozott legjobb modell magyarázatát.| Engedélyezés
    Letiltott algoritmusok | A betanítási feladatokból kizárni kívánt algoritmusok| Extrém véletlenszerű fák
    További előrejelzési beállítások| Ezek a beállítások segítenek a modell pontosságának javításában. <br><br> _**Előrejelzési cél késések:**_ milyen messzire kívánja állítani a cél változó késéseit <br> _**Cél gördülő ablak**_: megadja a gördülő ablak méretét, amely felett a funkciók, például a *Max, a min* és a *Sum* érték lesz létrehozva. | <br><br>Előrejelzési &nbsp; cél &nbsp; késései: nincs <br> Cél &nbsp; gördülési &nbsp; ablakának &nbsp; mérete: nincs
    Kilépési feltétel| Ha teljesülnek a feltételek, a betanítási feladatok leállnak. |Betanítási &nbsp; feladatok &nbsp; időpontja (óra): 3 <br> Metrika &nbsp; pontszámának &nbsp; küszöbértéke: nincs
    Érvényesítés | Válasszon egy több ellenőrzési típust és a tesztek számát.|Érvényesítés típusa:<br>&nbsp;k-szeres &nbsp; keresztek ellenőrzése <br> <br> Érvényességek száma: 5
    Egyidejűség| A másodpercenként végrehajtott párhuzamos ismétlések maximális száma| &nbsp;Egyidejű &nbsp; Ismétlések maximális száma: 6
    
    Kattintson a **Mentés** gombra.

## <a name="run-experiment"></a>Kísérlet futtatása

A kísérlet futtatásához válassza a **Befejezés** lehetőséget. Megnyílik a futtatási **részletek**  képernyő, amelyen a Futtatás **állapota** látható a futtatási szám mellett. Ez az állapot frissíti a kísérlet előrehaladását. Az értesítések a Studio jobb felső sarkában is megjelennek, hogy tájékoztassák a kísérlet állapotáról.

>[!IMPORTANT]
> Az előkészítés **10-15 percet** vesz igénybe a kísérlet futtatásának előkészítése érdekében.
> A futása után **az egyes iterációk esetében 2-3 percet** vesz igénybe.<br> <br>
> Éles környezetben valószínű, hogy ez a folyamat időt vesz igénybe. Várakozás közben javasoljuk, hogy a kipróbált algoritmusokat a kész **modellek** lapon vizsgálja meg. 

##  <a name="explore-models"></a>Modellek megismerése

Navigáljon a **modellek** lapra, és tekintse meg a tesztelt algoritmusokat (modelleket). Alapértelmezés szerint a modelleket metrikai pontszám szerint rendezi a rendszer. Ebben az oktatóanyagban a legmagasabbra értékelt modell, amely a kiválasztott **normalizált legfelső szintű, négyzetes hiba** mérőszáma alapján a legmagasabb, a lista tetején található.

Amíg megvárja az összes kísérleti modell befejeződését, válassza ki a befejezett modell **algoritmusának nevét** a teljesítmény részleteinek megismeréséhez. 

A következő példa a **részletek** és a **metrikák** lapokon keresztül navigál a kiválasztott modell tulajdonságainak, metrikáinak és teljesítmény-diagramjainak megtekintéséhez. 

![Futtatás részletei](./media/tutorial-automated-ml-forecast/explore-models.gif)

## <a name="deploy-the-model"></a>A modell üzembe helyezése

A Azure Machine Learning Studióban található automatizált gépi tanulás lehetővé teszi a legjobb modell üzembe helyezését webszolgáltatásként néhány lépésben. Az üzembe helyezés a modell integrációja, így előre jelezhető az új adatmennyiség, és azonosíthatók a lehetséges lehetőségek is. 

Ebben a kísérletben a webszolgáltatások üzembe helyezése azt jelenti, hogy a bike Share vállalat most már rendelkezik egy iterációs és méretezhető webes megoldással, amellyel előre jelezheti a kerékpárok megosztását. 

A Futtatás befejezése után váltson vissza a szülő futtatása lapra a képernyő tetején található **1. Futtatás** gombra kattintva.

A **legjobb modell összegzése** szakaszban a **StackEnsemble** a legjobb modellnek számít a kísérlet kontextusában, a **normalizált legfelső szintű, négyzetes hiba** mérőszáma alapján.  

Ezt a modellt üzembe helyezjük, de javasoljuk, hogy az üzembe helyezés körülbelül 20 percet vesz igénybe. Az üzembe helyezési folyamat több lépést is magában foglal, beleértve a modell regisztrálását, az erőforrások létrehozását és a webszolgáltatás konfigurálását.

1. Válassza a **StackEnsemble** lehetőséget a modell-specifikus oldal megnyitásához.

1. Kattintson a képernyő bal felső részén található **központi telepítés** gombra.

1. Töltse fel a **modell üzembe helyezése** panelt az alábbiak szerint:

    Mező| Érték
    ----|----
    Központi telepítés neve| bikeshare – üzembe helyezés
    Központi telepítés leírása| a bike Share kereslet üzembe helyezése
    Számítási típus | Azure számítási példány kiválasztása (ACI)
    Hitelesítés engedélyezése| Letiltás lehetőséget. 
    Egyéni központi telepítési eszközök használata| Letiltás lehetőséget. A Letiltás beállítás megadása esetén az alapértelmezett illesztőprogram-fájl (pontozási parancsfájl) és a környezeti fájl automatikusan létrejön. 
    
    Ebben a példában a *speciális* menüben megadott alapértékeket használjuk. 

1. Válassza az **Üzembe helyezés** lehetőséget.  

    A **futtatási** képernyő tetején megjelenik egy zöld sikert jelző üzenet, amely azt jelzi, hogy a központi telepítés sikeresen elindult. Az üzembe helyezés folyamata a **modell összegzése** ablaktáblán, az **üzembe helyezés állapota** területen található.
    
Miután az üzembe helyezés sikeres volt, rendelkezik egy operatív webszolgáltatással az előrejelzések létrehozásához. 

Folytassa a [**következő lépésekkel**](#next-steps) , ha többet szeretne megtudni az új webszolgáltatás használatáról, és tesztelje a jóslatokat a Power BI beépített Azure Machine learning-támogatásával.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az üzembe helyezési fájlok nagyobb méretűek, mint az adatmennyiség és a kísérlet során használt fájlok. Csak a központi telepítési fájlokat törölje, hogy csökkentse a költségeket a fiókjához, vagy ha meg szeretné őrizni a munkaterületet és a kísérletet. Ellenkező esetben törölje a teljes erőforráscsoportot, ha nem tervezi az egyik fájl használatát.  

### <a name="delete-the-deployment-instance"></a>A központi telepítési példány törlése

Törölje a központi telepítési példányt a Azure Machine Learning studióból, ha meg szeretné tartani az erőforráscsoportot és a munkaterületet más oktatóanyagok és feltárás céljából. 

1. Nyissa meg a [Azure Machine learning studiót](https://ml.azure.com/). Navigáljon a munkaterülethez, és a bal oldalon az **eszközök** ablaktáblán válassza a **végpontok** lehetőséget. 

1. Válassza ki a törölni kívánt központi telepítést, és válassza a **Törlés** lehetőséget. 

1. Válassza a **Folytatás** lehetőséget.

### <a name="delete-the-resource-group"></a>Az erőforráscsoport törlése

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban az automatikus ML-t használta a Azure Machine Learning Studióban egy idősorozat-előrejelzési modell létrehozásához és üzembe helyezéséhez, amely előre jelezi a bike Share bérleti igényét. 

Ebből a cikkből megtudhatja, hogyan hozhat létre Power BI támogatott sémát az újonnan telepített webszolgáltatás felhasználásának megkönnyítésére:

> [!div class="nextstepaction"]
> [Webszolgáltatás felhasználása](/power-bi/connect-data/service-aml-integrate?context=azure%2fmachine-learning%2fcontext%2fml-context)

+ További információ az [automatizált gépi tanulásról](concept-automated-ml.md).
+ A besorolási metrikákkal és diagramokkal kapcsolatos további információkért tekintse meg az [automatizált gépi tanulás eredményeinek megismerése](how-to-understand-automated-ml.md) című cikket.
+ További információ a [featurization](how-to-configure-auto-features.md#featurization).
+ További információ az [adatprofilkészítésről](how-to-connect-data-ui.md#profile).

>[!NOTE]
> Ez a kerékpáros megosztási adatkészlet módosult ehhez az oktatóanyaghoz. Ezt az adatkészletet egy [Kaggle-verseny](https://www.kaggle.com/c/bike-sharing-demand/data) részeként tették elérhetővé, és eredetileg a [Capital Bikeshare](https://www.capitalbikeshare.com/system-data)keresztül adták ki. Az [UCI Machine learning adatbázisban](http://archive.ics.uci.edu/ml/datasets/Bike+Sharing+Dataset)is megtalálható.<br><br>
> Forrás: Fanaee-T, hadi és Gama, Joao, esemény címkézése az Ensemble-érzékelők és a háttérbeli ismeretek kombinálásával, a mesterséges intelligencia előrehaladásával (2013): PP. 1-15, Springer Berlin Heidelberg.