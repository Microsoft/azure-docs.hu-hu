---
title: Azure arc használatára képes PostgreSQL nagy kapacitású-kiszolgálócsoport törlése
description: Azure arc-kompatibilis postgres nagy kapacitású-kiszolgálócsoport törlése
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 7932ad3b30910e539acfbff2329a03f80a4d1a0b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "104670358"
---
# <a name="delete-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure arc használatára képes PostgreSQL nagy kapacitású-kiszolgálócsoport törlése

Ez a dokumentum azt ismerteti, hogyan törölhető egy kiszolgálócsoport az Azure arc telepítőjének használatával.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="delete-the-server-group"></a>A kiszolgálócsoport törlése

Tegyük fel például, hogy törölni szeretnénk a _postgres01_ -példányt az alábbi beállítások közül:

```console
azdata arc postgres server list
Name        State    Workers
----------  -------  ---------
postgres01  Ready    3
```

A DELETE parancs általános formátuma:
```console
azdata arc postgres server delete -n <server group name>
```
A parancs végrehajtásakor a rendszer kérni fogja, hogy erősítse meg a kiszolgálócsoport törlését. Ha parancsfájlokat használ a törlés automatizálásához, akkor a--Force paramétert kell használnia a megerősítő kérelem megkerüléséhez. Például a következőhöz hasonló parancsot kell futtatnia: 
```console
azdata arc postgres server delete -n <server group name> --force
```

A DELETE paranccsal kapcsolatos további információkért futtassa a következőt:
```console
azdata arc postgres server delete --help
```

### <a name="delete-the-server-group-used-in-this-example"></a>A példában használt kiszolgálócsoport törlése

```console
azdata arc postgres server delete -n postgres01
```

## <a name="reclaim-the-kubernetes-persistent-volume-claims-pvcs"></a>A Kubernetes állandó mennyiségi jogcímek (PVC-EK) visszaigénylése

Egy kiszolgálócsoport törlése nem távolítja el a hozzá tartozó [PVC](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)-ket. Ez az elvárt működés. A cél az, hogy segítsen a felhasználónak elérni az adatbázisfájlokat abban az esetben, ha a példány törlése véletlenül történt. A PVC-k törlése nem kötelező, de ajánlott. Ha nem szabadítja fel ezeket a PVC-ket, végül hibákba fog ütközni, mivel a Kubernetes-fürt úgy fogja érzékelni, hogy elfogy a lemezterület. Hajtsa végre a következő lépéseket a PVC-k felszabadításához:

### <a name="1-list-the-pvcs-for-the-server-group-you-deleted"></a>1. a törölt számítógépcsoport kilistázása

A PVC-ket a következő parancs futtatásával listázhatja:

```console
kubectl get pvc [-n <namespace name>]
```

A szolgáltatás visszaadja a PVC-ket, különösen a törölt kiszolgálócsoport virtuális adatmennyiségét. Például:

```output
kubectl get pvc
NAME                                         STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
data-few7hh0k4npx9phsiobdc3hq-postgres01-0   Bound    pvc-72ccc225-dad0-4dee-8eae-ed352be847aa   5Gi        RWO            default        2d18h
data-few7hh0k4npx9phsiobdc3hq-postgres01-1   Bound    pvc-ce6f0c51-faed-45ae-9472-8cdf390deb0d   5Gi        RWO            default        2d18h
data-few7hh0k4npx9phsiobdc3hq-postgres01-2   Bound    pvc-5a863ab9-522a-45f3-889b-8084c48c32f8   5Gi        RWO            default        2d18h
data-few7hh0k4npx9phsiobdc3hq-postgres01-3   Bound    pvc-00e1ace3-1452-434f-8445-767ec39c23f2   5Gi        RWO            default        2d15h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-0   Bound    pvc-8b810f4c-d72a-474a-a5d7-64ec26fa32de   5Gi        RWO            default        2d18h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-1   Bound    pvc-51d1e91b-08a9-4b6b-858d-38e8e06e60f9   5Gi        RWO            default        2d18h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-2   Bound    pvc-8e5ad55e-300d-4353-92d8-2e383b3fe96e   5Gi        RWO            default        2d18h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-3   Bound    pvc-f9e4cb98-c943-45b0-aa07-dd5cff7ea585   5Gi        RWO            default        2d15h
```
Ehhez a kiszolgálócsoporthoz 8 PVC szükséges.

### <a name="2-delete-each-of-the-pvcs"></a>2. törölje az összes PVC-t

Törölje az összes olyan PostgreSQL nagy kapacitású-csomópontra (koordinátorra és feldolgozóra) vonatkozó adatmennyiséget és log PVC-t, amelyet törölt.

A parancs általános formátuma a következő: 

```console
kubectl delete pvc <name of pvc>  [-n <namespace name>]
```

Például:

```console
kubectl delete pvc data-few7hh0k4npx9phsiobdc3hq-postgres01-0
kubectl delete pvc data-few7hh0k4npx9phsiobdc3hq-postgres01-1
kubectl delete pvc data-few7hh0k4npx9phsiobdc3hq-postgres01-2
kubectl delete pvc data-few7hh0k4npx9phsiobdc3hq-postgres01-3
kubectl delete pvc logs-few7hh0k4npx9phsiobdc3hq-postgres01-0
kubectl delete pvc logs-few7hh0k4npx9phsiobdc3hq-postgres01-1
kubectl delete pvc logs-few7hh0k4npx9phsiobdc3hq-postgres01-2
kubectl delete pvc logs-few7hh0k4npx9phsiobdc3hq-postgres01-3
```

Ezen kubectl-parancsok mindegyike megerősíti a PVC sikeres törlését. Például:

```output
persistentvolumeclaim "data-postgres01-0" deleted
```
  

>[!NOTE]
> Ahogy azt a jelzett módon nem törli, előfordulhat, hogy a Kubernetes-fürtöt a hibák elhárítása során esetlegesen lekéri a rendszer. Előfordulhat, hogy az ilyen hibák némelyike nem tud bejelentkezni a Kubernetes-fürtbe a azdata, mivel a hüvelyek kizárják őket a tárolási probléma miatt (normál Kubernetes viselkedés).
>
> Előfordulhat például, hogy a naplók üzenetei a következőhöz hasonlóak:  
> ```output
> Annotations:    microsoft.com/ignore-pod-health: true  
> Status:         Failed  
> Reason:         Evicted  
> Message:        The node was low on resource: ephemeral-storage. Container controller was using 16372Ki, which exceeds its request of 0.
> ```
    
## <a name="next-step"></a>Következő lépés
[Azure arc-kompatibilis PostgreSQL-nagy kapacitású](create-postgresql-hyperscale-server-group.md) létrehozása
