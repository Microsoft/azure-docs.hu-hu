---
title: Metrikai riasztások Azure Monitorről tárolók számára
description: Ez a cikk a Azure Monitor for containers nyilvános előzetes verziójában elérhető javasolt metrikai riasztásokat tekinti át.
ms.topic: conceptual
ms.date: 10/28/2020
ms.openlocfilehash: 59c8d7b58809c981130d2ce92406fb5b1ce146ff
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100618798"
---
# <a name="recommended-metric-alerts-preview-from-azure-monitor-for-containers"></a>Ajánlott metrikai riasztások (előzetes verzió) Azure Monitorről tárolók számára

Ha riasztást szeretne kapni a Rendszererőforrásokkal kapcsolatos problémákról, amikor a maximális keresletet tapasztalják, és a közeli kapacitást futtatják, a Azure Monitor a tárolók esetében a Azure Monitor naplókban tárolt teljesítményadatok alapján kell létrehoznia a naplózási riasztást. A tárolók Azure Monitor mostantól nyilvános előzetes verzióban elérhető, előre konfigurált metrikus riasztási szabályokat is tartalmaz az AK-hoz és az Azure arc-kompatibilis Kubernetes-fürthöz.

Ez a cikk a tapasztalatok áttekintését és útmutatást nyújt a riasztási szabályok konfigurálásához és kezeléséhez.

