---
title: Cisco-adatkapcsolatok az Azure Sentinel-be | Microsoft Docs
description: Ismerje meg, hogyan csatlakoztatható a Cisco ASA-berendezés az Azure Sentinelhez az irányítópultok megtekintéséhez, egyéni riasztások létrehozásához és a vizsgálat javításához.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 62029b5c-29d3-4336-8a22-a9db8214eb7e
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: e8a64dd3e47384ba2bf7579f8052177252634622
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85566034"
---
# <a name="connect-cisco-asa-to-azure-sentinel"></a>A Cisco ASA összekötése az Azure Sentinel-vel



Ez a cikk azt ismerteti, hogyan csatlakoztatható a Cisco ASA-berendezés az Azure Sentinelhez. A Cisco ASA adatösszekötővel könnyedén csatlakoztathatja a Cisco ASA-naplókat az Azure Sentinel használatával, megtekintheti az irányítópultokat, egyéni riasztásokat hozhat létre, és javíthatja a vizsgálatot. A Cisco ASA használata az Azure Sentinelben további információkat biztosít a szervezet internetes használatáról, és fokozza a biztonsági üzemeltetési képességeit. 



## <a name="forward-cisco-asa-logs-to-the-syslog-agent"></a>Cisco ASA-naplók továbbítása a syslog-ügynöknek

A Cisco ASA nem támogatja a CEF, ezért a rendszer syslog-ként küldi el a naplókat, és az Azure Sentinel-ügynök tudja, hogyan elemezheti őket úgy, mintha CEF naplókat. A Cisco ASA konfigurálása a syslog-üzenetek Azure-munkaterületre történő továbbításához a syslog-ügynök használatával:

1. Válassza a [syslog-üzenetek küldése egy külső syslog-kiszolgálónak lehetőséget](https://aka.ms/asi-syslog-cisco-forwarding), majd kövesse az utasításokat a kapcsolódás beállításához. Ha a rendszer kéri, használja ezeket a paramétereket:
    - Állítsa a **portot** 514-re vagy az ügynökben beállított portra.
    - **Syslog_ip** beállítása az ügynök IP-címére.

1. A Cisco-események Log Analytics vonatkozó sémájának használatához keresse a következőt: `CommonSecurityLog` .

1. Folytassa a [3. lépéssel: a kapcsolat ellenőrzése](connect-cef-verify.md).




## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtudhatta, hogyan kapcsolódhat a Cisco ASA-készülékekhez az Azure Sentinel szolgáltatáshoz. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.


