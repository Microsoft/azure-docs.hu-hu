---
title: Ismerje meg, hogyan integrálható a kiépítés a Azure Active Directory Azure Monitor naplóival.
description: Ismerje meg, hogyan integrálható a kiépítés a Azure Active Directory Azure Monitor naplóival.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 10/12/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: f656f55b0c74103053f110b8c66645d353db9fbf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102561568"
---
# <a name="understand-how-provisioning-integrates-with-azure-monitor-logs"></a>A kiépítés integrálása Azure Monitor naplókkal

A kiépítés integrálható Azure Monitor naplók és Log Analytics. Az Azure monitoring szolgáltatással olyan műveleteket hajthat végre, mint például a munkafüzetek létrehozása, más néven irányítópultok, a kiépítési naplók 30 + napig való tárolása, valamint egyéni lekérdezések és riasztások létrehozása. Ez a cikk azt ismerteti, hogyan integrálható a naplók a Azure Monitor naplókba. Ha többet szeretne megtudni arról, hogy a naplók hogyan működnek általánosságban, tekintse meg a következő témakört: [kiépítési naplók](../reports-monitoring/concept-provisioning-logs.md).

## <a name="enabling-provisioning-logs"></a>A kiépítési naplók engedélyezése

Már ismernie kell az Azure monitoringot és a Log Analytics. Ha nem, ugorjon át a megismeréséhez, és térjen vissza az alkalmazás-kiépítési naplók megismeréséhez. További információ az Azure monitoring szolgáltatásról: [Azure monitor Overview (áttekintés](../../azure-monitor/overview.md)). További információ a Azure Monitor naplókról és Log Analyticsekről: [a Azure monitor-lekérdezések áttekintése](../../azure-monitor/logs/log-query-overview.md).

Miután konfigurálta az Azure monitoringot, engedélyezheti a naplók számára az alkalmazások üzembe helyezését. A beállítás a **diagnosztika beállításai** oldalon található.

:::image type="content" source="media/application-provisioning-log-analytics/diagnostic-settings.png" alt-text="Hozzáférés a diagnosztikai beállításokhoz" lightbox="media/application-provisioning-log-analytics/diagnostic-settings.png":::

:::image type="content" source="media/application-provisioning-log-analytics/enable-log-analytics.png" alt-text="Alkalmazás-kiépítési naplók engedélyezése" lightbox="media/application-provisioning-log-analytics/enable-log-analytics.png":::

> [!NOTE]
> Ha nemrég üzembe helyezett egy munkaterületet, eltarthat egy ideig, amíg el nem küldi a naplókat. Ha olyan hibaüzenetet kap, hogy az előfizetés nincs regisztrálva a *Microsoft. bepillantások* használatára, térjen vissza néhány perc múlva.
 
## <a name="understanding-the-data"></a>Az adatgyűjtés ismertetése
Az alapul szolgáló adatfolyam, amelyet a kiépítés küld, a naplók megjelenítői majdnem azonosak. Azure Monitor naplók közel azonos streamtel rendelkeznek, mint a Azure Portal felhasználói felület és az Azure API. A naplózási mezőkben csak néhány **eltérés** szerepel, ahogy az alábbi táblázatban is látható. A mezőkkel kapcsolatos további tudnivalókért tekintse meg a [ProvisioningObjectSummary listázása](/graph/api/provisioningobjectsummary-list?preserve-view=true&tabs=http&view=graph-rest-beta)című témakört.

|Azure Monitor-naplók   |Azure Portal felhasználói felület   |Azure API |
|----------|-----------|------------|
|errorDescription |reason |resultDescription |
|status |resultType |resultType |
|activityDateTime |TimeGenerated |TimeGenerated |


## <a name="azure-monitor-workbooks"></a>Munkafüzetek Azure Monitor

Azure Monitor munkafüzetek rugalmas vásznon biztosítanak adatelemzést. Emellett részletes vizualizációs jelentések létrehozását is lehetővé teszik a Azure Portalon belül. További információ: [Azure monitor munkafüzetek áttekintése](../../azure-monitor/visualize/workbooks-overview.md).

Az alkalmazás üzembe helyezése előre elkészített munkafüzetekből áll. A munkafüzeteket a munkafüzetek lapon tekintheti meg. Az adatok megtekintéséhez meg kell győződnie arról, hogy az összes szűrő (timeRange, jobID, appName) fel van töltve. Emellett meg kell győződnie arról, hogy kiépített egy alkalmazást, ellenkező esetben nem fog tudni semmilyen adatnaplót.

:::image type="content" source="media/application-provisioning-log-analytics/workbooks.png" alt-text="Alkalmazás-kiépítési munkafüzetek" lightbox="media/application-provisioning-log-analytics/workbooks.png":::

:::image type="content" source="media/application-provisioning-log-analytics/report.png" alt-text="Alkalmazás-üzembe helyezési irányítópult" lightbox="media/application-provisioning-log-analytics/report.png":::

## <a name="custom-queries"></a>Egyéni lekérdezések

