---
title: Infoblox hálózati azonosító operációs rendszer (NIOS) adatainak összekötése az Azure Sentinel-be | Microsoft Docs
description: Ismerje meg, hogyan csatlakoztathatók a Infoblox Network Identity operációs rendszer (NIOS) adatai az Azure Sentinel szolgáltatáshoz.
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
ms.openlocfilehash: 28abb9f09e3bca2522b959c6a9b890de5320b17a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98567437"
---
# <a name="connect-your-infoblox-nios-to-azure-sentinel"></a>A Infoblox-NIOS összekötése az Azure Sentinel használatával

> [!IMPORTANT]
> Az Azure Sentinel Infoblox NIOS-adatösszekötője jelenleg nyilvános előzetes verzióban érhető el.
> Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a cikk azt ismerteti, hogyan csatlakoztatható a [Infoblox Network Identity Operating System (NIOS) készülék](https://www.infoblox.com/glossary/network-identity-operating-system-nios/) az Azure sentinelhez. A Infoblox NIOS-adatösszekötővel könnyedén csatlakoztathatja a Infoblox-naplókat az Azure Sentinel segítségével, megtekintheti az irányítópultokat, egyéni riasztásokat hozhat létre, és javíthatja a vizsgálatot. A Infoblox NIOS és az Azure Sentinel közötti integráció a syslog használatát teszi lehetővé.

> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="forward-infoblox-logs-to-the-syslog-agent"></a>Infoblox-naplók továbbítása a syslog-ügynökhöz  

Konfigurálja a Infoblox, hogy a syslog-ügynökön keresztül továbbítsa a syslog-üzeneteket az Azure Sentinel-munkaterületre.

1. Az Azure Sentinel-portálon kattintson az **adatösszekötők** elemre, és válassza a **Infoblox NIOS** -összekötő lehetőséget.

1. Válassza az **összekötő megnyitása lapot**.

1. Kövesse a **INFOBLOX NIOS** oldalon található utasításokat.

## <a name="find-your-data"></a>Az adatai megkeresése

A sikeres kapcsolatok létrejötte után az adat Log Analytics a syslog alatt jelenik meg.

## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek a Log Analytics. 

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan csatlakoztatható a Infoblox NIOS az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.