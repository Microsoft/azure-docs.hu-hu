---
title: Biztonságos Azure VPN gateway RADIUS-hitelesítés az NPS-kiszolgálóval a multi-factor Authentication |} A Microsoft Docs
description: Ismerteti az Azure-átjáró RADIUS-hitelesítés integrálása az NPS-kiszolgálóval a multi-factor Authentication hitelesítéshez.
services: vpn-gateway
documentationcenter: na
author: ahmadnyasin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/16/2019
ms.author: genli
ms.openlocfilehash: ab152cca1d809d92803a3e50ea83da1cbcd8243c
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74174615"
---
# <a name="integrate-azure-vpn-gateway-radius-authentication-with-nps-server-for-multi-factor-authentication"></a>Azure VPN gateway RADIUS-hitelesítés integrálása az NPS-kiszolgálóval a multi-factor Authentication 

A cikkből megtudhatja, hogyan integrálható a hálózati házirend-kiszolgáló (NPS) az Azure VPN gateway RADIUS-hitelesítés, hogy a multi-factor Authentication (MFA) a pont – hely VPN-kapcsolatokhoz. 

## <a name="prerequisite"></a>Előfeltétel

Többtényezős hitelesítés engedélyezése a felhasználók az Azure Active Directoryban (Azure AD), amely szinkronizálnia kell a vagy a helyszíni vagy felhőalapú környezetben kell lennie. Emellett a felhasználó kell már végrehajtotta az automatikus regisztrációs folyamatot az MFA-hoz.  További információkért lásd: [a kétlépéses ellenőrzéshez a fiók beállítása](../active-directory/user-help/multi-factor-authentication-end-user-first-time.md)

## <a name="detailed-steps"></a>Részletes lépések

### <a name="step-1-create-a-virtual-network-gateway"></a>1\. lépés: A virtuális hálózati átjáró létrehozása

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
2. A virtuális hálózatban, amely a virtuális hálózati átjárót fogja futtatni, válassza ki a **alhálózatok**, majd válassza ki **átjáró-alhálózat** egy alhálózat létrehozásához. 

    ![Átjáró-alhálózat hozzáadása bemutató kép](./media/vpn-gateway-radiuis-mfa-nsp/gateway-subnet.png)
3. Hozzon létre egy virtuális hálózati átjáró a következő beállítások megadásával:

    - **Átjáró típusa**: válassza ki a **VPN** elemet.
    - **VPN-típust**: válasszon **útvonalalapú**.
    - **Termékváltozat**: Termékváltozatának típusa igényei alapján válassza ki.
    - **Virtuális hálózat**: válassza ki a virtuális hálózatot, amelyben létrehozta az átjáró-alhálózat.

        ![Virtuális hálózati átjáró beállításainak bemutató kép](./media/vpn-gateway-radiuis-mfa-nsp/create-vpn-gateway.png)


 
### <a name="step-2-configure-the-nps-for-azure-mfa"></a>2\. lépés a hálózati házirend-kiszolgáló konfigurálása az Azure MFA-hoz

1. A hálózati házirend-kiszolgálón [az NPS-bővítményének telepítése az Azure MFA-kiszolgáló](../active-directory/authentication/howto-mfa-nps-extension.md#install-the-nps-extension).
2. Nyissa meg az NPS-konzolt, kattintson a jobb gombbal a **RADIUS-ügyfelek**elemre, majd válassza az **új**lehetőséget. Hozza létre a RADIUS-ügyfelet a következő beállítások megadásával:

    - **Rövid név**: adjon neki tetszőleges nevet.
    - **(IP- vagy DNS-) cím**: írja be az 1. lépésben létrehozott átjáró-alhálózatot.
    - **Közös titkos kulcsot**: írja be a bármely titkos kulcsot, és ne felejtse el későbbi használatra.

      ![A RADIUS-ügyfél beállításaival kapcsolatos rendszerkép](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client1.png)

 
3.  Az a **speciális** lapon, a szállító nevét állítsa **RADIUS Standard** , és ellenőrizze, hogy a **további beállítások** jelölőnégyzet nincs bejelölve.

    ![A RADIUS-ügyfél speciális beállításaival kapcsolatos rendszerkép](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client2.png)

4. Lépjen a **házirendek** > **hálózati házirendek**, kattintson duplán a **Microsoft Routing és távelérési kiszolgáló felé irányuló kapcsolatot** szabályzatot, jelölje be  **Hozzáférés biztosítása**, és kattintson a **OK**.

### <a name="step-3-configure-the-virtual-network-gateway"></a>3\. lépés konfigurálása a virtuális hálózati átjáró

1. Jelentkezzen be [az Azure portal](https://portal.azure.com).
2. Nyissa meg a létrehozott virtuális hálózati átjárót. Győződjön meg arról, hogy az átjáró típusának értéke **VPN** és, hogy a VPN-típust **útvonalalapú**.
3. Kattintson a **Helykonfiguráció mutasson** > **konfigurálás most**, és adja meg a következő beállításokat:

    - **Címkészlet**: írja be az 1. lépésben létrehozott átjáró-alhálózatot.
    - **Hitelesítési típus**: válasszon **RADIUS-hitelesítés**.
    - **Kiszolgáló IP-cím**: írja be a hálózati házirend-kiszolgáló IP-címét.

      ![Mutasson a hely beállításait bemutató kép](./media/vpn-gateway-radiuis-mfa-nsp/configure-p2s.png)

## <a name="next-steps"></a>Következő lépések

- [Az Azure multi-factor Authentication](../active-directory/authentication/multi-factor-authentication.md)
- [Meglévő hálózati házirend-kiszolgáló infrastruktúra integrálása az Azure Multi-Factor Authenticationnel](../active-directory/authentication/howto-mfa-nps-extension.md)
