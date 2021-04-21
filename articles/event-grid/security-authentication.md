---
title: Eseménykezelőknek történő eseményk kézbesítésének hitelesítése (Azure Event Grid)
description: Ez a cikk az eseménykezelőknek történő kézbesítés hitelesítésének különböző módjait ismerteti a Azure Event Grid.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 7db258ee152e4b1c46362e74e0246b80513ca9f2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777256"
---
# <a name="authenticate-event-delivery-to-event-handlers-azure-event-grid"></a>Eseménykezelőknek történő eseményk kézbesítésének hitelesítése (Azure Event Grid)
Ez a cikk az eseménykezelőknek kézbesítő események hitelesítésére vonatkozó információkat tartalmaz. Azt is bemutatja, hogyan biztosíthatja az események fogadására használt webhook-végpontokat Event Grid (Azure AD) Azure Active Directory (Azure AD) vagy egy közös titkos okkal.

## <a name="use-system-assigned-identities-for-event-delivery"></a>Rendszer által hozzárendelt identitások használata események kézbesítéséhez
Engedélyezheti a rendszer által hozzárendelt felügyelt identitást egy témakörhöz vagy tartományhoz, és az identitás használatával továbbíthatja az eseményeket a támogatott célhelyre, például Service Bus-üzenetsorok, témakörök, eseményközpontok és tárfiókok számára.

A lépések a következők: 

1. Hozzon létre egy témakört vagy tartományt rendszer által hozzárendelt identitással, vagy frissítsen egy meglévő témakört vagy tartományt az identitás engedélyezéséhez. 
1. Adja hozzá az identitást egy megfelelő szerepkörhöz (például Service Bus)a célhelyen (például egy Service Bus sorhoz).
1. Esemény-előfizetések létrehozásakor engedélyezze az identitás használatát az események célhelyre való kézbesítéséhez. 

Részletes útmutatásért lásd: Event [delivery with a managed identity (Eseményk kézbesítése felügyelt identitással).](managed-service-identity.md)


## <a name="authenticate-event-delivery-to-webhook-endpoints"></a>Esemény webhookvégpontra történő kézbesítésének hitelesítése
A következő szakaszok azt ismertetik, hogyan hitelesíthető az események webhook-végpontokra való kézbesítése. A használt módszertől függetlenül érvényesítési kézfogási mechanizmust kell használnia. Részletekért [lásd: Webhook-eseményk kézbesítése.](webhook-event-delivery.md) 


### <a name="using-azure-active-directory-azure-ad"></a>Az Azure Active Directory használata (Azure AD)
Az Azure AD használatával biztosíthatja az események fogadására használt webhookvégpont Event Grid webhookvégpontot. Létre kell hoznia egy Azure AD-alkalmazást, létre kell hoznia egy szerepkört és egy szolgáltatásnév-t az alkalmazásban az Event Grid-t, és konfigurálnia kell az esemény-előfizetést az Azure AD-alkalmazás használatára. Ismerje meg, hogyan [konfigurálhatja a Azure Active Directory a Event Grid.](secure-webhook-delivery.md)

### <a name="using-client-secret-as-a-query-parameter"></a>Titkos ügyfélk titkos adatokat használ lekérdezési paraméterként
A webhook végpontja úgy is biztonságossá tehető, hogy lekérdezési paramétereket ad hozzá az esemény-előfizetés létrehozásakor megadott webhook cél URL-címéhez. Állítsa be az egyik lekérdezési paramétert titkos ügyfélként, például hozzáférési jogkivonatként [vagy](https://en.wikipedia.org/wiki/Access_token) közös titkos ként. Event Grid szolgáltatás minden, a webhooknak lekért eseménykésés lekérdezési paramétert tartalmaz. A webhook szolgáltatás lekérheti és ellenőrizheti a titkos adatokat. Ha frissíti az ügyfél titkos adatokat, az esemény-előfizetést is frissíteni kell. A titkos kulcsok rotációja során a kézbesítési hibák elkerülése érdekében a webhookot korlátozott időtartamra el kell fogadnia a régi és az új titkos kulcsokhoz is, mielőtt frissíti az esemény-előfizetést az új titkos okkal. 

Mivel a lekérdezési paraméterek tartalmazhatnak titkos ügyfél titkos adatokat, a rendszer nagy gondgal kezeli őket. Ezek titkosítva vannak tárolva, és nem érhetők el a szolgáltatásüzemeltetők számára. A rendszer nem naplózza őket a szolgáltatásnaplók/nyomkövetések részeként. Az Esemény-előfizetés tulajdonságainak lekérdezve a rendszer alapértelmezés szerint nem ad vissza céllekérdezés-paramétereket. Például: [az --include-full-endpoint-url](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_show) paramétert az Azure [CLI-ben kell használni.](/cli/azure)

További információ az események webhookokba való kézbesítésről: [Webhook-események kézbesítése](webhook-event-delivery.md)

> [!IMPORTANT]
> Azure Event Grid csak a  HTTPS-webhook-végpontokat támogatja. 

## <a name="endpoint-validation-with-cloudevents-v10"></a>Végpontérvényesítés a CloudEvents v1.0-val
Ha már ismeri a Event Grid, előfordulhat, hogy ismeri a végpontérvényesítési kézfogást a visszaélések megelőzése érdekében. A CloudEvents v1.0 a HTTP OPTIONS metódussal valósítja meg a saját visszaélés elleni védelmi **szemantikáját.** [](webhook-event-delivery.md) További információkért lásd: [HTTP 1.1 Web Hooks for event delivery - Version 1.0 (HTTP 1.1 web hookok eseményk kézbesítéshez – 1.0-s verzió).](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection) Ha a CloudEvents sémát használja a kimenethez, a Event Grid a CloudEvents 1.0-s vagy Event Grid elleni védelemmel. További információ: [CloudEvents 1.0-s](cloudevents-schema.md)séma használata a Event Grid. 


## <a name="next-steps"></a>Következő lépések
A [témakörökbe vagy tartományokba eseményeket](security-authenticate-publishing-clients.md) közzétevő ügyfelek hitelesítésével kapcsolatban lásd: Közzétételi ügyfelek hitelesítése. 
