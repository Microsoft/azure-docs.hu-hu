---
title: Méretezési útmutató
description: Tervezze meg az Azure arc-kompatibilis adatszolgáltatások üzembe helyezésének méretét.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 3bbd778eabf150b734b04e004006dfeea2254ec4
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106077479"
---
# <a name="sizing-guidance"></a>Méretezési útmutató

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="overview-of-sizing-guidance"></a>A méretezési útmutató áttekintése

Az Azure arc-adatszolgáltatások üzembe helyezésének tervezésekor meg kell terveznie az Azure arc-adatkezelő futtatásához szükséges számítási, memória-és tárterület megfelelő mennyiségét, valamint a telepíteni kívánt SQL felügyelt példány és PostgreSQL nagy kapacitású-kiszolgálócsoportok számát.  Mivel az Azure arc-kompatibilis adatszolgáltatások a Kubernetes-on vannak telepítve, a további számítási csomópontok vagy tárolók hozzáadásával rugalmasan növelhető a Kubernetes-fürt további kapacitása.  Ez az útmutató útmutatást nyújt a minimális követelményekről, valamint útmutatást nyújt az ajánlott méretekhez a gyakori követelmények tekintetében.

## <a name="general-sizing-requirements"></a>Általános méretezési követelmények

> [!NOTE]
> Ha nem ismeri a cikkben szereplő fogalmakat, további információt olvashat a [Kubernetes erőforrás-szabályozásáról](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/) és a [Kubernetes méretéről](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes).

A magok számának az eggyel nagyobb vagy azzal egyenlő egész számnak kell lennie.

Ha a azdata-t használja az üzembe helyezéshez, a memória értékét két számból kell megadni, azaz az utótagokat: ki, mi vagy gi.

Ha meg van adva, a határértéknek mindig nagyobbnak kell lennie, mint a kérelem értékének.

A magok határértékei a felügyelt SQL-példány és a PostgreSQL nagy kapacitású-kiszolgálócsoportok számlázási metrikái.

## <a name="minimum-deployment-requirements"></a>Minimális központi telepítési követelmények

Az Azure arc-kompatibilis adatszolgáltatások minimális mérete az Azure arc-adatkezelő, valamint egy SQL felügyelt példány, valamint egy PostgreSQL nagy kapacitású-kiszolgálócsoport, amely két feldolgozói csomóponttal rendelkezik.  Ennél a konfigurációnál legalább 16 GB RAM-mal és 4 mag _rendelkezésre álló_ kapacitással kell rendelkeznie a Kubernetes-fürtön.  Győződjön meg arról, hogy a Kubernetes legalább 8 GB RAM-mal és 4 maggal rendelkezik, valamint az összes Kubernetes-csomóponton elérhető teljes kapacitás 16 GB RAM.  Előfordulhat például, hogy 1 csomóponttal rendelkezik 32 GB RAM-mal és 4 maggal, vagy 2 csomóponttal rendelkezik, amelyekhez 16GB RAM és 4 mag tartozik.

A tárterület méretezésével kapcsolatos részletekért tekintse meg a [Storage-Configuration](storage-configuration.md) című cikket.

## <a name="data-controller-sizing-details"></a>Az adatkezelő méretezési adatai

Az adatkezelő a Kubernetes-fürtön üzembe helyezett hüvelyek gyűjteménye, amely API-t, a vezérlő szolgáltatást, a bootstrapper, valamint a figyelési adatbázisokat és irányítópultokat biztosítja.  Ez a táblázat a memória-és a CPU-kérések és-korlátok alapértelmezett értékeit ismerteti.

|Pod neve|CPU-kérelem|Memória-kérelem|CPU-korlát|Memória korlátja|Jegyzetek|
|---|---|---|---|---|---|
|**bootstrapper**|100m|100Mi|200m|200Mi||
|**ellenőrzési**|400m|2Gi|1800m|2Gi||
|**controldb**|200m|3Gi|800m|6Gi||
|**controlwd**|10 millió|100Mi|100m|200Mi||
|**logsdb**|200m|1600Mi|2|1600Mi||
|**logsui**|100m|500Mi|2|2Gi||
|**metricsdb**|200m|800Mi|400m|2Gi||
|**metricsdc**|100m|200Mi|200m|300Mi|A Metricsdc egy daemonset elemet, amely a fürt egyes Kubernetes-csomópontjain jön létre.  A táblázatban szereplő számok száma _csomópont_. Ha a allowNodeMetricsCollection = FALSE értéket adja meg az üzembe helyezési profilban az adatvezérlő létrehozása előtt, a rendszer nem hozza létre a metricsdc-daemonset elemet.|
|**metricsui**|20m|200Mi|500m|200Mi||
|**mgmtproxy**|200m|250Mi|500m|500Mi||

