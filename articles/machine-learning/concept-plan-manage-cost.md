---
title: Költségtervezés és a költségek kezelése
titleSuffix: Azure Machine Learning
description: Megtervezheti és kezelheti a költségek elemzésével Azure Machine Learning a Azure Portalban. A további költségmegtakarítási tippek segítségével csökkentheti a költségeket az ML-modellek kiépítésekor.
author: sdgilley
ms.author: sgilley
ms.custom: subject-cost-optimization
ms.reviewer: nigup
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: be8b11b6ddf715e5d6226372e8d03b42dec5fc7d
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102215986"
---
# <a name="plan-and-manage-costs-for-azure-machine-learning"></a>A Azure Machine Learning költségeinek megtervezése és kezelése

Ez a cikk a Azure Machine Learning költségeinek megtervezését és kezelését ismerteti. Első lépésként az Azure díjszabási kalkulátor segítségével megtervezheti a költségeket, mielőtt erőforrásokat hozna létre. Ezután adja hozzá az Azure-erőforrásokat, és tekintse át a becsült költségeket. Végül a költséghatékony tippeket használhatja a modell felügyelt Azure Machine Learning számítási fürtökkel való betanításához.

Azure Machine Learning erőforrások használatának elkezdése után a Cost Management szolgáltatással állíthatja be a költségvetéseket, és figyelheti a költségeket. Tekintse át az előre jelzett költségeket, és azonosítsa a kiadási trendeket, hogy azonosítsa azokat a területeket, ahol érdemes lehet eljárni.

Ismerje meg, hogy a Azure Machine Learning költségei csak a havi költségek egy részét jelentik az Azure-számlán. Ha más Azure-szolgáltatásokat használ, akkor az Azure-előfizetésében használt összes Azure-szolgáltatás és erőforrás után, beleértve a harmadik féltől származó szolgáltatásokat is. Ez a cikk a Azure Machine Learning költségeinek tervezését és kezelését ismerteti. Miután megismerte Azure Machine Learning költségeinek kezelését, hasonló módszerekkel kezelheti az előfizetésében használt összes Azure-szolgáltatás költségeit.

A gépi tanulási modellek betanításakor a felügyelt Azure Machine Learning számítási fürtök segítségével kihasználhatja a költségmegtakarítással járó tippeket:

* A betanítási fürtök konfigurálása automatikus skálázáshoz
* Kvóták beállítása az előfizetéshez és a munkaterületekhez
* Leállítási szabályzatok beállítása a betanítási futtatáshoz
* Alacsony prioritású virtuális gépek használata (VM)
* Azure-beli fenntartott VM-példány használata

## <a name="prerequisites"></a>Előfeltételek

A költségelemzés különböző Azure-fióktípusokat támogat. A támogatott fióktípusok teljes listáját lásd: [A Cost Management adatainak értelmezése](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). A költségadatok megtekintéséhez legalább olvasási jogosultsággal kell rendelkeznie az Azure-fiókjához. 

