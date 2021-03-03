---
title: Oktatóanyag – az alkalmazás szintű mérőszámok Apache Sparkának monitorozása a Prometheus és a Grafana
description: Oktatóanyag – megtudhatja, hogyan helyezheti üzembe a Apache Spark alkalmazás mérőszámait egy Azure Kubernetes-szolgáltatási (ak-) fürtön, és megtudhatja, hogyan integrálhatja a Grafana-irányítópultokat.
services: synapse-analytics
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 01/22/2021
ms.openlocfilehash: aebfd4e2fc89ab597d97ef0da60bb4b265d88b96
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101696104"
---
# <a name="tutorial-monitor-apache-spark-application-level-metrics-with-prometheus-and-grafana"></a>Oktatóanyag: Apache Spark alkalmazás-szintű metrikák monitorozása a Prometheus és a Grafana révén

## <a name="overview"></a>Áttekintés

Ebből az oktatóanyagból megtudhatja, hogyan helyezheti üzembe a Apache Spark alkalmazás mérőszámait az Azure Kubernetes Service (ak) fürtön, és megtudhatja, hogyan integrálhatja a Grafana-irányítópultokat.

Ezzel a megoldással valós időben gyűjthet és kérdezheti le a Apache Spark metrikák adatait. Az integrált Grafana irányítópultokkal diagnosztizálhatja és figyelheti Apache Spark alkalmazását. A forráskód és a konfigurációk nyílt forráskódú a GitHubon.

## <a name="prerequisites"></a>Előfeltételek

