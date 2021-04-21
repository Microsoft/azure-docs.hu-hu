---
title: Hiperparaméter modell finomhangolása
titleSuffix: Azure Machine Learning
description: Automatizálhatja a hiperparaméterek finomhangolását mély tanulási és gépi tanulási modellekhez a Azure Machine Learning.
ms.author: anumamah
author: Aniththa
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 02/26/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: ec01561c5c44c850b32187629552b1bdb99537e7
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107819177"
---
# <a name="hyperparameter-tuning-a-model-with-azure-machine-learning"></a>Hiperparaméter modell finomhangolása Azure Machine Learning

Automatizálhatja a hiperparaméterek hatékony finomhangolását Azure Machine Learning [HyperDrive-csomag használatával.](/python/api/azureml-train-core/azureml.train.hyperdrive) Útmutató a hiperparaméterek a Azure Machine Learning SDK-val való [hangolatához szükséges lépések éhez:](/python/api/overview/azure/ml/)

1. A paraméter keresési területének meghatározása
1. Az optimalizálni kívánt elsődleges metrika megadása  
1. Korai lefutási szabályzat megadása alacsony teljesítményű futtatás esetén
1. Erőforrások létrehozása és hozzárendelése
1. Kísérlet indítása a megadott konfigurációval
1. A betanítás futtatásának vizualizációja
1. A modellnek leginkább megfelelő konfiguráció kiválasztása

## <a name="what-is-hyperparameter-tuning"></a>Mi a hiperparaméterek finomhangolása?

**A hiperparaméterek** módosítható paraméterek, amelyek segítségével vezérelhető a modell betanítási folyamata. Neurális hálózatok esetén például az egyes rétegek rejtett rétegeinek és csomópontjainak számát kell eldöntenie. A modell teljesítménye erősen függ a hiperparamétertől.

 **A hiperparaméterek finomhangolása**( más néven **hiperparaméter-optimalizálás)** a legjobb teljesítményt eredményező hiperparaméterek konfigurációjának megkeresése. A folyamat általában számításokkal költséges és manuális.

Azure Machine Learning lehetővé teszi a hiperparaméterek finomhangolásának automatizálását és a kísérletek párhuzamos futtatását a hiperparaméterek hatékony optimalizálása érdekében.


## <a name="define-the-search-space"></a>A keresési terület meghatározása

A hiperparaméterek hangolása az egyes hiperparaméterek számára meghatározott értéktartomány vizsgálatával.

A hiperparaméterek diszkrétek vagy folyamatosak is, és az értékek eloszlását egy paraméterkifejezés [írja le.](/python/api/azureml-train-core/azureml.train.hyperdrive.parameter_expressions)

### <a name="discrete-hyperparameters"></a>Különálló hiperparaméterek

A különálló hiperparaméterek a különálló értékek `choice` között vannak megadva. `choice` A következő lehet:

* egy vagy több vesszővel elválasztott érték
* egy `range` objektum
* bármely tetszőleges `list` objektum


