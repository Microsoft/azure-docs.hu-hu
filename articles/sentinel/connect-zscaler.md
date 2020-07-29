---
title: Zscaler-adatbázis összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan csatlakozhat a Zscaler-adatbázisokhoz az Azure Sentinel szolgáltatáshoz.
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
ms.openlocfilehash: cc784afe5db64ccc4aad13fae7a2fa748e4befa3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "77587991"
---
# <a name="connect-zscaler-internet-access-to-azure-sentinel"></a>Az Zscaler internet-hozzáférésének összekötése az Azure Sentinel használatával

> [!IMPORTANT]
> Az Azure Sentinel Zscaler-adatösszekötője jelenleg nyilvános előzetes verzióban érhető el.
> Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a cikk azt ismerteti, hogyan csatlakoztatható a Zscaler Internet-hozzáférési berendezés az Azure Sentinelhez. A Zscaler adatösszekötővel egyszerűen csatlakoztathatja a Zscaler internet-hozzáférés (ZIA) naplóit az Azure Sentinel használatával, megtekintheti az irányítópultokat, egyéni riasztásokat hozhat létre, és javíthatja a vizsgálatot. A Zscaler használata az Azure Sentinelben részletesebb információkat biztosít a szervezet internetes használatáról, és fokozza a biztonsági üzemeltetési képességeit. 


## <a name="configure-your-zscaler-to-send-cef-messages"></a>A Zscaler konfigurálása CEF üzenetek küldésére

1. A Zscaler készüléken ezeket az értékeket úgy kell beállítania, hogy a berendezés a szükséges formátumú naplókat a szükséges formátumban küldje el az Azure Sentinel syslog-ügynöknek a Log Analytics ügynök alapján. Ezeket a paramétereket módosíthatja a berendezésben, ha az Azure Sentinel-ügynök syslog démonán is módosítja őket.
    - Protokoll = TCP
    - Port = 514
    - Format = CEF
    - IP-cím – ügyeljen arra, hogy a CEF üzeneteket az erre a célra kijelölt virtuális gép IP-címére küldje el.
 További információ: a [Zscaler és az Azure Sentinel üzembe helyezési útmutatója](https://aka.ms/ZscalerCEFInstructions).
 
   > [!NOTE]
   > Ez a megoldás a syslog RFC 3164 vagy az RFC 5424 szolgáltatást támogatja.


1. A CEF-események Log Analytics vonatkozó sémájának használatához keresse meg a következőt: `CommonSecurityLog` .
1. Folytassa a [3. lépéssel: a kapcsolat ellenőrzése](connect-cef-verify.md).


## <a name="next-steps"></a>További lépések
Ebből a dokumentumból megtudhatta, hogyan csatlakoztatható a Zscaler internet-hozzáférés az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.


