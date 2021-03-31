---
title: A hálózati házirend-kiszolgáló integrálása VPN Gateway RADIUS-hitelesítéssel MFA-hoz
description: Leírja, hogyan integrálható az Azure Gateway RADIUS-hitelesítés az NPS-kiszolgálóval a Multi-Factor Authenticationhoz.
services: vpn-gateway
documentationcenter: na
author: ahmadnyasin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/16/2019
ms.author: genli
ms.openlocfilehash: 208e99f61694f5a81a98dbc649e2a6035f57891b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96018273"
---
# <a name="integrate-azure-vpn-gateway-radius-authentication-with-nps-server-for-multi-factor-authentication"></a>Az Azure VPN Gateway RADIUS-hitelesítésének integrálása az NPS-kiszolgálóval Multi-Factor Authentication 

A cikk azt ismerteti, hogyan integrálható a hálózati házirend-kiszolgáló (NPS) az Azure VPN Gateway RADIUS-hitelesítéssel a Multi-Factor Authentication (MFA) pont – hely típusú VPN-kapcsolatokhoz való továbbításához. 

## <a name="prerequisite"></a>Előfeltétel

Az MFA engedélyezéséhez a felhasználóknak Azure Active Directory (Azure AD) szolgáltatásban kell lenniük, amelyet a helyszíni vagy a felhőalapú környezetből kell szinkronizálni. Emellett a felhasználónak már el kell végeznie az automatikus regisztrálási folyamatot az MFA-hoz.  További információ: [saját fiók beállítása kétlépéses ellenőrzéshez](../active-directory/user-help/multi-factor-authentication-end-user-first-time.md)

## <a name="detailed-steps"></a>Részletes lépések

### <a name="step-1-create-a-virtual-network-gateway"></a>1. lépés: virtuális hálózati átjáró létrehozása

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com).
2. A virtuális hálózati átjárót futtató virtuális hálózaton válassza az **alhálózatok** lehetőséget, majd válassza az **átjáró alhálózatot** az alhálózat létrehozásához. 

    ![Az átjáró-alhálózat hozzáadásának képe](./media/vpn-gateway-radiuis-mfa-nsp/gateway-subnet.png)
3. Hozzon létre egy virtuális hálózati átjárót a következő beállítások megadásával:

    - **Átjáró típusa**: válassza ki a **VPN** elemet.
    - **VPN típusa**: válassza az **útvonal-alapú** lehetőséget.
    - **SKU**: válasszon SKU-típust a követelmények alapján.
    - **Virtuális hálózat**: válassza ki azt a virtuális hálózatot, amelyben létrehozta az átjáró-alhálózatot.

        ![A virtuális hálózati átjáró beállításaival kapcsolatos rendszerkép](./media/vpn-gateway-radiuis-mfa-nsp/create-vpn-gateway.png)


 
### <a name="step-2-configure-the-nps-for-azure-ad-mfa"></a>2. lépés: a hálózati házirend-kiszolgáló konfigurálása az Azure AD MFA-hoz

1. Az NPS-kiszolgálón [telepítse az Azure ad MFA NPS-bővítményét](../active-directory/authentication/howto-mfa-nps-extension.md#install-the-nps-extension).
2. Nyissa meg az NPS-konzolt, kattintson a jobb gombbal a **RADIUS-ügyfelek** elemre, majd válassza az **új** lehetőséget. Hozza létre a RADIUS-ügyfelet a következő beállítások megadásával:

    - **Felhasználóbarát név**: írjon be bármilyen nevet.
    - **Cím (IP vagy DNS)**: írja be az 1. lépésben létrehozott átjáró-alhálózatot.
    - **Közös titok**: írjon be bármilyen titkos kulcsot, és jegyezze fel későbbi használatra.

      ![A RADIUS-ügyfél beállításaival kapcsolatos rendszerkép](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client1.png)

 
3.  A **speciális** lapon állítsa be a szállító nevét a **RADIUS standard** értékre, és győződjön meg arról, hogy a **További beállítások** jelölőnégyzet be van jelölve.

    ![A RADIUS-ügyfél speciális beállításaival kapcsolatos rendszerkép](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client2.png)

4. Nyissa meg a **házirendek**  >  **hálózati házirendek** csomópontot, kattintson duplán **a Microsoft Útválasztás és távelérési kiszolgáló** házirendje kapcsolatok elemre, válassza a **hozzáférés engedélyezése** lehetőséget, majd kattintson **az OK** gombra.

### <a name="step-3-configure-the-virtual-network-gateway"></a>3. lépés a virtuális hálózati átjáró konfigurálása

1. Jelentkezzen be [Azure Portalra](https://portal.azure.com).
2. Nyissa meg a létrehozott virtuális hálózati átjárót. Győződjön meg arról, hogy az átjáró típusa **VPN** , és hogy a VPN-típus **Route-alapú**.
3. Kattintson a **pont – hely konfiguráció**  >  **konfigurálása most** lehetőségre, majd adja meg a következő beállításokat:

    - **Címkészlet**: írja be az 1. lépésben létrehozott átjáró-alhálózatot.
    - **Hitelesítés típusa**: válassza a **RADIUS-hitelesítés** lehetőséget.
    - **Kiszolgáló IP-címe**: írja be az NPS-kiszolgáló IP-címét.

      ![A helyről a hely beállításaira mutató rendszerkép](./media/vpn-gateway-radiuis-mfa-nsp/configure-p2s.png)

## <a name="next-steps"></a>Következő lépések

- [Azure AD Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md)
- [Meglévő NPS-infrastruktúra integrálása az Azure AD-vel Multi-Factor Authentication](../active-directory/authentication/howto-mfa-nps-extension.md)