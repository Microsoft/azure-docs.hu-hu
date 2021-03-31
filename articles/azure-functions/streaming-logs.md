---
title: Stream-végrehajtási naplók a Azure Functions
description: 115-145 karakter, beleértve a szóközöket. Ez a kivonat jelenik meg a keresési eredmények között.
ms.date: 9/1/2020
ms.topic: how-to
ms.custom: contperf-fy21q2, devx-track-azurecli
ms.openlocfilehash: 085849386fce929ceaec4536844cf31b94fe7539
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97033051"
---
# <a name="enable-streaming-execution-logs-in-azure-functions"></a>Adatfolyam-végrehajtási naplók engedélyezése Azure Functions

Egy alkalmazás fejlesztése során gyakran érdemes megtekinteni, hogy mi történik a naplókba közel valós időben, amikor az Azure-ban fut.

Kétféle módon lehet megtekinteni a függvények végrehajtásával létrehozott naplófájlok streamjét.

* **Beépített log streaming**: a app Service platformon megtekintheti az alkalmazás naplófájljainak streamjét. Ez egyenértékű azzal a kimenettel, amelyet a függvények a [helyi fejlesztés](functions-develop-local.md) során végzett hibakereséskor, illetve a portálon a **teszt** lap használatakor észlelt. Megjelenik az összes napló alapú információ. További információ: stream- [naplók](../app-service/troubleshoot-diagnostic-logs.md#stream-logs). Ez a folyamatos átviteli módszer csak egyetlen példányt támogat, és nem használható a Linux rendszeren futó alkalmazással egy használati tervben.

* **Élő metrikastream**: Ha a function alkalmazás [Application Insightshoz csatlakozik](configure-monitoring.md#enable-application-insights-integration), a Azure Portal a [élő metrikastream](../azure-monitor/app/live-stream.md)használatával megtekintheti a naplózási adatokat és az egyéb mérőszámokat közel valós időben. Ezt a módszert akkor használja, ha több példányon vagy Linuxon futó figyelési funkciót használ a használati tervben. Ez a metódus [mintavételes adathalmazt](configure-monitoring.md#configure-sampling)használ.

A naplózási streamek a Portálon és a legtöbb helyi fejlesztési környezetben is megtekinthetők. 

## <a name="portal"></a>Portál

A portálon mindkét típusú naplózási adatfolyamot megtekintheti.

### <a name="built-in-log-streaming"></a>Beépített naplóstreamelés

Ha a portálon szeretné megtekinteni a folyamatos átviteli naplókat, válassza a **platform szolgáltatások** fület a Function alkalmazásban. Ezután a **figyelés** területen válassza a **naplózási adatfolyam** lehetőséget.

![Folyamatos átviteli naplók engedélyezése a portálon](./media/functions-monitoring/enable-streaming-logs-portal.png)

Ezzel összekapcsolja az alkalmazást a log streaming szolgáltatáshoz, és az alkalmazás naplói megjelennek az ablakban. Válthat az **alkalmazási naplók** és a **webkiszolgálói naplók** között.  

![Folyamatos átviteli naplók megtekintése a portálon](./media/functions-monitoring/streaming-logs-window.png)

### <a name="live-metrics-stream"></a>Élő metrikastream

Az alkalmazás Élő metrikastream megtekintéséhez válassza a Function app **Áttekintés** lapját. Application Insights engedélyezése esetén a **konfigurált szolgáltatások** területen megjelenik egy **Application Insights** -hivatkozás. Ez a hivatkozás az alkalmazás Application Insights lapjára lép.

A Application Insights területen válassza a **élő metrikastream** lehetőséget. A [mintavételes naplóbejegyzések](configure-monitoring.md#configure-sampling) a **minta telemetria** alatt jelennek meg.

![Élő metrikastream megtekintése a portálon](./media/functions-monitoring/live-metrics-stream.png) 

## <a name="visual-studio-code"></a>Visual Studio Code

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

## <a name="core-tools"></a>Alapvető eszközök

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

## <a name="azure-cli"></a>Azure CLI

Az [Azure CLI](/cli/azure/install-azure-cli)használatával engedélyezheti a folyamatos átviteli naplókat. A következő parancsokkal jelentkezzen be, válassza ki az előfizetését és a stream naplófájljait:

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <RESOURCE_GROUP_NAME> --name <FUNCTION_APP_NAME>
```

## <a name="azure-powershell"></a>Azure PowerShell

[Azure PowerShell](/powershell/azure/)használatával engedélyezheti a folyamatos átviteli naplókat. A PowerShell esetében a [set-AzWebApp](/powershell/module/az.websites/set-azwebapp) paranccsal engedélyezheti a naplózást a Function alkalmazásban, ahogy az a következő kódrészletben látható: 

:::code language="powershell" source="~/powershell_scripts/app-service/monitor-with-logs/monitor-with-logs.ps1" range="19-20":::

További információ: [példa a teljes programkódra](../app-service/scripts/powershell-monitor.md#sample-script). 

## <a name="next-steps"></a>Következő lépések

+ [Az Azure Functions monitorozása](functions-monitoring.md)
+ [Azure Functions telemetria elemzése Application Insights](analyze-telemetry-data.md)
