---
title: LDAP-hitelesítés és Azure MFA-kiszolgáló – Azure Active Directory
description: Az LDAP-hitelesítés és az Azure Multi-Factor Authentication-kiszolgáló üzembe helyezése.
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
ms.openlocfilehash: 8961cccbd57294e477f0d33202fe91cd292b7814
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96742221"
---
# <a name="ldap-authentication-and-azure-multi-factor-authentication-server"></a>LDAP-hitelesítés és Azure-Multi-Factor Authentication-kiszolgáló

Alapértelmezés szerint az Azure Multi-Factor Authentication-kiszolgáló úgy van konfigurálva, hogy a felhasználókat az Active Directoryból importálja vagy szinkronizálja. Azonban konfigurálható úgy különböző LDAP-címtárakhoz való kötésre is, például ADAM-címtárhoz vagy adott Active Directory-tartományvezérlőkhöz. Ha LDAP-kapcsolaton keresztül csatlakozik egy címtárhoz, az Azure Multi-Factor Authentication-kiszolgáló LDAP-proxyként működhet hitelesítések végrehajtásához. Ezenkívül lehetővé teszi, hogy az LDAP-kötést felhasználja RADIUS-célként az IIS-hitelesítést használó felhasználók előhitelesítésére illetve elsődleges hitelesítésre az Azure MFA felhasználói portálon.

Ha az Azure Multi-Factor Authenticationt LDAP-proxyként szeretné használni, szúrja be az Azure-Multi-Factor Authentication-kiszolgáló az LDAP-ügyfél (például VPN-készülék, alkalmazás) és az LDAP-kiszolgáló között. Az Azure Multi-Factor Authentication-kiszolgálót úgy kell konfigurálni, hogy az ügyfélkiszolgálókkal és az LDAP-címtárral is kommunikáljon. Ebben a konfigurációban az Azure Multi-Factor Authentication-kiszolgáló fogadja az ügyfélkiszolgálóktól és alkalmazásoktól érkező LDAP-kéréseket, majd továbbítja őket a cél LDAP-címtárkiszolgálónak az elsődleges hitelesítő adatok ellenőrzéséhez. Ha az LDAP-címtár érvényesíti az elsődleges hitelesítő adatokat, az Azure Multi-Factor Authentication végrehajt egy második identitás-ellenőrzést, és visszaküldi a választ az LDAP-ügyfélnek. A teljes hitelesítés csak akkor lesz sikeres, ha az LDAP-kiszolgálón való hitelesítés és a kétlépéses hitelesítés is sikeres.

> [!IMPORTANT]
> Az 2019. július 1-től a Microsoft már nem kínál az MFA-kiszolgálót az új üzemelő példányokhoz. Azok az új ügyfelek, amelyek a bejelentkezési események során a többtényezős hitelesítést (MFA) szeretnék megkövetelni, felhőalapú Azure AD-Multi-Factor Authenticationt kell használniuk.
>
> A felhőalapú MFA megismeréséhez tekintse meg az [oktatóanyag: felhasználói bejelentkezési események biztonságossá tétele az Azure ad-vel multi-Factor Authentication](tutorial-enable-azure-mfa.md).
>
> Az MFA-kiszolgálót az 2019. július 1. előtt aktivált meglévő ügyfelek letöltik a legújabb verziót, a jövőbeli frissítéseket, és a szokásos módon előállítják az aktiválási hitelesítő adatokat.

## <a name="configure-ldap-authentication"></a>LDAP-hitelesítés konfigurálása

Az LDAP-hitelesítés konfigurálásához telepítse az Azure Multi-Factor Authentication-kiszolgálót egy Windows-kiszolgálón. Kövesse az alábbi eljárást:

### <a name="add-an-ldap-client"></a>LDAP-ügyfél hozzáadása

1. Az Azure Multi-Factor Authentication-kiszolgáló válassza az LDAP-hitelesítés ikont a bal oldali menüben.
2. Jelölje be az **LDAP-hitelesítés engedélyezése** jelölőnégyzetet.

   ![LDAP-hitelesítés az MFA-kiszolgálón](./media/howto-mfaserver-dir-ldap/ldap2.png)

3. Az ügyfelek lapon módosítsa a TCP-port és az SSL-(TLS-) portot, ha az Azure Multi-Factor Authentication LDAP szolgáltatásnak nem szabványos portokhoz kell kötnie az LDAP-kérelmek figyeléséhez.
4. Ha LDAPs-t kíván használni az ügyfél és az Azure Multi-Factor Authentication-kiszolgáló között, akkor a TLS/SSL-tanúsítványt ugyanarra a kiszolgálóra kell telepíteni, mint az MFA-kiszolgálót. Kattintson a **Tallózás** elemre az SSL (TLS) tanúsítvány mező mellett, és válassza ki a biztonságos kapcsolathoz használni kívánt tanúsítványt.
5. Kattintson a **Hozzáadás** parancsra.
6. Az LDAP-ügyfél hozzáadása párbeszédpanelen adja meg annak a berendezésnek, kiszolgálónak vagy alkalmazásnak az IP-címét, amely hitelesíti magát a kiszolgálón és az alkalmazás nevét (nem kötelező). Az alkalmazásnév az Azure Multi-Factor Authentication-jelentésekben jelenik meg, illetve megjelenhet az SMS-es vagy mobilalkalmazásos hitelesítési üzenetekben.
7. Jelölje be a **Require Azure Multi-Factor Authentication user match** (Azure Multi-Factor Authentication felhasználói egyezés megkövetelése) jelölőnégyzetet, ha az összes felhasználót importálta vagy importálni fogja a kiszolgálóra, és kétlépéses hitelesítést alkalmaz rajtuk. Ha jelentős számú felhasználó még nincs importálva a kiszolgálóra és/vagy mentesül a kétlépéses ellenőrzés alól, ne jelölje be a jelölőnégyzetet. A szolgáltatással kapcsolatos további információkért tekintse meg az MFA-kiszolgáló súgóját.

