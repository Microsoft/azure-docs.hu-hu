---
title: A Sophos XG-tűzfalak összekapcsolása az Azure Sentinel szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan csatlakoztatható a Sophos XG-tűzfalak az Azure Sentinel szolgáltatáshoz.
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
ms.openlocfilehash: 0b8b6247a735bceaf98029740bf9d4f7e233069d
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100097568"
---
# <a name="connect-your-sophos-xg-firewall-to-azure-sentinel"></a>A Sophos XG-tűzfal összekapcsolása az Azure Sentinel szolgáltatással

> [!IMPORTANT]
> A Sophos XG Firewall adatösszekötő az Azure Sentinel-ben jelenleg nyilvános előzetes verzióban érhető el.
> Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a cikk azt ismerteti, hogyan csatlakoztatható a [SOPHOS XG tűzfala](https://www.sophos.com/products/next-gen-firewall.aspx) készülék az Azure sentinelhez. A Sophos XG tűzfal adatösszekötője lehetővé teszi, hogy könnyedén összekapcsolja a Sophos XG tűzfal naplóit az Azure Sentinel használatával, megtekintheti az irányítópultokat, egyéni riasztásokat hozhat létre, és javíthatja a vizsgálatot. A Sophos XG tűzfal és az Azure Sentinel közötti integráció a syslog használatát teszi lehetővé.

> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="forward-sophos-xg-firewall-logs-to-the-syslog-agent"></a>A Sophos XG tűzfal naplófájljainak továbbítása a syslog-ügynökhöz  

A Sophos XG tűzfal konfigurálása a syslog-üzenetek Azure-munkaterületre történő továbbításához a syslog-ügynök használatával.

1. Az Azure Sentinel portálon kattintson az **adatösszekötők** elemre, és válassza a **Sophos XG tűzfal** -összekötő lehetőséget.

1. Válassza az **összekötő megnyitása lapot**.

1. Kövesse a **SOPHOS XG Firewall** oldalon megjelenő utasításokat.

## <a name="find-your-data"></a>Az adatai megkeresése

A sikeres kapcsolatok létrejötte után az adat Log Analytics a syslog alatt jelenik meg.

## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek a Log Analytics.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan csatlakoztatható a Sophos XG tűzfal az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.