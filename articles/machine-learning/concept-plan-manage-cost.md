---
title: Költségtervezés és a költségek kezelése
titleSuffix: Azure Machine Learning
description: Tervezze meg és kezelje a Azure Machine Learning költségelemzéssel a Azure Portal. További költségmegtakarítási tippek a gépi tanulási modellek kiépítésekor a költségek csökkentéséhez.
author: sdgilley
ms.author: sgilley
ms.custom: subject-cost-optimization, devx-track-azurecli
ms.reviewer: nigup
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 39c649cccdf159810ad01c2312c4ea4837d9f4fc
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478641"
---
# <a name="plan-and-manage-costs-for-azure-machine-learning"></a>A költségek megterve és kezelése a Azure Machine Learning

Ez a cikk a költségek tervezését és kezelését ismerteti a Azure Machine Learning. Először az Azure díjkalkulátor használatával tervezze meg a költségeket, mielőtt erőforrásokat ad hozzá. Ezután az Azure-erőforrások hozzáadása során tekintse át a becsült költségeket. Végül költségmegtakarítási tippeket használhat a modell felügyelt számítási Azure Machine Learning való betanításkor.

Az erőforrások használatának Azure Machine Learning a költségkezelési funkciók használatával állíthatja be a költségvetéseket és figyelheti a költségeket. Tekintse át az előre jelzett költségeket, és azonosítsa a költési trendeket, hogy azonosítsa azokat a területeket, ahol érdemes lehet cselekedni.

Ne feledjük, hogy a Azure Machine Learning költségei csak egy részét jelentik az Azure-számla havi költségeinek. Ha más Azure-szolgáltatásokat használ, akkor az Azure-előfizetésében használt összes Azure-szolgáltatásért és -erőforrásért, beleértve a külső szolgáltatásokat is. Ez a cikk a költségek tervezését és kezelését ismerteti a Azure Machine Learning. Ha már ismeri a szolgáltatások költségeinek kezelését, Azure Machine Learning hasonló módszereket alkalmazhat az előfizetésben használt összes Azure-szolgáltatás költségeinek kezelésére.

A gépi tanulási modellek betanításakor felügyelt Azure Machine Learning számítási fürtök használatával további költségmegtakarítási tippeket használhat:

* A betanító fürtök konfigurálása automatikus skálázáshoz
* Kvóták beállítása az előfizetéshez és a munkaterülethez
* Leállító szabályzatok beállítása a betanítás futtatásához
* Alacsony prioritású virtuális gépek használata
* Azure-beli fenntartott virtuálisgép-példány használata

## <a name="prerequisites"></a>Előfeltételek

A költségelemzés különböző Azure-fióktípusokat támogat. A támogatott fióktípusok teljes listáját lásd: [A Cost Management adatainak értelmezése](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). A költségadatok megtekintéséhez legalább olvasási jogosultsággal kell rendelkeznie az Azure-fiókjához. 

