---
title: Azure arc-kompatibilis felügyelt példány magas rendelkezésre állása
titleSuffix: Deploy Azure Arc enabled Managed Instance with high availability
description: Megtudhatja, hogyan helyezheti üzembe a magas rendelkezésre állású Azure arc-kompatibilis felügyelt példányt.
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 03/02/2021
ms.topic: conceptual
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: e04b9b98db26b3b9a024a60c6f82820fe20fcbf1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693102"
---
# <a name="azure-arc-enabled-managed-instance-high-availability"></a>Azure arc-kompatibilis felügyelt példány magas rendelkezésre állása

Az Azure arc-kompatibilis felügyelt példány a Kubernetes tároló alkalmazásként van telepítve, és olyan Kubernetes-szerkezeteket használ, mint például az állapot-nyilvántartó készletek és a tartós tárolás, hogy beépített állapot-figyelést, meghibásodás-észlelést és feladatátvételi mechanizmusokat biztosítson a szolgáltatás állapotának fenntartása érdekében. A nagyobb megbízhatóság érdekében az Azure arc használatára képes felügyelt példányt úgy is konfigurálhatja, hogy további replikákkal telepítsen egy magas rendelkezésre állású konfigurációban. A figyelést, a hibák észlelését és az automatikus feladatátvételt az arc-adatszolgáltatások adatvezérlője kezeli. Ez a szolgáltatás felhasználói beavatkozás nélkül, a rendelkezésre állási csoport beállításával, az adatbázis-tükrözési végpontok konfigurálásával, adatbázisok a rendelkezésre állási csoportba való felvételével, illetve a feladatátvételi és frissítési koordinációval biztosítható. Ez a dokumentum mindkét típusú magas rendelkezésre állást vizsgálja.

## <a name="built-in-high-availability"></a>Beépített magas rendelkezésre állás 

A Kubernetes beépített magas rendelkezésre állást biztosít, ha a távoli állandó tárterület be van állítva, és az ív adatszolgáltatásának telepítése által használt csomópontokkal van megosztva. Ebben a konfigurációban a Kubernetes a fürt Orchestrator szerepét játssza le. Ha a felügyelt példány egy tárolóban vagy az alapul szolgáló csomópont meghibásodik, akkor a Orchestrator a tároló egy másik példányát, és ugyanahhoz az állandó tárolóhoz csatolja. Ez a típus alapértelmezés szerint engedélyezve van az Azure arc-kompatibilis felügyelt példány telepítésekor.

### <a name="verify-built-in-high-availability"></a>Beépített magas rendelkezésre állás ellenőrzése

Ez a szakasz a Kubernetes által biztosított beépített magas rendelkezésre állást ellenőrzi. Ha követi a funkció tesztelésének lépéseit, törölheti egy meglévő felügyelt példányhoz tartozó Pod-t, és ellenőrizheti, hogy a Kubernetes helyreállítja-e ezt a műveletet. 

### <a name="prerequisites"></a>Előfeltételek

