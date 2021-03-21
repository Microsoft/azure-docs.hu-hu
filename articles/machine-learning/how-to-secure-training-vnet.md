---
title: Biztonságos képzési környezetek virtuális hálózatokkal
titleSuffix: Azure Machine Learning
description: Egy elkülönített Azure-Virtual Network segítségével biztonságossá teheti Azure Machine Learning képzési környezetét.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 07/16/2020
ms.custom: contperf-fy20q4, tracking-python, contperf-fy21q1
ms.openlocfilehash: 81a6a6aeb826ba4a61fd4f0a4abdb63fe3674bc4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103573727"
---
# <a name="secure-an-azure-machine-learning-training-environment-with-virtual-networks"></a>Azure Machine Learning képzési környezet biztonságossá tétele virtuális hálózatokkal

Ebből a cikkből megtudhatja, hogyan védheti meg a képzési környezeteket virtuális hálózattal Azure Machine Learningban.

Ez a cikk egy öt részes sorozat harmadik része, amely végigvezeti egy Azure Machine Learning munkafolyamat biztonságossá tételének lépésein. Javasoljuk, hogy először olvassa el az első [részt: VNet – áttekintés](how-to-network-security-overview.md) az általános architektúra megismeréséhez. 

Tekintse meg a sorozat egyéb cikkeit:

[1. VNet – áttekintés](how-to-network-security-overview.md)  >  [2. A](how-to-secure-workspace-vnet.md)  >  **3. munkaterület védelme Gondoskodjon a 4. képzési környezet védelméről**  >  [. Gondoskodjon az 5. következtetési környezet védelméről](how-to-secure-inferencing-vnet.md)   >  [. A Studio funkcióinak engedélyezése](how-to-enable-studio-virtual-network.md)

Ebből a cikkből megtudhatja, hogyan védheti meg a virtuális hálózatok következő betanítási erőforrásait:
> [!div class="checklist"]
> - Számítási fürt Azure Machine Learning
> - Azure Machine Learning számítási példány
> - Azure Databricks
> - Virtuális gép
> - HDInsight-fürt

## <a name="prerequisites"></a>Előfeltételek

+ Olvassa el a [hálózati biztonság áttekintése című](how-to-network-security-overview.md) cikket a gyakori virtuális hálózati forgatókönyvek és az általános virtuális hálózati architektúra megismeréséhez.

+ Meglévő virtuális hálózat és alhálózat a számítási erőforrásokkal való használatra.

