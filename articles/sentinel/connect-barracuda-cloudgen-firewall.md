---
title: A Barracuda CloudGen tűzfal összekapcsolása az Azure Sentinel szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan csatlakoztatható a Barracuda CloudGen tűzfal az Azure Sentinelhez.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: dd0115fa34b10805570b6e9d8d09ae96fc169dfc
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/21/2021
ms.locfileid: "98621131"
---
# <a name="connect-barracuda-cloudgen-firewall"></a>A Barracuda CloudGen tűzfal csatlakoztatása

A Barracuda CloudGen Firewall (CGFW) összekötővel könnyedén csatlakoztathatja a Barracuda CGFW-naplóit az Azure Sentinel használatával, megtekintheti az irányítópultokat, egyéni riasztásokat hozhat létre, és javíthatja a vizsgálatot. Ez nagyobb betekintést nyújt a szervezet hálózatára, és javítja a biztonsági műveletek képességeit.

## <a name="prerequisites"></a>Előfeltételek

- Olvasási és írási engedélyek az Azure Sentinel-munkaterülethez.

- A Barracuda CloudGen tűzfalat úgy kell konfigurálni, hogy a syslog használatával exportálja a naplókat.

## <a name="connect-azure-sentinel-to-barracuda-cloudgen-firewall"></a>Az Azure Sentinel összekapcsolása a Barracuda CloudGen tűzfallal

1. A Azure Portal navigáljon az **Azure Sentinel**-  >  **adatösszekötők területére** , majd válassza a **Barracuda CloudGen tűzfal** -összekötőt.

2. Válassza az **összekötő megnyitása lapot**.

3. Kövesse a **Barracuda CloudGen Firewall** oldalon megjelenő utasításokat.


## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtanulta, hogyan csatlakoztatható a Barracuda CloudGen tűzfal az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.


