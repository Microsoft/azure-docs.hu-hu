---
title: Az Azure HDInsight-fürt megfelelő virtuálisgép-méretének kiválasztása
description: Megtudhatja, hogyan választhatja ki a HDInsight-fürthöz megfelelő virtuálisgép-méretet.
keywords: VM-méretek, szektorcsoport-méretek, fürtkonfiguráció
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: 51043f0a1009994528783a1b56ec5ccec68e99b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98931784"
---
# <a name="selecting-the-right-vm-size-for-your-azure-hdinsight-cluster"></a>A virtuális gép megfelelő méretének kiválasztása az Azure HDInsight-fürthöz

Ez a cikk azt ismerteti, hogyan választhatja ki a megfelelő virtuálisgép-méretet a HDInsight-fürt különböző csomópontjaihoz. 

Először is megtudhatja, hogy a virtuális gépek, például a CPU-feldolgozás, a RAM-méret és a hálózati késések tulajdonságai milyen hatással lesznek a számítási feladatok feldolgozására. Ezután gondoljon az alkalmazásra, és hogy az hogyan illeszkedik a különböző virtuálisgép-családokra. Győződjön meg arról, hogy a használni kívánt virtuálisgép-család kompatibilis-e a telepítendő fürt típusával. Az egyes fürtök összes támogatott és ajánlott virtuálisgép-méretének listáját lásd: az [Azure HDInsight által támogatott csomópont-konfigurációk](hdinsight-supported-node-configuration.md). Végül egy teljesítményteszt-folyamat segítségével tesztelheti a számítási feladatokat, és ellenőrizheti, hogy az adott családon belül melyik SKU megfelelő az Ön számára.

A fürt más szempontjainak megtervezésével, például a tárolási típus vagy a fürt méretének kiválasztásával kapcsolatos további információkért lásd: [HDInsight-fürtök kapacitásának megtervezése](hdinsight-capacity-planning.md).

## <a name="vm-properties-and-big-data-workloads"></a>Virtuális gép tulajdonságai és big data számítási feladatok

A virtuális gép méretét és típusát a CPU feldolgozási teljesítmény, a RAM mérete és a hálózati késés határozza meg:

- CPU: a virtuális gép mérete a magok számát határozza meg. Minél több magot használ, annál nagyobb az egyes csomópontok párhuzamos számítási foka. Emellett egyes virtuálisgép-típusok gyorsabb magokkal rendelkeznek.

- RAM: a virtuális gép mérete is a virtuális gépen elérhető RAM mennyiségét határozza meg. Olyan munkaterhelések esetében, amelyek a memóriában tárolt adatok feldolgozására, nem pedig a lemezről való olvasásra vonatkoznak, győződjön meg arról, hogy a feldolgozó csomópontok rendelkeznek elegendő memóriával az adatokhoz.

- Hálózat: a legtöbb fürt típusa esetén a fürt által feldolgozott adattípusok nem helyi lemezen, hanem külső tárolási szolgáltatásban, például Data Lake Storage vagy az Azure Storage-ban találhatók. Vegye figyelembe a hálózati sávszélességet és az átviteli sebességet a csomópont virtuális gépe és a Storage szolgáltatás között. A virtuális gépek számára elérhető hálózati sávszélesség általában nagyobb méretekben nő. Részletekért lásd: virtuálisgép- [méretek áttekintése](../virtual-machines/sizes.md).

## <a name="understanding-vm-optimization"></a>A virtuális gépek optimalizálásának ismertetése

Az Azure-beli virtuálisgép-családok különböző használati esetekre vannak optimalizálva. Az alábbi táblázatban megtalálja a legnépszerűbb használati eseteket és a nekik megfelelő virtuálisgép-családokat.

| Típus                     | Méretek           |    Leírás       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Belépési szint](../virtual-machines/sizes-general.md)          | A, Av2  | A CPU-teljesítmény és a memória-konfigurációk a legmegfelelőbbek a belépési szintű munkaterhelésekhez, például a fejlesztéshez és a teszteléshez. Ezek gazdaságos, és alacsony díjszabású lehetőséget biztosítanak az Azure megkezdésére. |
| [Általános célú](../virtual-machines/sizes-general.md)          | D, DSv2, Dv2  | Kiegyensúlyozott processzor-memória arány. Ideális választások a teszteléshez és a fejlesztéshez, a kicsi és közepes adatbázisokhoz, illetve az alacsony és közepes forgalmú webkiszolgálókhoz. |
| [Számításoptimalizált](../virtual-machines/sizes-compute.md)        | F           | Magas processzor-memória arány. Jó a közepes forgalmú webkiszolgálók, a hálózati berendezések, a kötegelt folyamatok és az alkalmazáskiszolgáló számára.        |
| [Memóriaoptimalizált](../virtual-machines/sizes-memory.md)         | Esv3, Ev3  | Magas memória-processzor arány. Ideális választások a relációs adatbázisok kiszolgálóihoz, a közepes és nagy gyorsítótárakhoz, valamint a memóriában végzett elemzésekhez.                 |

- A HDInsight által támogatott régiókban elérhető virtuálisgép-példányok díjszabásával kapcsolatos információkért lásd: [HDInsight díjszabása](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="cost-saving-vm-types-for-light-workloads"></a>A virtuálisgép-típusok költségmegtakarítása a könnyű számítási feladatokhoz

Ha fényfeldolgozási követelményekkel rendelkezik, az [F sorozat](https://azure.microsoft.com/blog/f-series-vm-size/) jó választás lehet a HDInsight megkezdéséhez. Az alacsonyabb óránkénti listaáron elérhető F-sorozat képviseli a legjobb ár–teljesítmény arányt az Azure-portfólióban az egy vCPU-ra jutó Azure-alapú számítási egységek (Azure Compute Unit, ACU) alapján.

A következő táblázat a Fsv2-sorozatú virtuális gépekkel létrehozható fürtök típusát és csomópont-típusait ismerteti.

| Fürt típusa | Verzió | Munkavégző csomópont | Fő csomópont | Zookeeper csomópont |
|---|---|---|---|---|
| Spark | Mind | F4 és újabb verziók | nem | nem |
| Hadoop | Mind | F4 és újabb verziók | nem | nem |
| Kafka | Mind | F4 és újabb verziók | nem | nem |
| HBase | Mind | F4 és újabb verziók | nem | nem |
| LLAP | tiltva | nem | nem | nem |
| Storm | tiltva | nem | nem | nem |
| ML szolgáltatás | CSAK A HDI 3,6 | F4 és újabb verziók | nem | nem |

Az egyes F sorozatú SKU-k specifikációinak megtekintéséhez lásd: [f sorozatú virtuális gépek mérete](https://azure.microsoft.com/blog/f-series-vm-size/).

## <a name="benchmarking"></a>Teljesítménymérésre

A teljesítményértékelés a szimulált számítási feladatok különböző virtuális gépeken való futtatásának folyamata, amellyel mérhető, hogy az éles számítási feladatokhoz milyen jól fognak elvégezni. 

A virtuális gépek és a fürtök méretének összehasonlításával kapcsolatos további információkért lásd: a [fürt kapacitásának megtervezése az Azure HDInsight-ben ](hdinsight-capacity-planning.md#choose-the-vm-size-and-type).

## <a name="next-steps"></a>Következő lépések

- [Az Azure HDInsight által támogatott csomópont-konfigurációk](hdinsight-supported-node-configuration.md)
- [Linuxos virtuális gépek méretei az Azure-ban](../virtual-machines/sizes.md)