---
title: Felügyelt identitás engedélyezése Azure Event Grid rendszer témakörében
description: Ez a cikk azt ismerteti, hogyan engedélyezhető a felügyelt szolgáltatás identitása egy Azure Event Grid rendszer témakörben.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: d8219cf9cba4ce0a4fb5ddbcf695f1ec72de36fe
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280513"
---
# <a name="assign-a-system-managed-identity-to-an-event-grid-system-topic"></a>Rendszer által felügyelt identitás társítása Event Grid rendszerbeli témakörhöz
Ebből a cikkből megtudhatja, hogyan engedélyezheti a rendszer által felügyelt identitást egy meglévő Event Grid rendszer-témakörhöz. A felügyelt identitások megismeréséhez tekintse meg a [Mi az Azure-erőforrások felügyelt identitásai](../active-directory/managed-identities-azure-resources/overview.md)című témakört.  

> [!IMPORTANT]
> Jelenleg nem engedélyezheti a rendszer által felügyelt identitást új rendszertémakör létrehozásakor, azaz olyan esemény-előfizetés létrehozásakor egy Azure-erőforrásra, amely támogatja a rendszertémaköröket. 


## <a name="use-azure-portal"></a>Az Azure Portal használata
A következő eljárás azt mutatja be, hogyan engedélyezheti a rendszer által felügyelt identitást egy rendszertémakörben. 

1. Nyissa meg az [Azure Portal](https://portal.azure.com).
2. Keresse meg az **Event Grid rendszertémaköröket** a felül található keresési sávon.
3. Válassza ki azt a **rendszertémakört** , amelyhez engedélyezni kívánja a felügyelt identitást. 
4. Válassza a bal oldali menü **identitás** elemét. Ez a beállítás nem jelenik meg a globális helyen található rendszertémakörben. 
5. Kapcsolja **be a** kapcsolót az identitás engedélyezéséhez. 
1. A beállítás mentéséhez válassza az eszköztár **Mentés** elemét. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic.png" alt-text="A rendszertémakör Identity lapja"::: 
1. Válassza az **Igen** lehetőséget a megerősítő üzenetben. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic-confirmation.png" alt-text="Identitás társítása rendszertémakörhöz – megerősítés"::: 
1. Ellenőrizze, hogy megjelenik-e a rendszer által hozzárendelt felügyelt identitás objektum-azonosítója, és tekintse meg a szerepkörök hozzárendelésére szolgáló hivatkozást. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic-completed.png" alt-text="Identitás társítása egy rendszertémakörhöz – befejezett"::: 

## <a name="global-azure-sources"></a>Globális Azure-források
A rendszer által felügyelt identitást csak a regionális Azure-erőforrások számára engedélyezheti. A globális Azure-erőforrásokhoz (például Azure-előfizetésekhez, erőforráscsoportokhöz vagy Azure Maps) kapcsolódó rendszertémakörökhöz nem engedélyezhető. Ezeknek a globális forrásoknak a rendszertémakörei szintén nincsenek társítva egy adott régióhoz. Nem jelenik meg a rendszertémakör **Identity** lapja, amelynek a helye **globális** értékre van állítva. 

:::image type="content" source="./media/managed-service-identity/system-topic-location-global.png" alt-text="Rendszertémakör a globálisan beállított hellyel"::: 



## <a name="next-steps"></a>Következő lépések
Adja hozzá az identitást egy megfelelő szerepkörhöz (például Service Bus adatfeladóhoz) a célhelyen (például egy Service Bus üzenetsor). A részletes lépésekért lásd: [a felügyelt identitás engedélyezése a Event Grid célhoz való hozzáféréshez](add-identity-roles.md). 