---
title: A redukáló lassú az Azure HDInsight
description: Az Azure HDInsight lassú az esetleges adattorzítás
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 07/30/2019
ms.openlocfilehash: f9b6e0d862d17badb1caa672852214cdd86abb49
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98930821"
---
# <a name="scenario-reducer-is-slow-in-azure-hdinsight"></a>Forgatókönyv: a redukáló lassú az Azure HDInsight

Ez a cikk az interaktív lekérdezési összetevők Azure HDInsight-fürtökben való használatakor felmerülő problémák hibaelhárítási lépéseit és lehetséges megoldásait ismerteti.

## <a name="issue"></a>Probléma

Egy lekérdezés futtatásakor, például `insert into table1 partition(a,b) select a,b,c from table2` a lekérdezési terv elindít egy több szűkítőt, de az egyes partíciók adatainak egyetlen szűkítővel kell rendelkezniük. Ez azt eredményezi, hogy a lekérdezés lassú, mint a legnagyobb partíció-csökkentő által elkészített idő.

## <a name="cause"></a>Ok

Nyissa meg a [Beeline](../hadoop/apache-hadoop-use-hive-beeline.md) értéket, és ellenőrizze a készlet értékét `hive.optimize.sort.dynamic.partition` .

A változó értékét igaz/hamis értékre kell állítani az adattermészet alapján.

Ha a bemeneti táblában lévő partíciók kevesebbek (kevesebb mint 10), így a kimeneti partíciók száma, és a változó értéke `true` , ez az adatok globális rendezését és írását eredményezi a partíciók egy-egy csökkentőjának használatával. Még akkor is, ha a rendelkezésre álló szűkítők száma nagyobb, az Adateltérés miatt néhány szűkítőt is el lehet érni, és a maximális párhuzamosság nem érhető el. Ha a értékre módosul `false` , több szűkítő is kezelhet egyetlen partíciót, és több kisebb fájlt is kiírja a rendszer, ami gyorsabb beszúrást eredményez. Ez a további lekérdezésekre is hatással lehet, azonban kisebb fájlok jelenléte miatt.

A érték azt `true` indokolja, hogy a partíciók száma nagyobb legyen, és az adatmennyiség ne legyen elferdítve. Ebben az esetben a térképi fázis eredményét úgy kell kiírni, hogy az egyes partíciókat egyetlen redukáló kezelje, ami a későbbi lekérdezési teljesítményt eredményezi.

## <a name="resolution"></a>Feloldás

1. Próbálja megismételni az adatparticionálást több partícióra való normalizálás érdekében.

1. Ha #1 nem lehetséges, állítsa a konfiguráció értékét false értékre a Beeline-munkamenetben, majd próbálja megismételni a lekérdezést. `set hive.optimize.sort.dynamic.partition=false`. Az érték false-ra állítása a fürt szintjén nem ajánlott. Az értéke `true` optimális, és szükség szerint a paramétert az adatmennyiség és a lekérdezés jellege alapján állítja be.

## <a name="next-steps"></a>Következő lépések

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]