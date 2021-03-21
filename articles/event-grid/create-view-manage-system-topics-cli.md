---
title: Azure Event Grid rendszertémakörök létrehozása, megtekintése és kezelése a parancssori felület használatával
description: Ez a cikk bemutatja, hogyan hozhat létre, tekinthet meg és törölhet rendszertémaköröket az Azure CLI használatával.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: c1c847c7f25e3a656b798e186a408e560b9ee9e6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98633222"
---
# <a name="create-view-and-manage-event-grid-system-topics-using-azure-cli"></a>Event Grid rendszertémakörök létrehozása, megtekintése és kezelése az Azure CLI használatával
Ebből a cikkből megtudhatja, hogyan hozhat létre és kezelhet rendszertémaköröket az Azure CLI használatával. A rendszertémakörök áttekintését itt találja: [rendszertémakörök](system-topics.md).

## <a name="install-extension-for-azure-cli"></a>Az Azure CLI-bővítmény telepítése
Az Azure CLI esetén a [Event Grid bővítményre](/cli/azure/azure-cli-extensions-list)van szükség.

Cloud Shell:

- Ha korábban már telepítette a bővítményt, frissítse a következőket: `az extension update -n eventgrid`
- Ha korábban még nem telepítette a bővítményt, telepítse a következőket:  `az extension add -n eventgrid`

Helyi telepítés esetén:

1. [Telepítse az Azure CLI](/cli/azure/install-azure-cli)-t. Ellenőrizze, hogy rendelkezik-e a legújabb verzióval a-nal való ellenőrzéssel `az --version` .
2. Távolítsa el a bővítmény korábbi verzióit: `az extension remove -n eventgrid`
3. Telepítse a eventgrid bővítményt a `az extension add -n eventgrid`

## <a name="create-a-system-topic"></a>Rendszerbeli témakör létrehozása

- Először hozzon létre egy rendszertémakört egy Azure-forráson, majd hozzon létre egy esemény-előfizetést az adott témakörhöz a következő témakörökben talál:
    - [az eventgrid System-topic Create](/cli/azure/ext/eventgrid/eventgrid/system-topic#ext-eventgrid-az-eventgrid-system-topic-create)

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

        A `topic-type` rendszertémakör létrehozásához használható értékek listáját a következő parancs futtatásával végezheti el. Ezek a témakör típusú értékek a rendszertémakörök létrehozását támogató eseményforráskat jelölik. Kérjük, hagyja figyelmen kívül `Microsoft.EventGrid.Topics` , és `Microsoft.EventGrid.Domains` a listából. 

        ```azurecli-interactive
        az eventgrid topic-type  list --output json | grep -w id
        ```
    - [az eventgrid System-topic Event-előfizetés Create](/cli/azure/ext/eventgrid/eventgrid/system-topic/event-subscription#ext-eventgrid-az-eventgrid-system-topic-event-subscription-create)

        ```azurecli-interactive
        az eventgrid system-topic event-subscription create --name <SPECIFY EVENT SUBSCRIPTION NAME> \
            -g rg1 --system-topic-name <SYSTEM TOPIC NAME> \
            --endpoint <ENDPOINT URL>         
        ```
- Ha egy Azure-forrásra vonatkozó esemény-előfizetés létrehozásakor szeretne létrehozni egy rendszertémakört (implicit módon), használja az az [eventgrid Event-előfizetés Create](/cli/azure/ext/eventgrid/eventgrid/event-subscription#ext-eventgrid-az-eventgrid-event-subscription-create) metódust. Bemutatunk egy példát:
    
    ```azurecli-interactive
    storageid=$(az storage account show --name <AZURE STORAGE ACCOUNT NAME> --resource-group <AZURE RESOURCE GROUP NAME> --query id --output tsv)
    endpoint=<ENDPOINT URL>

    az eventgrid event-subscription create \
      --source-resource-id $storageid \
      --name <EVENT SUBSCRIPTION NAME> \
      --endpoint $endpoint
    ```
    Részletes útmutatást tartalmazó oktatóanyagért lásd: [előfizetés a Storage-fiókra](../storage/blobs/storage-blob-event-quickstart.md?toc=%2Fazure%2Fevent-grid%2Ftoc.json#subscribe-to-your-storage-account).

## <a name="view-all-system-topics"></a>Az összes rendszertémakör megtekintése
A következő parancsokkal megtekintheti az összes rendszertémakört és a kiválasztott rendszer részleteit:

- [az eventgrid System-topic List](/cli/azure/ext/eventgrid/eventgrid/system-topic#ext-eventgrid-az-eventgrid-system-topic-list)

    ```azurecli-interactive
    az eventgrid system-topic list   
     ```
- [az eventgrid System-topic show](/cli/azure/ext/eventgrid/eventgrid/system-topic#ext-eventgrid-az-eventgrid-system-topic-show)

    ```azurecli-interactive
    az eventgrid system-topic show -g <AZURE RESOURCE GROUP NAME> -n <SYSTEM TOPIC NAME>     
     ```

## <a name="delete-a-system-topic"></a>Rendszer-témakör törlése
Egy rendszertémakör törléséhez használja a következő parancsot: 

- [az eventgrid System-topic delete](/cli/azure/ext/eventgrid/eventgrid/system-topic#ext-eventgrid-az-eventgrid-system-topic-delete)

    ```azurecli-interactive
    az eventgrid system-topic delete -g <AZURE RESOURCE GROUP NAME> --name <SYSTEM TOPIC NAME>   
     ```

## <a name="next-steps"></a>Következő lépések
A Azure Event Grid által támogatott rendszertémakörökkel és a témakörökkel kapcsolatos további tudnivalókért tekintse meg [Azure Event Grid szakasz rendszertémaköreit](system-topics.md) . 