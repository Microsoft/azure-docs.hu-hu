---
title: Az Azure arc használatára képes felügyelt SQL-példány törlése
description: Az Azure arc használatára képes felügyelt SQL-példány törlése
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: e531349e8f404380d9f0601caa3b66557c297062
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90940848"
---
# <a name="delete-azure-arc-enabled-sql-managed-instance"></a>Az Azure arc használatára képes felügyelt SQL-példány törlése
Ez a cikk azt ismerteti, hogyan törölhető az Azure arc használatára képes felügyelt SQL-példány.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="view-existing-azure-arc-enabled-sql-managed-instances"></a>Meglévő Azure arc-kompatibilis SQL felügyelt példányok megtekintése
Az SQL felügyelt példányainak megtekintéséhez futtassa a következő parancsot:

```console
azdata arc sql mi list
```

A kimenetnek a következőhöz hasonlóan kell kinéznie:

```console
Name    Replicas    ServerEndpoint    State
------  ----------  ----------------  -------
demo-mi 1/1         10.240.0.4:32023  Ready
```

## <a name="delete-a-azure-arc-enabled-sql-managed-instance"></a>Azure arc-kompatibilis SQL felügyelt példány törlése
A felügyelt SQL-példányok törléséhez futtassa a következő parancsot:

```console
azdata arc sql mi delete -n <NAME_OF_INSTANCE>
```

A kimenetnek a következőhöz hasonlóan kell kinéznie:

```console
# azdata arc sql mi delete -n demo-mi
Deleted demo-mi from namespace arc
```

## <a name="reclaim-the-kubernetes-persistent-volume-claims-pvcs"></a>A Kubernetes állandó mennyiségi jogcímek (PVC-EK) visszaigénylése

A felügyelt SQL-példányok törlése nem távolítja el a hozzá tartozó [PVC](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)-ket. Ez az elvárt működés. A cél az, hogy segítsen a felhasználónak elérni az adatbázisfájlokat abban az esetben, ha a példány törlése véletlenül történt. A PVC-k törlése nem kötelező, de ajánlott. Ha nem állítja vissza ezeket a PVC-ket, a rendszer végül hibákba ütközik, mivel a Kubernetes-fürtön nincs elég szabad lemezterület. Hajtsa végre a következő lépéseket a PVC-k felszabadításához:

### <a name="1-list-the-pvcs-for-the-server-group-you-deleted"></a>1. a törölt számítógépcsoport kilistázása
A PVC-ket a következő parancs futtatásával listázhatja:
```console
kubectl get pvc
```

Az alább látható példában figyelje meg a törölt SQL felügyelt példányok virtuális adatpvc-jét.
```console
# kubectl get pvc -n arc

NAME                    STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS      AGE
data-demo-mi-0        Bound     pvc-1030df34-4b0d-4148-8986-4e4c20660cc4   5Gi        RWO            managed-premium   13h
logs-demo-mi-0        Bound     pvc-11836e5e-63e5-4620-a6ba-d74f7a916db4   5Gi        RWO            managed-premium   13h
```

### <a name="2-delete-each-of-the-pvcs"></a>2. törölje az összes PVC-t
Törölje az összes törölt SQL felügyelt példányhoz tartozó adatmennyiséget és a naplózási adatkészletet.
A parancs általános formátuma a következő: 
```console
kubectl delete pvc <name of pvc>
```

Például:
```console
kubectl delete pvc data-demo-mi-0 -n arc
kubectl delete pvc logs-demo-mi-0 -n arc
```

Ezen kubectl-parancsok mindegyike megerősíti a PVC sikeres törlését. Például:
```console
persistentvolumeclaim "data-demo-mi-0" deleted
persistentvolumeclaim "logs-demo-mi-0" deleted
```
  

> [!NOTE]
> Ahogy azt a jelzett módon nem törli, előfordulhat, hogy a Kubernetes-fürtöt a hibák elhárítása során esetlegesen lekéri a rendszer. Előfordulhat, hogy az ilyen hibák némelyike nem tud bejelentkezni a Kubernetes-fürtbe a azdata, mivel a hüvelyek kizárják őket a tárolási probléma miatt (normál Kubernetes viselkedés).
>
> Előfordulhat például, hogy a naplók üzenetei a következőhöz hasonlóak:  
> - Jegyzetek: microsoft.com/ignore-pod-health: true  
> - Állapot: sikertelen  
> - Ok: kizárt  
> - Üzenet: a csomópont alacsony volt a (z) erőforráson: ideiglenes tároló. A Container Controller a 16372Ki-t használta, ami meghaladja a 0-as kérelmét.

## <a name="next-steps"></a>Következő lépések

További információ [Az Azure arc-kompatibilis SQL felügyelt példány szolgáltatásairól és képességeiről](managed-instance-features.md)

[Első lépések adatkezelő létrehozásával](create-data-controller.md)

Már létrehozott egy adatvezérlőt? [Azure arc használatára képes SQL felügyelt példány létrehozása](create-sql-managed-instance.md)