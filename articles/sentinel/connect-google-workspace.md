---
title: A Google Workspace (G Suite) adatok összekapcsolhatók az Azure Sentinel szolgáltatással | Microsoft Docs
description: Megtudhatja, hogyan használhatja a Google Workspace (G Suite) adatösszekötőt a Google-munkaterület tevékenységi eseményeinek Azure Sentinelbe való betöltésére. Megtekintheti a Google Workspace-beli munkafüzeteket a munkafüzetek között, riasztásokat hozhat létre, és javíthatja a vizsgálatot.
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
ms.date: 02/28/2021
ms.author: yelevin
ms.openlocfilehash: 4ada570502d913283ba9ee4cc4c65b7bdd853935
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101745264"
---
# <a name="connect-your-google-workspace-to-azure-sentinel"></a>A Google-munkaterület összekötése az Azure Sentinel használatával

> [!IMPORTANT]
> A Google Workspace-összekötő jelenleg **előzetes** verzióban érhető el. Tekintse meg a kiegészítő [használati feltételeket a Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziókra vonatkozó további jogi feltételekhez, amelyek olyan Azure-szolgáltatásokra vonatkoznak, amelyek a bétaverzióban, az előzetes verzióban vagy más esetben még nem jelent meg általánosan elérhetővé.

A [Google Workspace (korábban G Suite)](https://workspace.google.com/) adatösszekötő lehetővé teszi a Google-munkaterület tevékenységi eseményeinek betöltését az Azure sentinelbe a REST APIon keresztül. Az összekötő biztosítja ezeket az [eseményeket](https://developers.google.com/admin-sdk/reports/reference/rest/v1/activities) a SoC-ben, így megvizsgálhatja a lehetséges biztonsági kockázatokat, elemezheti csapata együttműködését, beállíthatja a konfigurációs problémákat, nyomon követheti, hogy ki és mikor, hogyan elemezheti a rendszergazdai tevékenységeket, hogyan hozhat létre és oszthat meg tartalmakat, és hogyan tekinthet meg több eseményt a szervezetében.

> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="prerequisites"></a>Előfeltételek

A Google Workspace-adatok összegyűjtéséhez a következő engedélyekkel és hitelesítő adatokkal kell rendelkeznie:

- Olvasási és írási engedélyek az Azure Sentinel-munkaterületen.

- Olvasási engedélyek a munkaterület megosztott kulcsaihoz. [További információ a munkaterület kulcsairól](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

- Függvényalkalmazás létrehozásához olvasási és írási engedélyek a Azure Functionshoz. [További információ a Azure Functionsról](../azure-functions/index.yml).

- A REST API **GooglePickleString** hitelesítő adatai szükségesek. [További információ a Google Rest APIról](https://developers.google.com/admin-sdk/reports/v1/reference/activities). [Útmutató a hitelesítő adatok beszerzéséhez](https://developers.google.com/admin-sdk/reports/v1/quickstart/python).

## <a name="configure-and-connect-google-workspace"></a>A Google Workspace konfigurálása és összekapcsolása

A Google Workspace Azure-függvényalkalmazás használatával integrálhat és exportálhat naplókat közvetlenül az Azure Sentinelbe.

> [!NOTE]
> Ez az összekötő Azure Functions használatával csatlakozik a Google Reports API-hoz a tevékenység eseményeinek Azure Sentinelbe való lekéréséhez. Ennek eredményeként további adatfeldolgozási költségek is megjelenhetnek. A részletekért olvassa el a [Azure functions díjszabását](https://azure.microsoft.com/pricing/details/functions/) ismertető oldalt.

1. Az Azure Sentinel portálon kattintson az **adatösszekötők** elemre. 

1. Válassza a **Google Workspace (G Suite) (előzetes verzió)** lehetőséget az összekötők katalógusból, és válassza az **összekötő lap megnyitása** lehetőséget.

1. Kövesse az összekötő oldal **konfiguráció** szakaszában leírt lépéseket.

## <a name="validate-connectivity-and-find-your-data"></a>Kapcsolat ellenőrzése és az adataik megkeresése

Akár 20 percet is igénybe vehet, amíg meg nem jelenik a betöltött adatmennyiség az Azure Sentinelben.

A sikeres kapcsolatok létrejötte után az adat a **naplókban** jelenik meg az **egyéni naplók** szakaszban, az alábbi táblázatokban:
- `GWorkspace_ReportsAPI_admin_CL`
- `GWorkspace_ReportsAPI_calendar_CL`
- `GWorkspace_ReportsAPI_drive_CL`
- `GWorkspace_ReportsAPI_login_CL`
- `GWorkspace_ReportsAPI_mobile_CL`
- `GWorkspace_ReportsAPI_token_CL`
- `GWorkspace_ReportsAPI_user_accounts_CL`

Ez az adatösszekötő egy Kusto függvény alapján egy elemzőtől függ, hogy a várt módon működjön-e. A **GWorkspaceActivityReports** Kusto functions alias létrehozásához [kövesse az alábbi lépéseket](https://aka.ms/sentinel-GWorkspaceReportsAPI-parser) .

Néhány hasznos minta lekérdezéshez tekintse meg az összekötő lap **következő lépések** lapját.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan csatlakoztatható a Google Workspace az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan [érheti el az adatait és a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.
