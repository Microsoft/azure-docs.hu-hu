---
title: Automatizált ML-kísérletek hibakeresése
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogy miként lehet elhárítani és elhárítani a gépi tanulási kísérletek során felmerülő problémákat.
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 03/08/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, automl, references_regions
ms.openlocfilehash: 2e6e4e468adec76d50242b31922e4db1c7e910ab
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210482"
---
# <a name="troubleshoot-automated-ml-experiments-in-python"></a>Automatizált ML-kísérletek a Pythonban – problémamegoldás

Ebből az útmutatóból megtudhatja, hogyan azonosíthatók és oldhatók fel az automatizált gépi tanulással kapcsolatos ismert problémák az [Azure Machine learning SDK](/python/api/overview/azure/ml/intro)-val.

## <a name="version-dependencies"></a>Verziók függőségei

**`AutoML` függőségek az újabb csomag-verziók megtörésének kompatibilitásával**. Az SDK verziójának 1.13.0 követően a modellek nem töltődnek be a régebbi SDK-k esetében, mert a korábbi csomagokban rögzített régebbi verziók `AutoML` és a ma rögzített újabb verziók inkompatibilisek.

A rendszer hibákat vár, például:

* A modul nem talált hibákat, például

  `No module named 'sklearn.decomposition._truncated_svd`

* Importálási hibák, például

  `ImportError: cannot import name 'RollingOriginValidator'`,
* Attribútum-hibák, például,

  `AttributeError: 'SimpleImputer' object has no attribute 'add_indicator`

A felbontások az `AutoML` SDK-képzés verziójától függenek:

* Ha az `AutoML` SDK-képzés verziója meghaladja a 1.13.0-t, akkor a és a is szükséges `pandas == 0.25.1` `scikit-learn==0.22.1` .

    * Ha a verzió nem egyezik, frissítse a scikit-Learn és/vagy a pandák verziót a megfelelő verzióra a következővel:

      ```bash
          pip install --upgrade pandas==0.25.1
          pip install --upgrade scikit-learn==0.22.1
      ```

* Ha az `AutoML` SDK-képzés verziója kisebb vagy egyenlő, mint a 1.12.0, akkor a és a értékre van szüksége `pandas == 0.23.4` `sckit-learn==0.20.3` .
  * Ha a verzió nem egyezik, a scikit-Learn és/vagy a pandák verzióját a következővel kell megjavítania:
  
    ```bash
      pip install --upgrade pandas==0.23.4
      pip install --upgrade scikit-learn==0.20.3
    ```

## <a name="setup"></a>Beállítás

`AutoML` a csomagok változásai a 1.0.76 verziótól kezdve az új verzióra való frissítés előtt el kell távolítani az előző verziót.

* **`ImportError: cannot import name AutoMLConfig`**

    Ha ezt a hibát a v 1.0.76 v 1.0.76 vagy újabb verzióra való frissítés után tapasztalja, hárítsa el a hibát a következő parancs futtatásával: `pip uninstall azureml-train automl` és `pip install azureml-train-automl` . A automl_setup. cmd parancsfájl ezt automatikusan elvégzi.

