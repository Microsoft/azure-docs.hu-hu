---
title: Arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálócsoport konfigurációjának megjelenítése
titleSuffix: Azure Arc enabled data services
description: Arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálócsoport konfigurációjának megjelenítése
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: a268cd6b2fa3da6846554e3d1b170298abec7f18
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93279401"
---
# <a name="show-the-configuration-of-an-arc-enabled-postgresql-hyperscale-server-group"></a>Arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálócsoport konfigurációjának megjelenítése

Ez a cikk azt ismerteti, Hogyan jeleníthető meg a kiszolgálói csoport (ok) konfigurációja. Ezt úgy teheti meg, hogy megkérdezi az Ön által feltett kérdéseket, és megválaszolja őket. Időnként több érvényes válasz is lehet. Ez a cikk a leggyakoribb vagy leghasznosabb. Ezeket a kérdéseket a téma szerint csoportosítja:

- Kubernetes szempontból
- egy Azure arc-kompatibilis adatszolgáltatási pontról

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="from-a-kubernetes-point-of-view"></a>Kubernetes szempontból

### <a name="how-many-pods-are-used-by-azure-arc-enabled-postgresql-hyperscale"></a>Hány hüvelyt használnak az Azure arc engedélyezve PostgreSQL nagy kapacitású?

A postgres típusú Kubernetes-erőforrások listázása. Futtassa a következő parancsot:

```console
kubectl get postgresqls [-n <namespace name>]
```

A parancs kimenete a létrehozott kiszolgálócsoportok listáját jeleníti meg. Mindegyik esetében a hüvelyek számát jelzi. Például:

```output
NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      6h34m
postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d7h
```

Ez a példa azt mutatja, hogy két kiszolgálócsoport jön létre, és mindegyik 3 hüvelyen fut (1 koordinátor + 2 feldolgozó). Ez azt jelenti, hogy az Azure arc-adatkezelőben létrehozott kiszolgálócsoportok 6 hüvelyt használnak.

### <a name="what-pods-are-used-by-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Milyen hüvelyeket használnak az Azure arc engedélyezve PostgreSQL nagy kapacitású-kiszolgálócsoportok?

Futtassa a következőt:

```console
kubectl get pods [-n <namespace name>]
```

Ez visszaadja a hüvelyek listáját. A kiszolgálócsoportok által használt hüvelyek az adott kiszolgálócsoportok számára megadott nevek alapján jelennek meg. Például:

```console 
NAME                 READY   STATUS    RESTARTS   AGE
bootstrapper-vdltm   1/1     Running   0          6d8h
control-h6kc9        2/2     Running   0          6d8h
controldb-0          2/2     Running   0          6d8h
controlwd-96sbn      1/1     Running   0          6d8h
logsdb-0             1/1     Running   0          6d8h
logsui-7wkg2         1/1     Running   0          6d8h
metricsdb-0          1/1     Running   0          6d8h
metricsdc-28ffl      1/1     Running   0          6d8h
metricsui-k7qsh      1/1     Running   0          6d8h
mgmtproxy-gd84z      2/2     Running   0          6d8h
postgres01-0         3/3     Running   0          6h50m
postgres01-1         3/3     Running   0          6h50m
postgres01-2         3/3     Running   0          6h50m
postgres02-0         3/3     Running   0          22h
postgres02-1         3/3     Running   0          22h
postgres02-2         3/3     Running   0          22h
```

Ebben a példában a két létrehozott kiszolgálócsoportok tárolására szolgáló hat hüvely a következő:
- `postgres01-0`
- `postgres01-1`
- `postgres01-2`
- `postgres02-0`
- `postgres02-1`
- `postgres02-2`  

### <a name="what-server-group-pod-is-used-for-what-role-the-server-group"></a>Milyen Server Group Pod-t használ a kiszolgálócsoport szerepkörhöz?

Bármely, az utótaggal ellátott Pod-név `-0` koordinátori csomópontot képvisel. Minden olyan csomópont neve, amelynek utótagja `-x` 1 vagy nagyobb a munkavégző csomópont. A fenti példában:
- A koordinátorok a következők: `postgres01-0` , `postgres02-0`
- A dolgozók a következők: `postgres01-2` ,, `postgres01-2` `postgres02-1` , `postgres02-2`

### <a name="what-is-the-status-of-the-pods"></a>Mi a hüvely állapota?

Futtassa `kubectl get pods` a parancsot, és tekintse meg az oszlopot `STATUS`

### <a name="what-persistent-volume-claims-pvcs-are-being-used"></a>Milyen állandó mennyiségi jogcímeket (PVC-ket) használnak? 

Annak megismeréséhez, hogy a rendszer milyen PVC-ket használ, valamint az adatokat, a naplókat és a biztonsági mentéseket is használja, futtassa a következőt: 

```console
kubectl get pvc [-n <namespace name>]
```

Alapértelmezés szerint a PVC nevének előtagja a használatot jelzi:

