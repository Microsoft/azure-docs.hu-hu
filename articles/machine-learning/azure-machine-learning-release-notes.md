---
title: A kiadás újdonsága
titleSuffix: Azure Machine Learning
description: Ismerje meg a pythonos Azure Machine Learning legújabb frissítéseit, valamint a gépi tanulás és adat-előkészítés Python-SDK-ját.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: larryfr
author: BlackMist
ms.date: 02/18/2021
ms.openlocfilehash: 1de495253dacac5aeab7dcff95f74aeed11782a8
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750733"
---
# <a name="azure-machine-learning-release-notes"></a>Azure Machine Learning kibocsátási megjegyzések

Ebben a cikkben a Azure Machine Learning ismerkedünk meg.  A teljes SDK-referenciatartalomért látogasson el a Azure Machine Learning [**Fő SDK for Python referenciaoldalára.**](/python/api/overview/azure/ml/intro)

__RSS-hírcsatorna:__ Az alábbi URL-cím a hírcsatornaolvasóba másolásával és beillesztésével értesítést kap az oldal frissítésével: `https://docs.microsoft.com/api/search/rss?search=%22Azure+machine+learning+release+notes%22&locale=en-us`


## <a name="2021-04-19"></a>2021-04-19

### <a name="azure-machine-learning-sdk-for-python-v1270"></a>Azure Machine Learning SDK for Python 1.27.0
+ **Hibajavítások és fejlesztések**
  + **azureml-core**
    + Az összetevőfeltöltés alapértelmezett időtúllépési értékének felülbírálása a "AZUREML_ARTIFACTS_DEFAULT_TIMEOUT" környezeti változóval.
    + Kijavítottunk egy hibát, amely miatt a ScriptRunConfig környezeti objektumában a Docker-beállítások nem teljesülnek.
    + Adatkészlet particionálásának engedélyezése célhelyre másoláskor.
    + Az OutputDatasetConfig egyéni móddal bővült, amely lehetővé teszi a létrehozott adatkészletek folyamaton keresztüli átadását egy hivatkozási függvényen keresztül. Ezek a támogatási fejlesztések lehetővé teszik a táblázatos particionálást a PRS-hez.
    + Új KubernetesCompute számítási típus hozzáadva az azureml-core-hoz.
  + **azureml-pipeline-core**
    + Egyéni mód hozzáadása az OutputDatasetConfig adatbázishoz, és annak engedélyezése, hogy a felhasználó a létrehozott adatkészleteket folyamatokban egy hivatkozási függvényen keresztül adja át. A fájl elérési útjai támogatják a helyőrzőket. Ezek támogatják a prS táblázatos particionálásának engedélyezéséhez szükséges fejlesztéseket.
    + Új KubernetesCompute számítási típus az azureml-core-hoz.
  + **azureml-pipeline-steps**
    + Új KubernetesCompute számítási típus az azureml-core-hoz.
  + **azureml-synapse**
    + A Spark felhasználói felületi URL-címének frissítése az azureml synapse vezérlőben
  + **azureml-train-automl-client**
    + Az előrejelzési feladat STL-featurizálója mostantól robusztusabb szezonalitás-észlelést használ az idősorozat gyakorisága alapján.
  + **azureml-train-core**
    + Kijavítva a hiba, amely miatt a környezeti objektum docker-beállításai nem teljesülnek.
    + Új KubernetesCompute számítási típus hozzáadása az azureml-core-hoz.


## <a name="2021-04-05"></a>2021-04-05

### <a name="azure-machine-learning-sdk-for-python-v1260"></a>Azure Machine Learning SDK for Python 1.26.0
+ **Hibajavítások és fejlesztések**
  + **azureml-automl-core**
    + Kijavítottunk egy hibát, amely miatt a Naive-modellek ajánlottak az AutoMLStep-futtatásban, és meghiúsulnak a késéssel vagy a gördülőablak-funkciókkal. Ezek a modellek nem lesznek ajánlottak, ha a cél késése vagy a cél gördülőablak-mérete be van állítva.
    +  A konzol kimenete módosult az AutoML-futtatás elküldésekor, hogy a futtatás portálhivatkozását mutassa.
  + **azureml-core**
    + HDFS mód hozzáadva a dokumentációhoz.
    + Támogatás hozzáadva a File Dataset partícióknak a glob struktúra alapján való megértőjéhez.
    + Az AzureML-munkaterülethez társított tároló-beállításjegyzék frissítésének támogatása hozzáadva.
    + A DockerSection – enabled, "shared_volume" és "arguments" alatti elavult környezeti attribútumok a DockerConfiguration részét képezi a RunConfigurationban.
    + Frissített folyamat CLI-klón dokumentációja
    + Frissített portál URI-k a bérlő hitelesítéshez való használatával
    + A kísérlet neve el lett távolítva a futtatott URI-kból az átirányítások elkerülése érdekében 
    + Az URO-kísérlet frissítve lett a kísérletazonosító használatára.
    + Hibajavítások távoli számítás Csatolása az AzureML CLI-hez.
    + A portál URI-i frissítve, hogy tartalmazzák a bérlőt a hitelesítéshez.
    + A kísérlet URI-azonosítója frissítve lett a kísérletazonosító használatára.
  + **azureml-interpret**
    + azureml-interpret frissítve az interpret-community 0.17.0 használatára
  + **azureml-opendatasets**
    + A bemeneti kezdő dátum és a záró dátumtípus érvényesítése és a hiba jelzése, ha nem datetime típusú.
  + **azureml-parallel-run**
    + [Kísérleti funkció] Adja hozzá a paramétert a ParallelRunConfighez, ha meg van adva, a bemeneti adatkészlet(ök) mini kötegekbe lesznek particionálva az által megadott `partition_keys` kulcsokkal. Ehhez az szükséges, hogy az összes bemeneti adatkészlet particionált legyen.
  + **azureml-pipeline-steps**
    + Hibajavítás – a path_on_compute az adatkészlet-konfiguráció letöltésként való átadása során.
    + Az RScriptStep elavult, és a CommandStep parancsokat használja az R-szkriptek folyamatokban való futtatásához. 
    + A EstimatorStep elavult, mert a CommandStep parancs használatával futtatja a gépi tanulási betanításokat (beleértve az elosztott betanításokat is) a folyamatokban.
  + **azureml-sdk**
    + Az python_requires frissítése < 3.9-es verzióra az azureml-sdk-hoz
  + **azureml-train-automl-client**
    +  A konzol kimenete módosult az AutoML-futtatás elküldésekor, hogy a futtatás portálhivatkozását mutassa.
  + **azureml-train-core**
    + A DockerConfiguration és a ScriptRunConfig használata mellett a DockerSection "enabled", "shared_volume" és "arguments" attribútumai elavultak.
    +  A Azure Open Datasets használata MNIST-adathalmaz
    + A HyperDrive hibaüzenetei frissültek.


## <a name="2021-03-22"></a>2021-03-22

### <a name="azure-machine-learning-sdk-for-python-v1250"></a>Azure Machine Learning SDK for Python 1.25.0
+ **Hibajavítások és fejlesztések**
  + **azureml-automl-core**
    +  A konzol kimenete módosult az AutoML-futtatás elküldésekor, hogy a futtatás portálhivatkozását mutassa.
  + **azureml-core**
    + Elkezdi támogatni a tároló-beállításjegyzék frissítését a munkaterülethez az SDK-ban és a CLI-ban
    + A DockerSection "enabled", "shared_volume" és "arguments" attribútumai elavultak a DockerConfiguration és a ScriptRunConfig használata mellett.
    + Frissített folyamat CLI-klón dokumentációja
    + Frissített portál URI-k a bérlő hitelesítéshez való használatával
    + A kísérlet neve el lett távolítva a futtatott URI-kból az átirányítások elkerülése érdekében
    + Az URO-kísérlet frissítve lett a kísérletazonosító használatára.
    + Hibajavítások távoli számítás csatolása az az cli használatával
    + A portál URI-i frissítve, hogy tartalmazzák a bérlőt a hitelesítéshez.
    + Támogatás hozzáadva a File Dataset partícióknak a glob struktúra alapján való megértőjéhez.
  + **azureml-interpret**
    + azureml-interpret frissítve a interpret-community 0.17.0 használatára
  + **azureml-opendatasets**
    + Bemeneti kezdő dátum és záró dátumtípus érvényesítése és hibajelzés, ha nem datetime típusú.
  + **azureml-pipeline-core**
    + Hibajavítás – az path_on_compute támogatása az adatkészlet-konfiguráció letöltésként való átadása során.
  + **azureml-pipeline-steps**
    + Hibajavítás – az path_on_compute támogatása az adatkészlet-konfiguráció letöltésként való átadása során.
    + Az RScriptStep elavult, és a CommandStep parancsokat használja az R-szkriptek folyamatokban való futtatásához. 
    + A EstimatorStep elavult, mert a CommandStep parancsot használja a gépi tanulási betanítás (beleértve az elosztott betanításokat is) folyamatokban való futtatásához.
  + **azureml-train-automl-runtime**
    +  A konzol kimenete módosult az AutoML-futtatás elküldésekor, hogy a futtatás portálhivatkozását mutassa.
  + **azureml-train-core**
    + A DockerSection "enabled", "shared_volume" és "arguments" attribútumai elavultak a DockerConfiguration és a ScriptRunConfig használata mellett.
    + A Azure Open Datasets használata MNIST-adathalmaz
    + A HyperDrive hibaüzenetei frissültek.


## <a name="2021-03-31"></a>2021-03-31
### <a name="azure-machine-learning-studio-notebooks-experience-march-update"></a>Azure Machine Learning Studio Notebooks felhasználói élménye (márciusi frissítés)
+ **Új funkciók**
  + CSV/TSV renderelése. A felhasználók rácsos formátumban renderelik a TSV-/CSV-fájlokat az egyszerűbb adatelemzés érdekében. 
  + SSO-hitelesítés számítási példányhoz. A felhasználók mostantól könnyedén hitelesítik az új számítási példányokat közvetlenül a Notebook felhasználói felületén, így könnyebben hitelesíthetők és használhatók az Azure SDK-k közvetlenül az AzureML-ben. 
  + Számítási példány metrika. A felhasználók terminálon keresztül megtekinthetik a számítási metrikákat, például a processzorhasználatot és a memóriát.
  + Fájl részletei. A felhasználók mostantól a fájl melletti 3 pontra kattintva láthatják a fájl részleteit, beleértve a legutóbbi módosítás idejét és a fájlméretet.

+ **Hibajavítások és fejlesztések**
  + Továbbfejlesztett oldalbetöltési idők.
  + Jobb teljesítmény.
  + Nagyobb sebesség és kernel megbízhatóság.
  + Függőleges tulajdont nyerhet, ha a Notebook-fájl panelt tartósan felfelé mozgatja
  + A hivatkozások mostantól kattinthatók a Terminálban
  + Jobb IntelliSense-teljesítmény


## <a name="2021-03-08"></a>2021-03-08

