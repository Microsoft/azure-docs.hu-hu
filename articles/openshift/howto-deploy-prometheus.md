---
title: Prometheus-példány üzembe helyezése az Azure Red Hat OpenShift-fürtben
description: Hozzon létre egy Prometheus-példányt egy Azure Red Hat OpenShift-fürtben az alkalmazás metrikáinak monitorozásához.
author: makdaam
ms.author: b-lejaku
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 06/17/2019
keywords: Prometheus, ARO, openshift, metrikák, Red Hat
ms.openlocfilehash: 974096e515401ab5e292dbad4c9beba7f1611a8a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100633444"
---
# <a name="deploy-a-standalone-prometheus-instance-in-an-azure-red-hat-openshift-cluster"></a>Önálló Prometheus-példány üzembe helyezése Azure Red Hat OpenShift-fürtben

> [!IMPORTANT]
> Az Azure Red Hat OpenShift 3,11 2022. június 30-án megszűnik. Az új Azure Red Hat OpenShift 3,11-fürtök létrehozásának támogatása az 2020. november 30-ig folytatódik. A biztonsági rések elkerülése érdekében a rendszer leállítja a fennmaradó Azure Red Hat OpenShift 3,11-fürtöket a kivonulást követően.
> 
> Kövesse ezt az útmutatót [egy Azure Red Hat OpenShift 4-fürt létrehozásához](tutorial-create-cluster.md).
> Ha konkrét kérdései vannak, vegye [fel velünk a kapcsolatot](mailto:arofeedback@microsoft.com).

Ez a cikk azt ismerteti, hogyan konfigurálható egy, a Service Discovery szolgáltatást használó önálló Prometheus-példány egy Azure Red Hat OpenShift-fürtben.

> [!NOTE]
> Nem szükséges ügyfél-rendszergazdai hozzáférés az Azure Red Hat OpenShift-fürthöz.

Cél beállítása:

- Egy projekt (Prometheus-projekt), amely Prometheus-t és Alertmanager tartalmaz.
- Két projekt (App-Projekt1 és app-Projekt2), amelyek tartalmazzák a figyelni kívánt alkalmazásokat.

A Prometheus konfigurációs fájljait helyileg kell előkészítenie. Hozzon létre egy új mappát a tárolásához. A konfigurációs fájlokat a rendszer titkokként tárolja a fürtben abban az esetben, ha a titkos jogkivonatokat később hozzáadja a fürthöz.

## <a name="sign-in-to-the-cluster-by-using-the-oc-tool"></a>Jelentkezzen be a fürtbe az OC eszköz használatával

