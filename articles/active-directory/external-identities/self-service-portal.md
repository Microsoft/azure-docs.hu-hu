---
title: Önkiszolgáló bejelentkezési portál B2B-együttműködéshez – Azure AD
description: Megtudhatja, hogyan szabhatja testre a bevezetési munkafolyamatot Azure Active Directory B2B-felhasználók számára, hogy megfeleljenek a szervezet igényeinek.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a5b800e78448afcc970010535ba12b543d3cc74
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96860507"
---
# <a name="self-service-for-azure-ad-b2b-collaboration-sign-up"></a>Önkiszolgáló Azure AD B2B együttműködési regisztrációhoz

Az ügyfelek sokféleképpen hasznát vehetik a végfelhasználók számára az [Azure Portalon](https://portal.azure.com) és az [alkalmazás-hozzáférési panelen](https://myapps.microsoft.com) elérhetővé tett, beépített funkcióknak. A B2B-felhasználók bevezetésének munkafolyamatát azonban szükséges lehet a vállalat igényeinek megfelelően testre szabni.

## <a name="azure-ad-entitlement-management-for-b2b-guest-user-sign-up"></a>Azure AD-jogosultságok kezelése VÁLLALATKÖZI vendég felhasználói regisztrációhoz

Meghívó szervezetként előfordulhat, hogy nem tudja, hogy az egyes külső közreműködők Mikor férhetnek hozzá az erőforrásaihoz. Az Ön által felügyelt szabályzatoknak megfelelő módon kell megadnia a partnerek vállalatai számára a bejelentkezést. Ha engedélyezni szeretné a más szervezetek felhasználói számára a hozzáférés kérését, és a jóváhagyást a vendég fiókkal kell kiépíteni, és a csoportokhoz, alkalmazásokhoz és SharePoint Online-webhelyekhez van rendelve, akkor az [Azure ad jogosultság-kezelési](../governance/entitlement-management-overview.md) szolgáltatással konfigurálhatja azokat a házirendeket, amelyek [a külső felhasználók hozzáférését kezelik](../governance/entitlement-management-external-users.md#how-access-works-for-external-users).

## <a name="azure-active-directory-b2b-invitation-api"></a>Azure Active Directory B2B meghívó API

A szervezetek használhatják a [Microsoft Graph meghívó Manager API](/graph/api/resources/invitation) -t, hogy saját bevezetési tapasztalatokat hozzanak létre a B2B vendég felhasználói számára. Ha önkiszolgáló B2B vendég felhasználói regisztrációt szeretne biztosítani, javasoljuk, hogy használja az [Azure ad-jogosultságok kezelését](../governance/entitlement-management-overview.md). Ha azonban saját felhasználói élményt szeretne létrehozni, a [meghívás API létrehozása](/graph/api/invitation-post?tabs=http) lehetőséggel automatikusan elküldheti a testreszabott meghívót a B2B-felhasználónak, például a következőt:. Vagy az alkalmazás használhatja a létrehozási válaszban visszaadott inviteRedeemUrl a saját meghívóját (a választott kommunikációs mechanizmuson keresztül) a meghívott felhasználó számára.

## <a name="next-steps"></a>Következő lépések

* [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
* [Az External Identities díjszabása](external-identities-pricing.md)
* [Azure Active Directory vállalatközi együttműködés – gyakori kérdések](faq.md)