További információért az Azure Cost Management adataihoz való hozzáférés hozzárendeléséről: [Adatokhoz való hozzáférés hozzárendelése](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimate-costs-before-using-azure-machine-learning"></a>Költségbecslés a Azure Machine Learning

Az [Azure-díjkalkulátor használatával](https://azure.microsoft.com/pricing/calculator?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) megbecsülheti a költségeket, mielőtt erőforrásokat hoz létre egy Azure Machine Learning fiókban. A bal oldalon válassza az **AI + Machine Learning** lehetőséget, majd **Azure Machine Learning** a kezdéshez.  

Az alábbi képernyőképen a költségbecslés látható a kalkulátor használatával:

:::image type="content" source="media/concept-plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Költségbecslés az Azure-kalkulátorban":::

Amikor új erőforrásokat ad hozzá a munkaterülethez, térjen vissza ehhez a kalkulátorhoz, és itt adja hozzá ugyanezt az erőforrást a költségbecslések frissítéséhez.

További információ: díjszabás [Azure Machine Learning.](https://azure.microsoft.com/pricing/details/machine-learning?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

## <a name="understand-the-full-billing-model-for-azure-machine-learning"></a>A teljes számlázási modell Azure Machine Learning

Azure Machine Learning Azure-infrastruktúrán futnak, amely az új erőforrás üzembe helyezésekor Azure Machine Learning költségek mellett költségekkel is jár. Fontos megérteni, hogy további infrastruktúra is felmerülhet a költségekben. Ezt a költséget akkor kell kezelnie, amikor módosítja az üzembe helyezett erőforrásokat. 

### <a name="costs-that-typically-accrue-with-azure-machine-learning"></a>A jellemzően az előfizetéssel együtt Azure Machine Learning

Amikor erőforrásokat hoz létre egy Azure Machine Learning munkaterülethez, más Azure-szolgáltatások erőforrásai is létrejönnek. Ezek a következők:

* [Azure Container Registry](https://azure.microsoft.com/pricing/details/container-registry?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) Alapszintű fiók
* [Azure Block Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) (általános célú v1)
* [Key Vault](https://azure.microsoft.com/pricing/details/key-vault?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Application Insights](https://azure.microsoft.com/en-us/pricing/details/monitor?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
 
### <a name="costs-might-accrue-after-resource-deletion"></a>Az erőforrás törlése után felmerülhet a költségek

Ha töröl egy Azure Machine Learning munkaterületet a Azure Portal vagy az Azure CLI használatával, a következő erőforrások továbbra is létezni fog. A költségek továbbra is felhalmozottak, amíg Ön nem törli őket.

* Azure Container Registry
* Azure Block Blob Storage
* Key Vault
* Application Insights

A munkaterület és a függő erőforrások törléséhez használja az SDK-t:

```python
ws.delete(delete_dependent_resources=True)
```

Ha létrehoz egy Azure Kubernetes Service (AKS)-t a munkaterületen, vagy ha számítási erőforrásokat csatol a munkaterülethez, ezeket külön kell törölnie a [Azure Portal.](https://portal.azure.com)

### <a name="using-azure-prepayment-credit-with-azure-machine-learning"></a>Azure-előrefizetési kredit használata Azure Machine Learning

Az azure Azure Machine Learning előrefizetési kreditjével (korábbi neve pénzügyi kötelezettségvállalással) fizetheti ki a díjakat. Az Azure-előrefizetés azonban nem használható fel harmadik féltől származó termékek és szolgáltatások díjának kifizetése érdekében, beleértve a Azure Marketplace.


## <a name="create-budgets"></a>Költségvetések létrehozása

A költségek kezeléséhez [költségvetéseket](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) és [riasztásokat](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) hozhat létre, amelyek automatikusan figyelmeztetik az érdekelt feleket a rendellenes kiadásokról és a túlköltekezési kockázatokról. A riasztások a költségvetés és a költségek küszöbértékei alapján működnek. A költségvetések és riasztások Azure-előfizetések és -erőforráscsoportok számára vannak létrehozva, így egy általános költségfigyelési stratégia részeként hasznosak. 

A költségvetések adott erőforrásokra vagy szolgáltatásokra vonatkozó szűrőkkel is létrehozhatóak az Azure-ban, ha részletesebb monitorozást szeretne. A szűrőkkel biztosítható, hogy ne hozzon létre véletlenül olyan új erőforrásokat, amelyek többletköltséggel járnak. A költségvetés létrehozásakor elérhető szűrési lehetőségekkel kapcsolatos további információkért lásd: [Csoportosítási és szűrési beállítások.](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

## <a name="export-cost-data"></a>Költségadatok exportálása

A [költségadatokat egy tárfiókba](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) is exportálhatja. Ez akkor hasznos, ha vagy másoknak kell további adatelemzést végzette a költségek alapján. A pénzügyi csapatok például az Excel vagy a Power BI. A költségeket napi, heti vagy havi ütemezés szerint exportálhatja, és egyéni dátumtartományt állíthat be. Költségadatok exportálása a költségadatkészletek lekérésének ajánlott módja.

## <a name="other-ways-to-manage-and-reduce-costs-for-azure-machine-learning"></a>Egyéb módszerek a költségek kezelésére és csökkentésére a Azure Machine Learning

Ezekkel a tippekkel költségeket tartalmazhat a gépi tanulási számítási erőforrásokon.

### <a name="use-azure-machine-learning-compute-cluster-amlcompute"></a>Számítási Azure Machine Learning használata (AmlCompute)

A folyamatosan változó adatokhoz gyors és zökkenőmentes modellképzésre és újratanolásra van szükség a pontos modellek fenntartásához. A folyamatos betanítás azonban költséggel jár, különösen a GPU-k mélytanulásos modelljei esetében. 

Azure Machine Learning AmlCompute néven Azure Machine Learning felügyelt számítási fürtöt. Az AmlCompute számos GPU- és CPU-lehetőséget támogat. Az AmlCompute belsőleg üzemel az előfizetése nevében a Azure Machine Learning. Ugyanazt a nagyvállalati szintű biztonságot, megfelelőséget és irányítást biztosítja az Azure IaaS felhőméretű környezetekben is.

Mivel ezek a számítási készletek az Azure IaaS-infrastruktúrájának részétik, a betanítás az infrastruktúra többi részével azonos biztonsági és megfelelőségi követelményekkel helyezhető üzembe, skálázható és kezelhető.  Ezek az üzemelő példányok az előfizetésben fordulnak elő, és betartják a cégirányítási szabályokat. További információ az [Azure Machine Learning számításról.](how-to-create-attach-compute-cluster.md)

### <a name="configure-training-clusters-for-autoscaling"></a>Betanító fürtök konfigurálása automatikus skálázáshoz

A fürtök a számítási feladatok követelményei alapján való automatikus skálázása segít csökkenteni a költségeket, hogy csak a szükséges költségeket használja.

Az AmlCompute-fürtök a számítási feladatok alapján dinamikus méretezésre vannak tervezve. A fürt a konfigurált csomópontok maximális számára skálázható fel. Az egyes futtatásokkal a fürt felszabadítja a csomópontokat, és a megadott minimális csomópontszámra skáláz.

[!INCLUDE [min-nodes-note](../../includes/machine-learning-min-nodes.md)]

Azt is beállíthatja, hogy a csomópont mennyi ideig tétlen a leskálás előtt. Alapértelmezés szerint a leskálás előtti üresjárati idő 120 másodpercre van beállítva.

+ Ha kevesebb iteratív kísérletezést végez, csökkentse ezt az időt a költségek csökkentése érdekében.
+ Ha nagy mértékben iteratív fejlesztési/tesztelési kísérletezést végez, lehet, hogy növelnie kell az időt, hogy ne fizessen az állandó fel- és leméretezésért a betanító szkript vagy a környezet minden módosítása után.

Az AmlCompute-fürtök a Azure Portal-ben a változó számítási feladatok követelményeihez konfigurálhatóak az [AmlCompute SDK-osztály](/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute), az [AmlCompute CLI](/cli/azure/ext/azure-cli-ml/ml/computetarget/create#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute)és a REST API-k [használatával.](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/machinelearningservices/resource-manager/Microsoft.MachineLearningServices/stable)

```azurecli
az ml computetarget create amlcompute --name testcluster --vm-size Standard_NC6 --min-nodes 0 --max-nodes 5 --idle-seconds-before-scaledown 300
```

### <a name="set-quotas-on-resources"></a>Erőforrások kvótáinak beállítása

Az AmlCompute [kvóta-konfigurációval (vagy korlátmal) is jár.](how-to-manage-quotas.md#azure-machine-learning-compute) Ez a kvóta a virtuálisgép-család (például Dv2 sorozat, NCv3 sorozat) szerint van megszava, és régiónként eltérő az egyes előfizetések esetében. Az előfizetések kis alapértelmezett értékekkel kezdődnek a kezdéshez, de ezzel a beállítással szabályozhatja az előfizetésben elérhető Amlcompute-erőforrások mennyiségét. 

Konfigurálja [a munkaterület-szintű kvótát is a](how-to-manage-quotas.md#workspace-level-quotas)virtuálisgép-család szerint az előfizetésen belüli egyes munkaterületek esetében. Így részletesebben szabályozhatja az egyes munkaterületek költségeit, és korlátozhatja egyes virtuálisgép-családokat. 

A kvóták munkaterületi szinten való beállítását kezdje a [Azure Portal.](https://portal.azure.com)  Válasszon ki egy munkaterületet az előfizetésében, majd a bal **oldali panelen** válassza a Használat + kvóták lehetőséget. Ezután válassza a **Kvóták konfigurálása** lapot a kvóták megtekintéséhez. A kvóta beállításához jogosultságokra van szükség az előfizetés hatókörében, mivel ez egy olyan beállítás, amely több munkaterületet is érint.

### <a name="set-run-autotermination-policies"></a>Futtatás automatikus terminációs szabályzatának beállítása 

Bizonyos esetekben úgy kell konfigurálnia a betanítás futását, hogy az korlátozza az időtartamukat, vagy korán állítsa le őket. Ha például az alkalmazás beépített Azure Machine Learning hiperparaméterek finomhangolását vagy automatizált gépi tanulást használ.

Íme néhány lehetőség:
* Definiálnia kell egy nevű paramétert a RunConfigurationban a futtatás által a választott számításon (helyi vagy távoli felhőalapú számítás) meghosszabbítható `max_run_duration_seconds` maximális időtartam szabályozásához.
* [Hiperparaméter-finomhangoláshoz](how-to-tune-hyperparameters.md#early-termination)határozzon meg egy korai leállítási szabályzatot egy Median leállítási szabályzatból, egy mediánleállítási szabályzatból vagy egy csonkolási kiválasztási szabályzatból. A hiperparaméteres lekérdezések további vezérlése érdekében használjon olyan paramétereket, mint a `max_total_runs` vagy `max_duration_minutes` a .
* Az [automatizált gépi tanuláshoz](how-to-configure-auto-train.md#exit)a jelzővel állítson be hasonló leállító  `enable_early_stopping` szabályzatokat. A futtatás vagy a teljes kísérlet maximális időtartamának szabályozására olyan tulajdonságokat is használhat, mint a és `iteration_timeout_minutes` `experiment_timeout_minutes` a .

### <a name="use-low-priority-vms"></a><a id="low-pri-vm"></a> Alacsony prioritású virtuális gépek használata

Az Azure lehetővé teszi, hogy felesleges kihasználatlan kapacitást használjon virtuális Low-Priority virtuális gépekként a virtuálisgép-méretezési készletekben, a Batchben és a Machine Learning szolgáltatásban. Ezek a lefoglalások előre kiüríthetetlenek, de alacsonyabb áron állnak elő a dedikált virtuális gépekhez képest. Általában ajánlott virtuális gépeket Low-Priority Batch számítási feladatokhoz. Akkor is érdemes használni őket, ha a megszakítások helyreállíthatók újraküldetésekkel (Batch-következtetéshez) vagy újraindításokkal (ellenőrzőpontokkal való mélytanulás esetén).

Low-Priority virtuális gépek egyetlen kvótával vannak elválasztva a dedikált kvótaértéktől, amely a virtuálisgép-család szerint van megszabadva. További [információ az AmlCompute-kvótákról.](how-to-manage-quotas.md)

 Low-Priority virtuális gépek nem működnek számítási példányok esetén, mivel támogatniuk kell az interaktív jegyzetfüzet-felületeket.

### <a name="use-reserved-instances"></a>Fenntartott példányok használata

A számítási erőforrások megtakarításának másik módja az Azure Reserved VM Instance. Ezzel az ajánlatkal egy- vagy hároméves feltételeket vállal. Ezek a kedvezmények a használat alapján fizetett árak 72%-a között vannak, és közvetlenül a havi Azure-számlára érvényesek.

Azure Machine Learning Compute eredendően támogatja a fenntartott példányokat. Ha egy egy- vagy hároméves fenntartott példányt vásárol, automatikusan kedvezményt alkalmazunk a Azure Machine Learning számításra.


## <a name="next-steps"></a>Következő lépések

- [Megtudhatja, hogyan optimalizálhatja felhővel való befektetését a Azure Cost Management.](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- További információ a költségek költségelemzéssel [való kezeléséről.](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Ismerje meg, hogyan [előzheti meg a váratlan költségeket.](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Vegyen részt [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) oktató kurzusán.
