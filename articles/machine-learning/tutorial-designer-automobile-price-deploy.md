---
title: 'Oktatóanyag: ML modellek üzembe helyezése a tervezővel'
titleSuffix: Azure Machine Learning
description: Hozzon létre egy prediktív elemzési megoldást Azure Machine Learning Designerben. A gépi tanulási modellek betanítása, pontszáma és üzembe helyezése drag-and-drop modulok használatával.
author: likebupt
ms.author: keli19
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 01/15/2021
ms.custom: designer
ms.openlocfilehash: ec563371ab505113117707f56c31f506f7fdf377
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101659527"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-designer"></a>Oktatóanyag: gépi tanulási modell üzembe helyezése a tervezővel


Az [oktatóanyag első részében](tutorial-designer-automobile-price-train-score.md) létrehozott prediktív modell üzembe helyezésével mások számára is lehetővé teheti a használatát. Az első részben betanított egy modellt. Most itt az ideje, hogy felhasználói bevitel alapján készítsen előrejelzéseket. Az oktatóanyag ezen részében a következőket fogja elsajátítani:

> [!div class="checklist"]
> * Valós idejű következtetési folyamat létrehozása.
> * Hozzon létre egy következtetésben lévő fürtöt.
> * A valós idejű végpont üzembe helyezése.
> * Tesztelje a valós idejű végpontot.

## <a name="prerequisites"></a>Előfeltételek

[Az oktatóanyag első részében](tutorial-designer-automobile-price-train-score.md) megismerheti, hogyan végezheti el a gépi tanulási modellek betanítását és kiértékelését a tervezőben.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="create-a-real-time-inference-pipeline"></a>Valós idejű következtetési folyamat létrehozása

A folyamat üzembe helyezéséhez először át kell alakítania a betanítási folyamatot egy valós idejű következtetési folyamatba. Ez a folyamat eltávolítja a betanítási modulokat, és hozzáadja a webszolgáltatás bemeneteit és kimeneteit a kérelmek kezeléséhez.

### <a name="create-a-real-time-inference-pipeline"></a>Valós idejű következtetési folyamat létrehozása

1. A folyamat fölötti vászonnál válassza a **következtetés létrehozása folyamat**  >  **valós idejű következtetési folyamat** lehetőséget.

    :::image type="content" source="./media/tutorial-designer-automobile-price-deploy/tutorial2-create-inference-pipeline.png" alt-text="Képernyőfelvétel a folyamat létrehozása gombról":::

    A folyamatnak most így kell kinéznie: 

   ![Képernyőfelvétel a folyamat várható konfigurációjának megjelenítéséről az üzembe helyezés előkészítése után](./media/tutorial-designer-automobile-price-deploy/real-time-inference-pipeline.png)

    Amikor kiválasztja a **következtetési folyamat létrehozása** lehetőséget, több dolog történik:
    
    * A betanított modellt a modul palettáján **adatkészlet** -modulként tárolja a rendszer. **Az adatkészletek** szakaszban találja.
    * A képzési modulok, például a **betanítási modell** és a **felosztott adategységek** törlődnek.
    * A rendszer visszaadja a mentett betanított modellt a folyamatba.
    * A **webszolgáltatás bemeneti** és **webszolgáltatás-kimeneti** moduljai hozzáadódnak a szolgáltatáshoz. Ezek a modulok azt mutatják be, hogy a felhasználói adatcsatorna hol adja meg a folyamatot

    > [!NOTE]
    > Alapértelmezés szerint a **webszolgáltatás bemenete** ugyanazt az adatsémát fogja várni, mint a prediktív folyamat létrehozásához használt betanítási adatok. Ebben az esetben a séma tartalmazza az árat. Az előrejelzés során azonban az ár nem használható faktorként.
    >

1. Válassza a **Submit (Küldés**) lehetőséget, és használja ugyanazt a számítási célt és kísérletet, amelyet az első részben használt.

    Ha ez az első futtatás, akár 20 percet is igénybe vehet, amíg a folyamat befejezi a futását. Az alapértelmezett számítási beállításokhoz a csomópont minimális mérete 0, ami azt jelenti, hogy a tervezőnek üresjárat után le kell foglalnia az erőforrásokat. Az ismétlődő folyamat-futtatások kevesebb időt vesznek igénybe, mivel a számítási erőforrások már le vannak foglalva. Emellett a tervező az egyes modulok gyorsítótárazott eredményeit használja a hatékonyság növelése érdekében.

1. Válassza az **Üzembe helyezés** lehetőséget.

## <a name="create-an-inferencing-cluster"></a>Következtetési fürt létrehozása

A megjelenő párbeszédpanelen bármelyik meglévő Azure Kubernetes Service-(ak-) fürtből kiválaszthatja a modell üzembe helyezését. Ha nem rendelkezik AK-fürttel, a következő lépésekkel hozhat létre egyet.

1. A **számítási** lapon megjelenő párbeszédpanelen kattintson a **számítás** elemre.

1. A navigációs menüszalagon válassza a **következtetési fürtök**  >  **+ új** lehetőséget.

    ![Az új következtetési fürt ablaktábla beszerzését bemutató képernyőkép](./media/tutorial-designer-automobile-price-deploy/new-inference-cluster.png)
   
1. A következtetési fürt ablaktáblán konfigurálja az új Kubernetes szolgáltatást.

1. Írja be a következőt: *Kaba-számítás* a **számítási névhez**.
    
1. Válasszon egy közeli régiót, amely elérhető a **régió** számára.

1. Válassza a **Létrehozás** lehetőséget.

    > [!NOTE]
    > Egy új AK-szolgáltatás létrehozása körülbelül 15 percet vesz igénybe. A kiépítési állapotot megtekintheti a **következtetési fürtök** oldalon.
    >

## <a name="deploy-the-real-time-endpoint"></a>A valós idejű végpont üzembe helyezése

Miután az AK-szolgáltatás befejezte a kiépítést, térjen vissza a valós idejű következtetési folyamatra az üzembe helyezés befejezéséhez.

1. Válassza a vászon fölötti **üzembe helyezés** lehetőséget.

1. Válassza az **új valós idejű végpont telepítése** lehetőséget. 

1. Válassza ki a létrehozott AK-fürtöt.

    :::image type="content" source="./media/tutorial-designer-automobile-price-deploy/setup-endpoint.png" alt-text="Az új valós idejű végpont beállítását bemutató képernyőkép":::

    A valós idejű végpont **speciális** beállítása is megváltoztatható.
    
    |Speciális beállítás|Leírás|
    |---|---|
    |Application Insights diagnosztika és adatgyűjtés engedélyezése| Azt határozza meg, hogy engedélyezi-e az Azure Application Insights az adatok gyűjtését az üzembe helyezett végpontokról. </br> Alapértelmezés szerint: false |
    |Pontozási időkorlát| A webszolgáltatásra irányuló pontozási hívások kényszerített időkorlátja ezredmásodpercben.</br>Alapértelmezés szerint: 60000|
    |Automatikus méretezés engedélyezve|   Azt határozza meg, hogy engedélyezi-e az automatikus skálázást a webszolgáltatáshoz.</br>Alapértelmezés szerint: true|
    |Replikák minimális száma| A webszolgáltatás automatikus skálázásakor használandó tárolók minimális száma.</br>Alapértelmezés szerint: 1|
    |Replikák maximális száma| A webszolgáltatás automatikus skálázásakor használandó tárolók maximális száma.</br> Alapértelmezés szerint: 10|
    |Cél kihasználtsága|A cél kihasználtsága (az 100-as százalékban kifejezve), amelyet az autoskálázásnak meg kell próbálnia fenntartani a webszolgáltatás számára.</br> Alapértelmezés szerint: 70|
    |Frissítési időszak|Az autoskálázás milyen gyakran próbálkozik a webszolgáltatás skálázásával.</br> Alapértelmezés szerint: 1|
    |CPU-foglalási kapacitás|A webszolgáltatás számára lefoglalható CPU-magok száma.</br> Alapértelmezés szerint: 0,1|
    |Memória foglalási kapacitása|A webszolgáltatás számára lefoglalható memória mennyisége (GB-ban).</br> Alapértelmezés szerint: 0,5|
        

