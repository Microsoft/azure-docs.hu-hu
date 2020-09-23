---
title: A PostgreSQL nagy kapacitású-kiszolgálócsoport elhelyezése a Kubernetes-fürt csomópontjain
description: Ismerteti, hogyan történik a PostgreSQL nagy kapacitású-kiszolgálói csoportot alkotó PostgreSQL-példányok elhelyezése a Kubernetes-fürt csomópontjain.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 5da00916a3f7a6a3685b1de1c56dd032355e28fa
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90935824"
---
# <a name="azure-arc-enabled-postgresql-hyperscale-server-group-placement"></a>Azure arc engedélyezve PostgreSQL nagy kapacitású Server Group elhelyezése

Ebben a cikkben egy példát mutatunk be arra, hogy az Azure arc-és PostgreSQL-nagy kapacitású-kiszolgálócsoport PostgreSQL-példányai az azokat üzemeltető Kubernetes-fürt fizikai csomópontjain legyenek elhelyezve. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="configuration"></a>Konfiguráció

Ebben a példában egy olyan Azure Kubernetes Service-(ak-) fürtöt használunk, amely négy fizikai csomóponttal rendelkezik. 

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/1_cluster_portal.png" alt-text="4 csomópontos AK-fürt Azure Portal":::

A parancs futtatásával sorolja fel a Kubernetes-fürt fizikai csomópontjait:

```console
kubectl get nodes
```

A Kubernetes-fürtben található négy fizikai csomópontot jeleníti meg:

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
A következő kimenetet hozza létre:

```output
NAME                 READY   STATUS    RESTARTS   AGE
…
postgres01-0         3/3     Running   0          9h
postgres01-1         3/3     Running   0          9h
postgres01-2         3/3     Running   0          9h
```
Ezek a hüvelyek egy PostgreSQL-példányt futtatnak. Együtt alkotják az Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálói csoportot:

```output
Pod name    Role in the server group
postgres01-0            Coordinator
postgres01-1    Worker
postgres01-2    Worker
```

## <a name="placement"></a>Elhelyezés
Nézzük meg, hogyan helyezi el a Kubernetes a csoport hüvelyeit. Írja le az egyes Pod-ket, és azonosítsa, hogy a Kubernetes-fürt mely fizikai csomópontján vannak elhelyezve. A koordinátor számára például futtassa a következő parancsot:

```console
kubectl describe pod postgres01-0 -n arc3
```

A következő kimenetet hozza létre:

```output
Name:         postgres01-0
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
kubectl describe pod postgres01-2 -n arc3
```

A következő kimenetet hozza létre:

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

|Tárolók|Leírás
|----|----|
|`Fluentbit` |Adatnapló-gyűjtő: https://fluentbit.io/
|`Postgres`|Az Azure arc engedélyezett PosgreSQL nagy kapacitású-kiszolgálócsoport részét képező PostgreSQL példány része
|`Telegraf` |Metrikák gyűjtője: https://www.influxdata.com/time-series-platform/telegraf/

Az architektúra a következőképpen néz ki:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/3_pod_placement.png" alt-text="3 hüvely külön csomópontokra helyezve":::

Ez azt jelenti, hogy ezen a ponton az Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálócsoport részét képező PostgreSQL-példányok a Kubernetes-tárolóban található adott fizikai gazdagépen futnak. Ez a legjobb konfiguráció, amellyel az Azure arc-kompatibilis PostgreSQL nagy kapacitású-kiszolgálócsoport legtöbbje kihasználható, mivel minden szerepkör (koordinátor és dolgozó) az egyes fizikai csomópontok erőforrásait használja. Ezeket az erőforrásokat a rendszer nem osztja meg több PostgreSQL-szerepkör között.

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
postgres01-0         3/3     Running   0          11h
postgres01-1         3/3     Running   0          11h
postgres01-2         3/3     Running   0          11h
postgres01-3         3/3     Running   0          5m2s
```

És írja le az új Pod-t annak azonosítására, hogy a Kubernetes-fürt mely fizikai csomópontjain fut.
Futtassa a következő parancsot:

```console
kubectl describe pod postgres01-3 -n arc3
```

Az üzemeltetési csomópont nevének azonosítása:

```output
Name:         postgres01-3
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

Figyelje meg, hogy az új feldolgozó (postgres01-3) Pod-je ugyanarra a csomópontra került, mint a koordinátor. 

Az architektúra a következőképpen néz ki:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/4_pod_placement_.png" alt-text="Negyedik Pod ugyanazon a csomóponton, mint a koordinátor":::