```Python
    {
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

Ebben az esetben a `batch_size` [16, 32, 64, 128] értékek egyike veszi fel az `number_of_hidden_layers` [1, 2, 3, 4] értékeket.

A következő speciális különálló hiperparaméterek is megszabadhatóak egy elosztás használatával:

* `quniform(low, high, q)` – Olyan értéket ad vissza, mint a round(uniform(low, high) / q) * q
* `qloguniform(low, high, q)` – Olyan értéket ad vissza, mint a round(exp(uniform(low, high)) / q) * q
* `qnormal(mu, sigma, q)` – Olyan értéket ad vissza, mint a round(normal(mu, sigma) / q) * q
* `qlognormal(mu, sigma, q)` – Olyan értéket ad vissza, mint a round(exp(normal(mu, sigma)) / q) * q

### <a name="continuous-hyperparameters"></a>Folyamatos hiperparaméterek 

A folyamatos hiperparaméterek az értékek folyamatos tartományának eloszlásaként vannak megadva:

* `uniform(low, high)` – Egy alacsony és magas értékek közötti egységesen elosztott értéket ad vissza
* `loguniform(low, high)` – Az exp(uniform(low, high)) alapján rajzolt értéket ad vissza, hogy a visszaadott érték logaritmusa egységesen legyen elosztva
* `normal(mu, sigma)` – Egy valós értéket ad vissza, amely általában az mu és a szórási szigma átlagos eloszlásával van elosztva
* `lognormal(mu, sigma)` – Az exp(normal(mu, sigma)) alapján kirajzolt értéket ad vissza, hogy a visszaadott érték logaritmusa a szokásos módon legyen elosztva

Példa paramétertér-definícióra:

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

Ez a kód egy keresési területet határoz meg két paraméter használatával: `learning_rate` és `keep_probability` . `learning_rate` A normál eloszlása 10-es, a szórása pedig 3. `keep_probability` A egységes eloszlása legalább 0,05, a maximális értéke pedig 0,1.

### <a name="sampling-the-hyperparameter-space"></a>Mintavételezés a hiperparaméterek között

Adja meg a hiperparaméter-térben használni kívánt paraméter-mintavételezési módszert. Azure Machine Learning a következő metódusokat támogatja:

* Véletlenszerű mintavételezés
* Rácsos mintavételezés
* Bayes-féle mintavételezés

#### <a name="random-sampling"></a>Véletlenszerű mintavételezés

[A véletlenszerű mintavételezés](/python/api/azureml-train-core/azureml.train.hyperdrive.randomparametersampling) támogatja a különálló és folyamatos hiperparamétereket. Támogatja az alacsony teljesítményű futtatás korai megszakítását. Egyes felhasználók egy kezdeti keresést véletlenszerű mintavételezéssel, majd a keresési terület finomításával javítják az eredményeket.

Véletlenszerű mintavételezés esetén a hiperparaméter-értékek véletlenszerűen vannak kiválasztva a megadott keresési területből. 

```Python
from azureml.train.hyperdrive import RandomParameterSampling
from azureml.train.hyperdrive import normal, uniform, choice
param_sampling = RandomParameterSampling( {
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

#### <a name="grid-sampling"></a>Rácsos mintavételezés

[A rácsos mintavételezés](/python/api/azureml-train-core/azureml.train.hyperdrive.gridparametersampling) támogatja a különálló hiperparamétereket. Használjon rácsos mintavételezést, ha a költségvetésben teljes körű keresést tud végezni a keresési térben. Támogatja az alacsony teljesítményű futtatás korai megszakítását.

A rácsos mintavételezés egyszerű rácskeresést tesz lehetővé az összes lehetséges értéken. A rácsos mintavételezés csak `choice` hiperparaméterekkel használható. Az alábbi tér például hat mintával rendelkezik:

```Python
from azureml.train.hyperdrive import GridParameterSampling
from azureml.train.hyperdrive import choice
param_sampling = GridParameterSampling( {
        "num_hidden_layers": choice(1, 2, 3),
        "batch_size": choice(16, 32)
    }
)
```

#### <a name="bayesian-sampling"></a>Bayes-féle mintavételezés

[A Bayes-mintavételezés](/python/api/azureml-train-core/azureml.train.hyperdrive.bayesianparametersampling) a Bayes-optimalizálási algoritmuson alapul. Mintákat választ az előző minták alapján, hogy az új minták javítják az elsődleges metrikát.

A Bayes-mintavételezés akkor ajánlott, ha elegendő költségkeret áll rendelkezésre a hiperparaméter-tér vizsgálathoz. A legjobb eredmény érdekében javasoljuk, hogy legfeljebb 20-szor több futtatás legyen, mint a hangolt hiperparaméterek száma. 

Az egyidejű futtatások száma hatással van a finomhangolási folyamat hatékonyságára. Az egyidejű futtatás kisebb száma jobb mintavételezés-konvergenciához vezethet, mivel a kisebb párhuzamosság növeli a korábban befejezett futtatásokat kihasználó futtatásokat.

A Bayes-mintavételezés csak a `choice` , és eloszlásokat támogatja a keresési `uniform` `quniform` térben.

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
from azureml.train.hyperdrive import uniform, choice
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```



## <a name="specify-primary-metric"></a><a name="specify-primary-metric-to-optimize"></a> Elsődleges metrika megadása

Adja meg [azt az elsődleges metrikát,](/python/api/azureml-train-core/azureml.train.hyperdrive.primarymetricgoal) amely optimalizálni szeretné a hiperparaméterek finomhangolását. A rendszer minden betanítás futtatáskor kiértékeli az elsődleges metrikát. A korai megszakítási szabályzat az elsődleges metrikát használja a kis teljesítményű futtatás azonosításához.

Adja meg a következő attribútumokat az elsődleges metrika számára:

* `primary_metric_name`: Az elsődleges metrika nevének pontosan meg kell egyeznie a betanító szkript által naplózott metrika nevével
* `primary_metric_goal`: Vagy lehet, és meghatározza, hogy az elsődleges metrika maximalizálva vagy minimalizálva lesz-e a `PrimaryMetricGoal.MAXIMIZE` `PrimaryMetricGoal.MINIMIZE` futtatás kiértékelése során. 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

Ez a minta maximalizálja a "pontosságot".

### <a name="log-metrics-for-hyperparameter-tuning"></a><a name="log-metrics-for-hyperparameter-tuning"></a>Naplómetrikák hiperparaméter-finomhangoláshoz

A modell betanító **szkriptje** naplózhatja az elsődleges metrikát a modell betanítás során, hogy a HyperDrive hozzáférjen a hiperparaméterek finomhangolathoz.

Naplózd az elsődleges metrikát a betanító szkriptben a következő minta kódrészlet használatával:

```Python
from azureml.core.run import Run
run_logger = Run.get_context()
run_logger.log("accuracy", float(val_accuracy))
```

A betanítás szkript kiszámítja a és a naplókat elsődleges `val_accuracy` metrikaként "pontosságként". A metrikát minden naplózáskor a hiperparaméter-hangolási szolgáltatás is megkapta. Önnek kell meghatároznia a jelentéskészítés gyakoriságát.

A modellbeképező futtatás értékeinek naplózási értékeivel kapcsolatos további információkért lásd: Naplózás engedélyezése [az Azure ML betanításfuttaiban.](how-to-log-view-metrics.md)

## <a name="specify-early-termination-policy"></a><a name="early-termination"></a> Korai felmondási szabályzat megadása

A rosszul teljesítő futtatás automatikus befejezése korai megszakítási szabályzatokkal. A korai megszakítás javítja a számítási hatékonyságot.

A következő paramétereket konfigurálhatja, amelyek a szabályzatok alkalmazását szabályják:

* `evaluation_interval`: a szabályzat alkalmazásának gyakorisága. Minden alkalommal, amikor a betanító szkript egy intervallumként naplózza az elsődleges metrikákat. Az `evaluation_interval` 1-es elem minden alkalommal alkalmazza a szabályzatot, amikor a betanító szkript jelenti az elsődleges metrikát. A `evaluation_interval` 2-esből egy minden más alkalommal alkalmazza a szabályzatot. Ha nincs megadva, `evaluation_interval` a alapértelmezett beállítása 1.
* `delay_evaluation`: adott számú időközönként késlelteti az első szabályzatértékelést. Ez egy nem kötelező paraméter, amely elkerüli a betanítási futtatás idő előtti megszakítását azáltal, hogy az összes konfigurációt a lehető legkevesebb időközönként futtatja. Ha meg van adva, a szabályzat a evaluation_interval minden olyan többszörösét alkalmazza, amely nagyobb vagy egyenlő, mint delay_evaluation.

Azure Machine Learning a következő korai felmondási szabályzatokat támogatja:
* [Házirend](#bandit-policy)
* [Leállítási szabályzat mediánja](#median-stopping-policy)
* [Csonkítási szabályzat](#truncation-selection-policy)
* [Nincs le megszakítási szabályzat](#no-termination-policy-default)


### <a name="bandit-policy"></a>Házirend

[A szabályzat](/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy#definition) alapja a Slack-tényező/Slack mennyisége és a kiértékelési időköz. A rendszer akkor ér véget, ha az elsődleges metrika nem a legeredményesebb futtatás megadott Slack-tényezőn/Slack-mennyiségen belül van.

> [!NOTE]
> A Bayes-mintavételezés nem támogatja a korai megszakítást. Bayes-mintavételezés esetén állítsa be a `early_termination_policy = None` halmazt.

Adja meg a következő konfigurációs paramétereket:

* `slack_factor` vagy : a legjobban teljesítő betanítás futtatása során engedélyezett `slack_amount` Slack. `slack_factor` – a megengedett Slacket adja meg arányként. `slack_amount` az arány helyett abszolút összegként határozza meg a megengedett Slacket.

    Tegyük fel például, hogy egy 10.10-es időközzel alkalmazott Házirend van alkalmazva. Tegyük fel, hogy a 10. intervallumban a legjobban teljesítő futtatás jelentett elsődleges metrika 0,8, az elsődleges metrika maximalizálása érdekében. Ha a szabályzat 0,2-es értékeket ad meg, akkor a rendszer megszakít minden olyan betanításfuttatást, amelynek 10.66-os időköznél kisebb a legjobb metrika `slack_factor` (0,8/(1+ `slack_factor` )).
* `evaluation_interval`: (nem kötelező) a szabályzat alkalmazásának gyakorisága
* `delay_evaluation`: (nem kötelező) adott számú időközönként késlelteti az első szabályzatértékelést


```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

Ebben a példában a korai felmondási szabályzatot a rendszer a metrikák jelentésének minden intervallumában alkalmazza, az 5. kiértékelési időköztől kezdve. Minden olyan futtatás megszakad, amelynek a legjobb metrika kisebb, mint (1/(1+0.1) vagy a legjobbnak megfelelő futtatás 91%-a.

### <a name="median-stopping-policy"></a>Leállítási szabályzat mediánja

[A mediánleállítás](/python/api/azureml-train-core/azureml.train.hyperdrive.medianstoppingpolicy) egy korai leállítási szabályzat, amely a futtatás által jelentett elsődleges metrikák futó átlagán alapul. Ez a szabályzat kiszámítja a futtatás átlagát az összes betanítás futtatásakor, és leállítja az olyan futtatásokat, amelyek elsődleges metrikaértéke rosszabb, mint az átlagok mediánja.

Ez a szabályzat a következő konfigurációs paramétereket veszi fel:
* `evaluation_interval`: a szabályzat alkalmazásának gyakorisága (opcionális paraméter).
* `delay_evaluation`: adott számú időközönként késlelteti az első szabályzatértékelést (opcionális paraméter).


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

Ebben a példában a korai felmondási szabályzatot az 5. kiértékelési időközzel kezdődően minden időközönként alkalmazza a rendszer. A futtatás leáll az 5. intervallumban, ha a legjobb elsődleges metrika rosszabb, mint a futtatás átlagának mediánja 1:5-ös intervallumokban az összes betanítás futtatásakor.

### <a name="truncation-selection-policy"></a>Csonkítási szabályzat

[A csonkolási](/python/api/azureml-train-core/azureml.train.hyperdrive.truncationselectionpolicy) kijelölés megszakítja a legalacsonyabban teljesítő futtatás százalékos arányát az egyes kiértékelési időközökben. A futtatásokat az elsődleges metrika alapján hasonlítja össze a rendszer. 

Ez a szabályzat a következő konfigurációs paramétereket veszi fel:

* `truncation_percentage`: a legalacsonyabb szinten teljesítő futtatás százalékos aránya, amely minden kiértékelési időszakban leáll. Egy 1 és 99 közötti egész szám.
* `evaluation_interval`: (nem kötelező) a szabályzat alkalmazásának gyakorisága
* `delay_evaluation`: (nem kötelező) adott számú időközönként késlelteti az első szabályzatértékelést


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

Ebben a példában a korai felmondási szabályzatot az 5. kiértékelési időközzel kezdődően minden időközönként alkalmazza a rendszer. A futtatás az 5. időközzel leáll, ha az 5. intervallum teljesítménye az összes futtatás 20%-ának legalacsonyabb 20%-ában van az 5. intervallumon belül.

### <a name="no-termination-policy-default"></a>Nincs leállítva szabályzat (alapértelmezés)

Ha nincs megadva szabályzat, a hiperparaméter-hangolási szolgáltatás minden betanításfuttatást a befejezésig futtat.

```Python
policy=None
```

### <a name="picking-an-early-termination-policy"></a>Korai felmondási szabályzat felvétele

* Az ígéretes feladatok megszakítása nélkül megtakarítást adó, óvatos házirend esetén érdemes megfontolni egy 1-es és 5-ös leállítási szabályzat `evaluation_interval` `delay_evaluation` mediánját. Ezek a biztonsági beállítások, amelyek körülbelül 25–35%-os megtakarítást biztosítanak az elsődleges metrikák elvesztése nélkül (a kiértékelési adatok alapján).
* Az agresszívabb megtakarítás érdekében kisebb, megengedő Slack-szabályzattal vagy csonkítási szabályzattal, nagyobb csonkítási százalékkal használhatja.

## <a name="create-and-assign-resources"></a>Erőforrások létrehozása és hozzárendelése

Az erőforrás-költségvetés szabályozása a betanításfuttaik maximális számának megadásával.

* `max_total_runs`: A betanításfuttatás maximális száma. 1 és 1000 közötti egész számnak kell lennie.
* `max_duration_minutes`: (nem kötelező) A hiperparaméter-hangolási kísérlet maximális időtartama (percben). Az ezt az időtartamot követően futtatott futtatásokat a program megszakítja.

>[!NOTE] 
>Ha a és a is meg van adva, a `max_total_runs` hiperparaméter-finomhangolási kísérlet akkor áll le, amikor a két küszöbérték közül az `max_duration_minutes` elsőt eléri.

Emellett adja meg a hiperparaméter-hangolási keresés során egyidejűleg futtatott betanításfuttassanak maximális számát.

* `max_concurrent_runs`: (nem kötelező) Az egyidejűleg futtatható futtatások maximális száma. Ha nincs megadva, minden futtatás párhuzamosan indul el. Ha meg van adva, a egész számnak 1 és 100 közé kell esnie.

>[!NOTE] 
>Az egyidejű futtatások száma korlátozva van a megadott számítási célban elérhető erőforrásokon. Győződjön meg arról, hogy a számítási cél rendelkezik a kívánt egyidejűséghez rendelkezésre álló erőforrásokkal.

```Python
max_total_runs=20,
max_concurrent_runs=4
```

Ez a kód úgy konfigurálja a hiperparaméter-hangolási kísérletet, hogy egyszerre legfeljebb 20 futtatást használjon négy konfiguráció futtatásával.

## <a name="configure-hyperparameter-tuning-experiment"></a>Hiperparaméter-finomhangolási kísérlet konfigurálása

A [hiperparaméter-finomhangolási kísérlet konfiguráláshoz](/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverunconfig) adja meg a következőket:
* A meghatározott hiperparaméter-keresési tér
* A korai felmondási szabályzat
* Az elsődleges metrika
* Erőforrás-elosztási beállítások
* ScriptRunConfig `script_run_config`

A ScriptRunConfig az a betanító szkript, amely a mintavételt tartalmazó hiperparaméterekkel fog futni. Feladatonként (egy vagy több csomópontos) és a használni szükséges számítási célt határozza meg.

> [!NOTE]
>A-ban használt számítási célnak elegendő erőforrással kell lennie az egyidejűségi `script_run_config` szint eléréséhez. További információ a ScriptRunConfig parancsról: [Betanításfuttasok konfigurálása.](how-to-set-up-training-targets.md)

Konfigurálja a hiperparaméterek finomhangolási kísérletét:

```Python
from azureml.train.hyperdrive import HyperDriveConfig
from azureml.train.hyperdrive import RandomParameterSampling, BanditPolicy, uniform, PrimaryMetricGoal

param_sampling = RandomParameterSampling( {
        'learning_rate': uniform(0.0005, 0.005),
        'momentum': uniform(0.9, 0.99)
    }
)

early_termination_policy = BanditPolicy(slack_factor=0.15, evaluation_interval=1, delay_evaluation=10)

hd_config = HyperDriveConfig(run_config=script_run_config,
                             hyperparameter_sampling=param_sampling,
                             policy=early_termination_policy,
                             primary_metric_name="accuracy",
                             primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                             max_total_runs=100,
                             max_concurrent_runs=4)
```

A `HyperDriveConfig` beállítja a paraméternek átadott `ScriptRunConfig script_run_config` paramétereket. A `script_run_config` paraméter továbbítja a paramétereket a betanító szkriptnek. A fenti kódrészlet a Betanítás, hiperparaméter hangolása és üzembe helyezése PyTorch alkalmazással [mintajegyzetfüzetből áll.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch) Ebben a példában a `learning_rate` és `momentum` a paramétert hangoljuk. A futtatás korai leállítását egy határozza meg, amely leállít egy olyan futtatás, amelynek elsődleges metrika a -n kívül esik `BanditPolicy` `slack_factor` (lásd a Következőt: < a2/2012; [](/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy) 

A mintából származó alábbi kód bemutatja, hogyan érkezik meg, elemezve és továbbküldve a betanító szkript függvénye a hangolt `fine_tune_model` értékeket:

```python
# from pytorch_train.py
def main():
    print("Torch version:", torch.__version__)

    # get command-line arguments
    parser = argparse.ArgumentParser()
    parser.add_argument('--num_epochs', type=int, default=25,
                        help='number of epochs to train')
    parser.add_argument('--output_dir', type=str, help='output directory')
    parser.add_argument('--learning_rate', type=float,
                        default=0.001, help='learning rate')
    parser.add_argument('--momentum', type=float, default=0.9, help='momentum')
    args = parser.parse_args()

    data_dir = download_data()
    print("data directory is: " + data_dir)
    model = fine_tune_model(args.num_epochs, data_dir,
                            args.learning_rate, args.momentum)
    os.makedirs(args.output_dir, exist_ok=True)
    torch.save(model, os.path.join(args.output_dir, 'model.pt'))
```

> [!Important]
> Minden hiperparaméter futtatása az nulláról indítja újra a betanítást, beleértve a modell és az összes _adatbetöltő újraépítését._ Ez a költség minimálisra csökkenthető egy Azure Machine Learning folyamattal vagy manuális folyamattal, hogy a betanítási futtatás előtt a lehető legnagyobb adat-előkészítést el tudja látni. 

## <a name="submit-hyperparameter-tuning-experiment"></a>Hiperparaméter-finomhangolási kísérlet elküldése

Miután meghatározta a hiperparaméter hangolási konfigurációját, küldje [el a kísérletet:](/python/api/azureml-core/azureml.core.experiment%28class%29#submit-config--tags-none----kwargs-)

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hd_config)
```

## <a name="warm-start-hyperparameter-tuning-optional"></a>Meleg indítású hiperparaméterek finomhangolása (nem kötelező)

A modellhez leginkább megfelelő hiperparaméter-értékek megtalálása iteratív folyamat lehet. A hiperparaméterek finomhangolásának felgyorsítása érdekében újból felhasználhatja az öt korábbi futtatásból származó ismereteket.

A meleg indítás kezelése a mintavételezési módszertől függően eltérő:
- **Bayes-mintavételezés:** Az előző futtatás kísérlete az új minták kiválasztásához és az elsődleges metrika javításához használt korábbi ismeretek alapján történik.
- **Véletlenszerű mintavételezés** **vagy rácsos mintavételezés:** A korai lefutás a korábbi futtatásokkal kapcsolatos ismereteket felhasználva határozza meg a rosszul teljesítő futtatásokat. 

Adja meg azon szülőfutatok listáját, amelyekről a kezdéshez szeretne kezdeni.

```Python
from azureml.train.hyperdrive import HyperDriveRun

warmstart_parent_1 = HyperDriveRun(experiment, "warmstart_parent_run_ID_1")
warmstart_parent_2 = HyperDriveRun(experiment, "warmstart_parent_run_ID_2")
warmstart_parents_to_resume_from = [warmstart_parent_1, warmstart_parent_2]
```

Ha megszakítják a hiperparaméter-hangolási kísérletet, az utolsó ellenőrzőponttól folytathatja a betanítás futtatásokat. A betanító szkriptnek azonban kezelnie kell az ellenőrzőpont-logikát.

A betanítási futtatásnak ugyanazt a hiperparaméter-konfigurációt kell használnia, és csatlakoztatnia kell a kimeneti mappákat. A betanító szkriptnek el kell fogadnia a argumentumot, amely tartalmazza azokat az ellenőrzőpont- vagy modellfájlokat, amelyekből folytatni `resume-from` tudja a betanítás futtatását. Az egyéni betanításfutokat a következő kódrészlet használatával folytathatja:

```Python
from azureml.core.run import Run

resume_child_run_1 = Run(experiment, "resume_child_run_ID_1")
resume_child_run_2 = Run(experiment, "resume_child_run_ID_2")
child_runs_to_resume = [resume_child_run_1, resume_child_run_2]
```

A hiperparaméter-finomhangolási kísérletet konfigurálhatja úgy, hogy egy korábbi kísérletből induljon ki, vagy folytatja az egyéni betanításfutokat a választható paraméterekkel és a `resume_from` `resume_child_runs` konfigurációval:

```Python
from azureml.train.hyperdrive import HyperDriveConfig

hd_config = HyperDriveConfig(run_config=script_run_config,
                             hyperparameter_sampling=param_sampling,
                             policy=early_termination_policy,
                             resume_from=warmstart_parents_to_resume_from,
                             resume_child_runs=child_runs_to_resume,
                             primary_metric_name="accuracy",
                             primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                             max_total_runs=100,
                             max_concurrent_runs=4)
```

## <a name="visualize-hyperparameter-tuning-runs"></a>Hiperparaméter-finomhangolási futtatás vizualizációja

A hiperparaméterek finomhangolási futtatásokat a Azure Machine Learning stúdió, vagy használhat jegyzetfüzet-vezérlőt.

### <a name="studio"></a>Studio

Az összes hiperparaméter-finomhangolási futtatás vizualizációja a [következő](https://ml.azure.com)Azure Machine Learning stúdió. A kísérletek portálon való megtekintésével kapcsolatos további információkért lásd: Futtatásrekordok megtekintése a [studióban.](how-to-log-view-metrics.md#view-the-experiment-in-the-web-portal)

- **Metrikadiagram:** Ez a vizualizáció a hiperparaméterek finomhangolásának időtartama alatt az egyes hipermeghajtó-gyermekfuttassakhoz naplózott metrikákat követi nyomon. Minden sor egy gyermekfuttetést képvisel, és minden pont az elsődleges metrika értékét méri a futásidő adott iterációja során.  

    :::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-metrics.png" alt-text="Hiperparaméterek finomhangolási metrikadiagramja":::

- **Párhuzamos koordináták** diagramja: Ez a vizualizáció az elsődleges metrikateljesítmény és az egyes hiperparaméter-értékek korrelációját mutatja be. A diagram interaktív a tengelyek mozgásával (kattintással és húzással a tengely címkéje alapján), valamint az értékek egyetlen tengelyre való kiemelésével (kattintással és húzással függőlegesen egy tengely mentén a kívánt értékek tartományának kiemelése érdekében).

    :::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-parallel-coordinates.png" alt-text="A hiperparaméterek párhuzamos koordinátái finomhangolásának diagramja":::

- **Kétdimenziós pontdiagram:** Ez a vizualizáció két különálló hiperparaméter korrelációját mutatja a hozzájuk tartozó elsődleges metrikaértékkel együtt.

    :::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-2-dimensional-scatter.png" alt-text="A hyparameter kétdimenziós pontdiagram finomhangolása":::

- **3 dimenziós pontdiagram:** Ez a vizualizáció ugyanaz, mint a 2D, de lehetővé teszi három hiperparaméteres dimenzió korrelációját az elsődleges metrikaértékkel. Kattintással és húzással is újratűrheti a diagramot a különböző korrelációk 3D-térben való megtekintéséhez.

    :::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-3-dimensional-scatter.png" alt-text="Hyparameter tuning 3 dimenziós pontdiagram":::

### <a name="notebook-widget"></a>Jegyzetfüzet widget

A [Notebook vezérlővel](/python/api/azureml-widgets/azureml.widgets.rundetails) vizualizálhatja a betanítás futásának előrehaladását. Az alábbi kódrészlet az összes hiperparaméter-finomhangolási futtatás egy helyen való megjelenítését ábrázolja egy Jupyter notebookban:

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

Ez a kód egy táblázatot jelenít meg, amely az egyes hiperparaméter-konfigurációk betanítási futtatási adatait tartalmazza.

:::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-table.png" alt-text="Hiperparaméterek finomhangolási táblázata":::

Az egyes futtatásokat a betanítás előrehaladása során is ábrázolhatja.

## <a name="find-the-best-model"></a>A legjobb modell megkeresve

Miután az összes hiperparaméter-finomhangolási futtatás befejeződött, azonosítsa a legjobban teljesítő konfigurációs és hiperparaméter-értékeket:

```Python
best_run = hyperdrive_run.get_best_run_by_primary_metric()
best_run_metrics = best_run.get_metrics()
parameter_values = best_run.get_details()['runDefinition']['Arguments']

print('Best Run Id: ', best_run.id)
print('\n Accuracy:', best_run_metrics['accuracy'])
print('\n learning rate:',parameter_values[3])
print('\n keep probability:',parameter_values[5])
print('\n batch size:',parameter_values[7])
```

## <a name="sample-notebook"></a>Mintajegyzetfüzet

Tekintse meg a train-hyperparameter-* jegyzetfüzeteket ebben a mappában:
* [how-to-use-azureml/ml-frameworks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Következő lépések
* [Kísérlet nyomon követése](how-to-log-view-metrics.md)
* [Betanított modell üzembe helyezése](how-to-deploy-and-where.md)
