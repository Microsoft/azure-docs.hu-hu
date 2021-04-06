---
title: Modell hiperparaméter finomhangolása
titleSuffix: Azure Machine Learning
description: A Azure Machine Learning segítségével automatizálhatja a hiperparaméter finomhangolását a Deep learning és a gépi tanulási modellekhez.
ms.author: anumamah
author: Aniththa
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 02/26/2021
ms.topic: conceptual
ms.custom: how-to, devx-track-python, contperf-fy21q1
ms.openlocfilehash: 34adcf2218e29572ec9a86583addc7c021313085
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102519639"
---
# <a name="hyperparameter-tuning-a-model-with-azure-machine-learning"></a>Hiperparaméter a modell finomhangolása Azure Machine Learning

A hatékony hiperparaméter-hangolás automatizálása Azure Machine Learning [HyperDrive-csomag](/python/api/azureml-train-core/azureml.train.hyperdrive)használatával. Ismerje meg, hogyan végezheti el a hiperparaméterek beállítása hangolásához szükséges lépéseket a [Azure Machine learning SDK](/python/api/overview/azure/ml/)-val:

1. A paraméter keresési területének megadása
1. Az optimalizáláshoz használandó elsődleges metrika meghatározása  
1. Korai megszakítási szabályzat megadása alacsony teljesítményű futtatásokhoz
1. Erőforrások létrehozása és kiosztása
1. Kísérlet elindítása a megadott konfigurációval
1. A betanítási futtatások megjelenítése
1. Válassza ki a modellhez legmegfelelőbb konfigurációt

## <a name="what-is-hyperparameter-tuning"></a>Mi a hiperparaméter hangolás?

A **hiperparaméterek beállítása** olyan állítható paraméterek, amelyek lehetővé teszik a modell betanítási folyamatának szabályozását. A neurális hálózatokkal például eldöntheti, hogy a rejtett rétegek száma és az egyes rétegek csomópontjainak száma. A modell teljesítménye nagymértékben függ a hiperparaméterek beállítása.

 A **hiperparaméter finomhangolása**, más néven **hiperparaméter-optimalizálás**, az a folyamat, amely a legjobb teljesítményt eredményező hiperparaméterek beállítása konfigurációjának megkeresésére szolgál. A folyamat általában számítási költséges és manuális.

Azure Machine Learning lehetővé teszi, hogy automatizálja a hiperparaméter hangolását, és párhuzamosan futtasson kísérleteket a hiperparaméterek beállítása hatékony optimalizálása érdekében.


## <a name="define-the-search-space"></a>A keresési terület meghatározása

A hiperparaméterek beállítása hangolása az egyes hiperparaméter meghatározott értékek tartományának feltárásával.

A hiperparaméterek beállítása diszkrét vagy folytonos lehet, és egy [paraméter kifejezése](/python/api/azureml-train-core/azureml.train.hyperdrive.parameter_expressions)által leírt értékek eloszlása.

### <a name="discrete-hyperparameters"></a>Különálló hiperparaméterek beállítása

A diszkrét hiperparaméterek beállítása a `choice` különálló értékek között vannak megadva. `choice` a következőket teheti:

* egy vagy több vesszővel tagolt érték
* egy `range` objektum
* tetszőleges `list` objektum