- `backups-`...: a biztonsági mentéshez használt PVC
- `data-`...: az adatfájlok számára használt PVC
- `logs-`...: a tranzakciós naplókhoz és a WAL-fájlokhoz használt PVC

Például:

```output
NAME                                            STATUS   VOLUME              CAPACITY   ACCESS MODES   STORAGECLASS    AGE
backups-few7hh0k4npx9phsiobdc3hq-postgres01-0   Bound    local-pv-485e37db   1938Gi     RWO            local-storage   6d6h
backups-few7hh0k4npx9phsiobdc3hq-postgres01-1   Bound    local-pv-9d3d4a15   1938Gi     RWO            local-storage   6d6h
backups-few7hh0k4npx9phsiobdc3hq-postgres01-2   Bound    local-pv-7b8dd819   1938Gi     RWO            local-storage   6d6h
...
data-few7hh0k4npx9phsiobdc3hq-postgres01-0      Bound    local-pv-3c1a8cc5   1938Gi     RWO            local-storage   6d6h
data-few7hh0k4npx9phsiobdc3hq-postgres01-1      Bound    local-pv-8303ab19   1938Gi     RWO            local-storage   6d6h
data-few7hh0k4npx9phsiobdc3hq-postgres01-2      Bound    local-pv-55572fe6   1938Gi     RWO            local-storage   6d6h
...
logs-few7hh0k4npx9phsiobdc3hq-postgres01-0      Bound    local-pv-5e852b76   1938Gi     RWO            local-storage   6d6h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-1      Bound    local-pv-55d309a7   1938Gi     RWO            local-storage   6d6h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-2      Bound    local-pv-5ccd02e6   1938Gi     RWO            local-storage   6d6h
...
```


## <a name="from-an-azure-arc-enabled-data-services-point-of-view"></a>Egy Azure arc-kompatibilis adatszolgáltatási szempontból:

* Hány kiszolgálócsoport jön létre egy ív adatvezérlőben?
* Mik a nevük?
* Hány munkavégző csomópontot használnak?
* Milyen verziójú postgres futnak?

Használja a következő parancsok egyikét.
- **A kubectl:**

   ```console
   kubectl get postgresqls [-n <namespace name>]
   ``` 

   Például az alábbi kimenetet hozza létre, ahol minden sor egy `servergroup` . Az alábbi képen látható struktúra szerkezete a következőképpen alakul:

   `<Name-Of-Custom-Resource-Definition>`/`<Server-Group-Name>`

   ```output
   NAME                                             STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
   postgresql-12.arcdata.microsoft.com/postgres01   Ready   3/3          10.0.0.4:30499      7h15m
   postgresql-12.arcdata.microsoft.com/postgres02   Ready   3/3          10.0.0.4:31066      6d7h
   ```

   A fenti kimenet két, postgres 12-es verziójú kiszolgálói csoportot mutat be. Ha a verzió postgres 11 volt, a CRD neve Ehelyett postgresql-11.arcdata.microsoft.com lesz.

   Mindegyikük 3 csomóponton/hüvelyen fut: 1 koordinátoron és 2 feldolgozón.

- **A azdata:**

Futtassa az alábbi parancsot. A kimenet hasonló információkat jelenít meg a kubectl megjelenítéséhez:

   ```console
   azdata arc postgres server list

   `output
   Name        State    Workers
   ----------  -------  ---------
   postgres01  Ready    2
   postgres02  Ready    2
   ```


### <a name="how-much-memory-and-vcores-are-being-used-and-what-extensions-were-created-for-a-group"></a>Mennyi memóriát és virtuális mag használ, és milyen bővítmények lettek létrehozva egy csoport számára?

Futtassa a következő parancsok egyikét

**A Kubectl:**

A kubectl használatával írja le a postgres-erőforrásokat. Ehhez szüksége lesz a megfelelő postgres-verzióhoz tartozó Kubernetes-erőforrás (CRD) nevére, valamint a kiszolgálócsoport nevére.
A parancs általános formátuma a következő:

```console
kubectl describe <CRD name>/<server group name> [-n <namespace name>]
```

Például:

```console
kubectl describe postgresql-12/postgres02
```

Ez a parancs a kiszolgálócsoport konfigurációját jeleníti meg:

```output
Name:         postgres02
Namespace:    arc
Labels:       <none>
Annotations:  <none>
API Version:  arcdata.microsoft.com/v1alpha1
Kind:         postgresql-12
Metadata:
  Creation Timestamp:  2020-08-31T21:01:07Z
  Generation:          10
  Resource Version:    569516
  Self Link:           /apis/arcdata.microsoft.com/v1alpha1/namespaces/arc/postgresql-12s/postgres02
  UID:                 8a9cd118-361b-4a2e-8a9d-5f9257bf6abb
