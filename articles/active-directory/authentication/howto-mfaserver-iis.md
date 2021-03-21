---
title: IIS-hitelesítés és Azure MFA-kiszolgáló – Azure Active Directory
description: Az IIS-hitelesítés és az Azure Multi-Factor Authentication-kiszolgáló üzembe helyezése.
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
ms.openlocfilehash: c522fb950e2489afce44553d233ac3eddf138730
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96742170"
---
# <a name="configure-azure-multi-factor-authentication-server-for-iis-web-apps"></a>Az Azure Multi-Factor Authentication-kiszolgáló konfigurálása IIS-webalkalmazásokhoz

Az Azure MFA-kiszolgáló IIS-hitelesítés szakaszában engedélyezheti és konfigurálhatja az IIS-hitelesítést a Microsoft IIS-webalkalmazásokkal való integrációra. Az Azure MFA-kiszolgáló egy beépülő modult telepít, amely képes szűrni az IIS-webkiszolgálónak küldött kéréseket az Azure Multi-Factor Authentication hozzáadása érdekében. Az IIS beépülő modul támogatja az űrlapalapú hitelesítést és az integrált Windows HTTP-hitelesítést. Ezenkívül a megbízható IP-címek konfigurálhatók úgy, hogy a belső IP-címek mentesüljenek kéttényezős hitelesítés alól.