Felülbírálhatja a controldb és a hüvelyek alapértelmezett beállításait a telepítési profil fájljában vagy a datacontroller YAML-fájlban.  Példa:

```yaml
  resources:
    controller:
      limits:
        cpu: "1000m"
        memory: "3Gi"
      requests:
        cpu: "800m"
        memory: "2Gi"
    controllerDb:
      limits:
        cpu: "800m"
        memory: "8Gi"
      requests:
        cpu: "200m"
        memory: "4Gi"
```

A tárterület méretezésével kapcsolatos részletekért tekintse meg a [Storage-Configuration](storage-configuration.md) című cikket.

## <a name="sql-managed-instance-sizing-details"></a>SQL felügyelt példány méretezésének részletei

Minden felügyelt SQL-példánynak a következő minimális erőforrás-kérelmekkel kell rendelkeznie:
- Memória: 2Gi
- Magok: 1

Minden létrehozott SQL felügyelt példányhoz tartozó Pod három tárolóval rendelkezik:

|Tárolónév|CPU-kérelem|Memória-kérelem|CPU-korlát|Memória korlátja|Jegyzetek|
|---|---|---|---|---|---|
|fluentbit|100m|100Mi|Nincs megadva|Nincs megadva|A fluentbit-tároló erőforrás-kérelmei az SQL felügyelt példányához megadott kérelmeken _felül_ vannak.|
|ív – sqlmi|A felhasználó megadott vagy nincs megadva.|A felhasználó megadott vagy nincs megadva.|A felhasználó megadott vagy nincs megadva.|A felhasználó megadott vagy nincs megadva.|
|összegyűjtött teljesítményadatok|Nincs megadva|Nincs megadva|Nincs megadva|Nincs megadva|

Az összes állandó kötet alapértelmezett kötetének mérete 5Gi.

## <a name="postgresql-hyperscale-server-group-sizing-details"></a>PostgreSQL nagy kapacitású-kiszolgáló csoport méretezési adatai

A PostgreSQL nagy kapacitású-kiszolgálócsoport összes csomópontjának a következő minimális erőforrás-kérelmekkel kell rendelkeznie:
- Memória: 256Mi
- Magok: 1

Minden olyan PostgreSQL nagy kapacitású-kiszolgálócsoport-koordinátor vagy Worker Pod, amely létrehozva három tárolóval rendelkezik:

|Tárolónév|CPU-kérelem|Memória-kérelem|CPU-korlát|Memória korlátja|Jegyzetek|
|---|---|---|---|---|---|
|fluentbit|100m|100Mi|Nincs megadva|Nincs megadva|A fluentbit-tároló erőforrás-kérelmei a PostgreSQL nagy kapacitású kiszolgálói csoport csomópontjaihoz megadott kérelmeken _kívül_ vannak.|
|postgres|A felhasználó megadott vagy nincs megadva.|A felhasználó által megadott vagy 256Mi (alapértelmezett).|A felhasználó megadott vagy nincs megadva.|A felhasználó megadott vagy nincs megadva.||
|Telegraf|Nincs megadva|Nincs megadva|Nincs megadva|Nincs megadva||

## <a name="cumulative-sizing"></a>Kumulatív méretezés

Az Azure arc-kompatibilis adatszolgáltatásokhoz szükséges környezet teljes mérete elsősorban a létrehozandó adatbázis-példányok számának és méretének függvénye.  A teljes méret nehéz lehet előre jelezni, hogy a példányok száma növekedni fog, és az egyes adatbázis-példányokhoz szükséges erőforrások mennyisége is változik.

Egy adott Azure arc-kompatibilis adatszolgáltatási környezet alapmérete az adatvezérlő mérete, amelyhez 4 mag és 16 GB RAM szükséges.  Innen felveheti az adatbázis-példányokhoz szükséges magok és memória összesített összegét.  SQL felügyelt példány esetén a hüvelyek száma megegyezik a létrehozott SQL felügyelt példányok számával.  A PostgreSQL nagy kapacitású-kiszolgálócsoportok esetében a hüvelyek száma megegyezik a munkavégző csomópontok számával és a koordinátori csomóponttal.  Ha például egy 3 feldolgozó csomóponttal rendelkező PostgreSQL-kiszolgáló csoporttal rendelkezik, a hüvelyek teljes száma 4 lesz.

Az egyes adatbázis-példányokhoz tartozó magok és memória mellett adja hozzá a 250m és a RAM-250Mi az ügynök-tárolók számára.

A következő példa egy méretezési számítást mutat be.

Követelmények:

