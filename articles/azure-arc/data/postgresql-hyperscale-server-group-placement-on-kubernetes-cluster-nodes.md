---
title: A PostgreSQL nagy kapacitású-kiszolgálócsoport elhelyezése a Kubernetes-fürt csomópontjain
description: Ismerteti, hogyan történik a PostgreSQL nagy kapacitású-kiszolgálói csoportot alkotó PostgreSQL-példányok elhelyezése a Kubernetes-fürt csomópontjain.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: b88b36ba8ec1d2d612adbbf19a6cf1e91fbb2cfd
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100377754"
---
# <a name="azure-arc-enabled-postgresql-hyperscale-server-group-placement"></a>Azure arc engedélyezve PostgreSQL nagy kapacitású Server Group elhelyezése

Ebben a cikkben egy példát mutatunk be arra, hogy az Azure arc-alapú PostgreSQL nagy kapacitású-kiszolgáló csoportjának PostgreSQL-példányai az azokat üzemeltető Kubernetes-fürt fizikai csomópontjain legyenek elhelyezve. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="configuration"></a>Konfiguráció

Ebben a példában egy olyan Azure Kubernetes Service-(ak-) fürtöt használunk, amely négy fizikai csomóponttal rendelkezik. 

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/1_cluster_portal.png" alt-text="4 csomópontos AK-fürt Azure Portal":::

A Kubernetes-fürt fizikai csomópontjainak listázása. Futtassa a következő parancsot:

```console
kubectl get nodes
```

`kubectl` négy fizikai csomópontot ad vissza a Kubernetes-fürtön belül:

```output
NAME                                STATUS   ROLES   AGE   VERSION
aks-agentpool-42715708-vmss000000   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000001   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000002   Ready    agent   11h   v1.17.9
aks-agentpool-42715708-vmss000003   Ready    agent   11h   v1.17.9
```

Az architektúra a következőképpen jeleníthető meg:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/2_logical_cluster.png" alt-text="4 csomópont logikai ábrázolása egy Kubernetes-fürtben csoportosítva":::

A Kubernetes-fürt egyetlen Azure arc-adatvezérlőt és egy Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálócsoport-kiszolgálót üzemeltet. Ez a kiszolgálócsoport három PostgreSQL-példányból áll: egy koordinátorból és két feldolgozóból áll.

A hüvelyek listázása a paranccsal:

```console
kubectl get pods -n arc3
```
`kubectl` adja vissza

```output
NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01c-0         3/3     Running   0          9h
postgres01w-0         3/3     Running   0          9h
postgres01w-1         3/3     Running   0          9h
```
Ezek a hüvelyek egy PostgreSQL-példányt futtatnak. Együtt, a hüvelyek alkotják az Azure arc engedélyezése PostgreSQL nagy kapacitású Server Group:

```output
Pod name        Role in the server group
postgres01c-0 Coordinator
postgres01w-0   Worker
postgres01w-1   Worker
```

## <a name="placement"></a>Elhelyezés
Nézzük meg, hogyan helyezi el a Kubernetes a csoport hüvelyeit. Írja le az egyes Pod-ket, és azonosítsa, hogy a Kubernetes-fürt mely fizikai csomópontján vannak elhelyezve. A koordinátor számára például futtassa a következő parancsot:

```console
kubectl describe pod postgres01c-0 -n arc3
```

`kubectl` adja vissza

```output
Name:         postgres01c-0
Namespace:    arc3
Priority:     0
Node:         aks-agentpool-42715708-vmss000000
Start Time:   Thu, 17 Sep 2020 00:40:33 -0700
…
```

Ahogy ezt a parancsot az egyes hüvelyek esetében futtatjuk, az aktuális elhelyezést a következőképpen összegzi:

| Kiszolgálói csoport szerepköre|Kiszolgálócsoport Pod|A pod helyet adó fizikai csomópont Kubernetes |
|--|--|--|
| Feldolgozói|postgres01 – 1|AK-agentpool-42715708-vmss000002 |
| Feldolgozói|postgres01 – 2|AK-agentpool-42715708-vmss000003 |

Továbbá jegyezze fel a hüvelyek leírását is, az egyes Pod-gazdagépek nevét. A második feldolgozóhoz például futtassa a következő parancsot:

```console
kubectl describe pod postgres01w-1 -n arc3
```

`kubectl` adja vissza

```output
…
Node:         aks-agentpool-42715708-vmss000003/10.240.0.7
..
Containers:
  Fluentbit:
…
  Postgres:
…
  Telegraf:
…
```

Az Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálócsoport részét képező összes Pod a következő három tárolót tárolja:

|Tárolók|Description
|----|----|
|`Fluentbit` |Adatnapló-gyűjtő: https://fluentbit.io/
|`Postgres`|Az Azure arc engedélyezett PosgreSQL nagy kapacitású-kiszolgálócsoport részét képező PostgreSQL példány része
|`Telegraf` |Metrikák gyűjtője: https://www.influxdata.com/time-series-platform/telegraf/

Az architektúra a következőképpen néz ki:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/3_pod_placement.png" alt-text="3 hüvely külön csomópontokra helyezve":::

Ez azt jelenti, hogy ezen a ponton az Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálócsoport részét képező PostgreSQL-példányok a Kubernetes-tárolóban található adott fizikai gazdagépen futnak. Ez a konfiguráció biztosítja a legnagyobb teljesítményt az Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálócsoport számára, mivel az egyes szerepkörök (koordinátorok és feldolgozók) minden egyes fizikai csomópont erőforrásait használják. Ezeket az erőforrásokat a rendszer nem osztja meg több PostgreSQL-szerepkör között.

## <a name="scale-out-azure-arc-enabled-postgresql-hyperscale"></a>Az Azure arc engedélyezése a PostgreSQL nagy kapacitású

Most hozzon létre egy harmadik feldolgozói csomópontot a kiszolgálócsoport számára, és figyelje meg, mi történik. Létrehoz egy negyedik PostgreSQL-példányt, amely egy negyedik Pod-ban lesz tárolva.
A kiskálázáshoz futtassa a következő parancsot:

```console
azdata arc postgres server edit --name postgres01 --workers 3
```

A következő kimenetet eredményezi:

```output
Updating postgres01 in namespace `arc3`
postgres01 is Ready
```

Sorolja fel az Azure arc-adatkezelőben üzembe helyezett kiszolgálócsoportok listáját, és ellenőrizze, hogy a kiszolgálócsoport most már három feldolgozóval fut-e. Futtassa a következő parancsot:

```console
azdata arc postgres server list
```

És figyelje meg, hogy két feldolgozóból három feldolgozóra kibővíthető:

```output
Name        State    Workers
----------  -------  ---------
postgres01  Ready    3
```

Ahogy korábban tettük, figyelje meg, hogy a kiszolgálócsoport már összesen négy hüvelyt használ:

```console
kubectl get pods -n arc3
```

```output
NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01c-0         3/3     Running   0          11h
postgres01w-0         3/3     Running   0          11h
postgres01w-1         3/3     Running   0          11h
postgres01w-2         3/3     Running   0          5m2s
```

És írja le az új Pod-t annak azonosítására, hogy a Kubernetes-fürt mely fizikai csomópontjain fut.
Futtassa a következő parancsot:

```console
kubectl describe pod postgres01w-2 -n arc3
```

Az üzemeltetési csomópont nevének azonosítása:

```output
Name:         postgres01w-2
Namespace:    arc3
Priority:     0
Node:         aks-agentpool-42715708-vmss000000
```

A PostgreSQL-példányok elhelyezése a fürt fizikai csomópontjain most:

|Kiszolgálói csoport szerepköre|Kiszolgálócsoport Pod|A pod helyet adó fizikai csomópont Kubernetes
|-----|-----|-----
|Coordinator|postgres01 – 0|AK-agentpool-42715708-vmss000000
|Feldolgozói|postgres01 – 1|AK-agentpool-42715708-vmss000002
|Feldolgozói|postgres01 – 2|AK-agentpool-42715708-vmss000003
|Feldolgozói|postgres01 – 3|AK-agentpool-42715708-vmss000000

Figyelje meg, hogy az új feldolgozó (postgres01w-2) Pod-je ugyanarra a csomópontra került, mint a koordinátor. 

Az architektúra a következőképpen néz ki:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/4_pod_placement_.png" alt-text="Negyedik Pod ugyanazon a csomóponton, mint a koordinátor":::

Miért nem az új feldolgozó/Pod kerül a Kubernetes-fürt (ak-agentpool-42715708-vmss000003) fennmaradó fizikai csomópontjára?