> [!IMPORTANT]
> Az 2019. július 1-től a Microsoft már nem kínál az MFA-kiszolgálót az új üzemelő példányokhoz. Azok az új ügyfelek, amelyek a bejelentkezési események során a többtényezős hitelesítést (MFA) szeretnék megkövetelni, felhőalapú Azure AD-Multi-Factor Authenticationt kell használniuk.
>
> A felhőalapú MFA megismeréséhez tekintse meg az [oktatóanyag: felhasználói bejelentkezési események biztonságossá tétele az Azure ad-vel multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Az MFA-kiszolgálót az 2019. július 1. előtt aktivált meglévő ügyfelek letöltik a legújabb verziót, a jövőbeli frissítéseket, és a szokásos módon előállítják az aktiválási hitelesítő adatokat.
>
> Felhőalapú Azure-Multi-Factor Authentication használata esetén nincs alternatívája az Azure Multi-Factor Authentication (MFA) kiszolgáló által biztosított IIS beépülő modulnak. Ehelyett használja a webalkalmazás-proxyt (WAP) Active Directory összevonási szolgáltatások (AD FS) (AD FS) vagy Azure Active Directory alkalmazásproxy használatával.

![IIS-hitelesítés az MFA-kiszolgálón](./media/howto-mfaserver-iis/iis.png)

## <a name="using-form-based-iis-authentication-with-azure-multi-factor-authentication-server"></a>Az űrlapalapú IIS-hitelesítés használata az Azure Multi-Factor Authentication-kiszolgálóval

Űrlapalapú hitelesítést használó IIS-webalkalmazás védelméhez telepítse az Azure Multi-Factor Authentication-kiszolgálót az IIS-webkiszolgálón és konfigurálja a kiszolgálót a következő eljárásnak megfelelően:

1. Az Azure Multi-Factor Authentication-kiszolgálón kattintson a bal oldali menüben lévő IIS-hitelesítés ikonra.
2. Kattintson az **Űrlapalapú fülre**.
3. Kattintson a **Hozzáadás** parancsra.
4. A Felhasználónév, jelszó és tartomány változók automatikus észleléséhez adja meg a bejelentkezési URL-címet (például `https://localhost/contoso/auth/login.aspx` ) az Form-Based webhely automatikus konfigurálása párbeszédpanelen, majd kattintson **az OK** gombra.
5. Jelölje be a **Multi-Factor Authentication felhasználói egyeztetés megkövetelése** jelölőnégyzetet, ha az összes felhasználót importálta vagy importálni fogja a kiszolgálóra, és többtényezős hitelesítést alkalmaz rajtuk. Ha jelentős számú felhasználó még nincs importálva a kiszolgálóra és/vagy mentesülni fog a többtényezős hitelesítés alól, ne jelölje be a jelölőnégyzetet.
6. Ha az oldal változóit nem lehet automatikusan észlelni, kattintson a **Megadás manuálisan** lehetőségre az Űrlapalapú webhely automatikus konfigurálása párbeszédpanelen.
7. Az Űrlapalapú webhely hozzáadása párbeszédpanelen adja meg a bejelentkezési oldal URL-címét a Küldési URL-cím mezőben, majd adjon meg egy alkalmazásnevet (nem kötelező). Az alkalmazásnév az Azure Multi-Factor Authentication-jelentésekben jelenik meg, illetve megjelenhet az SMS-es vagy mobilalkalmazásos hitelesítési üzenetekben.
8. Válassza ki a megfelelő kérési formátumot. Ez a legtöbb webalkalmazás esetén **POST or GET** értékre van állítva.
9. Adja meg a Felhasználónév változót, a Jelszó változót és a Tartomány változót (ha megjelenik a bejelentkezési oldalon). A beviteli mezők nevének megtekintéséhez nyissa meg a bejelentkezési lapot egy webböngészőben, majd a lapon a jobb gombbal kattintva válassza a **Forrás megtekintése** parancsot.
10. Jelölje be az **Azure multi-Factor Authentication felhasználói egyeztetés megkövetelése** jelölőnégyzetet, ha az összes felhasználó be lett importálva a kiszolgálóra, és a többtényezős hitelesítésre is vonatkozik. Ha jelentős számú felhasználó még nincs importálva a kiszolgálóra és/vagy mentesülni fog a többtényezős hitelesítés alól, ne jelölje be a jelölőnégyzetet.
11. A **Speciális** gombra kattintva áttekintheti a következő speciális beállításokat:

    - Egyéni elutasítási oldalfájl kiválasztása
    - Sikeres hitelesítések gyorsítótárazása a webhelyre egy adott időtartamra cookie-k használatával
    - Válassza ki, hogy az elsődleges hitelesítő adatokat Windows-tartományon, LDAP-címtáron vagy RADIUS-kiszolgálón hitelesíti.

12. Az Űrlapalapú webhely hozzáadása párbeszédpanelre való visszatéréshez kattintson az **OK** gombra.
13. Kattintson az **OK** gombra.
14. Miután a rendszer észlelte vagy megadta az URL-címet és az oldal változóit, a webhely adatai megjelennek az Űrlapalapú panelen.

## <a name="using-integrated-windows-authentication-with-azure-multi-factor-authentication-server"></a>Integrált Windows IIS-hitelesítés használata az Azure Multi-Factor Authentication-kiszolgálóval

Integrált Windows HTTP-hitelesítést használó IIS-webalkalmazás védelméhez telepítse az Azure MFA-kiszolgálót az IIS-webkiszolgálón, majd konfigurálja a kiszolgálót az alábbi lépéseket követve:

1. Az Azure Multi-Factor Authentication-kiszolgálón kattintson a bal oldali menüben lévő IIS-hitelesítés ikonra.
2. Kattintson a **HTTP** fülre.
3. Kattintson a **Hozzáadás** parancsra.
4. Az alap URL-cím hozzáadása párbeszédpanelen adja meg annak a webhelynek az URL-címét, amelyen a HTTP-hitelesítést végzi (például `http://localhost/owa` ), és adja meg az alkalmazás nevét (nem kötelező). Az alkalmazásnév az Azure Multi-Factor Authentication-jelentésekben jelenik meg, illetve megjelenhet az SMS-es vagy mobilalkalmazásos hitelesítési üzenetekben.
5. Módosítja az Üresjárati időkorlát és a Munkamenetek maximális időtartama értékét, ha az alapértelmezett értékek nem elégségesek.
6. Jelölje be a **Multi-Factor Authentication felhasználói egyeztetés megkövetelése** jelölőnégyzetet, ha az összes felhasználót importálta vagy importálni fogja a kiszolgálóra, és többtényezős hitelesítést alkalmaz rajtuk. Ha jelentős számú felhasználó még nincs importálva a kiszolgálóra és/vagy mentesülni fog a többtényezős hitelesítés alól, ne jelölje be a jelölőnégyzetet.
7. Szükség esetén jelölje be a **Cookie-gyorsítótár** jelölőnégyzetét.
8. Kattintson az **OK** gombra.

## <a name="enable-iis-plug-ins-for-azure-multi-factor-authentication-server"></a>IIS beépülő modulok engedélyezése az Azure Multi-Factor Authentication-kiszolgálón

Miután konfigurálta az űrlapalapú hitelesítés vagy a HTTP-hitelesítés URL-címeit és beállításait, válassza ki a helyeket, ahol az Azure Multi-Factor Authentication IIS beépülő modulokat betölteni és engedélyezni szeretné az IIS-ben. Kövesse az alábbi eljárást:

1. Ha az IIS 6-os verzióját futtatja, kattintson az **ISAPI** fülre. Válassza ki azt a webhelyet, amelyen a webalkalmazás fut (például alapértelmezett webhely) az Azure Multi-Factor Authentication ISAPI-szűrő beépülő modul engedélyezéséhez az adott helyen.
2. Ha az IIS 7-es vagy újabb verzióját futtatja, kattintson a **natív modul** fülre. Válassza ki azokat a kiszolgálókat, webhelyeket vagy alkalmazásokat, amelyeknek engedélyezni szeretné az IIS beépülő modult a kívánt szinteken.
3. Kattintson az **IIS-hitelesítés engedélyezése** jelölőnégyzetre a képernyő tetején. Az Azure Multi-Factor Authentication mostantól biztosítja a kiválasztott IIS-alkalmazás védelmét. Győződjön meg arról, hogy importálta a felhasználókat a kiszolgálóra.

## <a name="trusted-ips"></a>Megbízható IP-címek

A Megbízható IP-címek segítségével a felhasználók adott IP-címekről vagy alhálózatokról származó webhelykérések esetén kihagyhatják az Azure Multi-Factor Authenticationt. Például mentesítheti a felhasználókat az Azure Multi-Factor Authentication alól, ha az irodából jelentkeznek be. Ehhez az irodai alhálózatot Megbízható IP-címek bejegyzésként kell megadni. A Megbízható IP-címek konfigurálásához kövesse az alábbi eljárást:

1. Az IIS-hitelesítés szakaszban kattintson a **megbízható IP** -címek fülre.
2. Kattintson a **Hozzáadás** parancsra.
3. Amikor megjelenik a megbízható IP-címek hozzáadása párbeszédpanel, válassza az **egyetlen IP**-cím, az **IP-címtartomány** vagy az **alhálózat** választógombot.
4. Adja meg az IP-címet, az IP-címek tartományát, vagy az alhálózatot, amelyet engedélyezni kell. Ha alhálózatot ad meg, válassza ki a megfelelő hálózati maszkot, és kattintson az **OK** gombra.
