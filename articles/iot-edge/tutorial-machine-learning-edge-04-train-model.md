---
title: 'Oktatóanyag: modell betanítása és üzembe helyezése Machine Learning Azure IoT Edge'
description: Ebben az oktatóanyagban a gépi tanulási modellt Azure Machine Learning használatával fogja betanítani, majd becsomagolni a modellt Azure IoT Edge modulként üzembe helyezhető tároló-képként.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/24/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 16d5fe90be71f39d448e4c1ce10c0373f6bfc86c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103463102"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>Oktatóanyag: Azure Machine Learning modell betanítása és üzembe helyezése

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Ebben a cikkben a következő feladatokat hajtjuk végre:

* A Machine learning-modellek betanításához használjon Azure Machine Learning Studio.
* Csomagolja ki a betanított modellt tároló képként.
* Telepítse a tároló lemezképét Azure IoT Edge modulként.

Machine Learning Studio a gépi tanulási modellek kipróbálásához, betanításához és üzembe helyezéséhez használt alapvető blokk.

A cikkben ismertetett lépéseket általában az adatszakértők végzik.

Az oktatóanyag ezen szakaszában megismerheti a következőket:

> [!div class="checklist"]
> * Hozzon létre Jupyter jegyzetfüzeteket egy Azure Machine Learning munkaterületen a Machine learning-modell betanításához.
> * Tárolóba helyezése a betanított gépi tanulási modellt.
> * Hozzon létre egy IoT Edge modult a tároló Machine learning modellből.

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk egy sorozat részét képezi a Machine Learning IoT Edge-on való használatáról szóló oktatóanyaghoz. A sorozat minden cikke az előző cikkben található munkára épül. Ha ezt a cikket közvetlenül megérkezett, tekintse meg az [első cikket](tutorial-machine-learning-edge-01-intro.md) a sorozatban.

## <a name="set-up-azure-machine-learning"></a>Azure Machine Learning beállítása

A két Jupyter jegyzetfüzet és a támogató fájlok üzemeltetéséhez Machine Learning Studio használjuk. Itt egy Machine Learning projektet hozunk létre és konfigurálunk. Ha még nem használta a Jupyter vagy a Machine Learning Studio, két bevezető dokumentum van:

