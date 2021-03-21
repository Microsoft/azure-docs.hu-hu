---
title: 'Python-szkript végrehajtása: modul-hivatkozás'
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan futtathatja a Python-kód futtatását a Azure Machine Learning Designer Python-szkriptek moduljának használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.custom: devx-track-python
author: likebupt
ms.author: keli19
ms.date: 01/02/2021
ms.openlocfilehash: 6003ca9156d8553604d7ebbf94c5c3373d077f0f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102500699"
---
# <a name="execute-python-script-module"></a>Python parancsfájl-modul végrehajtása

Ez a cikk a Python parancsfájl-futtatási modult ismerteti Azure Machine Learning Designerben.

Ez a modul a Python-kód futtatására használható. További információ a Python architektúráról és tervezési alapelveiről: [a Python-kód futtatása Azure Machine learning Designerben](../how-to-designer-python.md).

A Python használatával olyan feladatokat hajthat végre, amelyeket a meglévő modulok nem támogatnak, például a következőket:

+ Az adatmegjelenítés a használatával `matplotlib` .
+ Python-kódtárak használata az adatkészletek és modellek enumerálásához a munkaterületen.
+ Az adatok [importálása](./import-data.md) modul által nem támogatott forrásokból származó adatok olvasása, betöltése és módosítása.
+ Futtasson saját mély tanulási kódot. 

## <a name="supported-python-packages"></a>Támogatott Python-csomagok

A Azure Machine Learning a Python anaconda-eloszlását használja, amely számos gyakori segédprogramot tartalmaz az adatfeldolgozáshoz. Az anaconda verzióját automatikusan frissíteni fogjuk. A jelenlegi verzió:
 -  Anaconda 4.5 + Distribution for Python 3,6 

