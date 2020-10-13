---
title: Az Orka biztonsági riasztások összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan csatlakoztathatók az Orka biztonsági riasztások az Azure Sentinelhez, hogyan jeleníthetők meg az irányítópultok, hogyan hozhatók létre egyéni riasztások, és hogyan javítható a vizsgálat
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
ms.openlocfilehash: 735f4b447d7258a9b444f3b75a6537dec20b1307
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87076276"
---
# <a name="connect-your-orca-security-alerts-to-azure-sentinel"></a>Az Orka biztonsági riasztások összekötése az Azure Sentinel szolgáltatással 

> [!IMPORTANT]
> Az Orka Security riasztások összekötő az Azure Sentinel szolgáltatásban jelenleg nyilvános előzetes verzióban érhető el.
> Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az Orka biztonsági riasztások összekötővel egyszerűen hozhatja létre az [Orka riasztások](https://orca.security/) biztonsági megoldásának riasztásait az Azure Sentinel szolgáltatásba, így megtekintheti őket a munkafüzetekben, felhasználhatja őket egyéni riasztások létrehozására, és a vizsgálat javítására is felépítheti azokat. Az Orka biztonsági riasztások és az Azure Sentinel közötti integráció a REST API használatát teszi lehetővé.

> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="configure-and-connect-orca-security-alerts"></a>Az Orka biztonsági riasztások konfigurálása és összekapcsolása

Az Orka biztonsági riasztásai közvetlenül az Azure Sentinelbe integrálják és exportálják a naplókat.

1. Az Azure Sentinel-portálon kattintson az **adatösszekötők** elemre, és válassza az **Orka biztonsági riasztások** lehetőséget, majd **nyissa meg az összekötő lapot**.

2. https://orcasecurity.zendesk.com/hc/en-us/articles/360043941992-Azure-Sentinel-integrationAz Orka platformon való integráció befejezéséhez tekintse meg a következőt:.

## <a name="find-your-data"></a>Az adatai megkeresése

A sikeres kapcsolatok létrejötte után az adat a **OrcaAlerts_CL** tábla **CustomLogs** területén log Analytics jelenik meg.
Ha a megfelelő sémát szeretné használni a Log Analyticsban az Orka-riasztásokhoz, keresse meg a következőt: `OrcaAlerts_CL` .

## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése
Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek a Log Analytics. 


## <a name="next-steps"></a>További lépések
Ebből a dokumentumból megtudhatta, hogyan csatlakoztathatók az Orka biztonsági riasztásai az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.