+ Ha erőforrásokat szeretne telepíteni egy virtuális hálózatba vagy alhálózatba, a felhasználói fióknak engedéllyel kell rendelkeznie a következő műveletekhez az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) szolgáltatásban:

    - "Microsoft. Network/virtualNetworks/JOIN/Action" a virtuális hálózati erőforráson.
    - "Microsoft. Network/virtualNetworks/subnet/JOIN/Action" az alhálózati erőforráson.

    A hálózatkezeléssel rendelkező Azure RBAC kapcsolatos további információkért tekintse meg a [hálózatkezelés beépített szerepköreit](../role-based-access-control/built-in-roles.md#networking) .


## <a name="compute-clusters--instances"></a><a name="compute-instance"></a>Számítási fürtök & példányok 

Ha [felügyelt Azure Machine learning __számítási célt__](concept-compute-target.md#azure-machine-learning-compute-managed) vagy [Azure Machine learning számítási __példányt__](concept-compute-instance.md) szeretne használni egy virtuális hálózaton, a következő hálózati követelményeknek kell teljesülniük:

> [!div class="checklist"]
> * A virtuális hálózatnak ugyanabban az előfizetésben és régióban kell lennie, mint a Azure Machine Learning munkaterületnek.
> * A számítási példányhoz vagy fürthöz megadott alhálózatnak elegendő, nem hozzárendelt IP-címmel kell rendelkeznie a célként kijelölt virtuális gépek számának kielégítéséhez. Ha az alhálózat nem rendelkezik elegendő nem hozzárendelt IP-címmel, a számítási fürt részlegesen le lesz foglalva.
> * Ellenőrizze, hogy a virtuális hálózat előfizetése vagy erőforráscsoport biztonsági szabályzatai vagy zárolásai korlátozzák-e az engedélyeket a virtuális hálózat kezeléséhez. Ha a virtuális hálózat védelmét úgy tervezi, hogy korlátozza a forgalmat, hagyjon meg néhány portot a számítási szolgáltatás számára. További információ: a [szükséges portok](#mlcports) szakasz.
> * Ha egy virtuális hálózatban több számítási példányt vagy fürtöt szeretne létrehozni, előfordulhat, hogy egy vagy több erőforrásra vonatkozó kvóta-növelést kell kérnie.
> * Ha a munkaterülethez tartozó Azure Storage-fiók (ok) is védett virtuális hálózatban, akkor a Azure Machine Learning számítási példánnyal vagy fürttel azonos virtuális hálózatban kell lenniük. 
> * A számítási példányok Jupyter működéséhez győződjön meg arról, hogy a webes szoftvercsatorna-kommunikáció nincs letiltva. Győződjön meg arról, hogy a hálózat engedélyezi a WebSocket-kapcsolatokat a *. instances.azureml.net és a *. instances.azureml.ms. 
> * Ha a számítási példány egy privát kapcsolati munkaterületen van üzembe helyezve, akkor csak a virtuális hálózaton belülről lehet hozzáférni. Ha egyéni DNS-vagy hosts fájlt használ, adjon hozzá egy bejegyzést a `<instance-name>.<region>.instances.azureml.ms` munkaterület privát végpontjának magánhálózati IP-címéhez. További információ az [Egyéni DNS-](./how-to-custom-dns.md) cikkben található.
> * A számítási fürt/példány telepítéséhez használt alhálózatot nem lehet delegálni más szolgáltatásokhoz, például az ACI-hoz
> * A virtuális hálózati szolgáltatás végpont-házirendjei nem működnek számítási fürt/példány rendszertárolói fiókok esetén

    
> [!TIP]
> A Machine Learning számítási példány vagy fürt automatikusan további hálózati erőforrásokat foglal le __a virtuális hálózatot tartalmazó erőforráscsoporthoz__. A szolgáltatás minden számítási példányhoz vagy fürthöz a következő erőforrásokat foglalja le:
> 
> * Egy hálózati biztonsági csoport
> * Egy nyilvános IP-cím. Ha rendelkezik olyan Azure-házirenddel, amely tiltja a nyilvános IP-címek létrehozását, akkor a fürt/példányok telepítése sikertelen lesz.
> * Egy Load Balancer
> 
> Fürtök esetében ezeket az erőforrásokat a rendszer minden alkalommal törli (és újból létrehozza), amikor a fürt 0 csomópontra van lebontva, azonban az erőforrásokat a rendszer a példány teljes törlése előtt megtartja (a Leállítás nem távolítja el az erőforrásokat). 
> Ezekre az erőforrásokra az előfizetésben meghatározott [erőforráskvóták](../azure-resource-manager/management/azure-subscription-service-limits.md) vonatkoznak. Ha a virtuális hálózati erőforráscsoport zárolva van, akkor a számítási fürt/példány törlése sikertelen lesz. A terheléselosztó csak a számítási fürt/példány törlése után törölhető. Gondoskodjon arról is, hogy ne legyenek olyan Azure-szabályzatok, amelyek tiltják a hálózati biztonsági csoportok létrehozását.


### <a name="required-ports"></a><a id="mlcports"></a> Szükséges portok

Ha a virtuális hálózat védelmét úgy tervezi, hogy korlátozza a nyilvános internetre irányuló hálózati forgalmat, engedélyeznie kell a bejövő kommunikációt a Azure Batch szolgáltatástól.

A Batch szolgáltatás hálózati biztonsági csoportokat (NSG) helyez üzembe a virtuális gépekhez csatolt hálózati adapterek (NIC-EK) szintjén. Ezek az NSG-k automatikusan konfigurálnak bejövő és kimenő szabályokat a következő forgalom engedélyezéséhez:

- Bejövő TCP-forgalom a 29876-es és a 29877-es portokon a __BatchNodeManagement__ __szolgáltatási címkéjén__ . A portok közötti adatforgalom titkosítva van, és a Azure Batch használja a Scheduler/Node kommunikációhoz.

    ![A BatchNodeManagement szolgáltatás címkéjét használó bejövő szabály](./media/how-to-enable-virtual-network/batchnodemanagement-service-tag.png)

- Választható Bejövő TCP-forgalom a 22-es porton a távoli hozzáférés engedélyezéséhez. Csak akkor használja ezt a portot, ha az SSH használatával szeretne csatlakozni a nyilvános IP-címen.

- Kimenő forgalom bármilyen porton keresztül a virtuális hálózathoz.

- Kimenő forgalom bármilyen porton keresztül az internetre.

- A számítási példány bejövő TCP-forgalma a 44224-as porton a __AzureMachineLearning__ __szolgáltatási címkéjén__ . A porton keresztüli forgalom titkosítva van, és az Azure Machine Learning használja a számítási példányokon futó alkalmazásokkal folytatott kommunikációhoz.

> [!IMPORTANT]
> Körültekintően járjon el a bejövő vagy kimenő szabályok módosításakor és hozzáadásakor a Batch által konfigurált NSG-kben. Ha egy NSG blokkolja a számítási csomópontok felé irányuló kommunikációt, a számítási szolgáltatás nem használhatóra állítja a számítási csomópontok állapotát.
>
> Nem kell megadnia a NSG az alhálózat szintjén, mert a Azure Batch szolgáltatás konfigurálja a saját NSG. Ha azonban a Azure Machine Learning számítási feltételt tartalmazó alhálózat NSG vagy tűzfallal rendelkezik, akkor a korábban felsorolt forgalmat is engedélyeznie kell.

A Azure Portal NSG-szabályának konfigurációja az alábbi képeken látható:

:::image type="content" source="./media/how-to-enable-virtual-network/amlcompute-virtual-network-inbound.png" alt-text="A Machine Learning Computehoz tartozó bejövő NSG-szabályok" border="true":::



![Machine Learning Computehoz tartozó bejövő NSG-szabályok](./media/how-to-enable-virtual-network/experimentation-virtual-network-outbound.png)

### <a name="limit-outbound-connectivity-from-the-virtual-network"></a><a id="limiting-outbound-from-vnet"></a> A virtuális hálózat kimenő kapcsolatának korlátozása

Ha nem szeretné az alapértelmezett kimenő szabályokat használni, és korlátozni szeretné a virtuális hálózat kimenő hozzáférését, kövesse az alábbi lépéseket:

- A kimenő internetkapcsolat megtagadása a NSG szabályok használatával.

- __Számítási példány__ vagy __számítási fürt__ esetén korlátozza a kimenő forgalmat a következő elemekre:
   - Azure Storage a __Storage. RegionName__ __szolgáltatási címkéjével__ . Ahol az `{RegionName}` egy Azure-régió neve.
   - Azure Container Registry a __AzureContainerRegistry. RegionName__ __szolgáltatási címkéje__ segítségével. Ahol az `{RegionName}` egy Azure-régió neve.
   - Azure Machine Learning a __AzureMachineLearning__ __szolgáltatási címkéjének__ használatával
   - Azure Resource Manager a __AzureResourceManager__ __szolgáltatási címkéjének__ használatával
   - Azure Active Directory a __AzureActiveDirectory__ __szolgáltatási címkéjének__ használatával

A Azure Portal NSG-szabályának konfigurációja a következő képen látható:

[![A Machine Learning Compute kimenő NSG szabályai](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png)](./media/how-to-enable-virtual-network/limited-outbound-nsg-exp.png#lightbox)

> [!NOTE]
> Ha azt tervezi, hogy a Microsoft által biztosított alapértelmezett Docker-rendszerképeket használja, és engedélyezi a felhasználó által felügyelt függőségeket, akkor a következő __szolgáltatási címkéket__ is használnia kell:
>
> * __MicrosoftContainerRegistry__
> * __AzureFrontDoor.FirstParty__
>
> Erre a konfigurációra akkor van szükség, ha az alábbi kódrészletekhez hasonló kóddal rendelkezik a betanítási szkriptek részeként:
>
> __RunConfig-képzés__
> ```python
> # create a new runconfig object
> run_config = RunConfiguration()
> 
> # configure Docker 
> run_config.environment.docker.enabled = True
> # For GPU, use DEFAULT_GPU_IMAGE
> run_config.environment.docker.base_image = DEFAULT_CPU_IMAGE 
> run_config.environment.python.user_managed_dependencies = True
> ```
>
> __Kalkulátor-képzés__
> ```python
> est = Estimator(source_directory='.',
>                 script_params=script_params,
>                 compute_target='local',
>                 entry_script='dummy_train.py',
>                 user_managed=True)
> run = exp.submit(est)
> ```

### <a name="forced-tunneling"></a>Alagúthasználat kényszerítése

Ha [kényszerített bújtatást](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) használ Azure Machine learning számítási feladatokkal, akkor engedélyeznie kell a kommunikációt a nyilvános internettel a számítási erőforrást tartalmazó alhálózatból. Ez a kommunikáció a feladatütemezés és az Azure Storage elérésére szolgál.

Ez kétféleképpen valósítható meg:

* [Virtual Network NAT](../virtual-network/nat-overview.md)használata. A NAT-átjáró kimenő internetkapcsolatot biztosít a virtuális hálózat egy vagy több alhálózatához. További információ: [virtuális hálózatok tervezése NAT Gateway-erőforrásokkal](../virtual-network/nat-gateway-resource.md).

* Adja hozzá a [felhasználó által megadott útvonalakat (UDR)](../virtual-network/virtual-networks-udr-overview.md) a számítási erőforrást tartalmazó alhálózathoz. Hozzon létre egy UDR minden olyan IP-címhez, amelyet a Azure Batch szolgáltatás használ azon a régióban, ahol az erőforrásai léteznek. Ezek a UDR lehetővé teszik a Batch szolgáltatás számára a feladatütemezés számítási csomópontjaival való kommunikációját. Adja hozzá a Azure Machine Learning szolgáltatáshoz tartozó IP-címet is, mivel ez szükséges a számítási példányokhoz való hozzáféréshez. A Azure Machine Learning szolgáltatás IP-címének hozzáadásakor hozzá kell adnia az IP-címet mind az __elsődleges,__ mind a másodlagos Azure-régióhoz. Az elsődleges régió, ahol a munkaterület található.

    A másodlagos régió megtalálásához tekintse meg az [üzletmenet folytonosságának biztosítása & a vész-helyreállítás az Azure párosított régiók használatával](../best-practices-availability-paired-regions.md#azure-regional-pairs)című témakört. Ha például az Azure Machine Learning-szolgáltatás az USA 2. keleti régiójában található, a másodlagos régió az USA középső régiója. 

    A Batch szolgáltatás és a Azure Machine Learning szolgáltatás IP-címeinek listájának megjelenítéséhez használja a következő módszerek egyikét:

    * Töltse le az [Azure IP-címtartományok és a szolgáltatás címkéit](https://www.microsoft.com/download/details.aspx?id=56519) , és keresse meg a és a fájlt `BatchNodeManagement.<region>` `AzureMachineLearning.<region>` , ahol `<region>` az az Azure-régió.

    * Az adatok letöltéséhez használja az [Azure CLI](/cli/azure/install-azure-cli) -t. Az alábbi példa letölti az IP-cím adatait, és kiszűri az USA 2. keleti régiójában (elsődleges) és az USA középső régiójában (másodlagos) található adatokat:

        ```azurecli-interactive
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
        # Get primary region IPs
        az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
        # Get secondary region IPs
        az network list-service-tags -l "Central US" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='centralus']"
        ```

        > [!TIP]
        > Ha az USA-Virginia, US-Arizona régiókat vagy a kínai – Kelet-2 régiót használja, ezek a parancsok nem adnak vissza IP-címet. Ehelyett használja az alábbi hivatkozások egyikét az IP-címek listájának letöltéséhez:
        >
        > * [Azure IP-címtartományok és szolgáltatás-címkék Azure Government](https://www.microsoft.com/download/details.aspx?id=57063)
        > * [Azure IP-címtartományok és szolgáltatás-címkék az Azure China-hoz](https://www.microsoft.com//download/details.aspx?id=57062)
    
    A UDR hozzáadásakor adja meg az útvonalat az egyes kapcsolódó batch IP-címek előtagjaként, és állítsa be a __következő ugrás típusát__ az __Internet__ értékre. Az alábbi képen látható példa erre a UDR mutat a Azure Portalban:

    ![UDR – példa a címek előtagjaként való használatra](./media/how-to-enable-virtual-network/user-defined-route.png)

    > [!IMPORTANT]
    > Az IP-címek idővel változhatnak.

    Az Ön által meghatározott UDR kívül az Azure Storage-ba irányuló kimenő forgalmat a helyszíni hálózati berendezésen keresztül kell engedélyezni. A forgalom URL-címei a következő formákban találhatók: `<account>.table.core.windows.net` , `<account>.queue.core.windows.net` és `<account>.blob.core.windows.net` . 

    További információ: [Azure batch készlet létrehozása egy virtuális hálózaton](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling).

### <a name="create-a-compute-cluster-in-a-virtual-network"></a>Számítási fürt létrehozása egy virtuális hálózaton

Machine Learning Compute-fürt létrehozásához kövesse az alábbi lépéseket:

1. Jelentkezzen be [Azure Machine learning studióba](https://ml.azure.com/), majd válassza ki az előfizetést és a munkaterületet.

1. Válassza a bal oldali __számítás__ lehetőséget.

1. Válassza ki a __betanítási fürtök__ lehetőséget a középpontban, majd válassza a elemet __+__ .

1. Az __új betanítási fürt__ párbeszédpanelen bontsa ki a __Speciális beállítások__ szakaszt.

1. Ha ezt a számítási erőforrást virtuális hálózat használatára szeretné konfigurálni, hajtsa végre a következő műveleteket a __virtuális hálózat konfigurálása__ szakaszban:

    1. Az __erőforráscsoport__ legördülő listában válassza ki a virtuális hálózatot tartalmazó erőforráscsoportot.
    1. A __virtuális hálózat__ legördülő listában válassza ki azt a virtuális hálózatot, amely az alhálózatot tartalmazza.
    1. Az __alhálózat__ legördülő listában válassza ki a használni kívánt alhálózatot.

   ![Machine Learning Compute virtuális hálózati beállításai](./media/how-to-enable-virtual-network/amlcompute-virtual-network-screen.png)

Machine Learning Compute-fürtöt a Azure Machine Learning SDK használatával is létrehozhat. A következő kód egy új Machine Learning Compute fürtöt hoz létre `default` egy nevű virtuális hálózat alhálózatában `mynetwork` :

```python
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

# The Azure virtual network name, subnet, and resource group
vnet_name = 'mynetwork'
subnet_name = 'default'
vnet_resourcegroup_name = 'mygroup'

# Choose a name for your CPU cluster
cpu_cluster_name = "cpucluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print("Found existing cpucluster")
except ComputeTargetException:
    print("Creating new cpucluster")

    # Specify the configuration for the new cluster
    compute_config = AmlCompute.provisioning_configuration(vm_size="STANDARD_D2_V2",
                                                           min_nodes=0,
                                                           max_nodes=4,
                                                           vnet_resourcegroup_name=vnet_resourcegroup_name,
                                                           vnet_name=vnet_name,
                                                           subnet_name=subnet_name)

    # Create the cluster with the specified name and configuration
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

    # Wait for the cluster to be completed, show the output log
    cpu_cluster.wait_for_completion(show_output=True)
```

A létrehozási folyamat befejeződése után a modellt egy kísérletben a fürt használatával kell betanítani. További információkért lásd: [számítási cél kiválasztása és használata képzéshez](how-to-set-up-training-targets.md).

[!INCLUDE [low-pri-note](../../includes/machine-learning-low-pri-vm.md)]

### <a name="access-data-in-a-compute-instance-notebook"></a>Adatok elérése számítási példányok jegyzetfüzetben

Ha egy Azure számítási példányon jegyzetfüzeteket használ, gondoskodnia kell arról, hogy a jegyzetfüzet az adatokkal azonos virtuális hálózat és alhálózat mögötti számítási erőforráson fusson. 

A számítási példányt úgy **kell konfigurálni,** hogy az a  >  **virtuális hálózat konfigurálása** során a létrehozás során azonos virtuális hálózatban legyen. Meglévő számítási példányt nem adhat hozzá virtuális hálózathoz.

## <a name="azure-databricks"></a>Azure Databricks

Ha Azure Databricks szeretne használni a munkaterülettel rendelkező virtuális hálózaton, a következő követelményeknek kell teljesülniük:

> [!div class="checklist"]
> * A virtuális hálózatnak ugyanabban az előfizetésben és régióban kell lennie, mint a Azure Machine Learning munkaterületnek.
> * Ha a munkaterülethez tartozó Azure Storage-fiók (ok) is védett virtuális hálózatban, akkor a Azure Databricks-fürttel megegyező virtuális hálózatban kell lenniük.
> * Az Azure Databricks által használt __databricks-__ és __databricks-__ alhálózatok mellett a virtuális hálózathoz létrehozott __alapértelmezett__ alhálózat is szükséges.

A Azure Databricks virtuális hálózattal való használatáról a [Azure Databricks üzembe helyezése az Azure-Virtual Network](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)című témakörben olvashat bővebben.

<a id="vmorhdi"></a>

## <a name="virtual-machine-or-hdinsight-cluster"></a>Virtuális gép vagy HDInsight-fürt

> [!IMPORTANT]
> A Azure Machine Learning csak az Ubuntut futtató virtuális gépeket támogatja.

Ebből a szakaszból megtudhatja, hogyan használhatja a virtuális gépet vagy az Azure HDInsight-fürtöt egy virtuális hálózatban a munkaterülettel.

### <a name="create-the-vm-or-hdinsight-cluster"></a>A virtuális gép vagy a HDInsight-fürt létrehozása

Hozzon létre egy virtuális GÉPET vagy HDInsight-fürtöt a Azure Portal vagy az Azure CLI használatával, és helyezze üzembe a fürtöt egy Azure-beli virtuális hálózaton. További információért tekintse át a következő cikkeket:
* [Azure Virtual Networks létrehozása és kezelése Linux rendszerű virtuális gépekhez](../virtual-machines/linux/tutorial-virtual-network.md)

* [HDInsight kiterjesztése Azure Virtual Network használatával](../hdinsight/hdinsight-plan-virtual-network-deployment.md)

### <a name="configure-network-ports"></a>Hálózati portok konfigurálása 

Engedélyezze Azure Machine Learning a virtuális gép vagy fürt SSH-portjával való kommunikációt, konfiguráljon egy forrásoldali bejegyzést a hálózati biztonsági csoport számára. Az SSH-port általában a 22-es port. A forrásból érkező forgalom engedélyezéséhez hajtsa végre a következő műveleteket:

1. A __forrás__ legördülő listában válassza a __szolgáltatás címkéje__ elemet.

1. A __forrás szolgáltatás címkéje__ legördülő listában válassza a __AzureMachineLearning__ lehetőséget.

    ![Bejövő szabályok a virtuális hálózaton belüli virtuálisgép-vagy HDInsight-fürtök kísérletezéséhez](./media/how-to-enable-virtual-network/experimentation-virtual-network-inbound.png)

1. A __forrás porttartomány-tartományok__ legördülő listában válassza a elemet __*__ .

1. A __cél__ legördülő listában válassza a __bármelyik__ lehetőséget.

1. A __cél porttartomány-tartományok__ legördülő listában válassza a __22__ lehetőséget.

1. A __protokoll__ területen válassza __a bármelyik__ lehetőséget.

1. A __művelet__ területen válassza az __Engedélyezés__ lehetőséget.

Tartsa meg a hálózati biztonsági csoport alapértelmezett kimenő szabályait. További információ: [biztonsági csoportok](../virtual-network/network-security-groups-overview.md#default-security-rules)alapértelmezett biztonsági szabályai.

Ha nem szeretné használni az alapértelmezett kimenő szabályokat, és szeretné korlátozni a virtuális hálózat kimenő hozzáférését, tekintse meg a [Kimenő kapcsolat korlátozása a virtuális hálózattal](#limiting-outbound-from-vnet) szakaszt.

### <a name="attach-the-vm-or-hdinsight-cluster"></a>A virtuális gép vagy a HDInsight-fürt csatlakoztatása

Csatlakoztassa a virtuális gépet vagy a HDInsight-fürtöt a Azure Machine Learning munkaterülethez. További információ: [számítási célok beállítása a modell betanításához](how-to-set-up-training-targets.md).

## <a name="next-steps"></a>Következő lépések

Ez a cikk egy öt részből álló virtuális hálózati sorozat harmadik része. A virtuális hálózatok biztonságossá tételéhez tekintse meg a cikkek további részeit:

* [1. rész: a Virtual Network áttekintése](how-to-network-security-overview.md)
* [2. rész: a munkaterület erőforrásainak védelme](how-to-secure-workspace-vnet.md)
* [4. rész: a következtetési környezet biztonságossá tétele](how-to-secure-inferencing-vnet.md)
* [5. rész: a Studio funkcióinak engedélyezése](how-to-enable-studio-virtual-network.md)

Tekintse meg az [Egyéni DNS](how-to-custom-dns.md) használata névfeloldáshoz című cikket is.