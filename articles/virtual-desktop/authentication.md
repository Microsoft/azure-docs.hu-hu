---
title: Windows rendszerű virtuális asztali hitelesítés – Azure
description: Hitelesítési módszerek a Windows rendszerű virtuális asztali gépekhez.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/26/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: 04a4366bfee6b1d9c5f52d649910163269962684
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101709257"
---
# <a name="supported-authentication-methods"></a>Támogatott hitelesítési módszerek

Ebben a cikkben röviden áttekintjük, hogy milyen típusú hitelesítést használhat a Windows rendszerű virtuális asztali környezetekben.

## <a name="session-host-authentication"></a>Munkamenet-gazdagép hitelesítése

A Windows virtuális asztal az NT LAN Manager (NTLM) és a Kerberos protokollt is támogatja a munkamenet-gazdagép hitelesítéséhez. A Kerberos használatához azonban az ügyfélnek egy tartományvezérlőn futó kulcsszolgáltató (KDC) szolgáltatásból kell beszereznie a Kerberos biztonsági jegyeit. A jegyek beszerzéséhez az ügyfélnek közvetlen rámutatnia kell a tartományvezérlőre. A vállalati hálózat használatával közvetlen áttekintést kaphat. VPN-kapcsolat is használható a vállalati hálózathoz, vagy beállíthat egy [KDC-proxykiszolgálót](key-distribution-center-proxy.md)is.

Ezek a jelenleg támogatott bejelentkezési módszerek:

- Windows asztali ügyfél
    - Felhasználónév és jelszó
    - Intelligens kártya
    - Vállalati Windows Hello (csak megbízható tanúsítvány)
- Windows áruházbeli ügyfél
    - Felhasználónév és jelszó
- Webes ügyfél
    - Felhasználónév és jelszó
- Android
    - Felhasználónév és jelszó
- iOS
    - Felhasználónév és jelszó
- macOS
    - Felhasználónév és jelszó

>[!NOTE]
>Az intelligens kártya és a vállalati Windows Hello csak Kerberos használatával jelentkezhet be. A Kerberosba való bejelentkezéshez a tartományvezérlőre vagy a [KDC-proxykiszolgáló](key-distribution-center-proxy.md)felé irányuló betekintés szükséges.

## <a name="hybrid-identity"></a>Hibrid identitás

A Windows virtuális asztal támogatja a [hibrid identitások](../active-directory/hybrid/whatis-hybrid-identity.md) használatát Azure Active Directory (ad) segítségével, beleértve az Active Directory ÖSSZEVONÁSI szolgáltatások (AD FS) (ADFS) használatával összevont összevonásokat is. Mivel a felhasználóknak felderíthetőnek kell lenniük az Azure AD-n keresztül, a Windows virtuális asztal nem támogatja az ADFS-vel való önálló Active Directory üzemelő példányokat.

## <a name="single-sign-on-sso"></a>Egyszeri bejelentkezés (SSO)

A Windows rendszerű virtuális asztali szolgáltatás jelenleg nem támogatja az egyszeri bejelentkezéshez Active Directory összevonási szolgáltatások (AD FS) (ADFS).

A munkamenet-gazdagéphez tartozó hitelesítő adatok megadásának egyetlen módja, ha az ügyfélen szeretné menteni őket. Azt javasoljuk, hogy csak akkor tegye ezt biztonságos eszközökkel, ha megakadályozza, hogy más felhasználók hozzáférjenek az erőforrásokhoz.

## <a name="next-steps"></a>Következő lépések

Kíváncsi az üzembe helyezés egyéb módjaira is? Tekintse meg az [ajánlott biztonsági eljárásokat](security-guide.md).
