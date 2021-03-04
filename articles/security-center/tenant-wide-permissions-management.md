---
title: Bérlői szintű engedélyek megadása és kérése Azure Security Center
description: Megtudhatja, hogyan kezelheti a bérlői szintű engedélyeket Azure Security Center
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/25/2021
ms.openlocfilehash: b3ddbdf04dc736b6f78a04dc6bb2bc484e67f70f
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107685"
---
# <a name="grant-and-request-tenant-wide-visibility"></a>Bérlői szintű láthatóság biztosítása és kérése

A **globális rendszergazda** Azure Active Directory (ad) szerepkörrel rendelkező felhasználó bérlői szintű felelősséggel rendelkezhet, de nem rendelkezik az Azure-engedélyekkel a szervezetre vonatkozó információk megtekintésére a Azure Security Centerban. A jogosultságszint-emelésre van szükség, mert az Azure AD-szerepkör-hozzárendelések nem biztosítanak hozzáférést az Azure-erőforrásokhoz. 

> [!TIP]
> További információ az [Azure-előfizetések és-felügyeleti csoportok kezeléséhez való jogosultságszint](../role-based-access-control/elevate-access-global-admin.md)-emelési jogosultságok a globális rendszergazdai szerepkörhöz való jogosultságszint-emeléséről.

## <a name="grant-tenant-wide-permissions-to-yourself"></a>A bérlői szintű engedélyek megadása saját magának

Bérlői szintű engedélyek kiosztása:

1. Ha a szervezet [Azure ad Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-configure.md)vagy bármely más PIM eszközzel felügyeli az erőforrás-hozzáférést, a globális rendszergazdai szerepkörnek aktívnak kell lennie a felhasználó számára az alábbi eljárást követve.

1. A bérlő legfelső szintű felügyeleti csoportjához tartozó hozzárendelés nélküli globális rendszergazda felhasználóként nyissa meg Security Center **Áttekintés** lapját, és válassza ki a teljes **bérlőre kiterjedő láthatósági** hivatkozást a szalagcímben. 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-banner.png" alt-text="Bérlői szintű engedélyek engedélyezése Azure Security Center":::

1. Válassza ki a hozzárendelni kívánt új Azure-szerepkört. 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-form.png" alt-text="A felhasználóhoz hozzárendelni kívánt bérlői szintű engedélyek definiálásának űrlapja":::

    > [!TIP]
    > Általában a biztonsági rendszergazdai szerepkör szükséges ahhoz, hogy a legfelső szintű házirendeket alkalmazzuk, míg a biztonsági olvasó elegendő lesz a bérlői szintű láthatóság biztosításához. A szerepkörök által biztosított engedélyekkel kapcsolatos további információkért tekintse meg a [biztonsági rendszergazda beépített szerepkörének leírását](../role-based-access-control/built-in-roles.md#security-admin) vagy a [biztonsági olvasó beépített szerepkörének leírását](../role-based-access-control/built-in-roles.md#security-reader).
    >
    > A Security Center-specifikus szerepkörök közötti különbségekért tekintse meg a [szerepkörök és az engedélyezett műveletek](security-center-permissions.md#roles-and-allowed-actions)táblázatát.

    A szervezeti szintű nézetet úgy érheti el, hogy a bérlő legfelső szintű felügyeleti csoport szintjén adja meg a szerepköröket.  

1. Jelentkezzen ki a Azure Portalból, majd jelentkezzen be újra.

1. Ha emelt szintű hozzáférést, nyissa meg vagy frissítse Azure Security Center annak ellenőrzéséhez, hogy látható-e az Azure AD-bérlő összes előfizetése. 


## <a name="request-tenant-wide-permissions-when-yours-are-insufficient"></a>Bérlői szintű engedélyek kérése, ha a tiéd nem elegendő

Ha bejelentkezik Security Centerba, és megjelenik egy szalagcím, amely arról tájékoztat, hogy a nézet korlátozott, a through gombra kattintva küldhet egy kérést a szervezet globális rendszergazdájának. A kérelemben megadhatja a hozzárendelni kívánt szerepkört, és a globális rendszergazda döntést fog hozni arról, hogy melyik szerepkört kell megadnia. 

Ez a globális rendszergazda döntése, hogy elfogadják vagy elutasítja ezeket a kéréseket. 

> [!IMPORTANT]
> Hét naponta csak egy kérést lehet elküldeni.

Emelt szintű engedélyek kérése a globális rendszergazdától:

1. A Azure Portal nyissa meg Azure Security Center.

1. Ha megjelenik a "korlátozott információ" felirat. Válassza ki.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions.png" alt-text="A felhasználó bérlői szintű engedélyeket kérhet a felhasználónak, hogy tájékoztassa a felhasználót.":::

1. A részletes kérelem űrlapján válassza ki a kívánt szerepkört és annak indoklását, hogy miért van szüksége ezekre az engedélyekre.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions-details.png" alt-text="A részleteket tartalmazó lap, amely a bérlői szintű engedélyeket kéri az Azure globális rendszergazdájától":::

1. Válassza a **hozzáférés kérése** lehetőséget.

    A rendszer e-mailt küld a globális rendszergazdának. Az e-mail tartalmaz egy hivatkozást Security Center, ahol jóváhagyhatja vagy elutasíthatja a kérést.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions-email.png" alt-text="E-mail küldése a globális rendszergazdának új engedélyek megadásához":::

    Miután a globális rendszergazda kiválasztja **a kérelem áttekintését** , és befejezi a folyamatot, a döntés e-mailben szerepel a kérelmező felhasználó számára. 

## <a name="next-steps"></a>Következő lépések

További információ Security Center engedélyekről a következő kapcsolódó oldalon:

- [Engedélyek az Azure Security Centerben](security-center-permissions.md)