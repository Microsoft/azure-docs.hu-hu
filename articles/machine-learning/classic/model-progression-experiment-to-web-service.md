---
title: 'ML Studio (klasszikus): hogyan válik a modell webszolgáltatás – Azure'
description: Annak mechanikája, hogy a Azure Machine Learning Studio (klasszikus) modell hogyan halad egy fejlesztési kísérletből egy webszolgáltatásba.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 03/20/2017
ms.openlocfilehash: 4e0f5786047977a319825aae9f3c7b89c0aa118b
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518623"
---
# <a name="how-a-machine-learning-studio-classic-model-progresses-from-an-experiment-to-a-web-service"></a>Hogyan halad egy Machine Learning Studio (klasszikus) modell egy kísérletből a webszolgáltatásba

**a következőkre vonatkozik:** ![ Ez egy pipa, ami azt jelenti, hogy ez a cikk a Machine Learning Studio (klasszikus) elemre vonatkozik. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasszikus) ![ Ez egy X, ami azt jelenti, hogy ez a cikk Azure Machine learningre vonatkozik.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

A Azure Machine Learning Studio (klasszikus) egy interaktív vászon, amely lehetővé teszi egy prediktív elemzési modellt jelölő ***kísérlet*** fejlesztését, futtatását, tesztelését és megismétlését. Számos különböző modul érhető el, amelyek a következőket tehetik:

* Adatok bevitele a kísérletbe
* Az adatkezelés
* Modell betanítása gépi tanulási algoritmusok használatával
* A modell pontozása
* Az eredmények kiértékelése
* Kimeneti végső értékek

Ha elégedett a kísérlettel, telepítheti a t ***klasszikus Azure Machine learning webszolgáltatásként**, vagy egy _ *_új Azure Machine learning webszolgáltatásként_**, hogy a felhasználók el tudják küldeni az új adatait, és megkapják az eredményeket.

Ebben a cikkben áttekintjük, hogy a Machine Learning modell hogyan halad egy fejlesztési kísérletből egy működőképes webszolgáltatásra.

> [!NOTE]
> A gépi tanulási modellek fejlesztésére és üzembe helyezésére más módszerek is használhatók, de ez a cikk a Machine Learning Studio (klasszikus) használatának módjára koncentrál. Ha például azt szeretné megtudni, hogyan hozhat létre egy klasszikus prediktív webszolgáltatást az R-vel, tekintse meg a blogbejegyzés készítése [& az RStudio és a Azure Machine learning Studio használatával történő prediktív Web Apps üzembe helyezését](/archive/blogs/machinelearning/build-deploy-predictive-web-apps-using-rstudio-and-azure-ml)ismertető témakört.
>
>

Habár a Azure Machine Learning Studio (klasszikus) úgy van kialakítva, hogy segítséget nyújtson a *prediktív elemzési modellek* fejlesztéséhez és üzembe helyezéséhez, a Studio (klasszikus) használatával olyan kísérletet fejleszthet, amely nem tartalmaz prediktív elemzési modellt. Előfordulhat például, hogy egy kísérlet csak bevitt adatokat tartalmaz, majd a módosítást végez, majd kiírja az eredményeket. A prediktív elemzési kísérletekhez hasonlóan ezt a nem prediktív kísérletet is üzembe helyezheti webszolgáltatásként, de ez egy egyszerűbb folyamat, mert a kísérlet nem tanít vagy nem talál gépi tanulási modellt. Habár a Studio (klasszikus) nem a szokásos módon használható, a beszélgetésbe belefoglaljuk, hogy a Studio (klasszikus) működése teljes körű magyarázatot nyújtson.

## <a name="developing-and-deploying-a-predictive-web-service"></a>Prediktív webszolgáltatás fejlesztése és üzembe helyezése
Az alábbi szakaszokban egy tipikus megoldás következik be, amelyet a Machine Learning Studio (klasszikus) használatával fejleszt és telepít:

![Üzembe helyezési folyamat](./media/model-progression-experiment-to-web-service/model-stages-from-experiment-to-web-service.png)

*1. ábra – egy tipikus prediktív elemzési modell szakaszai*

### <a name="the-training-experiment"></a>A betanítási kísérlet
A ***betanítási kísérlet*** a webszolgáltatások fejlesztésének kezdeti fázisa Machine learning Studio (klasszikus). A betanítási kísérlet célja, hogy lehetővé teszi a gépi tanulási modellek fejlesztését, tesztelését, megismétlését és végül betanítását. Akár egyszerre több modellt is betaníthat, amikor a legjobb megoldást keresi, de ha végzett a kísérletezéssel, egyetlen betanított modellt is kiválaszthat, és kizárja a többit a kísérletből. A prediktív elemzési kísérlet kialakítására példát a következő témakörben talál: [Azure Machine learning Studio (klasszikus) hitelkockázat-értékelésének prediktív elemzési megoldásának fejlesztése](tutorial-part1-credit-risk.md).

### <a name="the-predictive-experiment"></a>A prediktív kísérlet
Miután betanított modellt a betanítási kísérletben, kattintson a **webszolgáltatás beállítása** elemre, és válassza a **prediktív webszolgáltatás** Machine learning Studio (klasszikus) lehetőséget, hogy elindítsa a betanítási kísérletet egy **_prediktív kísérletre_**. A prediktív kísérlet célja, hogy a betanított modellt használja az új adatmennyiségek kiértékelésére, amelynek célja, hogy végül Azure-webszolgáltatásként működőképes legyen.

Ezt a konverziót a következő lépésekkel végezheti el:

* Átalakíthatja a betanításhoz használt modulok készletét egyetlen modulba, és megtakaríthatja azt betanított modellként
* A pontozáshoz nem kapcsolódó külső modulok kizárása
* Adja meg azokat a bemeneti és kimeneti portokat, amelyeket a végső webszolgáltatás használni fog

Előfordulhat, hogy a prediktív kísérletet webszolgáltatásként való üzembe helyezésre kész állapotba kívánja venni. Ha például azt szeretné, hogy a webszolgáltatás csak az eredmények egy részhalmazát adja eredményül, hozzáadhat egy szűrési modult a kimeneti port előtt.

Ebben az átalakítási folyamatban a betanítási kísérlet nem kerül elvetésre. Ha a folyamat elkészült, két füle van a Studióban (klasszikus): egyet a betanítási kísérlethez, egyet pedig a prediktív kísérlethez. Így módosíthatja a betanítási kísérletet a webszolgáltatás üzembe helyezése előtt, és újraépítheti a prediktív kísérletet. Vagy mentheti a betanítási kísérlet másolatát egy újabb kísérletezés elindításához.

> [!NOTE]
> Ha a **prediktív webszolgáltatás** elemre kattint, elindul egy automatikus folyamat, amellyel a betanítási kísérletet prediktív kísérletre alakíthatja, és ez a legtöbb esetben jól működik. Ha a betanítási kísérlet összetett (például több elérési úttal rendelkezik a betanításhoz), érdemes lehet ezt a konverziót manuálisan elvégezni. További információ: [a modell előkészítése az üzembe helyezéshez Azure Machine learning Studio (klasszikus)](deploy-a-machine-learning-web-service.md).
>
>

### <a name="the-web-service"></a>Webszolgáltatás
Ha meggyőződött arról, hogy a prediktív kísérlet készen áll, a szolgáltatást telepítheti klasszikus webszolgáltatásként vagy egy Azure Resource Manager alapján létrehozott új webszolgáltatásként is. Ahhoz, hogy *klasszikus Machine learning webszolgáltatásként* telepítse a modellt, kattintson a **webszolgáltatás telepítése** lehetőségre, és válassza a **webszolgáltatás telepítése [klasszikus]** lehetőséget működővé tenni. Ha *új Machine learning webszolgáltatásként* kíván üzembe helyezni, kattintson a **webszolgáltatás telepítése** és a **webszolgáltatás telepítése [új]** elemre. A felhasználók most már küldhetnek adattípust a modellnek a webszolgáltatási REST API használatával, és megkapják az eredményeket. További információ: [Az Azure Machine Learning webszolgáltatásainak használata](consume-web-services.md).

## <a name="the-non-typical-case-creating-a-non-predictive-web-service"></a>Nem tipikus eset: nem prediktív webszolgáltatás létrehozása
Ha a kísérlet nem egy prediktív elemzési modellt mutat be, akkor nem kell egyszerre betanítási kísérletet és pontozási kísérletet létrehoznia – csak egy kísérlet van, és webszolgáltatásként is üzembe helyezhető. A Machine Learning Studio (klasszikus) azt észleli, hogy a kísérlet prediktív modellt tartalmaz-e a használt modulok elemzésével.

Miután megismételte a kísérletet, és elégedett vele:

1. Kattintson a **webszolgáltatás beállítása** elemre, és válassza a **webszolgáltatás átképzése** -bemeneti és kimeneti csomópontok automatikus hozzáadása elemet.
2. Kattintson a **Futtatás** gombra.
3. Kattintson a **webszolgáltatás üzembe** helyezése lehetőségre, majd válassza a **webszolgáltatás telepítése [klasszikus]** lehetőséget, vagy **telepítse a webszolgáltatást [új]** attól függően, hogy melyik környezetbe szeretné telepíteni a szolgáltatást.

A webszolgáltatás üzembe helyezése már megtörtént, és egy prediktív webszolgáltatáshoz hasonlóan elérheti és kezelheti.

## <a name="updating-your-web-service"></a>Webszolgáltatás frissítése
Most, hogy üzembe helyezte a kísérletet webszolgáltatásként, mi a teendő, ha frissítenie kell?

Ez attól függ, hogy mit kell frissítenie:

**Módosítani szeretné a bemenetet vagy a kimenetet, vagy módosítani szeretné, hogy a webszolgáltatás hogyan kezelje az adatokat**

Ha nem módosítja a modellt, de csak megváltoztatja, hogy a webszolgáltatás hogyan kezeli az adatkezelést, akkor szerkesztheti a prediktív kísérletet, majd kattintson a webszolgáltatás **telepítése** lehetőségre, és válassza a webszolgáltatás telepítése **[klasszikus]** vagy a **webszolgáltatás telepítése [új]** lehetőséget. A webszolgáltatás leáll, a frissített prediktív kísérlet települ, és a webszolgáltatás újraindul.

Példa: tegyük fel, hogy a prediktív kísérlet a bemeneti adatok teljes sorát adja vissza a várt eredménnyel. Dönthet úgy, hogy azt szeretné, hogy a webszolgáltatás csak az eredményt küldje vissza. Így hozzáadhat egy **Project Columns** modult a prediktív kísérletben, közvetlenül a kimeneti port előtt, hogy az eredménytől eltérő oszlopokat lehessen kizárni. Ha a **webszolgáltatás telepítése** lehetőségre kattint, és kiválasztja a **webszolgáltatás telepítése [klasszikus]** vagy a **webszolgáltatás telepítése [új]** lehetőséget, a webszolgáltatás frissül.

**Új adattal szeretné áttanítani a modellt**

Ha szeretné megőrizni a gépi tanulási modellt, de az új adataival szeretné újratanítani, két lehetőség közül választhat:

1. **A modell újratanítása a webszolgáltatás futása közben** – ha a prediktív webszolgáltatás futása közben szeretné áttanítani a modellt, ezt úgy teheti meg, hogy a betanítási kísérlet során egy pár módosítást hajt végre a **betanítási kísérlet során.** Ennek módjával kapcsolatos útmutatásért olvassa el a [Machine learning modellek programozott újratanítása](./retrain-machine-learning-model.md)című témakört.
2. **Térjen vissza az eredeti betanítási kísérletre, és használjon különböző képzési adatait a modell fejlesztéséhez** – a prediktív kísérlet a webszolgáltatáshoz van csatolva, de a betanítási kísérlet nem kapcsolódik közvetlenül ehhez a módszerhez. Ha módosítja az eredeti betanítási kísérletet, és a **webszolgáltatás beállítása** elemre kattint, a rendszer létrehoz egy *új*     prediktív kísérletet, amely telepítésekor a rendszer létrehoz egy *új* webszolgáltatás-szolgáltatást. Nem csupán az eredeti webszolgáltatás frissítésére szolgál.

   Ha módosítania kell a betanítási kísérletet, nyissa meg, majd kattintson a **Mentés másként** lehetőségre a másoláshoz. Ez érintetlenül hagyja az eredeti betanítási kísérletet, a prediktív kísérletet és a webszolgáltatás-szolgáltatást. Mostantól létrehozhat egy új webszolgáltatást is a módosításaival. Miután telepítette az új webszolgáltatást, eldöntheti, hogy leállítja-e az előző webszolgáltatást, vagy továbbra is fut-e az új webszolgáltatás mellett.

**Egy másik modellt szeretne betanítani**

Ha módosítani kívánja az eredeti prediktív kísérletet, például egy másik gépi tanulási algoritmus kiválasztásával, egy másik betanítási módszer kipróbálásával, majd a modell átképzéséhez a fentebb ismertetett második eljárást kell követnie: Nyissa meg a betanítási kísérletet, kattintson a **Mentés másként** lehetőségre a másoláshoz, majd indítsa el a modell fejlesztésének új útvonalát, hozza létre a prediktív kísérletet és telepítse a webszolgáltatást. Ezzel egy új webszolgáltatás fog létrejönni, amely nem kapcsolódik az eredetihöz – eldöntheti, hogy melyik vagy mindkettőt használja a működés folytatásához.

## <a name="next-steps"></a>Következő lépések
A fejlesztés és kísérletezés folyamatával kapcsolatos további részletekért tekintse meg a következő cikkeket:

* a kísérlet átalakítása – a [modell előkészítése a Azure Machine learning Studio (klasszikus) üzembe helyezéséhez](deploy-a-machine-learning-web-service.md)
* Webszolgáltatás üzembe helyezése – [Azure Machine learning webszolgáltatás üzembe helyezése](deploy-a-machine-learning-web-service.md)
* a modell átképzése – [Machine learning modellek programozott](./retrain-machine-learning-model.md) újratanítása

A teljes folyamatra vonatkozó példákat a következő témakörben talál:

* [Machine learning-oktatóanyag: az első kísérlet létrehozása Azure Machine Learning Studio (klasszikus)](create-experiment.md)
* [Bemutató: prediktív elemzési megoldás létrehozása a hitelkockázat értékeléséhez Azure Machine Learning](tutorial-part1-credit-risk.md)