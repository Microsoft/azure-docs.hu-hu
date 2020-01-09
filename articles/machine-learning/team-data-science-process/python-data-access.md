---
title: Adatkészletek elérése a Python ügyféloldali függvénytárával – csoportos adatelemzési folyamat
description: A Python ügyféloldali kódtár telepítése és használata a Azure Machine Learning adatok biztonságos eléréséhez és kezeléséhez a helyi Python-környezetből.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 241f804b0519fd744e8b980b2d311a72680aafad
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75427388"
---
# <a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>Hozzáférés az adathalmazokhoz Python segítségével, az Azure Machine Learning Python ügyfélkönyvtárat használva
Microsoft Azure Machine Learning Python ügyféloldali kódtár előzetes verziója lehetővé teszi a Azure Machine Learning adatkészletek biztonságos elérését egy helyi Python-környezetből, és lehetővé teszi a munkaterületen lévő adatkészletek létrehozását és kezelését.

Ez a témakör a következőket ismerteti:

* a Machine Learning Python ügyféloldali kódtár telepítése
* adatkészletek elérése és feltöltése, beleértve a helyi Python-környezetből Azure Machine Learning adatkészletekhez való hozzáférés engedélyezésének utasításait is.
* közbenső adatkészletek elérése kísérletekből
* a Python ügyféloldali kódtár használatával enumerálhatja az adatkészleteket, elérheti a metaadatokat, beolvashatja az adatkészlet tartalmát, új adatkészleteket hozhat létre, és frissítheti a meglévő adatkészleteket.

## <a name="prerequisites"></a>Előfeltételek
A Python ügyféloldali kódtár a következő környezetekben lett tesztelve:

* Windows, Mac és Linux
* Python 2,7, 3,3 és 3,4

A következő csomagoktól függ:

* kérelmek
* Python – dateutil
* Pandák