Ha nem ismeri a Azure Monitor riasztásokat, a Kezdés előtt tekintse meg [a Microsoft Azure riasztások áttekintése](../platform/alerts-overview.md) című témakört. A metrikus riasztásokkal kapcsolatos további tudnivalókért tekintse meg [a metrikus riasztások Azure monitorban](../alerts/alerts-metric-overview.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, erősítse meg a következőket:

* Az egyéni metrikák csak az Azure-régiók egy részhalmazában érhetők el. A támogatott régiók listája a [támogatott régiókban](../essentials/metrics-custom-overview.md#supported-regions)van dokumentálva.

* A metrikus riasztások támogatásához és a további mérőszámok bevezetéséhez az ügynök minimálisan szükséges verziójának **MCR.microsoft.com/azuremonitor/containerinsights/ciprod:ciprod05262020** kell lennie az Azure arc-kompatibilis Kubernetes-fürthöz tartozó AK-hoz és **MCR.microsoft.com/azuremonitor/containerinsights/ciprod:ciprod09252020** -hez.

    Annak ellenőrzéséhez, hogy a fürt az ügynök újabb verzióját futtatja-e, a következők közül választhat:

    * Futtassa a parancsot: `kubectl describe <omsagent-pod-name> --namespace=kube-system` . A visszaadott állapotban jegyezze fel a kimenet omsagent található **rendszerkép** *a következőben* :. 
    * A **csomópontok** lapon válassza ki a fürtcsomópont csomópontot, majd a jobb oldalon a **Tulajdonságok** ablaktáblán jegyezze fel az értéket az **ügynök képcímkéje** területen.

    Az AK-hoz megadott értéknek **ciprod05262020** vagy újabb verziójúnak kell lennie. Az Azure arc-kompatibilis Kubernetes-fürtön látható értéknek **ciprod09252020** vagy újabb verziójúnak kell lennie. Ha a fürt rendelkezik egy régebbi verzióval, tekintse meg a [Hogyan lehet frissíteni a Azure monitor for containers Agent](container-insights-manage-agent.md#upgrade-agent-on-aks-cluster) című témakört a legújabb verzió beszerzésének lépéseihez.

    Az ügynök kiadásával kapcsolatos további információkért lásd az [ügynök kiadási előzményei](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)című témakört. A metrikák gyűjtésének ellenőrzéséhez használhatja a Azure Monitor metrikák Explorert, és ellenőrizheti, hogy a **metrikai névtér** tartalmazza-e az **észlelt** adatokat. Ha igen, megkezdheti a riasztások beállítását. Ha nem jelenik meg a begyűjtött metrikák, a fürtszolgáltatási tag vagy az MSI nem rendelkezik a szükséges engedélyekkel. Annak ellenőrzéséhez, hogy az SPN vagy az MSI a **figyelési metrikák közzétevői** szerepkör tagja-e, kövesse a [fürt frissítése az Azure CLI](container-insights-update-metrics.md#upgrade-per-cluster-using-azure-cli) -vel című szakaszban ismertetett lépéseket a szerepkör-hozzárendelés megerősítéséhez és beállításához.

## <a name="alert-rules-overview"></a>Riasztási szabályok áttekintése

A Azure Monitor for containers szolgáltatással kapcsolatos riasztásokhoz a következő metrikai riasztások tartoznak az AK-hoz és az Azure arc-kompatibilis Kubernetes-fürtökhöz:

|Név| Leírás |Alapértelmezett küszöbérték |
|----|-------------|------------------|
|Tároló processzorának átlagos kihasználtsága (%) |Kiszámítja a tárolón felhasznált átlagos CPU-t.|Ha a tároló átlagos CPU-használata meghaladja a 95%-ot.| 
|Tároló átlagos munkakészletének memóriája (%) |Kiszámítja egy tároló átlagos munkakészlet-memóriáját.|Ha a munkakészletek átlagos kihasználtsága a tárolók száma meghaladja a 95%-ot. |
|Average CPU % (Átlagos CPU-használat %-ban) |Kiszámítja a csomóponton felhasznált átlagos CPU-t. |Ha a csomópontok átlagos CPU-kihasználtsága nagyobb, mint 80% |
|Lemez átlagos kihasználtsága (%) |Kiszámítja egy csomópont átlagos lemezterület-használatát.|Ha a csomópont lemezhasználata nagyobb, mint 80%. |
|Állandó használat átlagos kihasználtsága (%) |Kiszámítja az átlagos PV-használatot Pod-ként. |Ha a hüvelyek átlagos PV-használata meghaladja a 80%-ot.|
|Munkakészlet átlagos memóriája (%) |Kiszámítja egy csomópont átlagos munkakészletének memóriáját. |Ha egy csomópont átlagos munkakészlet-memóriája nagyobb, mint 80%. |
|Tárolók számának újraindítása |Kiszámítja az újraindítási tárolók számát. | Ha a tároló újraindítása meghaladja a 0-nál nagyobb értéket. |
|Sikertelen Pod-számok |Kiszámítja, hogy valamelyik Pod-állapot hibás állapotban van-e.|Ha a hibás állapotú hüvelyek száma nagyobb, mint 0. |
|Csomópont – nem feltaposás állapota |Kiszámítja, hogy bármely csomóponton van-e a nem-futófelület állapotban.|Ha a több ponttal rendelkező csomópontok száma nagyobb, mint 0. |
|A bácsi megölte tárolókat |Kiszámítja a bácsi által leölt tárolók számát. |Ha több bácsi által leölt tároló nagyobb, mint 0. |
|Hüvelyre kész% |Kiszámítja a hüvelyek átlagos készenléti állapotát. |Ha a hüvely Ready állapota kevesebb, mint 80%.|
|Befejezett feladatok száma |Kiszámítja a több mint hat órája Befejezett feladatok számát. |Ha a hat óránál régebbi elavult feladatok száma nagyobb, mint 0.|

Az összes riasztási szabályhoz általános tulajdonságok tartoznak:

* Minden riasztási szabály metrikus alapú.

* Alapértelmezés szerint minden riasztási szabály le van tiltva.

* Az összes riasztási szabályt percenként egyszer értékeli ki a rendszer, és az utolsó 5 percre visszamenőleg visszatekint.

* A riasztási szabályok alapértelmezés szerint nem rendelkeznek hozzárendelt műveleti csoporttal. A riasztáshoz hozzáadhat egy [műveleti csoportot](../alerts/action-groups.md) vagy egy meglévő műveleti csoport kiválasztásával vagy egy új műveleti csoport létrehozásával a riasztási szabály szerkesztése közben.

* A riasztási szabályok küszöbértékét közvetlenül is módosíthatja. A küszöbérték módosítása előtt azonban tekintse meg az egyes riasztási szabályokban megadott útmutatást.

A következő riasztási alapú metrikák egyedi viselkedési jellemzőkkel rendelkeznek a többi metrikához képest:

* az *completedJobsCount* metrikát csak akkor kell elküldeni, ha több mint hat órája elvégezte a feladatokat.

* a *containerRestartCount* metrikát csak akkor küldik el, ha a tárolók újraindulnak.

* a *oomKilledContainerCount* metrikát csak akkor küldik el, ha a rendszer a bácsi által leölt tárolókat.

* a *cpuExceededPercentage*, a *MemoryRssExceededPercentage* és a *memoryWorkingSetExceededPercentage* mérőszámok akkor lesznek elküldése, ha a processzor, a memória RSS-és a memória-munkakészletének értéke meghaladja a beállított küszöbértéket (az alapértelmezett küszöbérték 95%). Ezek a küszöbértékek kizárólag a vonatkozó riasztási szabályhoz megadott riasztási feltétel küszöbértékét jelentik. Ha ezeket a metrikákat szeretné összegyűjteni és elemezni a [metrikák Intézőből](../essentials/metrics-getting-started.md), javasoljuk, hogy a küszöbértéket a riasztási küszöbértéknél alacsonyabb értékre konfigurálja. A tároló erőforrás-kihasználtsági küszöbértékek gyűjtési beállításaival kapcsolatos konfiguráció felülbírálható a szakasz ConfigMaps fájljában `[alertable_metrics_configuration_settings.container_resource_utilization_thresholds]` . A ConfigMap konfigurációs fájljának konfigurálásával kapcsolatos részletekért tekintse meg a [riasztásos metrikák konfigurálása című ConfigMaps](#configure-alertable-metrics-in-configmaps) .

* a rendszer akkor továbbítja az *pvUsageExceededPercentage* metrikát, ha a kötet állandó kihasználtságának százalékos aránya meghaladja a beállított küszöbértéket (az alapértelmezett küszöbérték 60%). Ez a küszöbérték kizárólag a megfelelő riasztási szabályhoz megadott riasztási feltétel küszöbértékét határozza meg. Ha ezeket a metrikákat szeretné összegyűjteni és elemezni a [metrikák Intézőből](../essentials/metrics-getting-started.md), javasoljuk, hogy a küszöbértéket a riasztási küszöbértéknél alacsonyabb értékre konfigurálja. Az állandó kötet-kihasználtsági küszöbértékek gyűjtési beállításaival kapcsolatos konfiguráció felülbírálható a szakasz ConfigMaps fájljában `[alertable_metrics_configuration_settings.pv_utilization_thresholds]` . A ConfigMap konfigurációs fájljának konfigurálásával kapcsolatos részletekért tekintse meg a [riasztásos metrikák konfigurálása című ConfigMaps](#configure-alertable-metrics-in-configmaps) . Alapértelmezés szerint ki van zárva az állandó mennyiségi mérőszámok gyűjteménye, amely jogcímeket tartalmaz a *Kube-System* névtérben. Ha engedélyezni szeretné a gyűjteményt ebben a névtérben, használja a `[metric_collection_settings.collect_kube_system_pv_metrics]` ConfigMap fájljának szakaszát. Részletekért lásd a [metrika-gyűjtemény beállításait](./container-insights-agent-config.md#metric-collection-settings) .

## <a name="metrics-collected"></a>Összegyűjtött metrikák

A szolgáltatás részeként a következő metrikákat engedélyezheti és gyűjtheti be, hacsak másként nincs megadva:

|Metrikai névtér |Metric |Leírás |
|---------|----|------------|
|Bepillantást nyerhet. tároló/csomópontok |cpuUsageMillicores |A CPU-kihasználtságot a millicores üzemelteti.|
|Bepillantást nyerhet. tároló/csomópontok |cpuUsagePercentage |CPU-használat százaléka a csomópont alapján.|
|Bepillantást nyerhet. tároló/csomópontok |memoryRssBytes |Memória RSS-kihasználtsága bájtban a gazdagép alapján.|
|Bepillantást nyerhet. tároló/csomópontok |memoryRssPercentage |Memóriabeli RSS-használati arány a gazdagép alapján.|
|Bepillantást nyerhet. tároló/csomópontok |memoryWorkingSetBytes |Memória munkakészletének kihasználtsága bájtban a gazdagép alapján.|
|Bepillantást nyerhet. tároló/csomópontok |memoryWorkingSetPercentage |Memória munkakészletének kihasználtsági aránya a gazdagép alapján.|
|Bepillantást nyerhet. tároló/csomópontok |nodesCount |Csomópontok száma állapot szerint.|
|Bepillantást nyerhet. tároló/csomópontok |diskUsedPercentage |A csomóponton az eszköz által használt lemez százalékos aránya.|
|Bepillantást nyerhet. tároló/hüvely |podCount |A hüvelyek száma a vezérlő, a névtér, a csomópont és a fázis alapján.|
|Bepillantást nyerhet. tároló/hüvely |completedJobsCount |A Befejezett feladatok száma régebbi felhasználó által konfigurálható küszöbérték (az alapértelmezett érték hat óra) a vezérlő, a Kubernetes névtér. |
|Bepillantást nyerhet. tároló/hüvely |restartingContainerCount |A tároló-újraindítások száma vezérlő által, Kubernetes névtérben.|
|Bepillantást nyerhet. tároló/hüvely |oomKilledContainerCount |OOMkilled-tárolók száma vezérlő szerint, Kubernetes-névtér alapján.|
|Bepillantást nyerhet. tároló/hüvely |podReadyPercentage |A hüvelyek százalékos aránya a vezérlő által üzemkész állapotú, Kubernetes névtérben.|
|Bepillantást nyerhet. tároló/tárolók |cpuExceededPercentage |A felhasználó által konfigurálható küszöbértéket meghaladó tárolók CPU-kihasználtsági aránya (az alapértelmezett érték 95,0) a tároló neve, a vezérlő neve, a Kubernetes névtér, a pod neve.<br> Gyűjtött  |
|Bepillantást nyerhet. tároló/tárolók |memoryRssExceededPercentage |A felhasználó által konfigurálható küszöbértéket meghaladó tárolók memóriabeli RSS-aránya (az alapértelmezett érték 95,0) a tároló neve, a vezérlő neve, a Kubernetes névtér, a pod neve.|
|Bepillantást nyerhet. tároló/tárolók |memoryWorkingSetExceededPercentage |A felhasználó által konfigurálható küszöbértéket meghaladó tárolók memória-munkakészletének százalékos aránya (az alapértelmezett érték 95,0) a tároló neve, a vezérlő neve, a Kubernetes névtér, a pod neve.|
|Bepillantást nyerhet. tároló/persistentvolumes |pvUsageExceededPercentage |Az állandó kötetek PV kihasználtsági aránya meghaladja a felhasználó által konfigurálható küszöbértéket (az alapértelmezett érték 60,0) a jogcím neve, a Kubernetes névtér, a kötet neve, a pod neve és a csomópont neve alapján.

## <a name="enable-alert-rules"></a>Riasztási szabályok engedélyezése

Az alábbi lépéseket követve engedélyezheti a metrika riasztásait a Azure Portal Azure Monitor. A Resource Manager-sablonok használatának engedélyezéséhez lásd: [Engedélyezés Resource Manager-sablonnal](#enable-with-a-resource-manager-template).

### <a name="from-the-azure-portal"></a>Az Azure Portalról

Ez a szakasz bemutatja, hogyan engedélyezhető a tárolók metrikai riasztása (előzetes verzió) Azure Monitor a Azure Portal.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

2. A tárolók metrikáinak riasztása (előzetes verzió) szolgáltatáshoz Azure Monitor való hozzáféréshez közvetlenül egy AK-fürtből lehet hozzáférni, ha a Azure Portal bal oldali ablaktáblájában **kijelöli** az eredményeket.

3. A parancssorban válassza a **javasolt riasztások** lehetőséget.

    ![A Azure Monitor ajánlott riasztások lehetőség a tárolók esetében](./media/container-insights-metric-alerts/command-bar-recommended-alerts.png)

4. Az **ajánlott riasztások** tulajdonságlapja automatikusan megjelenik az oldal jobb oldalán. Alapértelmezés szerint a listában szereplő összes riasztási szabály le van tiltva. Az **Engedélyezés** lehetőség kiválasztását követően létrejön a riasztási szabály, és a szabály neve frissül, hogy tartalmazza a riasztási erőforrásra mutató hivatkozást.

    ![Ajánlott riasztások tulajdonságai ablaktábla](./media/container-insights-metric-alerts/recommended-alerts-pane.png)

    Miután kiválasztotta az **Engedélyezés/letiltás** kapcsolót a riasztás engedélyezéséhez, egy riasztási szabályt hoz létre, és a szabály neve frissül, hogy tartalmazza a tényleges riasztási erőforrásra mutató hivatkozást.

    ![Riasztási szabály engedélyezése](./media/container-insights-metric-alerts/recommended-alerts-pane-enable.png)

5. A riasztási szabályok nincsenek olyan [műveleti csoportokhoz](../alerts/action-groups.md) társítva, amelyek értesítik a felhasználókat arról, hogy riasztást váltottak ki. Válassza a **nincs hozzárendelve műveleti csoport** elemet, és a **műveleti csoportok** lapon adjon meg egy meglévőt, vagy hozzon létre egy műveleti csoportot a **Hozzáadás** vagy a **Létrehozás** lehetőség kiválasztásával.

    ![Válasszon műveleti csoportot](./media/container-insights-metric-alerts/select-action-group.png)

### <a name="enable-with-a-resource-manager-template"></a>Engedélyezés Resource Manager-sablonnal

Egy Azure Resource Manager sablon és Parameters fájl segítségével létrehozhatja a tartalmazott metrikai riasztásokat a Azure Monitorban.

Az alapszintű lépések a következők:

1. Töltsön le egy vagy több elérhető sablont, amely leírja, hogyan hozhatja létre a riasztást a [githubról](https://github.com/microsoft/Docker-Provider/tree/ci_dev/alerts/recommended_alerts_ARM).

2. Hozzon létre és használjon egy [Parameters fájlt](../../azure-resource-manager/templates/parameter-files.md) JSON-ként a riasztási szabály létrehozásához szükséges értékek megadásához.

3. Telepítse a sablont a Azure Portal, a PowerShell vagy az Azure CLI használatával.

#### <a name="deploy-through-azure-portal"></a>Üzembe helyezés Azure Portal

1. Töltse le és mentse a helyi mappába, a Azure Resource Manager sablonra és paraméterre, hogy a riasztási szabályt a következő parancsok használatával hozza létre:

2. Ha egyéni sablont szeretne üzembe helyezni a portálon, válassza az **erőforrás létrehozása** lehetőséget a [Azure Portal](https://portal.azure.com).

3. Keressen rá a **sablon** kifejezésre, majd válassza a **template Deployment** lehetőséget.

4. Válassza a **Létrehozás** lehetőséget.

5. A sablonok létrehozásához több lehetőség is megjelenik, válassza **a saját sablon létrehozása a szerkesztőben** lehetőséget.

6. A **Sablon szerkesztése lapon** válassza a **fájl betöltése** lehetőséget, majd válassza ki a sablonfájlt.

7. A **Sablon szerkesztése** lapon válassza a **Mentés** lehetőséget.

8. Az **Egyéni telepítés** lapon adja meg a következőket, majd amikor a Befejezés gombra, válassza a **vásárlás** lehetőséget a sablon telepítéséhez és a riasztási szabály létrehozásához.

    * Erőforráscsoport
    * Hely
    * Riasztás neve
    * Fürterőforrás-azonosító

#### <a name="deploy-with-azure-powershell-or-cli"></a>Üzembe helyezés Azure PowerShell vagy parancssori felülettel

1. Töltse le és mentse a helyi mappába, a Azure Resource Manager sablonra és paraméterre, hogy a riasztási szabályt a következő parancsok használatával hozza létre:

2. A metrikai riasztást a sablon és paraméterek fájl használatával hozhatja létre a PowerShell vagy az Azure CLI használatával.

    Az Azure PowerShell használata

    ```powershell
    Connect-AzAccount

    Select-AzSubscription -SubscriptionName <yourSubscriptionName>
    New-AzResourceGroupDeployment -Name CIMetricAlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
    -TemplateFile templateFilename.json -TemplateParameterFile templateParameterFilename.parameters.json
    ```

    Az Azure parancssori felület használata

    ```azurecli
    az login

    az deployment group create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file templateFileName.json \
    --parameters @templateParameterFilename.parameters.json
    ```

    >[!NOTE]
    >Míg a metrika riasztása egy másik erőforráscsoporthoz hozható létre a célként megadott erőforráshoz, javasoljuk, hogy ugyanazt az erőforráscsoportot használja, mint a célként megadott erőforrás.

## <a name="edit-alert-rules"></a>Riasztási szabályok szerkesztése

Megtekintheti és kezelheti Azure Monitor a tárolók riasztási szabályaihoz, a küszöbértékének szerkesztéséhez vagy egy [műveleti csoport](../alerts/action-groups.md) konfigurálásához az AK-fürthöz. Ha ezeket a műveleteket a Azure Portal és az Azure parancssori felületéről is végrehajtja, akkor közvetlenül az AK-fürtből is elvégezhető a tárolók Azure Monitor.

1. A parancssorban válassza a **javasolt riasztások** lehetőséget.

2. A küszöbérték módosításához a **javasolt riasztások** panelen jelölje ki az engedélyezett riasztást. A **szabály szerkesztése** területen válassza ki a szerkeszteni kívánt **riasztási feltételeket** .

    * A riasztási szabály küszöbértékének módosításához válassza ki a **feltételt**.
    * Meglévő vagy létrehozott műveleti csoport megadásához válassza a **Hozzáadás** vagy **Létrehozás** a **műveleti csoportban** lehetőséget.

Az engedélyezett szabályokhoz létrehozott riasztások megtekintéséhez az **ajánlott riasztások** panelen válassza a **megtekintés a riasztásokban** lehetőséget. A rendszer átirányítja az AK-fürt riasztási menüjére, ahol a fürthöz jelenleg létrehozott összes riasztást láthatja.

## <a name="configure-alertable-metrics-in-configmaps"></a>Riasztásos metrikák konfigurálása a ConfigMaps-ben

A ConfigMap konfigurációs fájljának konfigurálásához hajtsa végre az alábbi lépéseket az alapértelmezett kihasználtsági küszöbértékek felülbírálásához. Ezek a lépések csak a következő riasztási mérőszámokra érvényesek:

* *cpuExceededPercentage*
* *memoryRssExceededPercentage*
* *memoryWorkingSetExceededPercentage*
* *pvUsageExceededPercentage*

1. Szerkessze a ConfigMap YAML fájlt a szakasz `[alertable_metrics_configuration_settings.container_resource_utilization_thresholds]` vagy a alatt `[alertable_metrics_configuration_settings.pv_utilization_thresholds]` .

   - Ha módosítani szeretné a *cpuExceededPercentage* küszöbértékét 90%-ra, és megkezdi a metrika gyűjtését, ha a küszöbérték teljesül és túllépi a határértéket, konfigurálja a ConfigMap-fájlt a következő példa használatával:

     ```
     [alertable_metrics_configuration_settings.container_resource_utilization_thresholds]
         # Threshold for container cpu, metric will be sent only when cpu utilization exceeds or becomes equal to the following percentage
         container_cpu_threshold_percentage = 90.0
         # Threshold for container memoryRss, metric will be sent only when memory rss exceeds or becomes equal to the following percentage
         container_memory_rss_threshold_percentage = 95.0
         # Threshold for container memoryWorkingSet, metric will be sent only when memory working set exceeds or becomes equal to the following percentage
         container_memory_working_set_threshold_percentage = 95.0
     ```

   - Ha módosítani szeretné a *pvUsageExceededPercentage* küszöbértékét 80%-ra, és megkezdi a metrika gyűjtését, ha a küszöbérték teljesül és túllépi a határértéket, konfigurálja a ConfigMap-fájlt a következő példa használatával:

     ```
     [alertable_metrics_configuration_settings.pv_utilization_thresholds]
         # Threshold for persistent volume usage bytes, metric will be sent only when persistent volume utilization exceeds or becomes equal to the following percentage
         pv_usage_threshold_percentage = 80.0
     ```

2. Futtassa a következő kubectl-parancsot: `kubectl apply -f <configmap_yaml_file.yaml>` .

    Példa: `kubectl apply -f container-azm-ms-agentconfig.yaml`.

A konfiguráció módosítása több percet is igénybe vehet, mielőtt érvénybe lépnek, és a fürtben lévő összes omsagent-hüvely újra fog indulni. Az újraindítás az összes omsagent-hüvely működés közbeni újraindítása; nem minden alkalommal indulnak újra. Az újraindítások befejezését követően megjelenik egy üzenet, amely az alábbi példához hasonlóan jelenik meg, és tartalmazza a következő eredményt: `configmap "container-azm-ms-agentconfig" created` .

## <a name="next-steps"></a>Következő lépések

- Megtekintheti a [napló lekérdezési példáit](container-insights-log-search.md#search-logs-to-analyze-data) , amelyekkel előre definiált lekérdezéseket és példákat tekinthet meg a fürtök riasztásának, megjelenítésének vagy elemzésének kiértékeléséhez és testreszabásához.

- Ha többet szeretne megtudni a Azure Monitorről és a Kubernetes-fürt egyéb szempontjainak figyeléséről, tekintse meg a [Kubernetes-fürt teljesítményének megtekintése](container-insights-analyze.md)című témakört.