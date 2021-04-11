---
title: 'ML Studio (klasszikus): webszolgáltatás üzembe helyezése – Azure'
description: Betanítási kísérlet előkészítése prediktív kísérletre, előkészítés üzembe helyezéshez, majd Azure Machine Learning Studio (klasszikus) webszolgáltatásként való üzembe helyezése.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 01/06/2017
ms.openlocfilehash: 018e292abc7050f5d71c6bf9065618fecf8b5668
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "100520442"
---
# <a name="deploy-an-azure-machine-learning-studio-classic-web-service"></a>Azure Machine Learning Studio (klasszikus) webszolgáltatás üzembe helyezése

**a következőkre vonatkozik:** ![ Ez egy pipa, ami azt jelenti, hogy ez a cikk a Machine Learning Studio (klasszikus) elemre vonatkozik. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasszikus) ![ Ez egy X, ami azt jelenti, hogy ez a cikk Azure Machine learningre vonatkozik.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


A Azure Machine Learning Studio (klasszikus) lehetővé teszi prediktív elemzési megoldások készítését és tesztelését. Ezután webszolgáltatásként üzembe helyezheti a megoldást.

A Machine Learning Studio (klasszikus) webszolgáltatások felületet biztosítanak egy alkalmazás és egy Machine Learning Studio (klasszikus) munkafolyamat pontozási modellje között. Egy külső alkalmazás valós időben tud kommunikálni egy Machine Learning Studio (klasszikus) munkafolyamat-pontozási modellel. Egy Machine Learning Studio (klasszikus) webszolgáltatás hívása visszaadja az előrejelzési eredményeket egy külső alkalmazásnak. A webszolgáltatások felé irányuló hívások indításához át kell adnia azt az API-kulcsot, amelyet a webszolgáltatás üzembe helyezésekor hozott létre. A Machine Learning Studio (klasszikus) webszolgáltatás egy REST-alapú, népszerű architektúra a webes programozási projektekhez.

A Azure Machine Learning Studio (klasszikus) webszolgáltatásoknak két típusa van:

* Request-Response szolgáltatás (ERŐFORRÁSREKORDOK): alacsony késésű, rugalmasan méretezhető szolgáltatás, amely egyetlen adatrekordot mutat be.
* Batch végrehajtási szolgáltatás (BES): egy aszinkron szolgáltatás, amely adatrekordok kötegét szerzi be.

A BES bemeneti adatai az RRS-ben használt bemeneti adatokhoz hasonlóak. A fő különbség az, hogy a BES több különböző forrásból olvassa be az adatköteget, például az Azure Blob Storage-ból és Azure Table Storage-ből, az Azure SQL Database-ből, a HDInsightból (Hive-lekérdezés) és HTTP-forrásokból.

A magas szintű nézetből három lépésben helyezheti üzembe a modelljét:

* **[Hozzon létre egy betanítási kísérletet]** a Studióban (klasszikus). a prediktív elemzési modellt betaníthatja és tesztelheti a betanítási adataival, és számos beépített gépi tanulási algoritmust használhat.
* **[Alakítsa át prediktív kísérletre]** – ha a modell már meglévő adataival lett betanítva, és készen áll arra, hogy új adatgyűjtést végezzen, előkészíti és egyszerűsíti a kísérletet az előrejelzésekhez.
* **Üzembe helyezés** **[új webszolgáltatásként]** vagy **[klasszikus webszolgáltatásként]** – ha a prediktív kísérletet Azure webszolgáltatásként helyezi üzembe, a felhasználók elküldhetik az adatait a modellbe, és megkapják a modell előrejelzéseit.

## <a name="create-a-training-experiment"></a>Képzési kísérlet létrehozása

A prediktív elemzési modellek betanításához Azure Machine Learning Studio (klasszikus) használatával hozzon létre egy képzési kísérletet, ahol különféle modulokat tartalmaz a betanítási adatgyűjtés betöltéséhez, a szükséges, a gépi tanulási algoritmusok alkalmazásához, valamint az eredmények kiértékeléséhez. Megismételheti a kísérletet, és különböző gépi tanulási algoritmusok kipróbálásával összehasonlíthatja és értékelheti az eredményeket.

A képzési kísérletek létrehozásának és kezelésének folyamata máshol még alaposabban szerepel. További információért lásd a következő cikkeket:

* [Egyszerű kísérlet létrehozása Azure Machine Learning Studio (klasszikus)](create-experiment.md)
* [Prediktív megoldás fejlesztése Azure Machine Learning Studio (klasszikus)](tutorial-part1-credit-risk.md)
* [Betanítási adatai importálása Azure Machine Learning Studioba (klasszikus)](import-data.md)
* [Kísérletezési ismétlések kezelése Azure Machine Learning Studio (klasszikus)](manage-experiment-iterations.md)

## <a name="convert-the-training-experiment-to-a-predictive-experiment"></a>A betanítási kísérlet átalakítása prediktív kísérletre

Ha betanítja a modellt, készen áll arra, hogy a betanítási kísérletet egy prediktív kísérletbe konvertálja, hogy az új adatait felhasználja.

A prediktív kísérletre való áttéréssel a betanított modell készen áll a pontozási webszolgáltatásként való üzembe helyezésre. A webszolgáltatás felhasználói bemeneti adatokat küldhetnek a modellbe, és a modell visszaküldi az előrejelzési eredményeket. A prediktív kísérletre való áttérés során ne feledje, hogy a modelleket mások is használják.

A betanítási kísérlet prediktív kísérletre való átalakításának folyamata három lépést tesz szükségessé:

1. Cserélje le a Machine learning-algoritmus moduljait a betanított modellre.
2. A kísérletet csak azokra a modulokra vágja, amelyek a pontozáshoz szükségesek. A betanítási kísérlet több modult is tartalmaz, amelyek szükségesek a képzéshez, de nem szükségesek a modell betanítása után.
3. Határozza meg, hogy a modell hogyan fogadja el az adatok elfogadását a webszolgáltatás-felhasználótól, és hogy milyen adatok lesznek visszaadva.

> [!TIP]
> A betanítási kísérlet során a saját adatai alapján a modell betanításával és pontozásával foglalkozott. Az üzembe helyezést követően azonban a felhasználók új adatait küldenek a modellbe, és az előrejelzési eredményeket fogják visszaadni. Tehát, ahogy átalakítja a betanítási kísérletet egy prediktív kísérletre, hogy készen álljon az üzembe helyezésre, ne feledje, hogy a modelleket mások is használják.

![Konvertálás pontozási kísérletre](./media/publish-a-machine-learning-web-service/figure-1.png)

### <a name="set-up-web-service-button"></a>Webszolgáltatás beállítása gomb
A kísérlet futtatása után (a kísérlet vászon alján kattintson a **Futtatás** gombra), kattintson a **webszolgáltatás beállítása** gombra (válassza ki a **prediktív webszolgáltatás** lehetőséget). A **webszolgáltatás beállítása** a betanítási kísérlet prediktív kísérletre történő átalakításának három lépését végzi el:

1. Elmenti a betanított modellt a modul palettájának **betanított modellek** szakaszában (a kísérlet vászontól balra). Ezután lecseréli a Machine learning-algoritmust és [betanítja a modell][train-model] modulokat a mentett betanított modellel.
2. Elemzi a kísérletet, és eltávolítja azokat a modulokat, amelyeket egyértelműen csak képzésre használtak, és amelyekre már nincs szükség.
3. A _webszolgáltatások bemeneti_ és _kimeneti_ moduljait a kísérlet alapértelmezett helyeire szúrja be (ezek a modulok elfogadják és visszaadják a felhasználói adatokat).

Az alábbi kísérlet például egy kétosztályos, kibővített döntési famodellt alkalmaz a minta-számbavételi adataival:

![Betanítási kísérlet](./media/convert-training-experiment-to-scoring-experiment/figure1.png)

A kísérlet moduljai alapvetően négy különböző funkciót látnak el:

![Modul függvények](./media/convert-training-experiment-to-scoring-experiment/figure2.png)

Ha ezt a betanítási kísérletet prediktív kísérletre konvertálja, ezeknek a moduloknak már nincs rá szüksége, vagy már más célt szolgálnak:

* **Adat** – a minta adatkészletben lévő adatokat a rendszer nem használja pontozás közben – a webszolgáltatás felhasználója megadja az adatokat. A betanított modell azonban a metaadatokat, például az adattípusokat használja. Ezért meg kell őriznie az adatkészletet a prediktív kísérletben, hogy meg tudja adni ezt a metaadatokat.

* **PREP** – attól függően, hogy milyen felhasználói adat lesz elküldve a pontozáshoz, ezek a modulok esetleg nem szükségesek a bejövő adat feldolgozásához. A **webszolgáltatás beállítása** gomb nem érinti ezeket – el kell döntenie, hogyan szeretné kezelni őket.
  
    Ebben a példában például a minta adatkészlet hiányzó értékekkel rendelkezhet, ezért a rendszer egy [tiszta hiányzó][clean-missing-data] adatmodult tartalmazott a velük való kezeléshez. Emellett a minta adatkészlet olyan oszlopokat is tartalmaz, amelyek nem szükségesek a modell betanításához. Így az [adatkészlet modul Select oszlopai][select-columns] is kizárhatók a további oszlopoknak az adatfolyamatból való kizárásával. Ha tudja, hogy a webszolgáltatáson keresztüli pontozásra elküldött adatok nem rendelkeznek hiányzó értékekkel, akkor eltávolíthatja a [hiányzó adatok][clean-missing-data] törlése modult. Mivel azonban az [adatkészlet modul Select oszlopai][select-columns] segítenek meghatározni a betanított modell által várt adatoszlopokat, a modulnak meg kell maradnia.

* **Betanítás** – ezek a modulok a modell betanítására szolgálnak. Ha a **webszolgáltatás beállítása** elemre kattint, a rendszer lecseréli ezeket a modulokat egyetlen modulra, amely tartalmazza a betanított modellt. Ezt az új modult a modul paletta **betanított modellek** szakasza menti.

* **Pontszám** – ebben a [példában az adatstreamek a][split] tesztelési és a betanítási adatként való felosztására szolgálnak. A prediktív kísérletben nem dolgozunk fel többé, ezért a [felosztott adatvesztést][split] el lehet távolítani. Hasonlóképpen, a második [pontszámot modellező][score-model] modul és a [modell kiértékelése][evaluate-model] modul a vizsgálati adatok eredményeinek összehasonlítására szolgál, így ezek a modulok nem szükségesek a prediktív kísérlet során. A fennmaradó [pontszám modell][score-model] modulnak azonban a webszolgáltatáson keresztüli pontszám eredményét kell visszaadnia.

A példa a **webszolgáltatás beállítása** elemre való kattintás után a következőképpen néz ki:

![Átalakított prediktív kísérlet](./media/convert-training-experiment-to-scoring-experiment/figure3.png)

A **webszolgáltatások beállításával** végzett munka elegendő lehet a kísérlet webszolgáltatásként való üzembe helyezésének előkészítéséhez. Azonban érdemes lehet elvégezni egy további, a kísérlethez kapcsolódó munkát.

#### <a name="adjust-input-and-output-modules"></a>Bemeneti és kimeneti modulok módosítása
A betanítási kísérlet során a betanítási adatkészletet használta, majd elvégezte a feldolgozást, hogy a gépi tanulási algoritmust igénylő űrlapon beolvassa az adatait. Ha a webszolgáltatásban várhatóan megjelenő adatokra nem lesz szükség a feldolgozásra, megkerülheti azt: a **webszolgáltatások bemeneti moduljának** kimenetét a kísérlet egy másik moduljának összekapcsolásával is elvégezheti. A felhasználó ekkor megérkezik a modellbe ezen a helyen.

Alapértelmezés szerint például a **webszolgáltatás beállítása** a **webszolgáltatás bemeneti** modulját az adatfolyamat tetején helyezi el, ahogy az a fenti ábrán is látható. Azonban manuálisan is elhelyezheti a **webszolgáltatás bemenetét** az adatfeldolgozó moduloknál:

![Webszolgáltatás bemenetének áthelyezése](./media/convert-training-experiment-to-scoring-experiment/figure4.png)

A webszolgáltatáson keresztül megadott bemeneti adatok mostantól közvetlenül a pontszám modell modulba kerülnek, előfeldolgozás nélkül.

Hasonlóképpen, alapértelmezés szerint a **webszolgáltatás** az adatfolyam alján helyezi üzembe a webszolgáltatás kimeneti modulját. Ebben a példában a webszolgáltatás visszaadja a felhasználónak a [pontszám modell][score-model] modul kimenetét, amely magában foglalja a teljes bemeneti adatvektort, valamint a pontozás eredményét.
Ha azonban inkább egy másikat szeretne visszaadni, akkor a **webszolgáltatás kimeneti** modulja előtt további modulokat is hozzáadhat. 

Ha például csak a pontozás eredményét szeretné visszaadni, és nem a bemeneti adatok teljes vektorát, vegyen fel egy [Select oszlopot az adatkészlet][select-columns] modulban az összes oszlop kizárásához, kivéve a pontozás eredményét. Ezután helyezze át a **webszolgáltatás kimeneti** modulját a [Select oszlopok][select-columns] kimenetére az adatkészlet modulban. A kísérlet így néz ki:

![Webszolgáltatás kimenetének áthelyezése](./media/convert-training-experiment-to-scoring-experiment/figure5.png)

#### <a name="add-or-remove-additional-data-processing-modules"></a>További adatfeldolgozási modulok hozzáadása vagy eltávolítása
Ha a kísérletben több modul is van, de nem lesz szükség a pontozásra, ezeket a rendszer eltávolítja. Mivel például áthelyezte a **webszolgáltatás bemeneti** modulját az adatfeldolgozási modulok utáni pontra, eltávolíthatja a [tiszta hiányzó][clean-missing-data] adatmodult a prediktív kísérletből.

A prediktív kísérlet így néz ki:

![További modul eltávolítása](./media/convert-training-experiment-to-scoring-experiment/figure6.png)


#### <a name="add-optional-web-service-parameters"></a>Opcionális webszolgáltatás-paraméterek hozzáadása
Bizonyos esetekben előfordulhat, hogy lehetővé szeretné tenni a webszolgáltatás felhasználójának, hogy megváltoztassa a modulok viselkedését a szolgáltatás elérésekor. A *webszolgáltatás paraméterei* lehetővé teszik ezt.

Gyakori példa egy [adatimportálási][import-data] modul beállítása, hogy a telepített webszolgáltatás felhasználója más adatforrást is megadhat a webszolgáltatáshoz való hozzáféréskor. Vagy az [exportálási][export-data] modul konfigurálása, hogy egy másik célhely is megadható legyen.

Meghatározhatja a webszolgáltatás paramétereit, és társíthatja azokat egy vagy több modul-paraméterrel, és megadhatja, hogy kötelező vagy opcionális-e. A webszolgáltatás felhasználója ezeket a paramétereket adja meg, ha a szolgáltatás elérhető, és a modul műveletei ennek megfelelően módosulnak.

A webszolgáltatás-paraméterekkel és azok használatának módjával kapcsolatos további információkért lásd: [Azure Machine learning webszolgáltatás paramétereinek használata][webserviceparameters].

A következő lépések leírják, hogyan helyezhet üzembe egy prediktív kísérletet új webszolgáltatásként. A kísérletet klasszikus webszolgáltatásként is üzembe helyezheti.

## <a name="deploy-it-as-a-new-web-service"></a>Üzembe helyezés új webszolgáltatásként

Most, hogy előkészítette a prediktív kísérletet, üzembe helyezheti új (Resource Manager-alapú) Azure-webszolgáltatásként. A webszolgáltatás használatával a felhasználók elküldhetik az adatait a modellbe, és a modell visszaadja az előrejelzéseit.

A prediktív kísérlet üzembe helyezéséhez kattintson a **Run (Futtatás** ) gombra a kísérlet vászon alján. A kísérlet befejezését követően kattintson a **webszolgáltatás üzembe helyezése** lehetőségre, és válassza a **webszolgáltatás [új]telepítése** lehetőséget.  Megnyílik a Machine Learning Studio (klasszikus) webszolgáltatás-portál üzembe helyezés lapja.

> [!NOTE] 
> Új webszolgáltatás telepítéséhez megfelelő engedélyekkel kell rendelkeznie ahhoz az előfizetéshez, amelyhez a webszolgáltatást telepíti. További információ: [webszolgáltatások kezelése a Azure Machine learning webszolgáltatások portálján](manage-new-webservice.md). 

### <a name="web-service-portal-deploy-experiment-page"></a>Webszolgáltatási portál üzembe helyezési kísérlet lapja

A kísérlet telepítése lapon adja meg a webszolgáltatás nevét.
Válasszon árképzési tervet. Ha meglévő díjszabási csomaggal rendelkezik, akkor kiválaszthatja azt, ellenkező esetben létre kell hoznia egy új díjcsomagot a szolgáltatáshoz.

1. Az **árlista** legördülő menüben válasszon ki egy meglévő csomagot, vagy válassza az **új csomag kiválasztása** lehetőséget.
2. A **csomag neve** mezőben adjon meg egy nevet, amely azonosítja a csomagot a számlán.
3. Válassza ki a **havi díjcsomag** egyikét. A csomag alapértelmezett értéke az alapértelmezett régió csomagjai, a webszolgáltatás pedig az adott régióban van üzembe helyezve.

Kattintson a **telepítés** elemre, és **megnyílik a webszolgáltatás** rövid útmutató lapja.

A webszolgáltatás gyors üzembe helyezése oldalon a webszolgáltatás létrehozása után elvégezhető leggyakoribb feladatokhoz férhet hozzá. Innen könnyedén elérheti a tesztoldal és a használat oldalát is.

<!-- ![Deploy the web service](./media/publish-a-machine-learning-web-service/figure-2.png)-->

### <a name="test-your-new-web-service"></a>Az új webszolgáltatás tesztelése

Az új webszolgáltatás teszteléséhez kattintson a gyakori feladatok területen a **webszolgáltatás tesztelése** elemre. A teszt lapon tesztelheti a webszolgáltatást Request-Response szolgáltatásként (RR) vagy kötegelt végrehajtási szolgáltatásként (BES).

Az ERŐFORRÁSREKORDOK tesztelése oldalon megjelennek a kísérlethez megadott bemenetek, kimenetek és a globális paraméterek. A webszolgáltatás teszteléséhez manuálisan megadhatja a bemenetek megfelelő értékeit, vagy megadhatja a tesztelési értékeket tartalmazó vesszővel tagolt (CSV) formátumú fájlt.

Ha az ERŐFORRÁSREKORDOK használatával szeretne tesztelni, a listanézet módból írja be a megfelelő értékeket a bemenetekhez, és kattintson a **kérelem-válasz tesztelése** elemre. Az előrejelzés eredményei a kimenet oszlopban a bal oldalon jelennek meg.

![Adja meg a megfelelő értékeket a webszolgáltatás teszteléséhez](./media/publish-a-machine-learning-web-service/figure-5-test-request-response.png)

A BES teszteléséhez kattintson a **Batch** elemre. A Batch test lapon kattintson a Tallózás elemre a bevitel alatt, és válasszon ki egy CSV-fájlt, amely tartalmazza a megfelelő értékeket. Ha nem rendelkezik CSV-fájllal, és létrehozta a prediktív kísérletet Machine Learning Studio (klasszikus) használatával, letöltheti az adatkészletet a prediktív kísérlethez, és használhatja azt.

Az adathalmaz letöltéséhez nyissa meg Machine Learning Studio (klasszikus). Nyissa meg a prediktív kísérletet, és kattintson a jobb gombbal a kísérlethez tartozó bemenetre. A helyi menüben válassza az **adatkészlet** elemet, majd válassza a **Letöltés** lehetőséget.

![Az adatkészlet letöltése a Studio (klasszikus) vászonról](./media/publish-a-machine-learning-web-service/figure-7-mls-download.png)

Kattintson a **teszt** gombra. A Batch-végrehajtási feladat állapota a **teszt batch-feladatok** területen a jobb oldalon jelenik meg.

![A Batch-végrehajtási feladatok tesztelése a webszolgáltatás-portálon](./media/publish-a-machine-learning-web-service/figure-6-test-batch-execution.png)

<!--![Test the web service](./media/publish-a-machine-learning-web-service/figure-3.png)-->

A **konfiguráció** lapon módosíthatja a leírást, a címet, a Storage-fiók kulcsát, és engedélyezheti a webszolgáltatáshoz tartozó mintaadatok beküldését.

![Webszolgáltatás konfigurálása](./media/publish-a-machine-learning-web-service/figure-8-arm-configure.png)

### <a name="access-your-new-web-service"></a>Hozzáférés az új webszolgáltatáshoz

Miután telepítette a webszolgáltatását a Machine Learning Studioról (klasszikus), elküldheti az adatait a szolgáltatásnak, és programozott módon fogadhatja a válaszokat.

A  felhasználat oldal a webszolgáltatáshoz való hozzáféréshez szükséges összes információt tartalmazza. Az API-kulcs például a szolgáltatáshoz való jogosult hozzáférés engedélyezésére szolgál.

A Machine Learning Studio (klasszikus) webszolgáltatás elérésével kapcsolatos további információkért lásd: [Azure Machine learning Studio (klasszikus) webszolgáltatás felhasználása](consume-web-services.md).

### <a name="manage-your-new-web-service"></a>Az új webszolgáltatás kezelése

Az új webszolgáltatásokat Machine Learning Studio (klasszikus) webszolgáltatási portál használatával kezelheti. A [fő portál lapon](https://services.azureml.net/)kattintson a **webszolgáltatások** elemre. A webszolgáltatások lapon törölheti vagy másolhatja a szolgáltatást. Egy adott szolgáltatás figyeléséhez kattintson a szolgáltatásra, majd az **irányítópult** elemre. A webszolgáltatáshoz társított batch-feladatok figyeléséhez kattintson a **Batch-kérelmek naplója** elemre.

### <a name="deploy-your-new-web-service-to-multiple-regions"></a><a id="multi-region"></a> Az új webszolgáltatás üzembe helyezése több régióban

Egyszerűen üzembe helyezhet egy új webszolgáltatást több régióban, anélkül, hogy több előfizetést vagy munkaterületet kellene használnia.

A díjszabás régió-specifikus, ezért meg kell határoznia egy számlázási tervet minden olyan régióhoz, amelyben telepíteni fogja a webszolgáltatást.

#### <a name="create-a-plan-in-another-region"></a>Csomag létrehozása egy másik régióban

1. Jelentkezzen be [Microsoft Azure Machine learning webszolgáltatásba](https://services.azureml.net/).
2. Kattintson a **csomagok** menüpontra.
3. A megjelenő csomagok nézet lapon kattintson az **új** elemre.
4. Az **előfizetés** legördülő listából válassza ki azt az előfizetést, amelyben az új terv fog szerepelni.
5. A **régió** legördülő listából válassza ki az új csomag régióját. A kiválasztott régióhoz tartozó csomag beállításai a lap **csomag beállításai** szakaszában jelennek meg.
6. Az **erőforráscsoport** legördülő listából válassza ki a csomaghoz tartozó erőforráscsoportot. További információ az erőforráscsoportok használatáról: [Azure Resource Manager Overview (áttekintés](../../azure-resource-manager/management/overview.md)).
7. A **csomag neve** mezőbe írja be a csomag nevét.
8. A **terv beállításai** területen kattintson az új csomag számlázási szintjére.
9. Kattintson a **Létrehozás** lehetőségre.

#### <a name="deploy-the-web-service-to-another-region"></a>Webszolgáltatás üzembe helyezése egy másik régióban

1. A Microsoft Azure Machine Learning webszolgáltatások lapon kattintson a **webszolgáltatások** menüpontra.
2. Válassza ki az új régióba telepítendő webszolgáltatást.
3. Kattintson a **Másolás** gombra.
4. A **webszolgáltatás neve** mezőbe írja be a webszolgáltatás új nevét.
5. A **webszolgáltatás leírása** mezőbe írja be a webszolgáltatás leírását.
6. Az **előfizetés** legördülő listából válassza ki azt az előfizetést, amelyben az új webszolgáltatást tárolni fogja.
7. Az **erőforráscsoport** legördülő menüben válasszon ki egy erőforráscsoportot a webszolgáltatáshoz. További információ az erőforráscsoportok használatáról: [Azure Resource Manager Overview (áttekintés](../../azure-resource-manager/management/overview.md)).
8. A **régió** legördülő listából válassza ki azt a régiót, amelyben a webszolgáltatást telepíteni kívánja.
9. A **Storage-fiók** legördülő listából válassza ki azt a Storage-fiókot, amelyben a webszolgáltatást tárolni szeretné.
10. Az **árlista** legördülő menüben válasszon ki egy csomagot a 8. lépésben kiválasztott régióban.
11. Kattintson a **Másolás** gombra.

## <a name="deploy-it-as-a-classic-web-service"></a>Üzembe helyezés klasszikus webszolgáltatásként

Most, hogy a prediktív kísérlet megfelelően elkészült, telepítheti klasszikus Azure-alapú webszolgáltatásként. A webszolgáltatás használatával a felhasználók elküldhetik az adatait a modellbe, és a modell visszaadja az előrejelzéseit.

A prediktív kísérlet üzembe helyezéséhez kattintson a **Futtatás** gombra a kísérlet vászon alján, majd kattintson a **webszolgáltatás telepítése** parancsra. A webszolgáltatás be van állítva, és a webszolgáltatás irányítópultra kerül.

![Webszolgáltatások üzembe helyezése a studióból (klasszikus)](./media/publish-a-machine-learning-web-service/figure-2.png)

### <a name="test-your-classic-web-service"></a>A klasszikus webszolgáltatás tesztelése

A webszolgáltatást a Machine Learning Studio (klasszikus) webszolgáltatások portálon vagy Machine Learning Studio (klasszikus) is tesztelheti.

A kérelem válasz webszolgáltatásának teszteléséhez kattintson a **teszt** gombra a webszolgáltatás irányítópultján. Megjelenik egy párbeszédpanel, amely rákérdez a szolgáltatás bemeneti adatára. Ezek a pontozási kísérlet által várt oszlopok. Adjon meg egy adatkészletet, majd kattintson **az OK** gombra. A webszolgáltatás által generált eredmények az irányítópult alján jelennek meg.

A **tesztelési** előnézet hivatkozásra kattintva tesztelheti a szolgáltatást a Azure Machine learning Studio (klasszikus) webszolgáltatások portálján, ahogyan az az új webszolgáltatás szakaszban is látható.

A Batch-végrehajtási szolgáltatás teszteléséhez kattintson a **teszt** előnézet hivatkozásra. A Batch test lapon kattintson a Tallózás elemre a bevitel alatt, és válasszon ki egy CSV-fájlt, amely tartalmazza a megfelelő értékeket. Ha nem rendelkezik CSV-fájllal, és létrehozta a prediktív kísérletet Machine Learning Studio (klasszikus) használatával, letöltheti az adatkészletet a prediktív kísérlethez, és használhatja azt.

![A webszolgáltatás tesztelése](./media/publish-a-machine-learning-web-service/figure-3.png)

A **konfiguráció** lapon módosíthatja a szolgáltatás megjelenítendő nevét, és megadhatja a leírását. A név és a leírás a webszolgáltatások kezelése [Azure Portalban](https://portal.azure.com/) jelenik meg.

Megadhatja a bemeneti adatok, a kimeneti adatok és a webszolgáltatás paramétereinek leírását, ha megad egy karakterláncot minden oszlophoz a **bemeneti séma**, a **kimeneti séma** és a **webszolgáltatás paraméter** alatt. Ezeket a leírásokat a webszolgáltatáshoz megadott mintakód-dokumentációban lehet használni.

Engedélyezheti a naplózást a webszolgáltatás elérésekor észlelt hibák diagnosztizálásához. További információt a [Machine learning Studio (klasszikus) webszolgáltatások naplózásának engedélyezése](web-services-logging.md)című témakörben talál.

![A naplózás engedélyezése a webszolgáltatások portálján](./media/publish-a-machine-learning-web-service/figure-4.png)

A webszolgáltatáshoz tartozó végpontokat a Azure Machine Learning webszolgáltatások portálon is konfigurálhatja, hasonlóan az új webszolgáltatás szakaszban bemutatott eljáráshoz. A lehetőségek különbözőek, hozzáadhat vagy módosíthat a szolgáltatás leírását, engedélyezheti a naplózást, és engedélyezheti a mintaadatok tesztelését.

### <a name="access-your-classic-web-service"></a>Hozzáférés a klasszikus webszolgáltatáshoz

Miután telepítette a webszolgáltatását a Azure Machine Learning Studioról (klasszikus), elküldheti az adatait a szolgáltatásnak, és programozott módon fogadhatja a válaszokat.

Az irányítópulton a webszolgáltatáshoz való hozzáféréshez szükséges összes információ elérhető. Az API-kulcs például lehetővé teszi, hogy engedélyezze a hozzáférést a szolgáltatáshoz, és az API-Súgó lapjai segítséget nyújtanak a kód megírásában.

A Machine Learning Studio (klasszikus) webszolgáltatás elérésével kapcsolatos további információkért lásd: [Azure Machine learning Studio (klasszikus) webszolgáltatás felhasználása](consume-web-services.md).

### <a name="manage-your-classic-web-service"></a>A klasszikus webszolgáltatás kezelése

A webszolgáltatás figyeléséhez számos művelet hajtható végre. Frissítheti, és törölheti is. Emellett további végpontokat is hozzáadhat egy klasszikus webszolgáltatáshoz, a központi telepítéskor létrehozott alapértelmezett végpont mellett.

További információ: [Azure Machine learning Studio (klasszikus) munkaterület kezelése](manage-workspace.md) és [webszolgáltatás kezelése a Azure Machine learning Studio (klasszikus) webszolgáltatások portálján](manage-new-webservice.md).

## <a name="update-the-web-service"></a>Webszolgáltatás frissítése
Módosíthatja a webszolgáltatást, például frissítheti a modellt további betanítási adataival, és újra telepítheti az eredeti webszolgáltatás felülírásával.

A webszolgáltatás frissítéséhez nyissa meg a webszolgáltatás üzembe helyezéséhez használt eredeti prediktív kísérletet, és végezze el a szerkeszthető másolást a **Mentés másként lehetőségre** kattintva. Végezze el a módosításokat, majd kattintson a **webszolgáltatás üzembe helyezése** lehetőségre.

Mivel korábban már üzembe helyezte ezt a kísérletet, a rendszer megkérdezi, hogy szeretné-e felülírni (klasszikus webszolgáltatás) vagy a meglévő szolgáltatás frissítését (új webszolgáltatás). Az **Igen** vagy a **frissítés** gombra kattintva leállítja a meglévő webszolgáltatást, és üzembe helyezi az új prediktív kísérletet.

> [!NOTE]
> Ha módosította az eredeti webszolgáltatás konfigurációjának módosításait, például egy új megjelenítendő név vagy leírás beírását, akkor újra meg kell adnia ezeket az értékeket.

A webszolgáltatás frissítésének egyik lehetősége a modell programozott módon történő újratanítása. További információ: [Machine learning Studio (klasszikus) modellek programozott módon történő átképzése](./retrain-machine-learning-model.md).

## <a name="next-steps"></a>Következő lépések

* Az üzembe helyezés módjával kapcsolatos további technikai részletekért lásd: [how a Machine learning Studio (klasszikus) modell egy kísérletből egy működőképes webszolgáltatásba halad](model-progression-experiment-to-web-service.md).

* A modell telepítésre való előkészítésének részletes ismertetését lásd: [a modell előkészítése az üzembe helyezéshez Azure Machine learning Studio (klasszikus)](deploy-a-machine-learning-web-service.md).

* A REST API felhasználására, illetve a webszolgáltatás elérésére számos megoldás áll rendelkezésre. Lásd: [Azure Machine learning Studio (klasszikus) webszolgáltatás felhasználása](consume-web-services.md).

<!-- internal links -->
[Képzési kísérlet létrehozása]: #create-a-training-experiment
[Átalakítás prediktív kísérletre]: #convert-the-training-experiment-to-a-predictive-experiment
[Új webszolgáltatás]: #deploy-it-as-a-new-web-service
[Klasszikus webszolgáltatás]: #deploy-it-as-a-classic-web-service
[új]: #deploy-it-as-a-new-web-service
[classic]: #deploy-the-predictive-experiment-as-a-classic-web-service
[Access]: #access-the-Web-service
[Manage]: #manage-the-Web-service-in-the-azure-management-portal
[Update]: #update-the-Web-service

[webserviceparameters]: web-service-parameters.md
[deploy]: deploy-a-machine-learning-web-service.md
[clean-missing-data]: /azure/machine-learning/studio-module-reference/clean-missing-data
[evaluate-model]: /azure/machine-learning/studio-module-reference/evaluate-model
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset
[import-data]: /azure/machine-learning/studio-module-reference/import-data
[score-model]: /azure/machine-learning/studio-module-reference/score-model
[split]: /azure/machine-learning/studio-module-reference/split-data
[train-model]: /azure/machine-learning/studio-module-reference/train-model
[export-data]: /azure/machine-learning/studio-module-reference/export-data