A teljes listát az [előre telepített Python-csomagok](#preinstalled-python-packages)című szakaszban találja.

Ha olyan csomagokat szeretne telepíteni, amelyek nincsenek az előre telepített listában (például *scikit-misc*), adja hozzá a következő kódot a parancsfájlhoz: 

```python
import os
os.system(f"pip install scikit-misc")
```

A következő kód használatával telepíthet csomagokat a jobb teljesítmény érdekében, különösen a következtetéshez:
```python
import importlib.util
package_name = 'scikit-misc'
spec = importlib.util.find_spec(package_name)
if spec is None:
    import os
    os.system(f"pip install scikit-misc")
```

> [!NOTE]
> Ha a folyamat több olyan Python parancsfájl-modult tartalmaz, amelyek olyan csomagokat igényelnek, amelyek nem az előre telepített listában vannak, telepítse a csomagokat az egyes modulokban.

> [!WARNING]
> A Excute Python parancsfájl-modulja nem támogatja olyan csomagok telepítését, amelyek olyan további natív kódtárak függenek, mint a "apt-get", például a Java, a PyODBC és az etc. Ennek az az oka, hogy ezt a modult egy egyszerű, csak a Python előre telepített és nem rendszergazdai engedéllyel rendelkező környezetben hajtja végre.  

## <a name="access-to-current-workspace-and-registered-datasets"></a>Hozzáférés az aktuális munkaterülethez és a regisztrált adatkészletekhez

A következő mintakód a munkaterületén [regisztrált adatkészletekhez](../how-to-create-register-datasets.md) való hozzáféréshez használható:

```Python
def azureml_main(dataframe1 = None, dataframe2 = None):

    # Execution logic goes here
    print(f'Input pandas.DataFrame #1: {dataframe1}')
    from azureml.core import Run
    run = Run.get_context(allow_offline=True)
    #access to current workspace
    ws = run.experiment.workspace

    #access to registered dataset of current workspace
    from azureml.core import Dataset
    dataset = Dataset.get_by_name(ws, name='test-register-tabular-in-designer')
    dataframe1 = dataset.to_pandas_dataframe()
     
    # If a zip file is connected to the third input port,
    # it is unzipped under "./Script Bundle". This directory is added
    # to sys.path. Therefore, if your zip file contains a Python file
    # mymodule.py you can import it using:
    # import mymodule

    # Return value must be of a sequence of pandas.DataFrame
    # E.g.
    #   -  Single return value: return dataframe1,
    #   -  Two return values: return dataframe1, dataframe2
    return dataframe1,
```

## <a name="upload-files"></a>Fájlok feltöltése
A Python parancsfájl végrehajtása modul támogatja a fájlok feltöltését a [Azure Machine learning PYTHON SDK](/python/api/azureml-core/azureml.core.run%28class%29#upload-file-name--path-or-stream-)használatával.

Az alábbi példa bemutatja, hogyan tölthet fel egy képfájlt a Python parancsfájl végrehajtása modulban:

```Python

# The script MUST contain a function named azureml_main,
# which is the entry point for this module.

# Imports up here can be used to
import pandas as pd

# The entry point function must have two input arguments:
#   Param<dataframe1>: a pandas.DataFrame
#   Param<dataframe2>: a pandas.DataFrame
def azureml_main(dataframe1 = None, dataframe2 = None):

    # Execution logic goes here
    print(f'Input pandas.DataFrame #1: {dataframe1}')

    from matplotlib import pyplot as plt
    plt.plot([1, 2, 3, 4])
    plt.ylabel('some numbers')
    img_file = "line.png"
    plt.savefig(img_file)

    from azureml.core import Run
    run = Run.get_context(allow_offline=True)
    run.upload_file(f"graphics/{img_file}", img_file)

    # Return value must be of a sequence of pandas.DataFrame
    # For example:
    #   -  Single return value: return dataframe1,
    #   -  Two return values: return dataframe1, dataframe2
    return dataframe1,
}
```

A folyamat futásának befejezése után a rendszerképet a modul jobb oldali paneljén tekintheti meg.

> [!div class="mx-imgBorder"]
> ![Feltöltött rendszerkép előnézete](media/module/upload-image-in-python-script.png)

## <a name="how-to-configure-execute-python-script"></a>A Python-szkript végrehajtásának konfigurálása

A Python-szkript végrehajtása modul olyan minta Python-kódot tartalmaz, amelyet kiindulási pontként használhat. A Python parancsfájl-végrehajtási moduljának konfigurálásához adjon meg egy bemeneti és Python-kódot a Python- **szkript** szövegmezőben való futtatáshoz.

1. Adja hozzá a **Python-szkript végrehajtása** modult a folyamathoz.

2. Adja hozzá a (z) elemet a **DataSet1 elemet** bármely olyan adatkészlethez, amelyet a bemenethez szeretne használni. Hivatkozzon erre az adatkészletre a Python-szkriptben **DataFrame1** néven.

    Az adatkészlet használata nem kötelező. Akkor használja, ha a Python használatával kívánja előállítani az adatgyűjtést, vagy a Python-kód használatával importálja az adategységeket közvetlenül a modulba.

    Ez a modul támogatja egy második adatkészlet hozzáadását a **Dataset2**-on. Hivatkozzon a Python-szkript második adatkészletére **DataFrame2**.

    Az Azure Machine Learningban tárolt adatkészletek automatikusan a Panda adatkeretbe lesznek konvertálva, ha a modul betöltődik.

    ![Python bemeneti térképének végrehajtása](media/module/python-module.png)

4. Ha új Python-csomagokat vagy-kódokat szeretne felvenni, akkor az ezeket az egyéni erőforrásokat tartalmazó tömörített fájlt a **parancsfájl-előfizetői** porthoz kell kötni. Ha a szkript mérete meghaladja a 16 KB-ot, a **parancsfájl** -létrehozási port használatával elkerülhető, hogy a *commandline érték meghaladja a 16597 karakteres korlátot*. 

    
    1. A szkriptet és más egyéni erőforrásokat csomagolja egy zip-fájlba.
    1. Töltse fel a zip-fájlt **fájl adatkészletként** a studióba. 
    1. Húzza az adatkészlet modult a Designer authoring oldal bal oldali modul paneljének *adatkészletek* listájából. 
    1. Az adatkészlet moduljának csatlakoztatása a **Python parancsfájl** -modul végrehajtása **parancsfájl-köteg** portjához.
    
    A feltöltött tömörített archívumban található összes fájl használható a folyamat végrehajtása során. Ha az Archívum tartalmaz egy címtár-struktúrát, a rendszer megőrzi a struktúrát.
 
    > [!WARNING]
    > **Ne** használja az **alkalmazást** mappa vagy parancsfájl neveként, mert az **alkalmazás** a beépített szolgáltatások számára fenntartott szó. Más névtereket is használhat, például a következőt: `app123` .
   
    A következő egy parancsfájl-köteg példa, amely egy Python-parancsfájlt és egy txt-fájlt tartalmaz:
      
    > [!div class="mx-imgBorder"]
    > ![Példa parancsfájl-csomagra](media/module/python-script-bundle.png)  

    A következő tartalma `my_script.py` :

    ```python
    def my_func(dataframe1):
    return dataframe1
    ```
    Az alábbi mintakód azt mutatja be, hogyan használhatók a fájlok a parancsfájl-csomagban:    

    ```python
    import pandas as pd
    from my_script import my_func
 
    def azureml_main(dataframe1 = None, dataframe2 = None):
 
        # Execution logic goes here
        print(f'Input pandas.DataFrame #1: {dataframe1}')
 
        # Test the custom defined python function
        dataframe1 = my_func(dataframe1)
 
        # Test to read custom uploaded files by relative path
        with open('./Script Bundle/my_sample.txt', 'r') as text_file:
            sample = text_file.read()
    
        return dataframe1, pd.DataFrame(columns=["Sample"], data=[[sample]])
    ```

5. A **Python-szkript** szövegmezőbe írja be vagy illessze be az érvényes Python-szkriptet.

    > [!NOTE]
    >  A szkript írásakor körültekintően járjon el. Győződjön meg arról, hogy nincsenek szintaktikai hibák, például nem deklarált változók vagy nem importált modulok vagy függvények használata. Ügyeljen az előre telepített modulok listájára. A nem felsorolt modulok importálásához telepítse a megfelelő csomagokat a parancsfájlba, például:
    >  ``` Python
    > import os
    > os.system(f"pip install scikit-misc")
    > ```
    
    A **Python-szkript** szövegmezője előre fel van töltve a megjegyzésekben található utasításokkal, és az adathozzáféréshez és a kimenethez tartozó mintakód. Szerkesztenie vagy cserélnie kell ezt a kódot. A behúzással és a burkolattal kapcsolatos Python-konvenciók követése:

    + A szkriptnek tartalmaznia kell egy nevű függvényt `azureml_main` , amely a modul belépési pontja.
    + A belépési pont függvénynek két bemeneti argumentummal kell rendelkeznie, `Param<dataframe1>` és `Param<dataframe2>` akkor is, ha ezek az argumentumok nem használatosak a parancsfájlban.
    + A harmadik bemeneti porthoz csatlakoztatott tömörített fájlok kibontása és tárolása a könyvtárban `.\Script Bundle` történik, amely a Pythonhoz is hozzá van adva `sys.path` . 

    Ha a. zip fájl tartalmaz `mymodule.py` , importálja azt a használatával `import mymodule` .

    Két adatkészletet lehet visszaadni a tervezőnek, amelynek típusú sorozatot kell megadni `pandas.DataFrame` . A Python-kódban más kimeneteket is létrehozhat, amelyeket közvetlenül az Azure Storage-ba írhat.

    > [!WARNING]
    > **Nem** ajánlott egy adatbázishoz vagy más külső tárolóhoz csatlakozni a **Python parancsfájl-modul végrehajtása** során. Használhatja az [adatimportálási modult](./import-data.md) , és [exportálhatja az adatmodult](./export-data.md)     

6. A folyamat elküldése.

    Ha a modul elkészült, a várt módon ellenőrizze a kimenetet.

    Ha a modul nem sikerült, néhány hibaelhárítást is végre kell hajtania. Válassza ki a modult, és nyissa meg a jobb oldali ablaktábla **kimenetek és naplók** elemét. Nyissa meg **70_driver_log.txt** és keresse meg **a azureml_main**, majd megtalálhatja, hogy melyik sor okozta a hibát. Például a "fájl"/tmp/tmp01_ID/user_script. másolt ", a 17. sor azureml_main" "azt jelzi, hogy a hiba a Python-szkript 17 sorában történt.

## <a name="results"></a>Results (Eredmények)

A beágyazott Python-kód alapján történő számítások eredményeit a következőként kell megadni `pandas.DataFrame` , amely automatikusan a Azure Machine learning adatkészlet formátumára lesz konvertálva. Ezután az eredményeket a folyamat más moduljaival is használhatja.

A modul két adatkészletet ad vissza:  
  
+ Az **eredmények adatkészlet 1**, a Python-szkriptek első visszaadott pandák adatkerete által definiált érték.

+ A **2. eredmény adatkészlete**, amelyet a második visszaadott pandák adatkeret definiál egy Python-parancsfájlban.

## <a name="preinstalled-python-packages"></a>Előre telepített Python-csomagok
Az előre telepített csomagok a következők:
-    adal = = 1.2.2
-    applicationinsights = = 0.11.9
-    attrs = = 19.3.0
-    Azure – Common = = 1.1.25
-    Azure-Core = = 1.3.0
-    Azure-graphrbac = = 0.61.1
-    Azure-Identity = = 1.3.0
-    Azure-mgmt-Authorization = = 0.60.0
-    Azure-mgmt-containerregistry = = 2.8.0
-    Azure-mgmt-kulcstartó = = 2.2.0
-    Azure-mgmt-Resource = = 8.0.1
-    Azure-mgmt-Storage = = 8.0.0
-    Azure-Storage – blob = = 1.5.0
-    Azure-Storage – Common = = 1.4.2
-    azureml-Core = = 1.1.5.5
-    azureml-adatelőkészítés-Native = = 14.1.0
-    azureml-adatelőkészítés = = 1.3.5
-    azureml – alapértékek = = 1.1.5.1
-    azureml-Designer-klasszikus-modulok = = 0.0.118
-    azureml-Designer-Core = = 0.0.31
-    azureml-Designer-Internal = = 0.0.18
-    azureml-Model-Management-SDK = = 1.0.1 B6. post1
-    azureml – folyamat – mag = = 1.1.5
-    azureml-telemetria = = 1.1.5.3
-    backports. tempfile = = 1.0
-    backports. weakref = = 1.0. post1
-    boto3 = = 1.12.29
-    botocore = = 1.15.29
-    cachetools = = 4.0.0
-    2019.11.28 = =
-    cffi = = 1.12.3
-    chardet = = 3.0.4
-    Kattintson = = 7.1.1
-    cloudpickle = = 1.3.0
-    configparser = = 3.7.4
-    contextlib2 = = 0.6.0. post1
-    titkosítás = = 2.8
-    feldolgozó = = 0.10.0
-    Kapros = = 0.3.1.1
-    disztribúció = = 1.4.0
-    Docker = = 4.2.0
-    docutils = = 0.15.2
-    dotnetcore2 = = 2.1.13
-    lombik = = 1.0.3
-    fusepy = = 3.0.1
-    gensim = = 3.8.1
-    Google-API-Core = = 1.16.0
-    Google-Auth = = 1.12.0
-    Google – Cloud-Core = = 1.3.0
-    Google-Cloud-Storage = = 1.26.0
-    Google-folytatható – média = = 0.5.0
-    googleapis-Common-proto = = 1.51.0
-    gunicorn = = 19.9.0
-    IDNA = = 2.9
-    kiegyensúlyozatlan – Learn = = 0.4.3
-    isodate = = 0.6.0
-    itsdangerous = = 1.1.0
-    jeepney = = 0.4.3
-    jinja2 = = 2.11.1
-    jmespath = = 0.9.5
-    joblib = = 0.14.0
-    JSON-naplózás-a-ről = = 0.2
-    jsonpickle = = 1.3
-    jsonschema = = 3.0.1
-    kiwisolver = = 1.1.0
-    liac-arff = = 2.4.0
-    lightgbm = = 2.2.3
-    markupsafe = = 1.1.1
-    matplotlib = = 3.1.3
-    több – itertools = = 6.0.0
-    msal-Extensions = = 0.1.3
-    msal = = 1.1.0
-    msrest = = 0.6.11
-    msrestazure = = 0.6.3
-    NDG-httpsclient = = 0.5.1
-    nimbusml = = 1.6.1
-    NumPy = = 1.18.2
-    oauthlib = = 3.1.0
-    Pandák = = 0.25.3
-    pathspec = = 0.7.0
-    pip = = 20.0.2
-    portalocker = = 1.6.0
-    protopuf = = 3.11.3
-    pyarrow = = 0.16.0
-    pyasn1 – modulok = = 0.2.8
-    pyasn1 = = 0.4.8
-    pycparser = = 2.20
-    pycryptodomex = = 3.7.3
-    pyjwt = = 1.7.1
-    pyopenssl = = 19.1.0
-    pyparsing = = 2.4.6
-    pyrsistent = = 0.16.0
-    Python-dateutil = = 2.8.1
-    pytz = = 2019.3
-    kérelmek – oauthlib = = 1.3.0
-    kérelmek = = 2.23.0
-    RSA = = 4.0
-    ruamel. YAML = = 0.15.89
-    s3transfer = = 0.3.3
-    scikit – Learn = = 0.22.2
-    SciPy = = 1.4.1
-    secretstorage = = 3.1.2
-    setuptools = = 46.1.1. post20200323
-    hat = = 1.14.0
-    intelligens – nyitott = = 1.10.0
-    urllib3 = = 1.25.8
-    WebSocket – Client = = 0.57.0
-    Werkzeug = = 0.16.1
-    Wheel = = 0.34.2

## <a name="next-steps"></a>Következő lépések

Tekintse [meg a Azure Machine learning elérhető modulok készletét](module-reference.md) .