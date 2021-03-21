---
title: Helyi mérőszámok és naplók konfigurálása az Azure API Management saját üzemeltetésű átjáróhoz | Microsoft Docs
description: Ismerje meg, hogyan konfigurálhatja az Azure API Management saját üzemeltetésű átjárójának helyi mérőszámait és naplóit egy Kubernetes-Custer
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/01/2021
ms.author: apimpm
ms.openlocfilehash: 2b66663c9ee8033bcb12bfac57964ea0eafecdac
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100594171"
---
# <a name="configure-local-metrics-and-logs-for-azure-api-management-self-hosted-gateway"></a>Helyi mérőszámok és naplók konfigurálása az Azure API Management saját üzemeltetésű átjáróhoz

Ez a cikk részletesen ismerteti a helyi mérőszámok és naplók konfigurálását a Kubernetes-fürtön üzembe helyezett saját üzemeltetésű [átjáróhoz](./self-hosted-gateway-overview.md) . A felhő metrikáinak és naplóinak konfigurálásához tekintse meg [ezt a cikket](how-to-configure-cloud-metrics-logs.md). 

## <a name="metrics"></a>Mérőszámok
A saját üzemeltetésű átjáró támogatja a [statisztikát](https://github.com/statsd/statsd), amely egységesítő protokollként szolgál a metrikák gyűjtéséhez és összesítéséhez. Ez a szakasz részletesen ismerteti a statisztikai adatok Kubernetes történő üzembe helyezésének lépéseit, az átjáró konfigurálását a mérőszámok statisztikán keresztüli kibocsátásához, valamint a [Prometheus](https://prometheus.io/) használatával a metrikák monitorozásához. 

### <a name="deploy-statsd-and-prometheus-to-the-cluster"></a>A statd és a Prometheus üzembe helyezése a fürtön

Az alábbi példa egy YAML konfigurációt biztosít a statisztikailag és a Prometheus üzembe helyezéséhez azon a Kubernetes-fürtön, ahol a saját üzemeltetésű átjáró üzembe van helyezve. Emellett létrehoz egy [szolgáltatást](https://kubernetes.io/docs/concepts/services-networking/service/) mindegyikhez. A saját üzemeltetésű átjáró metrikákat tesz közzé a statisztikás szolgáltatásban. A Prometheus-irányítópultot a szolgáltatásán keresztül fogjuk elérni.   

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: sputnik-metrics-config
data:
  statsd.yaml: ""
  prometheus.yaml: |
    global:
      scrape_interval:     3s
      evaluation_interval: 3s
    scrape_configs:
      - job_name: 'prometheus'
        static_configs:
          - targets: ['localhost:9090']
      - job_name: 'test_metrics'
        static_configs:
          - targets: ['localhost:9102']
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: sputnik-metrics
spec:
  replicas: 1
  selector:
    matchLabels:
      app: sputnik-metrics
  template:
    metadata:
      labels:
        app: sputnik-metrics
    spec:
      containers:
      - name: sputnik-metrics-statsd
        image: mcr.microsoft.com/aks/hcp/prom/statsd-exporter
        ports:
        - name: tcp
          containerPort: 9102
        - name: udp
          containerPort: 8125
          protocol: UDP
        args:
          - --statsd.mapping-config=/tmp/statsd.yaml
          - --statsd.listen-udp=:8125
          - --web.listen-address=:9102
        volumeMounts:
          - mountPath: /tmp
            name: sputnik-metrics-config-files
      - name: sputnik-metrics-prometheus
        image: mcr.microsoft.com/oss/prometheus/prometheus
        ports:
        - name: tcp
          containerPort: 9090
        args:
          - --config.file=/tmp/prometheus.yaml
        volumeMounts:
          - mountPath: /tmp
            name: sputnik-metrics-config-files
      volumes:
        - name: sputnik-metrics-config-files
          configMap:
            name: sputnik-metrics-config
---
apiVersion: v1
kind: Service
metadata:
  name: sputnik-metrics-statsd
spec:
  type: NodePort
  ports:
  - name: udp
    port: 8125
    targetPort: 8125
    protocol: UDP
  selector:
    app: sputnik-metrics
---
apiVersion: v1
kind: Service
metadata:
  name: sputnik-metrics-prometheus
spec:
  type: LoadBalancer
  ports:
  - name: http
    port: 9090
    targetPort: 9090
  selector:
    app: sputnik-metrics
```

Mentse a konfigurációkat egy nevű fájlba `metrics.yaml` , és az alábbi parancs használatával végezze el az összes beállítását a fürtön:

```console
kubectl apply -f metrics.yaml
```

Az üzembe helyezés befejezése után futtassa az alábbi parancsot a hüvelyek futtatásának vizsgálatához. Vegye figyelembe, hogy a pod neve eltérő lesz. 

```console
kubectl get pods
NAME                                   READY   STATUS    RESTARTS   AGE
sputnik-metrics-f6d97548f-4xnb7        2/2     Running   0          1m
```

Futtassa az alábbi parancsot, és győződjön meg arról, hogy a szolgáltatások futnak. Jegyezze fel a `CLUSTER-IP` statisztikát és a statisztikailag felhasználható `PORT` szolgáltatást, később szükség lesz rá. A Prometheus-irányítópultot a és a használatával érheti el `EXTERNAL-IP` `PORT` .

```console
kubectl get services
NAME                         TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE
sputnik-metrics-prometheus   LoadBalancer   10.0.252.72   13.89.141.90    9090:32663/TCP               18h
sputnik-metrics-statsd       NodePort       10.0.41.179   <none>          8125:32733/UDP               18h
```

### <a name="configure-the-self-hosted-gateway-to-emit-metrics"></a>A saját üzemeltetésű átjáró konfigurálása mérőszámok kibocsátásához

Most, hogy mind a statd, mind a Prometheus üzembe lett helyezve, frissítjük a saját üzemeltetésű átjáró konfigurációját, hogy elindítsa a mérőszámokat a statisztikán keresztül. A szolgáltatás engedélyezhető vagy letiltható a `telemetry.metrics.local` saját üzemeltetésű átjáró ConfigMap található kulcs használatával, további beállításokkal. Alább látható az elérhető lehetőségek részletezése:

| Mező  | Alapértelmezett | Description |
| ------------- | ------------- | ------------- |
| telemetria. Metrics. local  | `none` | Lehetővé teszi a naplózást a statisztikán keresztül. Az érték lehet `none` `statsd` . |
| telemetria. mérőszámok. local. statd. Endpoint  | n.a. | A statisztikai végpontot határozza meg. |
| telemetria. mérőszámok. local. destatd. mintavételezés  | n.a. | Meghatározza a metrikák mintavételezési sebességét. Az érték lehet 0 és 1. emelkedés pl `0.5`|
| telemetria. mérőszámok. local. statd. tag – Format  | n.a. | A statisztikailag kimutatott exportőr [címkézési formátuma](https://github.com/prometheus/statsd_exporter#tagging-extensions). Az érték lehet:,, `none` `librato` `dogStatsD` , `influxDB` . |

Íme egy példa konfiguráció:

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<self-hosted-gateway-management-endpoint>"
        telemetry.metrics.local: "statsd"
        telemetry.metrics.local.statsd.endpoint: "10.0.41.179:8125"
        telemetry.metrics.local.statsd.sampling: "1"
        telemetry.metrics.local.statsd.tag-format: "dogStatsD"
```

Frissítse a saját üzemeltetésű átjáró üzembe helyezésének YAML-fájlját a fenti konfigurációkkal, és alkalmazza a módosításokat az alábbi parancs használatával: 

```console
kubectl apply -f <file-name>.yaml
 ```

A legújabb konfigurációs változások kiválasztásához indítsa újra az átjáró telepítését az alábbi parancs használatával:

```console
kubectl rollout restart deployment/<deployment-name>
```

### <a name="view-the-metrics"></a>A metrikák megtekintése

Most már mindent üzembe helyezett és konfigurált, a saját üzemeltetésű átjárónak statisztikán keresztül kell jelentenie a metrikákat. A Prometheus felveszi a mérőszámokat a statisztikából. Nyissa meg a Prometheus-irányítópultot a `EXTERNAL-IP` és a `PORT` Prometheus szolgáltatás használatával. 

Hajtson végre néhány API-hívást a saját üzemeltetésű átjárón keresztül, ha minden megfelelően van konfigurálva, a következő metrikákat kell megtekinteni:

| Metric  | Leírás |
| ------------- | ------------- |
| Kérelmek  | API-kérelmek száma az adott időszakban |
| DurationInMS | A kérelem átjáróhoz való megérkezése és a teljes válasz elküldése között eltelt ezredmásodpercek száma |
| BackendDurationInMS | A háttérrendszer I/O-folyamatával töltött teljes idő ezredmásodpercben (csatlakozás, bájtok küldése és fogadása)  |
| ClientDurationInMS | Az ügyfél I/O-folyamatával töltött teljes idő ezredmásodpercben (csatlakozás, bájtok küldése és fogadása)  |

## <a name="logs"></a>Naplók

A saját üzemeltetésű átjáró alapértelmezés szerint a és a naplókat is megjeleníti `stdout` `stderr` . A naplók egyszerűen megtekinthetők a következő parancs használatával:

```console
kubectl logs <pod-name>
```

Ha a saját üzemeltetésű átjáró üzembe helyezése az Azure Kubernetes szolgáltatásban történik, engedélyezheti [Azure monitor a tárolók](../azure-monitor/containers/container-insights-overview.md) számára a számítási `stdout` `stderr` feladatokhoz és a munkaterhelések összegyűjtéséhez, valamint a naplófájlok megtekintéséhez log Analytics. 

A saját üzemeltetésű átjáró számos protokollt is támogat, például: `localsyslog` , `rfc5424` és `journal` . Az alábbi táblázat összefoglalja az összes támogatott lehetőséget. 

| Mező  | Alapértelmezett | Description |
| ------------- | ------------- | ------------- |
| telemetria. logs. STD  | `text` | Engedélyezi a naplózást a standard streamek számára. Az érték lehet `none` , `text` , `json` |
| telemetria. logs. local  | `none` | Engedélyezi a helyi naplózást. Az érték lehet `none` a,, `auto` `localsyslog` , `rfc5424` , `journal`  |
| telemetria. logs. local. localsyslog. Endpoint  | n.a. | Megadja a localsyslog-végpontot.  |
| telemetria. logs. local. localsyslog. Facility  | n.a. | Meghatározza a [localsyslog.](https://en.wikipedia.org/wiki/Syslog#Facility) emelkedés pl `7` 
| telemetria. logs. local. rfc5424. Endpoint  | n.a. | Megadja a rfc5424-végpontot.  |
| telemetria. logs. local. rfc5424. Facility  | n.a. | Meghatározza a létesítmény kódját [rfc5424](https://tools.ietf.org/html/rfc5424). emelkedés pl `7`  |
| telemetria. logs. local. Journal. Endpoint  | n.a. | Megadja a napló végpontját.  |

Példa a helyi naplózási konfigurációra:

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<self-hosted-gateway-management-endpoint>"
        telemetry.logs.std: "text"
        telemetry.logs.local.localsyslog.endpoint: "/dev/log"
        telemetry.logs.local.localsyslog.facility: "7"
```
 
## <a name="next-steps"></a>Következő lépések

* További információ a saját üzemeltetésű átjáróról: [Azure API Management saját üzemeltetésű átjáró – áttekintés](self-hosted-gateway-overview.md)
* [A naplók Felhőbeli konfigurálásának és](how-to-configure-local-metrics-logs.md) megőrzésének megismerése
