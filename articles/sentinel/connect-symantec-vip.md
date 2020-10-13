---
title: Symantec VIP-adatkapcsolatok összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan csatlakoztathatók a Symantec VIP-adatszolgáltatások az Azure Sentinelhez.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 2809ff6abe36956123e0e581e16aa85edeb40395
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87096809"
---
# <a name="connect-your-symantec-vip-to-azure-sentinel"></a>A Symantec VIP összekötése az Azure Sentinel-vel

> [!IMPORTANT]
> A Symantec VIP-adatösszekötő az Azure Sentinel szolgáltatásban jelenleg nyilvános előzetes verzióban érhető el.
> Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a cikk azt ismerteti, hogyan csatlakoztatható a [Symantec VIP](https://vip.symantec.com/) -berendezés az Azure sentinelhez. A Symantec VIP-adatösszekötővel könnyedén csatlakoztathatja a Symantec VIP-naplóit az Azure Sentinel használatával, megtekintheti az irányítópultokat, egyéni riasztásokat hozhat létre, és javíthatja a vizsgálatot. A Symantec VIP és az Azure Sentinel közötti integráció a syslog használatát teszi lehetővé.

> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="forward-symantec-vip-logs-to-the-syslog-agent"></a>Symantec VIP-naplók továbbítása a syslog-ügynökhöz  

Konfigurálja a Symantec VIP-t, hogy a syslog-ügynök használatával továbbítsa a syslog-üzeneteket az Azure-munkaterületre.

1. Az Azure Sentinel portálon kattintson az **adatösszekötők** elemre, és válassza a **Symantec VIP** -összekötő lehetőséget.

1. Válassza az **összekötő megnyitása lapot**.

1. Kövesse a **Symantec VIP** -oldalának utasításait.

## <a name="find-your-data"></a>Az adatai megkeresése

A sikeres kapcsolatok létrejötte után az adat Log Analytics a syslog alatt jelenik meg.

## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek a Log Analytics. 

## <a name="next-steps"></a>További lépések

Ebből a dokumentumból megtudhatta, hogyan csatlakoztatható a Symantec VIP-naplók az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.