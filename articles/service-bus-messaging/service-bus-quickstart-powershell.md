---
title: Service Bus üzenetsor létrehozásához használja a Azure PowerShell
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Service Bus névteret és egy várólistát a névtérben a Azure PowerShell használatával.
author: spelluru
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 08/12/2020
ms.author: spelluru
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8cf024735c66e6bae9d334e3d8ce8d0a0eed1426
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95799106"
---
# <a name="use-azure-powershell-to-create-a-service-bus-namespace-and-a-queue"></a>Service Bus névtér és üzenetsor létrehozása Azure PowerShell használatával
Ez a rövid útmutató bemutatja, hogyan hozhat létre Service Bus névteret és üzenetsor-kezelést a Azure PowerShell használatával. Azt is bemutatjuk, hogyan kérheti le az olyan engedélyezési hitelesítő adatokat, amelyeket az ügyfélalkalmazás használhat a várólistára irányuló üzenetek küldéséhez/fogadásához. 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]


## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató elvégzéséhez győződjön meg arról, hogy rendelkezik Azure-előfizetéssel. Ha nem rendelkezik Azure-előfizetéssel, akkor a Kezdés előtt létrehozhat egy [ingyenes fiókot][] . 

Ebben a rövid útmutatóban Azure Cloud Shelleket fog használni, amelyeket a Azure Portalba való bejelentkezés után indíthat el. A Azure Cloud Shellről további részleteket a [Azure Cloud Shell áttekintése](../cloud-shell/overview.md)című témakörben talál. A Azure PowerShell a gépen is [telepítheti](/powershell/azure/install-Az-ps) és használhatja. 


## <a name="provision-resources"></a>Erőforrások kiosztása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Indítsa el Azure Cloud Shell az alábbi képen látható ikon kiválasztásával: 

    :::image type="content" source="./media/service-bus-quickstart-powershell/launch-cloud-shell.png" alt-text="Cloud Shell elindítása":::
3. Az alsó Cloud Shell ablakban váltson át a **bash** -ből a **powershellre**. 

    :::image type="content" source="./media/service-bus-quickstart-powershell/cloud-power-shell.png" alt-text="Váltás PowerShell-módra":::    
4. Futtassa az alábbi parancsot egy Azure-erőforráscsoport létrehozásához. Ha kívánja, frissítse az erőforráscsoport nevét és a helyét. 

    ```azurepowershell-interactive
    New-AzResourceGroup –Name ContosoRG –Location eastus
    ```
5. A következő parancs futtatásával hozzon létre egy Service Bus üzenetküldési névteret. Ebben a példában az az `ContosoRG` erőforráscsoport, amelyet az előző lépésben hozott létre. `ContosoSBusNS` az adott erőforráscsoporthoz létrehozott Service Bus névtér neve. 

    ```azurepowershell-interactive
    New-AzServiceBusNamespace -ResourceGroupName ContosoRG -Name ContosoSBusNS -Location eastus
    ```
6. A következő lépés futtatásával hozzon létre egy várólistát az előző lépésben létrehozott névtérben. 

    ```azurepowershell-interactive
    New-AzServiceBusQueue -ResourceGroupName ContosoRG -NamespaceName ContosoSBusNS -Name ContosoOrdersQueue 
    ```
7. A névtér elsődleges kapcsolatok karakterláncának beolvasása. Ezt a kapcsolati karakterláncot használja a várólistához való kapcsolódáshoz, valamint az üzenetek küldéséhez és fogadásához. 

    ```azurepowershell-interactive    
    Get-AzServiceBusKey -ResourceGroupName ContosoRG -Namespace ContosoSBusNS -Name RootManageSharedAccessKey
    ```

    Jegyezze fel a kapcsolatok sztringjét és a várólista nevét. Ezek az üzenetek küldésére és fogadására használhatók. 


## <a name="next-steps"></a>Következő lépések
Ebben a cikkben egy Service Bus névteret és egy várólistát hozott létre a névtérben. Ha meg szeretné tudni, hogyan küldhet/fogadhat üzeneteket a várólistára, tekintse meg az alábbi rövid útmutatók egyikét a **küldési és fogadási üzenetek** szakaszban. 

- [.NET](service-bus-dotnet-get-started-with-queues.md)
- [Java](service-bus-java-how-to-use-queues.md)
- [JavaScript](service-bus-nodejs-how-to-use-queues.md)
- [Python](service-bus-python-how-to-use-queues.md)
- [PHP](service-bus-php-how-to-use-queues.md)
- [Ruby](service-bus-ruby-how-to-use-queues.md)

[ingyenes fiók]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

