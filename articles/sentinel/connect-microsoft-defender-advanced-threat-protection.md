---
title: A Microsoft Defender és a végponti adatkapcsolat összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan kapcsolódhat a Microsoft Defender for Endpoint (korábban Microsoft Defender ATP) szolgáltatáshoz az Azure Sentinelhez.
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
ms.date: 09/16/2020
ms.author: yelevin
ms.openlocfilehash: 0db4e0fe0472c75f1eae392980ae697f53007244
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98623366"
---
# <a name="connect-alerts-from-microsoft-defender-for-endpoint-formerly-microsoft-defender-atp"></a>Riasztások összekötése a Microsoft Defender for Endpoint (korábban Microsoft Defender ATP) szolgáltatással

> [!IMPORTANT]
>
> - **A Microsoft Defender for Endpoint** korábban a **Microsoft Defender komplex veszélyforrások elleni védelmi** vagy **MDATP** néven ismert.
>
>     Előfordulhat, hogy a régi név még használatban van a termékben (beleértve az Azure Sentinel adatösszekötőjét) egy adott időszakban.

A [Microsoft Defender for Endpoint](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) Connector lehetővé teszi, hogy riasztásokat továbbítson a Microsoft Defender for végpontnak az Azure Sentinel szolgáltatásba. Ezzel a művelettel átfogóbban elemezheti a szervezet biztonsági eseményeit, és a hatékony és azonnali reagálás érdekében forgatókönyveket hozhat létre.

> [!NOTE]
>
> Ha az új nyers adatnaplókat a Microsoft Defenderből kívánja bevenni a végpont [speciális vadászatára](/windows/security/threat-protection/microsoft-defender-atp/advanced-hunting-overview), használja az új összekötőt Microsoft 365 defenderhez (korábbi nevén Microsoft Threat Protection, [lásd a dokumentációt](./connect-microsoft-365-defender.md)).

## <a name="prerequisites"></a>Előfeltételek

- Érvényes licenccel kell rendelkeznie a Microsoft Defender for Endpoint szolgáltatáshoz, a [Microsoft Defender beállítása végpont üzembe helyezése](/windows/security/threat-protection/microsoft-defender-atp/licensing)című témakörben leírtak szerint. 

- Az Azure Sentinel-bérlő globális rendszergazdájának vagy biztonsági rendszergazdájának kell lennie.

## <a name="connect-to-microsoft-defender-for-endpoint"></a>Kapcsolódás a Microsoft Defenderhez a végponthoz

Ha a Microsoft Defender for Endpoint üzembe helyezését és az adataik betöltését végzi, a riasztások könnyen továbbíthatók az Azure Sentinel szolgáltatásba.

1. Az Azure Sentinelben válassza az **adatösszekötők** lehetőséget, válassza a **Microsoft Defender for Endpoint** (a *Microsoft Defender komplex veszélyforrások elleni védelem*) elemet a katalógusból, és válassza az **összekötő lap megnyitása** lehetőséget.

1. Kattintson a **Csatlakozás** gombra. 

1. Ha le szeretné kérdezni a Microsoft Defender for Endpoint riasztásait a **naplókban**, írja be a **SecurityAlert** nevet a lekérdezési ablakba, és adjon hozzá egy szűrőt, amelyben a **szolgáltató neve** **MDATP**.

## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtudhatta, hogyan csatlakoztatható a Microsoft Defender a végponthoz az Azure Sentinel szolgáltatáshoz. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](./tutorial-detect-threats-built-in.md).