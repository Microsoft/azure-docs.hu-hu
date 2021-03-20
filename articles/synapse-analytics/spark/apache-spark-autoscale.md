---
title: Apache Spark példányok automatikus méretezése
description: Az Azure szinapszis automatikus méretezés funkciójának használata az Apache Spark-példányok automatikus méretezéséhez
author: euangMS
ms.author: euang
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 03/31/2020
ms.openlocfilehash: f34bcfa8b743fbee6ee3b78fc1a042d1df0abfde
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93313639"
---
# <a name="automatically-scale-azure-synapse-analytics-apache-spark-pools"></a>Az Azure szinapszis Analytics Apache Spark-készletek automatikus méretezése

Az Azure Synapse Analyticshez készült Apache Spark-készlet automatikus skálázási funkciója automatikusan fel- és leskálázza a fürtpéldányban lévő csomópontok számát. Egy új Azure Synapse Analyticshez készült Apache Spark-készlet létrehozásakor megadhatja a csomópontok minimális és maximális számát, ha az automatikus skálázás ki lett választva. Az automatikus skálázás ezután monitorozza a terhelés erőforrás-követelményeit, és fel- vagy leskálázza a csomópontok számát. Ez a funkció felár nélkül használható.

## <a name="metrics-monitoring"></a>Metrikák figyelése

Az autoscale folyamatosan figyeli a Spark-példányt, és a következő metrikákat gyűjti:

|Metric|Leírás|
|---|---|
|Függőben lévő CPU összesen|Az összes függőben lévő csomópont végrehajtásának megkezdéséhez szükséges magok teljes száma.|
|Függőben lévő memória összesen|Az összes függőben lévő csomópont végrehajtásának megkezdéséhez szükséges teljes memória (MB).|
|Teljes szabad processzor|Az aktív csomópontokon fel nem használt magok összege.|
|Szabad memória összesen|A nem használt memória (MB) összege az aktív csomópontokon.|
|Felhasznált memória/csomópont|A csomópont terhelése. A 10 GB memóriát használó csomópontok nagyobb terhelésnek számítanak, mint a 2 GB-nyi memóriát használó feldolgozók.|

A fenti mérőszámok 30 másodpercenként vannak ellenőrizve. Az autoskálázás a mérőszámok alapján vertikális felskálázást és leskálázást tesz lehetővé.

## <a name="load-based-scale-conditions"></a>Terhelésen alapuló méretezési feltételek

Ha a következő feltételek észlelhetők, az autoskálázás egy méretezési kérelmet fog kiadni:

|Vertikális felskálázás|Vertikális leskálázás|
|---|---|
|A függőben lévő PROCESSZORok teljes száma nagyobb, mint 1 percenként az összes szabad processzor.|A függőben lévő PROCESSZORok száma több mint 2 percnél kevesebb, mint az összes szabad CPU.|
|A függőben lévő memória teljes mérete nagyobb, mint 1 percenkénti teljes szabad memória.|A függőben lévő memória teljes mérete kevesebb, mint 2 percnél nagyobb a teljes szabad memória.|

A vertikális felskálázáshoz az Azure szinapszis automatikusan kiszámítja, hogy hány új csomópontra van szükség a jelenlegi CPU-és memória-követelmények teljesítéséhez, majd kiadja a szükséges számú csomópont hozzáadására szolgáló Felskálázási kérést.

A leskálázás, a végrehajtók száma, az alkalmazás főkiszolgálói és a jelenlegi CPU-és memória-követelmények alapján, az autoskálázás egy adott számú csomópont eltávolítására irányuló kérést bocsát ki. A szolgáltatás azt is észleli, hogy mely csomópontok vannak kiválasztva az eltávolításra a jelenlegi feladatok végrehajtása alapján. A leskálázási művelet először leszereli a csomópontokat, majd eltávolítja őket a fürtből.

## <a name="get-started"></a>Bevezetés

### <a name="create-a-serverless-apache-spark-pool-with-autoscaling"></a>Kiszolgáló nélküli Apache Spark készlet létrehozása automatikus skálázással

Az autoscale funkció engedélyezéséhez hajtsa végre a következő lépéseket a készlet normál létrehozási folyamatának részeként:

1. Az **alapvető beállítások** lapon jelölje be az **autoskálázás engedélyezése** jelölőnégyzetet.
1. Adja meg a kívánt értékeket a következő tulajdonságokhoz:  

    * Csomópontok **minimális** száma
    * Csomópontok **maximális** száma.

A csomópontok kezdeti száma lesz a minimum. Ez az érték határozza meg a példány kezdeti méretét a létrehozáskor. A csomópontok minimális száma nem lehet kisebb, mint három.

## <a name="best-practices"></a>Ajánlott eljárások

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>Vegye figyelembe a vertikális Felskálázási vagy leskálázási műveletek késését

A skálázási művelet befejezéséhez 1 – 5 percet is igénybe vehet.

### <a name="prepare-for-scaling-down"></a>Felkészülés a méretezésre

A példány skálázási folyamata során az automatikus skálázás leszerelési állapotba helyezi a csomópontokat, így a csomóponton nem indíthatók el új végrehajtók.

A futó feladatok továbbra is futnak és befejeződik. A függőben lévő feladatok várhatóan a megszokott módon lesznek ütemezve kevesebb elérhető csomóponttal.

## <a name="next-steps"></a>Következő lépések

Új Spark-készlet létrehozásának első lépései [Spark-készlet létrehozása](../quickstart-create-apache-spark-pool-portal.md)
