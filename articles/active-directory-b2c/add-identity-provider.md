---
title: Identitás-szolgáltató hozzáadása – Azure Active Directory B2C | Microsoft Docs
description: Ismerje meg, hogyan adhat hozzá identitás-szolgáltatót a Active Directory B2C bérlőhöz.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 01/04/2021
ms.custom: mvc
ms.topic: how-to
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: e336fe8d8042657329936ed59058ed91109692fc
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2021
ms.locfileid: "97861844"
---
# <a name="add-an-identity-provider-to-your-azure-active-directory-b2c-tenant"></a>Identitás-szolgáltató hozzáadása a Azure Active Directory B2C bérlőhöz

A Azure AD B2C konfigurálható úgy, hogy a felhasználók a külső közösségi vagy vállalati identitás-szolgáltatóktól (identitásszolgáltató) származó hitelesítő adatokkal jelentkezzenek be az alkalmazásba. Azure AD B2C támogatja a külső identitás-szolgáltatókat, mint például a Facebook, a Microsoft-fiók, a Google, a Twitter, valamint a OAuth 1,0, OAuth 2,0, OpenID Connect és SAML protokollokat támogató identitás-szolgáltatókat.

A külső identitás-szolgáltató összevonásával lehetővé teheti a felhasználók számára, hogy a meglévő közösségi vagy vállalati fiókjával jelentkezzenek be anélkül, hogy új fiókot kellene létrehoznia az alkalmazáshoz.

A regisztrációs vagy bejelentkezési oldalon Azure AD B2C megjeleníti azon külső identitás-szolgáltatók listáját, amelyeket a felhasználó kiválaszthat a bejelentkezéshez. Miután kiválasztotta az egyik külső identitás-szolgáltatót, a rendszer a kiválasztott szolgáltató webhelyére helyezi (átirányítja) a bejelentkezési folyamat befejezéséhez. Ha a felhasználó sikeresen bejelentkezett, a rendszer visszaküldi Azure AD B2C az alkalmazásban lévő fiók hitelesítéséhez.

![Mobil bejelentkezési példa közösségi fiókkal (Facebook)](media/add-identity-provider/external-idp.png)

A Azure Portal használatával adhat hozzá Azure Active Directory B2C (Azure AD B2C) által támogatott identitás-szolgáltatókat a [felhasználói folyamatokhoz](user-flow-overview.md) . Az [Egyéni szabályzatokhoz](custom-policy-get-started.md)is hozzáadhat identitás-szolgáltatókat.

## <a name="select-an-identity-provider"></a>Válasszon ki egy identitás-szolgáltatót

Általában csak egy identitás-szolgáltatót használ az alkalmazásaiban, de lehetősége van további hozzáadására. Az alábbi útmutatókból megtudhatja, hogyan hozhatja létre az identitás-szolgáltató alkalmazást, hogyan adhatja hozzá az identitás-szolgáltatót a bérlőhöz, és hogyan adhatja hozzá az identitás-szolgáltatót a felhasználói folyamathoz vagy egyéni szabályzathoz.

* [AD FS](identity-provider-adfs.md)
* [Amazon](identity-provider-amazon.md)
* [Azure AD (egybérlős)](identity-provider-azure-ad-single-tenant.md)
* [Azure AD (több-bérlős)](identity-provider-azure-ad-multi-tenant.md)
* [Facebook](identity-provider-facebook.md)
* [Általános identitásszolgáltató](identity-provider-generic-openid-connect.md)
* [GitHub](identity-provider-github.md)
* [ID.me](identity-provider-id-me.md)
* [Google](identity-provider-google.md)
* [LinkedIn](identity-provider-linkedin.md)
* [Microsoft-fiók](identity-provider-microsoft-account.md)
* [QQ](identity-provider-qq.md)
* [Salesforce](identity-provider-salesforce-saml.md)
* [Twitter](identity-provider-twitter.md)
* [WeChat](identity-provider-wechat.md)
* [Weibo](identity-provider-weibo.md)