* **Jupyter notebook**: [Jupyter notebookok használata a Visual Studio Code-ban](https://code.visualstudio.com/docs/python/jupyter-support)
* **Azure Machine learning**: [a Jupyter-jegyzetfüzetek Azure Machine Learningának első lépései](../machine-learning/tutorial-1st-experiment-sdk-setup.md)

> [!NOTE]
> A szolgáltatás beállítása után a Machine Learning bármely gépről elérhető. A telepítés során a fejlesztési virtuális gépet kell használnia, amely rendelkezik az összes szükséges fájllal.

### <a name="install-azure-machine-learning-visual-studio-code-extension"></a>A Visual Studio Code-bővítmény telepítése Azure Machine Learning

A fejlesztői virtuális gépen a Visual Studio Code-nak telepítve kell lennie a bővítménynek. Ha egy másik példányon fut, telepítse újra a bővítményt a [Visual Studio Code bővítmény beállítása](../machine-learning/tutorial-setup-vscode-extension.md)című témakörben leírtak szerint.

### <a name="create-an-azure-machine-learning-account"></a>Azure Machine Learning fiók létrehozása

Az erőforrások kiépítéséhez és az Azure-beli számítási feladatok futtatásához jelentkezzen be az Azure-fiókja hitelesítő adataival.

1. A Visual Studio Code-ban nyissa meg a parancssort a menüsávban található parancssáv **megtekintése** lehetőség kiválasztásával  >   .

1. `Azure: Sign In`A bejelentkezési folyamat elindításához írja be a parancsot a parancs palettáján. A bejelentkezés befejezéséhez kövesse az utasításokat.

1. Hozzon létre egy Machine Learning számítási példányt a munkaterhelés futtatásához. A parancs palettáján adja meg a parancsot `Azure ML: Create Compute` .
1. Válassza ki Azure-előfizetését.
1. Válassza az **+ új Azure-ml-munkaterület létrehozása** lehetőséget, és írja be a **turbofandemo** nevet.
1. Válassza ki azt az erőforráscsoportot, amelyet ehhez a bemutatóhoz használt.
1. A munkaterület létrehozásának előrehaladását a Visual Studio Code ablakának jobb alsó sarkában tekintheti meg: **Munkaterület létrehozása: turobofandemo**. Ez a lépés egy-két percet is igénybe vehet.
1. Várjon, amíg a munkaterület létrehozása sikeresen megtörtént. Azt mondják, hogy az **Azure ml-munkaterület turbofandemo létre lett hozva**.

### <a name="upload-jupyter-notebook-files"></a>Jupyter Notebook fájlok feltöltése

A mintaként szolgáló jegyzetfüzet-fájlokat egy új Machine Learning munkaterületre fogjuk feltölteni.

1. Nyissa meg a ml.azure.com, és jelentkezzen be.
1. Válassza ki a Microsoft-címtárat, az Azure-előfizetést és az újonnan létrehozott Machine Learning munkaterületet.

    :::image type="content" source="media/tutorial-machine-learning-edge-04-train-model/select-studio-workspace.png" alt-text="A Azure Machine Learning munkaterület kijelölését bemutató képernyőkép." :::

1. Miután bejelentkezett a Machine Learning munkaterületre, nyissa meg a **jegyzetfüzetek** szakaszt a bal oldali menü használatával.
1. Válassza a **saját fájlok** fület.

1. Válassza a **feltöltés** lehetőséget (a felfelé mutató nyíl ikon).

1. Nyissa meg a **C:\source\IoTEdgeAndMlSample\AzureNotebooks**. Jelölje ki a listában szereplő összes fájlt, majd kattintson a **Megnyitás** gombra.

1. Jelölje be a **Megbízom a fájlok tartalmában** jelölőnégyzetet.

1. A feltöltés megkezdéséhez válassza a **feltöltés** lehetőséget. Ezután a folyamat befejezése után válassza a **kész** lehetőséget.

### <a name="jupyter-notebook-files"></a>Jupyter Notebook fájlok

Tekintse át a Machine Learning munkaterületre feltöltött fájlokat. Az oktatóanyag ezen részében szereplő tevékenységek két jegyzetfüzet-fájlra terjednek ki, amelyek néhány kiegészítő fájlt használnak.

* **01 – turbofan \_ regresszió. ipynb**: Ez a jegyzetfüzet a Machine learning munkaterületet használja a Machine learning-kísérletek létrehozásához és futtatásához. A jegyzetfüzet széles körben a következő lépéseket hajtja végre:

  1. Az eszköz által létrehozott Azure Storage-fiókból származó adatok letöltése.
  1. Felderíti és előkészíti az adatgyűjtést, majd a segítségével betanítja az osztályozó modellt.
  1. Kiértékeli a modellt a kísérletből egy tesztelési adatkészlet használatával (teszt \_FD003.txt).
  1. Közzéteszi a legjobb osztályozó modellt a Machine Learning munkaterületen.

* **02 – turbofan \_ üzembe helyezési \_ modell. ipynb**: Ez a jegyzetfüzet az előző jegyzetfüzetben létrehozott modellt hozza létre, és felhasználja egy IoT Edge eszközre való üzembe helyezésre kész tároló lemezkép létrehozásához. A jegyzetfüzet a következő lépéseket hajtja végre:

  1. Létrehoz egy pontozási parancsfájlt a modellhez.
  1. Létrehoz egy tároló rendszerképet a Machine Learning munkaterületen mentett osztályozó modell használatával.
  1. Üzembe helyezi a rendszerképet webszolgáltatásként Azure Container Instanceson.
  1. A webszolgáltatás használatával ellenőrzi a modellt, és a rendszerkép a várt módon működik. Az érvényesített lemezkép a IoT Edge eszközre lesz telepítve az oktatóanyag [egyéni IoT Edge modulok létrehozása és telepítése](tutorial-machine-learning-edge-06-custom-modules.md) részében.

* **Test \_FD003.txt**: Ez a fájl tartalmazza azokat az adatkészleteket, amelyeket a rendszer a tesztelési csoportként fog használni, amikor érvényesítjük a betanított osztályozó Úgy döntöttünk, hogy a tesztet az eredeti pályázathoz megadott módon használjuk, az egyszerűség kedvéért.
* **RUL \_FD003.txt**: Ez a fájl a tesztelésiFD003.txt fájlban lévő egyes eszközök utolsó ciklusának hátralévő hasznos élettartamát (RUL) tartalmazza \_ . Az \\ \\ \\ \\ információk részletes ismertetését lásd a readme.txt és a kár propagálása Modeling.pdf fájlok a C: Source IoTEdgeAndMlSample-Turbofan című részben.
* **Utils.py**: Ez a fájl a Python segédprogram azon funkcióit tartalmazza, amelyekkel dolgozhat az adatokkal. Az első jegyzetfüzet a függvények részletes magyarázatát tartalmazza.
* **Readme.MD**: Ez a readme-fájl ismerteti a jegyzetfüzetek használatát.

## <a name="run-the-jupyter-notebooks"></a>A Jupyter-jegyzetfüzetek futtatása

Most, hogy létrehozta a munkaterületet, futtathatja a jegyzetfüzeteket.

1. A **saját fájlok** lapon válassza a **01-turbofan \_ regresszió. ipynb** elemet.

    :::image type="content" source="media/tutorial-machine-learning-edge-04-train-model/select-turbofan-notebook.png" alt-text="Képernyőkép, amely az első futtatandó jegyzetfüzet kiválasztását mutatja.":::

1. Ha a jegyzetfüzet **nem megbízhatóként** jelenik meg, válassza a **nem megbízható** widgetet a jegyzetfüzet jobb felső sarkában. Amikor megjelenik a párbeszédpanel, válassza a **megbízhatóság** lehetőséget.

1. A legjobb eredmény érdekében olvassa el az egyes cellák dokumentációját, és futtassa azt egyenként. Kattintson a **Futtatás** elemre az eszköztáron. Később, célszerű több cella futtatására is. A frissítés és az elavultság figyelmeztetéseit figyelmen kívül hagyhatja.

    Amikor egy cella fut, a szögletes zárójelek ([]) közötti csillagot jeleníti meg \* . Ha a cella művelete befejeződött, a csillag helyére egy szám és egy kapcsolódó kimenet is megjelenik. A jegyzetfüzet cellái szekvenciálisan épülnek fel, és egyszerre csak egy cella futhat.

    A **cella** menüből is használhatja a futtatási beállításokat. Válassza a **CTRL + ENTER** billentyűkombinációt a cella futtatásához, majd a **SHIFT + ENTER** billentyűkombinációval futtasson egy cellát, és folytassa a következő cellával.

    > [!TIP]
    > A konzisztens cella-műveletek esetében ne futtassa ugyanazt a jegyzetfüzetet a böngészőben több lapon.

1. Adja meg az Azure-előfizetés, a beállítások és az erőforrások értékeit a **globális tulajdonságok megadása** című részben szereplő cellában. Ezután futtassa a cellát.

    ![A jegyzetfüzetben a globális tulajdonságok beállítását bemutató képernyőkép.](media/tutorial-machine-learning-edge-04-train-model/set-global-properties.png)

1. Az előző cellában, a **munkaterület részletei** között, a Futtatás után keresse meg a hivatkozást, amely arra utasítja Önt, hogy jelentkezzen be a hitelesítésbe.

    ![Képernyőkép, amely a bejelentkezési kérést jeleníti meg az eszköz hitelesítéséhez.](media/tutorial-machine-learning-edge-04-train-model/sign-in-prompt.png)

    Nyissa meg a hivatkozást, és írja be a megadott kódot. Ez a bejelentkezési eljárás hitelesíti a Jupyter-jegyzetfüzetet az Azure-erőforrások eléréséhez az Microsoft Azure platformfüggetlen parancssori felületen keresztül.

    ![Képernyőkép, amely az alkalmazás hitelesítését mutatja az eszköz jóváhagyásakor.](media/tutorial-machine-learning-edge-04-train-model/cross-platform-cli.png)

1. Az **eredményeket** tartalmazó cellában másolja ki az értéket a FUTTATÁSi azonosítóból, és illessze be a futtatási azonosítóhoz **az alábbi cellában található futtatási** azonosítóra.

   ![A futtatási azonosító cellák közötti másolását bemutató képernyőkép.](media/tutorial-machine-learning-edge-04-train-model/automl-id.png)

1. Futtassa a többi cellát a jegyzetfüzetben.

1. Mentse a jegyzetfüzetet, és térjen vissza a projekt lapra.

1. Nyissa meg a **02 – turbofan \_ üzembe helyezés \_ Model. ipynb**, és futtassa az egyes cellákat. Be kell jelentkeznie a hitelesítéshez a **Konfigurálás munkaterületet** követő cellában.

1. Mentse a jegyzetfüzetet, és térjen vissza a projekt lapra.

### <a name="verify-success"></a>Sikeres ellenőrzés

Annak ellenőrzéséhez, hogy a jegyzetfüzetek sikeresen befejeződtek-e, ellenőrizze, hogy néhány elem létrejött-e.

1. A Machine Learning jegyzetfüzetek **saját fájlok** lapján válassza a **frissítés** lehetőséget.

1. Ellenőrizze, hogy a következő fájlok lettek-e létrehozva.

    | Fájl | Description |
    | --- | --- |
    | ./aml_config/.azureml/config.jsbekapcsolva | A Machine Learning munkaterület létrehozásához használt konfigurációs fájl. |
    | ./aml_config/model_config.jsbekapcsolva | Konfigurációs fájl, amelyre telepíteni kell a modellt az Azure-beli **turbofanDemo** Machine learning munkaterületen. |
    | myenv. YML| Információt nyújt az üzembe helyezett Machine Learning modell függőségeiről.|

1. Ellenőrizze, hogy létrejöttek-e az alábbi Azure-erőforrások. Néhány erőforrás neve véletlenszerű karakterekkel van hozzáfűzve.

    | Azure-erőforrás | Name |
    | --- | --- |
    | Azure Machine Learning-munkaterület | turborfanDemo |
    | Azure Container Registry | turbofandemoxxxxxxxx |
    | Application Insights | turbofaninsightxxxxxxxx |
    | Azure Key Vault | turbofankeyvaultbxxxxxxxx |
    | Azure Storage | turbofanstoragexxxxxxxxx |

### <a name="debugging"></a>Hibakeresés

A jegyzetfüzetbe beillesztheti a hibakereséshez használható Python-utasításokat, például a `print()` parancsot az értékek megjelenítéséhez. Ha a nem definiált változókat vagy objektumokat látja, futtassa azokat a cellákat, amelyeken először a deklarált vagy a példányt létrehozták.

Előfordulhat, hogy törölnie kell a korábban létrehozott fájlokat és az Azure-erőforrásokat, ha újra meg kell ismételni a jegyzetfüzeteket.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ez az oktatóanyag egy olyan készlet része, amelyben az egyes cikkek az előzőekben végzett munkára épülnek. Várjon, amíg az összes erőforrást törölni szeretné, amíg el nem végzi a végleges oktatóanyagot.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben két, Machine Learning Studio-ban futó Jupyter-jegyzetfüzetet használtunk a turbofan-eszközökről a következő adatok használatára:

- RUL osztályozó betanítása.
- Az osztályozó mentése modellként.
- Hozzon létre egy tároló-rendszerképet.
- Webszolgáltatásként üzembe helyezheti és tesztelheti a rendszerképet.

IoT Edge-eszköz létrehozásához folytassa a következő cikkel.

> [!div class="nextstepaction"]
> [IoT Edge-eszköz konfigurálása](tutorial-machine-learning-edge-05-configure-edge-device.md)