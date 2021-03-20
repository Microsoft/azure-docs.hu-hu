---
title: Service Bus témakörök és előfizetések létrehozása az Azure CLI használatával
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre Service Bus témakört és előfizetéseket az adott témakörhöz az Azure CLI használatával
ms.date: 06/23/2020
ms.topic: quickstart
author: spelluru
ms.author: spelluru
ms.openlocfilehash: 080b089efa276779420f6d9bc8e76272f1e65788
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90069696"
---
# <a name="use-azure-cli-to-create-a-service-bus-topic-and-subscriptions-to-the-topic"></a>Az Azure CLI használata Service Bus témakör és előfizetések létrehozásához a témakörben
Ebben a rövid útmutatóban az Azure CLI használatával hozzon létre egy Service Bus témakört, majd hozzon létre előfizetéseket ehhez a témakörhöz. 

## <a name="what-are-service-bus-topics-and-subscriptions"></a>Mik azok a Service Bus-üzenettémák és -előfizetések?
A Service Bus-üzenettémák és -előfizetések *közzétételi/előfizetési* modellt biztosítanak az üzenettovábbításhoz. Üzenettémák és előfizetések használata esetén az elosztott alkalmazások összetevői nem közvetlenül egymással kommunikálnak, hanem egy közvetítőként szolgáló üzenettémakörön keresztül.

