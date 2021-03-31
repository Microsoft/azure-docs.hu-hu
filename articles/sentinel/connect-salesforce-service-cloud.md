---
title: A Salesforce szolgáltatás Felhőbeli adatkapcsolatának összekötése az Azure Sentinel-vel | Microsoft Docs
description: Megtudhatja, hogyan használhatja a Salesforce Service Cloud adatösszekötőt a Salesforce-naplók Azure Sentinelbe való lekéréséhez. Megtekintheti a munkafüzetek Salesforce, riasztásokat hozhat létre, és javíthatja a vizsgálatot.
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
ms.openlocfilehash: 1efd91d92bac1bc1f39d82aaa0cc71daa0275f8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100570534"
---
# <a name="connect-your-salesforce-service-cloud-to-azure-sentinel"></a>A Salesforce Service-felhő összekötése az Azure Sentinel szolgáltatással

> [!IMPORTANT]
> A Salesforce szolgáltatás felhőalapú összekötője jelenleg **előzetes** verzióban érhető el. Tekintse meg a kiegészítő [használati feltételeket a Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziókra vonatkozó további jogi feltételekhez, amelyek olyan Azure-szolgáltatásokra vonatkoznak, amelyek a bétaverzióban, az előzetes verzióban vagy más esetben még nem jelent meg általánosan elérhetővé.

Ez a cikk azt ismerteti, hogyan csatlakoztatható a Salesforce Service Cloud-megoldás az Azure Sentinelhez. A Salesforce szolgáltatás felhő-adatösszekötője lehetővé teszi, hogy könnyedén összekapcsolódjon a Salesforce-adataival az Azure Sentinel segítségével, így megtekintheti a munkafüzetekben, felhasználhatja egyéni riasztások létrehozására, és a vizsgálat javítására is felépítheti azt. A Salesforce Service Cloud és az Azure Sentinel közötti integráció a REST API használatát teszi lehetővé.

> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="prerequisites"></a>Előfeltételek

- Olvasási és írási engedéllyel kell rendelkeznie az Azure Sentinel munkaterületen.

- Olvasási jogosultsággal kell rendelkeznie a munkaterület megosztott kulcsaihoz. [További információ a munkaterület kulcsairól](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

- Függvényalkalmazás létrehozásához olvasási és írási engedéllyel kell rendelkeznie a Azure Functionshoz. [További információ a Azure Functionsról](../azure-functions/index.yml).

- A következő Salesforce REST API hitelesítő adatokkal kell rendelkeznie: **SALESFORCE API username**, **Salesforce API Password**, **Salesforce biztonsági jogkivonat**, **Salesforce fogyasztói kulcs**, **Salesforce fogyasztói titok**. [További információ a Salesforce REST API](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/quickstart.htm).

## <a name="configure-and-connect-salesforce-service-cloud"></a>A Salesforce Service Cloud konfigurálása és összekapcsolása

A Salesforce Service Cloud képes a naplók közvetlen integrálására és exportálására az Azure Sentinel szolgáltatásba.

1. Az Azure Sentinel navigációs menüjében válassza az **adatösszekötők** lehetőséget.

1. Az **adatösszekötők katalógusában** válassza a **Salesforce Service Cloud (előzetes verzió)** lehetőséget, majd **nyissa meg az összekötő lapot**.

1. Kövesse az összekötő oldal **konfiguráció** szakaszában leírt lépéseket.

## <a name="find-your-data"></a>Az adatai megkeresése

A sikeres kapcsolatok létrejötte után az adat megjelenik a **naplókban** a **CustomLogs** szakaszban, a `SalesforceServiceCloud_CL` táblázatban.

Néhány hasznos minta lekérdezéshez tekintse meg az összekötő lap **következő lépések** lapját.

## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek a Log Analytics.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtanulta, hogyan csatlakoztatható a Salesforce Service Cloud az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.