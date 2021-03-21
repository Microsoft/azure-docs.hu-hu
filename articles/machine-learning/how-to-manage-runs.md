---
title: A betanítási futtatások elindítása, figyelése és megszakítása a Pythonban
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan indíthatja el, tekintse át és kezelje a Machine learning-kísérletet a Azure Machine Learning Python SDK-val.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 03/04/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, devx-track-azurecli
ms.openlocfilehash: 977498abb17fe592cef344f407a662d3b79749b7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102634790"
---
# <a name="start-monitor-and-track-runs"></a>Indítás, figyelés és a futtatások nyomon követése 

A [Pythonhoz készült Azure Machine learning SDK](/python/api/overview/azure/ml/intro), [Machine learning parancssori](reference-azure-machine-learning-cli.md)felület és a [Azure Machine learning Studio](https://ml.azure.com) különböző módszereket biztosít a futtatások monitorozásához, rendszerezéséhez és kezeléséhez a képzés és kísérletezés érdekében.

Ez a cikk a következő feladatokra mutat be példákat:

* A futtatási teljesítmény figyelése.
* A futtatási állapot figyelése e-mailben értesítéssel.
* Címke és keresés futtatása.
* Adjon hozzá egy futtatási leírást. 
* A keresés futtatása. 
* Megszakítás vagy sikertelen Futtatás.
* Gyermek-futtatások létrehozása.
 

> [!TIP]
> Ha a Azure Machine Learning szolgáltatás és a kapcsolódó Azure-szolgáltatások figyelésével kapcsolatos információkat keres, tekintse meg a [Azure Machine learning figyelése](monitor-azure-machine-learning.md)című témakört.
> Ha webszolgáltatásként vagy IoT Edge modulként üzembe helyezett figyelési modellekkel kapcsolatos információkat keres, tekintse meg a következőt: a [modell adatainak](how-to-enable-data-collection.md) és [figyelésének összegyűjtése Application Insightssal](how-to-enable-app-insights.md).

## <a name="prerequisites"></a>Előfeltételek

A következő elemekre lesz szüksége:

* Azure-előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.

* Egy [Azure Machine learning munkaterület](how-to-manage-workspace.md).

* A Pythonhoz készült Azure Machine Learning SDK (1.0.21 vagy újabb verzió). Az SDK legújabb verziójának telepítéséhez vagy frissítéséhez lásd: [az SDK telepítése vagy frissítése](/python/api/overview/azure/ml/install).

    A Azure Machine Learning SDK verziójának vizsgálatához használja a következő kódot:

    ```python
    print(azureml.core.VERSION)
    ```

* Az [Azure CLI](/cli/azure/?preserve-view=true&view=azure-cli-latest) és [cli bővítmény a Azure Machine Learninghoz](reference-azure-machine-learning-cli.md).


## <a name="monitor-run-performance"></a>A futtatási teljesítmény figyelése

* Futtatás és a naplózási folyamat elindítása

    # <a name="python"></a>[Python](#tab/python)
    
    1. Állítsa be a kísérletet a [munkaterület](/python/api/azureml-core/azureml.core.workspace.workspace), a [kísérlet](/python/api/azureml-core/azureml.core.experiment.experiment), a [Futtatás](/python/api/azureml-core/azureml.core.run%28class%29)és a [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) osztályok importálásával a [azureml. Core](/python/api/azureml-core/azureml.core) csomagból.
    
        ```python
        import azureml.core
        from azureml.core import Workspace, Experiment, Run
        from azureml.core import ScriptRunConfig
        
        ws = Workspace.from_config()
        exp = Experiment(workspace=ws, name="explore-runs")
        ```
    
    1. Indítsa el a futtatást és a naplózási folyamatot a [`start_logging()`](/python/api/azureml-core/azureml.core.experiment%28class%29#start-logging--args----kwargs-) metódussal.
    
        ```python
        notebook_run = exp.start_logging()
        notebook_run.log(name="message", value="Hello from run!")
        ```
        
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    A kísérlet futtatásának elindításához kövesse az alábbi lépéseket:
    
    1. Egy rendszerhéjból vagy parancssorból az Azure CLI használatával hitelesítheti az Azure-előfizetését:
    
        ```azurecli-interactive
        az login
        ```
        
        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 
    
    1. Csatoljon egy munkaterület-konfigurációt a betanítási parancsfájlt tartalmazó mappához. Cserélje le a `myworkspace` t a Azure Machine learning munkaterületére. Cserélje le a helyére `myresourcegroup` a munkaterületet tartalmazó Azure-erőforráscsoportot:
    
        ```azurecli-interactive
        az ml folder attach -w myworkspace -g myresourcegroup
        ```
    
        Ez a parancs létrehoz egy `.azureml` alkönyvtárat, amely tartalmazza például a runconfig és a Conda környezeti fájlokat. Emellett tartalmaz egy fájlt is, `config.json` amely a Azure Machine learning munkaterülettel folytatott kommunikációhoz használható.
    
        További információ: [az ml mappa csatolása](/cli/azure/ext/azure-cli-ml/ml/folder?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).
    
    2. A Futtatás elindításához használja a következő parancsot. Ha ezt a parancsot használja, adja meg a runconfig-fájl nevét (a. runconfig karakterláncot, \* Ha a fájlrendszert keresi) a-c paraméterrel.
    
        ```azurecli-interactive
        az ml run submit-script -c sklearn -e testexperiment train.py
        ```
    
        > [!TIP]
        > A `az ml folder attach` parancs létrehozott egy `.azureml` alkönyvtárat, amely két példa runconfig-fájlt tartalmaz.
        >
        > Ha olyan Python-szkripttel rendelkezik, amely programozott módon hozza létre a futtatási konfigurációs objektumot, a [RunConfig. Save ()](/python/api/azureml-core/azureml.core.runconfiguration#save-path-none--name-none--separate-environment-yaml-false-) paranccsal mentheti RunConfig-fájlként.
        >
        > További példák a runconfig-fájlokra: [https://github.com/MicrosoftDocs/pipelines-azureml/](https://github.com/MicrosoftDocs/pipelines-azureml/) .
    
        További információ: [az ml Run Submit-script](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).

    # <a name="studio"></a>[Studio](#tab/azure-studio)

    A Azure Machine Learning Designerben található modellek betanítását bemutató példát a következő témakörben talál [: oktatóanyag: az autó árának előrejelzése a tervezővel](tutorial-designer-automobile-price-train-score.md).

    ---

* Futtatás állapotának figyelése

    # <a name="python"></a>[Python](#tab/python)
    
    * Egy Futtatás állapotának lekérése a [`get_status()`](/python/api/azureml-core/azureml.core.run%28class%29#get-status--) metódussal.
    
        ```python
        print(notebook_run.get_status())
        ```
    
    * A futtatási azonosító, a végrehajtási idő és a Futtatás további részleteinek beszerzéséhez használja a [`get_details()`](/python/api/azureml-core/azureml.core.workspace.workspace#get-details--) metódust.
    
        ```python
        print(notebook_run.get_details())
        ```
    
    * Ha a Futtatás sikeresen befejeződött, a metódus használatával fejezze be a befejezést [`complete()`](/python/api/azureml-core/azureml.core.run%28class%29#complete--set-status-true-) .
    
        ```python
        notebook_run.complete()
        print(notebook_run.get_status())
        ```
    
    * Ha a Python `with...as` tervezési mintát használja, a Futtatás automatikusan befejezettként jelenik meg, ha a Futtatás nem a hatókörön kívül van. Nem kell manuálisan megjelölni a futtatást befejezettként.
        
        ```python
        with exp.start_logging() as notebook_run:
            notebook_run.log(name="message", value="Hello from run!")
            print(notebook_run.get_status())
        
        print(notebook_run.get_status())
        ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    * A kísérlet futtatási listájának megtekintéséhez használja a következő parancsot. Cserélje le a `experiment` nevet a kísérlet nevére:
    
        ```azurecli-interactive
        az ml run list --experiment-name experiment
        ```
    
        Ez a parancs egy JSON-dokumentumot ad vissza, amely felsorolja a kísérlet futtatásával kapcsolatos információkat.
    
        További információ: [az ml-kísérletek listája](/cli/azure/ext/azure-cli-ml/ml/experiment?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).
    
    * Egy adott futtatással kapcsolatos információk megtekintéséhez használja az alábbi parancsot. Cserélje le a `runid` parancsot a Futtatás azonosítójának helyére:
    
        ```azurecli-interactive
        az ml run show -r runid
        ```
    
        Ez a parancs egy JSON-dokumentumot ad vissza, amely felsorolja a futtatással kapcsolatos információkat.
    
        További információ: [az ml Run show](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-show).
    
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    A futtatások megtekintése a Studióban: 
    
    1. Navigáljon a **kísérletek** lapra.
    
    1. Válassza ki az **összes kísérletet** egy kísérlet összes futtatásának megtekintéséhez, vagy válassza a **minden Futtatás** lehetőséget a munkaterületen elküldött összes Futtatás megtekintéséhez.
    
        A **minden Futtatás** lapon megadhatja a futtatások listáját címkék, kísérletek, számítási cél alapján, és így jobban rendszerezheti és kihasználhatja a munkáját.  
    
    1. Az oldal testreszabásához válassza a futtatások összevetése, diagramok hozzáadása vagy szűrők alkalmazása lehetőséget. Ezeket a módosításokat **Egyéni nézetként** mentheti, így könnyen visszatérhet a munkájához. A munkaterület-engedélyekkel rendelkező felhasználók módosíthatják vagy megtekinthetik az egyéni nézetet. Emellett a **megosztás nézet** kiválasztásával megoszthatja az egyéni nézetet a csapattagokkal a bővített együttműködéshez.   
    
        :::image type="content" source="media/how-to-manage-runs/custom-views.gif" alt-text="Képernyőfelvétel: egyéni nézet létrehozása":::
    
    1. A futtatási naplók megtekintéséhez válasszon ki egy adott futtatást, majd a **kimenetek és naplók** lapon keresse meg a futtatásához szükséges diagnosztikai és hibanapló-naplókat.
    
    ---

## <a name="monitor-the-run-status-by-email-notification"></a>A futtatási állapot figyelése e-mailben – értesítés

1. A [Azure Portal](https://ms.portal.azure.com/)bal oldali navigációs sávján kattintson a **figyelés** fülre. 

1. Válassza a **diagnosztikai beállítások** , majd a **+ diagnosztikai beállítás hozzáadása** elemet.

    ![Az e-mail-értesítések diagnosztikai beállításainak képernyőképe](./media/how-to-manage-runs/diagnostic-setting.png)

1. A diagnosztikai beállításban 
    1. a **Kategória részletei** területen válassza ki a **AmlRunStatusChangedEvent**. 
    1. A **célhely részletei** között válassza a **Küldés log Analytics munkaterületre**  lehetőséget, és adja meg az **előfizetést** és **log Analytics munkaterületet**. 

    > [!NOTE]
    > Az **azure log Analytics munkaterület** más típusú Azure-erőforrás, mint a **Azure Machine learning szolgáltatás munkaterülete**. Ha a listában nincsenek beállítások, [létrehozhat egy log Analytics munkaterületet](https://docs.microsoft.com/azure/azure-monitor/logs/quick-create-workspace). 
    
    ![Az e-mail-értesítések mentése](./media/how-to-manage-runs/log-location.png)

1. A **naplók** lapon adjon hozzá egy **új riasztási szabályt**. 

    ![Új riasztási szabály](./media/how-to-manage-runs/new-alert-rule.png)

1. Lásd: [naplózási riasztások létrehozása és kezelése Azure monitor használatával](https://docs.microsoft.com/azure/azure-monitor/alerts/alerts-log).

## <a name="run-description"></a>Futtatás leírása 

Egy futtatási Leírás hozzáadható egy futtatáshoz, amely további kontextust és információkat biztosít a futtatáshoz. Ezeket a leírásokat a Runs (futtatások) listában is megkeresheti, és a Futtatás leírását oszlopként is hozzáadhatja a futtatások listájában. 

Navigáljon a Futtatás **részletei** lapra, és válassza a Szerkesztés vagy a ceruza ikont a futtatásához szükséges leírások hozzáadásához, szerkesztéséhez vagy törléséhez. A futtatások listájának módosításához mentse a módosításokat a meglévő egyéni nézetbe vagy egy új egyéni nézetbe. A Markdown formátuma támogatott a futtatási leírások esetében, amelyek lehetővé teszik a képek beágyazását és mély összekapcsolását az alább látható módon.

:::image type="content" source="media/how-to-manage-runs/run-description.gif" alt-text="Képernyőkép: Futtatás leírásának létrehozása"::: 

## <a name="tag-and-find-runs"></a>Címke és keresés futtatása

Azure Machine Learning a tulajdonságok és címkék segítségével rendszerezheti és lekérdezheti a futtatásokat a fontos információkhoz.

* Tulajdonságok és Címkék hozzáadása

    # <a name="python"></a>[Python](#tab/python)
    
    Ha kereshető metaadatokat szeretne hozzáadni a futtatásokhoz, használja a [`add_properties()`](/python/api/azureml-core/azureml.core.run%28class%29#add-properties-properties-) metódust. A következő kód például hozzáadja a `"author"` tulajdonságot a futtatáshoz:
    
    ```Python
    local_run.add_properties({"author":"azureml-user"})
    print(local_run.get_properties())
    ```
    
    A tulajdonságok nem változtathatók meg, ezért állandó rekordot hoznak létre naplózási célokra. A következő kódrészlet egy hibát eredményez, mivel a tulajdonság értékeként már hozzáadva lett az `"azureml-user"` `"author"` előző kódban:
    
    ```Python
    try:
        local_run.add_properties({"author":"different-user"})
    except Exception as e:
        print(e)
    ```
    
    A tulajdonságoktól eltérően a címkék változhatnak. A kísérlet felhasználói számára kereshető és hasznos információk hozzáadásához használja a [`tag()`](/python/api/azureml-core/azureml.core.run%28class%29#tag-key--value-none-) metódust.
    
    ```Python
    local_run.tag("quality", "great run")
    print(local_run.get_tags())
    
    local_run.tag("quality", "fantastic run")
    print(local_run.get_tags())
    ```
    
    Hozzáadhat egyszerű karakterlánc-címkéket is. Ha ezek a címkék a címke szótárában kulcsként jelennek meg, akkor a értékük a következő: `None` .
    
    ```Python
    local_run.tag("worth another look")
    print(local_run.get_tags())
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    > [!NOTE]
    > A CLI használatával csak címkéket adhat hozzá vagy frissíthet.
    
    Címke hozzáadásához vagy frissítéséhez használja a következő parancsot:
    
    ```azurecli-interactive
    az ml run update -r runid --add-tag quality='fantastic run'
    ```
    
    További információ: [az ml Run Update](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-update).
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    Hozzáadhat, szerkeszthet vagy törölhet címkéket a studióból. Navigáljon a Futtatás **részletei** lapra, és válassza a Szerkesztés vagy a ceruza ikont a futtatott címkék hozzáadásához, szerkesztéséhez vagy törléséhez. A címkéket a futtatások listája lapról is megkeresheti és szűrheti.
    
    :::image type="content" source="media/how-to-manage-runs/run-tags.gif" alt-text="Képernyőkép: a futtatási Címkék hozzáadása, szerkesztése vagy törlése":::
    
    ---

* Lekérdezés tulajdonságai és címkék

    A kísérletek futtatásával lekérdezheti az adott tulajdonságokkal és címkékkel egyező futtatások listáját.

    # <a name="python"></a>[Python](#tab/python)
    
    ```Python
    list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
    list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    Az Azure CLI támogatja a [JMESPath](http://jmespath.org) -lekérdezéseket, amelyek a tulajdonságok és címkék alapján szűrhetik a futtatásokat. Ha JMESPath-lekérdezést szeretne használni az Azure CLI-vel, a paraméterrel kell megadnia `--query` . Az alábbi példák a tulajdonságokat és a címkéket használó lekérdezéseket mutatják be:
    
    ```azurecli-interactive
    # list runs where the author property = 'azureml-user'
    az ml run list --experiment-name experiment [?properties.author=='azureml-user']
    # list runs where the tag contains a key that starts with 'worth another look'
    az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
    # list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
    az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
    ```
    
    Az Azure CLI eredményeinek lekérdezésével kapcsolatos további információkért lásd: az [Azure CLI-parancs kimenetének lekérdezése](/cli/azure/query-azure-cli?preserve-view=true&view=azure-cli-latest).
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    Adott futtatások kereséséhez navigáljon a  **minden Futtatás** listára. Innen két lehetőség közül választhat:
    
    1. Használja a **szűrő hozzáadása** gombot, és válassza a szűrés a címkéken lehetőséget a Futtatás (ok) hez hozzárendelt címkék szűréséhez. <br><br>
    OR
    
    1. A keresési sáv használatával gyorsan megkeresheti a futtatásokat, ha a futtatási metaadatokat, például a futtatási állapotot, a leírásokat, a kísérlet nevét és a küldő nevét keresi. 
    
## <a name="cancel-or-fail-runs"></a>Megszakítás vagy sikertelen Futtatás

Ha hibát észlel, vagy ha a futtatása túl sokáig tart, megszakíthatja a futtatást.

# <a name="python"></a>[Python](#tab/python)

Ha az SDK használatával szeretne lemondani egy futtatást, használja a következő [`cancel()`](/python/api/azureml-core/azureml.core.run%28class%29#cancel--) metódust:

```python
src = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')
local_run = exp.submit(src)
print(local_run.get_status())

local_run.cancel()
print(local_run.get_status())
```

Ha a Futtatás véget ér, de hibát tartalmaz (például a nem megfelelő betanítási parancsfájlt használta), a [`fail()`](/python/api/azureml-core/azureml.core.run%28class%29#fail-error-details-none--error-code-none---set-status-true-) metódussal nem lehet megjelölést végrehajtani.

```python
local_run = exp.submit(src)
local_run.fail()
print(local_run.get_status())
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Ha a parancssori felületen szeretné megszakítani a futtatást, használja a következő parancsot. Cserélje le a `runid` parancsot a Futtatás azonosítójának helyére.

```azurecli-interactive
az ml run cancel -r runid -w workspace_name -e experiment_name
```

További információ: [az ml Run Cancel](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-cancel).

# <a name="studio"></a>[Studio](#tab/azure-studio)

A következő lépések végrehajtásával szakíthatja meg a futtatást a Studióban:

1. Ugorjon a futó folyamatra a **kísérletek** vagy **folyamatok** szakaszban. 

1. Válassza ki a megszüntetni kívánt folyamat-futtatási számot.

1. Az eszköztáron válassza a **Mégse** lehetőséget.

---

## <a name="create-child-runs"></a>Gyermek-futtatások létrehozása

Hozzon létre egy alárendelt futtatásokat a kapcsolódó futtatások csoportosításához, például a különböző hiperparaméter-hangolási ismétlésekhez.

> [!NOTE]
> A gyermek-futtatásokat csak az SDK használatával lehet létrehozni.

Ez a kód például a `hello_with_children.py` szkripttel hozza létre az öt gyermekből álló köteget egy elküldött futtatásból a következő [`child_run()`](/python/api/azureml-core/azureml.core.run%28class%29#child-run-name-none--run-id-none--outputs-none-) metódus használatával:

```python
!more hello_with_children.py
src = ScriptRunConfig(source_directory='.', script='hello_with_children.py')

local_run = exp.submit(src)
local_run.wait_for_completion(show_output=True)
print(local_run.get_status())

with exp.start_logging() as parent_run:
    for c,count in enumerate(range(5)):
        with parent_run.child_run() as child:
            child.log(name="Hello from child run", value=c)
```

> [!NOTE]
> A hatókörből való kilépéskor a gyermek-futtatások automatikusan befejezettként vannak megjelölve.

Sok gyermek hatékony futtatásához használja a [`create_children()`](/python/api/azureml-core/azureml.core.run.run#create-children-count-none--tag-key-none--tag-values-none-) metódust. Mivel minden létrehozás egy hálózati hívást eredményez, a futtatások kötegének létrehozása hatékonyabb, mint az egyiket a létrehozásuk.

### <a name="submit-child-runs"></a>Alárendelt futtatások küldése

Alárendelt futtatásokat is el lehet küldeni egy szülő futtatásból. Ez lehetővé teszi a szülő-és gyermek-futtatási hierarchiák létrehozását. Nem hozható létre szülő nélküli gyermekobjektum: még akkor is, ha a szülő futtatása nem sikerül, de a gyermek-futtatások indítása nem lehetséges, a hierarchia létrehozásához továbbra is szükség van. Az összes Futtatás állapota független: a szülő lehet `"Completed"` sikeres állapotban, még akkor is, ha egy vagy több alárendelt Futtatás megszakadt vagy sikertelen volt.  

Előfordulhat, hogy a gyermeke más futtatási konfigurációt használ, mint a szülő Futtatás. Előfordulhat például, hogy egy kevésbé hatékony, CPU-alapú konfigurációt használ a szülő számára, miközben GPU-alapú konfigurációkat használ a gyermekei számára. Egy másik gyakori vágy, hogy minden gyermek különböző argumentumokat és adatátvitelt adjon át. Gyermek futtatásának testreszabásához hozzon létre egy `ScriptRunConfig` objektumot a gyermek futtatásához. Az alábbi kód a következő műveleteket végzi el:

- A munkaterületről elnevezett számítási erőforrás beolvasása `"gpu-cluster"``ws`
- A gyermek objektumok számára átadandó különböző argumentumok értékének megismétlése `ScriptRunConfig`
- Új gyermek futtatásának létrehozása és elküldése az egyéni számítási erőforrás és argumentum használatával
- Blokkok, amíg az összes gyermek futtatása befejeződött

```python
# parent.py
# This script controls the launching of child scripts
from azureml.core import Run, ScriptRunConfig

compute_target = ws.compute_targets["gpu-cluster"]

run = Run.get_context()

child_args = ['Apple', 'Banana', 'Orange']
for arg in child_args: 
    run.log('Status', f'Launching {arg}')
    child_config = ScriptRunConfig(source_directory=".", script='child.py', arguments=['--fruit', arg], compute_target=compute_target)
    # Starts the run asynchronously
    run.submit_child(child_config)

# Experiment will "complete" successfully at this point. 
# Instead of returning immediately, block until child runs complete

for child in run.get_children():
    child.wait_for_completion()
```

Ha sok gyermeket szeretne létrehozni azonos konfigurációk, argumentumok és bemenetek hatékony futtatásához, használja a [`create_children()`](/python/api/azureml-core/azureml.core.run.run#create-children-count-none--tag-key-none--tag-values-none-) metódust. Mivel minden létrehozás egy hálózati hívást eredményez, a futtatások kötegének létrehozása hatékonyabb, mint az egyiket a létrehozásuk.

Gyermeken belüli futtatáskor megtekintheti a szülő futtatási AZONOSÍTÓját:

```python
## In child run script
child_run = Run.get_context()
child_run.parent.id
```

### <a name="query-child-runs"></a>Lekérdezési gyermek futtatása

Egy adott szülő alárendelt futtatásának lekérdezéséhez használja a [`get_children()`](/python/api/azureml-core/azureml.core.run%28class%29#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) metódust. Az ``recursive = True`` argumentum lehetővé teszi a gyermekek és unokák beágyazott fájának lekérdezését.

```python
print(parent_run.get_children())
```

### <a name="log-to-parent-or-root-run"></a>Napló a szülő vagy a gyökér futtatásához

A `Run.parent` mezővel elérheti az aktuális gyermek futtatását elindító futtatást. Általános használati eset, ha egyetlen helyen szeretné összevonni a napló eredményeit. Vegye figyelembe, hogy a gyermekeket a rendszer aszinkron módon futtatja, és nincs garancia arra, hogy a szülő nem tudja megvárni a gyermek futásának befejeződését.

```python
# in child (or even grandchild) run

def root_run(self : Run) -> Run :
    if self.parent is None : 
        return self
    return root_run(self.parent)

current_child_run = Run.get_context()
root_run(current_child_run).log("MyMetric", f"Data from child run {current_child_run.id}")

```

## <a name="example-notebooks"></a>Példajegyzetfüzetek

A következő jegyzetfüzetek a cikkben ismertetett fogalmakat mutatják be:

* A naplózási API-kkal kapcsolatos további tudnivalókért tekintse meg a [naplózási API jegyzetfüzetet](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api/logging-api.ipynb).

* A Azure Machine Learning SDK-val való futtatásával kapcsolatos további információkért tekintse meg a [futtatási jegyzetfüzet kezelése](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/manage-runs/manage-runs.ipynb)című témakört.

## <a name="next-steps"></a>Következő lépések

* Ha szeretné megtudni, hogyan naplózhatja a kísérletek mérőszámait, tekintse meg a következő témakört: a [betanítási mérőszámok](how-to-track-experiments.md).
* Ha szeretné megismerni, hogyan figyelheti Azure Machine Learning erőforrásait és naplóit, tekintse meg a [figyelés Azure Machine learning](monitor-azure-machine-learning.md).
