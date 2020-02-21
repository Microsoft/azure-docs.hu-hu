---
title: Az Azure HDInsight által támogatott csomópont-konfigurációk
description: A HDInsight-fürtcsomópontok minimális és ajánlott konfigurációinak megismerése.
keywords: VM-méretek, szektorcsoport-méretek, fürtkonfiguráció
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: e75146266568001d8fee7be26898ac8bdfffb7fc
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484786"
---
# <a name="what-are-the-default-and-recommended-node-configurations-for-azure-hdinsight"></a>Mik az Azure HDInsight alapértelmezett és ajánlott csomópont-konfigurációi?

Ez a cikk az Azure HDInsight-fürtök alapértelmezett és ajánlott csomópont-konfigurációit ismerteti.

## <a name="default-and-minimum-recommended-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Az alapértelmezett és a minimálisan ajánlott csomópont-konfiguráció és a virtuális gépek mérete a fürtökhöz

A következő táblázatok a HDInsight-fürtök alapértelmezett és ajánlott virtuálisgép-méreteit sorolja fel.  Ezek az információk szükségesek ahhoz, hogy megértsék a virtuálisgép-méreteket, amelyeket a PowerShell vagy az Azure CLI-parancsfájlok létrehozásakor kell használni a HDInsight-fürtök telepítéséhez.

Ha a fürtben több mint 32 feldolgozó csomópontra van szüksége, válasszon ki legalább 8 maggal és 14 GB RAM-mal rendelkező fő csomópont-méretet. 

A csak az adatlemezeket tartalmazó fürtök a Kafka és a HBase fürtök, amelyeken engedélyezve van a gyorsított írási funkció. A HDInsight ezekben a forgatókönyvekben támogatja a P30 és a S30 lemez méretét.

A jelen dokumentumban használt minimálisan ajánlott virtuálisgép-típusok specifikációit a következő táblázat foglalja össze.

| Méret              | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Ideiglenes tárterület maximális teljesítménye: IOPS / Olvasási MBps / Írási MBps | Adatlemezek max. száma / teljesítménye: IOPS | Hálózati adapterek max. száma / várt hálózati sávszélesség (Mbps) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D3_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 16/16x500           | 4 / 3000                                       |
| Standard_D4_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 32/32x500           | 8 / 6000                                       |
| Standard_D5_v2 | 16   | 56          | 800                    | 48000 / 750 / 375                                          | 64/64x500           | 8 / 12000                                    |
| Standard_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16x500                         | 4 / 3000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32x500                       | 8 / 6000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64 / 64x500                       | 8 / 12000          |
| Standard_A1_v2  | 1         | 2           | 10             | 1000 / 20 / 10                                           | 2 / 2x500               | 2 / 250                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000 / 40 / 20                                           | 4 / 4x500               | 2 / 500                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000 / 80 / 40                                           | 8 / 8x500               | 4 / 1000                     |

Az egyes virtuálisgép-típusok leírásával kapcsolatos további információkért tekintse meg a következő dokumentumokat:

* [Általános célú virtuálisgép-méretek: Dv2 sorozat 1-5](../virtual-machines/dv2-dsv2-series.md)
* [Memória-optimalizált virtuális gépek méretei: Dv2 sorozat 11-15](../virtual-machines/dv2-dsv2-series-memory.md)
* [Általános célú virtuálisgép-méretek: Av2 sorozat 1-8](../virtual-machines/av2-series.md)

### <a name="all-supported-regions-except-brazil-south-and-japan-west"></a>Minden támogatott régió, kivéve Dél-Brazília és Nyugat-Japán

> [!Note]
> A PowerShell-ben és más parancsfájlokban használható SKU-azonosító lekéréséhez vegyen fel `Standard_`t az alábbi táblázatokban szereplő összes VM-SKU elejére. Például `D12_v2` válna `Standard_D12_v2`.

| Fürt típusa | Hadoop | HBase | Interaktív lekérdezés | Storm | Spark | ML Server | Kafka |
|---|---|---|---|---|---|---|---|
| Fej: alapértelmezett VM-méret | D12_v2 | D12_v2 | D13_v2 | A4_v2 | D12_v2, <br/>D13_v2 * | D12_v2 | D3_v2 |
| Head: a minimálisan ajánlott virtuálisgép-méretek | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2, <br/>D13_v2 * | D12_v2 | D3_v2 |
| Worker: alapértelmezett VM-méret | D4_v2 | D4_v2 | D14_v2 | D3_v2 | D13_v2 | D4_v2 | 4 D12_v2, 2 S30 lemezzel |
| Worker: minimálisan ajánlott virtuálisgép-méretek | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 | D3_v2 |
| ZooKeeper: alapértelmezett VM-méret |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 | A4_v2 |
| ZooKeeper: minimálisan ajánlott virtuálisgép-méretek |  | A4_v2 | A4_v2 | A2_v2 |  | A2_v2 | A4_v2 |
| ML szolgáltatások: alapértelmezett VM-méret |  |  |  |  |  | D4_v2 |  |
| ML szolgáltatások: minimálisan ajánlott virtuálisgép-méret |  |  |  |  |  | D4_v2 |  |

\* = virtuálisgép-méretek a Spark Enterprise Security Package (ESP) fürtökhöz

### <a name="brazil-south-and-japan-west-only"></a>Dél-Brazília és Nyugat-Japán

| Fürt típusa | Hadoop | HBase | Interaktív lekérdezés | Storm | Spark | ML-szolgáltatások |
|---|---|---|---|---|---|---|
| Fej: alapértelmezett VM-méret | D12 | D12 | D13 | A4_v2 | D12 | D12 |
| Head: a minimálisan ajánlott virtuálisgép-méretek | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2 | D12_v2 |
| Worker: alapértelmezett VM-méret | D4 | D4 | D14 | D3 | D13 | D4 |
| Worker: minimálisan ajánlott virtuálisgép-méretek | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 |
| ZooKeeper: alapértelmezett VM-méret |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| ZooKeeper: minimálisan ajánlott virtuálisgép-méretek |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| ML szolgáltatások: alapértelmezett VM-méretek |  |  |  |  |  | D4 |
| ML szolgáltatások: minimálisan ajánlott virtuálisgép-méretek |  |  |  |  |  | D4_v2 |

> [!NOTE]
> - A *Nimbus* a Storm-fürt típusának nevezzük.
> - A Worker neve a Storm-fürt típusának *felügyelője* .
> - A feldolgozó neve a HBase-fürt típusának *régiója* .

## <a name="next-steps"></a>Következő lépések

* [Milyen Apache Hadoop-összetevők és-verziók érhetők el a HDInsight-ben?](hdinsight-component-versioning.md)
