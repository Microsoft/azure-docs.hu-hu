---
title: Az F5 ASM-adatbázis összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan hívhatja le az F5 ASM-naplókat az Azure Sentinel szolgáltatásba az F5 ASM adatösszekötő használatával. Megtekintheti az F5-es ASM-adatlemezeket, riasztásokat hozhat létre, és javíthatja a vizsgálatot.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/20/2020
ms.author: yelevin
ms.openlocfilehash: 81a0f0e01827c48518f2eb1fba062e9e6536d9df
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94655697"
---
# <a name="connect-f5-asm-to-azure-sentinel"></a>Az F5 ASM és az Azure Sentinel összekötése

Ez a cikk azt ismerteti, hogyan használható az F5 ASM adatösszekötő az F5 ASM-naplók az Azure Sentinelbe való egyszerű lekéréséhez. Ez lehetővé teszi, hogy megtekintse az F5-es ASM-adatlapokat a munkafüzetekben, hogy egyéni riasztásokat hozzon létre, és beépítse azt a vizsgálat javítására. Ha az F5 ASM-adatok az Azure Sentinelben jelennek meg, a szervezet webalkalmazás-biztonságával kapcsolatos további betekintést nyújt, és fokozza a biztonsági műveletek képességeit. 

## <a name="configure-your-f5-asm-to-send-cef-messages"></a>Az F5 ASM beállítása CEF üzenetek küldéséhez

1. A távoli naplózás beállításához kövesse az [alkalmazás-biztonsági események naplózása az F5](https://techdocs.f5.com/kb/en-us/products/big-ip_asm/manuals/product/asm-implementations-11-5-0/12.html) -ben című témakör utasításait az alábbi irányelvek alapján:
   - Állítsa a **távoli tárterület típusát** **CEF** értékre.
   - Állítsa a **protokollt** a **TCP** értékre.
   - Állítsa be az **IP-címet** a syslog-kiszolgáló IP-címére.
   - Állítsa a **portszámot** **514**-re, vagy azt a portot, amelyet az ügynöknek használnia kell.
   - Beállíthatja a **lekérdezési karakterlánc maximális méretét** az ügynökben beállított méretre.

1. A CEF-események Log Analytics vonatkozó sémájának használatához keresse meg a következőt: `CommonSecurityLog` .

1. Folytassa a [3. lépéssel: a kapcsolat ellenőrzése](connect-cef-verify.md).


## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtanulta, hogyan csatlakoztatható az F5 ASM az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](./tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.