1. Válassza az **Üzembe helyezés** lehetőséget. 

    Az üzembe helyezés befejeződése után a vászon fölötti sikeres értesítés jelenik meg. Néhány percet is igénybe vehet.

> [!TIP]
> Az Azure Container **instance** (ACI) szolgáltatásban is üzembe helyezhető, ha a valós idejű végpont-beállítás mezőben a **számítási típushoz** az **Azure Container instance** elemet választja.
> Az Azure Container instance tesztelésre vagy fejlesztésre szolgál. Az ACI-t alacsony léptékű CPU-alapú számítási feladatokhoz használhatja, amelyek kevesebb mint 48 GB RAM memóriát igényelnek.

## <a name="test-the-real-time-endpoint"></a>A valós idejű végpont tesztelése

Az üzembe helyezés befejeződése után megtekintheti a valós idejű végpontot a **végpontok** lapon.

1. A **végpontok** lapon válassza ki a telepített végpontot.

    A **részletek** lapon további információkat tekinthet meg, például a REST URI-t, a hencegés definícióját, az állapotot és a címkéket.

    A felhasználás **lapon** megtalálhatja a minta felhasználási kódját, a biztonsági kulcsokat, és beállíthatja a hitelesítési módszereket.

    A **központi telepítési naplók** lapon megtekintheti a valós idejű végpont részletes üzembe helyezési naplóit.

1. A végpont teszteléséhez lépjen a test ( **teszt** ) lapra. Itt megadhatja a tesztelési adatokat, és kiválaszthatja a végpont kimenetének ellenőrzése **tesztet** .

További információ a webszolgáltatás használatáról: a [webszolgáltatásként üzembe helyezett modell felhasználása](how-to-consume-web-service.md)

## <a name="limitations"></a>Korlátozások

Ha módosításokat hajt végre a betanítási folyamat során, akkor újra el kell küldenie a betanítási folyamatot, **frissítenie** kell a következtetési folyamatot, és újra le kell futtatnia a következtetési folyamatot.

Vegye figyelembe, hogy csak a betanított modellek lesznek frissítve a következtetési folyamatban, míg az adatátalakítás nem lesz frissítve.

A frissített átalakítás a következtetési folyamatban való használatához regisztrálnia kell az átalakítási modul átalakítási kimenetét adatkészletként.

![Az átalakítási adatkészlet regisztrálását bemutató képernyőkép](./media/tutorial-designer-automobile-price-deploy/register-transformation-dataset.png)

Ezután manuálisan cserélje le a **TD-** modult a következtetési folyamatba a regisztrált adatkészlettel.

![Az átalakítási modul lecserélését bemutató képernyőkép](./media/tutorial-designer-automobile-price-deploy/replace-td-module.png)

Ezután elküldheti a következtetési folyamatot a frissített modellel és átalakítással, és üzembe helyezheti.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megtanulta, hogyan hozhat létre, helyezhet üzembe és használhat fel gépi tanulási modellt a tervezőben. Ha többet szeretne megtudni a tervező használatáról, tekintse meg az alábbi hivatkozásokat:

+ [Tervezői minták](samples-designer.md): megtudhatja, hogyan használhatja a tervezőt más típusú problémák megoldására.
+ [Azure Machine learning Studio használata Azure-beli virtuális hálózaton](how-to-enable-studio-virtual-network.md).
