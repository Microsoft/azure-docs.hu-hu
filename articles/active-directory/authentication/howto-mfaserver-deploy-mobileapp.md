---
title: Azure MFA-kiszolgáló Mobile App Web Service – Azure Active Directory
description: Konfigurálja az MFA-kiszolgálót leküldéses értesítések a Microsoft Authenticator alkalmazást használó felhasználóknak történő küldésére.
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
ms.openlocfilehash: 6263491ce5b319c3968c542ebbaf00294c5152cd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96742425"
---
# <a name="enable-mobile-app-authentication-with-azure-multi-factor-authentication-server"></a>A mobilalkalmazásos hitelesítés engedélyezése az Azure Multi-Factor Authentication-kiszolgálóval

A Microsoft Authenticator alkalmazás egy további sávon kívüli ellenőrzési lehetőséget kínál. Ha nem szeretne automatikus telefonhívást vagy SMS-t elhelyezni a felhasználónak a bejelentkezés során, az Azure Multi-Factor Authentication értesítést küld a felhasználó okostelefonján vagy táblaszámítógépén található Microsoft Authenticator alkalmazásnak. A felhasználó egyszerűen csapok **ellenőrzése** (vagy PIN-kód beírása és a "hitelesítés") az alkalmazásban a bejelentkezés befejezéséhez.

Ha a telefonon a vétel nem megbízható, kétlépéses ellenőrzést biztosító mobilalkalmazás használata ajánlott. Ha az alkalmazást OATH token előállítására használja, nincs szükség hálózatra vagy internetkapcsolatra.

> [!IMPORTANT]
> Az 2019. július 1-től a Microsoft már nem kínál az MFA-kiszolgálót az új üzemelő példányokhoz. Azok az új ügyfelek, amelyek a bejelentkezési események során a többtényezős hitelesítést (MFA) szeretnék megkövetelni, felhőalapú Azure AD-Multi-Factor Authenticationt kell használniuk.
>
> A felhőalapú MFA megismeréséhez tekintse meg az [oktatóanyag: felhasználói bejelentkezési események biztonságossá tétele az Azure ad-vel multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Az MFA-kiszolgálót az 2019. július 1. előtt aktivált meglévő ügyfelek letöltik a legújabb verziót, a jövőbeli frissítéseket, és a szokásos módon előállítják az aktiválási hitelesítő adatokat.

> [!IMPORTANT]
> Ha telepítette az Azure Multi-Factor Authentication-kiszolgáló 8.x-es vagy újabb verzióját, a következő lépések legtöbbjére nincs szükség. A mobilalkalmazások hitelesítését [a mobilalkalmazás konfigurálását](#configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server) ismertető szakasz lépéseit követve állíthatja be.

## <a name="requirements"></a>Követelmények

A Microsoft Authenticator alkalmazás használatához az Azure Multi-Factor Authentication-kiszolgáló 8.x-es vagy újabb verzióját kell futtatnia.

## <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>A mobilalkalmazás beállításainak konfigurálása az Azure Multi-Factor Authentication-kiszolgálón

1. A Multi-Factor Authentication-kiszolgáló konzoljában kattintson a Felhasználói portál ikonra. Ha a felhasználók vezérelhetik a hitelesítési módszereiket, a Beállítások lapon a **Módszer kiválasztásának engedélyezése a felhasználóknak** lehetőség alatt jelölje be a **Mobile App** jelölőnégyzetét. Ha a szolgáltatás nincs engedélyezve, a végfelhasználóknak kapcsolatba kell lépniük a támogatási szolgálattal a Mobile App aktiválásának befejezéséhez.
2. Jelölje be a **Mobilalkalmazás aktiválásának engedélyezése a felhasználóknak** jelölőnégyzetet.
3. Jelölje be a **Felhasználó beléptetésének engedélyezése** jelölőnégyzetet.
4. Kattintson a **Mobile App** ikonra.
5. A **Fiók neve** mezőben adja meg a fiókhoz tartozó mobilalkalmazásban megjeleníteni kívánt cég- vagy szervezetnevet.
   ![MFA-kiszolgáló konfigurálása – A Mobile App beállításai](./media/howto-mfaserver-deploy-mobileapp/mobile.png)

## <a name="next-steps"></a>Következő lépések

- [Speciális forgatókönyvek az Azure multi-Factor Authentication és a harmadik féltől származó virtuális magánhálózatok esetében](howto-mfaserver-nps-vpn.md).
