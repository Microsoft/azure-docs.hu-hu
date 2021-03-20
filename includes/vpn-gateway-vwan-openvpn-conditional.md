---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 410570302eec418f1e4bcb75d6413936a96b5171
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "77471528"
---
A feltételes hozzáférés lehetővé teszi a részletes hozzáférés-vezérlést az alkalmazások alapján. A feltételes hozzáférés használatához a feltételes hozzáférési szabályok hatálya alá eső felhasználókra vonatkozóan prémium szintű Azure AD 1 vagy nagyobb licencelési licencet kell alkalmazni.

1. Navigáljon a **vállalati alkalmazások – minden alkalmazás** lapra, és kattintson az **Azure VPN** elemre.

   - Kattintson a **feltételes hozzáférés** lehetőségre.
   - Az **új** ablaktábla megnyitásához kattintson az **új házirend** elemre.
2. Az **új** panelen navigáljon a **hozzárendelések-> felhasználók és csoportok** elemre. A **felhasználók és csoportok – >** **belefoglalása** lapon:

   - Kattintson **a felhasználók és csoportok kiválasztása** elemre.
   - **Felhasználók és csoportok** keresése.
   - Kattintson a **kiválasztás** elemre az MFA által érintett csoport vagy felhasználók kiválasztásához.
   - Kattintson a **Kész** gombra.

   ![Hozzárendelések](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-assignments.png)
3. Az **új** panelen navigáljon a **hozzáférés-vezérlések – > engedélyezési** panelre:

   - Kattintson a **hozzáférés engedélyezése** elemre.
   - Kattintson a **többtényezős hitelesítés megkövetelése** lehetőségre.
   - Kattintson **a minden kijelölt vezérlő megkövetelése** elemre.
   - Kattintson a **Kiválasztás** elemre.
   
   ![Hozzáférés engedélyezése – MFA](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-grant-mfa.png)
4. A **házirend engedélyezése** szakaszban:

   - Válassza **a** be lehetőséget.
   - Kattintson a **Létrehozás** lehetőségre.

   ![Házirend engedélyezése](./media/vpn-gateway-vwan-openvpn-azure-ad-mfa/mfa-ca-enable-policy.png)