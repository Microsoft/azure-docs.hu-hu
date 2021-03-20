---
title: A Python és a TensorFlow használata az Azure-beli gépi tanuláshoz
description: A Python, a TensorFlow és a Azure Functions gépi tanulási modell használatával a képek besorolása a tartalom alapján.
author: anthonychu
ms.topic: tutorial
ms.date: 01/15/2020
ms.author: antchu
ms.custom: mvc, devx-track-python, devx-track-azurepowershell
ms.openlocfilehash: ca9ce27583168dfee1a597fce559afad38a3a8c7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95994604"
---
# <a name="tutorial-apply-machine-learning-models-in-azure-functions-with-python-and-tensorflow"></a>Oktatóanyag: gépi tanulási modellek alkalmazása Azure Functions Python és TensorFlow

Ebből a cikkből megtudhatja, hogyan használható a Python, a TensorFlow és a Azure Functions gépi tanulási modellel a rendszerképeknek a tartalom alapján történő besorolásához. Mivel minden helyileg működik, és nem hoz létre Azure-erőforrásokat a felhőben, az oktatóanyag elvégzéséhez nem kell fizetnie.

> [!div class="checklist"]
> * Helyi környezet inicializálása a Python-Azure Functions fejlesztéséhez.
> * Egyéni TensorFlow Machine learning-modell importálása egy Function alkalmazásba.
> * Hozzon létre egy kiszolgáló nélküli HTTP API-t, amely egy kutyát vagy macskát tartalmazó képet osztályoz.
> * Az API használata webalkalmazásból.

