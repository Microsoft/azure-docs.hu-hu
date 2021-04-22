---
title: Rendszer-témakörök létrehozása, megtekintése és Azure Event Grid a CLI használatával
description: Ez a cikk bemutatja, hogyan használhatja az Azure CLI-t rendszertémaktémák létrehozására, megtekintésére és törlésére.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 34a098406762fd57dc9dc4b58fc375286f5d5b13
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874296"
---
# <a name="create-view-and-manage-event-grid-system-topics-using-azure-cli"></a>A virtuális rendszerekkel kapcsolatos Event Grid létrehozása, megtekintése és kezelése az Azure CLI használatával
Ez a cikk bemutatja, hogyan hozhat létre és kezelhet rendszertémaköreket az Azure CLI használatával. A rendszertémaktémák áttekintését lásd: [Rendszertémaktémák.](system-topics.md)

## <a name="install-extension-for-azure-cli"></a>Az Azure CLI bővítményének telepítése
Az Azure CLI-hez szüksége lesz a [Event Grid bővítményre.](/cli/azure/azure-cli-extensions-list)

A Cloud Shell:

- Ha korábban már telepítette a bővítményt, frissítse: `az extension update -n eventgrid`
- Ha korábban még nem telepítette a bővítményt, telepítse:  `az extension add -n eventgrid`

Helyi telepítés esetén:

1. [Telepítse az Azure CLI-t.](/cli/azure/install-azure-cli) Ellenőrizze, hogy a legújabb verzióval van-e, és ellenőrizze a következőt: `az --version` .
2. Távolítsa el a bővítmény korábbi verzióit: `az extension remove -n eventgrid`
3. Az EventGrid-bővítmény telepítése a következővel: `az extension add -n eventgrid`

## <a name="create-a-system-topic"></a>Rendszertémakör létrehozása

- Ahhoz, hogy először rendszertémakört hozzon létre egy Azure-forráson, majd esemény-előfizetést hozzon létre a témakörben, tekintse meg a következő referencia-témaköröket:
    - [az eventgrid system-topic create](/cli/azure/eventgrid/system-topic#az_eventgrid_system_topic_create)

        ```azurecli-interactive
        # Get the ID of the Azure source (for example: Azure Storage account)
        storageid=$(az storage account show \
                --name <AZURE STORAGE ACCOUNT NAME> \
                --resource-group <AZURE RESOURCE GROUP NAME> \
                    --query id --output tsv)
    
        # Create the system topic on the Azure source (example: Azure Storage account)
        az eventgrid system-topic create \
            -g <AZURE RESOURCE GROUP NAME> \
            --name <SPECIFY SYSTEM TOPIC NAME> \
            --location <LOCATION> \
            --topic-type microsoft.storage.storageaccounts \
            --source $storageid
        ```           

        A rendszertémaktéma létrehozásához használható értékek `topic-type` listájáért futtassa a következő parancsot. Ezek a témakörtípus-értékek a rendszer-témakörök létrehozását támogató eseményforrásokat képviselik. Hagyja figyelmen kívül a `Microsoft.EventGrid.Topics` `Microsoft.EventGrid.Domains` listából a és a et. 

        ```azurecli-interactive
        az eventgrid topic-type  list --output json | grep -w id
        ```
    - [az eventgrid system-topic event-subscription create](/cli/azure/eventgrid/system-topic/event-subscription#az_eventgrid_system_topic_event-subscription-create)

        ```azurecli-interactive
        az eventgrid system-topic event-subscription create --name <SPECIFY EVENT SUBSCRIPTION NAME> \
            -g rg1 --system-topic-name <SYSTEM TOPIC NAME> \
            --endpoint <ENDPOINT URL>         
        ```
- Rendszertémakör (implicit) létrehozásához egy Azure-forrás esemény-előfizetésének létrehozásakor használja [az az eventgrid event-subscription create metódust.](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_create) Bemutatunk egy példát:
    
    ```azurecli-interactive
    storageid=$(az storage account show --name <AZURE STORAGE ACCOUNT NAME> --resource-group <AZURE RESOURCE GROUP NAME> --query id --output tsv)
    endpoint=<ENDPOINT URL>

    az eventgrid event-subscription create \
      --source-resource-id $storageid \
      --name <EVENT SUBSCRIPTION NAME> \
      --endpoint $endpoint
    ```
    A részletes útmutatásokat bemutató oktatóanyagért lásd: [Feliratkozás tárfiókra.](../storage/blobs/storage-blob-event-quickstart.md?toc=%2Fazure%2Fevent-grid%2Ftoc.json#subscribe-to-your-storage-account)

## <a name="view-all-system-topics"></a>Az összes megtekintése témakörei
A rendszer összes témakörének és egy kiválasztott rendszertéma részleteinek megtekintéséhez használja a következő parancsokat:

- [az eventgrid system-topic list](/cli/azure/eventgrid/system-topic#az_eventgrid_system_topic_list)

    ```azurecli-interactive
    az eventgrid system-topic list   
     ```
- [az eventgrid system-topic show](/cli/azure/eventgrid/system-topic#az_eventgrid_system_topic_show)

    ```azurecli-interactive
    az eventgrid system-topic show -g <AZURE RESOURCE GROUP NAME> -n <SYSTEM TOPIC NAME>     
     ```

## <a name="delete-a-system-topic"></a>Rendszertémaktéma törlése
Rendszertémaktéma törléséhez használja a következő parancsot: 

- [az eventgrid system-topic delete](/cli/azure/eventgrid/system-topic#az_eventgrid_system_topic_delete)

    ```azurecli-interactive
    az eventgrid system-topic delete -g <AZURE RESOURCE GROUP NAME> --name <SYSTEM TOPIC NAME>   
     ```

## <a name="next-steps"></a>Következő lépések
A [Azure Event Grid](system-topics.md) által támogatott rendszertémakémákról és témakörtípusokról további információt az Azure Event Grid. 