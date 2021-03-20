---
title: Az AI Vectra összekapcsolásával észlelhetők az Azure Sentinel | Microsoft Docs
description: Ismerje meg, hogyan csatlakoztathatók az AI Vectra az Azure Sentinel szolgáltatáshoz.
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
ms.date: 06/21/2020
ms.author: yelevin
ms.openlocfilehash: c57f4a49ac89f1347fc88f8bacddce3abab7e44e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87038222"
---
# <a name="connect-ai-vectra-detect-to-azure-sentinel"></a>AI Vectra-észlelés összekötése az Azure Sentinel használatával

> [!IMPORTANT]
> Az AI Vectra észlelése adatösszekötő az Azure Sentinel szolgáltatásban jelenleg nyilvános előzetes verzióban érhető el.
> Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a cikk azt ismerteti, hogyan csatlakoztatható a [AI Vectra-észlelési](https://www.vectra.ai/product/cognito-detect) berendezés az Azure sentinelhez. A mesterséges intelligenciát érzékelő adatösszekötő lehetővé teszi, hogy a mesterséges intelligenciát az Azure Sentinelbe könnyedén felismerje, így a munkafüzetekbe is megtekintheti, hogy egyéni riasztásokat hozzon létre, és a vizsgálat javítása érdekében beépítse azt.

> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="configure-your-ai-vectra-detect-appliance-to-send-cef-messages"></a>AI Vectra-észlelési berendezés konfigurálása CEF-üzenetek küldéséhez  

Állítsa be a AI Vectra észlelését, hogy továbbítsa a CEF-formázott syslog-üzeneteket a Log Analytics munkaterületre az [1. lépés: a napló-továbbító üzembe helyezése című témakörben](connect-cef-agent.md)beállított syslog-továbbítón keresztül.

1. A Vectra felületen navigáljon a beállítások > értesítések elemre, és válassza a syslog-konfiguráció szerkesztése lehetőséget. A kapcsolódás beállításához kövesse az alábbi utasításokat:

    - Új cél hozzáadása (a [napló-továbbító](connect-cef-agent.md)állomásneve)
    - A port beállítása **514** -ként
    - A protokoll beállítása **UDP** -ként
    - A **CEF** formátumának beállítása
    - Naplózási típusok beállítása (az összes elérhető naplózási típus kijelölése)
    - Kattintson a **Mentés** gombra.

2. A **teszt** gombra kattintva kényszerítheti a tesztelési események küldését a napló továbbítójának.

3. Ha a Log Analytics vonatkozó sémát szeretné használni a AI Vectra-észlelési eseményekhez, keresse meg a **CommonSecurityLog**.

4. Folytassa a [3. lépéssel: a kapcsolat ellenőrzése](connect-cef-verify.md).

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan csatlakoztathatók az AI Vectra-eszközök az Azure Sentinelhez. Az adatösszekötőhöz beépített képességek teljes kihasználásához kattintson a **következő lépések** lapra az adatösszekötő lapon. Itt talál néhány kész minta lekérdezést, hogy megismerkedjen a hasznos információk megtalálásával.

Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.
