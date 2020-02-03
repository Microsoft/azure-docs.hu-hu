---
title: Adatmintavétel az Azure HDInsight Hive-táblák – csoportos adatelemzési folyamat
description: Lefelé-minta használatával Hive-lekérdezések csökkentése érdekében az adatok könnyebben kezelhető elemzéshez méretre Azure HDInsight Hive-táblákban tárolt adatokat.
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
ms.openlocfilehash: df85edc3de00e2b0342bc3102fe9e85564a9835b
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719993"
---
# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Adatmintavétel az Azure HDInsight Hive-táblákban
Ez a cikk bemutatja, hogyan való Hive-lekérdezések segítségével könnyebben kezelhető elemzéshez méretre csökkenteni az Azure HDInsight Hive-táblákban tárolt adatokat. Három népszerű használatú mintavételi módszert foglal magában:

* Egységes véletlenszerű mintavétel
* A csoportok véletlenszerű mintavétel
* Rétegzett mintavétel

**Miért érdemes felvenni az adatait?**
Ha azt tervezi, hogy elemezheti az adatkészlet túl nagy, általában egy célszerű való az adatokat egy kisebb, de reprezentatív és könnyebben kezelhető méretű-re csökkenteni. Alsó-mintavétel segíti az adatok megértését, feltárási és funkciófejlesztési. A csoportos adatelemzési folyamat szerepét, hogy az adatokat feldolgozó függvények és a gépi tanulási modellek gyors prototípus-készítés engedélyezése.

Ez a mintavételi feladat a [csoportos adatelemzési folyamat (TDSP)](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)egyik lépése.

## <a name="how-to-submit-hive-queries"></a>Hogyan lehet elküldeni a Hive-lekérdezések
Hive-lekérdezések a Hadoop parancssori konzolból, az a Hadoop-fürt fő csomópontjának küldheti.  Jelentkezzen be a Hadoop-fürt fő csomópontjára, nyissa meg a Hadoop parancssori konzolt, és küldje el innen a kaptár-lekérdezéseket. A kaptár-lekérdezések a Hadoop parancssori konzolon való elküldésével kapcsolatos utasításokért lásd: [a struktúra-lekérdezések elküldése](move-hive-tables.md#submit).

## <a name="uniform"></a>Egységes véletlenszerű mintavétel
Egységes véletlenszerű mintavételi jelenti, hogy a készlet minden egyes sorára mintavételezése egyenlő esélye. Adja hozzá egy kiegészítő mezőt rand() az adatkészletben a belső "kijelölése" lekérdezésben, és a külső "kijelölése" lekérdezésben a feltételhez véletlenszerű mező hajtható végre.

Itt láthat egy példalekérdezést:

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

Itt `<sample rate, 0-1>` megadja a rekordok azon hányadát, amelyet a felhasználók szeretne felvenni.

## <a name="group"></a>Véletlenszerű mintavételezés csoportok szerint
Ha kategorikus adatok mintavételekor érdemes vagy vagy kizárja az összes példányainak néhány a kategorikus változó értékét. Az ilyen mintavételi "csoport által mintavételi" nevezzük. Ha például egy "*State*" kategorikus változóval rendelkezik, amely olyan értékeket tartalmaz, mint például a NY, a ma, a CA, a NJ és a PA, akkor az egyes állapotokból származó rekordokat össze kell állítani, függetlenül attól, hogy mintát vesznek-e.

Itt láthat egy példalekérdezést, hogy a minták csoport szerint:

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

## <a name="stratified"></a>Rétegzett mintavételezés
A szúrópróbaszerű műanyaggal vonatkozóan egy kategorikus változó rétegezett Ha kapott mintákat, amelyek szerepelnek a méretarány, a szülő feltöltése a kategorikus értékeket. Ugyanebben a példában használja a fenti, tegyük fel, hogy az adatok a következő megfigyeléseken rendelkezik állapot szerinti: Jersey 100 megfigyelések, NY rendelkezik 60 megfigyelések, pedig WA 300 megfigyeléseket. Ha megadja a 0.5-ös lennie rétegzett mintavételi aránya, akkor a mintát a kell körülbelül 50, 30 és 150 megfigyelések Jersey, NY és WA jelölik.

Itt láthat egy példalekérdezést:

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


További információ a kaptárban elérhető fejlettebb mintavételi módszerekről: [LanguageManual mintavétel](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling).