Ennek az az oka, hogy a Kubernetes-fürt utolsó fizikai csomópontja több hüvelyt üzemeltet, amelyek az Azure arc-kompatibilis adatszolgáltatások futtatásához szükséges további összetevőket működtetnek. A Kubernetes azt vizsgálta, hogy a legjobb jelölt – az ütemezés időpontjában – a további dolgozó üzemeltetése az AK-agentpool-42715708-vmss000000 fizikai csomópont. 

Ugyanazokat a parancsokat használja, mint a fentiekben; láthatjuk, hogy az egyes fizikai csomópontok hol találhatók:

|Egyéb hüvelyek nevei\* |Használat|A hüvelyt üzemeltető fizikai csomópont Kubernetes
|----|----|----
|bootstrapper – jh48b|Olyan szolgáltatás, amely kezeli a bejövő kérelmeket olyan egyéni erőforrások létrehozásához, szerkesztéséhez és törléséhez, mint például az SQL felügyelt példányai, a PostgreSQL nagy kapacitású-kiszolgálócsoportok és az adatkezelők|AK-agentpool-42715708-vmss000003
|vezérlés – gwmbs||AK-agentpool-42715708-vmss000002
|controldb – 0|Az adatkezelő konfigurációjának és állapotának tárolására szolgáló vezérlő-adattár.|AK-agentpool-42715708-vmss000001
|controlwd-zzjp7|A "videó megtekintése" szolgáltatást, amely figyelemmel kíséri az adatkezelő rendelkezésre állását.|AK-agentpool-42715708-vmss000000
|logsdb – 0|Rugalmas keresési példány, amely az összes arc adatszolgáltatási hüvelyben gyűjtött összes napló tárolására szolgál. Elasticsearch, fogadja az `Fluentbit` egyes Pod-tárolók adatait|AK-agentpool-42715708-vmss000003
|logsui-5fzv5|A rugalmas keresési adatbázisra épülő Kibana-példány, amely log Analytics grafikus felhasználói felületet mutat be.|AK-agentpool-42715708-vmss000003
|metricsdb – 0|Egy InfluxDB-példány, amely az összes ív adatszolgáltatási hüvelyben gyűjtött összes metrika tárolására szolgál. InfluxDB, fogadja az `Telegraf` egyes Pod-tárolók adatait|AK-agentpool-42715708-vmss000000
|metricsdc-47d47|A fürt összes Kubernetes-csomópontján központilag telepített démon a csomópontok csomópont szintű metrikáinak gyűjtésére.|AK-agentpool-42715708-vmss000002
|metricsdc-864kj|A fürt összes Kubernetes-csomópontján központilag telepített démon a csomópontok csomópont szintű metrikáinak gyűjtésére.|AK-agentpool-42715708-vmss000001
|metricsdc-l8jkf|A fürt összes Kubernetes-csomópontján központilag telepített démon a csomópontok csomópont szintű metrikáinak gyűjtésére.|AK-agentpool-42715708-vmss000003
|metricsdc-nxm4l|A fürt összes Kubernetes-csomópontján központilag telepített démon a csomópontok csomópont szintű metrikáinak gyűjtésére.|AK-agentpool-42715708-vmss000000
|metricsui-4fb7l|Egy Grafana-példány, amely a InfluxDB-adatbázis tetején található, és a figyelési irányítópult grafikus felhasználói felületét mutatja be.|AK-agentpool-42715708-vmss000003
|mgmtproxy-4qppp|Egy webalkalmazás-proxy réteg, amely a Grafana és a Kibana példányok előtt helyezkedik el.|AK-agentpool-42715708-vmss000002

> \* A pod-nevek utótagja eltérő lesz a többi üzemelő példányon. Emellett itt csak az Azure arc-adatkezelő Kubernetes névterében tárolt hüvelyek jelennek meg.

Az architektúra a következőképpen néz ki:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/5_full_list_of_pods.png" alt-text="A névtérben lévő összes hüvely a különböző csomópontokon":::

A fentiekben leírtak szerint az Azure arc-kompatibilis postgres nagy kapacitású-kiszolgálócsoport koordinátori csomópontjai (Pod 1) ugyanazokkal a fizikai erőforrásokkal rendelkeznek, mint a kiszolgálócsoport harmadik munkavégző csomópontjának (Pod 4). Ez elfogadható, mert a koordinátori csomópont általában nagyon kevés erőforrást használ a feldolgozói csomópontok által használthoz képest. Ezért gondosan válassza a következőt:
- a Kubernetes-fürt mérete és az egyes fizikai csomópontok jellemzői (memória, virtuális mag)
- a Kubernetes-fürtön belüli fizikai csomópontok száma
- a Kubernetes-fürtön üzemeltetett alkalmazások vagy munkaterhelések.

