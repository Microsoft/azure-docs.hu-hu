---
title: Regionális redundancia és feladatátvételi helyreállítás az Azure HPC cache-vel
description: Az Azure HPC cache-vel való vész-helyreállítási feladatátvételi képességeket biztosító technikák
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: v-erkel
ms.openlocfilehash: 9159807f55ae52393b8fccec339fcc94c3e4ebb0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87061382"
---
# <a name="use-multiple-caches-for-regional-failover-recovery"></a>Több gyorsítótár használata a regionális feladatátvétel helyreállításához

Minden egyes Azure HPC cache-példány egy adott előfizetésen belül és egy régióban fut. Ez azt jelenti, hogy a gyorsítótár munkafolyamata valószínűleg megszakadhat, ha a régió teljes leállás miatt leáll.

Ez a cikk a gyorsítótár-feladatátvétel második régiójának használatával csökkenti a munkahelyi fennakadások kockázatát.

A kulcs olyan háttérbeli tárterületet használ, amely több régióból is elérhető. Ez a tároló lehet a megfelelő DNS-támogatással rendelkező helyszíni NAS-rendszer, vagy az Azure Blob Storage, amely egy másik régióban található a gyorsítótárból.

Ahogy a munkafolyamat az elsődleges régióban folytatja, az adatait a régión kívüli hosszú távú tárolóba menti a rendszer. Ha a gyorsítótár-régió elérhetetlenné válik, létrehozhat egy duplikált Azure HPC cache-példányt egy másodlagos régióban, csatlakozhat ugyanahhoz a tárolóhoz, és folytathatja a munkát az új gyorsítótárból.

## <a name="planning-for-regional-failover"></a>Regionális feladatátvétel tervezése

A lehetséges feladatátvételre előkészített gyorsítótár beállításához kövesse az alábbi lépéseket:

1. Győződjön meg arról, hogy a háttérbeli tárterület egy második régióban is elérhető.
1. Az elsődleges gyorsítótár-példány létrehozásának tervezésekor elő kell készítenie a telepítési folyamat replikálását a második régióban is. Elemek belefoglalása:

   1. Virtuális hálózat és alhálózati struktúra
   1. Gyorsítótár kapacitása
   1. Tárolási cél részletei, nevek és névtér elérési útjai
   1. Az ügyfélgépek adatai, ha azok ugyanabban a régióban találhatók, mint a gyorsítótár
   1. Csatlakoztatási parancs gyorsítótár-ügyfelek általi használatra

   > [!NOTE]
   > Az Azure HPC cache programozott módon hozható létre [Azure Resource Manager sablonnal](../azure-resource-manager/templates/overview.md) vagy közvetlenül az API-hoz való hozzáféréssel. Részletekért forduljon az Azure HPC cache csapatához.

## <a name="failover-example"></a>Példa feladatátvételre

Tegyük fel például, hogy az Azure HPC-gyorsítótárát szeretné megkeresni az Azure USA keleti régiójában. A szolgáltatás a helyszíni adatközpontban tárolt adataihoz fog hozzáférni.

A gyorsítótár az USA 2. nyugati régiójában feladatátvételi biztonsági mentésként használható.

Ha a gyorsítótárat az USA keleti régiójában hozza létre, készítse el a második gyorsítótárat az USA 2. nyugati régiójában. Az előkészítés automatizálásához parancsfájlokat vagy sablonokat használhat.

Ha az USA keleti régiójában nem sikerül az egész régióra kiterjedő meghibásodás, hozza létre az USA 2. nyugati régiójában előkészített gyorsítótárat.

A gyorsítótár létrehozása után adja hozzá azokat a tárolási célokat, amelyek ugyanarra a helyszíni adattárakra mutatnak, és ugyanazokat az összesített névtereket használják, mint a régi gyorsítótár tárolási céljait.

Ha az eredeti ügyfelek érintettek, hozzon létre új ügyfeleket az USA 2. nyugati régiójában az új gyorsítótárral való használatra.

Minden ügyfélnek csatlakoztatnia kell az új gyorsítótárat, még akkor is, ha az ügyfeleket nem befolyásolta a régió meghibásodása. Az új gyorsítótár a régitől eltérő csatlakoztatási címmel rendelkezik.

## <a name="learn-more"></a>Tudjon meg többet

Az Azure-alkalmazás architektúrájának útmutatója további információkat tartalmaz arról, hogyan lehet [helyreállítani az egész régióra kiterjedő szolgáltatás megszakadását](<https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region>).
