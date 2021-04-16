---
title: B2B együttműködési felhasználó hozzáadása egy szerepkörhöz – Azure Active Directory
description: Vendégfelhasználó hozzáadása egy szerepkörhöz a Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/08/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 32a931fe43b6be406f0b2a4b8193c1631261f7e5
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575666"
---
# <a name="grant-permissions-to-users-from-partner-organizations-in-your-azure-active-directory-tenant"></a>Engedélyek megadása a bérlőben a partnerszervezetek Azure Active Directory számára

Azure Active Directory (Azure AD) B2B együttműködési felhasználók vendégfelhasználóként vannak hozzáadva a címtárhoz, és a címtár vendégengedélyei alapértelmezés szerint korlátozva vannak. Előfordulhat, hogy vállalatának néhány vendégfelhasználóra van szüksége, hogy magasabb jogosultsági szintű szerepköröket töltsenek ki a szervezetben. A magasabb jogosultságú szerepkörök definiálása érdekében a vendégfelhasználók bármilyen szerepkörhöz hozzáadhatóak a szervezet igényei alapján.

Ha egy címtár-szerepkört rendel egy vendégfelhasználóhoz, a vendégfelhasználó további, a szerepkörhöz kapott engedélyekkel fog rendelkezni, beleértve az alapszintű olvasási engedélyeket is. Lásd: [Az Azure AD beépített szerepkörei.](https://docs.microsoft.com/azure/active-directory/roles/permissions-reference)

## <a name="default-role"></a>Alapértelmezett szerepkör

![Képernyőkép az alapértelmezett címtárszerepkről](./media/add-guest-to-role/default-role.png)

## <a name="global-administrator-role"></a>Globális rendszergazdai szerepkör

![Képernyőkép a globális rendszergazdai szerepkörről](./media/add-guest-to-role/global-admin-role.png)

## <a name="limited-administrator-role"></a>Korlátozott rendszergazdai szerepkör

![Képernyőkép a korlátozott rendszergazdai szerepkörről](./media/add-guest-to-role/limited-admin-role.png)

## <a name="next-steps"></a>Következő lépések

- [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
- [B2B-együttműködés felhasználói tulajdonságok](user-properties.md)
