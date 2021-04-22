---
title: Futtatás nyomon követése, figyelése és elemzése
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan kezdheti el, figyelheti és követheti nyomon a gépi tanulási kísérleteket a Python SDK Azure Machine Learning használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: swinner95
ms.author: shwinne
ms.reviewer: sgilley
ms.date: 04/19/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, devx-track-azurecli
ms.openlocfilehash: af591a5defcef1e8d043d54f08447324a34a10c4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107876294"
---
# <a name="start-monitor-and-track-run-history"></a>Futtatás előzményeinek elkezdése, figyelése és nyomon követése

A [Azure Machine Learning SDK for Python](/python/api/overview/azure/ml/intro), Machine Learning [CLI](reference-azure-machine-learning-cli.md), és [Azure Machine Learning stúdió](https://ml.azure.com) különböző módszereket kínál a futtatás figyelésére, rendszerezésére és nyomon követésére a betanítás és kísérletezés érdekében. A gépi tanulási futtatási előzmények fontos részét képezi egy elmagyarázható és ismételhető gépi tanulási fejlesztési folyamatnak.

Ez a cikk a következő feladatok elvégzését mutatja be:

* A futtatás teljesítményének figyelése.
* Hozzon létre egy egyéni nézetet. 
* Adja meg a futtatás leírását. 
* A futtatás címkézése és megkeresása.
* Futtassa a keresést a futtatás előzményein. 
* Megszakíthatja vagy sikertelenül futtathatja a futtatásokat.
* Gyermekfuttaik létrehozása.
* A futtatás állapotának figyelése e-mail-értesítéssel.
 

> [!TIP]
> Ha információt keres a szolgáltatás és a társított Azure-szolgáltatások Azure Machine Learning service monitorozásával kapcsolatban, tekintse meg a [következőt:](monitor-azure-machine-learning.md)Azure Machine Learning.
> Ha webszolgáltatás IoT Edge ként vagy modulként üzembe helyezett modellek monitorozásával [](how-to-enable-data-collection.md) kapcsolatos információkat keres, tekintse meg a modelladatok gyűjtését és a monitorozást a [Application Insights.](how-to-enable-app-insights.md)

## <a name="prerequisites"></a>Előfeltételek

A következőkre lesz szüksége:

* Azure-előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a Azure Machine Learning [ingyenes vagy Azure Machine Learning](https://aka.ms/AMLFree) verzióját.

* Egy [Azure Machine Learning munkaterület.](how-to-manage-workspace.md)

* A Azure Machine Learning SDK for Python (1.0.21-es vagy újabb verzió). Az SDK legújabb verziójának telepítéséhez vagy frissítéséhez lásd: Az SDK telepítése vagy [frissítése.](/python/api/overview/azure/ml/install)

    A Azure Machine Learning SDK verziójának ellenőrzéshez használja a következő kódot:

    ```python
    print(azureml.core.VERSION)
    ```

* Az [Azure CLI](/cli/azure/?preserve-view=true&view=azure-cli-latest) és a cli bővítmény a [Azure Machine Learning.](reference-azure-machine-learning-cli.md)


## <a name="monitor-run-performance"></a>Futtatás teljesítményének figyelése

* Futtatás és naplózási folyamatának kezdete

    # <a name="python"></a>[Python](#tab/python)
    
    1. A kísérlet beállításhoz importálja a [Workspace](/python/api/azureml-core/azureml.core.workspace.workspace), [Experiment](/python/api/azureml-core/azureml.core.experiment.experiment), [Run](/python/api/azureml-core/azureml.core.run%28class%29), és [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig) osztályokat az [azureml.core csomagból.](/python/api/azureml-core/azureml.core)
    
        ```python
        import azureml.core
        from azureml.core import Workspace, Experiment, Run
        from azureml.core import ScriptRunConfig
        
        ws = Workspace.from_config()
        exp = Experiment(workspace=ws, name="explore-runs")
        ```
    
    1. Indítson el egy futtatási folyamatot és annak naplózási folyamatát a [`start_logging()`](/python/api/azureml-core/azureml.core.experiment%28class%29#start-logging--args----kwargs-) metódussal.
    
        ```python
        notebook_run = exp.start_logging()
        notebook_run.log(name="message", value="Hello from run!")
        ```
        
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    A kísérlet futtatásának elkezdésében kövesse az alábbi lépéseket:
    
    1. Egy rendszerhéjból vagy parancssorból az Azure CLI használatával hitelesítse magát az Azure-előfizetésben:
    
        ```azurecli-interactive
        az login
        ```
        
        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 
    
    1. Csatoljon egy munkaterület-konfigurációt a betanítási szkriptet tartalmazó mappához. Cserélje `myworkspace` le a helyére Azure Machine Learning munkaterületet. Cserélje `myresourcegroup` le a helyére a munkaterületet tartalmazó Azure-erőforráscsoportot:
    
        ```azurecli-interactive
        az ml folder attach -w myworkspace -g myresourcegroup
        ```
    
        Ez a parancs létrehoz egy alkönyvtárat, amely példa `.azureml` runconfig- és Conda-környezeti fájlokat tartalmaz. Emellett egy fájlt is tartalmaz, amely a `config.json` munkaterülettel való Azure Machine Learning használható.
    
        További információ: [az ml folder attach](/cli/azure/ml/folder?preserve-view=true&view=azure-cli-latest#az_ml_folder_attach).
    
    2. A futtatás a következő paranccsal indítandó el. A parancs használata esetén adja meg a runconfig fájl nevét (a .runconfig előtti szöveget, ha a fájlrendszert használja) a \* -c paraméterrel szemben.
    
        ```azurecli-interactive
        az ml run submit-script -c sklearn -e testexperiment train.py
        ```
    
        > [!TIP]
        > A `az ml folder attach` parancs létrehozott egy `.azureml` alkönyvtárat, amely két példa runconfig fájlt tartalmaz.
        >
        > Ha olyan Python-szkriptet használ, amely programozott módon hoz létre egy futtatási konfigurációs objektumot, a [RunConfig.save()](/python/api/azureml-core/azureml.core.runconfiguration#save-path-none--name-none--separate-environment-yaml-false-) használatával mentheti runconfig fájlként.
        >
        > További példa a runconfig fájlokra: [https://github.com/MicrosoftDocs/pipelines-azureml/](https://github.com/MicrosoftDocs/pipelines-azureml/) .
    
        További információ: [az ml run submit-script.](/cli/azure/ml/run?preserve-view=true&view=azure-cli-latest#az_ml_run_submit-script)

    # <a name="studio"></a>[Studio](#tab/azure-studio)

    Példa egy modell betanítára a modelltervezőben Azure Machine Learning tutorial: Predict automobile price with the designer (Oktatóanyag: Autó árának előrejelzése [a tervezővel).](tutorial-designer-automobile-price-train-score.md)

    ---

* Futtatás állapotának figyelése

    # <a name="python"></a>[Python](#tab/python)
    
    * Le kell kapnia egy futtatás állapotát a [`get_status()`](/python/api/azureml-core/azureml.core.run%28class%29#get-status--) metódussal.
    
        ```python
        print(notebook_run.get_status())
        ```
    
    * A futtatás azonosítójának, végrehajtási idejét és a futtatás egyéb részleteit a metódussal [`get_details()`](/python/api/azureml-core/azureml.core.workspace.workspace#get-details--) olvashatja be.
    
        ```python
        print(notebook_run.get_details())
        ```
    
    * Ha a futtatás sikeresen befejeződött, a metódussal jelölje [`complete()`](/python/api/azureml-core/azureml.core.run%28class%29#complete--set-status-true-) meg befejezettként.
    
        ```python
        notebook_run.complete()
        print(notebook_run.get_status())
        ```
    
    * Ha a Python tervezési mintáját használja, a futtatás automatikusan befejezettként fogja megjelölni magát, ha a futtatás `with...as` hatóköre kívül van. Nem kell manuálisan befejezettként megjelölni a futtatásokat.
        
        ```python
        with exp.start_logging() as notebook_run:
            notebook_run.log(name="message", value="Hello from run!")
            print(notebook_run.get_status())
        
        print(notebook_run.get_status())
        ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    * A kísérlet futtatás-listájának megtekintéséhez használja a következő parancsot. Cserélje `experiment` le a helyére a kísérlet nevét:
    
        ```azurecli-interactive
        az ml run list --experiment-name experiment
        ```
    
        Ez a parancs egy JSON-dokumentumot ad vissza, amely felsorolja a kísérlet futtatásokkal kapcsolatos információit.
    
        További információ: [az ml experiment list.](/cli/azure/ml/experiment?preserve-view=true&view=azure-cli-latest#az_ml_experiment_list)
    
    * Egy adott futtatás információinak megtekintéséhez használja a következő parancsot. Cserélje `runid` le a helyére a futtatás azonosítóját:
    
        ```azurecli-interactive
        az ml run show -r runid
        ```
    
        Ez a parancs egy JSON-dokumentumot ad vissza, amely felsorolja a futtatás adatait.
    
        További információ: [az ml run show.](/cli/azure/ml/run?preserve-view=true&view=azure-cli-latest#az_ml_run_show)
    
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    ---
## <a name="custom-view"></a>Egyéni nézet 
    
A futtatás megtekintése a studióban: 
    
1. Lépjen a **Kísérletek lapra.**
    
1. Válassza a **Minden kísérlet lehetőséget** a kísérlet  összes futtatásának megtekintéséhez, vagy válassza az Összes futtatás lehetőséget a munkaterületre elküldött összes futtatás megtekintéséhez.
    
A **Minden futtatás oldalon** címkék, kísérletek, számítási cél és egyéb adatok alapján szűrheti a futtatásokat, így jobban rendszerezheti és hatókörbe sorolhatja a munkáját.  
    
1. Az összehasonlításhoz kiválasztott futtatásokkal, diagramok hozzáadásával vagy szűrők alkalmazásával testre szabhatja az oldalt. Ezek a módosítások egyéni nézetként **menthetők,** így könnyedén visszatérhet a munkához. A munkaterület-engedélyekkel rendelkező felhasználók szerkeszthetik vagy megtekinthetik az egyéni nézetet. Emellett a Nézet megosztása lehetőség kiválasztásával ossza meg az egyéni nézetet a csapattagokkal a hatékonyabb **együttműködés érdekében.**   
    
:::image type="content" source="media/how-to-track-monitor-analyze-runs/custom-views.gif" alt-text="Képernyőkép: egyéni nézet létrehozása":::
    
1. A futtatási naplók megtekintéséhez válasszon ki egy adott futtatás, majd a **Kimenetek +** naplók lapon megtalálja a futtatás diagnosztikai és hibanaplóit.

## <a name="run-description"></a>Futtatás leírása 

A futtatás leírásával további kontextust és információkat kaphat a futtatásról. Ezeket a leírásokat a futtatáslistában is megkeresheti, és hozzáadhatja a futtatás leírását oszlopként a futtatás listához. 

Lépjen a **futtatás Futtatás** részletei lapjára, és válassza a szerkesztés vagy ceruza ikont a futtatás leírásának hozzáadásához, szerkesztéséhez vagy törléséhez. A futtatáslista módosításainak megőrzéséhez mentse a módosításokat a meglévő egyéni nézetbe vagy egy új egyéni nézetbe. A Markdown formátum támogatott a futtatás leírásában, amely lehetővé teszi képek beágyazást és mély hivatkozásokat az alább látható módon.

:::image type="content" source="media/how-to-track-monitor-analyze-runs/run-description.gif" alt-text="Képernyőkép: futtatás leírásának létrehozása"::: 

## <a name="tag-and-find-runs"></a>Futtatás címkézése és megkeresása

A Azure Machine Learning tulajdonságok és címkék használatával rendszerezheti és lekérdezheti a futtatásokat fontos információkért.

* Tulajdonságok és címkék hozzáadása

    # <a name="python"></a>[Python](#tab/python)
    
    Ha kereshető metaadatokat szeretne hozzáadni a futtatáshoz, használja a [`add_properties()`](/python/api/azureml-core/azureml.core.run%28class%29#add-properties-properties-) metódust. A következő kód például hozzáadja a `"author"` tulajdonságot a futtatáshoz:
    
    ```Python
    local_run.add_properties({"author":"azureml-user"})
    print(local_run.get_properties())
    ```
    
    A tulajdonságok nem módosíthatók, így naplózható állandó rekordot hoznak létre. Az alábbi példakód hibát jelez, mert az előző kódban már hozzáadtunk `"azureml-user"` `"author"` tulajdonságértéket:
    
    ```Python
    try:
        local_run.add_properties({"author":"different-user"})
    except Exception as e:
        print(e)
    ```
    
    A tulajdonságokkal ellentétben a címkék is testreszabhatók. A kísérlet fogyasztói számára kereshető és értelmezhető információk hozzáadásához használja a [`tag()`](/python/api/azureml-core/azureml.core.run%28class%29#tag-key--value-none-) metódust.
    
    ```Python
    local_run.tag("quality", "great run")
    print(local_run.get_tags())
    
    local_run.tag("quality", "fantastic run")
    print(local_run.get_tags())
    ```
    
    Egyszerű sztringcímkéket is hozzáadhat. Ha ezek a címkék kulcsokként jelennek meg a címkeszótárban, a értéke `None` .
    
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
    
    További információ: [az ml run update.](/cli/azure/ml/run?preserve-view=true&view=azure-cli-latest#az_ml_run_update)
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    Futtatáscímkéket adhat hozzá, szerkeszthet vagy törölhet a studióból. Lépjen **a futtatás Futtatás részletei** lapjára, és válassza a szerkesztést, vagy ceruza ikont a futtatás címkéinek hozzáadásához, szerkesztéséhez vagy törléséhez. A címkék között a futtatáslista oldalán is kereshet és szűrhet.
    
    :::image type="content" source="media/how-to-track-monitor-analyze-runs/run-tags.gif" alt-text="Képernyőkép: Futtatáscímkék hozzáadása, szerkesztése vagy törlése":::
    
    ---

* Lekérdezés tulajdonságai és címkéi

    A kísérletben futtatott futtatásokat lekérdezve visszaadhatja az adott tulajdonságoknak és címkéknek megfelelő futtatásokat.

    # <a name="python"></a>[Python](#tab/python)
    
    ```Python
    list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
    list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    Az Azure CLI támogatja a [JMESPath-lekérdezéseket,](http://jmespath.org) amelyek a futtatásokat tulajdonságok és címkék alapján szűrik. Ha JMESPath-lekérdezést használ az Azure CLI-vel, adja meg a `--query` paraméterrel. Az alábbi példák tulajdonságokat és címkéket használó lekérdezéseket mutatnak be:
    
    ```azurecli-interactive
    # list runs where the author property = 'azureml-user'
    az ml run list --experiment-name experiment [?properties.author=='azureml-user']
    # list runs where the tag contains a key that starts with 'worth another look'
    az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
    # list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
    az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
    ```
    
    Az Azure CLI-eredmények lekérdezésére vonatkozó további információkért lásd: [Az Azure CLI parancskimenetének lekérdezése.](/cli/azure/query-azure-cli?preserve-view=true&view=azure-cli-latest)
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    Adott futtatásokat a Minden futtatás **listából kereshet.** Innen két lehetőség közül választhat:
    
    1. Használja a **Szűrő hozzáadása gombot,** és válassza a szűrés címkékre lehetőséget a futtatás(ök)hoz rendelt címke szerinti szűréséhez. <br><br>
    OR
    
    1. A keresősáv használatával gyorsan megkeresheti a futtatás metaadatait, például a futtatás állapotát, leírását, kísérletnevét és beküldőnevét. 
    
## <a name="cancel-or-fail-runs"></a>Futtatás megszakítása vagy meghiúsulás

Ha hibát észleli, vagy ha a futtatás túl sokáig tart, megszakíthatja a futtatás futását.

# <a name="python"></a>[Python](#tab/python)

Az SDK használatával a következő módszerrel szakíthatja meg a [`cancel()`](/python/api/azureml-core/azureml.core.run%28class%29#cancel--) futtatásokat:

```python
src = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')
local_run = exp.submit(src)
print(local_run.get_status())

local_run.cancel()
print(local_run.get_status())
```

Ha a futtatás befejeződik, de hibát tartalmaz (például helytelen betanító szkriptet használt), a metódussal megjelölheti [`fail()`](/python/api/azureml-core/azureml.core.run%28class%29#fail-error-details-none--error-code-none---set-status-true-) sikertelenként.

```python
local_run = exp.submit(src)
local_run.fail()
print(local_run.get_status())
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

A következő paranccsal szakíthatja meg a futtatásokat a parancssori felület használatával. Cserélje `runid` le a helyére a futtatás azonosítóját

```azurecli-interactive
az ml run cancel -r runid -w workspace_name -e experiment_name
```

További információ: [az ml run cancel.](/cli/azure/ml/run?preserve-view=true&view=azure-cli-latest#az_ml_run_cancel)

# <a name="studio"></a>[Studio](#tab/azure-studio)

A következő lépésekkel szakíthatja meg a futtatásokat a Studióban:

1. A Kísérletek vagy a  Folyamatok szakaszban kattintson a futó **folyamatra.** 

1. Válassza ki a megszakítani kívánt folyamatfuttassanak számát.

1. Az eszköztáron válassza a Mégse **lehetőséget**

---

## <a name="create-child-runs"></a>Gyermekfuttaik létrehozása

Gyermekfutatokat hozhat létre a kapcsolódó futtatásokat csoportosítva, például különböző hiperparaméter-hangolási iterációkhoz.

> [!NOTE]
> Gyermekfutatokat csak az SDK használatával lehet létrehozni.

Ez a példakód a szkript használatával hoz létre öt gyermekfuttassa köteget egy elküldött futtatásból `hello_with_children.py` a [`child_run()`](/python/api/azureml-core/azureml.core.run%28class%29#child-run-name-none--run-id-none--outputs-none-) metódussal:

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
> A hatókören kívülre lépő gyermekfuttatásokat a rendszer automatikusan befejezettként jelöli meg.

Sok gyermekfuttatás hatékony létrehozásához használja a [`create_children()`](/python/api/azureml-core/azureml.core.run.run#create-children-count-none--tag-key-none--tag-values-none-) metódust. Mivel minden létrehozás hálózati hívást hoz létre, a futtatáskötet létrehozása hatékonyabb, mint egyesével létrehozni őket.

### <a name="submit-child-runs"></a>Gyermekfuttaik elküldése

A gyermekfuttatásokat egy szülőfuttatásból is be lehet nyújtani. Ez lehetővé teszi szülő- és gyermekfutta-hierarchiák létrehozására. Szülő nélküli gyermekfuttatás nem hozható létre: még ha a szülő futtatás csak gyermekfuttatásokat indít el, akkor is létre kell hoznia a hierarchiát. Az összes futtatás állapota független: a szülő akkor is lehet sikeres állapotban, ha egy vagy több gyermekfuttatás megszakadt vagy `"Completed"` meghiúsult.  

Előfordulhat, hogy azt szeretné, hogy a gyermekfuttassa a szülő futtatástól eltérő futtatási konfigurációt használjon. Használhat például egy kevésbé hatékony, processzoralapú konfigurációt a szülő számára, a gyermek számára pedig GPU-alapú konfigurációkat. Egy másik gyakori cél az, hogy minden gyermek különböző argumentumot és adatot ad át. Gyermekfutat testreszabásához hozzon létre egy objektumot a `ScriptRunConfig` gyermekfutat számára. 

> [!IMPORTANT]
> Ha távoli számításon futtatott szülő futtatásból szeretné gyermekfuttatást futtatni, először be kell jelentkeznie a munkaterületre a szülő futtatás kódjának használatával. Alapértelmezés szerint a távoli futtatás környezeti objektuma nem rendelkezik hitelesítő adatokkal a gyermekfuttatások elküldére. A bejelentkezéshez használjon szolgáltatásnév vagy felügyelt identitás hitelesítő adatait. A hitelesítéssel kapcsolatos további információkért lásd: [Hitelesítés beállítása.](how-to-setup-authentication.md)

Az alábbi kód:

- Lekér egy nevű számítási erőforrást a `"gpu-cluster"` munkaterületről `ws`
- Különböző argumentumértékeket iterál a gyermekobjektumok `ScriptRunConfig` számára
- Létrehoz és elküld egy új gyermekfuttat, az egyéni számítási erőforrás és argumentum használatával
- Blokkok, amíg a gyermek összes futtatása be nem fejeződik

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

Ha sok azonos konfigurációval, argumentummal és bemenettel futtatott gyermekfutatot is hatékonyan létre kell hoznia, használja a [`create_children()`](/python/api/azureml-core/azureml.core.run.run#create-children-count-none--tag-key-none--tag-values-none-) metódust. Mivel minden létrehozás hálózati hívást hoz létre, a futtatáskötet létrehozása hatékonyabb, mint egyesével létrehozni őket.

A gyermekfutaton belül megtekintheti a szülő futtatás azonosítóját:

```python
## In child run script
child_run = Run.get_context()
child_run.parent.id
```

### <a name="query-child-runs"></a>Gyermekfuttaik lekérdezése

Egy adott szülő gyermekfuttainak lekérdezéséhez használja a [`get_children()`](/python/api/azureml-core/azureml.core.run%28class%29#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) metódust. A argumentum lehetővé teszi a gyermekek és a nagyszülei ``recursive = True`` beágyazott fájának lekérdezését.

```python
print(parent_run.get_children())
```

### <a name="log-to-parent-or-root-run"></a>Naplózás szülő- vagy gyökérfutatra

A mező használatával hozzáférhet az aktuális gyermekfutatot `Run.parent` elindításakor futtatott futtatáshoz. A használatának gyakori esete a naplók eredményeinek egyetlen helyen `Run.parent` való egyes használata. A gyermekfuttatások aszinkron módon futnak, és nem garantálható, hogy a szülő csak akkor tudja végrehajtani a sorrendet vagy a szinkronizálást, ha a szülő megvárja, amíg a gyermekfuttatások befejeződnek.

```python
# in child (or even grandchild) run

def root_run(self : Run) -> Run :
    if self.parent is None : 
        return self
    return root_run(self.parent)

current_child_run = Run.get_context()
root_run(current_child_run).log("MyMetric", f"Data from child run {current_child_run.id}")

```

## <a name="monitor-the-run-status-by-email-notification"></a>A futtatás állapotának figyelése e-mail-értesítéssel

1. A [Azure Portal](https://ms.portal.azure.com/)bal oldali navigációs sávon válassza a **Figyelás** lapot. 

1. Válassza **a Diagnosztikai beállítások,** majd a **+ Diagnosztikai beállítás hozzáadása lehetőséget.**

    ![Képernyőkép az e-mail-értesítések diagnosztikai beállításairól](./media/how-to-track-monitor-analyze-runs/diagnostic-setting.png)

1. A Diagnosztikai beállításban: 
    1. A Category **details (Kategória részletei) alatt** válassza az **AmlRunStatusChangedEvent (AmlRunStatusChangedEvent) lehetőséget.** 
    1. A Cél **adatai között válassza** a Küldés a Log **Analyticsbe** munkaterületet, és adja meg az **Előfizetés** és a Log **Analytics munkaterületet.** 

    > [!NOTE]
    > Az **Azure Log Analytics-munkaterület** más típusú Azure-erőforrás, mint Azure Machine Learning service **munkaterület.** Ha a listában nincsenek lehetőségek, létrehozhat egy [Log Analytics-munkaterületet.](../azure-monitor/logs/quick-create-workspace.md) 
    
    ![E-mail-értesítés mentésének helye](./media/how-to-track-monitor-analyze-runs/log-location.png)

1. A **Naplók lapon** adjon hozzá egy **Új riasztási szabályt.** 

    ![Új riasztási szabály](./media/how-to-track-monitor-analyze-runs/new-alert-rule.png)

1. Tekintse [meg, hogyan hozhat létre és kezelhet naplóriasztásokat a Azure Monitor.](../azure-monitor/alerts/alerts-log.md)

## <a name="example-notebooks"></a>Példajegyzetfüzetek

Az alábbi jegyzetfüzetek a cikkben lévő fogalmakat mutatják be:

* A naplózási API-król a naplózási API-jegyzetfüzetben [olvashat bővebben.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api/logging-api.ipynb)

* A futtatásnak a Azure Machine Learning SDK-val való kezelésével kapcsolatos további információkért lásd a [futtatásokat kezelő jegyzetfüzetet.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/manage-runs/manage-runs.ipynb)

## <a name="next-steps"></a>Következő lépések

* A kísérletek metrikák naplózását a Log metrics during training runs (Metrikák naplózása a betanítás [futtatásai során) cikkből tudja meg.](how-to-log-view-metrics.md)
* Az erőforrások és naplók monitorozását a Azure Machine Learning [monitorozását Azure Machine Learning.](monitor-azure-machine-learning.md)