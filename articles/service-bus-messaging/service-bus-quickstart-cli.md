---
title: Gyors útmutató – Service Bus üzenetsor létrehozásához használja az Azure CLI-t | Microsoft Docs
description: Ebből a rövid útmutatóból megtudhatja, hogyan használhatja az Azure CLI-t egy Service Bus névtér, majd egy üzenetsor létrehozásához a névtérben.
author: spelluru
ms.topic: quickstart
ms.date: 08/12/2020
ms.author: spelluru
ms.openlocfilehash: 14892b114f3bb8a0bb6c07f7cdd01a174b931cf9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95810626"
---
# <a name="use-the-azure-cli-to-create-a-service-bus-namespace-and-a-queue"></a>Service Bus névtér és üzenetsor létrehozása az Azure CLI használatával
Ez a rövid útmutató bemutatja, hogyan hozhat létre Service Bus névteret és üzenetsor-kezelést az Azure CLI használatával. Azt is bemutatjuk, hogyan kérheti le az olyan engedélyezési hitelesítő adatokat, amelyeket az ügyfélalkalmazás használhat a várólistára irányuló üzenetek küldéséhez/fogadásához. 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="prerequisites"></a>Előfeltételek
Ha nem rendelkezik Azure-előfizetéssel, akkor a Kezdés előtt létrehozhat egy [ingyenes fiókot][free account] .

Ebben a rövid útmutatóban Azure Cloud Shelleket fog használni, amelyeket a Azure Portalba való bejelentkezés után indíthat el. A Azure Cloud Shellről további részleteket a [Azure Cloud Shell áttekintése](../cloud-shell/overview.md)című témakörben talál. A Azure PowerShell a gépen is [telepítheti](/cli/azure/install-azure-cli) és használhatja. 

## <a name="provision-resources"></a>Erőforrások kiosztása
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Indítsa el Azure Cloud Shell az alábbi képen látható ikon kiválasztásával. Váltson **bash** módra, ha a Cloud Shell **PowerShell** módban van. 

    :::image type="content" source="./media/service-bus-quickstart-powershell/launch-cloud-shell.png" alt-text="Cloud Shell elindítása":::
3. Futtassa az alábbi parancsot egy Azure-erőforráscsoport létrehozásához. Ha kívánja, frissítse az erőforráscsoport nevét és a helyét. 

    ```azurecli-interactive
    az group create --name ContosoRG --location eastus
    ```
4. A következő parancs futtatásával hozzon létre egy Service Bus üzenetküldési névteret.

    ```azurecli-interactive
    az servicebus namespace create --resource-group ContosoRG --name ContosoSBusNS --location eastus
    ```
5. A következő parancs futtatásával hozzon létre egy várólistát az előző lépésben létrehozott névtérben. Ebben a példában az az `ContosoRG` erőforráscsoport, amelyet az előző lépésben hozott létre. `ContosoSBusNS` az adott erőforráscsoporthoz létrehozott Service Bus névtér neve. 

    ```azurecli-interactive
    az servicebus queue create --resource-group ContosoRG --namespace-name ContosoSBusNS --name ContosoOrdersQueue
    ```
6. Futtassa a következő parancsot a névtér elsődleges kapcsolódási karakterláncának lekéréséhez. Ezt a kapcsolati karakterláncot használja a várólistához való kapcsolódáshoz, valamint az üzenetek küldéséhez és fogadásához. 

    ```azurecli-interactive
    az servicebus namespace authorization-rule keys list --resource-group ContosoRG --namespace-name ContosoSBusNS --name RootManageSharedAccessKey --query primaryConnectionString --output tsv    
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

[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