További LDAP-ügyfelek hozzáadásához ismételje meg ezeket a lépéseket.

### <a name="configure-the-ldap-directory-connection"></a>Az LDAP-címtár kapcsolatának konfigurálása

Ha az Azure Multi-Factor Authentication LDAP-hitelesítések fogadására van konfigurálva, a hitelesítéseket az LDAP-címtárnak kell proxykapcsolaton keresztül átadnia. Ezért a Cél lapon csak egyetlen, szürkén megjelenő lehetőség látható LDAP-cél használatához.

> [!NOTE]
> A címtár-integráció nem garantált, hogy a Active Directory Domain Serviceson kívüli címtárakkal is működjön.

1. Az LDAP-címtár-kapcsolatok konfigurálásához kattintson a **címtár-integráció** ikonra.
2. A beállítások lapon jelölje be az **adott LDAP-konfiguráció használata** választógombot.
3. **Szerkesztés kiválasztása..** .
4. Az LDAP-konfiguráció szerkesztése párbeszédpanelen adja meg az LDAP-címtárhoz való kapcsolódáshoz szükséges adatokat a mezőkben. A mezők leírását az Azure Multi-Factor Authentication-kiszolgáló súgófájljában találja.

    ![Címtár-integrációs LDAP-konfiguráció](./media/howto-mfaserver-dir-ldap/ldap.png)

5. Tesztelje az LDAP-kapcsolatokat a **tesztelés** gombra kattintva.
6. Ha az LDAP-kapcsolatok tesztelése sikeres volt, kattintson az **OK** gombra.
7. Kattintson a **szűrők** fülre. A kiszolgáló előre konfigurálva van a tárolók, biztonsági csoportok és felhasználók Active Directoryból való betöltésére. Másik LDAP-címtárhoz való kötés esetén valószínűleg szerkesztenie kell a megjelenített szűrőket. A szűrőkkel kapcsolatos további információkért kattintson a **Súgó** hivatkozásra.
8. Kattintson az **attribútumok** fülre. A kiszolgáló előre konfigurálva van az attribútumok leképezéséhez Active Directory.
9. Másik LDAP-címtárhoz való kötéshez vagy az előre konfigurált attribútumleképezések módosításához kattintson a **Szerkesztés…** elemre
10. Az Attribútumok szerkesztése párbeszédpanelen módosítsa az LDAP-attribútumleképezéseket a címtárra vonatkozóan. Az attribútumok nevei a **...** gombra kattintva adhatók meg vagy választhatók ki. gombra kattintva. Az attribútumokkal kapcsolatos további információért kattintson a **Súgó** hivatkozásra.
11. Kattintson az **OK** gombra.
12. Kattintson a **Vállalati beállítások** ikonra, és válassza a **Felhasználónév feloldása** fület.
13. Ha az Active Directoryhoz egy tartományhoz csatlakoztatott kiszolgálóról csatlakozik, hagyja bejelölve a **Windows biztonsági azonosítók (SID-k) használata a felhasználónevek egyeztetéséhez** választógombot. Ellenkező esetben jelölje be az **egyedi LDAP azonosító attribútum használata a felhasználónevek egyeztetéséhez** választógombot.

Ha az **Egyedi LDAP azonosító attribútum használata a felhasználónevek egyeztetéséhez** lehetőséget bejelöli, az Azure Multi-Factor Authentication-kiszolgáló a felhasználóneveket megkísérli egy egyedi azonosítóvá feloldani az LDAP-címtárban. Az LDAP-keresés a címtár-integráció – > attribútumok lapon meghatározott Felhasználónév-attribútumokon történik. Amikor egy felhasználó hitelesíti magát, a Felhasználónév az LDAP-címtárban található egyedi azonosítóra lesz feloldva. Az egyedi azonosító az Azure Multi-Factor Authentication adatfájlban található felhasználó egyeztetésére szolgál. Ez lehetővé teszi a kis-és nagybetűket megkülönböztető összehasonlításokat, illetve a hosszú és rövid felhasználónevek formátumát.

A fenti lépések elvégzése után az MFA-kiszolgáló figyeli a konfigurált ügyfelek LDAP-hozzáférési kérelmeit, és proxyként kezeli ezeket a kéréseket az LDAP-címtárban a hitelesítéshez.

## <a name="configure-ldap-client"></a>LDAP-ügyfél konfigurálása

Az LDAP-ügyfél konfigurálásához kövesse az alábbi útmutatást:

* Konfigurálja a készüléket, kiszolgálót vagy alkalmazást, hogy úgy végezzen hitelesítést LDAP-n keresztül az Azure Multi-Factor Authentication-kiszolgálón, mintha az az Ön LDAP-címtára lenne. Használja ugyanazokat a beállításokat, amelyeket általában használna az LDAP-címtárához való közvetlen csatlakozáshoz, azonban az Azure Multi-Factor Authentication-kiszolgáló kiszolgálónevét vagy IP-címét adja meg.
* Konfigurálja az LDAP-időtúllépést 30-60 másodpercre, hogy elegendő idő álljon rendelkezésre a felhasználó hitelesítő adatainak ellenőrzésére az LDAP-címtárral, a második lépés ellenőrzésének elvégzésére, a válasz fogadására és az LDAP-hozzáférési kérés megválaszolására.
* Ha LDAPs-t használ, az LDAP-lekérdezéseket készítő berendezésnek vagy kiszolgálónak meg kell bíznia az Azure Multi-Factor Authentication-kiszolgáló telepített TLS/SSL-tanúsítványban.