## <a name="prerequisites"></a>Előfeltételek 

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Python-3.7.4](https://www.python.org/downloads/release/python-374/). (A Python 3.7.4 és a Python 3.6. x ellenőrzése a Azure Functions; A Python 3,8-es és újabb verziói még nem támogatottak.)
- A [Azure functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools)
- Kódszerkesztő, például [Visual Studio Code](https://code.visualstudio.com/)

### <a name="prerequisite-check"></a>Előfeltételek ellenőrzése

1. A terminál vagy a parancssorablakban futtassa a parancsot az `func --version` Azure functions Core Tools 2.7.1846 vagy újabb verziójának megadásához.
1. Futtassa `python --version` (Linux/MacOS) vagy `py --version` (Windows) a Python-verzió jelentéseinek 3.7. x-ben való vizsgálatához.

## <a name="clone-the-tutorial-repository"></a>Az oktatóanyag-adattár klónozása

1. Egy terminál vagy parancssori ablakban a következő adattár klónozásával a git használatával:

    ```
    git clone https://github.com/Azure-Samples/functions-python-tensorflow-tutorial.git
    ```

1. Navigáljon a mappához, és vizsgálja meg a tartalmát.

    ```
    cd functions-python-tensorflow-tutorial
    ```

    - a *Start* a munkahelyi mappa az oktatóanyaghoz.
    - a *Befejezés* a hivatkozás végső eredménye és teljes megvalósítása.
    - az *erőforrások* a Machine learning-modellt és a segítő könyvtárakat tartalmazzák.
    - a *frontend* egy olyan webhely, amely meghívja a Function alkalmazást.
    
## <a name="create-and-activate-a-python-virtual-environment"></a>Python virtuális környezet létrehozása és aktiválása

Navigáljon a *Start* mappára, és futtassa a következő parancsokat a nevű virtuális környezet létrehozásához és aktiválásához `.venv` . Ügyeljen arra, hogy a Azure Functions által támogatott Python 3,7-et használja.


# <a name="bash"></a>[bash](#tab/bash)

```bash
cd start
```

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Ha a Python nem telepítette a venv csomagot a Linux-disztribúcióban, futtassa a következő parancsot:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
cd start
```

```powershell
py -3.7 -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
cd start
```

```cmd
py -3.7 -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

Az összes további parancsot futtatja ebben az aktivált virtuális környezetben. (A virtuális környezetből való kilépéshez futtassa a parancsot `deactivate` .)


## <a name="create-a-local-functions-project"></a>Helyi functions-projekt létrehozása

Azure Functions egy függvény-projekt egy vagy több olyan egyedi függvény tárolója, amely mindegyik reagál egy adott triggerre. Egy projekt összes funkciója ugyanazokat a helyi és üzemeltetési konfigurációkat használja. Ebben a szakaszban egy olyan Function-projektet hoz létre, amely egy nevű és egy HTTP-végpontot biztosító kiosztási függvényt tartalmaz `classify` . Egy későbbi szakaszban további speciális kódokat adhat hozzá.

1. A *Start* mappában használja a Azure functions Core Tools egy Python-függvény alkalmazásának inicializálásához:

    ```
    func init --worker-runtime python
    ```

    Az inicializálást követően a *Start* mappa különböző fájlokat tartalmaz a projekthez, beleértve a [local.settings.json](functions-run-local.md#local-settings-file) és a [host.js](functions-host-json.md)nevű konfigurációk fájljait. Mivel a *local.settings.json* az Azure-ból letöltött titkos kódok is lehetnek, a fájl a *. gitignore* fájlban alapértelmezés szerint ki van zárva a forrás-vezérlőelemből.

    > [!TIP]
    > Mivel a Function projekt egy adott futtatókörnyezethez van kötve, a projekt összes funkcióját ugyanazzal a nyelvvel kell írni.

1. Adjon hozzá egy függvényt a projekthez a következő parancs használatával, ahol az `--name` argumentum a függvény egyedi neve, és az `--template` argumentum megadja a függvény triggerét. `func new` hozzon létre egy olyan almappát, amely megfelel a projekt választott nyelvének, valamint egy *function.js* nevű konfigurációs fájlnak.

    ```
    func new --name classify --template "HTTP trigger"
    ```

    Ez a parancs létrehoz egy mappát, amely megfelel a függvény nevének, *besorolása*. Ebben a mappában két fájl található: az *\_ \_ init \_ \_ .* a (z), amely tartalmazza a függvény kódját, és *function.json*, amely leírja a függvény triggerét, valamint a bemeneti és kimeneti kötéseit. A fájlok tartalmával kapcsolatos részletekért lásd: [a fájl tartalmának vizsgálata](./create-first-function-cli-python.md#optional-examine-the-file-contents) a Python rövid útmutatójában.


## <a name="run-the-function-locally"></a>A függvény helyi futtatása

1. Indítsa el a függvényt úgy, hogy elindítja a helyi Azure Functions futtatókörnyezet-gazdagépet a *Start* mappában:

    ```
    func start
    ```
    
1. Ha megjelenik a `classify` végpont a kimenetben, keresse meg az URL-címet ```http://localhost:7071/api/classify?name=Azure``` . A "Hello Azure!" üzenet meg kell jelennie a kimenetben.

1.  - A gazdagép leállításához használja a CTRL **C** billentyűt.


## <a name="import-the-tensorflow-model-and-add-helper-code"></a>A TensorFlow-modell importálása és a segítő kód hozzáadása

Ha módosítani szeretné a `classify` függvényt, hogy az a tartalma alapján osztályozza a rendszerképet, egy előre összeállított TensorFlow-modellt kell használnia, amely az Azure Custom Vision Service használatával lett betanítva és exportálva. Az a modell, amely a korábban klónozott minta *erőforrások* mappájában található, osztályozza a rendszerképet attól függően, hogy kutyát vagy macskát tartalmaz-e. Ezután hozzáadhat egy segítő kódot és függőségeket a projekthez.

Ha saját modellt kíván létrehozni a Custom Vision Service ingyenes szintjével, kövesse a [minta projekt adattárának](https://github.com/Azure-Samples/functions-python-tensorflow-tutorial/blob/master/train-custom-vision-model.md)utasításait.

> [!TIP]
> Ha a TensorFlow-modellt a Function alkalmazástól függetlenül szeretné üzemeltetni, akkor ehelyett a modellt tartalmazó fájlmegosztást csatlakoztathatja a Linux-függvény alkalmazásához. További információ: [fájlmegosztás csatlakoztatása egy Python-függvényhez az Azure CLI használatával](./scripts/functions-cli-mount-files-storage-linux.md).

1. A *Start* mappában futtassa a következő parancsot a modell fájljainak a *besorolási* mappába való másolásához. Ügyeljen arra, hogy szerepeljen `\*` a parancsban. 

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    cp ../resources/model/* classify
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    copy ..\resources\model\* classify
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    copy ..\resources\model\* classify
    ```
    
    ---
    
1. Ellenőrizze, hogy a *besorolási* mappa tartalmazza-e a *Model. pb* és a *labels.txt* nevű fájlokat. Ha nem, ellenőrizze, hogy a parancsot futtatta-e a *Start* mappában.

1. A *Start* mappában futtassa a következő parancsot egy segítő kóddal rendelkező fájl másolásához a *besorolási* mappába:

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    cp ../resources/predict.py classify
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    copy ..\resources\predict.py classify
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    copy ..\resources\predict.py classify
    ```
    
    ---

1. Győződjön meg arról, hogy a *besorolási* mappa már tartalmaz egy *Predict.py* nevű fájlt.

1. Nyisson meg egy szövegszerkesztőben a *Start/requirements.txt* szöveget, és adja hozzá a segítő kód által igényelt következő függőségeket:

    ```txt
    tensorflow==1.14
    Pillow
    requests
    ```
    
1. *requirements.txt* mentése.

1. A függőségek telepítéséhez futtassa a következő parancsot a *Start* mappában. A telepítés néhány percet is igénybe vehet, amely alatt a következő szakaszban folytathatja a függvény módosítását.

    ```
    pip install --no-cache-dir -r requirements.txt
    ```
    
    Windows rendszeren a következő hibaüzenet jelenhet meg: "nem sikerült telepíteni a csomagokat egy EnvironmentError miatt: [errno 2] nincs ilyen fájl vagy könyvtár:", majd egy olyan fájlhoz, mint például *sharded_mutable_dense_hashtable. CPython-37. pyc*. Ez a hiba általában azért fordul elő, mert a mappa elérési útjának mélysége túl hosszú lesz. Ebben az esetben állítsa a beállításkulcsot a `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\FileSystem@LongPathsEnabled` `1` hosszú elérési utak engedélyezéséhez. Másik lehetőségként győződjön meg arról, hogy a Python-tolmács hol van telepítve. Ha a hely hosszú elérési úttal rendelkezik, próbálja meg újratelepíteni egy rövidebb elérési úttal rendelkező mappába.

> [!TIP]
> Ha a *Predict.py* az első előrejelzését kéri, a nevű függvény `_initialize` betölti a TensorFlow modellt a lemezről, és gyorsítótárazza a globális változókban. Ez a gyorsítótárazás felgyorsítja a későbbi előrejelzéseket. A globális változók használatával kapcsolatos további információkért tekintse meg a [Azure functions Python fejlesztői útmutatóját](functions-reference-python.md#global-variables).

## <a name="update-the-function-to-run-predictions"></a>A függvény frissítése az előrejelzések futtatásához

1. Nyissa meg a *besorolás/ \_ \_ init \_ \_ .* másolt egy szövegszerkesztőben, és adja hozzá a következő sorokat a meglévő utasítások után a `import` szabványos JSON-könyvtár és a *prediktív* segítők importálásához:

    :::code language="python" source="~/functions-python-tensorflow-tutorial/end/classify/__init__.py" range="1-6" highlight="5-6":::

1. Cserélje le a függvény teljes tartalmát a `main` következő kódra:

    :::code language="python" source="~/functions-python-tensorflow-tutorial/end/classify/__init__.py" range="8-19":::

    Ez a függvény egy nevű lekérdezési karakterlánc-paraméterben fogadja a rendszerkép URL-címét `img` . Ezután meghívja a `predict_image_from_url` segítő könyvtárát, hogy letöltse és osztályozza a rendszerképet a TensorFlow-modell használatával. A függvény ezután egy HTTP-választ ad vissza az eredményekkel. 

    > [!IMPORTANT]
    > Mivel ezt a HTTP-végpontot egy másik tartományban található weblap hívja meg, a válasz tartalmaz egy `Access-Control-Allow-Origin` fejlécet, amely megfelel a böngésző eltérő eredetű erőforrás-megosztási (CORS) követelményeinek.
    >
    > Éles alkalmazásban váltson `*` a weblap speciális forrására a további biztonság érdekében.

1. Mentse a módosításokat, és feltételezve, hogy a függőségek telepítése befejeződött, indítsa el újra a helyi függvény gazdagépét a következővel: `func start` . Győződjön meg arról, hogy a gazdagépet a *Start* mappában futtatja, és a virtuális környezet aktiválva van. Ellenkező esetben a gazdagép elindul, de hibaüzeneteket fog látni a függvény meghívásakor.

    ```
    func start
    ```

1. Egy böngészőben nyissa meg a következő URL-címet, hogy meghívja a függvényt egy Cat-rendszerkép URL-címével, és ellenőrizze, hogy a visszaadott JSON a képet macskaként osztályozza-e.

    ```
    http://localhost:7071/api/classify?img=https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png
    ```
    
1. Tartsa a gazdagépet, mert a következő lépésben használja. 

### <a name="run-the-local-web-app-front-end-to-test-the-function"></a>A függvény teszteléséhez futtassa a helyi webalkalmazás előtér-összetevőjét

Ha tesztelni szeretné a függvény végpontját egy másik webalkalmazásból, egy egyszerű alkalmazás található az adattár előtér *-mappájában* .

1. Nyisson meg egy új terminált vagy parancssort, és aktiválja a virtuális környezetet (a [Python virtuális környezet létrehozása és aktiválása](#create-and-activate-a-python-virtual-environment)című szakaszban leírtak szerint).

1. Navigáljon az adattár előtér *-mappájához* .

1. HTTP-kiszolgáló elindítása a Pythonmal:

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash 
    python -m http.server
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    py -m http.server
    ```

    # <a name="cmd"></a>[Cmd](#tab/cmd)

    ```cmd
    py -m http.server
    ```

1. Egy böngészőben nyissa meg a `localhost:8000` címet, majd adja meg az alábbi fénykép URL-címek egyikét a szövegmezőbe, vagy használja a nyilvánosan elérhető rendszerképek URL-címét.

    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat2.png`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog1.png`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog2.png`
    
1. Válassza a **Submit (Küldés** ) lehetőséget a függvény végpontjának meghívásához a rendszerkép besorolásához.

    ![A befejezett projekt képernyőképe](media/functions-machine-learning-tensorflow/functions-machine-learning-tensorflow-screenshot.png)

    Ha a böngésző hibát jelez a rendszerkép URL-címének elküldésekor, ellenőrizze azt a terminált, amelyben a Function alkalmazást futtatja. Ha olyan hibaüzenetet lát, mint a "nem található modul" PIL ", lehet, hogy elindította a Function alkalmazást a *Start* mappában anélkül, hogy először aktiválja a korábban létrehozott virtuális környezetet. Ha továbbra is megjelenik a hibák, futtassa `pip install -r requirements.txt` újra a virtuális környezetet, és keressen hibákat.

> [!NOTE]
> A modell minden esetben macskaként vagy kutyáként osztályozza a rendszerkép tartalmát, függetlenül attól, hogy a rendszerkép tartalmazza-e az alapértelmezettet, vagy sem. A Tigers és a Párducok képei, például általában Cat-ként vannak osztályozva, de az elefántok, a sárgarépa vagy a repülők képei Dog-ként vannak osztályozva.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Mivel az oktatóanyag teljes egészében helyileg fut a gépen, nincs szükség Azure-erőforrásokra vagy-szolgáltatásokra.

## <a name="next-steps"></a>Következő lépések

Ebből az oktatóanyagból megtudhatta, hogyan hozhat létre és szabhat testre egy HTTP API-végpontot a Azure Functions használatával a rendszerképek TensorFlow-modell használatával történő besorolásához. Azt is megtanulta, hogyan hívhatja meg az API-t egy webalkalmazásból. Az oktatóanyagban található technikák használatával bármilyen bonyolultságú API-t építhet ki, miközben a Azure Functions által biztosított kiszolgáló nélküli számítási modellen fut.

> [!div class="nextstepaction"]
> [A függvény üzembe helyezése Azure Functions az Azure CLI útmutatójának használatával](./functions-run-local.md#publish)

Lásd még:

- [A függvény üzembe helyezése az Azure-ban a Visual Studio Code használatával](https://code.visualstudio.com/docs/python/tutorial-azure-functions).
- [Azure Functions Python fejlesztői útmutató](./functions-reference-python.md)
- [Fájlmegosztás csatlakoztatása egy Python-függvény alkalmazásához az Azure CLI használatával](./scripts/functions-cli-mount-files-storage-linux.md)
