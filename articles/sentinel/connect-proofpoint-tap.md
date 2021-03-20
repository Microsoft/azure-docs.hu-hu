---
title: Az Proofpoint Target Attack Protection (KOPPINTson) az Azure Sentinelbe való kapcsolódása | Microsoft Docs
description: Megtudhatja, hogyan csatlakoztathatók a Proofpoint-beli támadási védelem (TAP) az Azure Sentinel szolgáltatáshoz.
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
ms.openlocfilehash: 86018bafaa42eac01e5dccf8da1d290b64e2475c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100092978"
---
# <a name="connect-your-proofpoint-tap-to-azure-sentinel-with-azure-function"></a>A Proofpoint KOPPINTson az Azure Sentinelre az Azure-függvény használatával

> [!IMPORTANT]
> A Proofpoint TAP adatösszekötő az Azure Sentinel szolgáltatásban jelenleg nyilvános előzetes verzióban érhető el.
> Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A Proofpoint Targeted Attack Protection (KOPPINTÁS) összekötővel könnyedén csatlakoztathatja az összes [Proofpoint](https://www.proofpoint.com/us/products/advanced-threat-protection/targeted-attack-protection) a biztonsági megoldások naplóihoz az Azure Sentinel használatával, az irányítópultok megtekintéséhez, egyéni riasztások létrehozásához és a vizsgálat javításához. A Proofpoint TAP és az Azure Sentinel közötti integráció a Azure Functions használatával kéri le a naplózási adataikat a REST API használatával.

> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="configure-and-connect-proofpoint-tap"></a>Proofpoint-beállítások konfigurálása és összekapcsolásának megérintése

A Azure Functions az eseményeket és naplókat közvetlenül a Proofpoint-ből integrálhatja és lehívhatja, és az Azure Sentinelbe továbbíthatja őket.

1. Az Azure Sentinel Portalon kattintson az **adatösszekötők** elemre, és válassza a **Proofpoint koppintson** az összekötő lehetőséget.

1. Válassza az **összekötő megnyitása lapot**.

1. Kövesse a **PROOFPOINT koppintó** oldalának utasításait.

## <a name="find-your-data"></a>Az adatai megkeresése

A sikeres kapcsolatok létrejötte után az adat a **ProofpointTAPMessagesBlocked_CL**, a **ProofpointTAPMessagesDelivered_CL**, a **ProofpointTAPClicksPermitted_CL** és a **ProofpointTAPClicksBlocked_CL** táblákban log Analytics jelenik meg.

## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek a Log Analytics.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan kapcsolódhat a Proofpoint az Azure Sentinelhez az Azure Function Apps használatával. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.
