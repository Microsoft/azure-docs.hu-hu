---
title: Beleegyezik több erőforrásra (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Megtudhatja, hogy a felhasználók hogyan kaphatnak előzetes beleegyezett több erőforrásra a Microsoft Authentication Library for .NET (MSAL.NET) használatával.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: d22b4109cdcdc965b1b2e03aba592022c58f773b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99583790"
---
# <a name="user-gets-consent-for-several-resources-using-msalnet"></a>A felhasználó beleegyezik több erőforráshoz a MSAL.NET használatával
A Microsoft Identity platform nem teszi lehetővé, hogy egyszerre több erőforráshoz kapjon tokent. A .NET-hez készült Microsoft Authentication Library (MSAL.NET) használatakor a beszerzési jogkivonat metódus Scopes paraméterének csak egyetlen erőforráshoz tartozó hatóköröket kell tartalmaznia. A Builder metódussal azonban további hatóköröket is megadhat, ha előre beleegyezett több erőforráshoz `.WithExtraScopeToConsent` .

> [!NOTE]
> A Microsoft Identity platform számos erőforrásának beszerzése, de nem Azure AD B2C. A Azure AD B2C csak a rendszergazdai jogosultságokat támogatja, a felhasználói beleegyezett nem.

Ha például két olyan erőforrással rendelkezik, amelyek mindegyike 2 hatókörrel rendelkezik:

- https: \/ /mytenant.onmicrosoft.com/customerapi (2 hatókörrel `customer.read` és `customer.write` )
- https: \/ /mytenant.onmicrosoft.com/vendorapi (2 hatókörrel `vendor.read` és `vendor.write` )

Az `.WithExtraScopeToConsent` alábbi példában látható módon a *extraScopesToConsent* paraméterrel rendelkező módosítót kell használnia:

```csharp
string[] scopesForCustomerApi = new string[]
{
  "https://mytenant.onmicrosoft.com/customerapi/customer.read",
  "https://mytenant.onmicrosoft.com/customerapi/customer.write"
};
string[] scopesForVendorApi = new string[]
{
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.read",
 "https://mytenant.onmicrosoft.com/vendorapi/vendor.write"
};

var accounts = await app.GetAccountsAsync();
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithAccount(accounts.FirstOrDefault())
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

Ez egy hozzáférési jogkivonatot kap az első webes API-hoz. A második webes API-hoz való hozzáféréshez ezután a jogkivonat-gyorsítótárból csendesen is beszerezheti a tokent:

```csharp
AcquireTokenSilent(scopesForVendorApi, accounts.FirstOrDefault()).ExecuteAsync();
```