- A Kubernetes-fürtnek [megosztott, távoli tárolóval](https://docs.microsoft.com/en-us/azure/azure-arc/data/storage-configuration#factors-to-consider-when-choosing-your-storage-configuration) kell rendelkeznie 
- Egy replikával telepített Azure arc-kompatibilis felügyelt példány (alapértelmezett)

1. A hüvelyek megtekintése. 

   ```console
   kubectl get pods -n <namespace of data controller>
   ```

2. Törölje a felügyelt példány Pod-t.

   ```console
   kubectl delete pod <name of managed instance>-0 -n <namespace of data controller>
   ```

   Példa:

   ```output
   user@pc:/# kubectl delete pod sql1-0 -n arc
   pod "sql1-0" deleted
   ```

3. A hüvelyek megtekintésével ellenőrizheti, hogy a felügyelt példány helyreállítása történik-e.

   ```console
   kubectl get pods -n <namespace of data controller>
   ```

   Példa:

   ```output
   user@pc:/# kubectl get pods -n arc
   NAME                 READY   STATUS    RESTARTS   AGE
   sql1-0               2/3     Running   0          22s
   ```

Miután a hüvelyben lévő összes tároló helyreállt, csatlakozhat a felügyelt példányhoz.

## <a name="deploy-with-always-on-availability-groups"></a>Üzembe helyezés always on rendelkezésre állási csoportokkal

A nagyobb megbízhatóság érdekében beállíthatja, hogy az Azure arc-kompatibilis felügyelt példányok további replikákkal is üzembe helyezhetők magas rendelkezésre állású konfigurációban. 

A rendelkezésre állási csoportokat engedélyező képességek:

- Több replikával való üzembe helyezés esetén egyetlen rendelkezésre állási csoport `containedag` jön létre. Alapértelmezés szerint `containedag` három replikával rendelkezik, beleértve az elsődlegest is. A rendelkezésre állási csoport összes szifilisz-művelete belsőleg van kezelve, beleértve a rendelkezésre állási csoport létrehozását vagy a replikák a létrehozott rendelkezésre állási csoportba való csatlakoztatását. További rendelkezésre állási csoportok nem hozhatók létre az Azure arc-kompatibilis felügyelt példányban.

- A rendszer az összes adatbázist automatikusan hozzáadja a rendelkezésre állási csoporthoz, beleértve az összes olyan felhasználói és rendszeradatbázist, mint a `master` és a `msdb` . Ez a funkció egyetlen rendszernézetet biztosít a rendelkezésre állási csoport replikái között. Figyelje `containedag_master` `containedag_msdb` meg a és az adatbázisokat is, ha közvetlenül a példányhoz csatlakozik. Az `containedag_*` adatbázisok a `master` `msdb` rendelkezésre állási csoportban lévő és azokon belüli csoportokat jelölik.

- A rendszer automatikusan kiépít egy külső végpontot a rendelkezésre állási csoportba tartozó adatbázisokhoz való csatlakozáshoz. Ez a végpont a `<managed_instance_name>-svc-external` rendelkezésre állási csoport figyelője szerepkörét játssza le.

### <a name="deploy"></a>Üzembe helyezés

A rendelkezésre állási csoportokkal rendelkező felügyelt példányok üzembe helyezéséhez futtassa a következő parancsot.

```console
azdata arc sql mi create -n <name of instance> --replicas 3
```

### <a name="check-status"></a>Állapot ellenőrzése
A példány üzembe helyezését követően futtassa a következő parancsokat a példány állapotának vizsgálatához:

```console
azdata arc sql mi list
azdata arc sql mi show -n <name of instance>
```

Példa a kimenetre:

```output
user@pc:/# azdata arc sql mi list
ExternalEndpoint    Name    Replicas    State
------------------  ------  ----------  -------
20.131.31.58,1433   sql2    3/3         Ready

user@pc:/#  azdata arc sql mi show -n sql2
{
...
  "status": {
    "AGStatus": "Healthy",
    "externalEndpoint": "20.131.31.58,1433",
    "logSearchDashboard": "link to logs dashboard",
    "metricsDashboard": "link to metrics dashboard",
    "readyReplicas": "3/3",
    "state": "Ready"
  }
}
```

Figyelje meg a `Replicas` `AGstatus` rendelkezésre állási csoport állapotát jelző további számot és a mezőt. Ha az összes replika fel van hajtva és szinkronizálva van, akkor ez az érték `healthy` . 

### <a name="restore-a-database"></a>Adatbázis helyreállítása 
Az adatbázisok rendelkezésre állási csoportba való visszaállításához további lépések szükségesek. A következő lépések bemutatják, hogyan állíthatja vissza az adatbázist egy felügyelt példányra, és hogyan adhatja hozzá egy rendelkezésre állási csoporthoz. 

1. Tegye elérhetővé az elsődleges példány külső végpontját egy új Kubernetes-szolgáltatás létrehozásával.

    Határozza meg az elsődleges replikát futtató Pod-t a felügyelt példányhoz való csatlakozással, majd futtassa a következőt:

    ```sql
    SELECT @@SERVERNAME
    ```
    Hozza létre az kubernetes szolgáltatást az elsődleges példányhoz az alábbi parancs futtatásával, ha a kubernetes-fürt nodePort szolgáltatásokat használ. Cserélje le az értéket az `podName` előző lépésben visszaadott kiszolgáló nevére, `serviceName` a létrehozott Kubernetes szolgáltatás előnyben részesített nevével.

    ```bash
    kubectl -n <namespaceName> expose pod <podName> --port=1533  --name=<serviceName> --type=NodePort
    ```

    Terheléselosztó-szolgáltatás esetén futtassa ugyanazt a parancsot, azzal a különbséggel, hogy a létrehozott szolgáltatás típusa: `LoadBalancer` . Például: 

    ```bash
    kubectl -n <namespaceName> expose pod <podName> --port=1533  --name=<serviceName> --type=LoadBalancer
    ```

    Íme egy példa erre a parancsra az Azure Kubernetes Service-ben, ahol az elsődleges helyet üzemeltető Pod a következő `sql2-0` :

    ```bash
    kubectl -n arc-cluster expose pod sql2-0 --port=1533  --name=sql2-0-p --type=LoadBalancer
    ```

    Szerezze be a létrehozott Kubernetes-szolgáltatás IP-címét:

    ```bash
    kubectl get services -n <namespaceName>
    ```
2. Állítsa vissza az adatbázist az elsődleges példány végpontján.

    Adja hozzá az adatbázis biztonságimásolat-fájlját az elsődleges példány tárolóhoz.

    ```console
    kubectl cp <source file location> <pod name>:var/opt/mssql/data/<file name> -n <namespace name>
    ```

    Példa

    ```console
    kubectl cp /home/WideWorldImporters-Full.bak sql2-1:var/opt/mssql/data/WideWorldImporters-Full.bak -c arc-sqlmi -n arc
    ```

    Állítsa vissza az adatbázis biztonságimásolat-fájlját az alábbi parancs futtatásával.

    ```sql 
    RESTORE DATABASE test FROM DISK = '/var/opt/mssql/data/<file name>.bak'
    WITH MOVE '<database name>' to '/var/opt/mssql/data/<file name>.mdf'  
    ,MOVE '<database name>' to '/var/opt/mssql/data/<file name>_log.ldf'  
    ,RECOVERY, REPLACE, STATS = 5;  
    GO
    ```
    
    Példa

    ```sql
    RESTORE Database WideWorldImporters
    FROM DISK = '/var/opt/mssql/data/WideWorldImporters-Full.BAK'
    WITH
    MOVE 'WWI_Primary' TO '/var/opt/mssql/data/WideWorldImporters.mdf',
    MOVE 'WWI_UserData' TO '/var/opt/mssql/data/WideWorldImporters_UserData.ndf',
    MOVE 'WWI_Log' TO '/var/opt/mssql/data/WideWorldImporters.ldf',
    MOVE 'WWI_InMemory_Data_1' TO '/var/opt/mssql/data/WideWorldImporters_InMemory_Data_1',
    RECOVERY, REPLACE, STATS = 5;  
    GO
    ```

3. Adja hozzá az adatbázist a rendelkezésre állási csoporthoz.

    Ahhoz, hogy az adatbázis hozzá legyen adva az AG-hoz, teljes helyreállítási módban kell futnia, és a napló biztonsági mentésére van szükség. Az alábbi TSQL-utasítások futtatásával adja hozzá a visszaállított adatbázist a rendelkezésre állási csoporthoz.

    ```sql
    ALTER DATABASE <databaseName> SET RECOVERY FULL;
    BACKUP DATABASE <databaseName> TO DISK='<filePath>'
    ALTER AVAILABILITY GROUP containedag ADD DATABASE <databaseName>
    ```

    A következő példa egy nevű adatbázist hoz létre `WideWorldImporters` , amely a példányon lett visszaállítva:

    ```sql
    ALTER DATABASE WideWorldImporters SET RECOVERY FULL;
    BACKUP DATABASE WideWorldImporters TO DISK='/var/opt/mssql/data/WideWorldImporters.bak'
    ALTER AVAILABILITY GROUP containedag ADD DATABASE WideWorldImporters
    ```

> [!IMPORTANT]
> Ajánlott eljárásként a következő parancs futtatásával törölje a fent létrehozott Kubernetes szolgáltatás törlését:
>
>```bash
>kubectl delete svc sql2-0-p -n arc
>```

### <a name="limitations"></a>Korlátozások

Az Azure arc-kompatibilis felügyelt példány rendelkezésre állási csoportjai ugyanazokkal a [korlátozásokkal rendelkeznek, mint a Big adatfürt rendelkezésre állási csoportjai. További információért kattintson ide.](/sql/big-data-cluster/deployment-high-availability#known-limitations)

## <a name="next-steps"></a>Következő lépések

További információ [Az Azure arc-kompatibilis SQL felügyelt példány szolgáltatásairól és képességeiről](managed-instance-features.md)