```Python
    {
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

Ebben az esetben `batch_size` az egyik érték [16, 32, 64, 128], és `number_of_hidden_layers` az alábbi értékek egyikét veszi figyelembe: [1, 2, 3, 4].

A következő speciális diszkrét hiperparaméterek beállítása is megadható eloszlás használatával:

* `quniform(low, high, q)` -Olyan értéket ad vissza, mint a Round (egységes (alacsony, magas)/q) * q
* `qloguniform(low, high, q)` -Egy olyan értéket ad vissza, mint a Round (exp (egységes (alacsony, magas))/q
* `qnormal(mu, sigma, q)` -Olyan értéket ad vissza, mint a Round (normál (MU, Sigma)/q) * q
* `qlognormal(mu, sigma, q)` -Olyan értéket ad vissza, mint a Round (exp (normál (MU, Sigma))/q) * q

### <a name="continuous-hyperparameters"></a>Folyamatos hiperparaméterek beállítása 

A folyamatos hiperparaméterek beállítása eloszlása az értékek folytonos tartományában történik:

* `uniform(low, high)` -Az alacsony és a magas közötti egységesen elosztott értéket adja vissza
* `loguniform(low, high)` – Az exp (egységes (alacsony, magas)) alapján rajzolt értéket adja vissza, hogy a visszatérési érték logaritmusa egységesen legyen elosztva.
* `normal(mu, sigma)` -Egy valós értéket ad vissza, amelyet általában a Mean és a standard szórás Sigma
* `lognormal(mu, sigma)` – Az exp (normál (MU, Sigma)) szerint rajzolt értéket adja vissza, hogy a visszatérési érték logaritmusa szabályosan legyen elosztva.

Egy példa a paraméter területének meghatározására:

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

Ez a kód egy keresési helyet határoz meg két paraméterrel – `learning_rate` és `keep_probability` . `learning_rate` a normál eloszlás a 10-es értékkel, a szórás pedig 3. `keep_probability` egy egységes eloszlású, amelynek minimális értéke 0,05, maximális értéke pedig 0,1.

### <a name="sampling-the-hyperparameter-space"></a>A hiperparaméter-terület mintavételezése

Adja meg a hiperparaméter-térben használni kívánt mintavételi módszert. Azure Machine Learning a következő módszereket támogatja:

* Véletlenszerű mintavétel
* Rács mintavételezése
* Bayes-féle mintavételezés

#### <a name="random-sampling"></a>Véletlenszerű mintavétel

A [véletlenszerű mintavételezés](/python/api/azureml-train-core/azureml.train.hyperdrive.randomparametersampling) támogatja a diszkrét és a folyamatos hiperparaméterek beállítása. Támogatja az alacsony teljesítményű futtatások korai megszüntetését. Egyes felhasználók véletlenszerű mintavételsel végzett kezdeti keresést végeznek, majd pontosítják a keresési területet az eredmények javítása érdekében.

Véletlenszerű mintavételezés esetén a hiperparaméter értékek véletlenszerűen vannak kiválasztva a megadott keresési területről. 

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

#### <a name="grid-sampling"></a>Rács mintavételezése

A [rácsos mintavételezés](/python/api/azureml-train-core/azureml.train.hyperdrive.gridparametersampling) támogatja a különálló hiperparaméterek beállítása. Használjon rácsos mintavételezést, ha a költségkeretet a keresési területtel való kimerítő kereséshez is használhatja. Az alacsony teljesítményű futtatások korai megszakítását támogatja.

A rács mintavételezése egyszerű rácsos keresést tesz elérhetővé az összes lehetséges értéken. A rács mintavételezése csak a hiperparaméterek beállítása használatával használható `choice` . Például a következő területnek hat mintája van:

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

A [Bayes mintavételezés](/python/api/azureml-train-core/azureml.train.hyperdrive.bayesianparametersampling) a Bayes optimalizációs algoritmuson alapul. A mintákat a korábbi minták alapján választja, így az új minták javítják az elsődleges metrikát.

A többhelyes mintavételezés használata akkor ajánlott, ha elegendő költségkerettel rendelkezik a hiperparaméter-terület megismeréséhez. A legjobb eredmények elérése érdekében javasoljuk, hogy legfeljebb 20 alkalommal fusson a beállított hiperparaméterek beállítása száma. 

Az egyidejű futtatások száma hatással van a hangolási folyamat hatékonyságára. Az egyidejű futtatások kisebb száma nagyobb mintavételezési konvergenciát eredményezhet, mivel a kisebb párhuzamosságok növelik a korábban befejezett futtatások előnyeit élvező futtatások számát.

A Bayes mintavételezés csak `choice` `uniform` `quniform` a és a keresési terület feletti eloszlásokat támogatja.

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
from azureml.train.hyperdrive import uniform, choice
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```



