---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 04/21/2020
ms.author: glenga
ms.openlocfilehash: 380878fedaa2f7ea6160c3c4801c8dece6e1a9ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "83648971"
---
#### <a name="enable-app-service-authenticationauthorization"></a>App Service hitelesítés/engedélyezés engedélyezése

A App Service platform lehetővé teszi, hogy az ügyfelek hitelesítéséhez Azure Active Directory (HRE) és számos harmadik féltől származó identitás-szolgáltatót használjon. Ezt a stratégiát használhatja a függvények egyéni engedélyezési szabályainak megvalósításához, és a függvény kódjából is dolgozhat a felhasználói adatokkal. További információ: [hitelesítés és engedélyezés Azure app Service](../articles/app-service/overview-authentication-authorization.md) és [ügyfél-identitások használata](../articles/azure-functions/functions-bindings-http-webhook-trigger.md#working-with-client-identities).

#### <a name="use-azure-api-management-apim-to-authenticate-requests"></a>A kérelmek hitelesítése az Azure API Management (APIM) használatával

A APIM számos API-biztonsági beállítást biztosít a bejövő kérelmekhez. További tudnivalók: [API Management hitelesítési házirendek](../articles/api-management/api-management-authentication-policies.md). A APIM használatával beállíthatja, hogy a Function alkalmazás csak a APIM-példány IP-címéről fogadjon kérelmeket. További információt az [IP-címek korlátozásai](../articles/azure-functions/ip-addresses.md#ip-address-restrictions)című témakörben talál.
