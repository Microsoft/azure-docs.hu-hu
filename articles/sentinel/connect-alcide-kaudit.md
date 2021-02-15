---
title: Alcide-kAudit-adatbázis összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan csatlakoztathatók a Alcide kAudit-adatkészletek az Azure Sentinelhez.
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
ms.openlocfilehash: 6d1314b79f09f1bed2de5d6964b0a6224b5962ca
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100096871"
---
# <a name="connect-your-alcide-kaudit-to-azure-sentinel"></a>A Alcide-kAudit összekötése az Azure Sentinel használatával

> [!IMPORTANT]
> Az Azure Sentinel Alcide kAudit-adatösszekötője jelenleg nyilvános előzetes verzióban érhető el.
> Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A [Alcide kAudit](https://www.alcide.io/kaudit-K8s-forensics/) segítségével azonosíthatja a rendellenes Kubernetes viselkedéseket, és az észlelési idő csökkentése mellett a Kubernetes és az incidensekre koncentrálhat. Ez a cikk azt ismerteti, hogyan csatlakoztatható a Alcide kAudit-megoldás az Azure Sentinelhez. A Alcide kAudit adatösszekötője lehetővé teszi a kAudit-naplózási adatai egyszerű üzembe helyezését az Azure Sentinelben, így megtekintheti a munkafüzetekbe, és egyéni riasztásokat hozhat létre, és a vizsgálat javítására is felhasználhatja azt. A Alcide kAudit és az Azure Sentinel közötti integráció a REST API használatát teszi lehetővé.

> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="prerequisites"></a>Előfeltételek

- Olvasási és írási engedélyekkel kell rendelkeznie az Azure Sentinel munkaterületen.

- Olvasási jogosultsággal kell rendelkeznie a munkaterület megosztott kulcsaihoz.

## <a name="configure-and-connect-alcide-kaudit"></a>Alcide-kAudit konfigurálása és összekapcsolása

A Alcide kAudit közvetlenül az Azure Sentinelbe is exportálhatja a naplókat.

1. Az Azure Sentinel-portálon kattintson a navigációs menü **adatösszekötők** elemére.

1. Válassza a **Alcide kAudit** elemet a katalógusból, majd kattintson az **összekötő megnyitása lapra** .

1. Kövesse a [Alcide KAudit telepítési útmutatójának](https://get.alcide.io/hubfs/Azure%20Sentinel%20Integration%20with%20kAudit.pdf)lépésenkénti útmutatóját.

1. Amikor a rendszer a munkaterület-azonosítót és az elsődleges kulcsot kéri, a Alcide kAudit-adatösszekötő lapról másolhatja őket.

    :::image type="content" source="media/connect-alcide-kaudit/alcide-workspace-id-primary-key.png" alt-text="Munkaterület-azonosító és elsődleges kulcs":::

## <a name="find-your-data"></a>Az adatai megkeresése

A sikeres kapcsolatok létrejötte után az adat a **naplókban** a következő adattípusok alatt jelenik meg a **CustomLogs**:

- **alcide_kaudit_detections_1_CL** – Alcide kaudit észlelések 
- **alcide_kaudit_activity_1_CL** – Alcide kaudit
- **alcide_kaudit_selections_count_1_CL** – Alcide kaudit-tevékenységek száma
- **alcide_kaudit_selections_details_1_CL** – Alcide kaudit-tevékenység részletei

Ha a Alcide kAudit tartozó naplókban szeretné használni a megfelelő sémát, keresse meg a fent említett adattípusokat.

Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek a Log Analytics.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan csatlakoztatható a Alcide kAudit az Azure Sentinelhez. Az adatösszekötőhöz beépített képességek teljes kihasználásához kattintson a **következő lépések** lapra az adatösszekötő lapon. Itt talál néhány kész minta lekérdezést, hogy megismerkedjen a hasznos információk megtalálásával.

Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.
