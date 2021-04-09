---
title: Az igény szerinti e-mailek biztonsági Proofpoint összekapcsolása az Azure Sentinel szolgáltatásban | Microsoft Docs
description: Megtudhatja, hogyan használhatja a Proofpoint on demand E-mail biztonsági adatösszekötőt a POD E-mail biztonsági naplók Azure Sentinelbe való lekéréséhez. Megtekintheti az e-mailek biztonsági szolgáltatásait a munkafüzetekben, riasztásokat hozhat létre, és javíthatja a vizsgálatot.
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
ms.date: 01/17/2021
ms.author: yelevin
ms.openlocfilehash: 73a9d9c7ab321aebd615922e5d4395c0318e809c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100580449"
---
# <a name="connect-your-proofpoint-on-demand-email-security-pod-solution-to-azure-sentinel"></a>A Proofpoint on demand mail Security (POD) megoldásának összekapcsolása az Azure Sentinel szolgáltatással

> [!IMPORTANT]
> A Proofpoint on demand E-mail biztonsági összekötő jelenleg **előzetes** verzióban érhető el. Tekintse meg a kiegészítő [használati feltételeket a Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziókra vonatkozó további jogi feltételekhez, amelyek olyan Azure-szolgáltatásokra vonatkoznak, amelyek a bétaverzióban, az előzetes verzióban vagy más esetben még nem jelent meg általánosan elérhetővé.

Ez a cikk azt ismerteti, hogyan csatlakoztatható a Proofpoint on demand E-mail biztonsági berendezés az Azure Sentinel szolgáltatáshoz. A POD adatösszekötővel könnyedén csatlakoztathatja a POD-naplókat az Azure Sentinel használatával, így megtekintheti az adatait a munkafüzetekben, felhasználhatja egyéni riasztások létrehozásához és a vizsgálat javítására.  A Proofpoint on demand e-mail-biztonság és az Azure Sentinel a WebSocket API használatát teszi lehetővé.

> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="prerequisites"></a>Előfeltételek

- Olvasási és írási engedéllyel kell rendelkeznie az Azure Sentinel munkaterületen.

- Olvasási jogosultsággal kell rendelkeznie a munkaterület megosztott kulcsaihoz. [További információ a munkaterület kulcsairól](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

- Függvényalkalmazás létrehozásához olvasási és írási engedéllyel kell rendelkeznie a Azure Functionshoz. [További információ a Azure Functionsról](../azure-functions/index.yml).

- A következő WebSocket API hitelesítő adatokkal kell rendelkeznie: ProofpointClusterID, ProofpointToken. [További információ a WebSocket API-ról](https://proofpointcommunities.force.com/community/s/article/Proofpoint-on-Demand-Pod-Log-API).

## <a name="configure-and-connect-proofpoint-on-demand-email-security"></a>A Proofpoint konfigurálása és az igény szerinti levelezés biztonsága

Az igény szerinti Proofpoint-e-mailek biztonsága a naplókat közvetlenül az Azure Sentinelbe integrálhatja és exportálhatja.

1. Az Azure Sentinel navigációs menüjében válassza az **adatösszekötők** lehetőséget.

1. Az **adatösszekötők katalógusában** válassza az **Proofpoint on demand E-mail biztonság (előzetes verzió)** lehetőséget, majd **nyissa meg az összekötő lapot**.

1. Kövesse az összekötő oldal **konfiguráció** szakaszában leírt lépéseket.

## <a name="find-your-data"></a>Az adatai megkeresése

A sikeres kapcsolatok létrejötte után az adat a **naplókban**, az *egyéni naplók* területen jelenik meg az alábbi táblázatokban:
- `ProofpointPOD_message_CL`
- `ProofpointPOD_maillog_CL`

Néhány hasznos minta lekérdezéshez tekintse meg az összekötő lap **következő lépések** lapját.

## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

Akár 60 percet is igénybe vehet, amíg a naplók elkezdenek megjelenni Log Analyticsban.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan csatlakoztatható a Proofpoint on demand E-mail biztonság az Azure Sentinel szolgáltatáshoz. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.