## <a name="specify-primary-metric"></a><a name="specify-primary-metric-to-optimize"></a> Elsődleges metrika meghatározása

Itt adhatja meg azt az [elsődleges metrikát](/python/api/azureml-train-core/azureml.train.hyperdrive.primarymetricgoal) , amelyet a hiperparaméter hangolással szeretne optimalizálni. Minden betanítási Futtatás kiértékelése az elsődleges metrika esetében történik. A korai megszakítási szabályzat az elsődleges metrikát használja az alacsony teljesítményű futtatások azonosítására.

Adja meg az elsődleges metrika következő attribútumait:

* `primary_metric_name`: Az elsődleges metrika nevének pontosan meg kell egyeznie a betanítási parancsfájl által naplózott metrika nevével
* `primary_metric_goal`: Lehet `PrimaryMetricGoal.MAXIMIZE` vagy vagy `PrimaryMetricGoal.MINIMIZE` , és meghatározza, hogy az elsődleges metrika maximalizálása vagy kisméretű legyen-e a futtatások kiértékelése során. 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

Ez a példa maximalizálja a "pontosságot".

### <a name="log-metrics-for-hyperparameter-tuning"></a><a name="log-metrics-for-hyperparameter-tuning"></a>Hiperparaméter hangolásának naplózási mérőszámai

A modell betanítási parancsfájljában be **kell** jelentkeznie az elsődleges metrikára a modell betanítása során, hogy a HyperDrive hozzáférhessen a hiperparaméter hangoláshoz.

A betanítási parancsfájlban az alábbi kódrészlettel naplózza az elsődleges metrikát:

```Python
from azureml.core.run import Run
run_logger = Run.get_context()
run_logger.log("accuracy", float(val_accuracy))
```

A betanítási szkript kiszámítja a `val_accuracy` következőt, és naplózza az elsődleges metrika "pontossága" értékként. Minden alkalommal, amikor a metrika be van jelentkezve, a hiperparaméter hangolási szolgáltatás fogadja. A jelentéskészítés gyakoriságának meghatározása.

További információ a modellek betanítási futtatásával kapcsolatos adatok naplózásáról: [a naplózás engedélyezése az Azure ml betanítási futtatásában](how-to-track-experiments.md).

## <a name="specify-early-termination-policy"></a><a name="early-termination"></a> Korai megszakítási szabályzat meghatározása

A gyengén teljesítő futtatások automatikus leállítása egy korai megszakítási házirenddel. A korai lezárás javítja a számítási hatékonyságot.

A következő paramétereket állíthatja be, amelyekkel szabályozhatja, hogy mikor alkalmazza a rendszer a szabályzatot:

* `evaluation_interval`: a szabályzat alkalmazásának gyakorisága. Minden alkalommal, amikor a betanítási parancsfájl egy intervallumként naplózza az elsődleges metrikát. A `evaluation_interval` rendszer minden alkalommal alkalmazza a szabályzatot, amikor a betanítási parancsfájl az elsődleges metrikát jelenti. `evaluation_interval`A (2) rendszer minden más alkalommal alkalmazza a házirendet. Ha nincs megadva, a értéke `evaluation_interval` alapértelmezés szerint 1.
* `delay_evaluation`: az első szabályzat kiértékelését a megadott számú intervallumra késlelteti. Ez egy opcionális paraméter, amely elkerüli a képzések korai leállítását azáltal, hogy lehetővé teszi az összes konfiguráció futtatását a minimális számú intervallumra. Ha meg van adva, a házirend a evaluation_interval minden olyan többszörösét alkalmazza, amely nagyobb vagy egyenlő, mint delay_evaluation.

