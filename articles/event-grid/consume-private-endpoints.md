---
title: Események kézbesítése a Private link Service használatával
description: Ez a cikk azt ismerteti, hogyan lehet megkerülni a korlátozást, hogy az események ne legyenek kézbesítve a Private link Service használatával.
ms.topic: how-to
ms.date: 02/12/2021
ms.openlocfilehash: 75ae8ff2c341048c39156e43da3615788e9d0f0a
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309431"
---
# <a name="deliver-events-using-private-link-service"></a>Események kézbesítése a Private link Service használatával
Jelenleg nem lehet eseményeket kézbesíteni [privát végpontok](../private-link/private-endpoint-overview.md)használatával. Ez azt eredményezi, hogy nincs támogatás, ha szigorú hálózati elkülönítési követelmények vannak, amelyekben a továbbított események forgalma nem hagyhatja el a magánhálózati IP-területet. 

## <a name="use-managed-identity"></a>Felügyelt identitás használata
Ha azonban a követelmények biztonságos módon küldik el az eseményeket egy titkosított csatornán keresztül, és a küldő (ebben az esetben Event Grid) nyilvános IP-cím használatával történő küldésének ismert identitását, akkor az eseményeket az Azure Event Grid egyéni témaköre vagy egy rendszer által felügyelt identitással rendelkező tartomány használatával Event Hubs, Service Bus vagy Azure Storage szolgáltatásba kézbesítheti. Az események felügyelt identitással történő kézbesítésével kapcsolatos részletekért lásd: [esemény kézbesítése felügyelt identitás használatával](managed-service-identity.md). 

Ezt követően használhat egy Azure Functions vagy a virtuális hálózaton üzembe helyezett webhookot az események lekéréséhez. Lásd a következő mintát: [Kapcsolódás privát végpontokhoz Azure functions használatával](/samples/azure-samples/azure-functions-private-endpoints/connect-to-private-endpoints-with-azure-functions/).


:::image type="content" source="./media/consume-private-endpoints/deliver-private-link-service.svg" alt-text="Kézbesítés Private link Service használatával":::


Ebben a konfigurációban a forgalom a nyilvános IP-cím/Internet Event Gridról Event Hubsra, Service Busra vagy az Azure Storage-ba kerül, de a csatorna titkosítható, és a Event Grid felügyelt identitása is használatos. Ha a virtuális hálózatra telepített Azure Functions vagy webhookot úgy konfigurálja, hogy Event Hubs, Service Bus vagy Azure Storage-t használ privát kapcsolaton keresztül, akkor a forgalom ezen szakasza nyilvánvalóan az Azure-ban marad.

## <a name="deliver-events-to-event-hubs-using-managed-identity"></a>Események továbbítása Event Hubs felügyelt identitás használatával
Ha a Event Hubs névtérben lévő esemény-központhoz felügyelt identitás használatával szeretne eseményeket küldeni, kövesse az alábbi lépéseket:

1. Rendszer által hozzárendelt identitás engedélyezése: [rendszertémakörök](enable-identity-system-topics.md), [Egyéni témakörök és tartományok](enable-identity-custom-topics-domains.md).  
1. [Adja hozzá az identitást az **Azure Event Hubs adatfeladói** szerepkörhöz a Event Hubs névtérben](../event-hubs/authenticate-managed-identity.md#to-assign-azure-roles-using-the-azure-portal).
1. [Engedélyezze a **megbízható Microsoft-szolgáltatások számára, hogy megkerüljék ezt a tűzfal-** beállítást a Event Hubs névtérben](../event-hubs/event-hubs-service-endpoints.md#trusted-microsoft-services). 
1. [Konfigurálja azt az esemény-előfizetést](managed-service-identity.md#create-event-subscriptions-that-use-an-identity) , amely az Event hub-t használja végpontként a rendszer által hozzárendelt identitás használatára.

## <a name="deliver-events-to-service-bus-using-managed-identity"></a>Események továbbítása Service Bus felügyelt identitás használatával
Az alábbi lépéseket követve továbbíthatja az eseményeket Service Bus várólistákba vagy témakörökbe Service Bus névterében a felügyelt identitás használatával:

1. Rendszer által hozzárendelt identitás engedélyezése: [rendszertémakörök](enable-identity-system-topics.md), [Egyéni témakörök és tartományok](enable-identity-custom-topics-domains.md). 
1. [Az identitás hozzáadása a **Azure Service Bus adatfeladói**](../service-bus-messaging/service-bus-managed-service-identity.md#azure-built-in-roles-for-azure-service-bus) szerepkörhöz a Service Bus névtérben
1. [Engedélyezze a **megbízható Microsoft-szolgáltatások számára, hogy megkerüljék ezt a tűzfal-** beállítást a Service Bus névtérben](../service-bus-messaging/service-bus-service-endpoints.md#trusted-microsoft-services). 
1. Konfigurálja az Service Bus üzenetsor vagy témakört használó [esemény-előfizetést](managed-service-identity.md) végpontként a rendszer által hozzárendelt identitás használatára.

## <a name="deliver-events-to-storage"></a>Események kézbesítése a tárolóba 
Ha felügyelt identitással szeretne eseményeket kézbesíteni a tárolási várólistákba, kövesse az alábbi lépéseket:

1. Rendszer által hozzárendelt identitás engedélyezése: [rendszertémakörök](enable-identity-system-topics.md), [Egyéni témakörök és tartományok](enable-identity-custom-topics-domains.md). 
1. [Adja hozzá az identitást a **Storage üzenetsor adatüzenet-küldő**](../storage/common/storage-auth-aad-rbac-portal.md) szerepköréhez az Azure Storage-várólistán.
1. Konfigurálja az Service Bus üzenetsor vagy témakört használó [esemény-előfizetést](managed-service-identity.md#create-event-subscriptions-that-use-an-identity) végpontként a rendszer által hozzárendelt identitás használatára.


## <a name="next-steps"></a>Következő lépések
Az események felügyelt identitással történő kézbesítésével kapcsolatos további információkért lásd: [esemény kézbesítése felügyelt identitás használatával](managed-service-identity.md). 