* **automl_setup sikertelen**

  * Windows rendszeren futtasson automl_setup egy Anaconda-parancssorból. [Telepítse a Miniconda](https://docs.conda.io/en/latest/miniconda.html).

  * Győződjön meg arról, hogy a Conda 64 bites verziójának 4.4.10 vagy újabb verziója telepítve van. A bitet a paranccsal lehet megtekinteni `conda info` . A legyen `platform` `win-64` Windows vagy Mac rendszerű `osx-64` . A verzió vizsgálatához használja a parancsot `conda -V` . Ha telepítve van egy korábbi verziója, a paranccsal frissítheti a parancsot: `conda update conda` . A 32 bites ellenőrzéséhez futtassa a következőt: 

  * Győződjön meg arról, hogy a Conda telepítve van. 

  * Linux `gcc: error trying to exec 'cc1plus'`

    1. Ha a `gcc: error trying to exec 'cc1plus': execvp: No such file or directory` hiba észlelhető, telepítse a GCC Build-eszközöket a Linux-disztribúcióhoz. Ubuntu esetén például használja az parancsot `sudo apt-get install build-essential` .

    1. Új Conda-környezet létrehozásához adjon meg egy új nevet az első paraméterként automl_setup. Megtekintheti a meglévő Conda-környezeteket `conda env list` , és eltávolíthatja őket a használatával `conda env remove -n <environmentname>` .

* a **automl_setup_linux. sh sikertelen**: ha a automl_setup_linus. sh sikertelen Ubuntu Linux a következő hibával:`unable to execute 'gcc': No such file or directory`

  1. Győződjön meg arról, hogy a 53-es és a 80-es kimenő portok engedélyezve vannak. Egy Azure-beli virtuális gépen ezt elvégezheti a Azure Portal a virtuális gép kiválasztásával, majd a **hálózatkezelés** elemre kattintva.
  1. Futtassa a parancsot: `sudo apt-get update`
  1. Futtassa a parancsot: `sudo apt-get install build-essential --fix-missing`
  1. Futtatás `automl_setup_linux.sh` újra

* a **Configuration. ipynb sikertelen**:

  * A helyi Conda először győződjön meg arról, hogy a `automl_setup` sikeresen futott.
  * Győződjön meg arról, hogy a subscription_id helyes. Keresse meg a subscription_id a Azure Portal a minden szolgáltatás, majd az előfizetések lehetőség kiválasztásával. A (z) "<" és a ">" karakterek nem szerepelhetnek a subscription_id értékben. Például `subscription_id = "12345678-90ab-1234-5678-1234567890abcd"` érvényes a formátuma.
  * Győződjön meg arról, hogy a közreműködő vagy a tulajdonos hozzáférése van az előfizetéshez.
  * Győződjön meg arról, hogy a régió a támogatott régiók egyike:,,,,,, `eastus2` `eastus` `westcentralus` `southeastasia` `westeurope` `australiaeast` `westus2` , `southcentralus` .
  * A Azure Portal használatával győződjön meg arról, hogy a régióhoz fér hozzá.
  
* **Workspace.from_config sikertelen**:

  Ha a hívás `ws = Workspace.from_config()` sikertelen:

  1. Győződjön meg arról, hogy a Configuration. ipynb jegyzetfüzet sikeresen futott.
  1. Ha a jegyzetfüzet olyan mappából fut, amely nem a `configuration.ipynb` futtatott mappában található, másolja a aml_config mappát, és a fájl config.jsaz új mappába. Workspace.from_config beolvassa az config.jsa jegyzetfüzet mappájába vagy a szülőmappa mappájába.
  1. Ha új előfizetést, erőforráscsoportot, munkaterületet vagy régiót használ, győződjön meg arról, hogy a `configuration.ipynb` jegyzetfüzetet újra futtatja. config.jsközvetlen módosítása csak akkor működik, ha a munkaterület már létezik a megadott erőforrás-csoportban a megadott előfizetésben.
  1. Ha módosítani szeretné a régiót, módosítsa a munkaterületet, az erőforráscsoportot vagy az előfizetést. `Workspace.create` a nem hoz létre vagy frissít egy munkaterületet, ha már létezik, még akkor is, ha a megadott régió eltér.

## <a name="tensorflow"></a>TensorFlow

Az SDK-ból származó 1.5.0-es verziótól kezdve az automatikus gépi tanulás nem telepíti a TensorFlow-modelleket alapértelmezés szerint. A TensorFlow telepítéséhez és az automatikus ML-kísérletekhez való használatához telepítse a-t a következőn `tensorflow==1.12.0` keresztül: `CondaDependencies` .

```python
  from azureml.core.runconfig import RunConfiguration
  from azureml.core.conda_dependencies import CondaDependencies
  run_config = RunConfiguration()
  run_config.environment.python.conda_dependencies = CondaDependencies.create(conda_packages=['tensorflow==1.12.0'])
```

## <a name="numpy-failures"></a>NumPy hibák

* **`import numpy` nem sikerül a Windows** rendszerben: egyes Windows-környezetekben a NumPy a legújabb Python-verzió 3.6.8 való betöltésekor hiba jelenik meg. Ha ezt a problémát látja, próbálkozzon a Python-verzió 3.6.7.

* **`import numpy` sikertelen**: az automatikus ml Conda-környezetben keresse meg a TensorFlow verzióját. A támogatott verziók a következők: < 1,13. Távolítsa el a TensorFlow a környezetből, ha a verzió >= 1,13.

A TensorFlow és az Eltávolítás verzióját a következőképpen tekintheti meg:

  1. Indítsa el a parancssort, aktiválja a Conda-környezetet, amelyben az automatikus ml-csomagok telepítve vannak.
  1. Adja meg `pip freeze` és keresse meg `tensorflow` , ha található, a felsorolt verziónak < 1,13
  1. Ha a felsorolt verzió nem támogatott verziójú, a `pip uninstall tensorflow` parancs-rendszerhéjban írja be az y értéket a megerősítéshez.

## `jwt.exceptions.DecodeError`

Pontos hibaüzenet: `jwt.exceptions.DecodeError: It is required that you pass in a value for the "algorithms" argument when calling decode()` .

Az SDK-verziók <= 1.17.0 esetében a telepítés a PyJWT nem támogatott verzióját eredményezheti. Győződjön meg arról, hogy az automatikus ml Conda-környezet PyJWT verziója támogatott verzió. Ez a PyJWT verziója < 2.0.0.

A PyJWT verzióját a következőképpen lehet megtekinteni:

1. Indítson el egy parancssort, és aktiválja a Conda-környezetet, amelyben az automatikus ML-csomagok telepítve vannak.

1. Adja meg `pip freeze` és keresse meg `PyJWT` , ha található, a felsorolt verziónak < 2.0.0 kell lennie

Ha a felsorolt verzió nem támogatott verzió:

1. Érdemes lehet a AutoML SDK legújabb verziójára frissíteni: `pip install -U azureml-sdk[automl]`

1. Ha ez nem életképes, távolítsa el a PyJWT a környezetből, és telepítse a megfelelő verziót a következőképpen:

    1. `pip uninstall PyJWT` a parancs-rendszerhéjban, és adja meg `y` a megerősítést.
    1. Telepítés a használatával `pip install 'PyJWT<2.0.0'` .
  
## <a name="databricks"></a>Databricks
Lásd: [AUTOMATIZÁLT ml-kísérlet konfigurálása a Databricks](how-to-configure-databricks-automl-environment.md#troubleshooting)használatával.

## <a name="forecasting-r2-score-is-always-zero"></a>Az előrejelzési R2 pontszáma mindig nulla

 Ez a probléma akkor fordul elő, ha a betanítási adatgyűjtési adatsorozat az utolsó `n_cv_splits`  +  adatpontok esetében ugyanazt az értéket tartalmazza `forecasting_horizon` .

Ha ez a minta várható az idősorozatban, átválthatja az elsődleges metrikát **normalizált, legfelső szintű négyzetes hibára**.

## <a name="failed-deployment"></a>Sikertelen üzembe helyezés

 A (z) <= 1.18.0 verziók esetében a központi telepítéshez létrehozott alaprendszerkép a következő hiba miatt meghiúsulhat: `ImportError: cannot import name cached_property from werkzeug` .

  A probléma megoldásához a következő lépések használhatók:

  1. A modell csomag letöltése
  1. A csomag kicsomagolása
  1. Üzembe helyezés a kibontott eszközök használatával

## <a name="azure-functions-application"></a>Azure Functions alkalmazás
  
  Az automatikus ML jelenleg nem támogatja Azure Functions alkalmazásokat. 

## <a name="sample-notebook-failures"></a>Jegyzetfüzet-hibák mintája

 Ha egy minta jegyzetfüzet hibát jelez, a tulajdonság, a metódus vagy a könyvtár nem létezik:

* Győződjön meg arról, hogy a megfelelő kernel van kiválasztva a Jupyter Notebook. A kernel a notebook oldal jobb felső sarkában jelenik meg. Az alapértelmezett érték *azure_automl*. A rendszer a rendszermagot a jegyzetfüzet részeként menti. Ha új Conda-környezetre vált, ki kell választania az új kernelt a jegyzetfüzetben.

  * Azure Notebooks esetén a Python 3,6-es értéknek kell lennie.
  * Helyi Conda környezetekben a automl_setupban megadott Conda-környezeti nevet kell megadni.

* Annak biztosítása érdekében, hogy a jegyzetfüzet a használt SDK-verzióhoz legyen,
  * Az SDK verziójának ellenőrzéséhez hajtson végre `azureml.core.VERSION` egy Jupyter notebook cellát.
  * A minta jegyzetfüzetek előző verzióját a GitHubról töltheti le a következő lépésekkel:
    1. Kattintson a `Branch` gombra
    1. Navigáljon a `Tags` lapra
    1. Válassza ki a verziót

## <a name="next-steps"></a>Következő lépések

+ További információ a [regressziós modell automatikus gépi tanulással való betanításáról](tutorial-auto-train-models.md) , illetve a [távoli erőforrásokon való automatikus gépi tanulás használatával történő](how-to-auto-train-remote.md)betanításról.

+ További információ a [modellek telepítéséről és helyéről](how-to-deploy-and-where.md).
