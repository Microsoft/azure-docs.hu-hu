---
title: Végrehajtási naplók streamelése a Azure Functions
description: 115–145 karakter, szóközöket is beleértve. Ez a kivonat jelenik meg a keresési eredmények között.
ms.date: 9/1/2020
ms.topic: how-to
ms.custom: contperf-fy21q2, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 4afb1068acda69c9dd65a423d887abea80c695cd
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830897"
---
# <a name="enable-streaming-execution-logs-in-azure-functions"></a>Streamvégrehajtási naplók engedélyezése a Azure Functions

Az alkalmazások fejlesztése során gyakran szeretné látni, hogy az Azure-ban való futtatáskor milyen adatok vannak a naplókba írva közel valós időben.

A függvény-végrehajtások által létrehozott naplófájlok adatfolyamát kétféleképpen lehet megtekinteni.

* **Beépített naplóstreamelés:** a App Service platform lehetővé teszi az alkalmazás naplófájlja adatfolyamának megtekintését. Ez megegyezik a függvények helyi fejlesztés [](functions-develop-local.md) során történő hibakeresése  során és a portál Tesztelés lapján látható kimenettel. Minden naplóalapú információ megjelenik. További információ: [Stream-naplók.](../app-service/troubleshoot-diagnostic-logs.md#stream-logs) Ez a streamelési módszer csak egy példányt támogat, és nem használható Használat alapján, Linuxon futó alkalmazásokkal.

* **Élő metrikastream:** ha a függvényalkalmazás a [Application Insights-hez](configure-monitoring.md#enable-application-insights-integration)csatlakozik, a naplóadatokat és más metrikákat közel valós időben megtekintheti a Azure Portal a [Élő metrikastream.](../azure-monitor/app/live-stream.md) Ezt a módszert akkor használja, ha több példányon vagy Linuxon futó függvényeket figyel használat alapján. Ez a metódus a [mintavételből vett adatokat használja.](configure-monitoring.md#configure-sampling)

A naplóstreamek a portálon és a legtöbb helyi fejlesztési környezetben is megtekinthetők. 

## <a name="portal"></a>Portál

A naplóstreamek mindkét típusát megtekintheti a portálon.

### <a name="built-in-log-streaming"></a>Beépített naplóstreamelés

A streamnaplók portálon való megtekintéséhez válassza a **függvényalkalmazás Platformfunkciók** lapját. Ezután a Figyelés **alatt** válassza a **Naplóstreamelés lehetőséget.**

![Streamnaplók engedélyezése a portálon](./media/functions-monitoring/enable-streaming-logs-portal.png)

Ez csatlakoztatja az alkalmazást a naplóstreamelési szolgáltatáshoz, és az alkalmazásnaplók megjelennek az ablakban. Válthat az Alkalmazásnaplók és a **Webkiszolgáló-naplók között.**   

![Streamnaplók megtekintése a portálon](./media/functions-monitoring/streaming-logs-window.png)

### <a name="live-metrics-stream"></a>Élő metrikastream

Az alkalmazás Élő metrikastream megtekintéséhez válassza a függvényalkalmazás **Áttekintés** lapját. Ha ezt Application Insights engedélyezi, a Konfigurált **szolgáltatások Application Insights** **egy hivatkozás.** Ez a hivatkozás az alkalmazás Application Insights lapjára viszi.

A Application Insights válassza **a** Élő metrikastream lehetőséget. [A mintában szereplő naplóbejegyzések](configure-monitoring.md#configure-sampling) a Minta **telemetria alatt jelennek meg.**

![A Élő metrikastream megtekintése a portálon](./media/functions-monitoring/live-metrics-stream.png) 

## <a name="visual-studio-code"></a>Visual Studio Code

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

## <a name="core-tools"></a>Core Tools

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

## <a name="azure-cli"></a>Azure CLI

A streamelési naplókat az Azure CLI használatával [engedélyezheti.](/cli/azure/install-azure-cli) Az alábbi parancsokkal bejelentkezhet, kiválaszthatja az előfizetését, és streamelheti a naplófájlokat:

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <RESOURCE_GROUP_NAME> --name <FUNCTION_APP_NAME>
```

## <a name="azure-powershell"></a>Azure PowerShell

A streamnaplók engedélyezéséhez használja a [Azure PowerShell.](/powershell/azure/) A PowerShell esetében használja a [Set-AzWebApp parancsot](/powershell/module/az.websites/set-azwebapp) a függvényalkalmazás naplózásának engedélyezéséhez, az alábbi kódrészletben látható módon: 

:::code language="powershell" source="~/powershell_scripts/app-service/monitor-with-logs/monitor-with-logs.ps1" range="19-20":::

További információért tekintse meg a [teljes példakódot.](../app-service/scripts/powershell-monitor.md#sample-script) 

## <a name="next-steps"></a>Következő lépések

+ [Az Azure Functions monitorozása](functions-monitoring.md)
+ [A Azure Functions telemetriának elemzése a Application Insights](analyze-telemetry-data.md)
