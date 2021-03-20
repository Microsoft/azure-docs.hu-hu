---
title: Azure Active Directoryval való hozzáférés engedélyezése
description: Ez a cikk a Azure Active Directory használatával történő Event Hubs erőforrásokhoz való hozzáférés engedélyezésére vonatkozó információkat tartalmaz.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: d794b03fdbb5429983788c74cbb05a7c13bf2d76
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92910797"
---
# <a name="authorize-access-to-event-hubs-resources-using-azure-active-directory"></a>Hozzáférés engedélyezése Event Hubs erőforrásokhoz a Azure Active Directory használatával
Az Azure Event Hubs támogatja a Azure Active Directory (Azure AD) használatát, hogy engedélyezze a kérelmeket Event Hubs erőforrásoknak. Az Azure AD-vel az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) segítségével engedélyeket biztosíthat egy rendszerbiztonsági tag számára, amely lehet egy felhasználó vagy egy egyszerű alkalmazás. További információ a szerepkörökről és a szerepkör-hozzárendelésekről: [a különböző szerepkörök megismerése](../role-based-access-control/overview.md).

## <a name="overview"></a>Áttekintés
Ha egy rendszerbiztonsági tag (felhasználó vagy alkalmazás) megpróbál hozzáférni egy Event Hubs erőforráshoz, a kérést engedélyezni kell. Az Azure AD-vel az erőforrásokhoz való hozzáférés kétlépéses folyamat. 

 1. Először a rendszerbiztonsági tag identitása hitelesítve van, és a rendszer egy OAuth 2,0 tokent ad vissza. A tokent kérő erőforrás neve `https://eventhubs.azure.net/` , és az összes felhő/bérlő esetében azonos. A Kafka-ügyfelek esetében a jogkivonat igénylésére szolgáló erőforrás `https://<namespace>.servicebus.windows.net` .
 1. Ezután a jogkivonat a Event Hubs szolgáltatásnak küldött kérelem részeként a megadott erőforráshoz való hozzáférés engedélyezéséhez lesz átadva.

A hitelesítési lépés megköveteli, hogy egy alkalmazás-kérelem OAuth 2,0 hozzáférési jogkivonatot tartalmazzon futásidőben. Ha egy alkalmazás egy Azure-entitáson, például egy Azure-beli virtuális gépen, egy virtuálisgép-méretezési csoporton vagy egy Azure Function-alkalmazáson belül fut, akkor a felügyelt identitás használatával férhet hozzá az erőforrásokhoz. Ha meg szeretné tudni, hogyan hitelesítheti a felügyelt identitások által küldött kéréseket Event Hubs szolgáltatásra, tekintse meg az Azure [Event Hubs-erőforrások hozzáférésének hitelesítése Azure Active Directory és felügyelt identitások Azure-erőforrásokhoz](authenticate-managed-identity.md)című témakört. 

Az engedélyezési lépés megköveteli, hogy egy vagy több Azure-szerepkört hozzá lehessen rendelni a rendszerbiztonsági tag számára. Az Azure Event Hubs olyan Azure-szerepköröket biztosít, amelyek a Event Hubs erőforrásokra vonatkozó engedélyek készleteit foglalják magukban. A rendszerbiztonsági tag számára hozzárendelt szerepkörök határozzák meg, hogy a résztvevő milyen engedélyeket fog tartalmazni. Az Azure-szerepkörökkel kapcsolatos további információkért lásd: [Azure-beli beépített szerepkörök az azure Event Hubshoz](#azure-built-in-roles-for-azure-event-hubs). 

A Event Hubsra irányuló kérelmeket használó natív alkalmazások és webalkalmazások is engedélyezhetik az Azure AD-t. Ha szeretné megtudni, hogyan kérhet hozzáférési jogkivonatot, és hogyan engedélyezheti a kérelmeket Event Hubs erőforrásokhoz, tekintse meg az [azure Event Hubs hozzáférésének hitelesítése az Azure ad-vel az alkalmazásokból](authenticate-application.md)című témakört. 

## <a name="assign-azure-roles-for-access-rights"></a>Azure-szerepkörök kiosztása hozzáférési jogosultságokhoz
Azure Active Directory (Azure AD) az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/overview.md)segítségével engedélyezi a hozzáférési jogokat a biztonságos erőforrásokhoz. Az Azure Event Hubs az Azure beépített szerepköreinek készletét határozza meg, amelyek az Event hub-adatok eléréséhez használt általános engedélyeket foglalják magukban, és egyéni szerepköröket is meghatározhat az adatok eléréséhez.