- **"SQL1"**: 1 SQL felügyelt példány 16 GB RAM-mal, 4 mag
- **"SQL2"**: 1 SQL felügyelt példány 256 GB RAM-mal, 16 mag
- **"Postgres1"**: 1 PostgreSQL nagy kapacitású-kiszolgálócsoport 4 feldolgozóval 12 GB RAM-mal, 4 mag

Méretezési számítások:

- A "SQL1" a következő: 1 Pod * ([16 GI RAM, 4 mag] + [250Mi RAM, 250m magok]) az ügynökökhöz/Pod = 16,25 GI RAM, 4,25 magok.
- A "SQL2" a következő: 1 Pod * ([256 GI RAM, 16 mag] + + [250Mi RAM, 250m magok]) az ügynökökhöz/Pod = 256,25 GI RAM, 16,25 magok.
- Az SQL 1 és az SQL 2 teljes mérete: (16,25 GB + 256,25 gi) = 272,5 GB RAM, (4,25 magok + 16,25 magok) = 20,5 mag.

- A "Postgres1" a következő: (4 feldolgozó hüvely + 1 koordinátor Pod) * ([12 GB RAM, 4 mag] + [250Mi RAM, 250m magok]) az ügynökökhöz/Pod = 61,25 GB RAM, 21,25 magok.

- Az adatbázis-példányokhoz szükséges teljes kapacitás: 272,5 GB RAM, 20,5 mag az SQL + 61,25 GB RAM, 21,25 magok a PostgreSQL nagy kapacitású = 333,75 GB RAM, 42,5 magok.

- Az adatbázis-példányokhoz és az adatvezérlőhöz szükséges teljes kapacitás: 333,75 GB RAM, 42,5 magok az adatbázis példányaihoz + 16 GB RAM, 4 mag az adatvezérlőhöz = 349,75 GB RAM, 46,5 magok.

A tárterület méretezésével kapcsolatos részletekért tekintse meg a [Storage-Configuration](storage-configuration.md) című cikket.

## <a name="other-considerations"></a>További szempontok

Ne feledje, hogy a magok vagy a RAM-ra vonatkozó adott adatbázis-példány méretére vonatkozó kérelem nem lépheti túl a fürt Kubernetes-csomópontjainak rendelkezésre álló kapacitását.  Ha például a Kubernetes-fürtben található legnagyobb Kubernetes-csomópont 256 GB RAM-mal és 24 maggal rendelkezik, nem fog tudni létrehozni egy adatbázis-példányt, amely 512 GB RAM-mal és 48 maggal rendelkezik.  

Érdemes fenntartani a rendelkezésre álló kapacitás legalább 25%-át a Kubernetes-csomópontokon, hogy a Kubernetes hatékonyan ütemezze a hüvelyek létrehozását, és lehetővé tegye a rugalmas skálázást és a hosszú távú növekedést igény szerint.  

A méretezési számításokban ne felejtse el felvenni a Kubernetes-rendszer hüvelyének erőforrás-követelményeit és minden egyéb olyan munkaterhelést, amely az Azure arc-kompatibilis adatszolgáltatásokkal azonos Kubernetes-fürtön is megoszthatja a kapacitást.

Ha magas rendelkezésre állást szeretne fenntartani a tervezett karbantartás és a katasztrófákra való folytonosság terén, meg kell terveznie, hogy a fürtben legalább egy Kubernetes-csomópont ne legyen elérhető az adott időpontban.  A Kubernetes megkísérli átütemezni azokat a hüvelyeket, amelyek egy adott csomóponton futnak karbantartás vagy hiba miatt.  Ha nincs elérhető kapacitás a többi csomóponton, a hüvelyek nem lesznek átütemezve a létrehozáshoz, amíg újra nem áll rendelkezésre a kapacitás.  A nagyméretű adatbázis-példányok esetében rendkívül körültekintően kell lennie.  Ha például csak egy Kubernetes-csomópont elég nagy ahhoz, hogy megfeleljen egy nagyméretű adatbázis-példány erőforrás-követelményeinek, és a csomópont meghibásodik, akkor a Kubernetes nem fogja tudni ütemezni, hogy az adatbázis-példány pod egy másik Kubernetes-csomópontra kerül.

Tartsa szem előtt a [Kubernetes-fürt méretének maximális korlátját](https://kubernetes.io/docs/setup/best-practices/cluster-large/) .

Lehetséges, hogy a Kubernetes rendszergazdája beállította az [erőforrás-kvótákat](https://kubernetes.io/docs/concepts/policy/resource-quotas/) a névtérben vagy a projektben.  Tartsa szem előtt ezeket az adatbázis-példányok méretének megtervezése során.
