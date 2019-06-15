---
title: Az Azure MFA-kiszolgálóval és az AD FS 2.0 – az Azure Active Directory
description: Ez az Azure Multi-Factor Authentication-oldal leírja, hogyan kezdheti el az Azure MFA és az AD FS 2.0 használatát.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 231375c94163f098cce86bdbbf285a58957a9927
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056113"
---
# <a name="configure-azure-multi-factor-authentication-server-to-work-with-ad-fs-20"></a>Azure Multi-Factor Authentication-kiszolgáló konfigurálása az AD FS 2.0-val való használathoz

Ez a cikk az Azure Active Directory összevonási szolgáltatást használó szervezeteknek szól, amelyek szeretnék biztosítani a helyszíni és a felhőben található erőforrásaik védelmét. Biztosítsa erőforrásai védelmét a Multi-Factor Authentication-kiszolgáló segítségével, illetve az AD FS-sel való együttműködés beállításával a nagy értékű végpontok kétlépéses ellenőrzésének aktiválása érdekében.

Ez a dokumentáció az Azure Multi-Factor Authentication-kiszolgáló AD FS 2.0-val való használatáról szól. Az AD FS-sel kapcsolatos információkért lásd: [A felhő és a helyszíni erőforrások védelme az Azure Multi-Factor Authentication-kiszolgáló és az AD FS használatával a Windows Server 2012 R2 rendszeren](howto-mfaserver-adfs-2012.md).

> [!IMPORTANT]
> 2019\. július 1-től a Microsoft már nem szavatolnak MFA-kiszolgáló az új üzembe helyezésekhez. Új felhasználókat szeretné a felhasználóktól többtényezős hitelesítést kell használnia a felhőalapú Azure multi-factor Authentication. Meglévő ügyfeleink, akik aktiválták az MFA-kiszolgáló előtt július 1 lesz a legújabb verzió, a jövőbeli frissítések letöltéséhez és a szokásos módon aktiváló hitelesítő adatok előállítása.

## <a name="secure-ad-fs-20-with-a-proxy"></a>Az AD FS 2.0 védelme proxyval

Az AD FS 2.0 proxyval való védelméhez telepítse az Azure Multi-Factor Authentication-kiszolgálót az AD FS-proxykiszolgálóra.

### <a name="configure-iis-authentication"></a>Az IIS-hitelesítés konfigurálása

1. Az Azure Multi-Factor Authentication-kiszolgálón kattintson a bal oldali menüben lévő **IIS-hitelesítés ikonra**.
2. Kattintson az **Űrlapalapú fülre**.
3. Kattintson a **Hozzáadás**lehetőségre.

   ![MFA-kiszolgáló IIS-hitelesítés ablak](./media/howto-mfaserver-adfs-2/setup1.png)