Miért nem az új feldolgozó/Pod kerül a Kubernetes-fürt (ak-agentpool-42715708-vmss000003) fennmaradó fizikai csomópontjára?

Ennek az az oka, hogy a Kubernetes-fürt utolsó fizikai csomópontja több hüvelyt üzemeltet, amelyek az Azure arc-kompatibilis adatszolgáltatások futtatásához szükséges további összetevőket működtetnek. A Kubernetes azt vizsgálta, hogy a legjobb jelölt – az ütemezés időpontjában – a további dolgozó üzemeltetése az AK-agentpool-42715708-vmss000000 fizikai csomópont. 

Ugyanazokat a parancsokat használja, mint a fentiekben; láthatjuk, hogy az egyes fizikai csomópontok hol találhatók:

|Egyéb hüvelyek nevei\* |Használat|A hüvelyt üzemeltető fizikai csomópont Kubernetes
|----|----|----
|bootstrapper – jh48b||AK-agentpool-42715708-vmss000003
|vezérlés – gwmbs||AK-agentpool-42715708-vmss000002
|controldb – 0||AK-agentpool-42715708-vmss000001
|controlwd-zzjp7||AK-agentpool-42715708-vmss000000
|logsdb – 0|Elasticsearch, fogadja az `Fluentbit` egyes Pod-tárolók adatait|AK-agentpool-42715708-vmss000003
|logsui-5fzv5||AK-agentpool-42715708-vmss000003
|metricsdb – 0|InfluxDB, fogadja az `Telegraf` egyes Pod-tárolók adatait|AK-agentpool-42715708-vmss000000
|metricsdc-47d47||AK-agentpool-42715708-vmss000002
|metricsdc-864kj||AK-agentpool-42715708-vmss000001
|metricsdc-l8jkf||AK-agentpool-42715708-vmss000003
|metricsdc-nxm4l||AK-agentpool-42715708-vmss000000
|metricsui-4fb7l||AK-agentpool-42715708-vmss000003
|mgmtproxy-4qppp||AK-agentpool-42715708-vmss000002

> \* A pod-nevek utótagja eltérő lesz a többi üzemelő példányon. Emellett itt csak az Azure arc-adatkezelő Kubernetes névterében tárolt hüvelyek jelennek meg.

Az architektúra a következőképpen néz ki:

:::image type="content" source="media/migrate-postgresql-data-into-postgresql-hyperscale-server-group/5_full_list_of_pods.png" alt-text="A névtérben lévő összes hüvely a különböző csomópontokon":::

Ez azt jelenti, hogy az Azure arc-kompatibilis postgres nagy kapacitású-kiszolgálócsoport koordinátor-csomópontjai (Pod 1) ugyanazokkal a fizikai erőforrásokkal rendelkeznek, mint a kiszolgálócsoport harmadik feldolgozói csomópontjának (Pod 4). Ez elfogadható, mivel a koordinátor csomópont általában nagyon kevés erőforrást használ a feldolgozó csomópontok által használthoz képest. Ebből a következtetésből kikövetkeztetheti, hogy körültekintően kell kiválasztania:
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
postgres01-0         3/3     Running   0          13h
postgres01-1         3/3     Running   0          13h
postgres01-2         3/3     Running   0          13h
postgres01-3         3/3     Running   0          179m
postgres01-4         3/3     Running   0          3m13s
```

A kiszolgálócsoport alakja mostantól:

|Kiszolgálói csoport szerepköre|Kiszolgálócsoport Pod
|----|-----
|Coordinator|postgres01 – 0
|Feldolgozói|postgres01 – 1
|Feldolgozói|postgres01 – 2
|Feldolgozói|postgres01 – 3
|Feldolgozói|postgres01 – 4

Írja le a postgres01-4 Pod-t annak azonosítására, hogy milyen fizikai csomópontot üzemeltet:

```console
kubectl describe pod postgres01-4 -n arc3
```

És figyelje meg, milyen hüvely fut:

|Kiszolgálói csoport szerepköre|Kiszolgálócsoport Pod| Pod
|----|-----|------
|Coordinator|postgres01 – 0|AK-agentpool-42715708-vmss000000
|Feldolgozói|postgres01 – 1|AK-agentpool-42715708-vmss000002
|Feldolgozói|postgres01 – 2|AK-agentpool-42715708-vmss000003
|Feldolgozói|postgres01 – 3|AK-agentpool-42715708-vmss000000
|Feldolgozói|postgres01 – 4|AK-agentpool-42715708-vmss000004

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