A túl sok számítási feladat a Kubernetes-fürtön való üzemeltetésének a szabályozása az Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálócsoport esetében fordulhat elő. Ha ez történik, akkor nem fog sokat kihasználni a képességeitől a horizontális skálázásig. A rendszerből kinyert teljesítmény nem csupán a fizikai csomópontok elhelyezésére vagy fizikai jellemzőire vagy a tárolási rendszerre mutat. A kapott teljesítmény arról is szól, hogyan konfigurálhatja a Kubernetes-fürtön belül futó összes erőforrást (beleértve az Azure arc-kompatibilis PostgreSQL-nagy kapacitású), például a memória-és virtuális mag beállított kérelmeket és korlátozásokat. Az adott Kubernetes-fürtön üzemeltethető számítási feladatok mennyisége a Kubernetes-fürt jellemzőihez, a munkaterhelések természetétől, a felhasználók számához, a Kubernetes-fürt műveleteinek módjához képest...

## <a name="scale-out-aks"></a>Vertikális felskálázás

Azt mutatjuk be, hogy az AK-fürt és az Azure arc engedélyezve PostgreSQL nagy kapacitású-kiszolgáló horizontális skálázása a lehető legtöbbet tudja kihasználni az Azure arc-kompatibilis PostgreSQL-nagy kapacitású nagy teljesítményével.
Vegyünk fel egy ötödik csomópontot az AK-fürthöz:

:::row:::
    :::column:::
        Előtte
    :::column-end:::
    :::column:::
        Utána
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        :::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/6_layout_before.png" alt-text="Azure Portal az elrendezés előtt":::
    :::column-end:::
    :::column:::
        :::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/7_layout_after.png" alt-text="Azure Portal az elrendezés után":::
    :::column-end:::
:::row-end:::

Az architektúra a következőképpen néz ki:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/8_logical_layout_after.png" alt-text="Logikai elrendezés a Kubernetes-fürtön a frissítés után":::

Nézzük meg, hogy az ív-adatkezelő névterének milyen hüvelyei futnak az új AK fizikai csomóponton a parancs futtatásával:

```console
kubectl describe node aks-agentpool-42715708-vmss000004
```

És frissítjük a rendszer architektúrájának ábrázolását:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/9_updated_list_of_pods.png" alt-text="Minden hüvely a fürt logikai diagramján":::

Bemutatjuk, hogy a Kubernetes-fürt új fizikai csomópontja csak az Azure arc-adatszolgáltatásokhoz szükséges metrikákat üzemelteti. Vegye figyelembe, hogy ebben a példában csak az ív-adatvezérlő névterére koncentrálunk, a többi hüvelyt nem jelöljük.

## <a name="scale-out-azure-arc-enabled-postgresql-hyperscale-again"></a>Az Azure arc engedélyezése a PostgreSQL nagy kapacitású újraméretezése

Az ötödik fizikai csomópont még nem üzemeltet munkaterhelést. Ahogy az Azure arc engedélyezett PostgreSQL-nagy kapacitású kibővítjük, a Kubernetes optimalizálja az új PostgreSQL Pod elhelyezését, és nem rézvezetékes végezhet azt olyan fizikai csomópontokon, amelyek már több számítási feladatot üzemeltetnek. Futtassa az alábbi parancsot az Azure arc engedélyezett PostgreSQL-nagy kapacitású 3 – 4 feldolgozóra történő skálázásához. A művelet befejezésekor a rendszer öt PostgreSQL-példányon, egy koordinátoron és négy feldolgozón keresztül hozza létre és osztja el a kiszolgálói csoportot.

```console
azdata arc postgres server edit --name postgres01 --workers 4
```

A következő kimenetet eredményezi:

```output
Updating postgres01 in namespace `arc3`
postgres01 is Ready
```

Sorolja fel az adatkezelőben üzembe helyezett kiszolgálócsoportok listáját, és ellenőrizze, hogy a kiszolgálócsoport most már négy feldolgozóval fut-e:

```console
azdata arc postgres server list
```