Azure Machine Learning a következő korai megszakítási szabályzatokat támogatja:
* [Bandita-szabályzat](#bandit-policy)
* [Középérték leállítása házirend](#median-stopping-policy)
* [Csonkítás kiválasztási szabályzata](#truncation-selection-policy)
* [Nincs megszakítási szabályzat](#no-termination-policy-default)


### <a name="bandit-policy"></a>Bandita-szabályzat

A [Bandit-szabályzat](/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy#definition) a Slack Factor/Slack és a kiértékelési időköz alapján történik. A Bandit akkor fejeződik be, ha az elsődleges metrika nem a legsikeresebb Futtatás megadott tartalékidő-tényezője/Slack-mennyisége szerint fut.

> [!NOTE]
> A Bayes mintavételezés nem támogatja a korai megszakítást. A Bayes-mintavétel használatakor állítsa be a t `early_termination_policy = None` .

A következő konfigurációs paramétereket kell megadni:

* `slack_factor` vagy `slack_amount` : a tartalékidőt a legjobb teljesítményt nyújtó tanfolyamra vonatkozóan lehet elvégezni. `slack_factor` meghatározza a megengedett tartalékidőt arányként. `slack_amount` meghatározza a megengedett tartalékidőt abszolút értékként az arány helyett.

    Tegyük fel például, hogy a rendszer a 10. intervallumban alkalmazza a Bandit-szabályzatot. Tegyük fel, hogy a legjobb teljesítményű Futtatás a 10-es intervallumban jelentett egy elsődleges metrikát 0,8, amelynek célja az elsődleges metrika maximalizálása. Ha a házirend `slack_factor` 0,2-as értéket ad meg, minden olyan képzés fut, amelynek a 10. intervallumában a legjobb metrika kevesebb, mint 0,66 (0,8/(1 + `slack_factor` )).
* `evaluation_interval`: (nem kötelező) a szabályzat alkalmazásának gyakorisága
* `delay_evaluation`: (nem kötelező) késlelteti az első szabályzat kiértékelését a megadott számú intervallumra vonatkozóan


```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

Ebben a példában a korai megszakítási szabályzatot minden intervallumban alkalmazza a rendszer, amikor a mérőszámok jelentése megtörténik, az 5. értékelési intervallumtól kezdődően. Minden olyan Futtatás, amelynek a legjobb metrikája kisebb, mint (1/(1 + 0,1), vagy a legjobb teljesítményű Futtatás 91%-a leáll.

### <a name="median-stopping-policy"></a>Középérték leállítása házirend

A [középérték leállítása](/python/api/azureml-train-core/azureml.train.hyperdrive.medianstoppingpolicy) a futtatások által jelentett elsődleges metrikák futtatási átlagán alapuló korai megszakítási házirend. Ez a szabályzat az összes betanítási Futtatás átlagát számítja ki, és leállítja azokat a futtatásokat, amelyek elsődleges metrikai értéke rosszabb, mint az átlagok középértéke.

Ez a szabályzat a következő konfigurációs paramétereket veszi figyelembe:
* `evaluation_interval`: a házirend alkalmazásának gyakorisága (opcionális paraméter).
* `delay_evaluation`: az első szabályzat kiértékelését késlelteti a megadott számú intervallumra (opcionális paraméter).


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

Ebben a példában a korai megszakítási szabályzatot minden olyan intervallumban alkalmazni kell, amely az 5. értékelési intervallumtól kezdődően történik. A Futtatás leállt az 5. intervallumban, ha a legjobb elsődleges metrikája rosszabb, mint a futó átlagok középértéke az összes betanítási időszakban 1:5 intervallumban.

### <a name="truncation-selection-policy"></a>Csonkítás kiválasztási szabályzata

A [csonkítás kiválasztása](/python/api/azureml-train-core/azureml.train.hyperdrive.truncationselectionpolicy) megszakítja a legalacsonyabb végrehajtású futtatások százalékos arányát minden értékelési intervallumban. A futtatások összehasonlítása az elsődleges metrika használatával történik. 

Ez a szabályzat a következő konfigurációs paramétereket veszi figyelembe:

* `truncation_percentage`: a legalacsonyabb végrehajtású futtatások százalékos aránya az egyes értékelési intervallumok leállításához. 1 és 99 közötti egész érték.
* `evaluation_interval`: (nem kötelező) a szabályzat alkalmazásának gyakorisága
* `delay_evaluation`: (nem kötelező) késlelteti az első szabályzat kiértékelését a megadott számú intervallumra vonatkozóan


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

Ebben a példában a korai megszakítási szabályzatot minden olyan intervallumban alkalmazni kell, amely az 5. értékelési intervallumtól kezdődően történik. A Futtatás az 5. intervallumban leáll, ha a teljesítménye az 5. intervallumban az összes Futtatás legalacsonyabb 20%-ában fut az 5. intervallumban.

### <a name="no-termination-policy-default"></a>Nincs lemondási szabályzat (alapértelmezett)

Ha nincs megadva házirend, a hiperparaméter hangolási szolgáltatás lehetővé teszi, hogy az összes tanítás fusson a befejezésig.

```Python
policy=None
```

### <a name="picking-an-early-termination-policy"></a>Korai megszakítási szabályzat kiválasztása

* Egy olyan konzervatív szabályzat esetében, amely megtakarítást biztosít az ígéretes feladatok megszakítása nélkül, vegye fontolóra az `evaluation_interval` 1 és 5 közötti adatmegőrzési szabályzatot `delay_evaluation` . Ezek a konzervatív beállítások, amelyek körülbelül 25%-35%-os megtakarítást biztosítanak az elsődleges metrika elvesztése nélkül (kiértékelési adatok alapján).
* Az agresszívebb megtakarítások esetében a Bandit-szabályzatot egy kisebb, megengedett tartalékidő-vagy csonkolt kiválasztási szabályzattal kell használni, amelynek nagyobb a csonkítás százalékaránya.

## <a name="create-and-assign-resources"></a>Erőforrások létrehozása és kiosztása

Az erőforrás-költségkeret szabályozása a betanítási futtatások maximális számának megadásával.

* `max_total_runs`: A betanítási futtatások maximális száma. 1 és 1000 közötti egész számnak kell lennie.
* `max_duration_minutes`: (nem kötelező) a hiperparaméter hangolási kísérlet maximális időtartama (percben). Ezt az időtartamot követően futtatja a rendszer.

>[!NOTE] 
>Ha a `max_total_runs` és a érték is meg `max_duration_minutes` van adva, a hiperparaméter hangolási kísérlet leáll, amikor eléri az első két küszöbértéket.

Emellett adja meg a betanítási futtatások maximális számát, hogy az a hiperparaméter hangolási keresés során egyidejűleg fusson.

* `max_concurrent_runs`: (nem kötelező) a futtatott futtatások maximális száma egyidejűleg. Ha nincs megadva, az összes Futtatás párhuzamosan elindul. Ha meg van adva, egy 1 és 100 közötti egész számnak kell lennie.

>[!NOTE] 
>Az egyidejű futtatások száma a megadott számítási célra elérhető erőforrásokon van lefuttatva. Győződjön meg arról, hogy a számítási cél rendelkezik a kívánt egyidejűséghez rendelkezésre álló erőforrásokkal.

```Python
max_total_runs=20,
max_concurrent_runs=4
```

Ez a kód úgy konfigurálja a hiperparaméter hangolási kísérletet, hogy legfeljebb 20 teljes futtatást használjon, egyszerre négy konfigurációt futtatva.

## <a name="configure-hyperparameter-tuning-experiment"></a>Hiperparaméter-hangolási kísérlet konfigurálása

A [hiperparaméter hangolási kísérlet konfigurálásához adja meg](/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverunconfig) a következőket:
* A megadott hiperparaméter keresési terület
* A korai megszakítási szabályzat
* Az elsődleges metrika
* Erőforrás-elosztási beállítások
* ScriptRunConfig `script_run_config`

A ScriptRunConfig az a betanítási szkript, amely a mintául szolgáló hiperparaméterek beállítása fog futni. Meghatározza az erőforrásokat (egy vagy több csomópontot) és a használni kívánt számítási célt.

> [!NOTE]
>A-ben használt számítási célnak `script_run_config` elegendő erőforrással kell rendelkeznie a párhuzamossági szint kielégítéséhez. További információ a ScriptRunConfig: a [betanítási futtatások konfigurálása](how-to-set-up-training-targets.md).

Konfigurálja a hiperparaméter hangolási kísérletet:

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

A `HyperDriveConfig` Beállítja a paraméternek átadott paramétereket `ScriptRunConfig script_run_config` . A- `script_run_config` ben pedig a paramétereket továbbítja a betanítási parancsfájlnak. A fenti kódrészlet a minta notebook [-Betanításból, a hiperparaméter és a PyTorch-vel való üzembe helyezésből](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks/pytorch/train-hyperparameter-tune-deploy-with-pytorch)származik. Ebben a példában a `learning_rate` és a `momentum` Paraméterek be lesznek hangolva. A futtatások korai leállítását az a határozza meg `BanditPolicy` , amely leállítja azt a futtatást, amelynek elsődleges metrikája kívül esik a `slack_factor` (lásd: [BanditPolicy-osztály referenciája](/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy)). 

A mintából származó következő kód azt mutatja be, hogy a rendszer hogyan fogadja el, elemezze és átadja a betanítási parancsfájl függvényének a beérkező értékeket `fine_tune_model` :

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
> Minden hiperparaméter-Futtatás újraindítja a képzést, beleértve a modell és _az összes adatbetöltő_ újrafordítását is. Ezt a költségeket egy Azure Machine Learning folyamat vagy manuális folyamat használatával csökkentheti, így a képzés futtatása előtt a lehető legtöbb adatelőkészítési műveletet végrehajthatja. 

## <a name="submit-hyperparameter-tuning-experiment"></a>Hiperparaméter-hangolási kísérlet elküldése

A hiperparaméter hangolási konfigurációjának meghatározása után [küldje el a kísérletet](/python/api/azureml-core/azureml.core.experiment%28class%29#submit-config--tags-none----kwargs-):

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hd_config)
```

## <a name="warm-start-hyperparameter-tuning-optional"></a>Meleg indítás hiperparaméter finomhangolása (nem kötelező)

A modell legjobb hiperparaméter-értékeinek megkeresése ismétlődő folyamat lehet. A hiperparaméter hangolásának felgyorsításához újra felhasználhatja az előző öt futtatási ismereteket.

A meleg kezdést a mintavételi módszertől függően másképp kell kezelni:
- **Bayes mintavételezés**: az előző futtatásból származó kísérletek az új minták kiválasztására és az elsődleges metrika javítására szolgáló korábbi ismeretekként használatosak.
- **Véletlenszerű mintavételezés** vagy **Rácsvonalak mintavételezése**: a korai megszakítás a korábbi futtatások ismereteit használja a gyengén teljesítő futtatások meghatározásához. 

Itt adhatja meg, hogy mely szülő-futtatásokból szeretne melegen indulni.

```Python
from azureml.train.hyperdrive import HyperDriveRun

warmstart_parent_1 = HyperDriveRun(experiment, "warmstart_parent_run_ID_1")
warmstart_parent_2 = HyperDriveRun(experiment, "warmstart_parent_run_ID_2")
warmstart_parents_to_resume_from = [warmstart_parent_1, warmstart_parent_2]
```

Ha a hiperparaméter-hangolási kísérlet meg lett szakítva, folytathatja az utolsó ellenőrzőponton futó képzések futtatását. A betanítási szkriptnek azonban az ellenőrzőpont-logikát kell kezelnie.

A betanítási futtatásnak ugyanazt a hiperparaméter-konfigurációt kell használnia, és csatlakoztatnia kell a kimeneti mappákat. A betanítási szkriptnek el kell fogadnia az `resume-from` argumentumot, amely tartalmazza azokat az ellenőrzőpontokat vagy modelleket, amelyekből folytatni szeretné a betanítást. A következő kódrészlettel folytathatja az egyéni képzések futtatását:

```Python
from azureml.core.run import Run

resume_child_run_1 = Run(experiment, "resume_child_run_ID_1")
resume_child_run_2 = Run(experiment, "resume_child_run_ID_2")
child_runs_to_resume = [resume_child_run_1, resume_child_run_2]
```

A hiperparaméter hangolási kísérletet beállíthatja úgy, hogy az egy korábbi kísérletből induljon, vagy folytassa az egyéni képzések futtatását a választható paraméterek `resume_from` és a `resume_child_runs` konfiguráció használatával:

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

## <a name="visualize-hyperparameter-tuning-runs"></a>Hiperparaméter-hangolási futtatások megjelenítése

Megjelenítheti a hiperparaméter hangolási folyamatait a Azure Machine Learning Studióban, vagy használhat notebook widgetet is.

### <a name="studio"></a>Studio

A [Azure Machine learning Studióban](https://ml.azure.com)megjelenítheti az összes hiperparaméter hangolási futtatást. A kísérleteknek a portálon történő megtekintésével kapcsolatos további információkért lásd: [a futtatási rekordok megtekintése a Studióban](how-to-monitor-view-training-logs.md#view-the-experiment-in-the-web-portal).

- **Metrikák diagramja**: Ez a vizualizáció nyomon követi az egyes HyperDrive-gyermekek naplózott metrikáit a hiperparaméter hangolásának időtartama alatt. Minden sor a gyermek futtatását jelöli, és minden pont az elsődleges metrika értékét méri a futtatókörnyezet ismétlésében.  

    :::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-metrics.png" alt-text="Hiperparaméter-hangolás metrikái diagramja":::

- **Párhuzamos koordináták diagramja**: Ez a vizualizáció az elsődleges metrikai teljesítmény és az egyes hiperparaméter értékek közötti korrelációt mutatja. A diagram interaktív a tengelyek mozgásával (kattintással és húzással), valamint az értékek egyetlen tengelyen való kiemelésével (kattintson és húzással függőlegesen egy tengely mentén, hogy kiemelje a kívánt értékek tartományát).

    :::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-parallel-coordinates.png" alt-text="Hiperparaméter hangolása párhuzamos koordináták diagramja":::

- **2 dimenziós pontdiagram**: Ez a vizualizáció a két egyéni hiperparaméterek beállítása közötti korrelációt mutatja a hozzájuk társított elsődleges metrikai értékkel együtt.

    :::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-2-dimensional-scatter.png" alt-text="Hyparameter finomhangolása 2 – dimenziós pontdiagram":::

- **3 dimenziós pontdiagram**: Ez a vizualizáció ugyanaz, mint a 2D, de lehetővé teszi az elsődleges metrikai értékkel való korreláció három hiperparaméter-dimenziójának használatát. Azt is megteheti, hogy a diagram áttájolása lehetőségre kattint, és a különböző korrelációkat a 3D térben jeleníti meg.

    :::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-3-dimensional-scatter.png" alt-text="3 dimenziós Hyparameter hangolási diagram":::

### <a name="notebook-widget"></a>Jegyzetfüzet widget

A betanítási folyamat előrehaladásának megjelenítéséhez használja a [notebook widgetet](/python/api/azureml-widgets/azureml.widgets.rundetails) . A következő kódrészlet megjeleníti az összes hiperparaméter finomhangolását egy helyen egy Jupyter-jegyzetfüzetben:

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

Ez a kód egy táblázatot jelenít meg, amely részletesen ismerteti az egyes hiperparaméter-konfigurációk betanítási futtatásait.

:::image type="content" source="media/how-to-tune-hyperparameters/hyperparameter-tuning-table.png" alt-text="Hiperparaméter hangolási táblázat":::

Az egyes futtatások teljesítményét a betanítási folyamatokban is megjelenítheti.

## <a name="find-the-best-model"></a>A legjobb modell megkeresése

Miután az összes hiperparaméter-hangolási Futtatás befejeződött, azonosítsa a legjobban teljesítő konfiguráció-és hiperparaméter értékeket:

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

## <a name="sample-notebook"></a>Minta notebook

Tekintse meg a következő mappában található hiperparaméter-* jegyzetfüzeteket:
* [használati útmutató – azureml/ml – keretrendszerek](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Következő lépések
* [Kísérlet nyomon követése](how-to-track-experiments.md)
* [Betanított modell üzembe helyezése](how-to-deploy-and-where.md)
