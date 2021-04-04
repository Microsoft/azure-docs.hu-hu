---
title: RADIUS-és Azure MFA-kiszolgáló – Azure Active Directory
description: A RADIUS-hitelesítés és az Azure Multi-Factor Authentication-kiszolgáló üzembe helyezése.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23e2eafae185551c79d651ec6dcf39ad11eb8534
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96742119"
---
# <a name="integrate-radius-authentication-with-azure-multi-factor-authentication-server"></a>RADIUS-hitelesítés integrálása az Azure Multi-Factor Authentication-kiszolgálóval

A RADIUS egy szabványos protokoll hitelesítési kérések fogadására és feldolgozására. Az Azure Multi-Factor Authentication-kiszolgáló RADIUS-kiszolgálóként is használható. A kétlépéses ellenőrzés hozzáadásához szúrja be a kiszolgálót a RADIUS-ügyfél (VPN-berendezés) és a hitelesítési cél közé. A hitelesítési cél Active Directory, LDAP-címtár vagy egy másik RADIUS-kiszolgáló is lehet. Az Azure Multi-Factor Authentication (MFA) működéséhez az Azure MFA-kiszolgálót úgy kell konfigurálni, hogy az ügyfélkiszolgálókkal és a hitelesítési céllal is tudjon kommunikálni. Az Azure MFA-kiszolgáló fogadja a RADIUS-ügyfél kéréseit, ellenőrzi a hitelesítő adatokat a hitelesítési célon, Azure Multi-Factor Authenticationt biztosít és választ küld a RADIUS-ügyfélnek. A hitelesítés csak akkor sikeres, ha az elsődleges hitelesítés és az Azure Multi-Factor Authentication is sikeres.

