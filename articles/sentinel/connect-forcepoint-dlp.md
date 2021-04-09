---
title: A Forcepoint DLP összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan csatlakoztatható a Forcepoint DLP az Azure Sentinelhez.
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
ms.openlocfilehash: 62ed3915dcaf596d144a2f59817626cdf8ec47e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100092774"
---
# <a name="connect-your-forcepoint-dlp-to-azure-sentinel"></a>A Forcepoint DLP és az Azure Sentinel összekötése

> [!IMPORTANT]
> Az Azure Sentinel Forcepoint adatvesztés-megelőzési (DLP) adatösszekötője jelenleg nyilvános előzetes verzióban érhető el. Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



A Forcepoint DLP-összekötő lehetővé teszi a DLP-incidensek automatikus exportálását az Azure Sentinel szolgáltatásba. Használhatja a felhasználói tevékenységek és az adatvesztési incidensek láthatóságának megismerésére. Emellett lehetővé teszi az Azure-munkaterhelések és egyéb hírcsatornák adataival való korrelációt, valamint javítja a figyelési képességet az Azure Sentinelben lévő munkafüzetek használatával.

> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="configure-and-connect-forcepoint-dlp"></a>A Forcepoint DLP konfigurálása és összekapcsolása

Konfigurálja a Forcepoint DLP-t, hogy JSON formátumban továbbítsa az incidensek adatait az Azure-munkaterületre REST API a [DLP-integrációs útmutatóban](https://frcpnt.com/dlp-sentinel)ismertetett módon.


## <a name="find-your-data"></a>Az adatai megkeresése

Miután beállította a Forcepoint DLP-összekötőt, az CustomLogs **ForcepointDLPEvents_CL** alatt megjelenik az adat a log Analytics.


Ha a Forcepoint DLP Log Analytics vonatkozó sémáját szeretné használni, keresse meg a **ForcepointDLPEvents_CL**.


## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek a Log Analytics.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan csatlakoztatható a Forcepoint DLP az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.