És figyelje meg, hogy három-négy feldolgozóval felskálázást végzett. 

```console
Name        State    Workers
----------  -------  ---------
postgres01  Ready    4
```

Ahogy korábban tettük, figyelje meg, hogy a kiszolgálócsoport már négy hüvelyt használ:

```output
kubectl get pods -n arc3

NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01c-0         3/3     Running   0          13h
postgres01w-0         3/3     Running   0          13h
postgres01w-1         3/3     Running   0          13h
postgres01w-2         3/3     Running   0          179m
postgres01w-3         3/3     Running   0          3m13s
```

A kiszolgálócsoport alakja mostantól:

|Kiszolgálói csoport szerepköre|Kiszolgálócsoport Pod
|----|-----
|Coordinator|postgres01c – 0
|Feldolgozói|postgres01w – 0
|Feldolgozói|postgres01w – 1
|Feldolgozói|postgres01w – 2
|Feldolgozói|postgres01w – 3

Írja le a postgres01w-3 Pod-t annak azonosítására, hogy milyen fizikai csomópontot üzemeltet:

```console
kubectl describe pod postgres01w-3 -n arc3
```

És figyelje meg, milyen hüvely fut:

|Kiszolgálói csoport szerepköre|Kiszolgálócsoport Pod| Pod
|----|-----|------
|Coordinator|postgres01c – 0|AK-agentpool-42715708-vmss000000
|Feldolgozói|postgres01w – 0|AK-agentpool-42715708-vmss000002
|Feldolgozói|postgres01w – 1|AK-agentpool-42715708-vmss000003
|Feldolgozói|postgres01w – 2|AK-agentpool-42715708-vmss000000
|Feldolgozói|postgres01w – 3|AK-agentpool-42715708-vmss000004

Az architektúra pedig így néz ki:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/10_kubernetes_schedules_newest_pod.png" alt-text="A Kubernetes a legújabb Pod-t a legalacsonyabb használatú csomóponton ütemezheti":::

A Kubernetes az új PostgreSQL Pod-t a Kubernetes-fürt legkevésbé betöltött fizikai csomópontjában ütemezhette.

## <a name="summary"></a>Összefoglalás

Annak érdekében, hogy a lehető leghatékonyabban kihasználhassák az Azure arc-kompatibilis kiszolgálócsoport méretezésének és teljesítményének horizontális felskálázását, az erőforrás-tartalmat a Kubernetes-fürtön belül érdemes elkerülni:
- Az Azure arc engedélyezett PostgreSQL nagy kapacitású-kiszolgálócsoport és az ugyanazon Kubernetes-fürtön futó egyéb munkaterhelések között
- Az Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálócsoport részét képező PostgreSQL-példányok között

Ezt többféleképpen is elérheti:
- Horizontális felskálázás mind a Kubernetes, mind az Azure arc enabled postgres nagy kapacitású: a Kubernetes-fürt vízszintes skálázása ugyanúgy, mint az Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálócsoport méretezése. Adjon hozzá egy fizikai csomópontot a fürthöz minden, a kiszolgálócsoport számára hozzáadott feldolgozóhoz.
- Az Azure arc-kompatibilis postgres horizontális felskálázása a nagy kapacitású-Kubernetes nélkül: a megfelelő erőforrás-korlátozásokat (a kérelmeket és a korlátozásokat a memóriára és a virtuális mag) a Kubernetes üzemeltetett munkaterheléseken (Azure arc-kompatibilis PostgreSQL nagy kapacitású) a Kubernetes-ben engedélyezheti a munkaterhelések elhelyezését, és csökkentheti az erőforrás-tartalom kockázatát. Meg kell győződnie arról, hogy a Kubernetes-fürt fizikai csomópontjainak fizikai jellemzői betartják a definiált erőforrások korlátozásait. Azt is biztosítania kell, hogy az egyensúly maradjon, ahogy a számítási feladatok idővel fejlődnek, vagy ha a Kubernetes-fürtben több számítási feladat van hozzáadva.
- A hüvelyek elhelyezésének befolyásolásához használja a Kubernetes mechanizmusokat (Pod választó, affinitás, affinitás).

## <a name="next-steps"></a>Következő lépések

[Több feldolgozó csomópont hozzáadásával bővítheti az Azure arc-alapú PostgreSQL nagy kapacitású-kiszolgáló csoportját](scale-out-postgresql-hyperscale-server-group.md)
