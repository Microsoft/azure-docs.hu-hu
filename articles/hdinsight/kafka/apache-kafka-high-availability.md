---
title: Magas rendelkezésre állás a Apache Kafka-Azure HDInsight
description: Útmutató magas rendelkezésre állás biztosításához az Apache Kafka platformmal az Azure HDInsight szolgáltatásban. Megtudhatja, hogyan egyensúlyozza ki újra a partíciók replikáit a Kafkában, hogy különböző tartalék tartományban legyenek a HDInsightot tartalmazó Azure régióban.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/09/2019
ms.openlocfilehash: e3f31bc1876a0c1f90ad4882ff782be25b4b6ed6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98932761"
---
# <a name="high-availability-of-your-data-with-apache-kafka-on-hdinsight"></a>Magas rendelkezésre állású adatok a HDInsightban futó Apache Kafka platformmal

Megtudhatja, hogyan konfigurálhat partíciós replikákat Apache Kafka témakörökhöz, hogy kihasználhassa a mögöttes hardveres állvány konfigurációját. Ez a konfiguráció biztosítja a HDInsighton az Apache Kafka platformon tárolt adatok rendelkezésre állását.

## <a name="fault-and-update-domains-with-apache-kafka"></a>Hibák és frissítési tartományok Apache Kafka

A tartalék tartomány az alapul szolgáló hardver logikai csoportosítása egy Azure-adatközpontban. Mindegyik tartalék tartomány közös áramforrással és hálózati kapcsolóval rendelkezik. A HDInsight-fürtön belül a csomópontokat implementáló virtuális gépek és felügyelt lemezek ezek között a tartalék tartományok között vannak elosztva. Ez az architektúra csökkenti a fizikai hardverhibák lehetséges hatását.

Mindegyik Azure-régió meghatározott számú tartalék tartománnyal rendelkezik. A tartományok listáját és a bennük található tartalék tartományok számát a [Rendelkezésre állási készletek](../../virtual-machines/availability.md#availability-sets) dokumentációjában találja.

> [!IMPORTANT]  
> A Kafka nem kezeli a tartalék tartományokat. Amikor létrehoz egy témakört a Kafkában, az lehet hogy minden partícióreplikát ugyanabban a tartalék tartományban tárol. Ennek a problémának a megoldásához a HDInsight a [Kafka partíció-újraegyensúlyozó eszközt](https://github.com/hdinsight/hdinsight-kafka-tools) biztosítja.

## <a name="when-to-rebalance-partition-replicas"></a>Mikor van szükség a partícióreplikák újraegyensúlyozására?

A Kafka-adatok lehető legmagasabb rendelkezésre állásának biztosításához a következő időpontokban kell újra egyensúlyoznia a partícióreplikákat a témaköréhez:

* Új témakör vagy partíció létrehozásakor

* Fürt vertikális felskálázásakor

## <a name="replication-factor"></a>Replikációs tényező

> [!IMPORTANT]  
> Javasoljuk, hogy olyan Azure-régiót használjon, amely három tartalék tartományt tartalmaz, és használjon 3-as replikációs tényezőt.

Ha kénytelen olyan régiót használni, amely csak két tartalék tartomány tartalmaz, használjon 4-es replikációs tényezőt, hogy egyenletesen ossza el a replikákat a két tartalék tartományban.

A témakörök létrehozásával és a replikációs tényező beállításával kapcsolatos példát a [Start with apache Kafka on HDInsight](apache-kafka-get-started.md) Document című témakörben talál.

## <a name="how-to-rebalance-partition-replicas"></a>A partícióreplikák újraegyensúlyozása

A kiválasztott témakörök újraelosztásához használja a [Apache Kafka Partition rebalance eszközt](https://github.com/hdinsight/hdinsight-kafka-tools) . Ezt az eszközt egy SSH-munkamenetből kell futtatni a Kafka-fürt főcsomópontjához.

A HDInsight-hoz SSH-val való kapcsolódásról további információért lásd az [SSH használata a HDInsighttal](../hdinsight-hadoop-linux-use-ssh-unix.md) dokumentumot.

## <a name="next-steps"></a>Következő lépések

* [Apache Kafka skálázhatósága a HDInsight](apache-kafka-scalability.md)
* [Tükrözés Apache Kafka a HDInsight-on](apache-kafka-mirroring.md)