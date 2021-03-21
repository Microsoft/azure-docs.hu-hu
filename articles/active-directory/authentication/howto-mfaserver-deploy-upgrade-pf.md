---
title: A PhoneFactor frissítése az Azure MFA-kiszolgálóra – Azure Active Directory
description: Az Azure MFA-kiszolgálóval kapcsolatos első lépések a régebbi Phonefactor-ügynökről való frissítés után.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 808109e00decf5c4084be5be550b49e2e7d4628c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96742357"
---
# <a name="upgrade-the-phonefactor-agent-to-azure-multi-factor-authentication-server"></a>Frissítés a PhoneFactor ügynökről az Azure Multi-Factor Authentication-kiszolgálóra

A PhoneFactor-ügynök 5.x verziójáról az Azure Multi-Factor Authentication-kiszolgálóra való frissítéshez először távolítsa el a PhoneFactor-ügynököt és a kapcsolódó összetevőket. A Multi-Factor Authentication-kiszolgáló és kapcsolódó összetevői ezután telepíthetők.

> [!IMPORTANT]
> Az 2019. július 1-től a Microsoft már nem kínál az MFA-kiszolgálót az új üzemelő példányokhoz. Azok az új ügyfelek, amelyek a bejelentkezési események során a többtényezős hitelesítést (MFA) szeretnék megkövetelni, felhőalapú Azure AD-Multi-Factor Authenticationt kell használniuk.
>
> A felhőalapú MFA megismeréséhez tekintse meg az [oktatóanyag: felhasználói bejelentkezési események biztonságossá tétele az Azure ad-vel multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Az MFA-kiszolgálót az 2019. július 1. előtt aktivált meglévő ügyfelek letöltik a legújabb verziót, a jövőbeli frissítéseket, és a szokásos módon előállítják az aktiválási hitelesítő adatokat.

## <a name="uninstall-the-phonefactor-agent"></a>A PhoneFactor-ügynök eltávolítása

1. Először készítsen biztonsági másolatot a PhoneFactor-adatfájlokról. Az alapértelmezett telepítési hely a C:\Program Files\PhoneFactor\Data\Phonefactor.pfdata mappa.

2. Ha a felhasználói portál telepítve van:
   1. Nyissa meg a telepítési mappát, és készítsen biztonsági másolatot a web.config fájlról. Az alapértelmezett telepítési hely a C:\inetpub\wwwroot\PhoneFactor mappa.

   2. Ha hozzáadott egyéni témákat a portálhoz, készítsen biztonsági másolatot az egyéni mappáról a C:\inetpub\wwwroot\PhoneFactor\App_Themes könyvtárban.

   3. Távolítsa el a felhasználói portált a PhoneFactor ügynök segítségével (csak akkor érhető el, ha ugyanazon a kiszolgálón telepítette, amelyen a PhoneFactor ügynököt) vagy a Windows Programok és szolgáltatások lapjáról.

3. Ha a Mobile App Web Service telepítve van:

   1. Nyissa meg a telepítési mappát, és készítsen biztonsági másolatot a web.config fájlról. Az alapértelmezett telepítési hely a C:\inetpub\wwwroot\PhoneFactorPhoneAppWebService mappa.

   2. Távolítsa el a Mobile App Web Service-t a Windows Programok és szolgáltatások lapja segítségével.

4. Ha a Web Service SDK telepítve van, távolítsa el a PhoneFactor ügynök vagy a Windows Programok és szolgáltatások lapja segítségével.

5. Távolítsa el a PhoneFactor ügynököt a Windows Programok és szolgáltatások lapja segítségével.

## <a name="install-the-multi-factor-authentication-server"></a>A Multi-Factor Authentication-kiszolgáló telepítése

A rendszer a telepítési útvonalat a korábbi PhoneFactor ügynök telepítése által létrehozott beállításjegyzékből olvassa be, ezért ugyanarra a helyre fogja telepíteni (pl. C:\Program Files\PhoneFactor). Az új telepítések eltérő alapértelmezett telepítési útvonallal rendelkeznek (pl. C:\Program Files\Multi-Factor Authentication Server). Az előző PhoneFactor ügynök által hátrahagyott adatfájlt a rendszer frissíti a telepítés során, ezért a felhasználók és beállítások megmaradnak az új Multi-Factor Authentication-kiszolgáló telepítése után.

1. Ha a rendszer kéri, aktiválja a Multi-Factor Authentication-kiszolgálót, és győződjön meg arról, hogy a megfelelő replikációs csoporthoz van hozzárendelve.

2. Ha a Web Service SDK-t korábban telepítette, telepítse az új Webszolgáltatás SDK-t a Multi-Factor Authentication-kiszolgáló felhasználói felületén keresztül.

   Az alapértelmezett virtuális címtár neve mostantól **PhoneFactorWebServiceSdk** helyett **MultiFactorAuthWebServiceSdk**. Ha a korábbi nevet szeretné használni, a virtuális címtár nevét a telepítés közben módosítania kell. Ellenkező esetben, ha engedélyezi a telepítés során az új alapértelmezett név használatát, módosítania kell az URL-címet minden alkalmazásban, amely a Web Service SDK-ra hivatkozik, (például a felhasználói portál és a Mobile App Web Service), hogy a megfelelő helyre mutassanak.

3. Ha a felhasználói portált korábban telepítette a PhoneFactor ügynök kiszolgálóján, telepítse az új Multi-Factor Authentication felhasználói portált a Multi-Factor Authentication-kiszolgáló felhasználói felületén keresztül.

   Az alapértelmezett virtuális címtár neve mostantól **PhoneFactor** helyett **MultiFactorAuth**. Ha a korábbi nevet szeretné használni, a virtuális címtár nevét a telepítés közben módosítania kell. Ellenkező esetben, ha a telepítés során engedélyezi az új alapértelmezett név használatát, kattintson a felhasználói portál ikonra a Multi-Factor Authentication-kiszolgálón és frissítse a felhasználói portál URL-címét a Beállítások lapon.

4. Ha a felhasználói portál és/vagy a Mobile App Web Service korábban a PhoneFactor ügynöktől eltérő kiszolgálón volt telepítve:

   1. Nyissa meg a telepítési helyet (például C:\Program Files\PhoneFactor), és másoljon át egy vagy több telepítőt a másik kiszolgálóra. A felhasználói portál és a Mobile App Web Service is rendelkezik 32 bites és 64 bites telepítővel is. Ezeknek a neve MultiFactorAuthenticationUserPortalSetupXX.msi, illetve MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi.

   2. A webkiszolgálón a felhasználói portál telepítéséhez nyisson meg egy parancssort rendszergazdaként, és futtassa a MultiFactorAuthenticationUserPortalSetupXX.msi fájlt.

      Az alapértelmezett virtuális címtár neve mostantól **PhoneFactor** helyett **MultiFactorAuth**. Ha a korábbi nevet szeretné használni, a virtuális címtár nevét a telepítés közben módosítania kell. Ellenkező esetben, ha engedélyezi a telepítés számára az új alapértelmezett név használatát, kattintson a Multi-Factor Authentication-kiszolgáló felhasználói portál ikonjára, és frissítse a felhasználói portál URL-címét a beállítások lapon. A meglévő felhasználókat tájékoztatni kell az új URL-címről.

   3. Lépjen a felhasználói portál telepítési helyére (pl. C:\inetpub\wwwroot\MultiFactorAuth), és szerkessze a web.config fájlt. Másolja az appSettings és az applicationSettings szakaszban található értékeket az eredeti web.config fájlból, amelyről a frissítés előtt biztonsági másolatot készített, az új web.config fájlba. Ha a Web Service SDK telepítésekor megtartotta az új alapértelmezett virtuális címtár nevét, módosítsa az URL-címet az applicationSettings szakaszban, hogy a megfelelő helyre mutasson. Ha bármilyen egyéb alapértelmezett értéket módosított a korábbi web.config fájlban, alkalmazza ugyanezeket a módosításokat az új web.config fájlra.

> [!NOTE]
> Ha az Azure MFA-kiszolgáló 8.0-s verziójánál régebbiről frissít 8.0-nál újabb verzióra, a mobilalkalmazás webszolgáltatását eltávolíthatja a frissítés után

## <a name="next-steps"></a>Következő lépések

- [Telepítse a felhasználói portált](howto-mfaserver-deploy-userportal.md) az Azure Multi-Factor Authentication-kiszolgálóhoz.

- [Konfigurálja a Windows-hitelesítést](howto-mfaserver-windows.md) az alkalmazásokhoz. 
