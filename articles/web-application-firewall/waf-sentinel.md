---
title: Az Azure Sentinel használata az Azure webalkalmazási tűzfallal
description: Ez a cikk bemutatja, hogyan használható az Azure Sentinel az Azure Web Application Firewall (WAF) használatával
services: web-application-firewall
author: TreMansdoerfer
ms.service: web-application-firewall
ms.date: 10/12/2020
ms.author: victorh
ms.topic: how-to
ms.openlocfilehash: 3d905dd1e6acab8f9f6d3885c882dd9c32133cb4
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100596440"
---
# <a name="using-azure-sentinel-with-azure-web-application-firewall"></a>Az Azure Sentinel használata az Azure webalkalmazási tűzfallal

Az Azure Sentinel szolgáltatással együtt az Azure webalkalmazási tűzfal (WAF) biztosítja a WAF-erőforrások biztonsági információinak kezelését. Az Azure Sentinel Log Analytics használatával biztosítja a biztonsági elemzéseket, így egyszerűen lebonthatja és megtekintheti a WAF adatait. Az Azure Sentinel használatával előre elkészített munkafüzetek érhetők el, és a szervezet igényei szerint módosíthatók. A munkafüzet az Azure Content Delivery Network (CDN) WAF, az Azure bejárati WAF, valamint a különböző előfizetések és munkaterületek Application Gateway WAF is képes megjeleníteni.

## <a name="waf-log-analytics-categories"></a>WAF log Analytics-kategóriák

A WAF log Analytics a következő kategóriákba van bontva:  

- Az összes végrehajtott WAF művelet 
- Leggyakoribb 40 letiltott kérelem URI-címei 
- A legfontosabb 50 esemény-eseményindítók,  
- Üzenetek az idő függvényében 
- Teljes üzenet részletei 
- Támadási események üzenetek szerint  
- Támadási események az idő múlásával 
- Követési azonosító szűrője 
- AZONOSÍTÓ üzenetek nyomon követése 
- 10 leggyakoribb támadó IP-cím 
- IP-címek támadási üzenetei 

## <a name="waf-workbook-examples"></a>WAF-munkafüzetek – példák

A következő WAF példa a mintaadatok megjelenítését szemlélteti:

:::image type="content" source="media//waf-sentinel/waf-actions-filter.png" alt-text="WAF műveletek szűrője":::

:::image type="content" source="media//waf-sentinel/top-50-event-trigger.png" alt-text="Leggyakoribb 50-események":::

:::image type="content" source="media//waf-sentinel/attack-events.png" alt-text="Támadási események":::

:::image type="content" source="media//waf-sentinel/top-10-attacking-ip-address.png" alt-text="10 leggyakoribb támadó IP-cím":::

## <a name="launch-a-waf-workbook"></a>WAF-munkafüzet elindítása

A WAF-munkafüzet az összes Azure bejárati ajtón, Application Gatewayon és CDN-WAF működik. Az ilyen erőforrások adatainak csatlakoztatása előtt engedélyeznie kell a log Analytics szolgáltatást az erőforráson. 

Az egyes erőforrásokhoz tartozó log Analytics engedélyezéséhez nyissa meg az egyes Azure bejárati ajtót, Application Gateway vagy CDN-erőforrást:

1. Válassza a **diagnosztikai beállítások** lehetőséget.
2. Válassza a **+ diagnosztikai beállítások hozzáadása** elemet. 
3. A diagnosztikai beállítások lapon:
   1. Adja meg a kívánt nevet. 
   1. Válassza **a küldés log Analytics** lehetőséget. 
   1. Válassza ki a napló célhelyének munkaterületét. 
   1. Válassza ki az elemezni kívánt naplózási típusokat:
      1. Application Gateway: "ApplicationGatewayAccessLog" és "ApplicationGatewayFirewallLog"
      1. Azure bejárati ajtó: "FrontDoorAccessLog" és "FrontDoorFirewallLog"
      1. CDN: "AzureCdnAccessLog"
   1. Kattintson a **Mentés** gombra.

   :::image type="content" source="media//waf-sentinel/diagnostics-setting.png" alt-text="Diagnosztikai beállítás":::

4. Az Azure kezdőlapján írja be az **Azure Sentinel** kifejezést a keresősávba, és válassza ki az **Azure Sentinel** -erőforrást. 
2. Válasszon egy már aktív munkaterületet, vagy hozzon létre egy új munkaterületet. 
3. A bal oldali panelen a **konfiguráció** területen válassza az **adatösszekötők** lehetőséget.
4. Keressen rá a **Microsoft webalkalmazási tűzfal** kifejezésre, és válassza a **Microsoft webalkalmazási tűzfal (WAF)** lehetőséget. Válassza a jobb alsó sarokban található **összekötő megnyitása** lapot.

   :::image type="content" source="media//waf-sentinel/data-connectors.png" alt-text="Adatösszekötők":::

8. Ha korábban még nem tette meg, kövesse az egyes WAF-erőforrások **konfigurációjának** utasításait.
6. Miután végzett az egyes WAF-erőforrások konfigurálásával, válassza a **következő lépések** fület. Válassza ki a javasolt munkafüzetek egyikét. Ez a munkafüzet a korábban engedélyezett összes naplózási elemzési adatmezőt fogja használni. Most már léteznie kell egy működő WAF-munkafüzetnek a WAF-erőforrásokhoz.

   :::image type="content" source="media//waf-sentinel/waf-workbooks.png" alt-text="WAF-munkafüzetek":::


## <a name="next-steps"></a>Következő lépések

- [További információ az Azure Sentinelről](../sentinel/overview.md)
- [További információ Azure Monitor munkafüzetekről](../azure-monitor/visualize/workbooks-overview.md)