Létrehozhat egyéni lekérdezéseket, és megjelenítheti az Azure-irányítópultokon tárolt adatmegjelenítést. Ebből a cikkből megtudhatja, hogyan [hozhat létre és oszthat meg irányítópultokat log Analytics-adatelemekről](../../azure-monitor/logs/get-started-queries.md). Ügyeljen arra is, hogy tekintse át a [Azure monitor a naplók lekérdezését](../../azure-monitor/logs/log-query-overview.md).

Íme néhány példa az alkalmazás üzembe helyezésének megkezdéséhez.

A felhasználó naplófájljainak lekérdezése a forrásrendszer AZONOSÍTÓjának alapján:
```kusto
AADProvisioningLogs
| extend SourceIdentity = parse_json(SourceIdentity)
| where tostring(SourceIdentity.Id) == "49a4974bb-5011-415d-b9b8-78caa7024f9a"
```

Összesítések száma ErrorCode:
```kusto
AADProvisioningLogs
| summarize count() by ErrorCode = ResultSignature
```

Események számának összefoglalása naponta, művelet szerint:
```kusto
AADProvisioningLogs
| where TimeGenerated > ago(7d)
| summarize count() by Action, bin(TimeGenerated, 1d)
```

Take 100-események és a Project Key tulajdonságok:
```kusto
AADProvisioningLogs
| extend SourceIdentity = parse_json(SourceIdentity)
| extend TargetIdentity = parse_json(TargetIdentity)
| extend ServicePrincipal = parse_json(ServicePrincipal)
| where tostring(SourceIdentity.identityType) == "Group"
| project tostring(ServicePrincipal.Id), tostring(ServicePrincipal.Name), ModifiedProperties, JobId, Id, CycleId, ChangeId, Action, SourceIdentity.identityType, SourceIdentity.details, TargetIdentity.identityType, TargetIdentity.details, ProvisioningSteps
|take 100
```

## <a name="custom-alerts"></a>Egyéni riasztások

Azure Monitor lehetővé teszi az egyéni riasztások konfigurálását, hogy értesítést kapjon az üzembe helyezéshez kapcsolódó kulcsfontosságú eseményekről. Előfordulhat például, hogy riasztást szeretne kapni a hibákkal kapcsolatos tüskékkel kapcsolatban. Vagy lehet, hogy a letiltások vagy a törlési tüskéket. Egy másik példa arra, hogy hol lehet riasztást kapni, mert hiányzik a kiépítés, ami azt jelzi, hogy valami nem megfelelő.

A riasztásokkal kapcsolatos további tudnivalókért tekintse meg az [Azure monitor riasztásokkal kapcsolatos események megválaszolásával foglalkozó](../../azure-monitor/alerts/tutorial-response.md)témakört.

Riasztás, ha hiba van a csúcson. Cserélje le a jobID az alkalmazás jobID.

:::image type="content" source="media/application-provisioning-log-analytics/alert1.png" alt-text="Riasztás, ha hiba van a csúcson." lightbox="media/application-provisioning-log-analytics/alert1.png":::

Előfordulhat, hogy a kiépítési szolgáltatás leállását okozó probléma merülhet fel. A következő riasztással derítheti fel, hogy egy adott időintervallumban nincsenek-e kiépítési események.

:::image type="content" source="media/application-provisioning-log-analytics/alert2.png" alt-text="Előfordulhat, hogy a kiépítési szolgáltatás leállását okozó probléma merülhet fel." lightbox="media/application-provisioning-log-analytics/alert2.png":::

Riasztás, ha a rendszer letiltja vagy törli a nyársat.

:::image type="content" source="media/application-provisioning-log-analytics/alert3.png" alt-text="Riasztás, ha a rendszer letiltja vagy törli a nyársat." lightbox="media/application-provisioning-log-analytics/alert3.png":::


## <a name="community-contributions"></a>Közösségi hozzájárulások

Nyílt forráskódú és közösségi alapú megközelítést végzünk az alkalmazás-kiépítési lekérdezésekhez és irányítópultokhoz. Ha olyan lekérdezést, riasztást vagy munkafüzetet készített, amelyet mások is hasznosnak talál, mindenképpen tegye közzé azt a [AzureMonitorCommunity GitHub](https://github.com/microsoft/AzureMonitorCommunity)-tárházban. Ezután írjon nekünk egy hivatkozást tartalmazó e-mailt. A szolgáltatás áttekinti és közzéteszi a szolgáltatást, így mások is részesülhetnek. Kapcsolatba léphet velünk a következő címen: provisioningfeedback@microsoft.com .

## <a name="next-steps"></a>Következő lépések

- [Log Analytics](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md)
- [Ismerkedés a Azure Monitor-naplókban található lekérdezésekkel](../../azure-monitor/logs/get-started-queries.md)
- [Riasztási csoportok létrehozása és kezelése a Azure Portal](../../azure-monitor/alerts/action-groups.md)
- [A log Analytics-nézetek telepítése és használata Azure Active Directory](../reports-monitoring/howto-install-use-log-analytics-views.md)
- [A naplók API üzembe helyezése](/graph/api/resources/provisioningobjectsummary?preserve-view=true&view=graph-rest-beta)