> [!IMPORTANT]
> Az 2019. július 1-től a Microsoft már nem kínál az MFA-kiszolgálót az új üzemelő példányokhoz. Azok az új ügyfelek, amelyek a bejelentkezési események során a többtényezős hitelesítést (MFA) szeretnék megkövetelni, felhőalapú Azure AD-Multi-Factor Authenticationt kell használniuk.
>
> A felhőalapú MFA megismeréséhez tekintse meg az [oktatóanyag: felhasználói bejelentkezési események biztonságossá tétele az Azure ad-vel multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Ha felhőalapú MFA-t használ, tekintse [meg a meglévő NPS-infrastruktúra integrálása az Azure multi-Factor Authentication](howto-mfa-nps-extension.md)használatával című témakört.
>
> Az MFA-kiszolgálót az 2019. július 1. előtt aktivált meglévő ügyfelek letöltik a legújabb verziót, a jövőbeli frissítéseket, és a szokásos módon előállítják az aktiválási hitelesítő adatokat.

> [!NOTE]
> Az MFA-kiszolgáló csak a PAP (Password Authentication Protocol) és az MSCHAPv2 (Microsoft Challenge-Handshake Authentication Protocol) RADIUS-protokollt támogatja, ha RADIUS-kiszolgálóként működik.  Egyéb protokollok, például az EAP (Extensible Authentication Protocol), akkor használhatók, ha az MFA-kiszolgáló egy másik olyan RADIUS-kiszolgáló RADIUS-proxyjaként működik, amely támogatja az adott protokollt.
>
> Ebben a konfigurációban az egyirányú SMS és OATH tokenek nem működnek, mivel az MFA-kiszolgáló nem tud sikeres RADIUS kérdés-választ kezdeményezni más protokollok használatával.

![RADIUS-hitelesítés az MFA-kiszolgálón](./media/howto-mfaserver-dir-radius/radius.png)

## <a name="add-a-radius-client"></a>RADIUS-ügyfél hozzáadása

A RADIUS-hitelesítés konfigurálásához telepítse az Azure Multi-Factor Authentication-kiszolgálót egy Windows-kiszolgálóra. Ha Active Directory-környezetet használ, a kiszolgálót a hálózaton belüli tartományhoz kell csatlakoztatni. Az Azure Multi-Factor Authentication-kiszolgáló konfigurálásához kövesse az alábbi eljárást:

1. Az Azure Multi-Factor Authentication-kiszolgálón kattintson a bal oldali menüben lévő RADIUS-hitelesítés ikonra.
2. Jelölje be a **RADIUS-hitelesítés engedélyezése** jelölőnégyzetet.
3. Az Ügyfelek lapon módosítsa a hitelesítési és a nyilvántartási portokat, ha az Azure MFA RADIUS szolgáltatásnak nem szabványos portokon kell figyelnie a RADIUS-kéréseket.
4. Kattintson a **Hozzáadás** parancsra.
5. Adja meg az Azure Multi-Factor Authentication-kiszolgálón hitelesítendő készülék/kiszolgáló IP-címét, egy alkalmazásnevet (nem kötelező) és egy közös titkos kulcsot.

   Az alkalmazásnév megjelenik a jelentésekben, illetve megjelenhet az SMS-es vagy mobilalkalmazásos hitelesítési üzenetekben.

   A közös titkos kulcsnak azonosnak kell lennie az Azure Multi-Factor Authentication-kiszolgálón és a készüléken/kiszolgálón.

6. Jelölje be a **Multi-Factor Authentication felhasználói egyeztetés megkövetelése** jelölőnégyzetet, ha az összes felhasználót importálta a kiszolgálóra, és többtényezős hitelesítést alkalmaz rajtuk. Ha jelentős számú felhasználó még nincs importálva a kiszolgálóra vagy mentesül a kétlépéses ellenőrzés alól, ne jelölje be a jelölőnégyzetet.
7. Jelölje be a **Tartalék OATH token engedélyezése** jelölőnégyzetet, ha mobil hitelesítő alkalmazásokból származó OATH PIN-kódokat szeretné biztonsági mentési módszerként használni.
8. Kattintson az **OK** gombra.

Szükség esetén a 4–8. lépés megismétlésével további RADIUS-ügyfeleket adhat hozzá.

## <a name="configure-your-radius-client"></a>RADIUS-ügyfél konfigurálása

1. Kattintson a **Cél** fülre.
   * Ha az Azure MFA-kiszolgáló egy Active Directory-környezetben található tartományhoz csatlakoztatott kiszolgálóra van telepítve, válassza a **Windows-tartomány** lehetőséget.
   * Ha a felhasználókat egy LDAP-címtár alapján kell hitelesíteni, válassza az **LDAP-kötést**.
      Válassza a Címtár-integráció ikont, és szerkessze úgy az LDAP-konfigurációt a Beállítások lapon, hogy a kiszolgáló kötést hozhasson létre a címtárával. Az LDAP konfigurálásával kapcsolatos utasításokat az [LDAP-proxy konfigurációs útmutatójában](howto-mfaserver-dir-ldap.md) talál.
   * Ha a felhasználókat egy másik RADIUS-kiszolgálóval kell hitelesíteni, válassza a **RADIUS-kiszolgáló (k)** elemet.
1. Kattintson a **Hozzáadás** gombra, és konfigurálja azt a kiszolgálót, amelynek a kiszolgáló proxykapcsolaton keresztül átadja a RADIUS-kéréseket.
1. A RADIUS-kiszolgáló hozzáadása párbeszédpanelen adja meg a RADIUS-kiszolgáló IP-címét és egy közös titkos kulcsot.

   A közös titkos kulcsnak azonosnak kell lennie az Azure Multi-Factor Authentication- és a RADIUS-kiszolgálón. Módosítsa a hitelesítési portot és a nyilvántartási portot, ha a RADIUS-kiszolgáló eltérő portokat használ.

1. Kattintson az **OK** gombra.
1. Adja hozzá az Azure MFA-kiszolgálót egy RADIUS-ügyfélként a másik RADIUS-kiszolgálón, hogy fel tudja dolgozni az Azure MFA-kiszolgálóról érkező hozzáférési kéréseket. Ugyanazt a közös titkos kulcsot használja, amelyet az Azure Multi-Factor Authentication-kiszolgálón konfigurált.

További RADIUS-kiszolgálók hozzáadásához ismételje meg ezeket a lépéseket. A **Feljebb** és **Lejjebb** gombokkal konfigurálhatja a sorrendet, amely alapján az Azure MFA-kiszolgáló meghívja a kiszolgálókat.

Sikeresen konfigurálta az Azure Multi-Factor Authentication-kiszolgálót. A kiszolgáló mostantól fogadja a konfigurált ügyfelek RADIUS hozzáférési kéréseit a beállított portokon.

## <a name="radius-client-configuration"></a>A RADIUS-ügyfél konfigurálása

A RADIUS-ügyfél konfigurálásához kövesse az alábbi útmutatást:

* Konfigurálja a készüléket/kiszolgálót a RADIUS-n keresztüli hitelesítésre az Azure Multi-Factor Authentication-kiszolgáló IP-címére, amely a RADIUS-kiszolgálóként működik.
* Használja a korábban konfigurált közös titkos kulcsot.
* Konfigurálja a RADIUS-időtúllépést 30-60 másodpercre, hogy a felhasználó hitelesítő adatainak érvényesítéséhez, a kétlépéses ellenőrzés végrehajtásához, a válasz fogadásához és a RADIUS-hozzáférési kérelem megválaszolásához legyen idő.

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [integrálhatja a RADIUS-hitelesítést](howto-mfa-nps-extension.md) , ha az Azure AD-multi-Factor Authentication a felhőben. 
