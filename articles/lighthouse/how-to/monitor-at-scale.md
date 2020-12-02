---
title: A delegált erőforrások nagy léptékű figyelése
description: Megtudhatja, hogyan használhatja hatékonyan a Azure Monitor-naplókat a felügyelt ügyfél-bérlők méretezhető módjában.
ms.date: 10/26/2020
ms.topic: how-to
ms.openlocfilehash: 96ca05faf2b3da8f214c14ae57eb186c7b71e1b3
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461526"
---
# <a name="monitor-delegated-resources-at-scale"></a>A delegált erőforrások nagy léptékű figyelése

Szolgáltatóként több ügyfél-bérlőt is felkészített az [Azure világítótoronyba](../overview.md). Az Azure Lighthouse lehetővé teszi a szolgáltatók számára, hogy egyszerre több bérlőn is elvégezzék a műveleteket, így hatékonyabbá téve a felügyeleti feladatokat.

Ebből a témakörből megtudhatja, hogyan használhatja a [Azure monitor naplókat](../../azure-monitor/platform/data-platform-logs.md) skálázható módon a kezelt ügyfél-bérlők között. Bár a jelen témakörben a szolgáltatók és az ügyfelekre is hivatkozunk, ez az útmutató az [Azure Lighthouse-t használó vállalatoknak is vonatkozik több bérlő kezelésére](../concepts/enterprise.md).

> [!NOTE]
> Győződjön meg arról, hogy a felügyeleti bérlők felhasználói megkaptak a szükséges szerepköröket a delegált ügyfél-előfizetések [log Analytics-munkaterületek kezeléséhez](../../azure-monitor/platform/manage-access.md#manage-access-using-azure-permissions) .

## <a name="create-log-analytics-workspaces"></a>Log Analytics-munkaterületek létrehozása

Az adatok gyűjtéséhez létre kell hoznia Log Analytics munkaterületeket. Ezek a Log Analytics-munkaterületek egyedi környezetek Azure Monitor által gyűjtött adatokhoz. Az egyes munkaterületek saját adattárral és konfigurációval rendelkeznek, és az adatforrások és megoldások úgy vannak konfigurálva, hogy egy adott munkaterületen tárolják az adataikat.

Javasoljuk, hogy ezeket a munkaterületeket közvetlenül az ügyfél bérlői hozza létre. Így az adatkezelésük a saját bérlőn marad, nem pedig a tiéd. Ez lehetővé teszi az Log Analytics által támogatott erőforrások és szolgáltatások központosított figyelését is, ami nagyobb rugalmasságot biztosít a figyelt adattípusok tekintetében.

Log Analytics munkaterületet az [Azure CLI](../../azure-monitor/learn/quick-create-workspace-cli.md)használatával vagy [Azure PowerShell](../../azure-monitor/platform/powershell-workspace-configuration.md)használatával hozhat létre a [Azure Portal](../../azure-monitor/learn/quick-create-workspace.md)használatával.

> [!IMPORTANT]
> Még ha az összes munkaterületet az ügyfél bérlője hozza létre, a Microsoft. betekintő erőforrás-szolgáltatót is regisztrálni kell egy előfizetésben a bérlői felügyeletben.

## <a name="deploy-policies-that-log-data"></a>Az adatnaplózási szabályzatok üzembe helyezése

Miután létrehozta a Log Analytics munkaterületeket, [Azure Policy](../../governance/policy/index.yml) telepítheti az összes ügyfél-hierarchiában, hogy a rendszer elküldje a diagnosztikai adatait az egyes bérlők megfelelő munkaterületére. Az Ön által telepített pontos házirendek a figyelni kívánt erőforrástípusok függvényében változhatnak.

A szabályzatok létrehozásával kapcsolatos további tudnivalókért tekintse meg az [oktatóanyag: házirendek létrehozása és kezelése a megfelelőség kikényszerítés érdekében](../../governance/policy/tutorials/create-and-manage.md)című témakört. Ez a [közösségi eszköz](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/tools/azure-diagnostics-policy-generator) egy olyan parancsfájlt biztosít, amely segítségével szabályzatokat hozhat létre a kiválasztott erőforrástípusok figyeléséhez.

Ha meghatározta, hogy mely házirendeket kell telepítenie, akkor a [delegált előfizetések nagy léptékben is üzembe](policy-at-scale.md)helyezhetők.

## <a name="analyze-the-gathered-data"></a>Az összegyűjtött adatok elemzése

A szabályzatok üzembe helyezése után a rendszer az egyes ügyfelek bérlői által létrehozott Log Analytics munkaterületeken naplózza az adatait. Az összes felügyelt ügyfélhez olyan eszközöket használhat, mint például a [Azure monitor munkafüzetek](../../azure-monitor/platform/workbooks-overview.md) több adatforrásból származó információk gyűjtésére és elemzésére. 

## <a name="next-steps"></a>További lépések

- Ismerje meg ezt az [MVP-alapú minta-munkafüzetet](https://github.com/scautomation/Azure-Automation-Update-Management-Workbooks), amely nyomon követi a javítási megfelelőségi jelentéskészítést Update Management naplók több log Analytics munkaterületen való [lekérdezésével](../../automation/update-management/query-logs.md) . 
- A [Azure monitor](../../azure-monitor/index.yml)megismerése.
- Ismerkedjen meg [Azure monitor naplókkal](../../azure-monitor/platform/data-platform-logs.md).
- További információ a [bérlők közötti felügyeleti élményekről](../concepts/cross-tenant-management-experience.md).