1. Nyisson meg egy webböngészőt, majd nyissa meg a fürt webkonzolját ( https://openshift .*véletlenszerű azonosító*. *region*. azmosa.IO).
2. Jelentkezzen be az Azure-beli hitelesítő adataival.
3. Válassza ki a felhasználónevet a jobb felső sarokban, majd válassza a **bejelentkezési parancs másolása** lehetőséget.
4. Illessze be a felhasználónevet a használni kívánt terminálba.

> [!NOTE]
> Ha szeretné megtekinteni, hogy be van-e jelentkezve a megfelelő fürtbe, futtassa a `oc whoami -c` parancsot.

## <a name="prepare-the-projects"></a>A projektek előkészítése

A projektek létrehozásához futtassa a következő parancsokat:
```
oc new-project prometheus-project
oc new-project app-project1
oc new-project app-project2
```


> [!NOTE]
> Használhatja a `-n` vagy a `--namespace` paramétert, vagy kijelölhet egy aktív projektet a parancs futtatásával `oc project` .

## <a name="prepare-the-prometheus-configuration-file"></a>A Prometheus konfigurációs fájljának előkészítése
Hozzon létre egy Prometheus. YML fájlt a következő tartalom megadásával:
```
global:
  scrape_interval: 30s
  evaluation_interval: 5s

scrape_configs:
    - job_name: prom-sd
      scrape_interval: 30s
      scrape_timeout: 10s
      metrics_path: /metrics
      scheme: http
      kubernetes_sd_configs:
      - api_server: null
        role: endpoints
        namespaces:
          names:
          - prometheus-project
          - app-project1
          - app-project2
```
Hozzon létre egy Prom nevű titkot a következő konfiguráció beírásával:
```
oc create secret generic prom --from-file=prometheus.yml -n prometheus-project
```

A Prometheus. YML fájl egy alapszintű Prometheus konfigurációs fájl. Beállítja az intervallumokat és konfigurálja az automatikus észlelést három projektben (Prometheus-Project, app-Projekt1, app-Projekt2). Az előző konfigurációs fájlban az automatikusan felderített végpontokat a rendszer a hitelesítés nélkül, HTTP-n keresztül kaparja.

A kaparós végpontokkal kapcsolatos további információkért lásd: [Prometheus tájkép config](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config).


## <a name="prepare-the-alertmanager-config-file"></a>A Alertmanager konfigurációs fájl előkészítése
Hozzon létre egy alertmanager. YML fájlt a következő tartalom megadásával:
```
global:
  resolve_timeout: 5m
route:
  group_wait: 30s
  group_interval: 5m
  repeat_interval: 12h
  receiver: default
  routes:
  - match:
      alertname: DeadMansSwitch
    repeat_interval: 5m
    receiver: deadmansswitch
receivers:
- name: default
- name: deadmansswitch
```
Hozzon létre egy Prom-Alerts nevű titkos kulcsot a következő konfiguráció megadásával:
```
oc create secret generic prom-alerts --from-file=alertmanager.yml -n prometheus-project
```

A Alertmanager. YML a riasztási kezelő konfigurációs fájlja.

> [!NOTE]
> Az előző két lépés ellenőrzéséhez futtassa a `oc get secret -n prometheus-project` parancsot.

## <a name="start-prometheus-and-alertmanager"></a>A Prometheus és a Alertmanager elindítása
Lépjen a [openshift/Origin adattárra](https://github.com/openshift/origin/tree/release-3.11/examples/prometheus) , és töltse le a [Prometheus-standalone. YAML](
https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml) sablont. Alkalmazza a sablont a Prometheus-projectre a következő konfiguráció beírásával:
```
oc process -f https://raw.githubusercontent.com/openshift/origin/release-3.11/examples/prometheus/prometheus-standalone.yaml | oc apply -f - -n prometheus-project
```
A Prometheus-standalone. YAML fájl egy OpenShift-sablon. Létrehoz egy Prometheus-példányt a OAuth-proxyval, és egy Alertmanager-példányt, amely a OAuth-proxyval is védett. Ebben a sablonban a OAuth-proxy úgy van konfigurálva, hogy engedélyezze bármely olyan felhasználó számára, aki "beolvashatja" a Prometheus-Project névteret (lásd a `-openshift-sar` jelzőt).

> [!NOTE]
> A parancs futtatásával ellenőrizheti, hogy a Prom StatefulSet egyenlő-e a kívánt és a jelenlegi számú replikával `oc get statefulset -n prometheus-project` . A projekt összes erőforrásának vizsgálatához futtassa a `oc get all -n prometheus-project` parancsot.

## <a name="add-permissions-to-allow-service-discovery"></a>Engedélyek hozzáadása a szolgáltatás felderítésének engedélyezéséhez

Hozzon létre egy Prometheus-sdrole. YML fájlt a következő tartalom megadásával:
```
apiVersion: template.openshift.io/v1
kind: Template
metadata:
  name: prometheus-sdrole
  annotations:
    "openshift.io/display-name": Prometheus service discovery role
    description: |
      Role and rolebinding added permissions required for service discovery in a given project.
    iconClass: fa fa-cogs
    tags: "monitoring,prometheus,alertmanager,time-series"
parameters:
- description: The project name, where a standalone Prometheus is deployed
  name: PROMETHEUS_PROJECT
  value: prometheus-project
objects:
- apiVersion: rbac.authorization.k8s.io/v1
  kind: Role
  metadata:
    name: prometheus-sd
  rules:
  - apiGroups:
    - ""
    resources:
    - services
    - endpoints
    - pods
    verbs:
    - list
    - get
    - watch
- apiVersion: rbac.authorization.k8s.io/v1
  kind: RoleBinding
  metadata:
    name: prometheus-sd
  roleRef:
    apiGroup: rbac.authorization.k8s.io
    kind: Role
    name: prometheus-sd
  subjects:
  - kind: ServiceAccount
    name: prom
    namespace: ${PROMETHEUS_PROJECT}
```
Ha a sablont minden olyan projektre alkalmazni kívánja, amelyről engedélyezni szeretné a szolgáltatás-felderítést, futtassa a következő parancsokat:
```
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project1
oc process -f prometheus-sdrole.yml | oc apply -f - -n app-project2
oc process -f prometheus-sdrole.yml | oc apply -f - -n prometheus-project
```

> [!NOTE]
> A szerepkör-és a RoleBinding helyes létrehozásának ellenőrzéséhez futtassa a `oc get role` és a `oc get rolebinding` parancsot.

## <a name="optional-deploy-example-application"></a>Nem kötelező: példa alkalmazás üzembe helyezése

Minden működik, de nincsenek metrikai források. Nyissa meg a Prometheus URL-címét ( https://prom-prometheus-project.apps .*véletlenszerű azonosító*. *region*. azmosa.IO/). A következő paranccsal keresheti meg:

```
oc get route prom -n prometheus-project
```
> [!IMPORTANT]
> Ne felejtse el hozzáadni a https://előtagot az állomásnév elejéhez.

Az **állapot > szolgáltatás felderítése** lapon az 0/0 aktív célpontok jelennek meg.

Egy példaként szolgáló alkalmazás üzembe helyezéséhez, amely a/Metrics-végpont alatti alapszintű Python-metrikákat teszi elérhetővé, futtassa a következő parancsokat:
```
oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example1 -n app-project1

oc new-app python:3.6~https://github.com/Makdaam/prometheus-example --name=example2 -n app-project2
```
Az új alkalmazásoknak érvényes célként kell szerepelniük a szolgáltatás felderítési lapján a telepítés után 30 másodpercen belül.

További részletekért válassza az **állapot**  >  **céljait**.

> [!NOTE]
> A Prometheus minden sikeresen lekapart cél esetében felvesz egy adatpontot a felfelé mutató metrikába. Válassza a **Prometheus** lehetőséget a bal felső sarokban **, írja be a kifejezést** kifejezésként, majd válassza a **végrehajtás** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Egyéni Prometheus-rendszerállapot-kialakítást adhat az alkalmazásaihoz. A Prometheus-ügyfél könyvtára, amely leegyszerűsíti a Prometheus-metrikák előkészítését, készen áll a különböző programozási nyelvekre.

További információt a következő GitHub-tárakban talál:

 - [Java](https://github.com/prometheus/client_java)
 - [Python](https://github.com/prometheus/client_python)
 - [Ugrás](https://github.com/prometheus/client_golang)
 - [Ruby](https://github.com/prometheus/client_ruby)