További információért az Azure Cost Management adataihoz való hozzáférés hozzárendeléséről: [Adatokhoz való hozzáférés hozzárendelése](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimate-costs-before-using-azure-machine-learning"></a>A költségek becslése a használata előtt Azure Machine Learning

Az [Azure díjszabási számológépével](https://azure.microsoft.com/pricing/calculator?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) megbecsülheti a költségeket, mielőtt létrehozza az erőforrásokat egy Azure Machine learning-fiókban. A bal oldalon válassza az **AI + Machine learning** lehetőséget, majd a kezdéshez válassza a **Azure Machine learning** lehetőséget.  

A következő képernyőkép a kalkulátor használatával mutatja be a költségbecslést:

:::image type="content" source="media/concept-plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Költségbecslés az Azure kalkulátorban":::

Amikor új erőforrásokat ad hozzá a munkaterülethez, térjen vissza ehhez a kalkulátorhoz, és adja hozzá ugyanezt az erőforrást a költségbecslés frissítéséhez.

További információ: [Azure Machine learning díjszabása](https://azure.microsoft.com/pricing/details/machine-learning?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="understand-the-full-billing-model-for-azure-machine-learning"></a>A Azure Machine Learning teljes számlázási modelljének megismerése

Azure Machine Learning olyan Azure-infrastruktúrán fut, amely költségekkel jár együtt, Azure Machine Learning az új erőforrás telepítésekor. Fontos tisztában lenni azzal, hogy további infrastruktúra merülhet fel. Az üzembe helyezett erőforrások módosításakor ezt a költségeket kell kezelnie. 

### <a name="costs-that-typically-accrue-with-azure-machine-learning"></a>Általában a Azure Machine Learningekkel felmerülő költségek

Ha Azure Machine Learning-munkaterülethez hoz létre erőforrásokat, az egyéb Azure-szolgáltatások erőforrásai is létrejönnek. Ezek a következők:

* [Azure Container Registry](https://azure.microsoft.com/pricing/details/container-registry?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) Alapszintű fiók
* [Azure Block blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) (általános célú v1)
* [Key Vault](https://azure.microsoft.com/pricing/details/key-vault?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Application Insights](https://azure.microsoft.com/en-us/pricing/details/monitor?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
 
### <a name="costs-might-accrue-after-resource-deletion"></a>A költségek az erőforrás törlése után merülhetnek fel

Ha töröl egy Azure Machine Learning munkaterületet a Azure Portalban vagy az Azure CLI-vel, a következő erőforrások továbbra is fennállnak. Amíg nem törli őket, továbbra is felmerülnek a költségek.

* Azure Container Registry
* Azure Block Blob Storage
* Key Vault
* Application Insights

Ha a munkaterületet ezekkel a függő erőforrásokkal együtt szeretné törölni, használja az SDK-t:

```python
ws.delete(delete_dependent_resources=True)
```

Ha az Azure Kubernetes szolgáltatást (ak) hozza létre a munkaterületen, vagy ha bármilyen számítási erőforrást csatlakoztat a munkaterülethez, ezeket külön kell törölnie [Azure Portalban](https://portal.azure.com).

### <a name="using-azure-prepayment-credit-with-azure-machine-learning"></a>Azure-előfizetési kredit használata Azure Machine Learning

Az Azure-előfizetéssel (korábban pénzügyi kötelezettségvállalásnak nevezett) Kredittel Azure Machine Learning díjat fizethet. Az Azure-előfizetések azonban nem használhatók fel a harmadik féltől származó termékekhez és szolgáltatásokhoz, például az Azure piactéren fizetendő díjakért.


## <a name="create-budgets"></a>Költségvetések létrehozása

A költségek kezeléséhez [költségvetéseket](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) és [riasztásokat](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) hozhat létre, amelyek automatikusan figyelmeztetik az érdekelt feleket a rendellenes kiadásokról és a túlköltekezési kockázatokról. A riasztások a költségvetés és a költségek küszöbértékei alapján működnek. Az Azure-előfizetésekhez és-erőforráscsoportokhöz költségvetést és riasztásokat hoznak létre, így azok a teljes költségű figyelési stratégia részeként hasznosak. 

A költségvetések az Azure-ban meghatározott erőforrásokhoz vagy szolgáltatásokhoz szűrőkkel hozhatók létre, ha a figyelésben részletesebb részletességre van szükség. A szűrők segítségével biztosíthatja, hogy véletlenül ne hozzon létre olyan új erőforrásokat, amelyek további pénzbe kerülnek. Ha többet szeretne megtudni a szűrési lehetőségekről, amikor költségvetést hoz létre, tekintse meg a [csoportosítási és szűrési beállítások](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)című témakört.

## <a name="export-cost-data"></a>Költségadatok exportálása

A költségadatok a Storage-fiókba is [exportálhatók](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) . Ez akkor hasznos, ha szüksége van rá, vagy másoknak további adatelemzést kell végeznie a költségekért. Egy pénzügyi csapat például az Excel vagy a Power BI használatával elemezheti az adatelemzést. A költségeket napi, heti vagy havi rendszerességgel exportálhatja, és egyéni dátumtartományt is beállíthat. A költségadatok exportálásának ajánlott módja a Cost-adatkészletek beolvasása.

## <a name="other-ways-to-manage-and-reduce-costs-for-azure-machine-learning"></a>Egyéb módszerek a Azure Machine Learning költségeinek kezeléséhez és csökkentéséhez

Ezekkel a tippekkel a gépi tanulási számítási erőforrásokra vonatkozó költségeket is megteheti.

### <a name="use-azure-machine-learning-compute-cluster-amlcompute"></a>Azure Machine Learning számítási fürt (AmlCompute) használata

A folyamatosan változó adatváltozások esetén gyors és gördülékeny modell-képzésre és-képzésre van szükség a pontos modellek fenntartása érdekében. A folyamatos képzés azonban a GPU-k mélyreható tanulási modelljeire is kikerül. 

Azure Machine Learning felhasználók használhatják a felügyelt Azure Machine Learning számítási fürtöt, más néven AmlCompute. A AmlCompute számos GPU-és CPU-beállítást támogat. A AmlCompute az előfizetése nevében, Azure Machine Learning alapján üzemelteti. Ugyanezt a vállalati szintű biztonsági, megfelelőségi és irányítási funkciókat biztosítja az Azure IaaS Cloud Scale-ben.

Mivel ezek a számítási készletek az Azure IaaS-infrastruktúráján belül vannak, a képzést az infrastruktúra többi részével megegyező biztonsági és megfelelőségi követelményekkel helyezheti üzembe, méretezheti és kezelheti.  Ezek az üzembe helyezések az előfizetésében történnek, és engedelmeskednek az irányítás szabályainak. További információ a [Azure Machine learning számítási](how-to-create-attach-compute-cluster.md)feladatokról.

### <a name="configure-training-clusters-for-autoscaling"></a>Betanítási fürtök konfigurálása automatikus skálázáshoz

A fürtök automatikus skálázása a munkaterhelés követelményei alapján csökkenti a költségeket, így csak a szükséges igényeket használhatja.

A AmlCompute-fürtök úgy vannak kialakítva, hogy a számítási feladatok alapján dinamikusan méretezhetőek legyenek. A fürt akár a konfigurált csomópontok maximális számáig is méretezhető. Ahogy az egyes futtatások befejeződik, a fürt felszabadítja a csomópontokat, és a beállított minimális csomópontok számának megfelelően méretezhető.

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

Azt is beállíthatja, hogy a csomópont mennyi ideig tétlen legyen a skálázás előtt. Alapértelmezés szerint a leskálázás előtti üresjárati idő 120 másodpercre van állítva.

+ Ha kevesebb iterációs kísérletezést végez, csökkentse ezt az időt a költségek megtakarítása érdekében.
+ Ha nagy mértékben ismétlődő fejlesztési/tesztelési kísérleteket hajt végre, akkor előfordulhat, hogy meg kell nőnie az időt, hogy a képzési parancsfájl vagy környezet minden módosítása után ne fizessen az állandó skálázásra.

A AmlCompute-fürtök konfigurálhatók a munkaterhelési követelmények Azure Portalban való módosításához a [AMLCOMPUTE SDK osztály](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute?preserve-view=true&view=azure-ml-py), a [AmlCompute CLI](/cli/azure/ext/azure-cli-ml/ml/computetarget/create#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute)és a [REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable)-k használatával.

```azurecli
az ml computetarget create amlcompute --name testcluster --vm-size Standard_NC6 --min-nodes 0 --max-nodes 5 --idle-seconds-before-scaledown 300
```

### <a name="set-quotas-on-resources"></a>Erőforrások kvótáinak beállítása

A AmlCompute [kvóta (vagy korlát) konfigurációt](how-to-manage-quotas.md#azure-machine-learning-compute)tartalmaz. Ez a kvóta virtuálisgép-család (például Dv2 sorozat, NCv3 sorozat), és az egyes előfizetések régiónként eltérőek. Az előfizetések kis alapértékekkel kezdődnek, és ezzel a beállítással szabályozható, hogy az előfizetésében milyen Amlcompute-erőforrások legyenek elérhetők. 

Konfigurálja a [munkaterület-szintű kvótát](how-to-manage-quotas.md#workspace-level-quotas)is a virtuálisgép-család szerint az előfizetésben található minden egyes munkaterülethez. Így részletesebben szabályozhatja az egyes munkaterületek által felmerülhető költségeket, és korlátozhatja bizonyos virtuálisgép-családokat. 

Ha a kvótákat a munkaterület szintjén szeretné beállítani, kezdje a [Azure Portal](https://portal.azure.com).  Válasszon ki egy munkaterületet az előfizetésben, majd a bal oldali ablaktáblán válassza a **kihasználtságok és kvóták** lehetőséget. Ezután válassza a **kvóták konfigurálása** lapot a kvóták megtekintéséhez. A kvóta beállításához jogosultságok szükségesek az előfizetési hatókörben, mivel ez egy olyan beállítás, amely több munkaterületet érint.

### <a name="set-run-autotermination-policies"></a>Az automegszakítási szabályzatok futtatásának beállítása 

Bizonyos esetekben konfigurálnia kell a betanítási időszakokat, hogy azok az időtartamra korlátozzák, vagy korán megszakítsák őket. Ha például Azure Machine Learning beépített hiperparaméter-hangolást vagy automatizált gépi tanulást használ.

Íme néhány lehetőség:
* Definiáljon egy nevű paramétert a `max_run_duration_seconds` RunConfiguration annak a maximális időtartamnak a szabályozására, ameddig a Futtatás kiterjeszthető a kiválasztott számítási lehetőségre (helyi vagy távoli Felhőbeli számítás).
* A [hiperparaméter-hangoláshoz](how-to-tune-hyperparameters.md#early-termination)adjon meg egy korai megszakítási szabályzatot egy bandita-házirendből, egy középértékes leállítási házirendből vagy egy csonkolt kiválasztási szabályzatból. A hiperparaméter-lóversenyfogadások további szabályozásához használja a következő paramétereket: `max_total_runs` vagy `max_duration_minutes` .
* Az [automatizált gépi tanuláshoz](how-to-configure-auto-train.md#exit)állítson be hasonló megszakítási házirendeket a  `enable_early_stopping` jelző használatával. Olyan tulajdonságokat is használhat, mint a és a a `iteration_timeout_minutes` `experiment_timeout_minutes` Futtatás maximális időtartamának szabályozására, vagy a teljes kísérletre.

### <a name="use-low-priority-vms"></a><a id="low-pri-vm"></a> Alacsony prioritású virtuális gépek használata

Az Azure-ban a virtuális gépek méretezési csoportjaiban, a Batchben és a Machine Learning szolgáltatásban Low-Priority virtuális gépeken túl nem használt kapacitást használhat. Ezek a foglalások előre emptible, de kedvezményes áron érhetők el a dedikált virtuális gépekhez képest. Általánosságban elmondható, hogy Low-Priority virtuális gépek használatát javasoljuk a Batch-munkaterhelésekhez. Azokat is érdemes használni, ahol a megszakítások az Újraküldés (a Batch-következtetések esetében) vagy az újraindítások (az ellenőrzőpontokkal folytatott mély tanulási képzések esetén) helyreállítására használhatók.

Low-Priority virtuális gépek egyetlen kvótával rendelkeznek a dedikált kvóta értéktől, amely a VM-család. [További információ a AmlCompute-kvótákkal kapcsolatban](how-to-manage-quotas.md).

 Low-Priority virtuális gépek nem működnek számítási példányok esetén, mert támogatniuk kell az interaktív notebook-élményt.

### <a name="use-reserved-instances"></a>Fenntartott példányok használata

A számítási erőforrások pénzének megtakarításának egy másik módja az Azure Reserved VM instance. Ennek az ajánlatnak a keretében egy-vagy hároméves szerződéssel kell eljutnia. Ezek az árengedmények az utólagos elszámolású díjak 72%-ában érvényesek, és közvetlenül a havi Azure-számlára vonatkoznak.

Azure Machine Learning a számítás a fenntartott példányokat is támogatja. Ha egy vagy három éves fenntartott példányt vásárol, a rendszer automatikusan kedvezményt alkalmaz a Azure Machine Learning felügyelt számítási feladatokra.


## <a name="next-steps"></a>Következő lépések

- Megtudhatja [, hogyan optimalizálhatja a felhőalapú befektetéseit Azure Cost Managementokkal](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- További információ a költségek a [Cost Analysis](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)szolgáltatással történő kezeléséről.
- További információ a [váratlan költségek megelőzéséről](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Vegye figyelembe a [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) interaktív tanulás tanfolyamát.