![Az üzenettémakörök alapfogalmai](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

Service Bus várólistákkal ellentétben, amelyekben az egyes üzeneteket egyetlen fogyasztó dolgozza fel, a témakörök és az előfizetések egy-a-több kommunikációt biztosítanak a közzétételi/előfizetési minta használatával. Egy üzenettémakörhöz több előfizetést is lehet regisztrálni. Ha egy üzenetet elküldenek egy témakörbe, az összes előfizetés számára elérhetővé válik, amelyek egymástól függetlenül kezelhetik és dolgozhatják fel az üzenetet. Az egyes témakörökre való előfizetés egy virtuális üzenetsorra hasonlít, amely minden, a témakörnek elküldött üzenetről kap egy másolatot. Igény szerint regisztrálhat egy témakörhöz tartozó szűrési szabályokat, amelyekkel szűrheti vagy korlátozhatja, hogy mely üzeneteket kapjanak a témakör előfizetései.

Service Bus témakörök és előfizetések lehetővé teszik nagy mennyiségű üzenet feldolgozását nagy számú felhasználó és alkalmazás között.

## <a name="prerequisites"></a>Előfeltételek
Ha nem rendelkezik Azure-előfizetéssel, akkor a Kezdés előtt létrehozhat egy [ingyenes fiókot][free account] .

Ebben a rövid útmutatóban Azure Cloud Shelleket fog használni, amelyeket a Azure Portalba való bejelentkezés után indíthat el. A Azure Cloud Shellről további részleteket a [Azure Cloud Shell áttekintése](../cloud-shell/overview.md)című témakörben talál. A Azure PowerShell a gépen is [telepítheti](/cli/azure/install-azure-cli) és használhatja. 

## <a name="create-a-service-bus-topic-and-subscriptions"></a>Service Bus-témakör és előfizetések létrehozása
Minden egyes [témakörre való előfizetés](service-bus-messaging-overview.md#topics) másolatot kaphat az összes üzenetről. A témakörök teljes protokoll és szemantika szempontjából is teljesen kompatibilisek a Service Bus-üzenetsorokkal. A Service Bus-témakörök számos különféle kiválasztási szabályt támogatnak szűrőfeltételekkel, illetve az üzenet tulajdonságait beállító vagy módosító választható műveletekkel. Minden szabályegyezéskor létrejön egy üzenet. A szabályokról, szűrőkről és műveletekről ezen a [hivatkozáson](topic-filters.md) talál további információt.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Indítsa el Azure Cloud Shell az alábbi képen látható ikon kiválasztásával. Váltson **bash** módra, ha a Cloud Shell **PowerShell** módban van. 

    :::image type="content" source="./media/service-bus-quickstart-powershell/launch-cloud-shell.png" alt-text="Cloud Shell elindítása":::
3. Futtassa az alábbi parancsot egy Azure-erőforráscsoport létrehozásához. Ha kívánja, frissítse az erőforráscsoport nevét és a helyét. 

    ```azurecli-interactive
    az group create --name MyResourceGroup --location eastus
    ```
4. A következő parancs futtatásával hozzon létre egy Service Bus üzenetküldési névteret. Frissítse a névtér nevét egyedire. 

    ```azurecli-interactive
    namespaceName=MyNameSpace$RANDOM
    az servicebus namespace create --resource-group MyResourceGroup --name $namespaceName --location eastus
    ```
5. A következő parancs futtatásával hozzon létre egy témakört a névtérben. 

    ```azurecli-interactive
    az servicebus topic create --resource-group MyResourceGroup   --namespace-name $namespaceName --name MyTopic
    ```
6. Az első előfizetés létrehozása a témakörben
    
    ```azurecli-interactive
    az servicebus topic subscription create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --name S1    
    ```
6. A második előfizetés létrehozása a témakörhöz
    
    ```azurecli-interactive
    az servicebus topic subscription create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --name S2    
    ```
6. A harmadik előfizetés létrehozása a témakörhöz
    
    ```azurecli-interactive
    az servicebus topic subscription create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --name S3    
    ```
7. Hozzon létre egy szűrőt az első előfizetéshez egy szűrővel egyéni tulajdonságok használatával (az egyik:, `StoreId` `Store1` `Store2` és `Store3` ).

    ```azurecli-interactive
    az servicebus topic subscription rule create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --subscription-name S1 --name MyFilter --filter-sql-expression "StoreId IN ('Store1','Store2','Store3')"    
    ```
8. Szűrő létrehozása a második előfizetéshez az ügyfél tulajdonságai ( `StoreId = Store4` ) használatával

    ```azurecli-interactive
    az servicebus topic subscription rule create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S2 --name MySecondFilter --filter-sql-expression "StoreId = 'Store4'"    
    ```
9. Hozzon létre egy szűrőt a harmadik előfizetésen egy szűrő használatával az ügyfél tulajdonságaival ( `StoreId` nem a `Store1` ,, `Store2` `Store3` vagy `Store4` ).

    ```azurecli-interactive
    az servicebus topic subscription rule create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --subscription-name S3 --name MyThirdFilter --filter-sql-expression "StoreId NOT IN ('Store1','Store2','Store3', 'Store4')"     
    ```
10. Futtassa a következő parancsot a névtér elsődleges kapcsolódási karakterláncának lekéréséhez. Ezt a kapcsolati karakterláncot használja a várólistához való kapcsolódáshoz, valamint az üzenetek küldéséhez és fogadásához. 

    ```azurecli-interactive
    az servicebus namespace authorization-rule keys list --resource-group MyResourceGroup --namespace-name $namespaceName --name RootManageSharedAccessKey --query primaryConnectionString --output tsv    
    ```

    Jegyezze fel a kapcsolatok sztringjét és a témakör nevét. Ezek az üzenetek küldésére és fogadására használhatók. 
    

## <a name="next-steps"></a>Következő lépések
Az alábbi cikkből megtudhatja, hogyan küldhet üzeneteket egy témakörbe, és hogyan fogadhat üzeneteket az előfizetésen keresztül: válassza ki a programozási nyelvet a TARTALOMJEGYZÉKben. 

> [!div class="nextstepaction"]
> [Üzenetek közzététele és feliratkozás rájuk](service-bus-dotnet-how-to-use-topics-subscriptions.md)


[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Install the Azure CLI]: /cli/azure/install-azure-cli
[az group create]: /cli/azure/group#az_group_create