### <a name="azure-machine-learning-sdk-for-python-v1240"></a>Azure Machine Learning SDK for Python v1.24.0
+ **Hibajavítások és fejlesztések**
  + **azureml-automl-core**
    + Visszamenőlegesen kompatibilis importok eltávolítva a `azureml.automl.core.shared` alkalmazásból. A nem található modulhibák a névtérben az importálása segítségével `azureml.automl.core.shared` oldhatóak `azureml.automl.runtime.shared` fel.
  + **azureml-contrib-automl-dnn-vision**
    + Elérhetővé téve az objektumészlelési yolo modellt.
  + **azureml-contrib-dataset**
    + Új funkció a táblázatos adatkészletek oszlopértékek és fájladatkészletek metaadatok alapján való szűréséhez.
  + **azureml-contrib-fairness**
    + JSON-séma használata a wheel-hez `azureml-contrib-fairness`
  + **azureml-contrib-br**
    + Ha a show_output true (Igaz) értékre van stb. a modellek üzembe helyezésekor, a rendszer visszajátszja a következtetési konfigurációt és az üzembe helyezési konfigurációt, mielőtt elküldi a kérést a kiszolgálónak.
  + **azureml-core**
    + Új funkció a táblázatos adatkészletek oszlopértékek és fájladatkészletek metaadatok alapján való szűréséhez.
    + Korábban a felhasználóknak kellett kiépítési konfigurációkat létrehozniuk a ComputeTarget-hez, amelyek nem feleltek meg a mező jelszóerősség-követelményeinek (azaz a következők közül legalább háromnak kell `admin_user_password` tartalmaznia: 1 kisbetű, 1 nagybetű, 1 számjegy és 1 speciális karakter a következő készletből: ``\`~!@#$%^&*()=+_[]{}|;:./'",<>?`` ). Ha a felhasználó gyenge jelszóval hoz létre konfigurációt, és ezzel a konfigurációval futtat egy feladatot, a feladat futásidőben sikertelen lesz. A hívása egy üzenetet fog küldeni a jelszóerősség követelményeit magyarázó, kapcsolódó `AmlCompute.provisioning_configuration` `ComputeTargetException` hibaüzenettel. 
    + Emellett bizonyos esetekben lehetőség volt negatív számú csomópontot is megadni a konfigurációhoz. Ezt már nem lehet megtenni. A most `AmlCompute.provisioning_configuration` egy függvényt `ComputeTargetException` ad vissza, ha `max_nodes` az argumentum egy negatív egész szám.
    + Ha a show_output True (Igaz) érték van bejelzve a modellek üzembe helyezésekor, megjelenik a következtetési konfiguráció és az üzembe helyezés konfigurációja.
    + Ha a show_output a modell üzembe helyezésének befejezésére való várakozáskor true (Igaz) érték van megszűkülve, megjelenik az üzembe helyezési művelet előrehaladása.
    + Az ügyfél által megadott AzureML hitelesítési konfigurációs könyvtár engedélyezése környezeti változóval: AZUREML_AUTH_CONFIG_DIR
    + Korábban lehetőség volt olyan kiépítési konfiguráció létrehozására, amely a csomópontok minimális számát a maximális csomópontszámnál kisebbre csökkenti. A feladat futna, de futásidőben meghiúsulna. Ezt a hibát kijavítottuk. Ha most megpróbál létrehozni egy kiépítési konfigurációt az SDK-val, az `min_nodes < max_nodes` a következőt fogja `ComputeTargetException` létrehozni: .
  + **azureml-interpret**
    + a magyarázatok irányítópultjának kijavítva, amely nem mutatja az összesített funkciók fontosságát a ritka magyarázatokhoz
    + ExplanationClient optimalizált memóriahasználata az azureml-interpret csomagban
  + **azureml-train-automl-client**
    +  A show_output=False érték kijavítva, hogy visszaadja a vezérlést a felhasználónak a Spark használatával való futtatáskor.

## <a name="2021-02-28"></a>2021-02-28
### <a name="azure-machine-learning-studio-notebooks-experience-february-update"></a>Azure Machine Learning Studio Notebooks experience (februári frissítés)
+ **Új funkciók**
  + [Natív terminál (GA)](./how-to-access-terminal.md). A felhasználók mostantól az integrált terminálon keresztül hozzáférhetnek az integrált terminálhoz és a Git-művelethez is.
  + Jegyzetfüzet-kódrészletek (előzetes verzió). Az Azure ML-kód gyakori kivonatai mostantól kéznél érhetők el. Lépjen a kódrészletek panelre, amely az eszköztáron keresztül érhető el, vagy aktiválja a kódban található kódrészletek menüjét a Ctrl + Szóköz billentyűkombinációval.  
  + [Billentyűparancsok.](./how-to-run-jupyter-notebooks.md#useful-keyboard-shortcuts) Teljes paritás a Jupyterben elérhető billentyűparancsokkal. 
  + Cellaparaméterek jelzése. Megmutatja a felhasználóknak, hogy a jegyzetfüzet mely cellái paramétercellák, és futtathat paraméteres jegyzetfüzeteket [a Papermalon](https://github.com/nteract/papermill) keresztül a számítási példányon.
  + Terminál- és kernel-munkamenetkezelő: A felhasználók a számításuk során futó összes kernelt és terminál-munkamenetet képesek lesznek kezelni.
  + Megosztás gomb. A felhasználók mostantól bármilyen fájlt megoszthatnak a Notebook-fájlkezelőben. Kattintson a jobb gombbal a fájlra, és használja a megosztás gombot.


+ **Hibajavítások és fejlesztések**
  + Továbbfejlesztett oldalbetöltési idők
  + Jobb teljesítmény 
  + Nagyobb sebesség és kernel megbízhatóság
  + Forgókerék hozzáadva, amely az összes folyamatban lévő [számításipéldány-művelet előrehaladását mutatja.](./how-to-run-jupyter-notebooks.md#status-indicators)
  + Kattintson a jobb gombbal a Fájlkezelő. Ha a jobb gombbal kattint valamelyik fájlra, megnyílik a fájlművelet. 


## <a name="2021-02-16"></a>2021-02-16

### <a name="azure-machine-learning-sdk-for-python-v1230"></a>Azure Machine Learning SDK for Python 1.23.0
+ **Hibajavítások és fejlesztések**
  + **azureml-core**
    + [Kísérleti funkció] Támogatás hozzáadása a synapse-munkaterület és az AML összekapcsolása csatolt szolgáltatásként
    + [Kísérleti funkció] Synapse Spark-készlet számítási erőforrásként való csatolásának támogatása az AML-hez
    + [Kísérleti funkció] Identitásalapú adatelérés támogatása. A felhasználók hitelesítő adatok megadása nélkül regisztrálhat adattárat vagy adatkészleteket. Ilyen esetben a rendszer a felhasználók AAD-jogkivonatát vagy a számítási cél felügyelt identitását használja a hitelesítéshez. További információkat [itt](./how-to-identity-based-data-access.md) talál.
  + **azureml-pipeline-steps**
    + [Kísérleti funkció] A [SynapseSparkStep támogatása hozzáadva](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.synapsesparkstep)
  + **azureml-synapse**
    + [Kísérleti funkció] A Spark Magic támogatása hozzáadva interaktív munkamenetek synapse Spark-készletben való futtatásához.
+ **Hibajavítások és fejlesztések**
  + **azureml-automl-runtime**
    + Ebben a frissítésben exponenciális simítást adtunk hozzá az AutoML SDK előrejelzési eszközkészletéhez. Adott idősorozat esetén a legjobb modellt választja ki az [AICc (Az Akaike](https://otexts.com/fpp3/selecting-predictors.html#selecting-predictors) információs feltételének kijavítása) és visszaadja.
    + Az AutoML mostantól egy helyett két naplófájlt hoz létre. A naplóbejegyzések az egyikhez vagy a másikhoz fognak átmenni attól függően, hogy melyik folyamatban lett létrehozva a naplókivonat.
    + Távolítsa el a felesleges mintánkénti előrejelzéseket a modell betanítás során keresztellenőrzésekkel. Ez bizonyos esetekben csökkentheti a modell betanításának idejét, különösen az idősorozat-előrejelzési modellek esetében.
  + **azureml-contrib-fairness**
    + Adjon hozzá egy JSON-sémát a dashboardDictionary feltöltéshez.
  + **azureml-contrib-interpret**
    + az azureml-contrib-interpret README frissült, hogy tükrözze, hogy a csomag október óta elavult, a következő frissítésben el lesz távolítva. Használja helyette az azureml-interpret csomagot
  + **azureml-core**
    + Korábban létre lehetett hozni egy kiépítési konfigurációt, amely a csomópontok minimális számát a maximális csomópontszámnál kisebbre csökkenti. Ezt kijavítottuk. Ha most megpróbál létrehozni egy kiépítési konfigurációt az SDK-val, `min_nodes < max_nodes` a rendszer a következőt fogja létrehozni: `ComputeTargetException` .
    +  Javítva lett az AmlCompute wait_for_completion hiba, amely miatt a függvény a művelet ténylegesen befejezése előtt visszaadta a vezérlési folyamatot
    + A Run.fail() elavult, a Run.tag() használatával megjelölhető a futtatás sikertelenként, vagy a Run.cancel() használatával megszakítottként jelölhető meg a futtatás.
    + "A környezet neve várt str, found" hibaüzenet jelenik meg, ha a környezet neve {} nem sztring.
  + **azureml-train-automl-client**
    + Kijavítottunk egy hibát, amely megakadályozta a Azure Databricks fürtökön végrehajtott AutoML-kísérletek megszakítását.


## <a name="2021-02-09"></a>2021-02-09

### <a name="azure-machine-learning-sdk-for-python-v1220"></a>Azure Machine Learning SDK for Python v1.22.0
+ **Hibajavítások és fejlesztések**
  + **azureml-automl-core**
    + Kijavítva a hiba, amely miatt egy további pip-függőség lett hozzáadva a conda yml-fájlhoz a látásmodellek számára.
  + **azureml-automl-runtime**
    + Kijavítottunk egy hibát, amely miatt a klasszikus előrejelzési modellek (például az AutoArima) betanításadatokat kaphattak, amelyekben a imputált célértékeket tartalmazó sorok nem voltak jelen. Ez megsértették ezeknek a modelleknek az adatszerződését. * Az idősor késési operátorának késési viselkedésével kapcsolatos különböző hibák kijavítva. Korábban a lag-by-occurrence művelet nem megfelelően jelölni az összes imputált sort, így nem mindig a megfelelő előfordulási késési értékeket generálta. Kijavítottunk néhány kompatibilitási problémát is a késési operátor és a gördülőablak-operátor között a késés előfordulási viselkedésével. Ez korábban azt eredményezte, hogy a gördülőablak-operátor eldobt néhány sort a betanítási adatokból, amelyet egyébként használnia kellene.
  + **azureml-core**
    + Token-hitelesítés támogatása célközönség szerint.
    + Adja hozzá a adatokat a `process_count` [PyTorchConfigurationhoz](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration) a többfolyamatos többcsomópontos PyTorch-feladatok támogatásához.
  + **azureml-pipeline-steps**
    + [CommandStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.commandstep) now GA and no longer experimental.
    + [ParallelRunConfig:](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.parallelrunconfig)adjon hozzá allowed_failed_count és allowed_failed_percent a hibaküszöbérték mini kötegszinten való ellenőrzéshez. A hibaküszöbnek most 3 íze van:
       + error_threshold – az engedélyezett hibás mini kötegelemek száma; 
       + allowed_failed_count – az engedélyezett hibás mini kötegek száma; 
       + allowed_failed_percent – az engedélyezett hibás mini kötegek százalékos aránya. 
       
       A feladat leáll, ha valamelyiket túllépi. error_threshold szükség van a visszamenőleges kompatibilitáshoz. A figyelmen kívül hagyáshoz állítsa a -1 értéket.
    + Ki van javítva az eltoláskezelés az AutoMLStep neve alatt.
    + A HyperDriveStep mostantól támogatja a ScriptRunConfig parancsprogramot
  + **azureml-train-core**
    + A ScriptRun parancsból meghívott HyperDrive-futtatásokat a rendszer gyermekfuttatásnak fogja tekinteni.
    + Adja hozzá a adatokat a `process_count` [PyTorchConfigurationhoz](/python/api/azureml-core/azureml.core.runconfig.pytorchconfiguration) a többfolyamatos többcsomópontos PyTorch-feladatok támogatásához.
  + **azureml-widgets**
    + A ParallelRunStepDetails vezérlő hozzáadása a ParallelRunStep állapotának megjelenítéséhez.
    + Lehetővé teszi a hiperdrive-felhasználók számára, hogy egy további tengelyt lássanak a párhuzamos koordináták diagramján, amely az egyes gyermekfuttassak hiperparaméter-készletének megfelelő metrikaértékeket jeleníti meg.


 ## <a name="2021-01-31"></a>2021-01-31
### <a name="azure-machine-learning-studio-notebooks-experience-january-update"></a>Azure Machine Learning Studio Notebooks experience (januári frissítés)
+ **Új funkciók**
  + Natív Markdown-szerkesztő az AzureML-ben. A felhasználók mostantól natív módon renderelhetik és szerkeszthetik a Markdown-fájlokat az AzureML Studióban.
  + [Run Button for Scripts (.py, . R és .sh)](./how-to-run-jupyter-notebooks.md#run-a-notebook-or-python-script). A felhasználók mostantól egyszerűen futtathatnak Python-, R- és Bash-szkripteket az AzureML-ben
  + [Változókezelő:](./how-to-run-jupyter-notebooks.md#explore-variables-in-the-notebook). A változók és adatkeretek tartalmát egy előugró panelen vizsgáljuk meg. A felhasználók könnyen ellenőrizhetik az adattípust, a méretet és a tartalmakat.
  + [Tartalomjegyzék.](./how-to-run-jupyter-notebooks.md#navigate-with-a-toc) Navigáljon a jegyzetfüzet szakaszaihoz, amelyet Markdown-fejlécek jeleznek.
  + Exportálja a jegyzetfüzetet Latex/HTML/Py formátumban. Könnyen megosztható jegyzetfüzetfájlok létrehozása LaTex-, HTML- vagy .py-exportálás használatával
  + Intellicode. Az ML-alapú eredmények továbbfejlesztett intelligens [automatikus kiegészítési élményt kínálnak.](/visualstudio/intellicode/overview)

+ **Hibajavítások és fejlesztések**
  + Továbbfejlesztett oldalbetöltési idők
  + Jobb teljesítmény 
  + Nagyobb sebesség és kernel megbízhatóság
  

 ## <a name="2021-01-25"></a>2021-01-25

### <a name="azure-machine-learning-sdk-for-python-v1210"></a>Azure Machine Learning SDK for Python 1.21.0
+ **Hibajavítások és fejlesztések**
  + **azure-cli-ml**
    + Kijavítottuk a CLI súgószövegét az AmlCompute userAssigned Identityvel való használata esetén
  + **azureml-contrib-automl-dnn-vision**
    + Az üzembe helyezési és letöltési gombok láthatóvá válnak az AutoML-látásfuttatások esetében, és a modellek más AutoML-futtatáshoz hasonlóan helyezhetők üzembe vagy tölthetők le. Két új fájl (scoring_file_v_1_0_0.py és conda_env_v_1_0_0.yml), amelyek egy következtetés futtatására használható szkriptet és egy yml-fájlt tartalmaznak a Conda-környezet újbóli létrehozása érdekében. A "model.pth" fájl is át lett nevezve a ".pt" kiterjesztés használatára.
  + **azureml-core**
    + MSI-támogatás az azure-cli-ml-hez
    + Felhasználó által hozzárendelt felügyelt identitás támogatása.
    + Ezzel a módosítással az ügyfeleknek meg kell adniuk egy felhasználóhoz hozzárendelt identitást, amely segítségével lekérheti a kulcsot az ügyfélkulcs-tárolóból az adatok titkosításához.
    +  javítva row_count=0 hiba a nagyon nagy fájlok profilja esetén – javítás a tagolt értékek kettős konvertálása során, térköz kitöltéssel
    + A kimeneti adatkészlet GA kísérleti jelzője el van távolítva
    + A modell adott verziójának lekérésére vonatkozó dokumentáció frissítése
    + Munkaterület frissítésének engedélyezése vegyes módú hozzáféréshez privát kapcsolat esetén
    + Javítás az adattár további regisztrációinak a futtatás folytatása funkcióhoz való eltávolításához
    + CLI/SDK-támogatás hozzáadva a munkaterület elsődleges felhasználóhoz hozzárendelt identitásának frissítéséhez
  + **azureml-interpret**
    + frissített azureml-interpret a community 0.16.0 értelmezéséhez
    + memóriaoptimalizálás magyarázat ügyfélhez az azureml-interpret-ben
  + **azureml-train-automl-runtime**
    + Az ADB-futtatás engedélyezett streamelése
  + **azureml-train-core**
    + Javítás az adattár további regisztrációinak a futtatás folytatása funkcióhoz való eltávolításához
  + **azureml-widgets**
    + Az ügyfeleknek nem szabad látniuk a widget használatával a meglévő futtatott adatvizualizációk változásait, és mostantól támogatásuk lesz, ha feltételes hiperparamétereket is használhatnak.
    + A felhasználó által futtatott widget most részletes magyarázatot tartalmaz arra, hogy miért van egy futtatás várólistán.


 ## <a name="2021-01-11"></a>2021-01-11

### <a name="azure-machine-learning-sdk-for-python-v1200"></a>Azure Machine Learning SDK for Python v1.20.0
+ **Hibajavítások és fejlesztések**
  + **azure-cli-ml**
    + framework_version a OptimizationConfig alatt. Akkor lesz használva, amikor a modell regisztrálva van a MULTI keretrendszerben.
  + **azureml-contrib-optimization**
    + framework_version a OptimizationConfig alatt. Akkor lesz használva, amikor a modell regisztrálva van a MULTI keretrendszerben.
  + **azureml-pipeline-steps**
    + A CommandStep bevezetése, amely a feldolgozáshoz parancsot adna. A parancs végrehajtható fájlokat, rendszerhéjparancsokat, szkripteket stb. tartalmazhat.
  + **azureml-core**
    + A munkaterület létrehozása mostantól támogatja a felhasználó által hozzárendelt identitást. UAI-támogatás hozzáadása az SDK-ból/CLI-ről
    + Ki van javítva a service.reload() problémája, amely a helyi üzembe helyezés score.py módosításokat.
    + `run.get_details()` A rendelkezik egy "submittedBy" nevű további mezővel, amely megjeleníti a futtatás szerzőjének nevét.
    + Szerkesztette a Model.register metódus dokumentációját, hogy megemlítse, hogyan regisztrálhatja közvetlenül a modellt a futtatásból
    + Javítva IOT-Server kapcsolatállapot-módosítás kezelési problémája.
   

## <a name="2020-12-31"></a>2020-12-31
### <a name="azure-machine-learning-studio-notebooks-experience-december-update"></a>Azure Machine Learning Studio Notebooks Experience (decemberi frissítés)
+ **Új funkciók**
  + Felhasználói fájlnév keresése. A felhasználók mostantól a munkaterületen mentett összes fájlban kereshetnek.
  + Markdown Side by Side support per Notebook Cell. A notebookcellákban a felhasználók mostantól egymás mellett is megtekinthetik a renderelt Markdown- és Markdown-szintaxist.
  + Cella állapotsávja. Az állapotsáv jelzi, hogy a kódcella milyen állapotban van, sikeres volt-e a cellafutatás, és mennyi ideig tartott a futtatása. 
   
+ **Hibajavítások és fejlesztések**
  + Továbbfejlesztett oldalbetöltési idők
  + Jobb teljesítmény 
  + Nagyobb sebesség és kernel megbízhatóság

  
## <a name="2020-12-07"></a>2020-12-07

### <a name="azure-machine-learning-sdk-for-python-v1190"></a>Azure Machine Learning SDK for Python 1.19.0
+ **Hibajavítások és fejlesztések**
  + **azureml-automl-core**
    + Kísérleti támogatás hozzáadva a tesztadatokhoz az AutoMLStephez.
    + Hozzá van adva a tesztkészlet-betlési funkció kezdeti alapvető implementációja.
    + Az sklearn.externals.joblib hivatkozásai áthelyezve, hogy közvetlenül a joblib-től függenek.
    + az "image-instance-segmentation" új AutoML-feladattípus bevezetése.
  + **azureml-automl-runtime**
    + Hozzá van adva a tesztkészlet-betlési funkció kezdeti alapvető implementációja.
    + Ha egy szöveges oszlop összes sztringje pontosan 1 karakterből áll, a TfIdf word-gram jellemzősítő nem fog működni, mert a tokenizer figyelmen kívül hagyja a 2 karakternél rövidebb sztringeket. A jelenlegi kód módosítása lehetővé teszi, hogy az AutoML kezelje ezt a használatot.
    + az "image-instance-segmentation" új AutoML-feladattípus bevezetése.
  + **azureml-contrib-automl-dnn-nlp**
    + Az új dnn-nlp csomag kezdeti le pr-e
  + **azureml-contrib-automl-dnn-vision**
    + az "image-instance-segmentation" új AutoML-feladattípus bevezetése.
  + **azureml-contrib-automl-pipeline-steps**
    + Ez az új csomag felelős a számos modell betanítása/következtetési forgatókönyvéhez szükséges lépések létrehozásáért. – A train/inference kódot is áthelyezi az azureml.train.automl.runtime csomagba, így a jövőbeli javítások automatikusan elérhetők lesznek az összeszámolt környezetek kiadásán keresztül.
  + **azureml-contrib-dataset**
    + az "image-instance-segmentation" új AutoML-feladattípus bevezetése.
  + **azureml-core**
    + Hozzá van adva a tesztelési készletbe való bevégrehajtásához szükséges funkció kezdeti alapvető implementációja.
    + Az xref-figyelmeztetések kijavítva az azureml-core csomagban található dokumentációhoz
    + Dokumentumsring-javítások az SDK parancstámogatási funkcióhoz
    + Parancstulajdonság hozzáadása a RunConfiguration parancshoz. A funkció lehetővé teszi, hogy a felhasználók tényleges parancsokat vagy végrehajtható fájlokat futtassanak a számításon az AzureML SDK-val.
    + A felhasználók törölhetik az üres kísérleteket a kísérlet azonosítója alapján.
  + **azureml-dataprep**
    + Adatkészlet-támogatás hozzáadva a Scala 2.12-es és a Sparkhoz. Ez növeli a meglévő 2.11-es támogatást.
  + **azureml-mlflow**
    + AzureML-MLflow biztonságos védőket ad hozzá a távoli szkriptekhez, hogy elkerülje a beküldött futtatás korai megszakítását.
  + **azureml-pipeline-core**
    + Kijavítottuk a felhasználói felületen létrehozott folyamatvégpont alapértelmezett folyamatbeállításával kapcsolatos hibát
  + **azureml-pipeline-steps**
    + Kísérleti támogatás hozzáadva a tesztadatokhoz az AutoMLStephez.
  + **azureml-tensorboard**
    + Az xref-figyelmeztetések kijavítva az azureml-core csomagban található dokumentációhoz
  + **azureml-train-automl-client**
    + Kísérleti támogatás hozzáadva a tesztadatokhoz az AutoMLStephez.
    + Hozzá van adva a tesztelési készletbe való bevégrehajtásához szükséges funkció kezdeti alapvető implementációja.
    + az "image-instance-segmentation" új AutoML-feladattípus bevezetése.
  + **azureml-train-automl-runtime**
    + Hozzá van adva a tesztkészlet-betlési funkció kezdeti alapvető implementációja.
    + Javítsa ki a legjobb AutoML-modell nyers magyarázatának számítását, ha az AutoML-modellek betanítása egy validation_size beállítással történik.
    + Az sklearn.externals.joblib hivatkozásai áthelyezve, hogy közvetlenül a joblib-től függenek.
  + **azureml-train-core**
    + HyperDriveRun.get_children_sorted_by_primary_metric() gyorsabban befejeződik
    + Továbbfejlesztett hibakezelés a HyperDrive SDK-ban.
    +  Az összes becslévő osztály elavult, és a ScriptRunConfig parancs használatával konfigurálta a kísérletfuttatásokat. Az elavult osztályok közé tartoznak a következők:
        + MMLBase
        + Becsléret
        + PyTorch 
        + TensorFlow 
        + Chainer 
        + SKLearn
    + A PyTorchConfiguration és a ScriptRunConfig használata mellett az Nccl és a Amitoo érvényes bemeneti típusként való használata elavult.
    + Az Mpi Használata elavult az Estimator osztályok érvényes bemeneti típusaként az MpiConfiguration és a ScriptRunConfig használata mellett.
    + Parancstulajdonság hozzáadása a runconfiguration parancshoz. A funkció lehetővé teszi, hogy a felhasználók tényleges parancsokat vagy végrehajtható fájlokat futtassanak a számításon az AzureML SDK-val.

    +  Az összes becslévő osztály elavult, és a ScriptRunConfig parancs használatával konfigurálta a kísérletfuttatásokat. Elavult osztályok: + MMLBaseEstimator + Estimator + PyTorch + TensorFlow + Chainer + SKLearn
    + A PyTorchConfiguration és a ScriptRunConfig használata mellett az Nccl és a Amitoo érvényes bemeneti típusként való használata elavult. 
    + Az MpiConfiguration és a ScriptRunConfig használata az Estimator osztályok érvényes bemeneti típusaként elavult.

## <a name="2020-11-30"></a>2020-11-30
### <a name="azure-machine-learning-studio-notebooks-experience-november-update"></a>Azure Machine Learning Studio Notebooks experience (novemberi frissítés)
+ **Új funkciók**
   + Natív terminál. A felhasználók mostantól az integrált terminálon keresztül hozzáférhetnek az integrált terminálhoz és a [Git-művelethez is.](./how-to-access-terminal.md)
  + Mappa megkettőzése 
  + Számítási költségek legördülő menü 
  + Offline számítási Pylance 

+ **Hibajavítások és fejlesztések**
  + Továbbfejlesztett oldalbetöltési idők
  + Jobb teljesítmény 
  + Nagyobb sebesség és kernel megbízhatóság
  + Nagy méretű fájlfeltöltés. Most már 95 MB-os >tölthet fel

## <a name="2020-11-09"></a>2020-11-09

### <a name="azure-machine-learning-sdk-for-python-v1180"></a>Azure Machine Learning SDK for Python 1.18.0
+ **Hibajavítások és fejlesztések**
  + **azureml-automl-core**
    +  A rövid idősorok továbbfejlesztett kezelése azáltal, hogy lehetővé teszi a zajokkal való kitöltést.
  + **azureml-automl-runtime**
    + ConfigException visszadobása, ha egy DateTime oszlop OutOfBoundsDatetime értékkel rendelkezik
    + A rövid idősorok továbbfejlesztett kezelése azáltal, hogy lehetővé teszi a zajokkal való kitöltést.
    + Annak biztosra mennie, hogy minden szöveges oszlop fel tudja használni a char-gram átalakítást az n-gram tartománysal az adott szöveges oszlopban lévő sztringek hossza alapján
    + Nyers funkcióra vonatkozó magyarázatok biztosítása a felhasználó helyi számításán futó AutoML-kísérletek legjobb módjához
  + **azureml-core**
    + Rögzítse a pyjwt csomagot, hogy elkerülje a legfrissebb verziók lehúzását a jövőbeli kiadásokban.
    + Egy kísérlet létrehozása visszaadja az aktív vagy az utolsó archivált kísérletet ugyanezen a megadott néven, ha létezik ilyen kísérlet, vagy egy új kísérlet.
    + A get_experiment hívása visszaadja az adott nevű aktív vagy utolsó archivált kísérletet.
    + A felhasználók nem neveznek át egy kísérletet az újraaktiválás során.
    + Továbbfejlesztett hibaüzenet, amely lehetséges javításokat tartalmaz, ha egy adatkészletet helytelenül ad át egy kísérletnek (például ScriptRunConfig). 
    + Továbbfejlesztett dokumentáció a `OutputDatasetConfig.register_on_complete` fájlhoz, hogy tartalmazza azt a viselkedést, ami akkor történik, ha a név már létezik.
    + Ha olyan bemeneti és kimeneti neveket ad meg, amelyek ütköznek a gyakori környezeti változókban, mostantól figyelmeztetést eredményeznek
    + Repurposed `grant_workspace_access` parameter when registering datastores.repurposed parameter when registering datastores. Állítsa a beállításra a virtuális hálózat mögötti adatok eléréséhez a `True` Machine Learning Studióból.
      [További információ](./how-to-enable-studio-virtual-network.md)
    + A csatolt szolgáltatási API finomított. Erőforrás-azonosító helyett 3 külön paramétert sub_id, rg és név definiálva a konfigurációban.
    + Annak érdekében, hogy az ügyfelek saját maga oldják meg a jogkivonatok sérülésével kapcsolatos problémákat, engedélyezze a munkaterületi jogkivonatok szinkronizálását nyilvános módszerként.
    + Ez a módosítás lehetővé teszi, hogy üres sztringet használjanak egy script_param
  + **azureml-train-automl-client**
    +  A rövid idősorok továbbfejlesztett kezelése azáltal, hogy lehetővé teszi a huss-zajokkal való kitöltést.
  + **azureml-train-automl-runtime**
    + ConfigException visszadobása, ha egy DateTime oszlop OutOfBoundsDatetime értékkel rendelkezik
    + Nyers funkcióra vonatkozó magyarázatok biztosítása a felhasználó helyi számításán futó AutoML-kísérletek legjobb modelljéhez
    + A rövid idősorok továbbfejlesztett kezelése azáltal, hogy lehetővé teszi a huss-zajokkal való kitöltést.
  + **azureml-train-core**
    + Ez a módosítás lehetővé teszi, hogy üres sztringet használjanak egy script_param
  + **azureml-train-restclients-hyperdrive**
    + A README módosult, hogy több kontextust nyújtson
  + **azureml-widgets**
    + Sztringtámogatás hozzáadása a diagramokhoz/párhuzamos koordináták könyvtárához a widgethez.

## <a name="2020-11-05"></a>2020-11-05

### <a name="data-labeling-for-image-instance-segmentation-polygon-annotation-preview"></a>Adatcímkék a képpéldány szegmentálása (sokszögjegyzet) (előzetes verzió)

A képpéldány-szegmentálás (sokszögjegyzetek) projekttípus már elérhető az adatcímkékben, így a felhasználók sokszögeket rajzolnak és jegyzetelnek a képeken lévő objektumok köré. A felhasználók osztályt és sokszöget rendelhetnek minden objektumhoz, amely a képen belül fontos.

További információ a [képpéldány szegmentálásának címkézésről.](how-to-label-images.md)



## <a name="2020-10-26"></a>2020-10-26

### <a name="azure-machine-learning-sdk-for-python-v1170"></a>Azure Machine Learning SDK for Python 1.17.0
+ **új példák**
  + A példák közösségi alapú új adattárak a következőben érhetők el: https://github.com/Azure/azureml-examples
+ **Hibajavítások és fejlesztések**
  + **azureml-automl-core**
    + Kijavítottunk egy hibát, get_output az XGBoostErrort is okozhatta.
  + **azureml-automl-runtime**
    + Az AutoML által létrehozott idő- és naptáralapú szolgáltatások már tartalmazni fogják az előtagot.
    + Ki van javítva a StackEnsemble betanítása során előforduló IndexError olyan besorolási adatkészletek esetén, amelyeken engedélyezve van a nagy számú osztály és részbélyegző.
    + Kijavítottunk egy hibát, amely miatt a VotingRegressor előrejelzései pontatlanok lehetnek a modell újrailledtetődése után.
  + **azureml-core**
    + További részletek az AKS üzembe helyezési konfigurációja és a Azure Kubernetes Service közötti kapcsolatról.
    + A környezeti ügyfélcímkék támogatása. A felhasználó címkézheti a környezeteket, és hivatkozhat rájuk címke alapján.
  + **azureml-dataprep**
    + Jobb hibaüzenet a jelenleg nem támogatott Spark és Scala 2.12 használata esetén.
  + **azureml-explain-model**
    + Az azureml-explain-model csomag hivatalosan elavult
  + **azureml-mlflow**
    + Megoldottunk egy hibát az mlflow.projects.run fájlban az azureml háttéren, ahol a véglegesítő állapot nem megfelelően lett kezelve.
  + **azureml-pipeline-core**
    + Támogatás hozzáadása egy folyamatvégpont alapján a folyamat ütemezésének létrehozásához, listához és lekért létrehozásához.
    +  A hiba dokumentációjának PipelineData.as_dataset érvénytelen használati példával – A PipelineData.as_dataset helytelen használata mostantól ValueException-hibákat eredményez
    + Módosította a HyperDriveStep-folyamatok jegyzetfüzetet, hogy a legjobb modellt regisztrálja a PipelineStepben közvetlenül a HyperDriveStep futtatása után.
  + **azureml-pipeline-steps**
    + Módosította a HyperDriveStep-folyamatok jegyzetfüzetet, hogy a legjobb modellt regisztrálja a PipelineStepben közvetlenül a HyperDriveStep futtatása után.
  + **azureml-train-automl-client**
    + Kijavítottunk egy hibát, get_output XGBoostErrort okozhatott.

### <a name="azure-machine-learning-studio-notebooks-experience-october-update"></a>Azure Machine Learning Studio Notebooks felhasználói élménye (októberi frissítés)
+ **Új funkciók**
  + [Teljes körű virtuális hálózati támogatás](./how-to-enable-studio-virtual-network.md)
  + [Fókusz mód](./how-to-run-jupyter-notebooks.md#focus-mode)
  + Jegyzetfüzetek mentése Ctrl-S
  + Sorszámok

+ **Hibajavítások és fejlesztések**
  + A sebesség és a kernel megbízhatóságának javítása
  + A Jupyter Widget felhasználói felületének frissítései

## <a name="2020-10-12"></a>2020-10-12

### <a name="azure-machine-learning-sdk-for-python-v1160"></a>Azure Machine Learning SDK for Python 1.16.0
+ **Hibajavítások és fejlesztések**
  + **azure-cli-ml**
    + Az AKSWebservice és az AKSEndpoints mostantól támogatja a podszintű processzor- és memória-erőforráskorlátokat. Ezek a választható korlátok a megfelelő CLI-hívásokban való beállításával `--cpu-cores-limit` `--memory-gb-limit` és jelzőivel használhatók
  + **azureml-core**
    + Az azureml-core közvetlen függőségeinek főverziói kitűzve
    + Az AKSWebservice és az AKSEndpoints mostantól támogatja a podszintű processzor- és memória-erőforráskorlátokat. További információ a [Kubernetes-erőforrásokról és -korlátokról](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#requests-and-limits)
    + A run.log_table, hogy az egyes sorokat naplózni is lehet.
    + Statikus metódus `Run.get(workspace, run_id)` hozzáadva a futtatás csak munkaterület használatával való lekéréséhez 
    + Példány metódus `Workspace.get_run(run_id)` hozzáadva a futtatás lekéréséhez a munkaterületen
    + A parancstulajdonság bevezetése a futtatási konfigurációban, amely lehetővé teszi a felhasználók számára, hogy parancsokat küldjenek el a & argumentumai helyett.
  + **azureml-interpret**
    + kijavítva a magyarázat az is_raw jelző viselkedése az azureml-interpret-ben
  + **azureml-sdk**
    + `azureml-sdk` hivatalosan támogatja a Python 3.8-at.
  + **azureml-train-core**
    + TensorFlow 2.3 válogatott környezet hozzáadása
    + A parancstulajdonság bevezetése a futtatási konfigurációban, amely lehetővé teszi a felhasználók számára, hogy parancsokat küldjenek el a & argumentumai helyett.
  + **azureml-widgets**
    + Újratervezett felület szkriptfuttassa widgethez.


## <a name="2020-09-28"></a>2020-09-28

### <a name="azure-machine-learning-sdk-for-python-v1150"></a>Azure Machine Learning SDK for Python 1.15.0
+ **Hibajavítások és fejlesztések**
  + **azureml-contrib-interpret**
    + A EXPLAIN magyarázó az azureml-contrib-interpret ről az azureml-contrib-interpret csomagból eltávolított közösségi csomag és kép magyarázó értelmezőjére helyezték át
    + az azureml-contrib-interpret csomagból eltávolított vizualizációs irányítópult, a magyarázat-ügyfél átkerült az azureml-interpret csomagba, és elavult az azureml-contrib-interpret csomagban és jegyzetfüzetben, frissítve a továbbfejlesztett API-hoz
    + az azureml-interpret, az azureml-explain-model, az azureml-contrib-interpret és az azureml-tensorboard pypi-csomagleírásának kijavítva
  + **azureml-contrib-notebook**
    + Rögzítse az nbcovert függőséget a 6<, hogy a papermill 1.x továbbra is működjön.
  + **azureml-core**
    + Paraméterek hozzáadva a TensorflowConfiguration és az MpiConfiguration konstruktorhoz, így az osztályattribútumok egyszerűbb inicializálását teheti lehetővé anélkül, hogy a felhasználónak minden egyes attribútumot be kell állítania. Hozzá van adva egy PyTorchConfiguration osztály elosztott PyTorch-feladatok konfigurálásához a ScriptRunConfig parancsban.
    + Rögzítse az azure-mgmt-resource verzióját a hitelesítési hiba kijavítása érdekében.
    + A Triton No Code Deploy támogatása
    + A Run.start_logging() elemben megadott outputs könyvtárak mostantól nyomon lesznek követve az interaktív forgatókönyvekben való futtatáskor. A nyomonott fájlok a Run.complete() hívása után láthatók lesznek az ML Studióban
    + A fájlkódolás mostantól az adatkészlet létrehozásakor is meg lehet adni a és a `Dataset.Tabular.from_delimited_files` `Dataset.Tabular.from_json_lines_files` `encoding` argumentummal. A támogatott kódolások: "utf8", "iso88591", "latin1", "ascii", utf16", "utf32", "utf8bom" és "windows1252".
    + Hibajavítás, ha a rendszer nem ad át környezeti objektumot a ScriptRunConfig konstruktornak.
    + A Run.cancel() frissült, hogy lehetővé tegye egy másik gép helyi futtatásának megszakítását.
  + **azureml-dataprep**
    +  Kijavítottuk az adatkészlet csatlakoztatási időtúllépési problémáit.
  + **azureml-explain-model**
    + az azureml-interpret, az azureml-explain-model, az azureml-contrib-interpret és az azureml-tensorboard pypi-csomagleírásának kijavítva
  + **azureml-interpret**
    + az azureml-contrib-interpret csomagból eltávolított vizualizációs irányítópult, a magyarázat-ügyfél átkerült az azureml-interpret csomagba, és elavult az azureml-contrib-interpret csomagban és a továbbfejlesztett API-knak megfelelően frissített jegyzetfüzetben
    + az azureml-interpret csomag frissült, hogy a interpret-community 0.15.0-stól függ
    + az azureml-interpret, az azureml-explain-model, az azureml-contrib-interpret és az azureml-tensorboard pypi-csomagleírásának kijavítva
  + **azureml-pipeline-core**
    +  Kijavítva a folyamat problémája, amely miatt előfordulhat, hogy a rendszer nem válaszol, ha a paraméter egy már létező adathalmaznévre van `OutputFileDatasetConfig` `register_on_complete` `name` állítva.
  + **azureml-pipeline-steps**
    + Elavult Databricks-jegyzetfüzetek eltávolítva.
  + **azureml-tensorboard**
    + az azureml-interpret, az azureml-explain-model, az azureml-contrib-interpret és az azureml-tensorboard pypi-csomagleírásának kijavítva
  + **azureml-train-automl-runtime**
    + az azureml-contrib-interpret csomagból eltávolított vizualizációs irányítópult, a magyarázat-ügyfél átkerült az azureml-interpret csomagba, és elavult az azureml-contrib-interpret csomagban és a továbbfejlesztett API-knak megfelelően frissített jegyzetfüzetben
  + **azureml-widgets**
    + az azureml-contrib-interpret csomagból eltávolított vizualizációs irányítópult, a magyarázat-ügyfél átkerült az azureml-interpret csomagba, és elavult az azureml-contrib-interpret csomagban és a továbbfejlesztett API-knak megfelelően frissített jegyzetfüzetben

## <a name="2020-09-21"></a>2020-09-21

### <a name="azure-machine-learning-sdk-for-python-v1140"></a>Azure Machine Learning SDK for Python 1.14.0
+ **Hibajavítások és fejlesztések**
  + **azure-cli-ml**
    + A Grid Profiling el lett távolítva az SDK-ból, és már nem támogatott.
  + **azureml-accel-models**
    + Az azureml-accel-models csomag mostantól támogatja a Tensorflow 2.x-et
  + **azureml-automl-core**
    + Hibakezelés hozzáadva a get_output olyan esetekben, amikor a pandas/sklearn helyi verziói nem egyeznek a betanítás során használt verzióval
  + **azureml-automl-runtime**
    + Kijavítottunk egy hibát, amely miatt az AutoArima-iterációk egy PredictionException kivétel miatt meghiúsultak, és a következő üzenet jelenik meg: "Csendes hiba történt az előrejelzés során."
  + **azureml-cli-common**
    + A Grid Profiling el lett távolítva az SDK-ból, és már nem támogatott.
  + **azureml-contrib-server**
    + Frissítse a pypi áttekintő oldalának csomagleírását.
  + **azureml-core**
    + A gridprofil-készítés el lett távolítva az SDK-ból, és már nem támogatott.
    + Csökkentse a hibaüzenetek számát, ha a munkaterület lekérése meghiúsul.
    + Ne mutasson figyelmeztetést, ha a metaadatok lekérése sikertelen
    + Új Kusto-lépés és Kusto számítási cél.
    + Frissítse a termékváltozat paraméter dokumentumát. Távolítsa el a termékváltozatot a munkaterület frissítési funkcióiban a CLI-ban és az SDK-ban.
    + Frissítse a pypi áttekintő oldalának csomagleírását.
    + Frissített dokumentáció az AzureML-környezetekhez.
    + Szolgáltatás által felügyelt erőforrások beállításainak felfedése az AML-munkaterületen az SDK-ban.
  + **azureml-dataprep**
    + Engedélyezze a végrehajtási engedélyt a fájlokon az adatkészlet-csatlakoztatáshoz.
  + **azureml-mlflow**
    + Frissített AzureML MLflow-dokumentáció és jegyzetfüzet-minták 
    + Új MLflow-projektek támogatása az AzureML-háttér használatával
    + MLflow-modell beállításjegyzékének támogatása
    + Azure RBAC-támogatás hozzáadva AzureML-MLflow műveletekhez 
    
  + **azureml-pipeline-core**
    + Továbbfejlesztettük a PipelineOutputFileDataset.parse_* metódusok dokumentációját.
    + Új Kusto-lépés és Kusto számítási cél.
    + A megadott Swaggerurl tulajdonság a folyamatvégpont entitáshoz az adott felhasználón keresztül láthatja a közzétett folyamatvégpont sémadefinícióját.
  + **azureml-pipeline-steps**
    + Új Kusto-lépés és Kusto számítási cél.
  + **azureml-telemetry**
    + Frissítse a pypi áttekintő oldalának csomagleírását.
  + **azureml-train**
    + Frissítse a pypi áttekintő oldalának csomagleírását.
  + **azureml-train-automl-client**
    + Hibakezelés hozzáadva a get_output olyan esetekben, amikor a pandas/sklearn helyi verziói nem egyeznek a betanítás során használtokkal
  + **azureml-train-core**
    + Frissítse a pypi áttekintő oldalának csomagleírását.
    
## <a name="2020-08-31"></a>2020-08-31

### <a name="azure-machine-learning-sdk-for-python-v1130"></a>Azure Machine Learning SDK for Python 1.13.0
+ **Előzetes verziójú funkciók**
  + **azureml-core** Az új kimeneti adatkészletek funkcióval visszaírhat olyan felhőalapú tárolóba, mint a Blob, az ADLS Gen 1, az ADLS Gen 2 és a FileShare. Konfigurálhatja az adatok kimeneti helyének és kimeneti adatainak (csatlakoztatáson vagy feltöltésen keresztüli) beállítását, a kimeneti adatok későbbi újbóli használatra és megosztásra való regisztrálását, valamint a köztes adatok zökkenőmentesen történő megosztását a folyamat lépései között. Ez lehetővé teszi a reprodukálhatóságot, a megosztást, megakadályozza az adatok duplikálását, és költséghatékonyságot és hatékonyságnövekedést eredményez. [Útmutató a használathoz](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig)
    
+ **Hibajavítások és fejlesztések**
  + **azureml-automl-core**
    + Új validated_{platform}_requirements.txt az AutoML összes pip-függőségének rögzítéséhez.
    + Ez a kiadás a 4 Gb-nál nagyobb modelleket támogatja.
    + Frissített AutoML-függőségek: `scikit-learn` (mostantól 0.22.1), `pandas` (mostantól 0.25.1) `numpy` (mostantól 1.18.2).
  + **azureml-automl-runtime**
    + Állítsa be a horovod (horovod) szöveget a DNN-hez, hogy mindig fp16 tömörítést használjon.
    + Ez a kiadás a 4 Gb-nál nagyobb modelleket támogatja.
    + Kijavítva a hiba, amely miatt az AutoML az ImportError: nem tudja importálni a `RollingOriginValidator` nevet.
    + Frissített AutoML-függőségek: `scikit-learn` (mostantól 0.22.1), `pandas` (mostantól 0.25.1) `numpy` (mostantól 1.18.2).
  + **azureml-contrib-automl-dnn-forecasting**
    + Frissített AutoML-függőségek: `scikit-learn` (mostantól 0.22.1), `pandas` (mostantól 0.25.1) `numpy` (mostantól 1.18.2).
  + **azureml-contrib-fairness**
    + Adja meg az azureml-contrib-fairness rövid leírását.
  + **azureml-contrib-pipeline-steps**
    + Hozzá van adva egy üzenet, amely jelzi, hogy ez a csomag elavult, és a felhasználónak inkább az azureml-pipeline-steps lépéseket kell használnia.
  + **azureml-core**
    + Hozzá van adva a list key parancs a munkaterülethez.
    + Címkék paraméter hozzáadása a Workspace SDK-ban és a CLI-ban.
    + Ki van javítva az a hiba, amely miatt sikertelen lesz egy gyermekfuttatás elküldése a Datasetben. `TypeError: can't pickle _thread.RLock objects`
    + A page_count() alapértelmezett/dokumentációjának hozzáadása.
    + Módosítsa a CLI&SDK-t az adbworkspace paraméter és az Add workspace adb lin/unlink runner (Munkaterületi adb lin/unlink futó hozzáadása) beállításhoz.
    + Ki lett javítva a Dataset.update hiba, amely miatt az adatkészlet legújabb verziója frissült, nem pedig az adatkészlet-frissítés verziójának a frissítése volt behívva. 
    + Kijavítottunk egy Dataset.get_by_name, amely akkor is a legújabb adatkészlet-verzió címkéit mutatná, ha egy adott régebbi verzió lett lekérve.
  + **azureml-interpret**
    + Valószínűségi kimenetek hozzáadva az azureml-interpret shap pontozási magyarázóihoz az eredeti magyarázó shap_values_output alapján.
  + **azureml-pipeline-core**
    + Továbbfejlesztett `PipelineOutputAbstractDataset.register` dokumentáció.
  + **azureml-train-automl-client**
    + Frissített AutoML-függőségek: `scikit-learn` (most 0.22.1), `pandas` (most 0.25.1) `numpy` (mostantól 1.18.2).
  + **azureml-train-automl-runtime**
    + Frissített AutoML-függőségek: `scikit-learn` (most 0.22.1), `pandas` (most 0.25.1) `numpy` (mostantól 1.18.2).
  + **azureml-train-core**
    + A felhasználóknak meg kell adniuk egy érvényes hyperparameter_sampling arg-et a HyperDriveConfig létrehozásakor. Emellett a HyperDriveRunConfig dokumentációja is szerkesztve lett, hogy tájékoztassa a felhasználókat a HyperDriveRunConfig elalasztásról.
    + A PyTorch alapértelmezett verziójának visszaáll az 1.4-es verzióra.
    + PyTorch 1.6 hozzáadása & Tensorflow 2.2-képekhez és válogatott környezethez.

### <a name="azure-machine-learning-studio-notebooks-experience-august-update"></a>Azure Machine Learning Studio Notebooks felhasználói élménye (augusztusi frissítés)
+ **Új funkciók**
  + Új Első lépések kezdőlap 
  
+ **Előzetes verziójú funkciók**
    + A Jegyzetfüzetek gyűjtése funkciója. A [Gather](./how-to-run-jupyter-notebooks.md#clean-your-notebook-preview) (Gyűjtés) funkcióval a felhasználók mostantól könnyedén eltávolíthatja a jegyzetfüzeteket a használatával, a Gather a jegyzetfüzet automatikus függőségelemzését használja, ezzel biztosítva, hogy az alapvető kód meg legyen megőrizve, de eltávolítsa az irreleváns részleteket.

+ **Hibajavítások és fejlesztések**
  + A sebesség és a megbízhatóság fejlesztése
  + A sötét mód hibái kijavítva
  + Kimeneti görgetési hibák kijavítva
  + A mintakeresés most már a mintajegyzetfüzet-adattára összes Azure Machine Learning keres
  + A többsoros R-cellák mostantól futtathatók
  + Az "I trust contents of this file" (Megbízható tartalom a fájlban) beállítás mostantól automatikusan be van jelölve az első alkalommal
  + Továbbfejlesztett ütközésfeloldási párbeszédpanel, új "Másolat létrehozása" beállítással
  
## <a name="2020-08-17"></a>2020-08-17

### <a name="azure-machine-learning-sdk-for-python-v1120"></a>Azure Machine Learning SDK for Python 1.12.0

+ **Hibajavítások és fejlesztések**
  + **azure-cli-ml**
    + Adja image_name és image_label paramétereket a Model.package() fájlhoz a beépített csomag rendszerképének az újnanamikának engedélyezéséhez.
  + **azureml-automl-core**
    + Az AutoML új hibakódot hoz létre a dataprepből, amikor a tartalom módosul az olvasás közben.
  + **azureml-automl-runtime**
    + Riasztások hozzáadva a felhasználóhoz, ha az adatok hiányzó értékeket tartalmaznak, de a featurizálás ki van kapcsolva.
    + Ki lett javítva a gyermekfuttatási hibák, amikor az adatok tartalmaznak egy nano-t, és a featurizálás ki van kapcsolva.
    + Az AutoML új hibakódot hoz létre a dataprepből, amikor a tartalom módosul az olvasás közben.
    + Frissült a normalizálás az előrejelző metrikákhoz a szemcse alapján.
    + Az előrejelzési kvantilisek továbbfejlesztett számítása, ha a visszacsatolási funkciók le vannak tiltva.
    + Ki van javítva a ritka bool mátrixkezelés az AutoML utáni magyarázatok számítása során.
  + **azureml-core**
    + Egy új metódus `run.get_detailed_status()` most az aktuális futtatás állapotának részletes magyarázatát jeleníti meg. Jelenleg csak az állapot magyarázatát `Queued` mutatja.
    + Adja image_name és image_label paramétereket a Model.package() fájlhoz a beépített csomag rendszerképének az újnanamikának engedélyezéséhez.
    + Új `set_pip_requirements()` metódus, amely a teljes pip szakaszt egyszerre [`CondaDependencies`](/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies) adja meg.
    + Engedélyezze a hitelesítő adatokkal nem ADLS Gen2 regisztrációját.
    + Javított hibaüzenet a helytelen típusú adatkészletek letöltésekor vagy csatlakoztatásakor.
    + Az idősorozat-adatkészletszűrő mintajegyzetfüzet frissítése a szűrőoptimalizálást partition_timestamp példákkal.
    + Módosítsa az sdk-t és a CLI-t úgy, hogy a privát végponti kapcsolat törlésekor a subscriptionId, resourceGroup, workspaceName, peConnectionName paramétereket fogadja el az ArmResourceId helyett.
    + A kísérleti dekorátor az osztály nevét jeleníti meg a könnyebb azonosítás érdekében.
    + A Modelleken belüli eszközök leírásai már nem jönnek létre automatikusan futtatás alapján.
  + **azureml-datadrift**
    + A create_from_model API megjelölése a DataDriftDetectorban elavultként.
  + **azureml-dataprep**
    + Javított hibaüzenet a helytelen adatkészlet-típus letöltésekor vagy csatlakoztatásakor.
  + **azureml-pipeline-core**
    + Kijavítva a hiba, amely a regisztrált adatkészleteket tartalmazó folyamatgráf deserializálása során volt kijavítva.
  + **azureml-pipeline-steps**
    + Az RScriptStep támogatja az azureml.core.environment fájlból származó RSection protokollt.
    + A passthru_automl_config paraméter el lett távolítva a nyilvános API-ból, és át lett `AutoMLStep` alakítva csak belső paraméterre.
  + **azureml-train-automl-client**
    + A helyi aszinkron, felügyelt környezet el lett távolítva az AutoML-ból. Minden helyi futtatás abban a környezetben fog futni, amelyből a futtatás elindult.
    + Ki vannak javítva az AutoML-futtatás felhasználói szkriptek nélkül való elküldésekor a pillanatképekkel kapcsolatos problémák.
    + Ki lett javítva a gyermekfuttatási hibák, amikor az adatok között nano és a featurizálás ki van kapcsolva.
  + **azureml-train-automl-runtime**
    + Az AutoML új hibakódot hoz létre az adat-előkészítésből, ha a tartalom módosul az olvasás közben.
    + Ki vannak javítva az AutoML-futtatás felhasználói szkriptek nélkül való elküldésekor a pillanatképekkel kapcsolatos problémák.
    + Ki lett javítva a gyermekfuttatási hibák, amikor az adatok között nano és a featurizálás ki van kapcsolva.
  + **azureml-train-core**
    + A pip-beállítások (például --extra-index-url) megadásának támogatása hozzáadva a paraméternek átadott pip-követelmények [`Estimator`](/python/api/azureml-train-core/azureml.train.estimator.estimator) `pip_requirements_file` fájljában.


## <a name="2020-08-03"></a>2020-08-03

### <a name="azure-machine-learning-sdk-for-python-v1110"></a>Azure Machine Learning SDK for Python 1.11.0

+ **Hibajavítások és fejlesztések**
  + **azure-cli-ml**
    + Javítva a modell-keretrendszer és a modell-keretrendszer nem a cli-modell regisztrációs útvonalának futtatott objektumában
    + A CLI amlcompute identity show parancsának kijavítva, amely a bérlőazonosítót és a rendszerbiztonsági tag azonosítóját mutatja 
  + **azureml-train-automl-client**
    + Új get_best_child () hozzáadva az AutoMLRunhoz, így a rendszer a társított modell letöltése nélkül lekéri az AutoML-futtatás legjobb gyermekfuttassanakat.
    + Hozzáadott ModelProxy-objektum, amely lehetővé teszi az előrejelzés vagy az előrejelzés futtatását egy távoli betanító környezetben a modell helyi letöltése nélkül.
    + Az AutoML nem kezelt kivételei mostantól egy ismert PROBLÉMÁK HTTP-oldalra mutatnak, ahol további információt talál a hibákról.
  + **azureml-core**
    + A modellnevek 255 karakter hosszúak is lehet.
    + Environment.get_image_details() visszatérési objektumtípus megváltozott. `DockerImageDetails` osztály lecserélve `dict` , a kép részletei az új osztálytulajdonságokból érhetők el. A módosítások visszamenőlegesen kompatibilisek.
    + Ki van javítva a Environment.from_pip_requirements() hiba a függőségi struktúra megőrzése érdekében
    + Kijavítottunk egy hibát, amely log_list hibát adna vissza, ha egy int és egy double is szerepelne ugyanabban a listában.
    + Miközben engedélyezi a privát kapcsolatot egy meglévő munkaterületen, vegye figyelembe, hogy ha a munkaterülethez számítási célok vannak társítva, akkor ezek a célok nem fognak működni, ha nem ugyanazon a virtuális hálózat mögött vannak, mint a munkaterület privát végpontja.
    + Nem kötelezővé lett, ha adatkészleteket használ a kísérletekben, és hozzáadta a és a `as_named_input` `as_mount` `as_download` `FileDataset` hez. A bemeneti név automatikusan létrejön a vagy a `as_mount` `as_download` hívása esetén.
  + **azureml-automl-core**
    + Az AutoML nem kezelt kivételei mostantól egy ismert PROBLÉMÁK HTTP-oldalra mutatnak, ahol további információt talál a hibákról.
    + Új get_best_child () hozzáadva az AutoMLRunhoz, hogy a társított modell letöltése nélkül lekérni az AutoML-futtatáshoz legmegfelelőbb gyermekfutatot.
    + Hozzáadott ModelProxy-objektum, amely lehetővé teszi az előrejelzés vagy az előrejelzés futtatását egy távoli betanító környezetben a modell helyi letöltése nélkül.
  + **azureml-pipeline-steps**
    + és `enable_default_model_output` `enable_default_metrics_output` jelölők hozzáadva a következő `AutoMLStep` hez: . Ezek a jelzők az alapértelmezett kimenetek engedélyezésére/letiltására használhatók.


## <a name="2020-07-20"></a>2020-07-20

### <a name="azure-machine-learning-sdk-for-python-v1100"></a>Azure Machine Learning SDK for Python 1.10.0

+ **Hibajavítások és fejlesztések**
  + **azureml-automl-core**
    + Az AutoML használata esetén, ha az útvonal az AutoMLConfig objektumba kerül, és még nem létezik, a rendszer automatikusan létrehoz egy elérési utat.
    + A felhasználók mostantól megadhatnak egy idősorozat-gyakoriságot az előrejelzési feladatokhoz a paraméter `freq` használatával.
  + **azureml-automl-runtime**
    + Az AutoML használata esetén automatikusan létrejön egy elérési út az AutoMLConfig objektumnak, és még nem létezik.
    + A felhasználók mostantól megadhatnak egy idősorozat-gyakoriságot az előrejelzési feladatokhoz a paraméter `freq` használatával.
    + Az AutoML-előrejelzés mostantól támogatja a működés közbeni kiértékelést, amely arra a használatra vonatkozik, hogy a tesztelési vagy ellenőrzési készlet hossza hosszabb a bemeneti horizontnál, és az ismert y_pred-értéket előrejelzési környezetként használják.
  + **azureml-core**
    + Ha futtatáskor nem letöltött fájlokat az adattárból, a rendszer figyelmeztető üzeneteket fog kinyomtatni.
    + A dokumentációja `skip_validation` hozzáadva a következő hez: `Datastore.register_azure_sql_database method` .
    + A felhasználóknak frissíteniük kell az SDK 1.10.0-s vagy magasabb verziójára egy automatikusan jóváhagyott privát végpont létrehozásához. Ez magában foglalja a virtuális hálózat mögött használható Notebook-erőforrást is.
    + Tegye elérhetővé a NotebookInfo et a munkaterület lekért válaszában.
    + A számítási célok listához és a számítási cél sikeres távoli futtatáson való sikerességéhez szükséges hívások módosításai. A számítási célokat lekért és a munkaterület számítási céljainak listáját lekért SDK-függvények mostantól működnek távoli futtatáskor.
    + Elalasztó üzeneteket adhat hozzá az azureml.core.image osztályok osztályleírásaihoz.
    + Kivételt ad vissza, és ha a munkaterület privát végpontjának létrehozása meghiúsul, a munkaterület és a függő erőforrások törlése.
    + A munkaterület termékváltozat-frissítésének támogatása a munkaterület frissítési metódusában.
  + **azureml-datadrift**
    + Frissítse a matplotlib verzióját a 3.0.2-esről a 3.2.1-es verzióra a Python 3.8 támogatásához.
  + **azureml-dataprep**
    + Webes URL-adatforrások támogatása a vagy a `Range` `Head` kéréssel. 
    + Nagyobb stabilitás a fájladatkészletek csatlakoztatásához és letöltéséhez.
  + **azureml-train-automl-client**
    + Kijavítottuk a setuptools eszközből való `RequirementParseError` eltávolítással kapcsolatos problémákat.
    + A "compute_target='local" használatával elküldött helyi futtatáshoz használja a dockert a Conda helyett
    + A konzolon kinyomtatott iteráció időtartama ki lett javítva. Korábban az iteráció időtartama néha futási idő és a futtatási létrehozási idő mínusz értékként lett kinyomtatva. Ez úgy lett kijavítva, hogy a futási idő és a futtatás kezdési ideje között egyenlő legyen.
    + Az AutoML használata esetén, ha az útvonal az AutoMLConfig objektumba kerül, és még nem létezik, a rendszer automatikusan létrehoz egy elérési utat.
    + A felhasználók mostantól megadhatnak egy idősorozat-gyakoriságot az előrejelzési feladatokhoz a paraméter `freq` használatával.
  + **azureml-train-automl-runtime**
    + Továbbfejlesztett konzolkimenet, ha a modell legjobb magyarázatai sikertelenek.
    + A bizalmas kifejezés eltávolításához blocked_models a bemeneti paramétert "blocked_models" névre.
      + A bizalmas kifejezés eltávolításához allowed_models a bemeneti paramétert "allowed_models" névre.
    + A felhasználók mostantól megadhatnak egy idősorozat-gyakoriságot az előrejelzési feladatokhoz a paraméter `freq` használatával.

  
## <a name="2020-07-06"></a>2020-07-06

### <a name="azure-machine-learning-sdk-for-python-v190"></a>Azure Machine Learning SDK for Python 1.9.0

+ **Hibajavítások és fejlesztések**
  + **azureml-automl-core**
    + A get_model_path() helyére AZUREML_MODEL_DIR AutoML automatikusan létrehozott pontozási szkriptben egy környezeti változót. Telemetriát is hozzáadtunk az init() hibák nyomon követéséhez.
    + Az `enable_cache` AutoMLConfig részeként nem adható meg
    + Kijavítottunk egy hibát, amely miatt a futtatás szolgáltatáshibák miatt meghiúsulhat adott előrejelzési futtatás során
    + Továbbfejlesztett hibakezelés adott modellek esetében a következő időszakban: `get_output`
    + Ki van javítva a fitted_model.fit(X, y) hívása az y transzformátorsal való besoroláshoz
    + Az előrejelzési feladatokhoz engedélyezett testreszabott előretöltési imputer
    + A rendszer új ForecastingParameters osztályt használ az előrejelzési paraméterek helyett, dict formátumban
    + Továbbfejlesztett cél késés automatikus dedetection
    + A TÖBBcsomópontos, több GPU-val elosztott featurizálás korlátozott rendelkezésre állása hozzáadva a OFTS-sel
  + **azureml-automl-runtime**
    + A ma már additív szezonalitás modellezése nem szorzó, hanem additív.
    + Javítva a hiba, amely akkor jelent meg, ha a rövid és a hosszú idősávok gyakoriságától eltérő gyakoriságok sikertelen futtatásokat eredményeznek.
  + **azureml-contrib-automl-dnn-vision**
    + Rendszer-/GPU-statisztikák és naplóátlagok gyűjtése betanítási és pontozási adatokhoz
  + **azureml-contrib-br**
    + Az enable-app-insights jelző támogatása hozzáadva a ManagedInferencingben
  + **azureml-core**
    + Ezeknek az API-knak egy érvényesítési paramétere, amely lehetővé teszi az ellenőrzés kihagyása, ha az adatforrás nem érhető el az aktuális számításból.
      + TabularDataset.time_before(end_time, include_boundary=True, validate=True)
      + TabularDataset.time_after(start_time, include_boundary=True, validate=True)
      + TabularDataset.time_recent(time_delta, include_boundary=True, validate=True)
      + TabularDataset.time_between(start_time, end_time, include_boundary=True, validate=True)
    + Keretrendszer-szűrési támogatás hozzáadva a modelllistához, valamint NCD AutoML-minta hozzáadva a notebookhoz
    + A Datastore.register_azure_blob_container és Datastore.register_azure_file_share (csak az SAS-jogkivonatot támogató beállítások) esetében frissítettük a mezőhöz szükséges dokumentumsringeket, hogy tartalmazzák a tipikus olvasási és írási forgatókönyvek minimális `sas_token` engedélykövetelményét.
    + A _with_auth paraméter elavult ws.get_mlflow_tracking_uri()
  + **azureml-mlflow**
    + Helyi virtuális gépi modellek üzembe helyezésének támogatása file:// a AzureML-MLflow
    + A _with_auth paraméter elavult ws.get_mlflow_tracking_uri()
  + **azureml-opendatasets**
    + A nemrég közzétett Covid-19 követési adatkészletek már elérhetők az SDK-val
  + **azureml-pipeline-core**
    + Kijelentkezés figyelmeztetés, ha az "azureml-defaults" nem része a pip-függőségnek
    + A Megjegyzés renderelésének javítása.
    + A tagolt fájlok PipelineOutputFileDataset elemhez való elemezéskor az idézett sortörések támogatása hozzáadva.
    + A PipelineDataset osztály elavult. További információ: https://aka.ms/dataset-deprecation. Az adatkészlet folyamatokkal való használatának elsajátítása: https://aka.ms/pipeline-with-dataset .
  + **azureml-pipeline-steps**
    + Az azureml-pipeline-steps dokumentumfrissítései.
    +  Támogatás hozzáadva a ParallelRunConfig-hez a felhasználók számára a környezetek a konfiguráció többi részével együtt vagy külön `load_yaml()` fájlban való definiálásához
  + **azureml-train-automl-client**.
    + Az `enable_cache` AutoMLConfig részeként nem adható meg
  + **azureml-train-automl-runtime**
    + A TÖBBcsomópontos, több GPU-val elosztott featurizálás korlátozott rendelkezésre állása a OFTS-sel.
    + Hibakezelés hozzáadva a nem kompatibilis csomagokhoz az ADB-alapú automatizált gépi tanulási futtatásban.
  + **azureml-widgets**
    + Az azureml-widgetek doc frissítései.

  
## <a name="2020-06-22"></a>2020-06-22

### <a name="azure-machine-learning-sdk-for-python-v180"></a>Azure Machine Learning SDK for Python 1.8.0
  
  + **Előzetes verziójú funkciók**
    + **azureml-contrib-fairness** A `azureml-contrib-fairness` csomag integrációt biztosít a Fairlearn és a [fairlearn](https://fairlearn.github.io) csomag nyílt forráskódú méltányosság-felmérése és a méltányosság Azure Machine Learning stúdió. A csomag lehetővé teszi, hogy az AzureML-futtatás részeként fel legyen töltve a modell méltányosságértékelési irányítópultja, és megjelenjenek a Azure Machine Learning stúdió

+ **Hibajavítások és fejlesztések**
  + **azure-cli-ml**
    + Az init-tároló naplói lekért naplók támogatása.
    + Új CLI-parancsok hozzáadva a ComputeInstance kezeléséhez
  + **azureml-automl-core**
    + A felhasználók mostantól engedélyezhetik a verem-együttes iterációt az idősorozat-feladatokhoz egy figyelmeztetéssel, amely szerint az túlilledhet.
    + Új típusú felhasználói kivétel lett hozzáadva, amely akkor merül fel, ha a gyorsítótár tartalmát illetéktelenül módosították
  + **azureml-automl-runtime**
    + Az osztályelosztási szolgáltatás nem lesz engedélyezve, ha a felhasználó letiltja a featurizálást.  
  + **azureml-contrib-itp**
    + A CmAks számítási típus támogatott. A betanításhoz saját AKS-fürtöt csatolhat a munkaterülethez.
  + **azureml-contrib-notebook**
    + A doc fejlesztései az azureml-contrib-notebook csomaghoz.
  + **azureml-contrib-pipeline-steps**
    + A doc fejlesztései az azureml-contrib-pipeline-steps csomaghoz.
  + **azureml-core**
    + További set_connection, get_connection, list_connections és delete_connection funkciók hozzáadása, amelyek segítségével az ügyfél a munkaterület kapcsolati erőforrásán fog működni
    + Dokumentációfrissítések az azureml-coore/azureml.exceptions csomaghoz.
    + Az azureml-core csomag dokumentációs frissítései.
    + A ComputeInstance osztály dokumentumfrissítése.
    + A doc fejlesztései az azureml-core/azureml.core.compute csomaghoz.
    + A dokumentum fejlesztései a webszolgáltatással kapcsolatos osztályokhoz az azureml-core-ban.
    + A felhasználó által kiválasztott adattár támogatása profilkészítési adatok tárolására
    + Kibontás és page_count tulajdonság hozzáadva a modelllista API-hoz
    + Kijavítva a hiba, amely miatt a felülírási tulajdonság eltávolítása miatt a beküldött futtatás deserializálási hibával meghiúsul.
    + Ki van javítva az inkonzisztens mappastruktúra egy fájl letöltésekor vagy csatlakoztatásakor.
    + A Parquet-fájlok adatkészletének to_spark_dataframe gyorsabban betöltődik, és támogatja az összes Parquet- és Spark SQL-adattípust.
    + Az init-tároló naplói lekért naplók támogatása.
    + Az AutoML-futtatásokat mostantól a párhuzamos futtatás lépés gyermekfuttassaként jelöli meg a rendszer.
  + **azureml-datadrift**
    + A doc fejlesztései az azureml-contrib-notebook csomaghoz.
  + **azureml-dataprep**
    + A Parquet-fájlok adatkészletének to_spark_dataframe gyorsabban betöltődik, és támogatja az összes Parquet- és Spark SQL-adattípust.
    + Az OutOfMemory-probléma jobb memóriakezelése a to_pandas_dataframe.
  + **azureml-interpret**
    + Az azureml-interpret frissítve az interpret-community 0.12-es verziójának használatára.*
  + **azureml-mlflow**
    + A doc fejlesztései az azureml-mlflow-ban.
    + Támogatja az AML-modelljegyzéket az MLFlow-val.
  + **azureml-opendatasets**
    + A Python 3.8 támogatása hozzáadva
  + **azureml-pipeline-core**
    + Frissítettük `PipelineDataset` a dokumentációját, hogy egyértelmű legyen, hogy belső osztályról van-e még információ.
    + A ParallelRunStep frissítései több értéket fogadnak el egy argumentumhoz, például: "--group_column_names", "Col1", "Col2", "Col3"
    + Eltávolítottuk passthru_automl_config követelményt a köztes adathasználathoz az AutoMLStep a folyamatokban.
  + **azureml-pipeline-steps**
    + Az azureml-pipeline-steps csomag doc-fejlesztései.
    + Eltávolítottuk passthru_automl_config követelményt a köztes adathasználathoz az AutoMLStep a folyamatokban.
  + **azureml-telemetry**
    + Az azureml-telemetria fejlesztései a doc használatával.
  + **azureml-train-automl-client**
    + Kijavítottunk egy hibát, amely miatt egy objektumon kétszer meg volt `experiment.submit()` `AutoMLConfig` hívva, és más viselkedést eredményezett.
    + A felhasználók mostantól engedélyezhetik a verem-együttes iterációt az idősorozat-feladatokhoz egy figyelmeztetéssel, amely szerint az túlilledhet.
    + Az AutoML-futtatás viselkedése userErrorException hibát váltott ki, ha a szolgáltatás felhasználói hibát jelez
    + Kijavít egy hibát, amely miatt a azureml_automl.log nem generált vagy hiányzó naplókat, amikor AutoML-kísérletet végez egy távoli számítási célon.
    + A kiegyensúlyozatlan besorolású besorolási adathalmazok esetében a Súlyelosztást alkalmazzuk, ha a szolgáltatás-kezelő megállapítja, hogy az alkiegyenlítő adatok esetében a Súlyozási terheléselosztás egy bizonyos küszöbértékgel javítja a besorolási feladat teljesítményét.
    + Az AutoML-futtatásokat mostantól a párhuzamos futtatás lépés gyermekfuttassaként jelöli meg a rendszer.
  + **azureml-train-automl-runtime**
    + Az AutoML-futtatás viselkedése userErrorException hibát váltott ki, ha a szolgáltatás felhasználói hibát jelez
    + Az AutoML-futtatásokat mostantól a párhuzamos futtatás lépés gyermekfuttassaként jelöli meg a rendszer.

  
## <a name="2020-06-08"></a>2020-06-08

### <a name="azure-machine-learning-sdk-for-python-v170"></a>Azure Machine Learning SDK for Python 1.7.0

+ **Hibajavítások és fejlesztések**
  + **azure-cli-ml**
    + Befejezte a modellprofil-készítés eltávolítását a parancssori felületi parancsok és csomagfüggőségek eltávolításával. A modellprofil-készítés a magban érhető el.
    + Az Azure CLI minimális verziójának frissítése a 2.3.0-s verzióra
  + **azureml-automl-core**
    + Jobb kivételüzenet a jellemzősítési lépésnél fit_transform() egyéni transzformátorparaméterek miatt.
    + Több nyelv támogatása hozzáadva a mélytanulás transzformátormodellek, például a FOGJA számára az automatizált gépi tanulásban.
    + Távolítsa el az elavult lag_length paramétert a dokumentációból.
    + Továbbfejlesztettük az előrejelzési paraméterek dokumentációját. A lag_length paraméter elavult.
  + **azureml-automl-runtime**
    + Kijavítottuk azt a hibát, amely akkor lépett fel, ha egy kategorikus oszlop üres az előrejelzés/tesztelés ideje alatt.
    + Javítsa ki a futási hibákat, amelyek akkor történnek, ha a visszacsatolási funkciók engedélyezve vannak, és az adatok rövid adatokat tartalmaznak.
    + Ki van javítva a duplikált időindex hibaüzenetének hibája, amikor a késések vagy a gördülő ablakok "auto" (automatikus) értékre voltak állítva.
    + Ki van javítva a Hiba az adathalmazokban található, a visszacsatolási funkciókat tartalmazó Halmaz- és Arima-modellek esetében.
    + A 1677-09-21 vagy 2262-04-11 előtti vagy utáni dátumok támogatása hozzáadva az előrejelzési feladatokban a dátum és idő oszlopaitól különböző oszlopokban. Továbbfejlesztett hibaüzenetek.
    + Továbbfejlesztettük az előrejelzési paraméterek dokumentációját. A lag_length paraméter elavult.
    + Jobb kivételüzenet a jellemzősítési lépésnél fit_transform() egyéni transzformátorparaméterek miatt.
    + Több nyelv támogatása a mélytanulás transzformátormodellek, például a FOG esetében az automatizált gépi tanulásban.
    + Az osErrorokat eredményező gyorsítótár-műveletek felhasználói hibát eredményeznek.
    + Ellenőrzések hozzáadva, hogy a betanítás és az ellenőrzési adatok száma és oszlopkészlete azonos legyen
    + Az automatikusan létrehozott AutoML pontozási szkript kijavítva, ha az adatok idézőjeleket tartalmaznak
    + Magyarázatok engedélyezése az AutoML-hez és az olyan együttes modellekhez, amelyek tartalmaznak egy modellel.
    + Egy közelmúltbeli ügyfélhiba feltárt egy élő webhelyről érkezett hibát, amelyben az osztályelosztási-sürgetés mellett naplóztunk üzeneteket, még akkor is, ha az osztályelosztási logika nincs megfelelően engedélyezve. Távolítsa el ezeket a naplókat/üzeneteket ezzel a le pr-val.
  + **azureml-cli-common**
    + Befejezte a modellprofil-készítés eltávolítását a parancssori felületi parancsok és csomagfüggőségek eltávolításával. A modellprofil-készítés a magban érhető el.
  + **azureml-contrib-reinforcementlearning**
    + Terheléstesztelési eszköz
  + **azureml-core**
    + A Script_run_config.py dokumentációjának változásai
    + Kijavít egy hibát, amely a folyamatbeküldő CLI kimenetének kinyomtatásával kapcsolatos
    + Az azureml-core/azureml.data dokumentációjának fejlesztései
    + Kijavította a tárfiók hdfs getconf paranccsal való lekért hibaét
    + Továbbfejlesztett register_azure_blob_container és register_azure_file_share dokumentáció
  + **azureml-datadrift**
    + Továbbfejlesztett implementáció az adat adatkészletek eltérésfigyelőinek letiltására és engedélyezésére
  + **azureml-interpret**
    + A magyarázat-ügyfélben a JSON-szerializálás előtt távolítsa el a NaN-eket vagy az Infs-eket az összetevőkből való feltöltéskor
    + Frissítsen az interpret-community legújabb verziójára, hogy javítsa a memóriaon túli hibákat a számos funkcióval és osztálysal kapcsolatos globális magyarázatok érdekében
    + A true_ys paraméter hozzáadása a magyarázatfeltöltéshez további funkciók engedélyezéséhez a Studio felhasználói felületén
    + A download_model_explanations() és a list_model_explanations() teljesítményének javítása
    + A notebookok kis finomhangolása a hibakeresés érdekében
  + **azureml-opendatasets**
    + Az azureml-opendatasets parancshoz az azureml-dataprep 1.4.0-s vagy újabb verziójára van szükség. Figyelmeztetés hozzáadva alacsonyabb verzió észlelése esetén
  + **azureml-pipeline-core**
    + Ez a módosítás lehetővé teszi, hogy a felhasználó opcionális runconfig paramétert adjon meg a moduleVersion-hez a modul hívásakor. Publish_python_script.
    + A Csomópontfiók engedélyezése folyamatparaméter lehet a ParallelRunStepben az azureml.pipeline.steps fájlban
  + **azureml-pipeline-steps**
    + Ez a módosítás lehetővé teszi, hogy a felhasználó opcionális runconfig paramétert adjon meg a moduleVersion-hez a modul hívásakor. Publish_python_script.
  + **azureml-train-automl-client**
    + Több nyelv támogatása hozzáadva a mélytanulás transzformátormodellek, például a FOGJA számára az automatizált gépi tanulásban.
    + Távolítsa el az elavult lag_length paramétert a dokumentációból.
    + Továbbfejlesztettük az előrejelzési paraméterek dokumentációját. A lag_length paraméter elavult.
  + **azureml-train-automl-runtime**
    + Magyarázatok engedélyezése az AutoML-hez és az olyan együttes modellekhez, amelyek tartalmaznak egy Modell modellt.
    + Dokumentációfrissítések az azureml-train-automl-* csomagokhoz.
  + **azureml-train-core**
    + A TensorFlow 2.1-es verziójának támogatása a PyTorch Estimatorban
    + Az azureml-train-core csomag fejlesztései.
  
## <a name="2020-05-26"></a>2020-05-26

### <a name="azure-machine-learning-sdk-for-python-v160"></a>Azure Machine Learning SDK for Python 1.6.0

+ **Új funkciók**
  + **azureml-automl-runtime**
    + Az AutoML-előrejelzés mostantól támogatja az előre megadott max-horizonton túli ügyfelek előrejelzését a modell újraképzése nélkül. Ha az előrejelzési cél a megadott maximális horizontnál távolabbi a jövőbe, az forecast() függvény továbbra is pont-előrejelzéseket ad a későbbi dátumra rekurzív működési mód használatával. Az új funkció illusztrációját az "Forecasting-forecast-function" jegyzetfüzet "A maximális horizontnál távolabbi előrejelzés" szakaszában láthatja a [mappában."](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)
  
  + **azureml-pipeline-steps**
    + A ParallelRunStep mostantól ki van adva, és az **azureml-pipeline-steps csomag része.** A meglévő ParallelRunStep az **azureml-contrib-pipeline-steps** csomagban elavult. Változások a nyilvános előzetes verzióról:
      + Hozzá van adva egy opcionális konfigurálható paraméter, amely az adott kötegek futtatási metódusának maximális hívását szabályozhatja. Az alapértelmezett `run_max_try` érték 3.
      + A PipelineParameters már nem automatikusan van generálva. Az alábbi konfigurálható értékek explicit módon állíthatók be PipelineParameter értékként.
        + mini_batch_size
        + node_count
        + process_count_per_node
        + logging_level
        + run_invocation_timeout
        + run_max_try
      + A szolgáltatás alapértelmezett process_count_per_node értéke 1-re módosul. A jobb teljesítmény érdekében a felhasználónak ezt az értéket kell finomhangolnia. Az ajánlott eljárás az, ha a GPU vagy a CPU-csomópont számaként van beállítva.
      + A ParallelRunStep nem injektál semmilyen csomagot, a felhasználónak **azureml-core** és **azureml-dataprep[pandas, fuse]** csomagokat kell tartalmaznia a környezet definíciójában. Ha egyéni Docker-rendszerképet használ a user_managed_dependencies akkor a felhasználónak telepítenie kell a Condát a lemezképre.
      
+ **Kompatibilitástörő változások**
  + **azureml-pipeline-steps**
    + Az azureml.dprep.Dataflow használata elavult az AutoMLConfig érvényes bemeneti típusaként
  + **azureml-train-automl-client**
    + Az azureml.dprep.Dataflow használata elavult az AutoMLConfig érvényes bemeneti típusaként

+ **Hibajavítások és fejlesztések**
  + **azureml-automl-core**
    + Ki van javítva az a hiba, amely miatt figyelmeztetés jelenik meg a felhasználótól az ügyfél `get_output` visszalépése során.
    + Frissítve lett a Mac, hogy a cudatoolkit=9.0-ra támaszkodjon, mivel még nem érhető el a 10-es verzióban.
    + Távoli számításon való betanításkor eltávolítja a rezdülő és az xgboost-modellekre vonatkozó korlátozásokat.
    + Továbbfejlesztett naplózás az AutoML-ben
    + Továbbfejlesztettük az előrejelzési feladatok egyéni tulajdonságokra vonatkozó hibakezelését.
    + Új funkciókkal lehetővé teszi a felhasználók számára, hogy az előrejelzések létrehozásához tartalmazniuk kell a nem megfelelő funkciókat.
    + A hibaüzenet frissítései a felhasználói hiba helyes megjelenítéséhez.
    + A cv_split_column_names használható training_data
    + Frissítse a kivételüzenet naplózását és a nyomkövetési visszacsatolást.
  + **azureml-automl-runtime**
    + Védőkorlátok engedélyezése a hiányzó értékbevallások előrejelzésére.
    + Továbbfejlesztett naplózás az AutoML-ben
    + Finomhangolt hibakezelés hozzáadva az adat-előkészítési kivételekhez
    + Távoli számítással betanított phrophet- és xgboost-modellek korlátozásának eltávolítása.
    + `azureml-train-automl-runtime` és `azureml-automl-runtime` frissítette a `pytorch` , és `scipy` `cudatoolkit` függőségeit. mostantól a , a és a `pytorch==1.4.0` `scipy>=1.0.0,<=1.3.1` `cudatoolkit==10.1.243` támogatott.
    + Továbbfejlesztettük az előrejelzési feladatok egyéni tulajdonságokra vonatkozó hibakezelését.
    + Továbbfejlesztettük az előrejelzési adatkészlet gyakoriságának észlelési mechanizmusát.
    + Ki van javítva a Hiba a Modell betanítása során egyes adatkészleteken.
    + Javult az előrejelzés során a maximális horizont automatikus észlelése.
    + Új funkciókkal lehetővé teszi a felhasználók számára, hogy az előrejelzések létrehozásához tartalmazniuk kell a nem megfelelő funkciókat.
    +  Olyan funkciókat ad hozzá az előrejelzési függvényhez, amelyek lehetővé teszik a betanított horizonton túli előrejelzéseket az előrejelzési modell újraképezése nélkül.
    + A cv_split_column_names használható training_data
  + **azureml-contrib-automl-dnn-forecasting**
    + Továbbfejlesztett naplózás az AutoML-ben
  + **azureml-contrib-br**
    + Windows-szolgáltatások támogatása hozzáadva a ManagedInferencingben
    + Távolítsa el a régi COMPUTE munkafolyamatokat, például a ATTACH COMPUTE, a SingleModelMirWebservice osztályt – A contrib-piac csomagba helyezett modellprofil-készítés törlése
  + **azureml-contrib-pipeline-steps**
    + Kisebb javítás YAML-támogatáshoz
    + A ParallelRunStep általánosan elérhetővé lett adva – az azureml.contrib.pipeline.steps egy elalasztó értesítéssel rendelkezik, és át lesz adva az azureml.pipeline.steps fájlba
  + **azureml-contrib-reinforcementlearning**
    + RL terheléstesztelési eszköz
    + Az RL estimator intelligens alapértelmezett értékekkel rendelkezik
  + **azureml-core**
    + Távolítsa el a régi COMPUTE munkafolyamatokat, például a ATTACH COMPUTE, a SingleModelMirWebservice osztályt – A contrib-piac csomagba helyezett modellprofil-készítés törlése
    + Ki van javítva a felhasználónak profilkészítési hiba esetén megadott információ: tartalmazza a kérés azonosítóját, és átrendelte az üzenetet, hogy kifejezőbb legyen. Új profilkészítési munkafolyamat hozzáadva a futók profilkészítéshez
    + Továbbfejlesztett hibaszöveg adatkészlet-végrehajtási hibák esetén.
    + Munkaterület privát hivatkozásának CLI-támogatása hozzáadva.
    + Egy opcionális paraméter hozzáadva a elemhez, amely lehetővé teszi az érvénytelen JSON-t tartalmazó sorok `invalid_lines` `Dataset.Tabular.from_json_lines_files` kezelésének megadását.
    + A számítási feladatok futtatásán alapuló létrehozást a következő kiadásban ki fogjuk számítani. Javasoljuk, hogy hozzon létre egy tényleges Amlcompute-fürtöt állandó számítási célként, és használja a fürt nevét számítási célként a futtatási konfigurációban. Lásd a példajegyzetfüzetet itt: aka.ms/amlcomputenb
    + Továbbfejlesztett hibaüzenetek adatkészlet-végrehajtási hibák esetén.
  + **azureml-dataprep**
    + Figyelmeztetést adott ki a pyarrow-verzió frissítésére.
    + Továbbfejlesztett hibakezelés és az adatfolyam sikertelen végrehajtása esetén visszaadott üzenet.
  + **azureml-interpret**
    + Az azureml-interpret csomag dokumentációs frissítései.
    + Ki van javítva az értelmezhetőségi csomagok és jegyzetfüzetek kompatibilitása a legújabb sklearn-frissítéssel
  + **azureml-opendatasets**
    + A None (Nincs) érték visszaadása, ha nem ad vissza adatot.
    + A teljesítmény javítása a to_pandas_dataframe.
  + **azureml-pipeline-core**
    + Gyorsjavítás a ParallelRunStephez, ahol a YAML-fájlból való betöltés megszakadt
    + A ParallelRunStep általánosan elérhetővé lett adva – az azureml.contrib.pipeline.steps egy elalasztó értesítéssel rendelkezik, és áttér az azureml.pipeline.steps-re – az új funkciók közé tartozik az 1. Adatkészletek mint PipelineParameter 2. Új paraméter run_max_retry 3. Konfigurálható append_row kimeneti fájl neve
  + **azureml-pipeline-steps**
    + Az azureml.dprep.Dataflow elavult a bemeneti adatok érvényes típusaként.
    + Gyorsjavítás a ParallelRunStephez, ahol a YAML-fájlból való betöltés megszakadt
    + A ParallelRunStep az általánosan elérhetővé lett adva – az azureml.contrib.pipeline.steps egy elalasztó értesítéssel rendelkezik, és át lesz adva az azureml.pipeline.steps-re – az új funkciók többek között a következők:
      + Adatkészletek mint PipelineParameter
      + Új paraméter run_max_retry
      + Konfigurálható append_row kimeneti fájl neve
  + **azureml-telemetry**
    + Frissítse a kivételüzenet naplózását és a nyomkövetési visszacsatolást.
  + **azureml-train-automl-client**
    + Továbbfejlesztett naplózás az AutoML-ben
    + A hibaüzenet frissítései a felhasználói hiba helyes megjelenítéséhez.
    + A cv_split_column_names használható training_data
    + A bemeneti adatok érvényes típusa az azureml.dprep.Dataflow.
    + Frissítve lett a Mac, hogy a cudatoolkit=9.0-ra támaszkodjon, mivel még nem érhető el a 10-es verzióban.
    + A phrophet- és xgboost-modellekre vonatkozó korlátozások eltávolítása távoli számításon való betanításkor.
    + `azureml-train-automl-runtime` és `azureml-automl-runtime` frissítette a `pytorch` , és `scipy` `cudatoolkit` függőségeit. Mostantól támogatjuk a `pytorch==1.4.0` , a és a `scipy>=1.0.0,<=1.3.1` `cudatoolkit==10.1.243` et.
    + Új funkciókkal lehetővé teszi a felhasználók számára, hogy az előrejelzések létrehozásához tartalmazniuk kell a nem megfelelő funkciókat.
  + **azureml-train-automl-runtime**
    + Továbbfejlesztett naplózás az AutoML-ben
    + Finomhangolt hibakezelés hozzáadva az adat-előkészítési kivételekhez
    + A phrophet- és xgboost-modellekre vonatkozó korlátozások eltávolítása távoli számításon való betanításkor.
    + `azureml-train-automl-runtime` és `azureml-automl-runtime` frissítette a `pytorch` , és `scipy` `cudatoolkit` függőségeit. Mostantól támogatjuk a `pytorch==1.4.0` , a és a `scipy>=1.0.0,<=1.3.1` `cudatoolkit==10.1.243` et.
    + Frissíti a hibaüzenetet, hogy megfelelően jelenítse meg a felhasználói hibát.
    + A cv_split_column_names használható training_data
  + **azureml-train-core**
    + Új HyperDrive-specifikus kivételek hozzáadva. Az azureml.train.hyperdrive mostantól részletes kivételeket ad vissza.
  + **azureml-widgets**
    + Az AzureML-widgetek nem jelennek meg a JupyterLab-ben
  

## <a name="2020-05-11"></a>2020-05-11

### <a name="azure-machine-learning-sdk-for-python-v150"></a>Azure Machine Learning SDK for Python 1.5.0

+ **Új funkciók**
  + **Előzetes verziójú funkciók**
    + **azureml-contrib-reinforcementlearning**
        + Azure Machine Learning a Ray keretrendszer használatával előzetes verziójú támogatást ad a meg [megerősítési tanuláshoz.](https://ray.io) A `ReinforcementLearningEstimator` lehetővé teszi a megerős tanulási ügynökök betanítát a GPU- és CPU-számítási célokon a Azure Machine Learning.

+ **Hibajavítások és fejlesztések**
  + **azure-cli-ml**
    + Kijavít egy véletlenül maradt figyelmeztető naplót az előző le pr-ban. A napló hibakeresésre lett használva, és véletlenül maradt hátra.
    + Hibajavítás: tájékoztassa az ügyfeleket a profilkészítés során történt részleges hibáról
  + **azureml-automl-core**
    + Az AutoML-előrejelzésben felgyorsíthatja a Sorozat/AutoArima modell felgyorsítása azáltal, hogy engedélyezi a párhuzamos illesztés funkcióját az idősorok számára, ha az adatkészletek több idősort tartalmaznak. Az új funkció használatához ajánlott a "max_cores_per_iteration = -1" (azaz az összes rendelkezésre álló processzormag használata) beállítása az AutoMLConfigben.
    + A KeyError kijavítva a konzolfelületen található védőkorlátokon
    + Kijavítottuk a hibaüzenetet a experimentation_timeout_hours
    + Az AutoML-hez elavult Tensorflow-modellek.
  + **azureml-automl-runtime**
    + Kijavítottuk a hibaüzenetet a experimentation_timeout_hours
    + Kijavítottuk a nem besorolt kivételt, amikor deserializálást próbált meg a gyorsítótárból
    + Az AutoML-előrejelzésben felgyorsíthatja a Sorozat/AutoArima modell felgyorsítása azáltal, hogy engedélyezi a párhuzamos illesztés funkcióját az idősorok számára, ha az adatkészletek több idősort tartalmaznak.
    + Ki van javítva az előrejelzés engedélyezett gördülő ablakokkal az adatkészleteken, ahol a tesztelési/előrejelzési készlet nem tartalmazza a betanításkészlet egyik grain beállítását sem.
    + A hiányzó adatok továbbfejlesztett kezelése
    + Ki van javítva az előrejelzési intervallumokkal kapcsolatos hiba az adathalmazok előrejelzése során, amelyek idősorokat tartalmaznak, és amelyek nincsenek időben igazítva.
    + Jobban érvényesítve lett az adatalakzat az előrejelzési feladatokhoz.
    + Továbbfejlesztett gyakoriságészlelés.
    + Jobb hibaüzenetet hozott létre, ha nem lehet létrehozni az előrejelzési feladatok keresztellenőrzési deduktálásait.
    + Javítva van a konzolfelület a hiányzó értékkorlátok megfelelő kinyomtatás érdekében.
    + Adattípus-ellenőrzések kényszerítését a cv_split_indices AutoMLConfigben.
  + **azureml-cli-common**
    + Hibajavítás: tájékoztassa az ügyfeleket a részleges hibákról a profilkészítés során
  + **azureml-contrib-br**
    + Hozzáad egy azureml.contrib.kb.RevisionStatus osztályt, amely továbbítja az aktuálisan üzembe helyezett LETT változat és a felhasználó által megadott legújabb verzió adatait. Ez az osztály a "webszolgáltatás" attribútum alatt található aWebservice Deployment_status objektumban.
    + Engedélyezi a Frissítést aWebservice típusú webszolgáltatásokon és annak SingleModelMirWebservice gyermekosztályán.
  + **azureml-contrib-reinforcementlearning**
    + A Ray 0.8.3 támogatása hozzáadva
    + Az AmlWindowsCompute csak a Azure Files tárolóként való tárolást támogatja
    + Átnevezve health_check_timeout: health_check_timeout_seconds
    + Kijavítottunk néhány osztály-/metódusleírást.
  + **azureml-core**
    + WasB -> blobkonverziók engedélyezése Azure Government kínai felhőkben.
    + Kijavítottunk egy hibát, amely lehetővé teszi, hogy az olvasói szerepkörök az az ml run CLI-parancsokkal lekért futtatásos információkat használjanak
    + Eltávolítottuk a szükségtelen naplózást a távoli Azure ML-futtatás során bemeneti adatkészletekkel.
    + Az RCranPackage mostantól támogatja a CRAN-csomagverzió "version" paraméterét.
    + Hibajavítás: tájékoztassa az ügyfeleket a részleges hibákról a profilkészítés során
    + Európai stílusú lebegőutas kezelés hozzáadva az azureml-core-hoz.
    + A munkaterület privát kapcsolati funkcióinak engedélyezése az Azure ML SDK-ban.
    + Amikor tabularDatasetet hoz létre a használatával, a logikai argumentum beállításával megadhatja, hogy az üres értékeket None értékként vagy üres `from_delimited_files` sztringként kell-e `empty_as_string` betölteni.
    + Európai stílusú lebegőutas kezelés hozzáadva az adatkészletek számára.
    + Továbbfejlesztett hibaüzenetek az adatkészlet csatlakoztatási sikertelenségére.
  + **azureml-datadrift**
    + Az SDK Sodródás eredménylekérdezése olyan hibát tartalmazott, amely nem különbözteti meg a minimális, maximális és átlagos jellemzőmetrikákat, ami duplikált értékeket eredményezett. Ezt a hibát úgy javítottuk ki, hogy a cél vagy az alapérték a metrikanevek előtagját tartalmazza. Előtte: duplikált min, max, mean. Utána: target_min, target_max, target_mean, baseline_min, baseline_max, baseline_mean.
  + **azureml-dataprep**
    + Az írási korlátozott Python-környezetek kezelésének javítása az adatkéséshez szükséges .NET-függőségek biztosításakor.
    + Kijavítottuk az adatfolyam létrehozását a kezdő üres rekordokkal.
    + Hibakezelési lehetőségek hozzáadva a `to_partition_iterator` következőhez hasonlóhoz: `to_pandas_dataframe` .
  + **azureml-interpret**
    + Csökkentett magyarázatok elérésiút-hosszkorlátja a Windows-korlát túllépésének valószínűségének csökkentése érdekében
    + Hibajavítás az utánzható magyarázóval létrehozott ritka magyarázatok esetében lineáris helyettes modell használatával.
  + **azureml-opendatasets**
    + Ki van javítva az MNIST-oszlopok sztringként való elemzési problémája, amelynek int típusúnak kell lennie.
  + **azureml-pipeline-core**
    + A modul regenerate_outputs a ModulStepbe ágyazott modul használata esetén.
  + **azureml-train-automl-client**
    + Az AutoML-hez elavult Tensorflow-modellek.
    + Kijavítva, hogy a felhasználók engedélyezték a nem támogatott algoritmusok listázását helyi módban
    + Az AutoMLConfig dokumentumjavítása.
    + Adattípus-ellenőrzések kényszerítését cv_split_indices AutoMLConfig bemenetében.
    + Ki van javítva az AutoML-futtatás sikertelen show_output
  + **azureml-train-automl-runtime**
    + Kijavítottunk egy hibát az együttes iterációkban, amely megakadályozta a modell letöltésének időtúllépését.
  + **azureml-train-core**
    + Ki van javítva az elírás az azureml.train.dnn.Nccl osztályban.
    + A PyTorch Estimator 1.5-ös verziójának támogatása
    + Kijavítottuk a problémát, amely miatt a keretrendszer rendszerképe nem hívható be Azure Government régióban a betanítás keretrendszerbecsülők használata során

  
## <a name="2020-05-04"></a>2020-05-04
**Új jegyzetfüzet-felhasználói élmény**

Mostantól gépi tanulási jegyzetfüzeteket és -fájlokat hozhat létre, szerkeszthet és oszthat meg közvetlenül a jegyzetfüzetek Studio webes Azure Machine Learning. A Python [SDK-ban](/python/api/overview/azure/ml/intro) elérhető összes osztályt és metódust használhatja Azure Machine Learning jegyzetfüzetek első lépései [itt](./how-to-run-jupyter-notebooks.md)

**Új funkciók:**

+ A VS Code által használt továbbfejlesztett szerkesztő (Monaco-szerkesztő) 
+ A felhasználói felület/felhasználói felület fejlesztései
+ Cella eszköztára
+ Új jegyzetfüzet-eszköztár és Számítási vezérlők
+ Jegyzetfüzet állapotsávja 
+ Beágyazott kernelváltás
+ R-támogatás
+ Akadálymentességgel és honosítással kapcsolatos fejlesztések
+ Parancskatapaletta
+ További billentyűparancsok
+ Automatikus mentés
+ Jobb teljesítmény és megbízhatóság

A studióból elérheti a következő webes szerzői eszközöket:
    
| Webalapú eszköz  |     Description  |
|---|---|
| Azure ML Studio-jegyzetfüzetek   |     Első osztályonként használható szerzői műveletek jegyzetfüzetfájlokhoz, és az Azure ML Python SDK-ban elérhető összes művelet támogatása. | 

## <a name="2020-04-27"></a>2020-04-27

### <a name="azure-machine-learning-sdk-for-python-v140"></a>Azure Machine Learning SDK for Python 1.4.0

+ **Új funkciók**
  + Az AmlCompute-fürtök mostantól támogatják a felügyelt identitások beállítását a fürtön a kiépítéskor. Egyszerűen adja meg, hogy rendszer által hozzárendelt vagy felhasználó által hozzárendelt identitást szeretne használni, és adja meg az utóbbi identitásazonosítóját. Ezután úgy állíthat be engedélyeket a különböző erőforrások eléréséhez, mint a Storage vagy az ACR, hogy a számítás identitásával biztonságosan hozzáférjen az adatokhoz az AmlCompute által jelenleg alkalmazott jogkivonat-alapú megközelítés helyett. A paraméterekkel kapcsolatos további információkért tekintse meg az SDK-referenciát.
  

+ **Kompatibilitástörő változások**
  + Az AmlCompute-fürtök támogatták a futtatásalapú létrehozás előzetes verziójú funkcióját, amelyet két hét múlva tervezünk elajánlni. Az Amlcompute osztály használatával továbbra is létrehozhat állandó számítási célokat, de az "amlcompute" azonosító futási konfigurációban való megadásának konkrét megközelítése a közeljövőben nem lesz támogatott. 

+ **Hibajavítások és fejlesztések**
  + **azureml-automl-runtime**
    + Az oszlopokban található egyedi értékek számának kiszámításakor engedélyezze a nem olvasható típus támogatását.
  + **azureml-core**
    + Nagyobb stabilitás a Azure Blob Storage a TabularDataset használatával.
    + Továbbfejlesztett dokumentáció a `grant_workspace_msi` `Datastore.register_azure_blob_store` paraméterhez.
    + Kijavítottuk a hibát, `datastore.upload` amely a `src_dir` argumentumot vagy végződéssel `/` `\` támogatja.
    + Műveletre használható hibaüzenet lett hozzáadva, amikor egy olyan Azure Blob Storage adattárba próbál feltölteni, amely nem rendelkezik hozzáférési kulccsal vagy SAS-jogkivonattal.
  + **azureml-interpret**
    + Felső korlát hozzáadva a fájlmérethez a feltöltött magyarázatok vizualizációs adataihoz.
  + **azureml-train-automl-client**
    + Explicit módon ellenőrizze, hogy label_column_name & weight_column_name-e, hogy az AutoMLConfig sztring típusú-e.
  + **azureml-contrib-pipeline-steps**
    + A ParallelRunStep mostantól folyamatparaméterként támogatja az adatkészletet. A felhasználó mintaadatkészlettel is felépíthet egy folyamatot, és módosíthatja az azonos típusú (fájl- vagy táblázatos) bemeneti adatkészletet az új folyamatfutathoz.

  
## <a name="2020-04-13"></a>2020-04-13

### <a name="azure-machine-learning-sdk-for-python-v130"></a>Azure Machine Learning SDK for Python 1.3.0

+ **Hibajavítások és fejlesztések**
  + **azureml-automl-core**
    + További telemetria hozzáadva a betanítás utáni műveletekhez.
    + Felgyorsítja az automatikus ARIMA-betanítás felgyorsítását a 100-asnál hosszabb sorozatú négyzetek (CSS) feltételes összegének használatával. A használt hossz állandóként ARIMA_TRIGGER_CSS_TRAINING_LENGTH a TimeSeriesInternal osztályban a következő időpontban: /src/azureml-automl-core/azureml/automl/core/shared/constants.py
    + Javult az előrejelzési futtatás felhasználói naplózása, így a naplóban további információk jelennek meg arról, hogy melyik fázis fut éppen
    + Nem engedélyezett target_rolling_window_size 2-esnél kisebb értékekre beállítani
  + **azureml-automl-runtime**
    + Javítottunk a duplikált időbélyegek esetén megjelenő hibaüzeneten.
    + Nem engedélyezett target_rolling_window_size, hogy a 2-esnél kisebb értékekre legyen beállítva.
    + Kijavítva a késés imputálásának hibája. A problémát az okozta, hogy nem áll rendelkezésre elegendő megfigyelés egy sorozat szezonális bontásához. A "szezonálisan dedukált" adatokkal részleges automatikus korrelációs függvény (PACF) számítható ki a késés hosszának meghatározásához.
    + Oszlop rendeltetésének testreszabása engedélyezett az előrejelzési feladatokhoz a featurizálási konfigurációval. A numerikus és a kategorikus oszlopcélú előrejelzési feladatok mostantól támogatottak.
    + A drop column featurization testreszabás engedélyezve az előrejelzési feladatokhoz a featurizálási konfiguráció alapján.
    + Engedélyezett imputálás testreszabása az előrejelzési feladatokhoz a featurizálási konfigurációval. Mostantól támogatott a céloszlop és a középérték, a medián, most_frequent és a betanítás adatainak állandó értékbetanítása.
  + **azureml-contrib-pipeline-steps**
    + A ParallelRunConfignek átveendő számítási sztringnevek elfogadása
  + **azureml-core**
    +  Hozzá van adva az Environment.clone(new_name) API a Környezeti objektum másolatának létrehozásához
    +  Environment.docker.base_dockerfile elfogadja a fájlatalútot. Ha meg tudja oldani a fájlt, a rendszer beolvassa a tartalmat base_dockerfile környezeti tulajdonságba
    + Automatikusan alaphelyzetbe állítja a kölcsönösen kizáró értékeket a base_image és base_dockerfile, amikor a felhasználó manuálisan állít be értéket a ker Environment.docmezőben
    + Új user_managed az RSection-ban, amely azt jelzi, hogy a környezetet felhasználó vagy az AzureML kezeli-e.
    + Adatkészlet: Az adatkészlet letöltési hibája javítva, ha az adat elérési útja Unicode-karaktereket tartalmaz.
    + Adatkészlet: Továbbfejlesztett csatlakoztatási gyorsítótárazás az Azure Machine Learning Compute minimális lemezterületre vonatkozó követelményének megfelelően, így elkerülhető, hogy a csomópont használhatatlanná válik, és megszakítsa a feladatot.
    + Adatkészlet: Az idősorozat-oszlop indexét akkor adjuk hozzá, ha egy idősorozat-adatkészletet pandas-adatkeretként használ, amellyel felgyorsíthatja az idősorozat-alapú adatelérést.  Korábban az index neve megegyezik az időbélyegző oszlop nevével, ami összezavarta a felhasználókat, ami a tényleges időbélyegző-oszlop, és amely az index. Most nem adunk meg konkrét nevet az indexnek, mivel nem használható oszlopként. 
    + Adatkészlet: Az adatkészlet hitelesítésének kijavítása a szuverén felhőben.
    + Adatkészlet: Javítva a hiba az Azure PostgreSQL-adattárakból `Dataset.to_spark_dataframe` létrehozott adatkészletek esetén.
  + **azureml-interpret**
    + Globális pontszámok hozzáadva a vizualizációhoz, ha a helyi fontosságértékek ritkaak
    + Frissítve lett az azureml-interpret a interpret-community 0.9 használatára.*
    + Ki lett javítva a ritka kiértékelési adatokkal kapcsolatos magyarázatok letöltésével kapcsolatos hiba
    + A magyarázatobjektum ritka formátumának támogatása hozzáadva az AutoML-ben
  + **azureml-pipeline-core**
    + ComputeInstance mint számítási cél támogatása folyamatokban
  + **azureml-train-automl-client**
    + További telemetria hozzáadva a betanítás utáni műveletekhez.
    + A regresszió kijavítva a korai leállítás során
    + Az azureml.dprep.Dataflow elavult a bemeneti adatok érvényes típusaként.
    +  Az alapértelmezett AutoML-kísérlet időkorrekta 6 nap.
  + **azureml-train-automl-runtime**
    + További telemetria hozzáadva a betanítás utáni műveletekhez.
    + ritka AutoML-végpontok támogatása hozzáadva
  + **azureml-opendatasets**
    + További telemetria hozzáadva a szolgáltatásmonitorhoz.
    + Front Door engedélyezése a blob számára a stabilitás növelése érdekében 

## <a name="2020-03-23"></a>2020-03-23

### <a name="azure-machine-learning-sdk-for-python-v120"></a>Azure Machine Learning SDK for Python 1.2.0

+ **Kompatibilitástörő változások**
  + A Python 2.7 támogatása elejtve

+ **Hibajavítások és fejlesztések**
  + **azure-cli-ml**
    + Hozzáadja a "--subscription-id" értéket `az ml model/computetarget/service` a parancssori felület parancsaihoz
    + Ügyfél által felügyelt kulcs (CMK) átadásának támogatása vault_url, key_name és key_version ACI üzembe helyezéséhez
  + **azureml-automl-core** 
    + Testreszabott imputálás engedélyezése állandó értékkel az X és y adat-előrejelzési feladatokhoz.
    + Ki van javítva a hibája, amely a hibaüzenetek felhasználónak való megjelenítésével kapcsolatban lépett fel.    
  + **azureml-automl-runtime**
    + Ki van javítva az adathalmazok előrejelzésének problémája, amely csak egy sorral tartalmaz grainseket
    + Csökkentette az előrejelzési feladatokhoz szükséges memória mennyiségét.
    + Jobb hibaüzenetek hozzáadva, ha az időoszlop formátuma helytelen.
    + Testreszabott imputálás engedélyezése állandó értékkel az X és y adat-előrejelzési feladatokhoz.
  + **azureml-core**
    + A ServicePrincipal környezeti változókból való betöltésének támogatása: AZUREML_SERVICE_PRINCIPAL_ID, AZUREML_SERVICE_PRINCIPAL_TENANT_ID és AZUREML_SERVICE_PRINCIPAL_PASSWORD
    + A új paramétere a következőhöz: Alapértelmezés szerint ( ), minden sortörés, beleértve az idézett mezőértékekben megadottakat is, rekordtörésként `support_multi_line` `Dataset.Tabular.from_delimited_files` lesz `support_multi_line=False` értelmezve. Az adatok ily módon történő beolvasása gyorsabb, és több processzormag párhuzamos végrehajtására van optimalizálva. Ez azonban azt eredményezheti, hogy csendesen több rekordot fog előállítani helytelen mezőértékekkel. Ezt a következőre kell beállítani: ha a tagolt fájlokról tudjuk, hogy idézett `True` sortöréseket tartalmaznak.
    + A regisztráció lehetősége hozzáadva ADLS Gen2 a Azure Machine Learning CLI-hez
    + A tabularDataset with_timestamp_columns() metódusának "fine_grain_timestamp" paramétere átnevezve "timestamp" névre, a "coarse_grain_timestamp" paraméter pedig "partition_timestamp" névre, hogy jobban tükrözze a paraméterek használatát.
    + A kísérlet nevének maximális hossza 255-re nőtt.
  + **azureml-interpret**
    + Frissítve lett az azureml-interpret a community 0.7 értelmezése érdekében.*
  + **azureml-sdk**
    + A kompatibilis Tilde-verzióval való függőségváltás a javítás támogatásához a kiadás előtti és a stabil kiadásokban.


## <a name="2020-03-11"></a>2020-03-11

### <a name="azure-machine-learning-sdk-for-python-v115"></a>Azure Machine Learning SDK for Python 1.1.5

+ **Funkció elalasztása**
  + **Python 2.7**
    + A python 2.7-et támogató utolsó verzió

+ **Kompatibilitástörő változások**
  + **Szemantikus verziószámozás 2.0.0**
    + Az 1.1-es verziótól kezdődően az Azure ML Python SDK a szemantikai verziószámozás 2.0.0-s verzióját használja. [További információt itt olvashat.](https://semver.org/) Minden további verzió az új számozási sémát és szemantikai verziószámozási szerződést követi. 

+ **Hibajavítások és fejlesztések**
  + **azure-cli-ml**
    + A konzisztencia érdekében módosítsa a végpont CLI-parancsnevét az "az ml endpoint aks" névről az "az ml végpont valós idejű" névre.
    + cli-telepítési utasítások frissítése a stabil és kísérleti ág cli-hez
    + Az egypéldányos profilkészítés ki lett javítva a javaslat előállításához, és elérhető lett a core SDK-ban.
  + **azureml-automl-core**
    + Engedélyezte a Batch módú deduktenciát (több sort vesz fel egyszer) az AutoML ONNX-modellekhez
    + Javult az adathalmazok gyakoriságának észlelése, az adatok hiánya vagy a szabálytalan adatpontok
    + Lehetővé tette a domináns gyakoriságnak meg nem felő adatpontok eltávolítását.
    + Módosította a konstruktor bemenetét, hogy a lehetőségek listájával alkalmazza a imputációs beállításokat a megfelelő oszlopokra.
    + A hibanaplózás tovább lett javítva.
  + **azureml-automl-runtime**
    + Ki lett javítva a hiba, amely akkor történt, ha a tesztelési készletben nem jelent meg a betanításkészletben a rendszer
    + A y_query az előrejelzési szolgáltatás pontozása során el lett távolítva
    + Ki van javítva az előrejelzési probléma, amely akkor jelent meg, ha az adatkészlet rövid részletes adatokat tartalmaz, és hosszú időre vonatkozó hézagokat tartalmaz.
    + Ki lett javítva a hiba, amikor az automatikus maximális horizont be van kapcsolva, és a dátumoszlop sztringek formájában tartalmaz dátumokat. Megfelelő konverziós és hibaüzenetek hozzáadva a következő időponthoz: ha az átalakítás dátumra nem lehetséges
    + Natív NumPy és SciPy használata a (helyi AutoML-futtatáshoz használt) FileCacheStore köztes adatainak szerializálására és de szerializálására
    + Kijavítottunk egy hibát, amely miatt a sikertelen gyermekfutozások Futó állapotban elakadtak.
    + A featurizálás nagyobb sebessége.
    + Kijavítottuk a gyakorisági ellenőrzést a pontozás során, így az előrejelzési feladatok nem igényelnek szigorú gyakorisági egyenértékűséget a betanítás és a tesztelési készlet között.
    + Módosította a konstruktor bemenetét, hogy a lehetőségek listájával alkalmazza a imputációs beállításokat a megfelelő oszlopokra.
    + Kijavítottuk a késési típus kiválasztásával kapcsolatos hibákat.
    + Ki van javítva az adathalmazok besorolás nélküli hibája, amely az egyetlen sorral rendelkező grains -eket tartalmaz
    + Ki van javítva a gyakoriságészlelés lassúsága.
    + Kijavít egy hibát az AutoML-kivételkezelésben, amely miatt a betanítás sikertelenségének valódi okát egy AttributeError váltotta fel.
  + **azureml-cli-common**
    + Az egypéldányos profilkészítés ki lett javítva a javaslat előállításához, és elérhető lett a core SDK-ban.
  + **azureml-contrib-br**
    + A Hozzáférési jogkivonat lekéréséhez funkciókat ad hozzá aWebservice Osztályhoz
    + Jogkivonat-hitelesítés használata alapértelmezés szerint aÉseWebservice.run() hívása során aWebservice.run() szolgáltatáshoz – Csak akkor frissítsen, ha a hívás meghiúsul
    + A webszolgáltatás üzembe helyezéséhez megfelelő Skus [Standard_DS2_v2, Standard_F16, Standard_A2_v2] szükséges a [Ds2v2, A2v2 és F16] helyett.
  + **azureml-contrib-pipeline-steps**
    + Nem kötelező paraméter side_inputs a ParallelRunStep elemhez. Ezzel a paraméterrel csatlakoztathat egy mappát a tárolón. Jelenleg a DataReference és a PipelineData típusok támogatottak.
    + A ParallelRunConfigben átadott paraméterek felülírhatók a folyamatparaméterek átadása után. Az új támogatott folyamatparaméterek aml_mini_batch_size, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout (aml_node_count és aml_process_count_per_node már részei a korábbi kiadásnak).
  + **azureml-core**
    + Az üzembe helyezett AzureML-webszolgáltatások alapértelmezés szerint naplózást `INFO` fognak használni. Ez a környezeti változónak az üzembe helyezett szolgáltatásban `AZUREML_LOG_LEVEL` való beállításával szabályozható.
    + A Python SDK a Felderítési szolgáltatás használatával "api" végpontot használ a folyamatok helyett.
    + Cserélje le az új útvonalakat az összes SDK-hívásban.
    + A ModelManagementService hívásának útválasztása egy új, egységes struktúrára változott.
      + Elérhetővé tette a munkaterület-frissítési módszert.
      + Új image_build_compute a munkaterület frissítési metódusában, hogy a felhasználó frissítheti a rendszerkép buildszámának számítási kapacitását.
    + Elalasztási üzenetek hozzáadva a régi profilkészítési munkafolyamathoz. A profilkészítés processzor- és memóriakorlátja kijavítva.
    + Az RSection hozzá van adva a környezet részeként az R-feladatok futtatásához.
    + A érvényesítése hozzáadva, hogy hibát jelentsen, ha az adatkészlet forrása nem érhető el vagy `Dataset.mount` nem tartalmaz adatokat.
    + További paraméterként hozzáadva a Datastore CLI-hez az Azure Blob-tároló regisztrálására, amely lehetővé teszi a virtuális hálózat mögötti `--grant-workspace-msi-access` blobtároló regisztrálását.
    + Az egypéldányos profilkészítés ki lett javítva a javaslat előállításához, és elérhetővé lett a core sdk-ban.
    + Kijavítottuk a hibát a aks.py _deploy.
    + Ellenőrzi a feltöltött modellek integritását a csendes tárolási hibák elkerülése érdekében.
    + A felhasználó mostantól megadhat egy értéket a hitelesítési kulcshoz, amikor újra létrehozza a webszolgáltatások kulcsait.
    + Kijavítva a hiba, amely miatt a nagybetűk nem használhatók az adatkészlet bemeneti neveként.
  + **azureml-defaults**
    + `azureml-dataprep` A most már telepítve lesz a `azureml-defaults` részeként. Az adatelőkészítés[fuse] manuális telepítése már nem szükséges a számítási célokon az adatkészletek csatlakoztatásához.
  + **azureml-interpret**
    + Frissítve lett az azureml-interpret a community 0.6 értelmezéséhez.*
    + Frissítve lett az azureml-interpret, hogy a interpret-community 0.5.0-ra támaszkodva
    + Azureml-stílusú kivételek hozzáadva az azureml-interprethez
    + Javítva a DeepScoringExplainer szerializálás a Keras-modellek esetében
  + **azureml-mlflow**
    + Szuverén felhők támogatása hozzáadva az azureml.mlflow-hoz
  + **azureml-pipeline-core**
    + A folyamatkötetek pontozási jegyzetfüzete mostantól a ParallelRunStepet használja
    + Kijavítottunk egy hibát, amely miatt a PythonScriptStep eredményei helytelenül újra felhasználhatók az argumentumlista módosítása ellenére
    + Hozzá van adva az oszlopok típusának beállításának lehetősége a parse_* metódusok a következőn való hívakor: `PipelineOutputFileDataset`
  + **azureml-pipeline-steps**
    + Áthelyezte `AutoMLStep` a et a `azureml-pipeline-steps` csomagba. A elavult a következőn `AutoMLStep` `azureml-train-automl-runtime` belül: .
    + Dokumentációs példa hozzáadva az adatkészlethez PythonScriptStep-bemenetként
  + **azureml-tensorboard**
    + frissített azureml-tensorboard a tensorflow 2.0 támogatásához
    + Helyes portszám megjelenítése egyéni Tensorboard-portok számítási példányon való használata esetén
  + **azureml-train-automl-client**
    + Kijavítottunk egy hibát, amely miatt bizonyos csomagok helytelen verziókra telepíthetők távoli futtatáskor.
    + A FeaturizationConfig felülbírálási problémája ki van javítva, amely szűri az egyéni featurizálási konfigurációt.
  + **azureml-train-automl-runtime**
    + Ki van javítva a gyakoriság észlelésének problémája a távoli futtatáskor
    + Áthelyezte `AutoMLStep` a et a `azureml-pipeline-steps` csomagba. A elavult a következőn `AutoMLStep` `azureml-train-automl-runtime` belül: .
  + **azureml-train-core**
    + A PyTorch Estimator 1.4-es verziójának támogatása
  
## <a name="2020-03-02"></a>2020-03-02

### <a name="azure-machine-learning-sdk-for-python-v112rc0-pre-release"></a>Azure Machine Learning SDK for Python v1.1.2rc0 (kiadás előtti)

+ **Hibajavítások és fejlesztések**
  + **azureml-automl-core**
    + Engedélyezte a Batch módú dedukenciát (egyszerre több sort is befogva) az AutoML ONNX-modellekhez
    + Javult az adathalmazok gyakoriságának észlelése, az adatok hiánya vagy a szabálytalan adatpontok
    + A domináns gyakoriságnak nem megfelelő adatpontok eltávolításának lehetősége hozzáadva.
  + **azureml-automl-runtime**
    + Ki lett javítva a hiba, amely akkor történt, ha a tesztelési készletben nem jelent meg a tesztelési készletben a rendszer
    + Az előrejelzési y_query pontozása során el lett távolítva a követelmény
  + **azureml-contrib-br**
    + A Hozzáférési jogkivonat lekéréséhez funkciókat ad hozzá aWebservice Osztályhoz
  + **azureml-core**
    + Az üzembe helyezett AzureML-webszolgáltatások alapértelmezés szerint naplózást `INFO` fognak használni. Ez a környezeti változónak az üzembe helyezett szolgáltatásban `AZUREML_LOG_LEVEL` való beállításával szabályozható.
    + Javítás a iterating (iterating) parancsra a munkaterületen regisztrált `Dataset.get_all` összes adatkészlet visszaadása érdekében.
    + Javíthatja a hibaüzenetet, ha érvénytelen típust ad át az `path` adatkészlet-létrehozási API-k argumentumának.
    + A Python SDK a discovery szolgáltatás használatával használja az "api" végpontot a folyamatok helyett.
    + Váltsa át az új útvonalakat az összes SDK-hívásban
    + A ModelManagementService-hívások útválasztását egy új, egységesített struktúrára módosítja
      + Elérhetővé tette a munkaterület-frissítési módszert.
      + Új image_build_compute a munkaterület frissítési metódusában, amely lehetővé teszi, hogy a felhasználó frissítse a számítási erőforrásokat a rendszerkép-összeállításhoz
    +  Elalasztási üzenetek hozzáadva a régi profilkészítési munkafolyamathoz. A processzor- és memóriakorlátok profilkészítésének kijavítása
  + **azureml-interpret**
    + update azureml-interpret to interpret-community 0.6.*
  + **azureml-mlflow**
    + Szuverén felhők támogatása hozzáadva az azureml.mlflow-hoz
  + **azureml-pipeline-steps**
    + A `AutoMLStep` átkerült a következőre: `azureml-pipeline-steps package` . A elavult a következőn `AutoMLStep` `azureml-train-automl-runtime` belül: .
  + **azureml-train-automl-client**
    + Kijavítottunk egy hibát, amely miatt bizonyos csomagok nem megfelelő verziókban telepíthetők távoli futtatáskor.
  + **azureml-train-automl-runtime**
    + Ki van javítva a gyakorisággal való észleléssel kapcsolatban a távoli futtatáskor
    + A `AutoMLStep` átkerült a következőre: `azureml-pipeline-steps package` . A elavult a következőn `AutoMLStep` `azureml-train-automl-runtime` belül: .
  + **azureml-train-core**
    + A `AutoMLStep` átkerült a következőre: `azureml-pipeline-steps package` . A elavult a következőn `AutoMLStep` `azureml-train-automl-runtime` belül: .

## <a name="2020-02-18"></a>2020-02-18

### <a name="azure-machine-learning-sdk-for-python-v111rc0-pre-release"></a>Azure Machine Learning SDK for Python v1.1.1rc0 (kiadás előtti)

+ **Hibajavítások és fejlesztések**
  + **azure-cli-ml**
    + Az egypéldányos profilkészítés ki lett javítva a javaslat előállításához, és elérhető lett a core SDK-ban.
  + **azureml-automl-core**
    + A hibanaplózás tovább lett javítva.
  + **azureml-automl-runtime**
    + Ki van javítva az előrejelzési probléma, amely akkor jelent meg, ha az adatkészlet rövid részletes adatokat tartalmaz, és hosszú időre vonatkozó hézagokat tartalmaz.
    + Ki lett javítva a hiba, amikor az automatikus maximális horizont be van kapcsolva, és a dátumoszlop dátumokat tartalmaz sztringek formájában. Megfelelő konverziót és észszerű hibát adtunk hozzá, ha a mai dátumra való átalakítás nem lehetséges
    + Natív NumPy és SciPy használata a fileCacheStore köztes adatainak szerializálására és de szerializálására (helyi AutoML-futtatáshoz)
    + Kijavítottunk egy hibát, amely miatt a sikertelen gyermekfutozások Futó állapotban elakadtak.
  + **azureml-cli-common**
    + Az egypéldányos profilkészítés ki lett javítva a javaslat előállításához, és elérhető lett a core SDK-ban.
  + **azureml-core**
    + A Datastore CLI további paramétereként hozzáadva az Azure Blob-tároló regisztrálására, amely lehetővé teszi a virtuális hálózat mögötti `--grant-workspace-msi-access` blobtároló regisztrálását
    + Az egypéldányos profilkészítés ki lett javítva a javaslat előállításához, és elérhető lett a core SDK-ban.
    + Kijavítottuk a hibát a aks.py _deploy
    + Ellenőrzi a feltöltött modellek integritását a csendes tárolási hibák elkerülése érdekében.
  + **azureml-interpret**
    + azureml-stílusú kivételek hozzáadva az azureml-interpret-hez
    + kijavítva a DeepScoringExplainer szerializálás a Keras-modellekhez
  + **azureml-pipeline-core**
    + A folyamatkötetek pontozása jegyzetfüzet már a ParallelRunStepet használja
  + **azureml-pipeline-steps**
    + Áthelyezte `AutoMLStep` a et a `azureml-pipeline-steps` csomagba. A elavult a következőn `AutoMLStep` `azureml-train-automl-runtime` belül: .
  + **azureml-contrib-pipeline-steps**
    + Nem kötelező paraméter side_inputs a ParallelRunStep elemhez. Ezzel a paraméterrel a tárolón lévő mappák csatlakoztathatóak. Jelenleg a DataReference és a PipelineData típusok támogatottak.
  + **azureml-tensorboard**
    + frissített azureml-tensorboard a tensorflow 2.0 támogatásához
  + **azureml-train-automl-client**
    + Ki van javítva a FeaturizationConfig felülbírálási probléma, amely szűri az egyéni tulajdonságokra való beállítás konfigurációját.
  + **azureml-train-automl-runtime**
    + Áthelyezte `AutoMLStep` a et a `azureml-pipeline-steps` csomagba. A elavult a következőn `AutoMLStep` `azureml-train-automl-runtime` belül: .
  + **azureml-train-core**
    + A PyTorch Estimator 1.4-es verziójának támogatása
  
## <a name="2020-02-04"></a>2020-02-04

### <a name="azure-machine-learning-sdk-for-python-v110rc0-pre-release"></a>Azure Machine Learning SDK for Python v1.1.0rc0 (kiadás előtti)

+ **Kompatibilitástörő változások**
  + **Szemantikus verziószámozás 2.0.0**
    + Az 1.1-es verziótól kezdődően az Azure ML Python SDK a szemantikai verziószámozás 2.0.0-s verzióját használja. [További információt itt olvashat.](https://semver.org/) Minden további verzió az új számozási sémát és szemantikai verziószámozási szerződést követi. 
  
+ **Hibajavítások és fejlesztések**
  + **azureml-automl-runtime**
    + A featurizálás nagyobb sebessége.
    + Kijavítottuk a gyakorisági ellenőrzést a pontozás során, most az előrejelzési feladatokban nincs szükség szigorú gyakorisági egyenértékűségre a betanítás és a tesztelési készlet között.
  + **azureml-core**
    + A felhasználó mostantól megadhat egy értéket a hitelesítési kulcshoz, amikor újra létrehozza a webszolgáltatások kulcsait.
  + **azureml-interpret**
    + Frissítve lett az azureml-interpret, hogy a interpret-community 0.5.0-ra támaszkodva
  + **azureml-pipeline-core**
    + Kijavítottunk egy hibát, amely miatt a PythonScriptStep-eredmények helytelenül újra felhasználhatók az argumentumlista módosítása ellenére
  + **azureml-pipeline-steps**
    + Dokumentációs példa hozzáadva az adatkészlethez PythonScriptStep-bemenetként
  + **azureml-contrib-pipeline-steps**
    + A ParallelRunConfigben átadott paraméterek felülírhatók a folyamatparaméterek átadása után. Az új támogatott folyamatparaméterek aml_mini_batch_size, aml_error_threshold, aml_logging_level, aml_run_invocation_timeout (aml_node_count és aml_process_count_per_node már részei a korábbi kiadásnak).
  
## <a name="2020-01-21"></a>2020-01-21

### <a name="azure-machine-learning-sdk-for-python-v1085"></a>Azure Machine Learning SDK for Python v1.0.85

+ **Új funkciók**
  + **azureml-core**
    + Az Aml aktuális alapvető használati és kvótakorlátozásának lekérte egy adott munkaterületen és előfizetésben lévő erőforrásokat
  
  + **azureml-contrib-pipeline-steps**
    + Táblázatos adatkészlet az előző lépésből a párhuzamosfutárba való áteredése köztes eredményként való engedélyezése a felhasználó számára

+ **Hibajavítások és fejlesztések**
  + **azureml-automl-runtime**
    + Az üzembe helyezett előrejelzési szolgáltatásnak y_query oszlopra vonatkozó követelmény el lett távolítva. 
    + A "y_query" el lett távolítva a Dominik OrangeLé jegyzetfüzet-szolgáltatáskérési szakaszból.
    + Kijavítottuk a hibát, amely megakadályozta az előrejelzést az üzembe helyezett modelleken, és dátum-idő oszlopokat tartalmazó adathalmazok esetében működik.
    + A Matthews korrelációs együttható hozzáadva besorolási metrikaként a bináris és a többosztályos besoroláshoz is.
  + **azureml-contrib-interpret**
    + Az azureml-contrib-interpret szöveg magyarázói el lett távolítva, mivel a szöveg magyarázata átkerült a hamarosan kiadott interpret-text repo-ba.
  + **azureml-core**
    + Adatkészlet: A fájladatkészlet használati adatai már nem függnek attól, hogy a Umpy és a pandas telepítve lesz-e a Python Env-ben.
    + A LocalWebservice.wait_for_deployment() módosult, hogy ellenőrizze a helyi Docker-tároló állapotát, mielőtt megpingelné az állapot végpontját, ami jelentősen csökkenti a sikertelen üzembe helyezés jelentéséhez szükséges időt.
    + Kijavítottuk a LocalWebservice.reload() által használt belső tulajdonság inicializálását, amikor a szolgáltatásobjektumot egy meglévő üzemelő példányból hozták létre a LocalWebservice() konstruktor használatával.
    + A pontosítás érdekében szerkesztett hibaüzenet.
    + Új, get_access_token() nevű metódus hozzáadva az AksWebservice szolgáltatáshoz, amely visszaadja az AksServiceAccessToken objektumot, amely hozzáférési jogkivonatot tartalmaz, frissítés időbélyeg után, lejárat az időbélyegzőn és a jogkivonattípuson. 
    + Az AksWebservice get_token() metódusa elavult, mivel az új metódus visszaadja a metódus által visszaadott összes információt.
    + Módosította az az ml service get-access-token parancs kimenetét. A token új neve accessToken, a refreshBy pedig refreshAfter. Hozzá van adva a expiryOn és a tokenType tulajdonság.
    + Kijavítva get_active_runs
  + **azureml-explain-model**
    + az shap 0.33.0-ra frissült, az interpret-community pedig a 0.4.* -re.
  + **azureml-interpret**
    + az shap 0.33.0-ra frissült, az interpret-community pedig a 0.4.* -re.
  + **azureml-train-automl-runtime**
    + A Matthews korrelációs együttható hozzáadva besorolási metrikaként a bináris és a többosztályos besoroláshoz is.
    + A kódból elavult előfeldolgozási jelző, és a helyébe a -featurization -featurization alapértelmezés szerint be van kapcsolva

## <a name="2020-01-06"></a>2020-01-06

### <a name="azure-machine-learning-sdk-for-python-v1083"></a>Azure Machine Learning SDK for Python 1.0.83

+ **Új funkciók**
  + Adatkészlet: Adjon hozzá két lehetőséget a és a parancshoz, ha az adatok hibás értékeket tartalmaznak ahelyett, hogy `on_error` `out_of_range_datetime` a `to_pandas_dataframe` értékkel tölteik ki `None` őket.
  + Munkaterület: Hozzá van adva a jelölő a bizalmas adatokat tartalmazó munkaterületek számára, amely további titkosítást tesz lehetővé, és letiltja a speciális `hbi_workspace` diagnosztikát a munkaterületeken. Emellett a saját kulcsok a társított Cosmos DB-példányhoz való létrehozására is támogatást nyújtunk azáltal, hogy a és a paramétert adhatja meg a munkaterület létrehozásakor, amely létrehoz egy Cosmos DB-példányt az előfizetésben a munkaterület kiépítése `cmk_keyvault` `resource_cmk_uri` során. [További információt itt olvashat.](./concept-data-encryption.md#azure-cosmos-db)

+ **Hibajavítások és fejlesztések**
  + **azureml-automl-runtime**
    + Kijavítottunk egy regressziót, amely miatt a TypeError megemelkedett az AutoML Python 3.5.4-es verziónál korábbi verzióiban való futtatásakor.
  + **azureml-core**
    + Ki lett javítva, hogy a relatív elérési útjai nem kezdődtek, de nem `datastore.upload_files` `./` voltak használhatók.
    + Elalasztó üzenetek hozzáadva az összes Képosztály kódútvonala számára
    + Kijavítva modellkezelés URL-konstrukció a Azure China 21Vianet számára.
    + Kijavítva a hiba, amely miatt a source_dir használó modellek nem csomagolhatóak Azure Functions.    
    + Lehetőség hozzáadva a [Environment.build_local() parancshoz, amely leküld](/python/api/azureml-core/azureml.core.environment.environment) egy rendszerképet az AzureML-munkaterület tárolójegyzékbe
    + Frissítettük az SDK-t, hogy kompatibilis módon használja az új jogkivonattárat az Azure Synapse-ban.
  + **azureml-interpret**
    + Kijavítva a hiba, amely miatt a None érték lett visszaadva, amikor nem voltak letölthető magyarázatok. Most kivételt képez, amely máshol is megfeleltetheti a viselkedést.
  + **azureml-pipeline-steps**
    + Nem engedélyezett a paraméterének való átadása, ha a egy paraméterben `DatasetConsumptionConfig` `Estimator` lesz `inputs` `Estimator` `EstimatorStep` használva.
  + **azureml-sdk**
    + AutoML-ügyfél hozzáadva az azureml-sdk csomaghoz, ami lehetővé teszi a távoli AutoML-futtatás elküldését a teljes AutoML-csomag telepítése nélkül.
  + **azureml-train-automl-client**
    + Az AutoML-futtatás konzolkimenetének igazítása javítva
    + Kijavítottunk egy hibát, amely miatt a pandas helytelen verziója telepíthető távoli amlcompute-re.

## <a name="2019-12-23"></a>2019-12-23

### <a name="azure-machine-learning-sdk-for-python-v1081"></a>Azure Machine Learning SDK for Python 1.0.81

+ **Hibajavítások és fejlesztések**
  + **azureml-contrib-interpret**
    + a shap függőség elhalasztva az azureml-interpret community értelmezéséhez
  + **azureml-core**
    + A számítási cél mostantól paraméterként is meg lehet adni a megfelelő üzembe helyezési konfigurációs objektumokhoz. Ez pontosabban annak a számítási célnak a neve, amelybe az üzembe helyezést szeretné helyezni, nem pedig az SDK-objektum.
    + CreatedBy-információk hozzáadva a modell- és szolgáltatásobjektumokhoz. Elérhető a through.created_by
    + Ki lett javítva a ContainerImage.run(), amely nem megfelelően beállította a Docker-tároló HTTP-portját.
    + Parancssori `azureml-dataprep` felületi `az ml dataset register` parancs választhatóként való megadása
    + Kijavítottunk egy hibát, amely miatt a helytelenül visszament egy másik olvasóhoz, `TabularDataset.to_pandas_dataframe` és figyelmeztetést adott ki.
  + **azureml-explain-model**
    + a shap függőség deduklikus értelmezése az azureml-interpret közösségtől
  + **azureml-pipeline-core**
    + Új folyamatlépés hozzáadva egy helyi jegyzetfüzet `NotebookRunnerStep` folyamatbeli lépésként való futtatásához.
    + El lett távolítva get_all PublishedPipeline, Schedules és PipelineEndpoints függvények elavult függvényei
  + **azureml-train-automl-client**
    + Elindította a data_script elalasztása az AutoML bemeneteként.


## <a name="2019-12-09"></a>2019-12-09

### <a name="azure-machine-learning-sdk-for-python-v1079"></a>Azure Machine Learning SDK for Python v1.0.79

+ **Hibajavítások és fejlesztések**
  + **azureml-automl-core**
    + A naplózható featurizationConfig eltávolítva
      + Frissítve lett a naplózás, hogy csak az "automatikus"/"ki"/"testre szabott" naplózást naplózva.
  + **azureml-automl-runtime**
    + A pandas támogatása hozzáadva. Series és pandas. Kategorikus az oszlop adattípusának észleléséhez. Korábban csak a numpy.ndarray támogatott
      + Kapcsolódó kódváltozások hozzáadva a kategorikus dtype helyes kezeléshez.
    + Továbbfejlesztettük az előrejelzési függvény interfészét: a y_pred paraméter opcionális lett. – A docstringeket továbbfejlesztettük.
  + **azureml-contrib-dataset**
    + Kijavítottunk egy hibát, amely miatt a címkével jelölt adatkészletek nem csatlakoztathatóak.
  + **azureml-core**
    + Hibajavítás a `Environment.from_existing_conda_environment(name, conda_environment_name)` hez. A felhasználó létrehozhat egy környezetpéldányt, amely pontosan a helyi környezet replikája
    + Az idősorozattal kapcsolatos adatsorozat-metódusok alapértelmezés szerint `include_boundary=True` a következőre módosult: .
  + **azureml-train-automl-client**
    + Kijavítva a hiba, amely miatt a rendszer nem nyomtatja ki az érvényesítési eredményeket, ha a kimenet megjelenítése hamisra van állítva.


## <a name="2019-11-25"></a>2019-11-25

### <a name="azure-machine-learning-sdk-for-python-v1076"></a>Azure Machine Learning SDK for Python 1.0.76

+ **Kompatibilitástörő változások**
  + Az Azureml-Train-AutoML frissítésével kapcsolatos problémák
    + Ha az azureml-train-automl>=1.0.76-os verzióra frissít az azureml-train-automl<1.0.76-os verzióról, az részleges telepítéseket okozhat, ami egyes AutoML-importok meghiúsulhat. A probléma megoldásához futtassa a következőnél található beállítási szkriptet: https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/automl_setup.cmd . Ha közvetlenül a pip-et használja, a következőt is használhatja:
      + "pip install --upgrade azureml-train-automl"
      + "pip install --ignore-installed azureml-train-automl-client"
    + vagy eltávolíthatja a régi verziót a frissítés előtt
      + "pip uninstall azureml-train-automl"
      + "pip install azureml-train-automl"

+ **Hibajavítások és fejlesztések**
  + **azureml-automl-runtime**
    + Az AutoML mostantól az igaz és a hamis osztályt is figyelembe veszi a bináris besorolási feladatok átlagos skaláris metrikaértékének kiszámításakor.
    + Az AzureML-AutoML-Core gépi tanulási és betanító kódját áthelyezte egy új AzureML-AutoML-Runtime csomagba.
  + **azureml-contrib-dataset**
    + Ha címkével címkével jelölt adatkészleten hívja meg a letöltési lehetőséget, mostantól megadhatja, hogy `to_pandas_dataframe` felülírja-e a meglévő fájlokat.
    + Ha a `keep_columns` vagy `drop_columns` a hívása idősorozatot, címkét vagy képoszlopot ad vissza, a rendszer az adatkészlet megfelelő képességeit is eldobja.
    + Ki van javítva a pytorch-betöltő objektumészlelési feladattal való problémája.
  + **azureml-contrib-interpret**
    + A magyarázat irányítópult-vezérlő el lett távolítva az azureml-contrib-interpret, a csomag úgy módosult, hogy az újra hivatkozik a interpret_community
    + Az interpret-community frissített verziója a 0.2.0-s verzióra
  + **azureml-core**
    + A teljesítményének `workspace.datasets` javítása.
    + A Datastore-regisztráció engedélyezése Azure SQL Database felhasználónévvel és jelszóval való hitelesítéssel
    + Javítás a RunConfigurations relatív elérési utakról való betöltéséhez.
    + Ha a vagy a hívása egy idősorozat-oszlop elejtését jelenti, az adatkészlet megfelelő képességei is el lesznek `keep_columns` `drop_columns` dobva.
  + **azureml-interpret**
    + az interpret-community frissített verziója a 0.2.0-s verzióra
  + **azureml-pipeline-steps**
    + Dokumentált támogatott értékek az `runconfig_pipeline_params` Azure Machine Learning-folyamat lépései esetében.
  + **azureml-pipeline-core**
    + Cli-lehetőség hozzáadva a kimenet json formátumban való letöltéséhez a Pipeline-parancsokhoz.
  + **azureml-train-automl**
    + Az AzureML-Train-AutoML felosztása két csomagra, egy AzureML-Train-AutoML-Client ügyfélcsomagra és egy AzureML-Train-AutoML-Runtime ml betanítócsomagra
  + **azureml-train-automl-client**
    + Hozzáadtunk egy vékony ügyfelet az AutoML-kísérletek beküldéséhez anélkül, hogy helyileg kellene telepíteni a gépi tanulási függőségeket.
    + Ki van javítva az automatikusan észlelt késések, a gördülőablakok mérete és a maximális horizontok naplózása a távoli futtatásban.
  + **azureml-train-automl-runtime**
    + Új AutoML-csomag hozzáadva a gépi tanulási és futásidejű összetevők az ügyféltől való elkülönítése érdekében.
  + **azureml-contrib-train-rl**
    + Megerősítési tanulási támogatás hozzáadva az SDK-ban.
    + AmlWindowsCompute támogatás hozzáadva az RL SDK-ban.


## <a name="2019-11-11"></a>2019-11-11

### <a name="azure-machine-learning-sdk-for-python-v1074"></a>Azure Machine Learning SDK for Python v1.0.74

  + **Előzetes verziójú funkciók**
    + **azureml-contrib-dataset**
      + Az azureml-contrib-dataset importálása után a hívása helyett létrehozhat egy címkével jelölt `Dataset.Labeled.from_json_lines` `._Labeled` adatkészletet.
      + Ha címkével címkével jelölt adatkészleten hívja meg a letöltési lehetőséget, mostantól megadhatja, hogy `to_pandas_dataframe` felülírja-e a meglévő fájlokat.
      + Ha a `keep_columns` vagy `drop_columns` a hívása idősorozatot, címkét vagy képoszlopot ad vissza, a rendszer az adatkészlet megfelelő képességeit is eldobja.
      + Kijavítottuk a PyTorch betöltővel kapcsolatos hibákat a hívatása `dataset.to_torchvision()` során.

+ **Hibajavítások és fejlesztések**
  + **azure-cli-ml**
    + Modellprofil-készítés hozzáadva az előzetes verziójú CLI-hez.
    + Javítja az Azure Storage-ban az AzureML CLI meghiúsulását okozó, feltörést okozó változást.
    + Új Load Balancer az MLC-hez az AKS-típusokhoz
  + **azureml-automl-core**
    + Ki van javítva a maximális horizont észlelése az idősorozaton, hiányzó értékek és több szint.
    + Kijavítottuk a keresztellenőrzési felosztások létrehozása során történt hibákat.
    + Cserélje le ezt a szakaszt egy Markdown formátumú üzenetre, amely a kibocsátási megjegyzésekben fog megjelenni: -Improved handling of short grains in the forecasting data sets (A rövid időkorreklatok továbbfejlesztett kezelése az előrejelzési adatkészletben).
    + Kijavítottuk a felhasználói adatok maszkolásával kapcsolatos hibát a naplózás során. – Javult a hibák naplózása az előrejelzési futtatás során.
    + A psutil hozzáadása Conda-függőségként az automatikusan létrehozott yml üzembe helyezési fájlhoz.
  + **azureml-contrib-br**
    + Javítja az Azure Storage-ban az AzureML CLI meghiúsulását okozó, feltörést okozó változást.
  + **azureml-core**
    + Kijavít egy hibát, amely miatt a Azure Functions üzembe helyezett modellek 500-as hibákat okoztak.
    + Kijavítottunk egy hibát, amely miatt az amlignore-fájl nem volt alkalmazva a pillanatképeken.
    + Új API-amlcompute.get_active_runs, amely egy generátort ad vissza egy adott amlcompute-on futó és várólistán lévő futtatásokhoz.
    + Új Load Balancer az MLC-hez az AKS-típusokhoz.
    + Új append_prefix a bool paraméter a download_files-ban run.py és download_artifacts_from_prefix a artifacts_client. Ez a jelölő a forrásfájlpath szelektív el simítására szolgál, így csak a fájl vagy mappa neve lesz hozzáadva a output_directory
    + Az adatkészlet-használat deszerializálási `run_config.yml` problémája kijavítva.
    + Ha a vagy a hívása egy idősorozat-oszlop elejtését jelenti, az adatkészlet megfelelő képességei is el lesznek `keep_columns` `drop_columns` dobva.
  + **azureml-interpret**
    + Az interpret-community verzió frissítve a 0.1.0.3-as verzióra
  + **azureml-train-automl**
    + Kijavítottunk egy hibát, automl_step nem nyomtathat érvényesítési problémákat.
    + Javítva register_model, hogy akkor is sikeresek legyenek, ha a modell környezetében helyileg hiányoznak függőségek.
    + Kijavítottunk egy hibát, amely miatt egyes távoli futtatásokat nem engedélyezte a Docker.
    + Adja hozzá annak a kivételnek a naplózását, amely miatt a helyi futtatás idő előtt meghiúsul.
  + **azureml-train-core**
    + Vegye resume_from, hogy a rendszer futtatja-e az automatikus hiperparaméter-finomhangolás legjobb gyermekfutatokat.
  + **azureml-pipeline-core**
    + Ki van javítva a paraméterkezelés a folyamat argumentumának konstrukcióban.
    + Hozzá van adva a folyamat leírása és a lépéstípus yaml-paramétere.
    + Új yaml-formátum a Folyamat lépéshez, és elalasztó figyelmeztetés hozzáadva a régi formátumhoz.



## <a name="2019-11-04"></a>2019-11-04

### <a name="web-experience"></a>Webes élmény

Az együttműködésen alapuló munkaterület kezdőlapja tovább lett javítva és újra lett [https://ml.azure.com](https://ml.azure.com) korrektálva, mint Azure Machine Learning stúdió.

A studióból betaníthat, tesztelhet, üzembe helyezhet és kezelhet Azure Machine Learning adateszközöket, például adatkészleteket, folyamatokat, modelleket, végpontokat stb.

A studióból elérheti a következő webes szerzői eszközöket:

| Webalapú eszköz | Description | 
|-|-|-|
| Notebook VM (előzetes verzió) | Teljes körűen felügyelt felhőalapú munkaállomás | 
| [Automatizált gépi tanulás](tutorial-first-experiment-automated-ml.md) (előzetes verzió) | Nincs kódélmény a gépi tanulási modell fejlesztésének automatizálására | 
| [Tervező](concept-designer.md) | Húzással használható gépi tanulási modellezési eszköz, korábbi nevén vizuális felület | 


### <a name="azure-machine-learning-designer-enhancements"></a>Azure Machine Learning tervező fejlesztései

+ Korábban vizuális felületként is ismert 
+    11 új [modul,](algorithm-module-reference/module-reference.md) köztük ajánlók, osztályozók és képzési segédprogramok, beleértve a funkciók tervezését, a keresztellenőrzést és az adatátalakítást.

### <a name="r-sdk"></a>R SDK 
 
Az adattudósok és AI-fejlesztők az [Azure Machine Learning SDK for R](https://github.com/Azure/azureml-sdk-for-r) használatával hoznak létre és futtatnak gépi tanulási munkafolyamatokat a Azure Machine Learning.

Az Azure Machine Learning SDK for R a `reticulate` csomagot használja a Python SDK-hoz való kötéshez. A Pythonhoz való közvetlen kötéssel az SDK for R lehetővé teszi a Python SDK-ban megvalósított alapvető objektumokhoz és metódusokhoz való hozzáférést bármely választott R-környezetből.

Az SDK fő képességei a következők:

+    Felhőbeli erőforrások kezelése a gépi tanulási kísérletek monitorozásához, naplózásához és rendszerezéséhez.
+    Modellek betanítása felhőalapú erőforrásokkal, beleértve a GPU-gyorsítással betanított modelleket.
+    Modellek üzembe helyezése webszolgáltatásként a Azure Container Instances (ACI) és Azure Kubernetes Service (AKS) szolgáltatásban.

A teljes [dokumentációért tekintse](https://azure.github.io/azureml-sdk-for-r) meg a csomag webhelyét.

### <a name="azure-machine-learning-integration-with-event-grid"></a>Azure Machine Learning integráció a Event Grid 

Azure Machine Learning mostantól erőforrás-szolgáltató a Event Grid számára, gépi tanulási eseményeket konfigurálhat a Azure Portal vagy az Azure CLI használatával. A felhasználók eseményeket hozhatnak létre a futtatás befejezéséhez, a modellregisztrációhoz, a modell üzembe helyezéséhez és az észlelt sodródáshoz. Ezek az események a Event Grid által támogatott eseménykezelőkre irányíthatóak. További részleteket a [](how-to-use-event-grid.md) machine learning-eseménysémát és az oktatóanyagokat ismertető cikkekben talál. [](../event-grid/event-schema-machine-learning.md)

## <a name="2019-10-31"></a>2019-10-31

### <a name="azure-machine-learning-sdk-for-python-v1072"></a>Azure Machine Learning SDK for Python 1.0.72

+ **Új funkciók**
  + Az [**azureml-datadrift**](/python/api/azureml-datadrift) csomagon keresztül hozzáadott adatkészlet-figyelők lehetővé teszik az idősorozat-adatkészletek adatsodródások vagy más statisztikai változások monitorozását az idő során. Riasztások és események akkor aktiválódnak, ha eltérést észlel, vagy az adatokra vonatkozó egyéb feltételek teljesülnek. A [részletekért tekintse meg](how-to-monitor-datasets.md) a dokumentációt.
  + Két új kiadás (más néven termékváltozat) bejelentése a Azure Machine Learning. Ebben a kiadásban most létrehozhat egy Alapszintű vagy Nagyvállalati Azure Machine Learning munkaterületet. Minden meglévő munkaterület alapszintű kiadásban lesz elérhető, és a Azure Portal vagy a studióba is bármikor frissítheti a munkaterületet. A munkaterületen alapszintű vagy vállalati munkaterületet Azure Portal. További [információért olvassa el](./how-to-manage-workspace.md) a dokumentációt. Az SDK-ból a munkaterület kiadása a munkaterület-objektum "termékváltozat" tulajdonságával határozható meg.
  + A Azure Machine Learning Compute fejlesztéseit is továbbfejlesztjük – a diagnosztikai naplók hibakereséshez való megtekintése mellett most már megtekintheti a fürtök metrikákat (például csomópontok száma, futó csomópontok, magkvóták teljes száma) az Azure Monitor-ban. Emellett megtekintheti a fürtön jelenleg futó vagy várólistán lévő futtatásokat, valamint olyan adatokat, mint a fürt különböző csomópontjainak IP-ip-jai. Ezeket megtekintheti a portálon, vagy az SDK vagy a CLI megfelelő függvényekkel.

  + **Előzetes verziójú funkciók**
    + Előzetes verziójú támogatást biztosítunk a helyi SSD lemeztitkosításához a Azure Machine Learning Compute-ban. Műszaki támogatási jegyet kell felhozni, hogy az előfizetése engedélyezze a listában a funkció használatát.
    + Az Azure Machine Learning Batch-következtetés nyilvános előzetes verziója. Azure Machine Learning Batch-következtetés olyan nagy következtetési feladatokhoz való, amelyek nem időérzékenyek. A Batch Dedukció költséghatékony dedukciós számítási skálázást biztosít, aszinkron alkalmazásokhoz pedig páratlan átviteli sebességet biztosít. Nagy mennyiségű adatra vonatkozó nagy átviteli sebességre, tűz- és feledési következtetésre van optimalizálva.
    + [**azureml-contrib-dataset**](/python/api/azureml-contrib-dataset)
        + Címkével rendelkező adatkészletek engedélyezett funkciói
        ```Python
        import azureml.core
        from azureml.core import Workspace, Datastore, Dataset
        import azureml.contrib.dataset
        from azureml.contrib.dataset import FileHandlingOption, LabeledDatasetTask

        # create a labeled dataset by passing in your JSON lines file
        dataset = Dataset._Labeled.from_json_lines(datastore.path('path/to/file.jsonl'), LabeledDatasetTask.IMAGE_CLASSIFICATION)

        # download or mount the files in the `image_url` column
        dataset.download()
        dataset.mount()

        # get a pandas dataframe
        from azureml.data.dataset_type_definitions import FileHandlingOption
        dataset.to_pandas_dataframe(FileHandlingOption.DOWNLOAD)
        dataset.to_pandas_dataframe(FileHandlingOption.MOUNT)

        # get a Torchvision dataset
        dataset.to_torchvision()
        ```

+ **Hibajavítások és fejlesztések**
  + **azure-cli-ml**
    + A CLI már támogatja a modellek csomagolását.
    + Hozzá van adva az adatkészlet cli-hez. További információ: `az ml dataset --help`
    + Támogatás hozzáadva a támogatott modellek (ONNX, scikit-learn és TensorFlow) InferenceConfig példány nélküli üzembe helyezéséhez és csomagolásához.
    + Új felülírásjelző hozzáadva a szolgáltatástelepítéshez (ACI és AKS) az SDK-ban és a CLI-ban. Ha meg van téve, a felülírja a meglévő szolgáltatást, ha a névvel már létezik szolgáltatás. Ha a szolgáltatás nem létezik, a létrehoz egy új szolgáltatást.
    + A modellek két új keretrendszerben regisztrálhatóak, az Onnx és a Tensorflow keretrendszerben. – A modellregisztráció minta bemeneti adatokat, minta kimeneti adatokat és erőforrás-konfigurációt fogad el a modellhez.
  + **azureml-automl-core**
    + Az iteráció betanítása gyermekfolyamatban csak akkor fut, ha futásidejű korlátozások vannak beállítva.
    + Hozzáadtunk egy védőkorlátot az előrejelzési feladatokhoz, amely ellenőrzi, hogy egy adott max_horizon okozhat-e memória-problémát az adott gépen. Ha igen, egy védőkorlát üzenet jelenik meg.
    + Az összetett gyakoriságok ( például két év és egy hónap) támogatása hozzáadva. – Érthető hibaüzenet, ha a gyakoriság nem határozható meg.
    + Az azureml-defaults hozzáadása az automatikusan létrehozott Conda env-hez a modell üzembehelyhelyozási hibája megoldásához
    + A folyamat köztes adatainak Azure Machine Learning a folyamat táblázatos adatkészletré alakítható, és használható `AutoMLStep` a-ban.
    + Megvalósított oszlop célú frissítés streameléshez.
    + Megvalósítottuk a transzformátorparaméter frissítését az Imputer és a HashOneHotEncoder számára a streameléshez.
    + Hozzáadta az aktuális adatméretet és a minimálisan szükséges adatméretet az érvényesítési hibaüzenethez.
    + Frissült a keresztellenőrzéshez minimálisan szükséges adatméret, hogy legalább két minta legyen garantálva az egyes ellenőrzési mintákban.
  + **azureml-cli-common**
    + A CLI már támogatja a modellek csomagolását.
    + A modellek két új keretrendszerben regisztrálhatóak, az Onnx és a Tensorflow keretrendszerben.
    + A modellregisztráció fogadja a minta bemeneti adatokat, a minta kimeneti adatokat és a modell erőforrás-konfigurációját.
  + **azureml-contrib-gbdt**
    + kijavítva a notebook kiadási csatornája
    + Figyelmeztetés hozzáadva a nem AmlCompute számítási célhoz, amely nem támogatott
    + LightGMB Estimator hozzáadva az azureml-contrib-gbdt csomaghoz
  + [**azureml-core**](/python/api/azureml-core)
    + A CLI már támogatja a modellek csomagolását.
    + Elalasztó figyelmeztetés hozzáadása elavult adatkészlet API-khoz. Lásd a Dataset API változási értesítését itt: https://aka.ms/tabular-dataset .
    + Ha az adatkészlet regisztrálva van, módosítsa a nevet és a verziót a regisztrációs név [`Dataset.get_by_id`](/python/api/azureml-core/azureml.core.dataset%28class%29#get-by-id-workspace--id-) és a verzió visszaadása érdekében.
    + Kijavítottunk egy hibát, amely miatt a ScriptRunConfig és az adatkészlet argumentumként való használata nem használható többször a kísérlet futtatásának elküldéhez.
    + A rendszer nyomon követi a futtatás során lekért adatkészleteket, amelyek a futtatás részleteit tartalmazó lapon vagy a futtatás befejezése után a [`run.get_details()`](/python/api/azureml-core/azureml.core.run%28class%29#get-details--) hívását követően láthatók.
    + Engedélyezze a Azure Machine Learning folyamat köztes adatainak táblázatos adatkészletré konvertálását és a-ban való felhasználhatóvá való [`AutoMLStep`](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlstep) átalakítását.
    + Támogatás hozzáadva a támogatott modellek (ONNX, scikit-learn és TensorFlow) InferenceConfig példány nélküli üzembe helyezéséhez és csomagolásához.
    + Új felülírásjelző hozzáadva a szolgáltatás üzembe helyezéséhez (ACI és AKS) az SDK-ban és a CLI-ban. Ha meg van téve, a felülírja a meglévő szolgáltatást, ha a névvel már létezik szolgáltatás. Ha a szolgáltatás nem létezik, a létrehoz egy új szolgáltatást.
    +  A modellek két új keretrendszerben regisztrálhatóak, az Onnx és a Tensorflow keretrendszerben. A modellregisztráció fogadja a minta bemeneti adatokat, a minta kimeneti adatokat és a modell erőforrás-konfigurációját.
    + Új adattár hozzáadva a Azure Database for MySQL. Példa hozzáadva a datatransferStep Azure Database for MySQL a datatransferStep Azure Machine Learning folyamatokban.
    + Hozzáadott funkciók címkék hozzáadásához és eltávolításához kísérletekből Hozzáadott funkciók a címkék futtatásból való eltávolításához
    + Új felülírásjelző hozzáadva a szolgáltatástelepítéshez (ACI és AKS) az SDK-ban és a CLI-ban. Ha meg van téve, a felülírja a meglévő szolgáltatást, ha a névvel már létezik szolgáltatás. Ha a szolgáltatás nem létezik, a létrehoz egy új szolgáltatást.
  + [**azureml-datadrift**](/python/api/azureml-datadrift)
    + Áthelyezve `azureml-contrib-datadrift` innen: `azureml-datadrift`
    + Idősorozat-adatkészletek eltérésekkel és más statisztikai értékekkel való monitorozásának támogatása hozzáadva
    + Új és `create_from_model()` `create_from_dataset()` metódusok a [`DataDriftDetector`](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector%28class%29) osztályhoz. A `create()` metódus elavult lesz.
    + A vizualizációk módosítása a Pythonban és a felhasználói felületen a Azure Machine Learning stúdió.
    + Heti és havi figyeléses ütemezés támogatása az adatkészlet-figyelők napi ütemezése mellett.
    + Támogatja az adatmonitorozó metrikák visszatöltését az adatkészlet-figyelők előzményadat-elemzéséhez.
    + Különböző hibajavítások
  + [**azureml-pipeline-core**](/python/api/azureml-pipeline-core)
    + Az azureml-dataprep már nem szükséges egy Azure Machine Learning folyamat futtatásának elküldéhez a `yaml` folyamatfájlból.
  + [**azureml-train-automl**](/python/api/azureml-train-automl-runtime/)
    + Azureml-alapértelmezések hozzáadása az automatikusan létrehozott Conda env-hez a modell üzembehelyhelyező hibája megoldásához
    + Az AutoML távoli betanítás mostantól tartalmazza az azureml-defaults (azureml-alapértelmezések) beállítást, amely lehetővé teszi a betanítási env újrafelhasználásának deduktenciát.
  + **azureml-train-core**
    + PyTorch 1.3-támogatás hozzáadva a [`PyTorch`](/python/api/azureml-train-core/azureml.train.dnn.pytorch) becslőben

## <a name="2019-10-21"></a>2019-10-21

### <a name="visual-interface-preview"></a>Vizuális felület (előzetes verzió)

+ A Azure Machine Learning (előzetes verzió) felülete át lett tervedve, hogy a következő [folyamatokon Azure Machine Learning fusson:](concept-ml-pipelines.md). A vizuális felületen létrehozott folyamatok (korábbi nevén kísérletek) most már teljesen integrálva vannak a Azure Machine Learning felülettel.
  + Egységes felügyeleti élmény az SDK-eszközökkel
  + Vizuális felületi modellek, folyamatok és végpontok verziószámozása és nyomon követése
  + Újratervezett felhasználói felület
  + Batch-következtetés üzembe helyezése hozzáadva
  + A Azure Kubernetes Service (AKS) támogatása hozzáadva a deduktencia számítási célokhoz
  + Új Python-lépéses folyamat-szerzői munkafolyamat
  + Új [kezdőlap vizualizáció-készítő](https://ml.azure.com) eszközökhöz

+ **Új modulok**
  + Matematikai művelet alkalmazása
  + SQL-átalakítás alkalmazása
  + Értékek kivágása
  + Adatok összegzése
  + Importálás SQL Database

## <a name="2019-10-14"></a>2019-10-14

### <a name="azure-machine-learning-sdk-for-python-v1069"></a>Azure Machine Learning SDK for Python 1.0.69

+ **Hibajavítások és fejlesztések**
  + **azureml-automl-core**
    + A modell magyarázatának a legjobb futtatásra való korlátozása ahelyett, hogy minden egyes futtatáshoz ki lenne ássa a magyarázatokat. Ennek a viselkedésnek a módosítása a helyi, távoli és ADB-hez.
    + Támogatás hozzáadva a felhasználói felület igény szerinti modelljeinek magyarázatához
    + A psutil hozzá van adva a függőségeként, és a psutil is `automl` conda-függőségként szerepel az amlcompute-ban.
    + Kijavítottuk a heurisztikus késésekkel és az előrejelzési adatkészletek gördülőablak-méretekkel kapcsolatos hibáit, amelyek közül néhány lineáris algebrahibákat okozhat
      + Nyomtatás hozzáadva az előrejelzési futtatás heurisztikusan meghatározott paramétereihez.
  + **azureml-contrib-datadrift**
    + Védelem hozzáadva a kimeneti metrikák létrehozásakor, ha az adatkészletszintű eltérés nem az első szakaszban található.
  + **azureml-contrib-interpret**
    + az azureml-contrib-explain-model csomag új neve azureml-contrib-interpret
  + **azureml-core**
    + API hozzáadva az adatkészletek regisztrációjának a megszöknésének a regisztrációhoz. `dataset.unregister_all_versions()`
    + Az azureml-contrib-explain-model csomag új neve azureml-contrib-interpret.
  + **[azureml-core](/python/api/azureml-core)**
    + API hozzáadva az adatkészletek regisztrációjának a megszöknésének a regisztrációhoz. Adatkészlet. [unregister_all_versions()](/python/api/azureml-core/azureml.data.abstract_datastore.abstractdatastore#unregister--).
    + Dataset API hozzáadva az adatok változási időének ellenőrzéséhez. `dataset.data_changed_time`.
    + A , a és a bemeneteként is adatokat lehet használni az Azure Machine Learning `FileDataset` `TabularDataset` `PythonScriptStep` `EstimatorStep` `HyperDriveStep` folyamatban
    + A nagy számú fájlt tartalmazó mappák teljesítménye `FileDataset.mount` javult.
    + A [FileDataset](/python/api/azureml-core/azureml.data.filedataset) és [a TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) a [PythonScriptStep,](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep) [EstimatorStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.estimatorstep)és [HyperDriveStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.hyperdrivestep) bemeneteként való használata a Azure Machine Learning folyamatban.
    + A FileDataset teljesítménye. [A mount()](/python/api/azureml-core/azureml.data.filedataset#mount-mount-point-none----kwargs-) továbbfejlesztve lett a nagy számú fájlt tartalmazó mappákhoz
    + URL-cím hozzáadva az ismert hibákra vonatkozó javaslatokhoz a futtatás részleteiben.
    + Kijavítottunk egy hibát a run.get_metrics, amely miatt a kérések meghiúsulnak, ha egy futtatás túl sok gyermekből áll
    + Kijavítottunk egy hibát a [run.get_metrics,](/python/api/azureml-core/azureml.core.run.run#get-metrics-name-none--recursive-false--run-type-none--populate-false-) amely miatt a kérések meghiúsulnak, ha egy futtatás túl sok gyermekkel volt
    + A Hitelesítés támogatása hozzáadva a Arkdia-fürtön.
    + Egy kísérletobjektum létrehozása lekért vagy létrehoz egy kísérletet a Azure Machine Learning munkaterületen a futtatás előzményeinek nyomon követéséhez. A kísérlet azonosítóját és az archivált időt a létrehozáskor a kísérlet objektuma tölti ki. Például: experiment = Experiment(workspace, "New Experiment") experiment_id = experiment.id archive() és reactivate() olyan függvények, amelyek egy kísérleten hívhatók meg a kísérlet elrejtése és visszaállítása érdekében, hogy a kísérlet ne legyen látható a felhasználói felületen, vagy alapértelmezés szerint vissza legyen állítva egy listakísérletek hívásában. Ha egy archivált kísérlet nevével azonos nevű új kísérletet hoz létre, az újraaktiváláskor átnevezheti az archivált kísérletet egy új név átadásával. Egy adott névvel csak egy aktív kísérlet lehet. Példa: experiment1 = Experiment(workspace, "Active Experiment") experiment1.archive() # Create new active experiment with the name with the archived (Kísérlet1 = kísérlet(munkaterület, "Active Experiment") experiment1.archive() # Create new active experiment with the name with the archived (Új aktív kísérlet létrehozása az archivált nevével). 2. kísérlet. = Experiment(workspace, "Active Experiment") experiment1.reactivate(new_name="Previous Active Experiment") A kísérlet statikus metóduslist() tulajdonsága névszűrőt és ViewType szűrőt is képes használni. ViewType értékek: "ACTIVE_ONLY", "ARCHIVED_ONLY" és "ALL" Példa: archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY") all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
    + A környezet modelltelepítéshez és szolgáltatásfrissítéshez való használatának támogatása
  + **azureml-datadrift**
    + A DataDriftDector osztály show attribútuma többé nem támogatja a "with_details" opcionális argumentumot. A show attribútum csak a jellemzőoszlopok eltérési együtthatóját és eltérési hozzájárulását mutatja.
    + A DataDriftDetector get_output viselkedése megváltozik:
      + A bemeneti start_time, end_time megadása nem kötelező, hanem nem kötelező;
      + Egy adott start_time és/vagy end_time egy adott run_id bemenete értékhiba-kivételt eredményez, mert kölcsönösen kizárják egymást
      + A megadott start_time és/vagy end_time csak az ütemezett futtatás eredményeit adja vissza;
      + A "daily_latest_only" paraméter elavult.
    + Adatkészlet-alapú sodródás kimenetek leolvasásának támogatása.
  + **azureml-explain-model**
    + Átnevezi az AzureML-explain-model csomagot az AzureML-interpret névre, így a régi csomag mostantól visszamenőleges kompatibilitást biztosít
    + kijavítva a hiba, amely nyers magyarázatokkal besorolási feladatra van beállítva, nem pedig alapértelmezés szerint regresszió a explanationClient fájlból `automl` való letöltéskor
    + Támogatás hozzáadása a `ScoringExplainer` közvetlen létrehozáshoz a használatával `MimicWrapper`
  + **azureml-pipeline-core**
    + Jobb teljesítmény a nagy méretű folyamatok létrehozásához
  + **azureml-train-core**
    + TensorFlow 2.0-támogatás hozzáadva a TensorFlow Estimatorban
  + **azureml-train-automl**
    + A Kísérlet [objektum létrehozása](/python/api/azureml-core/azureml.core.experiment.experiment) lekért vagy létrehozza a kísérletet a Azure Machine Learning munkaterületen a futtatáselőzmények nyomon követéséhez. A kísérlet azonosítóját és az archivált időt a létrehozáskor a Kísérlet objektum tölti ki. Példa:

        ```python
        experiment = Experiment(workspace, "New Experiment")
        experiment_id = experiment.id
        ```
        Az [archive() és](/python/api/azureml-core/azureml.core.experiment.experiment#archive--) [az reactivate()](/python/api/azureml-core/azureml.core.experiment.experiment#reactivate-new-name-none-) függvények olyan függvények, amelyek egy kísérleten hívhatók meg, hogy elrejtse és visszaállítsa a kísérletet a felhasználói felület megjelenítése vagy a listakísérletek hívása során alapértelmezés szerint visszaadva. Ha egy archivált kísérlet nevével azonos nevű új kísérletet hoz létre, az újraaktiváláskor átnevezheti az archivált kísérletet egy új név átadásával. Egy adott névvel csak egy aktív kísérlet lehet. Példa:

        ```python
        experiment1 = Experiment(workspace, "Active Experiment")
        experiment1.archive()
        # Create new active experiment with the same name as the archived.
        experiment2 = Experiment(workspace, "Active Experiment")
        experiment1.reactivate(new_name="Previous Active Experiment")
        ```
        A kísérlet [statikus metódusának list()](/python/api/azureml-core/azureml.core.experiment.experiment#list-workspace--experiment-name-none--view-type--activeonly---tags-none-) tulajdonsága névszűrőt és ViewType szűrőt is képes használni. A ViewType értéke "ACTIVE_ONLY", "ARCHIVED_ONLY" és "ALL". Példa:

        ```python
        archived_experiments = Experiment.list(workspace, view_type="ARCHIVED_ONLY")
        all_first_experiments = Experiment.list(workspace, name="First Experiment", view_type="ALL")
        ```
    + Környezet használatának támogatása a modell üzembe helyezéséhez és a szolgáltatásfrissítéshez.
  + **[azureml-datadrift](/python/api/azureml-datadrift)**
    + A [DataDriftDetector](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) osztály show attribútuma többé nem támogatja az opcionális "with_details" argumentumot. A show attribútum csak a jellemzőoszlopok eltérési együtthatóját és eltérési hozzájárulását mutatja.
    + A DataDriftDetector függvény [get_output]python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#get-output-start-time-none-end-time-none-run-id-none-) viselkedésváltozások:
      + A bemeneti start_time, end_time megadása nem kötelező, hanem nem kötelező;
      + Az adott start_time és/vagy end_time adott run_id bemenete értékhiba-kivételt eredményez, mert kölcsönösen kizárják egymást;
      + A megadott start_time és/vagy end_time csak az ütemezett futtatás eredményeit adja vissza;
      + A "daily_latest_only" paraméter elavult.
    + Adatkészlet-alapú adatsodródás-kimenetek leolvasásának támogatása.
  + **azureml-explain-model**
    + A [ScoringExplainer](/python/api/azureml-interpret/azureml.interpret.scoring.scoring_explainer.scoringexplainer) közvetlen létrehozása a MimicWrapper használatával
  + **[azureml-pipeline-core](/python/api/azureml-pipeline-core)**
    + Jobb teljesítmény nagy méretű folyamatok létrehozásához.
  + **[azureml-train-core](/python/api/azureml-train-core)**
    + TensorFlow 2.0-támogatás hozzáadva a [TensorFlow](/python/api/azureml-train-core/azureml.train.dnn.tensorflow) Estimatorban.
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + A szülő futtatás a továbbiakban nem fog meghiúsulni, ha a beállítási iteráció meghiúsult, mivel a vezénylés már gondoskodik róla.
    + A local-docker és a local-conda támogatása hozzáadva az AutoML-kísérletekhez
    + A local-docker és a local-conda támogatása hozzáadva az AutoML-kísérletekhez.


## <a name="2019-10-08"></a>2019-10-08

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Új webes élmény (előzetes verzió) Azure Machine Learning munkaterületek számára

Frissült az új [munkaterületi](https://ml.azure.com) portál Kísérletek lapja, így az adattudósok nagyobb teljesítményben figyelheti a kísérleteket. A következő funkciókat fedezheti fel:
+ Kísérletezés metaadatai a kísérletek listájának egyszerű szűréséhez és rendezéséhez
+ Egyszerűsített és nagy teljesítményű kísérleti részletek oldalai, amelyek lehetővé teszik a futtatás vizualizációt és összehasonlítást
+ Új kialakítás a részleteket tartalmazó lapok futtatásához a betanítás futásának érthető és monitoros ellenőrzésére

## <a name="2019-09-30"></a>2019-09-30

### <a name="azure-machine-learning-sdk-for-python-v1065"></a>Azure Machine Learning SDK for Python v1.0.65

  + **Új funkciók**
    + Összeadott környezetek hozzáadva. Ezek a környezetek előre konfigurálva vannak kódtárakkal a gyakori gépi tanulási feladatokhoz, és docker-rendszerképekként vannak előre felépítve és gyorsítótárazva a gyorsabb végrehajtás érdekében. Alapértelmezés szerint megjelennek a munkaterület környezetének listájában az "AzureML" előtaggal.
    + Összeadott környezetek hozzáadva. Ezek a környezetek előre konfigurálva vannak kódtárakkal a gyakori gépi tanulási feladatokhoz, és docker-rendszerképekként vannak előre felépítve és gyorsítótárazva a gyorsabb végrehajtás érdekében. Alapértelmezés szerint megjelennek a [munkaterület](/python/api/azureml-core/azureml.core.workspace%28class%29)környezetének listájában az "AzureML" előtaggal.

  + **azureml-train-automl**
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + Az ONNX-konverzió támogatása hozzáadva az ADB és a HDI számára

+ **Előzetes verziójú funkciók**
  + **azureml-train-automl**
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + Szövegvetítőként támogatott AZ ÉS a BiLSTM (csak előzetes verzió)
    + Támogatott jellemzősítés testreszabása az oszlopcélú és átalakítóparaméterek esetén (csak előzetes verzió)
    + Támogatott nyers magyarázatok, ha a felhasználó engedélyezi a modell magyarázatát a betanítás során (csak előzetes verzió)
    + Új Előrejelzés az `timeseries` előrejelzéshez betanítható folyamatként (csak előzetes verzió)

  + **azureml-contrib-datadrift**
    + Az azureml-contrib-datadriftről azureml-datadriftre áthelyezett csomagok; A `contrib` csomag egy későbbi kiadásban el lesz távolítva

+ **Hibajavítások és fejlesztések**
  + **azureml-automl-core**
    + A FeaturizationConfig az AutoMLConfig és az AutoMLBaseSettings szolgáltatásban
    + A FeaturizationConfig az [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) és az AutoMLBaseSettings szolgáltatásban
      + Oszlopok céljának felülbírálása jellemzősítéshez adott oszlop- és funkciótípussal
      + Transzformátorparaméterek felülbírálása
    + Elalasztó üzenet hozzáadva a explain_model() és a retrieve_model_explanations() számára
    + Új Fogavak betanítható folyamatként (csak előzetes verzió)
    + Elalasztó üzenet hozzáadva a explain_model() és a retrieve_model_explanations() számára.
    + Hozzáadva a Fogavat betanítható folyamatként (csak előzetes verzióban).
    + A cél késések, a gördülőablakok mérete és a maximális horizont automatikus észlelésének támogatása. Ha egy target_lags, target_rolling_window_size vagy max_horizon "auto" értékre van állítva, a heurisztika alkalmazásával megbecsüli a megfelelő paraméter értékét a betanítási adatok alapján.
    + Az előrejelzés kijavítva abban az esetben, ha az adatkészlet egy részletes adatoszlopot tartalmaz, ez a részletes adatmag numerikus típusú, és eltérés van a betanítás és a tesztelési készlet között
    + Ki van javítva a távoli futtatás ismétlődő indexének hibaüzenete az előrejelzési feladatokban
    + Az előrejelzés kijavítva abban az esetben, ha az adatkészlet egy részletes adatoszlopot tartalmaz, ez a részletes adatmag numerikus típusú, és eltérés van a betanítás és a tesztelési készlet között.
    + Ki van javítva a távoli futtatás ismétlődő indexének hibaüzenete az előrejelzési feladatokban.
    + Hozzáadott egy védőkorlátot, amely ellenőrzi, hogy az adatkészlet kiegyensúlyozatlan-e. Ha igen, a rendszer egy védőüzenetet ír a konzolra.
  + **azureml-core**
    + SAS URL-cím lekérése a modellhez a tárolóban a modellobjektumon keresztül. Például: model.get_sas_url()
    + Bemutatja `run.get_details()['datasets']` a beküldött futtatáshoz társított adatkészletek lekért adatait
    + API hozzáadása `Dataset.Tabular.from_json_lines_files` tabularDataset létrehozásához JSON-sorok fájljaiból. A TabularDataset JSON-sorfájljában található táblázatos [](how-to-create-register-datasets.md) adatokról ebben a cikkben talál dokumentációt.
    + További virtuálisgép-méret mezők (operációsrendszer-lemez, GPU-k száma) hozzáadva a supported_vmsizes () függvényhez
    + További mezők hozzáadva a list_nodes () függvényhez a futtatás, a privát és a nyilvános IP-cím, a port stb. megjelenítése érdekében.
    + Lehetőség új mező megadására a fürt kiépítése során – remotelogin_port_public_access, amely engedélyezhető vagy letiltható attól függően, hogy nyitva szeretné-e hagyni vagy bezárva hagyni az SSH-portot a fürt létrehozásakor. Ha nem adja meg, a szolgáltatás intelligensen megnyitja vagy bezárja a portot attól függően, hogy a fürtöt virtuális hálózatban telepíti-e.
  + **azureml-explain-model**
  + **[azureml-core](/python/api/azureml-core/azureml.core)**
    + Sas URL-cím lekérése a modellhez a tárolóban a modellobjektumon keresztül. Például: modell. [get_sas_url()](/python/api/azureml-core/azureml.core.model.model#get-sas-urls--)
    + A futtatás bevezetése. [get_details](/python/api/azureml-core/azureml.core.run%28class%29#get-details--)['datasets'] (adatkészletek) lekérte az elküldött futtatáshoz társított adatkészleteket
    + ADJA hozzá az `Dataset.Tabular` API-t.[ from_json_lines_files() használatával](/python/api/azureml-core/azureml.data.dataset_factory.tabulardatasetfactory#from-json-lines-files-path--validate-true--include-path-false--set-column-types-none--partition-format-none-) hozzon létre egy TabularDatasetet JSON-sorok fájljaiból. A TabularDataset JSON-sorfájljában található táblázatos adatokról a https://aka.ms/azureml-data dokumentációban talál további információt.
    + További virtuálisgép-méret mezők (operációsrendszer-lemez, GPU-k száma) hozzáadva a [supported_vmsizes() függvényhez](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#supported-vmsizes-workspace--location-none-)
    + További mezők hozzáadva a [list_nodes()](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#list-nodes--) függvényhez a futtatás, a privát és a nyilvános IP-cím, a port stb. megjelenítése érdekében.
    + Lehetőség új mező megadására [](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--) a fürt kiépítése során, amely engedélyezhető vagy letiltható attól függően, hogy nyitva szeretné-e hagyni vagy bezárva hagyni az SSH-portot a fürt létrehozásakor. Ha nem adja meg, a szolgáltatás intelligensen megnyitja vagy bezárja a portot attól függően, hogy a fürtöt virtuális hálózatban telepíti-e.
  + **azureml-explain-model**
    + Továbbfejlesztett dokumentáció a magyarázatok kimenetéhez a besorolási forgatókönyvben.
    + Az előrejelált y értékek feltöltésének lehetősége hozzáadva a kiértékelési példák magyarázatán. Hasznosabb vizualizációkat old fel.
    + Az magyarázó tulajdonság hozzáadva a MimicWrapperhez az alapul szolgáló MimicExplainer lekért engedélyezéséhez.
  + **azureml-pipeline-core**
    + Jegyzetfüzet hozzáadva a Module, ModuleVersion és ModuleStep leírására
  + **azureml-pipeline-steps**
    + RScriptStep hozzáadva az R-szkript AML-folyamaton keresztüli futtatásának támogatásához.
    + Ki lett javítva az AzureBatchStep metaadat-paramétereinek az a hibaüzenete, amely a következő hibaüzenetet okozta: "SubscriptionId paraméter hozzárendelése nincs megadva."
  + **azureml-train-automl**
    + Támogatott training_data, validation_data, label_column_name, weight_column_name adatbeviteli formátumként
    + Elalasztó üzenet hozzáadva a explain_model() és a retrieve_model_explanations() számára
  + **[azureml-pipeline-core](/python/api/azureml-pipeline-core)**
    + Hozzáadtunk egy [jegyzetfüzetet,](https://aka.ms/pl-modulestep) amely leírja a [modult](/python/api/azureml-pipeline-core/azureml.pipeline.core.module%28class%29), a [ModuleVersion és a [ModuleStep modult.](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.modulestep)
  + **[azureml-pipeline-steps](/python/api/azureml-pipeline-steps)**
    + [RScriptStep hozzáadva](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.rscriptstep) az R-szkript AML-folyamaton keresztüli futtatásának támogatásához.
    + Ki lett javítva az [AzureBatchStep paraméterének a "SubscriptionId paraméterhez való hozzárendelés nincs megadva" hibaüzenetet okozó [AzureBatchStep] paramétere.
  + **[azureml-train-automl](/python/api/azureml-train-automl-runtime/)**
    + Adatbeviteli training_data, validation_data, label_column_name weight_column_name támogatottak.
    + Elalasztó üzenet hozzáadva a [explain_model()](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#explain-model-fitted-model--x-train--x-test--best-run-none--features-none--y-train-none----kwargs-) és a [retrieve_model_explanations() számára.](/python/api/azureml-train-automl-runtime/azureml.train.automl.runtime.automlexplainer#retrieve-model-explanation-child-run-)


## <a name="2019-09-16"></a>2019-09-16

### <a name="azure-machine-learning-sdk-for-python-v1062"></a>Azure Machine Learning SDK for Python v1.0.62

+ **Új funkciók**
  + Bevezettük `timeseries`  a tulajdonságot a TabularDataset adattitkában. Ez a jellemző lehetővé teszi a TabularDataset adatkészlet adatainak egyszerű időbélyegző-szűrését, például az adatok egy időtartomány vagy a legutóbbi adatok közötti összes adat beszámítását.  https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/timeseries-datasets/tabular-timeseries-dataset-filtering.ipynb egy példajegyzetfüzethez.
  + Betanítás engedélyezése a TabularDataset és a FileDataset használatával. 

  + **azureml-train-core**
      + És `Nccl` támogatás `Gloo` hozzáadva a PyTorch becslőben

+ **Hibajavítások és fejlesztések**
  + **azureml-automl-core**
    + A "lag_length" AutoML-beállítás és a LaggingTransformer elavult.
    + Kijavítottuk a bemeneti adatok helyes érvényesítését, ha adatfolyam formátumban vannak megadva
    + Módosította a fit_pipeline.py fájlt, hogy létrehozza a gráf JSON-ját, és feltöltse az összetevőkbe.
    + A alatt renderelte a `userrun` gráfot `Cytoscape` a használatával.
  + **azureml-core**
    + Újra áttért a kivételkezelésre az ADB-kódban, és az új hibakezelésnek megfelelő módosításokat végrehajtott a következőn:
    + Automatikus MSI-hitelesítés hozzáadva notebookos virtuális gépekhez.
    + Kijavítottunk egy hibát, amely miatt hibás vagy üres modelleket lehetett feltölteni a sikertelen újra kísérlet miatt.
    + Ki van javítva az a hiba, amely miatt a név megváltozik a mód megváltozáskor (például a `DataReference` `DataReference` , vagy a `as_upload` `as_download` hívatáskor). `as_mount`
    + A és `mount_point` `target_path` a kötelező `FileDataset.mount` legyen, a és a esetén pedig `FileDataset.download` nem kötelező.
    + Az időbélyegző-oszlopot nem tartalmazó kivétel akkor lesz kivetve, ha az időbélyegzőhöz kapcsolódó API hívása finom időbélyegző oszlop hozzárendelése nélkül történik, vagy a hozzárendelt időbélyegző-oszlopok el lesznek vetve.
    + Idő soros oszlopokat kell hozzárendelni olyan oszlophoz, amelynek típusa Dátum, ellenkező esetben kivétel várható
    + Az "with_timestamp_columns" API-t hozzárendelő idő-sorozatok a Nincs érték finom/durva időbélyegző oszlopnevét is használhatja, ami törli a korábban hozzárendelt időbélyegző-oszlopokat.
    + A rendszer kivételt ad vissza, ha a durva vagy a finomhangolt időbélyegző-oszlop el lesz dobva, és jelzi a felhasználónak, hogy az időbélyegző-oszlop kizárása után a listából kihagyhatja az időbélyegző-oszlopot, vagy a None (Nincs) értékkel hívhatja meg az with_time_stamp-t az időbélyegző-oszlopok kiadásához
    + A rendszer kivételt ad vissza, ha a durva vagy a finomhangolt időbélyegző-oszlop nem szerepel az oszlopőrzők listájában, és jelzi a felhasználónak, hogy az időbélyegző oszlop az oszloplistában való megtartását követően vagy a with_time_stamp Nincs értékkel hívható meg az időbélyegző-oszlopok felszabadítása után.
    + Naplózás hozzáadva a regisztrált modell méretére.
  + **azureml-explain-model**
    + Ki lett javítva a konzolon a "csomagolási" Python-csomag telepítésekor a következő figyelmeztetés: "A lightgbm támogatott verziójánál régebbi verziót használva frissítsen a 2.2.1-esnél újabb verzióra"
    + Ki van javítva a modellletöltés magyarázata horizontális skálázással a számos funkcióval elérhető globális magyarázatok esetében
    + Kijavítottuk az utánozó magyarázó hiányzó inicializálási példái a kimenet magyarázatát
    + Kijavítottuk a tulajdonságok beállításával kapcsolatos nem módosítható hibát, amikor magyarázattal használó ügyfelet tölt fel két különböző modelltípus használatával
    + Hozzáadott egy get_raw paramétert a explainer.explain() pontozáshoz, így egy pontozó magyarázó a tervezett és a nyers értékeket is visszaadhatja.
  + **azureml-train-automl**
    + Az AutoML-től származó nyilvános API-k az SDK magyarázatának támogatásához – Az AutoML-magyarázatok támogatásának újabb módja az AutoML-funkciók leválasztása és az SDK magyarázata – Az Azureml explain SDK integrált nyers magyarázattámogatása az `automl` AutoML-modellekhez.
    + Az azureml-alapértelmezések eltávolítása a távoli betanító környezetből.
    + A gyorsítótár alapértelmezett helye FileCacheStore-alapúról AzureFileCacheStore 1-re módosult az AutoML-hez a Azure Databricks elérési úton.
    + Kijavítottuk a bemeneti adatok helyes érvényesítését, ha adatfolyam formátumban vannak megadva
  + **azureml-train-core**
    + Az ela source_directory_data_store vissza.
    + Lehetőség hozzáadva az azureml telepített csomagverziók felülbírálása.
    + Dockerfile-támogatás hozzáadva a paraméterhez a `environment_definition` becslőkben.
    + Egyszerűsített elosztott betanításparaméterek a becslőkben.

         ```python
        from azureml.train.dnn import TensorFlow, Mpi, ParameterServer
        ```

## <a name="2019-09-09"></a>2019-09-09

### <a name="new-web-experience-preview-for-azure-machine-learning-workspaces"></a>Új webes élmény (előzetes verzió) Azure Machine Learning munkaterületek számára
Az új webes felhasználói élmény lehetővé teszi, hogy az adattudósok és az adatmérnökök teljes gépi tanulási életciklust töltsenek le az adatok előzetes be- és vizualizációja, valamint a modellek egyetlen helyen való betanításán és üzembe helyezésén át.

![Azure Machine Learning felhasználói felülete (előzetes verzió)](./media/azure-machine-learning-release-notes/new-ui-for-workspaces.jpg)

**Fő funkciók:**

Ezzel az új Azure Machine Learning felülettel a következő lehetőségeket használhatja:
+ A notebookok kezelése vagy a Jupyterre mutató hivatkozás
+ [Automatizált gépi tanulási kísérletek futtatása](tutorial-first-experiment-automated-ml.md)
+ [Adatkészletek létrehozása helyi fájlokból, adattárakból & webfájlokból](how-to-create-register-datasets.md)
+ Az & modellek létrehozására való előkészítésének és előkészítésének megismerása
+ A sodródás monitorzása a modellekhez
+ Irányítópult legutóbbi erőforrásainak megtekintése

A kiadás jelenleg a következő böngészőket támogatja: Chrome, Firefox, Safari és Microsoft Edge előzetes verzió.

**Ismert problémák:**

1. Frissítse a böngészőt, ha a "Hiba történt! Hiba történt az adattömbfájlok betöltésekor" üzenet, ha az üzembe helyezés folyamatban van.

1. A jegyzetfüzetek és fájlok fájlját nem lehet törölni vagy átnevezni. A nyilvános előzetes verzióban a Jupyter felhasználói felülete vagy a Notebook vm terminálja használatával hajthat végre frissítési fájlműveleteket. Mivel ez egy csatlakoztatott hálózati fájlrendszer, minden módosítást a Notebook virtuális gépen kell eszközlni, azonnal megjelenik a Notebook munkaterületen.

1. SSH-ba a notebook virtuális géphez:
   1. Keresse meg a virtuális gép telepítése során létrehozott SSH-kulcsokat. Vagy keresse meg a kulcsokat a Azure Machine Learning munkaterületen> nyissa meg a Számítás lapot> keresse meg a Jegyzetfüzet virtuális gép listában > a tulajdonságokat: másolja ki a kulcsokat a párbeszédpanelről.
   1. Importálja ezeket a nyilvános és titkos SSH-kulcsokat a helyi gépre.
   1. Használja őket az SSH-hoz a notebook virtuális géphez.

## <a name="2019-09-03"></a>2019-09-03
### <a name="azure-machine-learning-sdk-for-python-v1060"></a>Azure Machine Learning SDK for Python v1.0.60

+ **Új funkciók**
  + Bevezettük a FileDataset adatkészletet, amely egy vagy több fájlra hivatkozik az adattárolókban vagy a nyilvános URL-címeken. A fájlok bármilyen formátumúak lehet. A FileDataset lehetővé teszi a fájlok letöltését vagy csatlakoztatását a számításhoz. 
  + A Folyamat Yaml-támogatása hozzáadva a PythonScript-lépéshez, az Adla-lépéshez, a Databricks-lépéshez, a DataTransferStephez és az AzureBatch lépéshez

+ **Hibajavítások és fejlesztések**
  + **azureml-automl-core**
    + Az AutoArima mostantól csak előzetes verziójú, javasolt folyamat.
    + Továbbfejlesztett hibajelentés az előrejelzéshez.
    + Javította a naplózást az előrejelzési feladatok általános helyett egyéni kivételekkel.
    + Az ellenőrzés el lett max_concurrent_iterations hogy kevesebb legyen, mint az iterációk teljes száma.
    + Az AutoML-modellek mostantól AutoMLExceptions-t is visszaadnak
    + Ez a kiadás javítja az automatizált gépi tanulási helyi futtatás végrehajtási teljesítményét.
  + **azureml-core**
    + A Dataset.get_all (workspace) bevezetése, amely a regisztrációs névvel kulcsolt és objektumok `TabularDataset` `FileDataset` szótárát adja vissza.

    ```python
    workspace = Workspace.from_config()
    all_datasets = Dataset.get_all(workspace)
    mydata = all_datasets['my-data']
    ```

    + A és a argumentumaként bemutatja `parition_format` `Dataset.Tabular.from_delimited_files` a `Dataset.Tabular.from_parquet.files` következőt: . Az egyes adatútvonalak partícióinformációi a megadott formátum alapján lesznek oszlopokba kinyerve. A{column_name}" sztringoszlopot hoz létre, és a {column_name:yyyy/MM/dd/HH/mm/ss} dátum/idő oszlopot hoz létre, amelyben az "yyyy", "MM", "dd", "HH", "mm" és "ss" értékeket a rendszer az év, hónap, nap, óra, perc és másodperc érték kinyerére használja a datetime típushoz. A partition_format az első partíciókulcs pozíciójától a fájl elérési útjának végéig kell kezdődni. Például a következő elérési út megadva: .. /USA/2019/01/01/data.csv" ahol a partíció ország és idő szerint van, partition_format=/{Ország}/{PartitionDate:yyyy/MM/dd}/data.csv" sztringoszlopot hoz létre az "USA" értékkel, és a "PartitionDate" datetime oszlop "2019-01-01" értékkel.
        ```python
        workspace = Workspace.from_config()
        all_datasets = Dataset.get_all(workspace)
        mydata = all_datasets['my-data']
        ```

    + A és a argumentumaként bemutatja `partition_format` `Dataset.Tabular.from_delimited_files` a `Dataset.Tabular.from_parquet.files` következőt: . Az egyes adatútvonalak partícióinformációi a megadott formátum alapján lesznek oszlopokba kinyerve. A{column_name}" sztringoszlopot hoz létre, a {column_name:yyyy/MM/dd/HH/mm/ss} pedig datetime oszlopot hoz létre, amelyben az "yyyy", az "MM", a "dd", a "HH", az "mm" és az "ss" az év, a hónap, a nap, az óra, a perc és a másodperc kinyerését használja a dátum/idő típushoz. A partition_format első partíciókulcs pozíciójától a fájl elérési útjának végéig kell kezdődni. Például a következő elérési út megadva: .. /USA/2019/01/01/data.csv" ahol a partíció ország és idő szerint van, partition_format=/{Ország}/{PartitionDate:yyyy/MM/dd}/data.csv" sztringoszlopot hoz létre az "USA" értékkel és a "PartitionDate" datetime oszloppal a "2019-01-01" értékkel.
    + `to_csv_files` A `to_parquet_files` és a metódus hozzá lett adva a következő hez: `TabularDataset` . Ezek a metódusok a és a közötti átalakítást teszik lehetővé azáltal, hogy az adatokat a megadott formátumú `TabularDataset` `FileDataset` fájlokká konvertálják.
    + Automatikusan jelentkezzen be az alapként megadott rendszerkép-beállításjegyzékbe a Model.package() által létrehozott Docker-fájl mentésekor.
    + A "gpu_support" már nem szükséges; Az AML mostantól automatikusan észleli és használja az nvidia docker bővítményt, amikor az elérhetővé válik. Egy későbbi kiadásban el lesz távolítva.
    + A PipelineDrafts létrehozása, frissítése és használata már támogatott.
    + Ez a kiadás javítja az automatizált gépi tanulási helyi futtatás végrehajtási teljesítményét.
    + A felhasználók név alapján is lekérdezhet metrikákat a futtatás előzményeiből.
    + Javította a naplózást az előrejelzési feladatok általános helyett egyéni kivételekkel.
  + **azureml-explain-model**
    + Új feature_maps hozzá az új MimicWrapperhez, így a felhasználók nyers funkcióra vonatkozó magyarázatokat kapnak.
    + A magyarázatok feltöltése érdekében az adatkészlet-feltöltések alapértelmezés szerint ki vannak kapcsolva, és a következővel engedélyezhetők újra: upload_datasets=True
    + A "is_law" szűrőparaméterek hozzáadva a magyarázatok listájához és letöltéséhez.
    + Hozzáadja a `get_raw_explanation(feature_maps)` metódust a globális és a helyi magyarázatobjektumhoz is.
    + Verzióellenőrzés hozzáadva a lightgbm-hoz nyomtatott figyelmeztetéssel, ha a támogatott verziónál régebbi
    + Optimalizált memóriahasználat magyarázatok kötegeléskor
    + Az AutoML-modellek mostantól AutoMLExceptions-t is visszaadnak
  + **azureml-pipeline-core**
    + A PipelineDrafts létrehozására, frissítésére és használatára vonatkozó támogatás hozzáadva – használható a kezelhető folyamatdefiníciók fenntartására, és interaktívan használható a futtatáshoz
  + **azureml-train-automl**
    + Létrehozott egy funkciót a GPU-kompatibilis pytorch v1.1.0, :::no-loc text="cuda"::: toolkit 9.0 és pytorch-transformers adott verzióinak telepítéséhez, amelyek a TO/ XLNet távoli Python-futásidejű környezetben való engedélyezéséhez szükségesek.
  + **azureml-train-core**
    + Néhány hiperparamétertér-definíciós hiba korai meghibásodása közvetlenül az SDK-ban a kiszolgálóoldal helyett.

### <a name="azure-machine-learning-data-prep-sdk-v1114"></a>Azure Machine Learning Data Prep SDK v1.1.14
+ **Hibajavítások és fejlesztések**
  + Az ADLS/ADLSGen2-be nyers elérési út és hitelesítő adatok használatával történő írás engedélyezése.
  + Kijavítottunk egy hibát, amely `include_path=True` miatt a nem működött a hez. `read_parquet`
  + Az `to_pandas_dataframe()` "Érvénytelen tulajdonságérték: hostSecret" kivétel által okozott hiba kijavítva.
  + Kijavítottunk egy hibát, amely miatt a fájlok nem olvashatók a DBFS-ben Spark módban.

## <a name="2019-08-19"></a>2019-08-19

### <a name="azure-machine-learning-sdk-for-python-v1057"></a>Azure Machine Learning SDK for Python v1.0.57
+ **Új funkciók**
  + Engedélyezve `TabularDataset` az AutomatedML számára. További információ a ről: `TabularDataset` https://aka.ms/azureml/howto/createdatasets .

+ **Hibajavítások és fejlesztések**
  + **azure-cli-ml**
    + Most már frissítheti az AKS-fürtön üzembe helyezett pontozási végpont TLS-/SSL-tanúsítványát mind a Microsoft által létrehozott, mind az ügyfél tanúsítványa számára.
  + **azureml-automl-core**
    + Ki lett javítva az AutoML egyik problémája, amely miatt a hiányzó címkékkel nem lett megfelelően eltávolítva a sor.
    + Továbbfejlesztett hibanaplózás az AutoML-ben; A teljes hibaüzenetek mostantól mindig a naplófájlba lesznek írva.
    + Az AutoML frissítette a csomagrögzítését, hogy tartalmazza a `azureml-defaults` következőket: `azureml-explain-model` , és `azureml-dataprep` . Az AutoML a továbbiakban nem figyelmeztet a csomagok eltérésére (kivéve a `azureml-train-automl` csomagokat).
    + Kijavítottunk egy hibát, amely miatt a cv felosztásai nem egyenlő méretűek, ezért a tároló `timeseries`  kiszámítása meghiúsult.
    + Amikor együttes iterációt futtatunk a keresztellenőrzési betanítási típushoz, és végül nem tudjuk letölteni a teljes adatkészleten betanított modelleket, inkonzisztenciát okoztunk a modell súlyozása és a szavazási együttesbe bemásolt modellek között.
    + Kijavítottuk azt a hibát, amely akkor merült fel, amikor a betanítás és/vagy az ellenőrzési címkék (y és y_valid) pandas-adatkeret formájában vannak megtéve, de nem umpy-tömbként.
    + Ki lett javítva az előrejelzési tevékenységekkel kapcsolatos probléma, amely akkor merült fel, amikor a bemeneti táblák logikai oszlopai között Nincs történt.
    + Lehetővé teszi, hogy az AutoML-felhasználók eldobják az előrejelzéshez nem elég hosszú betanítássorozatokat. – Lehetővé teszi, hogy az AutoML-felhasználók eldobják a tesztelési készletből azt a halmazt, amely nem létezik a betanítás során az előrejelzés során.
  + **azureml-core**
    + A paraméterrendezéssel blob_cache_timeout hiba kijavítva.
    + Külső illesztés és a kivételtípusok átalakítása a rendszerhibákhoz.
    + A távoli futtatás Key Vault titkos kulcsok támogatása hozzáadva. Adjon hozzá egy azureml.core.keyvault.Keyvault osztályt a munkaterülethez társított kulcstartó titkos kulcstartójának hozzáadásához, lekértéhez és listához. A támogatott műveletek a következőek:
      + azureml.core.workspace.Workspace.get_default_keyvault()
      + azureml.core.keyvault.Keyvault.set_secret(név, érték)
      + azureml.core.keyvault.Keyvault.set_secrets(secrets_dict)
      + azureml.core.keyvault.Keyvault.get_secret(név)
      + azureml.core.keyvault.Keyvault.get_secrets(secrets_list)
      + azureml.core.keyvault.Keyvault.list_secrets()
    + További módszerek az alapértelmezett kulcstartó beszerzéséhez és a titkos kulcsok távoli futtatás során történő lekért beszerzéséhez:
      + azureml.core.workspace.Workspace.get_default_keyvault()
      + azureml.core.run.Run.get_secret(név)
      + azureml.core.run.Run.get_secrets(secrets_list)
    + További felülbírálási paraméterek hozzáadva a submit-hyperdrive cli parancshoz.
    + Az API-hívások megbízhatóságának növelése kiterjeszti az újrakéréseket a gyakori kéréstár-kivételekre.
    + A futtatási kérelmek beküldésének támogatása hozzáadva egy elküldött futtatásból.
    + Ki lett javítva a lejáró SAS-jogkivonat a FileWatcherben, amely miatt a fájlok feltöltése leállt a kezdeti jogkivonat lejárta után.
    + TÁMOGATOTT a HTTP csv-/tsv-fájlok importálása az adatkészlet Python SDK-ában.
    + A Workspace.setup() metódus elavult. A felhasználók számára megjelenő figyelmeztető üzenet a create() vagy a get()/from_config() ehelyett a következőt javasolja: .
    + A Environment.add_private_pip_wheel() hozzáadva, amely lehetővé teszi privát egyéni Python-csomagok feltöltését a munkaterületre, és biztonságosan használja őket a környezet `whl` létrehozásához/materializáláshoz.
    + Most már frissítheti az AKS-fürtön üzembe helyezett pontozási végpont TLS-/SSL-tanúsítványát mind a Microsoft által létrehozott, mind az ügyfél tanúsítványa számára.
  + **azureml-explain-model**
    + Paraméter hozzáadva, amely hozzáad egy modellazonosítót a feltöltés magyarázataihoz.
    + Címkézés `is_raw` hozzáadva a memóriában és a feltöltésben a magyarázatok számára.
    + Pytorch-támogatás és tesztek hozzáadva az azureml-explain-model csomaghoz.
  + **azureml-opendatasets**
    + Az automatikus tesztkörnyezet észlelésének és naplózásának támogatása.
    + Osztályok hozzáadva az egyesült államokbeli népesség megyén és zip-fájlonként való lekért lekért értékére.
  + **azureml-pipeline-core**
    + Címketulajdonság hozzáadva a bemeneti és kimeneti portok definícióihoz.
  + **azureml-telemetry**
    + Javítva a helytelen telemetriakonfiguráció.
  + **azureml-train-automl**
    + Ki lett javítva az a hiba, amely miatt a hiba nem lett naplózva a telepítési futtatás "errors" mezőjében, ezért nem volt tárolva a szülő futtatás "errors" (hibák) mezőjében.
    + Ki lett javítva az AutoML egyik problémája, amely miatt a hiányzó címkékkel nem lett megfelelően eltávolítva a sor.
    + Lehetővé teszi, hogy az AutoML-felhasználók eldobják az előrejelzéshez nem elég hosszú betanítássorozatokat.
    + Annak engedélyezése, hogy az AutoML-felhasználók eldobják a tesztelési készletből azt a halmazt, amely nem létezik a betanítás-készletben az előrejelzés során.
    + Az AutoMLStep mostantól továbbítja a konfigurációt a háttérnek, hogy elkerülje az új konfigurációs paraméterek módosításaival vagy kiegészítésével `automl` kapcsolatos problémákat.
    + Az AutoML Data Guardrail mostantól nyilvános előzetes verzióban érhető el. A betanítás után egy Data Guardrail-jelentés (besorolási/regressziós feladatokhoz), valamint az SDK API-n keresztül is elérhető lesz.
  + **azureml-train-core**
    + Torch 1.2-támogatás hozzáadva a PyTorch Estimatorban.
  + **azureml-widgets**
    + Továbbfejlesztett keveredőmátrix-diagramok a besorolási betanításhoz.

### <a name="azure-machine-learning-data-prep-sdk-v1112"></a>Azure Machine Learning Data Prep SDK v1.1.12
+ **Új funkciók**
  + A sztringlisták mostantól bemenetként is átveheti őket a `read_*` metódusok számára.

+ **Hibajavítások és fejlesztések**
  + A teljesítménye `read_parquet` javult a Sparkban való futtatáskor.
  + Kijavítottunk egy hibát, amely miatt egyetlen, nem egyértelmű dátumformátumú oszlop esetében sikertelen `column_type_builder` volt a művelet.

### <a name="azure-portal"></a>Azure Portal
+ **Előzetes verziójú funkció**
  + A napló- és kimeneti fájlok streamelése mostantól elérhető a futtatás részleteit tartalmazó lapokhoz. A fájlok valós időben streamelik a frissítéseket, amikor az előnézeti váltógomb be van kapcsolva.
  + A kvóta munkaterületi szinten való beállításának lehetősége előzetes verzióban érhető el. Az AmlCompute-kvóták az előfizetés szintjén vannak lefoglalva, de most már lehetővé teszi, hogy ezt a kvótát elosztsa a munkaterületek között, és igazságos megosztáshoz és irányításhoz rendelje hozzá. Egyszerűen kattintson a munkaterület bal oldali navigációs sávján található **Usages+Quotas (Használat+kvóták)** panelre, és válassza a **Configure Quotas (Kvóták konfigurálása)** lapot. A kvóták munkaterületi szinten való beállításához előfizetés-rendszergazdának kell lennie, mivel ez egy munkaterületek közötti művelet.

## <a name="2019-08-05"></a>2019-08-05

### <a name="azure-machine-learning-sdk-for-python-v1055"></a>Azure Machine Learning SDK for Python 1.0.55

+ **Új funkciók**
  + A jogkivonat-alapú hitelesítés mostantól támogatott az AKS-hez üzembe helyezett pontozási végpontra tett hívásokhoz. Továbbra is támogatjuk az aktuális kulcsalapú hitelesítést, és a felhasználók egyszerre egy ilyen hitelesítési mechanizmust használhatnak.
  + Lehetőség a virtuális hálózat (VNet) mögötti blobtároló adattárként való regisztrálására.

+ **Hibajavítások és fejlesztések**
  + **azureml-automl-core**
    + Kijavít egy hibát, amely miatt a CV-felosztások érvényesítési mérete kicsi, és rossz előrejelezhető és valós regressziós és előrejelzési diagramokat ad vissza.
    + Javult az előrejelzési feladatok naplózása a távoli futtatáson, így a felhasználó átfogó hibaüzenetet kap, ha a futtatás sikertelen volt.
    + Kijavítva a hibája, `Timeseries` ha az előfeldolgozási jelző Igaz.
    + Az előrejelzési adatok ellenőrzésével kapcsolatos hibaüzenetek hatékonyabbak voltak.
    + Az AutoML-futtatás memóriafelhasználásának csökkentése az adatkészletek eldobása és/vagy lusta betöltése miatt, különösen a folyamatfuttetők között
  + **azureml-contrib-explain-model**
    + Az model_task jelző hozzáadva, hogy a felhasználó felülbírálja a modelltípus alapértelmezett automatikus következtetési logikáját
    + Widgetváltozások: Automatikusan telepít a használatával, nincs több telepítés/engedélyezés – támogatás magyarázata globális funkciós fontossággal `contrib` `nbextension` (például permutatív)
    + Irányítópult-módosítások: - Dobozdiagramok és vonaldiagramok az összefoglaló lapon való ábrázolás mellett – Sokkal gyorsabb a diagram újratolása a "Top -k" csúszkán – hasznos üzenet a top-k kiszámításának magyarázatához – Hasznos, testre szabható üzenetek diagramok helyett, ha az adatok `beeswarm` `beeswarm` nem állnak rendelkezésre
  + **azureml-core**
    + A Model.package() metódus hozzáadva a modelleket és azok függőségeit beágyazó Docker-lemezképek és Docker-fájlok létrehozásához.
    + Frissítve lett a helyi webszolgáltatások a környezeti objektumokat tartalmazó InferenceConfigs elfogadásához.
    + Ki van javítva, hogy a Model.register() érvénytelen modelleket ad vissza a". (az aktuális könyvtárhoz) paraméterként lesz model_path paraméterként.
    + A Run.submit_child a funkció tükrözi az Experiment.submit szolgáltatásokat, miközben a futtatás meg van határozva a beküldött gyermekfutat szülőjeként.
    + A Model.register konfigurációs lehetőségeinek támogatása a Run.register_model.
    + JAR-feladatok futtatása meglévő fürtön.
    + Mostantól támogatja instance_pool_id és cluster_log_dbfs_path paramétereket.
    + Környezeti objektum használatának támogatása a modell webszolgáltatásban való üzembe helyezésekor. Az Environment objektum mostantól az InferenceConfig objektum részeként is meg lehet adni.
    + Appinsifht-leképezés hozzáadása új régiókhoz – centralus – westus – northcentralus
    + Dokumentáció hozzáadva az összes Datastore osztály összes attribútuma számára.
    + Új blob_cache_timeout hozzáadva a következő `Datastore.register_azure_blob_container` hez: .
    + Új save_to_directory és load_from_directory az azureml.core.environment.Environment.Environment metódushoz.
    + Hozzá van adva az "az ml environment download" és az "az ml environment register" parancs a parancssori felülethez.
    + Új Environment.add_private_pip_wheel hozzáadva.
  + **azureml-explain-model**
    + Adatkészlet-követés hozzáadva a Magyarázatok szolgáltatáshoz az Adatkészlet szolgáltatás (előzetes verzió) használatával.
    + A globális magyarázatok streamelésekor az alapértelmezett kötegméret 10 ezerről 100-ra csökkent.
    + A model_task jelző hozzáadva, hogy a felhasználó felülbírálja a modelltípus alapértelmezett automatikus következtetési logikáját.
  + **azureml-mlflow**
    + Kijavítva a hiba a mlflow.azureml.build_image, ahol a beágyazott könyvtárak figyelmen kívül vannak hagyva.
  + **azureml-pipeline-steps**
    + JAR-feladatok meglévő fürtön való futtatásának Azure Databricks képesség.
    + Támogatás hozzáadva instance_pool_id és cluster_log_dbfs_path a DatabricksStep lépéshez.
    + Folyamatparaméterek támogatása hozzáadva a DatabricksStep lépésben.
  + **azureml-train-automl**
    + Hozzáadva `docstrings` az Együttessel kapcsolatos fájlokhoz.
    + A dokumentumok frissítve a és a `max_cores_per_iteration` nyelvnek megfelelőbb nyelvre `max_concurrent_iterations`
    + Javult az előrejelzési feladatok naplózása a távoli futtatáson, így a felhasználó átfogó hibaüzenetet kap, ha a futtatás sikertelen volt.
    + A get_data el a folyamat `automlstep` jegyzetfüzetből.
    + A támogatása a `dataprep` -ban `automlstep` indult el.

### <a name="azure-machine-learning-data-prep-sdk-v1110"></a>Azure Machine Learning Data Prep SDK v1.1.10

+ **Új funkciók**
  + Mostantól kérheti adott vizsgálók (például hisztogram, pontdiagram stb.) adott oszlopokon való végrehajtását.
  + A parallelize argumentuma hozzá van adva a következő hez: `append_columns` . Ha igaz, az adatok be lesznek töltve a memóriába, de a végrehajtás párhuzamosan fog futni; Ha hamis, a végrehajtás streamelési, de egyszálas lesz.

## <a name="2019-07-23"></a>2019-07-23

### <a name="azure-machine-learning-sdk-for-python-v1053"></a>Azure Machine Learning SDK for Python 1.0.53

+ **Új funkciók**
  + Az Machine Learning mostantól támogatja az ONNX-modellek betanítása a távoli számítási célon
  + Azure Machine Learning már lehetővé teszi a betanítás folytatását egy korábbi futtatásból, ellenőrzőpontból vagy modellfájlból.
    + Ismerje meg, hogyan folytathatja egy korábbi futtatás betanítását [becslők használatával](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/tensorflow/train-tensorflow-resume-training/train-tensorflow-resume-training.ipynb)

+ **Hibajavítások és fejlesztések**
  + **azure-cli-ml**
    + A "model deploy" és a "service update" parancssori felületi parancsok mostantól elfogadják a paramétereket, a konfigurációs fájlokat vagy a kettő kombinációját. A paraméterek elsőbbséget élveznek a fájlokban lévő attribútumokkal.
    + A modell leírása mostantól frissíthető a regisztráció után
  + **azureml-automl-core**
    + Frissítse a NimbusML függőséget az 1.2.0-s verzióra (az aktuális legújabb verzióra).
    + Támogatás hozzáadva a NimbusML& autoML-becslőknek használható folyamatokhoz.
    + Kijavítottunk egy hibát az Együttes kiválasztási eljárásban, amely feleslegesen növekedt az eredményül kapott együttesen akkor is, ha a pontszámok állandóak maradtak.
    + Engedélyezze néhány funkció újbóli felhasználásának engedélyezését a CV-felosztások között az előrejelzési feladatokhoz. Ez nagyjából egy olyan tényezővel gyorsítja fel a beállítás futási idejét, n_cross_validations költséges késések és gördülő ablakok miatt.
    + A probléma megoldása, ha az idő kívül esik a pandas által támogatott időtartományon. Most egy DataException-t is felvetünk, ha az idő rövidebb, mint a pd. Timestamp.min vagy nagyobb, mint pd. Timestamp.max
    + Az előrejelzés most különböző gyakoriságokat tesz lehetővé a betanítás és a tesztelési készletekben, ha azok igazíthatóak. Például a "januártól kezdődő negyedév" és a "negyedév októbertől kezdődően" igazítható.
    + A "parameters" tulajdonság hozzá lett adva a TimeSeriesTransformerhez.
    + Távolítsa el a régi kivételosztályokat.
    + Az előrejelzési feladatokban a paraméter mostantól egyetlen egész értéket vagy egész `target_lags` számlistát fogad el. Ha az egész szám meg van téve, csak egy késés jön létre. Ha meg van téve egy lista, a rendszer a késések egyedi értékeit fogja figyelembe venni. target_lags=[1, 2, 2, 4] érték egy, két és négy pont késését hozza létre.
    + Javítva van az oszloptípusok átalakítás utáni elvesztésével kapcsolatos hiba (hiba csatolva);
    + A fájlban y_query, hogy egy objektumtípus, amely a kezdetekor `model.forecast(X, y_query)` Nincs(s) adatokat tartalmaz (#459519).
    + Várt értékek hozzáadása a `automl` kimenethez
  + **azureml-contrib-datadrift**
    +  A példajegyzetfüzetek fejlesztései, beleértve az azureml-opendatasets helyett az azureml-contrib-opendatasets helyetti váltást, valamint az adatok gazdagításakor a teljesítmény javításait
  + **azureml-contrib-explain-model**
    + Ki van javítva az transformations argumentum az AZUREML-contrib-explain-model csomag nyers funkció fontossága esetében
    + Szegmentálások hozzáadva a kép magyarázataihoz az AzureML-contrib-explain-model csomag kép magyarázó részében
    + Scipy ritka támogatás hozzáadva a MertaExplainerhez
    + Hozzáadva az utánzó magyarázóhoz, ha a globális magyarázatok kötegelt streameléssel való továbbítása javítja a `batch_size` `include_local=False` DecisionTreeExplainableModel végrehajtási idejét
  + **azureml-contrib-featureengineering**
    + A set_featurizer_timeseries_params() hívásának kijavítva: dict value type change and null check ( dict value type change and null check – Add notebook for featurizer(): dict value type change and null check – Add notebook for `timeseries`  featurizer
    + Frissítse a NimbusML függőséget az 1.2.0-s verzióra (az aktuális legújabb verzióra).
  + **azureml-core**
    + DBFS-adattárak csatolásának lehetősége az AzureML CLI-ben
    + Kijavítva a hiba, amely az adattár feltöltésével kapcsolatos, ahol a rendszer üres mappát hozott létre, ha `target_path` elindította `/`
    + Javítva `deepcopy` a ServicePrincipalAuthentication problémája.
    + Hozzá van adva az "az ml environment show" és az "az ml environment list" parancs a parancssori felülethez.
    + A környezetek mostantól támogatják a base_dockerfile megadását a már felépített base_image.
    + A nem használt RunConfiguration auto_prepare_environment elavultként lett megjelölve.
    + A modell leírása mostantól frissíthető a regisztráció után
    + Hibajavítás: A modell és a rendszerkép törlése mostantól további információt nyújt az őket függő, a törlést egy upstream függőség miatt meghiúsuló upstream objektumok leolvasásával kapcsolatban.
    + Kijavítva a hiba, amely üres időtartamot nyomtatott az egyes környezetek munkaterületének létrehozásakor előforduló üzemelő példányok esetében.
    + Továbbfejlesztett hiba-kivételek a munkaterületek létrehozásakor. Így a felhasználók nem látják a "Nem hozható létre munkaterület. Nem található..." üzenetként, és ehelyett a tényleges létrehozási hibát fogja látni.
    + Jogkivonat-hitelesítés támogatása az AKS-webszolgáltatásokban.
    + Metódus `get_token()` hozzáadása `Webservice` objektumokhoz.
    + CLI-támogatás hozzáadva a gépi tanulási adatkészletek kezeléséhez.
    + `Datastore.register_azure_blob_container` Mostantól választhatóan egy (másodpercben megadott) értéket vesz fel, amely a blobfuse csatlakoztatási paramétereit konfigurálja az adattár gyorsítótár-lejáratának `blob_cache_timeout` engedélyezéséhez. Az alapértelmezett érték nem időtúllépés, például egy blob beolvassa, a helyi gyorsítótárban marad, amíg a feladat be nem fejeződik. A legtöbb feladat ezt a beállítást részesíti előnyben, de egyes feladatoknak több adatot kell beolvasnunk egy nagy adatkészletből, mint amennyit elférnek a csomópontjaikon. Ezekhez a feladatokhoz a paraméter finomhangolása segít a sikerben. A paraméter finomhangolásakor legyen nagy gond: a túl alacsony érték beállítása gyenge teljesítményt eredményezhet, mivel az alapidőkben használt adatok lejárhatnak az újra használat előtt. Az összes olvasás a blobtárolóból/hálózatból történik a helyi gyorsítótár helyett, ami negatív hatással van a betanítás időire.
    + A modell leírása mostantól megfelelően frissíthető a regisztráció után
    + A modell és a rendszerkép törlése mostantól további információkat nyújt az őket függő, a törlés meghiúsulását okozó upstream objektumokról
    + A távoli futtatás erőforrás-használatának javítása az azureml.mlflow használatával.
  + **azureml-explain-model**
    + Ki van javítva az transformations argumentum az AZUREML-contrib-explain-model csomag nyers funkció fontossága esetében
    + scipy ritka támogatás hozzáadása a<
    + az alakzat lineáris magyarázó burkolója, valamint egy másik szint hozzáadva a lineáris modellek magyarázatának táblázatos magyarázó szintjéhez
    + for mimic explainer in explain model library, fixed error when include_local=False for sparse data input
    + várt értékek hozzáadása a `automl` kimenethez
    + ki van javítva a permutációs funkció fontossága, ha az átalakítások argumentuma meg van adva a nyers funkció fontossága érdekében
    + hozzáadva a magyarázó utánzóhoz, ha a globális magyarázatokat streameli kötegben a `batch_size` `include_local=False` DecisionTreeExplainableModel végrehajtási ideje javításához
    + a modell elmagyarázhatósági kódtárához, rögzített feketedoboz-magyarázókhoz, ahol a pandas-adatkeret bemenete szükséges az előrejelzéshez
    + Kijavítottunk egy hibát, amely miatt időnként lebegőutas listát adna vissza, nem pedig egy `explanation.expected_values` lebegőutas listát.
  + **azureml-mlflow**
    + A teljesítmény javítása mlflow.set_experiment (experiment_name)
    + Ki van javítva az InteractiveLoginAuthentication mlflow-tracking_uri
    + A távoli futtatás erőforrás-használatának javítása az azureml.mlflow használatával.
    + Az azureml-mlflow csomag dokumentációjának fejlesztése
    + Javításhiba, amely miatt mlflow.log_artifacts("my_dir") a "my_dir/<artifact-paths>" <artifact-paths helyett az "<artifact-paths>" (Összetevők elérési útjai)>"
  + **azureml-opendatasets**
    + A `pyarrow` régi `opendatasets` verziókban (<0.14.0) rögzítve az újonnan bevezetett memória-probléma miatt.
    + Helyezze át az azureml-contrib-opendatasets adatokat az azureml-opendatasets-be.
    + Lehetővé teszi a nyílt adatkészletosztályok regisztrációját a Azure Machine Learning, és zökkenőmentesen kihasználhatja az AML-adatkészlet képességeit.
    + Jelentősen javíthatja a NoaaIsdWeather gazdagítási teljesítményét a nem SPARK-verziókban.
  + **azureml-pipeline-steps**
    + A DBFS-adattár mostantól támogatott a DatabricksStep bemenetei és kimenetei esetében.
    + Frissült a Azure Batch lépés dokumentációja a bemenetek/kimenetek tekintetében.
    + Az AzureBatchStepben az alapértelmezett *érték delete_batch_job_after_finish* true értékre *változott.*
  + **azureml-telemetry**
    +  Helyezze át az azureml-contrib-opendatasets adatokat az azureml-opendatasets-be.
    + Lehetővé teszi a nyílt adatkészletosztályok regisztrációját a Azure Machine Learning, és zökkenőmentesen kihasználhatja az AML-adatkészlet képességeit.
    + Jelentősen javíthatja a NoaaIsdWeather gazdagítási teljesítményét a nem SPARK-verziókban.
  + **azureml-train-automl**
    + Frissült a get_output, hogy tükrözze a tényleges visszatérési típust, és további megjegyzéseket adjon meg a kulcstulajdonságok lekért értékével kapcsolatban.
    + Frissítse a NimbusML függőséget az 1.2.0-s verzióra (az aktuális legújabb verzióra).
    + várt értékek hozzáadása a `automl` kimenethez
  + **azureml-train-core**
    + A sztringek mostantól számítási célként vannak elfogadva az automatikus hiperparaméter-finomhangoláshoz
    + A nem használt RunConfiguration auto_prepare_environment elavultként lett megjelölve.

### <a name="azure-machine-learning-data-prep-sdk-v119"></a>Azure Machine Learning Data Prep SDK v1.1.9

+ **Új funkciók**
  + Mostantól támogatott a fájlok közvetlen olvasása HTTP- vagy HTTPS-URL-címről.

+ **Hibajavítások és fejlesztések**
  + Továbbfejlesztett hibaüzenet a Parquet-adatkészlet távoli forrásból való beolvasása során (amely jelenleg nem támogatott).
  + Kijavítottunk egy hibát, amikor Parquet-fájlformátumba írtunk az ADLS Gen 2-ben, és frissítettünk egy ADLS Gen 2-tárolónevet az elérési úton.

## <a name="2019-07-09"></a>2019-07-09

### <a name="visual-interface"></a>Vizuális felület
+ **Előzetes verziójú funkciók**
  + Hozzá van adva az "R-szkript végrehajtása" modul a vizuális felületen.

### <a name="azure-machine-learning-sdk-for-python-v1048"></a>Azure Machine Learning SDK for Python v1.0.48

+ **Új funkciók**
  + **azureml-opendatasets**
    + **Az azureml-contrib-opendatasets** mostantól **azureml-opendatasets néven érhető el.** A régi csomag továbbra is működik, de javasoljuk az **azureml-opendatasets** használatát a további képességek és fejlesztések érdekében.
    + Ez az új csomag lehetővé teszi a nyitott adatkészletek adatkészletként való regisztrálását Azure Machine Learning munkaterületen, és az Adatkészlet által kínált funkciókat kihasználhatja.
    + Emellett olyan meglévő képességeket is tartalmaz, mint a nyitott adatkészletek pandas-/SPARK-adatkeretekként való megnyitása, valamint helyilledés egyes adatkészletek, például az időjárás.

+ **Előzetes verziójú funkciók**
    + A HyperDriveConfig mostantól elfogadhatja a folyamatobjektumot paraméterként a hiperparaméterek folyamatokkal való finomhangolásának támogatásához.

+ **Hibajavítások és fejlesztések**
  + **azureml-train-automl**
    + Ki van javítva az oszloptípusok átalakítás utáni elvesztésével kapcsolatos hiba.
    + Kijavítottuk a hibát, amely y_query, hogy egy olyan objektumtípus legyen, amely kezdetben None(s) típusú(k)t tartalmaz.
    + Ki lett javítva az Együttesválasztási eljárás azon problémája, amely szükségtelenül növekedt az eredményül kapott együttesen, még akkor is, ha a pontszámok állandóak maradtak.
    + Ki van javítva az AutoMLStep list_models és blokkbeállításai list_models hiba.
    + Kijavítottuk azt a hibát, amely megakadályozta az előfeldolgozás használatát, amikor az AutoML az Azure ML-folyamatok kontextusában lett volna használva.
  + **azureml-opendatasets**
    + Az azureml-contrib-opendatasets átkerült az azureml-opendatasets-be.
    + Lehetővé tette, hogy a nyitott adatkészletosztályok regisztrálva Azure Machine Learning munkaterületre, és zökkenőmentesen kihasználják az AML-adatkészlet képességeit.
    + A NoaaIsdWeather gazdagítási teljesítménye jelentősen javult a nem SPARK-verziókban.
  + **azureml-explain-model**
    + Frissített online dokumentáció az értelmezhetőségi objektumokhoz.
    + Hozzáadva az utánzó magyarázóhoz, ha a globális magyarázatokat kötegekbe streameli, így javítva a Modell magyarázhatósági `batch_size` `include_local=False` kódtárának DecisionTreeExplainableModel végrehajtási idejét.
    + Ki lett javítva a hiba, amely miatt időnként lebegő lebegőutas lista helyett időnként lebegő lebegőutas `explanation.expected_values` listára térne vissza.
    + A modell magyarázata kódtárban a várt értékek hozzáadva az utánzott `automl` magyarázó kimenethez.
    + Ki van javítva a permutáció funkció fontossága, amikor az átalakítások argumentuma meg van adva a nyers funkció fontossága érdekében.
  + **azureml-core**
    + A DBFS-adattárak csatolásának lehetősége hozzáadva az AzureML CLI-ben.
    + Kijavítottuk az adattárfeltöltéssel kapcsolatos hibát, amely miatt a rendszer üres mappát hozott létre, ha a `target_path` rendszer elindította az -t. `/`
    + Két adatkészlet összehasonlítása engedélyezve.
    + A modell és a rendszerkép törlése mostantól további információt nyújt az őket függő, a törlést egy upstream függőség miatt meghiúsuló upstream objektum lekért objektumról.
    + A nem használt RunConfiguration beállítás elavult a auto_prepare_environment.
  + **azureml-mlflow**
    + Az azureml.mlflow-t használó távoli futtatás erőforrás-kihasználtsága javult.
    + Továbbfejlesztettük az azureml-mlflow csomag dokumentációját.
    + Kijavítva a hiba, mlflow.log_artifacts("my_dir") az összetevők "artifact-paths" helyett my_dir "my_dir/artifact-paths" alá ment.
  + **azureml-pipeline-core**
    + A hash_paths lépés paraméter-száma elavult, és a jövőben el lesz távolítva. A rendszer alapértelmezés szerint kivonatolt source_directory (kivéve a vagy a alatt felsorolt `.amlignore` `.gitignore` fájlokat)
    + A Modul és a ModulStepek további fejlesztése a számítástípus-specifikus modulok támogatásához, a RunConfiguration integrációjára és egyéb módosításokra való felkészüléshez a számítási típusspecifikus modulhasználat folyamatokban való feloldásához.
  + **azureml-pipeline-steps**
    + AzureBatchStep: Továbbfejlesztett dokumentáció a bemenetek/kimenetek tekintetében.
    + AzureBatchStep: Az delete_batch_job_after_finish alapértelmezett értéke true (igaz) értékre változott.
  + **azureml-train-core**
    + A rendszer mostantól számítási célként fogadja el a sztringeket az automatikus hiperparaméter-finomhangoláshoz.
    + A nem használt RunConfiguration beállítás elavult a auto_prepare_environment.
    + A és a paraméter `conda_dependencies_file_path` `pip_requirements_file_path` `conda_dependencies_file` `pip_requirements_file` elavult.
  + **azureml-opendatasets**
    + A NoaaIsdWeather teljesítményének növelése a nem SPARK-verziókban.

## <a name="2019-04-26"></a>2019-04-26

### <a name="azure-machine-learning-sdk-for-python-v1033-released"></a>Azure Machine Learning SDK for Python 1.0.33-as verziója jelent meg.

+ Az Azure ML Hardware Accelerated Models [FPGA-kban általánosan](how-to-deploy-fpga-web-service.md) elérhető.
  + Most már [használhatja az azureml-accel-models csomagot a](how-to-deploy-fpga-web-service.md) következőre:
    + Egy támogatott mély neurális hálózat súlyozásának betanítása (ResNet 50, ResNet 152, DenseNet-121, VGG-16 és SSD-VGG)
    + A transzfertanulás használata a támogatott DNN-sel
    + A modell regisztrálása a modellkezelés szolgáltatásban és a modell tárolóba való helyeznie
    + A modell üzembe helyezése azure-beli virtuális gépen FPGA-val Azure Kubernetes Service (AKS-) fürtben
  + A tároló üzembe helyezése egy [Azure Data Box Edge-kiszolgálóeszközön](../databox-online/azure-stack-edge-overview.md)
  + Az adatok pontozása a gRPC-végponttal ezzel a [mintával](https://github.com/Azure-Samples/aml-hardware-accelerated-models)

### <a name="automated-machine-learning"></a>Automatizált gépi tanulás

+ Szolgáltatás-frissítés a teljesítményoptimalizálás dinamikus :::no-loc text="featurizers"::: hozzáadásának engedélyezéséhez. Új: :::no-loc text="featurizers"::: munkahelyi beágyazás, a bizonyítékok súlyozása, célkódolások, szövegcél kódolása, fürt távolsága
+ Smart CV az automatizált gépi tanulási betanítás/érvényes felosztások kezeléshez
+ Kevés memóriaoptimalizálási módosítás és a futásidejű teljesítmény javítása
+ Teljesítménybeli javulás a modell magyarázatában
+ ONNX modellátalakítás helyi futtatáshoz
+ Albélyegző támogatása hozzáadva
+ Intelligens leállítás, ha nincsenek meghatározva kilépési feltételek
+ Halmozott együttesek

+ Idősoros előrejelzés
  + Új előrejelzési előrejelzési függvény
  + Mostantól használhatja a gördülő eredet keresztellenőrzését az idősorozat-adatokon
  + Új funkciók hozzáadva az idősor késésének konfiguráláshoz
  + Új funkciók hozzáadva a görgetőablak-összesítési funkciók támogatásához
  + Új ünnepi észlelés és deaturizáló, ha az országkód meg van határozva a kísérlet beállításaiban

+ Azure Databricks
  + Engedélyezett idősor-előrejelzés és modellel magyarázó/értelmezhetőségi képesség
  + Most már megszakíthatja és folytathatja az automatizált gépi tanulási kísérleteket
  + Többmagos feldolgozás támogatása hozzáadva

### <a name="mlops"></a>MLOps
+ **Helyi üzembehely & tárolók pontozásának hibakeresése**<br/> Most már helyileg üzembe helyezhet egy ML-modellt, és gyorsan iterálhatja a pontozófájlt és a függőségeket, hogy a várt módon viselkedjenek.

+ **Az InferenceConfig & Model.deploy()**<br/> A modell üzembe helyezése mostantól támogatja a forrásmappa megadását egy bejegyzési szkriptel, ugyanúgy, mint a RunConfig.  Emellett a modell üzembe helyezése egyetlen paranccsal is leegyszerűsödött.

+ **Git-referenciakövetés**<br/> Az ügyfelek már egy ideje alapvető Git-integrációs képességeket kérnek, mivel ez segít a teljes audit auditálás fenntartásában. Megvalósítottuk a Githez kapcsolódó metaadatok (adattár, véglegesítés, tiszta állapot) nyomon követését az Azure ML-ben a főbb entitások között. Ezt az információt az SDK és a CLI automatikusan gyűjti.

+ **Modellprofil-készítés & érvényesítési szolgáltatás**<br/> Az ügyfelek gyakran panaszkodnak a következtetési szolgáltatásukhoz társított számítási kapacitás megfelelő méretének nehézsége miatt. A modellprofil-készítési szolgáltatásunk segítségével az ügyfél mintabeviteleket nyújthat, és 16 különböző CPU-/memóriakonfiguráción keresztül profilozunk az üzembe helyezés optimális méretezésének meghatározásához.

+ **Saját alapként hozott rendszerkép deduktenciaként**<br/> Egy másik gyakori probléma a kísérletezésből a következtetési RE-megosztási függőségekbe való áthelyezés nehézsége volt. Az új alapként használható rendszerképmegosztási funkcióval a dedigencia érdekében újra felhasználhatja a kísérletezési alapként használt rendszerképeket, függőségeket és minden más rendszerképet. Ez felgyorsítja az üzemelő példányokat, és csökkenti a belső és a külső hurok közötti hézagokat.

+ **Továbbfejlesztett Swagger-séma-generáció élmény**<br/> A korábbi Swagger-generáció metódusunk hibaeseteket észlelt, és nem automatizálható. A Swagger-sémák bármilyen Python-függvényből, dekorátorokon keresztül történő generálásának új módja van. Nyílt forráskódú ez a kód, és a séma-generálási protokollunk nincs az Azure ML platformhoz csatolva.

+ **Azure ML CLI általánosan elérhető (GA)**<br/> A modellek mostantól egyetlen CLI-paranccsal helyezhetők üzembe. Általános ügyfél-visszajelzéseket kaptunk arról, hogy senki nem helyez üzembe gépi tanulási modellt egy Jupyter-notebookból. A [**CLI referenciadokumentációja**](./reference-azure-machine-learning-cli.md) frissítve lett.


## <a name="2019-04-22"></a>2019-04-22

Azure Machine Learning SDK for Python 1.0.30-as verziója jelent meg.

A bevezette egy közzétett folyamat új verziójának hozzáadását, miközben [`PipelineEndpoint`](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipeline_endpoint.pipelineendpoint) ugyanazt a végpontot tartja fenn.

## <a name="2019-04-15"></a>2019-04-15

### <a name="azure-portal"></a>Azure Portal
  + Most már újra lehet futtatni egy meglévő szkriptfuttatást egy meglévő távoli számítási fürtön.
  + Most már futtathat egy közzétett folyamatot új paraméterekkel a Folyamatok lapon.
  + A futtatás részletei mostantól támogatják az új Pillanatképfájl-megjelenítőt. Megtekintheti a könyvtár pillanatképét, amikor elküldött egy adott futtatásot. A futtatás elkezdéhez elküldött jegyzetfüzetet is letöltheti.
  + Most már megszakíthatja a fölérendelt futtatásokat a Azure Portal.

## <a name="2019-04-08"></a>2019-04-08

### <a name="azure-machine-learning-sdk-for-python-v1023"></a>Azure Machine Learning SDK for Python 1.0.23

+ **Új funkciók**
  + A Azure Machine Learning SDK mostantól támogatja a Python 3.7-es verzióját.
  + Azure Machine Learning DNN-becslők mostantól beépített többverziós támogatást biztosítanak. Például a estimator mostantól elfogad egy paramétert, és a felhasználók megadhatják az `TensorFlow` `framework_version` 1.10-es vagy az 1.12-es verziót. Az aktuális SDK-kiadás által támogatott verziók listájáért hívja meg a következőt: a `get_supported_versions()` kívánt keretrendszerosztályon (például `TensorFlow.get_supported_versions()` ).
  A legújabb SDK-kiadás által támogatott verziók listáját a [DNN Estimator dokumentációjában találhatja meg.](/python/api/azureml-train-core/azureml.train.dnn)

## <a name="2019-03-25"></a>2019-03-25

### <a name="azure-machine-learning-sdk-for-python-v1021"></a>Azure Machine Learning SDK for Python 1.0.21

+ **Új funkciók**
  + A *azureml.core.Run.create_children* módszer lehetővé teszi több gyermekfutat alacsony késésű létrehozását egyetlen hívással.

## <a name="2019-03-11"></a>2019-03-11

### <a name="azure-machine-learning-sdk-for-python-v1018"></a>Azure Machine Learning SDK for Python 1.0.18

 + **Módosítások**
   + Az azureml-tensorboard csomag lecseréli az azureml-contrib-tensorboard helyére.
   + Ebben a kiadásban felhasználói fiókot állíthat be a felügyelt számítási fürtön (amlcompute) annak létrehozásakor. Ezt úgy teheti meg, ha ezeket a tulajdonságokat a kiépítési konfigurációban ad át. További részleteket az [SDK referenciadokumentációjában talál.](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#provisioning-configuration-vm-size-----vm-priority--dedicated---min-nodes-0--max-nodes-none--idle-seconds-before-scaledown-none--admin-username-none--admin-user-password-none--admin-user-ssh-key-none--vnet-resourcegroup-name-none--vnet-name-none--subnet-name-none--tags-none--description-none--remote-login-port-public-access--notspecified--)

### <a name="azure-machine-learning-data-prep-sdk-v1017"></a>Azure Machine Learning Data Prep SDK v1.0.17

+ **Új funkciók**
  + Mostantól két numerikus oszlop hozzáadását is támogatja egy keletkező oszlop kifejezésnyelv használatával való létrehozásához.

+ **Hibajavítások és fejlesztések**
  + Továbbfejlesztett dokumentáció és paraméterellenőrzés a random_split.

## <a name="2019-02-27"></a>2019-02-27

### <a name="azure-machine-learning-data-prep-sdk-v1016"></a>Azure Machine Learning Data Prep SDK v1.0.16

+ **Hibajavítás**
  + Ki lett javítva egy api-módosítás által okozott szolgáltatásnév-hitelesítési probléma.

## <a name="2019-02-25"></a>2019-02-25

### <a name="azure-machine-learning-sdk-for-python-v1017"></a>Azure Machine Learning SDK for Python 1.0.17

+ **Új funkciók**
  + Azure Machine Learning már első osztályú támogatást nyújt a népszerű DNN-keretrendszer Chainer számára. Az [`Chainer`](/python/api/azureml-train-core/azureml.train.dnn.chainer) osztály felhasználói könnyen betaníthatók és üzembe helyezhetők Chainer-modellek.
    + Ismerje meg, hogyan [futtathat elosztott betanításokat a ChainerMN-sel](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks/chainer/distributed-chainer/distributed-chainer.ipynb)
    + Megtudhatja, hogyan [futtathat hiperparaméter-finomhangolást a Chainerrel a HyperDrive használatával](https://github.com/Azure/MachineLearningNotebooks/blob/b881f78e4658b4e102a72b78dbd2129c24506980/how-to-use-azureml/ml-frameworks/chainer/deployment/train-hyperparameter-tune-deploy-with-chainer/train-hyperparameter-tune-deploy-with-chainer.ipynb)
  + Azure Machine Learning Pipelines új képessége, hogy az adattár módosításai alapján elindítsa a folyamat futását. A [folyamat-ütemezési jegyzetfüzet](https://aka.ms/pl-schedule) frissül, hogy bemutassa ezt a funkciót.

+ **Hibajavítások és fejlesztések**
  + Az Azure Machine Learning-folyamatokban már támogatott a source_directory_data_store tulajdonság kívánt adattárra (például blobtárolóra) való beállítása a [PythonScriptStep](/python/api/azureml-pipeline-steps/azureml.pipeline.steps.python_script_step.pythonscriptstep)számára biztosított [RunConfigurations](/python/api/azureml-core/azureml.core.runconfig.runconfiguration) esetében. Alapértelmezés szerint a Steps (Lépések) az Azure File Store-t használja háttéradattárként, amely szabályozási problémákat okozhat, ha egyszerre sok lépést hajt végre.

### <a name="azure-portal"></a>Azure Portal

+ **Új funkciók**
  + Új húzással való táblázatszerkesztő-felhasználói élmény jelentésekhez. A felhasználók áthúznak egy oszlopot a területről arra a táblázatterületre, ahol a tábla előnézete megjelenik. Az oszlopok átrendeződnek.
  + Új naplók fájlmegjelenítője
  + Kísérletek futtatásaira, számítási feladatokra, modellekre, rendszerképekre és üzembe helyezésre mutató hivatkozások a tevékenységek lapon

## <a name="next-steps"></a>Következő lépések

Olvassa el az [Azure Machine Learning](overview-what-is-azure-ml.md) áttekintését.
