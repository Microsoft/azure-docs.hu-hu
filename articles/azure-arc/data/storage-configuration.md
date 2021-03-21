---
title: Tároló konfigurálása
description: Az Azure arc-kompatibilis adatszolgáltatások tárolási konfigurációs beállításainak ismertetése
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: uc-msft
ms.author: umajay
ms.reviewer: mikeray
ms.date: 10/12/2020
ms.topic: conceptual
ms.openlocfilehash: 7b683029b7fd05078755d4e8cd027f55c805f991
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97107260"
---
# <a name="storage-configuration"></a>Tárolási konfiguráció

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="kubernetes-storage-concepts"></a>Kubernetes tárolási fogalmak

A Kubernetes biztosítja az infrastruktúra-absztrakciós réteget a mögöttes virtualizációs Tech stack (opcionális) és a hardver számára. A Kubernetes absztrakt tárolásának módja a **[tárolási osztályokon](https://kubernetes.io/docs/concepts/storage/storage-classes/)** keresztül történik. A pod kiépítés időpontjában megadható egy tárolási osztály, amely az egyes kötetekhez használható. A hüvely **[kiépítésekor](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/)** a rendszer meghívja a tárolási osztályt, hogy kiépítse a tárolót, majd **[állandó kötetet](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)** hozzon létre a kiépített tárolón, majd a pod egy **[állandó mennyiségi jogcím](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims)** szerint csatlakoztatva legyen az állandó kötethez.

A Kubernetes lehetővé teszi a tárolási infrastruktúra-szolgáltatók számára, hogy a Kubernetes kiterjesztésű illesztőprogramokat (más néven "addons") csatlakoztassa. A Storage-bővítményeknek meg kell felelniük a **[Container Storage Interface standard](https://kubernetes.io/blog/2019/01/15/container-storage-interface-ga/)** követelményeinek. A CSI-illesztőprogramok nem végleges **[listáján](https://kubernetes-csi.github.io/docs/drivers.html)** több tucat bővítmény található. Az Ön által használt CSI-illesztőprogram attól függ, hogy a felhőben üzemeltetett, felügyelt Kubernetes szolgáltatásban vagy a hardveréhez használt OEM-szolgáltatóban fut-e.

A következő parancs futtatásával megtekintheti, hogy mely tárolási osztályok vannak konfigurálva a Kubernetes-fürtben:

```console
kubectl get storageclass
```

Példa egy Azure Kubernetes Service (ak) fürt kimenetére:

```console
NAME                PROVISIONER                AGE
azurefile           kubernetes.io/azure-file   15d
azurefile-premium   kubernetes.io/azure-file   15d
default (default)   kubernetes.io/azure-disk   4d3h
managed-premium     kubernetes.io/azure-disk   4d3h
```

A tárolási osztály részleteit a következő parancs futtatásával kérheti le:

```console
kubectl describe storageclass/<storage class name>
```

Példa:

```console
kubectl describe storageclass/azurefile

Name:            azurefile
IsDefaultClass:  No
Annotations:     kubectl.kubernetes.io/last-applied-configuration={"allowVolumeExpansion":true,"apiVersion":"storage.k8s.io/v1beta1","kind":"StorageClass","metadata":{"annotations":{},"labels":{"kubernetes.io/cluster-service":"true"},"name":"azurefile"},"parameters":{"sku
Name":"Standard_LRS"},"provisioner":"kubernetes.io/azure-file"}

Provisioner:           kubernetes.io/azure-file
Parameters:            skuName=Standard_LRS
AllowVolumeExpansion:  True
MountOptions:          <none>
ReclaimPolicy:         Delete
VolumeBindingMode:     Immediate
Events:                <none>
```

Az aktuálisan kiépített állandó kötetek és az állandó mennyiségi jogcímek a következő parancsok futtatásával tekinthetők meg:

```console
kubectl get persistentvolumes -n <namespace>

kubectl get persistentvolumeclaims -n <namespace>
```

Példa az állandó kötetek megjelenítésére:

```console

kubectl get persistentvolumes -n arc

NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                      STORAGECLASS   REASON   AGE
pvc-07fc7b9f-9a37-4796-9442-4405147120da   15Gi       RWO            Delete           Bound    arc/sqldemo11-data-claim   default                 7d3h
pvc-3e772f20-ed89-4642-b34d-8bb11b088afa   15Gi       RWO            Delete           Bound    arc/data-metricsdb-0       default                 7d14h
pvc-41b33bbd-debb-4153-9a41-02ce2bf9c665   10Gi       RWO            Delete           Bound    arc/sqldemo11-logs-claim   default                 7d3h
pvc-4ccda3e4-fee3-4a89-b92d-655c04fa62ad   15Gi       RWO            Delete           Bound    arc/data-controller        default                 7d14h
pvc-63e6bb4c-7240-4de5-877e-7e9ea4e49c91   10Gi       RWO            Delete           Bound    arc/logs-controller        default                 7d14h
pvc-8a1467fe-5eeb-4d73-b99a-f5baf41eb493   10Gi       RWO            Delete           Bound    arc/logs-metricsdb-0       default                 7d14h
pvc-8e2cacbc-e953-4901-8591-e77df9af309c   10Gi       RWO            Delete           Bound    arc/sqldemo10-logs-claim   default                 7d14h
pvc-9fb79ba3-bd3e-42aa-aa09-3090135d4513   15Gi       RWO            Delete           Bound    arc/sqldemo10-data-claim   default                 7d14h
pvc-a39c85d4-5cd9-4249-9915-68a70a9bb5e5   15Gi       RWO            Delete           Bound    arc/data-controldb         default                 7d14h
pvc-c9cbd74a-76ca-4be5-b598-0c7a45749bfb   10Gi       RWO            Delete           Bound    arc/logs-controldb         default                 7d14h
pvc-d576e9d4-0a09-4dd7-b806-be8ed461f8a4   10Gi       RWO            Delete           Bound    arc/logs-logsdb-0          default                 7d14h
pvc-ecd7d07f-2c2c-421d-98d7-711ec5d4a0cd   15Gi       RWO            Delete           Bound    arc/data-logsdb-0          default                 7d14h
```

Példa állandó mennyiségi jogcímek megjelenítésére:

```console

kubectl get persistentvolumeclaims -n arc

NAME                   STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
data-controldb         Bound    pvc-a39c85d4-5cd9-4249-9915-68a70a9bb5e5   15Gi       RWO            default        7d14h
data-controller        Bound    pvc-4ccda3e4-fee3-4a89-b92d-655c04fa62ad   15Gi       RWO            default        7d14h
data-logsdb-0          Bound    pvc-ecd7d07f-2c2c-421d-98d7-711ec5d4a0cd   15Gi       RWO            default        7d14h
data-metricsdb-0       Bound    pvc-3e772f20-ed89-4642-b34d-8bb11b088afa   15Gi       RWO            default        7d14h
logs-controldb         Bound    pvc-c9cbd74a-76ca-4be5-b598-0c7a45749bfb   10Gi       RWO            default        7d14h
logs-controller        Bound    pvc-63e6bb4c-7240-4de5-877e-7e9ea4e49c91   10Gi       RWO            default        7d14h
logs-logsdb-0          Bound    pvc-d576e9d4-0a09-4dd7-b806-be8ed461f8a4   10Gi       RWO            default        7d14h
logs-metricsdb-0       Bound    pvc-8a1467fe-5eeb-4d73-b99a-f5baf41eb493   10Gi       RWO            default        7d14h
sqldemo10-data-claim   Bound    pvc-9fb79ba3-bd3e-42aa-aa09-3090135d4513   15Gi       RWO            default        7d14h
sqldemo10-logs-claim   Bound    pvc-8e2cacbc-e953-4901-8591-e77df9af309c   10Gi       RWO            default        7d14h
sqldemo11-data-claim   Bound    pvc-07fc7b9f-9a37-4796-9442-4405147120da   15Gi       RWO            default        7d4h
sqldemo11-logs-claim   Bound    pvc-41b33bbd-debb-4153-9a41-02ce2bf9c665   10Gi       RWO            default        7d4h

```

## <a name="factors-to-consider-when-choosing-your-storage-configuration"></a>A tárolási konfiguráció kiválasztásakor Megfontolandó tényezők

A megfelelő tárolási osztály kiválasztása fontos az adatrugalmasság és a teljesítmény szempontjából. Ha nem a megfelelő tárolási osztályt választja, akkor a hardver meghibásodása esetén a teljes adatvesztés kockázatára teheti az adatait, vagy kevésbé optimális teljesítményt eredményezhet.

A tárolásnak általában két típusa van:

- **Helyi tárterület** – egy adott csomóponton a helyi merevlemezeken kiépített tárterület. Ez a fajta tárterület ideális lehet a teljesítmény szempontjából, de kifejezetten az adatredundancia megtervezését igényli az adat több csomóponton keresztüli replikálásával.
- **Távoli, megosztott tároló** – néhány távoli tárolóeszközön kiépített tároló – például egy San-, NAS-vagy felhőalapú tárolási szolgáltatás, például az EBS vagy a Azure files. Az ilyen típusú tárolók általában automatikusan biztosítanak adatredundanciát, de nem olyan gyorsan, mint a helyi tárterület.

> [!NOTE]
> Most, ha az NFS-t használja, az Azure arc-adatkezelő üzembe helyezése előtt be kell állítania a allowRunAsRoot True értékre a telepítési profilban.

### <a name="data-controller-storage-configuration"></a>Adatkezelő tárolási konfigurációja

Az Azure arc adatszolgáltatások egyes szolgáltatásai attól függnek, hogy távoli, megosztott tárterületet használnak-e, mivel a szolgáltatások nem képesek az adatok replikálására. Ezek a szolgáltatások az adatkezelő hüvelyek gyűjteményében találhatók:

|**Szolgáltatás**|**Állandó kötet jogcímei**|
|---|---|
|**ElasticSearch**|`<namespace>/logs-logsdb-0`, `<namespace>/data-logsdb-0`|
|**InfluxDB**|`<namespace>/logs-metricsdb-0`, `<namespace>/data-metricsdb-0`|
|**Vezérlő SQL-példánya**|`<namespace>/logs-controldb`, `<namespace>/data-controldb`|
|**Vezérlő API-szolgáltatás**|`<namespace>/data-controller`|

Az adatkezelő kiosztásának időpontjában az egyes állandó kötetek esetében használandó tárolási osztályt a--Storage-osztály átadásával adja meg a rendszer. -SC paramétert a `azdata arc dc create` parancshoz, vagy állítsa be a tárolási osztályokat a control.jsa használt telepítési sablon fájljában.

A beépített központi telepítési sablonok rendelkeznek egy alapértelmezett tárolási osztállyal, amely megfelelő a célként megadott környezethez, de felülbírálható az üzembe helyezés során. Tekintse meg a [telepítési profil](create-data-controller.md) módosításának részletes lépéseit, ha módosítani szeretné az adatkezelő hüvelyének tárolási osztályának konfigurációját a telepítés ideje alatt.

Ha a Storage osztályt a--Storage-osztály használatával állítja be | -SC paraméter a tárolási osztály a log és az adattárolási osztályokhoz is használatos. Ha a tárolási osztályokat a telepítési sablon fájljában állítja be, különböző tárolási osztályokat adhat meg a naplókhoz és az adatforrásokhoz.

Az adatvezérlő hüvelyének tárolási osztályának kiválasztásakor megfontolandó fontos tényezők:

- Távoli, megosztott tárolási osztályt **kell** használnia az adattartósság biztosításához, így ha egy Pod vagy csomópont meghal, hogy amikor a pod biztonsági mentést végez, újra csatlakozhat az állandó kötethez.
- A vezérlő SQL-példányára, a metrikák adatbázisára és a naplók adatbázisára írt adatok jellemzően meglehetősen alacsony mennyiségű, és nem érzékenyek a késésre, így az ultra-Fast teljesítményű tárterület nem kritikus fontosságú. Ha olyan felhasználókkal rendelkezik, akik gyakran használják a Grafana és a Kibana felületet, és nagy számú adatbázis-példánya van, akkor előfordulhat, hogy a felhasználók gyorsabban végeznek tárterületet.
- A szükséges tárolási kapacitás változó a telepített adatbázis-példányok számával, mivel az egyes adatbázis-példányok naplóit és metrikáit gyűjti. Az adatok megmaradnak a naplók és a metrikák ADATBÁZISában két (2) héttel a tisztítás előtt. 
- A tárolási osztály az üzembe helyezés utáni telepítésének módosítása nehéz, nem dokumentált, és nem támogatott. Ügyeljen arra, hogy a tárolási osztályt helyesen válassza ki a központi telepítés ideje alatt.

> [!NOTE]
> Ha nincs megadva tárolási osztály, a rendszer az alapértelmezett tárolási osztályt fogja használni. Kubernetes-fürtön csak egy alapértelmezett tárolási osztály lehet. [Módosíthatja az alapértelmezett tárolási osztályt](https://kubernetes.io/docs/tasks/administer-cluster/change-default-storage-class/).

### <a name="database-instance-storage-configuration"></a>Adatbázis-példány tárolási konfigurációja

Minden adatbázis-példányhoz tartozik az adatmennyiség, a naplók és a biztonsági mentés állandó kötetei. Az állandó kötetek tárolási osztályai megadhatók a központi telepítési időszakban. Ha nincs megadva tárolási osztály, a rendszer az alapértelmezett tárolási osztályt fogja használni.

Ha a vagy a használatával hoz létre egy példányt `azdata arc sql mi create` `azdata arc postgres server create` , két paraméter használható a tárolási osztályok beállításához:

> [!NOTE]
> Ezen paraméterek némelyike fejlesztés alatt áll, és a `azdata arc sql mi create` `azdata arc postgres server create` közelgő kiadásokban elérhetővé válik.

|Paraméter neve, rövid név|Alkalmazási cél|
|---|---|
|`--storage-class-data`, `-scd`|A tárolási osztály megadására szolgál az összes adatfájlhoz, beleértve a tranzakciónapló-fájlokat is|
|`--storage-class-logs`, `-scl`|Az összes naplófájl tárolási osztályának megadására használatos|
|`--storage-class-data-logs`, `-scdl`|Az adatbázis-tranzakciós naplófájlok tárolási osztályának megadására szolgál. **Megjegyzés: még nem érhető el.**|
|`--storage-class-backups`, `-scb`|Az összes biztonságimásolat-fájl tárolási osztályának megadására szolgál. **Megjegyzés: még nem érhető el.**|

Az alábbi táblázat felsorolja az Azure SQL felügyelt példány tárolóján belüli elérési utakat, amelyek az adatforgalom és a naplók állandó kötetére vannak leképezve:

|Paraméter neve, rövid név|Az MSSQL-MIAA tárolón belüli elérési út|Description|
|---|---|---|
|`--storage-class-data`, `-scd`|/var/opt|Az MSSQL-telepítéshez és más rendszerfolyamatokhoz tartozó címtárakat tartalmaz. Az MSSQL-címtár alapértelmezett (beleértve a tranzakciós naplókat is), a hibanapló & a biztonsági mentési könyvtárakat tartalmazza|
|`--storage-class-logs`, `-scl`|/var/log|A konzol kimenetét (stderr, StdOut) tároló könyvtárakat, a tárolón belüli folyamatok egyéb naplózási információit tartalmazza.|

Az alábbi táblázat felsorolja a PostgreSQL-példány tárolóján belüli elérési utakat, amelyek az adatforgalom és a naplók állandó kötetére vannak leképezve:

|Paraméter neve, rövid név|Elérési út a postgres-tárolón belül|Description|
|---|---|---|
|`--storage-class-data`, `-scd`|/var/opt/postgresql|A postgres-telepítéshez szükséges adatés naplózási könyvtárakat tartalmazza|
|`--storage-class-logs`, `-scl`|/var/log|A konzol kimenetét (stderr, StdOut) tároló könyvtárakat, a tárolón belüli folyamatok egyéb naplózási információit tartalmazza.|

Minden adatbázis-példány külön állandó kötettel fog rendelkezni az adatfájlok, a naplók és a biztonsági másolatok számára. Ez azt jelenti, hogy az ilyen típusú fájlok I/O-fájljainak elkülönítése a kötet-kiépítő tárterület kiépítésének módjától függ. Minden adatbázis-példány saját állandó mennyiségi jogcímeket és állandó köteteket tartalmaz.

Ha egy adott adatbázis-példányon több adatbázis található, az összes adatbázis ugyanazt az állandó mennyiségi jogcímet, állandó kötetet és tárolási osztályt fogja használni. Minden biztonsági mentés – a különbözeti napló és a teljes biztonsági mentés egyaránt ugyanazt az állandó mennyiségi jogcímet és állandó kötetet fogja használni. Az adatbázis-példányhoz tartozó hüvelyek állandó mennyiségi jogcímei az alábbiak szerint jelennek meg:

|**Példány**|**Állandó kötet jogcímei**|
|---|---|
|**Felügyelt Azure SQL-példány**|`<namespace>/logs-<instance name>-0`, `<namespace>/data-<instance name>-0`|
|**Azure Database for PostgreSQL-példány**|`<namespace>/logs--<instance name>-0`, `<namespace>/data--<instance name>-0`|
|**Azure PostgreSQL nagy kapacitású**|`<namespace>/logs-<instance namme>-<ordinal>``<namespace>/data-<instance namme>-<ordinal>` *(0 és w közötti sorszám, ahol a feldolgozók száma)*|

Az adatbázis-példány hüvelyének tárolási osztályának kiválasztásánál megfontolandó fontos tényezők:

- Az adatbázis-példányok egyetlen Pod mintában vagy több Pod-mintában is üzembe helyezhetők. Egyetlen Pod minta például az Azure SQL felügyelt példányának fejlesztői példánya vagy az Azure SQL felügyelt példányának általános célú díjszabási szintje. Több Pod minta például egy nagy rendelkezésre állású üzleti szempontból kritikus díjszabási szint, amely az Azure SQL felügyelt példánya. (Megjegyzés: az árképzési szintek fejlesztés alatt állnak, és még nem érhetők el az ügyfelek számára.)  Az egyszeres Pod mintával üzembe helyezett adatbázis-példányoknak távoli, megosztott tárolási osztályt **kell** használniuk az adattartósság biztosításához, így ha egy Pod vagy csomópont meghal, ha a pod biztonsági mentést végez, akkor ismét csatlakozhat az állandó kötethez. Ezzel szemben a magas rendelkezésre állású Azure SQL felügyelt példányok az Always On rendelkezésre állási csoportokat használják az adatok egyik példányról a másikra való replikálásához szinkron vagy aszinkron módon. Különösen abban az esetben, ha a rendszer szinkron módon replikálja az adatgyűjtést, az adatmennyiség mindig több példányban szerepel – általában három (3) másolat. Emiatt a helyi tárolót vagy távoli, megosztott tárolási osztályokat lehet használni az adatfájlokhoz és a naplófájlokhoz. Ha a helyi tárterületet használja, az adat továbbra is megmarad, még egy sikertelen Pod, node vagy Storage hardver esetén is. A rugalmasság miatt érdemes lehet helyi tárterületet használni a jobb teljesítmény érdekében.
- Az adatbázis teljesítménye nagyrészt az adott tárolóeszköz I/O-teljesítményének függvénye. Ha az adatbázis nagy olvasási vagy nagy mennyiségű írást használ, olyan tárolási osztályt kell választania, amely az adott típusú számítási feladatokhoz készült hardveres. Ha például az adatbázis többnyire íráshoz használatos, a RAID 0 lehetőséggel választhatja a helyi tárterületet. Ha az adatbázist többnyire kis mennyiségű "forró adat" beolvasására használják, de nagy mennyiségű hideg adat áll rendelkezésre, akkor lehet, hogy olyan SAN-eszközt választ, amely képes a többrétegű tárolásra. A megfelelő tárolási osztály kiválasztása nem sokban különbözik a bármely adatbázishoz használni kívánt tárterület típusától.
- Ha helyi tárolási kötetet használ, győződjön meg arról, hogy az adatokat, naplókat és biztonsági mentéseket kiépítő helyi kötetek mindegyike különböző mögöttes tárolóeszközökön történik, hogy elkerülje a lemez I/O-terhelését. Az operációs rendszernek a különálló lemez (ek) hez csatlakoztatott köteten is szerepelnie kell. Ez lényegében ugyanaz az útmutató, mint a fizikai hardveren található adatbázis-példány esetében.
- Mivel az adott példány összes adatbázisa állandó mennyiségi jogcímet és állandó kötetet használ, ügyeljen arra, hogy ne helyezze el az azonos adatbázis-példányon található foglalt adatbázis-példányokat. Ha lehetséges, külön foglalt adatbázisokat a saját adatbázis-példányaira, hogy elkerülje az I/O-tartalmat. Ezen túlmenően a csomópont-címkék célzása a különböző csomópontokon lévő adatbázis-példányok számára, hogy a teljes I/O-forgalmat több csomóponton is el lehessen osztani. Ha virtualizációt használ, ügyeljen arra, hogy az I/O-forgalmat ne csak a csomópont szintjén végezze el, hanem az adott fizikai állomáson lévő összes csomópontos virtuális gép által végrehajtott egyesített I/O-tevékenységet is.

## <a name="estimating-storage-requirements"></a>Tárolási követelmények becslése
Az állapot-nyilvántartó adatmennyiséget tartalmazó összes hüvely két állandó kötetet használ ebben a kiadásban – egy állandó kötetet az adatforgalomhoz, és egy másik állandó kötetet a naplókhoz. Az alábbi táblázat felsorolja az egyetlen adatvezérlőhöz, az Azure SQL felügyelt példányhoz, a Azure Database for PostgreSQL példányhoz és az Azure PostgreSQL nagy kapacitású-példányhoz szükséges állandó kötetek számát:

|Erőforrás típusa|Állapot-nyilvántartó hüvelyek száma|Állandó kötetek kötelező száma|
|---|---|---|
|Adatkezelő|4 ( `control` ,,, `controldb` `logsdb` `metricsdb` )|4 * 2 = 8|
|Felügyelt Azure SQL-példány|1|2|
|Azure Database for PostgreSQL-példány|1| 2|
|Azure PostgreSQL nagy kapacitású|1 + w (W = feldolgozók száma)|2 * (1 + W)|

Az alábbi táblázat a minta telepítéséhez szükséges állandó kötetek teljes számát mutatja:

|Erőforrás típusa|Példányok száma|Állandó kötetek kötelező száma|
|---|---|---|
|Adatkezelő|1|4 * 2 = 8|
|Felügyelt Azure SQL-példány|5|5 * 2 = 10|
|Azure Database for PostgreSQL-példány|5| 5 * 2 = 10|
|Azure PostgreSQL nagy kapacitású|2 (feldolgozók száma = 4/példány)|2 * 2 * (1 + 4) = 20|
|***Állandó kötetek teljes száma***||8 + 10 + 10 + 20 = 48|

Ez a számítás felhasználható a Kubernetes-fürt tárterületének megtervezésére a Storage-létesítés vagy-környezet alapján. Ha például a helyi tárolót egy öt (5) csomóponttal rendelkező Kubernetes-fürthöz használja, akkor az összes csomópontnál a minta telepítéséhez legalább 10 állandó kötetre van szükség. Hasonlóképpen, amikor egy öt (5) csomóponttal rendelkező Azure Kubernetes-szolgáltatási (ak-) fürt kiosztása egy megfelelő virtuálisgép-méretet választ ki a csomópont-készlet számára, így a 10 adatlemez csatlakoztatható kritikus fontosságú. [Itt](../../aks/operator-best-practices-storage.md#size-the-nodes-for-storage-needs)talál további információt arról, hogyan méretezhetők a csomópontok az AK-csomópontok tárolási igényeihez.

## <a name="choosing-the-right-storage-class"></a>A megfelelő tárolási osztály kiválasztása

### <a name="on-premises-and-edge-sites"></a>Helyszíni és Edge-helyek

A Microsoft és az OEM, az operációs rendszer és a Kubernetes-partnerek az Azure arc-adatszolgáltatások minősítési programjával dolgoznak. Ez a program a minősítési tesztelési eszközkészlettől származó, összehasonlítható tesztelési eredményeket biztosít az ügyfeleknek. A tesztek kiértékelik a funkciók kompatibilitását, a stressz tesztelésének eredményét, valamint a teljesítményt és a méretezhetőséget. A tesztelési eredmények mindegyike a használt operációs rendszert, a Kubernetes-eloszlást, a felhasznált HW-t, a használt CSI-bővítményt és a felhasznált tárolási osztályokat fogja jelezni. Ez segít az ügyfeleknek kiválasztani a legjobb tárolási osztályt, az operációs rendszert, a Kubernetes-eloszlást és a HW-t a követelményeiknek megfelelően. A programról és a kezdeti tesztelési eredményekről további információt az Azure arc-adatszolgáltatások általános elérhetőségét ismertető cikkben talál.

#### <a name="public-cloud-managed-kubernetes-services"></a>Nyilvános felhő, felügyelt Kubernetes-szolgáltatások

A nyilvános felhőalapú, felügyelt Kubernetes-szolgáltatásokhoz a következő javaslatokat tehetjük:

|Nyilvános felhőalapú szolgáltatás|Ajánlás|
|---|---|
|**Azure Kubernetes Service (AKS)**|Az Azure Kubernetes Service (ak) két típusú Storage-Azure Files és Azure Managed Disks rendelkezik. Az egyes tárolási típusok két díjszabási/teljesítményi szinttel rendelkeznek – standard (HDD) és prémium (SSD). Így az AK-ban megadott négy tárolási osztály `azurefile` (Azure Files standard szint), `azurefile-premium` (Azure Files prémium szint), `default` (az Azure Disks standard szintű csomag) és az `managed-premium` (Azure Disks prémium szintű). Az alapértelmezett tárolási osztály `default` (Azure Disks standard szint). Jelentős **[díjszabási különbségek](https://azure.microsoft.com/en-us/pricing/details/storage/)** vannak a különböző típusok és rétegek között, amelyeket figyelembe kell venni a döntésében. A nagy teljesítményű követelményekkel rendelkező éles számítási feladatokhoz az `managed-premium` összes tárolási osztály használatát javasoljuk. A fejlesztési és tesztelési feladatokhoz, a koncepció igazolásához stb., ahol a költségeket figyelembe kell venni, akkor `azurefile` a legdrágább megoldás. Mind a négy lehetőség használható olyan helyzetekben, amelyek távoli, megosztott tárterületet igényelnek, mivel az összes hálózatra csatlakoztatott tárolóeszköz az Azure-ban. További információ az [AK Storage](../../aks/concepts-storage.md)-ról.|
|**AWS Elastic Kubernetes Service (EKS)**| Az Amazon rugalmas Kubernetes szolgáltatásának egyetlen elsődleges tárolási osztálya van, amely az [EBS CSI Storage-illesztőprogramon](https://docs.aws.amazon.com/eks/latest/userguide/ebs-csi.html)alapul. Ez éles számítási feladatokhoz ajánlott. Egy új Storage-illesztőprogram – [EFS CSI Storage-illesztőprogram](https://docs.aws.amazon.com/eks/latest/userguide/efs-csi.html) –, amely hozzáadható egy EKS-fürthöz, de jelenleg egy bétaverziós szakaszban van, és változhat. Bár az AWS azt mondja, hogy ez a tároló-illesztőprogram éles környezetben támogatott, ezért nem ajánlott használni, mert továbbra is bétaverzióban van, és változhat. Az EBS tárolási osztálya az alapértelmezett, és a neve `gp2` . További információ a [EKS Storage](https://docs.aws.amazon.com/eks/latest/userguide/storage-classes.html)-ról.|
|**Google Kubernetes Engine (GKE)**|A Google Kubernetes Engine (GKE) csak egy nevű tárolási osztállyal rendelkezik `standard` , amely a [GCE állandó lemezek](https://kubernetes.io/docs/concepts/storage/volumes/#gcepersistentdisk)esetében használatos. Az egyetlen, az alapértelmezett érték is. Bár van egy [helyi, statikus kötet-kiépítő](https://cloud.google.com/kubernetes-engine/docs/how-to/persistent-volumes/local-ssd#run-local-volume-static-provisioner) a GKE, amelyet közvetlenül csatlakoztatott SSD-k használatával használhat, nem ajánlott azt használni, mert a Google nem tartja karban és nem támogatja. További információ a [GKE Storage](https://cloud.google.com/kubernetes-engine/docs/concepts/persistent-volumes)-ról.
