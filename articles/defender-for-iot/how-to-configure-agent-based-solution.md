---
title: Az Azure Defender konfigurálása IoT-ügynökön alapuló megoldáshoz
description: Ismerje meg, hogyan konfigurálhatja az adatgyűjtést az Azure Defender IoT-ügynök alapú megoldásához
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/21/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: e32b9c690db70baeda7dec2f9f1270f82c6881bd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101733295"
---
# <a name="configure-azure-defender-for-iot-agent-based-solution"></a>Az Azure Defender konfigurálása IoT-ügynökön alapuló megoldáshoz  

Ez a cikk bemutatja, hogyan konfigurálhatja az adatgyűjtést az Azure Defender IoT-ügynökön alapuló megoldásához.

## <a name="configure-data-collection"></a>Adatgyűjtés konfigurálása

Az adatgyűjtési szolgáltatás konfigurálása az Azure Defender IoT-ügynökön alapuló megoldásához: 

1. Navigáljon a Azure Portal, és válassza ki azt a IoT Hub, amelyhez a IoT Defender csatlakozik 

1. A  **Biztonság**   menüben válassza a **Beállítások** lehetőséget. 

1. Válassza **az adatgyűjtés** lehetőséget. 

    :::image type="content" source="media/how-to-configure-agent-based-solution/data-collection.png" alt-text="Válassza az adatgyűjtés lehetőséget a biztonság menüben.":::

## <a name="geolocation-and-ip-address-handling"></a>Térinformatikai és IP-címek kezelése 

A IoT-megoldás biztonságossá tételéhez a bejövő és a kimenő kapcsolatok IP-címeit a IoT-eszközök, IoT Edge és IoT Hub (k) esetében a rendszer alapértelmezés szerint összegyűjti és tárolja. Ezek az információk elengedhetetlenek, és a gyanús IP-címektől származó rendellenes kapcsolatok észlelésére szolgálnak. Ha például olyan kísérletek történnek, amelyek megpróbálnak kapcsolatot létesíteni egy ismert botnet IP-címe forrásával, vagy a földrajzi helytől kívüli IP-címről. A Defender for IoT szolgáltatás lehetővé teszi, hogy bármikor engedélyezze és tiltsa le az IP-címekre vonatkozó adatgyűjtést. 

Az IP-címek adatgyűjtésének engedélyezése vagy letiltása: 

1. Nyissa meg a IoT Hub, majd ****   a **Biztonság** menüben válassza a beállítások lehetőséget   . 

1. Válassza ki az **adatgyűjtés**   képernyőt, és módosítsa a földrajzi hely és az IP-cím kezelésére vonatkozó beállításokat az igényeinek megfelelően. 

## <a name="log-analytics-creation"></a>Log Analytics létrehozása 

A IoT Defender a Log Analytics munkaterületen tárolja a biztonsági riasztásokat, a javaslatokat és a nyers biztonsági adatait. A IoT Hub Log Analytics betöltése alapértelmezés szerint **ki van kapcsolva** a Defender for IoT megoldásban. Lehetőség van arra, hogy a Defendert csatolja a IoT egy log Analytics-munkaterülethez, és ott tárolja a biztonsági adatbázisokat is. 

Az Log Analytics-munkaterületen a Defender for IoT két típusú információt tárol:
 
- Biztonsági riasztások.

- Javaslatok. 

Dönthet úgy is, hogy hozzáad egy további adattípusú tárolót a következőhöz: `raw events` . 

> [!Note] 
>  `raw events`   A log Analytics tárolása további tárolási költségeket is magában foglal. 

A Log Analytics a Micro agenttel való együttműködésének engedélyezése: 

1. Navigáljon a **munkaterület konfigurációs**  >  **adatgyűjtéséhez**, és kapcsolja be  **** a váltás a következőre:. 

1. Hozzon létre egy új Log Analytics munkaterületet, vagy csatolja egy meglévőt. 

1. Ellenőrizze, hogy be van-e jelölve a **nyers biztonsági beállításokhoz való hozzáférés**   lehetőség.  

    :::image type="content" source="media/how-to-configure-agent-based-solution/data-settings.png" alt-text="Győződjön meg arról, hogy ki van választva a nyers biztonsági információhoz való hozzáférés.":::

1. Válassza a **Mentés** lehetőséget.

Minden hónapban az Azure Log Analytics Service-szolgáltatásba betöltött első 5 gigabájtos adat ingyenes. Az Azure Log Analytics-munkaterületre betöltött minden GB-nyi adat díjmentesen megmarad az első 31 napban. A díjszabással kapcsolatos további információkért lásd: [log Analytics díjszabása](https://azure.microsoft.com/pricing/details/monitor/). 

Log Analytics munkaterület-konfigurációjának módosítása: 

1. A IoT Hub a **Biztonság** menüben válassza a  **Beállítások** lehetőséget. 

1. Válassza ki az **adatgyűjtési**   képernyőt, és az igényeinek megfelelően módosítsa log Analytics beállítások munkaterület-konfigurációját. 

A riasztások elérése a Log Analytics munkaterületen a konfiguráció után:

1. Válasszon ki egy riasztást a Defender for IoT.

1. Válassza **a riasztások vizsgálata log Analytics munkaterületen** lehetőséget.

A riasztások elérése a Log Analytics munkaterületen a konfiguráció után:

1. Válasszon egy javaslatot a Defender for IoT.

1. Válassza **a javaslatok vizsgálata log Analytics munkaterületen** lehetőséget. 
 
További információ a Log Analytics adatainak lekérdezéséről: Ismerkedés [a lekérdezésekkel a log Analyticsban](../azure-monitor/logs/get-started-queries.md). 

## <a name="turn-off-defender-for-iot"></a>A Defender kikapcsolása a IoT 

A Defender IoT szolgáltatáshoz való be-vagy kikapcsolása egy adott IoT Hubon: 

1. A IoT Hub a **Biztonság** menüben válassza a  **Beállítások** lehetőséget.

1. Válassza ki az **adatgyűjtési**   képernyőt, és az igényeinek megfelelően módosítsa log Analytics beállítások munkaterület-konfigurációját.

## <a name="next-steps"></a>Következő lépések 

Folytassa a következő cikkel a [megoldás konfigurálásához](quickstart-configure-your-solution.md).