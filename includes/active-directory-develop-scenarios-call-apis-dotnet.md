---
title: fájl belefoglalása
description: fájl belefoglalása
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 3d4e45d1bf53bab4d1f9c45367f9d051f1668e2b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "76308928"
---
### <a name="authenticationresult-properties-in-msalnet"></a>AuthenticationResult tulajdonságai a MSAL.NET

A tokenek beszerzésére szolgáló metódusok visszaadása `AuthenticationResult` . Aszinkron metódusok esetén a visszaadott `Task<AuthenticationResult>` értéket adja vissza.

A MSAL.NET a `AuthenticationResult` következőket teszi elérhetővé:

- `AccessToken` ahhoz, hogy a webes API hozzáférjen az erőforrásokhoz. Ez a paraméter egy karakterlánc, általában Base-64 kódolású JWT. Az ügyfélnek soha nem kell megkeresnie a hozzáférési jogkivonatot. A formátum nem garantált, hogy stabil maradjon, és titkosítva lehet az erőforrás számára. Az ügyfél hozzáférési jogkivonatának tartalmától függő kód írása az egyik legnagyobb forrása a hibáknak és az ügyfél-logikai szüneteknek. További információ: [hozzáférési tokenek](../articles/active-directory/develop/access-tokens.md).
- `IdToken` a felhasználó számára. Ez a paraméter egy kódolt JWT. További információ: [azonosító jogkivonatok](../articles/active-directory/develop/id-tokens.md).
- `ExpiresOn` Megadja a jogkivonat lejáratának dátumát és időpontját.
- `TenantId` azt a bérlőt tartalmazza, amelyben a felhasználó található. Azure Active Directory (Azure AD) B2B-forgatókönyvekben a vendég felhasználói számára a bérlői azonosító a vendég bérlő, nem az egyedi bérlő.
Ha a tokent egy felhasználó számára továbbítják, akkor a `AuthenticationResult` felhasználóval kapcsolatos információkat is tartalmaz. A bizalmas ügyfél-folyamatok esetében, amelyekben a rendszer a jogkivonatokat az alkalmazás felhasználója nélkül kéri le, ez a felhasználói információ null értékű.
- Az `Scopes` , amelyhez a tokent kiállították.
- A felhasználó egyedi azonosítója.

### <a name="iaccount"></a>IAccount

A MSAL.NET meghatározza egy fiók fogalmát az `IAccount` illesztőfelületen keresztül. Ez a feltörési változás biztosítja a megfelelő szemantikai lehetőséget. Ugyanaz a felhasználó több fiókkal is rendelkezhet különböző Azure AD-címtárakban. Emellett a MSAL.NET jobb információt nyújt a vendég forgatókönyvek esetében, mivel az otthoni fiókadatok vannak megadva.
Az alábbi ábrán az interfész szerkezete látható `IAccount` .

![IAccount felület szerkezete](https://user-images.githubusercontent.com/13203188/44657759-4f2df780-a9fe-11e8-97d1-1abbffade340.png)

Az `AccountId` osztály egy adott bérlőn belüli fiókot azonosít az alábbi táblázatban látható tulajdonságokkal.

| Tulajdonság | Leírás |
|----------|-------------|
| `TenantId` | Egy GUID-azonosító karakterlánc-ábrázolása, amely annak a bérlőnek az azonosítója, amelyben a fiók található. |
| `ObjectId` | Egy GUID-azonosító karakterlánc-ábrázolása, amely a bérlőben a fiókot birtokló felhasználó azonosítója. |
| `Identifier` | A fiók egyedi azonosítója. `Identifier` a `ObjectId` és a `TenantId` vesszővel elválasztott összefűzése. Nem Base 64 kódolású. |

Az `IAccount` illesztőfelület egyetlen fiókra vonatkozó információkat jelenít meg. Ugyanaz a felhasználó különböző bérlők között is szerepelhet, ami azt jelenti, hogy egy felhasználó több fiókkal is rendelkezhet. A tagok az alábbi táblázatban láthatók.

| Tulajdonság | Leírás |
|----------|-------------|
| `Username` | Egy karakterlánc, amely tartalmazza a UserPrincipalName (UPN) formátumát, például: john.doe@contoso.com . Ez a karakterlánc lehet null értékű, a HomeAccountId és a HomeAccountId. Identifier értéktől eltérően, amely nem lehet null. Ez a tulajdonság a `DisplayableId` MSAL.net korábbi verzióiban lévő tulajdonságot váltja fel `IUser` . |
| `Environment` | Egy karakterlánc, amely tartalmazza a fiók identitás-szolgáltatóját, például: `login.microsoftonline.com` . Ez a tulajdonság helyettesíti a `IdentityProvider` tulajdonságot, azzal a `IUser` különbséggel, hogy a `IdentityProvider` felhőalapú környezeten kívül a bérlőre vonatkozó információkkal is rendelkezett. Itt az érték csak a gazdagép. |
| `HomeAccountId` | A felhasználóhoz tartozó otthoni fiók azonosítója. Ez a tulajdonság egyedileg azonosítja a felhasználót az Azure AD-bérlők között. |

### <a name="use-the-token-to-call-a-protected-api"></a>Védett API meghívása a token használatával

Ezt követően `AuthenticationResult` adja hozzá a MSAL a-ben a `result` http-engedélyezési fejléchez, mielőtt elkezdené a hozzáférést a védett webes API-hoz.

```csharp
httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
...
}
```