---
title: A Zimperium Mobile Threat Defense összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan csatlakoztatható a Zimperium Mobile Threat Defense az Azure Sentinelhez.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: 7cbf1c52af1d2902ae0726fc0dd98dbf12cecc44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100097449"
---
# <a name="connect-your-zimperium-mobile-threat-defense-to-azure-sentinel"></a>A Zimperium Mobile Threat Defense és az Azure Sentinel összekötése


> [!IMPORTANT]
> A Zimperium Mobile Threat Defense-adatösszekötő az Azure Sentinel szolgáltatásban jelenleg nyilvános előzetes verzióban érhető el.
> Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



A Zimperium Mobile Threat Defense-összekötő lehetővé teszi az Zimperium-fenyegetések naplójának és az Azure Sentinel összekapcsolását az irányítópultok megtekintésére, egyéni riasztások létrehozására és a vizsgálat javítására. Így jobban betekintést nyerhet a szervezete Mobile Threat-környezetbe, és fokozza a biztonsági üzemeltetési képességeket.

> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="configure-and-connect-zimperium-mobile-threat-defense"></a>A Zimperium Mobile Threat Defense konfigurálása és összekapcsolása

A Zimperium Mobile Threat Defense képes a naplók közvetlen integrálására és exportálására az **Azure sentinelbe**.

1. Az Azure Sentinel portálon kattintson az adatösszekötők lehetőségre, és válassza a **Zimperium Mobile Threat Defense** lehetőséget.
2. Válassza az **összekötő megnyitása lapot**.
3. Kövesse a **Zimperium Mobile Threat Defense** -összekötő oldalának utasításait, a következőképpen Összefoglalva.
 1. A zConsole kattintson a navigációs sávon a **kezelés** elemre.
 2. Kattintson az **integrációk** fülre.
 3. Kattintson a **veszélyforrások jelentése** gombra, majd az **integráció hozzáadása** gombra.
 4. Az integráció létrehozásához válassza ki **Microsoft Azure Sentinel** elemet az elérhető integrációk közül, és adja meg a munkaterület-azonosítót és az elsődleges kulcsot az Azure sentinelhez való csatlakozáshoz.
 5. Lehetőség van arra, hogy kiválasszon egy szűrési szintet a veszélyforrások számára az Azure Sentinel szolgáltatásba való leküldéshez is. 

4. További információkért tekintse meg a [Zimperium ügyfélszolgálati portálját](https://support.zimperium.com).


## <a name="find-your-data"></a>Az adatai megkeresése

A sikeres kapcsolatok létrejötte után az adat Log Analytics a CustomLogs ZimperiumThreatLog_CL és ZimperiumMitigationLog_CL alatt jelenik meg.

A Zimperium Mobile Threat Defense Log Analytics vonatkozó sémájának használatához keresse meg a ZimperiumThreatLog_CL és a ZimperiumMitigationLog_CL kifejezést.


## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek a Log Analytics.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtanulta, hogyan csatlakoztatható a Zimperium Mobile Threat Defense az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).

- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).

- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.

A Zimperium kapcsolatos további tudnivalókért tekintse meg a következőket:

- [Zimperium](https://zimperium.com)

- [Zimperium Mobile Security Blog](https://blog.zimperium.com)

- [Zimperium ügyfélszolgálati portál](https://support.zimperium.com)