Spec:
  Engine:
    Extensions:
      Name:  citus
      Name:  pg_stat_statements
  Scale:
    Workers:  2
  Scheduling:
    Default:
      Resources:
        Limits:
          Cpu:     4
          Memory:  1024Mi
        Requests:
          Cpu:     1
          Memory:  512Mi
  Service:
    Type:  NodePort
  Storage:
    Data:
      Class Name:  local-storage
      Size:        5Gi
    Logs:
      Class Name:  local-storage
      Size:        5Gi
Status:
  External Endpoint:  10.0.0.4:31066
  Ready Pods:         3/3
  State:              Ready
Events:               <none>
```

>[!NOTE]
>Október 2020-os kiadás előtt `Workers` `Shards` az előző példában volt. További információért lásd a [kibocsátási megjegyzéseket – Azure arc-kompatibilis adatszolgáltatások (előzetes verzió)](release-notes.md) .

Nézzük meg, hogy vannak-e konkrét érdekes pontok a `servergroup` fent látható leírásban. Mit mond nekünk erről a kiszolgálócsoportról?

- A postgres 12-es verziója: 
   > ```json
   > Kind:         `postgresql-12`
   > ```
- A folyamat a 2020-es augusztus hónap során jött létre:
   > ```json
   > Creation Timestamp:  `2020-08-31T21:01:07Z`
   > ```
- Két postgres-bővítmény jött létre ebben a kiszolgálócsoportban: `citus` és `pg_stat_statements`
   > ```json
   > Engine:
   >    Extensions:
   >      Name:  `citus`
   >      Name:  `pg_stat_statements`
   > ```
- Két munkavégző csomópontot használ
   > ```json
   > Scale:
   >    Workers:  `2`
   > ```
- Minden csomóponton 1 CPU/virtuális mag és 512 MB RAM-ot használhat. Több mint 4 processzor-/virtuális mag és 1024MB fog használni:
   > ```json
   > Scheduling:
   >    Default: 
   >      Resources:
   >        Limits:
   >          Cpu:     4
   >          Memory:  1024Mi
   >        Requests:
   >          Cpu:     1
   >          Memory:  512Mi
   > ```
 - Lekérdezésekhez elérhető, és nem jelent problémát. Minden csomópont működik és fut:
   > ```json
   > Status:
   >  ...
   >  Ready Pods:         3/3
   >  State:              Ready
   > ```

**A azdata:**

A parancs általános formátuma:

```console
azdata arc postgres server show -n <server group name>
```

Például:

```console
azdata arc postgres server show -n postgres02
```

Az alábbi kimenetet adja vissza a kubectl által visszaadott formátum és tartalom számára.

```console
{
  "apiVersion": "arcdata.microsoft.com/v1alpha1",
  "kind": "postgresql-12",
  "metadata": {
    "creationTimestamp": "2020-08-31T21:01:07Z",
    "generation": 10,
    "name": "postgres02",
    "namespace": "arc",
    "resourceVersion": "569516",
    "selfLink": "/apis/arcdata.microsoft.com/v1alpha1/namespaces/arc/postgresql-12s/postgres02",
    "uid": "8a9cd118-361b-4a2e-8a9d-5f9257bf6abb"
  },
  "spec": {
    "engine": {
      "extensions": [
        {
          "name": "citus"
        },
        {
          "name": "pg_stat_statements"
        }
      ]
    },
    "scale": {
      "workers": 2
    },
    "scheduling": {
      "default": {
        "resources": {
          "limits": {
            "cpu": "4",
            "memory": "1024Mi"
          },
          "requests": {
            "cpu": "1",
            "memory": "512Mi"
          }
        }
      }
    },
    "service": {
      "type": "NodePort"
    },
    "storage": {
      "data": {
        "className": "local-storage",
        "size": "5Gi"
      },
      "logs": {
        "className": "local-storage",
        "size": "5Gi"
      }
    }
  },
  "status": {
    "externalEndpoint": "10.0.0.4:31066",
    "readyPods": "3/3",
    "state": "Ready"
  }
}
```

## <a name="next-steps"></a>Következő lépések
- [További információ az Azure arc-kompatibilis PostgreSQL nagy kapacitású kapcsolatos fogalmakról](concepts-distributed-postgres-hyperscale.md)
- [További információ arról, hogyan lehet felskálázást végezni (feldolgozói csomópontok hozzáadása)](scale-out-postgresql-hyperscale-server-group.md)
- [További információ a kiszolgálócsoport vertikális fel-és leskálázásáról (memória és/vagy virtuális mag növelésével vagy csökkentésével)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md)
- [További információ a tárolási konfigurációról](storage-configuration.md)
- [Olvassa el az adatbázis-példányok figyelését ismertető témakört.](monitor-grafana-kibana.md)
- [PostgreSQL-bővítmények használata az Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálói csoportban](using-extensions-in-postgresql-hyperscale-server-group.md)
- [Biztonság konfigurálása az Azure Arc-kompatibilis, rugalmas skálázású PostgreSQL-kiszolgálócsoport számára](configure-security-postgres-hyperscale.md)
