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
ms.openlocfilehash: 83976ed9d6f80b6c785cb84e74a0755472f9579f
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103561804"
---
# <a name="authenticate-to-azure-communication-services"></a>Hitelesítés az Azure kommunikációs szolgáltatásokban

Az Azure kommunikációs szolgáltatásokkal való kommunikációt minden ügyfélnek hitelesíteni kell. Egy tipikus architektúrában az [ügyfél és a kiszolgáló architektúrája](./client-and-server-architecture.md), a *hozzáférési kulcsok* vagy a *felügyelt identitások* használhatók a hitelesítéshez.

A hitelesítés egy másik típusa *felhasználói hozzáférési jogkivonatokat* használ a felhasználói részvételt igénylő szolgáltatásokkal való hitelesítéshez. A csevegés vagy a hívási szolgáltatás például *felhasználói hozzáférési jogkivonatokat* használ, hogy a felhasználók felvehetők legyenek egy szálba, és beszélgetni lehessen egymással.

## <a name="authentication-options"></a>Hitelesítési beállítások:

A következő táblázat az Azure kommunikációs szolgáltatások ügyféloldali kódtárait és hitelesítési lehetőségeit mutatja be:

| Ügyféloldali kódtár    | Hitelesítési beállítás                               |
| ----------------- | ----------------------------------------------------|
| Identitás          | Hozzáférési kulcs vagy felügyelt identitás                      |
| SMS               | Hozzáférési kulcs vagy felügyelt identitás                      |
| Telefonszámok     | Hozzáférési kulcs vagy felügyelt identitás                      |
| Hívó           | Felhasználói hozzáférési jogkivonat                                   |
| Csevegés              | Felhasználói hozzáférési jogkivonat                                   |

Az egyes engedélyezési beállítások rövid ismertetését az alábbiakban találja:

- A **hozzáférési kulcs** hitelesítése megbízható szolgáltatási környezetben futó szolgáltatásalkalmazás számára megfelelő. A hozzáférési kulcs az Azure Communication Services portálon található, és a szolgáltatásalkalmazás hitelesítő adatként használja a megfelelő ügyféloldali kódtárak inicializálásához. Tekintse meg, hogyan használja a rendszer az [Identity ügyféloldali függvénytárban](../quickstarts/access-tokens.md). Mivel a hozzáférési kulcs az erőforrás kapcsolati karakterláncának része, a kapcsolati karakterlánccal való hitelesítés egyenértékű a hozzáférési kulccsal való hitelesítéssel.

- A **felügyelt identitások** hitelesítése kiváló biztonságot és egyszerű használatot biztosít a többi engedélyezési lehetőséghez képest. Az Azure AD-vel például nem kell a fiók hozzáférési kulcsát a kóddal tárolnia, ahogy a hozzáférési kulcs engedélyezésével. Noha a kommunikációs szolgáltatások alkalmazásaival továbbra is használhatja a hozzáférési kulcs engedélyezését, a Microsoft javasolja az Azure AD-re való áttérést, ahol lehetséges. Felügyelt identitás beállításához [hozzon létre egy regisztrált alkalmazást az Azure CLI-ből](../quickstarts/managed-identity-from-cli.md). Ezután a végpont és a hitelesítő adatok használhatók az ügyféloldali kódtárak hitelesítésére. Tekintse át a [felügyelt identitás](../quickstarts/managed-identity.md) használatára vonatkozó példákat.

- A **felhasználói hozzáférési jogkivonatok** az Identity ügyféloldali kódtár használatával jönnek létre, és az identitás ügyféloldali függvénytárában létrehozott felhasználókhoz vannak társítva. Tekintse meg a [felhasználók létrehozásának és a jogkivonatok létrehozásának](../quickstarts/access-tokens.md)példáját. Ezt követően a felhasználói hozzáférési tokenek a csevegésben vagy a hívó SDK-ban a beszélgetésekbe felvett résztvevők hitelesítésére használhatók. További információ: [csevegés hozzáadása az alkalmazáshoz](../quickstarts/chat/get-started.md). A felhasználói hozzáférési jogkivonat hitelesítése különbözik a hozzáférés kulcsa és a felügyelt identitás hitelesítése között, és nem biztonságos Azure-erőforrás helyett a felhasználó hitelesítésére szolgál.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Kommunikációs szolgáltatások erőforrásainak](../quickstarts/create-communication-resource.md) 
>  létrehozása és kezelése [Azure Active Directory felügyelt identitás-alkalmazás létrehozása az Azure CLI](../quickstarts/managed-identity-from-cli.md) 
>  -vel [Felhasználói hozzáférési tokenek létrehozása](../quickstarts/access-tokens.md)

További információért tekintse át a következő cikkeket:
- [Az ügyfél és a kiszolgáló architektúrájának megismerése](../concepts/client-and-server-architecture.md)