4. A felhasználónév, jelszó és tartomány változók automatikus észleléséhez adja meg a bejelentkezési URL-t (például https://sso.contoso.com/adfs/ls) ) az Űrlapalapú webhely automatikus konfigurálása párbeszédpanelen, és kattintson az **OK** gombra.
5. Jelölje be a **Require Azure Multi-Factor Authentication user match** (Azure Multi-Factor Authentication felhasználói egyezés megkövetelése) jelölőnégyzetet, ha az összes felhasználót importálta vagy importálni fogja a kiszolgálóra, és kétlépéses hitelesítést alkalmaz rajtuk. Ha jelentős számú felhasználó még nincs importálva a kiszolgálóra és/vagy mentesülni fog a kétlépéses hitelesítés alól, ne jelölje be a jelölőnégyzetet.
6. Ha az oldal változóit nem lehet automatikusan észlelni, kattintson a **Megadás manuálisan** gombra az Űrlapalapú webhely automatikus konfigurálása párbeszédpanelen.
7. Az Űrlapalapú webhely hozzáadása párbeszédpanelen adja meg az AD FS bejelentkezési oldal URL-címét (például: https://sso.contoso.com/adfs/ls) ) a Küldési URL-cím mezőben, majd adjon meg egy alkalmazásnevet (nem kötelező). Az alkalmazásnév az Azure Multi-Factor Authentication-jelentésekben jelenik meg, illetve megjelenhet az SMS-es vagy mobilalkalmazásos hitelesítési üzenetekben.
8. A kérelmet állítsa **POST vagy GET** formátumúra.
9. Adja meg a Felhasználónév változót (ctl00$ContentPlaceHolder1$UsernameTextBox) és a Jelszó változót (ctl00$ContentPlaceHolder1$PasswordTextBox). Ha az űrlapalapú bejelentkezési oldalon egy tartomány szövegmező jelenik meg, írja be a Tartomány változót is. A bejelentkezési lapon megjelenő beviteli mezők nevének a megtekintéséhez nyissa meg a bejelentkezési lapot egy webböngészőben, majd a kattintson a jobb gombbal a **Forrás megtekintése** parancsra.
10. Jelölje be a **Require Azure Multi-Factor Authentication user match** (Azure Multi-Factor Authentication felhasználói egyezés megkövetelése) jelölőnégyzetet, ha az összes felhasználót importálta vagy importálni fogja a kiszolgálóra, és kétlépéses hitelesítést alkalmaz rajtuk. Ha jelentős számú felhasználó még nincs importálva a kiszolgálóra és/vagy mentesülni fog a kétlépéses hitelesítés alól, ne jelölje be a jelölőnégyzetet.

    ![Űrlapalapú webhely hozzáadása az MFA-kiszolgáló](./media/howto-mfaserver-adfs-2/manual.png)

11. Kattintson a **Speciális...** elemre a speciális beállítások áttekintéséhez. A konfigurálható beállítások közé tartoznak az alábbiak:

    - Egyéni elutasítási oldalfájl kiválasztása
    - Sikeres hitelesítések gyorsítótárazása a webhelyre cookie-k használatával
    - Az elsődleges hitelesítő adatok hitelesítési módjának kiválasztása

12. Mivel az AD FS-proxykiszolgáló valószínűleg nem fog a tartományhoz csatlakozni, valószínűleg LDAP-vel fog csatlakozni a tartományvezérlőhöz a felhasználó importálásához és előhitelesítéséhez. A Speciális űrlapalapú webhely párbeszédpanelen kattintson az **Elsődleges hitelesítés fülre**, és válassza az **LDAP-kötést** az előhitelesítés hitelesítési típusaként.
13. Amikor végzett, kattintson az **OK** gombra, hogy visszatérjen az Űrlapalapú webhely hozzáadása párbeszédpanelre.
14. A párbeszédpanel bezárásához kattintson az **OK** gombra.
15. Miután a rendszer észlelte vagy megadta az URL-címet és az oldal változóit, a webhely adatai megjelennek az Űrlapalapú panelen.
16. Kattintson a **Natív modul** fülre, és jelölje ki a kiszolgálót, az AD FS-proxyt futtató webhelyet (például az „Alapértelmezett webhelyet”) vagy az AD FS-proxyalkalmazást (például az „adfs” alatt található „Is”-t) az IIS beépülő modul kívánt szinten való engedélyezéséhez.
17. Kattintson az **IIS-hitelesítés engedélyezése** jelölőnégyzetre a képernyő tetején.

Az IIS-hitelesítés ezzel engedélyezett.

### <a name="configure-directory-integration"></a>Címtárintegrálás konfigurálása

Engedélyezte az IIS-hitelesítést, de az Active Directory (AD) LDAP-n keresztül való előhitelesítéséhez azonban konfigurálnia kell az LDAP-kapcsolatot a tartományvezérlőre.

1. Kattintson a **Címtárintegrálás** ikonra.
2. A Beállítások lapon jelölje be a **Megadott LDAP-konfiguráció használata** választógombot.

   ![LDAP-beállítások az egyes LDAP-beállítások konfigurálása](./media/howto-mfaserver-adfs-2/ldap1.png)

3. Kattintson a **Szerkesztés** gombra.
4. Az LDAP-konfiguráció szerkesztése párbeszédpanelen adja meg az AD tartományvezérlőhöz való kapcsolódáshoz szükséges adatokat a mezőkben. A mezők leírását az Azure Multi-Factor Authentication-kiszolgáló súgófájljában találja.
5. Tesztelje az LDAP-kapcsolatot a **Tesztelés** gombra kattintva.

   ![MFA-kiszolgáló az LDAP-konfiguráció tesztelése](./media/howto-mfaserver-adfs-2/ldap2.png)

6. Ha az LDAP-kapcsolat tesztelése sikeres volt, kattintson az **OK** gombra.

### <a name="configure-company-settings"></a>Vállalati beállítások konfigurálása

1. Ezután kattintson a **Vállalati beállítások** ikonra, majd a **Felhasználónevek feloldása** fülre.
2. Jelölje be az **Egyedi LDAP azonosító attribútum használata a felhasználónevek egyeztetéséhez** választógombot.
3. Ha a felhasználók „tartomány\felhasználónév” formátumban adják meg a felhasználónevüket, a kiszolgálónak le kell tudnia választani a tartományt a felhasználónévről az LDAP-lekérdezés létrehozásakor. Ez a beállításjegyzék beállításával végezhető el.
4. Nyissa meg a beállításszerkesztőt, és 64 bites kiszolgálón lépjen a HKEY_LOCAL_MACHINE/SOFTWARE/Wow6432Node/Positive Networks/PhoneFactor elemre. Vagy 32 bites kiszolgálón ugorjon az elérési út „Wow6432Node” csomópontjára. Hozzon létre egy „UsernameCxz_stripPrefixDomain” nevű DWORD beállításjegyzék-kulcsot, és állítsa az értékét 1-re. Az Azure Multi-Factor Authentication mostantól védi az AD FS-proxyt.

Győződjön meg arról, hogy importálta a felhasználókat az Active Directoryból a kiszolgálóra. Tekintse meg a [megbízható IP-címek szakaszt](#trusted-ips) Ha azt szeretné, hogy a belső IP-címek, hogy a kétlépcsős hitelesítés végrehajtása ne szükséges, amikor ezekről a helyszínekről jelentkezik be a webhelyre.

![A Beállításszerkesztő segítségével a vállalati beállítások konfigurálása](./media/howto-mfaserver-adfs-2/reg.png)

## <a name="ad-fs-20-direct-without-a-proxy"></a>AD FS 2.0 Direct proxy nélkül

Az AD FS védelmét akkor is biztosíthatja, ha az AD FS-proxy nincs használatban. Telepítse az Azure Multi-Factor Authentication-kiszolgálót az AD FS-kiszolgálóra, és konfigurálja a kiszolgálót az alábbi lépések szerint:

1. Az Azure Multi-Factor Authentication-kiszolgálón kattintson a bal oldali menüben lévő **IIS-hitelesítés ikonra**.
2. Kattintson a **HTTP** fülre.
3. Kattintson a **Hozzáadás**lehetőségre.
4. Az Alap URL-cím hozzáadása párbeszédpanelen az Alap URL-cím mezőben adja meg az URL-címét annak az AD FS-webhelynek, amelyen a rendszer a HTTP-hitelesítést végzi (például: https://sso.domain.com/adfs/ls/auth/integrated) ). Ezután írja be az alkalmazás nevét (nem kötelező). Az alkalmazásnév az Azure Multi-Factor Authentication-jelentésekben jelenik meg, illetve megjelenhet az SMS-es vagy mobilalkalmazásos hitelesítési üzenetekben.
5. Ha szeretné, állítsa be az Üresjárati időkorlátot és a Munkamenetek maximális időtartamát.
6. Jelölje be a **Require Azure Multi-Factor Authentication user match** (Azure Multi-Factor Authentication felhasználói egyezés megkövetelése) jelölőnégyzetet, ha az összes felhasználót importálta vagy importálni fogja a kiszolgálóra, és kétlépéses hitelesítést alkalmaz rajtuk. Ha jelentős számú felhasználó még nincs importálva a kiszolgálóra és/vagy mentesülni fog a kétlépéses hitelesítés alól, ne jelölje be a jelölőnégyzetet.
7. Szükség esetén jelölje be a cookie-gyorsítótár jelölőnégyzetét.

   ![AD FS 2.0 Direct proxy nélkül](./media/howto-mfaserver-adfs-2/noproxy.png)

8. Kattintson az **OK** gombra.
9. Kattintson a **Natív modul** fülre, és jelölje ki a kiszolgálót, a webhelyet (például az „Alapértelmezett webhelyet”) vagy az AD FS-alkalmazást (például az „adfs” alatta található „Is”-t) az IIS beépülő modul kívánt szinten való engedélyezéséhez.
10. Kattintson az **IIS-hitelesítés engedélyezése** jelölőnégyzetre a képernyő tetején.

Az Azure Multi-Factor Authentication mostantól védi az AD FS-t.

Győződjön meg arról, hogy importálta a felhasználókat az Active Directoryból a kiszolgálóra. Tekintse meg a megbízható IP-címek szakaszt, ha azt szeretné, hogy a belső IP-címeket, hogy a kétlépcsős hitelesítés végrehajtása ne szükséges, amikor ezekről a helyszínekről jelentkezik be a webhelyre.

## <a name="trusted-ips"></a>Megbízható IP-címek

A Megbízható IP-címek segítségével a felhasználók adott IP-címekről vagy alhálózatokról származó webhelykérések esetén kihagyhatják az Azure Multi-Factor Authenticationt. Előfordulhat például, hogy felhasználókat szeretne felmenteni a kétlépéses ellenőrzés alól, amikor bejelentkeznek az irodából. Ehhez az irodai alhálózatot Megbízható IP-címek bejegyzésként kell megadni.

### <a name="to-configure-trusted-ips"></a>Megbízható IP-címek konfigurálása

1. Az IIS-hitelesítés szakaszban kattintson a **Megbízható IP-címek** fülre.
2. Kattintson a **Hozzáadás...** gombra.
3. A Megbízható IP-címek hozzáadása párbeszédpanel megjelenésekor válassza az **Egyetlen IP-cím**, az **IP-címtartomány** vagy az **Alhálózat** választógombot.
4. Adja meg az IP-cím, IP-címtartományt vagy alhálózatot, amelyet engedélyezni kell. Ha alhálózatot ad meg, válassza ki a megfelelő hálózati maszkot, és kattintson az **OK** gombra.

![Az MFA-kiszolgáló megbízható IP-címek konfigurálása](./media/howto-mfaserver-adfs-2/trusted.png)
