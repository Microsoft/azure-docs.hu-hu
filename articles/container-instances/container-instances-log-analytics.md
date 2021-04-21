---
title: Erőforrásnaplók & és elemzése
description: Megtudhatja, hogyan küldhet erőforrásnaplókat és eseményadatokat a tárolócsoportokból Azure Container Instances a Azure Monitor naplókba
ms.topic: article
ms.date: 07/13/2020
ms.openlocfilehash: e46a1df65a4cfe5d10a58704aff485aa2834b55f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763918"
---
# <a name="container-group-and-instance-logging-with-azure-monitor-logs"></a>Tárolócsoport- és példánynaplózás a Azure Monitor naplókban

A Log Analytics-munkaterületek központi helyet biztosítanak a naplóadatok tárolásához és lekérdezéséhez nemcsak az Azure-erőforrásokból, hanem a helyszíni erőforrásokból és erőforrásokból is más felhőkben. Azure Container Instances beépített támogatást nyújt a naplók és eseményadatok a naplókba való Azure Monitor küldésekor.

Ha tárolócsoport napló- és eseményadatokat Azure Monitor naplókba, adjon meg egy meglévő Log Analytics-munkaterület-azonosítót és -munkaterületkulcsot a tárolócsoport konfigurálásakor. 

A következő szakaszok a naplózást engedélyező tárolócsoport és a naplók lekérdezésének a módszerét ismertetik. A naplózás [engedélyezéséhez a tárolócsoportokat](container-instances-update.md) munkaterület-azonosítóval és munkaterületi kulccsal is frissítheti.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
> Jelenleg csak Linux-tárolópéldányok eseményadatokat küldhet a Log Analyticsnek.

## <a name="prerequisites"></a>Előfeltételek

A tárolópéldányokban való naplózás engedélyezéséhez a következőkre van szükség:

* [Log Analytics-munkaterület](../azure-monitor/logs/quick-create-workspace.md)
* [Azure CLI](/cli/azure/install-azure-cli) (vagy [Cloud Shell](../cloud-shell/overview.md))

## <a name="get-log-analytics-credentials"></a>A Log Analytics hitelesítő adatainak lekérése

Az Azure Container Instancesnek engedélyre van szüksége ahhoz, hogy adatokat küldhessen a Log Analytics-munkaterületre. Ennek az engedélynek a megadásához és a naplózás engedélyezéséhez a tárolócsoport létrehozásakor meg kell adnia a Log Analytics-munkaterület azonosítóját és egy ahhoz tartozó kulcsot (az elsődleges vagy a másodlagos kulcsot).

A Log Analytics-munkaterület azonosítójának és elsődleges kulcsának beszerzése:

1. Az Azure Portalon lépjen a saját Log Analytics-munkaterületére
1. A **Beállítások alatt** válassza az Ügynökök kezelése **lehetőséget.**
1. Jegyezze fel a következőket:
   * **Munkaterület azonosítója**
   * **Elsődleges kulcs**

## <a name="create-container-group"></a>Tárolócsoport létrehozása

Most, hogy megvan a Log Analytics-munkaterület azonosítója és elsődleges kulcsa, készen áll egy naplózást engedélyező tárolócsoport létrehozására.

Az alábbi példák két módszert mutatnak be egy tárolócsoport létrehozásához, amely egyetlen [fluentd][fluentd] tárolóból áll: az Azure CLI-t és az Azure CLI-t YAML-sablonnal. A fluentd tároló több sornyi kimenetet állít elő az alapértelmezett konfigurációban. Mivel ez a kimenet a Log Analytics-munkaterületre lesz elküldve, jól használható a naplók megtekintésének és lekérdezésének bemutatásához.

### <a name="deploy-with-azure-cli"></a>Üzembe helyezés az Azure CLI-n keresztül

Az Azure CLI üzembe helyezéséhez adja meg a `--log-analytics-workspace` és a `--log-analytics-workspace-key` paramétereket az [az container create][az-container-create] parancsban. Cserélje le a két munkaterület-értéket az előző lépésben lekért értékekre (és frissítse az erőforráscsoport nevét) a következő parancs futtatása előtt.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainergroup001 \
    --image fluent/fluentd \
    --log-analytics-workspace <WORKSPACE_ID> \
    --log-analytics-workspace-key <WORKSPACE_KEY>
