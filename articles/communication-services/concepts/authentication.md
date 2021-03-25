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
ms.openlocfilehash: 9edfb63f5ce43ed325b4c4a1fa67e0e9ca52dc89
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110865"
---
# <a name="authenticate-to-azure-communication-services"></a>Hitelesítés az Azure kommunikációs szolgáltatásokban

Az Azure kommunikációs szolgáltatásokkal való kommunikációt minden ügyfélnek hitelesíteni kell. Egy tipikus architektúrában az [ügyfél és a kiszolgáló architektúrája](./client-and-server-architecture.md), a *hozzáférési kulcsok* vagy a *felügyelt identitások* használhatók a hitelesítéshez.

A hitelesítés egy másik típusa *felhasználói hozzáférési jogkivonatokat* használ a felhasználói részvételt igénylő szolgáltatásokkal való hitelesítéshez. A csevegés vagy a hívási szolgáltatás például *felhasználói hozzáférési jogkivonatokat* használ, hogy a felhasználók felvehetők legyenek egy szálba, és beszélgetni lehessen egymással.

## <a name="authentication-options"></a>Hitelesítési beállítások

A következő táblázat az Azure kommunikációs szolgáltatások SDK-kat és azok hitelesítési lehetőségeit mutatja be:

| SDK    | Hitelesítési beállítás                               |
| ----------------- | ----------------------------------------------------|
| Identitás          | Hozzáférési kulcs vagy felügyelt identitás                      |
| SMS               | Hozzáférési kulcs vagy felügyelt identitás                      |
| Telefonszámok     | Hozzáférési kulcs vagy felügyelt identitás                      |
| Hívó           | Felhasználói hozzáférési jogkivonat                                   |
| Csevegés              | Felhasználói hozzáférési jogkivonat                                   |

Az egyes engedélyezési beállítások rövid ismertetését az alábbiakban találja:

### <a name="access-key"></a>Hozzáférési kulcs

A hozzáférési kulcs hitelesítése megbízható szolgáltatási környezetben futó szolgáltatásalkalmazás számára megfelelő. Az elérési kulcs az Azure Communication Services portálon található. A szolgáltatásalkalmazás hitelesítő adatként használja a megfelelő SDK-k inicializálásához. Láthatja, hogyan használják az [Identity SDK](../quickstarts/access-tokens.md)-ban. 

Mivel a hozzáférési kulcs az erőforrás kapcsolati karakterláncának része, a kapcsolati karakterlánccal való hitelesítés egyenértékű a hozzáférési kulccsal való hitelesítéssel.

Ha az ACS API-kat manuálisan szeretné használni egy hozzáférési kulccsal, akkor alá kell írnia a kérést. A kérelem aláírása részletesen egy [oktatóanyagban](../tutorials/hmac-header-tutorial.md)található.

### <a name="managed-identity"></a>Felügyelt identitás

A felügyelt identitások kiváló biztonságot és könnyű használatot biztosítanak más engedélyezési beállításokkal szemben. Az Azure AD-vel például nem kell a fiókhoz tartozó hozzáférési kulcsot a kódban tárolnia, ahogy a hozzáférési kulcs engedélyezésével. Noha a kommunikációs szolgáltatások alkalmazásaival továbbra is használhatja a hozzáférési kulcs engedélyezését, a Microsoft javasolja az Azure AD-re való áttérést, ahol lehetséges. 

Felügyelt identitás beállításához [hozzon létre egy regisztrált alkalmazást az Azure CLI-ből](../quickstarts/managed-identity-from-cli.md). Ezután a végpont és a hitelesítő adatok használhatók az SDK-k hitelesítésére. Tekintse át a [felügyelt identitás](../quickstarts/managed-identity.md) használatára vonatkozó példákat.

### <a name="user-access-tokens"></a>Felhasználói hozzáférési tokenek

A felhasználói hozzáférési tokenek az Identity SDK használatával jönnek létre, és az Identity SDK-ban létrehozott felhasználókhoz vannak társítva. Tekintse meg a [felhasználók létrehozásának és a jogkivonatok létrehozásának](../quickstarts/access-tokens.md)példáját. Ezt követően a felhasználói hozzáférési tokenek a csevegésben vagy a hívó SDK-ban a beszélgetésekbe felvett résztvevők hitelesítésére használhatók. További információ: [csevegés hozzáadása az alkalmazáshoz](../quickstarts/chat/get-started.md). A felhasználói hozzáférési jogkivonat hitelesítése különbözik a hozzáférés kulcsa és a felügyelt identitás hitelesítése között, és nem biztonságos Azure-erőforrás helyett a felhasználó hitelesítésére szolgál.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Kommunikációs szolgáltatások erőforrásainak](../quickstarts/create-communication-resource.md) 
>  létrehozása és kezelése [Azure Active Directory felügyelt identitás-alkalmazás létrehozása az Azure CLI](../quickstarts/managed-identity-from-cli.md) 
>  -vel [Felhasználói hozzáférési tokenek létrehozása](../quickstarts/access-tokens.md)

További információért tekintse át a következő cikkeket:
- [Az ügyfél és a kiszolgáló architektúrájának megismerése](../concepts/client-and-server-architecture.md)