1.  [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true)
2.  [Helm-ügyfél 3.30 +](https://github.com/helm/helm/releases)
3.  [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
4.  [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/en-us/services/kubernetes-service/)

Vagy használja a [Azure Cloud Shell](https://shell.azure.com/), amely már tartalmazza az Azure CLI-t, a Helm-ügyfelet és a kubectl.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

```bash
az login
az account set --subscription "<subscription_id>"
```

## <a name="create-an-azure-kubernetes-service-instance-aks"></a>Azure Kubernetes Service-példány létrehozása (ak)

Az Azure CLI parancs használatával hozzon létre egy Kubernetes-fürtöt az előfizetésében.

```
az aks create --name <kubernetes_name> --resource-group <kubernetes_resource_group> --location <location> --node-vm-size Standard_D2s_v3
az aks get-credentials --name <kubernetes_name> --resource-group <kubernetes_resource_group>
```

Megjegyzés: Ez a lépés kihagyható, ha már rendelkezik AK-fürttel.

## <a name="create-a-service-principal-and-grant-permission-to-synapse-workspace"></a>Egyszerű szolgáltatásnév létrehozása és engedélyek megadása a szinapszis-munkaterülethez

```bash
az ad sp create-for-rbac --name <service_principal_name>
```

Az eredménynek a következőhöz hasonlóan kell kinéznie:

```json
{
  "appId": "abcdef...",
  "displayName": "<service_principal_name>",
  "name": "http://<service_principal_name>",
  "password": "abc....",
  "tenant": "<tenant_id>"
}
```

Jegyezze fel a appId, a jelszót és a tenantID.

[![képernyőfelvétel engedélyezési srbac](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-grant-permission-srbac-new.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-grant-permission-srbac-new.png#lightbox)

1. Jelentkezzen be az [Azure szinapszis Analytics-munkaterületre](https://web.azuresynapse.net/) a szinapszis-rendszergazdaként

2. A szinapszis Studióban a bal oldali ablaktáblán válassza a **kezelés > hozzáférés-vezérlés**

3. **Szerepkör-hozzárendelés hozzáadásához** kattintson a bal felső sarokban található Hozzáadás gombra.

4. A hatókör területen válassza a **munkaterület** lehetőséget.

5. A szerepkörhöz válassza a **szinapszis számítási operátor** elemet.

6. A felhasználó kiválasztása beállításnál adja meg a **<service_principal_name>** , majd kattintson a szolgáltatásra

7. Kattintson az **alkalmaz** gombra (várjon 3 percet, amíg az engedély érvénybe lép.)

## <a name="install-connector-prometheus-server-grafana-dashboard"></a>Telepítési összekötő, Prometheus-kiszolgáló, Grafana-irányítópult

1. A szinapszis-Charts tárház hozzáadása a Helm-ügyfélhez.

```bash
helm repo add synapse-charts https://github.com/microsoft/azure-synapse-spark-metrics/releases/download/helm-chart
```

2.  Összetevők telepítése Helm-ügyféllel:

```bash
helm install spo synapse-charts/synapse-prometheus-operator --create-namespace --namespace spo \
    --set synapse.workspaces[0].workspace_name="<workspace_name>" \
    --set synapse.workspaces[0].tenant_id="<tenant_id>" \
    --set synapse.workspaces[0].service_principal_name="<service_principal_app_id>" \
    --set synapse.workspaces[0].service_principal_password="<service_principal_password>" \
    --set synapse.workspaces[0].subscription_id="<subscription_id>" \
    --set synapse.workspaces[0].resource_group="<workspace_resource_group_name>"
```

 - workspace_name: szinapszis-munkaterület neve.
 - subscription_id: szinapszis-munkaterület előfizetés-azonosítója.
 - workspace_resource_group_name: szinapszis-munkaterület erőforráscsoport-neve.
 - tenant_id: szinapszis-munkaterület bérlői azonosítója.
 - service_principal_app_id: a "appId" egyszerű szolgáltatásnév
 - service_principal_password: a létrehozott egyszerű szolgáltatás jelszava.

## <a name="log-in-to-grafana"></a>Bejelentkezés a Grafana

Szerezze be a Grafana alapértelmezett jelszavát és a címeit. A jelszót a Grafana beállításokban módosíthatja.

```bash
kubectl get secret --namespace spo spo-grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
kubectl -n spo get svc spo-grafana
```

Szerezze be a szolgáltatás IP-címét, másolja & illessze be a külső IP-címet a böngészőben, és jelentkezzen be a "rendszergazda" felhasználónévvel és a jelszóval.

## <a name="use-grafana-dashboards"></a>Grafana-irányítópultok használata

Keresse meg a Grafana lap bal felső sarkában található szinapszis-irányítópultot (Home-> szinapszis munkaterület/szinapszis alkalmazás), próbáljon meg egy példát kódot futtatni a szinapszis Studióban, és várjon néhány másodpercet a metrikák húzásakor.

Emellett használhatja a "szinapszis munkaterület/munkaterület" és a "szinapszis-munkaterület/Spark-készletek" irányítópultokat, hogy áttekintést kapjon a munkaterületről és a Apache Spark készletekről.

## <a name="uninstall"></a>Eltávolítás

A következő módon távolítsa el az összetevőket a Helm paranccsal.

```bash
helm delete <release_name> -n <namespace>
```

Törölje az AK-fürtöt.

```bash
az aks delete --name <kubernetes_cluster_name> --resource-group <kubernetes_cluster_rg>
```

## <a name="components-introduction"></a>Összetevők bemutatása

Az Azure szinapszis Analytics egy [Helm-diagramot](https://github.com/microsoft/azure-synapse-spark-metrics/tree/main/helm) biztosít a Prometheus-kezelő és a szinapszis Prometheus-összekötő alapján. A Helm diagramon a Prometheus-kiszolgáló, a Grafana-kiszolgáló és a Grafana irányítópultok szerepelnek Apache Spark alkalmazás-szintű metrikák esetében. A [Prometheus](https://prometheus.io/)egy népszerű, nyílt forráskódú figyelési rendszer használatával gyűjti össze ezeket a mérőszámokat közel valós időben, és [Grafana](https://github.com/grafana/grafana) használhat a vizualizációhoz.

### <a name="synapse-prometheus-connector"></a>Szinapszis Prometheus-összekötő

A szinapszis Prometheus-összekötő az Azure szinapszis Apache Spark-készlet és a Prometheus-kiszolgáló összekapcsolását teszi lehetővé. A következőket implementálja:

1.  Hitelesítés: HRE-alapú hitelesítés, és képes automatikusan frissíteni az egyszerű szolgáltatásnév HRE-tokenjét az alkalmazások felderítésére, a metrikák betöltésére és más funkciókra.
2.  Apache Spark alkalmazás felderítése: amikor alkalmazásokat küld a cél munkaterületen, a szinapszis Prometheus-összekötő automatikusan képes felderíteni ezeket az alkalmazásokat.
3.  Apache Spark alkalmazás metaadatainak: az alkalmazás alapvető információkat gyűjt, és exportálja az adatokat a Prometheus-be.

A szinapszis Prometheus Connector a [Microsoft Container Registry](https://github.com/microsoft/containerregistry)üzemeltetett Docker-rendszerképként jelenik meg. Ez nyílt forráskódú, és az [Azure szinapszis Spark alkalmazás-metrikákban](https://github.com/microsoft/azure-synapse-spark-metrics)található.

### <a name="prometheus-server"></a>Prometheus-kiszolgáló

A Prometheus egy nyílt forráskódú monitorozási és riasztási eszközkészlet. A a Cloud Native Computing Foundation (CNCF) által beszerzett Prometheus a Felhőbeli natív figyeléshez a de facto szabványt kapta. A Prometheus segíthet a nagy mennyiségű idősoros adatok gyűjtésében, lekérdezésében és tárolásában, és könnyen integrálható a Grafana. Ebben a megoldásban a Prometheus-összetevőt a Helm diagram alapján helyezzük üzembe.

### <a name="grafana-and-dashboards"></a>Grafana és irányítópultok

A Grafana nyílt forráskódú vizualizációs és elemzési szoftver. Lehetővé teszi a metrikák lekérdezését, megjelenítését, riasztását és megismerését. Az Azure szinapszis Analytics az alapértelmezett Grafana-irányítópultok készletét kínálja Apache Spark alkalmazás szintű metrikák megjelenítéséhez.

A "szinapszis-munkaterület/munkaterület" irányítópulton a Apache Spark készletek, az alkalmazások száma, a CPU-magok stb. láthatók a munkaterület szintjén.

[![képernyőkép-irányítópult munkaterülete](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-workspace.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-workspace.png#lightbox)

A "szinapszis munkaterület/Spark-készletek" irányítópult a kiválasztott Apache Spark-készletben futó Apache Spark alkalmazások metrikáit tartalmazza az adott időszakban.

[![képernyőkép-irányítópult sparkpool](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-sparkpool.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-sparkpool.png#lightbox)

A "szinapszis-munkaterület/Spark-alkalmazás" irányítópult a kiválasztott Apache Spark alkalmazást tartalmazza.

[![képernyőkép-irányítópult alkalmazása](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-application.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-application.png#lightbox)

A fenti irányítópult-sablonok nyílt forráskódúak az [Azure szinapszis Spark alkalmazás-metrikákban](https://github.com/microsoft/azure-synapse-spark-metrics/tree/main/helm/synapse-prometheus-operator/grafana_dashboards).