```

### <a name="deploy-with-yaml"></a>Üzembe helyezés a YAML használatával

Ezt a módszert akkor használja, ha a YAML használatával szeretne tárolócsoportokat üzembe helyezni. Az alábbi YAML egy tárolócsoportot határoz meg, benne egyetlen tárolóval. Másolja a YAML-t egy új fájlba, majd cserélje le a `LOG_ANALYTICS_WORKSPACE_ID` és `LOG_ANALYTICS_WORKSPACE_KEY` helyőrzőket az előző lépésben lekért értékekre. Mentse a fájlt **deploy-aci.yaml** néven.

```yaml
apiVersion: 2019-12-01
location: eastus
name: mycontainergroup001
properties:
  containers:
  - name: mycontainer001
    properties:
      environmentVariables: []
      image: fluent/fluentd
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
  diagnostics:
    logAnalytics:
      workspaceId: LOG_ANALYTICS_WORKSPACE_ID
      workspaceKey: LOG_ANALYTICS_WORKSPACE_KEY
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Ezután hajtsa végre a következő parancsot a tárolócsoport üzembe helyezéséhez. Cserélje le a helyére az előfizetésben egy erőforráscsoportot (vagy először hozzon létre `myResourceGroup` egy myResourceGroup nevű erőforráscsoportot):

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainergroup001 --file deploy-aci.yaml
```

Röviddel a parancs kiadását követően választ kell kapnia az Azure-tól, amely az üzemelő példány részleteit taglalja.

## <a name="view-logs"></a>Naplók megtekintése

A tárolócsoport üzembe helyezése után néhány percbe telik (legfeljebb 10 percbe), hogy az Azure Portalon megjelenjenek az első naplóbejegyzések. 

A tárolócsoport naplóinak megtekintése a `ContainerInstanceLog_CL` táblázatban:

1. Az Azure Portalon lépjen a saját Log Analytics-munkaterületére
1. Az **Általános alatt** válassza a Naplók **lehetőséget.**  
1. Írja be a következő lekérdezést: `ContainerInstanceLog_CL | limit 50`
1. Válassza a **Futtatás lehetőséget**

A lekérdezés több eredményt is megjelenít. Ha először nem lát eredményeket, várjon néhány percet,  majd válassza a Futtatás gombot a lekérdezés újrafuttatáshoz. Alapértelmezés szerint a naplóbejegyzések Táblázat formátumban **jelennek** meg. Ezután a sorokat kibontva tekintheti meg az egyes naplóbejegyzések tartalmát.

![Naplókeresési találatok az Azure Portalon][log-search-01]

## <a name="view-events"></a>Események megtekintése

A tárolópéldányok eseményeit a következő Azure Portal. Az események közé tartozik a példány létrehozási ideje és az kezdődött. Az eseményadatok megtekintése a `ContainerEvent_CL` táblázatban:

1. Az Azure Portalon lépjen a saját Log Analytics-munkaterületére
1. Az **Általános alatt** válassza a Naplók **lehetőséget.**  
1. Írja be a következő lekérdezést: `ContainerEvent_CL | limit 50`
1. Válassza a **Futtatás lehetőséget**

A lekérdezés több eredményt is megjelenít. Ha először nem lát eredményeket, várjon néhány percet,  majd válassza a Futtatás gombot a lekérdezés újrafuttatáshoz. Alapértelmezés szerint a bejegyzések Táblázat formátumban **jelennek** meg. Ezután kibonthat egy sort, és láthatja egy adott bejegyzés tartalmát.

![Az Eseménykeresés eredményei a Azure Portal][log-search-02]

## <a name="query-container-logs"></a>Tárolónaplók lekérdezése

Azure Monitor naplók egy kiterjedt [lekérdezési nyelvet][query_lang] tartalmaznak, amely akár több ezer sornyi naplókimenet adatait is lekérdezheti.

A lekérdezések alapvető szerkezete a forrástábla (ebben a cikkben vagy ), amelyet a pipe karakterrel () elválasztott operátorok `ContainerInstanceLog_CL` `ContainerEvent_CL` `|` követnek. Több operátor sorba állításával finomíthatja az eredményeket, és speciális funkciókat végezhet el.

A példa lekérdezési eredményekért illessze be a következő  lekérdezést a lekérdezés szövegmezőjéhez, majd válassza a Futtatás gombot a lekérdezés végrehajtásához. Ez a lekérdezés megjeleníti az összes olyan naplóbejegyzést, amelynek „Message” (Üzenet) mezője tartalmazza a „warn” (figyelmeztetés) szót:

```query
ContainerInstanceLog_CL
| where Message contains "warn"
```

Összetettebb lekérdezések használata is támogatott. Például ez a lekérdezés csak a „mycontainergroup001” tárolócsoporthoz az elmúlt egy órában létrejött naplóbejegyzéseket jeleníti meg:

```query
ContainerInstanceLog_CL
| where (ContainerGroup_s == "mycontainergroup001")
| where (TimeGenerated > ago(1h))
```

## <a name="next-steps"></a>Következő lépések

### <a name="azure-monitor-logs"></a>Azure Monitor-naplók

A naplók lekérdezésére és a riasztások a naplókban való konfigurálásával kapcsolatos további Azure Monitor lásd:

* [A naplók naplókeresésének ismertetése Azure Monitor naplókban](../azure-monitor/logs/log-query-overview.md)
* [Egyesített riasztások az Azure Monitorban](../azure-monitor/alerts/alerts-overview.md)


### <a name="monitor-container-cpu-and-memory"></a>Tárolók processzor- és memóriahasználatának monitorozása

A tárolópéldányok processzor- és memória-erőforrásainak monitorozásával kapcsolatos bővebb információkért lásd:

* [Tároló-erőforrások figyelése a Azure Container Instances.](container-instances-monitor.md)

<!-- IMAGES -->
[log-search-01]: ./media/container-instances-log-analytics/portal-query-01.png
[log-search-02]: ./media/container-instances-log-analytics/portal-query-02.png

<!-- LINKS - External -->
[fluentd]: https://hub.docker.com/r/fluent/fluentd/
[query_lang]: /azure/data-explorer/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create