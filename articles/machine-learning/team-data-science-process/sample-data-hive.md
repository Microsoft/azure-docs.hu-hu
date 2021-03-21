---
title: Mintaadatok az Azure HDInsight-struktúra tábláiban – csoportos adatelemzési folyamat
description: Az Azure HDInsight-struktúra tábláiban tárolt, struktúra-lekérdezéseket használó adatmennyiség, amely az adatmennyiséget az elemzéshez könnyebben kezelhető méretre csökkenti.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 6a015da77cb7c0ba54be1dd5e729a9ee8a848c9d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93321880"
---
# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Adatmintavétel az Azure HDInsight Hive-táblákban
Ez a cikk azt ismerteti, hogyan lehet lekérdezni az Azure HDInsight-struktúra tábláiban tárolt adatmintákat a kaptár-lekérdezések használatával, hogy az elemzéshez könnyebben kezelhető méretre csökkentse. Három népszerű használatú mintavételi módszert foglal magában:

* Egységes véletlenszerű mintavétel
* Véletlenszerű mintavételezés csoportok szerint
* Rétegzett mintavételezés

**Miért érdemes felvenni az adatait?**
Ha az elemezni kívánt adatkészlet nagy méretű, általában egy jó ötlet, hogy lerövidítse az adatokat, hogy csökkentse azt kisebb, de reprezentatív és felügyelhető méretre. A leállási mintavételezés megkönnyíti az adatfelismerést, a feltárást és a szolgáltatások fejlesztését. A csapat adatelemzési folyamatának szerepe az adatfeldolgozási függvények és a gépi tanulási modellek gyors prototípusának engedélyezése.

Ez a mintavételi feladat a [csoportos adatelemzési folyamat (TDSP)](./index.yml)egyik lépése.

## <a name="how-to-submit-hive-queries"></a>Struktúra-lekérdezések beküldése
A kaptár-lekérdezések a Hadoop-fürt fő csomópontján található Hadoop Command-Line konzolról is elindíthatók.  Jelentkezzen be a Hadoop-fürt fő csomópontjára, nyissa meg a Hadoop Command-Line konzolt, és küldje el innen a kaptár lekérdezéseit. A kaptár-lekérdezések a Hadoop Command-Line-konzolon való elküldésével kapcsolatos utasításokért lásd: [a kaptár-lekérdezések elküldése](move-hive-tables.md#submit).

## <a name="uniform-random-sampling"></a><a name="uniform"></a> Egységes véletlenszerű mintavétel
Az egységes véletlenszerű mintavételezés azt jelenti, hogy az adatkészletben lévő minden egyes sor a mintavétel során egyenlő eséllyel szerepel. Ezt egy extra mező () a belső "Select" lekérdezésben megadott adatkészletbe, a külső "Select" lekérdezésen belül, az adott véletlenszerű mezőre való felvételével lehet megvalósítani.

Itt láthat egy példalekérdezést:

```python
SET sampleRate=<sample rate, 0-1>;
select
    field1, field2, …, fieldN
from
    (
    select
        field1, field2, …, fieldN, rand() as samplekey
    from <hive table name>
    )a
where samplekey<='${hiveconf:sampleRate}'
```

Itt `<sample rate, 0-1>` adhatja meg a rekordok azon hányadát, amelyet a felhasználók szeretne felvenni.

## <a name="random-sampling-by-groups"></a><a name="group"></a> Véletlenszerű mintavételezés csoportok szerint
A kategorikus azonosítók mintavétele során érdemes lehet bevenni vagy kizárni az összes példányt a kategorikus változó egyes értékeinél. Ezt a fajta mintavételezést "mintavételezés csoportonként" nevezzük. Ha például egy "*State*" kategorikus változóval rendelkezik, amely olyan értékeket tartalmaz, mint például a NY, a ma, a CA, a NJ és a PA, akkor az egyes állapotokból származó rekordokat össze kell állítani, függetlenül attól, hogy mintát vesznek-e.

Íme egy példa a Group:

```python
SET sampleRate=<sample rate, 0-1>;
select
    b.field1, b.field2, …, b.catfield, …, b.fieldN
from
    (
    select
        field1, field2, …, catfield, …, fieldN
    from <table name>
    )b
join
    (
    select
        catfield
    from
        (
        select
            catfield, rand() as samplekey
        from <table name>
        group by catfield
        )a
    where samplekey<='${hiveconf:sampleRate}'
    )c
on b.catfield=c.catfield
```

## <a name="stratified-sampling"></a><a name="stratified"></a>Rétegzett mintavételezés
A véletlenszerű mintavételezés egy kategorikus változóra vonatkozik, ha a beszerzett minták olyan kategorikus értékekkel rendelkeznek, amelyek ugyanabban az arányban találhatók, mint a szülő populációban. Ha ugyanezt a példát használja, tegyük fel, hogy az adatai a következő állapotok szerint jelennek meg: NJ 100 észrevételt tartalmaz, a NY 60 megfigyelésekkel rendelkezik, és a WA rendelkezik 300-megjegyzésekkel. Ha azt adja meg, hogy a rétegzett mintavételezés sebessége 0,5, akkor a beszerzett minta a NJ, NY és WA megközelítőleg 50, 30 és 150 megfigyeléssel rendelkezik.

Itt láthat egy példalekérdezést:

```hiveql
SET sampleRate=<sample rate, 0-1>;
select
    field1, field2, field3, ..., fieldN, state
from
    (
    select
        field1, field2, field3, ..., fieldN, state,
        count(*) over (partition by state) as state_cnt,
          rank() over (partition by state order by rand()) as state_rank
      from <table name>
    ) a
where state_rank <= state_cnt*'${hiveconf:sampleRate}'
```

További információ a kaptárban elérhető fejlettebb mintavételi módszerekről: [LanguageManual mintavétel](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling).