---
title: A kapcsolatok karakterláncának beolvasása – Azure Event Hubs | Microsoft Docs
description: Ez a cikk útmutatást nyújt olyan kapcsolati karakterlánc beszerzéséhez, amelyet az ügyfelek használhatnak az Azure-Event Hubshoz való kapcsolódáshoz.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 271d4dcc1f2fab08dc7f625ce36e021eda225b87
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/24/2020
ms.locfileid: "85320493"
---
# <a name="get-an-event-hubs-connection-string"></a>Event Hubs-kapcsolatok karakterláncának beolvasása

A Event Hubs használatához létre kell hoznia egy Event Hubs névteret. A névtér egy több Event hub-vagy Kafka-témakör hatókör-tárolója. Ez a névtér egyedi [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)-t biztosít. Miután létrehozta a névteret, beolvashatja a Event Hubssal való kommunikációhoz szükséges kapcsolódási karakterláncot.

Az Azure Event Hubshoz tartozó kapcsolatok karakterlánca a következő, beágyazott összetevőket tartalmaz:

* FQDN = a létrehozott EventHubs-névtér teljes tartományneve (tartalmazza a EventHubs-névtér nevét, majd a servicebus.windows.net-t)
* SharedAccessKeyName = az alkalmazás SAS-kulcsaihoz választott név
* SharedAccessKey = a kulcs generált értéke.

A kapcsolatok karakterlánc-sablonja úgy néz ki, mint
```
Endpoint=sb://<FQDN>/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>
```

Például a következőhöz hasonló kapcsolatok karakterlánca lehet:`Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=`

Ez a cikk végigvezeti a kapcsolatok sztring beszerzésének különböző módjain.

## <a name="get-connection-string-from-the-portal"></a>A portálon keresztüli kapcsolatok karakterláncának beolvasása
1. Jelentkezzen be az [Azure portálra](https://portal.azure.com). 
2. A bal oldali navigációs menüben válassza a **minden szolgáltatás** lehetőséget. 
3. Az **elemzés** szakaszban válassza a **Event Hubs** lehetőséget. 
4. Az Event hubok listájában válassza ki az Event hub elemet.
6. A **Event Hubs névtér** lapon válassza a bal oldali menüben a **megosztott elérési házirendek** elemet.

    ![Közös hozzáférésű házirendek menüelem](./media/event-hubs-get-connection-string/event-hubs-get-connection-string1.png)
7. Válasszon ki egy **megosztott hozzáférési házirendet** a szabályzatok listájában. Az alapértelmezett név: **RootManageSharedAccessPolicy**. Hozzáadhat egy szabályzatot a megfelelő engedélyekkel (olvasás, írás), és használhatja a szabályzatot. 

    ![Event Hubs megosztott hozzáférési házirendek](./media/event-hubs-get-connection-string/event-hubs-get-connection-string2.png)
8. Válassza a **kapcsolódási karakterlánc – elsődleges kulcs** mező melletti **Másolás** gombot. 

    ![Event Hubs – a kapcsolatok karakterláncának beolvasása](./media/event-hubs-get-connection-string/event-hubs-get-connection-string3.png)

## <a name="getting-the-connection-string-with-azure-powershell"></a>A Azure PowerShell a kapcsolatok karakterláncának beolvasása

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A [Get-AzEventHubKey](/powershell/module/az.eventhub/get-azeventhubkey) használatával lekérheti az adott házirend/szabály neve számára a kapcsolódási karakterláncot az alább látható módon:

```azurepowershell-interactive
Get-AzEventHubKey -ResourceGroupName dummyresourcegroup -NamespaceName dummynamespace -AuthorizationRuleName RootManageSharedAccessKey
```

## <a name="getting-the-connection-string-with-azure-cli"></a>A kapcsolatok karakterláncának beolvasása az Azure CLI-vel
A következő paranccsal kérheti le a névtérhez tartozó kapcsolódási karakterláncot:

```azurecli-interactive
az eventhubs namespace authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --name RootManageSharedAccessKey
```

Vagy az alábbi paranccsal kérheti le a EventHub entitáshoz tartozó kapcsolódási karakterláncot:

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --eventhub-name dummyeventhub --name RootManageSharedAccessKey
```

Az Event Hubs Azure CLI-parancsaival kapcsolatos további információkért lásd: [Az Azure CLI a Event Hubshoz](/cli/azure/eventhubs).

## <a name="next-steps"></a>További lépések

Az alábbi webhelyeken további információt talál az Event Hubsról:

* [Event Hubs áttekintése](event-hubs-what-is-event-hubs.md)
* [Event hub létrehozása](event-hubs-create.md)
