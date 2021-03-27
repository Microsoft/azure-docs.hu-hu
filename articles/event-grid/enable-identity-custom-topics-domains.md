---
title: Felügyelt identitás engedélyezése Azure Event Grid egyéni témakörökben és tartományokban
description: Ez a cikk azt ismerteti, hogyan engedélyezhető a felügyelt szolgáltatás identitása egy Azure Event Grid egyéni témakörhöz vagy tartományhoz.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 06fd4d6e472b33496e773596b0f3afc8e70be948
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630425"
---
# <a name="assign-a-system-managed-identity-to-an-event-grid-custom-topic-or-domain"></a>Rendszer által felügyelt identitás társítása Event Grid egyéni témakörhöz vagy tartományhoz 
Ez a cikk bemutatja, hogyan engedélyezheti a rendszer által felügyelt identitást egy Event Grid egyéni témakörhöz vagy tartományhoz. A felügyelt identitások megismeréséhez tekintse meg a [Mi az Azure-erőforrások felügyelt identitásai](../active-directory/managed-identities-azure-resources/overview.md)című témakört.

## <a name="enable-identity-at-the-time-of-creation"></a>Identitás engedélyezése a létrehozáskor

### <a name="using-azure-portal"></a>Az Azure Portal használata
Engedélyezheti a rendszerhez rendelt identitást egy egyéni témakörhöz vagy egy tartományhoz, miközben létrehozza azt a Azure Portalban. Az alábbi képen bemutatjuk, hogyan engedélyezhető a rendszer által felügyelt identitás egy egyéni témakörhöz. Alapvetően a létrehozási varázsló **speciális** lapján jelölje be a **rendszerhez rendelt identitás engedélyezése** jelölőnégyzetet. Ezt a lehetőséget a tartomány-létrehozási varázsló **speciális** oldalán fogja látni. 

![Identitás engedélyezése egyéni témakör létrehozásakor](./media/managed-service-identity/create-topic-identity.png)

### <a name="using-azure-cli"></a>Az Azure parancssori felület használata
Az Azure CLI-vel egyéni témakört vagy tartományt is létrehozhat egy rendszer által hozzárendelt identitással. Használja a parancsot a következőhöz `az eventgrid topic create` `--identity` beállított paraméterrel: `systemassigned` . Ha nem ad meg értéket ehhez a paraméterhez, a rendszer az alapértelmezett értéket `noidentity` használja. 

```azurecli-interactive
# create a custom topic with a system-assigned identity
az eventgrid topic create -g <RESOURCE GROUP NAME> --name <TOPIC NAME> -l <LOCATION>  --identity systemassigned
```

Hasonlóképpen a `az eventgrid domain create` paranccsal is létrehozható egy rendszer által felügyelt identitással rendelkező tartomány.

## <a name="enable-identity-for-an-existing-custom-topic-or-domain"></a>Identitás engedélyezése meglévő egyéni témakörhöz vagy tartományhoz
Ebből a szakaszból megtudhatja, hogyan engedélyezheti a rendszer által felügyelt identitásokat egy meglévő egyéni témakörhöz vagy tartományhoz. 

### <a name="using-azure-portal"></a>Az Azure Portal használata
Az alábbi eljárás bemutatja, hogyan engedélyezheti a rendszer által felügyelt identitást egy egyéni témakörhöz. A tartomány identitásának engedélyezésének lépései hasonlóak. 

1. Nyissa meg az [Azure Portal](https://portal.azure.com).
2. Keresse meg az **Event Grid-témaköröket** a felül található keresési sávon.
3. Válassza ki azt az **Egyéni témakört** , amelyhez engedélyezni kívánja a felügyelt identitást. 
4. Váltson az **Identity (identitás** ) lapra. 
5. Kapcsolja **be a** kapcsolót az identitás engedélyezéséhez. 
1. A beállítás mentéséhez válassza az eszköztár **Mentés** elemét. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-topic.png" alt-text="Egyéni témakör Identity lapja"::: 

Az Event Grid-tartomány identitásának engedélyezéséhez hasonló lépések használhatók.

### <a name="use-the-azure-cli"></a>Az Azure parancssori felületének használata
Használja az parancsot a következőre `az eventgrid topic update` `--identity` : beállítással `systemassigned` engedélyezheti a rendszerhez rendelt identitást egy meglévő egyéni témakörhöz. Ha le szeretné tiltani az identitást, állítsa be `noidentity` értékként. 

```azurecli-interactive
# Update the topic to assign a system-assigned identity. 
az eventgrid topic update -g $rg --name $topicname --identity systemassigned --sku basic 
```

Egy meglévő tartomány frissítésére szolgáló parancs hasonló ( `az eventgrid domain update` ).


## <a name="next-steps"></a>Következő lépések
Adja hozzá az identitást egy megfelelő szerepkörhöz (például Service Bus adatfeladóhoz) a célhelyen (például egy Service Bus üzenetsor). A részletes lépésekért lásd: [identitás hozzáadása az Azure-szerepkörökhöz a célhelyeken](add-identity-roles.md). 
