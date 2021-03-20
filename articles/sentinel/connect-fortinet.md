---
title: Fortinet-adatbázis összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: A Fortinet készülék az Azure Sentinelhez való összekapcsolásával megtekintheti az irányítópultokat, létrehozhat egyéni riasztásokat, és javíthatja a vizsgálatot.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: add92907-0d7c-42b8-a773-f570f2d705ff
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 60be22f439547d006f54e489833b63171e617e3e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92913993"
---
# <a name="connect-fortinet-to-azure-sentinel"></a>A Fortinet és az Azure Sentinel összekötése



Ez a cikk azt ismerteti, hogyan csatlakoztatható a Fortinet-berendezés az Azure Sentinelhez. A Fortinet adatösszekötővel egyszerűen csatlakoztathatja a Fortinet-naplókat az Azure Sentinel segítségével, megtekintheti az irányítópultokat, egyéni riasztásokat hozhat létre, és javíthatja a vizsgálatot. A Fortinet használata az Azure Sentinelben részletesebb információkat biztosít a szervezet internetes használatáról, és fokozza a biztonsági üzemeltetési képességeit. 


 
## <a name="forward-fortinet-logs-to-the-syslog-agent"></a>Fortinet-naplók továbbítása a syslog-ügynökhöz

Konfigurálja a Fortinet a syslog-üzenetek CEF formátumban való továbbításához az Azure-munkaterületen a syslog-ügynök használatával.

1. Nyissa meg a CLI-t a Fortinet készüléken, és futtassa a következő parancsokat:

    ```console
    config log syslogd setting
    set status enable
    set format cef
    set port 514
    set server <ip_address_of_Receiver>
    end
    ```

    - Cserélje le a kiszolgáló **IP-címét** az ügynök IP-címére.
    - Állítsa a **syslog portot** **514** -re vagy az ügynökön beállított portra.
    - Ha a CEF formátumot szeretné engedélyezni a korai FortiOS-verziókban, előfordulhat, hogy futtatnia kell a **CSV-letiltási** parancsot.
 
   > [!NOTE] 
   > További információért keresse fel a [Fortinet dokumentumtárat](https://aka.ms/asi-syslog-fortinet-fortinetdocumentlibrary). Válassza ki a verziót, és használja a **kézikönyv** és a **napló üzenetének hivatkozását**.

1. Ha a megfelelő sémát szeretné használni Azure Monitor Log Analytics a Fortinet-eseményekhez, keresse meg a következőt: `CommonSecurityLog` .

1. Folytassa a [3. lépéssel: a kapcsolat ellenőrzése](connect-cef-verify.md).


## <a name="next-steps"></a>Következő lépések
Ebben a cikkben megtanulta, hogyan csatlakoztathatók a Fortinet-készülékek az Azure Sentinel szolgáltatáshoz. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.


