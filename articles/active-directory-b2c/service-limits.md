---
title: Azure AD B2C szolgáltatási korlátok és korlátozások
titleSuffix: Azure AD B2C
description: A Azure Active Directory B2C szolgáltatásra vonatkozó szolgáltatási korlátokra és korlátozásokra mutató hivatkozás.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/29/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b980624ebf55c3666bcc7dd1d60f681d788c95ae
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98987047"
---
# <a name="azure-active-directory-b2c-service-limits-and-restrictions"></a>Azure Active Directory B2C szolgáltatási korlátok és korlátozások

Ez a cikk a Azure Active Directory B2C (Azure AD B2C) szolgáltatás használati korlátozásait és egyéb szolgáltatási korlátozásait tartalmazza.

## <a name="end-userconsumption-related-limits"></a>Végfelhasználók/fogyasztással kapcsolatos korlátok

A következő végfelhasználói szolgáltatási korlátozások érvényesek a Azure AD B2C által támogatott összes hitelesítési és engedélyezési protokollra, beleértve az SAML-t, az Open ID-t, a OAuth2 és a ROPC.

|Kategória |Korlát    |
|---------|---------|
|Kérelmek száma IP-címenként Azure AD B2C bérlő       |6000/5perc          |
|Kérelmek teljes száma Azure AD B2C bérlőn     |12000/perc          |

A kérések száma a Azure AD B2C felhasználói útvonalon előforduló címtárbeli olvasások és írások számától függően változhat. A címtárból beolvasott egyszerű bejelentkezési útvonal például 1 kérelemből áll. Ha a bejelentkezési útvonalnak is frissítenie kell a könyvtárat, a művelet további kérelemnek számít.

## <a name="azure-ad-b2c-configuration-limits"></a>Azure AD B2C konfigurációs korlátok

A következő táblázat a Azure AD B2C szolgáltatás felügyeleti konfigurációs korlátozásait sorolja fel.

|Kategória  |Korlát  |
|---------|---------|
|Alkalmazások száma Azure AD B2C bérlőn   |250           |
|Házirendek száma Azure AD B2C bérlőn       |200          |
|Fájl maximális mérete      |400 KB          |
|Hatókörök száma egy alkalmazásban        |1000          |
|Egyéni attribútumok száma felhasználónként <sup>1</sup>       |100         |
|Átirányítási URL-címek száma egy alkalmazásban       |100         |
|Kijelentkezési URL-címek száma egy alkalmazásban        |1          |
|Karakterlánc-korlát/attribútum      |250 karakter          |
|B2C-bérlők száma/előfizetés      |20         |
|[Öröklési](custom-policy-overview.md#inheritance-model) szintek az egyéni házirendekben     |10         |
|Házirendek száma Azure AD B2C bérlőn      |200         |
|Fájl maximális mérete      |400 KB         |

<sup>1</sup> lásd még [Az Azure ad szolgáltatás korlátozásait és korlátozásait](../active-directory/enterprise-users/directory-service-limits-restrictions.md).

## <a name="next-steps"></a>Következő lépések

- Tudnivalók a [Microsoft Graph szabályozási útmutatóról](/graph/throttling.md) 
- További információ az [Azure ad B2C alkalmazások érvényesítési eltérésekről](../active-directory/develop/supported-accounts-validation.md)













