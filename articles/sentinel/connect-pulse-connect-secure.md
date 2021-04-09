---
title: Impulzusos kapcsolat biztonságos adatkapcsolattal az Azure Sentinel szolgáltatással | Microsoft Docs
description: Megtudhatja, hogyan csatlakoztatható az impulzus a biztonságos adatkapcsolatokhoz az Azure Sentinel szolgáltatáshoz.
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
ms.openlocfilehash: 71e1c7787243713b29be9455fee966eff54f6d90
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100097653"
---
# <a name="connect-your-pulse-connect-secure-to-azure-sentinel"></a>A Pulse-kapcsolat biztonságos összekötése az Azure Sentinel használatával

> [!IMPORTANT]
> A biztonságos adatösszekötő az Azure Sentinelben jelenleg nyilvános előzetes verzióban érhető el.
> Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a cikk azt ismerteti, hogyan csatlakoztatható a [Pulse biztonságos](https://www.pulsesecure.net/products/pulse-connect-secure/) berendezése az Azure sentinelhez. Az Pulse Secure adatösszekötővel könnyedén csatlakoztathatja a Pulse biztonságos naplóit az Azure Sentinel használatával, megtekintheti az irányítópultokat, egyéni riasztásokat hozhat létre, és javíthatja a vizsgálatot. A Pulse-csatlakozás és az Azure Sentinel közötti integráció a syslog használatát teszi lehetővé.

> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="forward-pulse-connect-secure-logs-to-the-syslog-agent"></a>Forward Pulse biztonságos naplók összekötése a syslog-ügynökkel  

Az Impulzusos kapcsolat biztonságos beállítása a syslog-üzenetek Azure-munkaterületre való továbbításához a syslog-ügynök használatával.

1. Az Azure Sentinel-portálon kattintson az **adatösszekötők** elemre, és válassza a **Pulse csatlakozás biztonságos** összekötő lehetőséget.

1. Válassza az **összekötő megnyitása lapot**.

1. Kövesse az **Pulse Secure biztonságos** oldalának utasításait.

## <a name="find-your-data"></a>Az adatai megkeresése

A sikeres kapcsolatok létrejötte után az adat Log Analytics a syslog alatt jelenik meg.

## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek a Log Analytics. 

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan csatlakoztatható a Pulse-kapcsolat a biztonságos Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.