---
title: MFA engedélyezése VPN-felhasználók számára az Azure AD-hitelesítés használatával
description: Ismerje meg, hogyan engedélyezheti az Azure Multi-Factor Authentication (MFA) használatát a VPN-felhasználók számára az Azure AD-hitelesítés használatával.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: alzam
ms.openlocfilehash: efe01c9e0907fef4d33d2a70b3e479b30c471a7c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91267890"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users-by-using-azure-ad-authentication"></a>Azure Multi-Factor Authentication (MFA) engedélyezése VPN-felhasználók számára az Azure AD-hitelesítés használatával

[!INCLUDE [overview](../../includes/vpn-gateway-vwan-openvpn-enable-mfa-overview.md)]

## <a name="enable-authentication"></a><a name="enableauth"></a>Hitelesítés engedélyezése

[!INCLUDE [enable authentication](../../includes/vpn-gateway-vwan-openvpn-enable-auth.md)]

## <a name="configure-sign-in-settings"></a><a name="enablesign"></a>Bejelentkezési beállítások konfigurálása

[!INCLUDE [sign in](../../includes/vpn-gateway-vwan-openvpn-sign-in.md)]

## <a name="option-1---per-user-access"></a><a name="peruser"></a>1. lehetőség – felhasználónkénti hozzáférés

[!INCLUDE [per user](../../includes/vpn-gateway-vwan-openvpn-per-user.md)]

## <a name="option-2---conditional-access"></a><a name="conditional"></a>2. lehetőség – feltételes hozzáférés

[!INCLUDE [conditional access](../../includes/vpn-gateway-vwan-openvpn-conditional.md)]

## <a name="next-steps"></a>Következő lépések

A virtuális hálózathoz való csatlakozáshoz létre kell hoznia és konfigurálnia kell egy VPN-ügyféloldali profilt. Lásd: [Az Azure ad-hitelesítés konfigurálása pont – hely kapcsolathoz az Azure-hoz](virtual-wan-point-to-site-azure-ad.md).
