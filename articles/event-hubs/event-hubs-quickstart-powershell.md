---
title: 'Rövid útmutató: Eseményközpont létrehozása a PowerShell használatával – Azure Event Hubs'
description: Ez a rövid útmutató azt mutatja be, hogyan hozhat létre egy eseményközpontot az Azure PowerShell-lel, majd hogyan küldhet és fogadhat eseményeket a .NET Standard SDK használatával.
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: be2761bedd3198d25cb279e6a8ff188db6a51de2
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873766"
---
# <a name="quickstart-create-an-event-hub-using-azure-powershell"></a>Rövid útmutató: Eseményközpont létrehozása a PowerShell-lel

Az Azure Event Hubs egy Big Data streamplatform és eseményfeldolgozó szolgáltatás, amely másodpercenként több millió esemény fogadására és feldolgozására képes. Az Event Hubs képes az elosztott szoftverek és eszközök által generált események, adatok vagy telemetria feldolgozására és tárolására. Az eseményközpontokba elküldött adatok bármilyen valós idejű elemzési szolgáltató vagy kötegelési/tárolóadapter segítségével átalakíthatók és tárolhatók. Az Event Hubs részletes áttekintéséért lásd az [Event Hubs áttekintését](event-hubs-about.md) és az [Event Hubs-szolgáltatásokat](event-hubs-features.md) ismertető cikket.

Ebben a rövid útmutatóban az Azure PowerShell használatával hoz létre eseményközpontot.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az oktatóanyag elvégzéséhez győződjön meg arról, hogy rendelkezik a következőkkel:

- Egy Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot][], mielőtt hozzákezd.
- [Visual Studio 2019- ben.](https://www.visualstudio.com/vs)
- [.NET Core SDK](https://dotnet.microsoft.com/download), 2.0-s vagy újabb verzió.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha helyileg használja a PowerShellt, a rövid útmutató elvégzéséhez a PowerShell legújabb verzióját kell futtatnia. Ha telepíteni vagy frissíteni szeretne: [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/install-az-ps).

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az erőforráscsoport Azure-erőforrások logikai gyűjteménye. Az eseményközpont létrehozásához szüksége lesz egy erőforráscsoportra. 

A következő példa létrehoz egy erőforráscsoportot az USA keleti régiójában. Cserélje le a `myResourceGroup` elemet a használni kívánt erőforráscsoport nevére:

```azurepowershell-interactive
New-AzResourceGroup –Name myResourceGroup –Location eastus
```

## <a name="create-an-event-hubs-namespace"></a>Event Hubs-névtér létrehozása

Az erőforráscsoport létrehozása után hozzon létre egy Event Hubs-névteret az erőforráscsoportban. Az Event Hubs-névtér egyedi, teljes tartománynevet biztosít, amelyben létrehozhatja az eseményközpontot. Cserélje le a `namespace_name` elemet a névtér egyedi nevére:

```azurepowershell-interactive
New-AzEventHubNamespace -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Location eastus
```

## <a name="create-an-event-hub"></a>Eseményközpont létrehozása

Most, hogy rendelkezik Event Hubs-névtérrel, hozzon létre egy eseményközpontot abban a névtérben:  
A engedélyezett `MessageRetentionInDays` időtartama 1 és 7 nap között lehet.

```azurepowershell-interactive
New-AzEventHub -ResourceGroupName myResourceGroup -NamespaceName namespace_name -EventHubName eventhub_name -MessageRetentionInDays 3
```

Gratulálunk! Az Azure PowerShell segítségével létrehozott egy Event Hubs-névteret és egy eseményközpontot a névtéren belül. 

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben egy Event Hubs-névteret hozott létre, és mintaalkalmazások használatával eseményeket küldött az eseményközpontba, illetve fogadott onnan. Az események eseményközpontba való küldésének (vagy az eseményközpontból való fogadásának) lépéseiért tekintse meg az Események küldése és **fogadása** oktatóanyagokat: 

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [JavaScript](event-hubs-node-get-started-send.md)
- [Ugrás](event-hubs-go-get-started-send.md)
- [C (csak küldés)](event-hubs-c-getstarted-send.md)
- [Apache Storm (csak fogadás)](event-hubs-storm-getstarted-receive.md)


[ingyenes fiók létrehozása]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install and Configure Azure PowerShell]: /powershell/azure/install-az-ps
[New-AzResourceGroup]: /powershell/module/az.resources/new-azresourcegroup
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png
