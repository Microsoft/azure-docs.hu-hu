---
title: A Palo Alto Networks-adatkapcsolatok és az Azure Sentinel összekötése | Microsoft Docs
description: Megtudhatja, hogyan használhatja a Palo Alto Networks-adatösszekötőt a Palo Alto Networks-naplók Azure Sentinel használatával történő egyszerű összekapcsolásához.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: a4b21d67-1a72-40ec-bfce-d79a8707b4e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 245db436fc3216fe5c8c8f51c50c0ac03190f9eb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85564565"
---
# <a name="connect-palo-alto-networks-to-azure-sentinel"></a>A Palo Alto-hálózatok összekapcsolhatók az Azure Sentinel-vel



Ez a cikk azt ismerteti, hogyan csatlakoztatható a Palo Alto Networks-berendezés az Azure Sentinelhez. A Palo Alto Networks adatösszekötő segítségével könnyedén csatlakoztathatja a Palo Alto hálózati naplókat az Azure Sentinel használatával, megtekintheti az irányítópultokat, egyéni riasztásokat hozhat létre, és javíthatja a vizsgálatot. A Palo Alto Networks használata az Azure Sentinelben további információkat nyújt a szervezet internetes használatáról, és fokozza a biztonsági üzemeltetési képességeit. 


## <a name="forward-palo-alto-networks-logs-to-the-syslog-agent"></a>A Palo Alto hálózati naplók továbbítása a syslog-ügynökbe

A Palo Alto-hálózatok konfigurálása a syslog-üzenetek CEF formátumban való továbbításához az Azure-munkaterületen a syslog-ügynök használatával:
1.  Nyissa meg a [Common Event Format (CEF) konfigurációs útmutatóit](https://docs.paloaltonetworks.com/resources/cef) , és töltse le a készülék típusának PDF-fájlját. A CEF események gyűjtéséhez kövesse az útmutató utasításait a Palo Alto Networks-berendezés beállításához. 

1.  Lépjen a [syslog-figyelés konfigurálása](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/configure-syslog-monitoring) elemre, és kövesse a 2. és a 3. lépést a Palo Alto Networks-készülékről az Azure sentinelre történő CEF-továbbítás konfigurálásához.

    1. Győződjön meg arról, hogy a **syslog-kiszolgáló formátumát** a **BSD** értékre állítja be.

       > [!NOTE]
       > Előfordulhat, hogy a PDF-fájl másolási/beillesztési műveletei megváltoztatják a szöveget, és véletlenszerű karaktereket szúrnak be. Ennek elkerüléséhez másolja a szöveget egy Szerkesztőbe, és távolítson el minden olyan karaktert, amely megtöri a napló formátumát a beillesztés előtt, ahogy az ebben a példában látható.
 
        ![CEF szöveges másolási probléma](./media/connect-cef/paloalto-text-prob1.png)

1. Ha a Log Analytics vonatkozó sémát szeretné használni a Palo Alto Networks-eseményekhez, keresse meg a **CommonSecurityLog**.

1. Folytassa a [3. lépéssel: a kapcsolat ellenőrzése](connect-cef-verify.md).




## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtudhatta, hogyan csatlakoztatható a Palo Alto hálózati berendezések az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.


