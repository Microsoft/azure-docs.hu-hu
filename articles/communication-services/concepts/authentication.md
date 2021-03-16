---
title: Hitelesítés az Azure kommunikációs szolgáltatásokban
titleSuffix: An Azure Communication Services concept document
description: Ismerje meg, hogy az alkalmazás vagy szolgáltatás milyen módon tud hitelesíteni a kommunikációs szolgáltatásokban.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: b3cd0643a74ccadb8390ce906eb391420de15a29
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103490789"
---
# <a name="authenticate-to-azure-communication-services"></a>Hitelesítés az Azure kommunikációs szolgáltatásokban

Az Azure kommunikációs szolgáltatásokkal való kommunikációt minden ügyfélnek hitelesíteni kell. Egy tipikus architektúrában az [ügyfél és a kiszolgáló architektúrája](./client-and-server-architecture.md), a *hozzáférési kulcsok* vagy a *felügyelt identitás* használatos a megbízható felhasználói hozzáférési szolgáltatásban a felhasználók létrehozásához és a jogkivonatok kibocsátásához. A megbízható felhasználói hozzáférési szolgáltatás által kiadott *felhasználói hozzáférési jogkivonat* az ügyfélalkalmazások számára más kommunikációs szolgáltatásokhoz, például csevegéshez vagy híváshoz való hozzáféréshez használatos.

Az Azure Communication Services SMS szolgáltatás *hozzáférési kulcsokat* vagy *felügyelt identitást* is elfogad a hitelesítéshez. Ez általában egy megbízható szolgáltatási környezetben futó szolgáltatásalkalmazás esetében fordul elő.

Az egyes engedélyezési beállítások rövid ismertetését az alábbiakban találja:

- **Hozzáférési kulcs** hitelesítése SMS-és identitás-műveletekhez. A hozzáférési kulcs hitelesítése megbízható szolgáltatási környezetben futó szolgáltatásalkalmazás számára megfelelő. A hozzáférési kulcs az Azure Communication Services portálon található. Egy hozzáférési kulccsal való hitelesítéshez a szolgáltatásalkalmazás a hozzáférési kulcsot használja hitelesítő adatként a megfelelő SMS-vagy identitás-ügyféloldali kódtárak inicializálásához. a [hozzáférési jogkivonatok létrehozása és kezelése](../quickstarts/access-tokens.md)című témakörben talál további információt. Mivel a hozzáférési kulcs az erőforrás kapcsolati karakterláncának része, lásd: [kommunikációs szolgáltatások erőforrásainak létrehozása és kezelése](../quickstarts/create-communication-resource.md), a kapcsolati karakterlánccal való hitelesítés egyenértékű a hozzáférési kulccsal való hitelesítéssel.
- **Felügyelt identitások** hitelesítése az SMS-ben és az Identity műveletekben. Felügyelt identitás: a [felügyelt identitás](../quickstarts/managed-identity.md)a megbízható szolgáltatási környezetben futó alkalmazások számára megfelelő. A felügyelt identitással történő hitelesítéshez a szolgáltatásalkalmazás létrehoz egy hitelesítő adatot az azonosítóval és a felügyelt identitás titkos kódjával, majd inicializálja a megfelelő SMS-vagy identitás-ügyféloldali kódtárakat. a [hozzáférési tokenek létrehozása és kezelése](../quickstarts/access-tokens.md)című témakörben talál további információt.
- **Felhasználói hozzáférési jogkivonat** hitelesítése csevegéshez és híváshoz. A felhasználói hozzáférési tokenek lehetővé teszik az ügyfélalkalmazások számára az Azure kommunikációs csevegés és a hívási szolgáltatások hitelesítését. Ezek a tokenek a létrehozott "megbízható felhasználói hozzáférés" szolgáltatásban jönnek létre. Ezeket a rendszer a tokent használó ügyféleszközök számára adja meg a csevegés inicializálásához és az ügyféloldali kódtárak meghívásához. További információ: [csevegés hozzáadása az alkalmazáshoz](../quickstarts/chat/get-started.md) például.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Kommunikációs szolgáltatások erőforrásainak](../quickstarts/create-communication-resource.md) 
>  létrehozása és kezelése [Azure Active Directory felügyelt identitás-alkalmazás létrehozása az Azure CLI](../quickstarts/managed-identity-from-cli.md) 
>  -vel [Felhasználói hozzáférési tokenek létrehozása](../quickstarts/access-tokens.md)

További információért tekintse át a következő cikkeket:
- [Az ügyfél és a kiszolgáló architektúrájának megismerése](../concepts/client-and-server-architecture.md)
