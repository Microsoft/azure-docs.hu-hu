---
title: Azure-beli autoskálázás egyéni metrika használatával
description: Ismerje meg, hogyan méretezheti az erőforrásokat az Azure-beli egyéni metrika használatával.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/07/2017
ms.author: ancav
ms.subservice: autoscale
ms.openlocfilehash: f83c68dd2f2ddbb73974a2ea4e91e746308945c6
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73958118"
---
# <a name="get-started-with-auto-scale-by-custom-metric-in-azure"></a>Ismerkedés az Azure-beli egyéni metrika automatikus méretezésével
Ez a cikk azt ismerteti, hogyan méretezhető az erőforrás a Azure Portalban lévő egyéni metrika alapján.

Azure Monitor az autoscale csak [Virtual Machine Scale sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [app Service-Web Apps](https://azure.microsoft.com/services/app-service/web/)és [API Management szolgáltatásokra](https://docs.microsoft.com/azure/api-management/api-management-key-concepts)vonatkozik.

## <a name="lets-get-started"></a>Lehetővé teszi az első lépéseket
Ez a cikk azt feltételezi, hogy van egy webalkalmazása, amelyen be van állítva az Application betekintő szolgáltatás. Ha még nem rendelkezik ilyennel, beállíthatja [a ASP.net webhelyéhez Application Insights][1]

- [Azure Portal][2] megnyitása
- Kattintson Azure Monitor ikonra a bal oldali navigációs ablaktáblán.
  Azure Monitor ![elindítása][3]
- Az automatikus skálázás beállításra kattintva megtekintheti az összes olyan erőforrást, amelyhez az automatikus méretezés alkalmazható, valamint a jelenlegi automatikus méretezési állapottal ![az automatikus skálázás észlelése az Azure monitor][4]
- Nyissa meg az "autoscale" panelt Azure Monitor, és válassza ki a méretezni kívánt erőforrást
  > Megjegyzés: az alábbi lépések egy app Service-csomaghoz társított app Service-csomagot használnak, amely az alkalmazás-felismerések konfigurálására szolgál.
- Az erőforrás skálázási beállítás paneljén figyelje meg, hogy az aktuális példányszám 1. Kattintson az "autoscale engedélyezése" elemre.
  ![méretezési beállítás az új webalkalmazáshoz][5]
- Adja meg a skálázási beállítás nevét, és kattintson a "szabály hozzáadása" gombra. Figyelje meg, hogy a méretezési szabály beállításai a jobb oldali kontextus ablaktáblán nyílnak meg. Alapértelmezés szerint ez a beállítás a példányszám 1 értékre való skálázását adja meg, ha az erőforrás CPU-százaléka meghaladja a 70%-ot. Módosítsa a felső metrikai forrást a "Application Insights" értékre, válassza ki az alkalmazás-elemzési erőforrást az "erőforrás" legördülő menüben, majd válassza ki azt az egyéni metrikát, amely alapján méretezni szeretné a méretezést.
  ![méretezés egyéni metrika alapján][6]
- A fenti lépéshez hasonlóan vegyen fel egy méretezési szabályt, amely a skálázást végzi, és csökkentse a skálázási szám értékét 1-re, ha az egyéni metrika egy küszöbérték alá esik.
  ![méretezés CPU-][7] alapján
- Adja meg a példányok korlátait. Ha például az egyéni metrikák ingadozása alapján szeretné méretezni az 2-5-es példányok közötti skálázást, a "minimum" értéket állítsa "2", "maximum" értékre "5" és "default" értékre "2" értékre.
  > Megjegyzés: Ha probléma merül fel az erőforrás-metrikák beolvasásakor, és a jelenlegi kapacitás az alapértelmezett kapacitás alatt van, akkor az erőforrás rendelkezésre állásának biztosításához az automatikusan méretezhető az alapértelmezett érték. Ha az aktuális kapacitás már magasabb, mint az alapértelmezett kapacitás, az autoskálázás nem fog méretezni.
- Kattintson a Save (Mentés) gombra.

Gratulálunk! Most sikeresen létrehozta a méretezési beállítást, hogy az egyéni metrika alapján automatikusan méretezze a webalkalmazást.

> Megjegyzés: ugyanezek a lépések érvényesek a VMSS vagy a Cloud Service szerepkörrel való ismerkedésre.

<!--Reference-->
[1]: https://docs.microsoft.com/azure/application-insights/app-insights-asp-net
[2]: https://portal.azure.com
[3]: ./media/autoscale-custom-metric/azure-monitor-launch.png
[4]: ./media/autoscale-custom-metric/discover-autoscale-azure-monitor.png
[5]: ./media/autoscale-custom-metric/scale-setting-new-web-app.png
[6]: ./media/autoscale-custom-metric/scale-by-custom-metric.png
[7]: ./media/autoscale-custom-metric/autoscale-setting-custom-metrics-ai.png

