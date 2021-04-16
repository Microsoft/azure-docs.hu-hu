---
title: Üzenetsor Azure PowerShell létrehozása a Service Bus használatával
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Service Bus névteret és egy üzenetsort a névtérben a Azure PowerShell.
author: spelluru
ms.author: spelluru
ms.date: 08/12/2020
ms.topic: quickstart
ms.devlang: dotnet
ms.custom:
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: f7bf9e5435b00ee3076422cccbe689038051499d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537103"
---
# <a name="use-azure-powershell-to-create-a-service-bus-namespace-and-a-queue"></a>A Azure PowerShell használatával hozzon létre egy Service Bus-névteret és egy üzenetsort
Ez a rövid útmutató bemutatja, hogyan hozhat létre egy Service Bus névteret és egy üzenetsort a Azure PowerShell. Azt is bemutatja, hogyan kaphatja meg azokat az engedélyezési hitelesítő adatokat, amelyek használatával az ügyfélalkalmazás üzeneteket küldhet és fogadhat az üzenetsorból. 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]


## <a name="prerequisites"></a>Előfeltételek

A rövid útmutató befejezéséhez győződjön meg arról, hogy rendelkezik Azure-előfizetéssel. Ha nem rendelkezik Azure-előfizetéssel, a kezdés előtt létrehozhat egy [ingyenes][] fiókot. 

Ebben a rövid útmutatóban olyan Azure Cloud Shell, amely a bejelentkezés után indít Azure Portal. További információ a Azure Cloud Shell: [Overview of Azure Cloud Shell](../cloud-shell/overview.md). Telepíthet [és használhat Azure PowerShell](/powershell/azure/install-Az-ps) gépre is. 


## <a name="provision-resources"></a>Erőforrások kiosztása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Indítsa Azure Cloud Shell az alábbi képen látható ikont választva: 

    :::image type="content" source="./media/service-bus-quickstart-powershell/launch-cloud-shell.png" alt-text="A Cloud Shell":::
3. Az alsó Cloud Shell váltson **Bashről** **PowerShellre.** 

    :::image type="content" source="./media/service-bus-quickstart-powershell/cloud-power-shell.png" alt-text="Váltás PowerShell módra":::    
4. Futtassa a következő parancsot egy Azure-erőforráscsoport létrehozásához. Ha szeretné, frissítse az erőforráscsoport nevét és helyét. 

    ```azurepowershell-interactive
    New-AzResourceGroup –Name ContosoRG –Location eastus
    ```
5. Futtassa a következő parancsot egy Service Bus üzenetküldési névtér létrehozásához. Ebben a példában a az előző lépésben `ContosoRG` létrehozott erőforráscsoport. `ContosoSBusNS` A az erőforráscsoportban Service Bus névtér neve. 

    ```azurepowershell-interactive
    New-AzServiceBusNamespace -ResourceGroupName ContosoRG -Name ContosoSBusNS -Location eastus
    ```
6. Futtassa a következőt egy üzenetsor létrehozásához az előző lépésben létrehozott névtérben. 

    ```azurepowershell-interactive
    New-AzServiceBusQueue -ResourceGroupName ContosoRG -NamespaceName ContosoSBusNS -Name ContosoOrdersQueue 
    ```
7. Szerezze be a névtér elsődleges kapcsolati sztringet. Ezzel a kapcsolati sztring használatával csatlakozhat az üzenetsorhoz, és üzeneteket küldhet és fogadhat. 

    ```azurepowershell-interactive    
    Get-AzServiceBusKey -ResourceGroupName ContosoRG -Namespace ContosoSBusNS -Name RootManageSharedAccessKey
    ```

    Jegyezze fel a kapcsolati sztringet és az üzenetsor nevét. Az üzenetek küldésére és fogadására használhatja őket. 


## <a name="next-steps"></a>Következő lépések
Ebben a cikkben létrehozott egy új Service Bus és egy üzenetsort a névtérben. Az üzenetek üzenetsorba vagy üzenetsorból való küldésének/fogadásának elsajátításért tekintse meg az Üzenetek küldése és fogadása szakasz egyik alábbi rövid **útmutatóját.** 

- [.NET](service-bus-dotnet-get-started-with-queues.md)
- [Java](service-bus-java-how-to-use-queues.md)
- [JavaScript](service-bus-nodejs-how-to-use-queues.md)
- [Python](service-bus-python-how-to-use-queues.md)
- [PHP](service-bus-php-how-to-use-queues.md)
- [Ruby](service-bus-ruby-how-to-use-queues.md)

[ingyenes fiók]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
