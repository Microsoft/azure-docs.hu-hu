---
title: Symantec ProxySG-adatkapcsolatok összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan csatlakoztathatók a Symantec ProxySG-adatszolgáltatások az Azure Sentinelhez.
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
ms.openlocfilehash: 902475ae3e60761fb30620c5ba2fa8cbd905c916
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100099183"
---
# <a name="connect-your-symantec-proxysg-to-azure-sentinel"></a>A Symantec ProxySG és az Azure Sentinel összekötése

> [!IMPORTANT]
> A Symantec ProxySG-adatösszekötő az Azure Sentinel szolgáltatásban jelenleg nyilvános előzetes verzióban érhető el.
> Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a cikk azt ismerteti, hogyan csatlakoztatható a [Symantec ProxySG](https://www.broadcom.com/products/cyber-security/network/gateway/proxy-sg-and-advanced-secure-gateway) -berendezés az Azure sentinelhez. A Symantec ProxySG-adatösszekötővel könnyedén csatlakoztathatja a Symantec ProxySG-naplókat az Azure Sentinel használatával, megtekintheti az irányítópultokat, egyéni riasztásokat hozhat létre, és javíthatja a vizsgálatot. A Symantec ProxySG és az Azure Sentinel közötti integráció a syslog használatát teszi lehetővé.

> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="forward-symantec-proxysg-logs-to-the-syslog-agent"></a>Symantec ProxySG-naplók továbbítása a syslog-ügynökhöz  

Konfigurálja a Symantec ProxySG, hogy a syslog-ügynökön keresztül továbbítsa a syslog-üzeneteket az Azure-munkaterületre.

1. Az Azure Sentinel portálon kattintson az **adatösszekötők** elemre, és válassza a **Symantec ProxySG** -összekötő lehetőséget.

1. Válassza az **összekötő megnyitása lapot**.

1. Kövesse a **Symantec ProxySG** oldalon megjelenő utasításokat.

## <a name="find-your-data"></a>Az adatai megkeresése

A sikeres kapcsolatok létrejötte után az adat Log Analytics a syslog alatt jelenik meg.

## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek a Log Analytics. 

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtanulta, hogyan csatlakoztatható a Symantec ProxySG az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.