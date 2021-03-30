---
title: A csalóka Attack Management rendszerállapotának összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan kapcsolódhat a csalóka Attack Management rendszerinformációi az Azure Sentinelhez.
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
ms.openlocfilehash: ea7953be25473357f7ed572fa8b76076edc6f75a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87038117"
---
# <a name="connect-your-illusive-attack-management-system-to-azure-sentinel"></a>A csalóka Attack Management rendszerének összekötése az Azure Sentinel szolgáltatással

> [!IMPORTANT]
> A csalóka Attack Management System adatösszekötő az Azure Sentinelben jelenleg nyilvános előzetes verzióban érhető el.
> Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a cikk azt ismerteti, hogyan csatlakoztatható a [csalóka Attack Management rendszer](https://www.illusivenetworks.com/technology/platform/attack-detection-system) az Azure sentinelhez. A csalóka Attack Management rendszeradatok-összekötővel megoszthatja a csalóka támadási felületét az Azure Sentinel használatával, és megtekintheti ezeket az információkat olyan dedikált irányítópultokon, amelyek betekintést nyújtanak a szervezet támadási felületi kockázatára (ASM-irányítópult), és nyomon követhetik a szervezet hálózatán (ADS irányítópulton) a jogosulatlan oldalirányú mozgást.

> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="forward-illusive-attack-management-system-logs-to-the-syslog-agent"></a>Csalóka támadás-felügyeleti rendszernaplók továbbítása a syslog-ügynökhöz  

A támadás-felügyeleti rendszerek konfigurálása a syslog-üzenetek CEF formátumban való továbbításához az Azure-munkaterületen a syslog-ügynök használatával.

1. Jelentkezzen be a csalóka-konzolra, és navigáljon a Settings->Reporting elemre.

1. Keresse meg a syslog-kiszolgálókat.

1. Adja meg az alábbi adatokat:
   - Állomásnév: linuxos syslog-ügynök IP-címe vagy FQDN állomásneve
   - Port: 514
   - Protokoll: TCP
   - Naplózási üzenetek: naplózási üzenetek küldése a kiszolgálónak

1. A syslog-kiszolgáló hozzáadásához kattintson a Hozzáadás gombra.

1. Ha a csalóka támadási felügyeleti rendszer **naplóiban** szeretné használni a megfelelő sémát, keressen rá a **CommonSecurityLog** kifejezésre.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan kapcsolódhat a csalóka Attack Management rendszerhez az Azure Sentinel szolgáltatáshoz. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.