Javasoljuk, hogy olyan Python-disztribúciót használjon, mint például a [anaconda](http://continuum.io/downloads#all) vagy a [lombkorona](https://store.enthought.com/downloads/), amely a Pythonhoz, a IPython-hoz és a fent felsorolt három csomaghoz készült. Bár a IPython nem feltétlenül szükséges, ez nagyszerű környezet az adatkezeléshez és az interaktív megjelenítéshez.

### <a name="installation"></a>A Azure Machine Learning Python ügyféloldali kódtár telepítése
A jelen témakörben ismertetett feladatok végrehajtásához a Azure Machine Learning Python ügyféloldali függvénytárát is telepíteni kell. A [Python-csomag indexében](https://pypi.python.org/pypi/azureml)érhető el. A Python-környezetbe való telepítéséhez futtassa a következő parancsot a helyi Python-környezetből:

    pip install azureml

Azt is megteheti, hogy letölti és telepíti a [githubon](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python)található forrásokból.

    python setup.py install

Ha a git telepítve van a gépen, a pip használatával közvetlenül is telepítheti a git-tárházból:

    pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git


## <a name="datasetAccess"></a>Kódrészletek használata az adatkészletek eléréséhez
A Python ügyféloldali kódtár programozott hozzáférést biztosít a meglévő adatkészletekhez a futtatott kísérletekből.

A Azure Machine Learning Studio (klasszikus) webes felületen létrehozhat kódrészleteket, amelyek tartalmazzák az összes szükséges információt, amelyekkel letöltheti és deszerializálhatja az adatkészleteket a helyi gépen lévő pandák DataFrame-objektumokként.

### <a name="security"></a>Adathozzáférés biztonsága
A Python ügyféloldali kódtár számára a Azure Machine Learning Studio (klasszikus) által biztosított kódrészletek tartalmazzák a munkaterület-azonosítót és az engedélyezési jogkivonatot. Ezek teljes hozzáférést biztosítanak a munkaterülethez, és védelemmel kell ellátni, például jelszóval.

Biztonsági okokból a kódrészlet funkció csak olyan felhasználók számára érhető el, akiknek a szerepkörük **tulajdonosként** van beállítva a munkaterületen. A szerepkör a **felhasználók** lapon, a **Beállítások**területen jelenik meg Azure Machine learning Studio (klasszikus).

![Biztonság][security]

Ha a szerepkör nem **tulajdonosként**van beállítva, kérheti, hogy a rendszer visszahívja tulajdonosként, vagy kérje meg a munkaterület tulajdonosát, hogy megadja a kódrészletet.

Az engedélyezési jogkivonat beszerzéséhez a következők egyikét teheti:

* Kérjen meg egy jogkivonatot a tulajdonostól. A tulajdonosok a Azure Machine Learning Studio (klasszikus) munkaterületének beállítások lapján érhetik el az engedélyezési jogkivonatokat. A bal oldali ablaktáblában válassza a **Beállítások** lehetőséget, majd kattintson az **engedélyezési tokenek** elemre az elsődleges és másodlagos tokenek megtekintéséhez. Bár az elsődleges vagy a másodlagos engedélyezési token használható a kódrészletben, ajánlott, hogy a tulajdonosok csak a másodlagos engedélyezési jogkivonatokat használják.

   ![Engedélyezési jogkivonatok](./media/python-data-access/ml-python-access-settings-tokens.png)

* Kérje, hogy népszerűsítse a tulajdonos szerepkörét. Ehhez a munkaterület aktuális tulajdonosának először el kell távolítania a munkaterületről, majd újra meg kell hívnia a tulajdonosként.

Ha a fejlesztők megszerezték a munkaterület-azonosítót és az engedélyezési jogkivonatot, akkor a szerepkörtől függetlenül hozzáférhetnek a munkaterülethez a kódrészlet használatával.

Az engedélyezési jogkivonatok kezelése az **engedélyezési JOGkivonatok** lapon, a **Beállítások**területen történik. Újra létrehozhatja őket, de ez az eljárás visszavonja az előző tokenhez való hozzáférést.

### <a name="accessingDatasets"></a>Adatkészletek elérése helyi Python-alkalmazásból
1. Machine Learning Studio (klasszikus) területen kattintson a bal oldali navigációs sávban található **adatkészletek** elemre.
2. Válassza ki az elérni kívánt adatkészletet. Bármelyik adatkészletet kiválaszthatja a **saját ADATkészletek** listából vagy a **minták** listából.
3. Az alsó eszköztáron kattintson az **adatelérési kód előállítása**elemre. Ha az adatformátum nem kompatibilis a Python ügyféloldali függvénytárával, ez a gomb le lesz tiltva.
   
    ![Adathalmazok][datasets]
4. Válassza ki a kódrészletet a megjelenő ablakban, és másolja a vágólapra.
   
    ![Adatelérési kód előállítása gomb][dataset-access-code]
5. Illessze be a kódot a helyi Python-alkalmazás notebookján.
   
    ![Kód beillesztése a jegyzetfüzetbe][ipython-dataset]

## <a name="accessingIntermediateDatasets"></a>A köztes adatkészletek elérése Machine Learning kísérletekből
Machine Learning Studio (klasszikus) kísérlet után a közbenső adatkészletek a modulok kimeneti csomópontjairól is elérhetők. A köztes adatkészletek olyan adatokat hoztak létre, amelyek a modell eszköz futtatásakor köztes lépésekhez használatosak.

A köztes adatkészletek akkor érhetők el, ha az adatformátum kompatibilis a Python ügyféloldali kódtár használatával.

A következő formátumok támogatottak (az állandók a `azureml.DataTypeIds` osztályban találhatók):

* PlainText
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

A formátumot a modul kimeneti csomópontjának fölé húzva határozhatja meg. Megjelenik a csomópont neve mellett egy elemleírásban.

Néhány modul, például a [felosztott][split] modul, kimenete `Dataset`nevű formátumra, amelyet a Python ügyféloldali kódtár nem támogat.

![Adatkészlet formátuma][dataset-format]

Egy konverziós modult kell használnia, például a CSV-re történő [konvertálást][convert-to-csv], hogy a kimenet támogatott formátumú legyen.

![GenericCSV formátuma][csv-format]

Az alábbi lépések egy kísérletet létrehozó példát mutatnak be, és a köztes adatkészlethez férnek hozzá.

1. Hozzon létre egy új kísérletet.
2. Helyezzen be egy **felnőtt népszámlálás-bevétel bináris besorolási adatkészlet** modulját.
3. Szúrjon be egy [felosztott][split] modult, és csatlakoztassa a bemenetét az adatkészlet moduljának kimenetéhez.
4. Helyezzen be egy [Konvertálás CSV][convert-to-csv] -modulba, és csatlakoztassa a bemenetét az egyik [felosztott][split] modul kimenetéhez.
5. Mentse a kísérletet, futtassa, majd várjon, amíg a rendszer befejeződik.
6. Kattintson a kimenet csomópontra az [átalakítás CSV-][convert-to-csv] modulba.
7. Amikor megjelenik a helyi menü, válassza az **adatelérési kód létrehozása**lehetőséget.
   
    ![Helyi menü][experiment]
8. Válassza ki a kódrészletet, és másolja a vágólapra a megjelenő ablakban.
   
    ![Hozzáférési kód előállítása a helyi menüből][intermediate-dataset-access-code]
9. Illessze be a kódot a jegyzetfüzetbe.
   
    ![Kód beillesztése jegyzetfüzetbe][ipython-intermediate-dataset]
10. Az matplotlib használatával megjelenítheti az adataikat. Ez a kor oszlop hisztogramjában jelenik meg:
    
    ![Hisztogram][ipython-histogram]

## <a name="clientApis"></a>Adatkészletek elérése, olvasása, létrehozása és kezelése a Machine Learning Python ügyféloldali kódtár használatával
### <a name="workspace"></a>Munkaterület
A munkaterület a Python ügyféloldali kódtár belépési pontja. Példány létrehozásához adja meg a `Workspace` osztályt a munkaterület-AZONOSÍTÓval és az engedélyezési jogkivonattal:

    ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
                   authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')


### <a name="enumerate-datasets"></a>Adatkészletek számbavétele
Egy adott munkaterületen lévő összes adatkészlet enumerálása:

    for ds in ws.datasets:
        print(ds.name)

Csak a felhasználó által létrehozott adatkészletek enumerálása:

    for ds in ws.user_datasets:
        print(ds.name)

Csak a példaként szolgáló adatkészletek enumerálása:

    for ds in ws.example_datasets:
        print(ds.name)

Egy adatkészletet a név alapján érhet el (megkülönbözteti a kis-és nagybetűket):

    ds = ws.datasets['my dataset name']

Vagy elérheti az indexben:

    ds = ws.datasets[0]


### <a name="metadata"></a>Metaadatok
Az adatkészletek a tartalom mellett metaadatokat is tartalmaz. (A köztes adatkészletek kivételt képeznek a szabály alól, és nem rendelkeznek metaadatokkal.)

Egyes metaadat-értékeket a felhasználó a létrehozáskor rendel hozzá:

    print(ds.name)
    print(ds.description)
    print(ds.family_id)
    print(ds.data_type_id)

Mások az Azure ML-ben hozzárendelt értékek:

    print(ds.id)
    print(ds.created_date)
    print(ds.size)

A rendelkezésre álló metaadatokkal kapcsolatos további információkért tekintse meg a `SourceDataset` osztályt.

### <a name="read-contents"></a>Tartalom olvasása
A Machine Learning Studio (klasszikus) által megadott kódrészletek automatikusan letöltik és deszerializálják az adatkészletet egy Panda DataFrame objektumba. Ez a `to_dataframe` metódussal történik:

    frame = ds.to_dataframe()

Ha inkább a nyers adatok letöltését szeretné elvégezni, és saját maga hajtja végre a deszerializálást, ez egy lehetőség. Jelenleg ez az egyetlen lehetőség az olyan formátumokra, mint például a "ARFF", amelyet a Python ügyféloldali kódtár nem tud deszerializálni.

A tartalom olvasása szövegként:

    text_data = ds.read_as_text()

A tartalom beolvasása bináris fájlként:

    binary_data = ds.read_as_binary()

Egy streamet is megnyithat a tartalomhoz:

    with ds.open() as file:
        binary_data_chunk = file.read(1000)


### <a name="create-a-new-dataset"></a>Új adatkészlet létrehozása
A Python ügyféloldali kódtár lehetővé teszi adatkészletek feltöltését a Python-programból. Ezek az adatkészletek ezután használhatók a munkaterületen.

Ha az adatai egy pandák DataFrame, használja a következő kódot:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_dataframe(
        dataframe=frame,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Ha az adatai már szerializálva vannak, a következőket használhatja:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_raw_data(
        raw_data=raw_data,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

A Python ügyféloldali kódtár a következő formátumokba tudja szerializálni a pandák DataFrame (az állandók a `azureml.DataTypeIds` osztályban találhatók):

* PlainText
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

### <a name="update-an-existing-dataset"></a>Meglévő adatkészlet frissítése
Ha olyan nevű új adatkészletet próbál feltölteni, amely megegyezik egy meglévő adatkészlettel, ütközési hibát kell kapnia.

Meglévő adatkészlet frissítéséhez először be kell szereznie a meglévő adatkészletre mutató hivatkozást:

    dataset = ws.datasets['existing dataset']

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Ezután a `update_from_dataframe` használatával szerializálhatja és lecserélheti az adatkészlet tartalmát az Azure-ban:

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(frame2)

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Ha más formátumba kívánja szerializálni az adattípust, akkor a választható `data_type_id` paraméter értékét is meg kell adni.

    from azureml import DataTypeIds

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        data_type_id=DataTypeIds.GenericTSV,
    )

    print(dataset.data_type_id) # 'GenericTSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

A `description` paraméter értékének megadásával megadhat egy új leírást.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        description='data up to feb 2015',
    )

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to feb 2015'

A `name` paraméter értékének megadásával új nevet is beállíthat. Mostantól a csak az új név használatával kérdezi le az adatkészletet. A következő kód frissíti az adatfájlokat, a nevet és a leírást.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        name='existing dataset v2',
        description='data up to feb 2015',
    )

    print(dataset.data_type_id)                    # 'GenericCSV'
    print(dataset.name)                            # 'existing dataset v2'
    print(dataset.description)                     # 'data up to feb 2015'

    print(ws.datasets['existing dataset v2'].name) # 'existing dataset v2'
    print(ws.datasets['existing dataset'].name)    # IndexError

A `data_type_id`, `name` és `description` paraméterek nem kötelezőek, és alapértelmezés szerint az előző értékük. A `dataframe` paramétert mindig kötelező megadni.

Ha az adatai már szerializálva vannak, a `update_from_dataframe`helyett használja a `update_from_raw_data`. Ha `dataframe`helyett csak `raw_data`, akkor hasonló módon működik.

<!-- Images -->
[security]:./media/python-data-access/security.png
[dataset-format]:./media/python-data-access/dataset-format.png
[csv-format]:./media/python-data-access/csv-format.png
[datasets]:./media/python-data-access/datasets.png
[dataset-access-code]:./media/python-data-access/dataset-access-code.png
[ipython-dataset]:./media/python-data-access/ipython-dataset.png
[experiment]:./media/python-data-access/experiment.png
[intermediate-dataset-access-code]:./media/python-data-access/intermediate-dataset-access-code.png
[ipython-intermediate-dataset]:./media/python-data-access/ipython-intermediate-dataset.png
[ipython-histogram]:./media/python-data-access/ipython-histogram.png


<!-- Module References -->
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