Ha az Azure-szerepköröket egy Azure AD-rendszerbiztonsági tag számára rendeli hozzá, az Azure hozzáférést biztosít az adott rendszerbiztonsági tag erőforrásaihoz. A hozzáférés hatóköre az előfizetés, az erőforráscsoport, a Event Hubs névtér vagy az alatta lévő erőforrás szintjére is kiterjed. Az Azure AD rendszerbiztonsági tag lehet egy felhasználó vagy egy egyszerű alkalmazás vagy egy [felügyelt identitás az Azure-erőforrásokhoz](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="azure-built-in-roles-for-azure-event-hubs"></a>Azure-beli beépített szerepkörök az Azure Event Hubs
Az Azure az Azure AD-vel és a OAuth-vel való hozzáférés engedélyezéséhez a következő beépített Azure-szerepköröket biztosítja a Event Hubs adataihoz:

| Szerepkör | Leírás | 
| ---- | ----------- | 
| [Az Azure Event Hubs adattulajdonos](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner) | Ezzel a szerepkörrel teljes hozzáférést biztosíthat Event Hubs erőforrásaihoz. |
| [Azure Event Hubs adatfeladó](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender) | Ezzel a szerepkörrel biztosíthatja a küldési hozzáférést Event Hubs erőforrásaihoz. |
| [Azure Event Hubs adatfogadó](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver) | Ennek a szerepkörnek a használatával biztosíthatja a Event Hubs erőforrásainak felhasználását/fogadását. |

A séma-beállításjegyzék beépített szerepköreivel kapcsolatban lásd: [séma beállításjegyzékbeli szerepkörei](schema-registry-overview.md#azure-role-based-access-control).

## <a name="resource-scope"></a>Erőforrás hatóköre 
Mielőtt Azure-szerepkört rendeljen egy rendszerbiztonsági tag számára, határozza meg a rendszerbiztonsági tag hozzáférési hatókörét. Az ajánlott eljárások azt diktálják, hogy mindig csak a lehető legszűkebb hatókört adja meg.

Az alábbi lista azokat a szinteket ismerteti, amelyekkel a Event Hubs erőforrásaihoz férhet hozzá, a legszűkebb hatókörtől kezdve:

- **Fogyasztói csoport**: ezen a hatókörön a szerepkör-hozzárendelés csak erre az entitásra vonatkozik. A Azure Portal jelenleg nem támogatja az Azure-szerepkörök egy rendszerbiztonsági tag számára való hozzárendelését ezen a szinten. 
- **Event hub**: a szerepkör-hozzárendelés az Event hub-entitásra és az alatta lévő fogyasztói csoportra vonatkozik.
- **Névtér**: a szerepkör-hozzárendelés a névtér és a hozzá társított fogyasztói csoport teljes topológiáját Event Hubs.
- **Erőforráscsoport**: a szerepkör-hozzárendelés az erőforráscsoport összes Event Hubs erőforrására vonatkozik.
- **Előfizetés**: a szerepkör-hozzárendelés az előfizetés összes erőforrás-csoportjának összes Event Hubs erőforrására vonatkozik.

> [!NOTE]
> - Ne feledje, hogy az Azure-szerepkör-hozzárendelések akár öt percet is igénybe vehetnek. 
> - Ez a tartalom a Apache Kafka Event Hubs és Event Hubs is vonatkozik. A Kafka-támogatás Event Hubsáról további információt a [Event Hubs a Kafka-biztonság és hitelesítés](event-hubs-for-kafka-ecosystem-overview.md#security-and-authentication)című témakörben talál.


További információ a beépített szerepkörök meghatározásáról: a [szerepkör-definíciók ismertetése](../role-based-access-control/role-definitions.md#management-and-data-operations). Az egyéni Azure-szerepkörök létrehozásával kapcsolatos információkért lásd: [Azure egyéni szerepkörök](../role-based-access-control/custom-roles.md).



## <a name="samples"></a>Példák
- [Microsoft. Azure. EventHubs-minták](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    Ezek a minták a régi **Microsoft. Azure. EventHubs** könyvtárat használják, de egyszerűen frissítheti a legújabb **Azure. Messaging. EventHubs** könyvtár használatával. Ha át szeretné helyezni a mintát a régi könyvtárból az új verzióra, tekintse meg a [Microsoft. Azure. EventHubs-ről az Azure. Messaging. EventHubs-re való Migrálás útmutatóját](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md).
- [Azure. Messaging. EventHubs-minták](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    Ez a minta frissítve lett a legújabb **Azure. Messaging. EventHubs** könyvtár használatára.
- [Event Hubs a Kafka-OAuth mintákhoz](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth). 


## <a name="next-steps"></a>Következő lépések
- Ismerje meg, hogyan rendeljen hozzá egy Azure-beli beépített szerepkört egy rendszerbiztonsági tag számára: [Event Hubs erőforrásokhoz való hozzáférés hitelesítése Azure Active Directory használatával](authenticate-application.md).
- Ismerje meg [, hogyan hozhat létre egyéni szerepköröket az Azure RBAC használatával](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/CustomRole).
- Ismerje meg [, hogyan használhatja a Azure Active Directoryt az Eh használatával](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/AzureEventHubsSDK)

Tekintse meg a következő kapcsolódó cikkeket:

- [Kérelmek hitelesítése az Azure Event Hubs alkalmazásból Azure Active Directory használatával](authenticate-application.md)
- [Felügyelt identitás hitelesítése Azure Active Directory használatával Event Hubs erőforrások eléréséhez](authenticate-managed-identity.md)
- [Kérelmek hitelesítése az Azure Event Hubs megosztott hozzáférési aláírások használatával](authenticate-shared-access-signature.md)
- [Hozzáférés engedélyezése Event Hubs erőforrásokhoz közös hozzáférési aláírások használatával](authorize-access-shared-access-signature.md)
